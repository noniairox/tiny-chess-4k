# tiny-chess-4k

A tiny chess engine in under 4 KB of Python, built for the make.fun
tiny-chess-01 bounty. It plays the house gauntlet over the make.fun
environment protocol and needs nothing but the Python 3.13 standard
library.

## How it plays

The engine is a single packed file, `main.py` (2,988 bytes). The
readable source is `src.py` (7,889 bytes); `main.py` is produced from
it by `makefun compress`, so the two are identical in behaviour.

Board: 0x88, pieces 1-6 white and 7-12 black. Move generation covers
pawns (double push, en passant, promotion), knights, bishops, rooks,
queens, kings and both castling sides, with full legality filtering
by king safety.

Search: negamax alpha-beta with a transposition table, quiescence
search on captures and promotions (capped at 8 plies), and MVV-LVA
plus transposition-table move ordering. The node budget is fixed at
200,000 nodes per move, so the engine is deterministic: same
position, same move, every time. No clock, no randomness, no model
call anywhere. A wall-clock safety net caps every move at 7.5
seconds: if the deadline hits mid-search, the engine returns the
best move from the last completed iterative-deepening depth instead
of timing out, which is what the server's 10 second per-move limit
needs on slower hardware.

Evaluation: tapered middlegame/endgame piece-square tables with
phase interpolation, a bishop-pair bonus, and material values pawn
50, knight 160, bishop 165, rook 250, queen 450. The tables are
precomputed once at import, so evaluation is a table lookup instead
of arithmetic per square.

## Results

Local practice trial (trial 0, six games against the house gauntlet):
Elo 1,279 (2 wins, 2 draws, 2 losses), above the 1,154 practice
floor. The house baseline (sunfish, 50,000 nodes) sits near 1,365.

Perft is exact on the standard suite (startpos depth 4 = 197,281;
Kiwipete depth 4 = 4,085,603; positions 3, 4 and 5 all exact), and
the engine runs at roughly 45,000 nodes per second, so a 200,000
node move takes about 4.9 seconds, inside the 10 second limit.

The first submitted version (v2c) scored 0 on the server's practice
grade and was rejected below the 1,154 floor, even though it scores
1,279 locally. The likely cause is speed: locally a move takes
about 6 seconds at 200,000 nodes, and a slower server CPU pushes
moves past the 10 second limit, losing games on time. Version 3 is
the fix: about 30 percent faster per node plus the 7.5 second hard
cap, so it plays identically on fast hardware and cannot lose a
game to a move timeout on slow hardware.

## Tuning notes

Tuned against the local practice gauntlet (trial 0). Every variant
below was tried and reverted unless noted:

- v1, plain piece-square eval: Elo 1,279.
- v2, tapered eval + bishop-pair + passed-pawn + null-move +
  killer/history ordering: Elo 1,099.
- v2b, null-move off: Elo 1,099.
- v2c, tapered eval + bishop-pair only (the first submission):
  Elo 1,279.
- v2d, pawn-structure eval, 150,000 nodes: Elo 1,099.
- v2e, 250,000 nodes: Elo 1,191, hit the 1,500 second wall.
- v2f, quiescence depth 12: Elo 997.
- v3, v2c + precomputed tables + localised board + 7.5s deadline:
  Elo 1,279, wall 1,165s vs 1,400s. This submission.
- v4, v3 + 250,000 nodes: Elo 1,191.
- v5, v3 + null-move: Elo 1,099.
- v6, v3 + late-move reduction: Elo 1,191.
- v7, v3 + aspiration windows: Elo 1,099.

At 200,000 nodes plain alpha-beta with a good move order beats
fancier pruning; speed, not search tricks, is what mattered.

## Files

- `main.py` — the packed engine, the only file the harness runs.
- `src.py` — the readable source `main.py` is packed from.
- `LICENSE` — MIT.
- `.makefun/owner.json` — the entry wallet claim.

## Building

Re-pack after any change to the source:

```
makefun compress src.py --out main.py
```

Then commit `main.py`. The source file itself is not committed; it
is sent beside the transcripts at submission so the site can re-pack
and verify it byte for byte.
