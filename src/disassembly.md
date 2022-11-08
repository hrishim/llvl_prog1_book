# Disassembly examples

In this chapter we will examine the disassembly of some common C language syntax.

## Disassembly of control-flow syntax

Let us now look at disassembly of examples we had introduced in the **Control flow** subsection from **C Language Syntax**.

### If-Else

Here is the code for *if-else* statement example from C language syntax section: 
```c
#include "uart.h"


int main()
{
    int lower_int_val = find_lower_int(2, 6);
    uart_print_num(lower_int_val);
}

// function returns the lower of two integers
int find_lower_int(int x, int y) {
  if (x <= y) {
    // if the value of x is less than or equal to y
    // execute the code in this block
    return x;
  } else {
    // if the condition above is false
    // execute code in this block
    return y;
  }
}

```

Path of example:
```
exercises/c_functions/if_else_example.c
```

The command for compiling if_else_example.c file is:

```
aarch64-none-elf-gcc -O0 -ffreestanding -nostdinc -nostdlib -nostartfiles -I../include/ \
-c if_else_example.c -o if_else_example.o
```

The **-O0** option in the above command tells compiler not to optimize the code. The compiler optimization tries to improve the generated assembly code by making it consume fewer resources (compile time, memory space etc) so that it will result in faster-running machine code. We disable optimization here to make it easier to relate the C statements with the disassembly. Compiler supports different levels for optimization from **-O0** to **-O3**. **-O0** completly disables optimization and **-O3** performs maximum amount of optimization.

The linker command to produce final executable for this example is:
```
aarch64-none-elf-ld -nostdlib -nostartfiles start.o if_else_example.o ../common/uart.o -T link.ld -o if_else_example.elf
```

For generating disassembly for examples in this chapter we will an add option **--source** to **objdump** command. Turning on this switch tells it to interleave source code with disassembly. This would help in associating C lines with assembly. Here is the full command to produce the disassembly output for this example:

```
aarch64-none-elf-objdump --source -d if_else_example.o > if_else_example.disass
```

Let us now look at the output in more detail in small logical blocks. We will start with the *main* function. 

First registers x29 and x30 iare stored on the stack and SP is moved to x29.

```
0000000000080088 <main>:

int main()
{
   80088:       a9be7bfd        stp     x29, x30, [sp, #-32]!
   8008c:       910003fd        mov     x29, sp
```

Then arguments are prepared for calling the function **find_lower_int**. Before calling the function, values 2 and 6 are moved to registers **w0** and **w1**. If you recall from **Chapter 9: Functions**, registers **w0** through **w7** are used to pass arguments to functions and **w0** is used to return a value from the function. 

```
    int lower_int_val = find_lower_int(2, 6);
   80090:       528000c1        mov     w1, #0x6                        // #6
   80094:       52800040        mov     w0, #0x2                        // #2
```

The function **find_lower_int** is called using **bl** instruction. Finally the return value from the function which is placed in GPR **x0** is stored to stack at offset 0x28.
```
   80098:       97fffff0        bl      80058 <find_lower_int>
   8009c:       b9001fe0        str     w0, [sp, #28]
```

This code loads back the value from stack to GPR **x0** and calls the **uart_print_num** function to print the value in UART.
```
    uart_print_num(lower_int_val);
   800a0:       b9801fe0        ldrsw   x0, [sp, #28]
   800a4:       940000db        bl      80410 <uart_print_num>
}
```

 The above code loads back the value from stack to GPR **x0** and calls the **uart_print_num** function to print the value in UART.

```
   800a8:       d503201f        nop
   800ac:       a8c27bfd        ldp     x29, x30, [sp], #32
   800b0:       d65f03c0        ret
```
Now let us us now look at the function **find_lower_int** that returns the lower of two integers. The **sub** instruction creates space for stack and following **str** instructions save **w0** and **w1** in stack at offset #12 and #8.

