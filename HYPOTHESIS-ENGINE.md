# The Hypothesis Engine

A Guide to WHAT it is, How it THINKS, and How to USE it to get a game running.

---

## Why it exists

Several games have been brought up by hand. This was alot of work to do, especially
in the method Ultra Loader does, which is purely native arcade execution. Around six
hundred other game firmware modules are packaged and untested, and one person simply
cannot run them all.

In truth, the work of getting one arcade game running is unglamorous and indeed highly
repetitive. You launch a game. It fails. You read the log. You recognize the failure.
You try the setting that has fixed that failure before. You look at what changed. Then
you try the next thing. Most of a bring up session is not insight, it is that single 
loop, run twenty times. Over and over. Again and again. Sometimes big strides are made.
Sometimes little improvements are found. So in order to get all these games running in
Ultra Loader at a rapid pace, the only way, is to accelerate the bring up loop.

The "Hypothesis Engine" is that loop. It is fully written down, so anyone with a copy
of a game can run it. It knows what has gone wrong on this project before, what fixed
it, and how to tell whether an attempt helped.

It will not replace a real person. It will do the first two hours of a person's work
in fifteen minutes, and it writes down everything it tried, which is the part that
normally disappears the moment someone closes a window. This is what we need.

---

## The one idea that makes it work

**The engine never asks "does the game work?" It asks "did this attempt get further than
the last one?"**

That distinction is the whole design, and it exists because the first question
cannot be answered by software.

A game can exit with code 0, which is the code that means that a game "finished
booting and playing normally", while in reality, it didn't boot, and showed a white
screen and then crashed. Another family of games may crash at the first frame in an
unattended run *by design*, and plays perfectly when a person is watching. Any tool
that trusts an exit code will confidently tell you a dead game is working, and this
would be extremely bad at six hundred titles, and it could do so, even at scale.

"However, "did it get further?" is more answerable, because every board announces 
the stages it reaches, and this is what we can work with to ensure we move along in
the game booting process to render and gameplay:

```
launched  ->  module accepted  ->  hook injected  ->  graphics device created
          ->  presenting frames  ->  stayed up  ->  controls reaching the game
```

A run cannot present a frame without having created a device, and it cannot create
a device without the hook being inside the game. So the ladder itself is honest: it
climbs a ladder step by step, and will mark if something real improved, even when 
nobody yet knows it, and this determines whether the game is playable.

Please keep in mind: **You are the one who can see the screen.** The engine can only
measure progress; but you must judge playability. If you will participate in this new
community project, you will need to be comfortable with taking time to test, over and
over again. I built the engine to be smart, so it wont keep asking you over and over
about things that it knows isnt working, and it will only ask you at the point where
things starts to matter, because asking you over and over "did it play?" for a game 
that never opened a window wastes your time, which is better spent on testing games.

---

## What it is allowed to change

You can choose the scope of the engine at the start of every bring-up session.

**Container only.** Settings and in memory patches, written into a scratch layer
inside the game's container file. Your game files are never touched and your
machine is also never touched. This is completely reversible: the layer is fully
cleared when you stop using the engine. Always.

**+Plus the game folder.** Also allowed to create files the game is asking for
and not finding. This reaches a class of failure the firmware module container
cannot solve. Every file created is recorded in a plain text journal inside of
that folder, and there is an "undo" function.

**+Plus display and OS.** Also allowed to change resolution, colour depth and
compatibility settings per attempt. This reaches the sixteen bit fullscreen
class of failure, where a 2006 cabinet asks for a display mode modern Windows
simply refuses. That is a real and common wall.

### What the Golden Layer is and Why the Golden layer is never touched

Every game container has two configuration layers.

The **golden** or bottom layer is protected and signed, and this is what the game
will boot from. This is the bottom layer of every firmware module you download from
here or on the site. 

Then we have the **top** layer, which is unsigned, and the loader only reads it when
a container has been explicitly put into test mode.

The engine writes only to the top layer. That is why it can afford to be aggressive
and test different configurations without fear of loss: nothing it does can affect a
normal launch of this game or any other, and a session that ends badly leaves the full
shipping configuration exactly as it was when you downloaded it.

