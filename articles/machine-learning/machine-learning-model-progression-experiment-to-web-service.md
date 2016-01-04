<properties
	pageTitle="So entwickelt sich ein Machine Learning-Modell von einem Experiment zu einem betriebsbereiten Webdienst | Microsoft Azure"
	description="Ein Überblick darüber, wie Ihr Azure Machine Learning-Modell sich von einem Entwicklungsexperiment zu einem betriebsbereiten Webdienst entwickelt."
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
	ms.date="11/10/2015"
	ms.author="garye"/>


# So entwickelt sich ein Machine Learning-Modell von einem Experiment zu einem betriebsbereiten Webdienst

Ein ***Experiment*** ist ein Arbeitsbereich in Azure Machine Learning Studio, in dem Sie interaktiv entwickeln, ausführen, testen und Iterationen durchlaufen können, während Sie ein Vorhersageanalysemodell erstellen. Eine Vielzahl von Modulen stehen zur Verfügung, mit denen Sie Daten in Ihr Experiment einbeziehen, die Daten bearbeiten, ein Modell mithilfe von Machine Learning-Algorithmen trainieren, das Modell bewerten, die Ergebnisse auswerten und endgültige Werte ausgeben können.

Sobald Sie mit dem Experiment zufrieden sind, können Sie es als ***Azure-Webdienst*** bereitstellen, damit Benutzer neue Daten senden und Ergebnisse zurückerhalten können.

In diesem Artikel geben wir einen Überblick darüber, wie Ihr Machine Learning-Modell sich von einem Entwicklungsexperiment zu einem betriebsbereiten Webdienst entwickelt.

>[AZURE.NOTE]Es gibt noch weitere Methoden zum Entwickeln und Bereitstellen von Machine Learning-Modellen, aber dieser Artikel beschränkt sich auf die Verwendung von Machine Learning Studio. Eine Erläuterung zum Erstellen eines Vorhersagewebdiensts mit R finden Sie im Blogbeitrag [Build & Deploy Predictive Web Apps Using RStudio and Azure ML](http://blogs.technet.com/b/machinelearning/archive/2015/09/25/build-and-deploy-a-predictive-web-app-using-rstudio-and-azure-ml.aspx) (in englischer Sprache).

Azure Machine Learning Studio ist in erster Linie darauf ausgerichtet, Sie beim Entwickeln und Bereitstellen eines *Vorhersageanalysemodells* zu unterstützen. Es ist jedoch auch möglich, mithilfe von Studio ein Experiment zu entwickeln, das kein Vorhersageanalysemodell umfasst. Beispielsweise kann ein Experiment nur Daten eingeben, bearbeiten und anschließend die Ergebnisse ausgeben. Genau wie ein Vorhersageanalysemodell können Sie dieses Nicht-Vorhersageexperiment als Webdienst bereitstellen, es ist jedoch ein einfacherer Vorgang, da durch das Experiment kein Machine Learning-Modell trainiert oder bewertet wird. Auch wenn dies nicht das typische Einsatzgebiet von Studio ist, wird es in diesem Thema berücksichtigt, um Ihnen eine vollständige Erläuterung der Funktionsweise von Studio zu geben.

## Entwickeln und Bereitstellen eines Vorhersagewebdiensts

Im Folgenden sind die Phasen einer typischen Lösung aufgeführt, während Sie sie mit Machine Learning Studio entwickeln und bereitstellen:

![](media\machine-learning-model-progression-experiment-to-web-service\model-stages-from-experiment-to-web-service.png)

*Abbildung 1: Phasen eines typischen Vorhersageanalysemodells*

### Das Trainingsexperiment

Das ***Trainingsexperiment*** ist der anfängliche Experimentbereich in Machine Learning Studio. Das Trainingsexperiment soll Ihnen einen Bereich bieten, in dem Sie ein Machine Learning-Modell entwickeln, testen, durchlaufen und schließlich trainieren. Sie können sogar mehrere Modelle gleichzeitig trainieren, während sie nach der idealen Lösung suchen. Wenn Sie mit dem Experimentieren fertig sind, wählen Sie jedoch ein einziges trainiertes Modell aus und löschen die übrigen aus dem Experiment. Ein Beispiel für die Entwicklung eines Vorhersageanalyseexperiments finden Sie unter [Entwickeln einer Lösung zur Vorhersageanalyse für die Kreditrisikobewertung in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

### Das Vorhersageexperiment

Sobald das Trainingsexperiment ein trainiertes Modell umfasst, klicken Sie in Machine Learning Studio auf **Set Up Web Service**, und Studio durchläuft den Prozess zur Konvertierung des Trainingsexperiments in ein ***Vorhersageexperiment***. Das Vorhersageexperiment verwendet Ihr trainiertes Modell zur Bewertung neuer Daten und soll letztlich als Azure-Webdienst in Betrieb genommen werden.

Diese Konvertierung wird durch die folgenden Schritte durchgeführt:

-   Konvertieren des Satzes von Modulen, die für das Training verwendet werden, in ein einziges Modul und Speichern des Moduls als trainiertes Modell

-   Beseitigen überflüssiger Module, die nicht im Zusammenhang mit der Bewertung stehen

-   Hinzufügen von Eingabe- und Ausgabeports, die vom letztendlichen Webdienst verwendet werden

Es gibt möglicherweise weitere Änderungen, die Sie vornehmen möchten, um das Vorhersageexperiment auf die Bereitstellung als Webdienst vorzubereiten. Wenn der Webdienst zum Beispiel nur eine Teilmenge der Ergebnisse ausgeben soll, können Sie vor dem Ausgabeport ein Filtermodul hinzufügen.

In diesem Konvertierungsprozess wird das Trainingsexperiment nicht verworfen. Nach Abschluss des Prozesses werden in Studio zwei Registerkarten angezeigt: eine für das Trainingsexperiment und eine für das Vorhersageexperiment. So können Sie vor dem Bereitstellen Ihres Webdiensts noch Änderungen am Trainingsexperiment durchführen und das Vorhersageexperiment neu erstellen. Ebenso können Sie eine Kopie des Trainingsexperiments speichern, um eine weitere Experimentreihe zu starten.

>[AZURE.NOTE]Durch das Klicken auf **Set Up Web Service** starten Sie einen automatischen Prozess zum Konvertieren des Trainingsexperiments in ein Vorhersageexperiment. In den meisten Fällen verläuft dieser reibungslos. Wenn das Trainingsexperiment jedoch komplex ist (z. B. mehrere Pfade für das Training verknüpft werden), möchten Sie diese Konvertierung vielleicht manuell ausführen. Weitere Einzelheiten zur Funktionsweise dieses Konvertierungsvorgangs finden Sie unter [Konvertieren eines Machine Learning-Trainingsexperiments in ein Vorhersageexperiment](machine-learning-convert-training-experiment-to-scoring-experiment.md).

### Der Webdienst

Sobald Sie mit dem Vorhersageexperiment zufrieden sind, klicken Sie auf **Deploy Web Service**, um Ihr Modell in Betrieb zu nehmen, indem Sie es als ***Azure-Webdienst*** bereitstellen. Benutzer können jetzt über die Webdienst-REST-API Daten an Ihr Modell senden und Ergebnisse zurückerhalten. Weitere Informationen zur Vorgehensweise finden Sie unter [Nutzen eines Azure Machine Learning-Webdiensts, der von einem Machine Learning-Experiment aus bereitgestellt wurde](machine-learning-consume-web-services.md).

Nachdem Sie den Webdienst bereitgestellt haben, bleiben das Vorhersageexperiment und der Webdienst verbunden, und Sie können zwischen beiden hin und her wechseln:

|***Seite***|***Option***|***Geöffnete Seite***|
| ------------------- | --------------- | ---------------------- |
|Experimentbereich in Studio|**Go to web service**|Webdienstkonfiguration in Studio|
|Webdienstkonfiguration in Studio|**View latest**|Experimentbereich in Studio|
|Webdienstkonfiguration in Studio|**Manage endpoints…**|Endpunktverwaltung im klassischen Azure-Portal|
|Endpunktverwaltung im klassischen Azure-Portal|**Edit in Studio**|Experimentbereich in Studio|

![](media\machine-learning-model-progression-experiment-to-web-service\connections-between-experiment-and-web-service.png)

*Abbildung 2: Verbindungen zwischen einem Experiment und dem Webdienst*

## Der untypische Fall: Erstellen eines Nicht-Vorhersagewebdiensts

Wenn das Experiment kein Vorhersageanalysemodell trainiert, müssen Sie nicht sowohl ein Trainingsexperiment als auch ein Bewertungsexperiment erstellen. Es gibt nur ein Experiment, und dieses kann als Webdienst bereitgestellt werden. (Machine Learning Studio erkennt durch Analyse der verwendeten Module, ob das Experiment ein Vorhersagemodell enthält.)

Wenn Sie Ihr Experiment durchlaufen haben und damit zufrieden sind:

1.  Klicken Sie auf **Set Up Web Service**. Eingabe- und Ausgabeknoten werden automatisch hinzugefügt.

2.  Klicken Sie auf **Run**.

3.  Klicken Sie auf **Deploy Web Service**.

Ihr Webdienst wird jetzt bereitgestellt, und Sie können genau wie bei einem Vorhersagewebdienst darauf zugreifen und ihn verwalten.

## Die Webdienstschaltflächen

In Machine Learning Studio ändern die Webdienstschaltflächen **Set Up Web Service** und **Deploy Web Service** ihren Namen und ihre Funktion je nachdem, an welcher Stelle im Entwicklungsprozess Sie sich befinden.

**Experiment enthält ein Vorhersagemodell**

Wenn das Experiment ein Vorhersagemodell trainiert und bewertet, führen die Webdienstschaltflächen diese Funktionen aus:

|**Typ des Experiments**|**Schaltfläche**|**Funktionsbeschreibung**|
| -------------------- | -------- | -------------- |
|Experiment in der Entwicklungsphase|**Set Up Web Service**|Bietet zwei Optionen|
|&nbsp;|- **Predictive Web Service**|Konvertiert das Experiment sowohl in ein Trainingsexperiment als auch in ein Bewertungsexperiment|
|&nbsp;|- **Retraining Web Service**|Konvertiert das Experiment in ein Neutrainingsexperiment (weitere Informationen finden Sie unten im Abschnitt "Aktualisieren")|
|Trainingsexperiment|**Set Up Web Service**|Bietet zwei Optionen|
|&nbsp;|- **Update Predictive Experiment**|Aktualisiert das zugeordnete Vorhersageexperiment durch Änderungen, die Sie am Trainingsexperiment vorgenommen haben|
|&nbsp;|- **Retraining Web Service**|Konvertiert das Trainingsexperiment in ein Neutrainingsexperiment (weitere Informationen finden Sie unten im Abschnitt "Aktualisieren")|
|&nbsp;|-*oder*- **Deploy Web Service**|Wenn Sie das Neutrainingsexperiment für die Bereitstellung eingerichtet haben, wird es hierdurch als Webdienst bereitgestellt|
|Vorhersageexperiment|**Deploy Web Service**|Stellt das Vorhersageexperiment als Webdienst bereit|

**Experiment enthält *kein* Vorhersagemodell**

Wenn das Experiment kein Vorhersagemodell trainiert und bewertet, fallen die Webdienstschaltflächen wesentlich einfacher aus:

|**Typ des Experiments**|**Schaltfläche**|**Funktionsbeschreibung**|
| -------------------- | -------- | -------------- |
|Experiment in der Entwicklungsphase|**Set Up Web Service**|Bereitet das Experiment für die Bereitstellung als Webdienst vor|
|Für die Bereitstellung vorbereitetes Experiment|**Deploy Web Service**|Stellt das Experiment als Webdienst bereit, öffnet die Konfigurationsseite für den Webdienst|

## Aktualisieren des Webdiensts

Was geschieht, wenn Sie das Experiment nach seiner Bereitstellung als Webdienst aktualisieren möchten?

Das hängt davon ab, was Sie aktualisieren möchten:

**Sie möchten die Eingabe oder Ausgabe ändern, oder Sie möchten die Bearbeitung von Daten durch den Webdienst ändern**

Wenn Sie nicht das Modell ändern möchten, sondern nur die Art und Weise, wie Daten vom Webdienst verarbeitet werden, können Sie das Vorhersageexperiment bearbeiten und dann erneut auf **Deploy Web Service** klicken. Der Webdienst wird beendet, das aktualisierte Vorhersageexperiment wird bereitgestellt, und der Webdienst wird erneut gestartet.

Beispiel: Angenommen, das Vorhersageexperiment gibt die gesamte Zeile der Eingabedaten mit dem vorhergesagten Ergebnis zurück. Sie beschließen, dass der Webdienst nur das Ergebnis zurückgeben soll. Sie können im Vorhersageexperiment direkt vor dem Ausgabeport ein Modul **Project Columns** hinzufügen, um andere Spalten außer dem Ergebnis auszuschließen. Wenn Sie erneut auf **Deploy Web Service** klicken, wird der Webdienst aktualisiert.

**Sie möchten das Modell mit neuen Daten neu trainieren**

Wenn Sie Ihr Machine Learning-Modell beibehalten, es aber mit neuen Daten neu trainieren möchten, haben Sie zwei Optionen:

1.  **Neutrainieren des Modells, während der Webdienst ausgeführt wird.** Wenn Sie das Modell neu trainieren möchten, während der Vorhersagewebdienst ausgeführt wird, müssen Sie dazu ein paar Änderungen am Trainingsexperiment vornehmen, um es als ***Neutrainingsexperiment*** festzulegen. Anschließend können Sie es als ***Neutrainingswebdienst*** bereitstellen. Weitere Anweisungen hierzu finden Sie unter [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](machine-learning-retrain-models-programmatically.md).

2.  **Zurückwechseln zum ursprünglichen Trainingsexperiment und Verwenden anderer Trainingsdaten zum Entwickeln Ihres Modells.** Ihr Vorhersageexperiment ist mit dem Webdienst verknüpft, aber das Trainingsexperiment ist nicht direkt auf diese Weise verknüpft. Wenn Sie das ursprüngliche Trainingsexperiment ändern und auf **Set Up Web Service** klicken, wird ein *neues* Vorhersageexperiment erstellt, das bei seiner Bereitstellung einen *neuen* Webdienst erstellt. Der ursprüngliche Webdienst wird nicht einfach aktualisiert.

    Wenn Sie daher das Trainingsexperiment ändern müssen, öffnen Sie es, und klicken Sie auf **Speichern unter**, um eine Kopie zu erstellen. Auf diese Weise bleiben das ursprüngliche Trainingsexperiment, das Vorhersageexperiment und der Webdienst erhalten. Jetzt können Sie einen neuen Webdienst mit Ihren Änderungen erstellen. Nachdem Sie den neuen Webdienst bereitgestellt haben, können Sie entscheiden, ob Sie den vorherigen Webdienst beenden oder weiterhin parallel zu dem neuen Webdienst ausführen möchten.

**Sie möchten ein anderes Modell trainieren**

Wenn Sie am ursprünglichen Vorhersageexperiment Änderungen vornehmen möchten (z. B. einen anderen Machine Learning-Algorithmus auswählen oder eine andere Trainingsmethode ausprobieren), müssen Sie die zweite oben beschriebene Vorgehensweise zum Neutrainieren Ihres Modells befolgen: Öffnen Sie das Trainingsexperiment, klicken Sie auf **Speichern unter**, um eine Kopie zu erstellen, und starten Sie einen neuen Pfad zur Entwicklung des Modells, zur Erstellung des Vorhersageexperiments und zur Bereitstellung des Webdiensts. Dadurch wird ein neuer Webdienst erstellt, der vom ursprünglichen unabhängig ist. Sie können entscheiden, welcher von beiden weiterhin ausgeführt wird bzw. ob beide parallel ausgeführt werden.

## Weitere nützliche Informationen

Weitere Informationen zu diesem Prozess finden Sie in den folgenden Artikeln:

-   Konvertieren des Experiments: [Konvertieren eines Machine Learning-Trainingsexperiments in ein Vorhersageexperiment](machine-learning-convert-training-experiment-to-scoring-experiment.md)

-   Bereitstellen des Webdiensts: [Bereitstellen eines Azure Machine Learning-Webdiensts](machine-learning-publish-a-machine-learning-web-service.md)

-   Neutrainieren des Modells: [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](machine-learning-retrain-models-programmatically.md)

Beispiele für den gesamten Prozess finden Sie unter:

-   [Lernprogramm für maschinelles Lernen: Erstellen Ihres ersten Experiments im Azure Machine Learning Studio](machine-learning-create-experiment.md)

-   [Exemplarische Vorgehensweise: Entwickeln einer Lösung zur Vorhersageanalyse für die Kreditrisikobewertung in Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md)

<!---HONumber=AcomDC_1203_2015-->