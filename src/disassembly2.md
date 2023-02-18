# Disassembly examples: Functions


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
