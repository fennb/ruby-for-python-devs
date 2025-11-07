# Chapter 13: Error Handling

## 13.1 `begin`/`rescue`/`ensure` vs `try`/`except`/`finally`

Error handling in Ruby uses `begin`/`rescue`/`ensure`, which is functionally equivalent to Python's `try`/`except`/`finally`. The concepts are the same, but the syntax and some details differ.

**Key Similarities:**
- Both catch and handle exceptions
- Both have a "finally" clause for cleanup (`ensure` vs `finally`)
- Both support multiple exception types
- Both can re-raise exceptions

**Key Differences:**
- Different keywords (`rescue` vs `except`, `ensure` vs `finally`)
- Ruby can use `rescue` as a statement modifier (inline)
- Ruby methods have implicit `begin`, so `rescue` can be used directly in method bodies
- Different exception hierarchy and naming

### Python Example

```python
# Basic exception handling
try:
    result = 10 / 0
except ZeroDivisionError:
    print("Cannot divide by zero")

# Multiple exception types
try:
    file = open('nonexistent.txt')
    content = file.read()
except FileNotFoundError:
    print("File not found")
except PermissionError:
    print("Permission denied")

# Catch multiple exceptions together
try:
    risky_operation()
except (ValueError, TypeError) as e:
    print(f"Error: {e}")

# Catch all exceptions
try:
    dangerous_operation()
except Exception as e:
    print(f"Something went wrong: {e}")

# Finally clause (always executes)
try:
    file = open('data.txt')
    process(file)
except Exception as e:
    print(f"Error: {e}")
finally:
    file.close()  # Always executes

# Else clause (executes if no exception)
try:
    result = calculate()
except ValueError:
    print("Calculation failed")
else:
    print(f"Success: {result}")
finally:
    print("Cleanup")
```

### Ruby Equivalent

```ruby
# Basic exception handling
begin
  result = 10 / 0
rescue ZeroDivisionError
  puts "Cannot divide by zero"
end

# Multiple exception types
begin
  file = File.open('nonexistent.txt')
  content = file.read
rescue Errno::ENOENT
  puts "File not found"
rescue Errno::EACCES
  puts "Permission denied"
end

# Catch multiple exceptions together
begin
  risky_operation
rescue ValueError, TypeError => e
  puts "Error: #{e}"
end

# Catch all exceptions (StandardError, not Exception!)
begin
  dangerous_operation
rescue => e  # Shorthand for rescue StandardError
  puts "Something went wrong: #{e}"
end

# Ensure clause (always executes)
begin
  file = File.open('data.txt')
  process(file)
rescue => e
  puts "Error: #{e}"
ensure
  file.close if file  # Always executes
end

# Else clause (executes if no exception)
begin
  result = calculate
rescue ValueError
  puts "Calculation failed"
else
  puts "Success: #{result}"
ensure
  puts "Cleanup"
end

# Implicit begin in methods
def process_file(filename)
  file = File.open(filename)
  process(file)
rescue Errno::ENOENT
  puts "File not found"
ensure
  file.close if file
end
```

### Explanation

Ruby's exception handling is similar to Python's, but with some syntactic differences and Ruby-specific features.

**Basic rescue:**

```ruby
begin
  # Code that might raise an exception
  risky_operation
rescue
  # Handle any StandardError
  puts "Something went wrong"
end

# Capture exception object
begin
  risky_operation
rescue => e
  puts "Error: #{e.message}"
  puts "Backtrace: #{e.backtrace.first}"
end

# Specify exception type
begin
  File.open('missing.txt')
rescue Errno::ENOENT => e
  puts "File not found: #{e}"
end
```

**Multiple rescue clauses:**

```ruby
begin
  risky_operation
rescue ZeroDivisionError
  puts "Division by zero"
rescue ArgumentError
  puts "Invalid argument"
rescue => e
  puts "Other error: #{e}"
end

# Catch multiple types in one rescue
begin
  risky_operation
rescue ArgumentError, TypeError => e
  puts "Argument or type error: #{e}"
end
```

**Ensure clause (like finally):**

```ruby
# Ensure always executes
begin
  file = File.open('data.txt')
  process(file)
rescue => e
  puts "Error: #{e}"
ensure
  file.close if file
  puts "This always runs"
end

# Even if no exception occurs
begin
  puts "Normal operation"
ensure
  puts "Cleanup happens"
end

# Even if exception is re-raised
begin
  raise "Error"
rescue => e
  puts "Caught: #{e}"
  raise  # Re-raise
ensure
  puts "Still runs before re-raising"
end
```

**Else clause:**

```ruby
# Else executes only if no exception
begin
  result = calculate_something
rescue ArgumentError => e
  puts "Bad argument: #{e}"
else
  puts "Success! Result: #{result}"
ensure
  puts "Cleanup"
end

# Order: begin -> (rescue if error) -> (else if no error) -> ensure
```

**Implicit begin in methods:**

```ruby
# Methods have implicit begin
def read_file(filename)
  File.read(filename)
rescue Errno::ENOENT
  "File not found"
rescue => e
  "Error: #{e.message}"
end

# No need for begin...end
def divide(a, b)
  a / b
rescue ZeroDivisionError
  puts "Cannot divide by zero"
  nil
ensure
  puts "Division attempted"
end
```

**Rescue as statement modifier (inline rescue):**

```ruby
# Inline rescue for simple cases
result = risky_operation rescue "default value"

# Equivalent to:
begin
  result = risky_operation
rescue
  result = "default value"
end

# Common pattern for defaults
config = File.read('config.yml') rescue '{}'

# Parse with fallback
value = Integer(input) rescue 0

# Multiple statements (use parentheses)
result = (operation1; operation2) rescue "failed"
```

**Getting exception details:**

```ruby
begin
  raise "Something went wrong"
rescue => e
  puts e.class          # RuntimeError
  puts e.message        # "Something went wrong"
  puts e.backtrace      # Array of stack trace strings
  puts e.backtrace.join("\n")

  # Full exception info
  puts e.full_message
end
```

