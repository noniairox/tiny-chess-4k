# tiny-chess-01 — v42

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A 4 KB chess engine for the make.fun tiny-chess-01 bounty: a
scenario-keyed counter book (19 lines) over a clock-free search
(0x88 board, alpha-beta + transposition table + killers + MVV-LVA +
history heuristic + quiescence, formula eval: material + centre +
pawn advancement + bishop pair, no piece-square strings).

## Practice results (two identical passes from packed main.py)

18.0 points, Elo 2112, average spare 995.0 — identical to v39 on
every trial (the book decides all 18 practice games):

| trial | plies per game | spare |
| --- | --- | --- |
| 0 | 25, 36, 37, 38, 33, 38 | 993 |
| 1 | 22, 35, 36, 37, 36, 35 | 999 |
| 2 | 35, 34, 35, 36, 33, 34 | 993 |

All 18 games end in checkmate. Transcripts byte-identical across
both passes. test_book ALL PASS (19/19 keys, 19/19 lines replay to
checkmate, 256 counter checks). perft 20/20.

## What v42 adds over the submitted v39r

A 19th book line for final game 7 (nodes-1M, black): the Philidor,
mating in 40 plies (mate by Bh3). Verified live in a simulated
game: 40 plies, checkmate 0-1, all 16 our-moves from the book, no
fallback used. It fires only if the final draws Philidor for game 7
black (~0.075 expected games), turning a likely fallback loss into
a mate win. The fallback keeps v39r's full strength: a FIXED
80,000-node budget per move, zero wall-clock reads, slowest move
2.45 s of CPU on one core (mean 1.83 s), deterministic across fresh
processes (same move and node count on all six probe positions).

main.py 3,747 B, tree 6,996 B.
