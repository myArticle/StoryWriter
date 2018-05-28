---
title: ECMAScript6 入门 —— 字符串的扩展
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---


# 字符串的扩展


---
## 1.字符的 Unicode 表示法

JavaScript 允许采用 `\u****` 的形式表示一个字符，其中 `****` 表示字符的 Unicode 码。
``` javascript
{
	'\u0041'		// "A"
	'\u005a'		// "Z"
	'\u0061'		// "a"
	'\u007a'		// "z"
}

{
	'A'.length === 1		// true
	Array.from('A').length === 1		// true
}

{
	'A'.charCodeAt(0)		// 65
	String.fromCharCode(65)		// "A"
}

{
	'A'.codePointAt(0)		// 65
	String.fromCodePoint(65)		// "A"
}
```

这种表示法只限于 Unicode 码在 `\u0000` 到 `\uFFFF` 之间的字符。超出这个范围的字符，需要用两个双字节的形式表示。

``` javascript
{
	'\uD83D\uDCA9' === '💩'		// true
	'\uD842\uDFB7' === '𠮷'		// true
}

// 注意：字符串的 length 属性
{
	'💩'.length === 2	// true
	Array.from('💩').length === 1	// true
}

{
	'💩'.charCodeAt(0)		// 55357
	String.fromCharCode(55357)		// "�"
}

{
	'💩'.codePointAt(0)		// 128169
	String.fromCodePoint(128169)		// "💩"
}

// 注意：字符串的反转
{
	"💩abc".split("").reverse().join("")		// "cba��"
	Array.from("💩abc").reverse().join("")		// "cba💩"
}

```

`\u` 后面如果是超过 `0xFFFF` 的数值， JavaScript 会理解成 `\u**** + ***` 拼接成的字符串。 幸运地是 ES6 对此进行了改进，只要将 unicode 码 放入 `{}` 内，就能得到期望的结果字符。

``` javascript
{
	'\u88888'		// "袈8"
	'\u8888'		// "袈"
}

{
	"𠮷".codePointAt(0)		// 134071
	Number(134071).toString(16)		// "20bb7"
	'\u{20bb7}'		// "𠮷"
}

{
	let jl = 'wuxie'
	j\u{6c} === jl		// true
}
```

所以，在 JavaScript 中，一个字符串可以有六种写法。
``` javascript
'j'.codePointAt(0)		// 106 => 十进制
Number(106).toString(16)		// "6a" => 十六进制

'\j' === 'j'		// true
// '\152' === 'j'		// true
'\x6a' === 'j'		// true
'\u006a' === 'j'		// true
'\u{6a}' === 'j'		// true
```


---
## 2.codePointAt()

JavaScript 内部，字符串以 UTF-16 的格式存储，每个字符固定为 2 个字符。对于那些需要 4 个字节存储的字符（Unicode 码大于 `0xFFFF` 的字符），JavaScript 会认为它们是两个字符。
比如，汉字 “𠮷”（拼音： jí ）， Unicode 编码 `0x20bb7`	，十进制 `134071` ， UTF-16 `0xD842 0xDFB7` （十进制为  `55362 57271` ）， UTF-32 `00020bb7` ， UTF-8 `F0 A0 AE B7` ，需要 4 个字节存储。对于这种 4 个字节的字符， JavaScript 不能正确处理，字符串长度会误判为 2 ，而且 `charAt()` 无法正确地读取整个字符， `charCodeAt()` 只能返回前两个字节和后两个字节的值。
ES6 提供了 `codePointAt()` ，能够正确处理 4 个字节储存的字符，返回字符的 Unicode 十进制码。可以通过 `toString()` 转为其它进制。
一个字节为 8 位，每位用二进制表示，最大表示值为 `!$ 2^{8}-1 $` （即 255 ， 0xFF ）。

``` javascript
{
	let str = '𠮷'
	
	str.codePointAt(0)		// 134071
	
	str.length		// 2
	str.charAt(0)		// "�"
	str.charAt(1)		// "�"
	str.charCodeAt(0)		// 55362
	str.charCodeAt(1)		// 57271
	str.codePointAt(0)		// 134071
	str.codePointAt(1)		// 57271
	
	Number(134071).toString(16)		// "20bb7"
	Number(55362).toString(16)		// "d842"
	Number(57271).toString(16)		// "dfb7"
	
	'\u{20bb7}'		// "𠮷"
	'\uD842\uDFB7'		// "𠮷"
}
```

`codePointAt()` 接收一个可选参数，参数的取值范围取决于调用该函数的字符串的长度（ `0 ~ length-1` ），因为 JavaScript 对 `length` 属性的处理，并不是我们所期望得到的结果。所以，为了正确识别 32 位的 UTF-16 字符，可以使用 `for...of` 循环。

``` javascript
{
	let str = 'a💩b𠮷c'

	{
		str.length		// 7
		for (let i = 0; i < str.length; i++) {
			console.warn(str.codePointAt(i))
		}

		// 97
		// 128169
		// 56489
		// 98
		// 134071
		// 57271
		// 99
	}

	{
		Array.from(str).length		// 5
		for(let s of str) {
			console.info(s.codePointAt(0))
		}

		// 97
		// 128169
		// 98
		// 134071
		// 99
	}

}
```

`codePointAt()` 可以用来测试一个字符是否由两个字节组成。

``` javascript
{
	function is16Bit(character) {
		return character.codePointAt(0) < 0x10000
	}
	
	is16Bit('𠮷')		// false
	is16Bit('j')		// true
}
```


---
## 3. String.fromCodePoint()

ES5 提供的 `String.fromCharCode()` ，用于返回 Unicode 码对应的字符，与 `charCodeAt()` 用于返回字符对应的 Unicode 码配对使用。但，这个方法只能正确识别 16 位的字符集（Unicode 码小于 `0x10000`），不能正确识别 32 位及更多位的字符集。对于大于 `0xffff` 的 Unicode 码，会舍弃高位，识别末尾的 4 位 16 进制码。
ES6 提供 `String.fromCodePoint()` ，用于弥补 `String.fromCharCode()` 的不足。
注意： `fromCodePoint()` 定义在 `String` 对象上，而 `codePointAt()` 定义在字符串的实例对象上。

``` javascript
{
	String.fromCharCode(0x20bb7)		// "ஷ"
	String.fromCharCode(0x0bb7)		// "ஷ"
	String.fromCodePoint(0x20bb7)		// "𠮷"
}

{
	let str = '𠮷'
	str.charCodeAt(0)		// 55362
	str.charCodeAt(1)		// 57271
	str.codePointAt(0)		// 134071
	str.codePointAt(1)		// 57271
}
```

`String.fromCodePoint()` 可以接收多个参数，然后返回这些参数合并后的字符串。

``` javascript
{
	let a = '0x61'
	'\u0061'		// "a"
	let b = '0x62'
	'\u0062'		// "b"
	let c = '0x20bb7'
	'\u{20bb7}'		// "𠮷"
	String.fromCodePoint(a, b, c)		// "ab𠮷"
	'ab\u{20bb7}'		// "ab𠮷"
}
```


## 4. 字符串的遍历器接口

ES6 为字符串添加了遍历器接口（ Iterator ），使得字符串可以被 `for...of` 循环遍历。

``` javascript
{
	let str = 'abcd'
	for (let c of str) {
		console.info(c)
	}
	
	// a
	// b
	// c
	// d
}
```