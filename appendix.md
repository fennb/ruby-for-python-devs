# Appendix

---

## A. Ruby Style Guide

Ruby has strong community conventions around code style. Following these conventions makes your code more readable to other Rubyists and helps you write idiomatic Ruby.

### Naming Conventions

**Variables and methods: snake_case**
```ruby
# Good
user_name = "Alice"
def calculate_total(items)
  items.sum
end

# Bad (Python-like, but not Ruby style)
userName = "Alice"  # camelCase
def calculateTotal(items)
  items.sum
end
```

**Classes and modules: PascalCase**
```ruby
# Good
class UserAccount
end

module DatabaseConnection
end

# Bad
class User_account
end
```

**Constants: SCREAMING_SNAKE_CASE**
```ruby
# Good
MAX_RETRIES = 3
API_ENDPOINT = "https://api.example.com"

# Also acceptable for classes/modules
class User
end
```

**Predicate methods: end with ?**
```ruby
# Good
def valid?
  errors.empty?
end

def admin?
  role == :admin
end

# Bad
def is_valid
  errors.empty?
end
```

**Dangerous/mutating methods: end with !**
```ruby
# Good
def save!  # Raises exception on failure
  raise "Failed" unless valid?
  # save logic
end

def reverse!  # Modifies in place
  @items.reverse!
end

# Bad - only use ! when there's a non-! version
def create!  # Bad if there's no 'create' method
end
```

### Indentation and Formatting

**Use 2 spaces (not tabs)**
```ruby
# Good
def calculate_total
  items.map do |item|
    item.price * item.quantity
  end.sum
end

# Bad (4 spaces like Python)
def calculate_total
    items.map do |item|
        item.price * item.quantity
    end.sum
end
```

**Line length: 80-120 characters**
```ruby
# Good - break long lines
def send_notification(user, message, options = {})
  NotificationService.send(
    user: user,
    message: message,
    options: options
  )
end

# Bad - too long
def send_notification(user, message, options = {})
  NotificationService.send(user: user, message: message, options: options, timestamp: Time.now)
end
```

**Method chaining**
```ruby
# Good - each method on new line for readability
users
  .select(&:active?)
  .map(&:email)
  .sort

# Acceptable for short chains
users.map(&:email).sort

# Bad - hard to read
users.select(&:active?).map(&:email).sort.uniq.join(", ")
```

### Control Flow

**Prefer unless over if !**
```ruby
# Good
unless user.admin?
  raise "Unauthorized"
end

# Bad
if !user.admin?
  raise "Unauthorized"
end
```

**Use statement modifiers for simple conditions**
```ruby
# Good
return unless user.valid?
send_email if user.subscribed?

# Bad for simple cases
if user.subscribed?
  send_email
end
```

**No parentheses for control flow**
```ruby
# Good
if user.admin?
  grant_access
end

# Bad
if(user.admin?)
  grant_access
end
```

### Methods

**Parentheses: use for method definitions, optional for calls**
```ruby
# Good
def calculate_total(items, tax_rate)
  items.sum * (1 + tax_rate)
end

# Method calls - optional
calculate_total items, 0.08
calculate_total(items, 0.08)  # Also fine

# Bad - no parens in definition
def calculate_total items, tax_rate
  items.sum * (1 + tax_rate)
end
```

**Implicit return**
```ruby
# Good - implicit return
def full_name
  "#{first_name} #{last_name}"
end

# Bad - unnecessary return
def full_name
  return "#{first_name} #{last_name}"
end

# Good - explicit return for early exit
def process
  return nil unless valid?
  perform_operation
end
```

**Single-line methods for simple cases**
```ruby
# Good
def admin?; role == :admin; end
def full_name; "#{first_name} #{last_name}"; end

# Also acceptable
def admin?
  role == :admin
end
```

### Blocks

**Use {  } for single-line blocks, do...end for multi-line**
```ruby
# Good
users.each { |user| puts user.name }

users.each do |user|
  send_email(user)
  log_action(user)
end

# Bad - multi-line with {}
users.each { |user|
  send_email(user)
  log_action(user)
}

# Bad - single-line with do...end (debatable)
users.each do |user| puts user.name end
```

