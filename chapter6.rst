
************
6. Factoring
************

In this chapter we’ll continue our study of the
implementations phase, this time focusing on factoring.

Decomposition and factoring are chips off the same block. Both involve
dividing and organizing. Decomposition occurs during preliminary design;
factoring occurs during detailed design and implementation.

Since every colon definition reflects decisions of factoring, an
understanding of good factoring technique is perhaps the most important
skill for a Forth programmer.

What is factoring? Factoring
means organizing code into useful fragments. To make a fragment useful,
you often must separate reusable parts from non-reusable parts. The
reusable parts become new definitions. The non-reusable parts become
arguments or parameters to the definitions.

Making this separation is usually referred to as “factoring out.” The
first part of this chapter will discuss various “factoring-out”
techniques.

Deciding how much should go into, or stay out of, a definition is
another aspect of factoring. The second section will outline the
criteria for useful factoring.

Factoring Techniques
====================

    If a module seems almost, but not quite, useful from a second place in
    the system, try to identify and isolate the useful subfunction. The
    remainder of the module might be incorporated in its original caller
    (from "**Structured Design**" [stevens74-6]_ ).

The “useful subfunction” of course becomes the newly factored
definition.What about the part that “isn’t quite useful”? That depends
on what it is.

Factoring Out Data
------------------

The simplest thing to
factor out is data, thanks to Forth’s data stack. For instance, to
compute two-thirds of 1,000, we write

.. code-block:: none
   
   1000 2 3 */

To define a word that computes two-thirds of *any* number, we factor out
the argument from the definition:

.. code-block:: none
   
   : TWO-THIRDS  ( n1 -- n2)  2 3 */ ;

When the datum comes in the *middle* of the useful phrase, we have to
use stack manipulation. For instance, to center a piece of text ten
characters long on an 80-column screen, we would write:

.. code-block:: none
   
   80  10 -   2/ SPACES

But text isn’t always 10 characters long. To make the phrase useful for
any string, you’d factor out the length by writing:

.. code-block:: none
   
   : CENTER ( length -- ) 80  SWAP -  2/ SPACES ;

The data stack can also be used to pass addresses. Therefore what’s
factored out may be a *pointer* to data rather than the data themselves.
The data can be numbers or even strings, and still be factored out
through use of the stack.

Sometimes the difference appears to be a function, but you can factor it
out simply as a number on the stack. For instance:

.. list-table::
   :widths: auto

   * - Segment 1:
     - WILLY
     - WILLY
     -
     - PUDDIN’ PIE AND
   * - Segment 2:
     - WILLY
     - NILLY
     - 8 \*
     - PUDDIN’ PIE AND

How can you factor out the “8 \*” operation? By
including “\*” in the factoring and passing it a one or eight:

.. code-block:: none
   
   : NEW  ( n )  WILLY NILLY  *  PUDDIN' PIE AND ;

Segment 1:
    1 NEW

Segment 2:
    8 NEW

(Of course if WILLY NILLY changes the stack,
you’ll need to add appropriate stack-manipulation operators.)

If the operation involves addition, you can nullify it by passing a
zero.

.. hint::

   For simplicity, try to express the difference between similar
   fragments as a numeric difference (values or addresses), rather than
   as a procedural difference.

Factoring Out Functions
-----------------------

On the other hand, the
difference sometimes *is* a function. Witness:

Segment 1:
    .. code-block:: none
   
       BLETCH-A  BLETCH-B BLETCH-C
                BLETCH-D  BLETCH-E  BLETCH-F

Segment 2:
    .. code-block:: none
   
       BLETCH-A  BLETCH-B  PERVERSITY
                BLETCH-D  BLETCH-E  BLETCH-F

Wrong approach:

.. code-block:: none
   
   : BLETCHES  ( t=do-BLETCH-C | f=do-PERVERSITY -- ) 
      BLETCH-A  BLETCH-B  IF  BLETCH-C  ELSE  PERVERSITY
         THEN  BLETCH-D BLETCH-E BLETCH-F ;

Segment 1:
    TRUE BLETCHES

Segment 2:
    FALSE BLETCHES

A better approach:

.. code-block:: none
   
   : BLETCH-AB   BLETCH-A BLETCH-B ;
   : BLETCH-DEF   BLETCH-D BLETCH-E BLETCH-F ;


Segment 1:
    BLETCH-AB BLETCH-C BLETCH-DEF

Segment 2:
    BLETCH-AB PERVERSITY BLETCH-DEF

.. hint::

   Don't pass control flags downward.

Why not? First, you are asking your running application to make a
pointless decision—one you knew the answer to while programming—thereby
reducing efficiency. Second, the terminology doesn’t match the
conceptual model. What are ``TRUE BLETCHES`` as opposed to ``FALSE BLETCHES``?

Factoring Out Code from Within Control Structures
-------------------------------------------------

Be alert to repetitions on either side of an ``IF``  ``ELSE``  ``THEN``
statement. For instance:

.. code-block:: none
   
   ... ( c)  DUP  BL 127 WITHIN
          IF  EMIT  ELSE
          DROP  ASCII . EMIT   THEN ...

This fragment normally emits an ASCII character, but if the character is
a control code, it emits a dot. Either way, an ``EMIT`` is performed. Factor
``EMIT`` out of the conditional structure, like this:

.. code-block:: none
   
   ... ( c)  DUP  BL 127 WITHIN NOT
          IF  DROP  ASCII .  THEN  EMIT  ...

The messiest situation occurs when the
difference between two definitions is a function within a structure that
makes it impossible to factor out the half-fragments. In this case, use
stack arguments, variables, or even vectoring. We’ll see how vectoring
can be used in a section of :doc:`Chapter Seven<chapter7>` called “Using DOER/MAKE.”

