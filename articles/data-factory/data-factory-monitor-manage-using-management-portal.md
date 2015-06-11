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
Dieser Artikel beschreibt verschiedene Szenarien für die Verwendung von Azure-Vorschauportal überwachen und Verwalten von Azure Data-Factory.

## <a name="AllDataFactories"></a> Anzeigen von Factorys für alle Daten in einem Azure-Abonnement

- Melden Sie sich bei der [Azure Preview Portal][azure-preview-portal].
- Klicken Sie auf **Durchsuchen** Hub auf der linken Seite, und klicken Sie auf **Daten Factorys**.  

	![Durchsuchen von Hub-Daten Factorys >][image-data-factory-browse-datafactories]

	Wenn Sie nicht sehen, **Daten Factorys**, klicken Sie auf **Alles** und klicken Sie dann auf **Daten Factorries** in den **Durchsuchen** Blade.

	![Alles durchsuchen Hub ->][image-data-factory-browse-everything]

- Daraufhin sollte alle Factorys für die Daten in der **Daten Factorys** Blade.

	![Daten-Factorys-blade][image-data-factory-datafactories-blade]

    
## <a name="DataFactoryDetails"></a> Anzeigen von Details zu einem Daten-factory

Führen Sie einen der folgenden Schritte aus, um die Details zu einer Data Factory anzuzeigen:


- Klicken Sie auf eine Factory für Daten in der **Daten Factorys** Blade oben gezeigten.
- Klicken Sie auf den Link für die Factory Daten auf den **Startboard**. **Startboard** wird im Fenster angezeigt, wenn Sie in das Azure Preview Portal anmelden. Wenn Sie ausgewählt hatten **zum Startmenü hinzufügen** beim Erstellen einer Factory Daten (Standardoption), sollte die Factory Daten auf das Startmenü eine Verknüpfung, wie in der folgenden Abbildung dargestellt. In diesem Beispiel **ADFTutorialDataFactory**, **ADFTutorialDataFactoryDF** und **LogProcessingFactory** Factory Datenverbindungen stehen auf der **Startboard**.


!["Data Factory" aus dem Startmenü][image-data-factory-datafactory-from-startboard]

In beiden Fällen sehen Sie die **DATA FACTORY** Blade für die ausgewählten Daten-Factory, wie im folgenden Bild gezeigt.

 ![Daten-Factory-Homepage][image-data-factory-datafactory-home-page]
 
## <a name="DataFactoryDiagram"></a> Diagrammansicht der Factory Daten anzeigen
In der **DATA FACTORY** Blade für die Factory Daten klicken Sie auf **Diagramm** Kachel aus, um die Diagrammansicht der Factory Daten finden Sie unter.

![Daten-Factory-Diagrammansicht][image-data-factory-diagram-view]
 
### Öffnen Sie eine Pipeline in der Diagrammansicht
Sie können alle Aktivitäten in einer Pipeline anzeigen, indem Sie mit der rechten Maustaste in der Pipeline in der Diagrammansicht und auf **offene Pipeline**. Die Aktivitäten in der Pipeline sowie Eingabe- und Datasets für die Aktivitäten sollte angezeigt werden. ![Offene pipeline](./media/data-factory-monitor-manage-using-management-portal/DiagramView-OpenPipeline.png)

Klicken Sie auf **Data Factory** in die Breadcrumb-Leiste in der oberen linken Ecke, um die Diagrammansicht zurückzukehren. Die Diagrammansicht zeigt alle Leitungen. In diesem Beispiel haben Sie nur eine Pipeline erstellt.

## <a name="DataFactoryLinkedServices"></a> Anzeigen von verknüpften Dienste in einer Fabrik Daten
In der **DATA FACTORY** Blade für die Factory Daten klicken Sie auf **verknüpften Dienste** Kachel aus, um alle verknüpften Dienste in einer Liste angezeigt werden.

![Verknüpfte Dienste Blade][image-data-factory-linked-services]

## <a name="DataFactoryLinkedService"></a> Anzeigen von Details zu einem verknüpften Dienst
In der **verknüpften Dienste** Blade, klicken Sie auf der verknüpfte Dienst aus der Liste finden details zu.

![Verknüpfte Service-Karte][image-data-factory-linked-service]

