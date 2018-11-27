---
description: This chapter provides the basics tools to compiling C++ application for the Raspberry Pi.
---

# Developing apps for the RPi

Writing programs can be a fun thing to do. By expressing ourselves in a high-level language we can actually tell a computer or embedded system, which only understands low-level binary, what to do. This high-level language does have to be translated to binary processor instructions specific for the architecture we are going to run the program on. This is a job for compilers and interpreters.

## Compilers and Interpreters

We generally write a computer program using a high-level language. A high-level language is one which is understandable by us humans. It contains words and phrases from the English (or other) language. But a computer does not understand high-level language. It only understands program written in 0's and 1's in binary, called the machine code. A program written in high-level language is called a **source code**. We need to convert the source code into **machine code** and this is accomplished by compilers and interpreters. Hence, a compiler or an interpreter is a program that converts program written in high-level language into machine code understood by the computer.

In computer science, an interpreter is a computer program that directly executes, i.e. performs, instructions written in a programming or scripting language, without previously compiling them into a machine language program. An interpreter generally uses one of the following strategies for program execution:

* parse the source code and perform its behavior directly.
* translate source code into some efficient intermediate representation and immediately execute this.
* explicitly execute stored pre-compiled code made by a compiler which is part of the interpreter system.

While interpretation and compilation are the two main means by which programming languages are implemented, they are not mutually exclusive, as most interpreting systems also perform some translation work, just like compilers.

> **HINT** - **Just-in-time compilation**
>
> Further blurring the distinction between interpreters, byte-code interpreters and compilation is just-in-time compilation (JIT), a technique in which the intermediate representation is compiled to native machine code at runtime. This confers the efficiency of running native code, at the cost of startup time and increased memory use when the bytecode or AST is first compiled. Adaptive optimization is a complementary technique in which the interpreter profiles the running program and compiles its most frequently executed parts into native code. Both techniques are a few decades old, appearing in languages such as Smalltalk in the 1980s.
>
> Just-in-time compilation has gained mainstream attention amongst language implementors in recent years, with Java, the .NET Framework, most modern JavaScript implementations, and Matlab now including JITs.

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

![Java is Both a Compiled and Interpreted Language](img/java_jvm_execution.png)

## The C++ Compilation Process

Compiling a C++ source code file into an executable program is a four-step process. Take for example a source code file named `main.cpp`. This can be compiled into an executable `Hello` file by using the following command:

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

> **HINT** - **GNU**
> GNU is an operating system and an extensive collection of computer software. GNU is composed wholly of free software, most of which is licensed under GNU's own GPL (General Purpose License).
>
> GNU is a recursive acronym for "GNU's Not Unix!", chosen because GNU's design is Unix-like, but differs from Unix by being free software and containing no Unix code. The GNU project includes an operating system kernel, GNU HURD, which was the original focus of the Free Software Foundation (FSF). However, non-GNU kernels, most famously Linux, can also be used with GNU software; and since the kernel is the least mature part of GNU, this is how it is usually used. The combination of GNU software and the Linux kernel is commonly known as Linux (or less frequently GNU/Linux).

The programs gcc and g++ are not compilers, but really drivers that call other programs depending on what arguments you provide to them. These other programs include macro pre-processors (such as cpp), compilers (such as cc1), linkers (such as ld) and assemblers (such as as), as well as others, most of which are part of the GNU Compiler Collection (some are assumed to be on your system).

The actual compiler is "cc1" for C and "cc1plus" for C++.

Even though they automatically determine which compiler to call depending on the file-type, unless overridden with `-x language`, they have some differences.

The probably most important difference in their defaults is which libraries they link against automatically.

The main differences:

* gcc will compile .c/.cpp files as C and C++ respectively.
* g++ will compile .c/.cpp files but they will all be treated as C++ files.
* Also if you use g++ to link the object files it automatically links in the std C++ libraries (gcc does not do this).
* gcc compiling C files has less predefined macros.
* gcc compiling .cpp and g++ compiling .c/.cpp files has a few extra macros.

g++ is equivalent to `gcc -xc++ -lstdc++ -shared-libgcc` (the 1st is a compiler option, the 2nd two are linker options).

### Compiling Hello World

A simple hello world example:

```cpp
// main.cpp
#include <iostream>

using namespace std;

int main(void) {
  cout << "Hello Universe from the IoT World!" << endl;
  return 0;
}
```

Compiling this with g++ using `g++ main.cpp` results a fine working program:

```shell
./a.out
Hello Universe from the IoT World!
```

