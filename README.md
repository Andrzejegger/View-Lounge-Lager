# View Lounge — Lager (Warenwirtschafts-App)

Eine installierbare Single-File-Web-App (Vanilla HTML/CSS/JS, kein Build-Step) für die
Warenwirtschaft der **View Lounge Aichach**. Gleiche Technik wie die Checklisten-App;
nutzt dasselbe Firebase-Projekt `view-lounge-app` mit neuen Collections (`wws_*`).

---

## Was die App kann (MVP)

- **Anmelden** mit gemeinsamem Passwort (Start: `1234`).
- **Lager:** alle Artikel mit aktuellem Bestand, Suche + Kategorie-Filter.
- **Buchen:** Zugang (＋) / Abgang (−) mit Menge, Mitarbeiterauswahl und optionaler Notiz.
  Bestand aktualisiert sich sofort (Echtzeit).
- **Historie:** letzte Buchungen (wer, wann, wie viel), pro Artikel filterbar.
- **Verwaltung:** Artikel anlegen / bearbeiten / deaktivieren · Bestand korrigieren · App-Passwort ändern.
- **Als App installierbar** (PWA: „Zum Startbildschirm").

---

## Dateien

| Datei | Zweck |
| ----- | ----- |
| `index.html` | Die komplette App (HTML + CSS + JS + Logo inline) |
| `manifest.webmanifest` | PWA-Manifest (Name, Icons, installierbar) |
| `icons/` | App-Icons (192, 512, maskable) — gold auf anthrazit |
| `README.md` | Diese Anleitung |

Datenmodell: siehe `../../reference/firestore-datenmodell-wws.md`.

---

## Lokal testen

Wegen des Browser-Sicherheitsmodells (Firebase + Manifest) am besten über einen lokalen
Server statt per Doppelklick öffnen:

```bash
cd "outputs/warenwirtschaft"
python3 -m http.server 8080
# dann im Browser: http://localhost:8080
```

Passwort `1234` eingeben. Beim ersten Start legt die App `wws_config/access` automatisch an.

---

## Online stellen (Hosting)

Geplant: Subdomain **`lager.view-lounge.de`** via Netlify (wie die Website).

**Variante A — Netlify (empfohlen):** Den Ordner `outputs/warenwirtschaft/` als Site
deployen (Drag & Drop im Netlify-Dashboard oder eigenes Git-Repo + Auto-Deploy),
dann in Hostinger einen DNS-Eintrag `lager` → Netlify setzen und die Subdomain in Netlify
hinterlegen.

**Variante B — GitHub Pages:** Inhalt in ein Repo legen (wie `View-Lounge-App`) und Pages
aktivieren.

> Beide Varianten sind kostenlos. Die [Du]-Schritte (DNS/Netlify) gehen wir gemeinsam durch.

---

## Passwort ändern

In der App: **Verwaltung → 🔑 App-Passwort ändern**. Das neue Passwort gilt sofort für alle
(liegt in Firestore `wws_config/access`). Kein Code-Eingriff nötig.

---

## ⚠️ Vor dem „Scharfschalten" (Live-Betrieb)

1. **Passwort** von `1234` auf ein eigenes ändern.
2. **Firestore-Regeln absichern** — aktuell offen (`allow read, write: if true`), für die
   Testphase ok (Schutz über App-Passwort). Siehe Hinweise in
   `../../reference/firestore-datenmodell-wws.md`.

---

## Technik-Notizen

- Firebase **Compat-SDK 10.12.0** via CDN (`firebase-app-compat`, `firebase-firestore-compat`).
- Buchung = **Transaktion**: schreibt Bewegung + aktualisiert `stock` atomar.
- Mitarbeiterliste wird **live aus `config/staff` geteilt** (gleiche Quelle wie die Checklisten-App),
  Fallback auf eingebaute Default-Liste.
- Login-Status wird pro Browser-Sitzung gemerkt (`sessionStorage`).
- Kosten: Firebase + Hosting im Gratis-Tarif → ~0 €.
