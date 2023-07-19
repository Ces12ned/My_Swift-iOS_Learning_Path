Swift uses ***Automatic Reference Counting*** (ARC). 

- ARC keeps track of **strong references to instances of classes.**
    - Increase or decrease accordingly to assigning or unassigning instances of classes to constants, properties and variables.
        - It deallocates memory used by objects which reference count got down to zero.
- ARC does not increase or decrease the reference count of value types because, when assigned, these are copied.

- When two instances strongly reference each other and therefore never let each other’s reference count drop down to zero, a **Strong Reference Cycle** has occurred.
    - There are two ways of resolving a Strong Reference Cycle:
        
        
        - **Weak References**
            - It is used when you know that the reference is allowed to become nil.
            - It is used when the other reference has a shorter lifetime, that is when the other instance can be deallocated first.
            - ARC automatically sets a weak reference to nil when the instance that it refers to is deallocated.
            
        
        Weak references need to allow their value to be changed to nil at runtime, therefore they must be defined as optional variables. 
        
        ```swift
        
        weak var nameOfThePropertie: propertieType?  
        ```
        
        - **Unowned References**
            - It is used when you know that the reference will “never” become nil.
            - It is used when you are certain that the other reference has the same lifetime or a longer lifecycle.
            - ARC never sets an unowned reference’s value to nil.
            
            Unowned references have to be defined as non-optional since it is assumed to always have a value. 
            
            ```swift
            unowned let nameOfThePropertie: propertieType
            unowned var nameOfThePropertie: propertieType
            
            unowned let nameOfThePropertie: propertieType? 
            //It can be defined as an optional, nevertheless you are responsible of making sure 
            // it always refers to a valid object or is set to nil. 
            ```
            

- **Capture List**

Also when using closures within a class a potential capture of self can occurred, leading to a Strong Reference Cycle in Closures. 

This strong reference occurs because closures, like classes, are reference types. 

- To solve this problems Swift provides an elegant solution know as ***Capture List.***
    - A capture list defines how the closures would handle references captured in it.
        
         
        
        ```swift
        ... { [weak self] valueName in 
        
        	//Code inside the closure
        
        }
        ```
        
