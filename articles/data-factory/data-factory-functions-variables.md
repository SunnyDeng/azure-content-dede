<properties 
	pageTitle="Data Factory – Funktionen und Systemvariablen | Microsoft Azure" 
	description="Enthält eine Liste der Funktionen und Systemvariablen von Azure Data Factory." 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"
	services="data-factory"
/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2016" 
	ms.author="spelluru"/>

# Azure Data Factory – Funktionen und Systemvariablen
Dieser Artikel enthält Informationen zu Funktionen und Variablen, die von Azure Data Factory unterstützt werden.
  
## Data Factory-Systemvariablen

Variablenname | Beschreibung | Objektbereich | JSON-Bereich und Anwendungsfälle
------------- | ----------- | ------------ | ------------------------
WindowStart | Anfang des Zeitfensters der aktuellen Aktivitätsausführung | Aktivität | <ol><li>Geben Sie Abfragen zur Datenauswahl an. Informationen finden Sie in den Artikeln zu Connectors, auf die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) verwiesen wird.</li><li>Übergeben Sie Parameter an das Hive-Skript (siehe obiges Beispiel).</li>
WindowEnd | Ende des Zeitfensters der aktuellen Aktivitätsausführung | Aktivität | Wie oben
SliceStart | Anfang des Zeitfensters für den zu erstellenden Datenslice | Aktivität<br/>Dataset | <ol><li>Geben Sie bei der Arbeit mit [Azure Blob](data-factory-azure-blob-connector.md) und [Dateisystem-Datasets](data-factory-onprem-file-system-connector.md) dynamische Ordnerpfade und Dateinamen an.</li><li>Geben Sie Eingabeabhängigkeiten mit Data Factory-Funktionen in der Auflistung der Aktivitätseingaben an.</li></ol>
SliceEnd | Ende des Zeitfensters für den zu erstellenden Datenslice | Aktivität<br/>Dataset | Wie oben. 

> [AZURE.NOTE] Derzeit erfordert Data Factory, dass der in der Aktivität angegebene Zeitplan exakt mit dem Zeitplan übereinstimmt, der in "availability" für das Ausgabedataset angegeben ist. Das heißt, dass "WindowStart" und "WindowEnd" sowie "SliceStart" und "SliceEnd" immer dem gleichen Zeitraum und einem einzelnen Ausgabeslice zugeordnet sind.
 
## Data Factory-Funktionen 

Sie können Funktionen in Data Factory zusammen mit den zuvor genannten Systemvariablen für folgende Zwecke verwenden:

1.	Angeben von Abfragen zur Datenauswahl (siehe die Artikel zu Connectors, auf die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) verwiesen wird).

	Die Syntax zum Aufrufen einer Data Factory-Funktion ist **$$<function>** für Abfragen zur Datenauswahl und andere Eigenschaften in der Aktivität und den Datasets.  
2. Angeben von Eingabeabhängigkeiten mit Data Factory-Funktionen in der Auflistung der Aktivitätseingaben an (siehe das Beispiel oben).

	$$ ist nicht erforderlich, um Eingabeabhängigkeitsausdrücke anzugeben.

