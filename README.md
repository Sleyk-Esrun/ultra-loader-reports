# Ultra Loader, bring-up reports

This repository collects **bring-up reports** from people testing arcade games
with Ultra Loader. It holds no code.

## What a report is

When a game does not work, the loader can package up what happened: the last few
runs from that game's log, which board and container were used, your loader
settings, and, if you used the Hypothesis Engine, **everything it already
tried and what each attempt changed**.

That last part is the valuable bit. Every line reading "no change" is a cause
ruled out, on real hardware, with a real copy of the game. Ruling things out is
most of bring-up work, and it is normally the part that evaporates the moment
someone closes a window.

## Learn how it works

[**The Hypothesis Engine**](HYPOTHESIS-ENGINE.md) explains what the engine is,
how it decides what to try, how to run a session, and how to do bring up work by
hand when it runs out of ideas. Read that first if you want to help rather than
just submit.

## How to send one

1. In Ultra Loader, turn on **Advanced Features**
2. Run the game at least twice so there is something to compare
3. **Generate Report**, then **Send it**

That is all. You do not need a GitHub account.

## Please read before re-testing a game

Search the issues for your game first. If someone has already tried the obvious
things, their report says so, and the point of this repository is that nobody
has to cover the same ground twice.

## What is in a report, and what is not

**Removed:** your Windows username.

**Not removed:** your folder paths, so the layout of your game library is
visible. Reports are public. If that matters to you, do not use Send, the file
is written to your Desktop either way and you can share it privately instead.

**Never included:** any part of a game. Reports contain logs and settings only.
