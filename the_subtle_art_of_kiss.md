
# The subtle art of KISS

If you have been to one software engineering interview, chances are you have been asked to elaborate on your SOLID principles. And with good reason. A good grasp of those is essential to the craft. In the following though, I would like to shine a light on a different and much more underappreciated acronym; Keep It Simple Stupid (or Keep It Short & Simple for those of us that might feel delicate about the use of stupid), a.k.a. KISS. The acronym is pretty self explanatory and the idea behind it has been expressed in multiple ways and in contexts outside of engineering. To quote a few variations:

> - Occam's razor, "the simplest explanation is usually the best one."
> - Antoine de Saint Exupéry's "It seems that perfection is reached not when there is nothing left to add, but when there is nothing left to take away"
> - Leonardo da Vinci's "Simplicity is the ultimate sophistication"
> - Ludwig Mies Van Der Rohe’s “Less is more”
> - Albert Einstein's “Everything should be made as simple as possible, but no simpler.”

The idea expressed here is minimalism. Simply put, achieving simplicity by stripping our designs down to their most essential parts to arrive at lean and elegant solutions.

## What does this have to do with software? 
You might be familiar with minimalism as an art movement or architectural philosophy and possibly wondering at this point why a software engineer would care about any of this. The term KISS was coined by Kelly Johnson, lead engineer at the Lockheed Skunk Works (creators of the Lockheed U-2 and SR-71 Blackbird spy planes) and meant that the aircrafts they were designing should be repairable by an average engineer in the field, under combat conditions and with simple tools. 
As such, this was an engineering principle focused on the maintanability of a product. Now you see where I'm going with this; your code products should also be maintable by an average software engineer armed with common tooling under production incident conditions. For most of the software out there, maintainance is by far the largest part of the effort going into it, usually far outweighing the effort invested in the initial implementation. Simple solutions and designs have a lower barrier of entry for new engineers and don't require as much mental capacity to reason about. Our main goal is not to come up with the smartest or even more optimised solution but rather with the solution that will make it easier for our colleagues, or our future selves, to understand and evolve the codebase. Or to quote John Woods,
>“Always code as if the guy who ends up maintaining your code will be a violent psychopath who knows where you live”

## The smells 
Before we delve deeper into how we can achieve simplicity though, let us see what are the common attitudes that introduce unwarranted complexity into our solutions.

### Shiny new toy syndrome
> "The news appeals to the same jaded appetite that makes a child tire of a toy as soon as it becomes familiar and demand a new one in its place" - Christopher Lasch

This particular variant just has to use the latest toy out there and will stop at nothing to fit it in any design. They will deploy a k8s cluster to host a wordpress website, set up a kinesis stream to replace an internal synchronous REST API and will bend their design to impossible levels of complexity if it means they can add another skill to their LinkedIn profile. Approach with caution and ask them for a cost-benefits analysis.

### Lasagna syndrome
> "The object-oriented version of spaghetti code is, of course, 'lasagna code'. Too many layers." - Roberto Waltman

We've all implemented an onion architecture at some point and could speak for its relavite merits but this variant lives and breathes layered architectures. No solution is complete unless the critical path takes us through at least ten layers complete with their own DTOs, exceptions and mapping layers. Introduce them to [vertical slice architecture](https://jimmybogard.com/vertical-slice-architecture/) and watch as their head explodes.

### The world is a nail syndrome
> "I suppose it is tempting, if the only tool you have is a hammer, to treat everything as if it were a nail." -  Abraham Maslow

For this variant, the defining moment in their career as an engineer was getting a metaphorical hammer. Now everything is a nail and nothing will stop them from going to any lengths so they can use their hammer in the solution for any given problem. Do we need persistence for a service with a lot of complex access patterns that can also evolve over time? Sounds like a problem for MongoDB. This is a case similar to shiny toy syndrome but the toy doesn't have to be shiny or new. Asking them for a comparative analysis of a few alternatives would be your best strategy (and a fair amount of annoyance for them).  
 
### Look at how smart I am syndrome 
> "Think of how stupid the average person is, and realize half of them are stupider than that." - George Carlin

It is a sad reality of life that the average person thinks they're smarter than the average. And unable to see the irony. I'll have to admit that I fall into that pitfall that myself at times. My redeeming quality though is that the irony is not lost on me. For this variant, a solution design is not a way to deliver value to their customers. It is an opportunity to broadcast to the world how unbelievably smart they are. Why settle for a simple and effective architecture when you can have countless moving parts interacting in ways that the human brain fails to fathom? If future maintainers cannot work with it, then they should have been as smart as you, shouldn't they? There is no strategy I know of that would work dealing with that kind. Avoid at any cost. 

### Job security through obscurity syndrome 
> "Glory is fleeting, but obscurity is forever." - Napoleon Bonaparte

