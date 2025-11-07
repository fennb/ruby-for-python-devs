# Chapter 3: Control Flow

Control flow in Ruby will feel familiar if you know Python, but there are some delightful syntactic differences and Ruby-specific features that make code more expressive. This chapter covers conditionals, boolean operators, and Ruby's unique `unless` keyword.

---

## 3.1 `if`/`elsif`/`else` vs `if`/`elif`/`else`

Ruby's conditional statements work similarly to Python's, with two key syntax differences: Ruby uses `elsif` instead of `elif`, and Ruby uses `end` to close blocks instead of relying on indentation.

**Key Similarities:**
- Both evaluate conditions from top to bottom
- Both support multiple conditional branches
- Both use `else` for the default case

**Key Differences:**
- Ruby uses `elsif` (no second 'e'!) instead of Python's `elif`
- Ruby blocks end with `end` keyword, not indentation
- Ruby doesn't require parentheses around conditions
- Ruby doesn't require a colon after conditions

### Python Example

```python
temperature = 75

if temperature > 85:
    print("It's hot!")
elif temperature > 65:
    print("It's pleasant.")
elif temperature > 45:
    print("It's cool.")
else:
    print("It's cold!")

# With multiple conditions
age = 25
has_license = True

if age >= 16 and has_license:
    print("You can drive")
elif age >= 16:
    print("You need a license first")
else:
    print("You're too young to drive")
```

### Ruby Equivalent

```ruby
temperature = 75

if temperature > 85
  puts "It's hot!"
elsif temperature > 65
  puts "It's pleasant."
elsif temperature > 45
  puts "It's cool."
else
  puts "It's cold!"
end

# With multiple conditions
age = 25
has_license = true

if age >= 16 && has_license
  puts "You can drive"
elsif age >= 16
  puts "You need a license first"
else
  puts "You're too young to drive"
end
```

### Explanation

Ruby's `if` statements don't require parentheses around the condition (though you can use them if you prefer). Each conditional block must be terminated with the `end` keyword.

**Important gotcha:** It's `elsif`, not `elseif` or `elif`. This is one of the most common mistakes Python developers make when learning Ruby.

**Best Practices:**
- Omit parentheses around conditions (unless needed for clarity)
- Use 2-space indentation (Ruby convention)
- Always close with `end`

Ruby also allows you to write `if` statements as one-liners:

```ruby
puts "It's hot!" if temperature > 85
```

This is called a **statement modifier** and we'll cover it in detail in the next section.

---

## 3.2 Statement Modifiers (Postfix `if`/`unless`)

One of Ruby's most elegant features is the ability to write conditionals as statement modifiers - placing the `if` or `unless` at the end of a statement. This creates more readable code for simple conditions.

**Key Concept:**
- Statement modifiers let you write: `action if condition`
- This reads more naturally in English: "Do this action, if this condition is true"
- Python has no direct equivalent (though list comprehensions with `if` are somewhat similar)

**When to Use:**
- For simple, single-line conditionals
- When the action is more important than the condition
- To reduce visual noise from `if`/`end` blocks

### Python Example

```python
# Python requires the standard if syntax
user = {"name": "Alice", "admin": True}

if user["admin"]:
    print("Access granted")

# Or sometimes ternary operator
print("Access granted") if user["admin"] else None

# Common pattern: guard clause
def process_data(data):
    if data is None:
        return

    # process data...
    print(f"Processing {data}")

process_data("important info")
process_data(None)
```

### Ruby Equivalent

```ruby
# Ruby's statement modifier syntax
user = { name: "Alice", admin: true }

puts "Access granted" if user[:admin]

# Guard clause becomes much cleaner
def process_data(data)
  return if data.nil?

  # process data...
  puts "Processing #{data}"
end

process_data("important info")
process_data(nil)

# More examples
items = ["apple", "banana", "cherry"]

puts "We have items!" if items.any?
puts "List is empty" if items.empty?

# With variables
count = 42
puts "Count is: #{count}" if count > 0

# Even method calls
user.save if user.valid?
```

### Explanation

Statement modifiers execute the statement on the left **only if** the condition on the right is true. They work with both `if` and `unless` (covered in the next section).

**When to use statement modifiers:**
- The condition is simple and clear
- The action is a single statement
- Reading it as an English sentence makes sense

**When NOT to use them:**
- Multiple statements need to execute
- The condition is complex or long
- You need an `else` clause

