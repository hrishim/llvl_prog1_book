# Memory Instructions

Finally, let us start with some AArch64 assembly. Memory instructions can be used to transfer data from memory into registers. Memory is byte addressed, meaning that every byte (8 bits) of memory has a unique address that is used to identify the location.

In most computer/embedded systems memory is located in a chip that is separate from the CPU. It is connected to the CPU via a bus or interconnect. To load a byte of memory into a register we can use a *load byte* instruction:
```armasm
  LDRB W10, [X1], #0
```

There are different types of load instructions in the AArch64 ISA. This type of load uses the **post-indexed** addressing mode. It performs two operations:

1. Load 1 byte of data from memory into the specified destination register
2. Update the address in the base register

The memory address of the data is provided using a register, in this case X1. This instruction fetches 1 byte of data from memory at the address specified in X1. That byte is sign extended to 32 bits and stored in W10. The offset, specified as an immediate value, is added to the base address (by the processor) and the result is stored in the register X1. In this specific example the immediate value is 0. Therefore the base address in X1 will remain unchanged after the instruction is executed.

The LDRB instruction can also be used with **pre-indexed** addressing mode like this
```armasm
  LDRB W10, [X1, #0]!
```

In this addressing mode the data address is the sum of X1 and the immediate offset. One byte, at that address, is loaded (sign extended) into the destination register (W10) and the base register (X1) is also updated with the computed address.

Below is an example of a **post-indexed** store instruction. This instruction stores 1 byte from the register W10 into the memory location contained in register X1.

```armasm
  STRB W10, [X1], #2
```


Both LDRB and STRB instructions have multiple *addressing modes* (i.e. ways of specifying the memory address). We will eventually learn all of those modes. But now let us write a small program with two instructions that we know so far.


## Copying Data

To learn how to load/store data from/to memory we will write a few assembly programs. The first program, below, will copy the words "Hello" from one location in memory to another location. The program code and instructions to compile and run it are in the file **memory_instructions/ex1a_solution.s**

As a learning exercise, it would be best if you could type out the program into the specified location in the file **memory_instructions/ex_1a.s** and try running it yourself. You can do this using any text editor.

For all exercises in this book two assembly files (with extension ".s") are provided - a template file and a solution file. You should read the material in the book and attempt to write the code yourself for each exercise. Look at the solution only if you are unable to get your program to work correctly. The template files have clearly marked sections where you can write your code.

To get you started we will discuss the code for the first exercise in detail. Open *ex_1a.s* and type the code below into the appropriate location.

```armasm
    // Your code starts here

    ldrb w4, [x0, #0] // load H
    strb w4, [x1, #0] // store H

    ldrb w4, [x0, #1] // load e
    strb w4, [x1, #1] // store e

    ldrb w4, [x0, #2] // load l
    strb w4, [x1, #2] // store l

    ldrb w4, [x0, #3] // load l
    strb w4, [x1, #3] // store l

    ldrb w4, [x0, #4] // load o
    strb w4, [x1, #4] // store o

    // Your code ends here


```

To compile and run:
```
COMPILE_COMMAND: ex_1a.elf
RUN_COMMAND: make run
```

In *ex_1a.s* we have provided set up code code that will store the text "Hello" into one location in memory and "Howdy" in another location in memory. The setup also provides the start address of "Hello" (in X0) and the start address of "Howdy" (in X1). The objective of this exercise is to copy the contents of memory starting at X0 into memory location starting at X1.

So how does this program work? Actually, it is pretty simple. Every character or letter is stored in a format called ASCII. In ASCII encoding, numbers are used to represent English alphabets, numbers, and some symbols. For example the representation for the letter *a* in ASCII is 0b01000001 (i.e. 65). There are other formats for storing text but in this book we will use ASCII since it is easy to deal with. In ASCII each character is stored in a byte of data. The word Hello has five letters and therefore it takes five bytes of data to store it in memory. 

In *ex_1a.s* the register X0 has been pre-loaded with the memory address of the first byte of the string. This is the memory address of the character corresponding to the letter "H". The destination address - location where the string **should be copied to** - is provided in X1. Before the start of the program the address starting at X0 contains the word "Hello" in consecutive memory locations (X0, X0+1...X0+4). The starting address of the word "Howdy" is in X1.

