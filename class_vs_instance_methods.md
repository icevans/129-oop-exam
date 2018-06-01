# Class Methods vs Instance Methods

Ok, so we can give objects our class behavior by defining _instance methods_ for them. We do this simply by defining methods like we're used to, but inside the class definition. So, for example:

```ruby
class Cat
  def speak
    puts "meow"
  end
end
```

And just like that, we've created a new instance method for our cat class. This means that we can call speak on any instance of our `Cat` class.

But what if we have some behavior that we want the class itself to have -- something where it doesn't make sense to tell any particular object? For example, suppose we want our `Cat` class to be able to tell us what sound cats make. Ruby allows us to define _class methods_ for this. Here's how we could create one:

```ruby
class Cat
  def self.sound
    puts "Cats say 'meow'"
  end
end
```

What's different here (from our instance method definition) is that we put `self.` before the method name. This tells Ruby that we're defining a method on `self`. Since occurrences of `self` directly inside a class definition refer to the class, we are telling Ruby we want to define a new method on our class. And how do we call this method? Just as you'd expect, by using a reference to our class as the receiver: `Cat.sound # => Cats say 'meow'`.

## A Brief Diversion

Putting an explicit receiver in our method signature is something we haven't seen before. One might think that it's just a peculiar syntax Ruby gives us for defining class methods. In fact, however, it's generally available for any method definition. It provides way to define instance methods on particular objects. For example:

```ruby
jimmy = Cat.new
quica = Cat.new

def jimmy.chirp
  puts 'brrrr'
end

jimmy.chirp # => brrrr
quica.chirp # => NoMethodError
```

Here, we gave the Jimmy `Cat` instance an ability to `chirp` by defining an instance method specifically for that object. Since we did not define this method in the class, `quica` does not have this ability. (Under the hood, Ruby does this by creating a singleton class for `jimmy` and defining the method there. Each object in Ruby has its own singleton class where it looks for method definitions before moving to its explicit class. But this is a more advanced topic that we won't worry about for now.)

Knowing that we can do that, and that classes in Ruby _are just more objects_, we can do things like this:

```ruby
def Klass.class_method
  # stuff
end
```

to define methods on our class. This is really the only good way to give our class object its own instance methods. After all, classes all directly from `Class` and we certainly don't want to be extending the default `Class` class.

So a "class method" is really just an instance method for our particular class object! Everything in Ruby is an object! Of course, the most natural place to define these methods is inside the class definition. And from there, `self`, as we noted, refers to the class object itself, so we can just write `def self.class_method` instead of `def Klass.class_method`.

So it's not special syntax at all, and so-called "class methods" are not special in any way: they're just instance methods for our class objects.