**Important:** Statement modifiers cannot have `elsif` or `else` clauses. If you need those, use a regular `if` block.

```ruby
# Good uses
log_message if debug_mode
send_email unless user.unsubscribed?
return if invalid_input

# Bad uses (too complex)
calculate_complex_result(data, options) if user.admin? && feature_enabled? && !maintenance_mode?
```

---

## 3.3 `unless` (Ruby-specific)

Ruby's `unless` keyword is the logical opposite of `if`. It executes code when a condition is **false**. While you could always use `if !condition`, `unless` often reads more naturally in English.

**Key Concept:**
- `unless condition` is equivalent to `if !condition` or `if not condition`
- Python has no `unless` keyword
- Works as both a block statement and a statement modifier

**Philosophy:** Ruby emphasizes readability and "code that reads like English." Sometimes "unless" expresses intent more clearly than "if not."

### Python Example

```python
# Python uses 'if not' or 'if !'
logged_in = False

if not logged_in:
    print("Please log in")

# More examples
user = {"status": "inactive"}

if user["status"] != "active":
    print("Account is not active")

# Double negatives can be confusing
email_verified = False

if not email_verified:
    print("Please verify your email")
```

### Ruby Equivalent

```ruby
# Ruby's 'unless' keyword
logged_in = false

unless logged_in
  puts "Please log in"
end

# More examples
user = { status: "inactive" }

unless user[:status] == "active"
  puts "Account is not active"
end

# Much clearer than 'if not'
email_verified = false

unless email_verified
  puts "Please verify your email"
end

# Statement modifier form (very common)
puts "Please log in" unless logged_in
redirect_to_home unless user.admin?
create_account unless account_exists?

# Real-world examples
def withdraw(amount)
  return unless amount > 0
  return unless balance >= amount

  balance -= amount
end
```

### Explanation

Use `unless` when checking for the **absence** or **falsehood** of something. It often makes code more readable than `if !condition`.

**Best Practices:**

**Good uses of `unless`:**
```ruby
unless user.nil?
  user.greet
end

save_data unless file_exists?
send_reminder unless user.notified?
```

**Avoid `unless` with `else`:**
```ruby
# Confusing - use 'if' instead
unless user.admin?
  puts "Access denied"
else
  puts "Access granted"
end

# Better with 'if'
if user.admin?
  puts "Access granted"
else
  puts "Access denied"
end
```

**Avoid `unless` with complex conditions:**
```ruby
# Hard to read
unless user.admin? || user.moderator? && !user.banned?
  deny_access
end

# Better
if !user.admin? && (!user.moderator? || user.banned?)
  deny_access
end

# Even better - refactor into a method
deny_access unless user.has_access?
```

**Rule of thumb:** If you find yourself using `&&`, `||`, or `!` with `unless`, consider using `if` instead.

---

## 3.4 Ternary Operator

Both Ruby and Python support the ternary operator for simple conditional expressions, but with completely different syntax. Ruby's ternary operator looks like C/JavaScript, while Python uses a more English-like syntax.

**Key Difference:**
- Python: `value_if_true if condition else value_if_false`
- Ruby: `condition ? value_if_true : value_if_false`

### Python Example

```python
# Python's ternary syntax
age = 20
status = "adult" if age >= 18 else "minor"
print(status)  # Output: adult

# Nested ternary (not recommended but possible)
score = 85
grade = "A" if score >= 90 else "B" if score >= 80 else "C"
print(grade)  # Output: B

# In function calls
def greet(name, formal=False):
    greeting = "Good day" if formal else "Hey"
    return f"{greeting}, {name}!"

print(greet("Alice", formal=True))   # Output: Good day, Alice!
print(greet("Bob", formal=False))    # Output: Hey, Bob!

# Common use cases
max_value = a if a > b else b
message = "Empty" if len(items) == 0 else "Has items"
color = user.get("theme", "light") if user else "light"
```

### Ruby Equivalent