**Use &: for simple method calls**
```ruby
# Good
users.map(&:name)
numbers.select(&:even?)

# Bad - unnecessary block
users.map { |u| u.name }
numbers.select { |n| n.even? }
```

### Collections

**Prefer %w for string arrays**
```ruby
# Good
STATES = %w[pending approved rejected]

# Acceptable but less concise
STATES = ["pending", "approved", "rejected"]
```

**Prefer symbols for hash keys**
```ruby
# Good
user = { name: "Alice", email: "alice@example.com" }

# Bad - strings as keys (unless necessary)
user = { "name" => "Alice", "email" => "alice@example.com" }
```

**Modern hash syntax**
```ruby
# Good (Ruby 1.9+)
user = { name: "Alice", age: 30 }

# Old style (avoid unless necessary)
user = { :name => "Alice", :age => 30 }
```

### String Literals

**Prefer double quotes**
```ruby
# Good - Ruby community preference
message = "Hello, world!"
name = "Alice"

# Acceptable but less common
message = 'Hello, world!'

# Good - single quotes when no interpolation needed
regex = '[a-z]+'
```

**Use string interpolation, not concatenation**
```ruby
# Good
greeting = "Hello, #{name}!"

# Bad
greeting = "Hello, " + name + "!"
greeting = "Hello, " << name << "!"
```

### Classes

**Class organization**
```ruby
class User
  # 1. extends and includes
  extend SomeModule
  include AnotherModule

  # 2. Constants
  MAX_NAME_LENGTH = 50

  # 3. Attribute macros
  attr_reader :id, :name
  attr_accessor :email

  # 4. Class methods
  def self.find(id)
    # ...
  end

  # 5. Initialize
  def initialize(name, email)
    @name = name
    @email = email
  end

  # 6. Public instance methods
  def full_profile
    "#{name} <#{email}>"
  end

  # 7. Protected methods
  protected

  def validation_method
    # ...
  end

  # 8. Private methods
  private

  def helper_method
    # ...
  end
end
```

**Self vs @**
```ruby
# Good - use attr_reader and call method
class User
  attr_reader :name

  def greet
    "Hello, #{name}"  # Calls method
  end
end

# Acceptable - direct instance variable access
class User
  def initialize(name)
    @name = name
  end

  def greet
    "Hello, #{@name}"  # Direct access
  end
end

# Prefer self. for setters
class User
  attr_accessor :name

  def update_name(new_name)
    self.name = new_name  # self. required for setter
  end
end
```

### Module Organization

**File per class/module**
```
lib/
  user.rb           # class User
  user_service.rb   # class UserService
  models/
    account.rb      # class Models::Account
```

**Namespace with modules**
```ruby
# lib/models/account.rb
module Models
  class Account
    # ...
  end
end

# Or nested
module Models
  class Account < ApplicationRecord
  end
end
```

### Common Idioms

**Guard clauses**
```ruby
# Good
def process(user)
  return nil unless user.valid?
  return nil unless user.active?

  perform_operation(user)
end

# Bad - nested ifs
def process(user)
  if user.valid?
    if user.active?
      perform_operation(user)
    end
  end
end
```

**Safe navigation operator**
```ruby
# Good
user&.profile&.avatar_url

# Bad
user && user.profile && user.profile.avatar_url
```

**Double pipe assignment**
```ruby
# Good
@cache ||= expensive_operation

# Equivalent to:
@cache = @cache || expensive_operation

# Not the same as:
@cache = @cache ? @cache : expensive_operation
# (||= doesn't assign if @cache is false)
```

### Comments

**Avoid obvious comments**
```ruby
# Bad - comment just repeats code
# Get the user name
name = user.name

# Good - explains WHY
# Use display name for external users, full name for staff
name = user.external? ? user.display_name : user.full_name
```

