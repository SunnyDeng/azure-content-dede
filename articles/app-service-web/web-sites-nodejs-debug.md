<properties
	pageTitle="Debuggen einer Node.js-Web-App in Azure App Service"
	description="Erfahren Sie, wie Sie eine Node.js-Web-App in Azure App Service debuggen."
	tags="azure-portal"
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="article"
	ms.date="04/23/2015"
	ms.author="mwasson"/>

# Debuggen einer Node.js-Web-App in Azure App Service

Azure bietet integrierte Diagnosefunktionen zur Unterstützung des Debugging von in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps gehosteten Node.js-Anwendungen. In diesem Artikel erfahren Sie, wie Sie die Protokollierung von "stdout" und "stderr" aktivieren, Fehlerinformationen im Browser anzeigen und die Protokolldateien herunterladen und anzeigen.

Die auf Azure gehosteten Diagnosefunktionen für Node.js-Anwendungen werden von [IISNode] bereitgestellt. In diesem Artikel werden die am häufigsten verwendeten Einstellungen zum Erfassen von Diagnoseinformationen erläutert. Er stellt keine vollständige Referenz für die Arbeit mit IISNode dar. Weitere Informationen zum Arbeiten mit IISNode finden Sie unter [IISNode Readme] (IISNode-Readme, in englischer Sprache) auf GitHub.

<a id="enablelogging"></a>
## Aktivieren der Protokollierung

Standardmäßig erfasst eine App Service-Web-App nur Diagnoseinformationen zu Bereitstellungen, wenn Sie zum Beispiel eine Web-App mit Git bereitstellen. Diese Informationen sind nützlich, wenn bei der Bereitstellung Probleme auftreten, z. B. ein Fehler beim Installieren eines Moduls, das in **package.json** referenziert wird, oder wenn Sie ein benutzerdefiniertes Bereitstellungsskript verwenden.

Um die Protokollierung von "stdout"- und "stderr"-Streams zu ermöglichen, müssen Sie im Stammordner der Node.js-Anwendung eine **IISNode.yml**-Datei erstellen und Folgendes hinzufügen:

	loggingEnabled: true

Dies aktiviert die Protokollierung von "stderr" und "stdout" der Node.js-Anwendung.

Mit der **IISNode.yml**-Datei können Sie auch steuern, ob benutzerfreundliche Fehlermeldungen oder Entwicklerfehlermeldungen an den Browser zurückgegeben werden, wenn ein Fehler auftritt. Um Entwicklerfehlermeldungen zu aktivieren, fügen Sie die folgende Zeile zur Datei **IISNode.yml** hinzu:

	devErrorsEnabled: true

Wenn diese Option aktiviert ist, gibt IISNode die letzten 64 K der an "stderr" gesendeten Informationen zurück, statt einer benutzerfreundlichen Fehlermeldung wie "Interner Serverfehler".

> [AZURE.NOTE]"devErrorsEnabled" ist zwar für die Diagnose von Problemen währen der Entwicklungsphase nützlich, sollte jedoch nicht in Produktionsumgebungen verwendet werden, da Entwicklungsfehler möglicherweise an die Endbenutzer gesendet werden.

Wenn die Datei **IISNode.yml** in Ihrer Anwendung zuvor noch nicht vorhanden war, müssen Sie die Web-App nach dem Veröffentlichen der aktualisierten Anwendung neu starten. Wenn Sie nur Einstellungen in einer bereits vorhandenen **IISNode.yml**-Datei geändert haben, die bereits veröffentlich wurde, ist kein Neustart erforderlich.

> [AZURE.NOTE]Wenn Sie die Web-App mit den Azure-Befehlszeilentools oder Azure-PowerShell-Cmdlets erstellt haben, wird automatisch eine standardmäßige **IISNode.yml**-Datei erstellt.

Um die Web-App neu zu starten, wählen Sie die Web-App im [Azure-Vorschauportal](https://portal.azure.com) aus, und klicken Sie dann auf die Schaltfläche **NEU STARTEN**:

![Schaltfläche 'Neustart'][restart-button]

Wenn in Ihrer Entwicklungsumgebung die Azure-Befehlszeilentools installiert sind, können Sie zum Neustarten der Web-App den folgenden Befehl verwenden:

	azure site restart [sitename]

> [AZURE.NOTE]Auch wenn "loggingEnabled" und "devErrorsEnabled" die am häufigsten verwendeten Konfigurationsoptionen in der Datei "IISNode.yml" zum Erfassen von Diagnoseinformationen sind, können Sie die Datei "IISNode.yml" zum Konfigurieren verschiedener Optionen Ihrer Hostingumgebung verwenden. Eine vollständige Liste der Konfigurationsoptionen finden Sie in der Datei [iisnode_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml).

<a id="viewlogs"></a>
## Zugreifen auf Protokolle

Es gibt drei Möglichkeiten zum Zugreifen auf Diagnoseprotokolle: über das File Transfer Protocol (FTP), Herunterladen als Zip-Archiv oder als ständig aktualisierter Livestream des Protokolls (als "Tail" bezeichnet). Um die Protokolldateien als Zip-Archiv herunterladen oder als Livestream anzeigen zu können, benötigen Sie die Azure-Befehlszeilentools. Sie können diese mit dem folgenden Befehl installieren:

	npm install azure-cli -g

Nachdem sie installiert wurden, können Sie mit dem Befehl "azure" auf die Tools zugreifen. Sie müssen die Befehlszeilentools zunächst für die Verwendung Ihrer Azure-Abonnements konfigurieren. Informationen hierzu finden Sie im Abschnitt **Herunterladen und Importieren der Veröffentlichungseinstellungen** des Artikels "Verwenden der Azure-Befehlszeilentools".

###FTP

Um per FTP auf die Diagnoseinformationen zuzugreifen, rufen Sie das [Azure-Vorschauportal](https://portal.azure.com) auf, wählen Sie Ihre Web-App aus, und wählen Sie dann **DASHBOARD** aus. Im Abschnitt **QuickLinks** bieten die Links **FTP DIAGNOSTIC LOGS** und **FTPS DIAGNOSTIC LOGS** Zugriff auf die Protokolldateien per FTP.

> [AZURE.NOTE]Wenn Sie noch keinen Benutzernamen und kein Kennwort für FTP oder die Bereitstellung konfiguriert haben, können Sie dies auf der **QuickStart**-Verwaltungsseite durchführen, indem Sie **Set up deployment credentials** auswählen.

Die im Dashboard zurückgegebene FTP-URL gilt für das Verzeichnis **LogFiles**, das folgende Unterverzeichnisse enthält:

* [Bereitstellungsmethode] – Wenn Sie eine Bereitstellungsmethode wie Git verwenden, wird ein Verzeichnis mit dem gleichen Namen erstellt, das die Informationen in Bezug auf die Bereitstellungen enthält.

* nodejs – von "stdout" und "stderr" erfasste Informationen für alle Instanzen der Anwendung (wenn "loggingEnabled" auf "true" gesetzt ist)

###Zip-Archiv

Um ein Zip-Archiv der Diagnoseprotokolle herunterzuladen, verwenden Sie den folgenden Befehl der Azure-Befehlszeilentools:

	azure site log download [sitename]

Hierdurch wird eine **diagnostics.zip**-Datei in das aktuelle Verzeichnis heruntergeladen. Dieses Archiv hat die folgende Verzeichnisstruktur:

* deployments – Ein Protokoll mit Informationen über die Bereitstellungen der Anwendung

* LogFiles

	* [Bereitstellungsmethode] – Wenn Sie eine Bereitstellungsmethode wie Git verwenden, wird ein Verzeichnis mit dem gleichen Namen erstellt, das die Informationen in Bezug auf die Bereitstellungen enthält.

	* nodejs – von "stdout" und "stderr" erfasste Informationen für alle Instanzen der Anwendung (wenn "loggingEnabled" auf "true" gesetzt ist)

###Livestream (Tail)

Um einen Livestream der Diagnoseprotokollinformationen anzuzeigen, verwenden Sie den folgenden Befehl der Azure-Befehlszeilentools:

	azure site log tail [sitename]

Hierdurch werden Sie in einem Livestream über Protokollereignisse informiert, sobald sie auf dem Server auftreten. Dieser Stream gibt sowohl Bereitstellungsinformationen als auch Informationen von "stdout" und "stderr" zurück (wenn "loggingEnabled" auf "true" gesetzt ist).

<a id="nextsteps"></a>
## Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie Diagnoseinformationen für Azure aktivieren und darauf zugreifen. Diese Informationen sind nützlich, um in der Anwendung auftretende Probleme zu analysieren, sie können jedoch auch auf ein Problem mit einem von Ihnen verwendeten Modul hinweisen oder angeben, dass die von App Service-Web-Apps verwendete Version von Node.js sich von der in Ihrer Entwicklungsumgebung verwendeten Version unterscheidet.

Weitere Informationen zur Arbeit mit Modulen in Azure finden Sie unter [Verwenden von Node.js-Modulen mit Azure-Anwendungen].

Weitere Informationen zum Festlegen einer Node.js-Version für Ihre Anwendung finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung].

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

[IISNode]: https://github.com/tjanczuk/iisnode
[IISNode Readme]: https://github.com/tjanczuk/iisnode#readme
[How to Use The Azure Command-Line Interface]: ../xplat-cli.md
[Verwenden von Node.js-Modulen mit Azure-Anwendungen]: ../nodejs-use-node-modules-azure-apps.md
[Festlegen einer Node.js-Version in einer Azure-Anwendung]: ../nodejs-specify-node-version-azure-apps.md

[restart-button]: ./media/web-sites-nodejs-debug/restartbutton.png
 

<!---HONumber=62-->