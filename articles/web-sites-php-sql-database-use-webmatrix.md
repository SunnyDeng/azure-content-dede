<properties linkid="develop-php-website-with-sql-database-and-webmatrix" urlDisplayName="Web w/ SQL + WebMatrix" pageTitle="PHP website with SQL Database and WebMatrix - Azure" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in SQL Database." metaCanonical="" services="" documentationCenter="" title="Create and Deploy a PHP Website and SQL Database using WebMatrix" authors="cephalin" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Erstellen und Bereitstellen einer PHP-Website und SQL-Datenbank mit WebMatrix

In diesem Lernprogramm wird gezeigt, wie Sie mit WebMatrix eine PHP-Anwendung, die eine Azure-SQL-Datenbank verwendet, entwickeln und auf einer Azure-Website bereitstellen. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix unterstützt PHP und enthält IntelliSense für die PHP-Entwicklung.

Dieses Lernprogramm geht davon aus, dass Sie [SQL Server Express][SQL Server Express] auf Ihrem Computer installiert haben, sodass Sie eine Anwendung lokal testen können. Sie können das Lernprogramm jedoch auch durchführen, ohne SQL Server Express installiert zu haben. Sie können stattdessen die Anwendung auch direkt auf Azure-Websites bereitstellen.

Nach der Durchführung dieses Lernprogramms verfügen Sie über eine in Azure ausgeführte PHP-SQL-Datenbank.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und einer SQL-Datenbank über das Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
-   Entwickeln einer PHP-Anwendung mithilfe von WebMatrix.
-   Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von WebMatrix in Azure.

Mithilfe dieses Lernprogramms erstellen Sie eine einfache Tasklist-Webanwendung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der laufenden Anwendung:

![Azure-PHP-Website][Azure-PHP-Website]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Voraussetzungen

1.  [Herunterladen][Herunterladen] der Tasklist-Anwendungsdateien. Die Tasklist-Anwendung ist eine einfache PHP-Anwendung, mit der Sie Elemente einer Aufgabenliste hinzufügen, als erledigt markieren und löschen können. Elemente der Aufgabenliste werden in einer SQL-Datenbank gespeichert (SQL Server Express für lokales Testen). Die Anwendung besteht aus diesen Dateien:

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

## Erstellen einer Website und einer SQL-Datenbank

1.  Melden Sie sich am [Verwaltungsportal][Verwaltungsportal] an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Neue Azure-Website erstellen][Neue Azure-Website erstellen]

3.  Klicken Sie auf **WEBSITE** und dann auf **BENUTZERDEFINIERT ERSTELLEN**.

    ![Neue Website benutzerdefiniert erstellen][Neue Website benutzerdefiniert erstellen]

    Geben Sie einen Wert für **URL** ein, und wählen Sie **Neue SQL-Datenbank erstellen** in der Dropdownliste **DATENBANK** und das Rechenzentrum für Ihre Website in der Dropdownliste **REGION** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Websitedetails eingeben][Websitedetails eingeben]

4.  Geben Sie einen Wert für den **NAMEN** der Datenbank ein, und wählen Sie **Neuer SQL-Datenbankserver** aus. Geben Sie einen Anmeldenamen und ein Kennwort für den Server ein (und bestätigen Sie das Kennwort). Wählen Sie die Region, in der der neue SQL-Datenbankserver erstellt wird.

    ![SQL-Datenbankeinstellungen eingeben][SQL-Datenbankeinstellungen eingeben]

    Wenn die Website erstellt wurde, wird der Text **Die Erstellung der Website "[WEBSITENAME]" wurde erfolgreich abgeschlossen** angezeigt. Als nächstes erhalten Sie die Verbindungsinformationen für die Datenbank.

5.  Klicken Sie auf **VERKNÜPFTE RESSOURCEN** und anschließend auf den Namen der Datenbank.

    ![Verknüpfte Ressourcen][Verknüpfte Ressourcen]

