---
title: CSAPP 第二章 信息的表示和处理 家庭作业
date: 2020-11-19 15:31:11
tags:
  - CSAPP
  - 编码
---

从前段时间开始看CSAPP，到现在差不多看完了第三章的内容，上传一下自己的做题记录，当备份了
部分解答参考了网络答案，因为是两周前做的，现在记不得当时参考了哪些博客了，就不贴链接了

<!-- more -->


***

2.55-2.57 略

***

2.58


```C
int is_little_endian() {
    int a = 1;
    return *((char *) &a);
}
```
***

2.59


(x & 0xFF) | (y & ~0xFF)
***

2.60


自己第一遍做的答案

```C
unsigned replace_byte(unsigned x, int i, unsigned char b) {
    unsigned char* ptr = &x;
    *(ptr + i) = b;
}
```
参考网上解答的答案：
```C
unsigned replace_byte1(unsigned x, int i, unsigned char b) {
    return (x & ~( 0xff << (i << 3))) | (b << (i << 3));
}
```
***

2.61


A. ! ~ x
B. ! x
C. ! ~(x ~ 0xFF)
D. !(x >> ((sizeof(int) - 1) << 3))
***

2.62


```C
int int_shifts_are_arithmetic() {
    return !!((~ 0) >> ((sizeof(int)- 1) << 3));
}
```
***

2.63


```C
unsigned srl(unsigned x, int k) {
    unsigned xsra = (int) x >> k;
    int w = sizeof(int) << 3;
    unsigned mask = 1 << (w - k) - 1;
    return mask & xsra;
}

unsigned sra(int x, int k) {
    int xsrl = (unsigned) x >> k;
    int w = sizeof(int) << 3;
    unsigned z = 1 << (w - k - 1);
    unsigned mask = z - 1;
    unsigned right  = mask & xsrl;
    unsigned left = ~ mask & (~(z & xsrl) + z);
    return left | right; 
}
```
***

2.64

```C
int any_odd_one(unsigned x) {
    return !!(x & 0x55555555);
}
```
***

2.65

```C
int odd_ones(unsigned x) {
    x ^= x >> 16;
    x ^= x >> 8;
    x ^= x >> 4;
    x ^= x >> 2;
    x ^= x >> 1;
    return !(x & 1);
}
```
***

2.66

```C
int leftmost_one(unsigned x) {
    x = x | (x >> 1);
    x = x | (x >> 2);
    x = x | (x >> 4);
    x = x | (x >> 8);
    x = x | (x >> 16);
    return (x >> 1) ^ x;
}
```
***

2.67

A. 移位操作中，位移数量为负数或者大于等于32都是未定义行为，引自微软官方文档中对移位操作的解释：
>The result of a shift operation is undefined if additive-expression is negative or if additive-expression is greater than or equal to the number of bits in the (promoted) shift-expression. No shift operation is performed if additive-expression is 0.

B. int beyond_msb = set_msb << 1;
C. int a = 1 << 15; int b = a << 1; int set_msb = b << 15; int beyond_msb = set_msb << 1;

***

2.68

```C
int lower_one_mask(int n) {
    return (1 << n - 1) - 1 << 1 | 1;
}
```
***

2.69

```C
unsigned rotate_left(unsigned x, int n) {
    int w = sizeof(unsigned) << 3;
    return x << n | x >> w - n;
}
```
***

2.70

```C
int fits_bits(int x, int n) {
    x >>= (n - 1);
    return !x || !(~x);
}
```
***

2.71

A. 没有实现符号扩展
B. 
```C
int xbyte(packed_t word, int bytenum) {
    int x = word << ((3 - bytenum) << 3);
    return x >> 24;
}
```
***

2.72

A. 无符号数的运算结果还是无符号数，即大于等于0
B. maxbytes >= sizeof(val)

***

2.73

```C
int saturating_add(int x, int y) {
    int sum = x + y;
    int w = (sizeof(int) << 3) - 1;
    int xneg_mask = x >> w;
    int yneg_mask = y >> w;
    int sum_mask = sum >> w;
    int pos_over_mask = ~ xneg_mask & ~ yneg_mask & sum_mask;
    int neg_over_mask = xneg_mask & yneg_mask & (~ sum_mask);
    int over_mask = pos_over_mask | neg_over_mask;
    return (~ over_mask & sum) | (pos_over_mask & INT_MAX) | (neg_over_mask & INT_MIN);
}
```
***

2.74

```C
int tsub_ok(int x, int y) {
    return !((x >= 0 && y < 0 && x - y <= 0) || (x < 0 && y > 0 && x - y >= 0));
}
```
***

2.75

因为乘积长度为2w，由公式2.18，对2^2w取模，可得如下公式
(x^'⋅y^' )_h=(x⋅y)_h+x_(w-1)⋅y+y_(w-1)⋅x

```C
unsigned unsigned_high_prod(unsigned x, unsigned y) {
    int w = sizeof(unsigned) << 3;
    return signed_high_prod(x, y) + (x >> w - 1) & y + (y >> w - 1) & x;
}
```
***

2.76

```C
void *calloc (size_t nmemb, size_t size) {
    if (nmemb == 0 || size == 0 || nmemb * size / nmemb != size)
        return NULL;
    void *p = malloc(nmemb * size);
    memset(p, 0, size * nmemb);
    return p;
}
```
***

2.78

```C
int divide_power2(int x, int k) {
    int bias = (x >> sizeof(int) - 1) & (((unsigned)1 << k) - 1);
    return x + bias >> k;
}
```
***

