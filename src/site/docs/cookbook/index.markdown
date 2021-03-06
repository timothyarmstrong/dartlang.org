---
layout: default
title: "Dart Cookbook"
description: "Recipes and prescriptions for using Dart."
has-permalinks: true
---

# Dart Cookbook

## Contents

1. [Strings](#strings)
    1. [Concatenating strings](#concatenating-strings)
    1. [Interpolating expressions inside strings](#interpolating-expressions-inside-strings)
    1. [Handling special characters within strings](#handling-special-characters-within-strings)
    1. [Incrementally building a string using a StringBuffer](#incrementally-building-a-string-using-a-stringbuffer)
    1. [Determining whether a string is empty](#determining-whether-a-string-is-empty)
    1. [Removing leading and trailing whitespace](#removing-leading-and-trailing-whitespace)
    1. [Changing string case](#changing-string-case)
    1. [Handling extended characters that are composed of multiple code units](#handling-extended-characters-that-are-composed-of-multiple-code-units)
    1. [Converting between characters and numerical codes](#converting-between-characters-and-numerical-codes)
    1. [Calculating the length of a string](#calculating-the-length-of-a-string)
    1. [Processing a string one character at a time](#processing-a-string-one-character-at-a-time)
    1. [Splitting a string into substrings](#splitting-a-string-into-substrings)
    1. [Determining whether a string contains another string](#determining-whether-a-string-contains-another-string)
    1. [Finding matches of a regular expression](#finding-matches-of-a-regular-expression)
    1. [Substituting strings using regular expressions](#substituting-strings-using-regular-expressions)
1. [Lists](#lists)
    1. [Creating a fixed length list](#creating-a-fixed-length-list)
    1. [Creating an immutable List](#creating-an-immutable-list)
    1. [Creating a list and initializing it with default values](#creating-a-list-and-initializing-it-with-default-values)
    1. [Copying a list](#copying-a-list)
    1. [Appending items to a list](#appending-items-to-a-list)
1. [Numbers](#numbers)
    1. [Converting a string to a number](#converting-a-string-to-a-number)
    1. [Converting a number to a string](#converting-a-number-to-a-string)
1. [Json](#json)
    1. [Encoding JSON](#encoding-json)
    1. [Decoding JSON](#decoding-json)
1. [URIs](#uris)
    1. [Encoding and Decoding fully qualified URIs](#encoding-and-decoding-fully-qualified-uris)
    1. [Parsing URIs](#parsing-uris)
    1. [Building URIs](#building-uris)
1. [Testing](#testing)
    1. [Running only a single test](#running-only-a-single-test)
    1. [Filtering which tests are run](#filtering-which-tests-are-run)
    1. [Running code before and after each test](#running-code-before-and-after-each-test)
    1. [Testing synchronous exceptions](#testing-synchronous-exceptions)
    1. [Testing for double equality](#testing-for-double-equality)
{:.toc}


## Strings

### Concatenating strings

#### Problem

You want to concatenate strings in Dart. 

#### Solution

Use the `+` operator:

{% prettify dart %}
var fact = 'Dart' + 'is' + ' fun!'; // 'Dart is fun!'
{% endprettify %}

You can use the `+` operator to concatenate single-line and multi-line
strings:

{% prettify dart %}
var funnyGuys = 'Dewey ' + 'Cheatem' + 
''' and
Howe'''; // 'Dewey Cheatem and\n Howe'
{% endprettify %}

#### Discussion

You can also use adjacent string literals:

{% prettify dart %}
var fact = 'Dart'  'is'  ' fun!'; // 'Dart is fun!'
{% endprettify %}

Adjacent literals work over multiple lines:

{% prettify dart %}
var fact = 'Dart'
'is'
'fun!'; // 'Dart is fun!'
{% endprettify %}


If the string fragments you wish to concatenate are already in a list, use
`join()`:

{% prettify dart %}
var film = ['The', 'Big', 'Lebowski']).join(' '); // 'The Big Lebowski'
{% endprettify %}

If you want to incrementally build a longish string from its parts, a
StringBuffer works more efficiently than string concatenation (see
_Incrementally building a string efficiently using a StringBuffer_, below).

You can also use string interpolation (see _Interpolating expressions inside
strings_, below).


### Interpolating expressions inside strings

#### Problem

You want to embed Dart code inside strings.

#### Solution

You can put the value of an expression inside a string by using
$\{expression}.

{% prettify dart %}
var favFood = 'sushi';
var whatDoILove = 'I love ${favFood.toUpperCase()}'; // 'I love SUSHI'
{% endprettify %}

You can skip the \{} if the expression is an identifier:

{% prettify dart %}
var whatDoILove = 'I love $favFood'; // 'I love sushi'
{% endprettify %}

#### Discussion

The interpolated string, `'string ${expression}` is equivalent to the
concatenation of the strings `string` and `expression.toString()`.
Consider this code:

{% prettify dart %}
var four = 4;
var seasons = 'The $four seasons'; // 'The 4 seasons'
{% endprettify %}

This is functionally equivalent to the following:

{% prettify dart %}
var seasons = 'The ' + 4.toString() + ' seasons';
// 'The 4 seasons'
{% endprettify %}

You should consider implementing a `toString()` method for classes that you
define. Here's what happens if you don't:

{% prettify dart %}
class Point {
  num x, y;
  Point(this.x, this.y);
}

var point = new Point(3, 4);
print('Point: $point'); // "Point: Instance of 'Point'"
{% endprettify %}

Probably not what you wanted. Here is the same example with an explicit
`toString()`:

{% prettify dart %}
class Point {
  ...
    
  String toString() => 'x: $x, y: $y';
}

print('Point: $point'); // 'Point: x: 3, y: 4'
{% endprettify %}

### Handling special characters within strings

#### Problem

You want to put newlines, dollar signs, or other special characters in strings.

#### Solution

Prefix special characters with a `\`.

{% prettify dart %}
  print('Wile\nCoyote'); 
  // Wile
  // Coyote
{% endprettify %}

#### Discussion

Dart designates a few characters as special, and these can be escaped:

* \n for newline, equivalent to \x0A.
* \r for carriage return, equivalent to \x0D.
* \f for form feed, equivalent to \x0C.
* \b for backspace, equivalent to \x08.
* \t for tab, equivalent to \x09.
* \v for vertical tab, equivalent to \x0B.

If you prefer, you can use `\x` or `\u` notation to indicate the special
character:

{% prettify dart %}
print('Wile\x0ACoyote');   // Same as print('Wile\nCoyote')
print('Wile\u000ACoyote'); // Same as print('Wile\nCoyote') 
{% endprettify %}

You can also use `\u{}` notation:

{% prettify dart %}
print('Wile\u{000A}Coyote'); // same as print('Wile\nCoyote')
{% endprettify %}

You can also escape the `$` used in string interpolation:

{% prettify dart %}
var superGenius = 'Wile Coyote';
print('$superGenius and Road Runner');  // 'Wile Coyote and Road Runner'
print('\$superGenius and Road Runner'); // '$superGenius and Road Runner'
{% endprettify %}

If you escape a non-special character, the `\` is ignored:

{% prettify dart %}
print('Wile \E Coyote'); // 'Wile E Coyote'
{% endprettify %}


### Incrementally building a string using a StringBuffer

#### Problem

You want to collect string fragments and combine them in an efficient
manner.

#### Solution

Use a StringBuffer to programmatically generate a string. Consider this code
below for assembling a series of urls from fragments:

{% prettify dart %}
var data = [{'scheme': 'https', 'domain': 'news.ycombinator.com'}, 
            {'domain': 'www.google.com'}, 
            {'domain': 'reddit.com', 'path': 'search', 'params': 'q=dart'}
           ];

String assembleUrlsUsingStringBuffer(entries) {
  StringBuffer sb = new StringBuffer();
  for (final item in entries) {
    sb.write(item['scheme'] != null ? item['scheme']  : 'http');
    sb.write("://");
    sb.write(item['domain']);
    sb.write('/');
    sb.write(item['path'] != null ? item['path']  : '');
    if (item['params'] != null) {
      sb.write('?');
      sb.write(item['params']);
    }
    sb.write('\n');
  }
  return sb.toString();
}

// https://news.ycombinator.com/
// http://www.google.com/
// http://reddit.com/search?q=dart
{% endprettify %}

A StringBuffer collects string fragments, but does not generate a new string
until `toString()` is called. 

#### Discussion

Using a StringBuffer is vastly more efficient than concatenating fragments
at each step: Consider this rewrite of the above code:

{% prettify dart %}
String assembleUrlsUsingConcatenation(data) {
  var urls = '';
  for (final item in data) {
    urls += item['scheme'] != null ? item['scheme']  : 'http';
    urls += "://";
    urls += item['domain'];
    urls += '/';
    urls += item['path'] != null ? item['path']  : '';
    if (item['params'] != null) {
      urls += '?';
      urls += item['params'];
    }
    urls += '\n';
  }
  return urls;
}
{% endprettify %}

This approach produces the exact same result, but incurs the cost of
joining strings multiple times. 


##### Other StringBuffer methods

In addition to `write()`, the StringBuffer class provides methods to
write a list of strings (`writeAll()`), write a numerical character code
(`writeCharCode()`), write with an added newline (`writeln()`), and
more. The example below shows how to use these methods:

{% prettify dart %}
var sb = new StringBuffer();
sb.writeln('The Beatles:');
sb.writeAll(['John, ', 'Paul, ', 'George, and Ringo']);
sb.writeCharCode(33); // charCode for '!'.
var beatles = sb.toString(); // 'The Beatles:\nJohn, Paul, George, and Ringo!' 
{% endprettify %}


### Determining whether a string is empty

#### Problem

You want to know whether a string is empty. You tried `if (string) {...}`, but
that did not work.

#### Solution

Use `string.isEmpty`:

{% prettify dart %}
var emptyString = '';
print(emptyString.isEmpty); // true
{% endprettify %}

You can also just use `==`:

{% prettify dart %}
if (string == '') {...} // True if string is empty.
{% endprettify %}

A string with a space is not empty:

{% prettify dart %}
var space = ' ';
print(space.isEmpty); // false
{% endprettify %}

#### Discussion

Don't use `if (string)` to test the emptiness of a string. In Dart, all objects
except the boolean true evaluate to false, so `if(string)` is always false. You
will see a warning in the editor if you use an 'if' statement with a non-boolean
in checked mode.


### Removing leading and trailing whitespace

#### Problem

You want to remove spaces, tabs, and other whitespace from the beginning and
end of strings.

#### Solution

Use `string.trim()`:

{% prettify dart %}
var space = '\n\r\f\t\v';       // A variety of space characters.
var string = '$space X $space';
var newString = string.trim();  // 'X'
{% endprettify %}

The String class has no methods to remove only leading or only trailing
whitespace. You can always use a RegExp.

Remove only leading whitespace:

{% prettify dart %}
var newString = string.replaceFirst(new RegExp(r'^\s+'), ''); // 'X \n\r\f\t\v'
{% endprettify %}

Remove only trailing whitespace:

{% prettify dart %}
var newString = string.replaceFirst(new RegExp(r'\s+$'), ''); // '\n\r\f\t\v X'
{% endprettify %}


### Changing string case

#### Problem

You want to change the case of strings.

#### Solution

Use String's `toUpperCase()` and `toLowerCase()` methods: 

{% prettify dart %}
var theOneILove = 'I love Lucy';
theOneILove.toUpperCase();                           // 'I LOVE LUCY!'
theOneILove.toLowerCase();                           // 'i love lucy!'

// Zeus in modern Greek.
var zeus = '\u0394\u03af\u03b1\u03c2';               // 'Δίας'
zeus.toUpperCase();                                  // 'ΔΊΑΣ'

var resume = '\u0052\u00e9\u0073\u0075\u006d\u00e9'; // 'Résumé'
resume.toLowerCase();                                // 'résumé'
{% endprettify %}

The `toUpperCase()` and `toLowerCase()` methods don't affect the characters of
scripts such as Devanagri that don't have distinct letter cases.

{% prettify dart %}
var chickenKebab = '\u091a\u093f\u0915\u0928 \u0915\u092c\u093e\u092c'; 
// 'चिकन कबाब'  (in Devanagari)
chickenKebab.toLowerCase();  // 'चिकन कबाब'
chickenKebab.toUpperCase();  // 'चिकन कबाब'
{% endprettify %}

If a character's case does not change when using `toUpperCase()` and
`toLowerCase()`, it is most likely because the character only has one
form.


### Handling extended characters that are composed of multiple code units

#### Problem

You want to use emoticons and other special symbols that don't fit into 16
bits. How can you create such strings and use them correctly in your code? 

#### Solution

You can create an extended character using `'\u{}'` syntax:

{% prettify dart %}
var clef = '\u{1D11E}'; // 𝄞
{% endprettify %}

#### Discussion

Most UTF-16 strings are stored as two-byte (16 bit) code sequences.
Since two bytes can only contain the 65,536 characters in the 0x0 to 0xFFFF
range, a pair of strings is used to store values in the 0x10000 to 0x10FFFF
range. These strings only have semantic meaning as a pair. Individually, they
are invalid UTF-16 strings. The term 'surrogate pair' is often used to
describe these strings. 

The treble clef glyph `'\u{1D11E}'` is composed of the `'\uD834'` and
`'\uDD1E'` surrogate pair.

You can get an extended string's surrogate pair through its `codeUnits`
property:

{% prettify dart %}
clef.codeUnits.map((codeUnit) => codeUnit.toRadixString(16)); 
// ['D834', 'DD1E']
{% endprettify %}

Accessing a surrogate pair member leads to errors, and you should avoid
properties and methods that expose it:

{% prettify dart %}
print('\uD834');          // Error: '\uD834' is not a valid string.
print('\uDD1E');          // Error: '\uDD1E' is not a valid string either.
print(clef.split('')[1]); // Invalid string. Dart Editor prints '?'
print(clef[0]);           // Invalid string. Dart Editor prints '?'
{% endprettify %}

When dealing with strings containing extended characters, you should use the
`runes` getter.

To get the string's length, use `string.runes.length`. Don't use
`string.length`:

{% prettify dart %}
print(clef.runes.length);     // 1
print(clef.length);           // 2
print(clef.codeUnits.length); // 2
{% endprettify %}

To get an individual character or its numeric equivalent, index the rune list:

{% prettify dart %}
print(clef.runes.toList()[0]); // 119070 ('\u{1D11E}')
{% endprettify %}

To get the string's characters as a list, map the string runes:

{% prettify dart %}
var clef = '\u{1D11E}'; // 𝄞
var title = '$clef list:'
print(subject.runes.map((rune) => new String.fromCharCode(rune)).toList());
// ['𝄞', ' ', 'l', 'i', 's', 't', ':']
{% endprettify %}


### Converting between characters and numerical codes

#### Problem

You want to convert string characters into numerical codes and vice versa.
You want to do this because sometimes you need to compare characters in a string
to numerical values coming from another source. Or, maybe you want to split a
string and then operate on each character.

#### Solution

Use the `runes` getter to get a string's code points:

{% prettify dart %}
'Dart'.runes.toList();            // [68, 97, 114, 116]

var smileyFace = '\u263A';        // ☺
print(smileyFace.runes.toList()); // [9786], (equivalent to ['\u263A']).

var clef = '\u{1D11E}';           // 𝄞
print(clef.runes.toList());       // [119070], (equivalent to ['\u{1D11E}']).
{% endprettify %}

Use `string.codeUnits` to get a string's UTF-16 code units:

{% prettify dart %}
'Dart'.codeUnits.toList();     // [68, 97, 114, 116]
smileyFace.codeUnits.toList(); // [9786]
clef.codeUnits.toList();       // [55348, 56606]
{% endprettify %}

##### Using codeUnitAt() to get individual code units

To get the code unit at a particular index, use `codeUnitAt()`:

{% prettify dart %}
'Dart'.codeUnitAt(0);     // 68
smileyFace.codeUnitAt(0); // 9786 (the decimal value of '\u263A')
clef.codeUnitAt(0);       // 55348 (does not represent a legal string) 
{% endprettify %}

#### Converting numerical codes to strings

You can generate a new string from numerical codes using the factory
`String.fromCharCodes(charCodes)`. You can pass either runes or code units and
`String.fromCharCodes(charCodes)` can tell the difference and do the right
thing automatically:

{% prettify dart %}
print(new String.fromCharCodes([68, 97, 114, 116]));                  // 'Dart'

print(new String.fromCharCodes([73, 32, 9825, 32, 76, 117, 99, 121]));
// 'I ♡ Lucy'

// Passing code units representing the surrogate pair.
print(new String.fromCharCodes([55348, 56606]));                      // 𝄞

// Passing runes.
print(new String.fromCharCodes([119070]));                            // 𝄞
{% endprettify %}

You can use the `String.fromCharCode()` factory to convert a single rune
or code unit to a string:

{% prettify dart %}
new String.fromCharCode(68);     // 'D'
new String.fromCharCode(9786);   // ☺
new String.fromCharCode(119070); // 𝄞
{% endprettify %}

Creating a string with only one half of a surrogate pair is permitted,
but not recommended.


### Calculating the length of a string

#### Problem

You want to get the length of a string, but are not sure how to calculate the
length correctly when working with variable length Unicode characters.

#### Solution

Use `string.runes.length` to get the number of characters in a string.

{% prettify dart %}
print('I love music'.runes.length); // 12
{% endprettify %}

You can safely use `string.runes.length` to get the length of strings that
contain extended characters:

{% prettify dart %}
var clef = '\u{1D11E}';        // 𝄞
var subject = '$clef list:';   // 
var music = 'I $hearts $clef'; // 'I ♡ 𝄞'

clef.runes.length;             // 1
music.runes.length             // 5
{% endprettify %}

#### Discussion

You can directly use a string's `length` property (minus `runes`). This returns
the string's code unit length. Using `string.length` produces the same length
as `string.runes.length` for most unicode characters.

For extended characters, the code unit length is one more than the rune
length:

{% prettify dart %}
clef.length;                   // 2

var music = 'I $hearts $clef'; // 'I ♡ 𝄞'
music.length;                  // 6
{% endprettify %}

Unless you specifically need the code unit length of a string, use
`string.runes.length`.

##### Working with combined characters

It is tempting to brush aside the complexity involved in dealing with runes and
code units and base the length of the string on the number of characters it
appears to have. Anyone can tell that 'Dart' has four characters, and 'Amelié'
has six, right? Almost. The length of 'Dart' is indeed four, but the length of
'Amelié' depends on how that string was constructed:

{% prettify dart %}
var name = 'Ameli\u00E9';               // 'Amelié'
var anotherName = 'Ameli\u0065\u0301';  // 'Amelié'
print(name.length);                     // 6
print(anotherName.length);              // 7
{% endprettify %}

Both `name` and `anotherName` return strings that look the same, but where
the 'é' is constructed using a different number of runes. This makes it
impossible to know the length of these strings by just looking at them.


### Processing a string one character at a time

#### Problem

You want to do something with each character in a string.

#### Solution

Map the results of calling `string.split('')`:

{% prettify dart %}
var lang= 'Dart';

// ['*D*', '*a*', '*r*', '*t*']
print(lang.split('').map((char) => '*${char}*').toList());

var smileyFace = '\u263A';
var happy = 'I am $smileyFace';
print(happy.split('')); // ['I', ' ', 'a', 'm', ' ', '☺']
{% endprettify %}

Or, loop over the characters of a string:

{% prettify dart %}
var list = [];
for(var i = 0; i < lang.length; i++) {
  list.add('*${lang[i]}*'); 
}

print(list); // ['*D*', '*a*', '*r*', '*t*']
{% endprettify %}

Or, map the string runes:

{% prettify dart %}
// ['*D*', '*a*', '*r*', '*t*']
var charList = "Dart".runes.map((rune) {
  return '*${new String.fromCharCode(rune)}*').toList();
});

// [[73, 'I'], [32, ' '], [97, 'a'], [109, 'm'], [32, ' '], [9786, '☺']]
var runeList = happy.runes.map((rune) {
  return [rune, new String.fromCharCode(rune)]).toList();
});
{% endprettify %}

When working with extended characters, you should always map the string runes.
Don't use `split('')` and avoid indexing an extended string. See the _Handling
extended characters that are composed of multiple code units_ recipe for
special considerations when working with extended strings.


### Splitting a string into substrings

#### Problem

You want to split a string into substrings using a delimiter or a pattern.

#### Solution

Use the `split()` method with a string or a RegExp as an argument.

{% prettify dart %}
var smileyFace = '\u263A';
var happy = 'I am $smileyFace';
happy.split(' '); // ['I', 'am', '☺']
{% endprettify %}

Here is an example of using `split()` with a RegExp:

{% prettify dart %}
var nums = '2/7 3 4/5 3~/5';
var numsRegExp = new RegExp(r'(\s|/|~/)');
nums.split(numsRegExp); // ['2', '7', '3', '4', '5', '3', '5']
{% endprettify %}

In the code above, the string `nums` contains various numbers, some of which
are expressed as fractions or as int-divisions. A RegExp splits the string to
extract just the numbers.

You can perform operations on the matched and unmatched portions of a string
when using `split()` with a RegExp:

{% prettify dart %}
var phrase = 'Eats SHOOTS leaves';

var newPhrase = phrase.splitMapJoin((new RegExp(r'SHOOTS')),
  onMatch:    (m) => '*${m.group(0).toLowerCase()}*',
  onNonMatch: (n) => n.toUpperCase());

print(newPhrase); // 'EATS *shoots* LEAVES'
  
{% endprettify %}

The RegExp matches the middle word ('SHOOTS'). A pair of callbacks are
registered to transform the matched and unmatched substrings before the
substrings are joined together again.


### Determining whether a string contains another string

#### Problem

You want to find out whether a string is the substring of another string.

#### Solution

Use `string.contains()`:

{% prettify dart %}
var fact = 'Dart strings are immutable';
print(fact.contains('immutable')); // True.
{% endprettify %}

You can use a second argument to specify where in the string to start looking:

{% prettify dart %}
print(fact.contains('Dart', 2)); // False
{% endprettify %}

#### Discussion

The String class provides a couple of shortcuts for testing whether a
string is a substring of another:

{% prettify dart %}
print(string.startsWith('Dart')); // True.
print(string.endsWith('e'));      // True.
{% endprettify %}

You can also use `string.indexOf()`, which returns -1 if the substring
is not found within a string, and otherwise returns the matching index:

{% prettify dart %}
var found = string.indexOf('art') != -1; // True, `art` is found in `Dart`.
{% endprettify %}

You can also use a RegExp and `hasMatch()`:

{% prettify dart %}
var found = new RegExp(r'ar[et]').hasMatch(string);
//  True, 'art' and 'are' match.
{% endprettify %}

### Finding matches of a regular expression

#### Problem

You want to use RegExp to match a pattern in a string, and want to be
able to access the matches.

#### Solution

Construct a regular expression using the RegExp class, and find matches
using the `allMatches()` method:

{% prettify dart %}
var neverEatingThat = 'Not with a fox, not in a box';
var regExp = new RegExp(r'[fb]ox');
List matches = regExp.allMatches(neverEatingThat);
print(matches.map((match) => match.group(0)).toList()); // ['fox', 'box']
{% endprettify %}

#### Discussion

You can query the object returned by `allMatches()` to find out the
number of matches:

{% prettify dart %}
var howManyMatches = matches.length; // 2
{% endprettify %}

To find the first match, use `firstMatch()`:

{% prettify dart %}
var firstMatch = RegExp.firstMatch(neverEatingThat).group(0); // 'fox'
{% endprettify %}

To directly get the matched string, use `stringMatch()`:

{% prettify dart %}
print(regExp.stringMatch(neverEatingThat));         // 'fox'
print(regExp.stringMatch('I like bagels and lox')); // null
{% endprettify %}

### Substituting strings using regular expressions

#### Problem

You want to match substrings within a string and make substitutions
based on the matches.

#### Solution

Construct a regular expression using the RegExp class and make
replacements using `replaceAll()` method:

{% prettify dart %}
var resume = 'resume'.replaceAll(new RegExp(r'e'), '\u00E9'); // 'résumé'
{% endprettify %}

If you want to replace just the first match, use `replaceFirst()`:

{% prettify dart %}
// Replace the first match of one or more zeros with an empty string.
var smallNum = '0.0001'.replaceFirst(new RegExp(r'0+'), ''); // '.0001'
{% endprettify %}

You can use `replaceAllMapped()` to register a function that modifies the
matches:

{% prettify dart %}
var heart = '\u2661'; // '♡'
var string = 'I like Ike but I $heart Lucy';
var regExp = new RegExp(r'[A-Z]\w+');
var newString = string.replaceAllMapped(regExp, (match) {
  return match.group(0).toUpperCase()
}); 
print(newString); // 'I like IKE but I ♡ LUCY'
{% endprettify %}


## Lists

### Creating a fixed length list

#### Problem

You want to create a list but don't want to allow it's size to be
changed.

#### Solution

Pass the list size as an argument to the List constructor. This creates a
fixed-length list:

{% prettify dart %}
var fixedList = new List(3); // fixedList can have exactly 3 items.
{% endprettify %}

You cannot change the size of the list:

{% prettify dart %}
fixedList.add(2);       // UnsupportedError
fixedList.removeLast(); // UnsupportedError
fixedList.length = 10;  // UnsupportedError
{% endprettify %}

You _can_ change the values of list elements:

{% prettify dart %}
fixedList[0] = 'red';
fixedList[1] = 'green';
fixedList[2] = 'blue';
print(fixedList); // ['red', 'green', 'blue']
{% endprettify %}


### Creating an immutable List

#### Problem

Because you want to protect your data from being accidentally overwritten, you
want to use a list that cannot be changed after it is created.  You don't want
anyone to be able to modify list elements, add new elements, or remove
existing elments.

#### Solution

Create an immutable list using the reserved word 'const':

{% prettify dart %}
const List<String> vowels = const ['A', 'E', 'I', 'O', 'U'];
{% endprettify %}

A list constructed using const is a compile-time constant. Each element of a
const list must also be a compile-time constant.

{% prettify dart %}
const List<String> vowels = const ['A', 'E', 'I', 'O', 'U'];
const List<String> rgb = const ['R', 'G', 'B'];
const List<List<String>> chars = const [vowels, rgb];
{% endprettify %}

Attempting to use non-const values in a const list results in a compile-time
error:

{% prettify dart %}
// ILLEGAL.
const List<List<String>> chars = const [
    ['A', 'E', 'I', 'O', 'U'], ['R', 'G', 'B']];
{% endprettify %}

Once a const list is constructed, you cannot change its length:

{% prettify dart %}
vowels.add('Y'); // UnsupportedError
{% endprettify %}

And, you cannot modify any list element:

{% prettify dart %}
vowels[0] = 'a'; // UnsupportedError
{% endprettify %}

Two const lists are considered equal _and identical_ if they have the same
length, and the same values in the same order. In other words, const lists are
canonicalized:

{% prettify dart %}
const List<String> colors = const ['R', 'G', 'B'];    
const List<String> rgb    = const ['R', 'G', 'B']; 
      
colors == rgb;                   // true
identical(colors, rgb);          // true
colors.hashCode == rgb.hashCode; // true
{% endprettify %}


### Creating a list and initializing it with default values

#### Problem

You want to create a new list and want to assign defalt values at every position
when the list is created.

#### Solution

Use the `List.generate()` constructor. Pass it a function that generates the
fill value: 

{% prettify dart %}
var filledList = new List<bool>.generate(3, (_) => false);
print(filledList); // [false, false, false]

filledList.add(true);
print(filledList); // [false, false, false, true]
{% endprettify %}

If you want a fixed-length list, set the optional `growable` argument to
`false` (it is `true` by default):

{% prettify dart %}
var fixedAndFilled = new List<bool>.generate(3, (_) => false, growable: false);
{% endprettify %}

You can generate fill values dynamically:

{% prettify dart %}
List<List<int>> grid = new List.generate(3, (x) => [x, x + 1, x + 2]);
{% endprettify %}

The code above generates a 3 X 3 grid:

{% prettify dart %}
print(grid); // [[0, 1, 2], [1, 2, 3], [2, 3, 4]]
{% endprettify %}

#### Discussion

You will optionally use the `List.filled()` constructor to create a list and
initialize it with default values:

{% prettify dart %}
var filledList = new List<bool>.filled(3, false);
print(filledList); // [false, false, false]
{% endprettify %}

The `List.filled()` constructor has two restrictions that `List.generate()`
does not:

- You can only create fixed-length lists.
- You cannot dynamically generate fill values.

If these restrictions are not important to your code, you can use
`List.filled()` instead of `List.generate()`.


### Copying a list

#### Problem

You want to create a new list with the elements of another list.

#### Solution

Use the `List.from()` constructor and pass the original list as an argument:

{% prettify dart %}
var fruit1 = ['orange', 'banana', 'mango'];
var fruit2 = new List.from(fruit1);
{% endprettify %}

#### Discussion

The `List.from()` constructor creates a new, independent list:

{% prettify dart %}
identical(names, namesCopy); // false
{% endprettify %}

However, compound elements in the new list retain a reference to the original:

{% prettify dart %}
var names = [{'first': 'Reggie', 'last': 'Jackson'}, 
             {'first': 'Cy', 'last': 'Young'}];

var namesCopy = new List.from(names);
identical(names.first, namesCopy.first); // true
{% endprettify %}

Modifying an element in the original changes the corresponding element in the
copy (and vise versa):

{% prettify dart %}
names[0]['last'] = 'Jefferson';
print(namesCopy[0]['last']); // 'Jefferson'
{% endprettify %}

Since the lists themselves do not share a reference, adding a new element to
one does not affect the other:

{% prettify dart %}
names.add({'first' : 'Micky', 'last' : 'Mantle'});
names.length;     // 3
namesCopy.length; // 2
{% endprettify %}

##### Fixed-length vs. flexible-length lists

By default, the new list created will be growable. This is true even if you
passed a `const` list as an argument to `List.from()`:

{% prettify dart %}
const List<String> vowels1 = const ['A', 'E', 'I', 'O', 'U'];
var vowels2 = new List.from(vowels1);

vowels2.add('Y');
print(vowels2); // ['A', 'E', 'I', 'O', 'U', 'Y']

vowels2[0] = 'Y';
print(vowels2[0]); // 'Y'
{% endprettify %}

To make the copy fixed-length, set `List.from()`s `growable` argument to
`false`:

{% prettify dart %}
const List<String> vowels1 = const ['A', 'E', 'I', 'O', 'U'];
var vowelsFixed = new List.from(vowels1, growable: false);
var fruitFixed = new List.from(fruit1, growable: false);
{% endprettify %}

You can then modify an element:

{% prettify dart %}
vowelsFixed[0] = 'Y';
print(vowelsFixed[0]); 'Y'
{% endprettify %}

But you cannot add or remove elements:

{% prettify dart %}
vowelsFixed.add('Y');     // UnsupportedError
vowelsFixed.removeLast(); // UnsupportedError
{% endprettify %}


### Appending items to a list

#### Problem

You want to add items to the end of a list, and want to know the most
efficient way to do so.

#### Solution

To add one item at a time to a list, use `add()`:

{% prettify dart %}
var names = ['Seth', 'Timothy', 'John'];
names.add('Kathy');
names.add('Mary');
print(names); // ['Seth', 'Timothy', 'John', 'Kathy', 'Mary']
{% endprettify %}

If the items to be added are already in a list, you can add them all at once
using `addAll()`:

{% prettify dart %}
var names = ['Seth', 'Timothy', 'John'];
names.addAll(['Kathy', 'Mary']);
print(names); // ['Seth', 'Timothy', 'John', 'Kathy', 'Mary']
{% endprettify %}

Or, you can increase the `length` property of a list. This generates
additional entries that are initialized to null. 

{% prettify dart %}
names.length += 2; // Creates 2 more null items.
print(names);      // ['Seth', 'Timothy', 'John', null, null]
{% endprettify %}

You can then replace the `null` values with actual values:

{% prettify dart %}
var names = ['Seth', 'Timothy', 'John'];
var moreNames = ['Kathy', 'Mary'];
      
var oldLength = names.length;
names.length += 2;
      
for (var i = 0; i < moreNames.length; i++) {
  names[i + oldLength] = moreNames[i];
}

print(names); ['Seth', 'Timothy', 'John', 'Kathy', 'Mary']
{% endprettify %}

Appending items to a list by increasing the list length first is generally more
efficient than using `add()` or `addAll()`.


## Numbers

### Converting a string to a number

#### Problem

You want to parse a string and convert it to a number.

#### Solution

Use `int.parse` to convert a string to an int:

{% prettify dart %}
print(int.parse('231')); // 231
{% endprettify %}

The strings can be prefixed with a '+' or a '-':

{% prettify dart %}
print(int.parse('+231')); // 231
print(int.parse('-231')); // -231
{% endprettify %}
    
You can pass in the radix as a second argument:

{% prettify dart %}
print(int.parse('231', radix: 16));  // 561
print(int.parse('F34A', radix: 16)); // 62282
{% endprettify %}
    
Strings starting with '0x', '-0x' or '+0x' are assumed to have a radix of 16:

{% prettify dart %}
print(int.parse('0x231')); // 561
{% endprettify %}

Use `double.parse` to convert a string to a double:

{% prettify dart %}
print(double.parse('3.14')); // 3.14
{% endprettify %}
    
The method accepts exponential notation:

{% prettify dart %}
print(double.parse('3.14e5')); // 314000.0
{% endprettify %}

Both `int.parse` and `double.parse` throw a FormatException if they are passed
invalid arguments.


### Converting a number to a string

#### Problem

You want to convert a number to a string.

#### Solution

Use `toString()` for a no-frills number to string conversion:

{% prettify dart %}
1234.toString();   // '1234'
3.1519.toString(); // '3.1519'
{% endprettify %}

To specify the number of signficant digits, use the `toStringAsPrecision()`
method: 

{% prettify dart %}
1234.toStringAsPrecision(5);   // '1234.0'
3.1519.toStringAsPrecision(8); // '3.1519000'
{% endprettify %}
  
To specify the number of digits after the decimal, use `toStringAsFixed()`:

{% prettify dart %}
1234.toStringAsFixed(2);   // '1234.00'
3.1519.toStringAsFixed(2); // '3.15'
{% endprettify %}

To convert the number to decimal exponential notation, use the
`toStringAsExponential()` method: 

{% prettify dart %}
1234.toStringAsExponential();   // '1.234e+3'
{% endprettify %}

You can specify the radix when converting an integer to a string:

{% prettify dart %}
64.toRadixString(2);  // '1000000'
64.toRadixString(8);  // '100'
64.toRadixString(16); // '40'
{% endprettify %}


## JSON

### Encoding JSON

#### Problem

You want to convert a Dart object into JSON.

#### Solution

Use `json.stringify()` to encode a Dart object into a JSON-formatted string.

The following Dart objects are automatically encoded into JSON by
`json.stringify()`:

* int
* double
* String
* bool
* null
* List
* Map

{% prettify dart %}
var person = {'name': 'joe', 
              'born':  2002,
              'into': {'films' : ['crime', 'noir']},
              'aliases': null
             };

json.stringify(person);
// '{"name":"joe","born":2002,"into":{"films":["crime","noir"]},"aliases":null}'
{% endprettify %}

Note that `json.stringify()` encodes List and Map objects recursively.

If an object of a type not in the list above is passed to `json.stringify()`,
it calls that objects `toJson()` method:

{% prettify dart %}
class Person {
  String name;
  num age;
  
  Person(this.name, this.age);
 
  String toJson() => json.stringify({"name": name, "age": age});
}

var person = new Person('john', 32);
json.stringify(person); // '"{\"name\":\"john\",\"age\":32}"'
{% endprettify %}

If the `toJson()` method is not defined, `json.stringify()` throws an exception:

{% prettify dart %}
class Book {
  String title;
  num numPages;
 
  Book(this.title, this.numPages);
}

var book = new Book('War and Peace', 1089);
json.stringify(book); // json.JsonUnsupportedObjectError
{% endprettify %}

It is possible that `json.stringify()` calls `toJson()` on several objects,
and if any one of those objects lacks a `toJson()`, an exception gets thrown.
Use `JsonUnsupportedObjectError`'s `cause` property to see which object
triggered the exception:

{% prettify dart %}
var person = new Person('john', 32);          // Has a toJson().
var book = new Book('War and Peace', 1089);   // Lacks a toJson().
var object = {'person': person, 'reads' : book};

try {
  json.stringify(object);
} catch(e) {
  print(e.cause.toString());
  // "Class 'Book' has no instance method 'toJson'..."
}
{% endprettify %}


### Decoding JSON

#### Problem

You want to convert a JSON string into a Dart object.

#### Solution

Use `json.parse()` to decode a JSON-encoded string into a Dart object:

{% prettify dart %}
var jsonPerson = '{"name" : "joe", "date" : [2013, 3, 10]}';
  
var person = json.parse(jsonPerson);

person['name'];         // 'joe'
person['date'];         // [2013, 3, 10]
person['date'] is List; // true
{% endprettify %}
    
Sometimes you want to transform the data parsed by `json.parse`. For
example, you may prefer to express a date field as a DateTime object, and not
as a list of numbers representing the year, month and day. Specify a 'reviver'
function as a second argument to `json.parse`. 

This function is called once for each object or list property parsed, and the 
return value of the reviver function is used instead of the parsed value:

{% prettify dart %}
var jsonPerson = '{"name" : "joe", "date" : [2013, 3, 10]}';

var person = json.parse(jsonPerson, (k, v) {
  if (k == "date") {
    return new DateTime(2012, 10, 3);
  }
  return v;
});
      
person['name'];             // 'joe'
person['date'] is DateTime; // true
{% endprettify %}


## URIs

Note: all examples require that you load 'dart:uri':

{% prettify dart %}
import 'dart:uri';
{% endprettify %}


### Encoding and Decoding fully qualified URIs

#### Problem

You want to encode and decode URIs so that you can escape URI characters
correctly.

#### Solution

Use `encodeUri()` to encode a fully qualified URI:

{% prettify dart %}
encodeURI('http://www.example.com/file with spaces.html');
// 'http://www.example.com/file+with+spaces.html'
{% endprettify %}

Characters that have special meaning in the URI (such as #;,/?:@&=$) are not
escaped:

{% prettify dart %}
encodeUri('http://example.com/?x=10&y=20#last');
// 'http://example.com/?x=10&y=20#last'

encodeUri('mailto:bob@example.com'); // 'mailto:bob@example.com'
{% endprettify %}

Call `encodeUriComponent()` to encode any user-provided parameters that are
passed to the server as part of a URI:

{% prettify dart %}
var params = encodeUriComponent('?param1=10&param2=20');
print(params); // '%3Fparam1%3D10%26param2%3D20'

encodeUri('http://www.example.com/') + params;
// 'http://www.example.com/%3Fparam1%3D10%26param2%3D20'));
{% endprettify %}
      
Don't call `encodeUriComponent()` on the complete URI. It escapes characters
like : and / and renders the URI unusable:

{% prettify dart %}
encodeUriComponent('http://www.example.com/'); 
// 'http%3A%2F%2Fwww.example.com%2F'
{% endprettify %}

To decode a URI previously encoded using `encodeUri()`, use `decodeUri()`:

{% prettify dart %}
var uri = 'http://www.example.com/file with spaces.html';
var encodedUri = encodeUri(uri);
decodeUri(encodedUri) == uri; // true
{% endprettify %}

To decode a URI component previously encoded using `encodeUriComponent()`, use
`decodeUriComponent()`: 

{% prettify dart %}
var params = encodeUriComponent('?param1=10&param2=20');
var encodedParams = encodeUriComponent(params);
decodeUriComponent(encodedParams) == params; // true
{% endprettify %}


### Parsing URIs

#### Problem

You want to access the parts of a URI.

#### Solution

The properties of a URI created using the `Uri()` constructor can be directly
accessed in the following manner: 

{% prettify dart %}
var uri = new Uri('http://example.org:8080/content/a.html#intro');

uri.scheme;     // 'http'
uri.userInfo;   // ''
uri.port        // ''
uri.domain;     // 'example.org'
uri.path;       // '/content/a.html'
uri.fragment;   // 'intro'
{% endprettify %}

Get the query parameters using the URI's `query` property:

{% prettify dart %}
var params = 'name=john&age=32';
var uri = new Uri('http://example.org/?name=john&age=32');
uri.query; // 'name=john&age=32'
{% endprettify %}

For http/https schemes, you can access the `origin` property:

{% prettify dart %}
var uri = new Uri('http://example.org:8080/content/a.html#intro');
uri.origin;     // 'http://example.org:8080'
{% endprettify %}

#### Discussion

The Uri class treats all URs that do not explicitly begin with a scheme as
relative:

{% prettify dart %}
new Uri('//example.org:8080/content/').isAbsolute; // false
new Uri('example.org:8080/content/').isAbsolute;   // false
{% endprettify %}

URIs that begin with a scheme, but that contain a fragment, are also considered
relative:

{% prettify dart %}
new Uri('http//example.org:8080/content/#intro').isAbsolute; // false
{% endprettify %}
    
The `scheme` and `domain` properties for  relative URIs are empty. Instead, a 
relative URI starts with the path component:

{% prettify dart %}
var uri = new Uri('example.org/content/a.html#intro');
      
uri.isAbsolute; // false
uri.scheme;     // ''
uri.domain;     // ''
uri.path;       // 'example.org/content/a.html'
{% endprettify %}

Accessing the `origin` property of a relative URI throws an exception:

{% prettify dart %}
var uri = new Uri('example.org/content/a.html#intro');
uri.isAbsolute; // false

try {
  var origin = uri.origin;
} catch(e) {
  print(e); // 'Illegal argument(s): Cannot use origin without a scheme'
}
{% endprettify %}
      

### Building URIs

#### Problem

You want to build URIs from individual parts.

#### Solution

Use the `Uri.fromComponents()` constructor to build up a URI from individual
parts:

{% prettify dart %}
var uri = new Uri.fromComponents(
  scheme: 'http',
  domain: 'example.org',
  path: '/content/a.html',
  query: 'name=john');

uri.isAbsolute;        // true
print(uri.toString()); // 'http://example.org/content/a.html?name=john'
{% endprettify %}

If you do not pass in the scheme as an argument, `Uri.fromComponents()` creates
a relative URI, and prefixes the URI with leading '//`:

{% prettify dart %}
var uri = new Uri.fromComponents(domain: '/content/a.html');
         
uri.isAbsolute;       // false
print(uri.toString(); // '//content/a.html'
{% endprettify %}
 
You should remove the leading '//' before using the URI.


## Testing

### Running only a single test

#### Problem

You want to run just a single test.  Maybe it is the test you are
currently working on and want to make that pass before running all your tests.
Or, perhaps it is a failing test that you want to explore in the debugger,
without dealing with the noise from other tests. 

#### Solution

To isolate a test, change `test()` to `solo_test()`.

{% prettify dart %}
test('if y == 0', ()  => expect(() => ...));
solo_test('if y != 0', () => expect(() => ...));
{% endprettify %}

Only the `solo_test()` will run:

{% prettify dart %}
unittest-suite-wait-for-done
PASS: if y != 0

All 1 tests passed.
unittest-suite-success
{% endprettify %}

Do not have multiple `only_test()`s:

{% prettify dart %}
solo_test('if y == 0', () => expect(() => ...));
solo_test('if y != 0', () => expect(() => ...));
{% endprettify %}

This raises an exception, and only the first `solo_test()` runs:

{% prettify dart %}
unittest-suite-wait-for-done
Exception: Only one test can be soloed right now.
PASS: if y == 0

All 1 tests passed.
unittest-suite-success
{% endprettify %}


### Filtering which tests are run

#### Problem

You want to run just a subset of all your tests. Maybe you've divided up
your tests carefully using `group()` and want to run the tests for one or more
group. Or perhaps you carefully placed certain keywords in your test
descriptions, and want to run only tests whose desciption contains a
specific keyword.

#### Solution

Use `filterTests()`. This function takes a String or RegExp argument and
matches it against each test description.  If the description matches, the
test runs, otherwise, it doesn’t.

By default, the Unittest library is configured to run _all_ tests defined in a
file when that file executes. You want to override this behavior. Do that by
subclassing the default Configuration class, and creating a custom
configuration:

{% prettify dart %}
class CustomConfiguration extends Configuration {
  get autoStart => false;
}
{% endprettify %}

Then, tell the test runner to use the new configuration:

{% prettify dart %}
configure(new CustomConfiguration());
{% endprettify %}

And then, initialize the test library (you need to explicitly do this when using
a custom configuration): 

{% prettify dart %}
ensureInitialized();
{% endprettify %}

After this is done, you can define groups and tests, call `filteredTests()`,
and run your tests using `runTests()`:

{% prettify dart %}
void main() {
  configure(new CustomConfiguration());
  ensureInitialized();
  
  // Your tests go here.

  filterTests(someStringOrRegExp);
  runTests();
}
{% endprettify %}

A complete example that uses `filterTests()` is shown below. You can run the
code from the command line, adding a keyword argument that is then passed on
to `filterTests()`: 

{% prettify dart %}
$ dart filter_tests.dart <keyword>
{% endprettify %}

Here is the code:

{% prettify dart %}
import 'package:unittest/unittest.dart';
import 'package:args/args.dart';
import 'dart:io';

class CustomConfiguration extends Configuration {
  get autoStart => false;
}

void main() {
  configure(new CustomConfiguration());
  ensureInitialized();  
  
  // Get the args from the command line.
  ArgParser argParser = new ArgParser();
  Options options = new Options();
  ArgResults results = argParser.parse(options.arguments);
  List<String> args = results.rest;

  test('one test', () => ... ); 
  test('crucial test', () => ... );
  test('another crucial test', =>  ...);

  group('case change', () {
    test('to upper', () => ... );
    test('to lower', () => ... );
  });

  if (!args.isEmpty) {
    filterTests(args[0]);
  }

  runTests();
}
{% endprettify %}

Run the tests with 'crucial' as an argument:

{% prettify dart %}
$ dart filter_tests.dart crucial
{% endprettify %}

Only tests with 'crucial' in the description run:

{% prettify dart %}
unittest-suite-wait-for-done
PASS: crucial test
PASS: another crucial test

All 2 tests passed.
unittest-suite-success
{% endprettify %}

Run the tests with 'case' as an argument: 

{% prettify dart %}
$ dart filter_tests.dart case
{% endprettify %}

Only tests within the 'case change' group run:

{% prettify dart %}
unittest-suite-wait-for-done
PASS: case change to upper
PASS: case change to lower

All 2 tests passed.
unittest-suite-success
{% endprettify %}

The keyword argument is optional. If you omit it, all tests in the file run:

{% prettify dart %}
unittest-suite-wait-for-done
PASS: a test
PASS: crucial test
PASS: another crucial test
PASS: case change to upper
PASS: case change to lower

All 5 tests passed.
unittest-suite-success
{% endprettify %}


### Running code before and after each test

#### Problem

You want initialization code to run before each test, and cleanup code to run
after each test, but want to avoid code duplication.

#### Solution

Within each `group()`, call `setUp()` for initialization and `tearDown()` for
cleanup. The function passed as an argument to `setUp()` runs before each
test, and that passed to `tearDown()` runs after each test.

##### setUp() example

Assume you have defined a Point class that contains several methods. You want
to test each method, and need a Point object in each test. Place the Point
initialization code inside `setUp()`, and the Point object becomes available in
each test:

{% prettify dart %}
void main() {
  group('test Point with setUp()', () {
    Point point;
    setUp(() {  
      point = new Point(3, 4);
    });

    test('toString',  () => expect(point.toString(), equals(...)));
    test('[](index)', () => expect(point[0], equals(...)));
  });
}
{% endprettify %}

##### setUp() and tearDown() example

Tests that create files and directories need to clean up after themselves.
Here is a function that creates a file inside a given directory: 

{% prettify dart %}
// Writes a file in 'dir' directory.
Path writeFileToDirectory(dir) {
  ...
}
{% endprettify %}

The code to create the directory goes in `setUp()`. The code to remove the
directory and its contents goes in `tearDown()`:

{% prettify dart %}
void main() {
  group('test writeFileToDirectory()', () {
    var tempDir;
    
    setUp(() {
       tempDir = new Directory('').createTempSync();
    });

    tearDown(() {
      if (tempDir.existsSync()) {
        tempDir.deleteSync(recursive: true);
      }
    });

    test('creates the correct path', () => ...);
    test('throws with a non-existent directory', () => ...);
  });
}
{% endprettify %}

The code inside `tearDown()` runs regardless of whether `setUp()` sets up a
resource successfully or not, and regardless of whether a test passes or
fails. If there is an error inside a test, code within `tearDown()` still runs:

{% prettify dart %}
group('tearDown behavior when', () {
  var tempDir;

  setUp(() {
    print('setting up');
    tempDir = new Directory('').createTempSync();
  });

  tearDown(() {
    print('tearing down');
    if (tempDir.existsSync()) {
      tempDir.deleteSync(recursive: true);
    }
  });

  test('test has error in it', () {
    22 ~/ 0; // Trigger an IntegerDivisionByZeroException.
    ...
  });
});
{% endprettify %}

Here is the (truncated) test run output:

{% prettify dart %}
unittest-suite-wait-for-done
setting up
tearing down

FAIL: tearDown behavior when test has error in it
  Caught IntegerDivisionByZeroException
  ...

0 PASSED, 1 FAILED, 0 ERRORS
Uncaught Error: Exception: Some tests failed.
Stack Trace:
...
{% endprettify %}

##### setUp() and tearDown() in nested group()s

The `setUp()` and `tearDown()` functions reset with the start of a new `group()`.
This applies to nested `group()`s, which do not inherit these functions:

{% prettify dart %}
group('test Point with nested setUp()', () {
  Point point;
  setUp(() {  
    point = new Point(3, 4);
  });
  
  group('[]()', () {
    // setUp() from outer group() does not run here. point is null.
    test('with valid index', () => ... );
    test('with invalid index', () => ... );
  });
});
{% endprettify %}

You can fix this problem by assigning each nested `group()` its own `setUp()`
and `tearDown()`.


### Testing synchronous exceptions

#### Problem

You want to test exceptions in your code. You want to know if some code
returns normally, or if it throws. Or, you want to test that a specific error is
raised, and that the error message is correct. 

#### Solution

To test whether code throws, use the `throws` matcher: 

{% prettify dart %}
expect(() => 10 ~/ 0, throws);
{% endprettify %}

To test that code runs without generating an exception, use the
`returnsNormally` matcher:

{% prettify dart %}
expect(() => 10 ~/ 1, returnsNormally);
{% endprettify %}
  
You can test the error type:

{% prettify dart %}
expect(() => throw new StateError('functions called in the wrong order'), 
    throwsStateError);
{% endprettify %}

The Unittest library provides matchers for commonly occuring exceptions and
errors:

{% prettify dart %}
throwsException
throwsFormatException
throwsArgumentError
throwsRangeError
throwsNoSuchMethodError
throwsUnimplementedError
throwsStateError
throwsUnsupportedError
{% endprettify %}

You can also use `throwsA` with a `predicate()` to test the exception type.
A `predicate()` returns a matcher using a function that returns true or
false:

{% prettify dart %}
expect(() => 10 ~/ 0, 
  throwsA(predicate((e) => e is IntegerDivisionByZeroException)));
{% endprettify %}
  
You can test the error message:

{% prettify dart %}
expect(() => throw new ArgumentError('bad argument'), 
  throwsA(predicate((e) => e.message == 'bad argument')));
{% endprettify %}

You can test the error type and the error message together:
  
{% prettify dart %}
expect(() => throw new RangeError('out of range'), 
  throwsA(predicate((e) => (e is RangeError && e.message == 'out of range'))));
{% endprettify %}


### Testing for double equality

#### Problem

Arithmetic involving doubles is inexact. You want to compare two doubles to
determine if they are acceptably close.

#### Solution 

Use the `closeTo()` matcher for testing approximate numerical equality:

{% prettify dart %}
closeTo(value, delta)
{% endprettify %}

This matcher checks if the number under test is within delta of some value.

Consider this code that computes the distance between two points: 

{% prettify dart %}
Point point1 = new Point(-2, -3);
Point point2 = new Point(-4, 4);

print(point1.distanceTo(point2)); // 7.280109889280518.  
{% endprettify %}

Here's how you can test for approximate equality:

{% prettify dart %}
expect(point1.distanceTo(point2)), closeTo(7.28, .001)); 
{% endprettify %}



