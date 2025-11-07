# Chapter 17: Advanced Topics

This chapter covers advanced Ruby features that go beyond the basics. While not essential for everyday Ruby programming, these topics will help you understand Ruby codebases that use metaprogramming, deal with immutability, or leverage modern Ruby features like pattern matching.

---

## 17.1 Metaprogramming Basics

Metaprogramming is writing code that writes code. Ruby is famous for its metaprogramming capabilities, which are more extensive than Python's. While Python has decorators and `__getattr__`, Ruby provides a richer set of tools for runtime code generation.

### 17.1.1 `define_method`

`define_method` creates methods dynamically at runtime, similar to using `setattr` with a lambda in Python, but more idiomatic and powerful in Ruby.

**Key Similarities:**
- Both languages can create methods dynamically
- Both can use closures to capture variables
- Both are useful for reducing boilerplate
- Both execute at class definition time (if used at top level)

**Key Differences:**
- Ruby's `define_method` is a first-class feature, more commonly used
- Ruby's syntax is cleaner and more readable
- `define_method` integrates naturally with Ruby's open classes
- Ruby captures block context automatically (closure)

#### Python Example

```python
# Creating methods dynamically
class DynamicAttributes:
    def __init__(self):
        self._data = {}

    def __getattr__(self, name):
        if name.startswith('get_'):
            key = name[4:]
            return lambda: self._data.get(key)
        raise AttributeError(f"No attribute {name}")

# More explicit dynamic method creation
class DataAccessor:
    def __init__(self):
        self._data = {'name': 'Alice', 'age': 30}

# Create getter methods
for key in ['name', 'age']:
    def make_getter(k):
        def getter(self):
            return self._data[k]
        return getter
    setattr(DataAccessor, f'get_{key}', make_getter(key))

obj = DataAccessor()
print(obj.get_name())  # Alice
print(obj.get_age())   # 30

# Using type() to create class dynamically
def get_value(self, key):
    return self._data[key]

DynamicClass = type('DynamicClass', (), {
    '__init__': lambda self: setattr(self, '_data', {'x': 1}),
    'get_value': get_value
})
```

#### Ruby Equivalent

```ruby
# Creating methods dynamically with define_method
class DataAccessor
  def initialize
    @data = { name: 'Alice', age: 30 }
  end

  # Define getter methods dynamically
  [:name, :age].each do |key|
    define_method("get_#{key}") do
      @data[key]
    end
  end
end

obj = DataAccessor.new
puts obj.get_name  # Alice
puts obj.get_age   # 30

# More complex example: define CRUD methods
class Model
  attr_reader :attributes

  def initialize
    @attributes = {}
  end

  # Define getter and setter for multiple attributes
  def self.attribute(name)
    define_method(name) do
      @attributes[name]
    end

    define_method("#{name}=") do |value|
      @attributes[name] = value
    end
  end

  # Define multiple attributes at once
  def self.attributes(*names)
    names.each { |name| attribute(name) }
  end
end

class User < Model
  attributes :name, :email, :age
end

user = User.new
user.name = 'Alice'
user.email = 'alice@example.com'
puts user.name   # Alice
puts user.email  # alice@example.com

# Dynamic method with parameters
class Calculator
  [:add, :subtract, :multiply, :divide].each do |operation|
    define_method("calculate_#{operation}") do |a, b|
      a.send(operation, b)
    end
  end
end

calc = Calculator.new
puts calc.calculate_add(5, 3)       # 8
puts calc.calculate_multiply(4, 7)  # 28
```

#### Explanation

`define_method` is a powerful way to create methods programmatically. It's commonly used in Ruby frameworks (like Rails) to reduce boilerplate.

**Important points:**

**Basic syntax:**
```ruby
define_method(:method_name) do |args|
  # method body
end
```

**Capturing variables (closures):**
```ruby
class ConfigLoader
  def self.add_config(key, default_value)
    define_method(key) do
      @config ||= {}
      @config[key] || default_value
    end

    define_method("#{key}=") do |value|
      @config ||= {}
      @config[key] = value
    end
  end
end

class AppConfig < ConfigLoader
  add_config :timeout, 30
  add_config :retries, 3
end

config = AppConfig.new
puts config.timeout  # 30
config.timeout = 60
puts config.timeout  # 60
```

**Using in modules:**
```ruby
module Logging
  def self.included(base)
    [:debug, :info, :warn, :error].each do |level|
      base.define_method("log_#{level}") do |message|
        puts "[#{level.upcase}] #{message}"
      end
    end
  end
end

class Application
  include Logging
end

app = Application.new
app.log_info("Application started")   # [INFO] Application started
app.log_error("Something went wrong") # [ERROR] Something went wrong
```

**Common use cases:**
- Generating repetitive methods (getters, setters, delegators)
- Creating DSLs (Domain Specific Languages)
- Building framework features (like ActiveRecord associations)
- Reducing boilerplate code

**Watch out:**
- Methods defined with `define_method` are slightly slower than regular methods
- Debugging can be harder (stack traces less clear)
- Use sparingly - explicit methods are often clearer

---

### 17.1.2 `method_missing`

`method_missing` is called when a method doesn't exist, similar to Python's `__getattr__`. It's a powerful metaprogramming tool but should be used carefully.

**Key Similarities:**
- Both handle missing method calls
- Both can implement dynamic behavior
- Both can delegate to other objects
- Both should define corresponding inspection methods

**Key Differences:**
- Ruby's `method_missing` catches method calls, Python's `__getattr__` catches attribute access
- Ruby has `respond_to_missing?` to pair with `method_missing`
- Ruby's implementation is more commonly used in frameworks
- Python uses `__getattribute__` for all attribute access (more powerful but dangerous)

#### Python Example

```python
# Using __getattr__ for missing attributes
class DynamicProxy:
    def __init__(self, target):
        self._target = target

    def __getattr__(self, name):
        # Called when attribute not found
        if name.startswith('get_'):
            key = name[4:]
            return lambda: getattr(self._target, key, None)
        return getattr(self._target, name)

    def __dir__(self):
        # Support for dir() function
        return dir(self._target)

class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

user = User("Alice", "alice@example.com")
proxy = DynamicProxy(user)
print(proxy.get_name())  # Alice

# Dynamic API client
class APIClient:
    def __init__(self, base_url):
        self.base_url = base_url

    def __getattr__(self, name):
        if name.startswith('get_'):
            resource = name[4:]
            return lambda: f"GET {self.base_url}/{resource}"
        raise AttributeError(f"No attribute {name}")

api = APIClient("https://api.example.com")
print(api.get_users())  # GET https://api.example.com/users
print(api.get_posts())  # GET https://api.example.com/posts
```

#### Ruby Equivalent

```ruby
# Using method_missing for missing methods
class DynamicProxy
  def initialize(target)
    @target = target
  end

  def method_missing(method_name, *args, &block)
    if method_name.to_s.start_with?('get_')
      key = method_name.to_s[4..]
      @target.send(key) rescue nil
    else
      @target.send(method_name, *args, &block)
    end
  end

  # IMPORTANT: Always define respond_to_missing? with method_missing
  def respond_to_missing?(method_name, include_private = false)
    method_name.to_s.start_with?('get_') || @target.respond_to?(method_name)
  end
end

class User
  attr_accessor :name, :email

  def initialize(name, email)
    @name = name
    @email = email
  end
end

user = User.new("Alice", "alice@example.com")
proxy = DynamicProxy.new(user)
puts proxy.get_name  # Alice
puts proxy.respond_to?(:get_name)  # true (thanks to respond_to_missing?)

# Dynamic API client
class APIClient
  def initialize(base_url)
    @base_url = base_url
  end

  def method_missing(method_name, *args, &block)
    if method_name.to_s.start_with?('get_')
      resource = method_name.to_s[4..]
      "GET #{@base_url}/#{resource}"
    else
      super  # Call original method_missing (raises NoMethodError)
    end
  end

  def respond_to_missing?(method_name, include_private = false)
    method_name.to_s.start_with?('get_') || super
  end
end

api = APIClient.new("https://api.example.com")
puts api.get_users  # GET https://api.example.com/users
puts api.get_posts  # GET https://api.example.com/posts

# Hash-like access
class Configuration
  def initialize
    @settings = {}
  end

  def method_missing(method_name, *args)
    method_str = method_name.to_s

    if method_str.end_with?('=')
      # Setter
      key = method_str.chop.to_sym
      @settings[key] = args.first
    else
      # Getter
      @settings[method_name]
    end
  end

  def respond_to_missing?(method_name, include_private = false)
    true  # Accept any method
  end
end

config = Configuration.new
config.database_url = "postgresql://localhost"
config.port = 5432
puts config.database_url  # postgresql://localhost
puts config.port          # 5432
```

