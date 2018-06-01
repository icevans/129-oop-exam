# Classes and Objects

Ruby is an _object-oriented_ programming language. This means that we can create _objects_. Objects have internal _state_ (or _attributes_, or _properties_) and can be told to do various things. What sort of state does an object maintain? What sorts of things can we tell it to do? This is determined by the type of object we're dealing with. In Ruby, we use _classes_ to create types of objects. A class is ultimately a template for stamping out new objects: it specifies what objects of that type can be told to do, and what sort of state they maintain. So, in its most canonical form, creating a new object means first defining a class, and then _instantiating_ that class. We then have an object that is an _instance_ of said class.

Let's descend from abstraction a bit and illustrate with some code. Classes in Ruby can be defined as follows:

```ruby
class NamePrinter
  @name = 'Ian'

  def print
    puts @name
  end
end
```

Here we define a new class called `NamePrinter`. It specifies one attribute and one behavior. The attribute is a name, stored in the _instance variable_ `@name`, and the behvaior is an ability to print, stored in an _instance method_ named `print`. Now, if we create an object that is an instance of `NamePrinter`, that object will have state stored in `@name` and an ability to `print`. Let's instantiate such an object now:

```ruby
my_printer = NamePrinter.new
```

Here, we call the `new` method on our class, which returns a new object of that class. (`new` is actually an instance method of Ruby's generic `Class` class, and our custom class is an object that is an instance of that class. But we can sest taht aside for now.) We then store that object in the variable `my_printer` so we can reference it later, should we want to tell it to do something, or ask it about its state. This last step is optional, but generally its useful to have a way to refer to the objects you instantiate.

Alright, we have our object, now let's tell it to do something!

```ruby
my_printer.print # => "Ian"
```

We tell an object to do something by appending a reference to that object with a `.` and then the name of a method. I call this "calling a method on an object," and I say that the object I call a method on is "the receiver of the method call." When an object receives a method call it recognizes, it executes that method. But we can only call methods on an object that that object recognizes. This is determined by the _method lookup path_, which we'll discuss in a future note. But, in a nutshell, an object can recognize any method within its class definition, the class definition of one of its _superclasses_, or within a _module_ included in its class or subclasses. (Again, we will postpone discussion of superclasses and modules for a future note.) If an object receives a call to a method it doesn't recognize, it throws a `NoMethodError`.

Our `NamePrinter` class is sort of boring: every object of that class has identical state stored in the `@name` instance variable. But this is an accident of how we defined our class: in fact, every object of a class gets its own unique copy of the instance variables initialized in the class, and so can keep its own distinct state. Let's give `NamePrinter` objects a way of differentiating themselves:

```ruby
class NamePrinter
  def set_name(name)
    @name = name
  end

  def print
    # ... same as before
  end
end

ian_printer = NamePrinter.new
ian_printer.set_name('Ian')
ian_printer.print # => "Ian"

bob_printer = NamePrinter.new
bob_printer.set_name('Bob')
bob_printer.print # => "Bob"
```

Here we added an instance method `set_name` to our class. We call this method on a `NamePrinter` object and give it a string as an argument. The object recognizes this method and so executes: it assigns the string it receives as an argument to its `@name` instance variable. 

We demonstrate that each object has its own state (stored in its own copy of the instance variables declared in the class) by creating two `NamePrinter` objects and calling `set_name` on them with different arguments. (What we have created here is, in effect, a _setter method_. Ruby provides with a nicer syntac for these methods, as well as a shortcut for creating them. But we'll discuss that in a future note.)

There is one last thing to discuss here before moving on to more specific object-oriented topics. Generally, when we instantiate an object from a class, there's a bunch of state we'd like to set right away so that the object is ready for use. What we need is a _constructor_. In Ruby, we can create a constructor for our class by defining an instance method called `initialize`.

The `initialize` method will be called whenever we call `new` on our class. Even better, any arguments we provide to `new` will be passed to `initialize`. This means we can define `initialize` to accept parameters. Here's an example:

```ruby
class NamePrinter
  def initialize(name)
    @name = name
  end
  
  # ... rest same as before
end

ian_printer = NamePrinter.new('Ian')
ian_printer.print # => 'Ian'
```

Neat.
