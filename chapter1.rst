
**************************
1. The Philosophy of Forth
**************************

Forth is a language and an
operating system. But that\’s not all: It\’s also the embodiment of a
philosophy. The philosophy is not generally described as something apart
from Forth. It did not precede Forth, nor is it described anywhere apart
from discussions of Forth, nor does it even have a name other than
“Forth.”

What is this philosophy? How can you apply it to solve your software
problems?

Before we can answer these questions, let\’s take 100 steps backwards and
examine some of the major philosophies advanced by computer scientists
over the years. After tracing the trajectory of these advances, we\’ll
compare—and contrast—Forth with these state-of-the-art programming
principles.



An Armchair History of Software Elegance
========================================

In the prehistoric days of programming, when computers were dinosaurs,
the mere fact that some genius could make a program run correctly
provided great cause for wonderment. As computers became more civilized,
the wonder waned. Management wanted more from programmers and from their
programs.

As the cost of hardware steadily dropped, the cost of software soared.
It was no longer good enough for a program to run correctly. It also had
to be developed quickly and maintained easily. A new demand began to
share the spotlight with correctness. The missing quality was called
“elegance.”

In this section we\’ll outline a history of the tools and techniques for
writing more elegant programs.

Memorability
------------

The first computer programs looked something like
this:

.. code-block:: none
   
   00110101
   11010011
   11011001

..

Programmers entered these programs by setting rows of switches—“on” if
the digit was “1,” “off” if the digit was “0.” These values were the
“machine instructions” for the computer, and each one caused the
computer to perform some mundane operation like “Move the contents or
Register B to Register A,” or “Add the contents of Register C into the
contents of Register A.”

This proved a bit tedious.

Tedium being the stepmother of invention, some clever programmers
realized that the computer itself could be used to help. So they wrote a
program that translated easy-to-remember
abbreviations into the
hard-to-remember bit patterns. The new language looked something like
this:

.. code-block:: none
   
   MOV B,A
   ADD C,A
   JMC REC1

..

The translator program was called an
*assembler*, the new language *assembly
language* . Each instruction
“assembled” the appropriate bit pattern for that instruction, with a
one-to-one correspondence between assembly instruction and machine
instruction. But names are easier for programmers to remember. For this
reason the new instructions were called
*mnemonics* .

Power
-----

Assembly-language programming is
characterized by a one-for-one correspondence between each command that
the programmer types and each command that the processor performs.

In practice, programmers found themselves often repeating the same
*sequence* of instructions over and again to accomplish the same thing
in different parts of the program. How nice it would be to have a name
which would represent each of these common sequences.

This need was met by the “macro
assembler”, a more complicated
assembler that could recognize not only normal instructions, but also
special names (“macros”). For each name, the macro assembler assembles
the five or ten machine instructions represented by the name, just as
though the programmer had written them out in full.

.. figure:: img1-004.png
   :name: img1-004
   :alt: So then I typed GOTO 500---and here I am!

   So then I typed GOTO 500---and here I am!

..

Abstraction
-----------

A major advance was the invention of the “high-level
language".
Again this was a translator program, but a more
powerful one.
High-level languages make it possible for programmers to write code like
this:

.. code-block:: none
   
   X = Y (456/A) - 2

..

which looks a lot like algebra. Thanks to high-level languages,
engineers, not just bizarre bit-jockeys, could start writing programs.
BASIC and
FORTRAN are examples of high-level
languages.

High-level languages are clearly more “powerful” than assembly
languages in the sense that each
instruction might compile dozens of machine instructions. But more
significantly, high-level languages eliminate the linear correspondence
between source code and the resulting machine instructions.

The actual instructions depend on each entire “statement” of source code
taken as a whole. Operators such as :math:`+` and :math:`=` have no
meaning by themselves. They are merely part of a complex symbology that
depends upon syntax and the operator\’s location in the statement.

This nonlinear, syntax-dependent correspondence between source and
object code is widely considered to be an invaluable step in the
progress of programming methodology. But as we’ll see, the approach
ultimately offers more restriction than
freedom.

Manageability
-------------

Most computer programs involve much more than lists of instructions to work
down from start to finish. They also involve testing for various
conditions and then “branching” to the appropriate parts of the code
depending upon the outcome. They also involve “looping” over the same
sections of code repeatedly, usually testing for the moment to branch
out of the loop.

Both assembler and high-level languages provide
branching and
looping capabilities. In assembly
languages you use “jump
instructions;” in some
high-level
languages you
use “GO TO” commands. When these
capabilities are used in the most brute-force way, programs tend to look
like the jumble you see in  :numref:`fig1-1` .

.. figure:: fig1-1.png
   :name: fig1-1
   :alt: Unstructured code using jumps or "GOTOs."

   Unstructured code using jumps or "GOTOs."

..

This approach, still widely used in languages like
FORTRAN and
BASIC, suffers from being difficult to write
and difficult to change if corrections need to be made. In this
“bowl-of-spaghetti” school of programming, it’s impossible to test a
single part of the code or to figure out how something is getting
executed that isn’t supposed to be getting executed.

Difficulties with spaghetti
programs led to
the discovery of “flow charts.” These
were pen-and-ink drawings representing the “flow” of execution used by
the programmer as an aid to understanding the code being written.
Unfortunately the programmer had to make the translation from code to
flow chart and back by hand. Many programmers found old-fashioned flow
charts less than useful.

Modularity
----------

A significant advance arose with the invention of “Structured
Programming”, a methodology based on the observation that large problems
are more easily solved if treated as collections of smaller problems
[dahl72]_ . Each piece is called a *module*. Programs
consist of modules within modules.

Structured programming eliminates spaghetti coding by insisting that
control flow can be diverted only
within a module. You can’t jump out from the middle of one module into
the middle of another module.

For example,  :numref:`fig1-2`  shows a structured diagram of a
module to “Make Breakfast”, which consists of four submodules. Within
each submodule you\’ll find a whole new level of complexity which needn’t
be shown at this level.

.. figure:: fig1-2.png
   :name: fig1-2
   :alt: Design for a structured program

   Design for a structured program

