
******************************
Appendix B: Defining DOER/MAKE
******************************

If your system
doesn’t have ``DOER`` and ``MAKE`` already defined, this appendix is meant to
help you install them and, if necessary, understand how they work.
Because by its nature this construct is system dependent, I’ve included
several different implementations at the end of this appendix in the
hope that one of them will work for you. If no, and if this section
doesn’t give you enough information to get them running, you probably
have an unusual system. Please don’t ask me for help; ask your Forth
vendor.

Here’s how it works. ``DOER`` is a defining word that
creates an entry with one cell in its parameter field. That cell
contains the vector address, and is initialized to point to a no-op word
called ``NOTHING``.

Children of ``DOER`` will execute that
``DOES>`` code of ``DOER``, which
does only two things: fetch the vector address and place it on the
return stack. That’s all. Forth execution then continues with this
address on the return stack, which will cause the vectored function to
be performed. It’s like saying (in ’83-Standard)

.. code-block:: forth
   
   ' NOTHING >BODY >R <return>

..

which executes ``NOTHING`` . (This trick only works with colon definitions.)

Here’s an illustration of the dictionary entry created when we enter

.. image:: appendixb-img1.png

Now suppose we define:

.. code-block:: forth
   
   : TEST   MAKE JOE  CR ;

..

that is, we define a word that can vector ``JOE`` to do a carriage return.

Here’s a picture of the compiled definition of ``TEST`` :

.. image:: appendixb-img2.png

Let’s look at the code for
``MAKE``. Since we’re using ``MAKE``
inside a colon definition, ``STATE`` will be true, and
we’ll execute the phrase:

.. code-block:: forth
   
   COMPILE (MAKE)  HERE MARKER !  0 ,

..

We can see how ``MAKE`` has compiled the address of
the run-time routine, ``(MAKE)``, followed by a zero.
(We’ll explain what the zero is for, and why we save its address in the
variable ``MARKER``, later). Now let’s look at what
``(MAKE)`` does when we execute our new definition
``TEST`` :

