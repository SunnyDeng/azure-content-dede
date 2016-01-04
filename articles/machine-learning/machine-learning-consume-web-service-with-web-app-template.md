<properties
	pageTitle="Verwenden eines Machine Learning-Webdiensts mit einer Web-App-Vorlage | Microsoft Azure"
	description="Verwenden Sie eine Web-App-Vorlage in Azure Marketplace, um einen Vorhersagewebdienst in Azure Machine Learning zu nutzen."
	keywords="Webdienst, Operationalisierung, REST-API, Machine Learning"
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/26/2015"
	ms.author="garye;raymondl"/>

# Verwenden eines Azure Machine Learning-Webdiensts mit einer Web-App-Vorlage

Nachdem Sie Ihr Vorhersagemodell entwickelt und mit Machine Learning Studio (oder Tools wie R oder Python) als Azure-Webdienst bereitgestellt haben, können Sie auf das operationalisierte Modell mit einer REST-API zugreifen.

Es gibt eine Reihe von Möglichkeiten, die REST-API zu nutzen und auf den Webdienst zuzugreifen. Beispielsweise können Sie eine Anwendung in C#, R oder Python schreiben und dabei den Beispielcode verwenden, der für Sie beim Bereitstellen des Webdiensts generiert wurde (verfügbar auf der API-Hilfeseite im Webdienstdashboard in Machine Learning Studio). Sie können aber auch die Microsoft Excel-Beispielarbeitsmappe verwenden, die für Sie erstellt wurde (auch im Webdienstdashboard in Studio verfügbar).

