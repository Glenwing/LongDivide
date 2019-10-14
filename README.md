# LongDivide

Created in October 2019. Latest version v1.1.0 (Oct. 14, 2019)

# Basic Description

Performs the mathematical division operation. Can detect and display repeating decimal patterns. Formats output to a specified number of decimal places, and can implement other optional formatting, such as adding commas, forcing signs, and more.

A test environment can be found [here](https://glenwing.github.io/LongDivide/LDTestPage.html).

# Dependencies

Requires [Decimal.js](https://github.com/MikeMcl/decimal.js/)

# Motivation

The original goal was to create a division function that could recognize repeating decimals and display them with an overline. This was relatively simple to accomplish.

`LongDivide(256, 135)` --> 1.8~~962~~ `1.8<span style="text-decoration:overline;">962</span>`

(Note: strikethrough is used throughout this readme to represent an overline, since Github markdown does not allow CSS).

However, I quickly realized that it is also important to be able to control output formatting, such as specifying the number of decimal places. Since the output of this function must be a string in order to include HTML tags for the overbar, all the standard formatting options (such as .toFixed) won't work. As a result, I decided to implemented a formatting system into the division function as well, which gives control over digit grouping, precision, and many other things. Every symbol, sign, and notation (such as the syntax for overbar tags) can be customized to anything.

LongDivide is designed to be as exact as possible, and will handle large numbers and arbitrary precision without loss. If an exact result is not possible (for example, if the user-set limit on precision is not enough), then the output will be displayed with an approximation sign (`≈`). An overbar will be displayed only when there is a mathematically infinite repeating pattern.

# Usage

`Output = LongDivide(A, B)`

This performs A / B. Output is a string. The result can have up to 8 decimal places. Longer results will be rounded.

In order to gain more control over precision and other aspects, use a third optional argument:

`Output = LongDivide(A, B, precision)` or `LongDivide(A, B, format)` or `LongDivide(A, B, options)`

Where
* `precision` is an integer which simply sets a fixed number of decimal places
* `format` is a string which can set precision as well as other common formatting aspects in a quick, compact, and easily readable way
* `options` is a dictionary which offers the deepest level of formatting control, but is the most verbose.

For example:

<b>Using format string:</b>

    X = LongDivide(2560, 1.35, '0.000') // Performs 2560/1.35, formatted to 3 decimals of precision
    Y = LongDivide(2560, 1.35, ',0.00') // Performs 2560/1.35, formatted to 2 decimals, with a comma for digit grouping

X: 1896.<span style="text-decoration:overline">~~296~~</span> `'1896.<span style="text-decoration:overline;">296</span>'`<br>
Y: ≈1,896.30 `&approx;1,896.30`

<b>Using options dictionary:</b>

    X = LongDivide(2560, 1.35, {'p': 3} ) // Performs 2560/1.35, formatted to 3 decimals of precision
    Y = LongDivide(2560, 1.35, {'p': 2, 'thousands': ','} ) // Performs 2560/1.35, formatted to 2 decimals, with a comma for digit grouping

X: 1896.<span style="text-decoration:overline">~~296~~</span> `'1896.<span style="text-decoration:overline;">296</span>'`<br>
Y: ≈1,896.30 `'&approx;1,896.30'`

# Format

Here is the syntax for the format string:

`Output = LongDivide(A, B, 'Format')`

<table>
  <tr>
    <th align="center">Control</th>
    <th align="center">A&nbsp;/&nbsp;B</th>
    <th align="center">Format</th>
    <th align="center">Output</th>
    <th align="center">Comment</th>
  </tr>
  <tr>
    <td align="center">Precision</td>
    <td align="center">1&nbsp;/&nbsp;8</td>
    <td>'0.0000'<br>'0.000'<br>'0.00'<br></td>
    <td>0.1250<br>0.125<br>≈0.13</td>
    <td></td>
  </tr>
  <tr>
    <td align="center">Optional Precision</td>
    <td align="center">1&nbsp;/&nbsp;800<br>1&nbsp;/&nbsp;8<br>1&nbsp;/&nbsp;2</td>
    <td align="center">'0.00[00]'</td>
    <td align="center">≈0.0013<br>0.125<br>0.50</td>
    <td>Example Format is equivalent to {'p_max':4, 'p_min':2}</td>
  </tr>
  <tr>
    <td align="center">Leading Zeros</td>
    <td align="center">1&nbsp;/&nbsp;2</td>
    <td align="right">'00.0'<br>'0.0'<br>'.0'</td>
    <td align="right">00.5<br>0.5<br>.5</td>
    <td></td>
  </tr>
  <tr>
    <td align="center">Thousands Grouping</td>
    <td align="center">1000&nbsp;/&nbsp;1</td>
    <td align="center">',0.0'</td>
    <td align="center">1,000</td>
    <td>Adds commas for digit grouping in front of the decimal</td>
  </tr>
  <tr>
    <td align="center">Use Real Minus Sign</td>
    <td align="center">1&nbsp;/&nbsp;-8</td>
    <td align="right">'0.000'<br>'-0.000'</td>
    <td align="right">-0.125<br>−0.125</td>
    <td>Adding "-" to the format enables &amp;minus; for negative numbers (instead of hyphen-minus)</td>
  </tr>
  <tr>
    <td align="center">Force Plus Sign</td>
    <td align="center">1&nbsp;/&nbsp;8</td>
    <td align="right">'+0.000'</td>
    <td align="right">+0.125</td>
    <td>Forces + sign on positive numbers</td>
  </tr>
  <tr>
    <td align="center">Parentheses for Negative</td>
    <td align="center">1&nbsp;/&nbsp;-8</td>
    <td align="right">'(0.000)'</td>
    <td align="right">(0.125)</td>
    <td>Encloses negative values in parentheses</td>
  </tr>
  <tr>
    <td align="center">Don't Show Approx</td>
    <td align="center">1&nbsp;/&nbsp;8</td>
    <td align="center">'!0.00'</td>
    <td align="center">0.13</td>
    <td>Adding exclamation mark removes '≈' symbol from non-exact results</td>
  </tr>
  <tr>
    <td align="center">SI Prefixes</td>
    <td align="center">1&nbsp;/&nbsp;8</td>
    <td align="center">'0.00 si'</td>
    <td align="center">125 m</td>
    <td>Adds SI prefixes to output</td>
  </tr>
</table>

# Options

If using an options dictionary, here are the valid entries:

### output = LongDivide(A, B, {

### 'p': val, // integer

Fixes the number of decimal places to `val`. Same effect as calling .toFixed(val) on a number. Shortcut for setting both `p_max` and `p_min` to `val`. This option overrides `p_max` and `p_min` if those options are also set. Undefined by default (p_max and p_min are used to specify precision defaults)

Examples:
* `LongDivide(1, 32, {'p': 8})`: 0.03125000
* `LongDivide(1, 32, {'p': 5})`: 0.03125
* `LongDivide(1, 32, {'p': 2})`: ≈0.03

For repeating patterns, the pattern will be cycled or rounded to obtain the requested number of places:
* `LongDivide(256, 135)`: 1.8<span style="text-decoration:overline">~~962~~</span> (natural output)
* `LongDivide(256, 135, {'p': 2})`: ≈1.90 (output is rounded to meet specified precision)
* `LongDivide(256, 135, {'p': 5})`: 1.89<span style="text-decoration:overline">~~629~~</span> (pattern is cycled to meet specified precision)


### 'p_max': val, // integer; default 8

Sets the maximum number of decimal places to `val`, but fewer decimal places will be used if more aren't needed.

Examples:
* `LongDivide(1, 32, {'p_max': 8})`: 0.03125 (up to 8 decimal places are allowed, but only 5 are needed)
* `LongDivide(1, 32, {'p_max': 5})`: 0.03125
* `LongDivide(1, 32, {'p_max': 2})`: ≈0.13 (only up to 2 decimal places are allowed, output is rounded and marked as approximate)

### 'p_min': val, // integer; default 0

Sets the minimum number of decimal places to `val`. Trailing zeros will be added if necessary.

Examples:
* `LongDivide(1, 32, {'p_min': 8})`: 0.03125000 (Trailing zeros are added to meet the minimum 8 decimal places specified)
* `LongDivide(1, 32, {'p_min': 5})`: 0.03125
* `LongDivide(1, 32, {'p_min': 2})`: 0.03125 (Minimum of 2 decimal places are required, but output can use more if needed)

### 'leading': val, // integer; default 1

Sets the minimum number of leading digits to `val` (digits in front of the decimal point). Leading zeros are added if necessary.

Set this value to 0 if you don't want a leading 0 in front of decimals. (i.e. if you want .5 instead of 0.5).

Examples:
* `LongDivide(1, 2, {'leading': 2})`: 00.5
* `LongDivide(1, 2, {'leading': 1})`: 0.5
* `LongDivide(1, 2, {'leading': 0})`: .5

### 'thousands': str, // string; default '' (blank)

Uses `str` as the character for digit grouping in front of the decimal point. If set to a blank string (which is the default value), no digit grouping will occur there.

Examples:
* `LongDivide(4096, 4, {'thousands': ','})`: 1,024
* `LongDivide(4096, 4, {'thousands': '' })`: 1024

### 'thousandths': str, // string; default '' (blank)

Uses `str` as the character for digit grouping behind the decimal point. If set to a blank string (which is the default value), no digit grouping will occur there.

Examples:
* `LongDivide(66, 64, {'thousandths': '\u202f'})`: 1.031&#x202f;25
    * ('\u202f' is a non-breaking thin space)

### 'orphans': x, // boolean; default false

This takes effect when thousandths separators are enabled, and the number of decimal digits is one more than a multiple of 3. In this situation, it is often preferable to have a group of 4 digits at the end rather than an "orphan" digit. The 'orphans' option controls this behavior. If set to true, an orphan digit will be allowed.

Examples:
* `LongDivide(0.1234567, 1, {'thousandths': '\u202f', 'orphans:' false})`: 0.123&#x202f;4567
* `LongDivide(0.1234567, 1, {'thousandths': '\u202f', 'orphans:' true})`: 0.123&#x202f;456&#x202f;7

### 'decimal': str, // string; default '.'

Uses `str` as the character for the decimal point.

Examples:
* `LongDivide(1073741824, 1000, {'decimal': ',', 'thousands': '.'})`: 1.073.724,824

### 'minus': str, // string; default '\u2212' (&amp;minus;)

`str` is placed in front of all negative numbers. By default, it is set to the Minus Sign character (U+2212) rather than the normal "hyphen-minus" character. Set this option to a hyphen-minus if you want that instead.

<b>Special case:</b> If `str = '()'`, then negative numbers will be enclosed in parentheses.

Examples:
* `LongDivide(-1, 8)`: &minus;0.125
* `LongDivide(-1, 8, {'minus': '-' }) `: -0.125
* `LongDivide(-1, 8, {'minus': '()' })`: (0.125)

### 'plus': str, // string; default '' (blank)

`str` is placed in front of all positive numbers. By default it is a blank string. Set this option to '+' to force sign on positive numbers.

Examples:
* `LongDivide(1, 8)`: 0.125
* `LongDivide(1, 8, {'plus': '+' }) `: +0.125

### 'approx': str, // string; default '\u2248' (&amp;approx; / &approx;)

`str` is placed in front of non-exact results. Set to blank string '' if no indication is desired for non-exact results.

Examples:
* `LongDivide(1, 8, {'p': 2})`: ≈0.13
* `LongDivide(1, 8, {'p': 2, 'approx': '~'})`: ~0.13
* `LongDivide(1, 8, {'p': 2, 'approx': '' })`: 0.13

### 'currency': str, // string; defalut '' (blank)

`str` is placed between the sign and the number. Set to blank string '' if no symbol is desired at that position.
Examples:
* `LongDivide(-100, 8, {'p':2, 'currency': '$'})`: &minus;$12.50
* `LongDivide(-100, 8, {'p':2, 'currency': 'US$'})`: &minus;US$12.50

### 'OL_open': str, // string; default HTML/CSS overline

`str` is placed before the repeating portion of a repeating decimal.

By default, it is set to the HTML/CSS overline tag: `'<span style="text-decoration:overline">'`

It can be changed to other tags depending on what environment the output is intended for; for example to `'\{\{overline|'` (Wikipedia markup) or simply `'('` for ASCII-only environments where repeating decimals are represented with parentheses.

### 'OL_close': str, // string; default HTML/CSS overline

`str` is placed after the repeating portion of a repeating decimal.

By default, it is set to the HTML/CSS tag: `'</span>'`

See OL_open description.

### 'si': x, // boolean; default false

If set to true, output will use SI prefixes.

Examples:
* `LongDivide(2000, 1, {'si': true})`: 2&nbsp;k
* `LongDivide(2000000, 1, {'si': true}) + 'Hz'`: 2&nbsp;MHz

### '2_singles': x, // boolean; default true

If set to true, single-digit repeating patterns will be doubled.

Examples:
* `LongDivide(4, 3)`: 1.<span style='text-decoration:overline'>~~33~~</span>
* `LongDivide(4, 3, {'2_singles': false})`: 1.<span style='text-decoration:overline'>~~3~~</span>

### 'repeat': x, // boolean; default true

If set to false, repeating decimal detection/output will be disabled.

Examples:

* `LongDivide(16, 9)`: 1.<span style='text-decoration:overline'>~~77~~</span>
* `LongDivide(16, 9, {'repeat': false, 'p':4})`: 1.7778

# Versions

### 1.1.0 (Oct. 14, 2019)

* When using thousandths separators, conventionally it is preferrable to have a trailing group of 4 rather than having an "orphan" digit at the end (i.e. 0.123&#x202f;4567 rather than 0.123&#x202f;456&#x202f;7). To accommodate this, an 'orphans' option has been added, which will configure this behavior.

### 1.0.1 (Oct. 12, 2019)

* Added full support for large numbers without truncation or loss of precision. (Limit of 1000 decimal places for various internal processes, set in the Decimal.js config at the very beginning of LongDivide.js. Can be changed to larger if desired.)

### 1.0.0 (Oct. 12, 2019)

Initial version

# License

The following text is included here as required by the MIT license of LongDivide.js:

LongDivide.js is freely distributable under the terms of the MIT license.

Copyright &copy; 2019 Glenwing (https://github.com/Glenwing)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.