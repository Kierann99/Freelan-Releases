<p align="center">
  <img src="Logo.png" alt="Freelan Logo" width="280" />
</p>

<h1 align="center">Freelan</h1>

<p align="center">
  <strong>A Single Workspace for Warehouse Operational Control</strong>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/version-2.8.2-blue" alt="Version" />
  <img src="https://img.shields.io/badge/platform-Windows-0078D6?logo=windows" alt="Platform" />
  <img src="https://img.shields.io/badge/electron-42-47848F?logo=electron" alt="Electron" />
  <img src="https://img.shields.io/badge/react-18.3.1-61DAFB?logo=react" alt="React" />
</p>

---

## Overview

**Freelan** is a Windows desktop application (with a companion web app) built with Electron and React that consolidates warehouse operations into a single unified workspace. It replaces the need for multiple browser tabs, spreadsheets, and standalone tools by embedding key business systems alongside purpose-built operational modules — all in one window.

Freelan has evolved from a basic warehouse management tool into a full operational control platform, with deep productivity analytics and real-time, multi-site data synchronisation across all Korber sites.

---

## Key Features

### 🏭 Warehouse Operations
- **Data Import** — Drag-and-drop Excel import with automatic column mapping for operative, picking, sorting, and putaway data
- **Performance Reports** — Session-based productivity analytics with per-operative metrics (units, UPH, expected units, Prod %, average pick/sort/put times, hours worked vs. unaccounted time)
- **QC Checks** — Random sample generation from putaway data with tri-state pass/fail, inline notes, and progress counters
- **Task Planner** — Weekly task scheduling with real-time sync across shifts
- **Shift Handover** — Structured handover documentation with print support
- **Adjustments & Delivery Adjustments** — Log and track warehouse and delivery adjustments, with cell-level undo/redo
- **Warehouse Map** — Interactive bay-level visualisation with aisle floor views and adjustment heatmaps
- **Workload Calculator** — Estimate completion times for picking, sorting, and putaway workloads

### 📈 Productivity & Operative Profiles
- **Weekly Productivity** — Import a financial period's transactions once; it's stored in a site-shared, rolling 12-month history everyone can see
- **Per-shift Deep-Dive** — A per-operative modal with charts breaking a shift down by activity
- **Operative Profiles** — A per-operative deep-dive with per-period and per-week productivity trends, KPI cards, an activity mix, and a sortable breakdown table
- **Accurate Productivity %** — Generated-hours model where receiving, staging, and take-item time don't distort the score, so productivity reflects core warehouse work
- **Most & Least Productive activity + Generated Hours** — Highlights each person's strongest and weakest activity (units, hourly rate, Prod %) and the standard hours earned
- **Performance Conversations** — A site-shared, per-operative log of 1:1s, coaching, recognition, and concerns
- **1:1 Period Check-in prints** — One click produces a two-page pack per operative (KPI cards, trend charts, and that period's conversations, plus a signable feedback form). Print a single operative or bulk-print an entire period

### 🔗 Embedded Integrations
All embedded via Electron BrowserView with full interactivity:
- **Korber WMS** — Warehouse management system
- **Outlook** — Email and calendar with unread notification badges
- **Microsoft Teams** — Chat and collaboration with notification badges
- **Argos** — Argos website for stock checking
- **Colleague Hub** — Access to the full colleague hub
- **UKG** — Workforce management (scheduling, attendance)
- **Safety Conversations** — Health & safety conversation form

### 📊 Analytics Engine
- Unified-timeline session detection across all activity types
- Automatic break detection (≥ 15 min gaps)
- Per-activity generated (standard) hours and actual hours tracking
- Weighted, size-aware (Körber SMV) expected-rate blending
- Hours worked vs. hours not accounted for breakdown
- Export to Excel and print / save-as-PDF support

### 🔄 Multi-Site Sync
- **Supabase** — Real-time bidirectional sync (Postgres + Row Level Security) across devices and shifts
- **Offline write queue** — Pending cloud writes are persisted locally and replayed when connectivity returns; permanent (bad-request) writes are dropped cleanly instead of retrying forever
- **Cross-Window Sync** — Data consistency across popout windows
- Site-specific data isolation for all Korber sites

### 🔐 Accounts & Security
- Supabase authentication with a dedicated login page
- In-app password reset via one-time code, with password-strength requirements
- Chromium sandbox enabled for the main renderer and a tightened webview preload
- Central crash/error reporting and an app-wide Error Boundary so a crash in one tab never white-screens the whole app

### 🖥️ Desktop Experience
- Custom frameless window with draggable title bar
- Dark / Light mode with persistent theme
- Tab popout system — middle-click any tab to open in its own window
- Auto-update from GitHub Releases (MSI installer)
- Keyboard navigation and context menus
- Performance monitor in the title bar
- Barcode generation and printing (CODE128)
- Downloads panel for managing file downloads
- Web companion app deployed via Firebase Hosting

---

## Tech Stack

| Layer | Technology |
|-------|------------|
| **Desktop** | Electron 42 |
| **UI** | React 18 + Vite 5 |
| **Data Tables** | TanStack React Table 8 + React Virtual 3 |
| **Charts** | Chart.js 4 + Day.js |
| **Icons** | Lucide React, React Icons, HugeIcons, MDI |
| **Excel I/O** | SheetJS (xlsx 0.20.3) |
| **PDF** | pdf.js (pdfjs-dist) |
| **Barcodes** | react-barcode + JsBarcode |
| **Screenshots** | html2canvas |
| **Auth & Data** | Supabase (Auth + Postgres with Row Level Security) |
| **Auto-Update** | electron-updater + GitHub Releases |
| **Build** | electron-builder (MSI) |
| **Web Deploy** | Firebase Hosting |
| **Testing** | Vitest |
| **CI/CD** | GitHub Actions (release.yml + firebase-deploy.yml) |

---

## Project Structure

```
├── .github/workflows/       # CI/CD (release.yml, firebase-deploy.yml, build-local.yml)
├── electron/                # Electron main process
│   ├── main.js              # Main process entry
│   ├── preload.js           # Renderer preload bridge
│   ├── ipc/                 # Modular IPC handlers (window, files, print, updates, settings, app-data…)
│   ├── browserViewManager.js# Embedded BrowserView management
│   └── updater.js           # Auto-update logic
├── src/
│   └── renderer/
│       ├── components/      # React tabs, modals & shared UI
│       │   ├── productivity/ # Productivity engine helpers & per-shift detail
│       │   ├── handover/     # Handover config & time helpers
│       │   └── map/          # Warehouse map bays & modals
│       ├── services/        # Supabase, auth, data sync, site config
│       │   └── __tests__/    # Vitest business-logic tests
│       ├── hooks/           # Custom React hooks
│       ├── utils/           # Utility functions
│       ├── App.jsx          # Root application component
│       └── style.css        # Global styles (dark/light mode)
├── public/                  # Static assets (itemsDb.json, icons)
├── documentation/           # Feature documentation and methodology
├── dist/                    # Built renderer output (desktop)
└── dist-web/                # Built web deployment output
```

---

## Development

```bash
npm install          # Install dependencies
npm run dev          # Run the desktop app (Vite + Electron)
npm run dev:web      # Run the web companion app
npm test             # Run the Vitest test suite
npm run build:local  # Build the renderer + MSI installer (no publish)
```

---

## Documentation

Detailed documentation is available in the `documentation/` folder covering feature implementation, fixes, and methodology across all modules.

---

## Author

**Kieran Cutter**


