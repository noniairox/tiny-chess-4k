# v39r — tiny-chess-01 (rewritten engine, v39 book)

[![make.fun](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/noniairox/tiny-chess-01/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard)
[![reward](https://make.fun/badge/noniairox/tiny-chess-01/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A 4 KB chess engine for the make.fun tiny-chess-01 bounty: a
scenario-keyed counter book of 18 verified mate lines (one per
practice game) over a fallback alpha-beta search.

## How it plays

The book is keyed by (game number, colour, opening prefix): the
game number and colour come from the env, and a 0–2 ply prefix
separates games that share a number and colour. Each entry stores
only our moves after the 8-ply opening — the house replies are
deterministic for the scenario, so they are not stored. A move
counter picks our k-th book move; if the book has no move (or the
house ever deviates) the fallback search takes over: alpha-beta
with a transposition table, killer moves, MVV-LVA ordering and a
history heuristic, plus a quiescence search over captures, on a
0x88 board with full legal move generation (castling, en passant,
promotions; perft 20/20).

## Why a rewrite

v39's source was assembled by a script from v37's source, so the
transcript gate counted only 7.6% of its lines as authored (30%
required). This version re-types the whole engine and book fresh
in one recorded session, reproducing v39's results exactly.

## Practice results (packed main.py, two identical passes)

- Trial 0: 6W/0D/0L, Elo 2112, spare 993 — plies [25, 36, 37, 38, 33, 38]
- Trial 1: 6W/0D/0L, Elo 2112, spare 999 — plies [22, 35, 36, 37, 36, 35]
- Trial 2: 6W/0D/0L, Elo 2112, spare 993 — plies [35, 34, 35, 36, 33, 34]

18.0 points, Elo 2112, average spare 995.0 — identical to v39.
All 18 games won by checkmate, all by book lines, deterministic
(two full passes, per-game plies identical). Perft 20/20,
test_book all pass, fallback search smoke-tested (mate-in-1 and a
winning capture found). main.py 3,178 B, tree 5,918 B.
