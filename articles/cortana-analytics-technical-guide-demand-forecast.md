<properties
	pageTitle="Technischer Leitfaden zur Prognose des Energiebedarfs | Microsoft Azure"
	description="Ein technischer Leitfaden zur Microsoft Cortana Analytics-Lösungsvorlage für die Prognose des Energiebedarfs."
	services="cortana-analytics"
	documentationCenter=""
	authors="yijichen"
	manager="ireiter"
	editor="yijichen"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/24/2016"
	ms.author="yijichen"/>

# Technischer Leitfaden zur Cortana Analytics-Lösungsvorlage für die Prognose des Energiebedarfs

## **Übersicht**

Lösungsvorlagen dienen zur schnelleren Erstellung von E2E-Demos auf Grundlage der Cortana Analytics Suite. Durch das Bereitstellen einer Vorlage wird Ihrem Abonnement die erforderliche Cortana Analytics-Komponente bereitgestellt, und es wird eine Beziehung zwischen Vorlage und Komponente eingerichtet. Außerdem wird für die Datenpipeline ein Seeding mit Beispieldaten ausgeführt, die aus einer Anwendung zur Datensimulation generiert werden. Laden Sie den Datensimulator von dem bereitgestellten Link herunter, und installieren Sie ihn auf Ihrem lokalen Computer. Anweisungen zur Verwendung des Simulators finden Sie in der Datei „readme.txt“. Sobald die Datenpipeline mit den vom Simulator erzeugten Daten gefüllt ist, wird mit dem Erstellen von Machine Learning-Vorhersagen begonnen, die im Power BI-Dashboard visualisiert werden können.

Im Zuge der Bereitstellung führen Sie mehrere Schritte aus, bei denen Sie die Anmeldeinformationen für die Lösung einrichten. Notieren Sie sich diese während der Bereitstellung angegebenen Anmeldeinformationen, z. B. Lösungsname, Benutzername und Kennwort, oder zeichnen Sie sie anderweitig auf.

Das Ziel dieses Dokuments besteht darin, die Referenzarchitektur und die verschiedenen im Rahmen Ihres Abonnements bereitgestellten Komponenten zu beschreiben, die Teil dieser Lösungsvorlage sind. Das Dokument erklärt auch, wie Sie die Beispieldaten durch eigene echte Daten ersetzen, um Erkenntnisse und Vorhersagen aus Ihren eigenen Daten zu gewinnen. Außerdem erläutert das Dokument, welche Teile der Lösungsvorlage geändert werden müssen, wenn Sie die Lösung mit Ihren eigenen Daten anpassen möchten. Am Ende des Dokuments finden Sie Anweisungen zum Erstellen des Power BI-Dashboards für diese Lösungsvorlage.

>[AZURE.TIP] Sie können eine [PDF-Version dieses Dokuments](http://github.com/yijichen/document-public/raw/master/Demand%20Forecasting%20for%20Engery%20Solution%20Template.pdf) herunterladen und drucken.

## **Übersicht**

![](media\cortana-analytics-technical-guide-demand-forecast\ca-topologies-energy-forecasting.png)

### Erläuterungen zur Architektur
Beim Bereitstellen der Lösung werden verschiedene Azure-Dienste innerhalb der Cortana Analytics Suite aktiviert (*z. B.* Event Hub, Stream Analytics, HDInsight, Data Factory, Machine Learning *usw.*). Das oben abgebildete Architekturdiagramm ist eine allgemeine, umfassende Darstellung der Lösungsvorlage für die Prognose des Energiebedarfs. Sie können diese Dienste genauer kennenlernen, indem Sie in dem Lösungsvorlagendiagramm, das beim Bereitstellen der Lösung erstellt wurde, auf die Dienste klicken. In den folgenden Abschnitten werden die einzelnen Bestandteile beschrieben.

## **Datenquelle und Erfassung**

### Synthetische Datenquelle

Die für diese Vorlage verwendete Datenquelle wird mit einer Desktopanwendung generiert. Sie laden diese Anwendung nach dem erfolgreichen Bereitstellen der Vorlage herunter und führen sie aus. Die Anweisungen zum Herunterladen und Installieren dieser Anwendung finden Sie, wenn Sie im Lösungsvorlagendiagramm auf den ersten Knoten für den Datensimulator für die Energieprognose klicken und die Eigenschaftenleiste anzeigen. Diese Anwendung fügt dem [Azure Event Hub](#azure-event-hub)-Dienst Datenpunkte bzw. Ereignisse hinzu, die für den gesamten weiteren Lösungsfluss verwendet werden.

Die Ereignisgenerierungsanwendung füllt Azure Event Hub nur, solange sie auf dem Computer ausgeführt wird.

### Azure Event Hub

Der [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)-Dienst nimmt die von der oben beschriebenen synthetischen Datenquelle bereitgestellten Dateneingaben als Empfänger entgegen.

## **Datenvorbereitung und Analyse**


### Azure Stream Analytics

Mithilfe des [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)-Diensts wird der Eingabedatenstrom aus dem [Azure Event Hub](#azure-event-hub)-Dienst nahezu in Echtzeit analysiert. Die Analyseergebnisse werden in einem [Power BI](https://powerbi.microsoft.com)-Dashboard veröffentlicht, und alle eingehenden Rohereignisse werden in [Azure Storage](https://azure.microsoft.com/services/storage/) zur späteren Verarbeitung durch den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)-Dienst archiviert.

### Benutzerdefinierte Aggregation mit HD Insights

Mit dem Azure HD Insights-Dienst werden (von Azure Data Factory orchestrierte) [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skripts ausgeführt, um die vom Azure Stream Analytics-Dienst archivierten Ereignisse zu aggregieren.

### Azure Machine Learning

Mithilfe des (von Azure Data Factory orchestrierten) [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Diensts werden auf Basis der eingegangen Daten Vorhersagen über den zukünftigen Stromverbrauch einer bestimmten Region getroffen.

## **Veröffentlichung der Daten**


### Azure SQL-Datenbankdienst

Mit dem (von Azure Data Factory orchestrierten) [Azure SQL-Datenbankdienst](https://azure.microsoft.com/services/sql-database/) werden die von Azure Machine Learning empfangenen Vorhersagen gespeichert, damit sie später im [Power BI](https://powerbi.microsoft.com)-Dashboard genutzt werden können.

## **Nutzung der Daten**

### Power BI

Mit dem [Power BI](https://powerbi.microsoft.com)-Dienst wird ein Dashboard angezeigt, das die von [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) bereitgestellten Aggregationen sowie die Ergebnisse der Bedarfsprognose enthält, die von [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) erstellt und in [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) gespeichert wurden. Anweisungen zum Erstellen des Power BI-Dashboards für diese Lösungsvorlage finden Sie im entsprechenden Abschnitt weiter unten.

## **Einbringen Ihrer eigenen Daten**

Dieser Abschnitt beschreibt, wie Sie Ihre eigenen Daten zu Azure übertragen und in welchen Bereichen die in diese Architektur eingebrachten eigenen Daten Änderungen erfordern.

Es ist unwahrscheinlich, dass ein von Ihnen eingebrachtes Dataset mit dem für diese Vorlage verwendeten Dataset übereinstimmt. Daher ist es entscheidend, dass Sie Ihre Daten und die Anforderungen genau verstehen. Nur dann können Sie diese Vorlage so abändern, dass sie mit Ihren eigenen Daten ordnungsgemäß funktioniert. Wenn Sie dem Azure Machine Learning-Dienst hier erstmals begegnen, nutzen Sie das Beispiel unter [Erstellen Ihres ersten Experiments](machine-learning\machine-learning-create-experiment.md), um sich damit vertraut zu machen.

In den folgenden Abschnitten werden die Teile der Vorlage erläutert, die beim Verwenden eines neuen Datasets geändert werden müssen.

### Azure Event Hub

Der [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)-Dienst ist sehr allgemein angelegt, sodass Daten im CSV-Format oder im JSON-Format an den Hub gesendet werden können. Im Azure Event Hub wird keine spezielle Verarbeitung ausgeführt, aber Sie sollten unbedingt genau wissen, welche Daten an ihn übermittelt werden.

In diesem Dokument wird nicht beschrieben, wie Sie Daten erfassen. Es ist jedoch einfach, Ereignisse oder Daten über die [Event Hub-API](event-hubs\event-hubs-programming-guide.md) an Azure Event Hub zu senden.

### Azure Stream Analytics

Mithilfe des [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)-Diensts werden Analysen nahezu in Echtzeit durchgeführt. Dazu werden Datenströme gelesen und Daten an eine beliebige Anzahl von Quellen ausgegeben.

Bei der Lösungsvorlage für die Prognose des Energiebedarfs besteht die Azure Stream Analytics-Abfrage aus zwei Unterabfragen, die jeweils Ereignisse aus dem Azure Event Hub-Dienst als Eingabe nutzen und Ausgabedaten für zwei verschiedene Speicherorte erzeugen. Diese Ausgabedaten bestehen aus einem Power BI-Dataset und einem Azure Storage-Speicherort.

Sie finden die [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)-Abfrage, indem Sie folgende Schritte ausführen:

-   Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.

-   Suchen die Stream Analytics-Aufträge ![](media\cortana-analytics-technical-guide-demand-forecast\icon-stream-analytics.png), die beim Bereitstellen der Lösung generiert wurden. Einer dient zum Übertragen von Daten per Push an den Blobspeicher (z. B. mytest1streaming432822asablob), und der andere dient zum Übertragen von Daten per Push an Power BI (z. B. mytest1streaming432822asapbi).


-   Wählen Sie Folgendes aus:

    -   ***EINGABEN*** zum Anzeigen der Abfrageeingabe

    -   ***ABFRAGE*** zum Anzeigen der Abfrage selbst

    -   ***AUSGABEN*** zum Anzeigen der verschiedenen Ausgaben

Informationen zum Aufbau von Azure Stream Analytics-Abfragen finden Sie auf MSDN unter [Stream Analytics-Abfragereferenz](https://msdn.microsoft.com/library/azure/dn834998.aspx).

In dieser Lösung wird der Azure Stream Analytics-Auftrag, der ein Dataset mit nahezu in Echtzeit gewonnenen Analyseinformationen über den eingehenden Datenstrom an ein Power BI-Dashboard ausgibt, als Teil dieser Lösungsvorlage zur Verfügung gestellt. Da diesen Abfragen ein implizites Wissen über das Format der eingehenden Daten zugrunde liegt, müssen sie entsprechend Ihrem eigenen Datenformat geändert werden.

Der andere Azure Stream Analytics-Auftrag gibt alle [Event Hub](https://azure.microsoft.com/services/event-hubs/)-Ereignisse an [Azure Storage](https://azure.microsoft.com/services/storage/) aus und muss deshalb unabhängig von Ihrem Datenformat nicht geändert werden. Die Ereignisinformationen werden in jedem Fall vollständig in den Speicher übertragen.

### Azure Data Factory

Der [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)-Dienst koordiniert die Verschiebung und Verarbeitung von Daten. In der Lösungsvorlage für die Prognose des Energiebedarfs besteht die Data Factory aus zwölf [Pipelines](data-factory\data-factory-create-pipelines.md), die die Daten mit verschiedenen Technologien verschieben und verarbeiten.

  Sie können auf Ihre Data Factory zugreifen, indem Sie in dem Lösungsvorlagendiagramm, das bei der Bereitstellung der Lösung erstellt wurde, unten auf den Knoten für Data Factory klicken. Dadurch gelangen Sie zur Data Factory in Ihrem Azure-Verwaltungsportal. Wenn unter Ihren Datasets Fehler angezeigt werden, können Sie sie ignorieren. Sie sind entstanden, weil die Data Factory vor dem Start des Datengenerators bereitgestellt wurde. Diese Fehler verhindern nicht, dass Ihre Data Factory ordnungsgemäß funktioniert.

In diesem Abschnitt werden die notwendigen [Pipelines](data-factory\data-factory-create-pipelines.md) und [Aktivitäten](data-factory\data-factory-create-pipelines.md) erläutert, die in der [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) enthalten sind. Im Folgenden ist die Diagrammansicht der Lösung dargestellt.

![](media\cortana-analytics-technical-guide-demand-forecast\ADF2.png)

Fünf der Pipelines dieser Factory enthalten [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skripts, die zum Partitionieren und Aggregieren der Daten verwendet werden. Wenn angemerkt, werden die Skripts in dem bei der Einrichtung erstellten [Azure Storage](https://azure.microsoft.com/services/storage/)-Konto abgelegt. Ihr Speicherort ist: demandforecasting\\\script\\\hive\\\ (oder https://[Your Lösungsname].blob.core.windows.net/demandforecasting).

Ähnlich wie bei den [Azure Stream Analytics](#azure-stream-analytics-1)-Abfragen liegt den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skripts ein implizites Wissen über das Format der eingehenden Daten zugrunde. Diese Abfragen müssen also gemäß Ihrem Datenformat und anhand Ihrer Anforderungen für die [Featureentwicklung](machine-learning\machine-learning-feature-selection-and-engineering.md) geändert werden.

#### *AggregateDemandDataTo1HrPipeline*

Diese [Pipeline](data-factory\data-factory-create-pipelines.md) enthält eine einzelne Aktivität: eine [HDInsightHive](data-factory\data-factory-hive-activity.md)-Aktivität mit einem [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx), der ein [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skript ausführt, um die alle 10 Sekunden eingehenden gestreamten Bedarfsdaten auf Unterstationsebene auf die stündliche Regionsebene zu aggregieren und sie über den Stream Analytics-Auftrag in [Azure Storage](https://azure.microsoft.com/services/storage/) einzubringen.

Das [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skript für diese Partitionierungsaufgabe heißt ***AggregateDemandRegion1Hr.hql***


#### *LoadHistoryDemandDataPipeline*

Diese [Pipeline](data-factory\data-factory-create-pipelines.md) enthält zwei Aktivitäten:
- [HDInsightHive](data-factory\data-factory-hive-activity.md)-Aktivität mit einem [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx), der ein Hive-Skript ausführt, um die stündlich eingehenden Verlaufsdaten zum Bedarf auf Unterstationsebene auf die stündliche Regionsebene zu aggregieren und während des Azure Stream Analytics-Auftrags in Azure Storage einzubringen.

- [Kopieraktivität](https://msdn.microsoft.com/library/azure/dn835035.aspx), mit der die aggregierten Daten aus dem Azure Storage-Blob in die Azure SQL-Datenbank verschoben werden, die im Rahmen der Lösungsvorlageninstallation bereitgestellt wurde.

Das [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skript für diese Aufgabe ist ***AggregateDemandHistoryRegion.hql***.


#### *MLScoringRegionXPipeline*

Diese [Pipelines](data-factory\data-factory-create-pipelines.md) enthalten verschiedene Aktivitäten, deren Endergebnis die bewerteten Vorhersagen aus dem Azure Machine Learning-Experiment sind, das dieser Lösungsvorlage zugeordnet ist. Sie sind nahezu identisch, außer dass jede eine andere Region verarbeitet. Dies geschieht über eine jeweils unterschiedliche RegionID, die in der ADF-Pipeline und im Hive-Skript für die einzelnen Regionen übergeben wird. Folgende Aktivitäten sind enthalten:
-	Eine [HDInsightHive](data-factory\data-factory-hive-activity.md)-Aktivität mit [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx), die ein Hive-Skript ausführt, um Aggregationen und die Featureentwicklung durchzuführen. Beides wird für das Azure Machine Learning-Experiment benötigt. Die Hive-Skripts für diese Aufgabe sind die entsprechenden Dateien ***PrepareMLInputRegionX.hql***.

-	Eine [Kopieraktivität](https://msdn.microsoft.com/library/azure/dn835035.aspx), bei der die Ergebnisse aus der [HDInsightHive](data-factory\data-factory-hive-activity.md)-Aktivität in ein einziges Azure Storage-Blob verschoben werden, auf das von der [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)-Aktivität aus zugegriffen werden kann.

-	Eine [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)-Aktivität, die das Azure Machine Learning-Experiment aufruft. Dadurch werden die Ergebnisse in einem einzigen Azure Storage-Blob abgelegt.

#### *CopyScoredResultRegionXPipeline*
Diese [Pipelines](data-factory\data-factory-create-pipelines.md) enthalten eine einzige Aktivität: eine [Kopieraktivität](https://msdn.microsoft.com/library/azure/dn835035.aspx), bei der die Ergebnisse des Azure Machine Learning-Experiments aus der jeweiligen ***MLScoringRegionXPipeline*** in die bei der Installation der Lösungsvorlage bereitgestellte Azure SQL-Datenbank verschoben werden.

#### *CopyAggDemandPipeline*
Diese [Pipelines](data-factory\data-factory-create-pipelines.md) enthalten eine einzige Aktivität: eine [Kopieraktivität](https://msdn.microsoft.com/library/azure/dn835035.aspx), bei der die aggregierten fortlaufenden Bedarfsdaten aus der ***LoadHistoryDemandDataPipeline*** in die bei der Installation der Lösungsvorlage bereitgestellte Azure SQL-Datenbank verschoben werden.

#### *CopyRegionDataPipeline, CopySubstationDataPipeline, CopyTopologyDataPipeline*
Diese [Pipelines](data-factory\data-factory-create-pipelines.md) enthalten eine einzelne Aktivität: eine [Kopieraktivität](https://msdn.microsoft.com/library/azure/dn835035.aspx), bei der die Referenzdaten von Region/Unterstation/Topologiegeodaten, die bei der Installation der Lösungsvorlage in das Azure Storage-Blob hochgeladen werden, in die Azure SQL-Datenbank verschoben werden. Diese Datenbank wurde bei der Installation der Lösungsvorlage bereitgestellt.

### Azure Machine Learning
Das für diese Lösungsvorlage verwendete [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment gibt die Bedarfsprognose pro Region an. Dieses Experiment ist von dem hier genutzten Dataset abhängig und muss deshalb für die von Ihnen eingebrachten Daten angepasst oder ersetzt werden.

## **Überwachen des Fortschritts**
Nach dem Start des Daten-Generators füllt sich die Pipeline mit Daten, und die verschiedenen Komponenten der Lösung beginnen, im Anschluss an die von der Data Factory aufgerufenen Befehle in Aktion zu treten. Es gibt zwei Möglichkeiten zum Überwachen der Pipeline.

1. Prüfen Sie die Daten über Azure Blob Storage.

	Einer der Stream Analytics-Aufträge schreibt die unformatierten eingehenden Daten in Blobspeicher. Wenn Sie im Bildschirm auf die Komponente **Azure Blob Storage** Ihrer Lösung klicken, haben Sie die Lösung erfolgreich bereitgestellt. Wenn Sie dann im rechten Bereich auf **Öffnen** klicken, gelangen Sie zum [Azure-Verwaltungsportal](https://portal.azure.com). Klicken Sie dort auf **Blobs**. Im nächsten Bereich sehen Sie eine Liste mit Containern. Klicken Sie auf **energysadata**. Im nächsten Bereich sehen Sie den Ordner **demandongoing**. Innerhalb des Ordners „rawdata“ befinden sich Ordner mit Namen wie „date=2016-01-28“ usw. Wenn diese Ordner angezeigt werden, bedeutet dies, dass die unformatierten Daten erfolgreich auf Ihrem Computer erstellt und im Blobspeicher gespeichert wurden. In diesen Ordnern sollten Sie Dateien mit begrenzten Größen in MB vorfinden.

2. Prüfen Sie die Daten über Azure SQL-Datenbank.

	Der letzte Schritt der Pipeline ist das Schreiben von Daten (z. B. Prognosen aus Machine Learning) in die SQL-Datenbank. Möglicherweise müssen Sie bis zu zwei Stunden warten, bis die Daten in Azure SQL-Datenbank angezeigt werden. Eine Möglichkeit zum Überwachen, wie viele Daten in Ihrer SQL-Datenbank verfügbar sind, bietet das [Azure-Verwaltungsportal](https://manage.windowsazure.com/). Suchen Sie im linken Bereich SQL-DATENBANKEN![](media\cortana-analytics-technical-guide-demand-forecast\SQLicon2.png), und klicken Sie darauf. Suchen Sie anschließend Ihre Datenbank (also demo123456db), und klicken Sie darauf. Klicken Sie auf der nächsten Seite unter **Datenbankverbindung herstellen** auf **Transact-SQL-Abfragen an Ihrer SQL-Datenbank ausführen**.

	Klicken Sie hier auf „Neue Abfrage“, und fragen Sie die Anzahl der Zeilen ab (z. B. „select count(*) from DemandRealHourly“). Solange die Datenbank wächst, sollte sich die Anzahl der Zeilen in der Tabelle erhöhen.)

3. Überprüfen Sie die Daten über das Power BI-Dashboard.

	Sie können das Power BI-Dashboard für heiße Daten einrichten, um die eingehende Rohdaten zu überwachen. Befolgen Sie hierzu die Anweisung im Abschnitt „Power BI-Dashboard“.



## **Power BI-Dashboard**

### Übersicht

In diesem Abschnitt wird beschrieben, wie Sie das Power BI-Dashboard einrichten, um Ihre Echtzeitdaten aus Azure Stream Analytics zu visualisieren („heiße Daten“) und die Prognoseergebnisse aus Azure Machine Learning anzuzeigen („kalte Daten“).


### Einrichten des Dashboards für heiße Daten

In den folgenden Schritten wird erklärt, wie Sie die Echtzeit-Datenausgabe aus den beim Bereitstellen der Lösung generierten Stream Analytics-Aufträgen visualisieren. Zum Ausführen der folgenden Schritte benötigen Sie ein Konto für [Power BI Online](http://www.powerbi.com/). Wenn Sie über kein Konto verfügen, können Sie [eines erstellen](https://powerbi.microsoft.com/pricing).

1.  Fügen Sie die Power BI-Ausgabe in Azure Stream Analytics hinzu.

    -  Sie müssen die Anweisungen unter [Azure Stream Analytics und Power BI: Ein Dashboard mit Echtzeitanalyse und -sichtbarkeit von Streamingdaten](stream-analytics\stream-analytics-power-bi-dashboard.md) ausführen, um die Ausgabe des Azure Stream Analytics-Auftrags an Ihr Power BI-Dashboard einzurichten.

	- Suchen Sie den Stream Analytics-Auftrag im [Azure-Verwaltungsportal](https://manage.windowsazure.com). Der Name des Auftrags sollte folgendermaßen lauten: NameIhrerLösung + „streamingjob“ + Zufallszahl + „asapbi“ (d. h. demostreamingjob123456asapbi).

	- Richten Sie die Ausgabe der ASA-Abfrage ein, die **PBIoutput** lautet. Stellen Sie sicher, dass der **Ausgabealias** mit dem Ihrer Abfrage übereinstimmt. Sie können als **Datasetname** und **Tabellenname** den Namen **EnergyStreamData** angeben. Nachdem Sie die Ausgabe hinzugefügt haben, klicken Sie im unteren Seitenbereich auf **Starten**, um den Stream Analytics-Auftrag zu starten. Sie sollten eine Bestätigungsmeldung erhalten (*z. B.* „Stream Analytics-Auftrag myteststreamingjob12345asablob wurde erfolgreich gestartet.“).

2. Melden Sie sich bei [Power BI Online](http://www.powerbi.com) an.

    -   Im linken Bereich im Abschnitt „Datasets“ unter „Mein Arbeitsbereich“ sollte ein neues Dataset im linken Bereich von Power BI angezeigt werden. die Sie zuvor in den Power BI-Ausgabeeinstellungen des Azure Stream Analytics-Auftrags definiert haben.

    -   Stellen Sie sicher, dass der Bereich ***Visualisierungen*** geöffnet ist und auf der rechten Seite des Bildschirms angezeigt wird.

3. Erstellen Sie die Kachel „Bedarf nach Zeitstempel“:
	-	Klicken Sie im linken Bereich im Abschnitt „Datasets“ auf das Dataset **EnergyStreamData**.

	-	Klicken Sie auf das Symbol **Liniendiagramm** ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic8.png).

	-	Klicken Sie im Bereich **Felder** auf „EnergyStreamData“.

	-	Klicken Sie auf **Timestamp**, und stellen Sie sicher, dass dies unter „Achse“ angezeigt wird. Klicken Sie auf **Load**, und stellen Sie sicher, dass dies unter „Werte“ angezeigt wird.

	-	Klicken Sie oben auf **SPEICHERN**, und nennen Sie den Bericht „EnergyStreamDataReport“. Der Bericht „EnergyStreamDataReport“ wird links im Bereich „Navigator“ im Abschnitt „Berichte“ angezeigt.

	-	Klicken Sie in der oberen rechten Ecke dieses Liniendiagramms auf das Symbol **Visualisierung anheften**![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png). Möglicherweise wird ein Fenster „An das Dashboard anheften“ angezeigt, in dem Sie ein Dashboard auswählen können. Wählen Sie „EnergyStreamDataReport“, und klicken Sie dann auf „Anheften“.

	-	Bewegen Sie den Mauszeiger auf diese Kachel im Dashboard, klicken Sie in der oberen rechten Ecke auf das Symbol „Bearbeiten“, und ändern Sie den Titel in „Bedarf nach Zeitstempel“.

4.	Erstellen Sie weitere Dashboardkacheln auf Basis der entsprechenden Datasets. Die endgültige Dashboardansicht ist unten dargestellt. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic5.png)


### Einrichten des Dashboards für kalte Daten
In einer Pipeline mit kalten Daten besteht das grundlegende Ziel darin, die Bedarfsprognose der einzelnen Regionen zu erhalten. Power BI stellt eine Verbindung mit einer Azure SQL-Datenbank als Datenquelle her, denn dort werden die Vorhersageergebnisse gespeichert.

> [AZURE.NOTE] 1) Es dauert einige Stunden, bis genügend Prognoseergebnisse für das Dashboard gesammelt wurden. Es wird empfohlen, diesen Vorgang 2 bis 3 Stunden nach dem Start des Datengenerators zu starten. 2) Die Voraussetzung für diesen Schritt ist das Herunterladen und Installieren der kostenlosen Software [Power BI Desktop](https://powerbi.microsoft.com/desktop).



1.  Rufen Sie die Anmeldeinformationen für die Datenbank ab.

    Sie benötigen **den Namen des Datenbankservers, den Namen der Datenbank, den Benutzernamen und das Kennwort**, bevor Sie die nächsten Schritte ausführen können. Sie finden diese Informationen anhand der folgenden Schritte.

    -   Sobald **Azure SQL-Datenbank** in Ihrem Lösungsvorlagendiagramm grün angezeigt wird, klicken Sie darauf, und klicken Sie dann auf **Öffnen**. Sie gelangen zum Azure-Verwaltungsportal, und auch Ihre Seite mit den Datenbankinformationen wird geöffnet.

    -   Auf der Seite finden Sie einen Abschnitt „Datenbank“. Darin wird die Datenbank aufgeführt, die Sie erstellt haben. Der Name der Datenbank sollte folgendermaßen lauten: **NameIhrerLösung + Zufallszahl + „db“** (z. B. „mytest12345db“).

	-	Klicken Sie auf die Datenbank. Im neuen Ausklappbereich finden Sie den Namen Ihres Datenbankservers am oberen Rand. Der Name Ihres Datenbankservers sollte folgendermaßen lauten: **NameIhrerLösung + Zufallszahl + „database.windows.net,1433“** (z. B. „mytest12345.database.windows.net,1433“).

	-   Der **Benutzername** und das **Kennwort** für Ihre Datenbank sind identisch mit dem Benutzernamen und dem Kennwort, das Sie zuvor bei der Bereitstellung der Lösung notiert haben.

2.	Aktualisieren Sie die Datenquelle der Power BI-Datei für kalte Daten.
	-  Stellen Sie sicher, dass Sie die aktuelle Version von [Power BI Desktop](https://powerbi.microsoft.com/desktop) installiert haben.

	-	Doppelklicken Sie im heruntergeladenen Ordner **DemandForecastingDataGeneratorv1.0** auf die Datei **Power BI Template\\DemandForecastPowerBI.pbix**. Die anfänglichen Visualisierungen basieren auf unechten Daten. **Hinweis:** Wenn eine Fehlermeldung angezeigt wird, stellen Sie sicher, dass Sie die neueste Version von Power BI Desktop installiert haben.

		Klicken Sie nach dem Öffnen des Programms über der Datei auf **Abfragen bearbeiten**. Doppelklicken Sie im Ausklappfenster im rechten Bereich auf **Quelle**. ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic1.png)

	-   Ersetzen Sie im Ausklappfenster **Server** und **Datenbank** durch Ihre eigenen Server- und Datenbanknamen, und klicken Sie dann auf **OK**. Geben Sie beim Servernamen unbedingt den Port 1433 an (**NameIhrerLösung.database.windows.net, 1433**). Ignorieren Sie die auf dem Bildschirm angezeigten Warnmeldungen.

	-   Im nächsten Ausklappfenster werden im linken Bereich zwei Optionen angezeigt (**Windows** und **Datenbank**). Klicken Sie auf **Datenbank**, und geben Sie den **Benutzernamen** und das **Kennwort** ein (hierbei handelt es sich um den Benutzernamen und das Kennwort, das Sie beim ersten Bereitstellen der Lösung und beim Erstellen der Azure SQL-Datenbank eingegeben haben). Aktivieren Sie bei ***Wählen Sie die Ebene aus, auf die diese Einstellungen anzuwenden sind*** die Option für die Datenbankebene. Klicken Sie auf **Verbinden**.

	-   Wenn die vorherige Seite wieder angezeigt wird, schließen Sie das Fenster. Eine Meldung wird angezeigt. Klicken Sie darin auf **Anwenden**. Klicken Sie zum Schluss auf die Schaltfläche **Speichern**, um die Änderungen zu speichern. Für Ihre Power BI-Datei wurde nun eine Verbindung mit dem Server eingerichtet. Wenn Ihre Visualisierungen leer sind, stellen Sie sicher, dass Sie die Auswahl für die Visualisierungen aufheben, um alle Daten zu visualisieren. Klicken Sie dazu in der rechten oberen Ecke der Legenden auf das Radierersymbol. Klicken Sie auf die Schaltfläche zum Aktualisieren, damit neue Daten in den Visualisierungen widergespiegelt werden. Anfangs sehen Sie in den Visualisierungen nur die Seedingdaten, weil die Data Factory planmäßig alle drei Stunden aktualisiert wird. Wenn Sie die Daten nach drei Stunden aktualisieren, sehen Sie in den Visualisierungen neue Vorhersagen.

3. (Optional) Veröffentlichen Sie das Dashboard für kalte Daten in [Power BI Online](http://www.powerbi.com/). Beachten Sie, dass Sie für diesen Schritt ein Power BI-Konto (oder ein Office 365-Konto) benötigen.

	-   Klicken Sie auf **Veröffentlichen**. Nach einigen Sekunden wird durch eine Meldung mit einem grünen Häkchen bestätigt, dass die Veröffentlichung in Power BI erfolgreich war. Klicken Sie auf den Link unter „demoprediction.pbix in Power BI öffnen“. Ausführliche Anweisungen finden Sie unter [Veröffentlichen aus Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

	-   Um ein neues Dashboard zu erstellen, klicken Sie im linken Bereich neben dem Abschnitt **Dashboards** auf das Symbol **+**. Geben Sie den Namen „Demo für Bedarfsprognose“ für das neue Dashboard ein.

	-   Wenn Sie den Bericht geöffnet haben, klicken Sie auf ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic6.png), um alle Visualisierungen an Ihr Dashboard anzuheften. Ausführliche Informationen finden Sie unter [Anheften einer Kachel an ein Power BI-Dashboard aus einem Bericht](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Wechseln Sie zur Dashboardseite, passen Sie die Größe und Position der Visualisierungen an, und bearbeiten Sie deren Titel. Ausführliche Anweisungen zum Bearbeiten der Titel finden Sie unter [Bearbeiten einer Kachel – Größe ändern, verschieben, umbenennen, anheften, löschen, Link hinzufügen](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename) Nachfolgend sehen Sie ein Beispieldashboard, an das einige Visualisierungen für kalte Daten angeheftet sind.

		![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic7.png)

4. (Optional) Planen Sie die Aktualisierung der Datenquelle.
	-	  Um eine Aktualisierung der Daten zu planen, zeigen Sie mit dem Mauszeiger auf das Dataset **EnergyBPI-Final**, klicken auf ![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic3.png), und wählen Sie dann **Aktualisierung planen**. **Hinweis:** Wenn eine Warnmeldung angezeigt wird, klicken Sie auf **Anmeldeinformationen bearbeiten**, und stellen Sie sicher, dass die Anmeldeinformationen für die Datenbank mit den in Schritt 1 beschriebenen identisch sind.

	![](media\cortana-analytics-technical-guide-demand-forecast\PowerBIpic4.png)

	-   Erweitern Sie den Abschnitt **Aktualisierung planen**. Aktivieren Sie „Halten Sie Ihre Daten aktuell“.

	-   Legen Sie einen geeigneten Zeitplan für die Aktualisierung fest. Weitere Informationen finden Sie unter [Aktualisieren von Daten in Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).


## **Kostenschätzungstools**

Es stehen zwei Tools zur Verfügung, mit denen Sie die Gesamtkosten zum Ausführen der Lösungsvorlage zur Prognose des Energiebedarfs im Rahmen Ihres Abonnements ermitteln können:

-   [Microsoft Azure-Kostenschätzungstool (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure-Kostenschätzungstool (Desktopversion)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0309_2016-->