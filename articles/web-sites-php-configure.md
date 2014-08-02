<properties title="How to Configure PHP in Azure Web Sites" pageTitle="How to Configure PHP in Azure Web Sites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Web Sites." services="Web Sites" documentationCenter="PHP" authors="" />

So konfigurieren Sie PHP in Azure-Websites
==========================================

Hier erfahren Sie, wie Sie die integrierte PHP-Laufzeit in Azure-Websites konfigurieren, eine benutzerdefinierte PHP-Laufzeit angeben und Erweiterungen in Azure-Websites aktivieren. Um Azure-Websites zu nutzen, können Sie sich für eine [kostenlose Testversion](https://www.windowsazure.com/en-us/pricing/free-trial/) anmelden. Um optimal von dieser Anleitung zu profitieren, sollten Sie zuerst eine PHP-Website in Azure-Websites erstellen (siehe [Lernprogramme im PHP Developer Center](https://www.windowsazure.com/en-us/develop/php/tutorials/)). Allgemeine Informationen zur Konfiguration von Websites in Azure-Websites finden Sie unter [How to Configure Web Sites](https://www.windowsazure.com/en-us/manage/services/web-sites/how-to-configure-websites/) (Konfigurieren von Websites, in englischer Sprache).

Inhaltsverzeichnis
------------------

-   [Was ist Azure-Websites?](#WhatIs)
-   [Vorgehensweise: Ändern der Standard-PHP-Konfiguration](#ChangeBuiltInPHP)
-   [Vorgehensweise: Aktivieren von Erweiterungen in der integrierten PHP-Laufzeit](#EnableExtDefaultPHP)
-   [Vorgehensweise: Verwenden einer benutzerdefinierten PHP-Laufzeit](#UseCustomPHP)
-   [Nächste Schritte](#NextSteps)

Was ist Azure-Websites?
-----------------------

Azure-Websites ermöglicht Ihnen das Erstellen hoch skalierbarer Websites in Azure. Sie können schnell und einfach Websites in einer hochgradig skalierbaren Cloud-Umgebung bereitstellen, mit der Sie klein beginnen und dann aufskalieren können, wenn mehr Datenverkehr ansteht. Azure-Websites verwendet die Sprachen und Open-Source-Anwendungen Ihrer Wahl, und unterstützt die Bereitstellung mit Git, FTP und TFS. Sie können einfach andere Dienste wie MySQL, SQL Database, Caching, CDN und Storage integrieren.

Vorgehensweise: Ändern der integrierten PHP-Konfiguration
---------------------------------------------------------

PHP 5.4 ist standardmäßig installiert und kann sofort verwendet werden, wenn Sie eine Azure-Website erstellen. Die beste Möglichkeit, um die verfügbare Versionsrevision, die Standardkonfiguration und die aktivierten Erweiterungen anzuzeigen, ist die Bereitstellung eines Skripts, das die Funktion [phpinfo()](http://php.net/manual/en/function.phpinfo.php) abruft.

PHP 5.5 ist ebenfalls verfügbar, aber nicht standardmäßig aktiviert. Gehen Sie folgendermaßen vor, um dies zu aktivieren:

1.  Navigieren Sie zum Website-Dashboard im Azure-Portal, und klicken Sie auf **Konfigurieren**.

    ![Konfigurationsregisterkarte auf dem Websites-Dashboard](./media/web-sites-php-configure/configure.png)

2.  Klicken Sie auf PHP 5.5.

    ![Wählen Sie die PHP-Version aus](./media/web-sites-php-configure/select-php-version.png)

3.  Klicken Sie unten auf der Seite auf **Speichern**.

    ![Speichern Sie die Konfigurationseinstellungen](./media/web-sites-php-configure/save-button.png)

Für jede der integrierten PHP-Laufzeiten können Sie alle Konfigurationsoptionen ändern, die keine Direktiven nur auf Systemebene sind, indem Sie folgende Schritte ausführen. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven](http://www.php.net/manual/en/ini.list.php).)

1.  Fügen Sie eine [.user.ini](http://www.php.net/manual/en/configuration.file.per-user.php)-Datei zum Stammverzeichnis hinzu.
2.  Fügen Sie die Konfigurationseinstellungen zur Datei `.user.ini` hinzu, und verwenden Sie dieselbe Syntax wie für eine `php.ini`-Datei. Wenn Sie zum Beispiel die Einstellung `display_errors` aktivieren möchten und die Einstellung `upload_max_filesize` auf 10M festlegen möchten, würde die Datei `.user.ini` diesen Text enthalten:

         ; Example Settings
         display_errors=On
         upload_max_filesize=10M

3.  Stellen Sie die Anwendung bereit.
4.  Starten Sie die Website neu. (Der Neustart ist erforderlich, da die Frequenz, mit der PHP `.user.ini`-Dateien liest, durch die Einstellung `user_ini.cache_ttl` geregelt wird. Dies ist eine Einstellung auf Systemebene und beträgt standardmäßig 300 Sekunden (5 Minuten). Durch einen Neustart der Website wird PHP gezwungen, die neuen Einstellungen in der Datei `.user.ini` zu lesen.)

Alternativ zur Verwendung einer `.user.ini`-Datei können Sie auch die Funktion [ini\_set()](http://www.php.net/manual/en/function.ini-set.php) in Skripten verwenden, um Konfigurationsoptionen festzulegen, die keine Direktiven auf Systemebene sind.

Vorgehensweise: Aktivieren von Erweiterungen in Standard-PHP-Laufzeit
---------------------------------------------------------------------

Wie im vorherigen Anschnitt angegeben ist die Standardkonfiguration die beste Möglichkeit, um die Standard-PHP-Version anzuzeigen, und die aktivierten Erweiterungen dienen zur Bereitstellung eines Skripts, das [phpinfo()](http://php.net/manual/en/function.phpinfo.php) abruft. Um zusätzliche Erweiterungen zu aktivieren, folgen Sie den Schritten unten.

1.  Fügen Sie ein `bin`-Verzeichnis zum Stammverzeichnis hinzu.
2.  Verschieben Sie `.dll`-Erweiterungsdateien in das Verzeichnis `bin` (zum Beispiel `php_mongo.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP kompatibel sind (welche zu diesem Zeitpunkt PHP 5.4 ist) und kompatibel mit VC9 sowie nicht threadsicher (non-thread-safe, nts) sind.
3.  Stellen Sie die Anwendung bereit.
4.  Navigieren Sie zum Website-Dashboard im Azure-Portal, und klicken Sie auf **Konfigurieren**.

    ![Konfigurationsregisterkarte auf dem Websites-Dashboard](./media/web-sites-php-configure/configure.png)

5.  Erstellen Sie im Abschnitt **app settings** einen Schlüssel **PHP\_EXTENSIONS** und einen Wert **bin\\your-ext-file**. Um mehrfache Erweiterungen zu aktivieren, fügen Sie eine durch Kommas getrennte Liste von `.dll`-Dateien hinzu.

    ![Aktivieren Sie die Erweiterung in den App-Einstellungen](./media/web-sites-php-configure/app-settings.png)

6.  Klicken Sie unten auf der Seite auf **Speichern**.

    ![Speichern Sie die Konfigurationseinstellungen](./media/web-sites-php-configure/save-button.png)

Vorgehensweise: Verwenden einer benutzerdefinierten PHP-Laufzeit
----------------------------------------------------------------

Anstelle der Standard-PHP-Laufzeit kann Azure-Websites auch eine PHP-Laufzeit verwenden, die Sie für die Ausführung von PHP-Skripten angeben. Die Laufzeit, die Sie angeben, kann durch eine `php.ini`-Datei konfiguriert werden, die Sie ebenfalls bereitstellen. Folgen Sie den Schritten unten, um eine benutzerdefinierte PHP-Laufzeit in Azure-Websites zu verwenden.

1.  Besorgen Sie sich eine nicht threadsichere, VC9-kompatible Version von PHP für Windows. Neuere Versionen von PHP für Windows finden Sie hier: <http://windows.php.net/download/>. Ältere Versionen finden Sie hier im Archiv: <http://windows.php.net/downloads/releases/archives/>.
2.  Ändern Sie die `php.ini`-Datei für die gewünschte Laufzeit. Beachten Sie, dass Konfigurationseinstellungen, die Direktiven nur auf Systemebene sind, von Azure-Websites ignoriert werden. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven](http://www.php.net/manual/en/ini.list.php)).
3.  Optional können Sie auch Erweiterungen zu Ihrer PHP-Laufzeit hinzufügen und diese in der Datei `php.ini` aktivieren.
4.  Fügen Sie das Verzeichnis `bin` zum Stammverzeichnis hinzu, und legen Sie das Verzeichnis, das die PHP-Laufzeit enthält, hinein (zum Beispiel `bin\php`).
5.  Stellen Sie die Anwendung bereit.
6.  Navigieren Sie zum Website-Dashboard im Azure-Portal, und klicken Sie auf **Konfigurieren**.

    ![Konfigurationsregisterkarte auf dem Websites-Dashboard](./media/web-sites-php-configure/configure.png)

7.  Fügen Sie im Abschnitt **handler mappings** `*.php` zu EXTENSION hinzu, und fügen Sie den Pfad zur ausführbaren Datei `php-cgi.exe` hinzu. Wenn Sie die PHP-Laufzeit in das `bin`-Verzeichnis im Stammverzeichnis der Anwendung gelegt haben, ist der Pfad `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Geben Sie Handler in Handler mappings an](./media/web-sites-php-configure/handler-mappings.png)

8.  Klicken Sie unten auf der Seite auf **Speichern**.

    ![Speichern Sie die Konfigurationseinstellungen](./media/web-sites-php-configure/save-button.png)

Nächste Schritte
----------------

Nachdem Sie erfahren haben, wie Sie PHP in Azure-Websites konfigurieren, können Sie den Links unten folgen, um mehr über die nächsten Schritte zu erfahren.

-   [Configure, monitor, and scale your web sites in Azure (Konfigurieren, Überwachen und Skalieren von Websites in Azure, in englischer Sprache)](http://www.windowsazure.com/en-us/manage/services/web-sites/)
-   [Herunterladen des Azure SDK für PHP](http://www.windowsazure.com/en-us/develop/php/common-tasks/download-php-sdk/)

