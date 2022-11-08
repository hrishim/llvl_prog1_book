# Functions

When developing large programs it is useful break down the code into smaller more manageable pieces. This an be done by placing frequently occurring instructions into functions. Organising code this way offers these advantages:

- It provides the program structure and enables better understanding of the code.
- Code is re-used therefore overall program size (i.e. lines of code) becomes smaller. It also becomes easier to test and fix errors.

For example, say we are developing software for a scientific application and the algorithm we are using requires us to compute the sum of 1 to n numbers at different points in the code. The number n can change at different places in the program. First, we write the code to compute the sum of numbers.

```armasm
 //The general formula for the sum of numbers from 1 to n is sum = n * (n+1)/2
 //Number to be placed in w0
 add w1, w0, #1    //Compute n+1
 mul w1, w0, w1    //Compute n * (n+1)
 lsr w1, w1, #1    //Divide the result by 2
```

In the assembly code we expect that the value of n (an unsigned integer) should be made available in register w0. The three lines above will then compute the sum of integers from 1-n and place the result in w1. Note that to divide by 2 we use the lsr (right shift) instruction. If you do not understand how the lsr instruction is equivalent to division, review the chapters on binary arithmetic and integer instructions.

Now back to the complex (hypothetical) program we were developing...

We want to compute the sum of n integers at different points in our program. At each instance we want to compute the sum up to a different n. Sometimes the sum of numbers from 1-6, sometimes from 1-22 and later in the program 1-n (where n can be a different number). Example of the really large program:

```armasm
//Lots of complicated scientific computations
.
.
.
mov w0, #6
//Compute sum of integers from 1-6
add w1, w0, #1    //Compute n+1
mul w1, w0, w1    //Compute n * (n+1)
lsr w1, w1, #1    //Divide the result by 2

//Use value in w1 as an input to more complicated computations...
.
.
.
mov w0, #22
//Compute sum of integers from 1-22
add w1, w0, #1    //Compute n+1
mul w1, w0, w1    //Compute n * (n+1)
lsr w1, w1, #1    //Divide the result by 2

//Use value in w1 as an input to more complicated computations...
.
.
.
//So on and so forth
```

We could write our program as shown above and it will work. But the program is really long and difficult to understand. Also, if we made a mistake in typing the code to compute the sum of n integers anywhere in the program it could lead to errors.

One way to reduce the program size (number of instructions) would be to some how write the code to compute sum of N numbers only once. Wherever the sum-of-N needs to be computed in our program we can branch to the code that computes it and branch back once the value is computed.

What we want to do is restructure our program to something like this:

```armasm
sumOfN:
//Compute sum of integers from 1-n where n is given by the caller in w0
add w1, w0, #1    //Compute n+1
mul w1, w0, w1    //Compute n * (n+1)
lsr w1, w1, #1    //Divide the result by 2
//Return to the appropriate location. Huh, how do I do that?

//Main program starts here...
//Lots of complicated scientific computations
.
.
.
mov w0, #6
b sumOfN //Branch to the code that computes sum-of-N

//Once sumOfN is done we need to execute from the instruction below

//Use value in w1 as an input to more complicated computations...
.
.
.
mov w0, #22
b sumOfN //Branch to the code that computes sum-of-N

//Once sumOfN is done we need to execute from the instruction below

//Use value in w1 as an input to more complicated computations...
.
.
.
//So on and so forth

```

However, this does not work (yet). Why so? Well, we can use an unconditional branch instruction to keep jumping to the code to compute sumOfN. **But, after executing the code at sumOfN, how do we get back to the next instruction in the program?** At each instance in the program where we jump to sumOfN the code has to return to the immediate next instruction. So the location to jump back after executing sumOfN changes.

What we want is to be able to jump to sumOfN when needed, execute it, and jump back to the location from where we "called" the sumOfN "function". There are a few special instructions in the ARM ISA that can be used to accomplish this objective. The first one is the "branch with link" instruction (BL, BLR). This instruction is used to jump from any location in code to the start of a function. The second instruction is the return (RET) instruction that returns from the function back to the instruction just after BL/BLR.

