# Chapter 9: Classes and Object-Oriented Programming

## 9.1 Defining Classes

Both Ruby and Python are object-oriented languages with similar class concepts. However, Ruby's approach to OOP is more consistent - everything is an object, and the syntax reflects Ruby's philosophy of developer happiness.

**Key Similarities:**
- Both use the `class` keyword to define classes
- Both support instance variables and methods
- Both support inheritance
- Both create instances with `ClassName.new()` / `ClassName()`

**Key Differences:**
- Ruby uses `end` to close class definitions
- Ruby doesn't require explicit `self` in method parameters
- Ruby uses `@` prefix for instance variables
- Ruby uses `initialize` instead of `__init__`
- Ruby's class definitions are executable code

### Python Example

```python
# Basic class definition
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def greet(self):
        return f"Hello, I'm {self.name}"

    def birthday(self):
        self.age += 1

# Creating instances
alice = Person("Alice", 30)
bob = Person("Bob", 25)

# Calling methods
print(alice.greet())  # Hello, I'm Alice
alice.birthday()
print(alice.age)      # 31

# Class with class variables
class Counter:
    count = 0  # Class variable

    def __init__(self):
        Counter.count += 1
        self.instance_id = Counter.count

c1 = Counter()
c2 = Counter()
print(Counter.count)  # 2

# Empty class
class EmptyClass:
    pass

# Class with only methods (no __init__)
class Calculator:
    def add(self, a, b):
        return a + b
```

### Ruby Equivalent

```ruby
# Basic class definition
class Person
  def initialize(name, age)
    @name = name
    @age = age
  end

  def greet
    "Hello, I'm #{@name}"
  end

  def birthday
    @age += 1
  end
end

# Creating instances
alice = Person.new("Alice", 30)
bob = Person.new("Bob", 25)

# Calling methods
puts alice.greet  # Hello, I'm Alice
alice.birthday
puts alice.age    # Error! No reader defined

# Class with class variables
class Counter
  @@count = 0  # Class variable

  def initialize
    @@count += 1
    @instance_id = @@count
  end

  def self.count
    @@count
  end
end

c1 = Counter.new
c2 = Counter.new
puts Counter.count  # 2

# Empty class
class EmptyClass
end

# Class with only methods (no initialize)
class Calculator
  def add(a, b)
    a + b
  end
end
```

### Explanation

Ruby classes are defined with the `class` keyword and ended with `end`. The class body contains method definitions, and instance variables are prefixed with `@`.

**Basic class structure:**
```ruby
class ClassName
  # Constructor
  def initialize(parameters)
    @instance_variable = value
  end

  # Instance methods
  def method_name
    # method body
  end
end
```

**Creating instances:**
```ruby
# Ruby uses .new to create instances
instance = ClassName.new(arguments)

# Not ClassName(arguments) like Python
```

**Key differences from Python:**

1. **No explicit `self` parameter:**
```ruby
# Python
def greet(self):
    return self.name

# Ruby
def greet
  @name  # self is implicit
end
```

2. **Instance variables use `@` prefix:**
```ruby
# Python
self.name = name

# Ruby
@name = name
```

3. **Class definitions are executable:**
```ruby
class DynamicClass
  puts "This runs when the class is defined!"

  # Can run any code at class definition time
  @class_creation_time = Time.now

  def initialize
    puts "Instance created"
  end
end
# Prints: "This runs when the class is defined!"

instance = DynamicClass.new
# Prints: "Instance created"
```

4. **Everything is an object, including classes:**
```ruby
class User
end

User.class           # Class
User.is_a?(Object)   # true
User.methods         # Array of methods

# Classes are objects too
User.instance_of?(Class)  # true
```

**Reopening classes:**
Ruby allows you to reopen and modify existing classes:
```ruby
class Person
  def initialize(name)
    @name = name
  end
end

# Later, reopen the class
class Person
  def greet
    "Hello, I'm #{@name}"
  end
end

# Both methods are now available
person = Person.new("Alice")
person.greet  # "Hello, I'm Alice"
```

**Namespacing with modules:**
```ruby
module MyApp
  class User
    def initialize(name)
      @name = name
    end
  end
end

# Access with ::
user = MyApp::User.new("Alice")
```

**Best Practice:**
- Use PascalCase for class names
- Use snake_case for method and variable names
- Keep classes focused (Single Responsibility Principle)
- Initialize instance variables in `initialize`
- Use modules for namespacing

---

## 9.2 `initialize` vs `__init__`

The constructor method in Ruby is called `initialize`, while Python uses `__init__`. Both serve the same purpose: setting up a new instance of a class.

**Key Similarities:**
- Both are called automatically when creating an instance
- Both set up instance variables
- Both can take parameters
- Both are instance methods

**Key Differences:**
- Ruby: `initialize` (normal method name)
- Python: `__init__` (dunder method)
- Ruby: no need for `self` parameter
- Ruby: automatically returns the new instance
- Ruby: `initialize` is always private

### Python Example

```python
# Basic constructor
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

user = User("Alice", "alice@example.com")

# Constructor with default values
class User:
    def __init__(self, name, email, active=True):
        self.name = name
        self.email = email
        self.active = active

user = User("Alice", "alice@example.com")

# Constructor with validation
class User:
    def __init__(self, name, email):
        if not email or "@" not in email:
            raise ValueError("Invalid email")
        self.name = name
        self.email = email

# Constructor with computed values
class Circle:
    def __init__(self, radius):
        self.radius = radius
        self.diameter = radius * 2
        self.area = 3.14159 * radius ** 2

# Constructor calling other methods
class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email
        self._setup_defaults()

    def _setup_defaults(self):
        self.active = True
        self.created_at = datetime.now()

# No constructor (rare but valid)
class EmptyClass:
    pass  # Uses default __init__

# Constructor with *args and **kwargs
class FlexibleClass:
    def __init__(self, *args, **kwargs):
        self.args = args
        self.kwargs = kwargs
```

### Ruby Equivalent

```ruby
# Basic constructor
class User
  def initialize(name, email)
    @name = name
    @email = email
  end
end

user = User.new("Alice", "alice@example.com")

# Constructor with default values
class User
  def initialize(name, email, active = true)
    @name = name
    @email = email
    @active = active
  end
end

user = User.new("Alice", "alice@example.com")

# Constructor with validation
class User
  def initialize(name, email)
    raise ArgumentError, "Invalid email" if email.nil? || !email.include?("@")
    @name = name
    @email = email
  end
end

# Constructor with computed values
class Circle
  def initialize(radius)
    @radius = radius
    @diameter = radius * 2
    @area = Math::PI * radius ** 2
  end
end

# Constructor calling other methods
class User
  def initialize(name, email)
    @name = name
    @email = email
    setup_defaults
  end

  private

  def setup_defaults
    @active = true
    @created_at = Time.now
  end
end

# No constructor (valid)
class EmptyClass
  # Ruby provides a default initialize
end

# Constructor with splat operators
class FlexibleClass
  def initialize(*args, **kwargs)
    @args = args
    @kwargs = kwargs
  end
end
```

### Explanation

Ruby's `initialize` method is automatically called when you use `ClassName.new`. It's always private and automatically returns the new instance.

**How it works:**
```ruby
class User
  def initialize(name)
    @name = name
    puts "User created!"
  end
end

user = User.new("Alice")
# Prints: "User created!"
# Returns: the User instance

# You can't call initialize directly:
# user.initialize("Bob")  # NoMethodError: private method
```

**Parameters work the same as regular methods:**
```ruby
# Positional parameters
def initialize(name, age)
  @name = name
  @age = age
end

# Default parameters
def initialize(name, age = 18)
  @name = name
  @age = age
end

# Keyword parameters
def initialize(name:, age: 18)
  @name = name
  @age = age
end

User.new(name: "Alice", age: 30)

# Splat parameters
def initialize(*names)
  @names = names
end

User.new("Alice", "Bob", "Charlie")
```

**Initialize doesn't need explicit return:**
```ruby
class User
  def initialize(name)
    @name = name
    # No need to return self
  end
end

user = User.new("Alice")  # Returns the User instance automatically
```

**Validation in initialize:**
```ruby
class User
  def initialize(name, age)
    raise ArgumentError, "Name cannot be empty" if name.to_s.empty?
    raise ArgumentError, "Age must be positive" if age <= 0

    @name = name
    @age = age
  end
end

# user = User.new("", 25)   # ArgumentError: Name cannot be empty
# user = User.new("Alice", -5)  # ArgumentError: Age must be positive
```

**Setting default values:**
```ruby
class User
  def initialize(name, options = {})
    @name = name
    @email = options[:email]
    @active = options.fetch(:active, true)  # Default to true
    @role = options[:role] || "user"
  end
end

user = User.new("Alice", email: "alice@example.com")
# @active is true (default)
# @role is "user" (default)
```

**Using keyword arguments (modern Ruby):**
```ruby
class User
  def initialize(name:, email:, active: true, role: "user")
    @name = name
    @email = email
    @active = active
    @role = role
  end
end

user = User.new(name: "Alice", email: "alice@example.com")
# Very clear what each parameter is
```

**Calling other methods from initialize:**
```ruby
class User
  def initialize(name, email)
    @name = name
    @email = email
    normalize_name
    validate!
  end

  private

  def normalize_name
    @name = @name.strip.capitalize
  end

  def validate!
    raise "Invalid email" unless @email.include?("@")
  end
end
```

**Factory pattern (alternative constructors):**
```ruby
class User
  def initialize(name, email)
    @name = name
    @email = email
  end

  # Class method as alternative constructor
  def self.from_hash(hash)
    new(hash[:name], hash[:email])
  end

  def self.guest
    new("Guest", "guest@example.com")
  end
end

user1 = User.new("Alice", "alice@example.com")
user2 = User.from_hash({name: "Bob", email: "bob@example.com"})
user3 = User.guest
```