The code copies one character at a time from the address starting at X0 to the address at X1. Each character of the string Hello is 1 byte in size. So to copy the entire string we have to copy 5 bytes. Each character is stored in continuous locations of memory. In this program, **H** starts at memory address 0x800ac, **e** is at 0x800ad, and **l** at 0x800ae and so on. **Note:** Each character is 1 byte in size. 

The solution provided uses the base plus **immediate offset addressing mode** (aka immediate offset mode). This is a new addressing mode that we have not discussed earlier. In this mode the address of the memory operation is computed by adding the specified base register with a immediate value. The immediate value is also provided by the programmer. The register containing the base address remains unchanged.

The first instruction in the solution above is:

```armasm
  ldrb w4, [x0, #0]
```

This is a Load byte instruction that loads 1 byte of data from memory into the register W4. The address of the memory location to load the data from is computed by adding 0 to the base register X0. After execution of this instruction the memory contents of the byte starting at address *X0 + 0* will be loaded into the register W4. The value of register X0 will remain unchanged. Similarly the store instruction

```armasm
  strb w4, [x1, #0]
```

Stores one byte of data from W4 into the memory location (X1+0).

To copy the full string we load the value of each character into W4 and store it in the appropriate memory location.


---
**NOTE:** Exercises, in this and other sections,  have some supporting code and assembly directives. You should not modify these lines in the file. The supporting code is for the following:

 1. Place the string Hello in some address in memory.
 2. Reserve some space in memory for you to copy the string
 3. Provide the address of the first character of Hello (i.e. H) in the register X1
 4. Provide the address of the destination in address X2
 5. The strings in at X1 and X2 are printed before and after your code is executed so that you can check if the copying has been done correctly.

---


### Addressing Modes

In ex_1a.s we introduced the immediate offset addressing mode. The ARM architecture has other addressing modes to specify addresses in load and store instructions. In the rest of this section we will take  look at these modes.

Exercises ex_1b.s to ex_1d.s are similar to ex_1a.s. The objective is to accomplish the same result by using different addressing modes. Though the solutions for these exercises are provided we **strongly** encourage you to read the rest of this section and try to write the programs yourself. Consult the solution if you are unable to solve the exercise in reasonable time (say in one day).


**Post-indexed Addressing Mode**

Example:
```armasm
  ldrb w4, [x0], #1
```

This instruction performs two operations

   1. It loads the value from the memory address in the base register.
   2. It adds the base register with the provided offset and stores that result into the base register.

In the example above, the value contained in memory location x0 is loaded into register w4. In addition x0 is increased by 1.

**Programming exercise:** Write the code for exercise ex_1b.s using post-indexed addressing mode.

To compile and run:
```
COMPILE_COMMAND: ex_1b.elf
RUN_COMMAND: make run
```

**Pre-indexed Addressing Mode**


In this addressing mode the address is the sum of a base register and a specified offset value. The computed address is also stored back in the address register.

Example:
```armasm
  ldrb w4, [x0, #1]!
```

In the example, the memory address is computed as the sum of x0 and 1. The byte in that location is loaded into register w4. The computed address is also stored in register x0.

**Programming exercise:** Write the code for exercise ex_1c.s using pre-indexed addressing mode. Hint - you can load (and store) the first character using a different addressing mode.

To compile and run:
```
COMPILE_COMMAND: ex_1c.elf
RUN_COMMAND: make run
```

**Base Plus Register Offset Addressing Mode**

In this mode the address is the sum of the base register and an offset that is contained in another register.

Example:
```armasm
  ldrb w4, [x0, x2]
```

The address for this load instruction is computed by adding the base register x0 with the register x2.

**Programming exercise:** Write the code for exercise ex_1d.s using register offset addressing mode. 

To compile and run:
```
COMPILE_COMMAND: ex_1d.elf
RUN_COMMAND: make run
```

Hint - you can save an immediate value into a register using the **mov** instruction like this

```armasm
  mov  x2, #0
```

Note that in all the above addressing modes any register that is used in address computation is a 64-bit X register.

## Half-word, Word, and Double-Word Memory Operations

So far we have looked at load and store instructions that operate on one byte of memory. There are memory instructions that operate on larger chunks of memory, namely - half-word, word, and double-word.

What are these terms and what sizes do they indicate?

We already know that data in computers are represented as bits. Bits can take one of two values - 0 and 1. A group of 8-bits is called a byte. Furthermore, every byte of memory has its own identifier - a memory address that distinguishes each byte from other bytes in memory.

