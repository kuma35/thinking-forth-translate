
**************************************************
Appendix C: Other Utilities Described in This Book
**************************************************

This appendix is here to help you define some of
the words referred to in this book that may not exist in your system.
Definitions are given in Forth-83 Standard.

From Chapter Four
=================

A definition of ``ASCII``
that will work in ’83 Standard is:

.. code-block:: none
   
   : ASCII  ( -- c)  \  Compile:  c  ( -- )
   \ Interpret:   c   ( -- c)
        BL WORD 1+ C@  STATE @
        IF [COMPILE] LITERAL  THEN ; IMMEDIATE

..

From Chapter Five
=================

The word ``\bs``
can be defined as:

.. code-block:: none
   
   : \  ( skip rest of line)
        >IN @  64 / 1+  64 *  >IN ! ; IMMEDIATE

..

If you decide not to use ``EXIT`` to terminate a
screen, you can define ``\bs S`` as:

.. code-block:: none
   
   : \S   1024 >IN ! ;

..

The word ``FH`` can be
defined simply as:

.. code-block:: none
   
   : FH   \   ( offset -- offset-block)   "from here"
       BLK @ + ;

..

This factoring allows you to use ``FH`` in many ways, e.g.:

.. code-block:: none
   
   : TEST   [ 1 FH ] LITERAL LOAD ;

..

or

.. code-block:: none
   
   : SEE   [ 2 FH ] LITERAL LIST ;

..

A slightly more complicated version of ``FH`` also lets you edit or load a
screen with a phrase such as “14 FH LIST,” relative to the screen that
you just listed ``(SCR)``:

.. code-block:: none
   
   : FH   \   ( offset -- offset-block)   "from here"
        BLK @  ?DUP 0= IF  SCR @  THEN  + ;

..

``BL`` is a
simple constant:

.. code-block:: none
   
   32 CONSTANT BL

..

``TRUE`` and
``FALSE`` can be defined
as:

.. code-block:: none
   
   0 CONSTANT FALSE
   -1 CONSTANT TRUE

..

(Forth’s control words such as ``IF`` and ``UNTIL`` interpret zero as “false”
and any non-zero value as “true.” Before Forth ’83, the convention was
to indicate “true” with the value 1. Starting with Forth ’83,
however, “true” is indicated with hex FFFF, which is the signed number -1 (all bits set).

``WITHIN`` can be defined
in high level like this:

.. code-block:: none
   
   : WITHIN  ( n lo hi+1 -- ?)
        >R  1- OVER <  SWAP R>  < AND ;

..

or

.. code-block:: none
   
   : WITHIN ( n lo hi+1 -- ?)
      OVER -  >R - R> U< ;

..

From Chapter Eight
==================

The implementation of
``LEAP`` will depend on how
your system implements ``DO`` ``LOOP`` s. If ``DO`` keeps two items
on the return stack (the index and the limit),
``LEAP`` must drop both of them plus one more
return-stack item to exit:

.. code-block:: none
   
   : LEAP   R> R> 2DROP  R> DROP ;

..

If ``DO`` keeps *three* items on the return stack, it
must be defined:

.. code-block:: none
   
   : LEAP   R> R> 2DROP  R> R> 2DROP ;

..