**Re-raising exceptions:**

```ruby
# Re-raise the same exception
begin
  risky_operation
rescue => e
  log_error(e)
  raise  # Re-raise the caught exception
end

# Raise a different exception
begin
  risky_operation
rescue => e
  raise CustomError, "Failed: #{e.message}"
end
```

**Practical examples:**

```ruby
# File operations with cleanup
def process_file(filename)
  file = File.open(filename)
  data = file.read
  process(data)
rescue Errno::ENOENT
  puts "File not found: #{filename}"
  nil
rescue => e
  puts "Error processing file: #{e.message}"
  nil
ensure
  file.close if file
end

# Network request with error handling
def fetch_data(url)
  response = HTTP.get(url)
  JSON.parse(response.body)
rescue HTTP::Error => e
  puts "Network error: #{e}"
  {}
rescue JSON::ParserError => e
  puts "Invalid JSON: #{e}"
  {}
end

# Database operation with rollback
def save_user(user_data)
  db.begin_transaction
  user = User.create(user_data)
  db.commit
  user
rescue ActiveRecord::RecordInvalid => e
  db.rollback
  puts "Validation failed: #{e.message}"
  nil
rescue => e
  db.rollback
  puts "Database error: #{e.message}"
  nil
end

# Multiple file processing
def process_files(filenames)
  filenames.each do |filename|
    begin
      content = File.read(filename)
      process(content)
    rescue => e
      puts "Error with #{filename}: #{e.message}"
      next  # Continue with next file
    end
  end
end
```

**Common pitfall:** Rescuing `Exception` instead of `StandardError`:

```ruby
# Wrong - catches system exceptions too
begin
  operation
rescue Exception => e  # Don't do this!
  puts "Error: #{e}"
end

# Right - rescue StandardError (the default)
begin
  operation
rescue => e  # Catches StandardError and subclasses
  puts "Error: #{e}"
end

# Or explicitly
begin
  operation
rescue StandardError => e
  puts "Error: #{e}"
end
```

---

## 13.2 Raising Exceptions

Raising exceptions in Ruby is similar to Python, but uses the `raise` keyword instead of `raise` in Python. Ruby provides several ways to raise exceptions with varying levels of detail.

**Key Similarities:**
- Both use raise/raise to throw exceptions
- Both support custom exception messages
- Both can create exception instances

**Key Differences:**
- Ruby uses `raise` (Python also uses `raise` but slightly different syntax)
- Different ways to specify exception class and message
- Ruby can re-raise without arguments
- Ruby uses `fail` as an alias for `raise`

### Python Example

```python
# Raise with message
raise ValueError("Invalid input")

# Raise without message
raise ValueError

# Raise from exception class
raise ValueError()

# Re-raise caught exception
try:
    risky_operation()
except Exception as e:
    log_error(e)
    raise  # Re-raise

# Raise with cause (Python 3)
try:
    operation()
except ValueError as e:
    raise RuntimeError("Operation failed") from e

# Create and raise
error = ValueError("Bad value")
raise error

# Conditional raise
if value < 0:
    raise ValueError("Value must be positive")
```

### Ruby Equivalent

```ruby
# Raise with message
raise ArgumentError, "Invalid input"

# Alternative syntax
raise ArgumentError.new("Invalid input")

# Raise without message
raise ArgumentError

# Re-raise caught exception
begin
  risky_operation
rescue => e
  log_error(e)
  raise  # Re-raise the same exception
end

# Raise with cause (Ruby 2.1+)
begin
  operation
rescue ArgumentError => e
  raise RuntimeError, "Operation failed: #{e.message}"
end

# Create and raise
error = ArgumentError.new("Bad value")
raise error

# Conditional raise
raise ArgumentError, "Value must be positive" if value < 0

# Using fail (alias for raise)
fail "Something went wrong"
```

### Explanation

Ruby provides multiple syntaxes for raising exceptions, all using the `raise` keyword (or its alias `fail`).

**Basic raise syntax:**

```ruby
# Syntax 1: raise ExceptionClass, message
raise ArgumentError, "Invalid argument"

# Syntax 2: raise ExceptionClass.new(message)
raise ArgumentError.new("Invalid argument")

# Syntax 3: raise message (creates RuntimeError)
raise "Something went wrong"  # RuntimeError

# Syntax 4: raise (re-raises current exception)
begin
  operation
rescue => e
  log(e)
  raise  # Re-raise e
end

# No arguments creates RuntimeError with no message
raise  # RuntimeError: (no message)
```

**Raising built-in exceptions:**

```ruby
# ArgumentError - wrong number/type of arguments
raise ArgumentError, "Expected 2 arguments, got #{args.length}"

# RuntimeError - generic error
raise "Something went wrong"  # Creates RuntimeError

# StandardError - base class for normal errors
raise StandardError, "Generic error"

# TypeError - wrong type
raise TypeError, "Expected String, got #{obj.class}"

# NameError - undefined variable/constant
raise NameError, "Undefined variable: #{name}"

# IOError - I/O operation failed
raise IOError, "Cannot read file"

# ZeroDivisionError - division by zero
raise ZeroDivisionError, "Cannot divide by zero"

# IndexError - invalid index
raise IndexError, "Index out of bounds: #{index}"

# KeyError - missing hash key
raise KeyError, "Key not found: #{key}"

# NoMethodError - method doesn't exist
raise NoMethodError, "Undefined method: #{method_name}"
```

**Conditional raising:**

```ruby
# Inline if
raise ArgumentError, "Invalid age" if age < 0

# Unless
raise "Empty array" unless array.any?

# Early return pattern (guard clause)
def process(value)
  raise ArgumentError, "Value cannot be nil" if value.nil?
  raise ArgumentError, "Value must be positive" unless value > 0

  # Process value
  value * 2
end

# Ternary
age < 0 ? (raise ArgumentError, "Invalid age") : age
```

**Raise with custom message:**