**Use TODO comments**
```ruby
# TODO: Handle edge case where user has no email
# FIXME: This breaks when count > 1000
# OPTIMIZE: Consider caching this result
# HACK: Temporary workaround for API bug
```

### Documentation

**Use RDoc/YARD for method documentation**
```ruby
# Document public API methods
##
# Processes a payment for the given order.
#
# @param order [Order] the order to process
# @param amount [Float] the payment amount
# @return [Payment] the processed payment
# @raise [PaymentError] if payment fails
#
def process_payment(order, amount)
  # ...
end
```

### Testing

**Descriptive test names**
```ruby
# Good
def test_user_is_invalid_without_email
  user = User.new(name: "Alice")
  refute user.valid?
end

# RSpec style
it "is invalid without an email" do
  user = User.new(name: "Alice")
  expect(user).not_to be_valid
end
```

### Common Patterns to Avoid

**Don't use for loops**
```ruby
# Bad
for i in 0...array.length
  puts array[i]
end

# Good
array.each { |item| puts item }
array.each_with_index { |item, i| puts "#{i}: #{item}" }
```

**Don't modify parameters**
```ruby
# Bad
def add_item(items, item)
  items << item  # Modifies caller's array
end

# Good
def add_item(items, item)
  items + [item]  # Returns new array
end
```

**Don't use catch/throw for control flow**
```ruby
# Bad
catch :done do
  users.each do |user|
    throw :done if user.admin?
  end
end

# Good
admin = users.find(&:admin?)
```

### Style Enforcement

**RuboCop**

Use RuboCop to automatically check and fix style issues:

```bash
gem install rubocop

# Check code
rubocop

# Auto-fix issues
rubocop --auto-correct

# Or in Gemfile
gem 'rubocop', require: false
```

**.rubocop.yml configuration:**
```yaml
AllCops:
  TargetRubyVersion: 3.2
  NewCops: enable

Style/StringLiterals:
  EnforcedStyle: double_quotes

Style/Documentation:
  Enabled: false

Metrics/LineLength:
  Max: 120
```

---

## B. Common Gotchas for Python Developers

### Variables and Scope

**No tuple unpacking assignment (use parallel assignment)**
```ruby
# Python
x, y = 10, 20

# Ruby - similar syntax
x, y = 10, 20  # Works!

# But not with multiple targets like Python
# a, b = c, d = 10, 20  # Doesn't work the same
```

**No nonlocal keyword**
```ruby
# Python
def outer():
    x = 10
    def inner():
        nonlocal x
        x = 20
    inner()
    print(x)  # 20

# Ruby - instance variables work differently
def outer
  x = 10
  inner = -> { x = 20 }  # This creates a new local x!
  inner.call
  puts x  # 10 (unchanged)
end

# Solution: use instance variables or return values
def outer
  x = 10
  inner = -> { x = 20; x }  # Return new value
  x = inner.call
  puts x  # 20
end
```

### Strings

**String interpolation only in double quotes**
```ruby
# This doesn't work
name = 'Alice'
puts 'Hello, #{name}!'  # Prints: Hello, #{name}!

# Must use double quotes
puts "Hello, #{name}!"  # Prints: Hello, Alice!
```

**Strings are mutable**
```ruby
str = "hello"
str << " world"
puts str  # "hello world"

# Python strings are immutable
# str += " world" creates new string
```

### Truthiness

**Only false and nil are falsy**
```ruby
# These are truthy in Ruby, falsy in Python!
if 0
  puts "0 is truthy"  # Prints!
end

if ""
  puts "Empty string is truthy"  # Prints!
end

if []
  puts "Empty array is truthy"  # Prints!
end

if {}
  puts "Empty hash is truthy"  # Prints!
end

# Only these are falsy
if false
  puts "Won't print"
end

if nil
  puts "Won't print"
end
```

### Operators

**and/or vs &&/||**
```ruby
# and/or have very low precedence
x = true or false
puts x  # false (because of precedence!)
# Interpreted as: (x = true) or false

# &&/|| work as expected
x = true || false
puts x  # true

# Always use &&/|| for boolean logic
# Use and/or only for control flow
do_something and return  # Return if do_something is truthy
```

