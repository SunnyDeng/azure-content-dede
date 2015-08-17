<properties
   pageTitle="Anzeigen von Warnungen in Operations Manager"
   description="Erhalten Sie Informationen zum Verwalten von Operations Manager-Warnungen für überwachte Servern in Ihrer Infrastruktur"
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
   ms.date="08/05/2015"
   ms.author="banders" />




# Anzeigen von Operations Manager-Warnungen

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Bevor Sie Alert Management in Microsoft Azure Operational Insights verwenden können, müssen Sie die Lösung installiert haben. Weitere Informationen zum Installieren von Lösungen finden Sie unter [Einrichten Ihres Arbeitsbereichs](operational-insights-setup-workspace.md). Die Lösung funktioniert nur, wenn Sie Ihre Server mit Operations Manager-Agents überwacht werden. Weitere Informationen zur Verwendung von Operations Manager mit Operational Insights finden Sie unter [Verbinden mit Operational Insights von System Center Operations Manager aus](operational-insights-connect-scom.md).

Nach der Installation der Lösung können Sie Warnungen für die überwachten Server in Operational Insights über die Kachel **Alert Management** im Dashboard **Übersicht** anzeigen.

![Abbildung der Alert Management-Kachel](./media/operational-insights-alerts/overview-alert.png)


Sie können Microsoft Azure Operational Insights-Warnungen im Dashboard **Warnungen** anzeigen und verwalten. Im Dashboard werden Warnungsinformationen in den folgenden Kategorien angezeigt:

- Aktive Warnungen
	- Kritische Warnungen
	- Warnungen
	- Warnungsquellen
- Alle aktiven Warnungen
	- Zeigt den Prozentsatz der Warnungen an, die kritisch sind oder nur der Warnung bzw. der Information dienen.
- Allgemeine Warnungsabfragen
	- Dieser Bereich enthält vorgefertigte Abfragen, die vom Operational Insights-Softwareentwicklungsteams erstellt wurden, um Ihnen den Einstieg in Warnungen zu erleichtern.


Warnungen zeigen, dass ein Problem erkannt wurde, welcher Server von der Warnung betroffen ist, die Priorität sowie den Namen der Warnung.

![Abbildung des Warnungs-Dashboards](./media/operational-insights-alerts/alert-drilldown1.png)

![Abbildung des Warnungs-Dashboards](./media/operational-insights-alerts/alert-drilldown2.png)


Im Dashboard **Alert Management** können Sie alle Warnungen anzeigen, die Microsoft Azure Operational Insights vorgefunden hat.

## So zeigen Sie Operational Insights-Warnungen an

1. Klicken Sie auf der Seite **Übersicht** auf die Kachel **Alert Management**.
2. Zeigen Sie im Dashboard **Alert Management** die Warnungskategorien an, und wählen Sie diejenige aus, mit der Sie arbeiten möchten.
3. Klicken Sie auf eine Kachel oder ein Element, um detaillierte Informationen auf der Seite **Search** anzuzeigen.
4. Anhand der gefundenen Informationen können Sie die Warnung untersuchen und zusätzliche Aktionen ermitteln, die Sie zur Fehlerbehebung durchführen müssen.

[AZURE.INCLUDE [operational-insights-export](../../includes/operational-insights-export.md)]

<!---HONumber=August15_HO6-->