This is very similar to the "smart guy" syndrome but the complexity here is not a by-product of an engineer's godlike intelligence but rather a calculated strategy to create a nice little niche in the job market for them. The steps are fairly simple,

- Create or inherit a codebase/component of unmanageable complexity
- Build up your expertise in it
- Make sure nobody else can (working software over documentation, innit?)
- Convince management that refactoring or redesinging it will be prohibitively expensive 
- Sit back and enjoy your new-found role that only you can or even want to fill 

This variant is harmless really until someone asks them to hand their domain over to you. If that happens, run as fast as you can and never look back. 

## The pitfalls
### The problem must be solved
> "Everything should be made as simple as possible, but no simpler." - Alber Einstein

We should strive for simplicity in our solutions but they must still solve the problem at hand within the required constraints. There's simplifying the solution, and then there's "simplifying" the problem to fit our simplistic approach. Dropping or altering requirements to allow for a simpler solution is of course a valid strategy but only after negotiation with the stakeholders.   
 
### Timely optimisation is not evil
> "We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil. Yet we should not pass up our opportunities in that critical 3%." - Donald Knuth

Premature optimisation is the root of all evil, but there is a time and place for optimising our solutions. After profiling an application and identifying the limiting factors in the critical path, it is not only permissible but desirable to allow for some complexity in order to optimise our solution and allow it to scale to our needs. *Timely* is  the operative word here though. Instead of micro-optimising what we **think**  might be the bottleneck, introducing unnecessary complexity in the process, it is perhaps best to optimise what we **know** is the bottleneck, after we've done our due diligence to identify it. 

### Complexity has to justify itself 
> "Dealing with complexity is an inefficient and unnecessary waste of time, attention and mental energy. There is never any justification for things being complex when they could be simple." - Edward de Bono

So it is fair to say complexity is a tool and as such can be useful in certain contexts. My rule of thumb to identify when is asking "why?". Why is this complex approach justified? What value does it bring and what are the reasons I have eliminated other, possibly simpler, solutions? In short, how can I justify introducing this complexity? If I had to describe this approach in a more algorithmic fashion, I would come up with something like:

 1. Start with the simplest possible design/solution
 2. Review/profile it
 3. Are there any problems with it? 
 4. If yes, introduce the minimum amount of complexity to address your issues and go back to (2)
 5. If no, you are done
 
Or in a more reductive approach, for instance if you start with an already complex design:
 
 1. Is there an area of excessive complexity in your system? 
 2. If no, you are done
 3. If yes, design a simpler alternative and test it 
 4. Does it work adequately as well as the original system? 
 5. If yes, adopt the change go back to (1)
 6. If no, reject the change and go back to (1)

## Abstracting complexity
So if complexity is both detrimental to a system but sometimes useful and/or unavoidable, how could we go about managing it? As software engineers, part of our job is to manage complexity and the answer is usually by building on top of layers of "facades".
Analogous to a [facade](https://en.wikipedia.org/wiki/Facade "Facade") in architecture, a facade is an [object](https://en.wikipedia.org/wiki/Object_(computer_science) "Object (computer science)") that serves as a front-facing interface masking more complex underlying or structural code. - [Wiki](https://en.wikipedia.org/wiki/Facade_pattern)
But to move away from a strictly OOP view of this, the programming language you use is a facade over assembly language which itself is a facade over binary code. This is a techicque that is as old as software itself. We take a complex system that we depend on, we abstract away as much complexity as we can by building a "facade" on top of it, and we iterate the process as much as needed. This is how we can build systems that would otherwise be impossibly complex to design and reason about. Each level only cares about the complexities inherent to it and trusts the facade to work as expected without having to worry about its internals. Of course in practice some level of understanding of the inner workings will be beneficial, but we can generally still go about our work only thinking about the abstractions relevant to our "complexity level". 
That same approach can be adopted for our code or the systems we build. Structure our solutions with carefully designed abstractions that hide away the complexities that are irrelevant to the task at hand so that we can use them to build on top but in a manner that is structured and manageable. 

## Go forth and KISS
KISS is the most underappreciated engineering principle I have encountered in my career. A simple solution is a solution that is easy to maintain, easy to debug, easy to build on top of, and less likely to drive your engineers mad to the point that they will start polishing the old CV.
That said, complexity is oftentimes unavoidable or even desirable and, as such, another tool for us to leverage. I would rather start simple and let the solution organically evolve to higher levels of complexity to match my problem space than start inventing complex solutions to problems that nobody has. 

> "Junior programmers create simple solutions to simple problems. 
> Senior programmers create complex solutions to complex problems. 
> Great programmers find simple solutions to complex problems." 
>  
> [Beautiful Software](http://www.chc-3.com/pub/beautifulsoftware_v10.htm) by Charles Connell.
