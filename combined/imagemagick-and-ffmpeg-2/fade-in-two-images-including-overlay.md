# Task
**Given** three images "1.jpg", "2.jpg" and "3.jpg" of size 5184x2920 (Nikon Coolpix A 900 and Panasonic DC-TZ99).

**Task**: Create a FullHD (1920x1080) video of 10 seconds which shows "1.jpg" as a background image and fade-in 
both other images. "2.jpg" should be fade-in on the left part from bottom to top of the video and should 
make 50% of the video, "3.jpg" should fade-in on the right part from top to bottom and should make the remaining
50% of the video.
Both images, "2.jpg" and "3.jpg", will be cropped.

1. Create three intermediate files such that
   * "1a.jpg" has size 1920x1080
   * "2a.jpg" has size 960x1080
   * "3a.jpg" has size 960x1080

```sh
convert 1.jpg -geometry 1920x1080^ -gravity center -crop 16:9 1a.jpg;
convert 2.jpg -geometry 960x1080^ -gravity center -crop 8:9 2a.jpg;
convert 3.jpg -geometry 960x1080^ -gravity center -crop 8:9 3a.jpg;
```

2. Create the video

```sh
ffmpeg \
   -i 1a.jpg \
   -loop 1 -i 2a.jpg \
   -loop 1 -i 3a.jpg \
   -filter_complex "\
   [1:0]fade=in:st=0:d=3:alpha=1,fade=out:st=5:d=3:alpha=1 [t0];\
   [2:0]fade=in:st=0:d=3:alpha=1,fade=out:st=5:d=3:alpha=1 [t1];\
   [0:0][t0]overlay='0:min(0\,-overlay_h+t*overlay_h/3)'[t2];\
   [t2][t1]overlay=x=960:y=max(0\,overlay_h-t*overlay_h/3)\
   "\
   -t 10 -c:v libx264 output.mp4 -y;
```

"2a.jpg" and "3a.jpg" start immediately fading in for three seconds (d=3) and started to fade-out starting from second 5. The
final 2 Seconds (8-10) only "1a.jpg" will be shown.

# Example output


| <img src="/combined/imagemagick-and-ffmpeg-2/1a.jpg" width="280" height="186"> | <img src="/combined/imagemagick-and-ffmpeg-2/2a.jpg" width="140" height="144"> | <img src="/combined/imagemagick-and-ffmpeg-2/3a.jpg" width="140" height="144"> | <img src="/combined/imagemagick-and-ffmpeg-2/result.gif" width="280" height="186"> |
|:-------------------------------------------------------------------------------|:---------------------------------------------------------------------------------|:---------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------|
| 1a.jpg (1280x720)                                                              | 2a.jpg (640x720)                                                                 | 3a.jpg (640x720)                                                                 | output.mp4 (1280x720)                                                              |

Given: "1.jpg", "2.jpg" and "3.jpg" (1555x876) (which are created with "convert real_${i}.jpg -resize %30 ${i}.jpg").

1. Will will create a 1280x720 (HD) video of it so we first crop some pixels...
```sh
convert 1.jpg -geometry 1280x720^ -gravity center -crop 16:9 1a.jpg;
convert 2.jpg -geometry 640x720^ -gravity center -crop 8:9 2a.jpg;
convert 3.jpg -geometry 640x720^ -gravity center -crop 8:9 3a.jpg;
```
 
2. Now the video:

```sh
ffmpeg \
   -i 1a.jpg \
   -loop 1 -i 2a.jpg \
   -loop 1 -i 3a.jpg \
   -filter_complex "\
   [1:0]fade=in:st=0:d=3:alpha=1,fade=out:st=5:d=3:alpha=1 [t0];\
   [2:0]fade=in:st=0:d=3:alpha=1,fade=out:st=5:d=3:alpha=1 [t1];\
   [0:0][t0]overlay='0:min(0\,-overlay_h+t*overlay_h/3)'[t2];\
   [t2][t1]overlay=x=640:y=max(0\,overlay_h-t*overlay_h/3)\
   "\
   -t 10 -c:v libx264 output.mp4 -y;
```