**Best Practice:**
- Keep `initialize` simple and focused
- Validate inputs in `initialize`
- Use keyword arguments for clarity (3+ parameters)
- Set all instance variables in `initialize`
- Call private methods for complex setup logic
- Raise exceptions for invalid inputs
- Use class methods for alternative constructors

---

## 9.3 Instance Variables (`@variable`)

Instance variables in Ruby are prefixed with `@` and belong to a specific instance of a class. Unlike Python's `self.variable`, Ruby's instance variables don't need to be explicitly scoped to `self`.

**Key Similarities:**
- Both store data specific to an instance
- Both persist for the lifetime of the instance
- Both can be accessed from any instance method

**Key Differences:**
- Ruby uses `@` prefix; Python uses `self.`
- Ruby instance variables are private by default
- Ruby requires explicit accessor methods to read/write from outside
- Ruby instance variables spring into existence when first assigned

### Python Example

```python
# Instance variables with self
class User:
    def __init__(self, name, age):
        self.name = name    # Instance variable
        self.age = age      # Instance variable
        self._private = "private"  # Convention only

    def greet(self):
        return f"Hi, I'm {self.name}"

    def birthday(self):
        self.age += 1

# Access from outside
user = User("Alice", 30)
print(user.name)      # "Alice" - directly accessible
user.name = "Alicia"  # Directly modifiable
print(user.name)      # "Alicia"

# All instance variables are accessible
print(user.age)       # 30
print(user._private)  # "private" - underscore is just convention

# Adding instance variables dynamically
user.email = "alice@example.com"
print(user.email)     # Works in Python

# Checking if instance variable exists
hasattr(user, 'name')    # True
hasattr(user, 'missing')  # False

# Getting all instance variables
vars(user)  # {'name': 'Alicia', 'age': 31, '_private': 'private', ...}
```

### Ruby Equivalent

```ruby
# Instance variables with @ prefix
class User
  def initialize(name, age)
    @name = name    # Instance variable
    @age = age      # Instance variable
    @private = "private"
  end

  def greet
    "Hi, I'm #{@name}"
  end

  def birthday
    @age += 1
  end
end

# Instance variables are NOT accessible from outside by default
user = User.new("Alice", 30)
# puts user.name      # NoMethodError: undefined method `name'
# user.name = "Alicia"  # NoMethodError: undefined method `name='

# Must define accessor methods
class User
  def initialize(name, age)
    @name = name
    @age = age
  end

  # Getter method
  def name
    @name
  end

  # Setter method
  def name=(value)
    @name = value
  end
end

user = User.new("Alice", 30)
puts user.name        # "Alice" - uses getter method
user.name = "Alicia"  # Uses setter method
puts user.name        # "Alicia"

# Instance variables without accessors are truly private
class User
  def initialize(name)
    @name = name
    @secret = "hidden"
  end

  def name
    @name
  end
end

user = User.new("Alice")
puts user.name      # "Alice" - accessor defined
# puts user.secret  # NoMethodError - no accessor

# Instance variables spring into existence on assignment
class User
  def initialize(name)
    @name = name
  end

  def set_email(email)
    @email = email  # Created here
  end

  def email
    @email  # Will be nil if never set
  end
end

user = User.new("Alice")
puts user.email.inspect  # nil
user.set_email("alice@example.com")
puts user.email          # "alice@example.com"

# Checking if instance variable exists
user.instance_variable_defined?(:@name)   # true
user.instance_variable_defined?(:@missing) # false

# Getting instance variable value (avoid in normal code)
user.instance_variable_get(:@name)  # "Alice"

# Setting instance variable value (avoid in normal code)
user.instance_variable_set(:@name, "Bob")

# Getting all instance variables
user.instance_variables  # [:@name, :@email]
```

### Explanation

Ruby's instance variables are always private to the object. They can only be accessed from within instance methods, never directly from outside the class.

**Declaring instance variables:**
```ruby
# Instance variables don't need declaration
# They're created on first assignment

class User
  def initialize(name)
    @name = name  # Created here
  end

  def add_email(email)
    @email = email  # Created here
  end
end
```

**Instance variables are scoped to the instance:**
```ruby
class Counter
  def initialize
    @count = 0
  end

  def increment
    @count += 1
  end

  def count
    @count
  end
end

c1 = Counter.new
c2 = Counter.new

c1.increment
c1.increment
puts c1.count  # 2
puts c2.count  # 0 - separate instance
```

**Uninitialized instance variables are `nil`:**
```ruby
class User
  def initialize(name)
    @name = name
    # @age not set
  end

  def age
    @age  # Returns nil
  end

  def age_or_unknown
    @age || "unknown"
  end
end

user = User.new("Alice")
puts user.age.inspect       # nil
puts user.age_or_unknown    # "unknown"
```

**Instance variables vs local variables:**
```ruby
class Example
  def initialize
    @instance_var = "instance"  # Instance variable (persists)
    local_var = "local"          # Local variable (disappears)
  end

  def check
    puts @instance_var  # "instance" - still accessible
    # puts local_var    # Error: undefined local variable
  end
end

example = Example.new
example.check
```

**Common pattern - lazy initialization:**
```ruby
class User
  def initialize(name)
    @name = name
  end

  def email
    @email ||= fetch_email_from_database  # Set only once
  end

  private

  def fetch_email_from_database
    # Expensive operation
    "#{@name.downcase}@example.com"
  end
end

user = User.new("Alice")
puts user.email  # Calls fetch_email_from_database
puts user.email  # Uses cached @email
```

**Instance variables in different methods:**
```ruby
class User
  def initialize(name)
    @name = name
  end

  def greet
    "Hello, #{@name}"  # Accessible in any instance method
  end

  def formal_greet
    "Good day, #{@name}"  # Same @name
  end
end
```

**Naming conventions:**
```ruby
class User
  def initialize(name)
    @name = name           # Regular instance variable
    @_internal = "data"    # Internal (by convention)
    @is_active = true      # Boolean
  end
end
```

**Instance variables are NOT inherited visibility:**
```ruby
class Parent
  def initialize
    @value = "parent"
  end
end

class Child < Parent
  def show_value
    @value  # Accessible in subclass
  end
end

child = Child.new
puts child.show_value  # "parent"
```

**Avoid direct access (use instance_variable_get/set):**
```ruby
class User
  def initialize(name)
    @name = name
  end
end

user = User.new("Alice")

# Avoid these in production code:
user.instance_variable_get(:@name)        # "Alice"
user.instance_variable_set(:@name, "Bob")
user.instance_variable_defined?(:@name)   # true

# Use proper accessor methods instead (covered in 9.5)
```

**Best Practice:**
- Always prefix instance variables with `@`
- Initialize instance variables in `initialize` method
- Use accessor methods (attr_reader, etc.) for external access
- Uninitialized instance variables default to `nil`
- Use `@variable ||= value` for lazy initialization
- Don't use `instance_variable_get/set` in production code
- Keep instance variables private unless explicitly exposed

---

## 9.4 Instance Methods

Instance methods are methods that belong to instances of a class and can access instance variables. They work similarly in both Ruby and Python, with some syntactic differences.

**Key Similarities:**
- Both define behavior for instances
- Both can access instance variables
- Both can call other instance methods
- Both can have parameters and return values

**Key Differences:**
- Ruby doesn't require `self` as first parameter
- Ruby uses implicit returns
- Ruby allows `?` and `!` in method names
- Ruby's access control is different

### Python Example

```python
# Instance methods with self
class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    # Instance method
    def greet(self):
        return f"Hello, I'm {self.name}"

    # Instance method with parameters
    def set_age(self, age):
        if age < 0:
            raise ValueError("Age must be positive")
        self.age = age

    # Instance method calling another instance method
    def birthday(self):
        self.set_age(self.age + 1)

    # Boolean check method
    def is_adult(self):
        return self.age >= 18

    # Method modifying state
    def activate(self):
        self.active = True

    # Method with default parameters
    def greet_formal(self, title="Mr/Ms"):
        return f"Hello, {title} {self.name}"

user = User("Alice", 30)
print(user.greet())           # "Hello, I'm Alice"
print(user.is_adult())        # True
user.birthday()
print(user.age)               # 31
print(user.greet_formal("Dr"))  # "Hello, Dr Alice"
```

### Ruby Equivalent

```ruby
# Instance methods without explicit self
class User
  def initialize(name, age)
    @name = name
    @age = age
  end

  # Instance method
  def greet
    "Hello, I'm #{@name}"
  end

  # Instance method with parameters
  def set_age(age)
    raise ArgumentError, "Age must be positive" if age < 0
    @age = age
  end

  # Instance method calling another instance method
  def birthday
    set_age(@age + 1)
  end

  # Boolean check method (? convention)
  def adult?
    @age >= 18
  end

  # Method modifying state (! convention)
  def activate!
    @active = true
  end

  # Method with default parameters
  def greet_formal(title = "Mr/Ms")
    "Hello, #{title} #{@name}"
  end
end

user = User.new("Alice", 30)
puts user.greet              # "Hello, I'm Alice"
puts user.adult?             # true
user.birthday
puts user.age                # Error - no getter defined
puts user.greet_formal("Dr") # "Hello, Dr Alice"
```

### Explanation

Instance methods in Ruby are defined within the class and automatically have access to instance variables and other instance methods.

**Basic instance method:**
```ruby
class User
  def initialize(name)
    @name = name
  end

  def greet
    "Hello, #{@name}"  # Access instance variable
  end
end
```

**Calling other instance methods:**
```ruby
class Calculator
  def initialize(value)
    @value = value
  end

  def double
    multiply(2)  # Call another instance method
  end

  def triple
    multiply(3)
  end

  private

  def multiply(factor)
    @value * factor
  end
end

