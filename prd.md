# PRD — FocusSound

**Version:** 1.0  
**Status:** Shipped  
**Last Updated:** 2026-06-20

---

## 1. Overview

FocusSound is a browser-based focus timer that combines the Pomodoro technique with real-time webcam attention detection. When you lose focus, a boxing bell fires an alert. When a phase ends, the bell marks the transition — keeping you in a rhythm without needing to watch a screen.

### Problem

Remote workers and students struggle to maintain focus during deep-work sessions. Existing timers are passive — they count down but don't respond to the user's actual attention state.

### Solution

A lightweight single-page app that uses the device camera to detect whether the user is present and looking at the screen. Attention loss triggers an audible alert immediately, nudging the user back on task without any manual intervention.

---

## 2. Goals

| Goal | Metric |
|------|--------|
| Keep the user on-task during focus phases | Focus rate ≥ 80% per session |
| Require zero installation or server | Works by opening index.html |
| Provide clear session feedback | Report screen after every session |
| Be unintrusive during breaks | No camera / no sound during break phases |

---

## 3. Non-Goals

- Not a team / collaboration tool
- Not a habit tracker or streak manager
- Not a mobile app (desktop browser only for webcam support)
- No user accounts or data persistence across sessions

---

## 4. Target Users

- Solo developers, designers, or students doing focused work
- Anyone familiar with the Pomodoro technique who wants an automated attention layer

---

## 5. User Stories

| ID | As a... | I want to... | So that... |
|----|---------|-------------|------------|
| U1 | User | Configure rounds, focus time, and break time before starting | I can adapt the session to my schedule |
| U2 | User | Hear a bell when a focus phase starts | I know it's time to concentrate |
| U3 | User | Hear an alert if I look away during focus | I'm reminded to return to work |
| U4 | User | Hear a bell when a break starts | I know it's time to rest |
| U5 | User | Pause and resume the timer | I can handle unexpected interruptions |
| U6 | User | See a report at the end of the session | I can review how focused I was |

---

## 6. Features

### 6.1 Setup Screen
- Input: number of rounds (default 4, range 1–20)
- Input: focus duration in minutes (default 25, range 1–120)
- Input: break duration in minutes (default 5, range 1–60)
- Start button launches the session

### 6.2 Timer Screen
- Displays current round (e.g. "Round 2 / 4")
- State badge: **집중** (focus) or **휴식** (break)
- Countdown timer in MM:SS format
- Live webcam feed with face-detection status indicator
- Running totals: cumulative unfocused seconds, detection count
- Pause / Resume button
- End Session button

### 6.3 Attention Detection
- Uses MediaPipe Face Detection (loaded via CDN)
- Runs only during focus phases — camera is idle during breaks
- Triggers `soundAlert()` after ≥ 3 consecutive seconds without a detected face
- Increments unfocus counter once per continuous absence streak
- Status indicator: 🟡 initializing / 🟢 face detected / 🔴 no face

### 6.4 Boxing Bell Sound Engine
- Built entirely with the Web Audio API — no audio files
- Inharmonic partial synthesis for a realistic bell timbre
- Four sound events:

| Event | Pattern | Trigger |
|-------|---------|---------|
| Focus start | 3 strikes, 0.2 s apart | Phase switches to focus |
| Break start | 1 strike | Phase switches to break |
| Attention alert | Low buzzer tone | Face absent ≥ 3 s |
| Session finish | 3 strikes + long decay | All rounds complete |

### 6.5 Session Report
- Total focused time (seconds → MM:SS)
- Total unfocused time
- Focus rate (%) with a visual bar
- Per-round detail breakdown
- "New Session" button to restart

---

## 7. Technical Architecture

| Concern | Approach |
|---------|---------|
| App shell | Single `index.html` — no build step required to run |
| Audio | Web Audio API, synthesized in real time |
| Vision | MediaPipe Face Detection (`@mediapipe/face_detection` via jsDelivr CDN) |
| State machine | Vanilla JS — setup → timer (focus ↔ break loop) → report |
| Build | `npm run build` copies `index.html` to `public/` for deployment |
| Hosting | Azure Static Web Apps (Free tier) |
| IaC | Bicep (`infra/main.bicep`) provisioned via `azd up` |

---

## 8. Constraints

- Requires a modern browser with Web Audio API and `getUserMedia` support (Chrome, Edge, Firefox, Safari 15+)
- Webcam permission must be granted by the user
- MediaPipe model loads from CDN — requires an internet connection on first load
- No backend — all state is in memory and lost on page refresh

---

## 9. Out of Scope (Future Opportunities)

- PWA / offline support
- Mobile layout and touch optimisation
- Streak and history persistence (localStorage or backend)
- Customizable bell sounds
- Multi-user / shared session mode
- Gaze direction detection (not just face presence)
