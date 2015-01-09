<properties urlDisplayName="HDInsight and Excel" pageTitle="Verbinden von Excel über Power Query mit Hadoop | Azure" metaKeywords="hdinsight, excel, data explorer, hive excel, hdinsight excel, power query" description="Learn how to take advantage of business intelligence components and use Excel to access data stored in Azure HDInsight using Power Query." metaCanonical="" services="hdinsight" documentationCenter="" title="Connect Excel to Hadoop with Power Query" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />




#Verbinden von Excel über Power Query mit Hadoop

Eine Schlüsselfunktion der Big Data-Lösung von Microsoft ist die Integration von Microsoft Business Intelligence (BI)-Komponenten in Hadoop-Cluster in HDInsight. Ein wichtiges Beispiel für diese Integration ist die Möglichkeit, Excel mithilfe von Microsoft Power Query mit dem Azure-Speicherkonto zu verbinden, das die Daten für Ihren Hadoop-Cluster enthält. Dieser Artikel beschreibt die Einrichtung und Verwendung von Power Query in Excel für die Abfrage von Daten aus einem mit HDInsight verwalteten Hadoop-Cluster. 

**Voraussetzungen**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Einen HDInsight-Cluster. Hinweise zu dessen Konfiguration finden Sie unter [Erste Schritte mit Azure HDInsight][hdinsight-get-started].
- Einen Computer, auf dem Windows 8, Windows 7, Windows Server 2012 oder Windows Server 2008 R2 läuft.
- Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone oder Office 2010 Professional Plus.

## Themen in diesem Artikel

- [Installieren von Microsoft Power Query für Excel](#InstallPowerQuery)
- [Importieren von Daten in Excel](#ImportData)
- [Nächste Schritte](#NextSteps)


## <a id="InstallPowerQuery"></a>Installieren von Microsoft Power Query für Excel

Power Query kann zum Importieren von Daten aus verschiedensten Quellen in Excel verwendet werden, um diese in Business Intelligence (BI)-Tools wie PowerPivot und Power View zu verwenden. Insbesondere Power Query kann Daten importieren, die von einer Hadoop-Aufgabe in einem HDInsight-Cluster ausgegeben bzw. generiert wurden. 

Laden Sie Microsoft Power Query für Excel aus dem [Microsoft Download Center][powerquery-download] herunter, und installieren Sie es.

## <a id="ImportData"></a>Importieren von HDInsight-Daten in Excel

Das Power Query-Add-In für Excel erleichtert den Import von Daten aus Ihrem HDInsight-Cluster in Excel. Business Intelligence-Tools wie z. B. PowerPivot und Power Map dienen zur Inspektion, Analyse und Darstellung der Daten.

**Importieren von Daten aus einem HDInsight-Cluster**

1. Öffnen Sie Excel.

2. Erstellen Sie eine neue leere Arbeitsmappe.

3. Klicken Sie auf das Menü **Power Query**, dann auf **Von anderen Quellen** und schließlich auf **Von Azure HDInsight**. 

	![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]

	Hinweis: Falls das **Power Query**-Menü nicht angezeigt wird, öffnen Sie **Datei** > **Optionen** > **Add-Ins**, und wählen Sie **COM-Add-Ins** im Dropdownfeld **Manager** im unteren Bereich der Seite aus. Klicken Sie auf die Schaltfläche **Los...**, und vergewissern Sie sich, dass das Optionsfeld des Microsoft Office Power Query für Excel-Add-In markiert ist.

3. Geben Sie den **Kontonamen** des Ihrem Cluster zugeordneten Azure Blob-Speicherkontos ein, und klicken Sie dann auf **OK**. 

4. Geben Sie den **Kontoschlüssel** für das Blob-Speicherkonto ein, und klicken Sie auf **Speichern**. (Sie müssen diesen Schritt nur beim ersten Zugriff auf diesen Speicher ausführen.)	

5. Doppelklicken Sie im **Navigationsbereich** links vom **Abfrage-Editor** auf den Namen des Blob-Speichercontainers. Der Container hat standardmäßig denselben Name wie der Cluster. 

6. Suchen Sie **HiveSampleData.txt** in der Spalte **Name** (der Pfad zum Ordner ist **../hive/warehouse/hivesampletable/**), und klicken Sie dann links von HiveSampleData.txt auf **Binär**.

	![HDI.PowerQuery.ImportData][image-hdi-powerquery-importdata]

7. Bei Bedarf können Sie die Spaltennamen ändern. Klicken Sie auf **Übernehmen & schließen**, wenn Sie fertig sind.	

	![HDI.PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a id="NextSteps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Daten aus HDInsight mithilfe von Power Query in Excel verwenden können. Sie können Daten auf ähnliche Weise aus HDInsight nach SQL Azure abrufen. Außerdem können Sie Daten nach HDInsight hochladen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][hdinsight-ODBC]
* [Hochladen von Daten zu HDInsight][hdinsight-upload-data].

[hdinsight-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[image-hdi-powerquery-hdi-source]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.SelectHdiSource.png
[image-hdi-powerquery-importdata]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportData.png 
[image-hdi-powerquery-imported-table]: ./media/hdinsight-connect-excel-power-query/HDI.PowerQuery.ImportedTable.PNG 

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689 
\n<!--HONumber=35.1-->
