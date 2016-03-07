<properties
	pageTitle="Machine Learning APIs: Text Analytics | Microsoft Azure"
	description="Mit den Textanalyse-APIs von Microsoft Machine Learning kann unstrukturierter Text für Aufgaben wie Stimmungsanalyse, Schlüsselausdruckextraktion, Sprach- und Themenerkennung analysiert werden."
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/22/2016"
	ms.author="onewth"/>


# Machine Learning APIs: Textanalyse für Stimmungsanalyse, Schlüsselausdruckextraktion, Sprach- und Themenerkennung

## Übersicht

Die Text Analytics-API ist eine Zusammenstellung von [Webdiensten](https://datamarket.azure.com/dataset/amla/text-analytics) zur Textanalyse, die mit Azure Machine Learning erstellt wurden. Die API kann verwendet werden, um unstrukturierten Text für Aufgaben wie Stimmungsanalyse, Schlüsselausdruckextraktion, Sprach- und Themenerkennung zu analysieren. Zur Verwendung dieser API sind keine Trainingsdaten erforderlich. Geben Sie einfach Ihre Textdaten ein. Diese API greift auf erweiterte Techniken der natürlichen Sprachverarbeitung zurück, um bestmögliche Vorhersagen zu treffen.

Auf unserer [Demowebsite](https://text-analytics-demo.azurewebsites.net/) können Sie Textanalysen in Aktion sehen. Hier finden Sie auch [Beispiele](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) in C# und Python zum Implementieren von Textanalysen.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## Stimmungsanalyse

Die API gibt einen numerischen Wert zwischen 0 und 1 zurück. Werte nahe 1 zeigen eine positive Absicht an, Werte nahe 0 zeigen eine negative Absicht an. Mithilfe von Klassifizierungstechniken wird eine Absichtsbewertung generiert. Die Eingabefunktionen für das Klassifizierungsmodul schließen N-Gramme, aus Satzteil-Tags generierte Funktionen sowie Worteinbettung ein. Englisch ist derzeit die einzige unterstützte Sprache.
 
## Schlüsselwortextraktion

Die API gibt eine Liste von Zeichenfolgen zurück, die die wichtigsten Themen im Eingabetext angeben. Wir nutzen Methoden des ausgereiften Toolkits für natürliche Sprachverarbeitung von Microsoft Office. Englisch ist derzeit die einzige unterstützte Sprache.

## Spracherkennung

Die API gibt die erkannte Sprache und einen numerischen Wert von 0 bis 1 zurück. Werte nahe 1 stehen für 100 %-ige Sicherheit, dass die identifizierte Sprache stimmt. Insgesamt werden 120 Sprachen unterstützt.

## Themenerkennung

Diese neu veröffentlichte API gibt die am meisten erkannten Themen für eine Liste übermittelter Textdatensätze zurück. Ein Thema wird anhand eines Schlüsselausdrucks identifiziert, der aus einem oder mehreren darauf bezogenen Wörtern bestehen kann. Dieser API müssen mindestens 100 Datensätze übermittelt werden, aber sie kann Themen in Hunderten bis Tausenden von Datensätzen erkennen. Beachten Sie, dass diese API pro übermittelten Textdatensatz 1 Transaktion berechnet. Die API eignet sich ideal für kurzen, von Menschen geschriebenen Text, z. B. Kritiken und Feedback von Benutzern.

---

## API-Definition

### Header

Stellen Sie sicher, dass Sie die richtigen Header in Ihre Anforderung einbeziehen, was so erfolgen muss:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Sie finden den Schlüssel Ihres Kontos in [Azure Data Market](https://datamarket.azure.com/account/keys).

---

## APIs für einzelne Antworten

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Beispiel für eine Anforderung**

Im nachstehenden Aufruf fordern wir die Stimmungsanalyse für den Ausdruck „Hello World“ an:

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Daraufhin wird eine Antwort wie die folgende zurückgegeben:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Beispiel für eine Anforderung**

Im folgenden Aufruf fordern wir die Schlüsselausdrücke im Text „It was a wonderful hotel to stay at, with unique decor and friendly staff“ (Das Hotel war wunderbar mit einem angenehmen Ambiente und sehr freundlichen Mitarbeitern) an.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Daraufhin wird eine Antwort wie die folgende zurückgegeben:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**Beispiel für eine Anforderung**

Im nachstehenden GET-Aufruf fordern wir die Stimmung für die Schlüsselausdrücke im Text *Hello World* an.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

Daraufhin wird eine Antwort wie die folgende zurückgegeben:

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**Optionale Parameter**

`NumberOfLanguagesToDetect` ist ein optionaler Parameter. Der Standardwert ist 1.

---

## Batch-APIs

Mit dem Text Analytics-Dienst können Sie Stimmung- und Schlüsselphrasen-Extraktionen im Batchmodus durchführen. Beachten Sie, dass jeder der Datensätze als eine Transaktion gezählt wird. Wenn Sie also beispielsweise bei einem Aufruf die Stimmung für 1000 Datensätze anfordern, fallen 1000 Transaktionen an.

Die in das System eingegebenen IDs sind die vom System zurückgegeben IDs. Der Webdienst prüft nicht, ob diese IDs eindeutig sind. Es ist die Verantwortung des Aufrufers, deren Eindeutigkeit zu prüfen.


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Beispiel für eine Anforderung**

Mit folgendem POST-Aufruf fordern wir die Stimmungen der folgenden Sätze im Anforderungstext an: „Hello World“, „Hello FooWorld“ und „Hello My World“.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Anforderungstext:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
	    {"Id":"2","Text":"hello foo world"},
	    {"Id":"3","Text":"hello my world"},
	]}

In der folgenden Antwort erhalten Sie die Bewertungsliste im Zusammenhang mit Ihren Text-IDs:

	{
	  "odata.metadata":"<url>", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Beispiel für eine Anforderung**

Im folgenden Beispiel fordern wir die Liste der Stimmungen für die Schlüsselausdrücke in den folgenden Texten an:

* „It was a wonderful hotel to stay at, with unique decor and friendly staff“ (Das Hotel war wunderbar mit einem angenehmen Ambiente und sehr freundlichen Mitarbeitern)
* „It was an amazing build conference, with very interesting talks“ (Es war eine fantastische Build-Konferenz mit sehr interessanten Gesprächen)
* „The traffic was terrible, I spent three hours going to the airport“ (Der Verkehr war grauenhaft, ich habe drei Stunden zum Flughafen gebraucht)

Diese Anforderung erfolgt als POST-Aufruf an den Endpunkt:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Anforderungstext:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

In der folgenden Antwort erhalten Sie die Schlüsselwortliste im Zusammenhang mit Ihren Text-IDs:

	{ "odata.metadata":"<url>",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

Im folgenden POST-Aufruf fordern wir die Spracherkennung für zwei Texteingaben an:

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

Anforderungstext:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

Dies ergibt die folgende Antwort, wobei Englisch in der ersten Eingabe und Französisch in der zweiten Eingabe erkannt wird:

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

---

## Themenerkennung-APIs

Diese neu veröffentlichte API gibt die am meisten erkannten Themen für eine Liste übermittelter Textdatensätze zurück. Ein Thema wird anhand eines Schlüsselausdrucks identifiziert, der aus einem oder mehreren darauf bezogenen Wörtern bestehen kann. Beachten Sie, dass diese API pro übermittelten Textdatensatz 1 Transaktion berechnet.

Dieser API müssen mindestens 100 Datensätze übermittelt werden, aber sie kann Themen in Hunderten bis Tausenden von Datensätzen erkennen.


### Themen – Auftrag senden

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection

**Beispiel für eine Anforderung**


Im folgenden POST-Aufruf fordern wir Themen für einen Satz von 100 Artikeln an, wobei der zuerst und zuletzt eingegebene Artikel angezeigt werden und zwei „StopPhrases“ enthalten sind.

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/StartTopicDetection HTTP/1.1

Anforderungstext:

	{"Inputs":[
		{"Id":"1","Text":"I loved the food at this restaurant"},
		...,
		{"Id":"100","Text":"I hated the decor"}
	],
	"StopPhrases":[
		"restaurant", “visitor"
	]}

In der folgenden Antwort erhalten Sie die „JobId“ für den gesendeten Auftrag:

	{
		"odata.metadata":"<url>",
		"JobId":"<JobId>"
	}

Eine Liste von Ausdrücken, die aus einem oder mehreren Wörtern bestehen, die nicht als Themen zurückgegeben werden sollen. Sie kann verwendet werden, um sehr allgemeine Themen herauszufiltern. In einem Dataset zu Hotelkritiken könnten z. B. „Hotel“ und „Hostel“ sinnvolle Stoppausdrücke sein.

### Themen – Abrufen von Auftragsergebnissen

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult

**Beispiel für eine Anforderung**

Übergeben Sie die „JobId“ zum Abrufen der Ergebnisse aus dem Schritt „Auftrag senden“, um die Ergebnisse abzurufen. Sie sollten diesen Endpunkt minütlich abrufen, bis die Antwort „Status= ‚Complete‘“ enthält. Es dauert ungefähr 10 Minuten, bis ein Auftrag abgeschlossen ist – bei Aufträgen mit Tausenden von Datensätzen auch länger.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetTopicDetectionResult?JobId=<JobId>


Während der Ausführung fällt die Antwort wie folgt aus:

	{
		"odata.metadata":"<url>",
		"Status":"Running",
 		"TopicInfo":[],
		"TopicAssignment":[],
		"Errors":[]
	}


Die API gibt die Ausgabe folgendermaßen im JSON-Format zurück:

	{
		"odata.metadata":"<url>",
		"Status":"Finished",
		"TopicInfo":[
		{
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Score":8.0,
			"KeyPhrase":"food"
		},
		...
		{
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Score":6.0,
			"KeyPhrase":"decor"
    		}
  		],
		"TopicAssignment":[
		{
			"Id":"1",
			"TopicId":"ed00480e-f0a0-41b3-8fe4-07c1593f4afd",
			"Distance":0.7809
		},
		...
		{
			"Id":"100",
			"TopicId":"a5ca3f1a-fdb1-4f02-8f1b-89f2f626d692",
			"Distance":0.8034
		}
		],
		"Errors":[]


Die Eigenschaften für jeden Teil der Antwort lauten:

**TopicInfo-Eigenschaften**

| Schlüssel | Beschreibung |
|:-----|:----|
| TopicId | Ein eindeutiger Bezeichner für jedes Thema. |
| Punkte | Anzahl der Datensätze, die dem Thema zugewiesen sind. |
| KeyPhrase | Eine Zusammenfassung des Themas in einem Wort oder Ausdruck. Kann aus 1 oder mehreren Wörtern bestehen. |

**TopicAssignment-Eigenschaften**

| Schlüssel | Beschreibung |
|:-----|:----|
| ID | Bezeichner des Datensatzes. Entspricht der in der Eingabe enthaltenen ID. |
| TopicId | Die Themen-ID, der der Datensatz zugewiesen wurde. |
| Distance | Zuverlässigkeit, dass der Datensatz zu dem Thema gehört. Je näher „Distance“ an 0 liegt, desto höher ist die Zuverlässigkeit. |

<!---HONumber=AcomDC_0224_2016-->