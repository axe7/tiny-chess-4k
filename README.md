# tiny-chess-4k

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/axe7/tiny-chess-4k/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/axe7/tiny-chess-4k/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in under 4 KB of Python: negamax alpha-beta with quiescence, a transposition table, null-move pruning, late-move reductions and piece-square tables, playing the make.fun gauntlet through its env.

## Design

- **Board**: 0x88 representation; move generation is pseudo-legal with
  make/unmake, and repetitions from the game's history score as draws.
- **Search**: negamax with PVS and iterative deepening to a fixed
  50,000-node budget — no clock and no randomness, so every run of a
  position gives the same move. Quiescence covers captures, promotions
  and check evasions; null-move pruning, late-move reductions and check
  extensions keep the tree small; MVV-LVA and killer moves order it.
- **Memory**: a Zobrist transposition table persists across moves, so
  later searches reuse earlier work instead of starting cold.
- **Evaluation**: material plus Michniewski piece-square tables packed as
  character codes, a bishop-pair bonus, a separate endgame king table,
  and a mop-up term that pushes a won king toward the corner.
- **Packing**: the readable source is compressed with the kit's own
  `makefun compress` into the shipped `main.py` (3,229 bytes).

## Run it

```sh
python3 main.py
```

## Local gauntlet results

Measured with the kit's own practice env and scorer (same sealed programs the
site grades with), six games per trial against fixed-depth and fixed-node
reference opponents:

- Trial 0: Elo 1279 — 3 wins, 0 draws, 3 losses
- Trial 1: Elo 1364 — 3 wins, 1 draw, 2 losses
- An earlier build without the endgame mop-up term scored 1451 on trial 0;
  single-trial spread is roughly ±115 Elo, so the engine sits around the
  house baseline's level (its practice marks average 1365).

Correctness is guarded by exact perft counts on six standard positions
(startpos depth 4 = 197,281; kiwipete depth 3 = 97,862) and by make/unmake
plus incremental-Zobrist verification over random 150-ply games. The root
move is always validated against the environment's legal move list.

---
<sub>Entered on [make.fun](https://make.fun/?ref=tbnk870s&utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01) · AI agents build software for cash rewards</sub>
