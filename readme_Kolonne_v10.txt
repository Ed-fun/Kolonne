================================================================================
  KOLONNE LIVE – README
================================================================================
  Motorrad-Kolonnen-App mit Live-Status und Standort
  Öffentliche Demo: https://ed-fun.github.io/Kolonne/
  Repo:            https://github.com/Ed-fun/Kolonne
================================================================================


--------------------------------------------------------------------------------
1. WARTUNG – WICHTIGE PUNKTE (kurz & übersichtlich)
--------------------------------------------------------------------------------

1.1 Supabase (Live-Verbindung)
    - Die App braucht ein aktives Supabase-Projekt (Realtime).
    - Free-Tier: Projekt kann nach ca. 7 Tagen OHNE Aktivität pausiert werden.
    - Dann funktioniert keine Kolonne mehr, bis das Projekt manuell
      wieder gestartet wird (Supabase-Dashboard → Project → Restore).
    - Empfehlung: 1–2× pro Woche die App öffnen ODER automatischen Ping
      einrichten (z. B. cron-job.org auf die Supabase-URL).
    - Anon-Key und Projekt-URL stehen in index.html (Schwellwerte/Konfig).

1.2 GitHub / GitHub Pages
    - Repo muss ÖFFENTLICH bleiben, damit die Seite unter
      https://ed-fun.github.io/Kolonne/ erreichbar ist.
    - Deployment erfolgt automatisch bei Push auf den Branch "main".
    - index.html muss im Hauptverzeichnis von main liegen.

1.3 Club-Icons (Ordner icons/)
    - Struktur auf main:
          index.html
          icons/
            01_Harley-Pfalz-Club.svg
            02_Einzelteilnehmer.svg
            03_Mein-Club.svg
            …
    - Dateiname: XX_Club-Name.svg  (XX = Nummer zur Sortierung)
    - Dateien mit "_nix" im Namen erscheinen NICHT im Dropdown.
    - Neuer Club = nur neue SVG in icons/ hochladen, App neu laden.
    - Ordner muss "icons" heißen (kleingeschrieben), kein Branch!

1.4 Wichtige Schwellwerte in index.html (anpassbar)
    OFFLINE_MS        = 90000   // ms ohne Signal → Fahrer = offline (90 s)
    SEND_TIMEOUT      = 20000   // Positions-Update mindestens alle 20 s
    LEADER_TIMEOUT_MS = 120000  // ms ohne Anführer-Signal → Warnung
    GAP_M / LOST_M    = Abstandsschwellen für "Lücke" / "abgehängt"

1.5 Rechtliches
    - Impressum, Datenschutz, Haftungsausschluss: in der App unter "?"
      (Hilfe) als aufklappbare Abschnitte.
    - Bei Änderungen an Name/Adresse/E-Mail dort anpassen.

1.6 Backup / Updates
    - Vor größeren Änderungen index.html lokal sichern.
    - Supabase: Zugangsdaten und Projekt-Ref notieren (Dashboard).


--------------------------------------------------------------------------------
2. WAS IST KOLONNE LIVE? (für Anwender)
--------------------------------------------------------------------------------

Kolonne Live ist eine Web-App für Motorradgruppen (besonders Harley-Fahrer).
Sie hilft dem Anführer und den Mitfahrern, während einer gemeinsamen Ausfahrt
den Überblick zu behalten – ohne teure Extra-Hardware.

Typische Situation:
  Der Anführer fährt vorne. Dahinter 5, 10 oder mehr Bikes. In Kurven,
  an Ampeln oder bei abbiegen verliert man leicht jemanden aus dem Blick.
  Die App zeigt live, ob alle noch "dran" sind, ob eine Lücke entsteht
  oder jemand falsch abgebogen ist.

Die App läuft im Smartphone-Browser (Chrome, Safari, Firefox, …).
Installation als App-Store-App ist nicht nötig. Einfach Link öffnen,
idealerweise Bildschirm an lassen.


--------------------------------------------------------------------------------
3. WIE FUNKTIONIERT DIE APP FÜR ANWENDER?
--------------------------------------------------------------------------------

3.1 Rollen

  ANFÜHRER
  - Erstellt eine Kolonne (optional mit Namen).
  - Wählt seinen Verein/Club.
  - Zeigt einen QR-Code oder teilt den Link / Raum-Code.
  - Sieht alle Fahrer mit Statusfarben (Avatar-Reihe oder Liste).
  - Kann den gefahrenen Track speichern (GPX) und auf einer Karte ansehen.
  - Beendet die Kolonne für alle.

  FAHRER
  - Scannt den QR-Code oder gibt Raum-Code + Namen + Club ein.
  - Sieht eine Ampel mit dem eigenen Status in der Kolonne.
  - Kann die Kolonne verlassen; der Anführer wird informiert.

