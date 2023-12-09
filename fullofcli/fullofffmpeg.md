# Convert eac3 and ac3 6 ch to opus 6 ch
## arg for ffmpeg :
```
-c:a libopus -filter:a "channelmap=FL-FL|FR-FR|FC-FC|LFE-LFE|SL-BL|SR-BR:5.1" -b:a 192k
```
# Loop through files in a single directory
```
for name in *.ext ; do ffmpeg -i "$name"  "${name%.*}.ext" ; done
```
