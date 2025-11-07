# Chapter 8: Blocks, Procs, and Lambdas

## 8.1 Understanding Blocks

Blocks are one of Ruby's most powerful and distinctive features. If you've used Python's list comprehensions, lambda functions, or context managers (`with` statements), you've encountered similar concepts. However, Ruby's blocks are more fundamental to the language and are used extensively throughout Ruby code.

**What is a block?**
A block is a chunk of code that you can pass to a method. Think of it as an anonymous function that can be passed as an implicit argument to a method call.

**Key Similarities:**
- Similar to Python's lambda functions (but more powerful)
- Similar to callback functions in JavaScript
- Used for iteration like Python's `for` loops with functions

**Key Differences:**
- Blocks are not objects (unlike Python lambdas)
- Blocks are passed implicitly to methods (no special parameter needed)
- Almost every Ruby method can accept a block
- Ruby uses blocks far more extensively than Python uses lambdas

### Python Context

```python
# Python's closest equivalents to Ruby blocks:

# 1. List comprehensions (limited to building lists)
numbers = [1, 2, 3, 4, 5]
doubled = [n * 2 for n in numbers]

# 2. Lambda functions (single expression only)
doubled = list(map(lambda n: n * 2, numbers))

# 3. Regular functions passed as arguments
def double(n):
    return n * 2

doubled = list(map(double, numbers))

# 4. Context managers (with statement)
with open('file.txt', 'r') as f:
    content = f.read()
    # Code block executes within context

# 5. Callback functions
def process_items(items, callback):
    for item in items:
        callback(item)

process_items([1, 2, 3], lambda x: print(x * 2))

# None of these are exactly like Ruby blocks
```

### Ruby Blocks

```ruby
# Blocks are passed to methods using { } or do...end

# 1. Basic block with each
numbers = [1, 2, 3, 4, 5]
numbers.each { |n| puts n * 2 }
# 2
# 4
# 6
# 8
# 10

# 2. Block with map (like Python's map + lambda)
doubled = numbers.map { |n| n * 2 }
# [2, 4, 6, 8, 10]

# 3. Multi-line block with do...end
numbers.each do |n|
  squared = n * 2
  puts "#{n} doubled is #{squared}"
end

# 4. Blocks for resource management (like Python's with)
File.open('file.txt', 'r') do |f|
  content = f.read
  puts content
end
# File is automatically closed after the block

# 5. Custom methods that accept blocks
def repeat(times)
  times.times do
    yield  # Execute the block
  end
end

repeat(3) { puts "Hello" }
# Hello
# Hello
# Hello

# 6. Blocks with select (filtering)
evens = numbers.select { |n| n.even? }
# [2, 4]

# 7. Blocks with reduce (aggregation)
sum = numbers.reduce(0) { |total, n| total + n }
# 15
```

### Explanation

A block is code enclosed in either curly braces `{ }` or `do...end` keywords. Blocks are passed to methods and can be executed by the method using the `yield` keyword.

**Block syntax:**
```ruby
# Curly braces (preferred for single-line blocks)
array.each { |item| puts item }

# do...end (preferred for multi-line blocks)
array.each do |item|
  puts item
  puts item * 2
end
```

**Block parameters:**
The code between `|` pipes are block parameters (like function parameters):
```ruby
[1, 2, 3].each { |number| puts number }
#                  ^^^^^^ block parameter

hash.each { |key, value| puts "#{key}: #{value}" }
#            ^^^^^^^^^^^ multiple block parameters
```

**Why blocks matter in Ruby:**

1. **Iteration is built on blocks:**
```ruby
# Python
for i in range(5):
    print(i)

# Ruby
5.times { |i| puts i }
```

2. **Resource management:**
```ruby
# Automatic cleanup
File.open('file.txt') do |f|
  # Use file
end
# File automatically closed
```

3. **Custom control structures:**
```ruby
def benchmark
  start_time = Time.now
  yield  # Run the block
  end_time = Time.now
  puts "Took #{end_time - start_time} seconds"
end

benchmark do
  # Code to measure
  sleep(1)
end
# Took 1.0 seconds
```

4. **Functional programming:**
```ruby
# Chain operations with blocks
result = [1, 2, 3, 4, 5]
  .select { |n| n.even? }
  .map { |n| n * 2 }
  .reduce(:+)
# 12
```

**Blocks are not objects:**
Unlike Python lambdas, blocks themselves are not objects and can't be stored in variables directly:
```ruby
# This doesn't work:
# block = { |n| n * 2 }

# To store code, use Proc or lambda (covered in sections 8.4 and 8.5):
proc = Proc.new { |n| n * 2 }
lambda_func = lambda { |n| n * 2 }
```

**Best Practice:**
- Use `{ }` for simple, single-line blocks
- Use `do...end` for multi-line blocks
- Block parameters go between `|` pipes
- Blocks make Ruby code elegant and expressive
- Almost every iteration in Ruby uses blocks

---

## 8.2 Blocks with `do...end` and `{...}`

Ruby provides two syntaxes for blocks: curly braces `{ }` and `do...end` keywords. While they're functionally equivalent in most cases, there are conventions and subtle differences in precedence.

**Key Similarities:**
- Both define blocks
- Both can have parameters
- Both can be single or multi-line

**Key Differences:**
- `{ }` has higher precedence than `do...end`
- Different style conventions for single vs multi-line
- Precedence differences can affect method chaining

### Python Comparison

```python
# Python doesn't have direct equivalent
# But conceptually similar to choosing between:

# 1. Lambda (single expression)
squared = list(map(lambda x: x ** 2, numbers))

# 2. Function definition (multiple statements)
def square_and_print(x):
    result = x ** 2
    print(f"{x} squared is {result}")
    return result

squared = list(map(square_and_print, numbers))

# 3. List comprehension (single expression)
squared = [x ** 2 for x in numbers]

# 4. For loop (multiple statements)
squared = []
for x in numbers:
    result = x ** 2
    print(f"{x} squared is {result}")
    squared.append(result)
```

### Ruby Equivalent

```ruby
numbers = [1, 2, 3, 4, 5]

# Curly braces { } - preferred for single-line blocks
squared = numbers.map { |x| x ** 2 }
# [1, 4, 9, 16, 25]

numbers.each { |x| puts x }
# 1
# 2
# 3
# 4
# 5

# do...end - preferred for multi-line blocks
squared = numbers.map do |x|
  result = x ** 2
  puts "#{x} squared is #{result}"
  result
end
# 1 squared is 1
# 2 squared is 4
# ...

numbers.each do |x|
  puts x
  puts x * 2
end
# 1
# 2
# 2
# 4
# ...

# Both syntaxes work for single-line
numbers.each { |x| puts x }      # Common
numbers.each do |x| puts x end   # Less common but valid

# Both syntaxes work for multi-line
numbers.each {
  |x|
  puts x
  puts x * 2
}  # Valid but unconventional

# Block parameters with both syntaxes
{ |x| x * 2 }           # Curly braces
do |x| x * 2 end        # do...end

# Multiple parameters
hash = { a: 1, b: 2, c: 3 }
hash.each { |key, value| puts "#{key}: #{value}" }
hash.each do |key, value|
  puts "#{key}: #{value}"
end
```

