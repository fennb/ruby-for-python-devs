# Chapter 2: Basic Syntax and Data Types

## 2.1 Variables and Assignment

Variables work similarly in both Python and Ruby, but there are some important differences in syntax and conventions.

### Python Variable Assignment

```python
# Simple assignment
name = "Alice"
age = 30
price = 19.99

# Multiple assignment
x, y = 10, 20

# Swapping
a, b = b, a

# Chain assignment
x = y = z = 0
```

### Ruby Variable Assignment

```ruby
# Simple assignment
name = "Alice"
age = 30
price = 19.99

# Multiple assignment (parallel assignment)
x, y = 10, 20

# Swapping
a, b = b, a

# Chain assignment
x = y = z = 0
```

### Key Similarities

- Assignment uses `=` in both languages
- Multiple/parallel assignment works the same way
- Variables don't need type declarations (both are dynamically typed)
- Variable names are case-sensitive

### Key Differences

**No Declaration Keywords:**

Python:
```python
name = "Alice"  # No 'var', 'let', or 'const' needed
```

Ruby:
```ruby
name = "Alice"  # No 'var', 'let', or 'const' needed
```

Both languages are the same here—no declaration keywords needed. However, Ruby has **different variable scopes indicated by prefixes**:

```ruby
name = "Alice"        # Local variable
@name = "Alice"       # Instance variable (we'll cover this in Chapter 9)
@@name = "Alice"      # Class variable (Chapter 9)
$name = "Alice"       # Global variable (avoid these!)
NAME = "Alice"        # Constant (by convention, shouldn't change)
```

Python uses different mechanisms for these concepts (like `self.name` for instance variables).

**Constants:**

Python:
```python
# Convention: ALL_CAPS for constants (not enforced)
MAX_SIZE = 100
MAX_SIZE = 200  # No warning or error
```

Ruby:
```ruby
# Convention: ALL_CAPS or CapitalCase for constants
MAX_SIZE = 100
MAX_SIZE = 200  # Warning: "already initialized constant MAX_SIZE"
```

