
**************************************************
Appendix D: Answers to "Further Thinking” Problems
**************************************************

Chapter Three
=============

1. The answer depends on whether you believe that other components will
   need to know the numeric code associated with each key. Usually this
   would *not* be the case. The simpler, more compact form is therefore
   preferable. Also in the first version, to add a new key would require
   a change in two places.

2. The problem with the words ``RAM-ALLOT`` and ``THERE`` are that they are
   *time-dependent* : we must execute them in a particular order. Our
   solution then will be to devise an interface to the RAM allocation
   pointer that is not dependent on order; the way to do this is to have
   a *single* word which does both functions transparently.

   Our word’s syntax will be

.. code-block:: none
   
   : RAM-ALLOT   ( #bytes-to-allot -- starting-adr) 
       ... ;

..

   This syntax will remain the same whether we define it to allocate
   growing upward:

.. code-block:: none
   
   : RAM-ALLOT  ( #bytes-to-allot -- starting-adr)
       >RAM @  DUP ROT +  >RAM ! ;

..

   or to allocate growing downward:

.. code-block:: none
   
   : RAM-ALLOT  ( #bytes-to-allot -- starting-adr)
       >RAM @  SWAP -  DUP >RAM ! ;

Chapter Four
============

Our solution is as follows:

.. code-block:: none
   :linenos:

   \ CARDS Shuffle                              6-20-83
   52 CONSTANT #CARDS
   CREATE DECK  #CARDS ALLOT   \ one card per byte
   : CARD ( i -- adr) DECK + ;
   : INIT-DECK  #CARDS 0 DO  I  I CARD  C!  LOOP ;
   INIT-DECK
   : 'CSWAP  ( a1 a2 -- )  \  swap bytes at a1 and a2
      2DUP C@  SWAP C@  ROT C!  SWAP C! ;
   : SHUFFLE   \  shuffle deck of cards
      #CARDS 0 DO  I CARD  #CARDS CHOOSE CARD  'CSWAP
      LOOP ;

Chapter Eight
=============

.. code-block:: none
   
   : DIRECTION  ( n|-n|0 -- 1|-1|0)  DUP  IF  0< 1 OR  THEN ;
