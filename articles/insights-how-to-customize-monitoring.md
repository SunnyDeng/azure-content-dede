<properties title="How to customize monitoring" pageTitle="Anpassen der Überwachung" description="Erfahren Sie, wie Sie Überwachungsdiagramme in Azure anpassen." authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-11-04" ms.author="awills" />

# Anpassen der Überwachung

Die Azure-Anwendung verfügt über eine Vielzahl von Metriken, die Sie überwachen können, und Sie können sie über einen Zeitraum im gewählten Diagramm anzeigen.

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Durchsuchen** und dann auf eine Ressource, die Sie überwachen möchten..
2. Der Bereich **Überwachen** enthält die wichtigsten Metriken zu jeder Azure-Ressource. Zu Websites finden Sie dort beispielsweise Anforderungen, Fehler, [Webtests](http://go.microsoft.com/fwlink/?LinkID=394528&clcid=0x409) und [Analysen](http://go.microsoft.com/fwlink/?LinkID=394529&clcid=0x409). Wenn Sie auf **Anforderungen und Fehler heute** klicken, wird das Fenster **Metrik** geöffnet.  
    ![Monitoring lens](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)
3. Das Fenster **Metrik** enthält Details zu den ausgewählten Metriken. Im oberen Teil des Fensters erscheint ein Diagramm und unmittelbar darunter eine Tabelle mit einer Aggregation diese Metriken, also beispielsweise Durchschnitt, Minimum und Maximum. Darunter wiederum werden die Warnungen angezeigt, die Sie definiert haben. Diese Warnungen sind entsprechend den Metriken in diesem Fenster gefiltert worden. Dadurch ist sichergestellt, dass Sie auch bei zahlreichen Warnungen immer nur die an dieser Stelle relevanten sehen. Natürlich können Sie auch alle Warnungen für Ihre Website aufrufen, indem Sie auf den Teilbereich **Warnungs-Regeln** im Fenster **Website** klicken.  
    ![Metric blade](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)
4. Zur Anpassung der angezeigten Metriken klicken Sie mit der rechten Maustaste auf das Diagramm und wählen **Abfrage bearbeiten** aus:  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_MetricMenu.png)
5. Im Fenster "Abfrage bearbeiten" können Sie Folgendes tun: den Zeitbereich ändern und andere Metriken auswählen.  
    ![Edit Query](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)
6. Die Änderung des Zeitbereichs ist so einfach wie einen anderen Bereich auszuwählen (wie beispielsweise **letzte Stunde**). Klicken Sie dann unten im Fenster auf **Speichern**. Im Vorschauportal können Sie jetzt die Option **Benutzerdefiniert** auswählen:  
    ![Custom time range](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)
7. Über diese Option können Sie einen beliebigen Zeitraum innerhalb der letzten 2 Wochen auswählen, also beispielsweise die gesamten zwei Wochen einsehen oder auch nur 1 Stunde des Vortags. Geben Sie in das Textfeld eine andere Stunde ein.
8. Unterhalb des Zeitbereichs können Sie beliebig viele Metriken auswählen, die in dem Diagramm angezeigt werden sollen. Sie werden auch einigen neuen Metriken begegnen: **Speicher-Arbeitsset** und **Durchshnittliches Speicher-Arbeitsset**.

9. Wenn Sie auf "Save" klicken, bleiben Ihre Änderungen erhalten, bis Sie das Fenster "Web site" verlassen. Wenn Sie später zurückkehren, werden wieder die ursprünglichen Metriken und der ursprüngliche Zeitbereich angezeigt.

## Überwachung für neue Ressourcen

Neu im Azure-Vorschauportal ist die Möglichkeit, Leistungsmetriken für eine Reihe von neuen Ressourcen zu überwachen, darunter:
- Webhostingpläne
- Redis-Cache
- DocumentDB-Konto

Webhostingpläne sind etwas komplizierter als andere Ressourcen, da sie die Leistung der Instanzen darstellen, auf denen Ihre **Websites** ausgeführt werden. Sie rufen die Metriken von Webhostingplänen auf, indem Sie auf das Symbol für den Webhostingplan im Bereich "Summary" für Ihre Website klicken.

![Web hosting plan](./media/insights-how-to-customize-monitoring/Insights_WHPSelect.png)

Dort sehen Sie im Bereich **Überwachung** ein Diagramm, das sich genauso verhält wie das Diagramm im Fenster "Website", nur dass an dieser Stelle neue Metriken angezeigt werden:

- CPU-Prozentsatz
- Arbeitsspeicherprozentsatz
- HTTP-Warteschlangenlänge
- Warteschlangenlänge des Datenträgers

## Erstellen nebeneinander liegender Diagramme

Mit den leistungsstarken Anpassungsfunktionen des Azure-Vorschauportals können Sie Diagramme auch nebeneinander platzieren.

1. Klicken Sie zuerst mit der rechten Maustaste auf das Diagramm, mit dem Sie beginnen wollen, und wählen Sie **Anpassen**   aus.
    ![Customize chart](./media/insights-how-to-customize-monitoring/Insights_Customize.png)
2. Klicken Sie dann auf **Klonen** im Menü **...**, um den Teil zu kopieren.  
    ![Clone part](./media/insights-how-to-customize-monitoring/Insights_ClonePart.png)
3. Klicken Sie abschließend in der Symbolliste oben im Bildschirm auf **Fertig**. In diesem Teilbereich können Sie nun wie gewohnt Metriken anzeigen lassen. Wenn Sie mit der rechten Maustaste klicken und die angezeigte Metrik ändern, werden zwei verschiedene Metriken gleichzeitig nebeneinander angezeigt:  
    ![Two metrics Side by Side](./media/insights-how-to-customize-monitoring/Insights_SideBySide.png)

Hinweis: Der Zeitbereich des Diagramms und die ausgewählten Metriken werden zurückgesetzt, wenn Sie das Portal verlassen.