Ruby actually warns you when reassigning constants (though it doesn't prevent it).

### Variable Scope Indicators

This is important and unique to Ruby. The first character of a variable name indicates its scope:

| Prefix | Scope | Example |
|--------|-------|---------|
| lowercase or `_` | Local variable | `name`, `_temp` |
| `@` | Instance variable | `@name` |
| `@@` | Class variable | `@@count` |
| `$` | Global variable | `$debug_mode` |
| UPPERCASE | Constant | `MAX_SIZE` |

**Python Comparison:**

Python doesn't use prefixes; scope is determined by where the variable is defined:

```python
class Person:
    species = "Human"          # Class variable

    def __init__(self, name):
        self.name = name       # Instance variable

def greet():
    message = "Hello"          # Local variable

global_var = "accessible everywhere"  # Module-level variable
```

**Ruby Equivalent:**

```ruby
class Person
  @@species = "Human"          # Class variable

  def initialize(name)
    @name = name               # Instance variable
  end
end

def greet
  message = "Hello"            # Local variable
end

$global_var = "accessible everywhere"  # Global variable (rare in Ruby)
```

### Undefined Variables

Both languages raise errors for undefined variables:

**Python:**
```python
print(undefined_var)
# NameError: name 'undefined_var' is not defined
```

**Ruby:**
```ruby
puts undefined_var
# NameError: undefined local variable or method `undefined_var'
```

Notice Ruby's error message mentions "or method"—this is because Ruby tries to call a method if a variable isn't found. More on this in Chapter 7.

### Parallel Assignment with Fewer Variables

**Python:**
```python
a, b = [1, 2, 3]  # ValueError: too many values to unpack
a, *rest = [1, 2, 3]  # a=1, rest=[2, 3]
```

**Ruby:**
```ruby
a, b = [1, 2, 3]  # a=1, b=2, 3 is ignored
a, *rest = [1, 2, 3]  # a=1, rest=[2, 3]
```

Ruby is more forgiving—extra values are simply ignored unless you use the splat operator (`*`).

---

## 2.2 Naming Conventions (snake_case everywhere)

Both Python and Ruby favor `snake_case`, but Ruby uses it even more consistently.

### Python Naming Conventions (PEP 8)

```python
# Variables and functions: snake_case
user_name = "Alice"
def calculate_total():
    pass

# Classes: PascalCase
class UserAccount:
    pass

# Constants: SCREAMING_SNAKE_CASE
MAX_CONNECTIONS = 100

# "Private" (by convention): leading underscore
_internal_value = 42
def _private_method():
    pass

# "Magic" methods: double underscores
def __init__(self):
    pass
```

### Ruby Naming Conventions

```ruby
# Variables and methods: snake_case
user_name = "Alice"
def calculate_total
  # code
end

# Classes and modules: PascalCase
class UserAccount
end

# Constants: SCREAMING_SNAKE_CASE or PascalCase
MAX_CONNECTIONS = 100
DatabaseConfig = {}  # Also acceptable for constants

# "Private" (convention): leading underscore (less common)
_internal_value = 42

# Predicate methods: ending with ?
def empty?
  # returns true or false
end

# Dangerous/mutating methods: ending with !
def upcase!
  # modifies the object in place
end

# Setter methods: ending with =
def name=(value)
  @name = value
end
```

### Key Differences

**Method Names:**

Ruby allows special characters in method names that Python doesn't:

```ruby
# Question mark for predicate methods (return true/false)
[].empty?        # true
"hello".include?("e")  # true

# Exclamation mark for "dangerous" methods (mutate or have side effects)
name = "hello"
name.upcase      # Returns "HELLO", doesn't change name
name.upcase!     # Changes name to "HELLO"

# Equals sign for setter methods
person.name = "Alice"  # Calls person.name=(value)
```

Python doesn't allow these characters in identifiers:

```python
# Python equivalents use different conventions
[].is_empty()     # Would be a method name (but actually len([]) == 0)
"hello".count("e")

# Python doesn't have a naming convention for mutating vs non-mutating
name = "hello"
name.upper()      # Returns "HELLO", doesn't change name
# No equivalent to upcase! - you'd reassign: name = name.upper()
```

**Screaming Snake Case:**

Both use `SCREAMING_SNAKE_CASE` for constants, but Ruby enforces it more:

```python
# Python
MAX_SIZE = 100  # Convention only
```

```ruby
# Ruby
MAX_SIZE = 100  # Triggers warning if reassigned
```

### Boolean Naming

**Python:**
```python
# Use is_, has_, or can_ prefix
is_valid = True
has_permission = False
can_edit = True
```

**Ruby:**
```ruby
# Use ? suffix (idiomatic Ruby)
valid? = true        # Syntax error! Can't use ? in variable names
# Instead, use adjectives for variables:
valid = true

# But methods use ?:
def valid?
  true
end
```

**Important:** The `?` suffix is **only for method names**, not variable names. For boolean variables, use adjectives:

```ruby
# Variables
valid = true
empty = false
finished = true

# Methods
def valid?
  @errors.empty?
end
```

### Summary Table

| Category | Python | Ruby |
|----------|--------|------|
| Variables/functions | snake_case | snake_case |
| Classes | PascalCase | PascalCase |
| Constants | SCREAMING_SNAKE_CASE | SCREAMING_SNAKE_CASE or PascalCase |
| Predicate methods | `is_empty()` | `empty?` |
| Mutating methods | No convention | `upcase!` |
| Setter methods | Property decorators | `name=` |

---

## 2.3 Numbers (Integer, Float)

Numbers work very similarly in Python and Ruby, with a few minor differences.

### Python Numbers

```python
# Integers
count = 42
large_number = 1_000_000  # Underscores for readability (Python 3.6+)
hex_value = 0xFF
binary_value = 0b1010

# Floats
price = 19.99
scientific = 1.5e-4

# Arithmetic
print(10 + 5)   # 15
print(10 - 5)   # 5
print(10 * 5)   # 50
print(10 / 5)   # 2.0 (float division in Python 3)
print(10 // 5)  # 2 (integer division)
print(10 % 3)   # 1 (modulo)
print(10 ** 3)  # 1000 (exponentiation)

# Type conversion
int(3.14)       # 3
float(42)       # 42.0
str(100)        # "100"
```

### Ruby Numbers

```ruby
# Integers
count = 42
large_number = 1_000_000  # Underscores for readability
hex_value = 0xFF
binary_value = 0b1010

# Floats
price = 19.99
scientific = 1.5e-4

# Arithmetic
puts 10 + 5    # 15
puts 10 - 5    # 5
puts 10 * 5    # 50
puts 10 / 5    # 2 (integer division if both are integers!)
puts 10.0 / 5  # 2.0 (float division if either is float)
puts 10 % 3    # 1 (modulo)
puts 10 ** 3   # 1000 (exponentiation)

# Type conversion
3.14.to_i      # 3
42.to_f        # 42.0
100.to_s       # "100"
```

### Key Differences

**Division Behavior:**

Python 3 (always returns float):
```python
10 / 5   # 2.0
10 / 3   # 3.3333333333333335
10 // 3  # 3 (use // for integer division)
```

Ruby (integer division if both operands are integers):
```ruby
10 / 5   # 2 (both integers → integer result)
10 / 3   # 3 (integer division, not 3.333...)
10.0 / 3 # 3.3333333333333335 (float division)
10 / 3.0 # 3.3333333333333335
```

**Watch out:** This is a common gotcha for Python developers. In Ruby, use at least one float to get float division:

```ruby
# Wrong (if you want 3.333...)
result = 10 / 3  # 3

# Right
result = 10.0 / 3  # 3.3333333333333335
result = 10 / 3.0  # 3.3333333333333335
result = 10.to_f / 3  # 3.3333333333333335
```

**Type Conversion Syntax:**

Python uses functions:
```python
int(value)
float(value)
str(value)
```

Ruby uses methods (everything is an object!):
```ruby
value.to_i    # to integer
value.to_f    # to float
value.to_s    # to string
```

### Numeric Methods

Both integers and floats are objects in both languages, but Ruby makes this more explicit:

**Python:**
```python
abs(-10)           # 10
round(3.14159, 2)  # 3.14
max(1, 2, 3)       # 3
```

**Ruby:**
```ruby
-10.abs            # 10
3.14159.round(2)   # 3.14
[1, 2, 3].max      # 3
```

Ruby's object-oriented approach means methods are called directly on numbers:

```ruby
# Ruby: numbers are objects with methods
10.even?           # true
7.odd?             # true
-5.abs             # 5
3.14.floor         # 3
3.14.ceil          # 4
10.next            # 11
5.times { puts "Hello" }  # Prints "Hello" 5 times
```

**Python equivalent (different syntax):**

```python
10 % 2 == 0        # True (even check)
7 % 2 == 1         # True (odd check)
abs(-5)            # 5
import math
math.floor(3.14)   # 3
math.ceil(3.14)    # 4
10 + 1             # 11 (no .next() method)
for _ in range(5):
    print("Hello")
```

### Numeric Comparisons

Comparisons work identically:

```python
# Python
10 > 5      # True
10 >= 10    # True
10 == 10    # True
10 != 5     # True
```

```ruby
# Ruby
10 > 5      # true
10 >= 10    # true
10 == 10    # true
10 != 5     # true
```

### Infinity and NaN

**Python:**
```python
import math
infinity = float('inf')
neg_infinity = float('-inf')
not_a_number = float('nan')

math.isinf(infinity)  # True
math.isnan(not_a_number)  # True
```

**Ruby:**
```ruby
infinity = Float::INFINITY
neg_infinity = -Float::INFINITY
not_a_number = Float::NAN

infinity.infinite?      # 1
neg_infinity.infinite?  # -1
not_a_number.nan?       # true
```

### Rational and Complex Numbers

Both languages support these, but with different syntax:

**Python:**
```python
from fractions import Fraction
from complex import complex

rational = Fraction(1, 3)
complex_num = 3 + 4j
```

**Ruby:**
```ruby
require 'rational'  # Not needed in Ruby 1.9+
require 'complex'   # Not needed in Ruby 1.9+

rational = Rational(1, 3)  # or 1r/3
complex_num = Complex(3, 4)  # or 3+4i
```

---

## 2.4 Strings and String Interpolation

Strings in Ruby are similar to Python strings, but Ruby offers more elegant string interpolation and has important differences in quote behavior.

### Python Strings

```python
name = "Alice"
age = 30

# Old-style formatting
greeting = "Hello, %s! You are %d years old." % (name, age)

# str.format()
greeting = "Hello, {}! You are {} years old.".format(name, age)
greeting = "Hello, {name}! You are {age} years old.".format(name=name, age=age)

# f-strings (Python 3.6+, most common)
greeting = f"Hello, {name}! You are {age} years old."

# Expression in f-string
greeting = f"Hello, {name.upper()}! Next year you'll be {age + 1}."

# Single vs double quotes (no difference in Python)
single = 'Hello'
double = "Hello"
```

### Ruby Strings

```ruby
name = "Alice"
age = 30

# String interpolation (only works with double quotes!)
greeting = "Hello, #{name}! You are #{age} years old."

# Expression in interpolation
greeting = "Hello, #{name.upcase}! Next year you'll be #{age + 1}."

# Single quotes - NO interpolation!
literal = 'Hello, #{name}!'  # Outputs: Hello, #{name}!

# Double quotes - interpolation and escape sequences
double = "Line 1\nLine 2"    # Newline works

# Single quotes - almost everything is literal
single = 'Line 1\nLine 2'    # Outputs: Line 1\nLine 2 (literal \n)
```

### Key Differences

**String Interpolation:**

- **Python**: Requires `f` prefix for f-strings
- **Ruby**: Interpolation is always available in double-quoted strings, using `#{}`

**Quote Behavior:**

Python:
```python
'hello' == "hello"  # True - no difference between single and double quotes
'Line 1\nLine 2'    # Escape sequences work in both
```

Ruby:
```ruby
'hello' == "hello"  # true - both create strings, but...
"Value: #{x}"       # Interpolation works
'Value: #{x}'       # Literal text: "Value: #{x}"

"Line 1\nLine 2"    # Escape sequences work
'Line 1\nLine 2'    # Literal: "Line 1\\nLine 2" (only \\ and \' are escaped)
```

**Important Ruby Rule:**
- **Double quotes (`"`)**: Allow interpolation and all escape sequences
- **Single quotes (`'`)**: Print almost everything literally (only `\\` and `\'` are escaped)

### String Methods

Both languages have rich string APIs:

**Python:**
```python
text = "Hello, World!"

text.upper()           # "HELLO, WORLD!"
text.lower()           # "hello, world!"
text.strip()           # "Hello, World!" (removes whitespace)
text.replace("World", "Ruby")  # "Hello, Ruby!"
text.split(",")        # ["Hello", " World!"]
"".join(["a", "b"])    # "ab"
text.startswith("He")  # True
text.endswith("!")     # True
len(text)              # 13
```

**Ruby:**
```ruby
text = "Hello, World!"

text.upcase            # "HELLO, WORLD!"
text.downcase          # "hello, world!"
text.strip             # "Hello, World!" (removes whitespace)
text.gsub("World", "Ruby")  # "Hello, Ruby!" (gsub = global substitute)
text.split(",")        # ["Hello", " World!"]
["a", "b"].join        # "ab"
text.start_with?("He") # true
text.end_with?("!")    # true
text.length            # 13
text.size              # 13 (alias for length)
```

**Key Differences:**
- Ruby: `upcase` / `downcase` (vs Python: `upper()` / `lower()`)
- Ruby: `gsub` (global substitute) vs Python: `replace`
- Ruby: `start_with?` / `end_with?` (note the `?`) vs Python: `startswith` / `endswith`
- Ruby: `.length` or `.size` vs Python: `len()`

### Mutating vs Non-Mutating Methods

**Python strings are immutable:**
```python
text = "hello"
text.upper()  # Returns "HELLO", doesn't change text
print(text)   # Still "hello"
```

**Ruby strings are mutable, and has both versions:**
```ruby
text = "hello"

# Non-mutating (returns new string)
text.upcase   # Returns "HELLO", doesn't change text
puts text     # Still "hello"

# Mutating (modifies in place, note the !)
text.upcase!  # Changes text to "HELLO"
puts text     # Now "HELLO"
```

The `!` suffix indicates a method that modifies the object in place. This is a Ruby convention.

### Multi-line Strings

**Python:**
```python
# Triple quotes
multiline = """
This is a
multi-line string
"""

# Using parentheses and implicit concatenation
multiline = (
    "This is a "
    "multi-line string"
)
```

**Ruby:**
```ruby
# Heredocs (most common for multi-line strings)
multiline = <<~TEXT
  This is a
  multi-line string
TEXT

# Or just use quotes (less common)
multiline = "This is a
multi-line string"
```

Ruby's heredoc (`<<~TEXT`) automatically removes leading indentation, which is very convenient. We'll cover heredocs in more detail in Chapter 11.

### String Concatenation and Repetition

**Python:**
```python
# Concatenation
"Hello" + " " + "World"   # "Hello World"
" ".join(["Hello", "World"])  # "Hello World"

# Repetition
"Ha" * 3                  # "HaHaHa"
```

**Ruby:**
```ruby
# Concatenation
"Hello" + " " + "World"   # "Hello World"
"Hello" << " " << "World" # "Hello World" (mutates first string!)
["Hello", "World"].join(" ")  # "Hello World"

# Repetition
"Ha" * 3                  # "HaHaHa"
```

**Watch out:** The `<<` operator mutates the original string in Ruby:

```ruby
greeting = "Hello"
greeting << " World"  # greeting is now "Hello World"
```

### String Indexing

Both support bracket notation:

**Python:**
```python
text = "Hello"
text[0]      # "H"
text[-1]     # "o" (last character)
text[1:4]    # "ell" (slicing)
```

**Ruby:**
```ruby
text = "Hello"
text[0]      # "H"
text[-1]     # "o" (last character)
text[1..3]   # "ell" (range-based slicing)
text[1...4]  # "ell" (exclusive range)
```

We'll cover slicing and ranges in more detail in Chapter 4.

### Best Practices

**Ruby community conventions:**
- Prefer double quotes as the default (even without interpolation)
- Use single quotes only when you specifically want literal strings
- Use `#{}` interpolation instead of concatenation: `"Hello #{name}"` not `"Hello " + name`

**Python community conventions:**
- Use f-strings for interpolation (Python 3.6+)
- Single vs double quotes: choose one and be consistent (PEP 8 doesn't mandate either)

---

## 2.5 Booleans (`true`/`false` vs `True`/`False`)

Boolean values work similarly but have different capitalization and slightly different truthiness rules.

### Python Booleans

```python
# Boolean literals
is_active = True
is_deleted = False

# Boolean operations
result = True and False   # False
result = True or False    # True
result = not True         # False

# Comparisons return booleans
10 > 5    # True
10 == 10  # True

# Type
type(True)   # <class 'bool'>
```

### Ruby Booleans

```ruby
# Boolean literals (lowercase!)
is_active = true
is_deleted = false

# Boolean operations
result = true && false   # false (use && not 'and' for most cases)
result = true || false   # true (use || not 'or' for most cases)
result = !true           # false

# Comparisons return booleans
10 > 5    # true
10 == 10  # true

# Type/Class
true.class    # TrueClass
false.class   # FalseClass
```

### Key Differences

**Capitalization:**
- Python: `True`, `False` (capitalized)
- Ruby: `true`, `false` (lowercase)

This is one of the most common errors for Python developers learning Ruby!

```ruby
# Common mistake:
if True:  # NameError: uninitialized constant True
  puts "Wrong!"
end

# Correct:
if true
  puts "Correct!"
end
```

**Boolean Operators:**

Python has two styles:
```python
# Word operators (lower precedence)
True and False
True or False
not True

# Symbol operators (higher precedence)
True & False   # Bitwise AND (not commonly used for booleans)
True | False   # Bitwise OR (not commonly used for booleans)
```

Ruby also has two styles:
```ruby
# Symbol operators (standard, higher precedence)
true && false
true || false
!true

# Word operators (very low precedence, avoid except for control flow)
true and false
true or false
not true
```

**Important:** In Ruby, prefer `&&`, `||`, and `!` for boolean logic. The word operators (`and`, `or`, `not`) have very low precedence and can cause unexpected behavior:

```ruby
# Unexpected behavior with 'and':
result = true and false
puts result  # true (because of precedence!)

# Correct with &&:
result = true && false
puts result  # false
```

We'll cover this in detail in Chapter 3.6.

### Boolean Type Checking

**Python:**
```python
isinstance(True, bool)   # True
type(True) == bool       # True
```

**Ruby:**
```ruby
true.is_a?(TrueClass)    # true
false.is_a?(FalseClass)  # true

# Check if truthy/falsy (more common in Ruby)
!!value  # Converts any value to true/false
```

### Boolean Methods

Ruby has useful boolean methods on various objects:

```ruby
# Nil checks
value = nil
value.nil?              # true

# Empty checks
"".empty?               # true
[].empty?               # true
{}.empty?               # true

# Presence checks (requires ActiveSupport or define yourself)
"  ".blank?             # Would be true (Rails/ActiveSupport)
```

Python equivalents:
```python
# None checks
value = None
value is None           # True

# Empty checks
len("") == 0            # True
len([]) == 0            # True
len({}) == 0            # True
not ""                  # True (empty string is falsy)
```

---

## 2.6 `nil` vs `None`

Ruby's `nil` is equivalent to Python's `None`, representing the absence of a value.

### Python None

```python
# Assignment
value = None

# Checking for None
if value is None:
    print("Value is None")

# None is falsy
if not None:
    print("This prints")

# Type
type(None)  # <class 'NoneType'>
```

### Ruby nil

```ruby
# Assignment
value = nil

# Checking for nil
if value.nil?
  puts "Value is nil"
end

# Can also use ==, but .nil? is more idiomatic
if value == nil
  puts "Value is nil"
end

# nil is falsy
if !nil
  puts "This prints"
end

# Class
nil.class  # NilClass
```

### Key Differences

**Checking for nil/None:**

Python uses `is`:
```python
if value is None:  # Correct
    pass

if value == None:  # Works but not recommended (use 'is')
    pass
```

Ruby uses `.nil?` method (most idiomatic):
```ruby
if value.nil?  # Most idiomatic
  # code
end

if value == nil  # Also works
  # code
end

if !value  # Works but less clear (nil is falsy)
  # code
end
```

**nil is an Object:**

In Ruby, `nil` is an actual object with methods:

```ruby
nil.class        # NilClass
nil.nil?         # true
nil.to_s         # "" (empty string)
nil.to_i         # 0
nil.to_a         # [] (empty array)

# You can even call methods on nil!
result = nil
result.nil?      # true (no error!)
```

In Python, `None` is a singleton but has fewer methods:

```python
type(None)       # <class 'NoneType'>
None.something() # AttributeError
```

### Safe Navigation

Ruby has a safe navigation operator (similar to Python's optional chaining):

**Python (3.9+):**
```python
user = None
name = user and user.get("name")  # None

# No built-in safe navigation for attributes
# Would need: name = user.name if user else None
```

**Ruby:**
```ruby
user = nil
name = user&.fetch("name")  # nil (no error!)

# The &. operator short-circuits to nil if the receiver is nil
user = { "name" => "Alice" }
name = user&.fetch("name")  # "Alice"
```

The `&.` operator is called the "safe navigation operator" or "lonely operator." We'll cover it in Chapter 14.

### nil in Collections

**Python:**
```python
# None in lists
items = [1, None, 3]
items[1] is None  # True

# None as dict value
data = {"key": None}
data["key"] is None  # True

# None in conditionals
if not data.get("missing"):  # None is falsy
    print("Key doesn't exist or is None")
```

**Ruby:**
```ruby
# nil in arrays
items = [1, nil, 3]
items[1].nil?  # true

# nil as hash value
data = { "key" => nil }
data["key"].nil?  # true

# nil in conditionals
if !data["missing"]  # nil is falsy
  puts "Key doesn't exist or is nil"
end
```

---

## 2.7 Comments (single-line and multi-line)

Comments work similarly in both languages.

### Python Comments

```python
# Single-line comment

# Multi-line comments use multiple single-line comments
# This is line 1
# This is line 2

"""
This is technically a multi-line string (docstring),
not a comment, but often used as such.
"""

def example():
    """This is a docstring for documentation."""
    pass
```

### Ruby Comments

```ruby
# Single-line comment

# Multi-line comments using multiple single-line comments
# This is line 1
# This is line 2

=begin
This is a true multi-line comment in Ruby.
Everything between =begin and =end is ignored.
=end

def example
  # This is a method comment
end
```

### Key Differences

**Single-line:** Both use `#` (identical)

**Multi-line:**
- Python: No true multi-line comment syntax; use triple-quoted strings or multiple `#`
- Ruby: Has `=begin...=end` block comments, but they're rarely used

**Important:** Ruby's `=begin` must be at the start of a line (no indentation):

```ruby
# Wrong - will cause syntax error:
  =begin
  This won't work
  =end

# Correct:
=begin
This works
=end
```

Because `=begin...=end` can't be indented, it breaks code formatting. **Ruby developers almost never use it.** Instead, use `#` for all comments:

```ruby
# Preferred Ruby style:
# This is a multi-line
# comment using hash marks
# instead of =begin...=end

def example
  # Comments inside methods
  # also use hash marks
end
```

### Documentation Comments

**Python uses docstrings:**
```python
def greet(name):
    """
    Greets a person by name.

    Args:
        name (str): The person's name

    Returns:
        str: A greeting message
    """
    return f"Hello, {name}!"
```

**Ruby uses regular comments (RDoc or YARD format):**
```ruby
# Greets a person by name.
#
# @param name [String] the person's name
# @return [String] a greeting message
def greet(name)
  "Hello, #{name}!"
end
```

Ruby's documentation tools (RDoc, YARD) parse these special comment formats to generate documentation.

---

## 2.8 Everything is an Object

This is a key philosophical difference between Python and Ruby.

### Python's Approach

Python has objects and primitives that behave like objects:

```python
# These look like objects:
"hello".upper()    # "HELLO"
[1, 2, 3].append(4)

# But numbers and even functions are objects too:
(42).bit_length()  # 6
type(10)           # <class 'int'>

# Everything has a type:
type(True)         # <class 'bool'>
type(None)         # <class 'NoneType'>

# But the syntax isn't always uniform:
len([1, 2, 3])     # Function call, not method
```

### Ruby's Approach

In Ruby, **everything** is an object, and the syntax is consistent:

```ruby
# Strings are objects:
"hello".upcase     # "HELLO"

# Numbers are objects:
42.even?           # true
10.times { puts "Hi" }

# Booleans are objects:
true.class         # TrueClass
false.class        # FalseClass

# nil is an object:
nil.class          # NilClass
nil.nil?           # true

# Even classes are objects:
String.class       # Class

# Methods are uniform:
[1, 2, 3].length   # 3 (method call, not function)
"hello".length     # 5 (method call)
```

### Key Difference: Methods vs Functions

**Python mixes functions and methods:**
```python
len([1, 2, 3])         # Function
[1, 2, 3].append(4)    # Method
abs(-10)               # Function
str.upper("hello")     # Method called on class
"hello".upper()        # Method called on instance
```

**Ruby uses methods consistently:**
```ruby
[1, 2, 3].length       # Method
[1, 2, 3] << 4         # Method (operator)
-10.abs                # Method
"hello".upcase         # Method
```

### Examples of "Everything is an Object"

**Numbers as objects:**

Python:
```python
# Some object-like behavior:
(10).__add__(5)        # 15 (but weird to use)
(3.14).is_integer()    # False

# But typically use functions:
abs(-10)               # 10
round(3.14)            # 3
```

Ruby:
```ruby
# Everything is a method call:
10.+(5)                # 15 (operators are methods!)
3.14.integer?          # false
-10.abs                # 10
3.14.round             # 3
10.next                # 11

# Even loops:
5.times { puts "Hi" }  # Method on integer!
1.upto(5) { |i| puts i }
```

**Calling methods on literals:**

Python:
```python
# Works but requires parentheses for numbers:
(42).bit_length()      # 6
42.bit_length()        # SyntaxError

# Strings work fine:
"hello".upper()        # "HELLO"
```

Ruby:
```ruby
# All literals support method calls:
42.even?               # true (no parentheses needed)
"hello".upcase         # "HELLO"
[1, 2, 3].reverse      # [3, 2, 1]
{ a: 1 }.keys          # [:a]
```

### Object Introspection

Both languages support introspection, but Ruby's is more consistent:

**Python:**
```python
# Check type
type(42)               # <class 'int'>
isinstance(42, int)    # True

# List methods
dir(42)                # List all attributes/methods
```

**Ruby:**
```ruby
# Check class
42.class               # Integer
42.is_a?(Integer)      # true

# List methods
42.methods             # Array of all methods
42.methods.sort        # Sorted list
42.respond_to?(:even?) # Check if object has a method
```

### Practical Impact

This "everything is an object" philosophy makes Ruby code very consistent:

**Python:** Mix of functions and methods
```python
import math

# Different patterns:
len(my_list)           # Function
my_list.append(x)      # Method
abs(number)            # Function
string.upper()         # Method
math.sqrt(9)           # Module function
```

**Ruby:** Always methods
```ruby
# Uniform pattern:
my_array.length        # Method
my_array << x          # Method (even operators!)
number.abs             # Method
string.upcase          # Method
Math.sqrt(9)           # Method on Math module
```

This consistency is one of Ruby's core design principles, making the language feel more uniform once you learn the basic patterns.

---

## 2.9 Truthiness (only `false` and `nil` are falsy)

This is a crucial difference between Python and Ruby that affects conditionals.

### Python's Truthiness

In Python, many values are "falsy" (evaluate to `False` in boolean contexts):

```python
# All of these are falsy in Python:
if not None:        pass  # Falsy
if not False:       pass  # Falsy
if not 0:           pass  # Falsy
if not 0.0:         pass  # Falsy
if not "":          pass  # Falsy (empty string)
if not []:          pass  # Falsy (empty list)
if not {}:          pass  # Falsy (empty dict)
if not set():       pass  # Falsy (empty set)

# Only these are truthy:
if True:            pass  # Truthy
if 1:               pass  # Truthy
if "hello":         pass  # Truthy (non-empty string)
if [1]:             pass  # Truthy (non-empty list)
```

### Ruby's Truthiness

In Ruby, **only `false` and `nil` are falsy**. Everything else is truthy:

```ruby
# Falsy values (only two!):
if !nil             # Falsy
if !false           # Falsy

# EVERYTHING else is truthy:
if true             # Truthy
if 0                # Truthy (!!!)
if 0.0              # Truthy
if ""               # Truthy (!!!)
if []               # Truthy
if {}               # Truthy
```

### Key Difference: Empty Collections and Zero

This is a major gotcha for Python developers:

**Python:**
```python
items = []
if items:
    print("Has items")
else:
    print("Empty")  # This prints

count = 0
if count:
    print("Has count")
else:
    print("Zero")  # This prints
```

**Ruby:**
```ruby
items = []
if items
  puts "Has items"  # This prints (!!!)
else
  puts "Empty"
end

count = 0
if count
  puts "Has count"  # This prints (!!!)
else
  puts "Zero"
end
```

**How to check for empty in Ruby:**

```ruby
# Check array empty:
if items.empty?
  puts "Empty"
end

# Check for zero:
if count.zero?
  puts "Zero"
end

# Check for nil or empty:
if items.nil? || items.empty?
  puts "Nil or empty"
end
```

### Practical Examples

**Python idioms don't translate directly:**

Python:
```python
# Common Python pattern:
def process(data):
    if not data:  # Checks for None, [], {}, "", 0, etc.
        return "No data"
    return f"Processing {len(data)} items"

process([])      # "No data"
process(None)    # "No data"
process([1, 2])  # "Processing 2 items"
```

Ruby:
```ruby
# Direct translation DOESN'T work:
def process(data)
  if !data  # Only checks for nil and false!
    return "No data"
  end
  "Processing #{data.length} items"
end

process([])      # "Processing 0 items" (not what Python dev expects!)
process(nil)     # "No data"
process([1, 2])  # "Processing 2 items"

# Correct Ruby version:
def process(data)
  if data.nil? || data.empty?
    return "No data"
  end
  "Processing #{data.length} items"
end
```

### Using presence/blank? (Rails)

Rails adds helpful methods for truthiness checks:

```ruby
# Rails/ActiveSupport adds:
"".blank?          # true
[].blank?          # true
nil.blank?         # true
false.blank?       # true

"hello".present?   # true (opposite of blank?)
[1, 2].present?    # true
```

Without Rails, you need explicit checks:

```ruby
# Pure Ruby:
value.nil? || (value.respond_to?(:empty?) && value.empty?)
```

### Truthiness Table

| Value | Python | Ruby |
|-------|--------|------|
| `None`/`nil` | Falsy | Falsy |
| `False`/`false` | Falsy | Falsy |
| `0` | Falsy | **Truthy** |
| `0.0` | Falsy | **Truthy** |
| `""` | Falsy | **Truthy** |
| `[]` | Falsy | **Truthy** |
| `{}` | Falsy | **Truthy** |
| `True`/`true` | Truthy | Truthy |
| `1` | Truthy | Truthy |
| `"hello"` | Truthy | Truthy |
| `[1, 2]` | Truthy | Truthy |

### Converting to Boolean

**Python:**
```python
bool(value)        # Converts to True/False
not not value      # Double negation (same effect)
```

**Ruby:**
```ruby
!!value            # Double negation (converts to true/false)

# Examples:
!!nil              # false
!!0                # true (!)
!!""               # true (!)
!![].empty?        # true
```

### Best Practices

When coming from Python to Ruby:

**Don't assume empty collections are falsy:**
```ruby
# Wrong (Python thinking):
if !items
  puts "No items"
end

# Correct:
if items.empty?
  puts "No items"
end
```

**Be explicit about what you're checking:**
```ruby
# Instead of:
if value  # What are you checking for?

# Be clear:
if !value.nil?
if value.present?  # Rails
if !value.empty?
```

This makes your intent clear and prevents bugs when transitioning from Python.

---

That completes Chapter 2! You now understand Ruby's basic syntax and data types, and how they compare to Python. Key takeaways:

- Variables don't need declaration, but scope indicators (`@`, `@@`, `$`) are different
- Ruby uses `snake_case` everywhere, with special method suffixes (`?`, `!`)
- Integer division behaves differently
- String interpolation uses `#{}` in double quotes (no `f` prefix needed)
- Booleans are `true`/`false` (lowercase!)
- `nil` is Ruby's `None`
- Everything is truly an object in Ruby
- **Only `false` and `nil` are falsy** (major difference from Python!)

In Chapter 3, we'll explore control flow structures and see how Ruby's conditional statements differ from Python's.
