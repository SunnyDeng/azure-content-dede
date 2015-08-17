<properties
	pageTitle="Operational Insights-Dashboards"
	description="Artikel mit Informationen zu den Grundlagen der Verwendung des Operational Insights-Dashboards"
	services="operational-insights"
	documentationCenter=""
	authors="ehissey"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/21/2015"
	ms.author="evanhi"/>

# Operational Insights-Dashboards

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

In diesem Leitfaden wird beschrieben, wie in Operational Insights-Dashboards alle gespeicherten Protokollsuchen visualisiert werden können, um Ihnen einen zentralen Überblick über Ihre Umgebung zu ermöglichen.

![Beispiel-Dashboard](./media/operational-insights-use-dashboards/example-dash.png)

## Wie erstelle ich mein Dashboard?

Greifen Sie zuerst auf die Übersicht von Azure Operational Insights zu, indem Sie im linken Navigationsbereich auf die Schaltfläche „Übersicht“ klicken. Auf der linken Seite sehen Sie die Kachel „Mein Dashboard“. Klicken Sie darauf, um einen Drilldown in das Dashboard durchzuführen.

![Übersicht](./media/operational-insights-use-dashboards/overview.png)



## Hinzufügen einer Kachel

In Dashboards werden Kacheln über Ihre gespeicherten Protokollsuchen mit Informationen versorgt. Operational Insights verfügt über viele vorgefertigte Protokollsuchen, damit Sie sofort beginnen können. Die folgende Abbildung mit Startinformationen wird angezeigt.

![Abbildung](./media/operational-insights-use-dashboards/pictorial.png)

Klicken Sie in der Ansicht „Mein Dashboard“ einfach unten auf der Seite auf das Zahnrad „Anpassen“, um den Anpassungsmodus zu aktivieren. Im Bereich, der rechts auf der Seite geöffnet wird, werden alle gespeicherten Protokollsuchen Ihres Arbeitsbereichs angezeigt.

![Hinzufügen von Kacheln 1](./media/operational-insights-use-dashboards/add-tile1.png)

Sie können eine gespeicherte Protokollsuche als Kachel visualisieren, indem Sie sie einfach links in den leeren Bereich ziehen. Beim Ziehen wird die Umwandlung in eine Kachel vollzogen.

![Hinzufügen von Kacheln 2](./media/operational-insights-use-dashboards/add-tile2.png)

![Hinzufügen von Kacheln 3](./media/operational-insights-use-dashboards/add-tile3.png)


## Bearbeiten einer Kachel

Klicken Sie in der Ansicht „Mein Dashboard“ einfach unten auf der Seite auf das Zahnrad „Anpassen“, um den Anpassungsmodus zu aktivieren. Klicken Sie auf die Kachel, die Sie bearbeiten möchten. Der rechte Bereich wechselt in den Bearbeitungsmodus, und es werden entsprechende Optionen angezeigt: ![Bearbeiten der Kachel](./media/operational-insights-use-dashboards/edit-tile.png)

### Kachelvisualisierungen#
Es gibt zwei Arten von Kachelvisualisierungen:

**Balkendiagramm** <p> ![Balkendiagramm](./media/operational-insights-use-dashboards/bar-chart.png)

Hierbei wird eine Zeitachse mit den Ergebnissen Ihrer gespeicherten Protokollsuchen oder eine Liste mit Ergebnissen nach einem Feld angezeigt. Dies richtet sich danach, ob bei Ihrer Protokollsuche Ergebnisse basierend auf einem Feld zusammengefasst werden.

**Metrik** <p> ![Metrik](./media/operational-insights-use-dashboards/metric.png)

Hierbei wird die Gesamtzahl der Ergebnistreffer für Ihre Protokollsuche als Zahl in einer Kachel angezeigt. Bei Kacheln vom Typ „Metrik“ können Sie einen Schwellenwert festlegen, bei dem die Kachel hervorgehoben wird, wenn der Schwellenwert erreicht ist.

### Schwellenwert
Mit der Visualisierung „Metrik“ können Sie einen Schwellenwert für eine Kachel erstellen. Wählen Sie „Ein“, um auf der Kachel einen Schwellenwert zu erstellen. Geben Sie an, ob die Kachel hervorgehoben werden soll, wenn der Wert oberhalb oder unterhalb des gewählten Schwellenwerts liegt, und legen Sie darunter dann den Schwellenwert fest.

## Organisieren des Dashboards
Navigieren Sie zum Organisieren des Dashboards zur Ansicht „Mein Dashboard“, und klicken Sie unten auf der Seite auf das Zahnrand „Anpassen“, um den Anpassungsmodus zu aktivieren. Klicken Sie auf die Kachel, die Sie verschieben möchten, und ziehen Sie sie an die gewünschte Position.

![Organisieren des Dashboards](./media/operational-insights-use-dashboards/organize.png)

## Entfernen einer Kachel
Navigieren Sie zum Entfernen einer Kachel zur Ansicht „Mein Dashboard“, und klicken Sie unten auf der Seite auf das Zahnrand **Anpassen**, um den Anpassungsmodus zu aktivieren. Wählen Sie die Kachel aus, die Sie entfernen möchten, und wählen Sie im rechten Bereich die Option **Kachel entfernen**. ![Entfernen einer Kachel](./media/operational-insights-use-dashboards/remove-tile.png)

<!---HONumber=August15_HO6-->