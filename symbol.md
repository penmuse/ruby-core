# SYMBOLS#PROCS

Thank you for accepting this mission, and the mission is nothing else but to add Symbols#procs to our ruby toolchain . However, to achieve our mission, we need first `touch` **symbols** & **Procs** and then with their combined power we will take on the mission. So then, come on brave one, to `:symbols`.

## Symbols

Symbols is one of those awesome nuggets in Ruby you understand intuitively and trips you over when you attempt a comprehensive explanation. Although, luckily that's not our mission (now), we will establish some baseline on which we can complete our mission.

The following statements are handy in using(or not using) symbols:
* A Ruby symbol looks like a colon followed by characters. `:mysymbol`
* A Ruby symbol is a thing that has both a number **integer** and a **string**.
* The value of a Ruby symbol's string part is the name of the symbol, minus the leading colon.
* A Ruby symbol cannot be changed at runtime.
* Neither its string representation nor its integer representation can be changed at runtime.
* Ruby symbols are useful in preventing modification.
* Like most other things in Ruby, a symbol is an object.
* When designing a program, you can usually use a string instead of a symbol.
* Except when you must guarantee that the string isn't modified.
* Symbol objects do not have the rich set of instance methods that String objects do.
* After the first usage of :mysymbol all further useages of `:mysymbol` take no further memory -- they're all the same object.
* Ruby symbols save memory over large numbers of identical literal strings.
* Ruby symbols enhance runtime speed to at least some degree.

let's _digest_ this with some code examples:

## Procs
[Procs](http://ruby-doc.org/core-2.2.0/Proc.html), lambdas & blocks are often used interchangeably in Ruby and this is fine most of the times. We can understand a proc as an `object` that wraps a block of code which can then be stored in a variable or passed to a method for subsequent use. A block of code here implies code in between a `do ... end` or a `{ ... }`.

Proc objects are instances of the proc class and `respond_to` to proc methods such as `:call` (which arguable is the most used method).

Consider the following code snippet:
```ruby
  commander = Proc.new do
    num = rand(1..10)
    puts "Soldier no: #{num}, state your name and your resolve"
  end

  ready = Proc.new { puts "Are you ready for the mission?" }

  commander.call #=> Soldier no: 4, state you name and your resolve
  ready.call #=> Are you ready for the mission
```
Here we created a proc object and stored it in a varible which we then execute using the `:call` method.
> (Note that the solder uses a random number to assign the soldier number as such your output might be different but it would between 1 and 10)

Procs can also take in parameters like methods.
```ruby
  soldier = Proc.new do |name, resolve|
    state = resolve ? "ready" : "not ready"
    puts "My name is #{name}. I'm #{state}"
  end

  soldier.call('John Doe', true) #=> My name is John Doe. I'm ready
  soldier.call('Jane Doe', false) #=> My name is Jane Doe. I'm not ready
```
This can also be done using the {...} notation:

```ruby
  hail = Proc.new {|name| puts "Well done, Brave #{name}! Onward to the mission briefing room."}

  hail.call('John Doe') #=> Well done, Brave John Doe! Onward to the mission briefing room.
```
Note that one of procs attributes is that it's not strict about arguments unlike methods. As such if arguments are not provided, it will not throw errors but rather assign nil for the mission values:
```ruby
  hail.call #=> Well done, Brave ! Onward to the mission briefing room.
```

## Symbol#Proc
