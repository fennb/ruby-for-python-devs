# Chapter 12: File I/O

## 12.1 Reading Files

File reading in Ruby is similar to Python but with some convenient shortcuts. Ruby provides multiple ways to read files, from reading the entire contents at once to processing line by line.

**Key Similarities:**
- Both use file handles/objects
- Both support reading entire files or line by line
- Both automatically handle newline characters

**Key Differences:**
- Ruby has `File.read` for simple one-line file reading
- Ruby's block syntax with `File.open` auto-closes files
- Different method names (`read` vs `read()`, `readlines` vs `readlines()`)
- Ruby has more built-in convenience methods

### Python Example

```python
# Read entire file as string
with open('data.txt', 'r') as f:
    content = f.read()
    print(content)

# Read entire file as string (alternative)
content = open('data.txt').read()  # Not recommended - doesn't close file

# Read all lines into array
with open('data.txt', 'r') as f:
    lines = f.readlines()
    for line in lines:
        print(line.strip())

# Read line by line (memory efficient)
with open('data.txt', 'r') as f:
    for line in f:
        print(line.strip())

# Read first N characters
with open('data.txt', 'r') as f:
    first_100 = f.read(100)

# Read one line at a time
with open('data.txt', 'r') as f:
    line1 = f.readline()
    line2 = f.readline()

# Check if file exists
import os
if os.path.exists('data.txt'):
    content = open('data.txt').read()
```

### Ruby Equivalent

```ruby
# Read entire file as string (simplest way)
content = File.read('data.txt')
puts content

# Read all lines into array
lines = File.readlines('data.txt')
lines.each do |line|
  puts line.chomp  # chomp removes trailing newline
end

# Read line by line with block (auto-closes file)
File.foreach('data.txt') do |line|
  puts line.chomp
end

# Read with File.open and block (auto-closes)
File.open('data.txt', 'r') do |f|
  content = f.read
  puts content
end

# Read first N characters
File.open('data.txt', 'r') do |f|
  first_100 = f.read(100)
  puts first_100
end

# Read one line at a time
File.open('data.txt', 'r') do |f|
  line1 = f.gets
  line2 = f.gets
end

# Check if file exists
if File.exist?('data.txt')
  content = File.read('data.txt')
end

# Alternative: File.exists? (deprecated but still works)
if File.file?('data.txt')  # Also checks it's a file, not directory
  content = File.read('data.txt')
end
```

### Explanation

Ruby provides several convenient methods for file reading, with `File.read` being the simplest for reading entire files.

**Simple file reading:**

```ruby
# Read entire file - simplest way
content = File.read('data.txt')

# Specify encoding
content = File.read('data.txt', encoding: 'UTF-8')

# Read binary file
binary_data = File.read('image.png', mode: 'rb')

# Read with external and internal encoding
content = File.read('data.txt', external_encoding: 'UTF-8', internal_encoding: 'UTF-8')
```

**Reading lines:**

```ruby
# All lines as array (includes newlines)
lines = File.readlines('data.txt')
lines.each { |line| puts line }

# Remove newlines with chomp
lines = File.readlines('data.txt', chomp: true)

# Process line by line (memory efficient for large files)
File.foreach('data.txt') do |line|
  puts line.chomp
end

# With line number
File.foreach('data.txt').with_index do |line, index|
  puts "#{index + 1}: #{line.chomp}"
end
```

**Using File.open with blocks:**

```ruby
# Block form auto-closes file
File.open('data.txt', 'r') do |f|
  # File is open here
  content = f.read
  puts content
end
# File is automatically closed here, even if exception occurs

# Without block (manual close required)
f = File.open('data.txt', 'r')
content = f.read
f.close  # Must remember to close!

# Better: use ensure
f = File.open('data.txt', 'r')
begin
  content = f.read
ensure
  f.close if f
end

# But just use blocks - they're cleaner!
```

**File reading methods:**

```ruby
File.open('data.txt', 'r') do |f|
  # Read entire file
  all = f.read

  # Read N bytes
  chunk = f.read(100)

  # Read one line
  line = f.gets

  # Read all remaining lines
  lines = f.readlines

  # Read character by character
  char = f.getc

  # Read byte by byte
  byte = f.getbyte

  # Check if at end of file
  f.eof?  # true or false

  # Rewind to beginning
  f.rewind

  # Seek to position
  f.seek(10)  # Go to byte 10

  # Current position
  pos = f.pos
end
```

**Iterating through files:**

```ruby
# Each line
File.open('data.txt', 'r') do |f|
  f.each_line do |line|
    puts line.chomp
  end
end

# Each with index
File.open('data.txt', 'r') do |f|
  f.each_line.with_index(1) do |line, num|
    puts "#{num}: #{line.chomp}"
  end
end

# Each character
File.open('data.txt', 'r') do |f|
  f.each_char do |char|
    print char
  end
end

# Each byte
File.open('data.txt', 'rb') do |f|
  f.each_byte do |byte|
    puts byte
  end
end
```

**Practical examples:**

```ruby
# Read CSV-like data
data = []
File.foreach('data.csv') do |line|
  fields = line.chomp.split(',')
  data << fields
end

# Count lines
line_count = File.readlines('data.txt').count
# Or more efficiently:
line_count = File.foreach('data.txt').count

# Find lines matching pattern
matches = []
File.foreach('log.txt') do |line|
  matches << line if line.match?(/ERROR/)
end

# Read first N lines
first_5_lines = File.readlines('data.txt').first(5)

# Read last line (for small files)
last_line = File.readlines('data.txt').last

# Process large file in chunks
File.open('large_file.txt', 'r') do |f|
  while chunk = f.read(1024 * 1024)  # 1MB chunks
    process(chunk)
  end
end

# Read JSON file
require 'json'
data = JSON.parse(File.read('data.json'))

# Read YAML file
require 'yaml'
config = YAML.load_file('config.yml')
```

**File existence and properties:**

