<properties urlDisplayName="Web w/ WebMatrix" pageTitle="PHP-Website mit MySQL und WebMatrix - Azure_Lernprogramm" metaKeywords="" description="In diesem Lernprogramm wird gezeigt, wie Sie mithilfe der kostenlosen WebMatrix-IDE eine PHP-Website erstellen, die Daten in MySQL speichert." metaCanonical="" services="web-sites" documentationCenter="PHP" title="Create and deploy a PHP-MySQL Azure Website using WebMatrix" authors="tomfitz" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="11/14/2014" ms.author="tomfitz" />





#Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix

In diesem Lernprogramm erfahren Sie, wie Sie WebMatrix zum Entwickeln und Bereitstellen einer PHP-MySQL-Anwendung auf einer Azure-Website verwenden. WebMatrix ist ein kostenloses Webentwicklungstool von Microsoft, das alles enthält, was Sie für die Entwicklung von Websites benötigen. WebMatrix unterstützt PHP und enthält IntelliSense für die PHP-Entwicklung.

Dieses Lernprogramm geht davon aus, dass Sie [MySQL][install-mysql] auf Ihrem Computer installiert haben, sodass Sie eine Anwendung lokal testen können. Sie können das Lernprogramm jedoch auch durchführen, ohne MySQL installiert zu haben. Sie können stattdessen die Anwendung auch direkt in Azure-Websites bereitstellen.

Nachdem Sie diese Anleitung durchgearbeitet haben, werden Sie eine in Azure ausgeführte PHP-MySQL-Website besitzen.
 
Sie erhalten Informationen zu folgenden Themen:

* Erstellen einer Azure-Website und einer MySQL-Datenbank über das Verwaltungsportal. Da PHP standardmäßig auf Azure-Websites aktiviert ist, gelten für die Ausführung Ihres PHP-Codes keine besonderen Voraussetzungen.
* Entwickeln einer PHP-Anwendung mithilfe von WebMatrix.
* Veröffentlichen und erneutes Veröffentlichen Ihrer Anwendung mithilfe von WebMatrix in Azure.
 
Mithilfe dieses Lernprogramms erstellen Sie eine einfache Tasklist-Webanwendung in PHP. Die Anwendung wird auf einer Azure-Website gehostet. Unten finden Sie einen Screenshot der laufenden Anwendung:

![Azure PHP Web Site][running-app]

> [WACOM.NOTE]
> Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Sie können <a href="http://azure.microsoft.com/de-de/pricing/member-offers/msdn-benefits-details/">Vorteile für MSDN-Abonnenten aktivieren</a> oder <a href="http://azure.microsoft.com/de-de/pricing/free-trial/">sich für einen kostenlos Test von Azure anmelden</a>.
> 
> Einen Einstieg in Azure Websites vor Anmeldung für ein Konto finden Sie auf <a href="https://trywebsites.azurewebsites.net/?language=php">https://trywebsites.azurewebsites.net</a>, wo Sie sofort eine kurzlebige, kostenlose ASP.NET-Startwebsite in Azure Websites erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##Voraussetzungen

1. [Herunterladen][tasklist-mysql-download] der Tasklist-Anwendungsdateien. Die Tasklist-Anwendung ist eine einfache PHP-Anwendung, mit der Sie Elemente einer Aufgabenliste hinzufügen, als erledigt markieren und löschen können. Elemente der Aufgabenliste werden in einer MySQL-Datenbank gespeichert. Die Anwendung besteht aus diesen Dateien:

	* **additem.php**: Fügt ein Element zur Liste hinzu.
	* **createtable.php**: Erstellt die MySQL-Tabelle für die Anwendung. Diese Datei wird nur einmal abgerufen.
	* **deleteitem.php**: Löscht ein Element.
	* **getitems.php**: Ruft alle Elemente in der Datenbank ab.
	* **index.php**: Zeigt Aufgaben an und bietet ein Formular für das Hinzufügen eines Elements zur Liste.
	* **markitemcomplete.php**: Ändert den Status eines Elements in erledigt.
	* **taskmodel.php**: Enthält Funktionen, welche Elemente aus der Datenbank hinzufügen, abrufen, aktualisieren und löschen.

1. Erstellen Sie eine MySQL-Datenbank namens `tasklist`. Sie können dies entweder im Datenbankarbeitsbereich in WebMatrix (nach der Installation weiter unten in diesem Lernprogramm) oder über die MySQL-Eingabeaufforderung mit diesem Befehl ausführen:

		mysql> create database tasklist;

	Dieser Schritt ist nur erforderlich, wenn Sie die Anwendung lokal testen möchten.

