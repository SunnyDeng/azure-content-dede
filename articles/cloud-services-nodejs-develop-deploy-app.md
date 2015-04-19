<properties 
	pageTitle="Node.js-Anleitung, erste Schritte - Azure-Lernprogramm" 
	description="Ein umfassendes Lernprogramm, das Sie bei der Entwicklung einer einfachen Node.js-Webanwendung und deren Bereitstellung in Azure unterstützt." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>






# Erstellen und Bereitstellen einer Node.js-Anwendung zu einem Azure-Cloud-Dienst

Nach Durchführung dieser Anleitung werden Sie eine einfache Node.js-Anwendung erhalten, die in einem Azure-Cloud-Dienst ausgeführt wird. Cloud-Dienste sind die Bausteine skalierbarer Cloud-Anwendungen in Azure. Sie ermöglichen die Trennung und unabhängige Verwaltung und Skalierung von Front-End- und Back-End-Komponenten von Anwendungen.  Cloud-Dienste bieten stabile und dedizierte virtuelle Computer, um jede Rolle zuverlässig zu hosten.

Weitere Informationen zu Cloud-Diensten und einen Vergleich hinsichtlich Azure-Websites und virtueller Computern finden Sie unter [Vergleich von Websites, Cloud-Diensten und virtuellen Computern in Azure](http://azure.microsoft.com/documentation/articles/choose-web-site-cloud-service-vm/).

>[AZURE.TIP] Möchten Sie eine einfache Website erstellen? Wenn Ihr Szenario nur ein einfaches Website-Front-End umfasst, sollten Sie <a href="/de-de/documentation/articles/web-sites-nodejs-develop-deploy-mac/">die Verwendung einer einfachen Azure-Website ins Auge fassen.</a> Sie können einen Cloud-Dienst leicht upgraden, wenn die Website größer wird und sich Ihre Anforderungen ändern.


In diesem Lernprogramm werden Sie eine einfache Webanwendung erstellen, die in einer Webrolle gehostet wird. Sie werden den Serveremulator verwenden, um die Anwendung lokal zu testen. Anschließend werden Sie diese mithilfe der PowerShell-Befehlszeilentools bereitstellen.

Unten finden Sie einen Screenshot der vollständigen Anwendung:

<p><img src="https://wacomdpsstablestorage.blob.core.windows.net/articlesmedia/demo-ppe.windowsazure.com/de-de/documentation/articles/cloud-services-nodejs-develop-deploy-app/20140107035927/node21.png" alt="A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.">
</p>



## Erstellen einer neuen Knotenanwendung

Führen Sie folgende Aufgaben durch, um ein neues Azure-Cloud-Dienstprojekt sowie ein grundlegendes Node.js-Gerüst zu erstellen:

1. Suchen Sie im **Startmenü** oder auf der **Startseite** nach **Azure PowerShell**. Klicken Sie dann mit der rechten Maustaste auf **Azure PowerShell**, und wählen Sie **Als Administrator ausführen**.

	![Azure PowerShell icon][powershell-menu]

	[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Erstellen Sie ein neues **Knotenverzeichnis** auf dem Laufwerk C, und ändern Sie es in das Verzeichnis "c:\\node":
	
	![A command prompt displaying the commands 'mkdir c:\\node' and 'cd node'.][mkdir]

3.  Geben Sie das folgende Cmdlet ein, um eine neue Lösung zu erstellen:

        PS C:\node> New-AzureServiceProject helloworld

    	Sie werden folgende Antwort erhalten:

	![The result of the New-AzureService helloworld command](./media/cloud-services-nodejs-develop-deploy-app/node9.png)

	Das Cmdlet **New-AzureServiceProject** generiert eine einfache Struktur für die Erstellung einer neuen Azure-Knotenanwendung, die in einem Cloud-Dienst veröffentlicht wird. Darin sind Konfigurationsdateien enthalten, die für die Veröffentlichung in Azure erforderlich sind. Das Cmdlet ändert zudem das Arbeitsverzeichnis in das Verzeichnis des Diensts.

	Die durch das Cmdlet **New-AzureServiceProject** erstellten Dateien sind:

	-   **ServiceConfiguration.Cloud.cscfg**, **ServiceConfiguration.Local.cscfg** und **ServiceDefinition.csdef**. Dies sind Azure-spezifische Dateien, die für die Veröffentlichung der Anwendung erforderlich sind.
		
	Weitere Informationen zu diesen Dateien finden Sie unter
        [Übersicht zum Erstellen eines gehosteten Diensts für Azure][].

	-   **deploymentSettings.json** speichert lokale Einstellungen,die von den
        Azure PowerShell-Bereitstellungs-Cmdlets verwendet werden.

4.  Geben Sie den folgenden Befehl ein, um eine neue Webrolle mithilfe des Cmdlet **Add-AzureNodeWebRole** hinzuzufügen:

        PS C:\node\helloworld> Add-AzureNodeWebRole

	Sie werden folgende Antwort erhalten:

	![The output of the Add-AzureNodeWebRole command.](./media/cloud-services-nodejs-develop-deploy-app/node11.png)

	Das Cmdlet **Add-AzureNodeWebRole** erstellt ein neues Verzeichnis für die Anwendung, und generiert ein Gerüst für eine einfache Node.js-Anwendung. Zudem ändert es die im vorherigen Schritt erstellten Dateien **ServiceConfiguration.Cloud.csfg**, **ServiceConfiguration.Local.csfg** und **ServiceDefinition.csdef**, um Konfigurationseinträge für die neue Rolle hinzuzufügen.

	> [AZURE.NOTE] Wenn Sie keinen Rollennamen eingeben, wird standardmäßig ein Name erstellt. Sie können einen Namen als ersten Parameter für **Add-AzureNodeWebRole** angeben. Zum Beispiel `Add-AzureNodeWebRole MyRole`

5.  Verwenden Sie folgende Befehle, um zum Verzeichnis **WebRole1** zu navigieren, und öffnen Sie dann die Datei **server.js** im Editor. 

	PS C:\\node\\helloworld> cd WebRole1
        PS C:\node\helloworld\WebRole1> notepad server.js

	Die Datei **server.js** wurde durch das Cmdlet **Add-AzureNodeWebRole** erstellt, und enthält folgenden Startcode. Dieser Code ist ähnlich wie im "Hallo Welt"-Beispiel auf der [nodejs.org][]-Website, mit folgender Ausnahme:

   	-   Der Port wurde geändert, sodass die Anwendung den korrekten Port finden kann, der ihr durch die Cloud-Umgebung zugewiesen wurde.
   	-   Konsolenprotokollierung wurde entfernt.

	![Notepad displaying the contents of server.js](./media/cloud-services-nodejs-develop-deploy-app/node13.png)

## Lokales Ausführen der Anwendung im Emulator

Eines der durch das Azure-SDK erstellten Tools ist der Azure-Serveremulator, der Ihnen ermöglicht, die Anwendung lokal zu testen. Der Serveremulator simuliert die Umgebung, in der die Anwendung ausgeführt wird, wenn diese in der Cloud bereitgestellt ist. Führen Sie folgende Schritte aus, um die Anwendung im Emulator zu testen.

1.  Schließen Sie den Editor, und wechseln Sie zurück zum Windows PowerShell-Fenster.
  	Geben Sie das folgende Cmdlet ein, um den Dienst im Emulator auszuführen:

        PS C:\node\helloworld\WebRole1> Start-AzureEmulator -Launch

	Der Parameter **-Launch** gibt an, dass die Tools automatisch ein Browserfenster öffnen und die Anwendung anzeigen, sobald diese im Emulator ausgeführt wird. Es wird ein Browser geöffnet, der wie im Screenshot unten "Hallo Welt" anzeigt. Dies zeigt an, dass der Dienst im Serveremulator ausgeführt wird und korrekt läuft.

	![A web browser displaying the Hello World web page](./media/cloud-services-nodejs-develop-deploy-app/node14.png)

2.  Verwenden Sie den Befehl **Stop-AzureEmulator**, um den Serveremulator anzuhalten:
	
	PS C:\node\helloworld\WebRole1> Stop-AzureEmulator

## Bereitstellung der Anwendung zu Azure

	[AZURE.INCLUDE [create-account-note](../includes/create-account-note.md)]



### <a id="download_publishing_settings"></a>Herunterladen der Azure-Veröffentlichungseinstellungen

Um die Anwendung in Azure bereitzustellen, müssen Sie zuerst die Veröffentlichungseinstellungen für Ihr Azure-Abonnement herunterladen. Die folgenden Schritte führen Sie durch diesen Prozess:

1.  Starten Sie im Windows PowerShell-Fenster die Downloadseite, indem Sie folgendes Cmdlet ausführen:

        PS C:\node\helloworld\WebRole1> Get-AzurePublishSettingsFile

	Dadurch wird Ihr Browser zur Downloadseite mit den Veröffentlichungseinstellungen navigieren. Sie werden möglicherweise aufgefordert, sich mit einem Microsoft-Konto anzumelden. Verwenden Sie in diesem Fall das mit Ihrem Azure-Abonnement verknüpfte Konto.

	Speichern Sie das heruntergeladene Profil an einem Datenspeicherort, auf den Sie leicht zugreifen können.

2.  Verwenden Sie im Azure PowerShell-Fenster das folgende Cmdlet, um Windows PowerShell für Node.js-Cmdlets zur Nutzung des heruntergeladenen Azure-Veröffentlichungsprofils zu konfigurieren:

        PS C:\node\helloworld\WebRole1> Import-AzurePublishSettingsFile [path to file]


	> [AZURE.NOTE] Nach dem Importieren der Veröffentlichungseinstellungen sollten Sie die heruntergeladene .publishsettings-Datei löschen, da sie Informationen enthält, die von anderen Personen für den Zugriff auf Ihr Konto verwendet werden können.
    

### Veröffentlichung zur Anwendung

1.  Veröffentlichen Sie die Anwendung mithilfe des Cmdlet **Publish-AzureServiceProject** wie unten gezeigt.

        PS C:\node\helloworld\WebRole1> Publish-AzureServiceProject -ServiceName NodeHelloWorld -Location "East US" -Launch

	- Der Parameter **servicename** gibt den Namen an, der für diese Bereitstellung verwendet wurde. Dieser Name muss eindeutig sein, da ansonsten der Veröffentlichungsprozess fehlschlagen wird.

	- Der Parameter **location** gibt das Rechenzentrum an, in dem die Anwendung gehostet wird. Verwenden Sie das Cmdlet **Get-AzureLocation**, um eine Liste der verfügbaren Rechenzentren anzuzeigen.

	- Der Parameter **launch** startet den Browser und navigiert zum gehosteten Dienst, nachdem die Bereitstellung erfolgt ist.

	Nach der erfolgreichen Veröffentlichung wird eine Antwort dieser Art angezeigt:

	![The output of the Publish-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node19.png)

	Das Cmdlet **Publish-AzureServiceProject** führt folgende Schritte aus:

1.  Erstellt ein Paket, das in Azure bereitgestellt wird. Das Paket enthält alle Dateien im Ordner der node.js-Anwendung.

2.  Erstellt ein neues **Speicherkonto**, falls noch keines vorhanden ist. Das Azure-Speicherkonto wird verwendet, um das Anwendungspaket während der Bereitstellung zu speichern. Sie können das Speicherkonto problemlos löschen, wenn die Bereitstellung erfolgt ist.

3.  Erstellt einen neuen **Cloud-Dienst**, falls noch keiner vorhanden ist. Ein **Cloud-Dienst** ist der Container, in dem die Anwendung gehostet wird, wenn diese in Azure bereitgestellt wird. Weitere Informationen erhalten Sie unter [Übersicht zum Erstellen eines gehosteten Diensts für Azure][].

4.  Veröffentlicht das Bereitstellungspaket in Azure.


	> [AZURE.NOTE]
	> Es kann nach der ersten Veröffentlichung fünf bis sieben Minuten dauern, bis die Anwendung bereitgestellt wird und verfügbar ist.

	Sobald die Bereitstellung erfolgt ist, wird ein Browserfenster geöffnet und navigiert zum Cloud-Dienst.


	![A browser window displaying the hello world page. The URL indicates the page is hosted on Azure.](./media/cloud-services-nodejs-develop-deploy-app/node21.png)

	Ihre Anwendung läuft jetzt auf Azure!


## Anhalten und Löschen Ihrer Anwendung

Nachdem Sie Ihre Anwendung bereitgestellt haben, möchten Sie diese möglicherweise deaktivieren, um Extrakosten zu vermeiden. Azure berechnet Webrolleninstanzen pro Stunde verbrauchter Serverzeit. Die Serverzeit wird verbraucht, sobald Ihre Anwendung bereitgestellt wurde, selbst wenn die Instanzen nicht ausgeführt werden und gestoppt wurden.

1.  Stoppen Sie im Windows PowerShell-Fenster die im vorherigen Abschnitt erstellte Dienstbereitstellung mit folgendem Cmdlet:

        PS C:\node\helloworld\WebRole1> Stop-AzureService

	Das Stoppen des Diensts kann einige Minuten dauern. Wenn der Dienst gestoppt wurde, erhalten Sie eine entsprechende Nachricht.

	![The status of the Stop-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node48.png)

2.  Um den Dienst zu löschen, rufen Sie folgendes Cmdlet auf:

        PS C:\node\helloworld\WebRole1> Remove-AzureService

	Geben Sie nach entsprechender Aufforderung **Y** ein, um den Dienst zu löschen.

	Das Löschen des Diensts kann einige Minuten dauern. Nachdem der Dienst gelöscht wurde, erhalten Sie eine entsprechende Nachricht.

	![The status of the Remove-AzureService command](./media/cloud-services-nodejs-develop-deploy-app/node49.png)

	> [AZURE.NOTE] Durch das Löschen dieses Diensts wird das Speicherkonto, das beim erstmaligen Veröffentlichen des Diensts erstellt wurde, nicht gelöscht, und Sie erhalten weiterhin eine Rechnung über den verwendeten Speicherplatz. Weitere Informationen zum Löschen eines Speicherkontos finden Sie unter [Löschen eines Speicherkontos aus einem Azure-Abonnement](http://msdn.microsoft.com/library/windowsazure/hh531562.aspx).


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
[Löschen eines Speicherkontos aus einem Azure-Abonnement]: https://www.windowsazure.com/de-de/manage/services/storage/how-to-manage-a-storage-account/
[powershell-menu]: ./media/cloud-services-nodejs-develop-deploy-app/azure-powershell-start.png

<!--HONumber=45--> 
