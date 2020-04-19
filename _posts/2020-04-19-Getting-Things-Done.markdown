---
layout: post
title: "Getting Things Done: Dodging Helmetfire by Offboarding the Brain"
date: 2020-04-19 15:30 -300
categories: General
---

## Getting Things Done Is State Management
If I'm likely to be known for anything, it's probably the magpie-like way in which I acquire new projects to work on. I've always been a bit of a classical overachiever, perhaps not in the sense of actual achievements, but in the sense of attempting to achieve many things at once. Longtime followers of this blog, or people who know me in really any way, are no doubt well aware that at any given point in time I'm working on several things at once: two or three coding projects, something I want to build, some new skill to develop, or even just a few complicated tasks in open world games.

Over the years I've found it increasingly helpful to have some kind of system to keep track of, well, everything. If I tried to manage the state of all of my projects and efforts in my actual head, there'd be very little mental RAM left to handle any other kind of activity - like the projects themselves. To get around this, I've adopted a system of trackers, notes, and tools that I use to offload as much "management" as possible, leaving me free to at least attempt to focus on actual tasks.

Keep in mind that I grew up through the 90s - I had a palm pilot as a kid and used it for its stated purpose, and I'm known enough for my love of trips to office supply depots that my parents got me an actual, physical briefcase for christmas recently. I'm a nerd's nerd, a geek's geek, and with all that in mind your mileage may vary.

## Premise
This whole system is predicated on two truisms:
1. By comparison to phsyical storage media, typical human memory is unreliable.
2. By comparison to typical human memory, my personal memory is downright ghastly.

This can be further expanded into a few less-general (and therefore more helpful) maxims:
1. It is desirable to track everything. Even if things are later to be discarded once an action is completed, they can be tracked until it is.
2. If you're going to be retaining a large amount of task data, you need to keep it organized.
3. No system that is too complicated to be used will be.
4. No system that requires greater energy to use than would be spent in keeping state in human memory will be used.
5. No system that involves managing human creative activity will be set in stone.

It's important to note that because of the subjectivity of 3 and 4, personal tastes relating to 2, and rule 5 in full that there is no one-size-fits-all system. I encourage you to adopt as much of this as you find useful and make changes wherever you see fit.

## Prior Art and Tools
There are no original ideas, and if I tried to scratch-build every tool I used to keep track of my work, I'd never get any work done. This system, by and large, has been evolving since I was in my earliest months of my first stint at college, which I shudder to admit is now a decade in the past.

### GTD
The core of my system relies on a similar system, originally created for a pre-widespread-digitization age, described in the book _Getting Things Done_ by David Allen. Technically, that system falls under his copyright. However, with the exception of working from his original premise, nothing I'm describing below is especially unique or typical of David Allen's work.

The system called 43 Folders by some adherants, essentially boiled all work down into things that could be completed promptly (tasks) or things which required several steps to achieve (Projects).

