---
title: "Scripts, variables, and loops"
teaching: 45
exercises: 10
questions:
- How do I turn a set of commands into a program?
objectives:
- Write a shell script
- Understand and manipulate UNIX permissions
- Understand shell variables and how to use them
- Write a simple "for" loop.
keypoints:
- A shell script is just a list of bash commands in a text file.
- To make a shell script file executable, run `chmod +x script.sh`.
---

We now know a lot of UNIX commands! Wouldn't it be great if we could save
certain commands so that we could run them later or not have to type them out
again? As it turns out, this is straightforward to do. A "shell script" is
essentially a text file containing a list of UNIX commands to be executed in a
sequential manner. These shell scripts can be run whenever we want, and are a
great way to automate our work.

## Writing a Script

So how do we write a shell script, exactly? It turns out we can do this with a
text editor. Start editing a file called "demo.sh" (to recap, we can do this
with `nano demo.sh`). The ".sh" is the standard file extension for shell
scripts that most people use (you may also see ".bash" used).

Our shell script will have two parts:

* On the very first line, add `#!/bin/bash`. The `#!` (pronounced "hash-bang")
  tells our computer what program to run our script with. In this case, we are
  telling it to run our script with our command-line shell (what we've been
  doing everything in so far). If we wanted our script to be run with something
  else, like Perl, we could add `#!/usr/bin/perl`
* Now, anywhere below the first line, add `echo "Our script worked!"`. When our
  script runs, `echo` will happily print out `Our script worked!`.

Our file should now look like this:

```
#!/bin/bash

echo "Our script worked!"
```
{: .language-bash}

Ready to run our program?
Let's try running it:

```
$ demo.sh
```
{: .language-bash}

```
bash: demo.sh: command not found...
```
{: .error}

Strangely enough, Bash can't find our script. As it turns out, Bash will only
look in certain directories for scripts to run. To run anything else, we need
to tell Bash exactly where to look. To run a script that we wrote ourselves, we
need to specify the full path to the file, followed by the filename. We could
do this one of two ways: either with our absolute path `{{
site.workshop_host_homedir }}/yourUserName/demo.sh`, or with the relative path
`./demo.sh`.

```
$ ./demo.sh
```
{: .language-bash}

```
bash: ./demo.sh: Permission denied
```
{: .error}

There's one last thing we need to do. Before a file can be run, it needs
"permission" to run. Let's look at our file's permissions with `ls -l`:

```
$ ls -l
```
{: .language-bash}

```
-rw-rw-r-- 1 yourUsername tc001 12534006 Jan 16 18:50 bash-lesson.tar.gz
-rw-rw-r-- 1 yourUsername tc001       40 Jan 16 19:41 demo.sh
-rw-rw-r-- 1 yourUsername tc001 77426528 Jan 16 18:50 dmel-all-r6.19.gtf
-rw-r--r-- 1 yourUsername tc001   721242 Jan 25  2016 dmel_unique_protein_is...
drwxrwxr-x 2 yourUsername tc001     4096 Jan 16 19:16 fastq
-rw-r--r-- 1 yourUsername tc001  1830516 Jan 25  2016 gene_association.fb.gz
-rw-rw-r-- 1 yourUsername tc001       15 Jan 16 19:17 test.txt
-rw-rw-r-- 1 yourUsername tc001      245 Jan 16 19:24 word_counts.txt
```
{: .output}

That's a huge amount of output: a full listing of everything in the directory.
Let's see if we can understand **what each field of a given row represents**,
working left to right.

1. **Permissions:** On the very left side, there is a string of the characters
   `d`, `r`, `w`, `x`, and `-`. The `d` indicates if something is a directory
   (there is a `-` in that spot if it is not a directory). The other `r`, `w`,
   `x` bits indicate permission to **R**ead, **W**rite, and e**X**ecute a file.
   There are three fields of `rwx` permissions following the spot for `d`. If a
   user is missing a permission to do something, it's indicated by a `-`.
   1. The first set of `rwx` are the permissions that the owner has (in this
      case the owner is `yourUsername`).
   1. The second set of `rwx`s are permissions that other members of the
      owner's group share (in this case, the group is named `tc001`).
   1. The third set of `rwx`s are permissions that anyone else with access to
      this computer can do with a file. Though files are typically created with
      read permissions for everyone, typically the permissions on your home
      directory prevent others from being able to access the file in the first
      place.
