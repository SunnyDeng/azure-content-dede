<properties
   pageTitle="Effektive Verwendung der DevOps-Umgebungen für Ihre Web-App"
   description="Hier erfahren Sie, wie Sie Bereitstellungsslots zum Einrichten und Verwalten mehrerer Entwicklungsumgebungen für Ihre Anwendung verwenden."
   services="app-service\web"
   documentationCenter=""
   authors="sunbuild"
   manager="yochayk"
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="web"
   ms.date="02/26/2016"
   ms.author="sumuth"/>

# Effektive Verwendung der DevOps-Umgebungen für Ihre Web-Apps

In diesem Artikel werden die Einrichtung und Verwaltung von Webanwendungsbereitstellungen für mehrere Versionen Ihrer Anwendung (z. B. Entwicklung, Staging, Qualitätssicherung und Produktion) erläutert. Jede Version der Anwendung kann als Entwicklungsumgebung für bestimmte Anforderungen innerhalb Ihres Bereitstellungsprozesses betrachtet werden. Die Qualitätssicherungsumgebung (QA) kann z. B. vom Entwicklerteam zum Testen der Qualität der Anwendung verwendet werden, bevor Sie die Änderungen mithilfe von Push in die Produktionsumgebung übertragen. Das Einrichten mehrerer Entwicklungsumgebungen kann eine Herausforderung darstellen, da Sie die Ressourcen (Compute, Web-App, Datenbank, Cache usw.) in diesem Umgebungen nachverfolgen und verwalten und Inhalte umgebungsübergreifend bereitstellen müssen.

## Einrichten einer Nichtproduktionsumgebung (Staging, Entwicklung, QA)
Nachdem Sie eine Produktions-Web-App erstellt und in Betrieb genommen haben, besteht der nächste Schritt darin, eine Nichtproduktionsumgebung zu erstellen. Zur Verwendung von Bereitstellungsslots müssen Sie die App im App Service-Planmodus **Standard** oder **Premium** ausführen. Bereitstellungsslots sind Live-Web-Apps mit eigenen Hostnamen. Elemente für Web-App-Inhalte und -Konfigurationen können zwischen zwei Bereitstellungsslots, einschließlich des Produktionsslots, ausgetauscht werden. Die Bereitstellung von Anwendungen in einem Bereitstellungsslot hat die folgenden Vorteile:

1. Sie können Web-App-Änderungen in einem Stagingbereitstellungsslot überprüfen, bevor Sie die Web-App in den Produktionsslot überführen.
2. Indem Sie eine Web-App zuerst in einem Slot bereitstellen und sie dann in den Produktionsslot überführen, stellen Sie sicher, dass alle Instanzen erst nach einer Anlaufzeit in den Produktionsslot übernommen werden. Dadurch vermeiden Sie Ausfallzeit bei der Bereitstellung der Web-App. Die Datenverkehrsweiterleitung ist nahtlos, und es werden keine Anfragen aufgrund von Austauschvorgängen gelöscht. Dieser gesamte Wortflow kann durch Konfigurieren von [Auto Swap](web-sites-staged-publishing.md#configure-auto-swap-for-your-web-app) automatisiert werden, wenn keine Überprüfung vor dem Austauschen erforderlich ist.
3. Nach einem Austausch befindet sich im Slot mit der zuvor bereitgestellten Web-App jetzt die vorherige Produktions-Web-App. Wenn die in den Produktionsslot überführten Änderungen nicht Ihren Erwartungen entsprechen, können Sie denselben Austausch sofort noch einmal vornehmen, um die „letzte als gut befundene Web-App“ wiederherzustellen.

Informationen zum Einrichten eines Stagingbereitstellungsslots finden Sie unter [Einrichten von Stagingumgebungen für Web-Apps in Azure App Service](web-sites-staged-publishing.md) . Jede Umgebung sollte über einen eigenen Satz von Ressourcen verfügen. Wenn Ihre Web-App z. B. eine Datenbank verwendet, sollten für die Produktions-Web-App und die Staging-Web-App verschiedene Datenbanken verwendet werden. Fügen Sie zum Einrichten Ihrer Staging- und Entwicklungsumgebung Staging- und Entwicklungsumgebungsressourcen wie z. B. Datenbank, Speicher oder Cache hinzu.

## Beispiele für die Verwendung mehrerer Entwicklungsumgebungen

Für jedes Projekt ist eine Quellcodeverwaltung mit mindestens zwei Umgebungen erforderlich – eine Entwicklungs- und eine Produktionsumgebung. Bei der Verwendung von Content Management-Systemen (CMS), Anwendungsframeworks usw. können jedoch Probleme auftreten, wenn die Anwendung dieses Szenario nicht standardmäßig unterstützt. Dies gilt für einige der beliebten Frameworks, die im Folgenden erläutert werden. Wenn Sie mit einem CMS oder mit Frameworks arbeiten, sind viele Fragen zu klären, z. B.:

1. Wie nehme ich die Unterteilung in unterschiedliche Umgebungen vor?
2. Welche Dateien kann ich ändern, ohne dass sich dies auf Framework-Versionsaktualisierungen auswirkt?
3. Wie verwalte ich die Konfiguration pro Umgebung?
4. Wie verwalte ich Versionsaktualisierungen von Modulen/Plug-Ins und Kern-Frameworks?

Es gibt viele Methoden zum Einrichten mehrerer Umgebungen für Ihr Projekt. In den folgenden Beispielen wird nur eine dieser Methoden für die jeweiligen Anwendungen erläutert.

### WordPress
In diesem Abschnitt erfahren Sie, wie Sie einen Bereitstellungsworkflow mit Slots für WordPress einrichten. Wie die meisten CMS-Lösungen unterstützt WordPress die Verwendung mehrerer Entwicklungsumgebungen nicht standardmäßig. App Service-Web-Apps verfügen über einige Funktionen, die das Speichern von Konfigurationseinstellungen außerhalb des Codes vereinfachen.

Vor dem Erstellen eines Stagingslots richten Sie den Anwendungscode zur Unterstützung mehrerer Umgebungen ein. Um mehrere Umgebungen in WordPress zu unterstützen, müssen Sie `wp-config.php` in Ihrer lokalen Entwicklungs-Web-App bearbeiten und den folgenden Code am Anfang der Datei hinzufügen. Dadurch kann die Anwendung basierend auf der ausgewählten Umgebung die richtige Konfiguration auswählen.


	// Support multiple environments
	// set the config file based on current environment
	/**/
	if (strpos(filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING),'localhost') !== false) {
	    // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	      //single file for all azure development environments
	      $config_file = 'config/wp-config.azure.php';
	}
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}



Erstellen Sie im Web-App-Stammverzeichnis einen Ordner namens `config`, und fügen Sie die zwei Dateien `wp-config.azure.php` und `wp-config.local.php` hinzu, die die Azure-Umgebung bzw. Ihre lokale Umgebung darstellen.

Kopieren Sie den folgenden Code in `wp-config.local.php`:

```
	
	<?php
	
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', 'yourdatabasename');
	
	/** MySQL database username */
	define('DB_USER', 'yourdbuser');
	
	/** MySQL database password */
	define('DB_PASSWORD', 'yourpassword');
	
	/** MySQL hostname */
	define('DB_HOST', 'localhost');
	/**
	 * For developers: WordPress debugging mode.
	 * * Change this to true to enable the display of notices during development.
	 * It is strongly recommended that plugin and theme developers use WP_DEBUG
	 * in their development environments.
	 */
	define('WP_DEBUG', true);
	
	//Security key settings
	define('AUTH_KEY',         'put your unique phrase here');
	define('SECURE_AUTH_KEY',  'put your unique phrase here');
	define('LOGGED_IN_KEY',    'put your unique phrase here');
	define('NONCE_KEY',        'put your unique phrase here');
	define('AUTH_SALT',        'put your unique phrase here');
	define('SECURE_AUTH_SALT', 'put your unique phrase here');
	define('LOGGED_IN_SALT',   'put your unique phrase here');
	define('NONCE_SALT',       'put your unique phrase here');
	
	/**
	 * WordPress Database Table prefix.
	 *
	 * You can have multiple installations in one database if you give each a unique
	 * prefix. Only numbers, letters, and underscores please!
	 */
	$table_prefix  = 'wp_';
```

