# Chapter 6: Enumerables and Functional Programming

## 6.1 The Enumerable Module

Ruby's `Enumerable` module is one of the language's most powerful features. It's a mixin that provides collection classes with dozens of traversal, searching, and sorting methods. If you're familiar with Python's itertools, functional programming patterns, or list comprehensions, Ruby's Enumerable will feel both familiar and more integrated into the language.

**Key Similarities:**
- Both languages support functional programming patterns (map, filter, reduce)
- Both allow method chaining for data transformations
- Both work with any iterable/enumerable collection

**Key Differences:**
- Ruby's Enumerable methods are built directly into collection classes
- Ruby emphasizes method chaining over list comprehensions
- Ruby's approach is more object-oriented (methods on objects vs functions)
- Ruby uses blocks extensively with enumerable methods

### Python Approach

```python
# Python uses a mix of list comprehensions, built-in functions, and itertools
numbers = [1, 2, 3, 4, 5]

# List comprehension
doubled = [n * 2 for n in numbers]

# Built-in functions
total = sum(numbers)
evens = list(filter(lambda x: x % 2 == 0, numbers))

# Method chaining isn't idiomatic in Python
# You'd typically use multiple statements or nested calls
from functools import reduce
result = reduce(lambda acc, n: acc + n,
                filter(lambda x: x > 2,
                       map(lambda x: x * 2, numbers)))
```

### Ruby Equivalent

```ruby
# Ruby uses Enumerable methods with blocks
numbers = [1, 2, 3, 4, 5]

# Using map (equivalent to list comprehension)
doubled = numbers.map { |n| n * 2 }

# Built-in enumerable methods
total = numbers.sum
evens = numbers.select { |x| x.even? }

# Method chaining is idiomatic and readable
result = numbers
  .map { |x| x * 2 }
  .select { |x| x > 2 }
  .reduce(:+)
```

### Explanation

Any class that includes the `Enumerable` module and defines an `each` method automatically gets access to over 40 useful methods. Ruby's built-in collection classes (Array, Hash, Range, Set) all include Enumerable.

**Common Enumerable methods:**
- `map` / `collect` - Transform each element
- `select` / `filter` - Keep elements matching a condition
- `reject` - Exclude elements matching a condition
- `reduce` / `inject` - Combine elements into a single value
- `each_with_index` - Iterate with index
- `find` / `detect` - Find first matching element
- `any?` / `all?` / `none?` - Check conditions
- `sort` / `sort_by` - Sort elements
- `group_by` - Group elements by a key
- `zip` - Combine multiple arrays

**Important points:**
- Most Enumerable methods accept blocks (covered in detail in Chapter 8)
- Methods can be chained together for readable data transformations
- Ruby prefers Enumerable methods over list comprehensions
- Many methods have aliases (`map`/`collect`, `select`/`filter`, `reduce`/`inject`)
- Methods are lazy-evaluated when chained with `lazy` (covered in section 6.8)

---

## 6.2 `.map` vs List Comprehensions

In Python, list comprehensions are the primary way to transform collections. Ruby uses the `.map` method (also aliased as `.collect`) for the same purpose. While the syntax differs, the concept is identical: apply a transformation to each element and return a new collection.

**Key Similarities:**
- Both create new collections without modifying the original
- Both apply a transformation to each element
- Both can include conditional logic

**Key Differences:**
- Python uses special syntax (`[... for ... in ...]`)
- Ruby uses a method with a block (`array.map { |x| ... }`)
- Ruby's approach allows easy method chaining
- Python comprehensions can include filtering in the same expression

### Python Example

```python
# Basic transformation
numbers = [1, 2, 3, 4, 5]
squared = [n ** 2 for n in numbers]
# [1, 4, 9, 16, 25]

# With string transformation
names = ["alice", "bob", "charlie"]
capitalized = [name.capitalize() for name in names]
# ['Alice', 'Bob', 'Charlie']

# With condition (filter + map combined)
numbers = [1, 2, 3, 4, 5]
even_squared = [n ** 2 for n in numbers if n % 2 == 0]
# [4, 16]

# Nested comprehension
matrix = [[1, 2], [3, 4], [5, 6]]
flattened = [num for row in matrix for num in row]
# [1, 2, 3, 4, 5, 6]

# Transforming dictionaries
prices = {"apple": 0.50, "banana": 0.30}
doubled_prices = {k: v * 2 for k, v in prices.items()}
# {'apple': 1.0, 'banana': 0.6}
```

### Ruby Equivalent

