# RAII



## Gcc Preprocessor symbol

The gcc compiler can be executed with -fno-exceptions

```cpp
#ifdef __cpp_exceptions
throw exception();
#else
//other handling
#endif

```