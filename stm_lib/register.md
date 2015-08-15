# Structure
## Problem

A register is basicly a set of bits. The normal access in C is done like that:

```c
GPIOA->ODR |= GPIO_ODR_ODR_1;
```

ODR is just a std::uint16_t, so no type safety is given, i.e. the following will not yield a type error, not even a warning.

```c
GPIOA->ODR |= GPIO_PUPDR_PUPDR10_1;
```

This of course has a c stm-periph function to go with it:

```c
GPIO_WritePin(GPIOA, 12, GPIO_PIN_SET);
```

But this function also has no type safety, so the following is possible without an error, though using -1 for an enumerator value will give a warning.

```c
GPIO_WritePin(GPIOA, 42, -1);
```

## Solution

To solve this problem, [strong type-enums](http://www.codeguru.com/cpp/cpp/article.php/c19083/C-2011-Stronglytyped-Enums.htm) shall be used. At least when necessary. Hence we need a structure to organize this. A template will be constructed for that, which can be used in a similar fashion to the following:

```cpp
enum class value_type
{
	value_1 = 0b00,
	value_2 = 0b01,
	value_3 = 0b10,
	value_4 = 0b11,
};

reg_entry<2,4, value_type> reg1; 
reg_entry<2,4, value_type, 3> reg2;
```

Now this means the following:
 - req1: The bits starts at position 2, has a width of 4 and the value type value_type.
 - req1: The bits starts at position 2, has a width of 4 and the value type value_type but has an addtional offset of 3byte. 

By passing a strongly typed enum only it's types can be used.

Additionally, there will be methods, to explicitly convert the values shall be given. That is: explicitly integral types can be used.

### Status
An experimental implementation is existing, though it is neither tested nor analyzed sufficiently.
 
