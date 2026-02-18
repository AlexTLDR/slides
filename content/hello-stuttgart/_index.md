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
    <li class="fragment">The module name is also the name of the executable file that is created when we run the `go build` command</li>
  </ul>
</div>

# Going beyond Hello World
