---
layout: post
---

<figure>
  <img src="https://albatroxx.github.io/images/line_profiler.png" alt="Example output"/>
  <figcaption style="text-align:center;">Looks like eval() is super slow.</figcaption>
</figure>

Apparently, there aren’t a ton of very convenient code profilers for Python. That isn’t to say that there aren’t good ways to profile code, as there are some potent tools available, but none of them did what I wanted a profiler to do. I had three criteria for my theoretical profiler:
1. It should profile an entire script
2. It should provide clear visual feedback of the hotspots in the code itself
3. It should be simple to get and use

Generally, I write scripts that perform repetitive tasks I don’t want to do in order to make my life easier. These usually involve for loops and thousands of iterations, but not much in the way of classes and function calls. Since most of my code is imperative rather than object-oriented, my ideal profiler would be independent of classes and functions. I wanted a profiler that would work on anything that didn’t throw an error, without requiring any changes to that code. Second, I am a very visual person, and seeing a line of code outside of its context is super confusing to me. Ideally, a profiler would visually tell me (in context) which lines of code were my problem spots. Third, I hate dealing with dependencies, multiple utilities and several step processes. My ideal profiler would be a single program with no dependencies (except the Python standard library, since it will be profiling Python code), and be executable with a single command.

Spoiler, I didn’t find a profiler than met my requirements, but it turns out they are pretty fun to write. So that’s cool.

### Installation

In the interest of making life simple, I didn’t do much in the way of packaging. One day I will figure that out, but not today. For now, installation goes as follows:
1. Copy the code into a file. I named mine `line_profiler.py`, but if you want a different name, you can do that too. Just be aware that whatever name you choose will be the command you need to type to run the program later.
2. Save the file (or download) to somewhere in your `$PATH`. Usually, the most logical place is `/usr/bin/`, but you can always check what your options are by running `echo $PATH` in a terminal.
3. In the terminal, run `chmod +x line_profiler.py` from the file’s location to make it executable.
That's it. You're done.

### Execution

To run the program, run `line_profiler.py script_to_run.py [any command-line arguments used by script_to_run.py]`.

There are two command-line options, both of which are disabled by default:
 - `--include-imports` will include the time spent importing modules in the time profile
 - `--time-estimate` will run your script silently before beginning the profile to get an idea of how much time it will take to execute

### Known Issues

I have only tested this program for compatibility with Python 3.5+. I wholeheartedly intend to figure out a testing setup to allow me to test code against multiple Python versions, but I also have an extremely long list of things I intend to learn how to do, so we will see how that turns out.

This is NOT fast. Some very basic benchmarking pegs it at 15-60 times slower than the code being profiled. That being said, you don’t need to run the profile very often, and it is primarily intended for short-duration scripts. The two simplest workarounds are (a) select a small test case, and (b) use a different profiler to target specific functions in bigger code-bases.

I get odd results sometimes if I profile code in a subfolder (i.e. `line_profiler.py script.py` is fine, `line_profiler.py path/to/script.py` is sometimes not okay). This is inconsistent, so just be aware that if you get strange results that you don't believe, make sure you are in the folder where the script lives when you run the program.

This script contains very little in the way of error checking. If the script fails, it will likely fail spectacularly. I generally prefer this because it is (a) simple to code, and (b) alerts me to the fact that I have done something wrong.

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
