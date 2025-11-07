# Chapter 5: Collections

Collections are fundamental to any programming language. Ruby's Arrays and Hashes will feel familiar to Python developers who know lists and dictionaries, but Ruby adds some elegant syntax and powerful methods. This chapter also introduces **Symbols**, a uniquely Ruby concept that has no direct Python equivalent.

---

## 5.1 Arrays vs Python Lists

Ruby's Arrays are similar to Python lists: ordered, mutable collections that can hold any type of object. The syntax is nearly identical, but Ruby provides some additional conveniences and a rich set of methods.

### 5.1.1 Creating Arrays

**Key Similarities:**
- Both use square brackets `[]` syntax
- Both are zero-indexed
- Both can hold mixed types
- Both are dynamically sized

**Key Differences:**
- Ruby has `%w` and `%i` literals for creating arrays quickly
- Ruby's `Array.new` constructor offers more options
- Ruby arrays have no list comprehension syntax (use `.map` instead)

#### Python Example

```python
# Empty array
empty = []

# Array with values
fruits = ["apple", "banana", "cherry"]

# Mixed types
mixed = [1, "two", 3.0, True, None]

# Using list constructor
numbers = list(range(5))  # [0, 1, 2, 3, 4]

# List comprehension
squares = [i ** 2 for i in range(5)]  # [0, 1, 4, 9, 16]

# Nested arrays
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]

# Repeating elements
zeros = [0] * 5  # [0, 0, 0, 0, 0]

# Creating from string
chars = list("hello")  # ['h', 'e', 'l', 'l', 'o']
```

#### Ruby Equivalent

```ruby
# Empty array
empty = []

# Array with values
fruits = ["apple", "banana", "cherry"]

# Mixed types
mixed = [1, "two", 3.0, true, nil]

# Using Array constructor
numbers = Array(0..4)  # [0, 1, 2, 3, 4]
# Or
numbers = (0..4).to_a  # [0, 1, 2, 3, 4]

# Using .map (like list comprehension)
squares = (0..4).map { |i| i ** 2 }  # [0, 1, 4, 9, 16]

# Nested arrays
matrix = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9]
]

# Repeating elements
zeros = Array.new(5, 0)  # [0, 0, 0, 0, 0]

# Creating from string
chars = "hello".chars  # ["h", "e", "l", "l", "o"]

# %w literal - creates array of strings (whitespace-separated)
fruits = %w[apple banana cherry]  # ["apple", "banana", "cherry"]
# No quotes needed!

# %i literal - creates array of symbols
statuses = %i[pending approved rejected]  # [:pending, :approved, :rejected]

# Array.new with block
squares = Array.new(5) { |i| i ** 2 }  # [0, 1, 4, 9, 16]

# Creating array of arrays
grid = Array.new(3) { Array.new(3, 0) }
# [[0, 0, 0], [0, 0, 0], [0, 0, 0]]
```

#### Explanation

**The `%w` literal:**

The `%w` notation creates an array of strings separated by whitespace:
```ruby
# Instead of:
colors = ["red", "green", "blue"]

# You can write:
colors = %w[red green blue]

# Works with various delimiters
colors = %w(red green blue)
colors = %w{red green blue}
colors = %w|red green blue|
```

**Important:** `%w` doesn't interpolate or allow spaces in strings:
```ruby
name = "Alice"
arr = %w[hello #{name} world]  # ["hello", "\#{name}", "world"]
# The interpolation doesn't work!

# For interpolation, use %W (capital W)
arr = %W[hello #{name} world]  # ["hello", "Alice", "world"]
```

**Array.new variations:**
```ruby
# With size only (filled with nil)
Array.new(3)  # [nil, nil, nil]

# With size and default value
Array.new(3, "hello")  # ["hello", "hello", "hello"]

# WARNING - default value is the SAME object
arr = Array.new(3, [])
arr[0] << 1
puts arr.inspect  # [[1], [1], [1]] - all three reference the same array!

# Use a block to create distinct objects
arr = Array.new(3) { [] }
arr[0] << 1
puts arr.inspect  # [[1], [], []] - each is a separate array

# Block receives the index
arr = Array.new(5) { |i| i * 10 }  # [0, 10, 20, 30, 40]
```

**Converting to arrays:**
```ruby
# Range to array
(1..5).to_a  # [1, 2, 3, 4, 5]

# String to array of characters
"hello".chars  # ["h", "e", "l", "l", "o"]
# Or
"hello".split("")  # ["h", "e", "l", "l", "o"]

# String to array of words
"hello world".split  # ["hello", "world"]

# Hash to array of pairs
{ a: 1, b: 2 }.to_a  # [[:a, 1], [:b, 2]]
```

---

### 5.1.2 Accessing Elements

**Key Similarities:**
- Both use zero-based indexing with `[]`
- Both support negative indices
- Both allow assignment with `[]=`

**Key Differences:**
- Ruby returns `nil` for out-of-bounds access (Python raises IndexError)
- Ruby's `.fetch` method for safer access with defaults
- Ruby's `.first` and `.last` methods
- Ruby's `.at()` method as an alternative to `[]`

#### Python Example

```python
fruits = ["apple", "banana", "cherry", "date"]

# Accessing by index
print(fruits[0])   # apple
print(fruits[2])   # cherry

# Negative indexing
print(fruits[-1])  # date (last element)
print(fruits[-2])  # cherry (second to last)

# Out of bounds raises error
try:
    print(fruits[10])
except IndexError:
    print("Index out of range")

# Assignment
fruits[1] = "blueberry"
print(fruits)  # ['apple', 'blueberry', 'cherry', 'date']

# Get with default (using a helper)
def get_or_default(lst, index, default=None):
    try:
        return lst[index]
    except IndexError:
        return default

print(get_or_default(fruits, 10, "N/A"))  # N/A

# First and last elements
first = fruits[0]
last = fruits[-1]
```

#### Ruby Equivalent

```ruby
fruits = ["apple", "banana", "cherry", "date"]

# Accessing by index
puts fruits[0]   # apple
puts fruits[2]   # cherry

# Negative indexing
puts fruits[-1]  # date (last element)
puts fruits[-2]  # cherry (second to last)

# Out of bounds returns nil (no error!)
puts fruits[10]  # nil
puts fruits[10].inspect  # "nil"

# Assignment
fruits[1] = "blueberry"
puts fruits.inspect  # ["apple", "blueberry", "cherry", "date"]

# .fetch for safer access (raises error if missing)
begin
  puts fruits.fetch(10)
rescue IndexError => e
  puts "Index out of range"
end

# .fetch with default value
puts fruits.fetch(10, "N/A")  # N/A

# .fetch with block
puts fruits.fetch(10) { |i| "Index #{i} not found" }
# Output: Index 10 not found

# First and last elements (Ruby provides methods)
puts fruits.first  # apple
puts fruits.last   # date

# First/last N elements
puts fruits.first(2).inspect  # ["apple", "blueberry"]
puts fruits.last(2).inspect   # ["cherry", "date"]

# .at() method (alternative to [])
puts fruits.at(0)   # apple
puts fruits.at(-1)  # date
puts fruits.at(10)  # nil
```

#### Explanation

**Nil vs Error for out-of-bounds:**

This is a major difference from Python:
```ruby
arr = [1, 2, 3]

# Python would raise IndexError
# Ruby returns nil
puts arr[10]  # nil

# This can lead to subtle bugs
value = arr[10]
if value
  puts "Found: #{value}"
else
  puts "Not found"  # This will execute
end
```

**Use `.fetch` when you want an error:**
```ruby
arr = [1, 2, 3]

# Raises IndexError if index doesn't exist
arr.fetch(10)  # Error!

# Or provide a default
arr.fetch(10, 0)  # 0

# Or use a block for dynamic defaults
arr.fetch(10) { 0 }
```

**`.first` and `.last` are very convenient:**
```ruby
numbers = [1, 2, 3, 4, 5]

numbers.first     # 1
numbers.last      # 5

numbers.first(3)  # [1, 2, 3]
numbers.last(2)   # [4, 5]

# On empty arrays
[].first  # nil
[].last   # nil
```

**Multiple assignment:**
```ruby
# Assign to multiple indices
arr = [1, 2, 3, 4, 5]
arr[0], arr[2], arr[4] = 10, 20, 30
puts arr.inspect  # [10, 2, 20, 4, 30]

# Assign beyond array length - fills with nil
arr = [1, 2, 3]
arr[5] = 99
puts arr.inspect  # [1, 2, 3, nil, nil, 99]
```

---

### 5.1.3 Common Array Methods

Ruby arrays come with a rich set of built-in methods. Many will feel familiar to Python developers, while others offer new capabilities.