calc = Calculator.new(5)
puts calc.double  # 10
puts calc.triple  # 15
```

**Using `self` explicitly (when needed):**
```ruby
class User
  def initialize(name)
    @name = name
  end

  # Getter
  def name
    @name
  end

  # Setter
  def name=(value)
    @name = value
  end

  def update_name(new_name)
    # Need self to call the setter method
    self.name = new_name
    # @name = new_name would also work
  end

  def compare_with(other)
    self.name == other.name  # self.name calls the getter
  end
end
```

**Methods with multiple parameters:**
```ruby
class Rectangle
  def initialize(width, height)
    @width = width
    @height = height
  end

  def area
    @width * @height
  end

  def resize(new_width, new_height)
    @width = new_width
    @height = new_height
  end

  def scale(factor)
    @width *= factor
    @height *= factor
  end
end

rect = Rectangle.new(10, 5)
puts rect.area      # 50
rect.scale(2)
puts rect.area      # 200
```

**Predicate methods (ending with `?`):**
```ruby
class User
  def initialize(name, age, active)
    @name = name
    @age = age
    @active = active
  end

  def adult?
    @age >= 18
  end

  def active?
    @active
  end

  def teenager?
    @age >= 13 && @age < 20
  end
end

user = User.new("Alice", 16, true)
puts user.adult?     # false
puts user.active?    # true
puts user.teenager?  # true
```

**Bang methods (ending with `!`):**
```ruby
class User
  def initialize(name)
    @name = name
    @active = false
  end

  # Safe version - returns boolean
  def activate
    return false if @active
    @active = true
    true
  end

  # Bang version - raises error if already active
  def activate!
    raise "Already active" if @active
    @active = true
  end

  # Mutating method
  def normalize_name!
    @name = @name.strip.capitalize
  end
end
```

**Methods that return self (for chaining):**
```ruby
class Query
  def initialize
    @conditions = []
    @order = nil
    @limit = nil
  end

  def where(condition)
    @conditions << condition
    self  # Return self for chaining
  end

  def order(field)
    @order = field
    self
  end

  def limit(count)
    @limit = count
    self
  end

  def to_s
    "Query: #{@conditions.join(' AND ')}"
  end
end

query = Query.new
  .where("age > 18")
  .where("active = true")
  .order("name")
  .limit(10)

puts query.to_s
```

**Methods with keyword arguments:**
```ruby
class User
  def initialize(name:, age:, email: nil, active: true)
    @name = name
    @age = age
    @email = email
    @active = active
  end

  def update(name: nil, age: nil, email: nil)
    @name = name if name
    @age = age if age
    @email = email if email
  end
end

user = User.new(name: "Alice", age: 30)
user.update(email: "alice@example.com")
```

**Methods with blocks:**
```ruby
class Collection
  def initialize(items)
    @items = items
  end

  def each_with_greeting
    @items.each do |item|
      yield("Hello, #{item}")
    end
  end
end

collection = Collection.new(["Alice", "Bob"])
collection.each_with_greeting do |greeting|
  puts greeting
end
# Hello, Alice
# Hello, Bob
```

**Best Practice:**
- Keep methods short and focused
- Use `?` suffix for boolean-returning methods
- Use `!` suffix for dangerous/mutating methods
- Return `self` for chainable methods
- Use descriptive method names
- Limit parameters (use keyword arguments for many parameters)
- Use implicit returns for simple methods
- Make methods either queries (return data) or commands (change state), not both

---

## 9.5 `attr_reader`, `attr_writer`, `attr_accessor`

Writing getter and setter methods for instance variables is so common that Ruby provides shortcuts: `attr_reader`, `attr_writer`, and `attr_accessor`. These are much more concise than Python's `@property` decorators.

**Key Similarities:**
- Both provide controlled access to instance data
- Both generate methods automatically
- Both allow customization when needed

**Key Differences:**
- Ruby's approach is more concise
- Ruby uses class macros, Python uses decorators
- Ruby separates readers and writers clearly
- Ruby's version is more commonly used

### Python Example

```python
# Manual getters and setters
class User:
    def __init__(self, name, age):
        self._name = name
        self._age = age

    def get_name(self):
        return self._name

    def set_name(self, name):
        self._name = name

    def get_age(self):
        return self._age

    def set_age(self, age):
        if age < 0:
            raise ValueError("Age must be positive")
        self._age = age

# Using @property decorator
class User:
    def __init__(self, name, age):
        self._name = name
        self._age = age

    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        self._name = value

    @property
    def age(self):
        return self._age

    @age.setter
    def age(self, value):
        if value < 0:
            raise ValueError("Age must be positive")
        self._age = value

user = User("Alice", 30)
print(user.name)    # "Alice" - uses getter
user.name = "Bob"   # Uses setter
print(user.age)     # 30

# Read-only property
class Circle:
    def __init__(self, radius):
        self._radius = radius

    @property
    def radius(self):
        return self._radius

    @property
    def area(self):
        return 3.14159 * self._radius ** 2

circle = Circle(5)
print(circle.radius)  # 5
print(circle.area)    # 78.53975
# circle.area = 100   # AttributeError: can't set attribute
```

### Ruby Equivalent

```ruby
# Manual getters and setters (verbose)
class User
  def initialize(name, age)
    @name = name
    @age = age
  end

  def name
    @name
  end

  def name=(value)
    @name = value
  end

  def age
    @age
  end

  def age=(value)
    raise ArgumentError, "Age must be positive" if value < 0
    @age = value
  end
end

# Using attr_accessor (concise)
class User
  attr_accessor :name   # Creates getter and setter

  def initialize(name, age)
    @name = name
    @age = age
  end

  def age
    @age
  end

  def age=(value)
    raise ArgumentError, "Age must be positive" if value < 0
    @age = value
  end
end

user = User.new("Alice", 30)
puts user.name       # "Alice" - uses getter
user.name = "Bob"    # Uses setter
puts user.age        # 30

# attr_reader - read-only
class Circle
  attr_reader :radius  # Only creates getter

  def initialize(radius)
    @radius = radius
  end

  def area
    Math::PI * @radius ** 2
  end
end

circle = Circle.new(5)
puts circle.radius   # 5
puts circle.area     # 78.53981633974483
# circle.radius = 10 # NoMethodError: undefined method `radius='

# attr_writer - write-only (rare)
class SecretHolder
  attr_writer :secret  # Only creates setter

  def initialize
    @secret = nil
  end

  def reveal
    "The secret is: #{@secret}"
  end
end

holder = SecretHolder.new
holder.secret = "password123"
puts holder.reveal   # "The secret is: password123"
# puts holder.secret # NoMethodError: undefined method `secret'

# Multiple attributes at once
class User
  attr_accessor :name, :email
  attr_reader :id, :created_at
  attr_writer :password

  def initialize(name, email)
    @name = name
    @email = email
    @id = generate_id
    @created_at = Time.now
  end

  private

  def generate_id
    rand(1000..9999)
  end
end
```

### Explanation

Ruby's attribute accessors are class methods that generate getter and/or setter methods for you. They're called at class definition time and create the methods automatically.

**attr_reader - creates getter only:**
```ruby
class User
  attr_reader :name  # Creates: def name; @name; end

  def initialize(name)
    @name = name
  end
end

user = User.new("Alice")
puts user.name       # "Alice"
# user.name = "Bob"  # NoMethodError
```

**attr_writer - creates setter only:**
```ruby
class User
  attr_writer :name  # Creates: def name=(value); @name = value; end

  def initialize(name)
    @name = name
  end

  def display_name
    @name
  end
end

user = User.new("Alice")
user.name = "Bob"    # Works
puts user.display_name  # "Bob"
# puts user.name     # NoMethodError
```

**attr_accessor - creates both getter and setter:**
```ruby
class User
  attr_accessor :name  # Creates both getter and setter

  def initialize(name)
    @name = name
  end
end

user = User.new("Alice")
puts user.name       # "Alice" - getter
user.name = "Bob"    # Setter
puts user.name       # "Bob"
```

**Multiple attributes:**
```ruby
class User
  attr_accessor :name, :email, :phone
  attr_reader :id, :created_at

  def initialize(name, email)
    @name = name
    @email = email
    @id = rand(1000..9999)
    @created_at = Time.now
  end
end
```

**Custom getter with attr_writer:**
```ruby
class User
  attr_writer :name  # Auto setter

  def initialize(name)
    @name = name
  end

  # Custom getter
  def name
    @name.upcase
  end
end

user = User.new("alice")
puts user.name       # "ALICE"
user.name = "bob"
puts user.name       # "BOB"
```

**Custom setter with attr_reader:**
```ruby
class User
  attr_reader :age  # Auto getter

  def initialize(age)
    @age = age
  end

  # Custom setter with validation
  def age=(value)
    raise ArgumentError, "Age must be positive" if value < 0
    @age = value
  end
end

user = User.new(30)
puts user.age        # 30
user.age = 31        # Works
# user.age = -5      # ArgumentError
```

**Virtual attributes (computed):**
```ruby
class Person
  attr_accessor :first_name, :last_name

  def initialize(first_name, last_name)
    @first_name = first_name
    @last_name = last_name
  end

  # Virtual attribute - computed from other attributes
  def full_name
    "#{@first_name} #{@last_name}"
  end

  # Virtual attribute setter
  def full_name=(name)
    parts = name.split(' ', 2)
    @first_name = parts[0]
    @last_name = parts[1] || ''
  end
end

person = Person.new("Alice", "Smith")
puts person.full_name        # "Alice Smith"
person.full_name = "Bob Jones"
puts person.first_name       # "Bob"
puts person.last_name        # "Jones"
```

**Lazy-loaded attributes:**
```ruby
class User
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def profile
    @profile ||= load_profile
  end

  private

  def load_profile
    # Expensive operation
    { bio: "User bio", avatar: "avatar.jpg" }
  end
end
```

**Boolean attributes:**
```ruby
class User
  attr_accessor :name
  attr_reader :active  # Use custom methods for boolean

  def initialize(name, active = true)
    @name = name
    @active = active
  end

  # Predicate method
  def active?
    @active
  end

  # Setter for boolean
  def active=(value)
    @active = !!value  # Convert to boolean
  end