..

A branching decision occurs in this module to choose between the “cold
cereal” module and the “eggs” module, but control flow stays within the
outer module

Structured programming has three premises:

#. Every program is described as a linear sequence of self-contained
   functions, called *modules*. Each module has exactly one entry point
   and one exit point.

#. Each module consists of one or more functions, each of which has
   exactly one entry point and one exit point and can itself be
   described as a module.

#. A module can contain:

   a. operations or other modules

   b. decision structures (``IF``  ``THEN`` statements)

   c. looping structures

The idea of modules having “one-entry, one-exit” is that you can unplug
them, change their innards, and plug them back in, without screwing up
the connections with the rest of the program. This means you can test
each piece by itself. That’s only possible if you know exactly where you
stand when you start the module, and where you stand when you leave it.

In “Make Breakfast” you\’ll either fix cereal or make eggs, not both. And
you\’ll always clean up. (Some programmers I know circumvent this last
module by renting a new apartment every three months.)

.. figure:: fig1-3.png
   :name: fig1-3
   :alt: Structured programming with a non-structured language

   Structured programming with a non-structured language

..

Structured programming was originally conceived as a design approach.
Modules were imaginary entities that existed in the mind of the
programmer or designer, not actual units of source code. When structured
programming design techniques are applied to non-structured languages
like BASIC, the result looks something like :numref:`fig1-3` .

Writeability
------------

Yet another breakthrough encouraged the use of structured programs:
structured programming languages. These languages include control
structures in their command sets, so you can write programs that have a
more modular appearance. Pascal is such a
language, invented by Niklaus Wirth
to teach the principles of structured programming to his students.

.. figure:: fig1-4.png
   :name: fig1-4
   :alt: Using a structured language.

   Using a structured language.

..

:numref:`fig1-4`  shows how this type of language would allow
“Make Breakfast” to be written.

Structured programming languages include control structure operators
such as ``IF`` and ``THEN`` to ensure a modularity of control flow.
As you can see, indentation is important for readability, since all the
instructions within each module are still written out rather than being
referred to by name (e.g., ``MAKE-CEREAL``). The finished program might
take ten pages, with the ``ELSE`` on page five.

Designing from the Top
----------------------

How does one go about designing these modules? A methodology called
“top-down design” proclaims that modules should be designed in order
starting with the most general, overall module and working down to the
nitty-gritty modules.

Proponents of top-down design have witnessed shameful wastes of time due
to lack of planning. They’ve learned through painful experience that
trying to correct programs after they’ve been written—a practice known
as “patching”—is like locking the barn door after the
horse has bolted.

So they offer as a countermeasure this official rule of top-down
programming:

    Write no code until you have planned every last detail.

Because programs are so difficult to change once they\’ve been written,
any design oversight at the preliminary planning stage should be
revealed before the actual code-level modules are written, according to
the top-down design, Otherwise, man-years of effort may be wasted
writing code that cannot be used.

.. figure:: img1-010.png
   :name: img1-010
   :alt: Software patches are ugly and conceal structural weaknesses.

   Software patches are ugly and conceal structural weaknesses.

..

Subroutines
-----------

We’ve been discussing “modules” as abstract entities only. But all
high-level programming languages incorporate techniques that allow
modules of design to be coded as modules of code—discrete units that can
be given names and “invoked” by other pieces of code. These units are
called subroutines, procedures, or functions, depending on the
particular high-level language and on how they happen to be implemented.

Suppose we write “MAKE-CEREAL” as a subroutine. It might look something
like this:

.. code-block:: none
   
   procedure make-cereal
      get clean bowl
      open cereal box
      pour cereal
      open milk
      pour milk
      get spoon
   end

..

We can also write ``MAKE-EGGS`` and ``CLEANUP`` as subroutines. Elsewhere we
can define ``MAKE-BREAKFAST`` as a simple routine that invokes, or calls,
these subroutines:

.. code-block:: none
   
   procedure make-breakfast
      var h: boolean (indicates hurried)
      &textit{test for hurried}
      if h = true then
         &textbf{call make-cereal}
      else
         &textbf{call make-eggs}
      end
      &textbf{call cleanup}
   end

..

The phrase “call make-cereal” causes the subroutine
named “make-cereal” to be executed. When the subroutine has finished
being executed, control returns back to the calling program at the point
following the call. Subroutines obey the rules of structured
programming.

As you can see, the effect of the subroutine call is as if the
subroutine code were written out in full within the calling module. But
unlike the code produced by the macro assembler, the subroutine can be
compiled elsewhere in memory and merely referenced. It doesn\’t
necessarily have to be compiled within the object code of the main
program ( :numref:`fig1-5` ).

.. figure:: fig1-5.png
   :name: fig1-5
   :alt: A main program and a subroutine in memory.

   A main program and a subroutine in memory.

..

Over the years computer scientists have become more forceful in favoring
the use of many small subroutines over long-winded, continuous programs.
Subroutines can be written and tested independently. This makes it
easier to reuse parts of previously written programs, and easier to
assign different parts of a program to different programmers. Smaller
pieces of code are easier to think about and easier to verify for
correctness.

When subroutines are compiled in separate parts of memory and referred
to you can invoke the same subroutine many times throughout a program
without wasting space on repeated object code. Thus the judicious use of
subroutines can also decrease program size.

Unfortunately, there\’s a penalty in execution speed when you use a
subroutine. One problem is the overhead in saving registers before
jumping to the subroutine and restoring them afterwards. Even more
time-consuming is the invisible but significant code needed to pass
parameters to and from the subroutine.

Subroutines are also fussy about how you invoke them and particularly
how you pass data to and from them. To test them independently you need
to write a special testing program to call them from.

For these reasons computer scientists recommend their use in moderation.
In practice subroutines are usually fairly large between a half page to
a full page of source code in length.

Successive Refinement
---------------------

An approach that relies heavily on subroutines is called “Successive
Refinement” [wirth71]_ . The idea is that you begin by
writing a skeletal version of your program using natural names for
procedures for data structures. Then you write versions of each of the
named procedures. You continue this process to greater levels of detail
until the procedures can only be written in the computer language
itself.

