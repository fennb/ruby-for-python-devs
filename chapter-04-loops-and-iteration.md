# Chapter 4: Loops and Iteration

Iteration in Ruby will feel both familiar and refreshingly different if you're coming from Python. While Ruby has traditional loops like `while` and `for`, idiomatic Ruby code heavily favors **iterator methods** like `.each`, `.times`, and `.map`. This chapter will show you both approaches, but you'll quickly learn why Rubyists prefer iterators.

---

## 4.1 `while` and `until` Loops

Ruby's `while` loops work similarly to Python's, executing code repeatedly while a condition is true. Ruby also provides `until`, which loops while a condition is **false** (the opposite of `while`).

**Key Similarities:**
- Both execute code repeatedly based on a condition
- Both check the condition before each iteration
- Both support `break` to exit early

**Key Differences:**
- Ruby has `until` (the opposite of `while`)
- Ruby uses `end` to close the loop block
- Ruby supports statement modifier forms
- No colon after the condition in Ruby

### Python Example

```python
# Basic while loop
count = 0
while count < 5:
    print(f"Count: {count}")
    count += 1

# Output:
# Count: 0
# Count: 1
# Count: 2
# Count: 3
# Count: 4

# While with condition check
password = ""
while password != "secret":
    password = input("Enter password: ")

print("Access granted!")

# Python doesn't have 'until', so use 'while not'
attempts = 0
max_attempts = 3

while not attempts >= max_attempts:
    print(f"Attempt {attempts + 1}")
    attempts += 1

# Infinite loop with break
while True:
    user_input = input("Enter 'quit' to exit: ")
    if user_input == "quit":
        break
    print(f"You entered: {user_input}")
```

### Ruby Equivalent

```ruby
# Basic while loop
count = 0
while count < 5
  puts "Count: #{count}"
  count += 1
end

# Output:
# Count: 0
# Count: 1
# Count: 2
# Count: 3
# Count: 4

# While with condition check
password = ""
while password != "secret"
  print "Enter password: "
  password = gets.chomp
end

puts "Access granted!"

# Ruby's 'until' loop (opposite of 'while')
attempts = 0
max_attempts = 3

until attempts >= max_attempts
  puts "Attempt #{attempts + 1}"
  attempts += 1
end

# Infinite loop with break
loop do
  print "Enter 'quit' to exit: "
  user_input = gets.chomp
  break if user_input == "quit"
  puts "You entered: #{user_input}"
end

# Statement modifier forms (one-liners)
count = 0
puts count += 1 while count < 5  # Prints 1, 2, 3, 4, 5

count = 10
puts count -= 1 until count == 0  # Prints 9, 8, 7, 6, 5, 4, 3, 2, 1, 0
```

### Explanation

**`while` loop:**
```ruby
while condition
  # code to execute
end
```

The loop continues as long as the condition evaluates to true.

**`until` loop:**
```ruby
until condition
  # code to execute
end
```

The loop continues as long as the condition evaluates to false. Think of it as "while not condition."

**When to use `until`:**
Use `until` when you're waiting for something to become true:
```ruby
# More readable with 'until'
until user.verified?
  send_verification_email
  sleep 60
end

# Less readable with 'while'
while !user.verified?
  send_verification_email
  sleep 60
end
```

**The `loop` method:**
Ruby's `loop` is a method (not a keyword) that creates an infinite loop:
```ruby
loop do
  # infinite loop
  break if some_condition
end
```

This is often preferred over `while true` because it's more idiomatic Ruby.

**Statement modifiers:**
For simple loops, you can use the statement modifier form:
```ruby
i = 0
i += 1 while i < 10

# Or with 'until'
i = 10
i -= 1 until i == 0
```

**Important notes:**
- Ruby doesn't have `++` or `--` operators. Use `+= 1` and `-= 1` instead.
- `gets.chomp` is Ruby's equivalent of Python's `input()` (`.chomp` removes the trailing newline)
- Unlike Python, parentheses are optional for method calls like `puts` and `print`

---

## 4.2 `for` Loops

Both Ruby and Python have `for` loops, but they work quite differently. More importantly, **idiomatic Ruby rarely uses `for` loops** - Rubyists prefer iterator methods like `.each` (covered in section 4.5).