end

user = User.new("Alice")
puts user.active?      # true
user.active = false
puts user.active?      # false
```

**Best Practice:**
- Use `attr_reader` for read-only attributes
- Use `attr_accessor` for attributes that can be read and written
- Use `attr_writer` rarely (usually for write-only attributes)
- Customize getters/setters when you need validation or transformation
- Use predicate methods (`?`) for boolean attributes
- Keep attribute methods simple
- Use virtual attributes for computed values
- Initialize all instance variables in `initialize`

**Quick reference:**
```ruby
class Example
  attr_reader :read_only       # name
  attr_writer :write_only      # name=
  attr_accessor :read_write    # name and name=

  # Is equivalent to:

  def read_only
    @read_only
  end

  def write_only=(value)
    @write_only = value
  end

  def read_write
    @read_write
  end

  def read_write=(value)
    @read_write = value
  end
end
```

---

## 9.6 Class Variables (`@@variable`) vs Class Attributes

Class variables in Ruby use the `@@` prefix and are shared across all instances of a class and its subclasses. They're different from Python's class attributes and have some gotchas.

**Key Similarities:**
- Both are shared across all instances
- Both belong to the class, not instances
- Both can be accessed from instance methods

**Key Differences:**
- Ruby uses `@@` prefix; Python uses `ClassName.variable`
- Ruby's class variables have inheritance issues
- Python's class attributes are more straightforward
- Ruby developers often prefer class instance variables

### Python Example

```python
# Class attributes
class Counter:
    count = 0  # Class attribute

    def __init__(self):
        Counter.count += 1
        self.instance_id = Counter.count

c1 = Counter()
c2 = Counter()
c3 = Counter()

print(Counter.count)  # 3
print(c1.instance_id)  # 1
print(c2.instance_id)  # 2

# Class attributes with inheritance
class Parent:
    value = "parent"

    @classmethod
    def show(cls):
        print(f"Value: {cls.value}")

class Child(Parent):
    value = "child"  # Shadows parent's value

Parent.show()  # Value: parent
Child.show()   # Value: child

# Modifying class attributes
class Settings:
    debug = False

    @classmethod
    def enable_debug(cls):
        cls.debug = True

Settings.enable_debug()
print(Settings.debug)  # True

# Instance vs class attribute
class Example:
    class_var = []  # Shared across all instances

    def __init__(self):
        self.instance_var = []  # Unique to each instance

    def add_to_class(self, item):
        Example.class_var.append(item)

    def add_to_instance(self, item):
        self.instance_var.append(item)

e1 = Example()
e2 = Example()

e1.add_to_class("a")
e2.add_to_class("b")
print(Example.class_var)  # ['a', 'b'] - shared

e1.add_to_instance("x")
e2.add_to_instance("y")
print(e1.instance_var)  # ['x']
print(e2.instance_var)  # ['y'] - separate
```

### Ruby Equivalent

```ruby
# Class variables with @@
class Counter
  @@count = 0  # Class variable

  def initialize
    @@count += 1
    @instance_id = @@count
  end

  def instance_id
    @instance_id
  end

  def self.count
    @@count
  end
end

c1 = Counter.new
c2 = Counter.new
c3 = Counter.new

puts Counter.count  # 3
puts c1.instance_id  # 1
puts c2.instance_id  # 2

# Class variables with inheritance (GOTCHA!)
class Parent
  @@value = "parent"

  def self.show
    puts "Value: #{@@value}"
  end
end

class Child < Parent
  @@value = "child"  # Overwrites parent's @@value!
end

Parent.show  # Value: child (unexpected!)
Child.show   # Value: child

# Class instance variables (preferred alternative)
class Parent
  @value = "parent"

  class << self
    attr_accessor :value
  end

  def self.show
    puts "Value: #{@value}"
  end
end

class Child < Parent
  @value = "child"  # Separate from parent's @value
end

Parent.show  # Value: parent (correct!)
Child.show   # Value: child

# Modifying class variables
class Settings
  @@debug = false

  def self.debug
    @@debug
  end

  def self.enable_debug
    @@debug = true
  end
end

Settings.enable_debug
puts Settings.debug  # true

# Instance vs class variables
class Example
  @@class_var = []  # Shared across all instances

  def initialize
    @instance_var = []  # Unique to each instance
  end

  def add_to_class(item)
    @@class_var << item
  end

  def add_to_instance(item)
    @instance_var << item
  end

  def self.class_var
    @@class_var
  end

  def instance_var
    @instance_var
  end
end

e1 = Example.new
e2 = Example.new

e1.add_to_class("a")
e2.add_to_class("b")
puts Example.class_var.inspect  # ["a", "b"] - shared

e1.add_to_instance("x")
e2.add_to_instance("y")
puts e1.instance_var.inspect  # ["x"]
puts e2.instance_var.inspect  # ["y"] - separate
```

### Explanation

Ruby has three types of class-level variables, each with different behavior:

**1. Class variables (`@@variable`):**
```ruby
class Counter
  @@count = 0

  def initialize
    @@count += 1
  end

  def self.count
    @@count
  end
end
```

**2. Class instance variables (`@variable` at class level):**
```ruby
class Counter
  @count = 0

  class << self
    attr_accessor :count
  end

  def initialize
    self.class.count += 1
  end
end
```

**3. Constants:**
```ruby
class Config
  MAX_SIZE = 100
  DEFAULT_NAME = "unnamed"
end

puts Config::MAX_SIZE
```

**The inheritance gotcha with class variables:**
```ruby
class Parent
  @@shared = "parent value"

  def self.shared
    @@shared
  end

  def self.shared=(value)
    @@shared = value
  end
end

class Child < Parent
end

puts Parent.shared  # "parent value"
puts Child.shared   # "parent value"

# Modifying in child affects parent!
Child.shared = "child value"
puts Parent.shared  # "child value" - GOTCHA!
puts Child.shared   # "child value"
```

**Class instance variables (preferred):**
```ruby
class Parent
  @shared = "parent value"

  class << self
    attr_accessor :shared
  end
end

class Child < Parent
  @shared = "child value"
end

puts Parent.shared  # "parent value"
puts Child.shared   # "child value"

# Modifying in child doesn't affect parent
Child.shared = "new child value"
puts Parent.shared  # "parent value" - stays the same!
puts Child.shared   # "new child value"
```

**Using class instance variables:**
```ruby
class Database
  @connection = nil

  def self.connection
    @connection ||= establish_connection
  end

  def self.establish_connection
    # Create connection
    "Database connection"
  end
end

puts Database.connection  # "Database connection"
```

**Accessing from instance methods:**
```ruby
# Class variable - accessible from instance methods
class Example
  @@class_var = "class"

  def show_class_var
    @@class_var
  end
end

e = Example.new
puts e.show_class_var  # "class"

# Class instance variable - need to go through class
class Example
  @class_var = "class"

  class << self
    attr_reader :class_var
  end

  def show_class_var
    self.class.class_var
  end
end

e = Example.new
puts e.show_class_var  # "class"
```

**When to use each:**
```ruby
# Use constants for values that never change
class Config
  MAX_SIZE = 1000
  DEFAULT_TIMEOUT = 30
end

# Use class instance variables for mutable class-level state
class Logger
  @level = :info

  class << self
    attr_accessor :level
  end
end

Logger.level = :debug

# Avoid class variables (@@) due to inheritance issues
# If you must use them, be aware of the gotchas
```

**Best Practice:**
- **Prefer constants** for values that don't change
- **Prefer class instance variables** over class variables (`@@`)
- **Avoid `@@` class variables** unless you specifically need the inheritance sharing behavior
- Use `class << self` for defining class-level accessors
- Document class-level state clearly
- Consider if you really need class-level state (often instance state is better)

**Summary table:**
```ruby
class Example
  # Constant - never changes
  MAX = 100

  # Class instance variable - per class
  @count = 0

  # Class variable - shared with subclasses (avoid)
  @@shared = 0

  def initialize
    # Instance variable - per instance
    @value = 0
  end
end
```

---

## 9.7 Class Methods (`self.method_name` vs `@classmethod`)

Class methods belong to the class itself rather than instances. Ruby uses `self.method_name` or `class << self` syntax, while Python uses the `@classmethod` decorator.

**Key Similarities:**
- Both belong to the class, not instances
- Both can be called without creating an instance
- Both are often used for factory methods and utilities

**Key Differences:**
- Ruby uses `self.` prefix or `class << self` block
- Python uses `@classmethod` decorator
- Ruby's `self` refers to the class, Python's `cls` parameter is explicit
- Ruby has more syntax options

### Python Example

```python
# Class methods with @classmethod
class User:
    user_count = 0

    def __init__(self, name, email):
        self.name = name
        self.email = email
        User.user_count += 1

    @classmethod
    def get_user_count(cls):
        return cls.user_count

    @classmethod
    def from_dict(cls, data):
        """Factory method"""
        return cls(data['name'], data['email'])

    @classmethod
    def create_guest(cls):
        """Named constructor"""
        return cls("Guest", "guest@example.com")

# Calling class methods
print(User.get_user_count())  # 0

user1 = User("Alice", "alice@example.com")
user2 = User.from_dict({'name': 'Bob', 'email': 'bob@example.com'})
guest = User.create_guest()

print(User.get_user_count())  # 3

# Class methods vs static methods
class MathUtils:
    @classmethod
    def class_method(cls):
        print(f"Called on {cls.__name__}")
        return cls

    @staticmethod
    def static_method():
        print("Called statically")
        return None

MathUtils.class_method()   # Called on MathUtils
MathUtils.static_method()  # Called statically

# Class methods with inheritance
class Parent:
    @classmethod
    def identify(cls):
        return f"I am {cls.__name__}"

class Child(Parent):
    pass

