<properties 
	pageTitle="Aktivieren von Überwachung und Diagnose" 
	description="Erfahren Sie, wie Sie die Diagnose für Ihre Ressourcen in Azure einrichten." 
	authors="stepsic-microsoft-com" 
	manager="ronmart" 
	editor="" 
	services="azure-portal" 
	documentationCenter="na"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="stepsic"/>

# Aktivieren von Überwachung und Diagnose

Im [Azure-Portal](http://portal.azure.com) können Sie eine umfangreiche, regelmäßig durchgeführte Erfassung von Überwachungs- und Diagnosedaten konfigurieren. Es ist auch möglich, die Diagnose mit [REST-API](https://msdn.microsoft.com/library/azure/dn931932.aspx) oder [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) programmgesteuert zu konfigurieren.

Diagnose-, Überwachungs- und Metrikdaten werden in Azure in einem Speicherkonto Ihrer Wahl gespeichert. Auf diese Weise können Sie zum Lesen der Daten ein beliebiges Tool einsetzen – von einem Speicher-Explorer über Power BI bis hin zu einem Drittanbietertool.

## Beim Erstellen einer Ressource

Die meisten Dienste ermöglichen es Ihnen, die Diagnose bei der ersten Erstellung im [Azure-Portal](http://portal.azure.com) zu aktivieren.

1. Navigieren Sie zu **Neu**, und wählen Sie die gewünschte Ressource. 

2. Wählen Sie **Optionale Konfiguration**. ![Blatt "Diagnose"](./media/insights-how-to-use-diagnostics/Insights_CreateTime.png)

3. Wählen Sie **Diagnose**, und klicken Sie auf **Ein**. Sie müssen das Speicherkonto auswählen, in dem die Diagnosedaten gespeichert werden sollen. Ihnen werden normale Datenraten für Speicherung und Transaktionen in Rechnung gestellt, wenn Sie Diagnoseinformationen an ein Speicherkonto senden.

4. Klicken Sie auf **OK**, und erstellen Sie die Ressource.

## Ändern der Einstellungen für eine vorhandene Ressource

Wenn Sie bereits eine Ressource erstellt haben und die Diagnoseeinstellungen für diese Ressource ändern möchten (um beispielsweise den Umfang der Datensammlung zu ändern), können Sie diese Änderung direkt im Azure-Portal durchführen.

1. Wechseln Sie zur Ressource, und klicken Sie auf **Einstellungen**.

2. Wählen Sie **Diagnose**.

3. Auf dem Blatt **Diagnose** werden alle Diagnose- und Überwachungsdatensammlungen angezeigt, die für diese Ressource konfiguriert werden können. Für einige Ressourcen können Sie auch eine Richtlinie zur **Aufbewahrung** der Daten festlegen, um eine Bereinigung Ihres Speicherkontos durchzuführen. ![Speicherdiagnose](./media/insights-how-to-use-diagnostics/Insights_StorageDiagnostics.png)

4. Nachdem Sie die gewünschten Einstellungen ausgewählt haben, klicken Sie auf **Speichern**. Es kann nach dem ersten Aktivieren etwas Zeit in Anspruch nehmen, bis Überwachungsdaten angezeigt werden.

### Kategorien von Datensammlungen für virtuelle Computer
Für virtuelle Computer werden alle Metriken und Protokolle in Intervallen von 1 Minute aufgezeichnet, sodass Sie stets über aktuelle Daten zu Ihrem Computer verfügen.

- **Basismetriken**: Metriken zur Integrität des virtuellen Computers, z. B. für Prozessor und Arbeitsspeicher. 
- **Netzwerk- und Webmetriken**: Metriken zu Ihren Netzwerkverbindungen und Webdiensten.
- **.NET-Metriken**: Metriken zu den .NET- und ASP.NET-Anwendungen, die auf Ihrem virtuellen Computer ausgeführt werden.
- **SQL-Metriken**: Wenn Sie Microsoft SQL Service ausführen, werden die zugehörigen Leistungsmetriken angezeigt.
- **Anwendungsprotokolle zu Windows-Ereignissen**: Windows-Ereignisse, die an den Anwendungskanal gesendet werden.
- **Systemprotokolle zu Windows-Ereignissen**: Windows-Ereignisse, die an den Systemkanal gesendet werden. Dazu gehören auch alle Ereignisse von [Microsoft Antimalware](http://go.microsoft.com/fwlink/?LinkID=404171&clcid=0x409). 
- **Sicherheitsprotokolle zu Windows-Ereignissen**: Windows-Ereignisse, die an den Sicherheitskanal gesendet werden.
- **Infrastrukturprotokolle der Diagnose**: Protokolle zur Sammlungsinfrastruktur für die Diagnose.
- **IIS-Protokolle**: Protokolle zu Ihrem IIS-Server.

Beachten Sie, dass derzeit bestimmte Linux-Distributionen nicht unterstützt werden, und der Gast-Agent muss auf dem virtuellen Computer installiert sein.

## Nächste Schritte

* [Empfangen von Warnbenachrichtigungen](insights-receive-alert-notifications.md), wenn ein Vorgangsereignis auftritt oder Metriken einen Schwellenwert überschreiten.
* [Überwachen von Dienstmetriken](insights-how-to-customize-monitoring.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Automatisches Skalieren der Instanzenzahl](insights-how-to-scale.md), um sicherzustellen, dass Ihr Dienst verfügbar und reaktionsfähig ist.
* [Überwachen der Anwendungsleistung](insights-perf-analytics.md), um präzise Informationen zur Leistung Ihres Codes in der Cloud zu ermitteln.
* [Anzeigen von Ereignissen und Überwachungsprotokollen](insights-debugging-with-events.md), um sich über sämtliche Aktivitäten Ihres Diensts zu informieren.
* [Nachverfolgen der Dienstintegrität](insights-service-health.md), um den Zeitpunkt von Leistungsabfällen oder Dienstunterbrechungen zu ermitteln, die in Azure aufgetreten sind. 
 

<!---HONumber=July15_HO4-->