```ruby
# Check if exists
File.exist?('data.txt')   # true or false

# Check if it's a file (not directory)
File.file?('data.txt')    # true or false

# Check if it's a directory
File.directory?('data.txt')  # false

# Check if readable
File.readable?('data.txt')   # true or false

# Check if writable
File.writable?('data.txt')   # true or false

# Get file size
size = File.size('data.txt')  # in bytes

# Check if empty
File.zero?('data.txt')        # true if empty

# Get modification time
mtime = File.mtime('data.txt')

# Get creation time (on some systems)
ctime = File.ctime('data.txt')

# Get access time
atime = File.atime('data.txt')
```

**Common pitfall:** Forgetting to use `chomp` to remove newlines:

```ruby
# Lines include newline characters
lines = File.readlines('data.txt')
puts lines.first.inspect  # "Hello\n"

# Use chomp to remove them
lines.each do |line|
  puts line.chomp  # "Hello"
end

# Or use chomp option
lines = File.readlines('data.txt', chomp: true)
puts lines.first.inspect  # "Hello"
```

---

## 12.2 Writing Files

Writing files in Ruby follows similar patterns to reading, with `File.write` for simple cases and `File.open` with blocks for more complex scenarios.

**Key Similarities:**
- Both use file modes ('w', 'a', etc.)
- Both support writing strings and bytes
- Both auto-flush on close

**Key Differences:**
- Ruby has `File.write` for one-line file writing
- Ruby's block syntax ensures files are closed
- Different append methods
- Ruby has `<<` operator for appending to file handles

### Python Example

```python
# Write string to file (overwrites)
with open('output.txt', 'w') as f:
    f.write('Hello, World!\n')

# Write multiple lines
with open('output.txt', 'w') as f:
    f.write('Line 1\n')
    f.write('Line 2\n')
    f.write('Line 3\n')

# Write list of lines
lines = ['Line 1\n', 'Line 2\n', 'Line 3\n']
with open('output.txt', 'w') as f:
    f.writelines(lines)

# Append to file
with open('output.txt', 'a') as f:
    f.write('Appended line\n')

# Write binary data
with open('output.bin', 'wb') as f:
    f.write(b'\x00\x01\x02\x03')

# Write with specific encoding
with open('output.txt', 'w', encoding='utf-8') as f:
    f.write('Hello, 世界!\n')

# Write and flush immediately
with open('output.txt', 'w') as f:
    f.write('Important data\n')
    f.flush()  # Ensure written to disk

# One-liner (not recommended - doesn't use with)
open('output.txt', 'w').write('Hello\n')
```

### Ruby Equivalent

```ruby
# Write string to file (overwrites) - simplest way
File.write('output.txt', 'Hello, World!')

# Write automatically adds newline? No! Add it yourself
File.write('output.txt', "Hello, World!\n")

# Write multiple strings
File.write('output.txt', "Line 1\nLine 2\nLine 3\n")

# Write array of lines
lines = ['Line 1', 'Line 2', 'Line 3']
File.write('output.txt', lines.join("\n") + "\n")

# Append to file
File.write('output.txt', "Appended line\n", mode: 'a')

# Write with File.open and block
File.open('output.txt', 'w') do |f|
  f.write("Line 1\n")
  f.write("Line 2\n")
  f.write("Line 3\n")
end

# Write with puts (adds newline automatically)
File.open('output.txt', 'w') do |f|
  f.puts 'Line 1'  # Automatically adds \n
  f.puts 'Line 2'
  f.puts 'Line 3'
end

# Write with << operator
File.open('output.txt', 'w') do |f|
  f << "Line 1\n"
  f << "Line 2\n"
  f << "Line 3\n"
end

# Write binary data
File.write('output.bin', "\x00\x01\x02\x03", mode: 'wb')

# Write with specific encoding
File.write('output.txt', 'Hello, 世界!', encoding: 'UTF-8')

# Or with File.open
File.open('output.txt', 'w:UTF-8') do |f|
  f.write('Hello, 世界!')
end

# Flush immediately
File.open('output.txt', 'w') do |f|
  f.write("Important data\n")
  f.flush  # Ensure written to disk
end
```

### Explanation

Ruby's `File.write` is the simplest way to write files for basic cases. For more control, use `File.open` with a block.

**Simple file writing:**

```ruby
# Overwrite file with content
File.write('file.txt', 'Hello, World!')

# Append to file
File.write('file.txt', 'More content', mode: 'a')

# Write with newline
File.write('file.txt', "Line 1\nLine 2\n")

# Write array (need to join first)
data = ['Line 1', 'Line 2', 'Line 3']
File.write('file.txt', data.join("\n"))
```

**Using File.open:**

```ruby
# Basic writing
File.open('file.txt', 'w') do |f|
  f.write('Hello, World!')
end

# Multiple writes
File.open('file.txt', 'w') do |f|
  f.write("First line\n")
  f.write("Second line\n")
  f.write("Third line\n")
end

# Using puts (auto-adds newline)
File.open('file.txt', 'w') do |f|
  f.puts 'First line'   # Adds \n automatically
  f.puts 'Second line'
  f.puts 'Third line'
end

# Using << operator
File.open('file.txt', 'w') do |f|
  f << "First line\n"
  f << "Second line\n"
end

# Using print (no automatic newline)
File.open('file.txt', 'w') do |f|
  f.print 'Hello, '
  f.print 'World!'
end
```

**Write methods comparison:**

```ruby
File.open('file.txt', 'w') do |f|
  # write - no automatic newline
  f.write('Hello')
  f.write('World')
  # File contains: HelloWorld
end

File.open('file.txt', 'w') do |f|
  # puts - adds newline
  f.puts 'Hello'
  f.puts 'World'
  # File contains: Hello\nWorld\n
end

File.open('file.txt', 'w') do |f|
  # print - no newline, but spaces between args
  f.print 'Hello', ' ', 'World'
  # File contains: Hello World
end

File.open('file.txt', 'w') do |f|
  # << operator - no newline, returns file for chaining
  f << 'Hello' << ' ' << 'World'
  # File contains: Hello World
end
```

**Writing arrays and collections:**

