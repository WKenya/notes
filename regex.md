# Regex Notes

## Building a Regex Foundation

Regex follows the POSIX standard for being used.

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