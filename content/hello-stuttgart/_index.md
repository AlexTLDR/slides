+++
title = "Hello Stuttgart: Welcome to the Stuttgart Gophers Community"
outputs = ["Reveal"]
[logo]
src = "/img/shared/stuttgart_gophers.png"
[reveal_hugo]
margin = 0.2
+++

{{< slide background-color="#008080" >}}

<div class="title-slide" style="margin-top: -50px;">
<h1>Hello Stuttgart</h1>
<h2>Stuttgart Gophers · February 2026 Meetup</h2>
<p>stuttgart-gophers.de</p>

<div style="margin-top: 60px; display: flex; justify-content: center; align-items: center; gap: 80px;">
  <div style="text-align: center;">
    <img src="/img/shared/stuttgart-gophers-qr-code.png" alt="Stuttgart Gophers" style="max-width: 300px; width: 100%;">
    <p style="margin-top: 10px; font-size: 0.8em;">Stuttgart Gophers</p>
  </div>
  <div style="text-align: center;">
    <img src="/img/shared/alex-qr-code.png" alt="AlexTLDR Website" style="max-width: 300px; width: 100%;">
    <p style="margin-top: 10px; font-size: 0.8em;">AlexTLDR</p>
  </div>
</div>

<p style="margin-top: 40px; font-size: 0.8em; opacity: 0.8;">Presented by: <a href="https://alextldr.dev" target="_blank" style="color: white; text-decoration: underline;">AlexTLDR</a></p>
</div>

---

# Hello Stuttgart

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Nice to meet you all!</li>
    <li class="fragment">Special thanks to Thoughtworks for hosting us!</li>
    <li class="fragment">Today we will be diving into Go.</li>
    <li class="fragment">This session is based on the book "Learn Go with Pocket-Sized Projects" by Aliénor Latour, 
        Donia Chaiehloudj, and Pascal Bertrand.
        <div class="fragment">
            <img src="/img/hello-stuttgart/learn-go-with-poketsize.png" alt="Learn Go with Pocket-Sized Projects" style="max-width: 500px; width: 100%;">
        </div>
    </li>
  </ul>
</div>

---

# This session will cover

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Writing to the standard output</li>
    <li class="fragment">Testing writing to the standard output</li>
    <li class="fragment">Writing table-driven tests</li>
    <li class="fragment">Using a hash table to hold key-value pairs</li>
    <li class="fragment">Using flags to read command-line parameters</li>
  </ul>
</div>

---

# Going beyond Hello World

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Go code runs inside modules</li>
    <li class="fragment">In order to create a module, we use the `go mod init` command
      <div class="fragment">
        {{< highlight bash >}}
go mod init example.com/your-repository
        {{< /highlight >}}
or
        {{< highlight bash >}}
go mod init learngo-pockets/hello
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">Let's get our hands dirty!
      <div class="fragment">
                    {{< highlight go >}}
            package main
            
            import "fmt"
            
            func main() {
            fmt.Println("Hello world")
            }       
                    {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Going beyond Hello World

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Let's run the code:
      <div class="fragment">
        {{< highlight bash >}}
go run main.go
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">Expected result:
      <div class="fragment">
        {{< highlight bash >}}
$> go run main.go
Hello world
        {{< /highlight >}}
      </div>
    </li>

  </ul>
</div>

---

# Understanding the code

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Every Go file begins with the name of its package, in this case, main. Packages are Go’s way of
     organizing code, similar to modules or libraries in other languages.
    </li>
    <li class="fragment">The main package is a bit particular, for two reasons. First, it doesn’t respect Go’s convention
    of naming the package after its directory (or the other way around). Second, this is how the compiler knows the
    special function called main() will be found here. The main() function is what will be executed when the program is run.
    </li>
    <li class="fragment">After the package’s name, next comes the list of required imports this file will use.
    Imported packages are composed of standard library packages and third-party libraries:
          <div class="fragment">
                    {{< highlight go >}}
            import "fmt"
                    {{< /highlight >}}
          </div>
    </li>
  </ul>
</div>

---

# Understanding the code

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Most Go programs rely on external dependencies. A single Go file, without the help of imported
    packages, can only handle a limited set of tools.
    <br>
    <br>
    To use features in such external dependencies, we need to import the package where they reside via the import keyword,
    which provides visibility of the functions and variables in a specific package somewhere else.
    </li>
    <li class="fragment">Finally, we have the main() function itself. It doesn’t take any argument and doesn’t return
    anything. Go is a simple language:
          <div class="fragment">
                    {{< highlight go >}}
func main() {
    fmt.Println("Hello world")
}
                    {{< /highlight >}}
          </div>
    </li>
  </ul>
</div>

---

# A capital question

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">You might wonder why Println starts with an uppercase letter. The whole story about scope and visibility is explained below</li>
    <li class="fragment">Any symbol starting with a capital letter is exposed to external users of the package</li>
    <li class="fragment">Anything else isn’t accessible from outside the package. Common examples of unexposed names
        include those starting with a lowercase letter and those starting with an underscore
    </li>
    <li class="fragment">This applies to variables, constants, functions, and types</li>
    <li class="fragment">The Println function starts with a capital letter so that we can use it from outside the package</li>
  </ul>
</div>

---

# Testing: Example vs. test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">While Go functions usually return values, very few write specifically to the standard output. 
    The test strategy that we’ll implement here is only necessary when checking the standard output, which means it 
    won’t be the default approach for the rest of the code. However, because this is our first function and we want to 
    test it, this is the easy way. We’ll see more about test functions shortly.</li>
    <li class="fragment">First, we need a test file. We’ll name the file main_internal_test.go, for the following reasons:
      <ul>
        <li class="fragment">main, because the file we test is named main.go.</li>
        <li class="fragment">internal, because we want to access unexposed methods, a convention that we choose to follow in this book.</li>
        <li class="fragment">test, because this is a test file. When it comes to building or executing the program, *_test.go files are ignored by the compiler. Only when running tests will *_test.go files be considered.</li>
      </ul>
    </li>
    <li class="fragment">Unlike "normal" tests that usually check return values, this Example test is used specifically
    to check "standard output" (what is printed to the screen).</li>
  </ul>
</div>

---

# Testing: Example vs. test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">The test file should look like this:
      <div class="fragment">
        {{< highlight go >}}
package main

import "testing"

func ExampleHello() {
    Hello()
    // Output: Hello world
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">To run the test:
      <div class="fragment">
        {{< highlight bash >}}
go test
        {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Functions

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">We want some variations, some modularity</li>
    <li class="fragment">The main function does two distinct things: (1) defines a specific message and (2) prints it.
    We’ve cobbled everything on a single line in the previous code, but that doesn’t leave any space for adaptations. </li>
    <li class="fragment">Because we aim to enrich the message, we need some flexibility here.
    We’ll begin by extracting the message generation into a dedicated greet function. This function returns a string that
    we can keep in a variable we call greeting</li>
  </ul>
</div>

---

# Functions

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">
      <div class="fragment">
        {{< highlight go >}}
package main

import "fmt"

func main() {
    greeting := greet()
        fmt.Println(greeting)
}

// greet returns a greeting to the world.
func greet() string {
    // return a simple greeting message
    return "Hello world"
}
        {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---