```ruby
# Array of strings
lines = ['Line 1', 'Line 2', 'Line 3']

# Join and write
File.write('file.txt', lines.join("\n") + "\n")

# Or with File.open and puts
File.open('file.txt', 'w') do |f|
  lines.each { |line| f.puts line }
end

# Or simpler
File.open('file.txt', 'w') do |f|
  f.puts lines  # puts can take an array!
end

# Write hash as key-value pairs
data = { name: 'Alice', age: 30, city: 'NYC' }
File.open('data.txt', 'w') do |f|
  data.each { |key, value| f.puts "#{key}: #{value}" }
end
```

**Appending to files:**

```ruby
# Append with File.write
File.write('log.txt', "New entry\n", mode: 'a')

# Append with File.open
File.open('log.txt', 'a') do |f|
  f.puts 'New entry'
end

# Append multiple times
File.open('log.txt', 'a') do |f|
  10.times do |i|
    f.puts "Entry #{i}"
  end
end
```

**Writing with encoding:**

```ruby
# Specify encoding with File.write
File.write('file.txt', 'Hello, 世界!', encoding: 'UTF-8')

# Specify encoding with File.open
File.open('file.txt', 'w:UTF-8') do |f|
  f.puts 'Hello, 世界!'
end

# Different external and internal encoding
File.open('file.txt', 'w', external_encoding: 'UTF-8', internal_encoding: 'UTF-8') do |f|
  f.puts 'Hello, 世界!'
end
```

**Practical examples:**

```ruby
# Write JSON
require 'json'
data = { name: 'Alice', age: 30 }
File.write('data.json', JSON.pretty_generate(data))

# Write YAML
require 'yaml'
config = { database: 'postgres', host: 'localhost' }
File.write('config.yml', config.to_yaml)

# Write CSV (simple)
data = [
  ['Name', 'Age', 'City'],
  ['Alice', '30', 'NYC'],
  ['Bob', '25', 'LA']
]
File.open('data.csv', 'w') do |f|
  data.each { |row| f.puts row.join(',') }
end

# Or use CSV library
require 'csv'
CSV.open('data.csv', 'w') do |csv|
  csv << ['Name', 'Age', 'City']
  csv << ['Alice', '30', 'NYC']
  csv << ['Bob', '25', 'LA']
end

# Log file with timestamp
File.open('app.log', 'a') do |f|
  f.puts "[#{Time.now}] Application started"
end

# Write with atomic replacement (create temp, then rename)
temp_file = 'output.txt.tmp'
File.write(temp_file, 'Important data')
File.rename(temp_file, 'output.txt')

# Copy file
File.write('copy.txt', File.read('original.txt'))
# Or use FileUtils
require 'fileutils'
FileUtils.cp('original.txt', 'copy.txt')
```

**Flushing and syncing:**

```ruby
File.open('important.txt', 'w') do |f|
  f.write('Critical data')
  f.flush  # Flush to OS buffer
  f.fsync  # Force OS to write to disk (slower but safer)
end

# Auto-sync mode (every write is flushed)
File.open('important.txt', 'w') do |f|
  f.sync = true
  f.write('Each write is flushed')
end
```

**Common pitfall:** Forgetting that `write` doesn't add newlines:

```ruby
# Wrong - no newlines
File.open('file.txt', 'w') do |f|
  f.write('Line 1')
  f.write('Line 2')
end
# File contains: Line 1Line 2

# Right - add newlines manually
File.open('file.txt', 'w') do |f|
  f.write("Line 1\n")
  f.write("Line 2\n")
end

# Or use puts
File.open('file.txt', 'w') do |f|
  f.puts 'Line 1'
  f.puts 'Line 2'
end
```

---

## 12.3 File.open with Blocks (auto-closing)

One of Ruby's most elegant features is the block-based file handling that automatically closes files, similar to Python's `with` statement but more concise.

**Key Similarities:**
- Both automatically close files
- Both handle exceptions gracefully
- Both ensure cleanup happens

**Key Differences:**
- Ruby uses blocks instead of context managers
- Ruby's syntax is more concise
- Ruby's approach is used throughout the standard library
- No need for `with` keyword

### Python Example

```python
# with statement ensures file is closed
with open('file.txt', 'r') as f:
    content = f.read()
    print(content)
# File automatically closed here

# Multiple files
with open('input.txt', 'r') as input_file, \
     open('output.txt', 'w') as output_file:
    content = input_file.read()
    output_file.write(content.upper())

# Even if exception occurs, file is closed
try:
    with open('file.txt', 'r') as f:
        content = f.read()
        raise Exception('Oops!')
except:
    pass
# File is still closed

# Without with statement (old style, not recommended)
f = open('file.txt', 'r')
try:
    content = f.read()
finally:
    f.close()  # Must manually close
```

### Ruby Equivalent

```ruby
# Block ensures file is closed
File.open('file.txt', 'r') do |f|
  content = f.read
  puts content
end
# File automatically closed here

# Nested blocks for multiple files
File.open('input.txt', 'r') do |input_file|
  File.open('output.txt', 'w') do |output_file|
    content = input_file.read
    output_file.write(content.upcase)
  end
end

# Even if exception occurs, file is closed
begin
  File.open('file.txt', 'r') do |f|
    content = f.read
    raise 'Oops!'
  end
rescue
  # Handle error
end
# File is still closed

# Without block (manual close required)
f = File.open('file.txt', 'r')
begin
  content = f.read
ensure
  f.close  # Must manually close
end

# But always prefer blocks!
```

### Explanation

Ruby's block-based file handling is one of its most elegant features. The file is automatically closed when the block exits, even if an exception is raised.

**How it works:**

```ruby
# With block - file closes automatically
File.open('file.txt', 'r') do |f|
  # File is open inside block
  content = f.read
  # File automatically closes when block ends
end

# Without block - must close manually
f = File.open('file.txt', 'r')
content = f.read
f.close  # Must remember this!

# Check if file is closed
f = File.open('file.txt', 'r')
puts f.closed?  # false
f.close
puts f.closed?  # true
```

**The block receives the file handle:**

