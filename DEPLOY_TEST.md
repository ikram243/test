# Test-deploying Roadmind (free)

Use the **Monday** files (the full app). You can test from a separate test repo, so your real repo stays clean.

## Option A — Hugging Face Spaces (recommended, free, Docker)
1. Go to huggingface.co, then **New Space**. Choose **SDK: Docker**, template **Blank**, hardware **CPU basic (free)**.
2. In the Space, open **Files → Add file → Upload files**. Drag in everything from the Monday folder, except `COMMITS.md` and `.env`.
3. Replace the Space's `README.md` with `deploy/huggingface/README.md`. Its first lines tell Hugging Face to use port 7860.
4. Upload one junction clip as `data/clips/demo.mp4`. Keep it small (compress it with ffmpeg, see the README).
5. Open **Settings → Variables and secrets** and add:
   - `ROADMIND_DETECT_EVERY` = `2` (the free CPU is slow)
   - `ROADMIND_ADVICE_INTERVAL` = `30` (new advice every 30 s for the demo)
   - `GEMINI_API_KEY`, as a **secret** (optional)
6. Wait for the build (about 5–10 min). Then open the Space, go to `/calibrate`, draw the zones and press **Save**.

Note: the free Space can restart and lose the saved zones and decision log. That is fine for testing. Calibrate locally, commit `junctions/demo.json`, and it will always be there.

## Option B — Render.com
**New → Web Service**, connect the GitHub repo, and choose runtime **Docker**. Render sets `PORT` itself. Add the same environment variables.
The free plan has 512 MB RAM, which is tight for YOLO. If it crashes, use Hugging Face instead.

## Option C — Railway
**New project → Deploy from GitHub repo**. Railway detects the Dockerfile. Add the same variables. The trial has limited free credit.

## Check it works
- `/health` returns `{"status": "ok"}`
- `/` shows the live video with boxes, and advice appears after a few seconds
