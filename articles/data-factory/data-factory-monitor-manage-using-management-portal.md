<properties 
	pageTitle="Überwachen und Verwalten von Azure Data Factory mit dem Azure-Vorschauportal" 
	description="Erfahren Sie, wie Sie von Ihnen erstellte Azure Data Factories mithilfe des Azure-Verwaltungsportals überwachen und verwalten." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/18/2015" 
	ms.author="spelluru"/>

# Überwachen von Azure Data Factory mit dem Azure-Vorschauportal
In diesem Artikel werden verschiedene Szenarien für die Verwendung des Azure-Vorschauportals zum Überwachen und Verwalten von Azure Data Factory beschrieben.

## <a name="AllDataFactories"></a> Anzeigen aller Data Factorys in einem Azure-Abonnement

- Melden Sie sich beim [Azure-Vorschauportal][azure-preview-portal] an.
- Klicken Sie links auf den Hub **DURCHSUCHEN** und dann auf **Data Factorys**.  

	![Hub DURCHSUCHEN -> Data Factorys][image-data-factory-browse-datafactories]

	Wenn keine **Data Factorys** angezeigt werden, klicken Sie auf **Alles** und dann auf dem Blatt **Durchsuchen** auf **Data Factorys**.

	![Hub DURCHSUCHEN -> Alles][image-data-factory-browse-everything]

