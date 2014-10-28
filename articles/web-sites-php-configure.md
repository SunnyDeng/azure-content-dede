<properties title="How to Configure PHP in Azure Websites" pageTitle="How to Configure PHP in Azure Websites" metaKeywords="Azure, Azure Web Sites, configuration, PHP" description="Learn how to configure the default PHP installation or add a custom PHP installation in Azure Websites." services="Web Sites" documentationCenter="PHP" authors="cephalin" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Konfigurieren von PHP in Azure-Websites

In diesem Leitfaden erfahren Sie, wie Sie die integrierte PHP-Laufzeit in Azure-Websites konfigurieren, eine benutzerdefinierte PHP-Laufzeit bereitstellen und Erweiterungen in Azure-Websites aktivieren. Um Azure-Websites zu nutzen, können Sie sich für eine [kostenlose Testversion][kostenlose Testversion] anmelden. Damit Sie optimal von dieser Anleitung profitieren, sollten Sie zuerst eine PHP-Website in Azure-Websites erstellen (siehe [Lernprogramme im PHP Developer Center][Lernprogramme im PHP Developer Center]). Allgemeine Informationen zum Konfigurieren von Websites in Azure-Websites finden Sie unter [Konfigurieren von Websites][Konfigurieren von Websites].

## Inhaltsverzeichnis

-   [Was ist Azure-Websites?][Was ist Azure-Websites?]
-   [Gewusst wie: Ändern der Standard-PHP-Konfiguration][Gewusst wie: Ändern der Standard-PHP-Konfiguration]
-   [Gewusst wie: Aktivieren von Erweiterungen in der integrierten PHP-Laufzeit][Gewusst wie: Aktivieren von Erweiterungen in der integrierten PHP-Laufzeit]
-   [Gewusst wie: Verwenden einer benutzerdefinierten PHP-Laufzeit][Gewusst wie: Verwenden einer benutzerdefinierten PHP-Laufzeit]
-   [Nächste Schritte][Nächste Schritte]

## <a name="WhatIs"></a>Was ist Azure-Websites?

Azure-Websites ermöglicht Ihnen das Erstellen hoch skalierbarer Websites in Azure. Sie können schnell und einfach Websites in einer hochgradig skalierbaren Cloud-Umgebung bereitstellen, mit der Sie klein beginnen und dann aufskalieren können, wenn mehr Datenverkehr ansteht. Azure-Websites verwendet die Sprachen und Open-Source-Anwendungen Ihrer Wahl und unterstützt die Bereitstellung mit Git, FTP und TFS. Sie können einfach andere Dienste wie MySQL, SQL Database, Caching, CDN und Storage integrieren.

## <a name="ChangeBuiltInPHP"></a>Gewusst wie: Ändern der integrierten PHP-Konfiguration

PHP 5.4 ist standardmäßig installiert und kann sofort verwendet werden, wenn Sie eine Azure-Website erstellen. Die beste Möglichkeit, um die verfügbare Versionsrevision, die Standardkonfiguration und die aktivierten Erweiterungen anzuzeigen, ist die Bereitstellung eines Skripts, das die Funktion [phpinfo()][phpinfo()] abruft.

PHP 5.5 ist ebenfalls verfügbar, aber nicht standardmäßig aktiviert. Gehen Sie folgendermaßen vor, um dies zu aktivieren:

1.  Navigieren Sie im Azure-Portal zum Dashboard Ihrer Website und klicken Sie auf **Konfigurieren**.

    ![Konfigurationsregisterkarte auf dem Websites-Dashboard][Konfigurationsregisterkarte auf dem Websites-Dashboard]

2.  Klicken Sie auf PHP 5.5.

    ![Wählen Sie die PHP-Version aus][Wählen Sie die PHP-Version aus]

3.  Klicken Sie unten auf der Seite auf **Speichern**.

    ![Speichern Sie die Konfigurationseinstellungen][Speichern Sie die Konfigurationseinstellungen]

Für jede der integrierten PHP-Laufzeiten können Sie alle Konfigurationsoptionen ändern, die keine Direktiven nur auf Systemebene sind, indem Sie folgende Schritte ausführen. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven][Liste der php.ini-Direktiven].)