A word is a contiguous group of bytes. However, unlike byte which is always 8 bits, the size of a word is not a standard across different architectures. In the ARM architecture the size of a word is 4 bytes (i.e. 32 bits). A half-word is 2 bytes and a double-word is 8 bytes.

The architecture has load/store instructions that can load half-word, word, and double-word from/to memory.

Load/store double-word example:

```armasm
	ldr x4, [x0], #8
	str x4, [x1], #8
```

This instruction copies 64-bits of data (8 bytes) from the memory. The address of the first of these 8 bytes is contained in the register x0. Notice that the destination register is an **x** register so that we can accommodate the 8 bytes being read. Since we have used post-indexed addressing the address contained in x0 will also be incremented by 8.

Load/store word example:

```armasm
    ldr w4, [x0], #4
    str w4, [x1], #4
```

The **ldr** and **str** instructions load a half-word from memory (2 bytes). The examples above show post-indexed forms of the load instruction.

Load/store half-word example:

```armasm
	ldrh w4, [x0], #2
	strh w4, [x1], #2
```

The **ldrh** and **strh** instructions load a half-word from memory (2 bytes). The examples above show post-indexed forms of the load instruction.


**Note:** The ARM Architecture Reference Manual has detailed description of all the instructions discussed in this book. It is a useful reference to have when you attempt to write your own code for the exercises.

