# ORBITWELL — Health Monitoring App for Astronauts

ORBITWELL is a single-file web app (`orbitwell-integrated.html`) that combines three modules into one crew-health workflow:

1. **Heart Monitor (Pulsefield)**: live and simulated heart-rate monitoring.
2. **Diagnostic Console**: physical telemetry entry, automated analysis, 3D anatomy view, history and a procedures manual.
3. **Psychological Wellness Check-in**: a private 45-question reflection, with an **emergency path that skips the questions** and produces a report straight away.

Everything runs in the browser. There is no server, build step or account.

> **Disclaimer:** ORBITWELL is a reflection and monitoring aid. It is not a medical device, a diagnosis, or a replacement for flight-surgeon guidance. In an emergency, contact your flight surgeon or mission control first.

---

## Quick start

1. Open `orbitwell-integrated.html` in a modern browser (Chrome, Edge, Firefox or Safari).
2. That's it. All code, the check-in app, its hero image and the favicon are embedded in the one file.

Internet access is only needed for web fonts. The app falls back to system fonts without it.

For the **live camera** in the emergency section, open the file over `https://`, `localhost`, or directly as a local file, and allow camera access when the browser asks.

---

## How the workflow fits together

```
Heart Monitor ──► Diagnostic Console ──► Run Analysis & Save Log
                                              │
                                    "Physical diagnostics logged"
                                     (10 s countdown, or click)
                                              ▼
                              Psychological Check-in (full-screen)
                               ├─ Emergency? ► Report immediately
                               └─ No ► 45 questions ► Report
```

- Clicking **⚡ RUN ANALYSIS & SAVE LOG** opens a handoff popup. After the 10-second countdown, or when you click **Continue now**, the check-in opens. **Stay here** or **Esc** cancels the handoff.
- The **5. PSYCH CHECK-IN →** button in the nav opens the check-in directly.
- **← Back to diagnostics** closes the check-in and returns you to the console without a reload.

---

## Modules

### 1. Heart Monitor (Pulsefield)
- Shows current heart rate, a rolling average (last 30 samples), variation (SD) and trend (BPM/min).
- Data can come from a live stream or the demo stream. The source is labelled.
- **SEND TO DIAGNOSTIC CONSOLE →** and **⇣ PULL LATEST** move the cardiac values into the console.

### 2. Diagnostic Console & 3D Anatomy
Inputs, with units shown in the app:

| Group | Fields |
|---|---|
| Radiation | daily dose, cumulative dose, neutron spike |
| Respiratory | SpO₂, FEV1/FVC, cabin ppCO₂ |
| Blood | glucose, WBC, creatinine, vitamin D, ferritin, albumin |
| Urine | specific gravity, pH, calcium, protein/ketones |
| Sleep | total sleep, efficiency, circadian drift, night wake bouts |
| Cardiac | current, average, variation, trend |

- **Run Analysis & Save Log** produces an overall status, a health score (0–100) and a list of findings, each with a recommended protocol.
- **⚠️ Simulate Anomaly** and **↺ Reset Nominal** are for demos and testing.
- **📄 Download PDF Report** exports the analysis.
- Multiple crew profiles are supported (**+ ADD**). The mission day advances with **+1 SOL**, and the habitat is selectable (ISS, Lunar Gateway, Mars Transit, Surface Base).

### 3. Historical Comparison Logs
Saved analyses per crew member, for comparing readings across mission days. **Clear user history** removes one crew member's logs.

### 4. Test Rules & Procedures Manual
In-app reference for the tests and the reasoning behind the findings.

### 5. Psychological Wellness Check-in
- **Emergency gate (first thing you see):** *"Is someone in an emergency?"* → **Yes — emergency report →** skips the questionnaire.
  - Enter who needs help and what is happening, and add up to 5 photos (15 MB each) using the camera or upload.
  - A live-updating report preview is shown, with **Send urgent alert to flight surgeon** and **Download emergency report**.
  - A 🚨 **Emergency** button stays in the check-in header at every step.
- **Questionnaire:** 45 questions across five sections, answered on a Never to Very often scale, with optional follow-up details when an answer is Often or Very often. A *Prefer not to say* option is always available, and safety questions show a support notice.
- **Report:** a text report to download or share with a flight surgeon (see below).

---

## What the reports contain

Both the **emergency report** and the **normal check-in report** start with the physical data from modules 1 and 2:

- **Module 1:** current heart rate, average, variation, trend, data source.
- **Module 2:** crew ID, habitat, sol, overall status and score, radiation, respiratory, blood, urine and sleep values, plus every finding with its protocol.

The emergency report adds the reporter, the crew member in distress, the situation description and a list of attached photos with notes. The normal report adds the check-in responses, follow-up details and any personal-safety disclosure.

**Sharing:** the device's share sheet is used where available. Otherwise the files are downloaded and an email draft opens (the subject is prefilled). Photos and the report must be attached manually in that case. Nothing is ever sent automatically.

---

## Privacy and data handling

- **Check-in answers, emergency details and photos are never saved.** They exist only in memory while the check-in is open and are cleared when it is closed or restarted.
- **Photos stay on the device.** They are only included if you share or download them.
- **Crew profiles, the selected crew member and saved diagnostic logs** are stored in the browser's `localStorage` (`artemis_users`, `artemis_current_user`, and per-user history) so they survive a reload. Use **Clear user history** or clear site data to remove them.
- The only external requests are for web fonts.

---

## Project layout

| File | Purpose |
|---|---|
| `orbitwell-integrated.html` | The complete app: modules 1–2, the embedded check-in (module 3), favicon, and the hero image |
| `README.md` | This file |

### How the check-in is embedded
The check-in is stored inside the main file as base64 text and loaded into a full-screen `<iframe srcdoc>` when opened. When opened, the main page passes in a snapshot of the heart-monitor and diagnostic data, then clears the frame on close. Its questionnaire and report logic comes from the open-source repo [`techy-tej/NASA-astro-psychometric`](https://github.com/techy-tej/NASA-astro-psychometric), bundled into plain JavaScript. The interface was re-implemented without React so it can live in a single HTML file.

---

## Browser support and known limits

- Tested logic paths in a simulated DOM. Please test the camera, sharing and PDF export in your target browsers and devices.
- **Camera:** desktop browsers show a live preview. Phones and tablets open the native camera app. If the camera is blocked or absent, use **Upload photo**.
- **Sharing files:** the Web Share API with files is not available in all browsers. The download-and-email fallback is used there.
- Email drafts can't attach files automatically (a browser limitation).

---

## Customising

- **Change the handoff delay:** edit `HANDOFF_SECONDS` in the main script (default 10).
- **Update the favicon:** replace the `<link rel="icon">` and `apple-touch-icon` data URIs in the `<head>`.
- **Edit analysis thresholds and findings:** see `analyzeTelemetry()` in the main script.
- **Edit check-in questions or report wording:** these come from the bundled check-in logic. The source is the repo linked above.

---

## Credits

- Heart monitor, diagnostic console and integration: ORBITWELL.
- Psychological check-in logic and questionnaire: [`techy-tej/NASA-astro-psychometric`](https://github.com/techy-tej/NASA-astro-psychometric).