Im folgenden Beispiel wird die **SqlReaderQuery**-Eigenschaft in einer JSON-Datei einem Wert zugewiesen, der von der **Text.Format**-Funktion zurückgegeben wird. Dieses Beispiel verwendet ebenfalls die Systemvariable **WindowStart**, die die Startzeit des Zeitfensters für die Aktivitätsausführung darstellt.
	
	{
	    "Type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	}

### Funktionen

In den folgenden Tabellen werden alle Funktionen in Azure Data Factory aufgelistet:

Kategorie | Funktion | Parameter | Beschreibung
-------- | -------- | ---------- | ----------- 
Time | AddHours(X,Y) | X: DateTime <p>Y: int</p> | Fügt Y Stunden der angegebenen Uhrzeit X hinzu.<p>Beispiel: 05.09.2013 12:00:00 Uhr + 2 Stunden = 05.09.2013 14:00:00 Uhr</p>
Time | AddMinutes(X,Y) | X: DateTime <p>Y: int</p> | Fügt Y Minuten zu X hinzu.<p>Beispiel: 15.09.2013 12:00:00 Uhr + 15 Minuten = 15.09.2013 12:15:00 Uhr</p>
Time | StartOfHour(X) | X: Datetime | Ruft die Startzeit der Stunde ab, die von der Stundenkomponente von X dargestellt wird. <p>Beispiel: StartOfHour von 15.09.2013 05:10:23 Uhr ist 15.09.2013 05:00:00 Uhr</p>
Date | AddDays(X,Y) | X: DateTime<p>Y: int</p> | Fügt Y Tage zu X hinzu. <p>Beispiel: 15.09.2013 12:00:00 Uhr + 2 Tage = 17.09.2013 12:00:00 Uhr</p>
Date | AddMonths(X,Y) | X: DateTime<p>Y: int</p> | Fügt Y Monate zu X hinzu.<p>Beispiel: 15.09.2013 12:00:00 Uhr + 1 Monat = 15.10.2013 12:00:00 Uhr</p> 
Date | AddQuarters(X,Y) | X: DateTime <p>Y: int</p> | Fügt Y * 3 Monate zu X hinzu<p>Beispiel: 15.09.2013 12:00:00 Uhr + 1 Quartal = 15.12.2013 12:00:00 Uhr</p>
Date | AddWeeks(X,Y) | X: DateTime<p>Y: int</p> | Fügt Y * 7 Tage zu X hinzu. <p>Beispiel: 15.09.2013 12:00:00 Uhr + 1 Woche = 22.09.2013 12:00:00 Uhr</p>
Date | AddYears(X,Y) | X: DateTime<p>Y: int</p> | Fügt Y Jahre zu X hinzu.<p>Beispiel: 15.09.2013 12:00:00 Uhr + 1 Jahr = 15.09.2014 12:00:00 Uhr</p>
Date | Day(X) | X: DateTime | Ruft die Komponente "Tag" von X ab.<p>Beispiel: Tag von 15.09.2013 12:00:00 Uhr ist der 9.</p>
Date | DayOfWeek(X) | X: DateTime | Ruft den Tag der Komponente "Woche" von X ab.<p>Beispiel: DayOfWeek von 15.09.2013 12:00:00 Uhr ist Sonntag.</p>
Date | DayOfYear(X) | X: DateTime | Ruft den Tag des Jahres ab, der von der Komponente "Jahr" von X dargestellt wird.<p>Beispiele:<br/>01.12.2015: Tag 335 von 2015<br/>31.12.2015: Tag 365 von 2015<br/>31.12.2016: Tag 366 von 2016 (Schaltjahr)</p>
Date | DaysInMonth(X) | X: DateTime | Ruft die Tage des Monats ab, die von der Komponente "Monat" des Parameters X dargestellt werden.<p>Beispiel: DaysInMonth von 15.09.2013 sind 30, da der Monat September 30 Tage hat.</p>
Date | EndOfDay(X) | X: DateTime | Ruft die Datum/Uhrzeit-Angabe ab, die das Ende des Tages (Komponente "Tag") von X darstellt.<p>Beispiel: EndOfDay 15.09.2013 17:10:23 Uhr ist 15.09.2013 23:59:59 Uhr.</p>
Date | EndOfMonth(X) | X: DateTime | Ruft das Ende des Monats ab, das von der Komponente "Monat" des Parameters X dargestellt wird.<p>Beispiel: EndOfMonth 15.09.2013 17:10:23 Uhr ist 30.09.2013 23:59:59 Uhr (Datum/Uhrzeit-Angabe, die das Ende des Monats September darstellt)</p>
Date | StartOfDay(X) | X: DateTime | Ruft den Beginn des Tages ab, der von der Komponente "Tag" des Parameters X dargestellt wird.<p>Beispiel: StartOfDay 15.09.2013 17:10:23 Uhr ist 15.09.2013 12:00:00 Uhr.</p>
DateTime | From(X) | X: String | Analysieren der Zeichenfolge X in einen Datum/Uhrzeit-Wert.
DateTime | Ticks(X) | X: DateTime | Ruft die Zeiteinheitseigenschaft des Parameters X ab. Eine Zeiteinheit entspricht 100 Nanosekunden. Der Wert dieser Eigenschaft stellt die Anzahl der Zeiteinheiten dar, die seit Mitternacht am 1. Januar 0001 verstrichen sind. 
Text | Format(X) | X: Stringvariable | Formatiert den Text.

#### Beispiel für "Text.Format"

	"defines": { 
	    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
	    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
	    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
	    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
	}

Im Thema [Benutzerdefinierte Formatzeichenfolgen für Datum und Uhrzeit](https://msdn.microsoft.com/library/8kb3ddd4.aspx) werden verschiedene Formatoptionen beschrieben (z. B. „JJ“ im Vergleich zu „JJJJ“).

> [AZURE.NOTE] Bei Verwenden einer Funktion in einer anderen Funktion müssen Sie für die innere Funktion nicht das Präfix **$$** verwenden. Beispiel: $$Text.Format('PartitionKey eq \\'my\_pkey\_filter\_value\\' und RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). Beachten Sie bei diesem Beispiel, dass das Präfix **$$** für die **Time.AddHours**-Funktion nicht verwendet wird.
  

<!---HONumber=AcomDC_0224_2016-->