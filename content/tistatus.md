# REST-Schnittstelle TI-Status

**Inhaltsverzeichnis**
- [1. Beschreibung](#1-beschreibung)
- [2. TI Status Schnittstelle](#2-ti-status-schnittstelle)
  - [2.1 Abruf pot. Einschränkungen](#21-abruf-pot-einschränkungen)
  - [2.2 Abruf pot. Ursachen](#22-abruf-pot-ursachen)



## 1. Beschreibung

Der Webservice liefert die in der TI-Lagebild (Online Datenbank) abgelegten Informationen über eine technische Schnittstelle aus. Im Weiteren werden die bestehenden Routen sowie das Auslieferungsformat beschrieben.

- der TI-Status beinhaltet zwei maßgebliche Daten Objekte
  - pot. Einschränkungen
  - pot. Ursachen
- "pot. Einschränkungen" bilden eine Funktionseinschränkung von relevanten TI-Anwendungen ab. Eine pot. Einschränkung selbst besteht aus einem Status, zugeordneten TI-Anwendungen und Statusschritten, welche wiederum die Veränderung der Einschränkung zeitlich darstellen. Ein Statusschritt wiederum beinhaltet daher einen Zeitpunkt, einen Status welcher zu diesem Zeitpunkt vorlag und eine textuelle Beschreibung. 
- "pot. Ursachen" beinhalten beeinträchtigte Komponenten welche im Probing der gematik erkannt wurden. 


## 2. TI Status Schnittstelle
### 2.1. Abruf pot. Einschränkungen

Die Inhalte werden durch eine Route ausgeliefert. Eine Authentifizierung an der Schnittstelle ist nicht erforderlich. Alle dargestellten Abrufe sind via GET Methode durchzuführen.

Abrufbar unter folgendem Pfad:
```
https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1/tistatus/incident
```

Der Abruf erfolgt in Form einer REST-API, im Ergebnis wird somit JSON geliefert. Das Ergebnis beinhaltet bei einem erfolgreichen Abruf immer folgende Struktur: 
```json
{
  "success": true,
  "data": [
    {object}, {object},...
  ]
}
```
Die Ergebnisliste umfasst: 
* pot. Einschränkungen der letzten 14 Tage
* alle Statusschritte der pot. Einschränkungen 


Die Bedeutung der einzelnen Objekte kann der nachfolgenden Beschreibung entnommen werden.
```json
{
  "id": 439,
  "title": "Pot. Einschränkung bei WANDA (autom. Erkennung)",
  "status": 3,
  "app": [
    "WANDA"
  ],
  "createdAt": "2025-06-16T03:35:20.367Z",
  "closedAt": "2025-06-16T03:45:20.797Z",
  "steps": [
    {
      "id": 1426,
      "message": "In den vergangenen 2 Prüfintervallen wurde keine Einschränkung festgestellt.\u003Cbr/\u003EDer Vorfall wird automatisch geschlossen.",
      "status": 3,
      "timestamp": "2025-06-16T03:45:20.793Z",
      "hasMaintenance": false
    },
    {
      "id": 1425,
      "message": "Im letzten Prüfintervall wurden keine Einschränkungen festgestellt",
      "status": 2,
      "timestamp": "2025-06-16T03:40:22.827Z",
      "hasMaintenance": false
    },
    {
      "id": 1424,
      "message": "Es wurden funktionale Einschränkungen innerhalb der TI festgestellt, diese deuten auf einen \u003Cb\u003ETeilausfall\u003C/b\u003E hin.\u003Cbr/\u003ENachstehende pot. Einschränkungen wurden ermittelt:...",
      "status": 4,
      "timestamp": "2025-06-16T03:35:20.370Z",
      "hasMaintenance": false
    }
  ]
}
```

| Attribut  | Beschreibung |
| ------------- | ------------- |
| id  | eindeutige Id  |
| title  | Titel / Überschrift der pot. Einschränkung  |
| status  | aktueller Gesamtstatus der pot. Einschränkung, maßgeblich Status des aktuellsten Statusschrittes |
| app  | Liste von betroffenen TI-Anwendungen  |
| createdAt  | Erstellzeitpunkt der pot. Einschränkung  |
| closedAt  | Abschlusszeitpunkt der pot. Einschränkung, sofern ein Eintrag noch nicht abgeschlossen ist wird "null" als Wert gesetzt  |
| steps  | Liste der Statusschritte, abgebildet als Array  |
| steps > id | eindeutige Id des Statusschrittes |
| steps > message | textuelle Beschreibung des Statusschrittes |
| steps > status | Status des Statusschrittes |
| steps > timestamp | Zeitpunkt der Generierung des Statusschrittes |
| steps > hasMaintenance | kennzeichnet ob eine Wartung bei den ursächlichen Systemen vorliegt |


Übersetzung Statuscodes: 

| Attribut  | Beschreibung |
| ------------- | ------------- |
| 1  | (rot) es wurde ein Komplettausfall der TI-Anwendung(en) festgestellt, die textuelle Beschreibung beinhaltet dabei Details zu den betroffenen Anwendungsszenarien |
| 4  | (gelb) es wurde ein Teilausfall der TI-Anwendung(en) festgestellt, die textuelle Beschreibung beinhaltet dabei Details zu den betroffenen Anwendungsszenarien, es wird vsl. möglich sein die Weiteren Funktionen der Anwendung zu nutzen |
| 2  | (hellblau) das erste 5 Minuten Prüfintervall war erfolgreich |
| 3  | (grün) zwei aufeinander folgende Prüfintervalle waren erfolgreich, sobald dieser Status erreicht wird, wird die pot. Einschränkung abgeschlossen |





### 2.2. Abruf pot. Ursachen

Die Inhalte werden durch eine Route ausgeliefert. Eine Authentifizierung an der Schnittstelle ist nicht erforderlich. Alle dargestellten Abrufe sind via GET Methode durchzuführen.

Abrufbar unter folgendem Pfad:
```
https://ti-lage.prod.ccs.gematik.solutions/lageapi/v1/tistatus/outage
```

Der Abruf erfolgt in Form einer REST-API, im Ergebnis wird somit JSON geliefert. Das Ergebnis beinhaltet bei einem erfolgreichen Abruf immer folgende Struktur: 
```json
{
  "success": true,
  "data": [
    {object}, {object},...
  ]
}
```
Die Ergebnisliste umfasst die vom Probing erkannten beeinträchtigten TI-Komponenten. Die Liste liefert Inhalte der letzten 24 Stunden aus. 


Die Bedeutung der einzelnen Objekte kann der nachfolgenden Beschreibung entnommen werden.
```json
{
  "success": true,
  "data": [
    {
      "ci": "CI-1111111",
      "provider": "Anbieter XY",
      "service": "ePA",
      "slots": [
        {
          "function": "ePA Aktensystem der BIT20",
          "startTimestamp": "2025-06-15T15:25:00.000Z",
          "endTimestamp": "2025-06-15T15:35:00.000Z"
        }
      ]
    },
    {
      "ci": "CI-1111112",
      "provider": "Anbieter ABC",
      "service": "WANDA Basic",
      "slots": [
        {
          "function": "Patientenverfügungen aus dem zentralen Vorsorgeregister",
          "startTimestamp": "2025-06-16T03:30:00.000Z",
          "endTimestamp": "2025-06-16T03:40:00.000Z"
        }
      ]
    }
  ]
}
```

| Attribut  | Beschreibung |
| ------------- | ------------- |
| ci  | logische Produktinstanz  |
| provider  | Betreiber der logischen Produktinstanz  |
| service  | betroffener TI-Dienst / Anwendung |
| slots  | Liste von betroffenen zeitlich zusammenhängenden Einschränkungen  |
| slots > function  | betroffenes Anwendungsszenario der gematik Service Map  |
| slots > startTimestamp  | Startzeitpunkt der Einschränkung der Komponente |
| slots > endTimestamp  | Endzeitpunkt der Einschränkung der Komponente, sofern ein Eintrag noch nicht abgeschlossen ist wird "null" als Wert gesetzt  |
