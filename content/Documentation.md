# REST-Schnittstelle zum TI-Lagebild

**Inhaltsverzeichnis**
- [1. Beschreibung](#1-beschreibung)
  - [1.1. Versionen](#11-versionen)
- [2. Basis URLs](#2-basis-urls)
- [3. TI Lagebild Version 1](#3-ti-lagebild-version-1)
  - [3.1. Abruf der Gesamtliste](#31-abruf-der-gesamtliste)
    - [3.1.1. Beispiel](#311-beispiel)
  - [3.2. Weitere Routen](#32-weitere-routen)
    - [3.2.1. Betriebsumgebung](#321-betriebsumgebung)
    - [3.2.2. Configuration Item](#322-configuration-item)
    - [3.2.3. Telematik ID](#323-telematik-id)
- [4. TI Lagebild Version 2](#4-ti-lagebild-version-2)
  - [4.1. Detailbeschreibung "appStatus"](#41-detailbeschreibung-appstatus)
  - [4.2. Detailbeschreibung "cause"](#42-detailbeschreibung-cause)
  - [4.3. Gesamtbeispiel](#43-gesamtbeispiel)
- [5. TI Kennzahlen](#5-ti-kennzahlen)
  - [5.1. Aktueller Wert einer spezifischen Kennzahl](#51-aktueller-wert-einer-spezifischen-kennzahl)
  - [5.2. chronologische Werte einer spezifischen Kennzahl](#52-chronologische-werte-einer-spezifischen-kennzahl)
  - [5.3. Historischer Werte einer spezifischen Kennzahl](#53-historischer-werte-einer-spezifischen-kennzahl)


## 1. Beschreibung

Der Webservice liefert die in der TI-Lagebild (Online Datenbank) abgelegten Informationen über eine technische Schnittstelle aus. Im weiteren werden die bestehenden Routen sowie das Auslieferungsformat beschrieben.

- Das Lagebild stellt die aktuelle Sichtweise der gematik auf die TI Infrastruktur dar – welche durch das Probing ermittelt wird.
- Aktualisierung der Daten im 5 Minuten Rhythmus
  - Probing findet jeweils xx:x0 bzw. xx:x5 statt, also 01:00, 01:05, 01:10 Uhr usw. statt
  - Aktualisierung der Daten wird jeweils unmittelbar nach Abschluss eines Probing Intervalls vorgenommen
- REST-Schnittstelle liefert den gleichen Datenbestand welcher in der grafischen Oberfläche abrufbar ist
- Ziele der Schnittstelle
  - Anreicherung des Monitorings der eigenen Infrastruktur
  - autom. Abruf der Daten & Zugreifbarkeit der Informationen ermöglichen

### 1.1. Versionen
Es gibt mittlerweile 2 Versionen mit unterschiedlichen Sichtweisen auf das TI-Lagebild. 

Version 1:
- Gibt Einzelinformationen auf Ebene der CI
- hat Filtermöglichkeiten für die Betriebsumgebung, die CI oder die TID

Version 2: 
- Liefert anhand der Service Map vorberechnete Werte zu Applikationsgruppen
- Nennt Auswirkungen und Verursacher von Störungen


## 2. Basis URLs

- **Version 1** : ```https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1```

- **Version 2** : ```https://ti-lage.prod.ccs.gematik.solutions/lageapi/v2 ```

## 3. TI Lagebild Version 1
### 3.1. Abruf der Gesamtliste

Die Inhalte werden maßgeblich durch eine Route und einige Sub-Routen welche Filterungen anbieten ausgeliefert. Eine Authentifizierung an der Schnittstelle ist nicht erforderlich. Alle dargestellten Abrufe sind via GET Methode durchzuführen.

Die Gesamtliste zu TI-Lage ist unter folgendem Pfad erreichbar:
```
https://[URL]/lageapi/v1/tilage/
```

Der Abruf erfolgt in Form einer REST-API, im Ergebnis wird somit JSON geliefert. Das Ergebnis beinhaltet bei einem erfolgreichen Abruf immer ein Array von Objekten.
```json
[{object}, {object},...]
```
Es beeinhaltet:
- alle Betriebsumgebungen
- alle CIs


Die Bedeutung der einzelnen Objekte kann der nachfolgenden Beschreibung entnommen werden.
```json
{
    "time":"Zeitangange im Format YYYY-MM-DDTHH:mm:ss.fffZ, die Zeitangabe wird immer als UTC Zeitzone geliefert",
    "ci":"CI-0000XXX",
    "tid":"[Text]",
    "bu":"PU|RU|TU",
    "organization":"[Text]",
    "pdt":"[Text]",
    "product":"[Text]",
    "availability":"[Zahl]", // Zahl 0 | 1 welche die aktuelle Verfügbarkeit darstellt
    "comment":"[Text]",
    "name":"[Text]"
},
{...}
```
#### 3.1.1. Beispiel
Konkreter Abruf
```
GET https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1/tilage
```
Ergebnis und Inhalt
```json
[{
  "time":"2022-09-30T07:42:00.167Z",
  "ci":"CI-0000001",
  "tid":"BITTE",
  "bu":"PU",
  "organization":"BITMARCK Technik GmbH",
  "pdt":"PDT20",
  "product":"Fachdienste VSDM (UFS)",
  "availability":1,
  "comment":null,
  "name":"Fachdienst VSDM (UFS)"
},
{...}]
```

### 3.2. Weitere Routen

Für den gezielteren Abruf sind weitere Routen vorhanden, welche jeweils eine Filterung der Gesamtliste bewirken. Achtung die Werte des Abrufs sind jeweils in Großbuchstaben zu übergeben.

#### 3.2.1. Betriebsumgebung
Filterung bzw. Abruf nach Betriebsumgebung, hier kann "PU", "RU" oder "TU" angegeben werden.
```
https://[URL]/lageapi/v1/tilage/bu/[:bu]
```
Beispiel
```
GET https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1/tilage/bu/PU
```


#### 3.2.2. Configuration Item
Filterung bzw. Abruf nach konkretem CI
```
https://[URL]/lageapi/v1/tilage/ci/[:ci]
```
Beispiel
```
GET https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1/tilage/ci/CI-0000001
```


#### 3.2.3. Telematik ID
Filterung bzw. Abruf auf konkrete TID
```
https://[URL]/lageapi/v1/tilage/tid/[:tid]
```
Beispiel
```
GET https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1/tilage/tid/ARVTO
```

## 4. TI Lagebild Version 2

Die informationen für das Lagebild in Version 2 sind bereits verdichtet und werden zusammengefasst in einem JSON-Objekt ausgeliefert. Innerhalb der JSON-Struktur sind statische und dynamische Elemente vorhanden. Die statischen Attribute "epa", "erezept", "kim", "odg", "vsdm" und "wanda" spiegeln die in der Service Map vorhanden Gruppen wieder. Konsumenten welche sich nur für entsprechende Gruppen interessieren, können die Verarbeitungslogik des JSON daraufhin anpassen. 

Der Pfad für den Abruf
```
https://[URL]/lageapi/v2/tilage/
```

Der Abruf erfolgt in Form einer REST-API, im Ergebnis wird somit JSON geliefert. Das Ergebnis beinhaltet bei einem erfolgreichen Abruf immer ein JSON-Objekt mit zwei Attributen. Das Attribut "appStatus" beinhalte eine Bewertung der Funktionsfähigkeit der jeweiligen Gruppen, sowie deren Funktionseinschränkungen. Unter "cause" werden die verusachenden Systeme aufgelistet. 
```json
{
  "appStatus": {...},
  "cause": []
}
```

### 4.1. Detailbeschreibung "appStatus"

Konkreter Abruf
```
GET https://ti-lage.prod.ccs.gematik.solutions/lageapi/v2/tilage
```

Ergebnis und Inhalt
```json
{
  "appStatus": {
    "erezept": {
      "outage": "none"|"partial"|"full", // gibt den Grad des Funtionseinschränkung an
      "hasMaintenance": false, // true oder false, ist die Einschränkung durch ein Wartungsfenster begründet
      "hasSubComponentMaintenance": false, // true oder false, ist die Einschränkung durch eine Wartung eine benötigten Komponente / Dienst begründet
      "affectedFunctions": [
        {
          "function": "[Text]", // Bezeichnung der eingeschränkten Funktion
          "critical": 1, // 1 oder 0
          "impactDesc": "[Text]", // Beschreibung der Einschränkung
          "outage": "none"|"partial"|"full", // gibt den Grad des Funtionseinschränkung an
          "hasMaintenance": false // true oder false, ist die Einschränkung durch ein Wartungsfenster begründet
        },
        ...
      ]
    },
    "epa": {...},
    "kim": {...},
    "wanda": {...},
    "odg": {...},
    "vsdm": {...}
  },
  "cause": []
}
```

### 4.2. Detailbeschreibung "cause"

Konkreter Abruf
```
GET https://ti-lage.prod.ccs.gematik.solutions/lageapi/v2/tilage
```

Ergebnis und Inhalt
```json
{
  "appStatus": {...},
  "cause": [
    {
      "ci": "CI-000XXXX", // CI der Produktinstanz
      "component": "[Text]", // * oder Name der Komponente im Probing
      "service": "[Text]", // betroffenes Produkt / Dienst
      "organization": "[Text]", // Name des Anbieters
      "function": "[Text]" // Funktionsbeschreibung der Komponente der Produktinstanz
    },
    ...
  ]
}
```

### 4.3. Gesamtbeispiel

```json
{
  "appStatus": {
    "erezept": {
      "outage": "partial",
      "hasMaintenance": false,
      "hasSubComponentMaintenance": false,
      "affectedFunctions": [
        {
          "function": "E-Rezept einlösen per eGK",
          "critical": 1,
          "impactDesc": "Das Einlösen von E-Rezepten mit der eGK ist teilweise gestört",
          "outage": "partial",
          "hasMaintenance": false
        }
      ]
    },
    "epa": {
      "outage": "none",
      "hasMaintenance": false,
      "hasSubComponentMaintenance": false,
      "affectedFunctions": []
    },
    "kim": {
      "outage": "none",
      "hasMaintenance": false,
      "hasSubComponentMaintenance": false,
      "affectedFunctions": []
    },
    "wanda": {
      "outage": "none",
      "hasMaintenance": false,
      "hasSubComponentMaintenance": false,
      "affectedFunctions": []
    },
    "odg": {
      "outage": "none",
      "hasMaintenance": false,
      "hasSubComponentMaintenance": false,
      "affectedFunctions": []
    },
    "vsdm": {
      "outage": "none",
      "hasMaintenance": false,
      "hasSubComponentMaintenance": false,
      "affectedFunctions": []
    }
  },
  "cause": [
    {
      "ci": "CI-1111111",
      "component": "*",
      "service": "TSP-X.509nQ-SMC-B",
      "organization": "Anbieter XY",
      "function": "Legitimation von SMC-B-Zertifikaten des Anbieter XY"
    }
  ]
}
```


## 5. TI Kennzahlen
Kennzahlen werden immer in Form eines Wertes zu einem konkreten Erhebungszeitpukt bereitgestellt. Jede Kennzahl ist durch eine eindeute Kennzahl-Id identifiziert, dies muss für den Abruf entsprechend angegeben werden.

### 5.1. Aktueller Wert einer spezifischen Kennzahl
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

### 5.2. chronologische Werte einer spezifischen Kennzahl
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

### 5.3. Historischer Werte einer spezifischen Kennzahl
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
