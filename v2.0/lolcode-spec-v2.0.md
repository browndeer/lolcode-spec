# LOLCODE Specification 2.0

WORKING DRAFT 31 March 2017

Provisional specification for community input before formal ratification.

This is a working specification for LOLCODE version 2.0 and is based on the
original version 1.2 specification (a work of genius).  The goals for the
proposed changes introduce a parallel programming model, replace dynamic 
typing with static typing, add proper support for arrays, and add additional
language features.

---

## Formatting

### Whitespace

* Spaces are used to demarcate tokens in the language, although some keyword
  constructs may include spaces.

* Multiple spaces and tabs are treated as single spaces and are otherwise
  irrelevant.

* Indentation is irrelevant.

* A command starts at the beginning of a line and a newline indicates the end
  of a command, except in special cases.

* A newline will be Carriage Return (/13), a Line Feed (/10) or both (/13/10)
  depending on the implementing system. This is only in regards to LOLCODE 
  code itself, and does not indicate how these should be treated in strings or 
  files during execution.

* Multiple commands can be put on a single line if they are separated by a 
  comma (,). In this case, the comma acts as a virtual newline or a 
  soft-command-break.

* Multiple lines can be combined into a single command by including three 
  periods (...) at the end of the line. This causes the contents of the next 
  line to be evaluated as if it were on the same line.

* Lines with line continuation can be strung together, many in a row, to allow 
  a single command to stretch over more than one or two lines. As long as each 
  line is ended with three periods, the next line is included, until a line 
  without three periods is reached, at which point, the entire command may be 
  processed.

* A line with line continuation may not be followed by an empty line.
  Three periods may be by themselves on a single line, in which case, the empty
  line is "included" in the command (doing nothing), and the next line is
  included as well.

* A single-line comment is always terminated by a newline. Line continuation 
  (...) and soft-command-breaks (,) after the comment (`BTW`) are ignored.

* Line continuation and soft-command-breaks are ignored inside quoted strings.
  An unterminated string literal (no closing quote) will cause an error.

### Comments

*(from 1.1)*

Single line comments are begun by `BTW`, and may occur either after a line of
code, on a separate line, or following a line of code following a line
separator (,).

All of these are valid single line comments:

```
I HAS A VAR ITZ 12          BTW VAR = 12

I HAS A VAR ITZ 12,         BTW VAR = 12

I HAS A VAR ITZ 12
                BTW VAR = 12
```

Multi-line comments are begun by `OBTW` and ended with `TLDR`, and should be
started on their own lines, or following a line of code after a line separator.

These are valid multi-line comments:

```
I HAS A VAR ITZ 12
            OBTW this is a long comment block
                 see, i have more comments here
                 and here
            TLDR
I HAS A FISH ITZ BOB
```

```
I HAS A VAR ITZ 12,  OBTW this is a long comment block
      see, i have more comments here
      and here
TLDR, I HAS A FISH ITZ BOB
```

### File Creation

All LOLCODE programs must be opened with the command `HAI`. `HAI` should then
be followed with the current LOLCODE language version number (1.2, in this
case). There is no current standard behavior for implementations to treat the
version number, though.

A LOLCODE file is closed by the keyword `KTHXBYE` which closes the `HAI`
code-block.

---

## Variables

### Scope

*(to be revisited and refined)*

All variable scope, as of this version, is local to the enclosing function or to the main program block. Variables are only accessible after declaration, and there is no global scope.

### Naming

*(from 1.1)*

Variable identifiers may be in all lowercase or uppercase letters (or a mixture of the two). They must begin with a letter and may be followed only by other letters, numbers, and underscores. No spaces, dashes, or other symbols are allowed. Variable identifiers are CASE SENSITIVE – "cheezburger", "CheezBurger" and "CHEEZBURGER" would all be different variables.

### Declaration and Assignment

*(modified from 1.2)*

Variables mut be declared prior to use.  Local variable declarations take the form, `I HAS A <variable> ITZ A <type>`.  This may
be followed by one or more clauses separated with `AN` to provide initialization as well as other qualifiers.  
If no initialization is provided the variable is initialized with 0 cast to the correct type.  
The following are examples of variable declarations.  (Types are discussed more fully in the next section.)

```
I HAS A x ITZ A NUMBR
	BTW declares x as integer
I HAS A y ITZ A NUMBAR AN ITZ 5.6     
	BTW declares y as float with initial value of 5.6
```

