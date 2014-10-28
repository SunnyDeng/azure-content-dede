<properties linkid="develop-php-website-with-mysql-and-ftp" urlDisplayName="Web w/ MySQL + FTP" pageTitle="PHP website with MySQL and FTP - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to create a PHP website that stores data in MySQL and use FTP deployment to Azure." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create a PHP-MySQL Azure Website and Deploy Using FTP" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-MySQL-Azure-Website erstellen und über FTP bereitstellen. Dieses Lernprogramm setzt voraus, dass Sie [PHP][PHP], [MySQL][MySQL], einen Webserver und einen FTP-Client auf Ihrem Computer installiert haben. Die Anweisungen in diesem Lernprogramm lassen sich von jedem Betriebssystem aus befolgen, einschließlich Windows, Max und Linux. Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Azure ausgeführte PHP-MySQL-Website.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und einer MySQL-Datenbank über das Azure-Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
-   Veröffentlichen der Anwendung in Azure über FTP.

Mithilfe dieses Lernprogramms erstellen Sie eine einfache Webanwendung für die Registrierung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der vollständigen Anwendung:

![Azure-PHP-Website][Azure-PHP-Website]

[WACOM.INCLUDE [create-account-and-websites-note][create-account-and-websites-note]]

## Erstellen einer Azure-Website und Einrichten der FTP-Veröffentlichung

Führen Sie die folgenden Schritte aus, um eine Azure-Website und eine MySQL-Datenbank zu erstellen:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Neue Azure-Website erstellen][Neue Azure-Website erstellen]

3.  Klicken Sie auf **WEBSITE** und dann auf **BENUTZERDEFINIERT ERSTELLEN**.

    ![Neue Website benutzerdefiniert erstellen][Neue Website benutzerdefiniert erstellen]

    Geben Sie einen Wert für **URL** ein, und wählen Sie **Neue MySQL-Datenbank erstellen** aus der Dropdownliste **DATENBANK** und das Rechenzentrum für Ihre Website aus der Dropdownliste **REGION** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Websitedetails eingeben][Websitedetails eingeben]

4.  Geben Sie einen Wert für den **NAMEN** Ihrer Datenbank ein, wählen Sie das Rechenzentrum für Ihre Datenbank aus der Dropdownliste **REGION** aus, und aktivieren Sie das Kontrollkästchen zur Zustimmung zu den rechtlichen Bedingungen. Klicken Sie auf das Häkchen unten im Dialogfeld.

    ![Neue MySQL-Datenbank erstellen][Neue MySQL-Datenbank erstellen]

    Nachdem die Website erstellt wurde, wird der Text **Die Erstellung der Website "[WEBSITENAME]" wurde erfolgreich abgeschlossen** angezeigt. Nun können Sie die FTP-Veröffentlichung aktivieren.

5.  Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt ist, um das Dashboard **Schnellstart** der Website zu öffnen.

    ![Website-Dashboard öffnen][Website-Dashboard öffnen]

6.  Klicken Sie unten auf der Seite **SCHNELLSTART** auf **Anmeldeinformationen für die Bereitstellung zurücksetzen**.

    ![Zurücksetzen der Bereitstellungsanmeldeinformationen][Zurücksetzen der Bereitstellungsanmeldeinformationen]

7.  Um die FTP-Veröffentlichung aktivieren zu können, müssen Sie einen Benutzernamen und ein Kennwort angeben. Notieren Sie sich den Benutzernamen und das Kennwort, die Sie erstellen.

    ![Anmeldedaten für die Veröffentlichung erstellen][Anmeldedaten für die Veröffentlichung erstellen]

## Lokales Erstellen und Testen der Anwendung

Die Registrierungsanwendung ist eine einfache PHP-Anwendung, die Ihnen die Registrierung für eine Veranstaltung durch Angabe Ihres Namens und Ihrer E-Mail-Adresse ermöglicht. Informationen zu vorherigen Registranten werden in einer Tabelle angezeigt. Registrierungsinformationen werden in einer MySQL-Datenbank gespeichert. Die Anwendung besteht aus zwei Dateien:

-   **index.php**: Zeigt ein Registrierungsformular und eine Tabelle mit Registranteninformationen an.
-   **createtable.php**: Erstellt die MySQL-Tabelle für die Anwendung. Diese Datei wird nur einmal verwendet.

Befolgen Sie die unten stehenden Schritte, um die Anwendung lokal zu erstellen und auszuführen. Beachten Sie, dass Voraussetzung für diese Schritte ist, dass PHP, MySQL und ein Webserver auf Ihrem lokalen Computer eingerichtet sind und die [PDO-Erweiterung für MySQL][PDO-Erweiterung für MySQL] aktiviert ist.

1.  Erstellen Sie eine MySQL-Datenbank namens `registration`. Sie können dies in der MySQL-Eingabeaufforderung mit diesem Befehl ausführen:

        mysql> create database registration;

2.  Erstellen Sie im Stammverzeichnis Ihres Webservers einen Ordner mit dem Namen `registration`, und erstellen Sie darin zwei Dateien mit den Namen `createtable.php` und `index.php`.

3.  Öffnen Sie die Datei `createtable.php` in einem Texteditor oder IDE, und fügen Sie den folgenden Code hinzu. Dieser Code wird verwendet, um die Tabelle `registration_tbl` in der Datenbank `registration` zu erstellen.

        <?php
        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        try{
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
            $sql = "CREATE TABLE registration_tbl(
                        id INT NOT NULL AUTO_INCREMENT, 
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

    > [WACOM.NOTE]
    > Sie müssen die Werte für `$user` und `$pwd` mit Ihrem lokalen MySQL-Benutzernamen und -Kennwort aktualisieren.

4.  Öffnen Sie einen Webbrowser und navigieren Sie zu [][]<http://localhost/registration/createtable.php></a>. Dadurch wird die Tabelle `registration_tbl` in der Datenbank erstellt.

5.  Öffnen Sie die Datei **index.php** in einem Texteditor oder IDE, und fügen Sie den Basis-HTML- und CSS-Code für die Seite hinzu (der PHP-Code wird in einem späteren Schritt hinzugefügt).

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

6.  Fügen Sie innerhalb der PHP-Tags PHP-Code für die Verbindung zur Datenbank hinzu.

        // DB connection info
        $host = "localhost";
        $user = "user name";
        $pwd = "password";
        $db = "registration";
        // Connect to database.
        try {
            $conn = new PDO( "mysql:host=$host;dbname=$db", $user, $pwd);
            $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );
        }
        catch(Exception $e){
            die(var_dump($e));
        }

    > [WACOM.NOTE]
    > Auch hier müssen Sie die Werte für `$user` und `$pwd` mit Ihrem lokalen MySQL-Benutzernamen und -Kennwort aktualisieren.

7.  Fügen Sie nach dem Datenbankverbindungscode den Code für die Eingabe der Registrierungsinformationen in die Datenbank hinzu.

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

8.  Fügen Sie nach dem obigen Code den Code für das Abrufen der Daten von der Datenbank hinzu.

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

Nun können Sie [][1]<http://localhost/registration/index.php></a> aufrufen, um die Anwendung zu testen.

## Abrufen der Verbindungsinformationen für MySQL und FTP

Für die Herstellung einer Verbindung mit der auf Azure-Websites ausgeführten MySQL-Datenbank benötigen Sie die Verbindungsinformationen. Befolgen Sie die folgenden Schritte, um MySQL-Verbindungsinformationen abzurufen:

1.  Klicken Sie im Dashboard Ihrer Website auf den Link **Verbindungszeichenfolgen anzeigen** rechts auf der Seite:

    ![Datenbankverbindungsinformationen abrufen][Datenbankverbindungsinformationen abrufen]

