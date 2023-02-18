# Structs 

Earlier we saw that it was possible to use arrays to represent multiple values. However, arrays can only hold one type of value. One can have an array of `int`s where every value in the array is an integer. Similarly, in an array of `bool`s every value is a boolean. However, one cannot have an array that can hold `int` and `bool`.

The C programming language provides another way to group related, similar or dis-similar data. A *struct* (short for structure) can be used to group multiple variables of the same or different types. The most commonly cited example for using structures is to store positional co-ordinates. For example, if we want to represent points in a 2-D plane we can use a struct like this:

```c
struct point {
    float x, y;
};

void main() {
    struct point L;

    L.x = 3.0;
    L.y = 2.0;
}
```

The syntax above defines a struct named `point`. The struct contains two variables - both `float` - named `x` and `y`. Inside the function we declare a variable `L` which is of type `point`. To access the variable inside `L` we use the *dot* syntax.

*Structs* can be used to group dis-similar types also. For example, the code below defines `struct Person`. Each variable of type struct Person contains details of a specific person.

```c
struct Person {
    char name[80]; // char array to hold the name
    int age;
};

void main() {

    struct Person p1 = {"Lakshmi", 12}; 
    struct Person p2 = {"Shankar", 23} ;
    uart_puts(p1.name);
}
```
