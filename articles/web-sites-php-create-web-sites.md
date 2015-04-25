<properties 
	pageTitle="Erstellen einer PHP-Website in Azure-Websites" 
	description="Erfahren Sie, wie Sie eine PHP-Website in Azure Websites erstellen." 
	documentationCenter="php" 
	services="" 
	editor="mollybos" 
	manager="wpickett" 
	authors="tfitzmac"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/21/2014" 
	ms.author="tomfitz"/>

#Erstellen einer PHP-Website in Azure-Websites

Dieser Artikel beschreibt, wie Sie eine PHP-Website in [Azure-Websites][waws] mithilfe des [Azure-Verwaltungsportals], der [Azure-Befehlszeilentools für Mac und Linux][xplat-tools] oder der [Azure PowerShell cmdlets][powershell-cmdlets] erstellen.

Im Allgemeinen unterscheidet sich die Erstellung einer PHP-Website nicht von der Erstellung anderer Websites in Azure-Websites. PHP ist standardmäßig für alle Websites aktiviert. Informationen zur Konfiguration von PHP (oder zur Bereitstellung Ihrer eigenen benutzerdefinierten PHP-Laufzeit) finden Sie unter [Konfigurieren von PHP in Azure-Websites][configure-php]..

Jede unten beschriebene Option zeigt, wie Sie eine Website in einer freigegebenen Hostingumgebung kostenlos erstellen, jedoch mit eingeschränkter CPU-Auslastung und Bandbreitennutzung. Weitere Informationen finden Sie unter [Azure-Preise][websites-pricing]. Informationen zum Aktualisieren und Skalieren Ihrer Website finden Sie unter [Skalieren von Websites][scale-websites].

> [AZURE.NOTE]
> Wenn Sie Azure-Websites ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>. Auf dieser Seite können Sie sofort und kostenlos eine befristete ASP.NET Starter Site in Azure-Websites erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Inhaltsverzeichnis
* [Erstellen einer Website über das Azure-Verwaltungsportal](#portal)
* [Erstellen einer Website über die Azure-Befehlszeilentools für Mac und Linux](#XplatTools)
* [Erstellen einer Website über die Azure PowerShell-Cmdlets](#PowerShell)

<h2><a name="portal"></a>Erstellen einer PHP-Website über das Azure-Verwaltungsportal</h2>

Sie haben drei Möglichkeiten, um eine Website im Azure-Verwaltungsportal zu erstellen: **Schnellerfassung**, **mit einer Datenbank** und **aus der Galerie**. Die Anweisungen unten behandeln die Option **Schnellerstellung**. Informationen zu den anderen beiden Optionen finden Sie unter [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git][website-mysql-git] und [Erstellen einer WordPress-Website über die Galerie in Azure][wordpress-gallery].

Gehen Sie zum Erstellen einer PHP-Website über das Azure-Verwaltungsportal wie folgt vor:

1. Melden Sie sich am [Azure-Verwaltungsportal] an.
1. Klicken Sie unten auf der Seite auf **Neu**, und wählen Sie **Server**, **Website** und **Schnellerfassung** aus. Geben Sie eine **URL** für die Website an, und wählen Sie die **Region** für die Website aus. Klicken Sie abschließend auf **Website erstellen**.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux</h2>

Gehen Sie zum Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux wie folgt vor:

1. Installieren Sie die Azure-Befehlszeilentools, indem Sie folgende Anleitung befolgen: [Installation der Azure-Befehlszeilentools für Mac und Linux](/de-de/develop/php/how-to-guides/command-line-tools/#Download).

1. Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Herunterladen und Importieren der Veröffentlichungseinstellungen](/de-de/develop/php/how-to-guides/command-line-tools/#Account).

1. Geben Sie in der Befehlszeile Folgendes ein:

		azure site create MySiteName

Die URL für die neu erstellte Website lautet `http://MySiteName.azurewebsites.net`.  
 
Beachten Sie, dass Sie den Befehl  `azure site create` mit einer der folgenden Optionen ausführen können:

* `--location [location name]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `--hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
* `--git`. Mit dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Website verweist.

Informationen zu zusätzlichen Optionen finden Sie unter [Erstellen und Verwalten einer Azure-Website](/de-de/develop/php/how-to-guides/command-line-tools/#WebSites)..

<h2><a name="PowerShell"></a>Erstellen einer PHP-Website über die Azure PowerShell-Cmdlets</h2>

Gehen Sie zum Erstellen einer PHP-Website über die Azure PowerShell-Cmdlets wie folgt vor:

1. Installieren Sie Azure PowerShell-Cmdlets wie hier beschrieben: [Erste Schritte mit Azure PowerShell](/de-de/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Vorgehensweise: Importieren von Veröffentlichungseinstellungen](/de-de/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie folgenden Befehl aus:

		New-AzureWebSite MySiteName

Die URL für die neu erstellte Website lautet  `http://MySiteName.azurewebsites.net`.  
 
Beachten Sie, dass Sie den Befehl  `New-AzureWebSite` mit einer der folgenden Optionen ausführen können:

* `-Location [location name]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `-Hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
* `-Git`. Mit dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Website verweist.

Informationen zu weiteren Optionen finden Sie unter [Gewusst wie: Erstellen und Verwalten von Azure-Websites](/de-de/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>Nächste Schritte</h2>

Nachdem Sie eine PHP-Website in Azure-Websites erstellt haben, können Sie Ihre Website verwalten, konfigurieren, überwachen, bereitstellen und skalieren. Weitere Informationen finden Sie unter den folgenden Links:

* [Konfigurieren von Websites](/de-de/manage/services/web-sites/how-to-configure-websites/)
* [Konfigurieren von PHP in Azure-Websites][configure-php]
* [Verwalten von Websites](/de-de/manage/services/web-sites/how-to-manage-websites/)
* [Überwachen von Websites](/de-de/manage/services/web-sites/how-to-monitor-websites/)
* [Skalieren von Websites](/de-de/manage/services/web-sites/how-to-scale-websites/)
* [Veröffentlichen mit Git](/de-de/develop/php/common-tasks/publishing-with-git/)

Lernprogramme von Entwicklern für Entwickler finden Sie auf der Seite [PHP Developer Center - Lernprogramme](/de-de/develop/php/tutorials/).

[waws]: /de-de/manage/services/web-sites/
[Azure-Verwaltungsportal]: http://manage.windowsazure.com/
[xplat-tools]: /de-de/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /de-de/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /de-de/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /de-de/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /de-de/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://azure.microsoft.com/pricing/details/#header-1
[scale-websites]: /de-de/manage/services/web-sites/how-to-scale-websites/





<!--HONumber=42-->
