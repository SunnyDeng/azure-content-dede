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
	ms.date="11/17/2014" 
	ms.author="adamab"/>

#Streamingprotokolle und die Konsole

### Streamingprotokolle

Das Microsoft Azure-Portal bietet ein integriertes Streamingprotokoll, über  das Sie Ablaufverfolgungsereignisse von Ihren Web-Apps im Azure App Service in Echtzeit anzeigen können.  

Gehen Sie wie folgt vor, um diese Funktion einzurichten:

- Schreiben Sie Ablaufverfolgungen in Ihren Code.
- Aktivieren Sie die Anwendungsdiagnose über das Azure-Portal.
- Klicken Sie auf dem Blatt der Web-App auf den Abschnitt "Streamingprotokolle".

### So schreiben Sie Ablaufverfolgungen in den Code

Ablaufverfolgungen in den Code zu schreiben ist einfach.  In C# könnte dieser Code z. B. wie folgt aussehen:

`````````````````````````
Trace.TraceInformation("My trace statement");
`````````````````````````

`````````````````````````
Trace.TraceWarning("My warning statement");
`````````````````````````

`````````````````````````
Trace.TraceError("My error statement");
`````````````````````````

Die Ablaufverfolgungsklasse befindet sich im System Diagnostics-Namespace.

In einer node.js-App können Sie folgenden Code schreiben, um das gleiche Ergebnis zu erhalten:

`````````````````````````
console.log("My trace statement").
`````````````````````````

### Aktivieren und Anzeigen der Streamingprotokolle

Diagnosen werden pro Web-App aktiviert.  Klicken Sie im [Portal](https://portal.azure.com) auf der Menüleiste links auf die Schaltfläche **Durchsuchen** und dann auf **Web-Apps**, um eine Liste aller Ihrer Web-Apps anzuzeigen.  

![][BrowseSitesScreenshot]

Klicken Sie auf den Namen der Web-App, die Sie konfigurieren möchten.  Klicken Sie anschließend auf den Bereich **DIAGNOSEPROTOKOLLE**, und setzen Sie den Schalter **Anwendungsprotokollierung (Dateisystem)** auf **EIN**.  Es wird die Option **Schweregrad** angezeigt. Hier können Sie den Schweregrad der festgelegten Ablaufverfolgungen festlegen.  Sie sollten hier **Ausführlich** festlegen, wenn es nur darum geht, sich mit der Funktion vertraut zu machen, da so sichergestellt werden kann, dass alle Ablaufverfolgungen protokolliert werden.

Klicken Sie oben auf dem Blatt auf **SPEICHERN**, um die Protokolle anzuzeigen.

Klicken Sie auf dem Blatt der Web-App auf **STREAMINGPROTOKOLLE**, um die Streamingprotokolle im Portal anzuzeigen.  Verzeichnet Ihre App aktiv Ablaufverfolgungsmeldungen, sollten Ihnen diese in Echtzeit im Ergebnisfenster angezeigt werden.

![][StreamingLogsScreenshot]

## Konsole

Das Azure-Portal bietet Konsolenzugriff auf Ihre Web-App-Umgebung. Sie können das Dateisystem Ihrer Web-App untersuchen und PowerShell-/CMD-Skripts ausführen.  Sie müssen sich beim Ausführen von Konsolenbefehlen an die gleichen Berechtigungen wie der ausgeführte Web-App-Code halten. Sie können nicht auf geschützte Verzeichnisse zugreifen oder Skripts ausführen, die erhöhte Rechte erfordern.  

Wechseln Sie wie oben beschrieben zu einer Web-App, um auf die Konsole zuzugreifen.  Klicken Sie auf den Abschnitt **Konsole**, um sie zu öffnen.

![][ConsoleScreenshot]

Probieren Sie grundlegende Befehle wie die folgenden aus, um sich mit der Konsole vertraut zu machen:



`````````````````````````
dir
`````````````````````````

`````````````````````````
cd
`````````````````````````



<!-- Images. -->
[BrowseSitesScreenshot]: ./media/web-sites-streaming-logs-and-console/browse-sites.png
[StreamingLogsScreenshot]: ./media/web-sites-streaming-logs-and-console/streaming-logs.png
[ConsoleScreenshot]: ./media/web-sites-streaming-logs-and-console/console.png

<!--HONumber=52-->