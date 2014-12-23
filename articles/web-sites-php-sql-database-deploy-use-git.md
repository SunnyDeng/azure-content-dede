<properties urlDisplayName="Web w/ SQL + Git" pageTitle="PHP-Website mit SQL-Datenbank und Git - Azure-Lernprogramm" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in SQL Database and use Git deployment to Azure." metaCanonical="" services="web-sites,sql-database" documentationCenter="PHP" title="Create a PHP website with a SQL Database and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#Erstellen einer PHP-Website mit einer SQL-Datenbank und Bereitstellen mit Git

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-Azure-Website mit einer Azure-SQL-Datenbank erstellen und über Git bereitstellen. In diesem Lernprogramm wird davon ausgegangen, dass Sie [PHP][install-php], [SQL Server Express][install-SQLExpress], die [Microsoft-Treiber für SQL Server für PHP][install-drivers], einen Webserver und [Git][install-git] auf dem Computer installiert haben. Nach der Durchführung dieses Lernprogramms verfügen Sie über eine in Azure ausgeführte PHP-SQL-Datenbank.

> [WACOM.NOTE]
> Sie können PHP, SQL Server Express, die Microsoft-Treiber für SQL Server für PHP und Internet Information Services (IIS) mit dem <a href="http://www.microsoft.com/web/downloads/platform.aspx">Microsoft Web Platform Installer</a>.

You will learn:

* How to create an Azure Website and a SQL Database using the Azure Management Portal. Because PHP is enabled in Azure Websites by default, nothing special is required to run your PHP code.
* How to publish and re-publish your application to Azure using Git.
 
By following this tutorial, you will build a simple registration web application in PHP. The application will be hosted in an Azure Website. A screenshot of the completed application is below:

![Azure PHP Web Site][running-app]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]


##Create an Azure Website and set up Git publishing

Follow these steps to create an Azure Website and a SQL Database:

1. Login to the [Azure Management Portal][management-portal].
2. Click the **New** icon on the bottom left of the portal.
![Create New Azure Web Site][new-website]

3. Click **Website**, then **Custom Create**.

	![Custom Create a new Web Site][custom-create]

	Enter a value for **URL**, select **Create a New SQL Database** from the **Database** dropdown,  and select the data center for your website in the **Region** dropdown. Click the arrow at the bottom of the dialog.

	![Fill in web site details][website-details-sqlazure]

4. Enter a value for the **Name** of your database, select the **Edition** [(WEB or BUSINESS)][sql-database-editions], select the **Max Size** for your database, choose the **Collation**, and select **NEW SQL Database server**. Click the arrow at the bottom of the dialog.

	![Fill in SQL Database settings][database-settings]

5. Enter an administrator name and password (and confirm the password), choose the region in which your new SQL Database server will be created, and check the `Allow Azure Services to access the server` box.

	![Create new SQL Database server][create-server]

	When the website has been created you will see the text **Creation of Website "[SITENAME]" completed successfully**. Now, you can enable Git publishing.

6. Click the name of the website displayed in the list of websites to open the website's Quick Start dashboard.

	![Open web site dashboard][go-to-dashboard]


7. At the bottom of the Quick Start page, click **Set up deployment from source control**. 

	![Set up Git publishing][setup-git-publishing]

6. When asked "Where is your source code?" select **Local Git repository**, and then click the arrow.

	![where is your source code][where-is-code]

8. To enable Git publishing, you must provide a user name and password. Make a note of the user name and password you create. (If you have set up a Git repository before, this step will be skipped.)

	![Create publishing credentials][credentials]

	It will take a few seconds to set up your repository.

9. When your repository is ready, you will see instructions for pushing your application files to the repository. Make note of these instructions - they will be needed later.

	![Git instructions][git-instructions]

##Get SQL Database connection information

To connect to the SQL Database instance that is running in Azure Websites, your will need the connection information. To get SQL Database connection information, follow these steps:

1. From the Azure Management Portal, click **Linked Resources**, then click the database name.

	![Linked Resources][linked-resources]

2. Click **View connection strings**.

	![Connection string][connection-string]
	
3. From the **PHP** section of the resulting dialog, make note of the values for `SERVER`, `DATABASE`, and `USERNAME`.

##Build and test your application locally

The Registration application is a simple PHP application that allows you to register for an event by providing your name and email address. Information about previous registrants is displayed in a table. Registration information is stored in a SQL Database instance. The application consists of two files (copy/paste code available below):

* **index.php**: Displays a form for registration and a table containing registrant information.
* **createtable.php**: Creates the SQL Database table for the application. This file will only be used once.

To run the application locally, follow the steps below. Note that these steps assume you have PHP, SQL Server Express, and a web server set up on your local machine, and that you have enabled the [PDO extension for SQL Server][pdo-sqlsrv].

1. Create a SQL Server database called `registration`. You can do this from the `sqlcmd` command prompt with these commands:

		>sqlcmd -S localhost\sqlexpress -U <local user name> -P <local password>
		1> create database registration
		2> GO	


2. In your web server's root directory, create a folder called `registration` and create two files in it - one called `createtable.php` and one called `index.php`.

3. Open the `createtable.php` file in a text editor or IDE and add the code below. This code will be used to create the `registration_tbl` table in the `registration` database.

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

	Note that you will need to update the values for <code>$user</code> and <code>$pwd</code> with your local SQL Server user name and password.

