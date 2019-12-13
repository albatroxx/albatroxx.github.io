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
for a in range(10):
    print("I wish this was working")
```
