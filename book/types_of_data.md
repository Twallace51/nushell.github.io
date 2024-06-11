# Types of Data

Traditionally, Unix shell commands have communicated with each other using strings of text:  
one command would write text to standard output (often abbreviated 'stdout')  
and the other would read text from standard input (or 'stdin'), 
allowing the two commands to communicate.

Nu embraces this approach, and expands it to include other types of data, in addition to strings.

Like many programming languages, Nu models data using a set of simple, and structured data types.  
Simple data types include integers, floats, strings, booleans, dates.  
There are also special types for filesizes and time durations.

The [`describe`](/commands/docs/describe.md) command returns the type of a data value:

```nu
42 | describe   # > int
```

## Types at a glance

| Type              | Example                                                               |
| ----------------- | --------------------------------------------------------------------- |
| Integers          | `-65535`                                                              |
| Decimals (floats) | `9.9999`, `Infinity`                                                  |
| Strings           | <code>"hole 18", 'hole 18', \`hole 18\`, hole18</code>                |
| Booleans          | `true`                                                                |
| Dates             | `2000-01-01`                                                          |
| Durations         | `2min + 12sec`                                                        |
| File sizes        | `64mb`                                                                |
| Ranges            | `0..4`, `0..<5`, `4..0`, `..4`                                         |
| Binary            | `0x[FE FF]`                                                           |
| Lists             | `[0 1 'two' 3 32mb 2024-06-10]`                                                       |
| Records           | `{name:"Nushell", lang: "Rust"}`                                      |
| Tables            | `[{x:12, y:15}, {x:8, y:9}]`, `[[x, y]; [12, 15], [8, 9]]`            |
| Closures          | `{\|e\| $e + 1 \| into string }`, `{ $in.name.0 \| path exists }`     |
| Blocks            | `if true { print "hello!" }`, `loop { print "press ctrl-c to exit" }` |
| Null              | `null`                                                                |

## Integers

Examples of integers (i.e. "round numbers") include 1, 0, -5, and 100.
You can parse a string into an integer with the [`into int`](/commands/docs/into_int.md) command

```nu
"-5" | into int   # > -5
```

## Decimals (floats)

Decimal numbers are numbers with some fractional component.  
Examples include 1.5, 2.0, and 15.333.  
You can cast a string into a Float with the [`into float`](/commands/docs/into_float.md) command.

```nu
"1.2" | into float    # > 1.2
```

## Strings

A string of characters that represents text.  
There are a few ways these can be constructed:

- Double quotes
```
"Line1\nLine2\n"
```

- Single quotes 
```
'She said "Nushell is the future"
```

- Dynamic string interpolation
```
$"6 x 7 = (6 * 7)"          # > 6 x 7 = 42
```
```
ls ~ | each { |it| $"($it.name) is ($it.size)" }
```
```
╭───┬──────────────────────╮  
│ 0 │ Desktop is 4.0 KiB   │  
│ 1 │ Documents is 4.0 KiB │  
│ 2 │ Downloads is 4.0 KiB │  
│ 3 │ Music is 4.0 KiB     │  
│ 4 │ Pictures is 4.0 KiB  │
│ 5 │ Public is 4.0 KiB    │  
│ 6 │ Templates is 4.0 KiB │
│ 7 │ Videos is 4.0 KiB    │  
│ 8 │ test.sh is 40 B      │
╰───┴──────────────────────╯
```
- Bare strings
```
print hello         # > hello
```

```
[foo bar baz]      
```
```
╭───┬─────╮
│ 0 │ foo │
│ 1 │ bar │
│ 2 │ baz │
╰───┴─────╯
```


See [Working with strings](working_with_strings.md) and [Handling Strings](https://www.nushell.sh/book/loading_data.html#handling-strings) for details.

## Booleans

There are just two boolean values: `true` and `false`.  
Rather than writing the values directly, they often result from a comparison:

```
let mybool = 2 > 1
$mybool                   # > true
```
```
let mybool = ($env.HOME | path exists)
$mybool                   # > true
```


## Dates

Dates and times are held together in the Date value type.  
Date values used by the system are timezone-aware, and by default use the UTC timezone.

Dates are in three forms, based on the RFC 3339 standard:

- A date:
```
2022-02-02           # > Wed, 2 Feb 2022 00:00:00 +0000 (2 years ago)
```
- A date and time (in GMT):
```
2022-02-02T14:30:00
```
- A date and time with timezone:
```
2022-02-02T14:30:00+05:00
```
## Durations

Durations represent a length of time.  
This chart shows all durations currently supported:

| Duration | Length          |
| -------- | --------------- |
| `1ns`    | one nanosecond  |
| `1us`    | one microsecond |
| `1ms`    | one millisecond |
| `1sec`   | one second      |
| `1min`   | one minute      |
| `1hr`    | one hour        |
| `1day`   | one day         |
| `1wk`    | one week        |

You can make fractional durations:

```nu
3.14day               # > 3day 3hr 21min
```

And you can do calculations with durations:

```nu
30day / 1sec  # How many seconds in 30 days?  > 2592000
```

## File sizes

Nushell also has a special type for file sizes.  
Examples include `100b`, `15kb`, and `100mb`.

The full list of filesize units are:

- `b`: bytes
- `kb`: kilobytes (aka 1000 bytes)
- `mb`: megabytes
- `gb`: gigabytes
- `tb`: terabytes
- `pb`: petabytes
- `eb`: exabytes
- `kib`: kibibytes (aka 1024 bytes)
- `mib`: mebibytes
- `gib`: gibibytes
- `tib`: tebibytes
- `pib`: pebibytes
- `eib`: exbibytes

As with durations, you can make fractional file sizes, and do calculations:
```
1Gb / 1b                        # > 1000000000
```
```
1Gib / 1b                       # > 1073741824
```
```
(1Gib / 1b) == 2 ** 30          # > true
```

## Ranges

A range is a way of expressing a sequence of integer or float values from start to finish.  
They take the form \<start\>..\<end\>.  
For example, the range `1..3` means the numbers 1, 2, and 3.

You can also easily create lists of characters with a form similar to ranges,  
with the command [`seq char`](/commands/docs/seq_char.html),
as well as with dates using the [`seq date`](/commands/docs/seq_date.html) command.

### Specifying the step

You can specify the step of a range with the form \<start\>..\<second\>..\<end\>,  
where the step between values in the range is the distance between the \<start\> and \<second\> values,  
which numerically is \<second\> - \<start\>.  
For example, the range `2..5..11` means the numbers 2, 5, 8, and 11 because the step is \<second\> - \<first\> = 5 - 2 = 3.  
The third value is 5 + 3 = 8 and the fourth value is 8 + 3 = 11.

[`seq`](/commands/docs/seq.md) can also create sequences of numbers, and provides an alternate way of specifying the step with three parameters.  
It's called with `seq $start $step $end` where the step amount is the second parameter rather than being the second parameter minus the first parameter.  
So `2..5..9` would be equivalent to `seq 2 3 9`.

### Inclusive and non-inclusive ranges

Ranges are inclusive by default, meaning that the ending value is counted as part of the range.  
The range `1..3` includes the number `3` as the last value in the range.

Sometimes, you may want a range that is limited by a number but doesn't use that number in the output.  
For this, you can use `..<` instead of `..`.  
For example, `1..<5` is the numbers 1, 2, 3, and 4.

### Open-ended ranges

Ranges can also be open-ended.  
You can remove the start or the end of the range to make it open-ended.

Let's say you wanted to start counting at 3, but you didn't have a specific end in mind.  
You could use the range `3..` to represent this.  
When you use a range that's open-ended on the right side, remember that this will continue counting for as long as possible,  
which could be a very long time!  
You'll often want to use open-ended ranges with commands like [`take`](/commands/docs/take.md),  
so you can take the number of elements you want from the range.

You can also make the start of the range open.  
In this case, Nushell will start counting with `0`.  
For example, the range `..2` is the numbers 0, 1, and 2.

::: warning

Watch out for displaying open-ended ranges like just entering `3..` into the command line. 
It will keep printing out numbers very quickly until you stop it with something like Ctr + c.

:::

## Binary data

Binary data, like the data from an image file, is a group of raw bytes.

You can write binary as a literal using any of the `0x[...]`, `0b[...]`, or `0o[...]` forms:
For example, the following are equivalent:
```nu
0x[1F FF]  # Hexadecimal
```
```nu
0b[1 1010] # Binary
```
```nu
0o[377]    # Octal
```
Each will result in following if entered in terminal

    > Length: 1 (0x1) bytes | printable whitespace ascii_other non_ascii  
    > 00000000:   ff                                                   

Incomplete bytes will be left-padded with zeros.

## Structured data

Structured data is based on the concepts of:
records - collection of keys (type string) and corresponding values (type any)
tables - collection of records, each also with a unique index (type int)
lists - collection of unique indexes (type int) and corresponding values (type any)

### Records

Records hold key:value pairs, which associate string keys with various data values.  
Record syntax is very similar to objects in JSON.  

Records frequently have many keys, so records are printed up-down.
Note, commas are _not_ required to separate key:values, if Nushell can easily distinguish them!

```nu
{name: sam rank: 10}
```
```
╭──────┬─────╮
│ name │ sam │
│ rank │ 10  │
╰──────┴─────╯
```

A record can represent a single row of a table (see below),  if the record's key names and table column names (fields) are the same.

This means that any command that can operate on a table's rows can _also_ operate on records.  

For instance, [`insert`](/commands/docs/insert.md), which adds data to table rows, can be used with records:

```nu
{name: sam rank: 10} | insert age 25
```
```
╭──────┬─────╮
│ name │ sam │
│ rank │ 10  │
│ age  │ 25  │
╰──────┴─────╯

