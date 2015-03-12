<properties 
	pageTitle="Leistungsstarke Umgebung für WordPress auf Azure-Websites" 
	description="Erfahren Sie, wie Sie eine leistungsstarke Umgebung für WordPress-Websites auf Azure-Websites hosten" 
	services="web-sites" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="php" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="11/11/2014" 
	ms.author="tomfitz"/>

#Leistungsstarke Umgebung für WordPress auf Azure-Websites

Azure-Websites bieten eine skalierbare, sichere und benutzerfreundliche Umgebung für große unternehmenswichtige [WordPress][wordpress]-Websites. Microsoft selbst betreibt leistungsstarke Websites wie den [Office][officeblog]- und [Bing][bingblog]-Blog. In diesem Dokument wird erläutert, wie Sie mithilfe von Azure-Websites eine hochleistungsfähige, cloudbasierte WordPress-Website einrichten und verwalten können, die mit eine großen Anzahl von Besuchern zurechtkommt.

##Themen in diesem Artikel 

* [Architektur und Planung](#planning) - Machen Sie sich mit der Architektur, Anforderungen und Leistungsaspekten vertraut, ehe Sie Ihre Website erstellen

* [Vorgehensweise](#howto) - Erstellen, Bereitstellen und Konfigurieren Ihrer Website

* [Weitere Ressourcen](#resources) - Weitere Ressourcen und Informationen

##<a id="plan"></a>Architektur und Planung

Für eine grundlegende WordPress-Installation gelten nur zwei Anforderungen.

* **MySQL-Datenbank** - Verfügbar über [ClearDB im Azure Store][cdbnstore]. Sie können auch Ihre eigene MySQL-Installation auf virtuellen Azure-Computern entweder mithilfe von [Windows][mysqlwindows] oder [Linux][mysqllinux] verwalten.

    > [AZURE.NOTE] ClearDB bietet mehrere MySQL-Konfigurationen mit unterschiedlichen Leistungsmerkmalen für jede Konfiguration. Im [Azure Store][cdbnstore] finden Sie Informationen zu den dort verfügbaren Angeboten. Auf der [ClearDB-Webseite mit den Preisen](http://www.cleardb.com/pricing.view) finden Sie direkt von ClearDB beziehbare Angebote.
    
* **PHP 5.2.4 oder neuer** - Azure Websites unterstützten derzeit die [PHP-Versionen 5.3, 5.4 und 5.5][phpwebsite].

	> [AZURE.NOTE] Wir empfehlen, stets mit der neuesten Version von PHP zu arbeiten, um sicherzustellen, dass Sie über die neuesten Sicherheitskorrekturen verfügen.

###Grundlegende Bereitstellung

Indem Sie die grundlegenden Anforderungen erfüllen, können Sie eine Standardlösung innerhalb einer Azure-Region erstellen.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Wenngleich dies Ihnen ermöglicht, Ihre Anwendung horizontal zu skalieren, indem Sie mehrere Instanzen der Website erstellen, wird die gesamte Konfiguration in den Rechenzentren in einer bestimmten geografischen Region gehostet. Besuchern außerhalb dieser Region werden ggf. nur langsame Antwortzeiten auf der Website geboten, und wenn die Rechenzentren in dieser Region ausfallen, fällt auch Ihre Anwendung aus.


###Bereitstellung in mehreren Regionen

Mithilfe von [Azure Traffic Manager][trafficmanager] ist es möglich, Ihre WordPress-Website auf mehrere geografische Regionen zu skalieren und zugleich Besuchern nur eine URL bereitzustellen. Alle Besucher durchlaufen Traffic Manager und werden anschließend basierend auf der Lastenausgleichskonfiguration an eine Region weitergeleitet.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

Innerhalb jeder Region kann die WordPress-Website weiterhin über mehrere Website-Instanzen skaliert werden. Doch diese Skalierung ist regionsspezifisch. Regionen mit hohem Datenverkehr können anders als Regionen mit niedrigem Datenverkehr skaliert werden.

Die Replikation und das Routing an mehrere MySQL-Datenbanken kann mithilfe des [CDBR-Hochverfügbarkeitsrouters][cleardbscale] (links) oder von [MySQL Cluster CGE][cge] erfolgen. 

###Bereitstellung in mehreren Regionen mit Medienspeicherung und Zwischenspeichern

Wenn die Website Uploads zulässt oder Mediendateien hostet, nutzen Sie den Azure-BLOB-Speicher. Wenn Sie einen Cache benötigen, ziehen Sie [Redis Cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure Store](http://azure.microsoft.com/gallery/store/) in Betracht.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

BLOB-Speicher wird standardmäßig geografisch auf mehrere Regionen verteilt, sodass Sie sich um die Replikation von Dateien auf allen Websites keine Gedanken machen müssen. Sie können auch das Azure [Content Distribution Network (CDN)][cdn] für die BLOB-Speicherung aktivieren, das Dateien an Endknoten verteilt, die näher bei Ihren Besuchern sind.

###Planung

####Weitere Anforderungen

Maßnahme | Option ...
------------------------|-----------
**Große Dateien hochladen oder speichern** | [WordPress-Plug-In für das Verwenden von Blob-Speicher][storageplugin]
**E-Mail senden** | [SendGrid][storesendgrid] und das [WordPress-Plug-In für SendGrid][sendgridplugin]
**Benutzerdefinierte Domänennamen** | [Benutzerdefinierte Domänennamen mit Azure Websites][customdomain]
**HTTPS** | [HTTPS-Unterstützung für Azure-Websites][httpscustomdomain]
**Validierung vor der Einführung in die Produktion** | [Unterstützung für stufenweise Veröffentlichung in Azure Websites][staging] <p>Beachten Sie, dass beim Wechsel einer Website von der Staging- zur Produktionsumgebung auch die WordPress-Konfiguration verschoben wird. Sie müssen sicherstellen, dass alle Einstellungen an die Anforderungen Ihrer Produktionswebsite angepasst werden, bevor Sie die Stagingwebsite zur Produktionswebsite machen.</p> 
**Überwachung und Problembehandlung** | [Diagnoseprotokollierung mit Azure-Websites][log] und [Überwachung von Azure-Websites][monitor]
**Bereitstellen der Website** | [Bereitstellen einer Azure-Website][deploy]

####Verfügbarkeit und Notfallwiederherstellung

Maßnahme | Option ...
------------------------|-----------
**Lastenausgleich für Websites** oder **geografisch verteilte Bereitstellung von Websites** | [Weiterleiten von Datenverkehr mit Azure Traffic Manager][trafficmanager]
**Sicherung und Wiederherstellung** | [Sicherung von Azure-Websites][backup] und [Sicherung von Azure-Websites und Wiederherstellen einer Azure-Website][restore]

####Leistung

Leistung in der Cloud wird hauptsächlich mithilfe der Zwischenspeicherung und horizontalen Skalierung erreicht. Doch berücksichtigt werden sollten auch der Arbeitsspeicher, die Bandbreite und andere Aspekte des Hostings von Websites.

Maßnahme | Option ...
------------------------|-----------
**Verständnis der Websiteinstanzfunktionen** |  [Preisdetails, einschließlich der Funktionen der Websitegrößen und Modi][websitepricing]
**Cacheressourcen** | [Redis Cache][rediscache], [Memcache Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure Store](http://azure.microsoft.com/gallery/store/)
**Skalieren Ihrer Anwendung** | [Skalieren einer Azure-Website][websitescale] und [ClearDB-Hochverfügbarkeitsrouting][cleardbscale]. Wenn Sie sich für das Hosten und Verwalten Ihrer eigenen MySQL-Installation entscheiden, sollten Sie für das horizontale Skalieren [MySQL Cluster CGE][cge] in Erwägung ziehen.

####Migration

Es gibt zwei Möglichkeiten, eine vorhandene WordPress-Website zu Azure-Websites zu migrieren.

* **[WordPress-Export][export]** - Dient zum Exportieren des Inhalts Ihres Blogs, der anschließend in eine neue WordPress-Website in Azure mithilfe des Plug-Ins [WordPress Importer][import] importiert werden kann.

	> [AZURE.NOTE] Dieser Prozess ermöglicht lediglich das Migrieren Ihrer Inhalte und nicht von Plug-Ins, Designs oder anderer Anpassungen. Diese müssen manuell erneut installiert werden.

* **Manual migration** - [Sichern Sie Ihre Website][wordpressbackup] und [Datenbank][wordpressdbbackup], und stellen Sie sie anschließend manuell in einer Azure-Website und dazugehörigen MySQL-Datenbank wieder her, um stark angepasste Websites zu migrieren und den Aufwand der manuellen Installation von Plug-Ins, Designs und anderen Anpassungen zu vermeiden.

##Vorgehensweise

###<a id="create"></a>Erstellen einer neuen WordPress-Website

1. Nutzen Sie den [Azure Store][cdbnstore] für das Erstellen einer MySQL-Datenbank der Größe, die Sie im Abschnitt [Architektur und Planung](#planning) bestimmt haben, in den Regionen, in denen Sie Ihre Website hosten.

2. Befolgen Sie die Schritte in [Erstellen einer WordPress-Website über die Galerie in Azure][createwordpress] zum Erstellen einer neuen WordPress-Website. Wählen Sie beim Erstellen der Website **Vorhandene MySQL-Datenbank verwenden** und anschließend die in Schritt 1 erstellte Datenbank aus.

Wenn Sie eine vorhandene WordPress-Website migrieren, lesen Sie [Migrieren einer vorhandenen WordPress-Website](#migrate) nach dem Erstellen einer neuen Website.

###<a id="migrate"></a>Migrieren einer vorhandenen WordPress-Website in Azure

Wie im Abschnitt [Architektur und Planung](#planning) erwähnt, gibt es zwei Möglichkeiten für das Migrieren einer WordPress-Website.

* **Export und Import** - Für Websites ohne zahlreiche Anpassungen, deren Inhalt Sie lediglich verschieben möchten.

* **Sicherung und Wiederherstellung** - Für Websites mit zahlreichen Anpassungen, bei denen Sie alles verschieben möchten.

Befolgen Sie die Anweisungen zum Migrieren Ihrer Website in den folgenden Abschnitten.

####Die Export- und Importmethode

1. Verwenden Sie die [WordPress-Exportfunktion][export] zum Exportieren Ihrer vorhandenen Website.

2. Erstellen Sie eine neue Website anhand der Schritte im Abschnitt [Erstellen einer neuen WordPress-Website](#create) .

3. Melden Sie sich in Azure-Websites an Ihrer WordPress-Website an, und klicken Sie auf **Plug-Ins** -> **Neu hinzufügen**. Suchen Sie das Plug-In **WordPress Importer**, und installieren Sie es.

4. Klicken Sie nach der Installation des Importer-Plug-Ins auf **Extras** -> **Importieren**, und wählen Sie dann **WordPress** aus, um das WordPress-Importer-Plug-In zu verwenden.

5. Klicken Sie auf der Seite **WordPress importieren** auf **Datei wählen**. Wechseln Sie zu aus Ihrer vorhandenen Wordpress-Website exportierten WXR-Datei, und klicken Sie auf **Datei hochladen und importieren**.

6. Klicken Sie auf **Senden**. Sie werden informiert, wenn der Import erfolgreich war.

8. Nachdem Sie alle diese Schritte ausgeführt haben, starten Sie im [Azure-Verwaltungsportal][mgmtportal] im Dashboard Ihrer Website die Website neu.

Nach dem Importieren der Website müssen Sie ggf. die folgenden Schritte ausführen, um nicht in der Importdatei enthaltene Einstellungen zu aktivieren.

Bei Verwendung von | Tun Sie Folgendes ...
------------------ | ----------
**Permalinks** | Im WordPress-Dashboard der neuen Website klicken Sie auf **Einstellungen** -> **Permalinks** und aktualisieren anschließend die Permalinks-Struktur
**Bild-/Medienlinks** | Zum Anpassen von Links an den neuen Speicherort verwenden Sie das Plug-In [Velvet Blues Update URLs][velvet], ein Suchen-und-Ersetzen-Tool oder führen diese Schritte manuell in der Datenbank aus
**Designs** | Wechseln Sie zu **Darstellung** -> **Design**, und aktualisieren Sie das Websitedesign den Anforderungen entsprechend
**Menüs** | Falls Ihr Design Menüs unterstützt, ist in Links zu Ihrer Homepage ggf. das alte Unterverzeichnis eingebettet. Wechseln Sie zu **Darstellung** -> **Menüs**, und aktualisieren Sie sie

####Die Sicherungs- und Wiederherstellungsmethode

1. Sichern Sie Ihre vorhandene WordPress-Website mithilfe der Informationen auf der [WordPress-Seite zu Sicherungen][wordpressbackup].

2. Sichern Sie Ihre vorhandene Datenbank anhand der Informationen unter [Sichern Ihrer Datenbank][wordpressdbbackup].

3. Erstellen Sie eine neue Datenbank, und stellen Sie die Sicherung wieder her.

	1. Erwerben Sie im [Azure Store][cdbnstore] eine neue Datenbank, oder richten Sie auf einem virtuellen [Windows][mysqlwindows]- oder [Linux][mysqllinux]-Computer eine MySQL-Datenbank ein.

	2. Stellen Sie mit einem MySQL-Client wie [MySQL Workbench][workbench] eine Verbindung mit der neuen Datenbank her, und importieren Sie Ihre WordPress-Datenbank.

	3. Ändern Sie in der Datenbank die Domäneneinträge entsprechend Ihrer neuen Azure-Websitedomäne. Zum Beispiel mywordpress.azurewebsites.net. Verwenden Sie das Skript [Search and Replace for WordPress Databases Script][searchandreplace], um alle Vorkommen sicher zu ändern.

4. Erstellen Sie eine neue Website, und veröffentlichen Sie die WordPress-Sicherung.

	1. Erstellen Sie eine neue Website im [Azure-Verwaltungsportal][mgmtportal] mit einer Datenbank mithilfe von **Neu** -> **Website** -> **Benutzerdefiniert erstellen**. Hierdurch wird eine leere Website erstellt.

	2. Suchen Sie in Ihrer WordPress-Sicherung die Datei **wp-config.php**, und öffnen Sie sie in einem Editor. Ersetzen Sie die folgenden Einträge durch die Informationen für Ihre neue MySQL-Datenbank.

		* **DB_NAME** - the user name of the database

		* **DB_USER** - the user name used to access the database

		* **DB_PASSWORD** - the user password

		After changing these entries, save and close the **wp-config.php** file.

	3. Befolgen Sie die Informationen unter [Bereitstellen einer Azure-Website][deploy], um die gewünschte Bereitstellungsmethode zu aktivieren, und stellen Sie anschließend Ihre WordPress-Sicherung in Ihrer Azure-Website bereit.

5. Nach der Bereitstellung der WordPress-Website sollten Sie auf die neue Website über die URL "*.azurewebsite.net" zugreifen können.

###Konfigurieren Ihrer Website

Befolgen Sie nach dem Erstellen oder Migrieren der WordPress-Website die folgenden Informationen zum Verbessern der Leistung und Ermöglichen zusätzlicher Funktionalität.

Maßnahme | Option ...
------------- | -----------
**Websitemodus und Größe festlegen, Skalierung aktivieren** | [Skalieren von Websites][websitescale]
**Aktivieren dauerhafter Datenbankverbindungen** <p>WordPress arbeitet standardmäßig nicht mit dauerhaften Datenbankverbindungen, was bewirken kann, dass Ihre Verbindung mit der Datenbank nach mehreren Verbindungen gedrosselt wird.</p>  | <ol><li><p>Bearbeiten Sie die Datei <strong>wp-includes/wp-db.php</strong>.</p></li><li><p>Suchen Sie die folgende Zeile.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Ersetzen Sie die vorherige Zeile durch den folgenden Code.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Suchen Sie die folgende Zeile.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Ersetzen Sie die obige Zeile durch den folgenden Code.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Speichern Sie die Datei <strong>wp-includes/wp-db.php</strong>, und stellen Sie die Website erneut bereit.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>HINWEIS:</h5><p>Diese Änderungen werden ggf. überschrieben, wenn WordPress aktualisiert wird.</p><p>WordPress nutzt standardmäßig automatische Updates, was deaktiviert werden kann durch Bearbeiten der Datei <strong>wp-config.php</strong> und Hinzufügen von <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p>Eine weitere Möglichkeit des Umgangs mit Updates ist das Verwenden eines Webauftrags, der die Datei <strong>wp-db.php</strong> überwacht und die obigen Änderungen bei jeder Aktualisierung der Datei durchführt. Unter <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Einführung in Webaufträge</a> finden Sie weitere Informationen.</p></div>
**Verbessern der Leistung** | <ul><li><p>Das <a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deaktivieren des ARR-Cookies</a> kann die Leistung verbessern, wenn WordPress auf mehreren Website-Instanzen ausgeführt wird.</p></li><li><p>Aktivieren Sie das Zwischenspeichern. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis Cache</a> (Vorschau) lässt sich mit dem <a href="https://wordpress.org/plugins/redis-object-cache/">Redis Object Cache WordPress-Plug-In</a> einsetzen, oder Sie verwenden eines der anderen Cache-Angebot im <a href="http://azure.microsoft.com/gallery/store/">Azure Store</a>.</p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Beschleunigen von WordPress mit Wincache</a> - Wincache ist für Websites standardmäßig aktiviert.</p></li><li><p><a href="http://azure.microsoft.com/documentation/articles/web-sites-scale/">Skalieren Sie Ihre Azure-Website</a>, und verwenden Sie das <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB-Hochverfügbarkeitsrouting</a> oder <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>.</p></li></ul>
**Verwenden der BLOB-Speicherung** | <ol><li><p><a href="http://azure.microsoft.com/documentation/articles/storage-create-storage-account/">Erstellen eines Azure-Speicherkontos</a></p></li><li><p>Unter <a href="http://azure.microsoft.com/documentation/articles/cdn-how-to-use/">Verwenden von CDN für Azure</a> erfahren Sie, wie Sie in BLOBs gespeicherte Daten geografisch verteilen.</p></li><li><p>Installieren und konfigurieren Sie das Plug-In <a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage for WordPress</a>.</p><p>Detaillierte Informationen zu Einrichtung und Konfiguration des Plug-Ins finden Sie im <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Benutzerhandbuch</a>.</p> </li></ol>
**Aktivieren von E-Mail** | <ol><li><p><a href="http://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/">Aktivieren von SendGrid über den Azure Store</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installieren des SendGrid-Plug-Ins für WordPress</a></p></li></ol>
**Konfigurieren eines benutzerdefinierten Domänennamens** | [Verwenden Sie einen benutzerdefinierten Domänennamen mit einer Azure-Website][customdomain]
**Aktivieren von HTTPS für einen benutzerdefinierten Domänennamen** | [Verwenden Sie HTTPS mit einer Azure-Website.][httpscustomdomain]
**Lastenausgleich oder geografisch verteilte Bereitstellung Ihrer Website** | [Leiten Sie Datenverkehr mit Azure Traffic Manager weiter][trafficmanager]. Wenn Sie eine benutzerdefinierte Domäne verwenden, finden Sie unter [Verwenden eines benutzerdefinierten Domänennamens für eine Azure-Website][customdomain] Informationen zum Verwenden von Traffic Manager mit benutzerdefinierten Domänennamen.
**Aktivieren automatisierter Website-Backups** | [Sichern von Azure Websites][backup]
**Aktivieren der Diagnoseprotokollierung** | [Aktivieren Sie die Diagnoseprotokollierungfür Websites][log]

##<a href="resources"></a>Zusätzliche Ressourcen

* [WordPress-Optimierung](http://codex.wordpress.org/WordPress_Optimization)

* [Konvertieren einer WordPress-Website in eine Multisite](http://azure.microsoft.com/documentation/articles/web-sites-php-convert-wordpress-multisite/)

* [ClearDB-Upgrade-Assistent für Azure](http://www.cleardb.com/store/azure/upgrade)

* [Hosten von WordPress in einem Unterordner Ihrer Azure-Website](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Schrittweise Anleitung: Erstellen einer Website mithilfe von Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hosten Ihres vorhandenen WordPress-Blogs in Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Aktivieren von Permalinks in WordPress](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Migrieren und Ausführen Ihres WordPress-Blogs auf Azure-Websites](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [Kostenloses Ausführen von WordPress auf Azure-Websites](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress in Azure in maximal 2 Minuten](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Verschieben eines WordPress-Blogs in Azure - Teil 1: Erstellen eines WordPress-Blogs in Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Verschieben eines WordPress-Blogs in Azure - Teil 2: Übertragen Ihrer Inhalte](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Verschieben eines WordPress-Blogs in Azure - Teil 3: Einrichten Ihrer benutzerdefinierten Domäne](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Verschieben eines WordPress-Blogs in Azure - Teil 4: Permalinks und Regeln zum Umschreiben von URLs](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Verschieben eines WordPress-Blogs in Azure - Teil 5: Verschieben aus einem Unter- in den Stammordner](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Einrichten einer WordPress-Website unter Ihrem Azure-Konto](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Unterstützen von WordPress in Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Tipps für WordPress in Azure](http://www.johnpapa.net/azurecleardbmysql/)

[performance-diagram]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
[wordpress]: http://www.microsoft.com/web/wordpress
[officeblog]: http://blogs.office.com/
[bingblog]: http://blogs.bing.com/
[cdbnstore]: http://www.cleardb.com/store/azure
[storageplugin]: https://wordpress.org/plugins/windows-azure-storage/
[sendgridplugin]: http://wordpress.org/plugins/sendgrid-email-delivery-simplified/
[phpwebsite]: http://azure.microsoft.com/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/de-de/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[import]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: http://azure.microsoft.com/documentation/articles/web-sites-php-web-site-gallery/
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/documentation/articles/cdn-how-to-use/




<!--HONumber=42-->
