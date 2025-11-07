# From `__init__` to `initialize`: A Tale of Two Underscores

## Ruby for Python Developers

Welcome to **Ruby for Python Developers**, a comprehensive course designed to help experienced Python programmers quickly learn Ruby by highlighting the similarities and differences between the two languages.

## Who This Course Is For

This course is designed for:
- Python developers who want to learn Ruby
- Programmers transitioning to Ruby projects or teams
- Developers curious about Ruby's unique features and philosophy
- Anyone who wants to understand both languages better through comparison

## What You'll Learn

Ruby and Python share many similarities - both are dynamic, object-oriented languages with elegant syntax. However, Ruby has its own philosophy and idioms that make it unique. This course will help you:

- Understand Ruby syntax by comparing it to Python equivalents
- Learn Ruby-specific features like blocks, symbols, and mixins
- Write idiomatic Ruby code that follows community conventions
- Navigate the Ruby ecosystem, including gems and testing frameworks
- Appreciate the philosophical differences between the two languages

## Course Contents

### [Chapter 1: Introduction and Setup](chapter-01-introduction-and-setup.md)
Welcome guide for Python developers learning Ruby, covering the course overview, installation, and getting started with Ruby's REPL and scripting.

**Topics covered:**
- Course Overview
- Installing Ruby
- Interactive Ruby (IRB) vs Python REPL
- Running Ruby Scripts
- Hello World

---

### [Chapter 2: Basic Syntax and Data Types](chapter-02-basic-syntax-and-data-types.md)
Core Ruby syntax and fundamental data types, comparing Ruby's approach to Python's conventions.

**Topics covered:**
- Variables and Assignment
- Naming Conventions (snake_case everywhere)
- Numbers (Integer, Float)
- Strings and String Interpolation
- Booleans (`true`/`false` vs `True`/`False`)
- `nil` vs `None`
- Comments (single-line and multi-line)
- Everything is an Object
- Truthiness (only `false` and `nil` are falsy)

---

### [Chapter 3: Control Flow](chapter-03-control-flow.md)
Control flow in Ruby including conditionals, boolean operators, and Ruby's unique `unless` keyword.

**Topics covered:**
- `if`/`elsif`/`else` vs `if`/`elif`/`else`
- Statement Modifiers (Postfix `if`/`unless`)
- `unless` (Ruby-specific)
- Ternary Operator
- `case`/`when` vs `match`/`case` (or `if`/`elif`)
- Boolean Operators (`&&`/`||` vs `and`/`or`)

---

### [Chapter 4: Loops and Iteration](chapter-04-loops-and-iteration.md)
Ruby's iteration patterns, from traditional loops to idiomatic iterator methods like `.each`, `.times`, and `.map`.

**Topics covered:**
- `while` and `until` Loops
- `for` Loops
- Loop Control (`break`, `next`, `redo`)
- The `.times` Iterator
- `.each` vs `for...in`
- Ranges (`1..10` vs `range(1, 11)`)
- `.upto`, `.downto`, and `.step`

---

### [Chapter 5: Collections](chapter-05-collections.md)
Ruby's fundamental collection types: Arrays, Hashes, and Symbols - a uniquely Ruby concept with no direct Python equivalent.

**Topics covered:**
- Arrays vs Python Lists
- Hashes vs Python Dictionaries
- Symbols (Ruby-specific concept)
- Sets

---

### [Chapter 6: Enumerables and Functional Programming](chapter-06-enumerables-and-functional-programming.md)
Ruby's powerful Enumerable module providing functional programming patterns for traversal, searching, and sorting collections.

**Topics covered:**
- The Enumerable Module
- `.map` vs List Comprehensions
- `.select` and `.reject` (Filtering)
- `.reduce` (`.inject`) vs `reduce()`
- `.each_with_index` vs `enumerate()`
- `.zip`
- Method Chaining
- Lazy Enumerables

---

### [Chapter 7: Methods](chapter-07-methods.md)
Defining and using methods in Ruby, including Ruby's implicit returns and special method naming conventions.

**Topics covered:**
- Defining Methods (`def`...`end`)
- Implicit Returns vs Explicit `return`
- Method Arguments
- Method Naming Conventions (`?`, `!`, `=`)
- Parentheses are Optional

---

### [Chapter 8: Blocks, Procs, and Lambdas](chapter-08-blocks-procs-and-lambdas.md)
One of Ruby's most distinctive features - blocks, procs, and lambdas for passing chunks of code to methods.

**Topics covered:**
- Understanding Blocks
- Blocks with `do...end` and `{...}`
- `yield` and Block Parameters
- Procs vs Python Functions
- Lambdas vs Python Lambdas
- Procs vs Lambdas
- Using Blocks as Arguments (`&block`)
- The `&:symbol` Shorthand

---

### [Chapter 9: Classes and Object-Oriented Programming](chapter-09-classes-and-object-oriented-programming.md)
Ruby's consistent object-oriented approach where everything is an object, including classes and primitives.