It also means a trial configuration can be written with no signing key at all,
which is the reason none is needed on your machine. This is a most elegant solution
to allow the community to participate in, and do game bring-up work while not ever
needing to learn how to code or tinker with files outside of loading your dumps.

---

## Before you start: I advise you to use a SPARE machine or a non-production PC

**The Hypothesis Engine is a full game debugging tool, not a feature set.**

At full scope, it launches your game repeatedly, kills it each time, and changes
your video mode between attempts. A game killed mid-frame can leave your desktop
in that corrupted display mode. That can render a taskbar or start menu unusable.
The engine does attempt to restore your display after every attempt, including the
ones it aborted and the ones that crashed, and it recovers on the next start if it
was killed outright. The engine will be careful. However, it is still not something
to run on the PC you need working at all time, as you may be forced to reboot.

To be fair, you CAN run this alongside your current workloads, and I currently also
do exactly this work on my production PC,but I wanted to offer fair warning to those
who way notice changes happening to their PC and withoutwarning, may think something
is going wrong with the loader. Thus I ask that you only use the engine if you fully
understand and agree to contributing to the project and submitting reports and doing
actual game bring-up work.

**At best, I think that a virtual machine or a spare PC is the right place for this.
However, this is OPTIONAL, and totally up to you. If you don't really care, you can
run the Hypothesis Engine on your PC just fine.**

---

## Running an Engine session

### 1. Select your game

The engine works on whatever game is selected in the Game section. It finds that
game's container by itself. Browse for the game you want to work on and select the
game's .exe, but then do not hit "Save & Launch". Leave the path there as is.

*If it says no container covers this game, that is worth reporting on its own. A
missing container usually means the game's folder name does not match what the
container is looking for, and that is something only the internal project can fix.

### 2. Turn on Advanced Features in the Loader

Do this by hitting the "Advanced Features" checkbox in the loader. A few new buttons
will appear. You should see "Hypothesis Engine" there. Pleas READ the warning box!

### 3. Press Hypothesis Engine

Once you read the warning, you should then see the scope choice, then the opening.

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

**Fixes accumulate.** This matters more than it sounds. A game may need four or
fix different fixes before it can run, and any one of them may not look like
progress on its own. Therefore, every attempt the engine makes, is the best known
configuration for the game plus one or two new ideas added on.

When an attempt gets far enough to draw or render something, the engine hands will
then hand the decision over to you, because you can see the screen and it cannot.

### 6. When it runs out of things to try:

It says so plainly, rather than spinning and wasting time:

```
"I have run out of ideas for this game.
The furthest anything got was: graphics device created.
That is not a failure of the game. It is the edge of what I know."
```

Then it clears the scratch layer, restores your display, and offers to undo any
files it created.

**Please keep in mind: A session that found nothing is still worth reporting.** 

See below.

---

## Reading a report

After doing the Hypothesis Engine work, you may then press "Generate Report".
You may do this regardless of using the engine, but the report is that much
more valuable after running it, as it contains valuable bring-up work that can
be used to get the game working later. Once it generates the report, you will
get a zip. This is what is in it:

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

Ruling things out is a large part of bring up work. It is also the part that most
people take for granted, as it shows that "nothing changed" and nobody bothers to
write down the six things they tried that did not help. A report full of negative
or unchanging results saves the next person those six attempts, and an unrecognized
failure is exactly what teaches the engine its next idea. So please, send reports!

### The append mode trap, and why runs are split

Game logs are append mode only. One file holds every launch you have ever done.

Attaching the whole log means that whoever reads it picks a run at random and
diagnoses the wrong one. That has happened countless times to me on this project:
an eleven run log made a working feature look broken, and I regressed items that
worked. I lost controls and gamepad settings many times over this.

So the report splits the log on its run boundaries and attaches only the last
few, with the total count stated so nothing looks hidden. If a run is very long
it keeps the **end**, because the end is where it died.

### Why am I asking people to run the game at LEAST twice

One run cannot distinguish "fails every time this setting is placed" from "failed once"
and those are different problems with different fixes.

Some titles genuinely behave differently on a second launch, after logs and other game
related things are established and in place. Other games will need at least 2-3 failures
in order to correctly diagnose the issue.

The report says which of the attached runs ended the same way and which of them did not.
That single line is often the most useful thing in the file.