```ruby
File.open('file.txt', 'r') do |file|
  # 'file' is the file handle
  puts file.class  # File
  puts file.path   # "file.txt"
  content = file.read
end
# 'file' is out of scope here, and closed
```

**Exception safety:**

```ruby
# File closes even if exception occurs
File.open('file.txt', 'r') do |f|
  content = f.read
  raise 'Something went wrong!'
end
rescue => e
  puts "Error: #{e}"
end
# File is still closed!

# Equivalent to:
f = File.open('file.txt', 'r')
begin
  content = f.read
  raise 'Something went wrong!'
ensure
  f.close  # Always executes
end
```

**Multiple files (nested blocks):**

```ruby
# Copy file contents
File.open('source.txt', 'r') do |source|
  File.open('dest.txt', 'w') do |dest|
    dest.write(source.read)
  end
end
# Both files closed automatically

# Process multiple files
['file1.txt', 'file2.txt', 'file3.txt'].each do |filename|
  File.open(filename, 'r') do |f|
    puts "Contents of #{filename}:"
    puts f.read
  end
  # Each file closed after processing
end
```

**Return value:**

```ruby
# Block form returns the block's return value
content = File.open('file.txt', 'r') do |f|
  f.read  # This value is returned
end
puts content

# Without block returns the file handle
f = File.open('file.txt', 'r')  # Returns File object
puts f.class  # File
f.close
```

**Practical pattern - process and transform:**

```ruby
# Read, transform, write
File.open('input.txt', 'r') do |input|
  File.open('output.txt', 'w') do |output|
    input.each_line do |line|
      output.puts line.upcase
    end
  end
end

# Count lines
line_count = File.open('file.txt', 'r') do |f|
  f.readlines.count
end

# Find and collect
errors = File.open('log.txt', 'r') do |f|
  f.readlines.select { |line| line.include?('ERROR') }
end
```

**Other classes using block pattern:**

```ruby
# Dir.chdir (change directory temporarily)
Dir.chdir('/tmp') do
  # Do work in /tmp
  puts Dir.pwd  # /tmp
end
# Back to original directory

# Tempfile (auto-deletes)
require 'tempfile'
Tempfile.create('myfile') do |f|
  f.write('Temporary data')
  f.rewind
  puts f.read
end
# Tempfile automatically deleted

# StringIO (in-memory file)
require 'stringio'
StringIO.open('initial content') do |io|
  io.puts 'Added line'
  io.rewind
  puts io.read
end
```

**Common pitfall:** Using the file handle outside the block:

```ruby
# Wrong - file is closed after block
file_handle = nil
File.open('file.txt', 'r') do |f|
  file_handle = f
end
# file_handle.read  # Error! File is closed

# Right - do all work inside block
content = nil
File.open('file.txt', 'r') do |f|
  content = f.read
end
puts content  # This works
```

---

## 12.4 File Modes

File modes specify how a file should be opened - for reading, writing, appending, etc. Ruby's file modes are similar to Python's but with some additional options.

**Key Similarities:**
- Both use string mode indicators ('r', 'w', 'a', etc.)
- Both support read, write, append, and binary modes
- Both use 'b' for binary mode

**Key Differences:**
- Ruby can specify encoding in the mode string
- Ruby has some additional mode flags
- Slightly different mode combinations

### Python Example

```python
# Read mode (default)
f = open('file.txt', 'r')   # Read text
f = open('file.txt', 'rb')  # Read binary

# Write mode (creates or truncates)
f = open('file.txt', 'w')   # Write text
f = open('file.txt', 'wb')  # Write binary

# Append mode
f = open('file.txt', 'a')   # Append text
f = open('file.txt', 'ab')  # Append binary

# Read and write
f = open('file.txt', 'r+')  # Read and write (file must exist)
f = open('file.txt', 'w+')  # Write and read (truncates)
f = open('file.txt', 'a+')  # Append and read

# Exclusive creation (fails if exists)
f = open('file.txt', 'x')   # Python 3.3+

# With encoding
f = open('file.txt', 'r', encoding='utf-8')
```

### Ruby Equivalent

```ruby
# Read mode (default)
f = File.open('file.txt', 'r')   # Read text
f = File.open('file.txt', 'rb')  # Read binary

# Write mode (creates or truncates)
f = File.open('file.txt', 'w')   # Write text
f = File.open('file.txt', 'wb')  # Write binary

# Append mode
f = File.open('file.txt', 'a')   # Append text
f = File.open('file.txt', 'ab')  # Append binary

# Read and write
f = File.open('file.txt', 'r+')  # Read and write (file must exist)
f = File.open('file.txt', 'w+')  # Write and read (truncates)
f = File.open('file.txt', 'a+')  # Append and read

# Encoding in mode string
f = File.open('file.txt', 'r:UTF-8')              # Read with encoding
f = File.open('file.txt', 'w:UTF-8')              # Write with encoding
f = File.open('file.txt', 'r:UTF-8:UTF-8')        # External:internal encoding
f = File.open('file.txt', 'r:ISO-8859-1:UTF-8')   # Transcode on read

# Exclusive creation
f = File.open('file.txt', 'w', File::EXCL)  # Fails if exists (use with constants)

# Or with string mode (no exact equivalent to Python's 'x')
# Check first:
unless File.exist?('file.txt')
  f = File.open('file.txt', 'w')
end
```

### Explanation

File modes control how Ruby opens and interacts with files. The mode string can include the access mode, binary flag, and encoding.

**Basic modes:**

```ruby
# 'r' - Read only (file must exist)
File.open('file.txt', 'r') do |f|
  content = f.read
end

# 'w' - Write only (creates or truncates file)
File.open('file.txt', 'w') do |f|
  f.write('New content')
end

# 'a' - Append only (creates if doesn't exist)
File.open('log.txt', 'a') do |f|
  f.puts 'Log entry'
end

# 'r+' - Read and write (file must exist, doesn't truncate)
File.open('file.txt', 'r+') do |f|
  content = f.read
  f.rewind
  f.write('Modified: ' + content)
end

# 'w+' - Read and write (creates or truncates)
File.open('file.txt', 'w+') do |f|
  f.write('New content')
  f.rewind
  puts f.read
end

# 'a+' - Read and append (creates if doesn't exist)
File.open('file.txt', 'a+') do |f|
  f.rewind
  content = f.read
  f.puts 'Appended'
end
```

