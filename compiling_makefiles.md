<!-- toc -->

# Compiling and Makefiles

Writing programs can be a fun thing to do. By expressing ourselfes in a high-level language we can actually tell a computer or embedded system, which only understands low-level binary, what to do. This high-level language does have to be translated to binary processor instructions specific for the architecture we are going to run the program on. This is a job for compilers and interpreters.

Compiling source code (the high-level language) can be a very complex job, especially when your project starts to become big and complex. Modern IDE (Integrated Development Environments) such as Visual Studio and Eclipse have spoiled us a bit on this matter. However in the Embedded Linux world you do not always have these tools at your disposal. This is where makefiles can make our lifes a little easier. Makefiles are textual files that tell a compiler how the source code has to be build into an executable program.

While makefiles can be as complex as the projects they build, they are often generated or build step by step. In this course a brief introduction in makefiles is given.

## Compilers and Interpreters

We generally write a computer program using a high-level language. A high-level language is one which is understandable by us humans. It contains words and phrases from the English (or other) language. But a computer does not understand high-level language. It only understands program written in 0's and 1's in binary, called the machine code. A program written in high-level language is called a source code. We need to convert the source code into machine code and this is accomplished by compilers and interpreters. Hence, a compiler or an interpreter is a program that converts program written in high-level language into machine code understood by the computer.

In computer science, an interpreter is a computer program that directly executes, i.e. performs, instructions written in a programming or scripting language, without previously compiling them into a machine language program. An interpreter generally uses one of the following strategies for program execution:

* parse the source code and perform its behavior directly.
* translate source code into some efficient intermediate representation and immediately execute this.
* explicitly execute stored precompiled code made by a compiler which is part of the interpreter system.

While interpretation and compilation are the two main means by which programming languages are implemented, they are not mutually exclusive, as most interpreting systems also perform some translation work, just like compilers.

> #### Note::Just-in-time compilation
>
> Further blurring the distinction between interpreters, byte-code interpreters and compilation is just-in-time compilation (JIT), a technique in which the intermediate representation is compiled to native machine code at runtime. This confers the efficiency of running native code, at the cost of startup time and increased memory use when the bytecode or AST is first compiled. Adaptive optimization is a complementary technique in which the interpreter profiles the running program and compiles its most frequently executed parts into native code. Both techniques are a few decades old, appearing in languages such as Smalltalk in the 1980s.
>
> Just-in-time compilation has gained mainstream attention amongst language implementers in recent years, with Java, the .NET Framework, most modern JavaScript implementations, and Matlab now including JITs.


| Compiler | Interpreter|
|----------|------------|
| Compiler works on the complete program at once. It takes the entire program as input. | Interpreter program works line-by-line. It takes one statement at a time as input.|
| Compiler generates intermediate code, called the object code or machine code. | Interpreter does not generate intermediate object code or machine code. |
| Compiler executes conditional control statements (like if-else and switch-case) and logical constructs faster than interpreter. | Interpreter execute conditional control statements at a much slower speed. |
| Compiled programs take more memory because the entire object code has to reside in memory. | Interpreter does not generate intermediate object code. As a result, interpreted programs are more memory efficient. |
| Compile once and run anytime. Compiled program does not need to be compiled every time. | Interpreted programs are interpreted line-by-line every time they are run. |
| Errors are reported after the entire program is checked for syntactical  and other errors. | Error is reported as soon as the first error is encountered. Rest of the program will not be checked until the existing error is removed. |
| A compiled language is more difficult to debug. | Debugging is easy because interpreter stops and reports errors as it encounters them. |
| Compiler does not allow a program to run until it is completely error-free. | Interpreter runs the program from first line and stops execution only if it encounters an error. |
| Compiled languages are more efficient but difficult to debug. | Interpreted languages are less efficient but easier to debug. This makes such languages an ideal choice for new students. |
| Examples of programming languages that use compilers: C,  C++, COBOL | Examples of programming languages that use interpreters: BASIC, Visual Basic, Python, Ruby, PHP, Perl, MATLAB, Lisp |

![Compilers vs Interpreters](img/compiler-vs-interpreter-techwelkin.gif)

