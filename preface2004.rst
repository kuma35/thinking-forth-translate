
***************************
Preface to the 2004 Edition
***************************

It is an honor to find myself writing a preface
again, twenty years after the original publication of *Thinking Forth.*
It is gratifying to know that the concepts covered here still have
validity after so many waves of technology have crested in popularity.
These are simply concepts of good programming, discovered and
rediscovered by countless working developers over the years, and given a
fresh twist by a genius named Chuck Moore.

I\’ve never claimed to be an expert on comparative language studies. My
recent career has centered more on requirements and functional design
than on development technologies. But with the honor of writing another
preface to this book comes another opportunity to express my opinion.

In the course of designing Internet applications I\’ve picked up a little
of Java and C#. Enough to recognize in them glimmers of the elegance
that Chuck has espoused all along, but burdened with baggage. I looked
into some of the recent books that describe design patterns, assuming
that they would address recurring code designs for real-world
phenomenon. Some of the patterns do, like the controller pattern, but
too many others, such as the factory pattern, address problems that are
created by the programming language itself.

In the 1994 Preface, I apologized that my dismissal of objected-oriented
programming in the 1984 edition was a little overreaching. What
motivated that apology was having worked for a time with an
object-oriented flavor of Forth developed by Gary
Friedlander for Digalog Corp. I discovered that the principles of
encapsulation could be applied elegantly to Forth “objects” that derived
from classes that each had their own implementation of common methods.
The objects “knew” information about themselves, which made code that
called them simpler. But these were still just Forth constructs, and the
syntax was still Forth. It wasn\’t Java written in Forth. There was no
need for garbage collection, etc. I won’t apologize now for my apology
then, but please know that I didn\’t mean to sell out in favor of
full-blown object oriented languages.

Some people have noted parallels between *Thinking Forth* and Extreme
Programming. For example, emphasis on iterative development,
incrementally enhancing code that “works,” and not over-solving the
problem, and so on.

But in my opinion, Extreme Programming seems to miss an important step
in the software development lifecycle: the design of the conceptual
model. With only one or two developers working on a project, this phase
doesn\’t need to be formalized because good developers do it intuitively.
But in the projects I\’ve been working on, involving five or more
developers, it\’s crucial.

I define the conceptual model as the representation of how the software
*appears* to work. The conceptual model is not just a restatement of the
requirements. It is the result of carefully analyzing the top-level
requirements and creatively addressing them in a design that will make
sense to the user. An example is the “shopping basket” construct in a
commerce application. The conceptual design forms the basis for a second
tier of requirements and drives use cases describing user/system
interactions. This second tier of requirements then drives the technical
design and implementation, or how the software actually works. The
conceptual model is designed collaboratively by the program manager,
developers, and business owners.

What I\’ve read about Extreme Programming seems to instead assume that
requirements directly drive the implementation. In my career, I\’ve
gravitated to the position of Program Manager, the champion of the
conceptual model. Most software developers I\’ve worked with appreciate
my attention to defining the conceptual model before committing to a
logical and technical design. But the irony in comparing *Thinking
Forth* with Extreme Programming is that some developers of the XP stripe
(and who of course have never heard of this book) don\’t even see the
value of a spec!

Ah well.

**May wisdom, fun, and the greater good shine forth in all your work.**

Leo Brodie


Acknowledgments for 2004 Edition
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Normally, this is where the author thanks those who helped with the
book. In this case, it\’s the other way around. This entire project was
conceived, executed and completed by an inspired group of people with no
prompting or significant assistance from me.

For that reason, I have asked them to describe their contributions in
their own words:

**John R. Hogerhuis**:
    I contacted Leo Brodie to discuss the conditions under which
    he would be willing to allow republishing **Thinking Forth**, in
    electronic format under an open content license. I acquired a copy of
    1984 edition, chopped off the binding, and scanned it in. Then I did a
    proof-of-concept and typeset the first chapter of the book in LyX.
    
    It turned out to be a lot more work than I had time to do by myself (I
    have more ambition than sense sometimes, as my wife will attest), so I
    solicited help from the Forth community
    on :newsgroup:`comp.lang.forth`. The
    outpouring of support, based on love for this book, was so tremendous
    that I discovered---after dividing up work and setting the ground
    rules (stick to 1984 version, use LaTeX for the typesetting
    language, and copyrights had to be assigned to Leo Brodie)---that the
    more I stayed out of the way and avoided being a
    bottleneck the faster the work came together.
    
    I also OCRed all the pages at some point using Transym OCR tool
    (others used the same tool in their transcription effort), and did the
    cleanup/vectorization pass on the images for Chapter Three.

**Bernd Paysan**:
    I set up the infrastructure. I got the project approved by
    Sourceforge, set up CVS, mailing lists and added developers. The
    approval despite the non-commercial license is due to Jacob Moorman,
    who knew this book ("despite limitations on use, I recommend
    approval; this is a unique and excellent resource on Forth").
    The actual approval was processed by David Burley.
    
    As the LaTeX guru, I created most of the style file, and cleaned up
    most of the submissions so that they work with the style file.
    
    I cleaned up pictures, restoring halftone and removing raster
    as necessary. I translated non-hand-drawn figures to
    LaTeX. [#f1]_ 

**Andrew Nicholson**:
    * item extracted, rotated and converting the scanned images from
      Chapter One, Chapter Two, Chapter Seven, Chapter Eight into PNGs
      and adding the images into the correct places.
    * transcribed Chapter Two from OCR to LaTeX
    * rebuilt the index from 1984
    * revised and cleaned up Chapter One and Chapter Five
    * cleaned up Chapter Six, Chapter Seven, Chapter Eight

**Nils Holm**:
    Transcription/initial typesetting of Chapter Four,
    Chapter Seven, and Chapter Eight

**Anton Ertl**:
    I LaTeXified (typeset) and did some cleanup of Chapter Three.
    
**Joseph Knapka**:
    Transcription of Chapter Three

**Josef Gabriel**:
    I transcribed Chapter Six. I see my contribution as helping to pass on
    Forth to other folks.  I hope folks will read
    *Thinking Forth* and have their code changed.

**Ed Beroset**:
    Typeset the epilog and appendices, and did some of the LaTeX coding.

**Albert van der Horst**:
    Transcribed/did initial typesetting for Chapter Five

**Steve Fisher**:
    Ran the OCR for Chapter Seven and Chapter Eight

To all of the above, I am deeply indebted and honored.

**Leo Brodie** Seattle, WA November 2004

.. rubric:: Footnotes
	 
.. [#f1] Note from John: I'll add that
   Bernd really took the ball and ran with it, employing the
   "Free Software" development model to impressive effect.  Of course, an
   important part of most Free Software projects is one dedicated super
   developer who blazes the trail and gets a large percentage of the work
   done.  Bernd is that guy.
