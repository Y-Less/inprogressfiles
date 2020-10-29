<div align="center">

# PAWN

![Pawn](https://i.ibb.co/SRbRt3C/pawn-icon-daddy-DOT-small.png)

### embedded scripting language

</div>

<br/><br/>

<div align="right">

##### February 2006

</div>

---

<div align="right">

##### ITB CompuPhase

</div>

<br/><br/>
<br/><br/>

<div align="left">

##### ii
***

“Java” is  a  trademark  of  Sun Microsystems,  Inc.

“Microsoft”  and “Microsoft  Windows”  are  registered  trademarks  of  
Microsoft Corporation.

“Linux”  is a  registered  trademark  of  Linus  Torvalds.

“CompuPhase”  is  a registered  trademark  of ITB  CompuPhase.

“Unicode”  is  a registered  trademark  of  Unicode,  Inc.

<br/><br/>
<br/><br/>
<br/><br/>

Copyright  c  1997–2006, ITB CompuPhase

Eerste Industriestraat  19–21, 1401VL Bussum The Netherlands (Pays Bas)
telephone: (+31)-(0)35 6939 261

e-mail: info@compuphase.com, http://www.compuphase.com

The information in this manual and the associated software are provided “as
is”.  There are no guarantees, explicit  or implied, that the software and the
manual are accurate.

Requests for corrections and additions to the manual and the software can be
directed to ITB CompuPhase at the above address.

Typeset with TEX in the “Computer Modern” and “Palatino” typefaces  at a base 
size of 11 points.

<br/><br/>
<hr>
<hr>
<div align="right">1-2</div>
<div align="center">

# Table of contents

***

</div>
<div align="left">

      Foreword........................................................................................................................3
      
      A tutorial introduction.........................................................................................................5
      
      Data and declarations..........................................................................................................62
      
      Functions......................................................................................................................70
      
      The preprocessor...............................................................................................................93
      
      General syntax.................................................................................................................97
      
      Operators and expressions.....................................................................................................104
      
      Statements....................................................................................................................112
      
      Directives....................................................................................................................117
      
      Proposed function  library....................................................................................................124
      
      Pitfalls:  differences  from  C...............................................................................................134
      
      Assorted tips.................................................................................................................137
      
      Appendices....................................................................................................................148
      
            A: Error and warning messages...........................................................................................148
            
            B: The compiler.........................................................................................................168
            
            C: Rationale............................................................................................................174
            
            D: License..............................................................................................................181
            
      Index.........................................................................................................................183

</div>
<br/><br/>
<hr>
<hr>
<div align="right">3</div>
<div align="center">

# Foreword

***

</div>
<div align="left">

“pawn” is a simple, typeless, 32-bit “scripting” language with a C-like syntax.
Execution speed, stability, simplicity and a small footprint were essential 
design
criterions for both the language and the interpreter/abstract machine that a
pawn program runs on.

An  application or  tool  cannot  do  or  be  everything   for  all   users.   
This  not
only justifies the diversity of editors, compilers,  operating systems and many
other software systems, it also explains the presence of extensive configuration
options and macro or scripting languages in applications. My own applications
have contained a variety of little languages; most were very simple, some were
extensive. . .   and  most  needs  could  have  been  solved  by  a  general  
purpose
language with a special purpose library. Hence, pawn.

The pawn language was designed as a flexible language for manipulating ob-
jects  in  a  host  application.  The  tool  set  (compiler, abstract machine)  
were
written so  that they were  easily  extensible  and  would run  on  different  
soft-
ware/hardware architectures.

<div>
<div align="center">

## ♦

</div>
<div align="left">

pawn is a descendent of the original Small C by Ron Cain and James Hendrix,
which at its turn was a subset of C. Some of the modifications  that I did to
Small C, e.g. the removal of the type system and the substitution of pointers by
references, were so fundamental that I could hardly call my language a “subset

of C” or a “C dialect” anymore. Therefore, I stripped off the “C” from the title
and used the name “Small” for the name of the language in my publication in
Dr. Dobb’s Journal and the years since. During development and maintenance
of the product,  I received many requests for changes.  One of the frequently
requested changes  was to  use  a  different  name  for  the language —searching
for information on the Small scripting language on the Internet was hindered
by “small” being such a common word.  The name change occurred together
with a significant change in the language:  the support of “states” (and state
machines).

I  am  indebted to  Ron  Cain  and  James  Hendrix  (and  more  recently,  Andy
Yuen),  and to  Dr. Dobb’s Journal  to  get this ball  rolling.  Although I must
have touched nearly every line of the original code multiple times, the Small
C origins are still clearly visible.

</div>
<div align="center">

## ♦

</div>
<div align="left">

##### 4 ♙ Foreword
***

A detailed treatise of the design goals and compromises is in appendix C; here
I would like to summarize a few key points.  As written in the previous para-
graphs, pawn is for customizing applications (by writing scripts), not for writ-
ing applications.  pawn  is weak on data structuring because pawn  programs
are intended to manipulate objects (text, sprites, streams, queries, . . . ) in 
the
host application, but the pawn program is, by  intent, denied direct access to
any data outside its abstract machine.  The only means that a pawn program
has to manipulate objects in the host application is by calling subroutines, so
called “native functions”, that the host application provides.

pawn is flexible in that key area: calling functions. pawn supports default val-
ues for any of the arguments of a function (not just the last), 
call-by-reference
as well as call-by-value, and  “named” as well as “positional” function argu-
ments.  pawn does not have a “type checking” mechanism, by virtue of being
a typeless language, but it does  offer in replacement a “classification 
checking”
mechanism, called “tags”. The tag system is especially convenient for function
arguments because each argument may specify multiple acceptable tags.

For any language, the power (or weakness) lies not in the individual features,
but in their combination.  For pawn, I feel that the combination of named ar-
guments —which lets you specify function arguments in any order, and default
values —which allows you to skip specifying arguments that you are not inter-
ested in, blend together to a convenient and “descriptive” way to call (native)
functions to manipulate objects in the host application.

</div>
<br/><br/>
<hr>
<hr>
<div align="right">5</div>
<div align="center">

# A tutorial introduction

***

</div>
<div align="left">

pawn is a simple programming language with a syntax reminiscent to the “C”
programming  language.  A pawn  program  consists  of a  set of functions and
a  set of variables.  The variables are data  objects and the functions contain
instructions (called “statements”) that  operate  on  the  data  objects  or  that perform tasks.

The first program in almost any computer language is one that prints a simple
string;  printing  “Hello  world”  is  a  classic  example.   In  pawn,  the program would look like:

Listing: hello.p

```c

main()

	printf  "Hello  world\n"

```

This manual assumes that you know how to run a pawn program; if not, please
consult the application manual (more hints are at page 168).

A pawn program starts execution in an “entry” function∗  —in nearly all ex-
amples of this manual, this entry function is called “main”. Here, the function
main contains only a single instruction, which is at the line below the function
head itself.  Line breaks and indenting are insignificant; the invocation of the
function print could equally well be on the same line as the head of function main.

The definition  of a function requires that a pair of parentheses follow the func-
tion name. If a function takes parameters, their declarations  appear between
the parentheses.  The function main does not take any parentheses.  The rules
are  different  for  a  function  invocation  (or  a  function  call); parentheses are
optional in the call to the print function.

The single argument of the print function is a string, which must be enclosed
in double quotes.  The characters \n near the end of the string form an escape
sequence, in this case they indicate a “newline” symbol.  When print encoun-
ters the newline escape sequence, it advances the cursor to the first column of
the next line. One has to use the \n escape sequence to insert a “newline” into
the string, because a string may not wrap over multiple lines.

____________________________
###### ∗  This  should  not  be  confused  with  the  “state”  entry  functions, which  are  called  entry,  but serve  a  different  purpose — see  page  42.

</div>
<div align="right">

`Compiling and running scripts: see page 168`

`String literals: 99
Escape sequence: 99`

</div>
<div align="left">

##### 6 ♙ A tutorial introduction
***

pawn is a “case sensitive” language: upper and lower case letters are considered
to be different letters. It would be an error to spell the function printf in the
above example as “PrintF”. Keywords and predefined symbols, like the name
of function “main”, must be typed in lower case.

If you know the C language, you may feel that the above example  does not
look much like the equivalent “Hello world” program  in C/C++. pawn  can
also look very similar to C, though.  The next example program is also valid
pawn syntax (and it has the same semantics as the earlier example):

Listing: hello.p —  C  style

```c

#include  <console>

main()

{

	printf("Hello  world\n");

}

```

These first examples also reveal a few differences between pawn and the C language:

* there is usually no need to include any system-defined “header file”;

* semicolons  are  optional (except when  writing  multiple  statements on  one line);

* when the body of a function is a single instruction, the braces (for a compound instruction) are optional;

* when you do not use the result of a function in an expression or assignment, parentheses around the function argument are optional.

As an aside,  the few  preceding  points refer to  optional  syntaxes.  It  is 
your choice  what  syntax you  wish  to  use:  neither  style  is  “deprecated” or  
“considered harmful”. The examples  in this manual position the braces  and use
an indentation that is known as the “Whitesmith’s style”, but pawn is a free
format language and other indenting styles are just as good.

Because  pawn  is designed  to  be  an extension  language  for applications, 
the function set/library that a pawn program has at its disposal depends on the
host application.  As a result, the pawn language  has no intrinsic knowledge
of any  function. The print function, used in this first example, must be made
available by the host application and be “declared” to the pawn parser.

____________________________
###### ∗ In the language  specification,  the term “parser”  refers  to any implementation  that processes and  runs  on conforming  Pawn programs  —either  interpreters  or compilers.

</div>
<div align="right">

`More function descriptions at page 124`

</div>

<hr>

<div align="left">

##### 7 ♙ A tutorial introduction
***

It is assumed, however, that all host applications provide a minimal set of common
functions, like print and printf.

In some environments, the display or terminal must be enabled before any text
can be output onto it.  If this is the case, you must add a call to the function
“console”  before  the  first  call  to  function  print or  printf.  The console
function also allows you to specify device characteristics,  such as the number
of lines and  columns  of  the  display. The  example  programs  in  this  
manual do not use the console functions, because many platforms  do not require or provide it.

### • Arithmetic

Fundamental  elements  of  most  programs  are  calculations,  decisions  
(conditional execution), iterations (loops) and variables to store input data, output
data and intermediate results. The next program example illustrates many of
these concepts.  The program  calculates  the greatest common  divisor  of two
values using an algorithm invented by Euclides.

Listing: gcd.p

```c

/*  the  greatest  common  divisor  of  two  values,  using  Euclides’  
algorithm  */

main()
{

	print  "Input  two  values\n"
	new  a  =  getvalue()
	new  b  =  getvalue()
	while  (a  !=  b)
		if  (a  >  b)
			a  =  a  -  b
		else
			b  =  b  -  a
	printf  "The  greatest  common  divisor  is  %d\n",  a
}

```

Function main now contains more than just a single “print” statement.  When
the  body  of a  function contains more than one statement,  these statements
must be embodied in braces —the “{” and “}” characters.  This groups the in-
structions to a single compound statement.  The notion of grouping statements
in a compound statement applies as well to the bodies  of if–else and loop instructions.

The new keyword creates a variable. The name of the variable follows new.
It Data declarations is common,  but  not imperative,
to assign a value to the variable already at the moment of its creation.

</div>
<div align="right">

`Compound statement: 112`

`Data declarations are covered in detail starting at page 62`

</div>

<hr>

<div align="left">

##### 8 ♙ A tutorial introduction
***

Variables must be declared before they are used in an expression.
The getvalue function (also common predefined  function)
reads in a value from the keyboard and returns the result. Note that pawn
is a typeless language, all variables are numeric
cells that can hold a signed integral value.

The getvalue function name is followed by a pair of parentheses.  These are
required  because the value that getvalue returns is stored in a variable. Nor-
mally,  the  function’s  arguments  (or  parameters)  would  appear  between  
the parentheses, but  getvalue (as used  in  this program)  does  not  take any
explicit  arguments.  If you do not  assign the result of a function to a variable
or use it in a expression in another way, the parentheses are optional. For
example, the result of the print and printf statements are not used. You
may still use parentheses around the arguments, but it is not required.

Loop instructions, like “while”, repeat a single instruction as long as the loop
condition  (the  expression  between  parentheses)  is  “true”.   One  can  
execute multiple instructions in a loop by grouping them in a  compound statement.
The if–else instruction has one instruction for the “true” clause and one for the “false”.

Observe  that  some  statements,  like  while  and  if–else,  contain  (or  
“fold around”) another instruction —in the case of if–else even two  other instructions.
The complete bundle is, again, a single instruction. That is:

* the assignment statements “a  =  a  -  b” below the if and “b  =  b  -  a” below the else are statements;

* the if–else statement folds around these two assignment statements and forms a single statement of itself;

* the while statement folds around the if–else statement and forms, again, a single statement.

It is common to make the nesting of the statements explicit by indenting any
sub-statements below a statement in the source text.  In the “Greatest Com-
mon Divisor” example, the left margin indent increases by four space characters
after the while statement, and again after the if and else keywords.  State-
ments that belong to the same level, such as both printf invocations and the
while loop, have the same indentation.

The loop condition  for the while loop is “(a  !=  b)”;  the symbol != is the
“not equal  to”  operator.   That is, the  if–else instruction is repeated  
until “a” equals “b”.  It is good practice to indent the instructions that run under
control of another statement, as is done in the preceding example.

##### 9 ♙ A tutorial introduction
***

The call to printf, near the bottom of the example, differs from the print call
right below the opening brace (“{”). The “f” in printf stands for “formatted”,
which means that the function can format and print numeric values and other
data (in a user-specified format), as well as literal text. The %d symbol in the
string is a token that indicates the position and the format that the subsequent
argument to function printf should be printed.  At run time, the token %d is
replaced by the value of variable “a” (the second argument of printf).

Function print can only print text; it is quicker than printf.  If you want to
print a literal “%” at the display, you have to use print, or you have to double
it in the string that you give to printf.  That is:

    print  "20%  of  the  personnel  accounts  for  80%  of  the  costs\n"

and

    printf  "20%%  of  the  personnel  accounts  for  80%%  of  the  costs\n"

print the same string.

</div>
<div align="right">

`"while" loop: 116`

`"if else": 114`

`Relational operators: 107`

</div>

<hr>

<div align="left">

### • Arrays and constants

Next to simple variables with a size of a single cell, pawn supports “array 
variables” that hold many cells/values.  The following example program displays
a series of prime numbers using the well known “sieve of Eratosthenes”.  The
program also introduces another new concept:  symbolic constants.
Symbolic constants look like variables, but they cannot be changed.

Listing: sieve.p

```c

/*  Print  all  primes  below  100,  using  the  "Sieve  of  Eratosthenes"  */

main()
	{
	const  max_primes  =  100
	new  series[max_primes]  =  {  true,  ...  }
	for  (new  i  =  2;  i  <  max_primes;  ++i)
		if  (series[i])
		{
		printf  "%d  ",  i
		/*  filter  all  multiples  of  this  "prime"  from  the  list  */
		for  (new  j  =  2  *  i;  j  <  max_primes;  j  +=  i)
			series[j]  =  false
		}
	}

```

##### 10 ♙ A tutorial introduction
***

When a program or sub-program has some fixed limit built-in, it is good prac-
tice create  a symbolic constant for it.  In the preceding  example,  the symbol
max_primes is a constant with the value 100.  The program uses the symbol
max_primes three times after its definition:  in the declaration of the variable
series and in both for loops.  If we were to adapt the program  to print all
primes below 500, there is now only one line to change.

Like simple variables, arrays may be initialized upon creation.  pawn offers a
convenient  shorthand to  initialize all elements  to a  fixed  value: 
all hundred
elements of the “series” array are set to true —without requiring that the
programmer  types in the word “true” a  hundred  times.  The symbols  true
and false are predefined constants.

When a simple variable, like the variables i and j in the primes sieve example,
is  declared  in  the  first  expression  of  a  for  loop,  the  variable  is  
valid  only inside the loop.
Variable declaration  has its own rules; it is not a 
statement — although it looks like one.
One of those rules is that the first expression 
of a for loop may contain a variable declaration.

Both for loops also introduce new operators in their third expression.
The ++ operator  increments its operand  by one;  that is, ++i is equal  to i  =  i  + 1.
The  += operator  adds the  expression  on its right to the  variable on  its left;
that is, j  +=  i is equal to j  =  j  +  i.

There is an “off-by-one” issue that you need to be aware if when working with
arrays. The first element in the series array is series[0], so if the array 
holds max_primes elements,  the last element in the array is series[max_primes 1].
If max_primes is 100, the last element, then, is series[99].  Accessing series[100] is invalid.

</div>
<div align="right">

`Constant declaration: 101`

`Progressive initiallers: 65`

`"for" loop: 113`

`An overview of all operators: 104`

</div>

<div align="left">

### • Functions

Larger programs separate tasks and operations into functions. Using functions
increases the  modularity of programs  and  functions,  when well  written,  are
portable to other programs.  The following example implements a function to
calculate numbers from the Fibonacci series.

The Fibonacci sequence was discovered by Leonardo  “Fibonacci” of Pisa, an
Italian  mathematician of the 13th  century—whose greatest achievement  was
popularizing for the Western world the Hindu-Arabic numerals.  The goal of
the sequence was to describe the growth of a population of (idealized) rabbits;

and the sequence is 1, 1, 2, 3, 5, 8, 13, 21,. . . 
(every next value is the sum of its two predecessors).

##### 11 ♙ A tutorial introduction

Listing: fib.p

```c

/*  Calculation  of  Fibonacci  numbers  by  iteration  */

main()
	{
	print  "Enter  a  value:  "
	new  v  =  getvalue()
	if  (v  >  0)
		printf  "The  value  of  Fibonacci  number  %d  is  %d\n",
			v,  fibonacci(v)
	else
		printf  "The  Fibonacci  number  %d  does  not  exist\n",  v
	}
fibonacci(n)
	{
	assert  n  >  0
	new  a  =  0,  b  =  1
	(new  i  =  2;  i  <  n;  i++)
		{
		new  c  =  a  +  b
		a  =  b
		b  =  c
		}
	return  a  +  b
	}

```

The assert instruction at the top of the fibonacci function deserves explicit
mention; it guards against “impossible” or invalid conditions.  A negative Fi-
bonacci number is invalid, and the assert statement flags it as a programmer’s
error if this case ever occurs.  Assertions should only flag programmer’s 
errors, never user input errors.

The implementation of a user-defined function is not much different than that
of function  main.   Function  fibonacci shows two new  concepts,  though:  it
receives an input value through a parameter  and it returns a value (it has a
“result”).

Function parameters are declared in the function header; the single parameter
in this example  is “n”.   Inside  the function,  a  parameter  behaves  as a  
local variable,  but  one  whose  value  is passed  from  the  outside at
the call to  the function.

The return statement ends a function and sets the result of the function.
It need not appear at the very end of the function; early exits are permitted.

</div>
<div align="right">

`"assert" statement: 112`

`Funcitons: properties & features: 70`

</div>

<div align="left">

##### 12 ♙ A tutorial introduction
***

The main function of the Fibonacci example calls predefined “native” functions,
like  getvalue and  printf,  as  well  as  the  user-defined  function fibonacci.
From the perspective of calling  a function (as in function main), there is no
difference between user-defined and native functions.

The Fibonacci numbers sequence describes a surprising variety of natural phe-
nomena.  For example, the two or three sets of spirals in pineapples, pine cones
and sunflowers usually have consecutive Fibonacci numbers between 5 and 89
as their  number  of  spirals.   The  numbers  that occur  naturally  in branching
patterns (e.g. that of plants) are indeed Fibonacci numbers.  Finally, although
the Fibonacci sequence is not  a geometric sequence, the further the sequence
is extended,  the more closely  the ratio between successive terms approaches
the Golden  Ratio, of 1.618. . . ∗ that appears so often in art and architecture.

### • Call-by-reference & call-by-value

Dates  are  a  particularly  rich  source  of  algorithms  and  conversion routines,
because the calenders that a date refers to have known such a diversity,
through time and around the world.

The “Julian Day Number”  is attributed to Josephus Scaliger† and it counts
the  number  of  days  since  November  24,  4714  BC  (proleptic Gregorian cal-
endar‡).  Scaliger  chose that date because  it marked the coincidence of three
well-established cycles:  the 28-year Solar Cycle (of the old Julian calendar), 
the 19-year Metonic Cycle and the 15-year Indiction Cycle (periodic taxes or gov-
ernmental requisitions in ancient Rome), and because no literature or recorded
history was known to predate that particular date in the remote past.  Scaliger
used this concept to reconcile dates in historic documents, later astronomers
embraced it to calculate intervals between two events more easily.

____________________________
###### ∗  The  exact  value  for  the  Golden  Ratio  is   1/2(√5 + 1). The relation  between  Fibonacci numbers and the Golden Ratio also allows for a “direct” calculation of any sequence number, instead  of the  iterative  method  described  here.

###### ∗  There is some debate on exactly what Josephus Scaliger invented and who or what he called it  after.

###### ∗  The Gregorian calendar was decreed to start on 15 October 1582 by pope Gregory XIII, which means that  earlier  dates  do not  really  exist  in  the  Gregorian calendar.  When extending  the Gregorian calendar  to days before 15 October 1582, we refer to it as the proleptic Gregorian calendar.

</div>

<div align="right">

`Native function interface: 85`

</div>

<div align="left">

##### 13 ♙ A tutorial introduction
***

Julian Day numbers (sometimes denoted with unit “jd”) should not be con-
fused with Julian Dates (the number of days since the start of the same  year),
or with the Julian calendar that was introduced by Julius Caesar.

Below  is  a  program  that  calculates  the  Julian  Day  number  from  a date  in
the (proleptic) Gregorian calendar, and vice versa.  Note that in the proleptic
Gregorian calendar, the first year is 1 AD (Anno Domini) and the year before
that  is  1  BC  (Before  Christ):  year  zero  does  not  exist! The program  uses
negative year values for BC years and positive (non-zero) values for AD years.

Listing: julian.p

```c

/*  calculate  Julian  Day  number  from  a  date,  and  vice  versa  */

main()
	{
	new  d,  m,  y,  jdn
	print  "Give  a  date  (dd-mm-yyyy):  "
	d  =  getvalue(_,  ’-’,  ’/’)
	m  =  getvalue(_,  ’-’,  ’/’)
	y  =  getvalue()
	jdn  =  DateToJulian(d,  m,  y)
	printf("Date  %d/%d/%d  =  %d  JD\n",  d,  m,  y,  jdn)
	print  "Give  a  Julian  Day  Number:  "
	jdn  =  getvalue()
	JulianToDate  jdn,  d,  m,  y
	printf  "%d  JD  =  %d/%d/%d\n",  jdn,  d,  m,  y
	}
DateToJulian(day,  month,  year)
	{
	/*  The  first  year  is  1.  Year  0  does  not  exist:  it  is  1  BC  (or  -1)  */
	assert  year  !=  0
	if  (year  <  0)
	year++
	/*  move  January  and  February  to  the  end  of  the  previous  year  */
	if  (month  <=  2)
	year--,  month  +=  12
	new  jdn  =  365*year  +  year/4  -  year/100  +  year/400
	+  (153*month  -  457)  /  5
	+  day  +  1721119
	return  jdn
	}
JulianToDate(jdn,  &day,  &month,  &year)
	{
	jdn  -=  1721119
	/*  approximate  year,  then  adjust  in  a  loop  */
	year  =  (400  *  jdn)  /  146097
	while  (365*year  +  year/4  -  year/100  +  year/400  <  jdn)
	year++
	year--
	/*  determine  month  */
	jdn  -=  365*year  +  year/4  -  year/100  +  year/400
	month  =  (5*jdn  +  457)  /  153
	/*  determine  day  */
	day  =  jdn  -  (153*month  -  457)  /  5
	/*  move  January  and  February  to  start  of  the  year  */
	if  (month  >  12)
		month  -=  12,  year++
	/*  adjust  negative  years  (year  0  must  become  1  BC,  or  -1)  */
	if  (year  <=  0)
		year--
	}

```

##### 14 ♙ A tutorial introduction
***

Function main starts with creating variables to hold the day, month and year,
and the calculated Julian Day number.  Then it reads in a date —three calls to
getvalue— and calls function DateToJulian to calculate the day number.  Af-
ter calculating the result, main prints the date that you entered and the Julian
Day number for that date.  Now, let us focus on function DateToJulian. . .

Near the top of function DateToJulian, it increments the year value if it is
negative; it does this to cope with the absence of a “zero” year in the 
proleptic Gregorian calendar.  In other words, function DateToJulian modifies its func-
tion arguments (later, it also modifies month).  Inside a function, an argument
behaves like a local variable:  you may modify it.  These modifications  remain
local to the function DateToJulian, however. Function main passes the values
of d, m and y into DateToJulian, who maps them to its function arguments
day, month and year respectively.  Although DateToJulian modifies year and
month, it does not change y and m in function main; it only changes local copies
of y and m.  This concept is called “call by value”.

The example  intentionally uses different names  for the local variables in the
functions main and DateToJulian, for the purpose  of making the above ex-
planation  easier.   Renaming  main’s variables  d,  m and  y to  day,  month 
and year respectively, does not change the matter:  then you just happen to have
two local variables called day, two called month and two called year, which is
perfectly valid in pawn.

</div>

<div align="right">

`"Call by value" versus "Call by reference": 71`

</div>

<div align="left">

##### 15 ♙ A tutorial introduction
***

The  remainder  of  function  DateToJulian is,  regarding  the
pawn  language, uninteresting arithmetic.

Returning to  the  second  part  of the  function  main we  see  that it  now asks
for a day number and calls another function, JulianToDate, to find the date
that matches the day number.  Function JulianToDate is interesting because
it takes one input argument (the Julian Day number) and needs to calculate
three output values, the day, month and year.  Alas, a function can only have
a  single  return value  —that is,  a  return statement  in  a  function  may 
only contain one  expression.  To solve this, JulianToDate specifically requests that
changes that it makes to some of its function arguments are copied back to the
variables of the caller of the function.  Then, in main, the variables that must
hold the result of JulianToDate are passed as arguments to JulianToDate.

Function  JulianToDate marks the appropriate arguments for  being  “copied
back to caller” by prefixing them with an & symbol.  Arguments with an & are
copied back, arguments without is are not.  “Copying back” is actually not the
correct  term.  An argument tagged with an & is passed  to the function in a
special  way that allows the function to directly  modify the original variable.
This is called “call by reference” and an argument that uses it is a “reference argument”.

In other words, if main passes y to JulianToDate —who maps it to its function
argument year— and JulianToDate changes year, then JulianToDate really
changes y.  Only through reference arguments can a function directly modify
a variable that is declared in a different function.

To  summarize the  use  of call-by-value versus  call-by-reference:  if a function
has one output value, you typically use a return statement; if a function has
more output values, you use reference arguments.  You may combine the two
inside a  single function, for example  in a function that returns its “normal”
output via a reference argument and an error code in its return value.

As  an  aside,  many  desktop  application use  conversions  to  and  from  
Julian Day numbers (or varieties of it) to conveniently calculate the number of days
between  to  dates  or  to  calculate  the  date  that  is  90  days  from  now —for example.

##### 16 ♙ A tutorial introduction
***

### • Rational numbers

All calculations done up to this point involved only whole numbers —integer
values.  pawn  also has  support  for numbers  that can  hold  fractional values:

these are called “rational numbers”.  However, whether this support is enabled
depends on the host application.

Rational numbers can be implemented as either floating-point or fixed-point
numbers.  Floating-point arithmetic is commonly used for general-purpose and
scientific calculations,  while fixed-point arithmetic is more suitable for 
financial processing  and applications where rounding errors  should not come into

play (or at least, they should be predictable).  The pawn  toolkit has both a
floating-point and  a  fixed-point  module,  and  the  details  (and 
trade-offs) for
these modules in their respective documentation. The issue is, however, that a
host application may implement either  floating-point or  fixed-point, or  both
or  neither.∗  The program below requires that at least either kind of rational
number support is available; it will fail to run if the host application does 
not support rational numbers at all.

Listing: c2f.p

```c

main()
	{
	new  Rational:  Celsius
	new  Rational:  Fahrenheit

	print  "Celsius\t  Fahrenheit\n"
	for  (Celsius  =  5;  Celsius  <=  25;  Celsius++)
		{
		Fahrenheit  =  (Celsius  *  1.8)  +  32
		printf  "%r  \t  %r\n",  Celsius,  Fahrenheit
		}
	}

```

The example program converts a table of degrees Celsius to degrees Fahrenheit.
The first directive of this program is to import definitions for rational number
support  from  an  include  file.   The  file  “rational”  includes  either support
for floating-point numbers  or for fixed-point  numbers,  depending  on what is available.

The variables Celsius and Fahrenheit are declared with a tag “Rational:”
between  the  keyword  new and  the  variable name. A  tag  name  denotes  the
purpose of the variable, its permitted  use  and,  as a special
case  for rational numbers, its memory lay-out.

____________________________
###### ∗ Actually,  this  is  already  true  of  all  native  functions,  including  all  native  functions  that  the examples  in  this  manual  use.

</div>

<div align="right">

`Tag names: 68`

</div>

<hr>

<div align="left">

##### 17 ♙ A tutorial introduction
***

The Rational: tag tells the pawn parser that the variables Celsius and Fahrenheit
contain fractional values, rather than whole numbers.

The equation for obtaining degrees Fahrenheit from degrees Celsius is

</div>

<div align="center">

°F =  ⁹⁄₅ + 32 °C

</div>

<div align="left">

The program  uses the value 1.8 for the quotient  9/₅.  When rational number
support  is enabled,  pawn  supports values  with a  fractional part  behind  
the decimal point.

The  only  other  non-trivial  change  from  earlier  programs  is  that  the format
string  for  the  printf function  now  has  variable  placeholders  denoted with
“%r”  instead  of  “%d”.   The  placeholder  %r prints  a  rational  number  at 
the position; %d is only for integers (“whole numbers”).

I  used  the  include  file  “rational”  rather  than  “float”  or  “fixed”  in an
attempt to make the example  program  portable.  If you know  that the host
application supports floating point arithmetic, it may be more convenient to
“#include” the definitions from the file float and use the tag Float: instead
of Rational —when doing so, you should also replace %r by %f in the call to
printf.  For details on fixed point and floating point support, please see the
application notes “Fixed Point Support Library” and “Floating Point Support
Library” that are available separately.

### • Strings

pawn has no intrinsic “string” type; character strings are stored in arrays, with
the convention that the array element behind the last valid character is zero.
Working with strings is therefore equivalent with working with arrays.

Among  the  simplest  of  encryption  schemes  is  the  one  called  “ROT13”  —
actually the algorithm is quite “weak” from a cryptographical point of view.  It
is most widely used in public electronic forums (BBSes, Usenet) to hide texts
from casual reading, such as the solution to puzzles or riddles. ROT13 simply
“rotates” the alphabet by half its length, i.e. 13 characters.  It is a symmetric
operation:  applying it twice on the same text reveals the original.

##### 18 ♙ A tutorial introduction
***

Listing: rot13.p

```c

/*  Simple  encryption,  using  ROT13  */

main()
	{
	printf  "Please  type  the  string  to  mangle:  "

	new  str[100]
	getstring  str,  sizeof  str
	rot13  str

	printf  "After  mangling,  the  string  is:  \"%s\"\n",  str
	}

rot13(string[])
	{
	for  (new  index  =  0;  string[index];  index++)
	if  (’a’  <=  string[index]  <=  ’z’)
		string[index]  =  (string[index]  -  ’a’  +  13)  %  26  +  ’a’
	else  if  (’A’  <=  string[index]  <=  ’Z’)
		string[index]  =  (string[index]  -  ’A’  +  13)  %  26  +  ’A’
	}

```

In  the  function header  of rot13,  the  parameter  “string”  is declared  as an
array, but without specifying the size of the array —there is no value between
the square brackets.  When you specify a size for an array in a function header,
it must match the size of the actual  parameter in the function call.  Omitting
the array size specification in the function header removes this restriction and
allows the function to be called with arrays of any size.  You must then have
some other means of determining the (maximum) size of the array. In the case
of a string parameter, one can simply search for the zero terminator.

The for loop that walks over the string is typical for string processing func-
tions.  Note that the loop condition is “string[index]”.  The rule for true/
false conditions in pawn is that any value is “true”, except zero.  That is, 
when the array cell at string[index] is zero, it is “false” and the loop aborts.

The  ROT13  algorithm  rotates  only  letters;  digits,  punctuation  and special
characters are left unaltered.  Additionally, upper and lower case letters must
be handled separately.  Inside  the for loop, two if statements filter out the
characters  of interest.  The  way that the second if is chained  to  the “else”
clause   of  the  first  if is  noteworthy,  as  it  is a  typical  method  of 
testing for multiple non-overlapping conditions.

Earlier in this chapter, the concept of “call by value” versus “call by 
reference” were discussed.  When you are working with strings, or arrays in general, note
that pawn always  passes arrays by reference. It does this to conserve memory
and to increase performance —arrays can be large data structures and passing
them by value requires a copy of this data structure to be made, taking both memory and time.

</div>

<hr>

<div align="right">

`A function that takes an array as an argument and that does  not change it, may mark the argument as “const”; see page 72`

</div>

<div align="left">

##### 19 ♙ A tutorial introduction
***

Will continue...

</div>
