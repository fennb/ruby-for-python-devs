# Chapter 7: Methods

## 7.1 Defining Methods (`def`...`end`)

Methods in Ruby and Python serve the same fundamental purpose: encapsulating reusable code. Both languages use the `def` keyword to define methods/functions, but Ruby's syntax and conventions differ in several important ways.

**Key Similarities:**
- Both use `def` keyword to define methods
- Both support parameters and return values
- Both allow default arguments and variable-length arguments

**Key Differences:**
- Ruby uses `end` instead of indentation to close method definitions
- Ruby methods don't need `self` as the first parameter (unless defining class methods)
- Ruby has implicit returns (last expression is automatically returned)
- Ruby allows `?`, `!`, and `=` in method names for special meanings
- Parentheses are optional in Ruby for both definition and calls

### Python Example

```python
# Basic function definition
def greet(name):
    return f"Hello, {name}!"

result = greet("Alice")
# "Hello, Alice!"

# Multiple parameters
def add(a, b):
    return a + b

sum_result = add(5, 3)
# 8

# No parameters
def get_random():
    import random
    return random.randint(1, 100)

# Multi-line function
def process_data(data):
    cleaned = data.strip()
    uppercased = cleaned.upper()
    return uppercased

# Function with side effects (no return)
def print_greeting(name):
    print(f"Hello, {name}!")
    # Implicitly returns None

# Method in a class
class User:
    def __init__(self, name):
        self.name = name

    def greet(self):  # self is required
        return f"Hello, I'm {self.name}"

user = User("Alice")
user.greet()
# "Hello, I'm Alice"
```

### Ruby Equivalent

```ruby
# Basic method definition
def greet(name)
  "Hello, #{name}!"
end

result = greet("Alice")
# "Hello, Alice!"

# Multiple parameters
def add(a, b)
  a + b
end

sum_result = add(5, 3)
# 8

# No parameters
def get_random
  rand(1..100)
end

# Multi-line method
def process_data(data)
  cleaned = data.strip
  uppercased = cleaned.upcase
  uppercased  # Last expression is returned
end

# Method with side effects
def print_greeting(name)
  puts "Hello, #{name}!"
  # Implicitly returns nil (puts returns nil)
end

# Method in a class
class User
  def initialize(name)
    @name = name
  end

  def greet  # No self needed
    "Hello, I'm #{@name}"
  end
end

user = User.new("Alice")
user.greet
# "Hello, I'm Alice"
```

### Explanation

Ruby method definitions begin with `def` and end with the `end` keyword. Unlike Python, which uses indentation to denote code blocks, Ruby explicitly marks the end of the method.

**Basic syntax:**
```ruby
def method_name(parameter1, parameter2)
  # method body
  # last expression is automatically returned
end
```

**Key differences from Python:**

1. **No colon:** Ruby doesn't use `:` after the parameter list
2. **End keyword:** Methods must end with `end`
3. **No self parameter:** Instance methods don't explicitly receive `self` (but it's available)
4. **Implicit return:** The last evaluated expression is automatically returned
5. **Optional parentheses:** Both in definition and when calling

**Method visibility:**
```ruby
class Example
  # Public by default
  def public_method
    "Anyone can call this"
  end

  private

  def private_method
    "Only callable within this object"
  end

  protected

  def protected_method
    "Callable by this class and subclasses"
  end
end
```

**Calling methods:**
```ruby
# With parentheses (explicit)
greet("Alice")

# Without parentheses (optional, but be careful)
greet "Alice"

# No arguments - parentheses optional
get_random()
get_random  # More common
```

**Best Practice:**
- Use parentheses when defining methods with parameters
- Use parentheses when calling methods with arguments (for clarity)
- Omit parentheses for methods with no arguments
- Keep methods short and focused (Single Responsibility Principle)
- Use 2-space indentation for method bodies

---

## 7.2 Implicit Returns vs Explicit `return`

One of the most significant differences between Ruby and Python is how methods return values. Python requires an explicit `return` statement, while Ruby automatically returns the last evaluated expression.

**Key Similarities:**
- Both can use explicit `return` statements
- Both can return any type of value
- Both can return early from a method

**Key Differences:**
- Ruby has implicit returns (last expression is automatically returned)
- Python returns `None` without explicit `return`; Ruby returns the last expression
- Ruby developers typically avoid explicit `return` unless needed
- Explicit `return` in Ruby is used for early returns or clarity

### Python Example

```python
# Explicit return required
def add(a, b):
    result = a + b
    return result  # Must use return

# Without return, you get None
def add_broken(a, b):
    result = a + b
    # No return statement - returns None!

add_broken(5, 3)  # None

# Multiple return points
def classify_number(n):
    if n < 0:
        return "negative"
    elif n == 0:
        return "zero"
    else:
        return "positive"

# Returning early
def process_data(data):
    if not data:
        return None  # Early return

    cleaned = data.strip()
    return cleaned.upper()

# Function that returns None explicitly
def log_message(message):
    print(message)
    return None  # Explicit, but unnecessary

# Multiple statements - only last return matters
def calculate(x):
    temp1 = x * 2
    temp2 = temp1 + 5
    temp3 = temp2 ** 2
    return temp3  # Only this is returned
```

