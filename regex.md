# Regex Notes

## Building a Regex Foundation

Regex follows the POSIX standard for being used.

`(ab)` - Parenthesis allow for multiple characters to be treated as one.

### How to build a regex

1. Understand the requirement

    - What needs to be included?
    - What needs to be excluded?

2. Identify the patterns in the inclusions list or the exclusions list

3. Represent the patterns using regular expressions

4. Use a regex engine like grep, Python, or Java to apply the regax pattern on the input

### Hands on with Linux Grep Regex Engine

Regex engines share the same fundamental philosophy, though the complete feature set of each engine may vary

Almost all major regex engines are POSIX standard compliant

POSIX stands for Portable Operating System Interface, is an IEEE standard

Sample Linux command: `grep 'fooa*bar' regex01.txt`

## The Basic Regex Set

### Wildcard Symbol

`*` - matches zero or more occurrences of preceding literal character

- EX: `a*` matches zero or more 'a' characters

`.` - matches any __one__ character in a single position

- EX: `foo.bar` matches 'fooabar', 'fooxbar', 'foocbar'

### Wildcard Asterisk Combo

`.*` - Zero or more occurrences of wildcard, which means zero or more occurrences of any character

- EX: `foo.*bar` matches foobar, 'fooabcbar', 'foobxcbar', 'foozbar', but not 'barfoo'

### Representing White spaces

`\s` - represents white space.
`\s*` - represents zero or more occurrences of whitespace

- EX: `foo\s*bar` matches 'foo    bar'

### Character classes

`[abc]` - Character class. One of the characters inside the square brackets - a, b, or c. Represents only 1 character position.

- EX: `[fcl]00` matches foo, hoo, loo, but not moo, or poo

`[^abc]` - Any character __except__ any of the ones indie the square brackets, in a single position.

- EX: `[^mh]oo` excludes moo, hoo, but includes foo, doo, loo, etc...

### Character Classes with Ranges

`[a-c]` - One of the characters falling in the range given in square brackets - a,b,c

- Must be an ASCII value
- Starting point value must be less than the ending point value

- EX: `[j-m]oo` matches koo, loo, moo, and not boo or zoo

`[a-cx]` - One of the characters falling in the range  __or__ any of the other choices given in square brackets - a,b,c, x

- EX: `[j-mz]oo` matches koo, loo, moo, and zoo

`[a-cA-Cx]` - One of the characters falling in one of the ranges __or__ any of the other choices given in square brackets - a,b,c,A,B,C,x

- EX: `[j-mK-Lz]oo` matches joo,Koo, Loo, moo, and zoo

### Escaping with backslash

The following characters should be escaped with a backslash as these characters are reserved: `^$*.[()\`

- EX: `x*\.y*` matches xxx.yy, xx.yyy, x.yy, but not xy, xxyy, etc...

If a `.` is inside a character class, it does __not__ need to be escaped.

- EX: `x[#:.]y` matches x#y, x:y, x.z, but not x&y or x%y

If any of the characters `^` or `-` appear inside a character class, then they need to be escaped as they are reserved characters.

- EX: `x[#:\^]y` matches x#y, x:y, and x^y
- EX: `x[#\\\^]y` matches x#y, x\y, and x^y

### Anchors

`^` is a placeholder that signifies beginning of a line when not used us a character class. Outside, it is a placeholder for beginning of line.

- EX: `^foo.*` matches 'foo bar baz', 'foo baz bar', but not 'bar foo baz'.

`$` is a placeholder that signifies the end of a line

- EX: `.*bar$` matches 'baz foo bar' and 'foo baz bar' but not 'bar foo baz'
- EX: `^foo$` matches 'foo', and nothing else

## The Extended Set

For Grep you need to use the -E option in Linux for the Extended set

### Curly Brace Repeater

`a{m}` represents exactly 'm' repetitions of whatever immediately precedes it.

- EX: `^[0-9]{3}$` matches exactly and only 3 digits on a line.

`a{m,n}` represents exactly 'm' through and including 'n' repetitions of whatever immediately precedes it.

- EX: `^[a-z]{4,6}$` matches 4-6 characters of a to z per line.

### Single Ended Curly Braces Repeater

`a{m,}` represents exactly 'm' and beyond repetitions of whatever immediately precedes it.

- EX: `^(ha){4,}$` matches 'ha' exactly 4 or more times

`a{m,}` represents exactly 'm' and beyond repetitions of whatever immediately precedes it.

- EX: `^(ha){4,}$` matches 'ha' exactly 4 or more times.

`a{,n}` represents exactly 'n' or less repetitions of whatever immediately precedes it.

- EX: `^(ha){,2}$` matches 'ha' exactly 2 or less times.

### The Plus Repeater

`a+` - One or more occurrences of 'a'

- EX: `fooa+bar` matches fooabar, fooaabar, fooaaaaaabar, etc...

### The Question Mark Binary

`a?` - Zero or one occurrences of 'a'.

- EX: `https?://website` matches https://website and http://website

### Pipe Operator

`(a|b)` - represents either a or b, where a and b can be multi-character strings.

- EX: `(log|ply)wood` matches logwood and plywood