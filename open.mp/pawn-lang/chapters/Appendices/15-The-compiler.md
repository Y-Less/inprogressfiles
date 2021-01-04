<div align="center">

# The compiler

---

</div>

<div align="left">

Many applications that embed the PAWN scripting language use the stand-
alone compiler that comes with the PAWN toolkit. The PAWN compiler is a
command-line utility, meaning that you must run it from a “console window”,
a terminal/shell, or a “DOS box” (depending on how your operating system calls it).

### • Usage

Assuming that the command-line PAWN compiler is called “_pawncc_” (Unix/
Linux) or “_pawncc.exe_” (DOS/Windows), the command line syntax is:

    pawncc <filename> [more filenames...] [options]

The input file name is any legal filename. If no extension is given, “_.pawn_”
or “.p” is assumed. The compiler creates an output file with, by default, the
same name as the input file and the extension “.amx”.

After switching to the directory with the sample programs, the command:

    pawncc hello

should compile the very first “hello world” example (page 5). Should, because
the command implies that:

- the operating system can locate the “_pawncc_” program —you may need to add it to the search path;

- the PAWN compiler is able to determine its own location in the file system so that it can locate the include files —a few operating systems do not support this and require that you use the -i option (see below).

### • Input file

The input file for the PAWN compiler, the “source code” file for the  
script/program, must be a plain text file. All reserved words and all symbol names
(names for variables, functions, symbolic constants, tags, . . . ) must  
use the ascii character set. Literal strings, i.e text between quotes, may be in
extended ascii, such as one of the sets standardized in the ISO 8859 norm —ISO 8859-1
is the well known “Latin 1” set.

The PAWN compiler also supports UTF-8 encoded text files, which are practical
in an environment based on Unicode or UCS-4. The PAWN compiler only
recognizes UTF-8 encoded characters inside unpacked strings and character
constants. The compiler interprets the syntax rules for UTF-8 files  
strictly; non-conforming UTF-8 files are not recognized. The input file may have, but
does not require, a “Byte Order Mark” signature; the compiler
recognizes the UTF-8 format based on the file’s content.

### • Options

Options start with a dash (“-”) or, on Microsoft Windows and DOS, with a
forward slash (“/”). In other words, all platforms accept an option written
as “-a” (see below for the purpose of this option) and the  
DOS/Windows platforms accept “/a” as an alternative way to write “-a”.

All options should be separated by at least one space.

Many options accept a value —which is sometimes mandatory. A value may be
separated from the option letter by a colon or an equal sign (a “:” and a “=”
respectively), or the value may be glued to the option letter. Three equivalent
options to set the debug level to two are thus:

- -d2

- -d:2

- -d=2

The options are:

| Option     | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| -a         | Assembler: generate a text file with the pseudo-assembler code for the PAWN abstract machine, instead of binary code.                                                                                                                                                                                                                                                                                                                                                            |
| -C+/-      | Compact encoding of the binary file, which reduces the size a the output file typically to less than half the original size. Use -C+ to enable it and -C- to revert to “plain” encoding. The option -C (without + or − suffix) toggles the current setting.                                                                                                                                                                                                                      |
| -cname     | Codepage: set the codepage for translating the source file from extended ascii to Unicode/UCS-4. The default is no translation. The name parameter can specify a full path to a “mapping file” or just the identifier of the codepage —in the latter case, the compiler prefixes the identifier with the letters “cp”, appends the extension “.txt” and loads the mapping file from a system directory.                                                                          |
| -Dpath     | Directory: the “active” directory, where the compiler should search for its input files and store its output files.                                                                                                                                                                                                                                                                                                                                                              |
|            | This option is not supported on every platform. To verify whether the PAWN compiler supports this option, run the compiler without any option or filename on the command line. The compiler will then list its usage syntax and all available options in alphabetical order. If the -D switch is absent, the option is not available.                                                                                                                                            |
| -dlevel    | Debug level: 0 = none, 1 = bounds checking and assertions only, 2 = full symbolic information, 3 = full symbolic information and optimizations disabled (same as the combination -d2 and -O0).                                                                                                                                                                                                                                                                                   |
|            | When the debug level is 2 or 3, the PAWN compiler also prints the estimated number of stack/heap space required for the program.                                                                                                                                                                                                                                                                                                                                                 |
| -efilename | Error file: set the name of the file into which the compiler must write any warning and error messages; when set, there is no output to the screen.                                                                                                                                                                                                                                                                                                                              |
| -Hvalue    | “HWND” (Microsoft Windows version only): the compiler can optionally post a message to the specified window handle upon completion of the P-code generation. Host applications that invoke the PAWN compiler can wait for the arrival of this message or signal the user of the completion of the compile.                                                                                                                                                                       |
|            | The message number that is sent to the window is created with the Microsoft Windows SDK function RegisterWindowMessage using the name “PawnNotify”. The wParam of the message holds the compiler return code: 0 = success, 1 = warnings, 2 = errors (plus possibly warnings), 3 = compilation aborted by the user.                                                                                                                                                               |
| -ipathname | Include path: set the path where the compiler can find the include files. This option may appear multiple times at the command line, to allow you to set several include paths.                                                                                                                                                                                                                                                                                                  |
| -l         | Listing: perform only the file reading and preprocessing steps; for example, to verify the effect of the text substitution macros and the conditionally compiled/skipped sections.                                                                                                                                                                                                                                                                                               |
| -Olevel    | Optimization level: 0 = no optimizations; 1 = JIT compatible optimizations only (JIT = “Just In Time” compiler, a high performance abstract machine); 2 = full optimizations.                                                                                                                                                                                                                                                                                                    |
| -ofilename | Output file: set the name and path of the binary output file.                                                                                                                                                                                                                                                                                                                                                                                                                    |
| -pfilename | Prefix file: the name of the “prefix file”, this is a file that is parsed before the input file (as a kind of implicit “include file”). If used, this option overrides the default include file “default.inc”. The -p option on its own (without a filename) disables the processing of any implicit include file.                                                                                                                                                               |
| -rfilename | Report: enable the creation of the report and optionally set the filename to which the extracted documentation and a crossreference report will be written.                                                                                                                                                                                                                                                                                                                      |
|            | The report is in “XML” format. The filename parameter is optional; if not specified, the report file has the same name as the input file with the extension “.XML”.                                                                                                                                                                                                                                                                                                              |
| -Svalue    | Stack size: the size of the stack and the heap in cells.                                                                                                                                                                                                                                                                                                                                                                                                                         |
| -svalue    | Skip count: the number of lines to skip in the input file before starting to compile; for example, to skip a “header” in the source file which is not in a valid PAWN syntax.                                                                                                                                                                                                                                                                                                    |
| -tvalue    | tab size: the number of space characters to use for a tab character. When set to zero (i.e. option -t0) the compiler will no longer issue warning 217 (loose indentation).                                                                                                                                                                                                                                                                                                       |
| -vvalue    | Verbose: display imformational messages during the compilation. The value can be 0 (zero) for “quiet” compile, 1 (one) for the normal output and 2 for a code/data/stack usage report.                                                                                                                                                                                                                                                                                           |
| -wvalue+/- | Warning control: the warning number following the “-w” is enabled or disabled, depending on whether a “+” or a “-” follows the number. When a “+” or “-” is absent, the warning status is toggled. For example, -w225- disables the warning for “unreachable code”, -w225+ enables it and -w225 toggles between enabled/disabled.                                                                                                                                                |
|            | Only warnings can be disabled (errors and fatal errors cannot be disabled). By default, all warnings are enabled.                                                                                                                                                                                                                                                                                                                                                                |
| -Xvalue    | Limit for the abstract machine: the maximum memory requirements that a compiled script may have, in bytes. This value is is useful for (embedded) environments where the maximum size of a script is bound to a hard upper limit. If there is no setting for the amount of RAM for the data and stack, this refers to the total memory requirements; if the amount of RAM is explicitly set, this value only goves the amount of memory needed for the code and the static data. |
| -XDvalue   | RAM limit for the abstract machine: the maximum memory requirements for data and stack that a compiled script may have, in bytes. This value is is useful for (embedded) environments where the maximum data size of a script is bound to a hard upper limit. Especially in the case where the PAWN script runs from ROM, the sizes for the code and data sections need both to be set.                                                                                          |
| -\         | Control characters start with “\” (for the sake of similarity with C, C++ and Java)                                                                                                                                                                                                                                                                                                                                                                                              |
| -^         | Control characters start with “ˆ” (for compatibility with earlier versions of pawn).                                                                                                                                                                                                                                                                                                                                                                                             |
| -;+/-      | With -;+ every statement is required to end with a semicolon; with -;-, semicolons are optional to end a statement if the statement is the last on the line. The option -; (without + or − suffix) toggles the current setting.                                                                                                                                                                                                                                                  |
| sym=value  | define constant “sym” with the given (numeric) value, the value is optional;                                                                                                                                                                                                                                                                                                                                                                                                     |
| @filename  | read (more) options from the specified “response file”.                                                                                                                                                                                                                                                                                                                                                                                                                          |

### • Response file

To support operating systems with a limited command line length (e.g., Mi-
crosoft DOS), the PAWN compiler supports “response files”. A response
file is a text file that contains the options that you would otherwise put at the
command line. With the command:

    pawncc @opts.txt prog.pawn

the PAWN compiler compiles the file “prog.pawn” using the options that are
listed in the response file “opts.txt”.

### • Configuration file

On platforms that support it (currently Microsoft DOS, Microsoft Windows
and Linux), the compiler reads the options in a “configuration file” on startup.

The configuration file must have the name “pawn.cfg” and it must reside in
the same directory as the compiler executable program.

In a sense, the configuration file is an implicit response file. Options
specified on the command line may overrule those in the configuration file.

</div>

<hr>

<div align="right">

`Packed/unpacked strings: 99`

`Character constants: 99`

`#pragma dynamic: 121`

`Warnings: 161`

`See also #pragma amxlimit on page 119`

`See also #pragma amxram on page 120`

<hr>

[Go Back to Contents](00-Contents.md)

</div>
