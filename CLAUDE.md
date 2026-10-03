# ADC Kompass — Projektkontext für Claude Code

Single-Page-Orientierungstool für neue/bestehende ADC-Mitglieder: zeigt Sektionen (Städte),
Fachbereiche (Disziplinen), Initiativen/Ideen und "Büro & Ressourcen" (Kontakte, Formulare, CI).

- **Live:** https://adc-compass.vercel.app
- **Repo:** github.com/andrehennen/adc-compass (public)
- **Deploy:** Vercel, Auto-Deploy bei Push auf `main`. Keine Build-Schritte nötig.
- **Owner/Autor:** André Hennen (andre.hennen@adc.de), CCO Curious Company, incoming Sektionsvorstand ADC Hamburg (offiziell ab JHV Okt. 2026)
- **Schwester-Projekt:** `andrehennen/adc` → ADC HH Dashboard (adc-hh-dashboard.vercel.app) — Ideen-/Initiativen-Tracking für Hamburg, gleiche Pipeline (GitHub+Vercel), teilt CI-Assets/Favicons

## Struktur

Alles in **einer** Datei: `index.html` (Vanilla HTML/CSS/JS, kein Framework, kein Build-Step).
Assets liegen direkt im Repo:
- `ci/` — echte ADC-Logos (PNG) + das ADC Design Manual 2018 (PDF)
- `formulare/` — Mitgliedschafts-PDFs (Stand 2020, teils veraltete Namen, als Übergangslösung markiert)
- `favicon*.png/ico`, `apple-touch-icon.png`

### Navigation
Kompass-Rose mit 4 Himmelsrichtungen (bewusst reduziert von 6 auf 4 — mehr wirkte überladen):
Sektionen 🗺️ · Fachbereiche 🎨 · Ideen & Initiativen 💡 · Büro & Ressourcen 🗂️
Die Rose selbst ist **monochrom** (kein Farbcode pro Richtung) — Farbe lebt nur in Emojis/Akzenten.

### Datenmodelle (alle als JS-Objekte/Arrays im `<script>`)
- `sektionen` — 7 Städte (hamburg, berlin, duesseldorf, dresden, frankfurt, stuttgart, muenchen), je mit `name, role, accent, emoji, people:[{who,what}], text`. Hamburg hat zusätzlich `transition` (Übergabe-Hinweis) und einen Link zum Ideen-Dashboard.
- `fachbereiche` — 7 Disziplinen, **alphabetisch sortiert**: Design, Digitale Medien, Editorial, Film & Ton, Forschung & Lehre, Spatial Experience, Werbung
- `initiativen` — Ideen-Dashboard Hamburg, ADC Talents, Welcome to Creativity, Creative Club, ADC Beats, LADC, Future Females, Future Diversity, Mentoring, Speed-Recruiting, Fördermitglieder
- `ci` — 5 Karten aus dem echten 2018-Manual: Typografie, Farben (mit echten Swatches), Logo & Bildmarke, Bildsprache, Vorlagen & Formate — plus feste Karte "Offizielles CI-Manual" (PDF-Link)
- `formulare` — Mitgliedsantrag/Bewerbung, Stimmübertragung/Vollmacht (Platzhalter, "Vorlage folgt"), Anträge an den Vorstand (direkt an mitglieder@adc.de, Format: "Ich beantrage, dass …")

### Germany-Karte
Echte Geografie aus `isellsoap/deutschlandGeoJSON` (GitHub, `1_deutschland/4_niedrig.geo.json`),
per Python zu einem SVG-Path konvertiert (äquirektangulare Projektion, korrigiert mit `cos(mean_lat)`,
~160 Punkte subsampled). 7 Pins mit Puls-Animation (`.ping`), gestaffelt per `nth-of-type` Delay.
Karte ist `position:sticky`, Sektions-Karten scrollen daneben; Klick auf Pin → `scrollIntoView` zur Stadtkarte.

## Design-Entscheidungen (bitte respektieren)

- **Farben:** `--bg:#000`, `--fg:#fff`, Akzentpalette: coral `#FF5A3C`, teal `#00A896`, gold `#FFC145`,
  indigo `#5D5FEF`, pink `#EF476F`, sky `#3AA6FF`, purple `#9B59B6`, lime `#B4D64B`.
  Grundsatz nach mehreren Korrekturrunden: **sparsam** einsetzen — nur in Emojis, kleinen Akzenten,
  Buttons. NICHT großflächig auf Karten/Borders/Icons (wurde explizit zurückgebaut, "wird sofort zuviel").
- **Typografie:** Poppins (500/600/700/800) als freier Ersatz für ADC's proprietäre Centra No. 2
  (Le Jeune Deck als Akzent-Serife — rechtlich nicht redistributierbar, daher Substitut).
  **Keine kursiven/italic Schriften.**
- **Hover-Effekt:** kein Full-Invert (schwarz↔weiß) mehr — führte zu unlesbarem weiß-auf-weiß durch
  CSS-Specificity-Konflikte. Jetzt nur Border/Box-Shadow-Hover.
- **Kontakte:** Namen sind direkt als `<a href="mailto:...">Name</a>` verlinkt, keine separaten
  E-Mail-Buttons/Text mehr daneben (außer bei generischen Rollen ohne Personenname, z. B. "Mitgliederbetreuung").

## E-Mail-Konvention

ADC-Adressen folgen dem Muster `vorname.nachname@adc.de`. Mehrere Adressen sind über ein 2020er-PDF
bestätigt und aktuell; **andere sind musterbasierte Annahmen** — beim Hinzufügen neuer Personen
immer transparent machen, ob die Adresse bestätigt oder nur abgeleitet ist. **Nie Kontaktdaten erfinden.**

## Workflow / Gotchas

- Kein Build-Step — einfach `index.html` editieren, committen, pushen.
- Vercel deployt automatisch bei Push auf `main`.
- Bei großen Binär-Payloads (PDFs/PNGs) über die GitHub Contents API: niemals base64 direkt als
  `-d`-Argument an curl übergeben (`Argument list too long`) — immer über eine temp. JSON-Datei
  und `curl --data @payload.json`. (Lokal mit normalem `git push` ohnehin irrelevant — betrifft nur
  den API-Push-Pfad aus einer Cloud-Session ohne lokalen Git-Zugriff.)
- Repo ist **public** — entsprechend sind alle gehosteten PDFs/Kontaktdaten öffentlich sichtbar.
  Falls das geändert werden soll: Privat schalten würde GitHub Pages/Vercel-Freetier-Setup berühren,
  noch nicht final entschieden.
- Footer-Signatur-Format: "Stand [Monat Jahr] · Zusammengetragen und erstellt von André Hennen,
  Curious Company · Datenquellen: ADC Büro, adc.de, Mitglieder und Ideen-Dashboard" — bei Content-Updates
  "Stand" ggf. aktualisieren.

## Offene Punkte

- Stimmübertragung/Vollmacht-Formular: noch Platzhalter, Vorlage folgt
- Mitgliedschafts-PDFs: Stand 2020, Namen teils veraltet — Update angekündigt
- Private vs. Public Repo: noch offene Entscheidung (gilt auch für Schwester-Projekt `adc`)