### Explanation

Ruby provides two ways to write blocks, and while they're mostly interchangeable, there are important conventions and precedence differences.

**Convention (Ruby style guide):**

1. **Use `{ }` for single-line blocks:**
```ruby
[1, 2, 3].map { |n| n * 2 }
[1, 2, 3].select { |n| n.even? }
5.times { |i| puts i }
```

2. **Use `do...end` for multi-line blocks:**
```ruby
[1, 2, 3].each do |n|
  squared = n ** 2
  puts "#{n} squared is #{squared}"
end
```

3. **Use `{ }` when you care about the return value:**
```ruby
# Chaining - return value matters
result = [1, 2, 3]
  .map { |n| n * 2 }
  .select { |n| n > 3 }
# [4, 6]
```

4. **Use `do...end` for side effects:**
```ruby
# Iteration for side effects - return value doesn't matter
items.each do |item|
  puts item
  log(item)
  process(item)
end
```

**Precedence difference:**

This is subtle but important. `{ }` binds tighter than `do...end`:

```ruby
# Example method
def calculate
  yield + yield
end

# Curly braces bind to the closest method
puts calculate { 1 + 1 }
# Parses as: puts(calculate { 1 + 1 })
# Output: 4 (because 1+1=2, and 2+2=4)

# do...end binds loosely
puts calculate do 1 + 1 end
# Parses as: puts(calculate) do 1 + 1 end
# This will cause an error - puts doesn't take a block!

# With parentheses to force binding:
puts(calculate do 1 + 1 end)
# Output: 4
```

**More precedence examples:**
```ruby
# Array.new with block
array = Array.new(5) { |i| i * 2 }
# [0, 2, 4, 6, 8]

# Without parentheses, curly braces work
array = Array.new 5 { |i| i * 2 }
# [0, 2, 4, 6, 8]

# But do...end binds differently
array = Array.new 5 do |i| i * 2 end
# Error: wrong number of arguments

# Need parentheses with do...end
array = Array.new(5) do |i| i * 2 end
# [0, 2, 4, 6, 8]
```

**Chaining with blocks:**
```ruby
# Curly braces - natural chaining
result = [1, 2, 3, 4, 5]
  .select { |n| n.even? }
  .map { |n| n * 2 }
  .reduce(:+)
# 12

# do...end - less common for chaining
result = [1, 2, 3, 4, 5]
  .select do |n|
    n.even?
  end
  .map do |n|
    n * 2
  end
  .reduce(:+)
# Works but unconventional
```

**Block parameters:**
```ruby
# No parameters
5.times { puts "Hello" }
5.times do
  puts "Hello"
end

# One parameter
[1, 2, 3].each { |n| puts n }

# Multiple parameters
{ a: 1, b: 2 }.each { |key, value| puts "#{key}: #{value}" }

# Default parameter values (Ruby 1.9+)
[[1], [2], [3]].each { |a, b = 0| puts "a=#{a}, b=#{b}" }
# a=1, b=0
# a=2, b=0
# a=3, b=0

# Splat in block parameters
[[1, 2, 3], [4, 5, 6]].each { |first, *rest| puts "first: #{first}, rest: #{rest}" }
# first: 1, rest: [2, 3]
# first: 4, rest: [5, 6]
```

**Best Practice:**
- **Single-line blocks:** Use `{ }`
- **Multi-line blocks:** Use `do...end`
- **When return value matters (map, select, etc.):** Use `{ }`
- **For side effects (each, times, etc.):** Use `do...end`
- **When in doubt:** Use `{ }` for functional code, `do...end` for procedural code
- **Always use parentheses** with method calls when mixing `do...end` blocks

---

## 8.3 `yield` and Block Parameters

Methods in Ruby can execute blocks passed to them using the `yield` keyword. This is how Ruby implements callbacks, iterators, and custom control structures.

**Key Similarities:**
- Similar to Python's callback functions
- Similar to executing a function passed as parameter
- Allows customizable behavior in methods

**Key Differences:**
- Ruby's `yield` is a keyword, not a function call
- Blocks are passed implicitly (no parameter declaration needed)
- Python requires explicit function parameters; Ruby's blocks are implicit

### Python Example

```python
# Python uses explicit function parameters for callbacks

def repeat(times, callback):
    for i in range(times):
        callback(i)

# Pass a function
def print_number(n):
    print(f"Number: {n}")

repeat(3, print_number)
# Number: 0
# Number: 1
# Number: 2

# Pass a lambda
repeat(3, lambda n: print(f"Value: {n}"))

# More complex example with multiple callbacks
def process_file(filename, on_line, on_error=None):
    try:
        with open(filename) as f:
            for line in f:
                on_line(line.strip())
    except FileNotFoundError:
        if on_error:
            on_error(f"File {filename} not found")

process_file(
    'data.txt',
    on_line=lambda line: print(line.upper()),
    on_error=lambda msg: print(f"ERROR: {msg}")
)

# Context manager (similar concept)
class Timer:
    def __enter__(self):
        import time
        self.start = time.time()
        return self

    def __exit__(self, *args):
        import time
        print(f"Took {time.time() - self.start} seconds")

with Timer():
    # Code block
    sum(range(1000000))
```

### Ruby Equivalent

```ruby
# Ruby uses yield to execute blocks

def repeat(times)
  times.times do |i|
    yield(i)  # Execute the block, passing i
  end
end

# Call with a block
repeat(3) { |n| puts "Number: #{n}" }
# Number: 0
# Number: 1
# Number: 2

# Multi-line block
repeat(3) do |n|
  puts "Value: #{n}"
end

# More complex example with yield
def process_file(filename)
  File.foreach(filename) do |line|
    yield(line.strip)  # Execute block for each line
  end
rescue Errno::ENOENT => e
  yield_error(e.message) if block_given?
end

process_file('data.txt') do |line|
  puts line.upcase
end

# Context manager equivalent
def timer
  start_time = Time.now
  yield  # Execute the block
  end_time = Time.now
  puts "Took #{end_time - start_time} seconds"
end

timer do
  # Code block
  (1..1000000).reduce(:+)
end

# Yielding multiple values
def each_pair(array)
  (0...array.length).step(2) do |i|
    yield(array[i], array[i + 1]) if i + 1 < array.length
  end
end

each_pair([1, 2, 3, 4, 5, 6]) do |a, b|
  puts "#{a} and #{b}"
end
# 1 and 2
# 3 and 4
# 5 and 6
```

### Explanation

The `yield` keyword executes the block that was passed to the method. It's one of Ruby's most powerful features for creating flexible, reusable code.

