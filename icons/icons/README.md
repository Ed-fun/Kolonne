# Club-Icons für Kolonne Live

Die App liest diesen Ordner automatisch über die GitHub API aus.

## Namensschema

```
01_Harley-Pfalz-Club.svg
02_Einzelteilnehmer.svg
05_Mein-Chapter.svg
12_Baden-Chapter.svg
```

- Die **zweistellige Nummer** am Anfang bestimmt die Sortierung.
- Der Teil nach dem Unterstrich wird zum Anzeigenamen (Bindestriche werden zu Leerzeichen).
- Dateien, die `_nix` im Namen enthalten (z. B. `03_nix.svg`), erscheinen **nicht** im Dropdown.

## Neuen Club hinzufügen

1. SVG-Datei nach dem Schema `XX_Club-Name.svg` hier hochladen.
2. Fertig – die App erkennt den neuen Club beim nächsten Laden automatisch.

## Hinweise

- Empfohlene Größe: 64×64 oder 128×128 px, möglichst quadratisch.
- Format: reines SVG (keine externen Bilder).
- Die ersten beiden Clubs (01 + 02) sind zusätzlich als Inline-SVG im Code hinterlegt und funktionieren immer.