The branch with link instruction stores the address of the next instruction (i.e. the instruction following itself) into register X30 and jumps to the memory location specified by the programmer.

Branch with link variants
 - BL immediate_address  : address to branch is encoded as an immediate
 - *BLR <X<sub>n</sub>>*   : address to branch is contained in X register

The *RET {<X<sub>n</sub>>}* instruction is the last instruction in a function. It jumps to the address contained in the specified register. If the register name is omitted the contents of X30 are used as the address to jump to.

So here is what our large program looks like with BL and RET instructions added.

```armasm
sumOfN:
  //Compute sum of integers from 1-n where n is given by the caller in w0
  add w1, w0, #1    //Compute n+1
  mul w1, w0, w1    //Compute n * (n+1)
  lsr w1, w1, #1    //Divide the result by 2
  ret               //Return to the address in X30

//Main program starts here...
//Lots of complicated scientific computations
.
.
.
mov w0, #6
bl sumOfN //Branch to the code that computes sum-of-N

//Once sumOfN is done execution begins from the instruction below
//Use value in w1 as an input to more complicated computations...
.
.
.
mov w0, #22
bl sumOfN //Branch to the code that computes sum-of-N

//Once sumOfN is done execution begins from the instruction below
//Use value in w1 as an input to more complicated computations...
.
.
.
//So on and so forth

```

Note that it is possible to use just plain branch instruction to jump to sumOfN and back. We can do this by storing the return address in a X register ourselves and then jumping back to the address in that register. However, using BL/BLR and RET gives hint to the processor that a function is being called and will eventually return. The processor can use this information to execute the program more optimally.

The key points to understand from the above discussion are:
- Jump to functions using *BL/BLR*, return from the function using *RET*
- X30 is a special register that is used by *BL* to store the return address

## Execise 1A: Function to count leading zeros

Write a function to count the number of leading zeros. The template for this exercise is provided in exercises/functions/count_leading_zeros.s and the solution is at exercises/functions/count_leading_zeros_solution.s

To compile and run:
```
COMPILE_COMMAND: make count_leading_zeros.elf
RUN_COMMAND: make run
```

## Nested Function Calls

In a program it is possible for one function to call another. For example, sumOfN function could call addOne to add 1 to the value passed in.

```armasm
sumOfN:
  //Compute sum of integers from 1-n where n is given by the caller in w0
  mov w1, w0          //Save the value of w0
  str x30, [sp, -#16]! //Save the value of X30 onto the stack, also change SP
  bl addOne
  ldr x30, [sp], #16   //Load the saved value of x30 back from stack
  mul w1, w0, w1    //Compute n * (n+1)
  lsr w1, w1, #1    //Divide the result by 2
  ret               //Return to the address in X30

addOne:
  add w1, w0, #1    //Compute n+1
  ret

```


When we use the *BL/BLR* to call a function the return address is stored in X30. So, from our program when we call sumOfN the return address is stored in X30. From sumOfN, when  we call addOne the X30 register will be overwritten with the address of the instruction to return to after executing addOne. Therefore, we save the contents of X30 in the stack before calling addOne function. After the function call we restore the contents of X30 from the stack and then return from sumOfN.

Why save and restore X30 from the stack? Why not save it another register?

In a real program, the other registers may be holding values used in  computation and so may not be available. Plus we do not know if the function being called would overwrite our chosen register. Therefore, the correct practice is to use the stack to save X30.


## Passing parameters via registers

Some functions may need data to be given to them to operate. For example the sumOfN and the addOne functions we used earlier needed input values. We passed the value N to sumOfN and the value to be incremented to addOne. In both cases the value was placed in register *w0* before we called the corresponding functions. The values that are passed to functions are called parameters.

