# Liskov substitution principle (LSP)

> Let `Φ(x)` be a property provable about objects `x` of type `T`. Then `Φ(y)` should be true for objects `y` of type `S` where `S` is a subtype of `T`.
>
> Barbara Liskov

Or as Uncle Bob define:

> Functions that use base type should be able to use subtypes without knowing about it.
>
> Robert C. Martin

## Problem

We add subtypes (derived classes or implemented interfaces) but could not use them as replacement for their base types.

## Example

Imagine we have such code:

```go
type Rect interface {
    SetW(int)
    SetH(int)
    Area() int
}

func workWithRect(r Rect) {
    r.SetW(5)
    r.SetH(2)
    print(r.Area() == 10) // true for current Rect implementation
}
```

We used some concrete implementation of `Rect` and everything was fine but now we want add new one.

```go
type Square struct {
    side int
}

func (r *Rect) SetW(w int) {
    r.setSide(w)
}

func (r *Rect) SetH(h int) {
    r.setSide(h)
}

func (r *Rect) Area() int {
    return r.side * r.side
}

func (s *Square) setSide(n int) {
    s.side = n
}
```

This violates LSP because `r.Area() == 10` in `workWithRect` is now `false`.

- `workWithRect` cannot work with all of `Rect` implementations (e.g. squares)
- `Square` implements useless (in terms of squares) methods `SetW` and `SetH`