```
// function returns 
int find_lower_int(int x, int y) {
   80058:       d10043ff        sub     sp, sp, #0x10
   8005c:       b9000fe0        str     w0, [sp, #12]
   80060:       b9000be1        str     w1, [sp, #8]
```

This code loads back **w1** and **w0** from stack from offsets **12** and #8, effectively swapping the original value of **w0** with **w1**. It then compares the value of **w1** with **w0** and branches to location 0x8007c if value in **w1** is greater than **w0** value.

```
  if (x <= y) {
   80064:       b9400fe1        ldr     w1, [sp, #12]
   80068:       b9400be0        ldr     w0, [sp, #8]
   8006c:       6b00003f        cmp     w1, w0
   80070:       5400006c        b.gt    8007c <find_lower_int+0x24>
```


This stores the value in **w0** to stack at offset #12. It then unconditionally branches to location 0x80080.
```
    // if the value of x is less than or equal to y
    // execute the code in this block
    return x;
   80074:       b9400fe0        ldr     w0, [sp, #12]
   80078:       14000002        b       80080 <find_lower_int+0x28>
```

This loads register **w0** from stack at offset #8.
```
  } else {
    // if the condition above is false
    // execute code in this block
    return y;
   8007c:       b9400be0        ldr     w0, [sp, #8]
  }
}
```

It finally adjusts the stack by 0x10 to remove the space it had allocated for itself at the start of the function.
```
   80080:       910043ff        add     sp, sp, #0x10
   80084:       d65f03c0        ret
```


You would have noticed, with **-O0** option that we had used to **disable optimizations** makes the generated code very verbose. It forces the generated assembly code to make use of many unnecessary stack operations to save and restore intermediate results. At the same time, using a higher optimization level like **-O3** would make it hard to relate the C statements with generated disassembly. For example, here is the if-else example from above compiled with **-O1**.

Use the command line below to to compile:

```
aarch64-none-elf-gcc -O1 -ffreestanding -nostdinc -nostdlib -nostartfiles -I../include/ \
-c if_else_example.c -o if_else_example.o
```

Let us first look at the disassembly output of the **main** function:

```
0000000000000000 <main>:

int main()
{
   0:	a9bf7bfd 	stp	x29, x30, [sp, #-16]!

    int lower_int_val = find_lower_int(2, 6);
    uart_print_num(lower_int_val);
   4:	d2800040 	mov	x0, #0x2                   	// #2
   8:	910003fd 	mov	x29, sp
    uart_print_num(lower_int_val);
   c:	94000000 	bl	0 <uart_print_num>
  10:	a8c17bfd 	ldp	x29, x30, [sp], #16
  14:	d65f03c0 	ret
```

Compilers are capable of performing fairly advanced optimizations. 
Here, the compiler appears to have intelligently figured out that **0x2** is lower that **0x6** and moves value **0x2** to register **x0**, thereby completely avoiding the need to perform call to **find_lower_int** function!! It was able to do this because we passed 2 and 6 as literal values. So the compiler figured out that in this instance the output of `find_lower_int()` will always be 2.

Let us now see the code for **find_lower_int** function:
```
0000000000000000 <find_lower_int>:
  } else {
    // if the condition above is false
    // execute code in this block
    return y;
  }
}
   0:	6b01001f 	cmp	w0, w1
   4:	1a81d000 	csel	w0, w0, w1, le
   8:	d65f03c0 	ret
```
Although, the **main** function completely avoided call to **find_lower_int** function, the compiler has still generates code for the **find_lower_int** function so that it can honour calls from any other function that could not be optimized. It chooses to perform the entire functionality by using the **csel** instruction and returns the result in **x0** to its caller.

As an exercise, you can try generating just the disassenbly output without interleaving source code for this example.

