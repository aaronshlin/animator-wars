ANIMATOR WARS
=============

Animator Wars is a single-player artillery game built into Animator. You aim
by writing code in the same Gleam-like language you use for drawings, and the
app's own parser and evaluator run it. Bots (who shoot back) and dummies (who
don't) wait on the other side of a field full of rock.


GETTING IN AND OUT
------------------
- A gold "⚔ PLAY ANIMATOR WARS" button sits next to the Animator title. It
  glows softly and a shine sweeps across it every few seconds.
- Clicking it plays a full-screen "Welcome to ANIMATOR WARS!" title that fades
  in and back out (click to skip).
- The game reuses Animator's layout and look. The left sidebar becomes the
  shot editor, the canvas becomes the battlefield (resized to 5:3), and the
  bottom control panel becomes a status panel. A "⟵ Animator" button returns
  to the drawing app, which keeps its code and animation.


WRITING A SHOT
--------------
- A shot is a program that defines   fn shot() -> List(Point)
  The shot starts at your soldier, flies to each Point in order, then keeps
  going straight in the direction of its last leg.
- Points use the Animator canvas's coordinates: x from -500 to 500, y from
  -300 to 300, with y growing downward. The field has a labeled grid, hovering
  over it shows coordinates, and a Battlefield list gives every soldier's
  position.
- Any part of the language works: let, case, abs, lists, helper functions, and
  recursion to build long lists of Points.
- The program is checked as you type: parse errors, a shot that returns
  something other than a List(Point), an empty list, or points that never
  leave your soldier are reported in plain English.
- When the code is valid, a dashed preview line shows the path: brighter for
  the part the shot will really fly (until it hits rock or leaves the field),
  fainter for the rest. Your Points are marked as numbered circles.
- "Start from" buttons fill in example programs: Line (one point), Bend (two
  points over the rocks), and Arc (recursion builds 12 points).
- Fire with the Fire button or Cmd/Ctrl+Enter. Tab indents.
- No repeats: within a round you can't fire the same path twice. The check
  compares where the shot goes, not the text, so reformatting, renaming, or
  adding an extra point on the same straight line doesn't count as new.


THE BATTLE
----------
- Shots pass through soldiers (50 damage each) and stop at rock, blasting a
  crater that changes the terrain and does 20 splash damage nearby. A shot also
  ends if it leaves the field or flies more than 2600 units.
- You have 200 HP; every bot and dummy has 100.
- Turns go you, then each living bot. While a bot thinks, a banner says
  "<bot> is calculating...".
- Shots animate across the canvas with a glowing head, sparks on hits,
  explosions, floating damage numbers, and faded trails of earlier shots.
- Every round has a new random battlefield: blobs of rock, pillars rising from
  the floor or hanging from the ceiling, and new positions for everyone. A
  layout is only used if there is a shot that can reach every enemy, so every
  round is playable.
- Defeating every bot wins the round. Dummies are optional target practice.


THE BOTS
--------
Bots write real programs in the same language (a list of Points), which run
through the same interpreter. You can read them in the history and reuse them.
- Rookie (easy): bad at math. Fires straight at you without checking the rock
  and misjudges your height by up to 60%. Hits almost never.
- Dodger (medium): plans one waypoint, tries 40 plans against the terrain in a
  dry-run simulation, and fires the best, with some aiming error.
- Sniper (hard): plans up to two waypoints and tries up to 220 plans, avoiding
  its teammates and its own blast. The most accurate.
- Difficulty is set in the sidebar: Auto (Rookies in rounds 1-2, Dodgers in
  3-4, Snipers from 5 on), or a fixed Easy, Medium, or Hard. Later rounds bring
  two bots, then three.


HELPER AND HINTS
----------------
- The ✨ Helper button writes a new kind of curve program for you each time,
  never repeating a kind in a game. It knows 12 kinds, each showing off part of
  the language: parabola, cubic, step (case), stairs (recursion), bump and
  V shape (abs), plateau, sawtooth and zigzag (%), ramp, late dive, and
  doubling (recursion, like an exponential). After all 12, it combines them in
  pairs, then triples.
- Helper's program builds the curve with a recursive path function, explains
  the idea in comments, and is aimed near a bot (never a dummy) but
  deliberately off by a little, so you still have to fine-tune it. It
  simulates each candidate against the real terrain and only offers one that
  passes within 100 units of the bot. If rock blocks everything, it says so.
- A hint box suggests pressing Helper when you're stuck. It reacts to how you
  are doing: how far your last shot missed, a glow after a miss, a count of
  misses in a row, and a reminder about no repeats after a hit.


THE STATUS PANEL
----------------
The bottom panel shows:
- HP bars for every soldier.
- The last attack: who fired, whether it was a direct hit, otherwise the
  distance and direction (an arrow plus "above/below, left/right") of its
  closest pass to the nearest opponent, and what ended it.
- Code history: every shot from you and the bots, grouped by round, with its
  result. Click one to load its code into the editor.


WINNING AND LOSING
------------------
- Killing the last bot plays a full-screen "You WIN!" (with confetti). It then
  fades to a Victory card showing shots fired, accuracy, damage dealt, HP left,
  Helper uses, and time.
- Dying plays a red "You LOSE!" naming who got you, then a Defeated card with
  the same stats.
- Next Round continues to the next round. Try again replays the same round on
  a new layout. New game moves on to the next round number with a fresh score,
  code history, and Helper.


HOW IT'S BUILT
--------------
- src/graphwar.mjs: the game. It contains a terrain bitmask with craters, a
  headless trajectory simulator that the bots, Helper, the preview, and the
  level generator all reuse for dry runs, the bot and Helper code writers,
  and the renderer.
- src/war_code.gleam: runs a shot program through the app's own
  parser.parse_program and evaluator.run, and checks that it returns a
  List(Point).
- src/canvas.mjs: gained an overlay hook so the game draws on the same
  #animation-canvas.
- src/app.gleam and src/editor.gleam: the button, a game_open flag, and empty
  containers the game fills in for the sidebar, bottom panel, and win/lose
  cards.
- animator.css: the game's styles, in the same palette as Animator.