At each step the programmer must make decisions about the algorithms
being used and about the data structures they\’re being used on.
Decisions about the algorithms and
associated data structures should be made in parallel.

If an approach doesn\’t work out the programmer is encouraged to back
track as far as necessary and start again.

Notice this about successive refinement: You can\’t actually run any part
of the program until its lowest-level components are written. Typically
this means you can\’t test the program until after you\’ve completely
designed it.

Also notice: Successive refinement forces you to work out all details of
control structure on each level before proceeding to the next lower
level.

.. figure:: img1-013.png
   :name: img1-013
   :alt: Tobias, I think you\'ve carried the successive refinement of that module far enough.

   Tobias, I think you\'ve carried the successive refinement of that module far enough.

..

Structured Design
-----------------

By the middle of late \’70s, the computing industry had tried all the
concepts we\’ve described, and it was still unhappy. The cost of
maintaining software—keeping it functional in the face of
change—accounted for more than half of the total cost of software, in
some estimates as much as ninety percent!

Everyone agreed that these atrocities could usually be traced back to
incomplete analysis of the program, or poorly thought-out designs. Not
that there was anything wrong with structured programming *per se*. When
projects came in late, incomplete, or incorrect, the designers took the
blame for not anticipating the unforeseen.

Scholars naturally responded by placing more emphasis on design. “Next
time let\’s think things out better”.

About this time a new philosophy arose, described in an article called
“Structured Design” [stevens74-1]_ . One of its
principles is stated in this paragraph:

    Simplicity is the primary measurement recommended for evaluating
    alternative designs relative to reduced debugging and modification
    time. Simplicity can be enhanced by dividing the system into separate
    pieces in such a way that pieces can be considered, implemented, fixed
    and changed with minimal consideration or effect on the other pieces
    of the system.

By dividing a problem into simple modules, programs were expected to be
easier to write, easier to change, and easier to understand.

But what is a module, and on what basis does one make the divisions?
“Structured Design” outlines three factors for designing modules.

Functional Strength
-------------------

One factor is something called “functional strength,” which is a measure
of the uniformity of purpose of all the statements within a module. If
all the statements inside the module collectively can be thought of as
performing a single task, they are functionally bound.

You can generally tell whether the statements in a module are
functionally bound by asking the following questions. First, can you
describe its purpose in one sentence? If not, the module is probably not
functionally bound. Next, ask these four questions about the module:

#. Does the description have to be a compound sentence?

#. Does it use words involving time, such as “first,” “next,” “then,”
   etc.?

#. Does it use a general or nonspecific object following the verb?

#. Does it use words like “initialize” which imply a lot of different
   functions being done at the same time?

If the answer to any of these four questions is “yes,” you’re looking at
some less cohesive type of binding than functional binding. Weaker forms
of binding include:

Coincidental binding:
    (the statements just happen to appear in the same module)

Logical binding:
    (the module has several
    related functions and requires a flag or parameter to decide which
    particular function to perform)

Temporal binding:
    (the module contains a group of statements that happen at the same
    time, such as initialization but have no other relationship)

Communicational binding:
    (the module contains
    a group of statements that all refer to the same set of data)

Sequential binding:
    (where the output of one
    statement serves as input for the next statement)

Our ``MAKE-CEREAL`` module exhibits functional binding, because it can be
thought of as doing one thing, even though it consists of several
subordinate
tasks.

Coupling
--------

A second tenet of structured design concerns “coupling,” a measure of
how modules influence the behavior of other modules. Strong coupling is
considered bad form. The worst case is when one module actually modifies
code inside another module. Even passing control flags to other modules
with the intent to control their function is dangerous.

An acceptable form of coupling is “data coupling”, which involves passing
data (not control information) from one module to another. Even then,
systems are easiest to build and maintain when the data interfaces
between modules are as simple as possible.

When data can be accessed by many modules (for instance, global
variables), there\’s stronger coupling between the modules. If a
programmer needs to change one module, there\’s a greater danger that the
other modules will exhibit “side effects”.

The safest kind of data coupling is the passing of local variables as
parameters from one module to another. The calling module says to the
subordinate module, in effect, “I want you to use the data I\’ve put in
these variables named X and Y, and when you\’re done, I expect you to
have put the answer in the variable named Z. No one else will use these
variables”.

As we said, conventional languages that support subroutines include
elaborate methods of passing arguments from one module to
another.

Hierarchical Input-Process-Output Designing
-------------------------------------------

A third precept of structured design
concerns the design process. Designers are advised to use a top-down
approach, but to pay less attention initially to control structures.
“Decision designing” can wait until the later, detailed design of
modules. Instead, the early design should focus on the program\’s
hierarchy (which modules call which modules) and to the passing of data
from one module to another.

To help designers think along these new lines, a graphic representation
was invented, called the “structure
chart.” (A slightly different
form is called the “HIPO chart,” which stands for “hierarchical
input-process-output”). Structure charts include two parts, a hierarchy
chart and an input-output chart.

.. figure:: fig1-6.png
   :name: fig1-6
   :alt: The form of a structured chart, from "Structured Design," **IBM Systems Journal.**

   The form of a structured chart, from "Structured Design," **IBM Systems Journal.**

..

:numref:`fig1-6`  shows these two parts. The main program,
called DOIT, consists of three subordinate modules, which in turn invoke
the other modules shown below them. As you can see, the design
emphasizes the transformation of input to output.

The tiny numbers of the hierarchy chart refer to the lines on the in-out
chart. At point 1 (the module READ), the output is the value A. At point
2 (the module TRANSFORM-TO-B), the input is A, and the output is B.

Perhaps the greatest contribution of this approach is recognizing that
decisions about control flow should not dominate the emerging design. As
we’ll see, control flow is a superficial aspect of the problem. Minor
changes in the requirements can profoundly change the program\’s control
structures, and “deep-six” years of work. But if programs are designed
around other concerns, such as the flow of data, then a change in plan
won’t have so disastrous an effect.

