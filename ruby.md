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