You can also manage records by converting them into tables first.  
For example: 

```nu
{name: sam, rank: 10} | transpose key value
```
```
╭───┬──────┬───────╮
│ # │ key  │ value │
├───┼──────┼───────┤
│ 0 │ name │  sam  │
│ 1 │ rank │   10  │
╰───┴──────┴───────╯
```
Note: The index field is added automatically.

Accessing a records' data is done by placing a `.` before a string, which is usually a bare string:

```nu
{name: sam rank: 10}.rank         # > 10
```

However, if a record has a key name that can't be expressed as a bare string, 
or resembles an integer (see lists, below), you'll need to use more explicit string syntax.  
For example,  
```nu
{"1":true "2":false}."2"       # > false
```

To make a copy of a record with new fields, you can use the [spread operator](/book/operators#spread-operator) (`...`):

```nu
let data = { name: alice, age: 50 }
{ ...$data, hobby: cricket }
```
```
╭───────┬─────────╮
│ name  │ alice   │
│ age   │ 50      │
│ hobby │ cricket │
╰───────┴─────────╯
```

### Lists

Lists are ordered sequences of data values.  
List syntax is very similar to arrays in JSON.  
For example:

```nu
[bell book candle pencil] 
```
```
╭───┬────────╮
│ 0 │ sam    │
│ 1 │ fred   │
│ 2 │ george |
| 3 | pencil │
╰───┴────────╯
```
Note, commas are _not_ required to separate values if Nushell can easily distinguish them!

You can think of a list as essentially being a "one-column table" (with no column name).   
Thus, any command which can operate on a column can _also_ operate on a list.   
For instance, [`where`](/commands/docs/where.md) can be used to create a new list   
from all the items in the original list ($it), which partially match the given pattern (=~ 'b')

```nu
 [bell book candle pencil] | where ($it =~ 'b')
```
```
╭───┬──────╮
│ 0 │ bell │
│ 1 │ book │
╰───┴──────╯
```

Accessing lists' data is done by placing a `.` before a bare integer:

```nu
[a b c].1     # > b
```

To get a sub-list from a list, you can use the [`range`](/commands/docs/range.md) command:

```nu
> [a b c d e f] | range 1..3
╭───┬───╮
│ 0 │ b │
│ 1 │ c │
│ 2 │ d │
╰───┴───╯
```

To append one or more lists together, optionally with values interspersed in between,  
you can use the [spread operator](/book/operators#spread-operator) (`...`):

```nu
let x = [1 2]
[...$x 3 ...(4..7 | take 2)]
```
```
╭───┬───╮
│ 0 │ 1 │
│ 1 │ 2 │
│ 2 │ 3 │
│ 3 │ 4 │
│ 4 │ 5 │
╰───┴───╯
```

### Tables

The table is a core data structure in Nushell.  
As you run commands, you'll see that many of them return tables as output.  
A table has both rows and columns.

We can create our own tables similarly to how we create a list,  
by first providing a list of column names (type string - also known as fields),  followed by lists of data for each row.  

```nu
[[column1, column2]; [Value1, Value2] [Value3, Value4]]
```
```
╭───┬─────────┬─────────╮
│ # │ column1 │ column2 │
├───┼─────────┼─────────┤
│ 0 │ Value1  │ Value2  │
│ 1 │ Value3  │ Value4  │
╰───┴─────────┴─────────╯
```

You can also create a table as a list of records, JSON-style:

```nu
[{name: sam, rank: 10}, {name: bob, rank: 7}]
```
```
╭───┬──────┬──────╮
│ # │ name │ rank │
├───┼──────┼──────┤
│ 0 │ sam  │   10 │
│ 1 │ bob  │    7 │
╰───┴──────┴──────╯
```
Note: the record keys become the table's fields.

Note
Internally, tables are simply **lists of records**.  
This means that any command which extracts or isolates a specific row of a table will produce a record.  
For example, `get 0`, when used on a list, extracts the first value.  
But when used on a table (a list of records), it extracts a record:

```nu
[{x:12, y:5}, {x:3, y:6}] | get 0
```
```
╭───┬────╮
│ x │ 12 │
│ y │ 5  │
╰───┴────╯
```

This is true regardless of which table syntax you use:

```nu
[[x,y];[12,5],[3,6]] | get 0
```
```
╭───┬────╮
│ x │ 12 │
│ y │ 5  │
╰───┴────╯
```

### Cell Paths

You can combine list and record data access syntax to navigate tables.  
When used on tables, these access chains are called "cell paths".

You can access individual rows by number to obtain records:

@[code](@snippets/types_of_data/cell-paths.sh)

Moreover, you can also access entire columns of a table by name, to obtain lists:

```nu
[{x:12 y:5} {x:4 y:7} {x:2 y:2}].x
```
```
╭───┬────╮
│ 0 │ 12 │
│ 1 │  4 │
│ 2 │  2 │
╰───┴────╯
```

Of course, these resulting lists don't have the column names of the table.  
To choose columns from a table while leaving it as a table, you'll commonly use the [`select`](/commands/docs/select.md) command with column names:

```nu
[{x:0 y:5 z:1} {x:4 y:7 z:3} {x:2 y:2 z:0}] | select y z
```
```
╭───┬───┬───╮
│ # │ y │ z │
├───┼───┼───┤
│ 0 │ 5 │ 1 │
│ 1 │ 7 │ 3 │
│ 2 │ 2 │ 0 │
╰───┴───┴───╯
```

To get specific rows from a table, you'll commonly use the [`select`](/commands/docs/select.md) command with row numbers, as you would with a list:

```nu
[{x:0 y:5 z:1} {x:4 y:7 z:3} {x:2 y:2 z:0}] | select 1 2
```
```
╭───┬───┬───┬───╮
│ # │ x │ y │ z │
├───┼───┼───┼───┤
│ 0 │ 4 │ 7 │ 3 │
│ 1 │ 2 │ 2 │ 0 │
╰───┴───┴───┴───╯
```

#### Optional cell paths

By default, cell path access will fail if it can't access the requested row or column.  
To suppress these errors, you can add `?` to a cell path member to mark it as _optional_:

```nu
[{foo: 123}, {}].foo?
```
```
╭───┬─────╮
│ 0 │ 123 │
│ 1 │     │
╰───┴─────╯
```

When using optional cell path members, missing data is replaced with `null`.

## Closures

Closures are anonymous functions (also known in other universes as lambdas or callbacks)  
that can be passed a value through parameters and _close over_ (i.e. use) a variable outside their scope.

For example, in the command `each { |it| print $it }` the closure is the portion contained in curly braces, `{ |it| print $it }`.  
Closure parameters are specified between a pair of pipe symbols (for example, `|it|`) if necessary.  
You can also use a pipeline input as `$in` in most closures instead of providing an explicit parameter: `each { print $in }`

Closures itself can be bound to a named variable and passed as a parameter.  
To call a closure directly in your code use the [`do`](/commands/docs/do.md) command.

```nu
# Assign a closure to a variable
let greet = { |name| print $"Hello ($name)"}
do $greet "Julian"
```

Closures are a useful way to represent code that can be executed on each row of data.  
It is idiomatic (customary) to use `$it` as a parameter name in [`each`](/commands/docs/each.md) blocks, but not required;

`each { |x| print $x }` works the same way as `each { |it| print $it }`

## Blocks

Blocks don't close over variables, don't have parameters, and can't be passed as a value.  
However, unlike closures, blocks can access mutable variable in the parent closure.  
For example, mutating a variable inside the block used in an [`if`](/commands/docs/if.md) call is valid:

```nu
mut x = 1
if true {
    $x += 1000
}
print $x        # > 1001
```

## Null

Finally, there is `null` which is the language's "nothing" value, similar to JSON's "null".  
Whenever Nushell would print the `null` value (outside of a string or data structure), it prints nothing instead.  
Hence, most of Nushell's file system commands (like [`save`](/commands/docs/save.md) or [`cd`](/commands/docs/cd.md)) produce `null`.

You can place `null` at the end of a pipeline to replace the pipeline's output with it, and thus print nothing:

```nu
git checkout featurebranch | null
```

:::warning

`null` is not the same as the absence of a value!  
It is possible for a table to be produced that has holes in some of its rows.  
Attempting to access this value will not produce `null`, but instead cause an error:

Example: create a table with null in 1.a
```nu
let demo = [{a:1 b:2} {b:1}]
```
```
╭───┬────┬───╮
│ # │ a  │ b │
├───┼────┼───┤
│ 0 │  1 │ 2 │
│ 1 │ ❎ │ 1 │
╰───┴────┴───╯
```
Then try to access 1.a
```
$demo.1.a
```
```
Error: nu::shell::column_not_found

  × Cannot find column
   ╭─[entry #15:1:1]
 1 │ [{a:1 b:2} {b:1}].1.a
   ·            ──┬──    ┬
   ·              │      ╰── cannot find column
   ·              ╰── value originates here
   ╰────
```

If you would prefer this to return `null`, mark the cell path member as _optional_ like `.1.a?`.

The absence of a value is (as of Nushell 0.71) printed as the ❎ emoji in interactive output.