Information-Hiding 
-------------------

In a paper [parnas72]_ published back in 1972, Dr.
David L. Parnas showed that the
criteria for decomposing modules should not be steps in the process, but
rather pieces of information that might possibly change. Modules should
be used to hide such information.

Let’s look at this important idea of “information-hiding”: Suppose you
are writing a Procedures Manual for your company. Here\’s a portion:

    | Sales Dept. takes order
    | sends blue copy to Bookkeeping
    | orange copy to Shipping
    
    | Jay logs the orange copy in the red binder on his desk, and completes
    | packing slip.

Everyone agrees that this procedure is correct, and your manual gets
distributed to everyone in the company.

Then Jay quits, and Marilyn takes over. The new duplicate forms have
green and yellow sheets, not blue and orange. The red binder fills up
and gets replaced with a black one.

Your entire manual is obsolete. You could have avoided the obsolescence
by using the term “Shipping Clerk” instead of the name “Jay”, the terms
“Bookkeeping Dept. copy” and “Shipping Dept. copy” instead of “blue” and
“orange”, etc.

This example illustrates that in order to maintain correctness in the
face of a changing environment, arbitrary details should be excluded
from procedures. The details can be recorded elsewhere if necessary. For
instance, every week or so the personnel department might issue a list
of employees and their job titles, so anyone who needed to know who the
shipping clerk was could look it up in this single source. As the
personnel changes, this list would change.

This technique is very important in writing software. Why would a
program ever need to change, once it\’s running? For any of a million
reasons. You might want to run an old program on new equipment; the
program must be changed just enough to accommodate the new hardware. The
program might not be fast enough, or powerful enough, to suit the people
who are using it. Most software groups find themselves writing
“families” of programs; that is, many versions of related programs in
their particular application field, each a variant on an earlier
program.

To apply the principle of information-hiding to software, certain
details of the program should be confined to a single location, and any
useful piece of information should be expressed only once. Programs that
ignore this maxim are guilty of redundancy. While hardware redundancy
(backup computers, etc.) can make a system more secure, redundancy of
information is dangerous.

As any knowledgeable programmer will tell you, a number that might
conceivably change in future versions of the program should be made into
a “constant” and referred to throughout the program by name, not by
value. For instance, the number of columns representing the width of
your computer paper forms should be expressed as a constant. Even
assembly languages provide “EQU”s and labels for associating values such
as addresses and bit-patterns with names.

Any good programmer will also apply the concept of information-hiding to
the development of subroutines, ensuring that each module knows as
little as possible about the insides of other modules. Contemporary
programming languages such as C, Modula 2, and Edison apply this concept
to the architecture of their procedures.

But Parnas takes the idea much
further. He suggests that the concept should be extended to algorithms
and data structures. In fact, hiding information—not decision-structure
or calling-hierarchy—should be the primary basis for
design!

The Superficiality of Structure
===============================

Parnas proposes two criteria for decomposition:

    #. possible (though currently unplanned) reuse, and
    #. possible (though unplanned) change.

This new view of a “module” is different than the traditional view. This
“module” is a collection of routines, usually very small, which together
hide information about some aspect of the problem.

Two other writers describe the same idea in a different way, using the
term “data abstraction” [liskov75]_ .
Their example is a push-down stack. The stack “module” consists of
routines to initialize the stack, push a value onto the stack, pop a
value from the stack, and determine whether the stack is empty. This
“multiprocedure module”
hides the information of how the stack is constructed from the rest of
the application. The procedures are considered to be a single module
because they are interdependent. You can’t change the method for pushing
a value without also changing the method for popping a value.

The word *uses* plays an important role in this concept.
Parnas writes in a later paper
[parnas79]_:

    Systems that have achieved a certain "elegance"... have done so
    by having parts of the system use other parts...
    
    If such a hierarchical ordering exists then each level offers a
    testable and usable subset of the system...
    
    The design of the "uses" hierarchy should be one of the major
    milestones in a design effort. The division of the system into
    independently callable subprograms has to go in parallel with the
    decisions about **uses**, because they influence each other.

A design in which modules are
grouped according to control flow or sequence will not readily allow
design changes. Structure, in the sense or control-flow hierarchy, is
superficial.

A design in which modules are grouped according to things that may
change can readily accommodate
change.

Looking Back, and Forth
=======================

In this section we\’ll review the
fundamental features of Forth and relate them to what we\’ve seen about
traditional methodologies.

Here\’s an example of Forth code;

.. code-block:: none
   
   : BREAKFAST
      HURRIED?  IF  CEREAL  ELSE  EGGS  THEN CLEAN ;

..

This is structurally identical to the procedure ``MAKE-BREAKFAST`` on page
:numref:`fig1-4` . (If you\’re new to Forth, refer to
Appendix A for an explanation). The words ``HURRIED?``, ``CEREAL``,
``EGGS``, and ``CLEAN`` are (most likely) also defined as colon definitions.

Up to a point, Forth exhibits all the traits we\’ve studied: mnemonic
value, abstraction, power, structured control operators, strong
functional binding, limited coupling, and modularity. But regarding
modularity, we encounter what may be Forth’s most significant
breakthrough:

    The smallest atom of a Forth program is not a module or a subroutine
    or a procedure, but a "word."

Furthermore, there are no subroutines, main programs, utilities, or
executives, each of which must be invoked differently. *Everything* in
Forth is a word.

Before we explore the significance of a word-based environment, let\’s
first study two Forth inventions that make it possible.

Implicit Calls
--------------

First, calls are implicit. You don’t have to say ``CALL CEREAL`` ,
you simply say ``CEREAL`` . In Forth, the definition of ``CEREAL``
“knows” what kind of word it is and what procedure to use to invoke itself.

Thus variables and constants, system functions, utilities, as well as
any user-defined commands or data structures can all be “called” simply
by name.

Implicit Data Passing
---------------------

