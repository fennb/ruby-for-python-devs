# Chapter 11: String Handling

## 11.1 String Literals (single vs double quotes)

Both Ruby and Python support single and double-quoted strings, but the behavior differs significantly. Understanding when to use each type of quote is important for writing idiomatic Ruby code.

**Key Similarities:**
- Both use single (`'`) and double (`"`) quotes for strings
- Both support escape sequences in double-quoted strings
- Both treat strings as sequences of characters

**Key Differences:**
- Python treats single and double quotes identically (mostly interchangeable)
- Ruby's single quotes are more restrictive - no interpolation, limited escape sequences
- Ruby's double quotes support interpolation and full escape sequences
- Ruby has a stronger convention about when to use each

### Python Example

```python
# In Python, single and double quotes are mostly interchangeable
name = 'Alice'
greeting = "Hello"

# Both support the same escape sequences
message1 = "Hello\nWorld"
message2 = 'Hello\nWorld'  # Works the same

# Choice is mainly about avoiding escaping
quote1 = "She said, 'Hello!'"
quote2 = 'He said, "Hi!"'

# f-strings work with both quote types
name = "Alice"
msg1 = f"Hello, {name}"
msg2 = f'Hello, {name}'  # Both work identically
```

### Ruby Equivalent

```ruby
# Single quotes: literal strings, minimal processing
name = 'Alice'
greeting = 'Hello'

# Single quotes: limited escape sequences (only \\ and \')
message = 'Hello\nWorld'  # Prints literally: Hello\nWorld
escaped = 'It\'s Ruby'    # Only \' and \\ work
backslash = 'C:\\Users'   # Need to escape backslashes

# Double quotes: full escape sequences
message = "Hello\nWorld"  # Prints with newline: Hello
                          #                       World

# Double quotes: string interpolation
name = "Alice"
greeting = "Hello, #{name}!"  # Hello, Alice!

# Single quotes: NO interpolation
greeting = 'Hello, #{name}!'  # Prints literally: Hello, #{name}!

# Both styles for quotes within strings
quote1 = "She said, 'Hello!'"
quote2 = 'He said, "Hi!"'
```

### Explanation

Ruby treats single and double quotes very differently:

**Single quotes (`'`):**
- Create literal strings with minimal processing
- Only two escape sequences work: `\'` and `\\`
- No string interpolation
- Faster (slightly) because less processing
- Use for literal strings where you don't need special features

**Double quotes (`"`):**
- Support all escape sequences (`\n`, `\t`, `\r`, `\x`, `\u`, etc.)
- Support string interpolation with `#{}`
- More flexible and commonly used
- Use when you need interpolation or escape sequences

**Escape sequences in double quotes:**

```ruby
# Common escape sequences
puts "Line 1\nLine 2"        # Newline
puts "Column1\tColumn2"      # Tab
puts "Quote: \"Hello\""      # Escaped quote
puts "Backslash: \\"         # Escaped backslash
puts "Unicode: \u2665"       # Unicode heart: ♥
puts "Hex: \x41"             # Hex character: A

# These don't work in single quotes!
puts 'Line 1\nLine 2'        # Prints: Line 1\nLine 2
```

**Ruby style convention:**

The Ruby community generally prefers **double quotes as the default**, even when interpolation isn't needed. This is opposite to Python's PEP 8, which doesn't mandate a preference.

```ruby
# Common Ruby style (double quotes by default)
name = "Alice"
status = "active"
message = "Hello, #{name}!"

# Some teams prefer single quotes for non-interpolated strings
name = 'Alice'
status = 'active'
message = "Hello, #{name}!"  # But use double for interpolation

# Choose what your team prefers and be consistent
```

**Common pitfall:** Python developers might expect single and double quotes to behave the same. In Ruby, `'#{name}'` and `"#{name}"` produce very different results!

```ruby
name = "Alice"
puts "Hello, #{name}!"  # Hello, Alice!
puts 'Hello, #{name}!'  # Hello, #{name}!
```

---

## 11.2 String Interpolation (`#{}`) vs f-strings

String interpolation is how you embed expressions inside strings. Ruby's `#{}` syntax in double-quoted strings is similar to Python's f-strings, but it's been a core feature of Ruby since the beginning.

**Key Similarities:**
- Both allow embedding expressions directly in strings
- Both evaluate expressions at runtime
- Both automatically convert results to strings

**Key Differences:**
- Python requires the `f` prefix for f-strings
- Ruby's interpolation works in all double-quoted strings (no prefix needed)
- Ruby's `#{}` can contain any expression, including method calls and complex logic
- Ruby doesn't have Python's format specifiers (`:0.2f`, etc.)

### Python Example

```python
name = "Alice"
age = 30
price = 19.99

# Old-style formatting (avoid)
message = "Hello, %s! You are %d years old." % (name, age)

# str.format() method
message = "Hello, {}! You are {} years old.".format(name, age)

# f-strings (modern Python 3.6+)
message = f"Hello, {name}! You are {age} years old."

# Expressions in f-strings
message = f"Hello, {name.upper()}! Next year: {age + 1}"

# Format specifiers
formatted = f"Price: ${price:.2f}"  # Price: $19.99
aligned = f"{name:>10}"             # Right-aligned

# Multiline f-strings
message = f"""
Hello, {name}!
You are {age} years old.
"""
```

### Ruby Equivalent

```ruby
name = "Alice"
age = 30
price = 19.99

# String interpolation (always available in double quotes)
message = "Hello, #{name}! You are #{age} years old."

# Expressions in interpolation
message = "Hello, #{name.upcase}! Next year: #{age + 1}"

# No built-in format specifiers - use methods instead
formatted = "Price: $#{'%.2f' % price}"  # Price: $19.99
# Or use format method
formatted = "Price: $#{format('%.2f', price)}"

# Method calls without parentheses
message = "Uppercase: #{name.upcase}"
message = "Length: #{name.length}"

# Complex expressions
users = ["Alice", "Bob", "Charlie"]
message = "We have #{users.length} users: #{users.join(', ')}"

# Multiline strings with interpolation
message = "
Hello, #{name}!
You are #{age} years old.
"

# Or use heredocs (covered in next section)
message = <<~TEXT
  Hello, #{name}!
  You are #{age} years old.
TEXT
```

### Explanation

Ruby's interpolation syntax `#{}` is straightforward - put any expression inside the curly braces, and it will be evaluated and converted to a string.

**Important points:**

