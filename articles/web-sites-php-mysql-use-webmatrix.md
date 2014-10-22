<properties linkid="develop-php-website-with-mysql-and-webmatrix" urlDisplayName="Web w/ WebMatrix" pageTitle="PHP website with MySQL and WebMatrix - Azure tutorial" metaKeywords="" description="A tutorial that demonstrates how to use the free WebMatrix IDE to create and deploy a PHP website that stores data in MySQL." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure Website using WebMatrix" authors="" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author></tags>

# Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix

In diesem Lernprogramm erfahren Sie, wie Sie WebMatrix zum Entwickeln und Bereitstellen einer PHP-MySQL-Anwendung auf einer Azure-Website verwenden. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix unterstützt PHP und enthält IntelliSense für die PHP-Entwicklung.

Dieses Lernprogramm geht davon aus, dass Sie [MySQL][MySQL] auf Ihrem Computer installiert haben, sodass Sie eine Anwendung lokal testen können. Sie können das Lernprogramm jedoch auch durchführen, ohne MySQL installiert zu haben. Sie können stattdessen die Anwendung auch direkt in Azure-Websites bereitstellen.

Nachdem Sie diese Anleitung durchgearbeitet haben, werden Sie eine in Azure ausgeführte PHP-MySQL-Website besitzen.

Sie erhalten Informationen zu folgenden Themen:

-   Erstellen einer Azure-Website und einer MySQL-Datenbank über das Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
-   Entwickeln einer PHP-Anwendung mithilfe von WebMatrix.
-   Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von WebMatrix in Azure.

Mithilfe dieses Lernprogramms erstellen Sie eine einfache Tasklist-Webanwendung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der laufenden Anwendung:

![Azure-PHP-Website][Azure-PHP-Website]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Voraussetzungen

1.  [Herunterladen][Herunterladen] der Tasklist-Anwendungsdateien. Die Tasklist-Anwendung ist eine einfache PHP-Anwendung, mit der Sie Elemente einer Aufgabenliste hinzufügen, als erledigt markieren und löschen können. Elemente der Aufgabenliste werden in einer MySQL-Datenbank gespeichert. Die Anwendung besteht aus diesen Dateien:

    -   **additem.php**: Fügt ein Element zur Liste hinzu.
    -   **createtable.php**: Erstellt die MySQL-Tabelle für die Anwendung. Diese Datei wird nur einmal abgerufen.
    -   **deleteitem.php**: Löscht ein Element.
    -   **getitems.php**: Ruft alle Elemente in der Datenbank ab.
    -   **index.php**: Zeigt Aufgaben an und bietet ein Formular für das Hinzufügen eines Elements zur Liste.
    -   **markitemcomplete.php**: Ändert den Status eines Elements in erledigt.
    -   **taskmodel.php**: Enthält Funktionen, welche Elemente aus der Datenbank hinzufügen, abrufen, aktualisieren und löschen.

2.  Erstellen Sie eine MySQL-Datenbank namens `tasklist`. Sie können dies entweder im Datenbankarbeitsbereich in WebMatrix (nach der Installation weiter unten in diesem Lernprogramm) oder über die MySQL-Eingabeaufforderung mit diesem Befehl ausführen:

        mysql> create database tasklist;

    Dieser Schritt ist nur erforderlich, wenn Sie die Anwendung lokal testen möchten.

## <span id="CreateWebsite"></span></a>Erstellen einer Azure-Website und einer MySQL-Datenbank

1.  Melden Sie sich am [Verwaltungsportal][Verwaltungsportal] an.
2.  Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

    ![Neue Azure-Website erstellen][Neue Azure-Website erstellen]

3.  Klicken Sie auf **WEBSITE** und dann auf **CUSTOM CREATE**.

    ![Neue Website benutzerdefiniert erstellen][Neue Website benutzerdefiniert erstellen]

    > [WACOM.NOTE]
    > Sie könne eine MySQL-Datenbank für eine Website nicht erst nach dem Generieren der Website erstellen. Sie müssen eine Website und eine MySQL-Datenbank wie unten beschrieben erstellen.

