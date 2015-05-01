<properties 
	pageTitle="Leistungsstarkes WordPress in Azure App Service" 
	description="Erfahren Sie, wie Sie eine leistungsstarke WordPress-Website in Azure App Service hosten." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="tomfitz"/>

#Leistungsstarkes WordPress in Azure App Service

Azure App Service bietet eine skalierbare, sichere und benutzerfreundliche Umgebung für große unternehmenswichtige [WordPress][wordpress]-Websites. Microsoft betreibt selbst leistungsstarke Websites wie die [Office][officeblog]- und [Bing][bingblog]-Blogs. In diesem Dokument wird erläutert, wie Sie mithilfe von Azure App Service-Web-Apps eine leistungsfähige, cloudbasierte WordPress-Website einrichten und verwalten können, die mit eine großen Anzahl von Besuchern zurechtkommt.

## Architektur und Planung

Für eine grundlegende WordPress-Installation gelten nur zwei Anforderungen.

* **MySQL-Datenbank** - verfügbar über [ClearDB im Azure Store][cdbnstore]. Sie können auch Ihre eigene MySQL-Installation auf virtuellen Azure-Computern entweder mithilfe von [Windows][mysqlwindows] oder [Linux][mysqllinux] verwalten.

    > [AZURE.NOTE] ClearDB bietet mehrere MySQL-Konfigurationen mit unterschiedlichen Leistungsmerkmalen für jede Konfiguration. Im [Azure Store][cdbnstore] finden Sie Informationen zu den dort verfügbaren Angeboten. Auf der [ClearDB-Website mit den Preisen](http://www.cleardb.com/pricing.view) finden Sie direkt von ClearDB beziehbare Angebote.
    
* **PHP 5.2.4 oder neuer** - Azure App Service unterstützt derzeit die [PHP-Versionen 5.3, 5.4 und 5.5][phpwebsite].

	> [AZURE.NOTE] Wir empfehlen, stets mit der neuesten Version von PHP zu arbeiten, um sicherzustellen, dass Sie über die neuesten Sicherheitskorrekturen verfügen.

###Grundlegende Bereitstellung

Indem Sie die grundlegenden Anforderungen erfüllen, können Sie eine Standardlösung innerhalb einer Azure-Region erstellen.

![Eine Azure-Web-App und MySQL-Datenbank, die in einer einzelnen Azure-Region gehostet werden][basic-diagram]

Wenngleich dies Ihnen ermöglicht, Ihre Anwendung horizontal zu skalieren, indem Sie mehrere Web-App-Instanzen der Website erstellen, wird die gesamte Konfiguration in den Rechenzentren in einer bestimmten geografischen Region gehostet. Besuchern außerhalb dieser Region werden ggf. nur langsame Antwortzeiten auf der Website geboten, und wenn die Rechenzentren in dieser Region ausfallen, fällt auch Ihre Anwendung aus.


###Bereitstellung in mehreren Regionen

Mithilfe von Azure [Traffic Manager][trafficmanager] ist es möglich, Ihre WordPress-Website auf mehrere geografische Regionen zu skalieren und zugleich Besuchern nur eine URL bereitzustellen. Alle Besucher durchlaufen Traffic Manager und werden anschließend basierend auf der Lastenausgleichskonfiguration an eine Region weitergeleitet.

![Eine in mehreren Regionen gehostete Azure-Web-App mit CDBR-Hochverfügbarkeitsrouter zum regionsübergreifenden Weiterleiten an MySQL][multi-region-diagram]

Innerhalb jeder Region kann die WordPress-Website weiterhin über mehrere Web-App-Instanzen skaliert werden. Doch diese Skalierung ist regionsspezifisch. Regionen mit hohem Datenverkehr können anders als Regionen mit niedrigem Datenverkehr skaliert werden.

Die Replikation und das Routing an mehrere MySQL-Datenbanken kann mithilfe des [CDBR-Hochverfügbarkeitsrouters][cleardbscale] (links) oder von [MySQL Cluster CGE][cge] erfolgen. 

###Bereitstellung in mehreren Regionen mit Medienspeicherung und Zwischenspeichern

Wenn die Website Uploads zulässt oder Mediendateien hostet, nutzen Sie den Azure-BLOB-Speicher. Wenn Sie einen Cache benötigen, ziehen Sie [Redis-Cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure Store](http://azure.microsoft.com/gallery/store/) in Betracht.

![Eine in mehreren Regionen gehostete Azure-Web-App mit CDBR-Hochverfügbarkeitsrouter für MySQL, Managed Cache, Blob-Speicher und CDN][performance-diagram]

BLOB-Speicher wird standardmäßig geografisch auf mehrere Regionen verteilt, sodass Sie sich um die Replikation von Dateien auf allen Websites keine Gedanken machen müssen. Sie können auch das Azure [Content Distribution Network (CDN)][cdn] für die Blob-Speicherung aktivieren, das Dateien an Endknoten verteilt, die näher bei Ihren Besuchern sind.

###Planung

####Weitere Anforderungen

Maßnahme | Option ...
------------------------|-----------
**Hochladen oder Speichern großer Dateien** | [WordPress-Plug-In für die Verwendung von Blob-Speicher][storageplugin]
**Senden von E-Mail** | [SendGrid][storesendgrid] und das [WordPress-Plug-In für die Verwendung von SendGrid][sendgridplugin]
**Benutzerdefinierte Domänennamen** | [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service][customdomain]
**HTTPS** | [Aktivieren von HTTPS für eine Web-App in Azure App Service][httpscustomdomain]
**Überprüfung vor der Produktion** | [Einrichten von Stagingumgebungen für Web-Apps in Azure App Service][staging] <p>Beachten Sie, dass beim Wechsel einer Web-App von der Staging- zur Produktionsumgebung auch die WordPress-Konfiguration verschoben wird. Sie müssen sicherstellen, dass alle Einstellungen an die Anforderungen Ihrer Produktions-App angepasst werden, bevor Sie die Staging-App zur Produktions-App machen.</p> 
**Überwachung und Problembehandlung** | [Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service][log] und [Überwachen von Web-Apps in Azure App Service][monitor]
**Bereitstellen Ihrer Website** | [Bereitstellen von Web-Apps in Azure App Service][deploy]

####Verfügbarkeit und Notfallwiederherstellung

Maßnahme | Option ...
------------------------|-----------
**Lastenausgleich für Websites** oder **geografisch verteilte Bereitstellung von Websites** | [Weiterleiten von Datenverkehr mit Azure Traffic Manager][trafficmanager]
**Sichern und Wiederherstellen** | [Sichern von Web-Apps in Azure App Service][backup] und [Wiederherstellen einer Web-App in Azure App Service][restore]

####Leistung

Leistung in der Cloud wird hauptsächlich mithilfe der Zwischenspeicherung und horizontalen Skalierung erreicht. Doch berücksichtigt werden sollten auch der Arbeitsspeicher, die Bandbreite und andere Aspekte des Hostings von Web-Apps.

Maßnahme | Option ...
------------------------|-----------
**Grundlegendes zu den Funktionen von App Service-Instanzen** |  [Preisgestaltung, inklusive der Funktionen von App Service-Modi][websitepricing]
**Zwischenspeichern von Ressourcen** | [Redis-Cache][rediscache], [Memcache Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure Store](/gallery/store/)
**Skalieren Ihrer Anwendung** | [Skalieren einer Web-App in Azure App Service][websitescale] und [ClearDB-Hochverfügbarkeitsrouting][cleardbscale]. Wenn Sie sich für das Hosten und Verwalten Ihrer eigenen MySQL-Installation entscheiden, sollten Sie für das horizontale Hochskalieren [MySQL Cluster CGE][cge] in Erwägung ziehen.

####Migration

Es gibt zwei Möglichkeiten, eine vorhandene WordPress-Website nach Azure App Service zu migrieren.

* **[WordPress-Export][export]** - dient zum Exportieren des Inhalts Ihres Blogs, der anschließend in eine neue WordPress-Website in Azure App Service mithilfe des Plug-Ins [WordPress Importer][import] importiert werden kann.

	> [AZURE.NOTE] Dieser Prozess ermöglicht lediglich das Migrieren Ihrer Inhalte und nicht von Plug-Ins, Designs oder anderer Anpassungen. Diese müssen manuell erneut installiert werden.

* **Manuelle Migration** - [sichern Sie Ihre Website][wordpressbackup] und [Datenbank][wordpressdbbackup], und stellen Sie sie anschließend manuell in einer Azure App Service-Web-App und der dazugehörigen MySQL-Datenbank wieder her, um stark angepasste Websites zu migrieren und den Aufwand der manuellen Installation von Plug-Ins, Designs und anderen Anpassungen zu vermeiden.

## Schrittweise Anweisungen

### Erstellen einer neuen WordPress-Website

1. Nutzen Sie den [Azure Store][cdbnstore] für das Erstellen einer MySQL-Datenbank der Größe, die Sie im Abschnitt [Architektur und Planung](#planning) bestimmt haben, in den Regionen, in denen Sie Ihre Website hosten.

2. Befolgen Sie die Anweisungen in [Erstellen einer WordPress-Web-App in Azure App Service][createwordpress] zum Erstellen einer neuen WordPress-Web-App. Wählen Sie beim Erstellen der Web-App **Vorhandene MySQL-Datenbank verwenden** und anschließend die in Schritt 1 erstellte Datenbank aus.

Wenn Sie eine vorhandene WordPress-Website migrieren, lesen Sie [Migrieren einer vorhandenen WordPress-Website](#Migrate-an-existing-WordPress-site-to-Azure) nach dem Erstellen einer neuen Web-App.

### Migrieren einer vorhandenen WordPress-Website in Azure

Wie im Abschnitt [Architektur und Planung](#planning) beschrieben, gibt es zwei Möglichkeiten für das Migrieren einer WordPress-Website.

* **Export und Import** - Für Websites ohne zahlreiche Anpassungen, deren Inhalt Sie lediglich verschieben möchten.

* **Sicherung und Wiederherstellung** - Für Websites mit zahlreichen Anpassungen, bei denen Sie alles verschieben möchten.

Befolgen Sie die Anweisungen zum Migrieren Ihrer Website in den folgenden Abschnitten.

####Die Export- und Importmethode

1. Verwenden Sie [WordPress-Export][export] zum Exportieren Ihrer vorhandenen Website.

2. Erstellen Sie eine neue Web-App anhand der Schritte im Abschnitt [Erstellen einer neuen WordPress-Website](#Create-a-new-WordPress-site) wechseln.

3. Melden Sie sich in der Azure-Web-App an Ihrer WordPress-Website an, und klicken Sie auf **Plug-Ins** -> **Neu hinzufügen**. Suchen Sie das Plug-In **WordPress Importer**, und installieren Sie es.

4. Klicken Sie nach der Installation des Importer-Plug-Ins auf **Extras** -> **Importieren**, und wählen Sie dann **WordPress** aus, um das WordPress-Importer-Plug-In zu verwenden.

5. Klicken Sie auf der Seite **WordPress importieren** auf **Datei wählen**. Wechseln Sie zu aus Ihrer vorhandenen Wordpress-Website exportierten WXR-Datei, und klicken Sie auf **Datei hochladen und importieren**.

6. Klicken Sie auf **Submit**. Sie werden informiert, wenn der Import erfolgreich war.

8. Starten Sie, nachdem Sie alle Schritte abgeschlossen haben, Ihre Website auf dem Blatt der Web-App im [Azure-Portal][mgmtportal] neu.

Nach dem Importieren der Website müssen Sie ggf. die folgenden Schritte ausführen, um nicht in der Importdatei enthaltene Einstellungen zu aktivieren.

Bei Verwendung von | Tun Sie Folgendes ...
------------------ | ----------
**Permalinks** | Im WordPress-Dashboard der neuen Website klicken Sie auf **Einstellungen** -> **Permalinks** und aktualisieren anschließend die Permalinks-Struktur
**Bild-/Medienlinks** | Zum Anpassen von Links an den neuen Speicherort verwenden Sie das [Plug-In "Velvet Blues Update URLs"][velvet], ein Suchen-und-Ersetzen-Tool, oder führen diese Schritte manuell in der Datenbank aus.
**Designs** | Wechseln Sie zu **Darstellung** -> **Design**, und aktualisieren Sie das Websitedesign den Anforderungen entsprechend.
**Menüs** | Falls Ihr Design Menüs unterstützt, ist in Links zu Ihrer Homepage ggf. das alte Unterverzeichnis eingebettet. Wechseln Sie zu **Darstellung** -> **Menüs**, und aktualisieren Sie sie

####Die Sicherungs- und Wiederherstellungsmethode

1. Sichern Sie Ihre vorhandene WordPress-Website mithilfe der Informationen unter [WordPress-Sicherungen][wordpressbackup].

2. Sichern Sie Ihre vorhandene Datenbank anhand der Informationen unter [Sichern Ihrer Datenbank][wordpressdbbackup].

3. Erstellen Sie eine neue Datenbank, und stellen Sie die Sicherung wieder her.

	1. Erwerben Sie im [Azure Store][cdbnstore] eine neue Datenbank, oder richten Sie auf einem virtuellen [Windows][mysqlwindows]- oder [Linux][mysqllinux]-Computer eine MySQL-Datenbank ein.

	2. Stellen Sie mit einem MySQL-Client wie [MySQL Workbench][workbench] eine Verbindung mit der neuen Datenbank her, und importieren Sie Ihre WordPress-Datenbank.

	3. Ändern Sie in der Datenbank die Domäneneinträge entsprechend Ihrer neuen Azure App Service-Domäne. Zum Beispiel mywordpress.azurewebsites.net. Verwenden Sie das Skript [Search and Replace for WordPress Databases Script][searchandreplace], um alle Vorkommen sicher zu ändern.

4. Erstellen Sie im Azure-Verwaltungsportal eine neue Web-App, und veröffentlichen Sie die WordPress-Sicherung.

	1. Erstellen Sie im [Azure-Portal][mgmtportal] eine neue Web-App mit einer Datenbank, indem Sie **Neu** -> **Web + Mobile** -> **Azure Marketplace** -> **Web-Apps** -> **Web-App + SQL** (oder **Web-App + MySQL**) -> **Erstellen** auswählen. Konfigurieren Sie alle erforderlichen Einstellungen, um eine leere Web-App zu erstellen.

	2. Suchen Sie in Ihrer WordPress-Sicherung die Datei **wp-config.php**, und öffnen Sie sie in einem Editor. Ersetzen Sie die folgenden Einträge durch die Informationen für Ihre neue MySQL-Datenbank.

		* **DB_NAME** - der Name der Datenbank

		* **DB_USER** - der Benutzername für den Zugriff auf die Datenbank

		* **DB_PASSWORD** - das Kennwort des Benutzers

		Speichern und schließen Sie die Datei **wp-config.php** nach dem Ändern dieser Einträge.

	3. Befolgen Sie die Informationen unter [Bereitstellen von Web-Apps in Azure App Service][deploy], um die gewünschte Bereitstellungsmethode zu aktivieren, und stellen Sie anschließend Ihre WordPress-Sicherung in Ihrer Azure App Service-Web-App bereit.

5. Nach der Bereitstellung der WordPress-Website sollten Sie auf die neue Website (als App Service-Web-App) über die URL "*.azurewebsite.net" zugreifen können.

###Konfigurieren Ihrer Website

Befolgen Sie nach dem Erstellen oder Migrieren der WordPress-Website die folgenden Informationen zum Verbessern der Leistung und Ermöglichen zusätzlicher Funktionalität.

Maßnahme | Option ...
------------- | -----------
**Festlegen von App Service-Planmodus und -größe und Aktivieren der Skalierung** | [Skalieren einer Web-App in Azure App Service][websitescale]
**Aktivieren dauerhafter Datenbankverbindungen** <p>WordPress arbeitet standardmäßig nicht mit dauerhaften Datenbankverbindungen, was bewirken kann, dass Ihre Verbindung mit der Datenbank nach mehreren Verbindungen gedrosselt wird.</p>  | <ol><li><p>Bearbeiten Sie die Datei <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Suchen Sie die folgende Zeile.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Ersetzen Sie die vorherige Zeile durch den folgenden Code.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Suchen Sie die folgende Zeile.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Ersetzen Sie die obige Zeile durch den folgenden Code.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Speichern Sie die Datei <strong>wp-includes/wp-db.php</strong>, und stellen Sie die Website erneut bereit.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>Diese Änderungen werden ggf. überschrieben, wenn WordPress aktualisiert wird.</p><p>WordPress nutzt standardmäßig automatische Updates, was durch Bearbeiten der Datei <strong>wp-config.php</strong> und Hinzufügen von "<code>define ( 'WP_AUTO_UPDATE_CORE', false );</code>" deaktiviert werden kann.</p><p>Eine weitere Möglichkeit des Umgangs mit Updates ist das Verwenden eines Webauftrags, der die Datei <strong>wp-db.php</strong> überwacht und die oben genannten Änderungen bei jeder Aktualisierung der Datei durchführt. Unter <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Einführung in Webaufträge</a> finden Sie weitere Informationen.</p></div>
**Verbessern der Leistung** | <ul><li><p>Das <a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deaktivieren des ARR-Cookies</a> kann die Leistung verbessern, wenn WordPress auf mehreren Web-App-Instanzen ausgeführt wird.</p></li><li><p>Aktivieren Sie das Zwischenspeichern. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis-Cache</a> (Vorschau) lässt sich mit dem <a href="https://wordpress.org/plugins/redis-object-cache/">Redis Object Cache WordPress-Plug-In</a> einsetzen, oder Sie verwenden eines der anderen Cache-Angebot im <a href="/gallery/store/">Azure Store</a>.</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Beschleunigen von WordPress mit Wincache</a> - Wincache ist für Web-Apps standardmäßig aktiviert.</p></li><li><p><a href="../web-sites-scale/">Skalieren Sie die Web-App in Azure App Service</a> und nutzen Sie das <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB-Hochverfügbarkeitsrouting</a> oder <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>.</p></li></ul>
**Verwenden der BLOB-Speicherung** | <ol><li><p><a href="../storage-create-storage-account/">Erstellen eines Azure-Speicherkontos</a></p></li><li><p>Unter <a href="../cdn-how-to-use/">Verwenden von CDN für Azure</a> erfahren Sie, wie Sie in Blobs gespeicherte Daten geografisch verteilen.</p></li><li><p>Installieren und konfigurieren Sie das Plug-In <a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage for WordPress</a>.</p><p>Detaillierte Informationen zu Einrichtung und Konfiguration des Plug-Ins finden Sie im <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Benutzerhandbuch</a>.</p> </li></ol>
**Aktivieren von E-Mail** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Aktivieren von SendGrid über den Azure Store</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installieren des SendGrid-Plug-Ins für WordPress</a></p></li></ol>
**Konfigurieren eines benutzerdefinierten Domänennamens** | [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service][customdomain]
**Aktivieren von HTTPS für einen benutzerdefinierten Domänennamen** | [Aktivieren von HTTPS für eine Web-App in Azure App Service][httpscustomdomain]
**Lastenausgleich oder geografisch verteilte Bereitstellung Ihrer Website** | [Weiterleiten von Datenverkehr mit Azure Traffic Manager][trafficmanager]. Wenn Sie eine benutzerdefinierte Domäne verwenden, finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service][customdomain] Informationen zum Verwenden von Traffic Manager mit benutzerdefinierten Domänennamen.
**Aktivieren automatischer Sicherungen** | [Sichern von Web-Apps in Azure App Service][backup]
**Aktivieren der Diagnoseprotokollierung** | [Aktivieren der Diagnoseprotokollierung für Web-Apps in Azure App Service][log]

## Nächste Schritte

* [WordPress optimization](http://codex.wordpress.org/WordPress_Optimization) (in englischer Sprache)

* [Konvertieren von WordPress in Multisite in Azure App Service](web-sites-php-convert-wordpress-multisite.md)

* [ClearDB-Upgrade-Assistent für Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hosting WordPress in a subfolder of your web app in Azure App Service](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx) (in englischer Sprache)

* [Schrittweise Anleitung: Create a WordPress site using Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx) (in englischer Sprache)

* [Host your existing WordPress blog on Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx) (in englischer Sprache)

* [Enabling pretty permalinks in WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress) (in englischer Sprache)

* [How to migrate and run your WordPress blog on Azure App Service](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/) (in englischer Sprache)

* [How to run WordPress on Azure App Service for free](http://architects.dzone.com/articles/how-run-wordpress-azure) (in englischer Sprache)

* [WordPress on Azure in 2 minutes or less](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/) (in englischer Sprache)

* [Verschieben eines WordPress-Blogs in Azure - Teil 1: Erstellen eines WordPress-Blogs in Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1) (in englischer Sprache)

* [Verschieben eines WordPress-Blogs in Azure - Teil 2: Übertragen Ihrer Inhalte](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content) (in englischer Sprache)

* [Verschieben eines WordPress-Blogs in Azure - Teil 3: Einrichten Ihrer benutzerdefinierten Domäne](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain) (in englischer Sprache)

* [Verschieben eines WordPress-Blogs in Azure - Teil 4: Permalinks und Regeln zum Umschreiben von URLs](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules) (in englischer Sprache)

* [Verschieben eines WordPress-Blogs in Azure - Teil 5: Verschieben aus einem Unter- in den Stammordner](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root) (in englischer Sprache)

* [How to set up a WordPress web app in your Azure account](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/) (in englischer Sprache)

* [Propping up WordPress on Azure](http://www.johnpapa.net/wordpress-on-azure/) (in englischer Sprache)

* [Tips for WordPress on Azure](http://www.johnpapa.net/azurecleardbmysql/) (in englischer Sprache)

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: web-sites-php-configure.md
[customdomain]: web-sites-custom-domain-name.md
[trafficmanager]: /blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: web-sites-backup.md
[restore]: web-sites-restore.md
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: web-sites-scale.md
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: web-sites-staged-publishing.md
[monitor]: web-sites-monitor.md
[log]: web-sites-enable-diagnostic-log.md
[httpscustomdomain]: web-sites-configure-ssl-certificate.md
[mysqlwindows]: virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: virtual-machines-linux-mysql-use-opensuse.md
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: /pricing/details/app-service/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: web-sites-php-web-site-gallery.md
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://portal.azure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: web-sites-deploy.md
[posh]: install-configure-powershell.md
[xplat-cli]: xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: cdn-how-to-use.md


<!--HONumber=52-->