# Chapter 16: Testing in Ruby

Testing is a core part of Ruby culture, even more so than in Python. Ruby has two primary testing frameworks: Minitest (built into Ruby) and RSpec (the most popular third-party framework). Python developers will find both familiar - Minitest is similar to unittest, while RSpec shares philosophy with pytest but with different syntax.

---

## 16.1 Minitest vs unittest

Minitest is Ruby's built-in testing framework, similar to Python's unittest. It comes with Ruby and requires no installation. Minitest offers both classic xUnit-style tests (like unittest) and a more modern spec-style syntax.

**Key Similarities:**
- Both are built into the language standard library
- Both use xUnit-style test case classes
- Both have setUp/tearDown (or setup/teardown) methods
- Both support assertions and test discovery
- Both can run tests from the command line

**Key Differences:**
- Minitest is simpler and has less boilerplate than unittest
- Minitest offers both classic and spec-style syntax
- Minitest has better performance (simpler implementation)
- Ruby's testing culture emphasizes test-first development more

### Python Example

```python
# test_calculator.py
import unittest

class Calculator:
    def add(self, a, b):
        return a + b

    def divide(self, a, b):
        if b == 0:
            raise ValueError("Cannot divide by zero")
        return a / b

class TestCalculator(unittest.TestCase):
    def setUp(self):
        """Run before each test"""
        self.calc = Calculator()

    def tearDown(self):
        """Run after each test"""
        self.calc = None

    def test_add(self):
        """Test addition"""
        result = self.calc.add(2, 3)
        self.assertEqual(result, 5)

    def test_add_negative_numbers(self):
        result = self.calc.add(-1, -1)
        self.assertEqual(result, -2)

    def test_divide(self):
        result = self.calc.divide(10, 2)
        self.assertEqual(result, 5)

    def test_divide_by_zero(self):
        with self.assertRaises(ValueError):
            self.calc.divide(10, 0)

if __name__ == '__main__':
    unittest.main()

# Run tests
# $ python -m unittest test_calculator.py
# $ python test_calculator.py
```

### Ruby Equivalent

```ruby
# test_calculator.rb
require 'minitest/autorun'

class Calculator
  def add(a, b)
    a + b
  end

  def divide(a, b)
    raise ArgumentError, "Cannot divide by zero" if b.zero?
    a / b
  end
end

class TestCalculator < Minitest::Test
  def setup
    # Run before each test
    @calc = Calculator.new
  end

  def teardown
    # Run after each test
    @calc = nil
  end

  def test_add
    # Test addition
    result = @calc.add(2, 3)
    assert_equal 5, result
  end

  def test_add_negative_numbers
    result = @calc.add(-1, -1)
    assert_equal(-2, result)
  end

  def test_divide
    result = @calc.divide(10, 2)
    assert_equal 5, result
  end

  def test_divide_by_zero
    assert_raises(ArgumentError) do
      @calc.divide(10, 0)
    end
  end
end

# Run tests
# $ ruby test_calculator.rb
# $ rake test  (if using Rake)
```

### Explanation

Minitest provides a familiar xUnit-style testing interface. Test classes inherit from `Minitest::Test`, and test methods start with `test_`.

**Important points:**

**Test structure:**
- Inherit from `Minitest::Test`
- Test methods must start with `test_`
- Use `setup` and `teardown` for test fixtures (no camelCase in Ruby)
- Require `minitest/autorun` to run tests automatically

**Common assertions:**

```ruby
# Equality
assert_equal expected, actual
refute_equal expected, actual

# Boolean
assert true_value
refute false_value

# Nil checks
assert_nil value
refute_nil value

# Inclusion
assert_includes collection, item
refute_includes collection, item

# Matching (regex)
assert_match /pattern/, string
refute_match /pattern/, string

# Exceptions
assert_raises(ExceptionClass) { code }
assert_silent { code }  # No output to stdout/stderr

# Numeric
assert_in_delta 1.0, 1.001, 0.01  # Within delta
assert_operator 5, :>, 3  # 5 > 3

# Type checking
assert_instance_of String, "hello"
assert_kind_of Numeric, 42

# Object identity
assert_same object1, object2  # Same object reference
refute_same object1, object2
```

**Minitest spec-style syntax:**

Minitest also offers an RSpec-like spec syntax:

