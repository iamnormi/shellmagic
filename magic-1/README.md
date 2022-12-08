# Shell Script: Read line in file
https://stackoverflow.com/questions/21478334/shell-script-read-line-in-file

#!/bin/sh

while read line; do
    cp `echo $line | tr ':' ' '`
done < "paths.txt"
