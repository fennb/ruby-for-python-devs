# Chapter 10: Modules and Mixins

## 10.1 What are Modules?

Modules in Ruby serve two primary purposes: **namespacing** (organizing code) and **mixins** (sharing functionality across classes). Unlike Python modules which are simply files, Ruby modules are explicit language constructs that provide a way to group related methods, classes, and constants together.

**Key Similarities:**
- Both provide a way to organize and namespace code
- Both can contain methods and constants
- Both help avoid naming conflicts

**Key Differences:**
- Ruby modules are defined with the `module` keyword, not just file boundaries
- Ruby modules cannot be instantiated (no `module.new`)
- Ruby modules can be "mixed in" to classes to share behavior (Python uses multiple inheritance)
- A single Ruby file can contain multiple modules

### Python Example

```python
# In Python, modules are just files
# File: math_utils.py

PI = 3.14159

def circle_area(radius):
    return PI * radius ** 2

def circle_circumference(radius):
    return 2 * PI * radius

# Using the module
import math_utils
print(math_utils.circle_area(5))
```

### Ruby Equivalent

```ruby
# Modules are defined with the module keyword
module MathUtils
  PI = 3.14159

  def self.circle_area(radius)
    PI * radius ** 2
  end

  def self.circle_circumference(radius)
    2 * PI * radius
  end
end

# Using the module
puts MathUtils.circle_area(5)
puts MathUtils::PI
```

### Explanation

In Ruby, modules are first-class language constructs defined with `module...end`. To create methods that can be called directly on the module (like Python's module-level functions), you define them with `self.method_name`.

**Important points:**
- Module names use `PascalCase` (same convention as classes)
- Access module constants with `::` (e.g., `MathUtils::PI`)
- You can also use `.` to call module methods (e.g., `MathUtils.circle_area(5)`)
- Modules cannot be instantiated - `MathUtils.new` raises an error
- A single `.rb` file can contain multiple modules

**Common pitfall:** In Python, every `.py` file is automatically a module. In Ruby, you must explicitly define modules with the `module` keyword.

---

## 10.2 Modules as Namespaces

Modules provide namespacing to organize code and prevent naming conflicts, similar to Python packages and modules. You can nest modules to create hierarchical namespaces.

**Key Similarities:**
- Both prevent naming conflicts
- Both provide hierarchical organization
- Both use dot/double-colon notation for access

**Key Differences:**
- Ruby uses `::` for namespace resolution (Python uses `.`)
- Ruby modules can contain classes, methods, and constants all in one construct
- Ruby requires explicit `module` declarations, not just directory structure

### Python Example

```python
# File: geometry/shapes.py
class Circle:
    def __init__(self, radius):
        self.radius = radius

    def area(self):
        return 3.14159 * self.radius ** 2

# File: geometry/calculations.py
def perimeter(shape):
    return 2 * 3.14159 * shape.radius

# Usage
from geometry.shapes import Circle
from geometry import calculations

circle = Circle(5)
print(circle.area())
print(calculations.perimeter(circle))
```

### Ruby Equivalent

```ruby
module Geometry
  class Circle
    def initialize(radius)
      @radius = radius
    end

    def area
      3.14159 * @radius ** 2
    end
  end

  module Calculations
    def self.perimeter(shape)
      2 * 3.14159 * shape.radius
    end
  end
end

# Usage - access nested classes and modules
circle = Geometry::Circle.new(5)
puts circle.area
puts Geometry::Calculations.perimeter(circle)
```

### Explanation

Ruby modules create namespaces that can contain classes, other modules, methods, and constants. You use the `::` operator (called the scope resolution operator) to access nested items.

**Important points:**
- Use `::` to access classes, modules, and constants within a namespace
- You can nest modules as deeply as needed: `Outer::Middle::Inner::MyClass`
- Modules keep related code organized without requiring separate files
- You can reopen modules to add more definitions (just like classes)

**Alternative syntax for nesting:**

```ruby
# Compact notation
module Geometry::Shapes::ThreeD
  class Sphere
    # ...
  end
end

# Equivalent to:
module Geometry
  module Shapes
    module ThreeD
      class Sphere
        # ...
      end
    end
  end
end
```

**Best Practice:** Use modules to organize related functionality. For example, a web framework might use `MyApp::Models::User`, `MyApp::Controllers::UserController`, etc.

---

## 10.3 Modules as Mixins (`include` vs `extend`)

This is where Ruby modules truly shine and differ from Python. Modules can be "mixed in" to classes using `include` or `extend`, allowing you to share functionality across unrelated classes without traditional inheritance. This is Ruby's answer to multiple inheritance.

**Key Similarities:**
- Both allow sharing code across multiple classes
- Both provide a form of multiple inheritance

**Key Differences:**
- Ruby uses mixins (`include`) instead of multiple inheritance
- `include` adds module methods as instance methods
- `extend` adds module methods as class methods
- Ruby's approach is more explicit and avoids the "diamond problem"

### Python Example

```python
# Python uses multiple inheritance
class Swimmable:
    def swim(self):
        return f"{self.name} is swimming"

class Flyable:
    def fly(self):
        return f"{self.name} is flying"

class Duck:
    def __init__(self, name):
        self.name = name

class SuperDuck(Duck, Swimmable, Flyable):
    pass

duck = SuperDuck("Donald")
print(duck.swim())  # Donald is swimming
print(duck.fly())   # Donald is flying
```

### Ruby Equivalent

```ruby
# Ruby uses modules and include
module Swimmable
  def swim
    "#{name} is swimming"
  end
end

module Flyable
  def fly
    "#{name} is flying"
  end
end

class Duck
  def initialize(name)
    @name = name
  end

  def name
    @name
  end
end

class SuperDuck < Duck
  include Swimmable
  include Flyable
end

duck = SuperDuck.new("Donald")
puts duck.swim  # Donald is swimming
puts duck.fly   # Donald is flying
```

### Explanation

The `include` keyword mixes a module's methods into a class as **instance methods**. You can include multiple modules, and they'll all be available to instances of the class.

**`include` vs `extend`:**

```ruby
module Greetable
  def greet
    "Hello!"
  end
end

class Person
  include Greetable  # Adds greet as an instance method
end

class Robot
  extend Greetable   # Adds greet as a class method
end

# include: instance method
person = Person.new
puts person.greet    # "Hello!"
# Person.greet       # Error! No class method

# extend: class method
puts Robot.greet     # "Hello!"
robot = Robot.new
# robot.greet        # Error! No instance method
```

**Important points:**
- `include` makes module methods available to **instances** of the class
- `extend` makes module methods available to **the class itself**
- You can both `include` and `extend` the same module in a class
- Multiple modules can be included: `include ModuleA, ModuleB, ModuleC`
- Modules included later take precedence in the method lookup chain

**Method lookup order:**

```ruby
module A
  def greet
    "From A"
  end
end

module B
  def greet
    "From B"
  end
end

class MyClass
  include A
  include B  # B is included later, so it takes precedence
end

puts MyClass.new.greet  # "From B"
```

**Common use case:** Adding behavior to classes without inheritance:

```ruby
module Timestampable
  def created_at
    @created_at ||= Time.now
  end
end

class Article
  include Timestampable
end

class Comment
  include Timestampable
end

# Both Article and Comment now have created_at method
```

---

## 10.4 Mixins vs Python Multiple Inheritance

Ruby's mixin system is designed to provide the benefits of multiple inheritance while avoiding some of its complexities. Understanding how mixins differ from Python's multiple inheritance will help you write more idiomatic Ruby code.

**Key Similarities:**
- Both allow a class to inherit behavior from multiple sources
- Both enable code reuse across unrelated class hierarchies
- Both create a method resolution order (MRO)

**Key Differences:**
- Ruby modules cannot have instances (can't do `MyModule.new`)
- Ruby's method lookup is simpler and more predictable
- Ruby doesn't have the "diamond problem" in the same way Python does
- Mixins are explicitly for sharing behavior, not creating hierarchies

### Python Example

```python
class Animal:
    def __init__(self, name):
        self.name = name

    def breathe(self):
        return f"{self.name} breathes"

class Swimmable:
    def swim(self):
        return f"{self.name} swims"

class Flyable:
    def fly(self):
        return f"{self.name} flies"

# Multiple inheritance
class Duck(Animal, Swimmable, Flyable):
    pass

class Fish(Animal, Swimmable):
    pass

duck = Duck("Donald")
print(duck.breathe())  # Donald breathes
print(duck.swim())     # Donald swims
print(duck.fly())      # Donald flies

# Check method resolution order
print(Duck.__mro__)
# (<class 'Duck'>, <class 'Animal'>, <class 'Swimmable'>,
#  <class 'Flyable'>, <class 'object'>)
```

### Ruby Equivalent

```ruby
class Animal
  def initialize(name)
    @name = name
  end

  def breathe
    "#{@name} breathes"
  end

  protected

  attr_reader :name
end

module Swimmable
  def swim
    "#{name} swims"
  end
end

module Flyable
  def fly
    "#{name} flies"
  end
end

# Single inheritance + mixins
class Duck < Animal
  include Swimmable
  include Flyable
end

class Fish < Animal
  include Swimmable
end

duck = Duck.new("Donald")
puts duck.breathe  # Donald breathes
puts duck.swim     # Donald swims
puts duck.fly      # Donald flies

# Check method lookup chain
puts Duck.ancestors
# [Duck, Flyable, Swimmable, Animal, Object, Kernel, BasicObject]
```

### Explanation

Ruby takes a "single inheritance + mixins" approach. Each class can only inherit from one parent class (using `<`), but can include as many modules as needed. This makes the inheritance hierarchy clearer and more maintainable.

**Method lookup order in Ruby:**

1. The class itself
2. Modules included in the class (most recent first)
3. The parent class
4. Modules included in the parent class
5. And so on up the inheritance chain

**Key advantages of Ruby's approach:**

```ruby
# You can check if a class includes a module
puts Duck.include?(Swimmable)  # true
puts Duck.include?(Flyable)    # true

# You can see the full ancestor chain
puts Duck.ancestors
# [Duck, Flyable, Swimmable, Animal, Object, Kernel, BasicObject]

# Modules make intent clearer
class Penguin < Animal
  include Swimmable
  # Deliberately NOT including Flyable - penguins don't fly!
end
```

**When to use what:**

- **Inheritance (`<`):** Use for "is-a" relationships (Duck is an Animal)
- **Mixins (`include`):** Use for "has-ability" relationships (Duck has swimming ability)

**Common pattern - Composition over inheritance:**

```ruby
# Instead of deep inheritance hierarchies, use mixins
module Jsonable
  def to_json
    # Convert object to JSON
  end
end

module Cacheable
  def cache_key
    "#{self.class.name}:#{id}"
  end
end

class User < ActiveRecord::Base
  include Jsonable
  include Cacheable
end

class Product < ActiveRecord::Base
  include Jsonable
  include Cacheable
end
```

**Common pitfall:** Don't use modules as a substitute for proper inheritance. If there's a true "is-a" relationship, use inheritance. Mixins are for shared behaviors across unrelated classes.

---

## 10.5 Common Built-in Modules (Enumerable, Comparable)

Ruby includes several powerful built-in modules that you can mix into your own classes. The two most important are `Enumerable` and `Comparable`. Understanding these will help you write Ruby code that feels natural and integrates well with the language.

**Key Similarities:**
- Similar to Python's magic methods (`__lt__`, `__iter__`, etc.) for operator overloading
- Both provide rich functionality from simple interfaces

**Key Differences:**
- Ruby uses modules instead of individual magic methods
- Including a module gives you dozens of methods automatically
- More explicit about the contract your class must fulfill

### Python Example

```python
# Python uses magic methods
from functools import total_ordering

@total_ordering
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __eq__(self, other):
        return self.age == other.age

    def __lt__(self, other):
        return self.age < other.age

    def __iter__(self):
        return iter([self.name, self.age])

# Now you can compare and iterate
person1 = Person("Alice", 30)
person2 = Person("Bob", 25)

print(person1 > person2)  # True
print(person1 >= person2) # True
print(list(person1))      # ['Alice', 30]
```

### Ruby Equivalent

```ruby
class Person
  include Comparable  # Mix in comparison operators

  attr_reader :name, :age

  def initialize(name, age)
    @name = name
    @age = age
  end

  # Comparable requires <=> (spaceship operator)
  def <=>(other)
    @age <=> other.age
  end

  # For Enumerable, define each
  def each
    yield @name
    yield @age
  end
end

# Now you can compare
person1 = Person.new("Alice", 30)
person2 = Person.new("Bob", 25)

puts person1 > person2   # true
puts person1 >= person2  # true
puts person1 == person2  # false
puts person1.between?(person2, person1)  # true

# If we include Enumerable:
class Person
  include Comparable
  include Enumerable  # Now we get map, select, reduce, etc.

  # ... rest of the code ...
end

person = Person.new("Alice", 30)
puts person.map(&:upcase)     # ["ALICE", "30"]
puts person.select { |x| x.is_a?(String) }  # ["Alice"]
```

### Explanation

**The `Comparable` module:**

When you `include Comparable` and define the `<=>` (spaceship) operator, you automatically get:
- `<` (less than)
- `<=` (less than or equal)
- `==` (equal)
- `>=` (greater than or equal)
- `>` (greater than)
- `between?(min, max)`

The `<=>` operator should return:
- `-1` if self is less than other
- `0` if self equals other
- `1` if self is greater than other
- `nil` if not comparable

```ruby
class Temperature
  include Comparable

  attr_reader :celsius

  def initialize(celsius)
    @celsius = celsius
  end

  def <=>(other)
    @celsius <=> other.celsius
  end
end

freezing = Temperature.new(0)
boiling = Temperature.new(100)
room_temp = Temperature.new(20)

puts boiling > freezing        # true
puts room_temp.between?(freezing, boiling)  # true
```

**The `Enumerable` module:**

When you `include Enumerable` and define the `each` method, you automatically get methods like:
- `map`, `select`, `reject`, `reduce`
- `find`, `find_all`, `any?`, `all?`, `none?`
- `sort`, `sort_by`, `min`, `max`, `min_by`, `max_by`
- `group_by`, `partition`, `take`, `drop`
- And many more!

```ruby
class Playlist
  include Enumerable

  def initialize
    @songs = []
  end

  def add_song(song)
    @songs << song
  end

  # Enumerable requires each
  def each(&block)
    @songs.each(&block)
  end
end

playlist = Playlist.new
playlist.add_song("Song A")
playlist.add_song("Song B")
playlist.add_song("Song C")

# Now we get all Enumerable methods for free!
puts playlist.map(&:upcase)
puts playlist.select { |s| s.include?("A") }
puts playlist.count  # 3
```

**Important points:**
- `Comparable` needs `<=>`
- `Enumerable` needs `each`
- These modules demonstrate Ruby's philosophy: define a simple interface, get rich functionality
- You've already been using `Enumerable` - Arrays and Hashes include it!

**Checking what's included:**

```ruby
puts Array.include?(Enumerable)   # true
puts Hash.include?(Enumerable)    # true
puts Range.include?(Enumerable)   # true
```

---

## 10.6 `prepend`

In addition to `include` and `extend`, Ruby has a third way to mix in modules: `prepend`. This is a more advanced feature that changes where the module sits in the method lookup chain, and it's particularly useful for wrapping or modifying existing behavior.

**Key Similarities:**
- Python's method resolution order (MRO) determines lookup order
- Both languages search parent classes/mixins for methods

**Key Differences:**
- `prepend` puts the module *before* the class in the lookup chain
- Python doesn't have an exact equivalent to `prepend`
- `prepend` is specifically designed for method wrapping and decoration

### Python Example

```python
# Python uses method resolution order (MRO)
class Logger:
    def process(self, data):
        print(f"Logging: {data}")
        return super().process(data)

class DataProcessor:
    def process(self, data):
        return data.upper()

class LoggedProcessor(Logger, DataProcessor):
    pass

# Logger comes first, so its process() wraps DataProcessor's
processor = LoggedProcessor()
result = processor.process("hello")
# Output: Logging: hello
# Returns: HELLO
```

### Ruby Equivalent

```ruby
# Without prepend - using include (doesn't work as expected)
module Logger
  def process(data)
    puts "Logging: #{data}"
    super(data)  # Call the original method
  end
end

class DataProcessor
  def process(data)
    data.upcase
  end
end

class IncludedProcessor < DataProcessor
  include Logger  # include puts Logger AFTER DataProcessor
end

# This won't work! Logger's process won't be called
# processor = IncludedProcessor.new
# processor.process("hello")  # Just returns "HELLO", no logging

# With prepend - it works!
class PrependedProcessor < DataProcessor
  prepend Logger  # prepend puts Logger BEFORE DataProcessor
end

processor = PrependedProcessor.new
result = processor.process("hello")
# Output: Logging: hello
# Returns: HELLO

# Check the ancestors
puts PrependedProcessor.ancestors
# [Logger, PrependedProcessor, DataProcessor, Object, Kernel, BasicObject]
```

### Explanation

The key difference between `include`, `extend`, and `prepend`:

- **`include`**: Inserts the module *after* the class in the method lookup chain
- **`extend`**: Adds module methods as class methods (not in the instance lookup chain)
- **`prepend`**: Inserts the module *before* the class in the method lookup chain

**Method lookup with `include`:**
```ruby
module A
  def greet
    "From A"
  end
end

class MyClass
  include A

  def greet
    "From MyClass"
  end
end

puts MyClass.new.greet
# "From MyClass" - class method wins because class comes before module
puts MyClass.ancestors
# [MyClass, A, Object, Kernel, BasicObject]
```

**Method lookup with `prepend`:**
```ruby
module A
  def greet
    "From A"
  end
end

class MyClass
  prepend A  # Module comes FIRST

  def greet
    "From MyClass"
  end
end

puts MyClass.new.greet
# "From A" - module method wins because module comes before class
puts MyClass.ancestors
# [A, MyClass, Object, Kernel, BasicObject]
```

**Practical use case - Method wrapping:**

```ruby
module Benchmarking
  def perform(task)
    start_time = Time.now
    result = super(task)  # Call the original method
    end_time = Time.now
    puts "Task took #{end_time - start_time} seconds"
    result
  end
end

class TaskRunner
  prepend Benchmarking

  def perform(task)
    puts "Running: #{task}"
    sleep(0.5)  # Simulate work
    "Completed: #{task}"
  end
end

runner = TaskRunner.new
result = runner.perform("data processing")
# Output:
# Running: data processing
# Task took 0.500123 seconds
# Returns: "Completed: data processing"
```

**Another use case - Validation:**

```ruby
module Validation
  def save
    if valid?
      super  # Call original save method
    else
      puts "Validation failed!"
      false
    end
  end
end

class User
  prepend Validation

  attr_accessor :name, :email

  def save
    puts "Saving user: #{name}"
    true
  end

  def valid?
    !name.nil? && !email.nil?
  end
end

user = User.new
user.name = "Alice"
user.save
# Output: Validation failed!
# Returns: false

user.email = "alice@example.com"
user.save
# Output: Saving user: Alice
# Returns: true
```

**Important points:**
- `prepend` is less common than `include`, but very powerful for wrapping behavior
- When using `super` in a prepended module, it calls the original method
- `prepend` is ideal for cross-cutting concerns (logging, benchmarking, validation)
- The module must use `super` to call the original method, otherwise it completely replaces it

**Quick reference:**
```ruby
module MyModule
  def my_method
    "from module"
  end
end

class MyClass
  def my_method
    "from class"
  end
end

# include: module after class
class A < MyClass
  include MyModule
end
puts A.new.my_method  # "from class"

# prepend: module before class
class B < MyClass
  prepend MyModule
end
puts B.new.my_method  # "from module"

# extend: module methods become class methods
class C < MyClass
  extend MyModule
end
puts C.my_method      # "from module"
# C.new.my_method     # Error!
```

**Common pitfall:** Forgetting to call `super` in a prepended module means you completely replace the original method instead of wrapping it.

---

This chapter covered modules and mixins, one of Ruby's most distinctive features. You learned:

- How modules differ from Python's file-based modules
- Using modules for namespacing
- Using modules as mixins with `include` and `extend`
- How mixins compare to Python's multiple inheritance
- The power of built-in modules like `Enumerable` and `Comparable`
- Advanced module inclusion with `prepend`

Modules are central to idiomatic Ruby code. They enable code reuse, provide elegant solutions to cross-cutting concerns, and help you write more maintainable applications. As you work with Ruby, you'll find modules everywhere - from the standard library to popular gems to the code you write yourself.
