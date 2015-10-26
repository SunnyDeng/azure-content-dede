<properties 
	pageTitle="Überwachen von Dienstmetriken" 
	description="Erfahren Sie, wie Sie Überwachungsdiagramme in Azure anpassen." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal"
documentationCenter=""/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/08/2015" 
	ms.author="stepsic"/>

# Überwachen von Dienstmetriken

Alle Azure-Dienste verfolgen wichtige Metriken, die Ihnen das Überwachen der Integrität, Leistung, Verfügbarkeit und Nutzung Ihrer Dienste ermöglichen. Sie können diese Metriken im Azure-Portal anzeigen, und Sie können darüber hinaus mithilfe von [REST-API](https://msdn.microsoft.com/library/azure/dn931930.aspx) oder [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) programmgesteuert auf sämtliche Metriken zugreifen.

Für einige Dienste müssen Sie möglicherweise die Diagnose aktivieren, um Metriken anzeigen zu können. Für andere Dienste, z. B. virtuelle Computer, wird ein grundlegender Satz an Metriken bereitgestellt, die Hochfrequenzmetriken müssen jedoch aktiviert werden. Weitere Informationen erhalten Sie unter [Aktivieren von Überwachung und Diagnose](insights-how-to-use-diagnostics.md).

## Verwenden von Überwachungsdiagrammen 

Sie können für beliebige Metriken und einen gewählten Zeitraum ein Diagramm erstellen.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com/) auf **Durchsuchen** und anschließend auf eine Ressource, die Sie überwachen möchten.

2. Der Bereich **Überwachen** enthält die wichtigsten Metriken zu jeder Azure-Ressource. Für eine Web-App ist dies beispielsweise **Anforderungen und Fehler**, während für einen virtuellen Computer **CPU-Prozentsatz** und **Lese- und Schreibvorgänge auf dem Datenträger** angezeigt wird: ![Überwachungsfokus](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Wenn Sie auf eines dieser Diagramme klicken, wird das Blatt **Metrik** geöffnet. Auf diesem Blatt wird neben dem Diagramm eine Tabelle angezeigt, die Aggregationen der Metriken enthält (z. B. Durchschnitt, Mindest- und Maximalwert für den gewählten Zeitraum). Darunter werden die Warnregeln für die Ressource angezeigt. ![Fenster mit Metriken](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Zum Anpassen der angezeigten Zeilen klicken Sie auf die Schaltfläche **Bearbeiten** im Diagramm, oder klicken Sie auf den Befehl **Bearbeiten** auf dem Blatt "Metrik".

5. Auf dem Blatt "Abfrage bearbeiten" haben Sie drei Möglichkeiten:
    - Ändern des Zeitraums
    - Wechseln zwischen der Darstellung "Balken" und "Linie"
    - Auswählen anderer Metriken ![Abfrage bearbeiten](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Die Änderung des Zeitbereichs ist denkbar einfach: Sie wählen einen anderen Bereich aus (beispielsweise **Letzte Stunde**) und klicken unten im Fenster auf **Speichern**. Mit der Einstellung **Benutzerdefiniert** können Sie einen beliebigen Zeitraum in den letzten zwei Wochen auswählen. Beispielsweise können Sie die gesamten letzten zwei Wochen oder lediglich eine Stunde gestern anzeigen. Geben Sie in das Textfeld eine andere Uhrzeit ein. ![Benutzerdefinierter Zeitbereich](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Unterhalb des Zeitbereichs können Sie beliebig viele Metriken auswählen, die in dem Diagramm angezeigt werden sollen.

8. Wenn Sie auf "Speichern" klicken, werden Ihre Änderungen für die jeweilige Ressource gespeichert. Wenn Sie beispielsweise über zwei virtuelle Computer verfügen und ein Diagramm für einen der virtuellen Computer ändern, hat dies keine Auswirkungen auf den zweiten virtuellen Computer.

## Erstellen nebeneinander angezeigter Diagramme

Dank der umfangreichen Anpassungsmöglichkeiten im Portal können Sie so viele Diagramme hinzufügen, wie Sie möchten.

1. Klicken Sie im oberen Bereich des Blatts im Menü **...** auf **Kacheln hinzufügen**: ![Menü "Hinzufügen"  
](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Anschließend können Sie ein Diagramm aus dem **Katalog** auf der rechten Bildschirmseite auswählen: ![Katalog](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Wenn die gewünschte Metrik nicht angezeigt wird, können Sie einfach eine der voreingestellten Metriken hinzufügen und durch **Bearbeiten** des Diagramms die Metrik anzeigen, die Sie benötigen. 

## Überwachen mithilfe von Kontingenten

Die meisten Metriken zeigen Trends im Zeitverlauf an, für bestimmte Daten – beispielsweise für Nutzungskontingente – werden jedoch Informationen zu einem bestimmten Zeitpunkt mit einem Schwellenwert angezeigt.

Für Ressourcen mit Kontingenten können Sie Nutzungskontingente auch auf dem Blatt für Ressourcen anzeigen:

![Verwendung](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Wie bei den Metriken können Sie mithilfe von [REST-API](https://msdn.microsoft.com/library/azure/dn931963.aspx) oder [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) programmgesteuert auf den vollständigen Satz an Nutzungskontingenten zugreifen.

## Nächste Schritte

* [Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md), wenn eine Metrik einen Schwellenwert überschreitet.
* [Aktivieren von Überwachung und Diagnose](insights-how-to-use-diagnostics.md), um detaillierte Hochfrequenzmetriken zu Ihrem Dienst zu sammeln.
* [Automatisches Skalieren der Instanzenanzahl](insights-how-to-scale.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Überwachen der Anwendungsleistung](insights-perf-analytics.md), um präzise Informationen zur Leistung Ihres Codes in der Cloud zu ermitteln.
* Verwenden von [Application Insights für JavaScript-Apps und Webseiten](../app-insights-web-track-usage.md), um eine Clientanalyse über die Browser zu erhalten, mit denen auf eine Webseite zugegriffen wird.
* [Überwachen der Verfügbarkeit und Reaktionsfähigkeit einer beliebigen Webseite](../app-insights-monitor-web-app-availability.md) mit Application Insights, um zu ermitteln, ob eine Seite offline ist.
 

<!---HONumber=Oct15_HO3-->