**Binary mode:**

```ruby
# Text mode (default) - handles encoding
File.open('file.txt', 'r') do |f|
  text = f.read  # Returns string with encoding
end

# Binary mode - raw bytes
File.open('image.png', 'rb') do |f|
  bytes = f.read  # Returns binary string (ASCII-8BIT encoding)
end

# Write binary
File.open('data.bin', 'wb') do |f|
  f.write([1, 2, 3, 4].pack('C*'))  # Write bytes
end
```

**Encoding modes:**

```ruby
# Specify external encoding (file's encoding)
File.open('file.txt', 'r:UTF-8') do |f|
  content = f.read  # Read as UTF-8
end

# Specify external and internal encoding
# Format: 'mode:external:internal'
File.open('file.txt', 'r:ISO-8859-1:UTF-8') do |f|
  # Reads as ISO-8859-1, converts to UTF-8
  content = f.read
  puts content.encoding  # UTF-8
end

# Binary mode with BOM handling
File.open('file.txt', 'r:BOM|UTF-8') do |f|
  content = f.read  # Detects and strips BOM
end
```

**Mode reference table:**

| Mode | Description | File must exist? | Truncates? | Position |
|------|-------------|------------------|------------|----------|
| `r`  | Read only | Yes | No | Start |
| `r+` | Read/write | Yes | No | Start |
| `w`  | Write only | No | Yes | Start |
| `w+` | Read/write | No | Yes | Start |
| `a`  | Append only | No | No | End |
| `a+` | Read/append | No | No | End for writes, start for reads |

Add `b` for binary mode: `rb`, `wb`, `ab`, etc.

**File position with different modes:**

```ruby
# 'r+' - starts at beginning, doesn't truncate
File.write('file.txt', "Line 1\nLine 2\nLine 3\n")
File.open('file.txt', 'r+') do |f|
  puts f.read  # Reads all content
  f.rewind
  f.write('XXXX')  # Overwrites first 4 chars
end
# Result: "XXXXe 1\nLine 2\nLine 3\n"

# 'w+' - truncates file, starts at beginning
File.open('file.txt', 'w+') do |f|
  f.write("New content\n")
  f.rewind
  puts f.read  # "New content\n"
end

# 'a+' - appends, but can read from beginning
File.open('file.txt', 'a+') do |f|
  f.rewind  # Move to start for reading
  puts f.read  # Read existing content
  f.puts 'Appended line'  # Appends to end
end
```

**Using File constants:**

```ruby
# Open with flag constants
f = File.open('file.txt', File::RDONLY)  # Read only
f = File.open('file.txt', File::WRONLY)  # Write only
f = File.open('file.txt', File::RDWR)    # Read/write

# Combine flags with |
f = File.open('file.txt', File::RDWR | File::CREAT)  # Read/write, create if needed
f = File.open('file.txt', File::WRONLY | File::APPEND)  # Write, append mode
f = File.open('file.txt', File::WRONLY | File::TRUNC)  # Write, truncate

# Exclusive open (fail if exists)
begin
  f = File.open('file.txt', File::WRONLY | File::CREAT | File::EXCL)
  f.puts 'New file created'
  f.close
rescue Errno::EEXIST
  puts 'File already exists!'
end
```

**Practical examples:**

```ruby
# Update specific part of file
File.open('config.txt', 'r+') do |f|
  lines = f.readlines
  lines[2] = "updated line\n"
  f.rewind
  f.truncate(0)  # Clear file
  f.write(lines.join)
end

# Append to log with timestamp
File.open('app.log', 'a') do |f|
  f.puts "[#{Time.now}] Application event"
end

# Read binary file in chunks
File.open('large.bin', 'rb') do |f|
  while chunk = f.read(4096)
    process_chunk(chunk)
  end
end

# Create file only if it doesn't exist
unless File.exist?('settings.txt')
  File.write('settings.txt', 'default settings')
end

# Or use atomic creation
require 'tempfile'
Tempfile.create('settings') do |temp|
  temp.write('default settings')
  temp.close
  FileUtils.mv(temp.path, 'settings.txt')
end
```

**Common pitfall:** Using wrong mode for read/write:

```ruby
# Wrong - can't read in 'w' mode
File.open('file.txt', 'w') do |f|
  f.write('content')
  f.rewind
  # f.read  # Error: not opened for reading
end

# Right - use 'w+' for both
File.open('file.txt', 'w+') do |f|
  f.write('content')
  f.rewind
  puts f.read  # Works!
end

# Or use 'r+' if file exists
File.open('file.txt', 'r+') do |f|
  content = f.read
  f.rewind
  f.write('new content')
end
```

---

## 12.5 Working with Paths

Ruby provides the `File` and `Pathname` classes for working with file paths. While `File` offers class methods for path operations, `Pathname` provides an object-oriented interface similar to Python's `pathlib`.

**Key Similarities:**
- Both support path joining, splitting, and manipulation
- Both handle cross-platform path separators
- Both can get absolute paths, dirname, basename, etc.

**Key Differences:**
- Python 3 has `pathlib.Path` (object-oriented)
- Ruby has both `File` (class methods) and `Pathname` (OO)
- Different method names
- Ruby's `File` methods are more commonly used than `Pathname`

### Python Example

```python
import os
from pathlib import Path

# Join paths
path = os.path.join('dir', 'subdir', 'file.txt')
# Or with pathlib
path = Path('dir') / 'subdir' / 'file.txt'

# Get absolute path
abs_path = os.path.abspath('file.txt')
# pathlib
abs_path = Path('file.txt').absolute()

# Get directory name and basename
dirname = os.path.dirname('/path/to/file.txt')   # /path/to
basename = os.path.basename('/path/to/file.txt') # file.txt

# Split extension
name, ext = os.path.splitext('file.txt')  # ('file', '.txt')

# Check if path exists
if os.path.exists('file.txt'):
    print('exists')

# Check if it's a file or directory
os.path.isfile('file.txt')
os.path.isdir('directory')

# Get file size
size = os.path.getsize('file.txt')

# Expand user home directory
home_file = os.path.expanduser('~/file.txt')

# Current working directory
cwd = os.getcwd()

# pathlib approach (modern)
p = Path('file.txt')
print(p.name)       # file.txt
print(p.stem)       # file
print(p.suffix)     # .txt
print(p.parent)     # .
print(p.exists())
print(p.is_file())
```

