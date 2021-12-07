---
layout: post
title: "Reflections as a Manager"
date: 2021-12-06 07:40:00 -0500
tags:
- engineering-management
- management
- software-engineering
- start-up
- enterprise
- retrospective
---

I recently returned to being an individual contributor (IC) after having been an engineering manager (EM) for the past 20 months.  During that time, the team (and company) grew, shrank, and grew again.  The company was also sold during this time.  While not my first time being an EM, this stint has had a larger impact than before on how I view software development, management, and how companies are run.

This post will mostly review my time as EM at XMode/Digital Envoy (started as XMode and sold to Digital Envoy), but will occasionally mention my time at Siemens PLM.

I wrote more than I expected.  If you're stretched for time, the important sections are:
- [My Experience in Differences Between Startup and Enterprise Engineering Management](#my-experience-in-differences-between-startup-and-enterprise-engineering-management)
- [What I Did Poorly as EM](#what-i-did-poorly-as-em)
- [What I Did Well as EM](#what-i-did-well-as-em)


## Engineering Management at Siemens

While working at Siemens PLM as an IC, working on the OMNEO, I applied for and was hired as a backend team EM.  I was given responsibility of 7 - 10 people across 3 different sprint teams.  Being a larger company, this role was much further removed from the technical work than I had anticipated.  The role consisted of managing people, team organization, and product management; all of which I enjoy in moderation, but not full time.  

During my time at Siemens as an EM, I realized I did not like managing in a large organization.  I was far too removed from the actual work being done, often leaving me feeling unaccomplished at the end of the day.

This led me to begin looking for a role elsewhere that would allow me to "reset".  I was lucky enough to come across XMode Social, who hired me despite my questionable Scala.  At XMode, I became truly introduced to functional programming/functional Scala and was able to be fully immersed into a native cloud architecture.


## XMode

As I mentioned, my Scala was mediocre.  My first real project was converting a Java process that automatically transferred files and handle file name customization into a Scala based project.  This went very well and since 2018 has ran nearly flawlessly. I then moved into creating a [Data-as-a-Service (DaaS)](https://cahilltr.github.io/2019/03/27/AWS-Marketplace-Data-as-a-Service.html), via AWS's Marketplace.  This was usurped by AWS's Data exchange, which was fantastic news as I was able to leverage our file transfer process and eliminate the entire DaaS project.  My next major project was the export-api, which allowed ad-hoc exports of our data.  The project leveraged a JSON based DSL written for spark that allowed customers to essentially write their own custom exports.  By the end of 2019, Scala had eclipsed Java as my favorite language and I continued to look further into functional programming.

## My Time as an Engineering Manager at XMode

In the fall of 2019, my team lost 2 engineers and our engineering manager.  This put me as the person with the most seniority with our data pipeline.  At the time our CTO was filling in for the empty EM role, but it became apparent that there were things were not going as smoothly as they could.  So I asked for and received the responsibility of running our stand ups, which lead to me also running the sprint processes.

In December of 2019, I was offered the engineering manager role, which I accepted on the condition that we kept the role open and if we found someone to fill the job.  After hiring 3 developers onto the team by April, I was optimistic that we'd be able to hire an EM to replace me before summer.  Obviously, we didn't hire another EM, or I wouldn't be writing this.  

As 2020 progressed we were quickly evolving from a start up to a more enterprise company; this included hiring of a product manager.  As we evolved I found more and more of my time becoming consumed by product planning and scheduling.  Everything that was disliked about being an engineering manager at Siemens was starting to show up.  I asked my manager at the time to repost the job and allow me to move back to an IC. There were a handful of candidates, but none completed through the hiring process.

In December 2020, the company pivoted, which required an all hands effort; we stopped hiring and transitioned back into startup mode. This meant focusing on a single project, removing many of the excess meetings and overhead.  Had it not been for the stress of the how critical the pivot work was, I'd have enjoyed this time.  After the pivot was completed about 6 months later, we were acquired and started the march towards becoming more enterprise.  

Once again, we listed the EM role.  This time, we had better candidates and we were able to hire a new engineering manager, who started in October.  I had been the manager of the team for 20 months, seen the company transition from start up to enterprise back to start up and back again to enterprise during that time.  We were able to hire 5 people (including the new manager) during my tenure.

## Handing Off to the New EM

It's not often that an EM returns to being an IC at the same company.  But by doing this, we were able to ease the new EM into the role.  Instead of having to learn everything as they went along, they were able to get an idea of the projects, the code base, and the people.  This was the first month and I continued to handle all ad-hoc tickets, status meetings, 1 on 1s, etc.

 As we began the second month, the new EM took over status meetings, product road map meetings, 1 on 1s, and was tagged in all ad-hoc tickets and assigned some of the tickets out (I still handled ticket regarding questions internal to the team, but the new EM was still tagged so they could learn too).  As we entered the third month, most days I was completely an IC; only occasionally did I get EM work and the EM work ended entirely by the end of the third month or so.

## My Experience in Differences Between Startup and Enterprise Engineering Management

Engineering management naturally differs from company to company.  As such, all of this is written from my personal experiences.  At Siemens (and gaining momentum at XMode/Digital Envoy), engineering management consists primarily of people and project management.  Managing projects at an enterprise company tends to consist of continual back and forth with senior management, the product team, other stakeholders, and tech debt.  This results in many meetings, emails, deadlines, and moves away from moving quickly.  

People management differs much less between an enterprise company and a startup.  The two biggest differences were managing burn out and managing a spectrum of engineers.  Startups tend to have senior engineers, which results in developers having more autonomy and knowing what needs done.  Contrast this to enterprise companies, which tend to have a full spectrum of engineers ranging from interns and entry level developers to senior developers, as well as "A", "B", "C", etc players in between.  

For an enterprise EM, this means having to account for a wider spectrum individuals; helping interns and entry level developers find their path and helping guide them into growth, while letting senior developers take more control over their growth.  An enterprise EM also needs to figure out how to move "B" players to "A" players.  With startups, I've found that there's less emphasis on development of a developer and more on the product; in turn, siloing tends to be more prominent as developers work quickly to get a product/feature to market.  An EM at a startup needs to be acutely aware of when and where they can help a developer grow and also prevent siloing of team projects.

Managing burnout has only been an issue for me at XMode; due to the demands of the product team and being able to quickly move an idea to a marketable feature, startups tend to have less work-life balance.  For a startup manager, generally with a smaller team, the importance of foreseeing and handling burnout early cannot be understated.  In either case for an EM, it's important to remember that being proactive towards burnout (giving developers extra time off when needed, treating developers like people and not robots, being generous with PTO time, etc), pays deep dividends when compared with trying to manage a burning/burnt-out developer or having to replace a developer due to burnout.

The biggest difference and generally the deal breaker to being an EM, is the level of technical involvement for an EM.  At startups, EMs get involved, generally beyond just architecture review and PRs and actively contribute to the code base. Because of this, startup EMs feel the difficulties and issues that their developers feel, while also building a higher level of respect from the team than a non-contributing EM would.  I've found at both Siemens and XMode, this can lead to smoother meetings and better interactions with product teams/stakeholders.

I also found it important that the team be willing to tell me I was wrong or to speak up and challenge the leadership.  At XMode this wasn't really an issue, but at Siemens I sometimes had difficulty in getting feedback from the team, which lead me to use some techniques to get people talking (see note 2 for some of the techniques I used).  Some of those techniques were: doing a "round table", where everyone says the first thing on their mind; specifically asking quiet developers about their thoughts; and having a willing team member lead a meeting.

There's more here about the difficulties that an enterprise EM may encounter, but that's not to take away from the small team dynamics and quick moving nature of a startup.


## What I Did Poorly as EM

While there are many things I wish I'd have done better during my time, below are the biggest things I did poorly and how I'd have fixed them.

### Didn't Display the Team's Achievements Enough

My team achieved _alot_ during my time as EM.  I highlighted a few of our bigger accomplishments, but didn't showcase more of our smaller wins. Looking back, that would have been great for team morale and getting team members noticed.

### Didn't Participate in Planning Enough

I did not participate in planning as well as I could have, and the team's road map suffered.  I often worked on development, during the planning meetings (mostly due to point 8), only speaking up when asked or when something sounded wrong or unrealistic.  By being more involved, I could have given the team more say in the road map and had better organized efforts in tech debt payment.


### Mismanaged Low Performers

I managed a low performer, who is talented, but never quite lived up to their capability.  We ended up losing the developer and I believe that they knew they were underperforming and I suspect they were bored.  If I were to work a lower performer again, I'd attempt to better engage that person and work with them to have a shorter than normal feedback loop to help promote increasing quality that they're capable.

### Closed Minded to Some Ideas

While I was generally open to new ideas and allowed work on investigative projects, there were times when I didn't give an idea I didn't like or was unsure about the same allowances I gave other ideas.  This may have stifled creativity and motivation, not just for those ideas, but also for future ideas as there could have been a worry about being allowed to work on the idea. Motivation could have also suffered on any work related items.  In the future, when there's ideas I'm not keen on, I'd like to work more with the person proposing the idea to help them work through any issues.  This can lead to either the idea still being bad, but it was at least given attention, or the idea being good and worthwhile perusing.

### On-Call was too Big a Burden

For most of the time I was EM, we had a 3 person team including myself.  At our peak in 2020, we had 6 people.  Six people works great for a week on-call rotation; it's not too frequent and feels like less of a burden.  But with our teams of 3, the on-call rotation becomes a burden and can cause major disruptions in development cycles, especially when there were many issues during a rotation.  My untested ideas to help with the on-call burden of a small team was to either personally take 2 week rotations or have a rotating 2 week rotation (where each on-call rotation was in 2 week increments).  And while we compensated for the off the clock on-call incidents, rarely does handling an on-call incident leave the responder feeling accomplished.

### Argued for More Contractor Resources, Earlier

One of my biggest mistakes while EM was not asking for/being open to contractors to help fill out our workload and reduce tech debt.  There was a semi-significant amount of work that could have been handled by contractors that wouldn't actually touch the business logic.  We brought on contractors towards the end of my EM tenure and I wish I'd had pushed for them earlier.

### Not Understanding My Blind Spots

I wish I'd have had a better idea of my blind spots and areas to improve upon earlier.  I could have done this with anonymous surveys, to allow people to speak as freely as they were willing.  This was done with the larger team, but only once.  It also struggles to be done with a smaller team, but having it offered would have been worthwhile, as it present a forum to voice any issues.

### Taking on More Responsibility Than was Reasonable

As EM, I took the saying "Take responsibility until it crushes you" to heart, but instead of pushing back or distributing the workload when the responsibility became crushing, I worked more or let things fall through the cracks until they became unavoidable.  This lead me to be not as attentive as I should have been, which affected nearly all the above points.  Setting better boundaries would have been beneficial not just to myself, but to my team.

## What I Did Well as EM

### Reduced the Number of Meetings

I dislike Scrum as it's ran by many enterprises.  Many of the meetings merely interrupt the day, requiring a break in concentration for what normally ends up being lack luster meeting with mediocre results.  For example, story pointing meetings waste time because the estimates tend to be wrong and then the team becomes beholden to the incorrect estimates.

Retrospectives after every sprint tend to just be another meeting to attend with little coming from those meetings.  Retros should happen at a much less frequent cadence (say every 2 or 3 months) to give changes time to be implemented and become accustomed to the team.  Major issues that may arise can warrant their own meeting/resolution; which differs from a Retro as it's focused on a singular topic with a clear goal in mind.

Backlog grooming can be useful, but only when there's a messy backlog not every sprint.  Ideally, backlog tickets should be prioritized when they're created; since that doesn't normally happen, the occasional backlog grooming session can be used.  Sprint planning meetings can also be useful, but more often than not leads to a meeting of head nodding as the team should already know the priorities and goals of the team.  

Scrum does not easily allocate the ability to handle ad-hoc work.  This means, when there are high ad-hoc work sprints, it can appear that the sprint goals were not attained because the ad-hoc work took precedence.  The overhead of Scrum tends to be far too heavy for a small team with a vast array of responsibilities.

When I started as EM, we had all the above meetings in place.  The first thing I realized was that we never met our sprint goals, primarily due to ad-hoc work.  The second thing I realized was that the team knew the priorities, knew our backlog (where just about everything was of the same priority), and were willing to speak up.  With these 2 revelations, I got rid of all the Scrum meetings and moved to what I refer to as "minimal agile", but would probably be more regarded as Kanban.  

We were unable to move to a Kanban board, so I kept 2 week "sprints", where I would have the previous sprints unfinished priorities "fall through" or add new priorities to the sprint.  The primary principal of the system was "the priority is the priority", meaning we focused on the priority until it was completed or a higher priority came along.  We tackled tech debt in two ways: not letting it happen, even if the task took long or creating a backlog ticket to be completed later.  Backlog tickets were addressed when a priority during a "sprint" was completed and future priorities could wait until later (next sprint, in a week, etc).  With this approach, we were able to tackle road map priorities, handle ad-hocs without breaking the sprint, and address tech debt.

All of this resulted in reduced meetings (we had stand ups 3x/week) and bigger blocks of uninterrupted development time, which also helps with recruiting efforts.

### Treated People Like People

I like to think I did a good job of treating people like people and not commodities.  Starting with work/life balance, I created an aggressive compensation time plan for on-call incidents outside of work.  Anything under an hour was given an hour off, anything between 1 and 3 hours was given a half day off and anything more was given a full day off.  I would have preferred to give a combination of comp time and money, but I had no monetary budget.

I tried to give off afternoons the last day of the week for holiday weekends and the occasional Friday afternoon off.  If I were to do this again, I'd try to give more of a heads up so that the team could actually plan to use the time, rather than it being a surprise. I never asked the team to submit PTO time for appointments, uncontrollable issues, etc; allowing them to use their PTO as actual time off.  I was also a big proponent of letting the team work when they wanted too (so long as they made meetings they had scheduled). This resulted in some members of the team who were night-owls working late nights and coming in later in the day.  But their work was better than if they'd tried to conform to a 9 - 5 type schedule.  I never worried about work getting done or someone taking too much time off because I trusted my team to get their job done.

From a work perspective, I worked hard to prevent the team from being overloaded or from focusing on multiple projects by pushing back on the product team and sales.  Also, when possible, I tried to give developers preference to what priority _they_ wanted. Finally, every 1 on 1, I encouraged the team to carve out time in their day to read blogs, books, work on mini-projects, etc as well as remind them about their education stipend.

Treating people like you'd like to be treated seems to work very well when managing people.

### Maintained our Existing Production Systems

We made continual efforts to maintain and improve our existing production systems.  Rather than giving complete focus to new features/products, maintaining existing production systems helped reduce tech debt and lay a better foundation for future work.

### Gave the team the priority on "fun" and interesting work items

When a team member had a "fun" or interesting idea for our code base/processes, I would normally give them a few days right then to start a POC, or if they were working on a high priority ticket, give them time after the ticket was completed. By giving them time as early as possible, I hoped to "strike while the iron was hot" and hopefully roll their enthusiasm for the idea into a feature that we could utilize. Allowing a team member to work on their ideas let them be more creative, gave them a break from their normal work load, and embodied trust in that team member.

### Gave Priority to Fixing Issues that Caused On-Call Incidents

On-call can be miserable when everything seems to be going wrong.  To help give the team a tool to alleviate this, I gave the team/on-call person allowances to deviate from their prioritized work and address whatever caused the on-call issue.  But this went beyond on-call issues; it also allowed up to work on tickets like turning off EMRFS when S3 became consistent [link blog post] and prioritizing upgrades and better error handling.  By addressing on-call incidents when they happened, rather than creating a ticket and scheduling the work for later, the team was able to have confidence that their on-call time would result in a better code base and not just kick the issue down the road.

### Hired Well

The team and I hired well.  I helped to create the interview process and worked with our HR team to define the job descriptions that would appeal to the developers we wanted to attract.  I also had an onboarding process in place that gave developers a few quick wins and enabled them to contribute within the first week or two.  While this may seem like an attempt to see if the developer could "learn to swim", the intention was to make the new developer feel comfortable and confident in their work and in pull requests.  

The most important thing I did in the hiring process was to essentially allow the team to decide if they wanted a developer to join the team.  There were a handful of times where I disagreed with the team on their assessment of a candidate, but followed through with the team's decision.  This worked spectacularly. During my time as EM, we were able to hire 5 new team members (including my EM replacement) and each newly hired developer made significant contributions within their first 2 months of joining.

### Took on A Lot of Responsibility, Enabling Team Members to Stay Focused

Finally, I took on many small tickets, ad-hoc tickets, client success tickets (client questions, updating client configuration, etc), and less then desirable tickets.  By doing this, I allowed the team to stay focused on bigger, more involved tickets without needing to redirect their attention to ad-hoc or smaller tickets.  By taking on less than desirable tickets, the team was able to enjoy their work more, which hopefully boosted morale.  

But I didn't only take on undesirable tickets; I saved some tickets that I had interest in for myself.  I was also not able to shield the team from all the small, ad-hoc, etc tickets.  When I had too many of them come in at once, I had to start distributing those tickets to the team.

## Summary

My time as an EM at Siemens taught me I didn't want to manage at a large enterprise company, while my time as an EM at XMode taught me that I can enjoy managing a small team at a startup.  It also allowed me to experiment with Agile methods, which lead to the Kanban/minimalistic agile system we have now.

Being EM taught me quite a bit about myself as well.  It taught me boundaries and how much work I can actually handle.  It taught me creativity when working with limited resources.  The most important thing it taught me was how I wanted to be managed. By managing others, I was forced to think about how I as a developer would want to be managed.

In the future, I'll remain focused on being an IC, hopefully taking on roles as a tech lead/project lead. And while I won't actively seek out an EM role in the future, I would consider an EM role at the right startup company.
