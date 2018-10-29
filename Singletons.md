# Singletons
    A singleton class returns the same instance no matter how many times an application requests it. A typical class permits callers to create as many instances of the class as they want, whereas with a singleton class, there can be only one instance of the class per process.

    A singleton object provides a global point of access to the resources of its class. Singletons are used in situations where this single point of control is desirable, such as with classes that offer some general service or resource.

    You obtain the global instance from a singleton class through a factory method. The class lazily creates its sole instance the first time it is requested and thereafter ensures that no other instance can be created.

    ```
    + (id)sharedManager {
        static Singleton *sharedMyManager = nil;
        static dispatch_once_t onceToken;
        dispatch_once(&onceToken, ^{
            sharedMyManager = [[self alloc] init];
        });
        return sharedMyManager;
    }
    ```
    
