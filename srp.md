# Single responsibility principle (SPR)

> A class should have only one reason to change.
>
> Robert C. Martin

Or as Wiki define:

> Every module, class or function in a computer program should have responsibility over a single part of that program's functionality, and it should encapsulate that part."

So it's not about classes, it's about "software entities". And "responsibility" here means "reason to change".

## Problems

### Unwanted change

Change in one place leads to unwanted changes in others.

#### Example

Suppose we have type `Employee` with 3 methods and every method is used by different actor.

```go
type Employee struct{}

// used by bookkeeping
func (a A) CalcPay() {
    // a.regularHours() used here
}

// used by HR
func (a A) ReportHours() {
    // a.regularHours() used here
}

// used by both CalcPay and ReportHours
func (a A) RegularHours() {
    // some code here
}
```

So `CalcPay` and `ReportHours` both depends on `RegularHours` while all of them has _different reasons to change_.
This is violating of SRP and that will lead to problems.

Now imagine that _bookkeeping_ depertament wants us to change the `CalcPay` algorithm.

```go
func (a A) CalcPay() {
    // it now works differently
}
```

As it turns out, _HR_ departament did not want those changes.

System is broken.

### Merge conflicts

Multiple reasons to change leads to higher probability of several programmers making changes in same place so it leads to highter probability of merge conflict.

We let the code be used by different actors at the same time. We _bring actors together_.

## Solution

> Divide code that is used by different actors.

This means "divide the entity into several independent entities according to their responsibilities (for the reasons why they may change)."

### Why it works

Because you can't get unwanted changes in one entity while changing the other if there is no dependency between them.

### Example

```go
type Employee struct {
    ed employeeData // just a data without behaviour
}

func (a A) CalcPay() {
    pc := payCalculator{}
    return pc.calcPay(a.ed)
}

func (a A) ReportHours() {
    hr := hourReporter{}
    return hr.calcPay(a.ed)
}
```

`Employee` type acts as a facade for `payCalculator` and `hourReporter`.

Both `payCalculator` and `hourReporter` are _independent_ from each other (they depend on `employeeData` but it's safe, `employeeData` have no behaviour).

We can now safely change them.

## Conclusion

SRP **is not about classes (or methods) doing one thing**.

It's about **things having just one responsibility each** where _responsibility_ is _reason to change_ and where "thing" is a _software entity_.

SRP says that we **should not** have dependencies between entities that has different reasons to change.

---

DON'T BRING ACTORS TOGETHER
