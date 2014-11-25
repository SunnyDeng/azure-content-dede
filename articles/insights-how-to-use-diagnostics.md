<properties title="How to use diagnostics" pageTitle="How to use diagnostics" description="Learn how to set up diagnostics for your resources in Azure." authors="stepsic"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="stepsic" />

# Einrichten der Diagnose

In der Vorschau des Azure-Portals können Sie jetzt umfangreiche, zeitnahe Beobachtungs- und Diagnosedaten zu Ihren virtuellen Windows-Computern und Speicherkonten konfigurieren.

## Sammeln von Rich Data auf virtuellen Computern

1.  Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Durchsuchen** und dann auf **Virtuelle Computer**. Wählen Sie den zu überwachenden virtuellen Computer aus.
2.  Der Bereich **Überwachung** enthält einige Standardmetriken wie **CPU-Auslastung**, **Festplattenzugriffe** und **Netzwerkverkehr**. Wenn Sie auf diese Elemente klicken, wird das Fenster **Metrik** geöffnet.
    ![Überwachungsbereich](./media/insights-how-to-use-diagnostics/Insights_VMMonitoringLens.png)
3.  Das Fenster **Metric** enthält Details zu den ausgewählten Metriken. Im oberen Teil des Fensters erscheint ein Diagramm und unmittelbar darunter eine Tabelle mit einer Aggregation diese Metriken, also beispielsweise Durchschnitt, Minimum und Maximum. Darunter befindet sich eine Liste mit den von Ihnen definierten Warnungen, gefiltert nach den im Fenster angezeigten Metriken.
    ![Fenster mit Metriken](./media/insights-how-to-use-diagnostics/Insights_VMMetricBlade.png)
4.  Um weitergehende Diagnosen zu erhalten, klicken Sie auf die Schaltfläche **Einstellungen**, woraufhin der Bereich **Diagnose** angezeigt wird. Wählen Sie **EIN**:
    ![Diagnose-Fenster](./media/insights-how-to-use-diagnostics/Insights_VMDiagnosticsBlade.png)

    -   **Grundlegende Metriken**: Zustandsmetriken zum virtuellen Computer wie Prozessor und Speicher
    -   **Metriken nach Festplatten**: Metriken aller Festplatten, die mit Ihrem virtuellen Computer verbunden sind
    -   **.NET-Metriken**: Metriken zu den .NET- und ASP.NET-Anwendungen, die auf Ihrem virtuellen Computer ausgeführt werden.
    -   **Netzwerk-Metriken**: Metriken zu Ihren Netzwerkverbindungen und Webdiensten
    -   **Windows-Anwendungsereignisprotokolle**: Windows-Ereignisse, die zum Anwendungskanal gesendet werden.
    -   **Windows-Systemereignisprotokolle**: Windows-Ereignisse, die zum Systemkanal gesendet werden. Dazu gehören auch alle Ereignisse von [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409).
    -   **Windows-Sicherheitsereignisprotokolle**: Windows-Ereignisse, die zum Sicherheitskanal gesendet werden.
    -   **Diagnose-Infrastrukturprotokolle**: Protokollierung der Diagnosesammlungs-Infrastruktur.
    -   **IIS-Protokolle**: Protokolle zu Ihrem IIS-Server
        Alle Metriken und Protokolle werden im Takt von einer Minute aufgezeichnet, sodass Sie stets über aktuelle Daten zu Ihrem Computer verfügen.

Wenn Sie für ein Speicherkonto Diagnose-Informationen zulassen, werden diesem die Kosten für normale Speicherung, Transaktionen und Ausgangskosten in Rechnung gestellt. Diese Features verursachen jedoch kein großes Datenvolumen, ggf. mit Ausnahme von IIS-Protokollen. Um die Ausgangskosten zu minimieren, fordern wir Sie dazu auf, ein Speicherkonto in der Region auszuwählen, in der sich der virtuelle Computer befindet.