### Java is Both a Compiled and Interpreted Language

Java is both a compiled and interpreted language. When you write a Java program, the javac compiler converts your program into something called bytecode. All the Java programs run inside a JVM (Java Virtual Machine; the secret behind Java being cross-platform language). Bytecode compiled by javac, enters into JVM memory and there it is interpreted by another program called java. This java program interprets bytecode line-by-line and converts it into machine code to be run by the JVM. Following flowchart shows how a Java program executes.

![Java is Both a Compiled and Interpreted Language](img/java-both-compiled-interpreted-techwelkin.png)


## The C++ Compilation Process

Compiling a C++ source code file into an executable program is a four-step process. For example, if you have a C++ source code file named main.cpp and you execute the following compile command:

```shell
g++ -Wall -o Hello main.cpp -save-temps
```

`-Wall` tells the compiler to show ALL warnings as they may describe possible errors in your source code. If warnings are the only messages you get when you compile your source code, an executable will still be created. However it is good practice to fix the code to get no warnings or errors at all.

`-save-temps` tells the compiler to save all intermediate files to the compilation folder (pre-processed files, object files, ...).

In the example above the compilation process looks like this:

1. The C++ **preprocessor** copies the contents of the included header files into the source code file, generates macro code, and replaces symbolic constants defined using #define with their values. The output of this step is a "pure" C++ file without any pre-processor directives (start with a #). It also adds special markers that tell the compiler where each line came from so that these can be used to produce sensible error messages. The "pure" source code files can be really huge. Even a simple hello world program is transformed into a file with about 11'000 lines of code.

2. The expanded source code file produced by the C++ pre-processor is fed to a compiler and **compiled** into the assembly language for the platform.

3. The assembler code generated by the compiler is **assembled** into the object code for the platform. Object files can refer to symbols that are not defined. This is the case when you use a declaration, and don't provide a definition for it. The compiler doesn't mind this, and will happily produce the object file as long as the source code is well-formed. Compilers usually let you stop compilation at this point. This is very useful because with it you can compile each source code file separately. The advantage this provides is that you don't need to recompile everything if you only change a single file.

4. The object code file generated by the assembler is **linked** together with the object code files for any library functions used to produce an executable file. It links all the object files by replacing the references to undefined symbols with the correct addresses. Each of these symbols can be defined in other object files or in libraries. If they are defined in libraries other than the standard library, you need to tell the linker about them. The output of the linker can be either a dynamic library or an executable.

![The Compilation Process of a C++ Program](img/the_compilation_process.png)


## Difference between GCC and G++

Both gcc and g++ are compiler-drivers of the 'GNU Compiler Collection' (which was once upon a time just the 'GNU C Compiler', but it eventually changed when more languages were added.).

!!! note "GNU"
	GNU is an operating system and an extensive collection of computer software. GNU is composed wholly of free software, most of which is licensed under GNU's own GPL (General Purpose License).

	GNU is a recursive acronym for "GNU's Not Unix!", chosen because GNU's design is Unix-like, but differs from Unix by being free software and containing no Unix code. The GNU project includes an operating system kernel, GNU HURD, which was the original focus of the Free Software Foundation (FSF). However, non-GNU kernels, most famously Linux, can also be used with GNU software; and since the kernel is the least mature part of GNU, this is how it is usually used. The combination of GNU software and the Linux kernel is commonly known as Linux (or less frequently GNU/Linux).

The programs gcc and g++ are not compilers, but really drivers that call other programs depending on what arguments you provide to them. These other programs include macro pre-processors (such as cpp), compilers (such as cc1), linkers (such as ld) and assemblers (such as as), as well as others, most of which are part of the GNU Compiler Collection (some are assumed to be on your system).

The actual compiler is "cc1" for C and "cc1plus" for C++.

Even though they automatically determine which compiler to call depending on the file-type, unless overridden with -x language, they have some differences.

The probably most important difference in their defaults is which libraries they link against automatically.

The main differences:

* gcc will compile: .c/.cpp files as C and C++ respectively.
* g++ will compile: .c/.cpp files but they will all be treated as C++ files.
* Also if you use g++ to link the object files it automatically links in the std C++ libraries (gcc does not do this).
* gcc compiling C files has less predefined macros.
* gcc compiling .cpp and g++ compiling .c/.cpp files has a few extra macros.

g++ is equivalent to `gcc -xc++ -lstdc++ -shared-libgcc` (the 1st is a compiler option, the 2nd two are linker options).

### Compiling Hello World

A simple hello world example:

```cpp
// main.cpp
#include <stdio.h>
#include <string>

int main(void) {
  std::string hello = "Hello Universe from the Embedded World!\r\n";
  printf("%s", hello.c_str());

  return 0;
}
```

Compiling this with g++ results a fine working program:

```shell
pi@HAL:~/test_gcc_g++$ g++ main.cpp
pi@HAL:~/test_gcc_g++$ ls
a.out  main.cpp
pi@HAL:~/test_gcc_g++$ ./a.out
Hello Universe from the Embedded World!
```

Trying to do the same with gcc results in linking errors:

```shell
pi@HAL:~/test_gcc_g++$ gcc main.cpp
/tmp/cc7ZE8lV.o: In function `main':
main.cpp:(.text+0x20): undefined reference to `std::allocator<char>::allocator()'
main.cpp:(.text+0x35): undefined reference to `std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::basic_string(char const*, std::allocator<char> const&)'
main.cpp:(.text+0x41): undefined reference to `std::allocator<char>::~allocator()'
main.cpp:(.text+0x4d): undefined reference to `std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::c_str() const'
main.cpp:(.text+0x70): undefined reference to `std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::~basic_string()'
main.cpp:(.text+0x92): undefined reference to `std::allocator<char>::~allocator()'
main.cpp:(.text+0xac): undefined reference to `std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> >::~basic_string()'
/tmp/cc7ZE8lV.o:(.eh_frame+0x13): undefined reference to `__gxx_personality_v0'
collect2: error: ld returned 1 exit status
```

However if we repeat the command but inform the linker to link in the standard C++ libraries all is well:

```shell
pi@HAL:~/test_gcc_g++$ gcc main.cpp -lstdc++
pi@HAL:~/test_gcc_g++$ ls
a.out  main.cpp
pi@HAL:~/test_gcc_g++$ ./a.out
Hello Universe from the Embedded World!
```

Conclusion: don't make your life more complex than needed and use g++ to compile your C++ and C programs.

## Makefiles

Compiling your source code files can be tedious, specially when you want to include several source files and have to type the compiling command everytime you want to do it. Well, I have news for you ... Your days of command line compiling are (mostly) over, because you will learn how to write simple Makefiles.

Makefiles are special format files that together with the make utility will help you to automagically build and manage your projects.

The makefile directs make on how to compile and link a program. Using C/C++ as an example, when a C/C++ source file is changed, it must be recompiled. If a header file has changed, each C/C++ source file that includes the header file must be recompiled to be safe. Each compilation produces an object file corresponding to the source file. Finally, if any source file has been recompiled, all the object files, whether newly made or saved from previous compilations, must be linked together to produce the new executable program. These instructions with their dependencies are specified in a makefile. If none of the files that are prerequisites have been changed since the last time the program was compiled, no actions take place. For large software projects, using Makefiles can substantially reduce build times if only a few source files have changed.

### Separate Compilation

One of the features of C and C++ that's considered a strength is the idea of "separate compilation". Instead of writing all the code in one file, and compiling that one file, C/C++ allows you to write many .cpp files and compile them separately. With few exceptions, most .cpp files have a corresponding .h file.

A .cpp usually consists of:

* the implementations of all methods in a class,
* standalone functions (functions that aren't part of any class),
* and global variables (usually avoided).

The corresponding .h file contains

* class declarations,
* function prototypes,
* and extern variables (again, for global variables).
* The purpose of the .h files is to export "services" to other .cpp files.

For example, suppose you wrote a Vector class. You would have a .h file which included the class declaration. Suppose you needed a Vector in a MovieTheater class. Then, you would #include "vector.h.

Why all the talk about how .cpp files get compiled in C++? Because of the way C++ compiles files, makefiles can take advantage of the fact that when you have many .cpp files, it's not necessary to recompile all the files when you make changes. You only need to recompile a small subset of the files. Back in the old days, a makefile was very convenient. Compiling was slow, and therefore, having to avoid recompiling every single file meant saving a lot of time.

Although it's much faster to compile now, it's still not very fast. If you begin to work on projects with hundreds of files, where recompiling the entire code can take many hours, you will still want a makefile to avoid having to recompile everything.


### Basics of Makefiles

A makefile is based on a very simple concept. A makefile typically consists of many entries. Each entry has:

* a target (usually a file)
* the dependencies (files which the target depends on)
* and commands to run, based on the target and dependencies.

Let's look at a simple example.

```make
student.o: student.cpp
   g++ -Wall -c student.cpp
