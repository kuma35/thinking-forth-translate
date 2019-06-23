
***********************************
3. Preliminary Design/Decomposition
***********************************

Assuming you have some idea of what your program
should accomplish, it’s time to begin the design. The first stage,
preliminary design, focuses on shrinking your mountainous problem into
manageable molehills.

In this chapter we’ll discuss two ways to decompose your Forth
application.

Decomposition by Component
==========================

Has this sort of thing ever happened to you? You’ve been planning for
three months to take a weekend vacation to the mountains. You’ve been
making lists of what to bring, and daydreaming about the slopes.

Meanwhile you’re deciding what to wear to your cousin’s wedding next
Saturday. They’re informal types, and you don’t want to overdress.
Still, a wedding’s a wedding. Maybe you should rent a tuxedo anyway.

For all this planning, it’s not until Thursday that you realize the two
events coincide. You have expletives for such moments.

How is such a mental lapse possible, in one so intelligent as yourself?
Apparently the human mind actually makes links between memories. New
ideas are somehow added onto existing paths of related thoughts.

.. figure:: fig3-1.png
   :name: fig3-1
   :alt: Pools of thought not yet linked

   Pools of thought not yet linked

..

In the mishap just described, no connection was ever made between the
two separately-linked pools of thought until Thursday. The conflict
probably occurred when some new input (something as trivial as hearing
Saturday’s weather report) got linked into both pools of thought. A
lightning flash of realization arced between the pools, followed
inexorably by thunderous panic.

A simple tool has been invented to avoid such disasters. It’s called a
calendar. If you had recorded both plans in the same calendar, you would
have seen the other event scheduled, something your brain failed to do
for all its intricate magnificence.

.. tip::

   To see the relationship between two things, put them close
   together. To remind yourself of the relationship, **keep** them
   together.

These truisms apply to software design, particularly to the preliminary
design phase. This phase is traditionally the one in which the designer
dissects a large application into smaller, programmer-sized modules.

In Chapter One we discovered that applications can be conveniently
decomposed into components.

.. tip::

   The goal of preliminary design is to determine what components are
   necessary to accomplish the requirements.

For instance, you might have an application in which events must occur
according to some predetermined schedule. To manage the scheduling, you
might first design a few words to constitute a “schedule-building
lexicon.” With these words you’ll be able to describe the order of
events that must occur within your application.

Thus within a single component, you’ll not only share information, but
also work out potential conflicts. The wrong approach would be to let
each functional module “know” things about its schedule that could
potentially conflict with another module’s schedule.

How can you know, in designing a component, what commands the using
components will need? Admittedly, this is something of a “chicken vs.
egg” problem. But Forth programmers handle it the same way chickens and
eggs do: iteratively.

If the component is well-designed, completeness doesn’t matter. In fact,
a component need only suffice for the current iteration’s design. No
component should be considered a “closed book” until the application has
been completed—which, in the case of maintained applications, is never.

As an example, imagine that your product needs to “talk” to other
machines in the outside world via a universal I/O chip that is part of
your system. This particular chip has a “control register” and a “data
register.” In a badly designed application, pieces of code throughout
the program would access the communication chip by simply invoking the
OUT instruction to put an appropriate command byte into the command
register. This makes the entire application needlessly dependent on that
particular chip—very risky.

Instead, Forth programmers would write a component to control the I/O
chip. These commands would have logical names and a convenient interface
(usually Forth’s stack) to allow usage by the rest of the application.

For any iteration of your product’s design, you would implement only the
commands needed so far—not all the valid codes that could be sent to the
“control register.” If later in the project cycle you realize that you
need an additional command, say one to change the baud rate, the new
command would be added to the I/O chip lexicon, not to the code needed
to set the baud rate. There’s no penalty for making this change except
the few minutes (at most) it takes to edit and recompile.

