# RAII

## Introducing example

Normally, if a heap-allocation fails a exception is thrown. When disabling exceptions this does not happend, hence we have two possibilities of handling the error.

With exceptions:
```cpp
try 
{
	auto ptr = std::make_unique<SomeClass>();
} catch (std::allocation_error& )
{
	// error handling after deallocation
}
```

Without exceptions:
```cpp
auto ptr = std::make_unique<SomeClass>();
if (!ptr)
{
	// error handling without deallocation.  
}
```

But it is quite easy to change the code to perform a deallocation. This will however be done differently in the library.

```cpp
std::unique_part<SomeClass> ptr{nullptr};
{
	auto tmp = std::make_unique<SomeClass>();
	if (tmp)
		ptr = std::move(tmp);
	//elsewise tmp is deallocated.
}
if (!ptr)
{
	//error handling after deallocation
}

## Gcc Preprocessor symbol

The gcc compiler can be executed with -fno-exceptions, which disables excpetions. That results in errors when using try and catch in the code. Hence we can distinguish the cases with compiler intrinsics.

```cpp
#ifdef __cpp_exceptions
throw exception();
#else
//other handling
#endif
```

## Basic Idea

### Initialize

We should use a class, which is initialized and has a way to check if it was initialized correctly. To implement this correctly. This will be done via inheritance, whereby the class is named raii_checker.
```cpp
class something : raii_checker
{
	something()
	{
		initialize(); //...
		
		if (!initialized())
			raii_checker::handle_error<std::exception>(...); 
	
	}

};
```
Now depending on the __cpp_exceptions symbol the handler_error call either throws an exception or sets a value in the raii_checker class. If not using exceptions the raii_checker will contain a boolean value and a cast operator. That is it can be used the following way:
```cpp
something s;
if (!s) //error occured
{
	//handle error
	return;
}
```