At **-O0** the compiler generates very verbose code with a lot of unnecessary loads and stores. At higher optimization levels it could completely remove C code. This is a good thing in the real world but not useful when we want to show readers examples of what C code looks like when compiled. Therefore, for the remaining examples, we will use an optimization level that helps illustrate the point we want to convey.


### Switch statement example

Let us now look at the **switch** statement from the C language syntax section: 

```c
#include "uart.h"
int SAMPLE_VALUE = 25;

void main() {
  int x = SAMPLE_VALUE;

  switch (x) {
    case 5:
      uart_puts("X is 5");
      break;
    case 10:
    case 15:
      uart_puts("X is 10 or 15");
      break;
    case 25:
      uart_puts("X is 25");
      break;
    default:
      uart_puts("I dont know what X is...");
      break;
  }
}
```

Path of example:
```
exercises/c_functions/switch_example.c
```

Compiler/Linker commands:

```
aarch64-none-elf-gcc -O1 -g -ffreestanding -nostdinc -nostdlib -nostartfiles -I../include/ \
-c switch_example.c -o switch_example.o
aarch64-none-elf-ld -nostdlib -nostartfiles start.o switch_example.o ../common/uart.o \
-T link.ld -o switch_example.elf
```

Disassembly command:
```
aarch64-none-elf-objdump --source -d switch_example.elf > switch_example.disass
```

Disassembly output:
```
#include "uart.h"
int SAMPLE_VALUE = 25;

void main() {
   80058:	a9bf7bfd 	stp	x29, x30, [sp, #-16]!
   8005c:	910003fd 	mov	x29, sp
  int x = SAMPLE_VALUE;
   80060:	90000000 	adrp	x0, 80000 <_start>
   80064:	b945bc00 	ldr	w0, [x0, #1468]

  switch (x) {
   80068:	71003c1f 	cmp	w0, #0xf
   8006c:	540000c0 	b.eq	80084 <main+0x2c>  // b.none
   80070:	5400012c 	b.gt	80094 <main+0x3c>
   80074:	7100141f 	cmp	w0, #0x5
   80078:	540001a0 	b.eq	800ac <main+0x54>  // b.none
   8007c:	7100281f 	cmp	w0, #0xa
   80080:	54000201 	b.ne	800c0 <main+0x68>  // b.any
    case 5:
      uart_puts("X is 5");
      break;
    case 10:
    case 15:
      uart_puts("X is 10 or 15");
   80084:	90000000 	adrp	x0, 80000 <_start>
   80088:	91162000 	add	x0, x0, #0x588
   8008c:	94000062 	bl	80214 <uart_puts>
      break;
   80090:	1400000a 	b	800b8 <main+0x60>
  switch (x) {
   80094:	7100641f 	cmp	w0, #0x19
   80098:	54000141 	b.ne	800c0 <main+0x68>  // b.any
    case 25:
      uart_puts("X is 25");
   8009c:	90000000 	adrp	x0, 80000 <_start>
   800a0:	91166000 	add	x0, x0, #0x598
   800a4:	9400005c 	bl	80214 <uart_puts>
      break;
   800a8:	14000004 	b	800b8 <main+0x60>
      uart_puts("X is 5");
   800ac:	90000000 	adrp	x0, 80000 <_start>
   800b0:	91160000 	add	x0, x0, #0x580
   800b4:	94000058 	bl	80214 <uart_puts>
      break;
   800b8:	a8c17bfd 	ldp	x29, x30, [sp], #16
   800bc:	d65f03c0 	ret
    default:
      uart_puts("I dont know what X is...");
      break;
   800c0:	90000000 	adrp	x0, 80000 <_start>
   800c4:	91168000 	add	x0, x0, #0x5a0
   800c8:	94000053 	bl	80214 <uart_puts>
}
   800cc:	17fffffb 	b	800b8 <main+0x60>

```

Let us now look at the output in more detail on how the compiler has transformed the switch and case statements: 

