<properties
	pageTitle="Machine Learning-App: Text Analytics-Dienst für Absichtsanalyse | Azure"
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
	ms.date="01/27/2015"
	ms.author="luisca"/>


# Machine Learning Text Analytics-Dienst#
##Übersicht
Die Text Analytics-API ist eine Suite von Textanalyse-[Webdiensten]( https://datamarket.azure.com/dataset/amla/text-analytics), die mit Azure Machine Learning erstellt wurde. Die API kann verwendet werden, um unstrukturierten Text für Aufgaben wie Absichtsanalyse und Schlüsselwortextraktion zu analysieren. Zur Verwendung dieser API sind keine Trainingsdaten erforderlich. Geben Sie einfach Ihre Textdaten ein. Zurzeit wird nur die englische Sprache unterstützt. Diese API greift im Kern auf erweiterte Techniken der natürlichen Sprachverarbeitung zurück.

[AZURE.INCLUDE [machine-learning-free-trial](../includes/machine-learning-free-trial.md)] 
 
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
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey);  

Sie erhalten Ihren Kontoschlüssel [hier]( https://datamarket.azure.com/account/keys). 

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

Im nachstehenden GET-Aufruf fordern wir die Absicht für die Schlüsselwörter im Text *Das Hotel war wundervoll, ein angenehmes Ambiente und sehr freundliche Mitarbeiter* an.

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

Header:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64("AccountKey:" + yourActualAccountKey)

Sie erhalten Ihren Kontoschlüssel [hier]( https://datamarket.azure.com/account/keys). 


**Beispielantwort**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 

<!--HONumber=49-->