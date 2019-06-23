
********************************
8. Minimizing Control Structures
********************************

Control structures aren’t as important in Forth as
they are in other languages. Forth programmers tend to write very
complex applications in terms of short words, without much emphasis on
``IF THEN`` constructs.

There are several techniques for minimizing control structures. They
include:

-  computing or calculating

-  hiding conditionals through re-factoring

-  using structured exits

-  vectoring

-  redesigning.

In this chapter we’ll examine these techniques for simplifying and
eliminating control structures from your code.

What’s So Bad about Control Structures?
=======================================

Before we begin reeling off our list of tips, let’s pause to examine why
conditionals should be avoided in the first place.

The use of conditional structures adds complexity to your code. The more
complex your code is, the harder it will be for you to read and to
maintain. The more parts a machine has, the greater are its chances of
breaking down. And the harder it is for someone to fix.

**Moore** tells this story:
    I recently went back to a company we had done some work for several years
    ago. They called me in because their program is now five years old, and
    it's gotten very complicated. They've had programmers going in and
    patching things, adding state variables and conditionals. Every statement
    that I recall being a simple thing five years ago, now has gotten very
    complicated.  "If this, else if this, else if this" ... and then the
    simple thing.
    
    Reading that statement now, it's impossible for me to figure out what it's
    doing and why. I'd have to remember what each variable indicated, why it
    was relevant in this case, and then what was happening as a consequence of
    it---or not happening.
    
    It started innocently. They had a special case they needed to worry about.
    To handle that special case, they put a conditional in one place. Then they
    discovered that they also needed one here, and here. And then a few more.
    Each incremental step only added a little confusion to the program. Since
    they were the programmers, they were right on top of it.
    
    The net result was disastrous. In the end they had half a dozen flags.
    Test this one, reset it, set that one, and so on. As a result of this
    condition, you knew you had other conditions coming up you had to look out
    for. They created the logical equivalent of spaghetti code in spite of the
    opportunity for a structured program.
    
    The complexity went far beyond what they had ever intended. But they'd
    committed themselves to going down this path, and they missed the simple
    solution that would have made it all unnecessary---having two words
    instead of one. You either say ``GO`` or you say ``PRETEND``.
    
    In most applications there are remarkably few times when you need to test
    the condition. For instance in a video game, you don't really say "If he
    presses Button A, then do this; if he presses Button B, then do something
    else." You don't go through that kind of logic.
    
    If he presses the button, you do something. What you do is associated with
    the button, not with the logic.
    
    Conditionals aren't bad in themselves---they are an essential construct. But
    a program with a lot of conditionals is clumsy and unreadable. All you can
    do is question each one. Every conditional should cause you to ask, "What
    am I doing wrong?"
    
    What you're trying to do with the conditional can be done in a different
    way. The long-term consequences of the different way are preferable to the
    long-term consequences of the conditional.

Before
we introduce some detailed techniques, let’s look at three approaches to
the use of conditionals in a particular example.
:numref:`fig8-1`, :numref:`fig8-2`, and :numref:`fig8-3` illustrate
three versions of a design for an automatic teller machine.

.. code-block:: none
   :caption: The structured approach.
   :name: fig8-1

   AUTOMATIC-TELLER
   
   IF card is valid DO
      IF card owner is valid DO
         IF request withdrawal DO
            IF authorization code is valid DO
               query for amount
               IF request < current balance DO
                  IF withdrawal < available cash DO
                     vend currency
                     debit account
                  ELSE
                     message
                     terminate session
               ELSE
                  message
                  terminate session
            ELSE
               message
               terminate session
         ELSE
            IF authorization code is valid DO
               query for amount
               accept envelope through hatch
               credit account
            ELSE
               message
               terminate session
      ELSE
         eat card
   ELSE
      message
   END

The first example comes straight out of the School for Structured
Programmers. The logic of the application depends on the correct nesting
of IF statements.

Easy to read? Tell me under what condition the user’s card gets eaten.
To answer, you have to either count ELSEs from the bottom and match them
with the same number of IFs from the top, or use a straightedge.

.. code-block:: none
   :caption: Nesting conditionals within named procedures.
   :name: fig8-2

   AUTOMATIC-TELLER
   
   PROCEDURE READ-CARD
        IF  card is readable  THEN  CHECK-OWNER
             ELSE  eject card  END
   
   PROCEDURE CHECK-OWNER
        IF  owner is valid  THEN  CHECK-CODE
             ELSE  eat card  END
   
   PROCEDURE CHECK-CODE
        IF  code entered matches owner  THEN  TRANSACT
             ELSE message, terminate session  END
   
   PROCEDURE TRANSACT
        IF requests withdrawal  THEN  WITHDRAW
             ELSE  DEPOSIT END
   
   PROCEDURE WITHDRAW
        Query
        If  request &(&le&) current balance  THEN  DISBURSE  END
   
   PROCEDURE DISBURSE
        IF disbursement &(&le&) available cash  THEN
              vend currency
              debit account
            ELSE  message  END
   
   PROCEDURE DEPOSIT
        accept envelope
        credit account

The second version, :numref:`fig8-2`, shows the improvement
that using many small, named procedures can have on readability. The
user’s card is eaten if the owner is not valid.

But even with this improvement, the design of each word depends
completely on the *sequence* in which the tests must be performed. The
supposedly “highest” level procedure is burdened with eliminating the
worst-case, most trivial kind of event. And each test becomes
responsible for invoking the next test.

