# Embracing the context switch

One of the topics that will often come up on a dev's complaint list is context switching and how it takes away precious focus and productivity. During my career as a software engineer I have eventually learned that switching context cannot always be avoided. So perhaps a more pragmatic approach is to understand it a bit better, work out how to avoid letting it disrupt my focus time, and use it to my advantage when appropriate.  

## What is context switching?
Context switching is the mental effort required to switch between different tasks, and is commonly thought to be a cause of productivity loss and mistakes.  This is understandable since humans have limited capacity to retain information and are not as good as computers at moving between topics by loading and unloading the necessary context.  Every time we interrupt our attention from a specific task, our brain will have to make an effort to return to the required mental state later which will affect our "flow", our focus towards the task at hand. This effort of reconstructing the mental state we were in while focusing on this task is what causes adverse effects to our productivity and can even lead to mistakes.

## So is it all bad?
There is an important distinction to make first between the two types of context switching:
* __Involuntary switching__ (a.k.a. interruptions): This is a colleague interrupting to ask us a question, some meeting that breaks our focus time in half, or any other disruption to our flow that was caused by an external factor beyond our control. Those interruptions are fairly typical in the workplace and are the most common cause of context switching. 
* __Voluntary switching__: Ever had a tough problem at hand so you decided to walk away for a bit and come back later with new eyes? Well congratulations, you have participated in some voluntary context switching. The reason we tend to do this is to force our brain to unload the information pertaining to a problem so that when we come back to it later, we are more likely to think about it slightly differently and perhaps see a solution we were blind to before. 

So context switching can be a conscious choice and, as such, desirable and beneficial to our workflow rather than a dreaded focus-draining nuisance. 

## Is voluntary switching all good then?
Even with voluntary switching, returning to the mental state required to tackle a task will be challenging. What can we do to help our puny human brains do it effectively? 
### Plan and strategize
Since these disruptions are voluntary, it is possible (and it pays) to plan when to have them so that we maximise the benefit while minimising the scope of the switch. Finish up that method, follow your code execution to the bottom of the call stack, finish up your paragraph/thought on the document you are drafting. Spending a bit more time to get to a mental state that will be easier to recover later is going to pay dividends after you come back to the problem once you've had a coffee break or a walk to collect your thoughts. 
### Divide and conquer
If we try to arrive at some partial conclusions before consciously leaving our flow then the complexity of the task is reduced and so is the effort of reloading our mental state, e.g.we manage to reproduce a bug and write a test covering it before deciding to break our flow. Now the task is reduced from "figure this bug out" to "make this test green" which will be considerably easier to come back to. 
### Use memory aids
Taking some notes, leaving a code comment for our future selves, keeping a diagram up on the whiteboard, or leaving the relevant files open in our editor are all excellent examples of helpers for our brain to get back to our flow as fast and as efficiently as possible.

## What about involuntary disruptions?
So we have concluded there is such a thing as a desirable context switch and discussed how to make it easier, but is there a way to minimise the undesirable switching?

### Do Not Disturb and embracing asynchronous communication
We can try and move interruptions from the involuntary end of the spectrum towards the voluntary. Set up time periods where you can do focused work and ignore any non-essential communications during them. Educate your peers and your manager about this and help them manage their expectations. Of course, you will still interrupt your focus if there is an ongoing production incident you need to help with, but a question about "why is X working like it is" or "where to find Y" can probably wait until you are ready to give it your focus. 
Set up clear expectations about maximum delays for different communication channels that will help both you and your team to know what to expect and to slowly shift to a more asynchronous communication style without it becoming a blocker.  

### Optimise your meetings
First we can aim to cut down unnecessary meetings. Try to ensure there are clear agendas on every meeting and only the people that need to be in it are on the invite list. Try to answer the following questions before booking a meeting:
* What are we trying to achieve with this meeting? What is the desired outcome? 
* Who can help achieve that outcome? 
* What information could be beneficial for participants to have before joining?
* Does it even need to be a meeting? Could it be asynchronous instead? Could I send an email?

A good rule of thumb would be that sharing information can easily be asynchronous while aligning and making decisions is best done in a meeting. For instance, we can inform our team about the details of some production issue via a well-crafted email or sharing some incident report and we can then schedule a meeting only to actually decide how to best deal with it. By converting the information sharing component of this from a live discussion to an asynchronous mode of communication, we make a better use of everyone's time as we let them digest the information at a time that suits their calendars best and we ensure that when the meeting happens every participant has a good grasp of the topic and can contribute more efficiently. 
Another potential optimisation is when to have our meetings. Aim to book meetings towards the start or the end of the day, around lunch time, or close to other meetings rather than breaking up a nice juicy chunk of focus time. Getting back in flow after a disruption costs more, as we saw earlier, so it makes sense to try to book our meetings at a time where our focus time is already disrupted. 

## Filling the gaps
As developers, we often have some downtime while performing our tasks. Waiting on regression tests to run, monitoring an app after a deployment, waiting on our PR to be reviewed or having a small chunk of time in between meetings that is not long enough to devote to any highly focused task. This is where embracing context switching can shine. We can use those small chunks of "dead time" to reply to an email, do a quick PR review, answer a colleague's question, drop a ticket to the backlog and so many more similar short tasks. The more we hone our context switching skills the more effective we will be at filling in those gaps the same way our applications do, by parallelising.

## Conclusion
Context switching is an unavoidable aspect of software development, sometimes a necessary inconvenience and other times a conscious decision that can help us look at a problem from a different angle or use our downtime more productively. We should aim to reduce its disruptiveness and build the mental tools to do it more effectively. With practice, it can be another tool in our arsenal used to improve our productivity by increasing our capacity to parallelise work. We often need to "juggle" tasks in our professional lives and it will serve us better to embrace and manage context switching than to dread it.