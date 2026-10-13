# QA-Notiz ADC Kompass (Website Endboss)

Wird nicht ausgeliefert (`.vercelignore`). Spätere Prüfläufe bauen hierauf auf.

## Bestandsaufnahme
- **Art:** statische Single-Page-Orientierungsseite für ADC-Mitglieder, eine `index.html`, kein Build.
- **Hosting/Deploy:** Vercel, Auto-Deploy bei Push auf `main`, kein Staging. Header und Caching in `vercel.json`.
- **Inhalte:** gehören André Hennen; Texte nur nach Freigabe ändern. Sprache de-DE.
- **Vorgaben:** Design-System des ADC Dashboards (siehe `CLAUDE.md`).
- **Fremde Server:** keine. Keine Cookies, kein localStorage, kein Tracking.

## Lauf vom 13.10.2026 (v1.6.5 → v1.6.6)
Gemessen im eingebauten Chromium auf 320, 390, 768, 1024, 1280 und 1440 px, alle 7 Ansichten.

| Messung | vorher | nachher |
|---|---|---|
| axe-core 4.14 (WCAG 2.2 AA + best-practice) | 0 Befunde | 0 Befunde |
| Querscrollen | keines | keines |
| Schriftgewichte | 5 (400/500/600/700/800) | 4 (400/600/700/800) |
| Einzelwort in letzter Überschriftenzeile | 3–4 je Breite | per `text-wrap:balance` behoben |
| Sicherheits-Header | nur HSTS | CSP, nosniff, Referrer-Policy, Permissions-Policy, X-Frame-Options, COOP |
| Kopf-Logo | 40 KB (1181 px für 37 px Anzeige) | 11 KB (148 px) |
| `CLAUDE.md` öffentlich abrufbar | ja | nein |
| Canonical, Vorschaubild 1200×630, JSON-LD | fehlten | vorhanden |
| Fehlerseite | Vercel-Standardtext | eigene `404.html` |
| Server-Antwort (5 Abrufe) | 70–110 ms | – |

## Nicht geprüft
- Safari/WebKit und Firefox (Playwright hier nicht installiert), echte Geräte, Screenreader von Hand, Zoom 400 %.
- PageSpeed/Core Web Vitals (API-Kontingent erschöpft) – von Hand auf pagespeed.web.dev prüfen.
- Haptik und Homescreen-Icon auf echtem iPhone/Android.

## Entscheidungen des Inhabers (13.10.2026)
- **Impressum und Datenschutz:** eigene Seiten `/impressum` und `/datenschutz`, Basis sind die Texte des Dashboards
  (dort bereits abgestimmt), angepasst an den Kompass. Abschnitt 4 der Datenschutzerklärung (Rechtsgrundlage für
  die genannten Personen) ist neu formuliert und sollte fachlich gegengelesen werden.
- **Indexierung:** nicht indexieren. `noindex` als Meta-Tag und `X-Robots-Tag`-Header; `robots.txt` erlaubt den Abruf
  (sonst sehen Suchmaschinen das `noindex` nicht) und sperrt KI-Crawler.
- **Rechtschreibung:** Befunde 1–6 freigegeben und umgesetzt, per Wort-Vergleich kontrolliert (v1.6.7).
- **Schriftgröße:** Fließtext in Karten bleibt bei 14 px wie im Dashboard.
- Offen, nur gemeldet: „Gründungsort des ADC Deutschland (1964)“ bei Köln/Düsseldorf – welche Stadt ist gemeint?

## Bekannte, bewusst belassene Punkte
- CSP erlaubt `'unsafe-inline'` für Skript und Stil, weil alles in einer Datei ohne Build liegt.
- Lange Button-Beschriftungen in schmalen Karten dürfen umbrechen (sonst Querscrollen).
- CI-Logo-Downloads in `ci/` bleiben in Originalgröße.

## Wartung
- Quartalsweise: externe Links, `security.txt`-Ablauf (13.10.2027), Kontakte/Präsidium, PageSpeed, echtes Gerät.
