# 🏎️ F1 Strategy Analyzer

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![FastF1](https://img.shields.io/badge/FastF1-3.x-E8002D?style=flat)](https://docs.fastf1.dev)
[![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=flat&logo=jupyter&logoColor=white)](https://jupyter.org)
[![Plotly](https://img.shields.io/badge/Plotly-Interactive-3F4F75?style=flat&logo=plotly&logoColor=white)](https://plotly.com)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

> **Post-race analysis tool for F1 enthusiasts and analysts.**  
> Visualize circuit performance, decode tyre strategies, simulate alternate pit scenarios — all powered by real FastF1 telemetry data.

---

##  Features

| Feature | Description |
|---|---|
|  **Circuit Heatmap** | Real GPS telemetry mapped onto circuit — speed zones, brake points, gear maps, sector splits |
|  **Sector Analysis** | S1/S2/S3 delta-to-best heatmap across all drivers. See where tenths are lost |
|  **Tyre Strategy View** | Full compound stints, pit windows, tyre age status per driver |
|  **Position Trace** | Lap-by-lap position changes across the full race distance |
|  **What-If Simulator** | "What if Hamilton pitted Lap 25 instead of 32?" — simulates time delta + finish position |
|  **Race Debrief** | Auto-generated post-race intelligence report with timeline of key events |

---

## Quick Start

### 1. Clone the repo
```bash
git clone https://github.com/dhanushc13/f1-strategy-analyzer.git
cd f1-strategy-analyzer
```

### 2. Create & activate virtual environment
```bash
python -m venv f1_env

# Windows
f1_env\Scripts\activate

# Mac / Linux
source f1_env/bin/activate
```

### 3. Install dependencies
```bash
pip install -r requirements.txt
```

### 4. Launch the notebook
```bash
jupyter notebook notebooks/f1_strategy_analyzer.ipynb
```

---

##  Project Structure

```
f1-strategy-analyzer/
│
├── notebooks/
│   └── f1_strategy_analyzer.ipynb   # Main analysis notebook (7 sections)
│
├── src/
│   ├── circuit.py                   # Circuit heatmap rendering
│   ├── strategy.py                  # Tyre strategy + StrategySimulator class
│   ├── sector.py                    # Sector analysis functions
│   └── debrief.py                   # Race debrief report generator
│
├── data/
│   └── f1_cache/                    # FastF1 local cache (auto-created)
│
├── outputs/
│   ├── f1_full_analyzer.html        # Interactive web dashboard
│   ├── circuit_VER_speed.png        # Circuit heatmap outputs
│   ├── tyre_strategy.png            # Strategy visualizations
│   ├── sector_comparison.png        # Sector analysis plots
│   ├── position_trace.html          # Interactive Plotly chart
│   └── whatif_sweep_HAM.png         # What-if simulation sweep
│
├── assets/
│   └── screenshots/                 # README screenshots
│
├── requirements.txt
├── .gitignore
└── README.md
```

---

##  Changing the Race

Edit the config block at the top of the notebook — just 3 lines:

```python
SEASON        = 2023
RACE_NAME     = 'Monaco'       # 'Bahrain', 'Silverstone', 'Monza', 'Spa'...
FOCUS_DRIVERS = ['VER', 'ALO', 'LEC', 'HAM', 'SAI']

# What-If config
WHATIF_DRIVER   = 'HAM'
WHATIF_PIT_LAP  = 25
```

Any race from **2018–2024** works — FastF1 fetches the data automatically.

---

## 📊 Notebook Sections

| Section | What it does |
|---|---|
| **1 — Setup & Load** | Install deps, enable cache, load session via FastF1 |
| **2 — Circuit Heatmap** | `plot_circuit_heatmap()` — speed / gear / brake / throttle overlays |
| **3 — Sector Analysis** | Delta-to-best heatmap, sector time comparison across all drivers |
| **4 — Tyre Strategy** | `plot_tyre_strategy()` — compound stints + pit windows |
| **5 — Position Trace** | Interactive Plotly position chart across all laps |
| **6 — What-If Simulator** | `StrategySimulator` class — pit sweep, time delta, finish position |
| **7 — Race Debrief** | `race_debrief()` — auto-generated report, position gains, strategy verdicts |

---

##  How the What-If Simulator Works

The `StrategySimulator` models two key forces:

**Undercut** (pit earlier):
- Fresh tyre pace advantage = `base_pace_adv × remaining_laps`
- Cost = track position surrendered during in-lap + pit stop time loss (~22s)

**Overcut** (pit later):
- Track position held longer
- Cost = tyre degradation builds up: `deg_rate × extra_laps_on_worn_tyre`

Degradation rates are **fitted from real lap time data** using linear regression on stint laps, giving circuit-specific deg curves per compound.

---

##  Tech Stack

| Tool | Purpose |
|---|---|
| `FastF1` | Official F1 telemetry, lap times, tyre data, GPS position |
| `Pandas` | Data manipulation, stint detection, session EDA |
| `NumPy` | Numerical computation, degradation curve fitting |
| `Matplotlib` | Circuit heatmaps, strategy bars, static plots |
| `Plotly` | Interactive position trace, hover charts |
| `Scikit-learn` | Strategy prediction model (Phase 2) |
| `Jupyter` | Portfolio notebook — renders beautifully on GitHub |

---

##  Sample Outputs — Monaco GP 2023

### Circuit Speed Heatmap
> Green = Fast · Red = Slow · White = S/F line

### Tyre Strategy
```
VER  ████████████████░░░░ MEDIUM (L1-29) → ▲ → ░░░░░░░░░░░░░░░ HARD (L30-78)
ALO  ████████████████████████████████████████ MEDIUM (L1-78) — No Stop
LEC  ██████░░░░░░░░░░░░░░ SOFT (L1-24) → ▲ → ░░░░░░░░░░░░░░░░░ MEDIUM (L25-78)
HAM  ████████████████░░░░ MEDIUM (L1-32) → ▲ → ░░░░░░░░░░░░░░░ HARD (L33-78)
SAI  ████████░░░░░░░░░░░░ SOFT (L1-27) → ▲ → ░░░░░░░░░░░░░░░░░ MEDIUM (L28-78)
```

### What-If Result — HAM Lap 25 vs Actual Lap 32
```
Actual pit    : Lap 32  →  Finish: P4
Simulated pit : Lap 25  →  Finish: P3  ▲+1 position
Time delta    : -4.8s faster than actual strategy
Verdict       :  BETTER — Earlier undercut captures track position vs Sainz
```

---

##  Supported Circuits

FastF1 supports all circuits from **2018 onwards**:

`Bahrain` · `Saudi Arabia` · `Australia` · `Azerbaijan` · `Miami` · `Monaco` · `Spain` · `Canada` · `Austria` · `Silverstone` · `Hungary` · `Spa` · `Zandvoort` · `Monza` · `Singapore` · `Japan` · `Qatar` · `USA (COTA)` · `Mexico` · `Brazil` · `Las Vegas` · `Abu Dhabi`

---

##  Roadmap

- [x] Circuit heatmaps (speed, brake, gear, sector)
- [x] Tyre strategy visualization
- [x] What-If pit stop simulator
- [x] Sector analysis heatmap
- [x] Position trace (interactive)
- [x] Race debrief report
- [x] Interactive web dashboard (HTML)
- [ ] Safety car probability model per circuit
- [ ] Multi-year circuit comparison (e.g. Monaco 2021 vs 2023)
- [ ] Double-stop simulation
- [ ] Streamlit deployment
- [ ] ML-based pit window predictor

---

## 👤 Author

**Dhanush C**  
  
 [LinkedIn](https://linkedin.com/in/dhanush-c-a0611024a)  
 [GitHub](https://github.com/dhanushc13)

---

## 📄 License

MIT License — free to use, modify, and share.  
Data sourced from [FastF1](https://docs.fastf1.dev) — official F1 timing data.

---

> *Built as a portfolio project targeting Data Analyst and Data Scientist roles.*  
> *Demonstrates: data pipeline design, telemetry analysis, visualization, simulation modeling.*
