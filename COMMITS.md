# Monday (23 Nov) — 4 commits, then test deploy

**Before you start:** unzip this over your repo folder, after Sunday's commits are done, and let it replace files.
Do NOT commit this COMMITS.md file.
You can also delete `scripts/.gitkeep`, because the folder now has real files. Tick that deletion in commit 4.

In GitHub Desktop, tick **only** the files listed for a commit, type the message and press **Commit to main**. Push after each commit, or at the end.

---

### Commit 1 — morning
**Message:** `Add confidence check for dark, blurred or blocked camera views`
Files:
- `app/video/confidence.py`

### Commit 2 — midday
**Message:** `Add queue simulator comparing Roadmind with the fixed timer`
Files:
- `app/core/simulator.py`

### Commit 3 — afternoon
**Message:** `Add rhythm forecast, simulated emergency priority and wire all features`
Files:
- `app/core/rhythm.py`
- `config/events.json`
- `app/pipeline.py`
- `app/main.py`
- `requirements.txt`
- `tests/test_extras.py`

### Commit 4 — evening
**Message:** `Add Docker deploy, evaluation scripts, full README and credits`
Files:
- `Dockerfile`
- `.dockerignore`
- `deploy/huggingface/README.md`
- `scripts/offline.py`
- `scripts/evaluate_counts.py`
- `scripts/record_counts.py`
- `scripts/speed_up.py`
- `.env.example`
- `.gitignore`
- `scripts/.gitkeep` (the deletion, if you deleted it)
- `README.md`
- `CREDITS.md`

---

**Test after commit 4:**
```
pytest -q            # should print: 15 passed
uvicorn app.main:app --reload
```

## Final day (24 Nov) — real work, one commit per hour
Do these on-site. They are real changes, not pre-made ones:
1. Calibrate the zones for your real junction clip. Commit `junctions/*.json`.
2. Hand-count one clip and run `scripts/evaluate_counts.py`. Paste the table into the README Evaluation section.
3. Small UI or bug fixes you find while testing.
4. Add the live link and demo video link to the README.
5. Fill in the clip details (date, place, who recorded it) in CREDITS.md.