The declared type of the variable is static and cannot be changed.

---

## Types

*(updated from 1.2)*

The intrinsic variable types are: strings (YARN), integers (NUMBR), floats
(NUMBAR), and booleans (TROOF).

Variables are statically typed and the type of a variable cannot change.  (An
exception to this is the implied `IT` variable used with
expression-statements.)
 
### Booleans

The two boolean (TROOF) values are WIN (true) and FAIL (false). The empty
string (""), an empty array, and numerical zero are all cast to FAIL. All other
values evaluate to WIN.

### Numerical Types

A NUMBR is an integer as specified in the host implementation/architecture. Any
contiguous sequence of digits outside of a quoted YARN and not containing a
decimal point (.) is considered a NUMBR. A NUMBR may have a leading hyphen (-)
to signify a negative number.

A NUMBAR is a float as specified in the host implementation/architecture. It is
represented as a contiguous string of digits containing exactly one decimal
point. Casting a NUMBAR to a NUMBR truncates the decimal portion of the
floating point number. A NUMBAR may have a leading hyphen (-) to signify a
negative number.

Casting of a string to a numerical type parses the string as if it were not in
quotes. If there are any non-numerical, non-hyphen, non-period characters, then
it results in an error. Casting WIN to a numerical type results in "1" or
"1.0"; casting FAIL results in a numerical zero.

### Strings

String literals (YARN) are demarked with double quotation marks ("). Line
continuation and soft-command-breaks are ignored inside quoted strings. An
unterminated string literal (no closing quote) will cause an error.

Within a string, all characters represent their literal value except the colon
(:), which is the escape character. Characters immediately following the colon
also take on a special meaning.

* :) represents a newline (\n)
* :> represents a tab (\t)
* :o represents a bell (beep) (\g)
* :" represents a literal double quote (")
* :: represents a single literal colon (:)

The colon may also introduce more verbose escapes enclosed within some form of
bracket.

* :{`<var>`} interpolates the current value of the enclosed variable, cast as a
  string.

### Arrays

*(updated from 1.2)*

Arrays may be declared using the typing clause `ITZ LOTZ A
<type>S` and size allocation clause `AN THAR IZ <size>`.  For example,

```
I HAS A x ITZ LOTZ A NUMBRS AN THAR IZ 500
I HAS A y ITZ LOTZ A NUMBARS AN THAR IZ n
```

Here x is declared as an array of 500 integers, and y is declared an array
of floats of size n.

Array elements are accessed with the `'Z` operator that takes the form
`<array>'Z <index>`.  Array indexing begins with 0 like a proper programming
language.  For example,

```
I HAS A x ITZ LOTZ A NUMBRS AN THAR IZ 20
x'Z 5 R 6         BTW assign array element 5 the value 6
x'Z n R y         BTW assign array element n the value y
```

Assignment between arrays of the same size is allowed, and assignment between
arrays of different types implies the appropriate element-wise cast.  For
example,

```
I HAS A x ITZ LOTZ A NUMBRS AN THAR IZ 20
I HAS A y ITZ LOTZ A NUMBRS AN THAR IZ 20
I HAS A z ITZ LOTZ A NUMBARS AN THAR IZ 20
x R y      BTW assign all elements of y to x
x R z      BTW assign all elements of z to x cast as integers
```

### Structs and Derived Types

*Structs for derived types should use the concept of a `BUKKIT` and the
operator `'Z` for member access.  The syntax is under development at this
time.*

---

## Operators

### Calling Syntax and Precedence

Mathematical operators and functions in general rely on prefix notation. By
doing this, it is possible to call and compose operations with a minimum of
explicit grouping. When all operators and functions have known arity, no
grouping markers are necessary. In cases where operators have variable arity,
the operation is closed with `MKAY`. 

Calling unary operators then has the following syntax:

```
<operator> <expression1>
```

The `AN` keyword can optionally be used to separate arguments, 
so a binary operator expression has the following syntax:

```
<operator> <expression1> [AN] <expression2>
```

An expression containing an operator with infinite arity can then be expressed
with the following syntax:

```
<operator> <expr1> [[[AN] <expr2>] [AN] <expr3> ...] MKAY
```

### Math Operators

The basic math operators are unary or binary prefix operators.

