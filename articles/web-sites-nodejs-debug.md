<properties linkid="dev-nodejs-how-to-debug-website" urlDisplayName="Debug Websites (Node)" pageTitle="How to Debug Azure Websites in Node.js" metaKeywords="debug website azure, debugging azure, troubleshooting azure web site, troubleshoot azure website node" description="Learn how to debug an Azure website in Node.js." metaCanonical="" services="web-sites" documentationCenter="Node.js" title="How to debug a Node.js application in Azure Web Sites" authors="larryfr" solutions="" manager="paulettm" editor="mollybos" />

Debuggen einer Node.js-Anwendung auf Azure-Websites
===================================================

Azure bietet integrierte Diagnosefunktionen zur Unterstützung des Debugging einer auf Azure-Websites gehosteten Node.js-Anwendung. In diesem Artikel erfahren Sie, wie Sie die Protokollierung von "stdout" und "stderr" aktivieren, Fehlerinformationen im Browser anzeigen und die Protokolldateien herunterladen und anzeigen.

Die auf Azure gehosteten Diagnosefunktionen für Node.js-Anwendungen werden von [IISNode](https://github.com/tjanczuk/iisnode) bereitgestellt. In diesem Artikel werden die am häufigsten verwendeten Einstellungen zum Erfassen von Diagnoseinformationen erläutert. Er stellt keine vollständige Referenz für die Arbeit mit IISNode dar. Weitere Informationen zum Arbeiten mit IISNode finden Sie unter [IISNode Readme](https://github.com/tjanczuk/iisnode#readme) (IISNode-Readme, in englischer Sprache) auf GitHub.

Aktivieren der Protokollierung
------------------------------

Standardmäßig erfasst Azure-Websites nur Diagnoseinformtionen zu Bereitstellungen, wenn Sie zum Beispiel eine Website mit Git bereitstellen. Diese Informationen sind nützlich, wenn bei der Bereitstellung Probleme auftreten, z. B. ein Fehler beim Installieren eines Moduls, das in **package.json** referenziert wird, oder wenn Sie ein benutzerdefiniertes Bereitstellungsskript verwenden.

Um die Protokollierung von "stdout"- und "stderr"-Streams zu ermöglichen, müssen Sie im Stammordner der Node.js-Anwendung eine **IISNode.yml**-Datei erstellen und Folgendes hinzufügen:

    loggingEnabled: true

Dies aktiviert die Protokollierung von "stderr" und "stdout" der Node.js-Anwendung.

Mit der **IISNode.yml**-Datei können Sie auch steuern, ob benutzerfreundliche Fehlermeldungen oder Entwicklerfehlermeldungen an den Browser zurückgegeben werden, wenn ein Fehler auftritt. Um Entwicklerfehlermeldungen zu aktivieren, fügen Sie die folgende Zeile zur Datei **IISNode.yml** hinzu:

    devErrorsEnabled: true

Wenn diese Option aktiviert ist, gibt IISNode die letzten 64 K der an "stderr" gesendeten Informationen zurück, statt einer benutzerfreundlichen Fehlermeldung wie "Interner Serverfehler".

**Hinweis**

"devErrorsEnabled" ist zwar für die Diagnose von Problemen währen der Entwicklungsphase nützlich, sollte jedoch nicht in Produktionsumgebungen verwendet werden, da Entwicklungsfehler möglicherweise an die Endbenutzer gesendet werden.

Wenn die Datei **IISNode.yml** in Ihrer Anwendung zuvor noch nicht vorhanden war, müssen Sie die Website nach dem Veröffentlichen der aktualisierten Anwendung neu starten. Wenn Sie nur Einstellungen in einer bereits vorhandenen **IISNode.yml**-Datei geändert haben, die bereits veröffentlich wurde, ist kein Neustart erforderlich.

**Hinweis**

Wenn Sie die Website mit den Azure-Befehlszeilentools oder Azure-PowerShell-Cmdlets erstellt haben, wird automatisch eine standardmäßige **IISNode.yml**-Datei erstellt.

Sie können die Website neu starten, indem Sie die Website im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) auswählen und dann die Schaltfläche **NEU STARTEN** auswählen:

![Schaltfläche "Neu starten"](./media/web-sites-nodejs-debug/restartbutton.png)

Wenn in Ihrer Entwicklungsumgebung die Azure-Befehlszeilentools installiert sind, können Sie zum Neustarten der Website den folgenden Befehl verwenden:

    azure site restart [sitename]

**Hinweis**

