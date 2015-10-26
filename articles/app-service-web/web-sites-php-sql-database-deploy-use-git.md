<properties 
	pageTitle="Erstellen einer PHP-SQL-Web-App und Bereitstellen in Azure App Service mit Git" 
	description="In diesem Lernprogramm wird gezeigt, wie Sie eine PHP-Web-App erstellen, die Daten in einer Azure SQL-Datenbank speichert, und wie Sie die Git-Bereitstellung in Azure App Service verwenden." 
	services="app-service\web, sql-database" 
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
	ms.date="08/03/2015" 
	ms.author="tomfitz"/>

# Erstellen einer PHP-SQL-Web-App und Bereitstellen in Azure App Service mit Git

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-Web-App in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) erstellen, die eine Verbindung mit einer Azure-SQL-Datenbank herstellt, und wie Sie diese über Git bereitstellen. Dieses Lernprogramm setzt voraus, dass auf dem Computer [PHP][install-php], [SQL Server Express][install-SQLExpress], die [Microsoft-Treiber für SQL Server für PHP](http://www.microsoft.com/download/en/details.aspx?id=20098), ein Webserver und [Git][install-git] installiert sind. Nach der Durchführung dieses Lernprogramms verfügen Sie über eine in Azure ausgeführte PHP-SQL-Web-App.

> [AZURE.NOTE]Sie können PHP, SQL Server Express, die Microsoft-Treiber für SQL Server für PHP und Internet Information Services (IIS) mit dem [Microsoft-Webplattform-Installer](http://www.microsoft.com/web/downloads/platform.aspx) installieren und konfigurieren.

Sie erhalten Informationen zu folgenden Themen:

* Erstellen einer Web-App und einer SQL-Datenbank über das [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715). Da PHP standardmäßig in App Service-Web-Apps aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
* Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von Git in Azure
 
Mithilfe dieses Lernprogramms erstellen Sie eine einfache Webanwendung für die Registrierung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Azure-PHP-Website](./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Erstellen einer Azure-Web-App und Einrichten der Git-Veröffentlichung

Befolgen Sie diese Schritte, um eine Azure-Web-App und eine SQL-Datenbank zu erstellen:

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/) an.

2. Öffnen Sie Azure Marketplace entweder durch Klicken auf das Symbol **Marketplace** oder auf das Symbol **Neu** links unten im Dashboard. Wählen Sie anschließend **Web + Mobile** und dann unten **Azure Marketplace** aus.
	
3. Wählen Sie im Marketplace **Web-Apps** aus.

4. Klicken Sie auf das Symbol W**Web-App + SQL**.

5. Wählen Sie nach dem Lesen der Beschreibung der "Web-App + SQL"-App die Option **Erstellen** aus.

6. Klicken Sie auf die einzelnen Bereiche (**Ressourcengruppe**, **Web-App**, **Datenbank** und **Abonnement**), und geben Sie die Werte für die erforderlichen Felder ein, oder wählen Sie sie aus:
	
	- Geben Sie den gewünschten URL-Namen ein.	
	- Konfigurieren der Anmeldeinformationen für die Datenbank
	- Wählen Sie die Ihnen am nächsten gelegene Region aus.

	![App konfigurieren](./media/web-sites-php-sql-database-deploy-use-git/configure-db-settings.png)

7. Klicken Sie auf **Erstellen**, nachdem Sie die Definition der Web-App abgeschlossen haben.

	Nachdem die Web-App erstellt wurde, leuchtet auf der Schaltfläche **Benachrichtigungen** der Text **ERFOLG** in grüner Schrift, und das Blatt der Ressourcengruppe wird geöffnet und zeigt die Web-App und die SQL-Datenbank in der Gruppe an.

4. Klicken Sie auf das Symbol der Web-App auf dem Blatt der Ressourcengruppe, um das Blatt der Web-App zu öffnen.

	![Ressourcengruppe der Web-App](./media/web-sites-php-sql-database-deploy-use-git/resource-group-blade.png)

5. Klicken Sie auf **Kontinuierliche Bereitstellung einrichten** > **Quelle auswählen**. Wählen Sie **Lokales Git-Repository** aus, und klicken Sie auf **OK**.

	![Wo befindet sich Ihr Quellcode](./media/web-sites-php-sql-database-deploy-use-git/setup-local-git.png)

	Wenn Sie nicht bereits ein Git-Repository eingerichtet haben, müssen Sei einen Benutzernamen und ein Kennwort angeben. Klicken Sie dazu auf dem Blatt der Web-App auf **Anmeldeinformationen für die Bereitstellung einrichten**.

	![](./media/web-sites-php-sql-database-deploy-use-git/deployment-credentials.png)

6. **Kontinuierliche Bereitstellung einrichten** ändert sich in **Keine Bereitstellung gefunden**. Klicken Sie darauf, um die Git-Remote-URL anzuzeigen, die Sie später für die Bereitstellung Ihrer PHP-App benötigen.

##Abrufen von SQL-Datenbank-Verbindungsinformationen

Um eine Verbindung zu der mit Ihrer Web-App verknüpften SQL-Datenbankinstanz herzustellen, benötigen Sie die Verbindungsinformationen, die Sie beim Erstellen der Datenbank angegeben haben. Gehen Sie folgendermaßen vor, um die Verbindungsinformationen für die SQL-Datenbank abzurufen:

1. Klicken Sie auf dem Blatt der Ressourcengruppe auf das Symbol der SQL-Datenbank.

2. Klicken Sie auf dem Blatt der SQL-Datenbank auf **Eigenschaften** und dann auf **Verbindungszeichenfolgen anzeigen**.

	![Datenbankeigenschaften anzeigen](./media/web-sites-php-sql-database-deploy-use-git/view-database-properties.png)
	
3. Notieren Sie sich im Abschnitt **PHP** des angezeigten Dialogfelds die Werte für `Server`, `SQL Database` und `User Name`. Sie verwenden diese Werte später zum Veröffentlichen Ihrer PHP-Web-App in Azure App Service.

##Lokales Erstellen und Testen der Anwendung

Die Registrierungsanwendung ist eine einfache PHP-Anwendung, die Ihnen die Registrierung für eine Veranstaltung durch Angabe Ihres Namens und Ihrer E-Mail-Adresse ermöglicht. Informationen zu vorherigen Registranten werden in einer Tabelle angezeigt. Registrierungsinformationen werden in einer SQL-Datenbank gespeichert. Die Anwendung besteht aus zwei Dateien (unten stehenden Code kopieren/einfügen):

* **index.php**: Zeigt ein Registrierungsformular und eine Tabelle mit Registranteninformationen an.
* **createtable.php**: Erstellt die SQL-Datenbanktabelle für die Anwendung. Diese Datei wird nur einmal verwendet.

Gehen Sie wie unten beschrieben vor, um die Anwendung lokal zu erstellen und auszuführen. Beachten Sie, dass Voraussetzung für diese Schritte ist, dass PHP, SQL Server Express und ein Webserver auf Ihrem lokalen Computer eingerichtet sind und die [PDO-Erweiterung für SQL-Server][pdo-sqlsrv] aktiviert ist.

1. Erstellen Sie eine SQL Server-Datenbank namens `registration`. Sie können dies in der `sqlcmd`-Eingabeaufforderung mit diesen Befehlen ausführen:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. Erstellen Sie im Stammverzeichnis Ihres Webservers einen Ordner mit dem Namen `registration`, und erstellen Sie darin zwei Dateien mit den Namen `createtable.php` und `index.php`.

3. Öffnen Sie die Datei `createtable.php` in einem Texteditor oder IDE, und fügen Sie den folgenden Code hinzu. Dieser Code wird verwendet, um die Tabelle `registration_tbl` in der Datenbank `registration` zu erstellen.

		<?php
		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			$sql = "CREATE TABLE registration_tbl(
			id INT NOT NULL IDENTITY(1,1) 
			PRIMARY KEY(id),
			name VARCHAR(30),
			email VARCHAR(30),
			date DATE)";
			$conn->query($sql);
		}
		catch(Exception $e){
			die(print_r($e));
		}
		echo "<h3>Table created.</h3>";
		?>

	Die Werte für <code>$user</code> und <code>$pwd</code> müssen durch den lokalen SQL Server-Benutzernamen und das dazugehörige Kennwort ersetzt werden.

