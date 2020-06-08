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

- EX: `^(ha){4,}$` matches 'ha' 4 or more times

`a{,n}` represents exactly 'n' or less repetitions of whatever immediately precedes it.

- EX: `^(ha){,2}$` matches 'ha' up to and including 2 times.

### The Plus Repeater

`a+` - One or more occurrences of 'a'

- EX: `fooa+bar` matches fooabar, fooaabar, fooaaaaaabar, etc...

### The Question Mark Binary

`a?` - Zero or one occurrences of 'a'.

- EX: `https?://website` matches https://website and http://website

### Pipe Operator

`(a|b)` - represents either a or b, where a and b can be multi-character strings.

- EX: `(log|ply)wood` matches logwood and plywood

## Find and Replace with Capture Groups

Cannot use `grep`, need to use `sed` on the command line.

`sed -r 's/pattern/replacement/g' inputfile`

- `-r` enables the POSIX extended set
- `s` enables substitution
- `g` enables this globally. Sed substitutes only the first match by default.


### How to build a regex substitution

1. Understand the requirement

    - What needs to be replaced?
    - What should be the replacement?

2. Represent the patterns using regex. Enclose the pattern(s) that needs to be replaced with parentheses to segregate them into capture groups.

3. Come up with a substitution string by using the captured pattern groups.

4. Use a regex enabled find and replace engine to do the replacement.

### The Monitor Problem

Data is in format:

- 1280x720
- 1920x1080
- 1600x900

Need to transform to the following format:

- 1280 pix by 720 pix

Regex Pattern

`([0-9]+)x([0-9]+)`

Capture group 1 and 2 correspond to the data in the parentheses in order.

Substitution pattern

`\1 pix by \2 pix`

Escape the capture groups using the  group numbers to substitute the data.

Linux Command

`sed -r 's/([0-9]+)x([0-9]+)/\1 pix by \2 pix/g' regex25.txt`

### The First name Last name Problem

Data is in format:

- John Wallace
- Martin Cook
- Adam Smith

Need to transform to the following format:

- Wallace,John
- Martin,Cook

Regex Pattern

`([a-zA-Z]+\s([a-zA-Z]+))`

Substitution pattern

`\2,\1`

Linux Command

`sed -r 's/([a-zA-Z])+\s([a-zA-Z]+))/\2,\1/g' regex26.txt`

### The Clock Problem

Data is in format:

- 7:32
- 6:12
- 11:33

Need to transform to the following format:

- 32 mins past 7
- 12 mins past 6
- 33 mins past 11

Regex Pattern

`(1*[0-9]):([0-5][0-9])`

Substitution pattern

`\2 mins past \1`

Linux Command

`sed -r 's/(1*[0-9]):([0-5][0-9])/\2 mins past \1/g' regex27.txt`

### The Phone Number Problem

Data is in format:

- 914.582.3013
- 873.334.2589
- 521.589.3147

Need to transform to the following format:

- xxx.xxx.3013
- xxx.xxx.2589
- xxx.xxx.3147

Regex Pattern

`[0-9]{3}\.[0-9]{3}\.([0-9]{4})`

Substitution pattern

`xxx.xxx.\1`

Escaping is __not__ needed for replacement patterns, only search patterns

Linux Command

`sed -r 's/[0-9]{3}\.[0-9]{3}\.([0-9]{4})/xxx.xxx.\1/g' regex28.txt`

### The Date Problem

Data is in format:

- Jan 5th 1987
- Dec 26th 2010
- Mar 2nd 1923

Need to transform to the following format:

- 5-Jan-87
- 26-Dec-10
- 2-Mar-23

Regex Pattern

`([a-zA-Z]{3})\s([0-9]{1,2}[a-z]{2}\s[0-9]{2}([0-9]{2}))`

Substitution pattern

`\2-\1-\3`

Escaping is __not__ needed for replacement patterns, only search patterns

Linux Command

`sed -r 's/([a-zA-Z]{3})\s([0-9]{1,2}[a-z]{2}\s[0-9]{2}([0-9]{2}))/\2-\1-\3/g' regex29.txt`

### Another Phone Number Problem

Data is in format:

- (914).582.3013
- (873).334.2589
- (521).589.3147

Need to transform to the following format:

- 914.582.3013
- 873.334.2589
- 521.589.3147

Regex Pattern

`\(([0-9]{3})\)(\.[0-9]{3}\.[0-9]{4})`

Substitution pattern

`\1\2`

Escaping is __not__ needed for replacement patterns, only search patterns

Linux Command

`sed -r 's/\(([0-9]{3})\)(\.[0-9]{3}\.[0-9]{4})/\1\2/g' regex30.txt`

### Email Challenge

Data is in format:

Include:

- bob.123@gmail.com
- alice-personal@yahoo.com
- admin@cloud.guru
- tom_business@amazon.ca

Exclude:

- george@yahoo
- robert_at_gmail.com
- steve austin@gmail.com

Regex Pattern

`^[a-zA-Z0-9_.-]+@[a-zA-Z]+\.[a-zA-Z]+$`
