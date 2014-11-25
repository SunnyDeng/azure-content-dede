<properties title="Enterprise class WordPress on Azure Websites" pageTitle="Enterprise class WordPress on Azure Websites" description="Learn how to host an enterprise class WordPress site on Azure Websites" metaKeywords="wordpress azure, wordpress website, wordpress azure website" services="web-sites" solutions="web" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

## Leistungsstarke Umgebung für WordPress auf Azure-Websites

Azure-Websites bieten eine skalierbare, sichere und benutzerfreundliche Umgebung für große unternehmenswichtige [WordPress][WordPress]-Websites. Microsoft selbst betreibt leistungsstarke Websites wie die [Office][Office]- und [Bing][Bing]-Blogs. In diesem Dokument wird erläutert, wie Sie mithilfe von Azure-Websites eine hochleistungsfähige, cloudbasierte WordPress-Website einrichten und verwalten können, die mit eine großen Anzahl von Besuchern zurechtkommt.

## Themen in diesem Artikel

-   [Architektur und Planung][Architektur und Planung] - Machen Sie sich mit der Architektur, Anforderungen und Leistungsaspekten vertraut, ehe Sie Ihre Website erstellen.

-   [Anleitungen][Anleitungen] zum Erstellen, Bereitstellen und Konfigurieren Ihrer Website

-   [Weitere Ressourcen][Weitere Ressourcen] - Zusätzliche Ressourcen und Informationen

## <span id="plan"></span></a>Architektur und Planung

Für eine grundlegende WordPress-Installation gelten nur zwei Anforderungen.

-   **MySQL-Datenbank** - Verfügbar über [ClearDB im Azure Shop][ClearDB im Azure Shop]. Sie können auch Ihre eigene MySQL-Installation auf virtuellen Azure-Computern entweder mithilfe von [Windows][Windows] oder [Linux][Linux] verwalten.

    > [WACOM.NOTE] ClearDB bietet mehrere MySQL-Konfigurationen mit unterschiedlichen Leistungsmerkmalen für jede Konfiguration. Im [Azure Shop][ClearDB im Azure Shop] finden Sie Informationen zu den dort verfügbaren Angeboten. Auf der [ClearDB-Webseite mit den Preisen][ClearDB-Webseite mit den Preisen] finden Sie direkt von ClearDB beziehbare Angebote.

-   **Mindestens PHP 5.2.4** - Azure-Websites unterstützen derzeit die [PHP-Versionen 5.3, 5.4 und 5.5][PHP-Versionen 5.3, 5.4 und 5.5].

    > [WACOM.NOTE] Wir empfehlen, stets mit der neuesten Version von PHP zu arbeiten, um sicherzustellen, dass Sie über die neuesten Sicherheitskorrekturen verfügen.

### Grundlegende Bereitstellung

Indem Sie die grundlegenden Anforderungen erfüllen, können Sie eine Standardlösung innerhalb einer Azure-Region erstellen.

![Eine Azure-Website und MySQL-Datenbank, die in einer einzelnen Azure-Region gehostet werden][Eine Azure-Website und MySQL-Datenbank, die in einer einzelnen Azure-Region gehostet werden]

Wenngleich dies Ihnen ermöglicht, Ihre Anwendung horizontal zu skalieren, indem Sie mehrere Instanzen der Website erstellen, wird die gesamte Konfiguration in den Rechenzentren in einer bestimmten geografischen Region gehostet. Besuchern außerhalb dieser Region werden ggf. nur langsame Antwortzeiten auf der Website geboten, und wenn die Rechenzentren in dieser Region ausfallen, fällt auch Ihre Anwendung aus.

### Bereitstellung in mehreren Regionen

Mithilfe von Azure [Traffic Manager][Traffic Manager] ist es möglich, Ihre WordPress-Website auf mehrere geografische Regionen zu skalieren und zugleich Besuchern nur eine URL bereitzustellen. Alle Besucher durchlaufen Traffic Manager und werden anschließend basierend auf der Lastenausgleichskonfiguration an eine Region weitergeleitet.

![Eine in mehreren Regionen gehostete Azure-Website mit CDBR-Hochverfügbarkeitsrouter zum regionsübergreifenden Weiterleiten an MySQL][Eine in mehreren Regionen gehostete Azure-Website mit CDBR-Hochverfügbarkeitsrouter zum regionsübergreifenden Weiterleiten an MySQL]

