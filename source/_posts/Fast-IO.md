---
title: 【筆記】IO 優化
date: 2023-08-01 09:00:08
categories: [競程, 筆記]
tags: [IO 優化]
index_img:
banner_img:
---

本文將會介紹一些 IO 優化的方法。

## iostream

使用 `iostream` 下的 `cin/cout`。

```cpp
#include <iostream>

int main() {
	int a;
	std::cin >> a;
	std::cout << a << std::endl;
}
```

以上的程式碼在輸入輸出量巨大的時候很有可能會吃 TLE。實際上 `cin/cout` 會慢是因為 compiler 為了要讓 `cin/cout` 和 `scanf/printf` 可以混著用，因此多處理了一些東西，導致速度變慢。把同步的功能關掉後，`cin/cout` 的速度就會快非常多，基本上在各大 OJ 就很夠用了。需要注意的是把同步關閉後就不能夠在使用 `scanf/printf` 了。除此之外，把 `std::endl` 換成 `"\n"` 也會快很多，因為 `std::endl` 每次會把緩衝區清空。

```cpp
#include <iostream>

int main() {
	std::ios::sync_with_stdio(false);
	std::cin.tie(0);
	int a;
	std::cin >> a;
	std::cout << a << "\n";
}
```

## getchar/putchar

`getchar` 和 `putchar` 是內建的函式，在 `stdio.h` 下。`getchar` 一次讀入一個字元，`putchar` 一次輸出一個字元。透過這兩個函式，我們可以自己手刻輸入和輸出。

接下來進入黑科技了。

## getchar_unlocked/putchar_unlocked

使用方法與 `getchar/putchar` 相同，但加上 `unlocked` 後速度更快。這兩個函式並不是在所有環境都能夠執行，在 Windows 下要改成 `_getchar_nolock` 和 `_putchar_nolock`。

## fread/fwrite

使用時一樣要引入 `stdio.h`。`fread` 會一次讀取多個字元。一般而言我們會先透過 `fread` 讀取字元後存進一個字元陣列，模擬 `getchar` 的作用。

```cpp
inline char gc() {
	static const int BUF_SIZE = 1 << 20;
	static char buffer[BUF_SIZE];
	static char* s = buffer + BUF_SIZE;
	static char* e = buffer + BUF_SIZE;
	static int cnt = BUF_SIZE;
	if(s == e) {
		if(cnt < BUF_SIZE) {
			return EOF;
		}
		cnt = fread(buffer, 1, BUF_SIZE, stdin);
		s = buffer, e = buffer + cnt;
	}
	return *s++;
}
```

`fwrite` 也是類似，先把要輸出的東西存在陣列，在直接使用 `fwrite` 一次將全部印出來。

```cpp
const int BUF_SIZE = 1 << 20;
char buffer[BUF_SIZE];
int p = 0;

inline void pc(char c) {
	buffer[p++] = c;
	if(p == BUF_SIZE) {
		output();
	}
}

inline void output() {
	fwrite(buffer, 1, p, stdout);
	p = 0;
}
```

上述的程式碼只有在 `buffer` 滿的時候才會透過 `fwrite` 輸出，因此在程式結束之前我們要手動呼叫 `output` 讓他把剩下的也都輸出。

`fread/fwrite` 也有 `unlocked` 系列的 `fread_unlocked/fwrite_unlocked`，但是速度差異不大。

特別注意有些 OJ 不支援 `fwrite` (像是 Zerojudge)，因此可以用 `fwrite + putchar_unlocked` 的組合。

## tourist IO

