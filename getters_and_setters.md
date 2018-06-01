# Getters and Setters

In our previous note, we discussed how to define classes, and how to instantiate objects from them. We talked about how to create constructors via `initialize` to give set some state for new objects, and we also gave an example of creating a method to change the state of an object. Here we continue our discussion by looking in more detail about methods for getting and setting an object's state: so-called _setter_ and _getter_ methods.

Calling our `NamePrinter` setter method looked like this: `printer.set_name('ian')`. But wouldn't it be nicer if we could write something like this: `printer.name = 'ian'`? Because Ruby is awesome, we can! To do so, we just need to define our `setter` method as follows:

```ruby
class NamePrinter
  def name=(new_name)
    @name = new_name
  end
end
```

By ending our method name with an `=` sign, Ruby recognizes it as a setter method, and so will now know to call this method if we write `printer.name = 'foo'`. A nice spoon full of syntactical sugar!

Getter methods are even easier to create:

```ruby
class NamePrinter
  def name
    @name
  end
end
```

Here we've defined an instance method, `name`, that simply returns the value of the receiving object's `@name` instance variable.

## Using attr_*

That's nice and all, but it's a bit verbose... do we really need to do that for every instance variable that we want to have getters and setters for? Again, because Ruby is awesome, we do not. Ruby provides us with `attr_reader`, `attr_writer`, and `attr_accessor` methods that can generate getter and setter methods for us. These methods accept a list of symbols as arguments, and create getter/setter methods with names corresponding to the symbols that get/set instance variables named to correspond. `attr_reader` gives our class getter methods, `attr_writer` gives our class setter_methods, and `attr_accessor` gives our class both getter and setter methods.

Let's give thus a try:

```ruby
class NamePrinter
  attr_accessor :name

  def initialize(name)
    @name = name
  end
end

printer = NamePrinter.new('bob')
printer.name # => 'bob'
printer.name = 'ian'
printer.name # => 'ian'
```

So doing `attr_accessor :name` not only gave us `name` and `name=` instance methods, it also created those methods to get and set a `@name` instance variable. Neat. 

## A Brief Aside on Initializing Instance Variables

Since there's no other place to discuss this, and it's somewhat relevant,
I'll take a moment to note a subtle but significant difference between instance variables and regular old local variables. If we attempt to reference an uninitialized local variable, we'll get an error (a `NameError` exception to be exact). However, if we reference an unitialized _instance_ variable, we'll just get the value `nil`.

For example, suppose we didn't have an initialize method for our `NamePrinter` class:

```ruby
class NamePrinter
  attr_accessor :name
end

NamePrinter.new.name # => nil
```

Here we call our getter method `name` on a new `NamePrinter` object, _before_ ever calling our setter method. The `name` getter method dutifully returns the value of `@name`. But since we've never set a value for `@name`, it defaults to `nil`. This is good to be aware of because it's not uncommon to see code rely on this with things like `if ivar.nil? ...`, where we're checking whether an instance variable has been initialized yet.

### When to Use attr_*

As nice as these `attr_*` methods are, they're not always appropriate. It sometimes makes sense to define our own custom getter/setter methods. For example, we might want to include some input validation in our setter method (a setter method for a social security number ought to check that its argument has the correct number of digits). Or we may want our getter method to return the value of the instance variable in a certain format. Because the `attr_*` methods give us bare getters/setters, in these cases we will have to define our own custom getters and setters.

(Aside the `attr_*` methods are actually instance methods defined in the `Module` module, which is included in `Class`. Since our custom classes subclass `Class`, they are available to us. Since they are instance methods _of our class_ (and not of objects of our class), and since code inside a class definition is regular executable code, we can call these methods directly inside our class definition. This relies on the fact that a method call directly inside a class definition will use that class as the implicit receiver.) The `attr_*` methods then extend the class to include the relevant setters/getters.)

## Calling Getters and Setters

