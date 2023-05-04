Download Link: https://assignmentchef.com/product/solved-cs502-project-3-type-checking-functions-heap-allocation
<br>
<h1></h1>

In this project, we will enrich the language we de ned in the rst two projects.

At the end of the project, we will be able to parse full programs, including functions and arrays. Our parser will generate an intermediate representation in the form of an Abstract Syntax Tree (AST), and our Semantic Analyzer will be able to verify that the typing rules are being enforced.

With functions now in our language, we can add a little bit of I/O. When writing code in our programming language, the programmer will have access to two primitives:

<strong>getchar</strong> of type () =&gt; Int. <a href="https://en.wikibooks.org/wiki/C_Programming/stdio.h/getchar">doc</a>

<strong>putchar</strong> of type Int =&gt; Unit. <a href="https://en.wikibooks.org/wiki/C_Programming/stdio.h/putchar">doc</a> (we do not return the character)

Using these functions may lead to some issues when the code is launched from sbt. In order to test, launch the <strong>out</strong> binary generated in the folder <strong>gen/</strong>.

<h2>Step 1: Getting Started</h2>

The project has been designed and tested for Linux/Mac OS. If you only have Windows installed on your personal laptop, consider running Linux in a VM or using the lab machines for the project.

If you use remote access to work on your project, please use one of the lab machines pod1-1 to pod1-20 with the suf x cs.purdue.edu (e.g. pod1-1.cs.purdue.edu) Download the skeleton le <a href="https://www.cs.purdue.edu/homes/wang603/cs502/proj3.tar.gz">here</a>.

tar xzvf proj3.tar.gz cd proj3

<h2>Step 2: Project Structure</h2>

<h3>Browse the Files</h3>

A description of the different les is provided below. The les you need to complete are <strong>Parser.scala</strong>, <strong>SemanticAnalyzer.scala</strong>, <strong>Interpreter.scala</strong>, and <strong>Compiler.scala</strong>. Following the path of the rst project, we are going to implement the parser step by step. Follow the comments in the code and nd the <strong>TODOs</strong>. The parts are independent and can be develop separately. In order to implement the new features more easily, we have changed some of the previous class/function de nition we had. You can search for the string CHANGE into the source code to see these modi cations. <strong>src/main/scala/project3/Util.scala</strong>

This le de nes multiple classes that are used to generate the code and run it on your machine. Nothing needs to be modi ed, but it is recommended to read it and have an idea of what is happening behind the scenes. <strong>gen/bootstrap.c</strong>

As we are generating assembly code which is OS dependent, we are using GCC to do the heavy lifting for us. The bootstrap le is a generic C le that is allocation a chunk of memory called <strong>heap</strong>. This memory region is sent to our program through an argument of the function <strong>entry_point</strong>. The exit code of our program is then printed on the screen. Our compiler will generate the le <strong>gen/gen.s</strong> and will be assembled and bootstrapped by gcc:

gcc bootstrap.c gen.s -o out

<strong>out</strong> will then execute our program.

<h3>src/main/scala/project3/Main.scala</h3>

The main function is de ned in this le. The data ow in this le can be seen as:

You will be implementing many different parsers in order to test them through the main function.

The AST generated will then go through the semantic analyzer. If there are no errors, then the code is going to be interpreted or compiled. We provide you with one interpreter. You will have to write one interpreter and one compiler.

As our language becomes more complex, it will become more useful to read the code from a source le. A folder with sample les <strong>examples/</strong> has been provided. Here some examples how to run the code:

sbt &gt; run

Usage: run PROG [OPTION]

or: run FILE [OPTION]

OPTION: intStack

&gt; run examples/valid_arithm.scala           —&gt; interpreter with the provided interpreter, and x8

&gt; run examples/valid_arithm.scala intStack    —&gt; stack interpreter (your code), and x86 compiler

However, it is still possible to run code from the command line, as in project 1:

sbt

&gt; run “val x = 5; x”

&gt; run “val x = 5; x” intStack

<h3>src/main/scala/project3/Parser.scala</h3>

This class contains the de nition of our intermediate language.

As we discussed in class, we are introducing types in our language. In addition to Integer, we will also have the types Boolean and Unit. You will need to modify the <strong>Scanner</strong> class in order to tokenize the constants of type Boolean correctly. There is a single Unit constant: (). Because this construct can be used for other reasons (e.g. a function without parameters), the () will still be parsed into two delimiters: ‘(‘ and ‘)’. The function <strong>parseAtom</strong> will be handling the Unit literal.

The rest of the le is the de nition of each parser we are building, starting from a base parser that is an implementation of the parser we implemented in project 2. What you have to do for this project is highlighted with TODOs in the code. We encourage you to read the comments and code carefully before proceeding. <strong>src/main/scala/project3/SemanticAnalyzer.scala</strong>

While the parser is in charge of verifying that the input string follows the de ned syntax, the semantic analyzer veri es that the program described is meaningful and follows the rules. The skeleton code already contains the semantic checks we implemented in project 2.

However as we have seen in class, we are upgrading our semantic analyzer into a type checker. Your job is to complete the <strong>typeInfer</strong> function using the rules we have seen in class. <strong>src/main/scala/project3/Interpreter.scala</strong>

As we have seen in class, now that our programing language accepts more than just mathematical expressions, we need to de ne the required behavior of our language. An interpreter can be used to de ne this meaning.

We provide you with an interpreter that is fully functional. Your job is to complete the <strong>Stack-based </strong>interpreter.

<h3>src/main/scala/project3/Compiler.scala</h3>

The x86_64 compiler handling all features up to loops is already implemented for you. You need to complete the compiler with the new features. <strong>src/test/scala/project3/*Test.scala</strong>

These les contain some unit tests for the rst parsers. You will have to write your own tests for the others. There are some functions given to you in order to make the implementation easier.

<h2>Extra Credit</h2>

<h3>Char</h3>

The programmer can use two functions to interact with the console: <strong>getchar</strong> and <strong>putchar</strong>. However, we do not have characters, so in order to print hello world, we would have to write the code: putchar(72); putchar(101); putchar(108); putchar(108); putchar(111); …

This is not really convenient; we would rather do:

putchar(toInt(‘H’)); putchar(toInt(‘e’)); putchar(toInt(‘l’)); putchar(toInt(‘l’))…

(much better isn’t it?)

We are therefore going to add a new type in the language: Char. All letters, numbers, punctuation signs and spaces should be handled, e.g. you don’t have to handle the escaping characters ‘
’, ‘r’… In the program, a programmer will be able to use the single quote notation to enter a character e.g. ‘H’. You will also provide two primitives operations:

toInt: of type Char =&gt; Int toChar: of type Int =&gt; Char

You can use the example of the Array look up transformation into primitive. In the skeleton this is done in the semantic analyzer in the App case. The x86 implementation: toInt will be a no-op and toChar:

mov loc, %rax movzbq %al, loc

<h3>Functions as value</h3>

In the <strong>Compiler.scala</strong> le, there are some places marked as Extra Credit. These are the places where we need to generate code for functions used as arguments, stored into variables, or returned from a function. While you are required to parse this kind of code correctly and be able to type check it, you are not required to generate the corresponding x86 code. Implementing such generation correctly will be counted as extra credit.