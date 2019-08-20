
*********************************************
Appendix A: Overview of Forth (For Newcomers)
*********************************************

The Dictionary
==============

Forth is expressed in
words (and numbers) and is separated by spaces:

.. code-block:: none
   
   HAND OPEN  ARM LOWER  HAND CLOSE  ARM RAISE 

..

Such commands may be typed directly from the keyboard, or edited onto
mass storage then “``LOAD``”ed.

All words, whether
included with the system or user-defined, exist in the "dictionary," a
linked list. A “defining word” is used to add new names to the
dictionary. One defining word is ``:`` (pronounced
“colon”), which is used to define a new word in terms of previously
defined words. Here is how one might define a new word called LIFT:

.. code-block:: none
   
   : LIFT   HAND OPEN  ARM LOWER  HAND CLOSE  ARM RAISE ;

..

The ``;`` terminates the definition. The new word ``LIFT``
may now be used instead of the long sequence of words that comprise its
definition.

Forth words can be nested like this indefinitely. Writing a Forth
application consists of building increasingly powerful definitions, such
as this one, in terms of previously defined ones.

Another defining word is
``CODE``, which is used in
place of colon to define a command in terms of machine instructions for
the native processor. Words defined with ``CODE`` are
indistinguishable to the user from words defined with colon.
``CODE`` definitions are needed only for the most
time-critical portions of an applications, if at all.

Data Structures
===============

Still another defining
word is ``CONSTANT``,
which is used like this:

.. code-block:: none
   
   17 CONSTANT SEVENTEEN

..

The new word ``SEVENTEEN`` can now be used in place of the actual number 17.

The defining word
``VARIABLE`` creates a location for temporary
data. ``VARIABLE`` is used like this:

.. code-block:: none
   
   VARIABLE BANANAS

..

This reserves a location which is identified by the name ``BANANAS``.

Fetching the contents of this location is the job of the word
``@`` (pronounced “fetch”). For instance,

.. code-block:: none
   
   BANANAS @

..

fetches the contents of the variable ``BANANAS``. Its counterpart is
``!`` (pronounced “store”), which stores a value into
the location, as in:

.. code-block:: none
   
   100 BANANAS !

..

Forth also provides a word to increment the current value by the given
value; for instance, the phrase

.. code-block:: none
   
   2 BANANAS +!

..

increments the count by two, making it 102.

Forth provides many other data structure
operators, but more
importantly, it provides the tools necessary for the programmer to
create any type of data structure needed for the
application.

The Stack
=========

In Forth, variables and
arrays are used for saving values that may be required by many other
routines and/or at unpredictable times. They are *not* used for the
local passing of data between the definitions. For this, Forth employs a
much simpler mechanism: the data stack.

When you type a number, it goes on the stack. When you invoke a word
which has numeric input, it will take it from the stack. Thus the phrase

.. code-block:: none
   
   17 SPACES

..

will display seventeen blanks on the current output device. “17” pushes
the binary value 17 onto the stack; the word
``SPACES`` consumes it.

A constant also pushes its value onto the stack; thus the phrase:

.. code-block:: none
   
   SEVENTEEN SPACES

..

has the same effect.

The stack operates on a “last-in, first-out” (LIFO) basis. This means
that data can be passed between words in an orderly, modular way,
consistent with the nesting of colon definitions.

For instance, a definition called ``GRID`` might invoke the phrase 17
``SPACES``. This temporary activity on the stack will be transparent to any
other definition that invokes ``GRID`` because the value placed on the stack
is removed before the definition of ``GRID`` ends. The calling definition
might have placed some numbers of its own on the stack prior to calling
``GRID``. These will remain on the stack, unharmed, until ``GRID`` has been
executed and the calling definition continues.

Control Structures
==================

Forth provides all the control
structures needed for
structured, GOTO-less programming.

The syntax of the ``IF THEN`` construct is as follows:

.. code-block:: none
   
   ... ( フラグ ) IF  KNOCK  THEN  OPEN ...

..

The “flag” is a value on the stack, consumed
by IF. A non-zero value indicates true, zero indicates false. A true
flag causes the code after ``IF`` (in this case, the
word ``KNOCK`` ) to be executed. The word ``THEN`` marks
the end of the conditional phrase; execution resumes with the word ``OPEN``.
A false flag causes the code between ``IF`` and
``THEN`` to *not* be executed. In either case, ``OPEN``
will be performed.

The word ``ELSE`` allows an
alternate phrase to be executed in the false case. In the phrase:

.. code-block:: none
   
   ( flag ) IF KNOCK  ELSE  RING  THEN  OPEN ...

..

the word ``KNOCK`` will be performed if the flag is true, otherwise the word
``RING`` will be performed. Either way, execution will continue starting
with ``OPEN``.

Forth also provides for indexed loops in the
form

.. code-block:: none
   
   ( limit) ( index) DO ... LOOP

..

and indefinite loops in the forms:

.. code-block:: none
   
   ... BEGIN  ...  ( flag) UNTIL

..

and

.. code-block:: none
   
   ... BEGIN  ...  ( flag) WHILE ... REPEAT ;

..

For the Whole Story
===================

For a complete introduction to the Forth command set, read *Starting
Forth*, published by
Prentice-Hall.
