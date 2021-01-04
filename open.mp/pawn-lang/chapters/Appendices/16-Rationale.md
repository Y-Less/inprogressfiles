<div align="center">

# Rationale

---

</div>

<div align="left">

_The first issue in the presentation of a new computer language should be: why
a new language at all?_

Indeed, I did look at several existing languages before I designed  
my own. Many little languages were aimed at scripting the command shell (TCL, Perl,
Python). Other languages were not designed as extension languages, and put
the burden to embedding solely on the host application.

As I initially attempted to use Java as an extension language (rather than build
my own, as I have done now), the differences between PAWN and Java are illus-
trative for the almost reciprocal design goals of both languages. For example,

Java promotes distributed computing where “packages” reside on diverse ma-
chines, PAWN is designed so that the compiled applets can be easily stored in
a compound file together with other data. Java is furthermore designed to be
architecture neutral and application independent, inversely PAWN is designed
to be tightly coupled with an application; native functions are a taboo to some
extent in Java (at least, it is considered “impure”), whereas native functions
are “the reason to be” for PAWN. From the viewpoint of PAWN, the intended
use of Java is upside down: native functions are seen as an auxiliary library
that the application —in Java— uses; in PAWN, native functions are part of
“the application” and the PAWN program itself is a set of auxiliary functions
that the application uses.

__A language for scripting applications:__ PAWN is targeted as an exten-
sion language, meant to write application-specific macros or subprograms with.
PAWN is not the appropriate language for implementing business applications
or operating systems in. PAWN is designed to be easily integrated with, and
embedded in, other systems/applications.

As an extension language, PAWN programs typically manipulate objects of
the host application. In an animation system, PAWN scripts deal with sprites,
events and time intervals; in a communication application, PAWN scripts handle
packets and connections. I assume that the host application will make
(a subset of) its resources and functionality available via functions, handles,
magic cookies. . . in a similar way that a contemporary operating system
provides an interface to processes written in C/C++ —e.g., the Win32 API (“handles
everywhere”) or GNU/Linux’ “glibc”. To that end, PAWN has a simple and
efficient interface to the “native” functions of the host application. A PAWN
script manipulates data objects in the host application through function calls,
but it cannot access the data of the host application directly.

The first and foremost criterions for the PAWN language were execution speed
and reliability. Reliability in the sense that a PAWN program should not be
able to crash the application or tool in which it is embedded —at least, not
easily. Although this limits the capabilities of the language significantly,
the advantages are twofold:

- the application vendor can rest assured that its application will not crash due to user additions or macros,

- the user is free to experiment with the language with no (or little) risk of damaging the application files.

__Speed is essential:__ PAWN programs would probably run in an abstract ma-
chine, and abstract machines are notoriously slow. I had to make a language
that has low overhead and a language for which a fast abstract machine can be
written. Speed should also be reliable, in the sense that a PAWN script should
not slow down over time or have an occasional performance hiccup. Conse-
quently, PAWN excludes any required “background process”, such as garbage
collection, and the core of the abstract machine does not implicitly
allocate any system or application resources while it runs. That is, PAWN does
not allocate memory or open files, not without the help of a native function that
the script calls explicitly.

As Dennis Ritchie said, by intent the C language confines itself to facilities
that can be mapped relatively efficiently and directly to machine instructions. The
same is true for PAWN, and this is also a partial explication why PAWN looks
so much like C. Even though PAWN runs on an abstract machine, the goal is to
keep that abstract machine small and quick. PAWN is used in tiny embedded
systems with ram sizes of 32 kiB or less, as well as in high-performance games
that need every processor cycle for their graphics engine and game-play.  
In both environments, a heavy-weight scripting support is difficult to swallow.

A brief analysis showed that the instruction decoding logic for an abstract ma-
chine would quickly become the bottleneck in the performance of the abstract
machine. To keep the decoding simple, each opcode should have the  
same size (excluding operands), and the opcode should fully specify the instruction
(including the addressing methods, size of the operands, etc.). That meant
that for each operation on a variable, the abstract machine needed a separate
opcode for every combination of variable type, storage class and access method
(direct, or dereferenced). For even three types (int, char and unsigned int),
two storage classes (global and local) and three access methods (direct, indi-
rect or indexed), a total of 18 opcodes (3*2*3) are needed to simply fetch the
value of a variable.

