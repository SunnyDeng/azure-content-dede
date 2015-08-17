<properties
	pageTitle="Konfigurieren von PHP in Azure App Service-Web-Apps"
	description="Erfahren Sie mehr über das Konfigurieren der PHP-Standardinstallation oder das Hinzufügen einer benutzerdefinierten PHP-Installation für Web-Apps in Azure App Service."
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
	ms.date="06/24/2015"
	ms.author="tomfitz"/>

#Konfigurieren von PHP in Azure App Service-Web-Apps

## Einführung

In diesem Leitfaden erfahren Sie, wie Sie die integrierte PHP-Laufzeit für Web-Apps in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) konfigurieren, eine benutzerdefinierte PHP-Laufzeit angeben und Erweiterungen aktivieren. Um App Service zu verwenden, registrieren Sie sich für die[ kostenlose Testversion]. Um diese Anleitung optimal zu nutzen, sollten Sie zuerst eine PHP-Web-App in App Service erstellen.

## Vorgehensweise: Ändern der integrierten PHP-Version
PHP 5.4 ist standardmäßig installiert und kann sofort verwendet werden, wenn Sie eine App Service-Web-App erstellen. Die beste Möglichkeit, um die verfügbare Versionsrevision, die Standardkonfiguration und die aktivierten Erweiterungen anzuzeigen, ist die Bereitstellung eines Skripts, das die Funktion [phpinfo()] abruft.

PHP 5.5 und PHP 5.6 sind ebenfalls verfügbar, aber nicht standardmäßig aktiviert. Gehen Sie folgendermaßen vor, um die PHP-Version zu aktualisieren:

### Azure-Portal

1. Navigieren Sie im [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715) zu Ihrer Web-App, und klicken Sie auf die Schaltfläche** Einstellungen**.

	![Web-App-Einstellungen][settings-button]

2. Wählen Sie auf dem Blatt **Einstellungen** die Option **Anwendungseinstellungen** und dann die neue PHP-Version aus.

    ![Anwendungseinstellungen][application-settings]

3. Klicken Sie im oberen Bereich des Blatts **Web-App-Einstellungen** auf die Schaltfläche **Speichern**.

	![Speichern Sie die Konfigurationseinstellungen][save-button]

### Azure PowerShell (Windows)

1. Öffnen Sie Windows PowerShell.
2. Geben Sie `Set-AzureWebsite -PhpVersion [5.4 | 5.5 | 5.6] -Name <site-name>` ein, und drücken Sie anschließend die EINGABETASTE.
3. Die PHP-Version ist jetzt festgelegt.

	![Festlegen der PHP-Version mit Azure PowerShell][SETPHPVERPS]
4. Sie können diese Einstellungen bestätigen, indem Sie `Get-AzureWebiste -Name <site-name>` eingeben und dann die EINGABETASTE drücken.

	![Überprüfen der PHP-Version mit Azure PowerShell][GETPHPVERPS]

### Azure-Befehlszeilen-Schnittstelle (Linux, Mac, Windows)

Um die Azure-Befehlszeilenschnittstelle verwenden zu können, muss **Node.js** auf Ihrem Computer installiert sein.

1. Öffnen Sie das Terminal.
2. Geben Sie `azure site set --php-version [5.4 | 5.5] [site-name]` ein, und drücken Sie anschließend die EINGABETASTE.
3. Die PHP-Version ist jetzt festgelegt.

	![Festlegen der PHP-Version mit der Azure-Befehlszeilenschnittstelle][SETPHPVERCLI]
4. Sie können diese Einstellungen bestätigen, indem Sie `azure site show [site-name]` eingeben und dann die EINGABETASTE drücken.

	![Überprüfen der PHP-Version mit der Azure-Befehlszeilenschnittstelle][GETPHPVERCLI]

## Vorgehensweise: Ändern der integrierten PHP-Konfigurationen

Für jede integrierte PHP-Laufzeit können Sie alle Konfigurationsoptionen ändern, indem Sie folgende Schritte ausführen. (Informationen zu php.ini-Direktiven finden Sie unter [Liste der php.ini-Direktiven]).

### Ändern von PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL-Konfigurationseinstellungen

