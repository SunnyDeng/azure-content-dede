<properties 
	pageTitle="Migrieren von Drupal nach Azure App Service" 
	description="Migrieren Sie eine Drupal PHP-Website nach Azure App Service." 
	services="app-service\web" 
	documentationCenter="php" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>


# Migrieren von Drupal nach Azure App Service

Da Azure App Service sowohl PHP als auch MySQL unterstützt, ist es relativ unkompliziert, eine Drupal-Website in Azure App Service-Web-Apps zu migrieren. Und da Drupal sowie PHP auf jeder Plattform ausgeführt werden können, sollte das Verschieben von Drupal in Azure App Service-Web-Apps unabhängig von der aktuellen Plattform funktionieren. Da die Drupal-Installationen jedoch sehr unterschiedlich sein können, gibt es möglicherweise einige spezielle Migrationsschritte, die im Folgenden nicht behandelt werden. Beachten Sie, dass das Tool Drush nicht verwendet wird, da es in Azure App Service nicht unterstützt wird.

Wenn Sie eine umfangreiche und komplexe Drupal-Anwendung verschieben, können Sie auch in Erwägung ziehen, Azure-Cloud-Dienste zu verwenden. Weitere Informationen zu den Unterschieden zwischen App Service und Cloud Services finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=310123">Azure App Service, Cloud Services und Virtual Machines im Vergleich</a>. Hilfe beim Migrieren von Drupal zu Cloud Services finden Sie unter <a href="http://blogs.msdn.com/b/brian_swan/archive/2012/03/19/azure-real-world-migrating-drupal-from-lamp-to-windows-azure.aspx">Migrating a Drupal Site from LAMP to Azure</a> (Migrieren eines Drupal-Standorts von LAMP zu Azure; in englischer Sprache).
 
## Erstellen einer Web-App und einer MySQL-Datenbank

Machen Sie sich zunächst anhand des folgenden Lernprogramms mit der Erstellung einer neuen Web-App mit MySQL vertraut: [Erstellen einer PHP-MySQL-Web-App in Azure App Service und Bereitstellen über Git][]. Wenn Sie beabsichtigen, Git zum Veröffentlichen der Drupal-Website zu verwenden, führen Sie die Schritte im Lernprogramm aus, in denen erläutert wird, wie ein Git-Repository konfiguriert wird. Befolgen Sie die Anweisungen im Abschnitt **Get remote MySQL connection information** (Abrufen von MySQL-Remoteverbindungsinformationen), da Sie diese Informationen später benötigen. Für die Bereitstellung Ihrer Drupal-Website können Sie den Rest des Lernprogramm ignorieren. Wenn Sie jedoch noch keine Erfahrung mit Azure App Service (und Git) haben, sind die zusätzlichen Informationen für Sie möglicherweise hilfreich.

Nach dem Einrichten einer neuen Web-App mit einer MySQL-Datenbank verfügen Sie nun über Ihre MySQL-Datenbankverbindungsinformationen und ein (optionales) Git-Repository. Im nächsten Schritt kopieren Sie die Datenbank in die MySQL-Datenbank der Web-App.

## Kopieren der Datenbank in die MySQL-Datenbank der Web-App

Es gibt viele Möglichkeiten, eine Datenbank zu Azure zu migrieren. Ein Verfahren, das sich sehr gut für MySQL-Datenbanken eignet ist das Tool [MySqlDump][]. Der folgende Befehl ist ein Beispiel für das Kopieren von einem lokalen Computer nach Azure:

    mysqldump -u local_username --password=local_password  drupal | mysql -h remote_host -u remote_username --password=remote_password remote_db_name

Sie müssen natürlich den Benutzernamen und das Kennwort für die bestehende Drupal-Datenbank angeben. Außerdem müssen Sie den Hostnamen, den Benutzernamen, das Kennwort und den Datenbanknamen der MySQL-Datenbank angeben, die Sie im ersten Schritt erstellt haben. Diese Informationen sind in den Informationen zur Verbindungszeichenfolge enthalten, die Sie zuvor gesammelt haben. Die Verbindungszeichenfolge sollte ähnlich wie die folgende Zeichenfolge aussehen:

    Database=remote_db_name;Data Source=remote_host;User Id=remote_username;Password=remote_password

