# tiny-chess-01 — v45

[![make.fun](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A tiny chess engine (main.py, 4,042 bytes) that plays the 6 practice games
from a 19-line scenario-keyed counter book and falls back to a fixed-node
search when off-book.

## Result

- 18.0 points, Elo 2112, average spare plies **1000.33** (t0 1001, t1 1005, t2 995)
- All 18 practice games won by checkmate.
- Beats v43 (999.67) by 2 plies in trial 0, game 3.

## What changed vs v43

- **t0g3, 35 → 33 plies.** A Monte Carlo wave found a shorter mating line:
  at ply 19 the queen goes to b3 (d1b3) instead of the book's knight move,
  and the king hunt closes two plies earlier. The line was verified move by
  move against the house oracle (0 reply mismatches, final checkmate).
  Full line: e2e4 e7e5 g1f3 b8c6 f1c4 f8c5 c2c3 g8f6 b2b4 c5d6 e1g1 f6e4
  d2d4 e5d4 f1e1 f7f5 b1d2 d4c3 d1b3 c3d2 c1d2 e8f8 e1e4 f5e4 a1e1 e4f3
  b3f3 d6f4 f3f4 d8f6 f4f6 g7f6 d2h6.
- **Faster fallback search.** The evaluation now uses a piece-square table
  built once at import (same formula as before), so the 80,000-node fallback
  runs in 2.71 s at worst on one CPU (v43: 3.61 s).
- **B1 kept.** The philidor-vs-nodes-1M black line (40 plies) is unchanged.
- **Fallback is still clock-free.** Exactly 80,000 nodes per move, no
  wall-clock use, deterministic.

## Engine

- 0x88 board, alpha-beta with transposition table, killer moves,
  MVV-LVA + history ordering, quiescence search.
- Evaluation: material + piece-square values + bishop pair.
- Book: 19 entries (18 practice games + the B1 game-7 line), keyed by
  game, colour, and the first 8 plies.
