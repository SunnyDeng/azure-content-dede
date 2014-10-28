<properties title="Azure Machine Learning API service operations" pageTitle="Machine Learning API service operations | Azure" description="Creating and managing Azure Machine Learning web services" metaKeywords="" services="" solutions="" documentationCenter="" authors="derrickv" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="derrickv"></tags>

# Dienstoperationen der Azure Machine Learning-API
Ein typisches Microsoft Azure Machine Learning (Azure ML)-Projekt umfasst die folgenden übergeordneten Schritte:

1.  Abrufen, Analysieren und Vorbereiten der Daten
2.  Erstellen von Machine Learning-Experimenten unter Verwendung verschiedener ML-Algorithmen
3.  Training, Tests und Generierung eines trainierten Modells
4.  Erstellen eines Operationsworkflows mit dem trainierten Modell und Bereitstellung des Workflows in der Produktionsumgebung
5.  Überwachung der Leistung von Modell und nachfolgenden Updates

>Der Begriff "Experiment" beschreibt einen interaktiven Workflow, der Dateneingabe und -Manipulation, Trainer und Bewerter in Form eines gerichteten azyklischen Graphen (Directed Acyclic Graph DAG). Nachdem ein Workflow als Azure Webdienst veröffentlicht wurde, ist dieser nicht länger interaktiv. Dies bedeutet, dass das Modell geändert und neu veröffentlicht werden muss, um den Webdienst und dessen Verhalten zu ändern.

Die Schritte 1-3 werden normalerweise durch Datenforscher in Form mehrere Iterationen ausgeführt, an deren Ende ein ML-Modell an die technischen- und Betriebsteams übergeben wird, die das Modell wiederum in die Produktionssysteme integrieren.

