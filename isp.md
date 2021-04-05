# Interface segregation principle (ISP)

> No client should be forced to depend on methods it does not use.
>
> Robert C. Martin

## Problem

Client depend on code it does not use which means client's code will change any time when depencency will change.

### Example

Imagine we have this `ReadWriteCloser` instead of separate `Reader/Writer/Closer`:

```go
type ReadWriter interface {
    Read([]byte)  error
    Write([]byte) error
}

func Save(f ReadWriteCloser, doc *Document) {
    // f.Write() called somewhere here
}

func Read(f ReadWriteCloser, doc *Document) {
    // f.Read() called somewhere here
}
```

Clients of `Save` now have to implement `Read` method even though it's not used by `Save`. Same for clients of `Read`, they have to implement `Write`.

### Solution

Golang already solved this for us. ISP is one of the main design principles here.

```go
func Save(f io.Writer, doc *Document) {
    // some code here
}

func Read(f io.Reader, doc *Document) {
    // some code here
}
```

### Why it works

Because clients of `Save` function now depend on small `io.Writer` interface. They do not depend on something they don't use. Same goes for `Read` clients.

## Conclusion

> Accept interfaces, return structs.
>
> Jack Lindamood

ISP is all about **having a lot of small interfaces is better than having a few big ones**.