```ruby
require 'minitest/autorun'

describe Calculator do
  before do
    @calc = Calculator.new
  end

  describe '#add' do
    it 'adds two positive numbers' do
      _(@calc.add(2, 3)).must_equal 5
    end

    it 'adds two negative numbers' do
      _(@calc.add(-1, -1)).must_equal(-2)
    end
  end

  describe '#divide' do
    it 'divides two numbers' do
      _(@calc.divide(10, 2)).must_equal 5
    end

    it 'raises error when dividing by zero' do
      _ { @calc.divide(10, 0) }.must_raise ArgumentError
    end
  end
end
```

**Running tests:**

```bash
# Run a single test file
ruby test_calculator.rb

# Run all tests in a directory
ruby -I lib -I test test/**/*_test.rb

# Using Rake (common in Ruby projects)
rake test

# Run specific test
ruby test_calculator.rb --name test_add

# Verbose output
ruby test_calculator.rb --verbose

# Run tests matching a pattern
ruby test_calculator.rb --name /divide/
```

**Test file organization:**

```
project/
├── lib/
│   └── calculator.rb
├── test/
│   ├── test_helper.rb      # Common test setup
│   └── test_calculator.rb  # Test file
└── Rakefile               # Task definitions
```

**test_helper.rb (common setup):**

```ruby
require 'minitest/autorun'
require 'minitest/pride'  # Colorful output

# Load all application files
Dir[File.join(__dir__, '..', 'lib', '**', '*.rb')].each { |file| require file }
```

**Watch out:**
- Assertion argument order is `expected, actual` in Minitest (opposite of some Python test frameworks)
- Use `assert_raises` not `assert_raise` (plural)
- Test methods must start with `test_` to be discovered

---

## 16.2 RSpec vs pytest

RSpec is Ruby's most popular testing framework, used extensively in the Rails community. It offers behavior-driven development (BDD) style testing with expressive syntax. While Python's pytest is more minimal, RSpec is more opinionated and feature-rich.

**Key Similarities:**
- Both focus on readable, behavior-driven test descriptions
- Both have less boilerplate than xUnit frameworks
- Both have rich ecosystems of plugins
- Both support fixtures and test helpers
- Both have expressive assertion/expectation syntax

**Key Differences:**
- RSpec has more DSL magic and metaprogramming
- RSpec's `describe`/`it` blocks are more structured than pytest's functions
- RSpec has built-in mocking (pytest needs pytest-mock)
- RSpec's expectations read more like natural language
- RSpec is more opinionated about test structure

### Python Example

```python
# test_user.py
import pytest

class User:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def is_adult(self):
        return self.age >= 18

    def greet(self):
        return f"Hello, I'm {self.name}"

# Fixtures
@pytest.fixture
def user():
    return User("Alice", 25)

@pytest.fixture
def minor():
    return User("Bob", 15)

# Tests
def test_user_creation():
    user = User("Alice", 25)
    assert user.name == "Alice"
    assert user.age == 25

def test_is_adult_for_adult(user):
    assert user.is_adult() is True

def test_is_adult_for_minor(minor):
    assert minor.is_adult() is False

def test_greet(user):
    assert user.greet() == "Hello, I'm Alice"

def test_greet_contains_name(user):
    greeting = user.greet()
    assert "Alice" in greeting
    assert greeting.startswith("Hello")

# Parametrized tests
@pytest.mark.parametrize("name,age,expected", [
    ("Alice", 25, True),
    ("Bob", 15, False),
    ("Charlie", 18, True),
    ("Diana", 17, False),
])
def test_is_adult_various_ages(name, age, expected):
    user = User(name, age)
    assert user.is_adult() == expected

# Run tests
# $ pytest test_user.py
# $ pytest -v  # verbose
# $ pytest -k test_adult  # run tests matching pattern
```

### Ruby Equivalent