Second, data passing is implicit.
The mechanism that produces this effect is Forth\’s data stack.
Forth automatically pushes numbers onto the stack; words that require
numbers as input automatically pop them off the stack; words that
produce numbers as output automatically push them onto the stack. The
words ``PUSH`` and ``POP`` do not exist in high-level Forth.

Thus we can write:

.. code-block:: none
   
   : DOIT
       GETC  TRANSFORM-TO-D  PUT-D ;

..

confident that ``GETC`` will get “C”, and leave it on the stack.
``TRANSFORM-TO-D`` will pick up “C” from the stack, transform
it, and leave “D” on the stack. Finally, ``PUT-D`` will pick up
“D” on the stack and write it. Forth eliminates the act of passing
data from our code, leaving us to concentrate on the functional steps
of the data\’s transformation.

Because Forth uses a stack for passing data, words can nest within
words. Any word can put numbers on the stack and take them off without
upsetting the f1ow of data between words at a higher level (provided, of
course, that the word doesn\’t consume or leave any unexpected values).
Thus the stack supports structured, modular programming while providing
a simple mechanism for passing local arguments.

Forth eliminates from our programs the details of *how* words are
invoked and *how* data are passed. What’s left? Only the words that
describe our problem.

Having words, we can fully exploit the recommendations of
Parnas—to decompose problems
according to things that may change, and have each “module” consist of
many small functions, as many as are needed to hide information about
that module. In Forth we can write as many words as we need to do that,
no matter how simple each of them may be.

A line from a typical Forth application might read:

.. code-block:: none
   
   20 ROTATE LEFT TURRET

..

Few other languages would encourage you to concoct a subroutine called
``LEFT`` , merely as a modifier, or a subroutine called ``TURRET`` ,
merely to name part of the hardware.

Since a Forth word is easier to invoke than a subroutine (simply by
being named, not by being called), a Forth program is likely to be
decomposed into more words than a conventional program would be into
subroutines.

Component Programming
=====================

Having a larger set of simpler words makes it easy to use a technique
we’ll call “component programming.” To explain, let’s first reexamine
these collections we have vaguely described as “things that may change.”
In a typical system, just about everything is subject to change: I/O
devices such as terminals and printers, interfaces such as UART chips,
the operating system, any data structure or data representation, any
algorithm, etc.

The question is: “How can we minimize the impact of any such change?
What is the smallest set of other things that must change along with
such a change?”

The answer is: “The smallest set of interacting data structures and
algorithms that share knowledge about
how they collectively work.” We’ll call this unit a “component.”

A component is a resource. It may be a piece of hardware such as a UART
or a hardware stack. Or the component may be a software resource such as
a queue, a dictionary, or a software stack.

All components involve data objects and
algorithms. It doesn’t matter whether
the data object is physical (such as a hardware register), or abstract
(such as a stack location or a field in a data base). It doesn’t matter
whether the algorithm is described in machine code or in
problem-oriented words such as ``CEREAL`` and ``EGGS`` .

.. figure:: fig1-7.png
   :name: fig1-7
   :alt: Structured design vs. component design.

   Structured design vs. component design.

..

:numref:`fig1-7`  contrasts the results of structured design
with the results of designing by components. Instead of *modules* called
``READ-RECORD`` , ``EDIT-RECORD`` , and ``WRITE-RECORD`` , we’re
concerned with
*components* that describe the structure of records, provide a set of
editor commands, and provide read/write routines to storage.

What have we done? We’ve inserted a new stage in the development
process: We decomposed by components in our *design*, then we described
the sequence, hierarchy, and input-process-output in our
*implementation*. Yes, it’s an extra step, but we now have an extra
dimension for decomposition—not just slicing but
*dicing* .

Suppose that, after the program is written, we need to change the record
structure. In the sequential, hierarchical design, this change would
affect all three modules. In the design by components, the change would
be confined to the record-structure component. No code that uses this
component needs to know of the change.

Aside from maintenance, an advantage to this scheme is that programmers
on a team can be assigned components individually, with less
interdependence. The principle of component programming applies to team
management as well as to software
design.

We’ll call the set of words which describe a component a “lexicon.” (One
meaning of lexicon is “a set of words pertaining to a particular field
of interest.”) The lexicon is your interface with the component from the
outside ( :numref:`fig1-8` ).

In this book, the term “lexicon” refers only to those words of a
component that are used by name outside of a component. A component may
also contain definitions written solely to support the externally
visible lexicon. We’ll call the supporting definitions “internal” words.

.. figure:: fig1-8.png
   :name: fig1-8
   :alt: A lexicon describes a component.

   A lexicon describes a component.

..

The lexicon provides the logical equivalents to the data objects and
algorithms in the form of names. The lexicon veils the component’s data
structures and algorithms—the “how it works.” It presents to the world
only a “conceptual model” of the component described in simple words—the
“what it does.”

These words then become the language for describing the data structures
and algorithms of components written at a a higher level. The “what” of
one component becomes the “how” of a higher component.

.. figure:: fig1-9.png
   :name: fig1-9
   :alt: The entire application consists of components.

   The entire application consists of components.

..

Written in Forth, an entire application consists of nothing but
components.  :numref:`fig1-9`  show show a robotics application
might be decomposed.

You could even say that each lexicon is a special-purpose compiler,
written solely for the purpose of supporting higher-level application
code in the most efficient and reliable way.

By the way, Forth itself doesn’t support components. It doesn’t need to.
Components are the product of the program designer’s decomposition.
(Forth does have “screens,” however—small
units of mass storage for saving source code. A component can usually be
written in one or two screens of Forth.)

It’s important to understand that a lexicon can be used by any and all
of the components at higher levels. Each successive component does *not*
bury its supporting components, as is often the case with layered
approaches to design. Instead, each lexicon is free to use all of the
commands beneath it. The robot-movement command relies on the root
language, with its variables, constants, stack operators, math
operators, and so on, as heavily as any other
component.

An important result of this approach is that the entire application
employs a single syntax, which makes it easy to learn and maintain. This
is why I use the term “lexicon” and not “language.” Languages have
unique
syntaxes.