**Basic yield:**
```ruby
def greet
  puts "Before block"
  yield
  puts "After block"
end

greet { puts "Inside block" }
# Before block
# Inside block
# After block
```

**Yielding with arguments:**
```ruby
def greet_name
  yield("Alice")  # Pass "Alice" to the block
end

greet_name { |name| puts "Hello, #{name}!" }
# Hello, Alice!

# Multiple arguments
def provide_coords
  yield(10, 20)
end

provide_coords { |x, y| puts "x=#{x}, y=#{y}" }
# x=10, y=20
```

**Checking if a block was given:**
```ruby
def optional_block
  if block_given?
    yield
  else
    puts "No block provided"
  end
end

optional_block { puts "Block!" }
# Block!

optional_block
# No block provided
```

**Yielding multiple times:**
```ruby
def twice
  yield
  yield
end

twice { puts "Hello" }
# Hello
# Hello

# With counter
def n_times(n)
  n.times { |i| yield(i) }
end

n_times(3) { |i| puts "Iteration #{i}" }
# Iteration 0
# Iteration 1
# Iteration 2
```

**Using yield to implement iterators:**
```ruby
class CustomArray
  def initialize(array)
    @array = array
  end

  def each
    i = 0
    while i < @array.length
      yield(@array[i])
      i += 1
    end
  end
end

custom = CustomArray.new([1, 2, 3])
custom.each { |item| puts item }
# 1
# 2
# 3
```

**Capturing block return values:**
```ruby
def transform
  result = yield(5)
  puts "Block returned: #{result}"
end

transform { |n| n * 2 }
# Block returned: 10

# Use in calculations
def calculate(a, b)
  result = yield(a, b)
  result * 2
end

value = calculate(5, 3) { |x, y| x + y }
# value = 16 (because (5 + 3) * 2 = 16)
```

**Resource management pattern:**
```ruby
def with_resource(name)
  resource = acquire_resource(name)
  begin
    yield(resource)
  ensure
    release_resource(resource)  # Always cleanup
  end
end

with_resource("database") do |db|
  db.query("SELECT * FROM users")
end
# Database automatically released after block
```

**Real-world example - benchmarking:**
```ruby
def benchmark(label)
  start_time = Time.now
  result = yield  # Execute and capture result
  end_time = Time.now
  puts "#{label}: #{end_time - start_time} seconds"
  result  # Return the block's result
end

total = benchmark("Sum calculation") do
  (1..1000000).reduce(:+)
end

puts "Total: #{total}"
# Sum calculation: 0.123 seconds
# Total: 500000500000
```

**Implementing custom control structures:**
```ruby
def unless_zero(value)
  yield unless value.zero?
end

unless_zero(5) { puts "Not zero!" }
# Not zero!

unless_zero(0) { puts "Not zero!" }
# (nothing printed)

# Retry pattern
def with_retry(max_attempts = 3)
  attempts = 0
  begin
    attempts += 1
    yield(attempts)
  rescue StandardError => e
    if attempts < max_attempts
      puts "Attempt #{attempts} failed, retrying..."
      retry
    else
      puts "All attempts failed"
      raise
    end
  end
end

with_retry do |attempt|
  puts "Attempt #{attempt}"
  # Do something that might fail
end
```

**Best Practice:**
- Use `yield` to execute blocks passed to your methods
- Use `block_given?` to check if a block was provided
- Pass arguments to blocks with `yield(arg1, arg2)`
- Use blocks for callbacks, iterators, and resource management
- Capture block return values when needed
- Use `ensure` to guarantee cleanup in resource management
- Name your methods to indicate they expect blocks (e.g., `each`, `with_*`, `map`)

---

## 8.4 Procs vs Python Functions

While blocks are great, they can't be stored in variables or passed as explicit arguments. Ruby's `Proc` objects solve this by turning blocks into first-class objects that can be stored, passed around, and called multiple times.

**Key Similarities:**
- Similar to Python's function objects
- Can be stored in variables
- Can be passed as arguments
- Can be called multiple times

**Key Differences:**
- Ruby Procs are created explicitly with `Proc.new`
- Python functions are defined with `def` or `lambda`
- Procs have different `return` semantics than Python functions
- Ruby has both Procs and lambdas (covered in 8.5)

### Python Example

```python
# Python functions are first-class objects

# Define a function
def double(x):
    return x * 2

# Store in variable
my_func = double

# Call it
result = my_func(5)  # 10

# Pass as argument
def apply_twice(func, value):
    return func(func(value))

result = apply_twice(double, 5)  # 20

# Lambda functions (single expression)
triple = lambda x: x * 3
result = triple(5)  # 15

# Store lambdas in data structures
operations = {
    'double': lambda x: x * 2,
    'triple': lambda x: x * 3,
    'square': lambda x: x ** 2
}

operations['double'](5)  # 10

# Closures
def make_multiplier(n):
    def multiplier(x):
        return x * n
    return multiplier

times_3 = make_multiplier(3)
times_5 = make_multiplier(5)

times_3(10)  # 30
times_5(10)  # 50

# Functions can be passed around
def apply_operation(operation, x, y):
    return operation(x, y)

apply_operation(lambda a, b: a + b, 5, 3)  # 8
```

### Ruby Equivalent

```ruby
# Procs are objects that encapsulate blocks

# Create a Proc
double = Proc.new { |x| x * 2 }

# Call it with .call
result = double.call(5)  # 10

# Alternative calling syntax
result = double.(5)      # 10
result = double[5]       # 10

# Pass as argument
def apply_twice(func, value)
  func.call(func.call(value))
end

result = apply_twice(double, 5)  # 20

# Create Procs inline
triple = Proc.new { |x| x * 3 }
result = triple.call(5)  # 15

# Store Procs in data structures
operations = {
  double: Proc.new { |x| x * 2 },
  triple: Proc.new { |x| x * 3 },
  square: Proc.new { |x| x ** 2 }
}

operations[:double].call(5)  # 10

# Closures work the same way
def make_multiplier(n)
  Proc.new { |x| x * n }
end

times_3 = make_multiplier(3)
times_5 = make_multiplier(5)

times_3.call(10)  # 30
times_5.call(10)  # 50

# Procs can be passed around
def apply_operation(operation, x, y)
  operation.call(x, y)
end

apply_operation(Proc.new { |a, b| a + b }, 5, 3)  # 8

# Alternative Proc creation with proc method
my_proc = proc { |x| x * 2 }  # Shorthand for Proc.new
my_proc.call(5)  # 10
```

### Explanation

A `Proc` (short for "procedure") is an object that wraps a block of code. Unlike blocks, Procs are objects and can be stored, passed as arguments, and called multiple times.

**Creating Procs:**
```ruby
# Using Proc.new
greet = Proc.new { |name| "Hello, #{name}!" }

# Using proc method (lowercase - creates a Proc)
greet = proc { |name| "Hello, #{name}!" }

# Using & to convert a block to Proc (covered in 8.7)
def make_proc(&block)
  block  # block is now a Proc object
end

my_proc = make_proc { |x| x * 2 }
```

