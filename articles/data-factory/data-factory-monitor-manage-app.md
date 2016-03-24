<properties 
	pageTitle="Überwachen und Verwalten von Azure Data Factory-Pipelines" 
	description="Informationen zum Verwenden der App „Überwachung und Verwaltung“ für Azure Data Factorys und Pipelines." 
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
	ms.date="02/12/2016" 
	ms.author="spelluru"/>

# Überwachen und Verwalten von Azure Data Factory-Pipelines mit der neuen App „Überwachung und Verwaltung“
> [AZURE.SELECTOR]
- [Verwenden von Azure Portal/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
- [Verwenden der App "Überwachung und Verwaltung"](data-factory-monitor-manage-app.md)

In diesem Artikel wird das Überwachen, Verwalten und Debuggen Ihrer Pipelines mithilfe der **App "Überwachung und Verwaltung"** beschrieben. Ferner wird erläutert, wie mithilfe dieser Anwendung Warnungen erstellt und Benachrichtigungen bei Fehlern eingerichtet werden.
      
## Starten der App „Überwachung und Verwaltung“ 
Klicken Sie zum Starten der App "Überwachung und Verwaltung" auf dem Blatt Ihrer Data Factory auf der Kachel **DATA FACTORY** auf **Überwachungs-App**.

![Kachel „Überwachung“ auf der Data Factory-Startseite](./media/data-factory-monitor-manage-app/MonitoringAppTile.png)

Die gestartete App „Überwachung und Verwaltung“ sollte auf einer separaten Registerkarte bzw. in einem getrennten Fenster angezeigt werden.

![App „Überwachung und Verwaltung“](./media/data-factory-monitor-manage-app/AppLaunched.png)

## Grundlegendes zur App „Überwachung und Verwaltung“
Links befinden sich drei Registerkarten (**Ressourcen-Explorer**, **Überwachungsansichten** und **Warnungen**). Die erste Registerkarte (Ressourcen-Explorer) ist standardmäßig ausgewählt.

### Ressourcen-Explorer
Die **Strukturansicht** von Ressourcen-Explorer sehen Sie im linken Bereich, die **Diagrammansicht** oben und die Liste **Aktivitätsfenster** im mittleren Bereich unten. Die Registerkarten **Eigenschaften/Aktivitätsfenster-Explorer** werden rechts angezeigt.

Sie können alle Ressourcen (Pipelines, Datasets, verknüpfte Dienste) in der Data Factory in einer Strukturansicht anzeigen. Wenn Sie ein Objekt im Ressourcen-Explorer auswählen, sehen Sie Folgendes:

- die zugeordnete Data Factory-Entität ist in der Diagrammansicht hervorgehoben.
- Zugeordnete Aktivitätsfenster sind in der Liste „Aktivitätsfenster“ unten hervorgehoben. (Klicken Sie [hier](data-factory-scheduling-and-execution.md), um mehr zu Aktivitätsfenstern zu erfahren.)  
- Eigenschaften des ausgewählten Objekts im Fenster „Eigenschaften“ im rechten Bereich. 

![Ressourcen-Explorer](./media/data-factory-monitor-manage-app/ResourceExplorer.png)

Im Artikel [Planung und Ausführung](data-factory-scheduling-and-execution.md) finden Sie ausführliche konzeptionelle Informationen zum Aktivitätsfenster.

### Diagrammansicht
Die Diagrammansicht einer Data Factory bietet eine zentrale Konsole zum Überwachen und Verwalten der Data Factory und ihrer Ressourcen. Wenn Sie in der Diagrammansicht eine Data Factory-Entität (Dataset bzw. Pipeline) auswählen, sehen Sie Folgendes:
 
- die Data Factory-Entität ist in der Strukturansicht ausgewählt.
- zugeordnete Aktivitätsfenster sind in der Liste „Aktivitätsfenster“ hervorgehoben.
- Eigenschaften des ausgewählten Objekts im Fenster „Eigenschaften“.

Wenn die Pipeline aktiviert (d. h. nicht im Status „Angehalten“) ist, wird sie mit einer grünen Linie (siehe unten) angezeigt.

![Pipeline wird ausgeführt](./media/data-factory-monitor-manage-app/PipelineRunning.png)

Wie Sie sehen, gibt es in der Diagrammansicht drei Befehlsschaltflächen für die Pipeline. Die zweite Schaltfläche dient zum Anhalten der Pipeline. Dadurch werden die derzeit ausgeführten Aktivitäten nicht beendet, die bis zum Abschluss fortgesetzt werden können. Die dritte Schaltfläche dient zum Anhalten der Pipeline und Beenden vorhandener ausgeführter Aktivitäten. Über die erste Schaltfläche wird die angehaltene Pipeline fortgesetzt. Wenn Ihre Pipeline angehalten wird, ändert sich die Farbe der Pipelinekachel wie folgt.

![Fortsetzen/Anhalten auf Kachel](./media/data-factory-monitor-manage-app/SuspendResumeOnTile.png)

Durch Klicken bei gedrückter STRG-Taste können zwei oder mehrere Pipelines auswählen und diese gleichzeitig über die Schaltflächen auf der Befehlsleiste anhalten bzw. fortsetzen.

![Fortsetzen/Anhalten auf Befehlsleiste](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

Sie können alle Aktivitäten in der Pipeline anzeigen, indem Sie mit der rechten Maustaste auf die Pipeline klicken und dann auf **Pipeline öffnen** klicken.

![Menü „Pipeline öffnen“](./media/data-factory-monitor-manage-app/OpenPipelineMenu.png)

In der geöffneten Pipelineansicht sehen Sie alle Aktivitäten in der Pipeline. In diesem Beispiel gibt es mit der Kopieraktivität nur eine Aktivität. Um zur vorherigen Ansicht zurückzukehren, klicken Sie oben der Adressleiste auf den Namen der Data Factory.

![Geöffnete Pipeline](./media/data-factory-monitor-manage-app/OpenedPipeline.png)

Wenn Sie in der geschlossenen/geöffneten Pipelineansicht auf ein Ausgabedataset klicken und den Mauszeiger über diesem bewegen, wird das Aktivitätsfenster für dieses Dataset eingeblendet.

![Einblendfenster „Aktivitätsfenster“](./media/data-factory-monitor-manage-app/ActivityWindowsPopup.png)

Klicken Sie auf ein Aktivitätsfenster, um im rechten Bereich im Fenster **Eigenschaften** Details dazu anzuzeigen.

![Eigenschaften von Aktivitätsfenster](./media/data-factory-monitor-manage-app/ActivityWindowProperties.png)

Wechseln Sie im rechten Bereich zur Registerkarte **Aktivitätsfenster-Explorer**, um weitere Details anzuzeigen.

![Aktivitätsfenster-Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer.png)

Sie können das Aktivitätsfenster an drei Stellen sehen:

- im Einblendfenster in der Diagrammansicht (im mittleren Bereich)
- im Aktivitätsfenster-Explorer im rechten Bereich
- in der Liste „Aktivitätsfenster“ im unteren Bereich

Im Einblendfenster „Aktivitätsfenster“ und im Aktivitätsfenster-Explorer können Sie über die Pfeile nach links und rechts zur vorherigen und nächsten Woche navigieren.

![Aktivitätsfenster-Explorer, Pfeile nach links/rechts](./media/data-factory-monitor-manage-app/ActivityWindowExplorerLeftRightArrows.png)

Am unteren Rand der Diagrammansicht sehen Sie die Schaltflächen „Vergrößern“, „Verkleinern“, „Mit Zoom anpassen“, „Auf 100 % vergrößern“ und „Layout sperren“ (die verhindert, dass Sie versehentlich Tabellen und Pipelines in der Diagrammansicht verschieben). Die Schaltfläche „Layout sperren“ (mit dem Schloss) ist standardmäßig aktiviert. Sie können sie deaktivieren und Entitäten im Diagramm verschieben. Wenn Sie sie deaktivieren, können Sie die letzte Schaltfläche verwenden, um Tabellen und Pipelines automatisch zu positionieren. Sie können die Ansicht auch mit dem Mausrad vergrößern/verkleinern.

![Diagrammansicht, Zoombefehle](./media/data-factory-monitor-manage-app/DiagramViewZoomCommands.png)


### Liste „Aktivitätsfenster“
Die Liste „Aktivitätsfenster“ unten im mittleren Bereich zeigt alle Aktivitätsfenster für das Dataset, das Sie im Ressourcen-Explorer oder in der Diagrammansicht ausgewählt haben. Standardmäßig ist die Liste in absteigender Reihenfolge, was bedeutet, dass das aktuelle Aktivitätsfenster oben angezeigt wird.

![Liste „Aktivitätsfenster“](./media/data-factory-monitor-manage-app/ActivityWindowsList.png)

Diese Liste wird nicht automatisch aktualisiert, weshalb Sie sie über die Schaltfläche „Aktualisieren“ auf der Symbolleiste aktualisieren müssen.


Das Aktivitätsfenster kann einen der folgenden Status haben:

<table>
<tr>
	<th align="left">Status</th><th align="left">Unterstatus</th><th align="left">Beschreibung</th>
</tr>
<tr>
	<td rowspan="8">Warten</td><td>ScheduleTime</td><td>Der Zeitpunkt für die Ausführung des Aktivitätsfenster ist noch nicht erreicht.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>Die Upstream-Abhängigkeiten sind nicht bereit.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Die Compute-Ressourcen sind nicht verfügbar.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Alle Aktivitätsinstanzen führen andere Aktivitätsfenster aus.</td>
</tr>
<tr>
<td>ActivityResume</td><td>Die Aktivität wurde angehalten und kann die Aktivitätsfenster erst ausführen, wenn sie fortgesetzt wird.</td>
</tr>
<tr>
<td>Wiederholen</td><td>Die Aktivität wird erneut ausgeführt.</td>
</tr>
<tr>
<td>Überprüfen</td><td>Die Überprüfung wurde noch nicht gestartet.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Es wird auf die Wiederholung der Überprüfung gewartet.</td>
</tr>
<tr>
&lt; tr
<td rowspan="2">In Bearbeitung</td><td>Die Überprüfen erfolgt.</td><td>Die Überprüfung wird ausgeführt.</td>
</tr>
<td></td>
<td>Das Aktivitätsfenster wird verarbeitet.</td>
</tr>
<tr>
<td rowspan="4">Fehler</td><td>TimedOut</td><td>Die Ausführung dauerte länger, als für die Aktivität zulässig ist.</td>
</tr>
<tr>
<td>Canceled</td><td>Durch den Benutzer abgebrochen.</td>
</tr>
<tr>
<td>Überprüfen</td><td>Fehler bei der Überprüfung.</td>
</tr>
<tr>
<td></td><td>Das Aktivitätsfenster konnte nicht generiert und/oder überprüft werden.</td>
</tr>
<td>Bereit</td><td></td><td>Das Aktivitätsfenster ist für die Verwendung bereit.</td>
</tr>
<tr>
<td>Übersprungen</td><td></td><td>Das Aktivitätsfenster wird nicht verarbeitet.</td>
</tr>
<tr>
<td>Keine</td><td></td><td>Ein Aktivitätsfenster, das zuvor einen anderen Status hatte, aber zurückgesetzt wurde.</td>
</tr>
</table>


Wenn Sie in der Liste auf ein Aktivitätsfenster klicken, sehen Sie auf der rechten Seiten im Aktivitätsfenster-Explorer oder im Fenster „Eigenschaften“ Details zum Aktivitätsfenster.

![Aktivitätsfenster-Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-2.png)

### Aktualisieren von Aktivitätsfenstern  
Die Details werden nicht automatisch aktualisiert, weshalb Sie auf der Befehlsleiste auf die (zweite) Schaltfläche **Aktualisieren** klicken müssen, um die Liste der Aktivitätsfenster manuell zu aktualisieren.
 

### Fenster „Eigenschaften“
Das Fenster „Eigenschaften“ wird in der App „Überwachung und Verwaltung“ ganz rechts angezeigt.

![Fenster „Eigenschaften“](./media/data-factory-monitor-manage-app/PropertiesWindow.png)

Es zeigt Eigenschaften für das im Ressourcen-Explorer (Strukturansicht) oder in der Diagrammansicht oder in der Liste „Aktivitätsfenster“ ausgewählte Element.

### Aktivitätsfenster-Explorer

Das Fenster „Aktivitätsfenster-Explorer“ wird in der App „Überwachung und Verwaltung“ ganz rechts angezeigt. Es zeigt Details zum im Einblendfenster „Aktivitätsfenster“ oder in der Liste „Aktivitätsfenster“ ausgewählten Aktivitätsfenster.

![Aktivitätsfenster-Explorer](./media/data-factory-monitor-manage-app/ActivityWindowExplorer-3.png)

Sie können zu einem anderen Aktivitätsfenster wechseln, indem Sie in der Kalenderansicht oben darauf klicken. Sie können auch oben auf die Schaltflächen **Pfeil nach links/Pfeil nach rechts** klicken, um Aktivitätsfenster in der vorherigen oder nächsten Woche anzuzeigen.

Sie können im unteren Bereich auf der Symbolleiste auf die Schaltflächen klicken, um das Aktivitätsfenster zu **erneut aufzurufen** oder die Details im Bereich zu **aktualisieren**.


## Verwenden von Systemsichten
Die App "Überwachung und Verwaltung" bietet vordefinierte Systemansichten (**Letzte Aktivitätsfenster**, **Fehlerhafte Aktivitätsfenster**, **In Bearbeitung befindliche Aktivitätsfenster**), in denen Sie die entsprechenden Aktivitätsfenster für Ihre Data Factory anzeigen können.

Wechseln Sie zur Registerkarte **Überwachungsansichten** auf der linken Seite, indem Sie darauf klicken.

![Registerkarte „Überwachungsansichten“](./media/data-factory-monitor-manage-app/MonitoringViewsTab.png)

Derzeit werden drei Systemsichten unterstützt. Wählen Sie eine Option, um in der Liste „Aktivitätsfenster“ (unten im mittleren Bereich) die letzten Aktivitätsfenster oder fehlerhafte Aktivitätsfenster oder in Bearbeitung befindliche Aktivitätsfenster anzuzeigen.

Bei der Wahl der Option **Letzte Aktivitätsfenster** finden Sie die letzten Aktivitätsfenster in absteigender Reihenfolge basierend auf dem **Zeitpunkt des letzten Versuchs**.

Sie können die Option **Fehlerhafte Aktivitätsfenster** nutzen, um alle fehlerhaften Aktivitätsfenster in der Liste anzuzeigen. Wählen Sie ein fehlerhaftes Aktivitätsfenster in der Liste aus, um Details dazu im Fenster **Eigenschaften** oder im **Aktivitätsfenster-Explorer** anzuzeigen. Sie können für fehlerhafte Aktivitätsfenster auch Protokolle herunterladen.


## Sortieren und Filtern von Aktivitätsfenstern
Ändern Sie in der Befehlsleiste die **Startzeit** und **Endzeit**, um Aktivitätsfenster zu filtern. Klicken Sie nach Ändern von Start- und Endzeit auf die Schaltfläche neben der Endzeit, um die Liste „Aktivitätsfenster“ zu aktualisieren.

![Start- und Endzeiten](./media/data-factory-monitor-manage-app/StartAndEndTimes.png)

> [AZURE.NOTE] Derzeit haben alle Zeiten in der App „Überwachung und Verwaltung“ das UTC-Format.

Klicken Sie in der Liste **Aktivitätsfenster** auf den Namen einer Spalte (z. B. "Status").

![Liste „Aktivitätsfenster“, Spaltenmenü](./media/data-factory-monitor-manage-app/ActivityWindowsListColumnMenu.png)

Sie können Folgendes durchführen:

- Sortieren in aufsteigender Reihenfolge
- Sortieren in absteigender Reihenfolge
- Filtern nach einem oder mehreren Werten („Bereit“, „Wartend“ usw.)

Wenn Sie einen Filter für eine Spalte angeben, wird die Filterschaltfläche für diese Spalte aktiviert, um anzugeben, dass die Werte in der Spalte gefiltert sind.

![Filtern in der Spalte in der Liste „Aktivitätsfenster“](./media/data-factory-monitor-manage-app/ActivityWindowsListFilterInColumn.png)

Im selben Fenster können Sie Filter aufheben. Um alle Filter für die Liste „Aktivitätsfenster“ aufzuheben, klicken Sie auf der Befehlsleiste auf die Schaltfläche „Filter aufheben“.

![Aufheben aller Filter in der Liste „Aktivitätsfenster“](./media/data-factory-monitor-manage-app/ClearAllFiltersActivityWindowsList.png)


## Durchführen von Batchaktionen

### Wiederholen ausgewählter Aktivitätsfenster
Wählen Sie ein Aktivitätsfenster aus, klicken Sie für die erste Befehlsleisten-Schaltfläche auf den Pfeil nach unten, und wählen Sie **Wiederholen**/**Mit vorgelagerter Aktivität in der Pipeline wiederholen** aus. Bei Wahl der Option **Mit vorgelagerter Aktivität in der Pipeline wiederholen** werden alle vorgelagerten Aktivitätsfenster ebenfalls wiederholt. ![Wiederholen eines Aktivitätsfensters](./media/data-factory-monitor-manage-app/ReRunSlice.png)

Sie können auch mehrere Aktivitätsfenster in der Liste auswählen und diese gleichzeitig wiederholen. Sie können Aktivitätsfenster basierend auf dem Status (z. B. **Fehlerhaft**) filtern und das fehlerhafte Aktivitätsfenster wiederholen, nachdem Sie den Fehler behoben haben, der den Fehler des Aktivitätsfenster verursacht hat. Im folgenden Abschnitt finden Sie Details zum Filtern von Aktivitätsfenstern in der Liste.

### Anhalten/Fortsetzen mehrerer Pipelines
Sie können bei gedrückter STRG-TASTE zwei oder mehrere Pipelines auswählen und diese über Schaltflächen auf der Befehlsleiste (in der folgenden Abbildung mit einem roten Rechteck hervorgehoben) gleichzeitig anhalten bzw. fortsetzen.

![Anhalten/Fortsetzen auf Befehlsleiste](./media/data-factory-monitor-manage-app/SuspendResumeOnCommandBar.png)

## Erstellen von Warnungen 
Auf der Seite „Warnungen“ können Sie eine neue Warnung erstellen bzw. vorhandene Warnungen anzeigen, bearbeiten und löschen. Sie können eine Warnung auch aktivieren/deaktivieren. Klicken Sie auf die Registerkarte „Warnungen“, um die Seite anzuzeigen.

![Registerkarte „Warnungen“](./media/data-factory-monitor-manage-app/AlertsTab.png)

### So erstellen Sie eine Warnung

1. Klicken Sie auf **Warnung hinzufügen**, um eine Warnung hinzuzufügen. Die Seite „Details“ wird angezeigt. 

	![Warnungen erstellen – Seite „Details“](./media/data-factory-monitor-manage-app/CreateAlertDetailsPage.png)
1. Geben Sie den **Namen** und eine **Beschreibung** für die Warnung an, und klicken Sie auf **Weiter**. Daraufhin sollte die Seite **Filter** angezeigt werden.

	![Warnung erstellen – Seite „Filter“](./media/data-factory-monitor-manage-app/CreateAlertFiltersPage.png)

	Sie können Warnungsereignisse auch **zusammenfassen** (siehe unten):

	![Zusammenfassen von Warnungen](./media/data-factory-monitor-manage-app/AggregateAlerts.png)
2. Wählen Sie das **Ereignis**, den **Status** und den **Unterstatus** (optional) für die Warnung durch den Data Factory-Dienst aus, und klicken Sie auf **Weiter**. Daraufhin sollte die Seite **Empfänger** angezeigt werden.

	![Warnung erstellen – Seite „Empfänger“](./media/data-factory-monitor-manage-app/CreateAlertRecipientsPage.png) 
3. Wählen Sie **E-Mail-Abonnementadministratoren** aus, und/oder füllen Sie **E-Mail an weiteren Administrator** aus, und klicken Sie auf **Fertig stellen**. Sie sollten die Warnung in der Liste sehen. 
	
	![Liste „Warnungen“](./media/data-factory-monitor-manage-app/AlertsList.png)

In der Liste „Warnungen“ können Sie eine Warnung über die entsprechende Schaltfläche bearbeiten, löschen, deaktivieren und aktivieren.

### Ereignis/Status/Unterstatus
Die folgende Tabelle enthält eine Liste der verfügbaren Ereignisse und Status (samt Unterstatus).

Ereignisname | Status | Unterstatus
-------------- | ------ | ----------
Aktivitätsausführung gestartet | Gestartet | Wird gestartet
Aktivitätsausführung beendet | Succeeded | Succeeded 
Aktivitätsausführung beendet | Fehler| Fehlerhafte Ressourcenzuordnung <br/><br/>Fehlerhafte Ausführung<br/><br/>Timeout<br/><br/>Fehlerhafte Überprüfung<br/><br/>Abgebrochen
Bedarfsgesteuerte Erstellung eines HDI-Clusters gestartet | Gestartet | &nbsp; |
Bedarfsgesteuerte Erstellung eines HDI-Clusters erfolgreich | Succeeded | &nbsp; |
Bedarfsgesteuert erstellten HDI-Cluster gelöscht | Succeeded | &nbsp; |
### So bearbeiten, löschen oder deaktivieren Sie eine Warnung


![Schaltflächen „Warnungen“](./media/data-factory-monitor-manage-app/AlertButtons.png)



    
 

<!---HONumber=AcomDC_0309_2016-->