```ruby
# Ruby's ternary syntax
age = 20
status = age >= 18 ? "adult" : "minor"
puts status  # Output: adult

# Nested ternary (not recommended but possible)
score = 85
grade = score >= 90 ? "A" : score >= 80 ? "B" : "C"
puts grade  # Output: B

# In function calls
def greet(name, formal: false)
  greeting = formal ? "Good day" : "Hey"
  "#{greeting}, #{name}!"
end

puts greet("Alice", formal: true)   # Output: Good day, Alice!
puts greet("Bob", formal: false)    # Output: Hey, Bob!

# Common use cases
max_value = a > b ? a : b
message = items.length == 0 ? "Empty" : "Has items"
color = user ? user[:theme] || "light" : "light"

# With method calls
result = user.valid? ? user.save : false
display_name = user.name.empty? ? "Anonymous" : user.name
```

### Explanation

Ruby's ternary operator follows the traditional `condition ? true_value : false_value` syntax used in C, Java, and JavaScript. Python developers will need to adjust to this different syntax.

**Format breakdown:**
```ruby
condition ? value_if_true : value_if_false
```

**Best Practices:**

**Good uses (simple conditions):**
```ruby
status = active ? "online" : "offline"
label = count == 1 ? "item" : "items"
price = premium ? 99.99 : 9.99
```

**Avoid complex nested ternaries:**
```ruby
# Hard to read
result = a > b ? a > c ? a : c : b > c ? b : c

# Better - use if/elsif/else
if a > b && a > c
  result = a
elsif b > c
  result = b
else
  result = c
end

# Best - extract to a method
result = max_of_three(a, b, c)
```

**Readability guideline:** If your ternary expression doesn't fit comfortably on one line, use an `if/else` block instead.

**Common pattern - nil checking:**
```ruby
# Setting defaults
name = user.name ? user.name : "Guest"

# Better - use the || operator (covered in section 3.6)
name = user.name || "Guest"
```

---

## 3.5 `case`/`when` vs `match`/`case` (or `if`/`elif`)

Ruby's `case` statement is similar to Python's newer `match`/`case` (3.10+) or traditional `if`/`elif` chains. It provides a clean way to handle multiple conditions against a single value.

**Key Similarities:**
- Both test a value against multiple possibilities
- Both execute only the first matching branch
- Both support a default case

**Key Differences:**
- Ruby uses `case`/`when`/`else`/`end`
- Ruby's `when` can match multiple values in one line
- Ruby uses `===` (case equality operator) for matching, which enables powerful pattern matching
- No `break` needed in Ruby (unlike C/JavaScript)

### Python Example

```python
# Python 3.10+ match/case
status_code = 404

match status_code:
    case 200:
        message = "OK"
    case 404:
        message = "Not Found"
    case 500:
        message = "Internal Server Error"
    case _:
        message = "Unknown Status"

print(message)

# Traditional if/elif (works in all Python versions)
if status_code == 200:
    message = "OK"
elif status_code == 404:
    message = "Not Found"
elif status_code == 500:
    message = "Internal Server Error"
else:
    message = "Unknown Status"

# Matching multiple values
grade = 'B'

if grade in ['A', 'B']:
    print("Excellent or Good")
elif grade in ['C', 'D']:
    print("Average or Below Average")
elif grade == 'F':
    print("Failing")
else:
    print("Invalid grade")

# Pattern matching with types (Python 3.10+)
def describe(value):
    match value:
        case int():
            return "It's an integer"
        case str():
            return "It's a string"
        case list():
            return "It's a list"
        case _:
            return "It's something else"
```

### Ruby Equivalent

```ruby
# Ruby case/when
status_code = 404

message = case status_code
when 200
  "OK"
when 404
  "Not Found"
when 500
  "Internal Server Error"
else
  "Unknown Status"
end

puts message

# Note: Ruby's case returns a value, so you can assign it directly
# The last expression in each branch is returned

# Matching multiple values (comma-separated)
grade = 'B'

case grade
when 'A', 'B'
  puts "Excellent or Good"
when 'C', 'D'
  puts "Average or Below Average"
when 'F'
  puts "Failing"
else
  puts "Invalid grade"
end

# Matching with ranges
score = 85

case score
when 90..100
  puts "A"
when 80..89
  puts "B"
when 70..79
  puts "C"
when 60..69
  puts "D"
else
  puts "F"
end

# Matching with types/classes
def describe(value)
  case value
  when Integer
    "It's an integer"
  when String
    "It's a string"
  when Array
    "It's an array"
  else
    "It's something else"
  end
end

puts describe(42)        # Output: It's an integer
puts describe("hello")   # Output: It's a string
puts describe([1, 2])    # Output: It's an array

# Matching with regex
input = "hello@example.com"

case input
when /^[a-z]+$/
  puts "Lowercase letters only"
when /\A[\w+\-.]+@[a-z\d\-.]+\.[a-z]+\z/i
  puts "Looks like an email"
when /^\d+$/
  puts "Numbers only"
else
  puts "Something else"
end
# Output: Looks like an email
```