#### Explanation

`method_missing` intercepts calls to undefined methods. It's powerful but should be used carefully because it can make code harder to debug and slower.

**Important points:**

**Always implement `respond_to_missing?`:**
```ruby
def method_missing(method_name, *args, &block)
  # Handle missing method
end

def respond_to_missing?(method_name, include_private = false)
  # Return true if method_missing will handle this method
  # Otherwise call super
end
```

**Call `super` for unhandled methods:**
```ruby
def method_missing(method_name, *args, &block)
  if should_handle?(method_name)
    # Handle it
  else
    super  # Let Ruby raise NoMethodError
  end
end
```

**Delegation pattern:**
```ruby
class SimpleDelegator
  def initialize(target)
    @target = target
  end

  def method_missing(method_name, *args, &block)
    @target.send(method_name, *args, &block)
  end

  def respond_to_missing?(method_name, include_private = false)
    @target.respond_to?(method_name, include_private) || super
  end
end
```

**Common use cases:**
- Building DSLs
- Implementing delegation/proxy patterns
- Creating flexible APIs
- Hash-like attribute access

**Performance considerations:**
```ruby
# method_missing is slower than defined methods
# Consider defining methods dynamically instead:

class FastConfiguration
  def initialize
    @settings = {}
  end

  def set(key, value)
    @settings[key] = value

    # Define methods dynamically for better performance
    define_singleton_method(key) { @settings[key] }
    define_singleton_method("#{key}=") { |v| @settings[key] = v }
  end
end
```

**Watch out:**
- Always implement `respond_to_missing?` alongside `method_missing`
- Call `super` for methods you don't handle
- Performance penalty - `method_missing` is much slower than real methods
- Can make debugging difficult
- Use `define_method` instead when possible

---

### 17.1.3 `send` and Dynamic Method Calls

Ruby's `send` method allows calling methods dynamically by name, similar to Python's `getattr`. It's useful for metaprogramming and when method names are determined at runtime.

**Key Similarities:**
- Both call methods dynamically by name
- Both can pass arguments
- Both can access private methods (with variations)
- Both are useful for metaprogramming

**Key Differences:**
- Ruby's `send` can call any method including private ones
- Ruby has `public_send` that respects visibility
- Python uses `getattr(obj, name)()` which is more verbose
- Ruby's syntax is more concise for dynamic calls

#### Python Example

```python
# Dynamic method calls in Python
class Calculator:
    def add(self, a, b):
        return a + b

    def subtract(self, a, b):
        return a - b

    def multiply(self, a, b):
        return a * b

calc = Calculator()

# Using getattr to call methods dynamically
operation = 'add'
result = getattr(calc, operation)(5, 3)
print(result)  # 8

# With error handling
operation = 'divide'
if hasattr(calc, operation):
    result = getattr(calc, operation)(10, 2)
else:
    print(f"Operation {operation} not found")

# Generic operation handler
def perform_operation(obj, operation, *args):
    method = getattr(obj, operation, None)
    if method and callable(method):
        return method(*args)
    raise AttributeError(f"No method {operation}")

result = perform_operation(calc, 'multiply', 4, 7)
print(result)  # 28

# Calling private methods (Python doesn't enforce privacy)
class MyClass:
    def _private_method(self):
        return "private"

obj = MyClass()
# Python's "private" is just convention
print(getattr(obj, '_private_method')())  # private
```

#### Ruby Equivalent

```ruby
# Dynamic method calls in Ruby
class Calculator
  def add(a, b)
    a + b
  end

  def subtract(a, b)
    a - b
  end

  def multiply(a, b)
    a * b
  end

  private

  def internal_calculate
    "internal"
  end
end

calc = Calculator.new

# Using send to call methods dynamically
operation = 'add'
result = calc.send(operation, 5, 3)
puts result  # 8

# With error handling
operation = 'divide'
if calc.respond_to?(operation)
  result = calc.send(operation, 10, 2)
else
  puts "Operation #{operation} not found"
end

# Generic operation handler
def perform_operation(obj, operation, *args)
  obj.send(operation, *args)
rescue NoMethodError => e
  puts "Method #{operation} not found: #{e.message}"
end

result = perform_operation(calc, :multiply, 4, 7)
puts result  # 28

# send can call private methods!
result = calc.send(:internal_calculate)
puts result  # internal

# public_send respects visibility
begin
  calc.public_send(:internal_calculate)
rescue NoMethodError => e
  puts "Cannot call private method"  # This will print
end

# Practical example: attribute getter/setter
class Person
  attr_accessor :name, :age, :email

  def initialize(name, age, email)
    @name = name
    @age = age
    @email = email
  end
end

person = Person.new("Alice", 30, "alice@example.com")

# Dynamic attribute access
attributes = [:name, :age, :email]
attributes.each do |attr|
  value = person.send(attr)
  puts "#{attr}: #{value}"
end

# Dynamic attribute setting
person.send(:name=, "Bob")
puts person.name  # Bob

# Building a query interface
class Query
  def initialize
    @conditions = []
  end

  def where(field, value)
    @conditions << "#{field} = #{value}"
    self
  end

  def method_missing(method_name, *args)
    if method_name.to_s.start_with?('where_')
      field = method_name.to_s[6..]
      where(field, args.first)
    else
      super
    end
  end

  def to_sql
    "SELECT * WHERE #{@conditions.join(' AND ')}"
  end
end

query = Query.new
query.where_name('Alice').where_age(30)
puts query.to_sql
# SELECT * WHERE name = Alice AND age = 30
```

#### Explanation

`send` is one of Ruby's most useful metaprogramming tools. It allows you to call any method by passing its name as a symbol or string.

**Important points:**

**Basic usage:**
```ruby
object.send(:method_name, arg1, arg2)
object.send('method_name', arg1, arg2)  # Strings work too
```

**send vs public_send:**
```ruby
class MyClass
  def public_method
    "public"
  end

  private

  def private_method
    "private"
  end
end

obj = MyClass.new

# send can call private methods
obj.send(:private_method)  # "private" - works!

# public_send respects visibility
obj.public_send(:private_method)  # NoMethodError - fails!

# Use public_send when working with untrusted input
user_input = :private_method
obj.public_send(user_input)  # Safer - won't call private methods
```

**Dynamic delegation:**
```ruby
class Decorator
  def initialize(component)
    @component = component
  end

  def method_missing(method_name, *args, &block)
    if @component.respond_to?(method_name)
      @component.send(method_name, *args, &block)
    else
      super
    end
  end
end
```

**Operator overloading with send:**
```ruby
# Operators are methods in Ruby!
5.send(:+, 3)   # 8
10.send(:*, 2)  # 20
10.send(:>, 5)  # true

# Useful for dynamic operations
operations = { add: :+, subtract: :-, multiply: :*, divide: :/ }
result = 10.send(operations[:add], 5)  # 15
```

**Chaining with send:**
```ruby
class FluentAPI
  def initialize(value)
    @value = value
  end

  [:add, :subtract, :multiply, :divide].each do |op|
    define_method(op) do |n|
      @value = @value.send(op == :divide ? :/ : op == :multiply ? :* : op, n)
      self
    end
  end

  def value
    @value
  end
end

result = FluentAPI.new(10).add(5).multiply(2).subtract(3).value
puts result  # 27
```

**Common use cases:**
- Calling methods based on user input or configuration
- Building DSLs and fluent interfaces
- Implementing delegation patterns
- Dynamic dispatch in metaprogramming
- Testing private methods (use sparingly!)

**Watch out:**
- Use `public_send` with untrusted input for security
- `send` bypasses visibility - use responsibly
- String method names work but symbols are more efficient
- Always check `respond_to?` before `send` to avoid errors

---

### 17.1.4 `class_eval` and `instance_eval`

These methods evaluate code in the context of a class or instance, similar to Python's `exec` but more structured. They're powerful tools for modifying classes at runtime.

**Key Similarities:**
- Both languages can execute code dynamically
- Both can modify classes at runtime
- Both can define methods programmatically
- Both capture surrounding scope

