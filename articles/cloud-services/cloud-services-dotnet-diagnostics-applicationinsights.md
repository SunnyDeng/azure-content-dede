<properties
   pageTitle="Verwenden von Application Insights mit Azure-Diagnose zum Beheben von Clouddienstproblemen | Microsoft Azure"
   description="Erfahren Sie mehr über das Beheben von Clouddienstproblemen mithilfe von Application Insights zum Verarbeiten von Daten aus der Azure-Diagnose."
   services="cloud-services"
   documentationCenter=".net"
   authors="sbtron"
   manager=""
   editor="tysonn" />
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/16/2015"
   ms.author="saurabh" />


# Verwenden von Application Insights mit Azure-Diagnose zum Beheben von Clouddienstproblemen

Mit [Azure SDK 2.8](https://azure.microsoft.com/downloads/) und Azure-Diagnose-Erweiterung 1.5 können Sie jetzt Ihre Azure-Diagnose-Daten für Ihren Clouddienst direkt an Application Insights senden. Die verschiedenen von der Azure-Diagnose erfassten Protokolltypen, darunter Anwendungsprotokolle, Windows-Ereignisprotokolle, ETW-Protokolle und Leistungsindikatoren können jetzt an Application Insights gesendet und in der Benutzeroberfläche des Application Insights-Portals visualisiert werden. Bei der gemeinsamen Nutzung mit dem Application Insights-SDK können Sie nun Einblicke in die Metriken und Protokolle von der Anwendung sowie in die Daten der System- und Infrastrukturebene von Azure-Diagnose erhalten.
  
## Konfigurieren der Azure-Diagnose zum Senden von Daten an Application Insights

Führen Sie diese Schritte aus, um Ihr Clouddienstprojekt einzurichten, um Azure-Diagnose-Daten an Application Insights zu senden.

1) Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf eine Rolle, und wählen Sie **Eigenschaften** aus, um den Rollendesigner zu öffnen.
	
![Rolleneigenschaften des Projektmappen-Explorers][1]

2) Wählen Sie im Rollendesigner im Diagnose -Abschnitt das Kontrollkästchen **Diagnosedaten an Application Insights senden** aus.

![Rollendesigner sendet Diagnosedaten an Application Insights][2]

3) Wählen Sie im Dialogfeld, das angezeigt wird, die Application Insights-Ressource aus, an die Sie die Azure-Diagnose-Daten senden möchten. Im Dialogfeld können Sie eine vorhandene Application Insights-Ressource aus Ihrem Abonnement auswählen oder manuell einen Instrumentierungsschlüssel für eine Application Insights-Ressource angeben. Wenn Sie keine vorhandene Application Insights-Ressource haben, können Sie eine erstellen, indem Sie auf den Link **Neue Ressource erstellen** klicken, dadurch wird ein Browserfenster mit dem klassischen Azure-Portal geöffnet, in dem Sie eine Application Insights-Ressource erstellen können. Weitere Informationen zum Erstellen einer Application Insights-Ressource finden Sie unter [Erstellen einer neuen Application Insights-Ressource](app-insights-create-new-resource.md).

![Auswählen einer Application Insights-Ressource][3]

4) Wenn Sie die Application Insights-Ressource hinzugefügt haben, wird der Instrumentierungsschlüssel für diese Ressource als Dienstkonfigurationseinstellung mit dem Namen **APPINSIGHTS\_INSTRUMENTATIONKEY** gespeichert. Sie können diese Konfigurationseinstellung für jede Dienstkonfiguration oder Umgebung ändern, indem Sie eine andere Konfiguration aus der Dropdownliste „Dienstkonfiguration“ auswählen und einen neuen Instrumentierungsschlüssel für diese Konfiguration angeben.

![Auswählen einer Dienstkonfiguration][4]
	
Die **APPINSIGHTS\_INSTRUMENTATIONKEY**-Konfigurationseinstellung wird von Visual Studio zum Konfigurieren der Diagnose-Erweiterung mit den entsprechenden Application Insights-Ressourceninformationen während der Veröffentlichung verwendet. Die Konfigurationseinstellung ist eine bequeme Möglichkeit, die verschiedenen Instrumentierungsschlüssel für verschiedene Dienstkonfigurationen zu definieren. Visual Studio übersetzt diese Einstellung und fügt sie beim Veröffentlichen in die öffentliche Konfiguration der Diagnose-Erweiterung ein. Um die Konfiguration der Diagnose-Erweiterung mit PowerShell zu vereinfachen, enthält die Paketausgabe von Visual Studio auch die öffentliche Konfigurations-XML mit dem entsprechenden Application Insights-Instrumentierungsschlüssel. Die öffentlichen Konfigurationsdateien werden im Extensions-Ordner erstellt und haben das folgende Namensgebungsmuster: "PaaSDiagnostics.<RoleName>. PubConfig.xml". Dieses Muster kann von allen PowerShell-basierten Bereitstellungen verwendet werden, um die einzelnen Konfigurationen einer Rolle zuzuordnen.

