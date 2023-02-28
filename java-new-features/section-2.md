# Java 1.7

- switch 字符串参数
- 二进制数字
- try-with-resources
- catch 可包含多个异常类型
- 数字分隔符

## 二进制数字

```java
// An 8-bit 'byte' value:
byte aByte = (byte)0b00100001;

// A 16-bit 'short' value:
short aShort = (short)0b1010000101000101;

// Some 32-bit 'int' values:
int anInt1 = 0b10100001010001011010000101000101;
int anInt2 = 0b101;
int anInt3 = 0B101; // The B can be upper or lower case.

// A 64-bit 'long' value. Note the "L" suffix:
long aLong = 0b1010000101000101101000010100010110100001010001011010000101000101L;
```

## 数字分隔符

```java
// look like 100,000,000
int anInt = 100_000_000;
```

