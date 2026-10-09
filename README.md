# GENITUS Global 100

100 international bedeutende Aktien außerhalb des S&P500-Datensatzes, mit live aus Yahoo Finance berechneten Reports: volle 5-Faktor-Bilanzanalyse (echte Jahresabschluss-Einzelposten), 4-Strategien-Backtest (Einmalanlage / Sparplan / Dip-Käufer / Hodler), Risiko- & Markov-Kennzahlen, Kursverlauf.

**Live:** https://babybounce500.github.io/genitus-global-100/

## Stand
- Erstellt: 2026-10-09, Bilanzanalyse-Upgrade: 2026-10-09
- 100/100 Unternehmen erfolgreich verarbeitet (2 Ticker-Korrekturen während des Builds, siehe unten)
- 27 Regionen
- Ø Bilanz-Score: 66,8 / 100 (Spanne 49,9 – 87,3)

## Methodik
- Kursdaten: Yahoo Finance, 5 Jahre Wochenschlusskurse (split-/dividendenbereinigt über `adjclose`), live über einen Proxy abgerufen.
- Backtest: identische Logik wie der produktive GENITUS "Hätte ich gekauft"-Backtester (Einmalanlage / monatlicher Sparplan / Dip-Käufer 20/80 mit bis zu 20 Nachkäufen ab -15% Drawdown / Hodler ohne Basis-Sparplan), Kapital 10.000 (jeweilige Lokalwährung). Peak/Drawdown-Trigger läuft auf dem split-bereinigten Kurs (siehe Rheinmetall-Report-Audit für den Hintergrund dieses Fixes).
- **Bilanzanalyse**: volle 5-Faktor-Methodik 1:1 aus dem produktiven GENITUS Bilanz-Terminal übernommen (Profitabilität 30%, Liquidität 15%, Verschuldung 20%, Effizienz 15%, Bewertung 20%) — auf Basis echter Gewinn- und Verlustrechnung / Bilanz / Kapitalflussrechnung je Unternehmen (`/api/yahoo/financials`), nicht nur aggregierter Quote-Kennzahlen. Ersetzt die ursprüngliche 4-Faktor-"Schnellcheck"-Variante der ersten Version.
- Markov-Check: einfache Zustandsübergangs-Wahrscheinlichkeit (Woche steigt/fällt, gegeben Vorwoche) auf Wochenrenditen.

## Audit-Befunde (während des Builds gefunden und behoben)
1. **Zwei falsche Ticker-Symbole**: `ROG.SW` (Roche) → korrekt `RO.SW`; `AMXL.MX` (América Móvil) → korrekt `AMXB.MX`. Beide gaben HTTP 404, wurden identifiziert und nachträglich korrekt gefetcht.
2. **Ein stiller Fetch-Fehler**: `SAP.DE`s Fundamentaldaten-Request schlug beim ersten Durchlauf transient fehl; der Code fing den Fehler zwar ab, zeigte aber stillschweigend nur Striche ("—") statt den Fehler sichtbar zu machen. Beim erneuten Abruf lieferte derselbe Endpunkt sofort vollständige Daten (Bilanz-Score 73,55/100). Alle 100 Reports wurden auf `NaN`/`undefined`/`null`-Leaks geprüft (0 Treffer).
3. **Split-Artefakt-Check**: alle Positionen mit extremer 5-Jahres-Rendite (SK Hynix +1.786 %, MediaTek +603 %, Petrobras +540 %, MUFG +526 %, TSMC +385 %) wurden explizit auf unbereinigte Aktiensplits in der Kursreihe geprüft (gleiche Heuristik wie im Rheinmetall-Report-Fix) — keine gefunden. Die extremen Renditen sind echte Marktbewegungen (u.a. KI-/Speicherchip-Boom 2023-2025), keine Datenfehler.
4. **Bilanz-Score-Verteilung geprüft**: 0 von 100 Unternehmen mit 3 oder mehr fehlenden der 5 Teilfaktoren (d.h. jedes Unternehmen hat eine belastbare Gesamtbewertung, keine aus Datenmangel "erratene" Zahl).
5. Alle 100 generierten Report-Dateien (inkl. der neuen Bilanzanalyse-Tabellen) wurden syntaktisch validiert (`new Function()` auf jedes eingebettete `<script>`) — 0 Fehler.

## Nicht geprüft / bekannte Grenzen
- Kein manueller Line-by-Line-Review aller 100 Firmenprofile (Kurzbeschreibungen stammen aus eigenem Wissen, nicht pro Firma web-recherchiert) — bei 100 Reports nicht wirtschaftlich; stattdessen automatisierte Konsistenzprüfung + gezielte Stichproben (5 Reports quer durch die Regionen gelesen).
- Keine Nachrichten-Timeline pro Firma (anders als beim Rheinmetall-Einzelreport) — das wäre 100 zusätzliche Recherchen gewesen und war nicht Teil des Zeitbudgets.
- ROIC nutzt pauschal eine Steuerrate von 21% (wie im Bilanz-Terminal) statt der tatsächlichen landesspezifischen Steuerrate je Unternehmen — bei Unternehmen außerhalb der USA eine bekannte Vereinfachung.
- Jahresabschlussdaten sind das letzte von Yahoo Finance verfügbare Geschäftsjahr; bei Unternehmen mit abweichendem Geschäftsjahresende kann das Berichtsdatum leicht vom Kursdatum abweichen.

Keine Anlageberatung.
