
***********
2. Analysis
***********

Anyone who tells you there is some definite number
of phases to the software development cycle is a fool.

Nevertheless …

The Nine Phases of the Programming Cycle
========================================

As we’ve seen, Forth integrates aspects of design with aspects of
implementation and maintenance. As a result, the notion of a “typical
development cycle” makes as much sense as a “typical noise.”

But any approach is better than no approach, and indeed, some approaches
have worked out better than others. Here is a development cycle that
represents an “average” of the most successful approaches used in
software projects:

Analysis

    #. Discover the Requirements and Constraints

    #. Build a Conceptual Model of the Solution

    #. Estimate Cost/Schedule/Performance

Engineering

    4. Preliminary Design

    5. Detailed Design

    6. Implementation

Usage

    7. Optimization

    8. Validation and Debugging

    9. Maintenance

In this book we’ll treat the first six stages of the cycle, focusing on
analysis, design, and implementation.

In a Forth project the phases occur on several levels. Looking at a
project from the widest perspective, each of these steps could take a
month or more. One step follows the next, like seasons.

But Forth programmers also apply these same phases toward defining each
word. The cycle then repeats on the order of
minutes.

Developing an application with this rapid repetition of the programming
cycle is known as using the “Iterative Approach.”

The Iterative Approach
======================

The iterative approach was explained eloquently by Kim Harris
[harris81]_ .  He begins by describing the scientific method:

    \.\.\. a never-ending cycle of discovery and refinement. It first
    studies a natural system and gathers observations about its behavior.
    Then the observations are modeled to produce a theory about the
    natural system. Next, analysis tools are applied to the model, which
    produces predictions about the real system's behavior. Experiments
    are devised to compare actual behavior to the predicted behavior. The
    natural system is again studied, and the model is revised.

    .. figure:: fig2-1.png
       :name: fig2-1
       :alt: The iterative approach to the software development cycle.

       The iterative approach to the software development cycle, from "The Forth Philosophy", by Kim Harris, **Dr. Dobb's Journal.**

    The **goal** of the method is to produce a model which accurately
    predicts all observable behavior of the natural system.

Harris then applies the scientific method to the software development
cycle, illustrated in :numref:`fig2-1` :

#. A problem is analyzed to determine what functions are required in the
   solution.

#. Decisions are made about how to achieve those functions with the
   available resources.

#. A program is written which attempts to implement the design.

#. The program is tested to determine if the functions were implemented
   correctly.

Mr. Harris adds:

    Software development in Forth seeks first to find the simplest
    solution to a given problem. This is done by implementing selected
    parts of the problem separately and by ignoring as many constraints as
    possible. Then one or a few constraints are imposed and the program is
    modified.

An excellent testimonial to the development/testing model of design is
evolution. From protozoa to tadpoles to people, each species along the
way has consisted of functional, living beings. The Creator does not
appear to be a top-down designer.

.. tip::

   Start simple. Get it running. Learn what you're trying to do. Add
   complexity gradually, as needed to fit the requirements and
   constraints. Don't be afraid to restart from scratch.

The Value of Planning
=====================

In the nine phases at the start of this chapter we listed five steps
*before* “implementation.” Yet in Chapter One we saw that an
overindulgence in planning is both difficult and pointless.

Clearly you can’t undertake a significant software project—regardless of
the language—without some degree of planning. Exactly what degree is
appropriate?

More than one Forth programmer has expressed high regard for **Dave Johnson** 's meticulous approach to planning. Johnson is supervisor at Moore Products Co. in Springhouse, Pennsylvania. The firm specializes in industrial instrumentation and process control applications. Dave has been using Forth since 1978. He describes his approach:
    Compared with many others that use Forth, I suppose we take a more
    formal approach. I learned this the hard way, though. My lack of
    discipline in the early years has come back to haunt me.
    
    We use two tools to come up with new products: a functional specification
    and a design specification. Our department of Sales & Applications comes
    up with the functional specification, through customer contact.
    
    Once we've agreed on what we're going to do, the functional
    specification is turned over to our department. At that point we work
    through a design, and come up with the design specification.
    
    Up to this point our approach is no different from programming in any
    language. But with Forth, we go about designing somewhat
    differently. With Forth you don't have to work 95% through your
    design before you can start coding, but rather 60% before you can get
    into the iterative process.
    
    A typical project would be to add a functional enhancement to one of
    our products. For example, we have an intelligent terminal with disk
    drives, and we need certain protocols for communicating with another
    device. The project to design the protocols, come up with displays,
    provide the operator interfaces, etc. may take several months. The
    functional specification takes a month; the design specification takes
    a month; coding takes three months; integration and testing take
    another month.
    
    This is the typical cycle. One project took almost two years, but six
    or seven months is reasonable.

    When we started with Forth five years ago, it wasn't like that. When I
    received a functional specification, I just started coding. I used a
    cross between top-down and bottom-up, generally defining a structure,
    and as I needed it, some of the lower level, and then returning with
    more structure.
    
    The reason for that approach was the tremendous pressure to show
    something to management. We wound up never writing down what we were
    doing. Three years later we would go back and try to modify the code,
    without any documentation. Forth became a disadvantage because it
    allowed us to go in too early. It was fun to make the lights flash and
    disk drives hum. But we didn't go through the nitty-gritty design
    work. As I said, our "free spirits" have come back to haunt us.
    
    Now for the new programmers, we have an established requirement: a
    thorough design spec that defines in detail all the high-level Forth
    words---the tasks that your project is going to do. No more reading a
    few pages of the functional specification, answering that, reading a
    few more, answering that, etc.
    
    No living programmer likes to document. By ensuring the design ahead
    of time, we're able to look back several years later and remember what
    we did.

    I should mention that during the design phase there is some amount of
    coding done to test out certain ideas. But this code may not be part
    of the finished product. The idea is to map out your design.

