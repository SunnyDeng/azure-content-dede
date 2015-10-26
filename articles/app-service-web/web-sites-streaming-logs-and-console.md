<properties 
	pageTitle="Streamingprotokolle und Konsole" 
	description="Übersicht über Streamingprotokolle und die Konsole" 
	authors="btardif" 
	manager="wpickett" 
	editor="" 
	services="app-service\web" 
	documentationCenter=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/10/2015" 
	ms.author="byvinyal"/>

#Streamingprotokolle und die Konsole

### Streamingprotokolle ###

Das Microsoft Azure-Portal bietet ein integriertes Streamingprotokoll, über das Sie Ablaufverfolgungsereignisse von Ihren Web-Apps im Azure App Service in Echtzeit anzeigen können.

Gehen Sie wie folgt vor, um diese Funktion einzurichten:

- Schreiben Sie Ablaufverfolgungen in Ihren Code.
- Aktivieren Sie die Anwendungsdiagnose über das Azure-Portal.
- Klicken Sie auf dem Blatt der Web-App auf den Abschnitt "Streamingprotokolle".

### So schreiben Sie Ablaufverfolgungen in den Code: ###

Ablaufverfolgungen in den Code zu schreiben ist einfach. In C# könnte dieser Code z. B. wie folgt aussehen:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Die Ablaufverfolgungsklasse befindet sich in der System Diagnostics-Namespace.

In einer node.js-App können Sie folgenden Code schreiben, um das gleiche Ergebnis zu erhalten:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Aktivieren und Anzeigen der Streamingprotokolle ###

![][BrowseSitesScreenshot] Diagnosen werden pro Web-App aktiviert. Klicken Sie im [Portal](https://portal.azure.com) auf der Menüleiste links auf die Schaltfläche **Durchsuchen (1)** und dann auf **Web-Apps (2)**, um eine **Liste (3)** all Ihrer Web-Apps anzuzeigen.

Klicken Sie auf den Namen der Web-App, die Sie konfigurieren möchten.
  
![][DiagnosticsLogs] Klicken Sie dann auf **Einstellungen (1)** > **Diagnoseprotokolle (2)**, und **aktivieren** Sie **Anwendungsprotokollierung (Dateisystem)(3)**. Mit der Option **Ebene** können Sie den Schweregrad der Ablaufverfolgungen ändern, die erfasst werden sollen. Sie sollten hier **Ausführlich** festlegen, wenn es nur darum geht, sich mit der Funktion vertraut zu machen, da so sichergestellt werden kann, dass alle Ablaufverfolgungen protokolliert werden.

Klicken Sie oben auf dem Blade auf **SPEICHERN**, um die Protokolle anzuzeigen.

**HINWEIS:** Je höher Sie den **Schweregrad** einstellen, desto mehr Ressourcen werden für die Protokollierung belegt und desto mehr Ablaufverfolgungen erhalten Sie. Stellen Sie sicher, dass dieser Wert auf die angemessene Ebene festgelegt ist, wenn Sie dieses Feature für eine Website mit hohem Datenverkehr oder eine Produktionswebsite verwenden.

![][StreamingLogsScreenshot] Zum Anzeigen der Streamingprotokolle im Portal klicken Sie auf **Tools (1)** > **Protokollstream (2)**. Verzeichnet Ihre App aktiv Ablaufverfolgungsmeldungen, sollten Ihnen diese in Echtzeit im Ergebnisfenster angezeigt werden.

## Konsole ##

Das Azure-Portal bietet Konsolenzugriff auf Ihre Web-App-Umgebung. Sie können das Dateisystem Ihrer Web-App untersuchen und PowerShell-/CMD-Skripts ausführen. Sie müssen sich beim Ausführen von Konsolenbefehlen an die gleichen Berechtigungen wie der ausgeführte Web-App-Code halten. Sie können nicht auf geschützte Verzeichnisse zugreifen oder Skripts ausführen, die erhöhte Rechte erfordern.

![][ConsoleScreenshot] Wechseln Sie wie oben beschrieben zu einer Web-App, um auf die Konsole zuzugreifen. Klicken Sie auf **Tools** > **Konsole**, um die Konsole zu öffnen.

Probieren Sie grundlegende Befehle wie die folgenden aus, um sich mit der Konsole vertraut zu machen:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[DiagnosticsLogs]: ./media/web-sites-streaming-logs-and-console/diagnostic-logs.png
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!---HONumber=Oct15_HO3-->