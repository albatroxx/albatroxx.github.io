---
layout: post
---

<figure>
  <img src="https://albatroxx.github.io/images/terminal_formatting.png" alt="The output of the help function"/>
  <figcaption style="text-align:center;">The help() function displays a comprehensive set of examples.</figcaption>
</figure>

If you read my post from yesterday, you may have been thinking, "Gee, this does seem like an odd place for a very detailed function for colouring text in the terminal". And you would be right, as I originally wrote that function for a small terminal formatting library I made. I imagine there is an equivalent library somewhere that I could just install and use, but I wrote this several years ago before I learned about pip and conda.

Speaking of conda, if you do not know about pip and conda, today is your lucky day. I wholeheartedly recommend installing python through miniconda, since it makes all of your dependency problems go away. If you prefer, pip is also an option, though I have had mixed results with it.

This library provides three pieces of functionality. First, it allows for easy text coloration by providing dedicated variables so that you don't need to clutter your beautiful print statements with crazy escape sequences. Second, it allows for very granular text coloration by providing colours as close as possible to a target RGB colour. Third, it prints out all of the relevant values into the terminal so that you know exactly how things are going to look.

As always, I am a big fan of simple tools with no dependencies outside of the standard libraries. If you want simple terminal formatting with little overhead, feel free to use this.