```

`-Wall` has already been explained but `-c` has not. It tells the compiler to compile the code into an object file and stop there. This allows us to compile all .cpp files into object files and later link them all together into an executable file.

The basic syntax of an entry looks like:

```make
<target>: [ <dependency > ]*
	[ <TAB> <command> <endl> ]+
```

As with other programming, we also like to make our makefiles as DRY (Don't Repeat Yourself) as possible. For this reason the compiler, the compiler flags and linker flags are set as variables in the makefile, allowing them to be reused and changed quickly if needed.

Let's see an example for a simple hello world program with a single main.cpp file:

```Cpp
#include <stdio.h>

int main(void)
{
  printf("Hello World\r\n");
  return 0;
}
```

```make
# The compiler to use
CC=g++

# Compiler flags
CFLAGS=-c -Wall
	# -c: Compile or assemble the source files, but do not link. The linking stage simply is not done. The ultimate output is in the form of an object file for each source file.
	# -Wall: This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid (or modify to prevent the warning), even in conjunction with macros.

# Name of executable output
EXECUTABLE=hello

$(EXECUTABLE): main.o
	$(CC) main.o -o $(EXECUTABLE)

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp
```

Notice how the compilation of the main.cpp file and the eventual linking of all object files (is this case only one, excluding libraries) is split into two targets.

Now to start the make process all you need to do is traverse to the directory with the Makefile in it and execute the `make` with a target specified:

```shell
$ make hello
g++ -c -Wall main.cpp
g++ main.o -o hello
$ ls
hello  main.cpp  main.o  Makefile
```

Most makefiles will also include an 'all' target. This allows the compilation of the full project. The 'all' target is usually the first in the makefile, since if you just write `make` in command line, without specifying the target, it will build the first target. And you expect it to be 'all'.

Another frequent target is the 'clean' target. This removes both the executables and all intermediary files that were generated. Always make sure to execute a `make clean` before commiting your changes to git.

With both these targets added, the makefile becomes:

```make
# The compiler to use
CC=g++

# Compiler flags
CFLAGS=-c -Wall
	# -c: Compile or assemble the source files, but do not link. The linking stage simply is not done. The ultimate output is in the form of an object file for each source file.
	# -Wall: This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid (or modify to prevent the warning), even in conjunction with macros.

# Name of executable output
EXECUTABLE=hello

all: $(EXECUTABLE)

$(EXECUTABLE): main.o
	$(CC) main.o -o $(EXECUTABLE)

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp

clean:
	rm -f *.o $(EXECUTABLE)
```

> #### Success::Assignment 5.1
>
> Create a simple hello world program (in C++) with an accompanied makefile. Do however use gcc instead of g++. Remember you have to add something for the linker. Also take note that the argument needs to be supplied to the linker and not actually to the compiler. So for extra credits make sure to do it DRY. Zip it all up and upload it to Toledo. Make sure to execute a `make clean`.


### A More Complex Hello World Example

Now what if your project contained more files than just a simple main.cpp file. This will be the case for most projects.

The makefile below shows how separate classes can be compiled using a makefile.

```make
# The compiler to use
CC=g++

# Compiler flags
CFLAGS=-c -Wall
	# -c: Compile or assemble the source files, but do not link. The linking stage simply is not done. The ultimate output is in the form of an object file for each source file.
	# -Wall: This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid (or modify to prevent the warning), even in conjunction with macros.

