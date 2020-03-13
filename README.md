# Erlang style guide

A biased guide on how to write Erlang code that looks nice and is nice to read.
It's based on the *official* erlang-mode from OTP where applicable.

Each rule is presented together with a reason why. Most rules also have
examples. Some rules have counter-examples showing what not to do.

Some rules are never to be broken, as they are decided by erlang-mode. If those
rules are broken, indenting an entire file will cause unwanted changes.
However, some of the rules are up to the programmer to follow as they are not
in scope of `erlang-mode`'s indentation or other. Pragmatism is always number 1,
but you need to know and follow the rules in order to know when it's OK to break
them.

# Indentation

## Use spaces, not tabs.
Tabs are rendered different in different editors, or with different editor
settings. Spaces are not. Use spaces so that *everyone* gets the code rendered
the same way.

If you need to use spaces in strings, for example to test the output or input
of a program, use escape characters, not literal tabs.

## Indent with four spaces
Four spaces for each level of indentation (except for `fun` bodies).
It's a good number of spaces that makes sure the code isn't too compact.
Problems with lines that are too long usually have other problems (too much
nesting, too many input arguments, variable naming that doesn't match the scope)
that should be solved in different ways than reducing indentation.

## Indent `fun` bodies with eight spaces
`fun` bodies are indented with eight spaces as per `erlang-mode` standard.
This means that you should do this:
```erlang
f() ->
    fun() ->
            do_some_stuff(),
            do_some_more(),
            eight_spaces
    end.
```

And this:
```erlang
g() ->
    fun(X, Y, Z) ->
            X * Y / Z
    end.
```

And this:
```erlang
h() ->
    fun Fact(0) ->
            1;
        Fact(N) ->
            N * Fact(N - 1)
    end.
```

## Indent binary operators on a new line
Sometimes it makes sense to split a binary operator and its arguments into
two lines. If a line starts with a binary operator, indent once.

```erlang
f() ->
    it_is_like_this()
        andalso it_is_like_that().
```

## Indent the second argument of a binary operator
Similar to the rule above, if the previous line ended with a binary operator,
indent once.

```erlang
f() ->
    it_is_like_this() andalso
        it_is_like_that().
```

## Chaining of binary operators in the same statement is indented once
If several binary operators are chained together in the same statement,
the operators do not need to be indented in a nested fashion. The first line
should be at the same indentation level as the surrounding code, and all lines
after it should be indented once.

```erlang
f() ->
    some_thing() andalso
        some_other_thing() andalso
        a_third_thing()
        andalso a_fourth_one() andalso number_five()
        andalso
        last_one().
```

## Indent lists and tuples on the first character
If a list or a tuple is split into several lines, all lines after the first
are left-adjusted to start at the same place as the first element of the
list or tuple.

```erlang
f() ->
    {some_element,
     another_element,
     [a,
      list]}.
```

# Writing code that's easy on the eyes

## Use spaces before and after operators.
Don't try to compress code by removing spaces before and after operators.
The code should, as far as possible, read like a book. Don't try to compress
the text to save a couple of characters per line - instead make it easy to parse
the code, by not making it look dense.

Do:
```erlang
f() ->
    Number = a() + b(),
    (Number == 3) orelse (g() < 5).
```

Don't do this:
```erlang
f() ->
Number=a()+b(),
   (Number==3)orelse(g()<5).
```

## Space after comma
If there is more code on the line, put a space after a comma.
```erlang
{[1, 2, 3], [4, 5, 6]}
```

## Put a space before and after `->`
Put a space before `->` in all function heads and case clauses.
In case the function or case clause is a one-liner, put a space after it too.

```erlang
f(a) -> "one";
f(b) -> "two".
```

```erlang
f(X) ->
    case X of
        a -> "one";
        b -> "two"
    end.
```

## Only indent on code level
Don't do your own indentation in the middle of the line to try to match it up
with some other lines of code above or below. That means, don't add extra spaces
before or after commas, equal signs, etc. This adds more noise than readability
due to the fact that the assigned variable is further away from its value, the
visual cue for which line you are looking at gets reduced.

Another reason is that while this always starts with good intentions of making
the code look better, in practice the 50 mutually indented lines are almost
never updated when the fifty-first line that needs one or two more spaces than
the rest enters the code, and then it just looks very messy instead.

Do this:
```erlang
Apples = 5,
Pears = 2,
Bananas = 10,
Pommegranates = 7.
```

Don't do this:
```erlang
Apples        = 5,
Pears         = 2,
Bananas       = 10,
Pommegranates = 7.
```

## Make it look like actual text
We want the code to be easy to read. Make the code look like actual text.
This is a catch-all rule to avoid madness such as lines starting with commas.
It throws the reader off, and doesn't give any benefit that a modern editor
couldn't solve in a better way with *nice* syntax.

# Naming
## Use snake_cased atoms
By convention, use `snake_cased` atoms unless they are single-quoted.

Do this:
```erlang
atom_one,
'3GPP',
'A quoted atom'.
```

Don't do this:
```erlang
atomOne,
atomTwo,
threeGPP.
```

## snake_case all the things that are atoms
Function names and module names are atoms and therefore follow the same
rules as atoms above. Do not use quoted atoms for these.

## Use Snake_cased variable names
Variable names should be Snake_cased. They can of course also be abbreviations
in all-caps.

Do this:
```erlang
Some_variable = f(),
GCD = greatest_common_divisor(123, Some_variable).
```

## Use `?ALL_CAPS` macro names for constants
If a macro is a constant, give it a name in all caps.
```erlang
-define(SPEED_OF_LIGHT, 299792458).
```

## Don't name ignored variables
If a variable is to be ignored, it should have the name `_`. No more, no less.
If you follow this rule, the problem of using ignored variables doesn't exist.

### It's indicative of a code structuring problem
If you need to give a variable a name like `_Parameters` in order for the code
to make sense, there is a deeper problem there.

Name the variable like this:
```erlang
f(Ordinal) ->
    case Ordinal of
        first -> 1;
        second -> 2;
        _ -> {error, unknown_ordinal}
    end.
```

Don't write it in such a way that enforces a `_Name` style variable in order to
not make it confusing.
```erlang
f(first) -> 1;
f(second) -> 2;
f(_Ordinal) -> {error, unknown_ordinal}.
```
Those are obviously toy examples but you can still guess how fast it can get
much more complicated than having to guess what `first` and `second` have in
common and are supposed to represent.

If multiple clauses are used, it should be obvious from earlier clauses what's
inside each input argument without them needing `_Name` style names.
```erlang
f(Height, Length, Width) when Height > Width andalso Width > Length ->
   Height + Width * Length;
f(_, Length, Width) ->
   Length * Width.
```

If you need to do a lot of scrolling up and down, and compare lots of different
function clauses in order to get all of the data, then you have a deeper problem
that cannot and should not be solved by variable naming.

### They get assigned values
It is *possible* to do this even though you definitely shouldn't.
```erlang
_A = 1,
_B = 5,
_A + _B.
```

In other words, `_Name` style variables get a value assigned to them.
Unused `_Name` style variables might get removed by the compiler, I'm not
entirely sure, but it might lead to other bad practices such as *using*
ignored variables, for example in macros that only evaluate the variable
in case certain compiler flags are set, etc.

# Data types
## Lists
### Don't use lists for collections of fixed sizes
If the size of a collection of elements is known, use a tuple, map, record or
some other suitable data type.
Lists are for collections of variable sizes and can make the reader of the
code do a double-take, or trying in vain to find in which cases the list has
other sizes.

### Append elements to the head of a list
Build a list from the head. The way lists are defined in Erlang and indeed many
other functional languages, it's optimal to add new list elements to the
beginning of the list, rather than the end of it.
Every time a new element is appended to the end of a list, the entire list must
be rebuilt. This is especially important when working with strings, which are
disguised lists. It's tempting to build a string starting with the first word
and ending with the last.

Use different approaches instead, such as using `lists:reverse/1` after
the list has been completed. If building a list, try building line by line
and reversing the order of the lines at the end. Using `io_lib:format/2` is
also a great option.

`lists:reverse/1` is a native function and therefore very fast, so don't be
afraid to use it.

### Append to the head of a list with the cons operator `|`
Use the cons operator for clean code without noise. This mimics the behaviour
of how lists are actually treated by the Erlang virtual machine, and encourages
adding new elements to the *head* of the list rather than the tail.

Put new elements into a list like this:
```erlang
-spec f([any()]) -> [any()].
f(Some_stuff) ->
    E1 = new_element(),
    E2 = another_new_element(),
    [E1, E2 | Some_stuff].
```
Don't do this:
```erlang
-spec f([any()]) -> [any()].
f(Some_stuff) ->
    E1 = new_element(),
    E2 = another_new_element(),
    [E1, E2] ++ Some_stuff.
```
Absolutely don't do this:
```erlang
[E1] ++ [E2] ++ Some_stuff.
```
The same applies for any *set* number of elements appended to a list.

### Use the relevant modules to handle file names
Use `filename`, `filelib` and `file` when handling files and file names.
Don't concatenate file paths with `++`. This requires knowledge about the
value stored in the variable describing each part of the path - for example,
was there a / at the end of the string or does one need to be added?
This is all handled automatically by the `filename` module.
It also ensures that paths are not OS-dependent.

## Tuples
### Don't use tuples of size one
Tuples are for a collection of elements. There is absolutely no reason to put
a single element into a tuple. Pattern matching should be solved in other ways
than by separating a concept into a single element or a single element in a
tuple, for example by using a tuple of size two with the match done on an atom.

## Strings
### Use multi-line strings to avoid long lines
It's possible to break up a single string into several lines, by ending one
line with a double quote and beginning the next line with one. Do this to avoid
exceeding the maximum character length of a line for long strings.
Example:
```erlang
help_text_format() ->
    "How to use this script~n"
        "  Input parameters:~n"
        "    Iterations: How many times the test should be run~n"
        "    Granularity: Defines the smallest time unit to measure~n".
```
Do note that multi-line strings are to be indented one level beyond the first
line, as per OTP standard.

# Function style
## Don't do extractions in the function head
Deconstructing data types in the function header is for pattern matching, it is
not a good place to extract data. Data is supposed to be created/extracted as
close to where it is used as possible. A function head is not the place to
extract the 40 elements from nested records that some case clauses may or may
not need, especially not when *some* of those are used for pattern matching.

It makes it near impossible for the reader of the code to know which ones are
for pattern matching and which ones are just extractions if the function heads
aren't very, very tiny.

Be pragmatic though. Don't spend five lines to extract data from a trivial
data structure in some trivial one-liner function. If there is only one or two
un-nested data structures as input parameters, extracting in the function head
might be much easier to read than adding several lines just for extraction.

## Return the last statement directly
The last evaluated statement is always returned. There is no need to assign to a
variable just to return the same variable on the next line.

Do this:
```erlang
Test = fun first_test/0,
run_test_case(Test).
```

Don't do this:
```erlang
Test = fun first_test/0,
Result = run_test_case(Test),
Result.
```

# Testing
## One assertion per test
One test tests one thing. It's fine to have checks for example for setups,
since the test should crash as early as possible. But test one and only one
scenario per test. This way, it is not necessary to perform any manual analysis
to be able to see which functionality is failing. It will also not hide
the errors of functionalities that are tested before the first assertion, thus
requiring several iterations to fix several failing assertions.
