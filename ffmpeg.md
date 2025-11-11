# Examples:
* [Pan image from left to right](/combined/imagemagick-and-ffmpeg/pan-from-left-to-right-over-part-of-4k-image.md) (includes [Imagemagick](/imagemagick.md))
* [Fade-in 2 images incl. alpha-channel](/combined/imagemagick-and-ffmpeg-2/fade-in-two-images-including-overlay.md) (includes [Imagemagick](/imagemagick.md))

# ffmpeg

Some notes for creating videos from my holidays on CLI (wrapped in some scripts)

## speedup video

Scale down a gopro vid to 1080p, speedup by factor 2 and use only 25fps with (default) h264 codec: 

```sh
ffmpeg -i input.mp4\
 -filter_complex "[0:v]setpts=0.5*PTS,scale=1920:1080,fps=25[v];[0:a]atempo=2.0[a]"\
 -map "[v]"\
 -map "[a]"\
 -crf 20 output.mp4
```

Change codec with something like `-c:v libx265 -b:v 45107k`

## slideshow

Make a slideshow from 2 images including a zoom effect (https://en.wikipedia.org/wiki/Ken_Burns_effect) resulting in a video of 9 second

```sh
ffmpeg\
 -loop 1 -t 5 -framerate 25 -i image1.jpg\
 -loop 1 -t 5 -framerate 25 -i image2.jpg\
 -filter_complex \
     "[0]scale=8000:-1,zoompan=z='zoom+0.001':x=iw/2-(iw/zoom/2):y=ih/2-(ih/zoom/2):d=5*25:fps=25[s0];\
      [1]scale=8000:-1,zoompan=z='zoom+0.001':x=iw/2-(iw/zoom/2):y=ih/2-(ih/zoom/2):d=5*25:fps=25[s1];\
      [s0][s1]xfade=transition=circleopen:duration=2:offset=4" \
  -t 9 -c:v libx264 -y output.mp4
```
See e.g. https://www.bannerbear.com/blog/how-to-do-a-ken-burns-style-effect-with-ffmpeg/ and https://www.bannerbear.com/blog/how-to-create-a-slideshow-from-images-with-ffmpeg/

For three videos:

```sh
ffmpeg\
 -loop 1 -t 5 -framerate 25 -i image1.jpg\
 -loop 1 -t 5 -framerate 25 -i image2.jpg\
 -loop 1 -t 5 -framerate 25 -i image3.jpg\
 -filter_complex \
     "[0]scale=8000:-1,zoompan=z='zoom+0.001':x=iw/2-(iw/zoom/2):y=ih/2-(ih/zoom/2):d=5*25:fps=25[s0];\
      [1]scale=8000:-1,zoompan=z='zoom+0.001':x=iw/2-(iw/zoom/2):y=ih/2-(ih/zoom/2):d=5*25:fps=25[s1];\
      [2]scale=8000:-1,zoompan=z='zoom+0.001':x=iw/2-(iw/zoom/2):y=ih/2-(ih/zoom/2):d=5*25:fps=25[s2];\
      [s0][s1]xfade=transition=circleopen:duration=2:offset=4[f0];\
      [f0][s2]xfade=transition=circleopen:duration=2:offset=8" \
  -t 13 -c:v libx264 -y output.mp4
```

or one further example:

```sh
ffmpeg -loglevel quiet -threads 2 \
 -loop 1 -framerate 25 -t 5 -i input.jpg \
 -i silentaudio.mp3 \
 -filter_complex "[0:v]scale=8000:-1,zoompan=z='zoom+0.001':s=3840x2160:x=iw/2-(iw/zoom/2):y=ih/2-(ih/zoom/2):d=125[v];[1:a]atrim=0:5[a]" \
 -acodec aac -vcodec libx264 -map [v] -map [a] -t 5 \
 -y  \
 -r 25 -pix_fmt yuv420p  -crf 20 output.mp4
```

**Note**: Merging more than approx 20 sources (images, video) uses a lot of RAM (at least at 4K)! My PC crashes everytime.

## put text on video
Example to put text at different positions and times:

```sh
ffmpeg -i input.mp4\
 -vf "\
    drawtext=text='My text center/bottom':enable='between(t,1,3)':x=(w-text_w)/2:y=h-th:fontsize=48:fontcolor=black:box=1:boxcolor=white@0.5:boxborderw=5,\
    drawtext=text='My text center/center':enable='between(t,4,8)':x=(w-text_w)/2:y=(h-text_h)/2:fontsize=96:fontcolor=black:box=1:boxcolor=white@0.5:boxborderw=5\
 " \
 -c:v -y output.mp4
```

See e.g. https://stackoverflow.com/questions/17623676/text-on-video-ffmpeg


## misc
[https://ffmpeg.org](https://ffmpeg.org/)
* ffprobe -v quiet -of csv=p=0 -show_entries format=duration input.avi -- show length
* ffmpeg -i input.mp3 -af 'afade=t=in:ss=0:d=3,afade=t=out:st=27:d=3' output.mp3 -- fade-in/fade-out
* ffmpeg -t 120 -i input.mp3 output.mp3 -- only first 2 minutes
* ffmpeg -ss 00:00:00.636 -i input.MP4 -frames:v 1 -vf "scale=iw/4:ih/4" frame.png -- extract a frame and rescale

**TODO** Check how to concat two mp3-files (1.mp3, 2.mp3) with fade-in/fade-out the result and an overlay of x seconds, maybe [https://www.ffmpeg.org/ffmpeg-all.html#Audio-Options](https://www.ffmpeg.org/ffmpeg-all.html#Audio-Options)

A small script to convert video files.
```
#!/bin/bash
# Sources:
# https://trac.ffmpeg.org/wiki/Create%20a%20mosaic%20out%20of%20several%20input%20videos
# https://stackoverflow.com/questions/11552565/vertically-or-horizontally-stack-several-videos-using-ffmpeg
# https://ffmpeg.org/pipermail/ffmpeg-user/2017-August/037057.html

if [ -z "$1" ]
    then
        echo "Usage:" 
        echo  "stabilizator.sh filename.mp4"
        exit 0
fi

ffmpeg -y -i $1 \
      -vf vidstabdetect=stepsize=32:shakiness=10:accuracy=10:result=transforms.trf \
      -c:v libx264 -b:v 5000k -s 1920x1080 -an -pass 1 -f rawvideo /dev/null
ffmpeg -y -i $1 \
      -vf vidstabtransform=input=transforms.trf:zoom=0:smoothing=10,unsharp=5:5:0.8:3:3:0.4 \
      -vcodec libx264 -b:v 5000k -s 1920x1080 -c:a libmp3lame -b:a 192k -ac 2 -ar 44100 -pass 2 \
      ${1%.*}_twoSteps.mp4
```
The vidstabdetect-options need to be optimized...

## ffmpeg
https://www.ffmpeg.org/

ffmpeg -i input.avi -f mp3 -ab 160000 -acodec libmp3lame output.avi
ffmpeg -ss 0s -i input.avi -t 12s -acodec copy -vcodec copy output.avi

Not happy with the following result -> buy'd a gopro
```sh
ffmpeg -y -i $1 \
      -vf vidstabdetect=stepsize=32:shakiness=10:accuracy=10:result=transforms.trf \
      -c:v libx264 -b:v 5000k -s 1920x1080 -an -pass 1 -f rawvideo /dev/null
ffmpeg -y -i $1 \
      -vf vidstabtransform=input=transforms.trf:zoom=0:smoothing=10,unsharp=5:5:0.8:3:3:0.4 \
      -vcodec libx264 -b:v 5000k -s 1920x1080 -c:a libmp3lame -b:a 192k -ac 2 -ar 44100 -pass 2 \
      ${1%.*}_twoSteps.mp4
```

## ffprobe
https://ffmpeg.org/ffprobe.html

Extract codec, bitrate, framerate and duration from a video
```sh
ffprobe -v error -select_streams v:0 -show_entries stream=codec_name -of default=noprint_wrappers=1:nokey=1 input.avi
ffprobe -v error -select_streams v:0 -show_entries stream=bit_rate -of default=noprint_wrappers=1:nokey=1 input.avi
ffprobe -v error -select_streams v:0 -show_entries stream=r_frame_rate -of default=noprint_wrappers=1:nokey=1 input.avi
ffprobe -v error -select_streams v:0 -show_entries stream=duration -of default=noprint_wrappers=1:nokey=1 input.avi
```

