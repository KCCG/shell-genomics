---
title: "Introducing the Shell"
teaching: 20
exercises: 10
questions:
- "What is a command shell and why would I use one?"
- "How can I move around on my computer?"
- "How can I see what files and directories I have?"
- "How can I specify the location of a file or directory on my computer?"
objectives:
- "Describe key reasons for learning shell."
- "Navigate your file system using the command line."
- "Access and read help files for `bash` programs and use help files to identify useful command options."
- "Demonstrate the use of tab completion, and explain its advantages."
keypoints:
- "The shell gives you the ability to work more efficiently by using keyboard commands rather than a GUI."
- "Useful commands for navigating your file system include: `ls`, `pwd`, and `cd`."
- "Most commands take options (flags) which begin with a `-`."
- "Tab completion can reduce errors from mistyping and make work more efficient in the shell."
---

## What is a shell and why should I care?

A *shell* is a computer program that presents a command line interface
which allows you to control your computer using commands entered
with a keyboard instead of controlling graphical user interfaces
(GUIs) with a mouse/keyboard combination.

There are many reasons to learn about the shell:

* Many bioinformatics tools can only be used through a command line interface, or 
have extra capabilities in the command line version that are not available in the GUI.
This is true, for example, of BLAST, which offers many advanced functions only accessible
to users who know how to use a shell.  
* The shell makes your work less boring. In bioinformatics you often need to do
the same set of tasks with a large number of files. Learning the shell will allow you to
automate those repetitive tasks and leave you free to do more exciting things.  
* The shell makes your work less error-prone. When humans do the same thing a hundred different times
(or even ten times), they're likely to make a mistake. Your computer can do the same thing a thousand times
with no mistakes.  
* The shell makes your work more reproducible. When you carry out your work in the command-line 
(rather than a GUI), your computer keeps a record of every step that you've carried out, which you can use 
to re-do your work when you need to. It also gives you a way to communicate unambiguously what you've done, 
so that others can check your work or apply your process to new data.  
* Many bioinformatic tasks require large amounts of computing power and can't realistically be run on your
own machine. These tasks are best performed using remote computers or cloud computing, which can only be accessed
through a shell.

In this lesson you will learn how to use the command line interface to move around in your file system. 

## Before you start

We will be working on "Wolfpack", the Garvan High Performance Cluster (HPC), but almost all of the competencies 
covered here translate directly to other high performance computing environments. Most of the commands will also
work on your own laptop, especially if you use Mac or Linux (or the [Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/faq)).
There are some "dialect" differences between different operating systems, but most of the basic concepts translate well.

