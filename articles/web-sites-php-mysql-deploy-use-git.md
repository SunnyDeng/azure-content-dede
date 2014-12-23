<properties urlDisplayName="Web w/ MySQL + Git" pageTitle="PHP-Website mit MySQL und Git - Azure-Lernprogramm" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm" />

#Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-MySQL-Azure-Website erstellen und über Git bereitstellen. Sie verwenden [PHP][install-php], das MySQL-Befehlszeilentool (Teil von [MySQL][install-mysql]), einen Webserver und das auf Ihrem Computer installierte [Git][install-git]. Die Anweisungen in diesem Lernprogramm lassen sich von jedem Betriebssystem aus befolgen, einschließlich Windows, Max und Linux. Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Azure ausgeführte PHP-MySQL-Website.
 
Sie erhalten Informationen zu folgenden Themen:

* Erstellen einer Azure-Website und einer MySQL-Datenbank über das Azure-Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
* Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von Git in Azure
 
Mithilfe dieses Lernprogramms erstellen Sie eine einfache Webanwendung für die Registrierung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Azure PHP web site][running-app]

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie benötigen ein Azure-Konto, bei dem die Azure-Websites-Funktion aktiviert ist, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten dazu finden Sie hier:<a href="http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Azure Free Trial</a>.</p> </div>

##Set up the development environment

This tutorial assumes you have [PHP][install-php], the MySQL Command-Line Tool (part of [MySQL][install-mysql]), a web server, and [Git][install-git] installed on your computer.

> [WACOM.NOTE]
> If you are performing this tutorial on Windows, you can set up your machine for PHP and automatically configure IIS (the built-in web server in Windows) by installing the <a href="http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids">Azure SDK for PHP</a>.

##<a id="create-web-site-and-set-up-git"></a>Create an Azure website and set up Git publishing

Follow these steps to create an Azure website and a MySQL database:

1. Login to the [Azure Management Portal][management-portal].
2. Click the **New** icon on the bottom left of the portal.

	![Create New Azure web site][new-website]

3. Click **WebSite**, then **Custom Create**.

	![Custom Create a new web site][custom-create]
	
	Enter a value for **URL**, select **Create a New MySQL Database** from the **Database** dropdown,  and select the data center for your website in the **Region** dropdown. Click the arrow at the bottom of the dialog.

	![Fill in web site details][website-details]

4. Enter a value for the **Name** of your database, select the data center for your database in the **Region** dropdown, and check the box that indicates you agree with the legal terms. Click the checkmark at the bottom of the dialog.

	![Create new MySQL database][new-mysql-db]

	When the website has been created you will see the text **Creation of Website "[SITENAME]" completed successfully**. Now, you can enable Git publishing.

6. Click the name of the website displayed in the list of websites to open the website's **QuickStart** dashboard.

	![Open web site dashboard][go-to-dashboard]


7. At the bottom of the **QuickStart** page, click **Set up Git publishing**. 

	![Set up Git publishing][setup-git-publishing]

8. To enable Git publishing, you must provide a user name and password. Make a note of the user name and password you create. (If you have set up a Git repository before, this step will be skipped.)

	![Create publishing credentials][credentials]

	It will take a few seconds to set up your repository.

9. When your repository is ready, you will see instructions for pushing your application files to the repository. Make note of these instructions - they will be needed later.

	![Git instructions][git-instructions]

##Get remote MySQL connection information

To connect to the MySQL database that is running in Azure Websites, your will need the connection information. To get MySQL connection information, follow these steps:

1. From your website's dashboard, click the **View connection strings** link on the right side of the page:

	![Get database connection information][connection-string-info]
	
2. Make note of the values for `Database`, `Data Source`, `User Id`, and `Password`.

##Build and test your application locally

Now that you have created an Azure Website, you can develop your application locally, then deploy it after testing. 

The Registration application is a simple PHP application that allows you to register for an event by providing your name and email address. Information about previous registrants is displayed in a table. Registration information is stored in a MySQL database. The application consists of one file (copy/paste code available below):

* **index.php**: Displays a form for registration and a table containing registrant information.

To build and run the application locally, follow the steps below. Note that these steps assume you have PHP, the MySQL Command-Line Tool (part of MySQL), and a web server set up on your local machine, and that you have enabled the [PDO extension for MySQL][pdo-mysql].

1. Connect to the remote MySQL server, using the value for `Data Source`, `User Id`, `Password`, and `Database` that you retrieved earlier:

		mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2. The MySQL command prompt will appear:

		mysql>

3. Paste in the following `CREATE TABLE` command to create the `registration_tbl` table in your database:

		mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4. In your web server's root directory, create a folder called `registration` and create a file in it called `index.php`.

5. Open the **index.php** file in a text editor or IDE and add the following code, and complete the necessary changes marked with `//TODO:` comments.


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
			//TODO: Aktualisieren Sie die Werte für $host, $user, $pwd und $db
			//using the values you retrieved earlier from the portal.
			$host = "value of Data Source";
			$user = "value of User Id";
			$pwd = "value of Password";
			$db = "value of Database";
			// Connect to database.
			try{
				$conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
				$conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
			}
			catch(Exception $e){
				die(var_dump($e));
			}
			// Insert registration info
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
			// Retrieve data
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
		?>
		</body>
		</html>

Nun können Sie **http://localhost/registration/index.php** aufrufen, um die Anwendung zu testen.


##Veröffentlichen der Anwendung

Nachdem Sie Ihre Anwendung lokal getestet haben, können Sie sie über Git auf ihrer Azure-Website veröffentlichen. Sie initialisieren Ihr lokales Git-Verzeichnis und veröffentlichen die Anwendung.

> [WACOM.NOTE]
> Dies sind dieselben Schritte, die am Ende des obigen Abschnitts "Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung" im Portal gezeigt werden.

1. (Optional) Sollten Sie Ihre Git-Remoteverzeichnis-URL vergessen oder verlegt haben, rufen Sie die Registerkarte zur Bereitstellung im Portal auf.
	
	![Get Git URL][Git-Anweisungen]

1. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem "PATH" befindet), ändern Sie die Verzeichnisse zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git init
		git add .
		git commit -m "initial commit"
		git remote add azure [URL für Remote-Repository]
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

	![Initial Push to Azure via Git][git-initial-push]

2. Rufen Sie **http://[Websitename].azurewebsites.net/index.php**  auf, um die Anwendung zu verwenden (diese Informationen werden in Ihrem Konto-Dashboard gespeichert):

	![Azure PHP web site][running-app]

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen. 

##Veröffentlichen von Änderungen an der Anwendung

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1. Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2. Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem "PATH" befindet), ändern Sie die Verzeichnisse zum Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

		git add .
		git commit -m "comment describing changes"
		git push azure master

	Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

	![Pushing site changes to Azure via Git][git-change-push]

3. Rufen Sie **http://[Websitename].azurewebsites.net/index.php** auf, um Ihre Anwendung und etwaige Änderungen anzuzeigen:

	![Azure PHP web site][running-app]

4. Sie können die neue Bereitstellung auch auf der Registerkarte zu Bereitstellungen im Azure-Verwaltungsportal anzeigen:

	![List of web site deployments][deployments-list]

[install-php]: http://www.php.net/manual/en/install.php
[install-SQLExpress]: http://www.microsoft.com/en-us/download/details.aspx?id=29062
[install-Drivers]: http://www.microsoft.com/en-us/download/details.aspx?id=20098
[install-git]: http://git-scm.com/

[pdo-mysql]: http://www.php.net/manual/en/ref.pdo-mysql.php
[running-app]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
[new-website]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
[custom-create]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
[website-details]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
[new-mysql-db]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
[go-to-dashboard]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
[setup-git-publishing]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
[credentials]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png


[Git-Anweisungen]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
[git-change-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
[git-initial-push]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
[deployments-list]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
[connection-string-info]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
[management-portal]: https://manage.windowsazure.com
[sql-database-editions]: http://msdn.microsoft.com/en-us/library/windowsazure/ee621788.aspx