```ruby
# spec/user_spec.rb
require 'rspec'

class User
  attr_reader :name, :age

  def initialize(name, age)
    @name = name
    @age = age
  end

  def adult?
    @age >= 18
  end

  def greet
    "Hello, I'm #{@name}"
  end
end

RSpec.describe User do
  # let defines lazy-loaded test data
  let(:user) { User.new("Alice", 25) }
  let(:minor) { User.new("Bob", 15) }

  describe '#initialize' do
    it 'creates a user with name and age' do
      user = User.new("Alice", 25)
      expect(user.name).to eq("Alice")
      expect(user.age).to eq(25)
    end
  end

  describe '#adult?' do
    context 'when user is an adult' do
      it 'returns true' do
        expect(user.adult?).to be true
      end
    end

    context 'when user is a minor' do
      it 'returns false' do
        expect(minor.adult?).to be false
      end
    end
  end

  describe '#greet' do
    it 'returns a greeting with the user name' do
      expect(user.greet).to eq("Hello, I'm Alice")
    end

    it 'includes the user name in greeting' do
      greeting = user.greet
      expect(greeting).to include("Alice")
      expect(greeting).to start_with("Hello")
    end
  end

  # Shared examples (like parametrized tests)
  describe 'adult status for various ages' do
    [
      ["Alice", 25, true],
      ["Bob", 15, false],
      ["Charlie", 18, true],
      ["Diana", 17, false]
    ].each do |name, age, expected|
      it "returns #{expected} for #{name} aged #{age}" do
        user = User.new(name, age)
        expect(user.adult?).to eq(expected)
      end
    end
  end
end

# Run tests
# $ rspec spec/user_spec.rb
# $ rspec --format documentation  # verbose
# $ rspec --example adult  # run tests matching pattern
```

### Explanation

RSpec uses a describe/context/it structure that reads like documentation. It's designed for behavior-driven development (BDD).

**Important RSpec concepts:**

**Test structure:**
```ruby
RSpec.describe ClassName do
  describe '#method_name' do
    context 'when specific condition' do
      it 'does something specific' do
        # test code
      end
    end
  end
end
```

- `describe` - Groups related tests (for a class or method)
- `context` - Describes a specific scenario (optional, alias for `describe`)
- `it` - Individual test case
- Use `#method` for instance methods, `.method` for class methods

**Expectations (assertions):**

```ruby
# Equality
expect(actual).to eq(expected)        # ==
expect(actual).to eql(expected)       # eql?
expect(actual).to equal(expected)     # same object (equal?)
expect(actual).not_to eq(expected)

# Boolean
expect(value).to be true
expect(value).to be false
expect(value).to be_truthy  # anything except false/nil
expect(value).to be_falsy   # false or nil
expect(value).to be_nil

# Comparison
expect(actual).to be > expected
expect(actual).to be >= expected
expect(actual).to be_between(1, 10).inclusive
expect(actual).to be_within(0.01).of(1.0)

# Type checking
expect(value).to be_a(String)
expect(value).to be_an_instance_of(String)
expect(value).to be_kind_of(Numeric)

# Collections
expect(array).to include(item)
expect(array).to contain_exactly(1, 2, 3)  # any order
expect(array).to match_array([1, 2, 3])    # any order
expect(hash).to include(key: value)

# Strings
expect(string).to start_with("Hello")
expect(string).to end_with("world")
expect(string).to match(/pattern/)

# Exceptions
expect { code }.to raise_error(ArgumentError)
expect { code }.to raise_error(ArgumentError, /message/)
expect { code }.not_to raise_error

# Changes
expect { action }.to change { object.attribute }.from(old).to(new)
expect { array << item }.to change { array.size }.by(1)
```

**let and let! (fixtures):**

```ruby
describe User do
  # Lazy-loaded - only created when referenced
  let(:user) { User.new("Alice", 25) }

  # Eager-loaded - created before each test
  let!(:admin) { User.new("Admin", 30) }

  # before blocks for setup
  before(:each) do
    # Run before each test
    @connection = Database.connect
  end

  after(:each) do
    # Run after each test
    @connection.close
  end

  # One-time setup for all tests
  before(:all) do
    @config = load_config
  end

  it 'uses the let variable' do
    expect(user.name).to eq("Alice")
  end
end
```

**Shared examples (DRY tests):**

```ruby
# Define shared examples
RSpec.shared_examples 'a named object' do
  it 'has a name' do
    expect(subject.name).to be_a(String)
    expect(subject.name).not_to be_empty
  end
end

# Use shared examples
describe User do
  subject { User.new("Alice", 25) }
  it_behaves_like 'a named object'
end

describe Company do
  subject { Company.new("Acme Corp") }
  it_behaves_like 'a named object'
end
```

**subject (implicit test target):**

```ruby
describe User do
  subject { User.new("Alice", 25) }

  # Can reference 'subject' in tests
  it { is_expected.to be_adult }  # Same as: expect(subject).to be_adult

  its(:name) { is_expected.to eq("Alice") }  # requires rspec-its gem
end
```

**RSpec configuration:**

