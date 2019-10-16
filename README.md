# LongDivide.js

Created in October 2019. Latest version: 1.2.1 (Oct. 16, 2019)

## Basic Description

Performs the mathematical division operation. Can detect and display repeating decimal patterns. Formats output to a specified number of decimal places, and can implement other optional formatting, such as adding commas, forcing signs, and more.

A test environment can be found [here](https://glenwing.github.io/LongDivide/LDTestPage.html).

## Dependencies

Requires [decimal.js](https://github.com/MikeMcl/decimal.js/)

## Install

To include LongDivide.js in your project:

You can install it with npm: ([package](https://www.npmjs.com/package/longdivide.js))
* Install node.js and npm (https://www.npmjs.com/get-npm)
* Open a command line window in your project's root directory (in Windows, open your project's top level folder in Windows Explorer, hold down shift, and right click a blank spot in the folder, then click "Open PowerShell window here")
* In the command line, enter `npm install longdivide.js`

Or, if you are a noob like me and don't want to mess with any of that, just copy the source (LongDivide.js) into your project directory, and include it in your page.
`<script src="LongDivide.js"></script>`

Keep in mind you will also need to include [decimal.js](https://github.com/MikeMcl/decimal.js/).

## Motivation

The original goal was to create a division function that could recognize repeating decimals and display them with an overline. This was relatively simple to accomplish.

`LongDivide(256, 135)` --> 1.8~~962~~ `1.8<span style="text-decoration:overline;">962</span>`

(Note: strikethrough is used throughout this README to represent an overline, since Github markdown does not provide any way to overline or even underline text).

However, I quickly realized that it is also important to be able to control output formatting, such as specifying the number of decimal places. Since the output of this function must be a string in order to include HTML tags for the overbar, all the standard formatting options (such as .toFixed) won't work. As a result, I decided to implemented a formatting system into the division function as well, which gives control over digit grouping, precision, and many other things. Every symbol, sign, and notation (such as the syntax for overbar tags) can be customized to anything.

LongDivide is designed to be as exact as possible, and will handle large numbers and arbitrary precision without loss. If an exact result is not possible (for example, if the user-set limit on precision is too restrictive), then the output will be displayed with an approximation sign (`≈`). An overbar will be displayed only when there is a mathematically infinite repeating pattern.

## Usage

### Output = LongDivide(A, B)

This performs A / B. Output is a string, inputs are strings or numbers. Up to 8 decimal places will be displayed by default, longer results will be rounded.

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

It requires much more typing to accomplish the same thing in this example, but the options dictionary also has many additional customization options that are not possible with just the format string.

## Precision Argument

### Output = LongDivide(A, B, Precision)

Precision (integer) forces the output to a certain number of decimal places, like calling `Number(val).toFixed(Precision)` on a number.

Examples:

| Input                  | Output     | Comment                              |
|------------------------|------------|--------------------------------------|
| `LongDivide(1, 32, 8)` | 0.03125000 | Forces 8 decimal places              |
| `LongDivide(1, 32, 5)` | 0.03125    | Forces 5 decimals                    |
| `LongDivide(1, 32, 2)` | ≈0.03      | Forces 2 decimals, result is rounded |

For repeating patterns, the pattern will be cycled or rounded to obtain the requested number of places:

| A, B, options             | Output      | Comment                                       |
|---------------------------|-------------|-----------------------------------------------|
| `LongDivide(256, 135)`    | 1.8~~962~~  | Natural output when no options are specified  |
| `LongDivide(256, 135, 2)` | ≈1.90       | Output is rounded to meet specified precision |
| `LongDivide(256, 135, 5)` | 1.89~~629~~ | Pattern is cycled to meet specified precision |

## Format

The **Format string** can be used as the third argument to easily specify some common formatting options. The syntax is as follows:

### Output = LongDivide(A, B, Format)

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
    <td>Fixed precision</td>
  </tr>
  <tr>
    <td align="center">Optional&nbsp;Precision</td>
    <td align="center">1&nbsp;/&nbsp;800<br>1&nbsp;/&nbsp;8<br>1&nbsp;/&nbsp;2</td>
    <td align="center">'0.00[00]'</td>
    <td align="center">≈0.0013<br>0.125<br>0.50</td>
    <td>Use brackets for optional precision.<br>This example is equivalent to<br>options = {'p_max':4, 'p_min':2}</td>
  </tr>
  <tr>
    <td align="center">Leading&nbsp;Zeros</td>
    <td align="center">1&nbsp;/&nbsp;2</td>
    <td align="right">'00.0'<br>'0.0'<br>'.0'</td>
    <td align="right">00.5<br>0.5<br>.5</td>
    <td></td>
  </tr>
  <tr>
    <td align="center">Thousands&nbsp;Grouping</td>
    <td align="center">1000&nbsp;/&nbsp;1</td>
    <td align="center">',0.0'</td>
    <td align="center">1,000</td>
    <td>Adds commas for digit grouping in front of the decimal</td>
  </tr>
  <tr>
    <td align="center">Use&nbsp;Real&nbsp;Minus&nbsp;Sign</td>
    <td align="center">1&nbsp;/&nbsp;-8</td>
    <td align="right">'0.000'<br>'-0.000'</td>
    <td align="right">-0.125<br>−0.125</td>
    <td>Adding "-" to the format enables &amp;minus; for negative numbers (instead of hyphen-minus)</td>
  </tr>
  <tr>
    <td align="center">Force&nbsp;Plus&nbsp;Sign</td>
    <td align="center">1&nbsp;/&nbsp;8</td>
    <td align="right">'+0.000'</td>
    <td align="right">+0.125</td>
    <td>Forces + sign on positive numbers</td>
  </tr>
  <tr>
    <td align="center">Parentheses&nbsp;for&nbsp;Negative</td>
    <td align="center">1&nbsp;/&nbsp;-8</td>
    <td align="right">'(0.000)'</td>
    <td align="right">(0.125)</td>
    <td>Encloses negative values in parentheses</td>
  </tr>
  <tr>
    <td align="center">Don't&nbsp;Show&nbsp;Approx</td>
    <td align="center">1&nbsp;/&nbsp;8</td>
    <td align="center">'!0.00'</td>
    <td align="center">0.13</td>
    <td>Adding exclamation mark removes '≈' symbol from non-exact results</td>
  </tr>
  <tr>
    <td align="center">Exponential&nbsp;Form</td>
    <td align="center">1000&nbsp;/&nbsp;1</td>
    <td align="center">'0.00 e'<br>'0.00 ex'<br>'0.00 ed'</td>
    <td align="center">1.00e+3<br>1.00&nbsp;&times;&nbsp;10<sup>3</sup><br>1.00&nbsp;&#x2219;&nbsp;10<sup>3</sup></td>
    <td>Outputs in exponential form</td>
  </tr>
  <tr>
    <td align="center">SI&nbsp;Prefixes</td>
    <td align="center">1&nbsp;/&nbsp;8</td>
    <td align="center">'0.00 si'</td>
    <td align="center">125&nbsp;m</td>
    <td>Adds SI prefixes to output</td>
  </tr>
</table>

## Options

An **options dictionary** can be used as the third argument to customize all formatting aspects in great detail. The valid options are as follows:

### output = LongDivide(A, B, options)

### options = {

### 'p': val, // integer

Fixes the number of decimal places to `val`. Same effect as calling .toFixed(val) on a number. Shortcut for setting both `p_max` and `p_min` to `val`. This option overrides `p_max` and `p_min` if those options are also set. Undefined by default (p_max and p_min are used to specify precision defaults)

Examples:

| A, B, options     | Output     | Comment                              |
|-------------------|------------|--------------------------------------|
| `1, 32, {'p': 8}` | 0.03125000 | Forces 8 decimal places              |
| `1, 32, {'p': 5}` | 0.03125    | Forces 5 decimals                    |
| `1, 32, {'p': 2}` | ≈0.03      | Forces 2 decimals, result is rounded |

For repeating patterns, the pattern will be cycled or rounded to obtain the requested number of places:

| A, B, options        | Output      | Comment                                       |
|----------------------|-------------|-----------------------------------------------|
| `256, 135`           | 1.8~~962~~  | Natural output when no options are specified  |
| `256, 135, {'p': 2}` | ≈1.90       | Output is rounded to meet specified precision |
| `256, 135, {'p': 5}` | 1.89~~629~~ | Pattern is cycled to meet specified precision |


### 'p_max': val, // integer; default 8

Sets the maximum number of decimal places to `val`, but fewer decimal places will be used if more aren't needed.

Examples:

| A, B, options         | Output  | Comment                                                                |
|-----------------------|---------|------------------------------------------------------------------------|
| `1, 32, {'p_max': 8}` | 0.03125 | Up to 8 decimals are allowed, but only 5 are needed                    |
| `1, 32, {'p_max': 5}` | 0.03125 |                                                                        |
| `1, 32, {'p_max': 2}` | ≈0.03   | Only 2 decimals are allowed, output is rounded and marked as non-exact |

### 'p_min': val, // integer; default 0

Sets the minimum number of decimal places to `val`. Trailing zeros will be added if necessary.

Examples:

| A, B, options         | Output     | Comment                                                                 |
|-----------------------|------------|-------------------------------------------------------------------------|
| `1, 32, {'p_min': 8}` | 0.03125000 | Trailing zeros are added to meet the minimum 8 decimal places specified |
| `1, 32, {'p_min': 5}` | 0.03125    |                                                                         |
| `1, 32, {'p_min': 2}` | 0.03125    | Minimum of 2 decimals is required, but result can have more if needed   |

### 'leading': val, // integer; default 1

Sets the minimum number of leading digits to `val` (digits in front of the decimal point). Leading zeros are added if necessary.

Set this value to 0 if you don't want a leading 0 in front of decimals.

Examples:

| A, B, options          | Output | Comment                                                                    |
|------------------------|-------:|----------------------------------------------------------------------------|
| `1, 2, {'leading': 0}` |     .5 | Use leading:0 if you don't want leading zeros in front of decimals         |
| `1, 2, {'leading': 1}` |    0.5 | Use leading:1 (default) if you do want a leading zero in front of decimals |
| `1, 2, {'leading': 2}` |   00.5 | Use higher leading if you want to force a minimum number of integer digits |

### 'thousands': str, // string; default '' (blank)

Uses `str` as the character for digit grouping in front of the decimal point. If set to a blank string (which is the default value), no digit grouping will occur there. If spaces are used as thousands separators, it is recommended to use either U+202F (non-breaking thin space) or U+00A0 (non-breaking space / &amp;nbsp;). Standard spaces or standard thin spaces (&amp;thinsp;) will break across lines.

Examples:

| A, B, options                                   | Output   | Comment                                   |
|-------------------------------------------------|---------:|-------------------------------------------|
| `2460.1, 2`                                     | 1230.05  | Default output                            |
| `2460.1, 2, {'thousands': ','}`                 | 1,230.05 | Using commas for thousands digit grouping |
| `2460.1, 2, {'thousands': '.', 'decimal': ','}` | 1.230,05 | Euro style                                |

### 'thousandths': str, // string; default '' (blank)

Uses `str` as the character for digit grouping behind the decimal point. If set to a blank string (which is the default value), no digit grouping will occur there. Recommend using either U+202F (non-breaking thin space) or U+00A0 (non-breaking space / &amp;nbsp;). Standard spaces or standard thin spaces (&amp;thinsp;) will break across lines.

Examples:

| A, B, options                         | Output                     | Comment                             |
|---------------------------------------|----------------------------|-------------------------------------|
| `257, 256`                            | 1.00390625                 | Default output                      |
| `257, 256, {'thousandths': '\u202f'}` | 1.003&#x00A0;906&#x00A0;25 | U+202F is a non-breaking thin space |

### 'orphans': x, // boolean; default false

This takes effect when thousandths separators are enabled, and the number of decimal digits is 1 more than a multiple of 3. In this situation, it is often preferable to have a group of 4 digits at the end rather than a lone "orphan" digit. The 'orphans' option controls this behavior. If set to `true`, an orphan digit will be allowed.

Examples:

| A, B, options                                          | Output                    | Comment                                                    |
|--------------------------------------------------------|---------------------------|------------------------------------------------------------|
| `129, 128, {'thousandths': '\u202f'}`                  | 1.007&#x00A0;8125         | Default output; last digit is merged into preceeding group |
| `129, 128, {'thousandths': '\u202f', 'orphans': true}` | 1.007&#x00A0;812&#x00A0;5 | Last digit is orphaned                                     |

### 'decimal': str, // string; default '.'

Uses `str` as the character for the decimal point.

Examples:

| A, B, options                                          | Output        | Comment |
|--------------------------------------------------------|---------------|---------|
| `1073741824, 1000, {'thousands': '.', 'decimal': ','}` | 1.073.741,824 |         |

### 'minus': str, // string; default '\u2212' (&amp;minus;)

`str` is placed in front of all negative numbers. By default, it is set to the Minus Sign character (U+2212) rather than the normal "hyphen-minus" character. Set this option to a hyphen-minus if you want that instead.

**Special case:** If `str = '()'`, then negative numbers will be enclosed in parentheses.

Examples:

| A, B, options             | Output       | Comment                                                    |
|---------------------------|-------------:|------------------------------------------------------------|
| `-1, 8`                   | &minus;0.125 | Default output, uses `&minus;` sign (U+2212)               |
| `-1, 8, {'minus': '-' }`  | -0.125       | Sets the standard hyphen-minus character as the minus sign |
| `-1, 8, {'minus': '()' }` | (0.125)      | Uses parentheses to indicate negative numbers              |

### 'plus': str, // string; default '' (blank)

`str` is placed in front of all positive numbers. By default it is a blank string. Set this option to '+' to force sign on positive numbers.

Examples:

| A, B, options         | Output | Comment                         |
|-----------------------|-------:|---------------------------------|
| `1, 8`                | 0.125  | Default output (`'plus': ''`)   |
| `1, 8, {'plus': '+'}` | +0.125 | Forces sign on positive numbers |

### 'approx': str, // string; default '\u2248' (&amp;approx; / &approx;)

`str` is placed in front of non-exact results. Set to blank string '' if no indication is desired for non-exact results.

Examples:

| A, B, options                   | Output | Comment                                                            |
|---------------------------------|-------:|--------------------------------------------------------------------|
| `1, 8, {'p': 2}`                | ≈0.13  | Default output (`'approx': '\u2248'`)                              |
| `1, 8, {'p': 2, 'approx': '~'}` | ~0.13  | Use tilde instead, to improve compatibility with limited char sets |
| `1, 8, {'p': 2, 'approx': ''}`  | 0.13   | Disables indication of non-exact results                           |

### 'currency': str, // string; defalut '' (blank)

`str` is placed between the sign and the number. Set to blank string '' if no symbol is desired at that position.
Examples:

| A, B, options                       | Output        | Comment |
|-------------------------------------|---------------|---------|
| `-100, 8, {'p':2, 'currency': '$'}` | &minus;$12.50 |         |

### 'OL_open': str, // string; default HTML/CSS overline

`str` is placed before the repeating portion of a repeating decimal.

By default, it is set to the HTML/CSS overline tag: `'<span style="text-decoration:overline">'`

It can be changed to other tags depending on what environment the output is intended for; for example to `'{{overline|'` (Wikipedia markup) or simply `'('` for ASCII-only environments where repeating decimals are represented with parentheses.

### 'OL_close': str, // string; default HTML/CSS overline

`str` is placed after the repeating portion of a repeating decimal.

By default, it is set to the HTML/CSS tag: `'</span>'`

See OL_open description.

### 'exp': str, // string; default '' (blank)

Enables exponential notation if set to any of the following:
* `'exp': e` uses standard 'e' notation
* `'exp': ex` uses scientific notation with a multiplication sign
* `'exp': ed` same as ex, but using a multiplication dot instead
* `'exp': custom` allows the user to specify four parameters to make their own exponential formatting:
  * `'exp_open': str,` // string to be placed before the exponential power (such as e, E, superscript opening tag, etc.)
  * `'exp_close': str,` // string to be placed after the exponential power (such as &lt;/sup&gt; tag)
  * `'exp_minus': str,` // string to be placed before the exponential power if it is negative; defaults to the same as the general 'minus' option if left undefined
  * `'exp_plus': str,` // string to be placed before the exponential power if it is positive; defaults to the same as the general 'plus' option if left undefined

| A, B, options                                                                     | Output                       | Comment                                      |
|-----------------------------------------------------------------------------------|------------------------------|----------------------------------------------|
| `1000, 1, {'exp': 'e', 'p': 2}`                                                   | 1.00e+3                      | Standard exponential notation                |
| `1000, 1, {'exp': 'ex', 'p': 2}`                                                  | 1.00 &times; 10<sup>3</sup>  | Scientific notation with multiplication sign |
| `1000, 1, {'exp': 'ed', 'p': 2}`                                                  | 1.00 &#x2219; 10<sup>3</sup> | Scientific notation with multiplication dot  |
| `1000, 1, {'exp': 'custom',`<br>&nbsp;&nbsp;&nbsp;&nbsp;`'exp_open': ' E<sup>'`,<br>&nbsp;&nbsp;&nbsp;&nbsp;`'exp_close': '</sup>'`,<br>`}` | 1.00 E<sup>3</sup>           | Custom format                                | 
| `1000, 1, {'exp': 'custom',`<br>&nbsp;&nbsp;&nbsp;&nbsp;`'exp_open': 'x10^'`,<br>&nbsp;&nbsp;&nbsp;&nbsp;`'exp_close': ''`,<br>&nbsp;&nbsp;&nbsp;&nbsp;`'exp_plus': '+'`,<br>`}` | 1.00x10^+3           | Custom format                                | 

### 'si': x, // boolean; default false

If set to `true`, output will use SI prefixes.

Examples:

| A, B, options                                          | Output   | Comment                            |
|--------------------------------------------------------|----------|------------------------------------|
| `2000, 1, {'si': true}`                                | 2 k      |                                    |
| `LongDivide(2000, 0.001, {'si': true, 'p': 2}) + 'Hz'` | 2.00 MHz | Example attaching units at the end |

### '2_singles': x, // boolean; default true

If set to `true`, single-digit repeating patterns will be doubled (as long as it does not exceed `p_max`).

Examples:

| A, B, options                           | Output   | Comment                               |
|-----------------------------------------|----------|---------------------------------------|
| `4, 3`                                  | 1.~~33~~ | Default output (`'2_singles': true`)  |
| `4, 3, {'2_singles': false}`            | 1.~~3~~  | Disables single-doubling              |
| `4, 3, {'2_singles': true, 'p_max': 1}` | 1.~~3~~  | `2_singles` will not override `p_max` |

### 'repeat': x, // boolean; default true

If set to `false`, repeating decimal detection/output will be disabled.

Examples:

| A, B, options                     | Output   | Comment                              |
|-----------------------------------|----------|--------------------------------------|
| `16, 9`                           | 1.~~77~~ | Default output (`'repeat': true`)    |
| `16, 9, {'repeat': false, 'p':4}` | 1.7778   | Disables repeating decimal detection |

## Versions

### 1.2.1 (Oct. 16, 2019)

* Fixed LongDivide(A, B, Precision) syntax (previously was not working)

### 1.2.0 (Oct. 16, 2019)

* Added exponential form option
* Fixed incorrect handling when numbers are rounded such that the integer portion is increased (i.e. 5.99 with p_max = 1 should output 6.0, but instead would output 5.10)
* Fixed incorrect comparison in format parsing function (if typeof(...) === string instead of 'string')
* Fixed missing check for the case where no options are given

### 1.1.0 (Oct. 14, 2019)

* When using thousandths separators, conventionally it is preferrable to have a trailing group of 4 rather than having an "orphan" digit at the end (i.e. 0.123&#x202f;4567 rather than 0.123&#x202f;456&#x202f;7). To accommodate this, an 'orphans' option has been added, which will configure this behavior.

### 1.0.1 (Oct. 12, 2019)

* Added full support for large numbers without truncation or loss of precision. (Limit of 1000 decimal places for various internal processes, set in the Decimal.js config at the very beginning of LongDivide.js. Can be changed to larger if desired.)

### 1.0.0 (Oct. 12, 2019)

Initial version

## License

The following text is included here as required by the MIT license of LongDivide.js:

LongDivide.js is freely distributable under the terms of the MIT license.

Copyright &copy; 2019 Glenwing (https://github.com/Glenwing)

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.