- Works only in **double-quoted strings** (not single quotes)
- Automatically calls `.to_s` on the result
- Can contain any Ruby expression: variables, method calls, operators, even blocks
- No prefix needed (unlike Python's `f`)

**Complex interpolation examples:**

```ruby
# Method chaining in interpolation
text = "Hello, World!"
puts "Result: #{text.downcase.gsub('world', 'Ruby')}"
# Result: hello, ruby!

# Conditional expressions
age = 25
puts "You are #{age >= 18 ? 'an adult' : 'a minor'}"
# You are an adult

# Arithmetic
x, y = 10, 20
puts "The sum of #{x} and #{y} is #{x + y}"
# The sum of 10 and 20 is 30

# Hash access
person = { name: "Alice", age: 30 }
puts "Name: #{person[:name]}, Age: #{person[:age]}"
# Name: Alice, Age: 30
```

**Formatting numbers (without f-string specifiers):**

Ruby doesn't have format specifiers built into interpolation, but you can use the `%` operator or `format` method:

```ruby
price = 19.99

# Using % operator
puts "Price: $#{'%.2f' % price}"        # Price: $19.99

# Using format method
puts "Price: $#{format('%.2f', price)}" # Price: $19.99

# For simple cases, use round
puts "Price: $#{price.round(2)}"        # Price: $19.99

# Thousands separator
number = 1234567
puts "#{number.to_s.reverse.scan(/\d{3}|.+/).join(',').reverse}"
# Better: use number_to_currency from Rails, or the 'humanize' gem
```

**Nested interpolation (possible but avoid):**

```ruby
# This works but is hard to read
name = "Alice"
puts "Hello, #{"Mr. #{name}".upcase}!"
# Hello, MR. ALICE!

# Better: break it up
title = "Mr. #{name}"
puts "Hello, #{title.upcase}!"
```

**Performance note:**

Interpolation creates a new string each time. If you're building strings in a loop, consider using an array and `join`:

```ruby
# Less efficient
message = ""
100.times do |i|
  message += "#{i}, "  # Creates 100 new strings
end

# More efficient
parts = []
100.times do |i|
  parts << i
end
message = parts.join(", ")

# Or use String#<< (append)
message = String.new
100.times do |i|
  message << "#{i}, "
end
```

**Common pitfall:** Forgetting that interpolation only works in double quotes:

```ruby
name = "Alice"
puts 'Hello, #{name}!'  # Prints: Hello, #{name}!
puts "Hello, #{name}!"  # Prints: Hello, Alice!
```

---

## 11.3 Multiline Strings (heredocs)

Both Python and Ruby support multiline strings, but Ruby's heredoc syntax is more flexible and feature-rich. Heredocs are particularly useful for embedding long text, SQL queries, or formatted output.

**Key Similarities:**
- Both support multiline strings
- Both preserve newlines and whitespace
- Both can include interpolation

**Key Differences:**
- Python uses triple quotes (`"""` or `'''`)
- Ruby uses heredoc syntax (`<<IDENTIFIER`)
- Ruby has multiple heredoc variants for different use cases
- Ruby's squiggly heredoc (`<<~`) strips leading indentation

### Python Example

```python
# Triple-quoted strings
message = """
Hello, World!
This is a multiline string.
It preserves newlines and    spacing.
"""

# With interpolation (triple-quoted f-strings)
name = "Alice"
message = f"""
Hello, {name}!
Welcome to our application.
Your account is ready.
"""

# Indentation is preserved (including leading spaces)
def greet():
    message = """
    Hello!
    This has leading spaces.
    """
    print(message)
# Prints with all the leading spaces

# Using textwrap.dedent to remove leading indentation
from textwrap import dedent

def greet():
    message = dedent("""
        Hello!
        This is dedented.
        Much cleaner!
    """)
    print(message)
```

### Ruby Equivalent

```ruby
# Basic heredoc (<<IDENTIFIER)
message = <<TEXT
Hello, World!
This is a multiline string.
It preserves newlines and    spacing.
TEXT

# With interpolation (by default)
name = "Alice"
message = <<MESSAGE
Hello, #{name}!
Welcome to our application.
Your account is ready.
MESSAGE

# The identifier can be anything (commonly: EOF, SQL, HTML, TEXT)
query = <<SQL
SELECT *
FROM users
WHERE status = 'active'
SQL

# Squiggly heredoc (<<~) - strips leading indentation
def greet
  message = <<~TEXT
    Hello!
    This is dedented automatically.
    Much cleaner!
  TEXT
  puts message
end
# Prints without leading spaces

# Single-quoted heredoc (no interpolation)
name = "Alice"
message = <<'TEXT'
Hello, #{name}!
This won't be interpolated.
TEXT
# Prints: Hello, #{name}!

# Dashed heredoc (<<-) - allows indenting the terminator
def example
  message = <<-TEXT
    This is indented.
    The terminator can be indented too.
  TEXT
  puts message
end
```

### Explanation

Ruby's heredoc syntax starts with `<<` followed by an identifier (the terminator). Everything after that line until the terminator appears on its own line is part of the string.

**Heredoc variants:**

1. **`<<IDENTIFIER`** - Basic heredoc, terminator must be at start of line
2. **`<<-IDENTIFIER`** - Terminator can be indented
3. **`<<~IDENTIFIER`** - Squiggly heredoc, strips leading whitespace
4. **`<<'IDENTIFIER'`** - Single-quoted, no interpolation

**Basic heredoc:**

```ruby
# Terminator must be at the beginning of the line
message = <<END
This is a heredoc.
The END must be at the start of the line.
END

# This would be a syntax error:
# message = <<END
#   Text here
#   END  # Error! Indented terminator
```

**Dashed heredoc (`<<-`):**

```ruby
# Allows indenting the terminator (but content indentation is preserved)
def example
  message = <<-TEXT
    This text is indented.
    The terminator can be indented too.
  TEXT
  puts message
end
# Still prints with all the indentation in the content
```

**Squiggly heredoc (`<<~`) - Most useful!**

```ruby
# Automatically strips leading indentation
def greet(name)
  message = <<~TEXT
    Hello, #{name}!

    Welcome to our application.
    Your account is ready.

    Best regards,
    The Team
  TEXT
  puts message
end

greet("Alice")
# Prints cleanly without leading spaces:
# Hello, Alice!
#
# Welcome to our application.
# Your account is ready.
#
# Best regards,
# The Team
```

**Single-quoted heredoc (no interpolation):**

```ruby
# Use single quotes for no interpolation
name = "Alice"
template = <<'TEMPLATE'
Hello, #{name}!
This is a template that won't be interpolated.
TEMPLATE

puts template
# Prints: Hello, #{name}!
```

**Practical examples:**

```ruby
# SQL queries
query = <<~SQL
  SELECT users.name, orders.total
  FROM users
  INNER JOIN orders ON users.id = orders.user_id
  WHERE orders.status = 'completed'
  ORDER BY orders.created_at DESC
SQL

# HTML templates (before template engines)
html = <<~HTML
  <div class="user-card">
    <h2>#{user.name}</h2>
    <p>Email: #{user.email}</p>
    <p>Member since: #{user.created_at}</p>
  </div>
HTML

# JSON (though usually use proper JSON library)
json = <<~JSON
  {
    "name": "#{name}",
    "age": #{age},
    "active": true
  }
JSON

# Multi-line email
email_body = <<~EMAIL
  Dear #{customer_name},

  Thank you for your order ##{order_id}.

  Your items will be shipped within 3-5 business days.

  Tracking information will be sent to #{customer_email}.

  Best regards,
  The Team
EMAIL
```

**Heredoc with method chaining:**

```ruby
# You can chain methods on heredocs
message = <<~TEXT.upcase.gsub('WORLD', 'RUBY')
  Hello, World!
  Welcome to programming.
TEXT

puts message
# HELLO, RUBY!
# WELCOME TO PROGRAMMING.
```

**Inline heredocs (advanced):**

```ruby
# Heredoc can start inline and continue on next lines
puts(<<~TEXT, "Another argument", <<~MORE)
  This is the first heredoc.
  It can appear inline.
TEXT
  This is the second heredoc.
  Multiple heredocs in one statement!
MORE

# Useful for method calls with long text
send_email(
  to: user.email,
  subject: "Welcome!",
  body: <<~EMAIL
    Dear #{user.name},

    Welcome to our service!
  EMAIL
)
```

**Common pitfall:** The terminator must appear exactly as specified, with no leading or trailing spaces (except for `<<-` and `<<~`):

```ruby
# This works
message = <<END
Text here
END

# This fails (extra spaces after END)
message = <<END
Text here
END

# This fails (terminator is "END" not "end")
message = <<END
Text here
end
```

**Best practice:** Use `<<~` (squiggly heredoc) by default for cleaner, more readable code. It's the most similar to Python's `textwrap.dedent` behavior.

---

## 11.4 Common String Methods

Ruby has a rich set of string methods that parallel Python's string methods, but with Ruby naming conventions and some unique additions. Understanding the common methods will help you manipulate strings effectively.

**Key Similarities:**
- Both have methods for case conversion, searching, replacing, splitting, etc.
- Both support method chaining
- Both treat strings as sequences

**Key Differences:**
- Ruby methods often have `!` versions that modify in place
- Ruby has more methods with question mark names (`empty?`, `include?`)
- Ruby strings are mutable by default
- Different naming conventions (snake_case vs lowercase)

### Python Example

```python
text = "  Hello, World!  "

# Case conversion
print(text.upper())        # "  HELLO, WORLD!  "
print(text.lower())        # "  hello, world!  "
print(text.capitalize())   # "  hello, world!  "
print(text.title())        # "  Hello, World!  "

# Whitespace
print(text.strip())        # "Hello, World!"
print(text.lstrip())       # "Hello, World!  "
print(text.rstrip())       # "  Hello, World!"

# Searching
print(text.find("World"))       # 9
print(text.index("World"))      # 9
print("World" in text)          # True
print(text.startswith("  He"))  # True
print(text.endswith("!  "))     # True
print(text.count("l"))          # 3

# Replacing
print(text.replace("World", "Ruby"))  # "  Hello, Ruby!  "

# Splitting and joining
words = text.strip().split(", ")      # ["Hello", "World!"]
print("-".join(words))                # "Hello-World!"

# Testing
print(text.isalpha())     # False
print(text.isdigit())     # False
print("123".isdigit())    # True
```

### Ruby Equivalent

```ruby
text = "  Hello, World!  "

# Case conversion
puts text.upcase        # "  HELLO, WORLD!  "
puts text.downcase      # "  hello, world!  "
puts text.capitalize    # "  hello, world!  " (only first char)
puts text.swapcase      # "  hELLO, wORLD!  "

# Whitespace removal
puts text.strip         # "Hello, World!"
puts text.lstrip        # "Hello, World!  "
puts text.rstrip        # "  Hello, World!"

# Searching
puts text.index("World")        # 9
puts text.include?("World")     # true
puts text.start_with?("  He")   # true
puts text.end_with?("!  ")      # true
puts text.count("l")            # 3

# Replacing
puts text.gsub("World", "Ruby")   # "  Hello, Ruby!  "
puts text.sub("l", "L")           # "  HeLlo, World!  " (only first)
puts text.tr("l", "L")            # "  HeLLo, WorLd!  " (all chars)

# Splitting and joining
words = text.strip.split(", ")    # ["Hello", "World!"]
puts words.join("-")              # "Hello-World!"

# Testing (with ? methods)
puts text.empty?             # false
puts "".empty?               # true
puts text.include?("Hello")  # true

# Ruby-specific: reverse
puts text.reverse            # "  !dlroW ,olleH  "

# Characters
puts text.length             # 17
puts text.size               # 17 (alias)
puts text.chars              # Array of characters
```

### Explanation

Ruby string methods follow Ruby's naming conventions and often come in pairs - a regular version and a `!` (bang) version that modifies the string in place.

**Mutating vs non-mutating methods:**

```ruby
text = "hello"

# Non-mutating (returns new string)
result = text.upcase
puts result  # "HELLO"
puts text    # "hello" (original unchanged)

# Mutating (modifies in place)
text.upcase!
puts text    # "HELLO" (original modified!)
```

**Common methods reference:**

```ruby
str = "Hello, World!"

# CASE CONVERSION
str.upcase          # "HELLO, WORLD!"
str.downcase        # "hello, world!"
str.capitalize      # "Hello, world!" (only first char)
str.swapcase        # "hELLO, wORLD!"

# WHITESPACE
"  text  ".strip    # "text"
"  text  ".lstrip   # "text  "
"  text  ".rstrip   # "  text"

# SEARCHING & TESTING
str.include?("World")    # true
str.start_with?("Hel")   # true
str.end_with?("!")       # true
str.index("World")       # 7
str.rindex("o")          # 8 (rightmost index)
str.empty?               # false

# REPLACING
str.gsub("l", "L")       # "HeLLo, WorLd!" (global substitute)
str.sub("l", "L")        # "HeLlo, World!" (substitute first)
str.tr("lo", "LO")       # "HeLLO, WOrLd!" (translate chars)
str.delete("l")          # "Heo, Word!" (delete all l's)

# SPLITTING & EXTRACTING
str.split(", ")          # ["Hello", "World!"]
str.split("")            # Array of each character
str.chars                # ["H", "e", "l", "l", "o", ...]
str.lines                # Array of lines (split by \n)

# SLICING (covered earlier, but important)
str[0]                   # "H"
str[0..4]                # "Hello"
str[7..-1]               # "World!"
str.slice(0, 5)          # "Hello"

# LENGTH & SIZE
str.length               # 13
str.size                 # 13 (alias)
str.bytesize             # Byte size (differs for multibyte chars)

# TRANSFORMATION
str.reverse              # "!dlroW ,olleH"
str * 3                  # "Hello, World!Hello, World!Hello, World!"
str.concat(" Goodbye!")  # "Hello, World! Goodbye!"
str + " Goodbye!"        # "Hello, World! Goodbye!"
str << " Goodbye!"       # Append (mutating)

# COMPARISON
"abc" <=> "def"          # -1 (less than)
"abc" <=> "abc"          # 0 (equal)
"def" <=> "abc"          # 1 (greater than)
"abc".casecmp("ABC")     # 0 (case-insensitive comparison)
```

**Working with each character:**

```ruby
str = "Hello"

# Iterate over characters
str.each_char do |char|
  puts char
end

# Map characters
result = str.each_char.map { |c| c.upcase }  # ["H", "E", "L", "L", "O"]

# Or use chars
result = str.chars.map(&:upcase)  # ["H", "E", "L", "L", "O"]
```

**Practical examples:**

```ruby
# Remove all vowels
text = "Hello, World!"
puts text.delete("aeiouAEIOU")  # "Hll, Wrld!"

# Replace multiple patterns
text = "Hello, World!"
result = text.gsub(/[aeiou]/, '*')  # "H*ll*, W*rld!"

# Titleize (capitalize each word)
text = "hello world from ruby"
puts text.split.map(&:capitalize).join(" ")  # "Hello World From Ruby"

# Check if string contains only letters
puts "hello".match?(/^[a-zA-Z]+$/)  # true
puts "hello123".match?(/^[a-zA-Z]+$/)  # false

# Truncate with ellipsis
text = "This is a very long string"
max_length = 15
truncated = text.length > max_length ? text[0...max_length] + "..." : text
puts truncated  # "This is a very..."

# Or use slice with safe navigation
puts text[0...15] + (text.length > 15 ? "..." : "")
```

**Bang methods (in-place modification):**

```ruby
text = "hello"

# Non-bang returns new string
result = text.upcase
puts text    # "hello"
puts result  # "HELLO"

# Bang modifies original
text.upcase!
puts text    # "HELLO"

# Common bang methods:
# upcase!, downcase!, capitalize!, swapcase!
# strip!, lstrip!, rstrip!
# gsub!, sub!, tr!, delete!
# reverse!, clear!
```

**Common pitfall:** Python strings are immutable, but Ruby strings are mutable. Be careful with `!` methods:

```ruby
original = "hello"
reference = original

original.upcase!
puts reference  # "HELLO" - both variables point to same string!

# To avoid this, use non-bang version or dup
original = "hello"
reference = original.dup  # Create a copy
original.upcase!
puts reference  # "hello" - now independent
```

---

## 11.5 String Encoding

Both Ruby and Python 3 handle text as Unicode by default, but they manage encoding differently. Understanding encoding is important when working with files, network data, or text from external sources.

**Key Similarities:**
- Both default to UTF-8 for strings
- Both support various encodings (ASCII, UTF-8, UTF-16, etc.)
- Both allow explicit encoding specification

**Key Differences:**
- Ruby has explicit encoding methods on strings
- Python 3 has bytes vs str distinction
- Ruby allows changing a string's encoding
- Different syntax for encoding/decoding

### Python Example

```python
# Python 3 strings are Unicode by default
text = "Hello, 世界!"
print(type(text))  # <class 'str'>

# Encoding: str → bytes
utf8_bytes = text.encode('utf-8')
print(utf8_bytes)  # b'Hello, \xe4\xb8\x96\xe7\x95\x8c!'
print(type(utf8_bytes))  # <class 'bytes'>

# Decoding: bytes → str
decoded = utf8_bytes.decode('utf-8')
print(decoded)  # "Hello, 世界!"

# Different encodings
ascii_bytes = "Hello".encode('ascii')
latin1_bytes = "Café".encode('latin-1')

# Handling errors
try:
    "Café".encode('ascii')  # UnicodeEncodeError
except UnicodeEncodeError as e:
    print(f"Error: {e}")

# Error handling strategies
safe = "Café".encode('ascii', errors='ignore')    # b'Caf'
replace = "Café".encode('ascii', errors='replace') # b'Caf?'

# File I/O with encoding
with open('file.txt', 'w', encoding='utf-8') as f:
    f.write("Hello, 世界!")

with open('file.txt', 'r', encoding='utf-8') as f:
    content = f.read()
```

### Ruby Equivalent

```ruby
# Ruby strings have encoding
text = "Hello, 世界!"
puts text.encoding  # UTF-8

# Get encoding info
puts text.encoding.name      # "UTF-8"
puts text.valid_encoding?    # true

# Encoding: string → bytes
utf8_bytes = text.encode("UTF-8")
puts utf8_bytes.bytes  # Array of byte values

# Force encoding (reinterpret bytes)
ascii_text = "Hello"
ascii_text.force_encoding("ASCII-8BIT")
puts ascii_text.encoding  # ASCII-8BIT

# Transcode (convert encoding)
utf16_text = text.encode("UTF-16")
puts utf16_text.encoding  # UTF-16

# Back to UTF-8
utf8_again = utf16_text.encode("UTF-8")
puts utf8_again.encoding  # UTF-8

# Handling errors
begin
  "Café".encode("ASCII")  # Encoding::UndefinedConversionError
rescue Encoding::UndefinedConversionError => e
  puts "Error: #{e}"
end

# Error handling strategies
safe = "Café".encode("ASCII", invalid: :replace, undef: :replace)
puts safe  # "Caf?"

ignore = "Café".encode("ASCII", invalid: :replace, undef: :replace, replace: "")
puts ignore  # "Caf"

# File I/O with encoding
File.open('file.txt', 'w:UTF-8') do |f|
  f.write("Hello, 世界!")
end

content = File.read('file.txt', encoding: 'UTF-8')
puts content
```

### Explanation

Ruby strings carry their encoding information with them. You can check, change, and convert encodings using various methods.

**Key encoding methods:**

```ruby
str = "Hello, 世界!"

# Query encoding
puts str.encoding           # #<Encoding:UTF-8>
puts str.encoding.name      # "UTF-8"
puts str.valid_encoding?    # true

# Get byte representation
puts str.bytes              # [72, 101, 108, 108, 111, ...]
puts str.bytesize           # Byte size: 15
puts str.length             # Character count: 9

# ASCII vs UTF-8
ascii = "Hello"
puts ascii.bytesize         # 5
puts ascii.length           # 5

utf8 = "世界"
puts utf8.bytesize          # 6 (2 chars × 3 bytes each)
puts utf8.length            # 2 (character count)
```

**Changing encoding:**

Ruby has two ways to change a string's encoding:

1. **`force_encoding`** - Reinterpret bytes without changing them
2. **`encode`** - Convert/transcode to a new encoding

```ruby
# force_encoding: reinterpret bytes
str = "Hello"
str.force_encoding("ASCII-8BIT")  # Treat as binary
puts str.encoding  # ASCII-8BIT
str.force_encoding("UTF-8")       # Back to UTF-8
puts str.encoding  # UTF-8

# encode: actually convert
str = "Hello, 世界!"
utf16 = str.encode("UTF-16LE")
puts utf16.encoding      # UTF-16LE
puts utf16.bytesize      # Different byte size

# Convert back
utf8 = utf16.encode("UTF-8")
puts utf8.encoding       # UTF-8
```

**Handling encoding errors:**

```ruby
str = "Café"

# Default: raises error
begin
  str.encode("ASCII")
rescue Encoding::UndefinedConversionError => e
  puts "Cannot encode: #{e}"
end

# Replace invalid characters
replaced = str.encode("ASCII",
                      invalid: :replace,
                      undef: :replace,
                      replace: '?')
puts replaced  # "Caf?"

# Ignore invalid characters
ignored = str.encode("ASCII",
                     invalid: :replace,
                     undef: :replace,
                     replace: '')
puts ignored  # "Caf"

# Use XML entities
xml_safe = str.encode("ASCII",
                      invalid: :replace,
                      undef: :replace,
                      replace: '&')
```

**Common encoding scenarios:**

```ruby
# Reading binary data
binary_data = File.read('image.png', mode: 'rb')  # binary mode
puts binary_data.encoding  # ASCII-8BIT

# Reading text with specific encoding
text = File.read('file.txt', encoding: 'UTF-8')
puts text.encoding  # UTF-8

# Converting from one encoding to another
iso_text = File.read('legacy.txt', encoding: 'ISO-8859-1')
utf8_text = iso_text.encode('UTF-8')

# Checking if string is valid for its encoding
str = "Hello, 世界!"
puts str.valid_encoding?  # true

# Create invalid string
invalid = "Hello".force_encoding("UTF-16")  # Wrong encoding
puts invalid.valid_encoding?  # false

# Scrub invalid characters
fixed = invalid.scrub('?')
puts fixed.valid_encoding?  # true
```

**Encoding compatibility:**

```ruby
# Check if two strings are compatible
str1 = "Hello".encode("UTF-8")
str2 = "World".encode("ASCII")

puts str1.encoding  # UTF-8
puts str2.encoding  # US-ASCII

# ASCII is compatible with UTF-8
combined = str1 + str2
puts combined.encoding  # UTF-8

# Incompatible encodings raise error
str3 = "Test".encode("UTF-16")
begin
  str1 + str3  # Error: incompatible encodings
rescue Encoding::CompatibilityError => e
  puts "Error: #{e}"
end

# Convert to make compatible
str3_utf8 = str3.encode("UTF-8")
combined = str1 + str3_utf8  # Works!
```

**Magic comments for source file encoding:**

```ruby
# At the top of your .rb file:
# frozen_string_literal: true
# encoding: UTF-8

# Or:
# -*- coding: utf-8 -*-

# This tells Ruby what encoding to use for string literals in the file
```

**Common pitfall:** Using `force_encoding` when you should use `encode`:

```ruby
# Wrong: force_encoding just changes the label
str = "Café"
ascii_str = str.force_encoding("ASCII")
puts ascii_str  # Garbled! Bytes interpreted as ASCII

# Right: encode actually converts
str = "Café"
begin
  ascii_str = str.encode("ASCII", replace: '?')
  puts ascii_str  # "Caf?"
rescue => e
  puts "Conversion error: #{e}"
end
```

**Best practice:** In modern Ruby, you rarely need to worry about encoding if you:
- Keep your source files in UTF-8
- Read/write files with explicit encoding
- Use UTF-8 for all text processing

---

## 11.6 Regular Expressions

Regular expressions in Ruby are first-class language features with literal syntax and powerful methods. While Python's `re` module is powerful, Ruby's regex support is more deeply integrated into the language.

### 11.6.1 Regex Literals (`/.../`)

**Key Similarities:**
- Both use similar regex syntax (Perl-style)
- Both support flags/modifiers (case-insensitive, multiline, etc.)
- Both use `\` for escaping

**Key Differences:**
- Ruby has regex literals with `/pattern/` (no string quotes needed)
- Python requires importing the `re` module
- Ruby's regexes are objects with methods
- Different syntax for flags/modifiers

#### Python Example

```python
import re

# Compile a regex pattern
pattern = re.compile(r'\d+')

# Or use directly
result = re.search(r'\d+', 'abc123def')
print(result.group())  # "123"

# Case-insensitive flag
pattern = re.compile(r'hello', re.IGNORECASE)
match = pattern.search('HELLO WORLD')
print(match.group())  # "HELLO"

# Multiple flags
pattern = re.compile(r'hello.*world', re.IGNORECASE | re.DOTALL)

# Raw strings are important
path_pattern = r'C:\\Users\\Documents'  # r prefix avoids escaping issues
```

#### Ruby Equivalent

```ruby
# Regex literal
pattern = /\d+/

# Use directly in matching
result = 'abc123def'.match(/\d+/)
puts result[0]  # "123"

# Case-insensitive flag (i)
pattern = /hello/i
match = 'HELLO WORLD'.match(pattern)
puts match[0]  # "HELLO"

# Multiple flags
pattern = /hello.*world/im  # i = case-insensitive, m = multiline

# No need for raw strings - Ruby regex literals handle escaping
path_pattern = /C:\\Users\\Documents/

# Regex is an object
puts pattern.class  # Regexp
puts pattern.inspect  # /hello.*world/im
```

#### Explanation

Ruby's regex literals use forward slashes `/pattern/` similar to JavaScript. This makes regex a first-class part of the language syntax rather than strings passed to a library.

**Regex flags (modifiers):**

```ruby
# Common flags (placed after closing /)
/pattern/i   # Case-insensitive
/pattern/m   # Multiline mode (. matches newlines)
/pattern/x   # Extended (allows whitespace and comments)
/pattern/o   # Compile once (optimization)

# Combine flags
/hello.*world/im  # Case-insensitive AND multiline

# Extended mode example (x flag)
regex = /
  \d{3}    # Area code
  -        # Dash
  \d{4}    # Number
/x

# This matches: "123-4567"
```

**Creating regex dynamically:**

```ruby
# Literal regex
pattern = /hello/

# Regexp.new for dynamic patterns
search_term = "hello"
pattern = Regexp.new(search_term)  # /hello/

# With flags
pattern = Regexp.new(search_term, Regexp::IGNORECASE)  # /hello/i

# Escape user input to prevent regex injection
user_input = "hello (world)"
safe_pattern = Regexp.escape(user_input)  # "hello\ \(world\)"
pattern = Regexp.new(safe_pattern)  # /hello\ \(world\)/
```

**Regex interpolation:**

```ruby
# Interpolate variables into regex
word = "hello"
pattern = /#{word}/  # /hello/

# Combine patterns
prefix = /hello/
suffix = /world/
# Can't directly combine, but can use string interpolation
combined = /#{prefix.source}.*#{suffix.source}/  # /hello.*world/
```

---

### 11.6.2 Pattern Matching

**Key Similarities:**
- Both find patterns in strings
- Both return match objects with information
- Both support groups and captures

**Key Differences:**
- Ruby has the `=~` operator for quick matching
- Ruby's `match` method returns a MatchData object
- Different method names (`search` vs `match`, `findall` vs `scan`)

#### Python Example

```python
import re

text = "My email is alice@example.com and phone is 123-4567"

# Check if pattern exists
if re.search(r'\d{3}-\d{4}', text):
    print("Found phone number")

# Get match object
match = re.search(r'\w+@\w+\.\w+', text)
if match:
    print(match.group())  # "alice@example.com"
    print(match.start())  # 12
    print(match.end())    # 30

# Find all matches
phones = re.findall(r'\d{3}-\d{4}', text)
print(phones)  # ['123-4567']

# Match from start of string
if re.match(r'My', text):
    print("Starts with 'My'")

# Replace
new_text = re.sub(r'\d{3}-\d{4}', 'XXX-XXXX', text)
print(new_text)  # Email is alice@example.com and phone is XXX-XXXX
```

#### Ruby Equivalent

```ruby
text = "My email is alice@example.com and phone is 123-4567"

# =~ operator (returns position or nil)
if text =~ /\d{3}-\d{4}/
  puts "Found phone number"
end

# match method (returns MatchData object)
match = text.match(/\w+@\w+\.\w+/)
if match
  puts match[0]        # "alice@example.com"
  puts match.begin(0)  # 12
  puts match.end(0)    # 30
end

# scan method (find all matches)
phones = text.scan(/\d{3}-\d{4}/)
puts phones  # ["123-4567"]

# Match from start (use \A anchor)
if text.match(/\AMy/)
  puts "Starts with 'My'"
end

# Or use start_with? for simple cases
if text.start_with?("My")
  puts "Starts with 'My'"
end

# Replace with gsub
new_text = text.gsub(/\d{3}-\d{4}/, 'XXX-XXXX')
puts new_text  # My email is alice@example.com and phone is XXX-XXXX
```

#### Explanation

Ruby provides several ways to match patterns, each with different use cases:

**The `=~` operator:**

```ruby
# Returns position of match (or nil)
position = "hello world" =~ /world/
puts position  # 6

# Commonly used in conditionals
if "hello world" =~ /world/
  puts "Found it!"
end

# Also works reversed
if /world/ =~ "hello world"
  puts "Found it!"
end

# nil if no match (falsy)
if "hello" =~ /xyz/
  puts "This won't print"
end
```

**The `match` method:**

```ruby
# Returns MatchData object or nil
match = "hello world".match(/(\w+) (\w+)/)

if match
  puts match[0]  # "hello world" (entire match)
  puts match[1]  # "hello" (first group)
  puts match[2]  # "world" (second group)
end

# Named matches (covered in next section)
match = "2025-01-07".match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/)
puts match[:year]   # "2025"
puts match[:month]  # "01"
puts match[:day]    # "07"
```

**The `scan` method:**

```ruby
# Find all matches
text = "Phone: 123-4567 and 890-1234"
phones = text.scan(/\d{3}-\d{4}/)
puts phones  # ["123-4567", "890-1234"]

# With captures
text = "Alice:30, Bob:25, Charlie:35"
people = text.scan(/(\w+):(\d+)/)
puts people  # [["Alice", "30"], ["Bob", "25"], ["Charlie", "35"]]

# With block
text.scan(/(\w+):(\d+)/) do |name, age|
  puts "#{name} is #{age} years old"
end
```

**The `match?` method (Ruby 2.4+):**

```ruby
# Just test if it matches (faster than match)
if "hello world".match?(/world/)
  puts "Found it!"
end

# Doesn't create MatchData object (more efficient)
```

**Global variables (legacy feature):**

```ruby
# After =~ match, special variables are set
"hello world" =~ /(\w+) (\w+)/

puts $~  # #<MatchData "hello world" 1:"hello" 2:"world">
puts $1  # "hello" (first capture)
puts $2  # "world" (second capture)
puts $`  # "" (text before match)
puts $'  # "" (text after match)
puts $&  # "hello world" (entire match)

# These are useful but considered old style
# Prefer MatchData object instead
```

---

### 11.6.3 Capture Groups

**Key Similarities:**
- Both use parentheses for capturing groups
- Both support named groups
- Both allow back-references

**Key Differences:**
- Ruby uses `(?<name>)` for named groups (Python uses `(?P<name>)`)
- Different syntax for accessing captured groups
- Ruby's MatchData object has more methods

#### Python Example

```python
import re

text = "Contact: John Doe (john@example.com)"

# Numbered groups
match = re.search(r'(\w+) (\w+) \((\S+)\)', text)
if match:
    print(match.group(0))  # "John Doe (john@example.com)"
    print(match.group(1))  # "John"
    print(match.group(2))  # "Doe"
    print(match.group(3))  # "john@example.com"

# Named groups
pattern = r'(?P<first>\w+) (?P<last>\w+) \((?P<email>\S+)\)'
match = re.search(pattern, text)
if match:
    print(match.group('first'))  # "John"
    print(match.group('last'))   # "Doe"
    print(match.group('email'))  # "john@example.com"
    print(match.groupdict())     # Dict of all named groups

# Back-references in pattern
text2 = "hello hello world"
match = re.search(r'(\w+) \1', text2)  # \1 refers to first group
print(match.group())  # "hello hello"

# Non-capturing group
match = re.search(r'(?:\w+) (\w+)', 'hello world')
print(match.group(1))  # "world" (first group, (?:...) doesn't count)
```

#### Ruby Equivalent

```ruby
text = "Contact: John Doe (john@example.com)"

# Numbered groups
match = text.match(/(\w+) (\w+) \((\S+)\)/)
if match
  puts match[0]  # "John Doe (john@example.com)"
  puts match[1]  # "John"
  puts match[2]  # "Doe"
  puts match[3]  # "john@example.com"
end

# Named groups (use (?<name>) syntax)
pattern = /(?<first>\w+) (?<last>\w+) \((?<email>\S+)\)/
match = text.match(pattern)
if match
  puts match[:first]  # "John"
  puts match[:last]   # "Doe"
  puts match[:email]  # "john@example.com"
  puts match.named_captures  # Hash of named captures
end

# Back-references in pattern
text2 = "hello hello world"
match = text2.match(/(\w+) \1/)  # \1 refers to first group
puts match[0]  # "hello hello"

# Named back-references
match = "hello hello".match(/(?<word>\w+) \k<word>/)
puts match[:word]  # "hello"

# Non-capturing group
match = 'hello world'.match(/(?:\w+) (\w+)/)
puts match[1]  # "world"
```

#### Explanation

Capture groups let you extract parts of a matched pattern. Ruby provides both numbered and named access to groups.

**Numbered groups:**

```ruby
date = "2025-01-07"
match = date.match(/(\d{4})-(\d{2})-(\d{2})/)

puts match[0]  # "2025-01-07" (entire match)
puts match[1]  # "2025" (first group)
puts match[2]  # "01" (second group)
puts match[3]  # "07" (third group)

# Array-like access
year, month, day = match.captures
puts "Year: #{year}, Month: #{month}, Day: #{day}"
```

**Named groups:**

```ruby
date = "2025-01-07"
match = date.match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/)

# Access by name (symbol or string)
puts match[:year]    # "2025"
puts match['month']  # "01"
puts match[:day]     # "07"

# Get all named captures as hash
puts match.named_captures
# {"year"=>"2025", "month"=>"01", "day"=>"07"}

# Destructure
year = match[:year]
month = match[:month]
day = match[:day]
```

**Back-references:**

```ruby
# Numbered back-reference
text = "the the quick quick brown fox"
duplicates = text.scan(/(\w+) \1/)
puts duplicates  # [["the"], ["quick"]]

# Named back-reference
pattern = /(?<word>\w+) \k<word>/
match = "hello hello".match(pattern)
puts match[:word]  # "hello"

# Back-reference in replacement
text = "hello world"
result = text.gsub(/(\w+) (\w+)/, '\2 \1')
puts result  # "world hello"

# Or use block for more control
result = text.gsub(/(\w+) (\w+)/) do |match|
  "#{$2} #{$1}"
end
puts result  # "world hello"
```