**Key Similarities:**
- Both support append, insert, remove operations
- Both can be sorted, reversed, etc.
- Both support checking for element membership

**Key Differences:**
- Ruby uses `<<` for append (in addition to `.push`)
- Ruby has both `!` (mutating) and non-mutating versions of many methods
- Ruby's method names are often more expressive

#### Python Example

```python
# Starting array
fruits = ["apple", "banana"]

# Append (add to end)
fruits.append("cherry")
print(fruits)  # ['apple', 'banana', 'cherry']

# Extend (add multiple)
fruits.extend(["date", "elderberry"])
print(fruits)  # ['apple', 'banana', 'cherry', 'date', 'elderberry']

# Insert at position
fruits.insert(1, "blueberry")
print(fruits)  # ['apple', 'blueberry', 'banana', 'cherry', 'date', 'elderberry']

# Remove by value
fruits.remove("banana")
print(fruits)  # ['apple', 'blueberry', 'cherry', 'date', 'elderberry']

# Pop (remove and return last)
last = fruits.pop()
print(last)    # elderberry
print(fruits)  # ['apple', 'blueberry', 'cherry', 'date']

# Pop at index
item = fruits.pop(1)
print(item)    # blueberry
print(fruits)  # ['apple', 'cherry', 'date']

# Check membership
print("cherry" in fruits)      # True
print("banana" in fruits)      # False

# Get index of element
print(fruits.index("cherry"))  # 1

# Count occurrences
numbers = [1, 2, 3, 2, 1, 2]
print(numbers.count(2))  # 3

# Length
print(len(fruits))  # 3

# Sort (in-place)
numbers = [3, 1, 4, 1, 5]
numbers.sort()
print(numbers)  # [1, 1, 3, 4, 5]

# Sorted (returns new list)
numbers = [3, 1, 4, 1, 5]
sorted_nums = sorted(numbers)
print(sorted_nums)  # [1, 1, 3, 4, 5]
print(numbers)      # [3, 1, 4, 1, 5] - unchanged

# Reverse (in-place)
numbers.reverse()
print(numbers)  # [5, 1, 4, 1, 3]

# Clear
fruits.clear()
print(fruits)  # []

# Join
words = ["hello", "world"]
print(" ".join(words))  # hello world
```

#### Ruby Equivalent

```ruby
# Starting array
fruits = ["apple", "banana"]

# Append (multiple ways)
fruits.push("cherry")
# Or use << (the "shovel operator")
fruits << "date"
puts fruits.inspect  # ["apple", "banana", "cherry", "date"]

# Append multiple
fruits.push("elderberry", "fig")
# Or
fruits += ["grape", "honeydew"]
puts fruits.inspect

# << can be chained
fruits << "kiwi" << "lemon"

# Insert at position
fruits.insert(1, "blueberry")
puts fruits.inspect

# Remove by value
fruits.delete("banana")
puts fruits.inspect

# Pop (remove and return last)
last = fruits.pop
puts last           # lemon
puts fruits.inspect

# Shift (remove and return first)
first = fruits.shift
puts first          # apple
puts fruits.inspect

# Unshift (add to beginning)
fruits.unshift("apple")
puts fruits.inspect

# Check membership
puts fruits.include?("cherry")  # true
puts fruits.include?("banana")  # false

# Get index of element
puts fruits.index("cherry")     # (depends on current state)

# Find index with condition
numbers = [1, 2, 3, 2, 1, 2]
puts numbers.index { |n| n > 2 }  # 2 (index of first element > 2)

# Count occurrences
puts numbers.count(2)  # 3

# Count with block
puts numbers.count { |n| n > 1 }  # 4

# Length (multiple methods)
puts fruits.length  # Most common
puts fruits.size    # Same as length
puts fruits.count   # Same as length (without argument)

# Sort (returns new array)
numbers = [3, 1, 4, 1, 5]
sorted = numbers.sort
puts sorted.inspect  # [1, 1, 3, 4, 5]
puts numbers.inspect # [3, 1, 4, 1, 5] - unchanged

# Sort! (mutating version - modifies in place)
numbers.sort!
puts numbers.inspect # [1, 1, 3, 4, 5]

# Reverse (returns new array)
reversed = numbers.reverse
puts reversed.inspect  # [5, 4, 3, 1, 1]

# Reverse! (mutating)
numbers.reverse!
puts numbers.inspect  # [5, 4, 3, 1, 1]

# Clear
fruits.clear
puts fruits.inspect  # []

# Join
words = ["hello", "world"]
puts words.join(" ")  # hello world
puts words.join       # helloworld (default: no separator)
puts words.join(", ") # hello, world
```

#### Explanation

**The shovel operator `<<`:**

Ruby's `<<` is the idiomatic way to append to an array:
```ruby
arr = [1, 2, 3]
arr << 4
arr << 5 << 6  # Chainable!
puts arr.inspect  # [1, 2, 3, 4, 5, 6]

# Equivalent to:
arr.push(4)
arr.push(5).push(6)
```

**Mutating vs non-mutating methods:**

Ruby convention: methods ending in `!` modify the object in place:
```ruby
arr = [3, 1, 2]

# Non-mutating (returns new array)
sorted = arr.sort
puts sorted.inspect  # [1, 2, 3]
puts arr.inspect     # [3, 1, 2] - original unchanged

# Mutating (modifies in place)
arr.sort!
puts arr.inspect     # [1, 2, 3] - original modified
```

Common pairs:
- `sort` / `sort!`
- `reverse` / `reverse!`
- `uniq` / `uniq!`
- `compact` / `compact!`
- `map` / `map!`

**Important array methods:**

```ruby
arr = [1, 2, 3, 4, 5]

# Take first N elements (non-mutating)
arr.take(3)  # [1, 2, 3]

# Drop first N elements (non-mutating)
arr.drop(2)  # [3, 4, 5]

# Remove duplicates
[1, 2, 2, 3, 3, 3].uniq  # [1, 2, 3]

# Remove nil values
[1, nil, 2, nil, 3].compact  # [1, 2, 3]

# Flatten nested arrays
[1, [2, [3, 4]]].flatten  # [1, 2, 3, 4]

# Sample random element(s)
arr.sample     # Random element
arr.sample(2)  # Array of 2 random elements

# Shuffle
arr.shuffle  # Randomly ordered array

# Min/max
arr.min  # 1
arr.max  # 5

# Sum (Ruby 2.4+)
arr.sum  # 15

# Rotate
[1, 2, 3, 4].rotate     # [2, 3, 4, 1]
[1, 2, 3, 4].rotate(2)  # [3, 4, 1, 2]
[1, 2, 3, 4].rotate(-1) # [4, 1, 2, 3]
```

**Combining arrays:**
```ruby
a = [1, 2, 3]
b = [4, 5, 6]

# Concatenation
a + b        # [1, 2, 3, 4, 5, 6]
a.concat(b)  # Modifies a in place

# Union (removes duplicates)
[1, 2, 3] | [2, 3, 4]  # [1, 2, 3, 4]

# Intersection
[1, 2, 3] & [2, 3, 4]  # [2, 3]

# Difference
[1, 2, 3] - [2, 3, 4]  # [1]
```

---

### 5.1.4 Array Slicing and Ranges

Both Ruby and Python support array slicing, but with different syntax. Ruby uses ranges and can also use the bracket notation with start and length.

**Key Similarities:**
- Both support extracting subarrays
- Both support negative indices
- Both return new arrays (non-mutating by default)

**Key Differences:**
- Ruby uses ranges: `arr[1..3]` vs Python's `arr[1:4]`
- Ruby also supports `arr[start, length]` notation
- Ruby's `slice` method as an alternative to `[]`

#### Python Example

```python
fruits = ["apple", "banana", "cherry", "date", "elderberry"]

# Slicing with [start:stop:step]
print(fruits[1:3])    # ['banana', 'cherry']
print(fruits[0:4])    # ['apple', 'banana', 'cherry', 'date']
print(fruits[:3])     # ['apple', 'banana', 'cherry']
print(fruits[2:])     # ['cherry', 'date', 'elderberry']
print(fruits[:])      # All elements (copy)

# With step
print(fruits[::2])    # ['apple', 'cherry', 'elderberry']
print(fruits[1::2])   # ['banana', 'date']

# Negative indices
print(fruits[-2:])    # ['date', 'elderberry']
print(fruits[:-2])    # ['apple', 'banana', 'cherry']

# Reverse
print(fruits[::-1])   # ['elderberry', 'date', 'cherry', 'banana', 'apple']

# Slice assignment
fruits[1:3] = ["blueberry", "coconut"]
print(fruits)  # ['apple', 'blueberry', 'coconut', 'date', 'elderberry']

# Delete slice
del fruits[1:3]
print(fruits)  # ['apple', 'date', 'elderberry']
```

