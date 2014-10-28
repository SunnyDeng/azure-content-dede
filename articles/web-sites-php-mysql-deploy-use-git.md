<properties linkid="develop-php-website-with-mysql-and-git" urlDisplayName="Web w/ MySQL + Git" pageTitle="PHP website with MySQL and Git - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use Git deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure website and deploy using Git" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-MySQL-Azure-Website erstellen und über Git bereitstellen. Dazu verwenden Sie [PHP][PHP], das MySQL-Befehlszeilentool (Teil von [MySQL][install-mysql]), einen Webserver und [Git][Git] auf Ihrem Computer. Die Anweisungen in diesem Lernprogramm lassen sich von jedem Betriebssystem aus befolgen, einschließlich Windows, Max und Linux. Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Azure ausgeführte PHP-MySQL-Website.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und einer MySQL-Datenbank über das Azure-Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
-   Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von Git in Azure

Mithilfe dieses Lernprogramms erstellen Sie eine einfache Webanwendung für die Registrierung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Azure-PHP-Website][Azure-PHP-Website]

<div class="dev-callout"><strong>Hinweis</strong> <p>Um dieses Lernprogramm abzuschlie&szlig;en, ben&ouml;tigen Sie ein Azure-Konto, f&uuml;r das die Funktion Azure-Websites aktiviert ist. Wenn Sie noch kein Konto haben, k&ouml;nnen Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A74E0F923" target="_blank">Kostenlose Azure-Testversion</a>.</p> </div>

## Einrichten der Entwicklungsumgebung

Voraussetzung für dieses Tutorium sind [PHP][PHP], das MySQL-Befehlszeilentool (Teil von [MySQL][install-mysql]), ein Webserver und [Git][Git] auf Ihrem Computer.

> [WACOM.NOTE]
> Sollten Sie dieses Lernprogramm unter Windows ausführen, können Sie Ihren Computer für PHP einrichten und IIS (den integrierten Webserver in Windows) automatisch konfigurieren, indem Sie das [Azure-SDK für PHP][Azure-SDK für PHP] installieren.

## <span id="create-web-site-and-set-up-git"></span></a>Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung

Befolgen Sie diese Schritte, um eine Azure-Website und eine MySQL-Datenbank zu erstellen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links im Portal auf das Symbol **Neu**.

    ![Neue Azure-Website erstellen][Neue Azure-Website erstellen]

3.  Klicken Sie auf **WebSite** und dann auf **Benutzerdefiniert erstellen**.

    ![Neue Website benutzerdefiniert erstellen][Neue Website benutzerdefiniert erstellen]

    Geben Sie einen Wert für **URL** ein, wählen Sie **Neue MySQL-Datenbank erstellen** aus der Dropdownliste **Datenbank** aus, und wählen Sie das Datencenter für Ihre Website aus der Dropdownliste **Region** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Websitedetails eingeben][Websitedetails eingeben]

4.  Geben Sie einen Wert für den **Namen** Ihrer Datenbank ein, wählen Sie das Datencenter für Ihre Datenbank aus der Dropdown-Liste **Region** aus, und aktivieren Sie das Kontrollkästchen zur Zustimmung zu den rechtlichen Bedingungen. Klicken Sie auf das Häkchen unten im Dialogfeld.

    ![Neue MySQL-Datenbank erstellen][Neue MySQL-Datenbank erstellen]

    Nachdem die Website erstellt wurde, wird in etwa der Text **Website "[WEBSITENAME]" erfolgreich erstellt** angezeigt. Nun können Sie die Git-Veröffentlichung aktivieren.

5.  Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt ist, um das **QuickStart**-Dashboard der Website zu öffnen.

    ![Website-Dashboard öffnen][Website-Dashboard öffnen]

6.  Klicken Sie unten auf der Seite **QuickStart** auf **Set up Git publishing** (Git-Veröffentlichung einrichten).

    ![Git-Veröffentlichung einrichten][Git-Veröffentlichung einrichten]

7.  Um die Git-Veröffentlichung aktivieren zu können, müssen Sie einen Benutzernamen und ein Kennwort angeben. Notieren Sie sich den Benutzernamen und das Kennwort, die Sie erstellen. (Wenn Sie schon einmal ein Git-Verzeichnis eingerichtet haben, wird dieser Schritt übersprungen.)

    ![Anmeldedaten für die Veröffentlichung erstellen][Anmeldedaten für die Veröffentlichung erstellen]

    Das Einrichten des Verzeichnisses dauert ein paar Sekunden.

