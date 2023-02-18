# C Language Syntax

## Functions

As you already know large programs can be organized into smaller, reusable blocks of code called functions. We have used functions in the assembly exercises earlier. In the C programming language all programs **must** have a function named *main*. This function is where every program starts execution. 

The syntax of a function is `return_type function_name( parameters ) { /* Code inside function */ }`. The return type must be a standard C data type or a struct or enum. Structs and enums are additional data types in C that we will cover later. If the function returns no value then the return type must be specified as *void*. In the event no return type is specified then the compiler assumes it to be *int*. The code inside a function is contained within curly braces.

Examples of functions:

```c
// These are examples of functions not a full working program. 
// That is why there is no main().

void function1() {
  /* This function takes no parameters and returns no value */
  int x = 2;
  char y = 'i';
  uart_puts("Value of x is: ");
  uart_print_num(x);
  uart_puts("Value of y is: ");
  uart_puts(y);
}

// A function that takes an integer parameter, increments it by 1 and
// returns the result as an integer

int addOneToThis(int x) {
  return x + 1;
}

// Here we call the function addOneToThis(int) from within another function
void example_fn() {
  int d = 2;
  int f = addOneToThis(d);
  uart_puts("Value of f is: ");
  uart_print_num(f);
}
```

All functions have a name that is used to call the function. Functions can optionally take parameters. In the example above *function1* does not take parameters. The return type of the function is *void* meaning that it does not return any value.  This is a simple function that takes no value and returns no value. It merely prints an integer and a character by making calls to appropriate print functions. The functions `uart_puts(...)` and `uart_print_num(...)` are provided as part of this book. Whenever you compile the code for any exercise in this book those functions are automatically added to your program. `uart_puts(...)` is used to print text and `uart_print_num(...)` is used to print integers.

The function `addOneToThis(int)` takes one integer parameter (also called *argument*) and returns an integer value. This function can be called from any other function by passing an integer value as the argument. Note that parameters are also variables and they are defined the same way. This function takes an integer parameter named `x`. To return a value from a function we use the `return` keyword followed by the value or expression. In `addOneToThis(int)` we return the expression `x + 1`. This statement will add 1 to the variable `x` and return the result to the caller.

The third function - `example_fn()` - also takes no parameters and does not return any value. It calls `addOneTothis(int)` and stores the return value into a variable named `f`. Note that the type of `f` matches the return type of `addOneTothis(int)`.

Variables defined inside a function are local to that function and available only inside the function. This includes any parameters that are passed to the function. In the code above, the variable `x` in `function1()` is local to the function and cannot be accessed by any other function including `main()`. In `function1()`, when `uart_print_num(...)` is called, a **copy** of the contents of `x` are passed to it as a parameter. This means that the content of the memory location `x` is copied to a new memory location. That new memory location is passed to  `uart_print_num(...)`.

Similarly, in the case of `example_fn()` the variables `d` and `f` are visible only inside the function. They are not accessible from any other function. We pass a copy of `d` to `addOneToThis(int)` through its parameter named `x`. When `x` is changed inside `addOneToThis(int)` it does not alter the value of `d` in `example_fn()`. The local copy of `d`, passed as a parameter, is the one that is modified. Inside `addOneToThis(int)` the modified value of `x` is returned. A copy of the returned value is saved in a new memory location (different from `d`) called `f`. 

In summary, for the example above the variables `d` and `f` in `example_fn()` and the parameter `x` of `addOneToThis(int)` are distinct locations in memory. Furthermore, `x` is visible only to code inside the function `addOneToThis(int)` just as `d` and `f` are visible only inside `example_fn()`. In general, variables that are defined inside a pair of curly braces (i.e. { }) are accessible only within the braces. This is called the scope of the variable.

## Operators

The C programming language has the following operators:

| Arithmetic | | Relational and Logical | |
| --- | --- | --- | --- |
| **Operator** | **Operation** | **Operator** | **Operation** |
| + | addition | > | greater than |
| - | subtraction | >= | greater than or equal to |
| * | multiplication | < | less than |
| / | division | <= | less than or equal to |
| % | modulus | == | is equal to |
| ++ | increment | != | is not equal to |  
| -- | decrement | | |

