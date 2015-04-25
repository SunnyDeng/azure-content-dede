<properties 
	pageTitle="Django und SQL-Datenbank in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Erfahren Sie, wie Sie die Python Tools für Visual Studio verwenden, um eine Django-Anwendung zu erstellen, die Daten in einer SQL-Datenbankinstanz speichert und auf einer Website bereitgestellt werden kann. 
	services="" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="huvalo"/>




# Django und SQL-Datenbank in Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen wir eine einfache Anwendung für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=ZwcoGcIeHF4) zur Verfügung.

Wir sehen uns an, wie eine in Azure gehosteter SQL-Datenbank verwendet, die App für die Nutzung einer SQL-Datenbank konfiguriert und auf einer Azure-Website veröffentlicht wird.

Weitere Artikel finden Sie im [Python Developer Center][], wo die Entwicklung von Azure Websites mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure Table Storage, MySQL und SQL behandelt wird.  Zwar dreht sich dieser Artikel um Azure-Websites, doch die Schritte sind vergleichbar mit der Entwicklung von [Azure Cloud Services][].

+ [Voraussetzungen](#prerequisites)
+ [Erstellen des Projekts](#create-the-project)
+ [Erstellen einer SQLDatenbank](#create-a-sql-database)
+ [Konfigurieren des Projekts](#configure-the-project)
+ [Veröffentlichen auf einer Azure-Website](#publish-to-an-azure-website)
+ [Nächste Schritte](#next-steps)

##<a name="prerequisites"></a>Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio][]
 - [Python Tools 2.1 für Visual Studio, Beispiel-VSIX][]
 - [Azure SDK Tools für VS 2013][] oder [Azure SDK Tools für VS 2012][]
 - [Python 2.7 (32 Bit)][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete.  Wir erstellen eine lokale Datenbank mithilfe von SQLite.  Anschließend führen wir die Anwendung lokal aus.

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt**.

1.  Die Projektvorlagen aus dem PTVS-Beispiel-VSIX stehen unter **Python**, **Beispiele** bereit.  Wählen Sie das **Polls Django-Webprojekt**, und klicken Sie auf "OK", sodass das Projekt erstellt wird.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren.  Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoExternalPackages.png)

1.  Wählen Sie **Python 2.7** als Basisinterpreter aus.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonAddVirtualEnv.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python**, **Django Sync DB** aus.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-sql/PollsDjangoSyncDB.png)

1.  Eine Django-Verwaltungskonsole wird geöffnet.  Folgen Sie den Anweisungen zum Erstellen eines Benutzers.

    Dadurch wird eine SQLite-Datenbank im Projektordner erstellt.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Drücken Sie <kbd>F5</kbd>, um sicherzustellen, dass die Anwendung funktioniert.

1.  Klicken Sie oben auf der Navigationsleiste auf **Anmelden**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Geben Sie die Anmeldeinformationen für den Benutzer ein, den Sie beim Synchronisieren der Datenbank angelegt haben.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Klicken Sie**Beispielumfrage erstellen**.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoCommonBrowserNoPolls.png)

1.  Klicken Sie auf eine Umfrage und stimmen Sie ab.

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-sql-database"></a>Erstellen einer SQL-Datenbank

Für die Datenbank erstellen wir eine Azure SQL-Datenbank.

Mit den folgenden Schritten können Sie eine Datenbank erstellen.

1.  Melden Sie sich am [Azure-Verwaltungsportal][] an.

1.  Klicken Sie unten auf der Seite auf **NEU**.

  	![New Button](./media/web-sites-python-ptvs-django-sql/PollsCommonAzurePlusNew.png)

1.  Klicken Sie auf **DATA SERVICES**, **SQL-DATENBANK** und **SCHNELLERFASSUNG**.

  	![Quick Create SQL Database](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlCreate.png)

1.  Klicken Sie zum Erstellen auf "Neuen SQL-Datenbankserver erstellen".

1.  Wählen Sie eine Region/Affinitätsgruppe, in der sich die Datenbank befinden soll. Wenn Sie die Datenbank aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen.

##<a name="configure-the-project"></a>Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Anwendung, sodass Sie die eben erstellte SQL-Datenbank verwendet.  Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden.  Wir installieren außerdem weitere Python-Pakete, die für die Verwendung von SQL-Datenbanken mit Django erforderlich sind.  Anschließend führen wir die Anwendung lokal aus.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf **SQL-DATENBANKEN** und dann auf die im Vorfeld erstellte Datenbank.

1.  Klicken Sie auf **VERWALTEN**.

  	![Manage Button](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlManage.png)

1.  Sie werden aufgefordert, die Firewallregeln zu aktualisieren. Klicken Sie auf **Ja**.  Damit sind Verbindungen von Ihrem Entwicklungsrechner zum Datenbankserver möglich.

  	![Allow Connections](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlUpdateFirewall.png)

1.  Klicken Sie auf **SQL-DATENBANKEN** und dann auf **SERVER**.  Klicken Sie auf den Server für Ihre Datenbank, dann auf **KONFIGURIEREN**.

1.  Auf dieser Seite sehen Sie die IP-Adresse jedes Computers, der sich mit dem Datenbankserver verbinden darf.  Sie sollten die IP-Adresse Ihres Computers sehen.

    Achten Sie darauf, dass unter **Zulässige Dienste** Azure-Diensten der Zugriff auf den Server erlaubt ist.  Wenn die Anwendung in einer Azure Website ausgeführt wird (was wir im nächsten Abschnitt dieses Lernprogramms tun werden), kann sie sich mit der Datenbank verbinden.  Klicken Sie **SPEICHERN**, um die Änderung zu übernehmen.

  	![Allowed Services](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlAllowedServices.png)

1.  Öffnen Sie in Visual Studio die Datei **settings.py**, die sich im Ordner  *ProjectName* befindet. Bearbeiten Sie die Definition von  `DATABASES`.

        DATABASES = {
            'default': {
                'ENGINE': 'sql_server.pyodbc',
                'NAME': '<DatabaseName>',
                'USER': '<User>@<ServerName>',
                'PASSWORD': '<Password>',
                'HOST': '<ServerName>.database.windows.net',
                'PORT': '<ServerPort>',
                'OPTIONS': {
                    'driver': 'SQL Server Native Client 11.0',
                    'MARS_Connection': 'True',
                }
            }
        }

    `<DatabaseName>`, `<User>` und `<Password>` sind die Werte, die Sie beim Erstellen der Datenbank und des Servers angegeben haben.

    Die Werte für `<ServerName>` und `<ServerPort>` werden von Azure beim Erstellen des Servers generiert und befinden sich im Abschnitt **Datenbankverbindung herstellen**.

1.  Klicken Sie im Projektmappen-Explorer unter **Python-Umgebungen** mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Python-Paket installieren**.

1.  Installieren Sie das Paket  `pyodbc` mithilfe von **easy_install**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackagePyodbc.png)

1.  Installieren Sie das Paket `django-pyodbc-azure` mit **pip**.

  	![Install Python Package Dialog](./media/web-sites-python-ptvs-django-sql/PollsDjangoSqlInstallPackageDjangoPyodbcAzure.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python**, **Django Sync DB** aus.  

    Damit werden die Tabellen für die SQL-Datenbank erstellt, die wir im vorherigen Abschnitt angelegt haben.  Folgen Sie den Aufforderungen zum Erstellen eines Benutzers. Er muss nicht demjenigen der im ersten Abschnitt angelegten SQLite-Datenbank entsprechen.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-sql/PollsDjangoConsole.png)

1.  Führen Sie die Anwendung mit <kbd>F5</kbd> aus.  Umfragen, die mit **Beispielumfrage erstellen** erstellt wurden und die durch die Abstimmung erfassten Daten werden in der SQL-Datenbank serialisiert.


##<a name="publish-to-an-azure-website"></a>Veröffentlichen auf einer Azure-Website

PTVS bietet eine einfache Möglichkeit, wie Sie Ihre Webanwendung auf einer Azure Websites bereitstellen können.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Websites**.

1.  Klicken Sie auf **Neu**, sodass eine neue Website erstellt wird.

1.  Legen Sie einen **Websitenamen** sowie eine **Region** fest, und klicken Sie auf **Erstellen**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-sql/PollsCommonCreateWebSite.png)

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch die veröffentlichte Website.  Jetzt sollte die App wie erwartet arbeiten und dabei die in Azure gehostete **SQL**-Datenbank verwenden.

    Glückwunsch!

  	![Web Browser](./media/web-sites-python-ptvs-django-sql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Django und SQL-Datenbank erfahren möchten.

- [Python Tools für Visual Studio - Dokumentation][]
  - [Webprojekte][]
  - [Cloud-Dienstprojekte][]
  - [Remotedebugging in Microsoft Azure][]
- [Dokumentation zu Django][]
- [SQL-Datenbank][]


<!--Link references-->
[Python Developer Center]: /de-de/develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools für Visual Studio - Dokumentation]: http://pytools.codeplex.com/documentation
[Remotedebugging in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud-Dienstprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Dokumentation zu Django]: https://www.djangoproject.com/
[SQL-Datenbank]: /de-de/documentation/services/sql-database/





<!--HONumber=42-->
