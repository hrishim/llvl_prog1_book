# Introduction

This is **Part 1** of a series of online books which will give readers an introduction to the ARM AArch64 architecture using ARM assembly language and the C programming language. It takes a hands-on approach to teaching programming and provides explanations along with exercises. Readers will benefit if they follow along by doing the exercises themselves. Ideally, after the first exercise, readers should try to write the programs on their own. We will initially learn to write programs in assembly language and then in the C programming language.  We strongly encourage readers to try solving each exercise in this book by themselves. Refer to the provided solution only when stuck and to learn alternative solutions to exercises.


<BR>

**Who is this book for?**

This book will be useful to those interested in learning how processors work at the lowest level. Specifically, it can be useful for readers interested in working in embedded systems, OS development, compiler development, computer security, and CPU verification.

The book assumes readers have no prior knowledge of computer architecture or experience with any programming language. The book is self-contained and it is intended that everyone with high-school level mathematics background will be able to follow along.

<BR>

**What will you learn?**

This material teaches how microprocessors work under the hood. Readers will learn how actual computation is performed using the basic instructions that a processor can execute. They will learn how system software works with the underlying hardware.

Part 1 (i.e. this book) covers:
- Arm AArch64 architecture for application programmers
- Writing code in ARM assembly language
- An introduction to programming in C
- How C code relates to assembly

All the code written in this book runs "bare-metal". This means there is no underlying OS or any other software to support the code you will learn to write. Everything that is executed by the processor will be written by the reader (with some support from template code provided in the book).

<BR>

**Why learn assembly programming and CPU architecture?**

High-level languages (HLLs) abstract a lot of detail from the programmer. This is useful because abstraction hides underlying complexity and therefore makes programming easier and faster. Today there are many HLLs that are popular - Java, Swift, Python, C#, Kotlin etc. Developing applications in HLLs enables fast development time, abstraction, and portability at the cost of some overheads. For many software projects this is a very reasonable tradeoff. Especially since modern computer systems are much faster than their counterparts from a decade ago. HLL applications are efficiently translated by compilers into assembly language. Programmers developing applications do not need a detailed understanding of the CPU architecture and the language directly understood by the CPU (assembly).

However, there are some areas in software development and computer design that require programmers to have a good knowledge of CPU architecture, assembly language  and some HLL (usually C or C++). For example:
- Embedded systems
- Compiler development
- Operating System development
- Pre- and post-silicon verification of CPUs and computer systems
- Low level code optimization
- Cyber Security - analyzing and developing exploits, reverse-engineering etc.


Assembly language is specific to the architecture used by the system (e.g. ARM, x86, PowerPC etc.) and is not portable across processor types. It is the most direct way for programmers to interact with the processor and its peripherals. So there are no layers of abstraction or obfuscation between the programmer and the hardware. By learning assembly programming one can understand how a processor works under the hood. How computations are performed and how resources like memory are virtualized so that it can be shared among multiple applications. Coupling that knowledge with a programming language like C gives the programmer the skills to work in the domains listed above.

The ARM architecture is ubiquitous and used in a range of devices from wrist watches to super computers. This book teaches readers the fundamentals of the AArch64 architecture and assembly language. It also presents an introduction to the C programming language and explains how code written in C relates to assembly.


## Hardware and Software Requirements

To follow the exercises in this book you need a PC or a Mac computer with 4GB of memory or more. In addition, the computer will need to have 5GB of free disk space. The supported operating systems are listed below. The computer should be running the specified version or later.

macOS 10.15 (Catalina), Windows 10, and Ubuntu 20.04 LTS