<h2><a id="CreateWebsite"></a>Erstellen einer Azure-Website und einer MySQL-Datenbank</h2>

1. Melden Sie sich beim [Verwaltungsportal][preview-portal] an.
1. Klicken Sie unten links im Portal auf das Symbol **+ Neu**.

	![Create New Azure Web Site][NewWebSite1]

1. Klicken Sie auf**WEBSITE**, dann auf **BENUTZERDEFINIERT ERSTELLEN**.

	![Custom Create a new Web Site][NewWebSite2]

	> [WACOM.NOTE]
	Sie können eine MySQL-Datenbank für eine Website nicht erst nach dem Generieren der Website erstellen. Sie müssen eine Website und eine MySQL-Datenbank wie unten beschrieben erstellen.

1. Geben Sie einen Wert für **URL** ein, und wählen Sie **Neue MySQL-Datenbank erstellen** in der Dropdownliste **DATENBANK** und das Rechenzentrum für Ihre Website in der Dropdownliste **REGION** aus. Klicken Sie unten im Dialogfeld auf den Pfeil.

	![Fill in web site details][NewWebSite3]

5. Geben Sie einen Wert für den **NAMEN** Ihrer Datenbank ein, wählen Sie das Rechenzentrum für Ihre Datenbank aus der Dropdownliste **REGION** aus, und aktivieren Sie das Kontrollkästchen zur Zustimmung zu den rechtlichen Bedingungen. Klicken Sie auf das Häkchen unten im Dialogfeld.

	![Create new MySQL database][NewWebSite4]

	Nachdem die Website erstellt wurde, wird der Text **Die Erstellung der Website "[WEBSITENAME]" wurde erfolgreich abgeschlossen** angezeigt.

	Als nächstes benötigen Sie die MySQL-Verbindungsinformationen.


6. Klicken Sie auf den Namen der Website, der in der Liste der Websites aufgeführt ist, um die Schnellstart-Seite der Website zu öffnen.

	![Open web site dashboard][NewWebSite5]

7. Klicken Sie auf die Registerkarte **KONFIGURIEREN**:

	![Configure tab][NewWebSite6]

8. Scrollen Sie nach unten zum Abschnitt **Verbindungszeichenfolgen**. Die Werte für `Database`, `Data Source`, `User Id` und `Password` sind jeweils der Datenbank-, Server- und Benutzername sowie das Benutzerkennwort. Notieren Sie sich die Datenbankverbindungsinformationen, da Sie diese später benötigen.

	![Connection string][ConnectionString]

##Installieren von WebMatrix und Entwickeln Ihrer Anwendung

Sie können WebMatrix aus dem [Verwaltungsportal][preview-portal] installieren. 

1. Navigieren Sie nach der Anmeldung zur Schnellstartseite der Anwendung, und klicken Sie auf das WebMatrix-Symbol unten auf der Seite:

	![Install WebMatrix][InstallWebMatrix]

	Folgen Sie den Anweisungen, um WebMatrix zu installieren.

2. Nachdem WebMatrix installiert ist, wird es versuchen, die Website als WebMatrix-Projekt zu öffnen. Sie können die Live-Website direkt bearbeiten oder eine lokale Kopie herunterladen. Wählen Sie für dieses Lernprogramm das Bearbeiten einer lokalen Kopie. 

3. Wenn Sie aufgefordert werden, die Website herunterzuladen, wählen Sie **Yes, install from the Template Gallery**.

	![Download web site][download-site]

4. Wählen Sie aus den verfügbaren Vorlagen **PHP**.

	![Site from template][site-from-template]

5. Wählen Sie die Vorlagee **Empty Site**. Geben Sie einen Namen für die Website an, und klicken Sie auf **NEXT**.

	![Provide name for site][site-from-template-2]

	Ihre Website wird in WebMatrix mit einigen Standarddateien geöffnet.

	In den nächsten Schritten werden Sie die Tasklist-Anwendung entwickeln, indem Sie die zuvor heruntergeladenen Dateien hinzufügen und einige Änderungen vornehmen. Sie können jedoch auch eigene Dateien hinzufügen oder neue Dateien erstellen.