**Key Differences:**
- Ruby's eval methods change the context (`self`)
- Python's `exec` requires explicit namespace manipulation
- Ruby's approach is more common in production code
- Ruby has separate methods for class vs instance context

#### Python Example

```python
# Dynamic code execution in Python
class MyClass:
    class_variable = 10

# Adding methods to a class
def new_method(self):
    return "dynamically added"

MyClass.dynamic_method = new_method

obj = MyClass()
print(obj.dynamic_method())  # dynamically added

# Using exec to modify class
code = """
def another_method(self):
    return "from exec"
"""

exec(code, MyClass.__dict__)
print(obj.another_method())  # from exec

# Adding class methods
def class_method(cls):
    return f"Class variable: {cls.class_variable}"

MyClass.get_class_var = classmethod(class_method)
print(MyClass.get_class_var())  # Class variable: 10

# Modifying instance
obj = MyClass()
exec("self.new_attr = 42", {"self": obj})
print(obj.new_attr)  # 42

# Using type() for dynamic class creation
DynamicClass = type('DynamicClass', (), {
    'x': 10,
    'get_x': lambda self: self.x
})

dynamic_obj = DynamicClass()
print(dynamic_obj.get_x())  # 10
```

#### Ruby Equivalent

```ruby
# Dynamic code execution in Ruby
class MyClass
  @@class_variable = 10

  def self.class_variable
    @@class_variable
  end
end

# class_eval - evaluates code in class context
MyClass.class_eval do
  def dynamic_method
    "dynamically added"
  end

  def another_method
    "from class_eval"
  end

  # Can access class variable
  def get_class_var
    @@class_variable
  end
end

obj = MyClass.new
puts obj.dynamic_method    # dynamically added
puts obj.another_method    # from class_eval
puts obj.get_class_var     # 10

# instance_eval - evaluates code in instance context
obj = MyClass.new
obj.instance_eval do
  @instance_var = 42

  # Can define singleton methods (methods on just this instance)
  def singleton_method
    "only on this instance"
  end
end

puts obj.instance_variable_get(:@instance_var)  # 42
puts obj.singleton_method  # only on this instance

# Another instance doesn't have the singleton method
obj2 = MyClass.new
begin
  obj2.singleton_method
rescue NoMethodError
  puts "obj2 doesn't have singleton_method"
end

# class_eval with string (can access local variables)
method_name = "computed_method"
MyClass.class_eval <<-RUBY
  def #{method_name}
    "method name was: #{method_name}"
  end
RUBY

puts obj.computed_method  # method name was: computed_method

# Practical example: Adding methods from a configuration
class User
end

attributes = [:name, :email, :age]
User.class_eval do
  attributes.each do |attr|
    define_method(attr) do
      instance_variable_get("@#{attr}")
    end

    define_method("#{attr}=") do |value|
      instance_variable_set("@#{attr}", value)
    end
  end
end

user = User.new
user.name = "Alice"
user.email = "alice@example.com"
puts user.name   # Alice
puts user.email  # alice@example.com

# Module extension pattern
module Logging
  def self.included(base)
    base.class_eval do
      def log(message)
        puts "[#{self.class.name}] #{message}"
      end
    end
  end
end

class Application
  include Logging
end

app = Application.new
app.log("Application started")  # [Application] Application started

# instance_eval to create DSL
class ConfigBuilder
  def initialize
    @config = {}
  end

  def build(&block)
    instance_eval(&block)
    @config
  end

  def method_missing(name, *args)
    if args.length == 1
      @config[name] = args.first
    else
      super
    end
  end
end

config = ConfigBuilder.new.build do
  host "localhost"
  port 3000
  timeout 30
end

puts config  # {:host=>"localhost", :port=>3000, :timeout=>30}
```

#### Explanation

`class_eval` and `instance_eval` change the context in which code executes, allowing you to add methods, modify variables, and create DSLs.

**Important differences:**

**class_eval (also: module_eval):**
- Evaluates code in the context of a class/module
- `self` is the class
- Used to add instance methods
- Used to modify class structure

```ruby
class MyClass
end

MyClass.class_eval do
  puts self  # MyClass

  def instance_method
    "instance method"
  end

  def self.class_method
    "class method"
  end
end
```

**instance_eval:**
- Evaluates code in the context of an instance
- `self` is the instance
- Used to define singleton methods
- Used to access/modify instance variables

```ruby
obj = MyClass.new

obj.instance_eval do
  puts self  # #<MyClass:0x00007f...>

  @my_var = 42

  def singleton_method
    "only on this object"
  end
end
```

**Block vs string evaluation:**

```ruby
# Block form - cannot access string interpolation from outer scope
MyClass.class_eval do
  def method1
    "hello"
  end
end

# String form - can use interpolation
name = "dynamic"
MyClass.class_eval <<-RUBY
  def method_#{name}
    "#{name} method"
  end
RUBY

obj = MyClass.new
puts obj.method_dynamic  # dynamic method
```

**Accessing different scopes:**

```ruby
class MyClass
  @class_instance_var = "class instance"
  @@class_var = "class variable"

  def initialize
    @instance_var = "instance"
  end
end

# class_eval accesses class instance variables
MyClass.class_eval do
  puts @class_instance_var  # class instance
end

# instance_eval on class accesses class instance variables
MyClass.instance_eval do
  puts @class_instance_var  # class instance
end

# instance_eval on object accesses instance variables
obj = MyClass.new
obj.instance_eval do
  puts @instance_var  # instance
end
```

**Common use cases:**
- Building DSLs (Domain Specific Languages)
- Reopening classes to add functionality
- Creating testing frameworks
- Implementing configuration builders
- Metaprogramming in gems and frameworks

**Watch out:**
- Code in strings isn't syntax-checked until runtime
- Block form is generally safer than string form
- Performance impact - dynamic code is slower
- Debugging can be difficult
- `instance_eval` on a class is different from `class_eval`

---

## 17.2 Refinements (Scoped Monkey Patching)

Refinements provide scoped modifications to classes, solving the problem of global monkey patching. Unlike Python which doesn't have a direct equivalent, Ruby refinements allow you to modify classes locally.

**Key Similarities:**
- Both languages allow modifying existing classes
- Both can add methods to built-in types
- Both changes affect behavior of existing code

**Key Differences:**
- Ruby refinements are scoped (only active where imported)
- Python monkey patching is always global
- Ruby refinements are safer and more maintainable
- Refinements must be explicitly activated with `using`

### Python Example

```python
# Monkey patching in Python (always global)
class String(str):
    def shout(self):
        return self.upper() + "!"

# Or modify built-in directly (dangerous!)
def shout(self):
    return self.upper() + "!"

# This affects ALL code globally
str.shout = shout

print("hello".shout())  # HELLO!

# The change is global - affects everything
text = "goodbye"
print(text.shout())  # GOODBYE!

# No way to scope this change
# If imported in a module, it affects entire program

# Context manager workaround (manual)
class MonkeyPatch:
    def __init__(self, obj, method_name, new_method):
        self.obj = obj
        self.method_name = method_name
        self.new_method = new_method
        self.original = None

    def __enter__(self):
        self.original = getattr(self.obj, self.method_name, None)
        setattr(self.obj, self.method_name, self.new_method)
        return self

    def __exit__(self, *args):
        if self.original:
            setattr(self.obj, self.method_name, self.original)
        else:
            delattr(self.obj, self.method_name)

# Scoped usage (manual restoration)
with MonkeyPatch(str, 'shout', lambda self: self.upper() + "!"):
    print("hello".shout())  # HELLO!

# Outside context, method doesn't exist
try:
    print("hello".shout())
except AttributeError:
    print("shout method no longer exists")
```

### Ruby Equivalent

