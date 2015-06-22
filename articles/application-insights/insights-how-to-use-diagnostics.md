<properties 
	pageTitle="Verwenden der Diagnose" 
	description="Erfahren Sie, wie Sie die Diagnose für Ihre Ressourcen in Azure einrichten."  
	authors="stepsic-microsoft-com" 
	manager="kamrani" 
	editor="" 
	services="application-insights" 
	documentationCenter=""/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2014-11-04" 
	ms.author="stepsic"/>

# Einrichten der Diagnose

In der Vorschau des Azure-Portals können Sie jetzt umfangreiche, zeitnahe Beobachtungs- und Diagnosedaten zu Ihren virtuellen Windows-Computern und Speicherkonten konfigurieren.

## Sammeln von Rich Data auf virtuellen Computern
1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Durchsuchen** und dann auf **Virtuellel Computer**. Wählen Sie den zu überwachenden virtuellen Computer aus.
2. Der Bereich **Überwachung** enthält einige Stanard-Metriken wie **Prozente**, **Schreiben und Lesen auf Diskette** und **Netzwerk ein und aus**. Wenn Sie auf einen dieser Teile klicken, sehen Sie das Fenster **Metrik**.  
    ![Monitoring lens](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3. Das Fenster **Metrik** enthält Details zu den ausgewählten Metriken. Im oberen Teil des Fensters erscheint ein Diagramm und unmittelbar darunter eine Tabelle mit einer Aggregation diese Metriken, also beispielsweise Durchschnitt, Minimum und Maximum. Darunter wiederum werden die Warnungen angezeigt, die Sie definiert haben. Diese Warnungen sind entsprechend den Metriken in diesem Fenster gefiltert worden.  
    ![Metric blade](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4. Um weitergehende Diagnosen zu erhalten, klicken Sie auf die Schaltfläche **Einstellungen**, woraufhin das Fenster **Diagnose** angezeigt wird. Wählen Sie **EIN**:  
    ![Diagnostics blade](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)
    - **Grundlegende Metriken**: Zustandsmetriken zum virtuellen Computer wie Prozessor und Speicher 
    - **Metriken nach Festplatten**: Metriken aller Festplatten, die mit Ihrem virtuellen Computer verbunden sind
    - **.NET-Metriken**: Metriken zu den .NET- und ASP.NET-Anwendungen, die auf Ihrem virtuellen Computer ausgeführt werden
    - **Netzwerk-Metriken**: Metriken zu Ihren Netzwerkverbindungen und Webdiensten
    - **Windows-Anwendungsereignisprotokolle**: Windows-Ereignisse, die zum Anwendungskanal gesendet werden
    - **Windows-Systemereignisprotokolle**: Windows-Ereignisse, die zum Systemkanal gesendet werden. Dazu gehören auch alle Ereignisse von [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409). 
    - **Windows-Sicherheitsereignisprotokolle**: Windows-Ereignisse, die zum Sicherheitskanal gesendet werden
    - **Diagnose-Infrastrukturprotokolle**: Protokollierung der Diagnosesammlungs-Infrastruktur
    - **IIS-Protokolle**: Protokolle zu IIS-Server
    Alle Metriken und Protokolle werden im Takt von einer Minute aufgezeichnet, sodass Sie stets über aktuelle Daten zu Ihrem Computer verfügen.

Wenn Sie für ein Speicherkonto Diagnose-Informationen zulassen, werden diesem die Kosten für normale Speicherung, Transaktionen und Ausgangskosten in Rechnung gestellt. Diese Features verursachen jedoch kein großes Datenvolumen, ggf. mit Ausnahme von IIS-Protokollen. Um die Ausgangskosten zu minimieren, fordern wir Sie dazu auf, ein Speicherkonto in der Region auszuwählen, in der sich der virtuelle Computer befindet.

Nachdem Sie auf **OK** geklickt haben, werden innerhalb weniger Minuten Daten in ihrem Speicherkonto angezeigt. Sie können keine Diagnose für virtuelle Computer aktivieren, auf denen Linux ausgeführt wird, und der Gast-Agent muss für die Aktivierung der Diagnosefunktion installiert sein.

## Sammeln von Rich Data zu Speicherkonten

Sie konnten zwar bisher stets einige Daten von Speicherkonten erheben, aber jetzt können Sie in der Azure-Portalvorschau Daten mit der Genauigkeit von einer Minute sammeln, um einen echten Einblick in die Vorgänge auf Ihrem Speicherkonto zu erhalten. Die Schritte zum Aktivieren von minütlichen Metriken sind die gleichen wie für virtuelle Computer:

1. Wechseln Sie zum Fenster **Metrik**, indem Sie auf ein beliebiges Diagramm im Fenster **Speicherkonto** klicken.
2. Klicken Sie in der Befehlsleiste auf **Diagnose**.
3. Wählen Sie aus, welche Daten Sie vom Speicherkonto erheben möchten:  
    ![Storage diagnostics](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4. Klicken Sie auf **OK**. Nach einigen Minuten werden die ersten Daten angezeigt.

## Visualisieren von Diagnosedaten 

Nachdem Sie die Diagnose aktiviert haben, können Sie die vollständige Liste verfügbarer Metriken aufrufen, indem Sie mit der rechten Maustaste auf ein beliebiges Diagramm klicken und zu **Abfrage bearbeiten** wechseln:

![Edit query](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

Sie können diese Metriken aufzeichnen und vergrößern für **Vergangene Stunde**, verkleinern auf **Vergangene Woche** oder selbst einen **benutzerdefinierten** Zeitbereich wählen:
 
![Custom timerange](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

Sie werden bemerken, dass diese Metriken deutlich detaillierter sind als die zuvor verfügbaren Daten und es nur minimale Zeitverzögerungen gibt.

Derzeit besteht nicht die Möglichkeit, Metriken mit mehreren Instanzen grafisch darzustellen, wie Metriken nach Prozess oder nach Festplatte. Weitere Informationen zur Anpassung Ihrer Überwachungsdiagramme finden Sie unter [Anpassen der Überwachung](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409).

## Warnmeldungen von Diagnosedaten

Zusätzlich zur Visualisierung von Metriken können Sie Warnungen bei allen dieser Metriken in der Portalvorschau auslösen. Blättern Sie zunächst abwärts zum Bereich **Warnregeln** im Fenster des virtuellen Computers oder Speichers, und klicken Sie dann auf **Warnung**:

![Add alert](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

Danach können Sie aus den Metriken wählen, die Sie zur Diagnose aktiviert haben:

![JIT alert](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)

Der Graph zeigt Ihnen eine Vorschau Ihres Warnschwellenwerts im Vergleich zur Metrik des vorhergehenden Tages. Nachdem Sie auf **Speichern** geklickt haben, werden Sie innerhalb weniger Minuten informiert, sobald die von Ihnen gewählte Metrik den Schwellenwert überschreitet. 

Beachten Sie, dass Metriken, die nur im Vorschauportal angezeigt werden, nicht im vollständigen Portal mit Warnungen versehen werden können. Daher werden bestimmte Warnregeln aus dem Vorschauportal nicht im vollständigen Portal angezeigt.

<!--HONumber=46--> 
 