We have already used the *add* operator in the example above. That operator takes two *integers* (or *floats*) and the expression returns a value of the same type. All arithmetic operators other than *increment* and *decrement* work the same way as *add*. The *increment* and *decrement* operators need only one input. They increase or decrease the value of that variable by 1.

```c
// A version with increment operator
int addOneToThis(int x) {
  return x++;
}
```  

The *increment* and *decrement* operators can be used as pre-fix or post-fix operators. The example below shows the difference:

```c

void preFixPostFixExample() {
  int x = 2;

  //pre-fix increment
  int y = ++x; // increment the value of x then store in y

  uart_puts("Value of y is: ");
  uart_print_num(y); // y = 3
  uart_puts("Value of x is: ");
  uart_print_num(x); // x = 3

  // post-fix decrement
  y = x--;
  uart_puts("Value of y is: ");
  uart_print_num(y); // y = 3
  uart_puts("Value of x is: ");
  uart_print_num(x); // x = 2
}
```

This code is available in file exercises/c_functions/prefix_postfix_example.s. To compile and run (from within exercises/c_functions):
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make prefix_postfix_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT : 
Value of y is:
3
Value of x is:
3
Value of y is:
3
Value of x is:
2
```

<!-- ## Expressions and statements

In the C programming language an *expression* is a combination of operands and operators. Expressions typically return a value. Here are examples of expressions:

```c
x + 1

y < 5

a == b

3 + f * 2

g = 0  //This is an assignment expression and it returns the value assigned to g

```

Expressions are usually used in conjunction with control-flow statements. We will see control-flow syntax shortly. 


An *expression* becomes a *statement* when it is terminated by a semi-colon. Examples:

```c
x + 1;

y < 5;

a == b;

3 + f * 2;

g = 0;
```

Statements are usually 


Of course variables have to be declared before use in *expressions* or *statements*. For example:

```c
int x;
int j;
```

Curly braces (i.e. { }) are used to group declarations (i.e. int x;) and statements together into a *block*. Blocks do not have to be terminated by a semi-colon. Variables can be declared inside any block and their *scope* will be limited to the corresponding block. We will discuss scope of variables later in this section. -->

## Control flow

Control flow syntax can be used to selectively execute code or execute a certain section of code multiple times. These are the C equivalent of branches, jumps, and loops that we encountered in the assembly programming section.

## If-else
*if-else* expressions are used to selectively execute code if specific conditions are met. In an *if-else* pair only the *if-block* or the *else-block* will be executed. For example:

```c
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

int main()
{
    int lower_int_val = find_lower_int(2, 6);
    uart_print_num(lower_int_val);
}

```

This code is available in file exercises/c_functions/if_else_example.s. To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make if_else_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT : 2
```

The condition being evaluated by the if block can be a variable or a expression. The *if-block* is executed if the variable is non-zero (or the expression evaluates to a non-zero value). In the example above we have used the expression `x <= y` as the condition for the if block. This expression will evaluate to 0 if the condition is not true and 1 if the condition is true. The *else-block* is completely optional. When it is not present, nothing is executed if the expression is 0. The program just moves on to the next line of code.

In the example, curly braces (i.e. { }) are used to group declarations and statements together into a *block*. Blocks do not have to be terminated by a semi-colon. Variables can be declared inside any block and their *scope* will be limited to the corresponding block. 

In the event we have only one line of code for the *if* and *else* side we can use just a statement instead of a block. For example, the above function can be re-written this way:

```c
// function returns the lower of two integers
int find_lower_int(int x, int y) {
  if (x <= y) 
    return x;
  else 
    return y;
}
```
The curly braces used to denote the if-block and else-block are each replaced by single statements. Blocks do not need to be terminated with a semi-colon. However, statements have to be ended with semi-colon. 

### Loops

There are three types of loops in the C language:

- for loop
- while loop
- do while loop

The general form of the *for* loop is shown below:

```
for (expr1; expr2; expr3) {
  /* loop code */
}
```

The loop syntax has three *expressions* - *expr1* is the initial expression that is executed just once. The expression *expr2* is a loop termination condition and is executed at the beginning of every iteration of the loop. The final *expr3* typically changes variables that count the current iteration of the loop. Below is a specific example:

```c
void main() {
  int i;
  for(i = 0; i < 5; i++) {
    uart_puts("Value of i: ");
    uart_print_num(i);
  }

  uart_puts("After the loop i is:");
  uart_print_num(i);
}
```

This code is available in file exercises/c_functions/for_loop_example.s.

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make for_loop_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT : 
Value of i:
0
Value of i:
1
Value of i:
2
Value of i:
3
Value of i:
4
After the loop i is:
5
```

In this loop the loop control variable `i` is first set to 0 - this is the first expression in the *for* statement. The value of this variable determines when the loop will end. The second expression in the statement is `i < 5`. As long as this expression evaluates to a non-zero value (true) the loop will execute another iteration. The third expression - `i++` - is code that will be executed as part of the loop.

At the start of the loop `i = 0` is executed **once** and the value of the loop control variable `i` is set to 0. This expression is not executed after the first time (i.e. in any iteration of the loop).

The second expression  `i < 5` is executed at the beginning of every iteration of the loop. If the expression evaluates to *true* then the loop contents are executed. If not, control flow jumps to the code after the loop. The last expression `i++` increments the control variable and is executed as though it is part of the loop code. The *for* loop code will print:

```
Value of i:
0
Value of i:
1
Value of i:
2
Value of i:
3
Value of i:
4
After the loop i is:
5
```

In the first 5 iterations the value of `i` will be incremented by 1 during every iteration. So it will go from 0 till 4. As part of the code executing in the 5th iteration the value of `i` will be incremented to 5. At the beginning of the 6th iteration the comparison `i < 5` will evaluate to 0 (false). At the start of the next iteration the check `i<5` will fail and so the loop contents are skipped and the remaining statements of code are executed.

Another type of loop is the *while* loop. The general form of the while loop is:
```c
while (expr1) {
  /*loop code*/
}
```

This loop is executed as long as the expression expr1 evaluates to a non-zero value (i.e. true). The expression can be any legal expression in the C programming language. Usually it will involve a relational/logical operator. The while-loop example below gives the same result as the for-loop example above:


```c
void main() {
  int i= 0;

  while(i < 5) {
    uart_puts("Value of i: ");
    uart_print_num(i);
    i++;
  }

  uart_puts("After the loop i is:");
  uart_print_num(i);
}
```

This code is available in file exercises/c_functions/while_loop_example.s.

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make while_loop_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT:
Value of i:
0
Value of i:
1
Value of i:
2
Value of i:
3
Value of i:
4
After the loop i is:
5
```

In both the above loop constructs the loop termination condition is checked at the start of the loop. This means if the condition is not satisfied when the first iteration of the loop is encountered then the loop may be skipped. For example, in the while-loop code above if we initialize the value of *i* to be 5 or greater then the loop contents will not get executed. Give it a try by changing the initialization to  `i = 5;` and re-running the example. 

In the do-while loop the termination condition is checked at the end of the loop. Here is an example:

```c
void main() {
  int i= 0;

  do {
    uart_puts("Value of i: ");
    uart_print_num(i);
    i++;
  } while(i < 5);

  uart_puts("After the loop i is:");
  uart_print_num(i);
}
```

This code is available in file exercises/c_functions/do_while_loop_example.s.

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make do_while_loop_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT :
Value of i:
0
Value of i:
1
Value of i:
2
Value of i:
3
Value of i:
4
After the loop i is:
5
```

The above code also prints the same output as the previous loops. However, this loop will get executed at least once. So even if we set the initial value of `i` to a value greater than 5, the loop will still be executed once - because the condition is checked at the end of the loop.

### break and continue

Sometimes we may want to end a loop earlier than the control expression allows us to. This can be done using the **break** statement. Here is a modified version of the *do-while* example:

```c
void main() {
  int i= 0;

  do {
    if (i > 4)
      break;

    uart_puts("Value of i: ");
    uart_print_num(i);
    i++;
  } while(i < 5);

  uart_puts("After the loop i is:");
  uart_print_num(i);
}
```

This code is available in file exercises/c_functions/break_example.s.

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make break_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT :
Value of i:
0
Value of i:
1
Value of i:
2
Value of i:
3
Value of i:
4
After the loop i is:
5
```

