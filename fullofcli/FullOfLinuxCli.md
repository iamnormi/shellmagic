# How to Append Text At End of Each Line in Linux:
```
$ sed -i s/$/:80/ data.txt
$ sed -i s/$/:80/ data.txt > data-new.txt
```
## Eg:
### input:
```
127.0.0.1
192.0.0.1
243.23.43.1
```
### output:
```
127.0.0.1:80
192.0.0.1:80
243.23.43.1:80
```

# Add Text in prefix of line:
```
$ awk '{print NR "> " $s}' inputfile > outputfile
```
## Eg:
### Input:
```
PSS-A (Primary A)
PSS-B (Primary B)
PSS-C (Primary C)
PSS-D (Primary D)
PSS-E (Primary E)
PSS-F (Primary F)
PSS-G (Primary G)
PSS-H (Primary H)
PSS-I (Primary I)
SPARE (SPARE)
```
### output:
```
1> PSS-A (Primary A)
2> PSS-B (Primary B)
3> PSS-C (Primary C)
4> PSS-D (Primary D)
5> PSS-E (Primary E)
6> PSS-F (Primary F)
7> PSS-G (Primary G)
8> PSS-H (Primary H)
9> PSS-I (Primary I)
10> SPARE (SPARE) 
```
# How to use grep and cut in script to obtain website URLs from an HTML file:
```
$ wget -qO- http://google.com/ | grep -Eoi '<a [^>]+>' | grep -Eo 'href="[^\"]+"' | grep -Eo '(http|https)://[^/"]+'
```
## output:
```
http://www.google.com.au
http://maps.google.com.au
https://play.google.com
http://www.youtube.com
http://news.google.com.au
https://mail.google.com
https://drive.google.com
http://www.google.com.au
http://www.google.com.au
https://accounts.google.com
http://www.google.com.au
https://www.google.com
https://plus.google.com
http://www.google.com.au
```
# How to use grep and cut in script to obtain website URLs from an HTML file
## file as input
```
$ cat urls.html | grep -Eo "(http|https)://[a-zA-Z0-9./?=_%:-]*" | sort -u
```

* grep -E : is the same as egrep
* grep -o : only outputs what has been grepped
* (http|https) : is an either / or
* a-z : is all lower case
* A-Z : is all upper case
* . : is dot
* / : is the slash
* ? : is ?
* = : is equal sign
* _ : is underscore
* % : is percentage sign
* : : is colon
* - : is dash
* * : is repeat the [...] group
* sort -u : will sort & remove any duplicates

## URL as input
```
$ wget -qO- https://stackoverflow.com/ | grep -Eo "(http|https)://[a-zA-Z0-9./?=_-]*" | sort -u
```
## output:
```
https://stackauth.com
https://meta.stackoverflow.com
https://cdn.sstatic.net/Img/svg-icons
https://stackoverflow.com
https://www.stackoverflowbusiness.com/talent
https://www.stackoverflowbusiness.com/advertising
https://stackoverflow.com/users/login?ssrc=head
https://stackoverflow.com/users/signup?ssrc=head
https://stackoverflow.com
https://stackoverflow.com/help
https://chat.stackoverflow.com
https://meta.stackoverflow.com 
```
