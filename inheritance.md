# Inheritance

Here we're just going to give a very basic picture of inheritance: some additional details, like the specifics of the _method loookup path_ or how different sorts of variables are inherited, we will put in their own separate notes.

## Subclass

Inheritance follows from a natural way we think about objects in the real world (but there are some differences). We can look at any particular object and ask what it's "type" is. So, for example, I pick up a metal object from the dinner table and say "what sort of thing is this?" And someone might say "that's a knife" (_pace_ Crocodile Dundee). To speak a little more abstractly, we might say that it is an object of the _class_ knife. This way of organizing our thought about objects goes back at least to Aristotle. And an important part of this way of organizing objects into class/types is the notion of a hierarchy. So, sure, this object is a knife, but knives are eating utensils, and eating utensils are dinnerware, and dinnerware are (say) physical objects, and physical objects are objects (let's make the Platonists happy and leave room for the possibility of a separate class of abstract objects). Our hierarchy might look like this:

Object
Physical object
Dinnerware
Utensil
Knife

Now, of course, what this _means_ is that a knife has all the properties of a utensil, which has all the properties of dinnerware, which has all the properties of physical objects, which have all the properties of objects. To introduce some programming terminology, knives _inherit_ the behvaior and properties of their superclass, and so on up the chain.

This is, in a nutsell, the idea behind inheritance in object-oriented programming. So let's get real and write some code. Suppose we have an animal class:

```ruby
class Animal
  def alive?
    true
  end
end
```

Now suppose we need to work with animals that have some specific behavior... for example, that are warm-blooded. Well, since they're animals, we'll want to let them respond to an `alive?` call, but since they're warm-blood, they should also be able to respond to a `regulate_body_temperature` call. So we'll create a new class, `Mammal`, but rather than define the same `alive?` method in our new class, we'll _inherit it_ from our `Animal` class:

```ruby
class Mammal < Animal
  def regulate_body_temperature
    puts "Ok, burning calorie to warm up"
  end
end

my_mammal = Mammal.new
my_mammal.alive? # => true
my_mammal.regulate_body_temperature # => Ok, burning calories to warm up
```

When we create a subclass of an existing class, instances of the subclass inherit all the instance methods from the superclass. Somewhat interestingly, our subclass also inherits the _class methods_ from our superclass. I say surprisingly because the _class_ of our subclass is still `Class`, so by the standard lookup path it should look only in its own singleton class and then `Class` for methods. But here it's looking in the singleton class of the superclass. This is made even more confusing by the fact that class instance _variables_ aren't inherited.

But that's not all. Subclasses also inherit instance variables, class variables, and constants. (As I mentioned above, class _instance_ variables are not inherited. Which makes sense in its own right, but is a somewhat weird twist in light of the fact that class instance methods are ineherited.)

I think that's all we need to say here. We'll talk in other notes about how inheritance can be used to achieve polymorphism, about the weirdness of class variable inheritance, and about the method lookup path.