Die traditionelle Integration und Bereitstellung des ML-Modells in einem Produktionssystem kann in Abhängigkeit von verwendetem Code (R, Python, C# oder Java), Betrachtungen zu Plattformintegration und Infrastruktur und der Bereitstellungsplanung Wochen oder sogar Monate dauern.

Azure ML vereinfacht und beschleunigt dieses Verfahren. Dazu werden zunächst Modellerstellung und -Auswertung einfach und intuitiv gestaltet. Anschließend wird das Experiment über ein einfaches Verfahren als Webdienst in Azure bereitgestellt, um die Gesamtzeit von Modellexperimenten zur Ausführung des Modells als Webdienst in der Produktionsumgebung drastisch verkürzt wird.

Dieses Dokument beschreibt die Konzepte und Schritte für die Einrichtung eines Azure ML-Webdiensts aus einem ML-Experiment.

# Übersicht über den Azure ML-Prozess
Mit Azure ML können Sie Webdienste aus ML-Experimenten erstellen, die Sie im Azure Machine Learning Studio (ML Studio) definiert haben. Azure ML-Webdienste können Vorhersagen anhand tatsächlicher Eingabedaten in Echtzeit oder im Batch-Modus erstellen.

Das folgende Diagramm zeigt einen Überblick über die Schritte in zwei Teilen: Zunächst die Modellerstellung und anschließend die Veröffentlichung als Webdienst. Dieses Dokument konzentriert sich auf die rechte Seite in Abbildung 1 – Veröffentlichung eines Bewertungs-Webdiensts - und beschreibt die zugehörigen Konzepte.

![][]

Abbildung 1: Einrichtung, Erstellung und Veröffentlichung eines Bewertungs-Webdiensts

# Azure ML-Webdienste
Ein Webdienst im Kontext des maschinellen Lernens (ML) ist eine Softwareschnittstelle für die Kommunikation zwischen externen Anwendungen und einem Machine Learning-Workflow. Der Webdienst ermöglicht die Kommunikation mit einem Bewertungsmodell in Echtzeit und liefert Vorhersageergebnisse an externe Clientanwendungen. Azure ML nutzt Microsoft Azure für Bereitstellung, Hosting und Verwaltung der Azure ML-Webdienste. Mit Azure ML können Sie zwei Arten von Diensten erstellen.

## Antwort-Anfrage-Dienst (Request-Response Service RRS)
Der Antwort-Anfrage-Dienst (Request-Response Service RRS) ist ein hochskalierbarer Webdienst mit niedriger Latenz und stellt eine Schnittstelle für zustandslose Modelle bereit, die in der Experimentumgebung erstellt und veröffentlicht wurden.

- REST-API: RRS ist ein RESTFul-Webdienst.
-   Dienstschnittstelle: Der RRS-Webdienst wird bei der Erstellung des Experiments in Form von Ein- und Ausgabe-Anschlüssen für das Azure ML Studio-Experiment definiert.
-   Entwicklungsphasen: Der RRS-Dienst wird als Teil des Azure ML-Workflow zunächst in der Stagingumgebung generiert und kann dort getestet werden. Sobald der Dienst vollständig und bereit für den Produktionseinsatz ist, wird er in der Produktionsumgebung bereitgestellt.
-   In Azure bereitgestellt: Das Resultat der RRS-Bereitstellung ist ein Azure-Webdienst-Endpunkt.
-   Schnittstellenparameter: Eine Anfrage an einen RRS-Dienst enthält die zu bewertenden Daten für das in Studio definierte Experiment. Die Antwort ist das Vorhersageergebnis des Modells.
-   Antwortwerte: RRS akzeptiert eine einzige Reihe von Eingabeparametern und generiert eine einzige Reihe als Ausgabe. Die Ausgabereihe kann mehrere Spalten enthalten.

## Stapelausführungsdienst (Batch Execution Service BES)
Der Stapelausführungsdienst (Batch Execution Service BES) dient zur asynchronen Bewertung eines Stapels von Datensätzen. Die Eingaben für RRS und BES sind einander sehr ähnlich. BES liest im Gegensatz zu RRS einen Stapel von Einträgen aus einer Vielzahl von Quellen wie z. B. Blobs, Tabellen in Azure, SQL Azure, HDInsight (Hive-Abfrage) und HTTP-Quellen. Die Bewertungsergebnisse werden in eine Datei im Azure-Blobspeicher geschrieben, und Daten aus dem Speicher-Endpunkt werden in der Antwort zurückgegeben.

BES stellen außerdem Schnittstellen für Statusabfragen für den laufenden Bewertungsprozess und zum Abbrechen der Verarbeitung bereit. BES können Modellpakete für sehr große Datenmengen ausführen.

-   REST-API: BES ist ein RESTFul-Webdienst.
-   Dienstschnittstelle: Wie auch RRS wird der BES-Webdienst bei der Erstellung des Modells in Form von Ein- und Ausgabe-Anschlüssen für das Azure ML Studio-Experiment definiert.
-   Entwicklungsphasen: Der BES-Dienst wird als Teil des Workflow zunächst in der Stagingumgebung generiert und kann dort getestet werden. Sobald der Dienst vollständig und bereit für den Produktionseinsatz ist, wird er in der Produktionsumgebung bereitgestellt.
-   In Azure bereitgestellt: Das Resultat der BES-Bereitstellung ist ein Azure-Webdienst-Endpunkt.
-   Schnittstellenparameter: Anfragen an einen BES-Dienst bestehen aus einer URL zu einer Datei in einem Azure-Blob oder einer SAS-Eingabe mit den zu bewertenden Datensätzen. Die Antwort wird in einen Azure-Blob geschrieben, und die URL zum entsprechenden Speicherendpunkt wird zurückgegeben.

# Veröffentlichen eines Azure ML-Webdiensts
Azure ML Studio enthält eine browserbasierte Anwendung zur einfachen Erstellung und Ausführung von Machine Learning-Experimenten mit einer grafischen Benutzeroberfläche unter Verwendung verschiedener Datenquellen, Datenmanipulation, Prüfungsmodulen und ML-Algorithmen. Experimente in ML Studio werden als gerichtete azyklische Graphen (Directed Acyclic Graph DAG) von Datenverarbeitungsmodulen erstellt.

Sobald das Experiment eingerichtet und erfolgreich für das Training mit den Daten ausgeführt wurde, kann es als trainiertes Modell gespeichert und für die Bewertung eingesetzt werden. Das trainierte Modell wird anschließend in Bewertungsexperimenten oder als Workflow verwendet und in Form eines Azure-Webdiensts veröffentlicht.

## Trainingsexperiment
Experimente enthalten verschiedene Module zum Laden und Manipulieren von Daten, Anwenden von Machine Learning-Algorithmen und zur Auswertung von Ergebnissen. Ein Trainingsmodell verwendet das Trainingsdataset und einen Lernalgorithmus, um eine Antwort vorherzusagen.

Sobald das Modell erfolgreich durchlaufen wurde, kann ein trainiertes Modell als wiederverwendbare Komponente zur Bewertung von Testdatasets und Abfragen gespeichert werden.

![][1]

Abbildung 2: Beispiel eines trainierten Modells in einem Experiment

Das gespeicherte trainierte Modell ist anschließend im Bereich für trainierte Modelle der Anwendung verfügbar.

![][2]

Abbildung 3: Bereich für trainierte Modelle mit der Liste der Modelle

## Bewertungsexperiment
Bewertungsexperimente generieren Vorhersagen anhand des trainierten Modells und Beispieldaten.

Abbildung 1 zeigt die Verwendung eines Bewertungsmodells in einem Experiment. Im Studio ist dies Teil der Machine Learning-Module.

![][3]

Abbildung 4: Bewertungsmodell

### Anfrage-Antwort-Dienst vs. Stapelausführungsdienst
Bei der Erstellung von Bewertungsexperimenten, die als Webdienste veröffentlicht werden sollen, können je nach Bewertungsszenario beide Modelle verwendet werden. Falls bei den Bewertungsanfragen einzelne Datensätze bewertet werden müssen, wie z. B. die Frage, ob Kunde A den Anbieter wechseln wird (Kundenabwanderungsprognose), kann dies in Echtzeit geschehen und würde in Form eines RRS-Webdiensts erstellt. Der Dienst gibt das Ergebnis des Vorhersagemodells in Echtzeit zurück – im Fall der obigen Kundenabwanderungsanalyse wäre die Antwort entweder ja oder nein.

Für Bewertungsoperationen, in denen viele Datensätze in einer Anfrage bewertet werden müssen, z. B. wenn ein Stapel von Datensätzen mit Kundendaten zur Bewertung an den Dienst geschickt wird, ist BES das geeignete Modell. In diesem Fall wird eine asynchrone Anfrage gestellt, in deren Rahmen alle Datensätze verarbeitet und in einem Azure-Blob gespeichert werden, bevor eine Antwort zurückgegeben wird.

### Verwenden des trainierten Modells
Das trainierte Modell ("Adult Income Predictor" in Abbildung 3) wird zum Experiment hinzugefügt, um das Bewertungsexperiment einzurichten. Andere Module, die zum Trainieren des trainierten Modells verwendet wurden, können nun entfernt werden. Abbildung 5 zeigt ein Beispiel für einen vollständigen Workflow.

### Ein- und Ausgabeports
Nach der Einrichtung des Experiments mit dem trainierten Modell (siehe oben für Details) und der Bewertung des aktualisierten Experiments müssen die Ein- und Ausgabeports konfiguriert werden. Diese Ports definieren Ein- und Ausgangspunkte für die Daten im Vorhersagemodell und im Vorhersageergebnis und dienen gleichzeitig als Schnittstellendefinition für den veröffentlichten Webdienst. Der Eingabeport für das Bewertungsmodell kann per Rechtsklick auf den Einstiegspunkt gesetzt werden, wie in der Abbildung gezeigt.

![][4]

Abbildung 5: Setzen des Eingabeports für die Bewertung

Der Ausgabeport für das Bewertungsmodell kann auf dieselbe Weise gesetzt werden.

![][5]

Abbildung 6: Setzen des Ausgabeports

## Veröffentlichen des Diensts
Nachdem die Ports gesetzt und das Experiment ausgeführt wurde, kann das Modell als Webdienst veröffentlicht werden. Dabei wird der Dienst zunächst in der Stagingumgebung veröffentlicht und getestet, um sicherzustellen, dass er die korrekten Ergebnisse liefert, bevor er in der Produktionsumgebung bereitgestellt wird.

### Veröffentlichen in der Stagingumgebung
Wenn Sie auf das Symbol "Webdienst veröffentlichen" klicken, wird der Webdienst in der Stagingumgebung veröffentlicht.

![][6]

Abbildung 7: Symbol "Webdienst veröffentlichen"

Nach der Veröffentlichung des Modells als Webdienst in der Stagingumgebung können Sie den Dienst mit Eingabeparametern testen und für die Bereitstellung in der Produktionsumgebung markieren. Im Dashboard finden Sie den Link zum Testen.

![][7]

Abbildung 8: Webdienst-Dashboard

Testen Sie den Webdienst in der Stagingumgebung, indem Sie auf den Test-Link klicken und Parameter für die Bewertung eingeben. Die Testanfrage wird anhand des Modells und der eingegebenen Daten bewertet, und das Ergebnis der Bewertung wird zurückgegeben.

### Veröffentlichen in der Produktionsumgebung
Wenn ein Webdienst in der Produktionsumgebung veröffentlicht wird, kann er von anderen Anwendungen für Vorhersagen und Bewertungen verwendet werden. Nach Bereitstellung und erfolgreichen Tests in der Stagingumgebung wird der Webdienst für die Bereitstellung in der Produktionsumgebung markiert.

![][8]

Abbildung 9: Markieren des Webdiensts als bereit für die Produktionsbereitstellung

Dabei wird die Bereitstellung nicht direkt durchgeführt. Stattdessen wird eine Benachrichtigung an den Benutzer mit den entsprechenden Berechtigungen für die Bereitstellung des Diensts in der Produktionsumgebung generiert.

![][9]

Abbildung 10: Bereitstellungsbenachrichtigung und Option zur Bereitstellung in der Produktionsumgebung

## Aufrufen des Webdiensts
#### RRS
Der RRS-Webdienst ist ein REST-Endpunkt und kann von Clientanwendungen in verschiedensten Programmiersprachen aufgerufen werden. Auf der API-Hilfeseite finden sie einen Link zu Beispielcode für Aufrufe an den Webdienst mit Beispielen in C#, R und Python.

![][10]

Abbildung 11: Beispielcode für RRS-Aufrufe

## Andere Arten von Experimenten
Neben der Erstellung von Bewertungs-Webdiensten können Experimente auch andere Aufgaben wie z. B. Datenextraktion und -Transformation ausführen. In diesen Fällen führt der Webdienst keine Machine Learning-Operationen aus. Stattdessen verwendet er die Datenmanipulationsmöglichkeiten in Azure ML Studio, um aus verschiedenen Datenquellen zu lesen, Datentypen zu konvertieren oder Daten zu filtern bzw. Daten- und mathematische Manipulationen auszuführen.

### Veröffentlichen alternativer Webdienste
Die Schritte bei der Veröffentlichung alternativer Webdienste sind dieselben wie bei dem oben beschriebenen Bewertungswebdienst. Der Hauptunterschied liegt darin, dass der Ausgabeport nicht im Bewertungsmodell definiert ist.

# Aktualisieren eines veröffentlichten Diensts
Veröffentlichte Dienste müssen aus unterschiedlichen Gründen aktualisiert werden. Dazu gehören Änderungen an den Trainingsdaten, Änderungen an dem für Training und Bewertung verwendeten Datenschema, Algorithmusverbesserungen oder andere Änderungen am ML-Ausgangsmodell. Diese Änderungen wirken sich auf das trainierte Modell und die Bewertungsergebnisse aus und erfordern eine erneute Veröffentlichung des Webdiensts.

![][11]

Abbildung 12: Bearbeiten des Modells und Veröffentlichen des aktualisierten Bewertungswebdiensts

## Aktualisieren des trainierten Modells
Nach einer Änderung am Trainingsexperiment muss das trainierte Modell erneut trainiert werden. Dazu muss das veröffentlichte Modell bearbeitet werden. Das folgende Beispiel zeigt den Bewertungsworkflow aus Abbildung 5, nachdem das vorhandene trainierte Modell entfernt wurde.

![][12]

Abbildung 13: Trainiertes Modell aus Workflow entfernt

Anschließend würden Sie die Module zum Aufteilen der Daten in Trainings- und Testsegmente hinzufügen, den Lernalgorithmus ausführen, das Modell trainieren, die Trainingsdaten bewerten und die Ergebnisse überprüfen. Diese Schritte können in Abhängigkeit von anderen Änderungen am Experiment variieren, wenn Sie z. B. einen anderen Lernalgorithmus verwenden möchten. (Abbildung 14)

Sobald die neuen Module hinzugefügt wurden, müssen Sie diese entsprechend konfigurieren, bevor Sie das Experiment erneut ausführen können. Der rote Kreis im folgenden Beispiel gibt an, dass die Beschriftungsspalte für "Train Model" nicht gesetzt wurde.

![][13]

Abbildung 14: Module zum erneuten Trainieren des Modells hinzugefügt.

Klicken Sie auf Train Model und setzen Sie den Spaltennamen, um das Problem zu beheben.

![][14]

Abbildung 15: Einkommensspalte als Beschriftung auswählen

## Speichern des aktualisierten trainierten Modells
Nachdem alle neuen Module korrekt konfiguriert wurden, können Sie das Experiment speichern und erfolgreich erneut ausführen. Anschließend können Sie das trainierte Modell speichern (siehe Abbildung 2 oben). Markieren Sie in diesem Fall das Kontrollkästchen, um anzugeben, dass das vorhandene trainierte Modell aktualisiert werden soll.

![][15]

Abbildung 16: Aktualisieren des vorhandenen trainierten Modells

## Veröffentlichen des aktualisierten Diensts
Sobald Sie das trainierte Modell aktualisiert haben, können Sie die oben beschriebenen Schritte zum Veröffentlichen eines **Azure ML-Webdiensts** wiederholen:

-   Verwenden des (aktualisierten) trainierten Modells im Bewertungsexperiment
-   Setzen von Ein- und Ausgabeports
-   Veröffentlichen in der Stagingumgebung
-   Veröffentlichen in der Produktionsumgebung

Nachdem Sie das Experiment geändert und das neue trainierte Modell erstellt und bewertet haben, können Sie auf "Webdienst veröffentlichen" klicken, um den Dienst zu veröffentlichen. Der neu erstellte Dienst überschreibt den vorhandenen Dienst.

<!--Image references-->
<!--Link references-->

[]: ./media/machine-learning-overview-of-azure-ml-process/oamlp1.png
[1]: ./media/machine-learning-overview-of-azure-ml-process/oamlp2.png
[2]: ./media/machine-learning-overview-of-azure-ml-process/oamlp3.png
[3]: ./media/machine-learning-overview-of-azure-ml-process/oamlp4.png
[4]: ./media/machine-learning-overview-of-azure-ml-process/oamlp5.png
[5]: ./media/machine-learning-overview-of-azure-ml-process/oamlp6.png
[6]: ./media/machine-learning-overview-of-azure-ml-process/oamlp7.png
[7]: ./media/machine-learning-overview-of-azure-ml-process/oamlp8.png
[8]: ./media/machine-learning-overview-of-azure-ml-process/oamlp9.png
[9]: ./media/machine-learning-overview-of-azure-ml-process/oamlp10.png
[10]: ./media/machine-learning-overview-of-azure-ml-process/oamlp11.png
[11]: ./media/machine-learning-overview-of-azure-ml-process/oamlp12.png
[12]: ./media/machine-learning-overview-of-azure-ml-process/oamlp13.png
[13]: ./media/machine-learning-overview-of-azure-ml-process/oamlp14.png
[14]: ./media/machine-learning-overview-of-azure-ml-process/oamlp15.png
[15]: ./media/machine-learning-overview-of-azure-ml-process/oamlp16.png