**Non-capturing groups:**

```ruby
# Regular group (captures)
match = "http://example.com".match(/(https?):\/\/(\w+\.\w+)/)
puts match[1]  # "http"
puts match[2]  # "example.com"

# Non-capturing group (?:...)
match = "http://example.com".match(/(?:https?):\/\/(\w+\.\w+)/)
puts match[1]  # "example.com" (http not captured)

# Use non-capturing groups when you need grouping but not capture
url_pattern = /(?:https?|ftp):\/\/(\w+\.\w+)/
```

**Advanced grouping:**

```ruby
# Nested groups
match = "Name: John Doe".match(/Name: ((\w+) (\w+))/)
puts match[1]  # "John Doe" (outer group)
puts match[2]  # "John" (first inner group)
puts match[3]  # "Doe" (second inner group)

# Conditional groups and more advanced features
# Ruby supports many Perl-style regex features

# Positive lookahead (?=...)
match = "foo bar".match(/foo(?= bar)/)
puts match[0]  # "foo" (only if followed by " bar")

# Negative lookahead (?!...)
match = "foo baz".match(/foo(?! bar)/)
puts match[0]  # "foo" (only if NOT followed by " bar")

# Positive lookbehind (?<=...)
match = "Mr. Smith".match(/(?<=Mr\. )\w+/)
puts match[0]  # "Smith"

# Negative lookbehind (?<!...)
match = "Ms. Jones".match(/(?<!Mr\. )\w+/)
puts match[0]  # "Ms"
```

