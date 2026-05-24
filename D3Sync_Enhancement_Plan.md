# D3-Sync Enhancement Plan

## Product Direction

D3-Sync is the app name and user-facing brand. Solis was the initial working name.

Primary promise: help users estimate daily vitamin D coverage by combining UV conditions, personal skin/exposure settings, supplement logging, and HealthKit sync.

## ✅ Completed (v1.0)

- [x] Core UV index display with WeatherKit
- [x] D3 synthesis calculator (IU/min based on UV, skin type, exposed skin)
- [x] Supplement logging with HealthKit sync (IU → mcg)
- [x] Daily goal tracking with circular progress
- [x] Hourly UV forecast chart (Swift Charts)
- [x] Synthesis window card with exposure recommendation
- [x] User profile persistence (App Group shared UserDefaults)
- [x] Widget showing UV + daily goal progress as a Fitness-style ring
- [x] Local notifications: supplement reminder + sun window alerts
- [x] Background app refresh for periodic UV checks
- [x] Production localization baseline: English, Polish, Spanish
- [x] All managers marked @MainActor for thread safety
- [x] Entitlements: HealthKit, WeatherKit, App Group
- [x] Settings: profile, goals, notifications, Pro section, version info, dismiss button
- [x] Version bumped on app update: v1.3.2 build 7
- [x] Widget default-goal fallback hardened so first-run progress is based on 4000 IU, not 0 IU
- [x] Medical disclaimer (localized)

## 🔜 Next Build Steps

1. Replace simplified D3 calculation with documented methodology and safer upper-bound exposure guidance.
2. Store separate records for supplements, sun exposure sessions, UV samples, and totals (Core Data entities instead of only `currentDailyIU`).
3. Implement two-way HealthKit sync — read today's dietary vitamin D samples and merge only app-owned sun synthesis estimates locally.
4. Add StoreKit 2 subscription support with entitlement state, restore purchases, and visible subscription-management screen.
5. Convert smart sun-window alerts into a premium entitlement while keeping basic manual logging free.
6. Validate widgets and Live Activity on physical devices with actual D3-Sync states: current UV, next sun window, daily progress, active exposure timer.
7. Add exposure timer feature with burn-risk guardrails.
8. Add exportable history (CSV/PDF).

## Premium Packaging

**Free:**
- Current UV index
- Basic daily goal
- Manual supplement logging
- Basic widget

**D3-Sync Pro:**
- Smart sun-window alerts
- Background UV planner
- Weekly HealthKit trends
- Advanced widgets and Live Activity
- Exposure timer with burn-risk guardrails
- Exportable history

Recommended subscription products:
- `d3sync.pro.monthly` — USD 2.99–4.99
- `d3sync.pro.yearly` — USD 19.99–29.99
- `d3sync.pro.lifetime` — USD 49.99–79.99