Parameters are passed quite commonly using registers. By convention and as specified in the [ARM Procedure Call Standard](https://github.com/ARM-software/abi-aa/releases) registers *r0-r7* are used to pass parameters. If more than 8 parameters have to be passed to a function the remaining can be passed by pushing them on the stack.

The *r0-r7* registers can also be used to return values from the function. In the example above we used the *w1* register to return values from the function.

## Exercise 1B: Memcopy as a function

We have implemented memcopy as a function and the code is shown below.
Comments in the code explain how it is intended to work. However, in the present form the code has a bug (error). Can you spot the error and fix the problem.


```armasm

// Function to copy a string
memcopy_string:
    sub sp, sp, 32     // Adjust sp
    str x19, [sp, 8]   // Save x19 (local var 1)
    str x20, [sp, 16]  // Save x20 (local var 2)

    mov x19, x0 // Store str1 in local var1 (x19)
    mov x20, x1 // Store str2 in local var2 (x20)

    // Find the string length
    bl find_string_length

    mov x2, x0  // Move length value to x2(third argument)
    mov x0, x19 // Move str1 to x0(first argument)
    mov x1, x20 // Move str2 to x1(second argument)

    mov	  x19, #0x0    // Maintain count in local var 1(x19)
copy_loop_start:
    ldrb  w20, [x0, x19]  // Use local var 2(x20) to hold temporary results
    strb  w20, [x1, x19]
    add	  x19, x19, #0x1
    cmp	  x2, x19
    b.gt  copy_loop_start

    ldr x19, [sp, 8]   // Restore x19
    ldr x20, [sp, 16]  // Restore x20

    add sp, sp, 32     // Adjust sp

    ret


// Helper function to find length of a string
find_string_length:
    sub sp, sp, 32    // Adjust sp
    str x30, [sp]     // Save x30
    str x19, [sp, 8]  // Save x19 (local var 1)
    str x20, [sp, 16] // Save x20 (local var 2)

    mov   x19, #0         // Maintain count in local var 1(x19)
    ldrb  w20, [x0, x19]  // Use local var 2(x20) to hold temporary results
    cbz   x20, find_len_end
find_len_start:
    add   x19, x19, #0x1
    ldrb  w20, [x0, x19]
    cbnz  x20, find_len_start

find_len_end:
    mov x0, x19  // Move return value to x0

    ldr x19, [sp, 8]    // Restore x19
    ldr x20, [sp, 16]   // Restore x20

    ldr x30, [sp]       // Restore x30
    add sp, sp, 32      // Adjust sp

    ret

```

To compile and run:
```
COMPILE_COMMAND: make memcopy_register.elf
RUN_COMMAND: make run
```

## Register usage conventions

So far we have been talking about small programs where we use very few of the available ARM registers. However, large programs can have millions of lines of code written in collaboration by programmers. These programs have many functions. It is quite common for programmers to rely on libraries for specific purposes. Those libraries may have been written by other programmers (in a different company/organization).

A library can be thought of a set of functions the implement specific actions. For example, a math library can implement different mathematical operations. The functions in the library can be used by other programmers in their own programs.

Even though the architecture permits the programmer to use registers *r0-r30* as they choose, in practice sharing code makes it necessary to have some conventions. For example, a programmer could write a library with functions that expect input parameters in registers *r13-r20*. How would others using that code know to pass parameters using those specific registers? One could say - "read the documentation". But if every programmer were to pick their own favorite registers for different purposes then there would be a lot of documentation to read and adhere to - a total mess.

Also, most programs are written in higher level programming languages like C/C++, Rust, Swift etc. Compilers (in combination with assemblers) convert programs written in these languages to assembly and after that to machine code. Using a common convention will make it easier for code compiled using different compilers to correctly interoperate.

The [ARM procedure call standard](https://github.com/ARM-software/abi-aa/releases) (PCS) prescribes a convention to be followed for using registers. Following this convention ensures that assembly (or higher level language) code written by different programmers will interoperate without any problems.

The table below is from the ARM PCS. Readers are encouraged to refer to the full documentation for details.

| Registers | Role |
| --- | --- |
| r0-r7 | parameters and result registers |
| r8 | indirect result location register |
| r9-r15 | temporary registers |
| r16-r17 | intra-procedure-call temporary register; used by the linker as scratch registers |
| r18 | temporary register |
| r19-r28 | callee-saved registers |
| r29 | frame pointer |
| r30 | link register |
| SP | stack pointer |

**Note:** The stack pointer (SP) sould only be used to hold the address of top of stack. Register *r30* (link register) should be used to hold the address of a return.

As per the PCS, *r0-r7* are to be used for parameters and results. A called function may change the contents of these registers. So programmers should not expect these register contents will be preserved after a function call. Registers *r9-r15* and *r9-r15* can be used as temporary registers. This means they can be overwritten and these registers may not be preserved after a function call.

If the programmer wishes, registers that may be clobbered can be saved on the stack before a function call and restored after the call.

Callee-saved registers *r19-r28* are guaranteed to be preserved after a function call. Any function that uses these registers must save the values before use and restore the values before the function returns. These registers are called "callee-saved" because the onus is on the called function to preserve the values.


## Memcopy using variables on the stack

To illustrate how the stack can be used to store values local to a function we give you yet another version of the memcopy function. In this version we have the stack to hold the value for holding the index as we iterate over the elements and copy them.


```armasm

// Function to copy a string
memcopy_string:
    sub sp, sp, 48   // Adjust sp
    str x30, [sp]    // Save x30

    // Save arguments in stack
    str x0, [sp, 8]  // Save first argument in stack at (sp + #8)
    str x1, [sp, 16] // Save second argument in stack at (sp + #16)
    str x2, [sp, 24] // Save third argument in stack at (sp + #24)

    mov   x0, #0       // Initialize counter (local var)
    str   x0, [sp, 32] // Maintain counter in stack at (sp + #32)

copy_loop_start:
    ldr x0, [sp, 8]    // Load first argument from stack
    ldr x1, [sp, 16]   // Load second argument from stack
    ldr x2, [sp, 24]   // Load third argument from stack
    ldr x3, [sp, 32]   // Load local var counter from stack
    ldrb w4, [x0, x3]
    strb w4, [x1, x3]
    add  x3, x3, #0x1
    str  x3, [sp, 32]  // Save local var counter to stack
    cmp   w2, w3
    b.gt  copy_loop_start

    ldr x30, [sp]      // Restore x30
    add sp, sp, 48     // Adjust sp

    ret


// Helper function to find length of a string
find_string_length:
    sub sp, sp, 32 // Adjust sp
    str x30, [sp]  // Save x30
    str x0, [sp, 8]  // Save first argument

    ldrb  w5, [x0]
    cbz   w5, find_len_end

    mov   x0, #0         //  Initialize counter (local var)
    str   x0, [sp, 16]   //  Maintain counter in stack at (sp + #16)

    ldr   x0, [sp, 8]    // Load first argument from stack
find_len_start:
    ldr   x1, [sp, 16]   // Load counter from stack
    add   x1, x1, #0x1
    str   x1, [sp, 16]   // Save updated counter
    ldrb  w5, [x0, x1]
    cbnz  w5, find_len_start

find_len_end:
    ldr   x0, [sp, 16]   // Load counter(return value) from stack to x0

    ldr x30, [sp]        // Restore x30
    add sp, sp, 32       // Adjust sp

    ret

```

To compile and run:
```
COMPILE_COMMAND: make memcopy_stack.elf
RUN_COMMAND: make run
```


We have shown the above code as an example of how the stack can be used. It is **NOT** the ideal way to implement memcopy. The code below shows a more concise way to perform memcopy.

```armasm

// Function to copy a string
memcopy_string:
    sub sp, sp, 16  // Adjust sp
    str x30, [sp]   // Save x30

    mov   x3, #0x0
copy_loop_start:
    ldrb  w4, [x0, x3]
    strb  w4, [x1, x3]
    add   x3, x3, #0x1
    cmp   x2, x3
    b.gt  copy_loop_start

    ldr x30, [sp]   // Restore x30
    add sp, sp, 16  // Adjust sp

    ret


// Helper function to find length of a string
find_string_length:
    sub sp, sp, 16  // Adjust sp
    str x30, [sp]   // Save x30

    mov   x2, #0
    ldrb  w3, [x0, x2]
    cbz   w3, find_len_end
find_len_start:
    add   x2, x2, #0x1
    ldrb  w3, [x0, x2]
    cbnz  w3, find_len_start

find_len_end:
    mov x0, x2      // Move return value to x0

    ldr x30, [sp]   // Restore sp
    add sp, sp, 16  // Adjust sp

    ret

```

To compile and run:
```
COMPILE_COMMAND: make memcopy_optimized.elf
RUN_COMMAND: make run
```


## Recursion

Hrishi to explain general recursion here and what it involves.

```armasm
    // Recursive function to Find sum of all integers smaller than or equal to given number using a recursive function.
    // For example, if given number is 4, the result would be 4+3+2+1 which is 10.
sum_of_n_recursive:
    stp x9, x30, [sp, -32]! // Save X30(link register) and a X19 (temporary register used as a local variable) onto the stack and adjust SP
    mov w9, w0              // Move the number passed as argument to temporary register
    cmp w0, 1               // Check if number is less that or equal to 1
    ble done                // Branch to label 'done' if we have completed adding all numbers till 1
    sub w0, w0, #1          // Subtract 1 from the number to perform a recursive call and obtain sum of all lower numbers
    bl  sum_of_n_recursive  // Perform a recursive call to obtain sum of all lower numbers
    add w0, w0, w9          // Add the resultant sum of all lower numbers with current number
done:
    ldp x9, x30, [sp], 32   // Restore back temporary register and X30 from stack
    ret                     // Return the result
```
This code is available in file exercises/functions/sum_of_n.s.

To compile and run:
```
COMPILE_COMMAND: make sum_of_n.elf
RUN_COMMAND: make run
```

## Practice exercises

### Function to add and subtract matrices

In the provided template write functions to
- add two matrices
- subtract two matrices

The address of the matrices and the destination matrix are parameters passed to the function via *x0*, *x1*, and *x2* registers. Register *x3* holds the dimension of square matrices. The result, which is the address of the destination matrix, should be returned in the *x0* register.

The files to be used for this exercise and the compile and run commands are given below:

- Matrix Addition:

```
    File to be used: exercises/functions/matrix_addition.s
    COMPILE_COMMAND: make matrix_addition.elf
    RUN_COMMAND: make run
```

- Matrix Subtraction:

```
    File to be used: exercises/functions/matrix_subtract.s
    COMPILE_COMMAND: make matrix_subtract.elf
    RUN_COMMAND: make run
```

### Caesar Cipher

Show a function that will take a shift value and a string to encrypt. The output should be a encrypted string.
For example, if input string is: "Hello" and shift value is '2', the expected encrypted string is: "Jgnnq"

As a challenge the user can write a decrypt function also to perform the reverse operation and print the original string.

For this exercise use the file exercises/functions/caesar_cipher.s.

To compile and run:
```
COMPILE_COMMAND: make caesar_cipher.elf
RUN_COMMAND: make run
```

### Recursive function to compute n!

For this exercise use the file *exercises/functions/facorial.s*. In factorial.s, the number for which factorial will have to be computed is passed as argument to factorial_recursive function. Add code to compute the factorial using recursion and place the result in w0.

To compile and run:
```
COMPILE_COMMAND: make factorial.elf
RUN_COMMAND: make run
```

## What we learnt this chapter

- Breaking down programs into functions
- How parameters are passed into functions using registers
- How values are returned from functions
- Function recursion
- A little bit of the ARM procedure call standard