## <a name="DataFactoryDatasets"></a> Datasets in einer Fabrik Daten anzeigen 
In der **DATA FACTORY** Blade für die Factory Daten klicken Sie auf **Datasets** Kachel aus, um alle Tabellen in der Factory Daten anzuzeigen.

![Datasets Blade][image-data-factory-datasets]

## <a name="DataFactoryDataset"></a> Anzeigen von Details zu einem dataset
Klicken Sie in der Liste von Datasets im Fenster "DATASETS" auf das Dataset, um Details zu dem Dataset anzuzeigen. Beachten Sie, dass eine Tabelle ein rechteckiges Dataset mit einem Schema ist. Dies ist die einzige Art von Datasets, die derzeit unterstützt werden.

![Tabelle Blade][image-data-factory-table]

In der **Tabelle** Blade über beide **kürzlich aktualisierte Segmente** und **Slices einer vor kurzem fehlschlug** Listen sortiert die **Zeit der letzten Aktualisierung**. Der Zeitpunkt des Updates eines Segments wird in den folgenden Situationen geändert.

-  Sie den Status eines Segments manuell aktualisieren, z. B. mit der **Set AzureDataFactorySliceStatus** (oder), indem Sie auf **Ausführen** auf der **SLICE** Blade für das Segment.
-  Das Segment ändert sich der Status aufgrund einer Ausführung (z. B. eine Ausführung gestartet, eine Ausführung beendet wurde und fehlgeschlagen ist, eine Ausführung beendet wurde und erfolgreich war, usw.).
 
	
Klicken Sie zum Anzeigen der Datenslices sortiert nach den Slice Start-/Endzeiten stattdessen **Datenslices (von Slice-Zeit)** Kachel.
 
![Datenslices Slice Zeit][DataSlicesBySliceTime]

Klicken Sie auf den Titel der Listen oder **... (Ellipsen)** die Liste die größere Segmente anzeigen zu können.

![Alle Segmente einer Tabelle][image-data-factory-all-slices]

Auf der **Datenslices** Blade, klicken Sie auf die **Filter** Schaltfläche finden Sie unter der **Filter** Blade, dessen kann **Filter** Segmente auf die Segmente, die Sie überprüfen möchten, finden Sie unter. Blades ähnlich der folgenden wird angezeigt, wenn Sie auf **Filter** auf der **Datenslices** Blade mit Slices **sortiert nach Aktualisierungszeitpunkt**.

![Filter-Blade][image-data-factory-filter-blade]

Die **Filter** Blade können Sie filtern auf der Grundlage **Zeitpunkt des letzten Updates** und **slice Status**. Die folgende Tabelle beschreibt alle Slice-Status und der zugehörigen Beschreibung.
 
Slice-status | Beschreibung
------------ | ------------
Ausstehende Ausführung | Die Datenverarbeitung wurde noch nicht gestartet.
In Bearbeitung | Die Datenverarbeitung wird ausgeführt.
Bereit | Die Datenverarbeitung ist abgeschlossen, und der Datenslice ist bereit.
Fehler | Fehler bei der Ausführung, die den Slice erstellt.
Überspringen | Die Verarbeitung des Slices wird übersprungen.
Wiederholen | Erneuter Versuch der Ausführung, die den Slice erstellt.
Zeitüberschreitung | Zeitüberschreitung bei der Datenverarbeitung des Slices.
Ausstehende Überprüfung | Der Datenslice wartet auf Überprüfung im Hinblick auf Überprüfungsrichtlinien, bevor es verarbeitet wird.
RetryValidation | Wiederholen die Überprüfung des Segments.
FailedValidation | Fehler beim Überprüfen des Slices.
Lange Wiederholung | Ein Slice weist diesen Status auf, wenn „Lange Wiederholung“ in der JSON-Tabelle angegeben wird und reguläre Wiederholungen für den Slice fehlgeschlagen sind.
Überprüfung wird ausgeführt | Überprüfung des Slices (basierend auf den in der JSON-Tabelle definierten Richtlinien) wird ausgeführt.

Beim Klicken auf **Filter** auf auf die **Datenslices** Blade mit Slices **sortiert nach der Zeit Slice**, sehen Sie eine andere Art von **Filter** Blade.

![Filter-Blade 2][image-data-factory-filter-blade-2]