4. Open a web browser and browse to **http://localhost/registration/createtable.php**. This will create the `registration_tbl` table in the database.

5. Open the **index.php** file in a text editor or IDE and add the basic HTML and CSS code for the page (the PHP code will be added in later steps).

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
		try{
			$conn = new PDO( "sqlsrv:Server= $host ; Database = $db ", $user, $pwd);
			$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
		}
		catch(Exception $e){
			die(var_dump($e));
		}

    Sie müssen erneut die Werte für <code>$user</code> und <code>$pwd</code> durch Ihren lokalen MySQL-Benutzernamen und das Kennwort aktualisieren.

7. Fügen Sie nach dem Datenbankverbindungscode den Code für die Eingabe der Registrierungsinformationen in die Datenbank hinzu.

		if(!empty($_POST)) {
		try{
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
		catch(Exception $e){
			die(var_dump($e));
		}
		echo "<h3>Sie sind registriert!</h3>";
		}

8. Fügen Sie nach dem obigen Code den Code für das Abrufen der Daten von der Datenbank hinzu.

		$sql_select = "SELECT * FROM registration_tbl";
		$stmt = $conn->query($sql_select);
		$registrants = $stmt->fetchAll(); 
		if(count($registrants) > 0) {
			echo "<h2>Registrierte Personen:</h2>";
			echo "<table>";
			echo "<tr><th>Name</th>";
			echo "<th>E-Mail</th>";
			echo "<th>Datum</th></tr>";
			foreach($registrants as $registrant) {
				echo "<tr><td>".$registrant['name']."</td>";
				echo "<td>".$registrant['email']."</td>";
				echo "<td>".$registrant['date']."</td></tr>";
		    }
			echo "</table>";
		} else {
			echo "<h3>Derzeit ist niemand registriert.</h3>";
		}

Nun können Sie **http://localhost/registration/index.php** aufrufen, um die Anwendung zu testen.

##Veröffentlichen der Anwendung

Nachdem Sie Ihre Anwendung lokal getestet haben, können Sie sie über Git auf ihrer Azure-Website veröffentlichen. Sie müssen jedoch zuerst die Datenbankverbindungsinformationen in der Anwendung aktualisieren. Aktualisieren Sie mithilfe der Datenbankverbindungsinformationen, die Sie zuvor erhalten haben (im Abschnitt **Abrufen von SQL-Datenbank-Verbindungsinformationen**), folgende Informationen in **beiden** Dateien "createdatabase.php" und "index.php" mit den entsprechenden Werten:

	// DB connection info
	$host = "tcp:<Wert von SERVER>";
	$user = "<Wert von USERNAME>@<server ID>";
	$pwd = "<Ihr Kennwort>";
	$db = "<Wert von DATABASE>";

> [WACOM.NOTE]
> In <code>$host</code> muss dem Wert "SERVER" <code>tcp:</code> vorangestellt werden. Der Wert von <code>$user</code> ist die Verkettung des Werts von "USERNAME", "@" und Ihrer Server-ID. Ihre Server-ID sind die ersten zehn Zeichen des Werts von "SERVER".


Sie können nun die Git-Veröffentlichung einrichten und die Anwendung veröffentlichen.

> [WACOM.NOTE]
> Dies sind dieselben Schritte, die am Ende des obigen Abschnitts "Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung" aufgeführt sind.


1. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem "PATH" befindet), ändern Sie die Verzeichnisse zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL für Remote-Repository]
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

2. Wechseln Sie zu **http://[Websitename].azurewebsites.net/createtable.php**, um die MySQL-Tabelle für die Anwendung zu erstellen.
3. Wechseln Sie zu **http://[Websitename].azurewebsites.net/index.php**, um die Anwendung zu verwenden.

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen. 

##Veröffentlichen von Änderungen an der Anwendung

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem "PATH" befindet), ändern Sie die Verzeichnisse zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

3. Wechseln Sie zu **http://[Websitename].azurewebsites.net/index.php**, um die Änderungen anzuzeigen.

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/en-us/download/details.aspx?id=20098
[install-git]: http://git-scm.com/
[pdo-sqlsrv]: http://php.net/pdo_sqlsrv
[running-app]: ./media/web-sites-php-sql-database-deploy-use-git/running_app_3.png
[new-website]: ./media/web-sites-php-sql-database-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-sql-database-deploy-use-git/custom_create.png
[website-details-sqlazure]: ./media/web-sites-php-sql-database-deploy-use-git/website_details_sqlazure.jpg
[database-settings]: ./media/web-sites-php-sql-database-deploy-use-git/database_settings.jpg
[create-server]: ./media/web-sites-php-sql-database-deploy-use-git/create_server.jpg
[go-to-dashboard]: ./media/web-sites-php-sql-database-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-sql-database-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-sql-database-deploy-use-git/git-deployment-credentials.png


[Git-Anweisungen]: ./media/web-sites-php-sql-database-deploy-use-git/git-instructions.png
[linked-resources]: ./media/web-sites-php-sql-database-deploy-use-git/linked_resources.jpg
[connection-string]: ./media/web-sites-php-sql-database-deploy-use-git/connection_string.jpg
[management-portal]: https://manage.windowsazure.com/
[sql-database-editions]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621788.aspx
[where-is-code]: ./media/web-sites-php-sql-database-deploy-use-git/where_is_code.png
