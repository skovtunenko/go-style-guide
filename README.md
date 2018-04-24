# Preamble
Before proceeding, be prepared to set aside the tricks and ideas you apply in other programming languages. Languages have their differences and some, such as Go, can vary vastly. Keep that in mind as you review styling.
# Table of Contents
* [Brief Summary](#brief-summary)
  * [General Design](#general-design)
  * [General Naming](#general-naming)
  * [General Style](#general-style)
  * [General Testing](#general-testing)
* [Proverbs](#proverbs)
* [Tooling](#tooling)
* [Packages](#packages)
  * [Philosophy](#philosophy)
  * [Structure](#structure)
* [Interfaces](#interfaces)
  * [Usage](#usage)
  * [Names](#names)
* [Types](#types)
  * [Type Usage](#type-usage)
  * [Assertions and Reflection](#assertions-and-reflection)
  * [Tags](#tags)
  * [Getters and Setters](#getters-and-setters)
* [Functions](#functions)
  * [Usage](#function-usage)
  * [Pointer Receivers](#pointer-receivers)
  * [Defer](#defer)
* [SQL Database](#sql-database)
* [Testing](#testing)
  * [Coverage](#coverage)
  * [Test Methods](#test-methods)
  * [Table Driven Tests](#table-driven-tests)
  * [Failures](#failures)
  * [Useful Tools](#useful-tools)
* [Sources](#style-guide-sources)
# Brief Summary
This section is intended to give an upfront, quick view of Go standards. For a more detailed explanation, review the summary then proceed to the rest of the document.
## General Design
* Go is not a functional programming language - do not try to make it one.
* Simplicity and readability are paramount
* Packages should contain code for a specific problem domain. For example, all code *http* code is in the *http* package
* Packages such as *rest*, *utils*, *models*, *datastore*, etc. should not exist. See above point.
* Comments should be meaningful -- they're for the users
* Go doesn't need getters and setters on concrete types - access data structures directly
* Avoid inlining functions unless there's a solid reason for doing so
* Avoid returning functions unless absolutely necessary
* Avoid functions that return a static value (use a constant or var instead!)
* Functions should be limited in their reach, and easy to follow top to bottom without bouncing around
* Try to return early instead of using else statements
* Do not use trailing/unnecessary returns
* Named returns are ok for short functions, but not for medium or long functions
* No two packages of ours should have the same package name
* Use pointer receivers by default, and when nesting objects, unless you have a reason not to
* Avoid any possible nil pointer situations!  This may mean providing a convenience constructor function to initalize/create an object, and adding nil checks before using an exported Object with pointers inside.
* Avoid reflection and type assertions unless absolutely necessary
* Avoid variable shadowing
* Use one struct for working with your object. Ie, do NOT add one for json marhsalling, another for db writes and structs per operations.
* Avoid creating large interfaces. They should only have 1 or 2 functions (ideally)
* Use *defer* to close connections, release mutexes, etc. and declare it shortly after opening that connection, etc.
* Always handle errors.
* Never panic.
## General Naming
* Shorter is Better - Use shorthand descriptive variable names vs long names - eg *conn* vs *tcpSocketConnection*
* Use *CamelCase* or *mixedCamelCase*, not snake case or SoMeThInGaNnOyIng
* All scoped variables should begin with lowercase letters
* All arguments should begin with lower case letters
* Use short, one or two char names for temporary variables(indexes, iterations)
* Use 'ok' as second argument to functions that return a bool of whether an operation completed successfully, such as map indexing or type assertions
* Use descriptiveName for variables used more than a few lines from declaration
* Where possible, use *i* for indexes, *n* for counts(eg bytes read), *err* for errors
* Do not use exported names that stutter with the package name - eg use *netscaler.Client*, not *netscaler.NetscalerClient*
* Packages should be named according to the problem they provide code for.
* Package names should be singular
* Avoid using named imports. If imports clash, named imports can be used, but you should also question content of the packages (if they're our own)
## General Style
* Always run *goimports* before committing work. This does a lot of formatting for you and keeps it consistent.
* Always run *golint*, *go vet* and *errcheck* against your code
* Do not use semicolons to combine lines - eg don't use *if err = do(); err != nil {*.  Use two lines
* Add blank lines before and after if blocks, switches, iterations, etc. unless there is a variable being defined immediately preceding the block
* Format errors with *"%v"*
* Use *fmt.Sprint(f)* when adding more than 3 strings
* Avoid named imports, unless there's a collision
* SQL replacement should always use *?* instead of *"%s"* and the like. Ie, manual string concat/substitution avoids built-in SQL injection protection
* Prefer using PreparedStatements and Named parameters while executing SQL queries.
## General Testing
* Code should have a minimum of 80% coverage
* Use go-sqlmock for rdms mocks (<a href="https://github.com/DATA-DOG/go-sqlmock">go-sqlmock</a>)
* Use interfaces for testing elsewhere
* Use table driven tests
* Make test failure errors useful. Don't just say "it failed"
* *gotests* is a great tool for generating table driven test stubs (<a href="https://github.com/cweill/gotests">gotests</a>)
* *goconvey* is great for seeing live test results in the browser (<a href="http://goconvey.co/">goconvey</a>)
# Proverbs
Even though they are linked above, the Proverbs are included below. They are collection of statements from a talk by Rob Pike and touch on many concepts that every Go developer should understand and adhere to.
1. Don't communicate by sharing memory, share memory by communicating.
2. Concurrency is not parallelism.
3. Channels orchestrate; mutexes serialize.
4. The bigger the interface, the weaker the abstraction.
5. Make the zero value useful.
6. interface{} says nothing.
7. Gofmt's style is no one's favorite, yet gofmt is everyone's favorite.
8. A little copying is better than a little dependency.
9. Syscall must always be guarded with build tags.
10. Cgo must always be guarded with build tags.
11. Cgo is not Go.
12. With the unsafe package there are no guarantees.
13. Clear is better than clever.
14. Reflection is never clear.
15. Errors are values.
16. Don't just check errors, handle them gracefully.
17. Design the architecture, name the components, document the details.
18. Documentation is for users.
19. Don't panic.
# Tooling
The following is a list of common tools that can and should be used to improve code quality:
* goimports
  * Found at <a href="golang.org/x/tools/cmd/goimports">goimports</a>
  * It's a drop-in replacement of *gofmt* with the added feature of organizing your imports for you
  * Use this always to help keep code formatted properly
* go vet
  * Included with core Go
  * Examines Go source code and reports suspicious constructs, such as Printf calls whose arguments do not align with the format string.
* golint
  * Found at <a href="https://github.com/golang/lint">golang/lint</a>
  * Examines your code and provides a list of suggestions on how to improve the style
  * It's not bullet proof, so it's not reliable to enforce automatically in build tools
  * If you find it conflicts with this guide, err on the side of this guide
* errcheck
  * Found at <a href="https://github.com/kisielk/errcheck">errcheck</a>
  * Helps you find unchecked errors in your code.
  * Unchecked errors == highly likely that your code is at risk of panicking
# Packages
## Philosophy
*To be purposeful, packages must provide, not contain*
* Packages must be named with the intent to describe what it provides.
* Packages must not become a dumping ground of disparate concerns.
*To be usable, packages must be designed with the user as their focus*
* Packages must be intuitive and simple to use.
* Packages must respect their impact on resources and performance.
* Packages must protect the user’s application from cascading changes.
* Packages must prevent the need for type assertions to the concrete.
* Packages must reduce, minimize and simplify its code base.
*To be portable, packages must be designed with reusability in mind*
* Packages must aspire for the highest level of portability.
* Packages must reduce setting policies when it’s reasonable and practical.
* Packages must not become a single point of dependency.
Further reading: <a href="https://www.goinggo.net/2017/02/design-philosophy-on-packaging.html">Design Philosophy on Packaging</a>
## Structure
To expand on the above philosophy and highlight common problems: Packages should be focused on a specific domain.
A package's contents should be code addressing a specific domain. A package's name should be descriptive of that specific domain it provides code for. Examples of this are **http** and **io**. If you find that you are struggling to name the package, take another look at what you're trying to provide with the package and be sure that you are keeping it focused on that domain. Do NOT use a name such as **utils** or **helpers** -- these sort of packages should be avoided.
This also means avoiding patterns such as MVC where you might have a **models** or **controllers** package. Examples:
**BAD**
````
    |-- models
        |-- account.go
        |-- user.go
    |-- controllers
        |-- account.go
        |-- user.go
````
**GOOD**
````
    |-- account
        |-- db.go
        |-- rest.go
    |-- user
        |-- db.go
        |-- rest.go
````
As Bill Kennedy suggested, you can think of an individual package as a microservice within a source tree -- each bit of functionality is self-contained.
# Interfaces
## Usage
Do not use an Interface for the sake of using an Interface. Kennedy set forth some guidelines you can use when determining if an interface is needed:
* When users of the API need to provide an implementation detail.
* When API’s have multiple implementations they need to maintain internally.
* When parts of the API that can change have been identified and require decoupling.
Things to watch out for:
* An Interface has many, many functions. It's common for most Go interfaces to have 1 or 2 methods. Most in the standard library only have 1.
* What happens if I remove the interface? Are you actually using the interface, or just the concrete type that implements it?
Good Interface usage:
````go
package main
type Notifier interface {
    Notify() error
}
type Email struct {
    //some attributes
}
func (e *Email) Notify() error{
    //send email
    return nil
}
func (e *Email) SetBody() error{
    //generates plaintext and HTML bodies
     return nil
}
type SMS struct {
    //some attributes
}
func (e *SMS) Notify() error{
    //send sms
     return nil
}
func (e *SMS) Split() error{
    //SMS is too large so it's split into multiple
     return nil
}
````
## Names
From <a href="https://golang.org/doc/effective_go.html#interface-names">Effective Go</a>:
>By convention, one-method interfaces are named by the method name plus an *-er* suffix or similar modification to construct an agent noun: *Reader*, *Writer*, *Formatter*, *CloseNotifier* etc.
>
>There are a number of such names and it's productive to honor them and the function names they capture. *Read*, *Write*, *Close*, *Flush*, *String* and so on have canonical signatures and meanings. To avoid confusion, don't give your method one of those names unless it has the same signature and meaning. Conversely, if your type implements a method with the same meaning as a method on a well-known type, give it the same name and signature; call your string-converter method *String* not *ToString*.
# Types
## Type Usage
When declaring types, focus on behavior rather than state. Guidelines offered by Kennedy:
* Declare types that represent something new or unique.
* Validate that a value of any type is created or used on its own.
* Embed types to reuse existing behaviors you need to satisfy.
* Question types that are an alias or abstraction for an existing type.
* Question types whose sole purpose is to share common state.
## Assertions and Reflection
Type Assertions and reflection have a cost, and it's generally recommended to avoid using them unless absolutely necessary.
## Tags
Struct tags are basically a way of attaching extra metadata to a field that can be accessed during reflection. Common occurences of this are during JSON Marshaling/Unmarshaling and database access. To add your own tags generally requires you to write some code to act on it. If you find yourself already doing reflection, then tags may make life a lot easier.
## Getters and Setters
Getters and Setters as many OOP developers are used to are not needed. In Go you can access the field directly on a concrete type. Getters and Setters **may** make sense when working with Interfaces, however -- it depends on your use-case. As always, focus on what you want the object to *do*, not what it *is*.
# Functions
## Function Usage
Functions should be readable and simple from top to bottom -- Clear is better than clever. Here are some guidelines that should be followed:
* Functions should not be broken out into smaller function unless their cyclomatic complexity exceeds 8
  * This is to increase readability and reduce allocations for functions that don’t need to be functions
* Avoid inlining functions unless there's a solid reason for doing so
* Avoid returning functions unless absolutely necessary
* Avoid functions that return a static value (use a constant or var instead!)
* Use early returns where possible
## Synchronous vs Async
Prefer synchronous functions over asynchronous. It keeps the lifetime of their execution more predictable and makes them easier to test.
If the caller needs more concurrency, they can do so by calling the function from a separate go routine.
## Pointer Receivers
When in doubt, default to using a pointer receiver. Otherwise, CodeReviewComments offers some guidance on when to use them:
* If the receiver is a map, func or chan, don't use a pointer to them. If the receiver is a slice and the method doesn't reslice or reallocate the slice, don't use a pointer to it.
* If the method needs to mutate the receiver, the receiver must be a pointer.
* If the receiver is a struct that contains a sync.Mutex or similar synchronizing field, the receiver must be a pointer to avoid copying.
* If the receiver is a large struct or array, a pointer receiver is more efficient. For example, consider passing all elements/fields as arguments - is it alot? Use a pointer.
* Can function or methods, either concurrently or when called from this method, be mutating the receiver? A value type creates a copy of the receiver when the method is invoked, so outside updates will not be applied to this receiver. If changes must be visible in the original receiver, the receiver must be a pointer.
* If the receiver is a struct, array or slice and any of its elements is a pointer to something that might be mutating, prefer a pointer receiver, as it will make the intention more clear to the reader.
* If the receiver is a small array or struct that is naturally a value type (for instance, something like the time.Time type), with no mutable fields and no pointers, or is just a simple basic type such as int or string, a value receiver makes sense. A value receiver can reduce the amount of garbage that can be generated; if a value is passed to a value method, an on-stack copy can be used instead of allocating on the heap. (The compiler tries to be smart about avoiding this allocation, but it can't always succeed.) Don't choose a value receiver type for this reason without profiling first.
## Defer
Defer allows code to be excuted at the time the function goes out of scope (returns). This is great for control flow and also helps with readability. For example,
````go
package snmp
func listen() error {
    ///do stuff
    c, err := net.ListenUDP("udp4", addr)
    if err != nil {
        return err
    }
    defer c.Close()
    //do stuff
}
````
In the above example, *defer* is called almost immediately after the connection is opened. This allows us to easily see that the connection will close when the function returns. Furthermore, it helps guard against future function edits that might otherwise remove the *c.Close()* by accident.
Common use-cases for using *defer* are closing open connections and releasing locks.
# SQL Database
* Prefer using PreparedStatements and Named parameters while executing SQL queries. This approach has following advantages.
  * Prepared statements reduces parsing time as the preparation on the query is done only once (although the statement is executed multiple times).
  * Bound parameters minimize bandwidth to the server as you need send only the parameters each time, and not the whole query
  * Prepared statements are very useful against SQL injections, because parameter values, which are transmitted later using a different protocol, need not be correctly escaped. If the original statement template is not derived from external input, SQL injection cannot occur.
# Testing
## Coverage
All code should be unit tested before being committed. Always shoot for at least 80% coverage, though some leniency may be shown in some more extreme cases.
## Test Methods
* TODO: discuss possible usage of httpmock for 3rd party http apis
* Use go-sqlmock for RDMS mocks (<a href="https://github.com/DATA-DOG/go-sqlmock">go-sqlmock</a>)
* Use interfaces for testing elsewhere.
## Table Driven Tests
A common approach from testing is to use table driven tests. For example:
````go
func TestFlagParser(t *testing.T) {
    var flagtests = []struct {
    in  string
    out string
    }{
        {"%a", "[%a]"},
        {"%-a", "[%-a]"},
        {"%+a", "[%+a]"},
        {"%#a", "[%#a]"},
        {"% a", "[% a]"},
        {"%0a", "[%0a]"},
        {"%1.2a", "[%1.2a]"},
        {"%-1.2a", "[%-1.2a]"},
        {"%+1.2a", "[%+1.2a]"},
        {"%-+1.2a", "[%+-1.2a]"},
        {"%-+1.2abc", "[%+-1.2a]bc"},
        {"%-1.2abc", "[%-1.2a]bc"},
    }
    var flagprinter flagPrinter
    for _, tt := range flagtests {
        s := Sprintf(tt.in, &flagprinter)
        if s != tt.out {
            t.Errorf("Sprintf(%q, &flagprinter) => %q, want %q", tt.in, s, tt.out)
        }
    }
}
````
## Failures
Make test failure errors useful. Don't just say "it failed" -- include the output that you got and what you expected, at a minimum. A common format is:
````go
    s := Sprintf(tt.in, &flagprinter)
    if s != tt.out {
        t.Errorf("Sprintf(%q, &flagprinter) => %q, want %q", tt.in, s, tt.out)
    }
````
## Useful Tools
* *gotests* (<a href="https://github.com/cweill/gotests">gotests</a>) - Generates table-driven test stubs for your code
* *goconvey* (<a href="http://goconvey.co/">goconvey</a>) - While we currently do not do BDD-style testing, goconvey provides live results of your unit tests in the browser as you write code and tests
# Style Guide Sources
The following are a selection of sources that helped influence this style guide. Feel free to review them.
* <a href="https://github.com/golang/go/wiki/CodeReviewComments">Golang Code Review Comments</a>
* <a href="https://golang.org/doc/effective_go.html">Effective Go</a>
* <a href="https://go-proverbs.github.io/">Go Proverbs</a>
* <a href="https://talks.golang.org/2012/zen.slide#1">Go vs. Zen of Python</a>
* <a href="https://talks.golang.org/2014/readability.slide#1">When in Go, do as Gophers do</a>
* <a href="https://www.youtube.com/watch?v=8D3Vmm1BGoY">Twelve Go Best Practices</a>