```ruby
# Format message
raise ArgumentError, "Expected value between #{min} and #{max}, got #{value}"

# Multiline message
raise ArgumentError, <<~MSG
  Invalid configuration:
  - Name cannot be blank
  - Age must be positive
  - Email must be valid
MSG

# Message from exception object
error = ArgumentError.new("Bad input")
error.set_backtrace(caller)  # Set custom backtrace
raise error
```

**Re-raising exceptions:**

```ruby
# Re-raise the same exception
begin
  operation
rescue => e
  log_error(e)
  raise  # Re-raise e without modification
end

# Modify and re-raise
begin
  operation
rescue ArgumentError => e
  e.set_backtrace(caller)  # Modify backtrace
  raise e
end

# Wrap in new exception
begin
  operation
rescue ArgumentError => e
  raise RuntimeError, "Failed to complete operation: #{e.message}"
end

# Chain exceptions (preserve cause)
begin
  operation
rescue ArgumentError => e
  new_error = RuntimeError.new("Operation failed")
  new_error.set_backtrace(e.backtrace)
  raise new_error
end
```

**Using fail vs raise:**

```ruby
# fail is an alias for raise
fail "Something went wrong"

# Convention: use fail for unrecoverable errors
def critical_operation
  fail "Critical failure" unless system_healthy?
  # ...
end

# Convention: use raise for recoverable errors
def validate_input(input)
  raise ArgumentError, "Invalid input" unless valid?(input)
  # ...
end

# But they're exactly the same - just conventions
```

**Practical examples:**

```ruby
# Validation
def create_user(name, email, age)
  raise ArgumentError, "Name cannot be blank" if name.to_s.empty?
  raise ArgumentError, "Invalid email format" unless email =~ /@/
  raise ArgumentError, "Age must be 18 or older" if age < 18

  User.new(name, email, age)
end

# Preconditions
def divide(a, b)
  raise ArgumentError, "Arguments must be numbers" unless a.is_a?(Numeric) && b.is_a?(Numeric)
  raise ZeroDivisionError, "Cannot divide by zero" if b.zero?

  a / b.to_f
end

# State validation
class Account
  def withdraw(amount)
    raise ArgumentError, "Amount must be positive" if amount <= 0
    raise "Insufficient funds" if amount > @balance

    @balance -= amount
  end
end

# Not implemented
def future_feature
  raise NotImplementedError, "This feature is not yet implemented"
end

# Abstract method
class Animal
  def speak
    raise NotImplementedError, "Subclasses must implement speak method"
  end
end

# Configuration error
def load_config(file)
  raise "Config file not found: #{file}" unless File.exist?(file)

  config = YAML.load_file(file)
  raise "Invalid config: missing required key 'database'" unless config['database']

  config
end

# Retry with exception
def fetch_with_retry(url, max_attempts = 3)
  attempts = 0

  begin
    attempts += 1
    HTTP.get(url)
  rescue HTTP::Error => e
    raise if attempts >= max_attempts
    sleep(2 ** attempts)  # Exponential backoff
    retry
  end
end
```

**Raising with backtrace:**

```ruby
# Create exception with custom backtrace
def raise_with_context(message)
  error = RuntimeError.new(message)
  error.set_backtrace(caller)  # Use caller's backtrace
  raise error
end

# Preserve original backtrace
begin
  risky_operation
rescue => e
  e.set_backtrace(e.backtrace + ["Additional context"])
  raise e
end
```

**Common pitfall:** Raising Exception instead of StandardError:

```ruby
# Wrong - too broad
raise Exception, "Something went wrong"

# Right - use StandardError or more specific
raise StandardError, "Something went wrong"
# Or
raise RuntimeError, "Something went wrong"
# Or
raise ArgumentError, "Invalid input"
```

---

## 13.3 Custom Exception Classes

Creating custom exception classes helps you organize error handling and provide domain-specific error information. Ruby's custom exceptions work similarly to Python's.

**Key Similarities:**
- Both inherit from base exception classes
- Both can have custom attributes and methods
- Both integrate with the exception handling system

**Key Differences:**
- Ruby exceptions typically inherit from `StandardError`, not `Exception`
- Different initialization patterns
- Ruby uses `attr_accessor` for custom attributes

### Python Example

```python
# Basic custom exception
class ValidationError(Exception):
    pass

# With custom message
class InvalidAgeError(ValueError):
    def __init__(self, age, message="Invalid age"):
        self.age = age
        super().__init__(f"{message}: {age}")

# With multiple attributes
class DatabaseError(Exception):
    def __init__(self, query, error_code):
        self.query = query
        self.error_code = error_code
        message = f"Database error {error_code}: {query}"
        super().__init__(message)

# Using custom exceptions
try:
    if age < 0:
        raise InvalidAgeError(age)
except InvalidAgeError as e:
    print(f"Invalid age: {e.age}")

# Exception with custom behavior
class RetryableError(Exception):
    def __init__(self, message, retry_after=5):
        super().__init__(message)
        self.retry_after = retry_after
```

### Ruby Equivalent

```ruby
# Basic custom exception
class ValidationError < StandardError
end

# With custom message
class InvalidAgeError < ArgumentError
  attr_reader :age

  def initialize(age, message = "Invalid age")
    @age = age
    super("#{message}: #{age}")
  end
end

# With multiple attributes
class DatabaseError < StandardError
  attr_reader :query, :error_code

  def initialize(query, error_code)
    @query = query
    @error_code = error_code
    super("Database error #{error_code}: #{query}")
  end
end

# Using custom exceptions
begin
  raise InvalidAgeError.new(age) if age < 0
rescue InvalidAgeError => e
  puts "Invalid age: #{e.age}"
end

# Exception with custom behavior
class RetryableError < StandardError
  attr_reader :retry_after

  def initialize(message, retry_after = 5)
    @retry_after = retry_after
    super(message)
  end
end
```

### Explanation

Custom exceptions in Ruby are created by inheriting from `StandardError` (or one of its subclasses). This allows you to create domain-specific errors with custom attributes and behavior.

**Basic custom exception:**