Garvan staff and students can [request access](https://intranet.gimr.garvan.org.au/display/HPC/Connecting+to+Wolfpack) to use Wolfpack.
It may take a few days for your request to be approved, so allow plenty of time before the course starts.

Once you have received access, the steps required to connect to the cluster depend on your operating system. See [this Confluence page](https://intranet.gimr.garvan.org.au/display/BINF/Connecting+to+the+Cluster)) .

## How to access the shell

On a Mac or Linux machine, you can access a shell through a program called Terminal, which is already available
on your computer. If you're using Windows, you'll need to download a separate program to access the shell.
For this course, we recommend [PuTTY](https://www.putty.org/) for Windows users.

We will spend most of our time learning about the basics of the shell
by manipulating some experimental data. Some of the data we're going to be working with is quite large, and
we're also going to be using several bioinformatic packages in later
lessons to work with this data. 

## Logging on for the first time

When you log on to the cluster (either using PuTTY or using the "ssh" command from the Terminal), 
you will be greeted with something like this:

~~~
Last login: Tue Jun 30 21:53:23 2020 from 172.26.74.27
Rocks 6.2 (SideWinder)
Profile built 09:52 10-Apr-2020

Kickstarted 09:58 10-Apr-2020
-bash-4.1$
~~~
{: .output}

Here "-bash-4.1$" is a **prompt**, which shows us that the shell is waiting for input.
The text used for this prompt can be customised to include extra information, such as the
time, date or current working directory.
Later we will delve into **how** you can customise the prompt and other aspects of your work 
environment, but for now let's switch to a more useful setup.

Note1: Skip this next step if you have already configured your .bash_profile and .bashrc
Note2: If you don't know what I'm talking about, then don't worry -- the next step will take care of it for you.

Type the following command after the prompt. Take care with the spelling, including capitalisation.

~~~
$ source /share/ScratchGeneral/johree/course/setup_dot.sh
~~~
{: .bash}

If you mistype, you will get an error message. Otherwise, this script will create several files that
go some way towards customising your work environment. You should see output something like this:

~~~
Home quota...

  <GB> <soft> <hard> : <files> <soft> <hard> : <path to volume> <pan_identity(name)>
  0.77 500.00 600.00 :    3088 190000 200000 : /home/johree uid:10548(johree)

Scratch quota...

    <GB>  <soft>  <hard> : <files> <soft>  <hard> :      <path to volume> <pan_identity(name)>
  180.12 6000.00 7000.00 :   13198 900000 1000000 : /share/ScratchGeneral uid:111111(user)

user@dice02:~:$
~~~
{: .bash}

In fact, you will see this extra information about your disk quotas every time you log on from now
(unless you do some more customising yourself.) Also, you may have noticed that the prompt has changed.
Now the prompt tells you who is logged on (your Garvan user ID) and which node you are connected to.
Later in the course we will learn how to switch from a login node to a compute node, and so having
this information in the prompt to remind us where we are can be really useful.
The "~" tells you that you are in your home directory. This will change as you navigate through the file system.

As well as customising your work environment, you will also need to set up the data that you will be using for this course.
Type the following comand after the prompt. Again, you will need to be careful with your typing. You can speed things up AND 
reduce typos by taking advantage of "tab completion". Instead of typing "share", just type "sh" and then press the Tab key.
Similarly, type "Sc" and press Tab. (You still need to get capitalisation right...) If there are multiple candidates then 
pressing Tab once won't do anything, but if you press Tab a second time you will see a list of alternatives. In this case, 
type another letter or two until the first part is unique and then press Tab again.

~~~
$ source /share/ScratchGeneral/johree/course/setup.sh
~~~
{: .bash}

This "setup.sh" script creates a bunch of directories, copies data into them and then decompresses the data. The script 
prints out messages on the screen to show what it is doing. Most of these messages will probably be fairly cryptic at this stage,
so don't worry if you don't understand everything just yet. By the end of the course, most of this will make sense.
However, please alert the instructor if there are any **error** messages in the output. We want to make sure that you are set up
and ready to go before the course gets properly underway.

## Getting started

By now, the screen is full of text. Let's clear the screen (and our heads) with the  `clear` command. 

~~~
$ clear
~~~
{: .bash}

This will scroll your screen down to give you a fresh screen and will make it easier to read. 
You haven't lost any of the information on your screen. If you scroll up, you can see everything that has been output to your screen
up until this point.

> ## Tip
>
> If you like to use hot-key combinations you might be interested to know that clearing the console can also be achieved by pressing `Ctrl+L`. Feel free to try it and see for yourself.
{: .callout}

## Navigating your file system

The part of the operating system responsible for managing files and directories
is called the **file system**.
It organizes our data into files,
which hold information,
and directories (also called "folders"),
which hold files or other directories.

Several commands are frequently used to create, inspect, rename, and delete files and directories.



Let's find out where we are by running a command called `pwd`
(which stands for "print working directory").
At any moment, our **current working directory**
is our current default directory,
i.e.,
the directory that the computer assumes we want to run commands in,
unless we explicitly specify something else.
Here,
the computer's response is `/home/user`,
where "user" is your Garvan ID.

~~~
$ pwd
~~~
{: .bash}

~~~
/home/user
~~~
{: .output}

Let's look at how our file system is organized. We can see what files and subdirectories are in this directory by running `ls`,
which stands for "listing":

~~~
$ ls
~~~
{: .bash}

~~~
bio    course    data    scratch    shell_data    
~~~
{: .output}

`ls` prints the names of the files and directories in the current directory in
alphabetical order, arranged neatly into columns. 
We'll be working within the `shell_data` subdirectory, and creating new subdirectories, throughout this workshop.  

The command to change locations in our file system is `cd`, followed by a
directory name to change our working directory.
`cd` stands for "change directory".

Let's say we want to navigate to the `shell_data` directory we saw above.  We can
use the following command to get there:

~~~
$ cd shell_data
~~~
{: .bash}

Let's look at what is in this directory:

~~~
$ ls
~~~
{: .bash}

~~~
sra_metadata  untrimmed_fastq
~~~
{: .output}

We can make the `ls` output more comprehensible by using the **option** `-classify`,
which tells `ls` to add a trailing `/` to the names of directories:

~~~
$ ls --classify
~~~
{: .bash}

~~~
sra_metadata/  untrimmed_fastq/
~~~
{: .output}

Anything with a "/" after it is a directory. Things with a "*" after them are programs. 
Symbolic links have "@". If there are no decorations, it's a file.

`ls` has lots of other options. To find out what they are, we can type:

~~~
$ man ls
~~~
{: .bash}

`man` (short for manual) displays detailed documentation (also referred as man page or man file)
for `bash` commands. It is a powerful resource to explore `bash` commands, understand
their usage and flags. Some manual files are very long. You can scroll through the
file using your keyboard's down arrow or use the <kbd>Space</kbd> key to go forward one page
and the <kbd>b</kbd> key to go backwards one page. When you are done reading, hit <kbd>q</kbd>
to quit.

You'll notice that many options have a long form (such as "--classify") and short form (such as "-F"). 
The short form is often described as a **flag**.
The long forms are easier to remember and easier to read, while the short forms involve less typing.
I am a big fan of clarity over brevity, but choose between long and short forms depending on the situation.

> ## Challenge
> Use the `-l` option for the `ls` command to display more information for each item 
> in the directory. What is one piece of additional information this long format
> gives you that you don't see with the bare `ls` command?
>
> > ## Solution
> > ~~~
> > $ ls -l
> > ~~~
> > {: .bash}
> > 
> > ~~~
> > total 8
> > drwxr-x--- 2 dcuser dcuser 4096 Jul 30  2015 sra_metadata
> > drwxr-xr-x 2 dcuser dcuser 4096 Nov 15  2017 untrimmed_fastq
> > ~~~
> > {: .output}
> > 
> > The additional information given includes the name of the owner of the file,
> > when the file was last modified, and whether the current user has permission
> > to read and write to the file.
> > 
> {: .solution}
{: .challenge}

You don't need to learn all of these arguments, that's what the manual page
is for. You can (and should) refer to the manual page or other help files
as needed. You'll find yourself gradually learning the more common commands with time. 

On the other hand, it **is** possible to systematically learn all of the options of a command 
if you decide that would be helpful. Tools such as [Anki](https://apps.ankiweb.net/) are a great way to do this.

Let's go into the `untrimmed_fastq` directory and see what is in there.

~~~
$ cd untrimmed_fastq
$ ls -F
~~~
{: .bash}

~~~
SRR097977.fastq  SRR098026.fastq
~~~
{: .output}

This directory contains two files with `.fastq` extensions. FASTQ is a format
for storing information about sequencing reads and their quality.
We will be learning more about FASTQ files in a later lesson.

### Shortcut: Tab Completion

Typing out file or directory names can waste a
lot of time and it's easy to make typing mistakes. Instead we can use tab complete 
as a shortcut. When you start typing out the name of a directory or file, then
hit the <kbd>Tab</kbd> key, the shell will try to fill in the rest of the
directory or file name.

Return to your home directory:

~~~
$ cd
~~~
{: .bash}

then enter:

~~~
$ cd she<tab>
~~~
{: .bash}

The shell will fill in the rest of the directory name for
`shell_data`.

Now change directories to `untrimmed_fastq` in `shell_data`

~~~
$ cd shell_data
$ cd untrimmed_fastq
~~~
{: .bash}

Using tab complete can be very helpful. However, it will only autocomplete
a file or directory name if you've typed enough characters to provide
a unique identifier for the file or directory you are trying to access.

For example, if we now try to list the files which names start with `SR`
by using tab complete:  

~~~
$ ls SR<tab>
~~~
{: .bash}

The shell auto-completes your command to `SRR09`, because all file names in 
the directory begin with this prefix. When you hit
<kbd>Tab</kbd> again, the shell will list the possible choices.

~~~
$ ls SRR09<tab><tab>
~~~
{: .bash}

~~~
SRR097977.fastq  SRR098026.fastq
~~~
{: .output}

Tab completion can also fill in the names of programs, which can be useful if you
remember the beginning of a program name.

~~~
$ pw<tab><tab>
~~~
{: .bash}

~~~
pwck      pwconv    pwd       pwdx      pwunconv
~~~
{: .output}

Displays the name of every program that starts with `pw`. 

## Summary

We now know how to move around our file system using the command line.
This gives us an advantage over interacting with the file system through
a GUI as it allows us to work on a remote server, carry out the same set of operations 
on a large number of files quickly, and opens up many opportunities for using 
bioinformatic software that is only available in command line versions. 

In the next few episodes, we'll be expanding on these skills and seeing how 
using the command line shell enables us to make our workflow more efficient and reproducible.
