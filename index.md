---
layout: lesson
---

{% include gh_variables.html %}


Command line interface (OS shell) and graphic user interface (GUI) are different ways of interacting with a computer's operating system. The shell is a program that presents a command line interface which allows you to control your computer using commands entered with a keyboard instead of controlling graphical user interfaces (GUIs) with a mouse/keyboard combination.

There are quite a few reasons to start learning about the shell:

- For most bioinformatics tools, you have to use the shell. There is no graphical interface. If you want to work in metagenomics or genomics you're going to need to use the shell.
- The shell gives you power. The command line gives you the power to do your work more efficiently and more quickly. When you need to do things tens to hundreds of times, knowing how to use the shell is transformative.
- To use remote computers or cloud computing, you need to use the shell.

> ## Getting Started
>
> This lesson assumes no prior experience with the tools covered in the workshop. 
> However, learners are expected to have some familiarity with biological concepts,
> including the 
> concept of genomic variation within a population. Participants should bring their laptops and plan to participate actively. 
>
> This lesson is intended to be run on "Wolfpack", the Garvan HPC. Garvan staff and students can see [this Confluence page](https://intranet.gimr.garvan.org.au/display/BINF/Connecting+to+the+Cluster) to learn about accessing the cluster.
> In order to access the data files used in this lesson you will need to run a setup script.
> This will be one of the first things that we do in class.
> If you working through this on your own then connect to the cluster and run the following script:
> ~~~
> bash /share/ScratchGeneral/johree/course_materials/setup.sh
> ~~~
{: .prereq}