#### Ruby Equivalent

```ruby
fruits = ["apple", "banana", "cherry", "date", "elderberry"]

# Slicing with ranges
puts fruits[1..3].inspect    # ["banana", "cherry", "date"]
puts fruits[0..3].inspect    # ["apple", "banana", "cherry", "date"]
puts fruits[0..2].inspect    # ["apple", "banana", "cherry"]
puts fruits[2..-1].inspect   # ["cherry", "date", "elderberry"]
puts fruits[0..-1].inspect   # All elements (copy)

# Exclusive range (three dots)
puts fruits[1...3].inspect   # ["banana", "cherry"]

# Slicing with [start, length]
puts fruits[1, 2].inspect    # ["banana", "cherry"] - start at 1, take 2
puts fruits[0, 3].inspect    # ["apple", "banana", "cherry"]
puts fruits[2, 3].inspect    # ["cherry", "date", "elderberry"]

# Using .slice (same as [])
puts fruits.slice(1..3).inspect
puts fruits.slice(1, 2).inspect

# Negative indices
puts fruits[-2..-1].inspect  # ["date", "elderberry"]
puts fruits[0..-3].inspect   # ["apple", "banana", "cherry"]

# Every Nth element (use select with index)
every_other = fruits.select.with_index { |_, i| i.even? }
puts every_other.inspect  # ["apple", "cherry", "elderberry"]

# Or use each_slice
puts fruits.each_slice(2).map(&:first).inspect
# ["apple", "cherry", "elderberry"]

# Or step through indices
result = (0...fruits.length).step(2).map { |i| fruits[i] }
puts result.inspect  # ["apple", "cherry", "elderberry"]

# Reverse
puts fruits.reverse.inspect
# ["elderberry", "date", "cherry", "banana", "apple"]

# Slice assignment
fruits[1..2] = ["blueberry", "coconut"]
puts fruits.inspect
# ["apple", "blueberry", "coconut", "date", "elderberry"]

# Can also use [start, length] for assignment
fruits[1, 2] = ["banana"]
puts fruits.inspect
# ["apple", "banana", "date", "elderberry"]

# Delete slice
fruits.slice!(1..2)  # Returns deleted elements, modifies array
puts fruits.inspect
```

#### Explanation

**Range slicing:**
```ruby
arr = ["a", "b", "c", "d", "e"]

# Inclusive range (two dots)
arr[1..3]   # ["b", "c", "d"] - indices 1, 2, 3

# Exclusive range (three dots)
arr[1...3]  # ["b", "c"] - indices 1, 2 (excludes 3)

# To end
arr[2..-1]  # ["c", "d", "e"]

# From beginning
arr[0..2]   # ["a", "b", "c"]
```

**Start and length:**
```ruby
arr = ["a", "b", "c", "d", "e"]

# [start_index, number_of_elements]
arr[1, 2]   # ["b", "c"] - start at index 1, take 2 elements
arr[0, 3]   # ["a", "b", "c"]
arr[2, 1]   # ["c"]
arr[2, 100] # ["c", "d", "e"] - takes what's available
```

**Which to use?**
- Use ranges when thinking about start and end positions
- Use start/length when thinking about "take N elements from position X"

**Slicing gotchas:**
```ruby
arr = [1, 2, 3, 4, 5]

# Out of bounds returns nil
arr[10..20]  # nil

# But partial overlap works
arr[3..100]  # [4, 5]

# Empty slice
arr[2..1]    # []
arr[2...2]   # []
```

**Slice assignment:**
```ruby
arr = [1, 2, 3, 4, 5]

# Replace range
arr[1..3] = [20, 30]
puts arr.inspect  # [1, 20, 30, 5]

# Insert (zero-length range)
arr = [1, 2, 3, 4, 5]
arr[2..1] = [99]  # Insert before index 2
puts arr.inspect  # [1, 2, 99, 3, 4, 5]

# Delete (assign empty array)
arr = [1, 2, 3, 4, 5]
arr[1..3] = []
puts arr.inspect  # [1, 5]
```

**The `.slice!` method:**

Removes and returns elements (mutating):
```ruby
arr = ["a", "b", "c", "d", "e"]

removed = arr.slice!(1..3)
puts removed.inspect  # ["b", "c", "d"]
puts arr.inspect      # ["a", "e"]
```

---

## 5.2 Hashes vs Python Dictionaries

Ruby's Hashes are equivalent to Python dictionaries: unordered (in older Ruby) or insertion-ordered (Ruby 1.9+) collections of key-value pairs. The functionality is similar, but Ruby offers some syntactic sugar, especially for symbol keys.

### 5.2.1 Creating Hashes

**Key Similarities:**
- Both store key-value pairs
- Both support mixed key/value types
- Both maintain insertion order (modern versions)

**Key Differences:**
- Ruby uses `=>` (hash rocket) or `:` for key-value pairs
- Ruby has special syntax for symbol keys
- Ruby's `Hash.new` can take a default value or block

#### Python Example

```python
# Empty dictionary
empty = {}
# Or
empty = dict()

# Dictionary with values
person = {
    "name": "Alice",
    "age": 30,
    "city": "NYC"
}

# Mixed key types (though not recommended)
mixed = {
    "string_key": 1,
    42: "number key",
    (1, 2): "tuple key"
}

# Dictionary constructor
scores = dict(alice=95, bob=87, charlie=92)
print(scores)  # {'alice': 95, 'bob': 87, 'charlie': 92}

# From list of tuples
pairs = [("a", 1), ("b", 2), ("c", 3)]
d = dict(pairs)
print(d)  # {'a': 1, 'b': 2, 'c': 3}

# Dictionary comprehension
squares = {x: x**2 for x in range(5)}
print(squares)  # {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}

# Default value with get
value = person.get("country", "Unknown")
print(value)  # Unknown

# defaultdict for automatic defaults
from collections import defaultdict
counts = defaultdict(int)
counts["a"] += 1
print(counts)  # defaultdict(<class 'int'>, {'a': 1})
```

#### Ruby Equivalent

```ruby
# Empty hash
empty = {}
# Or
empty = Hash.new

# Hash with values (hash rocket syntax)
person = {
  "name" => "Alice",
  "age" => 30,
  "city" => "NYC"
}

# Symbol keys with modern syntax (preferred)
person = {
  name: "Alice",
  age: 30,
  city: "NYC"
}
# This is syntactic sugar for:
# { :name => "Alice", :age => 30, :city => "NYC" }

# Mixed syntax (not recommended but legal)
mixed = {
  "string_key" => 1,
  42 => "number key",
  [1, 2] => "array key"
}

# Hash from array of pairs
pairs = [[:a, 1], [:b, 2], [:c, 3]]
h = Hash[pairs]
puts h.inspect  # {:a=>1, :b=>2, :c=>3}

# Or using .to_h
h = pairs.to_h
puts h.inspect  # {:a=>1, :b=>2, :c=>3}

# Hash with default value
hash = Hash.new(0)
hash[:a] += 1
puts hash.inspect  # {:a=>1}
puts hash[:b]      # 0 (default)

# Hash with default block
hash = Hash.new { |h, k| h[k] = [] }
hash[:a] << 1
hash[:a] << 2
hash[:b] << 3
puts hash.inspect  # {:a=>[1, 2], :b=>[3]}

# Creating with .map (like dict comprehension)
squares = (0..4).map { |x| [x, x**2] }.to_h
puts squares.inspect  # {0=>0, 1=>1, 2=>4, 3=>9, 4=>16}

# Or using each_with_object
squares = (0..4).each_with_object({}) { |x, h| h[x] = x**2 }
puts squares.inspect

# Or transform_values (Ruby 2.4+)
hash = (0..4).to_a.zip((0..4).to_a).to_h
squares = hash.transform_values { |v| v**2 }
```

#### Explanation

**Hash rocket `=>` vs colon `:`:**

Ruby has two syntaxes for hashes:

1. **Hash rocket** (works for all key types):
```ruby
hash = {
  "string" => 1,
  :symbol => 2,
  42 => 3
}
```

2. **Colon syntax** (only for symbol keys):
```ruby
# Modern Ruby style (preferred)
hash = {
  name: "Alice",
  age: 30
}

# Exactly equivalent to:
hash = {
  :name => "Alice",
  :age => 30
}
```

**Important:** The colon syntax only works when the key is a symbol AND it comes first:
```ruby
# Valid
{ name: "Alice" }
{ :name => "Alice" }

# Invalid
{ "name": "Alice" }  # Syntax error! Use => for string keys
```

**Hash.new with defaults:**