.. tip::

   Within each component, implement only the commands needed for the
   current iteration. (But don't preclude future additions.)

What goes on inside a component is pretty much its own business. It’s
not necessarily bad style for definitions within the component to share
redundant information.

For instance, a record in a certain data structure is fourteen bytes
long. One definition in the component advances a pointer 14 bytes to
point to the next record; another definition decrements the pointer 14
bytes.

As long as that number 14 remains a “secret” to the component and won’t
be used elsewhere, you don’t need to define it as constant. Just use the
number 14 in both definitions:

.. code-block:: none
   
   : +RECORD  14 RECORD# +! ;
   : -RECORD -14 RECORD# +! ;

..

On the other hand, if the value will be needed outside of the component,
or if it’s used several times within the component and there’s a good
chance that it will change, you’re better off hiding it behind a name:

.. code-block:: none
   
   14 CONSTANT /RECORD
   : +RECORD /RECORD RECORD# +! ;
   : -RECORD /RECORD NEGATE RECORD# +! ;

..

(The name /RECORD, by convention, means “bytes per record.”)

Example: A Tiny Editor
======================

Let’s apply decomposition by component to a real problem. It would be
nice to design a large application right here in Chapter Three, but
alas, we don’t have the room and besides, we’d get sidetracked in trying
to understand the application.

Instead, we’ll take a component from a large application that has
already been decomposed. We’ll design this component by decomposing it
further, into subcomponents.

Imagine that we must create a tiny editor that will allow users to
change the contents of input fields on their terminal screen. For
instance, the screen might look like this:

.. image:: chapter3-img1.png
   :scale: 50%

The editor will provide three modes for users to change the contents of
the input field:

Overwrite.
    Typing ordinary characters overwrites any characters that were there
    before.

Delete.
    Pressing the combination of keys “Ctrl D” deletes the character
    under the cursor and slides the remaining characters leftwards.

Insert.
    Pressing the combination of keys “Ctrl I” switches the editor into
    “Insert Mode,” where subsequently typing ordinary characters inserts
    them at the cursor position, sliding the remaining characters
    rightwards.

As part of the conceptual model we should also consider the error or
exception-handling; for instance, what is the limit of the field? what
happens in insert mode when characters spill off the right? etc.

That’s all the specification we have right now. The rest is up to us.

Let’s try to determine what components we’ll need. First, the editor
will react to keys that are typed at the keyboard. Therefore we’ll need
a keystroke interpreter—some kind of routine that awaits keystrokes and
matches them up with a list of possible operations. The keystroke
interpreter is one component, and its lexicon will consist of a single
word. Since that word will allow the editing of a field, let’s call the
word EDIT.

The operations invoked by the keystroke interpreter will comprise a
second lexicon. The definitions in this lexicon will perform the various
functions required. One word might be called DELETE, another INSERT,
etc. Since each of these commands will be invoked by the interpreter,
each of them will process a single keystroke.

Below these commands should lie a third component, the set of words that
implement the data structure to be edited.

.. figure:: fig3-2.png
   :name: fig3-2
   :alt: Generalized decomposition of the Tiny Editor problem.

   Generalized decomposition of the Tiny Editor problem.

..

Finally, we’ll need a component to display the field on the video
screen. For the sake of simplicity, let’s plan on creating one word
only, REDISPLAY, to redisplay the entire field after each key is
pressed.

.. code-block:: none
   
   : EDITOR  BEGIN  KEY  REVISE  REDISPLAY  ... UNTIL ;

..

This approach separates revising the buffer from updating the display.
For now, we’ll only concentrate on revising the buffer.

Let’s look at each component separately and try to determine the words
each will need. We can begin by considering the events that must occur
within the three most important editing functions: overwriting,
deleting, and inserting. We might draw something like the following on
the back of an old pizza menu (we won’t pay much attention to
exception-handling in the present discussion):

To Overwrite:
    1. Store new character into byte pointer to by pointer.
    2. Advance pointer (unless at end of field).

    .. image:: chapter3-img2.png

To Delete:
    1. Copy leftwards, by one place, the string
       beginning one place to the right of the pointer.
    2. Store a "blank" into the last position on the line.

    .. image:: chapter3-img3.png

To Insert:
    1. Copy rightwards, by one place, the string beginning at the pointer.
    2. Store new character into byte pointed to by pointer.
    3. Advance pointer (unless at end of field).

    .. image:: chapter3-img4.png

We’ve just developed the algorithms for the problem at hand.

Our next step is to examine these three essential procedures, looking
for useful “names”—that is procedures or elements which can either:

1. possibly be reused, or

2. possibly change

We discover that all three procedures use something called a “pointer.”
We need two procedures:

3. to get the pointer (if the pointer itself is relative, this function
   will perform some computation).

4. to advance the pointer

Wait, three procedures:

5. to move the pointer backwards

because we will want “cursor keys” to move the cursor forward and back
without editing changes.

These three operators will all refer to a physical pointer somewhere in
memory. Where it is kept and how (relative or absolute) should be hidden
within this component.

Let’s attempt to rewrite these algorithms in code:

.. code-block:: none
   
   : KEY#  ( returns value of key last pressed )  ... ;
   : POSITION  ( returns address of character pointed-to)  ;
   : FORWARD  ( advance pointer, stopping at last position)  ;
   : BACKWARD  ( decrement pointer, stopping at first position)  ;
   : OVERWRITE   KEY# POSITION C!  FORWARD ;
   : INSERT   SLIDE>  OVERWRITE ;
   : DELETE   SLIDE<  BLANK-END ;

..

To copy the text leftwards and rightwards, we had to invent two new
names as we went along, SLIDE< and SLIDE> (pronounced “slide-backwards”
and “slide-forwards” respectively). Both of them will certainly use
POSITION, but they also must rely on an element we’ve deferred
considering: a way to “know” the length of the field. We can tackle that
aspect when we get to writing the third component. But look at what we
found out already: we can describe “Insert” as simply “SLIDE>
OVERWRITE”.

In other words, “Insert” actually *uses* “Overwrite” even though they
appear to exist on the same level (at least to a Structured Programmer).

Instead of probing deeper into the third component, let’s lay out what
we know about the first component, the key interpreter. First we must
solve the problem of “insert mode.” It turns out that “insert” is not
just something that happens when you press a certain key, as delete is.
Instead it is a *different way of interpreting* some of the possible
keystrokes.

For instance in “overwrite” mode, an ordinary character gets stored into
the current cursor position; but in “insert mode” the remainder of the
line must first be shifted right. And the backspace key works
differently when the editor is in Insert Mode as well.

Since there are two modes, “inserting” and “not-inserting,” the
keystroke interpreter must associate the keys with two possible sets of
named procedures.

We can write our keystroke interpreter as a decision table (worrying
about the implementation later):

.. csv-table:: decision table
   :header: "Key", "Not-inserting", "Inserting"
   
   "Ctrl-D", "DELETE", "INSERT-OFF"
   "Ctrl-I", "INSERT-ON", "INSERT-OFF"
   "backspace", "BACKWARD", "INSERT<"
   "left-arrow", "BACKWARD", "INSERT-OFF"
   "right-arrow", "FORWARD", "INSERT-OFF"
   "return", "ESCAPE", "INSERT-OFF"
   "any printable", "OVERWRITE", "INSERT"

We’ve placed the possible types of keys in the
left column, what they do normally in the middle column, and what they
do in “insert mode” in the right column.

To implement what happens when “backspace” is pressed while in Insert
Mode, we add a new procedure:

.. code-block:: none
   
   : INSERT<   BACKWARD  SLIDE< ;

..

(move the cursor backwards on top of the last character typed, then
slide everything to the right leftward, covering the mistake).

This table seems to be the most logical expression of the problem at the
current level. We’ll save the implementation for later (Chapter Eight).

Now we’ll demonstrate the tremendous value of this approach in terms of
maintainability. We’ll throw ourselves a curve—a major change of plans!

Maintaining a Component-based Application
=========================================

How well will our design fare in the face of change? Envision the
following scenario:

We originally assumed that we could refresh the video display simply by
retyping the field every time a key is pressed. We even implemented the
code on our personal computer, with its memory-mapped video that
refreshes an entire line in the blink of a scan cycle. But now our
customer wants the application to run on a telephone-based network, with
all I/O being done at a not-so-fast baud rate. Since some of our input
fields are almost as wide as the video screen, maybe 65 characters, it
just takes too long to refresh the entire line on every key stroke.

We’ve got to change the application so that we only refresh that part of
the field that actually changes. In “insert” and “delete,” this would
mean the text to the right of the cursor. In “overwrite” it would mean
changing just the single character being overwritten.

This change is significant. The video refresh function, which we
cavalierly relegated to the key interpreter, now must depend on which
editing functions occur. As we’ve discovered, the most important names
needed to implement the key interpreter are:

.. code-block:: none
   
   FORWARD
   BACKWARD
   OVERWRITE
   INSERT
   DELETE
   INSERT<

..

None of their descriptions make any reference to the video refresh
process, because that was originally assumed to happen later.

But things aren’t as bad as they seem. Looking at it now, the process
OVERWRITE could easily include a command to type the new character where
the terminal’s cursor is. And SLIDE< and SLIDE> could include commands
to type everything to the right of, and including, POSITION, then reset
the terminal’s cursor to its current position.

Here are our revised procedure names. The commands just added are in
boldface:

.. code-block:: none
   
   : OVERWRITE  KEY# POSITION C!  KEY# EMIT  FORWARD ;
   : RETYPE  ( type from current position to
      end of field and reset cursor) ;
   : INSERT   SLIDE>  RETYPE  OVERWRITE ;
   : DELETE   SLIDE<  BLANK-END RETYPE ;

..

Since these are the only three functions that change memory, they are
the only three functions that need to refresh the screen. This idea is
critical. We must be able to make such assertions to assure program
correctness. The assertion is intrinsic to the nature of the problem.

Note that the additional problem of video refresh adds an additional
“pointer”: the current cursor position on the screen. But decomposition
by component has encouraged us to view the ``OVERWRITE`` process as changing
both the data field and the video vision of it; similarly with ``SLIDE<``
and ``SLIDE>`` . For this reason it seems natural now to maintain only one
real pointer—a relative one—from which we can compute either the data
address in memory, or the column number on the screen.

Since the nature of the pointer is wholly hidden within the three
processes ``POSITION`` , ``FORWARD`` , and ``BACKWARD`` , we can readily accommodate
this approach, even if it wasn’t our first approach.

This change may have seemed simple enough here—even obvious. If so, it’s
because the technique ensures flexible design. If we had used a
traditional approach—if we had designed according to structure, or
according to data transformation through sequential processes—our
brittle design would have been shattered by the change.

To prove this assertion, we’ll have to start all over again from
scratch.

Designing and Maintaining a Traditional Application
===================================================

Let’s pretend we haven’t studied the Tiny Editor problem yet, and we’re
back with a minimal set of specs. We’ll also start with our initial
assumption, that we can refresh the display by retyping the entire field
after each keystroke.

According to the dictum of top-down design, let’s take the widest-angle
view possible and examine the problem.  :numref:`fig3-3`  depicts
the program in its simplest terms. Here we’ve realized that the editor
is actually a loop which keeps getting keystrokes and performing some
editing function, until the user presses the return key.

.. figure:: fig3-3.png
   :name: fig3-3
   :alt: The traditional approach: view from the top.

   The traditional approach: view from the top.

..

Inside the loop we have three modules: getting a character from the
keyboard, editing the data, and finally refreshing the display to match
the data.

Clearly most of the work will go on inside “Process a Keystroke.”

Applying the notion of successive refinement,  :numref:`fig3-4` 
shows the editor problem redrawn with “Process a Keystroke” expanded. We
find it takes several attempts before we arrive at this configuration.
Designing this level forces us to consider many things at once that we
had deferred till later in the previous try.

.. figure:: fig3-4.png
   :name: fig3-4
   :alt: A structure for "Process a Keystroke."

   A structure for "Process a Keystroke."

..

For instance, we must determine all the keys that might be pressed. More
significantly, we must consider the problem of “insert mode.” This
realization forces us to invent a flag called INSERT-MODE which gets
toggled by the “Ctrl I” key. It’s used within several of the structural
lines to determine how to process a type of key.

A second flag, called ESCAPE, seems to provide a nice structured way of
escaping the editor loop if the user presses the return key while not in
insert mode.

Having finished the diagram, we’re bothered by the multiple tests for
Insert Mode. Could we test for Insert Mode once, at the beginning?
Following this notion, we draw yet another chart
( :numref:`fig3-5` ).

As you can see, this turns out even more awkward than the first figure.
Now we’re testing for each key twice. It’s interesting though, how the
two structures are totally different, yet functionally equivalent. It’s
enough to make one wonder whether the control structure is terribly
relevant to the problem.

.. figure:: fig3-5.png
   :name: fig3-5
   :alt: Another structure for "Process a Keystroke."

   Another structure for "Process a Keystroke."

..

Having decided on the first structure, we’ve finally arrived at the most
important modules—the ones that do the work of overwriting, inserting,
and deleting. Take another look at our expansion of “Process a
Character” in  :numref:`fig3-4` . Let’s consider just one of the
seven possible execution paths, the one that happens if a printable
character is pressed.

In  :numref:`fig3-6` (a) we see the original structural path for
a printable character.

Once we figure out the algorithms for overwriting and inserting
characters, we might refine it as shown in  :numref:`fig3-6` (b).
But look at that embarrassing redundancy of code (circled portions).
Most competent structured programmers would recognize that this
redundancy is unnecessary, and change the structure as shown in
:numref:`fig3-6` (c). Not too bad so far, right?

Change in Plan
--------------

Okay, everyone, now act surprised. We’ve just been told that this
application won’t run on a memory-mapped display. What does this change
do to our design structure?

.. figure:: fig3-6.png
   :name: fig3-6
   :alt: The same section, "refined" and "optimized."

   The same section, "refined" and "optimized."

..

Well, for one thing it destroys “Refresh Display” as a separate module.
The function of “Refresh Display” is now scattered among the various
structural lines inside “Process a Keystroke.” The structure of our
entire application has changed. It’s easy to see how we might have spent
weeks doing top-down design only to find we’d been barking down the
wrong tree.

What happens when we try to change the program? Let’s look again at the
path for any printable character.

:numref:`fig3-7`  (a) shows what happens to our first-pass
design when we add refresh. Part (b) shows our “optimized” design with
the refresh modules expanded. Notice that we’re now testing the Insert
flag twice within this single leg of the outer loop.

But worse, there’s a bug in this design. Can you find it?

In both cases, overwriting and inserting, the pointer is incremented
*before* the refresh. In the case of overwrite, we’re displaying the new
character in the wrong position. In the case of insert, we’re typing the
remainder of the line but not the new character.

Granted, this is an easy problem to fix. We need only move the refresh
modules up before “Increment Pointer.” The point here is: How did we
miss it? By getting preoccupied with control flow structure, a
superficial element of program design.

.. figure:: fig3-7.png
   :name: fig3-7
   :alt: Adding refresh.

   Adding refresh.

..

In contrast, in our design by components the correct solution fell out
naturally because we “used” the refresh component inside the editing
component. Also we used OVERWRITE inside INSERT.

By decomposing our application into components which use one another, we
achieved not only *elegance* but a more direct path to
*correctness*.

The Interface Component
=======================

In computer science terminology, interfacing between modules has two
aspects. First, there’s the way other modules invoke the module; this is
the control interface. Second, there’s the way other modules pass and
receive data to and from the module; this is the data interface.

Because of Forth’s dictionary structure, control is not an issue.
Definitions are invoked by being named. In this section, when we use the
term “interface” we’re referring to data.

When it comes to data interfaces between modules, traditional wisdom
says only that “interfaces should be carefully designed, with a minimum
of complexity.” The reason for the care, of course, is that each module
must implement its own end of the interface ( :numref:`fig3-8` ).

This means the presence of redundant code. As we’ve seen, redundant code
brings at least two problems: bulky code and poor maintainability. A
change to the interface of one module will affect the interface of the
opposite module.

.. figure:: fig3-8.png
   :name: fig3-8
   :alt: Traditional view of the interface as a junction.

   Traditional view of the interface as a junction.

..

There’s more to good interface design than that. Allow me to introduce a
design element which I call the “interface component.” The purpose an
interface component is to implement, and *hide information
about*, the data interface
between two or more other components ( :numref:`fig3-9` ).

.. figure:: fig3-9.png
   :name: fig3-9
   :alt: Use of the interface component.

   Use of the interface component.

..

.. tip::

   Both data structures and the commands involved in the communication of
   data between modules should be localized in an interface component.

Let me give an example from my own recent experience. One of my hobbies
is writing text formatter/editors. (I’ve written two of them, including
the one on which I am writing this book.)

In my latest design the formatter portion contains two components. The
first component reads the source document and decides where to make line
and page breaks, etc. But instead of sending the text directly to the
terminal or printer, it saves up a line’s worth at a time in a “line
buffer.”

Similarly, instead of sending printer-control commands—for bold-facing,
underlining, etc.—as the text is being formatted, it defers these
commands until the text is actually sent. To defer the control commands,
I have a second buffer called the “attribute buffer.” It corresponds,
byte-for-byte, with the line buffer, except that each byte contains a
set of flags that indicate whether the corresponding character should be
underlined, boldfaced, or whatever.

The second component displays or prints the contents of the line buffer.
The component knows whether it is transmitting to the terminal or to the
printer, and outputs the text according to the attributes indicated by
the attribute buffer.

Here we have two well-defined components—the line-formatter and the
output component—each one shouldering part of the function of the
formatter as a whole.

The data interface between these two components is fairly complex. The
interface consists of two buffers, a variable that indicates the current
number of valid characters, and finally a “knowledge” of what all those
attribute patterns mean.

In Forth I’ve defined these elements together in a single screen. The
buffers are defined with ``CREATE``, the count is an
ordinary ``VARIABLE``, and the attribute patterns are
defined as ``CONSTANT`` s, such as:

.. code-block:: none
   
   1 CONSTANT UNDERNESS  ( bit mask for underlining)
   2 CONSTANT BOLDNESS  ( bit mask for boldface)

..

The formatting component uses phrases like ``UNDERNESS SET-FLAG`` to set
bits in the attribute buffer. The output component uses phrases like
``UNDERNESS AND`` to read the attribute buffer.

A Design Mistake
----------------

In designing an interface component, you should ask yourself “What is
the set of structures and commands that must be shared by the
communicating components?” It’s important to determine what elements
belong to the interface and what elements should remain within a single
component.

In writing my text formatter, I failed to answer this question fully and
found myself with a bug. The problem was this:

I allow different type widths to be used: condensed, double width, etc.
This means not only sending different signals to the printer, but
changing the number of characters allowed per line.

I keep a variable, called WALL, for the formatter. WALL indicates the
right margin: the point beyond which no more text can be set. Changing
to a different type width means changing the value of WALL
proportionately. (Actually, this turns out to be a mistake in itself. I
should be using a finer unit of measurement, the number of which remains
constant for the line. Changing type widths would mean changing the
number of units per character. But getting back to the mistake at hand…)

Alas, I was also using WALL inside the output component to determine how
many characters to display. My reasoning was that this value would
change depending on what type-width I was using.

I was right—99% of the time. But one day I discovered that, under a
certain condition, a line of condensed text was being somehow cut short.
The final couple of words were just missing. The reason turned out to be
that WALL was getting changed before the output component had a chance
to use it.

Originally I had seen nothing wrong with letting the output component
blithely use the formatter’s WALL as well. Now I realized that the
formatter had to leave a separate variable for the output component, to
indicate how many valid characters were in the buffers. This would leave
any subsequent font commands free to change WALL.

It was important that the two buffers, the attribute commands, and the
new variable were the *only* elements that could be shared between the
two modules. Reaching into either module from the other one spells
trouble.

The moral of this story is that we must distinguish between data
structures that are validly used only within a single component and
those that may be shared by more than one component.

A related point:

.. tip::

   Express in objective units any data to be shared by components.

For example:

-  Module A measures the temperature of the oven.

-  Module B controls the burner.

-  Module C makes sure the door is locked if the oven is too hot.

The information of global interest is the temperature of the oven,
expressed objectively in degrees. While Module A might receive a value
representing the voltage from a heat sensor, it should convert this
value to degrees before presenting it to the rest of the
application.

Decomposition by Sequential Complexity
======================================

We’ve been discussing one way to do decomposition: according to
components. The second way is according to sequential complexity.

One of Forth’s rules is that a word must already have been defined to be
invoked or referred to. Usually the sequence in which words are defined
parallels the order of increasing capabilities which the words must
possess. This sequence leads to a natural organization of the source
listing. The powerful commands are simply added on top of the elementary
application ( :numref:`fig3-10` a).

Like a textbook, the elementary stuff comes first. A newcomer to the
project would be able to read the elementary parts of the code before
moving on the advanced stuff.

.. figure:: fig3-10.png
   :name: fig3-10
   :alt: Two ways to add advanced capabilities.

   Two ways to add advanced capabilities.

..

But in many large applications, the extra capabilities are best
implemented as an enhancement to some private, root function in the
elementary part of the application ( :numref:`fig3-10` b). By
being able to change the root’s capability, the user can change the
capability of all the commands that use the root.

Returning to the word processor for an example, a fairly primitive
routine is the one that starts a new page. It’s used by the word that
starts a new line; when we run out of lines we must start a new page.
The word that starts a new line, in turn, is used by the routine that
formats words on the line; when the next word won’t fit on the current
line, we invoke ``NEWLINE`` . This “uses” hierarchy demands that we define
``NEWPAGE`` early in the application.

The problem? One of the advanced components includes a routine that must
be invoked by ``NEWPAGE`` . Specifically, if a figure or table appears in the
middle of text, but at format time won’t fit on what’s left of the page,
the formatter defers the figure to the next page while continuing with
the text. This feature requires somehow “getting inside of” ``NEWPAGE`` , so
that when ``NEWPAGE`` is next executed, it will format the deferred figure
at the top of the new page:

.. code-block:: none
   
   : NEWPAGE  ... ( terminate page with footer)
      ( start new page with header)  ...  ?HOLDOVER ... ;

..

How can ``NEWPAGE`` invoke ``?HOLDOVER`` , if ``?HOLDOVER`` is not defined until much
later?

While it’s theoretically possible to organize the listing so that the
advanced capability is defined before the root function, that approach
is bad news for two reasons.

First, the natural organization (by degree of capability) is destroyed.
Second, the advanced routines often use code that is defined amid the
elementary capabilities. If you move the advanced routines to the front
of the application, you’ll also have to move any routines they use, or
duplicate the code. Very messy.

You can organize the listing
by degree of complexity using a technique called “vectoring.” You can
allow the root function to invoke (point to) any of various routines
that have been defined after the root function itself. In our example,
only the *name* of the routine ``?HOLDOVER`` need be created early; its
definition can be given
later.

Chapter Seven treats the subject of vectoring in
Forth.

The Limits of Level Thinking
============================

Most of us are guilty of over-emphasizing the difference between
“high-level” and “low-level.” This notion is an arbitrary one. It limits
our ability to think clearly about software problems.

“Level” thinking, in the traditional sense, distorts our efforts in
three ways:

#. It implies that the order of development should follow a hierarchical
   structure

#. It implies that levels should be segregated from each other,
   prohibiting the benefits of reusability

#. It fosters syntactical differences between levels (e.g., assembler
   vs. “high-level” languages) and a belief that the nature of
   programming somehow changes as we move further from machine code.