This availability of commands also makes the process of testing and
debugging a whole lot easier. Because Forth is interactive, the
programmer can type and test the primitive commands, such as

.. code-block:: none
   
   RIGHT SHOULDER 20 PIVOT

..

from the “outside” as easily as the more powerful ones like

.. code-block:: none
   
   LIFT COFFEE-POT

..

At the same time, the programmer can (if he or she wants) deliberately
seal any commands, including Forth itself, from being accessed by the
end user, once the application is complete.

Now Forth’s methodology becomes clear. Forth programming consists of
extending the root language toward the application, providing new
commands that can be used to describe the problem at hand.

Programming languages designed especially for particular applications
such as robotics, inventory control, statistics, etc., are known as
“application-oriented languages.” Forth is a programming environment for
*creating* app-li-ca-tion-oriented
languages. (That last sentence may be the most succinct description of
Forth that you’ll find.)

In fact, you shouldn’t write any serious application in Forth; as a
language it’s simply not powerful enough. What you *should* do is write
your own language in Forth (lexicons) to model your understanding of the
problem, in which you can elegantly describe its solution.

Hide From Whom?
===============

Because modern mainstream languages give a slightly different meaning to
the phrase “information-hiding,” we should clarify. From what, or whom
are we hiding information?

The newest traditional languages (such as Modula 2) bend over backwards
to ensure that modules hide internal routines and data structures from
other modules. The goal is to achieve module independence (a minimum
coupling). The fear seems to be that modules strive to attack each other
like alien antibodies. Or else, that evil bands of marauding modules are
out to clobber the precious family data structures.

This is *not* what we’re concerned about. The purpose of hiding
information, as we mean it, is simply to minimize the effects of a
possible design-change by localizing things that might change within
each component.

Forth programmers generally prefer to keep the program under their own
control and not to employ any techniques to physically hide data
structures. (Nevertheless a brilliantly simple technique for adding
Modula-type modules to Forth has been implemented, in only three lines
of code, by Dewey Val Shorre
[shorre71]_ ).

Hiding the Construction of Data Structures
==========================================

We’ve
noted two inventions of Forth that make possible the methodology we’ve
described—implicit calls and implicit data
passing. A third feature
allows the data structures within a component to be described in terms
of previously-defined components. This feature is direct access memory.

Suppose we define a variable called APPLES, like
this:

.. code-block:: none
   
   VARIABLE APPLES

..

We can store a number into this variable to indicate how many apples we
currently have:

.. code-block:: none
   
   20 APPLES !

..

We can display the contents of the variable:

.. code-block:: none
   
   APPLES ? **20 ok**

..

We can up the count by one:

.. code-block:: none
   
   1 APPLES +!

..

(The newcomer can study the mechanics of these phrases in Appendix A.)

The word APPLES has but one function: to put on the stack the *address*
of the memory location where the tally of apples is kept. The tally can
be thought of as a “thing,” while the words that set the tally, read the
tally, or increment the tally can be considered as “actions.”

Forth conveniently separates “things” from “actions” by allowing
addresses of data structures to be passed on the stack and providing the
“fetch” and “store” commands.

We’ve discussed the importance of designing around things that may
change. Suppose we’ve written a lot of code using this variable APPLES.
And now, at the eleventh hour, we discover that we must keep track of
two different kinds of apples, red and green!

We needn’t wring our hands, but rather remember the function of APPLES:
to provide an address. If we need two separate tallies, APPLES can
supply two different addresses depending on which kind of apple we’re
currently talking about. So we define a more complicated version of
APPLES as follows:

.. code-block:: none
   
   VARIABLE COLOR  ( pointer to current tally)
   VARIABLE REDS  ( tally of red apples)
   VARIABLE GREENS  ( tally of green apples)
   : RED  ( set apple-type to RED)  REDS COLOR ! ;
   : GREEN  ( set apple-type to GREEN)  GREENS COLOR ! ;
   : APPLES  (  -- adr of current apple tally)  COLOR @ ;

..

.. figure:: fig1-10.png
   :name: fig1-10
   :alt: Changing the indirect pointer.

   Changing the indirect pointer.

..

Here we’ve redefined APPLES. Now it fetches the
contents of a variable called COLOR. COLOR is a pointer, either to the
variable REDS or to the variable GREENS. These two variables are the
real tallies.

If we first say RED, then we can use APPLES to refer to red apples. If
we say GREEN, we can use it to refer to green apples
( :numref:`fig1-10` ).

We didn’t need to change the syntax of any existing code that uses
APPLES. We can still say

.. code-block:: none
   
   20 APPLES !

..

and

.. code-block:: none
   
   1 APPLES +!

..

Look again at what we did. We changed the definition of APPLES from that
of a variable to a colon definition, without affecting its usage. Forth
allows us to hide the details of how APPLES is defined from the code
that uses it. What appears to be “thing” (a variable) to the original
code is actually defined as an “action” (a colon definition) within the
component.

Forth encourages the use of abstract data types by allowing data
structures to be defined in terms of lower level components. Only Forth,
which eliminates the CALLs from procedures, which allows addresses and
data to be implicitly passed via the stack, and which provides direct
access to memory locations with @ and !, can offer this level of
information-hiding.

Forth pays little attention to whether something is a data structure or
an algorithm. This indifference allows us programmers incredible freedom
in creating the parts of speech we need to describe our applications.

I tend to think of any word which returns an address, such as APPLES, as
a “noun,” regardless of how it’s defined. A word that performs an
obvious action is a “verb.”

Words such as RED and GREEN in our example can only be called
“adjectives” since they modify the function of APPLES. The phrase

.. code-block:: none
   
   RED APPLES ?

..

is different from

.. code-block:: none
   
   GREEN APPLES ?

..

Forth words can also serve as adverbs and prepositions. There’s little
value in trying to determine what part of speech a particular word is,
since Forth doesn’t care anyway. We need only enjoy the ease of
describing an application in natural
terms.

But Is It a High-Level Language?
================================