Innerhalb jeder Region kann die WordPress-Website weiterhin über mehrere Website-Instanzen skaliert werden. Doch diese Skalierung ist regionsspezifisch. Regionen mit hohem Datenverkehr können anders als Regionen mit niedrigem Datenverkehr skaliert werden.

Die Replikation und das Routing an mehrere MySQL-Datenbanken kann mithilfe des [CDBR-Hochverfügbarkeitsrouters][CDBR-Hochverfügbarkeitsrouters] (links) oder von [MySQL Cluster CGE][MySQL Cluster CGE] erfolgen.

### Bereitstellung in mehreren Regionen mit Medienspeicherung und Zwischenspeichern

Wenn die Website Uploads zulässt oder Mediendateien hostet, nutzen Sie den Azure-BLOB-Speicher. Wenn Sie einen Zwischenspeicher benötigen, prüfen Sie den [Redis-Cache][Redis-Cache], die [Memcache-Cloud][Memcache-Cloud], [MemCachier][MemCachier] oder eines der anderen Cacheangebote im [Azure Shop][Azure Shop].

![eine in mehreren Regionen gehostete Azure-Website mit CDBR-Hochverfügbarkeitsrouter für MySQL, Managed Cache, BLOB-Speicher und CDN][eine in mehreren Regionen gehostete Azure-Website mit CDBR-Hochverfügbarkeitsrouter für MySQL, Managed Cache, BLOB-Speicher und CDN]

BLOB-Speicher wird standardmäßig geografisch auf mehrere Regionen verteilt, sodass Sie sich um die Replikation von Dateien auf allen Websites keine Gedanken machen müssen. Sie können auch das Azure [Content Distribution Network (CDN)][Content Distribution Network (CDN)] für die BLOB-Speicherung aktivieren, das Dateien an Endknoten verteilt, die näher bei Ihren Besuchern sind.

### Planung

#### Weitere Anforderungen

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Maßnahme</th>
<th align="left">Option</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Hochladen oder Speichern großer Dateien</strong></td>
<td align="left"><a href="https://wordpress.org/plugins/windows-azure-storage/">WordPress-Plug-In für die Verwendung von BLOB-Speicher</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Senden von E-Mail</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/gallery/store/sendgrid/sendgrid-azure/">SendGrid</a> und das <a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">WordPress-Plug-In für die Verwendung von SendGrid</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Benutzerdefinierte Domänennamen</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-custom-domain-name/">Benutzerdefinierte Domänennamen mit Azure-Websites</a></td>
</tr>
<tr class="even">
<td align="left"><strong>HTTPS</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure-ssl-certificate/">HTTPS-Unterstützung für Azure-Websites</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Überprüfung vor Wechsel zur Produktionsumgebung</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-staged-publishing/">Unterstützung der gestaffelten Veröffentlichung für Azure-Websites</a>
<p>Beachten Sie, dass beim Wechsel einer Website von der Staging- zur Produktionsumgebung auch die WordPress-Konfiguration verschoben wird. Sie müssen sicherstellen, dass alle Einstellungen an die Anforderungen Ihrer Produktionswebsite angepasst werden, bevor Sie die Stagingwebsite zur Produktionswebsite machen.</p></td>
</tr>
<tr class="even">
<td align="left"><strong>Überwachung und Problembehandlung</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-enable-diagnostic-log/">Diagnoseprotokollierung mit Azure-Websites</a> und <a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-monitor/">Überwachen von Azure-Websites</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Bereitstellen Ihrer Website</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-deploy/">Bereitstellen einer Azure-Website</a></td>
</tr>
</tbody>
</table>

#### Verfügbarkeit und Notfallwiederherstellung

| Maßnahme                                                                                 | Option                                                                      |
|------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------|
| **Einen Lastenausgleich für Websites vornehmen** oder **Websites geografisch verteilen** | [Weiterleiten von Datenverkehr mit Azure Traffic Manager][Traffic Manager]  |
| **Sichern und Wiederherstellen**                                                         | [Sichern von Azure-Websites][Sichern von Azure-Websites] und [Wiederherstellen einer Azure-Website][Wiederherstellen einer Azure-Website] |

#### Leistung

Leistung in der Cloud wird hauptsächlich mithilfe der Zwischenspeicherung und horizontalen Skalierung erreicht. Doch berücksichtigt werden sollten auch der Arbeitsspeicher, die Bandbreite und andere Aspekte des Hostings von Websites.

