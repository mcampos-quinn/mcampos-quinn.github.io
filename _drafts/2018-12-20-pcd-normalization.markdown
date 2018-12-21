---
layout: post
title:  "Digital preservation spelunking: Kodak PhotoCD, PICT, and image file normalization"
date:   2018-12-20
tags: file formats preservation pict tiff imagemagick photocd pict
---

*The original title for this post was going to be "How to quickly normalize PICT files using ImageMagick," but the files (created in 1997!) I was working with turned out to be something much more interesting. I asked a student to rip some image files off of old CD-Rs and stumbled across a digital forensics mystery and learned about a digital image ecosystem I had never heard of! All Jean Painlevé images are copyright [Archives Jean Painlevé](http://jeanpainleve.org/) estate and courtesy BAMPFA.* 

I was working with a student employee this week to get images off a bunch CD-Rs from the early 2000s. Most of these contained film stills used in BAMPFA printed program guides and/or our [website](https://web.archive.org/web/20020622114814/http://www.bampfa.berkeley.edu:80/pfa/old.filmseries/redhollywood/redhollyflyer-images.html), and had been hovering near the bottom of my to-do list for... a long time. One odd outlier, though, was a CD-R marked *Painlevé #6*, which contained about 600MB worth of still frame scans from [Jean Painlevé](https://en.wikipedia.org/wiki/Jean_Painlev%C3%A9) prints. Our student employee called me to her workstation and showed me a bunch of files that were viewable in the Mac Finder and could be opened with Preview, though the controls for zooming and all the options for saving or exporting (except as PDF) were greyed out.

<p style="text-align:center">
	<img src="/images/2018-12-20-pcd-normalization/pict-preview.png" alt="preview of PICT file off the CD-R" style="max-height:400px; "/><br>
	<i>All the controls are greyed out :( sad octopus</i>
</p>

The CD-R itself was unremarkable, but the files indicated that they were made in 1997 and an inkling in the back of my mind warned me that there was bound to be something weird going on here. The folder strucure on the CD looked like this:

* 384 x 256
  * 001 
  * 002 [etc, files listed without extension]
* 3072 x 2048
  * 001 [same files listed]
* Desktop DB [hidden file]
* Desktop DF [hidden file]

<p style="text-align:center">
	<img src="/images/2018-12-20-pcd-normalization/cd-r-preview.png" alt="Finder view of the CD-R" style="max-height:400px; "/><br>
	<i>Note the dates, the previews, and the hidden files!</i>
</p>

I asked the student to rename the extracted files and I thought I'd show her how to use [ImageMagick](http://www.imagemagick.org/script/index.php) (a really powerful tool you should try out if you work with images!) to convert the images to TIFF for ingesting into our digital asset management system. *(DISCLAIMER: I'm going to try not to pretend that I know even a fraction of what ImageMagick can do, or fully grasp the underlying code of the digital forensics involved in this post...)* Here's what came out of that first pass:

<p style="text-align:center">
	<img src="/images/2018-12-20-pcd-normalization/tiff-black.png" alt="all-black tiff file" style="max-height:300px; "/><br>
	<i>I see a black TIFF and I want it painted normal... </i>
</p>

... Hm. Opening the TIFF file in a [hex editor](https://ridiculousfish.com/hexfiend/) (which is a really awesome type of [tool](http://www.av-rd.com/knowhow/data/hexedit_archivists-1.html) I'm just really getting to know, and lets you see the innards of a binary file like an image) showed me that the command `magick convert Painleve_001 Painleve_001.tif` output a TIFF file with 6MB worth of zeroes:

<p style="text-align:center">
	<img src="/images/2018-12-20-pcd-normalization/first-tiff-hex-edit.png" alt="6 megabytes of 00" style="max-height:400px; "/><br>
	<i>... garbage out ...</i>
</p>

I tried all manner of options with my `magick` command, but all with the same result. I reached out to the IM user forum to see if anyone had any suggestions, and also on Twitter to see if #digipres Twitter had any experience with PICT files. Both turned out to be excellent ideas! In the mean time, I started reading up on what exactly the PICT format was, and tried to figure out if maybe the files were just corrupted in some way. 

The [PICT file format](https://en.wikipedia.org/wiki/PICT) orginated in the early 80s as the native "metafile" format (still not totally sure what that means) for Apple's [QuickDraw](https://en.wikipedia.org/wiki/QuickDraw), the graphics core of the pre-OSX Mac operating system. The format was used until well into Mac OSX, which still supports it in a limited capacity (which is why I was able to preview them!), though Mac applications after OSX 10.8 balk at extracting the image data or even opening them (see the QuickDraw article for more details!). PICT was a way for applications to represent graphical information to the GUI using the QuickDraw API, and also perhaps to exchange graphical information between applications. From the [Apple developer documentation](https://web.archive.org/web/20150311163859/https://developer.apple.com/legacy/library/documentation/mac/QuickDraw/QuickDraw-333.html#HEADING333-29): 

> QuickDraw provides a simple set of routines for recording a collection of its drawing commands and then playing the collection back later. Such a collection of drawing commands (as well as its resulting image) is called a picture. A replayed collection of drawing commands results in the picture shown in Figure 7-1.
>
> Figure 7-1 A picture of a party hat
> ![party hat](https://web.archive.org/web/20180324001129im_/https://developer.apple.com/legacy/library/documentation/mac/QuickDraw/graphics/PICT-S-02.jpg)

 Its structure defined [opcodes](https://en.wikipedia.org/wiki/Opcode) (low-level computer instructions) for the representation of vector graphics on-screen, but also for a bunch of other stuff, like bitmapped graphical information, and some types of textual metadata (more on that shortly). Apple had the foresight to include some reserved opcodes that allowed the format to be extended into the 90s, which allowed for things other than vector graphics to be included in the byte stream (and more on that shortly too). For my immediate needs, I learned that version 2 PICT files (introduced in the mid-80s?) have this "[magic number](https://en.wikipedia.org/wiki/Magic_number_(programming)#Magic_numbers_in_files)": `00 11 02 ff 0c 00` at offset 522, they end with `00 FF`, and also have 512 bytes of zeroes at the file header. Looking at my PICTs with the hex editor confirmed that they were really PICT files and that they seemed fully kosher. What else could be going on here? 

<p style="text-align:center">
	<img src="/images/2018-12-20-pcd-normalization/pict-hex-view-header.png" alt="hex view of a PICT" style="max-height:400px; "/><br>
	<i>512-byte empty header, plus the magic number, plus some extras</i>
</p>

One of the first clues to how deep this goes is in the above image. Buried in the PICT metadata is the phrase "Photo YCC RGB Standard," which a Google search later revealed to be a colorspace developed by Kodak for compression with chroma subsampling (plot=thickened). There is also a phrase at the end of the file noting that "Quicktime(tm) and a Photo CD Decompressor are needed to use this picture." OK, QuickTime I get as a 90s-era requirement, but WTF is a Photo CD Decompressor? Here was another clue--running `exiftool -verbose` on one of the files revealed a DEEP set of tags with lots of crazy information, including `Tag 0x8200, CompressedQuickTime (CompressedQuickTime):` followed by a lot of image metadata including `compressor (string[31]) = Photo CD`. 

I was busy ignoring these clues, but at about this point, Kieran O'Leary had reached out on Twitter to one of the FFmpeg developers, Paul Mahol, who definitively identified what was going on here. We struck gold!

<p style="text-align:center">
	<img src="/images/2018-12-20-pcd-normalization/photocd.png" alt="Paul Mahol to the rescue" style="max-height:400px; "/><br>
	<i>mind=blown</i>
</p>

I had never even heard of that file format, so suddenly all the tidbits I had seen with the various tools I had tried started to fall into place. But I still couldn't figure out quite what to do with the images. Moving the string `PCD_IPI`, which I could see in the hex editor, to the correct position (PhotoCD [expects](http://fileformats.archiveteam.org/wiki/Photo_CD#Identification) the string at byte 2048 to constitute its magic number), and also deleting all the extraneous PICT bytes resulted in a file that registered according to exiftool as a PhotoCD format file. I was also able to get ImageMagick to convert the resulting file to a PNG (another raster graphics format with lossless compression, more on that later!) if I added a ".pcd" extension. But working through over 100 images like this didn't seem feasible or appealing.

What the heck was a PhotoCD in the first place though?? 

<!-- ![a photo cd](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b0/Kodak_photo_cd_package.jpg/660px-Kodak_photo_cd_package.jpg) -->

<p style="text-align:center">
	<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/b/b0/Kodak_photo_cd_package.jpg/660px-Kodak_photo_cd_package.jpg" alt="a photo cd" style="max-height:400px; "/><br>
	<i>A photo cd. Public domain image from Wikipedia.</i>
</p>

It turns out that in the early 90s Kodak developed its own universe of film scanning and digital image display, including everything from proprietary scanners and CD players to custom colorspaces and encoding algorithms. You really have to admire the grandiosity of Kodak's vision. In this scheme, photo labs and consumers (evidently a pro-sumer intended user?) would all partake in the Kodak walled garden for digital images. People were supposed to take their exposed 35mm film to a PhotoCD-equipped lab, which would develop and scan the negatives for them, and return a CD with up to 100 (!) images available at several resolutions, with the master scan done at 2200 ppi. Included within each PCD file were each of these derivatives along with metadata about the image, the scanner model and date, etc. WOW! So weird. You were then supposed to look at the images on a TV screen using a proprietary (of course) CD player equipped to read the encoded files. The quality of the images could be quite high (FADGI [recommends](http://www.digitizationguidelines.gov/guidelines/digitize-technical.html) 2000 ppi for "two-star" scans of 35mm negatives), and I'm really curious about the consumer opinion of this system at the time (how many people were able to use files like this at the time, and how did they use them?). Ultimately the system seems to have been overtaken by the explosion of cheaper digital camera technology around 2000 (and according to Wikipedia, problems with color rendering and also the preponderance of JPEG and PNG as more open and easier standards by the late 90s). I was alive and well in the 90s and I had never heard of the format, so.... go figure.

I also started doing some reading and found references on an [Corel user forum](https://groups.google.com/forum/m/#!topic/corelsupport.photo-paint8/pbtwY7AoWAo) (from 1998!) and on a PhotoCD enthusiast's [personal site](http://tedfelix.com/PhotoCD/PCDBasics.html) to some weird stuff that happened when you tried to open a Photo CD on a Mac. 

Meanwhile on the ImageMagick forum, someone humbly recommended this little utility he wrote that was able to read


https://github.com/ImageMagick/ImageMagick/issues/970
https://en.wikipedia.org/wiki/PICT

https://en.wikipedia.org/wiki/Magic_number_(programming)#Magic_numbers_in_files

http://tedfelix.com/PhotoCD/PCDAdvanced.html



Kodak's Photo CD and Proposed Photo YCC Color Standard.
Urrows, Henry; Urrows, Elizabeth
Computers in Libraries, v11 n4 p16-19 Apr 1991

https://web.archive.org/web/20150311163901/https://developer.apple.com/legacy/library/documentation/mac/QuickDraw/QuickDraw-334.html#HEADING334-90

https://web.archive.org/web/20150311163859/https://developer.apple.com/legacy/library/documentation/mac/QuickDraw/QuickDraw-333.html#HEADING333-29

https://web.archive.org/web/20070106233500/http://www.kodak.com:80/global/en/service/professional/products/ekn017045.jhtml?CID=go&idhbx=photocd

http://fileformats.archiveteam.org/wiki/QTIF
http://fileformats.archiveteam.org/wiki/Photo_CD
http://fileformats.archiveteam.org/wiki/PICT

apple pict documentatnion pdf 80s
http://show.docjava.com/posterous/file/2012/07/9614410-DOC01.PDF