Aber die schnellste und einfachste Möglichkeit, auf Ihren Webdienst zuzugreifen, bieten die Web-App-Vorlagen, die im [Azure Marketplace für Web-Apps](https://azure.microsoft.com/marketplace/web-applications/all/) verfügbar sind.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Azure Machine Learning-Web-App-Vorlagen

Die Web-App-Vorlagen, die auf dem Azure Marketplace verfügbar sind, können eine benutzerdefinierte Web-App erstellen, der die Eingabedaten und die erwarteten Ergebnisse des Webdiensts bekannt sind. Dafür müssen Sie nur der Web-App Zugriff auf den Webdienst und die Daten gewähren, und die Vorlage übernimmt den Rest.

Zwei Vorlagen sind verfügbar:

- [Azure ML-Web-App-Vorlage für den Anfrage-/Antwort-Dienst](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/)
- [Azure ML-Web-App-Vorlage für den Batchausführungsdienst](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/)

Jede Vorlage erstellt mit dem API-URI und dem Schlüssel für den Webdienst eine ASP.NET-Beispielanwendung und stellt sie als Website in Azure bereit. Die Vorlage für den Anfrage-/Antwort-Dienst (Request-Response Service, RRS) erstellt eine Web-App, die es Ihnen ermöglicht, eine einzelne Zeile mit Daten an den Webdienst zu senden, um ein einzelnes Ergebnis zu erhalten. Die Vorlage für den Batchausführungsdienst (Batch Execution Service, BES) erstellt eine Web-App, die es Ihnen ermöglicht, viele Zeilen mit Daten zu senden, um mehrere Ergebnisse zu erhalten.

Es ist keine Codierung erforderlich, um diese Vorlagen zu verwenden. Sie geben nur den URI-API und den Schlüssel an, und die Vorlage erstellt die Anwendung für Sie.

## Verwenden der Vorlage für den Anfrage-/Antwort-Dienst (RRS)

Nachdem Sie den Webdienst bereitgestellt haben, können Sie den unten aufgeführten Schritten folgen, um die RRS-Web-App-Vorlage zu verwenden, wie im folgenden Diagramm gezeigt.

![Prozess zur Verwendung einer RRS-Webvorlage][image1]

1. Öffnen Sie in Machine Learning Studio die Registerkarte **Webdienste** und dann den Webdienst, auf den Sie zugreifen möchten. Kopieren Sie den Schlüssel unter **API-Schlüssel**, und speichern Sie ihn.

	![API-Schlüssel][image3]

2. Öffnen Sie die API-Hilfeseite **ANFORDERUNG/ANTWORT**. Kopieren Sie oben auf der Seite unter **Anforderung** den Wert für **Anforderungs-URI**, und speichern Sie ihn. Dieser Wert sieht wie folgt aus:

		https://ussouthcentral.services.azureml.net/workspaces/<workspace-id>/services/<service-id>/execute?api-version=2.0&details=true

	![Anforderungs-URI][image4]

3. Wechseln Sie zur [Azure ML-Web-App für den Anfrage-/Antwort-Dienst](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlaspnettemplateforrrs/), und klicken Sie auf **Web-App erstellen**. Über die Vorlage gelangen Sie zum klassischen Azure-Portal von Microsoft, wo die neue Web-App erstellt wird.

	- Geben Sie Ihrer Web-App einen eindeutigen Namen. Die URL der Web-App besteht aus diesem Namen gefolgt von `.azurewebsites.net.`. Beispiel: `http://carprediction.azurewebsites.net.`

	- Wählen Sie das Azure-Abonnement und die Dienste aus, mit denen der Webdienst ausgeführt wird.

	- Klicken Sie auf **Erstellen**.

	![Web-App erstellen][image5]

4. Wenn Azure das Bereitstellen der Web-App beendet hat, klicken Sie auf die **URL** auf der Seite mit den Web-App-Einstellungen in Azure, oder geben Sie die URL in einem Webbrowser ein. Zum Beispiel, `http://carprediction.azurewebsites.net.`

5. Bei der ersten Ausführung der Web-App werden Sie aufgefordert, die **Post-URL der API** und den **API-Schlüssel** anzugeben. Geben Sie die Werte ein, die Sie zuvor gespeichert haben:
	- **Anforderungs-URI** von der API-Hilfeseite für die **Post-URL der API**
	- **API-Schlüssel** aus dem Webdienstdashboard für den **API-Schlüssel**.

	Klicken Sie auf **Senden**.

	![Post-URI und API-Schlüssel eingeben][image6]

6. Die Web-App zeigt die Seite **Web-App-Konfiguration** mit den aktuellen Einstellungen für den Webdienst an. Hier können Sie die von der Web-App verwendeten Einstellungen ändern.

	> [AZURE.NOTE]Wenn Sie hier Einstellungen ändern, werden sie nur für diese Web-App geändert. Die Standardeinstellungen des Webdiensts werden nicht geändert. Angenommen, Sie ändern hier die **Beschreibung**. In diesem Fall wird die im Webdienstdashboard in Machine Learning Studio angezeigte Beschreibung nicht geändert.

	Wenn Sie fertig sind, klicken Sie auf **Änderungen speichern** und dann auf **Zur Startseite wechseln**.

7. Auf der Startseite können Sie Werte eingeben, die an den Webdienst gesendet werden. Wenn Sie auf **Senden** klicken, wird das Ergebnis zurückgegeben.

Wenn Sie zur Seite **Konfiguration** zurückkehren möchten, wechseln Sie zur Seite `setting.aspx` der Web-App. Beispiel: `http://carprediction.azurewebsites.net/setting.aspx.` Sie werden aufgefordert, den API-Schlüssel erneut einzugeben. Sie benötigen ihn, um auf die Seite zuzugreifen und die Einstellungen zu aktualisieren.

Sie können die Web-App im klassischen Azure-Portal wie andere Web-Apps beenden, neu starten oder löschen. Solange sie ausgeführt wird, können Sie zur Webadresse der Startseite wechseln und neue Werte eingeben.

## Verwenden der Vorlage für den Batchausführungsdienst (BES)

Sie können die BES-Web-App-Vorlage auf die gleiche Weise wie die RRS-Vorlage verwenden. Der Unterschied ist, dass die erstellte Web-App es ermöglicht, mehrere Datenzeilen zu senden und mehrere Ergebnisse zu empfangen.

Die Ergebnisse von einem Webdienst mit Batchausführung werden in einem Azure-Speichercontainer gespeichert. Die Eingabewerte können aus einem Azure-Speicher oder einer lokalen Datei stammen. Daher benötigen Sie einen Azure-Speichercontainer für die Ergebnisse, die von der Web-App zurückgegeben werden, und Sie müssen Ihre Eingabedaten vorbereiten.

![Prozess zur Verwendung einer BES-Webvorlage][image2]

1. Führen Sie das gleiche Verfahren zum Erstellen der BES-Web-App wie für die RRS-Vorlage aus. Es gelten jedoch die folgenden Ausnahmen:
	- Rufen Sie den **Anforderungs-URI** von der API-Hilfeseite **BATCHAUSFÜHRUNG** für den Webdienst ab.
	- Wechseln Sie zur [Azure ML-Web-App-Vorlage für den Batchausführungsdienst](https://azure.microsoft.com/marketplace/partners/microsoft/azuremlbeswebapptemplate/), um die BES-Vorlage auf dem Azure Marketplace zu öffnen, und klicken Sie auf **Web-App erstellen**.

2. Um anzugeben, wo die Ergebnisse gespeichert werden sollen, geben Sie die Informationen zum Zielcontainer auf der Startseite der Web-App an. Geben Sie zudem an, woher die Web-App die Eingabewerte erhält: aus einer lokalen Datei oder einem Azure-Speichercontainer. Klicken Sie auf **Senden**.

	![Speicherinformationen][image7]

Die Web-App zeigt eine Seite mit dem Auftragsstatus an. Nach Abschluss des Auftrags werden Sie über den Speicherort der Ergebnisse im Azure-Blobspeicher informiert. Sie haben auch die Möglichkeit, die Ergebnisse in eine lokale Datei herunterzuladen.

## Weitere Informationen

Erfahren Sie mehr:

- Weitere Informationen zum Erstellen eines Machine Learning-Experiments mit Machine Learning Studio finden Sie unter [Erstellen Ihres ersten Experiments im Azure Machine Learning Studio](machine-learning-create-experiment.md).

- Informationen zum Bereitstellen Ihres Machine Learning-Experiments als Webdienst finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts](machine-learning-publish-a-machine-learning-web-service.md).

- Andere Möglichkeiten für den Zugriff auf den Webdienst finden Sie unter [Nutzen eines Azure Machine Learning-Webdiensts](machine-learning-consume-web-services.md).


[image1]: media\machine-learning-consume-web-service-with-web-app-template\rrs-web-template-flow.png
[image2]: media\machine-learning-consume-web-service-with-web-app-template\bes-web-template-flow.png
[image3]: media\machine-learning-consume-web-service-with-web-app-template\api-key.png
[image4]: media\machine-learning-consume-web-service-with-web-app-template\post-uri.png
[image5]: media\machine-learning-consume-web-service-with-web-app-template\create-web-app.png
[image6]: media\machine-learning-consume-web-service-with-web-app-template\web-service-info.png
[image7]: media\machine-learning-consume-web-service-with-web-app-template\storage.png

<!---HONumber=AcomDC_1203_2015-->