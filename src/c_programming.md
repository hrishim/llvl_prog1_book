# Programming in C

In previous chapters we learnt a little bit of the AArch64 assembly language. However, writing large programs in assembly can be tedious. Most applications are written in higher-level languages like C/C++, Rust, Java, Kotlin, Swift etc. In this chapter we will learn the fundamentals of the C programming language.

The C programming language is considered a "system programming language". It is used extensively in the development of operating systems and embedded systems. For example, most of the Linux kernel is written in C. Similarly, operating systems like OpenBSD, FreeBSD, NetBSD, and DragonFlyBSD all have large amounts of code written in the C language.

Programs written in high level languages are translated to assembly using special programs called compilers and assemblers. Two popular compilers for the C programming language are **gcc** and **Clang**. We will use a version of **gcc** that compiles C code to AArch64 assembly.

This chapter is a whirlwind tour of the C programming language. Readers will be introduced to commonly used portions of the C programming language and get to do some programming exercises for practice. The chapter will cover what is required to make progress for the rest of the book and will not be an in-depth tutorial in C programming.

---

Readers already familiar with C may skip this chapter. Kindly note that we will continue to compile and execute code "bare-metal" meaning that there is no OS on the system. This means the standard C library is not used by the programming exercises in this book.

---

## Hello World

Let us start our journey with a simple program:

```c
void main() {
  uart_puts("Hello World");
}
```

This program prints the text "Hello World" as output. You can compile and run it with these steps:
1. Change directory to exercises/c_functions/
1. Compile the code by executing the command - "make hello_world.elf"
1. RUn the code by executing the command - "make run"

Here is a quick summary of the hello world program. We will discuss specifics of functions and other syntax in later sections of this chapter.
- The first line of the program specifies the name of a function - *main*. All C programs must have a *main* function. Program execution will start at this function. The contents of functions are enclosed within curly brackets - { and }.
- In *main* we call another function named *uart_puts* by passing it a value "Hello World". The *uart_puts* function prints characters onto the screen. This function is defined in another file and when the program is compiled it is included in the compilation process.
- All statements - like the function call to *uart_puts* - are ended with a semicolon

Usually programs are developed to run on top of operating systems. In that case programmers can rely on standard libraries that will be available on most operating systems. These libraries will contain useful functions to print data on screen, read user input, read/write files etc. However, in this book all code that we write runs "bare-metal" meaning there is no underlying operating system or standard libraries to rely on. The *uart_puts* function is part of the code written to support this book. We will learn how it works in later chapters.  