{{#quiz ./quizzes/memory_inst_a.toml}}

## Copy memory using operations of different sizes
Now for a small challenge. In file *exercises/memory_instructions/ex_1e.s* the text "Welcome To ARM Assembly World" has been stored in memory and the address of the first byte of this string is placed in x0. The register x1 is loaded with the address of a memory location to which you may write. Using any combination of word, half-word, double-word and byte operations copy the text pointed to by x0 to the location pointed to by x1.

To compile and run:
```
COMPILE_COMMAND: ex_1e.elf
RUN_COMMAND: make run
```

<details>
  <summary>The solution to this exercise is below (also in exercises/memory_instructions/ex_1e_solution.s)</summary>

```armasm
    // Your code starts here

    ldr x4, [x0], #8 // copy "Welcome "
    str x4, [x1], #8 // store "Welcome "

    ldrh w4, [x0], #2 // copy "To"
    strh w4, [x1], #2 // store "To"

    ldr w4, [x0], #4 // copy " ARM"
    str w4, [x1], #4 // store " ARM"

    ldp x3, x4, [x0] // copy " Assembly World"
    stp x3, x4, [x1] // store " Assembly World"

    // Your code ends here
```
**Note:** The **ldp** (Load pair) and **stp** (Store pair) instructions in the above example loads/stores a pair of 64-bit **x** registers from memory. These instructions can therefore load/store 16 bytes (128 bits) of data at a time.  In this example, we have used the immediate offset addressing mode. The pair load/store instructions also support immediate offset, pre-indexed and post-indexed addressing modes.
</details>

## Copying data using a loop
So far we have used multiple load and store instructions to copy data. For instance, in ex_1a.s we used five load and five store instructions to copy the letters "Hello" from one location to another.

In this section we will see how to do the same using a **loop** and fewer load and store instructions instructions. The code in this section can be found in the file *exercises/memory_instructions/ex_1f_solution.s*. After reading the explanation in this section we recommend readers try to replicate this program using the file *exercises/memory_instructions/ex_1f.s*. This file contains markers indicating where code must be filled in.

To compile and run:
```
COMPILE_COMMAND: ex_1f.elf
RUN_COMMAND: make run
```

Our objective is to copy a string of unknown length from one location to another. In this exercise, that string is "Hello" and it has 5 letters. But we want to write a generic program that will work even if the string were longer or shorter. Strings are stored in memory using a format called ASCII. There are other formats to represent alphabets in computers but this one is one of the simplest and most popular - though it does have limitations.

The characters comprising a string are stored in contiguous locations in memory. Also, at the end of every string a special character (string termination) - namely 0 - is stored to indicate the end of the string. If the special character were not present there would be no way to tell when a string has ended.

The code listing below shows the program. The start address of the first string (source) is already loaded in *x0* and the start address of the destination (target) to which it must be copied to is given in *x1*.

```armasm
1.  mov  x2, #0
2.  ldrb w5, [x0, x2]
3.  cbz  w5, skip_to_copy
find_len_start:
4.  add  x2, x2, #0x1
5.  ldrb w5, [x0, x2]
6.  cbnz w5, find_len_start

skip_to_copy:
7.   mov  x3, #0x0
copy_loop_start:
8.   ldrb w4, [x0, x3]
9.   strb w4, [x1, x3]
10.  add  x3, x3, #0x1
11.  cmp  w2, w3
12.  b.gt copy_loop_start
```

The first three instructions of the program checks for an *empty* string. That is a string that has no characters and therefore has only the string terminator 0. If the string that we need to copy (source string) is empty, then we do not need to count the number of characters in it. So, we jump to the *skip_to_copy* label and copy only the character 0 (string terminator) to the destination. 

We use *x2* as an index into the array storing the source string. It also serves as a count of the number of characters in the source string. The **mov** instruction initializes *x2* to zero. Instruction 2 is a load-byte instruction that uses base plus register offset addressing mode. So the address will be the sum of registers *x0* and *x2*. Here *x2* is 0, so instruction 2 will load the first character of the source string into *w5*.

The third instruction is a *branch* instruction. The *cbz* instruction compares the value in *w5* with 0. If the register contains zero then *cbz* instructs the processor to "jump" to the address specified by the label *skip_to_copy* and start executing code from that memory address. If the register does not contain zero then the processor continues execution from the next instruction (in this case instruction 4). 

In summary, instruction 3 (*cbz*) checks if the first character read is 0. if true, then that means the string is a blank string with length 0. So we do not need to count the number of characters in that string. We can skip the code that is counting the number of characters and jump to the part that will copy the characters.

What is a **label**? Labels are special texts that can be used in assembly code to refer to the address of the immediately following instruction. In this example the label `skip_to_copy` refers to the address of instruction 7 (i.e. `mov  x3, #0x0`). Similarly, the label `find_len_start:`  refers to the address of instruction 4 (i.e. `add  x2, x2, #0x1`). Observe that the labels have to end with a colon symbol. But when used as part of an instruction, the colon is omitted. 




```armasm
find_len_start:
4.  add  x2, x2, #0x1
5.  ldrb w5, [x0, x2]
6.  cbnz w5, find_len_start
```

The next snippet of the program, comprising of instructions 4-6, counts the length of the source string. These three instructions comprise a loop. The CPU will execute them one after another and when it reaches instruction 6 - *cnbz* - it will either go on to instruction 7 or back to instruction 4. If the instruction contains a value other than zero then the code jumps to the instruction with the specified label. In this case that is instruction 4. This loop of three instructions will continue to be executed as long as the condition checked by instruction 6 is true.

The code in the loop counts the number of characters in the source string. For that, we store the count of the number of characters in register *x2*. Instruction 4 increments *x2* by 1. 
Instruction 5 loads the next character of the source string into *w5*. Instruction 6 -- ``cnbz`` - is a branch instruction. This instruction compares the value of *w5* with 0. If *w5* does *not* contain zero then control jumps to the address specified by the label (here *find_len_start*). Else the next instruction is executed.

The loop will be executed for as many characters as there are in the source string. The source string in this program is "Hello" and so the loop will execute 6 times (five characters and one string termination character). When the string termination character (0) is reached, the condition tested by *cnbz* will become false and the CPU will exit the loop and move to the next instruction. At the end of the loop the value stored in *x2* will be a count of the number of characters in the source string - including the string termination character.

The final snippet of the program copies the source string to the target memory location.

```armasm
skip_to_copy:
7.   mov  x3, #0x0
copy_loop_start:
8.   ldrb w4, [x0, x3]
9.   strb w4, [x1, x3]
10.  add  x3, x3, #0x1
11.  cmp  w2, w3
12.  b.gt copy_loop_start
```

After counting the number of characters in the source string we copy it to a location specified by the address in *x1*. We first initialize register *x3* to 0 using a `mov` instruction. We will use x3 to track the current character being copied. It will also aid in computing the address of that character. Copying the string is done using a loop comprising of instructions 8-12. The start of the loop is labelled copy_loop_start.

In the loop, instruction 8 loads a byte from the source string, which begins at the address in *x0*. Instruction 9 stores that value into the target string, which begins at the address in *x1*. Both instructions use a base plus register addressing with x0/x1 as the base and x3 as the offset  register. The next instruction increments the x3 register. We are using the x3 register as the offset from the base and also to count the number of bytes that have been copied. 

The registers *w2* and *w3* refer to the lower 32-bits of registers *x2* and *x3* respectively. The register *x3* (and therefore *w3*) contains a count of the present loop and the register *x2* (therefore *w2*) contains the length of the source string.

The next instruction is a compare instruction (`cmp`). This instruction compares the values in *w2* and *w3*. If the value in *w2* is greater, then the instruction sets a special condition flag in the processor. We will learn more about condition flags in a later section. For now, you can assume that there is a special flag (1-bit value) that is set to 1 if *w2* holds the larger value.

The instruction that follows cmp is a "branch if greater than" instruction. The cmp instruction has set the appropriate PFLAGS. The ``b.gt`` instruction uses the information in the flags to determine if the CPU should branch to the specified label or continue to the next instruction. The instruction will branch to copy_loop_start if the compare results show that w2 is greater than w3.

Thus, the second loop executes instructions 8-12 until the value in register *w3* becomes equal to the value in register *w2* (length of the string). The instructions in the loop copy the source string to the target location. 

In this program we are copying string characters one at a time. Each character is 1 byte so we can use either *w* or *x* registers to store them. However, memory addresses must be specified as *x* registers.

---

**Summary:** 

- **CBZ:** Compare and branch if zero. This instruction checks if the value of a specified register is 0. If true, the processor will jump to the instruction at the specified label. If not the processor continues to the next instruction.

- **CBNZ:** Compare and branch if not zero. This instruction checks if the value of a specified register is **not** 0. If true, the processor will jump to the instruction at the specified label. If not the processor continues to the next instruction.

- **CMP:** Compare two values and set special flags. We will discuss this instruction further in the section on condition codes.

---

## Memory Instruction Exercises

Below are a set of exercises to practice what has been described so far:

1. Load and store operations
2. Branches and loops

We have provided solutions to for the exercises. But we encourage readers to try writing the programs themselves. Look at the solutions only when completely stuck.

## Exercise ex_2a

The file ex_2a.s provided in the exercise section is to be used for this challenge. The objective of this exercise is to copy a string from one memory location to another but the new string should be a reversed version of the original. So if the original string was "Hello" the new string should be "olleH".

To compile and run:
```
COMPILE_COMMAND: ex_2a.elf
RUN_COMMAND: make run
```

The address of the string to reverse is provided in the register x0. The memory address where the reversed string should be placed is provided in register x1. Sufficient memory has been provided to hold both strings.

Below is pseudo code to reverse the string. You can use this as a guide to write the assembly code.

1. Count the length of the string using a loop
2. Use two registers - one to count from 0 till end of string and another to count down from the end of string to 0.
3. Using the above two registers in a loop, copy bytes from the string at location x0 to the string at location x1.

The solution for this exercise is provided in ex_2a.solution.s. We recommend that you try to write the program your self before looking at the solution.

## Exercise ex_2b

The file ex_2b.s provided in the exercise section is to be used for this challenge. The objective of this exercise is to reverse a string in-place. This means the string should not be copied to a new location but can be copied onto itself.

To compile and run:
```
COMPILE_COMMAND: ex_2b.elf
RUN_COMMAND: make run
```

The address of the string to be reversed is in register x0. Below is the pseudo code for the program:

1. Find the length of the string.
2. Use one register (any register, say X1) to count from 0 till the end of the string. Another register (X2) to count from the size of the string to 0.
3. Using the above registers in a loop. Load the characters from both offsets and store them into the appropriate location.

Hint: You can use different forms of memory addressing including post-indexed.


The solution for this exercise is provided in ex_2b.solution.s. We recommend that you try to write the program your self before looking at the solution.

## What we learnt this chapter
   1. Load and Store instructions of different sized - byte, half-word, word, and double
   2. The following addressing modes: pre-indexed, post-indexed (imm and register variant), base plus offset (imm and register variant).
   3. Compare and branch instructions
   4. Labels in assembly
   5. Loops in code


---
**NOTE:** In ARM assembly language the different forms of mov instructions are aliases to other instructions. That means the instruction encoding of the mov instruction is the same as that for another ARM instruction. Both operations will yield the same result.

For example:
```armasm
  mov x0, x2
```
The above instruction copies the value in x2 to x0. This instruction has the same encoding as an OR instruction shown below
```armasm
  or x0, XZR, x2
```


If this does not makes sense to you right now, that is OK. Just know that both the example instructions above will copy the value stored in x2 to x0.

---
