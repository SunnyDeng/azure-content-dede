<properties title="How to customize monitoring" pageTitle="How to customize monitoring" description="Learn how to customize monitoring charts in Azure." authors="stepsic" />

Anpassen der Überwachung
========================

Auf dem Azure-Portal (Vorschau) können Sie nun Ihre Überwachungsdaten in mehr Varianten anzeigen als zuvor, beispielsweise den Zeitbereich anpassen und mehr Metriken auswählen.

1.  Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Browse**, dann auf **Web Sites** und anschließend auf den Namen einer Website, um das Fenster zu öffnen.

2.  Der Bereich **Monitoring** enthält die Teilbereiche "Requests", "Errors", [Web tests](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) und [Analytics](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Wenn Sie auf **Requests and errors today** klicken, wird das Fenster **Metric** geöffnet.

    ![Überwachungsbereich](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3.  Das Fenster **Metric** enthält Details zu den ausgewählten Metriken. Im oberen Teil des Fensters erscheint ein Diagramm und unmittelbar darunter eine Tabelle mit einer Aggregation diese Metriken, also beispielsweise Durchschnitt, Minimum und Maximum. Darunter wiederum werden die Warnungen angezeigt, die Sie definiert haben. Diese Warnungen sind entsprechend den Metriken in diesem Fenster gefiltert worden. Dadurch ist sichergestellt, dass Sie auch bei zahlreichen Warnungen immer nur die an dieser Stelle relevanten sehen. Natürlich können Sie auch alle Warnungen für Ihre Website aufrufen, indem Sie auf den Teilbereich **Alert rules** im Fenster **Web site** klicken.

    ![Fenster mit Metriken](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4.  Zur Anpassung der angezeigten Metriken klicken Sie mit der rechten Maustaste auf das Diagramm und wählen **Edit Query** aus:

    ![Abfrage bearbeiten](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)

5.  In diesem Bearbeitungsfenster können Sie: den Zeitbereich ändern und andere Metriken auswählen.

    ![Abfrage bearbeiten](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6.  Die Änderung des Zeitbereichs ist denkbar einfach: Sie wählen einen anderen Bereich aus (beispielsweise **Past Hour**) und klicken unten im Fenster auf **Save**. Neu im Vorschauportal ist die Option **Custom**:

	![Benutzerdefinierter Zeitbereich](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

1.  Über diese Option können Sie einen beliebigen Zeitraum innerhalb der letzten 2 Wochen auswählen, also beispielsweise die gesamten zwei Wochen einsehen oder auch nur 1 Stunde des Vortags. Geben Sie in das Textfeld eine andere Stunde ein.

2.  Unterhalb des Zeitbereichs können Sie beliebig viele Metriken auswählen, die in dem Diagramm angezeigt werden sollen. Sie werden auch einigen neuen Metriken begegnen: **Memory working set** und **Average memory working set**.

3.  Wenn Sie auf "Save" klicken, bleiben Ihre Änderungen erhalten, bis Sie das Fenster "Web site" verlassen. Wenn Sie später zurückkehren, werden wieder die ursprünglichen Metriken und der ursprüngliche Zeitbereich angezeigt.

Überwachen des Webhosting-Plans
-------------------------------

Ebenfalls neu im Azure-Vorschauportal ist die Möglichkeit, Leistungsmetriken der Instanzen zu überwachen, auf denen Ihre Websites ausgeführt werden. Diese Metriken rufen Sie auf, indem Sie auf das Symbol für den Webhosting-Plan im Bereich "Summary" klicken.

![Webhosting-Plan](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Dort sehen Sie im Bereich **Monitoring** ein Diagramm, das sich genauso verhält wie das Diagramm im Fenster "Web Site", nur dass an dieser Stelle neue Metriken angezeigt werden: - CPU Percentage - Memory Percentage - HTTP Queue Depth - Disk Queue Depth

Erstellen nebeneinander liegender Diagramme
-------------------------------------------

Mit den leistungsstarken Anpassungsfunktionen des Azure-Vorschauportals können Sie Diagramme auch nebeneinander platzieren.

1.  Klicken Sie zuerst mit der rechten Maustaste auf das Diagramm, mit dem Sie beginnen wollen, und wählen Sie **Customize** aus.

    ![Diagramm anpassen](./media/insights-how-to-customize-monitoring/Insights_Customize.png)

2.  Klicken Sie dann auf **Clone** in dem Menü **"..."**, um diesen Teilbereich zu kopieren.

    ![Klonen](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)

3.  Klicken Sie abschließend auf **Done** in der Symbolleiste oben im Bildschirm. In diesem Teilbereich können Sie nun wie gewohnt Metriken anzeigen lassen. Wenn Sie mit der rechten Maustaste klicken und die angezeigte Metrik ändern, werden zwei verschiedene Metriken gleichzeitig nebeneinander angezeigt:

    ![Zwei Metriken nebeneinander](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Hinweis: Der Zeitbereich des Diagramms und die ausgewählten Metriken werden zurückgesetzt, wenn Sie das Portal verlassen.

