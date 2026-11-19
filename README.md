# Tiny Chess 4K — Tapered Eval

[![make.fun](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-7/rank.svg)](https://make.fun/bounty/tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/Rifat7600/tiny-chess-01-7/reward.svg)](https://make.fun/bounty/tiny-chess-01)

A chess engine in under 4 KB of Python for the
[make.fun tiny-chess-01 bounty](https://make.fun/bounty/tiny-chess-01):
build the strongest chess engine that fits in 4,096 bytes.

## How it plays

The engine is a single `main.py` (Python 3.13, standard library only).
It talks to the trial environment over HTTP, reads the position from the
game it is served, and answers each move from a fixed node budget —
never from a clock, so every trial replays move for move.

- **Board:** 0x88 representation, make/unmake with an undo stack and an
  incremental Zobrist hash.
- **Search:** iterative deepening alpha-beta with a transposition table,
  killer moves and MVV-LVA capture ordering, capped at **30,000 nodes**
  per move. Leaves are extended by a quiescence search over captures
  (and over every reply while in check).
- **Evaluation:** tapered middlegame/endgame piece-square tables, so a
  piece is worth a different amount depending on how much material is
  left on the board. On top of that: a bishop-pair bonus, a bonus for
  rooks on open and semi-open files, and a bonus for knights and
  bishops sitting on outposts — squares deep in enemy territory that
  are defended by a friendly pawn and cannot be chased away by an
  enemy pawn.

## Correctness

The move generator is validated against the standard perft suite:
the start position (20 / 400 / 8,902 / 197,281 to depth 4), Kiwipete
(48 / 2,039 / 97,862), and the en-passant, castling and promotion
positions — all exact. That matters because a single missing move, a
castling right lost too early, or a hash that drifts after make/unmake
would quietly lose games no amount of search could win back.

## Why fewer nodes

The kept entry from the same stable searches 45,000 nodes a move.
This one searches only 30,000 but spends them on better judgement:
the tapered tables and the positional bonuses steer the search toward
plans — a rook on an open file, a knight on an outpost — that a
material-only evaluation has to discover by brute force. Fewer nodes
also means a faster move, comfortably inside the 10-second limit.

## Files

- `main.py` — the whole engine (3,629 bytes as committed)
- `source.py` — the readable source it was packed from
- `LICENSE` — MIT
- `.makefun/owner.json` — the claim file for this entry

## License

MIT. See `LICENSE`.