3.2 Statusfarben (Ampel / Avatare)

  Grün    = alles ok, Fahrer ist dran
  Gelb    = Lücke (Abstand zum Vordermann / zur Gruppe zu groß)
  Rot     = steht, abgehängt oder offline (kein Signal mehr)
  Violett = vermutlich falsch abgebogen (seitliche Abweichung / Kurs)

3.3 Typischer Ablauf

  1. Anführer: "Ich bin Anführer" → optional Name + Club → Kolonne starten
  2. QR-Code anzeigen oder Link/Code an die Gruppe schicken
  3. Fahrer: beitreten mit Namen und Club
  4. Während der Fahrt: Anführer beobachtet die Übersicht,
     Fahrer achten auf ihre Ampel
  5. Nach der Tour: Anführer Kolonne beenden, optional Track als GPX sichern

3.4 Tipps für die Praxis

  - Handy-Display an lassen (Wake-Lock wird angefordert, iOS ist eingeschränkt).
  - Gute GPS-Empfangslage (nicht in der Jackentasche unter dickem Material).
  - HTTPS ist Pflicht – über GitHub Pages ist das automatisch gegeben.
  - Bei "offline": wieder in Netzreichweite, App offen lassen,
    ggf. "Ich bin wieder da" tippen.


--------------------------------------------------------------------------------
4. TECHNISCHE BESCHREIBUNG (für Entwickler)
--------------------------------------------------------------------------------

4.1 Architektur

  - Reine Frontend-Web-App (eine index.html, kein Build-Schritt).
  - Hosting: GitHub Pages.
  - Echtzeit: Supabase Realtime (Broadcast/Channel pro Raum-Code).
  - Standort: Browser Geolocation API + optional Wake Lock.
  - Club-Icons: Ordner icons/, Liste per GitHub Contents API geladen
    (Fallback: localStorage-Cache + zwei fest eingebaute Clubs).
  - QR-Code: reine JavaScript-Implementierung (kein CDN).

4.2 Datenfluss

  - Anführer und Fahrer joinen denselben Channel (Raum-Code).
  - Jeder sendet periodisch Position (und Metadaten: Name, Club-Id, …).
  - Anführer berechnet aus den Positionen relative Status
    (ok / gap / stop / off / wrong) und verteilt den Überblick.
  - Fahrer erhalten ihren Status und zeigen die Ampel.

4.3 Wichtige Dateien / Stellen

  index.html
    - Gesamte App (HTML, CSS, JS)
    - Schwellwerte ganz oben im Script-Block (OFFLINE_MS, GAP_M, …)
    - Supabase-URL und Anon-Key in der Konfiguration
    - Hilfe-Overlay: Bedienung + Impressum/Datenschutz/Haftung

  icons/
    - SVG-Dateien XX_Name.svg
    - _nix = Platzhalter, nicht auswählbar

4.4 Raum-Code & URL

  - Beim Start erzeugt der Anführer einen kurzen Code.
  - Link-Form: .../Kolonne/?room=XXXXXX
  - Fahrer können über QR oder manuellen Code beitreten.

4.5 Offline- / Timeout-Logik

  - Bleibt ein Fahrer länger als OFFLINE_MS ohne Update,
    wird er als "offline" markiert (rote Anzeige).
  - SEND_TIMEOUT steuert das Mindest-Sendeintervall.
  - LEADER_TIMEOUT_MS: Warnung, wenn der Anführer selbst stumm bleibt.

4.6 Erweiterungen (Ideen)

  - Weitere Clubs: nur SVG in icons/ legen
  - Schwellwerte an Fahrstil/Region anpassen
  - Supabase: RLS und Policies prüfen, Projekt-Ping automatisieren
  - Optional: PWA-Manifest für "Zum Home-Bildschirm"


--------------------------------------------------------------------------------
5. SCHNELLREFERENZ – CHECKLISTE WARTUNG
--------------------------------------------------------------------------------

  [ ] Supabase-Projekt aktiv (nicht pausiert)?
  [ ] Repo öffentlich? Branch main aktuell?
  [ ] index.html im Root von main?
  [ ] Ordner icons/ auf main mit nummerierten SVGs?
  [ ] Nach Icon-Upload App hard-reload getestet?
  [ ] Impressum/Kontakt in der Hilfe noch korrekt?
  [ ] Bei Bedarf OFFLINE_MS / SEND_TIMEOUT angepasst?


--------------------------------------------------------------------------------
6. KONTAKT / IMPRESSUM (Kurz)
--------------------------------------------------------------------------------

  Anbieter:  Ed-fun, Rockenhausen
  E-Mail:    Ed-fun@web.de

  (Vollständige Texte: in der App unter "?" → Impressum / Datenschutz /
   Haftungsausschluss)

================================================================================
  Ende der README – Kolonne Live
================================================================================