```ruby
# Default value
hash = Hash.new(0)
hash[:a]  # 0
hash[:b]  # 0

# All missing keys return the SAME default object
hash = Hash.new([])
hash[:a] << 1
puts hash[:b].inspect  # [1] - same array!

# Use a block for unique defaults
hash = Hash.new { |h, k| h[k] = [] }
hash[:a] << 1
puts hash[:b].inspect  # [] - different array
puts hash.inspect      # {:a=>[1], :b=>[]}
```

**Common pattern - counting:**
```ruby
# Without default
counts = {}
words = ["apple", "banana", "apple", "cherry", "banana", "apple"]

words.each do |word|
  counts[word] ||= 0  # Initialize if nil
  counts[word] += 1
end

# With default
counts = Hash.new(0)
words.each do |word|
  counts[word] += 1
end

puts counts.inspect  # {"apple"=>3, "banana"=>2, "cherry"=>1}
```

---

### 5.2.2 Accessing Hash Values

**Key Similarities:**
- Both use `[]` for access
- Both support checking for key existence
- Both return something falsy for missing keys (nil vs None)

**Key Differences:**
- Ruby returns `nil` for missing keys (vs KeyError in Python without `.get()`)
- Ruby's `.fetch` for strict access with optional defaults
- Ruby's `.dig` for nested access

#### Python Example

```python
person = {
    "name": "Alice",
    "age": 30,
    "address": {
        "city": "NYC",
        "country": "USA"
    }
}

# Access by key
print(person["name"])  # Alice
print(person["age"])   # 30

# Missing key raises error
try:
    print(person["email"])
except KeyError:
    print("Key not found")

# .get() for safe access
print(person.get("email"))           # None
print(person.get("email", "N/A"))    # N/A

# Check if key exists
print("name" in person)     # True
print("email" in person)    # False

# Get all keys
print(person.keys())        # dict_keys(['name', 'age', 'address'])
print(list(person.keys()))  # ['name', 'age', 'address']

# Get all values
print(person.values())      # dict_values(['Alice', 30, {...}])

# Get key-value pairs
print(person.items())
# dict_items([('name', 'Alice'), ('age', 30), ('address', {...})])

# Nested access
print(person["address"]["city"])  # NYC

# Safe nested access (Python 3.8+) - no direct support
# Must check each level or use get()
city = person.get("address", {}).get("city", "Unknown")
print(city)  # NYC

# Assignment
person["email"] = "alice@example.com"
print(person["email"])  # alice@example.com

# Update from another dict
updates = {"age": 31, "job": "Engineer"}
person.update(updates)
print(person["age"])  # 31
print(person["job"])  # Engineer
```

#### Ruby Equivalent

```ruby
person = {
  name: "Alice",
  age: 30,
  address: {
    city: "NYC",
    country: "USA"
  }
}

# Access by key
puts person[:name]  # Alice
puts person[:age]   # 30

# Missing key returns nil (no error!)
puts person[:email]          # nil
puts person[:email].inspect  # "nil"

# .fetch for strict access (raises error if missing)
begin
  puts person.fetch(:email)
rescue KeyError => e
  puts "Key not found"
end

# .fetch with default
puts person.fetch(:email, "N/A")  # N/A

# .fetch with block
puts person.fetch(:email) { |key| "#{key} not found" }
# Output: email not found

# Check if key exists
puts person.key?(:name)   # true
puts person.key?(:email)  # false
# Also: .has_key? (older style)

# Check if value exists
puts person.value?("Alice")  # true
puts person.value?(50)       # false
# Also: .has_value?

# Get all keys
puts person.keys.inspect        # [:name, :age, :address]

# Get all values
puts person.values.inspect      # ["Alice", 30, {:city=>"NYC", :country=>"USA"}]

# Nested access
puts person[:address][:city]  # NYC

# .dig for safe nested access (Ruby 2.3+)
puts person.dig(:address, :city)     # NYC
puts person.dig(:address, :zipcode)  # nil (no error!)
puts person.dig(:work, :company)     # nil (no error!)

# Assignment
person[:email] = "alice@example.com"
puts person[:email]  # alice@example.com

# Update/merge from another hash
updates = { age: 31, job: "Engineer" }
person.merge!(updates)  # Mutating
puts person[:age]       # 31
puts person[:job]       # Engineer

# Or non-mutating
new_person = person.merge(city: "SF")
puts new_person[:city]  # SF
puts person[:city]      # nil (original unchanged)
```

#### Explanation

**Nil vs KeyError:**

This is a significant difference:
```ruby
hash = { a: 1, b: 2 }

# Python would raise KeyError
# Ruby returns nil
value = hash[:c]  # nil

# This can hide bugs!
if hash[:c]
  puts "Found"
else
  puts "Not found"  # Always executes for missing keys
end

# Use .fetch when you want errors
hash.fetch(:c)  # KeyError!
```

**When to use `[]` vs `.fetch`:**
- Use `[]` when missing keys are expected and nil is acceptable
- Use `.fetch` when missing keys indicate a programming error
- Use `.fetch(key, default)` when you want a specific default value

**The `.dig` method:**

Safely access nested hashes without checking each level:
```ruby
data = {
  user: {
    profile: {
      name: "Alice"
    }
  }
}

# Without .dig
name = data[:user] && data[:user][:profile] && data[:user][:profile][:name]

# With .dig
name = data.dig(:user, :profile, :name)  # "Alice"

# Missing intermediate keys
email = data.dig(:user, :contact, :email)  # nil (no error)

# Works with arrays too
data = {
  users: [
    { name: "Alice" },
    { name: "Bob" }
  ]
}

data.dig(:users, 0, :name)  # "Alice"
data.dig(:users, 5, :name)  # nil
```

**Key checking methods:**
```ruby
hash = { name: "Alice", age: 30 }

# Check for key
hash.key?(:name)      # true
hash.has_key?(:name)  # true (older style)
hash.include?(:name)  # true (works but less clear)

# Check for value
hash.value?("Alice")     # true
hash.has_value?("Alice") # true (older style)

# Get key for value
hash.key("Alice")  # :name
hash.key("Bob")    # nil
```

---

### 5.2.3 Common Hash Methods

Ruby hashes have many useful methods for manipulation and transformation.

#### Python Example

```python
person = {"name": "Alice", "age": 30, "city": "NYC"}

# Get length
print(len(person))  # 3

# Remove key
age = person.pop("age")
print(age)    # 30
print(person) # {'name': 'Alice', 'city': 'NYC'}

# Remove key with default
country = person.pop("country", "Unknown")
print(country)  # Unknown

# Clear all
person.clear()
print(person)  # {}

# Copy
original = {"a": 1, "b": 2}
copy = original.copy()
copy["a"] = 99
print(original)  # {'a': 1, 'b': 2}
print(copy)      # {'a': 99, 'b': 2}

# Merge dictionaries
dict1 = {"a": 1, "b": 2}
dict2 = {"b": 3, "c": 4}

# Python 3.9+
merged = dict1 | dict2
print(merged)  # {'a': 1, 'b': 3, 'c': 4}

# Or
merged = {**dict1, **dict2}
print(merged)  # {'a': 1, 'b': 3, 'c': 4}

# Filter dictionary
scores = {"Alice": 95, "Bob": 75, "Charlie": 85}
passed = {k: v for k, v in scores.items() if v >= 80}
print(passed)  # {'Alice': 95, 'Charlie': 85}

# Transform values
scores = {"Alice": 95, "Bob": 75, "Charlie": 85}
curved = {k: v + 5 for k, v in scores.items()}
print(curved)  # {'Alice': 100, 'Bob': 80, 'Charlie': 90}

# Invert dictionary (swap keys and values)
original = {"a": 1, "b": 2, "c": 3}
inverted = {v: k for k, v in original.items()}
print(inverted)  # {1: 'a', 2: 'b', 3: 'c'}
```

#### Ruby Equivalent

```ruby
person = { name: "Alice", age: 30, city: "NYC" }

# Get length
puts person.length  # 3
puts person.size    # 3 (same)

# Remove key
age = person.delete(:age)
puts age           # 30
puts person.inspect # {:name=>"Alice", :city=>"NYC"}

# Remove key with block (if key doesn't exist)
country = person.delete(:country) { "Unknown" }
puts country  # Unknown

# Clear all
person.clear
puts person.inspect  # {}

# Copy (shallow)
original = { a: 1, b: 2 }
copy = original.dup
copy[:a] = 99
puts original.inspect  # {:a=>1, :b=>2}
puts copy.inspect      # {:a=>99, :b=>2}

# Deep copy (for nested hashes)
require 'json'
deep_copy = JSON.parse(original.to_json, symbolize_names: true)

# Merge hashes
hash1 = { a: 1, b: 2 }
hash2 = { b: 3, c: 4 }

# Non-mutating merge
merged = hash1.merge(hash2)
puts merged.inspect    # {:a=>1, :b=>3, :c=>4}
puts hash1.inspect     # {:a=>1, :b=>2} - unchanged

# Mutating merge
hash1.merge!(hash2)
puts hash1.inspect     # {:a=>1, :b=>3, :c=>4}

# Filter/select
scores = { Alice: 95, Bob: 75, Charlie: 85 }
passed = scores.select { |name, score| score >= 80 }
puts passed.inspect  # {:Alice=>95, :Charlie=>85}

# Reject (opposite of select)
failed = scores.reject { |name, score| score >= 80 }
puts failed.inspect  # {:Bob=>75}

# Transform values
scores = { Alice: 95, Bob: 75, Charlie: 85 }
curved = scores.transform_values { |score| score + 5 }
puts curved.inspect  # {:Alice=>100, :Bob=>80, :Charlie=>90}

# Transform keys
hash = { a: 1, b: 2, c: 3 }
upcase_keys = hash.transform_keys { |k| k.to_s.upcase }
puts upcase_keys.inspect  # {"A"=>1, "B"=>2, "C"=>3}

# Invert (swap keys and values)
original = { a: 1, b: 2, c: 3 }
inverted = original.invert
puts inverted.inspect  # {1=>:a, 2=>:b, 3=>:c}

# Keep only specific keys
person = { name: "Alice", age: 30, city: "NYC", country: "USA" }
basic = person.slice(:name, :age)
puts basic.inspect  # {:name=>"Alice", :age=>30}

# Remove specific keys (except)
person = { name: "Alice", age: 30, city: "NYC", country: "USA" }
without_location = person.except(:city, :country)
puts without_location.inspect  # {:name=>"Alice", :age=>30}

# Compact - remove nil values
hash = { a: 1, b: nil, c: 3, d: nil }
cleaned = hash.compact
puts cleaned.inspect  # {:a=>1, :c=>3}

# Check if empty
puts {}.empty?        # true
puts {a: 1}.empty?    # false

# Any? / None? / All?
hash = { a: 1, b: 2, c: 3 }
puts hash.any? { |k, v| v > 2 }   # true
puts hash.all? { |k, v| v > 0 }   # true
puts hash.none? { |k, v| v < 0 }  # true
```

#### Explanation

**Select vs Reject:**
```ruby
hash = { a: 1, b: 2, c: 3, d: 4 }

# Select keeps elements where block is true
hash.select { |k, v| v.even? }
# {:b=>2, :d=>4}

# Reject removes elements where block is true
hash.reject { |k, v| v.even? }
# {:a=>1, :c=>3}

# They're opposites
hash.select { |k, v| v > 2 }  # {:c=>3, :d=>4}
hash.reject { |k, v| v <= 2 } # {:c=>3, :d=>4}
```

**Transform methods (Ruby 2.4+):**
```ruby
hash = { a: 1, b: 2, c: 3 }

# Transform values (keep keys)
hash.transform_values { |v| v * 10 }
# {:a=>10, :b=>20, :c=>30}

# Transform keys (keep values)
hash.transform_keys { |k| k.to_s }
# {"a"=>1, "b"=>2, "c"=>3}

# Transform keys and values
hash.transform_values { |v| v * 2 }.transform_keys { |k| k.to_s.upcase }
# {"A"=>2, "B"=>4, "C"=>6}
```

**Merge with block:**
```ruby
hash1 = { a: 1, b: 2 }
hash2 = { b: 3, c: 4 }

# Simple merge - hash2 values win
hash1.merge(hash2)
# {:a=>1, :b=>3, :c=>4}

# Merge with block to resolve conflicts
hash1.merge(hash2) { |key, old_val, new_val| old_val + new_val }
# {:a=>1, :b=>5, :c=>4}  # b's values were summed

# Keep larger value
hash1.merge(hash2) { |key, old_val, new_val| [old_val, new_val].max }
```

**Useful hash combinations:**
```ruby
# Convert hash to array of pairs
hash = { a: 1, b: 2, c: 3 }
hash.to_a  # [[:a, 1], [:b, 2], [:c, 3]]

# Flatten hash to array
hash.flatten  # [:a, 1, :b, 2, :c, 3]

# Default value for all keys
hash = Hash.new(0)
hash.values_at(:a, :b, :c)  # [0, 0, 0]

# Fetch multiple values
hash = { a: 1, b: 2, c: 3 }
hash.values_at(:a, :c, :e)  # [1, 3, nil]
hash.fetch_values(:a, :c)   # [1, 3]
# hash.fetch_values(:a, :e) # KeyError!
```

---

### 5.2.4 Hash Iteration

**Key Similarities:**
- Both allow iteration over keys, values, or key-value pairs
- Both maintain insertion order (modern versions)

**Key Differences:**
- Ruby uses `.each` with block parameters
- Ruby has `.each_key` and `.each_value` convenience methods
- Ruby's iteration methods are more uniform with arrays

#### Python Example

```python
scores = {"Alice": 95, "Bob": 87, "Charlie": 92}

# Iterate over keys (default)
for name in scores:
    print(name)
# Output: Alice Bob Charlie

# Iterate over keys (explicit)
for name in scores.keys():
    print(name)

# Iterate over values
for score in scores.values():
    print(score)
# Output: 95 87 92

# Iterate over key-value pairs
for name, score in scores.items():
    print(f"{name}: {score}")
# Output:
# Alice: 95
# Bob: 87
# Charlie: 92

# With enumerate for index
for i, (name, score) in enumerate(scores.items()):
    print(f"{i}: {name} = {score}")
# Output:
# 0: Alice = 95
# 1: Bob = 87
# 2: Charlie = 92

# Dictionary comprehension with iteration
doubled = {name: score * 2 for name, score in scores.items()}
print(doubled)
# {'Alice': 190, 'Bob': 174, 'Charlie': 184}
```

#### Ruby Equivalent

```ruby
scores = { Alice: 95, Bob: 87, Charlie: 92 }

# Iterate over key-value pairs (most common)
scores.each do |name, score|
  puts "#{name}: #{score}"
end
# Output:
# Alice: 95
# Bob: 87
# Charlie: 92

# Single-line syntax
scores.each { |name, score| puts "#{name}: #{score}" }

# Iterate over keys only
scores.each_key do |name|
  puts name
end
# Output: Alice Bob Charlie

# Iterate over values only
scores.each_value do |score|
  puts score
end
# Output: 95 87 92

# With index
scores.each.with_index do |(name, score), i|
  puts "#{i}: #{name} = #{score}"
end
# Output:
# 0: Alice = 95
# 1: Bob = 87
# 2: Charlie = 92

# Transform with .map (returns array of pairs)
doubled_pairs = scores.map { |name, score| [name, score * 2] }
puts doubled_pairs.inspect
# [[:Alice, 190], [:Bob, 174], [:Charlie, 184]]

# Convert back to hash
doubled = scores.map { |name, score| [name, score * 2] }.to_h
puts doubled.inspect
# {:Alice=>190, :Bob=>174, :Charlie=>184}

# Or use transform_values (better)
doubled = scores.transform_values { |score| score * 2 }
puts doubled.inspect
# {:Alice=>190, :Bob=>174, :Charlie=>184}

# each_with_object - build a new hash
grades = scores.each_with_object({}) do |(name, score), result|
  result[name] = case score
                 when 90..100 then 'A'
                 when 80..89 then 'B'
                 when 70..79 then 'C'
                 else 'F'
                 end
end
puts grades.inspect
# {:Alice=>"A", :Bob=>"B", :Charlie=>"A"}
```

#### Explanation

**Block parameters for hashes:**

When iterating over a hash with `.each`, the block can take one or two parameters:
```ruby
hash = { a: 1, b: 2 }

# Two parameters - key and value
hash.each do |key, value|
  puts "#{key}: #{value}"
end

# One parameter - receives [key, value] array
hash.each do |pair|
  puts "#{pair[0]}: #{pair[1]}"
  # Or with destructuring
  key, value = pair
  puts "#{key}: #{value}"
end
```

**Specialized iteration methods:**
```ruby
hash = { a: 1, b: 2, c: 3 }

# Iterate keys
hash.each_key { |k| puts k }

# Iterate values
hash.each_value { |v| puts v }

# Iterate pairs
hash.each_pair { |k, v| puts "#{k}: #{v}" }
# Same as .each
```

**Building new hashes from iteration:**

