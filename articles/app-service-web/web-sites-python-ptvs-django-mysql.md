<properties 
	pageTitle="Django und MySQL in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Enthält Informationen zum Verwenden der Python-Tools für Visual Studio eine Django-Webanwendung erstellen, die Daten in eine Instanz der MySQL-Datenbank gespeichert und in Azure App Service-Webanwendungen bereitzustellen." 
	services="app-service\web" 
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




# Django und MySQL in Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen wir mit [Python-Tools für Visual Studio] eine einfache Web-App für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=oKCApIrS0Lo) zur Verfügung.

Wir lernen, zum verwenden einen MySQL-Dienst auf Azure gehostet, die Web-app zur Verwendung von MySQL zu konfigurieren und die Webanwendung zu veröffentlichen [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

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

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete. Wir erstellen eine lokale Datenbank mithilfe von SQLite. Anschließend führen wir die Anwendung lokal aus.

1.  Wählen Sie in Visual Studio **Datei** -> **Neues Projekt** aus.

1.  Die Projektvorlagen aus der PTVS-Beispiel-VSIX-Datei stehen unter **Python**, **Beispiele** bereit. Wählen Sie **Django-Webprojekt Umfragen** und klicken Sie auf OK, um das Projekt zu erstellen.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren. Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-django-mysql/PollsDjangoExternalPackages.png)

1.  Wählen Sie **Python 2.7** als Basis-Interpreter.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-django-mysql/PollsCommonAddVirtualEnv.png)

1.  Mit der rechten Maustaste in des Projektknoten, und wählen Sie **Python**, **Django Sync DB**.

  	!["Django Sync DB"-Befehl](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSyncDB.png)

1.  Eine Django-Verwaltungskonsole wird geöffnet. Folgen Sie den Anweisungen zum Erstellen eines Benutzers.

    Dadurch wird eine SQLite-Datenbank im Projektordner erstellt.

  	![Fenster der Django-Verwaltungskonsole](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Vergewissern Sie sich, dass die App funktioniert, indem Sie <kbd>F5</kbd> drücken.

1.  Klicken Sie auf **Anmelden** auf der Navigationsleiste oben.

  	![Webbrowser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalMenu.png)

1.  Geben Sie die Anmeldeinformationen für den Benutzer ein, den Sie beim Synchronisieren der Datenbank erstellt haben.

  	![Webbrowser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserLocalLogin.png)

1.  Klicken Sie auf **Beispiel Umfragen erstellen**.

  	![Webbrowser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoCommonBrowserNoPolls.png)

1.  Klicken Sie auf eine Umfrage und stimmen Sie ab.

  	![Webbrowser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoSqliteBrowser.png)

## Erstellen einer MySQL-Datenbank

Als Datenbank erstellen Sie eine gehostete ClearDB MySQL-Datenbank in Azure.

Alternativ können Sie einen eigenen, auf Azure ausgeführten virtuellen Computer erstellen und dann MySQL selbst installieren und verwalten.

Mit den folgenden Schritten können Sie eine Datenbank im Rahmen eines kostenlosen Plans erstellen.

1.  Melden Sie sich beim [Azure-Portal] an.

1.  Klicken Sie unten auf der Seite auf **NEU**. Klicken Sie auf **Daten + Speicher** > **Azure Marketplace**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-django-mysql/PollsCommonAzurePlusNew.png)-->

1.  Typ "** Mysql **" in das Suchfeld, klicken Sie dann auf **MySQL-Datenbank**, und klicken Sie dann auf **Erstellen**.

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon1.png) -->

1.  Konfigurieren Sie die neue MySQL-Datenbank durch eine neue Ressourcengruppe erstellen, und wählen Sie den entsprechenden Speicherort.

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-django-mysql/PollsDjangoClearDBAddon2.png) -->

1.  Sobald die MySQL-Datenbank erstellt wurde, klicken Sie auf **Eigenschaften** in das Fenster "Datenbank".
2.  Verwenden Sie die Schaltfläche "kopieren" den Wert eingefügt **VERBINDUNGSZEICHENFOLGE** in die Zwischenablage.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Webanwendung Verwendung die MySQL-Datenbank, die wir gerade erstellt haben. Wir installieren außerdem weitere Python-Pakete, die für die Verwendung von MySQL-Datenbanken mit Django erforderlich sind. Dann werden wir die Webanwendung lokal ausführen.

1.  Öffnen Sie in Visual Studio **settings.py**, aus der *Projektname* Ordner. Fügen Sie die Verbindungszeichenfolge temporär in den Editor ein. Die Verbindungszeichenfolge ist in folgendem Format:

        Database=<NAME>;Data Source=<HOST>;User Id=<USER>;Password=<PASSWORD>

    Die Standarddatenbank ändern **ENGINE** MySQL, und legen Sie die Werte für **NAME**, **Benutzer**, **Kennwort** und **HOST** aus der **CONNECTIONSTRING**.

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


1.  Im Projektmappen-Explorer unter **Python Environments**, mit der rechten Maustaste auf die virtuelle Umgebung, und wählen Sie **Installieren Sie Python-Paket**.

1. Installieren Sie das Paket `mysql-python` mit **Easy_install**.

  	![Dialogfeld "Paket installieren"](./media/web-sites-python-ptvs-django-mysql/PollsDjangoMySQLInstallPackage.png)

1.  Mit der rechten Maustaste in des Projektknoten, und wählen Sie **Python**, **Django Sync DB**.

    Damit werden die Tabellen für die MySQL-Datenbank erstellt, die wir im vorherigen Abschnitt angelegt haben. Folgen Sie den Aufforderungen zum Erstellen eines Benutzers. Er muss nicht demjenigen der im ersten Abschnitt angelegten SQLite-Datenbank entsprechen.

  	![Fenster der Django-Verwaltungskonsole](./media/web-sites-python-ptvs-django-mysql/PollsDjangoConsole.png)

1.  Führen Sie die Anwendung mit `F5`. Umfragen, die mit erstellt werden **Beispiel-Umfragen erstellen** und stimmen übermittelten Daten werden in der MySQL-Datenbank.

## Veröffentlichen Sie die Webanwendung in Azure-App-Dienst

Azure .NET SDK bietet eine einfache Möglichkeit, Ihre Webanwendung auf Azure-App-Dienst bereitstellen.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

  	![Dialogfeld "Web veröffentlichen"](./media/web-sites-python-ptvs-django-mysql/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Web-Apps**.

1.  Klicken Sie auf **Neu**, um eine neue Web-App zu erstellen.

1.  Füllen Sie die folgenden Felder aus, und klicken Sie auf **Erstellen**.
	-	**Name der Web-App**
	-	**App Service-Plan**
	-	**Ressourcengruppe**
	-	**Region**
	-	Lassen Sie für **Datenbankserver** die Option **Keine Datenbank** festlegt.

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-django-mysql/PollsCommonCreateWebSite.png) -->

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch mit der veröffentlichten Web-App. Die Webanwendung ordnungsgemäß funktioniert, verwenden, sollte der **MySQL** in Azure gehostete Datenbank.

    Glückwunsch!

  	![Webbrowser](./media/web-sites-python-ptvs-django-mysql/PollsDjangoAzureBrowser.png)

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Django und MySQL erfahren möchten.

- [Python Tools für Visual Studio – Dokumentation]
  - [Webprojekte]
  - [Cloud Service-Projekte]
  - [Remotedebugging in Microsoft Azure]
- [Dokumentation zu Django]
- [MySQL]

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
[MySQL]: http://www.mysql.com/
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->