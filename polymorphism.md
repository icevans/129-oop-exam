# Polymorphism

Polymorphism is a way of reducing dependencies between pieces of code by writing code that is agnostic about the type of data it is dealing with. In object-oriented programming we acheive by ignoring the class or implementation details of interacting objects, and rely instead only on their public interfaces. In this way, one object type of object can be swapped for another in an interaction, so long as it exposes the relevant interface methods.

Let's give some examples. Suppose we have a `feed_animal` method:

```ruby
def feed_animal(animal)
  animal.feed
end
```

This method is defined in a way that is agnostic about the type of object it operates on. As long as the object can respond to a `feed` method call, our code will work. Now there are three main ways we can generate different types of objects that will work happily with this code: inheritance, modules, and duck-typing.

## Inheritance

We might have an animal class:

```ruby
class Animal
  def feed
    @hunger = 0
  end
end
```

Obviously, we can pass instances of our `Animal` class to the `feed_animal` method. But suppose we realize we need some `Cat` objects that do a bunch of cat specific stuff (like `sit_on_keyboard_while_human_tries_to_work`), but we know they may be passed to the `feed_animal` method. Then we can create a subclass:

```ruby
class Cat < Animal
  # cat stuff
end
```

Since our `Cat` class inherits the `feed` method from our `Animal` class, we can pass `Cat`s or generic `Animal`s to `feed_method`, and everything works fine.

## Module

Suppose we're creating a new type of object that really shouldn't be a subclass of `Animal`. Perhaps, for example, we now have plants that need to be fed. For this case, we can create a module that contains our `feed` method, and include it in our plant class:

```ruby
module Feedable
  def feed
    @hunger = 0
  end
end

class Plant
  include Feedable

  # plant stuff
end
```

Now we can happily pass `Animal`, `Cat`, or `Plant` objects to our (now poorly named) `feed_animal` method.

## Duck-Typing

Ok, but now we have some unrelated class that we may want to pass to `feed_animal`, but which shouldn't be fed at all in the way our `Feedable` module works. We can just define the needed public methods directly on that class -- even though they will work very differently from the `Animal` or `Feedable` method, our `feed_animal` method will work fine:

```ruby
class Ego
  def feed
    self.praise
  end
end
```

Now we can even pass our weird `Ego` objects to `feed_animal`. They're not actually animals, but since they response to `feed`, they can be treated as such for the purposes of `feed_animal`.