- Auf dem Blatt **Data Factorys** sollten alle Data Factorys angezeigt sein.

	![Blatt "Data Factorys"][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Anzeigen von Details zu einer Data Factory

Führen Sie einen der folgenden Schritte aus, um die Details zu einer Data Factory anzuzeigen:


- Klicken Sie auf dem oben gezeigten Blatt **Data Factorys** auf eine Data Factory.
- Klicken Sie im **Startmenü** auf den Link der Data Factory. Das **Startmenü** ist das Blatt, das angezeigt wird, wenn Sie sich beim Azure-Vorschauportal anmelden. Wenn Sie während der Erstellung einer Data Factory **Zum Startmenü hinzufügen ** ausgewählt haben (Standardoption), wird der Data Factory-Link im Startmenü wie in der folgenden Abbildung angezeigt. In diesem Beispiel stehen im **Startmenü** die Data Factory-Links **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** und **LogProcessingFactory** zur Verfügung.


!["Data Factory" im Startmenü][image-data-factory-datafactory-from-startboard]

Es wird auf jeden Fall das Blatt **Data Factory** für die ausgewählte Data Factory angezeigt (siehe folgende Abbildung).

 ![Data Factory-Startseite][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Anzeigen der Diagrammansicht der Data Factory
Klicken Sie auf dem Blatt **Data Factory** für die Data Factory auf die Kachel **Diagramm**, um die Diagrammansicht der Data Factory anzuzeigen.

![Data Factory-Diagrammansicht][image-data-factory-diagram-view]
 
### Öffnen einer Pipeline in der Diagrammansicht
Sie können alle Aktivitäten in einer Pipeline anzeigen, indem Sie in der Diagrammansicht mit der rechten Maustaste auf die Pipeline klicken und dann auf **Pipeline öffnen** klicken. Es sollten die Aktivitäten in der Pipeline sowie Ein- und Ausgabedatasets für die Aktivitäten angezeigt werden.![Pipeline öffnen](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Klicken Sie auf der Breadcrumb-Leiste links oben auf **Data Factory**, um zur Diagrammansicht zurückzukehren. In der Diagrammansicht werden alle Pipelines angezeigt. In diesem Beispiel haben Sie nur eine Pipeline erstellt.

## <a name="DataFactoryLinkedServices"></a> Anzeigen von verknüpften Diensten in einer Data Factory
Klicken Sie auf dem Blatt **DATA FACTORY** für die Data Factory auf die Kachel **Verknüpfte Dienste**, um alle verknüpften Dienste in einer Liste anzuzeigen.

![Blatt "Verknüpfte Dienste"][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Anzeigen von Details zu einem verknüpften Dienst
Klicken Sie auf dem Blatt **VERKNÜPFTE DIENSTE** in der Liste auf den verknüpften Dienst, zu dem Sie Details anzeigen möchten.

![Blatt "Verknüpfter Dienst"][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Anzeigen von Datasets in einer Data Factory 
Klicken Sie auf dem Blatt **DATA FACTORY** für die Data Factory auf die Kachel **Datasets**, um alle Tabellen in der Data Factory anzuzeigen.

![Blatt "Datasets"][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Anzeigen von Details zu einem Dataset
Klicken Sie in der Liste von Datasets im Fenster "DATASETS" auf das Dataset, um Details zu dem Dataset anzuzeigen. Beachten Sie, dass eine Tabelle ein rechteckiges Dataset mit einem Schema ist. Dies ist die einzige Art von Datasets, die derzeit unterstützt werden.

![Blatt "Tabelle"][image-data-factory-table]

Auf dem Blatt **TABELLE** werden die Listen **Letzte aktualisierte Slices** und **Letzte fehlerhafte Slices** anhand der **UHRZEIT DER LETZTEN AKTUALISIERUNG** sortiert. Der Zeitpunkt der Aktualisierung eines Slices wird in den folgenden Situationen geändert.

-  Sie haben den Status eines Slices manuell aktualisiert, z. B. mit dem Cmdlet **Set AzureDataFactorySliceStatus** oder durch Klicken auf **Ausführen** auf dem Blatt **SLICE** des Slices.
-  Der Status des Slices ändert sich aufgrund einer Ausführung (z. B. Ausführung gestartet, Ausführung mit Fehler beendet, Ausführung erfolgreich beendet usw.).
 
	
Zum Anzeigen der nach Start-/Endzeit sortierten Datenslices klicken Sie auf die Kachel **Datenslices (nach Slicezeit)**.
 
![Datenslices nach Slicezeit][DataSlicesBySliceTime]

Klicken Sie auf den Titel der Listen oder auf **...** (Auslassungspunkte), um eine umfangreichere Liste mit Slices anzuzeigen.

![Alle Slices einer Tabelle][image-data-factory-all-slices]

Klicken Sie auf dem Blatt **Datenslices** auf die Schaltfläche **Filter**, um das Blatt **Filter** anzuzeigen, auf dem Sie Datenslices **filtern** können, um die zu überprüfenden Datenslices anzuzeigen. Blätter wie das folgende werden angezeigt, wenn Sie auf dem Blatt **Datenslices** mit **nach Aktualisierungszeitpunkt sortierten** Slices auf **Filter** klicken.

![Blatt "Filter"][image-data-factory-filter-blade]

Das Blatt **Filter** ermöglicht Ihnen das Filtern auf Grundlage des **Zeitpunkts der letzten Aktualisierung** und des **Slicestatus**. In der folgende Tabelle finden Sie alle Slicestatus samt Beschreibung.
 
Slicestatus | Beschreibung
------------ | ------------
Ausstehende Ausführung | Die Datenverarbeitung wurde noch nicht gestartet.
In Bearbeitung | Die Datenverarbeitung wird ausgeführt.
Bereit | Die Datenverarbeitung ist abgeschlossen, und der Datenslice ist bereit.
Fehler | Fehler bei der Ausführung, die den Slice erstellt.
Überspringen | Die Verarbeitung des Slices wird übersprungen.
Wiederholen | Erneuter Versuch der Ausführung, die den Slice erstellt.
Zeitüberschreitung | Zeitüberschreitung bei der Datenverarbeitung des Slices.
Ausstehende Überprüfung | Der Datenslice wartet auf Überprüfung im Hinblick auf Überprüfungsrichtlinien, bevor es verarbeitet wird.
RetryValidation | Die Überprüfung des Slices wird wiederholt.
FailedValidation | Fehler beim Überprüfen des Slices.
Lange Wiederholung | Ein Slice weist diesen Status auf, wenn „Lange Wiederholung“ in der JSON-Tabelle angegeben wird und reguläre Wiederholungen für den Slice fehlgeschlagen sind.
Überprüfung wird ausgeführt | Überprüfung des Slices (basierend auf den in der JSON-Tabelle definierten Richtlinien) wird ausgeführt.

Wenn Sie auf dem Blatt **Datenslices** mit **nach Slicezeit sortierten** Slices auf **Filter** klicken, wird ein anderer Typ des Blatts **Filter** angezeigt.

![2. Blatt "Filter"][image-data-factory-filter-blade-2]


Wenn Sie das Blatt **Filter** öffnen, wird für das Feld **Bis** automatisch (gerundet) der aktuellste Zeitpunkt festgelegt, um die Anzahl der zurückgegebenen Datensätze einzuschränken. Das Feld **Von** wird ebenfalls automatisch festgelegt. Sie können das Datum für **Von** ändern, indem Sie auf die Schaltfläche **Kalender** klicken. Das Datum für **Bis** wird automatisch geändert, wenn Sie das Datum für **Von** ändern.

Sie können auf die Schaltflächen **Zurück/Weiter** klicken, um Slices im vorherigen/nächsten Zeitraum anzuzeigen. Der Zeitraum für die Schaltflächen **Zurück** und **Weiter** wird auf Basis der Häufigkeit und des Intervalls für den Datenslice festgelegt, wie in der folgenden Tabelle gezeigt.

Häufigkeit | Wertebereich des Intervalls | Resultierender Zeitraum
----------| -------------------- | --------------------
Minute | 1-4 | 6 Stunden
Minute | 5-29 | 1 Tag
Minute | 30-180 | 7 Tage
Minute | Über 180 | 28 Tage (ungefähr ein Kalendermonat)
Stunde | 1-3 | 7 Tage
Stunde | 4-11 | 28 Tage (ungefähr ein Kalendermonat)
Stunde | 12-72 | 182 Tage (ungefähr 6 Monate
Stunde | Über 73 | 1 Jahr
Tag | 1-6 | 1 Jahr
Tag | 7-20 | 5 Jahre
Tag | Über 21 | 10 Jahre
Woche | 1-3 | 5 Jahre
Woche | Über 4 | 10 Jahre
Monat | beliebig | 10 Jahre
 
Wenn Sie für **Häufigkeit** z. B. **Stunde** und für **Intervall** den Wert **2** festlegen, wird durch Klicken auf die Schaltflächen **Weiter/Zurück** der Zeitbereich um **7 Tage** in die jeweilige Richtung verschoben. Diese Logik gilt unabhängig davon für das Fenster "Filter", ob Sie alle Slices, die letzten Slices bzw. Problemslices anzeigen.




## <a name="DataFactorySlice"></a> Anzeigen von Details zu einem Slice
Klicken Sie in der Sliceliste auf dem Blatt **TABELLE** oder auf dem Blatt **Datenslices** auf einen Slice, um Details dazu anzuzeigen.

![Datenslice][image-data-factory-dataslice]

Wenn der Slice nicht den Status **Bereit** hat, sehen Sie die vorgelagerten Slices, die nicht bereit sind und das Ausführen des aktuellen Slices blockieren, in der Liste **Vorgelagerte Slices, die nicht bereit sind**.

### <a name="DataFactoryActivtyRuns"></a> Anzeigen aller Aktivitätsausführungen für einen Slice
Für einen Slice kann es mehrere Ausführungen geben. Wenn bei einem Slice beispielsweise ein Fehler auftritt, kann der Dienst die Ausführung einige Male wiederholen. Sie können einen Slice, bei dem alle Wiederholungsversuche fehlgeschlagen sind, auch erneut ausführen. Auf dem Blatt **Datenslice** können Sie in der Liste unten alle Aktivitätsausführungen sehen.

## <a name="DataFactoryActivtyRunDetails"></a> Anzeigen von Details zu einer Aktivitätsausführung
Klicken Sie auf dem Blatt **Datenslice** in der Liste der Ausführungen auf eine Aktivitätsausführung, um Details zu der Aktivitätsausführung anzuzeigen.

![Aktivitätsauszuführung – Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Bereich "Vorgänge" – Ereignisse der letzten Woche
Klicken Sie auf dem Blatt **DATA FACTORY** (oder auf der Startseite) der Data Factory im Fokus **Vorgänge** auf **Ereignisse der letzten Woche **, um die Ereignisse der letzten Woche anzuzeigen. Dadurch erhalten einen allgemeinen Überblick über die Vorgänge, die von der Data Factory in der letzten Woche ausgeführt wurden. Außerdem können Sie so auch Fehler im Zusammenhang mit der Datenverarbeitung/-verschiebung beheben.

![Data Factory-Editor][image-data-factory-events]


## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Überwachen und Verwalten von Azure Data Factory mit Azure PowerShell][monitor-manage-using-powershell] | In diesem Artikel wird beschrieben, wie eine Azure Data Factory mithilfe von Azure PowerShell-Cmdlets überwacht wird. 


[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: http://portal.azure.com/

[image-data-factory-filter-blade]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade.png

[image-data-factory-filter-blade-2]: ./media/data-factory-monitor-manage-using-management-portal/FilterBlade2.png


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
[DataSlicesBySliceTime]: ./media/data-factory-monitor-manage-using-management-portal/DataSlicesBySliceTime.png

<!---HONumber=July15_HO4-->