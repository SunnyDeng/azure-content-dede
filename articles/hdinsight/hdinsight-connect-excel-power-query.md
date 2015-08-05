<properties
	pageTitle="Verbinden von Excel mit Hadoop mithilfe von Power Query | Microsoft Azure"
	description="Erfahren Sie, wie Sie Business Intelligence-Komponenten nutzen und wie Sie mithilfe von Power Query für Excel auf Daten in Hadoop in HDInsight zugreifen können."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>


#Verbinden von Excel mit Hadoop mithilfe von Power Query

Eine der wichtigsten Features der Big Data-Lösung von Microsoft ist die Integration von Microsoft Business Intelligence (BI)-Komponenten in Hadoop-Cluster in Azure HDInsight. Ein gutes Beispiel für diese Integration ist die Möglichkeit, Excel mithilfe des Microsoft Power Query für Excel-Add-Ins mit dem Azure-Speicherkonto zu verbinden, das die dem Hadoop-Cluster zugeordneten Daten enthält. Dieser Artikel beschreibt die Einrichtung und Verwendung von Power Query für die Abfrage von Daten aus einem mit HDInsight verwalteten Hadoop-Cluster.

> [AZURE.NOTE]Auch wenn die in diesem Artikel beschriebenen Schritte für Linux- und Windows-basierte HDInsight-Cluster gelten, ist für den Client in jedem Fall eine Windows-Arbeitsstation erforderlich.

## Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Einen HDInsight-Cluster**. Hinweise zu dessen Konfiguration finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started].
- **Eine Arbeitsstation** mit Windows 7, Windows Server 2008 R2 oder einem späteren Betriebssystem.
- **Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone oder Office 2010 Professional Plus**.


## <a id="InstallPowerQuery"></a>Installieren von Microsoft Power Query für Excel

Power Query kann zum Importieren von Daten aus verschiedensten Quellen in Excel verwendet werden, um diese in Business Intelligence (BI)-Tools wie PowerPivot und Power View zu verwenden. Insbesondere Power Query kann Daten importieren, die von einer Hadoop-Aufgabe in einem HDInsight-Cluster ausgegeben bzw. generiert wurden.

Laden Sie Microsoft Power Query für Excel von [Microsoft Download Center][powerquery-download] herunter, und installieren Sie es.

## <a id="ImportData"></a>Importieren von HDInsight-Daten in Excel

Das Power Query für Excel-Add-In erleichtert den Import von Daten aus Ihrem HDInsight-Cluster in Excel. Business Intelligence-Tools wie z. B. PowerPivot und Power Map dienen zur Inspektion, Analyse und Darstellung der Daten.

**So importieren Sie Daten aus einem HDInsight-Cluster**

1. Öffnen Sie Excel.

2. Erstellen Sie eine neue leere Arbeitsmappe.

3. Klicken Sie auf das Menü **Power Query**, klicken Sie auf **Von anderen Quellen**, und klicken Sie dann auf **Von Azure HDInsight**.

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Hinweis: Falls das **Power Query**-Menü nicht angezeigt wird, öffnen Sie **Datei** > **Optionen** > **Add-Ins** und wählen Sie **COM-Add-Ins ** im Dropdownfeld **Manager** im unteren Bereich der Seite aus. Klicken Sie auf die Schaltfläche **Los...**, und vergewissern Sie sich, dass das Optionsfeld des Power Query für Excel-Add-Ins markiert ist.

3. Geben Sie den **Kontonamen** des mit Ihrem Cluster verbundenen Azure-Blobspeicherkontos ein, und klicken Sie dann auf **OK**.

4. Geben Sie den **Kontoschlüssel** für das Blobspeicherkonto ein, und klicken Sie dann auf **Speichern**. (Sie müssen diesen Schritt nur beim ersten Zugriff auf diesen Speicher ausführen.)

5. Doppelklicken Sie im **Navigationsbereich** links vom Abfrage-Editor auf den Namen des Blobspeichercontainers. Der Containername ist standardmäßig derselbe Name wie der Clustername.

6. Suchen Sie **HiveSampleData.txt** in der Spalte **Name** (der Pfad zum Ordner ist **../hive/warehouse/hivesampletable/**) und klicken Sie auf **Binär** links von HiveSampleData.txt.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Bei Bedarf können Sie die Spaltennamen ändern. Klicken Sie auf **Übernehmen & schließen**, wenn Sie fertig sind.

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Daten aus HDInsight mithilfe von Power Query in Excel verwenden können. Auf ähnliche Weise können Sie Daten aus HDInsight für die Azure SQL-Datenbank abrufen. Außerdem können Sie Daten nach HDInsight hochladen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verbinden von Excel mit HDInsight mithilfe des Microsoft Hive ODBC-Treibers][hdinsight-ODBC]
* [Hochladen von Daten zu HDInsight][hdinsight-upload-data]

[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
 

<!---HONumber=July15_HO4-->