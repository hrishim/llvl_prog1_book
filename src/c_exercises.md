# C Programming Exercises

Here are some programming exercises for you to try. Just like in previous exercises, try to complete it yourself before looking at the solution.

## Count leading zeros (Loops, Integer data types)

The objective of this exercise is to count the number of 1s in the binary representation of an integer.

The file to be used for this exercise is : exercises/c_functions/count_leading_zeros.c. It provides the skeleton code shown below. You will have to add code to count the number of leading zeros in variable 'integer_value' and place the result in variable 'count'.

```c

int count_leading_zeros(unsigned int integer_value)
{
    // Initialize count to zero
    int count = 0;

    // Your code starts here

    // Your function should count the number of leading zeros in variable 'integer_value'
    // and place the result in variable 'count'

    // Your code ends here

    // Return the count value
    return count;
}

void main()
{
    int result = 0;
    unsigned int num = 0x0000FFFF;

    // Count the number of leading zeros on unsigned int 'num' and store the result in variable 'result'
    // A leading zero is any 0 digit that comes before the first nonzero digit in a number's binary form

    // Your function called here
    result = count_leading_zeros(num);


    uart_print_num(result);
}

```

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make count_leading_zeros.elf
RUN_COMMAND: make run
```

**Pseudo code**:
1. Initialize count to zero
2. Some kind of loop start. Determine what should be the loop termination condition?
     Hint: Unisgned bit has 32 bits
3. Keep left-shifting the integer value by '1'
4. Check if the leftmost bit is '1' or '0'. Which logical operation can be used for this check? You can refer to "Logical operations" in section 6.2 
5. Increment the count value if the leftmost bit is '0' and go to start of the loop started in step 2
6. Exit the loop if the the leftmost bit is '1'
7. Return the count value


A sample implementation of the above pseudo code is available in count_leading_zeros_solution.c file. Like always, try to write the program yourself before looking at the solution.

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make count_leading_zeros_solution.elf
RUN_COMMAND: make run
```


## Factorial (Loops, multiplication)


In this exercise we will compute the factorial of a number. Factorial value of a number 'N' is the multiplication of all integers smaller than or equal to 'N'. For example 4! (four factorial) = 4 * 3 * 2 * 1.

The file to be used for this exercise is : exercises/c_functions/factorial.c. It provides the skeleton code below. You will have to add code to compute the factorial of a given number and place the result in the variable 'factorial_val'.

```c
unsigned int factorial(unsigned int n)
{
    //This function take a parameter n and returns n!

    int i;

    // Initialize factorial_val to 1
    int factorial_val = 1;

    // Your code starts here

    // Your code ends here

    // Return the factorial value 'factorial_val'
    return factorial_val;
}

void main()
{
    unsigned int result = 0;
    unsigned int num = 5;

    // Find the factorial of given number 'num' and store the result in variable 'result'
    // Factorial value is multiplication of all integers smaller than or equal to given number. For example factorial of 4 is 4*3*2*1 which is 24.

    // Your function called here
    result = factorial(num);

    uart_print_num(result);
}
```

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make factorial.elf
RUN_COMMAND: make run
```

**Pseudo code**:
1. Declare a variable to compute factorial(say factorial_val) and initialize it to 1
2. Some kind of loop to iterate a variable(say i) through values from 2 to n-1 (where 'n' is argument to function)
3. Multiply current number(i) with the product in factorial_val in each iteration of the loop
4. Return the factorial value 'factorial_val' after the end of the loop


A sample implementation of the above pseudo code is available in factorial_solution.c file. 

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make factorial_solution.elf
RUN_COMMAND: make run
```

## Factorial recursive variant (Recursion)

In this exercise we will compute the factorial of a number using recursion. Factorial value of a number 'N' is multiplication of all integers smaller than or equal to 'N'.