2. **References:** This counts the number of references ([hard
   links](https://en.wikipedia.org/wiki/Hard_link)) to the item (file, folder,
   symbolic link or "shortcut").
3. **Owner:** This is the username of the user who owns the file. Their
   permissions are indicated in the first permissions field.
4. **Group:** This is the user group of the user who owns the file. Members of
   this user group have permissions indicated in the second permissions field.
5. **Size of item:** This is the number of bytes in a file, or the number of
   [filesystem blocks](https://en.wikipedia.org/wiki/Block_(data_storage))
   occupied by the contents of a folder. (We can use the `-h` option here to
   get a human-readable file size in megabytes, gigabytes, etc.)
6. **Time last modified:** This is the last time the file was modified.
7. **Filename:** This is the filename.

So how do we change permissions? As I mentioned earlier, we need permission to
execute our script. Changing permissions is done with `chmod`. To add
executable permissions for all users we could use this:

```
$ chmod +x demo.sh
$ ls -l
```
{: .language-bash}

```
-rw-rw-r-- 1 yourUsername tc001 12534006 Jan 16 18:50 bash-lesson.tar.gz
-rwxrwxr-x 1 yourUsername tc001       40 Jan 16 19:41 demo.sh
-rw-rw-r-- 1 yourUsername tc001 77426528 Jan 16 18:50 dmel-all-r6.19.gtf
-rw-r--r-- 1 yourUsername tc001   721242 Jan 25  2016 dmel_unique_protein_is...
drwxrwxr-x 2 yourUsername tc001     4096 Jan 16 19:16 fastq
-rw-r--r-- 1 yourUsername tc001  1830516 Jan 25  2016 gene_association.fb.gz
-rw-rw-r-- 1 yourUsername tc001       15 Jan 16 19:17 test.txt
-rw-rw-r-- 1 yourUsername tc001      245 Jan 16 19:24 word_counts.txt
```
{: .output}

Now that we have executable permissions for that file, we can run it.

```
$ ./demo.sh
```
{: .language-bash}

```
Our script worked!
```
{: .output}

Fantastic, we've written our first program! Before we go any further, let's
learn how to take notes inside our program using comments. A comment is
indicated by the `#` character, followed by whatever we want. Comments do not
get run. Let's try out some comments in the console, then add one to our
script!

```
# This won't show anything.
```
{: .output}

Now lets try adding this to our script with `nano`. Edit your script to look
something like this:

```
#!/bin/bash

# This is a comment... they are nice for making notes!
echo "Our script worked!"
```
{: .language-bash}

When we run our script, the output should be unchanged from before!

## Shell variables

One important concept that we'll need to cover are shell variables. Variables
are a great way of saving information under a name you can access later. In
programming languages like Python and R, variables can store pretty much
anything you can think of. In the shell, they usually just store text. The best
way to understand how they work is to see them in action.

To set a variable, simply type in a name containing only letters, numbers, and
underscores, followed by an `=` and whatever you want to put in the variable.
Shell variable names are often uppercase by convention (but do not have to be).

```
$ VAR="This is our variable"
```
{: .language-bash}

To use a variable, prefix its name with a `$` sign. Note that if we want to
simply check what a variable is, we should use echo (or else the shell will try
to run the contents of a variable).

```
$ echo $VAR
```
{: .language-bash}
```
This is our variable
```
{: .output}

Let's try setting a variable in our script and then recalling its value as part
of a command. We're going to make it so our script runs `wc -l` on whichever
file we specify with `FILE`.

Our script:

```
#!/bin/bash

# set our variable to the name of our GTF file
FILE=dmel-all-r6.19.gtf

# call wc -l on our file
wc -l $FILE
```
{: .language-bash}

```
$ ./demo.sh
```
{: .language-bash}

```
542048 dmel-all-r6.19.gtf
```
{: .output}

What if we wanted to do our little `wc -l` script on other files without having
to change `$FILE` every time we want to use it? There is actually a special
shell variable we can use in scripts that allows us to use arguments in our
scripts (arguments are extra information that we can pass to our script, like
the `-l` in `wc -l`).

To use the first argument to a script, use `$1` (the second argument is `$2`,
and so on). Let's change our script to run `wc -l` on `$1` instead of `$FILE`.
Note that we can also pass all of the arguments using `$@` (not going to use it
in this lesson, but it's something to be aware of).

Our script:

```
#!/bin/bash

# call wc -l on our first argument
wc -l $1
```
{: .language-bash}

```
$ ./demo.sh dmel_unique_protein_isoforms_fb_2016_01.tsv
```
{: .language-bash}
```
22129 dmel_unique_protein_isoforms_fb_2016_01.tsv
```
{: .output}

Nice! One thing to be aware of when using variables: they are all treated as
pure text. How do we save the output of an actual command like `ls -l`?

A demonstration of what doesn't work:

```
$ TEST=ls -l
```
{: .language-bash}

```
-bash: -l: command not found
```
{: .error}

What does work (we need to surround any command with `$(command)`):

{% include {{ site.snippets }}/05/ls-l.snip %}

Note that everything got printed on the same line. This is a feature, not a
bug, as it allows us to use `$(commands)` inside lines of script without
triggering line breaks (which would end our line of code and execute it
prematurely).

## Loops

To end our lesson on scripts, we are going to learn how to write a for-loop to
execute a lot of commands at once. This will let us do the same string of
commands on every file in a directory (or other stuff of that nature).

for-loops generally have the following syntax:

```
#!/bin/bash

for VAR in first second third
do
    echo $VAR
done
```
{: .language-bash}

When a for-loop gets run, the loop will run once for everything following the
word `in`. In each iteration, the variable `$VAR` is set to a particular value
for that iteration. In this case it will be set to `first` during the first
iteration, `second` on the second, and so on. During each iteration, the code
between `do` and `done` is performed.

Let's run the script we just wrote (I saved mine as `loop.sh`).

```
$ chmod +x loop.sh
$ ./loop.sh
```
{: .language-bash}
```
first
second
third
```
{: .output}

What if we wanted to loop over a shell variable, such as every file in the
current directory? Shell variables work perfectly in for-loops. In this
example, we'll save the result of `ls` and loop over each file:

```
#!/bin/bash

FILES=$(ls)
for VAR in $FILES
do
        echo $VAR
done
```
{: .language-bash}

```
$ ./loop.sh
```
{: .language-bash}
```
bash-lesson.tar.gz
demo.sh
dmel_unique_protein_isoforms_fb_2016_01.tsv
dmel-all-r6.19.gtf
fastq
gene_association.fb.gz
loop.sh
test.txt
word_counts.txt
```
{: .output}

There's a shortcut to run on all files of a particular type, say all `.gz`
files:

```
#!/bin/bash

for VAR in *.gz
do
    echo $VAR
done
```
{: .language-bash}
```
bash-lesson.tar.gz
gene_association.fb.gz
```
{: .output}

> ## Writing our own scripts and loops
>
> `cd` to our `fastq` directory from earlier and write a loop to print off the
> name and top 4 lines of every fastq file in that directory.
>
> Is there a way to only run the loop on fastq files ending in `_1.fastq`?
>
> > ## Solution
> >
> > Create the following script in a file called `head_all.sh`
> > ```
> > #!/bin/bash
> >
> > for FILE in *.fastq
> > do
> >    echo $FILE
> >    head -n 4 $FILE
> > done
> > ```
> > {: .language-bash}
> >
> > The "for" line could be modified to be `for FILE in *_1.fastq` to achieve
> > the second aim.
> {: .solution}
{: .challenge}

> ## Concatenating variables
>
> Concatenating (i.e. mashing together) variables is quite easy to do. Add
> whatever you want to concatenate to the beginning or end of the shell
> variable after enclosing it in `{}` characters.
>
> ```
> FILE=stuff.txt
> echo ${FILE}.example
> ```
> {: .language-bash}
> ```
> stuff.txt.example
> ```
> {: .output}
>
> Can you write a script that prints off the name of every file in a directory
> with ".processed" added to it?
>
> > ## Solution
> >
> > Create the following script in a file called `process.sh`
> > ```
> > #!/bin/bash
> >
> > for FILE in *
> > do
> >    echo ${FILE}.processed
> > done
> > ```
> > {: .language-bash}
> >
> > Note that this will also print directories appended with ".processed". To
> > truly only get files and not directories, we need to modify this to use the
> > `find` command to give us only files in the current directory:
> >
> > ```
> > #!/bin/bash
> >
> > for FILE in $(find . -max-depth 1 -type f)
> > do
> >    echo ${FILE}.processed
> > done
> > ```
> > {: .language-bash}
> >
> > but this will have the side-effect of listing hidden files too.
> {: .solution}
{: .challenge}

> ## Special permissions
>
> What if we want to give different sets of users different permissions.
> `chmod` actually accepts special numeric codes instead of stuff like `chmod
> +x`. The numeric codes are as follows: read = 4, write = 2, execute = 1. For
> each user we will assign permissions based on the sum of these permissions
> (must be between 7 and 0).
>
> Let's make an example file and give everyone permission to do everything with
> it.
>
> ```
> touch example
> ls -l example
> chmod 777 example
> ls -l example
> ```
> {: .language-bash}
>
> How might we give ourselves permission to do everything with a file, but
> allow no one else to do anything with it.
>
> > ## Solution
> >
> > ```
> > chmod 700 example
> > ```
> > {: .language-bash}
> >
> > We want all permissions so: 4 (read) + 2 (write) + 1 (execute) = 7 for user
> > (first position), no permissions, i.e. 0, for group (second position) and
> > all (third position).
> {: .solution}
{: .challenge}
