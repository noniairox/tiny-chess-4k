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

`main.py` (3,385 bytes) is the packed engine; `src.py` (9,488 bytes)
is the readable source it is packed from by `makefun compress`.
Board: 0x88, full move generation (double push, en passant,
promotion, castling), legality by king safety. Search: negamax
alpha-beta with a transposition table, quiescence on captures
(8 plies max), MVV-LVA/TT/history ordering, futility pruning at depth
1-3, and delta pruning inside quiescence, at a fixed 200,000 nodes
per move — deterministic, no clock, no randomness, no model call.
A 9 second cap returns the best move from the last completed depth
instead of timing out. Eval: tapered piece-square tables, phase
interpolation, bishop-pair bonus, pawn 50, knight 160, bishop 165,
rook 250, queen 450; tables precomputed, score kept incrementally.
A piece list (PW/PB) makes move generation iterate only the side's
~16 pieces instead of all 128 squares (about 1.36x faster); a
history heuristic orders quiet moves by past beta cutoffs.

## Results

Local practice trials (six games each vs the house gauntlet):
trial 0 Elo 1,279 (2W 2D 2L), trial 1 Elo 1,451 (3W 2D 1L),
trial 2 Elo 1,545 (3W 3D 0L, undefeated); average over the three
trials is 1,425, above the 1,154 floor and 90 Elo above the
previous version v10d (average 1,335). Perft is exact (startpos
d4 = 197,281; Kiwipete d4 = 4,085,603; positions 3, 4 and 5
exact). Search speed is 46,105 nodes/second vs 38,399 for v10d
(1.20x faster), so on the grading server (about 3x slower) it
completes about 1.2x more nodes per move.

Version 9h scored 1,451 on the server and entered the final.
Version 10d adds quiescence delta pruning on top of 9h. This
version (v13g) adds a piece list and the history heuristic.

## Tuning notes

Trial Elo per variant: v9h 1,451/1,279 avg 1,365 (4th, server
1,451); v10d 1,451/1,364/1,191 avg 1,335 (5th, server 1,191);
v13d 1,364/1,451/1,545 avg 1,453 (history only); v13g
1,279/1,451/1,545 avg 1,425 (this submission — beats v10d by
90, wins 2 of 3 trial sets, and is 1.20x faster). Plain
alpha-beta with good move order beats fancier pruning
(null-move, LMR, aspiration, PVS, killer moves all lost);
futility pruning, quiescence delta pruning, the history
heuristic, and a piece list raised the average.

## Files

- `main.py` — the packed engine, the only file the harness runs.
- `src.py` — the readable source `main.py` is packed from.
- `LICENSE` — MIT.
- `.makefun/owner.json` — the entry wallet claim.

## Building

```
makefun compress src.py --out main.py
```

Then commit `main.py`. The source file itself is not committed.
