---
layout: post
title: Tool Development in 3Ds Max
---

In the last month I moved into a new position in my company, I decided to be more focused on tool development for 3DS Max. I'm still a Technical Artist but fortunately this title is very large and give me the possibility to change when I'm little bit bored, or I think it is time to improve my skill in some area
I really think that to learn something I need to get out of my comfort zone.
That's why I grabbed 3DS Max (leaving my beloved Maya) and I decided to build my tools completely in Python

 <!--more-->

So let's keep it clear, 3DS Max has a really non mature python SDK, the fastest way to write tools in max for year has been MaxScript(And almost certainly ,it is still the same today)
But there is a different between a fast job and a proper job
I mean with maxscript, from what I see you can do everything, but the point is..how?
Is it correct to call pieces of code in maxscript, randomly call batch script, or inject .Net code to achieve something?
Can you make unit test development, or create a sort of SDK to avoid to reinvent the wheel every time you make a tool?
I don't think so. I studied a bit of max script before to take my decision to switch to python.
To be honest, in a professional environment everyone should be able to prototype quickly and mess around with some maxscript cycle
But the moment you start to do some complex tool, trying to write a complex UI, accessing library and processes outside 3dsMax,
it is clear that max script is not enough, and also I do not like to learn languages useless.

So I choose python and lets say if we can really use it in all his power in 3DSMax

Disclaimer:
Every language has is pro and cons but until Autodesk will continue to write documentation like a chimp, do not stress if you fill frustrated 
if you spend one hour looking for a function, it's simply Autodesk

Lets analyze some pro and cons

I added a personal evaluation to the documentation based on a range from 0 to 5

|   | Documentation  | SDK  |  UI |  language type |
|---|:-:|:-:|:-:|:-:|
|c++  |  2 |  yes |  yes(qt) | compiled  |
|.NET | 0  |  yes |  yes(WPF,WinForm) |  compiled  |
|Python   | 1  | yes  |  yes(PyQt/PySide) | interpreted  |
|MaxScript | 4  |  no |  yes | interpreted  |

C++ is the most powerful ,not so bad documented (we can expect more from Autodesk) but unfortunately you have to re-run max every time you recompile.
In other recently I've seen a Python talk called "How to write fast python code".
Someone notified to the man who exposed the talk the actually just writing the python code in c , was already a way to write fast python code.
That is why the talk has been renamed  "How to write fast python code,and do not hate your job"
So, no thanks, at least if I have to do something really memory-linked

.NET is so bad documented that I think is an Autodesk joke,but at least you have access to everything with good completion.And this really make the difference.
I'm contributing to development of BabylonJS specifically on the 3DSMax Exporter, and it is not so bad as development environment. Especially if you managed to add some pre/post build event to give you the possibility to fast build and deploy on Max

