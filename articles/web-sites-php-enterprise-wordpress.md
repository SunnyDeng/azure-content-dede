<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Leistungsstarke Umgebung für WordPress auf Azure-Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="cephalin" manager="wpickett" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.devlang="php" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="web" ms.date="01/01/1900" ms.author="cephalin" />

#Leistungsstarke Umgebung für WordPress auf Azure-Websites

Azure-Websites bieten eine skalierbare, sichere und benutzerfreundliche Umgebung für große unternehmenswichtige [WordPress][wordpress]-Websites. Microsoft selbst betreibt leistungsstarke Websites wie die [Office][officeblog]- und [Bing][bingblog]-Blogs. In diesem Dokument wird erläutert, wie Sie mithilfe von Azure-Websites eine hochleistungsfähige, cloudbasierte WordPress-Website einrichten und verwalten können, die mit eine großen Anzahl von Besuchern zurechtkommt.

##Themen in diesem Artikel 

* [Architektur und Planung](#planning) - Machen Sie sich mit der Architektur, den Anforderungen und den Leistungsaspekten vertraut, ehe Sie Ihre Website erstellen.

* [Anleitungen](#howto) zum Erstellen, Bereitstellen und Konfigurieren Ihrer Website

* [Weitere Ressourcen](#resources) - Zusätzliche Ressourcen und Informationen

##<a id="plan"></a>Architektur und Planung

Für eine grundlegende WordPress-Installation gelten nur zwei Anforderungen.

* **MySQL-Datenbank** - Verfügbar über [ClearDB im Azure-Shop][cdbnstore]. Sie können auch Ihre eigene MySQL-Installation auf virtuellen Azure-Computern entweder mithilfe von [Windows][mysqlwindows] oder [Linux][mysqllinux] verwalten.

    > [WACOM.NOTE] ClearDB bietet mehrere MySQL-Konfigurationen mit unterschiedlichen Leistungsmerkmalen für jede Konfiguration. Im [Azure-Shop][cdbnstore] finden Sie Informationen zu den dort verfügbaren Angeboten. Auf der [ClearDB-Website mit den Preisen](http://www.cleardb.com/pricing.view) finden Sie direkt von ClearDB beziehbare Angebote.
    
* **Mindestens PHP 5.2.4** - Azure-Websites unterstützt derzeit die [PHP-Versionen 5.3, 5.4 und 5.5][phpwebsite].

	> [WACOM.NOTE] Wir empfehlen, stets mit der neuesten Version von PHP zu arbeiten, um sicherzustellen, dass Sie über die neuesten Sicherheitskorrekturen verfügen.

###Grundlegende Bereitstellung

Indem Sie die grundlegenden Anforderungen erfüllen, können Sie eine Standardlösung innerhalb einer Azure-Region erstellen.

![an Azure Website and MySQL Database hosted in a single Azure region][basic-diagram]

Wenngleich dies Ihnen ermöglicht, Ihre Anwendung horizontal zu skalieren, indem Sie mehrere Instanzen der Website erstellen, wird die gesamte Konfiguration in den Rechenzentren in einer bestimmten geografischen Region gehostet. Besuchern außerhalb dieser Region werden ggf. nur langsame Antwortzeiten auf der Website geboten, und wenn die Rechenzentren in dieser Region ausfallen, fällt auch Ihre Anwendung aus.


###Bereitstellung in mehreren Regionen

Mithilfe von Azure [Traffic Manager][trafficmanager] ist es möglich, Ihre WordPress-Website auf mehrere geografische Regionen zu skalieren und zugleich Besuchern nur eine URL bereitzustellen. Alle Besucher durchlaufen Traffic Manager und werden anschließend basierend auf der Lastenausgleichskonfiguration an eine Region weitergeleitet.

![an Azure Website, hosted in multiple regions, using CDBR High Availability router to route to MySQL across regions][multi-region-diagram]

Innerhalb jeder Region kann die WordPress-Website weiterhin über mehrere Website-Instanzen skaliert werden. Doch diese Skalierung ist regionsspezifisch. Regionen mit hohem Datenverkehr können anders als Regionen mit niedrigem Datenverkehr skaliert werden.

Die Replikation und das Routing an mehrere MySQL-Datenbanken kann mithilfe des [CDBR-Hochverfügbarkeitsrouters][cleardbscale] (links) oder von [MySQL Cluster CGE][cge] erfolgen. 

###Bereitstellung in mehreren Regionen mit Medienspeicherung und Zwischenspeichern

Wenn die Website Uploads zulässt oder Mediendateien hostet, nutzen Sie den Azure-BLOB-Speicher. Wenn Sie einen Zwischenspeicher benötigen, prüfen Sie den [Redis-Cache][rediscache], die [Memcache-Cloud](http://azure.microsoft.com/en-us/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/en-us/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure-Shop](http://azure.microsoft.com/en-us/gallery/store/).

![an Azure Website, hosted in multiple regions, using CDBR High Availability router for MySQL, with Managed Cache, Blob storage, and CDN][performance-diagram]

BLOB-Speicher wird standardmäßig geografisch auf mehrere Regionen verteilt, sodass Sie sich um die Replikation von Dateien auf allen Websites keine Gedanken machen müssen. Sie können auch das Azure [Content Distribution Network (CDN)][cdn] für die BLOB-Speicherung aktivieren, das Dateien an Endknoten verteilt, die näher bei Ihren Besuchern sind.

###Planung

####Weitere Anforderungen

Maßnahme... | Option...
------------------------|-----------
**Hochladen oder Speichern großer Dateien** | [WordPress-Plug-In für die Verwendung von BLOB-Speicher][storageplugin]
**Senden von E-Mail** | [SendGrid][storesendgrid] und das [WordPress-Plug-In für die Verwendung von SendGrid][sendgridplugin]
**Benutzerdefinierte Domänennamen** | [Benutzerdefinierte Domänennamen mit Azure-Websites][customdomain]
**HTTPS** | [HTTPS-Unterstützung in Azure-Websites][httpscustomdomain]
**Validierung in der Vorproduktion** | [Unterstützung für gestaffelte Veröffentlichung für Azure-Websites][staging] <p>Achtung: Beim Wechseln einer Website vom Staging in die Produktion wird auch die WordPress-Konfiguration verschoben. Sie müssen sicherstellen, dass alle Einstellungen an die Anforderungen Ihrer Produktionswebsite angepasst werden, bevor Sie die gestaffelte Website zur Produktionswebsite machen.</p> 
**Überwachung und Problembehandlung** | [Diagnoseprotokollierung mit Azure-Websites][log] und [Überwachen von Azure-Websites][monitor]
**Bereitstellen Ihrer Website** | [Bereitstellen einer Azure-Website][deploy]

####Verfügbarkeit und Notfallwiederherstellung

Maßnahme... | Option...
------------------------|-----------
**Lastausgleich für Websites** oder **geografische Verteilung von Websites** | [Weiterleiten von Datenverkehr mit Azure Traffic Manager][trafficmanager]
**Sicherung und Wiederherstellung** | [Sichern von Azure-Websites][backup] und [Wiederherstellen einer Azure-Website][restore]

####Leistung

Leistung in der Cloud wird hauptsächlich mithilfe der Zwischenspeicherung und horizontalen Skalierung erreicht. Doch berücksichtigt werden sollten auch der Arbeitsspeicher, die Bandbreite und andere Aspekte des Hostings von Websites.

Maßnahme... | Option...
------------------------|-----------
**Verständnis der Websiteinstanzfunktionen** |  [Preisdetails, darunter Möglichkeiten für Websitegrößen und Modi][websitepricing]
**Cacheressourcen** | [Redis-Cache][rediscache], [Memcache-Cloud](http://azure.microsoft.com/en-us/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/en-us/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure-Shop](http://azure.microsoft.com/en-us/gallery/store/)
**Skalieren der Anwendung** | [Skalieren einer Azure-Website][websitescale] und [ClearDB-Hochverfügbarkeitsrouting][cleardbscale]. Wenn Sie sich für das Hosten und Verwalten Ihrer eigenen MySQL-Installation entscheiden, sollten Sie für das horizontale Skalieren [MySQL Cluster CGE][cge] in Erwägung ziehen.

####Migration

Es gibt zwei Möglichkeiten, eine vorhandene WordPress-Website zu Azure-Websites zu migrieren.

* **[WordPress-Export][export]** - Dient zum Exportieren des Inhalts Ihres Blogs, der anschließend in eine neue WordPress-Website in Azure mithilfe des [WordPress-Importer-Plug-Ins][import] importiert werden kann.

	> [WACOM.NOTE] Dieser Prozess ermöglicht lediglich das Migrieren Ihrer Inhalte und nicht von Plug-Ins, Designs oder anderer Anpassungen. Diese müssen manuell erneut installiert werden.

* **Manuelle Migration** - [Sichern Sie Ihre Website][wordpressbackup] und [Datenbank][wordpressdbbackup], und stellen Sie sie anschließend manuell in einer Azure-Website und dazugehörigen MySQL-Datenbank wieder her, um stark angepasste Websites zu migrieren und den Aufwand der manuellen Installation von Plug-Ins, Designs und anderen Anpassungen zu vermeiden.

##Anleitung

###<a id="create"></a>Erstellen einer neuen WordPress-Website

1. Nutzen Sie den [Azure-Shop][cdbnstore] zum Erstellen einer MySQL-Datenbank der im Abschnitt [Architektur und Planung](#planning) ermittelten Größe in den Regionen, in denen Ihre Website gehostet wird.

2. Befolgen Sie die Schritte in [Erstellen einer WordPress-Website über die Galerie in Azure][createwordpress] zum Erstellen einer neuen WordPress-Website. Wählen Sie beim Erstellen der Website **Vorhandene MySQL-Datenbank verwenden** und anschließend die in Schritt 1 erstellte Datenbank aus.

Wenn Sie eine vorhandene WordPress-Website migrieren, lesen Sie nach dem Erstellen einer neuen Website [Migrieren einer vorhandenen WordPress-Website](#migrate).

###<a id="migrate"></a>Migrieren einer vorhandenen WordPress-Website in Azure

Wie im Abschnitt [Architektur und Planung](#planning) erwähnt, gibt es zwei Möglichkeiten zum Migrieren einer WordPress-Website.

* **Export und Import** - Für Websites ohne zahlreiche Anpassungen, deren Inhalt Sie lediglich verschieben möchten.

* **Sicherung und Wiederherstellung** - Für Websites mit zahlreichen Anpassungen, bei denen Sie alles verschieben möchten.

Befolgen Sie die Anweisungen zum Migrieren Ihrer Website in den folgenden Abschnitten.

####Die Export- und Importmethode

1. Verwenden Sie [WordPress-Export][export] zum Exportieren Ihrer vorhandenen Website.

2. Erstellen Sie eine neue Website anhand der Schritte im Abschnitt [Erstellen einer neuen WordPress-Website](#create).

3. Melden Sie sich in Azure-Websites an Ihrer WordPress-Website an, und klicken Sie auf **Plug-Ins** -> **Neu hinzufügen**. Suchen Sie das Plug-In **WordPress Importer**, und installieren Sie es.

4. Klicken Sie nach der Installation des Importer-Plug-Ins auf **Extras** -> **Importieren**, und wählen Sie dann **WordPress**, um das WordPress-Importer-Plug-In zu verwenden.

5. Klicken Sie auf der Seite **WordPress importieren** auf **Datei auswählen**. Wechseln Sie zu aus Ihrer vorhandenen WordPress-Website exportierten WXR-Datei, und klicken Sie auf **Datei hochladen und importieren**.

6. Klicken Sie auf **Senden**. Sie werden informiert, wenn der Import erfolgreich war.

8. Nachdem Sie alle diese Schritte ausgeführt haben, starten Sie im [Azure-Verwaltungsportal][mgmtportal] im Dashboard Ihrer Website die Website neu.

Nach dem Importieren der Website müssen Sie ggf. die folgenden Schritte ausführen, um nicht in der Importdatei enthaltene Einstellungen zu aktivieren.

Bei Verwendung von... | Maßnahme...
------------------ | ----------
**Permalinks** | Im WordPress-Dashboard der neuen Website klicken Sie auf **Einstellungen** -> **Permalinks** und aktualisieren anschließend die Permalinks-Struktur
**Bild-/Medienlinks** | Zum Anpassen von Links an den neuen Speicherort verwenden Sie das [Plug-In "Velvet Blues Update URLs"][velvet], ein Suchen-und-Ersetzen-Tool, und führen diese Schritte manuell in der Datenbank aus.
**Designs** | Gehen Sie zu **Darstellung** -> **Design**, und aktualisieren Sie die Website wie gewünscht.
**Menüs** | Falls Ihr Design Menüs unterstützt, ist in Links zu Ihrer Homepage ggf. das alte Unterverzeichnis eingebettet. Wechseln Sie zu **Darstellung** -> **Menüs**, und aktualisieren Sie sie.

####Die Sicherungs- und Wiederherstellungsmethode

1. Sichern Sie Ihre vorhandene WordPress-Website mithilfe der Informationen auf der [WordPress-Seite zu Sicherungen][wordpressbackup].

2. Sichern Sie Ihre vorhandene Datenbank anhand der Informationen unter [Sichern Ihrer Datenbank][wordpressdbbackup].

3. Erstellen Sie eine neue Datenbank, und stellen Sie die Sicherung wieder her.

	1. Erwerben Sie eine neue Datenbank im [Azure-Shop][cdbnstore], oder richten Sie eine MySQL-Datenbank auf einem virtuellen [Windows][mysqlwindows]- oder [Linux][mysqllinux]-Computer ein.

	2. Stellen Sie mit einem MySQL-Client wie [MySQL Workbench][workbench] eine Verbindung mit der neuen Datenbank her, und importieren Sie Ihre WordPress-Datenbank.

	3. Ändern Sie in der Datenbank die Domäneneinträge entsprechend Ihrer neuen Azure-Websitedomäne. Beispiel: mywordpress.azurewebsites.net. Verwenden Sie das Skript [Search and Replace for WordPress Databases][searchandreplace], um alle Vorkommen sicher zu ändern.

4. Erstellen Sie eine neue Website, und veröffentlichen Sie die WordPress-Sicherung.

	1. Erstellen Sie im [Azure-Verwaltungsportal][mgmtportal] über **Neu** -> **Website** -> **Benutzerdefiniert erstellen** eine neue Website mit einer Datenbank. Hierdurch wird eine leere Website erstellt.

	2. Suchen Sie in Ihrer WordPress-Sicherung die Datei **wp-config.php**, und öffnen Sie sie in einem Editor. Ersetzen Sie die folgenden Einträge durch die Informationen für Ihre neue MySQL-Datenbank.

		* **DB_NAME** - der Name der Datenbank

		* **DB_USER** - Der Benutzername für den Zugriff auf die Datenbank

		* **DB_PASSWORD** - Das Kennwort des Benutzers

		Nach Ändern dieser Einträge speichern und schließen Sie die Datei **wp-config.php**.

	3. Befolgen Sie die Informationen unter [Bereitstellen einer Azure-Website][deploy], um die gewünschte Bereitstellungsmethode zu aktivieren, und stellen Sie anschließend Ihre WordPress-Sicherung in Ihrer Azure-Website bereit.

5. Nach der Bereitstellung der WordPress-Website sollten Sie auf die neue Website über die URL "*.azurewebsite.net" zugreifen können.

###Konfigurieren Ihrer Website

Befolgen Sie nach dem Erstellen oder Migrieren der WordPress-Website die folgenden Informationen zum Verbessern der Leistung und Ermöglichen zusätzlicher Funktionalität.

Maßnahme... | Option...
------------- | -----------
**Einrichten des Websitemodus und der Größe und Aktivieren der Skalierung** | [Skalieren von Websites][websitescale]
**Aktivieren von dauerhaften Datenbankverbindungen** <p>Standardmäßig verwendet WordPress keine dauerhaften Datenbankverbindungen. Daher kann es vorkommen, dass Ihre Verbindung zur Datenbank nach mehreren Verbindungen gedrosselt wird.</p>  | <ol><li><p>Edit the <strong>wp-includes/wp-db.php</strong> file.</p></li><li><p>Find the following line.</p><code>$this->dbh = mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags );</code></li><li><p>Replace the previous line with the following.</p><code>$this->dbh = mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); <br/>if ( false !== $error_reporting ) { /br/>&nbsp;&nbsp;error_reporting( $error_reporting ); <br/>} </code></li><li><p>Find the following line.</p><code>$this->dbh = @mysql_connect( $this->dbhost, $this->dbuser, $this->dbpassword, $new_link, $client_flags ); </code></li><li><p>Replace the above line with the following.</p><code>$this->dbh = @mysql_pconnect( $this->dbhost, $this->dbuser, $this->dbpassword,  $client_flags ); </code></li><li><p>Save the file <strong>wp-includes/wp-db.php</strong> file and redeploy the site.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>HINWEIS:</h5><p>Diese Änderungen können überschrieben werden, wenn WordPress aktualisiert wird.</p><p>Standardmäßig sind für WordPress automatische Aktualisierungen aktiviert. Diese können deaktiviert werden, indem Sie die Datei <strong>wp-config.php</strong> bearbeiten und <code>define ( 'WP_AUTO_UPDATE_CORE', false );</code></p><p> hinzufügen. Eine andere Möglichkeit zum Umgang mit Aktualisierungen ist die Verwendung eines WebJob, der die Datei <strong>wp-db.php</strong> überwacht und die obigen Änderungen jedes Mal durchführt, wenn die Datei aktualisiert wird. Unter <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Einführung in Webaufträge</a> finden Sie weitere Informationen.</p></div>
**Verbessern der Leistung** | <ul><li><p><a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deaktivieren des ARR-Cookies</a> - kann die Leistung verbessern, wenn WordPress auf mehreren Websiteinstanzen ausgeführt wird</p></li><li><p>Aktiveren von Caching. <a href="http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx">Redis-Cache</a> (Vorschau) kann mit dem <a href="https://wordpress.org/plugins/redis-object-cache/">WordPress-Plug-In des Redis-Objektcaches</a> verwendet werden, oder verwenden Sie eines der anderen Cacheangebote aus dem <a href="http://azure.microsoft.com/en-us/gallery/store/">Azure-Shop</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">WordPress beschleunigen mit Wincache</a> - Wincache ist für Websites standardmäßig aktiviert</p></li><li><p><a href="http://azure.microsoft.com/en-us/documentation/articles/web-sites-scale/">Skalieren Sie die Azure-Website</a> und verwenden Sie <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB-Hochverfügbarkeitsrouting</a> oder <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Verwenden von BLOBs für die Speicherung** | <ol><li><p><a href="http://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/">Erstellen eines Azure-Speicherkontos</a></p></li><li><p>Informationen zur <a href="http://azure.microsoft.com/en-us/documentation/articles/cdn-how-to-use/">Verwendung des Content Distribution Network (CDN)</a> für die geografische Verteilung der in BLOBs gespeicherten Daten.</p></li><li><p>Installieren und Konfigurieren des <a href="https://wordpress.org/plugins/windows-azure-storage/">Azure-Speicher-Plug-Ins für WordPress</a>.</p><p>Detaillierte Einrichtungs- und Konfigurationsinformationen für das Plug-In finden Sie im <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Benutzerhandbuch</a>.</p> </li></ol>
**Aktivieren von E-Mail** | <ol><li><p><a href="http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/">Aktivieren von SendGrid über den Azure-Shop</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installieren des SendGrid-Plug-Ins für WordPress</a></p></li></ol>
**Konfigurieren eines benutzerdefinierten Domänennamens** | [Verwenden eines benutzerdefinierten Domänennamens mit einer Azure-Website][customdomain]
**Aktivieren von HTTPS für einen benutzerdefinierten Domänennamen** | [Verwenden von HTTPS mit einer Azure-Website][httpscustomdomain]
**Lastausgleich oder geografische Verteilung Ihrer Website** | [Weiterleiten von Datenverkehr mit Azure Traffic Manager][trafficmanager]. Wenn Sie eine benutzerdefinierte Domäne verwenden, finden Sie unter [Verwenden eines benutzerdefinierten Domänennamens für eine Azure-Website][customdomain] Informationen zum Verwenden von Traffic Manager mit benutzerdefinierten Domänennamen.
**Aktivieren der automatischen Website-Sicherungen** | [Sichern von Azure-Websites][backup]
**Aktivieren der Diagnoseprotokollierung** | [Aktivieren der Diagnoseprotokollierung für Websites][log]

##<a href="resources"></a>Zusätzliche Ressourcen

* [WordPress-Optimierung](http://codex.wordpress.org/WordPress_Optimization)

* [Konvertieren einer WordPress-Website in eine Multisite](http://azure.microsoft.com/en-us/documentation/articles/web-sites-php-convert-wordpress-multisite/)

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
[phpwebsite]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-php-configure/
[customdomain]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-custom-domain-name/
[trafficmanager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
[backup]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-backup/
[restore]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-restore/
[rediscache]: http://msdn.microsoft.com/en-us/library/azure/dn690470.aspx
[managedcache]: http://msdn.microsoft.com/en-us/library/azure/dn386122.aspx
[websitescale]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-scale/
[managedcachescale]: http://msdn.microsoft.com/en-us/library/azure/dn386113.aspx
[cleardbscale]: http://www.cleardb.com/developers/cdbr/introduction
[staging]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-staged-publishing/
[monitor]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-monitor/
[log]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-enable-diagnostic-log/
[httpscustomdomain]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-configure-ssl-certificate/
[mysqlwindows]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
[mysqllinux]: http://azure.microsoft.com/en-us/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
[cge]: http://www.mysql.com/products/cluster/
[websitepricing]: https://azure.microsoft.com/en-us/pricing/details/web-sites/
[export]: http://en.support.wordpress.com/export/
[Importieren]: http://wordpress.org/plugins/wordpress-importer/
[wordpressbackup]: http://wordpress.org/plugins/wordpress-importer/
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[createwordpress]: (http://azure.microsoft.com/en-us/documentation/articles/web-sites-php-web-site-gallery/)
[velvet]: https://wordpress.org/plugins/velvet-blues-update-urls/
[mgmtportal]: https://manage.windowsazure.com/
[wordpressbackup]: http://codex.wordpress.org/WordPress_Backups
[wordpressdbbackup]: http://codex.wordpress.org/Backing_Up_Your_Database
[workbench]: http://www.mysql.com/products/workbench/
[searchandreplace]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
[deploy]: http://azure.microsoft.com/en-us/documentation/articles/web-sites-deploy/
[posh]: http://azure.microsoft.com/en-us/documentation/articles/install-configure-powershell/
[xplat-cli]: http://azure.microsoft.com/en-us/documentation/articles/xplat-cli/
[storesendgrid]: http://azure.microsoft.com/en-us/gallery/store/sendgrid/sendgrid-azure/
[cdn]: http://azure.microsoft.com/en-us/documentation/articles/cdn-how-to-use/