**Topics covered:**
- Defining Classes
- `initialize` vs `__init__`
- Instance Variables (`@variable`)
- Instance Methods
- `attr_reader`, `attr_writer`, `attr_accessor`
- Class Variables (`@@variable`) vs Class Attributes
- Class Methods (`self.method_name` vs `@classmethod`)
- Access Control (`private`, `protected`, `public`)
- `self` in Ruby
- Inheritance
- `super` Keyword
- Method Overriding

---

### [Chapter 10: Modules and Mixins](chapter-10-modules-and-mixins.md)
Ruby modules for namespacing and mixins - an alternative to Python's multiple inheritance for sharing functionality.

**Topics covered:**
- What are Modules?
- Modules as Namespaces
- Modules as Mixins (`include` vs `extend`)
- Mixins vs Python Multiple Inheritance
- Common Built-in Modules (Enumerable, Comparable)
- `prepend`

---

### [Chapter 11: String Handling](chapter-11-string-handling.md)
Ruby string manipulation, interpolation, heredocs, and regular expressions with comparisons to Python string handling.

**Topics covered:**
- String Literals (single vs double quotes)
- String Interpolation (`#{}`) vs f-strings
- Multiline Strings (heredocs)
- Common String Methods
- String Encoding
- Regular Expressions

---

### [Chapter 12: File I/O](chapter-12-file-io.md)
Reading and writing files in Ruby with convenient shortcuts and block-based auto-closing patterns.

**Topics covered:**
- Reading Files
- Writing Files
- File.open with Blocks (auto-closing)
- File Modes
- Working with Paths
- Directory Operations

---

### [Chapter 13: Error Handling](chapter-13-error-handling.md)
Exception handling with Ruby's `begin`/`rescue`/`ensure` syntax, functionally equivalent to Python's try/except/finally.

**Topics covered:**
- `begin`/`rescue`/`ensure` vs `try`/`except`/`finally`
- Raising Exceptions
- Custom Exception Classes
- Rescue Modifiers
- `retry`
- Exception Hierarchy

---

### [Chapter 14: Ruby Idioms and Best Practices](chapter-14-ruby-idioms-and-best-practices.md)
Writing idiomatic Ruby code that follows community conventions and leverages the language's expressive features.

**Topics covered:**
- The Ruby Way (Idiomatic Ruby)
- Guard Clauses
- Safe Navigation Operator (`&.`)
- Double Pipe Assignment (`||=`)
- Parallel Assignment
- String and Array Destructuring
- Using `_` for Unused Variables
- `then` (method chaining)

---

### [Chapter 15: Package Management and Ecosystem](chapter-15-package-management-and-ecosystem.md)
RubyGems package system and Bundler dependency management, comparing to Python's pip and requirements.txt.

**Topics covered:**
- Gems vs pip Packages
- `gem` Command
- Bundler vs pip/requirements.txt
- Gemfile and Gemfile.lock
- Popular Ruby Gems
- RubyGems.org

---

### [Chapter 16: Testing in Ruby](chapter-16-testing-in-ruby.md)
Ruby's testing frameworks: Minitest (built-in, similar to unittest) and RSpec (popular third-party, similar to pytest).

**Topics covered:**
- Minitest vs unittest
- RSpec vs pytest
- Test Structure and Assertions
- Mocking and Stubbing

---

### [Chapter 17: Advanced Topics](chapter-17-advanced-topics.md)
Advanced Ruby features including metaprogramming, refinements, pattern matching, and concurrency - going beyond the basics.

**Topics covered:**
- Metaprogramming Basics
- Refinements (Scoped Monkey Patching)
- Structs and OpenStructs
- Constants and Freezing Objects
- Concurrency
- Pattern Matching (Ruby 3.0+)
- Functional Programming with Ruby

---

### [Appendix](appendix.md)
Reference materials including Ruby style guide, common gotchas for Python developers, resources, and version management.

**Topics covered:**
- Ruby Style Guide
- Common Gotchas for Python Developers
- Useful Resources and Further Learning
- Ruby Version Management (rbenv, RVM)

---

## How to Use This Course

1. **Start from the beginning** if you're new to Ruby, or jump to specific chapters that interest you
2. **Try the code examples** - Ruby is best learned by doing
3. **Compare with Python** - each chapter highlights the differences and similarities
4. **Practice the Ruby way** - focus on learning idiomatic Ruby patterns
5. **Explore the ecosystem** - try out gems and tools mentioned in the course

## Philosophy: The Ruby Way

Ruby was designed with the principle of **"optimizing for programmer happiness"**. Creator Yukihiro Matsumoto (Matz) wanted a language that was natural to write and read. As you learn Ruby, you'll discover:

- **Multiple ways to do things** - Ruby values expressiveness and gives you choices
- **Blocks everywhere** - a powerful feature that makes code elegant and concise
- **Readability** - Ruby code often reads like English
- **Convention over configuration** - sensible defaults and community standards

Ready to start your Ruby journey? Head to [Chapter 1: Introduction and Setup](chapter-01-introduction-and-setup.md) to begin!