Python can use both Max lib, at the same time if you want (max plus and pymxs), thanks to the fact that is interpreted we have a fast pipeline.
And switching between MaxPlus documentation, maxscript documentation(in case you use pymxs) and PySide(really well documented), you can find mostly everything.
For the rest, thanks god for Google,
[ADN forum](https://forums.autodesk.com/t5/3ds-max-programming/bd-p/area-b35?profile.language=en), and [techartist.org](http://tech-artists.org/)

Lets see how to configure everything to write python code.

## Editor

I came from year of development in .NET and thought could be easy to do everything with VSCode or Visual Studio.
It ended up that if you want to have less headache possible just use PyCharm.
For me is the best Python Ide in the world
Because of the nature of the project I'm working on now , I configured PyCharm to point to user/documents/3dsmax/script folder
There I have all my samples, tools not linked to any projects , simple idea, and symlink to my current production projects

open the prompt in max installation folder as administrator and create a symlink

```batch
mklink python.exe 3dsmaxpy.exe
```

This because PyCharm accept only interpreter called python.exe
now PyCharm will index the resources of the new interpreter and normally library like MaxPlus will not be marked as error

Open PyCharm settings and add a System interpreter passing the [3dsmaxfolder]/python.exe as interpreter

<img src="{{ site.baseurl }}/assets/blog/3dsmaxdev/interpreter.PNG" alt="Interpreter Type" style="width: auto;"/>

Wait for PyCharm sync everything

If you want to install the suggested "Config Tool" do it and then remove except for pip and setup tools

<img src="{{ site.baseurl }}/assets/blog/3dsmaxdev/interpreter_config.PNG" alt="Interpreter Configuration" style="width: auto;"/>

Add a project configuration passing a python remote debugger

<img src="{{ site.baseurl }}/assets/blog/3dsmaxdev/debugger_configuration.PNG" alt="Debugger" style="width: auto;"/>

Remember to pass the port 7720

now we can create a test.py in script folder and add something like this

```python
import MaxPlus as mp
node = mp.Core.GetRootNode()
print "hello World: " + str(node)
```

if everything went ok call this from max listener:
`python.ExecuteFile @"[project_path]\scripts\Test.py"`
we should have something like this.

```maxscript
hello World: INode: Scene Root, <000001C7A4AD2EB0>
#success
```

## Debug

Now the tricky part, we need to install PyCharm Professional to do this

copy

```batch
%localappdata%\JetBrains\Toolbox\apps\PyCharm-P\ch-0\191.6605.12\debug-eggs\pydevd-pycharm.egg
```

to

```batch
C:\Program Files\Autodesk\3ds Max 2019\python\pydevd-pycharm.egg
```

if now add this line on top of your script you should be able to debug

```python

import sys
import os

localappdata_path=os.environ.get('LOCALAPPDATA')
# WARNING!: this path could not corrsipond on your pc
pydev_path = os.path.join(localappdata_path,"JetBrains/Toolbox/apps/PyCharm-P/ch-0/191.6605.12/helpers/pydev")
if not pydev_path in sys.path:
    sys.path.append(pydev_path)

import pydevd_pycharm
pydevd_pycharm.settrace('localhost', port=7720, suspend=False)

import MaxPlus as mp
node = mp.Core.GetRootNode()
print "hello World: " + str(node)

```

Voila , now adding a breakpoint and calling again

`python.ExecuteFile @"[project_path]\scripts\Test.py"`

will hit the breakpoint

now is time to configure PyCharm to automate this, avoiding to call the script from max listener

download the last release of MXSPyCom.zip
and copy the MXSPyCom.exe into "C:\Program Files\Autodesk"
and the initialize_COM_server.ms in %localappdata%\Autodesk\3dsMax\2019 - 64bit\ENU\scripts\startup

now create a an external tool in PyCharm as described [here](https://github.com/techartorg/MXSPyCOM/wiki/PyCharm)


<img src="{{ site.baseurl }}/assets/blog/3dsmaxdev/externalTool.PNG" alt="External tool" style="width: auto;"/>

and probably you would like to assign a keymap shortcut to this command
in settings->keymap
I use ctrl+alt+shift+e to execute the current script
And ctrl+e to run the __init__.py of a custom package when I'm working on a new tool

## Designer

The second advantage is that you can use a really good designer tool to create the interface of your tools.
install qt5 and Qt designer
I have an installation of Maya that come with the Qt designer already installed and I will you this one

As before let's create an external tool to open *.ui file with the Qt designer

<img src="{{ site.baseurl }}/assets/blog/3dsmaxdev/QTDesigner.PNG" alt="Qt Designer" style="width: auto;"/>

You can now build your ui and preview with ctrl+R

<img src="{{ site.baseurl }}/assets/blog/3dsmaxdev/QTDesignerInApp.PNG" alt="Qt Designer" style="width: auto;"/>

For more information on how to use ui and  python read Autodesk [doc](https://help.autodesk.com/view/3DSMAX/2019/ENU/?guid=__py_ref_demo_py_side_loader_8py_example_html)

## MaxScript support
We can also use [PyCharm to run maxscript](https://cgreminders.tumblr.com/post/108045289945/maxscript-highlighter-for-pycharm) but of course we cannot have the debugger



I will write in my next post some in formation on how to build an SDK and how to keep everything consistent using Test Driven development
