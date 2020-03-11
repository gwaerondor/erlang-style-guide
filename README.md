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
