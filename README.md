# multimedia
Some useful commands

## ffmpeg
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
The vidstabdetect maybe optimized...

## imagemagick
[https://imagemagick.org/index.php](https://imagemagick.org/index.php)

Rescale an image of 5184x3888 to 1920x1080 (maintain aspect ratio but crop)
* convert input.jpg -geometry 1920x -quality 100 temp-image.jpg
* convert temp-image.jpg -crop 1920x1080+0+180 -quality 100 output.jpg

Single command
* convert input.jpg -geometry 1920x -crop 1920x1080+0+180 -quality 100 output.jpg

A small script (imageconverter1080p.sh) to convert images to 1080p to use them in private holiday videos. Usage:
```
for i in *.jpg; do sh /home/oliver/multimedia_bis_inkl_2020/imageconverter1080p.sh $i; done
```
Be warned: Converted images will be deleted!
```
#!/bin/bash

if [ -z "$1" ]
    then
        echo "Usage:" 
        echo  "imageconvert1080p.sh filename.jpg"
        exit 0
fi

dimension=$(identify -format "%[fx:w]x%[fx:h]" $1)

echo $dimension

if [ $dimension = "5184x3888" ]; then
  convert $1 -geometry 1920x -crop 1920x1080+0+180 -quality 100 ${1%.*}-1080p.jpg
  rm $1
elif [ $dimension = "4320x3240" ]; then
  convert $1 -geometry 1920x -crop 1920x1080+0+180 -quality 100 ${1%.*}-1080p.jpg
  rm $1
elif [ $dimension = "6000x4000" ]; then
  # Nikon D3300
  convert $1 -geometry 1920x -crop 1920x1080+0+100 -quality 100 ${1%.*}-1080p.jpg
  rm $1
elif [ $dimension = "2592x1944" ]; then
  # Coolpix A 900, without crop 1920x1440
  convert $1 -geometry 1920x -crop 1920x1080+0+180 -quality 100 ${1%.*}-1080p.jpg
  rm $1
elif [ $dimension = "2160x1620" ]; then
  # Rollei, without crop 1920x1440
  convert $1 -geometry 1920x -crop 1920x1080+0+180 -quality 100 ${1%.*}-1080p.jpg
  rm $1
elif [ $dimension = "5184x2920" ]; then
  convert $1 -geometry 1920x1080! -quality 100 ${1%.*}-1080p.jpg
  rm $1
elif [ $dimension = "1920x1440" ]; then
  convert $1 -geometry 1920x -crop 1920x1080+0+180 -quality 100 ${1%.*}-1080p.jpg
  rm $1
else
  echo "dimension not considered: " + $dimension
fi
```

## mlt
[https://www.mltframework.org/](https://www.mltframework.org/)
```bash
#/!/usr/bin/env bash

melt \
color:black  out=00:00:36.000  `# comment` \
-track \
   temp2_temp_title.jpg     out=00:00:15.000 \
-track \
  -blank        out=00:00:2.000 \
   temp2_temp_DSCN3879.jpg   out=00:00:2.000 -attach-cut affine transition.geometry="0=200/350:300x0; 50=200/100:300x300" \
   temp2_temp_DSCN3879.jpg   out=00:00:3.000 -attach-cut affine transition.geometry="0=200/100:300x300"\
   temp2_temp_DSCN3879.jpg   out=00:00:2.000 -attach-cut affine transition.geometry="0=200/100:300x300; 50=200/250:300x0" \
-track \
  -blank        out=00:00:3.000 \
   temp2_temp_city.jpg  out=00:00:2.000 -attach-cut affine transition.geometry="0=750/250:300x0; 50=750/100:300x300" \
   temp2_temp_city.jpg  out=00:00:3.000 -attach-cut affine transition.geometry="0=750/100:300x300" \
   temp2_temp_city.jpg  out=00:00:2.000 -attach-cut affine transition.geometry="0=750/100:300x300; 50=750/250:300x0" \
-track \
  -blank        out=00:00:4.000 \
   temp2_temp_zug.jpg   out=00:00:2.000 -attach-cut affine transition.geometry="0=1300/250:300x0; 50=1300/100:300x300" \
   temp2_temp_zug.jpg   out=00:00:3.000 -attach-cut affine transition.geometry="0=1300/100:300x300" \
   temp2_temp_zug.jpg   out=00:00:2.000 -attach-cut affine transition.geometry="0=1300/100:300x300; 50=1300/250:300x0" \
-track \
   pango: text="MLT rocks" bgcolour=0xff000080 fgcolour=0x000000ff olcolour=0x000000ff                   out=00:00:02.000 size=192 weight=1000 \
   pango: markup="Sabbatical 05.2020-06.2020" bgcolour=0x00ff0080 fgcolour=0x000000ff olcolour=0x000000ff out=00:00:04.000 size=192 weight=1000 \
 \
-audio-track \
  26_002.mp3 out=00:00:111.000 \
-transition mix:-1 always_active=1                    a_track=0 b_track=1 sum=1  \
-transition frei0r.cairoblend                         a_track=0 b_track=1 disable=0 \
-transition composite                                 a_track=0 b_track=2 \
-transition composite                                 a_track=0 b_track=3 \
-transition composite                                 a_track=0 b_track=4 \
-transition affine valign=bottom halign=center fill=0 a_track=0 b_track=5 \
-transition composite                                 a_track=0 b_track=6 `# Audio` \
\
-profile atsc_1080p_25 \
-consumer avformat:output.avi acodec=libmp3lame vcodec=libx264
```
**TODO** Check how to use [Greyscale-Filter](https://www.mltframework.org/plugins/FilterGreyscale/) on the small images like: frame 0=100% greyscale,fragme1=98%greyscale,...,frame 24=50%greyscale,...,frame50=0%greyscale

Useful consumer:
- consumer sdl2 terminate_on_pause=1
- consumer avformat:frame17.jpg in=17 out=17
