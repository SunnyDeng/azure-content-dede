<properties
	pageTitle="Bereitstellen eines Machine Learning-Webdiensts | Microsoft Azure"
	description="So konvertieren Sie ein Trainingsexperiment in ein Vorhersageexperiment, bereiten es auf die Bereitstellung vor und stellen es dann aus Azure Machine Learning-Webdienst bereit"
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
	ms.date="11/03/2015"
	ms.author="garye"/>

# Bereitstellen eines Azure Machine Learning-Webdiensts

Azure Machine Learning ermöglicht es Ihnen, Vorhersageanalyselösungen zu erstellen, zu testen und bereitzustellen .

Allgemein betrachtet, geschieht dies in drei Schritten:

- **[Erstellen eines Trainingsexperiments]** – Azure Machine Learning Studio ist eine gemeinsame visuelle Entwicklungsumgebung, mit der Sie ein Vorhersageanalysemodell mithilfe von Trainingsdaten, die Sie bereitstellen, trainieren und testen können.
- **[Konvertieren in ein Vorhersageexperiment]** – Sobald Ihr Modell mit vorhandenen Daten trainiert wurde, können Sie es verwenden, um neue Daten zu bewerten. Sie bereiten das Experiment auf die Bewertung vor und optimieren es.
- **[Als Webdienst bereitstellen]** – Mit nur einem Klick können Sie nun das Bewertungsexperiment als Azure-Webdienst bereitstellen. Die Benutzer Daten an Ihr Modell senden und die Vorhersagen Ihres Modells empfangen.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

Nachdem Sie den Webdienst bereitgestellt haben, können Sie Folgendes tun:

- **[Zugreifen auf den Webdienst]** über die Webdienst-API,
- **[Verwalten des Webdiensts]** über das Azure-Verwaltungsportal und
- **[Aktualisieren des Webdiensts]**, wenn sich das Modell ändert.

[Erstellen eines Trainingsexperiments]: #create-a-training-experiment
[Konvertieren in ein Vorhersageexperiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Als Webdienst bereitstellen]: #deploy-the-predictive-experiment-as-a-web-service
[Zugreifen auf den Webdienst]: #access-the-web-service
[Verwalten des Webdiensts]: #manage-the-web-service-in-the-azure-management-portal
[Aktualisieren des Webdiensts]: #update-the-web-service


## Erstellen eines Trainingsexperiments

Zum Trainieren eines Vorhersageanalysemodells erstellen Sie in Azure Machine Learning Studio ein Trainingsexperiment, in das Sie verschiedene Module zum Laden der Trainingsdaten, zum ggf. erforderlichen Vorbereiten der Daten, Anwenden von Lernalgorithmen und Auswerten der Ergebnisse einbinden. Sie können ein Experiment iterieren und unterschiedliche Lernalgorithmen zum Vergleichen und Bewerten der Ergebnisse ausprobieren.

Der Prozess der Erstellung und Verwaltung von Trainingsexperimenten an anderer Stelle ausführlicher behandelt. Weitere Informationen und Beispiele finden Sie in diesen Artikeln:

- [Erstellen eines einfachen Experiments im Azure Machine Learning-Studio](machine-learning-create-experiment.md)
- [Entwickeln einer Vorhersagelösung mit Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)
- [Importieren von Trainingsdaten in Azure Machine Learning Studio](machine-learning-import-data.md)
- [Verwalten von Experimentiterationen in Azure Machine Learning-Studio](machine-learning-manage-experiment-iterations.md)

## Konvertieren des Trainingsexperiments in ein Vorhersageexperiment

Nachdem Sie das Modell trainiert haben, können Sie es verwenden, um neue Daten zu bewerten. Hierzu konvertieren Sie das Trainingsexperiment in ein Vorhersageexperiment. Durch die Konvertierung in ein Vorhersageexperiment wird das trainierte Modell darauf vorbereitet, als Bewertungswebdienst bereitgestellt zu werden. Benutzer des Webdiensts senden Eingabedaten an das Modell, und das Modell sendet Vorhersageergebnisse zurück. Während der Konvertierung in ein Vorhersageexperiment sollten Sie daher berücksichtigen, wie das Modell aus Ihrer Sicht erwartungsgemäß von anderen Benutzern verwendet wird.

Zum Konvertieren Ihres Trainingsexperiments in ein Vorhersageexperiment klicken Sie im Experimentbereich unten auf **Ausführen** und dann auf **Webdienst einrichten**. Wählen Sie dann **Vorhersagewebdienst** aus.

![Konvertieren in ein Bewertungsexperiment](./media/machine-learning-publish-a-machine-learning-web-service/figure-1.png)

Weitere Informationen zum Ausführen dieser Konvertierung finden Sie unter [Konvertieren eines Machine Learning-Trainingsexperiments in ein Vorhersageexperiment](machine-learning-convert-training-experiment-to-scoring-experiment.md).


## Bereitstellung des Vorhersageexperiments als Webdienst

Nachdem das Vorhersageexperiment ausreichend vorbereitet wurde, können Sie es als Azure-Webdienst bereitstellen. Mithilfe des Webdiensts können Benutzer Daten an das Modell senden, und das Modell gibt seine Vorhersagen zurück.

