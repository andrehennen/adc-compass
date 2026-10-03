# ADC Kompass — Projektkontext für Claude Code

Single-Page-Orientierungstool für neue/bestehende ADC-Mitglieder: zeigt Sektionen (Städte),
Fachbereiche (Disziplinen), Initiativen/Ideen und "Büro & Ressourcen" (Kontakte, Formulare, CI).

- **Live:** https://adc-compass.vercel.app
- **Repo:** github.com/andrehennen/adc-compass (public)
- **Deploy:** Vercel, Auto-Deploy bei Push auf `main`. Keine Build-Schritte nötig.
- **Owner/Autor:** André Hennen (andre.hennen@adc.de), CCO Curious Company, incoming Sektionsvorstand ADC Hamburg (offiziell ab JHV Okt. 2026)
- **Schwester-Projekt:** `andrehennen/adc` (lokal: `../adc-dashboard`) → „Dein ADC Dashboard“ (adc-germany-dashboard.vercel.app) — Ideen-/Initiativen-Tracking für den ganzen ADC (Sektionen + Gesamtverein), gleiche Pipeline (GitHub+Vercel), teilt CI-Assets/Favicons

## Struktur

Alles in **einer** Datei: `index.html` (Vanilla HTML/CSS/JS, kein Framework, kein Build-Step).
Assets liegen direkt im Repo:
- `fonts/` — Inter (woff2, identisch mit dem Dashboard)
- `ci/` — echte ADC-Logos (PNG) + das ADC Design Manual 2018 (PDF)
- `formulare/` — Mitgliedschafts-PDFs (Stand 2020, teils veraltete Namen, als Übergangslösung markiert)
- `favicon*.png/ico`, `apple-touch-icon.png`

### Navigation
Kompass-Rose mit 4 Himmelsrichtungen (bewusst reduziert von 6 auf 4 — mehr wirkte überladen):
Sektionen 📍 · Fachbereiche 🎨 · Ideen & Initiativen 💡 · Büro & Ressourcen 🗂️
Darunter erscheint beim Scrollen ein Dock mit denselben 4 Bereichen.
Aufbau der Rose (seit v1.5.0): Zifferblatt aus feinen Strichen in der Mitte, die 4 Bereiche als runde Buttons
außen herum (Positionen per CSS über `data-deg`). Die Nadel pendelt leicht (`.needle-sway`) und neigt sich bei
Maus-Hover zum Bereich — Hinweis, dass sie bedienbar ist.
Die Rose selbst ist **monochrom** (kein Farbcode pro Richtung) — Farbe lebt nur in Emojis/Akzenten.

### Datenmodelle (alle als JS-Objekte/Arrays im `<script>`)
- `sektionen` — 7 Städte (hamburg, berlin, duesseldorf, dresden, frankfurt, stuttgart, muenchen), je mit `name, role, accent, emoji, people:[{who,what}], text`. Hamburg hat zusätzlich `transition` (Übergabe-Hinweis) und einen Link zum ADC Dashboard.
- `fachbereiche` — 7 Disziplinen, **alphabetisch sortiert**: Design, Digitale Medien, Editorial, Film & Ton, Forschung & Lehre, Spatial Experience, Werbung
- `initiativen` — Dein ADC Dashboard, ADC Talents, Welcome to Creativity, Creative Club, ADC Beats, LADC, Future Females, Future Diversity, Mentoring, Speed-Recruiting, Fördermitglieder
- `ci` — 5 Karten aus dem echten 2018-Manual: Typografie, Farben (mit echten Swatches), Logo & Bildmarke, Bildsprache, Vorlagen & Formate — plus feste Karte "Offizielles CI-Manual" (PDF-Link)
- `formulare` — Mitgliedsantrag/Bewerbung, Stimmübertragung/Vollmacht (Platzhalter, "Vorlage folgt"), Anträge an den Vorstand (direkt an mitglieder@adc.de, Format: "Ich beantrage, dass …")

### Germany-Karte
Echte Geografie aus `isellsoap/deutschlandGeoJSON` (GitHub, `1_deutschland/4_niedrig.geo.json`),
per Python zu einem SVG-Path konvertiert (äquirektangulare Projektion, korrigiert mit `cos(mean_lat)`,
~160 Punkte subsampled). 7 Pins mit Puls-Animation (`.ping`), gestaffelt per `nth-of-type` Delay.
Karte ist `position:sticky`, Sektions-Karten scrollen daneben; Klick auf Pin → `scrollIntoView` zur Stadtkarte.

