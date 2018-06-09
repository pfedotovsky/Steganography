# Steganography

# Table of Contents
  * [WavSteg](#WavSteg)
  * [LSBSteg](#LSBSteg)
  * [StegDetect](#StegDetect)

<a name = "WavSteg"></a>
## WavSteg
WavSteg uses least significant bit steganography to hide a file in the samples
of a .wav file.

For each sample in the audio file, we overwrite the least significant bits with
the data from our file.

### How to use
WavSteg requires Python 3

Run WavSteg with the following command line arguments:

    -h, --hide        To hide data in a sound file
    -r, --recover     To recover data from a sound file
    -s, --sound=      Path to a .wav file
    -f, --file=       Path to a file to hide in the sound file
    -o, --output=     Path to an output file
    -n, --LSBs=       How many LSBs to use
    -b, --bytes=      How many bytes to recover from the sound file
    --help            Display this message
	
Example:

    WavSteg.py -h -s sound.wav -f file.txt -o sound_steg.wav -n 1
	# OR
	WavSteg.py -r -s sound_steg.wav -o output.txt -n 1 -b 1000
	
### Hiding Data
Hiding data requires the arguments -h, -s, -f, -o, and -n.

The following command would hide the contents of file.txt into sound.wav and
save the result as sound_steg.wav. The command also outputs how many bytes have
been used out of a theoretical maximum. In practice, the maximum amount of data
we can hide may be slightly lower than what is displayed because we skip
samples that have the smallest possible value (for most files, this never
occurs).

Example:

    $ WavSteg.py -h -s sound.wav -f file.txt -o sound_steg.wav -n 1
	Using 1000 B out of 2000 B
	
If you attempt to hide too much data, WavSteg will raise a ValueError and
print the minimum number of LSBs required to hide your data.

### Recovering Data
Recovering data requires the arguments -r, -s, -o, -n, and -b

The following command would recover the hidden data from sound_steg.wav and
save it as output.txt. This requires the size in bytes of the hidden data to
be accurate or the result may be too short or contain extraneous data.

Example:

    $ WavSteg.py -r -s sound_steg.wav -o output.txt -n 1 -b 1000
  
<a name = "LSBSteg"></a>
## LSBSteg
LSBSteg uses least significant bit steganography to hide a file in the color
information of an RGB image (.bmp or .png).

For each color channel (R,G,B) in each pixel of the image, we overwrite the
least significant bits of the color value with the data from our file.
In order to make recovering this data easier, we also hide the file size
of our input file in the first few color channels of the image.

### How to use
You need Python 3 and Pillow, a fork of the Python Imaging Library (PIL).

Run LSBSteg with the following command line arguments:

    Command Line Arguments:
     -h, --hide           To hide data in an image
     -r, --recover        To recover data from an image
     -a, --analyze        Print how much data can be hidden in image
     -i, --image=         Path to a bitmap (.bmp or .png) image
     -f, --file=          Path to a file to hide in the image
     -o, --output=        Path to an output file
     -n, --LSBs=          How many LSBs to use
     -c, --compression=   1 (best speed) to 9 (smallest file size)
     --help               Display this message

Example:

    LSBSteg.py -a -i input_image.png -f input_file.zip -n 2
    # OR
    LSBSteg.py -h -i input_image.png -f input_file.zip -o steg.png -n 2 -c 1
    # OR
    LSBSteg.py -r -i steg.png -o output_file.zip -n 2

### Analyzing
Before hiding data in an image, it can useful to see how much data can be
hidden. The following command will achieve this, producing output similar to

    $ LSBSteg.py -a -i input_image.png -f input_file.zip -n 2
    Image resolution: (2000, 1100)
    Using 2 LSBs, we can hide:      1650000 B
    Size of input file:             1566763 B
    File size tag:                  3 B
	
### Hiding Data
The following command will hide data in the input image and write the result to
the steganographed image, producing output similar to

    $ LSBSteg.py -h -i input_image.png -f input_file.zip -o steg.png -n 2 -c 1
    Hiding 1566763 bytes
    Runtime: 6.11 s

### Recovering Data
The following command will recover data from the steganographed image and write
the result to the output file, producing output similar to

    $ LSBSteg.py -r -i steg.png -o output_file.zip -n 2
    Looking to recover 1566763 bytes
    Runtime: 4.44 s

<a name = "StegDetect"></a>
## StegDetect
StegDetect provides one method for detecting simple steganography in images.

### How to Use
You need Python 3 and Pillow, a fork of the Python Imaging Library (PIL).

Run StegDetect with the following command line arguments:

    Command Line Arguments:
     -f, --file=       Path to an image
     -n, --LSBs=       How many LSBs to display
     --help            Display this message
	
### Showing the Least Significant Bits of an Image
We sum the least significant n bits of the RGB color channels for each pixel
and normalize the result to the range 0-255. This value is then applied to each
color channel for the pixel. Where n is the number of least significant bits to
show, the following command will save the resulting image, appending "_nLSBs"
to the file name, and will produce output similar to the following:

    $ StegDetect.py -f image.png -n 2
    Runtime: 3.55 s