```ruby
scores = { Alice: 95, Bob: 75, Charlie: 85 }

# Method 1: map + to_h
passed = scores.map { |name, score|
  [name, score] if score >= 80
}.compact.to_h
puts passed.inspect  # {:Alice=>95, :Charlie=>85}

# Method 2: select (better for filtering)
passed = scores.select { |name, score| score >= 80 }
puts passed.inspect  # {:Alice=>95, :Charlie=>85}

# Method 3: each_with_object (for transforming)
letter_grades = scores.each_with_object({}) do |(name, score), result|
  grade = score >= 90 ? 'A' : score >= 80 ? 'B' : 'C'
  result[name] = grade
end
puts letter_grades.inspect

# Method 4: transform_values (when only values change)
curved = scores.transform_values { |score| [score + 5, 100].min }
puts curved.inspect
```

**Nested hash iteration:**
```ruby
users = {
  alice: { age: 30, city: "NYC" },
  bob: { age: 25, city: "SF" },
  charlie: { age: 35, city: "LA" }
}

users.each do |name, info|
  puts "#{name}:"
  info.each do |key, value|
    puts "  #{key}: #{value}"
  end
end

# Output:
# alice:
#   age: 30
#   city: NYC
# bob:
#   age: 25
#   city: SF
# charlie:
#   age: 35
#   city: LA
```

---

## 5.3 Symbols (Ruby-specific concept)

Symbols are one of Ruby's unique features that don't have a direct equivalent in Python. They look like `:this` and are immutable, lightweight identifiers primarily used as hash keys and constant names.

### 5.3.1 What are Symbols?

**Key Concept:**
- Symbols are immutable identifiers
- Written with a leading colon: `:symbol_name`
- Each unique symbol exists only once in memory
- Python has no direct equivalent (closest: interned strings or enums)

#### Python Example

Python doesn't have symbols, but here are some conceptually similar patterns:

```python
# Python uses strings for keys
person = {
    "name": "Alice",
    "age": 30
}

# Or could use enums for constants
from enum import Enum

class Status(Enum):
    PENDING = 1
    APPROVED = 2
    REJECTED = 3

user_status = Status.PENDING

# String interning (similar concept, but rare in Python)
s1 = "hello"
s2 = "hello"
print(s1 is s2)  # True (CPython interns short strings)

# But not guaranteed for all strings
s1 = "hello world!"
s2 = "hello world!"
print(s1 is s2)  # False (different objects)

# Python typically just uses strings everywhere
config = {
    "database": "postgres",
    "port": 5432,
    "debug": True
}

# Constants by convention (but still strings/ints)
DEBUG_MODE = True
MAX_CONNECTIONS = 100
```

#### Ruby Equivalent

```ruby
# Symbols are written with a leading colon
name = :alice
status = :pending
direction = :north

# Using symbols as hash keys (most common use)
person = {
  name: "Alice",   # :name is a symbol
  age: 30          # :age is a symbol
}

# Equivalent to:
person = {
  :name => "Alice",
  :age => 30
}

# Symbols are unique - each symbol exists only once in memory
sym1 = :hello
sym2 = :hello
puts sym1.object_id  # Some number, e.g., 1234568
puts sym2.object_id  # Same number! They're the same object

# Strings are different objects
str1 = "hello"
str2 = "hello"
puts str1.object_id  # e.g., 260
puts str2.object_id  # e.g., 280 - different!

# Symbols for constants and status values
STATUS_PENDING = :pending
STATUS_APPROVED = :approved
STATUS_REJECTED = :rejected

user_status = :pending

case user_status
when :pending
  puts "Waiting for approval"
when :approved
  puts "Access granted"
when :rejected
  puts "Access denied"
end

# Symbols cannot be modified
sym = :hello
# sym[0] = 'H'  # Error! Symbols don't have []= method

# Getting all symbols in memory
puts Symbol.all_symbols.size  # Thousands of symbols
puts Symbol.all_symbols.first(10).inspect
```

#### Explanation

**What makes symbols special:**

1. **Immutable:** Symbols cannot be changed after creation
2. **Unique:** Each symbol exists only once in memory
3. **Fast comparisons:** Because they're unique, comparing symbols is very fast
4. **Lightweight:** Take less memory than strings for repeated use

**When to use symbols:**

```ruby
# Good uses of symbols:

# 1. Hash keys (most common)
person = { name: "Alice", age: 30 }

# 2. Method names and references
obj.send(:method_name)
obj.respond_to?(:method_name)

# 3. Constants and status values
status = :active
user.update(status: :inactive)

# 4. Internal identifiers
options = { mode: :strict, format: :json }
```

**When NOT to use symbols:**

```ruby
# Bad uses of symbols:

# 1. User input (creates symbols that never get garbage collected)
# BAD - Memory leak!
user_input = gets.chomp
config[user_input.to_sym] = value

# 2. Dynamic values that change frequently
# BAD
current_time = Time.now.to_s.to_sym  # ":2024-01-15 10:30:45"

# 3. Text that will be displayed
# BAD
puts :hello  # Prints ":hello" literally
# GOOD
puts "hello"  # Prints "hello"
```

**Memory characteristics:**
```ruby
# Strings create new objects
1000.times do
  str = "hello"  # Creates 1000 different string objects
end

# Symbols reuse the same object
1000.times do
  sym = :hello   # Always the same :hello object
end

# This makes symbols more memory-efficient for repeated use
# But symbols are never garbage collected!
```

---

### 5.3.2 Symbols vs Strings

Understanding when to use symbols vs strings is important for writing idiomatic Ruby code.

#### Python Example

Python just uses strings for everything:

```python
# Dictionary keys - strings
person = {"name": "Alice", "age": 30}

# Constants - strings
STATUS_ACTIVE = "active"
STATUS_INACTIVE = "inactive"

# Method names - strings
method_name = "capitalize"
result = getattr("hello", method_name)()  # "Hello"

# Everything is a string
config = {
    "environment": "production",
    "debug": "false",
    "log_level": "info"
}
```

#### Ruby Equivalent

```ruby
# Symbols vs Strings - key differences

# 1. MUTABILITY
string = "hello"
string.upcase!
puts string  # "HELLO" - modified

symbol = :hello
# symbol.upcase!  # Error! Symbols are immutable

# 2. IDENTITY
str1 = "hello"
str2 = "hello"
puts str1.object_id == str2.object_id  # false - different objects

sym1 = :hello
sym2 = :hello
puts sym1.object_id == sym2.object_id  # true - same object!

# 3. COMPARISON SPEED
require 'benchmark'

# Comparing strings
Benchmark.bm do |x|
  x.report("strings:") do
    100000.times { "hello" == "hello" }
  end

  x.report("symbols:") do
    100000.times { :hello == :hello }
  end
end
# Symbols are faster!

# 4. USE CASES

# Use SYMBOLS for:
# - Hash keys
user = { name: "Alice", email: "alice@example.com" }

# - Internal identifiers
def set_status(status)
  @status = status
end
set_status(:active)

# - Method arguments (especially options)
def process(data, mode: :strict, format: :json)
  # ...
end

# - Representing concepts/states
case order.status
when :pending then "Waiting"
when :shipped then "On the way"
when :delivered then "Complete"
end

# Use STRINGS for:
# - User-facing text
puts "Hello, world!"
error_message = "File not found"

# - User input
user_name = gets.chomp  # String, not symbol

# - Data that changes
timestamp = Time.now.to_s

# - Text that will be displayed or stored
File.write("output.txt", "Some content")

# 5. CONVERSION
# String to symbol
"hello".to_sym    # :hello
"hello".intern    # :hello (same as to_sym)

# Symbol to string
:hello.to_s       # "hello"
:hello.id2name    # "hello" (same as to_s)

# 6. STRING INTERPOLATION
name = "Alice"
symbol = :greeting

# Strings support interpolation
puts "Hello, #{name}!"  # "Hello, Alice!"

# Symbols don't (they're converted to strings)
puts "Symbol: #{symbol}"  # "Symbol: greeting"

# 7. WITH SPACES AND SPECIAL CHARACTERS
# Symbols can have spaces/special chars using quotes
sym = :"hello world"
sym = :"hello-world"
sym = :"hello@world"
puts sym.inspect  # :"hello world"

# Or using %s
sym = %s(hello world)
```

#### Explanation

**Decision tree - Symbol or String?**

```ruby
# Ask yourself:

# 1. Is this an identifier or label?
#    YES -> Use symbol
hash_key = :name
option = :verbose

# 2. Will this be displayed to users?
#    YES -> Use string
message = "Hello, user!"
error = "File not found"

# 3. Does this come from user input?
#    YES -> Use string (don't convert to symbol!)
user_input = gets.chomp  # Keep as string

# 4. Will this value change or be modified?
#    YES -> Use string
mutable_text = "hello"
mutable_text.upcase!

# 5. Is this a hash key for internal use?
#    YES -> Use symbol
config = { debug: true, verbose: false }

# 6. Is this a hash key from external data (JSON, etc.)?
#    MAYBE -> Often string, but can convert to symbol
json_data = { "name" => "Alice" }  # String keys from JSON
# Or convert if safe:
json_data = { name: "Alice" }  # Symbol keys
```