**No == for object comparison**
```ruby
# Python
class Point:
    def __eq__(self, other):
        return self.x == other.x and self.y == other.y

# Ruby - define == but also consider eql? and equal?
class Point
  attr_reader :x, :y

  def ==(other)
    other.is_a?(Point) && x == other.x && y == other.y
  end

  # eql? for hash equality (used by Hash)
  def eql?(other)
    self == other
  end

  # equal? checks object identity (don't override)
end
```

### Control Flow

**elsif not elif**
```ruby
# Wrong
if x > 10
  puts "big"
elif x > 5  # SyntaxError!
  puts "medium"
end

# Correct
if x > 10
  puts "big"
elsif x > 5  # No second 'e'
  puts "medium"
end
```

**No switch statement (use case/when)**
```ruby
# Python
match value:
    case 1:
        print("one")
    case 2:
        print("two")

# Ruby
case value
when 1
  puts "one"
when 2
  puts "two"
end

# No fall-through in Ruby (each when is independent)
```

### Collections

**Ranges are inclusive with .., exclusive with ...**
```ruby
# Python
range(1, 5)  # [1, 2, 3, 4]

# Ruby
(1..5)   # 1, 2, 3, 4, 5 (inclusive)
(1...5)  # 1, 2, 3, 4 (exclusive)

# Common mistake
5.times { |i| puts i }  # 0, 1, 2, 3, 4 (like range(5))
(1..5).each { |i| puts i }  # 1, 2, 3, 4, 5
```

**Negative indices work differently**
```ruby
array = [1, 2, 3, 4, 5]

# Python
# array[-1:-3]  # [] (empty)

# Ruby
array[-1..-3]  # [] (empty)
array[-3..-1]  # [3, 4, 5] (reverse the range!)
```

**Hash keys can be any object**
```ruby
# Python requires hashable keys
# Ruby allows any object (but should implement hash and eql?)

hash = { [1, 2] => "array key" }  # Discouraged but works
hash[[1, 2]]  # "array key"

# Prefer symbols or strings as keys
```

### Methods

**Parentheses are optional**
```ruby
# Can be confusing
puts 5 + 3  # Prints 8
puts(5 + 3)  # Prints 8
puts 5 + 3 * 2  # Prints 11

# But this is different
puts(5) + 3  # Returns nil + 3 -> NoMethodError!
```

**Return is implicit**
```ruby
def add(a, b)
  a + b  # Implicit return
end

# Common mistake - returning wrong value
def process(items)
  items.each do |item|
    item.process  # Returns items, not last processed item!
  end
end

# Fix
def process(items)
  items.map do |item|
    item.process  # map returns array of results
  end
end
```

**Can't have default and non-default parameters mixed the same way**
```ruby
# Python
def func(a, b=10, c):  # Error in Python 3

# Ruby - same issue
def func(a, b=10, c)  # Works but confusing
  # c would need to be provided
end

# Better
def func(a, c, b: 10)  # Use keyword arguments
  # ...
end
```

### Classes

**No __init__, use initialize**
```ruby
# Wrong
class User
  def __init__(name)
    @name = name
  end
end

# Correct
class User
  def initialize(name)
    @name = name
  end
end
```

**Instance variables are private by default**
```ruby
class User
  def initialize(name)
    @name = name
  end
end

user = User.new("Alice")
# user.@name  # SyntaxError!
# user.name   # NoMethodError!

# Must use attr_reader
class User
  attr_reader :name

  def initialize(name)
    @name = name
  end
end

user = User.new("Alice")
user.name  # "Alice"
```

**No multiple inheritance**
```ruby
# Python
class Child(Parent1, Parent2):
    pass

# Ruby - use mixins instead
module Loggable
  def log(message)
    puts message
  end
end

class Child < Parent
  include Loggable
end
```

### Modules and Imports