1. Fügen Sie eine [.user.ini]-Datei zum Stammverzeichnis hinzu.
2. Fügen Sie der Datei `.user.ini` die Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für eine `php.ini`-Datei. Wenn Sie zum Beispiel die Einstellung `display_errors` aktivieren und die Einstellung `upload_max_filesize` auf 10 M festlegen möchten, enthält die Datei `.user.ini` diesen Text:

		; Example Settings
		display_errors=On
		upload_max_filesize=10M

3. Stellen Sie Ihre Web-App bereit.
4. Starten Sie die Web-App neu. (Der Neustart ist erforderlich, da die Frequenz, mit der PHP `.user.ini`-Dateien liest, durch die Einstellung `user_ini.cache_ttl` geregelt wird. Dies ist eine Einstellung auf Systemebene, die standardmäßig 300 Sekunden (5 Minuten) beträgt. Durch einen Neustart der Web-App wird PHP gezwungen, die neuen Einstellungen in der `.user.ini`-Datei zu lesen.)

Alternativ zur Verwendung einer`.user.ini`-Datei können Sie auch die Funktion [ini\_set()] in Skripts verwenden, um Konfigurationsoptionen festzulegen, die keine Direktiven auf Systemebene sind.

### Ändern der PHP\_INI\_SYSTEM-Konfigurationseinstellungen

