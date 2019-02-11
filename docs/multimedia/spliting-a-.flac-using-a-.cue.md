# Spliting a .flac using a .cue

## Install the packages
```bash
sudo apt install cuetools shntool flac
```

## Split the file
```bash
shnsplit -f file.cue -t %n-%t -o flac file.flac
```