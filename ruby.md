**Ruby**

# Loops

```ruby
counter = 1
while counter < 11
  puts counter
  counter += 1
end
```
```ruby
counter = 1
until counter > 10
  puts counter
  counter += 1
end
```
```ruby
for num in 1..10
  puts num
end

for num in 1...11
  puts num
end
```
```ruby
i = 0
loop do
  i += 1
  puts i
  break if i > 10
end
```
```ruby
for i in 1..5
  next if i % 2 == 0
  print i
end
```
```ruby
range = 1..10
range.each do |i|
  puts i
end
```
```ruby
10.times { |i| puts i }
1.upto(10) { |i| puts i }
10.downto(1) { |i| puts i }
```
```ruby
["a", "b", "c"].each do |e|
  print "#{e}! "
end
# prints "a! b! c!"; returns the array
```
```ruby
{"Pinkie Pie" => "earth pony", "Rainbow Dash" => "pegasus", "Twilight Sparkle" => "unicorn"}.each do |name, race|
  puts "#{name} is a #{race}"
end

[:hearts, :spades, :clubs, :diamonds].each_with_index do |suit, i|
  puts "#{i}: #{suit}"
end
```
```ruby
next # jump to next iteration
redo # restart loop; don't recheck the condition
retry # restart loop, rechecking the condition
break # exit loop
```

# Arrays

## Create array of strings

Arrays of strings can be created using ruby's [percent string](http://ruby-doc.org/core/doc/syntax/literals_rdoc.html#label-Percent+Strings) syntax:
```rb
array = %w(one two three four)
```

This is functionally equivalent to defining the array as:
```rb
array = ['one', 'two', 'three', 'four']
```

Instead of `%w()` you may use other matching pairs of delimiters: `%w{...}`, `%w[...]`, or `%w<...>`.

It is also possible to use arbitrary non-alphanumeric delimiters, such as: `%w!...!`, `%w#...#`, or `%w@...@`.

`%W` can be used instead of `%w` to incorporate string interpolation. Consider the following:
```rb
var = 'hello'

%w(#{var}) # => ["\#{var}"]
%W(#{var}) # => ["hello"]
```
Multiple words can be interpreted by escaping the space with a `\`.
```rb
%w(Colorado California New\ York) # => ["Colorado", "California", "New York"]
```

## Create array of symbols

`array = %i(one two three four)` creates the array `[:one, :two, :three, :four]`.

You can use other delimiters (`[]`, `{}`, `!!`...) instead of `()`, as with strings.

Additionally, if you want to use interpolation, you can do this with %I.

```rb
a = 'hello'
b = 'goodbye'
array_one = %I(#{a} #{b} world) # => [:hello, :goodbye, :world]
array_two = %i(#{a} #{b} world) # => [:"\#{a}", :"\#{b}", :world]
```

## Manipulating array elements

Adding elements:
```rb
[1, 2, 3] << 4
# => [1, 2, 3, 4]

[1, 2, 3].push(4)
# => [1, 2, 3, 4]

[1, 2, 3].unshift(4)
# => [4, 1, 2, 3]

[1, 2, 3] << [4, 5]
# => [1, 2, 3, [4, 5]]
```

Removing elements:
```rb
array = [1, 2, 3, 4]
array.pop
# => 4
array
# => [1, 2, 3]

array = [1, 2, 3, 4]
array.shift
# => 1
array
# => [2, 3, 4]

array = [1, 2, 3, 4]
array.delete(1)
# => 1
array
# => [2, 3, 4]

array = [1,2,3,4,5,6]
array.delete_at(2) // delete from index 2
# => 3  
array
# => [1,2,4,5,6]


array = [1, 2, 2, 2, 3]
array - [2]
# => [1, 3]    # removed all the 2s
array - [2, 3, 4]
# => [1]       # the 4 did nothing
```

Combining arrays:
```rb
[1, 2, 3] + [4, 5, 6]
# => [1, 2, 3, 4, 5, 6]

[1, 2, 3].concat([4, 5, 6])
# => [1, 2, 3, 4, 5, 6]

[1, 2, 3, 4, 5, 6] - [2, 3]
# => [1, 4, 5, 6]

[1, 2, 3] | [2, 3, 4]
# => [1, 2, 3, 4]

[1, 2, 3] & [3, 4]
# => [3]
```

You can also multiply arrays:
```rb
[1, 2, 3] * 2
# => [1, 2, 3, 1, 2, 3]
```

# Closures

```ruby
# One line block
[1,2,3].each { |num| print "#{num}! " }
```
```ruby
# Multi-line block
[1,2,3].each do |num|
  print "#{num}! "
end
```
```ruby
# Yielding to a block
def method_taking_block
  yield("some argument for the block") if block_given?
end
```
```ruby
# Create and use a Proc
my_proc = Proc.new { |arg1| print "#{arg1}! " }
[1,2,3].each(my_proc)
```
```ruby
# Call a Proc inside a method
# Sort of like `yield`ing
my_proc.call("Some Argument")
```
```ruby
# Make a new Lambda (just like Procs)
my_lam = lambda { |word| puts word }
```
```ruby
# Use a Method
method(:print_stuff_method).call("something to print")
```
