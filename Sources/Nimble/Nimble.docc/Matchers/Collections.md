# Collection

Nimble allows you to easily check `Collection`s.

## Collection Count

```swift
// Swift

// Passes if 'actual' contains the 'expected' number of elements:
expect(actual).to(haveCount(expected))

// Passes if 'actual' does _not_ contain the 'expected' number of elements:
expect(actual).notTo(haveCount(expected))
```

```objc
// Objective-C

// Passes if 'actual' contains the 'expected' number of elements:
expect(actual).to(haveCount(expected))

// Passes if 'actual' does _not_ contain the 'expected' number of elements:
expect(actual).notTo(haveCount(expected))
```

For Swift, the actual value must be an instance of a type conforming to `Collection`.
For example, instances of `Array`, `Dictionary`, or `Set`.

For Objective-C, the actual value must be one of the following classes, or their subclasses:

 - `NSArray`,
 - `NSDictionary`,
 - `NSSet`, or
 - `NSHashTable`.

## Collection Elements

### Swift

In Swift, the collection must be an instance of a type conforming to
`Sequence`.

```swift
// Swift

// Providing a custom function:
expect([1, 2, 3, 4]).to(allPass { $0 < 5 })

// Composing the expectation with another matcher:
expect([1, 2, 3, 4]).to(allPass(beLessThan(5)))
```

There are also variants of `allPass` that check against async matchers, and
that take in async functions:

```swift
// Swift

// Providing a custom function:
expect([1, 2, 3, 4]).to(allPass { await asyncFunctionReturningBool($0) })

// Composing the expectation with another matcher:
expect([1, 2, 3, 4]).to(allPass(someAsyncMatcher()))
```

### Objective-C

In Objective-C, the collection must be an instance of a type which implements
the `NSFastEnumeration` protocol, and whose elements are instances of a type
which subclasses `NSObject`.

Additionally, unlike in Swift, there is no override to specify a custom
matcher function.

```objc
// Objective-C

expect(@[@1, @2, @3, @4]).to(allPass(beLessThan(@5)));
```

## Collection Membership

```swift
// Swift

// Passes if all of the expected values are members of 'actual':
expect(actual).to(contain(expected...))

// Passes if 'actual' is empty (i.e. it contains no elements):
expect(actual).to(beEmpty())
```

```objc
// Objective-C

// Passes if expected is a member of 'actual':
expect(actual).to(contain(expected));

// Passes if 'actual' is empty (i.e. it contains no elements):
expect(actual).to(beEmpty());
```

> In Swift `contain` takes any number of arguments. The expectation
  passes if all of them are members of the collection. In Objective-C,
  `contain` only takes one argument [for now](https://github.com/Quick/Nimble/issues/27).

For example, to assert that a list of sea creature names contains
"dolphin" and "starfish":

```swift
// Swift

expect(["whale", "dolphin", "starfish"]).to(contain("dolphin", "starfish"))
```

```objc
// Objective-C

expect(@[@"whale", @"dolphin", @"starfish"]).to(contain(@"dolphin"));
expect(@[@"whale", @"dolphin", @"starfish"]).to(contain(@"starfish"));
```

> `contain` and `beEmpty` expect collections to be instances of
  `NSArray`, `NSSet`, or a Swift collection composed of `Equatable` elements.

To test whether a set of elements is present at the beginning or end of
an ordered collection, use `beginWith` and `endWith`:

```swift
// Swift

// Passes if the elements in expected appear at the beginning of 'actual':
expect(actual).to(beginWith(expected...))

// Passes if the the elements in expected come at the end of 'actual':
expect(actual).to(endWith(expected...))
```

```objc
// Objective-C

// Passes if the elements in expected appear at the beginning of 'actual':
expect(actual).to(beginWith(expected));

// Passes if the the elements in expected come at the end of 'actual':
expect(actual).to(endWith(expected));
```

> `beginWith` and `endWith` expect collections to be instances of
  `NSArray`, or ordered Swift collections composed of `Equatable`
  elements.

  Like `contain`, in Objective-C `beginWith` and `endWith` only support
  a single argument [for now](https://github.com/Quick/Nimble/issues/27).

For code that returns collections of complex objects without a strict
ordering, there is the `containElementSatisfying` matcher:

```swift
// Swift

struct Turtle {
    let color: String
}

let turtles: [Turtle] = functionThatReturnsSomeTurtlesInAnyOrder()

// This set of matchers passes regardless of whether the array is 
// [{color: "blue"}, {color: "green"}] or [{color: "green"}, {color: "blue"}]:

expect(turtles).to(containElementSatisfying({ turtle in
    return turtle.color == "green"
}))
expect(turtles).to(containElementSatisfying({ turtle in
    return turtle.color == "blue"
}, "that is a turtle with color 'blue'"))

// The second matcher will incorporate the provided string in the error message
// should it fail
```

> Note: in Swift, `containElementSatisfying` also has a variant that takes in an
async function.

```objc
// Objective-C

@interface Turtle : NSObject
@property (nonatomic, readonly, nonnull) NSString *color;
@end

@implementation Turtle 
@end

NSArray<Turtle *> * __nonnull turtles = functionThatReturnsSomeTurtlesInAnyOrder();

// This set of matchers passes regardless of whether the array is 
// [{color: "blue"}, {color: "green"}] or [{color: "green"}, {color: "blue"}]:

expect(turtles).to(containElementSatisfying(^BOOL(id __nonnull object) {
    return [[turtle color] isEqualToString:@"green"];
}));
expect(turtles).to(containElementSatisfying(^BOOL(id __nonnull object) {
    return [[turtle color] isEqualToString:@"blue"];
}));
```

For asserting on if the given `Comparable` value is inside of a `Range`, use the `beWithin` matcher.

```swift
// Swift

// Passes if 5 is within the range 1 through 10, inclusive
expect(5).to(beWithin(1...10))

// Passes if 5 is not within the range 2 through 4.
expect(5).toNot(beWithin(2..<5))
```
