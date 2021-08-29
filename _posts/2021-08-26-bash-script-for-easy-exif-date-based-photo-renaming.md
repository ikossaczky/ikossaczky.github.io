---
layout: post
title: "Bash script for easy exif date based photo renaming"
date: 2021-08-26 22:00:00 +0100
categories: ["bash"]
---

Do you know the situation when you collect photos from several cameras into a single folder, and want to browse them based in chronological order... but your viewer has its own order, most probably the alphabetical one. For exactly this situation I wrote a script which renames the photos in such manner that they will be ordered according to exif date and time (in ideal but common case it is the date and time the photo was created).

### Usage:
`exif-rename [input]`

Possible input:
 - `-h --help`: displays this help
 - `path`: renames jpeg files found under the path so, that the photos are sorted by
	1. "Create Date" if found in exif data of the image,
	2. "Modification Date" or "Modify Date" else.

The new filenames indicate also the respective date and time.

If no input is specified program renames jpeg files in the current directory.

### Download:
You can download the script from my [bash-utils](https://github.com/ikossaczky/bash-utils/blob/master/exif-rename) git repo. 

To use the script you need to have the exiftool program (on manjaro, this is in package [perl-image-exiftool](https://discover.manjaro.org/packages/perl-image-exiftool)).