```ruby
# Simple custom exception
class MyError < StandardError
end

# Use it
raise MyError, "Something went wrong"

# Rescue it
begin
  raise MyError, "Error occurred"
rescue MyError => e
  puts "Caught MyError: #{e.message}"
end
```

**With custom attributes:**

```ruby
class ValidationError < StandardError
  attr_reader :field, :value

  def initialize(field, value, message = nil)
    @field = field
    @value = value
    message ||= "Validation failed for #{field}: #{value}"
    super(message)
  end
end

# Usage
begin
  raise ValidationError.new(:email, "invalid@", "Invalid email format")
rescue ValidationError => e
  puts "Field: #{e.field}"
  puts "Value: #{e.value}"
  puts "Message: #{e.message}"
end
```

**Hierarchy of custom exceptions:**

```ruby
# Base error for your application
class MyAppError < StandardError
end

# Specific errors
class AuthenticationError < MyAppError
end

class AuthorizationError < MyAppError
end

class ValidationError < MyAppError
  attr_reader :errors

  def initialize(errors)
    @errors = errors
    super("Validation failed: #{errors.join(', ')}")
  end
end

# Catch any app error
begin
  # ... operations
rescue MyAppError => e
  log_error(e)
  show_user_friendly_message
end

# Or catch specific errors
begin
  authenticate_user
  authorize_action
  validate_input
rescue AuthenticationError => e
  redirect_to_login
rescue AuthorizationError => e
  show_forbidden_page
rescue ValidationError => e
  show_validation_errors(e.errors)
end
```

**With default message:**

```ruby
class InsufficientFundsError < StandardError
  attr_reader :balance, :amount

  def initialize(balance, amount)
    @balance = balance
    @amount = amount
    super("Insufficient funds: balance=$#{balance}, requested=$#{amount}")
  end
end

# Usage
balance = 100
amount = 150
raise InsufficientFundsError.new(balance, amount) if amount > balance
```

**With custom behavior:**

```ruby
class NetworkError < StandardError
  attr_reader :url, :retry_count

  def initialize(url, message = "Network request failed")
    @url = url
    @retry_count = 0
    super("#{message}: #{url}")
  end

  def retryable?
    @retry_count < 3
  end

  def increment_retry
    @retry_count += 1
  end
end

# Usage
def fetch_data(url)
  HTTP.get(url)
rescue HTTP::Error => e
  error = NetworkError.new(url, e.message)
  raise error
end

begin
  fetch_data(url)
rescue NetworkError => e
  if e.retryable?
    e.increment_retry
    sleep(2)
    retry
  else
    log_error(e)
  end
end
```

**Exception with structured data:**

```ruby
class ApiError < StandardError
  attr_reader :status_code, :response_body, :request_id

  def initialize(status_code, response_body, request_id = nil)
    @status_code = status_code
    @response_body = response_body
    @request_id = request_id

    super(build_message)
  end

  private

  def build_message
    msg = "API Error (#{@status_code})"
    msg += " [Request ID: #{@request_id}]" if @request_id
    msg += "\n#{@response_body}"
    msg
  end
end

# Usage
begin
  response = api_call
  raise ApiError.new(response.status, response.body, response.headers['X-Request-ID']) if response.error?
rescue ApiError => e
  logger.error("API call failed: #{e.status_code}")
  logger.debug("Response: #{e.response_body}")
  logger.debug("Request ID: #{e.request_id}")
end
```

**Practical examples:**

```ruby
# Configuration error
class ConfigurationError < StandardError
  attr_reader :key, :value

  def initialize(key, value = nil)
    @key = key
    @value = value
    super(build_message)
  end

  private

  def build_message
    if @value
      "Invalid configuration for #{@key}: #{@value}"
    else
      "Missing required configuration: #{@key}"
    end
  end
end

# User-facing error with localization support
class UserError < StandardError
  attr_reader :error_code, :context

  def initialize(error_code, context = {})
    @error_code = error_code
    @context = context
    super(I18n.t("errors.#{error_code}", context))
  end

  def user_message
    message
  end
end

# Business logic error
class OrderError < StandardError
  attr_reader :order

  def initialize(order, message)
    @order = order
    super("Order #{order.id}: #{message}")
  end
end

class InvalidOrderStateError < OrderError
  def initialize(order, expected_state, actual_state)
    super(order, "Expected state #{expected_state}, but was #{actual_state}")
  end
end

# File processing error
class FileProcessingError < StandardError
  attr_reader :filename, :line_number

  def initialize(filename, line_number = nil, message = "Failed to process file")
    @filename = filename
    @line_number = line_number

    msg = "#{message}: #{filename}"
    msg += " (line #{line_number})" if line_number
    super(msg)
  end
end

# Usage
def process_csv(filename)
  File.readlines(filename).each_with_index do |line, index|
    begin
      process_line(line)
    rescue => e
      raise FileProcessingError.new(filename, index + 1, e.message)
    end
  end
rescue Errno::ENOENT
  raise FileProcessingError.new(filename, nil, "File not found")
end
```

**Exception factory pattern:**

```ruby
module Errors
  class Base < StandardError
    attr_reader :context

    def initialize(message = nil, context = {})
      @context = context
      super(message || default_message)
    end

    def default_message
      "An error occurred"
    end
  end

  class NotFound < Base
    def default_message
      "Resource not found"
    end
  end

  class Unauthorized < Base
    def default_message
      "Unauthorized access"
    end
  end

  class ValidationFailed < Base
    def default_message
      "Validation failed"
    end
  end
end

# Usage
raise Errors::NotFound.new("User not found", user_id: 123)
raise Errors::Unauthorized.new(action: 'delete', resource: 'post')
```

**Common pitfall:** Inheriting from `Exception` instead of `StandardError`:

```ruby
# Wrong - too broad, catches system exceptions
class MyError < Exception
end

# Right - inherit from StandardError
class MyError < StandardError
end

# Or from a more specific error
class MyValidationError < ArgumentError
end
```

---

## 13.4 Rescue Modifiers

Ruby has a unique feature called rescue modifiers that allow inline exception handling. This is similar to Python's try-except but in a single line, though Python doesn't have a direct equivalent.

