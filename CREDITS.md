# Credits and licences

Every library, model, dataset and asset used in Roadmind, with its licence and what we use it for.

## Libraries and models
| Name | Link | Licence | Used for |
|---|---|---|---|
| Ultralytics YOLO (YOLO11n weights) | https://github.com/ultralytics/ultralytics | AGPL-3.0 | Vehicle and pedestrian detection; built-in ByteTrack tracking |
| Supervision (Roboflow) | https://github.com/roboflow/supervision | MIT | Detection format and polygon road zones |
| lap | https://github.com/gatagat/lap | BSD-2-Clause | Assignment solver used by ByteTrack |
| OpenCV (headless) | https://opencv.org | Apache-2.0 | Video reading, image checks, drawing overlays |
| PyTorch | https://pytorch.org | BSD-3-Clause | Runs the YOLO model |
| NumPy | https://numpy.org | BSD-3-Clause | Numbers and arrays, simulator |
| FastAPI | https://fastapi.tiangolo.com | MIT | Web server and API |
| Uvicorn | https://www.uvicorn.org | BSD-3-Clause | Running the web server |
| python-dotenv | https://github.com/theskumar/python-dotenv | BSD-3-Clause | Loading settings from `.env` |
| Google Gen AI SDK (`google-genai`) | https://github.com/googleapis/python-genai | Apache-2.0 | Calling Gemini to phrase the Bangla sentence |
| tzdata | https://github.com/python/tzdata | Apache-2.0 | Bangladesh time zone for rhythm mode |
| OpenVINO (optional) | https://github.com/openvinotoolkit/openvino | Apache-2.0 | Faster detection on laptop CPUs (`scripts/speed_up.py`) |
| pytest (development only) | https://pytest.org | MIT | Unit tests |

Because Ultralytics YOLO is AGPL-3.0, this repository is public and open source.

## Fonts (bundled in `app/static/fonts`, so the dashboard works offline)
| Font | Link | Licence |
|---|---|---|
| Inter | https://github.com/rsms/inter | SIL Open Font License 1.1 |
| Noto Sans Bengali | https://fonts.google.com/noto/specimen/Noto+Sans+Bengali | SIL Open Font License 1.1 |

Font files were taken from the Fontsource packages (`@fontsource/inter`, `@fontsource/noto-sans-bengali`).

## APIs
| API | Used for |
|---|---|
| Google Gemini | Rewording the template Bangla sentence; the reply is accepted only if it keeps the exact numbers |

## AI tools
| Tool | Used for |
|---|---|
| Claude (Anthropic) | Coding assistance, design mock-ups and drafting documentation |

## Research that shaped the method
- Person-based signal control — minimising **person delay** instead of vehicle delay (E. Christofa et al., UC Berkeley, 2012–2013).
- **Max-Pressure** signal control and its occupancy-weighted variants (review: arXiv 2611.04029).
- These ideas are re-implemented in our own simple form in `app/core/advisor.py`; no code was copied.

## Data
- Junction videos: recorded by the team, with permission (add date, place and who recorded each clip here).
- Occupancy values (people per vehicle) are the team's stated assumptions, editable in the dashboard.
