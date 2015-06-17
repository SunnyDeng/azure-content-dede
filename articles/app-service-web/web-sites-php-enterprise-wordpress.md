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
	ms.date="04/29/2015"
	ms.author="tomfitz"/>

#Leistungsstarkes WordPress in Azure App Service

Azure-App-Dienst bietet eine skalierbare, sichere und benutzerfreundliche Umgebung für unternehmenswichtige wichtiger, großen Maßstab [WordPress][wordpress] Websites. Microsoft selbst führt Unternehmensklasse Sites wie z. B. die [Office][officeblog] und [Bing][bingblog] Blogs. In diesem Dokument wird erläutert, wie Sie mithilfe von Azure App Service-Web-Apps eine leistungsfähige, cloudbasierte WordPress-Website einrichten und verwalten können, die mit eine großen Anzahl von Besuchern zurechtkommt.

## Architektur und Planung

Für eine grundlegende WordPress-Installation gelten nur zwei Anforderungen.

* **MySQL-Datenbank** – über [ClearDB in Azure Marketplace][cdbnstore], oder Sie können eigene MySQL-Installation auf Azure Virtual Machines entweder verwalten [Windows][mysqlwindows] oder [Linux][mysqllinux].

    > [AZURE.NOTE]ClearDB bietet mehrere MySQL-Konfigurationen mit unterschiedlichen Leistungsmerkmalen für jede Konfiguration. Im [Azure Shop][cdbnstore] finden Sie Informationen zu den dort verfügbaren Angeboten. Auf der [ClearDB-Webseite mit den Preisen](http://www.cleardb.com/pricing.view) finden Sie direkt von ClearDB beziehbare Angebote.

* **PHP 5.2.4 oder größer** -Azure-App-Dienst derzeit bieten [PHP-Versionen, 5.3, 5.4 und 5.5][phpwebsite].

	> [AZURE.NOTE]Wir empfehlen, stets mit der neuesten Version von PHP zu arbeiten, um sicherzustellen, dass Sie über die neuesten Sicherheitskorrekturen verfügen.

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

Wenn die Website Uploads zulässt oder Mediendateien hostet, nutzen Sie den Azure-BLOB-Speicher. Wenn Sie einen Zwischenspeicher benötigen, prüfen Sie den [Redis-Cache][rediscache], die [Memcache-Cloud](http://azure.microsoft.com/gallery/store/garantiadata/memcached/), [MemCachier](http://azure.microsoft.com/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure Shop](http://azure.microsoft.com/gallery/store/).

![Eine in mehreren Regionen gehostete Azure-Web-App mit CDBR-Hochverfügbarkeitsrouter für MySQL, Managed Cache, Blob-Speicher und CDN][performance-diagram]

BLOB-Speicher wird standardmäßig geografisch auf mehrere Regionen verteilt, sodass Sie sich um die Replikation von Dateien auf allen Websites keine Gedanken machen müssen. Sie können auch das Azure [Content Distribution Network (CDN)][cdn] für die BLOB-Speicherung aktivieren, das Dateien an Endknoten verteilt, die näher bei Ihren Besuchern sind.

###Planung

####Weitere Anforderungen

Maßnahme | Option
------------------------|-----------
**Hochladen Sie oder speichern Sie große Dateien zu.** | [WordPress-Plug-In für die Verwendung von Blob-Speicher][storageplugin]
**E-Mail senden** | [SendGrid][storesendgrid] und das [WordPress-Plug-In für die Verwendung von SendGrid][sendgridplugin]
**Benutzerdefinierte Domänennamen** | [Konfigurieren eines benutzerdefinierten Domänennamens in Azure-App-Dienst][customdomain]
**HTTPS** | [Aktivieren von HTTPS für eine Webanwendung in Azure-App-Dienst][httpscustomdomain]
**Pre-Production-Überprüfung** | [Staging-Umgebungen für Webanwendungen in Azure-App-Dienst einrichten][staging] <p>Beachten Sie, dass die WordPress-Konfiguration wechseln von einer Webanwendung aus staging zur Produktion auch verschoben werden. Sie sollten sicherstellen, dass alle Einstellungen den Anforderungen für die Produktion app aktualisiert werden, vor dem Wechseln der bereitgestellten Anwendung in der Produktionsumgebung.</p>
**Überwachung und Problembehandlung** | [Aktivieren der Diagnoseprotokollierung für Webanwendungen in Azure-App-Dienst][log] und [Monitor Web-Apps in Azure-App-Dienst][monitor]
**Bereitstellen von Ihrer Website** | [Bereitstellen einer Webanwendung in Azure-App-Dienst][deploy]

####Verfügbarkeit und Notfallwiederherstellung

Maßnahme | Option
------------------------|-----------
**Einen Lastenausgleich für Websites vornehmen** oder **Websites geografisch verteilen** | [Weiterleitung von Verkehr mit Azure Traffic Manager][trafficmanager]
**Sicherung und Wiederherstellung** | [Sichern einer Webanwendung in Azure-App-Dienst][backup] und [Wiederherstellen eine Webanwendung in Azure-App-Dienst][restore]

####Leistung

Leistung in der Cloud wird hauptsächlich mithilfe der Zwischenspeicherung und horizontalen Skalierung erreicht. Doch berücksichtigt werden sollten auch der Arbeitsspeicher, die Bandbreite und andere Aspekte des Hostings von Web-Apps.

Maßnahme | Option
------------------------|-----------
**Verstehen von App-Instanz-Funktionen** | [Informationen zur Preisgestaltung, einschließlich der Funktionen des App-Dienstebenen][websitepricing]
**Cache-Ressourcen** | [Redis-Cache][rediscache], [Memcache-Cloud](/gallery/store/garantiadata/memcached/), [MemCachier](/gallery/store/memcachier/memcachier/) oder eines der anderen Cacheangebote im [Azure Shop](/gallery/store/)
**Skalieren Sie Ihre Anwendung** | [Skalieren eine Webanwendung in Azure-App-Dienst][websitescale] und [ClearDB hohe Verfügbarkeit Routing][cleardbscale]. Wenn Sie sich für das Hosten und Verwalten Ihrer eigenen MySQL-Installation entscheiden, sollten Sie für das horizontale Skalieren [MySQL Cluster CGE][cge] in Erwägung ziehen.

####Migration

Es gibt zwei Möglichkeiten, eine vorhandene WordPress-Website nach Azure App Service zu migrieren.

* **[WordPress exportieren][export]** -exportiert den Inhalt Ihres Blogs, die dann an eine neue WordPress-Website zur Verwendung von Azure-App-Dienst importiert werden, kann die [WordPress Importer-Plug-in][import].

	> [AZURE.NOTE]Dieser Prozess ermöglicht lediglich das Migrieren Ihrer Inhalte und nicht von Plug-Ins, Designs oder anderer Anpassungen. Diese müssen manuell erneut installiert werden.

* **Manuelle Migration** - [Sichern Sie Ihre Website][wordpressbackup] und [Datenbank][wordpressdbbackup], dann manuell Wiederherstellen einer Webanwendung in Azure-App-Dienst und zugeordneten MySQL-Datenbank, um stark angepasste Websites zu migrieren, und vermeiden den zeitlichen Aufwand-Plug-Ins, Designs und andere Anpassungen manuell zu installieren.

## Schrittweise Anweisungen

### Erstellen einer neuen WordPress-Website

1. Verwenden der [Azure Marketplace][cdbnstore] der Größe eine MySQL-Datenbank erstellen Sie, in identifiziert der [Architektur und Planung](#planning) Abschnitt in der Region, dass Sie Ihre Website hostet.

2. Führen Sie die Schritte im [erstellen eine WordPress-Webanwendung in Azure-App-Dienst][createwordpress] eine neue WordPress-Webanwendung zu erstellen. Wenn Sie die Webanwendung zu erstellen, wählen Sie **Verwenden Sie eine vorhandene MySQL-Datenbank** und wählen Sie die in Schritt 1 erstellten Datenbank.

Wenn Sie eine vorhandene WordPress-Website migrieren, finden Sie unter [Migrieren eine vorhandene WordPress-Website in Azure](#Migrate-an-existing-WordPress-site-to-Azure) nach dem Erstellen einer neuen Webanwendung.

### Migrieren einer vorhandenen WordPress-Website in Azure

Gemäß der [Architektur und Planung](#planning) Abschnitt, es gibt zwei Möglichkeiten zum Migrieren einer WordPress-Website.

* **Export und Import** - Für Websites ohne zahlreiche Anpassungen, deren Inhalt Sie lediglich verschieben möchten.

* **Sicherung und Wiederherstellung** - Für Websites mit zahlreichen Anpassungen, bei denen Sie alles verschieben möchten.

Befolgen Sie die Anweisungen zum Migrieren Ihrer Website in den folgenden Abschnitten.

####Die Export- und Importmethode

1. Verwenden Sie die [WordPress-Exportfunktion][export] zum Exportieren Ihrer vorhandenen Website.

2. Erstellen eine neuen Webanwendung mithilfe der Schritte in den [Erstellen Sie eine neue WordPress-Website](#Create-a-new-WordPress-site) Abschnitt.

3. Melden Sie sich auf Webanwendungen Ihrer WordPress-Website und klicken Sie auf **-Plug-Ins** -> **Hinzufügen**. Suchen Sie das Plug-In **WordPress Importer**, und installieren Sie es.

4. Klicken Sie nach der Installation des Importer-Plug-Ins auf **Extras** -> **Importieren**, und wählen Sie dann **WordPress** aus, um das WordPress-Importer-Plug-In zu verwenden.

5. Klicken Sie auf der Seite **WordPress importieren** auf **Datei wählen**. Wechseln Sie zu aus Ihrer vorhandenen Wordpress-Website exportierten WXR-Datei, und klicken Sie auf **Datei hochladen** und Importieren.

6. Klicken Sie auf **Senden**. Sie werden informiert, wenn der Import erfolgreich war.

8. Nachdem Sie alle Schritte abgeschlossen haben, neu starten Ihrer Website von der Web-app-Blade in der [Azure-vorschauportal][mgmtportal].

Nach dem Importieren der Website müssen Sie ggf. die folgenden Schritte ausführen, um nicht in der Importdatei enthaltene Einstellungen zu aktivieren.

Bei Verwendung von | Maßnahme
------------------ | ----------
**Permalinks** | Im WordPress-Dashboard der neuen Website klicken Sie auf **Einstellungen** -> **Permalinks** und aktualisieren anschließend die Permalinks-Struktur.
**Image-Medium-links** | Zum Anpassen von Links an den neuen Speicherort verwenden Sie das [Plug-In "Velvet Blues Update URLs"][velvet], ein Suchen-und-Ersetzen-Tool und führen diese Schritte manuell in der Datenbank aus.
**Designs** | Wechseln Sie zu **Darstellung** -> **Design** und das Design der Site zu aktualisieren, je nach Bedarf
**Menüs** | Falls Ihr Design Menüs unterstützt, ist in Links zu Ihrer Homepage ggf. das alte Unterverzeichnis eingebettet. Wechseln Sie zu **Darstellung** -> **Menüs**, und aktualisieren Sie sie.

####Die Sicherungs- und Wiederherstellungsmethode

1. Sichern Sie Ihre vorhandene WordPress-Website mithilfe der Informationen auf der [WordPress-Seite zu Sicherungen][wordpressbackup].

2. Sichern Sie Ihre vorhandene Datenbank anhand der Informationen unter [Sichern Ihrer Datenbank][wordpressdbbackup].

3. Erstellen Sie eine neue Datenbank, und stellen Sie die Sicherung wieder her.

	1. Erwerben Sie eine neue Datenbank aus der [Azure Marketplace][cdbnstore], oder einrichten eine MySQL-Datenbank auf einem [Windows][mysqlwindows] oder [Linux][mysqllinux] VM.

	2. Stellen Sie mit einem MySQL-Client wie [MySQL Workbench][workbench] eine Verbindung mit der neuen Datenbank her, und importieren Sie Ihre WordPress-Datenbank.

	3. Ändern Sie in der Datenbank die Domäneneinträge entsprechend Ihrer neuen Azure App Service-Domäne. Zum Beispiel mywordpress.azurewebsites.net. Verwenden der [Suchen und Ersetzen von WordPress Datenbanken Skript][searchandreplace] problemlos alle Instanzen ändern.

4. Erstellen Sie im Azure-Verwaltungsportal eine neue Web-App, und veröffentlichen Sie die WordPress-Sicherung.

	1. Erstellen Sie eine neue Webanwendung in der [Azure Preview Portal][mgmtportal] mit einer Datenbank mit **Neu** -> **Web + Mobile** -> **Azure Marketplace** -> **Webanwendungen** -> **Web app + SQL** (oder **Web app und MySQL**) -> **Erstellen**. Konfigurieren Sie alle erforderlichen Einstellungen, um eine leere Web-App zu erstellen.

	2. Suchen Sie in Ihrer WordPress-Sicherung die Datei **wp-config.php**, und öffnen Sie sie in einem Editor. Ersetzen Sie die folgenden Einträge durch die Informationen für Ihre neue MySQL-Datenbank.

		* **DB_NAME** - Der Name der Datenbank

		* **DB_USER** - Der Benutzername für den Zugriff auf die Datenbank

		* **DB_PASSWORD** - Das Kennwort des Benutzers

		Nach Ändern dieser Einträge speichern und schließen Sie die Datei **wp-config.php**.

	3. Verwenden Sie die [Bereitstellen eine Webanwendung in Azure-App-Dienst][deploy] Informationen zum Aktivieren der Bereitstellungsmethode Sie verwenden und die Sicherung WordPress für Ihre Webanwendung in Azure-App-Dienst bereitstellen möchten.

5. Sobald die WordPress-Website bereitgestellt wurde, muss Zugriff auf den neuen Standort (wie ein App-Service-Webanwendung) mit der *. azurewebsite.net-URL für die Website.

###Konfigurieren Ihrer Website

Befolgen Sie nach dem Erstellen oder Migrieren der WordPress-Website die folgenden Informationen zum Verbessern der Leistung und Ermöglichen zusätzlicher Funktionalität.

Maßnahme | Option
------------- | -----------
**Legen Sie App-Service-Plan-Modus, Größe und Skalierung aktivieren** | [Skalieren einer Web-App in Azure App Service][websitescale]
**Aktivieren von Verbindungen mit der persistenten Datenbank** <p>standardmäßig WordPress verwendet keine permanente Datenbankverbindungen, aufgrund derer die Verbindung mit der Datenbank nach mehreren Verbindungen eingeschränkt werden können.</p> | <ol><li><p>Bearbeiten der <strong>wp-umfasst/wp-db.php</strong> Datei.</p></li><li><p>Suchen Sie die folgende Zeile.</p><code>$Dies-Bhd > = Mysql_connect ($dies Dbhost $dies -> -> Dbuser $dies-Dbpassword, $ Link zu neues, $Client_flags >);</code></li><li><p>Ersetzen Sie die vorherige Zeile durch Folgendes.</p><code>$Dies-Bhd > = Mysql_pconnect ($dies Dbhost $dies Dbuser $dies -> -> -> Dbpassword $Client_flags); <br/>Wenn (false! == $Error_reporting) {/br/ > & Nbsp; & Nbsp; Error_reporting ($Error_reporting); <br/>} </code></li><li><p>Suchen Sie die folgende Zeile.</p><code>$Dies-Bhd > = @mysql_connect ($dies Dbhost $dies -> -> Dbuser $dies -> Dbpassword, $ Link zu neues, $Client_flags); </code></li><li><p>Ersetzen Sie die obige Zeile durch Folgendes.</p><code>$Dies-Bhd > = @mysql_pconnect ($dies Dbhost $dies Dbuser $dies -> -> -> Dbpassword $Client_flags); </code></li><li><p>Speichern Sie die Datei <strong>wp-umfasst/wp-db.php</strong> Datei und erneute Bereitstellung die Website.</p></li></ol><div class="wa-note"><span class="wa-icon-bulb"></span><p>Diese Änderungen können überschrieben werden, wenn WordPress aktualisiert wird.</p><p>WordPress-Standardeinstellungen für automatische Updates, die durch Bearbeiten deaktiviert werden können die <strong>wp-config.php</strong> Datei- und Hinzufügen von <code>definieren ("WP_AUTO_UPDATE_CORE", false);</code></p><p>Adressierung Updates eine weitere Möglichkeit wäre, eine WebJob zu verwenden, die überwacht die <strong>wp-db.php</strong> Datei und führt die oben genannten Änderungen jedes Mal die Datei wird aktualisiert. Finden Sie unter <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Einführung in die WebJobs</a> für Weitere Informationen.</p></div>
**Verbessern der Leistung** | <ul><li><p><a href="http://ppe.blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deaktivieren Sie das Cookie ARR</a> -kann die Leistung verbessern, bei der Ausführung von WordPress auf mehrere Instanzen von Webanwendungen</p></li><li><p>Zwischenspeichern aktivieren. <a href="http://msdn.microsoft.com/library/azure/dn690470.aspx">Redis-Cache</a> (Vorschau) kann verwendet werden, mit der <a href="https://wordpress.org/plugins/redis-object-cache/">Redis Objekt Cache WordPress-Plug-in</a>, oder verwenden Sie eine der anderen caching Angebote von der <a href="/gallery/store/">Azure Store</a></p></li><li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">erläutert, wie Sie mit Wincache schneller WordPress</a> -Wincache ist standardmäßig aktiviert, für Web-Apps</p></li><li><p><a href="../web-sites-scale/">Skalieren eine Webanwendung in Azure-App-Dienst</a> und <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB hohe Verfügbarkeit Routing</a> oder <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a></p></li></ul>
**Verwenden von Blobs für Speicher** | <ol><li><p><a href="../storage-create-storage-account/">Erstellen Sie ein Speicherkonto, das</a></p></li><li><p>informieren Sie sich wie <a href="../cdn-how-to-use/">Content Distribution Network (CDN) verwenden</a> geografische-Verteilen von Daten in Blobs gespeichert.</p></li><li><p>Installieren und Konfigurieren der <a href="https://wordpress.org/plugins/windows-azure-storage/">für WordPress-Plug-in Azure-Speicher</a>.</p><p>Detaillierte Setup- und Konfigurationsinformationen für das Plug-in finden Sie unter der <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">-Benutzerhandbuch</a>.</p> </li></ol>
**E-Mail aktivieren** | <ol><li><p><a href="/gallery/store/sendgrid/sendgrid-azure/">Aktivieren Sie den Azure-Speicher mit SendGrid</a></p></li><li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">der SendGrid-Plug-In für WordPress installieren</a></p></li></ol>
**Konfigurieren eines benutzerdefinierten Domänennamens** | [Konfigurieren eines benutzerdefinierten Domänennamens in Azure-App-Dienst][customdomain]
**Aktivieren von HTTPS für einen benutzerdefinierten Domänennamen** | [Aktivieren von HTTPS für eine Webanwendung in Azure-App-Dienst][httpscustomdomain]
**Saldo oder geografische laden-Verteilen Ihrer Website** | [Leiten Sie Datenverkehr mit Azure Traffic Manager weiter][trafficmanager]. Wenn Sie eine benutzerdefinierte Domäne verwenden, finden Sie unter [Konfigurieren Sie einen benutzerdefinierten Domänennamen in Azure-Anwendungsdiensts][customdomain] Informationen zur Verwendung von Traffic Manager mit benutzerdefinierten Domänennamen
**Automatisierte Sicherungen aktivieren** | [Sichern einer Webanwendung in Azure-App-Dienst][backup]
**Aktivieren der Diagnoseprotokollierung** | [Aktivieren der Diagnoseprotokollierung für Webanwendungen in Azure-App-Dienst][log]

## Nächste Schritte

* [WordPress-Optimierung](http://codex.wordpress.org/WordPress_Optimization)

* [Konvertieren von WordPress, mehrere Standorte in Azure-App-Dienst](web-sites-php-convert-wordpress-multisite.md)

* [ClearDB Aktualisierungs-Assistenten für Azure](http://www.cleardb.com/store/azure/upgrade)

* [WordPress in einem Unterordner der Webanwendung in Azure-App-Dienst hosten](http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx)

* [Schrittweise Anleitung: Erstellen einer WordPress-Website mithilfe von Azure](http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx)

* [Hosten Sie Ihre vorhandenen WordPress-Blog auf Azure](http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx)

* [Ziemlich Permalinks in WordPress aktivieren](http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress)

* [Wie Sie migrieren und WordPress-Blog auf Azure-App-Dienst ausführen](http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/)

* [WordPress kostenlos in Azure-App-Dienst ausführen](http://architects.dzone.com/articles/how-run-wordpress-azure)

* [WordPress auf Azure innerhalb von 2 Minuten oder weniger](http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/)

* [Verschieben einen WordPress-Blog in Azure - Teil 1: Erstellen eines WordPress-Blogs auf Azure](http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1)

* [Verschieben einen WordPress-Blog in Azure – Teil 2: Übertragen von Ihrer Inhalte](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content)

* [Verschieben einen WordPress-Blog in Azure - Teil 3: Einrichten Ihrer benutzerdefinierten Domäne](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain)

* [Verschieben einen WordPress-Blog in Azure - Teil 4: so ziemlich Permalinks und URL-Rewrite-Regeln](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules)

* [Verschieben einen WordPress-Blog in Azure – Teil 5: aus einem Unterordner in den Stamm verschieben](http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root)

* [Zum Einrichten einer WordPress-Webanwendung in Azure-Konto](http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/)

* [Propping von WordPress in Azure](http://www.johnpapa.net/wordpress-on-azure/)

* [Tipps für WordPress in Azure](http://www.johnpapa.net/azurecleardbmysql/)

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715).

[performance-diagram]: ./media/web-sites-php-enterprise-wordpress/performance-diagram.png
[basic-diagram]: ./media/web-sites-php-enterprise-wordpress/basic-diagram.png
[multi-region-diagram]: ./media/web-sites-php-enterprise-wordpress/multi-region-diagram.png
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
[mysqlwindows]: ../virtual-machines-mysql-windows-server-2008r2.md
[mysqllinux]: ../virtual-machines-linux-mysql-use-opensuse.md
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
[posh]: ../install-configure-powershell.md
[Azure CLI]: ../xplat-cli.md
[storesendgrid]: /gallery/store/sendgrid/sendgrid-azure/
[cdn]: ../cdn-how-to-use.md
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->