### Ruby Equivalent

```ruby
# Join paths
path = File.join('dir', 'subdir', 'file.txt')

# Get absolute path
abs_path = File.absolute_path('file.txt')
# Or
abs_path = File.expand_path('file.txt')

# Get directory name and basename
dirname = File.dirname('/path/to/file.txt')   # /path/to
basename = File.basename('/path/to/file.txt') # file.txt

# Get basename without extension
name = File.basename('/path/to/file.txt', '.txt')  # file

# Get extension
ext = File.extname('file.txt')  # .txt

# Split path
dir, file = File.split('/path/to/file.txt')  # ["/path/to", "file.txt"]

# Check if path exists
if File.exist?('file.txt')
  puts 'exists'
end

# Check if it's a file or directory
File.file?('file.txt')
File.directory?('directory')

# Get file size
size = File.size('file.txt')

# Expand tilde (home directory)
home_file = File.expand_path('~/file.txt')

# Current working directory
cwd = Dir.pwd

# Using Pathname (object-oriented)
require 'pathname'
p = Pathname.new('file.txt')
puts p.basename      # file.txt
puts p.extname       # .txt
puts p.dirname       # .
puts p.exist?
puts p.file?
puts p.directory?
```

### Explanation

Ruby provides multiple ways to work with paths. `File` class methods are most common, but `Pathname` offers a more object-oriented approach.

**Path joining:**

```ruby
# File.join - handles path separators automatically
path = File.join('dir', 'subdir', 'file.txt')
# => "dir/subdir/file.txt" (or "dir\subdir\file.txt" on Windows)

# Array form
parts = ['dir', 'subdir', 'file.txt']
path = File.join(parts)

# Leading slash indicates absolute path
path = File.join('/', 'usr', 'local', 'bin')
# => "/usr/local/bin"
```

**Path components:**

```ruby
path = '/home/user/documents/file.txt'

# Directory containing the file
File.dirname(path)   # => "/home/user/documents"

# Filename
File.basename(path)  # => "file.txt"

# Filename without extension
File.basename(path, '.txt')  # => "file"
File.basename(path, '.*')    # => "file" (any extension)

# Extension
File.extname(path)   # => ".txt"

# Split into directory and filename
dir, file = File.split(path)
# dir => "/home/user/documents"
# file => "file.txt"

# Split path into all components (not built-in, but easy)
path.split(File::SEPARATOR)
# => ["", "home", "user", "documents", "file.txt"]
```

**Absolute and expanded paths:**

```ruby
# Expand to absolute path
File.absolute_path('file.txt')
# => "/current/working/directory/file.txt"

File.absolute_path('../file.txt')
# => "/current/working/file.txt"

# expand_path also handles ~ and ~user
File.expand_path('~/documents/file.txt')
# => "/home/username/documents/file.txt"

File.expand_path('~alice/file.txt')
# => "/home/alice/file.txt"

# Expand relative to specific directory
File.expand_path('file.txt', '/tmp')
# => "/tmp/file.txt"

# Real path (resolves symlinks)
File.realpath('symlink.txt')
# => "/path/to/actual/file.txt"

# realdirpath (like realpath but file doesn't need to exist)
File.realdirpath('nonexistent.txt', '/tmp')
# => "/tmp/nonexistent.txt"
```

**Path testing:**

```ruby
path = 'file.txt'

# Existence
File.exist?(path)      # true/false
File.exists?(path)     # deprecated alias

# Type checks
File.file?(path)       # Is it a file?
File.directory?(path)  # Is it a directory?
File.symlink?(path)    # Is it a symlink?

# Permissions
File.readable?(path)   # Can read?
File.writable?(path)   # Can write?
File.executable?(path) # Can execute?

# Size
File.size(path)        # Size in bytes
File.zero?(path)       # Is it empty?

# Ownership and permissions
File.owned?(path)      # Owned by effective user?
File.grpowned?(path)   # Owned by effective group?

# Comparison
File.identical?('file1.txt', 'file2.txt')  # Same file?
```

**Using Pathname:**

```ruby
require 'pathname'

# Create pathname object
path = Pathname.new('/home/user/documents/file.txt')

# Path components
puts path.dirname      # #<Pathname:/home/user/documents>
puts path.basename     # #<Pathname:file.txt>
puts path.extname      # ".txt"

# Join paths with /
new_path = path.dirname / 'other_file.txt'
# => #<Pathname:/home/user/documents/other_file.txt>

# Convert to string
puts path.to_s         # "/home/user/documents/file.txt"

# Expand
puts path.expand_path  # Absolute path

# Test
puts path.exist?
puts path.file?
puts path.directory?
puts path.absolute?
puts path.relative?

# Read/write through pathname
content = path.read
path.write('new content')

# Iterate directory
Pathname.new('dir').children.each do |child|
  puts child
end

# Navigate up
parent = path.parent   # Same as dirname
grandparent = path.parent.parent

# Relative path from one to another
from = Pathname.new('/home/user/documents')
to = Pathname.new('/home/user/pictures/photo.jpg')
relative = to.relative_path_from(from)
# => #<Pathname:../pictures/photo.jpg>
```

**Practical examples:**

