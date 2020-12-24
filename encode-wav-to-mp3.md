# How to encode wave files to mp3, using ffmpeg

1. First, rename all the files in the CD folders to something like `file-01.wav`, `file-02.wav`.  
For this, you can use `KDE Dolphin` rename, use 2 # - `##` for renaming files.

2. Then, in each CD folder, run tbese commands: they will first concatenate the files into one `output.wav`.  
The file `output.wav` will then be compressed to `256kbps mp3`.

```
ffmpeg -f concat -safe 0  -i <( for f in *.wav; do echo "file '$(pwd)/$f'"; done ) output.wav  
for i in output.wav; do ffmpeg -threads 4 -i "$i" -b:a 256k -f mp3 "${i%.*}.mp3"; done  
```
3. Use a tool like `musicbrainz picard` to add folder art and title to the resulting mp3 files.