```python
import os

#formatting
reset =     "\x1b[0m"
bold =      "\x1b[1m"
dim =       "\x1b[2m"
italic =    "\x1b[3m"
underline = "\x1b[4m"
blink =     "\x1b[5m"
invert =    "\x1b[6m"
highlight = "\x1b[7m"
invisible = "\x1b[8m"
strikeout = "\x1b[9m"
#foreground colors
black =     "\x1b[30m"
dgrey =     "\x1b[90m"
red =       "\x1b[31m"
lred =      "\x1b[91m"
olive =     "\x1b[32m"
green =     "\x1b[92m"
gold =      "\x1b[33m"
yellow =    "\x1b[93m"
blue =      "\x1b[34m"
lblue =     "\x1b[94m"
purple =    "\x1b[35m"
magenta =   "\x1b[95m"
cyan =      "\x1b[36m"
lcyan =     "\x1b[96m"
lgrey =     "\x1b[37m"
white =     "\x1b[97m"
#background colors
hblack =    "\x1b[40m"
hdgrey =    "\x1b[100m"
hred =      "\x1b[41m"
hlred =     "\x1b[101m"
holive =    "\x1b[42m"
hgreen =    "\x1b[102m"
hgold =     "\x1b[43m"
hyellow =   "\x1b[103m"
hblue =     "\x1b[44m"
hlblue =    "\x1b[104m"
hpurple =   "\x1b[45m"
hmagenta =  "\x1b[105m"
hcyan =     "\x1b[46m"
hlcyan =    "\x1b[106m"
hlgrey =    "\x1b[47m"
hwhite =    "\x1b[107m"

# define the 256 colors
colors = [
	(  0,  0,  0), (128,  0,  0), (  0,128,  0), (128,128,  0), (  0,  0,128), (128,  0,128), (  0,128,128), (192,192,192),
	(128,128,128), (255,  0,  0), (  0,255,  0), (255,255,  0), (  0,  0,255), (255,  0,255), (  0,255,255), (255,255,255),
	(  0,  0,  0), (  0,  0, 95), (  0,  0,135), (  0,  0,175), (  0,  0,215), (  0,  0,255), (  0, 95,  0), (  0, 95, 95),
	(  0, 95,135), (  0, 95,175), (  0, 95,215), (  0, 95,255), (  0,135,  0), (  0,135, 95), (  0,135,135), (  0,135,175),
	(  0,135,215), (  0,135,255), (  0,175,  0), (  0,175, 95), (  0,175,135), (  0,175,175), (  0,175,215), (  0,175,255),
	(  0,215,  0), (  0,215, 95), (  0,215,135), (  0,215,175), (  0,215,215), (  0,215,255), (  0,255,  0), (  0,255, 95),
	(  0,255,135), (  0,255,175), (  0,255,215), (  0,255,255), ( 95,  0,  0), ( 95,  0, 95), ( 95,  0,135), ( 95,  0,175),
	( 95,  0,215), ( 95,  0,255), ( 95, 95,  0), ( 95, 95, 95), ( 95, 95,135), ( 95, 95,175), ( 95, 95,215), ( 95, 95,255),
	( 95,135,  0), ( 95,135, 95), ( 95,135,135), ( 95,135,175), ( 95,135,215), ( 95,135,255), ( 95,175,  0), ( 95,175, 95),
	( 95,175,135), ( 95,175,175), ( 95,175,215), ( 95,175,255), ( 95,215,  0), ( 95,215, 95), ( 95,215,135), ( 95,215,175),
	( 95,215,215), ( 95,215,255), ( 95,255,  0), ( 95,255, 95), ( 95,255,135), ( 95,255,175), ( 95,255,215), ( 95,255,255),
	(135,  0,  0), (135,  0, 95), (135,  0,135), (135,  0,175), (135,  0,215), (135,  0,255), (135, 95,  0), (135, 95, 95),
	(135, 95,135), (135, 95,175), (135, 95,215), (135, 95,255), (135,135,  0), (135,135, 95), (135,135,135), (135,135,175),
	(135,135,215), (135,135,255), (135,175,  0), (135,175, 95), (135,175,135), (135,175,175), (135,175,215), (135,175,255),
	(135,215,  0), (135,215, 95), (135,215,135), (135,215,175), (135,215,215), (135,215,255), (135,255,  0), (135,255, 95),
	(135,255,135), (135,255,175), (135,255,215), (135,255,255), (175,  0,  0), (175,  0, 95), (175,  0,135), (175,  0,175),
	(175,  0,215), (175,  0,255), (175, 95,  0), (175, 95, 95), (175, 95,135), (175, 95,175), (175, 95,215), (175, 95,255),
	(175,135,  0), (175,135, 95), (175,135,135), (175,135,175), (175,135,215), (175,135,255), (175,175,  0), (175,175, 95),
	(175,175,135), (175,175,175), (175,175,215), (175,175,255), (175,215,  0), (175,215, 95), (175,215,135), (175,215,175),
	(175,215,215), (175,215,255), (175,255,  0), (175,255, 95), (175,255,135), (175,255,175), (175,255,215), (175,255,255),
	(215,  0,  0), (215,  0, 95), (215,  0,135), (215,  0,175), (215,  0,215), (215,  0,255), (215, 95,  0), (215, 95, 95),
	(215, 95,135), (215, 95,175), (215, 95,215), (215, 95,255), (215,135,  0), (215,135, 95), (215,135,135), (215,135,175),
	(215,135,215), (215,135,255), (215,175,  0), (215,175, 95), (215,175,135), (215,175,175), (215,175,215), (215,175,255),
	(215,215,  0), (215,215, 95), (215,215,135), (215,215,175), (215,215,215), (215,215,255), (215,255,  0), (215,255, 95),
	(215,255,135), (215,255,175), (215,255,215), (215,255,255), (255,  0,  0), (255,  0, 95), (255,  0,135), (255,  0,175),
	(255,  0,215), (255,  0,255), (255, 95,  0), (255, 95, 95), (255, 95,135), (255, 95,175), (255, 95,215), (255, 95,255),
	(255,135,  0), (255,135, 95), (255,135,135), (255,135,175), (255,135,215), (255,135,255), (255,175,  0), (255,175, 95),
	(255,175,135), (255,175,175), (255,175,215), (255,175,255), (255,215,  0), (255,215, 95), (255,215,135), (255,215,175),
	(255,215,215), (255,215,255), (255,255,  0), (255,255, 95), (255,255,135), (255,255,175), (255,255,215), (255,255,255),
	(  8,  8,  8), ( 18, 18, 18), ( 28, 28, 28), ( 38, 38, 38), ( 48, 48, 48), ( 58, 58, 58), ( 68, 68, 68), ( 79, 79, 79),
	( 88, 88, 88), ( 98,908, 98), (108,108,108), (118,118,118), (128,128,128), (138,138,138), (148,148,148), (159,159,159),
	(168,168,168), (178,178,178), (188,188,188), (198,198,198), (208,208,208), (218,218,218), (244,244,244), (255,255,255)]
 
def rgb(r, g, b):
	# compare red, green, and blue to each of the 0-255 colors and pick the closest one
	distance = 3*255**2
	color = 0
	for a in range(256):
		x, y, z = colors[a]
		d = (r-x)**2 + (g-y)**2 + (b-z)**2
		if d < distance:
			distance = d
			color = a
	return f"\x1b[38;5;{color}m"

def help ():
    print ()
    print ("Use the following codes to change all future text. Note that the code '\\x1b[0m' resets everything to default.")
    print ("--------------------------------------------------------------------------------------------------------------")
    #you can double up text color and highlight tags
    print ("\x1b[30m \\x1b[30m Black      \x1b[0m     "     + "\x1b[40m \\x1b[40m  Black Highlight      \x1b[0m     \\x1b[0m \x1b[0m Regular \x1b[0m")
    print ("\x1b[90m \\x1b[90m Dark Grey  \x1b[0m     "    + "\x1b[100m \\x1b[100m Dark Grey Highlight  \x1b[0m     \\x1b[1m \x1b[1m Bolded \x1b[0m")
    print ("\x1b[31m \\x1b[31m Red        \x1b[0m     "     + "\x1b[41m \\x1b[41   Red Highlight        \x1b[0m     \\x1b[2m \x1b[2m Dim \x1b[0m")
    print ("\x1b[91m \\x1b[91m Light Red  \x1b[0m     "    + "\x1b[101m \\x1b[101  Light Red Highlight  \x1b[0m     \\x1b[3m \x1b[3m Italicized \x1b[0m")
    print ("\x1b[32m \\x1b[32m Olive      \x1b[0m     "     + "\x1b[42m \\x1b[42   Olive Highlight      \x1b[0m     \\x1b[4m \x1b[4m Underlined \x1b[0m")
    print ("\x1b[92m \\x1b[92m Green      \x1b[0m     "    + "\x1b[102m \\x1b[102  Green Highlight      \x1b[0m     \\x1b[5m \x1b[5m Blinking \x1b[0m")
    print ("\x1b[33m \\x1b[33m Gold       \x1b[0m     "     + "\x1b[43m \\x1b[43   Gold Highlight       \x1b[0m     \\x1b[6m \x1b[6m Inverted \x1b[0m")
    print ("\x1b[93m \\x1b[93m Yellow     \x1b[0m     \x1b[30m\x1b[103m \\x1b[103  Yellow Highlight     \x1b[0m     \\x1b[7m \x1b[7m Highlighted \x1b[0m")
    print ("\x1b[34m \\x1b[34m Blue       \x1b[0m     "     + "\x1b[44m \\x1b[44   Blue Highlight       \x1b[0m     \\x1b[8m \x1b[8m Invisible \x1b[0m")
    print ("\x1b[94m \\x1b[94m Light Blue \x1b[0m     \x1b[30m\x1b[104m \\x1b[104  Light Blue Highlight \x1b[0m     \\x1b[9m \x1b[9m Struck Out \x1b[0m")
    print ("\x1b[35m \\x1b[35m Purple     \x1b[0m     "     + "\x1b[45m \\x1b[45   Purple Highlight     \x1b[0m")
    print ("\x1b[95m \\x1b[95m Magenta    \x1b[0m     "    + "\x1b[105m \\x1b[105  Magenta Highlight    \x1b[0m")
    print ("\x1b[36m \\x1b[36m Cyan       \x1b[0m     "     + "\x1b[46m \\x1b[46   Cyan Highlight       \x1b[0m")
    print ("\x1b[96m \\x1b[96m Light Cyan \x1b[0m     "    + "\x1b[106m \\x1b[106  Light Cyan Highlight \x1b[0m")
    print ("\x1b[37m \\x1b[37m Light Grey \x1b[0m     \x1b[30m\x1b[47m \\x1b[47   Light Grey Highlight \x1b[0m")
    print ("\x1b[97m \\x1b[97m White      \x1b[0m     \x1b[30m\x1b[107m \\x1b[107  White Highlight      \x1b[0m")
    print ()
    print ("This:       \\x1b[1m\\x1b[4m\\x1b[5m\\x1b[93m\\x1b[100mBold, Underlined, Italic Yellow Text on a Dark Grey Background\\x1b[0m")
    print ("Produces:   \x1b[1m\x1b[4m\x1b[5m\x1b[93m\x1b[100mBold, Underlined, Italic Yellow Text on a Dark Grey Background\x1b[0m")
    print ()
    
    print ("The current screen dimensions (in characters) are:") 
    rows, columns = os.popen('stty size', 'r').read().split()
    print ("Rows:   " + str(rows))
    print ("Cols:   " + str(columns))
    ```
