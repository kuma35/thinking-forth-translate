
************************************
EPILOGUE: Forth’s Effect on Thinking
************************************

    Forth is like the Tao: it is a Way, and is realized when followed.
    Its fragility is its strength; its simplicity is its direction
    *(Michael Ham winning entry in Mountain View Press\'s contest to
    describe Forth in twenty-five words or less)*.

To help extract something of the Forth philosophy, I conducted a poll
among several Forth users in which I asked, “How has Forth affected your
thinking? Have you found yourself applying ‘Forth-like’ principles in
other areas?”

Here are some of the replies:

**Mark Bernstein** is president of Eastgate Systems Inc. @ in Cambridge, Massachusetts, and holds a doctorate from the department of chemistry at Harvard University.
    I first met Forth while working in laser chemistry.  I was trying to
    build a rather complicated controller for a new laser spectrometer.  The
    original plans called for a big green box full of electronics, The
    Interface.  Nobody had built this particular kind of instrument
    before---that\'s why we were doing it---and the list of things we wanted
    the computer to handle changed every couple of weeks.
    
    After a few months, I had hundreds of pages of assembly-language routines,
    three big circuit boards filled with ICs, and a 70-odd pin System Bus.
    Day by day, everything got more fragile and harder to fix.  The wiring on
    the circuit boards frayed, the connectors got loose, the assembler code
    grew ever more tangled.
    
    Forth was an obvious solution to the software problem, since it
    provided a decent environment in which to build and maintain a complex and
    rapidly-changing program.  But the essence of good Forth programming is
    the art of factoring procedures into useful, free-standing words.  The
    idea of the Forth word had unexpected implications for laboratory
    hardware design.
    
    Instead of building a big, monolithic, all-purpose Interface, I found
    myself building piles of simple little boxes which worked a lot like
    Forth words: they had a fixed set of standard inputs and standard
    outputs, they performed just one function, they were designed to connect
    up to each other without much effort, and they were simple enough that you
    could tell what a box did just by looking at its label.

    . The idea of "human scale" is, I think, today\'s seminal concept
    in software design.  This isn't specifically a Forth development; the
    great joy of UNIX, in its youth at least, was that you could read it
    (since it was written in C), understand it (since it was small), and
    modify it (since it was simple).  Forth shares these virtues, although
    it\'s designed to tackle a different sort of problem.
    
    Because Forth is small, and because Forth gives its users control
    over their machines, Forth lets humans control their applications.
    It\'s just silly to expect scientists to sit in front of a lab computer
    playing "twenty-questions" with packaged software. Forth, used
    properly, lets a scientist instruct the computer instead of letting the
    computer instruct the scientist.
    
    In the same sense that in baseball, a batter is supposed to feel the bat
    as an extension of himself, Forth is human-scaled, and helps convince
    you that the computer\'s achievements, and its failures, are also your own.

**Raymond E. Dessy** is Professor of Chemistry at Virginia Polytechnic Institute and State University, Blacksburg, Virginia.
    As I attempted to understand the nature and structure of the language C, I
    found myself drawing upon the knowledge I had of the organization and
    approach of Forth.  This permitted me to understand convoluted, or
    high-fog-coefficient sections describing C.
    
    I have found the Forth approach is an ideal platform upon which to build
    an understanding and an educational framework for other languages and
    operating system concepts.

**Jerry Boutelle** is owner of Nautilus Systems in Santa Cruz, California, which markets the Nautilus Cross-compiler.
    Forth has changed my thinking in many ways.  Since learning Forth
    I\'ve coded in other languages, including assembler, BASIC and FORTRAN.
    I\'ve found that I used the same kind of decomposition we do in
    Forth, in the sense of creating words and grouping them together.  For
    example, in handling strings I would define subroutines analogous to
    ``CMOVE``, ``-TRAILING``, ``FILL``, etc.
    
    More fundamentally, Forth has reaffirmed my faith in simplicity.  Most 
    people go out and attack problems with complicated tools.  But simpler
    tools are available and more useful.

    I try to simplify all the aspects of my life.  There's a quote I like
    from *Tao Te Ching*
    by the Chinese philosopher Lao Tzu: "To attain knowledge, add 
    things every day; to obtain wisdom, remove things every day."