**Calling Procs:**
```ruby
my_proc = Proc.new { |x| x * 2 }

# Standard way
my_proc.call(5)     # 10

# Shorthand with parentheses
my_proc.(5)         # 10

# Bracket syntax
my_proc[5]          # 10

# Rarely used - method call syntax
my_proc === 5       # 10 (works but confusing)
```

**Procs with multiple parameters:**
```ruby
add = Proc.new { |a, b| a + b }
add.call(5, 3)  # 8

# Procs are lenient with arguments (unlike lambdas)
subtract = Proc.new { |a, b| a - b }
subtract.call(10)      # Error-free, b is nil (10 - nil raises error)
subtract.call(10, 5)   # 5
subtract.call(10, 5, 3)  # Ignores extra argument, returns 5
```

**Procs as method arguments:**
```ruby
def each_with_proc(array, proc)
  array.each { |item| proc.call(item) }
end

printer = Proc.new { |x| puts x }
each_with_proc([1, 2, 3], printer)
# 1
# 2
# 3
```

**Procs in arrays:**
```ruby
operations = [
  Proc.new { |x| x * 2 },
  Proc.new { |x| x + 10 },
  Proc.new { |x| x ** 2 }
]

value = 5
operations.each do |op|
  puts op.call(value)
end
# 10
# 15
# 25
```

**Closures with Procs:**
```ruby
def counter
  count = 0
  Proc.new {
    count += 1
    count
  }
end

counter1 = counter
counter1.call  # 1
counter1.call  # 2
counter1.call  # 3

counter2 = counter
counter2.call  # 1 (separate counter)
```

**Return behavior (important difference):**
```ruby
def test_proc
  my_proc = Proc.new { return "from proc" }
  my_proc.call
  "from method"  # This line never executes!
end

test_proc  # "from proc"
# The return inside Proc returns from the enclosing method!

# This can cause unexpected behavior
def risky
  [1, 2, 3].each do |i|
    my_proc = Proc.new { return i }
    return my_proc.call if i == 2
  end
  "never reached"
end

risky  # 2
```

**Converting between Procs and blocks:**
```ruby
# Proc to block with &
my_proc = Proc.new { |x| x * 2 }
[1, 2, 3].map(&my_proc)
# [2, 4, 6]

# Block to Proc with &parameter
def capture_block(&block)
  block  # This is now a Proc object
end

my_proc = capture_block { |x| x * 2 }
my_proc.call(5)  # 10
```

**Practical example - callbacks:**
```ruby
class Button
  def initialize
    @on_click = nil
  end

  def on_click(&block)
    @on_click = block
  end

  def click
    @on_click.call if @on_click
  end
end

button = Button.new
button.on_click { puts "Button clicked!" }
button.click
# Button clicked!
```

**Best Practice:**
- Use Procs when you need to store blocks as objects
- Use `.call` for invoking Procs (clearest syntax)
- Be aware of Proc's lenient argument handling
- Be careful with `return` in Procs (returns from enclosing method)
- For stricter argument checking and return behavior, use lambdas (next section)
- Use `&` to convert between Procs and blocks

---

## 8.5 Lambdas vs Python Lambdas

Ruby's lambdas are a special type of Proc with stricter argument checking and different `return` behavior. Despite the name similarity, Ruby lambdas are more powerful than Python's lambda functions.

**Key Similarities:**
- Both called "lambda"
- Both are anonymous functions
- Both can be stored in variables

**Key Differences:**
- Ruby lambdas can have multiple statements (Python lambdas are single expression)
- Ruby lambdas are a type of Proc object
- Argument checking is different
- Return behavior is different

### Python Example

```python
# Python lambdas are single-expression anonymous functions

# Basic lambda
double = lambda x: x * 2
double(5)  # 10

# Lambda with multiple parameters
add = lambda x, y: x + y
add(3, 5)  # 8

# Lambdas in data structures
operations = {
    'add': lambda x, y: x + y,
    'subtract': lambda x, y: x - y,
    'multiply': lambda x, y: x * y
}

operations['add'](10, 5)  # 15

# Lambdas with map, filter, reduce
from functools import reduce

numbers = [1, 2, 3, 4, 5]
doubled = list(map(lambda x: x * 2, numbers))
evens = list(filter(lambda x: x % 2 == 0, numbers))
total = reduce(lambda acc, x: acc + x, numbers)

# LIMITATION: Only single expression
# This doesn't work:
# multi_line = lambda x: (
#     temp = x * 2
#     return temp + 5
# )

# For multi-line, must use def:
def multi_line(x):
    temp = x * 2
    return temp + 5

# Lambdas in sorting
users = [
    {'name': 'Charlie', 'age': 35},
    {'name': 'Alice', 'age': 30},
    {'name': 'Bob', 'age': 25}
]

sorted_users = sorted(users, key=lambda u: u['age'])
# [{'name': 'Bob', 'age': 25}, ...]

# Closures work with lambdas
def make_adder(n):
    return lambda x: x + n

add_5 = make_adder(5)
add_5(10)  # 15
```

### Ruby Equivalent

```ruby
# Ruby lambdas are Proc objects with stricter behavior

# Basic lambda
double = lambda { |x| x * 2 }
double.call(5)  # 10

# Alternative lambda syntax (stabby lambda - Ruby 1.9+)
double = ->(x) { x * 2 }
double.call(5)  # 10

# Lambda with multiple parameters
add = lambda { |x, y| x + y }
add.call(3, 5)  # 8

# Stabby lambda syntax
add = ->(x, y) { x + y }
add.call(3, 5)  # 8

# Lambdas in data structures
operations = {
  add: ->(x, y) { x + y },
  subtract: ->(x, y) { x - y },
  multiply: ->(x, y) { x * y }
}

operations[:add].call(10, 5)  # 15

# Lambdas with map, filter, reduce
numbers = [1, 2, 3, 4, 5]
doubled = numbers.map(&->(x) { x * 2 })
evens = numbers.select(&->(x) { x.even? })
total = numbers.reduce(&->(acc, x) { acc + x })

# ADVANTAGE: Multi-line lambdas work!
multi_line = lambda { |x|
  temp = x * 2
  temp + 5
}
multi_line.call(5)  # 15

# Or with stabby lambda
multi_line = ->(x) do
  temp = x * 2
  temp + 5
end
multi_line.call(5)  # 15

# Lambdas in sorting
users = [
  { name: 'Charlie', age: 35 },
  { name: 'Alice', age: 30 },
  { name: 'Bob', age: 25 }
]

sorted_users = users.sort_by { |u| u[:age] }
# Or with lambda:
sorted_users = users.sort_by(&->(u) { u[:age] })

# Closures work with lambdas
def make_adder(n)
  lambda { |x| x + n }
  # Or: ->(x) { x + n }
end

add_5 = make_adder(5)
add_5.call(10)  # 15
```