```ruby
# Basic transformation
numbers = [1, 2, 3, 4, 5]
squared = numbers.map { |n| n ** 2 }
# [1, 4, 9, 16, 25]

# With string transformation
names = ["alice", "bob", "charlie"]
capitalized = names.map { |name| name.capitalize }
# ["Alice", "Bob", "Charlie"]

# With condition (use select + map or filter_map)
numbers = [1, 2, 3, 4, 5]
even_squared = numbers.select { |n| n.even? }.map { |n| n ** 2 }
# [4, 16]

# Or use filter_map (Ruby 2.7+) to combine filtering and mapping
even_squared = numbers.filter_map { |n| n ** 2 if n.even? }
# [4, 16]

# Nested mapping (flatten with flat_map)
matrix = [[1, 2], [3, 4], [5, 6]]
flattened = matrix.flat_map { |row| row }
# [1, 2, 3, 4, 5, 6]

# Transforming hashes
prices = { apple: 0.50, banana: 0.30 }
doubled_prices = prices.transform_values { |v| v * 2 }
# {:apple=>1.0, :banana=>0.6}
```

### Explanation

Ruby's `.map` method takes a block and applies it to each element, returning a new array with the transformed values. The block parameter (between `|` pipes) represents each element.

**Block syntax options:**
```ruby
# Curly braces for single-line blocks
numbers.map { |n| n * 2 }

# do...end for multi-line blocks
numbers.map do |n|
  result = n * 2
  result + 1
end
```

