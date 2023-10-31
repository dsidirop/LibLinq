# LibLinq

LibLinq is a collection management library similar to .NET's System.Linq.

Originally pulled from [wowace](https://www.wowace.com/projects/liblinq-1-0). All credit goes to this lib's original author ckknight@gmail.com

## Features

This library provides a very functional approach to dealing with collections and sequences. Rather than handling collection data imperatively, it is handled declaratively.

It also is thoroughly unit-tested.

The library is built around the concept of an Enumerable, which nearly all collections could be theoretically classified as.

Many generators are provided, such as Enumerable.Range(0, 10), which generates an increasing sequence starting at 0 with a count of 10. See the API documentation for a list of more generators.

On any Enumerable, there are many methods that can be called to lazily gleam data or generate other Enumerables.

In this example:

```lua
tostring(Enumerable.From({ 1, 2, 3 }):Select(function(x) return x * x end)) == "[1, 4, 9]"
-- or
tostring(Enumerable.From({ 1, 2, 3 }):Select("x => x*x")) == "[1, 4, 9]"
```

Given an initial Enumerable containing the elements 1, 2, and 3, they are each multiplied by themselves to provide an enumerable containing 1, 4, and 9.

The other typical case is filtering down a larger sequence to a smaller one

```lua
tostring(Enumerable.From({ 1, 2, 3, 4 }):Where(function(x) return x % 2 == 0 end)) == "[2, 4]"
-- or
tostring(Enumerable.From({ 1, 2, 3, 4 }):Where("x => x%2 == 0")) == "[2, 4]"
```

As you can see, it got rid of 1 and 3 since they didn't match the where clause.

For efficiency's sake, and to allow for easy handling of infinite sequences, nearly all such methods are handled lazily and only performed on iteration. The exception to this is one that has to iterate over all items such as :Aggregate() or :Last().

See the API for the many more methods available.

Three types of standard collections provided, which all inherit the Enumerable methods, are:

List (similar to Lua's 1-indexed numerically-keyed tables)
Set (similar to a Lua table where all values are true)
Dictionary (A typically 1-to-1 mapping similar to a typical Lua table if it were used that way).
Each of these can transparently handle nil, unlike typical lua tables.

Each of these can be instantiated typically with a New function

```lua
local list = List.New() or List.New({ 1, 2, 3 }) or List.FromArguments(1, 2, 3)
local set = Set.New() or Set.New({ 1, 2, 3 }) or Set.FromArguments(1, 2, 3)
local dict = Dictionary.New() or Dictionary.New({ alpha = 1, bravo = 2 })
```

You can also wrap existing tables as such:

```lua
local list = List.WrapTable({ 1, 2, 3 })
local set = Dictionary.WrapTable({ [1] = true, [2] = true, [3] = true })
local dict = Set.WrapTable({ alpha = 1, bravo = 2 }) Any changes to these tables will affect the wrapped collection and vice-versa. These cannot gracefully handle nil, though, a deficiency of the Lua table system.
```

One nice thing about Set and Dictionary is that a comparison function can be supplied to make it so that the keys follow a different unique constraint rather than a simple equality check. For example, if you wanted a Set that was case-insensitive:

```lua
local set = Set.New(nil, string.upper)
set:Add("Hey")
set:Add("HEY")
set:Add("hey")
assert(set:Count() == 1)
assert(set:ToString() == 'Set["Hey"]')
```
