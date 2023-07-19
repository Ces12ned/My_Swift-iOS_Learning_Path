- Both structs and classes in Swift can have:
    - Properties
    - Methods
    - Initializers
    - Be extended
    - Conform to protocols

## Classes:
    - Are referenced types. (They increase their reference count when passed to a function or assigned to a variable or constant.)
        - Stored in the heap memory.
    - They can use inheritance from a superclass.
    - Type casting
    - Deinitializers (former dealloc).

## Structs:
    - Are value types. (When is assigned to a variable or constant, or is passed to a function, its value is copied instead of increasing its reference count.)
        - Stored in the stack memory.
    - No memory leaks.
    - They are inmutable.
            - True immutability (By declaring it as a let, you can´t change any part of the structure.)

            

The key thing about choosing between using a class or a struct is reference or value passing. 

- If you need to store some primitives, use struct.
- If you need custom behavior where passing by reference is preferable, use class.
