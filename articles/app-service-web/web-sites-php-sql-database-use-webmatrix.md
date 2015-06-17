<properties 
	pageTitle="Erstellen und Bereitstellen einer PHP-SQL-Web-App in Azure App Service mithilfe von WebMatrix" 
	description="In diesem Lernprogramm wird gezeigt, wie Sie mithilfe der kostenlosen WebMatrix-IDE eine PHP-Web-App in Azure App Service erstellen und bereitstellen, die Daten in MySQL speichert." 
	tags="azure-portal"
	services="app-service\web" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="tomfitz"/>





# Erstellen und Bereitstellen einer PHP-SQL-Web-App in Azure App Service mithilfe von WebMatrix

In diesem Lernprogramm wird gezeigt, wie Sie mithilfe von WebMatrix eine PHP-Anwendung, die eine Azure-SQL-Datenbank verwendet, entwickeln und in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)-Web-Apps bereitstellen. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Web-Apps benötigen. WebMatrix unterstützt PHP und enthält IntelliSense für die PHP-Entwicklung.

Dieses Lernprogramm geht davon aus, dass Sie [SQL Server Express][install-SQLExpress] auf Ihrem Computer installiert haben, sodass Sie eine Anwendung lokal testen können. Sie können das Lernprogramm jedoch auch durchführen, ohne SQL Server Express installiert zu haben. Sie können stattdessen die Anwendung auch direkt in Azure App Service-Web-Apps bereitstellen.

Nachdem Sie diese Anleitung durchgearbeitet haben, verfügen Sie über eine in Azure ausgeführte PHP-SQL-Datenbank-Web-App.
 
Sie erhalten Informationen zu folgenden Themen:

* Erstellen einer Web-App in App Service-Web-Apps und einer SQL-Datenbank über das [Azure-Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715). Da PHP standardmäßig auf Web-Apps aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
* Entwickeln einer PHP-Anwendung mithilfe von WebMatrix.
* Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von WebMatrix in Azure.
 
Mithilfe dieses Lernprogramms erstellen Sie eine einfache Tasklist-Webanwendung in PHP. Die Anwendung wird in App Service-Web-Apps gehostet. Unten finden Sie einen Screenshot der laufenden Anwendung:

![Azure-PHP-Website][running-app]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Voraussetzungen

1. [Herunterladen][tasklist-sqlazure-download] der Tasklist-Anwendungsdateien. Die Tasklist-Anwendung ist eine einfache PHP-Anwendung, mit der Sie Elemente einer Aufgabenliste hinzufügen, als erledigt markieren und löschen können. Elemente der Aufgabenliste werden in einer SQL-Datenbank gespeichert (SQL Server Express für lokales Testen). Die Anwendung besteht aus diesen Dateien:

	* **index.php**: Zeigt Aufgaben an und bietet ein Formular für das Hinzufügen eines Elements zur Liste.
	* **additem.php**: Fügt ein Element zur Liste hinzu.
	* **getitems.php**: Ruft alle Elemente in der Datenbank ab.
	* **markitemcomplete.php**: Ändert den Status eines Elements in "Abgeschlossen".
	* **deleteitem.php**: Löscht ein Element.
	* **taskmodel.php**: Enthält Funktionen, welche Elemente aus der Datenbank hinzufügen, abrufen, aktualisieren und löschen.
	* **createtable.php**: Erstellt die SQL-Datenbanktabelle für die Anwendung. Diese Datei wird nur einmal abgerufen.

2. Erstellen Sie eine SQL Server-Datenbank namens `tasklist`. Sie können dies in der `sqlcmd`-Eingabeaufforderung mit diesen Befehlen ausführen:

		>sqlcmd -S <server name>\sqlexpress -U <user name> -P <password>
		1> create database tasklist
		2> GO

	Dieser Schritt ist nur erforderlich, wenn Sie die Anwendung lokal testen möchten.

## Erstellen einer Web-App und MySQL-Datenbank

