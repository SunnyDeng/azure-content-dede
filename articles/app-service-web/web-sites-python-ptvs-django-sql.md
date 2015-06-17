<properties 
	pageTitle="Django und SQL-Datenbank in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Enthält Informationen zum Verwenden der Python-Tools für Visual Studio eine Django-Webanwendung erstellen, die Daten in einer SQL-Datenbankinstanz speichert und für Webanwendungen in Azure-App-Dienst bereitstellen." 
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
	ms.date="04/16/2015" 
	ms.author="huguesv"/>




# Django und SQL-Datenbank in Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen wir mit [Python-Tools für Visual Studio] eine einfache Web-App für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=ZwcoGcIeHF4) zur Verfügung.

Wir erfahren, wie Sie eine SQL-Datenbank, die auf Azure gehosteten, konfigurieren Sie die Webanwendung zur Verwendung einer SQL­Datenbank und die Webanwendung zu veröffentlichen [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Weitere Artikel finden Sie im [Python Developer Center], wo die Entwicklung von Azure App Service-Web-Apps mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure-Tabellenspeicher, MySQL und SQL behandelt wird. Der Schwerpunkt dieses Artikels liegt zwar auf App Service, die Schritte sind jedoch vergleichbar mit der Entwicklung von [Azure Cloud Services].

## Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio]
 - [Python Tools 2.1 für Visual Studio, Beispiel-VSIX]
 - [Azure-SDK-Tools für VS 2013] oder [Azure-SDK-Tools für VS 2012]
 - [Python 2.7 32-bit]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete. Wir erstellen eine lokale Datenbank mithilfe von SQLite. Dann werden wir die Webanwendung lokal ausführen.

1.  Wählen Sie in Visual Studio **Datei** -> **Neues Projekt** aus.

1.  Die Projektvorlagen aus der PTVS-Beispiel-VSIX-Datei stehen unter **Python**, **Beispiele** bereit. Wählen Sie **Django-Webprojekt Umfragen** und klicken Sie auf OK, um das Projekt zu erstellen.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren. Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Wählen Sie **Python 2.7** als Basis-Interpreter.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Mit der rechten Maustaste in des Projektknoten, und wählen Sie **Python**, **Django Sync DB**.

  	!["Django Sync DB"-Befehl](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Eine Django-Verwaltungskonsole wird geöffnet. Folgen Sie den Anweisungen zum Erstellen eines Benutzers.

    Dadurch wird eine SQLite-Datenbank im Projektordner erstellt.

  	![Fenster der Django-Verwaltungskonsole](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Vergewissern Sie sich, dass die App funktioniert, indem Sie <kbd>F5</kbd> drücken.

1.  Klicken Sie auf **Anmelden** auf der Navigationsleiste oben.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Geben Sie die Anmeldeinformationen für den Benutzer ein, den Sie beim Synchronisieren der Datenbank erstellt haben.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Klicken Sie auf **Beispiel Umfragen erstellen**.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Klicken Sie auf eine Umfrage und stimmen Sie ab.

  	![Webbrowser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

## Erstellen einer SQL-Datenbank

Für die Datenbank erstellen Sie eine Azure SQL-Datenbank.

Mit den folgenden Schritten können Sie eine Datenbank erstellen.

1.  Melden Sie sich beim [Azure-Portal] an.

1.  Klicken Sie am unteren Rand des Navigationsbereichs, **NEW**., klicken Sie auf **Daten + Speicher** > **SQL-Datenbank**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png) -->

1.  Konfigurieren Sie die neue SQL-Datenbank durch eine neue Ressourcengruppe erstellen, und wählen Sie den entsprechenden Speicherort.

  	<!-- ![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png) -->

1.  Nachdem die SQL-Datenbank erstellt wurde, klicken Sie auf **in Visual Studio geöffnet** in das Fenster "Datenbank".
2.  Klicken Sie auf **Konfigurieren Sie die Firewall**.
3.  In der **Firewalleinstellungen** Blade, fügen Sie eine Firewall-Regel mit **START-IP-** und **END-IP-** auf dem Entwicklungscomputer die öffentliche IP-Adresse festgelegt. Klicken Sie auf **Speichern**.

	Damit sind Verbindungen von Ihrem Entwicklungsrechner zum Datenbankserver möglich.

4.  Klicken Sie in das Fenster "Datenbank" auf **Eigenschaften**, klicken Sie dann auf **Anzeigen von Datenbank-Verbindungszeichenfolgen**.

2.  Verwenden Sie die Schaltfläche "kopieren" den Wert eingefügt **ADO.NET** in die Zwischenablage.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Webanwendung, um die SQL-Datenbank verwenden, die wir gerade erstellt haben. Wir installieren außerdem weitere Python-Pakete, die für die Verwendung von SQL-Datenbanken mit Django erforderlich sind. Dann werden wir die Webanwendung lokal ausführen.

1.  Öffnen Sie in Visual Studio **settings.py**, aus der *Projektname* Ordner. Fügen Sie die Verbindungszeichenfolge temporär in den Editor ein. Die Verbindungszeichenfolge ist in folgendem Format:

        Server=<ServerName>,<ServerPort>;Database=<DatabaseName>;User ID=<UserName>;Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;

Bearbeiten Sie die Definition des `DATABASES` Verwenden Sie die oben aufgeführten Werte.

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

1.  Im Projektmappen-Explorer unter **Python Environments**, mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Installieren Sie Python-Paket**.

1.  Installieren Sie das Paket `pyodbc` mit **Easy_install**.

  	![Dialogfeld zur Installation des Python-Pakets](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Installieren Sie das Paket `django-pyodbc-azure` mit **Pip**.

  	![Dialogfeld zur Installation des Python-Pakets](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Mit der rechten Maustaste in des Projektknoten, und wählen Sie **Python**, **Django Sync DB**.

    Damit werden die Tabellen für die SQL-Datenbank erstellt, die wir im vorherigen Abschnitt angelegt haben. Folgen Sie den Aufforderungen zum Erstellen eines Benutzers. Er muss nicht demjenigen der im ersten Abschnitt angelegten SQLite-Datenbank entsprechen.

  	![Fenster der Django-Verwaltungskonsole](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Führen Sie die Anwendung mit `F5`. Umfragen, die mit erstellt werden **Beispiel-Umfragen erstellen** und stimmen übermittelten Daten werden in der SQL-Datenbank.


## Veröffentlichen Sie die Webanwendung in Azure-App-Dienst

Azure .NET SDK bietet eine einfache Möglichkeit, Ihre Web-Webanwendung in Azure App Service Web Apps bereitstellen.

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

1.  Ihr Webbrowser öffnet automatisch mit der veröffentlichten Web-App. Die Webanwendung ordnungsgemäß funktioniert, verwenden, sollte der **SQL** in Azure gehostete Datenbank.

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
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure-Portal]: https://portal.azure.com
[Python-Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure-SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure-SDK-Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python Tools für Visual Studio – Dokumentation]: http://pytools.codeplex.com/documentation
[Remotedebugging in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud Service-Projekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Dokumentation zu Django]: https://www.djangoproject.com/
[SQL-Datenbank]: /documentation/services/sql-database/
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->