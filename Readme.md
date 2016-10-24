
#**1. Theory** 

##1.1 Program structure  

Traditionally first program should type the hello message on standard output. Here it is: 

>		SECTION .data
>>		hello: db ’ Hello world!’,10 	;’Hello world!’ and newline symbol
>		helloLen : equ 10 hello 	;Length of the ’Hello world!’line
>		SECTION .text 			;Code section begins here 
>		 GLOBAL _start			;A _start label should be global
>					        ;as linker would search it to make an
>					         ;ntrance point to the program.
>		_start:
>		mov eax, 4 	                 ;System call writing to a file: (sys_write)  
>		mov ebx, 1			;File descriptor 1 means standard output  
>		mov ecx, hello			;Address of the hello line - into   
>		mov edx, helloLen 		;helloLen - constant, not variable,  
>				       		; so no need to use mov edx,[helloLen]
>		int 80h 			; Call to the kernel
>		mov eax, 1 			; System call to leave (sys_exit)
>		mov ebx, 0 			; Exiting with 0 return code (no error)
>		int 80h 			; Call to the kernel  

Despite many high-level programming languages, in assembler each command is placed
in separate line. You can’t combine several commands in one line. Command is also usually
not divided into several lines.

There are different assemblers available for **GNU/Linux**, but we will use **NASM**, the
Netwide Assembler. It is based on Intel assembler syntax, which is simpler than **AT&T**
syntax used by as and gas assemblers, preinstalled in many distributions.
**NASM** syntax is case-sensitive.  