### Explanation

Ruby's `case` statement is more powerful than it might first appear. The key is understanding that it uses the `===` operator (case equality) for comparisons, not `==`.

**How `===` works:**
- `Integer === 42` returns `true` (class matches instance)
- `(1..10) === 5` returns `true` (range includes value)
- `/hello/ === "hello world"` returns `true` (regex matches string)
- `"hello" === "hello"` returns `true` (string equality)

**Important points:**

1. **No fall-through:** Unlike C or JavaScript, Ruby doesn't fall through to the next case. Only the first matching branch executes.

2. **Returns a value:** `case` is an expression that returns the last evaluated expression:
```ruby
grade = case score
when 90..100 then "A"
when 80..89  then "B"
when 70..79  then "C"
else "F"
end
```

3. **`then` keyword:** You can use `then` for one-liners:
```ruby
case day
when "Monday" then puts "Start of week"
when "Friday" then puts "Almost weekend!"
end
```

4. **Without a value:** You can use `case` without an initial value for complex conditions:
```ruby
case
when temperature > 85
  puts "Hot"
when temperature > 65
  puts "Pleasant"
when temperature > 45
  puts "Cool"
else
  puts "Cold"
end
```

This is equivalent to `if`/`elsif` chains but some Rubyists find it more readable.

**Best Practices:**
- Use `case` for checking a single value against multiple possibilities
- Use comma-separated values for multiple matches in one branch
- Leverage ranges, regex, and classes for powerful pattern matching
- Use `case` without a value sparingly (prefer `if`/`elsif` for clarity)

---

## 3.6 Boolean Operators (`&&`/`||` vs `and`/`or`)

Ruby has TWO sets of boolean operators: `&&`/`||`/`!` and `and`/`or`/`not`. While they seem similar, they have different precedence levels, which can lead to subtle bugs if you're not careful.

**Key Difference from Python:**
- Python has `and`, `or`, `not`
- Ruby has BOTH `&&`, `||`, `!` AND `and`, `or`, `not`
- They are NOT interchangeable in Ruby!

**Important:** The word-based operators (`and`, `or`, `not`) have very low precedence. Most Rubyists use the symbolic operators (`&&`, `||`, `!`) almost exclusively.

### Python Example

```python
# Python boolean operators
is_admin = True
is_logged_in = True
is_verified = False

# Using 'and', 'or', 'not'
if is_admin and is_logged_in:
    print("Full access granted")

if is_admin or is_verified:
    print("Some access granted")

if not is_verified:
    print("Please verify your account")

# Short-circuit evaluation
def expensive_check():
    print("Running expensive check...")
    return True

# expensive_check() is not called because first condition is False
if False and expensive_check():
    print("This won't print")

# Using 'or' for default values
name = ""
display_name = name or "Anonymous"
print(display_name)  # Output: Anonymous

# Combining operators
age = 25
has_license = True
has_insurance = True

if age >= 16 and has_license and has_insurance:
    print("You can drive")

# Truthiness in Python
if []:
    print("Won't print - empty list is falsy")

if [1, 2, 3]:
    print("Will print - non-empty list is truthy")
```

### Ruby Equivalent

```ruby
# Ruby boolean operators (symbolic - USE THESE)
is_admin = true
is_logged_in = true
is_verified = false

# Using &&, ||, !
if is_admin && is_logged_in
  puts "Full access granted"
end

if is_admin || is_verified
  puts "Some access granted"
end

if !is_verified
  puts "Please verify your account"
end

# Short-circuit evaluation
def expensive_check
  puts "Running expensive check..."
  true
end

# expensive_check is not called because first condition is false
if false && expensive_check
  puts "This won't print"
end

# Using || for default values
name = ""
display_name = name || "Anonymous"
puts display_name  # Output: Anonymous

# But watch out - empty string is truthy in Ruby!
name = ""
if name
  puts "This WILL print because empty string is truthy in Ruby!"
end

# Combining operators
age = 25
has_license = true
has_insurance = true

if age >= 16 && has_license && has_insurance
  puts "You can drive"
end

# Truthiness in Ruby - only false and nil are falsy
if []
  puts "WILL print - empty array is truthy in Ruby!"
end

if [1, 2, 3]
  puts "Will print - non-empty array is truthy"
end
```

