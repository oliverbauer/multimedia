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

## convert
https://imagemagick.org/script/convert.php

input: 5184x3888 output: 3840x2160
```sh
convert input.jpg -geometry 3840x -quality 100 output-temp.jpg
convert output-temp.jpg -geometry 3840x -crop 3840x2160+0+360 -quality 100 output.jpg
```
create thumbnails
> for i in *.[jJpPgG][pPnNiI][gGfF]; do convert "$i" -resize %10 small_"$i"; done

```sh
convert input.jpg \
       \( -clone 0 -blur 0x5 -resize 1920x1080\! -fill white -colorize 20% \) \
       \( -clone 0 -resize 1920x1080 \) \
       -delete 0 -gravity center -composite \
       output.jpg
```

## identify
https://imagemagick.org/script/identify.php

Get width, height from an image:
```sh
identify -verbose input.jpg
identify -format "%[fx:w]" input.jpg
identify -format "%[fx:h]" input.jpg
identify -format "%[fx:w]x%[fx:h]" input.jpg
```