Nachdem Sie auf **OK** geklickt haben, werden innerhalb weniger Minuten Daten in ihrem Speicherkonto angezeigt. Sie können keine Diagnose für virtuelle Computer aktivieren, auf denen Linux ausgeführt wird, und der Gast-Agent muss für die Aktivierung der Diagnosefunktion installiert sein.

## Sammeln von Rich Data zu Speicherkonten

Sie konnten zwar bisher stets einige Daten von Speicherkonten erheben, aber jetzt können Sie in der Azure-Portalvorschau Daten mit der Genauigkeit von einer Minute sammeln, um einen echten Einblick in die Vorgänge auf Ihrem Speicherkonto zu erhalten. Die Schritte zum Aktivieren von minütlichen Metriken sind die gleichen wie für virtuelle Computer:

1.  Wechseln Sie zum Fenster **Metrik**, indem Sie auf ein beliebiges Diagramm im Fenster **Speicherkonto** klicken.
2.  Klicken Sie in der Befehlsleiste auf **Diagnose**.
3.  Wählen Sie aus, welche Daten Sie vom Speicherkonto erheben möchten:
     
    ![Speicherdiagnose](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)
4.  Klicken Sie auf **OK**. Nach einigen Minuten werden die ersten Daten angezeigt.

## Visualisieren von Diagnosedaten

Nachdem Sie die Diagnose aktiviert haben, können Sie die vollständige Liste verfügbarer Metriken aufrufen, indem Sie mit der rechten Maustaste auf ein beliebiges Diagramm klicken und zu **Abfrage bearbeiten** wechseln.

![Abfrage bearbeiten](./media/insights-how-to-use-diagnostics/Insights_VMEditQuery.png)

Sie können diese Metriken grafisch darstellen, auf die **Letzte Stunde** fokussieren, die **Letzte Woche** zusammenfassen oder auch einen Zeitbereich angeben, der **benutzerdefiniert** ist:

![Benutzerdefinierter Zeitbereich](./media/insights-how-to-use-diagnostics/Insights_VMCustomTime.png)

Sie werden bemerken, dass diese Metriken deutlich detaillierter sind als die zuvor verfügbaren Daten und es nur minimale Zeitverzögerungen gibt.

Derzeit besteht nicht die Möglichkeit, Metriken mit mehreren Instanzen grafisch darzustellen, wie Metriken nach Prozess oder nach Festplatte. Weitere Informationen zur Anpassung Ihrer Überwachungsdiagramme finden Sie unter [Anpassen der Überwachung](http://go.microsoft.com/fwlink/?LinkID=394523&clcid=0x409).

## Warnmeldungen von Diagnosedaten

Zusätzlich zur Visualisierung von Metriken können Sie Warnungen bei allen dieser Metriken in der Portalvorschau auslösen. Blättern Sie zunächst abwärts zum Bereich **Warnregeln** im Fenster des virtuellen Computers oder Speichers, und klicken Sie dann auf **Warnung hinzufügen**.

![Warnung hinzufügen](./media/insights-how-to-use-diagnostics/Insights_VMAlerts.png)

Danach können Sie aus den Metriken wählen, die Sie zur Diagnose aktiviert haben:

![JIT-Warnung](./media/insights-how-to-use-diagnostics/Insights_VMJITAlert.png)


Der Graph zeigt Ihnen eine Vorschau Ihres Warnschwellenwerts im Vergleich zur Metrik des vorhergehenden Tages. Nachdem Sie auf **Speichern** geklickt haben, werden Sie innerhalb weniger Minuten informiert, sobald die von Ihnen gewählte Metrik den Schwellenwert überschreitet.

Beachten Sie, dass Metriken, die nur im Vorschauportal angezeigt werden, nicht im vollständigen Portal mit Warnungen versehen werden können. Daher werden bestimmte Warnregeln aus dem Vorschauportal nicht im vollständigen Portal angezeigt.


