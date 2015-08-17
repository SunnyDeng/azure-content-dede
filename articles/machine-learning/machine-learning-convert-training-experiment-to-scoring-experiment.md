<properties 
	pageTitle="Konvertieren eines Machine Learning-Trainingsexperiments in ein Bewertungsexperiment | Microsoft Azure" 
	description="Konvertieren eines Machine Learning-Trainingsexperiments zum Trainieren Ihres Modells für Vorhersageanalysen in ein Bewertungsexperiment, das als Webdienst veröffentlicht werden kann." 
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
	ms.date="07/10/2015" 
	ms.author="garye"/>

#Konvertieren eines Machine Learning-Trainingsexperiments in ein Bewertungsexperiment

Azure Machine Learning ermöglicht Ihnen, Vorhersageanalyselösungen zu erstellen, zu testen und bereitzustellen.

Sobald Sie ein *Trainingsexperiment* zum Trainieren Ihres Vorhersageanalysemodells erstellt und durchlaufen haben und bereit sind, dieses zum Bewerten neuer Daten zu verwenden, müssen Sie das Experiment vorbereiten und für die Bewertung optimieren. Sie können dann das *Bewertungsexperiment* als Azure-Webdienst veröffentlichen, damit Benutzer Daten an Ihr Modell senden und dessen Vorhersagen abrufen können.

Durch Konvertieren in ein Bewertungssystem wird das trainierte Modell darauf vorbereitet, als Bewertungswebdienst veröffentlicht zu werden. Benutzer des Webdiensts senden Eingabedaten an das Modell, und das Modell sendet Vorhersageergebnisse zurück. Wenn Sie es in ein Bewertungsexperiment konvertieren, sollten Sie also berücksichtigen, wie das Modell von anderen Benutzern verwendet werden soll.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

Der Prozess der Konvertierung eines Trainingsexperiments in ein Bewertungsexperiment umfasst drei Schritte:

1.	Speichern Sie das Machine Learning-Modell, das Sie trainiert haben, und ersetzen Sie dann den Machine Learning-Algorithmus und die [Train Model][train-model]-Module durch Ihr gespeichertes trainiertes Modell.
2.	Reduzieren des Experiments auf die Module, die für die Bewertung erforderlich sind. Ein Trainingsexperiment enthält mehrere Module, die für das Training erforderlich sind, jedoch nicht mehr benötigt werden, nachdem das Modell entsprechend trainiert wurde und bereit für die Bewertung ist.
3.	Definieren Sie, wo Sie im Experiment Daten des Webdienst-Benutzers akzeptieren und welche Daten zurückgegeben werden.

##Schaltfläche "Create Scoring Experiment"

Die Schaltfläche **Create Scoring Experiment** am unteren Rand des Experimentbereichs führt die drei Schritte der Konvertierung Ihres Trainingsexperiments in ein Bewertungsexperiment für Sie durch:

1.	Dabei wird das trainierte Modell als Modul im Abschnitt **Trained Models** der Modulpalette (links neben dem Experimentbereich) gespeichert, dann werden der Machine Learning-Algorithmus und die [Train Model][train-model]-Module durch das gespeicherte Modell ersetzt. 
2.	Module, die offensichtlich nicht erforderlich sind, werden entfernt. In unserem Beispiel gehören dazu die Module [Split][split], zweites [Score Model][score-model] und [Evaluate Model][evaluate-model].
3.	Webdiensteingabe- und -ausgabemodule werden erstellt und in den Standardpositionen Ihres Experiments eingefügt.

Das folgende Experiment trainiert z. B. ein Boosted Decision Tree-Zwei-Klassen-Modell mit Beispiel-Erhebungsdaten:

![Trainingsexperiment][figure1]

Die Module in diesem Experiment führen im Wesentlichen vier verschiedene Funktionen aus:

![Modulfunktionen][figure2]

Wenn Sie dieses Trainingsexperiment in ein Bewertungsexperiment konvertieren, werden einige dieser Module nicht mehr benötigt oder erhalten einen anderen Zweck:

- **Data** - Die Daten in diesem Beispiel-DataSet werden nicht während der Bewertung verwendet – der Benutzer des Webdiensts stellt die Daten für die Bewertung bereit. Die Metadaten aus dem DataSet, z. B. Datentypen, werden jedoch vom trainierten Modell verwendet. Daher müssen Sie das DataSet im Bewertungsexperiment beibehalten, damit diese Metadaten bereitgestellt werden können.

- **Prep** – Abhängig von den zum Bewerten übermittelten Daten können diese Module zum Verarbeiten der eingehenden Daten erforderlich sein oder auch nicht.

	In diesem Beispiel können etwa im Beispiel-DataSet Werte fehlen und Spalten enthalten sein, die zum Trainieren des Modells nicht benötigt werden. Das Modul [Clean Missing Data][clean-missing-data] wurde für den Umgang mit fehlenden Werten eingeschlossen, und das Modul [Project Columns][project-columns] wurde eingeschlossen, um diese zusätzlichen Spalten aus dem Datenfluss auszuschließen. Wenn Sie wissen, dass in den zum Bewerten durch den Webdienst übermittelten Daten keine Werte fehlen, können Sie das Modul [Clean Missing Data][clean-missing-data] entfernen. Da das Modul [Project Columns][project-columns] beim Festlegen der bewerteten Funktionen hilft, muss dieses Modul beibehalten werden.

- **Train** – Sobald das Modell erfolgreich trainiert wurde, speichern Sie es als einzelnes Modul des trainierten Modells. Anschließend ersetzen Sie diese einzelnen Module durch das gespeicherte trainierte Modell.

- **Score** – In diesem Beispiel wird das Modul "Split" zum Unterteilen des Datenstroms in einen Satz von Testdaten und Trainingsdaten verwendet. Im Bewertungsexperiment wird dies nicht benötigt und kann entfernt werden. Ebenso werden das zweite Modul [Score Model][score-model] und das Modul [Evaluate Model][evaluate-model] verwendet, um Ergebnisse aus den Testdaten zu vergleichen, daher werden auch diese Module im Bewertungsexperiment nicht benötigt. Das verbleibende Modul [Score Model][score-model] ist jedoch erforderlich, um über den Webdienst das Bewertungsergebnis zurückzugeben.

So sieht das Beispiel aus, nachdem auf **Create Scoring Experiment** geklickt wurde:

![Konvertiertes Bewertungsexperiment][figure3]

Das kann ausreichend sein, um das Experiment zur Veröffentlichung als Webdienst vorzubereiten. Allerdings sollten Sie einige zusätzliche Aufgaben für das Experiment durchführen.

###Anpassen von Eingabe- und Ausgabemodulen

Im Trainingsexperiment haben Sie einen Satz von Trainingsdaten verwendet und dann durch Verarbeitung die Daten in einem Formular abgerufen, die für den Algorithmus für maschinelles Lernen erforderlich sind. Wenn für die voraussichtlichen über den Webdienst empfangenen Daten keine Verarbeitung erforderlich ist, können Sie das **Modul Web Service Input** zu einem anderen Knoten im Experiment verschieben.

Beispielsweise setzt **Create Scoring Experiment** standardmäßig das Modul **Web Service Input** an den Beginn des Datenflusses, wie in der Abbildung oben dargestellt. Wenn diese Verarbeitung für die Eingabedaten nicht erforderlich ist, können Sie **Web Service Input** manuell hinter den Datenverarbeitungsmodulen platzieren:

![Verschieben des Web Service Input][figure4]

Die über den Webdienst bereitgestellten Eingabedaten werden jetzt ohne Vorverarbeitung direkt an das "Score Model"-Modul übergeben.

Auf ähnliche Weise setzt **Create Scoring Experiment** standardmäßig das Modul "Web Service Output" an das Ende des Datenflusses. In diesem Beispiel gibt der Webdienst die Ausgabe des [Score Model][score-model]-Moduls an den Benutzer zurück, die den vollständigen Eingabedatenvektor sowie die Bewertungsergebnisse enthält.

Wenn Sie jedoch etwas anderes zurückgeben möchten – z. B. nur die Bewertungsergebnisse und nicht den gesamten Eingabedatenvektor – können Sie ein [Project Columns][project-columns]-Modul einfügen, in dem alle Spalten mit Ausnahme der Bewertungsergebnisse ausgeschlossen werden. Anschließend verschieben Sie das **Web Service Output**-Modul zur Ausgabe des [Project Columns][project-columns]-Moduls:

![Verschieben des Web Service Output][figure5]

###Hinzufügen oder Entfernen zusätzlicher Datenverarbeitungsmodule

Wenn in Ihrem Experiment mehrere Module vorhanden sind, von denen Sie wissen, dass sie für die Bewertung nicht benötigt werden, können diese entfernt werden. Da wir beispielsweise das Modul **Web Service Input** an einen Punkt hinter den Datenverarbeitungsmodulen verschoben haben, können wir das Modul [Clean Missing Data][clean-missing-data] aus dem Bewertungsexperiment entfernen.

Unser Bewertungsexperiment sieht nun folgendermaßen aus:

![Entfernen zusätzlicher Module][figure6]

###Hinzufügen von optionalen Webdienst-Parametern

In einigen Fällen empfiehlt es sich, dem Benutzer des Webdiensts das Ändern des Modulverhaltens zu erlauben, wenn auf den Dienst zugegriffen wird. *Webdienstparameter* erlauben Ihnen, dies zu tun.

Ein typisches Beispiel ist das Einrichten des [Reader][reader]-Moduls, damit die Benutzer des veröffentlichten Webdiensts beim Zugreifen auf den Webdienst eine andere Datenquelle angeben können. Ein weiteres Beispiel ist die Konfiguration des [Writer][writer]-Moduls, damit ein anderes Ziel angegeben werden kann.

Sie können Web Service-Parameter definieren und einem oder mehreren Modulparametern zuordnen, und Sie können angeben, ob diese erforderlich oder optional sind. Der Benutzer des Webdiensts kann dann beim Zugreifen auf den Dienst Werte für diese Parameter angeben – die Aktionen des Moduls werden dann entsprechend angepasst.

Weitere Informationen über Webdienst-Parameter finden Sie unter [Verwenden von Azure Machine Learning Webdienst-Parametern][webserviceparameters]

[webserviceparameters]: machine-learning-web-service-parameters.md


##Veröffentlichen des Bewertungsexperiments als Webdienst

Nachdem das Bewertungsexperiment ausreichend vorbereitet wurde, können Sie es als Azure-Webdienst veröffentlichen. Mithilfe des Webdiensts können Benutzer Daten an das Modell senden, und das Modell gibt seine Vorhersagen zurück.

Weitere Informationen zum vollständigen Veröffentlichungsprozess finden Sie unter [Veröffentlichen des Azure Machine Learning-Webdiensts][publish]

[publish]: machine-learning-publish-a-machine-learning-web-service.md


<!-- Images -->
[figure1]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure1.png
[figure2]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure2.png
[figure3]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure3.png
[figure4]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure4.png
[figure5]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure5.png
[figure6]: ./media/machine-learning-convert-training-experiment-to-scoring-experiment/figure6.png


<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[project-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=August15_HO6-->