### The Difference Between `&&`/`||` and `and`/`or`

Here's where Ruby gets tricky. The word-based operators have much lower precedence:

```ruby
# DANGEROUS - using 'and' with assignment
result = true and false
puts result  # Output: true (!)
# Parsed as: (result = true) and false
# The assignment happens first, then 'and' operates on the result

# CORRECT - using && with assignment
result = true && false
puts result  # Output: false
# Parsed as: result = (true && false)
# The && operates first, then assignment

# Another example of the precedence trap
def authenticate
  puts "Authenticating..."
  true
end

# Using 'or' - WRONG
logged_in = authenticate or redirect_to_login
# Parsed as: (logged_in = authenticate) or redirect_to_login
# redirect_to_login might not get called!

# Using || - CORRECT
logged_in = authenticate || redirect_to_login
# Parsed as: logged_in = (authenticate || redirect_to_login)

# When 'and'/'or' ARE useful - control flow
file = File.open("data.txt") or raise "Cannot open file"
# This reads nicely: "open the file OR raise an error"

user.valid? and user.save
# This reads nicely: "if user is valid AND save"
```

### Explanation

**Precedence comparison:**
```ruby
# && and || bind tighter than =
result = true && false  # result = (true && false) = false

# 'and' and 'or' bind looser than =
result = true and false  # (result = true) and false; result = true
```

**When to use word-based operators:**
Use `and`/`or`/`not` for control flow (rarely):
```ruby
# These read nicely as English
document.save or raise "Save failed"
user.admin? and log_admin_access
```

**When to use symbolic operators:**
Use `&&`/`||`/`!` for everything else (95% of the time):
```ruby
# Conditions
if user.active? && user.verified?
  grant_access
end

# Default values
name = user.name || "Guest"

# Boolean logic
can_edit = is_owner || is_admin && !is_locked
```

**Critical gotcha for Python developers:**

Ruby's truthiness is DIFFERENT from Python's:
```ruby
# In Python, these are falsy: False, None, 0, "", [], {}
# In Ruby, ONLY false and nil are falsy!

if 0
  puts "This prints! 0 is truthy in Ruby"
end

if ""
  puts "This prints! Empty string is truthy in Ruby"
end

if []
  puts "This prints! Empty array is truthy in Ruby"
end

# Only these are falsy
if false
  puts "Won't print"
end

if nil
  puts "Won't print"
end
```

**Best Practices:**

1. **Default to symbolic operators:** Use `&&`, `||`, `!` unless you have a specific reason not to.

2. **Use `||` for default values:**
```ruby
name = user.name || "Guest"
config = options[:config] || default_config
```

3. **Be careful with empty strings:**
```ruby
# Python way (doesn't work in Ruby)
name = ""
display_name = name || "Anonymous"  # "Anonymous"

# If you want to check for empty strings in Ruby
display_name = name.empty? ? "Anonymous" : name
# Or use Rails' .presence method (if available)
display_name = name.presence || "Anonymous"
```

4. **Avoid `and`/`or` until you're comfortable with precedence rules.**

---

## Chapter 3 Summary

You've now learned Ruby's control flow features:

- **if/elsif/else:** Similar to Python but uses `elsif` and `end`
- **Statement modifiers:** Ruby's elegant `action if condition` syntax
- **unless:** Ruby-specific keyword for negative conditions
- **Ternary operator:** `condition ? true_val : false_val` (different from Python's syntax)
- **case/when:** Powerful pattern matching using `===` operator
- **Boolean operators:** Use `&&`/`||`/`!` for logic; avoid `and`/`or`/`not` until you understand precedence

**Key takeaways for Python developers:**
1. Remember: it's `elsif`, not `elif`
2. Every block ends with `end`
3. Only `false` and `nil` are falsy (unlike Python's many falsy values)
4. Embrace statement modifiers for cleaner code
5. Use `unless` for negative conditions, but avoid it with `else`
6. Stick with `&&`/`||`/`!` and avoid `and`/`or`/`not`

In the next chapter, we'll explore loops and iteration, where you'll discover Ruby's beautiful iterator methods that often replace traditional loops entirely.