## Design-Entscheidungen (bitte respektieren)

- **Gleiches Design-System wie das ADC Dashboard** (seit v1.4.0, Okt. 2026 — vorher schwarz mit Poppins),
  orientiert an adc.de. Tokens 1:1 aus dem Dashboard: `--bg:#fff`, `--soft:#f4f4f2`, `--line:#e2e2df`,
  `--line-strong:#c9c9c5`, `--ink:#0a0a0a`, `--ink-2:#3d3d3d`, `--ink-3:#6b6b6b`, `--radius:12px`.
  Pill-Buttons (999px, 1.5px Ink-Border), sticky Kopfleiste mit Blur, Kicker-Labels 12px/700/uppercase.
- **Farben:** Schwarz/Weiß/Grau. Die Akzentpalette (`--c-coral` etc., `accent` in den Daten) ist definiert,
  wird aber bewusst nicht großflächig genutzt — Farbe lebt in Emojis ("wird sofort zuviel").
- **Typografie:** Inter (variabel 400–800, selbst gehostet in `fonts/`, keine Google Fonts) als freier Ersatz
  für ADC's proprietäre Centra No. 2. Headlines 800 mit negativem Letter-Spacing.
  **Keine kursiven/italic Schriften.**
- **Bewegung ruhig halten:** Hover/Press nur mit `--spring` (kein Überschwingen), `--bounce` ausschließlich für
  die Nadeldrehung beim Klick. Beim Hover nie Schriftgewicht oder Größe von Text ändern, Buttons mit Label
  nicht als Ganzes skalieren (nur den inneren Kreis) — sonst "zuckt" es (Feedback André, 3.10.2026).
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
- **Standardregel (wie im Dashboard-Repo): Jede Änderung am Kompass wird committet und gepusht**, ohne
  nachzufragen. Danach kurz sagen, was sich geändert hat. (Von André am 3.10.2026 so festgelegt.)
- Vercel deployt automatisch bei Push auf `main`.
- Bei großen Binär-Payloads (PDFs/PNGs) über die GitHub Contents API: niemals base64 direkt als
  `-d`-Argument an curl übergeben (`Argument list too long`) — immer über eine temp. JSON-Datei
  und `curl --data @payload.json`. (Lokal mit normalem `git push` ohnehin irrelevant — betrifft nur
  den API-Push-Pfad aus einer Cloud-Session ohne lokalen Git-Zugriff.)
- Repo ist **public** — entsprechend sind alle gehosteten PDFs/Kontaktdaten öffentlich sichtbar.
  Falls das geändert werden soll: Privat schalten würde GitHub Pages/Vercel-Freetier-Setup berühren,
  noch nicht final entschieden.
- Footer-Signatur-Format: "v[Version] · Stand [Monat Jahr] · Zusammengetragen und erstellt von André Hennen,
  Curious Company · Datenquellen: ADC Büro, adc.de, Mitglieder und ADC Dashboard" — bei Content-Updates
  "Stand" ggf. aktualisieren.
- **Versionierung & Changelog:** SemVer-artig (`MAJOR.MINOR.PATCH`) — MINOR für neue Inhalte/Funktionen, PATCH für
  Korrekturen. Einzige Quelle ist das Array `changelog` im `<script>` von `index.html` (neuester Eintrag zuerst):
  Bei jedem Release dort einen Eintrag ergänzen — Versionsnummer im Footer und das aufklappbare Changelog
  (wie im Dashboard) werden daraus erzeugt.
- Dashboard-URL: https://adc-germany-dashboard.vercel.app/ (alte `adc-hh-dashboard`-URL leitet nur weiter).

## Offene Punkte

- Nach der JHV (Okt. 2026): Übergabe-Part bei Hamburg entfernen (Dörte Spengler-Ahrens als aktuelle
  Präsidiumsvertreterin, `transition`-Hinweis, "Vorstand ab Oktober 2026") — bis dahin bewusst unverändert.
  Ihre Adresse `doerte.spenglerahrens@adc.de` (weicht vom Muster ab) ist ungeprüft und vorerst zurückgestellt.
- Stimmübertragung/Vollmacht-Formular: noch Platzhalter, Vorlage folgt
- Mitgliedschafts-PDFs: Stand 2020, Namen teils veraltet — Update angekündigt
- Private vs. Public Repo: noch offene Entscheidung (gilt auch für Schwester-Projekt `adc`)