```
SUM OF <x> AN <y>       BTW +
DIFF OF <x> AN <y>      BTW -
PRODUKT OF <x> AN <y>   BTW *
QUOSHUNT OF <x> AN <y>  BTW /
MOD OF <x> AN <y>       BTW modulo
BIGGR OF <x> AN <y>     BTW max
SMALLR OF <x> AN <y>    BTW min
FLIP Of <x>             BTW 1/x
SQUAR OF <x>            BTW x*x
UNSQUAR OF <x>          BTW sqrt(x)
POWR OF <x> AN <y>      BTW pow(x,y)
ROOT OF <x> AN <y>      BTW pow(x,1/y)
```

`<x>` and `<y>` may each be expressions in the above, so mathematical operators
can be nested and grouped indefinitely.

Math is performed as integer math in the presence of two NUMBRs, but if either
of the expressions are NUMBARs, then floating point math takes over.

If one or both arguments are a YARN, they get interpreted as NUMBARs if the
YARN has a decimal point, and NUMBRs otherwise, then execution proceeds as
above.

If one or another of the arguments cannot be safely cast to a numerical type,
then it fails with an error.

### Boolean Operators

Boolean operators working on TROOFs are as follows:

```
BOTH OF <x> [AN] <y>          BTW and: WIN iff x=WIN, y=WIN
EITHER OF <x> [AN] <y>        BTW or: FAIL iff x=FAIL, y=FAIL
WON OF <x> [AN] <y>           BTW xor: FAIL if x=y
NOT <x>                       BTW unary negation: WIN if x=FAIL
ALL OF <x> [AN] <y> ... MKAY  BTW infinite arity AND
ANY OF <x> [AN] <y> ... MKAY  BTW infinite arity OR
```

`<x>` and `<y>` in the expression syntaxes above are automatically cast as TROOF values if they are not already so.

### Comparison Operators

Comparison is (currently) done with two binary equality operators:

```
BOTH SAEM <x> AN <y>   		BTW WIN if x == y
DIFFRINT <x> AN <y>    		BTW WIN if x != y
FURST SMALLR <x> AN <y>		BTW WIN if x < y
FURST BIGGR  <x> AN <y>		BTW WIN if x > y
```

Comparisons are performed as integer math in the presence of two NUMBRs, but if
either of the expressions are NUMBARs, then floating point math takes over.
Otherwise, there is no automatic casting in the equality, so `BOTH SAEM "3" AN
3` is FAIL.

### Concatenation Operator

An indefinite number of YARNs may be explicitly concatenated with the
`SMOOSH...MKAY` operator. Arguments may optionally be separated with `AN`. As
the `SMOOSH` expects strings as its input arguments, it will implicitly cast
all input values of other types to YARNs. The line ending may safely implicitly
close the `SMOOSH` operator without needing an `MKAY`.

### Casting

Operators that work on specific types implicitly cast parameter values of other
types. If the value cannot be safely cast, then it results in an error.

An expression's value may be explicitly cast with the binary `MAEK` operator.

```
MAEK <expression> [A] <type>
```

Where `<type>` is one of TROOF, YARN, NUMBR, NUMBAR, or NOOB. This is only for
local casting: only the resultant value is cast, not the underlying
variable(s), if any.

### Random Numbers

To generate integer or floating point random numbers, the keywords
`WHATEVR` and `WHATEVAR`, respectively, may be evaluated as expressions.
For example,

```
<var> R WHATEVR          BTW <var> is assigned a random integer
<var> R WHATEVAR         BTW <var> is assigned a random float
```

---

## Input/Output

### Terminal-Based

The print (to STDOUT or the terminal) operator is `VISIBLE`. It has infinite
arity and implicitly concatenates all of its arguments after casting them to
YARNs. 
It is terminated by the statement delimiter (line end or comma).
The output is automatically terminated with a carriage return (:)),
unless the final token is terminated with an exclamation point (!),
in which case the carriage return is suppressed.

```
VISIBLE <expression> [<expression> ...][!]
```

The print operators `RTM` and `RTFM` behave similarly but will print to
STDERR, and the latter will also cause the program to exit with an error
after printing.

```
RTM "something went wrong but I will continue"
RTFM "something went very wrong and the program is exiting"
```

There is currently no defined standard for printing to a file.

To accept input from the user, the keyword is

```
GIMMEH <variable>
```