The above code gives the same output at the previous loops. However, unlike in the previous *do-while* loop, if the initial value of `i` is set greater than 4 it will enter the loop and exit it at the *break* statement without executing the remaining iterations. Couple of things to note in the code above -

1. We can use an if-statement without an else. If the condition is satisfied the contents of the if-block are executed. If the condition is not satisfied those contents are skipped over.
2. There are no curly braces enclosing the contents of the if-statement. In the event we have only one statement to put inside an if-statement we can skip the braces. Same holds for `for` and `while` statements.

Sometimes we may want to skip an iteration of the loop based on a condition and not exit the loop completely. In the example below we print only even numbers:

```c
void main() {
  int i;
  for(i = 0; i < 10; i++) {
    if (i % 2 != 0)
      continue;

    uart_puts("Value of i: ");
    uart_print_num(i);
  }

  uart_puts("After the loop i is:");
  uart_print_num(i);
}
```

This code is available in file exercises/c_functions/continue_example.s.

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make continue_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT:
Value of i:
0
Value of i:
2
Value of i:
4
Value of i:
6
Value of i:
8
After the loop i is:
10
```

The statement inside the loop checks to see if `i` is perfectly divisible by 2. All even numbers, as you know, can be divided by 2 leaving no remainder. The mod operator (%) divides the first number by the second and returns the remainder. We check if that value **does not equal 0**. If the condition is true that means `i` is a odd number and we use `continue;` to go back to the start of the loop and execute the next iteration.

While using loops programmers should be careful to ensure that the loop termination condition will be satisfied eventually. If not, the loop will never exit and the program will run for ever. It is pretty easy to get into trouble like that when copy-pasting code :-). For example, here is the code to print even numbers. But it has a bug that makes the loop an infinite loop. Can you find the error and how to fix it?

```c
void main() {
  int i= 0;

  while(i < 10) {

    //This works for the for-loop so let me paste it here.
    if (i % 2 != 0)
      continue;

    uart_puts("Value of i: ");
    uart_print_num(i);
    i++;
  }

  uart_puts("After the loop i is:");
  uart_print_num(i);
}
```

<details>
  <summary>Try solving it your self  first and then click here to see answer.</summary>

  ```c
  void main() {
    int i= 0;

    while(i < 10) {
      if (i % 2 != 0) {
        i++;
        continue;
      }
      uart_puts("Value of i: ");
      uart_print_num(i);
      i++;
    }

    uart_puts("After the loop i is:");
    uart_print_num(i);
  }
  ```
</details>


### Switch

The *switch* statement is another way in which a decision can be made on which code section will be executed. The general form of the *switch* statement is:

```c
switch (expression) {
  case constant-expression1: code
  case constant-expression2: code
  default: code
}
```

The way *switch* works is that if *expression* matches any of the expressions in a case statement then the code corresponding to the  matching case statements are executed.

Here is an example of using the *switch* statement:

```c
void main() {
  int x = 25;

  switch x {
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

This code is available in file exercises/c_functions/switch_example.s.

To compile and run:
```
DIRECTORY: exercises/c_functions/
COMPILE_COMMAND: make switch_example.elf
RUN_COMMAND: make run
EXPECTED_OUTPUT : X is 25
```

Some things to be aware of regarding *switch*:
- If we want the same code to execute for two different cases then place them right next to each other like *case 10* and *case 15* above.
- The code fragments of each case has to end with a *break* statement to exit the *switch*. If not, matching with other *case* statements will continue.
- *default* will match if no other *case* matches. However, it is optional. If omitted and the given expression does not match any *case* then none of the code statements inside the *switch* will be executed.

Note that almost everything that can be done with *switch* statement can also be accomplished using *if-else*. But there may be times when one form is preferable over the other for conciseness or readability.

## Summary

We covered a lot of C programming language syntax in this section. While we have not covered all of the syntax, this should be sufficient for the reader to tackle the programming exercises in the next section. We will cover additional bits of the language as and when we need to use them.