# Name of executable output
EXECUTABLE=hello

all: $(EXECUTABLE)

$(EXECUTABLE): main.o robot.o
	$(CC) main.o robot.o -o $(EXECUTABLE)

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp

robot.o: lib/robot.cpp
	$(CC) $(CFLAGS) lib/robot.cpp

clean:
	rm -f *.o $(EXECUTABLE)
```

## Cross-compiling for Raspberry Pi

A compiler is a program that turns source code into executable code. Like all programs, a compiler runs on a specific type of computer, and the new programs it outputs also run on a specific type of computer. The computer the compiler runs on is called **the host**, and the computer the new programs run on is called **the target**. When the host and target are the same type of machine, the compiler is a **native compiler**. When the host and target are different, the compiler is a **cross compiler**.

When developing for the same architecture as used for the development, the software can easily be compiled on the development machine and provided as a binary to anyone who wants to use your application.

Embedded systems often have a different architecture than the development machine (PowerPC, ARM, ...). While you can definitely develop your application on an x86 machine and copy it to an embedded system for compilation it is most often not preferred. This because the work flow requires extra steps and it may also be slow if the target system is not adequate for the compilation process. The application may also be complex being the reason for the compilation to take a long time (try for example to compile apache2 webserver on a Raspberry Pi).

In some cases the application is tested and debugged on the development architecture and later compiled for the target architecture. This can greatly decrease testing and debugging time.

The solution lies in a cross-compiler.

### Why cross-compile?

In theory, a PC user who wanted to build programs for some device could get the appropriate target hardware (or emulator), boot a Linux distro on that, and compile natively within that environment. While this is a valid approach, it has a few prominent downsides for embedded systems:

* **Speed** - Target platforms are usually much slower than hosts, by an order of magnitude or more. Most special-purpose embedded hardware is designed for low cost and low power consumption, not high performance.
* **Capability** - Compiling is very resource-intensive. The target platform usually doesn't have gigabytes of memory and hundreds of gigabytes of disk space the way a desktop does; it may not even have the resources to build "hello world", let alone large and complicated packages.
* **Availability** - Bringing Linux up on a hardware platform it's never run on before requires a cross-compiler. Even on long-established platforms like ARM or Mips, finding an up-to-date full-featured prebuilt native environment for a given target can be hard. If the platform in question isn't normally used as a development workstation, there may not be a recent prebuilt distro readily available for it, and if there is it's probably out of date. If you have to build your own distro for the target before you can build on the target, you're back to cross-compiling anyway.
* **Flexibility** - A fully capable Linux distribution consists of hundreds of packages, but a cross-compile environment can depend on the host's existing distro for most things. Cross compiling focuses on building the target packages to be deployed, not spending time getting build-only prerequisites working on the target system.
* **Convenience** - The user interface of headless boxes tends to be a bit basic. Diagnosing build breaks are frustrating enough as it is. Switching back and forth between your test environment and your development environment gets old fast.

### Setting up the Cross-compiler for ARM

Let us setup a cross-compiler so we can use our development machine to build programs for an ARM architecture.

Luckely a cross-compiler is readily available. All we need to do is clone the `rpi-tools` repository to get it.

```shell
cd && git clone --depth 1 https://github.com/raspberrypi/tools.git rpi-tools
```

This will clone the required tools in your home directory.

Now if we want to cross-compile something we will need to provide the path to the cross-compiler. If you are manually compiling files you can just specify the full path to the compiler but this is a bit tedious.

Since we are already using makefiles, we will take this approach. There are actually two basic ways to specify the compiler, compared to a more complex and general system using target-specific makefiles or using configure (more info at [http://stackoverflow.com/questions/3090224/how-to-instruct-makefile-to-use-different-compilers](http://stackoverflow.com/questions/3090224/how-to-instruct-makefile-to-use-different-compilers)).

A first approach consists of making two makefiles. One for the native compiler and one for cross-compiler.

A native makefile

```make
# The compiler to use
CC=g++