.. list-table::
   :widths: 22 50

   * - R>
     - Gets an address from the return stack.
       This address points to the cell just
       past ``(MAKE)`` , where the zero is.
   * - DUP 2+
     - Gets the address of the second cell after
       ``(MAKE)`` , where the address of ``JOE`` is.
   * - DUP 2+
     - Gets the address of the third cell after
       ``(MAKE)`` , where the code we want to
       execute begins.  The stack now has:
       ( 'marker, 'joe, 'code --- )
   * - SWAP @ >BODY
     - Fetches the contents of the address
       pointing to ``JOE`` (i.e., gets the address
       ``JOE`` and computes ``JOE`` s pfa, where
       the vector address goes.
   * - !
     - Stores the address where the new code
       ``CR`` , etc.) into the vector
       address of ``JOE`` .
   * -
     - Now ``JOE`` points inside the definition of
       ``TEST`` .  When we type ``JOE`` , we'll do a
       carriage return.
   * - @ ?DUP IF >R THEN
     - Fetches the contents of the cell
       containing zero.  Since the cell does
       contain zero, the ``IF THEN`` statement is
       not performed.

That’s the basic idea. But what about that cell containing zero? That’s
for the use of ``;AND``. Suppose we changed TEST to
read:

.. code-block:: forth
   
   : TEST   MAKE JOE  CR ;AND SPACE ;

..

That is, when we invoke TEST we’ll vector JOE to do a
``CR``, and we’ll do a ``SPACE``
right now. Here’s what this new version of TEST will look like:

.. image:: appendixb-img3.png

Here’s the definition of ``;AND``:

.. code-block:: forth
   
   : ;AND   COMPILE  EXIT  HERE MARKER @ ! ;   IMMEDIATE

..

We can see that ``;AND`` has compiled an
``EXIT``, just as semicolon
would.

Next, recall that ``MAKE`` saved the address of that
cell in a variable called ``MARKER``. Now
``;AND`` stores ``HERE`` (the
location of the second string of code beginning with
``SPACE``) into the cell previously containing zero.
Now ``(MAKE)`` has a pointer to the place to resume
execution. The phrase

.. code-block:: forth
   
   IF >R THEN

..

will leave on the return stack the address of the code beginning with
``SPACE``. Thus execution will skip over the code
between ``MAKE`` and ``;AND`` and
continue with the remainder of the definition up to semicolon.

The word ``UNDO`` ticks the name of a
``DOER`` word, and stores the address of
``NOTHING`` into it.

One final note: on some systems you may encounter a problem. If you use
``MAKE`` outside of a colon definition to create a
forward reference, you may not be able to find the most recently defined
word. For instance, if you have:

.. code-block:: forth
   
   : REFRAIN   DO-DAH  DO-DAH ;
   MAKE SONG  CHORUS  REFRAIN ;

..

your system might think that refrain has not been defined. The problem
is due to the placement of ``SMUDGE`` . As a solution, try rearranging the
order of definitions or, if necessary, put ``MAKE`` code inside a definition
which you then execute:

.. code-block:: forth
   
   : SETUP   MAKE SONG  CHORUS  REFRAIN ;   SETUP

..

In Laboratory Microsystems PC/FORTH 2.0, the ``UNSMUDGE`` on line 9 handles
the problem. This problem does not arise with the Laxen/Perry/Harris
model.

The final screen is an example of using ``DOER/MAKE``.
After loading the block, enter

.. code-block:: forth
   
   RECITAL

..

then enter

.. code-block:: forth
   
   WHY?

..

followed by return, as many times as you like (you’ll get a different
reason each time).

.. code-block:: forth
   :lineno-start: 0

   ( DOER/MAKE   Shadow screen                      LPB 12/05/83 )
   NOTHING   A no-opp
   DOER      Defines a word whose behavior is vectorable.
   MARKER    Saves adr for optional continuation pointer.
   (MAKE)    Stuffs the address of further code into the
             parameter field of a doer word.
   MAKE      Used interpretively:  MAKE doer-name  forth-code ;
             or inside a definition:
                : def   MAKE doer-name  forth-code ;
             Vectors the doer-name word to the forth-code.
   ;AND      Allows continuation of the "making" definition
   UNDO      Usage:  UNDO doer-name ; makes it safe to execute

.. code-block:: forth
   :lineno-start: 0

   \ DOER/MAKE   FORTH-83 Laxen/Perry/Harris model  LPB 12/05/83 
   : NOTHING ;
   : DOER   CREATE  ['] NOTHING  >BODY ,  DOES> @ >R ;
   VARIABLE MARKER
   : (MAKE)  R>  DUP 2+  DUP 2+  SWAP @  >BODY !
      @ ?DUP IF >R THEN ;
   : MAKE   STATE @ IF ( compiling)
      COMPILE (MAKE)  HERE MARKER !  0 ,
      ELSE  HERE  [COMPILE] '  >BODY !
      [COMPILE] ]  THEN ;   IMMEDIATE
   : ;AND   COMPILE EXIT  HERE MARKER @ ! ;   IMMEDIATE
   : UNDO   ['] NOTHING  >BODY  [COMPILE] '  >BODY ! ;
   \
   \ The code in this screen is in the public domain.

.. code-block:: forth
   :lineno-start: 0

   ( DOER/MAKE   FORTH-83 Lab. Micro PC/FORTH 2.0   LPB 12/05/83 )
   : NOTHING ;
   : DOER   CREATE  ['] NOTHING  >BODY ,  DOES> @ >R ;
   VARIABLE MARKER
   : (MAKE)  R>  DUP 2+  DUP 2+  SWAP @  >BODY !
      @ ?DUP IF >R THEN ;
   : MAKE   STATE @ IF ( compiling)
      COMPILE (MAKE)  HERE MARKER !  0 ,
      ELSE  HERE  [COMPILE] '  >BODY !
      [COMPILE] ] UNSMUDGE  THEN ;   IMMEDIATE
   : ;AND   COMPILE EXIT  HERE MARKER @ ! ;   IMMEDIATE
   : UNDO   ['] NOTHING  >BODY  [COMPILE] '  >BODY ! ;
   \
   ( The code in this screen is in the public domain.)

.. code-block:: forth
   :lineno-start: 0

   ( DOER/MAKE   FIG model                          LPB 12/05/83 )
   : NOTHING   ;
   : DOES-PFA  ( pfa -- pfa of child of <BUILD-DOES> )   2+ ;
   : DOER   <BUILDS  ' NOTHING ,  DOES> @ >R ;
   0 VARIABLE MARKER
   : (MAKE)  R>  DUP 2+  DUP 2+  SWAP @  2+ DOES-PFA !
      @ -DUP IF >R THEN ;
   : MAKE  STATE @ IF ( compiling)
      COMPILE (MAKE)  HERE MARKER !  0 ,
      ELSE  HERE  [COMPILE] '  DOES-PFA !
      SMUDGE    [COMPILE] ] THEN ; IMMEDIATE
   : ;AND   COMPILE ;S  HERE MARKER @ ! ;  IMMEDIATE
   : UNDO   ' NOTHING  [COMPILE] '  DOES-PFA ! ;
   ;S
   The code in this screen is in the public domain.

.. code-block:: forth
   :lineno-start: 0

   ( DOER/MAKE   79-Standard  MVP FORTH             LPB 12/05/83 )
   : NOTHING ;
   : DOER   CREATE  ' NOTHING  ,  DOES> @ >R ;
   VARIABLE MARKER
   : (MAKE)  R>  DUP 2+  DUP 2+  SWAP @  2+ ( pfa) !
      @ ?DUP IF >R THEN ;
   : MAKE   STATE @ IF ( compiling)
      COMPILE (MAKE)  HERE MARKER !  0 ,
      ELSE  HERE  [COMPILE] ' !
       [COMPILE] ]  THEN ;   IMMEDIATE
   : ;AND   COMPILE EXIT  HERE MARKER @ ! ;   IMMEDIATE
   : UNDO   ['] NOTHING  [COMPILE] ' ! ;
   \
   ( The code in this screen is in the public domain.)

.. code-block:: forth
   :lineno-start: 0

   ( TODDLER: Example of DOER/MAKE                      12/01/83 )
   DOER ANSWER
   : RECITAL
     CR ." Your daddy is standing on the table.  Ask him 'WHY?' "
     MAKE ANSWER  ." To change the light bulb."
     BEGIN
     MAKE ANSWER  ." Because it's burned out."
     MAKE ANSWER  ." Because it was old."
     MAKE ANSWER  ." Because we put it in there a long time ago."
     MAKE ANSWER  ." Because it was dark!"
     MAKE ANSWER  ." Because it was night time!!"
     MAKE ANSWER  ." Stop saying WHY?"
     MAKE ANSWER  ." Because it's driving me crazy."
     MAKE ANSWER  ." Just let me change this light bulb!"
     FALSE UNTIL ;
   : WHY?   CR  ANSWER  QUIT ;
