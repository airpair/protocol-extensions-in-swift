Let's say you want a `clamped` function in Swift, where you'd make sure a given number is limited within a range. So that:

```swift

20.clamped(min: 1, max: 10) // gives 10
3.clamped(min: 1, max: 10) // gives 3

```

## Extending Int

What first comes to mind is extending the struct `Int` and adding the method there.

```swift
extension Int {
    func clamped(min min: Int, max: Int) -> Int {
        if self < min { // If self is smaller than the minimum
            return min // We'll return the minimum
        }
        
        if self > max { // If self is greater than the maximum
            return max // We'll return the maximum
        }
        
        return self // Otherwise we'll return self as we're inside the bounds
    }
}

```

Awesome. Now our sample code works.

But what if we try to clamp a double?

```swift
(20.3).clamped(min: 1, max: 10) // Compiler error: 'Double' does not have a member named 'clamped'
```
At this point, you might be thinking of extending `Double` type to add the `clamped` function there too. But what about `UInt`? Or `CGFloat` or `Int32`? There are a lot of number types in Swift, and all of them **can** be clamped just like the `Int` but you can't extend them all...

## A Swift Solution

Well, when you inspect our `clamped` method, you'll see that we actually only use 2 parameters inside it. Apart from `>` and `<` operator functions, there is no other functions or methods that we use. Now we know that those functions are very generic and a lot of types in Swift implement them by default.

Hmm... Those two look familiar. Inspect the Swift headers and you'll see this:

```swift
public protocol Comparable : Equatable {
    public func <(lhs: Self, rhs: Self) -> Bool
    public func <=(lhs: Self, rhs: Self) -> Bool
    public func >=(lhs: Self, rhs: Self) -> Bool
    public func >(lhs: Self, rhs: Self) -> Bool
}
```

Now with Swift 2.0, we can actually extend `Comparable` itself! Just replace `extension Int` with `extension Comparable` and other `Int`s with `Self` and voila:

```swift
extension Comparable {
    func clamped(min min: Self, max: Self) -> Self {
        if self < min {
            return min
        }
        if self > max {
            return max
        }
        return self
    }
}
```

## Clamp All the Things
Now every type that conforms to Comparable can be clamped by things of it's own type.

```swift
(20.3).clamped(min: 0.9, max: 10.1) // Gives 10.1

let number: CGFloat = 3.2
number.clamped(min: 0.9, max: 10.1) // Gives 3.2
```

We can even clamp `String`s!

```swift
"a".clamped(min: "x", max: "z") // Gives x
"y".clamped(min: "x", max: "z") // Gives y
```

Being able to extend protocols is a huge update to Swift. 
