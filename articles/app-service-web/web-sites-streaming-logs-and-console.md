<properties 
	pageTitle="Streamingprotokolle und Konsole" 
	description="Übersicht über Streamingprotokolle und die Konsole"
	authors="adamabdelhamed" 
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
	ms.date="04/25/2015" 
	ms.author="adamab"/>

#Streamingprotokolle und die Konsole

### Streamingprotokolle ###

Die [Azure Preview Portal](http://go.microsoft.com/fwlink/?LinkId=529715) bietet ein integriertes Streamingprotokoll, mit dem Sie die Ablaufverfolgung von Ereignissen aus anzeigen kann Ihre [Azure Anwendungsdiensts](http://go.microsoft.com/fwlink/?LinkId=529714) web-apps in Echtzeit.

Gehen Sie wie folgt vor, um diese Funktion einzurichten:

- Schreiben Sie Ablaufverfolgungen in Ihren Code.
- Aktivieren Sie Application Diagnostics aus der Azure-Vorschau-Portal
- Klicken Sie auf den streaming-Protokolle Kachel auf die Web-app-blade

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

Diagnosen werden pro Web-App aktiviert.

1. Innerhalb der [Azure-vorschauportal](https://portal.azure.com), klicken Sie auf **Durchsuchen** > **Webanwendungen** um die Liste aller Web-Apps zu erreichen.  

2. Klicken Sie auf den Namen der Web-App, die Sie konfigurieren möchten.

3. Klicken Sie auf **alle Einstellungen** > **Diagnoseprotokolle**, und schalten Sie die **Anwendungsprotokollierung (Dateisystem)** wechseln Sie in der **auf** Einstellung.

4. Es wird die Option **Schweregrad** angezeigt. Hier können Sie den Schweregrad der festgelegten Ablaufverfolgungen festlegen. Sie sollten hier **Ausführlich** festlegen, wenn es nur darum geht, sich mit der Funktion vertraut zu machen, da so sichergestellt werden kann, dass alle Ablaufverfolgungen protokolliert werden.

5. Klicken Sie auf **Speichern** am oberen Rand der Blade jetzt können Sie Protokolle anzeigen.

6. Anzeigen der Streamingprotokolle aus in das Portal klicken Sie auf die **Streamingprotokolle** Kachel auf die Web-app-Blade. Verzeichnet Ihre App aktiv Ablaufverfolgungsmeldungen, sollten Ihnen diese in Echtzeit im Ergebnisfenster angezeigt werden.

![][StreamingLogsScreenshot]

## Zugriff auf die Konsole ##

Das Azure Preview Portal bietet Zugriff auf Ihre Webanwendung Konsole. Sie können das Dateisystem Ihrer Web-App untersuchen und PowerShell-/CMD-Skripts ausführen. Sie müssen sich beim Ausführen von Konsolenbefehlen an die gleichen Berechtigungen wie der ausgeführte Web-App-Code halten. Sie können nicht auf geschützte Verzeichnisse zugreifen oder Skripts ausführen, die erhöhte Rechte erfordern.

1. Navigieren Sie zu einer Webanwendung Blade wie im vorstehenden Abschnitt beschrieben.

2. Klicken Sie auf die **Konsole** sowie für die Konsole wird geöffnet.

![][ConsoleScreenshot]

Um mit der Konsole vertraut zu machen, führen Sie grundlegende Befehle wie die folgenden aus:

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->