### Explanation

Ruby lambdas are created with the `lambda` keyword or the `->` (stabby lambda) syntax. They're similar to Procs but with two key differences: strict argument checking and local return behavior.

**Creating lambdas:**
```ruby
# Using lambda keyword
my_lambda = lambda { |x| x * 2 }

# Using stabby lambda (->)
my_lambda = ->(x) { x * 2 }

# Multi-line stabby lambda
my_lambda = ->(x) do
  x * 2
end

# With multiple parameters
add = ->(x, y) { x + y }

# With default parameters
greet = ->(name = "World") { "Hello, #{name}!" }
greet.call          # "Hello, World!"
greet.call("Alice") # "Hello, Alice!"
```

**Calling lambdas:**
```ruby
my_lambda = ->(x) { x * 2 }

# Same as Procs
my_lambda.call(5)   # 10
my_lambda.(5)       # 10
my_lambda[5]        # 10
```

**Strict argument checking (key difference from Procs):**
```ruby
# Lambda enforces argument count
my_lambda = ->(x, y) { x + y }

my_lambda.call(5, 3)      # 8 - correct
# my_lambda.call(5)       # ArgumentError: wrong number of arguments
# my_lambda.call(5, 3, 1) # ArgumentError: wrong number of arguments

# Compare to Proc (lenient)
my_proc = Proc.new { |x, y| x + y }
my_proc.call(5)           # No error, y is nil (causes error on +)
my_proc.call(5, 3, 1)     # No error, ignores extra argument
```

**Return behavior (key difference from Procs):**
```ruby
# Lambda's return only returns from the lambda itself
def test_lambda
  my_lambda = lambda { return "from lambda" }
  result = my_lambda.call
  "from method (result: #{result})"
end

test_lambda  # "from method (result: from lambda)"
# The method continues executing after the lambda!

# Compare to Proc (returns from enclosing method)
def test_proc
  my_proc = Proc.new { return "from proc" }
  my_proc.call
  "from method"  # Never reached
end

test_proc  # "from proc"
```

**Multi-line lambdas (advantage over Python):**
```ruby
# Ruby lambdas can have multiple statements
process = ->(x) do
  puts "Processing #{x}"
  squared = x ** 2
  puts "Squared: #{squared}"
  doubled = squared * 2
  puts "Doubled: #{doubled}"
  doubled  # Return value
end

process.call(5)
# Processing 5
# Squared: 25
# Doubled: 50
# => 50
```

**Lambdas with keyword arguments:**
```ruby
greet = ->(name:, greeting: "Hello") do
  "#{greeting}, #{name}!"
end

greet.call(name: "Alice")                      # "Hello, Alice!"
greet.call(name: "Bob", greeting: "Hi")        # "Hi, Bob!"
# greet.call("Alice")                          # ArgumentError
```

**Checking if a Proc is a lambda:**
```ruby
my_lambda = lambda { |x| x * 2 }
my_proc = Proc.new { |x| x * 2 }

my_lambda.lambda?  # true
my_proc.lambda?    # false
```

**Practical example - function composition:**
```ruby
def compose(f, g)
  ->(x) { f.call(g.call(x)) }
end

double = ->(x) { x * 2 }
add_one = ->(x) { x + 1 }

double_then_add = compose(add_one, double)
double_then_add.call(5)  # 11 (5 * 2 = 10, 10 + 1 = 11)
```

**Best Practice:**
- Prefer lambdas over Procs for most use cases
- Use `->` syntax for modern Ruby code
- Use lambdas when you want strict argument checking
- Use lambdas when you need proper `return` semantics
- Use multi-line lambdas for complex logic
- Remember that lambdas are just Procs with stricter behavior

**Lambda vs Proc summary:**
```ruby
# Procs:
# - Lenient with arguments
# - return exits the enclosing method
# - Created with Proc.new or proc

# Lambdas:
# - Strict with arguments (like methods)
# - return exits only the lambda
# - Created with lambda or ->

# Choose lambdas for most cases unless you specifically need Proc behavior
```

---

## 8.6 Procs vs Lambdas

Now that we've covered both Procs and lambdas, let's directly compare them to understand when to use each one.

**Key Similarities:**
- Both are Proc objects
- Both can be stored in variables
- Both can be passed as arguments
- Both support closures
- Both can be called with `.call`, `.()`, or `[]`

**Key Differences:**
- Argument checking (strict vs lenient)
- Return behavior (local vs non-local)
- Creation syntax

### Comparison Table

| Feature | Proc | Lambda |
|---------|------|--------|
| **Creation** | `Proc.new { }` or `proc { }` | `lambda { }` or `->() { }` |
| **Argument checking** | Lenient (ignores extras, nils for missing) | Strict (raises ArgumentError) |
| **Return behavior** | Returns from enclosing method | Returns from lambda only |
| **Is a Proc?** | Yes | Yes (special type) |
| **Check with** | `proc.lambda?` returns `false` | `proc.lambda?` returns `true` |

### Detailed Comparison

