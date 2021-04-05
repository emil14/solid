# Liskov substitution principle (LSP)

> Let `Φ(x)` be a property provable about objects `x` of type `T`. Then `Φ(y)` should be true for objects `y` of type `S` where `S` is a subtype of `T`.
>
> Barbara Liskov

Or as Uncle Bob defines:

> Functions that use base type should be able to use subtypes without knowing about it.
>
> Robert C. Martin

## Problem

We add subtype (derived class or implemented interface) but we can't use it as a replacement for its base type.

## Example

Imagine we have such code:

```go
type Rect interface {
    SetW(int)
    SetH(int)
    Area() int
}

func testRect(r Rect) {
    r.SetW(5)
    r.SetH(2)
    assert(r.Area() == 10) // ok for current Rect implementations
}
```

We used some concrete implementation of `Rect` and everything was fine but now we want add new one.

```go
type Square struct {
    side int
}

func (s *Square) SetW(w int) {
    r.setSide(w)
}

func (s *Square) SetH(h int) {
    r.setSide(h)
}

func (s *Square) Area() int {
    return r.side * r.side
}

func (s *Square) setSide(n int) {
    s.side = n
}
```

This breaks the system because `r.Area() == 10` in `testRect` is now `false`.

To fix this we may do something like this:

```go
func testRect(r Rect) {
    r.SetW(5)
    r.SetH(2)

    switch r.(type) {
    case Square: // special case
        assert(r.Area() == 2)
    default:
        assert(r.Area() == 10)
    }
}
```

This **violates LSP** because `testRect` cannot work with all `Rect` subtypes in the same way!

There should be no special cases in clean code.

## Solutuion

There is no trivial solution for Rectangle/Square problem. Rectangles and squares are not interchangeable, as square introduces a constraint to the parent class. Therefore, a square should not inherit from rectangles.

```go
type Shape interface {
    Area() int
}

type Rect interface {
    Shape
    SetW(int)
    SetH(int)
}

type Square struct {
    size int
}

func (s *Square) Area() int {
    return r.side * r.side
}

func (s *Square) setSide(n int) {
    s.side = n
}
```

`Square` is not `Rect` any longer. Both `Square` and `Rect` are `Shape` now. `Shape` does not enforce any rules regarding width or height. `Square` and `Rect` are interchangable now.

```go
func testRect(r Rect) {
    r.SetW(5)
    r.SetH(2)
    assert(r.Area() == 10) // "r" is never instance of Square now
}

func testSquare(s Square) {
    s.SetSide(5)
    assert(r.Area() == 25)
}

func testShape(s Shape) {
    assert(s.Area() > 0) // Squares and Rectangles are ok with this
}
```

## Conclusion

Your code should work with subtypes without modifications. Keep an eye on what you derive (or implement).