**No from...import**
```ruby
# Python
from module import function

# Ruby - require gives you everything
require 'json'

# Must use module prefix or include
JSON.parse(data)

# Or include (only in classes/modules)
class Parser
  include JSON
  # Now can use parse directly (not recommended for standard library)
end
```

**Require doesn't reload**
```ruby
# Python
import module
# Reimport doesn't reload

# Ruby
require 'myfile'  # Loads
require 'myfile'  # Doesn't load again

# Use load for reloading (mainly for development)
load 'myfile.rb'  # Always reloads
```

### Blocks and Procs

**Blocks aren't first-class without conversion**
```ruby
# Can't pass block to variable directly
# block = { |x| x * 2 }  # SyntaxError!

# Must use Proc or lambda
multiplier = ->(x) { x * 2 }
# Or
multiplier = Proc.new { |x| x * 2 }
```

**Return in blocks behaves differently**
```ruby
def process
  [1, 2, 3].each do |n|
    return n if n == 2  # Returns from process method!
  end
  "done"
end

puts process  # 2 (not "done")

# Use next for early block exit
def process
  result = []
  [1, 2, 3].each do |n|
    next if n == 2  # Skip this iteration
    result << n
  end
  result
end

puts process.inspect  # [1, 3]
```

### Symbols

**Symbols are not strings**
```ruby
:name == "name"  # false

# Common mistake
hash = { name: "Alice" }
hash["name"]  # nil (key is :name, not "name")
hash[:name]   # "Alice"

# Convert between them
:name.to_s  # "name"
"name".to_sym  # :name
```

### Regex

**Match returns MatchData, not boolean**
```ruby
# Python
if re.search(pattern, string):
    print("matched")

# Ruby
if /pattern/.match(string)  # Returns MatchData or nil
  puts "matched"
end

# Or use =~ for position
if string =~ /pattern/  # Returns position or nil
  puts "matched"
end
```

### Exception Handling

**begin/rescue/end not try/except**
```ruby
# Wrong
try
  risky_operation
except => e
  puts e
end

# Correct
begin
  risky_operation
rescue => e
  puts e
end

# Rescue StandardError by default, not Exception
begin
  risky_operation
rescue  # Rescues StandardError
  puts "Error"
end
```

---

## C. Useful Resources and Further Learning

### Official Documentation