8.  Wenn Ihr Repository bereit ist, werden Anweisungen zum Pushen Ihrer Anwendungsdateien zum Repository angezeigt. Notieren Sie diese Anweisungen, da sie später benötigt werden.

    ![Git-Anweisungen][Git-Anweisungen]

## Abrufen von MySQL-Remoteverbindungsinformationen

Für die Herstellung einer Verbindung mit der auf Azure-Websites ausgeführten MySQL-Datenbank benötigen Sie die Verbindungsinformationen. Befolgen Sie die folgenden Schritte, um MySQL-Verbindungsinformationen abzurufen:

1.  Klicken Sie im Dashboard Ihrer Website auf den Link **Verbindungszeichenfolgen anzeigen** rechts auf der Seite:

    ![Datenbankverbindungsinformationen abrufen][Datenbankverbindungsinformationen abrufen]

2.  Notieren Sie sich die Werte für `Database`, `Data Source`, `User Id` und `Password`.

## Lokales Erstellen und Testen der Anwendung

Nach der Erstellung einer Azure-Website können Sie Ihre Anwendung lokal entwickeln und nach dem Testen bereitstellen.

Die Registrierungsanwendung ist eine einfache PHP-Anwendung, die Ihnen die Registrierung für eine Veranstaltung durch Angabe Ihres Namens und Ihrer E-Mail-Adresse ermöglicht. Informationen zu vorherigen Registranten werden in einer Tabelle angezeigt. Registrierungsinformationen werden in einer MySQL-Datenbank gespeichert. Die Anwendung besteht aus einer Datei (unten stehenden Code kopieren/einfügen):

-   **index.php**: Zeigt ein Registrierungsformular und eine Tabelle mit Registranteninformationen an.

Befolgen Sie die unten stehenden Schritte, um die Anwendung lokal zu erstellen und auszuführen. Beachten Sie, dass Voraussetzung für diese Schritte ist, dass PHP, das MySQL-Befehlszeilentool (Teil von MySQL) und ein Webserver auf Ihrem lokalen Computer eingerichtet sind und die [PDO-Erweiterung für MySQL][PDO-Erweiterung für MySQL] aktiviert ist.

1.  Stellen Sie eine Verbindung mit dem MySQL-Remoteserver her, indem Sie den zuvor abgerufenen Wert für `Data Source`, `User Id`, `Password` und `Database` verwenden:

        mysql -h{Data Source] -u[User Id] -p[Password] -D[Database]

2.  Die MySQL-Eingabeaufforderung wird angezeigt:

        mysql>

3.  Fügen Sie den folgenden Befehl `CREATE TABLE`-Befehl ein, um die Tabelle `registration_tbl` in Ihrer Datenbank zu erstellen:

        mysql> CREATE TABLE registration_tbl(id INT NOT NULL AUTO_INCREMENT, PRIMARY KEY(id), name VARCHAR(30), email VARCHAR(30), date DATE);

4.  Erstellen Sie im Stammverzeichnis Ihres Webservers einen Ordner mit dem Namen `registration`, und erstellen Sie darin eine Datei mit dem Namen `index.php`.

5.  Öffnen Sie die Datei **index.php** in einem Texteditor oder einer IDE, und fügen Sie den folgenden Code ein. Nehmen Sie dann die erforderlichen Änderungen gemäß den `//TODO:`-Kommentaren vor.

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

Nun können Sie **<http://localhost/registration/index.php>** aufrufen, um die Anwendung zu testen.

## Veröffentlichen der Anwendung

Nachdem Sie Ihre Anwendung lokal getestet haben, können Sie sie über Git auf ihrer Azure-Website veröffentlichen. Sie initialisieren Ihr lokales Git-Verzeichnis und veröffentlichen die Anwendung.

> [WACOM.NOTE]
> Dies sind dieselben Schritte, die am Ende des obigen Abschnitts „Erstellen einer Azure-Website und Einrichten der Git-Veröffentlichung“ im Portal gezeigt werden.

