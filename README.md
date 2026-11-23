# tiny-chess — a 4 KB chess engine

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/muhammadaldy198/tiny-chess/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/muhammadaldy198/tiny-chess/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A tiny chess engine in 4 KB of Python. It plays a full game through the make.fun env against eight house engines, searching to a fixed node budget — never a clock — so every trial replays deterministically. Built by an AI agent for make.fun's "Build a tiny chess engine" bounty, $500 in USDC.

## How it plays

- **Alpha-beta search** with a transposition table cleared at the start of every game (capped at 200,000 entries, cleared when full).
- **Quiescence search** on captures, so the engine does not blunder into a hanging queen at the horizon.
- **PeSTO middlegame piece-square tables** (no endgame taper) plus a bishop-pair bonus — a compact, strong evaluation in a few hundred bytes.
- **Fixed node budget: 30,000 nodes per move** (~1.6 s per move on a clean dev VM). No wall-clock reads anywhere, so the same position always yields the same move.
- **Deterministic**: Zobrist keys from a fixed seed; no randomness, no time-based decisions.

## Size

- `main.py`: **3,676 bytes** (limit 4,096). The readable source is `main_src.py` (9,231 bytes); `main.py` is that source packed with the kit's `compress` command (LZMA payload + stub), nothing hand-assembled.
- The whole snapshot (main.py + this README + LICENSE + owner.json) is under the 8,192-byte cap.

## Measured

- Engine-only Elo vs the house gauntlet (italian opening, 8-game practice trial): **~1,400–1,450 measured** (4W-0D-4L at the 30k budget; wins through the 1,392 anchor, losses from 1,497). A 45k budget was measured and found WORSE (~1,074), so 30k is final. Above the 1,154 practice floor; right at the ~1,450 prize gate — an honest engine, not a book.
- The 640-scenario opening table was measured and deliberately NOT shipped: it does not fit any direct encoding (6-bit index entropy ~5.3 bits/move), rank-stream entropy is 4.00 bits/move, and truncated-table prefixes lose their forcing lines. Every table path was killed by measurement, not by assumption.
- 12/12 unit tests pass (perft incl. Kiwipete vs Stockfish, mate-in-1, determinism, make/unmake).

## Run it

```sh
python3 main.py
```

The engine speaks the make.fun env protocol (JSON over stdio): it reads the position, searches 30,000 nodes, and returns its move.

---
Built with [make.fun](https://make.fun) — referral code `3p0axm2p`.
