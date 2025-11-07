# Chapter 14: Ruby Idioms and Best Practices

## 14.1 The Ruby Way (Idiomatic Ruby)

Ruby has a strong culture of writing "idiomatic" code - code that follows Ruby conventions and leverages the language's expressive features. This section introduces core Ruby idioms that make code more readable and maintainable.

**Key Similarities:**
- Both Python and Ruby value readability
- Both have strong community style guides
- Both prefer explicit over implicit in many cases

**Key Differences:**
- Ruby emphasizes "natural language" readability
- Ruby has more options for expressing the same logic
- Ruby culture values elegance and expressiveness
- Different community conventions (Python's PEP 8 vs Ruby Style Guide)

### Python Example

```python
# Pythonic code follows PEP 8 and community conventions

# List comprehension (preferred over map/filter)
squares = [x**2 for x in range(10)]

# Checking for empty sequences
if len(my_list) == 0:  # Works but not idiomatic
    print("Empty")

if not my_list:  # More Pythonic
    print("Empty")

# Iteration
for item in items:
    process(item)

# Ternary operator
result = "yes" if condition else "no"

# With statement for resources
with open('file.txt') as f:
    content = f.read()

# Dictionary get with default
value = data.get('key', 'default')

# String formatting (modern Python)
message = f"Hello, {name}!"

# Enumerate for index and value
for index, value in enumerate(items):
    print(f"{index}: {value}")
```

### Ruby Equivalent

```ruby
# Idiomatic Ruby emphasizes readability and expressiveness

# Blocks and iteration (preferred over for loops)
squares = (0...10).map { |x| x**2 }

# Checking for empty collections
if my_array.empty?  # Idiomatic Ruby
  puts "Empty"
end

# Or with truthiness (empty arrays are truthy in Ruby!)
if my_array.any?
  puts "Has items"
end

# Iteration with blocks
items.each do |item|
  process(item)
end

# Ternary operator (same as Python)
result = condition ? "yes" : "no"

# Blocks for resource management
File.open('file.txt') do |f|
  content = f.read
end

# Hash fetch with default
value = data.fetch(:key, 'default')
# Or
value = data[:key] || 'default'

# String interpolation (no prefix needed)
message = "Hello, #{name}!"

# each_with_index
items.each_with_index do |value, index|
  puts "#{index}: #{value}"
end
```

### Explanation

Ruby idioms emphasize expressiveness, readability, and leveraging blocks and method chaining. Here are core principles and patterns:

**Prefer blocks over for loops:**

```ruby
# Not idiomatic (though it works)
for i in 0...10
  puts i
end

# Idiomatic
10.times do |i|
  puts i
end

# Or
(0...10).each do |i|
  puts i
end
```

**Use predicate methods (methods ending in ?):**

```ruby
# Not idiomatic
if array.length == 0
  puts "Empty"
end

# Idiomatic
if array.empty?
  puts "Empty"
end

# More predicate examples
string.empty?
hash.key?(:name)
array.include?(value)
number.zero?
number.positive?
number.negative?
value.nil?
```

**Use bang methods (!) for destructive operations:**

```ruby
# Non-destructive (returns new object)
uppercased = name.upcase
sorted = array.sort

# Destructive (modifies in place)
name.upcase!
array.sort!

# Convention: method! modifies receiver, method returns new value
```

**Statement modifiers for simple conditions:**

```ruby
# Not idiomatic (for simple cases)
if condition
  do_something
end

# Idiomatic
do_something if condition

# Also works with unless
do_something unless condition

# And with while/until
increment while value < 10
```

**Use `unless` instead of `if !`:**

```ruby
# Not idiomatic
if !valid?
  raise "Invalid"
end

# Idiomatic
unless valid?
  raise "Invalid"
end

# Or as modifier
raise "Invalid" unless valid?

# But avoid unless with else - it's confusing
# Don't do this:
unless condition
  do_this
else
  do_that
end

# Do this instead:
if condition
  do_that
else
  do_this
end
```

**Implicit returns:**

```ruby
# Not idiomatic
def calculate(x, y)
  result = x + y
  return result
end

# Idiomatic
def calculate(x, y)
  x + y  # Last expression is returned
end

# Only use explicit return for early returns
def process(value)
  return nil if value.nil?
  return 0 if value.zero?

  value * 2
end
```

**Symbol to proc (`&:method_name`):**

```ruby
# Not idiomatic
names.map { |name| name.upcase }

# Idiomatic
names.map(&:upcase)

# More examples
numbers.select(&:positive?)
users.map(&:name)
items.all?(&:valid?)
```

**Trailing commas in multiline collections:**

```ruby
# Idiomatic (makes diffs cleaner)
array = [
  'first',
  'second',
  'third',  # Trailing comma
]

hash = {
  name: 'Alice',
  age: 30,
  city: 'NYC',  # Trailing comma
}
```

**Use `%w` for word arrays:**

```ruby
# Not idiomatic
colors = ['red', 'green', 'blue']

# Idiomatic
colors = %w[red green blue]

# Or for symbols
statuses = %i[pending active completed]
```

**Method chaining:**

```ruby
# Break long chains across lines
result = users
  .select(&:active?)
  .map(&:email)
  .sort
  .uniq

# Or with leading dots
result = users.
  select(&:active?).
  map(&:email).
  sort.
  uniq
```

**Parentheses in method calls:**

```ruby
# Use parentheses for method definitions
def greet(name, age)
  # ...
end

# Optional in calls when unambiguous
puts "Hello"  # No parentheses
puts("Hello") # With parentheses (both fine)

# Use parentheses when passing arguments for clarity
user.update(name: 'Alice', age: 30)

# No parentheses for simple DSL-like code
class User
  validates :email, presence: true
  belongs_to :organization
end
```

**Common Ruby idioms summary:**

```ruby
# 1. Use blocks everywhere
items.each { |item| process(item) }

# 2. Use predicate methods
return unless valid?

# 3. Implicit returns
def total
  items.sum(&:price)
end

# 4. Symbol to proc
names.map(&:upcase)

# 5. Statement modifiers
save! if changed?

# 6. Safe navigation
user&.profile&.avatar

# 7. Default values with ||=
@cache ||= {}

# 8. Guard clauses
return if items.empty?

# 9. Method chaining
data.select(&:active).map(&:name).sort

# 10. String interpolation
"Hello, #{name}!"
```

**What to avoid:**

```ruby
# Don't use for loops (prefer blocks)
# Bad
for item in items
  puts item
end

# Good
items.each { |item| puts item }

# Don't check nil explicitly when you can use &.
# Bad
name = user ? user.name : nil

# Good
name = user&.name

# Don't use semicolons (except in one-liners)
# Bad
x = 1; y = 2; z = 3

# Good
x = 1
y = 2
z = 3

# Don't use then/next after if/unless
# Bad
if condition then do_something end

# Good
if condition
  do_something
end

# Or
do_something if condition
```

---

## 14.2 Guard Clauses

Guard clauses are early returns that handle edge cases or invalid inputs at the beginning of a method, keeping the main logic clean and unindented. This pattern is common in both Ruby and Python, but Ruby's syntax makes it particularly elegant.

**Key Similarities:**
- Both use early returns to handle edge cases
- Both improve readability by reducing nesting
- Both check preconditions first

**Key Differences:**
- Ruby's statement modifiers make guard clauses more concise
- Ruby has `unless` which reads more naturally for guards
- Different conventions for when to use guards

### Python Example

```python
# Without guard clauses (nested)
def process_user(user):
    if user is not None:
        if user.is_active:
            if user.has_permission('edit'):
                # Main logic deeply nested
                result = perform_action(user)
                return result
            else:
                return None
        else:
            return None
    else:
        return None

# With guard clauses (flat)
def process_user(user):
    if user is None:
        return None

    if not user.is_active:
        return None

    if not user.has_permission('edit'):
        return None

    # Main logic at top level
    return perform_action(user)

# Raising exceptions as guards
def divide(a, b):
    if not isinstance(a, (int, float)):
        raise TypeError("a must be a number")

    if not isinstance(b, (int, float)):
        raise TypeError("b must be a number")

    if b == 0:
        raise ValueError("Cannot divide by zero")

    return a / b
```

### Ruby Equivalent

```ruby
# Without guard clauses (nested)
def process_user(user)
  if user
    if user.active?
      if user.has_permission?('edit')
        # Main logic deeply nested
        result = perform_action(user)
        result
      end
    end
  end
end

# With guard clauses (flat) - idiomatic Ruby
def process_user(user)
  return nil if user.nil?
  return nil unless user.active?
  return nil unless user.has_permission?('edit')

  # Main logic at top level
  perform_action(user)
end

# Even more concise with statement modifiers
def process_user(user)
  return unless user&.active?
  return unless user.has_permission?('edit')

  perform_action(user)
end

# Raising exceptions as guards
def divide(a, b)
  raise ArgumentError, "a must be a number" unless a.is_a?(Numeric)
  raise ArgumentError, "b must be a number" unless b.is_a?(Numeric)
  raise ZeroDivisionError, "Cannot divide by zero" if b.zero?

  a / b.to_f
end
```

### Explanation

Guard clauses check for invalid conditions early and return immediately, keeping the main logic clean and at the top level of indentation.

**Basic guard clause pattern:**

```ruby
def process(value)
  # Check for nil
  return nil if value.nil?

  # Check for empty
  return [] if value.empty?

  # Check for invalid state
  return false unless value.valid?

  # Main logic here
  value.transform
end
```

**Multiple guards:**

```ruby
def create_order(user, items, payment_method)
  # Guard against nil inputs
  return { error: 'User required' } if user.nil?
  return { error: 'Items required' } if items.nil? || items.empty?
  return { error: 'Payment method required' } if payment_method.nil?

  # Guard against invalid state
  return { error: 'User not active' } unless user.active?
  return { error: 'Insufficient permissions' } unless user.can_order?

  # Guard against business rules
  total = items.sum(&:price)
  return { error: 'Order total too low' } if total < minimum_order_amount

  # Main logic - clean and unindented
  order = Order.create(
    user: user,
    items: items,
    payment_method: payment_method,
    total: total
  )

  { success: true, order: order }
end
```

**Raising vs returning in guards:**

```ruby
# Return nil/false for expected cases
def find_user(id)
  return nil if id.nil?
  return nil unless id.positive?

  User.find_by(id: id)
end

# Raise for invalid arguments
def update_user(user, attributes)
  raise ArgumentError, "User cannot be nil" if user.nil?
  raise ArgumentError, "Attributes cannot be empty" if attributes.empty?

  user.update(attributes)
end

# Mix both: raise for programmer errors, return for business logic
def process_payment(amount, card)
  # Programmer errors - should never happen
  raise ArgumentError, "Amount must be a number" unless amount.is_a?(Numeric)

  # Business logic - expected cases
  return { error: 'Amount too low' } if amount < 1
  return { error: 'Card declined' } unless card.valid?

  charge_card(amount, card)
end
```

**Guards with `unless`:**

```ruby
# unless reads naturally for guards
def save_draft(post)
  return false unless post.present?
  return false unless post.changed?
  return false if post.published?

  post.save_as_draft
end

# Equivalent with if ! (less readable)
def save_draft(post)
  return false if !post.present?
  return false if !post.changed?
  return false if post.published?

  post.save_as_draft
end
```

**Guards in different contexts:**

```ruby
# Controller action
def create
  return render_unauthorized unless current_user
  return render_forbidden unless current_user.admin?

  @resource = Resource.create(params)
  render json: @resource
end

# Service object
class UserRegistration
  def call(params)
    return failure('Email required') if params[:email].blank?
    return failure('Email taken') if email_taken?(params[:email])
    return failure('Password too short') if params[:password].length < 8

    user = User.create(params)
    success(user)
  end
end

# Validator
def validate_age(age)
  return 'Age is required' if age.nil?
  return 'Age must be a number' unless age.is_a?(Numeric)
  return 'Age must be positive' unless age.positive?
  return 'Age must be realistic' if age > 150

  nil  # No errors
end
```

**Practical examples:**

```ruby
# File processing
def process_file(filename)
  return { error: 'Filename required' } if filename.nil?
  return { error: 'File not found' } unless File.exist?(filename)
  return { error: 'File not readable' } unless File.readable?(filename)
  return { error: 'File empty' } if File.zero?(filename)

  content = File.read(filename)
  process_content(content)
end

# API endpoint
def update
  return render_not_found unless @user
  return render_forbidden unless can_edit?(@user)
  return render_validation_errors(@user) unless @user.valid?

  @user.update(user_params)
  render json: @user
end

# Complex business logic
def approve_loan(application)
  # Early exits for invalid state
  return :already_processed if application.processed?
  return :missing_documents unless application.documents_complete?

  # Business rule checks
  return :income_too_low if application.income < minimum_income
  return :credit_score_too_low if application.credit_score < minimum_score
  return :debt_ratio_too_high if application.debt_ratio > maximum_ratio

  # All checks passed - approve loan
  application.approve!
  :approved
end

# Data transformation
def format_phone(phone)
  return nil if phone.nil?
  return nil if phone.empty?

  # Remove formatting
  digits = phone.gsub(/\D/, '')

  return nil if digits.length < 10
  return nil if digits.length > 11

  # Format as (XXX) XXX-XXXX
  area = digits[-10..-8]
  prefix = digits[-7..-5]
  line = digits[-4..-1]

  "(#{area}) #{prefix}-#{line}"
end
```

**Guard clause anti-patterns:**

```ruby
# Don't use guards for the happy path
# Bad
def process(value)
  return process_special(value) if value.special?

  nil  # Main logic shouldn't be the guard
end

# Good
def process(value)
  return nil unless value.special?

  process_special(value)
end

# Don't make guards too complex
# Bad
def process(data)
  return nil if data.nil? || data.empty? || !data.valid? || data.processed? || current_user.nil?

  # ...
end

# Good
def process(data)
  return nil if data.nil?
  return nil if data.empty?
  return nil unless data.valid?
  return nil if data.processed?
  return nil if current_user.nil?

  # ...
end
```

---

## 14.3 Safe Navigation Operator (`&.`)

Ruby's safe navigation operator `&.` (introduced in Ruby 2.3) is similar to Python's walrus operator or optional chaining in other languages. It allows you to call methods on potentially nil objects without raising NoMethodError.

**Key Similarities:**
- Both handle potential nil/None values
- Both short-circuit on nil/None
- Both improve code readability

**Key Differences:**
- Ruby uses `&.` (Python doesn't have exact equivalent)
- Python uses `if obj is not None` or try/except
- Ruby's operator works in method chains

### Python Example

```python
# Without safe navigation (manual checks)
if user is not None:
    name = user.name
else:
    name = None

# Or with try/except
try:
    name = user.name
except AttributeError:
    name = None

# Nested access
if user and user.profile and user.profile.address:
    city = user.profile.address.city
else:
    city = None

# Using getattr
name = getattr(user, 'name', None)

# Dictionary get (similar concept)
email = data.get('user', {}).get('email')
```

### Ruby Equivalent

```ruby
# Without safe navigation (manual checks)
name = user ? user.name : nil

# Or
if user
  name = user.name
else
  name = nil
end

# With safe navigation operator - much cleaner!
name = user&.name

# Nested access (chaining)
city = user&.profile&.address&.city

# Works with any method call
count = items&.count
first = array&.first
upcase = string&.upcase

# With method arguments
result = object&.method_with_args(arg1, arg2)

# With blocks
names = users&.map { |u| u.name }
```

### Explanation

The safe navigation operator `&.` only calls the method if the receiver is not nil. If the receiver is nil, it returns nil without raising an error.

**Basic usage:**

```ruby
# Without &.
user = find_user(id)
if user
  email = user.email
else
  email = nil
end

# With &.
email = find_user(id)&.email

# Returns nil if user is nil, otherwise returns user.email
```

**Chaining:**

```ruby
# Long chains without &.
if user && user.profile && user.profile.settings
  theme = user.profile.settings.theme
else
  theme = nil
end

# With &. - much cleaner
theme = user&.profile&.settings&.theme

# Each &. stops the chain if nil is encountered
```

**With method arguments:**

```ruby
# Method calls with arguments
full_name = user&.full_name('Mr.')
formatted = date&.strftime('%Y-%m-%d')
substring = text&.slice(0, 10)

# With keyword arguments
user&.update(name: 'Alice', age: 30)
```

**With blocks:**

```ruby
# Map over potentially nil collection
names = users&.map { |u| u.name }
# Returns nil if users is nil, otherwise maps

# Select
active = users&.select { |u| u.active? }

# Any/all
has_active = users&.any?(&:active?)
```

**With array/hash access:**

```ruby
# Array access
first = array&.first
last = array&.[](0)  # Explicit
value = array&.[](index)

# Hash access
email = user&.[](:email)  # Explicit
# Or better:
email = user&.fetch(:email, nil)
```

**Combining with || for defaults:**

```ruby
# Safe navigation with default value
name = user&.name || 'Guest'

# Or with fetch
email = user&.email || 'no-email@example.com'

# Can also use presence
title = post&.title&.presence || 'Untitled'
```

**Practical examples:**

```ruby
# View helper
def user_avatar_url(user)
  user&.profile&.avatar&.url || default_avatar_url
end

# API response
def user_data(user)
  {
    name: user&.name,
    email: user&.email,
    avatar: user&.profile&.avatar_url,
    role: user&.role&.name
  }
end

# Service object
class NotificationService
  def notify(user, message)
    user&.phone&.send_sms(message)
    user&.email&.send_email(message)
    user&.push_token&.send_push(message)
  end
end

# Database query
def latest_post_title(user_id)
  User.find_by(id: user_id)&.posts&.last&.title
end

# Configuration
def feature_enabled?(feature_name)
  config&.features&.dig(feature_name, :enabled) || false
end

# File operations
def file_size(path)
  File.exist?(path) && File.stat(path)&.size
end
```

**When NOT to use `&.`:**

```ruby
# Don't use when you need to distinguish between nil and false
# Bad
result = object&.enabled?  # Could return nil or false

# Good
result = object ? object.enabled? : false

# Don't use when nil should be an error
# Bad
user&.save!  # Silently returns nil if user is nil

# Good
raise 'User required' if user.nil?
user.save!

# Don't use when you need to handle the nil case differently
# Bad
name = user&.name || create_default_user.name

# Good
name = if user
         user.name
       else
         create_default_user.name
       end
```

**Comparison with try (Rails):**

```ruby
# Rails' try method (older alternative)
name = user.try(:name)
email = user.try(:profile).try(:email)

# Safe navigation operator (preferred in modern Ruby)
name = user&.name
email = user&.profile&.email

# try with blocks (can't do with &.)
# This is still useful in Rails
user.try { |u| u.name.upcase }
```

**Common pitfall:** Confusing `&.` with `&&`:

```ruby
# &. is safe navigation
result = user&.name  # Returns nil if user is nil

# && is boolean AND
result = user && user.name  # Also works but less idiomatic

# Don't mix them up
result = user&&.name  # Syntax error!
result = user &.name  # Syntax error!
```

---

## 14.4 Double Pipe Assignment (`||=`)

The `||=` operator is a Ruby idiom for assigning a value only if the variable is nil or false. It's commonly used for lazy initialization and default values.

**Key Similarities:**
- Both languages have ways to set default values
- Both can use boolean short-circuiting

**Key Differences:**
- Ruby has the `||=` operator built-in
- Python typically uses `if var is None: var = value`
- Ruby's `||=` is affected by truthiness

### Python Example

```python
# Set default if None
if cache is None:
    cache = {}

# Or using ternary
cache = cache if cache is not None else {}

# Dictionary setdefault
cache = {}
cache.setdefault('key', [])

# For memoization
class Calculator:
    def __init__(self):
        self._result = None

    def result(self):
        if self._result is None:
            self._result = expensive_calculation()
        return self._result

# Or with property
@property
def result(self):
    if not hasattr(self, '_result'):
        self._result = expensive_calculation()
    return self._result
```

### Ruby Equivalent

```ruby
# Set default if nil
cache ||= {}

# Expanded form (what ||= means):
# cache = cache || {}

# Or more explicitly:
# cache = cache ? cache : {}

# For instance variables
@result ||= expensive_calculation

# Hash default
cache[:key] ||= []

# Memoization pattern
class Calculator
  def result
    @result ||= expensive_calculation
  end
end

# Multiple assignments
@cache ||= {}
@config ||= load_config
@connection ||= establish_connection
```

### Explanation

The `||=` operator assigns a value to a variable only if the variable is currently nil or false. This makes it perfect for lazy initialization and default values.

**Basic usage:**

```ruby
# If var is nil or false, assign 'default'
var ||= 'default'

# Equivalent to:
var = var || 'default'

# Or:
var = 'default' if var.nil? || var == false

# Or more precisely:
var = 'default' unless var
```

**Common use case - lazy initialization:**

```ruby
# Memoization (caching expensive calculations)
def users
  @users ||= User.all.to_a
end

# First call: fetches from database and caches
# Subsequent calls: returns cached value

# Configuration loading
def config
  @config ||= YAML.load_file('config.yml')
end

# Default empty collections
def items
  @items ||= []
end

def options
  @options ||= {}
end
```

**Hash defaults:**

```ruby
# Initialize hash value if not set
cache = {}
cache[:users] ||= []
cache[:users] << user

# Nested hash initialization
data = {}
data[:user] ||= {}
data[:user][:name] = 'Alice'

# Counter pattern
counts = Hash.new(0)
counts[:visits] ||= 0
counts[:visits] += 1
```

**Method parameters:**

```ruby
def greet(name = nil)
  name ||= 'Guest'
  "Hello, #{name}!"
end

# Or in method body
def search(query, limit = nil)
  limit ||= 10

  results.where(query: query).limit(limit)
end
```

**Important: `||=` gotcha with false values:**

```ruby
# IMPORTANT: ||= treats false as falsy!
enabled = false
enabled ||= true
puts enabled  # true (not false!)

# Because it expands to:
enabled = enabled || true
enabled = false || true
enabled = true

# If you need to preserve false, use explicit nil check
enabled = true if enabled.nil?

# Or:
enabled = enabled.nil? ? true : enabled
```

**Nil-only assignment:**

```ruby
# To assign only when nil (not false), be explicit
value = nil
value = value.nil? ? 'default' : value

# Or use fetch for hashes
hash[:key] = hash.fetch(:key, 'default')

# Or define a custom operator (not standard)
def nil_assign(var, default)
  var.nil? ? default : var
end
```

**Practical examples:**

```ruby
# Class-level configuration
class MyClass
  class << self
    def config
      @config ||= { timeout: 30, retries: 3 }
    end
  end
end

# Singleton pattern
class Logger
  def self.instance
    @instance ||= new
  end

  private_class_method :new
end

# View helper
def current_user
  @current_user ||= User.find_by(id: session[:user_id])
end

# Cache expensive operations
def total_sales
  @total_sales ||= Order.sum(:total)
end

# Default options pattern
def process(options = {})
  options[:timeout] ||= 30
  options[:retries] ||= 3
  options[:log] ||= true

  # Process with options
end

# Resource loading
class Config
  def database
    @database ||= load_database_config
  end

  def api
    @api ||= load_api_config
  end

  private

  def load_database_config
    YAML.load_file('config/database.yml')
  end

  def load_api_config
    YAML.load_file('config/api.yml')
  end
end
```

**Thread-safety concern:**

```ruby
# ||= is NOT thread-safe!
# Bad for multi-threaded code
def connection
  @connection ||= establish_connection  # Race condition!
end

# Better: use Mutex
require 'thread'

def connection
  @connection_mutex ||= Mutex.new
  @connection_mutex.synchronize do
    @connection ||= establish_connection
  end
end

# Or use thread-local storage
def connection
  Thread.current[:connection] ||= establish_connection
end
```

**Common patterns:**

```ruby
# 1. Memoization
def expensive_data
  @expensive_data ||= fetch_from_api
end

# 2. Default empty collections
def tags
  @tags ||= []
end

# 3. Configuration
def max_retries
  @max_retries ||= ENV.fetch('MAX_RETRIES', 3).to_i
end

# 4. Lazy loading
def user
  @user ||= User.find(user_id)
end

# 5. Hash accumulation
stats = {}
stats[:count] ||= 0
stats[:count] += 1

# 6. Options merging
def render(options = {})
  options[:status] ||= 200
  options[:format] ||= :json

  # ...
end
```

**When to use alternatives:**

```ruby
# Use explicit nil check when false is valid
def enabled?
  @enabled = true if @enabled.nil?
  @enabled
end

# Use Hash#fetch for hashes
value = hash.fetch(:key) { default_value }

# Use presence for Rails
name = params[:name].presence || 'Guest'

# Use defined? for checking if variable exists
@cache = {} unless defined?(@cache)
```

---

## 14.5 Parallel Assignment

Parallel assignment (also called multiple assignment or destructuring) allows you to assign multiple variables in a single expression. This is similar to tuple unpacking in Python.

**Key Similarities:**
- Both support multiple assignment
- Both can swap values without temp variables
- Both work with arrays/lists

**Key Differences:**
- Ruby's syntax is more flexible
- Ruby has splat operator for capturing remaining values
- Different handling of mismatched sizes

### Python Example

```python
# Basic tuple unpacking
x, y = 1, 2

# Swap values
a, b = b, a

# Unpack from list
first, second, third = [1, 2, 3]

# Unpack with *
first, *rest = [1, 2, 3, 4, 5]
# first = 1, rest = [2, 3, 4, 5]

first, *middle, last = [1, 2, 3, 4, 5]
# first = 1, middle = [2, 3, 4], last = 5

# Unpack nested structures
(a, b), c = (1, 2), 3

# Function return values
def get_coordinates():
    return 10, 20

x, y = get_coordinates()

# Ignore values with _
name, _, age = ('Alice', 'middlename', 30)
```

### Ruby Equivalent

```ruby
# Basic parallel assignment
x, y = 1, 2

# Swap values
a, b = b, a

# Unpack from array
first, second, third = [1, 2, 3]

# Unpack with splat (*)
first, *rest = [1, 2, 3, 4, 5]
# first = 1, rest = [2, 3, 4, 5]

first, *middle, last = [1, 2, 3, 4, 5]
# first = 1, middle = [2, 3, 4], last = 5

# Unpack nested structures
(a, b), c = [1, 2], 3

# Method return values
def get_coordinates
  [10, 20]
end

x, y = get_coordinates

# Ignore values with _
name, _, age = ['Alice', 'middlename', 30]

# Or without underscore (just don't use the variable)
name, age = ['Alice', 30]  # Ignores extra values if any
```

### Explanation

Parallel assignment allows you to assign multiple variables from an array or multiple values in a single expression.

**Basic usage:**

```ruby
# Assign multiple variables
a, b, c = 1, 2, 3
puts a  # 1
puts b  # 2
puts c  # 3

# From array
x, y, z = [10, 20, 30]

# Mixed
first, second = 'a', 'b'
```

**Swapping values:**

```ruby
# Swap without temp variable
a = 1
b = 2
a, b = b, a
puts a  # 2
puts b  # 1

# Rotate values
x, y, z = z, x, y
```

**Splat operator (*):**

```ruby
# Capture remaining values
first, *rest = [1, 2, 3, 4, 5]
puts first  # 1
puts rest   # [2, 3, 4, 5]

# Middle values
first, *middle, last = [1, 2, 3, 4, 5]
puts first   # 1
puts middle  # [2, 3, 4]
puts last    # 5

# Just first and rest
head, *tail = [1, 2, 3]
# head = 1, tail = [2, 3]

# All but first
_, *rest = [1, 2, 3]
# rest = [2, 3]
```

**Mismatched sizes:**

```ruby
# More values than variables (extras ignored)
a, b = 1, 2, 3, 4
puts a  # 1
puts b  # 2
# 3 and 4 are ignored

# Fewer values than variables (nils assigned)
x, y, z = 1, 2
puts x  # 1
puts y  # 2
puts z  # nil

# With splat
a, *rest = [1]
puts a     # 1
puts rest  # []
```

**Nested destructuring:**

```ruby
# Nested arrays
(a, b), c = [[1, 2], 3]
puts a  # 1
puts b  # 2
puts c  # 3

# Deep nesting
(a, (b, c)), d = [[1, [2, 3]], 4]
puts a  # 1
puts b  # 2
puts c  # 3
puts d  # 4
```

**Method return values:**

```ruby
# Return multiple values
def min_max(array)
  [array.min, array.max]
end

min, max = min_max([3, 1, 4, 1, 5])
puts "Min: #{min}, Max: #{max}"

# Return hash (destructures to array)
def user_info
  { name: 'Alice', age: 30 }
end

info = user_info
# info is the hash

# Or destructure hash to array
name, age = user_info.values_at(:name, :age)
```

**Practical examples:**

```ruby
# Parsing CSV-like data
row = "Alice,30,alice@example.com"
name, age, email = row.split(',')

# Iteration with parallel assignment
users = [
  ['Alice', 30],
  ['Bob', 25],
  ['Charlie', 35]
]

users.each do |name, age|
  puts "#{name} is #{age} years old"
end

# Hash iteration
data = { name: 'Alice', age: 30, city: 'NYC' }
data.each do |key, value|
  puts "#{key}: #{value}"
end

# Partition results
numbers = [1, 2, 3, 4, 5, 6]
evens, odds = numbers.partition(&:even?)
# evens = [2, 4, 6], odds = [1, 3, 5]

# Coordinate handling
def distance(point1, point2)
  x1, y1 = point1
  x2, y2 = point2

  Math.sqrt((x2 - x1)**2 + (y2 - y1)**2)
end

dist = distance([0, 0], [3, 4])  # 5.0

# File path splitting
path = "/home/user/documents/file.txt"
*dirs, filename = path.split('/')
# dirs = ["", "home", "user", "documents"]
# filename = "file.txt"

# Taking specific positions
_, _, year, month, day = "2025-01-07".match(/(\d{4})-(\d{2})-(\d{2})/).to_a
# Ignores full match and captures

# Array manipulation
array = [1, 2, 3, 4, 5]
first, *middle, last = array
middle.map! { |x| x * 2 }
result = [first, *middle, last]
# [1, 4, 6, 8, 5]
```

**With blocks:**

```ruby
# Block parameters with parallel assignment
[[1, 2], [3, 4], [5, 6]].each do |a, b|
  puts "#{a} + #{b} = #{a + b}"
end

# Hash iteration
{ name: 'Alice', age: 30 }.each do |key, value|
  puts "#{key}: #{value}"
end

# With indices
['a', 'b', 'c'].each_with_index do |letter, index|
  puts "#{index}: #{letter}"
end

# Complex destructuring in blocks
[[1, [2, 3]], [4, [5, 6]]].each do |a, (b, c)|
  puts "#{a}, #{b}, #{c}"
end
```

**Ignoring values:**

```ruby
# Use _ for values you don't need
name, _, age = ['Alice', 'unwanted', 30]

# Multiple underscores (each is separate)
first, _, _, last = [1, 2, 3, 4]

# Just take what you need
first, second = [1, 2, 3, 4, 5]
# Ignores 3, 4, 5

# With splat to ignore rest
first, * = [1, 2, 3, 4, 5]
# first = 1, rest ignored
```

**Common patterns:**

```ruby
# 1. Swapping
x, y = y, x

# 2. Function with multiple returns
status, message = validate_user(user)

# 3. Splitting strings
host, port = "localhost:3000".split(':')

# 4. Array destructuring
first, *rest = array

# 5. Hash to variables
name, email = user.values_at(:name, :email)

# 6. Iteration
data.each do |key, value|
  # ...
end

# 7. Regex matches
_, year, month, day = date_string.match(/(\d{4})-(\d{2})-(\d{2})/).to_a
```

---

## 14.6 String and Array Destructuring

Ruby provides powerful destructuring capabilities for strings and arrays, allowing you to extract values efficiently. This builds on parallel assignment with additional string and array-specific methods.

**Key Similarities:**
- Both support slicing and indexing
- Both can split strings into parts
- Both work with pattern matching

**Key Differences:**
- Ruby's destructuring is more integrated with parallel assignment
- Different string methods and syntax
- Ruby has more built-in methods for common patterns

### Python Example

```python
# String slicing
text = "Hello, World!"
first_five = text[:5]  # "Hello"
last_five = text[-5:]  # "orld!"

# String splitting
name = "Alice Bob Smith"
first, *rest = name.split()
# first = "Alice", rest = ["Bob", "Smith"]

# Regex groups
import re
match = re.match(r'(\d{4})-(\d{2})-(\d{2})', '2025-01-07')
if match:
    year, month, day = match.groups()

# Array slicing
numbers = [1, 2, 3, 4, 5]
first_three = numbers[:3]
last_two = numbers[-2:]

# Unpacking
head, *tail = [1, 2, 3, 4, 5]
```

### Ruby Equivalent

```ruby
# String slicing
text = "Hello, World!"
first_five = text[0, 5]    # "Hello"
last_five = text[-5..-1]   # "orld!"

# String splitting
name = "Alice Bob Smith"
first, *rest = name.split
# first = "Alice", rest = ["Bob", "Smith"]

# Regex captures
match = '2025-01-07'.match(/(\d{4})-(\d{2})-(\d{2})/)
year, month, day = match.captures

# Or with named captures
match = '2025-01-07'.match(/(?<year>\d{4})-(?<month>\d{2})-(?<day>\d{2})/)
year = match[:year]

# Array slicing
numbers = [1, 2, 3, 4, 5]
first_three = numbers[0, 3]  # or numbers[0..2]
last_two = numbers[-2..-1]

# Unpacking
head, *tail = [1, 2, 3, 4, 5]
```

### Explanation

Ruby provides multiple ways to destructure strings and arrays, combining slicing, splitting, regex matching, and parallel assignment.

**String destructuring with split:**

```ruby
# Basic split
text = "one,two,three"
parts = text.split(',')  # ["one", "two", "three"]

# Split with parallel assignment
first, second, third = text.split(',')

# Split with limit
first, rest = text.split(',', 2)
# first = "one", rest = "two,three"

# Split with splat
first, *rest = text.split(',')
# first = "one", rest = ["two", "three"]

# Split lines
text = "line1\nline2\nline3"
lines = text.split("\n")
# Or
lines = text.lines  # Returns array with newlines
lines = text.lines.map(&:chomp)  # Without newlines
```

**String destructuring with slicing:**

```ruby
text = "Hello, World!"

# Character access
first_char = text[0]        # "H"
last_char = text[-1]        # "!"

# Substring with index and length
substr = text[0, 5]         # "Hello"

# Substring with range
substr = text[0..4]         # "Hello" (inclusive)
substr = text[0...5]        # "Hello" (exclusive)

# From end
last_five = text[-5..-1]    # "orld!"

# Multiple values (custom method)
def extract_parts(string)
  [string[0, 5], string[7, 5]]
end

hello, world = extract_parts("Hello, World!")
# hello = "Hello", world = "World"
```

**String destructuring with regex:**

```ruby
# Regex match and capture
text = "Name: Alice, Age: 30"
match = text.match(/Name: (\w+), Age: (\d+)/)

if match
  _, name, age = match.to_a
  # _ = full match, name = "Alice", age = "30"

  # Or just captures
  name, age = match.captures
end

# Named captures
match = text.match(/Name: (?<name>\w+), Age: (?<age>\d+)/)
name = match[:name]
age = match[:age]

# Scan for multiple matches
text = "Prices: $10, $20, $30"
prices = text.scan(/\$(\d+)/).flatten
# ["10", "20", "30"]

# Destructure in block
text.scan(/\$(\d+)/) do |price|
  puts "Found: $#{price}"
end
```

**Array destructuring:**

```ruby
# Basic
arr = [1, 2, 3, 4, 5]
first, second, third = arr

# With splat
first, *rest = arr
# first = 1, rest = [2, 3, 4, 5]

first, *middle, last = arr
# first = 1, middle = [2, 3, 4], last = 5

# Just first and last
first, *_, last = arr

# Take specific positions
a, _, c, _, e = [1, 2, 3, 4, 5]
# a = 1, c = 3, e = 5
```

**Array slicing:**

```ruby
arr = [1, 2, 3, 4, 5]

# First N elements
first_three = arr[0, 3]     # [1, 2, 3]
first_three = arr[0..2]     # [1, 2, 3]
first_three = arr.take(3)   # [1, 2, 3]
first_three = arr.first(3)  # [1, 2, 3]

# Last N elements
last_two = arr[-2..-1]      # [4, 5]
last_two = arr.last(2)      # [4, 5]

# Drop first N
rest = arr[3..-1]           # [4, 5]
rest = arr.drop(3)          # [4, 5]

# Middle section
middle = arr[1..3]          # [2, 3, 4]
middle = arr[1, 3]          # [2, 3, 4]
```

**Nested destructuring:**

```ruby
# Nested arrays
data = [[1, 2], [3, 4]]
(a, b), (c, d) = data
# a = 1, b = 2, c = 3, d = 4

# Deeper nesting
coords = [[[1, 2], [3, 4]], [[5, 6], [7, 8]]]
((x1, y1), (x2, y2)), ((x3, y3), (x4, y4)) = coords

# Partial nested destructuring
data = [[1, 2, 3], [4, 5, 6]]
(a, *rest1), (b, *rest2) = data
# a = 1, rest1 = [2, 3], b = 4, rest2 = [5, 6]
```

**Practical examples:**

```ruby
# Parse CSV row
row = "Alice,30,alice@example.com,NYC"
name, age, email, city = row.split(',')

# Parse date
date_string = "2025-01-07"
year, month, day = date_string.split('-')

# Or with regex
_, year, month, day = date_string.match(/(\d{4})-(\d{2})-(\d{2})/).to_a

# Parse URL
url = "https://example.com:8080/path/to/page"
protocol, rest = url.split('://', 2)
host_port, path = rest.split('/', 2)
host, port = host_port.split(':')

# Or with regex
match = url.match(%r{^(\w+)://([^:/]+):(\d+)(/.*)$})
protocol, host, port, path = match.captures

# Parse log line
log = "[2025-01-07 10:30:15] ERROR: Connection failed"
timestamp, level, message = log.match(/\[(.*?)\] (\w+): (.*)/).captures

# Parse key-value pairs
config = "name=Alice age=30 city=NYC"
pairs = config.split.map { |pair| pair.split('=') }
# [["name", "Alice"], ["age", "30"], ["city", "NYC"]]

# Convert to hash
data = pairs.to_h
# {"name" => "Alice", "age" => "30", "city" => "NYC"}

# Extract file parts
path = "/home/user/documents/file.txt"
*dirs, filename = path.split('/')
dir_path = dirs.join('/')
basename, ext = filename.split('.')

# Parse email
email = "alice.smith@example.com"
username, domain = email.split('@')
first, last = username.split('.')

# Process table data
table = [
  ["Name", "Age", "City"],
  ["Alice", "30", "NYC"],
  ["Bob", "25", "LA"]
]

headers, *rows = table
rows.each do |name, age, city|
  puts "#{name} is #{age} years old and lives in #{city}"
end
```

**Pattern matching (Ruby 2.7+):**

```ruby
# Pattern matching with arrays
case [1, 2, 3]
in [a, b, c]
  puts "#{a}, #{b}, #{c}"
end

# With guards
case [1, 2, 3]
in [a, b, c] if a == 1
  puts "First is 1"
end

# Splat in pattern
case [1, 2, 3, 4, 5]
in [first, *rest]
  puts "First: #{first}, Rest: #{rest}"
end

# Hash patterns
case { name: 'Alice', age: 30 }
in { name:, age: }
  puts "#{name} is #{age}"
end
```

**Common patterns:**

```ruby
# 1. Parse structured strings
first, last = "Alice Smith".split
year, month, day = "2025-01-07".split('-')

# 2. Extract from arrays
first, *rest = array
first, *middle, last = array

# 3. Regex captures
host, port = url.match(/([^:]+):(\d+)/).captures

# 4. Iterate with destructuring
data.each do |name, value|
  # ...
end

# 5. Swap values
a, b = b, a

# 6. Multiple return values
min, max = find_min_max(array)
```

---

## 14.7 Using `_` for Unused Variables

In Ruby, the underscore `_` is conventionally used to indicate that a variable's value is intentionally not being used. This improves code readability by making it clear that certain values are ignored.

**Key Similarities:**
- Both Python and Ruby use `_` for unused variables
- Both make code intent clearer
- Both are conventions, not language requirements

**Key Differences:**
- Ruby allows multiple `_` in parallel assignment
- Python's `_` has special meaning in REPL
- Slightly different usage conventions

### Python Example

```python
# Ignore values in unpacking
name, _, age = ('Alice', 'middle', 30)

# Multiple ignores (must use different names in Python)
a, _, b, _2, c = (1, 2, 3, 4, 5)

# In loops
for _ in range(10):
    print("Hello")

# With enumerate (ignore index)
for _, value in enumerate(items):
    process(value)

# With enumerate (ignore value)
for index, _ in enumerate(items):
    print(index)

# Function parameters
def callback(success, error, _):
    # Third parameter not used
    return success or error

# Note: In Python REPL, _ holds last result
>>> 5 + 3
8
>>> _
8
```

### Ruby Equivalent

```ruby
# Ignore values in unpacking
name, _, age = ['Alice', 'middle', 30]

# Multiple ignores (same _ can be used)
a, _, b, _, c = [1, 2, 3, 4, 5]

# In loops
10.times do |_|
  puts "Hello"
end

# With each_with_index (ignore index)
items.each_with_index do |value, _|
  process(value)
end

# With each_with_index (ignore value)
items.each_with_index do |_, index|
  puts index
end

# Method parameters
def callback(success, error, _)
  # Third parameter not used
  success || error
end

# Block parameters
hash.each do |_, value|
  # Key not used, only value
  puts value
end
```

### Explanation

Using `_` makes it explicit that you're intentionally ignoring a value, improving code readability and signaling intent to other developers.

**In parallel assignment:**

```ruby
# Ignore middle values
first, _, last = [1, 2, 3]
# first = 1, last = 3, middle value ignored

# Multiple underscores are fine
a, _, _, b = [1, 2, 3, 4]
# a = 1, b = 4

# With splat
first, *_, last = [1, 2, 3, 4, 5]
# first = 1, last = 5, middle values ignored
```

**In block parameters:**

```ruby
# Ignore index in times
5.times do |_|
  puts "Hello"
end

# Ignore key in hash iteration
user = { name: 'Alice', age: 30, city: 'NYC' }
user.each do |_, value|
  puts value
end

# Ignore value
user.each do |key, _|
  puts key
end

# With each_with_index, ignore index
items.each_with_index do |item, _|
  process(item)
end

# Or ignore item
items.each_with_index do |_, index|
  puts "Position: #{index}"
end
```

**In method parameters:**

```ruby
# Indicate unused parameter
def greet(name, _age, city)
  # age parameter not used but required by interface
  "Hello, #{name} from #{city}!"
end

# Callback with unused args
def on_complete(success, _, _)
  puts "Done!" if success
end

# API compatibility
class BaseService
  def call(data, _options = {})
    # Base implementation doesn't use options
    process(data)
  end
end

class ExtendedService < BaseService
  def call(data, options = {})
    # Extended version uses options
    process(data, options)
  end
end
```

**In regex matches:**

```ruby
# Ignore full match, extract captures
_, year, month, day = "2025-01-07".match(/(\d{4})-(\d{2})-(\d{2})/).to_a
# _ = "2025-01-07" (full match, ignored)

# Or use captures to avoid underscore
year, month, day = "2025-01-07".match(/(\d{4})-(\d{2})-(\d{2})/).captures
```

**Practical examples:**

```ruby
# Iterate N times (don't need counter)
3.times do |_|
  puts "Executing task"
end

# Hash iteration (only need values)
config = { timeout: 30, retries: 3, log: true }
config.each do |_, value|
  validate(value)
end

# Only need specific positions from array
rows = CSV.read('data.csv')
rows.each do |id, _, _, name, email, _|
  # Only using id, name, email
  # Columns 2, 3, and 6 are ignored
  User.create(id: id, name: name, email: email)
end

# API response with unused fields
response = api.fetch_user
id, name, _, _, email = response.values
# Don't need fields 3 and 4

# Callback interface
class EventHandler
  def on_event(event_type, _timestamp, _metadata)
    # Only care about event_type in base implementation
    handle(event_type)
  end
end

# Pattern matching (Ruby 2.7+)
case data
in [first, _, last]
  puts "First: #{first}, Last: #{last}"
end
```

**When NOT to use `_`:**

```ruby
# If you might use the value later, give it a name
# Bad
items.each_with_index do |item, _|
  puts item
  # Later: wait, I need the index!
end

# Good
items.each_with_index do |item, index|
  puts item
end

# If the parameter is self-documenting
# Okay (but consider naming it)
def process(_, options)
  # What is the first parameter?
end

# Better
def process(data, options)
  # Clear what data is, even if unused
end
```

**Multiple underscores:**

```ruby
# Ruby allows multiple _ (unlike Python)
a, _, _, _, b = [1, 2, 3, 4, 5]
# All middle values ignored

# With splat for many ignored values
first, *_, last = [1, 2, 3, 4, 5, 6, 7, 8, 9]
# Cleaner than multiple underscores
```

**Common patterns:**

```ruby
# 1. Ignoring block parameter
10.times { |_| puts "Hello" }

# 2. Hash iteration (keys only)
hash.each { |key, _| puts key }

# 3. Hash iteration (values only)
hash.each { |_, value| puts value }

# 4. Parallel assignment (ignore middle)
first, _, last = array

# 5. Regex matches (ignore full match)
_, group1, group2 = string.match(/pattern/).to_a

# 6. Required but unused parameters
def handler(required, _, _)
  process(required)
end

# 7. Destructuring (ignore positions)
id, name, _, _, email = data
```

---

## 14.8 `then` (method chaining)

The `then` method (also aliased as `yield_self`) allows you to chain operations in a pipeline style, passing the result of one operation as input to the next. It's useful for transforming values through a series of steps.

**Key Similarities:**
- Both languages support method chaining
- Both can create pipelines of transformations
- Both improve readability for sequential operations

**Key Differences:**
- Ruby has explicit `then` method
- Python typically uses function composition or method chaining
- Ruby's `then` can use blocks for complex transformations

### Python Example

```python
# Method chaining
result = "  hello world  ".strip().upper().replace(' ', '_')

# Function composition (manual)
def process(text):
    text = text.strip()
    text = text.upper()
    text = text.replace(' ', '_')
    return text

result = process("  hello world  ")

# Using functions in sequence
from functools import reduce

def pipeline(*functions):
    def apply(value):
        return reduce(lambda v, f: f(v), functions, value)
    return apply

result = pipeline(
    str.strip,
    str.upper,
    lambda s: s.replace(' ', '_')
)("  hello world  ")

# Or assign intermediate values
text = "  hello world  "
text = text.strip()
text = text.upper()
text = text.replace(' ', '_')
```

### Ruby Equivalent

```ruby
# Method chaining (traditional)
result = "  hello world  ".strip.upcase.gsub(' ', '_')

# With then for complex operations
result = "  hello world  ".then { |s| s.strip }
                          .then { |s| s.upcase }
                          .then { |s| s.gsub(' ', '_') }

# Or more idiomatically
result = "  hello world  "
  .then(&:strip)
  .then(&:upcase)
  .then { |s| s.gsub(' ', '_') }

# Combining simple and complex operations
value = initial_value
  .then(&:strip)
  .then { |v| complex_transformation(v) }
  .then(&:upcase)

# Pipeline style
result = data
  .then { |d| parse(d) }
  .then { |parsed| validate(parsed) }
  .then { |valid| transform(valid) }
  .then { |transformed| save(transformed) }
```

### Explanation

The `then` method passes its receiver to a block and returns the block's result. This allows you to chain operations that don't naturally return the receiver, creating clean data transformation pipelines.

**Basic usage:**

```ruby
# Without then
value = "hello"
value = value.upcase
value = value.reverse
result = value

# With then
result = "hello".then { |s| s.upcase }
                .then { |s| s.reverse }

# Or with &:method_name
result = "hello".then(&:upcase)
                .then(&:reverse)
```

**Why use `then`:**

```ruby
# Some operations don't return self
# Bad - can't chain
array = [1, 2, 3]
array.map { |x| x * 2 }
.select { |x| x > 3 }  # Error! map returns new array

# Good - works because map returns an array
array = [1, 2, 3]
result = array.map { |x| x * 2 }
              .select { |x| x > 3 }

# then lets you chain anything
value = 10
result = value.then { |v| v * 2 }
              .then { |v| v + 5 }
              .then { |v| v.to_s }
# "25"
```

**Pipeline transformations:**

```ruby
# Clean data transformation pipeline
user_data = fetch_raw_data
  .then { |raw| parse_json(raw) }
  .then { |json| extract_user_fields(json) }
  .then { |fields| validate_fields(fields) }
  .then { |valid| normalize_data(valid) }
  .then { |normalized| User.new(normalized) }

# Or with intermediate variable names for clarity
user_data = fetch_raw_data
  .then { |raw| parse_json(raw) }
  .then { |parsed| extract_user_fields(parsed) }
  .then { |fields| validate_fields(fields) }
  .then { |validated| normalize_data(validated) }
  .then { |data| User.new(data) }
```

**With tap for side effects:**

```ruby
# Combine then with tap
result = initial_value
  .then { |v| transform(v) }
  .tap { |v| log("Transformed: #{v}") }
  .then { |v| v.upcase }
  .tap { |v| log("Uppercased: #{v}") }

# tap returns receiver, then returns block result
# tap is for side effects (logging, debugging)
# then is for transformations
```

**Practical examples:**

```ruby
# API request processing
response = fetch_api_data(url)
  .then { |raw| JSON.parse(raw) }
  .then { |json| json['data'] }
  .then { |data| data.map { |item| transform_item(item) } }
  .then { |items| items.select { |item| item.valid? } }

# File processing
content = File.read('data.txt')
  .then { |text| text.strip }
  .then { |text| text.split("\n") }
  .then { |lines| lines.map(&:chomp) }
  .then { |lines| lines.reject(&:empty?) }
  .then { |lines| lines.map { |line| line.split(',') } }

# Configuration building
config = ENV['CONFIG_JSON']
  .then { |json| JSON.parse(json) }
  .then { |data| data.transform_keys(&:to_sym) }
  .then { |hash| hash.merge(defaults) }
  .then { |merged| validate_config(merged) }

# Data calculation
total = orders
  .then { |o| o.select(&:paid?) }
  .then { |paid| paid.map(&:amount) }
  .then { |amounts| amounts.sum }
  .then { |sum| sum * tax_rate }
  .then { |with_tax| with_tax.round(2) }

# String processing
slug = title
  .then(&:downcase)
  .then { |s| s.gsub(/[^a-z0-9\s-]/, '') }
  .then { |s| s.strip }
  .then { |s| s.gsub(/\s+/, '-') }
  .then { |s| s.gsub(/-+/, '-') }
```

**Combining with other methods:**

```ruby
# Mix then with regular method chaining
result = array
  .map(&:to_s)
  .then { |strings| strings.join(', ') }
  .then { |csv| "Items: #{csv}" }
  .upcase

# With pattern matching (Ruby 2.7+)
value = data
  .then { |d| parse(d) }
  .then do |parsed|
    case parsed
    in { type: 'user', **rest }
      process_user(rest)
    in { type: 'order', **rest }
      process_order(rest)
    else
      raise "Unknown type"
    end
  end
```

**Error handling with then:**

```ruby
# Wrap in begin/rescue
result = data
  .then { |d| parse(d) }
  .then do |parsed|
    begin
      validate(parsed)
      parsed
    rescue ValidationError => e
      log_error(e)
      default_value
    end
  end
  .then { |v| transform(v) }

# Or use rescue modifier
result = data
  .then { |d| parse(d) rescue default_data }
  .then { |parsed| validate(parsed) rescue default_value }
```

**yield_self alias:**

```ruby
# then is an alias for yield_self (Ruby 2.5+)
# then is preferred, more readable

# These are identical
result = value.then { |v| v * 2 }
result = value.yield_self { |v| v * 2 }

# Use then in modern code
```

**When NOT to use then:**

```ruby
# Don't use then for simple method chaining
# Bad
name.then(&:strip).then(&:upcase).then(&:reverse)

# Good
name.strip.upcase.reverse

# Don't use then when intermediate variables are clearer
# Bad (hard to debug)
result = data.then { |d| d.map { |x| x * 2 } }
             .then { |d| d.select { |x| x > 5 } }
             .then { |d| d.sum }

# Good (easier to debug)
doubled = data.map { |x| x * 2 }
filtered = doubled.select { |x| x > 5 }
result = filtered.sum
```

**Common patterns:**

```ruby
# 1. Data transformation pipeline
result = input
  .then { |i| parse(i) }
  .then { |p| validate(p) }
  .then { |v| process(v) }

# 2. Conditional transformation
value.then { |v| condition ? transform(v) : v }

# 3. Complex calculation
total.then { |t| t * tax_rate }
     .then { |t| t + shipping }
     .then { |t| t.round(2) }

# 4. Multi-step conversion
string.then { |s| s.to_i }
      .then { |i| i * 100 }
      .then { |i| i.to_s }

# 5. Debugging pipeline
value.then { |v| puts "Before: #{v}"; v }
     .then { |v| transform(v) }
     .then { |v| puts "After: #{v}"; v }
```

---

This chapter covered essential Ruby idioms and best practices:

- **The Ruby Way** - Core idioms like preferring blocks, using predicate methods, statement modifiers, and symbol-to-proc
- **Guard Clauses** - Early returns to handle edge cases and keep main logic clean and unindented
- **Safe Navigation (&.)** - Elegant nil handling in method chains without explicit checks
- **Double Pipe Assignment (||=)** - Lazy initialization and memoization pattern
- **Parallel Assignment** - Destructuring arrays and multiple assignment for clean code
- **String and Array Destructuring** - Extracting values from strings and arrays efficiently
- **Using _ for Unused Variables** - Making intent clear when ignoring values
- **then (method chaining)** - Creating clean data transformation pipelines

These idioms make Ruby code more expressive, readable, and maintainable. They represent the "Ruby way" of writing code that the community values and uses in real-world applications.