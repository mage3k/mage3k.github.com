---
layout: post
title: ECMAScript Number
date: 2017-05-04
disqus: yes
---

在知乎上看到贺老回答的一个问题 [为什么JS严格模式要禁止使用八进制字面量？](https://www.zhihu.com/question/59180998)，看完以后才知道 JavaScript 里 Number 还能这么用。于是去查了一下文档，重新认识了一下 Number。

以下是 ECMASCript 中，Number 的语法组成

* NumericLiteral::
  * DecimalLiteral
  * BinaryIntegerLiteral
  * OctalIntegerLiteral
  * HexIntegerLiteral
* DecimalLiteral::
  * DecimalIntegerLiteral.DecimalDigitsopt ExponentPartopt
  * .DecimalDigits ExponentPartopt
  * DecimalIntegerLiteral ExponentPartopt
* DecimalIntegerLiteral::
  * 0
  * NonZeroDigitDecimalDigitsopt
* DecimalDigits::
  * DecimalDigit
  * DecimalDigits DecimalDigit
* DecimalDigit::one of
  * 0 1 2 3 4 5 6 7 8 9
* NonZeroDigit::one of
  * 1 2 3 4 5 6 7 8 9
* ExponentPart::
  * ExponentIndicator SignedInteger
* ExponentIndicator::one of
  * e E
* SignedInteger::
  * DecimalDigits
  * +DecimalDigits
  * -DecimalDigits
* BinaryIntegerLiteral::
  * 0b BinaryDigits
  * 0B BinaryDigits
* BinaryDigits::
  * BinaryDigit
  * BinaryDigitsBinaryDigit
* BinaryDigit::one of
  * 0 1
* OctalIntegerLiteral::
  * 0o OctalDigits
  * 0O OctalDigits
* OctalDigits::
  * OctalDigit
  * OctalDigitsOctalDigit
* OctalDigit::one of
  * 0 1 2 3 4 5 6 7
* HexIntegerLiteral::
  * 0x HexDigits
  * 0X HexDigits
* HexDigits::
  * HexDigit
HexDigitsHexDigit
* HexDigit::one of
  * 0 1 2 3 4 5 6 7 8 9 a b c d e f A B C D E F

根据这份标准，Number 有就有下面的几种写法

```
// 十进制
0
0.1
0.1e+5
0.1E-5
.1e-5
0e-5
2E+5

// 二进制
0b1010
0B0011

// 八进制
0O4
0o007

// 十六进制
0x0d9d93
0X83aaAeD
```

这是一份附加语法，在严格模式下是不可用的，也就是上面提到的知乎的问题

* NumericLiteral::
  * DecimalLiteral
  * BinaryIntegerLiteral
  * OctalIntegerLiteral
  * HexIntegerLiteral
  * LegacyOctalIntegerLiteral
* LegacyOctalIntegerLiteral::
  * 0 OctalDigit
  * LegacyOctalIntegerLiteralOctalDigit
* DecimalIntegerLiteral::
  * 0
  * NonZeroDigitDecimalDigits<sub>opt</sub>
  * NonOctalDecimalIntegerLiteral
* NonOctalDecimalIntegerLiteral::
  * 0 NonOctalDigit
  * LegacyOctalLikeDecimalIntegerLiteral NonOctalDigit
  * NonOctalDecimalIntegerLiteral DecimalDigit
* LegacyOctalLikeDecimalIntegerLiteral::
  * 0 OctalDigit
  * LegacyOctalLikeDecimalIntegerLiteral OctalDigit
* NonOctalDigit:: one of
  * 8 9

```
0747

```

