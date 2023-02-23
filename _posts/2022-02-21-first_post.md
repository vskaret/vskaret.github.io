# Hello world

I'm a software developer from Mjøndalen in Norway. My first experience with programming was scripting VBA in Excel while studying economics. I remember working day and night scripting excel to automate a bunch of stuff for an assignment and really having a great time with it. Later I figured out that scripting excel was pretty much the only thing I enjoyed about the economics studies, so I went on to study computer science as well. I enjoyed computer science a lot, and this made me extra happy and relieved because of how boring I found economics studies.

I'm currently following the [fast ai course](https://www.fast.ai/), and will hopefully post a lot about stuff related to that. Jeremy seems like a great teacher.

Timestamps and links from the fast.ai live coding sessions (and sometimes other places) that might be useful.

**Session 1:**

* [Install mamba](https://www.youtube.com/watch?v=56sIyFjihEc&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=2&t=32m) (python environment thingy)
* There is a bunch of scripts automating getting setup stuff in fastai's [fastsetup repo](https://github.com/fastai/fastsetup), e.g. [setup-conda.sh](https://github.com/fastai/fastsetup/blob/master/setup-conda.sh) for installing mamba

**Session 2:**

* [Using tmux](https://www.youtube.com/watch?v=0pWjZByJ3Lk&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=2&t=39m), [simple tmux cheatsheet](https://www.themoderncoder.com/simple-tmux-cheatsheet/) and [basic tmux configuration](https://www.themoderncoder.com/basic-tmux-configuration/)
* [jupyter intro](https://www.youtube.com/watch?v=0pWjZByJ3Lk&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=2&t=49m)

**Session 3:**

* [How to handle python environments with mamba in such a way that one does not need to freeze version numbers and thus do not need to rely as much on docker containers (specifically for data scientists? the point seems to be to facilitate rapid iteration)](https://www.youtube.com/watch?v=B6BQiIgiEks&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=3&t=12m)
* [Setup a jupyter notebook in paperspace gradient](https://www.youtube.com/watch?v=B6BQiIgiEks&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=3&t=21m50s)
* [Python debugger and set_trace](https://www.youtube.com/watch?v=B6BQiIgiEks&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=3&t=36m30s)
* Tip: learn the textual python debugger as its similar to debuggers for other languages so the knowledge generalizes (in contrast to learning properietary tools where you will probably have to re-learn it somewhat)
* Tip: in general, use the keybindings that the tools offer instead of coming up with your own keybindings as it's easier to switch between environments
* [It's fine to use pip to install anything that's only reliant on python code. When you install stuff that relies on code outside of python like pytorch (relies on cuda) it's faster to use conda as it also handles the dependencies outside of python. Sometimes you need to use pip as it let's you install something only on your user in cases where you are not admin: pip install -U --user package_name](https://www.youtube.com/watch?v=B6BQiIgiEks&list=PLfYUBJiXbdtSLBPJ1GMx-sQWf6iNhb8mM&index=4&t=46m10s)
* In paperspace, everything on the machine is whiped out after you're finished. However, you have a /storage folder where stuff is stored persistantly. Use this and symlinks (ln -s path_to_file_to_symlink) to not have to install stuff again each time you start the machine
* In paperspace, you can create a file called .bash.local in storage which will be run everytime you open the terminal. Create symlinks there :)
* When opening a paperspace notebook, /run.sh is executed. This script can't be changed, but it will execute /storage/pre-run.sh which might be a better place to put the symlinks and stuff like that

**Session 4:**

* Virtues of a great programmer: laziness, impatience and hubris. One way of being lazy is to learn stuff that is re-usable, such as using python for scripts rather than shell because python can be used for more purposes.

**Terminal tricks:**

* !! last command
* !$ last token in last command
* ctrl + u: delete everything left of the cursor
* ctrl + k: delete everything right of the cursor

For some more terminal tricks check out [this file](https://github.com/vskaret/configs/blob/master/bash_notes).

**Jupyter notebook tricks (given a function f):**

* f?: displays function signature, docstring, file and type on running
* f??: displays function source code on running
* f: displays where the function is from and its parameters on running
* f( and shift+tab: displays the same as f? in the active jupyter block (do not need to run it)
* shift+m: merge blocks?
* 0 0: restarts the kernal - nice to use when you want to re run everything
* a hotkey for running everything is not set by default, but can be assigned through help > edit keyboard shortcuts > run all cells

**Python technicalities and related stuff:**

* Inside of a class, the class does not know about itself. For type hinting to work one has to wrap the class name in quotes. From [this clip](https://www.youtube.com/watch?v=67FdzLSt4aAt=30m30s)
* fastcore.utils has functionality for defining funtions for a class outside of the class (like [extension functions in kotlin](https://kotlinlang.org/docs/extensions.html)?). This is done with the fastcore.utils.patch decorator. At this point python will know about the class, so no need to wrap type hint of the class in quotes
* [showcase of using nbdev for testing](https://www.youtube.com/watch?v=67FdzLSt4aA&t=41m)

**Other:**

* "When you’re trying to understand enough of a new codebase to make a specific PR, it’s best to use an editor which lets you jump to definitions to understand how the code works. Reading and experimenting in the notebooks is the best way to understand the bigger picture more thoroughly." [(from here)](https://forums.fast.ai/t/nbdev-v2-codebase-falls-short-of-literate-programming/103671/9)