.. code-block:: none
   :caption: Refactoring and/or eliminating conditionals.
   :name: fig8-3

   AUTOMATIC-TELLER
   
   : RUN
        READ-CARD  CHECK-OWNER  CHECK-CODE  TRANSACT  ;
   
   : READ-CARD
        valid code sequence NOT readable  IF  eject card  QUIT
           THEN ;
   
   : CHECK-OWNER
        owner is NOT valid  IF  eat card  QUIT  THEN ;
   
   : CHECK-CODE
        code entered MISmatches owner's code  IF  message  QUIT
           THEN ;
   
   : READ-BUTTON ( -- adr-of-button's-function)
        ( device-dependent primitive) ;
   
   : TRANSACT
        READ-BUTTON  EXECUTE ;
   
   1 BUTTON WITHDRAW
   
   2 BUTTON DEPOSIT
   
   : WITHDRAW
        Query
        request &(&le&) current balance  IF  DISBURSE  THEN ;
   
   : DISBURSE
        disbursement &(&le&) available cash  IF
               vend currency
               debit account
             ELSE  message  THEN  ;
   
   : DEPOSIT
        accept envelope
        credit account ;

The third version comes closest to the promise of Forth. The highest
level word expresses exactly what’s happening conceptually, showing only
the main path. Each of the subordinate words has its own error exit, not
cluttering the reading of the main word. One test does not have to
invoke the next test.

Also ``TRANSACT`` is designed around the fact that the user will make
requests by pressing buttons on a keypad. No conditions are necessary.
One button will initiate a withdrawal, another a deposit. This approach
readily accommodates design changes later, such as the addition of a
feature to transfer funds. (And this approach does not thereby become
dependent on hardware. Details of the interface to the keypad may be
hidden within the keypad lexicon, ``READ-BUTTON`` and ``BUTTON``.)

Of course, Forth will allow you to take any of the three approaches.
Which do you prefer?

How to Eliminate Control Structures
===================================

In this section we’ll study numerous techniques for simplifying or
avoiding conditionals. Most of them will produce code that is more
readable, more maintainable, and more efficient. Some of the techniques
produce code that is more efficient, but not always as readable.
Remember, therefore: Not all of the tips will be applicable in all
situations.

Using the Dictionary
--------------------

.. tip::

   Give each function its own definition.

By using the Forth dictionary properly, we’re not actually eliminating
conditionals; we’re merely factoring them out from our application code.
The Forth dictionary is a giant string case statement. The match and
execute functions are hidden within the Forth system.

**Moore**:
    In my accounting package, if you receive a check from somebody, you type
    the amount, the check number, the word ``FROM``, and the person's
    name:
    
    .. code-block:: none
       
       200.00 127 FROM ALLIED
    
    The word ``FROM`` takes care of that situation. If you want to bill
    someone, you type the amount, the invoice number, the word ``BILL``
    and the person's name:

    .. code-block:: none
   
       1000.00 280 BILL TECHNITECH

    ... One word for each situation. The dictionary is making the decision.\medskip

This notion pervades Forth itself. To add a pair of single-length
numbers we use the command ``+``. To add a pair of double-length numbers we
use the command ``D+``. A less efficient, more complex approach would be a
single command that somehow “knows” which type of numbers are being
added.

Forth is efficient because all these words— ``FROM`` and ``BILL`` and ``+`` and
``D+`` —can be implemented without any need for testing and branching.

.. tip::

   Use dumb words.

This isn’t advice for TV writers. It’s another instance of using the
dictionary. A “dumb” word is one that is not state-dependent, but
instead, has the same behavior all the time (“referentially
transparent”).

A dumb word is unambiguous, and therefore, more trustworthy.

A few common Forth words have been the source of controversy recently
over this issue. One such word is ``."`` which prints a string. In its
simplest form, it’s allowed only inside a colon definition:

.. code-block:: none
   
   : TEST   ." THIS IS A STRING " ;

Actually, this version of the word does *not* print a string. It
*compiles* a string, along with the address of another definition that
does the printing at run time.

This is the dumb version of the word. If you use it outside a colon
definition, it will uselessly compile the string, not at all what a
beginner might expect.

To solve this problem, the FIG model added a test inside ``."`` that
determined whether the system was currently compiling or interpreting.
In the first case, ``."`` would compile the string and the address of the
primitives; in the second case it would ``TYPE`` it.

``."`` became two completely different words housed together in one
definition with an ``IF ELSE THEN`` structure. The flag that indicates
whether Forth is compiling or interpreting is called ``STATE``. Since the ."
depends on ``STATE``, it is said to be “``STATE``\ -dependent,” literally.

The command *appeared* to behave the same inside and outside a colon
definition. This duplicity proved useful in afternoon introductions to
Forth, but the serious student soon learned there’s more to it than
that.

Suppose a student wants to write a new word called
``B."`` (for “bright-dot-quote”) to display a string
in bright characters on her display, to be used like this:

.. code-block:: none
   
   ." INSERT DISK IN "  B." LEFT "  ." DRIVE "

She might expect to define ``B."`` as

.. code-block:: none
   
   : B."   BRIGHT  ."  NORMAL ;

that is, change the video mode to bright, print the string, then reset
the mode to normal.

She tries it. Immediately the illusion is destroyed; the deception is
revealed; the definition won’t work.

To solve her problem, the programmer will have to study the definition
of ``(.")`` in her own system. I’m not going to get sidetracked here with
explaining how ``(.")`` works—my point is that smartness isn’t all it
appears to be.

Incidentally, there’s a different syntactical approach to our student’s
problem, one that does not require having two separate words, ``."`` and ``B."``
to print strings. Change the system’s ``(.")`` so that it always sets the
mode to normal after typing, even though it will already be normal most
of the time. With this syntax, the programmer need merely precede the
emphasized string with the simple word ``BRIGHT``.

.. code-block:: none
   
   ." INSERT DISK IN "  BRIGHT ." LEFT "  ." DRIVE "

The ’83 Standard now specifies a dumb ``."`` and, for those cases where an
interpretive version is wanted, the new word .( has been added. Happily,
in this new standard we’re using the dictionary to make a decision by
having two separate words.

The word ``’`` (tick) has a similar history.
It was ``STATE``\ -dependent in fig-Forth, and is now
dumb in the ’83 Standard. Tick shares with dot-quote the characteristic
that a programmer might want to reuse either of these words in a
higher-level definition and have them behave in the same way they do
normally.

.. tip::

   Words should not depend on ``STATE`` if a programmer might ever want
   to invoke them from within a higher-level definition and expect them to
   behave as they do interpretively.

``ASCII`` works well as a ``STATE``\ -dependent word, and so does
``MAKE``. (See :doc:`Appendix C<appendixc>`.)

Nesting and Combining Conditionals
----------------------------------

.. tip::

   Don't test for something that has already been excluded.

Take this example, please:

.. code-block:: none
   
   : PROCESS-KEY
      KEY  DUP  LEFT-ARROW  =  IF CURSOR-LEFT  THEN
           DUP  RIGHT-ARROW =  IF CURSOR-RIGHT THEN
           DUP  UP-ARROW    =  IF CURSOR-UP    THEN
                DOWN-ARROW  =  IF CURSOR-DOWN  THEN ;

This version is inefficient because all four tests must be made
regardless of the outcome of any of them. If the key pressed was the
left-arrow key, there’s no need to check if it was some other key.

Instead, you can nest the conditionals, like this:

.. code-block:: none
   
   : PROCESS-KEY
      KEY  DUP  LEFT-ARROW  =  IF CURSOR-LEFT  ELSE
           DUP  RIGHT-ARROW =  IF CURSOR-RIGHT ELSE
           DUP  UP-ARROW    =  IF CURSOR-UP    ELSE
                                  CURSOR-DOWN
              THEN THEN THEN  DROP ;

.. tip::

   Combine booleans of similar weight.

Many instances of doubly-nested
``IF``  ``THEN`` structures can be
simplified by combining the flags with logical operators before making
the decision. Here’s a doubly-nested test:

.. code-block:: none
   
   : ?PLAY   SATURDAY? IF  WORK FINISHED? IF
        GO PARTY  THEN  THEN ;

The above code uses nested ``IF``\ s to make sure that
it’s both Saturday and the chores are done before it boogies on down.
Instead, let’s combine the conditions logically and make a single
decision:

.. code-block:: none
   
   : ?PLAY   SATURDAY?  WORK FINISHED? AND  IF
      GO PARTY  THEN ;

It’s simpler and more readable.

The logical “or” situation, when implemented with
``IF``  ``THEN``\ s, is even clumsier:

.. code-block:: none
   
   : ?RISE    PHONE RINGS? IF  UP GET  THEN
        ALARM-CLOCK RINGS?  IF UP GET THEN ;

This is much more elegantly written as

.. code-block:: none
   
   : ?RISE  PHONE RINGS?  ALARM RINGS? OR  IF  UP GET THEN ;

One exception to this rule arises when the speed penalty for checking
some of the conditions is too great.

We might write

.. code-block:: none
   
   : ?CHOW-MEIN   BEAN-SPROUTS?  CHOW-MEIN RECIPE?  AND IF
      CHOW-MEIN PREPARE  THEN ;

But suppose it’s going to take us a long time to hunt through our recipe
file to see if there’s anything on chow mein. Obviously there’s no point
in undertaking the search if we have no bean sprouts in the fridge. It
would be more efficient to write

.. code-block:: none
   
   : ?CHOW-MEIN   BEAN-SPROUTS? IF  CHOW-MEIN RECIPE? IF
      CHOW-MEIN PREPARE THEN   THEN ;

We don’t bother looking for the recipe if there are no sprouts.

Another exception arises if any term is probably not true. By
eliminating such a condition first, you avoid having to try the other
conditions.

.. tip::

   When multiple conditions have dissimilar weights (in likelihood or
   calculation time) nest conditionals with the term that is least likely
   to be true or easiest to calculate on the outside.

Trying to improve performance in this way is more difficult with the OR
construct. For instance, in the definition

.. code-block:: none
   
   : ?RISE  PHONE RINGS?  ALARM RINGS? OR  IF  UP GET THEN ;

we’re testing for the phone and the alarm, even though only one of them
needs to ring for us to get up. Now suppose it were much more difficult
to determine that the alarm clock was ringing. We could write

.. code-block:: none
   
   : ?RISE   PHONE RINGS? IF  UP GET  ELSE
        ALARM-CLOCK RINGS?  IF UP GET THEN THEN  ;

If the first condition is true, we don’t waste time evaluating the
second. We have to get up to answer the phone anyway.

The repetition of ``UP GET`` is ugly—not nearly as readable as the solution
using ``OR``\ —but in some cases
desirable.

Choosing Control Structures
---------------------------

.. tip::

   The most elegant code is that which most closely matches the problem.
   Choose the control structure that most closely matches the control-flow
   problem.

Case Statements
~~~~~~~~~~~~~~~

A particular class of problem involves selecting one of several possible
paths of execution according to a numeric argument. For instance, we
want the word .SUIT to take a number representing a suit of playing
cards, 0 through 3, and display the name of the suit. We might define
this word using nested
``IF``  ``ELSE``  ``THEN``\ s,
like this:

.. code-block:: none
   
   : .SUIT ( suit -- )
     DUP  O=  IF ." HEARTS "   ELSE
     DUP  1 = IF ." SPADES "   ELSE
     DUP  2 = IF ." DIAMONDS " ELSE
                 ." CLUBS "
     THEN THEN THEN  DROP ;

We can solve this problem more elegantly by using a “case statement.”

Here’s the same definition, rewritten using the “Eaker case statement”
format, named after Dr. Charles E. Eaker, the gentleman who proposed it
[eaker]_.

.. code-block:: none
   
   : .SUIT ( suit -- )
     CASE
     O OF   ." HEARTS "    ENDOF
     1 OF   ." SPADES "    ENDOF
     2 OF   ." DIAMONDS "  ENDOF
     3 OF   ." CLUBS "     ENDOF     ENDCASE ;

The case statement’s value lies exclusively in its readability and
writeability. There’s no efficiency improvement either in object memory
or in execution speed. In fact, the case statement compiles much the
same code as the nested ``IF``  ``THEN`` statements. A case
statement is a good example of compile-time factoring.

Should all Forth systems include such a case statement? That’s a matter
of controversy. The problem is twofold. First, the instances in which a
case statement is actually needed are rare—rare enough to question its
value. If there are only a few cases, a nested
``IF``  ``ELSE``  ``THEN`` construct will work as well, though perhaps
not as readably. If there are many cases, a decision table is more
flexible.

Second, many case-like problems are not quite appropriate for the case
structure. The Eaker case statement assumes that you’re testing for
equality against a number on the stack. In the instance of ``.SUIT``, we
have contiguous integers from zero to three. It’s more efficient to use
the integer to calculate an offset and directly jump to the right code.

In the case of our Tiny Editor, later in this chapter, we have not one,
but two, dimensions of possibilities. The case statement doesn’t match
that problem either.

Personally, I consider the case statement an elegant solution to a
misguided problem: attempting an algorithmic expression of what is more
aptly described in a decision table.

A case statement ought to be part of the application when useful, but
not part of the
system.

Looping Structures
~~~~~~~~~~~~~~~~~~

The right looping structure can eliminate extra conditionals.

**Moore**:
    Many times conditionals are used to get out of loops. That particular use
    can be avoided by having loops with multiple exit points.
    
    This is a live topic, because of the multiple ``WHILE`` construct
    which is in poly Forth but hasn't percolated up to Forth \'83. It's a
    simple way of defining multiple ``WHILE``\ s in the same ``REPEAT``.

    Also Dean Sanderson [ of Forth, Inc.]
    has invented a new construct that introduces two exit points to a
    ``DO``  ``LOOP``. Given that construction you'll
    have fewer tests. Very often I leave a truth value on the stack, and if
    I'm leaving a loop early, I change the truth value to remind myself that I
    left the loop early. Then later I'll have an ``IF`` to see whether I
    left the loop early, and it's just clumsy.
    
    Once you've made a decision, you shouldn't have to make it again. With the
    proper looping constructs you won't need to remember where you came from,
    so more conditionals will go away.
    
    This is not completely popular because it's rather unstructured. Or perhaps
    it is elaborately structured. The value is that you get simpler programs.
    And it costs nothing.

Indeed, this is a live topic. As of this writing it’s too early to make
any specific proposals for new loop constructs. Check your system’s
documentation to see what it offers in the way of exotic looping
structures. Or, depending on the needs of your application, consider
adding your own conditional constructs. It’s not that hard in Forth.

I’m not even sure whether this use of multiple exits doesn’t violate the
doctrine of structured programming. In a ``BEGIN``  ``WHILE``  ``REPEAT``
loop with multiple ``WHILE``\ s, all the exits bring you
to a common “continue” point: the ``REPEAT``. But with
Sanderson’s  construct, you can
exit the loop by jumping *past* the end of the loop, continuing at an
``ELSE``. There are two possible “continue” points.

This is “less structured,” if we can be permitted to say that. And yet
the definition will always conclude at its semicolon and return to the
word that invoked it. In that sense it is well-structured; the module
has one entry point and one exit point.

When you want to execute special code only if you did *not* leave the
loop prematurely, this approach seems the most natural structure to use.
(We’ll see an example of this in a later section, “Using Structured
Exits.”) 

.. tip::

   Favor counts over terminators.

Forth handles strings by saving the length of the string in the first
byte. This makes it easier to type, move, or do practically anything
with the string. With the address and count on the stack, the definition
of ``TYPE`` can be coded:

.. code-block:: none
   
   : TYPE  ( a #)  OVER + SWAP DO  I C@ EMIT  LOOP ;

(Although ``TYPE`` really ought to be written in machine code.)

This definition uses no overt conditional. ``LOOP``
actually hides the conditional since each loop checks the index and
returns to ``DO`` if it has not yet reached the limit.

If a delimiter were used, let’s say ASCII null (zero), the definition
would have to be written:

.. code-block:: none
   
   : TYPE  ( a)  BEGIN DUP C@  ?DUP WHILE  EMIT  1+
      REPEAT  DROP ;

An extra test is needed on each pass of the loop.
(``WHILE`` is a conditional operator.)

Optimization note: The use of ``?DUP`` in this
solution is expensive in terms of time because it contains an extra
decision itself. A faster definition would be:

.. code-block:: none
   
   : TYPE  ( a)  BEGIN DUP C@  DUP WHILE EMIT 1+
       REPEAT  2DROP ;


The ’83 Standard applied this principle to
``INTERPRET``  which
now accepts a count rather than looking for a terminator.

The flip side of this coin is certain data structures in which it’s
easiest to *link* the structures together. Each record points to the
next (or previous) record. The last (or first) record in the chain can
be indicated with a zero in its link field.

If you have a link field, you have to fetch it anyway. You might as well
test for zero. You don’t need to keep a counter of how many records
there are. If you decrement a counter to decide whether to terminate,
you’re making more work for yourself. (This is the technique used to
implement Forth’s dictionary as a linked
list.)

Calculating Results
~~~~~~~~~~~~~~~~~~~

.. tip::

   Don't decide, calculate.

Many times conditional control structures are applied mistakenly to
situations in which the difference in outcome results from a difference
in numbers. If numbers are involved, we can calculate them.
(In :doc:`Chapter Four<chapter4>` see the section called
“Calculations vs. Data Structures vs. Logic.”) 

.. tip::

   Use booleans as hybrid values.

This is a fascinating corollary to the previous tip, “Don’t decide,
calculate.” The idea is that booleans, which the computer represents as
numbers, can efficiently be used to effect numeric decisions. Here’s one
example, found in many Forth systems:

.. code-block:: none
   
   : S>D  ( n -- d)  \ sign extend s to d
        DUP O<  IF -1  ELSE  O THEN ;

(The purpose of this definition is to convert a single-length number to
double-length. A double-length number is represented as two 16-bit
values on the stack, the high-order part on top. Converting a positive
integer to double-length merely means adding a zero onto the stack, to
represent its high-order part. But converting a negative integer to
double-length requires “sign extension;” that is, the high-order part
should be all ones.)

The above definition tests whether the single-length number is negative.
If so, it pushes a negative one onto the stack; otherwise a zero. But
notice that the outcome is merely arithmetic; there’s no change in
process. We can take advantage of this fact by using the boolean itself:

.. code-block:: none
   
   : S>D  ( n -- d)  \ sign extend s to d
        DUP  O< ;

This version pushes a zero or negative one onto the stack without a
moment’s (in)decision.

(In pre-1983 systems, the definition would be:

.. code-block:: none
   
   : S>D  ( n -- d)  \ sign extend s to d
        DUP  O< NEGATE ;

See :doc:`Appendix C<appendixc>`.)

We can do even more with “hybrid values”: 

.. tip::

   To effect a decision with a numeric outcome, use ``AND``.

In the case of a decision that produces either zero or a non-zero
“:math:`n`,” the traditional phrase

.. code-block:: none
   
   ( ? ) IF  n  ELSE  O  THEN


is equivalent to the simpler statement

.. code-block:: none
   
   ( ? )  n AND

Again, the secret is that “true” is represented by :math:`-1` (all ones)
in \’83 Forth systems. ``AND``\ ing “:math:`n`” with the
flag will either produce “:math:`n`” (all bits intact) or “:math:`0`”
(all bits cleared).

To restate with an example:

.. code-block:: none
   
   ( ? )  IF  200  ELSE  O  THEN

is the same as

.. code-block:: none
   
   ( ? )  200 AND

Take a look at this example:

.. code-block:: none
   
   n  a b <  IF  45 +  THEN

This phrase either adds 45 to “:math:`n`” or doesn’t, depending on the
relative sizes of “:math:`a`” and “:math:`b`.” Since “adding 45 or not”
is the same as “adding 45 or adding 0,” the difference between the two
outcomes is purely numeric. We can rid ourselves of a decision, and
simply compute:

.. code-block:: none
   
   n  a b <  45 AND  +

**Moore**:
    The "``45 AND``" is faster than the ``IF``, and certainly more
    graceful. It's simpler. If you form a habit of looking for instances where
    you're calculating this value from that value, then usually by doing
    arithmetic on the logic you get the same result more cleanly.
    
    I don't know what you call this. It has no terminology; it's merely doing
    arithmetic with truth values. But it's perfectly valid, and someday boolean
    algebra and arithmetic expressions will accommodate it.
    
    In books you often see a lot of piece-wise linear approximations that fail to
    express things clearly. For instance the expression

    .. code-block:: none
   
       x = O for t < O
       x = 1 for t &(&ge&) O

    This would be equivalent to
    
    .. code-block:: none
   
       t  O<  1 AND
    
    as a single expression, not a piece-wise expression.

I call these flags “hybrid values” because they are booleans (truth
values) being applied as data (numeric values). Also, I don’t know what
else to call them.

We can eliminate numeric ELSE clauses as well (where both results are
non-zero), by factoring out the difference between the two results. For
instance,

.. code-block:: none
   
   : STEPPERS  'TESTING? @  IF 150 ELSE 151  THEN  LOAD ;

can be simplified to

.. code-block:: none
   
   : STEPPERS   150  'TESTING? @  1 AND +  LOAD ;

This approach works here because conceptually we want to either load
Screen 150, or if testing, the next screen past it.

A Note on Tricks
================

This sort of approach is often labeled a “trick.” In the computing
industry at large, tricks have a bad reputation.

A trick is simply taking advantage of certain properties of operation.
Tricks are used widely in engineering applications. Chimneys eliminate
smoke by taking advantage of the fact that heat rises. Automobile tires
provide traction by taking advantage of gravity.

Arithmetic Logic Units (ALUs) take advantage of the fact that
subtracting a number is the same as adding its two’s complement.

These tricks allow simpler, more efficient designs. What justifies their
use is that the assumptions are certain to remain true.

The use of tricks becomes dangerous when a trick depends on something
likely to change, or when the thing it depends on is not protected by
information hiding.

Also, tricks become difficult to read when the assumptions on which
they’re based aren’t understood or explained. In the case of replacing
conditionals with ``AND``, once this technique becomes part of every
programmer’s vocabulary, code can become *more* readable. In the case of
a trick that is specific to a specific application, such as the order in
which data are arranged in a table, the listing must clearly document
the assumption used by the
trick.

.. tip::

   Use ``MIN`` and ``MAX`` for clipping.

Suppose we want to decrement the contents of the variable VALUE, but we
don’t want the value to go below zero:

.. code-block:: none
   
   -1 VALUE +!  VALUE @  -1 = IF  O VALUE !  THEN

This is more simply written:

.. code-block:: none
   
   VALUE @  1-  O MAX  VALUE !

In this case the conditional is factored within the word ``MAX``.

Using Decision Tables
---------------------

.. tip::

   Use decision tables.

We introduced these in Chapter Two. A decision table is a structure that
contains either data (a “data table”) or addresses of functions (a
“function table”) arranged according to any number of dimensions. Each
dimension represents all the possible, mutually exclusive states of a
particular aspect of the problem. At the intersection of the “true”
states of each dimension lies the desired element: the piece of data or
the function to be performed.

A decision table is clearly a better choice than a conditional structure
when the problem has multiple dimensions.

One-Dimensional Data Table
~~~~~~~~~~~~~~~~~~~~~~~~~~

Here’s an example of a simple, one-dimensional data table. Our
application has a flag called ``’FREEWAY?`` which is true when we’re
referring to freeways, false when we’re referring to city streets.

Let’s construct the word ``SPEED-LIMIT``, which returns the speed limit
depending on the current state. Using
``IF``  ``THEN`` we would write:

.. code-block:: none
   
   : SPEED-LIMIT  ( -- speed-limit)
        'FREEWAY? @  IF  55  ELSE  25  THEN ;

We might eliminate the ``IF``  ``THEN`` by using a hybrid
value with ``AND``:

.. code-block:: none
   
   : SPEED-LIMIT   25  'FREEWAY? @  30 AND + ;

But this approach doesn’t match our conceptual model of the problem and
therefore isn’t very readable.

Let’s try a data table. This is a one-dimensional table, with only two
elements, so there’s not much to it:

.. code-block:: none
   
   CREATE LIMITS   25 ,  55 ,

The word ``SPEED-LIMIT?`` now must apply the boolean to offset into the data
table:

.. code-block:: none
   
   : SPEED-LIMIT  ( -- speed-limit)
        LIMITS  'FREEWAY? @  2 AND  +  @ ;

Have we gained anything over the
``IF``  ``THEN`` approach? Probably
not, with so simple a problem.

What we have done, though, is to factor out the decision-making process
from the data itself. This becomes more cost-effective when we have more
than one set of data related to the same decision. Suppose we also had

.. code-block:: none
   
   CREATE #LANES   4 ,  10 ,

representing the number of lanes on a city street and on a freeway. We
can use identical code to compute the current number of lanes:

.. code-block:: none
   
   : #LANES?  ( -- #lanes)
        #LANES  'FREEWAY? @  2 AND  +  @ ;

Applying techniques of factoring, we simplify this to:

.. code-block:: none
   
   : ROAD  ( for-freeway for-city ) CREATE , ,
        DOES> ( -- data )  'FREEWAY? @  2 AND  +  @ ;
   55 25 ROAD SPEED-LIMIT?
   10  4 ROAD #LANES?

Another example of the one-dimensional data table is the “superstring”
(*Starting Forth*, Chapter Ten).

Two-Dimensional Data Table
~~~~~~~~~~~~~~~~~~~~~~~~~~

In :doc:`Chapter Two<chapter2>` we presented a phone-rate problem.
:numref:`fig8-4`  gives one solution to the problem, using a
two-dimensional data structure.

A solution to the phone rate problem.

.. code-block:: none
   :caption: Screen #103
   :name: fig8-4
   :linenos:

   \ Telephone rates                                       03/30/84
   CREATE FULL     30 , 20 , 12 ,
   CREATE LOWER    22 , 15 , 10 ,
   CREATE LOWEST   12 ,  9 ,  6 ,
   VARIABLE RATE   \ points to FULL, LOWER or LOWEST
                   \ depending on time of day
   FULL RATE !  \ for instance
   : CHARGE   ( o -- ) CREATE ,
      DOES>  ( -- rate )  @  RATE @ +  @ ;
   O CHARGE 1MINUTE   \ rate for first minute
   2 CHARGE +MINUTES  \ rate for each additional minute
   4 CHARGE /MILES    \ rate per each 100 miles

.. code-block:: none
   :caption: Screen #104
   :linenos:

   \ Telephone rates                                       03/30/84
   VARIABLE OPERATOR?  \ 90 if operator assisted; else O
   VARIABLE #MILES  \ hundreds of miles
   : ?ASSISTANCE  ( direct-dial charge -- total charge)
      OPERATOR? @  + ;
   : MILEAGE  ( -- charge )  #MILES @  /MILES * ;
   : FIRST  ( -- charge )  1MINUTE  ?ASSISTANCE  MILEAGE + ;
   : ADDITIONAL  ( -- charge)  +MINUTES  MILEAGE + ;
   : TOTAL ( #minutes -- total charge)
      1- ADDITIONAL *  FIRST + ;

In this problem, each dimension of the data table consists of three
mutually exclusive states. Therefore a simple boolean (true/false) is
inadequate. Each dimension of this problem is implemented in a different
way.

The current rate, which depends on the time of day, is stored as an
address, representing one of the three rate-structure sub-tables. We can
say

.. code-block:: none
   
   FULL RATE !

or

.. code-block:: none
   
   LOWER RATE !

etc.

The current charge, either first minute, additional minute, or per mile,
is expressed as an offset into the table (0, 2, or 4).

An optimization note: we’ve implemented the two-dimensional table as a
set of three one-dimensional tables, each pointed to by RATE. This
approach eliminates the need for a multiplication that would otherwise
be needed to implement a two-dimensional structure. The multiplication
can be prohibitively slow in certain
cases.

Two-Dimensional Decision Table
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We’ll hark back to our Tiny Editor example in :doc:`Chapter Three<chapter3>` to
illustrate a two-dimensional decision table.

In :numref:`fig8-5`  we’re constructing a table of functions to
be performed when various keys are pressed. The effect is similar to
that of a case statement, but there are two modes, Normal Mode and
Insert Mode. Each key has a different behavior depending on the current
mode.

The first screen implements the change of the modes. If we invoke

.. code-block:: none
   
   NORMAL MODE# !

we’ll go into Normal Mode.

.. code-block:: none
   
   INSERTING MODE# !

enters Inserting Mode.

The next screen constructs the function table, called ``FUNCTIONS``. The
table consists of the ASCII value of a key followed by the address of
the routine to be performed when in Normal Mode, followed by the address
of the routine to be performed when in Insert Mode, when that key is
pressed. Then comes the second key, followed by the next pair of
addresses, and so on.

In the third screen, the word ``’FUNCTION`` takes a key value, searches
through the ``FUNCTIONS`` table for a match, then returns the address of the
cell containing the match. (We preset the variable ``MATCHED`` to point to
the last row of the table—the functions we want when *any* character is
pressed.)

The word ``ACTION`` invokes ``’FUNCTION``, then adds the contents of the
variable ``MODE#``. Since MODE# will contain either a 2 or a 4, by adding
this offset we’re now pointing into the table at the address of the
routine we want to perform. A simple

.. code-block:: none
   
   @ EXECUTE

will perform the routine (or ``@EXECUTE`` if you have it).

In fig-Forth, change the definition of ``IS`` to:

.. code-block:: none
   
   : IS   [COMPILE] '  CFA , ;

Implementation of the Tiny Editor.
   
.. code-block:: none
   :caption: Screen #30
   :name: fig8-5
   :linenos:

   \ Tiny Editor
   2 CONSTANT NORMAL     \ offset in FUNCTIONS
   4 CONSTANT INSERTING  \        "
   6 CONSTANT /KEY       \ bytes in table for each key
   VARIABLE MODE#        \ current offset into table
   NORMAL MODE# !
   : INSERT-OFF   NORMAL    MODE# ! ;
   : INSERT-ON    INSERTING MODE# ! ;
   
   VARIABLE ESCAPE?      \ t=time-to-leave-loop
   : ESCAPE  TRUE ESCAPE? ! ;

.. code-block:: none
   :caption: Screen #31
   :linenos:

   \ Tiny Editor             function table             07/29/83
   : IS   ' , ;  \   function   ( -- )    ( for '83 standard)
   CREATE FUNCTIONS
   \ keys                  normal mode        insert mode
    4 ,  ( ctrl-D)         IS DELETE          IS INSERT-OFF
    9 ,  ( ctrl-I)         IS INSERT-ON       IS INSERT-OFF
    8 ,  ( backspace)      IS BACKWARD        IS INSERT<
   60 ,  ( left arrow)     IS BACKWARD        IS INSERT-OFF
   62 ,  ( right arrow)    IS FORWARD         IS INSERT-OFF
   27 ,  ( return)         IS ESCAPE          IS INSERT-OFF
    O ,  ( no match)       IS OVERWRITE       IS INSERT
   HERE /KEY -  CONSTANT 'NOMATCH  \ adr of no-match key

.. code-block:: none
   :caption: Screen #32
   :linenos:

   \ Tiny Editor cont'd                                 07/29/83
   VARIABLE MATCHED
   : 'FUNCTION  ( key -- adr-of-match )  'NOMATCH  MATCHED !
      'NOMATCH FUNCTIONS DO  DUP  I @ =  IF
        I MATCHED !  LEAVE  THEN  /KEY +LOOP  DROP
       MATCHED @ ;
   : ACTION  ( key -- )  'FUNCTION  MODE# @ +  @ EXECUTE ;
   : GO   FALSE ESCAPE? !  BEGIN  KEY ACTION  ESCAPE? @ UNTIL ;

In 79-Standard Forths, use:

.. code-block:: none
   
   : IS   [COMPILE] '  , ;

We’ve also used non-redundancy at compile time in the definition just
below the function table:

.. code-block:: none
   
   HERE /KEY -  CONSTANT 'NOMATCH  \  adr of no-match key

We’re making a constant out of the last row in the function table. (At
the moment we invoke ``HERE``, it’s pointing to the
next free cell after the last table entry has been filled in. Six bytes
back is the last row.) We now have two words:

.. code-block:: none
   
   FUNCTIONS  ( adr of beginning of function table )
   'NOMATCH   ( adr of "no-match" row; these are the
                routines for any key not in the table)

We use these names to supply the addresses passed to ``DO``:

.. code-block:: none
   
   'NOMATCH FUNCTION DO

to set up a loop that runs from the first row of the table to the last.
We don’t have to know how many rows lie in the table. We could even
delete a row or add a row to the table, without having to change any
other piece of code, even the code that searches through the table.

Similarly the constant ``/KEY`` hides information about the number of
columns in the table.

Incidentally, the approach to ``’FUNCTION`` taken in the listing is a
quick-and-dirty one; it uses a local variable to simplify stack
manipulation. A simpler solution that uses no local variable is:

.. code-block:: none
   
   : 'FUNCTION  ( key -- adr of match )
      'NOMATCH SWAP  'NOMATCH FUNCTIONS DO  DUP
         I @ =  IF SWAP DROP I SWAP  LEAVE  THEN
      /KEY +LOOP  DROP ;

(We’ll offer still another solution later in this chapter, under “Using
Structured Exits.”)

Decision Tables for Speed
-------------------------

We’ve stated that if you can calculate a value instead of looking it up
in a table, you should do so. The exception is where the requirements
for speed justify the extra complexity of a table.

Here is an example that computes powers of two to 8-bit precision:

.. code-block:: none
   
   CREATE TWOS
      1 C,  2 C,  4 C,  8 C,  16 C,  32 C,
   : 2**  ( n -- 2-to-the-n)
      TWOS +  C@ ;

Instead of computing the answer by multiplying two times itself
“:math:`n`” times, the answers are all pre-computed and placed in a
table. We can use simple addition to offset into the table and get the
answer.

In general, addition is much faster than multiplication.

**Moore** provides another example:
    If you want to compute trig functions, say for a graphics display, you don't
    need much resolution. A seven-bit trig function is probably plenty. A table
    look-up of 128 numbers is faster than anything else you're going to be able
    to do. For low-frequency function calculations, decision tables are great.
    
    But if you have to interpolate, you have to calculate a function anyway.
    You're probably better off calculating a slightly more complicated function
    and avoiding the table lookup.

Redesigning
-----------

.. tip::

   One change at the bottom can save ten decisions at the top.

In our interview with Moore at the
beginning of the chapter, he mentioned that much conditional testing
could have been eliminated from an application if it had been redesigned
so that there were two words instead of one: “You either say ``GO`` or you
say ``PRETEND``.”

It’s easier to perform a simple, consistent algorithm while changing the
context of your environment than to choose from several algorithms while
keeping a fixed environment.

Recall from Chapter One our example of the word ``APPLES``. This was
originally defined as a variable; it was referred to many times
throughout the application by words that incremented the number of
apples (when shipments arrive), decremented the number (when apples are
sold), and checked the current number (for inventory control).

When it became necessary to handle a second type of apples, the *wrong*
approach would have been to add that complexity to all the
shipment/sales/inventory words. The
*right* approach was the one we took: to add the complexity “at the
bottom”; that is, to APPLES itself.

This principle can be realized in many ways. In :doc:`Chapter Seven<chapter7>`
(under “The State Table”) we used state tables to implement the words ``WORKING``
and ``PRETENDING``, which changed the meaning of a group of variables. Later
in that chapter, we used vectored execution to define ``VISIBLE`` and
``INVISIBLE``, to change the meanings of ``TYPE’``, ``EMIT’``, ``SPACES’`` and ``CR’`` and thereby easily change all the formatting code that uses them.

.. tip::

   Don't test for something that can't possibly happen.

Many contemporary programmers are error-checking-happy.

There’s no need for a function to check an argument passed by another
component in the system. The calling program should bear the
responsibility for not exceeding the limits of the called component.

.. tip::

   Reexamine the algorithm.

**Moore**:
    A lot of conditionals arise from fuzzy thinking about the problem. In
    servo-control theory, a lot of people think that the algorithm for the
    servo ought to be different when the distance is great than when it is
    close. Far away, you're in slew mode; closer to the target you're in
    decelerate mode; very close you're in hunt mode. You have to test how far
    you are to know which algorithm to apply.
    
    I've worked out a non-linear servo-control algorithm that will handle full
    range. This approach eliminates the glitches at the transitioning points
    between one mode and the other. It eliminates the logic necessary to decide
    which algorithm to use. It eliminates your having to empirically determine
    the transition points. And of course, you have a much simpler program with
    one algorithm instead of three.
    
    Instead of trying to get rid of conditionals, you're best to question the
    underlying theory that led to the conditionals.

.. tip::

   Avoid the need for special handling.

One example we mentioned earlier in the book: if you keep the user out
of trouble you won’t have to continually test whether the user has
gotten into trouble.

**Moore**:
    Another good example is writing assemblers. Very often, even though an
    opcode may not have a register associated with it, pretending that it has a
    register---say, Register 0---might simplify the code. Doing arithmetic by
    introducing bit patterns that needn't exist simplifies the solution. Just
    substitute zeros and keep on doing arithmetic that you might have avoided
    by testing for zero and not doing it.

    It's another instance of the "don't care." If you don't care, then give
    it a dummy value and use it anyway.

Anytime you run into a special case, try to find an algorithm for which
the special case becomes a normal case.

.. tip::

   Use properties of the component.

A well-designed component—hardware or software—will let you implement a
corresponding lexicon in a clean, efficient manner. The character
graphics set from the old Epson MX-80 printer (although now obsolete)
illustrates the point well. :numref:`fig8-6` shows the graphics
characters produced by the ASCII codes 160 to 223.

.. figure:: fig8-6.png
   :name: fig8-6
   :alt: The Epson MX-80 graphics character set.

   The Epson MX-80 graphics character set.

Each graphics character is a different combination of six tiny boxes,
either filled in or left blank. Suppose in our application we want to
use these characters to create a design. For each character, we know
what we want in each of the six positions—we must produce the
appropriate ASCII character for the printer.

A little bit of looking will tell you there’s a very sensible pattern
involved. Assuming we have a six-byte table in which each byte
represents a pixel in the pattern:

.. image:: chapter8-img1.png
   :scale: 30%

and assuming that each byte contains hex FF if the
pixel is “on;” zero if it is “off,” then here’s how little code it takes
to compute the character:

.. code-block:: none
   
   CREATE PIXELS  6 ALLOT
   : PIXEL  ( i -- a )  PIXELS + ;
   : CHARACTER  ( -- graphics character)
      160   6 O DO  I PIXEL C@  I 2** AND  +  LOOP ;

(We introduced 2\*\* a few tips back.)

No decisions are necessary in the definition of ``CHARACTER``. The graphics
character is simply computed.

Note: to use the same algorithm to translate a set of six adjoining
pixels in a large grid, we can merely redefine PIXEL. That’s an example
of adding indirection backwards, and of good decomposition.

Unfortunately, external components are not always designed well. For
instance, The IBM Personal Computer uses a similar scheme for graphics
characters on its video display, but without any discernible
correspondence between the ASCII values and the pattern of pixels. The
only way to produce the ASCII value is by matching patterns in a lookup
table.

**Moore**:
    The 68000 assembler is another example you can break your heart over,
    looking for a good way to express those op-codes with the minimal number
    of operators. All the evidence suggests there is no good solution. The
    people who designed the 68000 didn't have assemblers in mind. And they
    could have made things a lot easier, at no cost to themselves.

By using properties of a component in this way, your code becomes
dependent on those properties and thus on the component itself. This is
excusable, though, because all the dependent code is confined to a
single lexicon, which can easily be changed if
necessary.

Using Structured Exits
----------------------

.. tip::

   Use the structured exit.

In the chapter on factoring we demonstrated the possibility of factoring
out a control structure using this technique:

.. code-block:: none
   
   : CONDITIONALLY   A B OR  C AND  IF  NOT R> DROP  THEN ;
   : ACTIVE   CONDITIONALLY   TUMBLE JUGGLE JUMP ;
   : LAZY   CONDITIONALLY  SIT  EAT  SLEEP ;

Forth allows us to alter the control flow by directly manipulating the
return stack. (If in doubt, see *Starting Forth*, Chapter Nine.)
Indiscreet application of this trick can lead to unstructured code with
nasty side effects. But the disciplined use of the structured exit can
actually simplify code, and thereby improve readability and
maintainability.

**Moore**:
    More and more I've come to favor ``R> DROP`` to alter the flow of
    control.  It's similar to the effect of an ``ABORT"``, which has an
    ``IF``  ``THEN`` built in it. But that's only one ``IF``  ``THEN`` in the
    system, not at every error.
    
    I either abort or I don't abort. If I don't abort, I continue. If I do
    abort, I don't have to thread my way through the path. I short-circuit the
    whole thing.
    
    The alternative is burdening the rest of the application with checking
    whether an error occurred. That's an inconvenience.

The “abort route” circumvents the normal paths of control flow under
special conditions. Forth provides this capability with the words
``ABORT"`` and ``QUIT``.

The “structured exit” extends the concept by allowing the immediate
termination of a single word, without quitting the entire application.

This technique should not be confused with the use of GOTO, which is
unstructured to the extreme. With GOTO you can go anywhere, inside or
outside the current module. With this technique, you effectively jump
directly to the final exit point of the module (the semicolon) and
resume execution of the calling word.  The
word ``EXIT`` terminates the definition in which the
word appears. The phrase ``R> DROP`` terminates the
definition that called the definition in which the phrase appears; thus
it has the same effect but can be used one level down. Here are some
examples of both approaches.

If you have an ``IF``  ``ELSE``  ``THEN`` phrase in which no code follows
``THEN``, like this:

.. code-block:: none
   
   ... HUNGRY?  IF  EAT-IT  ELSE  FREEZE-IT  THEN ;

you can eliminate ``ELSE`` by using ``EXIT``:

.. code-block:: none
   
   ... HUNGRY?  IF EAT-IT EXIT  THEN  FREEZE-IT ;

(If the condition is true, we eat and run; ``EXIT``
acts like a semicolon. If the condition is false, we skip to
``THEN`` and ``FREEZE-IT``.)

The use of ``EXIT`` here is more efficient, saving two
bytes and extra code to perform, but it is not as
readable.

**Moore** comments on the value, and danger, of this technique:
    Especially if your conditionals are getting elaborate, it's handy to jump
    out in the middle without having to match all your ``THEN``\ s at the
    end. In one application I had a word that went like this:

    .. code-block:: none
   
       : TESTING
          SIMPLE  1CONDITION IF ... EXIT THEN
                  2CONDITION IF ... EXIT THEN
                  3CONDITION IF ... EXIT THEN ;
    
    SIMPLE handled the simple cases. SIMPLE ended up with ``R> DROP``.
    These other conditions were the more complex ones.
    
    Everyone exited at the same point without having to painfully match all
    the ``IF``\ s, ``ELSE``\ s, and ``THEN``\ s. The final result, if
    none of the conditions matched, was an error condition.
    
    It was bad code, difficult to debug. But it reflected the nature of the
    problem. There wasn't any better scheme to handle it. The ``EXIT``
    and ``R> DROP`` at least kept things manageable.

Programmers sometimes also use ``EXIT`` to get out of
a complicated ``BEGIN`` loop in a graceful way. Or we
might use a related technique in the
``DO``  ``LOOP`` that we wrote for
``’FUNCTION`` in our Tiny Editor, earlier in this chapter. In this word, we
are searching through a series of locations looking for a match. If we
find a match, we want to return the address where we found it; if we
don’t find a match, we want the address of the last row of the functions
table.

We can introduce the word ``LEAP`` (see :doc:`Appendix C<appendixc>`),
which will work like ``EXIT`` (it will simulate a semicolon). Now
we can write:

.. code-block:: none
   
   : 'FUNCTION  ( key -- adr-of-match )
      'NOMATCH FUNCTIONS DO  DUP  I @ =  IF  DROP I LEAP
      THEN  /KEY +LOOP  DROP  'NOMATCH ;

If we find a match we ``LEAP``, not to ``+LOOP``, but
right out of the definition, leaving ``I`` (the address at which we found
it) on the stack. If we don’t find a match, we fall through the loop and
execute

.. code-block:: none
   
   DROP  'NOMATCH

which drops the key# being searched for, then leaves the address of the
last row!

As we’ve seen, there may be times when a premature exit is appropriate,
even multiple exit points and multiple “continue” points.

Remember though, this use of ``EXIT`` and
``R> DROP`` is *not consistent* with structured
programming in the strictest sense, and requires great care.

For instance, you may have a value on the stack at the beginning of a
definition which is consumed at the end. A premature
``EXIT`` will leave the unwanted value on the stack.

Fooling with the return stack is like playing with fire. You can get
burned. But how convenient it is to have
fire.

Employing Good Timing
---------------------

.. tip::

   Take the action when you know you need to, not later.

Any time you set a flag, ask yourself why you’re setting it. If the
answer is, “So I’ll know to do such-and-such later,” then ask yourself
if you can do such-and-such *now*. A little restructuring can greatly
simplify your design.

.. tip::

   Don't put off till run time what you can compile today.

Any time you can make a decision prior to compiling an application, do.

Suppose you had two versions of an array: one that did bounds checking
for your protection during development and one that ran faster, though
unprotected for the actual application.

Keep the two versions in different screens. When you compile your
application, load only the version you need.

By the way, if you follow this suggestion, you may go crazy editing
parentheses in and out of your load blocks to change which version gets
loaded each time. Instead, write throw-away definitions that make the
decisions for you. For instance (as already previewed in another
context):

.. code-block:: none
   
   : STEPPERS   150  'TESTING? @  1 AND +  LOAD ;

.. tip::

   ``DUP`` a flag, don't recreate it.

Sometimes you need a flag to indicate whether or not a previous piece of
code was invoked. The following definition leaves a flag which indicates
that ``DO-IT`` was done:

.. code-block:: none
   
   : DID-I?  ( -- t=I-did)
      SHOULD-I?  IF  DO-IT  TRUE  ELSE  FALSE  THEN ;

This can be simplified to:

.. code-block:: none
   
   : DID-I?  ( -- t=I-did)
           SHOULD-I? DUP  IF  DO-IT  THEN ;

.. tip::

   Don't set a flag, set the data.

If the only purpose to setting a flag is so that later some code can
decide between one number and another, you’re better off saving the
number itself.

The “colors” example in Chapter Six’s section called “Factoring
Criteria” illustrates this point.

The purpose of the word ``LIGHT`` is to set a flag which indicates whether
we want the intensity bit to be set or not. While we could have
written

.. code-block:: none
   
   : LIGHT   TRUE 'LIGHT? ! ;

to set the flag, and

.. code-block:: none
   
   'LIGHT? @ IF  8 OR  THEN ...

to use the flag, this approach is not quite as simple as putting the
intensity bit-mask itself in the variable:

.. code-block:: none
   
   : LIGHT   8 'LIGHT? ! ;

and then simply writing

.. code-block:: none
   
   'LIGHT? @  OR ...

to use it.

.. tip::

   Don't set a flag, set the function. (Vector.)

This tip is similar to the previous one, and lives under the same
restriction. If the only purpose to setting a flag is so that later some
code can decide between one function and another, you’re better off
saving the address of the function itself.

For instance, the code for transmitting a character to a printer is
different than for slapping a character onto a video display. A poor
implementation would define:

.. code-block:: none
   
   VARIABLE DEVICE  ( O=video | 1=printer)
   : VIDEO   FALSE DEVICE ! ;
   : PRINTER   TRUE DEVICE ! ;
   : TYPE  ( a # -- ) DEVICE @ IF
      ( ...code for printer...) ELSE
      ( ...code for video...)  THEN ;

This is bad because you’re deciding which function to perform every time
you type a string.

A preferable implementation would use vectored execution. For instance:

.. code-block:: none
   
   DOER TYPE  ( a # -- )
   : VIDEO   MAKE TYPE ( ...code for video...) ;
   : PRINTER   MAKE TYPE ( ...code for printer...) ;

This is better because ``TYPE`` doesn’t have to decide which code to use, it
already knows.

(On a multi-tasked system, the printer and monitor tasks would each have
their own copies of an execution vector for ``TYPE`` stored in a user
variable.)

The above example also illustrates the limitation of this tip. In our
second version, we have no simple way of knowing whether our current
device is the printer or the video screen. We might need to know, for
instance, to decide whether to clear the screen or issue a formfeed.
Then we’re making an additional use of the state, and our rule no longer
applies.

A flag would, in fact, allow the simplest implementation of additional
state-dependent operations. In the case of ``TYPE``, however, we’re
concerned about speed. We type strings so often, we can’t afford to
waste time doing it. The best solution here might be to set the function
of ``TYPE`` and also set a flag:

.. code-block:: none
   
   DOER TYPE
   : VIDEO   O DEVICE !  MAKE TYPE
        ( ...code for video...) ;
   : PRINTER   1 DEVICE !  MAKE TYPE
        ( ...code for printer...) ;

Thus ``TYPE`` already knows which code to execute, but other definitions
will refer to the flag.

Another possibility is to write a word that fetches the parameter of the
``DOER`` word ``TYPE`` (the pointer to the current code)
and compares it against the address of ``PRINTER``. If it’s less than the
address of ``PRINTER``, we’re using the ``VIDEO`` routine; otherwise we’re using
the ``PRINTER`` routine.

If changing the state involves changing a small number of functions, you
can still use ``DOER/MAKE``. Here are definitions of three memory-move
operators that can be shut off together.

.. code-block:: none
   
   DOER !'  ( vectorable ! )
   DOER CMOVE'  ( vectorable CMOVE )
   DOER FILL'  ( vectorable FILL )
   : STORING   MAKE !' ! ;AND
               MAKE CMOVE'  CMOVE ;AND
               MAKE FILL'  FILL ;
   : -STORING  MAKE !'  2DROP ;AND
               MAKE CMOVE'  2DROP DROP ;AND
               MAKE FILL'  2DROP DROP ;

But if a large number of functions need to be vectored, a state table
would be preferable.

A corollary to this rule introduces the “structured exit hook,” a
``DOER`` word vectored to perform a structured exit.

.. code-block:: none
   
   DOER HESITATE  ( the exit hook)
   : DISSOLVE   HESITATE  FILE-DIVORCE ;

(… Much later in the listing:)

.. code-block:: none
   
   : RELENT   MAKE HESITATE   SEND-FLOWERS  R> DROP ;

By default, ``HESITATE`` does nothing. If we invoke ``DISSOLVE``, we’ll end up
in court. But if we ``RELENT`` before we ``DISSOLVE``, we’ll send flowers, then
jump clear to the semicolon, canceling that court order before our
partner ever finds out.

This approach is especially appropriate when the cancellation must be
performed by a function defined much later in the listing (decomposition
by sequential complexity). Increased complexity of the earlier code is
limited solely to defining the hook and invoking it at the right
spot.

Simplifying
-----------

I’ve saved this tip for last because it exemplifies the rewards of
opting for simplicity. While other tips concern maintainability,
performance, compactness, etc., this tip relates to the sort of
satisfaction that Thoreau sought at Walden Pond.

.. tip::

   Try to avoid altogether saving flags in memory.

A flag on the stack is quite different from a flag in memory. Flags on
the stack can simply be determined (by reading the hardware,
calculating, or whatever), pushed onto the stack, then consumed by the
control structure. A short life with no complications.

But save a flag in memory and watch what happens. In addition to having
the flag itself, you now have the complexity of a location for the flag.
The location must be:

-  created

-  initialized (even before anything actually changes)

-  reset (otherwise, passing a flag to a command leaves the flag in that
   current state).

Because flags in memory are variables, they are not reentrant.

An example of a case in which we might reconsider the need for a flag is
one we’ve seen several times already. In our “colors” example we made
the assumption that the best syntax would
be:

.. code-block:: none
   
   LIGHT BLUE

that is, the adjective ``LIGHT`` preceding the color. Fine. But remember the
code to implement that version? Compare it with the simplicity of this
approach:

.. code-block:: none
   
   O CONSTANT BLACK    1 CONSTANT BLUE    2 CONSTANT GREEN
   3 CONSTANT CYAN     4 CONSTANT RED     5 CONSTANT MAGENTA
   6 CONSTANT BROWN    7 CONSTANT GRAY
   : LIGHT   ( color -- color )  8 OR ;

In this version we’ve reversed the syntax, so that we now say

.. code-block:: none
   
   BLUE LIGHT

We establish the color, then we modify the color.

We’ve eliminated the need for a variable, for code to fetch from the
variable and more code to reset the variable when we’re done. And the
code is so simple it’s impossible not to understand.

When I first wrote these commands, I took the English-like approach.
“``BLUE LIGHT``” sounded backwards, not at all acceptable. That was before
my conversations with Chuck Moore.

**Moore's philosophy is persuasive**:
    I would distinguish between reading nicely in English and reading nicely.
    In other languages such as Spanish, adjectives follow nouns. We should be
    independent of details like which language we're thinking in.
    
    It depends on your intention: simplicity, or emulation of English. English
    is not such a superb language that we should follow it slavishly.

If I were selling my “colors” words in a package for graphic artists, I
would take the trouble to create the flag. But writing these words for
my own use, if I had to do it over again, I’d favor the Moore-ish
influence, and use “``BLUE LIGHT``.”

Summary
=======

The use of logic and conditionals as a significant structural element in
programming leads to overly-complicated, difficult-to-maintain, and
inefficient code. In this chapter we’ve discussed several ways to
minimize, optimize or eliminate unnecessary conditional structures.

As a final note, Forth’s downplaying of conditionals is not shared by
most contemporary languages. In fact, the Japanese are basing their
fifth-generation computer project on a language called PROLOG—for
PROgramming in LOGic—in which one programs entirely in logic. It will be
interesting to see the battle-lines forming as we ponder the question:

   To ``IF`` or not to ``IF``

In this book we’ve covered the first six steps of the software
development cycle, exploring both the philosophical questions of
designing software and practical considerations of implementing robust,
efficient, readable software.

We have not discussed optimization, validation, debugging, documenting,
project management, Forth development tools, assembler definitions, uses
and abuses of recursion, developing multiprogrammed applications, or
target compilation.

But that’s another story.

For Further Thinking
====================

Define the word ``DIRECTION``, which returns either 1,
:math:`-1`, or 0, depending on whether the input argument is non-zero
positive, negative, or zero, respectively. 

REFERNCES
=========

.. [eaker]  Charles Eaker, "Just in Case,"  **ForthDimensions**  II/3, p. 37.

