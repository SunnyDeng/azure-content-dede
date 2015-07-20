<properties
   pageTitle="Identifizieren von Unterschieden mit Change Tracking"
   description="Verwenden Sie die Lösung zur Nachverfolgung von Konfigurationsänderungen in Microsoft Azure Operational Insights, um ganz einfach Änderungen an Software und Windows-Diensten zu identifizieren, die in Ihrer Umgebung vorgenommen werden"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="" />
<tags
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/02/2015"
   ms.author="banders" />

# Identifizieren von Unterschieden mit Change Tracking

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Mit der Lösung zur Nachverfolgung von Konfigurationsänderungen in Microsoft Azure Operational Insights können Sie ganz einfach Änderungen an Software und Windows-Diensten identifizieren, die in Ihrer Umgebung vorgenommen werden – durch das Feststellen solcher Konfigurationsänderungen können Sie Betriebsprobleme leichter lokalisieren.

Sie installieren die Lösung, um den Operations Manager-Agent und das Basiskonfigurationsmodul für Operational Insights zu aktualisieren. Änderungen an installierter Software und Windows-Diensten auf den überwachten Servern werden gelesen, und die Daten werden zur Verarbeitung an den Operational Insights-Dienst in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf. Wenn Änderungen gefunden werden, werden die Server mit den Änderungen im Change Tracking-Dashboard angezeigt. Mithilfe der Informationen im Change Tracking-Dashboard können Sie ganz leicht die Änderungen erkennen, die in Ihrer Serverinfrastruktur vorgenommen wurden.

## Verwenden von Change Tracking

Bevor Sie Change Tracking in Operational Insights verwenden können, müssen Sie die Lösung installiert haben. Weitere Informationen zum Installieren von Lösungen finden Sie unter [Verwenden von Solution Gallery zum Hinzufügen oder Entfernen von Lösungen](operational-insights-add-solution.md).

Nach der Installation können Sie die Zusammenfassung der Änderungen für die überwachten Server in Operational Insights über die Kachel **Change Tracking** auf der Seite **Übersicht** anzeigen.

![Abbildung der Kachel "Change Tracking"](./media/operational-insights-change-tracking/overview-change-track.png)

Sie können Änderungen an Ihrer Infrastruktur anzeigen und dann Einzelheiten für die folgenden Kategorien abrufen:

- Änderungen nach Konfigurationstyp für Software und Windows-Dienste

- Softwareänderungen an Anwendungen und Updates für einzelne Server

- Gesamtanzahl von Softwareänderungen für jede Anwendungen

- Änderungen am Windows-Dienst für einzelne Server

![Abbildung des Change Tracking-Dashboards](./media/operational-insights-change-tracking/gallery-changetracking-01.png) ![Abbildung des Change Tracking-Dashboards](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### So zeigen Sie Änderungen für Änderungstypen an

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Change Tracking**.

2. Prüfen Sie im Dashboard **Change Tracking** die Zusammenfassungsinformationen in einem der Blätter mit den Änderungstypen, und klicken Sie dann auf einen Eintrag, um detaillierte Informationen auf der Seite **Protokollsuche** anzuzeigen.

3. Sie können auf jeder Seite für die Protokollsuche die Ergebnisse nach Zeit, detaillierte Ergebnisse und Ihren Protokollsuchverlauf anzeigen. Außerdem können Sie nach Facets filtern, um die Ergebnisse einzugrenzen.

4. Auf jeder Seite für die Protokollsuche können Sie die Ergebnisdetails in eine CSV-Datei **exportieren**, die in Excel oder einer anderen Anwendung geöffnet, angezeigt oder geändert werden kann.

<!---HONumber=July15_HO2-->