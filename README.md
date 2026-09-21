# Ultra Loader Bring-up Reports

This repository collects **bring-up reports** from people testing arcade games
with Ultra Loader. It holds no code.

## Why a Reporting Structure

As it stands right now, there almost 600 Modern Arcade Games sitting in the catalog,
with more being added as time goes on and the community gets access to more arcade 
dumps. This entire project is being run by one person at the moment, and bring up time,
to test, debug or otherwise troubleshoot a non-working game takes alot of time and effort. 

Since this is, and always will be, a community project, it just makes sense that I then 
devised a way to allow the community to participate in this entire bring-up process, 
to aid in the process of getting these games to work in Ultra Loader for everyone. This
is the way. We are technically behind by over a decade, and it will take time to get to
a place where we are fully established, and as stated on the site, this is a full and open
community effort, and I can't do it alone. I will rely heavily on your reports and coding
expertise, in an effort to get to the goal of unlocking all games free for the community.

I already did the hard part! We now have the loader. Now its time for everyone to lend a hand! 

As Vegeta once said:

**"People of the Earth! Lend us your Power!!!"**

## What a Report actually is

When a game doesn't work, the loader can package up what happened; it can bundle
the last few runs from that game's log, which board and container were used, what
your loader settings were, and if you used the "Hypothesis Engine", built into the
loader, which will include **everything it tried and what each attempt changed**.

That last part is the valuable bit. Every line reading "no change" is a cause
ruled out, on real hardware, with a real copy of the game. Ruling things out is
most of bring-up work, and it is normally the part that evaporates the moment
someone closes a window.

## Learn how it works

**The Hypothesis Engine** (please see: HYPOTHESIS-ENGINE.md) explains what the 
engine is, how it decides what to try, how to run a session, and how to do bring
up work by hand when it runs out of ideas. Read that first if you want to help 
rather than just submit. It has a warning screen that you should take note of.

## How to send a Report

1. In Ultra Loader, go to the near bottom, and turn on **Advanced Features**
2. Then run the game at least TWICE so there is something to compare against
3. Hit **Generate Report**, then click on **Send it** and it will send to me.

That's it. You don't need a GitHub account or know how to code or anything.

## Please read before re-testing a game

Search the issues for your game first. If someone has already tried the obvious
things, their report says so, and the point of this repository is that nobody
has to cover the same ground twice. We want to work both efficiently and fast.

## What is in a report, and what is not

**Not In Report:** your Windows username and identifying data. We will never need this.
Any tool that springs up that asks for that is a scam! Don't use it and delete it!

**In The Report:** I will need at the bare minimum, your game folder paths, so the layout
of your game library is visible. All reports are always public. If that matters to you, 
please do not use the "Send" button. The tiny report file is also written to your Desktop,
so either way and you can share it privately by way of PM on Discord or through other ways.

**Never included:** Any part of any game file. Reports contain logs and .ini settings only.
