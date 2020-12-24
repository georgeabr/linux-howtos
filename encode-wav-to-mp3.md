# How to encode wave files to mp3, using ffmpeg

1. First, rename all the `.wav` files in the CD folders to something like `file-0001.wav`, `file-0002.wav`.  
The below command will rename all `.wav` files to `file-0001.wav`, etc..  
```
ls | cat -n | while read n f; do mv "$f" `printf "file-%04d.wav" $n`; done
```

2. Then, in each CD folder, run tbese commands: they will first concatenate the files into one `output.wav`.  
The file `output.wav` will then be compressed to `256kbps mp3`.

```
ffmpeg -f concat -safe 0  -i <( for f in *.wav; do echo "file '$(pwd)/$f'"; done ) output.wav  
for i in output.wav; do ffmpeg -threads 4 -i "$i" -b:a 256k -f mp3 "${i%.*}.mp3"; done  
```
3. Use a tool like `musicbrainz picard` to add folder art and title to the resulting mp3 files.
