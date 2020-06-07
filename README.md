# multimedia
Some useful commands

## ffmpeg
[https://ffmpeg.org](https://ffmpeg.org/)
* ffprobe -v quiet -of csv=p=0 -show_entries format=duration input.avi -- show length
* ffmpeg -i input.mp3 -af 'afade=t=in:ss=0:d=3,afade=t=out:st=27:d=3' output.mp3 -- fade-in/fade-out
* ffmpeg -t 120 -i input.mp3 output.mp3 -- only first 2 minutes

### Updating ffmpeg
Previously:
```
ffmpeg version 3.4.6-0ubuntu0.18.04.1 Copyright (c) 2000-2019 the FFmpeg developers
  built with gcc 7 (Ubuntu 7.3.0-16ubuntu3)
  configuration: --prefix=/usr --extra-version=0ubuntu0.18.04.1 --toolchain=hardened --libdir=/usr/lib/x86_64-linux-gnu --incdir=/usr/include/x86_64-linux-gnu --enable-gpl --disable-stripping --enable-avresample --enable-avisynth --enable-gnutls --enable-ladspa --enable-libass --enable-libbluray --enable-libbs2b --enable-libcaca --enable-libcdio --enable-libflite --enable-libfontconfig --enable-libfreetype --enable-libfribidi --enable-libgme --enable-libgsm --enable-libmp3lame --enable-libmysofa --enable-libopenjpeg --enable-libopenmpt --enable-libopus --enable-libpulse --enable-librubberband --enable-librsvg --enable-libshine --enable-libsnappy --enable-libsoxr --enable-libspeex --enable-libssh --enable-libtheora --enable-libtwolame --enable-libvorbis --enable-libvpx --enable-libwavpack --enable-libwebp --enable-libx265 --enable-libxml2 --enable-libxvid --enable-libzmq --enable-libzvbi --enable-omx --enable-openal --enable-opengl --enable-sdl2 --enable-libdc1394 --enable-libdrm --enable-libiec61883 --enable-chromaprint --enable-frei0r --enable-libopencv --enable-libx264 --enable-shared
```
Using:
```
sudo add-apt-repository ppa:savoury1/ffmpeg4
sudo add-apt-repository ppa:savoury1/graphics
sudo add-apt-repository ppa:savoury1/multimedia
sudo apt-get update
sudo apt-get install ffmpeg
ffmpeg -version
```
(from http://ubuntuhandbook.org/index.php/2019/08/install-ffmpeg-4-2-ubuntu-18-04/)

Result:
```
ffmpeg version 4.2.2-1ubuntu1~18.04.sav0 Copyright (c) 2000-2019 the FFmpeg developers
built with gcc 7 (Ubuntu 7.5.0-3ubuntu1~18.04)
configuration: --prefix=/usr --extra-version='1ubuntu1~18.04.sav0' --toolchain=hardened --libdir=/usr/lib/x86_64-linux-gnu --incdir=/usr/include/x86_64-linux-gnu --arch=amd64 --enable-gpl --disable-stripping --enable-avresample --disable-filter=resample --enable-avisynth --enable-gnutls --enable-ladspa --enable-libaom --enable-libass --enable-libbluray --enable-libbs2b --enable-libcaca --enable-libcdio --enable-libcodec2 --enable-libflite --enable-libfontconfig --enable-libfreetype --enable-libfribidi --enable-libgme --enable-libgsm --enable-libjack --enable-libmp3lame --enable-libmysofa --enable-libopenjpeg --enable-libopenmpt --enable-libopus --enable-libpulse --enable-librsvg --enable-librubberband --enable-libshine --enable-libsnappy --enable-libsoxr --enable-libspeex --enable-libssh --enable-libtheora --enable-libtwolame --enable-libvidstab --enable-libvorbis --enable-libvpx --enable-libwavpack --enable-libwebp --enable-libx265 --enable-libxml2 --enable-libxvid --enable-libzmq --enable-libzvbi --enable-lv2 --enable-omx --enable-openal --enable-opencl --enable-opengl --enable-sdl2 --enable-libdc1394 --enable-libdrm --enable-libiec61883 --enable-nvenc --enable-chromaprint --enable-frei0r --enable-libx264 --enable-shared
```

Significant: **--enable-libvidstab**, but **--enable-libopencv** is missing!

Maybe i need to compile on my one, for different enabled configurations check
http://www.iiwnz.com/install-ffmpeg-4/


**TODO** Check how to concat two mp3-files (1.mp3, 2.mp3) with fade-in/fade-out the result and an overlay of x seconds, maybe [https://www.ffmpeg.org/ffmpeg-all.html#Audio-Options](https://www.ffmpeg.org/ffmpeg-all.html#Audio-Options)

## imagemagick
[https://imagemagick.org/index.php](https://imagemagick.org/index.php)

Rescale an image of 5184x3888 to 1920x1080 (maintain aspect ratio but crop)
* convert input.jpg -geometry 1920x -quality 100 temp-image.jpg
* convert temp-image.jpg -crop 1920x1080+0+180 -quality 100 output.jpg

Single command
* convert input.jpg -geometry 1920x -crop 1920x1080+0+180 -quality 100 output.jpg

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