Here’s a reminder about factoring code from out of a ``DO``  ``LOOP``:

.. hint::

   In factoring out the contents of a ``DO``   ``LOOP`` into a new
   definition, rework the code so that ``I`` (the index) is not
   referenced within the new definition, but rather passed as a stack
   argument to it.

Factoring Out Control Structures Themselves
-------------------------------------------

Here are two definitions whose differences lies within a ``IF``  ``THEN``
construct:

.. code-block:: none
   
   : ACTIVE    A B OR  C AND  IF  TUMBLE JUGGLE JUMP THEN ;
   : LAZY      A B OR  C AND  IF   SIT  EAT  SLEEP   THEN ;

The condition and control structure remain the same; only the event
changes. Since you can’t factor the ``IF`` into one
word and the ``THEN`` into another, the simplest thing
is to factor the condition:

.. code-block:: none
   
   : CONDITIONS? ( -- ?) A B OR C AND ;
   : ACTIVE    CONDITIONS? IF TUMBLE JUGGLE JUMP THEN ;
   : LAZY      CONDITIONS? IF    SIT  EAT  SLEEP THEN ;

Depending on the number of repetitions of the
same condition and control structure, you may even want to factor out
both. Watch this:

.. code-block:: none
   
   : CONDITIONALLY   A B OR  C AND NOT IF  R> DROP   THEN ;
   : ACTIVE   CONDITIONALLY   TUMBLE JUGGLE JUMP ;
   : LAZY   CONDITIONALLY  SIT  EAT  SLEEP ;

The word ``CONDITIONALLY`` may—depending on the
condition—alter the control flow so that the remaining words in each
definition will be skipped. This approach has certain disadvantages as
well. We’ll discuss this technique—pros and cons—in :doc:`Chapter Eight<chapter8>`.

More benign examples of factoring-out control structures include case
statements, which eliminate nested ``IF``  ``ELSE``  ``THEN`` s,
and multiple exit loops  (the ``BEGIN``  ``WHILE``  ``WHILE``  ``WHILE``
``...``  ``REPEAT`` construct).
We\’ll also discuss these topics in :doc:`Chapter Eight<chapter8>`.

Factoring Out Names
-------------------

It’s even good to factor
out names, when the names seem almost, but not quite, the same. Examine
the following terrible example of code, which is meant to initialize
three variables associated with each of eight channels:

.. code-block:: none
   
   VARIABLE 0STS       VARIABLE 1STS       VARIABLE 2STS 
   VARIABLE 3STS       VARIABLE 4STS       VARIABLE 5STS
   VARIABLE 6STS       VARIABLE 7STS       VARIABLE 0TNR
   VARIABLE 1TNR       VARIABLE 2TNR       VARIABLE 3TNR
   VARIABLE 4TNR       VARIABLE 5TNR       VARIABLE 6TNR
   VARIABLE 7TNR       VARIABLE 0UPS       VARIABLE 1UPS
   VARIABLE 2UPS       VARIABLE 3UPS       VARIABLE 4UPS
   VARIABLE 5UPS       VARIABLE 6UPS       VARIABLE 7UPS

.. code-block:: none
   
   : INIT-CHO   0 0STS !  1000 0TNR !  -1 0UPS ! ; 
   : INIT-CH1   0 1STS !  1000 1TNR !  -1 1UPS ! ; 
   : INIT-CH2   0 2STS !  1000 2TNR !  -1 2UPS ! ; 
   : INIT-CH3   0 3STS !  1000 3TNR !  -1 3UPS ! ; 
   : INIT-CH4   0 4STS !  1000 4TNR !  -1 4UPS ! ; 
   : INIT-CH5   0 5STS !  1000 5TNR !  -1 5UPS ! ; 
   : INIT-CH6   0 6STS !  1000 6TNR !  -1 6UPS ! ; 
   : INIT-CH7   0 7STS !  1000 7TNR !  -1 7UPS ! ; 

.. code-block:: none
   
   : INIT-ALL-CHS    INIT-CHO  INIT-CH1  INIT-CH2  INIT-CH3
      INIT-CH4  INIT-CH5  INIT-CH6  INIT-CH7 ;

First there’s a similarity among the names of the variables; then
there’s a similarity in the code used in all the ``INIT-CH`` words.

Here’s an improved rendition. The similar variable names have been
factored into three data structures, and the lengthy recital of ``INIT-CH``
words has been factored into a ``DO``  ``LOOP``:

.. code-block:: none
   
   : ARRAY  ( #cells -- )  CREATE  2* ALLOT
      DOES> ( i -- 'cell)  SWAP  2* + ; 
   8 ARRAY STATUS  ( channel# -- adr)
   8 ARRAY TENOR   (        "       )
   8 ARRAY UPSHOT  (        "       )
   : STABLE   8 0 DO  0 I STATUS !  1000 I TENOR ! 
      -1 I UPSHOT !  LOOP ;

That’s all the code we need.

Even in the most innocent
cases, a little data structure can eliminate extra names. By convention
Forth handles text in “counted strings” (i.e., with the count in the
first byte). Any word that returns the “address of a string” actually
returns this beginning address, where the count is. Not only does use of
this two-element data structure eliminate the need for separate names
for string and count, it also makes it easier to move a string in
memory, because you can copy the string *and* the count with a single
``CMOVE``. 

When you start finding the same awkwardness here and there, you can
combine things and make the awkwardness go away.

Factoring Out Functions into Defining Words
-------------------------------------------

.. hint::

   If a series of definitions contains identical functions, with
   variation only in data, use a defining word.

Examine the structure of this code (without worrying about its
purpose—you’ll see the same example later
on):

.. code-block:: none
   
   : HUE  ( color -- color') 
      'LIGHT? @  OR  0 'LIGHT? ! ;
   : BLACK   0 HUE ;
   : BLUE   1 HUE ;
   : GREEN   2 HUE ;
   : CYAN   3 HUE ;
   : RED   4 HUE ;
   : MAGENTA   5 HUE ;
   : BROWN   6 HUE ;
   : GRAY   7 HUE ;

The above approach is technically correct, but
less memory-efficient than the following approach using defining words:

.. code-block:: none
   
   : HUE   ( color -- )  CREATE ,
      DOES>  ( -- color )  @ 'LIGHT? @  OR  0 'LIGHT? ! ;
    0 HUE BLACK         1 HUE BLUE          2 HUE GREEN
    3 HUE CYAN          4 HUE RED           5 HUE MAGENTA
    6 HUE BROWN         7 HUE GRAY

(Defining words are explained in *Starting Forth*, Chapter Eleven).

By using a defining word, we save memory because each compiled colon
definition needs the address of ``EXIT`` to conclude
the definition. (In defining eight words, the use of a defining word
saves 14 bytes on a 16-bit Forth.) Also, in a colon definition each
reference to a numeric literal requires the compilation of
``LIT`` (or ``literal``), another 2 bytes per definition. (If 1
and 2 are predefined constants, this costs another 10 bytes—24 total.)

In terms of readability, the defining word makes it absolutely clear
that all the colors it defines belong to the same family of words.

The greatest strength of defining words, however, arises when a series
of definitions share the same *compile-time* behavior. This topic is the
subject of a later section, “Compile-Time Factoring.”

Factoring Criteria
==================

Armed with an understanding
of factoring techniques, let’s now discuss several of the criteria for
factoring Forth definitions. They include:

#. Limiting the size of definitions

#. Limiting repetition of code

#. Nameability

#. Information hiding

#. Simplifying the command interface

.. hint::

   Keep definitions short.

We asked **Moore**, "How long should a Forth definition be?"
    A word should be a line long. That's the target.
    
    When you have a whole lot of words that are all useful in their own
    right---perhaps in debugging or exploring, but inevitably there's a
    reason for their existence---you feel you've extracted the essence of
    the problem and that those words have expressed it.

    Short words give you a good feeling.

An informal examination of one of Moore’s applications shows that he
averages seven references, including both words and numbers, per
definition. These are remarkably short definitions. (Actually, his code
was divided about 50–50 between one-line and two-line definitions.)

Psychological tests have shown that the human mind can only focus its
conscious attention on seven things, plus or minus two, at a time
[miller56]_. Yet all the while, day and night, the vast
resources of the mind are subconsciously storing immense amounts of
data, making connections and associations and solving problems.

Even if out subconscious mind knows each part of an application inside
out, our narrow-viewed conscious mind can only correlate seven elements
of it at once. Beyond that, our grasp wavers. Short definitions match
our mental capabilities.

Something that tempts many Forth programmers to write overly long
definitions is the knowledge that headers take space in the dictionary.
The coarser the factoring, the fewer the names, and the less memory that
will be wasted.

It’s true that more memory will be used, but it’s hard to say that
anything that helps you test, debug and interact with your code is a
“waste.” If your application is large, try using a default width of
three, with the ability to switch to a full-length name to avoid a
specific collision. (“Width” refers to a limit on the number of
characters stored in the name field of each dictionary header.)

If the application is still too big, switch to a Forth with multiple
dictionaries on a machine with extended memory, or better yet, a 32-bit
Forth on a machine with 32-bit addressing.

A related fear is that over-factoring will decrease performance due to
the overhead of Forth’s inner interpreter. Again, it’s true that there
is some penalty for each level of nesting. But ordinarily the penalty
for extra nesting due to proper factoring will not be noticeable. If you
timings are that tight, the real solution is to translate something into
assembler.

.. hint::

   Factor at the point where you feel unsure about your code (where
   complexity approaches the conscious limit).

Don’t let your ego take over with an “I can lick this!” attitude. Forth
code should never feel uncomfortably complex. Factor!

**Moore**:
    Feeling like you might have introduced a bug is one reason for factoring.
    Any time you see a doubly-nested ``DO``  ``LOOP``, that's a sign
    that something's wrong because it will be hard to debug. Almost always
    take the inner ``DO``  ``LOOP`` and make a word.
    
    And having factored out a word for testing, there's no reason for
    putting it back. You found it useful in the first place. There's no
    guarantee you won't need it again.

Here’s another facet of the same principle:

.. hint::

   Factor at the point where a comment seems necessary

Particularly if you feel a need to remind yourself what’s on the stack,
this may be a good time to “make a break.”

Suppose you have

.. code-block:: none
   
   ... BALANCE  DUP xxx xxx xxx xxx xxx xxx xxx xxx xxx
        xxx xxx xxx xxx xxx xxx   ( balance) SHOW  ...

which begins by computing the balance and ends by displaying it. In the
meantime, several lines of code use the balance for purposes of their
own. Since it’s difficult to see that the balance is still on the stack
when ``SHOW`` executes, the programmer has interjected a stack picture.

This solution is generally a sign of bad factoring. Better to write:

.. code-block:: none
   
   : REVISE  ( balance -- )  xxx xxx xxx xxx xxx xxx xxx
        xxx xxx xxx xxx xxx xxx xxx ;
   ... BALANCE  DUP REVISE  SHOW  ...

No narrative stack pictures are needed. Furthermore, the programmer now
has a reusable, testable subset of the definition.

.. hint::

   Limit repetition of code.

The second reason for
factoring, to eliminate repeated fragments of code, is even more
important than reducing the size of definitions.

**Moore**:
    When a word is just a piece of something, it's useful for clarity or
    debugging, but not nearly as good as a word that is used multiple
    times. Any time a word is used only once you want to question its
    value.
    
    Many times when a program has gotten too big I will go back through it
    looking for phrases that strike my eye as candidates for factoring.
    The computer can't do this; there are too many variables.

In looking over your work, you often find identical phrases or short
passages duplicated several times. In writing an editor I found this
phrase repeated several times:

.. code-block:: none
   
   FRAME  CURSOR @ +

Because it appeared several times I factored it into a new word called
``AT``.

It’s up to you to recognize fragments that are coded differently but
functionally equivalent, such as:

.. code-block:: none
   
   FRAME  CURSOR @ 1-  +

The ``1-`` appears to make this phrase different from the one defined as ``AT.``
But in fact, it can be written

.. code-block:: none
   
   AT 1-

On the other hand:

.. hint::

   When factoring out duplicate code, make sure the factored code serves
   a single purpose.

Don’t blindly seize upon duplications that may not be useful. For
instance, in several places in one application I used this phrase:

.. code-block:: none
   
   BLK @ BLOCK  >IN @ +  C@

I turned it into a new word and called it ``LETTER``, since it returned the
letter being pointed to by the interpreter.

In a later revision, I unexpectedly had to write:

.. code-block:: none
   
   BLK @ BLOCK  >IN @ +  C!

I could have used the existing ``LETTER`` were it not for its ``C@`` at the end.
Rather than duplicate the bulk of the phrase in the new section, I chose
to refactor ``LETTER`` to a finer resolution, taking out the ``C@``. The usage
was then either ``LETTER C@`` or ``LETTER C!``. This change required me to
search through the listing changing all instances of ``LETTER`` to
``LETTER C@``.
But I should have done that in the first place, separating the
computation of the letter’s address from the operation to be performed
on the address. 

Similar to our injunction against repetition of code:

.. hint::

   Look for repetition of patterns.

If you find yourself referring back in the program to copy the pattern
of previously-used words, then you may have mixed in a general idea with
a specific application. The part of the pattern you are copying perhaps
can be factored out as an independent definition that can be used in all
the similar cases. 

.. hint::

   Be sure you can name what you factor.

**Moore**:
    If you have a concept that you can't assign a single name to, not a
    hyphenated name, but a name, it's not a well-formed concept. The
    ability to assign a name is a necessary part of decomposition.
    Certainly you get more confidence in the idea.

Compare this view with the criteria
for decomposing a module espoused by structured design in :doc:`Chapter One<chapter1>`.
According to that method, a module should exhibit “functional binding,”
which can be verified by describing its function in a single,
non-compound, *sentence*. Forth’s “atom,” a *name*, is an order of
magnitude more refined.

.. hint::

   Factor definitions to hide details that may change.

We’ve seen the value of information hiding in earlier chapters,
especially with regard to preliminary design. It’s useful to remember
this criterion during the implementation stage as well.

Here’s a very short definition that does little except hide information:

.. code-block:: none
   
   : >BODY  ( acf -- apf )  2+ ;

This definition allows you to convert an acf (address of code field) to
an apf (address of parameter field) without depending on the actual
structure of a dictionary definition. If you were to use
``2+`` instead of the word ``>BODY``,
you would lose transportability if you ever
converted to a Forth system in which the heads were separated from the
bodies. (This is one of a set of words suggested by Kim Harris, and
included as an Experimental Proposal in the Forth-83 Standard
[harris83]_.)

Here’s a group of definitions that might be used in writing an editor:

.. code-block:: none
   
   : FRAME  ( -- a)  SCR @ BLOCK ;
   : CURSOR  ( -- a)  R# ;
   : AT  ( -- a)  FRAME  CURSOR @ + ;

These three definitions can form the basis for all calculations of
addresses necessary for moving text around. Use of these three
definitions completely separates your editing algorithms from a reliance
on Forth blocks.

What good is that? If you should decide, during development, to create
an editing buffer to protect the user from making errors that destroy a
block, you merely have to redefine two of these words, perhaps like
this:

.. code-block:: none
   
   CREATE FRAME  1024 ALLOT
   VARIABLE CURSOR

The rest of your code can remain intact.

.. hint::

   Factor functions out of definitions that display results.

This is really a question of decomposition.

Here’s an example. The word defined below, pronounced “people-to-paths,”
computes how many paths of communication there are between a given
number of people in a group. (This is a good thing for managers of
programmer teams to know—the number of communication paths increases
drastically with each new addition to the team.)

.. code-block:: none
   
   : PEOPLE>PATHS  ( #people -- #paths )  DUP 1-  *  2/ ;

This definition does the calculation only. Here’s the “user definition”
that invokes ``PEOPLE>PATHS`` to perform the calculation, and then displays
the result:

.. code-block:: none
   
   : PEOPLE  ( #people)
       ." = "  PEOPLE>PATHS  .  ." PATHS " ;

This produces:

.. code-block:: none
   
   2 PEOPLE = 1 PATHS
   3 PEOPLE = 3 PATHS
   5 PEOPLE = 10 PATHS
   10 PEOPLE = 45 PATHS

Even if you think you’re going to perform a particular calculation only
once, to display it in a certain way, believe me, you’re wrong. You will
have to come back later and factor out the calculation part. Perhaps
you’ll need to display the information in a right-justified column, or
perhaps you’ll want to record the results in a data base—you never know.
But you’ll always have to factor it, so you might as well do it right
the first time. (The few times you might get away with it aren’t worth
the trouble.)

The word ``.`` (dot) is a prime example. Dot is great 99\% of the time, but
occasionally it does too much. Here’s what it does, in fact (in
Forth–83):

.. code-block:: none
   
   : .   ( n )  DUP ABS 0 <# #S  ROT SIGN  #> TYPE SPACE ;

But suppose you want to convert a number on the stack into an ASCII
string and store it in a buffer for typing later. Dot converts it, but
also types it. Or suppose you want to format playing cards in the form
``10C`` (for “ten of clubs”). You can’t use dot to display the 10 because it
prints a final space.

Here’s a better factoring found in some Forth systems:

.. code-block:: none
   
   : (.)  ( n -- a #)  DUP ABS 0  <# #S  ROT SIGN  #> ;
   : .  ( n)  (.) TYPE SPACE ;

We find another example of failing to factor the output function from
the calculation function in our own Roman numeral example in :doc:`Chapter Four<chapter4>`
Four. Given our solution, we can’t store a Roman numeral in a buffer or
even center it in a field. (A better approach would have been to use
``HOLD`` instead of ``EMIT``.)

Information hiding can also be a reason *not* to factor. For instance,
if you factor the phrase

.. code-block:: none
   
   SCR @ BLOCK

into the definition

.. code-block:: none
   
   : FRAME   SCR @ BLOCK ;

remember you are doing so only because you may want to change the
location of the editing frame. Don’t blindly replace all occurrences of
the phrase with the new word ``FRAME,`` because you may change the
definition of ``FRAME`` and there will certainly be times when you really
want ``SCR``  ``@``  ``BLOCK``.

.. hint::

   If a repeated code fragment is likely to change in some cases but not
   others, factor out only those instances that might change. If the
   fragment is likely to change in more than one way, factor it into more
   than one definition.

Knowing when to hide information requires intuition and experience.
Having made many design changes in your career, you’ll learn the hard
way which things will be most likely to change in the future.

You can never predict everything, though. It would be useless to try, as
we’ll see in the upcoming section called “The Iterative Approach in
Implementation.”

.. hint::

   Simplify the command interface by reducing the number of commands.

It
may seem paradoxical, but good factoring can often yield *fewer* names.
In :doc:`Chapger Five<chapter5>` we saw how six simple names (``LEFT``, ``RIGHT``, ``MOTOR``,
``SOLENOID``, ``ON``, and ``OFF``) could do the work of eight badly-factored,
hyphenated names.

As another example, I found two definitions circulating in one
department in which Forth had recently introduced. Their purpose was
purely instructional, to remind the programmer which vocabulary was
``CURRENT``, and which was ``CONTEXT``:

.. code-block:: none
   
   : .CONTEXT   CONTEXT @  8 -  NFA  ID.   ;
   : .CURRENT   CURRENT @  8 -  NFA  ID.  ;

If you typed

.. code-block:: none
   
   .CONTEXT

the system would respond

.. code-block:: none
   
   .CONTEXT FORTH

(They worked—at least on the system used there—by backing up to the name
field of the vocabulary definition, and displaying it.)

The obvious repetition of code struck my eye as a sign of bad factoring.
It would have been possible to consolidate the repeated passage into a
third definition:

.. code-block:: none
   
   : .VOCABULARY   ( pointer )  @  8 -  NFA  ID. ;

shortening the original definitions to:

.. code-block:: none
   
   : .CONTEXT   CONTEXT .VOCABULARY ;
   : .CURRENT   CURRENT .VOCABULARY ;

But in this approach, the only difference between the two definitions
was the pointer to be displayed. Since part of good factoring is to make
fewer, not more definitions, it seemed logical to have only one
definition, and let it take as an argument either the word ``CONTEXT`` or
the word ``CURRENT``.

Applying the principles of good naming, I suggested:

.. code-block:: none
   
   : IS  ( adr)   @  8 -  NFA  ID. ;

allowing the syntax(``CONTEXT IS`` \[:kbd:`Enter`\])

.. code-block:: none
   
   CONTEXT IS ASSEMBLER ok

or(``CURRENT IS`` \[:kbd:`Enter`\])

.. code-block:: none
   
   CURRENT IS FORTH ok

The initial clue was repetition of code, but the final result came from
attempting to simplify the command interface.

Here’s another example. The IBM PC has four modes four displaying text
only:

    40 column monochrome

    40 column color

    80 column monochrome

    80 column color

The word ``MODE`` is available in the Forth system I use. ``MODE`` takes an
argument between 0 and 3 and changes the mode accordingly. Of course,
the phrase 0 ``MODE`` or 1 ``MODE`` doesn’t help me remember which mode is
which.

Since I need to switch between these modes in doing presentations, I
need to have a convenient set of words to effect the change. These words
must also set a variable that contains the current number of columns—40
or 80.

Here’s the most straightforward way to fulfill the requirements:

.. code-block:: none
   
   : 40-B&W       40 #COLUMNS !  0 MODE ;
   : 40-COLOR     40 #COLUMNS !  1 MODE ;
   : 80-B&W       80 #COLUMNS !  2 MODE ;
   : 80-COLOR     80 #COLUMNS !  3 MODE ;

By factoring to eliminate the repetition, we come up with this version:

.. code-block:: none
   
   : COL-MODE!     ( #columns mode )  MODE  #COLUMNS ! ;
   : 40-B&W       40 0 COL-MODE! ;
   : 40-COLOR     40 1 COL-MODE! ;
   : 80-B&W       80 2 COL-MODE! ;
   : 80-COLOR     80 3 COL-MODE! ;

But by attempting to reduce the number of commands, and also by
following the injunctions against numerically-prefixed and hyphenated
names, we realize that we can use the number of columns as a stack
argument, and *calculate* the mode:

.. code-block:: none
   
   : B&W    ( #cols -- )  DUP #COLUMNS !  20 /  2-     MODE ;
   : COLOR  ( #cols -- )  DUP #COLUMNS !  20 /  2-  1+ MODE ;

This gives us this syntax:

.. code-block:: none
   
   40 B&W
   80 B&W
   40 COLOR
   80 COLOR

We’ve reduced the number of commands from four to two.

Once again, though, we have some duplicate code. If we factor out this
code we get:

.. code-block:: none
   
   : COL-MODE!  ( #columns chroma?)
      SWAP DUP #COLUMNS !  20 / 2-  +  MODE ;
   : B&W    ( #columns -- )  0 COL-MODE! ;
   : COLOR  ( #columns -- )  1 COL-MODE! ;

Now we’ve achieved a nicer syntax, and at the same time greatly reduced
the size of the object code. With only two commands, as in this example,
the benefits may be marginal. But with larger sets of commands the
benefits increase geometrically.

Our final example is a set of words to represent colors on a particular
system. Names like ``BLUE`` and ``RED`` are nicer than numbers. One solution
might be to define:

.. code-block:: none
   
    0 CONSTANT BLACK                 1 CONSTANT BLUE
    2 CONSTANT GREEN                 3 CONSTANT CYAN
    4 CONSTANT RED                   5 CONSTANT MAGENTA
    6 CONSTANT BROWN                 7 CONSTANT GRAY
    8 CONSTANT DARK-GRAY             9 CONSTANT LIGHT-BLUE
   10 CONSTANT LIGHT-GREEN          11 CONSTANT LIGHT-CYAN
   12 CONSTANT LIGHT-RED            13 CONSTANT LIGHT-MAGENTA
   14 CONSTANT YELLOW               15 CONSTANT WHITE

These colors can be used with words such as BACKGROUND, FOREGROUND, and
BORDER:

.. code-block:: none
   
   WHITE BACKGROUND  RED FOREGROUND  BLUE BORDER

But this solution requires 16 names, and many of them are hyphenated. Is
there a way to simplify this?

We notice that the colors between 8 and 15 are all “lighter” versions of
the colors between 0 and 7. (In the hardware, the only difference
between these two sets is the setting of the “intensity bit.”) If we
factor out the “lightness,” we might come up with this
solution:

.. code-block:: none
   
   VARIABLE 'LIGHT?  ( intensity bit?)
   : HUE  ( color)  CREATE ,
      DOES>  ( -- color )  @  'LIGHT? @  OR  0 'LIGHT? ! ;
    0 HUE BLACK         1 HUE BLUE           2 HUE GREEN
    3 HUE CYAN          4 HUE RED            5 HUE MAGENTA
    6 HUE BROWN         7 HUE GRAY
   : LIGHT   8 'LIGHT? ! ;

With this syntax, the word

.. code-block:: none
   
   BLUE

by itself will return a “1” on the stack, but the phrase

.. code-block:: none
   
   LIGHT BLUE

will return a “9.” (The adjective LIGHT sets flag which is used by the
hues, then cleared.)

If necessary for readability, we still might want to define:

.. code-block:: none
   
   8 HUE DARK-GRAY
   14 HUE YELLOW

Again, through this approach we’ve achieved a more pleasant syntax and
shorter object code.

.. hint::

   Don't factor for the sake of factoring. Use clich\'es.

The phrase

.. code-block:: none
   
   OVER + SWAP

may be seen commonly in certain applications. (It converts an address
and count into an ending address and starting address appropriate for a
``DO LOOP``.)

Another commonly seen phrase is

.. code-block:: none
   
   1+ SWAP

(It rearranges a first-number and last-number into the
last-number-plus-one and first-number order required by
``DO``.)

It’s a little tempting to seize upon these phrases and turn them into
words, such as (for the first phrase) ``RANGE``.

**Moore**:
    That particular phrase ``OVER``  ``+``   ``SWAP`` is one
    that's right on the margin of being a useful word. Often, though, if
    you define something as a word, it turns out you use it only once. If
    you name such a phrase, you have trouble knowing exactly what
    ``RANGE`` does. You can't see the manipulation in your
    mind. ``OVER``  ``+``   ``SWAP`` has greater mnemonic
    value than ``RANGE``.

I call these phrases “clich\'es.” They stick together as meaningful
functions. You don’t have to remember how the phrase works, just what it
does. And you don’t have to remember an extra name.

Compile-Time Factoring
======================

In the last section we looked at many techniques
for organizing code and data to reduce redundancy.

We can also apply limited redundancy during compilation, by letting
Forth do some of out dirty work.

.. hint::

   For maximum maintainability, limit redundancy even at compile time.

Suppose in our application we must draw nine boxes as shown in
:numref:`fig6-1` .

.. code-block:: none
   :caption: What we\'re supposed to display
   :name: fig6-1
   
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********
   ********     ********     ********

In our design we need to have constants that represent values such as
the size of each box, the size of the gap between boxes, and the
left-most and top-most coordinates of the first box.

Naturally we can define:

.. code-block:: none
   
   8 CONSTANT WIDE
   5 CONSTANT HIGH
   4 CONSTANT AVE
   2 CONSTANT STREET

(Streets run east and west; avenues run north and south.)

Now, to define the left margin, we might compute it mentally, We want to
center all these boxes on a screen 80 columns wide. To center something,
we subtract its width from 80 and divide by two to determine the left
margin. To figure the total width of all the boxes, we add

.. math::

   8 + 4 + 8 + 4 + 8 = 32

 (three widths and two avenues). :math:`(80-31) / 2 = 24`.

So we could, crudely, define:

.. code-block:: none
   
   24 CONSTANT LEFTMARGIN

and use the same approach for ``TOPMARGIN``.

But what if we should later redesign the pattern, so that the width
changed, or perhaps the gap between the boxes? We’d have to recompute
the left margin ourselves.

In the Forth environment, we can use the full power of Forth even when
we’re compiling. Why not let Forth do the figuring?

.. code-block:: none
   
   WIDE 3 *  AVE 2 *  +  80 SWAP -  2/ CONSTANT LEFTMARGIN
   HIGH 3 *  STREET 2 * +  24 SWAP -  2/ CONSTANT TOPMARGIN

.. hint::

   If a constant's value depends on the value of an earlier constant, use
   Forth to calculate the value of the second.

None of these computations are performed when the application is
running, so run-time speed is not affected.

Here’s another example.  :numref:`fig6-2`  shows the code for a
word that draws shapes. The word ``DRAW`` emits a star at every x–y
coordinate listed in the table called ``POINTS``. (Note: the word ``XY``
positions the cursor to the ( x y ) coordinate on the stack.)

Notice the line immediately following the list of points:

.. code-block:: none
   
   HERE POINTS -  ( /table)  2/  CONSTANT #POINTS

.. code-block:: none
   :name: fig6-2
   :caption: Another example of limiting compile-time redundancy.
	     
   : P  ( x y -- )  C, C, ;
   CREATE POINTS
      10 10 P     10 11 P     10 12 P     10 13 P     10 14 P
      11 10 P     12 10 P     13 10 P     14 10 P
      11 12 P     12 12 P     13 12 P     14 12 P
   HERE POINTS -  ( /table)  2/  CONSTANT #POINTS
   : @POINTS  ( i -- x y)  2* POINTS + DUP 1+ C@  SWAP C@ ;
   : DRAW  #POINTS 0 DO  I @POINTS  XY  ASCII * EMIT  LOOP ;

The phrase ``HERE POINTS -`` computes the number of x–y coordinates in the
table: this value becomes the constant ``#POINTS``, used as the limit in
``DRAW`` \’s ``DO``   ``LOOP``.

This construct lets you add or subtract points from the table without
worrying about the number of points there are. Forth computes this for
you.

Compile-Time Factoring through Defining Words
---------------------------------------------

Let’s examine a series of approaches to the same problem—defining a
group of related addresses. Here’s the first try:

.. code-block:: none
   
   HEX 01A0 CONSTANT BASE.PORT.ADDRESS
   BASE.PORT.ADDRESS CONSTANT SPEAKER
   BASE.PORT.ADDRESS 2+ CONSTANT FLIPPER-A
   BASE.PORT.ADDRESS 4 + CONSTANT FLIPPER-B
   BASE.PORT.ADDRESS 6 + CONSTANT WIN-LIGHT
   DECIMAL

The idea is right, but the implementation is ugly. The only elements
that change from port to port are the numeric offset and the name of the
port being defined; everything else repeats. This repetition suggests
the use of a defining word.

The following approach, which is more readable, combines all the
repeated code into the “does” part of a defining word:

.. code-block:: none
   
   : PORT  ( offset -- )  CREATE ,
      \ does>  ( -- 'port) @ BASE.PORT.ADDRESS + ;
   0 PORT SPEAKER
   2 PORT FLIPPER-A
   4 PORT FLIPPER-B
   6 PORT WIN-LIGHT

In this solution we’re performing the offset calculation at *run*-time,
every time we invoke one of these names. It would be more efficient to
perform the calculation at compile time, like this:

.. code-block:: none
   
   : PORT  ( offset -- )  BASE.PORT.ADDRESS + CONSTANT ;
      \ does>  ( -- 'port)
   0 PORT SPEAKER
   2 PORT FLIPPER-A
   4 PORT FLIPPER-B
   6 PORT WIN-LIGHT

Here we’ve created a defining word, ``PORT``, that has a unique
*compile*-time behavior, namely adding the offset to ``BASE.PORT.ADDRESS``
and defining a ``CONSTANT``.

We might even go one step further. Suppose that all port addresses are
two bytes apart. In this case there’s no reason we should have to
specify these offsets. The numeric sequence

    0 2 4 6

is itself redundant.

In the following version, we begin with the ``BASE.PORT.ADDRESS`` on the
stack. The defining word ``PORT`` duplicates this address, makes a constant
out of it, then adds 2 to the address still on the stack, for the next
invocation of ``PORT``.

.. code-block:: none
   
   : PORT   ( 'port -- 'next-port)  DUP CREATE ,  2+ ;
      \ does>  ( -- 'port)
   BASE.PORT.ADDRESS
     PORT SPEAKER
     PORT FLIPPER-A
     PORT FLIPPER-B
     PORT WIN-LIGHT
   DROP ( port.address)

Notice we must supply the initial port address on the stack before
defining the first port, then invoke ``DROP`` when
we’ve finished defining all the ports to get rid of the port address
that’s still on the stack.

One final comment. The base-port address is very likely to change, and
therefore should be defined in only one place. This does *not* mean it
has to be defined as a constant. Provided that the base-port address
won’t be used outside of this lexicon of port names, it’s just as well
to refer to it by number here.

.. code-block:: none
   
   HEX 01A0  ( base port adr)
     PORT SPEAKER
     PORT FLIPPER-A
     PORT FLIPPER-B
     PORT WIN-LIGHT
   DROP


The Iterative Approach in Implementation
========================================

Earlier in the
book we discussed the iterative approach, paying particular attention to
its impact on the design phase. Now that we’re talking about
implementation, let’s see how the approach is actually used in writing
code.

.. hint::

   Work on only one aspect of a problem at a time.

Suppose we’re entrusted with the job of coding a word to draw or erase a
box at a given x–y coordinate. (This is the same problem we introduced
in the section called “Compile-Time Factoring.”)

At first we focus our attention on the problem of drawing a box—never
mind erasing it. We might come up with
this:

.. code-block:: none
   
   : LAYER   WIDE  0 DO  ASCII * EMIT  LOOP ;
   : BOX   ( upper-left-x  upper-left-y -- )
      HIGH  0 DO  2DUP  I +  XY LAYER  LOOP  2DROP ;

Having tested this to make sure it works correctly, we turn now to the
problem of using the same code to *un*\ draw a box. The solution is
simple: instead of hard-coding the ``ASCII *`` we\’d
like to change the emitted character from an asterisk to a blank. This
requires the addition of a variable, and some readable words for setting
the contents of the variable. So:

.. code-block:: none
   
   VARIABLE INK
   : DRAW   ASCII *  INK ! ;
   : UNDRAW   BL  INK ! ;
   : LAYER   WIDTH  0 DO  INK @  EMIT  LOOP ;

The definition of ``BOX``, along with the remainder of the application,
remains the same.

This approach allows the syntax

.. code-block:: none
   
   ( x y ) DRAW BOX

or

.. code-block:: none
   
   ( x y ) UNDRAW BOX

By switching from an explicit value to a variable that contains a value,
we’ve added a level of indirection. In this case, we’ve added
indirection “backwards,” adding a new level of complexity to the
definition of ``LAYER`` without substantially lengthening the definition.

By concentrating on one dimension of the problem at a time, you can
solve each dimension more efficiently. If there’s an error in your
thinking, the problem will be easier to see if it’s not obscured by yet
another untried, untested aspect of your code.

.. hint::

   Don't change too much at once.

While you’re editing your application—adding a new feature or fixing
something—it’s often tempting to go and fix several
other things at the same time. Our advice: Don’t.

Make as few changes as you can each time you edit-compile. Be sure to
test the results of each revision before going on. You’d be amazed how
often you can make three innocent modifications, only to recompile and
have nothing work!

Making changes one at a time ensures that when it stops working, you
know why.

.. hint::

   Don't try to anticipate ways to factor too early.

Some people wonder why most Forth
systems don’t include the definition word ARRAY. This rule is the
reason.

**Moore**:
    I often have a class of things called arrays. The simplest array
    merely adds a subscript to an address and gives you back an
    address. You can define an array by saying
    
    .. code-block:: none

       CREATE X   100 ALLOT
    
    then saying

    .. code-block:: none
   
       X +
    
    Or you can say
    
    .. code-block:: none
   
       : X   X + ;
    
    One of the problems that\'s most frustrating for me is knowing whether
    it\'s worth creating a defining word for a particular data structure.
    Will I have enough instances to justify it?
    
    I rarely know in advance if I\'m going to have more than one array. So
    I don't define the word ``ARRAY``.
    
    After I discover I need two arrays, the question is marginal.
    
    If I need three then it\'s clear. Unless they\'re different. And odds
    are they will be different. You may want it to fetch it for you. You
    may want a byte array, or a bit array. You may want to do bounds
    checking, or store its current length so you can add things to the
    end.
    
    I grit my teeth and say, "Should I make the byte array into a cell
    array, just to fit the data structure into the word I already have
    available?"
    
    The more complex the problem, the less likely it will be that you\'ll
    find a universally applicable data structure. The number of instances
    in which a truly complex data structure has found universal use is
    very small. One example of a successful complex data structure is the
    Forth dictionary. Very firm structure, great versatility. It's used
    everywhere in Forth. But that\s rare.

    If you choose to define the word ``ARRAY``, you\'ve done a
    decomposition step. You've factored out the concept of an array from
    all the words you\'ll later back in. And you've gone to another level
    of abstraction.
    
    Building levels of abstraction is a dynamic process, not one you can
    predict.

.. hint::

   Today, make it work. Tomorrow, optimize it.

Again **Moore**. On the day of this interview, Moore had been completing work on the design of a board-level Forth computer, using commercially available ICs. As part of his toolkit for designing the board, he created a simulator in Forth, to test the board\'s logic:
    This morning I realized I\'ve been mixing the descriptions of the chips
    with the placement of the chips on the board. This perfectly
    convenient for my purposes at the moment, but when I come up with
    another board that I want to use the same chips for, I have arranged
    things very badly.
    
    I should have factored it with the descriptions here and the uses
    there. I would then have had a chip description language. Okay. At the
    time I was doing this I was not interested in that level of
    optimization.
    
    Even if the thought had occurred to me then, I probably would have
    said, "All right, I'll do that later," then gone right ahead with
    what I was doing. Optimization wasn't the most important thing to me
    at the time.
    
    Of course I try to factor things well. But if there doesn't seem to be
    a good way to do something, I say, "Let's just make it work."
    
    My motivation isn't laziness, it's knowing that there are other things
    coming down the pike that are going to affect this decision in ways I
    can't predict. Trying to optimize this now is foolish. Until I get the
    whole picture in front of me, I can't know what the optimum is.

The observations in this section shouldn’t contradict what’s been said
before about information hiding and about anticipating elements that may
change. A good programmer continually tries to balance the expense of
building-in changeability against the expense of changing things later
if necessary.

These decisions take experience. But as a general rule:

.. hint::

   Anticipate things-that-may-change by organizing information, not by
   adding complexity. Add complexity only as necessary to make the
   current iteration work.

Summary
-------

In this chapter we’ve discussed various techniques and criteria for
factoring. We also examined how the iterative approach applies to the
implementation phase. 

REFERNCES
=========

.. [stevens74-6]  W.P. Stevens, G.J. Myers,and L.L. Constantine, ** IBM Systems Journal** , vol. 13, no. 2, 1974, Copyright 1974 byInternational Business Machines Corporation.
.. [miller56]  G.A. Miller, "The Magical Number Seven, Plus orMinus Two: Some Limits on our Capacity for Processing Information," **Psychol. Rev** ., vol. 63, pp. 81-97, Mar. 1956.
.. [harris83]  Kim R. Harris, "Definition Field AddressConversion Operators,"  **Forth--83 Standard** , Forth StandardsTeam.
