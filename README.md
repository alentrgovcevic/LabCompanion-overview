# LabCompanion 🧪

**The lab bench in your pocket.** An iOS and Android app for wet-lab scientists — calculators, protocol timers, and reference cards in one fast, offline-first, privacy-respecting app.

[App Store](https://apps.apple.com/dk/app/labcompanion-lab-calculator/id6770509131) · [Google Play](https://play.google.com/store/apps/details?id=dk.ATproductions.labcompanion)

![status](https://img.shields.io/badge/status-live%20on%20App%20Store%20%26%20Google%20Play-success)
![platform](https://img.shields.io/badge/platform-iOS%2018%2B%20%C2%B7%20SwiftUI-blue)
![platform](https://img.shields.io/badge/platform-Android%208%2B%20%C2%B7%20Compose-green)
![data](https://img.shields.io/badge/data-SwiftData%20%C2%B7%20CloudKit-informational)

## What it does
- **10 lab calculators** — dilution (C₁V₁=C₂V₂), molarity, serial dilution, primer Tm, centrifuge RCF↔RPM, cell counting, buffer pH, % solution, resuspension, DNA/RNA concentration — unit-aware, with European-comma support
- **Protocol timers** — multi-step, with background step alerts and **Lock Screen Live Activities**
- **8 offline reference cards** — codon & amino-acid tables, restriction enzymes, antibiotic concentrations, ladders, buffer recipes, safety symbols
- **Lab Notebook** (Premium) — photo + text notes, tags, search, PDF export, iCloud sync

## Architecture
```mermaid
flowchart TD
  subgraph App["iOS app · SwiftUI"]
    UI["5-tab UI: Timers · Calculators · Reference · Notebook · Settings"]
    TS["TimerService (chained notifications)"]
    LA["ActivityKit Live Activity → Lock Screen / Dynamic Island"]
    UI --> TS --> LA
  end
  UI --> DATA["SwiftData store"]
  DATA -->|sync| CK[("CloudKit")]
  UI --> SK["StoreKit 2 · one-time premium + 7-day trial"]
```

The Android app is a native Kotlin port, not a cross-platform build:

```mermaid
flowchart TD
  subgraph Android["Android app · Jetpack Compose"]
    AUI["Compose UI (Material 3)"]
    AL["TimerAlarmScheduler (exact alarms, re-queued after reboot/update)"]
    FS["Foreground service · ongoing timer notification"]
    AUI --> AL
    AUI --> FS
  end
  AUI --> ROOM["Room database · Android Auto Backup"]
  AUI --> PB["Play Billing · one-time premium + 7-day trial"]
```

## Engineering highlights
- **Live Activities** via ActivityKit — the running protocol counts down on the Lock Screen using `Text(timerInterval:)`, so updates are pushed only on step changes (battery-friendly).
- **Suspended-safe notifications** — all remaining step alerts are scheduled upfront with cumulative fire dates, so the phone buzzes at every step boundary even while backgrounded.
- **Resilient persistence** — SwiftData with a CloudKit → local → in-memory fallback ladder so a store failure never silently drops user data.
- **StoreKit 2** one-time non-consumable with a Keychain-backed install-date trial. No ads and no tracking SDKs.
- Accessibility-first: 44pt targets, VoiceOver labels, dark-mode colour hierarchy.

## Tech stack
| Area | Technologies |
|---|---|
| iOS app | SwiftUI, SwiftData, ActivityKit, WidgetKit (iOS 18+) |
| Android app | Kotlin, Jetpack Compose, Room, DataStore (Android 8.0+ / API 26) |
| Sync | CloudKit (iOS) · Android Auto Backup |
| Monetization | StoreKit 2 · Google Play Billing (no ads) |

## Screenshots
<p>
  <img src="docs/screenshots/1-welcome.jpg" width="200" alt="Onboarding">
  <img src="docs/screenshots/2-live-activity.jpg" width="200" alt="Protocol timer and Lock Screen Live Activity">
  <img src="docs/screenshots/3-calculators.jpg" width="200" alt="Calculators and reference cards">
</p>

---
© 2026 Alen Trgovcevic · AT Productions (CVR 46159640). Source code is private; this repository is a public architecture overview.
