# FocusSound

A browser-based focus timer that uses your webcam to detect whether you're paying attention, and plays a boxing bell to keep you on track.

## Features

- **Pomodoro-style rounds** — configurable focus / break durations and round count
- **Webcam attention detection** — monitors whether you're looking at the screen during focus time
- **Boxing bell sound engine** — realistic inharmonic bell synthesized via Web Audio API (no audio files required)
  - 3-strike bell on focus start
  - 1-strike bell on break start
  - Alert buzz when attention is lost
  - Celebratory 3-strike finish
- **Session report** — shows focus rate, total focused time, and distraction count per session
- **Pure static app** — single `index.html`, no framework, no dependencies

## Demo

Open `index.html` directly in any modern browser, or run locally:

```bash
npm start          # serves index.html on http://localhost:3000
npm run preview    # serves the built public/ folder
```

## Getting Started

```bash
# Clone
git clone https://github.com/ho7677/focus-sound.git
cd focus-sound

# Run (no install needed)
npm start
```

Then open http://localhost:3000 and allow webcam access.

## How It Works

1. Enter your focus time, break time, and number of rounds on the setup screen.
2. Click **Start** — the webcam activates and the timer begins.
3. If you look away, the alert sound fires and your distraction count increases.
4. A bell rings at the start and end of every focus / break phase.
5. After all rounds complete, a report shows your focus rate.

## Tech Stack

| Layer | Technology |
|-------|-----------|
| UI | Vanilla HTML / CSS / JavaScript |
| Audio | Web Audio API (synthesized bell) |
| Vision | MediaPipe Face Detection (via CDN) |
| Hosting | Azure Static Web Apps |
| Deploy | Azure Developer CLI (`azd up`) |

## Build & Deploy

```bash
npm run build        # copies assets to public/
npm run azure:up     # provision + deploy to Azure Static Web Apps
```

## Project Structure

```
focus-sound/
├── index.html          # entire app (single file)
├── server.js           # local dev server
├── package.json
├── scripts/
│   └── build-static-site.mjs
├── infra/
│   └── main.bicep      # Azure infrastructure
└── azure.yaml          # azd configuration
```

## License

MIT
