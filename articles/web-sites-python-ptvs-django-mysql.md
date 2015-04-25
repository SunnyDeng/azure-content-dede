<properties 
	pageTitle="Django und MySQL in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Erfahren Sie, wie Sie die Python Tools für Visual Studio verwenden, um eine Django-Anwendung zu erstellen, die Daten in einer MySQL-Datenbankinstanz speichert und auf einer Website bereitgestellt werden kann. 
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




# Django und MySQL in Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen wir eine einfache Anwendung für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=oKCApIrS0Lo) zur Verfügung.

Wir sehen uns an, wie ein in Azure gehosteter MySQL-Dienst verwendet, die App für die Nutzung von MySQL konfiguriert und auf einer Azure-Website veröffentlicht wird.

Weitere Artikel finden Sie im [Python Developer Center][], wo die Entwicklung von Azure Websites mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure Table Storage, MySQL und SQL behandelt wird.  Zwar dreht sich dieser Artikel um Azure-Websites, doch die Schritte sind vergleichbar mit der Entwicklung von [Azure Cloud Services][].

+ [Voraussetzungen](#prerequisites)
+ [Erstellen des Projekts](#create-the-project)
+ [Erstellen einer MySQL-Datenbank](#create-a-mysql-database)
+ [Konfigurieren des Projekts](#configure-the-project)
+ [Veröffentlichen auf einer Azure-Website](#publish-to-an-azure-website)
+ [Nächste Schritte](#next-steps)

##<a name="prerequisites"></a>Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio][]
 - [Python Tools 2.1 für Visual Studio, Beispiel-VSIX][]
 - [Azure SDK Tools für VS 2013][] oder [Azure SDK Tools für VS 2012][]
 - [Python 2.7 (32 Bit)][]

> [AZURE.NOTE]
> Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Sie können Ihre <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">Vorteile für MSDN-Abonnenten aktivieren</a> oder <a href="http://azure.microsoft.com/pricing/free-trial/"> sich für eine kostenlose Testversion anmelden</a>.
> 
> Wenn Sie Azure Website ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/?language=python">https://trywebsites.azurewebsites.net</a>, wo Sie sofort kostenlos eine kurzlebige ASP.NET Starter Site in Azure Websites erstellen können. Keine Kreditkarte erforderlich, keine Verpflichtungen.

##<a name="create-the-project"></a>Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete.  Wir erstellen eine lokale Datenbank mithilfe von SQLite.  Anschließend führen wir die Anwendung lokal aus.

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt**.

1.  Die Projektvorlagen aus dem PTVS-Beispiel-VSIX stehen unter **Python**, **Beispiele** bereit.  Wählen Sie das **Polls Django-Webprojekt**, und klicken Sie auf "OK", sodass das Projekt erstellt wird.

  	![New Project Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren.  Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![External Packages Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Wählen Sie **Python 2.7** als Basisinterpreter aus.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python**, **Django Sync DB** aus.

  	![Django Sync DB Command](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Eine Django-Verwaltungskonsole wird geöffnet.  Folgen Sie den Anweisungen zum Erstellen eines Benutzers.

    Dadurch wird eine SQLite-Datenbank im Projektordner erstellt.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Drücken Sie <kbd>F5</kbd>, um sicherzustellen, dass die Anwendung funktioniert.

1.  Klicken Sie oben auf der Navigationsleiste auf **Anmelden**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Geben Sie die Anmeldeinformationen für den Benutzer ein, den Sie beim Synchronisieren der Datenbank angelegt haben.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Klicken Sie**Beispielumfrage erstellen**.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Klicken Sie auf eine Umfrage und stimmen Sie ab.

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

##<a name="create-a-mysql-database"></a>Erstellen einer MySQL-Datenbank

Für die Datenbank erstellen wird eine auf Azure gehostete ClearDB MySQL-Datenbank.

Alternativ können Sie einen eigenen virtuellen Computer erstellen, der auf Azure läuft und dort MySQL selbst installieren und verwalten.

Mit den folgenden Schritten können Sie eine Datenbank im Rahmen eines kostenlosen Plans erstellen.

1.  Melden Sie sich am [Azure-Verwaltungsportal][] an.

1.  Klicken Sie unten auf der Seite auf **NEU**.

  	![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)

1.  Klicken Sie auf **STORE**, dann auf**ClearDB MySQL-Datenbank**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png)

1.  Geben Sie unter Name einen Namen für den Datenbankdienst ein.

1.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Datenbankdienst befinden soll. Wenn Sie die Datenbank aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png)

1.  Klicken Sie auf **KAUFEN**.

##<a name="configure-the-project"></a>Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Anwendung, sodass Sie die eben erstellte MySQL-Datenbank verwendet.  Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden.  Wir installieren außerdem weitere Python-Pakete, die für die Verwendung von MySQL-Datenbanken mit Django erforderlich sind.  Anschließend führen wir die Anwendung lokal aus.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf **ADD-ONS** und dann auf den im Vorfeld erstellten ClearDB MySQL-Datenbankdienst.

1.  Klicken Sie auf **VERBINDUNGSINFO**.  Sie können die Schaltfläche "Kopieren" verwenden, um den Wert von **VERBINDUNGSZEICHENFOLGE** in der Zwischenablage abzulegen.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLConnectionInfo.png)

1.  Öffnen Sie in Visual Studio die Datei **settings.py**, die sich im Ordner  *ProjectName* befindet.  Fügen Sie die Verbindungszeichenfolge temporär in den Editor ein.  Die Verbindungszeichenfolge ist in folgendem Format:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Ändern Sie die Standarddatenbank **ENGINE** auf die Verwendung von MySQL, und legen Sie die Werte für **NAME**, **BENUTZER**, **KENNWORT** und **HOST** in der **VERBINDUNGSZEICHENFOLGE** fest.

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


1.  Klicken Sie im Projektmappen-Explorer unter **Python-Umgebungen** mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Python-Paket installieren**.

1. Installieren Sie das Paket  `mysql-python` mithilfe von **easy_install**.

  	![Install Package Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Klicken Sie mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Python**, **Django Sync DB** aus.  

    Damit werden die Tabellen für die MySQL-Datenbank erstellt, die wir im vorherigen Abschnitt angelegt haben.  Folgen Sie den Aufforderungen zum Erstellen eines Benutzers. Er muss nicht demjenigen der im ersten Abschnitt angelegten SQLite-Datenbank entsprechen.

  	![Django Management Console Window](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Führen Sie die Anwendung mit <kbd>F5</kbd> aus.  Umfragen, die mit **Beispielumfrage erstellen** erstellt wurden und die durch die Abstimmung erfassten Daten werden in der MySQL-Datenbank serialisiert.

##<a name="publish-to-an-azure-website"></a>Veröffentlichen auf einer Azure-Website

PTVS bietet eine einfache Möglichkeit, wie Sie Ihre Webanwendung auf einer Azure Websites bereitstellen können.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen**.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Websites**.

1.  Klicken Sie auf **Neu**, sodass eine neue Website erstellt wird.

1.  Legen Sie einen **Websitenamen** sowie eine **Region** fest, und klicken Sie auf **Erstellen**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png)

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch die veröffentlichte Website.  Jetzt sollte die App wie erwartet arbeiten und dabei die in Azure gehostete **MySQL**-Datenbank verwenden.

    Glückwunsch!

  	![Web Browser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

##<a name="next-steps"></a>Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Django und MySQL erfahren möchten.

- [Python Tools für Visual Studio - Dokumentation][]
  - [Webprojekte][]
  - [Cloud-Dienstprojekte][]
  - [Remotedebugging in Microsoft Azure][]
- [Dokumentation zu Django][]
- [MySQL][]


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
[MySQL]: http://www.mysql.com/





<!--HONumber=42-->