這是筆者在 tourist 的 [submission](https://codeforces.com/contest/1603/submission/133678384) 翻到的，並進行了微調。預設使用了 `fread/fwrite` 的組合。如果 OJ 不支援 `fread` 或是 `fwrite`，可以在 `get_char` 和 `put_char` 函式裡面去修改成 `getchar_unlocked/putchar_unlocked`。這個模板的好處是有 `#define cin fast_input` 和 `#define cout fast_output`，因此使用 `cin/cout` 的人可以直接把這段程式碼貼到最上方，不用對 `main` 去做修改 (甚至連關閉同步的那兩行都不用拿掉)，甚至支援 `__int128` 和讀到 EOF！另外筆者把整個包在 `namespace std` 下，並加上了 `#define istream FastInput` 和 `#define ostream FastOutput`，因此可以客製化 `struct` 的輸入輸出。

<details><summary>tourist IO Code</summary>
```cpp
// https://codeforces.com/contest/1603/submission/133678384
namespace std {

static struct FastInput {
	static constexpr int BUF_SIZE = 1 << 20;
	char buf[BUF_SIZE];
	size_t chars_read = 0;
	size_t buf_pos = 0;
	FILE *in = stdin;
	char cur = 0;

	inline char get_char() {
		if(buf_pos >= chars_read) {
			chars_read = fread(buf, 1, BUF_SIZE, in);
			buf_pos = 0;
			buf[0] = (chars_read == 0 ? -1 : buf[0]);
		}
		return cur = buf[buf_pos++];
		// return cur = getchar_unlocked();
	}

	inline void tie(int) {}

	inline explicit operator bool() { return cur != -1; }
	inline static bool is_blank(char c) { return c <= ' '; }

	inline bool skip_blanks() {
		while(is_blank(cur) && cur != -1) {
			get_char();
		}
		return cur != -1;
	}

	inline FastInput& operator>>(char& c) {
		skip_blanks();
		c = cur;
		return *this;
	}

	inline FastInput& operator>>(string& s) {
		if(skip_blanks()) {
			s.clear();
			do {
				s += cur;
			} while(!is_blank(get_char()));
		}
		return *this;
	}

	template<class T>
	inline FastInput& read_integer(T& n) {
		n = 0;
		if(skip_blanks()) {
			int sign = +1;
			if(cur == '-') {
				sign = -1;
				get_char();
			}
			do {
				n += n + (n << 3) + cur - '0';
			} while(!is_blank(get_char()));
			n *= sign;
		}
		return *this;
	}

	template<class T> inline typename enable_if<is_integral<T>::value, FastInput&>::type operator>>(T& n) { return read_integer(n); }
	inline FastInput& operator>>(__int128& n) { return read_integer(n); }

	template<class T>
	inline typename enable_if<is_floating_point<T>::value, FastInput&>::type operator>>(T& n) {
		n = 0;
		if(skip_blanks()) {
			string s;
			(*this) >> s;
			sscanf(s.c_str(), "%lf", &n);
		}
		return *this;
	}
} fast_input;

#define istream FastInput
#define cin fast_input

static struct FastOutput {
	static constexpr int BUF_SIZE = 1 << 20;
	char buf[BUF_SIZE];
	size_t buf_pos = 0;
	static constexpr int TMP_SIZE = 1 << 20;
	char tmp[TMP_SIZE];
	FILE *out = stdout;
 
	inline void put_char(char c) {
		buf[buf_pos++] = c;
		if(buf_pos == BUF_SIZE) {
			fwrite(buf, 1, buf_pos, out);
			buf_pos = 0;
		}
		// putchar_unlocked(c);
	}

	~FastOutput() {
		fwrite(buf, 1, buf_pos, out);
	}

	inline FastOutput& operator<<(char c) {
		put_char(c);
		return *this;
	}

	inline FastOutput& operator<<(const char* s) {
		while(*s) {
			put_char(*s++);
		}
		return *this;
	}

	inline FastOutput& operator<<(const string& s) {
		for(int i = 0; i < (int) s.size(); i++) {
			put_char(s[i]);
		}
		return *this;
	}

	template<class T>
	inline char* integer_to_string(T n) {
		char* p = tmp + TMP_SIZE - 1;
		if(n == 0) {
			*--p = '0';
		} else {
			bool is_negative = false;
			if(n < 0) {
				is_negative = true;
				n = -n;
			}
			while(n > 0) {
				*--p = (char) ('0' + n % 10);
				n /= 10;
			}
			if(is_negative) {
				*--p = '-';
			}
		}
		return p;
	}

	template<class T> inline typename enable_if<is_integral<T>::value, char*>::type stringify(T n) { return integer_to_string(n); }
	inline char* stringify(__int128 n) { return integer_to_string(n); }

	template<class T>
	inline typename enable_if<is_floating_point<T>::value, char*>::type stringify(T n) {
		sprintf(tmp, "%.17f", n);
		return tmp;
	}

	template<class T>
	inline FastOutput& operator<<(const T& n) {
		auto p = stringify(n);
		for(; *p != 0; p++) {
			put_char(*p);
		}
		return *this;
	}
} fast_output;

#define ostream FastOutput
#define cout fast_output

} // namespace std
```
</details>

tourist IO 也可以客製化 `struct` 的輸入輸出，與常見的寫法相同。

```cpp
std::istream& operator>>(std::istream& in, std::pair<int, int> p) {
	return in >> p.first >> p.second;
}

std::ostream& operator<<(std::ostream& out, std::pair<int, int> p) {
	return out << p.first << " " << p.second;
}
```

## References

- [tourist IO](https://codeforces.com/contest/1603/submission/133678384)
