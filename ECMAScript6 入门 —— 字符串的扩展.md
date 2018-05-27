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
'\152' === 'j'		// true
'\x6a' === 'j'		// true
'\u006a' === 'j'		// true
'\u{6a}' === 'j'		// true
```