which takes YARN for input and stores the value in the given variable.

*`GIMMEH` is defined minimally here as a holdover from 1.0 and because there has not been any detailed discussion of this feature. We count on the liberal casting capabilities of the language and programmer inventiveness to handle input restriction. `GIMMEH` may change in a future version.*

---

## Statements

### Expression Statements

A bare expression (e.g. a function call or math operation), without any assignment, is a legal statement in LOLCODE. Aside from any side-effects from the expression when evaluated, the final value is placed in the temporary variable `IT`. `IT`'s value remains in local scope and exists until the next time it is replaced with a bare expression.

### Assignment Statements

Assignment of a variable is accomplished with an assignment statement,
`<variable> R <expression>`

```
I HAS A x ITZ A YARN            BTW declare x to be a string
x R "THREE"                     BTW x now equals "THREE"
I HAS A y ITZ A NUMBAR          BTW declare y to be a float
y R 3.14                        BTW y now equals 3.14
```

Additional assignment operators can be used to modify a variable, and take the
form,

```
<operator> <variable> [BY <expression>]
```

The following are examples of assignment operators,

```
UPPIN x          BTW increment x by 1
NERFIN x         BTW decrement x by 1
UPPIN x BY 6     BTW increment x by 6
NERFIN x BY y    BTW decrement x by y
```

Assignment statements have no side effects with `IT`. They are generally of the
form:

### Flow Control Statements

Flow control statements cover multiple lines and are described in the following section.

---

## Flow Control

### Conditionals

#### If-Then

The traditional if/then construct is a very simple construct operating on the implicit `IT` variable. In the base form, there are four keywords: `O RLY?`, `YA RLY`, `NO WAI`, and `OIC`.

`O RLY?` branches to the block begun with `YA RLY` if `IT` can be cast to WIN, and branches to the `NO WAI` block if `IT` is FAIL. The code block introduced with `YA RLY` is implicitly closed when `NO WAI` is reached. The `NO WAI` block is closed with `OIC`. The general form is then as follows:

```
<expression>
O RLY?
  YA RLY
    <code block>
  NO WAI
    <code block>
OIC
```

while an example showing the ability to put multiple statements on a line separated by a comma would be:

```
BOTH SAEM ANIMAL AN "CAT", O RLY?
  YA RLY, VISIBLE "J00 HAV A CAT"
  NO WAI, VISIBLE "J00 SUX"
OIC
```

The elseif construction adds a little bit of complexity. Optional `MEBBE <expression>` blocks may appear between the YA RLY and NO WAI blocks. If the `<expression>` following `MEBBE` is WIN, then that block is performed; if not, the block is skipped until the following `MEBBE`, `NO WAI`, or `OIC`. The full expression syntax is then as follows:

```
<expression>
O RLY?
  YA RLY
    <code block>
 [MEBBE <expression>
    <code block>
 [MEBBE <expression>
    <code block>
  ...]]
 [NO WAI
    <code block>]
OIC
```

An example of this conditional is then:

```
BOTH SAEM ANIMAL AN "CAT"
O RLY?
  YA RLY, VISIBLE "J00 HAV A CAT"
  MEBBE BOTH SAEM ANIMAL AN "MAUS"
    VISIBLE "NOM NOM NOM. I EATED IT."
OIC
```

#### Case

*(modified from 1.1)*

The LOLCODE keyword for switches is `WTF?`. The `WTF?` operates on `IT` as being the expression value for comparison. A comparison block is opened by `OMG` and must be a literal, not an expression. (A literal, in this case, excludes any YARN containing variable interpolation (`:{var}`).) Each literal must be unique. The `OMG` block can be followed by any number of statements and may be terminated by a `GTFO`, which breaks to the end of the the `WTF` statement. If an `OMG` block is not terminated by a `GTFO`, then the next `OMG` block is executed as is the next until a `GTFO` or the end of the `WTF` block is reached. The optional default case, if none of the literals evaluate as true, is signified by `OMGWTF`.

```
WTF?
  OMG <value literal>
    <code block>
 [OMG <value literal>
    <code block> ...]
 [OMGWTF
    <code block>]
OIC
```

```
COLOR, WTF?
  OMG "R"
    VISIBLE "RED FISH"
    GTFO
  OMG "Y"
    VISIBLE "YELLOW FISH"
  OMG "G"
  OMG "B"
    VISIBLE "FISH HAS A FLAVOR"
    GTFO
  OMGWTF
    VISIBLE "FISH IS TRANSPARENT"
OIC
```

