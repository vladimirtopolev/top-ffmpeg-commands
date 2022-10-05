# Inventory
Repository content some media items for practicing popular commands with FFmpeg.
All commands  have been successfully tested on MacBook Pro with macOS Monterey 12.5.1 and the version of FFmpeg is 5.0.1.

## 01 How to trim video/audio duration
```
// the first chunk 
ffmpeg -i media/input1.mp4 -t 00:00:10 cmd01_p01.mp4

// the second chunk
ffmpeg -ss 00:00:11 -i media/input1.mp4 -t 00:00:57 cmd01_02.mp4

// the third chunk
ffmpeg -ss 00:01:09 -i media/input1.mp4 cmd01_03.mp4
```

## 02 How to remove audio from a video
```
ffmpeg -i media/input1.mp4 -an cmd02.mp4
```

## 03 How to extract audio stream from video
```
ffmpeg -i media/input1.mp4 -vn cmd03.mp3
```

## 04 How to make a fast start for a video playing in a browser
```
ffmpeg -i media/input1.mp4 -movflags +faststart cmd04.mp4
```
## 05 How to scale a video resolution
```
ffmpeg -i media/input1.mp4 -s 640x360 cmd05_01.mp4
```
Scale video to 0.5x via filter
```
ffmpeg \
   -i media/input1.mp4 \
   -vf "scale=0.5*in_w:0.5*in_h" \
   cmd05_02.mp4
```

## 06 How to crop a video
```
ffmpeg -i media/part01.mp4 -vf "crop=x=(in_w-ow)/2:y=0:w=in_h*9/16:h=in_h" cmd06_01.mp4
```
Croipping with animation:
```
ffmpeg \
   -i media/part01.mp4 \
   -vf "crop=x=t*120:y=0:w=in_h*9/16:h=in_h" \
   cmd06_02.mp4
```
## 07 How to horizontally/vertically flip a video
Horizontal flip:
```
ffmpeg -i media/part01.mp4 -vf "hflip" cmd07_01.mp4
```
Vertical flip:
```
ffmpeg -i media/part01.mp4 -vf "vflip" cmd07_02.mp4
```

## 08 How to rotate a video
```
ffmpeg -i media/part01.mp4 -vf "rotate=angle=45*PI/180:fillcolor=red" cmd08.mp4
```
## 09 How to overlay images/videos on a video
Overlay image in a video:
```
ffmpeg -i media/part01.mp4 -i media/logo.png -filter_complex "overlay=W/2-w/2:H-h-30" cmd09_01.mp4
```
Animated watermark
```
ffmpeg -i media/part01.mp4 -i media/logo.png -filter_complex "overlay=t*90:H-h-30" cmd09_02.mp4

```
A bunch of watermarks together:
```
ffmpeg \
   -i media/part02.mp4 \
   -i media/part01.mp4 \
   -i media/logo.png \
   -filter_complex \
      "[1]scale=360:200[out1]; \
       [0][out1]overlay=W-w:H-h:shortest=1[out2]; \
       [out2][2]overlay=20:20[out]" \
   -map "[out]" -map "1:a" \
   cmd09_03.mp4
```

## 10 How to merge several videos in one
```
ffmpeg \
   -i media/part01.mp4 \
   -i media/part02.mp4 \
   -i media/part03.mp4 \
   -filter_complex "[0:v][0:a][1:v][1:a][2:v][2:a]concat=n=3:v=1:a=1[v][a]" \
   -map "[v]" -map "[a]" \
   cmd10.mp4
```

## 11 How to merge several videos with fade effect during video changes
Merge only video
```
ffmpeg \
   -i media/part01.mp4 \
   -i media/cover.mp4 \
   -i media/part03.mp4 \
   -filter_complex \
      "[0:v][1:v]xfade=transition=fade:duration=1:offset=9[vfade1]; \
       [vfade1][2:v]xfade=transition=fade:duration=1:offset=13[vout]" \
   -map "[vout]"\
   cmd11_01.mp4
```
Merge video and audio streams:
```
ffmpeg \
   -i media/part01.mp4 \
   -i media/cover.mp4 \
   -i media/part03.mp4 \
   -filter_complex \
      "[0:v][1:v]xfade=transition=fade:duration=1:offset=9[vfade1]; \
       [vfade1][2:v]xfade=transition=fade:duration=1:offset=13[vout]; \
       [0:a][1:a]acrossfade=d=1[afade1]; \
       [afade1][2:a]acrossfade=d=1[aout]" \
   -map "[vout]" -map "[aout]"\
   cmd11_02.mp4
```
Here another type of xfade transition - circleopen instead of fade:
```
ffmpeg \
   -i media/part01.mp4 \
   -i media/cover.mp4 \
   -i media/part03.mp4 \
   -filter_complex \
      "[0:v][1:v]xfade=transition=circleopen:duration=1:offset=9[vfade1]; \
       [vfade1][2:v]xfade=transition=circleopen:duration=1:offset=13[vout]" \
   -map "[vout]"\
   cmd11_03.mp4
```
## 12. How to convert a Video to Black and White
```
ffmpeg -i media/input1.mp4 -vf "hue=s=0" cmd12.mp4
```

## 13. How to extract images from the video
Take a thumbnail of video in 5 sec:
```
ffmpeg -ss 00:00:05 -i media/part01.mp4 -frames:v 1 thumbnail.png
```
Save more than 1 frame from video:
ffmpeg -i media/part01.mp4 -frames:v 20 thumbnail_%03d.png
```
```
## 14 How to create video from multiple images
```
ffmpeg -framerate 1/2 -i images/%03d.jpg -c:v libx264 -format yuv420p cmd14_01.mp4
```
With smooth transition between slides:
```
ffmpeg \
-loop 1 -t 3 -i images/001.jpg \
-loop 1 -t 3 -i images/002.jpg \
-loop 1 -t 3 -i images/003.jpg \
-filter_complex \
"[0][1]xfade=transition=hlslice:duration=1:offset=2[s0]; \
 [s0][2]xfade=transition=wipebr:duration=1:offset=4,format=yuv420p" \
-r 30 \
cmd14_02.mp4
```
## 15 How to add text to video
```
ffmpeg \
   -i media/cover.mp4 \
   -vf "drawtext=text=Vladimir Topolev: \
        x=(w-tw)/2:y=(h-th-50):\
        fontcolor=#ffffff:fontsize=48: \
        fontfile=media/ProximaNova.otf" \
   cmd15_01.mp4
```
With animation:
```
ffmpeg \
   -i media/cover.mp4 \
   -vf "drawtext=text=Vladimir Topolev: \
        x=t*180:y=(h-th-50):\
        fontcolor=#ffffff:fontsize=48: \
        fontfile=media/ProximaNova.otf" \
   cmd15_02.mp4
```
## 16 How to add subtitles to video
```
ffmpeg -i media/part01.mp4 -vf "subtitles=media/part01.srt" cmd16.mp4
```

## 17. How to combine two videos where one is like a mask
When mask is an image
```
ffmpeg \
   -i media/mask.jpg \
   -i media/part01.mp4 \
   -filter_complex "[0:v]colorkey=green[transper];[1:v][transper]overlay[out]" \
    -map "[out]" \
   cmd17-01.mp4
```
When mask is a video:
ffmpeg \
   -i media/mask.mp4 \
   -i media/part01.mp4 \
   -filter_complex "[0:v]colorkey=color=green:similarity=0.1:blend=0.0[transper];[1:v][transper]overlay[out]" \
    -map "[out]" \
   cmd17-02.mp4
```