```ruby
# ==========================================
# 1. ARGUMENT CHECKING
# ==========================================

# Proc - lenient
my_proc = Proc.new { |x, y| "x=#{x}, y=#{y}" }

my_proc.call(1, 2)        # "x=1, y=2"
my_proc.call(1)           # "x=1, y="  (missing arg becomes nil)
my_proc.call(1, 2, 3, 4)  # "x=1, y=2" (ignores extras)

# Lambda - strict (like a method)
my_lambda = ->(x, y) { "x=#{x}, y=#{y}" }

my_lambda.call(1, 2)      # "x=1, y=2"
# my_lambda.call(1)       # ArgumentError: wrong number of arguments (given 1, expected 2)
# my_lambda.call(1, 2, 3) # ArgumentError: wrong number of arguments (given 3, expected 2)

# ==========================================
# 2. RETURN BEHAVIOR
# ==========================================

# Proc - return exits the enclosing method
def proc_return_test
  my_proc = Proc.new { return "exiting method" }
  my_proc.call
  "this never executes"
end

proc_return_test  # "exiting method"

# Lambda - return exits only the lambda
def lambda_return_test
  my_lambda = lambda { return "exiting lambda" }
  result = my_lambda.call
  "result was: #{result}"
end

lambda_return_test  # "result was: exiting lambda"

# ==========================================
# 3. BREAK BEHAVIOR
# ==========================================

# Proc - break exits the enclosing method
def proc_break_test
  my_proc = Proc.new { break "breaking" }
  my_proc.call
  "never reached"
end

# proc_break_test  # LocalJumpError (can't break from Proc)

# Lambda - break works like return
def lambda_break_test
  my_lambda = lambda { break "breaking" }
  result = my_lambda.call
  "result was: #{result}"
end

lambda_break_test  # "result was: breaking"

# ==========================================
# 4. CHECKING TYPE
# ==========================================

my_proc = Proc.new { |x| x * 2 }
my_lambda = lambda { |x| x * 2 }
stabby = ->(x) { x * 2 }

my_proc.class        # Proc
my_lambda.class      # Proc (lambdas are Procs!)
stabby.class         # Proc

my_proc.lambda?      # false
my_lambda.lambda?    # true
stabby.lambda?       # true

# ==========================================
# 5. PRACTICAL EXAMPLES
# ==========================================

# Example 1: Lenient arguments (use Proc)
# Good for destructuring with varied input
array_printer = Proc.new { |first, *rest|
  puts "First: #{first}"
  puts "Rest: #{rest.inspect}"
}

array_printer.call(1, 2, 3)      # Works
array_printer.call(1)            # Works (rest is [])
# array_printer.call()           # Works (first is nil, rest is [])

# Example 2: Strict arguments (use Lambda)
# Good for well-defined operations
divide = ->(numerator, denominator) {
  numerator / denominator
}

divide.call(10, 2)  # 5
# divide.call(10)   # ArgumentError - must provide both arguments

# Example 3: Control flow (use Lambda)
# When you want return to work normally
def process_items(items)
  processor = lambda { |item|
    return nil if item.nil?  # Returns from lambda, not method
    item.upcase
  }

  items.map { |item| processor.call(item) }
end

process_items(["hello", nil, "world"])  # ["HELLO", nil, "WORLD"]

# Example 4: Callbacks (use Lambda)
class EventEmitter
  def initialize
    @listeners = []
  end

  def on(event, &block)
    @listeners << lambda { |data|
      block.call(data) if data[:event] == event
    }
  end

  def emit(event, data = {})
    @listeners.each { |listener| listener.call(event: event, **data) }
  end
end

emitter = EventEmitter.new
emitter.on(:save) { |data| puts "Saved: #{data[:name]}" }
emitter.emit(:save, name: "document.txt")
# Saved: document.txt

# ==========================================
# 6. CONVERTING BETWEEN PROC AND LAMBDA
# ==========================================

# Can't directly convert, but can wrap
def proc_to_lambda(a_proc)
  lambda { |*args| a_proc.call(*args) }
end

my_proc = Proc.new { |x| x * 2 }
my_lambda = proc_to_lambda(my_proc)

my_lambda.lambda?  # true

# ==========================================
# 7. PERFORMANCE
# ==========================================

# Lambdas have slight overhead for argument checking
# But the difference is negligible in most cases
# Choose based on semantics, not performance

require 'benchmark'

my_proc = Proc.new { |x| x * 2 }
my_lambda = lambda { |x| x * 2 }

n = 1_000_000
Benchmark.bm do |x|
  x.report("Proc:  ") { n.times { my_proc.call(5) } }
  x.report("Lambda:") { n.times { my_lambda.call(5) } }
end
# Performance is nearly identical
```

### When to Use Each

**Use Lambdas when:**
- You want strict argument checking (like normal methods)
- You need proper `return` semantics
- You're creating well-defined operations
- You want method-like behavior
- **Default choice for most cases**

**Use Procs when:**
- You need lenient argument handling
- You're working with varying argument counts
- You specifically need `return` to exit the enclosing method
- You're converting blocks to objects (with `&block`)

### Practical Decision Guide

```ruby
# ❌ Don't overthink it - lambdas are usually the right choice
my_lambda = ->(x, y) { x + y }

# ✅ Use lambdas by default
calculate = ->(a, b, operation = :add) do
  case operation
  when :add then a + b
  when :subtract then a - b
  when :multiply then a * b
  when :divide then a / b
  end
end

# ✅ Use Procs when capturing blocks
def with_timing(&block)
  start = Time.now
  result = block.call  # block is a Proc
  puts "Took #{Time.now - start} seconds"
  result
end

# ✅ Use lambdas for callbacks
button.on_click(->(event) {
  puts "Clicked at #{event.x}, #{event.y}"
})

# ✅ Use lambdas for functional programming
compose = ->(f, g) { ->(x) { f.call(g.call(x)) } }
double = ->(x) { x * 2 }
increment = ->(x) { x + 1 }

double_and_increment = compose.(increment, double)
double_and_increment.call(5)  # 11
```

### Best Practice

**General rules:**
1. **Default to lambdas** - they behave more like methods
2. Use `->` syntax for lambdas (modern and concise)
3. Use `Proc.new` or `proc` only when you specifically need Proc semantics
4. Be explicit about which you're using in documentation
5. Test argument handling carefully if using Procs

**Code style:**
```ruby
# Good - lambda with stabby syntax
process = ->(data) { data.transform }

# Good - Proc when capturing blocks
def method_name(&block)
  block.call  # block is a Proc
end

# Avoid - mixing styles unnecessarily
# Sometimes lambda { }, sometimes ->, sometimes Proc.new
# Pick one style and stick with it (prefer -> for lambdas)
```

---

## 8.7 Using Blocks as Arguments (`&block`)

While `yield` is great for simple cases, sometimes you need to store, pass around, or manipulate the block itself. Ruby lets you capture blocks as Proc objects using the `&block` parameter syntax.

**Key Similarities:**
- Similar to Python's `*args` for capturing variable arguments
- Allows storing callbacks
- Enables passing behavior between methods

**Key Differences:**
- Ruby uses `&` prefix for block parameters
- Only one block parameter allowed per method
- Automatically converts between blocks and Procs

### Python Example

```python
# Python uses explicit function parameters for callbacks

def apply_to_list(items, func):
    return [func(item) for item in items]

result = apply_to_list([1, 2, 3], lambda x: x * 2)
# [2, 4, 6]

# Storing callbacks
class Button:
    def __init__(self):
        self.click_handlers = []

    def on_click(self, handler):
        self.click_handlers.append(handler)

    def click(self):
        for handler in self.click_handlers:
            handler()

button = Button()
button.on_click(lambda: print("Clicked!"))
button.on_click(lambda: print("Also clicked!"))
button.click()
# Clicked!
# Also clicked!

# Passing functions between methods
def with_logging(func):
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__}")
        result = func(*args, **kwargs)
        print(f"Finished {func.__name__}")
        return result
    return wrapper

def add(a, b):
    return a + b

logged_add = with_logging(add)
logged_add(5, 3)
# Calling add
# Finished add
# 8
```

### Ruby Equivalent

```ruby
# Using &block to capture blocks as Proc objects

def apply_to_array(items, &block)
  items.map(&block)
end

result = apply_to_array([1, 2, 3]) { |x| x * 2 }
# [2, 4, 6]

# Storing callbacks
class Button
  def initialize
    @click_handlers = []
  end

  def on_click(&handler)
    @click_handlers << handler
  end

  def click
    @click_handlers.each { |handler| handler.call }
  end
end

button = Button.new
button.on_click { puts "Clicked!" }
button.on_click { puts "Also clicked!" }
button.click
# Clicked!
# Also clicked!

# Passing blocks between methods
def with_logging(&block)
  puts "Starting operation"
  result = block.call
  puts "Finished operation"
  result
end

def process_data(&block)
  with_logging(&block)  # Pass block along
end

process_data { 5 + 3 }
# Starting operation
# Finished operation
# 8

# Converting block to Proc and back
def capture_block(&block)
  block  # Returns a Proc object
end

my_proc = capture_block { |x| x * 2 }
my_proc.class  # Proc
my_proc.call(5)  # 10

# Using the captured Proc as a block again
[1, 2, 3].map(&my_proc)  # [2, 4, 6]
```