Je nach der Größe Ihrer Datenbank, kann der Kopiervorgang mehrere Minuten dauern.

Jetzt ist Ihre Drupal-Datenbank in Azure aktiv. Bevor Sie den Drupal-Code bereitstellen, müssen Sie ihn ändern, damit eine Verbindung mit der neuen Datenbank hergestellt werden kann.

## Ändern der Datenbankverbindungsinformationen in der Datei "settings.php"

Hier benötigen Sie die neuen Datenbankverbindungsinformationen erneut. Öffnen Sie die Datei **/drupal/sites/default/setting.php** in einem Texteditor, und ersetzen Sie die Werte von "database", "username", "password" und "host" im Array **$databases** durch die korrekten Werte für Ihre neue Datenbank. Nach der Änderung sieht der Code etwa folgendermaßen aus:

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

## Bereitstellen von Drupal-Code mithilfe von Git oder FTP

Im letzten Schritt stellen Sie den Code mithilfe von Git oder FTP in der Web-App bereit.

Wenn Sie FTP verwenden, ermitteln Sie im [Azure-Vorschauportal](https://portal.azure.com) auf dem Blatt Ihrer Web-App den FTP-Hostnamen und den Benutzernamen. Verwenden Sie anschließend einen beliebigen FTP-Client, um die Drupal-Dateien zum **/site/wwwroot** der Remote-Website hochzuladen.

Wenn Sie Git verwenden, müssen Sie in den vorherigen Schritten ein Git-Repository eingerichtet haben. Sie müssen Git auf dem lokalen Computer installieren. Folgen Sie anschließend den Anweisungen, die Sie nach Erstellen des Repositorys erhalten haben.

> [AZURE.NOTE]Je nach Ihren Git-Einstellungen müssen Sie eventuell Ihre .gitignore-Datei bearbeiten (eine verborgene Datei, die dem .git-Ordner gleichgeordnet ist, der nach der Ausführung von "git commit" im lokalen Stammverzeichnis erstellt wird). Damit werden Dateien in Ihrer Drupal-Anwendung angegeben, die ignoriert werden können. Wenn darin Dateien angegeben sind, die bereitgestellt werden sollen, entfernen Sie die entsprechenden Einträge, damit diese Dateien nicht ignoriert werden.

Nach der Bereitstellung von Drupal in Web-Apps können Sie weitere Updates über Git oder FTP bereitstellen.

## Verwandte Informationen

Weitere Informationen finden Sie in den folgenden Beiträgen und Themen:

- [Azure App Service Web Apps, a PHP Perspective][] (Azure App Service-Web-Apps – eine PHP-Perspektive; in englischer Sprache)
- [Azure App Service, Cloud Services und Virtual Machines im Vergleich][]
- [Configuring PHP in Azure App Service Web Apps with .user.ini Files][] (Konfigurieren von PHP in Azure App Service-Web-Apps mit Dateien vom Typ „.user.ini“; in englischer Sprache)
- [Azure-Integrationsmodul](https://drupal.org/project/azure_auth)
- [Azure-Blob-Speicher-Modul](https://drupal.org/project/azure_blob)

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715).

  [Erstellen einer PHP-MySQL-Web-App in Azure App Service und Bereitstellen über Git]: /develop/php/tutorials/website-w-mysql-and-git/
  
  [Azure App Service Web Apps, a PHP Perspective]: http://blogs.msdn.com/b/silverlining/archive/2012/06/12/windows-azure-websites-a-php-perspective.aspx
  [Azure App Service, Cloud Services und Virtual Machines im Vergleich]: http://go.microsoft.com/fwlink/?LinkId=310123
  [Configuring PHP in Azure App Service Web Apps with .user.ini Files]: http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx
  [Azure Integration Module]: http://drupal.org/project/azure
 

<!---HONumber=62-->