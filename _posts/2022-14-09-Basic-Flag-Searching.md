---
title: "Basic Flag Searching"
date: 2022-14-09
excerpt_separator: "<!--more-->"
categories:
  - Write Up
tags:
  - CTF
  - Write Up
  - Cyber Security
---

With Down Under CTF coming up, I did a live demo of how to use the command line tools `strings`, `grep`, and `r2` (Radare2) for 'hidden in plain sight' flags.

The following is a written version of that.

# Using strings and grep
PicoCTF have a challenge called '*strings it*' meant to get you used to using the command line tool strings and grep. If we download the file and run `strings strings-it` we are presented with a mountain of useless strings. To quickly find the flag, we can use the grep command line tool to specify a portion of the wanted string we know `pico CTF`. To do this we type `strings strings-it | grep picoCTF`. The `|` is called a pipe and it makes a direct connection between two or more commands by sending the output of one command as the input of the next, from left to right. 


# Saving the Output
Now that we have tried `strings`, `grep` and have learnt what a pipe is, we are going to try another PicoCTF challenge called '*first-grep*' which you can download from their website. Instead of going through all the steps again, we can jump straight to the end with `strings first-grep | grep picoCTF`. 

The next command line argument to learn is appending text with `>>`. With this the final output you get from grep can be passed through to a `.txt` file for later reference. What we would type in terminal is `strings first-grep | grep picoCTF >> first-grep-flag.txt`. You can also do this with `>` but I prefer to use `>>` on the off-change I write into an already existing file. `>` will overwrite what is already there.

# Using Radare2 to Get Flags
The next challenge we will look at is '*no strings*' from the 2021 Down Under CTF. Now if we go against the title of the challenge and try `strings nostrings | grep DUCTF` we will get nothing in return. We can try `strings nostrings` to view all output, but it will be a waste of effort. Instead we're going to use Radare2 to quickly grab the flag. 

Assuming you've already downloaded all components, we type `r2 nostrings`. What you will end up in is a debugging environment for the `nostrings` program. Type `iz` which according to the Radare2 documentation means we are running the file and loading the strings in memory. The flag will be presented to you in a table of current string variables in memory. To leave the application we use write `q`.

# Doing the Challenges the 'Right Way'
A final note is on doing challenges in the 'way they were intended'. I will be using the PicoCTF challenge '*wave a flag*' from it's description, it's clear it wants you to either run the program and follow the instructions, or use a debugger to get the string. The first way would be solved by using `./wave-a-flag -h` and you'll be presented the flag. The second way is identical to the '*no strings*' challenge. `r2 wave-a-flag` then in the debugging environment `iz`.

It is also possible to just type `strings wave-a-flag | grep picoCTF` and get the flag that way. There is no *wrong* way of solving a challenge (assuming you are following competition rules). In a live competition I would start with `strings file-name | grep flagCTF` just to get quick and easy points in the beginning. However, during practice, I would recommend not cheesing the challenges where possible so that you have more opportunity to learn new tools and practice problem solving. Because when you are finished with all the challenges you can easily cheese, you're going to need those other tools and knowledge to get more flag and score more points.

*Final Note*: remember to use `man` if you don't understand a command-line tool.

# Reference
[PicoCTF](https://play.picoctf.org/)
[Radare2](https://github.com/radareorg/radare2)
[CTF Walkthroughs Using Radare2](https://r2wiki.readthedocs.io/en/latest/home/ctf-solving-using-radare2/)