| Maßnahme                                                  | Option                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Grundlegendes zu den Funktionen von Website-Instanzen** | [Details zu den Preisen, einschließlich Funktionen verschiedener Größen und Modi von Websites][Details zu den Preisen, einschließlich Funktionen verschiedener Größen und Modi von Websites]                                                                                                                                                                                        |
| **Cacheressourcen**                                       | [Redis-Cache][Redis-Cache], [Memcache-Cloud][Memcache-Cloud], [MemCachier][MemCachier] oder eines der anderen Cacheangebote im [Azure Shop][Azure Shop]                                                                                                                                                                              |
| **Skalieren Ihrer Anwendung**                             | [Skalieren einer Azure-Website][Skalieren einer Azure-Website] und [ClearDB-Hochverfügbarkeitsrouting][CDBR-Hochverfügbarkeitsrouters]. Wenn Sie sich für das Hosten und Verwalten Ihrer eigenen MySQL-Installation entscheiden, sollten Sie für das horizontale Skalieren [MySQL Cluster CGE][MySQL Cluster CGE] in Erwägung ziehen. |

#### Migration

Es gibt zwei Möglichkeiten, eine vorhandene WordPress-Website zu Azure-Websites zu migrieren.

-   **[WordPress-Export][WordPress-Export]** - Dient zum Exportieren des Inhalts Ihres Blogs, der anschließend in eine neue WordPress-Website in Azure mithilfe des [WordPress-Import-Plug-Ins][WordPress-Import-Plug-Ins] importiert werden kann.

    > [WACOM.NOTE] Dieser Prozess ermöglicht lediglich das Migrieren Ihrer Inhalte und nicht von Plug-Ins, Designs oder anderer Anpassungen. Diese müssen manuell erneut installiert werden.

-   **Manuelle Migration** - [Sichern Sie Ihre Website][Sichern Sie Ihre Website] und [Datenbank][Datenbank], und stellen Sie sie anschließend manuell in einer Azure-Website und dazugehörigen MySQL-Datenbank wieder her, um stark angepasste Websites zu migrieren und den Aufwand der manuellen Installation von Plug-Ins, Designs und anderen Anpassungen zu vermeiden.

## Anleitung

### <span id="create"></span></a>Erstellen einer neuen WordPress-Website

1.  Nutzen Sie den [Azure Shop][ClearDB im Azure Shop] zum Erstellen einer MySQL-Datenbank der im Abschnitt [Architektur und Planung][Architektur und Planung] ermittelten Größe in den Regionen, in denen Ihre Website gehostet wird.

2.  Befolgen Sie die Schritte in [Erstellen einer WordPress-Website über die Galerie in Azure][Erstellen einer WordPress-Website über die Galerie in Azure] zum Erstellen einer neuen WordPress-Website. Wählen Sie beim Erstellen der Website **Vorhandene MySQL-Datenbank verwenden** und anschließend die in Schritt 1 erstellte Datenbank aus.

Wenn Sie eine vorhandene WordPress-Website migrieren, lesen Sie nach dem Erstellen einer neuen Website [Migrieren einer vorhandenen WordPress-Website][Migrieren einer vorhandenen WordPress-Website].

### <span id="migrate"></span></a>Migrieren einer vorhandenen WordPress-Website in Azure

Wie im Abschnitt [Architektur und Planung][Architektur und Planung] erwähnt, gibt es zwei Möglichkeiten zum Migrieren einer WordPress-Website.

-   **Export und Import** - Für Websites ohne zahlreiche Anpassungen, deren Inhalt Sie lediglich verschieben möchten.

-   **Sicherung und Wiederherstellung** - Für Websites mit zahlreichen Anpassungen, bei denen Sie alles verschieben möchten.

Befolgen Sie die Anweisungen zum Migrieren Ihrer Website in den folgenden Abschnitten.

#### Die Export- und Importmethode

1.  Verwenden Sie die [WordPress-Exportfunktion][WordPress-Export] zum Exportieren Ihrer vorhandenen Website.

2.  Erstellen Sie eine neue Website anhand der Schritte im Abschnitt [Erstellen einer neuen WordPress-Website][Erstellen einer neuen WordPress-Website].

3.  Melden Sie sich in Azure-Websites an Ihrer WordPress-Website an, und klicken Sie auf **Plug-Ins** -\> **Neu hinzufügen**. Suchen Sie das Plug-In **WordPress Importer**, und installieren Sie es.

