<properties 
	pageTitle="DocumentDB-Grenzwerte für die Vorschauversion | Azure" 
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


# DocumentDB-Einschränkungen für die Vorschauversion
In der folgenden Tabelle werden die Einschränkungen und Kontingenterzwingungen von DocumentDB bei der Vorschauversion beschrieben. In den meisten Fällen werden die Einschränkungen mit der Absicht erzwungen, Ihr Feedback einzuholen, oder basierend auf den aktuellen Kapazitätseinschränkungen. Wenn Sie in Ihrem Unternehmen die Einschränkungen lockern möchten, rufen Sie uns an. Wir werden uns bemühen, Ihre Wünsche im Rahmen der Einschränkungen des öffentlichen Angebots zu erfüllen.    

|Entität |Kontingent (Standardangebot für die Vorschauversion)|
|-------|--------|
|Datenbankkonten     |5
|Anzahl von Datenbanken pro Datenbankkonto     |100
|Anzahl der Benutzer pro Datenbankkonto - in allen Datenbanken |500.000
|Anzahl der Berechtigungen pro Datenbankkonto - in allen Datenbanken   |2.000.000
|Anlagenspeicher pro Datenbankkonto      |2 GB
|Maximale Anzahl von Kapazitätseinheiten pro Datenbankkonto       |50
|Anzahl der Sammlungen pro Kapazitätseinheit      |3
|Mindestens zugewiesener Speicher pro Sammlung mit mindestens 1 Dokument    |3,3 GB
|Mindestens zugewiesener Durchsatz pro Sammlung mit mindestens 1 Dokument |667 RUs
|Flexibilität einer Sammlung    |0 bis 10 GB
|Maximale Anforderungseinheiten/Sek. pro Sammlung   |2.000
|Anzahl gespeicherter Prozeduren, Trigger und UDFs pro Sammlung       |jeweils 25
|Maximale Ausführungszeit für gespeicherte Prozeduren und Trigger     |5 Sekunden
|Bereitgestellter Dokumentspeicher/Kapazitätseinheit |10 GB
|Bereitgestellte Anforderungseinheiten/Sek./Kapazitätseinheit     |2.000
|Maximaler Dokumentspeicher pro Datenbank (5 Kapazitätseinheiten)    |500 GB
|Maximale Länge der ID-Eigenschaft    |255 Zeichen
|Standardanzahl von Elementen pro Seite     |100
|Maximale Anzahl von Elementen pro Seite        |1.000
|Maximale Anforderungsgröße von Dokumenten und Anlagen       |512 KB
|Maximale Anforderungsgröße von gespeicherter Prozedur, Trigger und UDF        |256 KB
|Maximale Antwortgröße |1 MB
|Maximale Anzahl eindeutiger Pfade pro Sammlung       |100
|Zeichenfolge |Alle Zeichenfolgen müssen der UTF-8-Codierung entsprechen. Da es sich bei UTF-8 um eine Codierung mit variabler Breite handelt, werden die Zeichenfolgengrößen mithilfe der UTF-8-Bytes ermittelt.
|Maximale Länge der Eigenschaft oder des Werts  |Keine praktische Einschränkung
|Maximale Anzahl von UDFs pro Abfrage     |1
|Maximale Anzahl von JOINs pro Abfrage    |2
|Maximale Anzahl von UND-Klauseln pro Abfrage      |5
|Maximale Anzahl von ODER-Klauseln pro Abfrage      |5

<!--HONumber=47-->
