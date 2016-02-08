<properties
	pageTitle="Technische Anleitung für die Cortana Analytics-Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt und anderen Unternehmen | Microsoft Azure"
	description="Eine technische Anleitung für die Lösungsvorlage mit Microsoft Cortana Analytics für vorbeugende Wartung in der Luft- und Raumfahrt, in Versorgungsunternehmen und im Transport."
	services="cortana-analytics"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cortana-analytics"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/20/2016"
	ms.author="garye" />

# Technische Anleitung für die Cortana Analytics-Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt und anderen Unternehmen

## **Übersicht**

Lösungsvorlagen dienen zur schnelleren Erstellung von E2E-Demos auf Grundlage der Cortana Analytics Suite. Durch das Bereitstellen einer Vorlage wird Ihr Abonnement mit den erforderlichen Cortana Analytics-Komponenten bereitgestellt. Dabei werden auch die Beziehungen zwischen den Komponenten erstellt. Außerdem füllt die Vorlage die Datenpipeline mit Beispieldaten, die in einer Datensimulationsanwendung generiert werden. Diese Simulationsanwendung laden Sie nach dem Bereitstellen der Lösungsvorlage auf den lokalen Computer herunter und installieren sie dort. Die von der Simulationsanwendung generierten Daten werden in die Datenpipeline eingefüttert und stoßen das Generieren von Machine Learning-Vorhersagen an, die Sie anschließend im Power BI-Dashboard visualisieren können. Im Zuge der Bereitstellung führen Sie mehrere Schritte aus, bei denen Sie die Anmeldeinformationen für die Lösung einrichten. Notieren Sie sich diese während der Bereitstellung angegebenen Anmeldeinformationen, z. B. Lösungsname, Benutzername und Kennwort, oder zeichnen Sie sie anderweitig auf.

Das Ziel dieses Dokuments besteht darin, die Referenzarchitektur und die verschiedenen im Rahmen Ihres Abonnements bereitgestellten Komponenten zu beschreiben, die Teil dieser Lösungsvorlage sind. Das Dokument erklärt auch, wie Sie die Beispieldaten durch eigene echte Daten ersetzen, um Erkenntnisse und Vorhersagen aus Ihren eigenen Daten zu gewinnen. Außerdem erläutert das Dokument, welche Teile der Lösungsvorlage geändert werden müssen, wenn Sie die Lösung an Ihre eigenen Daten anpassen möchten. Am Ende des Dokuments finden Sie Anweisungen zum Erstellen des Power BI-Dashboards für diese Lösungsvorlage.