In our brief technical overview, we noted that traditional high-level
languages broke away from assembly-language by eliminating not only the
*one-for-one* correspondence between commands and machine operations,
but also the *linear* correspondence. Clearly Forth lays claim to the
first difference; but regarding the second, the order of words that you
use in a definition is the order in which those commands are compiled.

Does this disqualify Forth from the ranks of high-level languages?
Before we answer, let’s explore the advantages of the Forth approach.

.. figure:: img1-028.png
   :name: img1-028
   :alt: Two points of view.

   Two points of view.

..

Here\'s what **Charles Moore**, the inventor of Forth, has to say:
    You define each word so that the computer knows what it means. The way
    it knows is that it executes some code as a consequence of being
    invoked. The computer takes an action on every word. It doesn\'t store
    the word away and keep it in mind for later.
    
    In a philosophical sense I think this means that the computer
    "understands" a word. It understands the word ``DUP`` , perhaps
    more profoundly than you do, because there\'s never any question in its
    mind what ``DUP`` means.
    
    The connection between words that have meaning to you and words that
    have meaning to the computer is a profound one. The computer becomes
    the vehicle for communication between human being and concept.

One advantage of the correspondence between source code and machine
execution is the tremendous simplification of the compiler and
interpreter. This simplification improves performance in several ways,
as we’ll see in a later section.

From the standpoint of programming methodology, the advantage to
the Forth approach is
that *new* words and *new* syntaxes can easily be added. Forth cannot be
said to be “looking” for words—it finds words and executes them. If you
add new words Forth will find and execute them as well. There’s no
difference between existing words and words that you add.

What’s more, this “extensibility” applies to all types of words, not
just action-type functions. For instance, Forth allows you to add new
*compiling* words—like ``IF`` and
``THEN`` that provide structured control flow. You can
easily add a case statement or a multiple-exit loop if you need them,
or, just as importantly, take them out if you don’t need them.

By contrast, any language that depends on word order to understand a
statement must “know” all legal words and all legal combinations. Its
chances of including all the constructs you’d like are slim. The
language exists as determined by its manufacturer; you can’t extend its
knowledge.

Laboratory researchers cite flexibility and extensibility as among
Forth’s most important benefits in their environment. Lexicons can be
developed to hide information about the variety of test equipment
attached to the computer. Once this work has been done by a more
experienced programmer, the researchers are free to use their “software
toolbox” of small words to write simple programs for experimentation. As
new equipment appears, new lexicons are added.

Mark Bernstein  has described
the problem of using an off-the-shelf special-purpose procedure library
in the laboratory [bern83]_ : “The computer, not the
user, dominates the experiment.” But with Forth, he writes, “the
computer actually encourages scientists to modify, repair, and improve
the software, to experiment with and characterize their equipment.
Initiative becomes once more the prerogative of the
researcher.”

.. figure:: img1-030.png
   :name: img1-030
   :alt: Two solutions to the problem of security.

   Two solutions to the problem of security.

..

For those purists who believe Forth isn’t fit to be called a high-level
language, Forth makes matters even worse. While strong syntax checking
and data typing are becoming one of the major thrusts of contemporary
programming languages, Forth does almost no syntax checking at all. In
order to provide the kind of freedom and flexibility we have described,
it cannot tell you that you meant to type RED APPLES instead of APPLES
RED. You have just invented syntax!

Yet Forth more than makes up for its omission by letting you compile
each definition, one at a time, with turnaround on the order of seconds.
You discover your mistake soon enough when the definition doesn’t work.
In addition, you can add appropriate syntax checking in your definitions
if you want to.

An artist’s paintbrush doesn’t notify the artist of a mistake; the
painter will be the judge of that. The chef’s skillet and the composer’s
piano remain simple and yielding. Why let a programming language try to
out think you?

So is Forth a high-level language? On the question of syntax checking,
it strikes out. On the question of abstraction and power, it seems to be
of *infinite* level—supporting everything from bit manipulation at an
output port to business
applications.

You decide. (Forth doesn’t care.)

The Language of Design
======================

Forth
is a design language. To the student of traditional computer science,
this statement is self-contradictory. “One doesn’t design with a
language, one implements with a language. Design precedes
implementation.”

Experienced Forth programmers disagree. In Forth you can write abstract,
design-level code and still be able to test it at any time by taking
advantage of decomposition into lexicons. A component can easily be
rewritten, as development proceeds, underneath any components that use
it. At first the words in a component may print numbers on your terminal
instead of controlling stepper motors. They may print their own names
just to let you know they’ve executed. They may do nothing at all.

Using this philosophy you can write a simple but testable version of
your application, then successively change and refine it until you reach
your goal.

Another factor that makes designing in code possible is that Forth, like
some of the newer languages, eliminates the
“batch-compile”
development sequence (edit-compile-test-edit-compile-test). Because the
feedback is instantaneous, the medium becomes a partner in the creative
process. The programmer using a batch-compiler language can seldom
achieve the productive state of mind that artists achieve when the
creative current flows unhindered.

For these reasons, Forth programmers spend less time planning than their
classical counterparts, who feel righteous about planning. To them, not
planning seems reckless and irresponsible. Traditional environments
force programmers to plan because traditional programming languages do
not readily accommodate change.

Unfortunately, human foresight is limited even under the best
conditions. Too much planning becomes counterproductive.

Of course Forth doesn’t eliminate planning. It allows
prototyping. Constructing a prototype
is a more refined way to plan, just as breadboarding is in electronic
design.

As we’ll see in the next chapter, experimentation proves more reliable
in arriving at the truth than the guesswork of
planning.

The Language of Performance
===========================

Although performance is not the main topic of this book, the newcomer to
Forth should be reassured that its advantages aren’t purely
philosophical. Overall, Forth outdoes all other high-level languages in
speed, capability and compactness.

Speed
-----

Although Forth is an interpretive language, it executes compiled code.
Therefore it runs about ten times faster than interpretive BASIC.

Forth is optimized for the execution of words by means of a technique
known as “threaded code” [bell72]_ ,
[dewar]_ , [kogge82]_ . The penalty for
modularizing into very small pieces of code is relatively slight.

