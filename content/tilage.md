# REST-Schnittstelle zum TI-Lagebild

**Inhaltsverzeichnis**
- [1. Beschreibung](#1-beschreibung)
  - [1.2. Versionen](#12-versionen)
- [2. Basis URLs](#2-basis-urls)
- [3. TI Lagebild Version 1](#3-ti-lagebild-version-1)
  - [3.1. Abruf der Gesamtliste](#31-abruf-der-gesamtliste)
    - [3.1.1. Beispiel](#311-beispiel)
  - [3.2. Weitere Routen](#32-weitere-routen)
    - [3.2.1. Betriebsumgebung](#321-betriebsumgebung)
    - [3.2.2. Configuration Item](#322-configuration-item)
    - [3.2.3. Teilnehmer ID](#323-teilnehmer-id)
- [4. TI Lagebild Version 2](#4-ti-lagebild-version-2)
  - [4.1. Detailbeschreibung "appStatus"](#41-detailbeschreibung-appstatus)
  - [4.2. Detailbeschreibung "cause"](#42-detailbeschreibung-cause)
  - [4.3. Gesamtbeispiel](#43-gesamtbeispiel)
  - [4.4. parametrierter Abruf](#44-parametrierter-abruf-des-lagebildes-v2)
    - [4.4.1 Abruf Filterliste](#441-abruf-filterliste)
    - [4.4.2 Abruf des parametrierten Lagebildes](#442-abruf-des-parametrierten-lagebildes)

  

## 1. Beschreibung

Der Webservice liefert die in der TI-Lagebild (Online Datenbank) abgelegten Informationen über eine technische Schnittstelle aus. Im weiteren werden die bestehenden Routen sowie das Auslieferungsformat beschrieben.

- Das Lagebild stellt die aktuelle Sichtweise der gematik auf die TI Infrastruktur dar – welche durch das Probing ermittelt wird.
- Die Aktualisierung der Daten erfolgt im 5 Minuten Rhythmus
  - Probing findet jeweils xx:x0 bzw. xx:x5 statt, also 01:00, 01:05, 01:10 Uhr usw. statt
  - Die Aktualisierung der Daten wird entsprechend unmittelbar nach Abschluss eines Probing Intervalls vorgenommen
  - Aufgrund dieses Aktualisierungsrhythmus sollte auch eine **automatisierte zyklische Abfrage nur höchstens alle 5 Minuten** erfolgen.
  - Es wird empfohlen eine automatisierte Abfrage mit einem leichten Versatz zur Aktualisierung der Daten und nicht zeitgleich durchzuführen. Z.B. eine Minute danach xx:x1, also 01:01, 01:06, 01:11 Uhr usw.
- REST-Schnittstelle liefert den gleichen Datenbestand welcher in der grafischen Oberfläche abrufbar ist
- Ziele der Schnittstelle
  - Anreicherung des Monitorings der eigenen Infrastruktur
  - Automatischen Abruf der Daten & Zugreifbarkeit der Informationen ermöglichen

**Hinweis**

Die gematik behält nicht vor Nutzer mit einem unverhältnismäßig hohen Ressourcenbedarf (z.B. sekündliche Abfrage) oder solche, die sicherheitsverschleiernde Methoden (z.B. IP-Cycler) einsetzen ohne Verzug von der Nutzung der Schnittstelle auzuschließen.

### 1.2. Versionen
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

Für den gezielteren Abruf sind weitere Routen vorhanden, welche jeweils eine Filterung der Gesamtliste bewirken. Achtung, die Werte des Abrufs sind jeweils in Großbuchstaben zu übergeben.

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


#### 3.2.3. Teilnehmer ID
Filterung bzw. Abruf auf die konkrete Teilnehmer ID
```
https://[URL]/lageapi/v1/tilage/tid/[:tid]
```
Beispiel
```
GET https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1/tilage/tid/ARVTO
```

## 4. TI Lagebild Version 2

Die Informationen für das Lagebild in Version 2 sind bereits verdichtet und werden zusammengefasst in einem JSON-Objekt ausgeliefert. Innerhalb der JSON-Struktur sind statische und dynamische Elemente vorhanden. Die statischen Attribute "epa", "erezept", "kim", "odg", "vsdm" und "wanda" spiegeln die in der Service Map vorhanden Gruppen wieder. Konsumenten welche sich nur für entsprechende Gruppen interessieren, können die Verarbeitungslogik des JSON daraufhin anpassen. 

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
    "ogd": {...},
    "vsdm": {...},
    "tianschluss": {...}
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
    "ogd": {
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
    },
    "tianschluss": {
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


### 4.4. parametrierter Abruf des Lagebildes V2

Das Lagebild in Version 2 stellt Einschränkungen für alle in der TI verwendeten Dienste dar. Einschränkungen verstehen sich hier als Beeinträchtigung in der vollständigen Nutzung eines Dienstes, es sich z.B. Teilkomponenten wie der "Empfang von E-Mails" gestört. Einige der Dienste werden durch unterschiedliche Anbieter in getrennten Systemumgebungen bereitgestellt, daher haben Einschränkungen nicht immer eine tatsächliche Auswirkungen bei allen Leistungserbringern - eine Einschränkung des KIM Fachdienstes bei Anbieter A hat z.b. keine Auswirkung wenn der Dienst von Anbieter B verwendet wird. 
Um die genutzten Systeme des Leistungserbringers für den parametrierten Abruf nutzen zu können, stehen folgende Endpunkte zur Verfügung: 
* Abruf der Filterliste
* Abruf des parametrierten Lagebildes


#### 4.4.1 Abruf Filterliste

Die Filterliste bietet die aktuell zur Auswahl stehenden logischen Produktinstanzen an, welche als Filter verwendet werden können. Über die Filtergruppe kann eine Zuordnung zur den Filterparameter vorgenommen werden. 

Der Pfad für den Abruf
```
https://ti-lage.prod.ccs.gematik.solutions/lageapi/v2/tilage/filter
```

Ergebnis und Inhalt
```json
[
  {
    "filterGroup": "TSP SMC-B",
    "pdt": "PDT38",
    "prodTypeName": "TSP-X.509nQ-SMC-B",
    "cis": [
      {
        "ci": "CI-0000019",
        "orgShortName": "Anbieter A"
      },
      {
        "ci": "CI-0000137",
        "orgShortName": "Anbieter B"
      },
      {
        "ci": "CI-0000186",
        "orgShortName": "Anbieter C"
      },
      {
        "ci": "CI-0000192",
        "orgShortName": "Anbieter D"
      }
    ]
  },
  {...}
]
```

| Attribut  | Beschreibung |
| ------------- | ------------- |
| filterGroup  | Zuordnung zu den Filterattributen, siehe nachfolgende Erläuterung  |
| pdt  | Zuordnung zu den gematik Produkttypen  |
| prodTypeName  | Name des Produkttyps |
| cis  | Liste von wählbaren logischen Produktinstanzen  |
| cis > ci  | CI-Id der logischen Produktinstanz  |
| cis > orgShortName  | Name des Anbieters |


Anhand der Filtergruppen lässt sich Zuordnung der Abfageparameter festlegen: 

| Filtergruppe  | URL Filterparameter |
| ------------- | ------------- |
| TI-Zugang  | ciAccess  |
| TSP SMC-B  | ciTspSmcb  |
| TSP HBA  | ciTspHba  |
| KIM  | ciKim  |


#### 4.4.2 Abruf des parametrierten Lagebildes

Der Abruf des parametriesierten Lagebildes erfolgt über die gleiche Basis URL und den gleichen Endpunkt. Die gewünschten URL Filterparameter aus der darüber stehenden Tabellen werden als Query Parameter an die URL angehangen und sind damit auch kombinierbar. 

**Beispiel 1:**

Es werdem alle pot. Einschränkungen ausgegeben welche nicht KIM betreffen, für KIM nur Einschränkungen sofern diesen den Abieter mit CI-ID CI-0000200 betreffen. 
```
https://ti-lage.prod.ccs.gematik.solutions/lageapi/v2/tilage?ciKim=CI-0000200
```

**Beispiel 2:**

Es werdem alle pot. Einschränkungen ausgegeben welche nicht KIM und TI-Anschluss betreffen, für KIM nur Einschränkungen sofern diesen den Abieter mit CI-ID CI-0000200 und für TI-Anschluss den Abieter mit CI-Id CI-0000128 betreffen. 
```
https://ti-lage.prod.ccs.gematik.solutions/lageapi/v2/tilage?ciKim=CI-0000200&ciAccess=CI-0000128
```

**Beispiel 3:** 

Es werdem alle pot. Einschränkungen ausgegeben welche nicht KIM, TI-Anschluss und TSP SMC-B betreffen, für KIM nur Einschränkungen sofern diesen den Abieter mit CI-ID CI-0000200, für TI-Anschluss den Abieter mit CI-Id CI-0000128 und für TSP SMC-B den Anbieter mit CI-Id CI-0000019 betreffen. 
```
https://ti-lage.prod.ccs.gematik.solutions/lageapi/v2/tilage?ciKim=CI-0000200&ciAccess=CI-0000128&ciTspSmcb=CI-0000019
```

Je Filterparameter kann eine CI angegeben werden. Die Filterparameter untereinander sind frei kombinierbar. 


Beispiel wie die Filterliste genutzt werden kann um eine Abruf-URL zusammenzusetzen, sowie der Abruf des aktuellen Lagebildes in parametrierter und nicht parametrteriter Form sind im Verzeichnis "[example](/example/tilage-v2/)" zu finden. 

| Beispiel  | Direktlink | Beschreibung |
| ------------- | ------------- | ------------- |
| URL-Builder  | [Link](/example/tilage-v2/urlbuilder.html)  | Die HTML Datei kann als lokal gespeicherte Datei im Browser geöffnet werden. Über die TI-Lage V2 API wird die Liste der vorhanden Filtergruppen und zugehörigen logischen Produktinstanzen abgerufen. Je Filtergruppe kann nun ein Anbieter - und damit dessen logische Produktinstanz - gewählt werden. Die die parametrierung der URL wird automatisch angepasst. |
| TI-Lage V2 Abruf  | [Link](/example/tilage-v2/lagebild_v2.html)  | Die HTML Datei kann als lokal gespeicherte Datei im Browser geöffnet werden. Initial wird das aktuelle Lagebild ohne einschränkungen über Parameter dargestellt. Die URL Query Parameter welche mit Hilfe des URL Builder generiert wurden können als weitere Parameter angefügt werden um das parametrierte Lagebild V2 zu laden. Wenn die HTML Datei lokal z.b. im Verzeichnis `C:\temp\lagebild_v2.html` gespeichert haben, wird dies in der URL Leiste des Browsers ebenso dargestellt. Die Parameter können nun in der URL-Leiste wie hier zu sehen angehangen werden: `C:\temp\lagebild_v2.html?ciKim=CI-0000200&ciAccess=CI-0000128`. |