**Ruby Documentation**
- [Ruby Core Documentation](https://ruby-doc.org/core/) - Standard library reference
- [Ruby Standard Library](https://ruby-doc.org/stdlib/) - Additional modules
- [Ruby Language Documentation](https://www.ruby-lang.org/en/documentation/) - Official guides

**API References**
- [RubyDoc.info](https://www.rubydoc.info/) - Documentation for gems
- [DevDocs](https://devdocs.io/ruby/) - Offline-capable documentation

### Learning Resources

**Books**
- *The Well-Grounded Rubyist* by David A. Black - Comprehensive Ruby guide
- *Eloquent Ruby* by Russ Olsen - Writing idiomatic Ruby
- *Practical Object-Oriented Design in Ruby* (POODR) by Sandi Metz - OOP best practices
- *Metaprogramming Ruby 2* by Paolo Perrotta - Advanced Ruby techniques
- *99 Bottles of OOP* by Sandi Metz - Object-oriented design

**Online Courses**
- [Ruby Koans](http://rubykoans.com/) - Learn Ruby through testing
- [Exercism Ruby Track](https://exercism.org/tracks/ruby) - Practice with mentorship
- [Learn Ruby the Hard Way](https://learnrubythehardway.org/) - Beginner-friendly
- [Ruby on Rails Tutorial](https://www.railstutorial.org/) - If moving to Rails

**Interactive**
- [TryRuby](https://try.ruby-lang.org/) - Browser-based Ruby tutorial
- [Ruby Warrior](https://www.bloc.io/ruby-warrior) - Learn through game

### Community

**Forums and Q&A**
- [Ruby on Reddit](https://www.reddit.com/r/ruby/) - Active community
- [Stack Overflow Ruby Tag](https://stackoverflow.com/questions/tagged/ruby)
- [Ruby Forum](https://www.ruby-forum.com/)

**Chat**
- [Ruby on Discord](https://discord.gg/ruby)
- [Ruby on Slack](https://www.ruby-lang.org/en/community/)

**Blogs and Newsletters**
- [Ruby Weekly](https://rubyweekly.com/) - Weekly newsletter
- [Awesome Ruby](https://awesome-ruby.com/) - Curated list of resources
- [RubyFlow](http://www.rubyflow.com/) - Community link blog
- [Ruby Rogues Podcast](https://devchat.tv/podcasts/ruby-rogues/)

### Style and Best Practices

**Style Guides**
- [Ruby Style Guide](https://rubystyle.guide/) - Community-driven style guide
- [Shopify Ruby Style Guide](https://ruby-style-guide.shopify.dev/)
- [GitHub Ruby Style Guide](https://github.com/github/rubocop-github)

**Tools**
- [RuboCop](https://rubocop.org/) - Linter and formatter
- [Reek](https://github.com/troessner/reek) - Code smell detector
- [RubyCritic](https://github.com/whitesmith/rubycritic) - Code quality tool

### Gems and Libraries

**Finding Gems**
- [RubyGems.org](https://rubygems.org/) - Official gem repository
- [Ruby Toolbox](https://www.ruby-toolbox.com/) - Categorized gem directory
- [Awesome Ruby](https://github.com/markets/awesome-ruby) - Curated gem list

**Essential Gems**
- [Bundler](https://bundler.io/) - Dependency management
- [RSpec](https://rspec.info/) - Testing framework
- [Pry](https://pry.github.io/) - Enhanced REPL
- [Rake](https://ruby.github.io/rake/) - Task automation

### Ruby Implementations

**Alternative Implementations**
- [MRI (Matz's Ruby Interpreter)](https://www.ruby-lang.org/) - Official implementation
- [JRuby](https://www.jruby.org/) - Ruby on JVM
- [TruffleRuby](https://github.com/oracle/truffleruby) - High-performance Ruby
- [mruby](https://mruby.org/) - Lightweight embeddable Ruby

### Rails and Web Development

**If moving to Rails:**
- [Ruby on Rails Guides](https://guides.rubyonrails.org/) - Official Rails documentation
- [Rails Tutorial](https://www.railstutorial.org/) - Comprehensive Rails guide
- [GoRails](https://gorails.com/) - Rails screencasts

**Alternative Web Frameworks:**
- [Sinatra](http://sinatrarb.com/) - Minimal web framework
- [Hanami](https://hanamirb.org/) - Full-featured modern framework
- [Roda](https://roda.jeremyevans.net/) - Routing tree framework

### Testing

**Testing Frameworks**
- [RSpec](https://rspec.info/) - BDD testing
- [Minitest](https://github.com/minitest/minitest) - Built-in testing
- [Capybara](https://github.com/teamcapybara/capybara) - Integration testing
- [FactoryBot](https://github.com/thoughtbot/factory_bot) - Test data

### Performance

**Profiling and Optimization**
- [Benchmark](https://ruby-doc.org/stdlib/libdoc/benchmark/rdoc/Benchmark.html) - Built-in benchmarking
- [rack-mini-profiler](https://github.com/MiniProfiler/rack-mini-profiler) - Web profiler
- [memory_profiler](https://github.com/SamSaffron/memory_profiler) - Memory profiling
- [stackprof](https://github.com/tmm1/stackprof) - Sampling call-stack profiler

### Reference Materials

**Cheat Sheets**
- [Ruby Cheat Sheet](https://www.codecademy.com/learn/learn-ruby/modules/learn-ruby-introduction-to-ruby-u/cheatsheet)
- [Rails Cheat Sheet](https://gist.github.com/mdang/95b4f54cadf12e7e0415)

**Version Changes**
- [Ruby Changes](https://rubyreferences.github.io/rubychanges/) - What's new in each version
- [Ruby News](https://www.ruby-lang.org/en/news/) - Official announcements

### Staying Current

**Follow**
- [@ruby_lang](https://twitter.com/ruby_lang) - Official Ruby Twitter
- [Ruby Blog](https://www.ruby-lang.org/en/news/) - Official updates
- [This Week in Rails](https://world.hey.com/this.week.in.rails) - Rails news

**Conferences**
- RubyConf - Annual Ruby conference
- RailsConf - Rails-focused conference
- Regional Ruby conferences worldwide

---

## D. Ruby Version Management (rbenv, RVM)

Managing Ruby versions is similar to managing Python versions with pyenv. The two most popular tools are rbenv and RVM.

### Why Version Management?

**Similar to pyenv for Python:**
- Different projects need different Ruby versions
- System Ruby often outdated
- Avoid permission issues with system gems
- Test code across Ruby versions

### rbenv (Recommended)

rbenv is lightweight and simple, similar to pyenv for Python.

**Installation**

**macOS (with Homebrew):**
```bash
brew install rbenv ruby-build

# Add to shell profile (~/.zshrc or ~/.bash_profile)
echo 'eval "$(rbenv init -)"' >> ~/.zshrc
source ~/.zshrc
```

**Linux:**
```bash
# Clone rbenv
git clone https://github.com/rbenv/rbenv.git ~/.rbenv

# Add to PATH
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc

# Install ruby-build
git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```

**Basic Usage**

```bash
# List available Ruby versions
rbenv install --list

# Install a Ruby version
rbenv install 3.2.0

# List installed versions
rbenv versions

# Set global Ruby version
rbenv global 3.2.0

# Set local version (per-project)
rbenv local 3.1.0

# Show current version
rbenv version

# Rehash after installing gems with executables
rbenv rehash
```

**Per-Project Ruby Version**

```bash
cd myproject
rbenv local 3.2.0

# Creates .ruby-version file
cat .ruby-version
# 3.2.0

# Version automatically switches when entering directory
```

**Comparison to pyenv:**

```bash
# Python (pyenv)
pyenv install 3.11.0
pyenv global 3.11.0
pyenv local 3.10.0

# Ruby (rbenv)
rbenv install 3.2.0
rbenv global 3.2.0
rbenv local 3.1.0
```

### RVM (Alternative)

RVM is more feature-rich but also more complex. It includes gemset management.

**Installation**

```bash
# Install RVM
\curl -sSL https://get.rvm.io | bash -s stable

# Load RVM
source ~/.rvm/scripts/rvm

# Verify installation
rvm --version
```

**Basic Usage**

```bash
# List available Ruby versions
rvm list known

# Install a Ruby version
rvm install 3.2.0

# List installed versions
rvm list

# Use a specific version
rvm use 3.2.0

# Set default version
rvm use 3.2.0 --default

# Show current version
rvm current
```

**Gemsets (RVM-specific)**

Gemsets are like Python virtual environments but for gems:

```bash
# Create gemset for project
rvm gemset create myproject

# Use Ruby version with specific gemset
rvm use 3.2.0@myproject

# List gemsets
rvm gemset list

# Delete gemset
rvm gemset delete myproject
```

**Per-Project Configuration**

```bash
# Create .ruby-version and .ruby-gemset
echo "3.2.0" > .ruby-version
echo "myproject" > .ruby-gemset

# RVM automatically switches when entering directory
```

### Comparison: rbenv vs RVM

**rbenv:**
- ✅ Lightweight and simple
- ✅ Doesn't override shell commands
- ✅ Easy to understand
- ❌ No built-in gemset management
- ❌ Fewer features

**RVM:**
- ✅ Feature-rich
- ✅ Built-in gemset management
- ✅ More documentation
- ❌ Heavier/more complex
- ❌ Overrides shell commands (cd, etc.)

**Recommendation:** Use rbenv for simplicity. Use Bundler for dependency isolation (gemsets not usually needed).

### Using with Bundler

Bundler provides dependency isolation without gemsets:

```bash
# Install gems to project directory
bundle install --path vendor/bundle

# Or set globally
bundle config set --local path 'vendor/bundle'

# Run commands with project gems
bundle exec ruby script.rb
bundle exec rake test
```

### chruby (Another Alternative)

chruby is even simpler than rbenv:

```bash
# Install (macOS)
brew install chruby ruby-install

# Add to shell
echo 'source /usr/local/share/chruby/chruby.sh' >> ~/.zshrc

# Install Ruby
ruby-install ruby 3.2.0

# Switch versions
chruby 3.2.0

# Auto-switch with .ruby-version
echo 'source /usr/local/share/chruby/auto.sh' >> ~/.zshrc
```

### asdf (Universal Version Manager)

asdf manages multiple languages including Ruby:

```bash
# Install asdf
brew install asdf

# Add Ruby plugin
asdf plugin add ruby

# Install Ruby
asdf install ruby 3.2.0

# Set version
asdf global ruby 3.2.0
asdf local ruby 3.1.0

# Works with .tool-versions file
echo "ruby 3.2.0" >> .tool-versions
```

**Advantage:** One tool for Ruby, Node.js, Python, etc.

### Docker (Isolated Environments)

For complete isolation:

```dockerfile
# Dockerfile
FROM ruby:3.2.0

WORKDIR /app

COPY Gemfile Gemfile.lock ./
RUN bundle install

COPY . .

CMD ["ruby", "app.rb"]
```

```bash
docker build -t myapp .
docker run myapp
```

### Comparison to Python Version Management

| Python | Ruby |
|--------|------|
| pyenv | rbenv |
| virtualenv | Bundler |
| pip | gem |
| requirements.txt | Gemfile |
| N/A | RVM gemsets |
| Poetry | Bundler (combined) |

**Key Difference:** Ruby developers typically use Bundler for dependency isolation rather than separate virtual environments.

### Best Practices

**1. Use a version manager (rbenv or RVM)**
```bash
rbenv install 3.2.0
rbenv global 3.2.0
```

**2. Specify Ruby version in project**
```bash
# .ruby-version
3.2.0
```

**3. Use Bundler for dependencies**
```ruby
# Gemfile
source 'https://rubygems.org'
ruby '3.2.0'

gem 'sinatra'
gem 'pg'
```

**4. Don't use system Ruby for development**
```bash
# Bad
sudo gem install rails

# Good (with rbenv/RVM)
gem install rails
```

**5. Commit .ruby-version**
```bash
git add .ruby-version Gemfile Gemfile.lock
git commit -m "Specify Ruby version and dependencies"
```

### Troubleshooting

**rbenv not picking up gems:**
```bash
rbenv rehash
```

**RVM conflicts with rbenv:**
```bash
# Remove RVM
rvm implode

# Or remove rbenv
rm -rf ~/.rbenv
```

**System Ruby vs managed Ruby:**
```bash
which ruby
# Should show rbenv/rvm path, not /usr/bin/ruby

# If not working, check shell initialization
echo $PATH
```

**Bundler uses wrong Ruby:**
```bash
# Reinstall bundler for current Ruby
gem install bundler
rbenv rehash
```

---

## Summary

This appendix provides:

**A. Ruby Style Guide**
- Naming conventions (snake_case, PascalCase, SCREAMING_SNAKE_CASE)
- Formatting (2 spaces, line length)
- Idiomatic patterns (unless, guard clauses, implicit return)
- RuboCop for enforcement

**B. Common Gotchas for Python Developers**
- elsif not elif
- Only false/nil are falsy
- and/or vs &&/||
- initialize not __init__
- String interpolation requires double quotes
- Instance variables are private by default

**C. Resources and Further Learning**
- Official documentation
- Books and courses
- Community forums
- Essential gems
- Testing tools
- Performance profiling

**D. Ruby Version Management**
- rbenv (recommended for simplicity)
- RVM (feature-rich alternative)
- chruby (minimal option)
- asdf (universal version manager)
- Bundler for dependency isolation

These resources will help you continue your Ruby journey beyond this course. Welcome to the Ruby community!