2.79

```C
int mul3div4(int x) {
    x = (x << 1) + x;
    int bias = (x >> (sizeof(int) << 3) - 1) & 3;
    return x + bias >> 2;
}
```
***

2.80

```C
int threefourth(int x) {
    int w = sizeof(int) << 3;
    int ans = (x >> 1) + (x >> 2);
    int x0 = 1 & x;
    int x1 = 1 & (x >> 1);
    ans += x0 & x1;
    ans += (x >> w - 1) && (x & 0x3);
    return ans;
}
```
***

2.81

A. -1 << k
B. -1 << j & 1 << k + j

***

2.82

A. False : case x = 0x8fffffff and y = 0xffffffff
B. True : 补码运算的加减乘和顺序无关
C. False : case x = 1 and y = -1
D. True : 无符号数和符号数的位级运算是一致的
F. True : 补码中无论正负，低位清零都会导致变小

***

2.83

A. Y / (2 ^ k - 1)
B. (a) 5/7
(b) 6/15 = 2/5
(c) 19/63

***

2.84

((sx || sy) && (ux >= uy)) || (!sx && !sy && sx <= sy)

***

2.86-2.88 略

***

2.89

A. True: 因为double可以精确表示32位整数，所以x转成double再转成float和直接转成float没有区别
B. False: int相减会有溢出，但是转成double后运算不会产生溢出
C. True: int转成double后三个数不会产生溢出或舍入问题
D. False: double无法精确表示2^64范围的值，中间会有舍入问题，运算顺序的不同会产生不同的结果，例子我举不出来
E. False: case x != 0 and z = 0

***

2.90

```C
float u2f(unsigned u) {
    return *((float*) &u);
}

float fpwr2(int x) {
    unsigned exp, frac;
    unsigned u;

    if (x < -149) {
        exp = 0;
        frac = 0;
    }
    else if (x < -126) {
        exp = 0;
        frac = 1 << (23 + x + 126);
    }
    else if (x < 128) {
        exp = x + 127;
        frac = 0;
    }
    else {
        exp = 0xff;
        frac = 0;
    }

    u = exp << 23 | frac;
    return u2f(u);
}
```
***

2.91

A. 110010.000111111011011
B. 11.001[001]
C. 二进制指数表示，小数点后第十位

***

2.92

```C
float_bits float_negate(float_bits f) {
    unsigned exp = f >> 23 & 0xff;
    unsigned frac = f & 0x7fffff;
    if (exp == 0xff && frac) return f;
    unsigned sig = f >> 31;
    return ((~sig) << 31) | exp << 23 | frac;
}
```
***

2.93

```C
int is_NaN(float_bits f) {
    unsigned exp = f >> 23 & 0xff;
    unsigned frac = f & 0x7fffff;
    return exp == 0xff && frac;
}

float_bits float_absval(float_bits f) {
    if (is_NaN(f)) return f;
    return (((unsigned)-1) >> 1) & f;
}
```
***

2.94

```C
float_bits float_twice(float_bits f) {
    if (is_NaN(f)) return f;
    unsigned exp = f >> 23 & 0xff;
    unsigned frac = f & 0x7fffff;
    if (exp == 0) {
        return (f & ~ 0x7fffff) | (frac << 1);
    }
    else if (exp >= 0xff - 1){
        return f & (1 << 31) | (0xff << 23);
    }
    else {
        exp += 1;
        return f & (1 < 31) | (exp << 23) | frac;
    }
}
```
***

2.95

```C
float_bits float_half(float_bits f) {
    if (is_NaN(f)) return f;
    unsigned exp = f >> 23 & 0xff;
    unsigned frac = f & 0x7fffff;
    if (exp == 0) {
        return (f & ~ 0x7fffff) | (frac >> 1);
    }
    else if (exp >= 0xff) {
        return f & (1 << 31) | (0xff << 23);
    }
    else if (exp == 1) {
        return (f & ~ 0x7fffff) | (frac >> 1) | (1 << 22);
    }
    else {
        exp -= 1;
        return f & (1 < 31) | (exp << 23) | frac;
    }
}
```
***

2.96

```C
int float_f2i(float_bits f) {
    int exp = f >> 23 & 0xff;
    int frac = (f & 0x7fffff) | (1 << 23);
    unsigned sign  = f >> 31;

    exp -= 127;

    if (exp < 0) return 0;
    if (exp >= 31) return 0x80000000;
    if (exp > 23) frac <<= (exp - 23);
    else frac >>= (23 - exp);
    return sign ? -frac : frac;
}
```
***

2.97

```C
float_bits float_i2f(int i) {

    unsigned sign, exp, frac;
    unsigned bias = 127;
    int w = sizeof(int) << 3;

    sign = i >> (w - 1);
    unsigned t = i;
    if (sign) {
        t = -t;
    }

    int num = 0;
    for (num = w - 1; num >= 0; num--) {
        if ((t >> num) && 1) break;
    }
    exp = t ? bias + num : 0;

    if (num <= 23) frac = t << (23 - num);
    else {
        frac = t >> (num - 23);
        unsigned mask = (1 << (num - 23)) - 1;
        if ((t & mask) > (1 << (num - 24))) frac++;
        else if ((t & mask) == 1 << (num - 24) && frac & 1) frac++;
        if (frac == (1 << 24)) exp++;
    }
    return sign << 31 | exp << 23 | frac & 0x7fffff;
}
```