4. Öffnen Sie einen Webbrowser, und navigieren Sie zu ****http://localhost/registration/createtable.php**. Dadurch wird die Tabelle `registration_tbl` in der Datenbank erstellt.

5. Öffnen Sie die Datei **index.php** in einem Texteditor oder IDE, und fügen Sie den Basis-HTML- und CSS-Code für die Seite hinzu (der PHP-Code wird in einem späteren Schritt hinzugefügt).

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

		?>
		</body>
		</html>

6. Fügen Sie innerhalb der PHP-Tags PHP-Code für die Verbindung zur Datenbank hinzu.

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "user name";
		$pwd = "password";
		$db = "registration";
		// Connect to database.
		try {
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    Sie müssen erneut die Werte für <code>$user</code> und <code>$pwd</code> durch Ihren lokalen MySQL-Benutzernamen und das Kennwort aktualisieren.

7. Fügen Sie nach dem Datenbankverbindungscode den Code für die Eingabe der Registrierungsinformationen in die Datenbank hinzu.

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

8. Fügen Sie nach dem obigen Code den Code für das Abrufen der Daten von der Datenbank hinzu.

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

Nun können Sie zu ****http://localhost/registration/index.php** navigieren, um die Anwendung zu testen.

##Veröffentlichen der Anwendung

Nachdem Sie Ihre Anwendung lokal getestet haben, können Sie sie über Git in Ihrer App Service-Web-App veröffentlichen. Sie müssen jedoch zuerst die Datenbankverbindungsinformationen in der Anwendung aktualisieren. Aktualisieren Sie mithilfe der zuvor ermittelten Datenbankverbindungsinformationen (Abschnitt **Abrufen von SQL-Datenbank-Verbindungsinformationen**) die folgenden Informationen in den Dateien `createdatabase.php` **und** `index.php` mit den entsprechenden Werten:

	// DB connection info
	$host = "tcp:<value of Server>";
	$user = "<value of User Name>";
	$pwd = "<your password>";
	$db = "<value of SQL Database>";

> [AZURE.NOTE]Unter <code>$host</code> muss dem Serverwert die Zeichenfolge <code>tcp:</code> vorangestellt werden.


Sie können nun die Git-Veröffentlichung einrichten und die Anwendung veröffentlichen.

> [AZURE.NOTE]Hierbei handelt es sich um die gleichen Schritte, die auch weiter oben am Ende des Abschnitts **Erstellen einer Azure-Web-App und Einrichten der Git-Veröffentlichung** aufgeführt sind.


1. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git an Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer Anwendung (das Verzeichnis **registration**), und führen Sie die folgenden Befehle aus:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL for remote repository]
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

2. Wechseln Sie zu **http://[web-app-name].azurewebsites.net/createtable.php**, um die SQL-Datenbanktabelle für die Anwendung zu erstellen.
3. Navigieren Sie zu **http://[web-app-name].azurewebsites.net/index.php**, um die Anwendung zu verwenden.

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen.

##Veröffentlichen von Änderungen an der Anwendung

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git an Ihrem `PATH` befindet), wechseln Sie zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

3. Navigieren Sie zu **http://[web-app-name].azurewebsites.net/index.php**, um die Änderungen anzuzeigen.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715).




[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
 

<!---HONumber=Oct15_HO3-->