6.  Klicken Sie auf **Verbindungszeichenfolgen anzeigen**.

    ![Verbindungszeichenfolge][Verbindungszeichenfolge]

Notieren Sie sich im Abschnitt **PHP** des angezeigten Dialogfelds die Werte für `UID`, `PWD`, `Database` und `$serverName`. Sie werden diese Informationen später verwenden.

## Installieren von WebMatrix

Sie können WebMatrix aus dem [Verwaltungsportal][Verwaltungsportal] installieren.

1.  Navigieren Sie nach der Anmeldung zur Schnellstartseite der Anwendung, und klicken Sie auf das WebMatrix-Symbol unten auf der Seite:

    ![Installieren von WebMatrix][Installieren von WebMatrix]

    Folgen Sie den Anweisungen, um WebMatrix zu installieren.

2.  Nachdem WebMatrix installiert ist, wird es versuchen, die Website als WebMatrix-Projekt zu öffnen. Sie können die Live-Website direkt bearbeiten oder eine lokale Kopie herunterladen. Wählen Sie für dieses Lernprogramm das Bearbeiten einer lokalen Kopie.

3.  Wenn Sie aufgefordert werden, die Website herunterzuladen, wählen Sie **Yes, install from the Template Gallery**.

    ![Website herunterladen][Website herunterladen]

4.  Wählen Sie aus den verfügbaren Vorlagen **PHP**.

    ![Website von Vorlage][Website von Vorlage]

5.  Wählen Sie die Vorlage **Empty Site**. Geben Sie einen Namen für die Website an, und klicken Sie auf **NEXT**.

    ![Namen für Website angeben][Namen für Website angeben]

Ihre Website wird in WebMatrix mit einigen Standarddateien geöffnet.

## Entwickeln der Anwendung

In den nächsten Schritten werden Sie die Tasklist-Anwendung entwickeln, indem Sie die zuvor heruntergeladenen Dateien hinzufügen und einige Änderungen vornehmen. Sie können jedoch auch eigene Dateien hinzufügen oder neue Dateien erstellen.

1.  Fügen Sie bei in WebMatrix geöffneter Website die Anwendungsdateien hinzu, indem Sie auf **Add Existing** klicken:

    ![WebMatrix – Vorhandene Dateien hinzufügen][WebMatrix – Vorhandene Dateien hinzufügen]

    Navigieren Sie im angezeigten Dialogfenster zu den zuvor heruntergeladenen Dateien, wählen Sie alle aus, und klicken Sie dann auf Open. Wählen Sie bei entsprechender Aufforderung, dass die Datei `index.php` ersetzt wird.

2.  Anschließend müssen Sie die Verbindungsinformationen für die lokale SQL Server-Datenbank in die Datei `taskmodel.php` einfügen. Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`. (**Hinweis**: Gehen Sie zu [Veröffentlichen der Anwendung][Veröffentlichen der Anwendung], falls Sie die Anwendung nicht lokal testen möchten und diese stattdessen direkt in Azure-Websites veröffentlichen möchten.)

        // DB connection info
        $host = "localhost\sqlexpress";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    Speichern Sie die Datei `taskmodel.php`.

3.  Damit die Anwendung ausgeführt werden kann, muss die Tabelle `items` erstellt werden. Klicken Sie mit der rechten Maustaste auf die Datei `createtable.php`, und wählen Sie **Launch in browser**. Dadurch wird `createtable.php` im Browser gestartet und Code ausgeführt, wodurch die Tabelle `items` in der Datenbank `tasklist` erstellt wird.

    ![WebMatrix – createtable.php im Browser starten][WebMatrix – createtable.php im Browser starten]

4.  Sie können die Anwendung jetzt lokal testen. Klicken Sie mit der rechten Maustaste auf die Datei `index.php`, und wählen Sie **Launch in browser**. Testen Sie die Anwendung, indem Sie Elemente hinzufügen, diese als erledigt markieren und löschen.

## <span id="Publish"></span></a>Veröffentlichen der Anwendung

Bevor die Anwendung auf Azure-Websites veröffentlicht wird, müssen die Verbindungsinformationen für die Datenbank in `taskmodel.php` mit den Verbindungsinformationen aktualisiert werden, die Sie zuvor erhalten haben (im Abschnitt [Erstellen einer Website und einer SQL-Datenbank][Erstellen einer Website und einer SQL-Datenbank]).

1.  Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`.

        // DB connection info
        $host = "value of $serverName";
        $user = "value of UID";
        $pwd = "the SQL password you created when creating the website";
        $db = "value of Database";

    Speichern Sie die Datei `taskmodel.php`.