# Compiler flags
CFLAGS=-c -Wall
	# -c: Compile or assemble the source files, but do not link. The linking stage simply is not done. The ultimate output is in the form of an object file for each source file.
	# -Wall: This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid (or modify to prevent the warning), even in conjunction with macros.

# Name of executable output
EXECUTABLE=hello

all: $(EXECUTABLE)

$(EXECUTABLE): main.o
	$(CC) main.o -o $(EXECUTABLE)

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp

clean:
	rm -f *.o $(EXECUTABLE)
```

A cross-compilation makefile

```make
# The compiler to use
CCPREFIX="/home/$(whoami)/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-"
CC=$(CCPREFIX)g++

# Compiler flags
CFLAGS=-c -Wall
	# -c: Compile or assemble the source files, but do not link. The linking stage simply is not done. The ultimate output is in the form of an object file for each source file.
	# -Wall: This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid (or modify to prevent the warning), even in conjunction with macros.

# Name of executable output
EXECUTABLE=hello

all: $(EXECUTABLE)

$(EXECUTABLE): main.o
	$(CC) main.o -o $(EXECUTABLE)

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp

clean:
	rm -f *.o $(EXECUTABLE)
```

However a better approach would be to make a generic makefile that uses an exported variable `CCPREFIX` which can be set from outside of the makefile.

You can try to do it manually inside a terminal by exporting the path to the compiler. Do take note that if you close the terminal the path will be forgotten. So you will need to redo the export. Also do not forget to substitute `<username>` with the username of your account on your development machine.

```shell
export CCPREFIX="/home/$(whoami)/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-"
```

Make sure not to make any typo's and use tab-completion !

On the other hand to you can also create a small script to this for you. First create a shell script. Add a shebang and the export command to it using nano.

```shell
cd && nano setup_cross_compiler.sh
```

Add the following:

```shell
#!/usr/bin/env bash
export CCPREFIX="/home/$(whoami)/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-"
```

Save the script, make it executable and run it. Do take note that you need to use the `source` command to run the script otherwise it will be run in another shell and the export will be lost.

```shell
chmod u+x setup_cross_compiler.sh
source ./setup_cross_compiler.sh
```

> ####Warning "Closing the terminal"
>
> Again note that if you close the terminal, the export will also be lost.

Make sure to check if all went well by echoing: `echo $CCPREFIX`. You should get the following result:

```shell
echo $CCPREFIX
/home/bioboost/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-
```

Don't want to do this every time ? Then add the following lines to the bottom of `~/.zshrc` or `./bashrc` to make the export on every login or shell launch:

```shell
# Cross-compiler for RPI
export CCPREFIX="$HOME/rpi-tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-"
```

Now your general makefile can be the following

```make
# The compiler to use
CC=$(CCPREFIX)g++

# Compiler flags
CFLAGS=-c -Wall
	# -c: Compile or assemble the source files, but do not link. The linking stage simply is not done. The ultimate output is in the form of an object file for each source file.
	# -Wall: This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid (or modify to prevent the warning), even in conjunction with macros.

# Name of executable output
EXECUTABLE=hello

all: $(EXECUTABLE)

$(EXECUTABLE): main.o
	$(CC) main.o -o $(EXECUTABLE)

main.o: main.cpp
	$(CC) $(CFLAGS) main.cpp

clean:
	rm -f *.o $(EXECUTABLE)
```

Do note that this way you will need to override the export off `CCPFREFIX` if you want to use the native compiler:

```shell
export CCPREFIX=""
```

Now you are ready to make the *hello* application, copy the output binary to the embedded system and run it. If all went well.














### Embedded Systems Compilers

But wait, how do you build a compiler if you do not have a compiler?

Generally, when new platforms (architectures and/or operating systems) are released they come with a native compiler which may not be as powerful as the GCC ones but can compile basic C programs with minimal or no optimization. The default C compiler on a new system is often named cc. When GCC is ported to a new platform, this default compiler is used to build GCC which can then be used to build other applications on top of it.

As a configuration such as a Linux OS on an Intel x86 machine is pretty common, it is likely that the system comes with a pre-built GCC suite with the program cc simply linking to gcc.