4.  Klicken Sie nach der Installation des Importer-Plug-Ins auf **Extras** -\> **Importieren**, und wählen Sie dann **WordPress** aus, um das WordPress-Importer-Plug-In zu verwenden.

5.  Klicken Sie auf der Seite **WordPress importieren** auf **Datei wählen**. Wechseln Sie zu aus Ihrer vorhandenen Wordpress-Website exportierten WXR-Datei, und klicken Sie auf **Datei hochladen** und **Importieren**.

6.  Klicken Sie auf **Senden**. Sie werden informiert, wenn der Import erfolgreich war.

7.  Nachdem Sie alle diese Schritte ausgeführt haben, starten Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] im Dashboard Ihrer Website die Website neu.

Nach dem Importieren der Website müssen Sie ggf. die folgenden Schritte ausführen, um nicht in der Importdatei enthaltene Einstellungen zu aktivieren.

| Bei Verwendung von    | Maßnahme                                                                                                                                                                                       |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Permalinks**        | Im WordPress-Dashboard der neuen Website klicken Sie auf **Einstellungen** -\> **Permalinks** und aktualisieren anschließend die Permalinks-Struktur.                                          |
| **Bild-/Medienlinks** | Zum Anpassen von Links an den neuen Speicherort verwenden Sie das [Plug-In "Velvet Blues Update URLs"][Plug-In "Velvet Blues Update URLs"], ein Suchen-und-Ersetzen-Tool und führen diese Schritte manuell in der Datenbank aus. |
| **Designs**           | Wechseln Sie zu **Darstellung** -\> **Design**, und aktualisieren Sie das Websitedesign den Anforderungen entsprechend.                                                                        |
| **Menüs**             | Falls Ihr Design Menüs unterstützt, ist in Links zu Ihrer Homepage ggf. das alte Unterverzeichnis eingebettet. Wechseln Sie zu **Darstellung** -\> **Menüs**, und aktualisieren Sie sie.       |

#### Die Sicherungs- und Wiederherstellungsmethode

1.  Sichern Sie Ihre vorhandene WordPress-Website mithilfe der Informationen auf der [WordPress-Seite zu Sicherungen][Sichern Sie Ihre Website].

2.  Sichern Sie Ihre vorhandene Datenbank anhand der Informationen unter [Sichern Ihrer Datenbank][Datenbank].

3.  Erstellen Sie eine neue Datenbank, und stellen Sie die Sicherung wieder her.

    1.  Erwerben Sie im [Azure Shop][ClearDB im Azure Shop] eine neue Datenbank, oder richten Sie auf einem virtuellen [Windows][Windows]- oder [Linux][Linux]-Computer eine MySQL-Datenbank ein.

    2.  Stellen Sie mit einem MySQL-Client wie [MySQL Workbench][MySQL Workbench] eine Verbindung mit der neuen Datenbank her, und importieren Sie Ihre WordPress-Datenbank.

    3.  Ändern Sie in der Datenbank die Domäneneinträge entsprechend Ihrer neuen Azure-Websitedomäne. Beispiel: mywordpress.azurewebsites.net. Verwenden Sie das Skript [Search and Replace for WordPress Databases][Search and Replace for WordPress Databases], um alle Vorkommen sicher zu ändern.

4.  Erstellen Sie eine neue Website, und veröffentlichen Sie die WordPress-Sicherung.

    1.  Erstellen Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] über **Neu** -\> **Website** -\> **Benutzerdefiniert erstellen** eine neue Website mit einer Datenbank. Hierdurch wird eine leere Website erstellt.

    2.  Suchen Sie in Ihrer WordPress-Sicherung die Datei **wp-config.php**, und öffnen Sie sie in einem Editor. Ersetzen Sie die folgenden Einträge durch die Informationen für Ihre neue MySQL-Datenbank.

        -   **DB\_NAME** - Der Name der Datenbank

        -   **DB\_USER** - Der Benutzername für den Zugriff auf die Datenbank

        -   **DB\_PASSWORD** - Das Kennwort des Benutzers

        Nach Ändern dieser Einträge speichern und schließen Sie die Datei **wp-config.php**.

    3.  Befolgen Sie die Informationen unter [Bereitstellen einer Azure-Website][Bereitstellen einer Azure-Website], um die gewünschte Bereitstellungsmethode zu aktivieren, und stellen Sie anschließend Ihre WordPress-Sicherung in Ihrer Azure-Website bereit.