```ruby
# Refinements - scoped monkey patching
module StringExtensions
  refine String do
    def shout
      upcase + "!"
    end

    def whisper
      downcase + "..."
    end
  end
end

# Without 'using', refinements don't work
begin
  puts "hello".shout
rescue NoMethodError
  puts "shout method doesn't exist yet"
end

# Activate refinements with 'using'
class MyClass
  using StringExtensions

  def demonstrate
    puts "hello".shout     # HELLO!
    puts "GOODBYE".whisper # goodbye...
  end
end

obj = MyClass.new
obj.demonstrate

# Outside the class, refinement not active
begin
  puts "hello".shout
rescue NoMethodError
  puts "Refinement not active here"
end

# Refinements can be used in modules
module MyModule
  using StringExtensions

  def self.greet
    "hello".shout
  end
end

puts MyModule.greet  # HELLO!

# Refining multiple classes
module NumericExtensions
  refine Integer do
    def even_or_odd
      even? ? "even" : "odd"
    end
  end

  refine Float do
    def round_to(decimals)
      (self * (10 ** decimals)).round / (10.0 ** decimals)
    end
  end
end

class Calculator
  using NumericExtensions

  def analyze(number)
    if number.is_a?(Integer)
      "#{number} is #{number.even_or_odd}"
    else
      "#{number} rounded: #{number.round_to(2)}"
    end
  end
end

calc = Calculator.new
puts calc.analyze(7)     # 7 is odd
puts calc.analyze(3.14159)  # 3.14159 rounded: 3.14

# Refining your own classes
class User
  attr_accessor :name, :email

  def initialize(name, email)
    @name = name
    @email = email
  end
end

module UserExtensions
  refine User do
    def display_name
      "#{name} <#{email}>"
    end

    def domain
      email.split('@').last
    end
  end
end

class UserPresenter
  using UserExtensions

  def present(user)
    "User: #{user.display_name} from #{user.domain}"
  end
end

user = User.new("Alice", "alice@example.com")
presenter = UserPresenter.new
puts presenter.present(user)
# User: Alice <alice@example.com> from example.com

# Outside, refined methods don't exist
begin
  user.display_name
rescue NoMethodError
  puts "Refined methods not available here"
end
```

### Explanation

Refinements solve the problem of global monkey patching by providing scoped modifications to classes. They're activated with `using` and only affect code in that scope.

**Important points:**

**Scope rules:**
```ruby
module MyRefinements
  refine String do
    def custom_method
      "refined"
    end
  end
end

# File scope
using MyRefinements
puts "test".custom_method  # Works in this file

# Class scope
class MyClass
  using MyRefinements

  def test
    "hello".custom_method  # Works in this class
  end
end

# Module scope
module MyModule
  using MyRefinements

  def self.test
    "hello".custom_method  # Works in this module
  end
end
```

**Limitations:**
```ruby
module StringRefinements
  refine String do
    def shout
      upcase + "!"
    end
  end
end

class Example
  using StringRefinements

  # Works in methods
  def instance_method
    "hello".shout  # OK
  end

  # Doesn't work in class methods (gotcha!)
  def self.class_method
    "hello".shout  # NoMethodError!
  end

  # Workaround: use 'using' inside class method
  def self.class_method_fixed
    using StringRefinements
    "hello".shout  # OK
  end
end
```

**Refining core classes safely:**
```ruby
module SafeArrayExtensions
  refine Array do
    def second
      self[1]
    end

    def third
      self[2]
    end
  end
end

class DataProcessor
  using SafeArrayExtensions

  def process(data)
    puts "First: #{data.first}"
    puts "Second: #{data.second}"
    puts "Third: #{data.third}"
  end
end

processor = DataProcessor.new
processor.process([1, 2, 3, 4, 5])
# First: 1
# Second: 2
# Third: 3

# Outside this class, Array doesn't have these methods
# No global pollution!
```

**When to use refinements:**
- Extending core classes without global modifications
- Creating DSLs with custom syntax
- Testing alternative implementations
- Library development (provide opt-in enhancements)
- Avoiding conflicts between libraries

**Watch out:**
- Refinements don't work in all contexts (e.g., class methods need special handling)
- Performance overhead (small but exists)
- Can be confusing for code readers
- Not all Ruby code supports refinements properly
- Use sparingly - explicit methods often clearer

---

## 17.3 Structs and OpenStructs

Structs provide a convenient way to bundle attributes together, similar to Python's `namedtuple` or `dataclass`. OpenStruct is more dynamic, similar to JavaScript objects.

**Key Similarities:**
- Both create simple data structures
- Both provide attribute access
- Both are lighter than full classes
- Both useful for data transfer objects

**Key Differences:**
- Ruby Structs are classes, not tuples
- OpenStruct is completely dynamic (like a hash with dot notation)
- Ruby Structs are faster than OpenStructs
- Python's dataclasses have more features (type hints, defaults, etc.)

### Python Example

```python
from collections import namedtuple
from dataclasses import dataclass
from types import SimpleNamespace

# namedtuple - immutable
Point = namedtuple('Point', ['x', 'y'])
p = Point(10, 20)
print(p.x, p.y)  # 10 20
# p.x = 15  # Error - immutable

# dataclass - mutable with more features
@dataclass
class User:
    name: str
    age: int
    email: str = "unknown"

    def greet(self):
        return f"Hello, I'm {self.name}"

user = User("Alice", 30)
print(user.name)     # Alice
print(user.greet())  # Hello, I'm Alice
user.age = 31       # Mutable

# SimpleNamespace - dynamic attributes
config = SimpleNamespace()
config.host = "localhost"
config.port = 3000
config.debug = True

print(config.host)  # localhost

# Or from dict
data = {"host": "localhost", "port": 3000}
config2 = SimpleNamespace(**data)
print(config2.port)  # 3000

# Can add attributes dynamically
config.timeout = 30
print(config.timeout)  # 30
```

### Ruby Equivalent

```ruby
# Struct - creates a class with attributes
Point = Struct.new(:x, :y)
p = Point.new(10, 20)
puts p.x, p.y  # 10, 20

# Structs are mutable
p.x = 15
puts p.x  # 15

# Struct with methods
User = Struct.new(:name, :age, :email) do
  def greet
    "Hello, I'm #{name}"
  end

  def adult?
    age >= 18
  end
end

user = User.new("Alice", 30, "alice@example.com")
puts user.name      # Alice
puts user.greet     # Hello, I'm Alice
puts user.adult?    # true

# Struct with keyword arguments (Ruby 3.0+)
Person = Struct.new(:name, :age, keyword_init: true)
person = Person.new(name: "Bob", age: 25)
puts person.name  # Bob

# Struct with default values
Config = Struct.new(:host, :port, :debug) do
  def initialize(host: "localhost", port: 3000, debug: false)
    super(host, port, debug)
  end
end

config = Config.new
puts config.host   # localhost
puts config.port   # 3000

# OpenStruct - completely dynamic
require 'ostruct'

config = OpenStruct.new
config.host = "localhost"
config.port = 3000
config.debug = true

puts config.host  # localhost

# Or from hash
data = { host: "localhost", port: 3000 }
config2 = OpenStruct.new(data)
puts config2.port  # 3000

# Can add attributes dynamically
config.timeout = 30
puts config.timeout  # 30

# OpenStruct to hash
puts config.to_h
# {:host=>"localhost", :port=>3000, :debug=>true, :timeout=>30}

# Practical examples
# API response object
Response = Struct.new(:status, :body, :headers) do
  def success?
    status >= 200 && status < 300
  end

  def json
    JSON.parse(body)
  rescue JSON::ParserError
    {}
  end
end

response = Response.new(200, '{"message": "ok"}', {})
puts response.success?  # true

# Configuration object
require 'ostruct'

def load_config
  config = OpenStruct.new
  config.database = OpenStruct.new(
    host: "localhost",
    port: 5432,
    name: "myapp"
  )
  config.redis = OpenStruct.new(
    host: "localhost",
    port: 6379
  )
  config
end

config = load_config
puts config.database.host  # localhost
puts config.redis.port     # 6379

# Data transfer object
class UserDTO < Struct.new(:id, :name, :email, :created_at)
  def to_json
    {
      id: id,
      name: name,
      email: email,
      created_at: created_at.iso8601
    }
  end
end

dto = UserDTO.new(1, "Alice", "alice@example.com", Time.now)
puts dto.to_json
```

### Explanation

Structs and OpenStructs provide lightweight ways to create data objects without defining full classes.

**Struct characteristics:**

```ruby
# Creates a class
Point = Struct.new(:x, :y)
p = Point.new(1, 2)

# Access by name
puts p.x  # 1

# Access by index (like a tuple)
puts p[0]  # 1

# Iterate
p.each { |coord| puts coord }  # 1, 2

# Convert to array
puts p.to_a  # [1, 2]

# Convert to hash
puts p.to_h  # {:x=>1, :y=>2}

# Equality
p2 = Point.new(1, 2)
puts p == p2  # true
```

**Adding methods to Structs:**

