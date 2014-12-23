<properties pageTitle="Streamingprotokolle und Konsole" description="Streaming logs and console overview" title="Streaming logs and console" authors="adamab" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="adamab" />

#Streamingprotokolle und die Konsole

### Streamingprotokolle

Das Microsoft Azure-Vorschauportal bietet ein integriertes Streamingprotokoll, über das Sie Ablaufverfolgungsereignisse von Ihrer Website in Echtzeit anzeigen können.  

Gehen Sie wie folgt vor, um diese Funktion einzurichten:

- Schreiben Sie Ablaufverfolgungen in Ihren Code.
- Aktivieren Sie Application Diagnostics über das Azure-Vorschauportal.
- Klicken Sie auf der Website-Blade auf den Abschnitt "Streamingprotokolle".

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

Diagnostics werden pro Website aktiviert.  Klicken Sie im [Portal](https://portal.azure.com) links auf **Durchsuchen**, und klicken Sie dann auf **Websites**, um die Liste aller Websites anzuzeigen.  

![][BrowseSitesScreenshot]

Klicken Sie auf den Namen der Website, die Sie konfigurieren möchten.  Klicken Sie anschließend auf den Bereich **DIAGNOSTIC LOGS**, und setzen Sie den Schalter **Application Logging (Filesystem)** auf **ON**.  Es wird die Option **Schweregrad** angezeigt. Hier können Sie den Schweregrad der festgelegten Ablaufverfolgungen festlegen.  Sie sollten hier **Ausführlich** festlegen, wenn es nur darum geht, sich mit der Funktion vertraut zu machen, da so sichergestellt werden kann, dass alle Ablaufverfolgungen protokolliert werden.

Klicken Sie oben auf dem Blade auf **SPEICHERN**, um die Protokolle anzuzeigen.

Klicken Sie auf der Website-Blade auf **STREAMINGPROTOKOLLE**, um die Streamingprotokolle im Portal anzuzeigen.  Verzeichnet Ihre Seite aktiv Ablaufverfolgungsmeldungen, sollten Ihnen diese in Echtzeit im Ergebnisfenster angezeigt werden.

![][StreamingLogsScreenshot]

## Konsole

Das Azure-Vorschauportal bietet Konsolenzugriff auf Ihre Websiteumgebung. Sie können das Dateisystem Ihrer Website untersuchen und Powershell-/CMD-Skripts ausführen.  Sie müssen sich beim Ausführen von Konsolenbefehlen an die gleichen Berechtigungen wie der ausgeführte Websitecode halten. Sie können nicht auf geschützte Verzeichnisse zugreifen oder Skripts ausführen, die erhöhte Rechte erfordern.  

Gehen Sie wie oben beschrieben auf eine Website, um auf die Konsole zuzugreifen.  Klicken Sie auf den Abschnitt **Konsole**, um sie zu öffnen.

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
