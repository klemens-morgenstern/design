# Test framework design

## Introduction

### Motivation

For embedded software development software tests are required. Currently there are only proprietary tools (like tessy or vectorcast) for running tests on none-eabi devices. Currently the two candidates would be the boost.test and the google unit test library.

Both of the libraries do not provide stubbing and are not lightweight enough.

### Idea

The library shall have a lightweight test and syntax which is parseable. Thereby the documentation shall be derived in doxygen-style, so a documentation can be created from the syntax. It shall also be possible to create the test with a tool, though that would be a different tool.

This tool will be entirely gcc based, so compiler tools can and must be used.

## Instrumentation

### Call trace

Can be done by -finstrument-functions, which means we need the two functions implemented:
```C
void __cyg_profile_func_enter (void *this_fn, void *call_site);
void __cyg_profile_func_exit (void *this_fn, void *call_site);
```
Since the adresses are fix and found in the mapfile, we can hereby deduce the whole call trace. It will also be integrated in the normal test. I.e. a test can be told to assert a certain call trace.

The call trace will be optional and the functions defined above will be part of the test backend. Hence they will be ignored by the linker, if not used.

### Coverage

When enabling the coverage settings in the gcc, it will create files which can be read by gcov. To allow this to work, while testing on an none-eabi device, the gdb running the test, will put breakpoints in the locations which try to create the files. The gdb will then write the content to be written to the console, where it can be put into the files, so a the normal behavious is emulated. 

Additionally it can be converted to a html by using lcov.

![Icov example](https://qiaomuf.files.wordpress.com/2011/05/lcov_report1.jpg)

To generate a pdf report it can either be converted from html to latex or - if that doesn't work - it will be converted by a tool, which is yet to be created.

## Output

### Formats

As the general output format of choice, doxygen shall be used. This has the huge advantage of having the code documentation and the test in one place. Hence tracebility is given in a superb way. Additionaly output to latex shall be possible. Other formats may also be included.

### Structure doxygen from remote device

The generating of the output works as shown in the picture below:

![gen doxygen](http://g.gravizo.com/g?digraph G {
 rankdir=LR;
 "Test header" -> "Test definition";
 "Input code" -> "Test definition";
 "Test definition"-> "Test.elf";
 Linkerscript -> "Test.elf";
 Backend -> "Test.elf";
 "Test.elf" -> "Gdb execution" ;
 "Gdb execution"-> Log;
 Log -> "gcov output";
 Log -> "Call trace";
 "Test.elf" -> "Call trace" ;
 Log -> "Test result";
 "Test result" -> "Output generator";
 "Call trace" -> "Output generator";
 "Input code" -> "Output generator";
 "gcov output" -> lcov;
 lcov -> "Output generator";
 "Output generator"-> doxygen;
})

Of course, running the test directly on the device will yield an easier way, by skipping the Log and conversion of data from it.

## Library structure

The library is a set of macros combined with a backend. The backend is a binary which will generate the testoutput. If not used in the backend, the macros will not use the heap.

### GDB Backend

The GDB backend is used on remote tests. It will only have empty functions. Since their names are known a gdb-call will place a breakpoint in each function and handle the information as described above for the other information. Another script will then pickup the information and generate a json file (probably). 

### Json Backend

The Json Backend is used when executing the test on a device which can create files. It skips the intermediate step of the GDB backend.

## Example
The names of the macros have no prefix, since the macros will only be included into one source file. A complex example using the cte functionality can be found [here](cte.md). Analogous the test can be used without a cte, though the syntax stays the same. 

## Requirements

Requirements shall be created in the reqif format. This has a very poweful editor plugin for eclipse. Since it stores it's data in an xml format, it can be used efficently by a diff tool. So it can be stored in a repository. It can then be read by a converter tool, which generates an output which can be parsed by doxygen. Additionally export to latex shall be given.  
