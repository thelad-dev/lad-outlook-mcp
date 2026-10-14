# Plan: fest eingebaute Empfänger-Domain entfernen

## Annahme
Die Prüflogik in `src/lad_outlook_mcp/policy.py` und die Defaults in
`src/lad_outlook_mcp/config.py` enthalten keine Domain; beide Erlaubtlisten sind
standardmäßig leer und "beide leer = alles erlaubt" ist gewolltes Verhalten.
Die fest eingebaute Kundendomain steht nur in `config.env.example`, `README.md` und zwei Testdateien.
Ich setze voraus, dass eine reservierte Beispieldomain (`example.com`, `example.net`,
RFC 2606) als neutraler Platzhalter akzeptiert ist und dass die ausgelieferte
Beispielkonfiguration keine Erlaubtliste mehr vorbelegt.

## Zwei Wege
1. Beispielwert durch eine neutrale Beispieldomain ersetzen
   (`OUTLOOK_ALLOWED_RECIPIENT_DOMAINS=example.com`). Vorteil: Die Datei zeigt
   weiterhin eine aktive Sperre. Nachteil: Wer sie unverändert kopiert, bekommt eine
   Erlaubtliste, die keine echte Adresse zulässt - das Senden scheitert ohne erkennbaren
   Grund, und der Wert sieht wie eine Produktvorgabe aus.
2. (nicht aus dem Auftrag) Beispielwert leer lassen und die Beispieldomain nur als
   auskommentierte Zeile plus Hinweis daneben zeigen. Vorteil: kein vorbelegter
   Empfängerkreis, dokumentierter Weg zum Einschalten, gleiches Verhalten wie die
   Code-Defaults. Nachteil: Die Vorlage liefert ab Werk keine Sperre, der Hinweis muss
   deutlich sein.

Ich nehme Weg 2 für `config.env.example` und README (Vorlage ohne vorbelegte Sperre,
Beispieldomain nur als Kommentar und in Erklärtexten) und Weg 1 innerhalb der Tests,
weil die Allowlist-Prüfung dort einen konkreten Wert braucht.

## Ablehnung
Ein erfahrener Kollege würde ablehnen: eine andere echte Firmen- oder Kanzlei-Domain als
neuen Platzhalter (auch die bisherige Beispiel-Kanzleidomain); ein stillschweigendes Lockern der Sperre
ohne Hinweis in der Vorlage; eine Änderung an `policy.py`, die "beide Listen leer =
alles erlaubt" umdreht; Restvorkommen der alten Domain irgendwo im Repo.

## Nachweis
- Eine Suche nach der alten Kundendomain über das ganze Repo liefert keinen Treffer.
- `.venv/bin/python -m pytest` (bzw. `python -m pytest`) läuft grün, inklusive der
  Allowlist-Tests in `tests/test_policy.py` und `tests/test_server.py`.
- `ruff check .` bleibt grün.

## Grenze
Nicht angefasst: Anmeldung (`auth.py`, Login-Abschnitte im README), Sende-Bestätigung
(`OUTLOOK_CONFIRM_SEND`), Anhangs- und Pfadregeln, die Prüflogik in `policy.py`, die
Defaults in `config.py` und die lokale Datei `~/.config/lad-outlook-mcp/config.env`.
Geändert werden nur `config.env.example`, die Empfänger-Textstellen in `README.md`
und die Testdaten in `tests/test_policy.py` und `tests/test_server.py`.
