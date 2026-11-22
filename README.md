# Roadmind

**Traffic lights that think about people.**

Roadmind is an AI traffic-signal **advisor** for busy junctions in Bangladeshi cities. A camera watches the junction; AI detects and tracks every vehicle on each road, estimates how many **people** are waiting, and recommends green time to the **traffic officer** — explained in simple Bangla. The officer always decides. **Roadmind never controls real signals.**

> Built for the **CoU AI Hackathon 2026** by team **IIUC_ZeroCode**, Department of CSE, International Islamic University Chittagong.

**Live demo:** _add your deployment link here_ · **Demo video:** _add your YouTube link here_

---

## Team
| Member | Role |
|---|---|
| **Ehsanul Alam** | Lead developer — vision pipeline, advisor, dashboard, Bangla explanations, demo |
| **K.A Shahriar Rubaiyat** | Data & evaluation — junction videos, ground-truth counts, zone calibration, accuracy results |
| **MD. Ikram Hossain Sami** | Testing, deployment & docs — tests, hosting, README, credits, pitch |

## The problem
Most junction signals in Bangladesh run on fixed timers, or police control them by hand. A fixed timer cannot react to what is actually happening: one road sits nearly empty with a long green, while another is jammed. A bus carrying around 40 people waits as long as a car carrying one. People trying to cross are an afterthought. Past automatic signal projects in Dhaka were abandoned and police went back to hand signals — automation that ignores mixed traffic and the officer does not work.

## The solution
1. **See** — a camera watches the junction; YOLO detects buses, cars, motorbikes, bicycles, trucks and people (and CNG, rickshaw, leguna, e-rickshaw with a fine-tuned model). ByteTrack follows each vehicle.
2. **Count per road** — the officer draws a zone for each road; Roadmind counts vehicles, the queue (stopped vehicles) and the longest wait on each road, and pedestrians at crossings.
3. **Think about people** — vehicles become people waiting, using editable occupancy values (a bus ≈ 40, a CNG ≈ 3, a car ≈ 1.5 — clearly labelled assumptions).
4. **Advise** — an occupancy-weighted **Max-Pressure** advisor splits the signal cycle by people waiting, with safety rules (minimum/maximum green, a fairness guard so no road waits too long, extra walk time for pedestrians).
5. **Explain in Bangla** — e.g. *“রোড ২-এ বেশি মানুষ অপেক্ষা করছে, তাই ১৫ সেকেন্ড বেশি সবুজ বাতি দিন।”* A template works offline; Gemini can rephrase it, but its reply is accepted only if it keeps the exact numbers.
6. **The officer decides** — Accept or Override (with a reason). Every decision is logged.
7. **Knows when not to guess** — in the dark, rain/blur or with a blocked camera, it reports **low confidence** and advises keeping the fixed timer.

## Features
| # | Feature | Where |
|---|---|---|
| 1 | Vehicle detection + tracking per road, queue and longest wait | `app/video/detector.py`, `tracker.py` |
| 2 | People-first estimate with editable occupancy (“Assumptions” button) | `app/core/people.py` |
| 3 | Timing advisor — occupancy-weighted Max-Pressure + fairness guard | `app/core/advisor.py` |
| 4 | Confidence check — dark, blur/rain, blocked view, low detection confidence | `app/video/confidence.py` |
| 5 | Pedestrian protection — extra walk time when many wait to cross | `app/core/advisor.py` |
| 6 | Bangladeshi rhythm mode — forecast with Jumu'ah, school release, office and market rush | `app/core/rhythm.py`, `config/events.json` |
| 7 | Bangla explanation (template + Gemini with number check) and voice read-out | `app/core/explain.py` |
| 8 | Emergency priority — **simulated only** | `app/core/advisor.py`, dashboard 🚑 button |
| 9 | Officer dashboard — live video, Vehicles ↔ People toggle, Accept/Override, decision log | `app/static/` |

Also: a simulated comparison of Roadmind vs the fixed timer (average person wait, people moved per minute, a 2-minute preview), a calibration page to draw road zones, video from a file, webcam, phone camera URL or CCTV RTSP link, and an English/Bangla interface.

## How the AI works
```
camera frame ─► YOLO detection ─► ByteTrack IDs ─► road zones ─► vehicles, queue, longest wait per road
                                                                      │
                     occupancy assumptions ──────────────────────────► people waiting per road
                                                                      │
confidence check (brightness, sharpness, edges, detector confidence) ─┤
                                                                      ▼
                     occupancy-weighted Max-Pressure advisor (min/max green, fairness, walk time)
                                                                      │
                     Bangla template ──► (optional) Gemini rewording, accepted only if numbers match
                                                                      ▼
                                  officer dashboard: Accept / Override ─► decision log
```
- **Detection:** YOLO11n (Ultralytics) on each frame (or every Nth frame on slow hardware). The pretrained model knows bus, car, truck, motorbike, bicycle and person. Bangladeshi types need fine-tuning on a licensed Bangladeshi dataset; the dashboard states which types the current model supports.
- **People estimate:** `people = Σ count(type) × occupancy(type)`. Values are averages, not exact counts.
- **Advisor:** each road's green time is proportional to its people waiting, clamped to [min, max] green, with the cycle and walk time fixed. Roads waiting longer than the fairness limit are served first. Advice is recomputed once per signal cycle (not every frame), so it is stable.
- **Explanation:** numbers always come from the advisor; the language model only rewords, so it can never change the decision.
- **Rhythm mode:** the app saves a count every minute; `scripts/record_counts.py` adds history from clips recorded at known times. The forecast is the average for the same kind of day and time, times event multipliers (assumptions until enough data exists), with an uncertainty band.

