---
title: CSAPP 第二章实验 datalab-handout
date: 2020-11-19 17:04:03
tags:
  - CSAPP
  - 编码
---

CSAPP 第二章实验题解

<!-- more -->

```C
//1
/* 
 * bitXor - x^y using only ~ and & 
 *   Example: bitXor(4, 5) = 1
 *   Legal ops: ~ &
 *   Max ops: 14
 *   Rating: 1
 */
int bitXor(int x, int y) {
  return ~(~x & ~y) & ~(x & y);
}
/* 
 * tmin - return minimum two's complement integer 
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 4
 *   Rating: 1
 */
int tmin(void) {
  return 1 << 31;
}
//2
/*
 * isTmax - returns 1 if x is the maximum, two's complement number,
 *     and 0 otherwise 
 *   Legal ops: ! ~ & ^ | +
 *   Max ops: 10
 *   Rating: 1
 */
int isTmax(int x) {
  return !(~(x + 1 + x) | !(x + 1));
}
/* 
 * allOddBits - return 1 if all odd-numbered bits in word set to 1
 *   where bits are numbered from 0 (least significant) to 31 (most significant)
 *   Examples allOddBits(0xFFFFFFFD) = 0, allOddBits(0xAAAAAAAA) = 1
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 2
 */
int allOddBits(int x) {
  x = x >> 16 & x;
  x = x >> 8 & x;
  x = x >> 4 & x;
  x = x >> 2 & x;
  return x >> 1 & 1;
}
/* 
 * negate - return -x 
 *   Example: negate(1) = -1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 5
 *   Rating: 2
 */
int negate(int x) {
  return (~ x) + 1;
}
//3
/* 
 * isAsciiDigit - return 1 if 0x30 <= x <= 0x39 (ASCII codes for characters '0' to '9')
 *   Example: isAsciiDigit(0x35) = 1.
 *            isAsciiDigit(0x3a) = 0.
 *            isAsciiDigit(0x05) = 0.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 15
 *   Rating: 3
 */
int isAsciiDigit(int x) {
  int s = 1 << 31;
  int upper = ~(s | 0x39);
  int lower = ~ 0x30 + 1;
  return ! (((x + lower) & s) | ((x + upper) & s));
}
/* 
 * conditional - same as x ? y : z 
 *   Example: conditional(2,4,5) = 4
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 16
 *   Rating: 3
 */
int conditional(int x, int y, int z) {
  int mask = !!x;
  mask = ~ mask + 1;
  return (mask & y) | (~mask & z);
}
/* 
 * isLessOrEqual - if x <= y  then return 1, else return 0 
 *   Example: isLessOrEqual(4,5) = 1.
 *   Legal ops: ! ~ & ^ | + << >>
 *   Max ops: 24
 *   Rating: 3
 */
int isLessOrEqual(int x, int y) {
  int sig_mask = 1 << 31;
  int sig_x = sig_mask & x;
  int sig_y = sig_mask & y;
  int sig_y_x = sig_mask & (y + (~ x + 1));
  return !!(((sig_x & ~ sig_y) | (~(sig_x ^ sig_y) & ~sig_y_x)) & sig_mask);
}
//4
/* 
 * logicalNeg - implement the ! operator, using all of 
 *              the legal operators except !
 *   Examples: logicalNeg(3) = 0, logicalNeg(0) = 1
 *   Legal ops: ~ & ^ | + << >>
 *   Max ops: 12
 *   Rating: 4 
 */
int logicalNeg(int x) {
  return ((x | (~ x + 1)) >> 31) + 1;
}
/* howManyBits - return the minimum number of bits required to represent x in
 *             two's complement
 *  Examples: howManyBits(12) = 5
 *            howManyBits(298) = 10
 *            howManyBits(-5) = 4
 *            howManyBits(0)  = 1
 *            howManyBits(-1) = 1
 *            howManyBits(0x80000000) = 32
 *  Legal ops: ! ~ & ^ | + << >>
 *  Max ops: 90
 *  Rating: 4
 */
int howManyBits(int x) {
  int temp = x ^ (x >> 31); // 对负数取反，非负数不变
  int isZero = !temp;
  int notZeroMask = ((!!temp) << 31) >> 31;
  int bit_16, bit_8, bit_4, bit_2, bit_1;

  bit_16 = (!!(temp >> 16)) << 4;
  temp = temp >> bit_16;

  bit_8 = (!!(temp >> 8)) << 3;
  temp = temp >> bit_8;

  bit_4 = (!!(temp >> 4)) << 2;
  temp = temp >> bit_4;

  bit_2 = (!!(temp >> 2)) << 1;
  temp = temp >> bit_2;

  bit_1 = !!(temp >> 1);
  temp = bit_16 + bit_8 + bit_4 + bit_2 + bit_1 + 2;    //我们每次只考虑高位，最低那一位是没有被计算的，但是不管什么数都要使用最低的那一位，另外最高位的符号位也是需要使用的

  return isZero | (temp & notZeroMask);
}
//float
/* 
 * floatScale2 - Return bit-level equivalent of expression 2*f for
 *   floating point argument f.
 *   Both the argument and result are passed as unsigned int's, but
 *   they are to be interpreted as the bit-level representation of
 *   single-precision floating point values.
 *   When argument is NaN, return argument
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
unsigned floatScale2(unsigned uf) {
  unsigned sign, exp, frac;
  sign = uf >> 31;
  exp = (uf >> 23) & 0xff;
  frac = uf & 0x7fffff;

  if (exp == 0xff && frac) return uf;

  if (exp == 0) {
    frac = frac << 1;
    if (frac & 0x800000) exp += 1;
  }
  else 
    exp += 1;
  if (exp >= 0xff) {
    exp = 0xff;
    frac = 0;
  }
  return (sign << 31) | (exp << 23) | (frac);
}
/* 
 * floatFloat2Int - Return bit-level equivalent of expression (int) f
 *   for floating point argument f.
 *   Argument is passed as unsigned int, but
 *   it is to be interpreted as the bit-level representation of a
 *   single-precision floating point value.
 *   Anything out of range (including NaN and infinity) should return
 *   0x80000000u.
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. also if, while
 *   Max ops: 30
 *   Rating: 4
 */
int floatFloat2Int(unsigned uf) {
  int sign, exp, frac;
  sign = uf >> 31;
  exp = (uf >> 23) & 0xff;
  frac = uf & 0x7fffff;

  exp -= 127;

  if (exp < 0) return 0;
  if (exp >= 31) return 0x80000000;
  frac = frac | 0x800000;
  if (exp > 23) frac <<= (exp - 23);
  else frac >>= (23 - exp);
  return sign ? -frac : frac;
}
/* 
 * floatPower2 - Return bit-level equivalent of the expression 2.0^x
 *   (2.0 raised to the power x) for any 32-bit integer x.
 *
 *   The unsigned value that is returned should have the identical bit
 *   representation as the single-precision floating-point number 2.0^x.
 *   If the result is too small to be represented as a denorm, return
 *   0. If too large, return +INF.
 * 
 *   Legal ops: Any integer/unsigned operations incl. ||, &&. Also if, while 
 *   Max ops: 30 
 *   Rating: 4
 */
unsigned floatPower2(int x) {
  if (x >= 128) return 0x7f800000;
  if (x <= -127) return 0;
  return (x + 127) << 23;
}
// 这道题虽然很简单，但是判题显示超时，我不知道为什么

```