**Useful map variants:**
- `map` - Basic transformation
- `flat_map` - Map then flatten one level (like Python's nested comprehension)
- `filter_map` - Combine filtering and mapping in one pass (Ruby 2.7+)
- `map.with_index` - Map with access to the index

**Hash-specific methods:**
- `transform_keys` - Transform hash keys
- `transform_values` - Transform hash values
- `map` - Returns an array of `[key, value]` pairs

**Best Practice:** Use curly braces for simple one-line transformations and `do...end` for multi-line blocks. Ruby developers typically prefer `.map` over `.collect`, though both work identically.

---

## 6.3 `.select` and `.reject` (Filtering)

Python uses list comprehensions with conditionals or the `filter()` function to filter collections. Ruby provides `.select` (keep matching elements) and `.reject` (remove matching elements) methods. These are clearer and more readable than Python's filter with lambda functions.

**Key Similarities:**
- Both filter collections based on conditions
- Both create new collections without modifying the original
- Both work with any iterable/enumerable

**Key Differences:**
- Ruby provides both positive (`.select`) and negative (`.reject`) filtering
- Ruby's syntax is more readable for complex conditions
- Ruby has aliases (`.select`/`.filter`, `.reject` works like inverse filter)
- Ruby's predicate methods (`even?`, `odd?`, `nil?`) work seamlessly

### Python Example

```python
# Using list comprehension (most common)
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens = [n for n in numbers if n % 2 == 0]
# [2, 4, 6, 8, 10]

# Using filter() with lambda
evens = list(filter(lambda n: n % 2 == 0, numbers))
# [2, 4, 6, 8, 10]

# Negative filtering (keeping odds)
odds = [n for n in numbers if n % 2 != 0]
# [1, 3, 5, 7, 9]

# Filtering with method calls
words = ["hello", "world", "python", "programming"]
short_words = [w for w in words if len(w) < 7]
# ['hello', 'world', 'python']

# Filtering None values
values = [1, None, 2, None, 3]
without_none = [v for v in values if v is not None]
# [1, 2, 3]

# Multiple conditions
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
result = [n for n in numbers if n % 2 == 0 and n > 5]
# [6, 8, 10]
```

### Ruby Equivalent

```ruby
# Using select
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
evens = numbers.select { |n| n.even? }
# [2, 4, 6, 8, 10]

# Or using the filter alias (Ruby 2.6+)
evens = numbers.filter { |n| n.even? }
# [2, 4, 6, 8, 10]

# Negative filtering using reject
odds = numbers.reject { |n| n.even? }
# [1, 3, 5, 7, 9]

# Filtering with method calls
words = ["hello", "world", "ruby", "programming"]
short_words = words.select { |w| w.length < 7 }
# ["hello", "world", "ruby"]

# Filtering nil values
values = [1, nil, 2, nil, 3]
without_nil = values.reject { |v| v.nil? }
# [1, 2, 3]
# Or more idiomatically:
without_nil = values.compact
# [1, 2, 3]

# Multiple conditions
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
result = numbers.select { |n| n.even? && n > 5 }
# [6, 8, 10]
```

### Explanation

Ruby's `.select` keeps elements where the block returns a truthy value, while `.reject` removes elements where the block returns a truthy value. This dual approach often makes code more readable than Python's single filter mechanism.

**Key methods:**
- `select` / `filter` - Keep elements where block is truthy
- `reject` - Remove elements where block is truthy
- `compact` - Remove all `nil` values (no block needed)
- `uniq` - Remove duplicates (no block needed)

**Using predicate methods:**
Ruby's convention of ending boolean methods with `?` makes filtering very readable:
```ruby
numbers.select { |n| n.even? }      # Keep even numbers
numbers.reject { |n| n.zero? }       # Remove zeros
strings.select { |s| s.empty? }      # Keep empty strings
array.reject { |item| item.nil? }    # Remove nils
```

**Symbol to proc shorthand:**
For simple method calls, Ruby offers an elegant shorthand:
```ruby
# These are equivalent:
numbers.select { |n| n.even? }
numbers.select(&:even?)

values.reject { |v| v.nil? }
values.reject(&:nil?)
```
This `&:method_name` syntax is covered in detail in section 8.8.

**Best Practice:**
- Use `.select` when you think "keep items that match"
- Use `.reject` when you think "remove items that match"
- Use `.compact` specifically for removing nils
- Prefer the `&:method` shorthand for simple predicate checks

---

## 6.4 `.reduce` (`.inject`) vs `reduce()`

Both Python and Ruby have a `reduce` function/method for combining all elements of a collection into a single value. Python's `reduce()` is in the `functools` module, while Ruby's is a built-in Enumerable method called `.reduce` (aliased as `.inject`).

**Key Similarities:**
- Both accumulate values across a collection
- Both take an initial value and a combining function/block
- Both are used for sums, products, and custom aggregations

**Key Differences:**
- Ruby's reduce is a method, not a function
- Ruby can pass operators as symbols (`:+`, `:*`, etc.)
- Ruby's syntax is more flexible with or without initial values
- Ruby has convenience methods for common cases (`sum`, `max`, `min`)

### Python Example

```python
from functools import reduce

# Basic sum
numbers = [1, 2, 3, 4, 5]
total = reduce(lambda acc, n: acc + n, numbers)
# 15

# Sum with initial value
total = reduce(lambda acc, n: acc + n, numbers, 0)
# 15

# Product
product = reduce(lambda acc, n: acc * n, numbers)
# 120

# Finding maximum
maximum = reduce(lambda acc, n: acc if acc > n else n, numbers)
# 5

# More complex: building a hash/dict
items = [("a", 1), ("b", 2), ("c", 3)]
result = reduce(
    lambda acc, pair: {**acc, pair[0]: pair[1]},
    items,
    {}
)
# {'a': 1, 'b': 2, 'c': 3}

# Flattening a list
nested = [[1, 2], [3, 4], [5, 6]]
flattened = reduce(lambda acc, lst: acc + lst, nested, [])
# [1, 2, 3, 4, 5, 6]

# More readable: use sum() for numbers
total = sum(numbers)  # Preferred over reduce for simple sum
```

### Ruby Equivalent

```ruby
# Basic sum
numbers = [1, 2, 3, 4, 5]
total = numbers.reduce { |acc, n| acc + n }
# 15

# Sum with initial value
total = numbers.reduce(0) { |acc, n| acc + n }
# 15

# Sum using symbol (most idiomatic)
total = numbers.reduce(:+)
# 15

# Product
product = numbers.reduce(:*)
# 120

# Finding maximum (but max method is better)
maximum = numbers.reduce { |acc, n| acc > n ? acc : n }
# 5

# More complex: building a hash
items = [["a", 1], ["b", 2], ["c", 3]]
result = items.reduce({}) do |acc, (key, value)|
  acc[key] = value
  acc
end
# {"a"=>1, "b"=>2, "c"=>3}

# Or using to_h (more idiomatic for this case)
result = items.to_h
# {"a"=>1, "b"=>2, "c"=>3}

# Flattening a list (but flatten is better)
nested = [[1, 2], [3, 4], [5, 6]]
flattened = nested.reduce([]) { |acc, lst| acc + lst }
# [1, 2, 3, 4, 5, 6]

# Preferred methods for common operations
total = numbers.sum        # Better than reduce(:+)
maximum = numbers.max      # Better than reduce with comparison
product = numbers.reduce(1, :*)  # When you need product
```

### Explanation

Ruby's `.reduce` (also called `.inject`) accumulates a value by applying a block to each element. The accumulator is passed to the next iteration, building up the final result.

**Syntax variations:**
```ruby
# With initial value and block
numbers.reduce(0) { |acc, n| acc + n }

# Without initial value (first element becomes initial acc)
numbers.reduce { |acc, n| acc + n }

# With symbol (for operators and methods)
numbers.reduce(:+)
numbers.reduce(:*)

# With initial value and symbol
numbers.reduce(1, :*)
```

**How it works:**
```ruby
[1, 2, 3, 4].reduce(0) { |acc, n| acc + n }

# Iteration 1: acc = 0, n = 1, returns 0 + 1 = 1
# Iteration 2: acc = 1, n = 2, returns 1 + 2 = 3
# Iteration 3: acc = 3, n = 3, returns 3 + 3 = 6
# Iteration 4: acc = 6, n = 4, returns 6 + 4 = 10
# Final result: 10
```

**Common use cases:**
```ruby
# Sum
numbers.sum                    # Preferred
numbers.reduce(:+)             # Alternative

# Product
numbers.reduce(1, :*)

# Building hashes
pairs.reduce({}) { |h, (k, v)| h.merge(k => v) }

# Counting occurrences
words.reduce(Hash.new(0)) { |counts, word|
  counts[word] += 1
  counts
}

# Joining strings
words.reduce { |acc, word| "#{acc}, #{word}" }
# But prefer: words.join(", ")
```

**Best Practice:**
- Use `.reduce` with a symbol for simple operations: `.reduce(:+)`
- Use specific methods when available: `sum`, `max`, `min`, `join`
- The `inject` alias is less common; prefer `reduce`
- Always remember to return the accumulator in your block
- Use descriptive variable names: `sum`, `product`, `hash` instead of `acc`

---

## 6.5 `.each_with_index` vs `enumerate()`

Python's `enumerate()` function adds an index counter to an iteration. Ruby provides `.each_with_index` for the same purpose, along with several related methods for indexed iteration.

**Key Similarities:**
- Both provide index alongside the element
- Both start at index 0 by default
- Both work with any iterable/enumerable

**Key Differences:**
- Python's `enumerate()` can start at any number
- Ruby has multiple methods for different iteration patterns
- Ruby's approach integrates with other Enumerable methods
- Ruby provides `.with_index` that can be chained with any iterator

### Python Example

```python
# Basic enumeration
fruits = ["apple", "banana", "cherry"]
for index, fruit in enumerate(fruits):
    print(f"{index}: {fruit}")
# 0: apple
# 1: banana
# 2: cherry

# Starting at a different number
for index, fruit in enumerate(fruits, start=1):
    print(f"{index}: {fruit}")
# 1: apple
# 2: banana
# 3: cherry

# Using enumerate with list comprehension
indexed = [(i, fruit) for i, fruit in enumerate(fruits)]
# [(0, 'apple'), (1, 'banana'), (2, 'cherry')]

# Enumerate with filtering
fruits = ["apple", "banana", "cherry", "date"]
long_fruits = [(i, f) for i, f in enumerate(fruits) if len(f) > 5]
# [(1, 'banana'), (2, 'cherry')]

# Enumerate over dictionary values
prices = {"apple": 0.5, "banana": 0.3}
for index, (key, value) in enumerate(prices.items()):
    print(f"{index}: {key} costs ${value}")
```

### Ruby Equivalent

```ruby
# Basic enumeration with each_with_index
fruits = ["apple", "banana", "cherry"]
fruits.each_with_index do |fruit, index|
  puts "#{index}: #{fruit}"
end
# 0: apple
# 1: banana
# 2: cherry

# Starting at a different number (use with_index)
fruits.each.with_index(1) do |fruit, index|
  puts "#{index}: #{fruit}"
end
# 1: apple
# 2: banana
# 3: cherry

# Creating an array of index-value pairs
indexed = fruits.map.with_index { |fruit, i| [i, fruit] }
# [[0, "apple"], [1, "banana"], [2, "cherry"]]

# Enumerate with filtering
fruits = ["apple", "banana", "cherry", "date"]
long_fruits = fruits.each_with_index.select { |f, i| f.length > 5 }
                    .map { |f, i| [i, f] }
# [[1, "banana"], [2, "cherry"]]

# Or more simply:
long_fruits = fruits.filter_map.with_index { |f, i| [i, f] if f.length > 5 }
# [[1, "banana"], [2, "cherry"]]

# Enumerate over hash entries
prices = { apple: 0.5, banana: 0.3 }
prices.each_with_index do |(key, value), index|
  puts "#{index}: #{key} costs $#{value}"
end
```

### Explanation

Ruby provides several ways to iterate with indices, each suited for different use cases:

**Primary methods:**
- `each_with_index` - Iterate with index, starting at 0
- `each.with_index(n)` - Iterate with index, starting at n
- `map.with_index` - Transform elements with access to index
- `select.with_index` - Filter elements with access to index

**How `.with_index` works:**
The `.with_index` method can be chained after any enumerator method:
```ruby
# After each
array.each.with_index(1) { |item, i| ... }

# After map
array.map.with_index { |item, i| ... }

# After select
array.select.with_index { |item, i| ... }
```

**Useful patterns:**
```ruby
# Find index of first match
fruits.each_with_index do |fruit, index|
  if fruit == "banana"
    puts "Found at index #{index}"
    break
  end
end

# Or more idiomatically:
index = fruits.index("banana")
# 1

# Build a hash with indices as keys
hash = fruits.each_with_index.to_h.invert
# {0=>"apple", 1=>"banana", 2=>"cherry"}

# Modify elements based on index
fruits.map.with_index do |fruit, i|
  i.even? ? fruit.upcase : fruit
end
# ["APPLE", "banana", "CHERRY"]
```

**Block parameter order:**
Notice that `.each_with_index` puts the element first, index second:
```ruby
fruits.each_with_index do |fruit, index|
  # fruit is the element, index is the position
end
```

This matches Ruby's convention where the main value comes first.

**Best Practice:**
- Use `.each_with_index` when you need the index in an iteration
- Use `.with_index(n)` when you need a custom starting index
- Use `.index(value)` or `.find_index { |x| ... }` when you just need to find an index
- Chain `.with_index` with `map`, `select`, etc. for indexed transformations

---

## 6.6 `.zip`

Both Python and Ruby provide a `zip` function/method to combine multiple collections element-by-element. The concept is identical, but the syntax and some behaviors differ slightly.

**Key Similarities:**
- Both combine multiple sequences element-wise
- Both create tuples/arrays of corresponding elements
- Both stop at the shortest sequence

**Key Differences:**
- Python's `zip()` is a built-in function; Ruby's is a method
- Python's `zip()` returns an iterator; Ruby's returns an array
- Ruby's `.zip` is called on the first array, takes others as arguments
- Ruby can zip with a block for immediate processing

### Python Example

```python
# Basic zip
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
people = list(zip(names, ages))
# [('Alice', 25), ('Bob', 30), ('Charlie', 35)]

# Zip three lists
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
cities = ["NYC", "LA", "Chicago"]
people = list(zip(names, ages, cities))
# [('Alice', 25, 'NYC'), ('Bob', 30, 'LA'), ('Charlie', 35, 'Chicago')]

# Unequal lengths (stops at shortest)
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30]
people = list(zip(names, ages))
# [('Alice', 25), ('Bob', 30)]

# Unzipping (using zip with *)
pairs = [('Alice', 25), ('Bob', 30), ('Charlie', 35)]
names, ages = zip(*pairs)
# names = ('Alice', 'Bob', 'Charlie')
# ages = (25, 30, 35)

# Creating a dictionary from two lists
keys = ["name", "age", "city"]
values = ["Alice", 25, "NYC"]
person = dict(zip(keys, values))
# {'name': 'Alice', 'age': 25, 'city': 'NYC'}

# Using enumerate and zip together
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
for index, (name, age) in enumerate(zip(names, ages)):
    print(f"{index}: {name} is {age}")
```

### Ruby Equivalent

```ruby
# Basic zip
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
people = names.zip(ages)
# [["Alice", 25], ["Bob", 30], ["Charlie", 35]]

# Zip three arrays
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
cities = ["NYC", "LA", "Chicago"]
people = names.zip(ages, cities)
# [["Alice", 25, "NYC"], ["Bob", 30, "LA"], ["Charlie", 35, "Chicago"]]

# Unequal lengths (fills with nil for missing elements)
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30]
people = names.zip(ages)
# [["Alice", 25], ["Bob", 30], ["Charlie", nil]]

# Unzipping (using transpose)
pairs = [["Alice", 25], ["Bob", 30], ["Charlie", 35]]
names, ages = pairs.transpose
# names = ["Alice", "Bob", "Charlie"]
# ages = [25, 30, 35]

# Creating a hash from two arrays
keys = ["name", "age", "city"]
values = ["Alice", 25, "NYC"]
person = keys.zip(values).to_h
# {:name=>"Alice", :age=>25, :city=>"NYC"}

# Or more directly:
person = Hash[keys.zip(values)]
# {:name=>"Alice", :age=>25, :city=>"NYC"}

# Using with_index and zip together
names = ["Alice", "Bob", "Charlie"]
ages = [25, 30, 35]
names.zip(ages).each_with_index do |(name, age), index|
  puts "#{index}: #{name} is #{age}"
end
```

### Explanation

Ruby's `.zip` method is called on an array and takes one or more arrays as arguments. It returns a new array where each element is an array of the corresponding elements from all the input arrays.

**Important difference from Python:**
Ruby's `.zip` pads with `nil` if arrays are different lengths, while Python's `zip()` stops at the shortest:
```ruby
# Ruby
[1, 2, 3].zip([4, 5])
# [[1, 4], [2, 5], [3, nil]]

# Python
list(zip([1, 2, 3], [4, 5]))
# [(1, 4), (2, 5)]
```

**Using zip with blocks:**
Ruby allows you to pass a block to `.zip` for immediate processing:
```ruby
names = ["Alice", "Bob"]
ages = [25, 30]

names.zip(ages) do |name, age|
  puts "#{name} is #{age} years old"
end
# Alice is 25 years old
# Bob is 30 years old
```

**Common patterns:**
```ruby
# Create hash from keys and values
keys.zip(values).to_h

# Combine and transform
names.zip(ages).map { |name, age| "#{name}: #{age}" }
# ["Alice: 25", "Bob: 30", "Charlie: 35"]

# Parallel iteration (when you need zip-like behavior)
names.zip(ages, cities).each do |name, age, city|
  puts "#{name}, #{age}, lives in #{city}"
end

# Transpose (2D array rotation)
matrix = [[1, 2, 3], [4, 5, 6]]
transposed = matrix.transpose
# [[1, 4], [2, 5], [3, 6]]
```

**Best Practice:**
- Use `.zip` when combining parallel arrays
- Remember Ruby pads with `nil` for unequal lengths
- Use `.transpose` to unzip (opposite of `.zip`)
- Call `.to_h` on zipped arrays to create hashes
- Consider using `Hash[array1.zip(array2)]` for hash creation

---

## 6.7 Method Chaining

Method chaining is the practice of calling multiple methods in sequence on the same object or the result of the previous method call. While both Python and Ruby support this, Ruby's design makes chaining far more natural and idiomatic.

**Key Similarities:**
- Both languages allow chaining methods that return objects
- Both benefit from improved readability when chaining appropriately

**Key Differences:**
- Ruby's Enumerable methods are designed for chaining
- Ruby methods conventionally return `self` or new objects to enable chaining
- Python has fewer built-in chainable methods
- Ruby developers chain more extensively and idiomatically

### Python Example

```python
# Python's list methods often return None, breaking chains
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# This doesn't work (sort returns None):
# result = numbers.sort().reverse()  # Error!

# You have to do separate steps:
numbers.sort()
numbers.reverse()

# Or use sorted() which returns a new list:
result = sorted(numbers, reverse=True)

# Some limited chaining with string methods:
text = "  hello world  "
cleaned = text.strip().upper().replace("WORLD", "PYTHON")
# "HELLO PYTHON"

# Pandas is an exception - designed for chaining:
import pandas as pd
df = pd.DataFrame({'A': [1, 2, 3], 'B': [4, 5, 6]})
result = (df
    .query('A > 1')
    .assign(C=lambda x: x.A + x.B)
    .sort_values('C'))

# Functools and itertools require nested calls or variables:
from functools import reduce
from itertools import islice

numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
# Nested (hard to read):
result = reduce(
    lambda x, y: x + y,
    map(lambda x: x ** 2,
        filter(lambda x: x % 2 == 0, numbers))
)

# Or multiple variables (verbose):
evens = filter(lambda x: x % 2 == 0, numbers)
squared = map(lambda x: x ** 2, evens)
result = reduce(lambda x, y: x + y, squared)
```

### Ruby Equivalent

```ruby
# Ruby's enumerable methods return new arrays, enabling chaining
numbers = [3, 1, 4, 1, 5, 9, 2, 6]

# Beautiful chaining:
result = numbers.sort.reverse
# [9, 6, 5, 4, 3, 2, 1, 1]

# String method chaining (similar to Python):
text = "  hello world  "
cleaned = text.strip.upcase.gsub("WORLD", "RUBY")
# "HELLO RUBY"

# Complex enumerable chaining:
numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
result = numbers
  .select { |x| x.even? }
  .map { |x| x ** 2 }
  .reduce(:+)
# 220 (4 + 16 + 36 + 64 + 100)

# Real-world example: data processing pipeline
users = [
  { name: "Alice", age: 30, active: true },
  { name: "Bob", age: 25, active: false },
  { name: "Charlie", age: 35, active: true },
  { name: "David", age: 28, active: true }
]

active_user_names = users
  .select { |u| u[:active] }
  .sort_by { |u| u[:age] }
  .map { |u| u[:name] }
  .join(", ")
# "Alice, David, Charlie"

# Chaining with formatting (multi-line for readability):
result = [1, 2, 3, 4, 5]
  .map { |n| n * 2 }
  .select { |n| n > 5 }
  .reduce(:+)
# 24

# Custom objects can return self for chaining:
class Query
  def initialize
    @conditions = []
  end

  def where(condition)
    @conditions << condition
    self  # Return self to enable chaining
  end

  def order(field)
    @order = field
    self
  end

  def limit(count)
    @limit = count
    self
  end
end

# Usage:
query = Query.new
  .where("age > 18")
  .where("active = true")
  .order("name")
  .limit(10)
```

### Explanation

Ruby's enumerable methods are designed with chaining in mind. Most methods return a new object (or `self`), allowing you to immediately call another method on the result.

**Why Ruby chains better:**

1. **Enumerable methods return useful values:**
```ruby
# Every step returns something you can chain
[1, 2, 3]
  .map { |n| n * 2 }      # Returns [2, 4, 6]
  .select { |n| n > 3 }   # Returns [4, 6]
  .sum                     # Returns 10
```

2. **Convention to return `self` for mutations:**
```ruby
# Methods that modify the object often return self
string = "hello"
  .concat(" world")  # Returns the modified string
  .upcase            # Returns the modified string
  .strip
# "HELLO WORLD"
```

3. **Multi-line chaining is readable:**
```ruby
# Ruby developers format chains vertically:
result = collection
  .method_one
  .method_two { |x| ... }
  .method_three
  .method_four

# Leading dots are a style choice (widely accepted)
```

**Common chaining patterns:**
```ruby
# Filter, transform, aggregate
numbers
  .select(&:even?)
  .map { |n| n ** 2 }
  .sum

# Sort and take
users
  .sort_by { |u| u[:score] }
  .reverse
  .first(5)

# Group and transform
words
  .group_by(&:length)
  .transform_values(&:count)

# Flatten and unique
arrays
  .flatten
  .uniq
  .sort
```

**Best Practice:**
- Chain methods when it improves readability
- Use multi-line formatting for long chains (one method per line)
- Start each line with a dot (leading dot style)
- Avoid overly long chains (split complex logic into steps)
- Ensure each step returns something meaningful
- Use descriptive intermediate variables if chain becomes unclear

**When not to chain:**
```ruby
# Too complex - hard to debug:
result = data.map { |x| x.split }.flatten.group_by { |w| w[0] }
  .transform_values { |v| v.uniq.sort }.select { |k, v| v.length > 2 }
  .map { |k, v| "#{k}: #{v.join(', ')}" }.join("\n")

# Better - intermediate steps:
words = data.flat_map { |x| x.split }
by_first_letter = words.group_by { |w| w[0] }
unique_sorted = by_first_letter.transform_values { |v| v.uniq.sort }
filtered = unique_sorted.select { |k, v| v.length > 2 }
result = filtered.map { |k, v| "#{k}: #{v.join(', ')}" }.join("\n")
```

---

## 6.8 Lazy Enumerables

Ruby's lazy enumerables allow you to chain enumerable operations without creating intermediate arrays. This is similar to Python's generators and iterators, where operations are evaluated on-demand rather than eagerly.

**Key Similarities:**
- Both avoid creating intermediate collections
- Both enable working with infinite sequences
- Both evaluate elements only when needed
- Both improve memory efficiency for large datasets

**Key Differences:**
- Python uses generators by default (range, map, filter)
- Ruby requires explicit `.lazy` to enable lazy evaluation
- Ruby's lazy chains maintain the same enumerable interface
- Python's approach is more implicit; Ruby's is explicit

### Python Example

```python
# Python's range, map, filter are lazy by default (Python 3)
numbers = range(1, 1000000)  # Doesn't create array
evens = filter(lambda x: x % 2 == 0, numbers)  # Lazy
squared = map(lambda x: x ** 2, evens)  # Lazy

# Only computes when you iterate or convert to list
first_five = list(islice(squared, 5))
# [4, 16, 36, 64, 100]

# Generator expressions (lazy list comprehensions)
# Eager (list comprehension):
squares = [x ** 2 for x in range(1000000)]  # Creates full list

# Lazy (generator expression):
squares = (x ** 2 for x in range(1000000))  # Creates generator
first_five = [next(squares) for _ in range(5)]
# [0, 1, 4, 9, 16]

# Infinite sequences with itertools
from itertools import count, islice

# Infinite counter
naturals = count(1)
# Only takes what you need:
first_ten = list(islice(naturals, 10))
# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Chaining lazy operations
from itertools import islice, count

result = islice(
    map(lambda x: x ** 2,
        filter(lambda x: x % 2 == 0,
               count(1))),
    5
)
list(result)
# [4, 16, 36, 64, 100]

# Custom generator function
def fibonacci():
    a, b = 0, 1
    while True:
        yield a
        a, b = b, a + b

fibs = fibonacci()
first_ten = [next(fibs) for _ in range(10)]
# [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]
```

### Ruby Equivalent

```ruby
# Ruby arrays are eager by default
numbers = (1..1000000).to_a  # Creates full array
evens = numbers.select { |x| x.even? }  # Creates new array
squared = evens.map { |x| x ** 2 }  # Creates another array

# Using .lazy to avoid intermediate arrays
numbers = (1..1000000)
result = numbers
  .lazy
  .select { |x| x.even? }
  .map { |x| x ** 2 }
  .first(5)
# [4, 16, 36, 64, 100]

# The lazy chain doesn't create arrays until .first(5)

# Infinite sequences with Enumerator
# Infinite counter (similar to Python's count)
naturals = (1..Float::INFINITY)

# Only takes what you need:
first_ten = naturals.lazy.first(10)
# [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

# Chaining lazy operations (readable!)
result = (1..Float::INFINITY)
  .lazy
  .select { |x| x.even? }
  .map { |x| x ** 2 }
  .first(5)
# [4, 16, 36, 64, 100]

# Custom infinite enumerator
fibonacci = Enumerator.new do |yielder|
  a, b = 0, 1
  loop do
    yielder << a
    a, b = b, a + b
  end
end

first_ten = fibonacci.lazy.first(10)
# [0, 1, 1, 2, 3, 5, 8, 13, 21, 34]

# Real-world example: processing large files
File.foreach("large_file.txt")
  .lazy
  .select { |line| line.include?("ERROR") }
  .map { |line| line.strip.upcase }
  .first(10)
# Only reads as many lines as needed to find 10 errors
```

### Explanation

Ruby's `.lazy` method converts an enumerable into a lazy enumerator. All subsequent chained methods will be evaluated lazily (on-demand) rather than eagerly creating intermediate arrays.

**When to use `.lazy`:**
- Working with large datasets
- Processing infinite sequences
- Chaining many enumerable operations
- When you only need a subset of results
- Reading large files line-by-line

**How it works:**
```ruby
# Eager (creates 3 intermediate arrays):
(1..1000000).to_a
  .select { |x| x.even? }      # Array of 500k elements
  .map { |x| x ** 2 }           # Array of 500k elements
  .first(5)                     # Takes 5 elements

# Lazy (no intermediate arrays):
(1..1000000)
  .lazy                         # Convert to lazy enumerator
  .select { |x| x.even? }       # Lazy operation
  .map { |x| x ** 2 }           # Lazy operation
  .first(5)                     # Triggers evaluation, stops after 5
```

**Creating infinite sequences:**
```ruby
# Using ranges
(1..Float::INFINITY).lazy

# Using Enumerator.new with loop
powers_of_two = Enumerator.new do |y|
  n = 1
  loop do
    y << n
    n *= 2
  end
end

powers_of_two.lazy.first(10)
# [1, 2, 4, 8, 16, 32, 64, 128, 256, 512]

# Using cycle for repeating sequences
%w[red green blue].cycle.lazy.first(7)
# ["red", "green", "blue", "red", "green", "blue", "red"]
```

**Performance example:**
```ruby
require 'benchmark'

# Eager evaluation
Benchmark.measure do
  (1..1000000)
    .select { |x| x.even? }
    .map { |x| x ** 2 }
    .first(5)
end
# Creates 2 arrays of 500k elements each

# Lazy evaluation
Benchmark.measure do
  (1..1000000)
    .lazy
    .select { |x| x.even? }
    .map { |x| x ** 2 }
    .first(5)
end
# Stops after finding 5 matching elements
```

**Materializing lazy enumerables:**
```ruby
lazy_enum = (1..10).lazy.map { |x| x * 2 }

# Force evaluation with:
lazy_enum.to_a        # Convert to array
lazy_enum.first(5)    # Take first n elements
lazy_enum.each { }    # Iterate through all
lazy_enum.force       # Same as to_a
```

**Best Practice:**
- Use `.lazy` for large or infinite collections
- Don't use `.lazy` for small collections (overhead not worth it)
- Call `.lazy` early in the chain, before other operations
- End with a method that triggers evaluation (`.first`, `.take`, `.to_a`)
- Use `Enumerator.new` for custom infinite sequences
- Remember that `.lazy` returns a lazy enumerator, not an array

---

This concludes Chapter 6 on Enumerables and Functional Programming. You've now learned how Ruby's powerful Enumerable module provides a rich set of methods for data transformation and collection processing. These methods form the backbone of idiomatic Ruby code and will be used extensively in real-world applications.

In the next chapter, we'll explore methods in Ruby, including how to define them, argument handling, and Ruby's unique method naming conventions.
