# Classes and Objects

Ok, here are a bunch of examples of basic class and object concepts.

## Defining a Class

Objects are things that have attributes (or _state_, or _properties_), and are capable of various _behaviors_ (or can be told to perform various _actions_). In Ruby, we generally create a new object by first defining a class that specifies a set of actions -- methods -- and a set of states -- instance variables -- that objects characterize objects of that class. We define a class as follows:

```ruby
class Person
  def initialize(name)
    @name = name
  end

  def speak
    puts @name
  end
end
```

Here we create a new class, `Person`, and define two instance methods for the class. The first, `initialize` is a constructor method, which gets called whenever we instantiate a new `Person` object. This sets the object's `@name` instance variable to whatever value is passed to it (which we'll see how to do in a moment). The `speak` instance method calls `puts` with the value of `@name` as an argument. Now all `Person` objects will have a name, and will be able to speak.

## Creating an Object

Now that we have a class, we can create an object:

```ruby
bob = Person.new('Bob')
bob.speak # => 'Bob'
```

We instantiate an object from our class by calling the `#new` class method on our class (which our class inherits from its ancestor, the `Class` class). `#new` calls our class's constructor (if it has one). Since our constructor takes a parameter (a name), we have to call `new` with an argument, which we do (the string `'Bob'`). Our constructor then creates a new `Person` object and assigns `Bob` to that object's `@name` instance variable. 

To demonstrate that our object knows how to speak, we call the `speak` message on `bob` in the next line. When `bob` receives this method call, it looks in its class definition for a definition of the `speak` method. Finding one, it does what the method says (which is to print the value of its `@name` ivar to the screen).

## Inheritance

```ruby
class Chef < Person
  def mash_potatoes
    # Grandma's secret mashed potato recipe
  end
end
```

Here we create a new class, `Chef`, that is a subclass of `Person` (we can say that `Person` is its _ancestor_, or its _parent_, or its _superclass_) -- the `< Person` syntax ensures this. This means that our `Chef` class will inherit all of the methods of its parent class, and will have access to all instance variables of the parent class (provided that these instance variables get initialized; see below). That means we can do this:

```ruby
susan = Chef.new('Susan')
susan.speak # => 'Susan'
susan.mash_potatoes # => (Follows Grandma's secret m. potatoes recipe)
```

### Unintialized Instance Variables in Parent

```ruby
class Animal
  def learn_to_swim
    @can_swim = true
  end

  def can_swim?
    @can_swim
  end
end

class Dog < Animal; end

fido = Dog.new
fido.can_swim? # => nil
```

Here we create a new `Dog` object, `fido` and ask it if it can swim by calling its `can_swim?` method. `fido` finds this method, and sees that it references its `@can_swim` instance variable. But because we never called `learn_to_swim` on `fido`, we never initialized this instance variable with a value. Since references to uninitialized instance variables return `nil` (rather than throw an error like uninitialized local variables), our call to `can_swim?` returns `nil`. So our subclasses have access to the instance variables of the parent class, but because instance variables can only ever be initialized from within an instance method, they won't get intitialized with a value unless we call the relevant instance method.

# Create getters and setters

```ruby
class Person
  def initialize
    @name = 'Bob'
  end

  def name
    @name
  end

  def name=(new_name)
    @name = new_name
  end
end
```

Objects of our person class have an `@name` instance variable. Ruby has no concept of public or private instance variables, so unless we provide some methods, there is no way for the rest of our code to ask our object what value it's storing in its `@name` instance variable (or to update that value). Since this is sometimes necessary, we have create _getter_ and _setter_ methods that let us _get_ and _set_ the value of this instance variable. We didn't have to, but we followed convention and named these methods to correspond with the name of the instance method. Now we can do this:

```ruby
bob = Person.new('Bob')
bob.name # => 'Bob'
bob.name = 'Mike'
bob.name # => 'Mike' (this example shows that you should be careful how you name things!
```