### Ruby Equivalent

```ruby
# Implicit return (most common)
def add(a, b)
  result = a + b
  result  # Automatically returned (no 'return' needed)
end

# Even simpler - last expression is returned
def add(a, b)
  a + b  # This is automatically returned
end

# Multiple return points (explicit return for early exit)
def classify_number(n)
  return "negative" if n < 0
  return "zero" if n == 0
  "positive"  # Implicit return for last case
end

# Returning early (explicit return)
def process_data(data)
  return nil if data.nil? || data.empty?  # Early return

  cleaned = data.strip
  cleaned.upcase  # Implicit return
end

# Method that returns nil implicitly
def log_message(message)
  puts message  # puts returns nil, so method returns nil
end

# Multiple statements - last expression is returned
def calculate(x)
  temp1 = x * 2
  temp2 = temp1 + 5
  temp3 = temp2 ** 2
  temp3  # Automatically returned (or just write the expression)
end

# Can be written as:
def calculate(x)
  temp1 = x * 2
  temp2 = temp1 + 5
  temp2 ** 2  # Last expression, automatically returned
end
```

### Explanation

Ruby's implicit return means the value of the last expression evaluated in a method is automatically returned. You don't need (and typically shouldn't use) an explicit `return` statement unless you're returning early.

**When the implicit return happens:**
```ruby
def example
  x = 5
  y = 10
  x + y  # This is the last expression, so 15 is returned
end

example  # => 15
```

**What gets returned:**
```ruby
def demo
  if condition
    "yes"  # Returned if condition is true
  else
    "no"   # Returned if condition is false
  end
  # The if/else expression is the last thing, so its result is returned
end
```

**When to use explicit `return`:**

1. **Early returns (guard clauses):**
```ruby
def process_user(user)
  return nil unless user
  return nil if user.inactive?

  user.process
end
```

2. **Returning from nested blocks early:**
```ruby
def find_and_process(items)
  items.each do |item|
    if item.valid?
      return item.process  # Returns from method, not just block
    end
  end
  nil
end
```

3. **For clarity in complex methods:**
```ruby
def complex_calculation(x, y)
  if x > y
    result = x * y
    return result + 100  # Explicit for clarity
  end

  x + y  # Implicit return
end
```

**Common gotcha with puts/print:**
```ruby
def greet(name)
  puts "Hello, #{name}!"  # puts returns nil!
end

greet("Alice")  # Returns nil, not the string

# If you want to return the string:
def greet(name)
  message = "Hello, #{name}!"
  puts message
  message  # Explicitly return the message
end
```

**Assignment returns the assigned value:**
```ruby
def example
  x = 42  # This expression evaluates to 42
end

example  # => 42

def another_example
  @name = "Alice"  # Returns "Alice"
end

another_example  # => "Alice"
```

**Conditional expressions:**
```ruby
def check_age(age)
  if age >= 18
    "adult"
  else
    "minor"
  end
  # The entire if/else is the last expression
  # So it returns either "adult" or "minor"
end
```

**Best Practice:**
- Rely on implicit returns for the normal/happy path
- Use explicit `return` for early exits and guard clauses
- Be aware of what the last expression evaluates to
- Remember that puts, print, and p return nil
- Use explicit return when it improves readability
- Avoid unnecessary `return` at the end of methods

---

## 7.3 Method Arguments

### 7.3.1 Positional Arguments

Positional arguments work similarly in both Ruby and Python - arguments are matched to parameters based on their position in the method call.

**Key Similarities:**
- Arguments are matched by position
- Required arguments must be provided
- Order matters

**Key Differences:**
- Ruby doesn't enforce argument count as strictly in all contexts
- Ruby's error messages differ
- Parentheses are optional in Ruby

#### Python Example

```python
# Basic positional arguments
def greet(first_name, last_name):
    return f"Hello, {first_name} {last_name}!"

greet("Alice", "Smith")
# "Hello, Alice Smith!"

# Order matters
def divide(numerator, denominator):
    return numerator / denominator

divide(10, 2)  # 5.0
divide(2, 10)  # 0.2 - different result!

# Missing arguments cause errors
def add(a, b, c):
    return a + b + c

add(1, 2)  # TypeError: missing 1 required positional argument: 'c'

# Too many arguments also cause errors
add(1, 2, 3, 4)  # TypeError: takes 3 positional arguments but 4 were given

# Multiple parameters
def create_user(name, age, email, city):
    return {
        'name': name,
        'age': age,
        'email': email,
        'city': city
    }

create_user("Alice", 30, "alice@example.com", "NYC")
```

#### Ruby Equivalent

```ruby
# Basic positional arguments
def greet(first_name, last_name)
  "Hello, #{first_name} #{last_name}!"
end

greet("Alice", "Smith")
# "Hello, Alice Smith!"

# Order matters
def divide(numerator, denominator)
  numerator / denominator
end

divide(10, 2)  # 5
divide(2, 10)  # 0 - different result!

# Missing arguments cause errors
def add(a, b, c)
  a + b + c
end

add(1, 2)  # ArgumentError: wrong number of arguments (given 2, expected 3)

# Too many arguments also cause errors
add(1, 2, 3, 4)  # ArgumentError: wrong number of arguments (given 4, expected 3)

# Multiple parameters
def create_user(name, age, email, city)
  {
    name: name,
    age: age,
    email: email,
    city: city
  }
end

create_user("Alice", 30, "alice@example.com", "NYC")

# Parentheses optional (but can be confusing)
greet "Alice", "Smith"  # Works, but less clear
```

#### Explanation

Positional arguments in Ruby work just like Python - they're matched by position, and you must provide the correct number of arguments.

**Method calls with parentheses:**
```ruby
greet("Alice", "Smith")  # Clear and recommended
```

**Method calls without parentheses:**
```ruby
greet "Alice", "Smith"  # Valid but potentially confusing
```

**Best Practice:**
- Use parentheses when calling methods with arguments
- Keep parameter lists short (ideally ≤ 3 parameters)
- Consider using keyword arguments for methods with many parameters
- Name parameters clearly to indicate their purpose

---

### 7.3.2 Default Arguments

Both Ruby and Python support default parameter values, allowing methods to be called with fewer arguments.

**Key Similarities:**
- Both use `=` syntax to specify defaults
- Defaults are evaluated when the method is called
- Optional parameters typically come after required ones

**Key Differences:**
- Python evaluates defaults at function definition time
- Ruby evaluates defaults at method call time
- This leads to different behavior with mutable defaults

#### Python Example

```python
# Basic default argument
def greet(name, greeting="Hello"):
    return f"{greeting}, {name}!"

greet("Alice")           # "Hello, Alice!"
greet("Alice", "Hi")     # "Hi, Alice!"

# Multiple defaults
def create_user(name, age=18, active=True):
    return {
        'name': name,
        'age': age,
        'active': active
    }

create_user("Alice")                    # age=18, active=True
create_user("Bob", 25)                  # age=25, active=True
create_user("Charlie", 30, False)       # age=30, active=False

# Default can be an expression
def log_message(message, timestamp=None):
    from datetime import datetime
    if timestamp is None:
        timestamp = datetime.now()
    return f"[{timestamp}] {message}"

# GOTCHA: Mutable default arguments (dangerous!)
def add_item(item, items=[]):  # DON'T DO THIS!
    items.append(item)
    return items

add_item(1)  # [1]
add_item(2)  # [1, 2] - Unexpected! Same list is reused
add_item(3)  # [1, 2, 3] - Same list keeps growing

# Correct way with mutable defaults
def add_item_correct(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items

add_item_correct(1)  # [1]
add_item_correct(2)  # [2] - New list each time
```

#### Ruby Equivalent

```ruby
# Basic default argument
def greet(name, greeting = "Hello")
  "#{greeting}, #{name}!"
end

greet("Alice")           # "Hello, Alice!"
greet("Alice", "Hi")     # "Hi, Alice!"

# Multiple defaults
def create_user(name, age = 18, active = true)
  {
    name: name,
    age: age,
    active: active
  }
end

create_user("Alice")                    # age=18, active=true
create_user("Bob", 25)                  # age=25, active=true
create_user("Charlie", 30, false)       # age=30, active=false

# Default can be an expression
def log_message(message, timestamp = Time.now)
  "[#{timestamp}] #{message}"
end

# Mutable defaults work differently in Ruby
def add_item(item, items = [])
  items.append(item)
  items
end

add_item(1)  # [1]
add_item(2)  # [2] - New array each time!
add_item(3)  # [3] - Ruby evaluates [] at call time

# However, if you want a persistent default, use a class variable
def add_item_persistent(item, items = nil)
  items ||= []  # Only creates new array if items is nil
  items.append(item)
  items
end

# Or use the ||= operator for clarity
def build_list(item, list = nil)
  list ||= []  # Assigns [] if list is nil
  list << item
end
```

#### Explanation

Default arguments allow you to make parameters optional. If the caller doesn't provide a value, the default is used.

**Syntax:**
```ruby
def method_name(required, optional = default_value)
  # method body
end
```

**Evaluation time difference:**
This is a critical difference between Python and Ruby:

```python
# Python: Default evaluated at DEFINITION time
def python_func(items=[]):  # [] created once when function is defined
    items.append(1)
    return items

python_func()  # [1]
python_func()  # [1, 1] - Same list!
```

```ruby
# Ruby: Default evaluated at CALL time
def ruby_method(items = [])  # [] created fresh each call
  items.append(1)
  items
end

ruby_method()  # [1]
ruby_method()  # [1] - New array each time!
```

**Complex defaults:**
```ruby
# Defaults can be expressions
def greet(name, time_of_day = Time.now.hour < 12 ? "morning" : "evening")
  "Good #{time_of_day}, #{name}!"
end

# Defaults can reference earlier parameters
def create_email(username, domain = "example.com")
  "#{username}@#{domain}"
end

create_email("alice")              # "alice@example.com"
create_email("bob", "gmail.com")   # "bob@gmail.com"
```

**Mixing required and optional:**
```ruby
def build_url(path, protocol = "https", domain = "example.com")
  "#{protocol}://#{domain}/#{path}"
end

build_url("users")                      # "https://example.com/users"
build_url("users", "http")              # "http://example.com/users"
build_url("users", "http", "api.com")   # "http://api.com/users"
```

**Best Practice:**
- Use default arguments to make APIs more convenient
- Put required parameters before optional ones
- Keep defaults simple and predictable
- Use `nil` as default and check inside the method for complex cases
- Document default values in comments for complex methods

---

### 7.3.3 Keyword Arguments

Keyword arguments allow you to pass arguments by name rather than position, making method calls more readable and flexible.

**Key Similarities:**
- Both support named arguments
- Both improve readability for methods with many parameters
- Both allow arguments to be passed in any order

**Key Differences:**
- Python uses `name=value` syntax; Ruby uses `name: value`
- Ruby's keyword arguments (Ruby 2.0+) have different syntax evolution
- Ruby 3.0+ made keyword arguments stricter

#### Python Example

```python
# Defining with keyword arguments
def create_user(name, age, email, active=True, role="user"):
    return {
        'name': name,
        'age': age,
        'email': email,
        'active': active,
        'role': role
    }

# Calling with keyword arguments
create_user(
    name="Alice",
    age=30,
    email="alice@example.com",
    role="admin"
)

# Can mix positional and keyword
create_user("Alice", 30, email="alice@example.com", role="admin")

# Order doesn't matter for keyword arguments
create_user(
    role="admin",
    email="alice@example.com",
    name="Alice",
    age=30
)

# Keyword-only arguments (Python 3)
def create_account(username, *, password, email):
    # password and email MUST be passed as keywords
    return f"Created account for {username}"

create_account("alice", password="secret", email="a@ex.com")  # OK
# create_account("alice", "secret", "a@ex.com")  # Error!

# **kwargs for arbitrary keyword arguments
def build_query(**filters):
    return " AND ".join(f"{k}={v}" for k, v in filters.items())

build_query(age=30, city="NYC", active=True)
# "age=30 AND city=NYC AND active=True"
```

#### Ruby Equivalent

```ruby
# Defining with keyword arguments (Ruby 2.0+)
def create_user(name:, age:, email:, active: true, role: "user")
  {
    name: name,
    age: age,
    email: email,
    active: active,
    role: role
  }
end

# Calling with keyword arguments
create_user(
  name: "Alice",
  age: 30,
  email: "alice@example.com",
  role: "admin"
)

# Order doesn't matter
create_user(
  role: "admin",
  email: "alice@example.com",
  name: "Alice",
  age: 30
)

# Required vs optional keyword arguments
# name:, age:, email: are required (no default)
# active:, role: are optional (have defaults)

# Calling without required keyword argument causes error
# create_user(name: "Alice", age: 30)  # ArgumentError: missing keyword: :email

# **kwargs equivalent (double splat)
def build_query(**filters)
  filters.map { |k, v| "#{k}=#{v}" }.join(" AND ")
end

build_query(age: 30, city: "NYC", active: true)
# "age=30 AND city=NYC AND active=true"

# Pre-Ruby 2.0 style (using options hash - still seen in legacy code)
def create_user_old_style(name, options = {})
  defaults = { age: 18, active: true, role: "user" }
  opts = defaults.merge(options)

  {
    name: name,
    age: opts[:age],
    active: opts[:active],
    role: opts[:role]
  }
end

create_user_old_style("Alice", age: 30, role: "admin")
```

#### Explanation

Ruby's keyword arguments use the `name: value` syntax, where the colon comes after the parameter name in the definition and before the value in the call.

**Defining keyword arguments:**
```ruby
# Required keyword arguments (no default)
def method_name(required_key:, another_required:)
  # Must be provided when calling
end

# Optional keyword arguments (with default)
def method_name(optional_key: "default", another_optional: 42)
  # Can be omitted when calling
end

# Mix of required and optional
def method_name(required:, optional: "default")
  # required must be provided, optional can be omitted
end
```

**Calling methods:**
```ruby
# With keyword arguments
method_name(required: "value", optional: "other")

# Required only
method_name(required: "value")  # optional uses default
```

**Ruby 3.0+ keyword argument changes:**
Ruby 3.0 made keyword arguments stricter to avoid confusion:

```ruby
def greet(name:, greeting: "Hello")
  "#{greeting}, #{name}!"
end

# Ruby 2.x: could pass hash as keyword arguments
options = { name: "Alice", greeting: "Hi" }
greet(options)  # Worked in Ruby 2.x

# Ruby 3.0+: must explicitly double-splat
greet(**options)  # Must use ** in Ruby 3.0+
```

**Advantages of keyword arguments:**
```ruby
# Much more readable than positional
create_user(
  name: "Alice",
  age: 30,
  email: "alice@example.com",
  role: "admin",
  active: true
)

# vs positional (unclear what each argument means)
create_user("Alice", 30, "alice@example.com", "admin", true)
```

**Best Practice:**
- Use keyword arguments for methods with 3+ parameters
- Use keyword arguments when parameter order isn't obvious
- Mark parameters as required (no default) when they must be provided
- Provide sensible defaults for optional parameters
- Use descriptive parameter names
- In Ruby 3.0+, use `**` to pass a hash as keyword arguments

---

### 7.3.4 Splat Operators (`*args`, `**kwargs`)

Both Ruby and Python support variable-length arguments using splat operators. Python uses `*args` and `**kwargs`, while Ruby uses `*args` and `**kwargs` with similar behavior.

**Key Similarities:**
- Both use `*` for positional variable arguments
- Both use `**` for keyword variable arguments
- Both allow methods to accept arbitrary numbers of arguments

**Key Differences:**
- Ruby can use splat in more contexts (array destructuring, assignment)
- Ruby's splat can appear in different positions in parameter list
- Naming conventions differ slightly (`*args` vs `*rest`)

#### Python Example

```python
# *args for variable positional arguments
def sum_all(*numbers):
    return sum(numbers)

sum_all(1, 2, 3)        # 6
sum_all(1, 2, 3, 4, 5)  # 15

# Can combine with regular arguments
def greet_all(greeting, *names):
    for name in names:
        print(f"{greeting}, {name}!")

greet_all("Hello", "Alice", "Bob", "Charlie")
# Hello, Alice!
# Hello, Bob!
# Hello, Charlie!

# **kwargs for variable keyword arguments
def build_user(**attributes):
    return attributes

build_user(name="Alice", age=30, city="NYC")
# {'name': 'Alice', 'age': 30, 'city': 'NYC'}

# Combining regular, *args, and **kwargs
def complex_function(required, *args, optional="default", **kwargs):
    print(f"Required: {required}")
    print(f"Args: {args}")
    print(f"Optional: {optional}")
    print(f"Kwargs: {kwargs}")

complex_function(
    1,                    # required
    2, 3, 4,             # args
    optional="custom",    # optional
    extra1="a",          # kwargs
    extra2="b"
)
# Required: 1
# Args: (2, 3, 4)
# Optional: custom
# Kwargs: {'extra1': 'a', 'extra2': 'b'}

# Unpacking with * and **
numbers = [1, 2, 3]
print(*numbers)  # Same as print(1, 2, 3)

options = {'name': 'Alice', 'age': 30}
create_user(**options)  # Unpacks as keyword arguments
```

#### Ruby Equivalent

```ruby
# *args for variable positional arguments
def sum_all(*numbers)
  numbers.sum
end

sum_all(1, 2, 3)        # 6
sum_all(1, 2, 3, 4, 5)  # 15

# Can combine with regular arguments
def greet_all(greeting, *names)
  names.each do |name|
    puts "#{greeting}, #{name}!"
  end
end

greet_all("Hello", "Alice", "Bob", "Charlie")
# Hello, Alice!
# Hello, Bob!
# Hello, Charlie!

# **kwargs for variable keyword arguments
def build_user(**attributes)
  attributes
end

build_user(name: "Alice", age: 30, city: "NYC")
# {:name=>"Alice", :age=>30, :city=>"NYC"}

# Combining regular, *args, and **kwargs
def complex_method(required, *args, optional: "default", **kwargs)
  puts "Required: #{required}"
  puts "Args: #{args.inspect}"
  puts "Optional: #{optional}"
  puts "Kwargs: #{kwargs.inspect}"
end

complex_method(
  1,                    # required
  2, 3, 4,             # args
  optional: "custom",   # optional
  extra1: "a",         # kwargs
  extra2: "b"
)
# Required: 1
# Args: [2, 3, 4]
# Optional: custom
# Kwargs: {:extra1=>"a", :extra2=>"b"}

# Unpacking with * and **
numbers = [1, 2, 3]
puts(*numbers)  # Same as puts(1, 2, 3)

options = { name: "Alice", age: 30 }
create_user(**options)  # Unpacks as keyword arguments

# Ruby's splat can appear in middle of parameters
def method_name(first, *middle, last)
  puts "First: #{first}"
  puts "Middle: #{middle.inspect}"
  puts "Last: #{last}"
end

method_name(1, 2, 3, 4, 5)
# First: 1
# Middle: [2, 3, 4]
# Last: 5
```

#### Explanation

The splat operators allow methods to accept a variable number of arguments, making them flexible and reusable.

**Single splat (`*`) for positional arguments:**
```ruby
def method_name(*args)
  # args is an array of all arguments
  args.each { |arg| puts arg }
end

method_name(1, 2, 3)  # args = [1, 2, 3]
```

**Double splat (`**`) for keyword arguments:**
```ruby
def method_name(**kwargs)
  # kwargs is a hash of all keyword arguments
  kwargs.each { |key, value| puts "#{key}: #{value}" }
end

method_name(name: "Alice", age: 30)  # kwargs = { name: "Alice", age: 30 }
```

**Combining different parameter types:**
```ruby
# Order matters:
# 1. Required positional
# 2. Optional positional (with defaults)
# 3. Splat (*args)
# 4. Required keyword
# 5. Optional keyword (with defaults)
# 6. Double splat (**kwargs)

def full_example(
  required_pos,           # Required positional
  optional_pos = "def",   # Optional positional
  *rest,                  # Variable positional
  required_kw:,           # Required keyword
  optional_kw: "default", # Optional keyword
  **other_kw              # Variable keyword
)
  puts "Required positional: #{required_pos}"
  puts "Optional positional: #{optional_pos}"
  puts "Rest: #{rest.inspect}"
  puts "Required keyword: #{required_kw}"
  puts "Optional keyword: #{optional_kw}"
  puts "Other keywords: #{other_kw.inspect}"
end

full_example(
  1,                      # required_pos
  2,                      # optional_pos
  3, 4, 5,               # rest
  required_kw: "must",    # required_kw
  optional_kw: "custom",  # optional_kw
  extra1: "a",           # other_kw
  extra2: "b"            # other_kw
)
```

**Destructuring with splat:**
```ruby
# In assignment
first, *rest, last = [1, 2, 3, 4, 5]
# first = 1, rest = [2, 3, 4], last = 5

# In method parameters (same as above)
def method(first, *middle, last)
  # ...
end

# Forwarding arguments to another method
def wrapper(*args, **kwargs)
  original_method(*args, **kwargs)  # Pass everything through
end
```

**Unpacking arrays and hashes:**
```ruby
# Array unpacking
numbers = [1, 2, 3]
sum_all(*numbers)  # Same as sum_all(1, 2, 3)

# Hash unpacking
options = { name: "Alice", age: 30 }
create_user(**options)  # Same as create_user(name: "Alice", age: 30)

# Merging arrays
arr1 = [1, 2, 3]
arr2 = [4, 5, 6]
combined = [*arr1, *arr2]  # [1, 2, 3, 4, 5, 6]

# Merging hashes
hash1 = { a: 1, b: 2 }
hash2 = { c: 3, d: 4 }
combined = { **hash1, **hash2 }  # { a: 1, b: 2, c: 3, d: 4 }
```

**Best Practice:**
- Use `*args` when you need variable positional arguments
- Use `**kwargs` when you need variable keyword arguments
- Name them descriptively (`*numbers`, `*items`) not just `*args`
- Use splat to forward arguments to other methods
- Remember that `*args` is an Array and `**kwargs` is a Hash
- Document what arguments are expected even with splat operators

---

## 7.4 Method Naming Conventions (`?`, `!`, `=`)

Ruby has unique naming conventions that use special characters (`?`, `!`, `=`) as method suffixes to convey meaning. This is one of Ruby's most distinctive features and has no direct equivalent in Python.

**Key Similarities:**
- Both languages have naming conventions for methods
- Both encourage descriptive method names

**Key Differences:**
- Ruby allows `?`, `!`, and `=` in method names
- These suffixes have conventional meanings in Ruby
- Python uses decorators and naming patterns instead

### Python Approach

```python
# Python uses descriptive names without special characters
class User:
    def __init__(self, name, active=True):
        self.name = name
        self._active = active

    # Boolean checks - use is_, has_, can_ prefixes
    def is_active(self):
        return self._active

    def has_permission(self, permission):
        return permission in self.permissions

    # Mutating vs non-mutating methods
    # Python doesn't have a convention - must read docs
    def sorted_items(self):
        # Returns new sorted list
        return sorted(self.items)

    def sort_items(self):
        # Mutates in place
        self.items.sort()

    # Setters - use @property decorator
    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        self._name = value

user = User("Alice")
if user.is_active():  # Boolean check - parentheses required
    print(user.name)

# List methods in Python
items = [3, 1, 2]
items.sort()         # Mutates in place (returns None)
sorted_items = sorted(items)  # Returns new list
```

### Ruby Equivalent

```ruby
class User
  def initialize(name, active = true)
    @name = name
    @active = active
  end

  # Predicate methods - end with ?
  # Conventionally return true/false
  def active?
    @active
  end

  def has_permission?(permission)
    @permissions.include?(permission)
  end

  def admin?
    @role == "admin"
  end

  # Bang methods - end with !
  # Indicate danger, mutation, or "surprising" behavior
  def activate!
    @active = true
    save_to_database  # Has side effect
  end

  def destroy!
    # Dangerous - permanently deletes
    delete_from_database
  end

  # Setter methods - end with =
  # Allow assignment-like syntax
  def name=(value)
    @name = value
  end

  def age=(value)
    raise ArgumentError, "Age must be positive" if value < 0
    @age = value
  end
end

user = User.new("Alice")

# Predicate methods - readable as questions
if user.active?  # No parentheses needed, reads naturally
  puts user.name
end

# Bang methods indicate mutation/danger
user.activate!  # The ! warns: "this has side effects!"

# Setter methods allow natural assignment
user.name = "Bob"  # Calls name=(value)
user.age = 30      # Calls age=(value)

# Array methods in Ruby
items = [3, 1, 2]
items.sort!        # Mutates in place - ! indicates this
sorted = items.sort  # Returns new array - no !
```

### Explanation

Ruby's method naming conventions use suffixes to communicate intent:

**1. Question mark (`?`) - Predicate methods**

Methods ending with `?` conventionally return boolean values:

```ruby
# Boolean checks
user.active?
user.admin?
array.empty?
number.even?
string.include?("hello")
value.nil?

# Reads naturally in conditions
if user.admin?
  grant_access
end

unless items.empty?
  process(items)
end
```

**Convention:** Any method returning true/false should end with `?`

**2. Bang/exclamation mark (`!`) - Dangerous methods**

Methods ending with `!` indicate:
- Mutation (modifying the object)
- Raising exceptions instead of returning nil
- Surprising or dangerous behavior

```ruby
# Mutation - changes the object
string = "hello"
string.upcase!   # Modifies string in place
# string is now "HELLO"

array = [3, 1, 2]
array.sort!      # Sorts in place
array.reverse!   # Reverses in place

# Exception raising
user = User.find_by(email: "x@y.com")  # Returns nil if not found
user = User.find_by!(email: "x@y.com") # Raises exception if not found

# Dangerous operations
record.destroy!  # Permanently deletes
cache.clear!     # Clears all data
```

**Convention:** Use `!` when there's a non-bang version that's safer:
```ruby
# Safe version
array.sort   # Returns new sorted array

# Dangerous version
array.sort!  # Modifies array in place
```

**3. Equals (`=`) - Setter methods**

Methods ending with `=` allow assignment syntax:

```ruby
class Person
  def name=(value)
    @name = value.strip.capitalize
  end

  def name
    @name
  end
end

person = Person.new
person.name = "alice"  # Calls name=("alice")
# @name is now "Alice" (stripped and capitalized)

# Without setter, you'd need:
person.set_name("alice")  # Less natural
```

**More setter examples:**
```ruby
class Temperature
  def celsius=(value)
    @celsius = value
    @fahrenheit = value * 9.0 / 5.0 + 32
  end

  def celsius
    @celsius
  end

  def fahrenheit
    @fahrenheit
  end
end

temp = Temperature.new
temp.celsius = 100
temp.fahrenheit  # 212.0
```

**Combining with attr_accessor:**
```ruby
class User
  # Creates both getter and setter
  attr_accessor :name, :email

  # Equivalent to:
  # def name
  #   @name
  # end
  #
  # def name=(value)
  #   @name = value
  # end
end

user = User.new
user.name = "Alice"
puts user.name
```

**Common patterns:**

```ruby
# Predicate methods
def valid?
  errors.empty?
end

def ready?
  @status == :ready
end

# Bang methods with non-bang equivalents
def process
  # Returns new processed version
  ProcessedData.new(@data)
end

def process!
  # Modifies self
  @data = ProcessedData.new(@data)
  save
end

# Setter with validation
def age=(value)
  raise ArgumentError, "Invalid age" unless value.is_a?(Integer) && value > 0
  @age = value
end
```

**Best Practice:**
- Use `?` for all methods that return boolean values
- Use `!` only when there's a safer non-bang alternative
- Use `!` to indicate mutation or exceptions
- Use `=` for setters to enable natural assignment syntax
- Don't use `?` for methods that return non-boolean values
- Don't overuse `!` - not every mutating method needs it
- Keep predicate methods simple and fast

---

## 7.5 Parentheses are Optional

One of Ruby's most visible syntactic features is that parentheses are optional for method calls. This makes Ruby code more readable but can also cause confusion if misused.

**Key Similarities:**
- Both languages support method calls
- Both require parentheses in certain contexts

**Key Differences:**
- Ruby makes parentheses optional in most cases
- Python requires parentheses for all function/method calls
- Ruby's approach can make code more readable or more confusing

### Python Example

```python
# Parentheses always required (with rare exceptions)
print("Hello")          # Required
len([1, 2, 3])         # Required
"hello".upper()        # Required

# Even with no arguments
import random
random.random()        # Parentheses required

# Without parentheses, you get the function object
print                  # <built-in function print>
"hello".upper         # <built-in method upper...>

# This is an error:
# print "Hello"  # SyntaxError in Python 3

# Method chaining requires parentheses
result = "hello".upper().replace("H", "J")

# Decorators (one case where Python omits parentheses)
@property
def name(self):
    return self._name
```

### Ruby Equivalent

```ruby
# Parentheses optional
puts("Hello")          # With parentheses
puts "Hello"           # Without parentheses - both work

[1, 2, 3].length       # No parentheses needed
[1, 2, 3].length()     # Parentheses optional

"hello".upcase         # No parentheses
"hello".upcase()       # With parentheses - both work

# Methods with arguments
def greet(name)
  "Hello, #{name}"
end

greet("Alice")         # With parentheses (recommended)
greet "Alice"          # Without parentheses (works)

# Multiple arguments
def add(a, b)
  a + b
end

add(5, 3)             # With parentheses (clear)
add 5, 3              # Without parentheses (less clear)

# Method chaining - parentheses often omitted
"hello".upcase.reverse
# vs
"hello".upcase().reverse()

# When parentheses are required or strongly recommended:

# 1. Chaining with arguments
"hello world".gsub("world", "Ruby").upcase
# vs ambiguous:
# "hello world".gsub "world", "Ruby".upcase  # Confusing!

# 2. Nested method calls
puts(greet("Alice"))   # Clear
# puts greet "Alice"   # Works but confusing

# 3. Empty argument list in certain contexts
def do_something
  # ...
end

do_something()        # Recommended for zero-arg calls
do_something          # Also works

# Method definitions
def method_name(arg1, arg2)  # Parentheses recommended
  # ...
end

def method_name arg1, arg2   # Works but uncommon
  # ...
end
```

### Explanation

Ruby makes parentheses optional for method calls to make code more natural and readable, especially for methods that read like statements or commands.

**When to use parentheses:**

1. **Method calls with arguments (recommended):**
```ruby
# Good
puts("Hello")
greet("Alice")
calculate(5, 10)

# Acceptable but less clear
puts "Hello"
greet "Alice"
```

2. **Method chaining:**
```ruby
# Good - clear precedence
result = items.select { |x| x.even? }.map { |x| x * 2 }.sum

# Good with arguments
text.gsub("old", "new").upcase.strip

# Bad - confusing
# text.gsub "old", "new".upcase  # upcase called on "new"!
```

3. **Nested calls:**
```ruby
# Good
puts(calculate(5, 10))

# Confusing
# puts calculate 5, 10
```

**When to omit parentheses:**

1. **No arguments:**
```ruby
# Common
items.length
array.first
user.active?
```

2. **DSL-style methods (Rails, RSpec, etc.):**
```ruby
# RSpec
describe "User" do
  it "is valid" do
    expect(user.valid?).to be true
  end
end

# Rails
validates :email, presence: true
has_many :posts
before_action :authenticate
```

3. **Statement-like methods:**
```ruby
# Reads naturally without parentheses
puts "Hello"
require "json"
attr_reader :name
```

**Common pitfalls:**

```ruby
# Ambiguous parsing
def method1(arg)
  puts arg
end

def method2
  "value"
end

# This is confusing:
method1 method2.upcase
# Is it: method1(method2().upcase) ?

# Better with parentheses:
method1(method2.upcase)

# Unexpected precedence
puts 5 + 3 * 2    # puts(5 + 3 * 2) => 11
# vs
(puts 5) + 3 * 2  # Different!

# Method calls vs local variables
def name
  "Alice"
end

name       # Calls method
name()     # Also calls method (explicit)

# But if there's a variable:
name = "Bob"
name       # Variable takes precedence
name()     # Still calls method!
```

**Method definitions:**

```ruby
# Recommended - use parentheses
def greet(name)
  "Hello, #{name}"
end

# Works but uncommon
def greet name
  "Hello, #{name}"
end

# No parameters - parentheses optional
def greet()
  "Hello"
end

def greet
  "Hello"
end
```

**Best Practice:**

**DO use parentheses:**
- Method calls with arguments (for clarity)
- Method chaining with arguments
- Nested method calls
- When order of operations could be ambiguous
- In method definitions with parameters

**DON'T use parentheses:**
- Method calls with no arguments (usually)
- DSL-style code (Rails, RSpec, etc.)
- Statement-like methods (puts, require, attr_reader)

**General rule:** When in doubt, use parentheses. Clarity is more important than brevity.

**Style guide recommendation:**
```ruby
# Method definitions - always use parentheses if there are parameters
def method_name(arg1, arg2)
  # ...
end

# Method calls - use parentheses for clarity
object.method(arg1, arg2)

# Zero-argument methods - omit parentheses
object.method_name
array.length
user.active?

# Statement-like methods - omit parentheses
puts "Hello"
require "library"
```

---

This concludes Chapter 7 on Methods. You've learned how to define methods in Ruby, work with different types of arguments, use Ruby's unique naming conventions, and understand when to use parentheses. These fundamentals will serve as the foundation for writing clean, idiomatic Ruby code.

In the next chapter, we'll explore one of Ruby's most powerful and distinctive features: blocks, procs, and lambdas.
