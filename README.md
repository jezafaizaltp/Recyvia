# Recyvia — AI waste segregation at the point of disposal

> Don't change people. Change the system.

**Team:** Jeza Faizal · Rasha — Providence Women's College, Kozhikode  
**Event:** AIBoomi Startup Weekend 2026

**▶ Demo video:** [Watch the Recyvia MVP demo](https://drive.google.com/file/d/1mcQTaqsRJ6C5j265B5bocLpxA4v5jRzt/view?usp=drivesdk)

---

## Problem
India generates ~62 million tonnes of municipal solid waste a year, but only ~22–28% is processed and treated (Planning Commission Task Force, 2014; MoEFCC). The main reason: waste arrives **mixed**. Mixed recyclables lose value, sanitation workers hand-sort hazardous waste, and institutions can't prove they segregate at source as SWM Rules 2016 require.

## Users & context
- **Campus / office facility managers** — legally required to segregate, no way to verify it
- **Students & staff** — want to dispose correctly but don't know which bin
- **Recyclers** — need clean, single-material streams

## Solution
Recyvia is a **sensor bin**. A camera alone can't segregate waste reliably (foil looks like plastic, and bottles often still have water in them), so each item rests on a sensor tray where the camera *and* physical sensors check it. The bin then routes it to the right chamber, compacts plastic bottles, logs the event and rewards the user.

```
Camera ─► Detector (TACO) ──┐
Inductive (metal) ──────────┤
Capacitive (moisture) ──────┼─► Fusion + confidence gate ─► Rotating chute ─► 1 of 4 chambers ─► Log ─► Dashboard + points
Load cell (weight) ─────────┤         (disagree → Reject)
Spectral material sensor ───┘   (plastic vs paper)
```

**Decision rules (checked in order)**

1. E-waste button pressed, or a device/battery seen → **E-waste**
2. Metal sensor on → **Metal** (never enters the compactor — no crushed batteries)
3. Too heavy for its type (liquid inside) or food/greasy → **Reject** (never compacted)
4. Material sensor says plastic, dry, normal weight → **Plastic** (compacted 3–5× at ~80% full)
5. Material sensor says paper, clean and dry → **Paper**
6. Anything else, or camera and sensors disagree → **Reject**

**Bin streams (v1):** Plastic · Paper · Metal · E-waste · Reject. Wet waste is deliberately left out of the first deployment to avoid smell, leaks and daily emptying; food that people still throw in goes to the leak-proof Reject chamber. Biomedical waste is not handled.

**Physical bin (planned):** 70 × 60 × 130 cm, intake at 95–100 cm with an e-waste button, 5-way rotating chute, chambers Plastic ~38 L (+ spiked-plate compactor) · Paper ~50 L · Metal ~35 L · Reject ~40 L · E-waste ~20 L (steel, heat sensor). Raspberry Pi 5 + ESP32, ultrasonic fill sensors, mains + LiFePO4 backup in a sealed rear bay. Researched prototype cost ₹84k–1.26L per bin (see `Recyvia_Bin_BOM_and_Build_Plan.xlsx`).

### What works in this MVP
- [ ] Live camera detection
- [ ] Mapping detections to 4 bin streams
- [ ] 6-rule sensor-fusion logic (real sensors or simulated readings — say which)
- [ ] Confidence gate → Reject + ask user
- [ ] On-screen chamber routing (simulated)
- [ ] Dashboard: fill level per chamber, items sorted, user points

### What's next (not built yet)
Physical sensor bin (tray, 5-way chute, compactor), local dataset with sensor readings, wet-waste module (v2), cloud dashboard, rewards redemption.

## Setup & run
```bash
git clone <repo-url> && cd recyvia
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
python app.py            # ← replace with your actual entry point
```
Requires Python 3.10+ and a webcam.

## Tech stack
Python · OpenCV · [detector, e.g. Ultralytics YOLOv8n] · [UI, e.g. Streamlit / FastAPI] · SQLite

## Models & data
| Item | Source | Licence |
|---|---|---|
| TACO — Trash Annotations in Context (1,500 images, 60 classes) | Proença & Simões, 2020, arXiv:2003.06975 | CC BY 4.0 |
| Pre-trained detector weights | [e.g. Ultralytics YOLOv8n COCO weights] | [AGPL-3.0 for Ultralytics — check] |
| Self-collected images (future) | Team, with consent | — |

**Prior work disclosure:** model training on TACO began before the event. Declared at kickoff; all event-window work is listed in the commit history.

## Evaluation & guardrails
- Held-out TACO test split: [mAP@50 / accuracy — add your real number]
- Confidence threshold [e.g. 0.5]; below it the item goes to **Reject** and the user is asked
- Reward points only for confident classifications; daily cap per user
- Every decision is logged (class, confidence, timestamp) for audit

## Known limitations & risks
- TACO is mostly outdoor Western litter — Indian items (multilayer packets, steel tumblers, etc.) may be misclassified
- Lighting and cluttered backgrounds reduce accuracy
- Physical sorting and compaction are simulated in this MVP
- Black plastics and dirty/wet surfaces read poorly on the spectral sensor; these go to Reject
- Prototype cost ₹84k–1.26L; the Lite tier (₹1.10L) is not viable at prototype cost — pricing to be revalidated after quotes for 10 units

## Team
| Name | Role | Contact |
|---   |---    |---      |
| Jeza Faizal | [Co-Founder] | [jezaafaizal@gmail.com] |
| Rasha | [Co-Founder] | [rashafathimavp652@gmail.com] |

## Licence
MIT — see [LICENSE](LICENSE).
# Recyvia
AI + sensor smart bin that segregates waste at source. Built at AIBoomi Startup Weekend 2026.