>[AZURE.TIP] Sie können eine [PDF-Version dieses Dokuments](http://download.microsoft.com/download/F/4/D/F4D7D208-D080-42ED-8813-6030D23329E9/cortana-analytics-technical-guide-predictive-maintenance.pdf) herunterladen und drucken.

## **Übersicht**

![](media/cortana-analytics-technical-guide-predictive-maintenance\predictive-maintenance-architecture.png)

Beim Bereitstellen der Lösung werden verschiedene Azure-Dienste innerhalb der Cortana Analytics Suite aktiviert (*z. B.* Event Hub, Stream Analytics, HDInsight, Data Factory, Machine Learning *usw.*). Das oben abgebildete Architekturdiagramm veranschaulicht, wie die Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt in allen ihren Teilen allgemein aufgebaut ist. Sie können diese Dienste genauer kennenlernen, indem Sie in dem Lösungsvorlagendiagramm, das beim Bereitstellen der Lösung erstellt wurde, auf die Dienste klicken. Eine Version des Diagramms in voller Größe können Sie [hier](http://download.microsoft.com/download/1/9/B/19B815F0-D1B0-4F67-AED3-A40544225FD1/ca-topologies-maintenance-prediction.png) herunterladen.

In den folgenden Abschnitten werden die einzelnen Bestandteile beschrieben.

## **Datenquelle und die Erfassung**

### Synthetische Datenquelle

Die für diese Vorlage verwendete Datenquelle wird mit einer Desktopanwendung generiert. Sie laden diese Anwendung nach dem erfolgreichen Bereitstellen der Vorlage herunter und führen sie aus. Die Anweisungen zum Herunterladen und Installieren dieser Anwendung finden Sie, wenn Sie im Lösungsvorlagendiagramm auf den ersten Knoten für den Datensimulator für vorbeugende Wartung klicken und die Eigenschaftenleiste anzeigen. Diese Anwendung füttert Datenpunkte bzw. Ereignisse in den [Azure Event Hub](#azure-event-hub)-Dienst ein, die für den gesamten weiteren Datenfluss der Lösung verwendet werden. Die Datenquelle besteht aus öffentlich zugänglichen Daten aus dem [Datenrepository der NASA](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/) (und dort konkret aus dem [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan)) oder ist aus diesen abgeleitet.

Die Ereignisgenerierungsanwendung füllt Azure Event Hub nur, solange sie auf dem Computer ausgeführt wird.

### Azure Event Hub

Der [Azure Event Hub](https://azure.microsoft.com/services/event-hubs/)-Dienst nimmt die von der oben beschriebenen synthetischen Datenquelle bereitgestellten Dateneingaben als Empfänger entgegen.

## **Datenvorbereitung und Analyse**


### Azure Stream Analytics

Mithilfe des [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)-Diensts wird der Eingabedatenstrom aus dem [Azure Event Hub](#azure-event-hub)-Dienst nahezu in Echtzeit analysiert. Die Analyseergebnisse werden in einem [Power BI](https://powerbi.microsoft.com)-Dashboard veröffentlicht, und alle eingehenden Rohereignisse werden in [Azure Storage](https://azure.microsoft.com/services/storage/) zur späteren Verarbeitung durch den [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)-Dienst archiviert.

### Benutzerdefinierte Aggregation mit HD Insights

Mit dem Azure HD Insights-Dienst werden [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skripte ausgeführt (orchestriert durch Azure Data Factory), um die durch Azure Stream Analytics archivierten Ereignisse zu aggregieren.

### Azure Machine Learning

Mithilfe des [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Diensts (orchestriert durch Azure Data Factory) werden auf Basis der eingegangen Daten Vorhersagen über die Restlebensdauer eines bestimmten Flugzeugtriebwerks getroffen.

## **Veröffentlichung der Daten**


### Azure SQL-Datenbankdienst

Mit dem [Azure SQL-Datenbankdienst](https://azure.microsoft.com/services/sql-database/) werden (orchestriert durch Azure Data Factory) die von Azure Machine Learning empfangenen Vorhersagen gespeichert, damit sie später im [Power BI](https://powerbi.microsoft.com)-Dashboard genutzt werden können.

## **Nutzung der Daten**

### Power BI

Mit dem [Power BI](https://powerbi.microsoft.com)-Dienst wird ein Dashboard angezeigt, dass die von [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/) bereitgestellten Aggregationen und Warnungen sowie die Vorhersagen zur Restlebensdauer enthält, die von [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) erstellt und in [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) gespeichert wurden. Anweisungen zum Erstellen des Power BI-Dashboards für diese Lösungsvorlage finden Sie im entsprechenden Abschnitt weiter unten.

## **Einbringen Ihrer eigenen Daten**

Dieser Abschnitt beschreibt, wie Sie Ihre eigenen Daten zu Azure übertragen und in welchen Bereichen die in diese Architektur eingebrachten eigenen Daten Änderungen erfordern.

Es ist unwahrscheinlich, dass ein von Ihnen eingebrachtes Dataset mit dem für diese Vorlage verwendeten Dataset aus dem [Turbofan Engine Degradation Simulation Data Set](http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/#turbofan) übereinstimmt. Daher ist es entscheidend, dass Sie Ihre Daten und die Anforderungen genau verstehen. Nur dann können Sie diese Vorlage so abändern, dass sie mit Ihren eigenen Daten ordnungsgemäß funktioniert. Wenn Sie dem Azure Machine Learning-Dienst hier erstmals begegnen, nutzen Sie das Beispiel in [Erstellen Ihres ersten Experiments](machine-learning-create-experiment.md), um sich damit vertraut zu machen.

In den folgenden Abschnitten werden die Teile der Vorlage erläutert, die beim Verwenden eines neuen Datasets geändert werden müssen.

### Azure Event Hub

Der Azure Event Hub-Dienst ist sehr allgemein angelegt, sodass Daten im CSV-Format oder im JSON-Format an den Hub gesendet werden können. Im Azure Event Hub wird keine spezielle Verarbeitung ausgeführt, aber es ist wichtig, dass Sie die an ihn übermittelten Daten wirklich verstehen.

In diesem Dokument wird nicht beschrieben, wie Sie Daten erfassen. Es ist jedoch einfach, Ereignisse oder Daten über die Event Hub-APIs an Azure Event Hub zu senden.

### Azure Stream Analytics

Mithilfe des Azure Stream Analytics-Diensts werden Analysen nahezu in Echtzeit durchgeführt. Die Datenströme werden gelesen, und Daten können an eine beliebige Anzahl von Quellen ausgegeben werden.

Bei der Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt besteht die Azure Stream Analytics-Abfrage aus vier Unterabfragen, die jeweils Ereignisse aus dem Azure Event Hub-Dienst nutzen und Ausgabedaten für vier verschiedene Speicherorte erzeugen. Diese Ausgabedaten bestehen aus drei Power BI-Datasets und einem Azure Storage-Speicherort.

Sie finden die Azure Stream Analytics-Abfrage, indem Sie folgende Schritte ausführen:

-   Melden Sie sich beim Azure-Portal an.

-   Suchen Sie die Stream Analytics-Aufträge ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-stream-analytics.png), die beim Bereitstellen der Lösung generiert wurden (*z. B.* **maintenancesa02asapbi** und **maintenancesa02asablob** für die Lösung für vorbeugende Wartung).

-   Wählen Sie Folgendes aus:

    -   ***EINGABEN*** zum Anzeigen der Abfrageeingabe

    -   ***ABFRAGE*** zum Anzeigen der Abfrage selbst

    -   ***AUSGABEN*** zum Anzeigen der verschiedenen Ausgaben

Informationen zum Aufbau von Azure Stream Analytics-Abfragen finden Sie auf MSDN unter [Stream Analytics-Abfragereferenz](https://msdn.microsoft.com/library/azure/dn834998.aspx).

In dieser Lösung geben die Abfragen drei Datasets mit nahezu in Echtzeit gewonnenen Analyseinformationen über den eingehenden Datenstrom an ein Power BI-Dashboard aus, das als Teil der Lösungsvorlage zur Verfügung gestellt wird. Da diesen Abfragen ein implizites Wissen über das Format der eingehenden Daten zugrunde liegt, müssen sie entsprechend Ihrem eigenen Datenformat geändert werden.

Die Abfrage im zweiten Stream Analytics-Auftrag **maintenancesa02asablob** gibt einfach nur alle [Event Hub](https://azure.microsoft.com/services/event-hubs/)-Ereignisse an [Azure Storage](https://azure.microsoft.com/services/storage/) aus und muss deshalb unabhängig von Ihrem Datenformat nicht geändert werden. Die Ereignisinformationen werden in jedem Fall vollständig an Storage übertragen.

### Azure Data Factory

Der [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)-Dienst koordiniert die Verschiebung und Verarbeitung von Daten. In der Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt besteht die Data Factory aus drei [Pipelines](../data-factory/data-factory-create-pipelines.md), die die Daten mit verschiedenen Technologien verschieben und verarbeiten. Sie können auf Ihre Data Factory zugreifen, indem Sie im Lösungsvorlagendiagramm, das bei der Bereitstellung der Lösung erstellt wurde, unten auf den Knoten für Data Factory klicken. Dadurch gelangen Sie zur Data Factory in Ihrem Azure-Portal. Wenn unter Ihren Datasets Fehler angezeigt werden, können Sie sie ignorieren. Sie sind entstanden, weil die Data Factory vor dem Start des Datengenerators bereitgestellt wurde. Diese Fehler verhindern nicht, dass Ihre Data Factory ordnungsgemäß funktioniert.

![](media/cortana-analytics-technical-guide-predictive-maintenance/data-factory-dataset-error.png)

In diesem Abschnitt werden die notwendigen [Pipelines](../data-factory/data-factory-create-pipelines.md) und [Aktivitäten](../data-factory/data-factory-create-pipelines.md) erläutert, die in der [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) enthalten sind. Im Folgenden ist die Diagrammansicht der Lösung dargestellt.

![](media/cortana-analytics-technical-guide-predictive-maintenance/azure-data-factory.png)

Zwei der Pipelines dieser Factory enthalten [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skripte, die zum Partitionieren und Aggregieren der Daten verwendet werden. Wenn angemerkt, werden die Skripte in dem bei der Einrichtung erstellten [Azure Storage](https://azure.microsoft.com/services/storage/)-Konto abgelegt. Ihr Speicherort ist: maintenancesascript\\\script\\\hive\\\ (oder https://[Your Lösungsname].blob.core.windows.net/maintenancesascript).

Ähnlich wie bei den [Azure Stream Analytics](#azure-stream-analytics-1)-Abfragen liegt den [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skripten ein implizites Wissen über das Format der eingehenden Daten zugrunde. Diese Abfragen müssen also gemäß Ihrem Datenformat und anhand Ihrer Anforderungen für die [Featureentwicklung](machine-learning-feature-selection-and-engineering.md) geändert werden.

#### *AggregateFlightInfoPipeline*

Diese [Pipeline](../data-factory/data-factory-create-pipelines.md) enthält eine einzige Aktivität: eine [HDInsightHive](../data-factory/data-factory-hive-activity.md)-Aktivität, die mithilfe von [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) ein [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skript ausführt, um die während des [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/)-Auftrags in [Azure Storage](https://azure.microsoft.com/services/storage/) abgelegten Daten zu partitionieren.

Das [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skript für diese Partitionierungsaufgabe heißt ***AggregateFlightInfo.hql***

#### *MLScoringPipeline*

Diese [Pipeline](../data-factory/data-factory-create-pipelines.md) enthält verschiedene Aktivitäten, deren Endergebnis die bewerteten Vorhersagen aus dem [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment sind, das dieser Lösungsvorlage zugeordnet ist.

Folgende Aktivitäten sind enthalten:

-   Eine [HDInsightHive](../data-factory/data-factory-hive-activity.md)-Aktivität, die mithilfe von [HDInsightLinkedService](https://msdn.microsoft.com/library/azure/dn893526.aspx) ein [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skript ausführt, um Aggregationen und die Featureentwicklung durchzuführen. Beides wird für das [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment benötigt. Das [Hive](http://blogs.msdn.com/b/bigdatasupport/archive/2013/11/11/get-started-with-hive-on-hdinsight.aspx)-Skript für diese Partitionierungsaufgabe heißt ***PrepareMLInput.hql***.

-   Eine [Kopieraktivität](https://msdn.microsoft.com/library/azure/dn835035.aspx), die die Ergebnisse aus der [HDInsightHive](../data-factory/data-factory-hive-activity.md)-Aktivität in ein einziges [Azure Storage](https://azure.microsoft.com/services/storage/)-Blob verschiebt, auf das von der [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)-Aktivität zugegriffen werden kann.

-   Eine [AzureMLBatchScoring](https://msdn.microsoft.com/library/azure/dn894009.aspx)-Aktivität, die das [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment aufruft. Dies bewirkt, dass die Ergebnisse in einem einzigen [Azure Storage](https://azure.microsoft.com/services/storage/)-Blob abgelegt werden.

#### *CopyScoredResultPipeline*

Diese [Pipeline](../data-factory/data-factory-create-pipelines.md) enthält eine einzige Aktivität: eine [Kopieraktivität](https://msdn.microsoft.com/library/azure/dn835035.aspx), die die Ergebnisse des [Azure Machine Learning](#azure-machine-learning)-Experiments aus der ***MLScoringPipeline*** in die bei der Installation der Lösungsvorlage bereitgestellte [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/) verschiebt.

### Azure Machine Learning

Das für diese Lösungsvorlage verwendete [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)-Experiment gibt die Restlebensdauer (Remaining Useful Life, RUL) für ein Flugzeugtriebwerk an. Dieses Experiment ist von dem hier genutzten Dataset abhängig und muss deshalb für die von Ihnen eingebrachten Daten angepasst oder ersetzt werden.

Informationen zum Erstellen des Azure Machine Learning-Experiments finden Sie unter [Vorbeugende Wartung: Schritt 1 von 3, Datenvorbereitung und Featureentwicklung](http://gallery.cortanaanalytics.com/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2).

## **Überwachen des Fortschritts**
 Nach dem Start des Daten-Generators füllt sich die Pipeline mit Daten, und die verschiedenen Komponenten der Lösung beginnen, im Anschluss an die von der Data Factory aufgerufenen Befehle in Aktion zu treten. Es gibt zwei Möglichkeiten zum Überwachen der Pipeline.

1. Einer der Stream Analytics-Aufträge schreibt die unformatierten eingehenden Daten in Blobspeicher. Wenn Sie auf dem Bildschirm auf die Komponente „Blobspeicher“ Ihrer Lösung klicken, haben Sie die Lösung erfolgreich bereitgestellt. Wenn Sie dann im rechten Bereich auf „Öffnen“ klicken, gelangen Sie zum [Verwaltungsportal](https://portal.azure.com/). Klicken Sie dort auf „Blobs“. Im nächsten Bereich sehen Sie eine Liste mit Containern. Klicken Sie auf **maintenancesadata**. Im nächsten Bereich sehen Sie den Ordner **rawdata**. Innerhalb des Ordners „rawdata“ sehen Sie Ordner mit Namen wie „hour=17“, „hour=18“ usw. Wenn diese Ordner angezeigt werden, bedeutet dies, dass die unformatierten Daten erfolgreich auf Ihrem Computer erstellt und im Blobspeicher gespeichert wurden. In diesen Ordnern sollten Sie CSV-Dateien mit Größen in MB vorfinden.

2. Der letzte Schritt der Pipeline ist das Schreiben von Daten (z. B. Prognosen aus Machine Learning) in die SQL-Datenbank. Möglicherweise müssen Sie bis zu drei Stunden warten, bis die Daten in Azure SQL-Datenbank angezeigt werden. Eine Möglichkeit zum Überwachen, wie viele Daten in Ihrer SQL-Datenbank verfügbar sind, bietet das [Azure-Portal](https://manage.windowsazure.com/). Klicken Sie im linken Bereich „SQL-DATENBANKEN“ ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-SQL-databases.png). Suchen Sie Ihre Datenbank **pmaintenancedb**, und klicken Sie darauf. Klicken Sie auf der nächsten Seite unten auf VERWALTEN.

	![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-manage.png).

	Hier können Sie auf „Neue Abfrage“ klicken und dann die Anzahl der Zeilen, z. B. select count(*) aus PMResult, abfragen. Wenn Ihre Datenbank wächst, nimmt auch die Anzahl der Zeilen in der Tabelle zu.


## **Power BI-Dashboard**

### Übersicht

In diesem Abschnitt wird beschrieben, wie Sie das Power BI-Dashboard einrichten, um Ihre Echtzeitdaten aus Azure Stream Analytics zu visualisieren („heiße Daten“) und die Batchvorhersageergebnisse aus Azure Machine Learning anzuzeigen („kalte Daten“).

### Einrichten des Dashboards für kalte Daten

Bei der Datenpipeline für kalte Daten besteht das wesentliche Ziel darin, die vorhergesagte Restlebensdauer jedes Flugzeugtriebwerks abzurufen, nachdem ein Flug oder Flugzyklus abgeschlossen ist. Das Vorhersageergebnis wird alle drei Stunden aktualisiert, um Vorhersagen für die Flugzeugtriebwerke zu erhalten, die in den letzten drei Stunden einen Flug beendet haben.

Power BI stellt eine Verbindung mit einer Azure SQL-Datenbank als Datenquelle her, denn dort werden die Vorhersageergebnisse gespeichert. Beachten Sie: 1) Nach dem Bereitstellen Ihrer Lösung wird alle drei Stunden eine echte Vorhersage in der Datenbank angezeigt. Die PBIX-Datei, die Teil des Downloads für den Generator ist, enthält einige Seedingdaten, sodass Sie das Power BI-Dashboard sofort erstellen können. 2) Die Voraussetzung für diesen Schritt ist das Herunterladen und Installieren der kostenlosen Software [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/).

In den folgenden Schritten wird erklärt, wie Sie die PBIX-Datei mit der SQL-Datenbank verbinden, die beim Bereitstellen der Lösung in Betrieb genommen wurde und die die Daten für die Visualisierung enthält (*z. B.* Vorhersageergebnisse).

1.  Rufen Sie die Anmeldeinformationen für die Datenbank ab.

    Sie benötigen **den Namen des Datenbankservers, den Namen der Datenbank, den Benutzernamen und das Kennwort**, bevor Sie die nächsten Schritte ausführen können. Sie finden diese Informationen anhand der folgenden Schritte.

    -   Sobald **Azure SQL-Datenbank** in Ihrem Lösungsvorlagendiagramm grün angezeigt wird, klicken Sie darauf und dann auf **Öffnen**.

    -   Es wird eine neue Registerkarte im Browser oder ein neues Browserfenster mit der Azure-Portalseite angezeigt. Klicken Sie im linken Bereich auf **Ressourcengruppen**.

    -   Wählen Sie das Abonnement aus, das Sie zum Bereitstellen der Lösung verwenden, und wählen Sie dann **IhrLösungsname\_Ressourcengruppe** aus.

    -   Klicken Sie im daraufhin angezeigten Popupbereich auf das Symbol ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-sql.png), um auf Ihre Datenbank zuzugreifen. Neben diesem Symbol wird der Name Ihrer Datenbank angezeigt (*z. B.* **pmaintenancedb**), und der **Name des Datenbankservers** wird unter der Eigenschaft „Servername“ aufgelistet. Er sollte ähnlich dem Folgenden aussehen: **IhrLösungsname.database.windows.net**.

	-   Der **Benutzername** und das **Kennwort** für Ihre Datenbank sind identisch mit dem Benutzernamen und dem Kennwort, das Sie zuvor bei der Bereitstellung der Lösung notiert haben.

2.  Aktualisieren Sie die Datenquelle der Berichtsdatei für kalte Daten mit Power BI Desktop.

    -   Doppelklicken Sie in dem Ordner auf Ihrem PC, in dem Sie die heruntergeladene Generatordatei entzippt haben, auf die Datei **PowerBI\\PredictiveMaintenanceAerospace.pbix**. Wenn beim Öffnen der Datei Warnmeldungen angezeigt werden, ignorieren Sie sie. Klicken Sie über der Datei auf **Abfragen bearbeiten**.

	    ![](media\cortana-analytics-technical-guide-predictive-maintenance\edit-queries.png)

	-	Es werden zwei Tabellen angezeigt, **RemainingUsefulLife** und **PMResult**. Wählen Sie die erste Tabelle aus, und klicken Sie im rechten Bereich **Abfrageeinstellungen** unter **ANGEWENDETE SCHRITTE** neben **Quelle** auf ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-query-settings.png). Ignorieren Sie alle angezeigten Warnmeldungen.

    -   Ersetzen Sie im Popupfenster **Server** und **Datenbank** durch Ihre eigenen Server- und Datenbanknamen, und klicken Sie dann auf **OK**. Geben Sie beim Servernamen unbedingt den Port 1433 an (**IhrLösungsname.database.windows.net, 1433**). Ignorieren Sie die auf dem Bildschirm angezeigten Warnmeldungen.

    -   Im nächsten Popupfenster werden im linken Bereich zwei Optionen angezeigt (**Windows** und **Datenbank**). Klicken Sie auf **Datenbank**, geben Sie den **Benutzernamen** und das **Kennwort** ein (hierbei handelt es sich um den Benutzernamen und das Kennwort, das Sie beim ersten Bereitstellen der Lösung und beim Erstellen der Azure SQL-Datenbank eingegeben haben). Aktivieren Sie bei ***Wählen Sie die Ebene aus, auf die diese Einstellungen anzuwenden sind*** die Option für die Datenbankebene. Klicken Sie auf **Verbinden**.

    -   Klicken Sie auf die zweite Tabelle **PMResult**, und klicken Sie dann im rechten Bereich **Abfrageeinstellungen** unter **ANGEWENDETE SCHRITTE** neben **Quelle** auf ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-navigation.png). Aktualisieren Sie die Server- und Datenbanknamen wie in den Schritten zuvor, und klicken Sie auf „OK“.

    -   Wenn die vorherige Seite wieder angezeigt wird, schließen Sie das Fenster. Es wird eine Meldung angezeigt – klicken Sie darin auf **Anwenden**. Klicken Sie zum Schluss auf die Schaltfläche **Speichern**, um die Änderungen zu speichern. Für Ihre Power BI-Datei wurde nun eine Verbindung mit dem Server eingerichtet. Wenn Ihre Visualisierungen leer sind, stellen Sie sicher, dass Sie die Auswahl für die Visualisierungen aufheben, um alle Daten zu visualisieren. Klicken Sie dazu in der rechten oberen Ecke der Legenden auf das Radierersymbol. Klicken Sie auf die Schaltfläche zum Aktualisieren, damit neue Daten in den Visualisierungen widergespiegelt werden. Anfangs sehen Sie in den Visualisierungen nur die Seedingdaten, weil die Data Factory planmäßig alle drei Stunden aktualisiert wird. Wenn Sie die Daten nach drei Stunden aktualisieren, sehen Sie in den Visualisierungen neue Vorhersagen.

3.  (Optional) Veröffentlichen des Dashboards für kalte Daten in [Power BI online](http://www.powerbi.com/). Beachten Sie, dass Sie für diesen Schritt ein Power BI-Konto (oder ein Office 365-Konto) benötigen.

    -   Klicken Sie auf **Veröffentlichen**. Nach einigen Sekunden wird durch eine Meldung mit einem grünen Häkchen bestätigt, dass die Veröffentlichung in Power BI erfolgreich war. Klicken Sie auf den Link unter „PredictiveMaintenanceAerospace.pbix in Power BI öffnen“. Ausführliche Anweisungen finden Sie unter [Veröffentlichen aus Power BI Desktop](https://support.powerbi.com/knowledgebase/articles/461278-publish-from-power-bi-desktop).

    -   Um ein neues Dashboard zu erstellen, klicken Sie im linken Bereich neben dem Abschnitt **Dashboards** auf das Symbol **+**. Geben Sie für dieses neue Dashboard den Namen „Demo für vorbeugende Wartung“ ein.

    -   Wenn Sie den Bericht geöffnet haben, klicken Sie auf ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-pin.png), um alle Visualisierungen an Ihr Dashboard anzuheften. Ausführliche Informationen finden Sie unter [Anheften einer Kachel an ein Power BI-Dashboard aus einem Bericht](https://support.powerbi.com/knowledgebase/articles/430323-pin-a-tile-to-a-power-bi-dashboard-from-a-report). Wechseln Sie zur Dashboardseite, passen Sie die Größe und Position der Visualisierungen an, und bearbeiten Sie deren Titel. Ausführliche Anweisungen zum Bearbeiten der Titel finden Sie unter [Bearbeiten einer Kachel – Größe ändern, verschieben, umbenennen, anheften, löschen, Link hinzufügen](https://powerbi.microsoft.com/documentation/powerbi-service-edit-a-tile-in-a-dashboard/#rename) Nachfolgend sehen Sie ein Beispieldashboard, an das einige Visualisierungen für kalte Daten angeheftet sind. Abhängig davon, wie lange Sie Ihren Datensimulator ausführen, unterscheiden sich die Zahlen in Ihren Visualisierungen unter Umständen. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\final-view.png) <br/>
    -   Um eine Aktualisierung der Daten zu planen, zeigen Sie mit dem Mauszeiger auf das Dataset **PredictiveMaintenanceAerospace**, klicken auf ![](media\cortana-analytics-technical-guide-predictive-maintenance\icon-elipsis.png) und wählen dann **Aktualisierung planen**. <br/> **Hinweis:** Wenn eine Warnmeldung angezeigt wird, klicken Sie auf **Anmeldeinformationen bearbeiten** und stellen sicher, dass die Anmeldeinformationen für die Datenbank identisch mit den in Schritt 1 angegebenen sind. <br/> ![](media\cortana-analytics-technical-guide-predictive-maintenance\schedule-refresh.png) <br/>
    -   Erweitern Sie den Abschnitt **Aktualisierung planen**. Aktivieren Sie „Halten Sie Ihre Daten aktuell“. <br/>
    -   Legen Sie einen geeigneten Zeitplan für die Aktualisierung fest. Weitere Informationen finden Sie unter [Aktualisieren von Daten in Power BI](https://support.powerbi.com/knowledgebase/articles/474669-data-refresh-in-power-bi).

### Einrichten des Dashboards für heiße Daten

In den folgenden Schritten wird erklärt, wie Sie die Echtzeit-Datenausgabe aus den beim Bereitstellen der Lösung generierten Stream Analytics-Aufträgen visualisieren. Zum Ausführen der folgenden Schritte benötigen Sie ein Konto für [Power BI online](http://www.powerbi.com/). Wenn Sie über kein Konto verfügen, können Sie [ein solches erstellen](https://powerbi.microsoft.com/pricing).

1.  Fügen Sie die Power BI-Ausgabe in Azure Stream Analytics hinzu.

    -  Sie müssen die Anweisungen unter [Azure Stream Analytics und Power BI: Ein Dashboard mit Echtzeitanalyse und -sichtbarkeit von Streamingdaten](stream-analytics-power-bi-dashboard.md) ausführen, um die Ausgabe des Azure Stream Analytics-Auftrags an Ihr Power BI-Dashboard einzurichten.
	- Suchen Sie im [Azure-Portal](https://manage.windowsazure.com) den Stream Analytics-Auftrag **maintenancesa02asapbi**.
	- Richten Sie die drei Ausgaben der Azure Stream Analytics-Abfrage ein. Dies sind **aircraftmonitor**, **aircraftalert** und **flightsbyhour**. Stellen Sie sicher, dass der **Ausgabealias**, der **Datasetname** und der **Tabellenname** mit den Angaben in der Abfrage übereinstimmen (**aircraftmonitor**, **aircraftalert** und **flightsbyhour**). Nachdem Sie alle drei Ausgabetabellen hinzugefügt und den Stream Analytics-Auftrag gestartet haben, sollte eine Bestätigungsmeldung angezeigt werden (*z. B.* „Das Starten des Stream Analytics-Auftrags „maintenancesa02asapbi“ war erfolgreich.“)

2. Melden Sie sich bei [Power BI online](http://www.powerbi.com) an.

    -   Unter „Mein Arbeitsbereich“ sollten Sie im linken Bereich im Abschnitt „Datasets“ die ***DATASET***-Namen **aircraftmonitor**, **aircraftalert** und **flightsbyhour** angezeigt werden, die Sie zuvor in den Power BI-Ausgabeeinstellungen des Azure Stream Analytics-Auftrags definiert haben.

    -   Stellen Sie sicher, dass der Bereich ***Visualisierungen*** geöffnet ist und auf der rechten Seite des Bildschirms angezeigt wird.

3. Erstellen Sie die Kachel „Flottenansicht von Sensor 11 im Vergleich zu Schwellenwert 48,26“:

    -   Klicken Sie im linken Bereich im Abschnitt „Datasets“ auf das Dataset **aircraftmonitor**.

    -   Klicken Sie auf das Symbol **Liniendiagramm**.

    -   Klicken Sie im Bereich **Felder** auf **Verarbeitet**, sodass das Feld im Bereich **Visualisierungen** unter „Achse“ angezeigt wird.

    -   Klicken Sie auf „s11“ und „s11\_alert“, sodass beide unter „Werte“ angezeigt werden. Klicken Sie auf den kleinen Pfeil neben **s11** und **s11\_alert**, und ändern Sie „Summe“ in „Durchschnitt“.

    -   Klicken Sie oben auf **SPEICHERN**, und nennen Sie den Bericht „Flugzeugmonitor“. Der Bericht „Flugzeugmonitor“ wird im links im Bereich **Navigator** im Abschnitt **Berichte** angezeigt.

    -   Klicken Sie in der oberen rechten Ecke dieses Liniendiagramms auf **Visualisierung anheften**. Möglicherweise wird ein Fenster „An das Dashboard anheften“ angezeigt, in dem Sie ein Dashboard auswählen können. Wählen Sie „Demo für vorbeugende Wartung“ aus, und klicken Sie dann auf „Anheften“.

    -   Zeigen Sie mit dem Mauszeiger auf diese Kachel auf dem Dashboard, klicken Sie in der oberen rechten Ecke auf das Symbol „Bearbeiten“, und ändern Sie den Titel in „Flottenansicht von Sensor 11 im Vergleich zu Schwellenwert 48,26“ und den Untertitel in „Flottendurchschnitt im Zeitverlauf“.

4.  Erstellen Sie weitere Dashboardkacheln auf Basis der entsprechenden Datasets. Nachfolgend sehen Sie ein Beispieldashboard, an das einige Visualisierungen für heiße Daten angeheftet sind. Abhängig davon, wie lange Sie Ihren Datensimulator ausführen, unterscheiden sich die Zahlen in Ihren Visualisierungen unter Umständen.

    ![](media\cortana-analytics-technical-guide-predictive-maintenance\dashboard-view.png)

## **Kostenschätzungstools**

Es stehen zwei Tools zur Verfügung, mit denen Sie die Gesamtkosten zum Ausführen der Lösungsvorlage für vorbeugende Wartung in der Luft- und Raumfahrt im Rahmen Ihres Abonnements ermitteln können:

-   [Microsoft Azure-Kostenschätzungstool (online)](https://azure.microsoft.com/pricing/calculator/)

-   [Microsoft Azure-Kostenschätzungstool (Desktopversion)](http://www.microsoft.com/download/details.aspx?id=43376)

<!---HONumber=AcomDC_0128_2016-->