In this example, the output results of evaluating the variable `COLOR` would be:

"R":

```
RED FISH
```

"Y":

```
YELLOW FISH
FISH HAS A FLAVOR
```

"G":

```
FISH HAS A FLAVOR
```

"B":

```
FISH HAS A FLAVOR
```

none of the above:

```
FISH IS TRANSPARENT
```

#### Loops

*(modified from 1.2)*

Simple loops are demarcated with the keyword `IM IN YR LOOP` and 
`IM OUTTA YR LOOP`. 
Loops defined this way are infinite loops that must be explicitly
exited with a GTFO break.  Below is an example of a simple loop.

```
IM IN YR LOOP
  <code-block>
  <expression>, O RLY?
  YA RLY,
      GTFO
  OIC
IM OUTTA YR LOOP
```

Conditional loops require the clause `TIL <expression>` or `WILE <expression>`
and take the form below,

```
IM IN YR LOOP [TIL|WILE <expression>]
  <code-block>
IM OUTTA YR <label>
```

The `TIL <expression>` evaluates the expression as a TROOF: if it evaluates as
FAIL, the loop continues once more, if not, then loop execution stops, and
continues after the matching IM OUTTA YR LOOP. The `WILE <expression>` is the
converse: if the expression is WIN, execution continues, otherwise the loop
exits.

Iteration loops include clauses for defining the iteration, and take the form,

```
IM IN YR LOOP <operation> <variable> [FRUM <expression>] [BY <expression>] TIL|WILE <expression>
  <code-block>
IM OUTTA YR LOOP
```

Where *operation* may be UPPIN (increment) or NERFIN (decrement)
or any other assignment operator, 
the `FRUM` clause defines the intial value of *variable*, 
and the `BY` clause provides the argument to the assignment operator.

The iteration variabe is temporary, and local to the loop. 

As an example, the code below will count backwards from 10 to 4 by 2,

```
IM IN YR LOOP NERFIN i FRUM 10 BY 2 TIL BOTH SAEM i AN 2
  VISIBLE i
IM OUTTA YR LOOP
```

---

## Functions

### Definition

A function is demarked with the opening keyword `HOW IZ I` and the closing
keyword `IF U SAY SO`. The syntax is as follows:

```
HOW IZ I <function name> [YR <argument1> [AN YR <argument2> …]]
  <code block>
IF U SAY SO
```

Currently, the number of arguments in a function can only be defined as a fixed
number. The `<argument>`s are single-word identifiers that act as variables
within the scope of the function's code. The calling parameters' values are
then the initial values for the variables within the function's code block when
the function is called.

Function arguments are automatically statically typed based on the type of
the expression passed as an argument.  As an example, the following code
is valid and complies with the static typing of the language.

```
HOW IZ I FOO YR x AN YR y AN YR z MKAY
  <code-block> 
IF U SAY SO

I HAS A x ITZ A NUMBR
I HAS A y ITZ A NUMBAR
I HAS A z ITZ A NUMBAR

I IZ FOO YR x AN YR y AN YR z
I IZ FOO YR y AN YR z AN YR x
I IZ FOO YR z AN YR x AN YR y
```

### Returning

Return from the function is accomplished in one of the following ways:

* `FOUND YR <expression>` returns the value of the expression.
* `GTFO` returns with no value (NOOB).
* in the absence of any explicit break, when the end of the code block is
  reached (`IF U SAY SO`), the value in `IT` is returned.

The return type of the function is automatically determined by the type of
the expression returned.

### Calling

A function of given arity is called with:

```
I IZ <function name> [YR <expression1> [AN YR <expression2> [AN YR <expression3> ...]]] MKAY
```

That is, an expression is formed by the function name followed by any
arguments. Those arguments may themselves be expressions. The expressions'
values are obtained before the function is called. The arity of the functions
is determined in the definition.

---

## Parallel Extensions

A parallel programming model is supported with simple syntax and semantics
based on single program multiple data (SPMD) execution and a parallel global
address space (PGAS).  Support for symmetric variables, remote memory access,
and locking and synchronization are provided.

### Single Program Multiple Data (SPMD) Execution