### Explanation

The `&block` parameter syntax captures a block passed to a method as a Proc object. This allows you to store, pass, and manipulate blocks just like any other object.

**Basic syntax:**
```ruby
def method_name(&block)
  # block is now a Proc object
  block.call
end

method_name { puts "Hello" }
# Hello
```

**Difference between `yield` and `&block`:**
```ruby
# Using yield (simpler, faster)
def with_yield
  yield
end

with_yield { puts "Hello" }

# Using &block (more flexible)
def with_block(&block)
  block.call
end

with_block { puts "Hello" }
```

**Why use `&block` over `yield`?**

1. **Store the block:**
```ruby
class Calculator
  def initialize(&operation)
    @operation = operation
  end

  def calculate(a, b)
    @operation.call(a, b)
  end
end

calc = Calculator.new { |x, y| x + y }
calc.calculate(5, 3)  # 8
```

2. **Pass block to another method:**
```ruby
def outer_method(&block)
  inner_method(&block)  # Pass it along
end

def inner_method(&block)
  puts "In inner method"
  block.call
end

outer_method { puts "In block" }
# In inner method
# In block
```

3. **Check if block was given:**
```ruby
def optional_block(&block)
  if block
    block.call
  else
    puts "No block given"
  end
end

optional_block { puts "Block!" }  # Block!
optional_block                     # No block given

# Alternatively with yield:
def optional_yield
  if block_given?
    yield
  else
    puts "No block given"
  end
end
```

4. **Store multiple callbacks:**
```ruby
class EventEmitter
  def initialize
    @callbacks = {}
  end

  def on(event, &block)
    @callbacks[event] ||= []
    @callbacks[event] << block
  end

  def emit(event, *args)
    return unless @callbacks[event]
    @callbacks[event].each { |callback| callback.call(*args) }
  end
end

emitter = EventEmitter.new
emitter.on(:save) { |name| puts "Saving #{name}" }
emitter.on(:save) { |name| puts "Logging save of #{name}" }
emitter.emit(:save, "document.txt")
# Saving document.txt
# Logging save of document.txt
```

**Converting between blocks and Procs with `&`:**

The `&` operator works in both directions:
- `&block` in parameter list: block → Proc
- `&proc` in argument list: Proc → block

```ruby
# Block to Proc (in parameter)
def capture(&block)
  block  # Now a Proc
end

my_proc = capture { |x| x * 2 }

# Proc to block (in argument)
[1, 2, 3].map(&my_proc)  # [2, 4, 6]

# Works with lambdas too
my_lambda = ->(x) { x * 2 }
[1, 2, 3].map(&my_lambda)  # [2, 4, 6]
```

**Combining regular parameters and &block:**
```ruby
def process(items, multiplier, &block)
  items.map do |item|
    result = item * multiplier
    block.call(result) if block
    result
  end
end

process([1, 2, 3], 2) { |x| puts "Processed: #{x}" }
# Processed: 2
# Processed: 4
# Processed: 6
# [2, 4, 6]
```

**Important: Only one &block parameter:**
```ruby
# This is invalid:
# def method(¶m1, ¶m2)  # ERROR: Only one & parameter allowed
#   ...
# end

# If you need multiple blocks, use explicit Proc parameters:
def method(block1, block2)
  block1.call
  block2.call
end

method(
  lambda { puts "First" },
  lambda { puts "Second" }
)
```

**Performance consideration:**
```ruby
# yield is slightly faster than &block
# Because &block creates a Proc object

# Use yield when:
# - You just need to execute the block
# - Performance is critical
# - You don't need to store or pass the block

# Use &block when:
# - You need to store the block
# - You need to pass it to another method
# - You need to inspect or manipulate it
```

**Practical example - method decorator:**
```ruby
def benchmark(&block)
  start_time = Time.now
  result = block.call
  end_time = Time.now
  puts "Execution time: #{end_time - start_time}s"
  result
end

def with_retry(max_attempts = 3, &block)
  attempts = 0
  begin
    attempts += 1
    block.call
  rescue StandardError => e
    if attempts < max_attempts
      puts "Attempt #{attempts} failed, retrying..."
      retry
    else
      raise
    end
  end
end

# Using the decorators
with_retry do
  benchmark do
    # Some operation
    sleep(0.1)
    puts "Done!"
  end
end
```

**Best Practice:**
- Use `yield` for simple block execution
- Use `&block` when you need to store, pass, or inspect the block
- Use `&` to convert between blocks and Procs
- Remember: only one `&block` parameter per method
- Name it `&block` by convention (could be `&callback`, `&handler`, etc.)
- Check for block presence with `if block` or `block_given?`

---

## 8.8 The `&:symbol` Shorthand

One of Ruby's most elegant features is the `&:symbol` shorthand for passing simple method calls as blocks. This is a unique Ruby idiom with no direct Python equivalent.

**Key Similarities:**
- Similar to Python's `operator.methodcaller` (but more elegant)
- Reduces boilerplate in functional operations

**Key Differences:**
- Ruby's `&:` syntax is built into the language
- Python requires explicit imports or lambdas
- Ruby's version is more concise and readable

### Python Example

```python
# Python requires explicit lambdas for method calls

numbers = [1, 2, 3, 4, 5]

# Check if even - need lambda
evens = list(filter(lambda x: x % 2 == 0, numbers))

# Convert to string - need lambda
strings = list(map(lambda x: str(x), numbers))

# Using operator module (less common)
from operator import methodcaller

words = ["hello", "world"]
upper = list(map(methodcaller('upper'), words))
# ['HELLO', 'WORLD']

# Still verbose compared to Ruby
strings = [" hello ", " world "]
trimmed = list(map(methodcaller('strip'), strings))

# Most common: explicit lambda
doubled = list(map(lambda x: x * 2, numbers))
uppered = list(map(lambda s: s.upper(), words))

# List comprehension (still explicit)
doubled = [x * 2 for x in numbers]
uppered = [s.upper() for s in words]
```

### Ruby Equivalent

```ruby
numbers = [1, 2, 3, 4, 5]

# &:symbol shorthand - extremely concise!

# Check if even
evens = numbers.select(&:even?)
# [2, 4]

# Convert to string
strings = numbers.map(&:to_s)
# ["1", "2", "3", "4", "5"]

# Uppercase strings
words = ["hello", "world"]
uppered = words.map(&:upcase)
# ["HELLO", "WORLD"]

# Strip whitespace
strings = [" hello ", " world "]
trimmed = strings.map(&:strip)
# ["hello", "world"]

# Negate numbers
negated = numbers.map(&:-@)
# [-1, -2, -3, -4, -5]

# Sum (when used with reduce)
total = numbers.reduce(&:+)
# 15

# Get length of each string
words = ["hello", "world", "ruby"]
lengths = words.map(&:length)
# [5, 5, 4]
```

