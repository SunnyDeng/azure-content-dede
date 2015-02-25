<properties 
	pageTitle="Konfigurieren von PHP in Azure-Websites" 
	description="Erfahren Sie mehr über die Konfiguration der PHP-Standardinstallation oder das Hinzufügen einer benutzerdefinierten PHP-Installation in Azure Websites." 
	services="" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/18/2014" 
	ms.author="tomfitz"/>

#Konfigurieren von PHP in Azure-Websites

In diesem Leitfaden erfahren Sie, wie Sie die integrierte PHP-Laufzeit in Azure-Websites konfigurieren, eine benutzerdefinierte PHP-Laufzeit bereitstellen und Erweiterungen in Azure-Websites aktivieren. Um Azure-Websites zu verwenden, registrieren Sie sich für die [kostenlose Testversion]. Damit Sie optimal von dieser Anleitung profitieren, sollten Sie zuerst eine PHP-Website in Azure-Websites erstellen (siehe die [Lernprogramme im PHP Developer Center]). Allgemeine Informationen zum Konfigurieren von Websites in Azure-Websites finden Sie unter [Konfigurieren von Websites].

##Inhaltsverzeichnis

* [Was ist Azure-Websites?](#WhatIs)
* [Vorgehensweise: Ändern der Standard-PHP-Konfiguration](#ChangeBuiltInPHP)
* [Vorgehensweise: Aktivieren von Erweiterungen in der integrierten PHP-Laufzeit](#EnableExtDefaultPHP)
* [Vorgehensweise: Verwenden einer benutzerdefinierten PHP-Laufzeit](#UseCustomPHP)
* [Nächste Schritte](#NextSteps)

<h2><a name="WhatIs"></a>Was ist Azure-Websites?</h2>
Azure-Websites ermöglicht Ihnen das Erstellen hoch skalierbarer Websites in Azure. Sie können schnell und einfach Websites in einer hochgradig skalierbaren Cloud-Umgebung bereitstellen, mit der Sie klein beginnen und dann aufskalieren können, wenn mehr Datenverkehr ansteht. Azure-Websites verwendet die Sprachen und Open-Source-Anwendungen Ihrer Wahl und unterstützt die Bereitstellung mit Git, FTP und TFS. Sie können einfach andere Dienste wie MySQL, SQL Database, Caching, CDN und Storage integrieren.

<h2><a name="ChangeBuiltInPHP"></a>Vorgehensweise: Ändern der integrierten PHP-Konfiguration</h2>
PHP 5.4 ist standardmäßig installiert und kann sofort verwendet werden, wenn Sie eine Azure-Website erstellen. Die beste Möglichkeit, um die verfügbare Versionsrevision, die Standardkonfiguration und die aktivierten Erweiterungen anzuzeigen, ist die Bereitstellung eines Skripts, das die Funktion [phpinfo()] aufruft.

PHP 5.5 ist ebenfalls verfügbar, aber nicht standardmäßig aktiviert. Gehen Sie folgendermaßen vor, um dies zu aktivieren:

1. Navigieren Sie im Azure-Portal zum Dashboard Ihrer Website, und klicken Sie auf **Konfigurieren**.

	![Configure tab on Web Sites dashboard][configure]

1. Klicken Sie auf PHP 5.5.

	![Select PHP version][select-php-version]

1. Klicken Sie unten auf der Seite auf **Speichern**.

	![Save configuration settings][save-button]

Für jede der integrierten PHP-Laufzeiten können Sie alle Konfigurationsoptionen ändern, die keine Direktiven nur auf Systemebene sind, indem Sie folgende Schritte ausführen. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven].)

1. Fügen Sie die Datei [.user.ini] zum Stammverzeichnis hinzu.
1. Fügen Sie der Datei  `.user.ini` die Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für die Datei  `php.ini`. Wenn Sie zum Beispiel die Einstellung  `display_errors` aktivieren und die Einstellung  `upload_max_filesize` auf 10M festlegen möchten, würde die Datei  `.user.ini` diesen Text enthalten:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

1. Stellen Sie die Anwendung bereit.
1. Starten Sie Ihre Website neu. (Der Neustart ist erforderlich, da die Frequenz, mit der PHP  `.user.ini`-Dateien liest, durch die Einstellung  `user_ini.cache_ttl` geregelt wird. Dies ist eine Einstellung auf Systemebene, die standardmäßig 300 Sekunden (5 Minuten) beträgt. Durch einen Neustart der Website wird PHP gezwungen, die neuen Einstellungen in der Datei  `.user.ini` zu lesen.)

Alternativ zur Verwendung einer  `.user.ini`-Datei können Sie auch die Funktion [ini_set()] in Skripts verwenden, um Konfigurationsoptionen festzulegen, die keine Direktiven auf Systemebene sind.

<h2><a name="EnableExtDefaultPHP"></a>Vorgehensweise: Aktivieren von Erweiterungen in der Standard-PHP-Laufzeit</h2>
Wie im vorherigen Anschnitt angegeben, ist die Standardkonfiguration die beste Möglichkeit, um die Standard-PHP-Version anzuzeigen, und die aktivierten Erweiterungen dienen zur Bereitstellung eines Skripts, das [phpinfo()] aufruft. Um zusätzliche Erweiterungen zu aktivieren, folgen Sie den Schritten unten.

1. Fügen Sie dem Stammverzeichnis das Verzeichnis  `bin` hinzu.
1. Legen Sie Dateien mit der Erweiterung  `.dll` im Verzeichnis  `bin` ab (zum Beispiel  `php_mongo.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP kompatibel sind (welche zu diesem Zeitpunkt PHP 5.4 ist) und kompatibel mit VC9 sowie nicht threadsicher (non-thread-safe, nts) sind.
1. Stellen Sie die Anwendung bereit.
1. Navigieren Sie zum Website-Dashboard im Azure-Portal, und klicken Sie auf **Konfigurieren**.

	![Configure tab on Web Sites dashboard][configure]

1. Erstellen Sie im Abschnitt **App-Einstellungen** den Schlüssel **PHP_EXTENSIONS** und den Wert **bin\your-ext-fi**. Um mehrfache Erweiterungen zu aktivieren, fügen Sie eine durch Kommas getrennte Liste von  `.dll`-Dateien hinzu.

	![Enable extension in app settings][app-settings]

1. Klicken Sie unten auf der Seite auf **Speichern**.

	![Save configuration settings][save-button]

<h2><a name="UseCustomPHP"></a>Vorgehensweise: Verwenden einer benutzerdefinierten PHP-Laufzeit</h2>
Anstelle der Standard-PHP-Laufzeit kann Azure-Websites auch eine PHP-Laufzeit verwenden, die Sie für die Ausführung von PHP-Skripts angeben. Die Laufzeit, die Sie angeben, kann durch eine  `php.ini`-Datei konfiguriert werden, die Sie ebenfalls bereitstellen. Folgen Sie den Schritten unten, um eine benutzerdefinierte PHP-Laufzeit in Azure-Websites zu verwenden.

1. Besorgen Sie sich eine nicht threadsichere, VC9-kompatible Version von PHP für Windows. Neuere Versionen von PHP für Windows finden Sie hier: [http://windows.php.net/download/]. Ältere Versionen finden Sie hier im Archiv: [http://windows.php.net/downloads/releases/archives/].
1. Ändern Sie die Datei  `php.ini` für Ihre Laufzeit. Beachten Sie, dass Konfigurationseinstellungen, die reine Systemebenendirektiven sind, von Azure-Websites ignoriert werden. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven].)
1. Optional können Sie Ihrer PHP-Laufzeit auch Erweiterungen hinzufügen und diese in der Datei  `php.ini` aktivieren.
1. Fügen Sie dem Stammverzeichnis das Verzeichnis  `bin` hinzu, und legen Sie das Verzeichnis, das die PHP-Laufzeit enthält, darin ab (zum Beispiel  `bin\php`).
1. Stellen Sie die Anwendung bereit.
1. Navigieren Sie zum Website-Dashboard im Azure-Portal, und klicken Sie auf **Konfigurieren**.

	![Configure tab on Web Sites dashboard][configure]

1. Fügen Sie im Abschnitt **Handlerzuordnungen** `*.php`  zu ERWEITERUNG hinzu, und fügen Sie den Pfad zur ausführbaren Datei  `php-cgi.exe` ein. Wenn Sie die PHP-Laufzeit im Verzeichnis  `bin` des Stammverzeichnisses Ihrer Anwendung abgelegt haben, lautet der Pfad  `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specify handler in hander mappings][handler-mappings]

1. Klicken Sie unten auf der Seite auf **Speichern**.

	![Save configuration settings][save-button]

<h2><a name="NextSteps"></a>Nächste Schritte</h2>
Nachdem Sie jetzt wissen, wie Sie PHP auf Azure-Websites konfigurieren, können Sie den Links unten folgen, um mehr über die nächsten Schritte zu erfahren.

- [Konfigurieren, Überwachen und Skalieren von Websites in Azure]
- [Herunterladen des Azure SDK für PHP]


[Kostenlose Testversion]: https://www.windowsazure.com/de-de/pricing/free-trial/
[PHP Developer Center-Lernprogramme]: https://www.windowsazure.com/de-de/develop/php/tutorials/
[Konfigurieren von Websites]: https://www.windowsazure.com/de-de/manage/services/web-sites/how-to-configure-websites/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Liste der php.ini-Direktiven]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[configure]: ./media/web-sites-php-configure/configure.png
[app-settings]: ./media/web-sites-php-configure/app-settings.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[Konfigurieren, Überwachen und Skalieren von Websites in Azure]: http://www.windowsazure.com/de-de/manage/services/web-sites/
[Herunterladen des Azure SDK für PHP]: http://www.windowsazure.com/de-de/develop/php/common-tasks/download-php-sdk/




<!--HONumber=42-->