**Key Similarities:**
- Both handle exceptions
- Both can provide fallback values

**Key Differences:**
- Ruby's rescue modifier is inline (statement modifier)
- More concise syntax for simple cases
- Python requires full try-except block

### Python Example

```python
# Try-except for default value
try:
    value = risky_operation()
except:
    value = "default"

# One-liner isn't really possible in Python
# Closest is ternary with exception:
value = risky_operation() if safe() else "default"

# Or using getattr/get for dictionary
value = data.get('key', 'default')

# Context manager for files
try:
    content = open('file.txt').read()
except FileNotFoundError:
    content = ""
```

### Ruby Equivalent

```ruby
# Rescue modifier for default value
value = risky_operation rescue "default"

# Equivalent to:
begin
  value = risky_operation
rescue
  value = "default"
end

# File reading with fallback
content = File.read('file.txt') rescue ""

# Parse with fallback
number = Integer(input) rescue 0

# Hash access with fallback
value = hash.fetch(:key) rescue "default"

# Method call with fallback
result = object.method_call rescue nil
```

### Explanation

Rescue modifiers provide a concise way to handle exceptions inline. They catch `StandardError` and its subclasses, making them perfect for simple fallback scenarios.

**Basic rescue modifier:**

```ruby
# Basic syntax: expression rescue fallback_value
result = risky_operation rescue "default"

# Real examples
config = File.read('config.yml') rescue '{}'
value = Integer(user_input) rescue 0
name = user.name rescue "Unknown"

# With method calls
data = fetch_from_api rescue []
user = User.find(id) rescue nil
```

**When to use rescue modifiers:**

```ruby
# Good: Simple fallback values
count = file.size rescue 0

# Good: Parsing with defaults
age = Integer(params[:age]) rescue 18

# Good: Method calls that might fail
email = user&.profile&.email rescue "no-email@example.com"

# Avoid: Complex expressions (use begin/rescue instead)
# Not recommended
result = (complex; multi; line; operation) rescue fallback

# Better
begin
  result = complex
  result = multi
  result = line
  result = operation
rescue
  result = fallback
end
```

**Common use cases:**

```ruby
# Configuration with defaults
API_KEY = ENV['API_KEY'] rescue 'development-key'
MAX_RETRIES = Integer(ENV['MAX_RETRIES']) rescue 3

# File operations
config = YAML.load_file('config.yml') rescue {}
data = JSON.parse(response) rescue {}

# Type conversion
age = Integer(params[:age]) rescue nil
amount = Float(params[:amount]) rescue 0.0

# Database queries
user = User.find_by(id: params[:id]) rescue User.new
latest_post = user.posts.last rescue nil

# Hash/Array access
first_name = data.dig(:user, :name, :first) rescue "Unknown"
value = array[10] rescue nil

# Method chaining with potential nils
title = article&.title&.upcase rescue "NO TITLE"
```

**Rescue modifier with blocks:**

```ruby
# Works with blocks
result = File.open('file.txt') { |f| f.read } rescue "default content"

# Iterate with fallback
items = Dir.glob('*.txt').map { |f| File.read(f) } rescue []

# Can wrap multiple statements (use parentheses)
result = (
  file = File.open('data.txt')
  data = file.read
  process(data)
) rescue "default"
```

**Practical examples:**

```ruby
# API client with fallback
class ApiClient
  def fetch_users
    response = HTTP.get('/api/users')
    JSON.parse(response.body)
  rescue HTTP::Error
    []
  end

  # Or with modifier
  def fetch_user(id)
    JSON.parse(HTTP.get("/api/users/#{id}").body) rescue nil
  end
end

# Configuration loader
class Config
  def self.load
    @config ||= YAML.load_file('config.yml') rescue default_config
  end

  def self.default_config
    { 'environment' => 'development' }
  end
end

# Cache with fallback to computation
class Cache
  def fetch(key)
    @cache[key] rescue compute_and_cache(key)
  end
end

# Safe type conversion utility
module TypeConvert
  def self.to_int(value, default = 0)
    Integer(value) rescue default
  end

  def self.to_float(value, default = 0.0)
    Float(value) rescue default
  end

  def self.to_bool(value)
    return true if value == true || value =~ /^(true|t|yes|y|1)$/i
    return false if value == false || value.nil? || value =~ /^(false|f|no|n|0)$/i
    raise ArgumentError, "Invalid boolean: #{value}"
  rescue
    false
  end
end

# Logger that never crashes
class SafeLogger
  def log(message)
    File.open('app.log', 'a') { |f| f.puts "[#{Time.now}] #{message}" }
  rescue
    # Swallow errors - logging should never crash the app
    nil
  end
end

# URL parsing with fallback
def extract_domain(url)
  URI.parse(url).host rescue "unknown"
end

# JSON parsing with error logging
def parse_json(json_string)
  JSON.parse(json_string)
rescue JSON::ParserError => e
  Rails.logger.error("JSON parsing failed: #{e.message}")
  {}
end
```

**When NOT to use rescue modifiers:**

```ruby
# Don't use for complex error handling
# Bad
result = complex_operation rescue (log_error; retry_operation; fallback)

# Good
begin
  result = complex_operation
rescue => e
  log_error(e)
  result = retry_operation
  result = fallback if result.nil?
end

# Don't use when you need the exception
# Bad
value = operation rescue (puts "Error occurred"; default)

# Good
begin
  value = operation
rescue => e
  puts "Error: #{e.message}"
  value = default
end

# Don't use for control flow
# Bad
result = try_method_a rescue try_method_b rescue try_method_c

# Good
result = try_method_a ||
         (try_method_b rescue nil) ||
         (try_method_c rescue nil)
```

**Common pitfall:** Using rescue modifier when you need to know what failed:

```ruby
# Bad - can't tell what went wrong
result = fetch_and_process_data rescue "error"

# Good - explicit error handling
begin
  result = fetch_and_process_data
rescue NetworkError => e
  log("Network error: #{e.message}")
  result = "network_error"
rescue ProcessingError => e
  log("Processing error: #{e.message}")
  result = "processing_error"
end
```

