# Shell Script: Read lines in file
https://stackoverflow.com/questions/21478334/shell-script-read-line-in-file

```
#!/bin/sh
while read line; do
    cp `echo $line | tr ':' ' '`
done < "paths.txt"
```
# RegEx: Cheetsheet
https://regexr.com/#native_link#
```
Character classes:
.	any character except newline
\w\d\s	word, digit, whitespace
\W\D\S	not word, digit, whitespace
[abc]	any of a, b, or c
[^abc]	not a, b, or c
[a-g]	character between a & g

Anchors:
^abc$	start / end of the string
\b\B	word, not-word boundary

Escaped characters:
\.\*\\	escaped special characters
\t\n\r	tab, linefeed, carriage return

Groups & Lookaround:
(abc)	capture group
\1	backreference to group #1
(?:abc)	non-capturing group
(?=abc)	positive lookahead
(?!abc)	negative lookahead

Quantifiers & Alternation:
a*a+a?	0 or more, 1 or more, 0 or 1
a{5}a{2,}	exactly five, two or more
a{1,3}	between one & three
a+?a{2,}?	match as few as possible
ab|cd	match ab or cd
```
# How to Configure Auto-Login in Arch Linux?
## Edit the file
```
sudo vim /etc/systemd/system/getty.target.wants/getty@tty1.service
```
## default line look like (Manual login)
```
ExecStart=-/sbin/agetty -o '-- \\u' --noreset --noclear - ${TERM}
```
## For Autologin
type current user at USER
```
ExecStart=-/sbin/agetty -a USER  - ${TERM}
```