5.  Nach der Bereitstellung der WordPress-Website sollten Sie auf die neue Website über die URL "\*.azurewebsite.net" zugreifen können.

### Konfigurieren Ihrer Website

Befolgen Sie nach dem Erstellen oder Migrieren der WordPress-Website die folgenden Informationen zum Verbessern der Leistung und Ermöglichen zusätzlicher Funktionalität.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Maßnahme</th>
<th align="left">Option</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><strong>Festlegen von Modus und Größe der Website und Aktivieren der Skalierung</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-scale/">Skalieren von Websites</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Aktivieren dauerhafter Datenbankverbindungen</strong>
<p>WordPress arbeitet standardmäßig nicht mit dauerhaften Datenbankverbindungen, was bewirken kann, dass Ihre Verbindung mit der Datenbank nach mehreren Verbindungen gedrosselt wird.</p></td>
<td align="left"><ol>
<li><p>Bearbeiten Sie die Datei <strong>wp-includes/wp-db.php</strong>.</p></li>
<li><p>Suchen Sie die folgende Zeile.</p>
<p><code data-inline="1">$this-&gt;dbh = mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags );</code></p></li>
<li><p>Ersetzen Sie die vorherige Zeile durch den folgenden Code.</p>
<p><code>$this-&gt;dbh = mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); if ( false !== $error_reporting ) { /br/&gt;  error_reporting( $error_reporting ); } </code></p></li>
<li><p>Suchen Sie die folgende Zeile.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_connect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $new_link, $client_flags ); </code></p></li>
<li><p>Ersetzen Sie die obige Zeile durch den folgenden Code.</p>
<p><code data-inline="1">$this-&gt;dbh = @mysql_pconnect( $this-&gt;dbhost, $this-&gt;dbuser, $this-&gt;dbpassword, $client_flags ); </code></p></li>
<li><p>Speichern Sie die Datei <strong>wp-includes/wp-db.php</strong>, und stellen Sie die Website erneut bereit.</p></li>
</ol>
<div class="wa-note"><span class="wa-icon-bulb"></span><h5><a name="note"></a>HINWEIS:</h5><p>Diese &Auml;nderungen werden ggf. &uuml;berschrieben, wenn WordPress aktualisiert wird.</p><p>WordPress nutzt standardm&auml;&szlig;ig automatische Updates, was durch Bearbeiten der Datei <strong>wp-config.php</strong> und Hinzuf&uuml;gen von <code data-inline="1">define ( 'WP_AUTO_UPDATE_CORE', false );</code> deaktiviert werden kann.</p><p>Eine weitere M&ouml;glichkeit des Umgangs mit Updates ist das Verwenden eines Webauftrags, der die Datei <strong>wp-db.php</strong> &uuml;berwacht und die obigen &Auml;nderungen bei jeder Aktualisierung der Datei durchf&uuml;hrt. Unter <a href="http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx">Einf&uuml;hrung in Webauftr&auml;ge</a> finden Sie weitere Informationen.</p></div></td>
</tr>
<tr class="odd">
<td align="left"><strong>Verbessern der Leistung</strong></td>
<td align="left"><ul>
<li><p>Das <a href="http://blogs.msdn.com/b/windowsazure/archive/2013/11/18/disabling-arr-s-instance-affinity-in-windows-azure-web-sites.aspx">Deaktivieren des ARR-Cookies</a> kann die Leistung verbessern, wenn WordPress auf mehreren Website-Instanzen ausgeführt wird.</p></li>
<li><p>Aktivieren Sie das Zwischenspeichern. Der <a href="http://msdn.microsoft.com/de-de/library/azure/dn690470.aspx">Redis-Cache</a> (Preview) kann mit dem WordPress-Plug-In <a href="https://wordpress.org/plugins/redis-object-cache/">Redis Object Cache</a> verwendet werden. Oder nutzen Sie eines der anderen Cacheangebote im <a href="http://azure.microsoft.com/de-de/gallery/store/">Azure Shop</a>.</p></li>
<li><p><a href="http://ruslany.net/2010/03/make-wordpress-faster-on-iis-with-wincache-1-1/">Beschleunigen von WordPress mit Wincache</a> - Wincache ist für Websites standardmäßig aktiviert.</p></li>
<li><p><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-scale/">Skalieren Sie Ihre Azure-Website</a>, und verwenden Sie das <a href="http://www.cleardb.com/developers/cdbr/introduction">ClearDB-Hochverfügbarkeitsrouting</a> oder <a href="http://www.mysql.com/products/cluster/">MySQL Cluster CGE</a>.</p></li>
</ul></td>
</tr>
<tr class="even">
<td align="left"><strong>Verwenden Sie die BLOB-Speicherung.</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/de-de/documentation/articles/storage-create-storage-account/">Erstellen eines Azure-Speicherkontos</a></p></li>
<li><p>Unter <a href="http://azure.microsoft.com/de-de/documentation/articles/cdn-how-to-use/">Verwenden von CDN für Azure</a> erfahren Sie, wie Sie in BLOBs gespeicherte Daten geografisch verteilen.</p></li>
<li><p>Installieren und konfigurieren Sie das Plug-In <a href="https://wordpress.org/plugins/windows-azure-storage/">Azure Storage for WordPress</a>.</p>
<p>Detaillierte Informationen zu Einrichtung und Konfiguration des Plug-Ins finden Sie im <a href="http://plugins.svn.wordpress.org/windows-azure-storage/trunk/UserGuide.docx">Benutzerhandbuch</a>.</p></li>
</ol></td>
</tr>
<tr class="odd">
<td align="left"><strong>Aktivieren von E-Mail</strong></td>
<td align="left"><ol>
<li><p><a href="http://azure.microsoft.com/de-de/gallery/store/sendgrid/sendgrid-azure/">Aktivieren von SendGrid über den Azure Shop</a></p></li>
<li><p><a href="http://wordpress.org/plugins/sendgrid-email-delivery-simplified/">Installieren des SendGrid-Plug-Ins für WordPress</a></p></li>
</ol></td>
</tr>
<tr class="even">
<td align="left"><strong>Konfigurieren eines benutzerdefinierten Domänennamens</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-custom-domain-name/">Verwenden eines benutzerdefinierten Domänennamens für eine Azure-Website</a></td>
</tr>
<tr class="odd">
<td align="left"><strong>Aktivieren von HTTPS für einen benutzerdefinierten Domänennamen</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-configure-ssl-certificate/">Verwenden von HTTPS für eine Azure-Website</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Lastenausgleich oder geografische Verteilung für Ihre Website</strong></td>
<td align="left"><a href="http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/">Leiten Sie Datenverkehr mit Azure Traffic Manager weiter</a>. Wenn Sie eine benutzerdefinierte Domäne verwenden, finden Sie unter <a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-custom-domain-name/">Verwenden eines benutzerdefinierten Domänennamens für eine Azure-Website</a> Informationen zum Verwenden von Traffic Manager mit benutzerdefinierten Domänennamen.</td>
</tr>
<tr class="odd">
<td align="left"><strong>Aktivieren automatisierter Sicherungen von Websites</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/">Sicherungen von Azure-Websites</a></td>
</tr>
<tr class="even">
<td align="left"><strong>Aktivieren der Diagnoseprotokollierung</strong></td>
<td align="left"><a href="http://azure.microsoft.com/de-de/documentation/articles/web-sites-enable-diagnostic-log/">Aktivieren der Diagnoseprotokollierung für Websites</a></td>
</tr>
</tbody>
</table>