Um das Vorhersageexperiment bereitzustellen, klicken Sie unten im Experimentbereich auf **Ausführen** und anschließend auf **Webdienst bereitstellen**. Der Webdienst wird eingerichtet, und Sie werden zum Webdienst-Dashboard weitergeleitet.

![Bereitstellen des Webdiensts](./media/machine-learning-publish-a-machine-learning-web-service/figure-2.png)

Klicken Sie zum Testen des Webdiensts im Webdienst-Dashboard auf den Link **Testen**. Ein Dialogfeld wird geöffnet, in dem nach den Eingabedaten für den Dienst gefragt werden. Hierbei handelt es sich um die Spalten, die vom Bewertungsexperiment erwartet werden. Geben Sie eine Reihe von Daten ein, und klicken Sie auf **OK**. Die vom Webdienst generierten Ergebnisse werden jetzt unten im Dashboard angezeigt.

![Testen des Webdiensts](./media/machine-learning-publish-a-machine-learning-web-service/figure-3.png)

Auf der Registerkarte **CONFIGURATION** (KONFIGURATION) können Sie den Anzeigenamen des Dienstes ändern und eine Beschreibung dafür eingeben. Der Namen und die Beschreibung werden im Azure-Verwaltungsportal angezeigt, wo Sie Ihre Webdienste verwalten. Sie können eine Beschreibung für die Eingabedaten, Ausgabedaten und Webdienstparameter bereitstellen, indem Sie Zeichenfolgen für jede Spalte unter **EINGABESCHEMA**, **AUSGABESCHEMA** und **WEBDIENSTPARAMETER** eingeben. Diese Beschreibungen werden in der Beispielcodedokumentation für den Webdienst verwendet. Sie können auch die Protokollierung aktivieren, um Fehler zu diagnostizieren, die angezeigt werden, wenn Sie auf den Webdienst zugreifen.

Weitere Informationen hierzu finden Sie unter [Aktivieren der Protokollierung für Machine Learning-Webdienste](machine-learning-web-services-logging.md).

![Konfigurieren des Webdiensts](./media/machine-learning-publish-a-machine-learning-web-service/figure-4.png)


## Zugreifen auf den Webdienst

Nachdem Sie den Webdienst in Machine Learning Studio bereitgestellt haben, können Sie Daten an den Dienst senden und programmgesteuert Antworten erhalten.

Das Dashboard bietet alle Informationen, die Sie benötigen, um auf den Webdienst zuzugreifen. Zum Beispiel wird der API-Schlüssel bereitgestellt, um den autorisierten Zugriff auf den Dienst zu ermöglichen, und API-Hilfeseiten werden bereitgestellt, um das Schreiben von Code zu erleichtern.

Weitere Informationen zum Zugreifen auf einen Machine Learning-Webdienst finden Sie unter [Nutzen eines bereitgestellten Azure Machine Learning-Webdiensts](machine-learning-consume-web-services.md).


## Verwalten des Webdiensts im Azure-Verwaltungsportal

Im Azure-Verwaltungsportal können Sie Ihre Webdienste verwalten, indem Sie auf den Dienst **Machine Learning** klicken, den Machine Learning-Arbeitsbereich öffnen und anschließend auf der Registerkarte **Webdienste** den Webdienst öffnen. Auf dieser Seite aus können Sie den Webdienst überwachen, aktualisieren und löschen. Sie können auch zusätzlich zu dem Standardendpunkt, der beim Bereitstellen erstellt wird, einen zweiten Endpunkt für den Webdienst hinzufügen.

Weitere Informationen finden Sie unter [Verwalten eines Machine Learning-Arbeitsbereichs](machine-learning-manage-workspace.md).<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->


## Aktualisieren des Webdiensts

Sie können Änderungen am Webdienst vornehmen, z. B. das Modell mit zusätzlichen Trainingsdaten aktualisieren, und ihn dann erneut bereitstellen, wobei der ursprüngliche Webdienst überschrieben wird.

Um den Webdienst zu aktualisieren, öffnen Sie das ursprüngliche Vorhersageexperiment, das Sie zum Bereitstellen des Webdiensts verwendet haben, und klicken Sie auf **SPEICHERN UNTER**, um eine bearbeitbare Kopie zu erstellen. Nehmen Sie die gewünschten Änderungen vor, und klicken Sie dann auf **Webdienst bereitstellen**. Da Sie dieses Experiment zuvor bereits bereitgestellt hatten, werden Sie von Machine Learning Studio gefragt, ob Sie den vorhandenen Dienst überschreiben möchten. Wenn Sie auf **JA** klicken, wird der vorhandenen Webdienst beendet, und das neue Vorhersageexperiment wird an seiner Stelle veröffentlicht.

> [AZURE.NOTE]Wenn Sie Konfigurationsänderungen im ursprünglichen Webdienst vorgenommen haben, z. B. einen anderen Anzeigenamen oder eine andere Beschreibung eingegeben haben, dann müssen Sie diese Werte erneut eingeben.

Eine Option zum Aktualisieren des Webdiensts ist das programmgesteuerte erneute Trainieren des Modells. Weitere Informationen finden Sie unter [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](machine-learning-retrain-models-programmatically.md).

<!---HONumber=Nov15_HO2-->