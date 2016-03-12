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

For this mission, we would be leveraging on symbols use for method calls. Say we want to `capitalize` the name of all our soldiers we can do:
```ruby
  'john doe'.capitalize #=> John Doe
```
To use symbol for the method call, we do
```ruby
  'john doe'.send(:capitalize) #=> John Doe
```
Neat! Add this to your armory and onward to procs.

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

The last pick up we need from this stop is converting procs to blocks and vice-verse. This conversion can be made using the **&** sign. Consider the following method which accepts a block and executes it
```ruby
  def accept_proc_regiment(some_proc_arg)
    some_proc_arg.call
  end

  proc = Proc.new { puts 'Soldiers of the Proc regiment are brave' }
  proc = Proc.new { puts 'They always proceed first in battle' }

  accept_proc_regiment proc #=> Soldiers of the Proc regiment are brave
  accept_proc_regiment proc #=> They always proceed first in battle
```

Now let's modify this methods to accept a block instead. To that we affix **&** to the argument definition like so:

```ruby
  def accept_proc_regiment(&some_proc_arg)
    some_proc_arg.call
  end
```
With that we can then execute our method like so

```ruby
  accept_proc_regiment { puts 'Soldiers of the Proc regiment are brave' } #=> Soldiers of the Proc regiment are brave
```
Yes the do..end syntax works also:

```ruby
  accept_proc_regiment do
    puts 'Soldiers of the block regiment are brave also. They guard the rear flanks.'
  end
  #=> Soldiers of the block regiment are brave also. They guard the rear flanks.
```
What do you think will happen when we do the following code from our previous snippet with our modified method?
```ruby
  accept_proc_regiment proc #=> Soldiers of the Proc regiment are brave
  accept_proc_regiment proc #=> They always proceed first in battle
```

So, what exactly is the magic of the **&**? We'll find out together in minute, for now, add this to your armory.

## Symbol#Proc
Now the mission.

Let's construct a class for our soldier object
```ruby
  class Soldier
    def initialize(name, weapon)
      @name = name
      @weapon = weapon
    end

    def name
      @name
    end

    def first_name
      @name.split.first
    end

    def last_name
      @name.split.last
    end

    def weapon
      @weapon
    end

    def salary
      500 * name.length
    end

    def grade_a_weapon
      weapon.length > 5
    end
  end

  soldier = Soldier.new("John Doe", "AK-47")

  # From our symbol armory
  soldier.send(:name) #=> John Doe
  soldier.send(:weapon) #=> AK-47
  soldier.send(:salary) #=> 500
```
_note that using the **(.)** notation also work but for the sake of target we use send_
Next up, we'll create a collection of all soldiers that signed up for the mission

```ruby
  soldiers = []
  soldiers << Soldier.new('Big Mike', "Bazooka")
  soldiers << Soldier.new('Small John', "Pistol")
  soldiers << Soldier.new('Lightening Larry', "Uzi")

  # From our proc armory
  proc = Proc.new { |soldier| puts "Hail #{soldier.name}"}
  soldiers.each(&proc)
  #=> Hail Big Mike
  #=> Hail Small John
  #=> Hail Lightening Larry

```

What if we need a collection of just the name of the soldiers, we could using the map function

```ruby
  soldiers.map{ |soldier|  soldier.send(:name) } #=> ["Big Mike", "Small John", "Lightening Larry"]

  # Using our proc armory
  proc = Proc.new { |soldier| soldier.send(:name) }
  soldiers.map(&proc) #=> ["Big Mike", "Small John", "Lightening Larry"]
```

With symbol#proc, we can simplify this further

```ruby
  soldiers.map(&:name) #=> ["Big Mike", "Small John", "Lightening Larry"]
```

Now you know why need to make those stops we made at symbols and procs as such the question now won't be what each components of the code mean but rather, **How?**

To explain, we need to answer our pending question what exactly is the magic of the **&**?
The & calls `to_proc` on the object, and passes it as a block to the method. As such, if you put & in front of a Proc instance in the argument position, that will be interpreted as a block. If you combine something other than a Proc instance with &, then implicit class casting will try to convert that to a Proc instance using to_proc method defined on that object if there is any. In case of a Symbol instance, like above, to_proc works in this way:

```ruby
  class Symbol
    def to_proc
      Proc.new { |x| x.send(self) }
    end
  end
```
What this does is to call a method named of the symbol (self.to_s) on the passed in argument. So say the symbol name is : and the passed in argument is enemy, the result will be `enemy.send('attack')` which is equivalent to `enemy.attack`

So for each of the soldiers in the array, the map method passes it to the proc as x. Therefore, the argument to map is interpreted as
```ruby
  soldiers.map { |x| x.name } # x in this case is each soldier in the array
```
Note that this `Symbol#proc` conversion is valid for all methods that accept a proc.
With that knowledge, to return the soldiers weapon, we can easily do

```ruby
  soldiers.map(&:weapon)
  soldiers.collect(&:salary) #=> [4000, 5000, 8000]

  #to get soldiers with grade_a_weapon
  soldiers.select(&:grade_a_weapon) #=> [#<Context::Soldier:0x000000017065b0 @name="Big Mike", @weapon="Bazooka">, #<Context::Soldier:0x00000001706538 @name="Small John", @weapon="Pistol">]
  soldiers.select(&:grade_a_weapon).map(&:weapon) #=> ["Bazooka", "Pistol"]
  # How will get a list of all grade a weapon
```

Now let's towards more ambitious adventure. We can override the to_proc method in the Symbol class to achieve custom result. For instance say we want to always append the team captain's name to all to_proc requests;

```ruby
  class Symbol
    def to_proc
      Proc.new { |x| "#{x.send(self)} (signed John Doe Commander)" }
    end
  end

  # The we have
  soldiers.map(&:name) #=> ["Big Mike (signed John Doe Commander)", "Small John (signed John Doe Commander)", "Lightening Larry (signed John Doe Commander)"]
```
Of course this will override every other symbol to conversion

```ruby
  [1, 2, 3, 4, 5].map(&:to_f) #=> ["1.0 (signed John Doe Commander)", "2.0 (signed John Doe Commander)", "3.0 (signed John Doe Commander)", "4.0 (signed John Doe Commander)", "5.0 (signed John Doe Commander)"]
```

What if we don't want to depend on the Symbol to_proc conversion, we'll we can define our own. Say we want to still append the team captain's name to our conversion but we don't want it to override other conversion we might want to do, we can create a custom class with a to_proc method defined therein and then pass that as the arg to our map method method.

```ruby
  class CustomToProc
    def self.to_proc
      Proc.new { |x| "#{x.name} (signed John Doe Commander)" }
    end
  end

  #Then we pass our custom to the map method
  soldiers.map(&CustomToProc) #=> ["Big Mike (signed John Doe Commander)", "Small John (signed John Doe Commander)", "Lightening Larry (signed John Doe Commander)"]

  # And our default symbol#to_proc remains unchanged
  [1, 2, 3, 4, 5].map(&:to) #=> [1.0, 2.0, 3.0, 4.0, 5.0]
```

One common gotchas to watch out for is that symbol#to_proc does not take an extra argument
```ruby
  # say you want to names of all the grade_a_weapon
  soldiers.select(&:grade_a_weapon.name) #=> undefined method `name' for :grade_a_weapon:Symbol

  # or
  soldiers.select(&:grade_a_weapon, name) #=> syntax error, unexpected ',', expecting ')'
```

## Conclusion
Symbol to proc are really useful and often come in handy in the drive for cleaner concise codes. One rule of thumb for when to use symbol to proc is whenever you find yourself having blocks whose sole function is call a single method on the passed arg i.e
```ruby
  something {|i| i.foo }

  # should be replaced with
  something(&:foo)
```
examples
```ruby
  (1..100).reject{|num| num.odd? }
  (1..3).map {|num| num.to_s}
  (1..100).inject { |a, b| a + b}
  words.map { |s| s.length }
  %w(john paul ringo george).map { |p| p.capitalize }
  collection.select { |x| x.valid }
  soldiers.map{|x| x.last_name }
  soldiers.map{|x| x.first_name }
```
Congrats you've successfully completed the mission. Show your mastery of symbol to_proc by rewriting the examples above; paste your implementation in the comment section below.