```ruby
# Build path to file in same directory as script
script_dir = File.dirname(__FILE__)
config_file = File.join(script_dir, 'config.yml')

# Find files with specific extension
dir = '/path/to/files'
Dir.glob(File.join(dir, '*.txt')).each do |file|
  puts File.basename(file)
end

# Ensure directory exists before writing file
path = '/path/to/output/file.txt'
dir = File.dirname(path)
require 'fileutils'
FileUtils.mkdir_p(dir)  # Create directory if needed
File.write(path, 'content')

# Get all .rb files recursively
Dir.glob('**/*.rb').each do |file|
  puts file
end

# Using Pathname for cleaner code
require 'pathname'

# Process all .txt files in a directory
Pathname.new('data').children.select(&:file?).each do |file|
  next unless file.extname == '.txt'
  puts "Processing #{file.basename}"
  content = file.read
  # Process content
end

# Build relative path
base = Pathname.new('/home/user/project')
file = base / 'src' / 'main.rb'
puts file  # /home/user/project/src/main.rb

# Check if path is under directory
def under_directory?(path, dir)
  Pathname.new(path).expand_path.to_s.start_with?(
    Pathname.new(dir).expand_path.to_s
  )
end
```

**Common pitfall:** Not handling path separators correctly:

```ruby
# Wrong - won't work on Windows
path = 'dir/subdir/file.txt'

# Right - use File.join
path = File.join('dir', 'subdir', 'file.txt')

# Or use Pathname with /
path = Pathname.new('dir') / 'subdir' / 'file.txt'

# File::SEPARATOR gives OS-specific separator
puts File::SEPARATOR  # "/" on Unix, "\" on Windows
```

---

## 12.6 Directory Operations

Ruby provides the `Dir` class and `FileUtils` module for directory operations. These allow you to create, list, navigate, and remove directories.

**Key Similarities:**
- Both can list directory contents
- Both can create and remove directories
- Both support glob patterns
- Both can change working directory

**Key Differences:**
- Ruby uses `Dir` class and `FileUtils` module
- Python uses `os` and `shutil`
- Different method names and organization
- Ruby's `Dir.glob` is more powerful than Python's basic `glob`

### Python Example

```python
import os
import shutil
import glob

# List directory contents
files = os.listdir('.')
for file in files:
    print(file)

# List with full paths
for file in os.listdir('.'):
    full_path = os.path.join('.', file)
    print(full_path)

# Glob pattern matching
txt_files = glob.glob('*.txt')
all_txt = glob.glob('**/*.txt', recursive=True)

# Create directory
os.mkdir('new_dir')

# Create nested directories
os.makedirs('path/to/nested/dir', exist_ok=True)

# Remove empty directory
os.rmdir('empty_dir')

# Remove directory tree
shutil.rmtree('directory')

# Change directory
os.chdir('/tmp')
print(os.getcwd())

# Temporary directory change
original = os.getcwd()
os.chdir('/tmp')
# Do work
os.chdir(original)

# Walk directory tree
for root, dirs, files in os.walk('.'):
    for file in files:
        print(os.path.join(root, file))
```

### Ruby Equivalent

```ruby
# List directory contents
files = Dir.entries('.')
files.each { |file| puts file }

# List without . and ..
files = Dir.children('.')

# List with full paths
Dir.foreach('.') do |file|
  full_path = File.join('.', file)
  puts full_path
end

# Glob pattern matching
txt_files = Dir.glob('*.txt')
all_txt = Dir.glob('**/*.txt')

# Create directory
Dir.mkdir('new_dir')

# Create nested directories (use FileUtils)
require 'fileutils'
FileUtils.mkdir_p('path/to/nested/dir')

# Remove empty directory
Dir.rmdir('empty_dir')
# Or
Dir.delete('empty_dir')

# Remove directory tree (use FileUtils)
require 'fileutils'
FileUtils.rm_rf('directory')

# Change directory
Dir.chdir('/tmp')
puts Dir.pwd

# Temporary directory change with block
Dir.chdir('/tmp') do
  puts Dir.pwd  # /tmp
  # Do work
end
# Back to original directory

# Walk directory tree (use Find)
require 'find'
Find.find('.') do |path|
  puts path if File.file?(path)
end
```

### Explanation

The `Dir` class handles directory operations, while `FileUtils` provides more advanced file and directory utilities.

**Listing directories:**

```ruby
# All entries (including . and ..)
entries = Dir.entries('.')
# => [".", "..", "file1.txt", "file2.txt", "subdir"]

# Without . and ..
entries = Dir.children('.')
# => ["file1.txt", "file2.txt", "subdir"]

# Filter for files only
files = Dir.children('.').select { |f| File.file?(f) }

# Filter for directories only
dirs = Dir.children('.').select { |f| File.directory?(f) }

# Iterate with block
Dir.foreach('.') do |entry|
  next if entry == '.' || entry == '..'
  puts entry
end

# Each (similar to foreach)
Dir.each('.') do |entry|
  puts entry
end
```

**Glob patterns:**

```ruby
# All .txt files in current directory
Dir.glob('*.txt')

# All .rb files recursively
Dir.glob('**/*.rb')

# Multiple patterns
Dir.glob(['*.txt', '*.md'])
# Or with braces
Dir.glob('*.{txt,md}')

# Case-insensitive (use flags)
Dir.glob('*.TXT', File::FNM_CASEFOLD)

# Match dotfiles too
Dir.glob('*', File::FNM_DOTMATCH)

# Common patterns
Dir.glob('*')           # All files/dirs in current dir
Dir.glob('**/*')        # All files/dirs recursively
Dir.glob('src/**/*.rb') # All .rb files under src/
Dir.glob('*.{rb,py}')   # All .rb and .py files

# Using block
Dir.glob('*.txt') do |file|
  puts "Found: #{file}"
end

# Get Pathname objects
require 'pathname'
paths = Dir.glob('*.txt').map { |f| Pathname.new(f) }
```

**Creating directories:**

```ruby
# Create single directory
Dir.mkdir('new_directory')

# Create with permissions
Dir.mkdir('new_directory', 0755)

# Check if exists first
Dir.mkdir('new_directory') unless Dir.exist?('new_directory')

# Create nested directories (FileUtils required)
require 'fileutils'
FileUtils.mkdir_p('path/to/nested/directory')

# Create multiple directories
FileUtils.mkdir_p(['dir1', 'dir2', 'dir3'])

# Create directory and parents if needed
require 'pathname'
Pathname.new('path/to/directory').mkpath
```

