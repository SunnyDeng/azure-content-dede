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
In diesem Artikel werden Ihnen die zum Erstellen einer PHP-Web-app in [Azure Anwendungsdiensts] mithilfe der [Azure Preview Portal](https://portal.azure.com), die [Azure CLI][Azure CLI], oder die [Azure PowerShell-Cmdlets][powershell-cmdlets].

Im Allgemeinen erstellen eine PHP-Web-app unterscheidet sich nicht, dass das Erstellen *alle* Web-app in Azure-App-Dienst. PHP ist standardmäßig für alle Web-Apps aktiviert. Informationen zu Konfiguration von PHP (oder Ihre eigenen benutzerdefinierte PHP-Laufzeit bereitstellen), finden Sie unter [Konfigurieren Sie PHP in Azure App Service Web-Apps].

Jede unten beschriebene Option zeigt, wie Sie eine Web-App in einer freigegebenen Hostingumgebung kostenlos erstellen, jedoch mit eingeschränkter CPU-Auslastung und Bandbreitennutzung. Weitere Informationen finden Sie unter [App Preisen]. Weitere Informationen zum Aktualisieren und skalieren Ihre Web-app im App-Dienst finden Sie unter [Skalieren eine Webanwendung in Azure-App-Dienst].

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen einer PHP-Webanwendung, die mithilfe von Azure Preview-Portal.

Wenn Sie eine PHP-Web-app in das Azure Preview Portal erstellen, haben Sie drei Optionen:

- **Schnellerfassung** -finden Sie unter [Vorgehensweise: erstellen eine Webanwendung mithilfe das Azure-vorschauportal](../web-sites-create-deploy.md#createawebsiteportal)
- **Mit Datenbank erstellen** -finden Sie unter [eine PHP-MySQL-Webanwendung in Azure-App-Dienst erstellen und Bereitstellen über Git]
- **Von Marketplace** -finden Sie unter [erstellen eine WordPress-Webanwendung in Azure-App-Dienst]

## Erstellen einer PHP-Webanwendung, die mithilfe der Azure-Befehlszeilenschnittstelle

So erstellen Sie eine PHP-Web-app mithilfe der Azure-CLI stimmen die folgenden:

1. Installieren der Azure-Befehlszeilenschnittstelle wie hier beschrieben: [der Azure-Befehlszeilenschnittstelle installieren](../xplat-cli.md#install).

1. Herunterladen und Importieren der Einstellungsdatei veröffentlichen, indem Sie wie hier beschrieben: [herunterladen und Importieren der Veröffentlichungseinstellungen](../xplat-cli.md#configure).

1. Geben Sie in der Befehlszeile Folgendes ein:

		azure site create MyWebAppName

Die URL für die neu erstellte Webanwendung `http://MyWebAppName.azurewebsites.net`.

Beachten Sie, dass Sie den Befehl `azure site create` mit einer der folgenden Optionen ausführen können:

* `--location [location name]`. Diese Option können Sie den Speicherort des Rechenzentrums an, in dem Ihre Webanwendung (z. B. erstellt wird "USA, Westen"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `--hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Web-App auswählen.
* `--git`. Mithilfe dieser Option können Sie Git zur Veröffentlichung Ihrer Web-App verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Web-App erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Web-App verweist.

## Erstellen einer PHP-Web-App über die Azure PowerShell-Cmdlets

Gehen Sie zum Erstellen einer PHP-Web-App mithilfe von Azure PowerShell-Cmdlets wie folgt vor:

1. Installieren der Azure-PowerShell-Cmdlets wie hier beschrieben: [Erste Schritte mit Azure PowerShell](/develop/php/how-to-guides/powershell-cmdlets/#GetStarted).

1. Herunterladen und Importieren der Einstellungsdatei veröffentlichen, indem Sie wie hier beschrieben: [Vorgehensweise: Importieren der Veröffentlichungseinstellungen](/develop/php/how-to-guides/powershell-cmdlets/#ImportPubSettings).

1. Öffnen Sie eine PowerShell-Eingabeaufforderung, und führen Sie folgenden Befehl aus:

		New-AzureWebsite MyWebAppName

Die URL für die neu erstellte Webanwendung `http://MyWebAppName.azurewebsites.net`.

Beachten Sie, dass Sie den Befehl `New-AzureWebsite` mit einer der folgenden Optionen ausführen können:

* `-Location [location name]`. Diese Option können Sie den Speicherort des Rechenzentrums an, in dem Ihre Webanwendung (z. B. erstellt wird "USA, Westen"). Wenn Sie diese Option auslassen, werden Sie aufgefordert, einen Standort auszuwählen.
* `-Hostname [custom host name]`. Mithilfe dieser Option können Sie einen benutzerdefinierten Hostnamen für Ihre Web-App auswählen.
* `-Git`. Mithilfe dieser Option können Sie Git zur Veröffentlichung Ihrer Web-App verwenden, indem Sie Git-Repositorys sowohl in Ihrem lokalen Anwendungsverzeichnis als auch im Rechenzentrum Ihrer Web-App erstellen. Falls Ihr lokaler Ordner bereits ein Git-Repository ist, fügt der Befehl ein neues Remoterepository zu dem bereits vorhandenen Repository hinzu, das auf das Repository im Rechenzentrum Ihrer Web-App verweist.

## Nächste Schritte

Nachdem Sie eine PHP-Web-App in Azure App Service erstellt haben, können Sie Ihre App verwalten, konfigurieren, überwachen, bereitstellen und skalieren. Weitere Informationen finden Sie unter den folgenden Links:

* [Konfigurieren von Webanwendungen in Azure-App-Dienst](web-sites-configure.md)
* [Konfigurieren Sie PHP in Azure Anwendungsdiensts Web-Apps]
* [Verwaltung von Webanwendungen, die mit der Azure-Verwaltungsportal](web-sites-manage.md)
* [Überwachen von Webanwendungen in Azure-App-Dienst](web-sites-monitor.md)
* [Skalieren einer Web-App in Azure App Service]
* [Fortlaufende Bereitstellung mit GIT in Azure-App-Dienst](web-sites-publish-source-control.md)

Lernprogramme von Entwicklern für Entwickler finden Sie auf der Seite [Lernprogramme im PHP Developer Center](/develop/php/tutorials/).

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715).

[Azure Anwendungsdiensts]: http://go.microsoft.com/fwlink/?LinkId=529714
[Azure Portal]: http://go.microsoft.com/fwlink/?LinkId=529715
[Azure CLI]: ../xplat-cli.md
[powershell-cmdlets]: ../powershell-install-configure.md
[Konfigurieren Sie PHP in Azure Anwendungsdiensts Web-Apps]: web-sites-php-configure.md
[Konfigurieren Sie PHP in Azure App Service Web-Apps]: web-sites-php-configure.md
[eine PHP-MySQL-Webanwendung in Azure-App-Dienst erstellen und Bereitstellen über Git]: web-sites-php-mysql-deploy-use-git.md
[erstellen eine WordPress-Webanwendung in Azure-App-Dienst]: web-sites-php-web-site-gallery.md
[App Preisen]: /pricing/details/app-service/
[Skalieren eine Webanwendung in Azure-App-Dienst]: web-sites-scale.md
[Skalieren einer Web-App in Azure App Service]: web-sites-scale.md
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->