Befolgen Sie diese Schritte, um eine Azure-Web-App und eine SQL-Datenbank zu erstellen:

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.

2. Öffnen Sie Azure Marketplace entweder durch Klicken auf das Symbol **Marketplace** oder auf das Symbol **Neu** links unten im Dashboard. Wählen Sie anschließend **Web + Mobile** und dann unten **Azure Marketplace** aus.
	
3. Wählen Sie im Marketplace **Web-Apps** aus.

4. Klicken Sie auf das Symbol W**Web-App + SQL**.

5. Wählen Sie nach dem Lesen der Beschreibung der "Web-App + SQL"-App die Option **Erstellen** aus.

6. Klicken Sie auf die einzelnen Bereiche (**Ressourcengruppe**, **Web-App**, **Datenbank** und **Abonnement**), und geben Sie die Werte für die erforderlichen Felder ein, oder wählen Sie sie aus:
	
	- Geben Sie den gewünschten URL-Namen ein.	
	- Konfigurieren der Anmeldeinformationen für die Datenbank
	- Wählen Sie die Ihnen am nächsten gelegene Region aus.

	![App konfigurieren](./media/web-sites-php-sql-database-use-webmatrix/configure-db-settings.png)

7. Klicken Sie auf **Erstellen**, nachdem Sie die Definition der Web-App abgeschlossen haben.

	Nachdem die Web-App erstellt wurde, leuchtet auf der Schaltfläche **Benachrichtigungen** der Text **ERFOLG** in grüner Schrift, und das Blatt der Ressourcengruppe wird geöffnet und zeigt die Web-App und die SQL-Datenbank in der Gruppe an.

4. Klicken Sie auf das Symbol der Web-App auf dem Blatt der Ressourcengruppe, um das Blatt der Web-App zu öffnen.

	![Ressourcengruppe der Web-App](./media/web-sites-php-sql-database-use-webmatrix/resource-group-blade.png)

##Abrufen von SQL-Datenbank-Verbindungsinformationen

Um eine Verbindung zu der mit Ihrer Web-App verknüpften SQL-Datenbankinstanz herzustellen, benötigen Sie die Verbindungsinformationen, die Sie beim Erstellen der Datenbank angegeben haben. Gehen Sie folgendermaßen vor, um die Verbindungsinformationen für die SQL-Datenbank abzurufen:

1. Klicken Sie auf dem Blatt der Ressourcengruppe auf das Symbol der SQL-Datenbank.

2. Klicken Sie auf dem Blatt der SQL-Datenbank auf **Eigenschaften** und dann auf **Verbindungszeichenfolgen anzeigen**.

	![Datenbankeigenschaften anzeigen](./media/web-sites-php-sql-database-use-webmatrix/view-database-properties.png)
	
3. Notieren Sie sich im Abschnitt **PHP** des angezeigten Dialogfelds die Werte für `Server`, `SQL Database` und `User Name`. Sie verwenden diese Werte später zum Veröffentlichen Ihrer PHP-Web-App in Azure App Service.

## Erstellen Ihrer Anwendung in WebMatrix

In den nächsten Schritten werden Sie die Tasklist-Anwendung entwickeln, indem Sie die zuvor heruntergeladenen Dateien hinzufügen und einige Änderungen vornehmen. Sie können jedoch auch eigene Dateien hinzufügen oder neue Dateien erstellen.