---

## What the engine knows

Almost 50 diagnostic rules, each keyed to a string that appears in a real log,
and each one is paired with a change that fixed that failure before.

Forty five (40+) boards are covered, with thirty five of them including a recipe
of what games on that board usually need.

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

**Crashes that are ours, not the game's.** Sometimes, the main culprit could be
the loader rather than the title: a module list read while a file was unloading,
an observe only tracer inventing failures, a patching loop briefly removing the
"may run" permission from memory pages. The engine will then turn much of its own
machinery off before blaming the game.

### Why it refuses to start if its own knowledge is broken

The engine was built to test and bring-up games. If the knowledge itself is broken,
then it cannot help. Every setting the engine can proposes is checked, both at start
up, and against the list of settings the loader actually reads.

So a failed self-check is a refusal to start, not a warning.

---

## Doing bring up work by hand

The engine, while containing mostly all of what this project has learned, is still
limited, and has limits in terms of what it can do. So it may sometimes be necessary
to do this work by hand. If you prefer to go beyond the loader and engine, here is
what you should do:

### Get a log first

A run that leaves no evidence has wasted the launch. Every container sets a log
path for exactly this reason.

### Work out how far it got, not whether it worked

Use the ladder. The question is never "is it broken", it is "what is the last
thing that succeeded, and what was supposed to happen next". This will guide you
to much faster bring-ups for many titles. It is the method I use to get games to
render and work, rather than doing a deep dive every time into the game's code.

### Read the last run only

The log may hold many runs. Find the boundary and read from there. Avoid old runs
if possible, especially if you changed something, as you will need to test to see
if the change worked, which will produce a new run, and therefore, new log lines.

### Suspect your own tools before the game

This is the single most valuable habit I do on this project. A large share of
failures I blamed on a game turned out to be the loader. A crash in my own game
patcher, or a tracer inventing an error that was never raised, or perhaps stale
files being loaded instead of the one just built. Be sure to turn all optional
machinery off and run the game again. If it survives, the cause was the tools.

### Change one thing, then look

This is tedious, but it is the only way. Do a run. Then see if it fails. Then go
ahead and change one more thing. Then repeat. An attempt that changes three settings
at once, and shows improvement is fine, but you lost something in the process that
could have taught you something about a game's mechanism, but this now lost unless
you redo, of which most won't bother. If you do things this way, you won't be able
to tell which setting mattered, and the next person inherits that uncertainty.

### Prefer the least invasive fix first!

In order:

1. Configure it honestly. If the game wants something, try to avoid a bypass.
2. Assert the flag the missing hardware would have set if unavoidable.
3. Retarget a branch, keeping the instruction length identical if possible.
4. Then if it shows an unavoidable error, suppress the error being raised.
5. Then force a success path. This is last resort, some games will never be
   perfectly happy, so you may have no choice but to let it complain a little bit,
   as long as it doesnt hinder gameplay.

### See what shows on screen

Do not infer it from the log. The log may show a game complaining with errors
while it runs just fine in practice. Check the logs, but see what the live game
run does. If the game renders and works, you can safely ignore the log.

---

## Frequently hit walls

**Antivirus blocking the loader.** The loader starts a game frozen, injects
into it, then unfreezes it. Some behavior shields may kill that silently and
it may present as a timeout rather than an error. The tell is a log file that
exists and is completely empty. Your antivirus might have stifled the file.

**The wrong file.** Pointing the loader at a `.bat` launcher in a dump instead
of the game's actual program executable makes it stop before the board module 
even loads. You will mostly get nothing at all, which looks like a much deeper
problem than what it is. The loader will only run true executables, so .bat
files are currently not supported.

**A dump that's missing files.** The log names the exact path it could not
open. This is the cheapest finding available and it is often the whole answer.

**Encrypted content.** Several boards keep their games inside an encrypted
volume unlocked by a chip in the cabinet. No setting can reach this. If that
happens, the engine tells you up front if the board has one of them.

---

## In short

Run the game twice. Then enable "Advanced Features" and then enable and run the
"Hypothesis Engine". Read the opening, because it usually contains the answer,
or the reason there isn't one. Work through the ideas. Then generate and send the
report whether it worked or not. All negative results are still highly valuable.