Let’s examine each of these misconceptions one by one.

Where to Begin?
---------------

I asked Moore how he would go about
developing a particular application, a game for children. As the child
presses the digits on the numeric keypad, from zero to nine, that same
number of large boxes would appear on the screen.

**Moore**:
    I don't start at the top and work down. Given that exact
    problem, I would write a word that draws a box. I'd start at
    the bottom, and I'd end up with a word called ``GO`` ,
    which monitored the keyboard.

How much of that is intuitive?

    Perhaps some degree of it. I know where I'm going so I don't
    have to start there. But also it's more fun to draw boxes than
    to program a keyboard. I'll do the thing that's most fun in
    order to get into the problem. If I have to clean up all those
    details later, that's the price I pay.

Are you advocating a "fun-down" approach?

    Given that you're doing it in a free-spirit fashion, yes. If
    we were giving a demonstration to a customer in two days, I'd
    do it differently. I would start with the most visible thing,
    not the most fun thing. But still not in that hierarchical
    sequence, top down. I base my approach on more immediate
    considerations such as impressing the customer, getting
    something to work, or showing other people how it's going to
    work to get them interested.
    
    If you define a level as "nesting," then yes, it's a good
    way to decompose a problem. But I've never found the notion of
    "level" useful. Another aspect of levels is languages,
    metalanguages, meta-metalanguages. To try and split hairs as
    to which level you are on---assembler level, first integration
    level, last integration level---it's just tedious and not
    helpful. My levels get all mixed up hopelessly.