---

## 13.5 `retry`

Ruby's `retry` keyword allows you to restart a `begin` block from the beginning. This is particularly useful for handling transient errors like network timeouts. Python doesn't have a built-in `retry` mechanism.

**Key Similarities:**
- Both can implement retry logic
- Both need safeguards against infinite loops

**Key Differences:**
- Ruby has built-in `retry` keyword
- Python requires manual loop implementation
- Ruby's retry is scope-aware (restarts the begin block)

### Python Example

```python
# Manual retry with loop
max_attempts = 3
attempts = 0

while attempts < max_attempts:
    try:
        result = risky_operation()
        break
    except TransientError:
        attempts += 1
        if attempts >= max_attempts:
            raise
        time.sleep(2 ** attempts)  # Exponential backoff

# Function-based retry
def retry_operation(func, max_attempts=3):
    for attempt in range(max_attempts):
        try:
            return func()
        except Exception as e:
            if attempt == max_attempts - 1:
                raise
            time.sleep(2 ** attempt)

result = retry_operation(lambda: risky_operation())

# Using decorator (common pattern)
def retry(max_attempts=3):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise
                    time.sleep(1)
        return wrapper
    return decorator

@retry(max_attempts=3)
def fetch_data():
    return api.get('/data')
```

### Ruby Equivalent

```ruby
# Basic retry
begin
  risky_operation
rescue TransientError
  retry  # Restart the begin block
end

# Retry with limit
attempts = 0
begin
  attempts += 1
  risky_operation
rescue TransientError
  retry if attempts < 3
  raise
end

# Retry with exponential backoff
attempts = 0
begin
  attempts += 1
  fetch_from_api
rescue NetworkError => e
  if attempts < 3
    sleep(2 ** attempts)
    retry
  else
    raise
  end
end

# Retry in method (implicit begin)
def fetch_data
  attempts = 0
  fetch_from_api
rescue NetworkError
  attempts += 1
  retry if attempts < 3
  raise
end
```

### Explanation

The `retry` keyword jumps back to the beginning of the `begin` block (or method body), allowing you to re-execute code that failed. This is particularly useful for handling transient errors.

**Basic retry:**

```ruby
# Simplest form - retry indefinitely (dangerous!)
begin
  connect_to_server
rescue ConnectionError
  retry  # Try again
end

# With a limit
attempts = 0
begin
  attempts += 1
  connect_to_server
rescue ConnectionError
  retry if attempts < 3
  raise  # Give up after 3 attempts
end
```

**Retry with backoff:**

```ruby
# Exponential backoff
attempts = 0
begin
  attempts += 1
  api_call
rescue RateLimitError => e
  if attempts < 5
    wait_time = 2 ** attempts  # 2, 4, 8, 16, 32 seconds
    puts "Rate limited. Retrying in #{wait_time} seconds..."
    sleep(wait_time)
    retry
  else
    raise
  end
end

# Fixed backoff
attempts = 0
begin
  attempts += 1
  database_query
rescue DatabaseError
  if attempts < 3
    sleep(5)  # Wait 5 seconds
    retry
  else
    raise
  end
end
```

**Retry in methods:**

```ruby
# Methods have implicit begin/end
def fetch_user(id)
  attempts = 0
  User.find(id)
rescue ActiveRecord::RecordNotFound => e
  attempts += 1
  if attempts < 3
    sleep(1)
    retry
  else
    raise
  end
end

# Multiple rescue clauses with different retry strategies
def process_order(order)
  attempts = 0

  order.process!
rescue NetworkError => e
  attempts += 1
  if attempts < 5
    sleep(2 ** attempts)
    retry
  else
    raise
  end
rescue ValidationError => e
  # Don't retry validation errors
  raise
rescue => e
  # Retry other errors once
  retry if attempts.zero?
  raise
end
```

**Retry with state modification:**

```ruby
# Change something before retrying
begin
  connect_to_database(host)
rescue ConnectionError => e
  if host == 'primary'
    host = 'secondary'
    retry
  else
    raise
  end
end

# Modify parameters
timeout = 5
begin
  fetch_with_timeout(url, timeout)
rescue TimeoutError
  if timeout < 30
    timeout += 5
    puts "Increasing timeout to #{timeout} seconds..."
    retry
  else
    raise
  end
end
```

**Practical examples:**

```ruby
# HTTP request with retry
def fetch_data(url)
  attempts = 0
  max_attempts = 3

  HTTP.get(url)
rescue HTTP::Error => e
  attempts += 1
  if attempts < max_attempts
    wait_time = 2 ** attempts
    logger.warn("Request failed (attempt #{attempts}/#{max_attempts}). Retrying in #{wait_time}s...")
    sleep(wait_time)
    retry
  else
    logger.error("Request failed after #{max_attempts} attempts")
    raise
  end
end

# Database operation with retry
def save_with_retry(record)
  attempts = 0

  record.save!
rescue ActiveRecord::Deadlocked => e
  attempts += 1
  if attempts < 3
    logger.warn("Deadlock detected (attempt #{attempts}). Retrying...")
    sleep(0.5)
    retry
  else
    logger.error("Deadlock persisted after #{attempts} attempts")
    raise
  end
end

# File operation with retry
def read_file_with_retry(filename)
  attempts = 0

  File.read(filename)
rescue Errno::EAGAIN, Errno::EBUSY => e
  attempts += 1
  if attempts < 5
    logger.warn("File busy. Retrying...")
    sleep(0.1 * attempts)
    retry
  else
    raise
  end
end

# API call with rate limiting
class ApiClient
  def call_api(endpoint)
    @attempts = 0
    @max_attempts = 5

    make_request(endpoint)
  rescue RateLimitError => e
    handle_rate_limit(e)
  rescue NetworkError => e
    handle_network_error(e)
  end

  private

  def handle_rate_limit(error)
    @attempts += 1
    if @attempts < @max_attempts
      wait_time = error.retry_after || (2 ** @attempts)
      logger.warn("Rate limited. Waiting #{wait_time}s...")
      sleep(wait_time)
      retry
    else
      raise
    end
  end

  def handle_network_error(error)
    @attempts += 1
    if @attempts < @max_attempts
      logger.warn("Network error (attempt #{@attempts}/#{@max_attempts})")
      sleep(1)
      retry
    else
      raise
    end
  end
end

# Complex retry with circuit breaker pattern
class CircuitBreaker
  def initialize
    @failures = 0
    @last_failure_time = nil
    @threshold = 5
    @timeout = 60
  end

  def call
    raise "Circuit breaker open" if open?

    yield
    reset
  rescue => e
    record_failure
    retry if should_retry?
    raise
  end

  private

  def open?
    @failures >= @threshold &&
      @last_failure_time &&
      Time.now - @last_failure_time < @timeout
  end

  def should_retry?
    @failures < @threshold
  end

  def record_failure
    @failures += 1
    @last_failure_time = Time.now
  end

  def reset
    @failures = 0
    @last_failure_time = nil
  end
end
```

