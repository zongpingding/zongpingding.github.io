---
title: TrueType Font in pdfTeX - II
tags: font
categories: LaTeX
date: 2024-09-29 02:00:00
---

{% note danger %}
There is something wrong with current article and **do not read it**. I may fixed later.
{% endnote %}


## Preface
we have write a blog about using TrueType Font under pdfTeX in `TeX` in the [previous article](https://zongpingding.github.io/2024/09/27/pdftex_ttf/). Here we will continue to discuss the details of using TrueType Font under pdfTeX. And the configuration under `LaTeX`.

This blog will be totally copy from the blog:[Using TrueType fonts with TeX and pdfTeX](http://www.radamir.com/tex/ttf-tex.htm). There will be some more information during the process and some error correction for the original blog. 

At the same time, i refer to the blog: [using TrueType Fonts with TexLive pdftex pdflatex](https://www.monperrus.net/martin/using-truetype-fonts-with-texlive-pdftex-pdflatex) as well.

> * **Why i make a copy of this blog** ? Just in case of the original blog is not available in the future. The author of the original blog says that: "This note describes how to add TrueType fonts support to TeX and LaTeX. This information is provided for educational purpose only. I do not know if procedures described here violate any copywrite/license agreement. Also, names ectimes and `winfonts` are used just for example. I do not know how they should be named correctly."
> * All the following commands are running in `Powershell` in Windows 11. TeXLive 2024 is installed in the system. 
> * Assume that `PC` is the username of the computer.


## Before we begin (TeX)
Suppose, we want to typeset in Times New Roman instead of Computer Modern Roman (cmr). Times New Roman in Windows 9x/NT 4 is a Unicode font which has almost all symbols from Computer Modern. Certainly, they are in different places. To use these fonts in TeX (LaTeX) and PDF documents, it is necessary to create two more fonts: raw tex font, containing necessary characters but without kerning information, and virtual tex font with kerns and ligatures. For simplicity, we’ll use the rule that if TrueType font file has a name  `foo.ttf`, than virtual tex font in `T1` encoding will be named `ecfoo` and raw tex font will be named recfoo. Times New Roman family of fonts is presented by the files `times.ttf`, `timesi.ttf`, `timesbd.ttf` and `timesbi.ttf`, found in `c:\windows\fonts` directory in Windows 9x or `c:\winnt\fonts` in Windows NT. The family has Regular, Italic, Bold and BoldItalic members. We’ll also create oblique (slanted) versions of Regular and Bold members, which will be marked by letter “o” at the end of the name. The above is summarized in a table:

|             | TrueType File Name (`.ttf`) | TeX Name (virtual font; `.vf`, `.tfm`) | Raw TeX Name (`.tfm`, `.afm`, `.pk`) |
| ----------- | --------------------------- | -------------------------------------- | ------------------------------------ |
| Regular     | times                       | ectimes                                | rectimes                             |
| Italic      | timesi                      | ectimesi                               | rectimesi                            |
| Bold        | timesbd                     | ectimesbd                              | rectimesbd                           |
| BoldItalic  | timesbi                     | ectimesbi                              | rectimesbi                           |
| Oblique     | -                           | ectimeso                               | rectimeso                            |
| BoldOblique | -                           | ectimesbdo                             | rectimesbdo                          |


Also we’ll need an encoding vector—a file which determines characters positions in a new fonts. We’ll use a file [T1-WGL4.enc](http://www.radamir.com/tex/T1-WGL4.enc) which corresponds to T1 encoding.

> To be able to typeset with Cyrillic characters, you should use `T2A-mod0.enc` file or similar, found in T2 package.

Create a temporary working directory and copy font files ( `times.ttf`, `timesbd.ttf`, `timesbi.ttf`, `timesi.ttf` from `c:\windows\fonts` ) and encoding file (`T1-WGL4.enc`) there. Now we are ready for actual work.


## Creating TeX Font Metrics (tfm)
Change to the working directory and run the following commands:

```shell
ttf2tfm times.ttf -q -T T1-WGL4.enc -v ectimes.vpl rectimes.tfm >> ttfonts.map
ttf2tfm timesi.ttf -q -T T1-WGL4.enc -v ectimesi.vpl rectimesi.tfm >> ttfonts.map
ttf2tfm timesbd.ttf -q -T T1-WGL4.enc -v ectimesbd.vpl rectimesbd.tfm >> ttfonts.map
ttf2tfm timesbi.ttf -q -T T1-WGL4.enc -v ectimesbi.vpl rectimesbi.tfm >> ttfonts.map
```

When running these commands, you may get some warning messages:
```shell
ttf2tfm: WARNING: Cannot find character `compwordmark'
         specified in input encoding.
ttf2tfm: WARNING: Cannot find character `perthousandzero'
         specified in input encoding.
ttf2tfm: WARNING: Cannot find character `dotlessj'
         specified in input encoding.
ttf2tfm: WARNING: Cannot find character `ff'
         specified in input encoding.
ttf2tfm: WARNING: Cannot find character `ffi'
         specified in input encoding.
ttf2tfm: WARNING: Cannot find character `ffl'
         specified in input encoding.
ttf2tfm: WARNING: Cannot find character `visualspace'
         specified in input encoding.
ttf2tfm: WARNING: Character `hyphen' encoded twice in input encoding
         (positions 2d and 7f; the latter is ignored).
ttf2tfm: WARNING: Cannot map character `compwordmark'
         specified in output encoding.
ttf2tfm: WARNING: Cannot map character `perthousandzero'
         specified in output encoding.
ttf2tfm: WARNING: Cannot map character `dotlessj'
         specified in output encoding.
ttf2tfm: WARNING: Cannot map character `ff'
         specified in output encoding.
ttf2tfm: WARNING: Cannot map character `ffi'
         specified in output encoding.
ttf2tfm: WARNING: Cannot map character `ffl'
         specified in output encoding.
ttf2tfm: WARNING: Cannot map character `visualspace'
         specified in output encoding.
```

This will create a tfm files of raw fonts and vpl files of virtual fonts. To generate slanted versions of regular and bold font, command should be extended like this:
```shell
ttf2tfm times.ttf -q -T T1-WGL4.enc -s .167 -v ectimeso.vpl rectimeso.tfm >> ttfonts.map
ttf2tfm timesbd.ttf -q -T T1-WGL4.enc -s .167 -v ectimesbdo.vpl rectimesbdo.tfm >> ttfonts.map
```


Then your project directory should contain the following files:

```shell
.
├── ectimes.vpl
├── ectimesbd.vpl
├── ectimesbdo.vpl
├── ectimesbi.vpl
├── ectimesi.vpl
├── ectimeso.vpl
├── rectimes.tfm
├── rectimesbd.tfm
├── rectimesbdo.tfm
├── rectimesbi.tfm
├── rectimesi.tfm
├── rectimeso.tfm
├── T1-WGL4.enc
├── times.ttf
├── timesbd.ttf
├── timesbi.ttf
├── timesi.ttf
└── ttfonts.map
```


## Creating Virtual Fonts (vf)
Run commands: 
```shell
vptovf ectimes.vpl ectimes.vf ectimes.tfm
vptovf ectimesi.vpl ectimesi.vf ectimesi.tfm
vptovf ectimesbd.vpl ectimesbd.vf ectimesbd.tfm
vptovf ectimesbi.vpl ectimesbi.vf ectimesbi.tfm
vptovf ectimeso.vpl ectimeso.vf ectimeso.tfm
vptovf ectimesbdo.vpl ectimesbdo.vf ectimesbdo.tfm
```

You will see such log when generating the virtual fonts:
```shell
I had to round some heights by 12.0000000 units.
I had to round some depths by 1.0000000 units.
I had to round some italic corrections by 0.5000000 units.
```

This creates vf and tfm files for virtual fonts. After that you can delete vpl files, just run command:
```shell
rm -Force *.vpl
```

to delete them all. Now your project directory should contain the following files:

```shell
.
├── ectimes.tfm
├── ectimes.vf
├── ectimesbd.tfm
├── ectimesbd.vf
├── ectimesbdo.tfm
├── ectimesbdo.vf
├── ectimesbi.tfm
├── ectimesbi.vf
├── ectimesi.tfm
├── ectimesi.vf
├── ectimeso.tfm
├── ectimeso.vf
├── rectimes.tfm
├── rectimesbd.tfm
├── rectimesbdo.tfm
├── rectimesbi.tfm
├── rectimesi.tfm
├── rectimeso.tfm
├── T1-WGL4.enc
├── times.ttf
├── timesbd.ttf
├── timesbi.ttf
├── timesi.ttf
└── ttfonts.map
```


## Installation of fonts for TeX and LaTeX
Now vf and tfm files should be moved to the place where TeXLive can find them. Move all vf files to `<localtexmf>\fonts\vf\ms\times\`, and all tfm files to `<localtexmf>\fonts\tfm\ms\times\`. What is the path of `<localtexmf>` ? In Linux this path is usually at:
```shell
~/texmf/tex/latex/
```

For Windows, the `<localtexmf>` directory is usually at:
```shell
C:\Users\PC\texmf\tex\latex\
```

To move these files, run the following commands:
```shell
mv *.vf C:\Users\PC\texmf\fonts\vf\ms\times
mv *.tfm C:\Users\PC\texmf\fonts\tfm\ms\times
```

> The directories `ms\times` are not mandatory, you can leave the files even in parent dirs, but this way it is easier to maintain them and it corresponds to the general rule of placement: `<supplier>\<family>`.


Now your project directory should contain the following files:
```shell
.
├── T1-WGL4.enc
├── times.ttf
├── timesbd.ttf
├── timesbi.ttf
├── timesi.ttf
└── ttfonts.map
```

---

Open the file `ttfonts.map` in a temporary working directory with your favorite text editor (`notepad`, `vscode`, `vim` etc) and copy its contents to clipboard. `ttfonts.map` contains:
```shell
rectimes   times.ttf Encoding=T1-WGL4.enc
rectimesi   timesi.ttf Encoding=T1-WGL4.enc
rectimesbd   timesbd.ttf Encoding=T1-WGL4.enc
rectimesbi   timesbi.ttf Encoding=T1-WGL4.enc
rectimeso   times.ttf Slant=.167 Encoding=T1-WGL4.enc
rectimesbdo   timesbd.ttf Slant=.167 Encoding=T1-WGL4.enc
```

Now open a file `C:\texlive\2024\tlpkg\dviout\map\ttfonts.map` with the same editor and paste the lines from clipboard and save it. The added part looks like:
```shell
wnsri8r		"Times New Roman"		italic
wcpbs8r		"Copperplate Gothic Bold"
wcpls8r		"Copperplate Gothic Light"
% <before part>

%%%%%%%%%%%%%%%  My Own TTF and OTF Config %%%%%%%%%%%%%%%
rectimes   times.ttf Encoding=T1-WGL4.enc
rectimesi   timesi.ttf Encoding=T1-WGL4.enc
rectimesbd   timesbd.ttf Encoding=T1-WGL4.enc
rectimesbi   timesbi.ttf Encoding=T1-WGL4.enc
rectimeso   times.ttf Slant=.167 Encoding=T1-WGL4.enc
rectimesbdo   timesbd.ttf Slant=.167 Encoding=T1-WGL4.enc

% <after part>
%input	pttfonts.map
%input	morisawa.map
```

> You may need to run the editor as **administrator** to be able to save the file.

---

This is sufficient for generating DVI file, but to view or print it, there should be a raster `pk` font. TeXLive will attempt to generate it automatically with new utility: `ttf2pk`. For successful generation ttf2pk should find both TrueType font and encoding file. 
* It happens that ttf2pk can find ttf files, if they are installed and are in a system fonts directory, 
* and it looks for encoding files in a directory `C:\texlive\2024\texmf-dist\fonts\enc\ttf2pk\base` (among others).Therefore if there is no such encoding file, you have to copy `T1-WGL4.enc` to `C:\texlive\2024\texmf-dist\fonts\enc\ttf2pk\base`.

> In TeXLive 2024, there may be already a `T1-WGL4.enc` file in the directory `C:\texlive\2024\texmf-dist\fonts\enc\ttf2pk\base`, so you don't need to copy it again.

Now we have to update databases, open the `Powershell` as **administrator** and run the following commands:
```shell
texhash 
```

## Testing the installation in TeX (don't skip this step)
Change current directory from temporary working to some other where there are no files related to fonts creation. To have a look at our brand new fonts, run a command
```shell 
❯ tex testfont
This is TeX, Version 3.141592653 (TeX Live 2024) (preloaded format=tex)
(c:/texlive/2024/texmf-dist/tex/plain/knuth-lib/testfont.tex

Name of the font to test = ectimes
Now type a test command (\help for help):)
*\table\eject\init
[1]
Name of the font to test = ectimeso
Now type a test command (\help for help):
*\table\eject\init
[2]
Name of the font to test = ectimesbd
Now type a test command (\help for help):
*\table\eject\init
[3]
Name of the font to test = ectimesi
Now type a test command (\help for help):
*\table\eject\init
[4]
Name of the font to test = ectimesbd
Now type a test command (\help for help):
*\table\eject\init
[5]
Name of the font to test = ectimesbdo
Now type a test command (\help for help):
*\bye
[6]
Output written on testfont.dvi (6 pages, 53556 bytes).
Transcript written on testfont.log.
```

It will ask about a font to test. Enter "`ectimes`". It will ask about a command. Enter "`\table\eject\init`". This time enter "`ectimeso`", then "`ectimesbd`" and so on. After all fonts are tested, enter a command "`\bye`". This will create a file `testfont.dvi` in current directory.

> If you have problems at this stage, it is either wrong font names or tfm, vf files placement.

But **do not hurry** to open `testfont.dvi` with YAP. If something goes wrong, it is quite hard to trace the problem in YAP. Better try converting dvi to PostScript:
```shell 
dvips testfont.dvi
```

> `YAP` is a viewer for DVI files in the past. Nowadays you should use `dviout.exe` which has already contained in TeXLive.

Here you will have plenty of messages about what is happening. In particular, the generation of pk for TrueType will look like this:
```shell
❯ ttf2pk.exe -q rectimes 600
ttf2pk: WARNING: Cannot map character `compwordmark'.
ttf2pk: WARNING: Cannot map character `perthousandzero'.
ttf2pk: WARNING: Cannot map character `dotlessj'.
ttf2pk: WARNING: Cannot map character `ff'.
ttf2pk: WARNING: Cannot map character `ffi'.
ttf2pk: WARNING: Cannot map character `ffl'.
ttf2pk: WARNING: Cannot map character `visualspace'.
ttf2pk: WARNING: Cannot map character `Germandbls'.
```

If the previous file location or file name is wrong, that causes `mktexpk` can not find the now font `rectimes`, you will see the following log:
```shell
❯ dvips testfont.dvi
This is dvips(k) 2024.1 (TeX Live 2024)  Copyright 2024 Radical Eye Software (www.radicaleye.com)
' TeX output 2024.09.29:2035' -> testfont.ps

kpathsea: Running mktexpk --mfmode ljfour --bdpi 600 --mag 1+0/600 --dpi 600 rectimes

The command name is C:\texlive\2024\bin\windows\mktexpk

kpathsea: Running mktexmf rectimes.mf

The command name is C:\texlive\2024\bin\windows\mktexmf
name = rectimes, rootname = rectimes, pointsize =
mktexmf: empty or non-existent rootfile!
Cannot find rectimes.mf .
I try ps2pk --> gsftopk --> ttf2pk --> hbf2gf.
ps2pk cannot be used.
I try gsftopk.
gsftopk.exe rectimes 600
Cannot find font rectimes in map file(s).
gsftopk cannot be used.
Next I try ttf2pk.
ttf2pk.exe -q rectimes 600
ttf2pk failed.
Finally I try hbf2gf.
hbf2gf.exe -q -p rectimes 600
All trials failed.
kpathsea: Appending font creation commands to missfont.log.
C:\texlive\2024\bin\windows\dvips.exe: PK font rectimes not found; using cmr10
<c:/Users/PC/.texlive2024/texmf-var/fonts/pk/ljfour/public/cm/cmr10.600pk>
C:\texlive\2024\bin\windows\dvips.exe: ! [scanpage] invalid char 128 from font rectimes
```

## Using new fonts in TeX
create a simple tex source file `test.tex`:
```latex 
\font\myfont=ectimes
\font\mybigfont=ectimes at 36pt
\myfont Hello, I am being typeset in Times New Roman

\mybigfont Me too...
\bye
```

Compile it with `pdftex`:
```shell
pdftex test.tex
```

The the log should be like:
```shell
This is pdfTeX, Version 3.141592653-2.6-1.40.26 (TeX Live 2024) (preloaded format=pdftex)
 restricted \write18 enabled.
entering extended mode
(./test.tex [1{c:/texlive/2024/texmf-var/fonts/map/pdftex/updmap/pdftex.map}] )

kpathsea: Running mktexpk --mfmode / --bdpi 600 --mag 3+360/600 --dpi 2160 rectimes

The command name is C:\texlive\2024\bin\windows\mktexpk

kpathsea: Running mktexmf rectimes.mf

The command name is C:\texlive\2024\bin\windows\mktexmf
name = rectimes, rootname = rectimes, pointsize =
mktexmf: empty or non-existent rootfile!
Cannot find rectimes.mf .
I try ps2pk --> gsftopk --> ttf2pk --> hbf2gf.
ps2pk cannot be used.
I try gsftopk.
gsftopk.exe rectimes 2160
Cannot find font rectimes in map file(s).
gsftopk cannot be used.
Next I try ttf2pk.
ttf2pk.exe -q rectimes 2160
ttf2pk: WARNING: Cannot map character `compwordmark'.
ttf2pk: WARNING: Cannot map character `perthousandzero'.
ttf2pk: WARNING: Cannot map character `dotlessj'.
ttf2pk: WARNING: Cannot map character `ff'.
ttf2pk: WARNING: Cannot map character `ffi'.
ttf2pk: WARNING: Cannot map character `ffl'.
ttf2pk: WARNING: Cannot map character `visualspace'.
ttf2pk: WARNING: Cannot map character `Germandbls'.
mktexupd failed
 <c:/Users/PC/.texlive2024/texmf-var/fonts/pk/modeless/ms/times/rectimes.2160pk
>
kpathsea: Running mktexpk --mfmode / --bdpi 600 --mag 1+0/600 --dpi 600 rectimes

The command name is C:\texlive\2024\bin\windows\mktexpk

kpathsea: Running mktexmf rectimes.mf

The command name is C:\texlive\2024\bin\windows\mktexmf
name = rectimes, rootname = rectimes, pointsize =
mktexmf: empty or non-existent rootfile!
Cannot find rectimes.mf .
I try ps2pk --> gsftopk --> ttf2pk --> hbf2gf.
ps2pk cannot be used.
I try gsftopk.
gsftopk.exe rectimes 600
Cannot find font rectimes in map file(s).
gsftopk cannot be used.
Next I try ttf2pk.
ttf2pk.exe -q rectimes 600
ttf2pk: WARNING: Cannot map character `compwordmark'.
ttf2pk: WARNING: Cannot map character `perthousandzero'.
ttf2pk: WARNING: Cannot map character `dotlessj'.
ttf2pk: WARNING: Cannot map character `ff'.
ttf2pk: WARNING: Cannot map character `ffi'.
ttf2pk: WARNING: Cannot map character `ffl'.
ttf2pk: WARNING: Cannot map character `visualspace'.
ttf2pk: WARNING: Cannot map character `Germandbls'.
mktexupd failed
 <c:/Users/PC/.texlive2024/texmf-var/fonts/pk/modeless/ms/times/rectimes.600pk
><c:/texlive/2024/texmf-dist/fonts/type1/public/amsfonts/cm/cmr10.pfb>
Output written on test.pdf (1 page, 17004 bytes).
Transcript written on test.log.
```

The second time you run it, you will see the following log:
```shell
This is pdfTeX, Version 3.141592653-2.6-1.40.26 (TeX Live 2024) (preloaded format=pdftex)
 restricted \write18 enabled.
entering extended mode
(./test.tex [1{c:/texlive/2024/texmf-var/fonts/map/pdftex/updmap/pdftex.map}] )
 <c:/Users/PC/.texlive2024/texmf-var/fonts/pk/modeless/ms/times/rectimes.2160pk
> <c:/Users/PC/.texlive2024/texmf-var/fonts/pk/modeless/ms/times/rectimes.600pk
><c:/texlive/2024/texmf-dist/fonts/type1/public/amsfonts/cm/cmr10.pfb>
Output written on test.pdf (1 page, 17004 bytes).
Transcript written on test.log.
```

and you will get the pdf `test.pdf`. But the pdf looks a little bit weird, see below:
![bitmap font in pdf](/img/pdftex_ttf_II/pdftex_ttf_II_bitmap_font.png)


## Using new fonts in LaTeX
LaTeX requires some additional efforts. Create a font definition file `t1tnr.fd` in `<localtexmf>\tex\latex\winfonts` directory with the following contents:
```latex 
\ProvidesFile{t1tnr.fd}[Put your description of font here]

\DeclareFontFamily{T1}{tnr}{}

\DeclareFontShape{T1}{tnr}{b}{n}{<->ectimesbd}{}
\DeclareFontShape{T1}{tnr}{b}{sl}{<-> ectimesbdo}{}
\DeclareFontShape{T1}{tnr}{b}{it}{<-> ectimesbi}{}

\DeclareFontShape{T1}{tnr}{m}{n}{<-> ectimes}{}
\DeclareFontShape{T1}{tnr}{m}{sl}{<-> ectimeso}{}
\DeclareFontShape{T1}{tnr}{m}{it}{<-> ectimesi}{}

\DeclareFontShape{T1}{tnr}{bx}{n}{<->ssub * tnr/b/n}{}
\DeclareFontShape{T1}{tnr}{bx}{sl}{<->ssub * tnr/b/sl}{}
\DeclareFontShape{T1}{tnr}{bx}{it}{<->ssub * tnr/b/it}{}

\endinput
```

Now you can use new fonts in LaTeX like the following:

```latex
\documentclass{article}
\begin{document}
\usefont{T1}{tnr}{m}{sl} 

Hello, I am being typeset in Times New Roman Slanted 

\end{document}
```

Finally, if you want to use Times New Roman as you default roman font, put two `\renewcommand` command in preamble:
```latex
\documentclass{article}

\renewcommand{\encodingdefault}{T1}
\renewcommand{\rmdefault}{tnr}

\begin{document}

Hello, I am being typeset in \textsl{Times New Roman Slanted} 

\end{document}
```

Now you can try use pdftex and pdflatex to create PDF documents but new fonts will be embeded as bitmaps which makes the whole pdf-story half useless. Let's see how to embed TrueType fonts in PDF documents in native format.


## Before we begin (pdfTeX)
First you shoulld check that your pdfTeX is up and running. A command
```shell
pdftex story
```

will give the following screen messages:
```shell
C:\texmf>pdftex story
This is pdfTeX, Version 3.14159-12r (MiKTeX 1.11c)
(pdftex.cfg) (story.tex [1<cm.map>])
*
```

Enter a command \bye and you should get

```shell
*\bye
<cmtext.enc><cmr10.pfb><cmsl10.pfb><cmbx10.pfb>
Output written on story.pdf (1 page, 31672 bytes).
Transcript written on story.log.
```

There are two important things in these lines. First is (`pdftex.cfg`) and <`cm.map`>, which means that pdftex has found its configuration file and loaded the Computer Modern fonts map. Second is <`cmtext.enc`><`cmr10.pfb`>..., which means that Type 1 PostScript fonts CMR10, CMSL10 and CMBX10 were included in document along with encoding file.

## Creating Adobe Font Metrics (afm)
To create afm files, go back to temporary working directory and run the following commands:
```latex 
ttf2afm -e T1-WGL4.enc -o rectimes.afm times.ttf
ttf2afm -e T1-WGL4.enc -o rectimesi.afm timesi.ttf
ttf2afm -e T1-WGL4.enc -o rectimesbd.afm timesbd.ttf
ttf2afm -e T1-WGL4.enc -o rectimesbi.afm timesbi.ttf
```

The program will complain it can not find few characters but will create desired afm files.

## Gathering map file
Now we have to create a map file whiich will map raw tex font names to actual TrueType names. Though this can be done by copying necessary strings from afm files, it also can be accomplished by afm2tfm program. As the name implies, it creates tfm files whcih we already have. But a side effect of this proram is outputting a string which is necessary for map file. Run the following commands:
```latex 
afm2tfm rectimes.afm -T T1-WGL4.enc rectimes.tfm >>winfonts.map
afm2tfm rectimesi.afm -T T1-WGL4.enc rectimesi.tfm >>winfonts.map
afm2tfm rectimesbd.afm -T T1-WGL4.enc rectimesbd.tfm >>winfonts.map
afm2tfm rectimesbi.afm -T T1-WGL4.enc rectimesbi.tfm >>winfonts.map
afm2tfm rectimes.afm -T T1-WGL4.enc -s .167 rectimeso.tfm >>winfonts.map
afm2tfm rectimesbd.afm -T T1-WGL4.enc -s .167 rectimesbdo.tfm >>winfonts.map
```

Open winfonts.map file for editing and add near the end of each line a name of the corresponding ttf file :

```latex 
rectimes TimesNewRomanPSMT " T1Encoding ReEncodeFont " <times.ttf T1-WGL4.enc
rectimesi TimesNewRomanPS-ItalicMT " T1Encoding ReEncodeFont " <timesi.ttf T1-WGL4.enc
rectimesbd TimesNewRomanPS-BoldMT " T1Encoding ReEncodeFont " <timesbd.ttf T1-WGL4.enc
rectimesbi TimesNewRomanPS-BoldItalicMT " T1Encoding ReEncodeFont " <timesbi.ttf T1-WGL4.enc
rectimeso TimesNewRomanPSMT " .167 SlantFont T1Encoding ReEncodeFont " <times.ttf T1-WGL4.enc
rectimesbdo TimesNewRomanPS-BoldMT " .167 SlantFont T1Encoding ReEncodeFont " <timesbd.ttf T1-WGL4.enc
```


## Installation of fonts for pdfTeX and pdfLaTeX
Create a directory in localtexmf tree `<localtexmf>\fonts\afm\ms\times` and move all afm files there.

Create a directory in localtexmf tree `<localtexmf>\pdftex\base` and move `winfonts.map` file there.

Edit the file `c:\texmf\miktex\config\miktex.ini`: find the line which starts with "`TrueType Font Dirs=`" and add windows fonts directory:

```latex
TrueType Font Dirs=.;%R\fonts\truetype//;c:\windows\fonts
```

Open `c:\texmf\pdftex\base\pdftex.cfg` file for editing and add the following line:

```latex
map +winfonts.map
```

As you can see, there are a lot of other map files available in this dir, but most of them require that you additionaly purchase font files.

Now we have to update databases:

```shell 
Start=>Programs=>MiKTeX=>Refresh databases
```


## Testing the installation in pdfTeX
Change current directory from temporary working to some other where there are no files related to fonts creation and run a command
```latex 
pdftex testfont
```

It will ask about a font to test. Enter "`ectimes`". It will ask about a command. Enter "`\table\eject\init`". This time enter "`ectimeso`", then "`ectimesbd`" and so on. After all fonts are tested, enter a command "`\bye`". Screen output will be like this:

```shell
pdftex testfont
This is pdfTeX, Version 3.14159-12r (MiKTeX 1.11c)
(pdftex.cfg) (testfont.tex
Name of the font to test = ectimes
Now type a test command (\help for help):)
*\table\eject\init
[1<cm.map><winfonts.map>]
Name of the font to test = ectimesbdo
Now type a test command (\help for help):
*\table\bye
[2]<T1-WGL4.enc><timesbd.ttf><cmttext.enc><cmtt10.pfb><times.ttf><cmitext.enc><
cmti10.pfb><T1-WGL4.enc><cmr10.pfb><cmr7.pfb>
Output written on testfont.pdf (2 pages, 195888 bytes).
Transcript written on testfont.log. 
```

As you can see, this time pdftex has found `winfonts.map` and embeded encodingg file `T1-WGL4.enc` and TrueType fonts `times.ttf` and `timesbd.ttf` into resulting PDF document. Open `testfont.pdf` with GSview and you will see:

Adobe Acrobat users, please read a Bug Note at the end of the document.