1. Hinzufügen einer App-Einstellung zu Ihrer Web-App mit Schlüssel `PHP_INI_SCAN_DIR` und Wert `d:\home\site\ini`
2. Erstellen Sie eine `settings.ini`-Datei über die Kudu-Konsole (http://&lt;site-name&gt;.scm.azurewebsite.net) im Verzeichnis `d:\home\site\ini`.
3. Fügen Sie der `settings.ini`-Datei Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für eine php.ini-Datei. Wenn Sie beispielsweise auf die Einstellung `curl.cainfo` für eine `*.crt`-Datei verweisen und, und die Einstellung 'wincache.maxfilesize' auf 512 KB festlegen, enthält die Datei `settings.ini` diesen Text:

		; Example Settings
		curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
		wincache.maxfilesize=512
4. Starten Sie Ihre Web-App neu, um die Änderungen zu laden.

## Vorgehensweise: Aktivieren von Erweiterungen in der PHP-Standardlaufzeit
Wie im vorherigen Anschnitt angegeben ist die Standardkonfiguration die beste Möglichkeit, um die Standard-PHP-Version anzuzeigen, und die aktivierten Erweiterungen dienen zur Bereitstellung eines Skripts, das [phpinfo()] abruft. Führen Sie die folgenden Schritte aus, um zusätzliche Erweiterungen zu aktivieren:

### Konfigurieren über ini-Einstellungen

1. Fügen Sie ein `ext`-Verzeichnis zum `d:\home\site`-Verzeichnis hinzu.
2. Legen Sie Dateien mit der Erweiterung `.dll` im Verzeichnis `ext` ab (zum Beispiel `php_mongo.dll` und `php_xdebug.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP kompatibel sind (welche zu diesem Zeitpunkt PHP 5.4 ist) und kompatibel mit VC9 sowie nicht threadsicher (non-thread-safe, nts) sind.
3. Hinzufügen einer App-Einstellung zu Ihrer Web-App mit Schlüssel `PHP_INI_SCAN_DIR` und Wert `d:\home\site\ini`
4. Erstellen Sie in `d:\home\site\ini` eine `ini`-Datei namens `extensions.ini`.
5. Fügen Sie der `extensions.ini`-Datei Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für eine php.ini-Datei. Wenn Sie beispielsweise die MongoDB- und XDebug-Erweiterungen aktivieren möchten, würde Ihre `extensions.ini`-Datei diesen Text enthalten:

		; Enable Extensions
		extension=d:\home\site\ext\php_mongo.dll
		zend_extension=d:\home\site\ext\php_xdebug.dll
6. Starten Sie Ihre Web-App neu, um die Änderungen zu laden.

### Konfigurieren über App-Einstellungen

1. Fügen Sie dem Stammverzeichnis das Verzeichnis `bin` hinzu.
2. Legen Sie Dateien mit der Erweiterung `.dll` im Verzeichnis `bin` ab (zum Beispiel `php_mongo.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP kompatibel sind (welche zu diesem Zeitpunkt PHP 5.4 ist) und kompatibel mit VC9 sowie nicht threadsicher (non-thread-safe, nts) sind.
3. Stellen Sie Ihre Web-App bereit.
4. Navigieren Sie im Azure-Portal zu Ihrer Web-App, und klicken Sie auf die Schaltfläche **Einstellungen**.

	![Web-App-Einstellungen][settings-button]

5. Wählen Sie auf dem Blatt **Einstellungen** die Option **Anwendungseinstellungen** aus, und führen Sie einen Bildlauf zum Bereich **App-Einstellungen** aus.
6. Erstellen Sie im Bereich **App-Einstellungen** den Schlüssel **PHP\_EXTENSIONS**. Der Wert für diesen Schlüssel ist ein Pfad relativ zum Stammverzeichnis der Website: **bin\\your-ext-file**.

	![Aktivieren Sie die Erweiterung in den App-Einstellungen][php-extensions]

7. Klicken Sie im oberen Bereich des Blatts **Web-App-Einstellungen** auf die Schaltfläche **Speichern**.

	![Speichern Sie die Konfigurationseinstellungen][save-button]

Zend-Erweiterungen werden bei Verwendung des Schlüssels **PHP\_ZENDEXTENSIONS** ebenfalls unterstützt. Um mehrere Erweiterungen zu aktivieren, fügen Sie eine durch Trennzeichen getrennte Liste von `.dll`-Dateien für den Wert der App-Einstellung hinzu.


## Vorgehensweise: Verwenden einer benutzerdefinierten PHP-Laufzeit
Anstelle der PHP-Standardlaufzeit können App Service-Web-Apps auch eine PHP-Laufzeit verwenden, die Sie für die Ausführung von PHP-Skripten angeben. Die Laufzeit, die Sie angeben, kann durch eine `php.ini`-Datei konfiguriert werden, die Sie ebenfalls angeben. Folgen Sie den Schritten unten, um eine benutzerdefinierte PHP-Laufzeit mit Web-Apps zu verwenden.

1. Besorgen Sie sich eine nicht threadsichere, VC9- oder VC11-kompatible Version von PHP für Windows. Neuere Versionen von PHP für Windows finden Sie hier: [http://windows.php.net/download/]. Ältere Versionen finden Sie im Archiv unter: [http://windows.php.net/downloads/releases/archives/].
2. Ändern Sie die Datei `php.ini` für Ihre Laufzeit. Beachten Sie, dass Konfigurationseinstellungen, die reine Systemebenendirektiven sind, von Web-Apps ignoriert werden. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven]).
3. Optional können Sie Ihrer PHP-Laufzeit auch Erweiterungen hinzufügen und in der Datei `php.ini` aktivieren.
4. Fügen Sie dem Stammverzeichnis das Verzeichnis `bin` hinzu, und legen Sie das Verzeichnis, das die PHP-Laufzeit enthält, darin ab (zum Beispiel `bin\php`).
5. Stellen Sie Ihre Web-App bereit.
4. Navigieren Sie im Azure-Portal zu Ihrer Web-App, und klicken Sie auf die Schaltfläche **Einstellungen**.

	![Web-App-Einstellungen][settings-button]

7. Wählen Sie im Blatt **Einstellungen** die Option **Anwendungseinstellungen** aus, und führen Sie einen Bildlauf zum Bereich **Handlerzuordnungen** aus. Geben Sie im Feld "Erweiterung" `*.php` ein, und fügen Sie der ausführbaren Datei `php-cgi.exe` den Pfad hinzu. Wenn Sie die PHP-Laufzeit in Verzeichnis `bin` des Stammverzeichnisses Ihrer Anwendung abgelegt haben, lautet der Pfad `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

	![Handler in Handlerzuordnungen angeben][handler-mappings]

8. Klicken Sie im oberen Bereich des Blatts **Web-App-Einstellungen** auf die Schaltfläche **Speichern**.

	![Speichern Sie die Konfigurationseinstellungen][save-button]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

[ kostenlose Testversion]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Liste der php.ini-Direktiven]: http://www.php.net/manual/en/ini.list.php
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
[ini\_set()]: http://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[http://windows.php.net/download/]: http://windows.php.net/download/
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
 

<!---HONumber=August15_HO6-->