```ruby
# Block form
Person = Struct.new(:first_name, :last_name) do
  def full_name
    "#{first_name} #{last_name}"
  end
end

# Or inherit and extend
class Employee < Struct.new(:name, :department, :salary)
  def give_raise(amount)
    self.salary += amount
  end

  def to_s
    "#{name} (#{department}): $#{salary}"
  end
end

emp = Employee.new("Alice", "Engineering", 100_000)
emp.give_raise(10_000)
puts emp  # Alice (Engineering): $110000
```

**OpenStruct characteristics:**

```ruby
require 'ostruct'

# Completely dynamic
person = OpenStruct.new
person.name = "Alice"
person.age = 30

# Or from hash
person = OpenStruct.new(name: "Alice", age: 30)

# Delete attributes
person.delete_field(:age)

# Check if attribute exists
puts person.age  # nil (doesn't exist)

# Convert to hash
puts person.to_h  # {:name=>"Alice"}

# Nested OpenStructs
person = OpenStruct.new(
  name: "Alice",
  address: OpenStruct.new(
    street: "123 Main St",
    city: "Springfield"
  )
)

puts person.address.city  # Springfield
```

**Performance comparison:**

```ruby
require 'benchmark'
require 'ostruct'

# Struct is much faster
PersonStruct = Struct.new(:name, :age)

n = 100_000
Benchmark.bm do |x|
  x.report("Struct:") do
    n.times { PersonStruct.new("Alice", 30) }
  end

  x.report("OpenStruct:") do
    n.times { OpenStruct.new(name: "Alice", age: 30) }
  end

  x.report("Hash:") do
    n.times { { name: "Alice", age: 30 } }
  end
end

# Struct is ~10x faster than OpenStruct
# Hash is fastest but no dot notation
```

**When to use each:**

**Use Struct when:**
- You know the attributes upfront
- Performance matters
- You want a lightweight class
- You need immutable-like behavior (with freeze)

**Use OpenStruct when:**
- Attributes are dynamic/unknown
- Convenience over performance
- Working with JSON/API responses
- Rapid prototyping

**Use regular class when:**
- Complex behavior needed
- Validation required
- Performance critical
- Part of public API

**Watch out:**
- OpenStruct is much slower than Struct
- Structs can be modified (use `freeze` for immutability)
- OpenStruct requires `require 'ostruct'`
- Typos in OpenStruct attribute names create new attributes
- Structs are classes - can be inherited and extended

---

## 17.4 Constants and Freezing Objects

Ruby constants and frozen objects provide immutability, though Ruby's approach is different from Python's. While Python uses conventions, Ruby has built-in mechanisms for preventing changes.

**Key Similarities:**
- Both languages support immutable objects
- Both use naming conventions for constants
- Both can freeze/make immutable strings and collections
- Both have const-like behavior in some contexts

**Key Differences:**
- Ruby constants can be reassigned (with warning)
- Ruby's `freeze` makes objects immutable at runtime
- Python's immutability is more type-based (tuples vs lists)
- Ruby has more explicit freezing mechanisms

### Python Example

```python
# Constants (convention only)
MAX_SIZE = 100
API_KEY = "secret"

# Nothing prevents reassignment
MAX_SIZE = 200  # No error, just convention

# Immutable types
immutable_tuple = (1, 2, 3)
# immutable_tuple[0] = 5  # Error

# But mutable elements can change
mutable_list = [1, 2, 3]
mixed_tuple = (mutable_list, "string")
mixed_tuple[0].append(4)  # Works!
print(mixed_tuple)  # ([1, 2, 3, 4], 'string')

# Frozen sets (immutable)
frozen = frozenset([1, 2, 3])
# frozen.add(4)  # Error

# Immutable strings (default)
text = "hello"
# text[0] = "H"  # Error
new_text = text.upper()  # Creates new string

# dataclass with frozen=True
from dataclasses import dataclass

@dataclass(frozen=True)
class Point:
    x: int
    y: int

p = Point(1, 2)
# p.x = 5  # Error - frozen dataclass

# Named constants namespace
from types import SimpleNamespace

CONST = SimpleNamespace(
    MAX_SIZE=100,
    MIN_SIZE=10
)
# Can still be modified (not truly const)
CONST.MAX_SIZE = 200  # Works (not ideal)

# Using __slots__ for efficiency
class ImmutablePerson:
    __slots__ = ('name', 'age')

    def __init__(self, name, age):
        object.__setattr__(self, 'name', name)
        object.__setattr__(self, 'age', age)

    def __setattr__(self, name, value):
        raise AttributeError("Cannot modify immutable object")

person = ImmutablePerson("Alice", 30)
# person.name = "Bob"  # Error
```

### Ruby Equivalent

```ruby
# Constants (by convention: UPPERCASE)
MAX_SIZE = 100
API_KEY = "secret"

# Ruby warns but allows reassignment
MAX_SIZE = 200  # Warning: already initialized constant

# Suppressing warnings
def change_constant
  Object.const_set(:MAX_SIZE, 300)  # No warning
end

# Accessing constants
puts MAX_SIZE          # 300
puts Object::MAX_SIZE  # 300

# Freezing objects
array = [1, 2, 3]
array.freeze

# Can't modify frozen objects
begin
  array << 4
rescue FrozenError => e
  puts "Cannot modify frozen array"
end

# Check if frozen
puts array.frozen?  # true

# Freeze strings
text = "hello"
text.freeze
begin
  text.upcase!  # upcase! modifies in place
rescue FrozenError
  puts "Cannot modify frozen string"
end

new_text = text.upcase  # Creates new string (works)
puts new_text  # HELLO

# Freeze hashes
config = { host: "localhost", port: 3000 }
config.freeze

begin
  config[:timeout] = 30
rescue FrozenError
  puts "Cannot modify frozen hash"
end

# Shallow freeze (elements not frozen)
array = [[1, 2], [3, 4]]
array.freeze
array[0] << 5  # Works! Inner array not frozen
puts array  # [[1, 2, 5], [3, 4]]

# Deep freeze (recursive)
def deep_freeze(obj)
  obj.freeze
  case obj
  when Hash
    obj.each_value { |v| deep_freeze(v) }
  when Array
    obj.each { |v| deep_freeze(v) }
  end
  obj
end

array = [[1, 2], [3, 4]]
deep_freeze(array)

begin
  array[0] << 5
rescue FrozenError
  puts "Inner arrays are frozen too"
end

# Immutable value objects
class Point
  attr_reader :x, :y

  def initialize(x, y)
    @x = x
    @y = y
    freeze  # Freeze immediately
  end

  # Return new instances instead of modifying
  def move(dx, dy)
    Point.new(@x + dx, @y + dy)
  end

  def ==(other)
    other.is_a?(Point) && x == other.x && y == other.y
  end
end

p1 = Point.new(10, 20)
begin
  p1.instance_variable_set(:@x, 15)
rescue FrozenError
  puts "Cannot modify frozen point"
end

p2 = p1.move(5, 5)
puts "p1: #{p1.x}, #{p1.y}"  # 10, 20
puts "p2: #{p2.x}, #{p2.y}"  # 15, 25

# Frozen string literals (Ruby 3+)
# frozen_string_literal: true
# All string literals in file are automatically frozen

# Constants in modules/classes
module Constants
  MAX_USERS = 1000
  MIN_AGE = 18

  ERRORS = {
    not_found: "Record not found",
    invalid: "Invalid data"
  }.freeze  # Freeze the hash
end

puts Constants::MAX_USERS
puts Constants::ERRORS[:not_found]

# Using Struct for immutable data
Person = Struct.new(:name, :age) do
  def initialize(name, age)
    super
    freeze
  end
end

person = Person.new("Alice", 30)
begin
  person.name = "Bob"
rescue FrozenError
  puts "Cannot modify frozen struct"
end

# dup and clone with frozen objects
original = [1, 2, 3].freeze

# dup creates unfrozen copy
copy = original.dup
copy << 4  # Works
puts copy  # [1, 2, 3, 4]

# clone preserves frozen state (and singleton methods)
clone = original.clone
puts clone.frozen?  # true
begin
  clone << 4
rescue FrozenError
  puts "Clone is also frozen"
end
```

### Explanation

Ruby provides several mechanisms for immutability, from constants to frozen objects.

**Constants:**