**Common gotcha - mixing symbols and strings:**

```ruby
hash = { name: "Alice", age: 30 }

# Symbol key works
puts hash[:name]  # "Alice"

# String key doesn't work!
puts hash["name"] # nil

# They're different keys!
hash = {
  :name => "Alice",
  "name" => "Bob"
}

puts hash[:name]   # "Alice"
puts hash["name"]  # "Bob"
```

**Performance comparison:**
```ruby
# Memory usage
# Creating 10,000 symbols that are the same
10000.times { :hello }  # Only 1 :hello object in memory

# Creating 10,000 strings
10000.times { "hello" } # 10,000 different objects!

# Speed comparison
# Symbol comparison: O(1) - compare object_id
:hello == :hello  # Fast!

# String comparison: O(n) - compare each character
"hello" == "hello"  # Slower (but usually negligible)
```

---

### 5.3.3 Symbols as Hash Keys

Using symbols as hash keys is one of the most common patterns in Ruby. It's so common that Ruby has special syntax for it.

#### Python Example

Python uses strings (or other hashable types) as dictionary keys:

```python
# String keys
person = {
    "name": "Alice",
    "age": 30,
    "email": "alice@example.com"
}

# Access
print(person["name"])

# Method with keyword arguments (similar concept)
def create_user(name, age, email):
    return {
        "name": name,
        "age": age,
        "email": email
    }

user = create_user(name="Alice", age=30, email="alice@example.com")

# Options dictionary
def process_data(data, **options):
    debug = options.get("debug", False)
    verbose = options.get("verbose", False)
    print(f"Debug: {debug}, Verbose: {verbose}")

process_data([], debug=True, verbose=False)

# Nested dictionaries
config = {
    "database": {
        "host": "localhost",
        "port": 5432,
        "name": "mydb"
    },
    "cache": {
        "enabled": True,
        "ttl": 3600
    }
}

print(config["database"]["host"])
```

#### Ruby Equivalent

```ruby
# Symbol keys - modern syntax (preferred)
person = {
  name: "Alice",
  age: 30,
  email: "alice@example.com"
}

# This is syntactic sugar for:
person = {
  :name => "Alice",
  :age => 30,
  :email => "alice@example.com"
}

# Access with symbols
puts person[:name]   # "Alice"
puts person[:age]    # 30

# Method with keyword arguments
def create_user(name:, age:, email:)
  {
    name: name,
    age: age,
    email: email
  }
end

user = create_user(name: "Alice", age: 30, email: "alice@example.com")

# Method with optional keyword arguments
def process_data(data, debug: false, verbose: false)
  puts "Debug: #{debug}, Verbose: #{verbose}"
end

process_data([], debug: true, verbose: false)

# Can also use ** to collect keyword arguments
def process_data(data, **options)
  debug = options[:debug] || false
  verbose = options[:verbose] || false
  puts "Debug: #{debug}, Verbose: #{verbose}"
end

process_data([], debug: true, mode: :strict)

# Nested hashes with symbols
config = {
  database: {
    host: "localhost",
    port: 5432,
    name: "mydb"
  },
  cache: {
    enabled: true,
    ttl: 3600
  }
}

puts config[:database][:host]  # "localhost"

# Using .dig for safe nested access
puts config.dig(:database, :host)      # "localhost"
puts config.dig(:database, :username)  # nil
puts config.dig(:logging, :level)      # nil

# Symbol keys are the standard for options
def render(template, locals: {}, layout: nil, status: 200)
  puts "Template: #{template}"
  puts "Locals: #{locals.inspect}"
  puts "Layout: #{layout || 'default'}"
  puts "Status: #{status}"
end

render("users/show", locals: { user: "Alice" }, layout: "application")

# Fetching with symbols
settings = {
  theme: "dark",
  language: "en",
  notifications: true
}

theme = settings.fetch(:theme, "light")
timezone = settings.fetch(:timezone, "UTC")
puts theme     # "dark"
puts timezone  # "UTC"

# Converting string keys to symbol keys
string_hash = { "name" => "Alice", "age" => 30 }

# Manual conversion
symbol_hash = {}
string_hash.each do |key, value|
  symbol_hash[key.to_sym] = value
end
puts symbol_hash.inspect  # {:name=>"Alice", :age=>30}

# Or with transform_keys
symbol_hash = string_hash.transform_keys(&:to_sym)
puts symbol_hash.inspect  # {:name=>"Alice", :age=>30}

# Ruby 2.5+ - transform_keys!
string_hash.transform_keys!(&:to_sym)
puts string_hash.inspect  # {:name=>"Alice", :age=>30}
```

#### Explanation

**Why symbol keys are preferred:**

1. **More efficient:** Symbols are faster to compare
2. **More idiomatic:** Symbol keys are the Ruby way
3. **Cleaner syntax:** `name: "Alice"` vs `"name" => "Alice"`
4. **Keyword arguments:** Ruby's keyword arguments use symbols

**The modern syntax:**
```ruby
# These are identical:
hash = { name: "Alice", age: 30 }
hash = { :name => "Alice", :age => 30 }

# The :key syntax only works at the START of the expression
# This is VALID:
hash = { name: "Alice" }

# This is NOT valid:
# hash = { "Alice": name }  # Syntax error!

# Must use hash rocket for non-symbol keys:
hash = { "Alice" => :name }  # Valid
```

**Working with both string and symbol keys:**

```ruby
# Problem: Hash from JSON has string keys
require 'json'
json_string = '{"name":"Alice","age":30}'
hash = JSON.parse(json_string)
puts hash.inspect  # {"name"=>"Alice", "age"=>30}

# Accessing with symbols doesn't work
puts hash[:name]  # nil

# Solution 1: Convert to symbols
hash = JSON.parse(json_string, symbolize_names: true)
puts hash.inspect  # {:name=>"Alice", :age=>30}
puts hash[:name]   # "Alice" - works!

# Solution 2: Use HashWithIndifferentAccess (Rails)
# This allows both string and symbol access
# (Not in pure Ruby, but very common in Rails)

# Solution 3: Manually transform
hash = hash.transform_keys(&:to_sym)

# Solution 4: Use string keys everywhere (less common in Ruby)
hash = { "name" => "Alice", "age" => 30 }
puts hash["name"]  # "Alice"
```

**Common patterns:**

```ruby
# Default options pattern
def process(data, options = {})
  defaults = {
    verbose: false,
    strict: true,
    format: :json
  }

  opts = defaults.merge(options)

  puts "Processing with options: #{opts.inspect}"
end

process([], verbose: true)
# Output: Processing with options: {:verbose=>true, :strict=>true, :format=>:json}

# Configuration hashes
DATABASE_CONFIG = {
  adapter: "postgresql",
  host: "localhost",
  port: 5432,
  database: "myapp_production",
  pool: 5
}

# State machines
TRANSITIONS = {
  pending: [:approved, :rejected],
  approved: [:shipped],
  rejected: [:pending],
  shipped: [:delivered],
  delivered: []
}

# Method options with symbols
user.update(
  name: "Alice Smith",
  email: "alice.smith@example.com",
  status: :active
)
```

---

## 5.4 Sets

Ruby's Set class provides an unordered collection of unique elements, similar to Python's set. However, unlike Python where sets are built-in, Ruby's Set class must be required from the standard library.

**Key Similarities:**
- Both store unique elements
- Both support set operations (union, intersection, difference)
- Both provide fast membership testing

**Key Differences:**
- Ruby requires `require 'set'` to use Sets
- Ruby uses method names like `.add` instead of Python's `.add()`
- Ruby's Set is a class wrapper around Hash

#### Python Example