1.  (Optional) Sollten Sie Ihre Git-Remoteverzeichnis-URL vergessen oder verlegt haben, rufen Sie die Registerkarte zur Bereitstellung im Portal auf.

    ![Git-URL abrufen][Git-Anweisungen]

2.  Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), ändern Sie die Verzeichnisse in das Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

        git init
        git add .
        git commit -m "initial commit"
        git remote add azure [URL for remote repository]
        git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

    ![Anfängliche Push-Übertragung über Git zu Azure][Anfängliche Push-Übertragung über Git zu Azure]

3.  Rufen Sie **[http://[site][http://[site] name].azurewebsites.net/index.php** auf, um die Anwendung zu verwenden (diese Informationen werden in Ihrem Konto-Dashboard gespeichert):

    ![Azure-PHP-Website][Azure-PHP-Website]

Nach Veröffentlichung Ihrer Anwendung können Sie Änderungen an ihr vornehmen und diese über Git veröffentlichen.

## Veröffentlichen von Änderungen an der Anwendung

Befolgen Sie die folgenden Schritte, um Änderungen an der Anwendung zu veröffentlichen:

1.  Nehmen Sie lokal Änderungen an Ihrer Anwendung vor.
2.  Öffnen Sie GitBash (oder eine Terminalsitzung, wenn sich Git in Ihrem `PATH` befindet), ändern Sie die Verzeichnisse in das Stammverzeichnis Ihrer Anwendung, und führen Sie die folgenden Befehle aus:

        git add .
        git commit -m "comment describing changes"
        git push azure master

    Sie werden aufgefordert, das zuvor erstellte Kennwort einzugeben.

    ![Push-Übertragung von Websiteänderungen über Git zu Azure][Push-Übertragung von Websiteänderungen über Git zu Azure]

3.  Rufen Sie **[http://[site][http://[site] name].azurewebsites.net/index.php** auf, um Ihre Anwendung und etwaige Änderungen anzuzeigen:

    ![Azure-PHP-Website][Azure-PHP-Website]

4.  Sie können die neue Bereitstellung auch auf der Registerkarte zu Bereitstellungen im Azure-Verwaltungsportal anzeigen:

    ![Liste der Websitebereitstellungen][Liste der Websitebereitstellungen]

  [PHP]: http://www.php.net/manual/en/install.php
  [Git]: http://git-scm.com/
  [Azure-PHP-Website]: ./media/web-sites-php-mysql-deploy-use-git/running_app_2.png
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A74E0F923
  [Azure-SDK für PHP]: http://www.microsoft.com/web/handlers/webpi.ashx/getinstaller/azurephpsdk.appids
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Neue Azure-Website erstellen]: ./media/web-sites-php-mysql-deploy-use-git/new_website.jpg
  [Neue Website benutzerdefiniert erstellen]: ./media/web-sites-php-mysql-deploy-use-git/custom_create.png
  [Websitedetails eingeben]: ./media/web-sites-php-mysql-deploy-use-git/website_details.jpg
  [Neue MySQL-Datenbank erstellen]: ./media/web-sites-php-mysql-deploy-use-git/new_mysql_db.jpg
  [Website-Dashboard öffnen]: ./media/web-sites-php-mysql-deploy-use-git/go_to_dashboard.png
  [Git-Veröffentlichung einrichten]: ./media/web-sites-php-mysql-deploy-use-git/setup_git_publishing.png
  [Anmeldedaten für die Veröffentlichung erstellen]: ./media/web-sites-php-mysql-deploy-use-git/git-deployment-credentials.png
  [Git-Anweisungen]: ./media/web-sites-php-mysql-deploy-use-git/git-instructions.png
  [Datenbankverbindungsinformationen abrufen]: ./media/web-sites-php-mysql-deploy-use-git/connection_string_info.png
  [PDO-Erweiterung für MySQL]: http://www.php.net/manual/en/ref.pdo-mysql.php
  [Anfängliche Push-Übertragung über Git zu Azure]: ./media/web-sites-php-mysql-deploy-use-git/php-git-initial-push.png
  [http://[site]: http://[site
  [Push-Übertragung von Websiteänderungen über Git zu Azure]: ./media/web-sites-php-mysql-deploy-use-git/php-git-change-push.png
  [Liste der Websitebereitstellungen]: ./media/web-sites-php-mysql-deploy-use-git/php-deployments-list.png