At the same time, to keep the abstract machine small and manageable, I set the
goal at approximately 100 instructions.∗ With 18 opcodes to load a variable
in a register, 18 more to store a register into a variable, another 18 to get
the address of a variable, etc. . . I was quickly exceeding my self-imposed limit
of a hundred opcodes.

The languages bob and rexx inspired me to design a typeless language. This
saved me a lot of opcodes. At the same time, the language could no longer be
called a “subset of C”. I was changing the language. Why, then, not go a foot
further in changing the language? This is where a few more design guidelines came into play:

- give the programmer a general purpose tool, not a special purpose solution

- avoid error prone language constructs; promote error checking

- be pragmatic

__A general purpose tool:__ PAWN is targeted as an extension language, with-
out specifying exactly what it will extent. Typically, the application or  
the tool that uses PAWN for its extension language will provide many, optimized
routines or commands to operate on its native objects, be it text, database
records or animated sprites. The extension language exists to permit the user
to do what the application developer forgot, or decided not to include. Rather
than providing a comprehensive library of functions to sort data, match reg-
ular expressions, or draw cubic B´ezier splines, PAWN should supply a (general
purpose) means to use, extend and combine the specific (“native”) functions
that an application provides.

PAWN lacks a comprehensive standard library. By intent, PAWN also lacks fea-
tures like pointers, dynamic memory allocation, direct access to the operating
system or to the hardware, that are needed to remain competitive in the field of
general purpose application or system programming. You cannot build linked
lists or dynamic tree data structures in PAWN, and neither can you access any
memory beyond the boundaries of the abstract machine. That is not to say
that a PAWN program can never use dynamic, sorted symbol tables, or change
a parameter in the operating system; it can do that, but it needs  
to do so by calling a “native” function that an application provides to the abstract machine.

---

###### ∗ 136 Opcodes are defined at this writing, plus 20 “macro” opcodes. To exploit performance gains by forcing proper alignment of memory words (essential on ARM microprocessors), the current abstract machine uses 32-bit opcodes. There is no technical   limit on the number of opcodes, but in the interest of a small footprint, the number of opcodes should be restricted.

---

In other words, if an application chooses to implement the well known peek and
poke functions (from BASIC) in the abstract machine, a PAWN program can
access any byte in memory, insofar the operating system permits this. Likewise,
an application can provide native functions that insert, delete or search
symbols in a table and allows several operations on them. The proposed core functions
getproperty and setproperty are an example of native functions that build
a linked list in the background.

__Promote error checking:__ As you may have noticed, one of the foremost
design criterions of the C language, “trust the programmer”, is absent from
my list of design criterions. Users of script languages may not be experienced
programmers; and even if they are, PAWN will probably not be their primary
language. Most PAWN programmers will keep learning the language as they
go, and will even after years not have become experts. Enough reason, hence,
to replace error prone elements from the C language (pointers) with  
saver, albeit less general, constructs (references).† References are copied from C++.
They are nothing else than pointers in disguise, but they are  
restricted in various, mostly useful, ways. Turn to a C⁺⁺ book to find more justification for references.

I find it sad that many, even modern, programming languages have so  
little built-in, or easy to use, support for confirming that programs do  
as the programmer intended. I am not referring to theoretical correctness (which is
too costly to achieve for anything bigger than toy programs), but  
practical, easy to use, verification mechanisms as a help to the programmer.
PAWN provides both compile time and execution time assertions to use for preconditions, postconditions and invariants.

The typing mechanism that most programming languages use is also an auto-
matic “catcher” of a whole class of bugs. By virtue of being a typeless
language, PAWN lacked these error checking abilities. This was clearly a weakness, and I
created the “tag” mechanism as an equivalent for verifying function parameter
passing, array indexing and other operations.

---

###### † You should see this remark in the context of my earlier assertion that many “PAWN” programmers will be novice programmers. In my (teaching) experience, novice programmers make many pointer errors, as opposed to experienced C/C++ programmers.

---

The quality of the tools: the compiler and the abstract machine, also have a
great impact on the robustness of code —whatever the language. Although
this is only very loosely related to the design of the language, I set out to
build the tools such that they promote error checking. The warning system of PAWN
goes a step beyond simply reporting where the parser fails to  
interpret the data according to the language grammar. At several occasions, the compiler
runs checks that are completely unrelated to generating code and that are im-
plemented specifically to catch possible errors. Likewise, the “debugger hook”
is designed right into the abstract machine, it is not an add-on implemented
as an after-thought.

