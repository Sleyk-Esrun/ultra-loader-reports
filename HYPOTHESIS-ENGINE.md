# The Hypothesis Engine

A guide to what it is, how it thinks, and how to use it to get a game running.

---

## Why it exists

Thirteen games have been brought up by hand. Around six hundred are packaged and
untested, and one person cannot run them all.

The work of getting one arcade game running is unglamorous and highly
repetitive. You read the log. You recognise the failure. You try the setting
that has fixed that failure before. You look at what changed. You try the next
thing. Most of a bring up session is not insight, it is that loop, run twenty
times.

The Hypothesis Engine is that loop, written down, so anyone with a copy of a
game can run it. It knows what has gone wrong on this project before, what fixed
it, and how to tell whether an attempt helped.

It will not replace a person. It will do the first two hours of a person's work
in fifteen minutes, and it writes down everything it tried, which is the part
that normally disappears the moment someone closes a window.

---

## The one idea that makes it work

**It never asks "does the game work?" It asks "did this attempt get further than
the last one?"**

That distinction is the whole design, and it exists because the first question
cannot be answered by software.

One game on this project exits with code 0, the code that means "finished
normally", while showing a white screen and then crashing. Another family of
games crashes at the first frame in an unattended run *by design*, and plays
perfectly when a person is watching. Any tool that trusts an exit code will
confidently tell you a dead game is working, and at six hundred titles it will
do so at scale.

"Did it get further?" is answerable, because every board announces the stages it
reaches:

```
launched  ->  module accepted  ->  hook injected  ->  graphics device created
          ->  presenting frames  ->  stayed up  ->  controls reaching the game
```

A run cannot present a frame without having created a device, and cannot create
a device without the hook being inside the game. So the ladder is honest: climb
a rung and something real improved, even when nobody yet knows whether the game
is playable.

**You are the one who can see the screen.** The engine measures progress; you
judge playability. It asks you at the point where that starts to matter, and not
before, because asking "did it play?" about a game that never opened a window
wastes the one thing the engine cannot do for itself.

### Our lines versus the game's lines

A related rule, and the engine got this wrong once before it was fixed.

A log line like `COINEDGE: pad0 SELECT down` looks like proof that a control
reached the game. It is not. It is the loader saying *it* saw a button press,
and it prints whether or not the game is listening, has a window, or is even
still running.

An early version counted that as progress and reported a game that was black
screening and quitting as having reached the top of the ladder. That was the
worst answer available, because it also filtered out every idea that might have
helped.

**A rung can only be claimed by a line the game caused.** Anything the loader
announces about itself is evidence, never progress.

---

## What it is allowed to change

You choose the scope at the start of every session.

**Container only.** Settings and in memory patches, written into a scratch layer
inside the game's container file. Your game files are never touched and your
machine is never touched. Completely reversible: the layer is cleared when you
stop.

**Plus the game folder.** Also allowed to create files the game is asking for
and not finding. This reaches a class of failure the container cannot. One game
on this project was fully working except for a single missing data table, and
the log named the exact path. Every file created is recorded in a plain text
journal inside that folder, and there is an Undo.

**Plus display and OS.** Also allowed to change resolution, colour depth and
compatibility settings per attempt. This reaches the sixteen bit fullscreen
class of failure, where a 2006 cabinet asks for a display mode modern Windows
simply refuses. That is a real and common wall.

### Why the golden layer is never touched

Every game container has two configuration layers.

The **golden** layer is signed and is what the game normally boots from. The
**top** layer is unsigned, and the loader only reads it when a container has
been explicitly put into test mode.

The engine writes only to the top layer. That is why it can afford to be
aggressive: nothing it does can affect a normal launch of this game or any
other, and a session that ends badly leaves the shipping configuration exactly
as it was.

It also means a trial configuration can be written with no signing key at all,
which is the reason none is needed on your machine.

---

## Before you start: use a spare machine

**This is debugging tooling, not a feature.**

At full scope it launches your game repeatedly, kills it each time, and changes
your video mode between attempts. A game killed mid frame can leave your desktop
in its display mode. That has made a taskbar unusable on this project more than
once, and at twenty attempts it stops being a risk and becomes the expected
outcome.

The engine restores your display after every attempt, including ones it aborted
and ones that crashed, and it recovers on the next start if it was killed
outright. It is careful. It is still not something to run on the PC you need
working tomorrow.

**A virtual machine or a spare PC is the right place for this.**

---

## Running a session

### 1. Turn on Advanced Features

