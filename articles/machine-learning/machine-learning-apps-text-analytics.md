<properties
	pageTitle="Machine Learning-App: Stimmungsanalyse | Microsoft Azure"
	description="Die Text Analytics-API ist eine Suite von Textanalysefunktionen, die mit Azure Machine Learning erstellt wurde. Die API kann verwendet werden, um unstrukturierten Text für Aufgaben wie Absichtsanalyse und Schlüsselwortextraktion zu analysieren."
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="luisca"/>


# Machine Learning-App: Text Analytics-Dienst für Stimmungsanalyse#
##Übersicht
Die Text Analytics-API ist eine Suite von Textanalyse-[Webdiensten](https://datamarket.azure.com/dataset/amla/text-analytics), die mit Azure Machine Learning erstellt wurden. Die API kann verwendet werden, um unstrukturierten Text für Aufgaben wie Absichtsanalyse und Schlüsselwortextraktion zu analysieren. Zur Verwendung dieser API sind keine Trainingsdaten erforderlich. Geben Sie einfach Ihre Textdaten ein. Zurzeit wird nur die englische Sprache unterstützt. Diese API greift im Kern auf erweiterte Techniken der natürlichen Sprachverarbeitung zurück.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]
 
## Stimmungsanalyse##
Die API gibt einen numerischen Wert zwischen 0 und 1 zurück. Werte nahe 1 zeigen eine positive Absicht an, Werte nahe 0 zeigen eine negative Absicht an. Mithilfe von Klassifizierungstechniken wird eine Absichtsbewertung generiert. Die Eingabefunktionen für das Klassifizierungsmodul schließen N-Gramme, aus Satzteil-Tags generierte Funktionen sowie Worteinbettung ein.
 
## Schlüsselwortextraktion##
Die API gibt eine Liste von Zeichenfolgen zurück, die die wichtigsten Themen im Eingabetext angeben. Wir nutzen Methoden des ausgereiften Toolkits für natürliche Sprachverarbeitung von Microsoft Office.

## API-Definition##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**Beispiel für eine Anforderung**

Im nachstehenden GET-Aufruf fordern wir die Absicht für den Ausdruck *Hello World* an.

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

Header:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

Sie erhalten Ihren Kontoschlüssel [hier](https://datamarket.azure.com/account/keys).

**Beispielantwort**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**Beispiel für eine Anforderung**

Im folgenden GET-Aufruf fordern wir die Absicht der Schlüsselausdrücke im Text *It was a wonderful hotel to stay at, with unique decor and friendly staff* (Das Hotel war wunderbar mit einem angenehmen Ambiente und sehr freundlichen Mitarbeitern) an.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Header:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Sie erhalten Ihren Kontoschlüssel [hier](https://datamarket.azure.com/account/keys).


**Beispielantwort**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**Beispiel für eine Anforderung**

Mit folgendem POST-Aufruf fordern wir die Ansichten der folgenden Sätze im Anforderungstext an: "Hello World, Hello FooWorld, Hello My World".

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

Text:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


Header:

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


Sie erhalten Ihren Kontoschlüssel [hier](https://datamarket.azure.com/account/keys).

**Beispielantwort**

In der folgenden Antwort erhalten Sie die Bewertungsliste im Zusammenhang mit Ihren Text-IDs:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[] 
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**Beispiel für eine Anforderung**

Mit dem folgenden GET-Aufruf fordern wir die Absichtsliste für die Schlüsselwörter der folgenden Texte an:

*It was a wonderful hotel to stay at, with unique decor and friendly staff* (Das Hotel war wunderbar mit einem angenehmen Ambiente und sehr freundlichen Mitarbeitern).
 
*It was an amazing build conference, with very interesting talks* (Es war eine fantastische Build-Konferenz mit sehr interessanten Gesprächen).

*The traffic was terrible, I spent three hours going to the airport* (Der Verkehr war grauenhaft, ich brauchte drei Stunden zum Flughafen).



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

Text:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

Header:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

Sie erhalten Ihren Kontoschlüssel [hier](https://datamarket.azure.com/account/keys).


**Beispielantwort**

In der folgenden Antwort erhalten Sie die Schlüsselwortliste im Zusammenhang mit Ihren Text-IDs:

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[ ]
	}

---

**Hinweise zur Stapelverarbeitung**

Die ins System eingegebenen IDs werden von diesem zurückgegeben. Der Webdienst überprüft nicht, ob die IDs eindeutig sind. Es ist die Verantwortung des Aufrufers, deren Eindeutigkeit zu prüfen.
 

<!---HONumber=58_postMigration-->