```ruby
# Define constant
MAX_SIZE = 100

# Nested constants
module MyApp
  VERSION = "1.0.0"

  module Config
    DATABASE = "postgresql"
  end
end

puts MyApp::VERSION
puts MyApp::Config::DATABASE

# Dynamic constant access
puts MyApp.const_get(:VERSION)  # 1.0.0

# Check if constant defined
puts MyApp.const_defined?(:VERSION)  # true

# List constants
puts MyApp.constants  # [:VERSION, :Config]
```

**Freezing:**

```ruby
# Freeze makes object immutable
obj = [1, 2, 3]
obj.freeze

# All mutating methods raise FrozenError
# obj << 4         # Error
# obj.clear        # Error
# obj[0] = 5       # Error

# Non-mutating methods still work
new_array = obj + [4]  # [1, 2, 3, 4] (new array)
```

**Frozen string literals:**

```ruby
# At top of file
# frozen_string_literal: true

# All strings automatically frozen
str = "hello"
puts str.frozen?  # true

# Forces immutability, improves performance
# Ruby doesn't create duplicate strings
```

**Value objects (immutable):**

```ruby
class Money
  attr_reader :amount, :currency

  def initialize(amount, currency)
    @amount = amount
    @currency = currency
    freeze
  end

  def +(other)
    raise ArgumentError, "Currency mismatch" unless currency == other.currency
    Money.new(amount + other.amount, currency)
  end

  def *(multiplier)
    Money.new(amount * multiplier, currency)
  end

  def to_s
    "#{amount} #{currency}"
  end
end

price = Money.new(100, "USD")
tax = price * 0.1
total = price + tax
puts total  # 110.0 USD
```

**When to use freezing:**

**Use freeze for:**
- Constants that should never change
- Configuration objects
- Value objects (immutable data types)
- Preventing accidental mutations
- Security (preventing tampering)

**Performance considerations:**

```ruby
# Frozen strings are more memory efficient
# Ruby reuses same frozen string

1000.times do
  "hello".freeze  # Only one string object in memory
end

# With frozen_string_literal: true, even better
# All literal strings automatically frozen and deduplicated
```

**Watch out:**
- Freeze is shallow by default
- Constants can be reassigned (with warning)
- Frozen objects can't be unfrozen
- `dup` removes frozen state, `clone` preserves it
- Freezing impacts performance (very slightly)

---

## 17.5 Concurrency

Ruby provides several concurrency primitives: Threads, Fibers, and Ractors (Ruby 3.0+). Python has similar concepts with threads, asyncio, and multiprocessing.

### 17.5.1 Threads

Ruby threads are similar to Python threads - they share memory but are limited by the GIL (Global Interpreter Lock) in MRI Ruby.

**Key Similarities:**
- Both have OS-level threads
- Both have GIL in main implementations (MRI Ruby, CPython)
- Both share memory between threads
- Both need synchronization primitives

**Key Differences:**
- Ruby Thread API is simpler
- Ruby has Thread-local variables built-in
- Different synchronization primitives
- Ruby threads can be more easily managed

#### Python Example

```python
import threading
import time
import queue

# Basic thread
def worker(name):
    print(f"Thread {name} starting")
    time.sleep(1)
    print(f"Thread {name} finishing")

thread = threading.Thread(target=worker, args=("A",))
thread.start()
thread.join()  # Wait for completion

# Multiple threads
threads = []
for i in range(3):
    t = threading.Thread(target=worker, args=(f"Worker-{i}",))
    threads.append(t)
    t.start()

for t in threads:
    t.join()

# Thread with return value (using queue)
result_queue = queue.Queue()

def calculate(n):
    result = n * n
    result_queue.put(result)

thread = threading.Thread(target=calculate, args=(5,))
thread.start()
thread.join()
print(result_queue.get())  # 25

# Thread synchronization with Lock
counter = 0
lock = threading.Lock()

def increment():
    global counter
    for _ in range(100000):
        with lock:
            counter += 1

threads = [threading.Thread(target=increment) for _ in range(10)]
for t in threads:
    t.start()
for t in threads:
    t.join()

print(counter)  # 1000000

# Thread-local storage
thread_local = threading.local()

def process():
    thread_local.value = threading.current_thread().name
    print(f"Thread local value: {thread_local.value}")

threads = [threading.Thread(target=process) for _ in range(3)]
for t in threads:
    t.start()
for t in threads:
    t.join()
```

#### Ruby Equivalent

```ruby
# Basic thread
thread = Thread.new do
  puts "Thread starting"
  sleep 1
  puts "Thread finishing"
end

thread.join  # Wait for completion

# Multiple threads
threads = 3.times.map do |i|
  Thread.new do
    puts "Worker-#{i} starting"
    sleep 1
    puts "Worker-#{i} finishing"
  end
end

threads.each(&:join)

# Thread with return value
thread = Thread.new do
  result = 5 * 5
  result  # Last expression is return value
end

puts thread.value  # Joins and returns value: 25

# Thread synchronization with Mutex
counter = 0
mutex = Mutex.new

threads = 10.times.map do
  Thread.new do
    100_000.times do
      mutex.synchronize do
        counter += 1
      end
    end
  end
end

threads.each(&:join)
puts counter  # 1000000

# Thread-local variables
3.times.map do |i|
  Thread.new do
    Thread.current[:name] = "Worker-#{i}"
    Thread.current[:value] = i * 10

    sleep 0.1

    puts "Thread: #{Thread.current[:name]}, Value: #{Thread.current[:value]}"
  end
end.each(&:join)

# Thread exception handling
thread = Thread.new do
  raise "Something went wrong"
end

begin
  thread.join
rescue => e
  puts "Thread raised: #{e.message}"
end

# Thread pool pattern
require 'thread'

queue = Queue.new
results = Queue.new

# Add work to queue
10.times { |i| queue << i }

# Worker threads
workers = 3.times.map do
  Thread.new do
    while !queue.empty?
      begin
        number = queue.pop(true)  # non-blocking
        result = number * number
        results << result
        puts "Thread #{Thread.current.object_id}: #{number} * #{number} = #{result}"
      rescue ThreadError
        # Queue empty
        break
      end
    end
  end
end

workers.each(&:join)

# Collect results
final_results = []
final_results << results.pop until results.empty?
puts "Results: #{final_results.sort}"
```

### 17.5.2 Fibers

Fibers are lightweight concurrency primitives that enable cooperative multitasking, similar to Python's generators or coroutines.

#### Python Example

```python
# Generator (similar to Fiber)
def counter(start):
    n = start
    while True:
        value = yield n
        if value is not None:
            n = value
        else:
            n += 1

c = counter(0)
print(next(c))  # 0
print(next(c))  # 1
print(c.send(10))  # 10
print(next(c))  # 11

# Coroutine
async def async_operation():
    print("Starting")
    await asyncio.sleep(1)
    print("Finished")
    return 42

# asyncio.run(async_operation())
```

#### Ruby Equivalent

```ruby
# Basic Fiber
fiber = Fiber.new do
  puts "Fiber started"
  Fiber.yield "First value"
  puts "Fiber resumed"
  Fiber.yield "Second value"
  puts "Fiber ending"
  "Final value"
end

puts fiber.resume  # "Fiber started", returns "First value"
puts fiber.resume  # "Fiber resumed", returns "Second value"
puts fiber.resume  # "Fiber ending", returns "Final value"

# Fiber for iteration
def fibonacci
  Fiber.new do
    a, b = 0, 1
    loop do
      Fiber.yield a
      a, b = b, a + b
    end
  end
end

fib = fibonacci
10.times do
  print "#{fib.resume} "
end
puts
# 0 1 1 2 3 5 8 13 21 34

# Bidirectional communication
fiber = Fiber.new do
  x = Fiber.yield "What's your name?"
  Fiber.yield "Hello, #{x}!"
  y = Fiber.yield "What's your age?"
  "You are #{y} years old"
end

puts fiber.resume           # What's your name?
puts fiber.resume("Alice")  # Hello, Alice!
puts fiber.resume           # What's your age?
puts fiber.resume(30)       # You are 30 years old

# Fiber for state machine
class StateMachine
  def initialize
    @fiber = Fiber.new do
      loop do
        state_start
        state_running
        state_stopped
      end
    end
  end

  def next_state
    @fiber.resume
  end

  private

  def state_start
    Fiber.yield :starting
  end

  def state_running
    Fiber.yield :running
  end

  def state_stopped
    Fiber.yield :stopped
  end
end

machine = StateMachine.new
6.times { puts machine.next_state }
# starting, running, stopped, starting, running, stopped
```

