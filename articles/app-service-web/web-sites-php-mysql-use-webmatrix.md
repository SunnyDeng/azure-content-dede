<properties 
	pageTitle="Erstellen und Bereitstellen einer PHP-MySQL-Web-App in Azure App Service mithilfe von WebMatrix" 
	description="In diesem Lernprogramm wird gezeigt, wie Sie mithilfe der kostenlosen WebMatrix-IDE eine PHP-Web-App in Azure App Service erstellen und bereitstellen, die Daten in MySQL speichert."
	tags="azure-portal" 
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
	ms.date="07/07/2015" 
	ms.author="tomfitz"/>





# Erstellen und Bereitstellen einer PHP-MySQL-Web-App in Azure App Service mithilfe von WebMatrix

In diesem Lernprogramm erfahren Sie, wie Sie eine PHP-MySQL-Anwendung mithilfe von WebMatrix in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps entwickeln und bereitstellen. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix unterstützt PHP und enthält IntelliSense für die PHP-Entwicklung.

In diesem Lernprogramm wird davon ausgegangen, dass Sie [MySQL][install-mysql] auf Ihrem Computer installiert haben, sodass Sie eine Anwendung lokal testen können. Sie können das Lernprogramm jedoch auch durchführen, ohne MySQL installiert zu haben. Sie können stattdessen die Anwendung auch direkt in Azure App Service-Web-Apps bereitstellen.

Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Web-Apps ausgeführte PHP-MySQL-Website.
 
Sie erhalten Informationen zu folgenden Themen:

* Erstellen einer Website in App Service-Web-Apps und einer MySQL-Datenbank über das [Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715). Da PHP standardmäßig auf Web-Apps aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
* Entwickeln einer PHP-Anwendung mithilfe von WebMatrix.
* Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von WebMatrix in Web-Apps.
 
Mithilfe dieses Lernprogramms erstellen Sie eine einfache Tasklist-Webanwendung in PHP. Die Anwendung wird in App Service-Web-Apps gehostet. Unten finden Sie einen Screenshot der laufenden Anwendung:

![Azure-PHP-Website][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Voraussetzungen

1. [Herunterladen][tasklist-mysql-download] der Tasklist-Anwendungsdateien. Die Tasklist-Anwendung ist eine einfache PHP-Anwendung, mit der Sie Elemente einer Aufgabenliste hinzufügen, als erledigt markieren und löschen können. Die Elemente der Aufgabenliste werden in einer MySQL-Datenbank gespeichert. Die Anwendung besteht aus diesen Dateien:

	* **additem.php**: Fügt ein Element zur Liste hinzu.
	* **createtable.php**: Erstellt die MySQL-Tabelle für die Anwendung. Diese Datei wird nur einmal abgerufen.
	* **deleteitem.php**: Löscht ein Element.
	* **getitems.php**: Ruft alle Elemente in der Datenbank ab.
	* **index.php**: Zeigt Aufgaben an und bietet ein Formular für das Hinzufügen eines Elements zur Liste.
	* **markitemcomplete.php**: Ändert den Status eines Elements in Abgeschlossen.
	* **taskmodel.php**: Enthält Funktionen, welche Elemente aus der Datenbank hinzufügen, abrufen, aktualisieren und löschen.

1. Erstellen Sie eine lokale MySQL-Datenbank namens `tasklist`. Verwenden Sie dazu entweder den Datenbank-Arbeitsbereich in WebMatrix (nachdem er nachfolgend im Lernprogramm installiert wurde) oder die MySQL-Eingabeaufforderung mit folgendem Befehl:

		mysql> create database tasklist;

	Dieser Schritt ist nur erforderlich, wenn Sie die Anwendung lokal testen möchten.

## Erstellen einer Web-App und einer MySQL-Datenbank

Befolgen Sie diese Schritte, um eine Web-App und eine MySQL-Datenbank zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie unten links im Portal auf das Symbol **Neu**.

	![Neue Azure-Website erstellen](./media/web-sites-php-mysql-use-webmatrix/new_website2.png)

3. Klicken Sie auf **Web + Mobile** und dann auf **Web-App + MySQL**.

	![Neue Website benutzerdefiniert erstellen](./media/web-sites-php-mysql-use-webmatrix/create_web_mysql.png)

4. Geben Sie einen gültigen Namen für die Ressourcengruppe ein.

    ![Ressourcengruppenname festlegen](./media/web-sites-php-mysql-use-webmatrix/set_group.png)

5. Geben Sie Werte für die neue Web-App ein.

    ![Web-App erstellen](./media/web-sites-php-mysql-use-webmatrix/create_wa.png)

6. Geben Sie Werte für die neue Datenbank ein, einschließlich Ihrer Zustimmung der rechtlichen Bedingungen.

	![Neue MySQL-Datenbank erstellen](./media/web-sites-php-mysql-use-webmatrix/create_db.png)

	Wenn die Web-App erstellt wurde, wird die neue Ressourcengruppe angezeigt.

## Abrufen von MySQL-Remoteverbindungsinformationen

Um eine Verbindung mit der in Web-Apps ausgeführten MySQL-Datenbank herzustellen, benötigen Sie die Verbindungsinformationen. Befolgen Sie die folgenden Schritte, um MySQL-Verbindungsinformationen abzurufen:

1. Klicken Sie in der Ressourcengruppe auf die Datenbank:

	![Datenbank auswählen](./media/web-sites-php-mysql-use-webmatrix/select_database.png)

2. Wählen Sie für der Datenbankzusammenfassung die Option **Eigenschaften**.

    ![Eigenschaften auswählen](./media/web-sites-php-mysql-use-webmatrix/select_properties.png)

2. Notieren Sie sich die Werte für `Database`, `Host`, `User Id` und `Password`.

    ![Eigenschaften notieren](./media/web-sites-php-mysql-use-webmatrix/note-properties.png)

## Erstellen Ihrer Anwendung in WebMatrix

In den nächsten Schritten werden Sie die Tasklist-Anwendung entwickeln, indem Sie die zuvor heruntergeladenen Dateien hinzufügen und einige Änderungen vornehmen. Sie können jedoch auch eigene Dateien hinzufügen oder neue Dateien erstellen.

1. Starten Sie [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Wenn Sie es noch nicht installiert haben, holen Sie dies jetzt nach.
2. Wenn Sie WebMatrix 3 zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden. Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**. Melden Sie sich mit Ihrem Microsoft-Konto an.

	![Konto hinzufügen](./media/web-sites-php-mysql-use-webmatrix/webmatrix-add-account.png)

3. Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

	![Anmelden bei Azure](./media/web-sites-php-mysql-use-webmatrix/webmatrix-sign-in.png)

1. Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog**, um eine neue Site aus dem Vorlagenkatalog zu erstellen:

	![Neue Site aus Vorlagenkatalog](./media/web-sites-php-mysql-use-webmatrix/webmatrix-site-from-template.png)

4. Wählen Sie aus den verfügbaren Vorlagen **PHP**.

	![Website von Vorlage][site-from-template]

5. Wählen Sie die Vorlage **Empty Site**. Geben Sie einen Namen für die Website an, und klicken Sie auf **NEXT**.

	![Namen für Website angeben][site-from-template-2]

	Ihre Website wird in WebMatrix mit einigen Standarddateien geöffnet.

	In den nächsten Schritten werden Sie die Tasklist-Anwendung entwickeln, indem Sie die zuvor heruntergeladenen Dateien hinzufügen und einige Änderungen vornehmen. Sie können jedoch auch eigene Dateien hinzufügen oder neue Dateien erstellen.

6. Fügen Sie die Anwendungsdateien hinzu, indem Sie auf **Vorhandene hinzufügen** klicken:

	![WebMatrix – Vorhandene Dateien hinzufügen][edit_addexisting]

	Navigieren Sie im angezeigten Dialogfenster zu den zuvor heruntergeladenen Dateien, wählen Sie alle aus, und klicken Sie dann auf Open. Geben Sie bei entsprechender Aufforderung an, dass die Datei `index.php` ersetzt werden soll.

7. Anschließend müssen Sie die Verbindungsinformationen für die lokale MySQL-Datenbank zur Datei `taskmodel.php` hinzufügen. Öffnen Sie die `taskmodel.php`-Datei, indem Sie darauf doppelklicken, und aktualisieren Sie die Datenbank-Verbindungsinformationen in der Funktion `connect`. (** Hinweis **: Wechseln Sie zu [Veröffentlichung der Anwendung](#Publish), wenn Sie Ihre Anwendung nicht lokal testen möchten und stattdessen direkt in Azure App Service-Web-Apps veröffentlichen möchten.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Speichern Sie die Datei `taskmodel.php`.

8. Damit die Anwendung ausgeführt werden kann, muss die Tabelle `items` erstellt werden. Klicken Sie mit der rechten Maustaste auf die Datei `createtable.php`, und wählen Sie **In Browser starten**. Dadurch wird `createtable.php` im Browser gestartet und Code ausgeführt, wodurch die Tabelle `items` in der Datenbank `tasklist` erstellt wird.

	![WebMatrix – createtable.php im Browser starten][edit_run]

9. Sie können die Anwendung jetzt lokal testen. Klicken Sie mit der rechten Maustaste auf die Datei `index.php`, und wählen Sie **In Browser starten**. Testen Sie die Anwendung, indem Sie Elemente hinzufügen, diese als erledigt markieren und löschen.


## Veröffentlichen der Anwendung

Bevor die Anwendung in App Service-Web-Apps veröffentlicht wird, müssen die Verbindungsinformationen für die Datenbank in `taskmodel.php` mit den Verbindungsinformationen aktualisiert werden, die Sie zuvor erhalten haben (im Abschnitt [Erstellen einer Web-App und einer SQL-Datenbank](#CreateWebsite)).

1. Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Speichern Sie die Datei `taskmodel.php`.

2. Klicken Sie in WebMatrix auf **Veröffentlichen**.

	![WebMatrix – Veröffentlichen][edit_publish]

3. Klicken Sie auf **Bestehende Website in Microsoft Azure auswählen**.

3. Wählen Sie die App Service-Web-App aus, die Sie zuvor erstellt haben.

	![](./media/web-sites-php-mysql-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Klicken Sie so lange auf **Weiter**, bis WebMatrix die Website in Azure App Service-Web-Apps veröffentlicht.

3. Navigieren Sie zu http://[your Name der Website].azurewebsites.net/createtable.php, um die Tabelle `items` zu erstellen.

4. Navigieren Sie zuletzt zu http://[your Name der Website].azurewebsites.net/index.php, um die Anwendung zu verwenden.
	
##Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung einfach ändern, indem Sie die lokale Kopie der zuvor heruntergeladenen Website bearbeiten und dann erneut veröffentlichen. Alternativ können Sie die Änderung auch direkt im Remotemodus vornehmen. In diesem Fall nehmen Sie eine einfache Änderung an der Kopfzeile der Datei `index.php` vor und speichern diese direkt in der Live-Website.

1. Klicken Sie in WebMatrix auf die Remote-Registerkarte der Website, und wählen Sie **Open Remote View**. Dadurch wird die Remotewebsite für die direkte Bearbeitung geöffnet.![WebMatrix – Remote-Ansicht öffnen][OpenRemoteView]
 
2. Öffnen Sie die Datei `index.php` durch Doppelklick. ![WebMatrix – Indexdatei öffnen][Remote_editIndex]

3. Ändern Sie **My ToDo List** in **My Task List** in den Tags **Titel** und **h1**, und speichern Sie die Datei.


4. Klicken Sie nach dem Speichern auf die Schaltfläche "Ausführen", um die Änderungen an der Live-Website anzuzeigen. ![WebMatrix – Website in Remote starten][Remote_run]


## Nächste Schritte

* [WebMatrix-Website](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)




[install-mysql]: http://dev.mysql.com/doc/refman/5.6/en/installing.html
[running-app]: ./media/web-sites-php-mysql-use-webmatrix/tasklist_app_windows.png
[tasklist-mysql-download]: http://go.microsoft.com/fwlink/?LinkId=252506
[NewWebSite1]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite2.png
[NewWebSite3]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite3.png
[NewWebSite4]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite4.png
[NewWebSite5]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite5.png
[NewWebSite6]: ./media/web-sites-php-mysql-use-webmatrix/NewWebSite6.png
[ConnectionString]: ./media/web-sites-php-mysql-use-webmatrix/ConnectionString.png
[InstallWebMatrix]: ./media/web-sites-php-mysql-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-mysql-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-mysql-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-mysql-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-mysql-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-mysql-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-mysql-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-mysql-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-mysql-use-webmatrix/Remote_run.png













[preview-portal]: https://manage.windowsazure.com













 

<!---HONumber=July15_HO3-->