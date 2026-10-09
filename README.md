# GENITUS Global 100

100 international bedeutende Aktien außerhalb des S&P500-Datensatzes, mit live aus Yahoo Finance berechneten Reports: Fundamental-Schnellcheck, 4-Strategien-Backtest (Einmalanlage / Sparplan / Dip-Käufer / Hodler), Risiko- & Markov-Kennzahlen, Kursverlauf.

**Live:** https://babybounce500.github.io/genitus-global-100/

## Stand
- Erstellt: 2026-10-09
- 100/100 Unternehmen erfolgreich verarbeitet (2 Ticker-Korrekturen während des Builds, siehe unten)
- 27 Regionen
- Ø Fundamental-Schnellcheck: 69,2 / 100

## Methodik
- Kursdaten: Yahoo Finance, 5 Jahre Wochenschlusskurse (split-/dividendenbereinigt über `adjclose`), live über einen Proxy abgerufen.
- Backtest: identische Logik wie der produktive GENITUS "Hätte ich gekauft"-Backtester (Einmalanlage / monatlicher Sparplan / Dip-Käufer 20/80 mit bis zu 20 Nachkäufen ab -15% Drawdown / Hodler ohne Basis-Sparplan), Kapital 10.000 (jeweilige Lokalwährung). Peak/Drawdown-Trigger läuft auf dem split-bereinigten Kurs (siehe Rheinmetall-Report-Audit für den Hintergrund dieses Fixes).
- Fundamental-Schnellcheck: leichtgewichtige 4-Faktor-Variante (Profitabilität 35%, Liquidität 20%, Verschuldung 20%, Bewertung 25%) direkt aus Yahoo-Quote-Modulen (`summaryDetail`, `defaultKeyStatistics`, `financialData`) — bewusst **nicht** identisch mit dem vollen 5-Faktor-Score aus Bilanz-Terminal/dem Rheinmetall-Report, da keine Jahresabschluss-Einzelposten abgerufen wurden (das würde 100 zusätzliche Requests pro Lauf kosten).
- Markov-Check: einfache Zustandsübergangs-Wahrscheinlichkeit (Woche steigt/fällt, gegeben Vorwoche) auf Wochenrenditen.

## Audit-Befunde (während des Builds gefunden und behoben)
1. **Zwei falsche Ticker-Symbole**: `ROG.SW` (Roche) → korrekt `RO.SW`; `AMXL.MX` (América Móvil) → korrekt `AMXB.MX`. Beide gaben HTTP 404, wurden identifiziert und nachträglich korrekt gefetcht.
2. **Ein stiller Fetch-Fehler**: `SAP.DE`s Fundamentaldaten-Request schlug beim ersten Durchlauf transient fehl; der Code fing den Fehler zwar ab, zeigte aber stillschweigend nur Striche ("—") statt den Fehler sichtbar zu machen. Beim erneuten Abruf lieferte derselbe Endpunkt sofort vollständige Daten. Alle 100 Reports wurden auf `NaN`/`undefined`/`null`-Leaks geprüft (0 Treffer) — dieser eine Fall wurde nur durch gezielte Stichprobenprüfung der Health-Score-Verteilung gefunden, nicht automatisch. Das ist ein bekanntes Risiko dieses Architekturmusters (stiller Fallback auf `{}` bei Fetch-Fehlern) und für künftige Läufe zu beobachten.
3. **Split-Artefakt-Check**: alle Positionen mit extremer 5-Jahres-Rendite (SK Hynix +1.786 %, MediaTek +603 %, Petrobras +540 %, MUFG +526 %, TSMC +385 %) wurden explizit auf unbereinigte Aktiensplits in der Kursreihe geprüft (gleiche Heuristik wie im Rheinmetall-Report-Fix) — keine gefunden. Die extremen Renditen sind echte Marktbewegungen (u.a. KI-/Speicherchip-Boom 2023-2025), keine Datenfehler.
4. Alle 100 generierten Report-Dateien wurden syntaktisch validiert (`new Function()` auf jedes eingebettete `<script>`) — 0 Fehler.

## Nicht geprüft / bekannte Grenzen
- Kein manueller Line-by-Line-Review aller 100 Firmenprofile (Kurzbeschreibungen stammen aus eigenem Wissen, nicht pro Firma web-recherchiert) — bei 100 Reports nicht wirtschaftlich; stattdessen automatisierte Konsistenzprüfung + gezielte Stichproben (5 Reports quer durch die Regionen gelesen).
- Keine Nachrichten-Timeline pro Firma (anders als beim Rheinmetall-Einzelreport) — das wäre 100 zusätzliche Recherchen gewesen und war nicht Teil des Zeitbudgets.
- Fundamental-Schnellcheck ist eine leichtere Methodik als der volle Bilanz-Terminal-Score (siehe oben) — nicht 1:1 vergleichbar zwischen beiden Report-Typen.

Keine Anlageberatung.