### TickTick
For several years, I was an avid user of Wunderlist. Wunderlist is dead now, or nearly, and so I went on a brief quest to see if I could find an even better task manager app. In that search, I found [TickTick](https://ticktick.com). You can use the tool of your liking, as long as it will allow you to:
- Create individual todo items
- Put those tasks into folders
- Assign those tasks due dates (and extra points for recurrence)

TickTick won out over several other candidate apps because it:
- Has an android native app version as well as a browser version, which synchronize well
- Supports desktop and android notifications
- Supports tags (for additional sorting) as well as smart lists (which filter content based on how it is tagged), and
- Has a dark mode. I sit at a computer all day. I prefer dark mode.

You can implement most of my system in any app, even without the smart lists, though figuring out just how to replace smart lists with regular folders is a good exercise for the reader.

### Google Calendar
I know the new hotness in the security industry as well as with privacy advocates as a whole is to hate on Google, but to a certain extent I'm actually ambivalent. For one thing, I use an android phone, and for another, my google profile is almost as old as I am, so at this point "going dark" from their platform wouldn't actually change much.

Google Calendar does what it says on the tin. It's a calendar. It integrates nicely with my phone, supports all the usual digital calendar utilities like recurrence and notification, and it's got a usable browser version.

### Github Project Boards
I guarantee that at least one person who read the section on TickTick wondered why in the hell anyone would ever use a straight to-do list app when board-style apps like Trello exist, and this is my answer. Some state is too complex to manage in a straight line like a todo-list would provide. To this end, I use github extensively, and not just for its project boards.

If you have Github for your projects (even your private ones), you have:
- A cloud backup of your VCS, assuming you're using git to do VC for the project you're working on
- A wiki attached to the project for reference material
- Project boards to track individual sub-projects of the thing you're making, which can be laid out with as few or as many columns as desired (I've been favouring 3-column Kanban, but I'm thinking of adding a few middle columns.)

People who've read GTD or read up on 43 Folders probably see splitting work-to-do between TickTick and Github a violation of the idea that a single master system rules all. This is reasonably understandable, and to be honest this seperation adds friction to the system. However, there are two justifying benefits:
- I can make github project boards public without making my entire todo-list public, and;
- Taking complex project state out of TickTick lowers the "All View" Pressure of the system, which is desirable.

### References
There is information you will encounter in your day to day life that needs to be tracked as part of your system but isn't, necessarily, a task. Bills to retain, documentation for that one weird accelerometer module you're using, that interesting whitepaper you wanted to read later, Diane's TPS report you promised you would review, and so on. Projects themselves also generate stateful data in this way, like a diagram of an experimental pinout or that `#TODO: Resume here` note you leave yourself when you realize 3 AM is probably the wrong time to try implementing a patch.

Understanding the type of information you need to collect and how best to organize it is something I am constantly playing with personally. In general though, I have two maxims:
- Minimize the number of places information about something *could* be.
- Make recording new information as frictionless as possible

The second point is particularly important as it leads to something I call a hash folder: the junk drawer of data that you collect over the run of a period of time that all gets saved into one location. Items that are in a hash folder could be long-lived references you'll hold on to for years (like my offline copy of the Py3.6 documentation site) or ephemeral notes that you only hit save on out of habit (a JWT you disassembled to get the target algo out of.) Hash folders are good, in that they lower the resistance to taking notes digitally or saving bookmarks, as there's no indexing step to take right when you're recording something. They do, however, become a work burden that needs to be tidied up later.

I also have, in violation of most modern judgements, revised something known as the Commonplace Book. This is a physical notebook in which technical notes, recipes, or basically any kind of information I need goes. It acts something like a physical hash folder - somewhat less ephemeral than a sticky note or a piece of scrap paper, but not always a long-lived reference. As this notebook gets closer and closer to getting full, I tend to go through it and start dog-earing the interesting pages so that I can pull the data out of them to make it more accessible in the future, or at least copy into the next book.

This practice could become a blog post all on its own, but it's such a small part of how the system works it's not worth diving into further detail about here.

## Rebuilding (And Explaining) My System From Scratch
### Don't Actually Do This
Don't get me wrong. You should definitely have *a* system for managing yourself. But it needn't be mine (remember rule 3). Think of this section less of a step-by-step guide on how to build my exact system (because it isn't) and more a general concept of what a single-flow system like this should look like.

### Implementing a Bare-Minimum Structure in TickTick
TickTick is effectively the primary state engine for this entire task management system. While we will be using Google Calendar in a few specific ways, the actual to-do list app is at the core of how tasks get sorted and managed in the system. You can create my system very quickly in a few quick steps.
#### Create Needful Folders
Some contextual queues (like "Household Tasks") or ("Things to Buy") are best treated as folders. The exact list of folders you need is going to depend on your activity level and what you do. I have some parent folders that might be a good place for you to start:
- **@Errands**: A parent folder that contains a general folder of the same name as well as a second folder called "Groceries", in which each item on a grocery list would exist as a specific task.
- **KenshoSec**: Contains actions specifically to my side work at Kensho Security Labs, seperated into folders by their general area, such as "Website Maintaince", "Server Upgrades", and individual lists for particular projects.
- **Household**: Which is where Steven Homemaker would put all the chores that needed done that week.
- **Someday/Maybe**: A *hidden count* folder which contains action items that I *might* get around to doing later, such as work that's out of budget or a reminder to dig old project state out of reference and restart the project itself.
- **@Holding**: A very important folder! This is where I put work that *is waiting for someone or something else to complete*. If I've ordered a part, contracted out some copy editing, asked someone else to do something, or need to wait for a large batch processing operation to complete, it goes into Holding so that I can check on it later without actually thinking about.
#### Create the Needful Tags
There's some friction involved in creating tags on TickTick, so I create a few at minimum:
- NextActions, which indicates that the task is suitable to be on the Next Actions List (discussed below). This lets you flag an item to go onto Next Actions without actually moving it out of the project it's part of.
- Workblocks, which is much the same in NextActions as it puts an item on the Workblock list.
- Blocking, which can be used to indicate that there are actions further down the road of some project which rely on this project to complete, and
- Context tags such as @Computer to suggest that an activity would rely on you satisfying some sort of condition (being at a computer) or otherwise suit a particular context. Again, this allows you to create smart lists to better organize your work.

#### The "Next Actions" List
Back in Wunderlist, this was an actual list. Now, with TickTick, I'm glad to say you can use smart lists to just add the NextActions tag to an item to put it on this list.

If you were following GTD's ruleset to the letter, this is a list of *individual tasks that are completable in 2 minutes or less*. I take 2 minutes more as a rough guideline, and instead reserve Next Actions for a list more like "activities I could reasonably achieve in a single shot provided I have the downtime", with a vague time of something 15 minutes or less in the back of my mind.

As an exception, on the weekends, basically the whole Household list *becomes* the Next Actions list as I try to desperately pretend two professional adults live in this house instead of a couple of kids who have somehow conned the world into treating them like grown folks.

In my specific case there is a companion list called *Workblocks*. This corresponds to activities slated for a particular work block in Google Calendar, and is where tasks that are monolithic but potentially time consuming goes. I'll talk more about the workblock and why it matters in the Calendar section Below.

### Implementing your Google Calendar Recurrences
People have written whole books on effective calendar management, but outside of an at-work context I don't really use my calendar *that* granularly. Instead, I use google calendar in the following way:
- Actual appointments go on in a specific colour at their proper time, as you would expect.
- I have a weekdays-only recurrent event that blocks out my working hours (padded to include my commute) so that I don't schedule personal stuff inside my work day.
- I have recurring events on particular workdays reminding me to attend to the household list, because I'm a child who won't clean up after myself without my phone to nag me, and even then you could flip a coin, and;
- I have recurrent events on particular workdays called Work Blocks which are dedicated either to particular projects or particular classes of projects. (e.g. a writing day, a day to work on Tapestry, a day to learn for a new cert, etc.)

These last type of appoinments are where the Workblocks tag comes in in the task list. If I were sorting through projects related to writing to figure out what to do on writing day, I could set actions that are part of that project to be due on that day, at the end of that work block, and add the Workblocks tag to make it easier to find those specific items when it comes time to work on them.

Making the work blocks specific (on mondays we write novels) or general (I will do deep hobby work on any one particular hobby at 8pm each night for at least one hour) is a deeply personal choice. I've tried both and haven't found one more effective than the other. 

### Setting Up Tools for Reference Management
This takes surprisingly little. For avoidance of several holy wars simultaneously I am not going to comment on text editors, systems of note taking, systems of organization, particularly useful notepads or pens, or the correct way in which one should categorize physical or digital notes.

I will only specify the creation of three specific things;
- A hash folder somewhere in your local filesystem that you can just quickly save virtually anything into. I used to use a special folder for this; after working digitally for some time, I have now just made the Downloads folder the hash folder.
- A hash folder within your browser that you can use to quickly throw a bookmark to later be categorized, and;
- A physical hash folder, or inbox, or tray, or taped-off-section-of-desk that you can use to store notes, in the physical sense, for the medium term before later deciding to properly file or purge individual notes.

### Further Friction Reduction
This sounds complicated, right? So far you've got at least three systems of record to track, and most of that is going to be a file explorer window and two browser tabs. Ideally, there'd be a way to tie everything together into one system as much as possible.

In my particular case for example, I have found it useful to combine all of my email systems into one application so that I at least have only one place to check my mail. In an ideal world, if you had your own license for Microsoft Outlook (or were very comfortable in thunderbird), you could approximate all of these systems together into such a tool with some modification.

Generally speaking the easier it is to use the system, the more likely you will be to use it. While you are adding some administrative overhead to your life no matter what, you can do as much as possible to reduce that.

### Understanding the Difference between a Task and a Project
There's a very clear distinction that needs to be made here, between tasks and projects:
- A task is monolithic, and complete within itself with no further segregation (or, you could arguably seperate it into smaller items, but it doesn't make sense not to do all of those items at once). "Wash the Dishes" is an example. You could technically break that out into "wash pans" and "wash cutlery", but why would you.
- A project is a goal or objective that you can and likely will seperate into smaller tasks. "Release Tapestry 3.0" would be an example of this.
 - This may be because the project itself is complex, with many moving parts
 - Or because the project is very straight forward, but will take a long time and shouldn't reasonably completed in one sitting.
 
 As a general rule, you will track tasks as individual items in TickTick, whereas a project would be a list, either on its own or in a folder. For example, my "Creative Projects" folder contains a list for a project called "JLE Heist Novel Thing" which contains the actions I'd need to take to finish hammering out a draft of a novel I may or may not ever actually try to put in print.

### Initial Data Dump
This is probably the hardest part of the project, and likely the most time-consuming portion of setting up a system like this. The David Allen Group actually publishes a tool to help with this called a [Trigger List](https://gettingthingsdone.com/wp-content/uploads/2014/10/Mind_Sweep_Trigger_List.pdf). The general idea is to sit in the inbox of TickTick, and go down the completion trigger list, recording every actionable item you can think of as an individual task in the inbox. I would find this extremely daunting now - my current TickTick setup has 108 action items in it at time of writing!

David Allen himself also suggests taking this time to sort out your reference items.


### Sorting Actionable Items
What follows the data dump - as quickly as possible, is sorting items. In my experience, this goes in three phases:
- Sort everything in the inbox so that it's in a list - either as part of a specific project's list or a general list like your chores or errands list.
- For each item, make a quick evaluation of how long you think it will take and either add it to the description to handle later (my method), or use that information to immediately make one of two choices:
 - Tag it as a Next Action, or
 - Pick a specific time to do it, and assign a due date with the reminder for that time.
- Flip over into the "All" view, and identify actions that would be appropriate for your work blocks. Tag them accordingly and make their due date the date and end-time of that workblock.

At this stage, you have the system organized with all of your state managed. It won't be perfect. Lists should be created organically as needed, and deleted when no longer appropriate. You will periodically realize you needed to add a task or remove one. Some tasks will need to be made recurring. Entropy rules even your state management system - you will need to constantly work against its tendancy to fall apart.

## Maintaining and Using the System
### Daily Maintenance Tasks
When it's time to start working within the bounds of the system, I do the following tasks:
- Check the Today list to see if there are any specific work items to do on a given day. This is almost always the case, and is usually the workblock item.
- Check the Tomorrow List to make sure there's at least one thing on it (and add something if there isn't.)
- Check the inbox and file anything that I haven't put away there yet.
- Check my mail inboxes and create action items out of anything I need to, and otherwise sort my email (inbox 0 is a thing!)
- Check Next Actions. If it is less than five items, I go to the "all" view and add the nexactions tag to things until the list is full again. It's important (in my case) to keep a variety of items on next actions to avoid stagnating on one particular topic.

### Weekly Maintenance Tasks
Once a week I do a quick audit of the system to make sure it's functioning correctly (and this audit is itself a recurring action item). The general idea is much the same as the daily flow, except that I start by taking an opportunity to run through the trigger list to make sure there's nothing truly missing from tracking. 

This is also when I sort out all of my hash folders, deleting items that are no longer required or else filing them away nicely.

### Quarterly Maintenance Tasks
While it doesn't directly impact the system, I take the opportunity once every few months to make myself a set of goals, separated into time frames:
- Long-Term Dreams (vague notions like "find a house to actually own")
- 3-5 Year Visions
- 1-2 Year Goals
- Projects and Goals for the Next Six Months
- Projects and Goals for the Next Three Months

Anything that goes on the list has to be measurable, and I use this list to determine if projects need to be added (or removed) from my tracking.

On that note, retiring projects is a very easy process:
- Print the existing project list to a PDF
- Put that PDF with the reference files for that project, either in the filesystem or printed and stored physically
- Add an item to the Someday/Maybe list to remind myself that the project exists.
- Delete the project list itself.

## Systems Aren't Perfect
As with any semiorganic system there are a lot of friction points and grey areas

### Github/TickTick split
Not having every single action item in TickTick is both a blessing and a curse. While it means that I'm not burdened with "All View" pressure for having dozens of extra action items that are "in the weeds"-specific for individual dev projects, that also means I need to add back in work-block items to remind myself that the github project actually exists.

Deciding to split a project off into a github project usually depends on its complexity. Technically, I could do VCS for writing projects, but there aren't enough action items or reference documents to make a full github repo worth the extra work. On the other hand, a development project requires on the order of hundreds of steps and adjustments. Tracking those adjustments can sometimes be done in code comments if you have an IDE that supports special highlighting for TODO comments, but this is unweildy when compared to a proper github Kanban board (especially when you consider you can add whole github projects to other github projects as individual tasks.)

While I will throw a `#TODO continue here` if interrupted or rough a function out in comments, any "true" state management or future work planning *needs to live in the Project tab* and any documentation needs to either be in docfiles or tracked in the project Wiki. This includes adding things that were previously only on paper to the wiki as time permits.

### Work/Life Balance: Incorporate or Discorporate?
This is a personal choice. My day job work is highly technical, involving dozens of emails to be read a day (out of hundreds received per day - thank god for filtering). While I have a similar (simplified) system for work's work, and I have some work-related recurring tasks living in my main system (mostly because I haven't bothered to re-create them in outlook), I don't support merging them together beyond that.

### No truly-centralized viewing location
Even the "All" list of TickTick can't tell me everything that I'm working on. However, that becomes something of a forest-for-the-trees situation, and creating such a view would also require a project in its own right that would have to consume both the ticktick and github APIs.

### The problem of "All View"
This system potentially lists a hundred or more assets at any time as the count of the "All" list in TickTick. Sometimes, I find this highly intimidating. Short of writing a custom browser extension to hide ONLY that count, there's not much I can do about that on a technical level.

---
If you find any of my projects useful, they can be sponsored by backing ZAdamMac in the [GitHub Sponsors Program](https://github.com/sponsors/ZAdamMac). For the first year of the program, Github is matching your sponsorship pledges - so they essentially count for double!