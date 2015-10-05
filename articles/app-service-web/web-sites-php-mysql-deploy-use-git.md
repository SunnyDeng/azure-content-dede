<properties
	pageTitle="Erstellen einer PHP-MySQL-Web-App in Azure App Service und Bereitstellen über Git"
	description="In diesem Lernprogramm wird gezeigt, wie Sie eine PHP-Web-App erstellen, die Daten in MySQL speichert, und wie Sie die Git-Bereitstellung in Azure verwenden."
	services="app-service\web"
	documentationCenter="php"
	authors="tfitzmac"
	manager="wpickett"
	editor="mollybos"
	tags="mysql"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="08/03/2015"
	ms.author="tomfitz"/>

#Erstellen einer PHP-MySQL-Web-App in Azure App Service und Bereitstellen über Git

> [AZURE.SELECTOR]
- [.Net](web-sites-dotnet-get-started.md)
- [Node.js](web-sites-nodejs-develop-deploy-mac.md)
- [Java](web-sites-java-get-started.md)
- [PHP - Git](web-sites-php-mysql-deploy-use-git.md)
- [PHP - FTP](web-sites-php-mysql-deploy-use-ftp.md)
- [Python](web-sites-python-ptvs-django-mysql.md)

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-MySQL-Web-App erstellen und über Git in [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) bereitstellen. Dazu verwenden Sie [PHP][install-php], das MySQL-Befehlszeilentool (Teil von [MySQL][install-mysql]), einen Webserver und [Git][install-git] auf Ihrem Computer. Die Anweisungen in diesem Lernprogramm lassen sich von jedem Betriebssystem aus befolgen, einschließlich Windows, Max und Linux. Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Azure ausgeführte PHP-/MySQL-Web-App.

Sie erhalten Informationen zu folgenden Themen:

* Erstellen einer Web-App und einer MySQL-Datenbank über das [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715). Da PHP standardmäßig in[App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
* Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von Git in Azure

Mithilfe dieses Lernprogramms erstellen Sie eine einfache Web-App für die Registrierung in PHP. Die Anwendung wird in Web-Apps gehostet. Nachfolgend sehen Sie einen Screenshot der fertigen Anwendung:

![Azure-PHP-Website][running-app]

##Einrichten der Entwicklungsumgebung

Voraussetzung für dieses Lernprogramm sind [PHP][install-php], das MySQL-Befehlszeilentool (Teil von [MySQL][install-mysql]), ein Webserver und [Git][install-git] auf Ihrem Computer.

> [AZURE.NOTE]Sollten Sie dieses Lernprogramm unter Windows ausführen, können Sie Ihren Computer für PHP einrichten und IIS (den integrierten Webserver in Windows) automatisch konfigurieren, indem Sie das <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Azure SDK für PHP</a> installieren.

##<a id="create-web-site-and-set-up-git"></a>Erstellen einer Web-App und Einrichten der Git-Veröffentlichung

Befolgen Sie diese Schritte, um eine Web-App und eine MySQL-Datenbank zu erstellen:

1. Melden Sie sich beim [Azure-Portal][management-portal] an.
2. Klicken Sie auf das Symbol **Neu**.

3. Klicken Sie auf **Web + Mobile** und dann auf **Azure Marketplace**.

4. Klicken Sie auf**Web-Apps** und dann auf **Web-App + MySQL**. Klicken Sie dann auf **Erstellen**.

4. Geben Sie einen gültigen Namen für die Ressourcengruppe ein.

    ![Ressourcengruppenname festlegen][resource-group]

5. Geben Sie Werte für die neue Web-App ein.

    ![Web-App erstellen][new-web-app]

6. Geben Sie Werte für die neue Datenbank ein, einschließlich Ihrer Zustimmung der rechtlichen Bedingungen.

	![Neue MySQL-Datenbank erstellen][new-mysql-db]

7. Wenn die Web-App erstellt wurde, wird die neue Ressourcengruppe angezeigt. Klicken Sie auf den Namen der Web-App, um deren Einstellungen zu konfigurieren.

7. Klicken Sie auf **Kontinuierliche Bereitstellung einrichten**.

	![Git-Veröffentlichung einrichten][setup-publishing]

8. Wählen Sie **Lokales Git-Repository** für Ihren Quellcode aus.

    ![Git-Repository einrichten][setup-repository]


9. Um die Git-Veröffentlichung aktivieren zu können, müssen Sie einen Benutzernamen und ein Kennwort angeben. Notieren Sie sich den Benutzernamen und das Kennwort, die Sie erstellen. (Wenn Sie schon einmal ein Git-Verzeichnis eingerichtet haben, wird dieser Schritt übersprungen.)

	![Anmeldedaten für die Veröffentlichung erstellen][credentials]


##Abrufen von MySQL-Remoteverbindungsinformationen

Um eine Verbindung mit der in Web-Apps ausgeführten MySQL-Datenbank herzustellen, benötigen Sie die Verbindungsinformationen. Befolgen Sie die folgenden Schritte, um MySQL-Verbindungsinformationen abzurufen:

1. Klicken Sie in der Ressourcengruppe auf die Datenbank:

	![Datenbank auswählen][select-database]

2. Wählen Sie für der Datenbankzusammenfassung die Option **Eigenschaften**.

    ![Eigenschaften auswählen][select-properties]

2. Notieren Sie sich die Werte für `Database`, `Host`, `User Id` und `Password`.

    ![Eigenschaften notieren][note-properties]

##Lokales Erstellen und Testen der App

Nach der Erstellung einer Web-App können Sie Ihre Anwendung lokal entwickeln und nach dem Testen bereitstellen.

Die Registrierungsanwendung ist eine einfache PHP-Anwendung, die Ihnen die Registrierung für eine Veranstaltung durch Angabe Ihres Namens und Ihrer E-Mail-Adresse ermöglicht. Informationen zu vorherigen Registranten werden in einer Tabelle angezeigt. Registrierungsinformationen werden in einer MySQL-Datenbank gespeichert. Die Anwendung besteht aus einer Datei (unten stehenden Code kopieren/einfügen):

* **index.php**: Zeigt ein Registrierungsformular und eine Tabelle mit Registranteninformationen an.

Befolgen Sie die unten stehenden Schritte, um die Anwendung lokal zu erstellen und auszuführen. Beachten Sie, dass Voraussetzung für diese Schritte ist, dass PHP, das MySQL-Befehlszeilentool (Teil von MySQL) und ein Webserver auf Ihrem lokalen Computer eingerichtet sind und die [PDO-Erweiterung für MySQL][pdo-mysql] aktiviert ist.

1. Stellen Sie eine Verbindung mit dem MySQL-Remoteserver her, indem Sie den zuvor abgerufenen Wert für `Data Source`, `User Id`, `Password` und `Database` verwenden:

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. Die MySQL-Eingabeaufforderung wird angezeigt:

		mysql>

3. Fügen Sie den folgenden `CREATE TABLE`-Befehl ein, um die Tabelle `registration_tbl` in Ihrer Datenbank zu erstellen:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. Erstellen Sie im Stammverzeichnis Ihres Webservers einen Ordner mit dem Namen `registration`, und erstellen Sie darin eine Datei mit dem Namen `index.php`.

5. Öffnen Sie die Datei **index.php** in einem Texteditor oder einer IDE, und fügen Sie den folgenden Code ein. Nehmen Sie dann die erforderlichen Änderungen gemäß den `//TODO:`-Kommentaren vor.


		<html>
		<head>
		<Title>Registration Form</Title>
		<style type="text/css">
			body { background-color: #fff; border-top: solid 10px #000;
			    color: #333; font-size: .85em; margin: 20; padding: 20;
			    font-family: "Segoe UI", Verdana, Helvetica, Sans-Serif;
			}
			h1, h2, h3,{ color: #000; margin-bottom: 0; padding-bottom: 0; }
			h1 { font-size: 2em; }
			h2 { font-size: 1.75em; }
			h3 { font-size: 1.2em; }
			table { margin-top: 0.75em; }
			th { font-size: 1.2em; text-align: left; border: none; padding-left: 0; }
			td { padding: 0.25em 2em 0.25em 0em; border: 0 none; }
		</style>
		</head>
		<body>
		<h1>Register here!</h1>
		<p>Fill in your name and email address, then click <strong>Submit</strong> to register.</p>
		<form method="post" action="index.php" enctype="multipart/form-data" >
		      Name  <input type="text" name="name" id="name"/></br>
		      Email <input type="text" name="email" id="email"/></br>
		      <input type="submit" name="submit" value="Submit" />
		</form>
		<?php
			// DB connection info
			//TODO: Update the values for $host, $user, $pwd, and $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try {
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
			if(!empty($_POST)) {
			try {
				$name = $_POST['name'];
				$email = $_POST['email'];
				$date = date("Y-m-d");
				// Insert data
				$sql_insert = "INSERT INTO registration_tbl (name, email, date)
						   VALUES (?,?,?)";
				$stmt = $conn->prepare($sql_insert);
				$stmt->bindValue(1, $name);
				$stmt->bindValue(2, $email);
				$stmt->bindValue(3, $date);
				$stmt->execute();
			}
			catch(Exception $e) {
				die(var_dump($e));
			}
			echo "<h3>Your're registered!</h3>";
			}
			// Retrieve data
			$sql_select = "SELECT * FROM registration_tbl";
			$stmt = $conn->query($sql_select);
			$registrants = $stmt->fetchAll();
			if(count($registrants) > 0) {
				echo "<h2>People who are registered:</h2>";
				echo "<table>";
				echo "<tr><th>Name</th>";
				echo "<th>Email</th>";
				echo "<th>Date</th></tr>";
				foreach($registrants as $registrant) {
					echo "<tr><td>".$registrant['name']."</td>";
					echo "<td>".$registrant['email']."</td>";
					echo "<td>".$registrant['date']."</td></tr>";
		    	}
		 		echo "</table>";
			} else {
				echo "<h3>No one is currently registered.</h3>";
			}
		?>
		</body>
		</html>

Nun können Sie zu ****http://localhost/registration/index.php** navigieren, um die Anwendung zu testen.


##Veröffentlichen der App

Nachdem Sie Ihre App lokal getestet haben, können Sie sie über Git in Web-Apps veröffentlichen. Sie initialisieren Ihr lokales Git-Verzeichnis und veröffentlichen die Anwendung.

> [AZURE.NOTE]Dies sind dieselben Schritte, die am Ende des obigen Abschnitts „Erstellen einer Web-App und Einrichten der Git-Veröffentlichung“ im Portal gezeigt werden.

1. (Optional) Sollten Sie Ihre Git-Remoteverzeichnis-URL vergessen oder verlegt haben, navigieren Sie zu den Web-App-Eigenschaften im Portal.

1. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

	![Anfängliche Push-Übertragung über Git zu Azure][git-initial-push]

2. Navigieren Sie zu **http://[site name].azurewebsites.net/index.php**, um die Anwendung zu verwenden (diese Informationen werden in Ihrem Konto-Dashboard gespeichert):

	![Azure-PHP-Website][running-app]

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen.

##Veröffentlichen von Änderungen an der Anwendung

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer App, und führen Sie die folgenden Befehle aus:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

	![Push-Übertragung von Websiteänderungen über Git zu Azure][git-change-push]

3. Navigieren Sie zu **http://[site name].azurewebsites.net/index.php**, um Ihre Anwendung und etwaige Änderungen anzuzeigen:

	![Azure-PHP-Website][running-app]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[install-mysql]: http://dev.mysql.com/downloads/mysql/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website2.png
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/create_web_mysql.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/create_db.png
[go-to-webapp]: ./media/web-sites-php-mysql-deploy-use-git/select_webapp.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/save_credentials.png
[resource-group]: ./media/web-sites-php-mysql-deploy-use-git/set_group.png
[new-web-app]: ./media/web-sites-php-mysql-deploy-use-git/create_wa.png
[setup-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_deploy.png
[setup-repository]: ./media/web-sites-php-mysql-deploy-use-git/select_local_git.png
[select-database]: ./media/web-sites-php-mysql-deploy-use-git/select_database.png
[select-properties]: ./media/web-sites-php-mysql-deploy-use-git/select_properties.png
[note-properties]: ./media/web-sites-php-mysql-deploy-use-git/note-properties.png

[git-instructions]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://portal.azure.com
[sql-database-editions]: http://msdn.microsoft.com/library/windowsazure/ee621788.aspx
 

<!---HONumber=Sept15_HO4-->