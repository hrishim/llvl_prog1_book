# Variables, Constants and Data types

In any program we need to be able to hold data for processing. In  assembly we used registers and memory (stack) to store data. When programming in higher level languages the programmer can use labels called "variables" to hold data. The variable name is a convenient way to refer to the data which may be stored in registers or in memory.

Variable names are made of letters and digits, but the first character of the name **must** be a letter. For the purpose of variables the underscore character "_" counts as a letter. Here  are examples of valid variable names: *employee_name*, *car_speed*, *num_of_forks*, *table9* etc. Variable names are case sensitive so a variable named *x* is different from *X*. The general C programming convention is to use lower case names and separate words using underscore. However, this is not a requirement.

Variable names have to be unique within the same scope<span style="color:red"><sup>1</sup></span>. So we cannot have more than one variable named *score* in our function because it would be impossible to say which *score* we are referring to at different points in the code. 

Generally, library routines, consisting of helpful pre-written code, use variable names beginning with underscore. So it is a good practice for programmers not to start variable names with underscore and inadvertently shadow those variables.

When we define a variable in our program we are telling the compiler to allocate a specific amount of memory. We refer to that memory location in our program using the variable name. Here is an example of a variable definition:

```c
void main() {   //beginning of the main function
  int count;    //a variable named count should be given the space allocated to an integer
  count = 45;   //write a value to count
}               //end of the main function
```

Let us break down the three lines above. The line defining *count* asks the compiler to reserve memory that corresponds to an "integer" and assign it the label *count*. In the remainder of  *main()* we can refer to this memory location using its label (i.e. variable name).

Variable *count* is defined to be an integer. Some other data types supported in C are:

- *char* : a single byte of data capable of holding one ASCII<span style="color:red"><sup>2</sup></span> character
- *float* : single precision floating point numbers
- *double* : double precision floating point numbers

The *float* and *double* data types are used to store rational numbers (e.g. 1.2, 3.414 etc). We have not discussed how computers represent rational numbers and related assembly instructions. That is out of the scope of the material in this book. We may use some rational numbers in our C programming examples. Such numbers will be stored in variables of *float* or *double* type.

So we know `int count;` reserves enough memory to hold an integer but how much is that? The actual number of bytes that *int* represents is architecture dependent. In AArch64 an *int* is 4 bytes (i.e. 32 bits). Incidentally, it is the same size on the other popular architecture - X86. The size of different types of variables in C on AArch64 is documented in the [ARM Cortex-A Series Programmer's Guide](https://developer.arm.com/documentation/den0024/a/Porting-to-A64/Data-types).

In addition to types, variables may have additional qualifiers. Two qualifiers - *short* and *long* - can control the size of a *int* type variable when it is defined.

```c
short int x;      // a 16-bit integer
short y;          // same as above; can skip int
long int a;       // a 32-bit integer
int b;            // same as long int
long c;           // same as long int
long long int d;  // a 64-bit integer
long long e;      // same as long long int

float k;          // a 32-bit floating point number
double l;         // a 64-bit floating point number
```

Another two qualifiers - *signed* and *unsigned* - can be used to specify if an *int* or *char* variable holds signed numbers or unsigned numbers. An *unsigned char* variable is 8 bits in size and can hold values from 0 to 2<sup>8</sup>-1. A *signed char* is also 8-bits long. It can hold values between -128 (i.e. -2<sup>7</sup>) to 127 (i.e. 2<sup>7</sup>-1). This should be no surprise to you as we covered binary representation in a [previous chapter](./binary_representation.md).

```c
int z;            // signed integer
signed int z;     // signed integer
unsigned int b;   // unsigned integer

char x;           // signed char
signed char y;    // signed char
unsigned char c;  // unsigned char  

int l,m,n;        // defining 3 variables l, m, and n in one line
```

Variables are labels to memory locations. In our programs, once we define them, we can assign values to them, and also overwrite existing values by assigning the variable a new value. All variables must be declared before use. A declaration specifies the type of the variable (therefore its size in memory) and the name of the variable. Variables may be optionally initialized at the time of declaration. It is considered good programming practice to initialize variables.

```c
int x = 5;

x = 7;

x = 11;

int y = x;
```

It is important to remember that the code in a C program is first read by a compiler which in turn generates the assembly code (and machine code) that will be executed on the processor. So, when we define a variable we are instructing the compiler to reserve space of a specific size to hold our data. The compiler can decide whether that reservation will be in memory or register. By defining a variable we are also telling the complier that within a certain program scope (we will discuss scope later) that memory location will be referred to by the variable name.

<BR>

---

<span style="color:red"><sup>1</sup></span>Scope is a concept in programming that refers to where variables in a program can be accessed. For example, if the programmer defines a variable inside a function it can be accessed only within the function. 

<span style="color:red"><sup>2</sup></span>ASCII (American Standard Code for Information Interchange) is a character encoding format to represent text data in computers. In this format one byte of data can be used to represent a character.

