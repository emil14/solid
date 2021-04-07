# Open-closed Principle (OCP)

> Software entities should be open for extension, but closed for modification
>
> Bertrand Meyer

Here's what it means

- _Entity opened for extention_ means **it's easy to extend it's behavior** where _extend behavior_ is "extend class" or "implement interface".
- _Entity closed for modification_ means **there is no need in _modifying_ it in order to _extend its behaviour_**.

So if it's hard to _extend_ or there's a need in _modifying_ - an Open-Closed Principle is violated.

## Problems

1. One cannot extend entity and code need to be duplicated.
2. One cannot extend entity without modifying it.

## Example

Suppose we have this

```go
func countWords() {
    file, _ := ioutil.ReadFile("/path_to_file") // ignore err
    s := strings.Split(string(file), " ")
    return len(s)
}
```

Now imagine if we have to add an ability to count words in a string from url

```go
func countWords(file bool) int {
    var s string
    if file {
        file, _ := ioutil.ReadFile("/path_to_file")
        s = string(file)
    } else {
        resp, _ := http.Get("http://example.com/")
        s = string(resp.Body())
    }
    return len(strings.Split(s, " "))
}
```

Very soon function will become consisting of dozens `if` statement in multiple different places.

`countWords` violates not only OCP (it's hard to extend it) but also SPR (making a request and reading from a file are different responsibilities).

## Solution

Move _less important_ parts out of the _more important_ ones and make them _depend_ on them.

```go
func countWords(s string) int {
    return len(strings.Split(s, " "))
}

func countWordsUrl() int {
    file, _ := ioutil.ReadFile("/path_to_file")
    return countWords(string(file))
}

func countWordsFile() int {
    resp, _ := http.Get("http://example.com/")
    return countWords(string(resp.Body()))
}
```

Note than `countWords` now takes `string` which is a very common data type and it makes it easy to use `countWords` in a lot of scenarios.

In more complex cases we should use abstract types like interfaces. Because abstract requirements are easyier to satisfy than concrete ones. `io.Writer` and `io.Reader` are good examples of this.

### Why it works

Because `countWords` now works with any string and we can create new functions by composing it (we can _extend its behaviour_) and without modifying its code (it is _closed for modification_).

This is a simple example. In real life we could use interface or abstract classes to handle more complicated cases.

## Role in architecture

We should divide the system into components where the more important component is the more independent it should be.

In such system we're allowed to create new less important components using more important ones without worrying about
modifying those more important components.

For example `web-presenter` component may depend on `controller` component but not vice versa. This way `web-presenter` is safe from modifications in `web-presenter`.

## Conclusion

OCP **is not about encapsulation**.

OCP is about **make it easy to extend things without modifying them**.

---

MAKE IT EASY TO EXTEND THINGS