### 17.5.3 Ractors (Ruby 3.0+)

Ractors are Ruby's answer to true parallelism, similar to Python's multiprocessing. They don't share memory and avoid the GIL limitation.

#### Python Example

```python
from multiprocessing import Process, Queue

def worker(queue, n):
    result = n * n
    queue.put(result)

if __name__ == '__main__':
    queue = Queue()
    processes = []

    for i in range(5):
        p = Process(target=worker, args=(queue, i))
        processes.append(p)
        p.start()

    for p in processes:
        p.join()

    results = []
    while not queue.empty():
        results.append(queue.get())

    print(results)
```

#### Ruby Equivalent

```ruby
# Ractors (Ruby 3.0+)
# Note: Ractors are still experimental

# Basic Ractor
r = Ractor.new do
  result = 5 * 5
  result  # Send result back
end

puts r.take  # 25 (receive result)

# Multiple Ractors
ractors = 5.times.map do |i|
  Ractor.new(i) do |n|
    n * n
  end
end

results = ractors.map(&:take)
puts results  # [0, 1, 4, 9, 16]

# Sending messages to Ractors
r = Ractor.new do
  received = Ractor.receive
  received.upcase
end

r.send("hello")
puts r.take  # HELLO

# Ractor communication
pipe = Ractor.new do
  loop do
    value = Ractor.receive
    Ractor.yield value * 2
  end
end

pipe.send(5)
puts pipe.take  # 10

pipe.send(10)
puts pipe.take  # 20

# Shareable objects
# Ractors can only share immutable objects
frozen_array = [1, 2, 3].freeze

r = Ractor.new(frozen_array) do |arr|
  arr.sum
end

puts r.take  # 6

# This would fail (mutable object):
# mutable_array = [1, 2, 3]
# r = Ractor.new(mutable_array) do |arr|  # Error!
#   arr.sum
# end
```

### Explanation

**Threads:**
- Shared memory
- Limited by GIL (in MRI Ruby)
- Good for I/O-bound tasks
- Simple API

**Fibers:**
- Cooperative multitasking
- No parallelism (single thread)
- Lightweight (can create thousands)
- Manual yielding
- Good for generators/coroutines

**Ractors:**
- True parallelism (no GIL)
- No shared memory
- Message passing
- Ruby 3.0+ only
- Still experimental

**Watch out:**
- Threads don't provide true parallelism in MRI (GIL)
- Fibers require manual control (cooperative)
- Ractors can only share immutable objects
- Ractors are experimental and may change

---

## 17.6 Pattern Matching (Ruby 3.0+)

Ruby 3.0 introduced pattern matching, similar to Python 3.10's structural pattern matching but with Ruby-specific features.

**Key Similarities:**
- Both destructure data structures
- Both support value matching
- Both have guard clauses
- Both are expressions (return values)

**Key Differences:**
- Ruby uses `case`/`in` syntax
- Different syntax for array/hash matching
- Ruby has pin operator (^) for variable matching
- Ruby's pattern matching is more object-oriented

### Python Example

```python
# Python 3.10+ pattern matching
def process_command(command):
    match command:
        case ["quit"]:
            return "Quitting"
        case ["load", filename]:
            return f"Loading {filename}"
        case ["save", filename]:
            return f"Saving {filename}"
        case ["move", x, y]:
            return f"Moving to ({x}, {y})"
        case _:
            return "Unknown command"

print(process_command(["load", "file.txt"]))  # Loading file.txt
print(process_command(["move", 10, 20]))      # Moving to (10, 20)

# Matching with dict
def process_user(user):
    match user:
        case {"name": name, "age": age} if age >= 18:
            return f"{name} is an adult"
        case {"name": name, "age": age}:
            return f"{name} is a minor"
        case {"name": name}:
            return f"Age unknown for {name}"
        case _:
            return "Invalid user"

print(process_user({"name": "Alice", "age": 25}))  # Alice is an adult

# Matching types
def process_value(value):
    match value:
        case int(x):
            return f"Integer: {x}"
        case str(s):
            return f"String: {s}"
        case [*items]:
            return f"List with {len(items)} items"
        case _:
            return "Unknown type"

print(process_value(42))        # Integer: 42
print(process_value("hello"))   # String: hello
```

### Ruby Equivalent

```ruby
# Ruby 3.0+ pattern matching
def process_command(command)
  case command
  in ["quit"]
    "Quitting"
  in ["load", filename]
    "Loading #{filename}"
  in ["save", filename]
    "Saving #{filename}"
  in ["move", x, y]
    "Moving to (#{x}, #{y})"
  else
    "Unknown command"
  end
end

puts process_command(["load", "file.txt"])  # Loading file.txt
puts process_command(["move", 10, 20])      # Moving to (10, 20)

# Matching with hashes
def process_user(user)
  case user
  in { name:, age: } if age >= 18
    "#{name} is an adult"
  in { name:, age: }
    "#{name} is a minor"
  in { name: }
    "Age unknown for #{name}"
  else
    "Invalid user"
  end
end

puts process_user({ name: "Alice", age: 25 })  # Alice is an adult
puts process_user({ name: "Bob", age: 15 })    # Bob is a minor

# Matching types
def process_value(value)
  case value
  in Integer => x
    "Integer: #{x}"
  in String => s
    "String: #{s}"
  in Array => items
    "Array with #{items.length} items"
  else
    "Unknown type"
  end
end

puts process_value(42)        # Integer: 42
puts process_value("hello")   # String: hello
puts process_value([1, 2, 3]) # Array with 3 items

# Pin operator (^) for matching against variables
expected_status = 200

response = { status: 200, body: "OK" }

case response
in { status: ^expected_status, body: }
  puts "Success: #{body}"
in { status: code, body: }
  puts "Unexpected status: #{code}"
end
# Success: OK

# Array patterns with rest
case [1, 2, 3, 4, 5]
in [first, *middle, last]
  puts "First: #{first}, Middle: #{middle}, Last: #{last}"
end
# First: 1, Middle: [2, 3, 4], Last: 5

# Nested patterns
user = {
  name: "Alice",
  address: {
    city: "New York",
    zip: "10001"
  }
}

case user
in { name:, address: { city:, zip: } }
  puts "#{name} lives in #{city}, #{zip}"
end
# Alice lives in New York, 10001

# Alternative pattern (|)
case command
in ["quit"] | ["exit"]
  "Quitting application"
in ["help"] | ["?"]
  "Showing help"
else
  "Unknown command"
end

# Find pattern (Ruby 3.1+)
case [1, 2, 3, 4, 5]
in [*, a, b, *] if a + b == 7
  puts "Found #{a} and #{b}"
end
# Found 3 and 4

# Rightward assignment (=>)
case value
in Integer => x
  puts "Got integer: #{x}"
end

# One-line pattern matching
[1, 2, 3] => [a, b, c]
puts "a=#{a}, b=#{b}, c=#{c}"  # a=1, b=2, c=3

{ name: "Alice", age: 30 } => { name:, age: }
puts "#{name} is #{age}"  # Alice is 30
```

### Explanation

Pattern matching provides a powerful way to destructure and match data structures. It's particularly useful for:

**Array matching:**
```ruby
case array
in []
  "Empty array"
in [x]
  "Single element: #{x}"
in [first, second]
  "Two elements: #{first}, #{second}"
in [head, *tail]
  "Head: #{head}, Tail: #{tail}"
end
```

**Hash matching:**
```ruby
case hash
in {}
  "Empty hash"
in { name: }
  "Has name: #{name}"
in { name:, age: }
  "Has name and age: #{name}, #{age}"
in { name:, **rest }
  "Name: #{name}, Rest: #{rest}"
end
```

**Guard clauses:**
```ruby
case value
in x if x > 10
  "Greater than 10"
in x if x.even?
  "Even number"
else
  "Other"
end
```

**Type matching:**
```ruby
case object
in Integer
  "Integer"
in String
  "String"
in Array
  "Array"
end
```

**Watch out:**
- Pattern matching raises `NoMatchingPatternError` if no pattern matches (unless you have `else`)
- Use `case`/`in`, not `case`/`when` for pattern matching
- Pin operator `^` matches against variable value
- Pattern matching is experimental and syntax may evolve

---

## 17.7 Functional Programming with Ruby

While Ruby is primarily object-oriented, it supports functional programming concepts like immutability, higher-order functions, and composition.