**Practical examples:**

```ruby
# Parse email
email = "john.doe@example.com"
match = email.match(/(?<user>[^@]+)@(?<domain>.+)/)
puts "User: #{match[:user]}, Domain: #{match[:domain]}"

# Parse URLs
url = "https://example.com:8080/path/to/page?query=value"
pattern = /(?<protocol>\w+):\/\/(?<host>[^:\/]+)(?::(?<port>\d+))?(?<path>[^?]*)(?:\?(?<query>.*))?/
match = url.match(pattern)
puts match[:protocol]  # "https"
puts match[:host]      # "example.com"
puts match[:port]      # "8080"
puts match[:path]      # "/path/to/page"
puts match[:query]     # "query=value"

# Extract phone numbers with area code
text = "Call me at (555) 123-4567 or (444) 890-1234"
phones = text.scan(/\((\d{3})\) (\d{3}-\d{4})/)
phones.each do |area_code, number|
  puts "#{area_code}-#{number}"
end
```

---

### 11.6.4 `scan`, `match`, `=~`

Ruby provides several methods for working with regular expressions. Understanding when to use each makes your code more idiomatic and efficient.

**Key Similarities:**
- Both find patterns in text
- Both support capture groups
- Both can iterate over matches

**Key Differences:**
- Ruby has the `=~` operator for simple existence checks
- `scan` finds all matches (like `findall`)
- `match` returns detailed MatchData (like `search`)
- Different iteration methods

#### Python Example

```python
import re

text = "The prices are $10, $20, and $30"

# Check if pattern exists
if re.search(r'\$\d+', text):
    print("Found prices")

# Get first match
match = re.search(r'\$(\d+)', text)
if match:
    print(match.group(1))  # "10"

# Find all matches
prices = re.findall(r'\$(\d+)', text)
print(prices)  # ['10', '20', '30']

# Iterate over matches
for match in re.finditer(r'\$(\d+)', text):
    print(f"Found ${match.group(1)} at position {match.start()}")

# Replace
new_text = re.sub(r'\$\d+', '$X', text)
print(new_text)  # "The prices are $X, $X, and $X"

# Replace with function
def increase_price(match):
    price = int(match.group(1))
    return f'${price + 5}'

new_text = re.sub(r'\$(\d+)', increase_price, text)
print(new_text)  # "The prices are $15, $25, and $35"
```

#### Ruby Equivalent