```ruby
# spec/spec_helper.rb
RSpec.configure do |config|
  config.expect_with :rspec do |expectations|
    expectations.syntax = :expect  # Use expect() syntax
  end

  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end

  config.filter_run_when_matching :focus
  config.example_status_persistence_file_path = "spec/examples.txt"
end
```

**Project structure:**

```
project/
├── lib/
│   └── user.rb
├── spec/
│   ├── spec_helper.rb
│   └── user_spec.rb
└── .rspec               # RSpec configuration
```

**.rspec file:**
```
--require spec_helper
--format documentation
--color
```

**Install RSpec:**
```bash
gem install rspec

# Or in Gemfile
gem 'rspec', '~> 3.12', group: :test

# Initialize RSpec in project
rspec --init
```

**Watch out:**
- Test files end with `_spec.rb`, not `_test.rb`
- Use `expect().to` syntax, not the older `should` syntax
- Remember to use blocks `{ }` with `raise_error` and `change`
- RSpec tests go in `spec/` directory, not `test/`

---

## 16.3 Test Structure and Assertions

Both Python and Ruby testing frameworks support similar test structures, but with different syntax and conventions. Understanding these patterns helps write clear, maintainable tests.

**Key Similarities:**
- Both support setup/teardown hooks
- Both have helper methods and shared fixtures
- Both support test organization and grouping
- Both have extensive assertion libraries
- Both support test tagging/filtering

**Key Differences:**
- Ruby testing emphasizes describe/context nesting more
- RSpec's `let` is lazy-loaded (pytest fixtures can be too)
- Ruby tests often use more metaprogramming
- RSpec has more built-in matchers than pytest assertions

### Python Example

```python
# test_bank_account.py
import pytest

class BankAccount:
    def __init__(self, balance=0):
        self._balance = balance

    @property
    def balance(self):
        return self._balance

    def deposit(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        self._balance += amount
        return self._balance

    def withdraw(self, amount):
        if amount <= 0:
            raise ValueError("Amount must be positive")
        if amount > self._balance:
            raise ValueError("Insufficient funds")
        self._balance -= amount
        return self._balance

# Fixtures with different scopes
@pytest.fixture
def account():
    """Create a new account for each test"""
    return BankAccount(100)

@pytest.fixture(scope="module")
def bank_name():
    """Shared across all tests in module"""
    return "Test Bank"

class TestBankAccount:
    """Test suite for BankAccount"""

    def test_initial_balance(self):
        account = BankAccount(100)
        assert account.balance == 100

    def test_default_balance(self):
        account = BankAccount()
        assert account.balance == 0

    def test_deposit(self, account):
        new_balance = account.deposit(50)
        assert new_balance == 150
        assert account.balance == 150

    def test_deposit_updates_balance(self, account):
        initial = account.balance
        account.deposit(50)
        assert account.balance == initial + 50

    def test_deposit_negative_amount(self, account):
        with pytest.raises(ValueError, match="must be positive"):
            account.deposit(-50)

    def test_withdraw(self, account):
        new_balance = account.withdraw(30)
        assert new_balance == 70
        assert account.balance == 70

    def test_withdraw_insufficient_funds(self, account):
        with pytest.raises(ValueError, match="Insufficient funds"):
            account.withdraw(200)

    @pytest.mark.slow
    def test_large_transaction(self):
        account = BankAccount(1_000_000)
        account.deposit(1_000_000)
        assert account.balance == 2_000_000

# Nested test organization with classes
class TestDeposit:
    def test_positive_amount(self, account):
        account.deposit(50)
        assert account.balance == 150

    def test_zero_amount(self, account):
        with pytest.raises(ValueError):
            account.deposit(0)

# Run tests
# $ pytest test_bank_account.py
# $ pytest -v
# $ pytest -m slow  # run only slow tests
```

### Ruby Equivalent