1.  Fügen Sie eine [.user.ini][.user.ini]-Datei zum Stammverzeichnis hinzu.
2.  Fügen Sie der Datei `.user.ini` die Konfigurationseinstellungen hinzu, und verwenden Sie dieselbe Syntax wie für eine `php.ini`-Datei. Wenn Sie zum Beispiel die Einstellung `display_errors` aktivieren und die Einstellung `upload_max_filesize` auf 10M festlegen möchten, würde die Datei `.user.ini` diesen Text enthalten:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

3.  Stellen Sie die Anwendung bereit.
4.  Starten Sie Ihre Website neu. (Der Neustart ist erforderlich, da die Frequenz, mit der PHP `.user.ini`-Dateien liest, durch die Einstellung `user_ini.cache_ttl` geregelt wird. Dies ist eine Einstellung auf Systemebene, die standardmäßig 300 Sekunden (5 Minuten) beträgt. Durch einen Neustart der Website wird PHP gezwungen, die neuen Einstellungen in der Datei `.user.ini` zu lesen.)

Alternativ zur Verwendung einer `.user.ini`-Datei können Sie auch die Funktion [ini\_set()][ini\_set()] in Skripts verwenden, um Konfigurationsoptionen festzulegen, die keine Direktiven auf Systemebene sind.

## <a name="EnableExtDefaultPHP"></a>Gewusst wie: Aktivieren von Erweiterungen in Standard-PHP-Laufzeit

Wie im vorherigen Anschnitt angegeben ist die Standardkonfiguration die beste Möglichkeit, um die Standard-PHP-Version anzuzeigen, und die aktivierten Erweiterungen dienen zur Bereitstellung eines Skripts, das [phpinfo()][phpinfo()] abruft. Um zusätzliche Erweiterungen zu aktivieren, folgen Sie den Schritten unten.

1.  Fügen Sie dem Stammverzeichnis ein `bin`-Verzeichnis hinzu.
2.  Legen Sie Dateien mit der Erweiterung `.dll` im Verzeichnis `bin` ab (zum Beispiel `php_mongo.dll`). Stellen Sie sicher, dass die Erweiterungen mit der Standardversion von PHP kompatibel sind (welche zu diesem Zeitpunkt PHP 5.4 ist) und kompatibel mit VC9 sowie nicht threadsicher (non-thread-safe, nts) sind.
3.  Stellen Sie die Anwendung bereit.
4.  Navigieren Sie zum Website-Dashboard im Azure-Portal, und klicken Sie auf **Konfigurieren**.

    ![Konfigurationsregisterkarte auf dem Websites-Dashboard][Konfigurationsregisterkarte auf dem Websites-Dashboard]

5.  Erstellen Sie im Abschnitt **App-Einstellungen** den Schlüssel **PHP\_EXTENSIONS** und den Wert **bin\\your-ext-file**. Um mehrere Erweiterungen zu aktivieren, fügen Sie eine durch Kommas getrennte Liste von `.dll`-Dateien hinzu.

    ![Aktivieren Sie die Erweiterung in den App-Einstellungen][Aktivieren Sie die Erweiterung in den App-Einstellungen]

6.  Klicken Sie unten auf der Seite auf **Speichern**.

    ![Speichern Sie die Konfigurationseinstellungen][Speichern Sie die Konfigurationseinstellungen]

## <a name="UseCustomPHP"></a>Gewusst wie: Verwenden einer benutzerdefinierten PHP-Laufzeit

Anstelle der Standard-PHP-Laufzeit kann Azure-Websites auch eine PHP-Laufzeit verwenden, die Sie für die Ausführung von PHP-Skripts angeben. Die Laufzeit, die Sie angeben, kann durch eine `php.ini`-Datei konfiguriert werden, die Sie ebenfalls bereitstellen. Folgen Sie den Schritten unten, um eine benutzerdefinierte PHP-Laufzeit in Azure-Websites zu verwenden.

