<properties
   pageTitle="Verbinden einer Web-App in Azure App Service mit Redis-Cache über das Memcache-Protokoll"
   description="Verbinden Sie eine Web-App in Azure App Service mit Redis-Cache über das Memcache-Protokoll."
   services="app-service\web"
   documentationCenter="php"
   authors="SyntaxC4"
   manager="wpickett"
   editor="riande"/>

<tags
   ms.service="app-service-web"
   ms.devlang="php"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="windows"
   ms.workload="web"
   ms.date="03/31/2015"
   ms.author="cfowler"/>

# Verbinden einer Web-App in Azure App Service mit Redis-Cache über das Memcache-Protokoll

In diesem Artikel erfahren Sie, wie die Verbindung eine WordPress-Webanwendung in [Azure Anwendungsdiensts](http://go.microsoft.com/fwlink/?LinkId=529714) auf [Azure Redis Cache][12] mithilfe der [Memcache][13] Protokoll. Wenn Sie über eine vorhandene Web-App verfügen, die einen Memcached-Server für die Zwischenspeicherung im Arbeitsspeicher verwendet, können Sie diese in Azure App Service migrieren und die Erstanbieter-Lösung für die Zwischenspeicherung in Microsoft Azure mit nur wenigen oder gar keinen Änderungen am Anwendungscode nutzen. Darüber hinaus können Sie Ihre vorhandenen Memcache-Erfahrungen nutzen, um hochgradig skalierbare, verteilte Apps in Azure App Service mit Azure-Redis-Cache für die Zwischenspeicherung im Arbeitsspeicher zu erstellen und dabei gängige Anwendungsframeworks wie .NET, PHP, Node.js, Java und Python zu verwenden.

App Service-Web-Apps ermöglichen dieses Anwendungsszenario mit dem Web-App-Memcache-Shim, einem lokalen Memcache-Server, der als Memcache-Proxy für die Aufrufe zur Zwischenspeicherung von Azure-Redis-Cache fungiert. Dadurch kann jede App, die mithilfe des Memcache-Protokolls kommuniziert, Daten mit Redis-Cache zwischenspeichern. Der Memcache-Shim arbeitet auf Protokollebene, damit er von Anwendungen oder Anwendungsframeworks verwendet werden kann, sofern diese mit dem Memcache-Protokoll kommunizieren.

## Voraussetzungen

Der Web-Apps-Memcache-Shim kann mit jeder Anwendung verwendet werden, sofern diese über das Memcache-Protokoll kommuniziert. In diesem speziellen Beispiel ist die Referenzanwendung eine skalierbare WordPress-Website, die aus dem Azure Marketplace bereitgestellt werden kann.

Führen Sie die Schritte in den folgenden Beiträgen aus:

* [Bereitstellen einer Instanz von Azure Redis Cache Service][1]
* [Bereitstellen einer skalierbaren WordPress-Website in Azure][0]

Nachdem Sie die skalierbare WordPress-Website und eine Redis-Cache-Instanz bereitgestellt haben, können Sie damit fortfahren, den Memcache-Shim in Azure App Service-Web-Apps zu aktivieren.

## Aktivieren des Web-Apps-Memcache-Shims

Zum Konfigurieren des Memcache-Shims müssen Sie drei Anwendungseinstellungen erstellen. Dies kann mithilfe einer Vielzahl von Methoden, einschließlich der [Azure Portal](http://go.microsoft.com/fwlink/?LinkId=529715), die [alte Portal][3], die [Azure-PowerShell-Cmdlets][5] oder [Azure-Befehlszeilenschnittstelle][5]. Für die Zwecke dieses Beitrags, ich verwende die [Azure Portal][4] app-Einstellungen festlegen. Können die folgenden Werte abgerufen werden **Einstellungen** Blade Ihrer Redis-Cache-Instanz.

![Azure Redis Cache-Einstellungen-Blade](./media/web-sites-connect-to-redis-using-memcache-protocol/1-azure-redis-cache-settings.png)

### Hinzufügen der Anwendungseinstellung REDIS_HOST

Ist die erste Anwendungseinstellung erstellen Sie müssen die **REDIS_HOST** Anwendungseinstellung. Diese Einstellung legt das Ziel fest, an das der Shim Cacheinformationen weiterleitet. Der erforderliche Wert für den REDIS_HOST Anwendungseinstellung aus abgerufen werden kann die **Eigenschaften** Blade Ihrer Redis-Cache-Instanz.

![Azure Redis Cache-Hostname](./media/web-sites-connect-to-redis-using-memcache-protocol/2-azure-redis-cache-hostname.png)

Legen Sie den Schlüssel der app-Einstellung auf **REDIS_HOST** und den Wert der Einstellung für die app auf die **Hostnamen** der Redis-Cache-Instanz.

![Web App AppSetting REDIS_HOST](./media/web-sites-connect-to-redis-using-memcache-protocol/3-azure-website-appsettings-redis-host.png)

### Hinzufügen der Anwendungseinstellung REDIS_KEY

Ist die zweite Anwendungseinstellung, die Sie erstellen müssen die **REDIS_KEY** Anwendungseinstellung. Diese Einstellung stellt das Authentifizierungstoken für den sicheren Zugriff auf die Redis-Cache-Instanz bereit. Der erforderliche Wert für den REDIS_KEY Anwendungseinstellung aus abgerufen werden kann die **Zugriffstasten** Blade Redis Cache-Instanz.

![Azure Redis Cache-Primärschlüssel](./media/web-sites-connect-to-redis-using-memcache-protocol/4-azure-redis-cache-primarykey.png)

Legen Sie den Schlüssel der app-Einstellung auf **REDIS_KEY** und den Wert der Einstellung für die app auf die **Primärschlüssel** der Redis-Cache-Instanz.

![Azure-Website AppSetting REDIS_KEY](./media/web-sites-connect-to-redis-using-memcache-protocol/5-azure-website-appsettings-redis-primarykey.png)

### Hinzufügen der Anwendungseinstellung MEMCACHESHIM_REDIS_ENABLE

Die letzte Anwendungseinstellung wird verwendet, um den Memcache-Shim in Web-Apps zu aktivieren. Dieser verwendet REDIS_HOST und REDIS_KEY, um eine Verbindung mit dem Azure-Redis-Cache herzustellen und die Cacheaufrufe weiterzuleiten. Legen Sie den Schlüssel der app-Einstellung auf **MEMCACHESHIM_REDIS_ENABLE** und den Wert **true**.

![Web App AppSetting MEMCACHESHIM_REDIS_ENABLE](./media/web-sites-connect-to-redis-using-memcache-protocol/6-azure-website-appsettings-enable-shim.png)

Sobald Sie fertig sind die drei (3) Appeinstellungen hinzufügen, klicken Sie auf **Speichern**.

## Aktivieren der Memcache-Erweiterung für PHP

Damit die Anwendung über das Memcache-Protokoll kommunizieren kann, muss die Memcache-Erweiterung für PHP (Sprachen-Framework für Ihre WordPress-Website) installiert werden.

### Herunterladen der php_memcache-Erweiterung

Navigieren Sie zu [PECL][6], klicken Sie unter der Kategorie Zwischenspeichern auf [Memcache][7]. Klicken Sie in der Spalte mit den Downloads auf den DLL-Link.

![PHP-PECL-Website](./media/web-sites-connect-to-redis-using-memcache-protocol/7-php-pecl-website.png)

Laden Sie das Ziel des x86-Links zum Non-Thread Safe (NTS) für Ihre in Web-Apps aktivierte Version von PHP herunter. (Die Standardeinstellung ist PHP 5.4)

![PHP-PECL-Website Memcache-Paket](./media/web-sites-connect-to-redis-using-memcache-protocol/8-php-pecl-memcache-package.png)

### Aktivieren der php_memcache-Erweiterung

Klicken Sie nach dem Download der Datei extrahieren und Hochladen der **php_memcache.dll** in den **d:\\home\\site\\wwwroot\\bin\\ext** Verzeichnis. Nachdem die "php_memcache.dll" in die Web-App hochgeladen wurde, muss für die Erweiterung für die PHP-Laufzeit aktiviert werden. Öffnen Sie zum Aktivieren der Memcache-Erweiterungs in der Azure-Verwaltungsportal die **Anwendungseinstellungen** Blade für die Webanwendung wird dann eine neue Anwendungseinstellung hinzufügen, mit dem Schlüssel des **PHP_EXTENSIONS** und den Wert **bin\\ext\\php_memcache.dll**.


> Wenn die Web-App mehrere PHP-Erweiterungen laden muss, sollte der Wert von PHP_EXTENSIONS eine durch Kommas getrennte Liste von relativen Pfaden zu DLL-Dateien sein.

![Web App AppSetting PHP_EXTENSIONS](./media/web-sites-connect-to-redis-using-memcache-protocol/9-azure-website-appsettings-php-extensions.png)

Sobald Sie fertig sind, klicken Sie auf **Speichern**.

## Installieren des Memcache-WordPress-Plug-Ins

Klicken Sie auf der Seite WordPress-Plug-Ins auf dem **Hinzufügen** Schaltfläche.

![WordPress-Plug-in-Seite](./media/web-sites-connect-to-redis-using-memcache-protocol/10-wordpress-plugin.png)

Geben Sie in das Suchfeld **Memcached** und drücken Sie die **EINGABETASTE** Schlüssel.

![WordPress Hinzufügen neuer-Plug-in](./media/web-sites-connect-to-redis-using-memcache-protocol/11-wordpress-add-new-plugin.png)

Suchen **Memcached-Objektcache** in der Liste, klicken Sie dann auf die **Jetzt installieren** Schaltfläche.

![WordPress Memcache-Plug-in zu installieren](./media/web-sites-connect-to-redis-using-memcache-protocol/12-wordpress-install-memcache-plugin.png)

### Aktivieren des Memcache WordPress-Plug-Ins

>[AZURE.NOTE]Befolgen Sie die Anweisungen in diesem Blog auf [zum Aktivieren einer Website-Erweiterung in Webanwendungen][8] Visual Studio Online installieren.

In der `wp-config.php` -Datei, fügen Sie den folgenden Codeausschnitt über dem Symbol Kommentar am Ende der Datei bearbeiten.

```php
$memcached_servers = array(
	'default' => array('localhost:' . getenv("MEMCACHESHIM_PORT"))
);
```

Wenn dieser Codeausschnitt eingefügt wurde, speichert Monaco das Dokument automatisch.

Im nächsten Schritt wird das Object-Cache-Plug-In aktiviert. Dies erfolgt durch Ziehen und Ablegen von **Objekt cache.php** von **wp-Inhalt/Memcached** Ordner die **wp-Content** Ordner, um die Memcache-Objektcache-Funktion aktivieren.

![Suchen Sie das Memcache-Objekt cache.php-Plug-in](./media/web-sites-connect-to-redis-using-memcache-protocol/13-locate-memcache-object-cache-plugin.png)

Nun die **Objekt cache.php** Datei befindet sich in der **wp-Content** Ordner, der Cache des Memcached ist nun aktiviert.

![Aktivieren Sie das Memcache-Objekt cache.php-Plug-in](./media/web-sites-connect-to-redis-using-memcache-protocol/14-enable-memcache-object-cache-plugin.png)

## Überprüfen der Funktion des Memcached Object Cache-Plug-Ins

Alle Schritte zum Aktivieren des Web-Apps-Memcache-Shims sind damit abgeschlossen. Der letzte Schritt besteht darin, sicherzustellen, dass Ihre Redis-Cache-Instanz mit Daten aufgefüllt wird.

### Aktivieren der Nicht-SSL-Port-Unterstützung in Azure-Redis-Cache

>[AZURE.NOTE]Zum Zeitpunkt der Erstellung dieses Dokuments unterstützt die Redis-CLI keine SSL-Verbindungen, daher sind die folgenden Schritte erforderlich.

Navigieren Sie im Azure-Portal zur Redis-Cache-Instanz, die Sie für diese Web-App erstellt haben. Sobald der Cache Blade geöffnet ist, klicken Sie auf die **Einstellungen** Symbol.

![Azure Redis Cache-Schaltfläche "Einstellungen"](./media/web-sites-connect-to-redis-using-memcache-protocol/15-azure-redis-cache-settings-button.png)

Wählen Sie **Zugriffsports** aus der Liste.

![Azure Redis Cache-Access-Port](./media/web-sites-connect-to-redis-using-memcache-protocol/16-azure-redis-cache-access-port.png)

Klicken Sie auf **Nr.** für **ermöglichen den Zugriff nur über SSL**.

![Nur Azure Redis Cache-Port für den SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/17-azure-redis-cache-access-port-ssl-only.png)

Es wird angezeigt, dass der Nicht-SSL-Port nun geöffnet ist. Klicken Sie auf **Speichern**.

![Azure Redis Cache Redis Zugriff Portal nicht-SSL](./media/web-sites-connect-to-redis-using-memcache-protocol/18-azure-redis-cache-access-port-non-ssl.png)

### Herstellen einer Verbindung mit dem Azure-Redis-Cache von Redis-CLI

>[AZURE.NOTE]Dieser Schritt setzt voraus, dass Redis lokal auf dem Entwicklungscomputer installiert ist. [Redis-Installation lokal mithilfe der folgenden Anleitungen][9].

Öffnen Sie die Befehlszeilenkonsole Ihrer Wahl, und geben Sie den folgenden Befehl ein:

```shell
redis-cli –h <hostname-for-redis-cache> –a <primary-key-for-redis-cache> –p 6379
```

Ersetzen Sie die **<hostname-for-redis-cache>** mit den tatsächlichen xxxxx.redis.cache.windows.net Hostnamen und die **<primary-key-for-redis-cache>** durch den Zugriffsschlüssel für den Cache, und drücken Sie dann **EINGABETASTE**. Sobald die CLI eine Verbindung mit der Redis-Cache-Instanz hergestellt hat, übermitteln Sie einen beliebigen Redis-Befehl. Im Screenshot unten liste ich die Schlüssel auf.

![Redis CLI in Terminaldienste eine Verbindung mit der Azure-Redis-Cache](./media/web-sites-connect-to-redis-using-memcache-protocol/19-redis-cli-terminal.png)

Der Aufruf zum Auflisten der Schlüssel sollte einen Wert zurückgeben. Wenn dies nicht der Fall ist, navigieren Sie zur Web-App, und versuchen Sie es erneut.

## Zusammenfassung

Glückwunsch! Die WordPress-App verfügt jetzt über einen zentralen Cache im Arbeitsspeicher zur Unterstützung eines erhöhten Durchsatzes. Beachten Sie, dass der Web-Apps-Memcache-Shim mit jedem Memcache-Client verwendet werden kann, unabhängig von der Programmiersprache oder dem Anwendungsframework. Stellen Sie zum Bereitstellen von Feedback oder Fragen zu den Web-Apps Memcache-Shim in [MSDN-Foren][10] oder [Stackoverflow][11].

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)


[0]: http://bit.ly/1F0m3tw
[1]: http://bit.ly/1t0KxBQ
[2]: http://manage.windowsazure.com
[3]: http://portal.azure.com
[4]: ../powershell-install-configure.md
[5]: /downloads
[6]: http://pecl.php.net
[7]: http://pecl.php.net/package/memcache
[8]: http://blog.syntaxc4.net/post/2015/02/05/how-to-enable-a-site-extension-in-azure-websites.aspx
[9]: http://redis.io/download#installation
[10]: https://social.msdn.microsoft.com/Forums/home?forum=windowsazurewebsitespreview
[11]: http://stackoverflow.com/questions/tagged/azure-web-sites
[12]: /services/cache/
[13]: http://memcached.org
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->