---
title: "Redirection"
teaching: 30
exercises: 15
questions:
- "How can I search within files?"
- "How can I combine existing commands to do new things?"
objectives:
- "Employ the `grep` command to search for information within files."
- "Print the results of a command to a file."
- "Construct command pipelines with two or more stages."
- "Use `for` loops to run the same command for several input files."
keypoints:
- "`grep` is a powerful search tool with many options for customization."
- "`>`, `>>`, and `|` are different ways of redirecting output."
- "`command > file` redirects a command's output to a file."
- "`command >> file` redirects a command's output to a file without overwriting the existing contents of the file."
- "`command_1 | command_2` redirects the output of the first command as input to the second command."
- "`for` loops are used for iteration."
- "`basename` gets rid of repetitive parts of names."
---

## Searching files

We discussed in a previous episode how to search within a file using `less`. We can also
search within files without even opening them, using `grep`. `grep` is a command-line
utility for searching plain-text files for lines matching a specific set of 
characters (sometimes called a string) or a particular pattern 
(which can be specified using something called regular expressions). We're not going to work with 
regular expressions in this lesson, and are instead going to specify the strings 
we are searching for.
Let's give it a try!

> ## Nucleotide abbreviations
> 
> The four nucleotides that appear in DNA are abbreviated `A`, `C`, `T` and `G`. 
> Unknown nucleotides are represented with the letter `N`. An `N` appearing
> in a sequencing file represents a position where the sequencing machine was not able to 
> confidently determine the nucleotide in that position. You can think of an `N` as being aNy 
> nucleotide at that position in the DNA sequence. 
> 
{: .callout}

We'll search for strings inside of our fastq files. Let's first make sure we are in the correct 
directory:

~~~
$ cd ~/shell_data/untrimmed_fastq
~~~
{: .bash}

Suppose we want to see how many reads in our file have really bad segments containing 10 consecutive unknown nucleotides (Ns).

