# Copilot Instructions — Soc Ops

## Mandatory Checklist (run before every change)

```bash
uv run ruff check .   # lint — must pass
uv run pytest         # test — 25 tests must pass
```

> Do not proceed if either command fails.

---

**Soc Ops** is a Social Bingo game (FastAPI + Jinja2 + HTMX). Players mark squares matching people they find to get 5 in a row.

## Commands

```bash
uv sync                                                             # install deps
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000   # dev server
```

## Architecture

```
app/
  main.py         FastAPI routes & HTMX endpoints
  models.py       GameState (StrEnum), BingoSquareData (frozen), BingoLine
  game_logic.py   generate_board, toggle_square, check_bingo (pure functions)
  game_service.py GameSession dataclass + in-memory _sessions dict
  data.py         QUESTIONS list, FREE_SPACE string
  templates/      Jinja2 (base.html, home.html, components/)
  static/         app.css, htmx.min.js
```

## Key Conventions

- snake_case, type hints everywhere; Pydantic for data, dataclasses for mutable state
- `BingoSquareData` is frozen — use `model_copy(update={...})`, never mutate in-place
- Routes return HTML fragments (HTMX swaps), not JSON; full-page routes return full templates
- All logic in Python — no JS game logic
- CSS utilities only from `app/static/css/app.css`; see [css-utilities.instructions.md](.github/instructions/css-utilities.instructions.md). No Tailwind CDN.

## Pitfalls

- Free space is index 12 — always pre-marked, cannot be toggled
- `_get_winning_lines()` is cached — never pass mutable args
- Returning a full page from a component endpoint breaks HTMX swaps
