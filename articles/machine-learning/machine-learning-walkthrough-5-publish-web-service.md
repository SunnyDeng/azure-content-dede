<properties
	pageTitle="Schritt 5: Bereitstellen des Machine Learning-Webdiensts | Microsoft Azure"
	description="Exemplarische Vorgehensweise zum Entwickeln einer Vorhersagelösung – Schritt 5: Bereitstellen eines Vorhersageexperiments als Webdienst in Machine Learning Studio."
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
	ms.date="09/08/2015"
	ms.author="garye"/>


# Anleitung Schritt 5: Bereitstellen des Azure Machine Learning-Webdiensts

Dies ist der fünfte Schritt der exemplarischen Vorgehensweise zum [Entwickeln einer Vorhersagelösung mit Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Trainieren und Bewerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Bereitstellen des Webdiensts**
6.	[Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)

----------

Damit das Vorhersagemodell für Dritte von Nutzen sein kann, wird es als Webdienst in Azure bereitgestellt.

Bis jetzt haben Sie mit dem Trainieren des Modells experimentiert. Der bereitgestellte Dienst führt jedoch kein Training mehr durch – er generiert Vorhersagen auf Grundlage der Eingabe des Benutzers. Nun werden wir einige vorbereitende Aufgaben ausführen und dieses Experiment dann als funktionierenden Webdienst bereitstellen, auf den Benutzer zugreifen können. Ein Benutzer kann einen Satz Kreditantragsdaten an den Dienst senden, und der Dienst gibt die Vorhersage des Kreditrisikos zurück.

Gehen Sie hierzu wie folgt vor:

- Konvertieren des *Trainingsexperiments* in ein *Vorhersageexperiment*
- Bereitstellung des Vorhersageexperiments als Webdienst

Zunächst muss das Experiment optimiert werden. Zurzeit enthält das Experiment zwei verschiedene Modelle, nun müssen Sie ein Modell auswählen, das bereitgestellt werden soll.

In unserem Beispiel entscheiden wir, dass das Boosted Tree-Modell sich besser zur Verwendung eignet. Als Erstes müssen Sie nun das Modul [Two-Class Support Vector Machine][two-class-support-vector-machine] sowie die Module entfernen, mit denen dieses Modul trainiert wurde. Am besten erstellen Sie zunächst eine Kopie des Experiments, indem Sie unten in der Canvas des Experiments auf **Save As** klicken.

Folgende Module müssen gelöscht werden:

1.	[Two-Class Support Vector Machine][two-class-support-vector-machine]
2.	Die damit verbundenen Module [Train Model][train-model] und [Score Model][score-model]
3.	[Normalize Data][normalize-data] (beide)
4.	[Evaluate Model][evaluate-model]

Jetzt ist das Modell bereit für die Bereitstellung.

## Konvertieren des Trainingsexperiments in ein Vorhersageexperiment

Die Konvertierung in ein Vorhersageexperiment umfasst drei Schritte:

1. Speichern des trainierten Modells und Ersetzen der Trainingsmodule durch das Modell
2. Optimieren des Experiments, um Module zu entfernen, die nur zu Trainingszwecken benötigt wurden
3. Definieren, wo sich Eingabe- und Ausgabeknoten des Webdiensts befinden sollen

Glücklicherweise können alle drei Schritte ausgeführt werden, indem Sie einfach unten im Experimentbereich auf **Webdienst bereitstellen** klicken (wählen Sie die Option **Vorhersagewebdienst**).

Beim Klicken auf **Webdienst bereitstellen** geschehen mehrere Dinge:

- Das trainierte Modell wird als **Trained Model**-Modul in der Modulpalette gespeichert, die sich links neben dem Experimentbereich befindet. (Sie finden die Palette unter **Trained Models**.)
- Module, die zum Training verwendet wurden, werden entfernt. Dies gilt insbesondere in folgenden Fällen:
  - [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree]
  - [Train Model][train-model]
  - [Split][split]
  - Das zweite [Execute R Script][execute-r-script]-Modul, das für die Testdaten verwendet wurde
- Das gespeicherte trainierte Modell wird zum Experiment hinzugefügt.
- Die **Webdiensteingabe**- und **Webdienstausgabe**-Module werden hinzugefügt.

> [AZURE.NOTE]Das Experiment wurde in zwei Teilen gespeichert: das ursprüngliche Trainingsexperiment und das neue Vorhersageexperiment. Sie können auf beide Teile über die Registerkarten oberhalb der Experimentcanvas zugreifen.

Für das vorliegende Experiment muss nun noch ein zusätzlicher Schritt ausgeführt werden. Machine Learning Studio hat beim Entfernen des [Split][split]-Moduls eines der [Execute R Script][execute-r-script]-Module entfernt, das andere [Execute R Script][execute-r-script]-Modul jedoch nicht. Da dieses Modul nur zum Trainieren und Testen verwendet wurde (es hat eine Gewichtungsfunktion für die Stichprobendaten bereitgestellt), können Sie es nun entfernen und den [Metadata Editor][metadata-editor] mit dem Modul [Score Model][score-model] verbinden.

Unser Experiment sollte nun wie folgt aussehen:

![Bewerten des trainierten Modells][4]


Sie fragen sich vielleicht, warum der Datensatz „UCI German Credit Card Data“ im Vorhersageexperiment verblieben ist. Der Dienst verwendet die Daten des Benutzers, nicht das Originaldataset. Weshalb also die Verbindung belassen?

Es stimmt zwar, dass der Dienst die Originalkreditkartendaten nicht benötigt. Er benötigt aber das Schema für diese Daten, darunter Angaben zur Anzahl der vorhandenen Spalten, und welche Spalten numerisch sind. Diese Schemainformationen sind erforderlich, um die Benutzerdaten zu interpretieren. Wir lassen diese Komponenten verbunden, damit das Bewertungsmodul über das Datasetschema verfügt, wenn der Dienst ausgeführt wird. Es werden nicht die Daten verwendet, sondern nur das Schema.

Führen Sie das Experiment ein letztes Mal aus (klicken Sie auf **Ausführen**). Wenn Sie überprüfen möchten, ob das Modell noch funktioniert, klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls [Score Model][score-model] und wählen **Ergebnisse anzeigen**. Sie sehen, dass die Originaldaten zusammen mit dem Wert für das Kreditrisiko („Bewertete Beschriftungen“)' und dem Bewertungswahrscheinlichkeitswert („Bewertete Wahrscheinlichkeiten“) angezeigt werden.

##
Bereitstellen des Webdiensts

Zum Bereitstellen eines aus dem Experiment abgeleiteten Webdiensts klicken Sie unter dem Bereich auf **Webdienst bereitstellen**. Machine Learning Studio stellt das Experiment als Webdienst bereit und führt Sie zum Dienst-Dashboard.

> [AZURE.TIP]Sie können den Webdienst nach der Bereitstellung aktualisieren. Wenn Sie das Modell ändern möchten, bearbeiten Sie einfach die Modellparameter im Trainingsexperiment und klicken auf **Webdienst bereitstellen**. Wenn Sie das Experiment erneut bereitstellen, wird der Webdienst ersetzt und das aktualisierte Modell verwendet.

Sie können den Dienst konfigurieren, indem Sie auf die Registerkarte **KONFIGURATION** klicken. Hier können Sie den Dienstnamen ändern (er erhält standardmäßig den Namen des Experiments) und eine Beschreibung hinzufügen. Sie können auch benutzerfreundlichere Beschriftungen für die Eingabe- und Ausgabespalten festlegen.

## Testen des Webdiensts
Klicken Sie auf der Seite **DASHBOARD** auf den Link **Test** unter **Standardendpunkt**. Ein Dialogfeld wird geöffnet, in dem Sie nach den Eingabedaten für den Dienst gefragt werden. Dies sind die gleichen Spalten, die im Originaldataset „German Credit Risk“ angezeigt wurden.

Geben Sie eine Reihe von Daten ein, und klicken Sie auf **OK**.

Die vom Webdienst generierten Ergebnisse werden jetzt unten im Dashboard angezeigt. So, wie der Dienst konfiguriert wurde, werden die angezeigten Ergebnisse vom Bewertungsmodul generiert.


----------

**Nächster Schritt: [Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png


<!-- Module References -->
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[metadata-editor]: https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66/
[normalize-data]: https://msdn.microsoft.com/library/azure/986df333-6748-4b85-923d-871df70d6aaf/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[two-class-boosted-decision-tree]: https://msdn.microsoft.com/library/azure/e3c522f8-53d9-4829-8ea4-5c6a6b75330c/
[two-class-support-vector-machine]: https://msdn.microsoft.com/library/azure/12d8479b-74b4-4e67-b8de-d32867380e20/

<!----HONumber=Sept15_HO2-->