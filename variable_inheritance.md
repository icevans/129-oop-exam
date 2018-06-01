# Variable Inheritance

Instance variables are inherited in the sense that if a superclass instance method initializes an instance variable, and we call that method on an object of the subclass, said object gets its own copy of that instance variable set to that value. This works intuitively, and I'm not sure there's anything else to say about it.

## Class Variables

Class variables are inherited, but it's a little funky. Let's use the way instance method inheritance works as an example. Instance methods are inherited by subclasses, so we can call them on objects of our subclass even if we haven't defined them in our subclass (supposing they're defined in the parent class). But, if we want to, we can override (or augment, with `super`) the instance method in the parent class by redefining it in the subclass. But this has no effect on the parent class or other subclasses of the parent: the parent method is unchanged.

Class variables are different. If we set them in a parent class, we can reference them from subclasses of that parent. But if we change them in a subclass, that change propogates up to the parent class, and all other classes which inherit from that class. In effect, all members of the class hierarchy share a single copy of the class variable.Of course, if the parent class does not define the class variable, then a subclass that defines a class variable will not propogate _that_ variable up the chain! So that's weird.

Because of this weirdness many people eschew the use of class variables altogether in favor of class instance variables. This isn't a perfect solution, though, because class instance variables aren't inherited at all.

## Constants

Constant inheritance _almost_ works like we want, but the fact that constants are lexically scoped creates one little gotcha. Let's start with an example of things working the way we'd expect:

```ruby
class Parent
  CONST = 'parent_value'
end

class Child < Parent
  def self.speak
    puts CONST
  end
end

Child.speak # => 'parent_value'
```

And, as with methods, we can override the value of a constant in our child if we wish, without changing the parent's value of the constant:

```ruby
class Child < Parent
  CONST = 'child_value'
  
  def self.speak
    puts CONST
  end
end

Child.speak # => 'child_value'
Parent::CONST # => 'parent_value'
```

So that's cool. However, class and instance _methods_ defined in the parent will always use whatever value of the constant the parent sets, even if the child has "overridden" that value:

```ruby
class Parent
  CONST = 'parent_value'

  def self.speak
    puts CONST
  end
end

class Child < Parent
  CONST = 'child_value'
end

Child.speak # => 'parent_value' (!)
```

Here we call the speak method on our `Child` class, which is defined in the `Parent` class. That method is executed and it encounters a reference to `CONST`, since the value of constants is determined before run time, that reference has already been set to `parent_value` (what else would we set it to at compile time?). To use the child's value, we would have to dynamically evaluate the reference to `CONST` in the `speak` method at runtime, which Ruby doesn't do.

Of course, we can namespace constant references, so there is a way to get the expected behavior: change the implementation of the speak method to: `puts self::CONST`. Now it all works very nicely.

That's all there is to say about variable/constant inheritance.