Settings, then the Advanced Features checkbox. The engine and the report button
appear.

### 2. Select your game

The engine works on whatever game is selected in the Game section. It finds that
game's container by itself.

If it says no container covers this game, that is worth reporting on its own. A
missing container usually means the game's folder name does not match what the
container is looking for, and that is something only the project can fix.

### 3. Press Hypothesis Engine

You will see the warning, then the scope choice, then the opening.

### 4. Read the opening carefully

This is the most useful thing the engine says all session. It contains:

**What the container already knows about your game.** Every container records
the executable it expects and an input classification derived from that game's
own control list. Not from its genre, which is a real distinction: one shooter
on this project plays on a gamepad, so genre would have been wrong and "the
control list has aim axes in it" is not.

**What this board has taught us.** If someone has already got a game running on
this board, its lessons are handed to you before you launch anything. This is
the single biggest shortcut available. Two games solved on one board established
that its controller identity is per game and one number wide, that the report
length differs per game, and that two of our own test harnesses fight the game
unless switched off. A third game on that board starts with all of that already
applied rather than from nothing.

**An honest refusal, when one applies.** Some boards cannot run a game at all,
and not because anything is misconfigured. Several are locked by a physical
security chip whose key is derived inside the silicon and appears in no copy of
the game. One is bound to a specific machine's TPM, so even a perfect copy of
someone else's drive is unusable. One is a platform this project deliberately
does not target.

If your board is one of those, the engine says so at the start, names what would
actually be needed, and lets you decide. It will still run a session if you want
one, because the report is still worth having. It just will not let you spend
an evening discovering that on your own.

### 5. Work through the ideas

Each step tells you what it thinks is wrong, what it wants to change, and why.
You can apply it, skip it, or stop.

When you apply one, the game launches. It takes over your screen. When it
finishes, or when the engine stops it, you are told what changed:

```
That helped. It got further than before:

    was:  hook injected
    now:  graphics device created

I am KEEPING that change. It stays in force for everything I try from
here, because fixes usually stack.
```

**Fixes accumulate.** This matters more than it sounds. One game on this project
needed four separate fixes before it ran, and not one of them looked like
progress on its own. An engine that tested ideas one at a time and threw away
the winners could never have found that combination. Every attempt is the
running best known configuration plus one new idea.

When an attempt gets far enough to draw something, the engine hands the decision
to you, because you can see the screen and it cannot.

### 6. When it runs out

It says so plainly, rather than spinning:

```
I have run out of ideas for this game.

The furthest anything got was: graphics device created.

That is not a failure of the game. It is the edge of what I know.
```

Then it clears the scratch layer, restores your display, and offers to undo any
files it created.

**A session that found nothing is still worth reporting.** See below.

---

## Reading a report

Press Generate Report. You get a zip, and this is what is in it.

| file | what it is |
|---|---|
| `00-READ-THIS-FIRST.txt` | plain language summary |
| `01-report.json` | the same facts, machine readable |
| `02-what-the-engine-tried.txt` | **every attempt and what it changed** |
| `10-game-log-RUNS.txt` | the last few runs from the game's own log |
| `11-loader-log-RUNS.txt` | the matching runs from the loader's log |
| `20-settings.ini` | your loader settings |
| `30-container.txt` | which container and board were used |

`02` is numbered second on purpose. Anyone opening this should see what has
already been eliminated before they start eliminating it again.

### Why "nothing worked" is still worth sending

Every line in that file reading "no change" is a cause **ruled out**, on real
hardware, with a real copy of the game.

Ruling things out is most of bring up work. It is also the part that normally
evaporates, because nobody writes down the six things they tried that did not
help. A report full of negative results saves the next person those six
attempts, and an unrecognised failure is exactly what teaches the engine its
next idea.

### The append mode trap, and why runs are split

Game logs are append mode. One file holds every launch you have ever done.

Attaching the whole thing means whoever reads it picks a run at random and
diagnoses the wrong one. That has genuinely happened on this project: an eleven
run log made a working feature look broken.

So the report splits the log on its run boundaries and attaches only the last
few, with the total count stated so nothing looks hidden. If a run is very long
it keeps the **end**, because the end is where it died.

### Why you are asked to run the game twice

One run cannot distinguish "fails every time" from "failed once", and those are
different problems with different fixes.

Some titles genuinely behave differently on a second launch. One game on this
project scans for its controller exactly once, latches the result, and never
retries, so run one and run two are not the same experiment.

The report says which of the attached runs ended the same way and which did not.
That single line is often the most useful thing in the file.