```ruby
# spec/bank_account_spec.rb
require 'rspec'

class BankAccount
  attr_reader :balance

  def initialize(balance = 0)
    @balance = balance
  end

  def deposit(amount)
    raise ArgumentError, "Amount must be positive" if amount <= 0
    @balance += amount
  end

  def withdraw(amount)
    raise ArgumentError, "Amount must be positive" if amount <= 0
    raise ArgumentError, "Insufficient funds" if amount > @balance
    @balance -= amount
  end
end

RSpec.describe BankAccount do
  # let is lazy-loaded - only created when referenced
  let(:account) { BankAccount.new(100) }

  # let! is eager-loaded - created before each test
  let!(:bank_name) { "Test Bank" }

  describe '#initialize' do
    it 'sets initial balance' do
      account = BankAccount.new(100)
      expect(account.balance).to eq(100)
    end

    it 'defaults to zero balance' do
      account = BankAccount.new
      expect(account.balance).to eq(0)
    end
  end

  describe '#deposit' do
    context 'with valid amount' do
      it 'increases the balance' do
        account.deposit(50)
        expect(account.balance).to eq(150)
      end

      it 'returns the new balance' do
        new_balance = account.deposit(50)
        expect(new_balance).to eq(150)
      end

      it 'updates balance by the deposited amount' do
        initial = account.balance
        account.deposit(50)
        expect(account.balance).to eq(initial + 50)
      end

      # Alternative: test behavior change
      it 'changes the balance' do
        expect { account.deposit(50) }.to change { account.balance }.by(50)
      end
    end

    context 'with invalid amount' do
      it 'raises error for negative amount' do
        expect { account.deposit(-50) }.to raise_error(
          ArgumentError,
          /must be positive/
        )
      end

      it 'raises error for zero amount' do
        expect { account.deposit(0) }.to raise_error(ArgumentError)
      end
    end
  end

  describe '#withdraw' do
    context 'with sufficient funds' do
      it 'decreases the balance' do
        account.withdraw(30)
        expect(account.balance).to eq(70)
      end

      it 'returns the new balance' do
        new_balance = account.withdraw(30)
        expect(new_balance).to eq(70)
      end

      # Test with change matcher
      it 'reduces balance by withdrawn amount' do
        expect { account.withdraw(30) }.to change { account.balance }.by(-30)
      end
    end

    context 'with insufficient funds' do
      it 'raises error' do
        expect { account.withdraw(200) }.to raise_error(
          ArgumentError,
          /Insufficient funds/
        )
      end

      it 'does not change balance' do
        expect { account.withdraw(200) rescue nil }.not_to change { account.balance }
      end
    end

    context 'with invalid amount' do
      it 'raises error for negative amount' do
        expect { account.withdraw(-30) }.to raise_error(ArgumentError)
      end
    end
  end

  describe 'large transactions', :slow do
    it 'handles large balances' do
      account = BankAccount.new(1_000_000)
      account.deposit(1_000_000)
      expect(account.balance).to eq(2_000_000)
    end
  end
end

# Run tests
# $ rspec spec/bank_account_spec.rb
# $ rspec --format documentation
# $ rspec --tag slow  # run only slow tests
# $ rspec --tag ~slow  # exclude slow tests
```

### Explanation

**Test organization in RSpec:**

RSpec encourages deep nesting to create readable test descriptions:

```ruby
describe ClassName do
  describe '#method_name' do
    context 'when condition A' do
      it 'behaves in specific way' do
        # test
      end
    end

    context 'when condition B' do
      it 'behaves differently' do
        # test
      end
    end
  end
end
```

**Setup and teardown:**

```ruby
describe BankAccount do
  before(:each) do
    # Run before each test (default)
    @account = BankAccount.new(100)
  end

  before(:all) do
    # Run once before all tests in this block
    @bank_config = load_config
  end

  after(:each) do
    # Run after each test
    @account = nil
  end

  after(:all) do
    # Run once after all tests
    cleanup_resources
  end

  around(:each) do |example|
    # Wrap each test
    DB.transaction do
      example.run
      raise ActiveRecord::Rollback  # rollback after test
    end
  end
end
```

**let vs before:**

```ruby
# let is lazy - only created when referenced
let(:account) { BankAccount.new(100) }

# let! is eager - created before each test
let!(:admin) { create_admin_user }

# Prefer let for most cases
describe BankAccount do
  let(:account) { BankAccount.new(100) }

  # 'account' is only created if this test references it
  it 'has initial balance' do
    expect(account.balance).to eq(100)
  end
end

# Use before for side effects
describe BankAccount do
  before do
    clear_database
    seed_test_data
  end
end
```

**Useful RSpec matchers:**

