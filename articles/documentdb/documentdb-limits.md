<properties 
	pageTitle="DocumentDB-Einschränkungen für die Vorschauversion | Azure" 
	description="Erfahren Sie mehr über die Grenzen und Kontingenterzwingungen von DocumentDB in der Vorschauversion." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/29/2015" 
	ms.author="mimig"/>


#DocumentDB-Einschränkungen für die Vorschauversion
In der folgenden Tabelle werden die Einschränkungen und Kontingenterzwingungen von DocumentDB bei der Vorschauversion beschrieben. In den meisten Fällen werden die Einschränkungen mit der Absicht erzwungen, Ihr Feedback einzuholen, oder basierend auf den aktuellen Kapazitätseinschränkungen. Wenn Sie in Ihrem Unternehmen die Einschränkungen lockern möchten, rufen Sie uns an. Wir werden uns bemühen, Ihre Wünsche im Rahmen der Einschränkungen des öffentlichen Angebots zu erfüllen.    

|Entität |Kontingent (Standardangebot für die Vorschauversion)|
|-------|--------|
|Datenbankkonten     |5
|Anzahl von Datenbanken pro Datenbankkonto     |100
|Anzahl von Benutzern pro Datenbankkonto - über alle Datenbanken hinweg |500.000
|Anzahl von Berechtigungen pro Datenbankkonto - über alle Datenbanken hinweg   |2.000.000
|Anhangspeicher pro Datenbankkonto      |2 GB
|Maximale Anzahl von Kapazitätseinheiten pro Datenbankkonto       |50
|Anzahl von Auflistungen pro Kapazitätseinheit      |3
|Mindestens zugewiesener Speicher pro Auflistung mit mindestens 1 Dokument    |3,3 GB
|Mindestens zugewiesener Durchsatz pro Auflistung mit mindestens 1 Dokument |667 RUs
|Elastizität einer Auflistung    |0 bis 10 GB
|Maximale Anforderungseinheiten/Sek. pro Auflistung   |2000
|Anzahl gespeicherter Prozeduren, Trigger und UDFs pro Auflistung       |Jeweils 25
|Maximale Ausführungszeit für gespeicherte Prozedur und Trigger     |5 Sekunden
|Bereitgestellter Dokumentspeicher/Kapazitätseinheit |10 GB
|Bereitgestellte Anforderungseinheiten/Sek. pro Auflistung     |2000
|Maximaler Dokumentspeicher pro Datenbank (5 Kapazitätseinheiten)    |500 GB
|Maximale Länge der ID-Eigenschaft    |255 Zeichen
|Standardanzahl von Elementen pro Seite     |100
|Maximale Elemente pro Seite        |1000
|Maximale Anforderungsgröße von Dokumenten und Anlagen       |512KB
|Maximale Anforderungsgröße von gespeicherter Prozedur, Trigger und UDF        |256KB
|Maximale Antwortgröße |1MB
|Maximale Anzahl von eindeutigen Pfaden pro Auflistung       |100
|String |Alle Zeichenfolgen müssen der UTF-8-Codierung entsprechen. Da es sich bei UTF-8 um eine Codierung mit variabler Breite handelt, werden die Zeichenfolgengrößen mithilfe der UTF-8-Bytes ermittelt.
|Maximale Länge der Eigenschaft oder des Werts  |Keine Einschränkung
|Maximale Anzahl von UDFs pro Abfrage     |1
|Maximale Anzahl von JOINs pro Abfrage    |2
|Maximale Anzahl von UND-Klauseln pro Abfrage      |5
|Maximale Anzahl von ODER-Klauseln pro Abfrage       |5

<!--HONumber=49--> 