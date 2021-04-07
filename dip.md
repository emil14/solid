## Dependency inversion principle (DIP)

> A. High-level modules should not depend on low-level modules. Both should depend on abstractions.
> B. Abstractions should not depend on details. Details should depend on abstractions.

Here's a few rules:

- Dependencies should point to abstractions, not concrete implementations.
- But dependencies from _stable_ (rarely changing) implementations are allowed.
- Every change of abstraction leads to changes in implementation.
- But not vice versa so abstractions are more stable than their implementations.

Knowing that a couple of advices could be spelled:

1. Avoid depending on implementations as they are less stable than abstractions. Depend on abstractions instead.
2. Among all types of dependency especially be carefull with implementation extenting. It is the strongest coupling.

## Problems

The more coupling we have the more modifications we have to do.

Every SOLID violation leads to stronger coupling but

Modification of one entity leads to modification of the other.

```go
package app

import (
    "project/ui"
    "project/web"
)

type App struct {
    ui ui.Web
    db db.MySQL
}

func (a App) UserPage(email string) string {
    u := a.db.UserByEmail(email)
    html := a.ui.Render(u)
    return html
}
```

Imagine `ui` changed `Render` and it now returns struct instead html string.
Imagine `db` removed `UserByEmail` method and replaced it with more general `UserByFilter` method with a compleat different signature.

**You have to modify your code.**

And you'll have to modify it very often. Because `web.UI` and `db.MySQL` are _less important_ than `App`. Their are _details_. _Details_ tend to change more often than _business rules_.

`App` **must not** depend on `ui` and `db` packages **at all**.

## Solution

Instead of that `ui` and `db` should depend on `app`.

```go
package app

type App struct {
    ui UI
    db DB
}

func (a App) UserPage(email string) string {
    u := a.db.UserByEmail(email)
    html := a.ui.Render(u)
    return html
}

type UI interface {
    Render(u User) string
}

type DB interface {
    UserByEmail(email string) User
}

type User struct {
    age   int
    name  string
    email string
}
```

No dependencies in App. Here's what we have in `ui` package

```go
package ui

import (
    "app"
)

type Web struct {
    // some internal state
}

func (w Web) Render(u app.User) string {
    // return html
}
```

And here's the `db` package:

```go
import (
    "app"
)

type MySQL struct {
    // some internal state
}

func (w Web) UserByEmail(email string) app.User {
    // select user by email from mysql database
}
```

### Why it works

To _protect_ component "A" from changes in component "B", one should make "A" dependency of "B".

This way "A" doesn't know about "B" or we can say "A" is _independent_ from "B".

Changes in "B" cannot lead to changes in "A".

## Conclusion

Dependency flow has been inverted.

Instead of hight-level components depending on low-level components we have details depended from abstractions in hight level component.

---

LET HIGHER LEVEL MODULES OWN ABSTRACTIONS

AND MAKE LOWER LEVEL MODULES DEPEND ON THAT ABSTRACTIONS