```ruby
text = "The prices are $10, $20, and $30"

# Check if pattern exists (=~ operator)
if text =~ /\$\d+/
  puts "Found prices"
end

# Or use match?
if text.match?(/\$\d+/)
  puts "Found prices"
end

# Get first match (match method)
match = text.match(/\$(\d+)/)
if match
  puts match[1]  # "10"
end

# Find all matches (scan method)
prices = text.scan(/\$(\d+)/)
puts prices  # ["10", "20", "30"]

# scan with block (iterate)
text.scan(/\$(\d+)/) do |price|
  puts "Found $#{price}"
end

# With multiple captures
text.scan(/\$(\d+)/) do |match|
  puts "Price: $#{match[0]}"
end

# Replace (gsub method)
new_text = text.gsub(/\$\d+/, '$X')
puts new_text  # "The prices are $X, $X, and $X"

# Replace with block
new_text = text.gsub(/\$(\d+)/) do |match|
  price = $1.to_i
  "$#{price + 5}"
end
puts new_text  # "The prices are $15, $25, and $35"

# Or access via MatchData
new_text = text.gsub(/\$(\d+)/) do
  price = Regexp.last_match[1].to_i
  "$#{price + 5}"
end
```

#### Explanation

Each method serves a specific purpose:

**`=~` operator - Quick existence check:**

```ruby
# Returns position of match (or nil)
if "hello world" =~ /world/
  puts "Contains 'world'"
end

# Position returned
position = "hello world" =~ /world/
puts position  # 6

# nil (falsy) if no match
unless "hello" =~ /xyz/
  puts "No match found"
end

# Works both ways
/world/ =~ "hello world"  # Also works
```

**`match?` - Boolean check (Ruby 2.4+):**

```ruby
# More efficient than match when you just need true/false
if "hello world".match?(/world/)
  puts "Found it!"
end

# Doesn't create MatchData object (faster)
# Use this instead of =~ when you only need boolean
```

**`match` - Detailed first match:**

```ruby
text = "Order #12345 for $99.99"
match = text.match(/Order #(\d+) for \$(\d+\.\d+)/)

if match
  puts match[0]  # "Order #12345 for $99.99" (full match)
  puts match[1]  # "12345" (order number)
  puts match[2]  # "99.99" (price)

  # MatchData methods
  puts match.begin(0)  # Start position
  puts match.end(0)    # End position
  puts match.string    # Original string
end

# With named groups
match = text.match(/Order #(?<order>\d+) for \$(?<price>\d+\.\d+)/)
puts match[:order]  # "12345"
puts match[:price]  # "99.99"
```

**`scan` - Find all matches:**

```ruby
text = "Colors: red, green, blue, red"

# Simple scan
colors = text.scan(/\w+/)
puts colors  # ["Colors", "red", "green", "blue", "red"]

# With pattern
colors = text.scan(/red|green|blue/)
puts colors  # ["red", "green", "blue", "red"]

# With captures (returns array of arrays)
text = "Users: Alice(25), Bob(30), Charlie(35)"
users = text.scan(/(\w+)\((\d+)\)/)
puts users  # [["Alice", "25"], ["Bob", "30"], ["Charlie", "35"]]

# scan with block
text.scan(/(\w+)\((\d+)\)/) do |name, age|
  puts "#{name} is #{age} years old"
end

# Named captures in scan
text.scan(/(?<name>\w+)\((?<age>\d+)\)/) do |match|
  # match is an array in block form
  puts "#{match[0]} is #{match[1]} years old"
end
```

**`gsub` - Global substitution:**

```ruby
text = "Hello, world! Hello, Ruby!"

# Simple replacement
result = text.gsub(/Hello/, "Hi")
puts result  # "Hi, world! Hi, Ruby!"

# With captures
text = "Date: 2025-01-07"
result = text.gsub(/(\d{4})-(\d{2})-(\d{2})/, '\3/\2/\1')
puts result  # "Date: 07/01/2025"

# With block (more powerful)
text = "Prices: $10, $20, $30"
result = text.gsub(/\$(\d+)/) do |match|
  price = $1.to_i
  discounted = price * 0.9
  "$#{'%.2f' % discounted}"
end
puts result  # "Prices: $9.00, $18.00, $27.00"

# Access MatchData in block
result = text.gsub(/\$(\d+)/) do
  match_data = Regexp.last_match
  price = match_data[1].to_i
  "$#{price * 0.9}"
end
```

**`sub` - Single substitution (first match only):**

```ruby
text = "Hello, Hello, Hello!"

# Replace only first occurrence
result = text.sub(/Hello/, "Hi")
puts result  # "Hi, Hello, Hello!"

# Use gsub to replace all
result = text.gsub(/Hello/, "Hi")
puts result  # "Hi, Hi, Hi!"
```

**Practical examples:**

```ruby
# Extract all emails from text
text = "Contact: alice@example.com or bob@test.org for info"
emails = text.scan(/\w+@\w+\.\w+/)
puts emails  # ["alice@example.com", "bob@test.org"]

# Parse log file
log = "ERROR: Failed at 10:30:15\nWARNING: Slow query at 10:31:42\nERROR: Connection lost at 10:32:00"
errors = log.scan(/ERROR: (.+) at (\d+:\d+:\d+)/)
errors.each do |message, time|
  puts "#{time}: #{message}"
end

# Clean up whitespace
text = "  Too    many    spaces  "
cleaned = text.gsub(/\s+/, ' ').strip
puts cleaned  # "Too many spaces"

# Convert snake_case to camelCase
snake = "hello_world_from_ruby"
camel = snake.gsub(/_(\w)/) { $1.upcase }
puts camel  # "helloWorldFromRuby"

# Validate format
phone = "123-456-7890"
if phone.match?(/\A\d{3}-\d{3}-\d{4}\z/)
  puts "Valid phone number"
end

# Extract and process
html = "<div>Hello</div><span>World</span><div>Ruby</div>"
divs = html.scan(/<div>(.*?)<\/div>/).flatten
puts divs  # ["Hello", "Ruby"]
```

**Common pitfall:** Using `match` when you need all matches:

```ruby
text = "Prices: $10, $20, $30"

# Wrong: only gets first match
match = text.match(/\$(\d+)/)
puts match[1]  # "10" only

# Right: use scan for all matches
prices = text.scan(/\$(\d+)/)
puts prices  # ["10", "20", "30"]
```

---

This chapter covered comprehensive string handling in Ruby:

- **String literals** - Understanding single vs double quotes
- **String interpolation** - Ruby's `#{}` syntax vs Python's f-strings
- **Heredocs** - Multiline strings with `<<~` for clean formatting
- **String methods** - Rich set of transformation and query methods
- **Encoding** - Working with UTF-8 and other encodings
- **Regular expressions** - First-class regex support with literals, matching, and powerful methods

Ruby's string handling is both powerful and elegant. The deep integration of regex, flexible interpolation, and rich method library make text processing a strength of the language. As you work with Ruby, you'll find string manipulation to be intuitive and expressive.
