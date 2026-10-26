# AI Architecture & Project Roadmap (caliborg, magorg, homeorg)

## Grundprinzipien
1. **API Credits (100 USD/Monat):** Nur für wiederkehrende, automatisierte Hintergrund-Pipelines (Import-Hooks, Cronjobs).
2. **Claude Max Quota (Claude Code interaktiv):** Für einmalige Migrations-, Cleanup- und Backfill-Aufgaben.
3. **Exit-Strategie (Kein Vendor Lock-in):** Alle laufenden KI-Aufrufe laufen über einen `LLMService`-Adapter (`AI_PROVIDER=anthropic | gemini | ollama`). Kernsysteme funktionieren bei abgeschalteter KI ohne Datenverlust weiter.

---

## Phase 1: Aktive Umsetzung (Automatisierte API-Pipelines)

### 1.1 `caliborg` – Provider-Abstraktion & Spoilerfreie Tiefen-Zusammenfassung
- **Ziel:** Kapselung der KI-Aufrufe hinter einem Provider-Adapter (`anthropic` als neuer Standard mit Prompt Caching, `gemini` und `ollama` als Fallback).
- **Feature:** Beim automatisierten Anreichern neuer Bücher wird neben der bestehenden Genre-Taxonomie eine tiefgehende, spoilerfreie Zusammenfassung (auf Basis von Inhaltsverzeichnis + Leseprobe statt nur Klappentext) generiert. Keine neuen Metadaten-Spalten in Calibre.

### 1.2 `magorg` – KI-Markdown-Reflow & Themen-Radar beim Import
- **Ziel:** Automatische Veredelung neu importierter PDF-Ausgaben auf `/Volumes/SSD0/NewsMedia`.
- **Features:**
  1. **Markdown-Reflow:** Bereinigung von Spaltenumbrüchen, Silbentrennungen und Bildunterschriften zu sauberem Lese-Markdown (statisch gecacht auf SSD/DB).
  2. **Inhaltsverzeichnis & Themen-Radar:** Strukturierter Artikel-Index inkl. 2-Satz-Teaser, Lesezeit und Highlighting für relevante Schwerpunkte (Geschichte, Technik, Radsport, Wirtschaft, Wissenschaft).
- **Fallback:** Klassischer heuristischer PDF-Parser, falls `AI_PROVIDER=none`.

### 1.3 `homeorg` – Wöchentlicher Energie-Analyst & Hybrid-Steuerung
- **Ziel:** Ausbau des `energy`-Moduls von Monitoring zu einer 2-Schichten-Steuerung (Wolf CHA-10, Fronius GEN24, BYD HVS, Großverbraucher).
- **Architektur:**
  1. **Schicht 1 (Deterministischer Controller):** Regelbasiertes Skript für PV-Überschuss-Steuerung (CHA-10 Sollwertanhebung, Batterie-Ladestrategie, Zeitfenster für Waschmaschine/Trockner/Geschirrspüler/künftiges EV) auf Basis parametrisierter Grenzwerte in Supabase (`energy_control_params`).
  2. **Schicht 2 (KI-Analyst via Cronjob):** Wöchentliche Auswertung der Telemetrie (Taktverhalten, Verschattungsverluste, Wärmeverlust des Hauses) mit Text-Report in `energy_reports` und konkreten Tuning-Vorschlägen für `energy_control_params`.

---

## Phase 2: Geparktes Backlog (Spätere Umsetzung)

### 2.1 `homeorg` & Übergreifend – Smart DMS & Beleg-/E-Mail-Pipeline (Eigenes Projekt)
- **Umfang:** Übergreifendes Dokumenten-Management-System inkl. Abgriff von E-Mail-Postfächern (`mailbox.org`) und Upload-Dropzone.
- **Funktionen:** Automatische Klassifizierung, konsistente Dateibenennung, strukturierte Ablage, Datenextraktion (Rechnungen, Handwerker, Reisebuchungen) und Vorab-Befüllung/Abgleich mit Supabase-Buchungen.

### 2.2 `homeorg` – Historisches Datenbank-Cleanup (Manuelle Einmal-Aufgabe via Claude Code)
- **Umfang:** Interaktiver Lauf über historische Buchungen (Ninox-Altlasten) in Supabase zur Normalisierung von Händlernamen, Bereinigung von Kategorien und Prüfung auf Doppelerfassungen/50-50-Splits.

### 2.3 `magorg` – Archiv-Backfill (Manuelle Einmal-Aufgabe via Claude Code)
- **Umfang:** Einmalige Nachindexierung und Reflow-Generierung für bereits vorhandene PDF-Ausgaben im Bestandsarchiv über interaktive Claude-Code-Läufe.
