<properties title="How to create a PHP website in Azure Websites" pageTitle="How to create a PHP website in Azure Websites" metaKeywords="PHP Azure Web Sites" description="Learn how to create a PHP website in Azure Websites" documentationCenter="PHP" services="Web Sites" editor="mollybos" manager="wpickett" authors="cephalin" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Erstellen einer PHP-Website in Azure-Websites

Dieser Artikel beschreibt, wie Sie eine PHP-Website in [Azure-Websites][Azure-Websites] mithilfe des [Azure-Verwaltungsportals][Azure-Verwaltungsportals], der [Azure-Befehlszeilentools für Mac und Linux][Azure-Befehlszeilentools für Mac und Linux] oder der [Azure PowerShell-Cmdlets][Azure PowerShell-Cmdlets] erstellen.

Im Allgemeinen unterscheidet sich die Erstellung einer PHP-Website nicht von der Erstellung *irgendeiner anderen* Website in Azure-Websites. PHP ist standardmäßig für alle Websites aktiviert. Informationen zur Konfiguration von PHP (oder zur Bereitstellung Ihrer eigenen benutzerdefinierten PHP-Laufzeit) finden Sie unter [So konfigurieren Sie PHP in Azure-Websites][So konfigurieren Sie PHP in Azure-Websites].

Jede unten beschriebene Option zeigt, wie Sie eine Website in einer freigegebenen Hostingumgebung kostenlos erstellen, jedoch mit eingeschränkter CPU-Auslastung und Bandbreitennutzung. Weitere Informationen finden Sie unter [Azure-Preisübersicht][Azure-Preisübersicht]. Informationen zum Aktualisieren und Skalieren Ihrer Website finden Sie unter [Skalieren von Websites][Skalieren von Websites].

## Inhaltsverzeichnis

-   [Erstellen einer Website über das Azure-Verwaltungsportal][Erstellen einer Website über das Azure-Verwaltungsportal]
-   [Erstellen einer Website über die Azure-Befehlszeilentools für Mac und Linux][Erstellen einer Website über die Azure-Befehlszeilentools für Mac und Linux]
-   [Erstellen einer Website über die Azure PowerShell-Cmdlets][Erstellen einer Website über die Azure PowerShell-Cmdlets]

## <a name="portal"></a>Erstellen einer PHP-Website über das Azure-Verwaltungsportal

Sie haben drei Möglichkeiten, um eine Website im Azure-Verwaltungsportal zu erstellen: Über die **Schnellerfassung**, **mit einer Datenbank** und **aus der Galerie**. Die Anweisungen unten behandeln die Option **Schnellerstellung**. Informationen zu den anderen beiden Optionen finden Sie unter [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git][Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git] und [Erstellen einer WordPress-Website über die Galerie in Azure][Erstellen einer WordPress-Website über die Galerie in Azure].

Gehen Sie zum Erstellen einer PHP-Website über das Azure-Verwaltungsportal wie folgt vor:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportals] an.
2.  Klicken Sie unten auf der Seite auf **Neu**, und wählen Sie **Server**, **Website** und **Schnellerfassung** aus. Geben Sie eine **URL** für die Website an, und wählen Sie die **Region** für die Website aus. Klicken Sie abschließend auf **Website erstellen**.

    ![Schnellerfassung der Website auswählen][Schnellerfassung der Website auswählen]

## <a name="XplatTools"></a>Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux

Gehen Sie zum Erstellen einer PHP-Website über die Azure-Befehlszeilentools für Mac und Linux wie folgt vor:

1.  Installieren Sie die Azure-Befehlszeilentools, indem Sie folgende Anleitung befolgen: [Installation der Windows Azure-Befehlszeilentools für Mac und Linux][Installation der Windows Azure-Befehlszeilentools für Mac und Linux].

2.  Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Herunterladen und Importieren der Veröffentlichungseinstellungen][Herunterladen und Importieren der Veröffentlichungseinstellungen].

3.  Geben Sie in der Befehlszeile Folgendes ein:

        azure site create MySiteName

Die URL für die neu erstellte Website lautet `http://MySiteName.azurewebsites.net`.

Beachten Sie, dass Sie den Befehl `azure site create` mit einer der folgenden Optionen ausführen können:

-   `--location [location name]`. Mit dieser Option geben Sie den Standort des Rechenzentrums an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
-   `--hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
-   `--git`. Mit dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Website verweist.

Informationen zu zusätzlichen Optionen finden Sie unter [Erstellen und Verwalten einer Azure-Website][Erstellen und Verwalten einer Azure-Website].

## <a name="PowerShell"></a>Erstellen einer PHP-Website über die Azure PowerShell-Cmdlets

Gehen Sie zum Erstellen einer PHP-Website über die Azure PowerShell-Cmdlets wie folgt vor:

1.  Installieren Sie Azure PowerShell-Cmdlets wie hier beschrieben: [Erste Schritte mit Azure PowerShell][Erste Schritte mit Azure PowerShell].

2.  Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie diese wie hier beschrieben: [Gewusst wie: Veröffentlichungseinstellungen importieren][Gewusst wie: Veröffentlichungseinstellungen importieren].

3.  Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie folgenden Befehl aus:

        New-AzureWebSite MySiteName

Die URL für die neu erstellte Website lautet `http://MySiteName.azurewebsites.net`.

Beachten Sie, dass Sie den Befehl `New-AzureWebSite` mit einer der folgenden Optionen ausführen können:

-   `-Location [location name]`. Mit dieser Option geben Sie den Standort des Rechenzentrums an, in dem Ihre Website erstellt wird (z. B. "West US"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
-   `-Hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Website auswählen.
-   `-Git`. Mit dieser Option können Sie Git zur Veröffentlichung Ihrer Website verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Website erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Website verweist.

Informationen zu weiteren Optionen finden Sie unter [Gewusst wie: Erstellen und Verwalten von Azure-Websites][Gewusst wie: Erstellen und Verwalten von Azure-Websites].

## <a name="NextSteps"></a>Nächste Schritte

Nachdem Sie eine PHP-Website in Azure-Websites erstellt haben, können Sie Ihre Website verwalten, konfigurieren, überwachen, bereitstellen und skalieren. Weitere Informationen finden Sie unter den folgenden Links:

-   [Konfigurieren von Websites][Konfigurieren von Websites]
-   [So konfigurieren Sie PHP in Azure-Websites][So konfigurieren Sie PHP in Azure-Websites]
-   [Verwalten von Websites][Verwalten von Websites]
-   [Überwachen von Websites][Überwachen von Websites]
-   [Skalieren von Websites][Skalieren von Websites]
-   [Veröffentlichen mit Git][Veröffentlichen mit Git]

Lernprogramme von Entwicklern für Entwickler finden Sie auf der Seite [Lernprogramme im PHP Developer Center][Lernprogramme im PHP Developer Center].

  [Azure-Websites]: /de-de/manage/services/web-sites/
  [Azure-Verwaltungsportals]: http://manage.windowsazure.com/
  [Azure-Befehlszeilentools für Mac und Linux]: /de-de/develop/php/how-to-guides/command-line-tools/
  [Azure PowerShell-Cmdlets]: /de-de/develop/php/how-to-guides/powershell-cmdlets/
  [So konfigurieren Sie PHP in Azure-Websites]: /de-de/develop/php/common-tasks/configure-php-web-site/
  [Azure-Preisübersicht]: http://www.windowsazure.com/de-de/pricing/details/#header-1
  [Skalieren von Websites]: /de-de/manage/services/web-sites/how-to-scale-websites/
  [Erstellen einer Website über das Azure-Verwaltungsportal]: #portal
  [Erstellen einer Website über die Azure-Befehlszeilentools für Mac und Linux]: #XplatTools
  [Erstellen einer Website über die Azure PowerShell-Cmdlets]: #PowerShell
  [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git]: /de-de/develop/php/tutorials/website-w-mysql-and-git/
  [Erstellen einer WordPress-Website über die Galerie in Azure]: /de-de/develop/php/tutorials/website-from-gallery/
  [Schnellerfassung der Website auswählen]: ./media/web-sites-php-create-web-sites/select-quickcreate-website.png
  [Installation der Windows Azure-Befehlszeilentools für Mac und Linux]: /de-de/develop/php/how-to-guides/command-line-tools/#Download
  [Herunterladen und Importieren der Veröffentlichungseinstellungen]: /de-de/develop/php/how-to-guides/command-line-tools/#Account
  [Erstellen und Verwalten einer Azure-Website]: /de-de/develop/php/how-to-guides/command-line-tools/#WebSites
  [Erste Schritte mit Azure PowerShell]: /de-de/develop/php/how-to-guides/powershell-cmdlets/#GetStarted
  [Gewusst wie: Veröffentlichungseinstellungen importieren]: /de-de/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [Gewusst wie: Erstellen und Verwalten von Azure-Websites]: /de-de/develop/php/how-to-guides/powershell-cmdlets/#WebSite
  [Konfigurieren von Websites]: /de-de/manage/services/web-sites/how-to-configure-websites/
  [Verwalten von Websites]: /de-de/manage/services/web-sites/how-to-manage-websites/
  [Überwachen von Websites]: /de-de/manage/services/web-sites/how-to-monitor-websites/
  [Veröffentlichen mit Git]: /de-de/develop/php/common-tasks/publishing-with-git/
  [Lernprogramme im PHP Developer Center]: /de-de/develop/php/tutorials/
