<properties 
   pageTitle="Identifizieren von Unterschieden mit Change Tracking"
   description="Verwenden Sie das Intelligence Pack zur Nachverfolgung von Konfigurationsänderungen in Microsoft Azure Operational Insights, um ganz einfach Änderungen an Software und Windows-Diensten zu identifizieren, die in Ihrer Umgebung vorgenommen werden"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Identifizieren von Unterschieden mit Change Tracking

Mit dem Intelligence Pack zur Nachverfolgung von Konfigurationsänderungen in Microsoft Azure Operational Insights können Sie ganz einfach Änderungen an Software und Windows-Diensten identifizieren, die in Ihrer Umgebung vorgenommen werden - durch das Feststellen solcher Konfigurationsänderungen können Sie Betriebsprobleme leichter lokalisieren.

Sie installieren das Intelligence Pack, um den Operations Manager-Agent und das Basiskonfigurationsmodul für Operational Insights zu aktualisieren. Änderungen an installierter Software und Windows-Diensten auf den überwachten Servern werden gelesen, und die Daten werden zur Verarbeitung an den Operational Insights-Dienst in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Cloud-Dienst zeichnet die Daten auf. Wenn Änderungen gefunden werden, werden die Server mit den Änderungen im Change Tracking-Dashboard angezeigt. Mithilfe der Informationen im Change Tracking-Dashboard können Sie ganz leicht die Änderungen erkennen, die in Ihrer Serverinfrastruktur vorgenommen wurden.

## Verwenden von Change Tracking

Bevor Sie Change Tracking in Operational Insights verwenden können, müssen Sie das Intelligence Pack installiert haben. Weitere Informationen zum Installieren von Intelligence Packs finden Sie unter [Verwenden der Galerie zum Hinzufügen oder Entfernen von Intelligence Packs](operational-insights-add-intelligence-packs.md). 

Nach der Installation können Sie die Zusammenfassung der Änderungen für die überwachten Server in Operational Insights über die Kachel **Change Tracking** auf der Seite **Übersicht** anzeigen. 

![Abbildung der Kachel "Change Tracking"](./media/operational-insights-change-tracking/overview-change-track.png)

Sie können Änderungen an Ihrer Infrastruktur anzeigen und dann Einzelheiten für die folgenden Kategorien abrufen:

- Änderungen nach Konfigurationstyp für Software und Windows-Dienste

- Softwareänderungen an Anwendungen und Updates für einzelne Server

- Gesamtanzahl von Softwareänderungen für jede Anwendungen

- Änderungen am Windows-Dienst für einzelne Server

![Abbildung des Change Tracking-Dashboards](./media/operational-insights-change-tracking/gallery-changetracking-01.png)
![Abbildung des Change Tracking-Dashboards](./media/operational-insights-change-tracking/gallery-changetracking-02.png)

### So zeigen Sie Änderungen für Änderungstypen an

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Change Tracking**.

2. Prüfen Sie im Dashboard **Change Tracking** die Zusammenfassungsinformationen in einem der Blätter mit den Änderungstypen, und klicken Sie dann auf einen Eintrag, um detaillierte Informationen auf der Seite **Search** anzuzeigen.

3. Sie können auf jeder Search-Seite die Ergebnisse nach Zeit, detaillierte Ergebnisse und Ihren Suchverlauf anzeigen. Außerdem können Sie nach Facets filtern, um die Ergebnisse einzugrenzen.

4. Auf jeder Seite in Search können Sie die Ergebnisdetails in eine CSV-Datei **exportieren**, die in Excel oder einer anderen Anwendung geöffnet, angezeigt oder geändert werden kann.


<!--HONumber=52-->