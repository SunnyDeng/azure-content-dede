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

Das [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715) bietet ein integriertes Streamingprotokoll, über das Sie Ablaufverfolgungsereignisse von Ihren Web-Apps in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) in Echtzeit anzeigen können.

Gehen Sie wie folgt vor, um diese Funktion einzurichten:

- Schreiben Sie Ablaufverfolgungen in Ihren Code.
- Aktivieren Sie die Anwendungsdiagnose über das Azure-Vorschauportal.
- Klicken Sie auf dem Blatt der Web-App auf die Kachel "Streamingprotokolle".

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

1. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com) auf **Durchsuchen** > **Web-Apps**, um eine Liste Ihrer Web-Apps anzuzeigen.  

2. Klicken Sie auf den Namen der Web-App, die Sie konfigurieren möchten.

3. Klicken Sie auf **Alle Einstellungen** > **Diagnoseprotokolle**, und legen Sie für **Anwendungsprotokollierung (Dateisystem)** die Einstellung **EIN** fest.

4. Es wird die Option **Schweregrad** angezeigt. Hier können Sie den Schweregrad der festgelegten Ablaufverfolgungen festlegen. Sie sollten hier **Ausführlich** festlegen, wenn es nur darum geht, sich mit der Funktion vertraut zu machen, da so sichergestellt werden kann, dass alle Ablaufverfolgungen protokolliert werden.

5. Klicken Sie oben auf dem Blatt auf **Speichern**, um die Protokolle anzuzeigen.

6. Klicken Sie auf dem Blatt der Web-App auf die Kachel **Streamingprotokolle**, um die Streamingprotokolle im Portal anzuzeigen. Verzeichnet Ihre App aktiv Ablaufverfolgungsmeldungen, sollten Ihnen diese in Echtzeit im Ergebnisfenster angezeigt werden.

![][StreamingLogsScreenshot]

## Zugriff auf die Konsole ##

Das Azure-Vorschauportal bietet Konsolenzugriff auf Ihre Web-App. Sie können das Dateisystem Ihrer Web-App untersuchen und PowerShell-/CMD-Skripts ausführen. Sie müssen sich beim Ausführen von Konsolenbefehlen an die gleichen Berechtigungen wie der ausgeführte Web-App-Code halten. Sie können nicht auf geschützte Verzeichnisse zugreifen oder Skripts ausführen, die erhöhte Rechte erfordern.

1. Navigieren Sie wie im vorherigen Abschnitt beschrieben zu dem Blatt einer Web-App.

2. Klicken Sie auf die Kachel **Konsole**, um sie zu öffnen.

![][ConsoleScreenshot]

Probieren Sie folgende grundlegende Befehle aus, um sich mit der Konsole vertraut zu machen:

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png
 

<!---HONumber=62-->