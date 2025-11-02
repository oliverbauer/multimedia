# Task
**Given** an image "orig.jpg" of size 7008x4672 (Sony a7 IV)

**Task**: Create a 4k (3840x2160) video which pans from left to right in the center of the image (which means part of the image will be cropped).

1. Create an intermediate image "temp.jpg" of size 7008x2160 (so height is already correct). Note: In the example it is not exactly in the center (-> 400 pixels shifted)
> convert orig.jpg -gravity Center -crop x2160+0+400 temp.jpg

2. Create a video "result.mp4" of 1 minute with 60 fps which pans from left to right. Note second value of 60 is for speed and not for 60 seconds.

> ffmpeg -loop 1 -i temp.jpg -t 60 -vf "crop=3840:2160: x='(iw - 3840) * t / 60':y=0" -c:v libx264 -crf 23 -preset medium -pix_fmt yuv420p result.mp4 -y

# Example output

| <img src="/combined/imagemagick-and-ffmpeg/orig.jpg" width="280" height="186"> | <img src="/combined/imagemagick-and-ffmpeg/temp.jpg" width="280" height="144"> | <img src="/combined/imagemagick-and-ffmpeg/result.gif" width="280" height="144"> |
|:-------------------------------------------------------------------------------|:-------------------------------------------------------------------------------|:---------------------------------------------------------------------------------|
| orig.jpg (1402x934)                                                            | temp.jpg (1402x720)                                                            | result.mp4 (1280x720)                                                            |

Given: "orig.jpg" (1402x934) (which is created with "convert real_orig.jpg -resize %20 orig.jpg")

1. Will will create a 1280x720 (HD) video of it so we first crop some pixels (equally from top and bottom)
> convert orig.jpg -gravity Center -crop x720+0+0 temp.jpg

Note: temp.jpg has size "1402x720". Since a 16:9 AR is "1280x720", the following command uses 1280x720:

2. Command for a 5 seconds video (note factor 40 is a speed factor):
> ffmpeg -loop 1 -i temp.jpg -t 5 -vf "crop=1280:720: x='(iw - 720) * t / 40':y=0" -c:v libx264 -crf 23 -preset medium -pix_fmt yuv420p result.mp4 -y

Result (animated gif for repo created with ffmpeg -t 5 -i result.mp4 -vf "fps=4,scale=320:-1:flags=lanczos,split[s0][s1];[s0]palettegen[p];[s1][p]paletteuse" -loop 0 result.gif)
