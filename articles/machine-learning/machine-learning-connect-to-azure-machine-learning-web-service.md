<properties
	pageTitle="Verbindung mit einem Machine Learning-Webdienst | Microsoft Azure"
	description="Stellen Sie mit C# oder Python mithilfe eines Autorisierungsschlüssels eine Verbindung mit einem Azure Machine Learning-Webdienst her."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015" 
	ms.author="derrickv" />


# Herstellen einer Verbindung mit einem Azure Machine Learning-Webdienst
Die Azure Machine Learning-Entwicklungsumgebung ist eine Webdienst-API zum Erstellen von Vorhersagen aus Eingabedaten in Echtzeit oder im Stapelmodus. Sie verwenden Azure Machine Learning Studio zum Erstellen von Vorhersagen und zum Bereitstellen eines Azure Machine Learning-Webdiensts.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Informationen zum Erstellen und Bereitstellen eines Azure Machine Learning-Webdiensts mit Studio finden Sie unter:

- [Bereitstellen eines Machine Learning-Webdiensts](machine-learning-publish-a-machine-learning-web-service.md)
- [Erste Schritte mit ML Studio](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Azure-Machine Learning-Vorschau](https://studio.azureml.net/)
- [Machine Learning Dokumentationscenter](http://azure.microsoft.com/documentation/services/machine-learning/)

## Azure Machine Learning-Webdienst ##

Mit dem Azure Machine Learning (ML)-Webdienst kommunizieren externe Anwendungen in Echtzeit mit einem ML-Workflow-Bewertungsmodell. Ein ML-Webdienstaufruf gibt Ergebnisse an eine externe Anwendung zurück. Zur Durchführung eines ML Webdienstaufrufs übergeben Sie einen API-Schlüssel, der beim Bereitstellen einer Vorhersage erstellt wird. Der ML-Webdienst basiert auf REST, einer verbreiteten Architektur für Webprogrammierungsprojekte.

Azure Machine Learning verfügt über zwei Arten von Diensten:

- Antwort-/Anfrage-Dienst (Request-Response Service, RRS): ein hochskalierbarer Webdienst mit niedriger Latenz, der eine Schnittstelle für zustandslose Modelle bereitstellt, die in ML Studio erstellt und bereitgestellt wurden.
- Batch Execution Service (BES) – ein asynchroner Dienst für die Bewertung eines Stapels für Datensätze.

Weitere Informationen zu Azure Machine Learning-Webdiensten finden Sie unter [Bereitstellen von Azure Machine Learning-Webdiensten](machine-learning-publish-a-machine-learning-web-service.md).

## Abrufen eines Autorisierungsschlüssels für Azure Machine Learning ##
Sie erhalten einen Webdienst-API-Schlüssel von einem ML-Webdienst. Sie können ihn über Microsoft Azure Machine Learning Studio oder über das Azure-Verwaltungsportal abrufen.
### Microsoft Azure Machine Learning Studio ###
1. Klicken Sie in Microsoft Azure Machine Learning Studio links auf **WEB SERVICES**.
2. Klicken Sie auf einen Webdienst. Der "API Key" befindet sich auf der Registerkarte **DASHBOARD**.

### Azure-Verwaltungsportal ###

1. Klicken Sie links auf **MACHINE LEARNING**.
2. Klicken Sie auf einen Arbeitsbereich.
3. Klicken Sie auf **WEB SERVICES**.
4. Klicken Sie auf einen Webdienst.
5. Klicken Sie auf einen Endpunkt. "API KEY" befindet sich unten rechts.

## <a id="connect"></a>Herstellen einer Verbindung mit einem Azure Machine Learning-Webdienst

Sie können mit jeder Programmiersprache, die HTTP-Anforderungen und -Antworten unterstützt, eine Verbindung zu einem Azure Machine Learning-Webdienst herstellen. Sie können Beispiele in C#, Python und R auf einer für den Azure ML-Webdienst-Hilfeseite anzeigen.

### So zeigen Sie Azure ML Webdienst-API-Hilfeseiten an ###
Wenn Sie einen Webdienst bereitstellen, wird eine Azure ML-API-Hilfeseite erstellt. Weitere Informationen finden Sie unter [Azure Machine Learning – exemplarische Vorgehensweise – Veröffentlichen eines Webdiensts](machine-learning-walkthrough-5-publish-web-service.md).


**Anzeigen einer Azure ML-API-Hilfeseite** in Microsoft Azure Machine Learning Studio:

1. Wählen Sie **WEB SERVICES** aus.
2. Wählen Sie einen Webdienst aus.
3. Wählen Sie **API help page** - **REQUEST/RESPONSE** oder **BATCH EXECUTION** aus.


**Azure ML-API-Hilfeseite** Die Azure ML-API-Hilfeseite enthält Details zu einem Vorhersage-Webdienst.



### C#-Beispiel ###

Verwenden Sie zum Verbinden mit einem Azure ML-Webdienst einen **HttpClient** durch Übergeben von ScoreData. ScoreData enthält ein FeatureVector, ein n-dimensionaler Vektor von numerischen Funktionen, die die ScoreData darstellen. Sie authentifizieren sich mit einem API-Schlüssel beim Azure ML-Dienst.

Für die Verbindung zu einem ML-Webdienst muss das NuGet-Paket **Microsoft.AspNet.WebApi.Client** installiert sein.

**Installieren von Microsoft.AspNet.WebApi.Client NuGet in Visual Studio**

1. Veröffentlichen Sie das Dataset Download von Webdienst „UCI: Adult 2 class dataset“.
2. Klicken Sie auf **Tools** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
2. Wählen Sie **Install-Package Microsoft.AspNet.WebApi.Client aus**.

**So führen Sie das Codebeispiel aus**

1. Veröffentlichen Sie das Experiment „Sample 1: Download dataset from UCI: Adult 2 class dataset“, Teil der Azure ML-Beispielauflistung.
2. Weisen Sie "apiKey" den Schlüssel von einem Webdienst zu. Weitere Informationen finden Sie unter "Abrufen eines Autorisierungsschlüssels für Azure ML".
3. Weisen Sie "serviceUri" die Anforderungs-URI zu.


### Python-Beispiel ###

Verwenden Sie zum Verbinden mit einem Azure ML-Webdienst die **urllib2**-Bibliothek, durch Übergeben von ScoreData. ScoreData enthält ein FeatureVector, ein n-dimensionaler Vektor von numerischen Funktionen, die die ScoreData darstellen. Sie authentifizieren sich mit einem API-Schlüssel beim Azure ML-Dienst.


**So führen Sie das Codebeispiel aus**

1. Veröffentlichen Sie das Experiment „Sample 1: Download dataset from UCI: Adult 2 class dataset“, Teil der Azure ML-Beispielauflistung.
2. Weisen Sie "apiKey" den Schlüssel von einem Webdienst zu. Weitere Informationen finden Sie unter "Abrufen eines Autorisierungsschlüssels für Azure ML".
3. Weisen Sie "serviceUri" die Anforderungs-URI zu. Weitere Informationen finden Sie unter "Abrufen einer Anforderungs-URI".

<!---HONumber=Oct15_HO3-->