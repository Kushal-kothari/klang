
# klang

--------------------------------------------------------------------------------

# What Is It?

klang is an high level language compiler based on Linux platform that emits 
Netwide Assembler(NASM) x86 assembly. You can develop simple programs as well as 
low-level applications such as writing OS components.

# What Can It Do?

Through klang, users can write programs according to the syntax of the language provided in 
Grammar and produce Intel x86 assembly language.

Simplifying the generated assembly by backend of the compiler that can be understand by anyone 
who wants to learn NASM assembly language.The generated assembly contain comments having line numbers,
variables used, local variables location on stack and their sizes, expressions etc.

# Features

At present, klang has a set of **core features** as below:

  - Support for using 8-bit, 16-bit and 32-bit registers.
  - Support for simple expression assembly generation.
  - Support for inline assembly.
  - Support for pointers and arrays.
  - Support for conditional statements such as if-else
  - Support for loops such as while, for, do-while.
  - Support for functions.
  - Support for importing C language functions.
  - Support for Abstract data types such as records.

# Documentation

See **doc** directory for documentation.
Or see the manual,

    $ man klang

# Installation and Uninstallation

#### Dependency

  GCC with C++11 compiler(g++)<br/>
  Netwide Assembler(NASM)

### Step 1: Getting the Source Code

You can download klang by command:

    $ git clone https://github.com/kushal-kothari/klang.git
    $ cd klang


### Step 2: Compile and Install

Installing klang requires root(super user)'s privilege. Run command:

    $ make
    $ sudo make install

## Uninstall

To uninstall the klang, just run command:

    $ cd klang
    $ sudo make remove

# How to Start

Create a file with .x file extension. Write a klang program(see **doc** or **examples**).
Compile the program and Run it.

## Example
Here's a simple addition program that imports C printf() function.
Create a file named "hello_world.x" which reads as follow:

    extern void printf(char*, int);

    global void main()
    {
        int a, b, sum;
        a = 10;
        b = 20;

        sum = a + b;

        printf("sum: %d\n", sum);
    }

Then run klang in the terminal

    $ klang hello_world.x

You can run the program after compilation:

    $ ./a.out

To see the generated Intel x86 assembly, 
Then run klang in the terminal with **-S** option.

    $ klang -S hello_world.x

It will create a new file "hello_world.asm" which reads as follows:

    section .text
        extern printf
        global main

    ; [ function: main() ]
    main:
        push ebp
        mov ebp, esp
        sub esp, 12    ; allocate space for local variables
        ; sum = [ebp - 12], dword
        ; b = [ebp - 4], dword
        ; a = [ebp - 8], dword
    ; line 6
        mov eax, 10
        mov dword[ebp - 8], eax
    ; line 7
        mov eax, 20
        mov dword[ebp - 4], eax
    ; line 9
        xor eax, eax
        xor edx, edx
        mov eax, dword[ebp - 8]  ; a
        mov ebx, dword[ebp - 4]  ; b
        add eax, ebx
        mov dword[ebp - 12], eax
    ; line: 11, func_call: printf
    ; line 11
        mov eax, dword[ebp - 12]  ; assignment sum
        push eax    ; param 2
        mov eax, string_val1
        push eax    ; param 1
        call printf
        add esp, 8    ; restore func-call params stack frame
    ._exit_main:
        mov esp, ebp
        pop ebp
        ret 
    
    section .data
        string_val1 db 0x73,0x75,0x6D,0x3A,0x20,0x25,0x64,0x0A,0x00    ; 'sum: %d\n'

--------------------------------------------------------------------------------


