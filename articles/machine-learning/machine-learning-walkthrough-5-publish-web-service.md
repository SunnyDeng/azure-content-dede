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
	ms.date="02/03/2016"
	ms.author="garye"/>


# Anleitung Schritt 5: Bereitstellen des Azure Machine Learning-Webdiensts

Dies ist der fünfte Schritt der exemplarischen Vorgehensweise zum [Predictive Analytics-Lösung in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).


1.	[Erstellen eines Machine Learning-Arbeitsbereichs](machine-learning-walkthrough-1-create-ml-workspace.md)
2.	[Hochladen vorhandener Daten](machine-learning-walkthrough-2-upload-data.md)
3.	[Erstellen eines neuen Experiments](machine-learning-walkthrough-3-create-new-experiment.md)
4.	[Trainieren und Bewerten der Modelle](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5.	**Bereitstellen des Webdiensts**
6.	[Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)

----------

Um anderen die Möglichkeit zu geben, das Vorhersagemodell zu verwenden, das wir in dieser exemplarischen Vorgehensweise entwickelt haben, stellen wir es als Webdienst in Azure bereit.

Bis jetzt haben Sie mit dem Trainieren des Modells experimentiert. Der bereitgestellte Dienst führt jedoch kein Training mehr durch, sondern generiert Vorhersagen durch Bewerten der Benutzereingaben basierend auf unserem Modell. Wir führen einige vorbereitende Schritte durch, um dieses Experiment von einem ***Trainingsexperiment*** in ein ***Vorhersageexperiment*** umzuwandeln.

Dies ist ein zweistufiger Prozess:

1. Konvertieren des *Trainingsexperiments* in ein *Vorhersageexperiment*
2. Bereitstellung des Vorhersageexperiments als Webdienst

Zunächst muss das Experiment optimiert werden. Zurzeit enthält das Experiment zwei verschiedene Modelle, doch nur eines davon wollen wir als Webdienst bereitstellen.

In unserem Beispiel entscheiden wir, dass das Boosted Tree-Modell sich besser zur Verwendung eignet. Als Erstes müssen Sie nun das Modul [Two-Class Support Vector Machine][two-class-support-vector-machine] sowie die Module entfernen, mit denen dieses Modul trainiert wurde. Am besten erstellen Sie zunächst eine Kopie des Experiments, indem Sie unten in der Canvas des Experiments auf **Save As** klicken.

Folgende Module müssen gelöscht werden:

- [Two-Class Support Vector Machine][two-class-support-vector-machine]
- Die damit verbundenen Module [Train Model][train-model] und [Score Model][score-model]
- [Normalize Data][normalize-data] (beide)
- [Evaluate Model][evaluate-model]

Wählen Sie das Modul aus, und drücken Sie die ENTF-Taste, oder klicken Sie mit der rechten Maustaste auf das Modul, und wählen Sie **Delete** aus.

Jetzt sind wir bereit, dieses Modells mit dem [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] bereitzustellen.

## Konvertieren des Trainingsexperiments in ein Vorhersageexperiment

Die Konvertierung in ein Vorhersageexperiment umfasst drei Schritte:

1. Speichern des trainierten Modells und Ersetzen der Trainingsmodule
2. Optimieren des Experiments, um Module zu entfernen, die nur zu Trainingszwecken benötigt wurden
3. Festlegen, wo der Webdienst Eingaben akzeptiert und wo er Ausgaben generiert

Glücklicherweise können alle drei Schritte ausgeführt werden, indem Sie einfach unten im Experimentbereich auf **Webdienst bereitstellen** klicken (wählen Sie die Option **Vorhersagewebdienst**).

Beim Klicken auf **Webdienst bereitstellen** geschehen mehrere Dinge:

- Das trainierte Modell wird als Modul **Trained Model** in der Modulpalette gespeichert, die sich links neben dem Experimentbereich befindet. (Sie finden sie unter **Trained Models**.)
- Module, die zum Training verwendet wurden, werden entfernt. Dies gilt insbesondere in folgenden Fällen:
  - [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree]
  - [Train Model][train-model]
  - [Split Data][split]
  - Das zweite [Execute R Script][execute-r-script]-Modul, das für die Testdaten verwendet wurde
- Das gespeicherte trainierte Modell wird wieder dem Experiment hinzugefügt.
- Die **Webdiensteingabe**- und **Webdienstausgabe**-Module werden hinzugefügt.

> [AZURE.NOTE] Das Experiment wurde in zwei Teilen unter Registerkarten gespeichert, die oben im Experimentbereich hinzugefügt wurden: das ursprüngliche Trainingsexperiment befindet sich unter der Registerkarte **Trainingsexperiment**, und das neu erstellte Vorhersageexperiment finden Sie unter **Vorhersageexperiment**.

Bei diesem bestimmten Experiment ist ein zusätzlicher Schritt erforderlich. Wir haben zwei [Execute R Script][execute-r-script]-Module hinzugefügt, um die Daten für Training und Tests mit einer Gewichtungsfunktion zu versehen. Im endgültigen Modell ist dies nicht erforderlich. Machine Learning Studio hat ein [Execute R Script][execute-r-script]-Modul beim Entfernen des Moduls [Split Data][split] entfernt. Deshalb können wir jetzt das andere entfernen und [Metadata Editor][metadata-editor] direkt mit dem Modul [Score Model][score-model] verbinden.

Unser Experiment sollte nun wie folgt aussehen:

![Bewerten des trainierten Modells][4]


> [AZURE.NOTE] Sie fragen sich vielleicht, warum der Datensatz „UCI German Credit Card Data“ im Vorhersageexperiment verblieben ist. Der Dienst verwendet die Daten des Benutzers, nicht das Originaldataset. Weshalb also das Originaldataset im Modell belassen?
>
>Es stimmt zwar, dass der Dienst die Originalkreditkartendaten nicht benötigt. Er benötigt aber das Schema für diese Daten, darunter Angaben zur Anzahl der vorhandenen Spalten, und welche Spalten numerisch sind. Diese Schemainformationen sind erforderlich, um die Benutzerdaten zu interpretieren. Wir lassen diese Komponenten verbunden, damit das Bewertungsmodul über das Datasetschema verfügt, wenn der Dienst ausgeführt wird. Es werden nicht die Daten verwendet, sondern nur das Schema.

Führen Sie das Experiment ein letztes Mal aus (klicken Sie auf **Ausführen**). Wenn Sie überprüfen möchten, ob das Modell noch funktioniert, klicken Sie mit der rechten Maustaste auf die Ausgabe des Moduls [Score Model][score-model] und wählen **Ergebnisse anzeigen**. Sie sehen, dass die Originaldaten zusammen mit dem Wert für das Kreditrisiko („Bewertete Beschriftungen“)' und dem Bewertungswahrscheinlichkeitswert („Bewertete Wahrscheinlichkeiten“) angezeigt werden.

## Bereitstellen des Webdiensts

Zum Bereitstellen eines aus dem Experiment abgeleiteten Webdiensts klicken Sie unter dem Bereich auf **Webdienst bereitstellen**. Machine Learning Studio stellt das Experiment als Webdienst bereit und führt Sie zum Dashboard dieses Webdiensts. Von hier aus können Sie zum Experiment zurückkehren (**View snapshot** oder **View latest**) und einen einfachen Test des Webdiensts ausführen (Schaltfläche **Test** – siehe **Testen des Webdiensts** weiter unten). Dort befinden sich auch Informationen zum Erstellen von Anwendungen, die auf den Webdienst zugreifen können (mehr dazu im nächsten Schritt dieser exemplarischen Vorgehensweise).

![Dashboard des Webdiensts][6]

> [AZURE.TIP] Sie können den Webdienst nach der Bereitstellung aktualisieren. Wenn Sie das Modell ändern möchten, bearbeiten Sie einfach die Modellparameter im Trainingsexperiment und klicken auf **Webdienst bereitstellen**. Wenn Sie das Experiment erneut bereitstellen, wird der Webdienst ersetzt und das aktualisierte Modell verwendet.

Sie können den Dienst konfigurieren, indem Sie auf die Registerkarte **KONFIGURATION** klicken. Hier können Sie den Dienstnamen ändern (er erhält standardmäßig den Namen des Experiments) und eine Beschreibung hinzufügen. Sie können auch benutzerfreundlichere Beschriftungen für die Eingabe- und Ausgabespalten festlegen.

![Konfigurieren des Webdiensts][5]

## Testen des Webdiensts
Klicken Sie auf der Seite **DASHBOARD** unter **Default Endpoint** auf die Schaltfläche **Test**. Ein Dialogfeld wird geöffnet, in dem Sie nach den Eingabedaten für den Dienst gefragt werden. Dies sind die gleichen Spalten, die im Originaldataset „German Credit Risk“ angezeigt wurden.

Geben Sie eine Reihe von Daten ein, und klicken Sie auf **OK**.

Im Webdienst durchlaufen die Daten das Modul **Web Service Input**, dann das Modul [Metadata Editor][metadata-editor] und schließlich das Modul [Score Model][score-model], in dem sie bewertet werden. Die Ergebnisse werden danach vom Webdienst über das Modul **Web service output** ausgegeben.

> [AZURE.TIP] So wie wir das Vorhersageexperiment konfiguriert haben, gibt das Modul [Score Model][score-model] sämtliche Ergebnisse zurück. Dies schließt alle Eingabedaten sowie den Kreditrisikowert und die Bewertungswahrscheinlichkeit ein. Wenn Sie etwas anderes zurückgeben möchten, z. B. nur den Kreditrisikowert, können Sie ein Modul des Typs [Project Columns][project-columns] zwischen [Score Model][score-model] und **Web service output** einfügen, um Spalten auszuschließen, die der Webdienst nicht zurückgeben soll.

## Verwalten des Webdiensts
Nachdem Sie den Webdienst bereitgestellt haben, können Sie ihn im [klassischen Azure-Portal](https://manage.windowsazure.com) verwalten.

1. Melden Sie sich beim [klassischen Azure-Portal](https://manage.windowsazure.com) an.
2. Klicken Sie im Microsoft Azure-Dienstbereich auf **MACHINE LEARNING**.
3. Klicken Sie auf Ihren Arbeitsbereich.
4. Klicken Sie auf die Registerkarte **WEBDIENSTE**.
5. Klicken Sie auf den Webdienst, den wir gerade erstellt haben.
6. Klicken Sie auf den Endpunkt „Default“.

Hier können Sie überwachen, wie der Webdienst funktioniert, und Leistungsanpassungen vornehmen, indem Sie ändern, wie viele gleichzeitige Aufrufe der Dienst unterstützen kann. Sie können sogar Ihren Webdienst in Azure Marketplace veröffentlichen.

Weitere Informationen finden Sie unter:

- [Erstellen von Endpunkten](machine-learning-create-endpoint.md)
- [Skalieren von Webdiensten](machine-learning-scaling-webservice.md)
- [Veröffentlichen Ihres Azure Machine Learning-Webdiensts im Azure Marketplace](machine-learning-publish-web-service-to-azure-marketplace.md)

----------

**Nächster Schritt: [Zugreifen auf den Webdienst](machine-learning-walkthrough-6-access-web-service.md)**

[1]: ./media/machine-learning-walkthrough-5-publish-web-service/publish1.png
[2]: ./media/machine-learning-walkthrough-5-publish-web-service/publish2.png
[3]: ./media/machine-learning-walkthrough-5-publish-web-service/publish3.png
[4]: ./media/machine-learning-walkthrough-5-publish-web-service/publish4.png
[5]: ./media/machine-learning-walkthrough-5-publish-web-service/publish5.png
[6]: ./media/machine-learning-walkthrough-5-publish-web-service/publish6.png


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
[project-columns]: https://msdn.microsoft.com/de-DE/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/

<!---HONumber=AcomDC_0211_2016-->