5) Durch Aktivieren von **Diagnosedaten an Application Insights senden** wird Azure-Diagnose automatisch konfiguriert, damit alle Leistungsindikatoren und Protokolle mit dem Error-Protokolliergrad, die vom Azure-Diagnose-Agent gesammelt werden, an Application Insights gesendet werden. Wenn Sie zusätzlich konfigurieren möchten, welche Daten an Application Insights gesendet werden, müssen Sie die *diagnostics.wadcfgx*-Datei für jede Rolle manuell bearbeiten. Weitere Informationen zum manuellen Aktualisieren der Konfiguration finden Sie unter [Konfigurieren von Azure-Diagnose zum Senden von Daten an Application Insights](azure-diagnostics-configure-applicationinsights.md).

Sobald der Clouddienst so konfiguriert wurde, dass Azure-Diagnose-Daten an Application Insights gesendet werden, können Sie ihn wie gewohnt in Azure bereitstellen. Stellen Sie dabei sicher, dass die Azure-Diagnose-Erweiterung aktiviert ist. Siehe [Veröffentlichen eines Clouddiensts mit Visual Studio](vs-azure-tools-publishing-a-cloud-service.md).

## Anzeigen von Azure-Diagnose-Daten in Application Insights
Die Azure-Diagnose-Telemetrie wird in der Application Insights-Ressource angezeigt, die für den Clouddienst konfiguriert wurde.

Im Folgenden wird gezeigt, wie die verschiedenen Azure-Diagnose-Protokolltypen den Application Insights-Konzepten zugeordnet werden:

-  Leistungsindikatoren werden als benutzerdefinierte Metriken in Application Insights angezeigt.
-  Windows-Ereignisprotokolle werden als Ablaufverfolgungen und benutzerdefinierte Ereignisse in Application Insights angezeigt.
-  Anwendungsprotokolle, ETW-Protokolle und alle Diagnose-Infrastrukturprotokolle werden als Ablaufverfolgungen in Application Insights angezeigt.

So zeigen Sie Azure-Diagnose-Daten in Application Insights an:

- Verwenden Sie den [Metrik-Explorer](../application-insights/app-insights-metrics-explorer.md), um alle benutzerdefinierten Leistungsindikatoren oder die Anzahl verschiedener Arten von Windows-Ereignisprotokollereignissen zu visualisieren.

![Benutzerdefinierte Metriken im Metrik-Explorer][5]

- Verwenden Sie die [Suche](../application-insights/app-insights-diagnostic-search.md), um die verschiedenen Ablaufverfolgungsprotokolle zu durchsuchen, die von Azure-Diagnose gesendet werden. Wenn Sie beispielsweise über eine nicht bearbeitete Ausnahme in einer Rolle verfügen, aufgrund dessen die Rolle abstürzt und wiederverwendet wird, werden diese Informationen im *Application*-Kanal des *Windows-Ereignisprotokolls* angezeigt. Mit der Suchfunktion können Sie den Windows-Ereignisprotokollfehler anzeigen und die vollständige Stapelüberwachung für die Ausnahme abrufen, um die Ursache des Problems zu ermitteln. 

![Durchsuchen von Ablaufverfolgungen][6]

## Nächste Schritte

- [Fügen Sie dem Clouddienst das Application Insights-SDK hinzu](../application-insights/app-insights-cloudservices.md), um Daten zu Anforderungen, Ausnahmen, Abhängigkeiten und einer benutzerdefinierten Telemetrie aus Ihrer Anwendung zu senden. Zusammen mit den Azure-Diagnose-Daten erhalten Sie eine umfassende Ansicht Ihrer Anwendung und des Systems in der gleichen Application Insights-Ressource.  


<!--Image references-->
[1]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/solution-explorer-properties.png
[2]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-sendtoappinsights.png
[3]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/select-appinsights-resource.png
[4]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/role-designer-appinsights-serviceconfig.png
[5]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/metrics-explorer-custom-metrics.png
[6]: ./media/cloud-services-dotnet-diagnostics-applicationinsights/search-windowseventlog-error.png

<!---HONumber=AcomDC_1203_2015-->