Notice how we invoked the setter method in a way that looks like assignment. It isn't. It's just calling `Person#name=()`, using some tasty syntactical sugar ruby provides. We could have more excplicitly written `bob.name=('Mike')`. When we do `obj.some_method = value`, Ruby looks in the object's lookup chain for a method named `some_method=` and then passes it `value` as an argument.

## Using attr_* methods

This is a bit verbose, it would be nicer if we could just declare which instance variables we want to provide getters and setters for. We can do this using `Module#attr_reader`, `Module#attr_writer`, and `Module#attr_accessor`. These methods take a list of symbols as arguments: the symbol corresponds to the name of the instance variable (i.e. `:name` for `@name`). The first creates getter methods for all instance variables corresponding to its symbol parameters, the second creates setters, and the final creates both getters and setters. So we could achieve what we did above by doing either:

```ruby
class Person
  attr_reader :name
  attr_writer :name
  ...
end
```

or:

```ruby
class Person
  attr_accessor :name
end
```

## When not to use attr_* methods

Sometimes it's useful to more with getters and setters than merely get or set the value of an instance variable. For example, we might want to format a value in a nice way before returning it. Or we might want to make sure that a value is valid before assigning it to our instance variable. In that case, we will have to resort to defining our own custom methods:

```ruby
class Person
  def name
    @name.capitalize # So it looks nice
  end

  def name=(new_name)
    # raise error if new_name isn't valid
    @name = new_name
  end
end
```

In fact, when an instance variable contains an array or a string, it's often a good idea to have your getter return a _copy_ of that array or string, so that outside code can't bypass your setter methods and mutate the string or array directly:

```ruby
class Person
  def name
    @name.dup
  end
end

bob.name # => 'Bob'
bob.name.gsub!(/.+/, 'Mike') # => 'Mike'
bob.name # => 'Bob'
```

## A wrinkle on calling setters from inside our class

```ruby
class Person
  attr_accessor :age

  def get_older
    age = age + 1
  end
end

bob.age = 55
bob.get_older
bob.age # => 55!
```

Why didn't this work? On the first line of our `get_older` method, we attempted to use our `age=` setter method to increment `bob`'s age by 1 (passing it the result of adding 1 to `bob`'s current age, which we accessed with the `age` getter method). But think about things from Ruby's perspective: `age = age + 1` looks an awful lot like local variable assignment. And that's how Ruby interprets it. Normally, we can call methods without an explicit receiver and Ruby will use `self` as the receiver (that's how we called the getter method on the same line). But with our fancy setter syntax, we can't do that because it it looks like variable assignment. What to do? Just give it `self` as an explicit receiver `self.age = age + 1`.

Note that this conundrum creates an exception for private methods. Normally, private methods cannot be invoked with an explicit receiver (this is what makes them private!). But the exception is private setter methods. These can be called with `self` as the explicit receiver. That still keeps them private.

# Instance Methods vs Class Methods

Class methods are methods we call on our class, instance methods are methods we call on instances of our class. It is sometimes useful to have methods that aren't specific to any instance of our class, these are class methods. To define them, we can take advantage of the fact that `self` inside a class definition refers to the class we are defining:

```ruby
class Dog
  def self.sound
    puts "Dogs say 'woof'"
  end
end

Dog.sound # => Dogs say 'woof'
```

This adds a new `sound` method to `Dog`'s singleton class, allowing us to call `Dog.sound`. 

## Instance Variables vs Class Variables

```ruby
class Dog
  @@number_of_dogs = 0
  
  def self.number_of_dogs
    @@number_of_dogs
  end

  def initialize
    @@number_of_dogs += 1
  end
end

dog1 = Dog.new
Dog.number_of_dogs # => 1
```

Here we combine a class method, `number_of_dogs` with a _class variable_ `@@number_of_dogs`. As our `initialize` method here demonstrates, class variables are accessible from within instance methods, and all instances of a class share the same copy of its class variables.

## Modules