Durch das Festlegen der obigen Sicherheitsschlüssel können Sie verhindern, dass Ihre Web-App gehackt wird. Verwenden Sie daher eindeutige Werte. Wenn Sie die Zeichenfolge für die oben genannten Sicherheitsschlüssel generieren müssen, können Sie den automatischen Generator unter diesem [Link](https://api.wordpress.org/secret-key/1.1/salt) zum Erstellen neuer Schlüssel/Werte verwenden.

Kopieren Sie den folgenden Code in `wp-config.azure.php`:


```

	<?php
	// MySQL settings
	/** The name of the database for WordPress */
	
	define('DB_NAME', getenv('DB_NAME'));
	
	/** MySQL database username */
	define('DB_USER', getenv('DB_USER'));
	
	/** MySQL database password */
	define('DB_PASSWORD', getenv('DB_PASSWORD'));
	
	/** MySQL hostname */
	define('DB_HOST', getenv('DB_HOST'));
	
	/**
	* For developers: WordPress debugging mode.
	*
	* Change this to true to enable the display of notices during development.
	* It is strongly recommended that plugin and theme developers use WP_DEBUG
	* in their development environments.
	* Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
	* do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
	* with WP_DEBUG_LOG so that errors are not displayed on the page */
	
	*/
	define('WP_DEBUG', getenv('WP_DEBUG'));
	define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
	define('WP_DEBUG_DISPLAY',false);
	
	//Security key settings
	/** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
	define('AUTH_KEY' ,getenv('DB_AUTH_KEY'));
	define('SECURE_AUTH_KEY',  getenv('DB_SECURE_AUTH_KEY'));
	define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
	define('NONCE_KEY', getenv('DB_NONCE_KEY'));
	define('AUTH_SALT',  getenv('DB_AUTH_SALT'));
	define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
	define('LOGGED_IN_SALT',   getenv('DB_LOGGED_IN_SALT'));
	define('NONCE_SALT',   getenv('DB_NONCE_SALT'));
	
	/**
	* WordPress Database Table prefix.
	*
	* You can have multiple installations in one database if you give each a unique
	* prefix. Only numbers, letters, and underscores please!
	*/
	$table_prefix  = getenv('DB_PREFIX');
```

#### Verwenden von relativen Pfaden
Zuletzt müssen Sie WordPress noch die Verwendung relativer Pfade ermöglichen. WordPress speichert URL-Informationen in der Datenbank. Dies erschwert das Verschieben von Inhalten zwischen Umgebungen, da Sie die Datenbank jedes Mal aktualisieren müssen, wenn Sie von der lokalen zur Staging- oder von der Staging- zur Produktionsumgebung wechseln. Zur Vermeidung von Problemen, die durch das Bereitstellen der Datenbank bei jeder umgebungsübergreifenden Bereitstellung entstehen können, verwenden Sie das [Plug-In für relative Stammlinks](https://wordpress.org/plugins/root-relative-urls/), das über das WordPress-Administratordashboard installiert oder [hier](https://downloads.wordpress.org/plugin/root-relative-urls.zip) manuell heruntergeladen werden kann.

Fügen Sie Ihrer Datei `wp-config.php` die folgenden Einträge vor dem Kommentar `That's all, stop editing!` hinzu:

```

    define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

Aktivieren Sie das Plug-In über das Menü `Plugins` im WordPress-Administratordashboard. Speichern Sie die Permalink-Einstellungen für die WordPress-App.

#### Die fertige Datei `wp-config.php`
Aktualisierungen des WordPress-Kerns haben keine Auswirkung auf Ihre Dateien `wp-config.php`, `wp-config.azure.php` und `wp-config.local.php`. Am Ende sieht die Datei `wp-config.php` wie folgt aus:

```

	<?php
	/**
	 * The base configurations of the WordPress.
	 *
	 * This file has the following configurations: MySQL settings, Table Prefix,
	 * Secret Keys, and ABSPATH. You can find more information by visiting
	 *
	 * Codex page. You can get the MySQL settings from your web host.
	 *
	 * This file is used by the wp-config.php creation script during the
	 * installation. You don't have to use the web web app, you can just copy this file
	 * to "wp-config.php" and fill in the values.
	 *
	 * @package WordPress
	 */
	
	// Support multiple environments
	// set the config file based on current environment
	if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
	    $config_file = 'config/wp-config.local.php';
	}
	elseif  ((strpos(getenv('WP_ENV'),'stage') !== false) ||  (strpos(getenv('WP_ENV'),'prod' )!== false )){
	    $config_file = 'config/wp-config.azure.php';
	}
	
	
	$path = dirname(__FILE__) . '/';
	if (file_exists($path . $config_file)) {
	    // include the config file if it exists, otherwise WP is going to fail
	    require_once $path . $config_file;
	}
	
	/** Database Charset to use in creating database tables. */
	define('DB_CHARSET', 'utf8');
	
	/** The Database Collate type. Don't change this if in doubt. */
	define('DB_COLLATE', '');
	
	
	/* That's all, stop editing! Happy blogging. */
	
	define('WP_HOME', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_SITEURL', 'http://' . filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	define('WP_CONTENT_URL', '/wp-content');
	define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
	
	/** Absolute path to the WordPress directory. */
	if ( !defined('ABSPATH') )
		define('ABSPATH', dirname(__FILE__) . '/');
	
	/** Sets up WordPress vars and included files. */
	require_once(ABSPATH . 'wp-settings.php');
```

#### Einrichten einer Stagingumgebung
Wenn Sie bereits über eine auf Azure Web ausgeführte WordPress-Web-App verfügen, melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und wechseln Sie zu Ihrer WordPress-Web-App. Andernfalls können Sie die App über den Marketplace erstellen. Klicken Sie [hier](web-sites-php-web-site-gallery.md), um weitere Informationen zu erhalten. Klicken Sie auf **Einstellungen** -> **Bereitstellungsslots** -> **Hinzufügen**, um einen Bereitstellungsslot mit dem Namen „stage“ zu erstellen. Ein Bereitstellungsslot ist eine weitere Webanwendung, die die gleichen Ressourcen wie die zuvor erstellte primäre Web-App nutzt.

![Erstellen des Bereitstellungslots „stage“](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

Fügen Sie der Ressourcengruppe `wordpressapp-group` eine weitere MySQL-Datenbank hinzu, z. B. `wordpress-stage-db`.

 ![Hinzufügen einer MySQL-Datenbank zur Ressourcengruppe](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

Aktualisieren Sie die Verbindungszeichenfolgen für Ihren Bereitstellungsslot „stage“, um auf die neu erstellte Datenbank `wordpress-stage-db` zu verweisen. Beachten Sie, dass die Produktions-Web-App `wordpressprodapp` und die Staging-Web-App `wordpressprodapp-stage` auf unterschiedliche Datenbanken verweisen müssen.

#### Konfigurieren umgebungsspezifischer App-Einstellungen
Entwickler können Schlüssel-Wert-Zeichenfolgenpaare in Azure mit einer Web-App namens App-Einstellungen als Teil der zugehörigen Konfigurationsinformationen speichern. App Service-Web-Apps rufen diese Werte zur Laufzeit automatisch für Sie ab und machen sie für den in Ihrer Web-App ausgeführten Code verfügbar. Im Hinblick auf die Sicherheit ist dies ein Vorteil, da vertrauliche Informationen wie Datenbank-Verbindungszeichenfolgen mit Kennwörtern nie als Klartext in einer Datei wie `wp-config.php` angezeigt werden sollten.

Der folgende Prozess ist bei Aktualisierungen nützlich, da sowohl Datei- als auch Datenbankänderungen für die WordPress-App eingeschlossen werden:

- WordPress-Versionsupgrade
- Hinzufügen neuer Plug-Ins oder Bearbeiten bzw. Aktualisieren von Plug-Ins
- Hinzufügen neuer Designs oder Bearbeiten bzw. Aktualisieren von Designs

Konfigurieren Sie App-Einstellungen für Folgendes:

- Datenbankinformationen
- Aktivieren/Deaktivieren der WordPress-Protokollierung
- WordPress-Sicherheitseinstellungen

![App-Einstellungen für die WordPress-Web-App](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

Stellen Sie sicher, dass Sie die folgenden App-Einstellungen für die Produktions-Web-App und den Slot „stage“ hinzugefügt haben. Beachten Sie, dass die Produktions-Web-App und die Staging-Web-App unterschiedliche Datenbanken verwenden. Deaktivieren Sie das Kontrollkästchen **Sloteinstellung** für alle Einstellungsparameter außer WP\_ENV. Dadurch wird die Konfiguration für Ihre Web-App zusammen mit Dateiinhalten und der Datenbank ausgetauscht. Wenn **Sloteinstellung** **aktiviert** ist, werden die App-Einstellungen und die Verbindungszeichenfolgen-Konfiguration der Web-App bei einem Austausch NICHT umgebungsübergreifend verschoben. Falls Datenbankänderungen vorliegen, wird die Produktions-Web-App dadurch also nicht unterbrochen.

Stellen Sie die Web-App in der lokalen Entwicklungsumgebung mithilfe von WebMatrix oder Tools Ihrer Wahl (z. B. FTP, Git oder PhpMyAdmin) für die Staging-Web-App und -Datenbank bereit.

![WebMatrix-Veröffentlichungsdialogfeld für die WordPress-Web-App](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

Durchsuchen und testen Sie Ihre Staging-Web-App. Das folgende Beispiel zeigt die Staging-Web-App für ein Szenario, in dem das Design der Web-App aktualisiert werden soll.

![Durchsuchen der Staging-Web-App vor dem Austauschen von Slots](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)


 Wenn alles Ihren Vorstellungen entspricht, klicken Sie in Ihrer Staging-Web-App auf die Schaltfläche **Austauschen**, um den Inhalt in die Produktionsumgebung zu verschieben. In diesem Fall tauschen Sie die Web-App und die Datenbank bei jedem **Austauschvorgang** zwischen den Umgebungen aus.

![Vorschau der Änderungen beim Austauschen für WordPress](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

 > [AZURE.NOTE]
 Im Fall eines Szenarios, in dem Sie nur Dateien (keine Datenbankupdates) mithilfe von Push übertragen müssen, **aktivieren** Sie vor dem Austausch im Azure-Portal auf dem Blatt mit den Web-App-Einstellungen das Kontrollkästchen **Sloteinstellung** für alle datenbankbezogenen *App-Einstellungen* und *Verbindungszeichenfolgen-Einstellungen*. In diesem Fall sollten DB\_NAME, DB\_HOST, DB\_PASSWORD, DB\_USER und die standardmäßige Verbindungszeichenfolgen-Einstellung bei einem **Austausch** nicht in der Vorschau der Änderungen angezeigt werden. Nach Abschluss des Vorgangs **Austauschen** enthält die WordPress-Web-App **NUR** die aktualisierten Dateien.

Hier sehen Sie die WordPress-Produktions-Web-App vor dem Austauschvorgang. ![Produktions-Web-App vor dem Austauschen von Slots](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

Nach dem Austauschvorgang ist das Design Ihrer Produktions-Web-App aktualisiert.

![Produktions-Web-App nach dem Austauschen von Slots](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

Falls Sie ein **Rollback** ausführen müssen, können Sie zu den Einstellungen der Produktions-Web-App wechseln und auf die Schaltfläche **Austauschen** klicken, um für die Web-App und die Datenbank den Produktions- gegen den Stagingslot auszutauschen. Beachten Sie, dass wenn der Vorgang **Austauschen** Datenbankänderungen beinhaltet, Sie bei der nächsten erneuten Bereitstellung in der Staging-Web-App die Datenbankänderungen in der aktuellen Datenbank für Ihre Staging-Web-App bereitstellen müssen. Dabei kann es sich um die vorherige Produktionsdatenbank oder die Stagingdatenbank handeln.

#### Zusammenfassung
Die allgemeinen Schritte des Prozesses für Anwendungen mit einer Datenbank lauten wie folgt:

1. Installieren Sie die Anwendung in Ihrer lokalen Umgebung.
2. Fügen Sie die umgebungsspezifische Konfiguration hinzu (lokale App und Azure-Web-App).
3. Richten Sie Ihre Umgebungen in App Service-Web-Apps ein – Staging und Produktion.
4. Wenn Sie bereits eine auf Azure ausgeführte Produktionsanwendung haben, synchronisieren Sie Ihre Produktionsinhalte (Dateien, Code und Datenbank) mit der lokalen Umgebung und der Stagingumgebung.
5. Entwickeln Sie die Anwendung in Ihrer lokalen Umgebung.
6. Versetzen Sie die Produktions-Web-App in den Wartungs- oder gesperrten Modus, und synchronisieren Sie Datenbankinhalte aus der Produktionsumgebung mit den Staging-und Entwicklungsumgebungen.
7. Stellen Sie die App in der Stagingumgebung bereit, und testen Sie sie.
8. Stellen Sie die App in der Produktionsumgebung bereit.
9. Wiederholen Sie die Schritte 4 bis 6

### Umbraco
In diesem Abschnitt erfahren Sie, wie Umbraco CMS als Grundlage für die übergreifende Bereitstellung in einer Umgebung mit mehreren DevOps ein benutzerdefiniertes Modul verwendet. In diesem Beispiel wird ein anderer Ansatz zum Verwalten mehrerer Entwicklungsumgebungen vorgestellt.

[Umbraco CMS](http://umbraco.com/) ist eine beliebte .NET-CMS-Lösung, die von vielen Entwicklern verwendet wird. Sie bietet das Modul [Courier2](http://umbraco.com/products/more-add-ons/courier-2) für die Bereitstellung von der Entwicklungsumgebung in den Staging- und Produktionsumgebungen. Sie können problemlos eine lokale Entwicklungsumgebung für eine Umbraco CMS-Web-App mit Visual Studio oder WebMatrix erstellen.

1. Klicken Sie [hier](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget), um Informationen zum Erstellen einer Umbraco-Web-App mit Visual Studio anzuzeigen.
2. Klicken Sie [hier](http://umbraco.com/help-and-support/video-tutorials/getting-started/working-with-webmatrix), um Informationen zum Erstellen einer Umbraco-Web-App mit WebMatrix anzuzeigen.

Denken Sie immer daran, den Ordner `install` Ihrer Anwendung zu entfernen. Laden Sie ihn niemals in Staging- oder Produktions-Web-Apps hoch. In diesem Tutorial verwende ich WebMatrix.

#### Einrichten einer Stagingumgebung
Sofern Sie bereits eine Umbraco CMS-Web-App erstellt haben und ausführen, erstellen Sie wie oben erläutert einen Bereitstellungsslot für die Umbraco CMS-Web-App. Andernfalls können Sie die App über den Marketplace erstellen.

Aktualisieren Sie die Verbindungszeichenfolge für Ihren Bereitstellungsslot „stage“, um auf die neu erstellte Datenbank **umbraco-stage-db** zu verweisen. Ihre Produktions-Web-App (umbraositecms-1) und Ihre Staging-Web-App (umbracositecms-1-stage) **MÜSSEN** auf unterschiedliche Datenbanken verweisen.

![Aktualisieren der Verbindungszeichenfolge für die Staging-Web-App mit der neuen Stagingdatenbank](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

Klicken Sie für den Bereitstellungsslot **stage** auf **Veröffentlichungseinstellungen abrufen**. Dadurch wird eine Datei mit Veröffentlichungseinstellungen heruntergeladen, die alle Informationen enthalten, die Visual Studio oder WebMatrix benötigen, um Ihre lokale Entwicklungs-Web-App in der Azure-Web-App zu veröffentlichen.

 ![Abrufen der Veröffentlichungseinstellungen der Staging-Web-App](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)

- Öffnen Sie die lokale Entwicklungs-Web-App in **WebMatrix** oder **Visual Studio**. In diesem Tutorial verwende ich WebMatrix. Als Erstes müssen Sie die Datei mit den Veröffentlichungseinstellungen für Ihre Staging-Web-App importieren.

![Importieren der Veröffentlichungseinstellungen für Umbraco mit WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

- Überprüfen Sie die Änderungen im Dialogfeld, und stellen Sie Ihre lokale Web-App in Ihrer Azure-Web-App *umbracositecms-1-stage* bereit. Wenn Sie Dateien direkt in der Staging-Web-App bereitstellen, werden alle Dateien im Ordner `~/app_data/TEMP/` ausgelassen, da diese beim erstmaligen Start der Staging-Web-App erneut generiert werden. Sie sollten auch die Datei `~/app_data/umbraco.config` auslassen, da diese ebenfalls erneut generiert wird.

![Überprüfen der Veröffentlichungseinstellungen in WebMatrix](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

- Nachdem Sie die lokale Umbraco-Web-App erfolgreich in der Staging-Web-App veröffentlicht haben, wechseln Sie zu Ihrer Staging-Web-App, und führen Sie einige Tests aus, um Probleme auszuschließen.

#### Einrichten des Bereitstellungsmoduls „Courier2“
Mit dem Modul [Courier2](http://umbraco.com/products/more-add-ons/courier-2) können Inhalte, Stylesheets, Entwicklungsmodule usw. einfach per Rechtsklick mithilfe von Push von einer Staging-Web-App in eine Produktions-Web-App übertragen werden. Dies ermöglicht problemlose Bereitstellungen und verringert das Risiko, dass Ihre Produktions-Web-App beim Bereitstellen eines Updates unterbrochen wird. Erwerben Sie eine Lizenz für „Courier2“ für die Domäne `*.azurewebsites.net` und Ihre benutzerdefinierte Domäne (z. B. http://abc.com). Speichern Sie nach dem Kauf der Lizenz die heruntergeladene Lizenz (LIC-Datei) im Ordner `bin`.

![Speichern der Lizenzdatei im Ordner „bin“](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

Laden Sie das Courier2-Paket [hier](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/) herunter. Melden Sie sich bei Ihrer Staging-Web-App an (z. B. http://umbracocms-site-stage.azurewebsites.net/umbraco), klicken Sie auf das Menü **Entwickler**, und wählen Sie **Pakete** aus. Klicken Sie auf **Lokales Paket installieren**.

![Umbraco-Paketinstaller](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

Laden Sie das Courier2-Paket mithilfe des Installers hoch.

![Hochladen des Pakets für das Courier-Modul](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

Zum Konfigurieren müssen Sie die Datei „courier.config“ im Ordner **Config** Ihrer Web-App aktualisieren.

```xml
<!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1.azurewebsites.net</url>
            <user>0</user>
            <!--<login>user@email.com</login> -->
            <!-- <password>user_password</password>-->
           <!-- <passwordEncoding>Clear</passwordEncoding>-->
           </repository>
  </repositories>
 ```

Geben Sie unter `<repositories>` die URL der Produktionswebsite und die Benutzerinformationen ein. Geben Sie bei Verwendung des Standardmitgliedschaftsanbieters für Umbraco die ID des Administratorbenutzers im Abschnitt <user> ein. Wenn Sie einen benutzerdefinierten Mitgliedschaftsanbieter für Umbraco verwenden, verwenden Sie `<login>`,`<password>`, um dem Courier2-Modul mitzuteilen, wie die Verbindung zur Produktionswebsite hergestellt wird. Weitere Informationen finden Sie in der [Siteübersicht](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation) für das Courier-Modul.

Installieren Sie auf ähnliche Weise das Courier-Modul auf der Produktionswebsite, und konfigurieren Sie es in der betreffenden Datei „courier.config“ so, dass es auf die Stage-Web-App zeigt.

```xml
  <!-- Repository connection settings -->
  <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
  <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear:  -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
            <url>http://umbracositecms-1-stage.azurewebsites.net</url>
            <user>0</user>
           </repository>
  </repositories>
```

Klicken Sie im Dashboard der Umbraco CMS-Web-App auf die Registerkarte „Courier2“, und wählen Sie Speicherorte aus. Der Name des Repositorys sollte wie in `courier.config` erwähnt angezeigt werden. Führen Sie diesen Vorgang für die Produktions- und die Staging-Web-App aus.

![Anzeigen des Zielrepositorys der Web-App](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

Jetzt können Sie Inhalte von der Stagingwebsite in der Produktionswebsite bereitstellen. Wechseln Sie zu „Inhalt“, und wählen Sie eine vorhandene Seite aus, oder erstellen Sie eine neue Seite. Ich wähle eine vorhandene Seite aus meiner Web-App aus, deren Titel in **Getting Started – new** geändert wurde, und klicke auf **Speichern und veröffentlichen**.

![Ändern des Titels der Seite und Veröffentlichen](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

Wählen Sie jetzt die geänderte Seite aus, und *klicken Sie mit der rechten Maustaste*, um alle Optionen anzuzeigen. Klicken Sie auf **Courier**, um das Dialogfeld „Bereitstellung“ anzuzeigen. Klicken Sie auf **Bereitstellen**, um die Bereitstellung zu initiieren.

![Bereitstellungsdialogfeld für das Courier-Modul](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

Überprüfen Sie die Änderungen, und klicken Sie auf „Weiter“.

![Überprüfen der Änderungen im Bereitstellungsdialogfeld für das Courier-Modul](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

Im Bereitstellungsprotokoll wird angezeigt, ob die Bereitstellung erfolgreich war.

 ![Anzeigen der Bereitstellungsprotokolle des Courier-Moduls](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

Durchsuchen Sie Ihre Produktions-Web-App, um zu überprüfen, ob die Änderungen angewendet wurden.

 ![Durchsuchen der Produktions-Web-App](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

Weitere Informationen zur Verwendung von Courier finden Sie in der Dokumentation.

#### Aktualisieren der Umbraco CMS-Version

Courier bietet keine Hilfe beim Aktualisieren der Umbraco CMS-Version. Wenn Sie die Umbraco CMS-Version aktualisieren, müssen Sie überprüfen, ob Inkompatibilitäten mit Ihren benutzerdefinierten Modulen oder Drittanbietermodulen und den Umbraco-Kernbibliotheken vorliegen. Folgende Vorgehensweise wird empfohlen:

1. Sichern Sie Ihre Web-App und Datenbank IMMER, bevor Sie ein Upgrade durchführen. Für Azure-Web-Apps können Sie mithilfe der Sicherungsfunktion automatische Sicherungen für Ihre Websites einrichten und Ihre Website bei Bedarf mit der Wiederherstellungsfunktion wiederherstellen. Weitere Informationen finden Sie unter [Sichern Ihrer Web-App](web-sites-backup.md) und [Wiederherstellen Ihrer Web-App](web-sites-restore.md).

2. Überprüfen Sie, ob die von Ihnen verwendeten Drittanbieterpakete mit der Version kompatibel sind, auf die Sie aktualisieren. Überprüfen Sie auf der Downloadseite des Pakets die Projektkompatibilität mit der Umbraco CMS-Version.

Weitere Informationen zum lokalen Aktualisieren Ihrer Web-App finden Sie [hier](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general).

Nach dem Aktualisieren Ihrer lokalen Entwicklungswebsite veröffentlichen Sie die Änderungen in der Staging-Web-App. Testen Sie Ihre Anwendung, und klicken Sie, wenn alles Ihren Vorstellungen entspricht, auf die Schaltfläche **Austauschen**, um Ihre Stagingwebsite und die Produktions-Web-App **auszutauschen**. Beim Ausführen des **Austauschvorgangs** können Sie die Änderungen anzeigen, die in Ihrer Web-App-Konfiguration betroffen sind. Bei diesem **Austauschvorgang** werden die Web-Apps und Datenbanken ausgetauscht. Daher verweist die Produktions-Web-App nach dem Austausch auf die Datenbank „umbraco-stage-db“ und die Staging-Web-App auf die Datenbank „umbraco-prod-db“.

![Austauschvorschau für die Bereitstellung von Umbraco CMS](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

Das Austauschen der Web-App und der Datenbank bietet folgende Vorteile:
1. Sie können mit einem weiteren **Austauschvorgang** ein Rollback auf die vorherige Version der Web-App ausführen, wenn in der Anwendung Probleme auftreten.
2. Bei einer Aktualisierung müssen Sie Dateien und die Datenbank von der Staging-Web-App in der Produktions-Web-App und der Datenbank bereitstellen. Bei der Bereitstellung von Dateien und Datenbanken kann vieles schief gehen. Durch das **Austauschen** von Slots wird das Risiko von Ausfallzeiten während einer Aktualisierung und von Fehlern bei der Bereitstellung von Änderungen reduziert.
3. Sie können **A/B-Tests** mithilfe der Funktion zum [Testen in der Produktionsumgebung](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/) durchführen.

Dieses Beispiel veranschaulicht die Flexibilität der Plattform. Sie ermöglicht es Ihnen, benutzerdefinierte Module wie das Umbraco Courier-Modul zur umgebungsübergreifenden Verwaltung der Bereitstellung zu erstellen.

## Referenzen
[Agile Softwareentwicklung mit Azure App Service](app-service-agile-software-development.md)

[Einrichten von Stagingumgebungen für Web-Apps in Azure App Service](web-sites-staged-publishing.md)

[Blockieren des Webzugriffs auf Nicht-Produktionsslots](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)

<!---HONumber=AcomDC_0302_2016-->