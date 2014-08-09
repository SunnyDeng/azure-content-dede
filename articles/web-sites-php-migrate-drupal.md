<properties linkid="migrating-drupal-to-azure-websites" urlDisplayName="Migrating Drupal to Azure Web Sites" pageTitle="Migrating Drupal to Azure Web Sites" metaKeywords="Drupal, PHP, Web Sites" description="Migrate a Drupal PHP site to Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Migrating Drupal to Azure Web Sites" authors="jroth" solutions="" manager="paulettm" editor="mollybos" />

Migrieren von Drupal nach Azure-Websites
========================================

Da Azure-Websites sowohl PHP als auch MySQL unterstützt, ist es relativ unkompliziert, eine Drupal-Website nach Azure-Websites zu migrieren. Und da Drupal sowie PHP auf jeder Plattform ausgeführt werden können, sollte das Verschieben von Drupal nach Azure-Websites unabhängig von der aktuellen Plattform funktionieren. Da die Drupal-Installationen jedoch sehr unterschiedlich sein können, gibt es möglicherweise einige spezielle Migrationsschritte, die im Folgenden nicht behandelt werden. Beachten Sie, dass das Tool Drush nicht verwendet wird, da es auf Azure-Websites nicht unterstützt wird.

> [WACOM.NOTE] Wenn Sie eine umfangreiche und komplexe Drupal-Anwendung verschieben, können Sie auch in Erwägung ziehen, Azure-Clouddienste zu verwenden. Weitere Informationen über die Unterschiede zwischen Websites und Clouddienste finden Sie unter [Azure Web Sites, Cloud Services and Virtual Machines Comparison](http://go.microsoft.com/fwlink/?LinkId=310123) (Vergleich zwischen Azure-Websites, -Clouddienste und Azure Virtual Machines, in englischer Sprache). Hilfe beim Verschieben von Drupal nach Clouddienste finden Sie unter [Migrating a Drupal Site from LAMP to Windows Azure](http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx) (Migrieren einer Drupal-Website von LAMP nach Azure, in englischer Sprache).

Inhaltsverzeichnis
------------------

-   [Erstellen der Azure-Website](#create-siteanddb)
-   [Kopieren der Datenbank](#copy-database)
-   [Ändern der Datei Settings.php](#modify-settingsphp)
-   [Bereitstellen des Drupal-Codes](#deploy-drupalcode)
-   [Verwandte Informationen](#related-information)

Erstellen einer Azure-Website und einer MySQL-Datenbank1. Erstellen einer Azure-Website und einer MySQL-Datenbank
-----------------------------------------------------------------------------------------------------------------

Lernen Sie zunächst mit dem Schritt-für-Schritt-Lernprogramm, wie eine neue Website mit MySQL erstellt wird: [Create a PHP-MySQL Azure web site and deploy using Git](http://www.windowsazure.com/de-de/develop/php/tutorials/website-w-mysql-and-git/) (Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git, in englischer Sprache). Wenn Sie beabsichtigen, Git zum Veröffentlichen der Drupal-Website zu verwenden, führen Sie die Schritte im Lernprogramm aus, in denen erläutert wird, wie ein Git-Repository konfiguriert wird. Befolgen Sie die Anweisungen im Abschnitt **Get remote MySQL connection information** (Abrufen von MySQL-Remoteverbindungsinformationen), da Sie diese Informationen später benötigen. Für die Bereitstellung Ihrer Drupal-Website können Sie den Rest des Lernprogramm ignorieren. Wenn Sie jedoch noch keine Erfahrung mit Azure-Websites (und Git) haben, sind die zusätzlichen Informationen für Sie möglicherweise hilfreich.

Nach dem Einrichten einer neuen Website mit einer MySQL-Datenbank verfügen Sie nun über Ihre MySQL-Datenbank-Verbindungsinformationen und ein (optionales) Git-Repository. Im nächsten Schritt kopieren Sie Ihre Datenbank nach MySQL in Azure-Websites.

Kopieren der Datenbank nach MySQL in Azure-Websites2. Kopieren der Datenbank nach MySQL in Azure-Websites
---------------------------------------------------------------------------------------------------------

Es gibt viele Möglichkeiten, eine Datenbank nach Azure zu migrieren. Ein Verfahren, das sich sehr gut für MySQL-Datenbanken eignet ist das Tool [MySqlDump][]. Der folgende Benutzer zeigt an einem Beispiel das Kopieren von einem lokalen Computer nach Azure-Websites:

    mysqldump -u lokaler_benutzername --password=lokales_kennwort  drupal | mysql -h remote_host -u remote_benutzername --password=remote_kennwort remote_db_name

Sie müssen natürlich den Benutzernamen und das Kennwort für die bestehende Drupal-Datenbank angeben. Außerdem müssen Sie den Hostnamen, den Benutzernamen, das Kennwort und den Datenbanknamen der MySQL-Datenbank angeben, die Sie im ersten Schritt erstellt haben. Diese Informationen sind in den Informationen zur Verbindungszeichenfolge enthalten, die Sie zuvor gesammelt haben. Die Verbindungszeichenfolge sollte ähnlich wie die folgende Zeichenfolge aussehen:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_benutzername;Password=remote_kennwort

Je nach der Größe Ihrer Datenbank, kann der Kopiervorgang mehrere Minuten dauern.

Jetzt ist Ihre Drupal-Datenbank in Azure-Websites aktiv. Bevor Sie den Drupal-Code bereitstellen, müssen Sie ihn ändern, damit eine Verbindung mit der neuen Datenbank hergestellt werden kann.

Ändern der Datenbankverbindungsinformationen in der Datei settings.php3. Ändern der Datenbankverbindungsinformationen in der Datei settings.php
-----------------------------------------------------------------------------------------------------------------------------------------------

Hier benötigen Sie die neuen Datenbankverbindungsinformationen erneut. Öffnen Sie die Datei **/drupal/sites/default/setting.php** in einem Texteditor, und ersetzen Sie die Werte für "database", "username", "password" und "host" im Array **\$databases** durch die richtigen Werte für die neue Datenbank. Nach der Änderung sieht der Code etwa folgendermaßen aus:

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_benutzername',
           'password' => 'remote_kennwort',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

Speichern Sie die Datei **settings.php**. Jetzt können Sie den Code bereitstellen.

Bereitstellen von Drupal-Code mithilfe von Git oder FTP4. Bereitstellen von Drupal-Code mithilfe von Git oder FTP
-----------------------------------------------------------------------------------------------------------------

Im letzten Schritt stellen Sie den Code mithilfe von Git oder FTP auf Azure-Websites bereit.

Wenn Sie FTP verwenden, rufen Sie den FTP-Hostnamen und -Benutzernamen aus dem Dashboard der Website ab. Verwenden Sie anschließend einen beliebigen FTP-Client, um die Drupal-Dateien zum **/site/wwwroot** der Remote-Website hochzuladen.

Wenn Sie Git verwenden, müssen Sie in den vorherigen Schritten ein Git-Repository eingerichtet haben. Sie müssen Git auf dem lokalen Computer installieren. Folgen Sie anschließend den Anweisungen, die Sie nach Erstellen des Repositorys erhalten haben.

> [WACOM.NOTE] Je nach den Git-Einstellungen müssen Sie eventuell Ihre .gitignore-Datei bearbeiten (eine verborgene Datei, die dem .git-Ordner gleichgeordnet ist, der nach der Ausführung von git commit im lokalen Stammverzeichnis erstellt wird. Damit werden Dateien in Ihrer Drupal-Anwendung angegeben, die ignoriert werden können. Wenn darin Dateien angegeben sind, die bereitgestellt werden sollen, entfernen Sie die entsprechenden Einträge, damit diese Dateien nicht ignoriert werden.

Nach der Bereitstellung von Drupal in Azure-Websites können Sie weitere Updates über Git oder FTP bereitstellen.

Verwandte InformationenVerwandte Informationen
----------------------------------------------

Weitere Informationen finden Sie in den folgenden Beiträgen und Themen:

-   [Azure Websites, a PHP Perspective (Azure-Websites aus PHP-Perspektive, in englischer Sprache)](http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx)
-   [Azure Web Sites, Cloud Services and Virtual Machines Comparison](http://go.microsoft.com/fwlink/?LinkId=310123) (Vergleich von Azure-Websites, -Clouddienste und Azure Virtual Machines, in englischer Sprache)
-   [Configuring PHP in Azure Websites with .user.ini Files (Konfigurieren von PHP in Azure-Websites mit .user.ini-Dateien, in englischer Sprache)](http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx)
-   [Windows Azure Authentication (Azure-Authentifizierung, in englischer Sprache)](https://drupal.org/project/azure_auth)
-   [Windows Azure Blob (Azure-Blob-Speicher)](https://drupal.org/project/azure_blob)


