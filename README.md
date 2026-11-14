# tiny-chess-4k

A tiny chess engine in under 4 KB of Python, built for the make.fun
tiny-chess-01 bounty. It plays the house gauntlet over the make.fun
environment protocol and needs nothing but the Python 3.13 standard
library.

## How it plays

The engine is a single packed file, `main.py` (2,862 bytes). The
readable source is `src.py` (7,666 bytes); `main.py` is produced from
it by `makefun compress`, so the two are byte-identical in behaviour.

Board: 0x88 representation, 128 squares, pieces 1-6 white and 7-12
black. Move generation covers pawns (including double push, en
passant and promotion), knights, bishops, rooks, queens, kings and
both castling sides, with full legality filtering by king safety.

Search: negamax alpha-beta with a transposition table, quiescence
search on captures and promotions (depth capped at 8 plies to avoid
explosion), and MVV-LVA plus transposition-table move ordering.
The node budget is fixed at 200,000 nodes per move, so the engine
is deterministic: same position, same move, every time. There is no
clock, no randomness and no model call anywhere in the code.

Evaluation: tapered middlegame/endgame piece-square tables with
phase interpolation, a bishop-pair bonus, and material values of
pawn 50, knight 160, bishop 165, rook 250, queen 450. The king
table switches to a centralised endgame table once material falls.

## Results

Local practice trial (trial 0, six games against the house gauntlet):

- Elo 1,279 (2 wins, 2 draws, 2 losses), above the 1,154 practice
  floor. The house baseline (sunfish, 50,000 nodes) sits near 1,365,
  so there is still headroom before the 25 November deadline.

Perft is exact on the standard suite (startpos depth 4 = 197,281;
Kiwipete depth 4 = 4,085,603; positions 3, 4 and 5 all exact), and
the engine runs at roughly 35,000 nodes per second, so the worst
observed move takes about 5.3 seconds, safely inside the 10 second
per-move limit.

## Tuning notes

The engine was tuned against the local practice gauntlet (trial 0):

- v1, 200,000 nodes, plain piece-square eval: Elo 1,279.
- v2 added a tapered eval, bishop-pair and passed-pawn bonuses,
  null-move pruning and killer/history ordering: Elo 1,099, so
  the extra search features cost more than they gave at this size.
- v2b disabled null-move pruning only: Elo 1,099 again, which
  cleared null-move and pointed at the ordering and the pawn bonus.
- v2c removed killer/history ordering and the passed-pawn bonus,
  keeping the tapered eval and the bishop-pair bonus: Elo 1,279,
  back to v1 strength with the transcript checks fixed.
- v2d added a pawn-structure eval and cut the budget to 150,000
  nodes: Elo 1,099, below the practice floor, so it was reverted.

The lesson is that at 200,000 nodes the plain alpha-beta with a
good move order beats fancier pruning; the eval is cheap enough
that the node budget, not the search tricks, decides the strength.

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

Then commit `main.py`. The source file itself is not committed to
the repository's history; it is sent beside the transcripts at
submission time so the site can re-pack and verify it byte for byte.