Johnson advises us to complete the design specification before starting
to code, with the exception of needed preliminary tests. The next
interview backs up this point, and adds some additional reasons.

**John Teleska** has been an independent software consultant since 1976, specializing in custom applications for academic research environments. He enjoys providing research tools "right at the edge of what technology is able to do." Teleska works in Rochester, New York:
    I see the software development process as having two phases. The first is
    making sure I understand what the problem is. The second is
    implementation, including debugging, verification, etc.
    
    My goal in Phase One is an operational specification. I start with a
    problem description, and as I proceed it becomes the operational
    specification. My understanding of the problem metamorphoses into a
    solution. The better the understanding, the more complete the
    solution. I look for closure; a sense of having no more questions that
    aren't answered in print.
    
    I've found that on each project I've been putting more time into Phase
    One, much to the initial dismay of many of my clients. The limiting
    factor is how
    much I can convince the client it's necessary to spend that time up
    front.  Customers generally don't know the specifications for the job
    they want done. And they don't have the capital---or don't feel they
    do---to spend on good specs. Part of my job is to convince them it
    will end up costing more time and money not to.
    
    Some of Phase One is spent on feasibility studies. Writing the spec
    unearths uncertainties. I try to be as uncertain about uncertainties
    as possible. For instance, they may want to collect 200,000 samples a
    second to a certain accuracy. I first need to find out if it's even
    possible with the equipment they've got. In this case I've got to test
    its feasibility by writing a patch of code.
    
    Another reason for the spec is to cover myself. In case the
    application performs to the spec but doesn't fully satisfy the
    customer, it's the customer's responsibility. If the customer wants
    more, we'll have to renegotiate. But I see it as the designer's
    responsibility to do whatever is necessary to generate an operational
    specification that will do the job to the customer's satisfaction.
    
    I think there are consultants who bow to client pressure and limit the
    time they spend on specs, for fear of losing the job. But in these
    situations nobody ends up happy.

We’ll return to the Teleska interview
momentarily.

The Limitations of Planning
===========================

Experience has taught us to map out where we’re going before we begin
coding. But planning has certain limitations. The following interviews
give different perspectives to the value of planning.

**Despite Teleska** 's preference for a well-planned project, he suggests that the choice between a top-down and bottom-up approach may depend on the situation:
    On two recent projects involving a lot of technical interface work, I
    did the whole thing bottom-up. I milled around in a bunch of
    data-sheets and technical descriptions of little crannies of the
    operating system I was dealing with. I felt lost most of the time,
    wondering why I ever took the job on.  Then finally I reached a
    critical mass of some sort and began putting small programs together
    that made small things happen. I continued, bottom-up, until I matched
    the target application.
    
    My top-down sense was appalled at this procedure. But I've seen me go
    through this process successfully too many times to discount it for
    any pedagogical reasons. And there is always this difficult phase
    which it seems no amount of linear thinking will penetrate.
    Programming seems a lot more intuitive than we, in this business, tell
    each other it ought to be.
    
    I think if the application elicits this sense of being lost, I proceed
    bottom-up. If the application is in familiar territory then I'll
    probably use a more traditional by-the-book approach.

And here’s another
view:

At the time I interviewed him, Michael Starling of Union
Carbide was putting the final touches on two applications involving
user-configurable laboratory automation and process control automation
systems. For the pilot plant system, Starling designed both the
hardware and software to known requirements; on the laboratory
automation system he also defined the requirements himself.

His efforts were extremely successful. On one project, the new system
typically costs only 20\% as much as the equivalent system and
requires days, instead of months, to install and configure.

    I asked him what techniques of project management he employed.

    On both of these projects much design was needed. I did not follow the
    traditional analysis methods, however. I did employ these steps:
    
    First, I clearly defined the boundaries of the problem.
    
    Second, I determined what the smaller functional pieces, the software
    subsystems, had to be.
    
    Third, I did each piece, put them together, and the system ran.
    
    Next, I asked the users "Does this meet your requirements?"
    Sometimes it didn't, and in ways that neither the users nor the
    specification designers could have anticipated.
    
    For instance, the designers didn't realize that the original
    specification wouldn't produce pleasing, human-oriented graphics
    displays. After working with the interactive graphics on the first
    version, users were applying arbitrary scales and coming up with
    oddball displays.
    
    So even after the basic plot algorithm was designed, we realized we
    needed auto-scaling. We went back in and analyzed how human beings
    plot data and wrote a first level plot function that evaluates the x
    and y data and how much will fit on the graph.
    
    After that, we realized that not all the data taken will be of
    interest to experimenters. So we added a zoom capability.
    
    This iterative approach resulted in cleaner code and better thought
    out code. We established a baseline set of goals and built a minimal
    system to the users' known requirements. Then we'd crank in the
    programmer's experience to improve it and determine what the users
    forgot they needed when they generated the specs.
    
    The users did not invent most of the new ideas. The programmers did,
    and they would bounce these ideas off the users. The problem
    definition was a two-way street. In some cases they got things they
    didn't know they could do on such a small computer, such as applying
    digital filters and signal processing to the data.
    
    One of the things about {Forth}{} that makes this approach possible is
    that primitives are easily testable. It takes some experience with
    Forth to learn how to take advantage of this. Guys from traditional
    environments want to write ten pages of code at their desk, then sit
    down to type it in and expect it to work.
    
    To summarize my approach: I try to find out from the users what they
    need, but at the same time recognizing its incompleteness. Then I keep
    them involved in the design during the implementation, since they have
    the expertise in the application.  When they see the result, they feel
    good because they know their ideas were involved.

    The iterative approach places highest value on producing a good
    solution to the real problem. It may not always give you the most
    predictable software costs. The route to a solution may depend upon
    your priorities. Remember:

    | Good
    | Fast
    | Cheap

    Pick any two!

