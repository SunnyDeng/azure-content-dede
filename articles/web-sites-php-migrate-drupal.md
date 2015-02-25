<properties 
	pageTitle="Migrieren von Drupal zu Azure-Websites" 
	description="Migrieren einer Drupal PHP-Website zu Azure Websites." 
	services="web-sites" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="11/11/2014" 
	ms.author="tomfitz"/>


# Migrieren von Drupal zu Azure-Websites

Da Azure-Websites sowohl PHP als auch MySQL unterstützt, ist es relativ unkompliziert, eine Drupal-Website zu Azure-Websites zu migrieren. Und da Drupal sowie PHP auf jeder Plattform ausgeführt werden können, sollte das Verschieben von Drupal zu Azure-Websites unabhängig von der aktuellen Plattform funktionieren. Da die Drupal-Installationen jedoch sehr unterschiedlich sein können, gibt es möglicherweise einige spezielle Migrationsschritte, die im Folgenden nicht behandelt werden. Beachten Sie, dass das Tool Drush nicht verwendet wird, da es auf Azure-Websites nicht unterstützt wird.

> [AZURE.NOTE]
> Wenn Sie eine umfangreiche und komplexe Drupal-Anwendung verschieben, können Sie auch in Erwägung ziehen, Azure-Cloud-Dienste zu verwenden. Weitere Informationen über die Unterschiede zwischen Websites und Cloud-Diensten finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich</a>. Hilfe beim Verschieben von Drupal nach Clouddienste finden Sie unter <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migrieren einer Drupal-Website von LAMP nach Azure</a>.

## Inhaltsverzeichnis

- [Erstellen der Azure-Website][]
- [Kopieren der Datenbank][]
- [Ändern der Datei "Settings.php"][]
- [Bereitstellen des Drupal-Codes][]
- [Verwandte Informationen][]
 
##<a name="create-siteanddb"></a>1. Erstellen einer Azure-Website und einer MySQL-Datenbank

Lernen Sie zunächst mit dem Schritt-für-Schritt-Lernprogramm, wie eine neue Website mit MySQL erstellt wird: [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git][]. Wenn Sie beabsichtigen, Git zum Veröffentlichen der Drupal-Website zu verwenden, führen Sie die Schritte im Lernprogramm aus, in denen erläutert wird, wie ein Git-Repository konfiguriert wird. Befolgen Sie die Anweisungen im Abschnitt **Abrufen von MySQL-Remoteverbindungsinformationen**, da Sie diese Informationen später benötigen. Für die Bereitstellung Ihrer Drupal-Website können Sie den Rest des Lernprogramm ignorieren. Wenn Sie jedoch noch keine Erfahrung mit Azure-Websites (und Git) haben, sind die zusätzlichen Informationen für Sie möglicherweise hilfreich.

Nach dem Einrichten einer neuen Website mit einer MySQL-Datenbank verfügen Sie nun über Ihre MySQL-Datenbank-Verbindungsinformationen und ein (optionales) Git-Repository. Im nächsten Schritt kopieren Sie Ihre Datenbank zu MySQL in Azure-Websites.

##<a name="copy-database"></a>2. Kopieren der Datenbank nach MySQL in Azure-Websites

Es gibt viele Möglichkeiten, eine Datenbank zu Azure zu migrieren. Eine Möglichkeit, die sich sehr gut für MySQL-Datenbanken eignet, ist das Tool [MySqlDump][]. Der folgende Befehl ist ein Beispiel für das Kopieren von einem lokalen Computer zu Azure-Websites:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Sie müssen natürlich den Benutzernamen und das Kennwort für die bestehende Drupal-Datenbank angeben. Außerdem müssen Sie den Hostnamen, den Benutzernamen, das Kennwort und den Datenbanknamen der MySQL-Datenbank angeben, die Sie im ersten Schritt erstellt haben. Diese Informationen sind in den Informationen zur Verbindungszeichenfolge enthalten, die Sie zuvor gesammelt haben. Die Verbindungszeichenfolge sollte ähnlich wie die folgende Zeichenfolge aussehen:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

