<properties 
	pageTitle="Django und SQL-Datenbank in Azure mit Python Tools 2.2 für Visual Studio" 
	description="Erfahren Sie, wie Sie mithilfe der Python-Tools für Visual Studio eine Django-Web-App erstellen, die Daten in einer SQL-Datenbankinstanz speichert, und die Web-App für Azure App Service-Web-Apps bereitstellen." 
	services="app-service\web" 
	tags="python"
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="11/13/2015"
	ms.author="huguesv"/>




# Django und SQL-Datenbank in Azure mit Python Tools 2.2 für Visual Studio 

In diesem Tutorial erstellen wir mit [Python-Tools für Visual Studio] eine einfache Web-App für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Tutorial steht auch als [Video](https://www.youtube.com/watch?v=ZwcoGcIeHF4) zur Verfügung.

Sie erfahren, wie Sie eine in Azure gehostete SQL-Datenbank verwenden, wie Sie die Web-App für die Nutzung einer SQL-Datenbank konfigurieren und wie Sie sie für [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) veröffentlichen.

Weitere Artikel finden Sie im [Python Developer Center], wo die Entwicklung von Azure App Service-Web-Apps mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure-Tabellenspeicher, MySQL und SQL behandelt wird. Der Schwerpunkt dieses Artikels liegt zwar auf App Service, die Schritte sind jedoch vergleichbar mit der Entwicklung von [Azure Cloud Services].

## Voraussetzungen

 - Visual Studio 2013 oder 2015
 - [Python Tools 2.2 für Visual Studio]
 - [Python Tools 2.2 für Visual Studio, Beispiel-VSIX]
 - [Azure SDK-Tools für VS 2013] oder [Azure SDK-Tools für VS 2015]
 - [Python 2.7 (32 Bit)]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete. Wir erstellen eine lokale Datenbank mithilfe von SQLite. Anschließend führen wir die Web-App lokal aus.

1.  Wählen Sie in Visual Studio **Datei** -> **Neues Projekt** aus.

1.  Die Projektvorlagen aus der PTVS-Beispiel-VSIX-Datei stehen unter **Python**, **Beispiele** bereit. Wählen Sie **Polls Django Web Project** aus, und klicken Sie auf "OK", um das Projekt zu erstellen.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren. Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Wählen Sie **Python 2.7** als Basisinterpreter aus.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python** > **Django Sync DB** aus.

  	!["Django Sync DB"-Befehl](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Eine Django-Verwaltungskonsole wird geöffnet. Folgen Sie den Anweisungen zum Erstellen eines Benutzers.

    Dadurch wird eine SQLite-Datenbank im Projektordner erstellt.

  	![Fenster der Django-Verwaltungskonsole](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Vergewissern Sie sich, dass die App funktioniert, indem Sie <kbd>F5</kbd> drücken.

1.  Klicken Sie oben auf der Navigationsleiste auf **Anmelden**.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Geben Sie die Anmeldeinformationen für den Benutzer ein, den Sie beim Synchronisieren der Datenbank erstellt haben.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Klicken Sie auf **Beispielumfrage erstellen**.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Klicken Sie auf eine Umfrage und stimmen Sie ab.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Erstellen einer SQL-Datenbank

Für die Datenbank erstellen Sie eine Azure SQL-Datenbank.

Mit den folgenden Schritten können Sie eine Datenbank erstellen.

1.  Melden Sie sich beim [Azure-Portal] an.

1.  Klicken Sie am unteren Rand des Navigationsbereichs auf **NEU**. Klicken Sie anschließend auf **Daten + Speicher** > **SQL-Datenbank**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png) -->

1.  Konfigurieren Sie die neue SQL-Datenbank, indem Sie eine neue Ressourcengruppe erstellen und den entsprechenden Speicherort auswählen.

  	<!-- ![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png) -->

1.  Klicken Sie nach der Erstellung der SQL-Datenbank auf dem Datenbankblatt auf **In Visual Studio öffnen**.
2.  Klicken Sie auf **Firewall konfigurieren**.
3.  Fügen Sie auf dem Blatt **Firewalleinstellungen** eine Firewall-Regel hinzu, und konfigurieren Sie sie mit der **START-IP** und **END-IP** der öffentlichen IP-Adresse Ihres Entwicklungscomputers. Klicken Sie auf **Speichern**.

	Damit sind Verbindungen von Ihrem Entwicklungsrechner zum Datenbankserver möglich.

4.  Klicken Sie auf dem Datenbankblatt auf **Eigenschaften** und dann auf **Verbindungszeichenfolgen anzeigen**.

2.  Mithilfe der Schaltfläche "Kopieren" können Sie den Wert für **ADO.NET** in die Zwischenablage kopieren.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Web-App, sodass sie die soeben erstellte SQL-Datenbank verwendet. Wir installieren außerdem weitere Python-Pakete, die für die Verwendung von SQL-Datenbanken mit Django erforderlich sind. Anschließend führen wir die Web-App lokal aus.

1.  Öffnen Sie in Visual Studio die Datei **settings.py** (im Ordner *<Projektname>*). Fügen Sie die Verbindungszeichenfolge temporär in den Editor ein. Die Verbindungszeichenfolge ist in folgendem Format:

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Bearbeiten Sie die Definition von `DATABASES`, sodass die oben angegebenen Werte verwendet werden:

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<UserName>',
                'PASSWORD': '{your_password_here}',
                'HOST': '<ServerName>',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

1.  Klicken Sie im Projektmappen-Explorer unter **Python-Umgebungen** mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Python-Paket installieren** aus.

1.  Installieren Sie das Paket `pyodbc` mithilfe von **pip**.

  	![Dialogfeld zur Installation des Python-Pakets](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Installieren Sie das Paket `django-pyodbc-azure` mithilfe von **pip**.

  	![Dialogfeld zur Installation des Python-Pakets](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python** > **Django Sync DB** aus.

    Damit werden die Tabellen für die SQL-Datenbank erstellt, die wir im vorherigen Abschnitt angelegt haben. Folgen Sie den Aufforderungen zum Erstellen eines Benutzers. Er muss nicht demjenigen der im ersten Abschnitt angelegten SQLite-Datenbank entsprechen.

  	![Fenster der Django-Verwaltungskonsole](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Drücken Sie `F5`, um die Anwendung auszuführen. Die mittels **Beispielumfrage erstellen** erstellten Umfragen sowie die bei der Abstimmung erfassten Daten werden in der SQL-Datenbank serialisiert.


## Veröffentlichen der Web-App in Azure App Service

Das Azure .NET SDK bietet eine einfache Möglichkeit zum Bereitstellen Ihrer Web-App in Azure App Service-Web-Apps.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

  	<!-- ![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png) -->

1.  Klicken Sie auf **Microsoft Azure-Web-Apps**.

1.  Klicken Sie auf **Neu**, um eine neue Web-App zu erstellen.

1.  Füllen Sie die folgenden Felder aus, und klicken Sie auf **Erstellen**.
	-	**Name der Web-App**
	-	**App Service-Plan**
	-	**Ressourcengruppe**
	-	**Region**
	-	Lassen Sie für **Datenbankserver** die Option **Keine Datenbank** festlegt.

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png) -->

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch mit der veröffentlichten Web-App. Die Web-App funktioniert nun wie erwartet und verwendet die von Azure gehostete **SQL**-Datenbank.

    Glückwunsch!

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Django und SQL-Datenbank erfahren möchten.

- [Python Tools für Visual Studio – Dokumentation]
  - [Webprojekte]
  - [Cloud Service-Projekte]
  - [Remotedebugging in Microsoft Azure]
- [Dokumentation zu Django]
- [SQL-Datenbank]

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure-Portal]: https://portal.azure.com
[Python-Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.2 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkID=624025
[Python Tools 2.2 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkID=624025
[Azure SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-Tools für VS 2015]: http://go.microsoft.com/fwlink/?LinkId=518003
[Python 2.7 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python Tools für Visual Studio – Dokumentation]: http://aka.ms/ptvsdocs
[Remotedebugging in Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=624026
[Webprojekte]: http://go.microsoft.com/fwlink/?LinkId=624027
[Cloud Service-Projekte]: http://go.microsoft.com/fwlink/?LinkId=624028
[Dokumentation zu Django]: https://www.djangoproject.com/
[SQL-Datenbank]: /documentation/services/sql-database/

<!---HONumber=AcomDC_1203_2015-->