# Build system

## Used backend

At the moment [boost.build](http://www.boost.org/build/) will be used as the build system. Though it is quite slow, it allows very powerful operations and a very easy syntax. Though the latter only applies, if the user does not try to add functionality. In that case, boost.build becomes a huge enigma.
Since this is the case, it is planned to create a custom build system. This will however have a similar syntax, at least for defining the build process. Hence the given examples shall apply.

## Introducing Example

```jam

#basic declaration of a target, i.e. an elf, produces my_program.elf
# the device setting automatically chooses the correct linker scripts and libraries
elf my_program : main.cpp some_other_thing.cpp : <device>stm32f407vg ;
#create the hex, binary
hex my_prog : my_program ;

#may of course be shortened, the following way:
hex my_prog2 : main.cpp some_other_thing.cpp : <device>stm32f407vg ;

#get the disassembly
S my_program_dis : my_program ;

#get the map file
map my_program_map : my_program ;


#now, how do I run tests?
remote-test test1.log : [ hex test1 : test.cpp : <device>stm32f407vg ] : <debug-server>st-link <coverage>line ;

# local test:

run-test test2.log : [ exe test2 : test2.cpp ] : <debug-server>st-link <call-trace>on <coverage>all ;


```

To call the whole thing, the following command is needed (for bjam)
```cmd
bjam toolset=gcc-arm -a
```

## Device settings

We can skip the <device>stm32f407vg setting and pass it at bjam call, i.e.:

```
bjam toolset=gcc-arm device=stm32f407vg -a
```

That way, a project can be compiled to different devices, without changing the build settings. This of course fails, if the code does use hardware, not present in the device or the api is broken.

## Local testing

In the introducting example a local test was shown. But, what does it mean? Well: a test can be executed locally, i.e. on the compiling machine, if the test does not require a working hardware. This is a huge advantage, because the test can be integrate into a CI system, without the CI needing a hardware.
Through the implementation of the library, all the hardware registered can be stubbed. That is, the following call will not accesss a hardware, but rather a memory location, which can be analyzed a different way.
```cpp
Gpio.A.Odr[5] = true;
``` 
This also allows tests to analyze if the registers are accessed correctly, while ignoring the behaviour of the hardware register. That is, software errors can be analyzed much more effeciently. 