1. Starten Sie [Microsoft WebMatrix](http://www.microsoft.com/web/webmatrix/). Wenn Sie es noch nicht installiert haben, holen Sie dies jetzt nach.
2. Wenn Sie WebMatrix 3 zum ersten Mal verwenden, werden Sie aufgefordert, sich bei Azure anzumelden. Klicken Sie sonst auf die Schaltfläche **Anmelden**, und wählen Sie **Konto hinzufügen**. Melden Sie sich mit Ihrem Microsoft-Konto an.

	![Konto hinzufügen](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-add-account.png)

3. Wenn Sie sich für ein Azure-Konto angemeldet haben, können Sie sich mit Ihrem Microsoft-Konto anmelden:

	![Anmelden bei Azure](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-sign-in.png)

1. Klicken Sie im Startbildschirm auf die Schaltfläche **Neu**, und wählen Sie **Vorlagenkatalog**, um eine neue Site aus dem Vorlagenkatalog zu erstellen:

	![Neue Site aus Vorlagenkatalog](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template.png)

4. Wählen Sie aus den verfügbaren Vorlagen **PHP**.

	![Website von Vorlage][site-from-template]

5. Wählen Sie die Vorlage **Empty Site**. Geben Sie einen Namen für die Website an, und klicken Sie auf **NEXT**.

	![Namen für Website angeben][site-from-template-2]

3. Wenn Sie bei Azure angemeldet sind, können Sie nun eine Azure App Service-Web- Apps-Instanz für Ihre lokale Site erstellen. Wählen Sie vorläufig **Überspringen** aus.

	![Site auf Azure erstellen](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-site-from-template-azure.png)

1. Nachdem WebMatrix die lokale Website erstellt hat, wird die WebMatrix IDE angezeigt. Fügen Sie die Anwendungsdateien hinzu, indem Sie auf **Vorhandene hinzufügen** klicken:

	![WebMatrix – Vorhandene Dateien hinzufügen][edit_addexisting]

	Navigieren Sie im angezeigten Dialogfenster zu den zuvor heruntergeladenen Dateien, wählen Sie alle aus, und klicken Sie dann auf Open. Geben Sie bei entsprechender Aufforderung an, dass die Datei `index.php` ersetzt werden soll.

2. Anschließend müssen Sie die Verbindungsinformationen für die lokale SQL Server-Datenbank in die Datei `taskmodel.php` einfügen. Öffnen Sie die `taskmodel.php`-Datei, indem Sie darauf doppelklicken, und aktualisieren Sie die Datenbank-Verbindungsinformationen in der Funktion `connect`. (** Hinweis **: Wechseln Sie zu [Veröffentlichung der Anwendung](#Publish), wenn Sie Ihre Anwendung nicht lokal testen möchten und stattdessen direkt in Azure App Service-Web-Apps veröffentlichen möchten.)

		// DB connection info
		$host = "localhost\sqlexpress";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Speichern Sie die Datei `taskmodel.php`.

3. Damit die Anwendung ausgeführt werden kann, muss die Tabelle `items` erstellt werden. Klicken Sie mit der rechten Maustaste auf die Datei `createtable.php`, und wählen Sie **In Browser starten**. Dadurch wird `createtable.php` im Browser gestartet und Code ausgeführt, wodurch die Tabelle `items` in der Datenbank `tasklist` erstellt wird.

	![WebMatrix – createtable.php im Browser starten][edit_run]

4. Sie können die Anwendung jetzt lokal testen. Klicken Sie mit der rechten Maustaste auf die Datei `index.php`, und wählen Sie **In Browser starten**. Testen Sie die Anwendung, indem Sie Elemente hinzufügen, diese als erledigt markieren und löschen.

<a id="Publish"></a>
## Veröffentlichen der Anwendung

Bevor die Anwendung in App Service-Web-Apps veröffentlicht wird, müssen die Verbindungsinformationen für die Datenbank in `taskmodel.php` mit den Verbindungsinformationen aktualisiert werden, die Sie zuvor erhalten haben (im Abschnitt [Erstellen einer Web-App und einer SQL-Datenbank](#CreateWebsite)).

1. Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion`connect`.

		// DB connection info
		$host = "value of $serverName";
		$user = "value of UID";
		$pwd = "the SQL password you created when creating the web app";
		$db = "value of Database";
	
	Speichern Sie die Datei `taskmodel.php`.

2. Klicken Sie in WebMatrix auf **Veröffentlichen**.

	![WebMatrix – Veröffentlichen][edit_publish]

3. Klicken Sie auf **Bestehende Website in Microsoft Azure auswählen**.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site.png)

3. Wählen Sie die App Service-Web-App aus, die Sie zuvor erstellt haben.

	![](./media/web-sites-php-sql-database-use-webmatrix/webmatrix-publish-existing-site-choose.png)

3. Klicken Sie so lange auf **Weiter**, bis WebMatrix die Website in Azure App Service-Web-Apps veröffentlicht.

3. Navigieren Sie zu http://[your Name der Website].azurewebsites.net/createtable.php, um die Tabelle `items` zu erstellen.

4. Navigieren Sie zuletzt zu http://[your Name der Website].azurewebsites.net/index.php, um die Anwendung zu starten.
	
##Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung problemlos ändern, indem Sie die lokale Kopie der zuvor heruntergeladenen Website bearbeiten und dann erneut veröffentlichen. Alternativ können Sie die Änderung auch direkt im **Remote**-Modus vornehmen. In diesem Fall nehmen Sie eine einfache Änderung an der Kopfzeile der Datei`index.php` vor und speichern diese direkt in der Live-App-Service-Web-App.

1. Klicken Sie auf die Registerkarte **Remote** der Website in WebMatrix, und wählen Sie **Remoteansicht öffnen**. Dadurch werden die Remotedateien (gehostet auf Web-Apps) für die direkte Bearbeitung geöffnet. ![WebMatrix – Remote-Ansicht öffnen][OpenRemoteView]
 
2. Öffnen Sie die Datei `index.php` durch Doppelklick. ![WebMatrix – Indexdatei öffnen][Remote_editIndex]

3. Ändern Sie **My ToDo List** in **My Task List** in den Tags **Titel** und **h1**, und speichern Sie die Datei.


4. Klicken Sie nach dem Speichern auf die Schaltfläche "Ausführen", um die Änderungen an der Live-App-Service-Web-App anzuzeigen. ![WebMatrix – Website in Remote starten][Remote_run]



## Nächste Schritte

Sie haben gesehen, wie Sie eine Web-App erstellen und von WebMatrix in Azure App Service-Web-Apps bereitstellen. Weitere Informationen zu WebMatrix finden Sie unter [WebMatrix-Website](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398).

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des Portals gegenüber dem Vorschauportal finden Sie unter [Referenz zur Navigation im Vorschauportal](http://go.microsoft.com/fwlink/?LinkId=529715).




[install-SQLExpress]: http://www.microsoft.com/download/details.aspx?id=29062
[running-app]: ./media/web-sites-php-sql-database-use-webmatrix/tasklist_app_windows.png
[tasklist-sqlazure-download]: http://go.microsoft.com/fwlink/?LinkId=252504
[NewWebSite1]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite1.jpg
[NewWebSite2]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite2.png
[NewWebSite3_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite3_SQL.png
[NewWebSite4_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite4_SQL.png

[NewWebSite6_SQL]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite6_SQL.png
[NewWebSite7]: ./media/web-sites-php-sql-database-use-webmatrix/NewWebSite7.png

[InstallWebMatrix]: ./media/web-sites-php-sql-database-use-webmatrix/InstallWebMatrix.png
[download-site]: ./media/web-sites-php-sql-database-use-webmatrix/download-site-1.png
[site-from-template]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template.png
[site-from-template-2]: ./media/web-sites-php-sql-database-use-webmatrix/site-from-template-2.png
[edit_addexisting]: ./media/web-sites-php-sql-database-use-webmatrix/edit_addexisting.png
[edit_run]: ./media/web-sites-php-sql-database-use-webmatrix/edit_run.png
[edit_publish]: ./media/web-sites-php-sql-database-use-webmatrix/edit_publish.png
[OpenRemoteView]: ./media/web-sites-php-sql-database-use-webmatrix/OpenRemoteView.png
[Remote_editIndex]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_editIndex.png
[Remote_run]: ./media/web-sites-php-sql-database-use-webmatrix/Remote_run.png

[preview-portal]: https://manage.windowsazure.com








 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->