## [][]Zusätzliche Ressourcen

-   [WordPress-Optimierung][WordPress-Optimierung]

-   [Konvertieren einer WordPress-Website in eine Multisite][Konvertieren einer WordPress-Website in eine Multisite]

-   [ClearDB-Upgrade-Assistent für Azure][ClearDB-Upgrade-Assistent für Azure]

-   [Hosten von WordPress in einem Unterordner Ihrer Azure-Website][Hosten von WordPress in einem Unterordner Ihrer Azure-Website]

-   [Schrittweise Anleitung: Erstellen einer Website mithilfe von Azure][Schrittweise Anleitung: Erstellen einer Website mithilfe von Azure]

-   [Hosten Ihres vorhandenen WordPress-Blogs in Azure][Hosten Ihres vorhandenen WordPress-Blogs in Azure]

-   [Aktivieren von Permalinks in WordPress][Aktivieren von Permalinks in WordPress]

-   [Migrieren und Ausführen Ihres WordPress-Blogs auf Azure-Websites][Migrieren und Ausführen Ihres WordPress-Blogs auf Azure-Websites]

-   [Kostenloses Ausführen von WordPress auf Azure-Websites][Kostenloses Ausführen von WordPress auf Azure-Websites]

-   [WordPress in Azure in maximal 2 Minuten][WordPress in Azure in maximal 2 Minuten]