```python
# Sets are built-in in Python
numbers = {1, 2, 3, 4, 5}
print(numbers)  # {1, 2, 3, 4, 5}

# Empty set (can't use {} - that's a dict)
empty = set()

# From list (removes duplicates)
numbers = set([1, 2, 2, 3, 3, 3])
print(numbers)  # {1, 2, 3}

# Add element
numbers.add(4)
print(numbers)  # {1, 2, 3, 4}

# Add multiple elements
numbers.update([5, 6, 7])
print(numbers)  # {1, 2, 3, 4, 5, 6, 7}

# Remove element
numbers.remove(3)  # Raises KeyError if not found
print(numbers)  # {1, 2, 4, 5, 6, 7}

# Discard (no error if missing)
numbers.discard(10)  # No error

# Check membership
print(4 in numbers)     # True
print(10 in numbers)    # False

# Set operations
a = {1, 2, 3, 4}
b = {3, 4, 5, 6}

# Union
print(a | b)      # {1, 2, 3, 4, 5, 6}
print(a.union(b)) # {1, 2, 3, 4, 5, 6}

# Intersection
print(a & b)             # {3, 4}
print(a.intersection(b)) # {3, 4}

# Difference
print(a - b)          # {1, 2}
print(a.difference(b)) # {1, 2}

# Symmetric difference (XOR)
print(a ^ b)                      # {1, 2, 5, 6}
print(a.symmetric_difference(b))  # {1, 2, 5, 6}

# Subset/superset
c = {1, 2}
print(c.issubset(a))     # True
print(a.issuperset(c))   # True

# Iteration
for num in numbers:
    print(num)

# Length
print(len(numbers))  # 6

# Clear
numbers.clear()
print(numbers)  # set()
```

#### Ruby Equivalent

```ruby
# Must require 'set' first
require 'set'

# Create a set
numbers = Set.new([1, 2, 3, 4, 5])
puts numbers.inspect  # #<Set: {1, 2, 3, 4, 5}>

# Or use Set[]
numbers = Set[1, 2, 3, 4, 5]

# Empty set
empty = Set.new

# From array (removes duplicates)
numbers = Set.new([1, 2, 2, 3, 3, 3])
puts numbers.inspect  # #<Set: {1, 2, 3}>

# Add element
numbers.add(4)
# Or use <<
numbers << 5
puts numbers.inspect  # #<Set: {1, 2, 3, 4, 5}>

# Add multiple elements
numbers.merge([6, 7, 8])
puts numbers.inspect  # #<Set: {1, 2, 3, 4, 5, 6, 7, 8}>

# Remove element
numbers.delete(3)
puts numbers.inspect  # #<Set: {1, 2, 4, 5, 6, 7, 8}>

# Delete doesn't raise error if missing
numbers.delete(10)  # No error

# Check membership
puts numbers.include?(4)   # true
puts numbers.include?(10)  # false
# Also: member?
puts numbers.member?(4)    # true

# Set operations
a = Set[1, 2, 3, 4]
b = Set[3, 4, 5, 6]

# Union
puts (a | b).inspect      # #<Set: {1, 2, 3, 4, 5, 6}>
puts a.union(b).inspect   # #<Set: {1, 2, 3, 4, 5, 6}>

# Intersection
puts (a & b).inspect             # #<Set: {3, 4}>
puts a.intersection(b).inspect   # #<Set: {3, 4}>

# Difference
puts (a - b).inspect          # #<Set: {1, 2}>
puts a.difference(b).inspect  # #<Set: {1, 2}>

# Symmetric difference (XOR)
puts (a ^ b).inspect  # #<Set: {1, 2, 5, 6}>

# Subset/superset
c = Set[1, 2]
puts c.subset?(a)     # true
puts a.superset?(c)   # true

# Proper subset/superset (excluding equal)
puts c.proper_subset?(a)     # true
puts a.proper_superset?(c)   # true

# Iteration
numbers.each do |num|
  puts num
end

# Length
puts numbers.length  # 7
puts numbers.size    # 7

# Convert to array
puts numbers.to_a.inspect  # [1, 2, 4, 5, 6, 7, 8]

# Clear
numbers.clear
puts numbers.inspect  # #<Set: {}>

# Check if empty
puts numbers.empty?  # true

# Set operations with arrays
set = Set[1, 2, 3]
set.merge([4, 5, 6])  # Add elements from array
puts set.inspect  # #<Set: {1, 2, 3, 4, 5, 6}>

# Filter/select
set = Set[1, 2, 3, 4, 5, 6]
even_set = set.select(&:even?)
puts even_set.inspect  # #<Set: {2, 4, 6}>

# Map (returns Set)
set = Set[1, 2, 3]
doubled = set.map { |n| n * 2 }
puts doubled.inspect  # #<Set: {2, 4, 6}>

# Comparison
set1 = Set[1, 2, 3]
set2 = Set[1, 2, 3]
set3 = Set[3, 2, 1]

puts set1 == set2  # true
puts set1 == set3  # true (order doesn't matter)
```

#### Explanation

**When to use Sets:**

```ruby
# Good use cases:

# 1. Removing duplicates
tags = ["ruby", "python", "ruby", "javascript", "python"]
unique_tags = Set.new(tags)
puts unique_tags.to_a.inspect  # ["ruby", "python", "javascript"]

# 2. Fast membership testing
require 'set'
allowed_users = Set.new(["alice", "bob", "charlie"])

def authorized?(username, allowed)
  allowed.include?(username)  # O(1) lookup
end

puts authorized?("alice", allowed_users)  # true

# 3. Set operations
admins = Set["alice", "bob"]
moderators = Set["bob", "charlie", "david"]

# Who has any special privileges?
staff = admins | moderators
puts staff.inspect  # #<Set: {"alice", "bob", "charlie", "david"}>

# Who is both admin and moderator?
super_staff = admins & moderators
puts super_staff.inspect  # #<Set: {"bob"}>

# 4. Tracking seen items
seen = Set.new

items = [1, 2, 3, 2, 1, 4, 3, 5]
items.each do |item|
  if seen.include?(item)
    puts "Duplicate: #{item}"
  else
    seen << item
  end
end
```

**Set vs Array:**

```ruby
# Arrays maintain order and allow duplicates
arr = [1, 2, 2, 3]
puts arr.inspect  # [1, 2, 2, 3]

# Sets remove duplicates and don't guarantee order
set = Set.new([1, 2, 2, 3])
puts set.inspect  # #<Set: {1, 2, 3}>

# Array lookup is O(n)
arr = (1..1000).to_a
arr.include?(999)  # Checks up to 999 elements

# Set lookup is O(1)
set = Set.new(1..1000)
set.include?(999)  # Constant time lookup

# Use arrays when:
# - Order matters
# - Duplicates are needed
# - You need indexing

# Use sets when:
# - Only uniqueness matters
# - You need fast membership testing
# - You need set operations (union, intersection, etc.)
```

**Common patterns:**

```ruby
require 'set'

# Building a set from method results
files = Set.new
Dir.glob("**/*.rb").each do |file|
  files << file
end

# Collecting unique values
unique_ids = Set.new
users.each do |user|
  unique_ids << user.id
end

# Or more idiomatically:
unique_ids = users.map(&:id).to_set

# Comparing two collections
old_tags = Set["ruby", "rails", "sinatra"]
new_tags = Set["ruby", "hanami", "sinatra"]

added = new_tags - old_tags
puts "Added: #{added.to_a.inspect}"  # ["hanami"]

removed = old_tags - new_tags
puts "Removed: #{removed.to_a.inspect}"  # ["rails"]

# Filtering with set membership
valid_statuses = Set[:pending, :approved, :rejected]

def valid_status?(status, valid_set)
  valid_set.include?(status)
end

puts valid_status?(:approved, valid_statuses)  # true
puts valid_status?(:deleted, valid_statuses)   # false
```

---

## Chapter 5 Summary

You've now learned about Ruby's collection types:

**Arrays:**
- Similar to Python lists but with Ruby syntax
- Created with `[]`, `Array.new`, or `%w` literal
- Rich set of methods with mutating (`!`) and non-mutating versions
- Slicing with ranges `[1..3]` or start/length `[1, 3]`

**Hashes:**
- Equivalent to Python dictionaries
- Use `=>` (hash rocket) or `:` syntax for symbol keys
- Modern Ruby prefers symbol keys: `{ name: "Alice" }`
- Powerful methods like `.transform_values`, `.dig`, `.merge`

**Symbols:**
- Unique Ruby feature - immutable identifiers written as `:symbol`
- Each symbol exists only once in memory
- Preferred for hash keys, internal identifiers, and constants
- Use strings for user-facing text, symbols for internal use

**Sets:**
- Require `require 'set'`
- Store unique elements with fast O(1) lookup
- Support set operations: union (`|`), intersection (`&`), difference (`-`)

**Key takeaways for Python developers:**

1. **Symbol keys are idiomatic:** `{ name: "Alice" }` not `{ "name" => "Alice" }`
2. **Mutating methods end in `!`:** `sort` vs `sort!`, `reverse` vs `reverse!`
3. **Ranges in slicing:** `arr[1..3]` includes index 3, `arr[1...3]` excludes it
4. **Nil for missing keys:** Hashes return `nil`, not an error (use `.fetch` for errors)
5. **Symbols are forever:** Don't convert user input to symbols (memory leak!)

In the next chapter, we'll explore **Enumerables and Functional Programming**, where you'll discover Ruby's powerful iterator methods that replace Python's list comprehensions.