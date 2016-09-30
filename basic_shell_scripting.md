<!-- toc -->

# Basic Shell Scripting

## Creating a shell script

To create script file just touch it and make it executable. Example:

```shell
$ cd /tmp
$ touch hello
$ chmod u+x hello
$ ls -al hello
-rwxr--r-- 1 bioboost bioboost 0 Sep 30 11:45 hello
```

Open the file with nano and add this `#!/usr/bin/env bash` as a first line:

```shell
nano hello
```

This is what is called the shebang. Under Unix-like operating systems, when a script with a shebang is run as a program, the program loader parses the rest of the script's initial line as an interpreter directive; the specified interpreter program is run instead, passing to it as an argument the path that was initially used when attempting to run the script. In this example the script is run by the bash Linux shell.


> #### Note::The older shebang
>
> In the older days the shebang for a bash script was `#!/bin/bash`. However as some
> Linux systems dare to put the bash shell executable in a different place, these
> scripts would fail. By using the newer `#!/usr/bin/env bash` this problem is solved.

> #### Alert::sh is not Bash
>
> Please do not be fooled by scripts or examples on the Internet that use `/bin/sh` as the interpreter. sh is not bash! Bash itself is a "sh-compatible" shell (meaning that it can run most 'sh' scripts and carries much of the same syntax) however, the opposite is not true; some features of Bash will break or cause unexpected behavior in sh. Also, please refrain from giving scripts a .sh extension. It serves no purpose, and it's completely misleading (since it's going to be a bash script, not an sh script).

## Outputting text to the console

By using `echo` you can output text to the terminal:

```bash
#!/usr/bin/env bash

echo "Hello World"
```

## Running the script

The script can be run using the following command:

```shell
$ cd /tmp
$ ./hello
Hello World
```

## Variables

Variables are areas of memory that can be used to store information and are referred to by a name.

To create a variable, put a line in your script that contains the name of the variable followed immediately by an equal sign ("="). No spaces are allowed. After the equal sign, assign the information you wish to store. Note that no spaces are allowed on either side of the equal sign.

To use a variable place a dollar sign ("$") in front of the name where you want to use it.

```bash
#!/usr/bin/env bash

hello="Hello World"

echo $hello
```

As the name variable suggests, the content of a variable is subject to change. This means that it is expected that during the execution of your script, a variable may have its content modified by something you do.

On the other hand, there may be values that, once set, should never be changed. These are called constants. I bring this up because it is a common idea in programming. Most programming languages have special facilities to support values that are not allowed to change. Bash also has these facilities but, to be honest, I never see it used. Instead, if a value is intended to be a constant, it is simply given an uppercase name. Environment variables are usually considered constants since they are rarely changed. Like constants, environment variables are given uppercase names by convention.

So the previous script should actually be written as:

```bash
#!/usr/bin/env bash

HELLO="Hello World"

echo $HELLO
```

## Command substitution

You can substitute the output of a command into your script by using a dollar sign
followed by parentheses. Let's for example say we want to combine the output of a `cat` command with the output of a `date` command to
make some sort of log entry.

```bash
#!/usr/bin/env bash

mem=$(cat /proc/meminfo | grep 'MemAvailable')
logdate=$(date)

echo "[$logdate] $mem"
```
The characters "$( )" tell the shell, "substitute the results of the enclosed command."

Or even shorter:

```bash
#!/usr/bin/env bash

echo "[$(date)] $(cat /proc/meminfo | grep 'MemAvailable')"
```

## Quoting

Quoting is used to accomplish two goals:

* To control (i.e., limit) substitutions and
* To perform grouping of words.

### Single and double quotes

The shell recognizes both single and double quote characters. The following are equivalent:

```bash
var="this is some text"
var='this is some text'
```

However, there is an important difference between single and double quotes. Single quotes limit substitution. As we saw in the previous lesson, you can place variables in double quoted text and the shell still performs substitution. We can see this with the echo command:

```shell
$ echo "My host name is $HOSTNAME."
My host name is linuxbox.
```

If we change to single quotes, the behavior changes:

```shell
$ echo 'My host name is $HOSTNAME.'
My host name is $HOSTNAME.
```

Double quotes do not suppress the substitution of words that begin with "$"
but they do suppress the expansion of wildcard characters.

For example, try the following:

```shell
$ echo *
embedded_course hello hello.txt hsperfdata_mdm icedteaplugin-mdm-H8q7Cj mintUpdate pulse-PKdhtXMmr18n ssh-5OZhO7zNIJ8U systemd-private-f412405725f34e05a9ab0c320a71be53-colord.service-HGUSHh systemd-private-f412405725f34e05a9ab0c320a71be53-rtkit-daemon.service-bCX7DF
```

```shell
$ echo "*"
*
```

### Escaping a character

There is another quoting character you will encounter. It is the backslash.
The backslash tells the shell to "ignore the next character."
This is typically called escaping a character. Here is an example:

```shell
$ echo "My host name is \$HOSTNAME."
My host name is $HOSTNAME.
```

By using the backslash, the shell ignored the `$` symbol. Since the shell ignored it, it did not perform the substitution on $HOSTNAME.

Here is a more useful example:

```shell
$ echo "My host name is \"$HOSTNAME\"."
My host name is "linuxbox".
```

As you can see, using the `\"` sequence allows us to embed double quotes into our text.
