<properties linkid="develop-php-website-with-sql-database-and-webmatrix" urlDisplayName="Web w/ SQL + WebMatrix" pageTitle="PHP web site with SQL Database and WebMatrix - Azure" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP web site that stores data in SQL Database." metaCanonical="" services="" documentationCenter="" title="Create and Deploy a PHP Web Site and SQL Database using WebMatrix" authors="" solutions="" manager="" editor="mollybos" />

Erstellen und Bereitstellen einer PHP-Website und SQL-Datenbank mit WebMatrix
=============================================================================

In diesem Lernprogramm wird gezeigt, wie Sie WebMatrix nutzen, um eine PHP-Anwendung, die eine Azure-SQL-Datenbank verwendet, zu entwickeln und auf einer Azure-Website bereitzustellen. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix unterstützt PHP und enthält IntelliSense für die PHP-Entwicklung.

Dieses Lernprogramm geht davon aus, dass Sie [SQL Server Express](http://www.microsoft.com/en-us/download/details.aspx?id=29062) auf Ihrem Computer installiert haben, sodass Sie eine Anwendung lokal testen können. Sie können das Lernprogramm jedoch auch durchführen, ohne SQL Server Express installiert zu haben. Sie können stattdessen die Anwendung auch direkt in Azure-Websites bereitstellen.

Nach der Durchführung dieses Lernprogramms haben Sie eine in Azure ausgeführte PHP-SQL-Datenbank.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und einer SQL-Datenbank über das Azure-Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
-   Entwickeln einer PHP-Anwendung mithilfe von WebMatrix.
-   Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von WebMatrix in Azure.

Mithilfe dieses Lernprogramms erstellen Sie eine einfache Tasklist-Webanwendung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der laufenden Anwendung:

![Azure-PHP-Website](./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

Voraussetzungen
---------------

1.  [Herunterladen](http://go.microsoft.com/fwlink/?LinkId=252504) der Tasklist-Anwendungsdateien. Die Tasklist-Anwendung ist eine einfache PHP-Anwendung, mit der Sie Elemente einer Aufgabenliste hinzufügen, als erledigt markieren und löschen können. Elemente der Aufgabenliste werden in einer SQL-Datenbank gespeichert (SQL Server Express für lokales Testen). Die Anwendung besteht aus diesen Dateien:

-   **index.php**: Zeigt Aufgaben an und bietet ein Formular für das Hinzufügen eines Elements zur Liste.
-   **additem.php**: Fügt ein Element zur Liste hinzu.
-   **getitems.php**: Ruft alle Elemente in der Datenbank ab.
-   **markitemcomplete.php**: Ändert den Status eines Elements in erledigt.
-   **deleteitem.php**: Löscht ein Element.
-   **taskmodel.php**: Enthält Funktionen, welche Elemente aus der Datenbank hinzufügen, abrufen, aktualisieren und löschen.
-   **createtable.php**: Erstellt die SQL-Datenbanktabelle für die Anwendung. Diese Datei wird nur einmal abgerufen.

1.  Erstellen Sie eine SQL Server-Datenbank namens `tasklist`. Sie können dies in der `sqlcmd`-Eingabeaufforderung mit diesen Befehlen ausführen:

         >sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
         1> create database tasklist
         2> GO

    Dieser Schritt ist nur erforderlich, wenn Sie die Anwendung lokal testen möchten.

Erstellen einer Website und SQL-Datenbank
-----------------------------------------

1.  Melden Sie sich am [Verwaltungsportal](https://manage.windowsazure.com) an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ New**.

    ![Neue Azure-Website erstellen](./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg)

3.  Klicken Sie auf **WEBSITE** und dann auf **CUSTOM CREATE**.

    ![Neue Website benutzerdefiniert erstellen](./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png)

    Geben Sie einen Wert für **URL** ein, wählen Sie die Option zum Erstellen einer neuen SQL-Datenbank**** aus der Dropdown-Liste **DATENBANK** aus, und wählen Sie das Datencenter für Ihre Website aus der Dropdown-Liste **REGION** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Websitedetails eingeben](./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png)

4.  Geben Sie einen Wert für den **NAMEN** der Datenbank ein, und wählen Sie **NEW SQL Database server**. Geben Sie einen Anmeldenamen und ein Kennwort für den Server ein (und bestätigen Sie das Kennwort). Wählen Sie die Region, in der der neue SQL-Datenbankserver erstellt wird.

    ![SQL-Datenbankeinstellungen eingeben](./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png)

    Wenn die Website erstellt wurde, wird der Text **Creation of Web Site "[SITENAME]" succeeded** angezeigt. Als nächstes erhalten Sie die Verbindungsinformationen für die Datenbank.

5.  Klicken Sie auf **LINKED RESOURCES** und anschließend auf den Namen der Datenbank.

    ![Verknüpfte Ressourcen](./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png)

6.  Klicken Sie auf **View connection strings**.

    ![Verbindungszeichenfolge](./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png)

Notieren Sie sich im Abschnitt **PHP** des folgenden Dialogfelds die Werte für `UID`, `PWD`, `Database` und `$serverName`. Sie werden diese Informationen später verwenden.

Installieren von WebMatrix
--------------------------

Sie können WebMatrix aus dem [Verwaltungsportal](https://manage.windowsazure.com) installieren.

1.  Navigieren Sie nach der Anmeldung zur Schnellstartseite der Anwendung, und klicken Sie auf das WebMatrix-Symbol unten auf der Seite:

    ![Installieren von WebMatrix](./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png)

    Folgen Sie den Anweisungen, um WebMatrix zu installieren.

2.  Nachdem WebMatrix installiert ist, wird es versuchen, die Website als WebMatrix-Projekt zu öffnen. Sie können die Live-Website direkt bearbeiten oder eine lokale Kopie herunterladen. Wählen Sie für dieses Lernprogramm das Bearbeiten einer lokalen Kopie.

3.  Wenn Sie aufgefordert werden, die Website herunterzuladen, wählen Sie **Yes, install from the Template Gallery**.

    ![Website herunterladen](./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png)

4.  Wählen Sie aus den verfügbaren Vorlagen **PHP**.

    ![Website von Vorlage](./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png)

5.  Wählen Sie die Vorlage **Empty Site**. Geben Sie einen Namen für die Website an, und klicken Sie auf **NEXT**.

    ![Namen für Website angeben](./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png)

Ihre Website wird in WebMatrix mit einigen Standarddateien geöffnet.

Entwickeln der Anwendung
------------------------

In den nächsten Schritten werden Sie die Tasklist-Anwendung entwickeln, indem Sie die zuvor heruntergeladenen Dateien hinzufügen und einige Änderungen vornehmen. Sie können jedoch auch eigene Dateien hinzufügen oder neue Dateien erstellen.

1.  Fügen Sie bei in WebMatrix geöffneter Website die Anwendungsdateien hinzu, indem Sie auf **Add Existing** klicken:

    ![WebMatrix – Vorhandene Dateien hinzufügen](./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png)

    Navigieren Sie im angezeigten Dialogfenster zu den zuvor heruntergeladenen Dateien, wählen Sie alle aus, und klicken Sie dann auf Open. Wählen Sie bei entsprechender Aufforderung, dass die Datei `index.php` ersetzt wird.

2.  Anschließend müssen Sie die Verbindungsinformationen für die lokale SQL Server-Datenbank zur Datei `taskmodel.php` hinzufügen. Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`. (**Hinweis**: Gehen Sie zu [Veröffentlichen der Anwendung](#Publish), falls Sie die Anwendung nicht lokal testen möchten und diese stattdessen direkt in Azure-Websites veröffentlichen möchten.)

         // DB connection info
         $host = "localhost\sqlexpress";
         $user = "your user name";
         $pwd = "your password";
         $db = "tasklist";

    Speichern Sie die Datei `taskmodel.php`.

3.  Damit die Anwendung ausgeführt werden kann, muss die Tabelle `items` erstellt werden. Klicken Sie mit der rechten Maustaste auf die Datei `createtable.php`, und wählen Sie **Launch in browser**. Dadurch wird `createtable.php` im Browser gestartet und Code ausgeführt, wodurch die Tabelle `items` in der Datenbank `tasklist` erstellt wird.

    ![WebMatrix – createtable.php im Browser starten](./media/web-sites-php-sql-database-use-webmatrix/edit_run.png)

4.  Sie können die Anwendung jetzt lokal testen. Klicken Sie mit der rechten Maustaste auf die Datei `index.php`, und wählen Sie **Launch in browser**. Testen Sie die Anwendung, indem Sie Elemente hinzufügen, diese als erledigt markieren und löschen.

Veröffentlichen der Anwendung
-----------------------------

Bevor die Anwendung in Azure-Websites veröffentlicht wird, müssen die Verbindungsinformationen für die Datenbank in `taskmodel.php` mit den Verbindungsinformationen aktualisiert werden, die Sie zuvor erhalten haben (im Abschnitt [Erstellen einer Azure-Website und einer SQL-Datenbank](#CreateWebsite)).

1.  Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`.

         // DB connection info
         $host = "value of $serverName";
         $user = "value of UID";
         $pwd = "the SQL password you created when creating the web site";
         $db = "value of Database";

    Speichern Sie die Datei `taskmodel.php`.

2.  Klicken Sie in WebMatrix auf **Veröffentlichen** und anschließend auf **Weiter** im Dialogfeld **Vorschau veröffentlichen**.

    ![WebMatrix – Veröffentlichen](./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png)

3.  Navigieren Sie zu http://[Name der Website].azurewebsites.net/createtable.php, um die Tabelle `items` zu erstellen.

4.  Navigieren Sie zuletzt zu http://[Name der Website].azurewebsites.net/index.php, um die Anwendung zu starten.

Ändern und erneutes Veröffentlichen der Anwendung
-------------------------------------------------

Sie können die Anwendung einfach ändern, indem Sie die lokale Kopie der zuvor heruntergeladenen Website bearbeiten und dann erneut veröffentlichen. Alternativ können Sie die Änderung auch direkt im Remotemodus vornehmen. In diesem Fall nehmen Sie eine einfache Änderung an der Kopfzeile der Datei `index.php` vor, und speichern diese direkt in der Live-Website.

1.  Klicken Sie in WebMatrix auf die Remote-Registerkarte der Website, und wählen Sie **Open Remote View**. Dadurch wird die Remote-Website für die direkte Bearbeitung geöffnet. ![WebMatrix – Remote-Ansicht öffnen](./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png)

2.  Öffnen Sie durch Doppelklick die Datei `index.php`. ![WebMatrix – Indexdatei öffnen](./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png)

3.  Ändern Sie **My ToDo List** in **My Task List** in den Tags **Titel** und **h1**, und speichern Sie die Datei.

4.  Klicken Sie nach dem Speichern auf die Run-Schaltfläche, um die Änderungen an der Live-Website anzuzeigen. ![WebMatrix – Website in Remote starten](./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png)

Nächste Schritte
----------------

Sie haben gesehen, wie Sie eine Website erstellen und von WebMatrix zu Azure bereitstellen. Weitere Informationen zu WebMatrix erhalten Sie hier:

-   [WebMatrix für Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

-   [WebMatrix-Website](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)