It has converted the **switch** statement to a series of **compare** and **conditional branch** instructions. It compares the input argument against the values used in **case** statement and branches to the appropriate section of code generated for the corresponding **case** statement as shown below:

```
  switch (x) {
   80068:	71003c1f 	cmp	w0, #0xf
   8006c:	540000c0 	b.eq	80084 <main+0x2c>  // b.none
   80070:	5400012c 	b.gt	80094 <main+0x3c>
   80074:	7100141f 	cmp	w0, #0x5
   80078:	540001a0 	b.eq	800ac <main+0x54>  // b.none
   8007c:	7100281f 	cmp	w0, #0xa
   80080:	54000201 	b.ne	800c0 <main+0x68>  // b.any
```

The code for each **case** statement gets the address of string literal to be printed using **adrp** instruction and performs a call to **uart_puts** function to print the string:

```
      uart_puts("X is 5");
   800ac:	90000000 	adrp	x0, 80000 <_start>
   800b0:	91160000 	add	x0, x0, #0x580
   800b4:	94000058 	bl	80214 <uart_puts>
```

The code for **default** statement too similarly loads the corresponding string:
```
    default:
      uart_puts("I dont know what X is...");
      break;
   800c0:	90000000 	adrp	x0, 80000 <_start>
   800c4:	91168000 	add	x0, x0, #0x5a0
   800c8:	94000053 	bl	80214 <uart_puts>
```

### For Loop

Let us now look at the **For loop** example from the C language syntax section: 

```c
#include "uart.h"
int NUM = 5;
void main() {
  int i;
  for(i = 0; i < NUM; i++) {
    uart_puts("Value of i: ");
    uart_print_num(i);
  }
}
```

Path of example:
```
exercises/c_functions/for_loop_example.c
```

Compiler/Linker commands:

```
aarch64-none-elf-gcc -O1 -ffreestanding -nostdinc -nostdlib -nostartfiles -I../include/ \
-c for_loop_example.c -o for_loop_example.o
aarch64-none-elf-ld -nostdlib -nostartfiles start.o for_loop_example.o ../common/uart.o -T link.ld -o for_loop_example.elf
```

Disassembly command:
```
aarch64-none-elf-objdump --source -d for_loop_example.o > for_loop_example.disass
```

Disassembly output:
```
void main() {
   80068:	a9bd7bfd 	stp	x29, x30, [sp, #-48]!
   8006c:	910003fd 	mov	x29, sp
   80070:	a90153f3 	stp	x19, x20, [sp, #16]
   80074:	f90013f5 	str	x21, [sp, #32]
  for(i = 0; i < NUM; i++) {
   80078:	d2800013 	mov	x19, #0x0                   	// #0
    uart_puts("Value of i: ");
   8007c:	90000015 	adrp	x21, 80000 <_start>
   80080:	9115c2b5 	add	x21, x21, #0x570
  for(i = 0; i < NUM; i++) {
   80084:	90000014 	adrp	x20, 80000 <_start>
   80088:	91160294 	add	x20, x20, #0x580
    uart_puts("Value of i: ");
   8008c:	aa1503e0 	mov	x0, x21
   80090:	9400005d 	bl	80204 <uart_puts>
    uart_print_num(i);
   80094:	aa1303e0 	mov	x0, x19
   80098:	940000de 	bl	80410 <uart_print_num>
  for(i = 0; i < NUM; i++) {
   8009c:	91000673 	add	x19, x19, #0x1
   800a0:	b9400280 	ldr	w0, [x20]
   800a4:	6b13001f 	cmp	w0, w19
   800a8:	54ffff2c 	b.gt	8008c <main+0x34>
  }
}
   800ac:	a94153f3 	ldp	x19, x20, [sp, #16]
   800b0:	f94013f5 	ldr	x21, [sp, #32]
   800b4:	a8c37bfd 	ldp	x29, x30, [sp], #48
   800b8:	d65f03c0 	ret
```

