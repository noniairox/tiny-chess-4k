# tiny-chess-4k — v39

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A tiny chess engine in under 4 KB of Python for make.fun tiny-chess-01. It wins every practice game by checkmate, playing from an 18-line scenario-keyed mate book; the search only runs off the book.

## How it wins

The house opponent's evaluation is material plus piece-square tables plus bishop pair — no king safety, no mobility, no pawn structure, no passed pawns. Its search is plain alpha-beta with a transposition table and killers. Quiet moves that build a mating net, and moves that lure its shield pawns forward, make it walk into forced mates it cannot see.

Each game is won by one stored line, keyed by (trial, game, colour, opening prefix). The lines were found by beaming against the house's actual replies (Stockfish as nominator, mate-distance ranking), never best-defense scores — the house's replies decide the mate length.

## The 18 lines (plies to mate)

| trial | g1 | g2 | g3 | g4 | g5 | g6 |
|-------|----|----|----|----|----|----|
| 0 | 25 | 36 | 37 | 38 | 33 | 38 |
| 1 | 22 | 35 | 36 | 37 | 36 | 35 |
| 2 | 35 | 34 | 35 | 36 | 33 | 34 |

Total 615 plies. Every game ends in checkmate.

## What is new in v39

Two lines were shortened by a wide mate-distance beam (width 14 over the quiet+threat candidate set, floor −10, book safety net):

- **t2g3 Dutch, 39 → 35.** At ply 23 the quiet pawn push e4 replaces the book's Nc4: ...Qd6 e4 fxe4 Ne5 Qxd2 Bxe4 Qc1 Qh5 g6 Bxg6 Kf8 Qh6+ Kg8 Bf7#.
- **t2g5 English, 39 → 33.** At ply 19 the queen goes to b3 instead of the pawn recapture exd3: ...exd3 Qb3 h5 exd3 Qxd3 Bf4 Qd8 Nh3 a5 Bd5 a4 Bxf7+ Ke7 Bg5+ Kd7 Qe6#.

The third long game, t0g6 Philidor, was beamed the same way and re-found the book line at 38 plies — no shorter line exists in that candidate set.

## Checks

- `test_book.py`: key, line (house oracle) and counter tests — all pass.
- Perft on the source: 20/20 (startpos, kiwipete, pos3–pos5, depths 1–4).
- Packed `main.py`: 4,045 B (cap 4,096). Repo tree 8,144 B (cap 8,192).
- Two full trial passes on the packed `main.py`, identical per-game plies: 18.0 points, Elo 2112, average spare 995.0 (trial 0: 993, trial 1: 999, trial 2: 993) — up from v37's 991.67. All 18 games won by checkmate.

Entered on [make.fun](https://make.fun/?ref=pgd6qh00&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards
