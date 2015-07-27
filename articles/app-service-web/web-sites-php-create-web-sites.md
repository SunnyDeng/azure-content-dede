<properties
	pageTitle="Erstellen einer PHP-Web-App in Azure App Service"
	description="Erfahren Sie, wie Sie PHP-Web-Apps in Azure App Service erstellen."
	documentationCenter="php"
	services="app-service\web"
	editor="mollybos"
	manager="wpickett"
	authors="tfitzmac"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="hero-article"
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

# Erstellen einer PHP-Web-App in Azure App Service

## Übersicht
Dieser Artikel beschreibt, wie Sie eine PHP-Web-App in [Azure App Service] mithilfe des [Azure-Vorschauportals](https://portal.azure.com), der [Azure-Befehlszeilenschnittstelle (Azure-CLI)][Azure CLI] oder der [Azure PowerShell-Cmdlets][powershell-cmdlets] erstellen.

Im Allgemeinen unterscheidet sich die Erstellung einer PHP-Web-App *nicht* von der Erstellung einer anderen Web-App in Azure App Service. PHP ist standardmäßig für alle Web-Apps aktiviert. Informationen zur Konfiguration von PHP (oder zur Bereitstellung Ihrer eigenen benutzerdefinierten PHP-Laufzeit) finden Sie unter [Konfigurieren von PHP in Azure App Service-Web-Apps].

Jede unten beschriebene Option zeigt, wie Sie eine Web-App in einer freigegebenen Hostingumgebung kostenlos erstellen, jedoch mit eingeschränkter CPU-Auslastung und Bandbreitennutzung. Weitere Informationen finden Sie in der [App Service-Preisübersicht]. Weitere Informationen zum Aktualisieren und Skalieren Ihrer Web-App in App Service finden Sie unter [Skalieren einer Web-App in Azure App Service].

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen einer PHP-Web-App mithilfe des Azure-Vorschauportals

Sie haben drei Optionen, um eine PHP-Web-App im Azure-Vorschauportal zu erstellen:

- **Schnellerfassung**: siehe [Erstellen einer Web-App mithilfe des Azure-Vorschauportals](../web-sites-create-deploy.md#createawebsiteportal)
- **Erstellung mit Datenbank**: siehe [Erstellen einer PHP-MySQL-Web-App in Azure App Service und Bereitstellen über Git]
- **Über den Marketplace**: siehe [Erstellen einer WordPress-Web-App in Azure App Service]

## Erstellen einer PHP-Web-App mithilfe der Azure-Befehlszeilenschnittstelle

Gehen Sie zum Erstellen einer PHP-Web-App mithilfe der Azure-Befehlszeilenschnittstelle wie folgt vor:

1. Installieren Sie die Azure-Befehlszeilenschnittstelle gemäß den Anweisungen unter [Installieren der Azure-Befehlszeilenschnittstelle](../xplat-cli.md#install).

1. Laden Sie die Datei mit den Veröffentlichungseinstellungen herunter, und importieren Sie sie gemäß der [entsprechenden Anleitung](../xplat-cli.md#configure).

1. Geben Sie in der Befehlszeile Folgendes ein:

		azure site create MyWebAppName

Die URL für die neu erstellte Web-App lautet `http://MyWebAppName.azurewebsites.net`.

Beachten Sie, dass Sie den Befehl `azure site create` mit einer der folgenden Optionen ausführen können:

* `--location [location name]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Web-App erstellt wird (z. B. "USA, Westen"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `--hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Web-App auswählen.
* `--git`. Mithilfe dieser Option können Sie Git zur Veröffentlichung Ihrer Web-App verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Web-App erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Web-App verweist.

## Erstellen einer PHP-Web-App über die Azure PowerShell-Cmdlets

Gehen Sie zum Erstellen einer PHP-Web-App mithilfe von Azure PowerShell-Cmdlets wie folgt vor:

1. Installieren Sie die Azure PowerShell-Cmdlets gemäß den Anweisungen unter [Erste Schritte mit Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Laden Sie die Datei mit den Veröffentlichungseinstellungen gemäß den Anweisungen unter [Importieren von Veröffentlichungseinstellungen](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings) herunter.

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie folgenden Befehl aus:

		New-AzureWebsite MyWebAppName

Die URL für die neu erstellte Web-App lautet `http://MyWebAppName.azurewebsites.net`.

Beachten Sie, dass Sie den Befehl `New-AzureWebsite` mit einer der folgenden Optionen ausführen können:

* `-Location [location name]`. Mit dieser Option geben Sie den Standort des Datencenters an, in dem Ihre Web-App erstellt wird (z. B. "USA, Westen"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `-Hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Web-App auswählen.
* `-Git`. Mithilfe dieser Option können Sie Git zur Veröffentlichung Ihrer Web-App verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Web-App erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Web-App verweist.

## Nächste Schritte

Nachdem Sie eine PHP-Web-App in Azure App Service erstellt haben, können Sie Ihre App verwalten, konfigurieren, überwachen, bereitstellen und skalieren. Weitere Informationen finden Sie unter den folgenden Links:

* [Konfigurieren von Web-Apps in Azure App Service](web-sites-configure.md)
* [Konfigurieren von PHP in Azure App Service-Web-Apps]
* [Verwalten von Web-Apps mithilfe des Azure-Portals](web-sites-manage.md)
* [Überwachen von Web-Apps in Azure App Service](web-sites-monitor.md)
* [Skalieren einer Web-App in Azure App Service]
* [Kontinuierliche Bereitstellung mit GIT in Azure App Service](web-sites-publish-source-control.md)

Lernprogramme von Entwicklern für Entwickler finden Sie auf der Seite [Lernprogramme im PHP Developer Center](/develop/php/tutorials/).

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715).

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Portal]: http://go.microsoft.com/fwlink/?LinkId=529715
[Azure CLI]: ../xplat-cli.md
[powershell-cmdlets]: ../powershell-install-configure.md
[Konfigurieren von PHP in Azure App Service-Web-Apps]: web-sites-php-configure.md
[Erstellen einer PHP-MySQL-Web-App in Azure App Service und Bereitstellen über Git]: web-sites-php-mysql-deploy-use-git.md
[Erstellen einer WordPress-Web-App in Azure App Service]: web-sites-php-web-site-gallery.md
[App Service-Preisübersicht]: /pricing/details/app-service/
[Skalieren einer Web-App in Azure App Service]: web-sites-scale.md
 

<!---HONumber=July15_HO3-->