**NASM** command may be a *directive* - a guidance for translator, which it should follow
translating program into machine code. Many directives are started from period. Traditionally
they are written in upper case to be easily distinguished. Beside directives there
are also *instructions* - processor commands. Instructions are translated into machine
code.
[##1.2 Creating assembler program] [id] reference-style link.
##1.2 Creating assembler program
Assembler allows to gain lowest level access to the computer architecture, but as in other
modern OS, so in **GNU/Linux** kernel functions are used to perform most of actions in a
program. Despite high-level programs, assembler program contains only code *written by
the programmer*, and there are no code correctness checks.

For example, sub-program should be ended with the return command. High-level
language translator would add such command in case of absence, but assembler program
with no return command would not branch to the invocation point, but would instead
execute code placed in RAM after the body of a sub-program.  

You even may try to execute data instead of code  usually it is senseless, but translator
would not generate error. Assembler allows to do everything, it has less limitations
than high-level language, but surely it has also much less comfort.

System calls in Linux are traditionally done through **int 0x80** command. Linux features
a so-called "fastcall" convention for system calls: the system function number is
passed in **eax**, and arguments are passed through registers, not the stack. There can be
up to six arguments in **ebx**, **ecx**, **edx**, **esi**, **edi**, **ebp** consequently. If there are more arguments,
they are simply passed though the structure in RAM, addressed with the first
argument. The result is returned in eax.  

System call function numbers can be seen in **asm/unistd.h**.
Actually see **/usr/include/asm/unistd_32.h** for 32-bit system, or **unistd_64.h** for a
64-bit one. Documentation on the actual system calls is in section 2 of the manual pages
some documentation is in the 2nd section of manual (for example to find info on **write**
system call, use the command **man 2 write**).
##1.3 Translating assembler program
Due to programming specifics and traditions, command line utilities are usually used to
translate assembler code (but of course there are IDEs supporting assembler too). Process
of program creation includes 4 steps (without the design and thinking stage).  

1. Typing program in the text editor and saving it to separate file. Traditionally assembler
program filename is ended with **.asm**.  

2. Processing program text with translator. Text is converted to machine code, socalled
object code. Listing of the program with additional information from translator
can be also obtained here. Object filename ends with **.o**, listing file with **.lst**.
The step itself is called translation.  

3. Processing object code with linker. Program is "bound" to specific conditions of
the environment. Obtained machine code is called executable. Also map of loading
into RAM can be generated. Executable filename has no specific extension in GNU/Linux as against Windows, but user should have rights to execute this file.
Map filename has .map extension. The step is known as linkage.  

4. Running the program. Debugging can be also performed on this step to find errors
in the program text (with return to the first step to correct them).
Summarizing, assembler program creation can be illustrated by following scheme 
with final target of creating correctly working hello (see fig. 1.3).    

![Figure 1: Creating an assembler program](Page1.jpg)    

##1.4 Main functions of mcedit text editor
mcedit is a text editor embedded into an orthodox two-panel file manager Midnight
Commander. Midnight Commander (or mc) is very close to other classic "commanders".

To create **lab1.asm** file in current directory and start editing it you should type:

**mcedit ./lab1.asm**

General command line format:

**mcedit [-bcCdfhstVx?] [+number] file**

Some parameters:
+number jump to specified line (there should be no space between + sign
and number)
-b black-and-white mode
-c color ANSI mode for terminals
-d without mouse support
-V show program version
**mcedit** is a convenient text editor. Most used features are block copy, move, cut, delete
and insert; undo; slide-down menus; insert from file; macro-commands; search and replace
with regular expressions; syntax highlighting; word wrap; variable tab length.

Editor is very simple in usage and needs no preliminary studying. Sliding menu is
called by F9 key. Here is the list of most used hotkeys (Ctrl and Shift are as engraved on
keyboard, Meta is Alt or Esc in modern computers):

>F3            Start text selection. Press F3 again to stop selecting text  
>Shift+F3      Start text block selection. Press F3 again to stop selecting text block  
>F5 	       Copy selected text

>F6			Move selected text

>F8 		Delete selected text  


Some terminals allow to select text by mouse.

Auto-completion key (usually Alt-Tab or Escape Tab) completes word under cursor,
using other words from the file.

Additional information can be obtained in the usual Unix way, with man mc command.
##1.5 Formatting an assembler program
Following typesetting rules are recommended for assembler program:
 directives are to be typed in upper case, and instructions in lower case;
 type text widely, but don’t exceed screen width (text should be easily readable);
 use tabulation for indents (the TAB key);
 use uniform indents in comment blocks.

Optimal is following line formatting:

>		<TAB><TAB> mov <TAB> eax,<space>ebx <(1-3)TAB>;<space>text of the comment
Number of TABs before comment depends on arguments length and may vary from
1 to 3.  

##1.6 NASM translator
**NASM** translates program into the object code. Program filename is placed in command
line. In simplest way it looks like: nasm hello.asm (don’t forget to specify filename
extension). Program text from hello.asm file is converted into the object code and saved
as **hello.o** file.

**NASM** always creates output files in current directory.

**NASM** will do nothing without command line parameters, as it is not an integrated
environment, but only a translator.
Separate directory is recommended for each laboratory work. E. g. create **~/labs/asm/01**
folder for this one.

Type nasm hello.asm to run translator. You will see no messages if there is no
translation errors. In case of errors object file is not created.
For example, to compile "Hello World" program you should type:

**nasm -f elf hello.asm**

The **-f elf** key makes translator to create binary files in ELF format (elf64 should
be used in 64-bit Linux version to generate 64-bit code).
##1.7 LD linker
As shown on fig. 1.3, object file should pass linkage step to be transformed into an
executable:

**ld -o hello hello.o**

The **-o** key with following value specifies the executable filename.
The LD command line will be examined in next works, and it also can be seen after
typing **ld --help**. More details can be obtained with **man ld**. Created file can be executed
as:

**./hello**

Note: here hello executable is started from current directory (specified by "./" symbols
before its name).
#2 **Laboratory work execution order**

1. Create subdirectory **asm_01** in your home directory. Make lab1.asm text file in it,
with program text from section 1.1 (you may use mcedit).
2. Translate it to object file.
3. Link object file and run the executable.
4. Change message from "Hello world!" to your own name. Repeate items 2 and 3.

##Acknowledgements
This study guide is authored and maintained by Dmitryi Kostuk and Halina Chetverkina with help from its contributors.


[View on Github >](https://github.com/fiowro/asm)

[View on bstu >](http://en.bstu.by/index.php/en/university/faculties/electronic-information-systems/computer-and-computer-systems)


![](http://en.bstu.by/images/logo.png)
**BREST STATE TECHNICAL UNIVERSITY**
