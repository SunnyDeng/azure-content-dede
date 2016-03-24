<properties 
	pageTitle="Verwenden von io.js mit Azure App Service-Web-Apps" 
	description="Erfahren Sie, wie Sie eine Web-App in Azure App Service mit io.js verwenden." 
	services="app-service\web" 
	documentationCenter="nodejs" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="03/04/2016"
	ms.author="robmcm" />

# Verwenden von io.js mit Azure App Service-Web-Apps

Der beliebte Fork von Node.js, [io.js], unterscheidet sich in verschiedenen Punkten vom Node.js-Projekt von Joyent, einschließlich eines offeneren Prozessführungsmodells, eines schnelleren Versionszyklus und einer schnelleren Einführung neuer und experimenteller JavaScript-Funktionen.

Während in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps viele Node.js-Versionen vorinstalliert sind, lassen sie auch die Verwendung einer vom Benutzer bereitgestellten Node.js-Binärdatei zu. In diesem Artikel werden zwei Methoden zur Aktivierung von io.js in App Service-Web-Apps beschrieben: Die Verwendung eines erweiterten Bereitstellungsskripts, das Azure automatisch für die neueste verfügbare Version von io.js konfiguriert, sowie das manuelle Hochladen einer io.js-Binärdatei.

<a id="deploymentscript"></a>
## Verwenden eines Bereitstellungsskripts

Bei der Bereitstellung einer Node.js-App führen App Service-Web-Apps eine Reihe kleiner Befehle aus, um sicherzustellen, dass die Umgebung ordnungsgemäß konfiguriert ist. Mit einem Bereitstellungsskript kann dieser Prozess angepasst werden, sodass er das Herunterladen und die Konfiguration der Datei "io.js" umfasst.

Das [io.js-Bereitstellungsskript](https://github.com/felixrieseberg/iojs-azure) ist auf GitHub verfügbar. Um io.js in Ihrer Web-App zu aktivieren, kopieren Sie einfach **.deployment**, **deploy.cmd** und **IISNode.yml** in das Stammverzeichnis des Anwendungsordners und stellen die Dateien für Web-Apps bereit.

Die erste Datei, **.deployment**, weist Web-Apps an, **deploy.cmd** bei der Bereitstellung auszuführen. Dieses Skript führt alle üblichen Schritte für eine Node.js-Anwendung aus, lädt jedoch auch die neueste Version von „io.js“ herunter. **IISNode.yml** schließlich konfiguriert die Web-Apps für die Verwendung der soeben heruntergeladenen io.js-Binärdatei anstelle einer vorinstallierten Node.js-Binärdatei.

> [AZURE.NOTE] Um die verwendete io.js-Binärdatei zu aktualisieren, stellen Sie die Anwendung einfach erneut bereit. Das Skript lädt bei jeder Bereitstellung der Anwendung eine neue Version der Datei "io.js" herunter.

<a id="manualinstallation"></a>
## Verwenden der manuellen Installation

Die manuelle Installation einer benutzerdefinierten io.js-Version umfasst nur zwei Schritte. Laden Sie zunächst die **win-x64**-Binärdatei direkt aus der [io.js-Distribution] herunter. Zwei Dateien werden benötigt: **iojs.exe** und **iojs.lib**. Speichern Sie beide Dateien in einem Ordner innerhalb Ihrer Web-App, z. B. in **bin/iojs**.

Um Web-Apps zur Verwendung von **iojs.exe** anstelle einer vorinstallierten Node-Version zu konfigurieren, erstellen Sie die Datei **IISNode.yml** im Stammverzeichnis der Anwendung und fügen in dieser die folgende Zeile hinzu.

    nodeProcessCommandLine: "D:\home\site\wwwroot\bin\iojs\iojs.exe"

<a id="nextsteps"></a>
## Nächste Schritte

In diesem Artikel haben Sie erfahren, wie io.js sowohl mithilfe der vorhandenen Bereitstellungsskripts als auch über eine manuelle Installation mit App Service- Web-Apps verwendet wird.

> [AZURE.NOTE] io.js wird ständig weiterentwickelt und häufiger aktualisiert als Node.js. Einige Node.js-Module funktionieren möglicherweise nicht mit io.js. Hinweise zur Problembehandlung erhalten Sie unter [io.js auf GitHub].

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

[io.js]: https://iojs.org
[io.js-Distribution]: https://iojs.org/dist/
[io.js auf GitHub]: https://github.com/iojs/io.js
[io.js Deployment Script]: https://github.com/felixrieseberg/iojs-azure
 

<!---HONumber=AcomDC_0309_2016-->