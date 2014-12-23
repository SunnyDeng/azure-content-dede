<properties linkid="web-sites-python-ptvs-django-mysql" title="Django and MySQL on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Django und MySQL in Azure mit Python Tools 2.1 für Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Django application that stores data in a MySQL database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Django und MySQL in Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen Sie eine einfache Umfrageanwendung mithilfe einer der PTVS-Beispielvorlagen.

Sie erfahren, wie Sie einen auf Azure gehosteten MySQL-Dienst verwenden, eine Anwendung zum Verwenden von MySQL konfigurieren und die Anwendung in einer Azure-Website veröffentlichen.

Im [Python Developer Center][] finden Sie weitere Artikel zur Entwicklung von Azure-Websites mit PTVS mithilfe der Bottle-, Flask und Django-Webframeworks und MongoDB-, Azure-Tabellenspeicher-, MySQL- und SQL-Datenbankdiensten.  In diesem Artikel geht es primär um Azure-Websites, die Schritte sind aber mit der Entwicklung von [Azure Cloud Services][] vergleichbar.

+ [Voraussetzungen](#prerequisites)
+ [Erstellen des Projekts](#create-the-project)
+ [Erstellen einer MySQL-Datenbank](#create-a-mysql-database)
+ [Konfigurieren des Projekts](#configure-the-project)
+ [Veröffentlichen in einer Azure-Website](#publish-to-an-azure-website)
+ [Nächste Schritte](#next-steps)

##<a name="prerequisites"></a>Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2,1 für Visual Studio][]
 - [Python Tools 2.1 für Visual Studio - Beispiel-VSIX][]
 - [Azure SDK-Tools für VS 2013][] oder [Azure SDK-Tools für VS 2012][]
 - [Python 2.7 32-Bit][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Erstellen des Projekts

In diesem Abschnitt erstellen Sie ein Visual Studio-Projekt anhand einer Beispielvorlage. Sie erstellen eine virtuelle Umgebung und installieren erforderliche Pakete.  Sie erstellen eine lokale Datenbank mit sqlite.  Dann führen Sie die Anwendung lokal aus.

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt**.

1.  Die Projektvorlagen aus den PTVS-Beispieldateien sind unter **Python**, **Beispiele** verfügbar.  Wählen Sie **Polls Django Web Project**, und klicken Sie auf "OK", um das Projekt zu erstellen.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Sie werden aufgefordert, die externen Pakete zu installieren.  Wählen Sie **In einer virtuellen Umgebung installieren**.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Wählen Sie **Python 2.7** als Basis-Interpreter aus.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python**, **Django Sync DB**.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Eine Django-Verwaltungskonsole wird geöffnet.  Folgen Sie den Anweisungen zum Erstellen eines Benutzers.

    Dadurch wird eine sqlite-Datenbank im Projektordner erstellt.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Vergewissern Sie sich, dass die Anwendung funktioniert, indem Sie <kbd>F5</kbd> drücken.

1.  Klicken Sie oben in der Navigationsleiste auf **Anmelden**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Geben Sie die Anmeldeinformationen für den Benutzer ein, den Sie beim Synchronisieren der Datenbank erstellt haben.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Klicken Sie auf **Beispielumfragen erstellen**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Klicken Sie auf eine Umfrage und stimmen Sie ab.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Erstellen einer MySQL-Datenbank

Als Datenbank erstellen Sie eine gehostete ClearDB MySQL-Datenbank in Azure.

Alternativ können Sie einen eigenen, auf Azure ausgeführten virtuellen Computer erstellen und dann MySQL selbst installieren und verwalten.

Sie können eine Datenbank mit einem kostenlosen Plan erstellen, indem Sie folgende Schritte ausführen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.

1.  Klicken Sie unten im Navigationsbereich auf **NEU**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Klicken Sie auf **SPEICHERN** und dann auf **ClearDB MySQL Database**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  Geben Sie im Feld "Name" einen Namen für den Datenbankdienst ein.

1.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Datenbankdienst befinden soll. Wenn Sie die Datenbank aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung bereitstellen.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Klicken Sie auf **KAUFEN**.

##<a name="configure-the-project"></a>Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir die Anwendung für die Verwendung der soeben erstellten MySQL-Datenbank.  Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden.  Es werden auch die zusätzlichen Python-Pakete erstellt, die zum Verwenden der MySQL-Datenbanken mit Django erforderlich sind.  Dann führen Sie die Anwendung lokal aus.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf **ADD-ONS** und dann auf den weiter oben erstellten ClearDB MySQL-Datenbankdienst.

1.  Klicken Sie auf **VERBINDUNGSINFORMATIONEN**.  Sie können die Kopierschaltfläche verwenden, um den Wert von **CONNECTIONSTRING** in der Zwischenablage abzulegen.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Öffnen Sie in Visual Studio im Ordner *ProjectName* die Datei **settings.py**.  Fügen Sie die Verbindungszeichenfolge vorübergehend im Editor ein.  Die Verbindungszeichenfolge hat folgendes Format:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Ändern Sie die Standarddatenbank **ENGINE** zur Verwendung von MySQL, und legen Sie die Werte für **NAME**, **BENUTZER**, **KENNWORT** und **HOST** in **CONNECTIONSTRING** fest.

        DATABASES = {
            'default': {
                'ENGINE': 'django.db.backends.mysql',
                'NAME': '<Database>',
                'USER': '<User Id>',
                'PASSWORD': '<Password>',
                'HOST': '<Data Source>',
                'PORT': '',
            }
        }


1.  Klicken Sie im Projektmappen-Explorer unter **Python-Umgebungen** mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Python-Paket installieren** aus.

1. Installieren Sie das Paket "mysql-python" mit **easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python**, **Django Sync DB**.  

    Dadurch werden die Tabellen für die MySQL-Datenbank erstellt, die Sie im vorherigen Abschnitt erstellt haben.  Folgen Sie den Anweisungen zum Erstellen eines Benutzers, der nicht dem Benutzer in der im ersten Abschnitt erstellten sqlite-Datenbank entsprechen muss.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Führen Sie die Anwendung mit <kbd>F5</kbd> aus.  Umfragen, die mit **Beispielumfragen erstellen** erstellt werden, und die durch Abstimmung gesandten Daten werden in der MySQL-Datenbank serialisiert.

##<a name="publish-to-an-azure-website"></a>Veröffentlichen in einer Azure-Website

PTVS bietet eine einfache Möglichkeit, Ihre Webanwendung auf einer Azure-Website bereitzustellen.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Websites**.

1.  Klicken Sie auf **Neu**, um eine neue Website zu erstellen.

1.  Wählen Sie einen **Websitenamen** und eine **Region** aus, und klicken Sie auf **Erstellen**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Die veröffentlichte Website wird automatisch im Webbrowser geöffnet.  Sie Anwendung sollte jetzt wie erwartet funktionieren und die auf Azure gehostete **MySQL**-Datenbank verwenden.

    Glückwunsch!

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Nächste Schritte

Folgen Sie diesen Links, um weitere Informationen zu Python-Tools für Visual Studio, Django und MySQL zu erhalten.

- [Python Tools für Visual Studio - Dokumentation][]
  - [Webprojekte][]
  - [Cloud-Dienstprojekte][]
  - [Remotedebugging auf Microsoft Azure][]
- [Django-Dokumentation][]
- [MySQL][]


<!--Link references-->
[Python Developer Center]: /en-us/develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[Python Tools 2,1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio - Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK-Tools für Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-Tools für Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-Bit]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python Tools für Visual Studio - Dokumentation]: http://pytools.codeplex.com/documentation
[Remotedebugging auf Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud-Dienstprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Django-Dokumentation]: https://www.djangoproject.com/
[MySQL]: http://www.mysql.com/