Wenn Sie starten die **Filter** Blade, der **auf** Feld wird automatisch festgelegt, um den letzten Zeitpunkt (gerundet), um die Anzahl der zurückgegebenen Datensätze beschränken. Die **von** Feld wird ebenfalls automatisch festgelegt. Sie können ändern, die **aus** Datum, indem Sie auf die **Kalender** Schaltfläche. Die  Datum wird automatisch geändert, wenn Sie ändern die **von** Datum.

Klicken Sie auf **vorherige**/ ** weiter ** Schaltflächen anzeigen slices in der vorherigen Periode/nächsten Zeitraum. Der Zeitraum für **vorherige** und **Weiter** Schaltflächen festgelegt werden, auf die Slice-Häufigkeit und das Intervall, wie in der folgenden Tabelle dargestellt.

Häufigkeit | Intervall Wertebereich | Resultierende Uhrzeit-Block
----------| -------------------- | --------------------
Minute | 1 bis 4 | 6 Stunden
Minute | 5 bis 29 | 1 Tag
Minute | 30 – 180 | 7 Tage
Minute | 180 + | 28 Tage (ungefähre. Kalendermonats)
Stunde | 1 bis 3 | 7 Tage
Stunde | 4-11 | 28 Tage (ungefähre. Kalendermonats)
Stunde | 12 bis 72 | 182 Tage (ca.. 6 Monate
Stunde | 73 + | 1 Jahr
Tag | 1 bis 6 | 1 Jahr
Tag | 7-20 | fünf Jahre
Tag | 21 + | 10 Jahre
Woche | 1 bis 3 | fünf Jahre
Woche | 4 + | 10 Jahre
Monat | alle | 10 Jahre
 
Angenommen, Sie definieren **Häufigkeit** als **Stunde** und **Intervall** von **2**, klicken Sie auf die **Weiter**/ ** zurück ** Schaltflächen Verschieben des Zeitbereichs **7 Tage** in beide Richtungen. Diese Logik gilt unabhängig davon für das Fenster "Filter", ob Sie alle Slices, die letzten Slices bzw. Problemslices anzeigen.




## <a name="DataFactorySlice"></a> Anzeigen von Details zu einem slice
Klicken Sie auf ein Segment in der Liste der Segmente entweder auf die **Tabelle** Blade oder **Datenslices** Blade über dieses Segment angezeigt.

![Datenslices][image-data-factory-dataslice]

Wenn das Segment nicht in die **bereit** Status, sehen Sie die upstream Segmente, die nicht bereit sind, und blockieren das aktuelle Segment in Ausführen der **Upstream-Segmente, die nicht bereit sind** Liste.

### <a name="DataFactoryActivtyRuns"></a> Ansicht für alle Aktivitäten für ein Segment ausgeführt wird.
Für einen Slice kann es mehrere Ausführungen geben. Wenn bei einem Slice beispielsweise ein Fehler auftritt, kann der Dienst die Ausführung einige Male wiederholen. Sie können einen Slice, bei dem alle Wiederholungsversuche fehlgeschlagen sind, auch erneut ausführen. Sehen Sie alle die Aktivität ausgeführt wird, auf die ** Data Slice ** Blades in der Liste unten.

## <a name="DataFactoryActivtyRunDetails"></a> Anzeigen von Details zu einer Aktivität ausführen
Klicken Sie auf eine Aktivität, führen Sie aus der Liste der ausgeführt wird, auf die **Datenslice** Blade Anzeigen von Details zur Aktivität ausführen.

![Aktivität auszuführen, Details][image-data-factory-activity-run-details]

## <a name="EventsInThePastweek"></a> Vorgänge Linse - Ereignisse in der letzten Woche
In der **DATA FACTORY** Blade (oder Startseite) für die Factory Daten klicken Sie auf **Ereignisse in der letzten Woche** in **Operations** objektiv die Ereignisse in der Vorwoche anzuzeigen. Dadurch erhalten einen allgemeinen Überblick über die Vorgänge, die von der Data Factory in der letzten Woche ausgeführt wurden. Außerdem können Sie so auch Fehler im Zusammenhang mit der Datenverarbeitung/-verschiebung beheben.

![Daten-Factory-Ereignisse][image-data-factory-events]


## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Überwachen und Verwalten von Azure Data Factory mit PowerShell][monitor-manage-using-powershell] | Dieser Artikel beschreibt, wie ein Azure Data-Factory mit Azure-PowerShell-Cmdlets zu überwachen. 


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

<!---HONumber=GIT-SubDir--> 