2.  Notieren Sie sich die Werte für `Database`, `Data Source`, `User Id` und `Password`.

3.  Klicken Sie im Dashboard Ihrer Website auf den Link **Veröffentlichungsprofil herunterladen** rechts unten auf der Seite:

    ![Veröffentlichungsprofil herunterladen][Veröffentlichungsprofil herunterladen]

4.  Öffnen Sie die Datei `.publishsettings` in einem XML-Editor.

5.  Suchen Sie das Element `<publishProfile >` mit `publishMethod="FTP"`, das ungefähr wie folgt aussieht:

        <publishProfile publishMethod="FTP" publishUrl="ftp://[mysite].azurewebsites.net/site/wwwroot" ftpPassiveMode="True" userName="[username]" userPWD="[password]" destinationAppUrl="http://[name].antdf0.antares-test.windows-int.net" 
            ...
        </publishProfile>

Notieren Sie sich die Attribute `publishUrl`, `userName` und `userPWD`.

## Veröffentlichen der Anwendung

Nachdem Sie Ihre Anwendung lokal getestet haben, können Sie sie über FTP auf Ihrer Azure-Website veröffentlichen. Sie müssen jedoch zuerst die Datenbankverbindungsinformationen in der Anwendung aktualisieren. Aktualisieren Sie mithilfe der Datenbank-Verbindungsinformationen, die Sie zuvor erhalten haben (im Abschnitt **Abrufen der Verbindungsinformationen für MySQL und FTP**), folgende Informationen in **beiden** Dateien `createdatabase.php` und `index.php` mit den entsprechenden Werten:

    // DB connection info
    $host = "value of Data Source";
    $user = "value of User Id";
    $pwd = "value of Password";
    $db = "value of Database";

Jetzt können Sie die Anwendung jetzt mithilfe von FTP veröffentlichen.

1.  Öffnen Sie den FTP-Client Ihrer Wahl.

2.  Geben Sie den *Hostnameteil* des Attributs `publishUrl` ein, das Sie oben im FTP-Client notiert haben.

3.  Geben Sie die Attribute `userName` und `userPWD` ein, die Sie oben unverändert im FTP-Client notiert haben.

4.  Stellen Sie eine Verbindung her.

Nachdem Sie eine Verbindung hergestellt haben, können Sie Dateien beliebig hoch- und herunterladen. Achten Sie darauf, dass Sie Dateien in das Stammverzeichnis `/site/wwwroot` hochladen.

Nachdem Sie sowohl `index.php` als auch `createtable.php` hochgeladen haben, navigieren Sie zu **http://[Sitename].azurewebsites.net/createtable.php**, um die MySQL-Tabelle für die Anwendung zu erstellen. Navigieren Sie anschließend zu **http://[Sitename].azurewebsites.net/index.php**, um die Anwendung zu nutzen.

  [PHP]: http://www.php.net/manual/en/install.php
  [MySQL]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
  [Azure-PHP-Website]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/running_app_2.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Neue Azure-Website erstellen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_website.jpg
  [Neue Website benutzerdefiniert erstellen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/custom_create.png
  [Websitedetails eingeben]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/website_details.jpg
  [Neue MySQL-Datenbank erstellen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/new_mysql_db.jpg
  [Website-Dashboard öffnen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/go_to_dashboard.png
  [Zurücksetzen der Bereitstellungsanmeldeinformationen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/reset-deployment-credentials.png
  [Anmeldedaten für die Veröffentlichung erstellen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/git-deployment-credentials.png
  [PDO-Erweiterung für MySQL]: http://www.php.net/manual/en/ref.pdo-mysql.php
  []: http://localhost/tasklist/createtable.php
  [1]: http://localhost/tasklist/index.php
  [Datenbankverbindungsinformationen abrufen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/connection_string_info.png
  [Veröffentlichungsprofil herunterladen]: ./media/web-sites-php-web-site-mysql-deploy-use-ftp/download_publish_profile_2.png
