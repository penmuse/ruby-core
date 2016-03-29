# The Mystery behind &:symbol(Symbol#to_proc)

## Mission Briefing
`&:symbol` _a.k.a_ **Symbol#to_proc** is one of those awesome nuggets of the Ruby programming that comes in really handy in writing clean, beautify code. Consider the following code snippet that adds the sum of numbers from **1** to **10**:
```ruby
sum = (1..10).to_a.inject do |sum, num|
        sum + num
     end
```
We can easily write this using **Symbol#to_proc** as follows:

```ruby
sum = (1..10).to_a.inject(&:+)
```

This is one of the varying implementations of **Symbol#to_proc** that you might come across and wonder, **what mysterious magic is going on behind the scene with the cryptic looking code? Is this another one of Ruby's [_dark magic_](http://goodheads.io/tag/magic/)?**

Well, let's find out.

#### Caveat
Our mission to demystify the _magic_ of **Symbol#to_proc** requires an understanding of the **Symbol** & **Proc** concepts in Ruby. If you have difficulties with any of these concepts, do not worry, I made arrangements for you. [The Ruby_Newbie Guide to Symbols](http://www.troubleshooters.com/codecorn/ruby/symbols.htm), has exactly what you will need for symbols and [Ruby Explained: Blocks, Procs, and Lambdas, aka "Closures"](http://www.eriktrautman.com/posts/ruby-explained-blocks-procs-and-lambdas-aka-closures) has got your back on procs. Once you are well equipped, onward to the mission.

## Meet the Calvary
I've taken the liberty to prepare a class of brave soldiers to aid us on our quest. Note down their abilities; we'll be adding some more later on.

```ruby
  class Soldier
    def initialize(name, weapon)
      @name = name
      @weapon = weapon
    end

    def name
      @name
    end

    def weapon
      @weapon
    end

    def salary
      500 * name.length
    end
  end
```
A quick test of our soldier class:
```ruby
  soldier = Soldier.new("John Doe", "AK-47")
  soldier.name #=> John Doe
  soldier.weapon #=> AK-47
  soldier.salary #=> 4000

  soldiers = []
  soldiers << Soldier.new('Big Mike', "Bazooka")
  soldiers << Soldier.new('Small John', "Pistol")
  soldiers << Soldier.new('Lightening Larry', "Uzi")

  soldiers.each { |s| puts "Hail #{s.name}" }
  #=> Hail Big Mike
  #=> Hail Small John
  #=> Hail Lightening Larry

```

## Proc as arguments
You would need to recall at this point that a method which accepts a **block** can be passed a **Proc** instead. This is done by prefixing the proc with a **&** when passing it to the method. In our example above, we could have written:
```ruby
  proc = Proc.new { |s| puts "Hail #{s.name}" }
  soldiers.each(&proc)
```
Basically, if you put **&** in front of a **Proc** instance in the argument position, it will be interpreted as a block. the **&** takes our proc and converts it to a block as required by the method.

If you are comfortable with this, then your understanding of **&:symbol** is almost complete.

## It's all about to_proc 
What then happens if we pass a _non proc_ object to **&**?

If you combine something other than a **Proc** instance with **&**, then implicit class casting will try to convert that to a **Proc** instance using `to_proc` method defined on that object (if any).

Let's test this out using a custom class
```ruby
  class CustomToProc
    def self.to_proc
      Proc.new { |s| "I'm a custom to_proc" }
    end
  end

  soldiers.map(&CustomToProc)
  #=> I'm a custom to_proc
  #=> I'm a custom to_proc
  #=> I'm a custom to_proc

```
Interesting!
How does this then explain **&:symbol**

## The Symbol to_proc implementation
By now, you would be having the right guess as to how **&:symbol** works. Let's try out our guess
```ruby
# Say we want a collection of just the names of our brave soldiers
# Using proc we could do
  proc = Proc.new { |soldier| soldier.send(:name) }
  soldiers.map(&proc) #=> ["Big Mike", "Small John", "Lightening Larry"]

# A simpler implementation would then be
  soldiers.map(&:name) #=> ["Big Mike", "Small John", "Lightening Larry"]
```
You are right in guessing that the **Symbol** class defines a [`to_proc` method](http://ruby-doc.org/core-2.2.0/Symbol.html#method-i-to_proc) which is called on any `symbol` object passed to **&**. Like our `CustomToProc` earlier, the implementation for `Symbol` is like this:

```ruby
  class Symbol
    def to_proc
      Proc.new { |x| x.send(self) }
    end
  end
```
What the `to_proc` implementation in the **Symbol** class does is to call a method with the same name as the `symbol` (i.e. `self`) on the passed in object. So, say the symbol name is `:attack` and the passed in argument is `enemy`, the result will be `enemy.send('attack')` which is equivalent to `enemy.attack`. In our snippet above, for each soldier in the array, the map method passes it to the proc as `x`. Therefore, the argument to the `#map` method is interpreted as
```ruby
  soldiers.map { |x| x.name } # where x in this case is each soldier in the array
```
Note that this `Symbol#proc` conversion is valid for all methods that accept a proc (or a block).
> I'll be surprised if you are not curious of the #send method used above. Here, [check this out](http://goodheads.io/2016/03/12/ruby-on-steroids-the-magic-of-metaprogramming-method-spells/).

## Debriefing
Let' validate our findings;

```ruby
  # To return the soldiers weapon, we can easily do
  soldiers.map(&:weapon) #=> ["Bazooka", "Pistol", "Uzi"]

  # To return the soldiers salaries
  soldiers.collect(&:salary) #=> [4000, 5000, 8000]
```
Let's add some more methods to our **Soldier** class to practice **&:symbol**

```ruby
def first_name
  @name.split.first
end

def last_name
  @name.split.last
end

def grade_a_weapon?
  weapon.length > 5
end
```
Some more examples

```ruby
# To get soldiers with grade_a_weapon
soldiers.select(&:grade_a_weapon?) #=> [#<Context::Soldier:0x000000017065b0 @name="Big Mike", @weapon="Bazooka">, #<Context::Soldier:0x00000001706538 @name="Small John", @weapon="Pistol">]

# To get grade_a_weapon weapons
soldiers.select(&:grade_a_weapon?).map(&:weapon) #=> ["Bazooka", "Pistol"]
```

*Here's one for you to try out: __Retrieve the last_name of soldiers with grade_a_weapon.__ Share your implementation in the comment section.*

One common gotchas to watch out for is that symbol#to_proc does not take an extra argument
```ruby
  # Say you want to retrieve the names of all the grade_a_weapon?
  soldiers.select(&:grade_a_weapon?.name) #=> undefined method `name' for :grade_a_weapon?:Symbol

  # OR
  soldiers.select(&:grade_a_weapon?, name) #=> syntax error, unexpected ',', expecting ')'

```

## Conclusion
Symbol to proc are really useful and often come in handy in the drive for cleaner concise codes. One rule of thumb for when to use **&:symbol** is whenever you find yourself having blocks whose sole function is to call a single method on the passed in argument i.e
```ruby
  something { |i| i.foo }

  # should be replaced with
  something(&:foo)
```
For example, all the following can be replaced with **&:symbol**
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
Show your mastery of `symbol#proc` by rewriting the examples above using what you just learned and paste your implementation in the comment section below.

**Congrats you've successfully completed your mission. **