We've already discussed how to call getters and setters outside a class's definition: type a reference to an object, followed by a dot, followed by the name of the getter/setter. But what about inside the class, where we don't have a reference to a specific object?

Suppose for example, that we have the following:

```ruby
class Bill
  attr_accessor :total

  def initialize
    @total = 0
    @item_prices = []
  end

  def add_item_price(item_price)
    @item_prices << item_price
  end
end
```

Here we have used `attr_reader` to create a getter for a `@total` instance variable. Objects instantiated from our `Bill` start out with their `@total` ivar initialized to `0` and their `@item_prices` initialized to an empty array. We have also provided an `add_item_price` method so that we can add new items to our bill (our bill is simple and only includes a list of amounts, not the names of the items that cost those amounts). 

Of course, when we add an item to our bill, it should probably update the total. Since we have a setter method for `@total`, we might think to amend our method as follows:

```ruby
  def add_item_price(item_price)
    @item_prices << item_price
    total = total + item_price
  end
```

Unfortunately, this doesn't work. Here we are attempting call our `total=` setter method. There's no obvious receiver to use since we're inside our class definition: we're not calling `total=` on any particular instance of our class, but simply trying to tell our class that _whichever_ instsance receives an `add_item_price` call should then use its `total=` setter to update the total. This is the right idea, but the problem is that `total =` without an explicit receiver looks just like local variable assignment. And that's exactly how Ruby treats it. Instaed of updating our object's state by changing the value in its `@total` ivar, we've created a temporary local variable!

The way out of this conundrum is to use a special Ruby keyword: `self`. The `self` keyword is like a variable that keeps track of whichever object is currently in scope. Inside the instance method definitions of a class, `self` will always refer to whatever instance of that class received the method call. So, to use our `total=` setter method, we can give it an explicit receiver: `self.total = total + item_price`. 

Of course, that line of code also makes use of our `total` getter method. Why don't we have to preface that with an explicit `self` receiver? While that invocation could be confused with a reference to a local variable named `total`, there isn't a local variable by that name, so Ruby checks to see whether's there's a method of that name, which there is. Of course, if we wanted to, we could also always prepend our setter methods with `self`. This is a matter of style (the main Ruby style guide comes out against this, for what it's worth).

We could say more about `self` here, but we'll save that for a future note. There are also some caveats about how to call these methods depending on whether they're public, private, or protected. But we'll defer that discussion to a future note that deals with public/private/protected in general.

## Getters/Setters vs. Instance Variable Reference

We just discussed how to call our getters/setters from within our instance method definitions. But why do we even need to do this? After all, our instance variables are scoped at the instance level, and so we can just access them directly from instance methods!

The answer is that we don't _need_ to, but it is _good practice_ to do so. For example, suppose our setter method reformats its input to guarantee that data is stored in a certain way. Well then we should always use that setter method, even inside our class. Otherwise we risk storing data in the wrong form (or else reduplicating the formatting code every time we set the instance variable directly). If, down the line, we decide to change the formatting, we only have to change the setter method, knowing that the only way that the instance variable gets changed is throught that setter. Ditto for our getter methods: even if they don't now return the data in a certain form, we may decide down the line to change that. If we only ever get the values of our instance variables via our getter method, we only ever have to make that change in once place.

There are gray areas, however. Suppose our class stores social security numbers in an `@ssn` instsance variable, and we provide a public `ssn` reader that returns only the last four digits of the social security number (for security reasons). Still, there may be places insde the class where need to work with the full social security number (to compare it to an input, say). In that case, we can't use our public `ssn` method. In this case, it seems defensible to just reference `@ssn` instance variable directly.

Still, there may be a better option: we could create a private `full_ssn` getter that returns the full social security number. Then we can get the data we need while still getting the benefits of referencing instance variables only through their getters/setters. Private getters and setters are thus useful even for instance variables that we don't to publicly expose.
