<properties title="How to create a PHP web site in Azure Web Sites" pageTitle="How to create a PHP web site in Azure Web Sites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP web site in Azure Web Sites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="bjsmith" authors="" />

Erstellen einer PHP-Website in Azure-Websites
=============================================

Dieser Artikel beschreibt, wie Sie eine PHP-Website in [Azure-Websites](/de-de/manage/services/web-sites/) mithilfe des [Azure-Verwaltungsportal](http://manage.windowsazure.com/), der [Azure-Befehlszeilentools für Mac und Linux](/de-de/develop/php/how-to-guides/command-line-tools/) oder der [Azure PowerShell-Cmdlets](/de-de/develop/php/how-to-guides/powershell-cmdlets/) erstellen.

Im Allgemeinen unterscheidet sich die Erstellung einer PHP-Website nicht von der Erstellung *irgendeiner anderen* Website in Azure-Websites. PHP ist standardmäßig für alle Websites aktiviert. Informationen zur Konfiguration von PHP (oder zur Bereitstellung Ihrer eigenen benutzerdefinierten PHP-Laufzeit) finden Sie unter [So konfigurieren Sie PHP in Azure-Websites](/de-de/develop/php/common-tasks/configure-php-web-site/).

Jede unten beschriebene Option zeigt, wie Sie eine Website in einer freigegebenen Hostingumgebung kostenlos erstellen, jedoch mit eingeschränkter CPU-Auslastung und Bandbreitennutzung. Weitere Informationen finden Sie unter [Azure-Preisübersicht](http://www.windowsazure.com/de-de/pricing/details/#header-1). Informationen zum Aktualisieren und Skalieren Ihrer Website finden Sie unter [Skalieren von Websites](/de-de/manage/services/web-sites/how-to-scale-websites/).

Inhaltsverzeichnis
------------------

-   [Erstellen einer Website über das Azure-Verwaltungsportal](#portal)
-   [Erstellen einer Website über die Azure-Befehlszeilentools für Mac und Linux](#XplatTools)
-   [Erstellen einer Website über die Azure PowerShell-Cmdlets](#PowerShell)

Erstellen einer PHP-Website über das Azure-Verwaltungsportal
------------------------------------------------------------

Sie haben drei Optionen, um eine Website im Azure-Verwaltungsportal zu erstellen: **Schnellerfassung**, **Create with Database** und **From Gallery**. Die Anweisungen unten behandeln die Option **Schnellerstellung**. Informationen zu den anderen beiden Optionen finden Sie unter [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git](/de-de/develop/php/tutorials/website-w-mysql-and-git/) und [Erstellen einer WordPress-Website über die Galerie in Azure](/de-de/develop/php/tutorials/website-from-gallery/).

Die Erstellung einer PHP-Website über das Azure-Verwaltungsportal geschieht folgendermaßen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal](http://manage.windowsazure.com/) an.
2.  Klicken Sie unten auf der Seite auf **Neu**, wählen Sie **Berechnen**, **Website** und **Schnellerfassung**. Geben Sie eine **URL** für die Website an, und wählen Sie die **Region** für die Website aus. Klicken Sie anschließend auf **Website erstellen**.

    ![Schnellerfassung der Website auswählen](./media/web-sites-php-create-web-sites/select-quickcreate-website.png)

Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux
-------------------------------------------------------------------------------

Gehen Sie zum Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux wie folgt vor:

1.  Installieren Sie die Azure-Befehlszeilentools, indem Sie folgende Anleitung befolgen: [Installation der Windows Azure-Befehlszeilentools für Mac und Linux](/de-de/develop/php/how-to-guides/command-line-tools/#Download).

2.  Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Herunterladen und Importieren der Veröffentlichungseinstellungen](/de-de/develop/php/how-to-guides/command-line-tools/#Account).

3.  Geben Sie in der Befehlszeile Folgendes ein:

         azure site create MySiteName

Die URL für die neu erstellte Website wird `http://MySiteName.azurewebsites.net` sein.

Beachten Sie, dass Sie den Befehl `azure site create` mit einer der folgenden Optionen ausführen können:

-   `--location [location name]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
-   `--hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
-   `--git`. Mithilfe dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Datencenter Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Datencenter Ihrer Website verweist.

Informationen zu zusätzlichen Optionen finden Sie unter [Erstellen und Verwalten einer Azure-Website](/de-de/develop/php/how-to-guides/command-line-tools/#WebSites).

Erstellen einer PHP-Website über die Azure PowerShell-Cmdlets
-------------------------------------------------------------

Die Erstellung einer PHP-Website über Azure PowerShell-Cmdlets geschieht folgendermaßen:

1.  Installieren Sie Azure PowerShell-Cmdlets wie hier beschrieben: [Erste Schritte mit Azure PowerShell](/de-de/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

2.  Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Gewusst wie: Veröffentlichungseinstellungen importieren](/de-de/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

3.  Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie folgenden Befehl aus:

         New-AzureWebSite MySiteName

Die URL für die neu erstellte Website wird `http://MySiteName.azurewebsites.net` sein.

Beachten Sie, dass Sie den Befehl `New-AzureWebSite` mit einer der folgenden Optionen ausführen können:

-   `-Location [location name]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
-   `-Hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
-   `-Git`. Mithilfe dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Datencenter Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Datencenter Ihrer Website verweist.

Informationen zu weiteren Optionen finden Sie unter [Gewusst wie: Erstellen und Verwalten von Azure-Websites](/de-de/develop/php/how-to-guides/powershell-cmdlets/#WebSite).

Nächste Schritte
----------------

Nachdem Sie eine PHP-Website in Azure-Websites erstellt haben, können Sie Ihre Website verwalten, konfigurieren, überwachen, bereitstellen und skalieren. Weitere Informationen finden Sie unter den folgenden Links:

-   [Konfigurieren von Websites](/de-de/manage/services/web-sites/how-to-configure-websites/)
-   [So konfigurieren Sie PHP in Azure-Websites](/de-de/develop/php/common-tasks/configure-php-web-site/)
-   [Verwalten von Websites](/de-de/manage/services/web-sites/how-to-manage-websites/)
-   [Überwachen von Websites](/de-de/manage/services/web-sites/how-to-monitor-websites/)
-   [Skalieren von Websites](/de-de/manage/services/web-sites/how-to-scale-websites/)
-   [Veröffentlichen mit Git](/de-de/develop/php/common-tasks/publishing-with-git/)

Lernprogramme von Entwicklern für Entwickler finden Sie auf der Seite [Lernprogramme im PHP Developer Center](/de-de/develop/php/tutorials/).

