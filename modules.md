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