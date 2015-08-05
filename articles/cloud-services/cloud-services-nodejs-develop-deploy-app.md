<properties
	pageTitle="Node.js-Anleitung, erste Schritte – Azure-Lernprogramm"
	description="Erfahren Sie, wie Sie eine einfache Node.js-Webanwendung erstellen und in einem Azure-Clouddienst bereitstellen können."
	services="cloud-services"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="cloud-services"
	ms.workload="tbd"
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="06/01/2015"
	ms.author="mwasson"/>


# Erstellen und Bereitstellen einer Node.js-Anwendung in einem Azure-Clouddienst

> [AZURE.SELECTOR]
- [Node.js](cloud-services-nodejs-develop-deploy-app.md)
- [.NET](cloud-services-dotnet-get-started.md)

Dieses Lernprogramm veranschaulicht, wie Sie eine einfache Node.js-Anwendung erstellen können, die in einem Azure-Clouddienst ausgeführt wird. Clouddienste sind die Bausteine skalierbarer Cloudanwendungen in Azure. Sie ermöglichen die Trennung und unabhängige Verwaltung und Skalierung von Front-End- und Back-End-Komponenten von Anwendungen. Clouddienste bieten stabile und dedizierte virtuelle Computer, um jede Rolle zuverlässig zu hosten.

Weitere Informationen zu Cloud Services und einen Vergleich mit Azure-Websites und Virtual Machines finden Sie unter [Azure Websites, Cloud Services and Virtual Machines comparison](../choose-web-site-cloud-service-vm.md) (Vergleich von Azure-Websites, Cloud Services und Virtual Machines, in englischer Sprache).

>[AZURE.TIP]Möchten Sie eine einfache Website erstellen? Wenn Ihr Szenario nur ein einfaches Website-Front-End umfasst, sollten Sie die <a href="/documentation/articles/web-sites-nodejs-develop-deploy-mac/">Verwendung einer einfachen Web-App</a> in Betracht ziehen. Sie können einen Clouddienst mühelos aktualisieren, wenn die Web-App größer wird und sich Ihre Anforderungen ändern.


In diesem Lernprogramm werden Sie eine einfache Webanwendung erstellen, die in einer Webrolle gehostet wird. Sie werden den Serveremulator verwenden, um die Anwendung lokal zu testen. Anschließend stellen Sie sie mithilfe der PowerShell-Befehlszeilentools bereit.

Die Anwendung ist eine einfache "Hello World"-Anwendung:

![Ein Webbrowser, der die Hallo Welt-Webseite anzeigt](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

## Voraussetzungen

> [AZURE.NOTE]In diesem Lernprogramm wird Azure PowerShell verwendet, für die Windows installiert sein muss.

- Installieren und konfigurieren Sie [Azure PowerShell](../install-configure-powershell.md).
- Laden Sie das [Azure SDK für .NET 2.5](http://go.microsoft.com/fwlink/?linkid=518091), und installieren Sie es. Wählen Sie während der Installationseinrichtung Folgendes:
    - MicrosoftAzureAuthoringTools
    - MicrosoftAzureComputeEmulator


## Erstellen eines Azure-Clouddienstprojekts

Führen Sie folgende Aufgaben durch, um ein neues Azure-Clouddienstprojekt sowie ein einfaches Node.js-Gerüst zu erstellen:


1. Führen Sie **Azure PowerShell** als Administrator aus. (Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**.)

2.  Geben Sie das folgende PowerShell-Cmdlet ein, um das Projekt zu erstellen:

        New-AzureServiceProject helloworld

	![Ergebnis des New-AzureService-Hallowelt-Befehls](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Das **New-AzureServiceProject**-Cmdlet generiert eine einfache Struktur für die Veröffentlichung einer Node.js-Anwendung in einem Clouddienst. Darin sind Konfigurationsdateien enthalten, die für die Veröffentlichung in Azure erforderlich sind. Das Cmdlet ändert zudem das Arbeitsverzeichnis in das Verzeichnis des Diensts.

	Das Cmdlet erstellt die folgenden Dateien:

	-   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** und **ServiceDefinition.csdef**. Dies sind Azure-spezifische Dateien, die für die Veröffentlichung der Anwendung erforderlich sind. Weitere Informationen erhalten Sie unter [Übersicht zum Erstellen eines gehosteten Diensts für Azure][].

	-   **deploymentSettings.json** speichert lokale Einstellungen, die von den Azure PowerShell-Bereitstellungs-Cmdlets verwendet werden.

4.  Geben Sie den folgenden Befehl zum Hinzufügen einer neuen Webrolle ein:

        Add-AzureNodeWebRole

	![Das Ergebnis des Add-AzureNodeWebRole-Befehls.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Das **Add-AzureNodeWebRole**-Cmdlet erstellt eine einfache Node.js-Anwendung. Zudem ändert es die **CSFG**- und **CSDEF**-Dateien, um Konfigurationseinträge für die neue Rolle hinzuzufügen.

	> [AZURE.NOTE]Wenn Sie keinen Rollennamen angeben, wird ein Standardname verwendet. Sie können einen Namen als ersten Cmdlet-Parameter angeben: `Add-AzureNodeWebRole MyRole`


Die Node.js-App wird in der Datei **server.js** im Verzeichnis für die Webrolle (Standardeinstellung: **WebRole1**) definiert. Hier folgt der Code:

	var http = require('http');
	var port = process.env.port || 1337;
	http.createServer(function (req, res) {
	    res.writeHead(200, { 'Content-Type': 'text/plain' });
	    res.end('Hello World\n');
	}).listen(port);

Dieser Code ist im Wesentlichen identisch mit dem Beispiel "Hello World" auf der [nodejs.org][]-Website, mit der Ausnahme, dass die von der Cloudumgebung zugewiesene Portnummer verwendet wird.

## Bereitstellen der Anwendung in Azure

	[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]


### Herunterladen der Azure-Veröffentlichungseinstellungen

Um die Anwendung in Azure bereitzustellen, müssen Sie zuerst die Veröffentlichungseinstellungen für Ihr Azure-Abonnement herunterladen.

1.  Führen Sie das folgende Azure PowerShell-Cmdlet aus:

        Get-AzurePublishSettingsFile

	Dadurch wird Ihr Browser zur Downloadseite mit den Veröffentlichungseinstellungen navigieren. Sie werden möglicherweise aufgefordert, sich mit einem Microsoft-Konto anzumelden. Verwenden Sie in diesem Fall das mit Ihrem Azure-Abonnement verknüpfte Konto.

	Speichern Sie das heruntergeladene Profil an einem Datenspeicherort, auf den Sie leicht zugreifen können.

2.  Führen Sie das folgende Cmdlet aus, um das heruntergeladene Veröffentlichungsprofil zu importieren:

        Import-AzurePublishSettingsFile [path to file]


	> [AZURE.NOTE]Nach dem Importieren der Veröffentlichungseinstellungen empfiehlt es sich, die heruntergeladene .publishSettings-Datei zu löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können.


### Veröffentlichen der Anwendung

Führen Sie zum Veröffentlichen das **Publish-AzureServiceProject**-Cmdlet wie folgt aus:

    Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

- **-ServiceName** gibt den Namen für die Bereitstellung an. Dieser Name muss eindeutig sein, andernfalls schlägt der Veröffentlichungsvorgang fehl.

- **-Location** gibt das Datencenter an, in dem die Anwendung gehostet wird. Um eine Liste der verfügbaren Datencenter anzuzeigen, verwenden Sie das **Get-AzureLocation**-Cmdlet.

- **-Launch** öffnet nach Abschluss der Bereitstellung ein Browserfenster und navigiert zum gehosteten Dienst.

Nach der erfolgreichen Veröffentlichung wird eine Antwort dieser Art angezeigt:

![Das Ergebnis des Publish-AzureService-Befehls](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

> [AZURE.NOTE]Es kann nach der ersten Veröffentlichung fünf bis sieben Minuten dauern, bis die Anwendung bereitgestellt wird und verfügbar ist.

Nachdem die Bereitstellung abgeschlossen ist, wird ein Browserfenster geöffnet und der Clouddienst angezeigt.


![Ein Browserfenster, das die Seite Hallo Welt anzeigt. Die URL zeigt an, dass die Seite auf Azure gehostet wird.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

Ihre Anwendung wird jetzt in Azure ausgeführt.

Das Cmdlet **Publish-AzureServiceProject** führt folgende Schritte aus:

1.  Erstellt ein Paket zum Bereitstellen. Das Paket enthält alle Dateien im Anwendungsordner.

2.  Erstellt ein neues **Speicherkonto**, falls noch keines vorhanden ist. Das Azure-Speicherkonto wird verwendet, um das Anwendungspaket während der Bereitstellung zu speichern. Sie können das Speicherkonto problemlos löschen, wenn die Bereitstellung erfolgt ist.

3.  Erstellt einen neuen **Clouddienst**, falls noch keiner vorhanden ist. Ein **Clouddienst** ist der Container, in dem die Anwendung gehostet wird, wenn diese in Azure bereitgestellt wird. Weitere Informationen erhalten Sie unter [Übersicht zum Erstellen eines gehosteten Diensts für Azure][].

4.  Veröffentlicht das Bereitstellungspaket in Azure.



## Beenden und Löschen Ihrer Anwendung

Nachdem Sie Ihre Anwendung bereitgestellt haben, möchten Sie diese möglicherweise deaktivieren, um Extrakosten zu vermeiden. Azure berechnet Webrolleninstanzen pro Stunde verbrauchter Serverzeit. Die Serverzeit wird verbraucht, sobald Ihre Anwendung bereitgestellt wurde, selbst wenn die Instanzen nicht ausgeführt werden und gestoppt wurden.

1.  Stoppen Sie im Windows PowerShell-Fenster die im vorherigen Abschnitt erstellte Dienstbereitstellung mit folgendem Cmdlet:

        Stop-AzureService

	Das Stoppen des Diensts kann einige Minuten dauern. Wenn der Dienst gestoppt wurde, erhalten Sie eine entsprechende Nachricht.

	![Der Status des Stop-AzureService-Befehls](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Um den Dienst zu löschen, rufen Sie folgendes Cmdlet auf:

        Remove-AzureService

	Geben Sie nach entsprechender Aufforderung **Y** ein, um den Dienst zu löschen.

	Das Löschen des Diensts kann einige Minuten dauern. Nachdem der Dienst gelöscht wurde, erhalten Sie eine entsprechende Nachricht.

	![Der Status des Remove-AzureService-Befehls](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE]Durch das Löschen dieses Diensts wird das Speicherkonto, das beim erstmaligen Veröffentlichen des Diensts erstellt wurde, nicht gelöscht, und Sie erhalten weiterhin eine Rechnung über den verwendeten Speicherplatz. Weitere Informationen zum Löschen eines Speicherkontos finden Sie unter [Löschen eines Speicherkontos aus einem Azure-Abonnement](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


[The Windows Start menu with the Azure SDK Node.js entry expanded]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-menu.png
[mkdir]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-6.png
[nodejs.org]: http://nodejs.org/
[A directory listing of the helloworld folder.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-7.png
[Übersicht zum Erstellen eines gehosteten Diensts für Azure]: http://msdn.microsoft.com/library/windowsazure/jj155995.aspx
[A directory listing of the WebRole1 folder]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-8.png
[The menu displayed when right-clicking the Azure emulator from the task bar.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-11.png
[A browser window displaying http://www.windowsazure.com/ with the Free Trial link highlighted]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-12.png
[A browser window displaying the liveID sign in page]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-13.png
[Internet Explorer displaying the save as dialog for the publishSettings file.]: ./media/cloud-services-nodejs-develop-deploy-app/getting-started-14.png

[The full status output of the Publish-AzureService command]: ./media/cloud-services-nodejs-develop-deploy-app/node20.png
[How to Delete a Storage Account from an Azure Subscription]: https://www.windowsazure.com/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png
 

<!---HONumber=July15_HO4-->