```ruby
# Change matchers
expect { action }.to change { object.value }
expect { action }.to change { object.value }.by(5)
expect { action }.to change { object.value }.from(old).to(new)
expect { action }.to change { object.value }.by_at_least(1)
expect { action }.to change { object.value }.by_at_most(5)

# Compound expectations
expect(user).to have_attributes(name: "Alice", age: 25)
expect(array).to include(1).and include(2)
expect(value).to be_positive.and be < 100

# Collection matchers
expect(array).to contain_exactly(1, 2, 3)  # ignores order
expect(array).to match_array([1, 2, 3])    # ignores order
expect(array).to all(be_positive)           # all elements match
expect(array).to start_with(1, 2)
expect(array).to end_with(9, 10)

# Predicate matchers (method ending in ?)
expect(value).to be_empty    # calls value.empty?
expect(value).to be_present  # calls value.present?
expect(value).to be_valid    # calls value.valid?
```

**Test tags:**

```ruby
# Tag tests
describe BankAccount do
  it 'works fast', :fast do
    # quick test
  end

  it 'runs slowly', :slow, :integration do
    # slow test
  end
end

# Run specific tags
# $ rspec --tag fast
# $ rspec --tag slow --tag integration
# $ rspec --tag ~slow  # exclude slow tests
```

**Custom matchers:**

```ruby
# Define custom matcher
RSpec::Matchers.define :be_adult do
  match do |user|
    user.age >= 18
  end

  failure_message do |user|
    "expected #{user.name} to be adult but was #{user.age} years old"
  end
end

# Use custom matcher
expect(user).to be_adult
```

**Watch out:**
- Don't overuse nesting - too many levels makes tests hard to read
- Use `let` for test data, `before` for setup actions
- Remember `let` is lazy - use `let!` if you need eager evaluation
- Keep individual tests focused - test one thing at a time

---

## 16.4 Mocking and Stubbing

Mocking and stubbing allow you to isolate code under test by replacing dependencies with test doubles. Python uses unittest.mock (or pytest-mock), while Ruby has built-in mocking in RSpec and standalone libraries like rspec-mocks.

**Key Similarities:**
- Both support mocking and stubbing
- Both can verify method calls
- Both can control return values
- Both support partial mocks (spy on real objects)
- Both can raise errors from mocks

**Key Differences:**
- RSpec's mocking is built-in and more integrated
- Ruby uses `allow`/`expect` for stubs/mocks (more explicit)
- RSpec has more expressive syntax for message expectations
- Ruby's `double` is similar to Python's `Mock`

### Python Example

```python
# test_notification_service.py
import pytest
from unittest.mock import Mock, patch, call

class EmailService:
    def send_email(self, to, subject, body):
        # Would actually send email
        print(f"Sending email to {to}")
        return True

class NotificationService:
    def __init__(self, email_service):
        self.email_service = email_service

    def notify_user(self, user, message):
        subject = f"Notification for {user}"
        return self.email_service.send_email(
            user.email,
            subject,
            message
        )

    def notify_multiple(self, users, message):
        results = []
        for user in users:
            result = self.notify_user(user, message)
            results.append(result)
        return results

class User:
    def __init__(self, name, email):
        self.name = name
        self.email = email

# Test with mock
def test_notify_user():
    # Create mock
    mock_email = Mock(spec=EmailService)
    mock_email.send_email.return_value = True

    service = NotificationService(mock_email)
    user = User("Alice", "alice@example.com")

    result = service.notify_user(user, "Hello!")

    # Verify mock was called correctly
    mock_email.send_email.assert_called_once_with(
        "alice@example.com",
        "Notification for Alice",
        "Hello!"
    )
    assert result is True

# Test with multiple calls
def test_notify_multiple():
    mock_email = Mock()
    mock_email.send_email.return_value = True

    service = NotificationService(mock_email)
    users = [
        User("Alice", "alice@example.com"),
        User("Bob", "bob@example.com")
    ]

    results = service.notify_multiple(users, "Hello!")

    # Verify called twice
    assert mock_email.send_email.call_count == 2

    # Verify specific calls
    mock_email.send_email.assert_has_calls([
        call("alice@example.com", "Notification for Alice", "Hello!"),
        call("bob@example.com", "Notification for Bob", "Hello!")
    ])

# Stub method to raise error
def test_handles_email_failure():
    mock_email = Mock()
    mock_email.send_email.side_effect = Exception("Email failed")

    service = NotificationService(mock_email)
    user = User("Alice", "alice@example.com")

    with pytest.raises(Exception, match="Email failed"):
        service.notify_user(user, "Hello!")

# Patch a class
def test_with_patch():
    with patch('__main__.EmailService') as MockEmail:
        mock_instance = MockEmail.return_value
        mock_instance.send_email.return_value = True

        service = NotificationService(mock_instance)
        user = User("Alice", "alice@example.com")

        service.notify_user(user, "Hello!")

        mock_instance.send_email.assert_called_once()
```