-   [Verschieben eines WordPress-Blogs in Azure - Teil 1: Erstellen eines WordPress-Blogs in Azure][Verschieben eines WordPress-Blogs in Azure - Teil 1: Erstellen eines WordPress-Blogs in Azure]

-   [Verschieben eines WordPress-Blogs in Azure - Teil 2: Übertragen Ihrer Inhalte][Verschieben eines WordPress-Blogs in Azure - Teil 2: Übertragen Ihrer Inhalte]

-   [Verschieben eines WordPress-Blogs in Azure - Teil 3: Einrichten Ihrer benutzerdefinierten Domäne][Verschieben eines WordPress-Blogs in Azure - Teil 3: Einrichten Ihrer benutzerdefinierten Domäne]

-   [Verschieben eines WordPress-Blogs in Azure - Teil 4: Permalinks und Regeln zum Umschreiben von URLs][Verschieben eines WordPress-Blogs in Azure - Teil 4: Permalinks und Regeln zum Umschreiben von URLs]

-   [Verschieben eines WordPress-Blogs in Azure - Teil 5: Verschieben aus einem Unter- in den Stammordner][Verschieben eines WordPress-Blogs in Azure - Teil 5: Verschieben aus einem Unter- in den Stammordner]

-   [Einrichten einer WordPress-Website unter Ihrem Azure-Konto][Einrichten einer WordPress-Website unter Ihrem Azure-Konto]

-   [Unterstützen von WordPress in Azure][Unterstützen von WordPress in Azure]

