<properties
	pageTitle="Konfigurieren von PHP in Azure App Service-Web-Apps "
	description="Erfahren Sie mehr über die Konfiguration der PHP-Standardinstallation oder das Hinzufügen einer benutzerdefinierten PHP-Installation für Web-Apps in Azure App Service."
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tomfitz"/>

#Konfigurieren von PHP in Azure App Service-Web-Apps 

## Einführung

In diesem Leitfaden erfahren Sie, wie Sie die integrierte PHP-Laufzeit in Web-Apps für [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) konfigurieren, eine benutzerdefinierte PHP-Laufzeit angeben und Erweiterungen aktivieren. Um App Service zu verwenden, registrieren Sie sich für die [kostenlose Testversion]. Um diese Anleitung optimal zu nutzen, sollten Sie zuerst eine PHP-Web-App in App Service erstellen.

## Gewusst wie: Ändern der integrierten PHP-Konfiguration
PHP 5.4 ist standardmäßig installiert und kann sofort verwendet werden, wenn Sie eine App Service-Web-App erstellen. Die beste Möglichkeit, um die verfügbare Versionsrevision, die Standardkonfiguration und die aktivierten Erweiterungen anzuzeigen, ist die Bereitstellung eines Skripts, das die Funktion [phpinfo()] abruft.

PHP 5.5 und PHP 5.6 sind ebenfalls verfügbar, aber nicht standardmäßig aktiviert. Gehen Sie folgendermaßen vor, um die PHP-Version zu aktualisieren:

1. Navigieren Sie im [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715) zu Ihrer Web-App, und klicken Sie auf die Schaltfläche **Einstellungen**.

	![Web-App-Einstellungen][settings-button]

2. Wählen Sie auf dem Blatt **Einstellungen** die Option **Anwendungseinstellungen** aus, und wählen Sie die neue PHP-Version aus.

    ![Anwendungseinstellungen][application-settings]

3. Klicken Sie im oberen Bereich des Blatts **Web app settings** auf die Schaltfläche **Speichern**.

	![Speichern von Konfigurationseinstellungen][save-button]

Für jede integrierte PHP-Laufzeit können Sie alle Konfigurationsoptionen ändern, die keine Direktiven nur auf Systemebene sind, indem Sie folgende Schritte ausführen. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven].)

1. Fügen Sie eine [.user.ini]-Datei zum Stammverzeichnis hinzu.
2. Fügen Sie der Datei  `.user.ini` die Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für die Datei  `php.ini`. Wenn Sie zum Beispiel die Einstellung  `display_errors` aktivieren und die Einstellung  `upload_max_filesize` auf 10M festlegen möchten, würde die Datei  `.user.ini` diesen Text enthalten:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Stellen Sie Ihre Web-App bereit.
4. Starten Sie die Web-App neu. (Der Neustart ist erforderlich, da die Frequenz, mit der PHP  `.user.ini`-Dateien liest, durch die Einstellung  `user_ini.cache_ttl` geregelt wird. Dies ist eine Einstellung auf Systemebene, die standardmäßig 300 Sekunden (5 Minuten) beträgt. Durch einen Neustart der Web-App wird PHP gezwungen, die neuen Einstellungen in der Datei `.user.ini` zu lesen.)

Alternativ zur Verwendung einer `.user.ini`-Datei können Sie auch die Funktion [ini_set()] in Skripts verwenden, um Konfigurationsoptionen festzulegen, die keine Direktiven auf Systemebene sind.

## Gewusst wie: Aktivieren von Erweiterungen in der Standard-PHP-Laufzeit
Wie im vorherigen Anschnitt angegeben, ist die Standardkonfiguration die beste Möglichkeit zum Anzeigen der Standard-PHP-Version, und die aktivierten Erweiterungen dienen zur Bereitstellung eines Skripts, das [phpinfo()] abruft. Um zusätzliche Erweiterungen zu aktivieren, folgen Sie den Schritten unten.

