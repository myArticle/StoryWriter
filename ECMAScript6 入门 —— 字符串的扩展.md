---
title: ECMAScript6 入门 —— 字符串的扩展
tags: 新建,模板,小书匠
grammar_cjkRuby: true
---


# 字符串的扩展


---
## 1. 字符的 Unicode 表示法

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
## 2. codePointAt()

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

遍历器还可以识别大于 `0xffff` 的 Unicode 码。传统的 `for` 循环无法识别，但 `for...of` 循环会正确识别。

``` javascript
{
	let str = '𠮷'
	
	// 传统的 for 循环
	{
		for (let i = 0; i < str.length; i++) {
			console.warn(str[i])
		}
		
		// �
		// �
	}
	
	{
		for (let c of str) {
			console.info(c)
		}
		
		// 𠮷
	}
}
```


---
## 5. at()

ES5 提供了 `charAt()` ，返回字符串给定位置的字符。该方法期望返回的是用 2 个字节表示的字符，所以不能识别 Unicode 码大于 `0xffff` 的字符。

``` javascript
'abcd'.charAt(2)		// "c"
'𠮷'.charAt(0)		// "�"
```

目前，有一个[提案](https://github.com/es-shims/String.prototype.at/blob/master/at.js)，提出字符串实例的 `at()` 方法，可以识别 Unicode 码大于 `0xffff` 的字符，返回正确的字符。

``` javascript
/*! https://mths.be/at v0.2.0 by @mathias */
if (!String.prototype.at) {
	(function() {
		'use strict'; // needed to support `apply`/`call` with `undefined`/`null`
		var defineProperty = (function() {
			// IE 8 only supports `Object.defineProperty` on DOM elements.
			try {
				var object = {};
				var $defineProperty = Object.defineProperty;
				var result = $defineProperty(object, object, object) && $defineProperty;
			} catch (exception) {}
			return result;
		}());
		var at = function(position) {
			if (this == null) {
				throw TypeError();
			}
			var string = String(this);
			var size = string.length;
			// `ToInteger`
			var index = position ? Number(position) : 0;
			if (index != index) { // better `isNaN`
				index = 0;
			}
			// Account for out-of-bounds indices
			// The odd lower bound is because the ToInteger operation is
			// going to round `n` to `0` for `-1 < n <= 0`.
			if (index <= -1 || index >= size) {
				return '';
			}
			// Second half of `ToInteger`
			index = index | 0;
			// Get the first code unit and code unit value
			var cuFirst = string.charCodeAt(index);
			var cuSecond;
			var nextIndex = index + 1;
			var len = 1;
			if ( // Check if it’s the start of a surrogate pair.
				cuFirst >= 0xD800 && cuFirst <= 0xDBFF && // high surrogate
				size > nextIndex // there is a next code unit
			) {
				cuSecond = string.charCodeAt(nextIndex);
				if (cuSecond >= 0xDC00 && cuSecond <= 0xDFFF) { // low surrogate
					len = 2;
				}
			}
			return string.slice(index, index + len);
		};
		if (defineProperty) {
			defineProperty(String.prototype, 'at', {
				'value': at,
				'configurable': true,
				'writable': true
			});
		} else {
			String.prototype.at = at;
		}
	}());
}

// at()
'abcd'.at(2)		// "c"
'𠮷'.at(0)		// "𠮷"
```


---
## 6. normalize()

许多欧洲语言有语调符号和重音符号，为了表示它们， Unicode 码提供了两种方法。
法一：提供带重音符号的字符，比如 `Ǒ` （`\u01d1`）。
法二：提供合成符号，及原字符与重音符号的合成，两个字符合成一个字符，比如 `O` （`\u004f`）和  ̌ (`\u030c`)合成 `Ǒ`（`\u004f\u030c`）。

``` javascript
{
	'\u01d1'		// "Ǒ"
	'\u004f\u030c'		// "Ǒ"
}
```

但是，用这两种方法表示的同一字符，JavaScript 会将合成字符视为两个字符，从而认为它们不相等。

``` javascript
{
	let a = '\u01d1',
		b = '\u004f\u030c'
	a == b		// false
}
```

ES6 提供字符串实例的函数 `normalize()` ，用来将字符的不同表示方法统一为同样的形式，这被称为 Unicode 正规化。

``` javascript
{
	let a = '\u01d1',
		b = '\u004f\u030c'
	a.normalize() == b.normalize()		// true
}
```

`normalize()` 接收一个可选参数来指定正规化的方式，参数的四个可选值如下：

- `NFC`，默认参数，表示“标准等价合成”，返回多个简单字符的合成字符。所谓“标准等价”指的是视觉上和语义上的等价。
- `NFD` ，表示“标准等价分解”，即在标准等价的前提下，返回合成字符分解的多个简单字符。
- `NFKC` ，表示“兼容等价合成”，返回合成字符。所谓“兼容等价”指的是语义上存在等价，但视觉上不等价。
- `NFKD` ，表示“兼容等价分解”，即在兼容等价的前提下，返回合成字符分解的多个简单字符。

`NF[K]C` 参数返回字符的合成形式，`NF[K]D` 参数返回字符的分解形式。

不过， 目前 `normalize()` 不能识别三个或三个以上字符的合成。

``` javascript
{
	let a = '\u004f\u030c'
	
	a.length		// 2
	let b = a.normalize('NFC'),
		c = a.normalize('NFD'),	
		d = a.normalize('NFKC'),
		e = a.normalize('NFKD')
		
	a.length		// 2
	b.length		// 1
	c.length		// 2
	d.length		// 1
	e.length		// 2
	
	a == b		// false
	a == c		// true
	a == d		// false
	a == e		// true
	
	b == c		// false
	b == d		// true
	b == e		// false
	
	c == d		// false
	c == e		// true
	
	d == e		// false
}
```


---
## 7. includes() , startsWidth() , endsWith()

之前， JavaScript 只有 `indexOf()` 方法，可以用来确定一个字符是否包含在另一个字符串中。

``` javascript
{
	let str = 'wuxie'
	str.indexOf('x')		// 2
}
```

ES6 提供了三种新方法：

- includes()：返回布尔值，表示是否包含参数字符串。
- startsWith()：返回布尔值，表示参数字符串是否在原字符串的头部。
- endsWith()：返回布尔值，表示参数字符串是否在原字符串的尾部。

``` javascript
{
	let str = 'wuxie'
	str.includes('x')		// true
	str.includes('a')		// false
	str.startsWith('w')		// true
	str.startsWith('a')		// false
	str.endsWith('e')		// true
	str.endsWith('a')		// false
}
```

这三个方法都支持第二个参数，表示开始搜索的位置下标。但 `endsWith()` 的第二个参数有些不同，表示结束搜索的位置下标（也可以理解为前 n 位）。
``` javascript
{
	let str = 'wuxie'
	str.includes('x', 2)		// true
	str.includes('x', 3)		// false
	str.startsWith('w', 0)		// true
	str.startsWith('w', 1)		// false
	str.startsWith('u', 1)		// true
	str.endsWith('e', 1)		// false
	str.endsWith('e', 5)		// true
	str.endsWith('w', 1)		// true
}
```


---
## 8. repeat()

`repeat()` 方法返回一个新字符串，用来将原字符串重复 n 次。

``` javascript
{
	let a = '12345'
	a.repeat(2)		// "1234512345"
	a		// "12345"
	a.repeat()		// ""
	a		// "12345"
	a.repeat(0)		// ""
	a		// "12345"
}
```

参数 n 如果是小数，会被向下取整（也可以理解为忽略小数点及其后面的数值）。

``` javascript
{
	let a = '12345'
	a.repeat(2.1)		// "1234512345"
	a		// "12345"
	a.repeat(2.9)		// "1234512345"
	a		// "12345"
}
```

参数 n 如果是负数或者 `Infinity` ，会抛出错误。

``` javascript
{
	let a = '12345'
	// a.repeat(-2.1)		// Uncaught RangeError: Invalid count value
	a		// "12345"
	// a.repeat(Infinity)		// Uncaught RangeError: Invalid count value
	a		// "12345"
}
```

但是，如果参数是 `0 ~ -1` 之间的小数，则等同于 `0` 。这是因为会先进行取整运算， `0 ~ -1` 取整之后等于 `-0` ，`repeat()` 视同为 `0` 。

``` javascript
{
	let a = '12345'
	a.repeat(-0.9)		// ""
	a		// "12345"
}
```

如果参数是 `NaN` ，等同于 `0` 。

``` javascript
{
	let a = '12345'
	a.repeat(NaN)		// ""
	a		// "12345"
}
```

如果参数是字符串，则先将参数字符串转为数值，再操作调用函数的字符串实例。
``` javascript
{
	let a = '12345'
	
	Number('2abcd')		// NaN
	Number('2')		// 2
	Number('abcd')		// NaN
	
	a.repeat('2abcd')		// ""
	a.repeat('2')		// "1234512345"
	a.repeat('abcd2')		// ""
	a.repeat('abcd')		// ""
	a		//	"12345"
}
```


