<properties
	pageTitle="Machine Learning APIs: Text Analytics | Microsoft Azure"
	description="Von Azure Machine Learning gebotene Text Analytics-APIs. Die API kann verwendet werden, um unstrukturierten Text für Aufgaben wie Stimmungsanalyse, Schlüsselwortextraktion und Spracherkennung zu analysieren."
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
	ms.date="11/17/2015"
	ms.author="onewth"/>


# Machine Learning APIs: Text Analytics für Stimmungsanalyse, Schlüsselwortextraktion und Spracherkennung

## Übersicht

Die Text Analytics-API ist eine Zusammenstellung von [Webdiensten](https://datamarket.azure.com/dataset/amla/text-analytics) zur Textanalyse, die mit Azure Machine Learning erstellt wurden. Die API kann verwendet werden, um unstrukturierten Text für Aufgaben wie Stimmungsanalyse, Schlüsselwortextraktion und Spracherkennung zu analysieren. Zur Verwendung dieser API sind keine Trainingsdaten erforderlich. Geben Sie einfach Ihre Textdaten ein. Diese API greift auf erweiterte Techniken der natürlichen Sprachverarbeitung zurück, um bestmögliche Vorhersagen zu treffen.

Auf unserer [Demowebsite](https://text-analytics-demo.azurewebsites.net/) können Sie Textanalysen in Aktion sehen. Hier finden Sie auch [Beispiele](https://text-analytics-demo.azurewebsites.net/Home/SampleCode) in C# und Python zum Implementieren von Textanalysen.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## Stimmungsanalyse

Die API gibt einen numerischen Wert zwischen 0 und 1 zurück. Werte nahe 1 zeigen eine positive Absicht an, Werte nahe 0 zeigen eine negative Absicht an. Mithilfe von Klassifizierungstechniken wird eine Absichtsbewertung generiert. Die Eingabefunktionen für das Klassifizierungsmodul schließen N-Gramme, aus Satzteil-Tags generierte Funktionen sowie Worteinbettung ein. Englisch ist derzeit die einzige unterstützte Sprache.
 
## Schlüsselwortextraktion

Die API gibt eine Liste von Zeichenfolgen zurück, die die wichtigsten Themen im Eingabetext angeben. Wir nutzen Methoden des ausgereiften Toolkits für natürliche Sprachverarbeitung von Microsoft Office. Englisch ist derzeit die einzige unterstützte Sprache.

## Spracherkennung

Die API gibt die erkannte Sprache und einen numerischen Wert von 0 bis 1 zurück. Werte nahe 1 stehen für 100 %-ige Sicherheit, dass die identifizierte Sprache stimmt. Insgesamt werden 120 Sprachen unterstützt.

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
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
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

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
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

<!---HONumber=AcomDC_1125_2015-->