Designing by components makes where you start less important. You could
start with the key interpreter, for instance. Its goal is to receive
keystrokes and convert them to numbers, passing these numbers to an
internally invoked word. If you substitute the Forth word . (“dot,”
which prints a number from the stack), then we can implement the key
interpreter, test it, and debug it without using routines that have
anything to do with drawing squares. On the other hand, if the
application required hardware support (such as a graphics package) that
we didn’t have on hand, we might want to substitute something available,
such as displaying an asterisk, just to get into the problem. Thinking
in terms of lexicons is like painting a huge mural that spans several
canvases. You work on all the canvases at once, first sketching in the
key design elements, then adding splashes of color here and there… until
the entire wall is complete.

.. tip::

   In deciding where to start designing, look for:
   
   * areas where the most creativity is required
     (the areas where change is most likely)
   * areas that give the most satisfying feedback
     (get the juices flowing)
   * areas in which the approach decided upon will greatly affect other
     areas, or which will determine whether the stated problem can be
     solved at all
   * things you should show the customer, for mutual understanding
   * things you can show the investors, if necessary for the rent.

No Segregation Without Representation
-------------------------------------

The second way in which levels can
interfere with optimal solutions is by encouraging segregation of the
levels. A popular design construct called the “object” typifies this
dangerous
philosophy. [#f1]_

An object is a portion of code that can be invoked by a single name, but
that can perform more than one function. To select a particular function
you have to invoke the object and pass it a parameter or a group of
parameters. You can visualize the parameters as representing a row of
buttons you can push to make the object do what you want.

The benefit of designing an application in terms of objects is that,
like a component, the object hides information from the rest of the
application, making revision easier.

There are several problems, though. First, the object must contain a
complicated decision structure to determine which function it must
perform. This increases object size and decreases performance. A
lexicon, on the other hand, provides all usable functions by name for
you to invoke directly.

Second, the object is usually designed to stand alone. It can’t take
advantage of tools provided by supporting components. As a result, it
tends to duplicate code inside itself that will appear elsewhere in the
application. Some objects are even required to parse text in order to
interpret their parameters. Each may even use its own syntax. A
shameless waste of time and energy!

.. figure:: no-scrambled.png
   :name: no-scrambled
   :alt: "No scrambled?"

   "No scrambled?"

..

Finally, because the object is constructed to recognize a finite set of
possibilities, it’s difficult to make additions to the row of buttons
when a new function is needed. The tools inside the object have not been
designed for reuse.

The idea of levels pervades the design of the IBM Personal Computer.
Besides the processor itself (with its own machine instruction set, of
course), there are these software levels:

-  the set of utilities written in assembler and burned into the
   system’s ROM

-  the disk operating system, which invokes the utilities

-  the high-level language of choice, which invokes the operating system
   and the utilities

-  and finally, any application using the language.

The ROM utilities provide the hardware-dependent
routines: those that handle the video screen, disk drives, and keyboard.
You invoke them by placing a control code in a certain register and
generating the appropriate software interrupt.

For instance, software interrupt 10H causes entry to the video routines.
There are 16 of these routines. You load register AH with the number of
the video routine you want.

Unfortunately, in all 16 routines there is not one that displays a text
string. To do that, you must repeat the process of loading registers and
generating a software interrupt, which in turn must make a decision
about which routine you want, and do a few other things you don’t
need—for *every single character*.

Try writing a text editor in which the entire screen may need to be
refreshed with each keystroke. Slow as mail! You can’t improve the speed
because you can’t reuse any of the information within the video routines
except for what’s provided on the outside. The stated reason for this is
to “insulate” the programmer from device addresses and other details of
the hardware. After all, these could change with future upgrades.

The only way to efficiently implement video I/O on this machine is to
move strings directly into video memory. You can do this easily, because
the reference manual tells you the address at which video memory starts.
But this defeats the intent of the system’s designers. Your code may no
longer survive a hardware revision.

By supposedly “protecting” the programmer from details, segregation has
defeated the purpose of information hiding. Components, in contrast, are
not segregated modules but rather cumulative additions to the
dictionary. A video lexicon would, at the very least, give a name for
the address of video memory.

It’s not that anything’s wrong with the concept of a bit-switch function
interface between components, when it’s necessary. The problem here is
that this video component was incompletely designed. On the other hand,
if the system had been fully integrated—operating system and drivers
written in Forth—the video component would not *have* to be designed to
suit all needs. An application programmer could either rewrite the
driver or write an extension to the driver using available tools from
the video lexicon.

.. tip::

   Don't bury your tools.

The Tower of Babble
-------------------

The final deception perpetrated by level thinking is that programming
languages should become qualitatively different the “higher” you go. We
tend to speak of high-level code as something rarefied, and low-level
code as something grubby and profane.

To some degree these distinctions have validity, but this is only the
result of certain arbitrary architectural constraints that we all accept
as the norm. We’ve grown accustomed to assemblers with terse mnemonics
and unnatural syntactical rules, because they’re “low-level.”

The component concept rebels against the polarity of high-level vs.
low-level. All code should look and feel the same. A component is simply
a set of commands that together transform data structures and algorithms
into useful functions. These functions can be used without knowledge of
the structures and/or algorithms within.

The distance of these structures from actual machine code is irrelevant.
The code written to toggle bits in an output port should, in theory,
look no more intimidating than the code to format a report.

Even machine code should be readable. A true Forth-based engine would
enjoy a syntax and dictionary identical and continuous with the
“high-level” dictionary we know
today.`Level'' thinking, limits of|)}`

Summary
=======

In this chapter we’ve seen two ways that applications can be decomposed:
into components, and according to sequential complexity.

Special attention should be paid to those components that serve as
interfaces between other components.

Now, if you’ve done preliminary design correctly, your problem is lying
at your feet in a heap of manageable pieces. Each piece represents a
problem to solve. Grab your favorite piece and turn to the next
chapter.

For Further Thinking
====================

*(Answers appear in Appendix D.)*

#. Below are two approaches to defining an editor’s keyboard
   interpreter. Which would you prefer? Why?

   (a).
   
   .. code-block:: none
      
      ( Define editor keys )
      HEX
      72 CONSTANT UPCURSOR
      80 CONSTANT DOWNCURSOR
      77 CONSTANT RIGHTCURSOR
      75 CONSTANT LEFTCURSOR
      82 CONSTANT INSERTKEY
      83 CONSTANT DELETEKEY
      DECIMAL
      ( Keystroke interpreter)
      : EDITOR
         BEGIN  MORE WHILE  KEY   CASE
            UPCURSOR     OF  CURSOR-UP     ENDOF
            DOWNCURSOR   OF  CURSOR-DOWN   ENDOF
            RIGHTCURSOR  OF  CURSOR>       ENDOF
            LEFTCURSOR   OF  CURSOR<       ENDOF
            INSERTKEY    OF  INSERTING     ENDOF
            DELETEKEY    OF  DELETE        ENDOF
         ENDCASE  REPEAT ;

   (b).
   
   .. code-block:: none
   
      ( Keystroke interpreter)
      : EDITOR
         BEGIN  MORE WHILE  KEY   CASE
            72 OF  CURSOR-UP     ENDOF
            80 OF  CURSOR-DOWN   ENDOF
            77 OF  CURSOR>       ENDOF
            75 OF  CURSOR<       ENDOF
            82 OF  INSERTING     ENDOF
            83 OF  DELETE        ENDOF
         ENDCASE  REPEAT ;

#. This problem is an exercise in information hiding. Let’s suppose we
   have a region of memory outside of the Forth dictionary which we want
   to allocate for data structures (for whatever reason). The region of
   memory begins at HEX address C000. We want to define a series of
   arrays which will reside in that memory.

   We might do something like this:

   .. code-block:: none
   
      HEX
      C000 CONSTANT FIRST-ARRAY  ( 8 bytes)
      C008 CONSTANT SECOND-ARRAY  ( 6 bytes)
      C00C CONSTANT THIRD ARRAY  ( 100 bytes)

   Each array-name defined above will return the starting address of the
   appropriate array. But notice we had to compute the correct starting
   address for each array, based on how many bytes we had already
   allocated. Let’s try to automate this, by keeping an “allocation
   pointer,” called >RAM, showing where the next free byte is. We first
   set the pointer to the beginning of the RAM space:

   .. code-block:: none
   
      VARIABLE >RAM
      C000 >RAM !

   Now we can define each array like this: x

   .. code-block:: none
   
      >RAM @ CONSTANT FIRST-ARRAY    8 >RAM +!
      >RAM @ CONSTANT SECOND-ARRAY   6 >RAM +!
      >RAM @ CONSTANT THIRD-ARRAY  100 >RAM +!

   Notice that after defining each array, we increment the pointer by
   the size of the new array to show that we’ve allocated that much
   additional RAM.

   To make the above more readable, we might add these two definitions:

   .. code-block:: none
   
      : THERE ( -- address of next free byte in RAM)
           >RAM @ ;
      : RAM-ALLOT ( #bytes to allocate -- )  >RAM +! ;

   We can now rewrite the above equivalently as:

   .. code-block:: none
   
      THERE CONSTANT FIRST-ARRAY    8 RAM-ALLOT
      THERE CONSTANT SECOND-ARRAY   6 RAM-ALLOT
      THERE CONSTANT THIRD-ARRAY  100 RAM-ALLOT

   (An advanced Forth programmer would probably combine these operations
   into a single defining word, but that whole topic is not germane to
   what I’m leading up to.)

   Finally, suppose we have 20 such array definitions scattered
   throughout our application.

   Now, the problem: Somehow the architecture of our system changes and
   we decide that we must allocate this memory such that it *ends* at
   HEX address EFFF. In other words, we must start at the end,
   allocating arrays backwards. We still want each array name to return
   its *starting* address, however.

   To do this, we must now write:

   .. code-block:: none
   
      F000 >RAM ! ( EFFF, last byte, plus one)
      : THERE ( -- address of next free byte in RAM)
           >RAM @ ;
      : RAM-ALLOT  ( #bytes to allocate)  NEGATE >RAM +! ;
        8 RAM-ALLOT  THERE CONSTANT FIRST-ARRAY
	6 RAM-ALLOT  THERE CONSTANT SECOND-ARRAY
      100 RAM-ALLOT  THERE CONSTANT THIRD-ARRAY

   This time ``RAM-ALLOT`` *decrements* the pointer. That’s okay, it’s easy
   to add ``NEGATE`` to the definition of ``RAM-ALLOT``. Our present concern is
   that each time we define an array we must ``RAM-ALLOT`` *before* defining
   it, not after. Twenty places in our code need finding and correcting.

   The words ``THERE`` and ``RAM-ALLOT`` are nice and friendly, but they didn’t
   succeed at hiding *how* the region is allocated. If they had, it
   wouldn’t matter which order we invoked them in.

   At long last, our question: What could we have done to ``THERE`` and
   ``RAM-ALLOT`` to minimize the impact of this design change? (Again, the
   answer I’m looking for has nothing to do with defining words.)

.. rubric:: Footnotes

.. [#f1] Editor\'s note: But see the recant in the 1994 Preface on page
	 :doc:`preface94` , and the clairification in the 2004 Preface on page
	 :doc:`preface2004` . Think of something like Windows COM "objects" or
	 CORBA.
	 Real object oriented programming, as it originates in Smalltalk, does
	 not hide information from the programmer. Adding a "scrambled"
	 method to the "egg master object" is no problem. Smalltalk works by
	 adding methods to known classes, you don't even need to subclass them.
	 You can look inside an object and its source code whenever you want.
	 And table driven method dispatching can be quite efficient.
	 \-\-\- **Bernd Paysan**