Let us now look at the output in more detail on how the **for** loop gets converted to assembly instructions: 

As we recall from **for** loop section of C language syntax, the for loop syntax has three expressions: expr1 is the initial expression that is executed just once. The expression expr2 is a loop termination condition and is executed at the beginning of every iteration of the loop. The final expr3 typically changes variables that count the current iteration of the loop. The below code to move 0 to **x19** corresponds to initial expression that initializes i with 0:

```
  for(i = 0; i < NUM; i++) {
   80078:	d2800013 	mov	x19, #0x0                   	// #0
```

It subsequently initializes **x21** with address of string used in uart_puts within loop so that it need not repeat executing **adrp** instruction every iteration of the for loop: 
```
    uart_puts("Value of i: ");
   8007c:	90000015 	adrp	x21, 80000 <_start>
   80080:	9115c2b5 	add	x21, x21, #0x570
```

It similarly initializes **x20** with the address of global variable **NUM**: 
```
   80084:	90000014 	adrp	x20, 80000 <_start>
   80088:	91160294 	add	x20, x20, #0x580
```

The following assembly instructions correspond to **expr2** and **expr3** of the for loop. The **x19** value is incremented by 1 by the **add** instruction. The **ldr** instruction loads the value in global variable **NUM** using the **x20** register it had initialized before the loop and compares the value with **x19**. The result of the compare is used to decide whether to jump back to the start of the loop(address 0x8008c) and exit the loop by not taking the branch (**b.gt**). 
  for(i = 0; i < NUM; i++) {
   8009c:	91000673 	add	x19, x19, #0x1
   800a0:	b9400280 	ldr	w0, [x20]
   800a4:	6b13001f 	cmp	w0, w19
   800a8:	54ffff2c 	b.gt	8008c <main+0x34>

It is left as an exercise to the reader to generate disassebly for the **while** and **do-while** loop examples in C Language Syntax section and analyze the disassembly output.


## Disassembly of functions

Let us now at disassembly output of some functions.

### Swap function with pointers

Let us now look at an example to swap two integer variables. The **swap** function in the code below swaps the integer variables *num1* and *num2* using a temporary variable *temp*. The temp variable is first assigned the value of the *num1* variable. Then, the value of the *num2* is assigned to *num1*. Finally, the temp (which holds the initial value of *num1*) is assigned to *num2* completing the swapping of the two variables. This is clearly a naive implementation that will not correctly swap the values. Why is that the case? When we call the `swap()` from `main()` by passing values NUM1 and NUM2 a copy of those values are passed as *num1* and *num2*. The scope of these copies is limited to the `swap()` function and those memory locations do not "exist" outside that function.


Let us take a look at the code for **broken swap function**: 

```
#include "uart.h"

// function to swap the two numbers
void swap(int num1,int num2)
{
    int temp;
    temp   = num1;
    num1   = num2;
    num2   =  temp;
}

int main()
{
    int NUM1 =  2, NUM2 = 5;

    //displaying numbers after swapping
    uart_print_num(NUM1);
    uart_print_num(NUM2);

    // Your code starts here

    swap(NUM1,NUM2);

    // Your code ends here

    //displaying numbers after swapping
    uart_print_num(NUM1);
    uart_print_num(NUM2);

    return 0;
}

```

Path of example:
```
exercises/c_functions/broken_swap.c
```

Compiler/Linker commands:

```
aarch64-none-elf-gcc -O1 -ffreestanding -nostdinc -nostdlib -nostartfiles -I../include/ \
-c broken_swap.c -o broken_swap.o
aarch64-none-elf-ld -nostdlib -nostartfiles start.o broken_swap.o ../common/uart.o -T link.ld -o broken_swap.elf
```

Use this command to run ELF file using QEMU:

```qemu-system-aarch64 -M raspi3 -kernel broken_swap.elf -serial null -serial stdio -nographic```


This output from this example produces following output:
```
2
5
2
5
```
instead of the expected output of:
```
2
5
5
2
```

Let us now look at the disassembly of the ELF file in order to understand why it is not generating the expected output.

Disassembly command:
```
aarch64-none-elf-objdump --source -d broken_swap.elf > broken_swap.disass
```

Disassembly output for swap function:
```
0000000000080058 <swap>:
// function to swap the two numbers
void swap(int num1,int *num2)
{
    int temp;
    temp   = num1;
    num1   = num2;
    num2   =  temp;
}
   80058:	d65f03c0 	ret

```

Surprisingly, the compiler has just generated a *RET* for the **swap** function ignoring the C code to swap the two variables using of temporary variable!!!. This is because, when we call a function in C, only the values of the arguments are passed to the function. So, in the above code, values in variable **NUM1** and **NUM2** are copied to variables **num1** and **num2** of swap function. So, the changes to *num1* and *num2* get reflected only within the particular function. The compiler figures out that the modifications it performs to **num1** and **num2** variables in **swap** functions do not get used anywhere and therefore, chooses to optimize out the C statements within the *swap* function. 

Let us know see how we can make modifications to function arguments to be seen by the calling function. To achieve this, we need to pass the address of the variable while calling the function. The following code shows the correct approach of performing the swapping so that the caller can see the effect.


Code for Swap function with ponters: 

```c
#include "uart.h"

// function to swap the two numbers
void swap(int *num1_ptr,int *num2_ptr)
{
    int temp;
    temp   = *num1_ptr;
    *num1_ptr   = *num2_ptr;
    *num2_ptr   =  temp;
}

int main()
{
    int num1 =  2, num2 = 5;

    //displaying numbers after swapping
    uart_print_num(num1);
    uart_print_num(num2);

    // Your code starts here

    swap(&num1,&num2);

    // Your code ends here

    //displaying numbers after swapping
    uart_print_num(num1);
    uart_print_num(num2);

    return 0;
}

```

Path of example:
```
exercises/c_functions/swap_with_pointers.c
```

Compiler/Linker commands:

```
aarch64-none-elf-gcc -O1 -ffreestanding -nostdinc -nostdlib -nostartfiles -I../include/ \
-c swap_with_pointers.c -o swap_with_pointers.o
aarch64-none-elf-ld -nostdlib -nostartfiles start.o swap_with_pointers.o ../common/uart.o -T link.ld -o swap_with_pointers.elf
```

Let us now look at its disassembly output for the swap function:

Disassembly command:
```
aarch64-none-elf-objdump --source -d swap_with_pointers.elf > swap_with_pointers.disass
```

Disassembly output for swap function:
```
// function to swap the two numbers
void swap(int *num1_ptr,int *num2_ptr)
{
    int temp;
    temp   = *num1_ptr;
   80058:	b9400002 	ldr	w2, [x0]
    *num1_ptr   = *num2_ptr;
   8005c:	b9400023 	ldr	w3, [x1]
   80060:	b9000003 	str	w3, [x0]
    *num2_ptr   =  temp;
   80064:	b9000022 	str	w2, [x1]
}
   80068:	d65f03c0 	ret

```

The above code passes addresses of variables num1 and num1 to the swap() function. The **swap** function takes 2 pointer variables \*num1_ptr and \*num2_ptr. Pointer variable num1_ptr holds the address of num1 and pointer variable num2_ptr holds the address of variable num2. Let us now run this ELF file on QEMU using command:

```qemu-system-aarch64 -M raspi3 -kernel broken_swap.elf -serial null -serial stdio -nographic```


This code now produces the expected output:
```
2
5
5
2
```

Since we are directly changing the value present at particular address, the value gets reflected in the calling function. Therefore the expected output gets printed.


## What we learnt this chapter

- How to examine the disassembly output of ELF executable files
- Analysis of disassembly output of common C language constructs