### Ruby Equivalent

```ruby
# spec/notification_service_spec.rb
require 'rspec'

class EmailService
  def send_email(to, subject, body)
    # Would actually send email
    puts "Sending email to #{to}"
    true
  end
end

class NotificationService
  def initialize(email_service)
    @email_service = email_service
  end

  def notify_user(user, message)
    subject = "Notification for #{user.name}"
    @email_service.send_email(user.email, subject, message)
  end

  def notify_multiple(users, message)
    users.map { |user| notify_user(user, message) }
  end
end

User = Struct.new(:name, :email)

RSpec.describe NotificationService do
  let(:user) { User.new("Alice", "alice@example.com") }

  describe '#notify_user' do
    it 'sends email with correct parameters' do
      # Create a test double (mock)
      email_service = double('EmailService')

      # Stub the method to return true
      allow(email_service).to receive(:send_email).and_return(true)

      service = NotificationService.new(email_service)
      result = service.notify_user(user, "Hello!")

      # Verify the method was called
      expect(email_service).to have_received(:send_email).with(
        "alice@example.com",
        "Notification for Alice",
        "Hello!"
      )

      expect(result).to be true
    end

    # Alternative: expect before calling (strict mock)
    it 'sends email (with expect)' do
      email_service = double('EmailService')

      # Set expectation before calling
      expect(email_service).to receive(:send_email).with(
        "alice@example.com",
        "Notification for Alice",
        "Hello!"
      ).and_return(true)

      service = NotificationService.new(email_service)
      result = service.notify_user(user, "Hello!")

      expect(result).to be true
    end
  end

  describe '#notify_multiple' do
    it 'sends email to multiple users' do
      email_service = double('EmailService')
      allow(email_service).to receive(:send_email).and_return(true)

      users = [
        User.new("Alice", "alice@example.com"),
        User.new("Bob", "bob@example.com")
      ]

      service = NotificationService.new(email_service)
      results = service.notify_multiple(users, "Hello!")

      # Verify called twice
      expect(email_service).to have_received(:send_email).twice

      # Verify specific calls (order matters)
      expect(email_service).to have_received(:send_email).with(
        "alice@example.com",
        "Notification for Alice",
        "Hello!"
      ).ordered

      expect(email_service).to have_received(:send_email).with(
        "bob@example.com",
        "Notification for Bob",
        "Hello!"
      ).ordered
    end
  end

  describe 'error handling' do
    it 'propagates email service errors' do
      email_service = double('EmailService')

      # Stub to raise an error
      allow(email_service).to receive(:send_email).and_raise(
        StandardError.new("Email failed")
      )

      service = NotificationService.new(email_service)

      expect {
        service.notify_user(user, "Hello!")
      }.to raise_error(StandardError, "Email failed")
    end
  end

  describe 'partial mocking (spy)' do
    it 'spies on real object' do
      # Create real object and spy on it
      email_service = EmailService.new
      allow(email_service).to receive(:send_email).and_return(true)

      service = NotificationService.new(email_service)
      service.notify_user(user, "Hello!")

      expect(email_service).to have_received(:send_email)
    end
  end

  describe 'instance_double for type safety' do
    it 'ensures mock matches real interface' do
      # instance_double verifies methods exist on real class
      email_service = instance_double(EmailService)
      allow(email_service).to receive(:send_email).and_return(true)

      service = NotificationService.new(email_service)
      service.notify_user(user, "Hello!")

      expect(email_service).to have_received(:send_email)
    end
  end
end
```

### Explanation

RSpec provides powerful mocking and stubbing built into the framework. The key concepts are `double`, `allow`, and `expect`.

**Creating test doubles:**

```ruby
# Generic double
double_obj = double('name')

# Double with predefined methods
double_obj = double('EmailService', send_email: true)

# instance_double - verifies method exists on real class
email = instance_double(EmailService)

# class_double - for class methods
klass = class_double(EmailService)
```

**Stubbing (allow):**