**Retry with redo (different from retry):**

```ruby
# retry - restarts the begin block
# redo - restarts the current iteration (loops)

# retry example
attempts = 0
begin
  attempts += 1
  puts "Attempt #{attempts}"
  raise "Error"
rescue
  retry if attempts < 3
end
# Output: Attempt 1, Attempt 2, Attempt 3

# redo example (in loop)
5.times do |i|
  puts "Iteration #{i}"
  redo if i == 2  # Infinite loop at i=2!
end
```

**Common pitfall:** Infinite retry loop:

```ruby
# Wrong - infinite loop!
begin
  risky_operation
rescue
  retry  # Will retry forever
end

# Right - limit attempts
attempts = 0
begin
  attempts += 1
  risky_operation
rescue
  retry if attempts < 3
  raise
end

# Or use a maximum time
start_time = Time.now
begin
  risky_operation
rescue
  retry if Time.now - start_time < 30
  raise
end
```

---

## 13.6 Exception Hierarchy

Understanding the exception hierarchy is crucial for effective error handling. Ruby's hierarchy differs from Python's, with `StandardError` being the key base class for rescuable errors.

**Key Similarities:**
- Both have hierarchical exception classes
- Both have a base exception class
- Both separate system errors from application errors

**Key Differences:**
- Ruby uses `StandardError` as the base for normal errors (not `Exception`)
- Different exception class names
- Ruby's hierarchy is more focused on `StandardError` subclasses

### Python Example

```python
# Python exception hierarchy (simplified)
# BaseException
#   ├─ SystemExit
#   ├─ KeyboardInterrupt
#   └─ Exception
#       ├─ StopIteration
#       ├─ ArithmeticError
#       │   ├─ ZeroDivisionError
#       │   └─ FloatingPointError
#       ├─ AssertionError
#       ├─ AttributeError
#       ├─ EOFError
#       ├─ ImportError
#       ├─ LookupError
#       │   ├─ IndexError
#       │   └─ KeyError
#       ├─ NameError
#       ├─ OSError (IOError)
#       │   ├─ FileNotFoundError
#       │   └─ PermissionError
#       ├─ RuntimeError
#       ├─ TypeError
#       └─ ValueError

# Catching exceptions at different levels
try:
    operation()
except ValueError:  # Specific
    pass
except Exception:   # Broad (normal errors)
    pass
except BaseException:  # Very broad (includes system)
    pass
```

### Ruby Equivalent

```ruby
# Ruby exception hierarchy (simplified)
# Exception
#   ├─ NoMemoryError
#   ├─ ScriptError
#   │   ├─ LoadError
#   │   ├─ NotImplementedError
#   │   └─ SyntaxError
#   ├─ SecurityError
#   ├─ SignalException
#   │   └─ Interrupt
#   ├─ SystemExit
#   └─ StandardError (← rescue catches this by default)
#       ├─ ArgumentError
#       ├─ EncodingError
#       ├─ FiberError
#       ├─ IOError
#       │   └─ EOFError
#       ├─ IndexError
#       │   ├─ KeyError
#       │   └─ StopIteration
#       ├─ LocalJumpError
#       ├─ NameError
#       │   └─ NoMethodError
#       ├─ RangeError
#       │   └─ FloatDomainError
#       ├─ RegexpError
#       ├─ RuntimeError (default for raise)
#       ├─ SystemCallError
#       │   ├─ Errno::EACCES
#       │   ├─ Errno::ENOENT
#       │   └─ (many more Errno subclasses)
#       ├─ ThreadError
#       ├─ TypeError
#       └─ ZeroDivisionError

# Catching exceptions at different levels
begin
  operation
rescue ArgumentError  # Specific
  # ...
rescue StandardError  # Broad (normal errors)
  # ...
rescue Exception      # Very broad (DON'T DO THIS)
  # ...
end
```

### Explanation

Ruby's exception hierarchy is designed so that `rescue` without a specific class catches `StandardError` and its subclasses - the errors you typically want to handle. System-level exceptions like `SignalException` and `SystemExit` inherit directly from `Exception`.

**StandardError and its common subclasses:**

```ruby
# StandardError - base for normal errors
begin
  # ...
rescue StandardError => e
  # Catches all normal errors
end

# Or just rescue (same as rescue StandardError)
begin
  # ...
rescue => e
  # Also catches all normal errors
end

# Common StandardError subclasses:

# ArgumentError - wrong number/type of arguments
def greet(name, age)
  raise ArgumentError, "Name required" if name.nil?
end

# TypeError - wrong type
5 + "string"  # TypeError: String can't be coerced into Integer

# NameError - undefined variable/constant
undefined_variable  # NameError: undefined local variable

# NoMethodError - method doesn't exist (subclass of NameError)
"hello".nonexistent_method  # NoMethodError

# IndexError - invalid index
[1, 2, 3][10]  # nil (no error)
[1, 2, 3].fetch(10)  # IndexError

# KeyError - missing hash key (subclass of IndexError)
hash = { a: 1 }
hash.fetch(:b)  # KeyError

# ZeroDivisionError - division by zero
10 / 0  # ZeroDivisionError

# IOError - I/O operation failed
File.open('/nonexistent/file')  # Errno::ENOENT (subclass of SystemCallError)

# RuntimeError - generic error (default for raise)
raise "Something went wrong"  # RuntimeError

# RangeError - value out of range
[1, 2, 3].pack("C*" * 1000)  # Might raise RangeError

# RegexpError - invalid regexp
Regexp.new("(")  # RegexpError: unmatched parenthesis
```

