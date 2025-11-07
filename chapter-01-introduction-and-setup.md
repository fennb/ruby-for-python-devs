# Chapter 1: Introduction and Setup

## 1.1 Course Overview

Welcome! If you're reading this, you already know Python and want to learn Ruby. This course is designed specifically for you.

Unlike traditional Ruby tutorials that assume no programming experience, this course leverages your Python knowledge to accelerate your learning. We'll use a comparative approach: showing you Python code you already understand, then demonstrating how to accomplish the same thing in Ruby.

**What makes Ruby different from Python?**

While Python and Ruby share many similarities—both are dynamic, interpreted, object-oriented languages with elegant syntax—they have distinct philosophies:

- **Python's philosophy**: "There should be one—and preferably only one—obvious way to do it" (The Zen of Python)
- **Ruby's philosophy**: "Ruby is designed to make programmers happy" with multiple ways to express the same idea

**Key Similarities:**
- Both are dynamically typed, interpreted languages
- Both have clean, readable syntax with significant whitespace (though Ruby is less strict)
- Both support object-oriented, functional, and procedural programming styles
- Both have rich standard libraries and vibrant package ecosystems

**Key Differences:**
- Ruby has blocks and powerful iterator methods that replace many of Python's list comprehensions
- Ruby emphasizes object-oriented programming more heavily (everything is an object, including primitives)
- Ruby has more flexible syntax (parentheses are often optional, multiple ways to write the same code)
- Python uses indentation for blocks; Ruby uses keywords like `end`
- Ruby has symbols (`:symbol`), a concept Python doesn't have

**What you'll learn:**

By the end of this course, you'll be able to:
- Read and understand Ruby code with confidence
- Write idiomatic Ruby that follows community conventions
- Work effectively in Ruby codebases
- Understand Ruby's unique features like blocks, symbols, and mixins
- Know when and why to use Ruby instead of Python

**How to use this course:**

