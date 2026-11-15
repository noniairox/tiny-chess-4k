# tiny-chess-4k

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A tiny chess engine in under 4 KB of Python for the make.fun tiny-chess-01 bounty. It plays the house gauntlet with a 0x88 board, negamax alpha-beta search and a fixed 200,000-node budget.

## Run it

```sh
MAKEFUN_ENV_URL=http://127.0.0.1:18080 python3 main.py
```

---

Entered on [make.fun](https://make.fun/?ref=pgd6qh00&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards

## How it plays

`main.py` (2,988 bytes) is the packed engine; `src.py` (7,889 bytes)
is the readable source it is packed from by `makefun compress`.
Board: 0x88, pieces 1-6 white, 7-12 black, with full move
generation (double push, en passant, promotion, both castling sides)
and legality filtering by king safety. Search: negamax alpha-beta
with a transposition table, quiescence search on captures and
promotions (capped at 8 plies), MVV-LVA and transposition-table move
ordering, at a fixed 200,000 nodes per move — deterministic, no
clock, no randomness, no model call. A 7.5 second wall-clock cap
returns the best move from the last completed depth instead of
timing out, which is what the server's 10 second per-move limit
needs on slower hardware. Eval: tapered piece-square tables with
phase interpolation, a bishop-pair bonus, material pawn 50, knight
160, bishop 165, rook 250, queen 450; the tables are precomputed at
import, so evaluation is a lookup.

## Results

Local practice trial (trial 0, six games vs the house gauntlet):
Elo 1,279 (2 wins, 2 draws, 2 losses), above the 1,154 practice
floor; the house baseline (sunfish, 50,000 nodes) sits near 1,365.
Perft is exact (startpos depth 4 = 197,281; Kiwipete depth 4 =
4,085,603; positions 3, 4 and 5 exact). At ~45,000 nodes per second
a 200,000 node move takes about 4.9 seconds, inside the limit.

The first submitted version (v2c) scored 0 on the server's practice
grade and was rejected below the floor, though it scores 1,279
locally. The likely cause is speed: locally a move takes about 6
seconds, and a slower server CPU pushes moves past the 10 second
limit. Version 3 is the fix: about 30 percent faster per node plus
the 7.5 second hard cap.

## Tuning notes

Trial 0 Elo for every variant tried (all reverted unless noted):
v1 1,279; v2 1,099; v2b 1,099; v2c 1,279 (first submission); v2d
1,099; v2e 1,191; v2f 997; v3 1,279, wall 1,165s vs 1,400s (this
submission); v4 1,191; v5 1,099; v6 1,191; v7 1,099. At 200,000
nodes plain alpha-beta with a good move order beats fancier pruning
(null-move, late-move reduction, aspiration windows all lost);
speed, not search tricks, is what mattered.

## Files

- `main.py` — the packed engine, the only file the harness runs.
- `src.py` — the readable source `main.py` is packed from.
- `LICENSE` — MIT.
- `.makefun/owner.json` — the entry wallet claim.

## Building

```
makefun compress src.py --out main.py
```

Then commit `main.py`. The source file itself is not committed; it
is sent beside the transcripts at submission so the site can re-pack
and verify it byte for byte.