**Exception vs StandardError:**

```ruby
# Exception - base class for ALL exceptions
# Should almost NEVER be rescued

# These inherit from Exception (not StandardError):
# - SystemExit (raised by exit)
# - SignalException (Ctrl+C, etc.)
# - NoMemoryError
# - ScriptError (LoadError, SyntaxError, NotImplementedError)

# Wrong - catches system exceptions!
begin
  operation
rescue Exception => e  # DON'T DO THIS
  # This catches SystemExit, Interrupt, etc.
  # Your program might not exit when it should
end

# Right - catch StandardError
begin
  operation
rescue StandardError => e  # Good
  # Or just: rescue => e
end

# Why catching Exception is bad:
begin
  puts "Running..."
  exit  # User wants to exit
rescue Exception
  puts "Can't exit!"  # Prevents exit!
end
```

**System call errors (Errno):**

```ruby
# Errno module contains system call errors
# All inherit from SystemCallError < StandardError

# Common Errno exceptions:
begin
  File.open('/nonexistent/file')
rescue Errno::ENOENT => e  # No such file or directory
  puts "File not found"
end

begin
  File.open('/root/protected.txt')
rescue Errno::EACCES => e  # Permission denied
  puts "Access denied"
end

begin
  # Various file operations
rescue Errno::ENOENT  # File not found
rescue Errno::EACCES  # Permission denied
rescue Errno::EISDIR  # Is a directory
rescue Errno::ENOSPC  # No space left on device
rescue SystemCallError => e  # Catch all system errors
  puts "System error: #{e.message}"
end

# Check error number
begin
  File.open('/protected')
rescue SystemCallError => e
  puts e.errno  # Error number
  puts e.class  # Specific Errno class
end
```

**Rescue order matters:**

```ruby
# Right - specific to general
begin
  operation
rescue Errno::ENOENT => e
  puts "File not found"
rescue SystemCallError => e
  puts "Other system error"
rescue IOError => e
  puts "I/O error"
rescue StandardError => e
  puts "Other error"
end

# Wrong - general first (specific never reached)
begin
  operation
rescue StandardError => e  # Catches everything
  puts "Error"
rescue Errno::ENOENT => e  # Never reached!
  puts "File not found"
end
```

**Practical hierarchy usage:**

```ruby
# Create exception hierarchy for your app
class MyAppError < StandardError
end

class ValidationError < MyAppError
end

class DatabaseError < MyAppError
end

class NetworkError < MyAppError
end

# Catch all app errors
begin
  application_operation
rescue MyAppError => e
  log_app_error(e)
  show_user_message
end

# Or catch specific errors
begin
  application_operation
rescue ValidationError => e
  show_validation_errors
rescue DatabaseError => e
  show_database_error
rescue NetworkError => e
  retry_operation
rescue MyAppError => e
  show_generic_error
end
```

**Checking exception hierarchy:**

```ruby
# Check if exception is a kind of another
e = ArgumentError.new
e.is_a?(StandardError)  # true
e.is_a?(Exception)      # true
e.is_a?(TypeError)      # false

# Get ancestors
puts ArgumentError.ancestors
# [ArgumentError, StandardError, Exception, Object, Kernel, BasicObject]

# Check inheritance
ArgumentError < StandardError  # true
StandardError < Exception      # true
```

**Common exception patterns:**

```ruby
# Pattern 1: Catch specific, log others
begin
  critical_operation
rescue ActiveRecord::RecordNotFound => e
  render_404
rescue ActiveRecord::RecordInvalid => e
  render_errors(e.record.errors)
rescue => e
  logger.error("Unexpected error: #{e.class} - #{e.message}")
  logger.error(e.backtrace.join("\n"))
  render_500
end

# Pattern 2: Hierarchical rescue
begin
  process_data
rescue JSON::ParserError => e
  # Specific JSON error
  handle_json_error(e)
rescue StandardError => e
  # Any other normal error
  handle_general_error(e)
ensure
  cleanup
end

# Pattern 3: Rescue and re-raise as custom
begin
  third_party_api_call
rescue ThirdPartyError => e
  raise MyAppError, "API call failed: #{e.message}"
end
```

**Common pitfall:** Rescuing `Exception`:

```ruby
# Wrong - prevents program from exiting
def process_forever
  loop do
    begin
      process_item
    rescue Exception  # DON'T DO THIS
      next  # Swallows Interrupt (Ctrl+C)
    end
  end
end
# User can't stop this with Ctrl+C!

# Right - rescue StandardError
def process_forever
  loop do
    begin
      process_item
    rescue StandardError => e  # or just: rescue => e
      log_error(e)
      next
    end
  end
end
# Ctrl+C will work
```

---

This chapter covered comprehensive error handling in Ruby:

- **begin/rescue/ensure** - Ruby's exception handling syntax compared to Python's try/except/finally
- **Raising exceptions** - Using `raise` with various syntaxes and exception types
- **Custom exceptions** - Creating domain-specific error classes with custom attributes
- **Rescue modifiers** - Inline exception handling for simple cases
- **retry** - Ruby's unique ability to restart failed operations
- **Exception hierarchy** - Understanding StandardError vs Exception and when to use each

Ruby's error handling is powerful and expressive. The `rescue` modifier provides convenient inline error handling, while `retry` enables elegant handling of transient failures. Understanding the exception hierarchy, particularly the distinction between `Exception` and `StandardError`, is crucial for writing robust Ruby code.