1.  Besorgen Sie sich eine nicht threadsichere, VC9-kompatible Version von PHP für Windows. Neuere Versionen von PHP für Windows finden Sie hier: [][]<http://windows.php.net/download/></a>. Ältere Versionen finden Sie hier im Archiv: [][1]<http://windows.php.net/downloads/releases/archives/></a>.
2.  Ändern Sie die Datei `php.ini` für Ihre Laufzeit. Beachten Sie, dass Konfigurationseinstellungen, die reine Systemebenendirektiven sind, von Azure-Websites ignoriert werden. (Informationen zu Direktiven nur auf Systemebene finden Sie unter [Liste der php.ini-Direktiven][Liste der php.ini-Direktiven]).
3.  Optional können Sie Ihrer PHP-Laufzeit auch Erweiterungen hinzufügen und diese in der Datei `php.ini` aktivieren.
4.  Fügen Sie dem Stammverzeichnis das Verzeichnis `bin` hinzu, und legen Sie das Verzeichnis, das die PHP-Laufzeit enthält, darin ab (zum Beispiel `bin\php`).
5.  Stellen Sie die Anwendung bereit.
6.  Navigieren Sie zum Website-Dashboard im Azure-Portal, und klicken Sie auf **Konfigurieren**.

    ![Konfigurationsregisterkarte auf dem Websites-Dashboard][Konfigurationsregisterkarte auf dem Websites-Dashboard]

7.  Fügen Sie im Abschnitt **Handlerzuordnungen** `*.php` zu ERWEITERUNG hinzu, und fügen Sie den Pfad zur ausführbaren Datei `php-cgi.exe` ein. Wenn Sie die PHP-Laufzeit im Verzeichnis `bin` des Stammverzeichnisses Ihrer Anwendung abgelegt haben, lautet der Pfad `D:\home\site\wwwroot\bin\php\php-cgi.exe`.

    ![Handler in Handlerzuordnungen angeben][Handler in Handlerzuordnungen angeben]

8.  Klicken Sie unten auf der Seite auf **Speichern**.

    ![Speichern Sie die Konfigurationseinstellungen][Speichern Sie die Konfigurationseinstellungen]

## <a name="NextSteps"></a>Nächste Schritte

Nachdem Sie jetzt wissen, wie Sie PHP auf Azure-Websites konfigurieren, können Sie den Links unten folgen, um mehr über die nächsten Schritte zu erfahren.

-   [Configure, monitor, and scale your web sites in Azure (Konfigurieren, Überwachen und Skalieren von Websites in Azure, in englischer Sprache)][Configure, monitor, and scale your web sites in Azure (Konfigurieren, Überwachen und Skalieren von Websites in Azure, in englischer Sprache)]
-   [Herunterladen des Azure SDK für PHP][Herunterladen des Azure SDK für PHP]

  [kostenlose Testversion]: https://www.windowsazure.com/de-de/pricing/free-trial/
  [Lernprogramme im PHP Developer Center]: https://www.windowsazure.com/de-de/develop/php/tutorials/
  [Konfigurieren von Websites]: https://www.windowsazure.com/de-de/manage/services/web-sites/how-to-configure-websites/
  [Was ist Azure-Websites?]: #WhatIs
  [Gewusst wie: Ändern der Standard-PHP-Konfiguration]: #ChangeBuiltInPHP
  [Gewusst wie: Aktivieren von Erweiterungen in der integrierten PHP-Laufzeit]: #EnableExtDefaultPHP
  [Gewusst wie: Verwenden einer benutzerdefinierten PHP-Laufzeit]: #UseCustomPHP
  [Nächste Schritte]: #NextSteps
  [phpinfo()]: http://php.net/manual/en/function.phpinfo.php
  [Konfigurationsregisterkarte auf dem Websites-Dashboard]: ./media/web-sites-php-configure/configure.png
  [Wählen Sie die PHP-Version aus]: ./media/web-sites-php-configure/select-php-version.png
  [Speichern Sie die Konfigurationseinstellungen]: ./media/web-sites-php-configure/save-button.png
  [Liste der php.ini-Direktiven]: http://www.php.net/manual/en/ini.list.php
  [.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php
  [ini\_set()]: http://www.php.net/manual/en/function.ini-set.php
  [Aktivieren Sie die Erweiterung in den App-Einstellungen]: ./media/web-sites-php-configure/app-settings.png
  []: http://windows.php.net/download/
  [1]: http://windows.php.net/downloads/releases/archives/
  [Handler in Handlerzuordnungen angeben]: ./media/web-sites-php-configure/handler-mappings.png
  [Configure, monitor, and scale your web sites in Azure (Konfigurieren, Überwachen und Skalieren von Websites in Azure, in englischer Sprache)]: http://www.windowsazure.com/de-de/manage/services/web-sites/
  [Herunterladen des Azure SDK für PHP]: http://www.windowsazure.com/de-de/develop/php/common-tasks/download-php-sdk/