> ## Determining quality
> 
> In this lesson, we're going to be manually searching for strings of `N`s within our sequence
> results to illustrate some principles of file searching. It can be really useful to do this
> type of searching to get a feel for the quality of your sequencing results, however, in your 
> research you will most likely use a bioinformatics tool that has a built-in program for
> filtering out low-quality reads. You'll learn how to use one such tool in 
> [a later lesson](https://datacarpentry.org/wrangling-genomics/02-quality-control/index.html).
> 
{: .callout}

Let's search for the string NNNNNNNNNN in the SRR098026 file:
~~~
$ grep NNNNNNNNNN SRR098026.fastq
~~~
{: .bash}

This command returns a lot of output to the terminal. Every single line in the SRR098026 
file that contains at least 10 consecutive Ns is printed to the terminal, regardless of how long or short the file is. 
We may be interested not only in the actual sequence which contains this string, but 
in the name (or identifier) of that sequence. We discussed in a previous lesson 
that the identifier line immediately precedes the nucleotide sequence for each read
in a FASTQ file. We may also want to inspect the quality scores associated with
each of these reads. To get all of this information, we will return the line 
immediately before each match and the two lines immediately after each match.

We can use the `--before-context` argument for grep to return a specific number of lines before
each match. The `--after-context` argument returns a specific number of lines after each matching line. 
Here we want the line *before* and the two lines *after* each 
matching line, so we add `--before-context=1 --after-context=2` to our grep command:

~~~
$ grep --before-context=1 --after-context=2 NNNNNNNNNN SRR098026.fastq
~~~
{: .bash}

The short version of this command is easier to type but harder to remember:
~~~
$ grep -B1 -A2 NNNNNNNNNN SRR098026.fastq
~~~
{: .bash}

One of the sets of lines returned by this command is: 

~~~
@SRR098026.177 HWUSI-EAS1599_1:2:1:1:2025 length=35
CNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
+SRR098026.177 HWUSI-EAS1599_1:2:1:1:2025 length=35
#!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
~~~
{: .output}

> ## Exercise
>
> 1. Search for the sequence `GNATNACCACTTCC` in the `SRR098026.fastq` file.
> Have your search return all matching lines and the name (or identifier) for each sequence
> that contains a match.
> 
> 2. Search for the sequence `AAGTT` in both FASTQ files.
> Have your search return all matching lines and the name (or identifier) for each sequence
> that contains a match.
> 
> > ## Solution  
> > 1. `grep --before-context=1 GNATNACCACTTCC SRR098026.fastq` 
> > 
> >     ```
> >     @SRR098026.245 HWUSI-EAS1599_1:2:1:2:801 length=35
> >     GNATNACCACTTCCAGTGCTGANNNNNNNGGGATG
> >     ```
> > 
> > 2. `grep --before-context=1 AAGTT *.fastq`
> >
> >     ```
> > SRR097977.fastq-@SRR097977.11 209DTAAXX_Lenski2_1_7:8:3:247:351 length=36
> > SRR097977.fastq:GATTGCTTTAATGAAAAAGTCATATAAGTTGCCATG
> > --
> > SRR097977.fastq-@SRR097977.67 209DTAAXX_Lenski2_1_7:8:3:544:566 length=36
> > SRR097977.fastq:TTGTCCACGCTTTTCTATGTAAAGTTTATTTGCTTT
> > --
> > SRR097977.fastq-@SRR097977.68 209DTAAXX_Lenski2_1_7:8:3:724:110 length=36
> > SRR097977.fastq:TGAAGCCTGCTTTTTTATACTAAGTTTGCATTATAA
> > --
> > SRR097977.fastq-@SRR097977.80 209DTAAXX_Lenski2_1_7:8:3:258:281 length=36
> > SRR097977.fastq:GTGGCGCTGCTGCATAAGTTGGGTTATCAGGTCGTT
> > --
> > SRR097977.fastq-@SRR097977.92 209DTAAXX_Lenski2_1_7:8:3:353:318 length=36
> > SRR097977.fastq:GGCAAAATGGTCCTCCAGCCAGGCCAGAAGCAAGTT
> > --
> > SRR097977.fastq-@SRR097977.139 209DTAAXX_Lenski2_1_7:8:3:703:655 length=36
> > SRR097977.fastq:TTTATTTGTAAAGTTTTGTTGAAATAAGGGTTGTAA
> > --
> > SRR097977.fastq-@SRR097977.238 209DTAAXX_Lenski2_1_7:8:3:592:919 length=36
> > SRR097977.fastq:TTCTTACCATCCTGAAGTTTTTTCATCTTCCCTGAT
> > --
> > SRR098026.fastq-@SRR098026.158 HWUSI-EAS1599_1:2:1:1:1505 length=35
> > SRR098026.fastq:GNNNNNNNNCAAAGTTGATCNNNNNNNNNTGTGCG
> >     ```
> > 
> {: .solution}
{: .challenge}

## Redirecting output

`grep` allowed us to identify sequences in our FASTQ files that match a particular pattern. 
All of these sequences were printed to our terminal screen, but in order to work with these 
sequences and perform other operations on them, we will need to capture that output in some
way. 

We can do this with something called "redirection". The idea is that
we are taking what would ordinarily be printed to the terminal screen and redirecting it to another location. 
In our case, we want to print this information to a file so that we can look at it later and 
use other commands to analyze this data.

The command for redirecting output to a file is `>`.

Let's try out this command and copy all the records (including all four lines of each record) 
in our FASTQ files that contain 
'NNNNNNNNNN' to another file called `bad_reads.txt`.

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR098026.fastq > bad_reads.txt
~~~
{: .bash}

(Here and below we are using the short forms of the `--before-context` and `--after-context` options, namely `-B` and `-A`.
Note that you don't need an `=` with the short form, but inappropriate spaces will confuse the shell interpreter in both cases.)

> ## File extensions
> 
> You might be confused about why we're naming our output file with a `.txt` extension. After all,
> it will be holding FASTQ formatted data that we're extracting from our FASTQ files. Won't it 
> also be a FASTQ file? The answer is, yes - it will be a FASTQ file and it would make sense to 
> name it with a `.fastq` extension. However, using a `.fastq` extension will lead us to problems
> when we move to using wildcards later in this episode. We'll point out where this becomes
> important. For now, it's good that you're thinking about file extensions! 
> 
{: .callout}


The prompt should sit there a little bit, and then it should look like nothing
happened. But type `ls`. You should see a new file called `bad_reads.txt`. 

We can check the number of lines in our new file using a command called `wc`. 
`wc` stands for **word count**. This command counts the number of words, lines, and characters
in a file. 

~~~
$ wc bad_reads.txt
~~~
{: .bash}

~~~
  537  1073 23217 bad_reads.txt
~~~
{: .output}

This will tell us the number of lines, words and characters in the file. If we
want only the number of lines, we can use the `-l` flag for `lines`.

~~~
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
537 bad_reads.txt
~~~
{: .output}

> ## Exercise
>
> How many sequences in `SRR098026.fastq` contain at least 3 consecutive Ns?
>
>> ## Solution
>>  
>>
>> ~~~
>> $ grep NNN SRR098026.fastq > bad_reads.txt
>> $ wc -l bad_reads.txt
>> ~~~
>> {: .bash}
>> 
>> ~~~
>> 249
>> ~~~
>> {: .output}
>>
> {: .solution}
{: .challenge}

We might want to search multiple FASTQ files for sequences that match our search pattern.
However, we need to be careful, because each time we use the `>` command to redirect output
to a file, the new output will replace the output that was already present in the file. 
This is called "overwriting" and, just like you don't want to overwrite your video recording
of your kid's first birthday party, you also want to avoid overwriting your data files.

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR098026.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
537 bad_reads.txt
~~~
{: .output}

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR097977.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
0 bad_reads.txt
~~~
{: .output}

Here, the output of our second  call to `wc` shows that we no longer have any lines in our `bad_reads.txt` file. This is 
because the second file we searched (`SRR097977.fastq`) does not contain any lines that match our
search sequence. So our file was overwritten and is now empty.

We can avoid overwriting our files by using the command `>>`. `>>` is known as the "append redirect" and will 
append new output to the end of a file, rather than overwriting it.

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR098026.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
537 bad_reads.txt
~~~
{: .output}

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR097977.fastq >> bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
537 bad_reads.txt
~~~
{: .output}

The output of our second call to `wc` shows that we have not overwritten our original data. 

We can also do this with a single line of code by using a wildcard: 

~~~
$ grep -B1 -A2 NNNNNNNNNN *.fastq > bad_reads.txt
$ wc -l bad_reads.txt
~~~
{: .bash}

~~~
537 bad_reads.txt
~~~
{: .output}

> ## File extensions - part 2
> 
> This is where we would have trouble if we were naming our output file with a `.fastq` extension. 
> If we already had a file called `bad_reads.fastq` (from our previous `grep` practice) 
> and then ran the command above using a `.fastq` extension instead of a `.txt` extension, `grep`
> would give us a warning. 
> 
> ~~~
> grep -B1 -A2 NNNNNNNNNN *.fastq > bad_reads.fastq
> ~~~
> {: .bash}
> 
> ~~~
> grep: input file ‘bad_reads.fastq’ is also the output
> ~~~
> {: .output}
> 
> `grep` is letting you know that the output file `bad_reads.fastq` is also included in your
> `grep` call because it matches the `*.fastq` pattern. Be careful with this as it can lead to
> some unintended results.
> 
> Another alternative would be to use the ".fastq" extension, but to save the results in a different directory.
> Separating raw data from processed data is generally a good thing.
> Note that you'll need to make the directory first if it doesn't exist already.
>
> ~~~
> mkdir results
> grep -B1 -A2 NNNNNNNNNN *.fastq > results/bad_reads.fastq
> ~~~
> {: .bash}
{: .callout}

## Pipes

Since we might have multiple different criteria we want to search for, 
creating a new output file each time has the potential to clutter up our workspace. We also
thus far haven't been interested in the actual contents of those files, only in the number of 
reads that we've found. We created the files to store the reads and then counted the lines in 
the file to see how many reads matched our criteria. There's a way to do this, however, that
doesn't require us to create these intermediate files - the pipe command (`|`).

This is probably not a key on
your keyboard you use very much, so let's all take a minute to find that key. For the standard QWERTY keyboard
layout, the `|` character can be found using the key combination

- <kbd>Shift</kbd>+<kbd>\</kbd>

What `|` does is take the output that is scrolling by on the terminal and uses that output as input to another command. 
When our output was scrolling by, we might have wished we could slow it down and
look at it, like we can with `less`. Well it turns out that we can! We can redirect our output
from our `grep` call through the `less` command.

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR098026.fastq | less
~~~
{: .bash}

We can now see the output from our `grep` call within the `less` interface. We can use the up and down arrows 
to scroll through the output and use `q` to exit `less`.

If we don't want to create a file before counting lines of output from our `grep` search, we could directly pipe
the output of the grep search to the command `wc -l`. This can be helpful for investigating your output if you are not sure
you would like to save it to a file. 

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR098026.fastq | wc -l 
~~~
{: .bash}

Once again, the **output** from the **left** hand side of the pipe operator `|` becomes the **input** for the **right** hand side.
Notice how we didn't have to specify a file name for the `wc` command here? That's because `wc` processes the output from `grep`
instead of a regular file.

Because we asked `grep` for all four lines of each FASTQ record, we need to divide the output by
four to get the number of sequences that match our search pattern. Since 537 / 4 = 134.25 and we
are expecting an integer number of records, there is something added or missing in `bad_reads.txt`. 
If we explore `bad_reads.txt` using `less`, we might be able to notice what is causing the uneven 
number of lines. Luckily, this issue happens by the end of the file so we can also spot it with `tail`.

~~~
$ tail bad_reads.txt
~~~
{: .bash}

~~~
@SRR098026.133 HWUSI-EAS1599_1:2:1:0:1978 length=35
ANNNNNNNNNTTCAGCGACTNNNNNNNNNNGTNGN
+SRR098026.133 HWUSI-EAS1599_1:2:1:0:1978 length=35
#!!!!!!!!!##########!!!!!!!!!!##!#!
--
@SRR098026.177 HWUSI-EAS1599_1:2:1:1:2025 length=35
CNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN
+SRR098026.177 HWUSI-EAS1599_1:2:1:1:2025 length=35
#!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
~~~
{: .output}

The fifth line in the output displays "--" which is the default action for `grep` to separate groups of 
lines matching the pattern, and indicate groups of lines which did not match the pattern so are not displayed. 
To fix this issue, we can redirect the output of grep to a second instance of `grep` as follows.

~~~
$ grep -B1 -A2 NNNNNNNNNN SRR098026.fastq | grep --invert-match '^--' > bad_reads.fastq
~~~
{: .bash}

The `--invert-match` option in the second `grep` causes `grep` to display only the 
lines which do **not** match the searched pattern, in this case `'^--'`. The caret (`^`) is an **anchoring** 
character matching the beginning of the line, and the pattern has to be enclose by single quotes so `grep` does 
not interpret the pattern as an extended option (starting with --). The short form for `--invert-match` is `-v`.

> ## Custom `grep` control
> 
> Use `man grep` to read more about other options to customize the output of `grep` including extended options, 
> anchoring characters, and much more.
> 
{: .callout}

Redirecting output is often not intuitive, and can take some time to get used to. Once you're 
comfortable with redirection, however, you'll be able to combine any number of commands to
do all sorts of exciting things with your data!

None of the command line programs we've been learning
do anything all that impressive on their own, but when you start chaining
them together, you can do some really powerful things very
efficiently. Here are a couple of examples.

Remember how you can use the `history` command to view the entire history of every command that you've ever typed?
Well, before you know it that list of commands will be hundreds or even thousands of lines long. Piping the **output** of
`history` into the `less` command makes it much easier to navigate. You can scroll down with the <kbd>space</kbd> key, jump to the
end of the list with <kbd>G</kbd> or search for key words with <kbd>/</kbd>. 

~~~
$ history | less
~~~
{: .bash}

Another example is searching your command history for particular words, and then displaying all the matching records. You
can achieve this by piping `history` through `grep`. Can't remember where you moved that file, or even exactly what it was called? 
The following command will show you all of the `mv` commands that you have ever entered.

~~~
$ history | grep mv
~~~
{: .bash}

You can really power up this last trick by typing **comments** into the shell. `bash` treats the `#`
character as a comment character. Any text on a line after a `#` is ignored by
bash when evaluating the text as code. Comments are used extensively in scripts (see next chapter) to document the code,
but you can also type comments directly into the command line as a free text record of what you just did.
The comment won't be executed as code, but it will be saved in the history log. 
This allows you to search back through your history using free text, *even when you can't remember the exact name of the command that you used*.
There is no need to document every `ls` or `cd` that you do, but making comments for important steps is a good practice.

We'll see a few more examples of piping output through `less` and `grep`, but the idea generalises quite well. 
Anytime a command spits out more output than you can scan easily, consider using pipes with `less`, `grep` or maybe `head` or `tail`.

> # File manipulation and more practices with pipes
> 
> To practice a bit more with the tools we’ve added to our tool kit so far and learn a few extra ones you can follow [this extra lesson](https://datacarpentry.org/shell-genomics/Extra_lesson/index.html) which uses the SRA metadata file. 
> 
{: .callout}

## Aliases

Some combinations like `history | grep` are so useful that you will find yourself using them all the time.
Rather than type them out each time, you can define an `alias` as a kind of a short hand.

~~~
$ alias hgrep="history | grep"
~~~
{: .bash}

This code snippet defines a new alias.
After typing this snippet at the command prompt, you can now simply type "hgrep mv" instead of "history | grep mv".
The result will be the same.
(In this case, searching for all of the times that you have used the `mv` command.)

Defining aliases is also handy for commands with a lot of options that are hard to remember (but where you generally use the same options each time).
For example, when I list the files in a directory I usually like to see **all** the files, including any hidden files.
I also like to see subdirectories listed on top, and extra information such as permissions etc.
I like to see file sizes displayed in human readable format, and I like directories, executablees and symbolic links to be "classified" using symbols after the file name.
I can achieve this with the `ls` command the following options:

~~~
$ ls -AlhF --group-directories-first
~~~
{: .bash}

**Or** I can define an alias to save my typing all that every time. 

~~~
$ alias ll="ls -AlhF --group-directories-first"
$ ll
~~~
{: .bash}

If you type "alias" you will see a list of the aliases that have already been defined.
Some of these won't make sense yet, but by the end of the course you should be able to decipher all of them.

~~~
$ alias
~~~
{: .bash}

~~~
alias ll="ls -AlhF --directories-first"
alias la="ls -aF"
alias cdscr="cd /share/ScratchGeneral/${USER}" 
alias hgrep="history | grep"
alias mods="module avail 2>&1 | less"
alias modgrep="module avail 2>&1 | grep"
alias coursehome="cd /share/ScratchGeneral/${USER}/course"
alias shrub="tree -L 3"
~~~
{: .output}

All of the alias definitions are in the form "alias A=B".
Once an alias has been defined, you can just type "A" instead of "B".  

Unfortunately, if you define an alias directly at the command line it is only valid until you log out.
So where did these aliases come from?
Answer: they are defined in a file called ".bash_aliases" in your home directory.
(Note the dot at the start of the file name.)
This file was created as part of the setup for this course, and the alias definitions in this file are loaded each time you log in.
(Ask me how this works.)
This means that you can create **permanent** alias definitions by adding them to this file.
We'll look at how to edit files from the command line in the next chapter, but here is a quick preview.


~~~
$ nano ~/.bash_aliases
~~~
{: .bash}
(Type <kbd>Control</kbd>+<kbd>x</kbd> to exit.)

The pay off for the extra work of editing this file is that these alias definitions are now **portable**.
By copying this file to another Linux environment (such as your Mac or the National Compute Infrastructure in Canberra) you can take all of your favourite shortcuts with you.  
This is just one of many ways that you can customise your work environment to suit your habits and preferences, reducing tedium and making you more efficient.  

Feel free to edit ".bash_aliases" to change the definitions to something easier for you to remember.
For example, you might prefer "history_search" rather than "hgrep", or you might have different preferences for how you like your files listed.
(Alphabetical order? Most recently modified first? Last? Consult the `man` page for the options.)

## Variables

As well as using the shell to issue commands, we can also do some basic programming to automate tasks. Actually, shell programming can
get quite sophisticated, but once the level of complexity crosses a certain threshold you are probably better off using a general-purpose
programming language such as Python. Different people have different opinions about exactly where that threshold lies, and it partly 
depends on your skill and comfort level with each toolkit. 

A key concept in programming is the idea of a "variable". You can think of variables as "named buckets" that contain values. To use them,
we have to learn to put values in and take values out. Let's start with the latter.

A bunch of variables are created automatically when you first log in. These variables are stored in what is known as the "shell environment",
and they are available without you having to do anything at all. The following commands all include a variable, indicated by the `$`.
See if you can figure out what kind of information each variable stores:

~~~
$ echo $USER
$ echo $SHELL
$ echo $HOSTNAME
$ ls $HOME
~~~
{: .bash}

The `$` tells the shell interpreter to treat the **variable** as a variable name and substitute its value in its place, 
rather than treat it as text or an external command. In shell programming, this is usually called "expanding" the variable.

There are dozens of environment variables, but probably only a handful that you are like to use regularly. 
You can use the `printenv` command to see a full list, but because there are so many we'll pipe the results through `less`. 
(Remember to press <kbd>space</kbd> to scroll and press <kbd>q</kbd> to exit `less` when you finish.)

~~~
$ printenv | less
~~~
{: .bash}

More interestingly, you can also create your **own** variables. The basic format is `variable_name=value`, as shown in the examples below.
Note that there is **no** `$` in front of the name when you **define** a variable and there is no space on either side of the `=` sign. Also,
there are no spaces inside the variable name. If you need to use spaces in a value, enclose the whole value in quotes (`"` or `'`). 
There is a subtle difference between single and double-quotes that I will elaborate on if anybody is curious enough. 
Although it is conventional to use ALL CAPS to define **environment** variables, user-defined variable names can be upper or lower case,
or any mix of both. Variable names are case-sensitive though, so make sure that you match the case used when the variable was defined.

~~~
$ foo=abc
$ echo $foo
$ bar=123
$ echo $bar
$ Greeting="Good morning"
$ echo Greeting
~~~
{: .bash}

Variables are shell programming are a lot more limited than other programming languages that you may be familiar with. Still, there are
a few subtleties that you will need to wrap your head around before too long, so I recommend reading a thorough discussion 
(such as [this tutorial](https://ryanstutorials.net/bash-scripting-tutorial/bash-variables.php)) either now or next time you start 
to get confused about variables. Best to get it sorted out in your mind early on.

We will tackle a few subtleties now. Sometimes, we want to expand a variable without any whitespace to its right.
Suppose we have a variable named `foo` that contains the text `abc`, and would
like to expand `foo` to create the text `abcEFG`.

~~~
$ foo=abc
$ echo foo is $foo
foo is abc
$ echo foo is $fooEFG      
foo is
~~~
{: .bash}

The interpreter is trying to expand a variable named `fooEFG`, which (probably)
doesn't exist. We can avoid this problem by enclosing the variable name in 
braces (`{` and `}`, sometimes called "squiggle braces"). 

~~~
$ foo=abc
$ echo foo is $foo
foo is abc
$ echo foo is ${foo}EFG      # now it works!
foo is abcEFG
~~~
{: .bash}

Personally, I am in the habit of (almost) always using the `${variable}` construction, at least when the variable reference
is part of a more complex expression. You can get away with plain old `$` when the variable stands alone.

## Using the output of commands with $( )

The `$` symbol is a bit overused in `bash`. We've seen it used for the command prompt, and now also as a way to extract the value of a variable.
You can also use it with the `$( )` pattern to execute a command and then use the result as part of a larger expression, including saving that result in a variable.
That may sound a little abstract -- a concrete example should make it clearer:

~~~
$ file_list=$( ls ~/shell_data )
$ echo $file_list
~~~
{: .bash}

In the first line above we are executing a command inside the `$( )` pattern, in this case the `ls` command listing the content of the `shell_data` directory.
The output of this command is a string of text, and this string is stored as the value of the variable `file_list`. 
We can then extract this value later. Here we just `echo` the value to the screen, but you can do much more interesting things as we will see below.

The trick to deciphering `$` is to look at the immediate context:
- `$` in front of a variable name **extracts** the value of that variable
- `$` together with `{ }` in a `${variable}` pattern also **extracts** the value of that variable
- `$` together with `( )` in a `${ command }` pattern first **runs** the command and then uses the **result** of the command as part of a larger expression. 
The larger expression could be a variable assignment (as in the example above) but not necessarily (as we shall see below).


## Writing for loops

Loops are key to productivity improvements through automation as they allow us to execute commands repeatedly. 
Similar to wildcards and tab completion, using loops also reduces the amount of typing (and typing mistakes). 
Loops are helpful when performing operations on groups of sequencing files, such as unzipping or trimming multiple files. 
We will use loops for these purposes in subsequent analyses, but will cover the basics of them for now.

When the shell sees the keyword `for`, it knows to repeat a command (or group of commands) once for each item in a list. 
Each time the loop runs (called an iteration), an item in the list is assigned in sequence to the **variable**, and the commands inside the loop are executed, before moving on to the next item in the list. 
Inside the loop, we call for the variable's value by putting `$` in front of it. 

Let's write a for loop to show us the first two lines of the fastq files we downloaded earlier. 
You will notice the shell prompt changes from `$` to `>` and back again as we were typing in our loop. 
The second prompt, `>`, is different to remind us that we haven’t finished typing a complete command yet. 
A semicolon, `;`, can be used to separate two commands written on a single line.

~~~
$ cd ../untrimmed_fastq/
~~~
{: .bash}

~~~
$ for filename in *.fastq
> do
> head -n 2 ${filename}
> done
~~~
{: .bash}

The for loop begins with the formula `for <variable> in <group to iterate over>`. In this case, the word `filename` is designated 
as the variable to be used over each iteration. In our case `SRR097977.fastq` and `SRR098026.fastq` will be substituted for `filename` 
because they fit the pattern of ending with .fastq in the directory we've specified. The next line of the for loop is `do`. The next line is 
the code that we want to execute. We are telling the loop to print the first two lines of each variable we iterate over. Finally, the
word `done` ends the loop.

After executing the loop, you should see the first two lines of both fastq files printed to the terminal. Let's create a loop that 
will save this information to a file.

Note: the ">" symbols below indicate that the line is a continuation of the previous line.
You might not see these symbols, depending on your configuration.
In particular, if you are using `ble.sh` for auto-completion, you might see "MULTILINE" displayed below your prompt.
This just means that the command is being entered over multiple lines.
In this case, pressing "RETURN" (or "ENTER") just adds another line -- to actually execute the loop you will need to press `Control-J`.

~~~
$ for filename in *.fastq
> do
> head -n 2 ${filename} >> seq_info.txt
> done
~~~
{: .bash}

When writing a loop, you will not be able to return to previous lines once you have pressed Enter. Remember that we can cancel the current command using

- <kbd>Ctrl</kbd>+<kbd>C</kbd>

If you notice a mistake that is going to prevent your loop for executing correctly.

Note that we are using `>>` to append the text to our `seq_info.txt` file. If we used `>`, the `seq_info.txt` file would be rewritten
every time the loop iterates, so it would only have text from the last variable used. Instead, `>>` adds to the end of the file.

## Using Basename in for loops
Basename is a function in UNIX that is helpful for removing a uniform part of a name from a list of files. In this case, we will use basename to remove the `.fastq` extension from the files that we’ve been working with. 

~~~
$ basename SRR097977.fastq .fastq
~~~
{: .bash}

We see that this returns just the SRR accession, and no longer has the .fastq file extension on it.

~~~
SRR097977
~~~
{: .output}

If we try the same thing but use `.fasta` as the file extension instead, nothing happens. This is because basename only works when it exactly matches a string in the file.

~~~
$ basename SRR097977.fastq .fasta
~~~
{: .bash}

~~~
SRR097977.fastq
~~~
{: .output}

Basename is really powerful when used in a for loop. It allows to access just the file prefix, which you can use to name things. Let's try this.

Inside our for loop, we create a new name variable. 
We call the basename function inside the parenthesis, passing in our variable name from the for loop, in this case `${filename}`.
Finally we state that `.fastq` should be removed from the file name. 
It’s important to note that we’re not changing the actual files, we’re creating a new variable called "name". 
The line > echo $name will print to the terminal the variable name each time the for loop runs. 
Because we are iterating over two files, we expect to see two lines of output.

~~~
$ for filename in *.fastq
> do
> name=$(basename ${filename} .fastq)
> echo ${name}
> done
~~~
{: .bash}

Let's unpack `name=$(basename ${filename} .fastq)`. 
The first `$` is actually part of a `$( command )` pattern. 
In this case the command is `basename ${filename} .fastq`. 
So the second `$` is part of a `${variable}` pattern. 
The value of the `filename` variable is "expanded" into the `basename` command before the command is executed.
Finally, the result of the `basename` command is stored in the `name` variable.
Later, when we want to **use** the value of the `name` variable, we'll need to stick a `$` in front as either `$name` or perhaps `${name}`.

> ## Exercise
>
> Print the file prefix of all of the `.txt` files in our current directory.
>
>> ## Solution
>>  
>>
>> ~~~
>> $ for filename in *.txt
>> > do
>> > name=$(basename ${filename} .txt)
>> > echo ${name}
>> > done
>> ~~~
>> {: .bash}
>>
> {: .solution}
{: .challenge}

One way this is really useful is to move files. 
Let's rename all of our .txt files using `mv` so that they have the years on them, which will document when we created them. 

~~~
$ for filename in *.txt
> do
> name=$(basename ${filename} .txt)
> mv ${filename}  ${name}_2020.txt
> done
~~~
{: .bash}


> ## Exercise
>
> Remove `_2020` from all of the `.txt` files. 
>
>> ## Solution
>>  
>>
>> ~~~
>> $ for filename in *_2020.txt
>> > do
>> > name=$(basename ${filename} _2020.txt)
>> > mv ${filename} ${name}.txt
>> > done
>> ~~~
>> {: .bash}
>>
> {: .solution}
{: .challenge}


