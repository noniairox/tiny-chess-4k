# tiny-chess-4k

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/noniairox/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in 4 KB of Python for make.fun's "Build the strongest chess engine in 4 KB of Python" bounty, $500 in USDC.

## Run it

```sh
python3 main.py
```

## How it works

`main.py` is the whole engine, packed with `makefun compress` from the readable `src.py` (0x88 board, negamax with alpha-beta, quiescence, transposition table, MVV-LVA move ordering, piece-square evaluation, fixed 50,000-node budget per move, no clock, no randomness). It talks to the trial env at `MAKEFUN_ENV_URL` and plays the house gauntlet in UCI.

---

Entered on [make.fun](https://make.fun/?ref=pgd6qh00&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards
