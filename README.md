# multimedia
mlt, ffmpeg, imagemagick... 

## ffmpeg
* ffprobe -v quiet -of csv=p=0 -show_entries format=duration input.avi -- show length
* ffmpeg -i input.mp3 -af 'afade=t=in:ss=0:d=3,afade=t=out:st=27:d=3' output.mp3 -- fade-in/fade-out
* ffmpeg -t 120 -i input.mp3 output.mp3 -- only first 2 minutes

## imagemagick
* Rescale 5184x38888 to 1920x1080 (maintain aspect ratio but crop)
* convert input.jpg -geometry 1920x -quality 100 temp-image.jpg
* convert temp-image.jpg -crop 1920x1080+0+180 -quality 100 output.jpg

## mlt
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
