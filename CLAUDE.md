# Saul Goodman Courtroom — agent notes

## Commands
- Tests: `python -m pytest` (~15 s). Lint: `ruff check courtroom tests && ruff format --check courtroom tests`. Types: `pyright`.
- Robustness gate: `python -m courtroom stress --cases 100` must exit 0.
- After changing judge constants, agents, or cases, run `python -m courtroom export` to refresh `courtroom/viz/web/data/bundle.js`.

## Invariants you must not break
- `courtroom/judge/**` decides verdicts in `fractions.Fraction`. Never compare floats there; floats are display-only.
- Judge inputs are floats with ≤ 4 decimals (validated in contracts) and converted with `q()` via `repr`.
- Randomness only via seeded `random.Random(f"{seed}:...")`. Never use the global RNG.
- Dependency direction: contracts ← cases/judge/procedure ← agents ← engine ← gametheory/api/cli ← viz. Viz never calls the engine.
- `tests/test_gametheory.py::test_flagship_game_is_not_trivial` pins the flagship mixed equilibrium. If you retune the case or the sanctions, check it on several disjoint seed ranges, not just one.

## Windows
- `ProcessPoolExecutor` (`--workers N`) needs a real script/module entry point, not `python - <<EOF`.
- The CLI reconfigures stdout to UTF-8 because cp1252 consoles crash rich output.