As Starling observes, you don’t know completely
what you’re doing till you’ve done it once. In my own experience, the
best way to write an application is to write it twice. Throw away the
first version and chalk it up to
experience.

**Peter Kogge** is Senior Technical Staff in the IBM Federal Systems Division, Oswego, New York:
    One of the key advantages I find in Forth is that it allows me to
    very quickly prototype an application without all the bells and
    whistles, and often with significant limitations, but enough to wring
    out the "human interface" by hands-on trial runs.
    
    When I build such a prototype, I do so with the firm constraint that I
    will use not a single line of code from the prototype in the final
    program. This enforced "do-over" almost always results in far
    simpler and more elegant final programs, even when those programs are
    written in something other than Forth.

Our conclusions? In the Forth environment planning
is necessary. But it should be kept short. Testing and prototyping are
the best ways to discover what is really needed.

A word of caution to project managers: If you’re supervising any
experienced Forth programmers, you won’t have to worry about them
spending too much time on planning. Thus the following tip has two
versions:

.. tip::
   
   For newcomers to Forth (with "traditional" backgrounds):
   Keep the analysis phase to a minimum.
   
   For Forth addicts (without a "traditional" background):
   Hold off on coding as long as you can possibly stand it.

Or, as we observed in Chapter One:

.. tip::

   Plan for change (by designing components that can be changed).

Or, simply:

.. tip::
   
   Prototype.

The Analysis Phase
==================

In the remainder of this chapter we’ll
discuss the analysis phase. Analysis is an organized way of
understanding and documenting what the program should
do.

With a simple program that you write for yourself in less than an hour,
the analysis phase may take about 250 microseconds. At the other
extreme, some projects will take many man-years to build. On such a
project, the analysis phase is critical to the success of the entire
project.

We’ve indicated three parts to the analysis phase:

#. Discovering the requirements and constraints

#. Building a conceptual model of the solution

#. Estimating cost, scheduling, and performance

Let’s briefly describe each part:

Discovering the Requirements
----------------------------

The first step is to determine what the application should do. The
customer, or whoever wants the system, should supply a “requirements
specification.” This is a modest document that lists the minimum
capabilities for the finished product.

The analyst may also probe further by conducting interviews and sending
out questionnaires to the
users.

Discovering the Constraints
---------------------------

The next step is to discover any limiting factors. How important is
speed? How much memory is available? How soon do you need it?

No matter how sophisticated our technology becomes, programmers will
always be bucking limitations. System capacities inexplicably diminish
over time. The double-density disk drives that once were the answer to
my storage prayers no longer fill the bill. The double-sided,
double-density drives I’ll get next will seem like a vast frontier—for a
while. I’ve heard guys with 10-megabyte hard disks complain of feeling
cramped.

Whenever there’s a shortage of something—and there always will
be—tradeoffs have to be made. It’s best to use the analysis phase to
anticipate most limitations and decide which tradeoffs to make.

On the other hand, you should *not* consider other types of constraints
during analysis, but should instead impose them gradually during
implementation, the way one stirs flour into gravy.

The type of constraint to consider during analysis includes those that
might affect the overall approach. The type to defer includes those that
can be handled by making iterative refinements to the planned software
design.

As we heard in our earlier interviews, finding out about *hardware*
constraints often requires
writing some test code and trying things out.

Finding out about the *customer’s*
constraints is usually a
matter of asking the customer, or of taking written surveys. “How fast
do you need such-and-such, on a scale of one to ten?”,
etc.

Building a Conceptual Model of the Solution
-------------------------------------------

A conceptual model is an imaginary solution to the problem. It is a view
of how the system *appears* to work. It is an answer to all the
requirements and
constraints.

.. figure:: img2-047.png
   :name: img2-047
   :alt: Refining the conceptual model to meet requirements and constraints.

   Refining the conceptual model to meet requirements and constraints.

..

If the requirements definition is for “something to stand on to paint
the ceiling,” then a description of the conceptual model is “a device
that is free-standing (so you can paint the center of the room), with
several steps spaced at convenient intervals (so you can climb up and
down), and having a small shelf near the top (to hold your paint can).”

A conceptual model is not quite a design, however. A design begins to
describe how the system *really* works. In design, the image of a step
ladder would begin to emerge.

Forth blurs the distinction a little, because all definitions are
written in conceptual terms, using the lexicons of lower level
components. In fact, later in this chapter we’ll use Forth “pseudocode”
to describe conceptual model solutions.

Nevertheless, it’s useful to make the distinction. A conceptual model is
more flexible than a design. It’s easier to fit the requirements and
constraints into the model than into a design.

.. tip::

   Strive to build a solid conceptual model before beginning the design.

Analysis consists of expanding the requirements
definition into a conceptual model. The technique involves two-way
communication with the customer in successive attempts to describe the
model.

Like the entire development cycle, the analysis phase is best approached
iteratively. Each new requirement will tend to suggest something in your
mental model. Your job is to juggle all the requirements and constraints
until you can weave a pattern that fits the bill.

.. figure:: fig2-2.png
   :name: fig2-2
   :alt: An iterative approach to analysis.

   An iterative approach to analysis.

..

:numref:`fig2-2`  illustrates the iterative approach to the
analysis phase. The final step is one of the most important: show the
documented model to the customer. Use whatever means of communication
are necessary—diagrams, tables, or cartoons—to convey your understanding
to the customer and get the needed feedback. Even if you cycle through
this loop a hundred times, it’s worth the effort.

In the next three sections we’ll explore three techniques for defining
and documenting the conceptual model:

#. defining the interfaces

#. defining the rules

#. defining the data structures.

Defining the Interfaces
=======================

.. tip::

   First, and most importantly, the conceptual model should describe the
   system's interfaces.

