# What's new Swift

## What's new in Swift 2.2
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

## What's new in Swift 3.0
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

## What's new in Swift 4.0
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

## What’s new in Swift 4.1
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


## What’s new in Swift 4.2
