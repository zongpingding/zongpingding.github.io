---
title: TeX Font Files
tags: font
categories: LaTeX
date: 2025-08-14 01:00:00
---



## NOTE
TeX just need to know the font metric, characters’ glyph in font is not essential to TeX


## ENC
Defines the mapping between character codes within your LaTeX text and the corresponding character shapes (glyphs) contained within the font file.

ref: https://www.overleaf.com/learn/latex/Questions/I_have_a_custom_font_I%27d_like_to_load_to_my_document._How_can_I_do_this%3F


## TFM/AFM
Font vendors such as Adobe supply metric files for each font, in AFM (Adobe Font Metric) form; these can be converted to TFM (TeX Font Metric) form.
- Alan Jeffrey's `fontinst` package (fonts/utilities/fontinst) is an AFM to TFM converter written in TeX; it is used to generate the files used by LaTeX2e's PSNFSS package to support use of PostScript fonts.
- Rokicki's `afm2tfm`, distributed with dvips (dviware/dvips), is fast and efficient;
- Or use `afmtotfm` in Windows.

TFM files are able to encode only 256 characters per font. Type1 fonts and their AFM format can include unlimited number of characters, the standardized character-names are used in the format.

LuaTeX with luaotfload is able to load Type1 fonts directly without TFM, it reads AFM+PFB files. The standardized names of characters are mapped to Unicode in such case, so user can access all characters of the font, no limited to 256 characters like TFM.

When OTF format is present in a font package then Type1 format (AFM+PFB) seems to be **obsolete** and there is no reason to use such format. Maybe, you want to provide it for users of obsolete TeX engines (like pdftex) where only TFM files are possible when loading the font. And users of such obsolete engines don't need the AFM format.

Note that:
>  - AFM format includes data of kerning pairs. If it is generated automatically, then almost each character to each character has its kerning pair, much of them unnecessary. If you remove such unnecessary kerning pairs from AFM then you get "normal size" of these files. 
>  - For Classic TeX using tfm files the kerning and ligature information is in the ligtable in the tfm file.
>  - In Computer Modern family (METAFONT) such information is described in *.mf (METAFONT) files and used via *.tfm (Table of Font Metric) files.

ref(Copy From):
- https://tex.stackexchange.com/a/694311/294585
- https://tex.stackexchange.com/a/110494/294585
- https://tex.stackexchange.com/a/110487/294585

## PL
`tftopl` is a utility program designed to convert TeX font metric (TFM) files(binary files) into property list (PL) files(Human readable format). And `pltotf` can convert PL files back to TFM.

## PFA/PFB
PFA (Printer Font ASCII) and PFB (PostScript Font Binary/Printer Font Binary) are two formats for Type 1 PostScript fonts.

`PFA` stands for Printer Font ASCII, which is the unpacked version of a PFB file. In `PFB`, the data is stored as-is. In a PFA, any binary data present in a former PFB file is stored as `ascii hex`--meaning each byte of binary data is turned into two ASCII characters representing the hexdecimal value.

ref: https://docs.aspose.com/font/net/what-is-font/pfa-pfb-fonts/


## FD
This is the font discription file, that connects LaTeX NFSS command with the TFM file which generated from the actual font you use.


## MAP
This file defines a series of mappings, maps each font metric file with the physical font.


## PK/GF
A `gf` file which contains a sequence of bitmaps at a specified resolution (that needed to be specified to metafont, usually as part of a printer configuration).

tfm files are read directly by TeX but few programs could read gf fonts directly so they were almost always converted immediately to pk (packed) format which contained the same information in a more compressed form and could be read by dvi drivers such as `dvips` or `xdvi`.

ref: https://tex.stackexchange.com/a/120419/294585

## VF
Virtual fonts provide a means of collecting bits and pieces together to make the glyphs of a font: 

* the bits and pieces may be glyphs from “other” fonts, 
* rules and other “basic” typesetting commands, 
* and the positioning information that specifies how everything comes together.

Virtual fonts provide a way of telling TeX about something more complicated than just a one-to-one character mapping. TeX reads a TFM file of the font, just as before, but the DVI processor will read the VF and use its content to specify how each glyph is to be processed.

Virtual fonts are normally created in a single ASCII VPL (Virtual Property List) file, which includes two sets of information. The vptovf utility will use the VPL file to create the binary TFM and VF files.

ref: https://texfaq.org/FAQ-virtualfonts


## VPL
virtual font property list, that is similar to `PL` file. Use tool `vptovf` to convert it to virtual font.




