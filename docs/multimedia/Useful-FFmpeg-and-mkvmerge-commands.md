# Useful FFmpeg and mkvmerge commands

## Burning subtitles
The output file could be in another format.
### SRT format
**In a .srt file:**
```bash
ffmpeg -i video.avi -vf subtitles=subtitle.srt out.avi
```
**From a video:**
```bash
ffmpeg -i video.mkv -vf subtitles=video.mkv out.avi
```

### ASS format
```bash
ffmpeg -i video.avi -vf "ass=subtitle.ass" out.avi
```

## Extracting audio tracks
The output file could be in another format.

**Extract the first track:**
```bash
ffmpeg -i input.avi -codec copy -map 0:a:0 -metadata TITLE="title" out.mp3
```

## Converting video to MKV
The input file could be in another format.
```bash
ffmpeg -i input_video.avi -f matroska -vcodec libx264 -acodec aac output_video.mkv
```

## Changing audio speed
The input and output files could be in another format.
**Increase from the speed at 23.976fps to 25fps:**
```bash
ffmpeg -i test.mkv -filter:a "atempo=1.0427083333333333" -vn output.aac
```

## Changing video framerate
```bash
mkvmerge -o out.mkv -A -S --default-duration '0:25fps' --fix-bitstream-timing-information 0 input.mkv
```