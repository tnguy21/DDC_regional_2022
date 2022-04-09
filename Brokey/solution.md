# Challenge name: Brokey
This is  a walkthrough of the brokey challenge, which was under the category forensics.

# Keywords
hex editor, png, reparing file, IHDR, PNG magic number (File signature) & IDAT

# Utilities used
[hex](https://wiki.gnome.org/Apps/Ghex), file & [pngcheck](https://manpages.debian.org/testing/pngcheck/pngcheck.1.en.html)

# Description 
In this challenge you are given a .png file, which is broken and has to be fixed in order to open 
the image and read the flag.

# Walkthrough
Initially you are given the file, and one of the first things that pops into my mind is to check
whether or not the extension to the file called `brokey.png` is actually a png or not. Using the
`file` utility on the file gives us:

```bash
foo@bar~$ file brokey.png
brokey.png: data
```
The `data` output makes it very clear, that something is not adding up - Maybe the file is corrupted.
Assuming that the file extension is true, you can check its integrity by using `pnghcheck`, which
helps verify the integrity and tell what is missing in the case that the file might be corrupted.
Running `pngcheck` on the file then gives us:

```bash
foo@bar~$ pngcheck brokey.png
brokey.png  this is neither a PNG or PNG image nor a MNG stream
ERROR: brokey.png
```

Now that `pngcheck` has tolds us that it is not a PNG. Now to find out what kind of file it is or repair
it in case it is corrupted you can open the file in a hexeditor to see that actual data stored in the file.
For this you can use `ghex`, which is a simple graphics hex editor.

INSERT IMAGE

If you have never tried reparing a file before it might seem very sophisticated, but for now all you have to do 
is focus on the first blue part that I have marked on the image. The first part looks like the `PNG magic number`
or also called the `file signature` tells us that an actual PNG should start with the numbers:
`89 50 4E 47 0D 0A 1A 0A`
Although the magic number of a PNG looks very similar to the on in the image above they are not the same.
Changing the file signature to match a PNG magic number and using pngcheck on it again now gives up:

```bash
foo@bar~$ pngcheck brokey.png
brokey.png  invalid IHDR length
ERROR: brokey.png
```

If you do not know what a PNG file is structured like please refere to the picture below:
INSERT IMAGE

Now we have to set the IHDR length to the appropiate length, which according to the PNG specification should be
13 bytes (See the [documentation](http://www.libpng.org/pub/png/spec/1.2/PNG-Chunks.html) at section 4.1.1),
which converted to hexadecimal is 0x0D. In the image below you can see that the IHDR length is incorrect,
because is it set to 0xD0 instead of 0x0D.

INSERT IMAGE

After setting the IHDR length correctly we can now check the image using pngcheck again which gives us:
```bash
foor@bar~$ pngcheck brokey.png
brokey.png  EOF while reading IDAT data
ERROR: brokey.png
```
Although this is an error it turns out that we now can open the image, which gives the flag:
`DDC{thx_for_fixing_me}
