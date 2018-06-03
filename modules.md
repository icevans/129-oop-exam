# Modules

Modules are Ruby's way of handling Multiple inheritance. Let's look at an example.

```ruby
modle Breakable
  def break
    puts "It's broken"
  end
end

class DinnerWare
  include Breakable
end

class Electronics
  include Breakable
end
```

Now, if both `DinnerWare` and `Electronics` objects will be able to respond to the `break` method, without us having to define this method twice. With single inheritance, there is no way to achieve this without giving both classes a common ancestor, which really wouldn't make sense here.

One way to think about whether to use a module or regular class inheritance via subclassing: you want to use class inheritance when the relationship is an "is a", and modules when the relationship is a "has a." In particular, when its "has an ability to." So neither DinnerWare nor Electronics _is a_ breakable, but both _have an ability_ to break ("ability" is maybe an awkward word in this case... we could talk instead about a "tendency" or a "disposition").

## Namespacing

We can also use modules to _group_ and _namespace_ our classes. Because of the syntax for referencing classes defined inside modules, this can help make clear the relationships between classes. It also ensures that if we include other libraries, that our class names won't conflict.

```ruby
module TabularData
  class Row
    # I'm a data row class
  end
  
  class Cell
    # I'm a data cell
  end
end

my_row = TabularData::Row.new
my_cell = TabularData::Cell.new
```

So this is kind of cool because in the client code, it's clear that the `Row` class and `Cell` class are related... they are both part of a library designed for handling tabular data. Additionally, we can now rest easy that if we include some other library that might define a row class (say, a `CSV` module), that our class names won't conflict.

## Grouping Methods

Just as we can _group_ and _namespace_ our classes inside a module, we cand do something similar for methods. Sometimes, we need a general purpose method that is not part of any particular class. Rather than having these floating in the global namespace, we can put them inside a module for better organization and to avoid global namespace pollution. So, for example:

```ruby
module TabularData
  def self.some_method
    # ...
  end
  
  def self.another_method
    #...
  end
end

TabularData.some_method # calls some_method
TabularData.another_method # calls another_method
```

(We can also call module methods using the `::` syntax, but the `.` syntax is preferred.)