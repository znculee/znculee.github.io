---
layout:   post
title:    Ideal Operating System in My Mind
category: Random_Thoughts
date:     2019-03-14
comments: false
---

[Linux from Scratch](http://www.linuxfromscratch.org/) invokes my passion to develop my own Linux Distribution.
I am interested in consistently refining working experience for higher efficiency.
And I think my experience of building a high efficient working space should be originally embedded in an fantastic operating system.

Mac is my prime device because its relative perfectly combining command line interface (CLI) and graphic user interface (GUI).
CLI occupies the most of my working time due to is high efficiency, simplicity and elegance.
My CLI mainly consists of three parts, including [tmux](https://github.com/tmux/tmux), [vim](https://github.com/vim/vim) and [fish](https://github.com/fish-shell/fish-shell).
In addition, [conda](https://docs.conda.io/en/latest/) and docker can elegantly manage my developing environment.
With a number of open source tools, working in CLI leads everything clear and effective.
However, sometimes we want to plot some figures, reading pdf files, watching a movie, and we have to use GUI in a lot of situations.
Therefore, our new system has to support GUI application naturally.
GUI is the base environment of almost all modern operating system except them for server without GUI totally.
Fortunately, [Alfred](https://www.alfredapp.com/) builds a great CLI experience for GUI situation, because it can launch applications, files and even any personalised workflows by typing few characters.
With Alfred, I seldom gaze at my mouse and everything just flows from my fingers.
So, why don't embed Alfred into CLI and only use GUI application when we need.
Note that, the windows of GUI applications and workspaces can be controlled by something like [SizeUp](http://www.irradiatedsoftware.com/sizeup/).

Like Finder is the permanent progress in Mac, we want terminal be the permanent progress in our new system, and launch GUI applications only when it is necessary.
Let's imagine, after pressing the power button, we are asked to input our user name and password, note that the user name can be completed by TAB.
After the login information is entered, we would be in a tmux environment.
So we would be working in CLI by default.
When we need GUI applications, we can launch them by typing in command line with different options, and mouse will only appear when we are not typing in command line.
No matter which application is focused, we would be able to use shortcuts to organize the windows and evoke command line anytime like Alfred.
The system would be able to highly personalise and always keep simplicity.

The philosophy behind this system obeys the UNIX's philosophy.
I believe real hackers would enjoy immersing into this system for both making the world better and enjoying the beauty of this world.

## Further Readings

- [Understanding Open Source Software, and How It Makes You Money Online](https://www.websiteplanet.com/blog/what-is-open-source-software/)