Be pragmatic: The object-oriented programming paradigm has not entirely
lived up to its promise, in my opinion. On the one hand, OOP solves many
tasks in an easier or cleaner way, due to the added abstraction layer. On the
other hand, contemporary object-oriented languages leave you struggling with
the language as much as with the task at hand. Object-oriented languages are
attractive mainly because of the comprehensive class libraries that they come
with —but leaning on a standard library goes against one of the design goal
for PAWN. Object-oriented programming is not a solution for a  
non-expert programmer with little patience for artificial complexity. The criterion
“be pragmatic” is a reminder to seek solutions, not elegancy.

### • Practical design criterions

The fact that PAWN looks so much like C cannot be a coincidence, and it isn’t.
PAWN started as a C dialect and stayed that way, because C has a proven track
record. The changes from C were mostly born out of necessity after rubbing
out the features of C that I did not want in a scripting language: no pointers
and no “typing” system.

PAWN, being a typeless language, needed a different means to declare variables.
In the course of modifying this, I also dropped the C requirement  
that all variables should be declared at the top of a compound statement. PAWN is a
little more like C⁺⁺ in this respect.

C language functions can pass “output values” via pointer arguments. The
standard function scanf, for example, stores the values or strings that it reads
from the console into its arguments. You can design a function in C so that
it optionally returns a value through a pointer argument; if the caller of the
function does not care for the return value, it passes NULL as the pointer
value. The standard function strtol is an example of a function that does this. This
technique frequently saves you from declaring and passing dummy variables.
PAWN replaces pointers with references, but references cannot be NULL. Thus,
PAWN needed a different technique to “drop” the values that a function returns
via references. Its solution is the use of an “argument placeholder”
that is written as an underscore character (“ ”); Prolog programmers will recognize
it as a similar feature in that language. The argument placeholder  
reserves a temporary anonymous data object (a “cell” or an array of cells)  
that is automatically destroyed after the function call.

The temporary cell for the argument placeholder should still have a value, be-
cause the function may see a reference parameters as input/output. Therefore,
a function must specify for each passed-by-reference argument what value it
will have upon entry when the caller passes the placeholder instead of an ac-
tual argument. By extension, I also added default values for arguments that
are “passed-by-value”. The feature to optionally remove all arguments with
default values from the right was copied from C++.

When speaking of BCPL and B, Dennis Ritchie said that C was invented in part
to provide a plausible way of dealing with character strings when one begins
with a word-oriented language. PAWN provides two options for working with
strings, packed and unpacked strings. In an unpacked string, every character

fits in a cell. The overhead for a typical 32-bit implementation is large: one
character would take four bytes. Packed strings store up to four characters
in one cell, at the cost of being significantly more difficult  
to handle if you could only access full cells. Modern BCPL implementations provide two array
indexing methods: one to get a word from an array and one to get a character
from an array. PAWN copies this concept, although the syntax differs from that
of BCPL. The packed string feature also led to the new operator char.

Unicode applications often have to deal with two characters sets:  
8-bit for legacy file formats and standardized transfer formats (like many of the Internet
protocols) and the 16-bit Unicode character set (or the 31-bit UCS-4 character
set). Although the PAWN compiler has an option that makes characters 16-bit
(so only two characters fit in a 32-bit cell), it is usually more
convenient to store single-byte character strings in packed strings and multi-byte strings in
unpacked strings. This turns a weakness in PAWN —the need to distinguish
packed strings from unpacked strings— into a strength: PAWN can make that

distinction quite easily. And instead of needing two implementations for every
function that deals with strings (an ascii version and a Unicode version —look
at the Win32 API, or even the standard C library), PAWN enables functions to
handle both packed and unpacked strings with ease.

Notwithstanding the above mentioned changes, plus those in the chapter “Pit-
falls: differences from C” (page 134), I have tried to keep PAWN close to C. A
final point, which is unrelated to language design, but important nonetheless,
is the license: PAWN is distributed under a liberal license allowing you to use
and/or adapt the code with a minimum of restrictions —see appendix D.

</div>

<hr>

<div align="right">

`Support for Unicode string literals: 139`

<hr>

[Go Back to Contents](00-Contents.md)

</div>
