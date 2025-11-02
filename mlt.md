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

```
#/!/usr/bin/env bash

path=/home/oliver/.../02_Baerenschuetzklamm

miximg=' out=249 -mix 50 -mixer luma -mixer mix:-1'
mixvid=' -mix 50 -mixer luma -mixer mix:-1'

melt \
-track \
   $path/10_20_34-1-1080p.jpg out=249 \
   $path/10_36_18-1-1080p.jpg $miximg \
   $path/10_41_39_twoSteps.mp4 $mixvid \
...
   $path/15_05_31-1-1080p.jpg $miximg \
\
 \
 -transition mix:-1 always_active=1 a_track=0 b_track=1 sum=1  \
 -transition frei0r.cairoblend a_track=0 b_track=1 disable=0 \
 -profile atsc_1080p_50 \
 -consumer avformat:2017-10-02-baerenschuetzklamm.avi acodec=libmp3lame vcodec=libx264 b=5000k
```

## melt
https://www.mltframework.org/docs/melt/

Note: Only works with an old version of melt...

```sh
#/!/usr/bin/env bash

melt \
color:black out=1 \
-track \
   13_Trenta/05_44_00-1080p.jpg out=249 \
   13_Trenta/06_16_58_twoSteps.mp4 -mix 50 -mixer luma \
\
 \
 -transition mix:-1 always_active=1 a_track=0 b_track=1 sum=1  \
 -transition frei0r.cairoblend a_track=0 b_track=1 disable=0 \
 -profile atsc_1080p_50 \
 -consumer avformat:output.avi acodec=libmp3lame vcodec=libx264
```
