# What's new Swift

* [Swift 2.2](#What's-new-in-Swift-2.2)
* [Swift 3.0](#What's new-in-Swift-3.0)
* [Swift 4.0](#What's-new-in-Swift-4.0)
* [Swift 4.1](#What’s-new-in-Swift-4.1)
* [Swift 4.2](#What’s-new-in-Swift-4.2)


# What's new in Swift 2.2
- ++ and -- are deprecated
- Why??
    - Writing `++` rather than `+=` 1 is hardly a dramatic time saving
    - Although it's easy once you know it, `++` doesn't have an obvious meaning to people learning Swift, whereas += at least reads as "add and assign."
    - C-style loops – one of the most common situations where `++` and `--` were used – have also been deprecated
- Traditional C-style for loops are deprecated.
- Arrays and other slice types now have `removeFirst()`
    Warning: whereas `removeLast()` has an `optional` equivalent, `popLast()`, there is no optional equivalent for `removeFirst()`. This means if you call it on an empty array, your code will crash.
- You can now compare tuples (within reason).
- Tuple splat syntax is deprecated.
- More keywords can be used as argument labels.
- var parameters have been deprecated.
    Reason: `var` parameters are deprecated because they offer only marginal usefulness, and are frequently confused with `inout`.
- Renamed debug identifiers: `#line, #function, #file`
    Swift 2.1 and earlier used the "screaming snake case" symbols __FILE__, __LINE__, __COLUMN__, and __FUNCTION__, which automatically get replaced the compiler by the filename, line number, column number and function name where they appear.
- Stringified selectors are deprecated
    `navigationItem.rightBarButtonItem = UIBarButtonItem(title: "Tap!", style: .Plain, target: self, action: "buttonTaped")`
    If you look closely, I wrote `"buttonTaped"` rather than `"buttonTapped"`, but Xcode wasn't able to notify me of my mistake if either of those methods didn't exist.

    This has been resolved as of Swift 2.2: using strings for selectors has been deprecated, and you should now write `#selector(buttonTapped)` in that code above. If the `buttonTapped()` method doesn't exist, you'll get a compile error – another whole class of bugs eliminated at compile time!
- Compile-time Swift version checking.
- New documentation keywords: recommended, recommendedover, and keyword.

# What's new in Swift 3.0
- All function parameters have labels unless you request otherwise.
    `SKAction.rotateByAngle(CGFloat(M_PI_2), duration: 10)
    SKAction.rotate(byAngle: CGFloat(M_PI_2), duration: 10)

    UIFont.preferredFontForTextStyle(UIFontTextStyleSubheadline)
    UIFont.preferredFont(forTextStyle: UIFontTextStyle.subheadline)`
- Omit needless words
    `let blue = UIColor.blueColor()
    let min = numbers.minElement()
    with
    let blue = UIColor.blue
    let min = numbers.min()`
- UpperCamelCase has been replaced with lowerCamelCase for enums and properties.
    `UIInterfaceOrientationMask.Portrait // old
    UIInterfaceOrientationMask.portrait // new

    NSTextAlignment.Left // old
    NSTextAlignment.left // new`
- Swifty importing of C functions
- Verbs and nouns

# What's new in Swift 4.0
- Swifty encoding and decoding.
    `Codable protocol` that lets you `serialize` and `deserialize` custom data types without writing any special code – and without having to worry about losing your value types. Even better, you can choose how you want the data to be serialized: you can use `classic property list` format or even `JSON`.

    `struct Language: Codable {
    var name: String
    var version: Int
    }

    let swift = Language(name: "Swift", version: 4)`

    `let encoder = JSONEncoder()
    if let encoded = try? encoder.encode(swift) {
        // save `encoded` somewhere
    }`

- Multi-line string literals.
    """
    hey
    there,
    """
- Improved keypaths for key-value coding
- Improved dictionary functionality
- Strings are collections again!
    You can reverse them, loop over them character-by-character, map() and flatMap() them, and more.
- One-sided ranges
    `let characters = ["XYZ", "Captain America", "Switty", "Horse", "Mouist"]
    let bigParts = characters[..<3]
    let smallParts = characters[3...]`

# What’s new in Swift 4.1
- Synthesized Equatable and Hashable
    The `Equatable` protocol allows Swift to compare one instance of a type against another. When we say `5 == 5`, Swift understands what that means because Int conforms to Equatable, which means it implements a function describing what `==` means for two instances of Int.

    Swift 4.1 also introduces synthesized support for the `Hashable protocol`, which means it will generate a `hashValue` property for conforming types automatically.

- Conditional conformances.
- Recursive constraints on associated types.
    `protocol Employee {
        associatedtype Manager: Employee
        var manager: Manager? { get set }
    }`
    Note: We've used an optional Manager? because ultimately one person (presumably the CEO) has no manager
- Build configuration import testing.
- Target environment testing.
    `#if targetEnvironment(simulator)
    // code for the simulator here
    #else
    // code for real devices here
    #endif`
- flatMap is now (partly) compactMap.


# What’s new in Swift 4.2
- Derived collections of enum cases.
    `CaseIterable protocol` that automatically generates an array property of all cases in an enum.

    `enum Pasta: CaseIterable {
        case cannelloni, fusilli, linguine, tagliatelle
    }`

    `for shape in Pasta.allCases {
        print("I like eating \(shape).")
    }`

- Warning and error diagnostic directives.
    The two new directives are `#warning` and `#error:` the former will force Xcode to issue a warning when building your code, and the latter will issue a compile error so your code won’t build at all. Both of these are useful for different reasons:

    - `#warning` is mainly useful as a reminder to yourself or others that some work is incomplete. Xcode templates often use `#warning` to mark method stubs that you should replace with your own code.

    - `#error` is mainly useful if you ship a library that requires other developers to provide some data. For example, an authentication key for a web API – you want users to include their own key, so using `#error` will force them to change that code before continuing.

    -  `#warning("This is terrible method of encryption")`
    -  `#error("Please enter your API key below then delete this line.")`

- Dynamic member look up.
    At the core of this feature is a new attribute called @dynamicMemberLookup, which instructs Swift to call a subscript method when accessing properties. This subscript method, subscript(dynamicMember:), is required: you’ll get passed the string name of the property that was requested, and can return any value you like.

    `@dynamicMemberLookup
    struct Person {
        subscript(dynamicMember member: String) -> String {
            let properties = ["name": "Taylor Swift", "city": "Nashville"]
            return properties[member, default: ""]
        }
    }`

    `let person = Person()
    print(person.name)
    print(person.city)
    print(person.favoriteIceCream)
    `
    That will compile cleanly and run, even though `name, city, and favoriteIceCream` do not exist as properties on the `Person` type. Instead, they are all looked up at runtime: that code will print `“Taylor Swift” and “Nashville”` for the first two calls to `print(),` then an empty string for the final one because our dictionary doesn’t store anything for `favoriteIceCream`.

- Enhanced conditional conformances.
- Random number generation and shuffling.
    You can generate random numbers by calling the `random()` method on whatever numeric type you want, providing the range you want to work with. For example, this generates a random number in the range 1 through 4, inclusive on both sides:

    `let randomInt = Int.random(in: 1..<5)`

- Simpler, more secure hashing.
    From Swift 4.1 onwards conformance to `Hashable` can be synthesized by the compiler. However, if you want your own hashing implementation – for example, if your type has many properties but you know that one of them was enough to identify it uniquely – you still need to write your own code using whatever algorithm you thought was best.

    `struct iPad: Hashable {
        var serialNumber: String
        var capacity: Int

        func hash(into hasher: inout Hasher) {
            hasher.combine(serialNumber)
        }
    }`

    `let first = iPad(serialNumber: "12345", capacity: 256)
    let second = iPad(serialNumber: "54321", capacity: 512)

    var hasher = Hasher()
    hasher.combine(first)
    hasher.combine(second)
    let hash = hasher.finalize()`

- Checking sequence elements match a condition.
    `allSatisfy()` method that checks whether all items in a sequence pass a condition. For example, if we had an array of exam results like this:

    `let scores = [85, 88, 95, 92]`

    We could decide whether a student passed their course by checking whether all their exam results were 85 or higher:

    `let passed = scores.allSatisfy { $0 >= 85 }`

- In-place collection element removal.
    `removeAll(where:)` method that performs a high-performance, in-place filter for collections. You give it a closure condition to run, and it will strip out all objects that match the condition.

    For example, if you have a collection of names and want to remove people called “Terry”, you’d use this:
    `var pythons = ["John", "Michael", "Graham", "Terry", "Eric", "Terry"]
    pythons.removeAll { $0.hasPrefix("Terry") }
    print(pythons)`

    Now, you might very well think that you could accomplish that by using filter() like this:
    `pythons = pythons.filter { !$0.hasPrefix("Terry") }
    `

- Boolean toggling.
    `toggle()` method to booleans that flip them between true and false.

    The entire code to implement proposal is only a handful of lines of Swift:
    `extension Bool {
       mutating func toggle() {
          self = !self
       }
    }`

    However, the end result makes for much more natural Swift code:
    `var loggedIn = false
    loggedIn.toggle()`