print(Parent.identify())  # I am Parent
print(Child.identify())   # I am Child
```

### Ruby Equivalent

```ruby
# Class methods with self.
class User
  @@user_count = 0

  def initialize(name, email)
    @name = name
    @email = email
    @@user_count += 1
  end

  def self.user_count
    @@user_count
  end

  def self.from_hash(data)
    # Factory method
    new(data[:name], data[:email])
  end

  def self.create_guest
    # Named constructor
    new("Guest", "guest@example.com")
  end
end

# Calling class methods
puts User.user_count  # 0

user1 = User.new("Alice", "alice@example.com")
user2 = User.from_hash({name: "Bob", email: "bob@example.com"})
guest = User.create_guest

puts User.user_count  # 3

# Alternative syntax: class << self
class User
  class << self
    def user_count
      @@user_count
    end

    def from_hash(data)
      new(data[:name], data[:email])
    end

    def create_guest
      new("Guest", "guest@example.com")
    end
  end
end

# Class methods vs instance methods
class MathUtils
  # Class method
  def self.class_method
    puts "Called on #{self.name}"
    self
  end

  # Instance method
  def instance_method
    puts "Called on instance"
    self
  end
end

MathUtils.class_method       # Called on MathUtils
# MathUtils.instance_method  # NoMethodError

utils = MathUtils.new
utils.instance_method        # Called on instance
# utils.class_method         # NoMethodError

# Class methods with inheritance
class Parent
  def self.identify
    "I am #{self.name}"
  end
end

class Child < Parent
end

puts Parent.identify  # I am Parent
puts Child.identify   # I am Child
```

### Explanation

Ruby class methods are methods that belong to the class object itself. They're defined with `self.method_name` or within a `class << self` block.

**Defining class methods with `self.`:**
```ruby
class User
  def self.total_users
    "Total users: #{@@count}"
  end

  def self.create_admin(name)
    user = new(name)
    user.role = :admin
    user
  end
end

User.total_users
admin = User.create_admin("Alice")
```

**Defining class methods with `class << self`:**
```ruby
class User
  class << self
    def total_users
      "Total users: #{@@count}"
    end

    def create_admin(name)
      user = new(name)
      user.role = :admin
      user
    end
  end
end
```

**When to use each syntax:**
```ruby
# Use self. for one or two class methods
class Example
  def self.method1
    # ...
  end

  def self.method2
    # ...
  end
end

# Use class << self for many class methods
class Example
  class << self
    def method1; end
    def method2; end
    def method3; end
    def method4; end
  end
end
```

**Factory methods (alternative constructors):**
```ruby
class User
  attr_reader :name, :email

  def initialize(name, email)
    @name = name
    @email = email
  end

  def self.from_hash(hash)
    new(hash[:name], hash[:email])
  end

  def self.from_csv(csv_line)
    name, email = csv_line.split(',')
    new(name.strip, email.strip)
  end

  def self.guest
    new("Guest", "guest@example.com")
  end
end

user1 = User.new("Alice", "alice@example.com")
user2 = User.from_hash(name: "Bob", email: "bob@example.com")
user3 = User.from_csv("Charlie, charlie@example.com")
guest = User.guest
```

**Accessing class instance variables:**
```ruby
class Database
  @connections = []

  class << self
    attr_reader :connections

    def add_connection(connection)
      @connections << connection
    end

    def reset
      @connections = []
    end
  end
end

Database.add_connection("conn1")
Database.add_connection("conn2")
puts Database.connections.inspect  # ["conn1", "conn2"]
Database.reset
```

**Class methods calling other class methods:**
```ruby
class Logger
  @level = :info

  class << self
    attr_accessor :level

    def debug?
      level == :debug
    end

    def log(message)
      puts message if debug?
    end
  end
end

Logger.level = :debug
Logger.log("Debug message")  # Prints
Logger.level = :info
Logger.log("Debug message")  # Doesn't print
```

**Class methods with inheritance:**
```ruby
class Parent
  def self.inherited(subclass)
    puts "#{subclass} inherited from #{self}"
  end

  def self.greet
    "Hello from #{self.name}"
  end
end

class Child < Parent
end
# Prints: Child inherited from Parent

puts Parent.greet  # Hello from Parent
puts Child.greet   # Hello from Child
```

**Calling instance methods from class methods:**
```ruby
class User
  def initialize(name)
    @name = name
  end

  def greet
    "Hello, I'm #{@name}"
  end

  def self.create_and_greet(name)
    user = new(name)
    user.greet  # Call instance method
  end
end

puts User.create_and_greet("Alice")  # Hello, I'm Alice
```

**Private class methods:**
```ruby
class User
  def self.public_class_method
    private_class_method
  end

  class << self
    private

    def private_class_method
      "This is private"
    end
  end
end

User.public_class_method         # Works
# User.private_class_method      # NoMethodError
```

**Practical example - configuration:**
```ruby
class Config
  @settings = {
    debug: false,
    timeout: 30,
    retries: 3
  }

  class << self
    def get(key)
      @settings[key]
    end

    def set(key, value)
      @settings[key] = value
    end

    def reset
      @settings = {
        debug: false,
        timeout: 30,
        retries: 3
      }
    end
  end
end

Config.set(:debug, true)
puts Config.get(:debug)  # true
Config.reset
puts Config.get(:debug)  # false
```

**Best Practice:**
- Use class methods for factory methods and alternative constructors
- Use class methods for operations that don't need instance data
- Use `self.method_name` for a few class methods
- Use `class << self` block for many class methods
- Name factory methods descriptively (`from_hash`, `create_guest`)
- Don't overuse class methods - prefer instance methods when possible
- Use class methods for utilities and helpers
- Remember that `self` in a class method refers to the class itself

---

## 9.8 Access Control (`private`, `protected`, `public`)

Both Ruby and Python support access control for methods, but Ruby's approach is more strictly enforced and uses different keywords.

**Key Similarities:**
- Both support public, private, and protected visibility
- Both use keywords to control access
- Both are meant to encapsulate implementation details

**Key Differences:**
- Python uses naming conventions (`_method`, `__method`)
- Ruby strictly enforces access control
- Ruby's `private` works differently than Python's
- Ruby's `protected` allows access from same-class instances

### Python Example

```python
# Python uses naming conventions for access control
class User:
    def __init__(self, name, ssn):
        self.name = name           # Public
        self._internal = "data"    # "Protected" (convention only)
        self.__ssn = ssn          # "Private" (name mangling)

    def public_method(self):
        return "Anyone can call this"

    def _protected_method(self):
        return "Should only be called internally (convention)"

    def __private_method(self):
        return "Name mangled to prevent access"

    def get_ssn(self):
        return self.__private_method()

user = User("Alice", "123-45-6789")

# Public - accessible
print(user.name)              # "Alice"
print(user.public_method())   # Works

# "Protected" - accessible but discouraged
print(user._internal)          # "data" - just convention
print(user._protected_method())  # Works but discouraged

# "Private" - name mangled but still accessible
# print(user.__ssn)            # AttributeError
print(user._User__ssn)         # "123-45-6789" - can still access!

# Python's access control is mostly by convention
class BankAccount:
    def __init__(self, balance):
        self._balance = balance  # "Please don't touch" (convention)

    def deposit(self, amount):
        if amount > 0:
            self._balance += amount

    def _validate(self, amount):
        """Internal method (convention)"""
        return amount > 0

account = BankAccount(1000)
print(account._balance)  # 1000 - accessible but discouraged
account._balance = 5000  # Can modify (no enforcement)
```

### Ruby Equivalent

```ruby
# Ruby strictly enforces access control
class User
  attr_reader :name  # Public getter

  def initialize(name, ssn)
    @name = name
    @ssn = ssn
  end

  def public_method
    "Anyone can call this"
  end

  protected

  def protected_method
    "Only callable by same class instances"
  end

  private

  def private_method
    "Only callable within this instance"
  end

  def get_ssn
    private_method  # Can call private methods internally
  end
end

user = User.new("Alice", "123-45-6789")

# Public - accessible
puts user.name                # "Alice"
puts user.public_method       # Works

# Protected - not accessible from outside
# puts user.protected_method  # NoMethodError

# Private - not accessible from outside
# puts user.private_method    # NoMethodError

# Ruby strictly enforces access control
class BankAccount
  def initialize(balance)
    @balance = balance
  end

  def deposit(amount)
    @balance += amount if validate(amount)
  end

  def balance
    @balance
  end

  private

  def validate(amount)
    amount > 0
  end
end

account = BankAccount.new(1000)
puts account.balance   # 1000
# account.validate(50) # NoMethodError: private method
```

### Explanation

Ruby has three access levels for methods: `public`, `protected`, and `private`. These are strictly enforced by the Ruby runtime.

**Public methods (default):**
```ruby
class Example
  # These are public by default
  def method1
    "public"
  end

  def method2
    "also public"
  end

  # Can explicitly mark as public
  public

  def method3
    "explicitly public"
  end
end
```

**Private methods:**
```ruby
class User
  def initialize(name)
    @name = name
  end

  def greet
    "Hello, #{formatted_name}"  # Can call private methods
  end

  private  # Everything below is private

  def formatted_name
    @name.upcase
  end

  def internal_helper
    "helper"
  end
end

user = User.new("Alice")
puts user.greet            # Works
# puts user.formatted_name # NoMethodError: private method
```

**Private methods cannot be called with explicit receiver:**
```ruby
class Example
  def public_method
    private_method      # Works - implicit self
    # self.private_method  # Error! Can't call private with explicit receiver
  end

  private

  def private_method
    "private"
  end
end
```

**Protected methods (accessible from same class):**
```ruby
class Person
  attr_reader :name

  def initialize(name, age)
    @name = name
    @age = age
  end

  def older_than?(other)
    age > other.age  # Can call protected method on other instance
  end

  protected

  def age
    @age
  end
end

alice = Person.new("Alice", 30)
bob = Person.new("Bob", 25)

