# Delegation
**Delegation is a simple and powerful pattern in which one object in a program acts on behalf of, or in
coordination with, another object.**

1. The delegating object keeps a reference to the other object—the delegate—and at the appropriate time sends a message to it.
2. The message informs the delegate of an event that the delegating object is about to handle or has just handled.
3. The delegate may respond to the message by updating the appearance or state of itself or other objects in the application, and in some cases it can return a value that affects how an impending event is handled.
4. The main value of delegation is that it allows you to easily customize the behavior of several objects in one central object.

## Delegation and the Cocoa Frameworks
* The delegating object is typically a framework object, and the delegate is typically a custom controller
object.
* In a **managed memory environment**, the delegating object maintains a `weak reference` to its
`delegate`;
* In a **garbage‑collected environment**, the receiver maintains a `strong reference` to its `delegate`.

## Example:
An example of a delegating object is an instance of the `NSWindow` class of the AppKit framework.
1. NSWindow declares a protocol, among whose methods is `windowShouldClose:`.
2. When a user clicks the close box in a window, the window object sends `windowShouldClose:` to its delegate to ask it to confirm the closure of the window.
3. The delegate returns a Boolean value, thereby controlling the behavior of the window object.

## Data Source
A data source is almost identical to a delegate. The difference is in the relationship with the delegating
object. Instead of being delegated control of the user interface, **a data source is delegated control of
data**. The delegating object, typically a view object such as a table view, holds a reference to its data
source and occasionally asks it for the data it should display. **A data source, like a delegate, must adopt
a protocol and implement at minimum the required methods of that protocol. Data sources are
responsible for managing the memory of the model objects they give to the delegating view.**

## Delegation and Notifications
The delegate of most Cocoa framework classes is automatically registered as an observer of notifications
posted by the delegating object. The delegate need only implement a notification method declared by
the framework class to receive a particular notification message.

Following the example above, a window object posts an `NSWindowWillCloseNotification` to observers but sends a `windowShouldClose:` message to its delegate.

# Notification
**A notification is a message sent to one or more observing objects to inform them of an event in a program.**
* The notification mechanism of Cocoa follows a `broadcast model`.
* It is a way for an object that initiates or handles a program event to communicate with any number of objects that want to know about that event.
* These recipients of the notification, known as `observers`, can adjust their own appearance, behavior, and state in response to the event.
* The object sending (or posting) the notification doesn’t have to know what those observers are.
* Notification is thus a powerful mechanism for attaining coordination and cohesion in a program. It reduces the need for strong dependencies between objects in a program (such dependencies would reduce the reusability of those objects).
* Many classes of the Foundation, AppKit, and other Objective-C frameworks define notifications that your program can register to observe.

## Notification Mechanism
The centerpiece of the notification mechanism is a `per-process singleton object` known as the notification center (`NSNotificationCenter`).

1. When an object posts a notification, it goes to the notification center, which acts as a kind of clearing house and broadcast center for notifications.
2. Objects that need to know about an event elsewhere in the application register with the notification center to let it know they want to be notified when that event happens.
3. Although the notification center delivers a notification to its observers synchronously, you can post notifications asynchronously using a notification queue (`NSNotificationQueue`).

## The Notification Object
* A notification is represented by an instance of the `NSNotification` class.
* A notification object contains several bits of state: a *unique name, the posting object, and (optionally) a dictionary of supplementary information, called the userInfo dictionary*.
* When a notification is delivered to an interested observer, the notification object is passed in as an argument of the method handling the notification.

## Observing a Notification
* To observe a notification, obtain the singleton `NSNotificationCenter` instance and send it an `addObserver:selector:name:object:` message.
* Typically, this registration step is done shortly after your application launches. The second parameter of the `addObserver:selector:name:object:` method is a selector that identifies the method that you implement to handle the notification.
* The method must have the following signature:

```
- (void)myNotificationHandler:(NSNotification *)notif;
```

In this handling method, you can extract information from the notification to help you in your response, especially data in the `userInfo` dictionary (if one exists).

## Posting a Notification
* Before posting a notification, you should define a *unique global string constant as the name of your notification*.
* A convention is to use a two- or three-letter application-specific prefix for the name, for example:
```
NSString *AMMyNotification = @"AMMyNotication";
```

* To post the notification, send a `postNotificationName:object:userInfo:` (or similar) message to the singleton `NSNotificationCenter` object. This method creates the notification object before it sends the notification to the notification center.


# Protocol
**A protocol declares a programmatic interface that any class may choose to implement.**

* Protocols make it possible for two classes distantly related by inheritance to communicate with each other to
accomplish a certain goal. They thus offer an alternative to subclassing.
* Any class that can provide behavior useful to other classes may declare a programmatic interface for vending that behavior anonymously.
* Any other class may choose to adopt the protocol and implement one or more of its methods, thereby making use of the behavior.
* The class that declares a protocol is expected to call the methods in the protocol if they are implemented by the protocol adopter.

## Formal and Informal Protocols
There are two varieties of protocol, formal and informal:

1. **A formal protocol** declares a list of methods that client classes are expected to implement.
    * Formal protocols have their own declaration, adoption, and type‑checking syntax. You can designate methods whose implementation is required or optional with the @required and @optional keywords.
    * Subclasses inherit formal protocols adopted by their ancestors. A formal protocol can also adopt other protocols.
*Formal protocols are an extension to the Objective‑C language.*

2. **An informal protocol** is a category on NSObject, which implicitly makes almost all objects
adopters of the protocol. *(A category is a language feature that enables you to add methods to a
class without subclassing it.)*
    * Implementation of the methods in an informal protocol is optional.
    * Before invoking a method, the calling object checks to see whether the target object implements it.
    * Until optional protocol methods were introduced in Objective‑C 2.0, informal protocols were essential to the way Foundation and AppKit classes implemented delegation.

## Adopting and Conforming to a Formal Protocol