It does not run as fast as assembler code because the inner interpreter
(which interprets the list of addresses that comprise each colon
definition) may consume up to 50% of the run time of primitive words,
depending on the processor.

But in large applications, Forth comes very close to the speed of
assembler. Here are three reasons:

First and foremost, Forth is simple. Forth’s use of a data
stack greatly reduces the performance
cost of passing arguments from word to word. In most languages, passing
arguments between modules is one of the main reasons that the use of
subroutines inhibits performance.

Second, Forth allows you to define words either in high-level or in
machine language. Either way, no special calling sequence is needed. You
can write a new definition in high level and, having verified that it is
correct, rewrite it in assembler without changing any of the code that
uses it. In a typical application, perhaps 20% of the code will be
running 80% of the time. Only the most often used, time-critical
routines need to be machine coded. The Forth system itself is largely
implemented in machine-code definitions, so you’ll have few application
words that need to be coded in assembler.

.. figure:: img1-033.png
   :name: img1-033
   :alt: The best top-down designs of mice and young men.

   The best top-down designs of mice and young men.

..

Third, Forth applications tend to be better designed than those written
entirely in assembler. Forth programmers take advantage of the
language’s prototyping capabilities and try out several algorithms
before settling on the one best suited for their needs. Because Forth
encourages change, it can also be called the language of optimization.

Forth doesn’t guarantee fast applications. It does give the programmer a
creative environment in which to design fast
applications.

Capability
----------

Forth
can do anything any other language can do—usually
easier.

At the low end, nearly all Forth systems include
assemblers. These support
control-structure operators for writing conditionals and loops using
structured programming techniques. They usually allow you to write
interrupts—you can even write interrupt code in high level if desired.

Some Forth systems are multitasked, allowing you to add as many
foreground or background tasks as you want.

Forth can be written to run on top of any operating system such as
RT-11, CP/M, or MS-DOS—or, for those who prefer it, Forth can be written
as a self-sufficient operating system including its own terminal drivers
and disk drivers.

With a Forth cross-compiler or
target
compiler,
you can use Forth to recreate new Forth systems, for the same computer
or for different computers. Since Forth is written in Forth, you have
the otherwise unthinkable opportunity to rewrite the operating system
according to the needs of your application. Or you can transport
streamlined versions of applications over to embedded systems.

Size
----

There are two
considerations here: the size of the root Forth system, and the size of
compiled Forth applications.

The Forth nucleus is very flexible. In an embedded application, the part
of Forth you need to run your application can fit in as little as 1K. In
a full development environment, a multitasked Forth system including
interpreter, compiler, assembler,
editor, operating system, and all other support utilities averages 16K.
This leaves plenty of room for applications. (And some Forths on the
newer processors handle 32-bit addressing, allowing unimaginably large
programs.)

Similarly, Forth compiled applications tend to be very small—usually
smaller than equivalent assembly language programs. The reason, again,
is threaded code. Each reference to a previously defined word, no matter
how powerful, uses only two bytes.

One of the most exciting new territories for Forth is the production of
Forth chips such as the Rockwell R65F11 Forth-based
microprocessor [dumse]_ .
The chip includes not only hardware features
but also the run-time portions of the Forth language and operating
system for dedicated applications. Only Forth’s architecture and
compactness make Forth-based micros
possible.

Summary
=======

Forth has often been characterized as offbeat, totally unlike any other
popular language in structure or in philosophy. On the contrary, Forth
incorporates many principles now boasted by the most contemporary
languages. Structured design, modularity, and information-hiding are
among the buzzwords of the day.

Some newer languages approach even closer to the spirit of Forth. The
language C, for instance, lets the programmer define new functions
either in C or in assembly language, as does Forth. And as with Forth,
most of C is defined in terms of functions.

But Forth extends the concepts of modularity and information-hiding
further than any other contemporary language. Forth even hides the
manner in which words are invoked and the way local arguments are
passed.

The resulting code becomes a concentrated interplay of words, the purest
expression of abstract thought. As a result, Forth programmers tend to
be more productive and to write tighter, more efficient, and better
maintainable code.

Forth may not be the ultimate language. But I believe the ultimate
language, if such a thing is possible, will more closely resemble Forth
than any other contemporary
language.

REFERNCES
=========

.. [dahl72]  O. J. Dahl, E. W. Dijkstra, and C. A. R. Hoare, **Structured Programming,**  London, Academic Press, 1972.
.. [wirth71]  Niklaus Wirth, "Program Development by StepwiseRefinement,"  **Communications of ACM,**  14, No. 4 (1971), 221-27.
.. [stevens74-1]  W. P. Stevens, G. J. Myers, and L. L. Constantine,"Structured Design,"  **IBM Systems Journal,**  Vol. 13, No. 2, 1974.
.. [parnas72]  David L. Parnas, "On the Criteria To Be Used inDecomposing Systems into Modules,"  **Communications of the ACM,** December 1972.
.. [liskov75]  Barbara H. Liskov and Stephen N. Zilles,"Specification Techniques for Data Abstractions,"  **IEEETransactions on Software Engineering,**  March 1975.
.. [parnas79]  David L. Parnas, "Designing Software for Ease ofExtension and Contraction,"  **IEEE Transactions on SoftwareEngineering,**  March 1979.
.. [shorre71]  Dewey Val Shorre, "Adding Modules to Forth,"1980 FORML Proceedings, p. 71.
.. [bern83]  Mark Bernstein, "Programming in the Laboratory,"  unpublished paper, 1983.
.. [bell72]  James R. Bell, "Threaded Code,"  **Communicationsof ACM,**  Vol. 16, No. 6, 370-72.
.. [dewar]  Robert B. K. DeWar, "Indirect Threaded Code," **Communications of ACM,**  Vol. 18, No. 6, 331.
.. [kogge82]  Peter M. Kogge, "An Architectural Trail toThreaded-Code Systems,"  **Computer,**  March, 1982.
.. [dumse]  Randy Dumse, "The R65F11 Forth Chip,"  **ForthDimensions,**  Vol. 5, No. 2, p. 25.