- Read sections in order—concepts build on each other
- Type out the code examples yourself; don't just read them
- Experiment in IRB (Ruby's REPL) as you learn
- Pay special attention to "gotchas" and differences highlighted throughout

Let's get started!

---

## 1.2 Installing Ruby

Before you can write Ruby code, you need to install Ruby on your system. If you've installed Python before, this process will feel familiar.

### Checking if Ruby is Already Installed

Many macOS and Linux systems come with Ruby pre-installed. Open your terminal and check:

```bash
ruby --version
```

If you see output like `ruby 3.2.2`, you have Ruby installed. However, system Ruby versions are often outdated. For development, you'll want to install a newer version and use a version manager.

### Installation Methods by Platform

#### macOS

**Recommended: Using Homebrew (like pip or conda for Python)**

```bash
# Install Homebrew if you don't have it
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Install Ruby
brew install ruby

# Add to your PATH (add this to your ~/.zshrc or ~/.bash_profile)
export PATH="/opt/homebrew/opt/ruby/bin:$PATH"
```

**Alternative: Using rbenv (like pyenv for Python)**

```bash
# Install rbenv
brew install rbenv ruby-build

# Initialize rbenv (add this to ~/.zshrc or ~/.bash_profile)
eval "$(rbenv init -)"

# Install latest Ruby
rbenv install 3.2.2
rbenv global 3.2.2
```

#### Linux (Ubuntu/Debian)

```bash
# Using apt (basic installation)
sudo apt update
sudo apt install ruby-full

# Or using rbenv (recommended for development)
sudo apt install git curl libssl-dev libreadline-dev zlib1g-dev autoconf bison build-essential libyaml-dev libreadline-dev libncurses5-dev libffi-dev libgdbm-dev

curl -fsSL https://github.com/rbenv/rbenv-installer/raw/HEAD/bin/rbenv-installer | bash

# Add to ~/.bashrc
echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.bashrc
echo 'eval "$(rbenv init -)"' >> ~/.bashrc
source ~/.bashrc

# Install Ruby
rbenv install 3.2.2
rbenv global 3.2.2
```

#### Windows

**Using RubyInstaller (like Python's official Windows installer)**

1. Download RubyInstaller from [rubyinstaller.org](https://rubyinstaller.org/)
2. Run the installer (choose version 3.2.x with Devkit)
3. Follow the installation wizard
4. In the final step, run `ridk install` to install MSYS2 and development toolchain

Verify installation:

```bash
ruby --version
```

### Ruby Version Managers: rbenv vs RVM

Just as Python has `pyenv` and `conda` for managing multiple Python versions, Ruby has version managers:

**rbenv** (Recommended, similar to pyenv):
- Lightweight and simple
- Doesn't override shell commands
- Popular in the Ruby community

**RVM (Ruby Version Manager)**:
- More features but heavier
- Modifies your shell environment more aggressively
- Older, but still widely used

For this course, use whichever you prefer. Examples will work with any Ruby 3.x version.

### Verifying Your Installation

After installation, verify everything works:

```bash
ruby --version   # Should show Ruby 3.x.x
gem --version    # Should show RubyGems version (Ruby's package manager, like pip)
irb --version    # Should show IRB version (Ruby's REPL)
```

**Comparison to Python:**

```bash
# Python
python --version
pip --version

# Ruby
ruby --version
gem --version
```

Notice the parallel: `pip` is to Python as `gem` is to Ruby.

---

## 1.3 Interactive Ruby (IRB) vs Python REPL

Just as Python has its interactive interpreter (REPL), Ruby has IRB (Interactive Ruby). Both let you experiment with code and test ideas quickly.

### Starting the REPL

**Python:**
```bash
$ python
Python 3.11.0 (main, Oct 24 2022, 18:26:48)
>>> print("Hello")
Hello
>>> 2 + 2
4
>>> exit()
```

**Ruby:**
```bash
$ irb
irb(main):001:0> puts "Hello"
Hello
=> nil
irb(main):002:0> 2 + 2
=> 4
irb(main):003:0> exit
```

### Key Differences

**Output and Return Values:**

In Python's REPL, you see the result of expressions:

```python
>>> 2 + 2
4
>>> "hello".upper()
'HELLO'
```

In IRB, you see both the output (if any) and the return value (prefixed with `=>`):

```ruby
irb(main):001:0> 2 + 2
=> 4
irb(main):002:0> puts "hello"
hello
=> nil
```

Notice that `puts` (Ruby's `print`) outputs "hello" but **returns** `nil` (Ruby's equivalent of `None`). IRB shows both.

**Prompt Format:**

- Python: Simple `>>>`
- IRB: Shows context like `irb(main):001:0>` (line number and nesting level)

You can customize IRB's prompt or use simpler prompts by adding to `~/.irbrc`:

```ruby
IRB.conf[:PROMPT_MODE] = :SIMPLE
```

### Enhanced IRB Features (Ruby 3.1+)

Modern Ruby includes enhanced IRB features similar to IPython:

**Autocomplete:** Press Tab to autocomplete methods and variables

```ruby
irb(main):001:0> "hello".up<TAB>
# Shows: upcase, upcase!
```

**Multi-line Editing:** IRB supports multi-line method definitions:

```ruby
irb(main):001:1> def greet(name)
irb(main):002:1>   "Hello, #{name}!"
irb(main):003:0> end
=> :greet
```

**Syntax Highlighting:** IRB includes built-in syntax highlighting (like IPython)

### IRB Tips for Python Developers

**Show documentation:**
```ruby
irb(main):001:0> help String#upcase
# Shows method documentation
```

**Last result:**
```ruby
irb(main):001:0> 2 + 2
=> 4
irb(main):002:0> _ * 10  # _ holds last result
=> 40
```

**Load files:**
```ruby
irb(main):001:0> load 'my_script.rb'
```

**Require gems:**
```ruby
irb(main):001:0> require 'json'
=> true
```

### Exiting IRB

Multiple ways, just like Python:

```ruby
exit        # Like Python's exit()
quit        # Also works
Ctrl+D      # EOF signal (works in both Python and Ruby)
```

### Alternative: Pry (Like IPython for Ruby)

If you want a more powerful REPL (similar to IPython), install Pry:

```bash
gem install pry
```

Then use `pry` instead of `irb`:

```bash
$ pry
[1] pry(main)> show-source Array#map
# Shows source code for methods
```

For this course, standard IRB is sufficient, but Pry is popular in the Ruby community for debugging and exploration.

---

## 1.4 Running Ruby Scripts

Just as you run Python scripts with `python script.py`, you run Ruby scripts with `ruby script.rb`.

### Python Script Execution

**Python script (`hello.py`):**
```python
#!/usr/bin/env python3

def greet(name):
    return f"Hello, {name}!"

if __name__ == "__main__":
    print(greet("World"))
```

**Running it:**
```bash
python hello.py
# or make it executable:
chmod +x hello.py
./hello.py
```

### Ruby Script Execution

**Ruby script (`hello.rb`):**
```ruby
#!/usr/bin/env ruby

def greet(name)
  "Hello, #{name}!"
end

if __FILE__ == $PROGRAM_NAME
  puts greet("World")
end
```

**Running it:**
```bash
ruby hello.rb
# or make it executable:
chmod +x hello.rb
./hello.rb
```

### Key Differences

**File Extension:**
- Python: `.py`
- Ruby: `.rb`

**Shebang Line:**
- Python: `#!/usr/bin/env python3`
- Ruby: `#!/usr/bin/env ruby`

**Main Guard:**
- Python: `if __name__ == "__main__":`
- Ruby: `if __FILE__ == $PROGRAM_NAME` or `if __FILE__ == $0`

Both patterns check if the file is being run directly (not imported/required).

### Command-Line Execution

Both Python and Ruby let you execute code directly from the command line:

**Python:**
```bash
python -c "print('Hello')"
```

**Ruby:**
```bash
ruby -e "puts 'Hello'"
```

### Running with Arguments

**Python (`args.py`):**
```python
import sys

if __name__ == "__main__":
    print(f"Script: {sys.argv[0]}")
    print(f"Arguments: {sys.argv[1:]}")
```

```bash
python args.py arg1 arg2
```

**Ruby (`args.rb`):**
```ruby
if __FILE__ == $PROGRAM_NAME
  puts "Script: #{$PROGRAM_NAME}"
  puts "Arguments: #{ARGV.inspect}"
end
```

```bash
ruby args.rb arg1 arg2
```

**Key Difference:** Ruby uses `ARGV` (a constant, in all caps) while Python uses `sys.argv`. In Ruby, `ARGV` doesn't include the script name (index 0).

### Script Organization

Both languages follow similar patterns for organizing scripts:

**Python:**
```python
# imports at top
import os
import sys

# constants
API_KEY = "secret"

# function definitions
def main():
    pass

# execution
if __name__ == "__main__":
    main()
```

**Ruby:**
```ruby
# requires at top
require 'json'
require 'net/http'

# constants (ALL_CAPS)
API_KEY = "secret"

# method definitions
def main
  # code here
end

# execution
if __FILE__ == $PROGRAM_NAME
  main
end
```

The structure is nearly identical. Ruby uses `require` instead of `import`, and constant naming is the same.

---

## 1.5 Hello World

Let's write the traditional first program in both languages and explore the differences.

### Python Hello World

```python
# hello.py
print("Hello, World!")
```

Run it:
```bash
python hello.py
```

Output:
```
Hello, World!
```

### Ruby Hello World

```ruby
# hello.rb
puts "Hello, World!"
```

Run it:
```bash
ruby hello.rb
```

Output:
```
Hello, World!
```

### Key Differences: print vs puts vs p

Python has `print()`, but Ruby has several options:

**puts** (most common, like Python's `print`):
```ruby
puts "Hello"      # Outputs: Hello (with newline)
puts "Hello", "World"  # Outputs: Hello\nWorld
```

**print** (no automatic newline):
```ruby
print "Hello"     # Outputs: Hello (no newline)
print "World"     # Outputs: World (right after Hello)
```

**p** (inspect/debug output):
```ruby
p "Hello"         # Outputs: "Hello" (shows quotes and returns the value)
p [1, 2, 3]       # Outputs: [1, 2, 3]
```

**Comparison:**

| Python | Ruby | Behavior |
|--------|------|----------|
| `print("Hello")` | `puts "Hello"` | Output with newline |
| `print("Hello", end="")` | `print "Hello"` | Output without newline |
| `print(repr(obj))` | `p obj` | Debug representation |

### Parentheses are Optional in Ruby

One of Ruby's most distinctive features: **method calls don't require parentheses**.

**Python (parentheses required):**
```python
print("Hello")
len([1, 2, 3])
```

**Ruby (parentheses optional):**
```ruby
puts "Hello"       # Common style
puts("Hello")      # Also valid

[1, 2, 3].length   # Common style
[1, 2, 3].length() # Also valid
```

**Ruby Style Convention:**
- Omit parentheses for "keyword-like" methods: `puts`, `require`, `attr_accessor`
- Use parentheses for "function-like" methods: `[1, 2, 3].join(", ")`
- Always use parentheses when chaining methods: `"hello".upcase().reverse()`

### A Slightly More Complex Hello World

Let's write a version that takes a name as input:

**Python:**
```python
def greet(name):
    print(f"Hello, {name}!")

if __name__ == "__main__":
    greet("Alice")
    greet("Bob")
```

**Ruby:**
```ruby
def greet(name)
  puts "Hello, #{name}!"
end

if __FILE__ == $PROGRAM_NAME
  greet "Alice"  # Parentheses optional
  greet "Bob"
end
```

### Taking User Input

**Python:**
```python
name = input("What's your name? ")
print(f"Hello, {name}!")
```

**Ruby:**
```ruby
print "What's your name? "
name = gets.chomp
puts "Hello, #{name}!"
```

**Key Differences:**
- Python's `input()` automatically strips the newline; Ruby's `gets` doesn't
- Use `.chomp` in Ruby to remove the trailing newline (like `.strip()` but only for trailing whitespace)

### Complete Interactive Hello World

**Python version:**
```python
def greet(name):
    return f"Hello, {name}!"

def main():
    name = input("What's your name? ")
    greeting = greet(name)
    print(greeting)

if __name__ == "__main__":
    main()
```

**Ruby version:**
```ruby
def greet(name)
  "Hello, #{name}!"
end

def main
  print "What's your name? "
  name = gets.chomp
  greeting = greet(name)
  puts greeting
end

if __FILE__ == $PROGRAM_NAME
  main
end
```

Notice Ruby's **implicit return**: methods return the value of the last expression automatically. The `greet` method doesn't need an explicit `return` statement.

### Running Your First Ruby Program

Create the file `hello.rb` with the interactive version above, then:

```bash
ruby hello.rb
```

You should see:
```
What's your name? Alice
Hello, Alice!
```

Congratulations! You've written and run your first Ruby program. The syntax should feel familiar coming from Python, with just a few differences in keywords and conventions.

### What's Next

Now that you can write and run basic Ruby scripts, we'll dive into Ruby's syntax and data types in Chapter 2, comparing each concept to its Python equivalent.