A parallel LOLCODE program may be executed on one or more processing 
elements (PEs) refered to as friends.  The expression `MAH FRENZ` will
evaluate to an integer value representing the number of PEs executing the
program.  The expression `ME` will evaluate to a unique integer value
representing the executing PE within the enumerated set of PEs such that
`0 <= ME < MAH FRENZ`

### Symmetric Shared Variables

Symmetric variables are created for each executing program across all PEs
such that they may be accessed by any remote PE.  The declaration of
symmetric variables, as opposed to ordinary local variables, is distinguished
with the keyword `WE HAS A` and supports all of the declaration clauses
described above.  The following are examples of symetric variable declaration.

```
WE HAS A <x> ITZ A NUMBR
WE HAS A <y> ITZ A NUMBAR AN ITZ 4.5
WE HAS A <z> ITZ LOTZ A NUMBARS AN THAR IZ <n>
```

Here *x*, *y* and *z* are a symmetric integer variable, a symmetric floating
point variable initialized to 4.5, and a floating point array of size *n*,
respectively.  In each case the local values on a given PE may be accessed
remotely by other PEs.

### Remote Predication

One-sided Communication between two PEs is setup using remote predication
whereby the remote PE that is to be the target for communication is dynamically
set and unset using the keywords `TXT MAH BFF` and `TTYL`.  There are two
forms of remote predication.  The first predicates a single statement and
takes the form,

```
TXT MAH BFF <k>, <statement>
```

In this case, all references to a remote PE within *statement* will refer
to PE *k*, and following the execution of statement remote predication ends.

The second form predicates multiple statements and takes the form,

```
TXT MAH BFF <k> AN STUFF,
<code-block>
TTYL
```

In this case, all references to a remote PE within each statement in
*code-block* will refer to PE *k*.  Remote predication ends following the
closing statement `TTYL`.

Symmetric variables belonging to a remote PE are referenced using the 
prefix operator `UR`.  In the example below, the value of *x* is updated
with the sum of *y* on the executing PE and the value of *z* on remote PE *k*.
The prefix operator `UR` may only be applied to symmetric variables.

```
TXT MAH BFF <k>, <x> R SUM OF <y> AN UR <z>
```

Remote predication may be applied to statements of any complexity and include
multiple references to remote variables, for example,

```
TXT MAH BFF <k>, UR <x> R PRODUKT OF UR <c> AN SUM OF <y> AN UR <z>
```

Here the variable *x* on remote PE *k* is assigned the sum of *y* and the
remote value of *z*, with the product multiplied by the remote value of *c*.

It is optional to refer to a local variable with the prefix operator `MAH`
so that expressions may read more clearly, for example,

```
TXT MAH BFF <k>, MAH <x> R UR <y> AN MAH <z>
```

### Variable locks

Safe access to symmetric variables is supported with implicit fine-grained
locks for variables intended to be shared between PEs.  Symmetric variables
must be declared as sharable using the declaration clause `IM SHARIN IT`.  For
example, in the code below x is declared as a symmetric variable and it is also
declared as sharable, implicitly providing a lock to protect it.

```
WE HAS A <x> ITZ A NUMBR AN IM SHARIN IT
```

The executing PE may then lock a variable using either of the keywords
`IM MESIN WIF` or `IM SRSLY MESIN WIF`.  The former makes a non-blocking 
attempt to acquire a lock and returns WIN if successful.  The latter blocks
until the lock can be acquired.

The keyword `DUN MESIN WIF` releases the lock on a variable.

The following example shows the use of the locking statements.

```
BTW non-blocking attempt to lock variable
IM MESIN WIF UR <x>, O RLY?
  YA RLY,
    UR <x> r <y>
    DUN MESIN WIF <x>
  NO WAI,
    RTFM "failed to acquire lock"
OIC
```

```
BTW blocking attempt to lock variable
IM SRSLY MESIN WIF UR <x>
UR <x> r <y>
DUN MESIN WIF <x>
```

### Synchronization

Using a SPMD execution model, it is often necessary to ensure that each PE
reaches the same point within a program before continuing.  The keyword `HUGZ`
is used to create a barrier that must be entered by all PEs before any PE
may continue.  In the example below, it is guaranteed that all PEs complete
there update of \<x> before proceeding to execute the rest of the program.

```
UR <x> R SUM OF <y> AN <z>
HUGZ
<y> R SUM OF <x> AN <z>
```