4.  Geben Sie einen Wert für **URL** ein, wählen Sie **Create a New MySQL Database** (Neue MySQL-Datenbank erstellen) aus der Dropdown-Liste **DATENBANK** aus, und wählen Sie das Datencenter für Ihre Website aus der Dropdown-Liste **REGION** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

    ![Websitedetails eingeben][Websitedetails eingeben]

5.  Geben Sie einen Wert für den **NAMEN** Ihrer Datenbank ein, wählen Sie das Datencenter für Ihre Datenbank aus der Dropdown-Liste **REGION** aus, und aktivieren Sie das Kontrollkästchen zur Zustimmung zu den rechtlichen Bedingungen. Klicken Sie auf das Häkchen unten im Dialogfeld.

    ![Neue MySQL-Datenbank erstellen][Neue MySQL-Datenbank erstellen]

    Wenn die Website erstellt wurde, wird der Text **Creation of Web Site "[SITENAME]" succeeded** angezeigt.

    Als nächstes benötigen Sie die MySQL-Verbindungsinformationen.

6.  Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt ist, um die Schnellstart-Seite der Website zu öffnen.

    ![Website-Dashboard öffnen][Website-Dashboard öffnen]

7.  Klicken Sie auf die Registerkarte **KONFIGURIEREN**:

    ![Registerkarte "Konfigurieren"][Registerkarte "Konfigurieren"]

8.  Scrollen Sie nach unten zum Abschnitt **Verbindungszeichenfolgen**. Die Werte für `Database`, `Data Source`, `User Id` und `Password` sind jeweils der Datenbankname, Servername, Benutzername und das Benutzerkennwort. Notieren Sie sich die Datenbankverbindungsinformationen, da Sie diese später benötigen.

    ![Verbindungszeichenfolge][Verbindungszeichenfolge]

## Installieren von WebMatrix und Entwickeln Ihrer Anwendung

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

    In den nächsten Schritten werden Sie die Tasklist-Anwendung entwickeln, indem Sie die zuvor heruntergeladenen Dateien hinzufügen und einige Änderungen vornehmen. Sie können jedoch auch eigene Dateien hinzufügen oder neue Dateien erstellen.

6.  Fügen Sie die Anwendungsdateien hinzu, indem Sie auf **Add Existing** klicken:

    ![WebMatrix – Vorhandene Dateien hinzufügen][WebMatrix – Vorhandene Dateien hinzufügen]

    Navigieren Sie im angezeigten Dialogfenster zu den zuvor heruntergeladenen Dateien, wählen Sie alle aus, und klicken Sie dann auf Open. Wählen Sie bei entsprechender Aufforderung, dass die Datei `index.php` ersetzt wird.

7.  Anschließend müssen Sie die Verbindungsinformationen für die lokale MySQL-Datenbank zur Datei `taskmodel.php` hinzufügen. Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`. (**Hinweis**: Gehen Sie zu [Veröffentlichen der Anwendung][Veröffentlichen der Anwendung], falls Sie die Anwendung nicht lokal testen möchten und diese stattdessen direkt in Azure-Websites veröffentlichen möchten.)

        // DB connection info
        $host = "localhost";
        $user = "your user name";
        $pwd = "your password";
        $db = "tasklist";

    Speichern Sie die Datei `taskmodel.php`.

8.  Damit die Anwendung ausgeführt werden kann, muss die Tabelle `items` erstellt werden. Klicken Sie mit der rechten Maustaste auf die Datei `createtable.php`, und wählen Sie **Launch in browser**. Dadurch wird `createtable.php` im Browser gestartet und Code ausgeführt, wodurch die Tabelle `items` in der Datenbank `tasklist` erstellt wird.

    ![WebMatrix – createtable.php im Browser starten][WebMatrix – createtable.php im Browser starten]

9.  Sie können die Anwendung jetzt lokal testen. Klicken Sie mit der rechten Maustaste auf die Datei `index.php`, und wählen Sie **Launch in browser**. Testen Sie die Anwendung, indem Sie Elemente hinzufügen, diese als erledigt markieren und löschen.

## <span id="Publish"></span></a>Veröffentlichen der Anwendung

Bevor die Anwendung in Azure-Websites veröffentlicht wird, müssen die Verbindungsinformationen für die Datenbank in `taskmodel.php` mit den Verbindungsinformationen aktualisiert werden, die Sie zuvor erhalten haben (im Abschnitt [Erstellen einer Azure-Website und einer SQL-Datenbank][Erstellen einer Azure-Website und einer SQL-Datenbank]).

1.  Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`.

        // DB connection info
        $host = "value of Data Source";
        $user = "value of User Id";
        $pwd = "value of Password";
        $db = "value of Database";

    Speichern Sie die Datei `taskmodel.php`.

2.  Klicken Sie in WebMatrix auf **Veröffentlichen** und anschließend auf **Weiter** im Dialogfeld **Vorschau veröffentlichen**.

    ![WebMatrix – Veröffentlichen][WebMatrix – Veröffentlichen]

3.  Navigieren Sie zu http://[Name der Website].azurewebsites.net/createtable.php, um die Tabelle `items` zu erstellen.

4.  Navigieren Sie zuletzt zu http://[Name der Website].azurewebsites.net/index.php, um die Anwendung zu verwenden.

## Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung einfach ändern, indem Sie die lokale Kopie der zuvor heruntergeladenen Website bearbeiten und dann erneut veröffentlichen. Alternativ können Sie die Änderung auch direkt im Remotemodus vornehmen. In diesem Fall nehmen Sie eine einfache Änderung an der Kopfzeile der Datei `index.php` vor und speichern diese direkt in der Live-Website.

1.  Klicken Sie in WebMatrix auf die Remote-Registerkarte der Website, und wählen Sie **Open Remote View**. Dadurch wird die Remote-Website für die direkte Bearbeitung geöffnet.
     ![WebMatrix – Remote-Ansicht öffnen][WebMatrix – Remote-Ansicht öffnen]

2.  Öffnen Sie die Datei `index.php` durch Doppelklick.
    ![WebMatrix - Indexdatei öffnen][WebMatrix - Indexdatei öffnen]

3.  Ändern Sie **My ToDo List** in **My Task List** in den Tags **Titel** und **h1**, und speichern Sie die Datei.

4.  Klicken Sie nach dem Speichern auf die Run-Schaltfläche, um die Änderungen an der Live-Website anzuzeigen.
    ![WebMatrix - Website in Remote starten][WebMatrix - Website in Remote starten]

# Nächste Schritte

Sie haben gesehen, wie Sie eine Website erstellen und von WebMatrix auf Azure bereitstellen. Weitere Informationen zu WebMatrix erhalten Sie hier:

-   [WebMatrix für Azure][WebMatrix für Azure]

-   [WebMatrix-Website][WebMatrix-Website]

  [MySQL]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
  [Azure-PHP-Website]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
  [create-account-and-websites-note]: ../includes/create-account-and-websites-note.md
  [Herunterladen]: http://go.microsoft.com/fwlink/?LinkId=252506
  [Verwaltungsportal]: https://manage.windowsazure.com
  [Neue Azure-Website erstellen]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
  [Neue Website benutzerdefiniert erstellen]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
  [Websitedetails eingeben]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
  [Neue MySQL-Datenbank erstellen]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
  [Website-Dashboard öffnen]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
  [Registerkarte "Konfigurieren"]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
  [Verbindungszeichenfolge]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
  [Installieren von WebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
  [Website herunterladen]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
  [Website von Vorlage]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
  [Namen für Website angeben]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
  [WebMatrix – Vorhandene Dateien hinzufügen]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
  [Veröffentlichen der Anwendung]: #Publish
  [WebMatrix – createtable.php im Browser starten]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
  [Erstellen einer Azure-Website und einer SQL-Datenbank]: #CreateWebsite
  [WebMatrix – Veröffentlichen]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
  [WebMatrix – Remote-Ansicht öffnen]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
  [WebMatrix - Indexdatei öffnen]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
  [WebMatrix - Website in Remote starten]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png
  [WebMatrix für Azure]: http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409
  [WebMatrix-Website]: http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398
