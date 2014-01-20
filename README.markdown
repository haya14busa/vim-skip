Vim Skip
========
Intro
-----

As a "mid-to-long range" movement, plugins like
[EasyMotion](https://github.com/Lokaltog/vim-easymotion),
[Stupid-EasyMotion](https://github.com/joequery/Stupid-EasyMotion) and
[Sneak](https://github.com/justinmk/vim-sneak) have created "find based"
motions, i.e. you first initialize the movement, then you are prompted for your
destination (similar to ``f``, ``F``, ``t``, ``T``, ``/``).  After which, in
Sneak's case, you use another key to continue the motion, or restart the process
all together.  These are fantastic plugins, but do not fit my personality.  Thinking
too much up front really messes up my chi.

Vim-skip attempts to provide a "mid-range" movement, between the longer ``$``,
``0`` and the shorter ``w``, ``W``, ``e``, ``E``, ``b``, ``B``, in a more
similar style, i.e. a single key press movement that is repeatable by pressing
the same key.  It is meant to move across lines *quickly*, be more accurate than
``$``, ``0`` while not stepping on the toes of ``w``, ``W``, ``e``, ``E``,
``b``, ``B``.  These "smaller" motions are *necessary* and vim-skip *does not
want to* replace them.  Instead, Vim-skip hopes to replace motions such as
``WWWwww``, ``Ww5w`` and ``$BBww`` with 1-2 quick key strokes.  As such it may
not be *as precise* as the aforementioned plugins, but with a little
experimenting and tweaking, you will see the speed benefit!

Though there are multiple options and modes, Vim-skip *does not attempt to
provide* you many mid-range motions!  Rather, it strives to provide a minimal
tool set allowing the user to tailor a single, *efficient* mid-range motion
based on their own needs.  Don't get overwhelmed with the modes or options!  It
is my intention to keep this skipping motion *quick* and *efficient*, while
still providing enough versatility to fit your working style on many file types.

Installation
------------

Using your favorite package manager, for example see
[Vundle](https://github.com/gmarik/vundle), or copy ``/doc/*`` to
``$HOME/.vim/doc/`` and likewise for ``/plugin``.

The "Line"
----------

Vim-skip moves the cursor based on the current (and sometimes the next/prev)
"line's" length.  The variables ``g:vimskip_ignore_initial_ws`` and
``g:vimskip_ignore_trailing_ws`` define the "line" based on your preference.
All future refernces to the "line" will take into account your settings of these
variables.

```
a)      |--------------------------------------------|            # = non-whitespace character
b) |-------------------------------------------------|            _ = whitespace
c)      |--------------------------------------------------|
d) |____#__#######_##_#########_##################_###_____| <---- physical line in buffer


    g:vimskip_ignore_initial_ws |  g:vimskip_ignore_trailing_ws
  +------------------------------------------------------------
a |             1               |             1
b |             0               |             1
c |             1               |             0
d |             0               |             0
```

Binds (mappings)
----------------

The way you "skip" forward or backward depends on your mode (see below) and the
options you have set.

* ``s`` - skip forward
* ``S`` - skip backward
* ``gs`` - skip to center of the line

Modes
=====

The distance your cursor skips always (except in fixed mode) depends on its
position in the line.  Fractions will always represent the *actual* position of
the cursor in the line as a fraction of the line's length, e.g. ``0``, ``1/2``
and ``1`` mean the cursor is at the beginning, middle and end of the line
respectively.  To give examples of how sequences like ``sS`` behave, I'll write
``0 --s--> 1/2 --S--> 1/4`` to mean "first press ``s`` then ``S`` move your
cursor *from* the beginning of the line, *to* the middle of the line, *then* to
(the position ) ``1/4`` (of the line's length from the beginning) of the line.
Of course, you could as well stop after the first ``s`` to be at the middle of
the line.  These examples just showcase how ``s`` and ``S`` get you places.

This is a lot of talking for some movements that are quite simple.  So lets get
started.

* ``gs`` - always moves your cursor to the center of the line, aka ``1/2``

The behavior of wrapping to beginning/end of the line and the "skip multiplier"
are customizable.

Normal
------

**The default mode**.  This mode works well for getting the cursor across the
line quickly and slows down near the beginning/end of the line.

* ``s`` - skips *forward* half (default, can be changed.  Applicable for every
  mode) the distance between itself and the end of the line.
* ``S`` - skips *backward* half the distance between itself and the
  end of the line.

**Examples**
* ``0 --s--> 1/2 --s--> 3/4``
* ``0 --s--> 1/2 --S--> 1/4``
* ``1 --S--> 1/2 --s--> 3/4``
* ``anywhere --gs--> 1/2 --s--> 3/4``
* ``1 --s--> 0`` and ``0 --S--> 1``, so that jumping forward (resp. backward)
  from the end (resp. beginning) of the line wraps to the beginning (resp. end)
  of the line.

Split
-----

This mode is like applying Normal mode to the first and second halves of the
line *separately*.  This mode is best for files with *long* lines coupled with
``$``, ``0`` to get you from the first half of the line to the second half
quickly.

* ``s`` - skips forward half the distance to the:
    * center, when the cursor is in the first half of the line
    * end of the line, when the cursor is in the second of the line
* ``S`` - skip backward half the distance to:
    * center, when the cursor is in the second half of the line
    * beginning of the line, when the cursor is in the first half of the line

The wrapping behavior (skipping forward/backward and the beginning/end of
line/center) is customizable.

**Examples**
* ``0 --s--> 1/4 --s--> 3/8``
* ``0 --s--> 1/4 --S--> 1/8``
* ``1 --S--> 3/4 --S--> 5/8``
* ``1 --S--> 3/4 --s--> 7/8``
* ``anywhere --gs--> 1/2 --s--> 3/4``

By default, tapping ``s`` from the beginning of the line will eventually allow
you to "pass through" to the second half of the line (likewise for ``S`` moving
from the second half).  This is customizable, however, this would take more
``s`` presses than one would want to.  If it is your goal to get to the center,
simply ``gs``, or to the second half of the line ``gss`` or ``$s``.

Fixed
-----

The simplest of all the modes.  The amount the cursor moves depends only on the
length of the line, not the cursor position within the line.  When using this
mode, be sure to adjust ``g:vimskip_multiplier``.

* ``s`` - skip cursor forward ``(line length) * (g:vimskip_multiplier)``.  The
  variable ``g:vimskip_multipler=0.5`` by default (useful for other modes) which
  is not helpul here.  Lower values are more appropriate.
* ``S`` - skip cursor backward ``(line length) * (g:vimskip_multiplier)``.

**Examples** (when ``g:vimskip_multiplier=0.2``)
* ``0 --s--> 1/5 --s--> 2/5`` etc.
* ``1 --S--> 4/5 --S--> 3/5`` etc.
* ``anywhere --gs--> 1/2 --s--> 7/10``

Anti(podal)
-----------

This mode moves the cursor through the ends of the lines *quickly* and slows
near the center.  It receives its name from the following visual:

```
<> = ends of line
* = center of line          -->||<--             ---><---
                           /        \           /        \
 |<---*--->|      ---->   |          |   --->  |          |
 ^         ^               \        /           \        /
 |         |                 ---*--               --|*|-
 skip "slowly" toward these points in normal mode
```

So if you imagine joining the ends of the line, we get a circle.  Then ``s``
skips counter-clockwise and ``S`` clockwise.  In normal mode, both skip half the
arc length from their current position to the north pole.  In anti(podal) mode,
simply move point which we skip toward to the south pole (the antipodal point of
the circle) which is the center of the line.

* ``s`` - skip forward , wrap if necessary, half the distance between the cursor
  and the:
    * center, when the cursor is in the first half of the line
    * end of the line + (length of first half of line), when the cursor is in
      the second half of the line.
* ``S`` - skip backward, wrap if necessary, half the distance between the cursor
  and the:
    * center, when the cursor is in the second half of the line
    * beginning of the line + (length of second half of line), when the cursor
      lies in the first half of the line.

Both skips are best understood using the circle picture above still noting that
``s`` skips counter-clockwise, ``S`` clockwise and that skips move half the arc
length between the current position and the south pole.

**Examples**
* ``0 --s--> 1/4 --s--> 3/8``
* ``1 --S--> 3/4 --S--> 5/8``
* ``1/4 --S--> 7/8 --S--> 11/16 (= 1/2 + 3/8)``
* ``anywhere --gs--> 1/2 --s--> 1``
* ``anywhere --gs--> 1/2 --S--> 0``

This mode may seem awkward, but it has its advantages.  In normal mode, the
cursor jumps across the center quickly,  and becomes more accurate near the
ends.  But one could argue that "around the center" is where a mid-range motion
should spend its time.  Split mode solves the "center" problem, but movement is
slowed due to the line being "chopped in half".

Anti mode, on the other hand, has a hard time getting to the ends of the line
(unless you commonly start from ``1/2`` and keep ``g:vimskip_multiplier=0.5``)
where we already have ``$`` and ``0`` at our disposal.  Find a sweet spot for
``g:vimskip_multiplier`` in this mode could make it the most efficient of the
modes.

Options
=======
* ``g:vimskip_disable_default_maps``: set to 1 to not use the default key
  mappings (``s``, ``S``, ``gs``).  Default 0.
* ``g:vimskip_multiplier``: (float) between 0 and 1.  Determines the fraction of
  a distance to skip. Values greater than ``1`` were not intended to be used, 
  though they are not restricted.  You currently cannot skip past the succeeding line
  when ``g:vimskip_helix`` is active.  Default ``0.5``.
* ``g:vimskip_mode``: one of "normal", "split", "fixed" or "anti".  Default
  "normal".
* ``g:vimskip_wraptocenter``: Set to 1 to wrap to the center of line rather than
  beginning (resp. end) of line when skipping forward (resp. backward) through
  the end (resp. beginning) of line.  Default 0.
* ``g:vimskip_split_passthroughcenter``: (for split mode)  Set to 0 to keep the
  cursor from passing through the center by repeatedly tapping ``s`` or ``S``.
  Default 1.
* ``g:vimskip_helix``: Set to 1 to move down (resp. up) a line when skipping
  forward (resp. backward) through the beginning (resp. end) of a line.
  *Should* work in conjunction with ``g:vimskip_wraptocenter``, though there
  might be unexpected behavior (especially with short lines).  Default 0.
* ``g:vimskip_ignore_initial_ws``: Set to 0 to allow skipping into whitespace
  preceeding all non-whitespace characters on the line (usually an indent).
  Default 1.
* ``g:vimskip_ignore_trailing_ws``: Set to 0 to allow jumping into trailing
  whitespace.  Default 1.

Other things you can bind
-------------------------
Map these using, e.g. ``nmap <Plug>SwitchMode <F1>``.
* ``<Plug>SwitchMode``: Will cycle you through the different modes.
* ``<Plug>IncreaseMultiplier``: Increase ``g:vimskip_multiplier`` by ``0.05``.
* ``<Plug>DecreaseMultiplier``: Decrease ``g:vimskip_multiplier``by ``0.05``.
* ``VSMultiplier(multiplier)``: Call this from the command line to set ``g:vimskip_multipler`` to the value given by ``multiplier``.

These can help find a good multiplier to fit what "mid-range" means to YOU.  Also, as different file types can have very different layouts, if you so choose, you can switch multipliers/modes to best fit your situation!


Outro
=====

A special thanks to the nice people at #vim for answering my stupid questions.  Especially accolade, Raimondi and sakkemo!

Constructive feedback would be great!  This is my first vim script and I am a
newbie to both vim and programming in general.  It is my intent for this plugin
to provide a new way of thinking about motions, not necessarly to showcase my 
technical prowess.  If nothing else, I hope some vim afficianados take a look 
this and go on to create something very clever!

Please take a look at the code, if you so desire, and let me know of optimizations.  There are currently no comments, but it is written to be readable (since optimal is
out of my reach!). =)

Changelog
=========
* **V 0.1** (1/20/2014): Initial working commit.
    * ToDo:
        * Comment the script.
        * Make vim help doc different from this readme.
        * Allow different multipliers for skipping forwards and backwards.
          Easy, will do if requested.
        * Due to rounding, skipping "at center" is unpredictable.  Try to clean
          up this up.