### Explanation

The `&:symbol` syntax is shorthand for passing a method call as a block. It's one of Ruby's most distinctive and beloved features.

**How it works:**

```ruby
# These are equivalent:
[1, 2, 3].map { |n| n.to_s }
[1, 2, 3].map(&:to_s)

# And these:
[1, 2, 3].select { |n| n.even? }
[1, 2, 3].select(&:even?)

# And these:
words.map { |w| w.upcase }
words.map(&:upcase)
```

**What's happening behind the scenes:**

1. `:to_s` is a Symbol
2. `&` tries to convert it to a Proc
3. Symbol has a `to_proc` method that returns a Proc
4. That Proc calls the method represented by the symbol

```ruby
# Equivalent to:
:to_s.to_proc.call(123)  # "123"

# Symbol#to_proc is approximately:
class Symbol
  def to_proc
    ->(obj) { obj.send(self) }
  end
end
```

**Common use cases:**

```ruby
# 1. Map transformations
numbers = [1, 2, 3, 4, 5]

numbers.map(&:to_s)      # ["1", "2", "3", "4", "5"]
numbers.map(&:to_f)      # [1.0, 2.0, 3.0, 4.0, 5.0]
numbers.map(&:even?)     # [false, true, false, true, false]

# 2. Select/filter with predicate methods
numbers.select(&:even?)  # [2, 4]
numbers.select(&:odd?)   # [1, 3, 5]
numbers.reject(&:zero?)  # [1, 2, 3, 4, 5]

# 3. String operations
words = ["hello", "world", "ruby"]

words.map(&:upcase)      # ["HELLO", "WORLD", "RUBY"]
words.map(&:capitalize)  # ["Hello", "World", "Ruby"]
words.map(&:length)      # [5, 5, 4]
words.map(&:reverse)     # ["olleh", "dlrow", "ybur"]

# 4. Reduce with operators
numbers.reduce(&:+)      # 15 (sum)
numbers.reduce(&:*)      # 120 (product)
[10, 5, 2].reduce(&:-)   # 3 (10 - 5 - 2)
[100, 10, 2].reduce(&:/) # 5 (100 / 10 / 2)

# 5. Boolean operations
[true, true, true].all?(&:itself)   # true
[true, false, true].any?(&:itself)  # true
[false, nil].none?(&:itself)        # true

# 6. Hash operations
users = [
  { name: "Alice", age: 30 },
  { name: "Bob", age: 25 },
  { name: "Charlie", age: 35 }
]

users.map { |u| u[:name] }  # ["Alice", "Bob", "Charlie"]
# Can't use &:[] for hash access, but:
users.map(&:values)         # [[30, "Alice"], [25, "Bob"], [35, "Charlie"]]
users.map(&:keys)           # [[:name, :age], [:name, :age], [:name, :age]]

# 7. Checking nil
values = [1, nil, 2, nil, 3]
values.reject(&:nil?)       # [1, 2, 3]
values.select(&:nil?)       # [nil, nil]
values.map(&:to_s)          # ["1", "", "2", "", "3"]

# 8. Calling methods that return values
files = ["file1.txt", "file2.txt", "file3.txt"]
files.map(&:upcase)         # ["FILE1.TXT", "FILE2.TXT", "FILE3.TXT"]
```

**Limitations:**

```ruby
# Only works for methods with no required arguments
[1, 2, 3].map(&:to_s)    # ✓ Works (no args)
# [1, 2, 3].map(&:+)     # ✗ Doesn't work (requires an argument)

# Can't pass arguments
# ["hello", "world"].map(&:gsub("h", "H"))  # ✗ Syntax error

# For methods with arguments, use a block:
["hello", "world"].map { |s| s.gsub("h", "H") }

# Only works for method calls, not complex expressions
# [1, 2, 3].map(&:* 2)   # ✗ Syntax error
[1, 2, 3].map { |n| n * 2 }  # ✓ Use a block
```

**Chaining with &:symbol:**

```ruby
# Beautiful chaining
result = ["  hello  ", "  WORLD  ", "  Ruby  "]
  .map(&:strip)
  .map(&:downcase)
  .map(&:capitalize)
# ["Hello", "World", "Ruby"]

# Mix &:symbol with blocks
numbers = [1, 2, 3, 4, 5]
result = numbers
  .select(&:even?)
  .map { |n| n ** 2 }
  .reduce(&:+)
# 20 (4 + 16 = 20)
```

**Custom classes with &:symbol:**

```ruby
class User
  attr_reader :name, :age

  def initialize(name, age)
    @name = name
    @age = age
  end

  def adult?
    @age >= 18
  end
end

users = [
  User.new("Alice", 30),
  User.new("Bob", 16),
  User.new("Charlie", 25)
]

# Works with any method!
users.map(&:name)        # ["Alice", "Bob", "Charlie"]
users.map(&:age)         # [30, 16, 25]
users.select(&:adult?)   # [Alice and Charlie]
users.reject(&:adult?)   # [Bob]
```

**Practical example - data processing pipeline:**

```ruby
# Process a CSV-like structure
data = [
  "  alice@example.com  ",
  "  BOB@EXAMPLE.COM  ",
  "  charlie@example.com  "
]

cleaned_emails = data
  .map(&:strip)
  .map(&:downcase)
  .select { |email| email.include?("@") }
  .uniq
# ["alice@example.com", "bob@example.com", "charlie@example.com"]

# Get word lengths
sentence = "the quick brown fox jumps"
word_lengths = sentence
  .split
  .map(&:length)
# [3, 5, 5, 3, 5]
```

**Best Practice:**
- Use `&:symbol` for simple, no-argument method calls
- Prefer `&:symbol` over `{ |x| x.method }` for readability
- Use blocks when the operation requires arguments
- Use blocks for complex expressions
- Chain `&:symbol` operations for elegant data transformations
- Common patterns: `map(&:to_s)`, `select(&:present?)`, `reduce(&:+)`

**Remember:**
- `&:method_name` is shorthand for `{ |x| x.method_name }`
- Only works for methods without required arguments
- One of Ruby's most recognizable idioms
- Makes functional-style code very clean and readable

---

This concludes Chapter 8 on Blocks, Procs, and Lambdas. You've now learned one of Ruby's most powerful and distinctive features. Blocks are fundamental to Ruby programming and are used extensively throughout the ecosystem. Understanding blocks, when to use Procs vs lambdas, and shortcuts like `&:symbol` will make you much more effective as a Ruby developer.

In the next chapter, we'll explore Classes and Object-Oriented Programming in Ruby.
