<properties 
	pageTitle="Erstellen von Datasets"
	description="Lernen Sie Azure Data Factory-Datasets kennen, und erfahren Sie, wie Sie sie erstellen können."
	services="data-factory"
	documentationCenter=""
	authors="spelluru"
	manager="jhubbard"
	editor="monicar"/>

<tags 
	ms.service="data-factory"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="spelluru"/>

# Datasets

## Beschreibung
Ein Dataset ist eine logische Beschreibung der Daten. Die Daten, die beschrieben werden, können von einfachen Bytes und halbstrukturieren Daten wie CSV-Dateien bis hin zu relationalen Tabellen oder sogar Modellen reichen. Der Mechanismus (Adresse, Protokoll, Authentifizierungsschema) für den Zugriff auf die Daten ist im verknüpften Dienst definiert, und in der Definition des Datasets wird auf den Mechanismus verwiesen.

## Syntax

	{
	    "name": "<name of dataset>",
	    "properties":
	    {
	       "structure": [ ],
	       "type": "<type of dataset>",
			"external": <boolean flag to indicate external data>,
	        "typeProperties":
	        {
	        },
	        "availability":
	        {
	
	        },
	       "policy": 
	        {      
	
	        }
	    }
	}

**Syntaxdetails**

| Eigenschaft | Beschreibung | Erforderlich | Standard |
| -------- | ----------- | -------- | ------- |
| Name | Name des Datasets | Ja | – |
| Strukturdefinition | <p>Schema des Datasets</p><p>Im Abschnitt [Dataset: Structure](#Structure) finden Sie weitere Details.</p> | Nein. | – |
| Typ | Typ des Datasets | Ja | – |
| typeProperties | <p>Eigenschaften, die dem ausgewählten Typ entsprechen</p><p>Im Abschnitt [Dataset: Type](#Type) finden Sie ausführliche Informationen über die unterstützten Typen und deren Eigenschaften.</p> | Ja | – |
| external | Boolesches Flag, das angibt, ob ein Dataset explizit durch eine Data Factory-Pipeline erstellt wird oder nicht. | Nein | false | 
| Availability | <p>Definiert das Verarbeitungsfenster oder das Modell für das Aufteilen in Slices für die Dataset-Produktion. </p><p>Unter [Dataset: Availability](#Availability) finden Sie weitere Informationen.</p><p>Im Artikel [Planung und Ausführung](data-factory-scheduling-and-execution.md) finden Sie weitere Informationen zum Modell für das Aufteilen von Datasets in Slices.</p> | Ja | –
| policy | Definiert die Kriterien oder die Bedingung, die die Dataset-Slices erfüllen müssen. <p>Unter [Dataset: Policy](#Policy) finden Sie weitere Informationen.</p> | Nein | – |

### Beispiel

Im Folgenden finden Sie ein Beispiel für ein Dataset, das eine Tabelle mit dem Namen **MyTable** in der **Azure SQL-Datenbank** darstellt. Die Azure SQL-Datenbank-Verbindungszeichenfolgen werden in **AzureSqlLinkedService** definiert, worauf in diesem Dataset verwiesen wird. Dieses Dataset wird täglich in Slices aufgeteilt.

	{
	    "name": "DatasetSample",
	    "properties": {
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": 
	        {
	            "tableName": "MyTable"
	        },
	        "availability": 
	        {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

## <a name="Structure"></a>Dataset: Structure

Der Abschnitt **Structure** bestätigt das Schema des Datasets. Er enthält die Auflistung der Spalten und den Typ dieser Spalten. Jede Spalte enthält die folgenden Eigenschaften:

Eigenschaft | Beschreibung | Erforderlich | Standard  
-------- | ----------- | -------- | -------
Name | Name der Spalte | Nein | – 
Typ | Datentyp der Spalte | Nein | – 

### Beispiel

Im folgenden Beispiel hat das Dataset die drei Spalten "slicetimestamp", "projectname" und "pageviews".

	structure:  
	[ 
	    { "name": "slicetimestamp", "type": "String"},
	    { "name": "projectname", "type": "String"},
	    { "name": "pageviews", "type": "Decimal"}
	]

## <a name="Type"></a> Dataset: Type

Die unterstützten Datenquellen und die Dataset-Typen werden ausgerichtet. Informationen zu den Typen und der Konfiguration von Datasets finden Sie in den Connector-Themen, auf die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) verwiesen wird.

## <a name="Availability"></a> Dataset: Availability

Der Abschnitt "Availability" in einem Dataset definiert das Verarbeitungsfenster oder das Modell für das Aufteilen in Slices für die Dataset-Produktion. Unter "Dataset: Slice" finden Sie weitere Informationen zum Modell für das Aufteilen von Datasets in Slices und Abhängigkeit.

| Eigenschaft | Beschreibung | Erforderlich | Standard |
| -------- | ----------- | -------- | ------- |
| frequency | Gibt die Zeiteinheit für die Produktion der Dataset-Slices an.<p>** Unterstützte Häufigkeit **: "Minute", "Hour", "Day", "Week", "Month"</p> | Ja | – |
| interval | Gibt einen Multiplikator für die Häufigkeit an<p>"Frequency x interval" bestimmt, wie oft der Slice erzeugt wird.</p><p>Wenn das Dataset auf Stundenbasis in Slices aufgeteilt werden soll, legen Sie **Frequency** auf **Hour** und **interval** auf **1** fest.</p><p>**Hinweis:** Wenn Sie "Frequency" auf "Minute" festlegen, sollten Sie "interval" auf mindestens 15 festlegen.</p> | Ja | – |
| style | Gibt an, ob der Slice am Anfang/Ende des Intervalls erzeugt werden soll.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><p>Wenn "Frequency" auf "Month" und "style" auf "EndOfInterval" festgelegt ist, wird der Slice am letzten Tag des Monats erstellt. Wenn "style" auf "StartOfInterval" festgelegt ist, wird der Slice am ersten Tag des Monats erstellt.<.p><p>Wenn "Frequency" auf "Day" und "style" auf "EndOfInterval" festgelegt ist, wird der Slice in der letzten Stunde des Tages erstellt.</p>Wenn "Frequency" auf "Hour" und "style" auf "EndOfInterval" festgelegt ist, wird der Slice am Ende der Stunde erstellt. Ein Slice für den Zeitraum 13:00–14:00 Uhr wird z. B. u: 14.00 Uhr erstellt.</p> | Nein | EndOfInterval |
| anchorDateTime | Definiert die absolute Position in der Zeit, die der Scheduler benötigt, um Dataset-Slicegrenzen zu berechnen. <p>**Hinweis:** Wenn "anchorDateTime" über Datumsteile verfügt, die präziser als die Häufigkeit sind, werden die präziseren Teile ignoriert. Wenn z. B. **interval** auf **stündlich** festgelegt ist ("frequency": "hour" und "interval": 1) und **anchorDateTime** **Minuten und Sekunden** enthält, werden die **Minuten- und Sekundenteile** von "anchorDateTime" ignoriert.</p>| Nein | 01/01/0001 |
| Offset | Zeitspanne, um die Anfang und Ende aller Dataset-Slices verschoben werden. <p>**Hinweis:** Wenn sowohl "anchorDateTime" als auch "Offset" angegeben wird, ist das Ergebnis die kombinierte Verschiebung.</p> | Nein | – |

### Beispiele zu "anchorDateTime"

**Beispiel:** 23-Stunden-Dataset-Slices mit Startbeginn "2007-04-19T08:00:00"

	"availability":	
	{	
		"frequency": "Hour",		
		"interval": "23",	
		"anchorDataTime":"2007-04-19T08:00:00"	
	}


### Beispiel zu Offset

Tägliche Slices, die um 6:00 Uhr anstelle des Standards Mitternacht beginnen.

	"availability":
	{
		"frequency": "Daily",
		"interval": "1",
		"offset": "06:00:00"
	}

In diesem Fall wird "SliceStart" um 6 Stunden auf 6:00 Uhr verschoben.

Für einen zwölfmonatigen Plan ("frequency" = "month"; "interval" = 12) bedeutet "offset": 60.00:00:00 den Beginn am 2. oder 3. März jedes Jahres (60 Tage ab dem Anfang des Jahres, wenn "style" = "StartOfInterval"), abhängig davon, ob es sich um ein Schaltjahr handelt.



## <a name="Policy"></a>Dataset: Policy

Der Abschnitt "Policy" im Dataset definiert die Kriterien oder die Bedingung, die die Dataset-Slices erfüllen müssen.

### Überprüfungsrichtlinien

| Richtlinienname | Beschreibung | Angewendet auf | Erforderlich | Standard |
| ----------- | ----------- | ---------- | -------- | ------- |
| minimumSizeMB | Überprüft, ob die Daten in einem Azure-Blob die minimale Größenanforderung (in Megabytes) erfüllen. | Azure Blob | Nein | – |
|minimumRows | Überprüft, ob die Daten in einer Azure SQL-Datenbank oder einer Azure-Tabelle die minimale Anzahl von Zeilen enthalten. | <ul><li>Azure SQL-Datenbank</li><li>Azure-Tabelle</li></ul> | Nein | –

#### Beispiele

**minimumSizeMB:**

	"policy":
	
	{
	    "validation":
	    {
	        "minimumSizeMB": 10.0
	    }
	}

**minimumRows**

	"policy":
	{
		"validation":
		{
			"minimumRows": 100
		}
	}

### ExternalData-Richtlinien

Externe Datasets werden nicht durch eine Pipeline erstellt, die in der Data Factory ausgeführt wird. Wenn das Dataset als extern gekennzeichnet ist, kann die ExternalData-Richtlinie definiert werden, um das Verhalten der Dataset-Sliceverfügbarkeit zu beeinflussen.

| Name | Beschreibung | Erforderlich | Standardwert |
| ---- | ----------- | -------- | -------------- |
| dataDelay | <p>Zeit, um die die Prüfung der Verfügbarkeit der externen Daten für den angegebenen Slice verzögert wird. Wenn die Daten z. B. stündlich verfügbar sein sollen, die Überprüfung der externen Daten verfügbar und der entsprechende Slice bereit ist, kann eine Verzögerung durch "dataDelay" ausgeführt werden.</p><p>Diese bezieht sich nur auf die aktuelle Zeit; wenn es z. B. gerade 13:00 Uhr ist und dieser Wert 10 Minuten beträgt, beginnt die Überprüfung um 13:10 Uhr.</p><p>Diese Einstellung wirkt sich nicht auf Slices in der Vergangenheit aus (ohne Verzögerung werden Slices verarbeitet, für die Folgendes gilt: Slice-Endzeit + "dataDelay" < jetzt).</p> <p>Ein Zeitwert von mehr als 23:59 Stunden muss im Format "Tag.Stunden:Minuten:Sekunden" angegeben werden. Um beispielsweise 24 Stunden anzugeben, verwenden Sie nicht 24:00:00, sondern stattdessen 1.00:00:00. Wenn Sie 24:00:00 verwenden, wird dies als 24 Tage (24.00:00:00) gewertet. Für 1 Tag und 4 Stunden geben Sie 1.04:00:00 an. </p>| Nein | 0 |
| retryInterval | Die Wartezeit zwischen einem Fehler und dem nächsten Wiederholungsversuch. Gilt für die aktuelle Zeit. Wenn der vorherige Versuch fehlgeschlagen ist, ist dies die Wartezeit nach dem letzten Versuch. <p>Wenn es jetzt gerade 13:00 Uhr ist, beginnt der erste Versuch. Wenn die Ausführung der ersten Überprüfung 1 Minute gedauert hat und ein Fehler aufgetreten ist, findet die nächste Wiederholung um 13:00 + 1 Min. (Dauer) + 1 Min. (Wiederholungsintervall) = 1:02 Uhr statt. </p><p>Für Slices in der Vergangenheit gibt es keine Verzögerung. Die Wiederholung erfolgt sofort.</p> | Nein | 00:01:00 (1 Minute) | 
| retryTimeout | Das Timeout für die einzelnen Wiederholungsversuche.<p>Wenn dieser Wert auf 10 Minuten festgelegt ist, muss die Überprüfung innerhalb von 10 Minuten abgeschlossen werden. Wenn die Ausführung der Überprüfung länger als 10 Minuten dauert, wird das Timeout für die Wiederholung wirksam.</p><p>Wenn für alle Überprüfungsversuche ein Timeout wirksam wird, wird der Slice als "TimedOut" gekennzeichnet.</p> | Nein | 00:10:00 (10 Minuten) |
| maximumRetry | Gibt an, wie oft die Verfügbarkeit der externen Daten überprüft werden soll. Der zulässige Höchstwert ist 10. | Nein | 3 | 

#### Weitere Beispiele

Wenn Sie eine Pipeline jeden Monat an einem bestimmten Tag und zu einer bestimmten Uhrzeit ausführen müssen (beispielsweise am 3. jedes Monats um 8:00 Uhr), könnten Sie mit dem **offset**-Tag das Datum und die Uhrzeit der Ausführung angeben.

	{
	  "name": "MyDataset",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Month",
	      "interval": 1,
	      "offset": "3.08:10:00",
	      "style": "StartOfInterval"
	    }
	  }
	}

## Feedback senden
Über Ihr Feedback zu diesem Artikel würden wir uns sehr freuen. Bitte nehmen Sie sich einen Moment Zeit, und senden Sie uns Ihr Feedback per [E-Mail](mailto:adfdocfeedback@microsoft.com?subject=data-factory-create-datasets.md).





  

<!---HONumber=September15_HO1-->