puts alice.older_than?(bob)  # true
# puts alice.age             # NoMethodError: protected method
```

**Different ways to define private methods:**
```ruby
# Method 1: private keyword affects all methods below
class Example
  def public1; end

  private

  def private1; end
  def private2; end
end

# Method 2: private with method names (symbols)
class Example
  def public1; end
  def private1; end
  def private2; end

  private :private1, :private2
end

# Method 3: inline private (Ruby 2.1+)
class Example
  def public1; end

  private def private1
    "private"
  end

  private def private2
    "also private"
  end
end
```

**Access control can be changed:**
```ruby
class Example
  def method1; end

  private
  def method2; end

  public  # Switch back to public
  def method3; end
end
```

**Private class methods:**
```ruby
class Example
  def self.public_class_method
    private_class_method
  end

  class << self
    private

    def private_class_method
      "private class method"
    end
  end
end

Example.public_class_method     # Works
# Example.private_class_method  # NoMethodError
```

**Practical example - banking:**
```ruby
class BankAccount
  def initialize(balance)
    @balance = balance
    @transactions = []
  end

  def deposit(amount)
    return false unless valid_amount?(amount)
    @balance += amount
    record_transaction(:deposit, amount)
    true
  end

  def withdraw(amount)
    return false unless valid_amount?(amount) && sufficient_funds?(amount)
    @balance -= amount
    record_transaction(:withdrawal, amount)
    true
  end

  def balance
    @balance
  end

  private

  def valid_amount?(amount)
    amount.positive?
  end

  def sufficient_funds?(amount)
    @balance >= amount
  end

  def record_transaction(type, amount)
    @transactions << { type: type, amount: amount, time: Time.now }
  end
end

account = BankAccount.new(1000)
account.deposit(500)     # Works
puts account.balance     # 1500
# account.valid_amount?(100)  # NoMethodError
```

**Best Practice:**
- Make methods `private` by default
- Only expose methods that are part of the public interface
- Use `protected` when instances need to access each other's methods
- Use inline `private def` for clarity (Ruby 2.1+)
- Group private methods at the end of the class
- Document why methods are public if it's not obvious
- Keep public interfaces small and focused

**Summary:**
```ruby
class Example
  # Public (default) - callable from anywhere
  def public_method
    protected_method    # Can call protected
    private_method      # Can call private
  end

  # Protected - callable from same class instances
  protected

  def protected_method
    private_method  # Can call private
  end

  # Private - callable only within same instance, no explicit receiver
  private

  def private_method
    "private"
  end
end
```

---

## 9.9 `self` in Ruby

The keyword `self` in Ruby refers to the current object. It works similarly to Python's `self`, but with some important differences in usage and requirements.

**Key Similarities:**
- Both refer to the current object/instance
- Both provide access to instance methods and variables
- Both can be used to disambiguate

**Key Differences:**
- Ruby's `self` is implicit in most cases
- Python requires explicit `self` parameter
- Ruby uses `self` for different contexts (instance, class, module)
- Ruby's `self` changes depending on context

### Python Example

```python
# Python requires explicit self parameter
class User:
    def __init__(self, name):
        self.name = name  # self is explicit

    def greet(self):
        return f"Hello, {self.name}"  # self.name is explicit

    def update_name(self, new_name):
        self.name = new_name  # self is required

    def compare(self, other):
        return self.name == other.name  # Both instances use self

    @classmethod
    def class_method(cls):
        return cls  # cls refers to the class

user = User("Alice")
print(user.greet())  # "Hello, Alice"

# Using self in calculations
class Rectangle:
    def __init__(self, width, height):
        self.width = width
        self.height = height

    def area(self):
        return self.width * self.height  # self required

    def is_square(self):
        return self.width == self.height

rect = Rectangle(10, 10)
print(rect.is_square())  # True
```

### Ruby Equivalent

```ruby
# Ruby doesn't require self in parameters
class User
  def initialize(name)
    @name = name  # self is implicit
  end

  def greet
    "Hello, #{@name}"  # self.name not needed
  end

  def update_name(new_name)
    @name = new_name  # self is implicit
  end

  # Need self to call a method with the same name as a local variable
  def name
    @name
  end

  def name=(value)
    @name = value
  end

  def update_name_alternative(new_name)
    self.name = new_name  # self required for setter call
  end

  def compare(other)
    name == other.name  # self.name implicit, other.name explicit
  end

  def self.class_method
    self  # self refers to the class
  end
end

user = User.new("Alice")
puts user.greet  # "Hello, Alice"

# Using self in calculations
class Rectangle
  def initialize(width, height)
    @width = width
    @height = height
  end

  def area
    @width * @height  # self not needed
  end

  def square?
    @width == @height
  end
end

rect = Rectangle.new(10, 10)
puts rect.square?  # true
```

### Explanation

In Ruby, `self` refers to the current object but is usually implicit. You only need to use it explicitly in certain situations.

**When `self` is implicit:**
```ruby
class User
  def initialize(name)
    @name = name  # self.@name not needed
  end

  def greet
    helper_method  # self.helper_method not needed
  end

  private

  def helper_method
    "Hello, #{@name}"
  end
end
```

**When `self` is required:**

1. **Calling setter methods:**
```ruby
class User
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def update_name(new_name)
    self.name = new_name  # self required for setter
    # name = new_name     # This creates a local variable!
  end
end
```

2. **Distinguishing from local variables:**
```ruby
class User
  attr_accessor :name

  def confusing_method
    name = "local"      # Local variable
    self.name = "Bob"   # Instance variable via setter
    puts name           # "local"
    puts self.name      # "Bob"
  end
end
```

3. **Returning the current object:**
```ruby
class Builder
  def initialize
    @config = {}
  end

  def set(key, value)
    @config[key] = value
    self  # Return self for chaining
  end

  def build
    @config
  end
end

result = Builder.new
  .set(:name, "test")
  .set(:value, 42)
  .build
```

**`self` in different contexts:**

1. **Instance methods - `self` is the instance:**
```ruby
class User
  def instance_method
    self  # Returns the instance
  end
end

user = User.new
puts user.instance_method == user  # true
```

2. **Class methods - `self` is the class:**
```ruby
class User
  def self.class_method
    self  # Returns the class (User)
  end
end

puts User.class_method == User  # true
```

3. **Class definition - `self` is the class:**
```ruby
class User
  puts self  # User
  puts self.class  # Class
end
```

4. **Singleton class - `self` is special:**
```ruby
class User
  class << self
    puts self  # #<Class:User> (singleton class)

    def class_method
      self  # User
    end
  end
end
```

**Using `self` for method chaining:**
```ruby
class Query
  def initialize
    @conditions = []
  end

  def where(condition)
    @conditions << condition
    self  # Enable chaining
  end

  def order(field)
    @order = field
    self
  end

  def to_sql
    "SELECT * WHERE #{@conditions.join(' AND ')} ORDER BY #{@order}"
  end
end

sql = Query.new
  .where("age > 18")
  .where("active = true")
  .order("name")
  .to_sql
```

**`self` in blocks:**
```ruby
class Example
  def test
    puts "Outside block: #{self}"

    [1, 2, 3].each do |n|
      puts "Inside block: #{self}"  # Same self
      puts "Number: #{n}"
    end
  end
end

Example.new.test
# Outside block: #<Example:...>
# Inside block: #<Example:...>
# ...
```

**`self` for accessing class from instance:**
```ruby
class User
  def my_class
    self.class  # Returns User
  end

  def class_name
    self.class.name  # Returns "User"
  end
end

user = User.new
puts user.my_class      # User
puts user.class_name    # "User"
```

**`self` with protected methods:**
```ruby
class Person
  def initialize(age)
    @age = age
  end

  def older_than?(other)
    self.age > other.age  # self optional here
  end

  protected

  def age
    @age
  end
end

alice = Person.new(30)
bob = Person.new(25)
puts alice.older_than?(bob)  # true
```

**Best Practice:**
- Use `self` only when necessary
- Always use `self` for calling setter methods
- Use `self` to return current object for chaining
- Don't use `self` to access instance variables (`@variable`, not `self.@variable`)
- Use `self` when it improves clarity
- Remember that `self` changes in different contexts

**Summary - when to use `self`:**
```ruby
class Example
  attr_accessor :value

  def example_method
    # DON'T need self:
    @instance_var       # Accessing instance variable
    other_method        # Calling other method

    # DO need self:
    self.value = 5      # Calling setter method
    self                # Returning current object
    self.class          # Getting the class
  end

  def self.class_method
    self                # Refers to the class
  end
end
```

---

## 9.10 Inheritance

Both Ruby and Python support single inheritance, allowing a class to inherit from a parent class. Ruby uses `<` syntax while Python uses parentheses.

**Key Similarities:**
- Both support single inheritance
- Subclasses inherit methods and can override them
- Both can call parent methods using `super`

**Key Differences:**
- Ruby uses `<` for inheritance; Python uses `(ParentClass)`
- Ruby's `super` works without arguments by default
- Ruby has different method lookup rules
- Ruby's inheritance is strictly single (use modules for mixins)

### Python Example

```python
# Basic inheritance
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "Some sound"

    def info(self):
        return f"I am {self.name}"

class Dog(Animal):
    def speak(self):
        return "Woof!"

class Cat(Animal):
    def speak(self):
        return "Meow!"

dog = Dog("Buddy")
print(dog.speak())  # "Woof!"
print(dog.info())   # "I am Buddy"

# Inheritance with __init__
class Vehicle:
    def __init__(self, brand):
        self.brand = brand

    def drive(self):
        return "Driving..."

class Car(Vehicle):
    def __init__(self, brand, model):
        super().__init__(brand)
        self.model = model

    def info(self):
        return f"{self.brand} {self.model}"

car = Car("Toyota", "Camry")
print(car.info())   # "Toyota Camry"
print(car.drive())  # "Driving..."

# Multiple inheritance (Python supports this)
class Flyable:
    def fly(self):
        return "Flying!"

class Bird(Animal, Flyable):
    def speak(self):
        return "Chirp!"

