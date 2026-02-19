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
    // Output:
    // Hello world
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

# Functions

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">The new function is called greet because it will return the greeting message.
    For now, it takes no parameters and simply returns the message in the form of a string</li>
    <li class="fragment">In the main function, we call the new greet function and store its output in the greeting string
    variable, which we print:
        <div class="fragment">
        {{< highlight go >}}
func main() {
    greeting := greet()
        fmt.Println(greeting)
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">Does the test still pass? This is our first refactoring</li>
  </ul>
</div>

---

# Testing with the testing package

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Because we’ll want to enrich the greet function, we should be covering it with dedicated tests</li>
    <li class="fragment">As part of Go’s standard library packages, the package <b>testing</b> is available for use </li>
    <li class="fragment">We’ve already seen the Example&lt;FunctionName&gt;() syntax, which is used for documentation and for
    testing standard output. Let’s venture into a new set of test functions: those with the signature:
      <div style="background-color: #282a36; padding: 15px; border-radius: 8px; border-left: 4px solid #bd93f9; margin: 15px 0; text-align: center;">
        <code style="color: #f8f8f2; font-size: 1.2em; font-weight: bold;">Test&lt;FunctionName&gt;(t *testing.T)</code>
      </div>
    There’s an important difference here with the previous category: these functions accept a parameter—a pointer to a testing.T structure.
    The reasons for using a pointer here are beyond the scope of this presentation</li>
  </ul>
</div>

---

# Testing with the testing package

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">A TestXxx function runs one or more tests on a function, as defined by the developer.
    We’ll start with one, and then grow from there. A test consists of calling the function and checking its returned
    value or the state of some variable against a wanted value or state. Should they match, the test is considered passing;
    otherwise, it’s considered failing. Every test has four main steps:
    </li>
    <li class="fragment">Preparation phase—This is where we set up everything we need to run the test—input values,
    expected outputs, environment variables, global variables, network connections, and so on</li>
    <li class="fragment">Execution phase—This is where we call the tested function. This step is usually a single line</li>
    <li class="fragment">Decision phase—This is where we check that the output we got corresponds to the output we want.
    This might include several comparisons, evaluations, and sometimes some processing, as well as the test failing or passing.</li>
    <li class="fragment">Teardown phase—This is where we kindly clean back to whatever the state was prior to the test’s execution.
    This step is made extremely simple thanks to Go’s defer keyword: anything that was altered or created during preparation
    should be fixed or destroyed here.</li>
  </ul>
</div>

---

# Testing with the testing package

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Our TestGreet function will be written in the same main_internal_test.go file as earlier,
    mostly because the tested function, greet, is also in the same main.go file.</li>
    <li class="fragment">In Go, we like to use want for the expected value and got for the actual one:
        <div class="fragment">
        {{< highlight go >}}
package main

import "testing"

func TestGreet(t *testing.T) {
    want := "Hello world"

    got := greet()

    if got != want {
        // mark this test as failed
        t.Errorf("expected: %q, got: %q", want, got)
    }
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">Does the test still pass? This is our first refactoring</li>
  </ul>
</div>

---

# Explaining the test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
want := "Hello world"

got := greet()

if got != want {
    // mark this test as failed
    t.Errorf("expected: %q, got: %q", want, got)
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">The decision phase here isn’t too tricky. We need to compare two strings, and we’ll accept no alteration,
    so the '!=' comparison operator works fine for us here. We’ll soon face cases where comparing two strings isn’t enough,
    but let’s not skip steps, as we still have a final line here that needs more explanation</li>
  </ul>
</div>

---

# Explaining the test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
t.Errorf("expected: %q, got: %q", want, got)
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">So far, the need for the t parameter hasn’t been obvious. As mentioned earlier,
    a test needs to be either PASSing or FAILing.
    Calling t.Errorf is one way of letting the go test tool know that this test was unsuccessful.
    Errorf has a similar signature as Printf; see appendix B for more about formatting strings.
    Once again, you can run the tests with the same go test command as earlier.</li>
    <li class="fragment">As a challenge, try to make the test fail by changing the expected value (want).</li>
  </ul>
</div>

---

# Are you a polyglot?

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Let's first add support for a new language</li>
    <li class="fragment">Handle the user’s language request</li>
    <li class="fragment">Adapt the tests and ensure we didn’t break the previous behavior</li>
  </ul>
</div>

---

<div class="responsive-container">
  <ul class="responsive-list">
        <div class="fragment">
        {{< highlight go >}}
package main

import "fmt"

func main() {
    greeting := greet("en")
    fmt.Println(greeting)
}

// language represents the language's code
type language string

// greet says hello to the world in the specified language
func greet(l language) string {
    switch l {
    case "en":
        return "Hello world"
    case "fr":
        return "Bonjour le monde"
    default:
        return ""
    }
}
        {{< /highlight >}}
      </div>
  </ul>
</div>

---

# Explaining the code

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">We need a language parameter for our greet function, and for simplicity, we will use a string</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
type language string
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">Now that we have an explicit type, we can pass it as a parameter to the greet function. The new signature becomes</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
func greet(l language) string
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">To call it, we changed the first line of our main function:</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
greeting := greet("en")
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">We need a language parameter for our greet function, and for simplicity, we will use a string</li>
  </ul>
</div>

---

# Explaining the code

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">For the first iteration, we can add a switch on the language and return the corresponding greeting</li>
    <li class="fragment">The default value for the moment is just an empty string</li>
    <li class="fragment">Gophers consider that switch is clearer when dealing with most types—the exceptions being error, pointers, and bool</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
switch l {
    case "en":
        return "Hello world"
    case "fr":
        return "Bonjour le monde"
    default:
        return ""
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">Go automatically breaks at the end of every case to prevent errors. Since our code uses return anyway,
    this doesn't affect us here, but it's a handy feature to remember</li>
  </ul>
</div>

---

# Let's adapt the tests

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Previously, the greet function accepted no parameter. It now takes one, which means we broke the
    contract we had with the users of our code. Well, for now, the only user is a test, so we can change it.
    We now want to test the greet function with various inputs.</li>
    <li class="fragment">We’ll make a call to the greet function by passing the desired input language and storing the output in a variable,
    so we can verify it. The preparation phase now contains two variables: the desired language and the expected greeting message.</li>
    <li class="fragment">Let’s use a new convention of the testing package: when testing a function with two (or more) different scenarios,
    we can write several functions, Test<FunctionName>_{ScenarioName}. The full test file now looks like the following listing.</li>
  </ul>
</div>

---

<div class="responsive-container">
  <div class="fragment">
    <div style="max-height: 60vh; overflow-y: auto; overflow-x: hidden; border-radius: 8px;">
      {{< highlight go >}}
package main

import "testing"

func ExampleMain() {
    ...
}

func TestGreet_English(t *testing.T) {
    lang := language("en")
    want := "Hello world"

    got := greet(lang)

    if got != want {
        // mark this test as failed
        t.Errorf("expected: %q, got: %q", want, got)
    }
}

func TestGreet_French(t *testing.T) {
    lang := language("fr")
    want := "Bonjour le monde"

    got := greet(lang)

    if got != want {
        // mark this test as failed
        t.Errorf("expected: %q, got: %q", want, got)
    }
}

func TestGreet_Akkadian(t *testing.T) {
    // Akkadian is not implemented yet!
    lang := language("akk")
    want := ""

    got := greet(lang)

    if got != want {
        // mark this test as failed
        t.Errorf("expected: %q, got: %q", want, got)
    }
}
      {{< /highlight >}}
    </div>
  </div>
</div>

---

# Let's adapt the tests

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">As you can see, the TestGreet_English function is in charge of testing the English greeting,
    while the TestGreet_French function tests the French message. While this approach is interesting and worth remembering,
    you probably noticed that, in our case, there’s no real change between the English and the French scenarios.
    Only the preparation step differs—only slightly. The next section will improve on this. To run the tests, simply run your new favorite go test command. </li>
    <li class="fragment">As you’ve noticed, we’ve added another function to test a language unknown to the program.
    Testing isn’t always about making sure the “good” inputs provide “good” results. Making sure the safety nets are in
    place is almost more valuable than making sure the code works as intended.</li>
    <li class="fragment">Next we will support more languages with a phrasebook, introducing a new type called map</li>
  </ul>
</div>

---

# Introducing the Go map hash table

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
// phrasebook holds greeting for each supported language
var phrasebook = map[language]string{
    "el": "Χαίρετε Κόσμε",     // Greek
    "en": "Hello world",       // English
    "fr": "Bonjour le monde",  // French
    "he": "שלום עולם",         // Hebrew
    "ur": "ہیلو دنیا",         // Urdu
    "vi": "Xin chào Thế Giới", // Vietnamese
}

// greet says hello to the world in various languages
func greet(l language) string {
    greeting, ok := phrasebook[l]
    if !ok {
        return fmt.Sprintf("unsupported language: %q", l)
    }

    return greeting
}
        {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Introducing the Go map hash table

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
// greet says hello to the world in various languages
func greet(l language) string {
    greeting, ok := phrasebook[l]
    if !ok {
        return fmt.Sprintf("unsupported language: %q", l)
    }

    return greeting
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">Accessing an item in a Go map returns two pieces of valuable information: a value—in our case,
    the message associated with the key language l—and a Boolean (ok per convention) that tells us whether the key was found</li>
    <li class="fragment">It’s necessary to check the second return value of the access to the map. If the language were unsupported,
    we’d receive the zero value of a string, which is the empty string, with no knowledge of whether the map had an entry for our language</li>
    <li class="fragment"><b>Note that in production-ready code, we would be returning an error because an empty string doesn’t carry any meaning.
    We are just returning a string for simplicity</b></li>
  </ul>
</div>

---

# An extra on Multiple return values

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Gophers usually see many occurrences of multiple value assignment, mostly in four common cases:
      <ul>
        <li class="fragment">When we read the value associated with a key from a map, we also receive whether that key was found in the map, as we did in this piece of code</li>
        <li class="fragment">When we use the range keyword, which allows us to iterate through all the key-value pairs in a map or all the index-value elements of a slice or array (an example appears in the next version of the test file)</li>
        <li class="fragment">When we read from a channel with the &lt;- operator, which returns a value and whether the channel is closed</li>
        <li class="fragment">When we retrieve multiple values returned by a single function, which is the most frequent case, mostly due to Go's handling of errors</li>
      </ul>
    </li>
  </ul>
</div>

---

# Writing a table-driven test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Our previous tests were linear—they tested every language in a sequential way. Taking a step back,
    we realize each test runs the same sequence: take an input language, call the greet function, and check the greeting
    to see if that language is the expected one. This can be summed up in the following snippet of code that was executed
    for languages "en", "fr", or "akk" in our previous example</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
got := greet(language(lang))
if got != want {
    t.Errorf("expected: %q, got: %q", want, got)
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">There’s no point in duplicating this piece of code every time we want to check that we’re properly supporting a new language.
    Isn’t the test always going to be the same? Do we really need to add an extra 10 lines to our test file if only 2 of these lines change?
    This isn’t sustainable. That was our motivation to use maps in the body of the greet function, and this is also our motivation to use maps in our tests!
    We can make use of table-driven tests to enhance the reusability and clarity of our test file, and get the nice side effect of shrinking it a lot!</li>
  </ul>
</div>

---

<div class="responsive-container">
  <div class="fragment">
    <div style="max-height: 60vh; overflow-y: auto; overflow-x: hidden; border-radius: 8px;">
      {{< highlight go >}}
func TestGreet(t *testing.T) {
    type testCase struct {
        lang language
        want string
    }

    var tests = map[string]testCase{
        "English": {
            lang: "en",
            want: "Hello world",
        },
        "French": {
            lang: "fr",
            want: "Bonjour le monde",
        },
        "Akkadian, not supported": {
            lang: "akk",
            want: `unsupported language: "akk"`,
        },
        "Greek": {
            lang: "el",
            want: "Χαίρετε Κόσμε",
        },
        ...
        "Empty": {
            lang: "",
            want: `unsupported language: ""`,
        },
    }

    // range over all the scenarios
    for name, tc := range tests {
        t.Run(name, func(t *testing.T) {
            got := greet(tc.lang)

            if got != tc.want {
                t.Errorf("expected: %q, got: %q", tc.want, got)
            }
        })
    }
}
      {{< /highlight >}}
    </div>
  </div>
</div>

---

# Writing a table-driven test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">As we’ve seen previously, every test we want to run needs two values: the language of the desired message
    and the expected greeting message that will be returned by the greet function. For this, we introduce a new structure
    that contains the input language, and the expected greeting</li>
    <li class="fragment">Structures are Go’s way of aggregating data types together in a meaningful entity. In our case,
    because the structure represents a test case, we’ll name it testCase. Our structure needs only to be accessible in the
    TestGreet function (and nowhere else), so it is defined within the TestGreet function</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
type testCase struct {
    lang language
    want string
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">This will make writing a test over a pair of "language, greeting" even simpler.</li>
  </ul>
</div>

---

# Writing a table-driven test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Now that we can easily write one test case, let’s see how to write a lot of them</li>
    <li class="fragment">In Go, the common way of writing a list of test cases is to use a map structure that will refer to each test case with a specific description key</li>
    <li class="fragment">The description should be explicit about what this case tests</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
var tests = map[string]testCase{
    "English": {
        lang: "en",
        want: "Hello world",
    },
    "French": {
        lang: "fr",
        want: "Bonjour le monde",
    },
}
        {{< /highlight >}}
      </div>
    </li>
    <li class="fragment">This will make writing a test over a pair of language and greeting even simpler.</li>
  </ul>
</div>

---

# Writing a table-driven test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">To test these scenarios, we can iterate over the tests map and run each test case sequentially</li>
    <li class="fragment">This for + range syntax returns the key and the value of each element of the map</li>
    <li class="fragment">We then pass the name as the first parameter to Run, a method from the testing package that makes
    tests so much easier to use: if a test case fails, the tool will give you its name so that you can find it and fix it</li>
    <li class="fragment">To test these scenarios, we can iterate over the tests map and run each test case sequentially</li>
    <li class="fragment">Most code editors also let you run one single test case if you use this syntax</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
 for name, tc := range tests {
 t.Run(name, func(t *testing.T) {
     got := greet(tc.language)
     if got != tc.want {
         t.Errorf("expected: %q, got: %q", tc.want, got)
     }
  })
}
        {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Writing a table-driven test

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Because the call to the greet function is the same regardless of the input language,
    creating a new test case only has us adding an entry in the tests map</li>
    <li class="fragment">This can be seen in the following listing</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
 var tests = map[string]testCase{
   "English": {...},
   "French": {...},
   "Akkadian, not supported": {
       lang: "akk",
       want: `unsupported language: "akk"`,
   },
}
        {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Using the flag package to read the user’s language

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Right now, changing the language requires changing the code. To fix this, we will let the user
    pass their language choice directly through the command line when they run the program</li>
    <li class="fragment">Go has two ways to read command-line arguments: the os package (manual, tedious, and requires custom parsing)
    and the flag package (automatic, handles formats, and converts data types for you). We will use flag</li>
    <li class="fragment">The first thing we need to do, when it comes to exposing a parameter on our command-line executable,
    is to give it a nice, short name. Here, we’ll offer the user a choice of language, which makes lang a fairly obvious choice</li>
  </ul>
</div>

---

<div class="responsive-container">
  <div class="fragment">
    <div style="max-height: 60vh; overflow-y: auto; overflow-x: hidden; border-radius: 8px;">
      {{< highlight go >}}
package main

import (
    "flag"
    "fmt"
)

func main() {
    var lang string
	flag.StringVar(&lang,
        "lang",
        "en",
        "The required language, e.g. en, ur...")
    flag.Parse()

    greeting := greet(language(lang))
    fmt.Println(greeting)
}

// language represents a language
type language string

// phrasebook holds greeting for each supported language
var phrasebook = map[language]string{
    ...
}

// greet says hello to the world
func greet(l language) string {
    ...
}
      {{< /highlight >}}
    </div>
  </div>
</div>

---

# Test the command-line interface

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">We’ve now completed the code, and it’s time to run some end-user testing</li>
    <li class="fragment">For this, we’ll simulate calls from the command line</li>
    <li class="fragment">We can pass the parameter on the command line with go run main.go -lang=en</li>
    <li class="fragment">
        <div class="fragment">
        {{< highlight go >}}
❯ go run main.go -lang=el
Χαίρετε Κόσμε
        {{< /highlight >}}
      </div>
    </li>
  </ul>
</div>

---

# Q & A

<div class="responsive-container">
  <ul class="responsive-list">
    <li class="fragment">Link to the book</li>
    <li class="fragment">https://www.manning.com/books/learn-go-with-pocket-sized-projectse</li>
    <li class="fragment">
      <img src="/img/hello-stuttgart/book-qr-code.png" alt="Book QR Code" style="max-width: 400px; width: 100%; margin-top: 20px;">
    </li>
  </ul>
</div>