## Honest limits
- Detection accuracy drops at night, in rain and when vehicles overlap — that is exactly when Roadmind reports low confidence.
- Occupancy values are averages, not counted passengers.
- The simulator shows what the advice *could* do; it is **not** proof of real-world savings.
- Emergency priority is a simulation; Roadmind never controls real signals.
- Without fine-tuning, CNGs, rickshaws and legunas are not recognised as their own types.

## Privacy and safety
- No number plates are read or stored. No faces are identified.
- Raw frames are never saved; the decision log keeps only a tiny, heavily blurred snapshot.
- Roadmind only advises. The officer makes every decision.

## Evaluation
Run on hand-counted test clips (see `scripts/evaluate_counts.py`) and paste the results here — including weak cases.

_Results: to be added._

## Run locally
Requires Python 3.10+.
```bash
python -m venv .venv
# Windows: .venv\Scripts\activate      macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env            # Windows: copy .env.example .env  — add a Gemini key if you have one
uvicorn app.main:app --reload
```
1. Put a junction video at `data/clips/demo.mp4`, or press **Upload a video** on the calibration page.
2. Open **http://localhost:8000/calibrate** and press **✨ Auto-detect roads** (it watches ~20 s of traffic and suggests one zone per approach), or draw one zone per road by hand just before the stop line. Add crossing zones, then press **Save**.
3. Open **http://localhost:8000** — the officer dashboard.

Other video sources: a webcam (`0`), a phone IP-camera app URL (`http://…/video`) or a CCTV RTSP link (`rtsp://…`).
To add a junction, copy `junctions/demo.json` to a new name and change `name` and `source`.

### Speed and accuracy
The video and the AI run in separate threads: the dashboard video stays smooth (up to 20 fps) and the boxes update at the AI speed, shown as "AI n" in the video badge.
- **Faster (recommended on a laptop):** `pip install openvino`, then `python scripts/speed_up.py`. Put the printed `ROADMIND_MODEL=...` line into `.env`. About 2–3x faster, same detections.
- **More accurate:** `python scripts/speed_up.py --model yolo11s` (a bigger model that runs about as fast as the small one did without OpenVINO). Set `ROADMIND_IMG_SIZE=960` for small, far-away vehicles.
- One box per object: a vehicle is never counted twice as two classes, and people riding a motorbike, bicycle or rickshaw are marked as riders, not pedestrians.
- No detector is 100% accurate. Results depend on camera height, light and how crowded the road is. We measure our real accuracy with `scripts/evaluate_counts.py` and report it below.

### Compress a clip (keep clips under ~40 MB for GitHub)
```bash
ffmpeg -i input.mp4 -vf scale=960:-2 -c:v libx264 -crf 28 -an data/clips/demo.mp4
```

### Tests
```bash
pip install -r requirements-dev.txt
pytest -q
```

### Scripts
```bash
# accuracy against hand counts (CSV: second,road_id,type,count)
python scripts/evaluate_counts.py --junction demo --gt data/ground_truth/demo.csv
# add rhythm history from a clip recorded at a known time
python scripts/record_counts.py --junction demo --clip data/clips/gec_fri.mp4 --start "2026-11-27 12:30"
```

## Deploy
**Hugging Face Spaces (free, Docker):** create a Space with the *Docker* SDK, push this repository to it, and replace the Space's README with `deploy/huggingface/README.md`. Optionally add `GEMINI_API_KEY` and `ROADMIND_DETECT_EVERY=2` as Space secrets/variables.
**Render / Railway / any Docker host:** build the `Dockerfile`; the app listens on `$PORT` (default 7860).

## Tech stack
Python · FastAPI · Ultralytics YOLO11 + ByteTrack · Supervision · OpenCV · NumPy · SQLite · Google Gemini (optional) · plain HTML/CSS/JavaScript dashboard (no external CDNs, works offline).

## AI tools used
- **Claude (Anthropic)** — coding assistance, UI mock-ups and drafting documentation.
- **Google Gemini** — optional rewording of the Bangla explanation at runtime.
- **YOLO11 (Ultralytics)** — the detection model.

Full list of libraries, models, fonts and licences: [CREDITS.md](CREDITS.md).