bird = Bird("Tweety")
print(bird.speak())  # "Chirp!"
print(bird.fly())    # "Flying!"
```

### Ruby Equivalent

```ruby
# Basic inheritance
class Animal
  def initialize(name)
    @name = name
  end

  def speak
    "Some sound"
  end

  def info
    "I am #{@name}"
  end
end

class Dog < Animal
  def speak
    "Woof!"
  end
end

class Cat < Animal
  def speak
    "Meow!"
  end
end

dog = Dog.new("Buddy")
puts dog.speak  # "Woof!"
puts dog.info   # "I am Buddy"

# Inheritance with initialize
class Vehicle
  def initialize(brand)
    @brand = brand
  end

  def drive
    "Driving..."
  end
end

class Car < Vehicle
  def initialize(brand, model)
    super(brand)
    @model = model
  end

  def info
    "#{@brand} #{@model}"
  end
end

car = Car.new("Toyota", "Camry")
puts car.info   # "Toyota Camry"
puts car.drive  # "Driving..."

# Ruby doesn't support multiple inheritance
# Use modules (mixins) instead
module Flyable
  def fly
    "Flying!"
  end
end

class Bird < Animal
  include Flyable  # Mixin

  def speak
    "Chirp!"
  end
end

bird = Bird.new("Tweety")
puts bird.speak  # "Chirp!"
puts bird.fly    # "Flying!"
```

### Explanation

Ruby uses single inheritance with the `<` operator. A class can inherit from only one parent class but can include multiple modules (covered in Chapter 10).

**Basic inheritance syntax:**
```ruby
class Parent
  def method1
    "from parent"
  end
end

class Child < Parent
  def method2
    "from child"
  end
end

child = Child.new
child.method1  # "from parent" - inherited
child.method2  # "from child" - defined in child
```

**Overriding methods:**
```ruby
class Parent
  def greet
    "Hello from parent"
  end
end

class Child < Parent
  def greet
    "Hello from child"
  end
end

child = Child.new
puts child.greet  # "Hello from child" - overridden
```

**Checking inheritance:**
```ruby
class Animal; end
class Dog < Animal; end

dog = Dog.new

dog.is_a?(Dog)     # true
dog.is_a?(Animal)  # true
dog.is_a?(Object)  # true (everything inherits from Object)

Dog < Animal       # true (Dog is subclass of Animal)
Dog.superclass     # Animal
Animal.superclass  # Object
```

**Inheriting and extending:**
```ruby
class User
  attr_reader :name

  def initialize(name)
    @name = name
  end

  def greet
    "Hello, I'm #{@name}"
  end
end

class Admin < User
  attr_reader :permissions

  def initialize(name, permissions)
    super(name)  # Call parent's initialize
    @permissions = permissions
  end

  def greet
    "#{super}. I'm an admin."  # Call parent's greet and extend
  end

  def admin_action
    "Performing admin action"
  end
end

admin = Admin.new("Alice", [:read, :write, :delete])
puts admin.greet              # "Hello, I'm Alice. I'm an admin."
puts admin.admin_action       # "Performing admin action"
puts admin.permissions.inspect  # [:read, :write, :delete]
```

**Abstract base class pattern:**
```ruby
class Shape
  def area
    raise NotImplementedError, "Subclass must implement area method"
  end

  def perimeter
    raise NotImplementedError, "Subclass must implement perimeter method"
  end
end

class Circle < Shape
  def initialize(radius)
    @radius = radius
  end

  def area
    Math::PI * @radius ** 2
  end

  def perimeter
    2 * Math::PI * @radius
  end
end

class Rectangle < Shape
  def initialize(width, height)
    @width = width
    @height = height
  end

  def area
    @width * @height
  end

  def perimeter
    2 * (@width + @height)
  end
end

circle = Circle.new(5)
puts circle.area  # 78.53981633974483

rectangle = Rectangle.new(10, 5)
puts rectangle.area  # 50
```

**Inheritance chain:**
```ruby
class GrandParent
  def method1
    "grandparent"
  end
end

class Parent < GrandParent
  def method2
    "parent"
  end
end

class Child < Parent
  def method3
    "child"
  end
end

child = Child.new
child.method1  # "grandparent"
child.method2  # "parent"
child.method3  # "child"

# Check the inheritance chain
Child.ancestors
# [Child, Parent, GrandParent, Object, Kernel, BasicObject]
```

**Best Practice:**
- Keep inheritance hierarchies shallow (prefer composition)
- Use inheritance for "is-a" relationships
- Override methods carefully - ensure consistent behavior
- Use `super` to extend parent behavior
- Document what subclasses are expected to implement
- Consider modules/mixins for shared behavior (covered in Chapter 10)
- Use abstract base classes to define interfaces

---

## 9.11 `super` Keyword

The `super` keyword calls the parent class's method with the same name. Ruby's `super` is more flexible than Python's, with automatic argument forwarding.

**Key Similarities:**
- Both call the parent class's method
- Both allow extending parent functionality
- Both are used in method overriding

**Key Differences:**
- Ruby's `super` forwards arguments automatically
- Ruby's `super()` with parentheses passes no arguments
- Python requires explicit `super().__init__()`
- Ruby's syntax is more concise

### Python Example

```python
# Basic super usage
class Animal:
    def __init__(self, name):
        self.name = name

    def speak(self):
        return "Some sound"

class Dog(Animal):
    def __init__(self, name, breed):
        super().__init__(name)  # Explicit argument passing
        self.breed = breed

    def speak(self):
        parent_sound = super().speak()
        return f"{parent_sound} but actually Woof!"

dog = Dog("Buddy", "Golden Retriever")
print(dog.speak())  # "Some sound but actually Woof!"

# Super with multiple methods
class Vehicle:
    def __init__(self, brand):
        self.brand = brand
        self.speed = 0

    def accelerate(self, amount):
        self.speed += amount
        return f"Speed: {self.speed}"

class Car(Vehicle):
    def __init__(self, brand, model):
        super().__init__(brand)
        self.model = model

    def accelerate(self, amount):
        result = super().accelerate(amount)
        return f"{self.brand} {self.model} - {result}"

car = Car("Toyota", "Camry")
print(car.accelerate(20))  # "Toyota Camry - Speed: 20"

# Multiple inheritance with super
class A:
    def method(self):
        print("A")

class B(A):
    def method(self):
        print("B")
        super().method()

class C(A):
    def method(self):
        print("C")
        super().method()

class D(B, C):
    def method(self):
        print("D")
        super().method()

d = D()
d.method()
# D
# B
# C
# A
```

### Ruby Equivalent

```ruby
# Basic super usage
class Animal
  def initialize(name)
    @name = name
  end

  def speak
    "Some sound"
  end
end

class Dog < Animal
  def initialize(name, breed)
    super(name)  # Automatically forwards name
    @breed = breed
  end

  def speak
    parent_sound = super()
    "#{parent_sound} but actually Woof!"
  end
end

dog = Dog.new("Buddy", "Golden Retriever")
puts dog.speak  # "Some sound but actually Woof!"

# Super with multiple methods
class Vehicle
  def initialize(brand)
    @brand = brand
    @speed = 0
  end

  def accelerate(amount)
    @speed += amount
    "Speed: #{@speed}"
  end
end

class Car < Vehicle
  def initialize(brand, model)
    super(brand)
    @model = model
  end

  def accelerate(amount)
    result = super(amount)
    "#{@brand} #{@model} - #{result}"
  end
end

car = Car.new("Toyota", "Camry")
puts car.accelerate(20)  # "Toyota Camry - Speed: 20"

# Ruby doesn't have multiple inheritance
# Use modules instead (mixins with super work!)
module A
  def method
    puts "A"
  end
end

module B
  def method
    puts "B"
    super
  end
end

module C
  def method
    puts "C"
    super
  end
end

class D
  include A
  include B
  include C

  def method
    puts "D"
    super
  end
end

d = D.new
d.method
# D
# C
# B
# A
```

### Explanation

Ruby's `super` has three forms with different behaviors:

**1. `super` (no parentheses) - forwards all arguments:**
```ruby
class Parent
  def method(a, b, c)
    puts "Parent: #{a}, #{b}, #{c}"
  end
end

class Child < Parent
  def method(a, b, c)
    super  # Automatically forwards a, b, c
    puts "Child: #{a}, #{b}, #{c}"
  end
end

Child.new.method(1, 2, 3)
# Parent: 1, 2, 3
# Child: 1, 2, 3
```

**2. `super(args)` - passes specific arguments:**
```ruby
class Parent
  def method(a, b)
    puts "Parent: #{a}, #{b}"
  end
end

class Child < Parent
  def method(a, b, c)
    super(a, b)  # Only pass a and b
    puts "Child: #{a}, #{b}, #{c}"
  end
end

Child.new.method(1, 2, 3)
# Parent: 1, 2
# Child: 1, 2, 3
```

**3. `super()` - passes no arguments:**
```ruby
class Parent
  def method
    puts "Parent (no args)"
  end
end

class Child < Parent
  def method(a, b)
    super()  # Call parent with no arguments
    puts "Child: #{a}, #{b}"
  end
end

Child.new.method(1, 2)
# Parent (no args)
# Child: 1, 2
```

**Using super in initialize:**
```ruby
class User
  attr_reader :name, :email

  def initialize(name, email)
    @name = name
    @email = email
  end
end

class Admin < User
  attr_reader :role

  def initialize(name, email, role)
    super(name, email)  # Call parent's initialize
    @role = role
  end
end

admin = Admin.new("Alice", "alice@example.com", "superadmin")
puts admin.name   # "Alice"
puts admin.role   # "superadmin"
```

**Extending parent method:**
```ruby
class Logger
  def log(message)
    puts "[LOG] #{message}"
  end
end

class TimestampedLogger < Logger
  def log(message)
    timestamped = "#{Time.now} - #{message}"
    super(timestamped)  # Pass modified message to parent
  end