1. Fügen Sie dem Stammverzeichnis das Verzeichnis `bin` hinzu.
2. Legen Sie Dateien mit der Erweiterung  `.dll` im Verzeichnis  `bin` ab (zum Beispiel  `php_mongo.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP kompatibel sind (welche zu diesem Zeitpunkt PHP 5.4 ist) und kompatibel mit VC9 sowie nicht threadsicher (non-thread-safe, nts) sind.
3. Stellen Sie Ihre Web-App bereit.
4. Navigieren Sie im Azure-Portal zu Ihrer Web-App, und klicken Sie auf die Schaltfläche **Einstellungen**.

	![Web App Settings][settings-button]

5. Wählen Sie auf dem Blatt **Einstellungen** die Option **Anwendungseinstellungen** aus, und führen Sie einen Bildlauf zum Bereich **App-Einstellungen** aus.
6. Erstellen Sie im Bereich **App-Einstellungen** den Schlüssel **PHP_EXTENSIONS**. Der Wert für diesen Schlüssel ist ein Pfad relativ zum Stammverzeichnis der Website: **bin\Ihre-externe-Datei**.

	![Enable extension in app settings][php-extensions]

7. Klicken Sie im oberen Bereich des Blatts **Web app settings** auf die Schaltfläche **Speichern**.

	![Save configuration settings][save-button]

Zend-Erweiterungen werden durch den Schlüssel **PHP_ZENDEXTENSIONS** ebenfalls unterstützt. Um mehrere Erweiterungen zu aktivieren, fügen Sie eine durch Trennzeichen getrennte Liste von `.dll`-Dateien für den Wert der App-Einstellung bei.

## Gewusst wie: Verwenden einer benutzerdefinierten PHP-Laufzeit
Anstelle der Standard-PHP-Laufzeit können App Service-Web-Apps auch eine PHP-Laufzeit verwenden, die Sie für die Ausführung von PHP-Skripten angeben. Die Laufzeit, die Sie angeben, kann durch eine  `php.ini`-Datei konfiguriert werden, die Sie ebenfalls bereitstellen. Folgen Sie den Schritten unten, um eine benutzerdefinierte PHP-Laufzeit mit Web-Apps zu verwenden.

1. Besorgen Sie sich eine nicht threadsichere, VC9-kompatible Version von PHP für Windows. Neuere Versionen von PHP für Windows finden Sie hier: [http://windows.php.net/download/]. Ältere Versionen finden Sie hier im Archiv: [http://windows.php.net/downloads/releases/archives/].
2. Ändern Sie die Datei  `php.ini` für Ihre Laufzeit. Beachten Sie, dass Konfigurationseinstellungen, die reine Systemebenendirektiven sind, von Web-Apps ignoriert werden. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven].)
3. Optional können Sie Ihrer PHP-Laufzeit auch Erweiterungen hinzufügen und diese in der Datei  `php.ini` aktivieren.
4. Fügen Sie dem Stammverzeichnis das Verzeichnis `bin` hinzu, und legen Sie das Verzeichnis, das die PHP-Laufzeit enthält, darin ab (zum Beispiel `bin\php`).
5. Stellen Sie Ihre Web-App bereit.
4. Navigieren Sie im Azure-Portal zu Ihrer Web-App, und klicken Sie auf die Schaltfläche **Einstellungen**.

	![Web App Settings][settings-button]

7. Wählen Sie auf dem Blatt **Einstellungen** die Option **Anwendungseinstellungen** aus, und führen Sie einen Bildlauf zum Bereich **Handlerzuordnungen** aus. Geben Sie im Feld "Erweiterung" die Zeichenfolge "*.php" ein, und fügen Sie der ausführbaren Datei `php-cgi.exe` den Pfad hinzu. Wenn Sie die PHP-Laufzeit im Verzeichnis `bin` des Stammverzeichnisses Ihrer Anwendung abgelegt haben, lautet der Pfad `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Specify handler in hander mappings][handler-mappings]

8. Klicken Sie im oberen Bereich des Blatts **Web app settings** auf die Schaltfläche **Speichern**.

	![Save configuration settings][save-button]

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

[Kostenlose Testversion]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Liste der php.ini-Direktiven]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/

<!--HONumber=49-->