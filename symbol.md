# SYMBOLS#PROCS

Thank you for accepting this mission, and the mission is nothing else but to add Symbols#procs to our Ruby toolchain . However, to achieve our mission, we need to have expertise of  **symbols** & **Procs** and then with their combined power we will take on the mission.

If you have some difficulties with any of these concepts, do not worry, I made arrangements for you. [The Ruby_Newbie Guide to Symbols](http://www.troubleshooters.com/codecorn/ruby/symbols.htm), has exactly what you will need for symbols and [Ruby Explained: Blocks, Procs, and Lambdas, aka "Closures"](http://www.eriktrautman.com/posts/ruby-explained-blocks-procs-and-lambdas-aka-closures). Once you are well equipped, onward to the mission.

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

    def grade_a_weapon?
      weapon.length > 5
    end
  end

  soldier = Soldier.new("John Doe", "AK-47")

  # From our symbol armory
  soldier.send(:name) #=> John Doe
  soldier.send(:weapon) #=> AK-47
  soldier.send(:salary) #=> 4000
```
_note that using the **(.)** notation also works but for the sake of our mission we use send_
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

What if we need a collection of just the names of the soldiers, we could using the map function do

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

Now you know why we needed to make those stops we made at symbols and procs as such the question now won't be what each components of the code mean but rather, **How they work together?**

The **&** calls `to_proc` on the object, and passes it as a block to the method. As such, if you put **&** in front of a Proc instance in the argument position, it will be interpreted as a block. If you combine something other than a Proc instance with **&**, then implicit class casting will try to convert that to a Proc instance using `to_proc` method defined on that object (if any). In the case of a Symbol instance, like above, `to_proc` works in this way:

```ruby
  class Symbol
    def to_proc
      Proc.new { |x| x.send(self) }
    end
  end
```
What the `to_proc` implementation in the **Symbol** class does is to call a method with the same name as the `symbol` (i.e. self) on the passed in object. So say the symbol name is `:attack` and the passed in argument is `enemy`, the result will be `enemy.send('attack')` which is equivalent to `enemy.attack`

So for each of the soldiers in the array, the map method passes it to the proc as `x`. Therefore, the argument to map is interpreted as
```ruby
  soldiers.map { |x| x.name } # where x in this case is each soldier in the array
```
Note that this `Symbol#proc` conversion is valid for all methods that accept a proc.

With that knowledge, to return the soldiers weapon, we can easily do

```ruby
  soldiers.map(&:weapon) #=> ["Bazooka", "Pistol", "Uzi"]

  # to return the soldiers salaries
  soldiers.collect(&:salary) #=> [4000, 5000, 8000]

  #to get soldiers with grade_a_weapon
  soldiers.select(&:grade_a_weapon?) #=> [#<Context::Soldier:0x000000017065b0 @name="Big Mike", @weapon="Bazooka">, #<Context::Soldier:0x00000001706538 @name="Small John", @weapon="Pistol">]
  soldiers.select(&:grade_a_weapon?).map(&:weapon) #=> ["Bazooka", "Pistol"]
```
_Here's one for you to try out: write code to retrieve the last_name of soldiers with grade_a_weapon. Share your implementation in the comment section._

Now let's match towards a more ambitious adventure.

We can override the `to_proc` method in the Symbol class to achieve custom result. For instance say we want to always append the team captain's name to all `to_proc` requests;

```ruby
  class Symbol
    def to_proc
      Proc.new { |x| "#{x.send(self)} (signed John Doe Commander)" }
    end
  end

  # Then we have
  soldiers.map(&:name) #=> ["Big Mike (signed John Doe Commander)", "Small John (signed John Doe Commander)", "Lightening Larry (signed John Doe Commander)"]
```
Of course this will override every other symbol `to_proc` conversion

```ruby
  [1, 2, 3, 4, 5].map(&:to_f) #=> ["1.0 (signed John Doe Commander)", "2.0 (signed John Doe Commander)", "3.0 (signed John Doe Commander)", "4.0 (signed John Doe Commander)", "5.0 (signed John Doe Commander)"]
```

What if we don't want to depend on the **Symbol** `to_proc` conversion, well, we can define our own.

Say we want to still append the team captain's name to our conversion but we don't want it to override other `symbol#proc` conversion, we can create a custom class with a to_proc method defined therein and then pass that as the arg to our map method method.

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
  # say you want to retrieve the names of all the grade_a_weapon?
  soldiers.select(&:grade_a_weapon?.name) #=> undefined method `name' for :grade_a_weapon?:Symbol

  # or
  soldiers.select(&:grade_a_weapon?, name) #=> syntax error, unexpected ',', expecting ')'

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
Congrats you've successfully completed the mission. Show your mastery of `symbol#proc` by rewriting the examples above using what you just learned; paste your implementation in the comment section below.