Trying to do the same with gcc using `gcc main.cpp` results in linking errors:

```shell
/tmp/ccfPhiXa.o: In function `main':
main.cpp:(.text+0x10): undefined reference to `std::basic_ostream<char, std::char_traits<char> >& std::operator<< <std::char_traits<char> >(std::basic_ostream<char, std::char_traits<char> >&, char const*)'
main.cpp:(.text+0x20): undefined reference to `std::ostream::operator<<(std::ostream& (*)(std::ostream&))'
main.cpp:(.text+0x34): undefined reference to `std::cout'
main.cpp:(.text+0x38): undefined reference to `std::basic_ostream<char, std::char_traits<char> >& std::endl<char, std::char_traits<char> >(std::basic_ostream<char, std::char_traits<char> >&)'
/tmp/ccfPhiXa.o: In function `__static_initialization_and_destruction_0(int, int)':
main.cpp:(.text+0x70): undefined reference to `std::ios_base::Init::Init()'
main.cpp:(.text+0x9c): undefined reference to `std::ios_base::Init::~Init()'
collect2: error: ld returned 1 exit status
```

However if we repeat the command but inform the linker to link in the standard C++ libraries using `gcc main.cpp -lstdc++` all is well:

```shell
./a.out
Hello Universe from the IoT World!
```

Conclusion: don't make your life harder than needed and use g++ to compile your C++ and C programs.

## Developing for the RPi

The current approach - creating an app on the RPi itself - is doable because it's only a very small application. However, when your projects starts to grow, this is not a feasible approach anymore. We need to be able to develop our applications on our development machine using our tools such as Visual Code.

There are a couple of approaches we can take here:

* Transfer our source code **via GitHub**. This is slow and tedious and we would also need to commit non-tested code. So that is not a good path to be taking.
* Use **secure copy** to transfer our code to the RPi. This is doable but still tedious as a copy action takes up time, especially if the project contains a lot of files.
* Setup a **network share** on the RPi and develop inside of this directory using our Windows machine. This is probable the best approach for the moment.

In all of these situations we are compiling our project on the target platform (RPi in this case). It is also possible to **cross-compile** the application on our development machine for ARM and **transfer only the resulting binary to the target**. This is the best approach but it takes some work to set it all up. We will back to this later.

For the moment we will take the network share approach. It also allows us to easily backup our code.

## Setting up a Share

To share network folders to a Windows computer we need to install some special software on the Raspberry Pi. The software providing the secret sauce this time is called *Samba*. The Samba software package implements the SMB protocol and provides support for the Windows naming service (WINS) and for joining a Windows Workgroup.

Installing the software using the commands below:

```shell
sudo apt-get update
sudo apt-get install samba samba-common-bin
```

After installation configure the software by opening the file `/etc/samba/smb.conf` using nano.

```shell
sudo nano /etc/samba/smb.conf
```

Read through the file and make sure you have the following parameters set:

```text
workgroup = WORKGROUP
wins support = yes
```

You can use anything as your workgroup name as long as it is alphanumerical and matches the workgroup you would like to join. The default workgroup in Windows 7, 8 and 10 is WORKGROUP.

### Setup a folder to share

Next step is to create the folder you would like to share. To create a folder called `iot_projects` in your home directory do the following:

```shell
mkdir ~/iot_projects
```

With the folder created we can now tell the Samba software to share it on the network. Open the file `/etc/samba/smb.conf` using nano.

```shell
sudo nano /etc/samba/smb.conf
```

Scroll to the bottom and add the following:

```config
# Sharing our IoT Projects directory
[iot_projects]
 comment=IoT Projects Share
 path=/home/pi/iot_projects
 browseable=Yes
 writeable=Yes
 only guest=no
 public=no
 create mask=0664
 directory mask=0775
```

Notice how we tell Samba that public access is not allowed via `public=no` – this means that anyone wanting to access the shared folder must login with a valid user.

In this case the valid user is the user called `pi`. To set the Samba access password for the `pi` user, execute the `smbpasswd` command.

```shell
sudo smbpasswd -a pi
```

**Restart the Samba service** using `sudo service smbd restart`.

### Opening the shared folder from Windows

Now you should be able to traverse to the share using the network url: `\\<ip>\iot_projects`. It will request the credentials of the `pi` user so enter the password of the Samba `pi` user.

![RPi Network Share](img/network_share.png)

Make sure to regularly backup the folder so you don't lose any projects during the course.

<!-- How about MAC and Linux? -->