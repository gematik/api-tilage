# Kennzahlen der TI

**Inhaltsverzeichnis**
- [1. An der Schnittstelle des Lagebilds](#1-an-der-schnittstelle-des-lagebilds)
  - [1.1. Aktueller Wert einer spezifischen Kennzahl](#11-aktueller-wert-einer-spezifischen-kennzahl)
  - [1.2. chronologische Werte einer spezifischen Kennzahl](#12-chronologische-werte-einer-spezifischen-kennzahl)
  - [1.3. Historischer Werte einer spezifischen Kennzahl](#13-historischer-werte-einer-spezifischen-kennzahl)

## 1. An der Schnittstelle des Lagebilds

Kennzahlen werden immer in Form eines Wertes zu einem konkreten Erhebungszeitpukt bereitgestellt. Jede Kennzahl ist durch eine eindeute Kennzahl-Id identifiziert, dies muss für den Abruf entsprechend angegeben werden.

### 1.1. Aktueller Wert einer spezifischen Kennzahl
Abruf via
```
https://[URL]/lageapi/v1/kpi/[:kpiId]/date/latest
```

Liefert den aktuellsten Wert der Kennzahl und damit genau ein JSON Objekt.
```json
{
  "kpiId":1000,
  "value":"121152",
  "utcTime":"2022-08-03T22:10:04.123Z"
}
```

### 1.2. chronologische Werte einer spezifischen Kennzahl
```
https://[URL]/lageapi/v1/kpi/[:kpiId]
```
Liefert alle Wert einer Kennzahl als Liste aus.
```json
[
  {
    "kpiId":1000,
    "value":"121152",
    "utcTime":"2022-08-03T22:10:04.123Z"
  }, 
  {
    "kpiId":1000,
    "value":"118286",
    "utcTime":"2022-08-02T22:10:02.693Z"
  },
  ...
]
```

### 1.3. Historischer Werte einer spezifischen Kennzahl
```
https://[URL]/lageapi/v1/kpi/[:kpiId]/date/[:date]
```
Kann verwendet werden um die Kennzahlwerte eines konkreten Tages abzurufen. Der Wert [:date] muss dabei im Format "yyyy-mm-dd" geliefert werden.

Liefert alle Wert einer Kennzahl als Liste aus.
```json
[
  {
    "kpiId":1000,
    "value":"121152",
    "utcTime":"2022-08-03T22:10:04.123Z"
  },
  ...
]
```
