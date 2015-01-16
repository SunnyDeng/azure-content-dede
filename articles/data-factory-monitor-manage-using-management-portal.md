<properties title="Monitor and manage Azure Data Factory using Azure Preview Portal" pageTitle="Überwachen und Verwalten von Azure Data Factory mit dem Azure-Vorschauportal" description="Erfahren Sie, wie Sie von Ihnen erstellte Azure Data Factories mithilfe des Azure-Verwaltungsportals überwachen und verwalten." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Überwachen von Azure Data Factory mit dem Azure-Vorschauportal

- [Anzeigen aller Data Factorys in einem Azure-Abonnement](#AllDataFactories)
- [Anzeigen von Details zu einer Data Factory](#DataFactoryDetails)
- [Anzeigen der Diagrammansicht einer Data Factory](#DataFactoryDiagram)
- [Anzeigen von verknüpften Diensten in einer Data Factory](#DataFactoryLinkedServices)
- [Anzeigen von Details zu einem verknüpften Dienst](#DataFactoryLinkedService) 
- [Anzeigen von Datasets in einer Data Factory](#DataFactoryDatasets)
- [Anzeigen von Details zu einem Dataset](#DataFactoryDataset)
- [Anzeigen von Details zu einem Slice](#DataFactorySlice) 
- [Anzeigen aller Aktivitätsausführungen für einen Slice](#DataFactoryActivtyRuns) 
- [Anzeigen von Details zu einer Aktivitätsausführung](#DataFactoryActivtyRunDetails)
- [Operations-Bereich - Ereignisse der letzten Woche](#EventsInThePastweek)  
   


## <a name="AllDataFactories"></a> Anzeigen aller Data Factorys in einem Azure-Abonnement

- Melden Sie sich beim [Azure-Vorschauportal][azure-preview-portal] an.
- Klicken Sie auf den **BROWSE**-Hub links, und klicken Sie auf **Data Factorys**.  

	![BROWSE hub -> Data Factories][image-data-factory-browse-datafactories]

	Wenn keine **Data Factorys** angezeigt werden, klicken Sie auf **Alles**, und dann auf **Data Factorys** im Fenster **Durchsuchen**.

	![BROWSE hub -> Everything] [image-data-factory-browse-everything]

- Es sollten alle Data Factorys im Fenster **Data Factorys** angezeigt werden.

	![Data factories blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Anzeigen von Details zu einer Data Factory

Führen Sie einen der folgenden Schritte aus, um die Details zu einer Data Factory anzuzeigen: 


- Klicken Sie im oben dargestellten Fenster **Data Factorys** auf eine Data Factory.
- Klicken Sie auf dem **Startmenü** auf den Link für die Data Factory. **Startmenü** ist das Fenster, das angezeigt wird, wenn Sie sich beim Azure-Vorschauportal anmelden. Wenn Sie **Zum Startmenü hinzufügen** während der Erstellung einer Data Factory ausgewählt hatten (Standardoption), wird der Data Factory-Link auf dem Startmenü, wie im folgenden Bild dargestellt, angezeigt. In diesem Beispielsind die Data Factory-Links **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** und **LogProcessingFactory** auf dem **Startmenü** verfügbar.


![Data factory from the Startboard][image-data-factory-datafactory-from-startboard]

Es wird auf jeden Fall das Fenster **Data Factory** für die ausgewählte Data Factory angezeigt, wie im folgenden Bild dargestellt. 

 ![Data Factory Home Page][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Anzeigen der Diagrammansicht der Data Factory
Klicken Sie im Fenster **Data Factory** für die Data Factory auf die **Diagramm**-Kachel, um die Diagrammansicht der Data Factory anzuzeigen. 

![Data Factory Diagram View][image-data-factory-diagram-view]
 

## <a name="DataFactoryLinkedServices"></a> Anzeigen von verknüpften Diensten in einer Data Factory
Klicken Sie im Fenster **Data Factory** für die Data Factory auf die Kachel **Verknüpfte Dienste**, um alle verknüpften Dienste in einer Liste anzuzeigen. 

![Linked Services Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Anzeigen von Details zu einem verknüpften Dienst
Klicken Sie im Fenster **VERKNÜPFTE DIENSTE** in der Liste auf den verknüpften Dienst, zu dem Sie Details anzeigen möchten. 

![Linked Service Blade][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Anzeigen von Datasets in einer Data Factory 
Klicken Sie im Fenster **Data Factory** für die Data Factory auf die **Datasets**-Kachel, um alle Tabellen in der Data Factory anzuzeigen.

![Data Sets Blade][image-data-factory-datasets] 

## <a name="DataFactoryDataset"></a>  Anzeigen von Details zu einem Dataset
Klicken Sie in der Liste von Datasets im Fenster "DATASETS" auf das Dataset, um Details zu dem Dataset anzuzeigen. Beachten Sie, dass eine Tabelle ein rechteckiges Dataset mit einem Schema ist. Dies ist die einzige Art von Datasets, die derzeit unterstützt werden. 

![Table Blade][image-data-factory-table]

Im Fenster **TABELLE** oben werden sowohl **Zuletzt verwendete Slices** als auch **Problemslices** angezeigt. Klicken Sie auf **... (Ellipse)**, um alle Slices anzuzeigen. 

![All Slices of a Table][image-data-factory-all-slices]

Im Fenster **Datenslices** können Sie einen Filter verwenden, um die spezifischen Slices anzuzeigen, die Sie überprüfen möchten.


## <a name="DataFactorySlice"></a> Anzeigen von Details zu einem Slice
Klicken Sie in der Sliceliste im Fenster **TABELLE** oder im Fenster **Datenslices** auf einen Slice, um Details dazu anzuzeigen. 

![Data Slice][image-data-factory-dataslice]


### <a name="DataFactoryActivtyRuns"></a> Anzeigen aller Aktivitätsausführungen für einen Slice
Für einen Slice kann es mehrere Ausführungen geben. Wenn bei einem Slice beispielsweise ein Fehler auftritt, kann der Dienst die Ausführung einige Male wiederholen. Sie können einen Slice, bei dem alle Wiederholungsversuche fehlgeschlagen sind, auch erneut ausführen. Im Fenster **Datenslice** können Sie in der Liste unten alle Aktivitätsausführungen sehen. 

## <a name="DataFactoryActivtyRunDetails"></a>  Anzeigen von Details zu einer Aktivitätsausführung
Klicken Sie im Fenster **Datenslice** in der Liste der Ausführungen auf eine Aktivitätsausführung, um Details zu der Aktivitätsausführung anzuzeigen. 

![Activity Run Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Operations-Bereich - Ereignisse der letzten Woche
Klicken Sie im Fenster **Data Factory** (oder auf der Startseite) der Data Factory auf **Ereignisse der letzten Woche** im Bereich **Operations**, um die Ereignisse der letzten Woche anzuzeigen. Dadurch erhalten einen allgemeinen Überblick über die Vorgänge, die von der Data Factory in der letzten Woche ausgeführt wurden. Außerdem können Sie so auch Fehler im Zusammenhang mit der Datenverarbeitung/-verschiebung beheben. 

![ Data Factory Events][image-data-factory-events]


## Weitere Informationen

Artikel | Beschreibung
------ | ---------------
[Überwachen und Verwalten von Azure Data Factory mithilfe von PowerShell][monitor-manage-using-powershell] | In diesem Artikel wird beschrieben, wie eine Azure Data Factory mithilfe von Azure PowerShell-Cmdlets überwacht wird. 
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-BLOB.
[Verwenden von Pig und Hive mit Data Factory][use-pig-and-hive-with-data-factory] | Dieser Artikel enthält eine exemplarische Vorgehensweise, in der gezeigt wird, wie eine HDInsight-Aktivität verwendet wird, um ein Hive-/Pig-Skript zur Verarbeitung von Eingabedaten auszuführen, um Ausgabedaten zu produzieren. 
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | Dieser Artikel enthält eine umfassende exemplarische Vorgehensweise, in der gezeigt wird, wie ein realitätsnahes Szenario mit Azure Data Factory implementiert wird, um Daten aus Protokolldateien in Einblicke zu transformieren.
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und deren Verwendung in einer Pipeline. 
[Problembehandlung für Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Probleme in Azure Data Factory behoben werden.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 
[Azure Data Factory-Cmdlet-Referenz][cmdlet-reference] | Diese Referenz enthält Einzelheiten zu allen **Data Factory-Cmdlets**.


[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-browse-everything]: ./media/data-factory-monitor-manage-using-management-portal/BrowseEverything.png

[image-data-factory-browse-datafactories]: ./media/data-factory-monitor-manage-using-management-portal/BrowseDataFactories.png

[image-data-factory-datafactories-blade]: ./media/data-factory-monitor-manage-using-management-portal/DataFactories.png

[image-data-factory-datafactory-from-startboard]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryFromStartboard.png

[image-data-factory-datafactory-home-page]: ./media/data-factory-monitor-manage-using-management-portal/DataFactoryHomePage.png

[image-data-factory-diagram-view]: ./media/data-factory-monitor-manage-using-management-portal/DiagramView.png

[image-data-factory-linked-services]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServicesBlade.png

[image-data-factory-linked-service]: ./media/data-factory-monitor-manage-using-management-portal/LinkedServiceBlade.png

[image-data-factory-datasets]: ./media/data-factory-monitor-manage-using-management-portal/Datasets.png

[image-data-factory-table]: ./media/data-factory-monitor-manage-using-management-portal/Table.png

[image-data-factory-all-slices]: ./media/data-factory-monitor-manage-using-management-portal/AllSlices.png

[image-data-factory-filter]: ./media/data-factory-monitor-manage-using-management-portal/Filter.png

[image-data-factory-dataslice]: ./media/data-factory-monitor-manage-using-management-portal/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-monitor-manage-using-management-portal/ActivityRunDetails.png

[image-data-factory-events]: ./media/data-factory-monitor-manage-using-management-portal/Events.png