**Removing directories:**

```ruby
# Remove empty directory
Dir.rmdir('empty_directory')
# Or
Dir.delete('empty_directory')
# Or
Dir.unlink('empty_directory')

# Remove directory and all contents (dangerous!)
require 'fileutils'
FileUtils.rm_rf('directory')

# Remove multiple directories
FileUtils.rm_rf(['dir1', 'dir2', 'dir3'])

# Remove only if empty
begin
  Dir.rmdir('directory')
rescue Errno::ENOTEMPTY
  puts 'Directory not empty'
end

# Safe removal - only if exists
FileUtils.rm_rf('directory') if Dir.exist?('directory')
```

**Changing directories:**

```ruby
# Change directory
Dir.chdir('/tmp')
puts Dir.pwd  # /tmp

# Change with block (auto-restores)
original_dir = Dir.pwd
Dir.chdir('/tmp') do
  puts Dir.pwd  # /tmp
  # Do work in /tmp
end
puts Dir.pwd  # original_dir (automatically restored)

# Get current directory
current = Dir.pwd
# Or
current = Dir.getwd

# Home directory
home = Dir.home
# Or specific user
user_home = Dir.home('alice')
```

**Working with FileUtils:**

```ruby
require 'fileutils'

# Copy file
FileUtils.cp('source.txt', 'dest.txt')

# Copy directory recursively
FileUtils.cp_r('source_dir', 'dest_dir')

# Move/rename
FileUtils.mv('old_name.txt', 'new_name.txt')

# Remove file
FileUtils.rm('file.txt')

# Remove files matching pattern
FileUtils.rm(Dir.glob('*.tmp'))

# Remove directory tree
FileUtils.rm_rf('directory')

# Create symlink
FileUtils.ln_s('target.txt', 'link.txt')

# Change permissions
FileUtils.chmod(0644, 'file.txt')
FileUtils.chmod_R(0755, 'directory')

# Change owner
FileUtils.chown('user', 'group', 'file.txt')

# Touch (create empty file or update timestamp)
FileUtils.touch('file.txt')

# Compare files
FileUtils.identical?('file1.txt', 'file2.txt')

# Dry run mode (doesn't actually do anything)
FileUtils.mkdir_p('test/dir', noop: true)

# Verbose mode (prints what it's doing)
FileUtils.cp('source.txt', 'dest.txt', verbose: true)
```

**Directory traversal:**

```ruby
# Using Find module
require 'find'

Find.find('.') do |path|
  # Process each file and directory
  puts path
end

# Skip directories
require 'find'
Find.find('.') do |path|
  if File.directory?(path) && File.basename(path) == 'skip_me'
    Find.prune  # Don't descend into this directory
  end
  puts path if File.file?(path)
end

# Custom traversal
def traverse_directory(dir, &block)
  Dir.foreach(dir) do |entry|
    next if entry == '.' || entry == '..'
    path = File.join(dir, entry)
    block.call(path)
    traverse_directory(path, &block) if File.directory?(path)
  end
end

traverse_directory('.') do |path|
  puts path
end
```

**Practical examples:**

```ruby
# Find all Ruby files
ruby_files = Dir.glob('**/*.rb')
ruby_files.each { |f| puts f }

# Count files by extension
require 'pathname'
extensions = Hash.new(0)
Dir.glob('**/*').each do |file|
  next unless File.file?(file)
  ext = File.extname(file)
  extensions[ext] += 1
end
puts extensions

# Clean up temp files
Dir.glob('*.tmp').each do |file|
  File.delete(file)
  puts "Deleted #{file}"
end

# Copy directory structure
require 'fileutils'
source = 'source_dir'
dest = 'dest_dir'
Dir.glob("#{source}/**/*").each do |file|
  relative = file.sub(source, '')
  dest_path = File.join(dest, relative)
  if File.directory?(file)
    FileUtils.mkdir_p(dest_path)
  else
    FileUtils.mkdir_p(File.dirname(dest_path))
    FileUtils.cp(file, dest_path)
  end
end

# Find largest files
files_with_sizes = Dir.glob('**/*').select { |f| File.file?(f) }
                      .map { |f| [f, File.size(f)] }
                      .sort_by { |_, size| -size }
                      .first(10)

files_with_sizes.each do |file, size|
  puts "#{file}: #{size} bytes"
end

# Organize files by extension
Dir.glob('*').select { |f| File.file?(f) }.each do |file|
  ext = File.extname(file)[1..]  # Remove leading dot
  next if ext.empty?

  require 'fileutils'
  FileUtils.mkdir_p(ext)
  FileUtils.mv(file, ext)
end

# Backup directory
require 'fileutils'
source = 'important_data'
timestamp = Time.now.strftime('%Y%m%d_%H%M%S')
backup = "backup_#{timestamp}"
FileUtils.cp_r(source, backup)
puts "Backed up to #{backup}"
```

**Common pitfall:** Forgetting to check if directory exists before creating:

```ruby
# Wrong - raises error if exists
Dir.mkdir('my_dir')  # Error if directory already exists

# Right - check first
Dir.mkdir('my_dir') unless Dir.exist?('my_dir')

# Or use FileUtils.mkdir_p (doesn't error if exists)
require 'fileutils'
FileUtils.mkdir_p('my_dir')  # Safe to call even if exists
```

---

This chapter covered comprehensive file I/O in Ruby:

- **Reading files** - `File.read`, `File.readlines`, `File.foreach`, and more
- **Writing files** - `File.write`, `File.open` with blocks, `puts` vs `write`
- **Block-based file handling** - Automatic cleanup and exception safety
- **File modes** - Understanding 'r', 'w', 'a', and encoding modes
- **Path operations** - `File` methods and `Pathname` for path manipulation
- **Directory operations** - `Dir` class and `FileUtils` module for directory management

Ruby's file I/O system is elegant and practical. The block-based approach ensures files are always properly closed, and the rich set of methods makes common operations simple while still supporting advanced use cases. Combined with `FileUtils` and glob patterns, Ruby provides a complete toolkit for file system operations.