```ruby
# Basic stub - method returns nil
allow(object).to receive(:method_name)

# Stub with return value
allow(object).to receive(:method_name).and_return(value)

# Multiple return values
allow(object).to receive(:method_name).and_return(first, second, third)

# Raise error
allow(object).to receive(:method_name).and_raise(StandardError)

# Conditional stub
allow(object).to receive(:method_name).with("arg").and_return(value)

# Any args
allow(object).to receive(:method_name).with(any_args).and_return(value)

# Call original method
allow(object).to receive(:method_name).and_call_original

# Yield to block
allow(object).to receive(:method_name).and_yield(arg1, arg2)
```

**Mocking (expect):**

```ruby
# Expect method to be called
expect(object).to receive(:method_name)

# With specific arguments
expect(object).to receive(:method_name).with(arg1, arg2)

# With return value
expect(object).to receive(:method_name).and_return(value)

# Expect multiple calls
expect(object).to receive(:method_name).twice
expect(object).to receive(:method_name).exactly(3).times
expect(object).to receive(:method_name).at_least(:once)
expect(object).to receive(:method_name).at_most(5).times

# Expect never called
expect(object).not_to receive(:method_name)
```

**Verification (after calling code):**

```ruby
# Allow then verify
allow(object).to receive(:method_name)
# ... code that calls method ...
expect(object).to have_received(:method_name)

# With arguments
expect(object).to have_received(:method_name).with(arg1, arg2)

# Call count
expect(object).to have_received(:method_name).once
expect(object).to have_received(:method_name).twice
expect(object).to have_received(:method_name).exactly(3).times

# Not called
expect(object).not_to have_received(:method_name)
```

**Argument matchers:**

```ruby
# Anything
expect(object).to receive(:method).with(anything)

# Any instance of class
expect(object).to receive(:method).with(instance_of(String))
expect(object).to receive(:method).with(kind_of(Numeric))

# Pattern matching
expect(object).to receive(:method).with(/pattern/)

# Hash including
expect(object).to receive(:method).with(hash_including(key: value))

# Array including
expect(object).to receive(:method).with(array_including(1, 2))

# Duck typing
expect(object).to receive(:method).with(duck_type(:method_name))
```

**Spying on real objects:**

```ruby
# Spy on real object's methods
user = User.new("Alice", "alice@example.com")
allow(user).to receive(:save).and_return(true)

# Original implementation is replaced
user.save  # returns true, doesn't actually save

# Spy while calling original
allow(user).to receive(:save).and_call_original
user.save  # calls real save method
```

**Stubbing class methods:**

```ruby
# Stub class method
allow(User).to receive(:find).and_return(user)

# With class_double (safer)
class_double(User, find: user)
```

**Common patterns:**

```ruby
# Stub dependency in before block
describe NotificationService do
  let(:email_service) { double('EmailService') }
  let(:service) { NotificationService.new(email_service) }

  before do
    allow(email_service).to receive(:send_email).and_return(true)
  end

  it 'sends notification' do
    service.notify_user(user, "Hello!")
    expect(email_service).to have_received(:send_email)
  end
end

# Stub external API
before do
  allow(HTTParty).to receive(:get).and_return(
    double(body: '{"status": "ok"}', code: 200)
  )
end

# Stub time
allow(Time).to receive(:now).and_return(Time.new(2024, 1, 1))

# Stub database queries
allow(User).to receive(:where).and_return([user1, user2])
```

**Watch out:**
- Use `allow` for stubs (setup), `expect` for mocks (verification)
- `instance_double` is safer than `double` (verifies methods exist)
- Don't over-mock - integration tests are also important
- Use `and_call_original` to partially stub methods
- Remember to set up stubs before calling the code under test

---

## Chapter Summary

Ruby has strong testing culture with excellent frameworks:

**Minitest:**
- Built into Ruby (no installation needed)
- Similar to Python's unittest
- Supports both classic (TestCase) and spec-style syntax
- Simple, fast, minimal DSL
- Test methods start with `test_`

**RSpec:**
- Most popular Ruby testing framework
- Similar to pytest in philosophy, but more structured
- Uses describe/context/it for BDD-style tests
- Rich expectation syntax with matchers
- Built-in mocking and stubbing
- Test files end with `_spec.rb` in `spec/` directory

**Key concepts:**
- `let` for lazy-loaded fixtures
- `expect().to` for assertions/expectations
- `double` for test doubles (mocks)
- `allow`/`expect` for stubbing/mocking
- Deep nesting with describe/context for organization

Ruby's testing tools are mature and expressive, making test-driven development (TDD) a natural part of Ruby development.