The file to be used for this exercise is: exercises/c_functions/factorial_recursive.c. It provides the skeleton code below.. You will have to add code to compute factorial using recursion.

```c
unsigned int factorial_recursive(unsigned int n)
{
    // Your code starts here

    // Your code ends here
}

void main()
{
    unsigned int result = 0;
    unsigned int num = 5;

    // Find the factorial of given number 'num' and store the result in variable 'result'
    // Factorial value is multiplication of all integers smaller than or equal to given number. For example factorial of 4 is 4*3*2*1 which is 24.

    // Your function called here
    result = factorial_recursive(num);

    uart_print_num(result);
}
```

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make factorial_recursive.elf
RUN_COMMAND: make run
```

**Pseudo code**:
1. Return 1 if value of 'n'(argument to function) is either 0 or 1
2. If the value is greater than 1, perform a recursive call to the same function with (n - 1) value as argument
3. Multiply the result returned by recursive call in step 2, with 'n'
4. Return the result obtained by step 3

A sample implementation of the above pseudo code is available in factorial_recursive_solution.c file. 

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make factorial_recursive_solution.elf
RUN_COMMAND: make run
```


## Reverse a number (Modulo operator)

The objective of this exercise is to reverse the digits of a decimal number.

File to be used for this exercise is : exercises/c_functions/reverse_num.c. It provides the skeleton code below. You will have to add function to reverse the digits of provided number.

```c
void main()
{
    unsigned int result = 0;
    unsigned int num = 13579;

    // Reverse the digits of a given number 'num' and store the result in variable 'result'
    // For example: if given number is = 13579 Output: 97531

    // Your function called here
    result = reverse_num(num);

    uart_print_num(result);
}
```

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make reverse_num.elf
RUN_COMMAND: make run
```

**Pseudo code**:
1. Initialize a variable (say reverse_num) with value 0
2. Some kind of loop that will exit only when the value of 'num' (argument to function) becomes less than or equal to '0'
3. Multiply the 'reverse_num' by 10 and then add remainder value obtained by dividing 'num' by 10
4. Divide the 'num' by 10
5. Return 'reverse_num'

A sample implementation of the above pseudo code is available in reverse_num_solution.c file. 

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make reverse_num_solution.elf
RUN_COMMAND: make run
```

## Print number of days in a month (Switch/Case)

The objective of this exercise is to return the number of days in given month using switch statement. The month is passed as an integer parameter to the function and the number passed maps to the appropriate month. For example, if the input value is 2, it refers to February and 12 means December

The file to be used for this exercise is : exercises/c_functions/print_num_days_in_month.c. It provides the skeleton code below. You will have to add a function named `find_num_days_in_month` to find the number of days in given month. Your function should take one integer parameter and return an integer.

```c
void main()
{
    int result = 0;
    unsigned int month_num = 5;

    // Find number of days in given month number 'month_num' and store the result in variable 'result'
    // For ex: If the value in register x0 is 1, then it means January, 2 means February .. 12 means December

    // Your function called here
    result = find_num_days_in_month(month_num);

    uart_print_num(result);
}
```

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make print_num_days_in_month.elf
RUN_COMMAND: make run
```

**Pseudo code**:
1. Make use a switch statement (introduced in section 10.2 C language syntax) to test the value passed as argument (say month_num)
2. If the value of month number (month_num) is greater than 12, print an error message that an invalid month number has been passed
3. If the month number (month_num) is one out of 1, 3, 5, 7, 10 or 12, return result as 31 Days
4. If the month number(month_num) is one out of 4, 6, 9 or 11 return result as 30 Days
5. Return 28 Days, if month_num does not match condition in (3) or (4) conditions

A sample implementation of the above pseudo code is available in print_num_days_in_month_solution.c file. 

To compile and run:
```
DIRECTORY: exercises/c_functions/ 
COMPILE_COMMAND: make print_num_days_in_month_solution.elf
RUN_COMMAND: make run
```