end

logger = TimestampedLogger.new
logger.log("Something happened")
# [LOG] 2025-01-07 12:00:00 -0800 - Something happened
```

**Super with blocks:**
```ruby
class Parent
  def method
    yield "from parent"
  end
end

class Child < Parent
  def method
    super do |msg|
      puts "Received: #{msg}"
    end
  end
end

Child.new.method
# Received: from parent
```

**Super in class methods:**
```ruby
class Parent
  def self.class_method
    "parent class method"
  end
end

class Child < Parent
  def self.class_method
    "#{super} extended by child"
  end
end

puts Child.class_method
# "parent class method extended by child"
```

**Common patterns:**

1. **Before and after parent:**
```ruby
class Parent
  def save
    puts "Saving..."
    true
  end
end

class Child < Parent
  def save
    puts "Before save"
    result = super
    puts "After save"
    result
  end
end

Child.new.save
# Before save
# Saving...
# After save
```

2. **Conditional super:**
```ruby
class Parent
  def process(data)
    data.upcase
  end
end

class Child < Parent
  def process(data)
    if data.length > 10
      super
    else
      data
    end
  end
end
```

3. **Super with default arguments:**
```ruby
class Parent
  def method(a, b = "default")
    "Parent: #{a}, #{b}"
  end
end

class Child < Parent
  def method(a, b = "child_default")
    super  # Forwards both a and b (including default)
  end
end

puts Child.new.method("test")
# "Parent: test, child_default"
```

**Best Practice:**
- Use `super` without parentheses when forwarding all arguments
- Use `super(specific_args)` when you need to pass specific arguments
- Use `super()` when parent method takes no arguments
- Always call `super` in `initialize` if parent has initialization logic
- Document when `super` should be called in subclasses
- Remember that `super` looks up the inheritance chain

---

## 9.12 Method Overriding

Method overriding allows a subclass to provide a specific implementation of a method that is already defined in its parent class. Both Ruby and Python support method overriding with similar concepts.

**Key Similarities:**
- Both allow subclasses to override parent methods
- Both maintain the same method signature
- Both can call parent method via `super`

**Key Differences:**
- Ruby's method resolution is simpler (single inheritance)
- Python has `@override` decorator (3.12+) for explicitness
- Ruby's `super` is more automatic
- Both approaches to visibility differ

### Python Example

```python
# Basic method overriding
class Animal:
    def speak(self):
        return "Some sound"

    def move(self):
        return "Moving"

class Dog(Animal):
    def speak(self):  # Override
        return "Woof!"

class Bird(Animal):
    def speak(self):  # Override
        return "Chirp!"

    def move(self):  # Override
        return "Flying"

dog = Dog()
print(dog.speak())  # "Woof!"
print(dog.move())   # "Moving" - inherited

bird = Bird()
print(bird.speak())  # "Chirp!"
print(bird.move())   # "Flying" - overridden

# Overriding with super
class Shape:
    def __init__(self, color):
        self.color = color

    def describe(self):
        return f"A {self.color} shape"

class Circle(Shape):
    def __init__(self, color, radius):
        super().__init__(color)
        self.radius = radius

    def describe(self):
        base = super().describe()
        return f"{base} with radius {self.radius}"

circle = Circle("red", 5)
print(circle.describe())  # "A red shape with radius 5"

# Completely replacing vs extending
class Calculator:
    def add(self, a, b):
        return a + b

class LoggingCalculator(Calculator):
    def add(self, a, b):
        result = super().add(a, b)
        print(f"Added {a} + {b} = {result}")
        return result

calc = LoggingCalculator()
calc.add(5, 3)  # Prints: Added 5 + 3 = 8
```

### Ruby Equivalent

```ruby
# Basic method overriding
class Animal
  def speak
    "Some sound"
  end

  def move
    "Moving"
  end
end

class Dog < Animal
  def speak  # Override
    "Woof!"
  end
end

class Bird < Animal
  def speak  # Override
    "Chirp!"
  end

  def move  # Override
    "Flying"
  end
end

dog = Dog.new
puts dog.speak  # "Woof!"
puts dog.move   # "Moving" - inherited

bird = Bird.new
puts bird.speak  # "Chirp!"
puts bird.move   # "Flying" - overridden

# Overriding with super
class Shape
  def initialize(color)
    @color = color
  end

  def describe
    "A #{@color} shape"
  end
end

class Circle < Shape
  def initialize(color, radius)
    super(color)
    @radius = radius
  end

  def describe
    base = super
    "#{base} with radius #{@radius}"
  end
end

circle = Circle.new("red", 5)
puts circle.describe  # "A red shape with radius 5"

# Completely replacing vs extending
class Calculator
  def add(a, b)
    a + b
  end
end

class LoggingCalculator < Calculator
  def add(a, b)
    result = super
    puts "Added #{a} + #{b} = #{result}"
    result
  end
end

calc = LoggingCalculator.new
calc.add(5, 3)  # Prints: Added 5 + 3 = 8
```

### Explanation

Method overriding in Ruby allows subclasses to replace or extend parent class methods. The overridden method completely replaces the parent's version unless you explicitly call `super`.

**Complete override (replacement):**
```ruby
class Parent
  def greet
    "Hello from parent"
  end
end

class Child < Parent
  def greet  # Completely replaces parent's version
    "Hello from child"
  end
end

puts Child.new.greet  # "Hello from child"
```

**Extending with super:**
```ruby
class Parent
  def greet
    "Hello"
  end
end

class Child < Parent
  def greet
    "#{super} from child"  # Extends parent's version
  end
end

puts Child.new.greet  # "Hello from child"
```

**Overriding with different behavior:**
```ruby
class FileProcessor
  def process(filename)
    content = File.read(filename)
    content.upcase
  end
end

class JsonProcessor < FileProcessor
  def process(filename)
    content = File.read(filename)
    require 'json'
    JSON.parse(content)
  end
end
```

**Overriding initialize:**
```ruby
class User
  attr_reader :name

  def initialize(name)
    @name = name
    @created_at = Time.now
  end
end

class Admin < User
  attr_reader :permissions

  def initialize(name, permissions)
    super(name)  # Call parent's initialize
    @permissions = permissions
  end
end

admin = Admin.new("Alice", [:read, :write])
puts admin.name         # "Alice"
puts admin.permissions.inspect  # [:read, :write]
```

**Overriding with argument changes:**
```ruby
class Parent
  def method(a, b)
    "#{a} and #{b}"
  end
end

class Child < Parent
  def method(a, b, c = nil)
    if c
      "#{super(a, b)} and #{c}"
    else
      super(a, b)
    end
  end
end

child = Child.new
puts child.method(1, 2)     # "1 and 2"
puts child.method(1, 2, 3)  # "1 and 2 and 3"
```

**Overriding to add validation:**
```ruby
class BankAccount
  attr_reader :balance

  def initialize(balance)
    @balance = balance
  end

  def withdraw(amount)
    @balance -= amount
  end
end

class SafeBankAccount < BankAccount
  def withdraw(amount)
    if amount > @balance
      raise "Insufficient funds"
    end
    super
  end
end

account = SafeBankAccount.new(100)
# account.withdraw(200)  # Raises: Insufficient funds
account.withdraw(50)     # Works
```

**Overriding to add logging:**
```ruby
class Service
  def call(data)
    process(data)
  end

  private

  def process(data)
    data.upcase
  end
end

class LoggedService < Service
  def call(data)
    puts "Calling service with: #{data}"
    result = super
    puts "Result: #{result}"
    result
  end
end

service = LoggedService.new
service.call("hello")
# Calling service with: hello
# Result: HELLO
```

**Method visibility in overriding:**
```ruby
class Parent
  private

  def secret_method
    "secret"
  end
end

class Child < Parent
  public  # Can change visibility in override

  def secret_method
    super
  end
end

puts Child.new.secret_method  # "secret" - now public!
```

**Checking if method is overridden:**
```ruby
class Parent
  def method1; end
end

class Child < Parent
  def method2; end
end

# Check if method is defined
Child.instance_methods(false)  # [:method2] - only Child's methods
Child.instance_methods(true)   # [..., :method1, :method2, ...] - includes inherited

# Check method owner
Child.instance_method(:method1).owner  # Parent
Child.instance_method(:method2).owner  # Child
```

**Best Practice:**
- Override methods to specialize behavior for subclasses
- Call `super` when you want to extend (not replace) parent behavior
- Keep method signatures compatible (same parameter expectations)
- Document when methods are intended to be overridden
- Consider using abstract methods (raise NotImplementedError) to force overriding
- Don't change method visibility unexpectedly
- Test overridden methods thoroughly

**Common patterns:**

1. **Template method pattern:**
```ruby
class ReportGenerator
  def generate
    gather_data
    format_data
    output_data
  end

  def gather_data
    raise NotImplementedError, "Subclass must implement gather_data"
  end

  def format_data
    # Default implementation
    @data
  end

  def output_data
    puts @data
  end
end

class SalesReport < ReportGenerator
  def gather_data
    @data = "Sales data"
  end

  def format_data
    @data.upcase
  end
end

SalesReport.new.generate
# SALES DATA
```

2. **Hook methods:**
```ruby
class Base
  def save
    before_save
    perform_save
    after_save
  end

  def before_save; end  # Hook for subclasses
  def after_save; end   # Hook for subclasses

  private

  def perform_save
    puts "Saving..."
  end
end

class User < Base
  def before_save
    puts "Validating user..."
  end

  def after_save
    puts "User saved!"
  end
end

User.new.save
# Validating user...
# Saving...
# User saved!
```

---

This concludes Chapter 9 on Classes and Object-Oriented Programming. You've now learned how Ruby implements OOP concepts, including classes, instance variables, methods, inheritance, and access control. These fundamentals form the basis for building Ruby applications.

In the next chapter, we'll explore Modules and Mixins, which provide Ruby's alternative to multiple inheritance.