---

## What the engine knows

Forty nine diagnostic rules, each keyed to a string that really appears in a
real log, each paired with a change that fixed that failure before.

Forty four boards covered: thirty five with a recipe of what games on that board
usually need, eleven with an honest wall.

Some examples of what it recognises:

**A game that quits tidily without ever drawing.** A clean exit is a game that
failed a check and decided not to run. It did not crash. On several boards the
usual gate is the network and authentication stack the cabinet would have had.

**Alive and pumping but never drew a frame.** The watchdog counts the game's own
frames five times a second, which separates three states an exit code cannot:
drawing, alive but never drew (the white screen signature), and wedged.

**Aim that works for a second at boot and then dies.** The game works out where
you are aiming inside its own small native picture, but the window was stretched
to fill the screen, so the pointer ends up outside the strip the game watches.
It looks exactly like dead controls and it is not. This is checked first on any
aimed game.

**Every analog control dead and staying dead.** If the input layer cannot tell
which board this is, it falls back to a generic profile with fourteen buttons
and zero axes, and every wheel, pedal and stick channel reads a fixed value
forever.

**Crashes that are ours, not the game's.** More than once the culprit has been
the loader rather than the title: a module list read while a file was unloading,
an observe only tracer inventing failures, a patching loop briefly removing the
"may run" permission from memory pages. The engine turns its own machinery off
before blaming your game.

### Why it refuses to start if its own knowledge is broken

Every setting the engine can propose is checked, at start up, against the list
of settings the loader actually reads.

This is not paranoia. The first version of the rule base proposed eight settings
that **nothing in the loader read at all**, and five of its thirteen rules
depended on them. Those would have been applied, changed nothing, and then been
reported as causes tested and ruled out.

That is worse than having no rule, because it converts "untested" into
"eliminated", and it looks exactly like a correct negative result. Nobody would
ever have caught it by reading the output.

So a failed self check is a refusal to start, not a warning.

---

## Doing bring up work by hand

The engine encodes what has already been learned. When it runs out, here is how
the learning happened.

### Get a log first

A run that leaves no evidence has wasted the launch. Every container sets a log
path for exactly this reason, and the very first run of a game on this project
froze a machine and left nothing behind because its container did not.

### Work out how far it got, not whether it worked

Use the ladder. The question is never "is it broken", it is "what is the last
thing that succeeded, and what was supposed to happen next".

### Read the last run only

The log holds many. Find the boundary, read from there.

### Suspect your own tools before the game

This is the single most valuable habit on this project. A large share of
failures blamed on a game turned out to be the loader: a crash in our own
patcher, a tracer inventing an error that was never raised, a stale file being
loaded instead of the one just built. Turn the optional machinery off and run
again. If it survives, the cause was ours.

### Change one thing

Then look. Then change one more thing. An attempt that changes three settings
and improves something has taught you almost nothing, because you cannot tell
which one mattered, and the next person inherits your uncertainty.

### Prefer the least invasive fix

In order:

1. Configure it honestly. If the game supports "this hardware is not fitted",
   say that.
2. Assert the flag the missing hardware would have set.
3. Retarget a branch, keeping the instruction length identical.
4. Suppress the error being raised.
5. Force a success path. Last resort, because if the success path sets an
   "available" flag while the thing is still absent, the game crashes the first
   time it uses it.

### Ask what is on screen

Do not infer it from the log. One game on this project was declared blocked by
four separate high confidence conclusions drawn from static analysis. It plays
fine. The log and the disassembly were both describing something real and both
answering a question nobody had asked.

---

## Frequently hit walls

**Antivirus blocking the loader.** The loader starts a game frozen, injects
into it, then unfreezes it. Some behaviour shields kill that silently and it
presents as a timeout rather than an error. The tell is a log file that exists
and is completely empty.

**The wrong file.** Pointing the loader at a `.bat` launcher instead of the
game's actual program makes it stop before the board module even loads. You get
nothing at all, which looks like a much deeper problem than it is.

**A dump that is missing files.** The log names the exact path it could not
open. This is the cheapest finding available and it is often the whole answer.

**Encrypted content.** Several boards keep their games inside an encrypted
volume unlocked by a chip in the cabinet. No setting reaches this. The engine
tells you up front when your board is one of them.

---

## In short

Run the game twice. Press Hypothesis Engine. Read the opening, because it
usually contains the answer or the reason there is not one. Work through the
ideas. Send the report whether it worked or not.

The negative results are not a consolation prize. They are most of the work.
