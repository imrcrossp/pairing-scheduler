# Pairing Scheduler

A doubles rotation scheduler for a recurring one-court session. Give it a roster
and a number of rounds; it generates the whole schedule at once — each round puts
four people on court as two opposing pairs and leaves the rest resting.

**Live: https://imrcrossp.github.io/pairing-scheduler/**

## Usage

Open `index.html` in a browser. No build step, no dependencies, no server.

- **Roster** — one name per line. At least 4 people; no upper limit.
- **Too-strong pairs** — optional. Pick two people and add them to the list to
  keep them off the same team. Leaving the list empty means anyone may pair with
  anyone.
- **Rounds** — 1 to 100.

Results are two tables: the schedule (both teams plus who is resting each round,
with a note on any round where a rule had to be bent) and per-person statistics
(games played, longest rest streak, back-to-back games).

Nothing is persisted — reloading starts clean.

## Rules, in priority order

1. Anyone who has rested 2 consecutive rounds **must** play — hard, never relaxed.
2. A too-strong pair must not be teammates — soft, relaxed last.
3. Anyone who played last round does not play — soft, relaxed first.
4. Keep total games played even across the roster.
5. Avoid repeating the same teammate pair.
6. Avoid repeating the same opponent pair — tie-break for rule 5 only.

With exactly 8 people, rule 3 would lock the roster into two fixed groups that
alternate forever. So on rounds 4, 7, 10, … one person from the previous round
plays again and one person who would have played rests instead; that person has
then rested twice and rule 1 brings them back, mixing the two groups. The person
benched this way is whoever among last round's resters has been benched least.

Whenever someone must play back-to-back (planned or not), it goes to whoever has
played back-to-back the fewest times so far.

The schedule is built round by round: enumerate every legal way to fill the four
slots and split them into two pairs, score each with a lexicographic cost vector,
take the minimum, break ties randomly.

Games come out evenest when the round count is a multiple of `roster / 4` — with
10 people, every multiple of 5 rounds gives everyone exactly the same number of
games.
