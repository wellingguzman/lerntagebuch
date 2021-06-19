---
title: Welcome 
datetime: 2020-05-21T23:46:54Z
tags: learning bash
---
Welcome, this is my learning journal. The goal of this journal is to have a public place where I can write about my learning activities in a infrequently manner.

To start this learning journal, the first thing I tried to learn was to make a simple site generator in bash. That script was used to build this page. I didn't make so many notes because I was expecting to write one, but let the code speak a bit for itself.
<!--more-->

It wasn't easy to create and at one point when things started to get uphill, I just stopped trying to make this in a "perfect" way, and starting to dump code just to make it work and this is how the first idea came on, not perfect, but actually working which was the intention.

The code has some duplication code, and maybe not the best bash script out there. I tried to create this without following any tutorial or copying another generator, but I peek into the code of some projects to see how they do it, and I started from there just recalling how it did it, not making an exact copy and searching on google A LOT.

I learned to use `man` and `grep` to find information in the `man` command, e.g. `man date | grep "format"`.

I learned another thing, basically all the things in this code are just re-learned or learned for the first time. I was trying to remove the newline from the css when appending into the document, without no luck, tried `sed` with `[[:space]]`, didn't work because it removes the spaces too, yeah I know. I tried `[\n\r\t]` but it removes the `n`, `r`, and `t` characters from the styles.

Then I remember, hey, early while working on this, there was a thing that  was annoying me, and it was `echo` removing newlines that I didn't want it to remove. Using `echo` without double quote remove the newlines. so there's that, I solve my issue without any regex or anything.

	# From this
	echo "<style>$styles</style>" >> "$tmp"

	# To this
	echo "<style>" >> "$tmp"
	echo $styles >> "$tmp"
	echo "</style>" >> "$tmp"

Another thing I wanted to create was a variable that holds separate values to act like a struct/object, so I used an array, but there's something you have to keep in mind, if the variable is empty the value is not set in that position in the array, and ignored. For example, you want an array to always have three elements, each position must be wrapped in double-quote otherwise if the first variable is empty, the second variable now is going to be the first element, and the second will be defined by a third variable.

	name="image"
	ext=""
	filename="$name$ext"
	list=($name $ext $filename)

If we want to `list[1]` to always be the extension value, but because `$ext` is empty that position is not set by `$ext`, but by `$filename`, to solve this each variable must be wrapped into double-quote.

	name="image"
	ext=""
	filename="$name$ext"
	list=("$name" "$ext" "$filename")

One difficult thing that I didn't find a way to do it properly was to sort the files not based on the filename, by including the datetime, but on an the datetime attribute inside the file content. I wanted to create an array of objects that I could create from the first files iteration, then sort accordingly, but that's something easy to do in high level languages, in bash I didn't find anything to do it that I could think I know what was going on exactly. So I ended up creating temporary files with the date part of the filename, then sort them, this way I keep the original file without datetime in its file, but then I had to re read again the files, that's the drawback from getting my ideal structure.

I wanted to make this script barely functional first, then just fix things along the way (if I ever keep using it much), and add things that will make it easier for me to add notes. Because this static site generator was created with the purpose of learning and just create something.

Back to the roots of having fun and enjoy making something. If interested the code available in [github](https://github.com/wellingguzman/b.sh).
