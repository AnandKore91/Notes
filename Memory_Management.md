# Memory management
- Memory management is the programming discipline of managing the life cycles of objects and freeing them when they are no longer needed. Managing object memory is a matter of performance; if an application doesn’t free unneeded objects, its memory footprint grows and performance suffers. Memory management in a Cocoa application that doesn’t use garbage collection is based on a reference counting model.

- Application memory management is the process of allocating memory during your program’s runtime, using it, and freeing it when you are done with it. A well-written program uses as little memory as possible. In Objective-C, it can also be seen as a way of distributing ownership of limited memory resources among many pieces of data and code. When you have finished working through this guide, you will have the knowledge you need to manage your application’s memory by explicitly managing the life cycle of objects and freeing them when they are no longer needed.

## Objective-C provides two methods of application memory management.
1. **Manual retain-release**:In the method described in this guide, referred to as “manual retain-release” or MRR, you explicitly manage memory by keeping track of objects you own. This is implemented using a model, known as reference counting, that the Foundation class NSObject provides in conjunction with the runtime environment.

2. **Automatic Reference Counting**, or ARC, the system uses the same reference counting system as MRR, but it inserts the appropriate memory management method calls for you at compile-time. You are strongly encouraged to use ARC for new projects. If you use ARC, there is typically no need to understand the underlying implementation described in this document, although it may in some situations be helpful.

## Reference counting:
When you create or copy an object, its retain count is 1. Thereafter other objects may express an ownership interest in your object, which increments its retain count. The owners of an object may also relinquish their ownership interest in it, which decrements the retain count. When the retain count becomes zero, the object is deallocated (destroyed).

## Memory-Management Rules:
Memory-management rules, sometimes referred to as the ownership policy.
- You own any object you create by allocating memory for it or copying it.
    *Related methods: alloc, allocWithZone:, copy, copyWithZone:, mutableCopy, mutableCopyWithZone:*

- If you are not the creator of an object, but want to ensure it stays in memory for you to use, you can express an ownership interest in it.
    *Related method: retain*

- If you own an object, either by creating it or expressing an ownership interest, you are responsible for releasing it when you no longer need it.
    *Related methods: release, autorelease*

- Conversely, if you are not the creator of an object and have not expressed an ownership interest, you must not release it.

If you receive an object from elsewhere in your program, it is normally guaranteed to remain valid within the method or function it was received in. If you want it to remain valid beyond that scope, you should retain or copy it. If you try to release an object that has already been deallocated, your program crashes.

## Aspects of Memory Management:
The following concepts are essential to understanding and properly managing object memory:
1. **Autorelease pools**. Sending autorelease to an object marks the object for later release, which is useful when you want the released object to persist beyond the current scope. Autoreleasing an object puts it in an autorelease pool (an instance of NSAutoreleasePool), which is created for an arbitrary program scope. When program execution exits that scope, the objects in the pool are released.

2. **Deallocation**. When an object’s retain count drops to zero, the runtime calls the dealloc method of the object’s class just before it destroys the object. A class implements this method to free any resources the object holds, including objects pointed to by its instance variables.

3. **Factory methods**. Many framework classes define class methods that, as a convenience, create objects of the class for you. These returned objects are not guaranteed to be valid beyond the receiving method’s scope.

## Object life cycle:
An object’s life cycle—that is, its runtime life from its creation to its destruction—is marked or determined by various messages it receives. An object comes into being when a program explicitly allocates and initializes it or when it makes a copy of another object. An object can also begin its runtime life during unarchiving, when it is asked to decode itself from the archive byte stream. If an object was unarchived from a nib file, it receives an *awakeFromNib* message after all objects in the nib file have been loaded into memory.

## Good Practices Prevent Memory-Related Problems:
There are two main kinds of problem that result from incorrect memory management:
1. Freeing or overwriting data that is still in use.
This causes memory corruption, and typically results in your application crashing, or worse, corrupted user data.

2. Not freeing data that is no longer in use causes memory leaks.
A memory leak is where allocated memory is not freed, even though it is never used again. Leaks cause your application to use ever-increasing amounts of memory, which in turn may result in poor system performance or your application being terminated.


## Using Autorelease Pool Blocks:
Autorelease pool blocks provide a mechanism whereby you can relinquish ownership of an object, but avoid the possibility of it being deallocated immediately (such as when you return an object from a method). Typically, you don’t need to create your own autorelease pool blocks, but there are some situations in which either you must or it is beneficial to do so.

