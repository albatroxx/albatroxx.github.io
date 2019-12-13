---
layout: post
---

<figure>
  <img src="https://albatroxx.github.io/images/terminal_formatting.png" alt="The output of the help function"/>
  <figcaption style="text-align:center;">The help() function displays a comprehensive set of examples.</figcaption>
</figure>

If you read my post from yesterday, you may have been thinking, "Gee, this does seem like an odd place for a very detailed function for colouring text in the terminal". And you would be right, as I originally wrote that function for a small terminal formatting library I made. I imagine there is an equivalent library somewhere that I could just install and use, but I wrote the first version of this library several years ago before I learned about pip and conda.

Speaking of conda, if you have not yet heard [the good news that is conda](https://docs.conda.io/en/latest/miniconda.html), today is your lucky day. I wholeheartedly recommend installing python through miniconda, since it makes all of your dependency problems go away.

This library provides three pieces of functionality. First, it allows for easy text coloration by providing dedicated variables so that you don't need to clutter your beautiful print statements with crazy escape sequences. Second, it allows for very granular text coloration by providing colours as close as possible to a target RGB colour. Third, it prints out all of the relevant values into the terminal so that you know exactly how things are going to look.

As always, I am a big fan of simple tools with no dependencies outside of the standard libraries. If you want simple terminal formatting with little overhead, feel free to use this.

```python
import os

# styles
reset = "\x1b[0m"
bold = "\x1b[1m"
dim = "\x1b[2m"
italic = "\x1b[3m"
underline = "\x1b[4m"
blink = "\x1b[5m"
invert = "\x1b[6m"
highlight = "\x1b[7m"
invisible = "\x1b[8m"
strikeout = "\x1b[9m"
styles = {"reset":reset, "bold":bold, "dim":dim, "italic":italic, "underline":underline,
	"blink":blink, "invert":invert, "highlight":highlight, "invisible":invisible, "strikeout":strikeout}
# foreground colors
black = "\x1b[30m"
darkgray = "\x1b[90m"
darkgrey = "\x1b[90m"
red = "\x1b[31m"
lightred = "\x1b[91m"
olive = "\x1b[32m"
green = "\x1b[92m"
gold = "\x1b[33m"
yellow = "\x1b[93m"
blue = "\x1b[34m"
lightblue = "\x1b[94m"
purple = "\x1b[35m"
magenta = "\x1b[95m"
cyan = "\x1b[36m"
lightcyan = "\x1b[96m"
lightgray = "\x1b[37m"
lightgrey = "\x1b[37m"
white = "\x1b[97m"
foreground_colors = {"black":black, "darkgray":darkgray, "darkgrey":darkgrey, "red":red, "lightred":lightred,
	"olive":olive, "green":green, "gold":gold, "yellow":yellow, "blue":blue, "lightblue":lightblue,
	"purple":purple, "magenta":magenta, "cyan":cyan, "lightcyan":lightcyan, "lightgray":lightgray,
	"lightgrey":lightgrey, "white":white}
# background colors
hblack = "\x1b[40m"
hdarkgray = "\x1b[100m"
hdarkgrey = "\x1b[100m"
hred = "\x1b[41m"
hlightred = "\x1b[101m"
holive = "\x1b[42m"
hgreen = "\x1b[102m"
hgold = "\x1b[43m"
hyellow = "\x1b[103m"
hblue = "\x1b[44m"
hlightblue = "\x1b[104m"
hpurple = "\x1b[45m"
hmagenta = "\x1b[105m"
hcyan = "\x1b[46m"
hlightcyan = "\x1b[106m"
hlightgray = "\x1b[47m"
hlightgrey = "\x1b[47m"
hwhite = "\x1b[107m"
background_colors = {"black":hblack, "darkgray":hdarkgray, "darkgrey":hdarkgrey, "red":hred, "lightred":hlightred,
	"olive":holive, "green":hgreen, "gold":hgold, "yellow":hyellow, "blue":hblue, "lightblue":hlightblue,
	"purple":hpurple, "magenta":hmagenta, "cyan":hcyan, "lightcyan":hlightcyan, "lightgray":hlightgray,
	"lightgrey":hlightgrey, "white":hwhite}
# define the 256 colors available in the terminal
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

# apply different typefaces to the text
def style(*text_styles):
	return "".join([styles[a] for a in text_styles])

# color the text (including choosing both a foreground and background)
def color(color_desc):
	if "-on-" in color_desc:
		color_desc = color_desc.split("-on-")
		return foreground_colors[color_desc[0]] + background_colors[color_desc[1]]
	else:
		return foreground_colors[color_desc]

# color the background only
def highlight(h_color):
	return background_colors[h_color]

# find the color closest to a provided RGB triple
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

# print a table of possible outputs, with some helpful examples
def help ():
	# print some context first
	print()
	print("This example assumes that this module was imported with 'import terminal_formatting as tf'")
	print("Each of the following commands changes all future text, and evaluates to a string.")
	print("The output from each command shown below is equivalent to writing \"<command> + 'Hello World!' + tf.reset\"")
	print()
	# print the header
	print("-"*177)
	print("|{:^57}|{:^67}|{:^49}|".format("Change Foreground", "Change Background", "Change Both"))
	print("-"*177)
	# generate and print the table
	for c in ("black", "darkgrey", "red", "lightred", "olive","green", "gold", "yellow", "blue", "lightblue", "purple", "magenta", "cyan", "lightcyan", "lightgrey", "white"):
		output = [
			"{} or {} = {}".format("tf.{0:<10}", "tf.color('{0}')" + " "*(10-len(c)), "{1}{4}"),
			"{} or {} = {}".format("tf.h{0:<10}", "tf.highlight('{0}')" + " "*(10-len(c)), "{2}{4}"),
			"{} = {}".format("tf.color('black-on-{0}')" + " "*(10-len(c)), "{3}{4}")]
		print("    |    ".join(output).format(c, color(c), highlight(c), color("black-on-"+c), "Hello World!" + reset))
	print("-"*177)
	print("\n")
	print("Unlike the previous commands, styles() can take any number of arguments, each representing a different style.")
	print("For example, \"style('bold', 'italic', 'strikeout')\" would produce {}Hello World!{}".format(style("bold", "italic", "strikeout"), reset))
	print()
	print("-"*53)
	print("|{:^51}|".format("Change Text Style"))
	print("-"*53)
	for s in sorted(list(styles.keys())):
		print(("tf.{:<9} or tf.styles('{}')" + " "*(9-len(s)) + " = {}{}").format(s, s, styles[s], "Hello World!" + reset))
	print()
	print("This module also contains a large number of special characters, including the lowercase greek alphabet.")
	# print the screen size too, just in case that is useful
	print("The current screen size (in characters) is {0[0]} x {0[1]}".format(os.popen('stty size', 'r').read().split()))

# math and science symbols
neq = "\u2260"
leq = "\u2264"
geq = "\u2265"
equiv = "\u2261"
approximately = "\u2248"
times = "\xd7"
div = "\xf7"
sqrt = "\u221A"
cuberoot = "\u221B"
sum = "\u03A3"
integral = "\u222B"
infinity = "\u221E"
langle = "\u27E8"
rangle = "\u27E9"
plus_minus = "\xb1"
pow_zero = "\xba"
pow_one = "\xb9"
squared = "\xb2"
cubed = "\xb3"
textohm = "\u03A9"

# geometry symbols
diameter = "\u2300"
angle = "\u2220"
perpendicular = "\u22A5"
degrees = "\xb0"
clockwise = "\u293E"
counterclockwise = "\u293F"

# fun numbers
textonequarter = "\xbc"
textonehalf = "\xbd"
textthreequarters = "\xbe"
naught = "\xd8"

# greek letters
alpha = "\u03B1"
beta = "\u03B2"
gamma = "\u03B3"
delta = "\u03B4"
epsilon = "\u03B5"
zeta = "\u03B6"
eta = "\u03B7"
theta = "\u03B8"
iota = "\u03B9"
kappa = "\u03BA"
lambdachar = "\u03BB"
mu = "\u03BC"
nu = "\u03BD"
xi = "\u03BE"
pi = "\u03C1"
rho = "\u03C2"
sigma = "\u03C3"
tau = "\u03C4"
upsilon = "\u03C5"
phi = "\u03C6"
chi = "\u03C7"
psi = "\u03C8"
omega = "\u03C9"

# other symbols
textcopyright = "\u00A9"
textregistered = "\u00AE"
texttrademark = "\u2122"
```
