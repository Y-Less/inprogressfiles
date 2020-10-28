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

e-mail: info@compuphase.com, www:http://www.compuphase.com

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

will continue...
