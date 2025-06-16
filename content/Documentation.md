# REST-Schnittstelle zum TI-Lagebild & TI-Status

## Kurzbeschreibung

Die gematik bietet unterschiedliche Schnittstellen an welche einen Blick auf den aktuellen Status der Telematik-Infrastruktur ermöglichen. 

Die Schnittstellen `TI-Lage V1`gibt für diesen Zweck Einzelinformationen zur Verfügbarkeit von logischen Produktinstanzen (CIs) aus. Der Abruf ist nach Betriebsumgebung, CI oder über die TID filterbar. 

Die Informationen der Schnittstelle `TI-Lage V2` ermöglicht einen erweiterten Blick auf die Zusammenhänge der Systeme der TI. Es werden mit Hilfe der gematik Service Map und anhand von vorliegenden Störungen Auswirkungen für die relevanten Anwendungen berechnet und in textueller Form zur Verfügung gestellt. 

**Die weiterführende Beschreibung zum TI-Lagebild ist [hier](./tilage.md) zu finden.**

Die Informationen für die Schnittstellen TI-Lagve V1 & V2 beinhalten jeweils den Status des letzten abgeschlossenen 5 Minuten Intervalls. Um eine rückwirkende Betrachtung zu ermöglichen, den Verlauf von pot. Einschränkungen transparent zu machen und dies in verständlicher textueller Form abrufen zu können, kann die `TI-Status` Schnittstelle verwendetw erden. 

**Die weiterführende Beschreibung zur TI-Status Schnittstelle ist [hier](./tistatus.md) zu finden.**