# Stm Periph Library design
## Targets
 - Introduce type-safety to hardware access
 - Abstract hardware into objects
 - Allow to use [RAII](https://en.wikipedia.org/wiki/Resource_Acquisition_Is_Initialization) for everything
 - Resource use checking

## Register Access

To access the hardware register custom template types the design is explained [here](register.md).

## RAII Principles

As a basis for the library structure the RAII pattern shall be used for everything. However the programmer may want do disbale exceptions. So the RAII implementation shall also work without exceptions. The design for this is found [here](raii.md).