**Key Differences:**
- Python's `for` loop is an iterator over sequences
- Ruby's `for` loop is syntactic sugar for `.each`
- Ruby's `for` doesn't create a new scope (variables leak out!)
- Most Rubyists avoid `for` and use `.each` instead

### Python Example

```python
# Iterating over a list
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# Output:
# apple
# banana
# cherry

# Iterating over a range
for i in range(5):
    print(f"Number: {i}")

# Output:
# Number: 0
# Number: 1
# Number: 2
# Number: 3
# Number: 4

# Iterating with index
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# Output:
# 0: apple
# 1: banana
# 2: cherry

# Iterating over dictionary
person = {"name": "Alice", "age": 30, "city": "NYC"}
for key, value in person.items():
    print(f"{key}: {value}")

# Output:
# name: Alice
# age: 30
# city: NYC

# Nested loops
for i in range(3):
    for j in range(3):
        print(f"({i}, {j})", end=" ")
    print()

# Output:
# (0, 0) (0, 1) (0, 2)
# (1, 0) (1, 1) (1, 2)
# (2, 0) (2, 1) (2, 2)
```

### Ruby Equivalent

```ruby
# For loop (NOT idiomatic Ruby - shown for comparison)
fruits = ["apple", "banana", "cherry"]
for fruit in fruits
  puts fruit
end

# Output:
# apple
# banana
# cherry

# IDIOMATIC RUBY - use .each instead
fruits.each do |fruit|
  puts fruit
end

# Iterating over a range (with for - not idiomatic)
for i in 0..4
  puts "Number: #{i}"
end

# IDIOMATIC RUBY - use .each with a range
(0..4).each do |i|
  puts "Number: #{i}"
end

# Or even better - use .times when starting from 0
5.times do |i|
  puts "Number: #{i}"
end

# Iterating with index (IDIOMATIC)
fruits.each_with_index do |fruit, index|
  puts "#{index}: #{fruit}"
end

# Output:
# 0: apple
# 1: banana
# 2: cherry

# Iterating over a hash
person = { name: "Alice", age: 30, city: "NYC" }
person.each do |key, value|
  puts "#{key}: #{value}"
end

# Output:
# name: Alice
# age: 30
# city: NYC

# Nested loops (idiomatic with ranges)
(0..2).each do |i|
  (0..2).each do |j|
    print "(#{i}, #{j}) "
  end
  puts
end

# Output:
# (0, 0) (0, 1) (0, 2)
# (1, 0) (1, 1) (1, 2)
# (2, 0) (2, 1) (2, 2)
```

### Explanation

**Why Rubyists avoid `for` loops:**

1. **No new scope:** Variables defined in a `for` loop leak into the outer scope:
```ruby
for i in 1..3
  x = i * 2
end

puts x  # Output: 6 (x is accessible outside the loop!)

# With .each, block variables are scoped
(1..3).each do |i|
  y = i * 2
end

puts y  # Error: undefined local variable 'y'
```

2. **Less flexible:** Iterator methods like `.each` are more composable and chainable.

3. **Less idiomatic:** Experienced Rubyists will recognize you as a beginner if you use `for` loops.

**The Ruby way - blocks and iterators:**

Instead of `for` loops, Ruby uses **blocks** (code enclosed in `do...end` or `{...}`):

```ruby
# Single-line block with {}
fruits.each { |fruit| puts fruit }

# Multi-line block with do...end
fruits.each do |fruit|
  puts fruit.upcase
  puts "---"
end

# Block parameters are between | | (pipes)
fruits.each do |fruit|  # 'fruit' is the block parameter
  puts fruit
end
```

**Block syntax rules:**
- Use `{...}` for single-line blocks
- Use `do...end` for multi-line blocks
- Block parameters go between `|pipes|`

**Important:** We'll cover blocks in much more detail in Chapter 8. For now, just understand that `.each` passes each element to the block.

---

## 4.3 Loop Control (`break`, `next`, `redo`)

Both Ruby and Python provide ways to control loop execution. Ruby has `break` and `next` (similar to Python's `break` and `continue`), plus an additional keyword: `redo`.

**Key Similarities:**
- `break` exits the loop entirely (both languages)
- Python's `continue` is called `next` in Ruby

**Key Differences:**
- Ruby has `redo` which restarts the current iteration
- Ruby's loop control keywords work with blocks, not just loops
- Ruby doesn't have Python's `else` clause for loops

### Python Example

```python
# break - exit the loop
for i in range(10):
    if i == 5:
        break
    print(i)

# Output: 0 1 2 3 4

# continue - skip to next iteration
for i in range(5):
    if i == 2:
        continue
    print(i)

# Output: 0 1 3 4

# break with while loop
count = 0
while True:
    count += 1
    if count > 5:
        break
    print(count)

# Output: 1 2 3 4 5

# Nested loops with break
for i in range(3):
    for j in range(3):
        if i == 1 and j == 1:
            break
        print(f"({i}, {j})", end=" ")
    print()

# Output:
# (0, 0) (0, 1) (0, 2)
# (1, 0)
# (2, 0) (2, 1) (2, 2)

# Loop with else clause (Python-specific)
for i in range(5):
    if i == 10:
        break
else:
    print("Loop completed without break")

# Output: Loop completed without break
```

### Ruby Equivalent

```ruby
# break - exit the loop
(0..9).each do |i|
  break if i == 5
  puts i
end

# Output: 0 1 2 3 4

# next - skip to next iteration (like Python's continue)
(0..4).each do |i|
  next if i == 2
  puts i
end

# Output: 0 1 3 4

# break with while loop
count = 0
while true
  count += 1
  break if count > 5
  puts count
end

# Output: 1 2 3 4 5

# Using 'loop' method (more idiomatic)
count = 0
loop do
  count += 1
  break if count > 5
  puts count
end

# Nested loops with break
(0..2).each do |i|
  (0..2).each do |j|
    break if i == 1 && j == 1
    print "(#{i}, #{j}) "
  end
  puts
end

# Output:
# (0, 0) (0, 1) (0, 2)
# (1, 0)
# (2, 0) (2, 1) (2, 2)

# Ruby's 'redo' - restart current iteration
attempts = 0
(0..4).each do |i|
  attempts += 1
  puts "Attempt #{attempts} for iteration #{i}"

  if attempts < 3 && i == 2
    redo  # Restart this iteration
  end

  attempts = 0  # Reset for next iteration
end

# Output:
# Attempt 1 for iteration 0
# Attempt 1 for iteration 1
# Attempt 1 for iteration 2
# Attempt 2 for iteration 2
# Attempt 3 for iteration 2
# Attempt 1 for iteration 3
# Attempt 1 for iteration 4
```

### Explanation

**`break`:**
Exits the loop immediately, just like Python:
```ruby
loop do
  user_input = gets.chomp
  break if user_input == "exit"
  puts "You entered: #{user_input}"
end
```

**`next`:**
Skips the rest of the current iteration and moves to the next one (equivalent to Python's `continue`):
```ruby
(1..10).each do |num|
  next if num.even?  # Skip even numbers
  puts num
end
# Output: 1 3 5 7 9
```

**`redo`:**
Restarts the current iteration without re-evaluating the loop condition or fetching the next element:
```ruby
# Practical example: retry on failure
attempts = 0
loop do
  print "Enter a number: "
  input = gets.chomp

  attempts += 1

  if input.match?(/^\d+$/)
    puts "Valid number: #{input}"
    break
  else
    puts "Invalid! Try again."
    redo if attempts < 3

    puts "Too many attempts. Exiting."
    break
  end
end
```

**Important gotcha:** `redo` doesn't reset loop variables:
```ruby
i = 0
loop do
  puts "i = #{i}"
  i += 1
  redo if i == 2  # This will only redo once
  break if i > 3
end

# Output:
# i = 0
# i = 1
# i = 2
# i = 3
```

**Return values with `break`:**
In Ruby, `break` can return a value:
```ruby
result = loop do
  user_input = gets.chomp
  break user_input if user_input.length > 5
  puts "Too short, try again"
end

puts "You entered: #{result}"
```

**No `else` clause:**
Ruby doesn't have Python's `for...else` construct. If you need similar behavior, use a flag:
```ruby
# Python way
# for item in items:
#     if condition:
#         break
# else:
#     print("Not found")

# Ruby way
found = false
items.each do |item|
  if condition
    found = true
    break
  end
end

puts "Not found" unless found
```

---

## 4.4 The `.times` Iterator

Ruby's `.times` method is a beautiful, readable way to repeat an action a specific number of times. Python doesn't have a direct equivalent - you'd use `for i in range(n)` instead.

**Key Concept:**
- `.times` is called on an integer: `5.times { ... }`
- It passes the current iteration number (0-indexed) to the block
- More readable than `for i in range(n)` for simple repetition

### Python Example

```python
# Repeat action N times
for _ in range(5):
    print("Hello!")

# Output:
# Hello!
# Hello!
# Hello!
# Hello!
# Hello!

# With index
for i in range(5):
    print(f"Iteration {i}")

# Output:
# Iteration 0
# Iteration 1
# Iteration 2
# Iteration 3
# Iteration 4

# Practical example: game loop
for round in range(3):
    print(f"Round {round + 1}")
    # game logic here

# Building a list
squares = []
for i in range(5):
    squares.append(i ** 2)
print(squares)  # Output: [0, 1, 4, 9, 16]

# Or with list comprehension
squares = [i ** 2 for i in range(5)]
print(squares)  # Output: [0, 1, 4, 9, 16]
```

### Ruby Equivalent

```ruby
# Repeat action N times
5.times { puts "Hello!" }

# Output:
# Hello!
# Hello!
# Hello!
# Hello!
# Hello!

# With index (block parameter)
5.times do |i|
  puts "Iteration #{i}"
end

# Output:
# Iteration 0
# Iteration 1
# Iteration 2
# Iteration 3
# Iteration 4

# Practical example: game loop
3.times do |round|
  puts "Round #{round + 1}"
  # game logic here
end

# Building an array
squares = []
5.times do |i|
  squares << i ** 2
end
puts squares.inspect  # Output: [0, 1, 4, 9, 16]

# Or with .map (more idiomatic)
squares = 5.times.map { |i| i ** 2 }
puts squares.inspect  # Output: [0, 1, 4, 9, 16]

# If you don't need the index, ignore it
3.times { puts "Knock knock!" }

# Or explicitly use _ to show you're ignoring it
3.times do |_|
  puts "Repeat"
end
```

### Explanation

The `.times` method is called on an integer and executes the block that many times:

```ruby
n.times do |i|
  # code here
  # i goes from 0 to n-1
end
```

**When to use `.times`:**
- When you need to repeat something N times
- When you're starting from 0
- When the iteration count is more important than what you're iterating over

**Block parameter (optional):**
```ruby
# Without parameter - just repeat
10.times { print "." }

# With parameter - use the index
10.times { |i| puts i }
```

**Comparison with range:**
```ruby
# Using .times
5.times { |i| puts i }

# Using range and .each (more verbose)
(0...5).each { |i| puts i }

# They're equivalent, but .times is clearer when starting from 0
```

**Chaining `.times` with other methods:**
```ruby
# Create an array of random numbers
random_numbers = 10.times.map { rand(1..100) }

# Create array of indices
indices = 5.times.to_a  # [0, 1, 2, 3, 4]

# Sum of squares
sum = 10.times.reduce(0) { |sum, i| sum + i ** 2 }
```

**Practical examples:**
```ruby
# Retry logic
3.times do
  break if connect_to_server
  puts "Retrying..."
  sleep 1
end

# Simple animation
10.times do |i|
  print "\rLoading #{'.' * (i + 1)}"
  sleep 0.2
end
puts

# Generate test data
users = 5.times.map do |i|
  { id: i, name: "User#{i}", email: "user#{i}@example.com" }
end
```

---

## 4.5 `.each` vs `for...in`

The `.each` method is the heart of Ruby iteration. While Python uses `for item in collection`, Ruby uses `collection.each do |item|`. This is one of the most fundamental patterns you'll use in Ruby.

**Key Differences:**
- Python: `for item in collection:`
- Ruby: `collection.each do |item|`
- Ruby's `.each` uses blocks (covered in detail in Chapter 8)
- Ruby's `.each` returns the original collection
- `.each` creates a new scope for variables

### Python Example

```python
# Iterating over a list
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)

# Output:
# apple
# banana
# cherry

# Iterating over a string
for char in "Ruby":
    print(char)

# Output:
# R
# u
# b
# y

# Iterating over a dictionary
scores = {"Alice": 95, "Bob": 87, "Charlie": 92}
for name, score in scores.items():
    print(f"{name}: {score}")

# Output:
# Alice: 95
# Bob: 87
# Charlie: 92

# With enumerate for index
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")

# Output:
# 0: apple
# 1: banana
# 2: cherry

# For loop always executes; returns nothing
result = []
for i in range(5):
    result.append(i * 2)
print(result)  # Output: [0, 2, 4, 6, 8]
```

### Ruby Equivalent

```ruby
# Iterating over an array
fruits = ["apple", "banana", "cherry"]
fruits.each do |fruit|
  puts fruit
end

# Output:
# apple
# banana
# cherry

# Single-line syntax for simple operations
fruits.each { |fruit| puts fruit }

# Iterating over a string (must convert to array of chars)
"Ruby".each_char do |char|
  puts char
end

# Output:
# R
# u
# b
# y

# Iterating over a hash
scores = { "Alice" => 95, "Bob" => 87, "Charlie" => 92 }
scores.each do |name, score|
  puts "#{name}: #{score}"
end

# Output:
# Alice: 95
# Bob: 87
# Charlie: 92

# With index using each_with_index
fruits.each_with_index do |fruit, index|
  puts "#{index}: #{fruit}"
end

# Output:
# 0: apple
# 1: banana
# 2: cherry

# .each returns the original collection
result = fruits.each do |fruit|
  puts fruit.upcase
end

puts result.inspect  # Output: ["apple", "banana", "cherry"]
# The array itself is returned, not the block results!

# To collect results, use .map instead (covered in Chapter 6)
doubled = (0..4).map { |i| i * 2 }
puts doubled.inspect  # Output: [0, 2, 4, 6, 8]
```

### Explanation

**Basic `.each` syntax:**
```ruby
collection.each do |element|
  # do something with element
end
```

**Block parameter naming:**
The parameter name between `|pipes|` can be anything, but use descriptive names:
```ruby
users.each do |user|
  puts user.name
end

numbers.each do |num|
  puts num * 2
end

["a", "b", "c"].each do |letter|
  puts letter.upcase
end
```

**Single-line vs multi-line blocks:**
```ruby
# Single-line: use { }
numbers.each { |n| puts n }

# Multi-line: use do...end
numbers.each do |n|
  squared = n ** 2
  puts "#{n} squared is #{squared}"
end
```

**Convention:** Use `{}` for one-liners, `do...end` for multiple lines.

**Important: `.each` returns the original collection**
```ruby
numbers = [1, 2, 3]

result = numbers.each do |n|
  n * 2  # This value is ignored!
end

puts result.inspect  # Output: [1, 2, 3] (original array)

# If you want to transform and collect results, use .map
result = numbers.map do |n|
  n * 2
end

puts result.inspect  # Output: [2, 4, 6]
```

**Multiple block parameters:**

For hashes or nested arrays:
```ruby
# Hash with two parameters
person = { name: "Alice", age: 30, city: "NYC" }
person.each do |key, value|
  puts "#{key}: #{value}"
end

# Array of arrays
pairs = [[1, 2], [3, 4], [5, 6]]
pairs.each do |first, second|
  puts "#{first} + #{second} = #{first + second}"
end

# Output:
# 1 + 2 = 3
# 3 + 4 = 7
# 5 + 6 = 11
```

**Common `.each` variants:**

```ruby
# .each_with_index - provides index
fruits.each_with_index do |fruit, i|
  puts "#{i + 1}. #{fruit}"
end

# .each_char - for strings
"Hello".each_char { |c| puts c }

# .each_line - for multiline strings
text = "Line 1\nLine 2\nLine 3"
text.each_line { |line| puts line }

# .each_key and .each_value - for hashes
scores.each_key { |name| puts name }
scores.each_value { |score| puts score }
```

**Practical examples:**
```ruby
# Process files
Dir.glob("*.txt").each do |filename|
  content = File.read(filename)
  puts "#{filename}: #{content.length} bytes"
end

# Validate data
errors = []
users.each do |user|
  errors << "Missing email for #{user.name}" if user.email.nil?
end

# Build HTML
items = ["Apple", "Banana", "Cherry"]
html = "<ul>\n"
items.each do |item|
  html += "  <li>#{item}</li>\n"
end
html += "</ul>"
```

---

## 4.6 Ranges (`1..10` vs `range(1, 11)`)

Ranges are a core feature in both Ruby and Python, but Ruby's syntax is more elegant and ranges are first-class objects with powerful methods.

**Key Similarities:**
- Both represent sequences of values
- Both are memory-efficient (lazy evaluation)
- Both work with iterations

**Key Differences:**
- Ruby: `1..10` (inclusive) or `1...10` (exclusive)
- Python: `range(1, 11)` (always exclusive of end)
- Ruby ranges are objects with methods
- Ruby ranges work with letters and other comparable types

### Python Example

```python
# Python range (exclusive of end)
for i in range(5):
    print(i, end=" ")
# Output: 0 1 2 3 4

# With start and stop
for i in range(1, 6):
    print(i, end=" ")
print()
# Output: 1 2 3 4 5

# With step
for i in range(0, 10, 2):
    print(i, end=" ")
print()
# Output: 0 2 4 6 8

# Negative step (countdown)
for i in range(10, 0, -1):
    print(i, end=" ")
print()
# Output: 10 9 8 7 6 5 4 3 2 1

# Convert to list
numbers = list(range(1, 6))
print(numbers)  # Output: [1, 2, 3, 4, 5]

# Check membership
if 5 in range(1, 10):
    print("5 is in range")

# Get length
print(len(range(1, 11)))  # Output: 10

# Ranges only work with integers in Python
# letters = range('a', 'z')  # This doesn't work
```

### Ruby Equivalent

```ruby
# Ruby inclusive range (two dots)
(0..4).each { |i| print "#{i} " }
puts
# Output: 0 1 2 3 4

# Ruby exclusive range (three dots)
(0...5).each { |i| print "#{i} " }
puts
# Output: 0 1 2 3 4

# With start and stop (inclusive)
(1..5).each { |i| print "#{i} " }
puts
# Output: 1 2 3 4 5

# With start and stop (exclusive)
(1...6).each { |i| print "#{i} " }
puts
# Output: 1 2 3 4 5

# Step (covered in section 4.7, but here's a preview)
(0..10).step(2) { |i| print "#{i} " }
puts
# Output: 0 2 4 6 8 10

# Reverse range with downto (covered in 4.7)
10.downto(1) { |i| print "#{i} " }
puts
# Output: 10 9 8 7 6 5 4 3 2 1

# Convert to array
numbers = (1..5).to_a
puts numbers.inspect  # Output: [1, 2, 3, 4, 5]

# Check membership (using include? or cover?)
if (1..10).include?(5)
  puts "5 is in range"
end

# Get size
puts (1..10).size  # Output: 10

# Ranges work with letters!
('a'..'e').each { |letter| print "#{letter} " }
puts
# Output: a b c d e

# Ranges work with strings
('aa'..'ad').each { |s| print "#{s} " }
puts
# Output: aa ab ac ad
```

### Explanation

**Two types of ranges:**

1. **Inclusive range (`..`)** - includes both endpoints:
```ruby
(1..5).to_a  # [1, 2, 3, 4, 5]
```

2. **Exclusive range (`...`)** - excludes the end:
```ruby
(1...5).to_a  # [1, 2, 3, 4]
```

**Mnemonic:** More dots = more exclusive (excludes the end).

**Common use cases:**

```ruby
# Array slicing
fruits = ["apple", "banana", "cherry", "date", "elderberry"]
puts fruits[1..3].inspect  # ["banana", "cherry", "date"]
puts fruits[1...3].inspect # ["banana", "cherry"]

# Checking if value is in range
age = 25
if (18..65).include?(age)
  puts "Working age"
end

# Conditional logic
score = 85
grade = case score
when 90..100 then "A"
when 80..89  then "B"
when 70..79  then "C"
when 60..69  then "D"
else "F"
end
puts grade  # Output: B
```

**Ranges with non-integers:**

```ruby
# Character ranges
('A'..'Z').each { |letter| print letter }
puts
# Output: ABCDEFGHIJKLMNOPQRSTUVWXYZ

# String ranges (be careful - can be surprising!)
('a'..'abc').to_a
# Output: ["a", "b", "c", "d", ..., "abc"]
# This generates all strings in lexicographic order!

# Date ranges (requires 'date' library)
require 'date'
start_date = Date.new(2024, 1, 1)
end_date = Date.new(2024, 1, 5)
(start_date..end_date).each do |date|
  puts date
end
```

**`include?` vs `cover?`:**

```ruby
# include? - checks each element (slow for large ranges)
(1..1000000).include?(500000)  # Iterates through elements

# cover? - checks if value is between bounds (fast)
(1..1000000).cover?(500000)    # Just checks: 1 <= 500000 <= 1000000

# They differ for non-integer ranges
('a'..'z').cover?('cc')   # true (lexicographically between 'a' and 'z')
('a'..'z').include?('cc') # false ('cc' is not in the sequence)
```

**Best practice:** Use `.cover?` for numeric ranges, `.include?` when you need exact membership.

**Infinite ranges (Ruby 2.6+):**
```ruby
# Endless range
(1..).each do |i|
  puts i
  break if i >= 5
end
# Output: 1 2 3 4 5

# Beginless range
numbers = [1, 5, 10, 15, 20]
puts numbers.select { |n| n < 12 }
# Same as:
puts numbers.select { |n| (..12).cover?(n) }
```

**Common gotcha - parentheses required:**
```ruby
# Without parentheses - syntax error
1..5.each { |i| puts i }  # Error!

# With parentheses - works
(1..5).each { |i| puts i }

# Or use a variable
range = 1..5
range.each { |i| puts i }
```

---

## 4.7 `.upto`, `.downto`, and `.step`

Ruby provides elegant methods for numeric iteration: `.upto`, `.downto`, and `.step`. These are more readable alternatives to ranges with custom increments.

**Key Concept:**
- Python uses `range(start, stop, step)` for all cases
- Ruby has specialized methods that read like English
- More expressive and intention-revealing code

### Python Example

```python
# Counting up
for i in range(1, 6):
    print(i, end=" ")
print()
# Output: 1 2 3 4 5

# Counting down
for i in range(5, 0, -1):
    print(i, end=" ")
print()
# Output: 5 4 3 2 1

# Stepping by 2
for i in range(0, 11, 2):
    print(i, end=" ")
print()
# Output: 0 2 4 6 8 10

# Stepping by 3
for i in range(0, 20, 3):
    print(i, end=" ")
print()
# Output: 0 3 6 9 12 15 18

# Countdown by 2
for i in range(10, 0, -2):
    print(i, end=" ")
print()
# Output: 10 8 6 4 2

# Floating-point stepping (need numpy or manual)
i = 0.0
while i <= 2.0:
    print(round(i, 1), end=" ")
    i += 0.5
print()
# Output: 0.0 0.5 1.0 1.5 2.0
```

### Ruby Equivalent

```ruby
# Counting up with .upto
1.upto(5) { |i| print "#{i} " }
puts
# Output: 1 2 3 4 5

# Counting down with .downto
5.downto(1) { |i| print "#{i} " }
puts
# Output: 5 4 3 2 1

# Stepping by 2 with .step
0.step(10, 2) { |i| print "#{i} " }
puts
# Output: 0 2 4 6 8 10

# Or using range with step
(0..10).step(2) { |i| print "#{i} " }
puts
# Output: 0 2 4 6 8 10

# Stepping by 3
0.step(20, 3) { |i| print "#{i} " }
puts
# Output: 0 3 6 9 12 15 18

# Countdown by 2
10.step(0, -2) { |i| print "#{i} " }
puts
# Output: 10 8 6 4 2 0

# Floating-point stepping
0.0.step(2.0, 0.5) { |i| print "#{i} " }
puts
# Output: 0.0 0.5 1.0 1.5 2.0

# Works with 'by' keyword (Ruby 2.6+)
(0..10).step(by: 2) { |i| print "#{i} " }
puts
# Output: 0 2 4 6 8 10
```

### Explanation

**`.upto(limit)`:**
Counts from the number up to (and including) the limit:
```ruby
1.upto(5) do |i|
  puts "Count: #{i}"
end

# Equivalent to:
(1..5).each { |i| puts "Count: #{i}" }

# But .upto is more expressive when counting up
```

**`.downto(limit)`:**
Counts from the number down to (and including) the limit:
```ruby
5.downto(1) do |i|
  puts "Countdown: #{i}"
end

# More readable than:
(1..5).reverse_each { |i| puts "Countdown: #{i}" }
```

**`.step(limit, step_size)`:**
Steps from the number to the limit by the specified increment:
```ruby
# Syntax: number.step(limit, step_size)
0.step(10, 2) { |i| puts i }  # 0, 2, 4, 6, 8, 10

# Works with negative steps
10.step(0, -2) { |i| puts i }  # 10, 8, 6, 4, 2, 0

# Works with floats
1.0.step(2.0, 0.25) { |i| puts i }  # 1.0, 1.25, 1.5, 1.75, 2.0
```

**Range with `.step`:**
```ruby
# Alternative syntax using ranges
(0..10).step(3) { |i| puts i }  # 0, 3, 6, 9

# Exclusive range
(0...10).step(3) { |i| puts i }  # 0, 3, 6, 9
```

**Practical examples:**

```ruby
# Print every 10th line number
1.upto(100) do |line_num|
  puts "Line #{line_num}" if line_num % 10 == 0
end

# Better - use step
0.step(100, 10) do |line_num|
  puts "Line #{line_num}"
end

# Countdown timer
10.downto(1) do |seconds|
  puts "#{seconds}..."
  sleep 1
end
puts "Blast off!"

# Generate test data with increments
prices = []
10.0.step(100.0, 10.0) do |price|
  prices << price
end
puts prices.inspect
# Output: [10.0, 20.0, 30.0, 40.0, 50.0, 60.0, 70.0, 80.0, 90.0, 100.0]

# Process every Nth item
users.each_with_index do |user, i|
  next unless (i + 1) % 5 == 0  # Every 5th user
  send_survey_email(user)
end

# Better - using step with indices
(0...users.length).step(5) do |i|
  send_survey_email(users[i])
end
```

**Chaining without blocks:**

These methods return enumerators when called without a block:
```ruby
# Create an enumerator
countdown = 10.downto(1)

# Use it later
countdown.each { |i| puts i }

# Or chain with other methods
odd_numbers = 1.upto(10).select(&:odd?)
puts odd_numbers.inspect  # [1, 3, 5, 7, 9]

# Create array of multiples
multiples_of_5 = 0.step(50, 5).to_a
puts multiples_of_5.inspect  # [0, 5, 10, 15, 20, 25, 30, 35, 40, 45, 50]
```

**Which method to use?**

```ruby
# Use .upto when counting up from a specific number
1.upto(10) { |i| puts i }

# Use .downto when counting down
10.downto(1) { |i| puts i }

# Use .step when you need custom increments
0.step(100, 5) { |i| puts i }

# Use .times when starting from 0 with increment of 1
10.times { |i| puts i }

# Use ranges when the increment is 1
(1..10).each { |i| puts i }
```

**Performance note:**
All these methods are similarly efficient. Choose based on readability and expressiveness.

---

## Chapter 4 Summary

You've now learned Ruby's iteration features:

- **while/until loops:** Traditional loops with Ruby's `until` keyword for negative conditions
- **for loops:** Exist in Ruby but rarely used; `.each` is preferred
- **Loop control:** `break`, `next`, and Ruby's unique `redo` keyword
- **.times:** Elegant way to repeat N times: `5.times { ... }`
- **.each:** The fundamental Ruby iterator, replacing most `for` loops
- **Ranges:** `1..10` (inclusive) or `1...10` (exclusive), work with more than just numbers
- **.upto/.downto/.step:** Readable methods for numeric iteration

**Key takeaways for Python developers:**

1. **Prefer iterators over loops:** Use `.each`, `.times`, `.map` instead of `for` loops
2. **Ranges are inclusive by default:** `1..5` includes 5, unlike Python's `range(1, 6)`
3. **Blocks are central:** Get comfortable with the `do |param| ... end` syntax
4. **Ruby is more expressive:** `10.times`, `5.upto(10)`, `10.downto(1)` read like English
5. **`.each` returns the original:** Use `.map` to transform and collect (covered in Chapter 6)

In the next chapter, we'll dive into Ruby's collection types: **Arrays** and **Hashes**, and explore the unique concept of **Symbols**.
