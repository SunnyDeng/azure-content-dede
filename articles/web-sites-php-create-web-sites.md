<properties title="How to create a PHP website in Azure Websites" pageTitle="Erstellen einer PHP-Website in Azure-Websites" metaKeywords=" PHP Azure-Websites" description="Erfahren Sie, wie Sie eine PHP-Website in Azure Websites erstellen." documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="tomfitz" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="10/21/2014" ms.author="tomfitz" />

#Erstellen einer PHP-Website in Azure-Websites

In diesem Artikel erfahren Sie, wie Sie eine PHP-Website in [Azure-Websites][waws] über das [Azure-Verwaltungsportal], die [Azure-Befehlszeilentools für Mac und Linux][xplat-tools] oder die [Azure PowerShell-Cmdlets][powershell-cmdlets] erstellen.

Im Allgemeinen unterscheidet sich die Erstellung einer PHP-Website nicht von der Erstellung irgend einer anderen Website in Azure-Websites. PHP ist standardmäßig für alle Websites aktiviert. Informationen zur Konfiguration von PHP (oder zur Bereitstellung Ihrer eigenen benutzerdefinierten PHP-Laufzeit) finden Sie unter [So konfigurieren Sie PHP in Azure-Websites][configure-php].

Jede unten beschriebene Option zeigt, wie Sie eine Website in einer freigegebenen Hostingumgebung kostenlos erstellen, jedoch mit eingeschränkter CPU-Auslastung und Bandbreitennutzung. Weitere Informationen finden Sie in der [Azure-Preisübersicht][websites-pricing]. Informationen zum Aktualisieren und Skalieren Ihrer Website finden Sie unter [Skalieren von Websites][scale-websites].

##Inhaltsverzeichnis
* [Erstellen einer Website über das Azure-Verwaltungsportal](#portal)
* [Erstellen einer Website über die Azure-Befehlszeilentools für Mac und Linux](#XplatTools)
* [Erstellen einer Website über die Azure PowerShell-Cmdlets](#PowerShell)

<h2><a name="portal"></a>Erstellen einer PHP-Website über das Azure-Verwaltungsportal</h2>

Sie haben drei Möglichkeiten, um eine Website im Azure-Verwaltungsportal zu erstellen: Über die **Schnellerfassung**, **mit einer Datenbank** und **aus der Galerie**. Die Anweisungen unten behandeln die Option **Schnellerstellung**. Informationen zu den anderen beiden Optionen finden Sie unter [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git][website-mysql-git] und [Erstellen einer WordPress-Website über die Galerie in Azure][wordpress-gallery].

Gehen Sie zum Erstellen einer PHP-Website über das Azure-Verwaltungsportal wie folgt vor:

1. Melden Sie sich beim [Azure-Verwaltungsportal] an.
1. Klicken Sie unten auf der Seite auf **Neu**, und wählen Sie **Berechnen**, **Website** und **Schnellerfassung**. Geben Sie eine **URL** für die Website an, und wählen Sie die **Region** für die Website aus. Klicken Sie abschließend auf **Website erstellen**.

	![Select Quick Create web site](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

<h2><a name="XplatTools"></a>Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux</h2>

Gehen Sie zum Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux wie folgt vor:

1. Installieren Sie die Azure-Befehlszeilentools, indem Sie folgende Anleitung befolgen: [Installation der Azure-Befehlszeilentools für Mac und Linux](/en-us/develop/php/how-to-guides/command-line-tools/#Download).

1. Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Herunterladen und Importieren der Veröffentlichungseinstellungen](/en-us/develop/php/how-to-guides/command-line-tools/#Account).

1. Geben Sie in der Befehlszeile Folgendes ein:

		azure site create MySiteName

Die URL für die neu erstellte Website wird "http://MySiteName.azurewebsites.net" sein.  
 
Beachten Sie, dass Sie den Befehl "azure site create" mit einer der folgenden Optionen ausführen können:

* `--location [Standortname]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `--hostname [benutzerdefinierter Hostname]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
* `--git`. Mit dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Datencenter Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Website verweist.

Informationen zu weiteren Optionen finden Sie unter [Erstellen und Verwalten einer Azure-Website](/en-us/develop/php/how-to-guides/command-line-tools/#WebSites).

<h2><a name="PowerShell"></a>Erstellen einer PHP-Website über die Azure PowerShell-Cmdlets</h2>

Gehen Sie zum Erstellen einer PHP-Website über die Azure PowerShell-Cmdlets wie folgt vor:

1. Installieren Sie Azure PowerShell-Cmdlets wie hier beschrieben: [Erste Schritte mit Azure PowerShell](/en-us/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Gewusst wie: Importieren der Veröffentlichungseinstellungen](/en-us/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie folgenden Befehl aus:

		New-AzureWebSite MySiteName

Die URL für die neu erstellte Website wird "http://MySiteName.azurewebsites.net" sein.  
 
Beachten Sie, dass Sie den Befehl "New-AzureWebSite" mit einer der folgenden Optionen ausführen können:

* `-Location [Standortname]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `-Hostname [benutzerdefinierter Hostname]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
* `-Git`. Mit dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Datencenter Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Website verweist.

Informationen zu weiteren Optionen finden Sie unter [Gewusst wie: Erstellen und Verwalten von Azure-Websites](/en-us/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

<h2><a name="NextSteps"></a>Nächste Schritte</h2>

Nachdem Sie eine PHP-Website in Azure-Websites erstellt haben, können Sie Ihre Website verwalten, konfigurieren, überwachen, bereitstellen und skalieren. Weitere Informationen finden Sie unter den folgenden Links:

* [Konfigurieren von Websites](/en-us/manage/services/web-sites/how-to-configure-websites/)
* [So konfigurieren Sie PHP in Azure-Websites][configure-php]
* [Verwalten von Websites](/en-us/manage/services/web-sites/how-to-manage-websites/)
* [Überwachen von Websites](/en-us/manage/services/web-sites/how-to-monitor-websites/)
* [Skalieren von Websites](/en-us/manage/services/web-sites/how-to-scale-websites/)
* [Veröffentlichen mit Git](/en-us/develop/php/common-tasks/publishing-with-git/)

Lernprogramme von Entwicklern für Entwickler finden Sie auf der Seite [Lernprogramme im PHP Developer Center](/en-us/develop/php/tutorials/).

[waws]: /en-us/manage/services/web-sites/
[Azure Management Portal]: http://manage.windowsazure.com/
[xplat-tools]: /en-us/develop/php/how-to-guides/command-line-tools/
[powershell-cmdlets]: /en-us/develop/php/how-to-guides/powershell-cmdlets/
[configure-php]: /en-us/develop/php/common-tasks/configure-php-web-site/
[website-mysql-git]: /en-us/develop/php/tutorials/website-w-mysql-and-git/
[wordpress-gallery]: /en-us/develop/php/tutorials/website-from-gallery/
[websites-pricing]: http://www.windowsazure.com/en-us/pricing/details/#header-1
[scale-websites]: /en-us/manage/services/web-sites/how-to-scale-websites/