Je nach der Größe Ihrer Datenbank, kann der Kopiervorgang mehrere Minuten dauern.

Jetzt ist Ihre Drupal-Datenbank in Azure-Websites aktiv. Bevor Sie den Drupal-Code bereitstellen, müssen Sie ihn ändern, damit eine Verbindung mit der neuen Datenbank hergestellt werden kann.

##<a name="modify-settingsphp"></a>3. Ändern der Datenbankverbindungsinformationen in der Datei "settings.php"

Hier benötigen Sie die neuen Datenbankverbindungsinformationen erneut. Öffnen Sie die Datei **/drupal/sites/default/setting.php** in einem Texteditor, und ersetzen Sie die Werte von  'database', 'username', 'password' und  'host' im Array **$databases** durch die korrekten Werte für Ihre neue Datenbank. Nach der Änderung sieht der Code etwa folgendermaßen aus:

    $databases = array (
       'default' => 
       array (
         'default' => 
         array (
           'database' => 'remote_db_name',
           'username' => 'remote_username',
           'password' => 'remote_password',
           'host' => 'remote_host',
           'port' => '',
           'driver' => 'mysql',
           'prefix' => '',
         ),
       ),
     );

Speichern Sie die Datei **settings.php**. Jetzt können Sie den Code bereitstellen.

##<a name="deploy-drupalcode"></a>4. Bereitstellen von Drupal-Code mithilfe von Git oder FTP

Im letzten Schritt stellen Sie den Code mithilfe von Git oder FTP auf Azure-Websites bereit.

Wenn Sie FTP verwenden, rufen Sie den FTP-Hostnamen und -Benutzernamen aus dem Dashboard Ihrer Website ab. Verwenden Sie anschließend einen beliebigen FTP-Client, um die Drupal-Dateien in den Ordner **/site/wwwroot** der Remotewebsite hochzuladen.

Wenn Sie Git verwenden, müssen Sie in den vorherigen Schritten ein Git-Repository eingerichtet haben. Sie müssen Git auf dem lokalen Computer installieren. Folgen Sie anschließend den Anweisungen, die Sie nach Erstellen des Repositorys erhalten haben.

> [AZURE.NOTE]
> Je nach Ihren Git-Einstellungen müssen Sie eventuell Ihre .gitignore-Datei bearbeiten (eine verborgene Datei, die dem .git-Ordner gleichgeordnet ist, der nach der Ausführung von "git commit" im lokalen Stammverzeichnis erstellt wird). Damit werden Dateien in Ihrer Drupal-Anwendung angegeben, die ignoriert werden können. Wenn darin Dateien angegeben sind, die bereitgestellt werden sollen, entfernen Sie die entsprechenden Einträge, damit diese Dateien nicht ignoriert werden.

Nach der Bereitstellung von Drupal auf Azure-Websites können Sie weitere Updates über Git oder FTP bereitstellen.

##<a name="related-information"></a>Verwandte Informationen

Weitere Informationen finden Sie in den folgenden Beiträgen und Themen:

- [Azure-Websites, eine PHP-Perspektive][]
- [Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich][]
- [Konfigurieren von PHP in Azure-Websites mit ".user.ini"-Dateien][]
- [Azure-Integrationsmodul](https://drupal.org/project/azure_auth)
- [Azure-Blob-Speicher-Modul](https://drupal.org/project/azure_blob)

  [Erstellen der Azure-Website]: #create-siteanddb
  [Kopieren der Datenbank]: #copy-database
  [Ändern der Datei "Settings.php"]: #modify-settingsphp
  [Bereitstellen des Drupal-Codes]: #deploy-drupalcode
  [Verwandte Informationen]: #related-information
  [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git]: http://www.windowsazure.com/de-de/develop/php/tutorials/website-w-mysql-and-git/
  
  [Azure-Websites, eine PHP-Perspektive]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Azure-Websites, Cloud-Dienste und virtuelle Computer: Vergleich]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Konfigurieren von PHP in Azure-Websites mit ".user.ini"-Dateien]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure-Integrationsmodul]: http://drupal.org/project/azure




<!--HONumber=42-->