Teleska:
    The "spec" basically deals with WHAT. In its most glorious form, it
    describes what the system would look like to the user---you might call it
    the user's manual. I find I write more notes on the human
    interaction---what it
    will look like on the outside---than on the part that gets the job
    done. For instance, I'll include a whole error-action listing to show
    what happens when a particular error occurs. Oddly, this is the part
    that takes the most time to implement anyway.
    
    I'm currently working on a solid-state industrial washing-machine
    timer. In this case, the user interface is not that complex. What is
    complex is the interface to the washing machine, for which I must
    depend on the customer and the documentation they can provide.
    
    The significant interface is whatever is the arms and legs of the
    product. I don't make the distinction between hardware and software at
    this early stage. They can be interchanged in the implementation.

    The process of designing hardware and the process of designing
    software are analogous. The way I design hardware is to treat it as a
    black box. The front panel is input and output. You can do the same
    with software.
    
    I use any techniques, diagrams, etc., to show the customer what the
    inputs and outputs look like, using his description of what the
    product has to do.  But in parallel, in my own mind, I'm imagining how
    it will be implemented.  I'm evaluating whether I can do this
    efficiently. So to me it's not a black box, it's a gray box. The
    designer must be able to see inside the black boxes.

    When I design a system that's got different modules, I try to make the
    coupling as rational and as little as possible. But there's always
    give and take, since you're compromising the ideal.
    
    For the document itself, I use DFDs [data-flow diagrams, which we'll
    discuss later], and any other kind of representation that I can show
    to my client. I show them as many diagrams as I can to clarify my
    understanding.  I don't generally use these once it comes to
    implementation. The prose must be complete, even without reference to
    the diagrams.

.. tip::

   Decide on error- and exception-handling early as part of defining the
   interface.

It’s true that when coding for oneself, a
programmer can often concentrate first on making the code run correctly
under *normal* conditions, then worry about error-handling later. When
working for someone else, however, error-handling should be worked out
ahead of time. This is an area often overlooked by the beginning
programmer.

The reason it’s so important to decide on error-handling at this stage
is the wide divergence in how errors can be treated. An error might be:

-  ignored

-  made to set a flag indicating that an error occurred, while
   processing continues

-  made to halt the application immediately

-  designed to initiate procedures to correct the problem and keep the
   program running.

There’s room for a serious communications gap if the degree of
complexity required in the error-handling is not nailed down early.
Obviously, the choice bears tremendous impact on the design and
implementation of the
application.

.. tip::

   Develop the conceptual model by imagining the data traveling through and
   being acted upon by the parts of the model.

A discipline called
*structured analysis* [weinberg80]_ offers some
techniques for describing interfaces in ways that your clients will
easily understand. One of these techniques is called the “data-flow
diagram” (DFD), which Teleska mentioned.

.. figure:: fig2-3.png
   :name: fig2-3
   :alt: A data-flow diagram.

   A data-flow diagram.

..

A data-flow diagram, such as the one depicted in
:numref:`fig2-3` , emphasizes what happens to items of data as
they travel through the system. The circles represent “transforms,”
functions that act upon information. The arrows represent the inputs and
outputs of the transforms.

The diagram depicts a frozen moment of the system in action. It ignores
initialization, looping structures, and other details of programming
that relate to time.

Three benefits are claimed for using DFDs:

First, they speak in simple, direct terms to the customer. If your
customer agrees with the contents of your data-flow diagram, you know
you understand the problem.

Second, they let you think in terms of the logical “whats,” without
getting caught up in the procedural “hows,” which is consistent with the
philosophy of hiding information as we discussed in the last chapter.

Third, they focus your attention on the interfaces to the system and
between modules.

Forth programmers, however, rarely use DFDs except for the customer’s
benefit. Forth encourages you to think in terms of the conceptual model,
and Forth’s implicit use of a data stack makes the passing of data among
modules so simple it can usually be taken for granted. This is because
Forth, used properly, approaches a functional
language.

For anyone with a few days’ familiarity with Forth, simple definitions
convey at least as much meaning as the diagrams:

.. code-block:: none
   
   : REQUEST  ( quantity part# -- )
      ON-HAND?  IF  TRANSFER  ELSE  REORDER  THEN ;
   : REORDER   AUTHORIZATION?  IF  P.O.  THEN ;
   : P.O.   BOOKKEEPING COPY   RECEIVING COPY
      VENDOR MAIL-COPY ;

..

This is Forth pseudocode. No effort has been made to determine what
values are actually passed on the stack, because that is an
implementation detail. The stack comment for ``REQUEST`` is used only to
indicate the two items of data needed to initiate the process.

(If I were designing this application, I’d suggest that the user
interface be a word called ``NEED``, which has this syntax:

.. code-block:: none
   
   NEED 50 AXLES

..

``NEED`` converts the quantity into a numeric value on
the stack, translates the string ``AXLES`` into a part number, also on the
stack, then calls ``REQUEST``. Such a command should be defined only at the
outer-most level.)

Johnson of Moore Products Co. has a few words on Forth pseudocode:
    IBM uses a rigorously documented PDL (program design language). We use
    a PDL here as well, although we call it FDL, for Forth design
    language. It's probably worthwhile having all those standards, but
    once you're familiar with Forth, Forth itself can be a design
    language. You just have to leave out the so-called "noise" words:
    ``C@``, ``DUP``, ``OVER``, etc., and show only the basic
    flow. Most Forth people probably do that informally. We do it
    purposefully.

During one of our interviews I asked Moore if he used diagrams of any sort to plan out the conceptual model, or did he code straight into Forth? His reply:
    The conceptual model **is** Forth. Over the years I've learned
    to think that way.

    **Can everyone learn to think that way?**

    I've got an unfair advantage. I codified my programming style and other
    people have adopted it. I was surprised that this happened. And I feel at a
    lovely advantage because it is my style that others are learning to emulate.
    Can they learn to think like I think? I imagine so. It's just a matter of
    practice, and I've had more practice.

Defining the Rules
==================

Most of your efforts at defining a problem will center on describing the
interface. Some applications
will also require that you define the set of application rules.

All programming involves rules. Usually these rules are so simple it
hardly matters how you express them: “If someone pushes the button, ring
the bell.”

Some applications, however, involve rules so complicated that they can’t
be expressed in a few sentences of English. A few formal techniques can
come in handy to help you understand and document these more complicated
rules.

Here’s an example. Our requirements call for a system to compute the
charges on long-distance phone calls. Here’s the customer’s explanation
of its rate structure. (I made this up; I have no idea how the phone
company actually computes their rates except that they overcharge.)

    All charges are computed by the minute, according to distance in
    hundreds of miles, plus a flat charge. The flat charge for direct dial
    calls during weekdays between 8 A.M. and 5 P.M. is .30 for the first
    minute, and .20 for each additional minute; in addition, each minute
    is charged .12 per 100 miles. The flat charge for direct calls during
    weekdays between 5 P.M. and 11 P.M. is .22 for the first minute, and
    .15 for each additional minute; the distance rate per minute is .10
    per 100 miles. The flat charge for direct calls late during weekdays
    between 11 P.M. or anytime on Saturday, Sundays, or holidays is .12
    for the first minute, and .09 for each additional minute; the distance
    rate per minute is .06 per 100 miles. If the call requires assistance
    from the operator, the flat charge increases by .90, regardless of the hour.

This description is written in plain old English, and it’s quite a
mouthful. It’s hard to follow and, like an attic cluttered with
accumulated belongings, it may even hide a few bugs.

In building a conceptual model for this system, we must describe the
rate structure in an unambiguous, useful way. The first step towards
cleaning up the clutter involves factoring out irrelevant pieces of
information—that is, applying the rules of limited redundancy. We can
improve this statement a lot by splitting it into two statements. First
there’s the time-of-day
rule:

    Calls during weekdays between 8 A.M. and 5 P.M. are charged at "full" rate.
    Calls during weekdays between 5 P.M. and 11 P.M. are charged at "lower"
    rate. Calls placed during weekdays between 11 P.M. or anytime on Saturday,
    Sundays, or holidays are charged at the "lowest" rate.

Then there’s the rate structure itself, which should be described in
terms of “first-minute rate,” “additional minute rate,” “distance rate,”
and “operator-assistance rate.”

.. tip::

   Factor the fruit. (Don't confuse apples with oranges.)

These prose statements are still difficult to read, however. System
analysts use several techniques to simplify these statements: structured
English, decision trees, and decision tables. Let’s study each of these
techniques and evaluate their usefulness in the Forth environment.

Structured English
------------------

Structured English is a sort of structured pseudocode in which our rate
statement would read something like this:

.. code-block:: none
   
   IF full rate
      IF direct-dial
         IF first-minute
        .30 + .12/100miles
         ELSE ( add'l- minute)
        .20 + .12/100miles
         ENDIF
      ELSE ( operator )
         IF first-minute
        1.20 + .12/100miles
         ELSE ( add'l- minute)
        .20 + .12/100miles
         ENDIF
      ENDIF
   ELSE  ( not-full-rate)
      IF lower-rate
         IF direct-dial
        IF first-minute
           .22 + .10/100miles
        ELSE ( add'l- minute)
           .15 + .10/100miles
        END IF
         ELSE ( operator)
        IF first-minute
           1.12 + .10/100miles
        ELSE ( add'l- minute)
           .15 + .10/100miles
        ENDIF
         ENDIF
      ELSE ( lowest-rate)
         IF direct-dial
        IF first-minute
           .12 + .06/100miles
        ELSE ( add'l- minute)
           .09 + .O6/100miles
        ENDIF
         ELSE ( operator)
        IF first-minute
           1.02 + .O6/100miles
        ELSE ( add'l- minute)
           .09 + .06/100miles
        ENDIF
         ENDIF
      ENDIF
   ENDIF

..

This is just plain awkward. It’s hard to read, harder to maintain, and
hardest to write. And for all that, it’s worthless at implementation
time. I don’t even want to talk about it
anymore.

The Decision Tree
-----------------

.. figure:: fig2-4.png
   :name: fig2-4
   :alt: Example of a decision tree.

   Example of a decision tree.

..

:numref:`fig2-4`  illustrates the telephone rate rules by means
of a decision tree. The decision tree is the easiest method of any to
“follow down” to determine the result of certain conditions. For this
reason, it may be the best representation to show the customer.

Unfortunately, the decision tree is difficult to “follow up,” to
determine which conditions produce certain results. This difficulty
inhibits seeing ways to simplify the problem. The tree obscures the fact
that additional minutes cost the same, whether the operator assists or
not. You can’t see the facts for the
tree.

The Decision Table
------------------

The decision table, described next, provides the most usable graphic
representation of compound rules for the programmer, and possibly for
the customer as well.  :numref:`fig2-5`  shows our rate structure
rules in decision-table form.

.. figure:: fig2-5.png
   :name: fig2-5
   :alt: The decision table.

   The decision table.

..

In :numref:`fig2-5`  there are three dimensions: the rate
discount, whether an operator intervenes, and initial minute vs.
additional minute.

Drawing problems with more than two dimensions gets a little tricky. As
you can see, these additional dimensions can be depicted on paper as
subdimensions within an outer dimension. All of the subdimension’s
conditions appear within every condition of the outer dimension. In
software, any number of dimensions can be easily handled, as we’ll see.

All the techniques we’ve described force you to analyze which conditions
apply to which dimensions. In factoring these dimensions, two rules
apply:

First, all the elements of each dimension must be mutually exclusive.
You don’t put “first minute” in the same dimension as “direct dial,”
because they are not mutually exclusive.

Second, all possibilities must be accounted for within each dimension.
If there were another rate for calls made between 2 A.M. to 2:05 A.M.,
the table would have to be enlarged.

But our decision tables have other advantages all to themselves. The
decision table not only reads well to the client but actually benefits
the implementor in several ways:

Transferability to actual code.
    This is particularly true in Forth, where decision tables are easy
    to implement in a form very similar to the drawing.

Ability to trace the logic upwards.
    Find a condition and see what factors produced it.

Clearer graphic representation.
    Decision tables serve as a better tool for understanding, both for
    the implementor and the analyst.

Unlike decision trees, these decision tables group the *results*
together in a graphically meaningful way. Visualization of ideas helps
in understanding problems, particularly those problems that are too
complex to perceive in a linear way.

For instance, :numref:`fig2-5`  clearly shows that the charge
for additional minutes does not depend on whether an operator assisted
or not. With this new understanding we can draw a simplified table, as
shown in :numref:`fig2-6` .

.. figure:: fig2-6.png
   :name: fig2-6
   :alt: A simplified decision table.

   A simplified decision table.

..

It’s easy to get so enamored of one’s analytic tools that one forgets
about the problem. The analyst must do more than carry out all
possibilities of a problem to the nth degree, as I have seen authors of
books on structured analysis recommend. That approach only increases the
amount of available detail. The problem solver must also try to simplify
the problem.

.. tip::

   You don't understand a problem until you can simplify it.

If the goal of analysis is not only understanding,
but simplification, then perhaps we’ve got more work to do.

Our revised decision table ( :numref:`fig2-6` ) shows that the
per-mile charge depends only on whether the rate is full, lower, or
lowest. In other words, it’s subject to only one of the three dimensions
shown in the table. What happens if we split this table into two tables,
as in :numref:`fig2-7` ?

.. figure:: fig2-7.png
   :name: fig2-7
   :alt: The sectional decision table.

   The sectional decision table.

..

Now we’re getting the answer through a combination of table look-up and
calculation. The formula for the per-minute charge can be expressed as a
pseudoForth definition:

.. code-block:: none
   
   : PER-MINUTE-CHARGE ( -- per-minute-charge)
           CONNECT-CHARGE  MILEAGE-CHARGE  + ;

..

The “+” now appears once in the definition, not nine times in the table.

Taking the principle of calculation one step further, we note (or
remember from the original problem statement) that operator assistance
merely adds a one-time charge of .90 to the total charge. In this sense,
the operator charge is not a function of any of the three dimensions.
It’s more appropriately expressed as a “logical calculation”; that is, a
function that combines logic with arithmetic:

.. code-block:: none
   
   : ?ASSISTANCE
      ( direct-dial-charge -- total-charge)
      OPERATOR? IF .90 + THEN ;

..

(But remember, this charge applies only to the first minute.)

.. figure:: fig2-8.png
   :name: fig2-8
   :alt: The decision table without operator involvement depicted.

   The decision table without operator involvement depicted.

..

This leaves us with the simplified table shown in
:numref:`fig2-8` , and an increased reliance on expressing
calculations. Now we’re getting somewhere.

Let’s go back to our definition of PER-MINUTE-CHARGE:

.. code-block:: none
   
   : PER-MINUTE-CHARGE ( -- per-minute-charge)
      CONNECT-CHARGE  MILEAGE-CHARGE  + ;

..

Let’s get more specific about the rules for the connection charge and
for the mileage charge.

The connection charge depends on whether the minute is the first or an
additional minute. Since there are two kinds of per-minute charges,
perhaps it will be easiest to rewrite PER-MINUTE-CHARGE as two different
words.

Let’s assume we will build a component that will fetch the appropriate
rates from the table. The word 1MINUTE will get the rate for the first
minute; +MINUTES will get the rate for each additional minute. Both of
these words will depend on the time of day to determine whether to use
the full, lower, or lowest rates.

Now we can define the pair of words to replace PER-MINUTE-CHARGE:

.. code-block:: none
   
   : FIRST  ( -- charge)
     1MINUTE  ?ASSISTANCE   MILEAGE-CHARGE + ;
   : PER-ADDITIONAL  ( -- charge)
      +MINUTES  MILEAGE-CHARGE + ;

..

What is the rule for the mileage charge? Very simple. It is the rate
(per hundred miles) times the number of miles (in hundreds). Let’s
assume we can define the word MILEAGE-RATE, which will fetch the mileage
rate from the table:

.. code-block:: none
   
   : MILEAGE-CHARGE  ( -- charge)
      #MILES @  MILEAGE-RATE * ;

..

Finally, if we know the total number of minutes for a call, we can now
calculate the total direct-dial charge:

.. code-block:: none
   
   : TOTAL   ( -- total-charge)
      FIRST                        ( first minute rate)
      ( #minutes) 1-               ( additional minutes)
         PER-ADDITIONAL *          ( times the rate)
      +  ;                         ( added together)

..

We’ve expressed the rules to this particular problem through a
combination of simple tables and logical calculations.

(Some final notes on this example: We’ve written something very close to
a running Forth application. But it is only pseudocode. We’ve avoided
stack manipulations by assuming that values will somehow be on the stack
where the comments indicate. Also, we’ve used hyphenated names because
they might be more readable for the customer. Short names are preferred
in real code—see Chapter Five.)

We’ll unveil the finished code for this example in Chapter
Eight.

Defining the Data Structures
============================

After defining the interfaces, and sometimes defining the rules,
occasionally you’ll need to define certain data structures as well.
We’re not referring here to the implementation of the data structures,
but rather to a description of their conceptual model.

If you’re automating a library index, for instance, a crucial portion of
your analysis will concern developing the logical data structure. You’ll
have to decide what information will be kept for each book: title,
author, subject, etc. These “attributes” will comprise an “entity” (set
of related records) called BOOKS. Then you’ll have to determine what
other data structures will be required to let the users search the BOOKS
efficiently.

.. figure:: img2-060.png
   :name: img2-060
   :alt: Given two adequate solutions, the correct one is the simpler.

   Given two adequate solutions, the correct one is the simpler.

..

Certain constraints will also affect the conceptual model of the data
structure. In the library index example, you need to know not only
*what* information the users need, but also how long they’re willing to
*wait* to get it.

For instance, users can request listings of topics by year of
publication—say everything on ladies’ lingerie between 1900 and 1910. If
they expect to get this information in the snap of a girdle, you’ll have
to index on years and on topics. If they can wait a day, you might just
let the computer search through all the books in the
library.

Achieving Simplicity
====================

.. tip::

   Keep it simple.

While you are taking these crucial first steps
toward understanding the problem, keep in mind the old saying:

    Given two solutions to a problem, the correct one is the simpler.

This is especially true in software design. The simpler solution is
often more difficult to discover, but once found, it is:

-  easier to understand

-  easier to implement

-  easier to verify and debug

-  easier to maintain

-  more compact

-  more efficient

-  more fun

One of the most compelling advocates of simplicity is Moore:
    You need a feeling for the size of the problem. How much code should
    it take to implement the thing? One block? Three? I think this is a
    very useful design tool. You want to gut-feel whether it's a trivial
    problem or a major problem, how much time and effort you should spend
    on it.
    
    When you're done, look back and say, "Did I come up with a solution
    that is reasonable?" If your solution fills six screens, it may seem
    you've used a sledgehammer to kill a mosquito. Your mental image is
    out of proportion to the significance of the problem.
    
    I've seen nuclear physics programs with hundreds of thousands of lines
    of FORTRAN. Whatever that code does, it doesn't warrant hundreds of
    thousands of lines of code. Probably its writers have overgeneralized
    the problem. They've solved a large problem of which their real needs
    are a subset. They have violated the principle that the solution
    should match the problem.

.. tip::

   Generality usually involves complexity. Don't generalize your solution any
   more than will be required; instead, keep it changeable.

Moore continues:
    Given a problem, you can code a solution to it. Having done that, and found
    certain unpleasantnesses to it, you can go back and change the problem,
    and end up with a simpler solution.
    
    There's a class of device optimization---minimizing the number of gates in a
    circuit-where you take advantage of the "don\'t care" situation. These
    occur either because a case won't arise in practice or because you really
    don't care. But the spec is often written by people who have no appreciation
    for programming. The designer may have carefully specified all the cases,
    but hasn't told you, the programmer, which cases are really important.

    If you are free to go back and argue with him and take advantage of the
    "don\'t cares", you can come up with a simpler solution.
    
    Take an engineering application, such as a 75-ton metal powder press,
    stamping out things. They want to install a computer to control the
    valves in place of the hydraulic control previously used. What kind of
    spec will you get from the engineer? Most likely the sensors were
    placed for convenience from an electromechanical standpoint. Now they
    could be put somewhere else, but the engineer has forgotten. If you
    demand explanations, you can come closer to the real world and further
    from their model of the world.
    
    Another example is the PID (proportional integration and
    differentiation) algorithm for servos. You have one term that
    integrates, another term that differentiates, and a third term that
    smooths. You combine those with 30% integration, 10%
    differentiation, or whatever. But it's only a digital filter. It used
    to be convenient in analog days to break out certain terms of the
    digital filter and say, "This is the integrator and this is the
    differentiator. I\'ll make this with a capacitor and I\'ll make that
    with an inductor".
    
    Again the spec writers will model the analog solution which was
    modeling the electromechanical solution, and they're several models
    away from reality. In fact, you can replace it all with two or three
    coefficients in a digital filter for a much cleaner, simpler and more
    efficient solution.

.. tip::

   Go back to what the problem was before the customer tried to solve it.
   Exploit the "don\'t cares".

.. figure:: img2-063.png
   :name: img2-063
   :alt: An overgeneralized solution.

   An overgeneralized solution.

..

Moore continues:
    Sometimes the possibilities for simplification aren't immediately
    obvious.
    
    There's this problem of zooming in a digitized graphics display, such
    as CAD systems. You have a picture on the screen and you want to zoom
    in on a portion to see the details.
    
    I used to implement it so that you move the cursor to the position of
    interest, then press a button, and it zooms until you have a window of
    the desired size. That was the way I've always done it. Until I
    realized that that was stupid. I never needed to zoom with such fine
    resolution.
    
    So instead of moving the cursor a pixel at a time, I jump the cursor
    by units of, say, ten. And instead of increasing the size of box, I
    jump the size of the box. You don't have a choice of sizes. You zoom
    by a factor of four. The in-between sizes are not interesting. You can
    do it as many times as you like.
    
    By quantizing things fairly brutally, you make it easier to work with,
    more responsive, and simpler.

.. tip::

   To simplify, quantize.

Moore concludes:
    It takes arrogance to go back and say "You didn\'t really mean this",
    or "Would you mind if I took off this page and replaced it with this
    expression?" They get annoyed. They want you to do what they told you
    to do.
    
    LaFarr Stuart took this attitude when he redesigned Forth
    [stuart80]_ . He didn't like the input buffer, so he implemented
    Forth without it, and discovered he didn't really need an input buffer.
    
    If you can improve the problem, it's a great situation to get into.
    It's much more fun redesigning the world than implementing it.

Effective programmers learn to be tactful and to
couch their approaches in non-threatening ways: “What would be the
consequences of replacing that with this?” etc.

Yet another way to simplify a problem is this:

.. tip::

   To simplify, keep the user out of trouble.

Suppose you’re designing part of a word processor
that displays a directory of stored documents on the screen, one per
line. You plan that the user can move the cursor next to the name of any
document, then type a one-letter command indicating the chosen action:
“p” for print, “e” for edit, etc.

Initially it seems all right to let the user move the cursor anywhere on
the screen. This means that those places where text already appears must
be protected from being overwritten. This implies a concept of
“protected fields” and special handling. A simpler approach confines the
cursor to certain fields, possibly using reverse video to let the user
see the size of the allowable field.

Another example occurs when an application prompts the user for a
numeric value. You often see such applications that don’t check input
until you press “return,” at which time the system responds with an
error message such as “invalid number.” It’s just as easy—probably
easier—to check each key as it’s typed and simply not allow non-numeric
characters to appear.

.. tip::

   To simplify, take advantage of what's available.

Michael LaManna, a Forth programmer in Long Island, New York, comments:
    I always try to design the application on the most powerful processor
    I can get my hands on. If I have a choice between doing development on
    a 68000-based system and a 6809-based system, I'd go for the
    68000-based system. The processor itself is so powerful it takes care
    of a lot of details I might otherwise have to solve myself.
    
    If I have to go back later and rewrite parts of the application for a
    simpler processor, that's okay. At least I won't have wasted my time.

A word of caution: If you’re using an existing
component to simplify your prototype, don’t let the component affect
your design. You don’t want the design to depend on the internals of the
component.

Budgeting and Scheduling
========================

Another important aspect of the analysis phase is figuring the price
tag. Again, this process is much more difficult than it would seem. If
you don’t know the problem till you solve it, how can you possibly know
how long it will take to solve it?

Careful planning is essential, because things always take longer than
you expect. I have a theory about this, based on the laws of
probability:

.. figure:: img2-066.png
   :name: img2-066
   :alt: Conventional wisdom reveres complexity.

   Conventional wisdom reveres complexity.

..

.. tip::

   The mean time for making a "two-hour" addition to an application is
   approximately 12 hours.

Imagine the following scenario: You’re in the
middle of writing a large application when suddenly it strikes you to
add some relatively simple feature. You think it’ll take about two
hours, so without further planning, you just do it. Consider: That’s two
hours coding time. The design time you don’t count because you perceived
the need—and the design—in a flash of brilliance while working on the
application. So you estimate two hours.

But consider the following possibilities:

1. Your implementation has a bug. After two hours it doesn’t work. So
   you spend another two hours recoding. (Total 4.)

2. OR, before you implemented it, you realized your initial design
   wouldn’t work. You spend two hours redesigning. *These* two hours
   count. Plus another two hours coding it. (Total 4.)

3. OR, you implement the first design before you realize the design
   wouldn’t work. So you redesign (two more hours) and reimplement (two
   more). (Total 6.)

4. OR, you implement the first design, code it, find a bug, rewrite the
   code, find a design flaw, redesign, recode, find a bug in the new
   code, recode again. (Total 10.)

You see how the thing snowballs?

5. Now you have to document your new feature. Add two hours to the
   above. (Total 12.)

6. After you’ve spent anywhere from 2 to 12 hours installing and
   debugging your new feature, you suddenly find that element Y of your
   application bombs out. Worst yet, you have no idea why. You spend two
   hours reading memory dumps trying to divine the reason. Once you do,
   you spend as many as 12 additional hours redesigning element Y.
   (Total 26.) Then you have to document the syntax change you made to
   element Y. (Total 27.)

That’s a total of over three man-days. If all
these mishaps befell you at once, you’d call for the men with the little
white coats. It rarely gets that bad, of course, but the odds are
decidedly *against* any project being as easy as you think it will be.

How can you improve your chances of judging time requirements correctly?
Many fine books have been written on this topic, notably *The Mythical
Man-Month* by Frederick P. Brooks, Jr.
[brooks75]_ .
I have little to add to this body of knowledge except for some personal
observations.

#. Don’t guess on a total. Break the problem up into the smallest
   possible pieces, then estimate the time for each piece. The sum of
   the pieces is always greater than what you’d have guessed the total
   would be. (The whole appears to be less than the sum of the parts.)

#. In itemizing the pieces, separate those you understand well enough to
   hazard a guess from those you don’t. For the second category, give
   the customer a range.

#. A bit of psychology: always give your client some options. Clients
   *like* options. If you say, “This will cost you $6,000,” the client
   will probably respond “I’d really like to spend $4,000.” This puts
   you in the position of either accepting or going without a job.

   But if you say, “You have a choice: for $4,000 I’ll make it *walk*
   through the hoop; for $6,000 I’ll make it *jump* through the hoop.
   For $8,000 I’ll make it *dance* through the hoop waving flags,
   tossing confetti and singing “Roll Out the Barrel.”

   Most customers opt for jumping through the hoop.

.. tip::

   Everything takes longer than you think, including thinking.

Reviewing the Conceptual Model
==============================

The final box on our iterative analytic wheel is labeled “Show Model to
Customer.” With the tools we’ve outlined in this chapter, this job
should be easy to do.

In documenting the requirements specification, remember that specs are
like snowmen. They may be frozen now, but they shift, slip, and melt
away when the heat is on. Whether you choose data-flow diagrams or
straight Forth pseudocode, prepare yourself for the great thaw by
remembering to apply the concepts of limited redundancy.

Show the documented conceptual model to the customer. When the customer
is finally satisfied, you’re ready for the next big step: the
design!

REFERNCES
=========

.. [harris81]  Kim Harris, "The Forth Philosophy,"   **Dr. Dobb's Journal,**  Vol. 6, Iss. 9, No. 59 (Sept. 81),  pp. 6-11.
.. [weinberg80]  Victor Weinberg,  **Structured Analysis,**   Englewood Cliffs, N.J.: Prentice-Hall, Inc., 1980.
.. [stuart80]  LaFarr Stuart, "LaFORTH,"   **1980 FORML Proceedings,**  p. 78.
.. [brooks75]  Frederick P. Brooks, Jr.,  **The Mythical  Man-Month,**  Reading, Massachusetts, Addison-Wesley, 1975.

