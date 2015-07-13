<properties 
	pageTitle="DocumentDB-Grenzen und -Kontingente | Azure" 
	description="Erfahren Sie mehr über die Grenzen und Kontingenterzwingungen von DocumentDB" 
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
	ms.date="05/04/2015" 
	ms.author="mimig"/>


#DocumentDB-Grenzen und -Kontingente

In der folgenden Tabelle werden die Grenzen und Kontingenterzwingungen von DocumentDB beschrieben. Kontingente mit einem Sternchen (*) [können angepasst werden. Wenden Sie sich dafür an den Azure-Support](documentdb-increase-limits.md).

|Entität |Kontingent (Standardangebot)|
|-------|--------|
|Datenbankkonten* |5
|Anzahl von Datenbanken pro Datenbankkonto |100
|Anzahl von Benutzern pro Datenbankkonto – über alle Datenbanken hinweg |500.000
|Anzahl von Berechtigungen pro Datenbankkonto – über alle Datenbanken hinweg |2.000.000
|Anhangspeicher pro Datenbankkonto (Vorschaufunktion) |2 GB
|Maximale Anzahl Anforderungseinheiten/Sek. pro Sammlung |2500
|Anzahl gespeicherter Prozeduren, Trigger und UDFs pro Sammlung* |Jeweils 25
|Maximale Ausführungszeit für gespeicherte Prozedur und Trigger |5 Sekunden
|Bereitgestellter Dokumentspeicher/Sammlung |10 GB
|Maximale Anzahl Sammlungen pro Datenbankkonto* |100
|Maximaler Dokumentspeicher pro Datenbank (100 Sammlungen)* |1 TB
|Maximale Länge der ID-Eigenschaft |255 Zeichen
|Maximale Elemente pro Seite |1000
|Maximale Anforderungsgröße von Dokumenten und Anlagen |512 KB
|Maximale Anforderungsgröße von gespeicherter Prozedur, Trigger und UDF |512 KB
|Maximale Antwortgröße |1 MB
|String |Alle Zeichenfolgen müssen der UTF-8-Codierung entsprechen. Da es sich bei UTF-8 um eine Codierung mit variabler Breite handelt, werden die Zeichenfolgengrößen mithilfe der UTF-8-Bytes ermittelt.
|Maximale Länge der Eigenschaft oder des Werts |Keine Grenze
|Maximale Anzahl von UDFs pro Abfrage* |1
|Maximale Anzahl von integrierten Funktionen pro Abfrage |Keine Grenze
|Maximale Anzahl von JOINs pro Abfrage* |2
|Maximale Anzahl von UND-Klauseln pro Abfrage* |5
|Maximale Anzahl von ODER-Klauseln pro Abfrage* |5
|Maximale Anzahl von Werten pro IN-Ausdruck* |100
|Maximale Anzahl von Sammlungserstellungen pro Minute* |5
|Maximale Anzahl von Skalierungsvorgängen pro Minute* |5
 

<!---HONumber=July15_HO1-->