-   [Tipps für WordPress in Azure][Tipps für WordPress in Azure]

  [WordPress]: http://www.microsoft.com/web/wordpress
  [Office]: http://blogs.office.com/
  [Bing]: http://blogs.bing.com/
  [Architektur und Planung]: #planning
  [Anleitungen]: #howto
  [Weitere Ressourcen]: #resources
  [ClearDB im Azure Shop]: http://www.cleardb.com/store/azure
  [Windows]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-mysql-windows-server-2008r2/
  [Linux]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-linux-mysql-use-opensuse/
  [ClearDB-Webseite mit den Preisen]: http://www.cleardb.com/pricing.view
  [PHP-Versionen 5.3, 5.4 und 5.5]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-php-configure/
  [Eine Azure-Website und MySQL-Datenbank, die in einer einzelnen Azure-Region gehostet werden]: ./media/web-sites-enterprise-wordpress/basic-diagram.png
  [Traffic Manager]: http://azure.microsoft.com/blog/2014/03/27/azure-traffic-manager-can-now-integrate-with-azure-web-sites/
  [Eine in mehreren Regionen gehostete Azure-Website mit CDBR-Hochverfügbarkeitsrouter zum regionsübergreifenden Weiterleiten an MySQL]: ./media/web-sites-enterprise-wordpress/multi-region-diagram.png
  [CDBR-Hochverfügbarkeitsrouters]: http://www.cleardb.com/developers/cdbr/introduction
  [MySQL Cluster CGE]: http://www.mysql.com/products/cluster/
  [Redis-Cache]: http://msdn.microsoft.com/de-de/library/azure/dn690470.aspx
  [Memcache-Cloud]: http://azure.microsoft.com/de-de/gallery/store/garantiadata/memcached/
  [MemCachier]: http://azure.microsoft.com/de-de/gallery/store/memcachier/memcachier/
  [Azure Shop]: http://azure.microsoft.com/de-de/gallery/store/
  [eine in mehreren Regionen gehostete Azure-Website mit CDBR-Hochverfügbarkeitsrouter für MySQL, Managed Cache, BLOB-Speicher und CDN]: ./media/web-sites-enterprise-wordpress/performance-diagram.png
  [Content Distribution Network (CDN)]: http://azure.microsoft.com/de-de/documentation/articles/cdn-how-to-use/
  [Bereitstellen einer Azure-Website]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-deploy/
  [Sichern von Azure-Websites]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-backup/
  [Wiederherstellen einer Azure-Website]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-restore/
  [Details zu den Preisen, einschließlich Funktionen verschiedener Größen und Modi von Websites]: https://azure.microsoft.com/de-de/pricing/details/web-sites/
  [Skalieren einer Azure-Website]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-scale/
  [WordPress-Export]: http://en.support.wordpress.com/export/
  [WordPress-Import-Plug-Ins]: http://wordpress.org/plugins/wordpress-importer/
  [Sichern Sie Ihre Website]: http://codex.wordpress.org/WordPress_Backups
  [Datenbank]: http://codex.wordpress.org/Backing_Up_Your_Database
  [Erstellen einer WordPress-Website über die Galerie in Azure]:  "http://azure.microsoft.com/de-de/documentation/articles/web-sites-php-web-site-gallery/"
  [Migrieren einer vorhandenen WordPress-Website]: #migrate
  [Erstellen einer neuen WordPress-Website]: #create
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Plug-In "Velvet Blues Update URLs"]: https://wordpress.org/plugins/velvet-blues-update-urls/
  [MySQL Workbench]: http://www.mysql.com/products/workbench/
  [Search and Replace for WordPress Databases]: http://interconnectit.com/124/search-and-replace-for-wordpress-databases/
  []: resources
  [WordPress-Optimierung]: http://codex.wordpress.org/WordPress_Optimization
  [Konvertieren einer WordPress-Website in eine Multisite]: http://azure.microsoft.com/de-de/documentation/articles/web-sites-php-convert-wordpress-multisite/
  [ClearDB-Upgrade-Assistent für Azure]: http://www.cleardb.com/store/azure/upgrade
  [Hosten von WordPress in einem Unterordner Ihrer Azure-Website]: http://blogs.msdn.com/b/webapps/archive/2013/02/13/hosting-wordpress-in-a-subfolder-of-your-windows-azure-web-site.aspx
  [Schrittweise Anleitung: Erstellen einer Website mithilfe von Azure]: http://blogs.technet.com/b/blainbar/archive/2013/08/07/article-create-a-wordpress-site-using-windows-azure-read-on.aspx
  [Hosten Ihres vorhandenen WordPress-Blogs in Azure]: http://blogs.msdn.com/b/msgulfcommunity/archive/2013/08/26/migrating-a-self-hosted-wordpress-blog-to-windows-azure.aspx
  [Aktivieren von Permalinks in WordPress]: http://www.iis.net/learn/extensions/url-rewrite-module/enabling-pretty-permalinks-in-wordpress
  [Migrieren und Ausführen Ihres WordPress-Blogs auf Azure-Websites]: http://www.kefalidis.me/2012/06/how-to-migrate-and-run-your-wordpress-blog-on-windows-azure-websites/
  [Kostenloses Ausführen von WordPress auf Azure-Websites]: http://architects.dzone.com/articles/how-run-wordpress-azure
  [WordPress in Azure in maximal 2 Minuten]: http://www.sitepoint.com/wordpress-windows-azure-2-minutes-less/
  [Verschieben eines WordPress-Blogs in Azure - Teil 1: Erstellen eines WordPress-Blogs in Azure]: http://www.davebost.com/2013/07/10/moving-a-wordpress-blog-to-windows-azure-part-1
  [Verschieben eines WordPress-Blogs in Azure - Teil 2: Übertragen Ihrer Inhalte]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-transferring-your-content
  [Verschieben eines WordPress-Blogs in Azure - Teil 3: Einrichten Ihrer benutzerdefinierten Domäne]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-3-setting-up-your-custom-domain
  [Verschieben eines WordPress-Blogs in Azure - Teil 4: Permalinks und Regeln zum Umschreiben von URLs]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-4-pretty-permalinks-and-url-rewrite-rules
  [Verschieben eines WordPress-Blogs in Azure - Teil 5: Verschieben aus einem Unter- in den Stammordner]: http://www.davebost.com/2013/07/11/moving-a-wordpress-blog-to-windows-azure-part-5-moving-from-a-subfolder-to-the-root
  [Einrichten einer WordPress-Website unter Ihrem Azure-Konto]: http://www.itexperience.net/2014/01/20/how-to-set-up-a-wordpress-website-in-your-windows-azure-account/
  [Unterstützen von WordPress in Azure]: http://www.johnpapa.net/wordpress-on-azure/
  [Tipps für WordPress in Azure]: http://www.johnpapa.net/azurecleardbmysql/