2.  Klicken Sie in WebMatrix auf **Veröffentlichen** und anschließend auf **Weiter** im Dialogfeld **Vorschau veröffentlichen**.

    ![WebMatrix – Veröffentlichen][WebMatrix – Veröffentlichen]

3.  Navigieren Sie zu http://[Name der Website].azurewebsites.net/createtable.php, um die Tabelle `items` zu erstellen.

4.  Navigieren Sie zuletzt zu http://[Name der Website].azurewebsites.net/index.php, um die Anwendung zu starten.

## Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung einfach ändern, indem Sie die lokale Kopie der zuvor heruntergeladenen Website bearbeiten und dann erneut veröffentlichen. Alternativ können Sie die Änderung auch direkt im Remotemodus vornehmen. In diesem Fall nehmen Sie eine einfache Änderung an der Kopfzeile der Datei `index.php` vor und speichern diese direkt in der Live-Website.

1.  Klicken Sie in WebMatrix auf die Remote-Registerkarte der Website, und wählen Sie **Open Remote View**. Dadurch wird die Remote-Website für die direkte Bearbeitung geöffnet.
     ![WebMatrix – Remote-Ansicht öffnen][WebMatrix – Remote-Ansicht öffnen]

2.  Öffnen Sie die Datei `index.php` durch Doppelklick.
    ![WebMatrix - Indexdatei öffnen][WebMatrix - Indexdatei öffnen]

3.  Ändern Sie **My ToDo List** in **My Task List** in den Tags **Titel** und **h1**, und speichern Sie die Datei.

4.  Klicken Sie nach dem Speichern auf die Run-Schaltfläche, um die Änderungen an der Live-Website anzuzeigen.
    ![WebMatrix - Website in Remote starten][WebMatrix - Website in Remote starten]

## Nächste Schritte

Sie haben gesehen, wie Sie eine Website erstellen und von WebMatrix auf Azure bereitstellen. Weitere Informationen zu WebMatrix erhalten Sie hier:

-   [WebMatrix für Azure][WebMatrix für Azure]

-   [WebMatrix-Website][WebMatrix-Website]

  [SQL Server Express]: http://www.microsoft.com/de-de/download/details.aspx?id=29062
  [Azure-PHP-Website]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
  [Herunterladen]: http://go.microsoft.com/fwlink/?LinkId=252504
  [Verwaltungsportal]: https://manage.windowsazure.com
  [Neue Azure-Website erstellen]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
  [Neue Website benutzerdefiniert erstellen]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
  [Websitedetails eingeben]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
  [SQL-Datenbankeinstellungen eingeben]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png
  [Verknüpfte Ressourcen]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
  [Verbindungszeichenfolge]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png
  [Installieren von WebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
  [Website herunterladen]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
  [Website von Vorlage]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
  [Namen für Website angeben]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
  [WebMatrix – Vorhandene Dateien hinzufügen]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
  [Veröffentlichen der Anwendung]: #Publish
  [WebMatrix – createtable.php im Browser starten]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
  [Erstellen einer Website und einer SQL-Datenbank]: #CreateWebsite
  [WebMatrix – Veröffentlichen]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
  [WebMatrix – Remote-Ansicht öffnen]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
  [WebMatrix - Indexdatei öffnen]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
  [WebMatrix - Website in Remote starten]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png
  [WebMatrix für Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [WebMatrix-Website]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