6. Fügen Sie die Anwendungsdateien hinzu, indem Sie auf **Add Existing** klicken:

	![WebMatrix - Add existing files][edit_addexisting]

	Navigieren Sie im angezeigten Dialogfenster zu den zuvor heruntergeladenen Dateien, wählen Sie alle aus, und klicken Sie dann auf Open. Wählen Sie bei entsprechender Aufforderung, dass die Datei `index.php` ersetzt wird. 

7. Anschließend müssen Sie die Verbindungsinformationen für die lokale MySQL Server-Datenbank zur Datei `taskmodel.php` hinzufügen. Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`. (**Hinweis**: Gehen Sie zu [Veröffentlichen der Anwendung](#Publish) , falls Sie die Anwendung nicht lokal testen möchten und diese stattdessen direkt in Azure-Websites veröffentlichen möchten.)

		// DB connection info
		$host = "localhost";
		$user = "your user name";
		$pwd = "your password";
		$db = "tasklist";

	Speichern Sie die Datei `taskmodel.php`.

8. Damit die Anwendung ausgeführt werden kann, muss die Tabelle `items` erstellt werden. Klicken Sie mit der rechten Maustaste auf die Datei `createtable.php`, und wählen Sie**Launch in browser**. Dadurch wird `createtable.php` im Browser gestartet und Code ausgeführt, wodurch die Tabelle `items` in der Datenbank tasklist erstellt wird.

	![WebMatrix - Launch createtable.php in browser][edit_run]

9. Sie können die Anwendung jetzt lokal testen. Klicken Sie mit der rechten Maustaste auf die Datei `index.php`, und wählen Sie**Launch in browser**. Testen Sie die Anwendung, indem Sie Elemente hinzufügen, diese als erledigt markieren und löschen.  


<h2><a id="Publish"></a>Veröffentlichen der Anwendung</h2>

Bevor die Anwendung auf Azure-Websites veröffentlicht wird, müssen die Verbindungsinformationen für die Datenbank in `taskmodel.php` mit den Verbindungsinformationen aktualisiert werden, die Sie zuvor erhalten haben (im Abschnitt [Erstellen einer Website und einer MySQL-Datenbank](#CreateWebsite) ).

1. Öffnen Sie die Datei `taskmodel.php` durch einen Doppelklick, und aktualisieren Sie die Verbindungsinformationen für die Datenbank in der Funktion `connect`.

		// DB connection info
		$host = "value of Data Source";
		$user = "value of User Id";
		$pwd = "value of Password";
		$db = "value of Database";
	
	Save the `taskmodel.php` file.

2. Klicken Sie in WebMatrix auf **Veröffentlichen** und anschließend auf **Weiter** im Dialogfeld **Vorschau veröffentlichen**.

	![WebMatrix - Publish][edit_publish]

3. Navigieren Sie zu http://[Name der Website].azurewebsites.net/createtable.php, um die Tabelle `items` zu erstellen.

4. Navigieren Sie zuletzt zu http://[Name der Website].azurewebsites.net/index.php, um die Anwendung zu verwenden.
	
##Ändern und erneutes Veröffentlichen der Anwendung

Sie können die Anwendung einfach ändern, indem Sie die lokale Kopie der zuvor heruntergeladenen Website bearbeiten und dann erneut veröffentlichen. Alternativ können Sie die Änderung auch direkt im Remotemodus vornehmen. In diesem Fall nehmen Sie eine einfache Änderung an der Kopfzeile der Datei `index.php` vor, und speichern diese direkt in der Live-Website.

1. Klicken Sie in WebMatrix auf die Remote-Registerkarte der Website, und wählen Sie **Open Remote View**. Dadurch wird die Remote-Website für die direkte Bearbeitung geöffnet.
	 ![WebMatrix - Open Remote View][OpenRemoteView]
 
2. Öffnen Sie durch Doppelklick die Datei `index.php`.
	![WebMatrix - Open index file][Remote_editIndex]

3. Ändern Sie **My ToDo List** in **My Task List** in den Tags **title** und **h1**, und speichern Sie die Datei.


4. Klicken Sie nach dem Speichern auf die Run-Schaltfläche, um die Änderungen an der Live-Website anzuzeigen.
	![WebMatrix - Launch site in Remote][Remote_run]


# Nächste Schritte

Sie haben gesehen, wie Sie eine Website erstellen und von WebMatrix auf Azure bereitstellen. Weitere Informationen zu WebMatrix erhalten Sie hier:

* [WebMatrix für Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [WebMatrix-Website](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





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













\n<!--HONumber=35.1--> 