An autorelease pool block is marked using @autoreleasepool, as illustrated in the following example:

    @autoreleasepool {
        // Code that creates autoreleased objects.
    }
    
At the end of the autorelease pool block, objects that received an autorelease message within the block are sent a release message—an object receives a release message for each time it was sent an autorelease message within the block.

Like any other code block, autorelease pool blocks can be nested:

    @autoreleasepool {
        // . . .
        @autoreleasepool {
            // . . .
        }
        . . .
    }
    
(You wouldn’t normally see code exactly as above; typically code within an autorelease pool block in one source file would invoke code in another source file that is contained within another autorelease pool block.) For a given autorelease message, the corresponding release message is sent at the end of the autorelease pool block in which the autorelease message was sent.

Cocoa always expects code to be executed within an autorelease pool block, otherwise autoreleased objects do not get released and your application leaks memory.

If you send an autorelease message outside of an autorelease pool block, Cocoa logs a suitable error message.

The AppKit and UIKit frameworks process each event-loop iteration (such as a mouse down event or a tap) within an autorelease pool block. Therefore you typically do not have to create an autorelease pool block yourself, or even see the code that is used to create one. There are, however, three occasions when you might use your own autorelease pool blocks:

   - If you are writing a program that is not based on a UI framework, such as a command-line tool.
   - If you write a loop that creates many temporary objects.
        You may use an autorelease pool block inside the loop to dispose of those objects before the next iteration. Using an autorelease pool block in the loop helps to reduce the maximum memory footprint of the application.
   - If you spawn a secondary thread.
        You must create your own autorelease pool block as soon as the thread begins executing; otherwise, your application will leak objects.

## Use Local Autorelease Pool Blocks to Reduce Peak Memory Footprint:
Many programs create temporary objects that are autoreleased. These objects add to the program’s memory footprint until the end of the block. In many situations, allowing temporary objects to accumulate until the end of the current event-loop iteration does not result in excessive overhead; in some situations, however, you may create a large number of temporary objects that add substantially to memory footprint and that you want to dispose of more quickly. In these latter cases, you can create your own autorelease pool block. At the end of the block, the temporary objects are released, which typically results in their deallocation thereby reducing the program’s memory footprint.

The following example shows how you might use a local autorelease pool block in a for loop.

    NSArray *urls = <# An array of file URLs #>;
    for (NSURL *url in urls) {
        @autoreleasepool {
            NSError *error;
            NSString *fileContents = [NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:&error];
            /* Process the string, creating and autoreleasing more objects. */
        }
    }

The for loop processes one file at a time. Any object (such as fileContents) sent an autorelease message inside the autorelease pool block is released at the end of the block.

After an autorelease pool block, you should regard any object that was autoreleased within the block as “disposed of.” Do not send a message to that object or return it to the invoker of your method. If you must use a temporary object beyond an autorelease pool block, you can do so by sending a retain message to the object within the block and then send it autorelease after the block, as illustrated in this example:

    – (id)findMatchingObject:(id)anObject {
     id match;
        while (match == nil) {
            @autoreleasepool {
                /* Do a search that creates a lot of temporary objects. */
                match = [self expensiveSearchForObject:anObject];
                if (match != nil) {
                    [match retain]; /* Keep match around. */
                }
            }
        }
        return [match autorelease];   /* Let match go and return it. */
    }

Sending retain to match within the autorelease pool block the and sending autorelease to it after the autorelease pool block extends the lifetime of match and allows it to receive messages outside the loop and be returned to the invoker of findMatchingObject:.

## Autorelease Pool Blocks and Threads:
Each thread in a Cocoa application maintains its own stack of autorelease pool blocks. If you are writing a Foundation-only program or if you detach a thread, you need to create your own autorelease pool block.

If your application or thread is long-lived and potentially generates a lot of autoreleased objects, you should use autorelease pool blocks (like AppKit and UIKit do on the main thread); otherwise, autoreleased objects accumulate and your memory footprint grows. If your detached thread does not make Cocoa calls, you do not need to use an autorelease pool block.

**Note**: *If you create secondary threads using the POSIX thread APIs instead of NSThread, you cannot use Cocoa unless Cocoa is in multithreading mode. Cocoa enters multithreading mode only after detaching its first NSThread object. To use Cocoa on secondary POSIX threads, your application must first detach at least one NSThread object, which can immediately exit. You can test whether Cocoa is in multithreading mode with the NSThread class method isMultiThreaded.*
