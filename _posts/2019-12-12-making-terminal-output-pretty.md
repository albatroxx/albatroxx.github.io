---
layout: post
---

<figure>
  <img src="https://albatroxx.github.io/images/terminal_formatting.png" alt="The output of the help function"/>
  <figcaption style="text-align:center;">The help() function displays a comprehensive set of examples.</figcaption>
</figure>

If you read my post from yesterday, you may have been thinking, "Gee, this does seem like an odd place for a very detailed function for colouring text in the terminal". And you would be right, as I originally wrote that function for a small terminal formatting library I made. I imagine there is an equivalent library somewhere that I could just install and use, but I wrote this several years ago before I learned about pip and conda.

Speaking of conda, if you have not yet heard [the good news that is conda](https://docs.conda.io/en/latest/miniconda.html), today is your lucky day. I wholeheartedly recommend installing python through miniconda, since it makes all of your dependency problems go away.

This library provides three pieces of functionality. First, it allows for easy text coloration by providing dedicated variables so that you don't need to clutter your beautiful print statements with crazy escape sequences. Second, it allows for very granular text coloration by providing colours as close as possible to a target RGB colour. Third, it prints out all of the relevant values into the terminal so that you know exactly how things are going to look.

As always, I am a big fan of simple tools with no dependencies outside of the standard libraries. If you want simple terminal formatting with little overhead, feel free to use this.

```python
#!/usr/bin/python3

import trace as tr
import time as ti
import subprocess as su
import glob as gl
import sys as sy

print("\n"*5)
# ensure a valid file was entered
if len(sy.argv) < 2:
	print("must specify a python file to profile")
	exit()
# make sure the first argument is actually a python file
if sy.argv[1][-3:] != ".py":
	print("the selected file is not a python script")
	exit()
# set variables from command line arguments
script = sy.argv[1]
ignore_imports = "--include-imports" not in sy.argv
compare_runtimes = "--time-estimate" in sy.argv
# remove any folder structure to get the name of the script
script_name = script
while "/" in script_name:
	script_name = script_name[script_name.find("/")+1:]



# define the 256 available terminal colors so that the lines can get colorized appropriately
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
	return "\x1b[38;5;{}m".format(color)



#############################
# RUN THE SCRIPT AND LOGGER #
#############################

# give an estimate of runtime in case the expected duration is very high
if compare_runtimes:
	start_time = ti.time()
	process = su.run(["python3", script], universal_newlines=True, stdout=su.PIPE, stderr=su.PIPE)
	stop_time = ti.time()
	print("Script originally executed in {:8.4} seconds".format(stop_time-start_time))
	print("Expected runtime of line_profiler.py is {:7.3} to {:7.3} seconds".format((stop_time-start_time)*15, (stop_time-start_time)*60))

# time the script
print("executing '{}' now.".format(script_name))
start_time = ti.time()
process = su.run(["python3", "-m", "trace", "--trace", "--missing", "--timing", script], universal_newlines=True, stdout=su.PIPE, stderr=su.PIPE)
stop_time = ti.time()



####################
# PROCESS THE DATA #
####################

# get the file that was run
file = open(script, "r").read().split("\n")
# get the output from that file
output = process.stdout.split("\n")

print("="*50)
print("{} commands executed in {:8.4} seconds".format(len(output), stop_time-start_time))
output = [a for a in output if " {}(".format(script_name) in a] # only include lines relating to the selected script
output = [a for a in output if a.find(" {}(".format(script_name)) < 20] # occasionally unintended lines slip through with the script file referenced at the end
print("{} of which are local to '{}'".format(len(output), script_name))
print("="*50)

# extract the timing information
line_times = {a:0.0 for a in range(len(file)+1)}
for a in range(len(output)-1):
	time = float(output[a+1][:output[a+1].index(" ")]) - float(output[a][:output[a].index(" ")])
	line = int(output[a][output[a].index(" {}(".format(script_name))+len(" {}(".format(script_name)):output[a].index(")")])
	line_times[line-1] += time

# edit out the import statements
if ignore_imports:
	for a in range(len(file)):
		if file[a][:6] == "import" or (file[a][:4] == "from" and "import" in file[a]):
			line_times[a] = 0



######################################
# DISPLAY THE COLORIZED LINE PROFILE #
######################################

# display the results
total_time = sum(line_times.values())
scale = 1.0 / (max(line_times.values())/total_time)
for a in range(len(file)):
	coloredness = 256*scale*line_times[a]/total_time
	print("{}{:5}{:6.1f}: {}".format(rgb(128 + coloredness, 128 - coloredness, 128 - coloredness), a+1, 100*line_times[a]/total_time, file[a]))
    ```
