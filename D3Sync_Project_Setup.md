# Projekt: D3-Sync — Inteligentny Asystent Witaminy D3

D3-Sync to aplikacja na iOS, która pomaga użytkownikom optymalizować poziom witaminy D3 poprzez łączenie danych o promieniowaniu UV w czasie rzeczywistym z logowaniem suplementacji.

## 1. Nazwa Projektu

**Nazwa:** D3-Sync  
**Nazwa robocza historyczna:** Solis  
**Slogan:** Twoje osobiste słońce pod kontrolą.

## 2. Podstawowa Architektura

* **Platforma:** iOS 17+ (SwiftUI, @Observable, Swift Concurrency)
* **Lokalizacja:** Xcode String Catalog (`Localizable.xcstrings`) — produkcyjny zestaw QA: en, pl, es
* **Frameworki:**
    * **WeatherKit:** Pobieranie indeksu UV, prognozy godzinowej i danych pogodowych.
    * **HealthKit:** Odczytywanie i zapisywanie danych o suplementacji witaminy D.
    * **Core Location:** Określanie położenia dla kalkulacji UV.
    * **WidgetKit:** Widżet na ekranie głównym z bieżącym indeksem UV i postępem celu.
    * **UserNotifications:** Przypomnienia o suplementacji i alerty okna słonecznego.
    * **BackgroundTasks:** Okresowe sprawdzanie UV w tle (BGAppRefreshTask).
    * **Charts:** Wykres prognozy UV (godzinowy).

## 3. Architektura Kodu

```
ios/D3-Sync/
├── D3_SyncApp.swift              # @main, environment injection, background task
├── ContentView.swift             # Główny ekran: UV, prognoza, okno syntezy, postęp
├── SettingsView.swift            # Profil, cele, powiadomienia, Pro, wersja
├── SupplementView.swift          # Logowanie dawki witaminy D
├── Persistence.swift             # Core Data (NSPersistentContainer)
├── Localizable.xcstrings         # String Catalog — komplet en/pl/es
├── Managers/
│   ├── WeatherManager.swift      # @MainActor @Observable — WeatherKit + prognoza
│   ├── LocationManager.swift     # @MainActor @Observable — CLLocationManager
│   ├── HealthKitManager.swift    # @MainActor @Observable — zapis/odczyt witaminy D
│   ├── NotificationManager.swift # @MainActor @Observable — UNUserNotificationCenter
│   └── BackgroundRefreshManager.swift  # BGAppRefreshTask scheduling
├── Models/
│   ├── D3Calculator.swift        # Algorytm IU/min, okno syntezy, rekomendacje
│   ├── D3SyncConstants.swift     # App group ID, identyfikatory, domyślne wartości
│   └── UserProfile.swift         # @Observable — UserDefaults (app group shared)
ios/D3_SyncWidget/
├── D3_SyncWidget.swift           # Widget z pierścieniem Fitness + postęp celu
├── D3_SyncWidgetLiveActivity.swift # Live Activity aktywnej sesji
├── D3_SyncWidgetControl.swift    # Control Widget alertów słonecznych
└── D3_SyncWidgetBundle.swift     # Widget bundle
```

## 4. Logika Aplikacji (Kluczowe Funkcje)

### A. Moduł Analizy Środowiska
* Pobieranie `uvIndex` z Apple WeatherKit.
* Prognoza godzinowa UV na cały dzień (wykres Charts).
* Określanie okna syntezy — czas, kiedy UV ≥ 3 pozwala na produkcję D3.

### B. Kalkulator Personalizacji (Algorytm)
Obliczanie szacowanej produkcji witaminy D na podstawie:
* Indeksu UV (0–11+).
* Typu karnacji (Skala Fitzpatricka 1–6).
* Procentu odkrytego ciała (0.1–1.0).
* Rekomendacja czasu ekspozycji (5–60 min) na podstawie pozostałej dziennej dawki.

### C. Moduł Suplementacji
* Interfejs logowania dawki (IU, krok 400).
* Integracja z Apple Health (zapis w µg).
* Dzienny cel z progresem.

### D. Powiadomienia i Tło
* Przypomnienie o suplementacji (konfigurowalna godzina).
* Alert okna słonecznego (UV ≥ 3, z rekomendacją minut).
* Background app refresh (BGAppRefreshTask co 30 min).

## 5. Wymagania UI/UX

* **Minimalizm:** Czysty interfejs z glassmorphism (.regularMaterial).
* **Główny widok:** Duży wskaźnik UV z dynamicznym gradientem.
* **Karta prognozy:** Wykres UV z Chart framework.
* **Karta okna syntezy:** Rekomendacja ekspozycji + przycisk alertów.
* **Progress bar:** Kołowy wskaźnik dziennego celu.
* **Widget:** Pierścień w stylu Apple Fitness pokazujący dzienny postęp D3 względem celu IU.

## 6. Lokalizacja

Aplikacja używa Xcode String Catalog (`Localizable.xcstrings`).

Obsługiwane języki QA: **English, Polski, Español**

Wszystkie stringi UI przechodzą przez `String(localized:)` lub `Text("key")` z LocalizedStringKey. Powiadomienia i stringi z D3Calculator również zlokalizowane.

## 7. Bezpieczeństwo i Disclaimer

Aplikacja zawiera widoczny komunikat (zlokalizowany):
> "D3-Sync dostarcza jedynie szacunkowych danych. Przed zmianą suplementacji skonsultuj się z lekarzem i zbadaj poziom 25(OH)D we krwi."

## 8. App Group i Widget

* App Group: `group.AJWA.D3-Sync`
* Shared UserDefaults keys: `sharedUvIndex`, `currentDailyIU`, `dailyGoalIU`, `lastLatitude`, `lastLongitude`
* Widget odczytuje dane z shared UserDefaults i wyświetla UV + postęp celu w pierścieniu.
* `dailyGoalIU` zawsze ma bezpieczny default 4000 IU, żeby widget nie pokazywał celu `0` przy pierwszym uruchomieniu.