**Key Similarities:**
- Both support first-class functions
- Both have map/filter/reduce
- Both support function composition
- Both can use lambda/anonymous functions

**Key Differences:**
- Ruby emphasizes blocks over standalone functions
- Python has more functional builtins (itertools, functools)
- Ruby's Enumerable is more object-oriented
- Different syntax for currying and partial application

### Python Example

```python
from functools import reduce, partial
from operator import add, mul

# Higher-order functions
numbers = [1, 2, 3, 4, 5]

# Map
squared = list(map(lambda x: x ** 2, numbers))
print(squared)  # [1, 4, 9, 16, 25]

# Filter
evens = list(filter(lambda x: x % 2 == 0, numbers))
print(evens)  # [2, 4]

# Reduce
total = reduce(add, numbers)
print(total)  # 15

# Function composition
def compose(*functions):
    def inner(arg):
        for f in reversed(functions):
            arg = f(arg)
        return arg
    return inner

double = lambda x: x * 2
increment = lambda x: x + 1
square = lambda x: x ** 2

pipeline = compose(square, increment, double)
print(pipeline(3))  # ((3 * 2) + 1) ** 2 = 49

# Partial application
def power(base, exponent):
    return base ** exponent

square_func = partial(power, exponent=2)
cube_func = partial(power, exponent=3)

print(square_func(5))  # 25
print(cube_func(5))    # 125

# Currying (manual)
def curry_add(x):
    def inner(y):
        return x + y
    return inner

add_5 = curry_add(5)
print(add_5(3))  # 8

# Immutable operations
original = [1, 2, 3]
new_list = [*original, 4]  # Don't modify original
print(original)  # [1, 2, 3]
print(new_list)  # [1, 2, 3, 4]
```

### Ruby Equivalent

```ruby
# Higher-order functions
numbers = [1, 2, 3, 4, 5]

# Map
squared = numbers.map { |x| x ** 2 }
puts squared.inspect  # [1, 4, 9, 16, 25]

# Filter (select)
evens = numbers.select { |x| x.even? }
puts evens.inspect  # [2, 4]

# Reduce
total = numbers.reduce(:+)
puts total  # 15

# Or with block
total = numbers.reduce { |sum, n| sum + n }
puts total  # 15

# Function composition
def compose(*functions)
  ->(arg) do
    functions.reverse.reduce(arg) { |result, f| f.call(result) }
  end
end

double = ->(x) { x * 2 }
increment = ->(x) { x + 1 }
square = ->(x) { x ** 2 }

pipeline = compose(square, increment, double)
puts pipeline.call(3)  # ((3 * 2) + 1) ** 2 = 49

# Method chaining (Ruby's idiomatic approach)
result = [1, 2, 3, 4, 5]
  .map { |x| x * 2 }
  .select { |x| x > 5 }
  .reduce(:+)
puts result  # 6 + 8 + 10 = 24

# Currying
add = ->(x, y) { x + y }
curried_add = add.curry
add_5 = curried_add.call(5)
puts add_5.call(3)  # 8

# Or define curried from start
curried_multiply = ->(x) { ->(y) { x * y } }
times_3 = curried_multiply.call(3)
puts times_3.call(4)  # 12

# Partial application
def power(base, exponent)
  base ** exponent
end

square_func = method(:power).curry.call(exponent: 2)
cube_func = method(:power).curry.call(exponent: 3)

# Using lambdas for partial application
square_func = ->(base) { power(base, 2) }
cube_func = ->(base) { power(base, 3) }

puts square_func.call(5)  # 25
puts cube_func.call(5)    # 125

# Immutable operations
original = [1, 2, 3].freeze
new_list = [*original, 4]  # Don't modify original
puts original.inspect  # [1, 2, 3]
puts new_list.inspect  # [1, 2, 3, 4]

# Functional data transformations
users = [
  { name: "Alice", age: 30, active: true },
  { name: "Bob", age: 25, active: false },
  { name: "Charlie", age: 35, active: true }
]

result = users
  .select { |u| u[:active] }
  .map { |u| u[:name] }
  .sort
puts result.inspect  # ["Alice", "Charlie"]

# Function as value
operations = {
  add: ->(a, b) { a + b },
  subtract: ->(a, b) { a - b },
  multiply: ->(a, b) { a * b },
  divide: ->(a, b) { a / b }
}

puts operations[:add].call(10, 5)      # 15
puts operations[:multiply].call(10, 5) # 50

# Lazy evaluation
range = (1..Float::INFINITY).lazy
  .select { |n| n % 3 == 0 }
  .map { |n| n * n }
  .first(5)
puts range.inspect  # [9, 36, 81, 144, 225]

# Memoization (functional pattern)
def memoize(func)
  cache = {}
  ->(arg) do
    cache[arg] ||= func.call(arg)
  end
end

fibonacci = ->(n) do
  n <= 1 ? n : fibonacci.call(n - 1) + fibonacci.call(n - 2)
end

fast_fib = memoize(fibonacci)
puts fast_fib.call(10)  # 55

# Monadic operations (then chain)
result = "hello"
  .then { |s| s.upcase }
  .then { |s| s.reverse }
  .then { |s| s + "!" }
puts result  # !OLLEH!

# Tap for side effects in chain
numbers = [1, 2, 3, 4, 5]
  .tap { |arr| puts "Starting with: #{arr}" }
  .map { |n| n * 2 }
  .tap { |arr| puts "After doubling: #{arr}" }
  .select { |n| n > 5 }
puts numbers.inspect
# Starting with: [1, 2, 3, 4, 5]
# After doubling: [2, 4, 6, 8, 10]
# [6, 8, 10]
```

### Explanation

Ruby supports functional programming through:

**Lambdas and Procs:**
```ruby
# Lambda
add = ->(x, y) { x + y }
puts add.call(3, 5)  # 8

# Proc
multiply = Proc.new { |x, y| x * y }
puts multiply.call(3, 5)  # 15
```

**Method chaining:**
```ruby
result = [1, 2, 3, 4, 5]
  .map(&:to_s)
  .select { |s| s.to_i.even? }
  .join(", ")
puts result  # "2, 4"
```

**Currying:**
```ruby
add = ->(x, y, z) { x + y + z }
curried = add.curry

add_1 = curried.call(1)
add_1_2 = add_1.call(2)
puts add_1_2.call(3)  # 6
```

**Immutable operations:**
```ruby
class ImmutableList
  def initialize(items = [])
    @items = items.freeze
  end

  def add(item)
    ImmutableList.new([*@items, item])
  end

  def map(&block)
    ImmutableList.new(@items.map(&block))
  end

  def to_a
    @items.dup
  end
end

list = ImmutableList.new([1, 2, 3])
new_list = list.add(4).map { |x| x * 2 }
puts list.to_a.inspect      # [1, 2, 3]
puts new_list.to_a.inspect  # [2, 4, 6, 8]
```

**Watch out:**
- Procs and lambdas behave differently (return, arity)
- Ruby favors blocks over passing functions
- Method chaining is idiomatic, pure functional style less so
- Performance: functional style can be slower
- Use lazy evaluation for infinite sequences

---

## Chapter Summary

Advanced Ruby topics covered:

**Metaprogramming:**
- `define_method` - Define methods dynamically
- `method_missing` - Handle undefined methods
- `send` - Call methods by name
- `class_eval`/`instance_eval` - Evaluate code in different contexts

**Refinements:**
- Scoped monkey patching
- Safer than global modifications
- Activated with `using`

**Structs and OpenStructs:**
- Lightweight data structures
- Struct for known attributes (faster)
- OpenStruct for dynamic attributes

**Immutability:**
- Constants (can be reassigned with warning)
- `freeze` for runtime immutability
- Shallow vs deep freezing
- Value objects pattern

**Concurrency:**
- Threads (shared memory, GIL-limited)
- Fibers (cooperative multitasking)
- Ractors (true parallelism, Ruby 3.0+)

**Pattern Matching (Ruby 3.0+):**
- `case`/`in` syntax
- Array and hash destructuring
- Type matching and guards
- Pin operator (^)

**Functional Programming:**
- Lambdas and currying
- Function composition
- Method chaining (idiomatic)
- Immutable operations
- Lazy evaluation

These advanced features make Ruby extremely flexible and expressive, though they should be used judiciously. Most day-to-day Ruby programming doesn't require these techniques, but understanding them helps when reading library code and advanced codebases.