Auch wenn "loggingEnabled" und "devErrorsEnabled" die am häufigsten verwendeten Konfigurationsoptionen in der Datei "IISNode.yml" zum Erfassen von Diagnoseinformationen sind, können Sie die Datei "IISNode.yml" zum Konfigurieren verschiedener Optionen Ihrer Hostingumgebung verwenden. Eine vollständige Liste der Konfigurationsoptionen finden Sie in der Datei [iisnode\_schema.xml](https://github.com/tjanczuk/iisnode/blob/master/src/config/iisnode_schema.xml).

Zugreifen auf Protokolle
------------------------

Es gibt drei Möglichkeiten zum Zugreifen auf Diagnoseprotokolle: über das File Transfer Protocol (FTP), Herunterladen als Zip-Archiv oder als ständig aktualisierter Livestream des Protokolls (als "Tail" bezeichnet). Um die Protokolldateien als Zip-Archiv herunterladen oder als Livestream anzeigen zu können, benötigen Sie die Azure-Befehlszeilentools. Sie können diese mit dem folgenden Befehl installieren:

    npm install azure-cli -g

Nachdem sie installiert wurden, können Sie mit dem Befehl "azure" auf die Tools zugreifen. Sie müssen die Befehlszeilentools zunächst für die Verwendung Ihrer Azure-Abonnements konfigurieren. Informationen hierzu finden Sie im Abschnitt **Herunterladen und Importieren der Veröffentlichungseinstellungen** des Artikels [Verwenden der Azure-Befehlszeilentools](https://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/command-line-tools/).

### FTP

Um per FTP auf die Diagnoseinformationen zuzugreifen, rufen Sie das [Azure-Portal] auf, wählen Sie Ihre Website aus, und wählen Sie dann **DASHBOARD**. Im Abschnitt **QuickLinks** bieten die Links **FTP DIAGNOSTIC LOGS** und **FTPS DIAGNOSTIC LOGS** Zugriff auf die Protokolldateien per FTP.

**Hinweis**

Wenn Sie noch keinen Benutzernamen und kein Kennwort für FTP oder die Bereitstellung konfiguriert haben, können Sie dies auf der **QuickStart**-Verwaltungsseite durchführen, indem Sie **Set up deployment credentials** auswählen.

Die im Dashboard zurückgegebene FTP-URL gilt für das Verzeichnis **LogFiles**, das folgende Unterverzeichnisse enthält:

-   [Bereitstellungsmethode] – Wenn Sie eine Bereitstellungsmethode wie Git verwenden, wird ein Verzeichnis mit dem gleichen Namen erstellt, das die Informationen in Bezug auf die Bereitstellungen enthält.

-   nodejs – von "stdout" und "stderr" erfasste Informationen für alle Instanzen der Anwendung (wenn "loggingEnabled" auf "true" gesetzt ist)

### Zip-Archiv

Um ein Zip-Archiv der Diagnoseprotokolle herunterzuladen, verwenden Sie den folgenden Befehl der Azure-Befehlszeilentools:

    azure site log download [sitename]

Hierdurch wird eine **diagnostics.zip**-Datei in das aktuelle Verzeichnis heruntergeladen. Dieses Archiv hat die folgende Verzeichnisstruktur:

-   deployments – Ein Protokoll mit Informationen über die Bereitstellungen der Anwendung

-   LogFiles

    -   [Bereitstellungsmethode] – Wenn Sie eine Bereitstellungsmethode wie Git verwenden, wird ein Verzeichnis mit dem gleichen Namen erstellt, das die Informationen in Bezug auf die Bereitstellungen enthält.

    -   nodejs – von "stdout" und "stderr" erfasste Informationen für alle Instanzen der Anwendung (wenn "loggingEnabled" auf "true" gesetzt ist)

### Livestream (Tail)

Um einen Livestream der Diagnoseprotokollinformationen anzuzeigen, verwenden Sie den folgenden Befehl der Azure-Befehlszeilentools:

    azure site log tail [sitename]

Hierdurch werden Sie in einem Livestream über Protokollereignisse informiert, sobald sie auf dem Server auftreten. Dieser Stream gibt sowohl Bereitstellungsinformationen als auch Informationen von "stdout" und "stderr" zurück (wenn "loggingEnabled" auf "true" gesetzt ist).

Nächste Schritte
----------------

In diesem Artikel haben Sie erfahren, wie Sie Diagnoseinformationen für Azure aktivieren und darauf zugreifen. Diese Informationen sind nützlich, um in der Anwendung auftretende Probleme zu analysieren, Sie können jedoch auch auf ein Problem mit einem Modul, das Sie verwenden, hinweisen, oder angeben, dass die von den Azure-Websites verwendete Version von Node.js sich von der in Ihrer Entwicklungsumgebung verwendeten Version unterscheidet.

Weitere Informationen zur Arbeit mit Modulen in Azure finden Sie unter [Verwenden von Node.js-Modulen mit Azure-Anwendungen](https://www.windowsazure.com/en-us/develop/nodejs/common-tasks/working-with-node-modules/).

Weitere Informationen zum Festlegen einer Node.js-Version für Ihre Anwendung finden Sie unter [Festlegen einer Node.js-Version in einer Azure-Anwendung](https://www.windowsazure.com/en-us/develop/nodejs/common-tasks/specifying-a-node-version/).

