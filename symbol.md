# SYMBOLS#PROCS

Thank you for accepting this mission, and the mission is nothing else but to add Symbols#procs to our ruby toolchain . However, to achieve our mission, we need first `touch` **symbols** & **Procs** and then with their combined power we will take on the mission. So then, come on brave one to `:symbols`.

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
