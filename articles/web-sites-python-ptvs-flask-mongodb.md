<properties linkid="web-sites-python-ptvs-flask-mongodb" title="Flask and MongoDB on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask und MongoDB unter Azure mit Python-Tools 2.1 für Visual Studio" description="Learn how to use the Python Tools for Visual Studio to create a Flask application that stores data in a MongoDB database instance and can be deployed to a web site." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask und MongoDB unter Azure mit Python-Tools 2.1 für Visual Studio

In diesem Lernprogramm erstellen Sie eine einfache Umfrageanwendung mithilfe einer der PTVS-Beispielvorlagen.

Die Umfrageanwendung definiert eine Abstraktion für das Repository, damit Sie problemlos zwischen verschiedenen Typen von Repositorys (InMemory, Azure-Tabellenspeicher, MongoDB) wechseln können.

Sie erfahren, wie einer der gehosteten MongoDB-Dienste in Azure verwendet wird, wie die Anwendung für die Verwendung von MongoDB konfiguriert wird und wie die Anwendung in einer Azure-Website veröffentlicht wird.

Im [Python Developer Center][] finden Sie weitere Artikel, in denen die Entwicklung von Azure-Websites mit PTVS mithilfe von Bottle-, Flask- und Django-Web-Frameworks, mit MongoDB, Azure-Tabellenspeicher, MySQL und SQL-Datenbankdiensten behandelt wird.  Dieser Artikel befasst sich zwar mit Azure-Websites, die Schritte ähneln jedoch der Entwicklung von [Azure-Cloud-Diensten][].

+ [Voraussetzungen](#prerequisites)
+ [Erstellen des Projekts](#create-the-project)
+ [Erstellen einer MongoDB-Datenbank](#create-a-mongodb-database)
+ [Konfigurieren des Projekts](#configure-the-project)
+ [Untersuchen der MongoDB-Datenbank](#explore-the-mongodb-database)
+ [Veröffentlichen in einer Azure-Website](#publish-to-an-azure-website)
+ [Konfigurieren der Azure-Website](#configure-the-azure-website)
+ [Nächste Schritte](#next-steps)

##<a name="prerequisites"></a>Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2,1 für Visual Studio][]
 - [Python Tools 2.1 für Visual Studio - Beispiel-VSIX][]
 - [Azure SDK-Tools für VS 2013][] oder [Azure SDK-Tools für VS 2012][]
 - [Python 2.7 32-Bit][] oder [Python 3.4 32-Bit][]
 - [RoboMongo][] (optional)

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Erstellen des Projekts

In diesem Abschnitt erstellen Sie ein Visual Studio-Projekt anhand einer Beispielvorlage.  Sie erstellen eine virtuelle Umgebung und installieren erforderliche Pakete.  Dann führen Sie die Anwendung lokal mithilfe des standardmäßigen InMemory-Repository aus.

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt** aus. 

1.  Die Projektvorlagen aus dem PTVS-Beispiel-VSIX sind unter **Python**, **Beispiele** verfügbar.  Wählen Sie **Umfragen-Flask-Webprojekt** aus, und klicken Sie auf "OK", um das Projekt zu erstellen.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren.  Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  Wählen Sie **Python 2.7** oder **Python 3.4** als Basisinterpreten aus.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  Vergewissern Sie sich, dass die Anwendung funktioniert, indem Sie <kbd>F5</kbd> drücken.  Standardmäßig verwendet die Anwendung ein InMemory-Repository, das keine Konfiguration erfordert.  Alle Daten gehen verloren, wenn der Webserver beendet wird.

1.  Klicken Sie auf **Beispielumfragen erstellen**, und klicken Sie dann auf eine Umfrage, und stimmen Sie ab.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

##<a name="create-a-mongodb-database"></a>Erstellen einer MongoDB-Datenbank

Als Datenbank erstellen Sie eine gehostete MongoLab-Datenbank in Azure.

Alternativ können Sie einen eigenen, auf Azure ausgeführten virtuellen Computer erstellen und dann MongoDB selbst installieren und verwalten.

Mithilfe dieser Schritte können Sie eine kostenlose Testversion mit MongoLab erstellen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.

1.  Klicken Sie unten im Navigationsbereich auf**NEU**.

  	![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png)

1.  Klicken Sie auf **SPEICHERN** und dann auf **MongoLab**.

  	![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png)

1.  Geben Sie im Feld "Name" einen Namen für den Datenbankdienst ein.

1.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Datenbankdienst befinden soll. Wenn Sie die Datenbank aus Ihrer Azure-Anwendung verwenden, wählen Sie die gleiche Region aus, in der Sie auch Ihre Anwendung bereitstellen.

  	![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png)

1.  Klicken Sie auf **KAUFEN**.

##<a name="configure-the-project"></a>Konfigurieren des Projekts

In diesem Abschnitt konfigurieren Sie die Anwendung für die Verwendung der soeben erstellten MongoDB-Datenbank.  Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden.  Dann führen Sie die Anwendung lokal aus.

1.  Klicken Sie in [Azure-Verwaltungsportal][] auf **ADD-ONS**, und klicken Sie dann auf den MongoLab-Dienst, den Sie zuvor erstellt haben.

1.  Klicken Sie auf **VERBINDUNGSINFORMATIONEN**.  Sie können den Wert von **MONGOLAB\_URI** mithilfe der Kopierschaltfläche in die Zwischenablage kopieren.

  	![Connection Info Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  Klicken Sie in Visual Studio mit der rechten Maustaste im Projektmappen-Explorer auf Ihren Projektknoten, und wählen Sie **Eigenschaften** aus.  Klicken Sie auf die Registerkarte **Debuggen**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  Legen Sie die Werte der für die Anwendung erforderlichen Umgebungsvariablen in **Serverbefehl debuggen**, **Umgebung** fest.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<Wert von MONGOLAB_URI>
        MONGODB_DATABASE=<Datenbankname>

Dadurch werden die Umgebungsvariablen beim **Starten des Debuggens** festgelegt.  Wenn die Variablen beim **Starten ohne Debuggen** festgelegt werden sollen, legen Sie die gleichen Werte auch unter **Serverbefehl ausführen** fest.

Alternativ können Sie Umgebungsvariablen mithilfe der Windows-Systemsteuerung definieren.  Dies ist eine bessere Option, wenn Sie keine Anmeldeinformationen im Quellcode/in der Projektdatei speichern möchten.  Beachten Sie, dass Sie Visual Studio neu starten müssen, damit die neuen Umgebungswerte für die Anwendung verfügbar sind.

1.  Der Code, der das MongoDB-Repository implementiert, befindet sich in **models/mongodb.py**.

1.  Führen Sie die Anwendung mit <kbd>F5</kbd> aus.  Umfragen, die über **Beispielumfragen erstellen** erstellt werden und die durch Abstimmung übermittelten Daten werden in MongoDB serialisiert.

1.  Navigieren Sie zur Seite **Info**, um zu überprüfen, ob die Anwendung das **MongoDB**-Repository verwendet.

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

##<a name="explore-the-mongodb-database"></a>Untersuchen der MongoDB-Datenbank

Sie können eine Anwendung wie [RoboMongo][] verwenden, um Abfragen und Bearbeitungen an einer MongoDB-Datenbank vorzunehmen.  In diesem Abschnitt verwenden wir RoboMongo zum Anzeigen des Inhalts der Umfragen-Anwendungsdatenbank.

1.  Erstellen Sie eine neue Verbindung.  Sie benötigen den **MONGOLAB\_URI**, den wir im vorherigen Abschnitt abgerufen haben.

Beachten Sie das Format des URI: `mongodb://<Name>:<Kennwort>@<Adresse>:<Port>/<Name>`

Der Name entspricht dem Namen, der beim Erstellen des Diensts mit Azure eingegeben wurde.  Er wird sowohl für den Namen der Datenbank als auch für den Benutzernamen verwendet.

1.  Legen Sie auf der Verbindungsseite den **Namen** auf einen beliebigen Namen für die Verbindung fest.  Legen Sie auch die Felder **Adresse** und **Port** auf die *Adresse* und den *Port* aus **MONGOLAB\_URI** fest.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Legen Sie auf der Authentifizierungsseite die **Datenbank** und den **Benutzernamen** auf den *Namen* aus **MONGOLAB\_URI** fest.  Legen Sie das **Kennwort** auf das *Kennwort* aus **MONGOLAB\_URI** fest.

  	![Connection Settings Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Speichern Sie, und stellen Sie eine Verbindung zur Datenbank her.  Sie können jetzt die Umfragenauflistung abfragen.

  	![RoboMongo Query Results](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

##<a name="publish-to-an-azure-website"></a>Veröffentlichen in einer Azure-Website

PTVS bietet eine einfache Möglichkeit, Ihre Webanwendung auf einer Azure-Website bereitzustellen.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Websites**.

1.  Klicken Sie auf **Neu**, um eine neue Website zu erstellen.

1.  Wählen Sie einen **Websitenamen** und eine **Region** aus, und klicken Sie auf **Erstellen**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png)

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Die veröffentlichte Website wird automatisch im Webbrowser geöffnet.  Wenn Sie die Info-Seite öffnen, sehen Sie, dass es sich um ein **InMemory**-Repository handelt, nicht um das **Azure-Tabellenspeicher**-Repository.

Das liegt daran, dass die Umgebungsvariablen nicht auf der Azure-Website festgelegt wurden, daher werden die in **settings.py** angegebenen Standardwerte verwendet.

##<a name="configure-the-azure-website"></a>Konfigurieren der Azure-Website

In diesem Abschnitt werden wir die Umgebungsvariablen für die Site konfigurieren.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf die Site, die im vorherigen Abschnitt erstellt wurde.

1.  Klicken Sie im oberen Menü auf **KONFIGURIEREN**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Scrollen Sie nach unten zum Abschnitt **App-Einstellungen**, und legen Sie die Werte für **REPOSITORY\_NAME**, **MONGODB\_HOST** und **MONGODB\_DATABASE** fest, wie im Abschnitt oben beschrieben.

  	![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  Klicken Sie im unteren Menü auf **SPEICHERN**, auf **NEUSTART** und zuletzt auf **DURCHSUCHEN**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Die Anwendung sollte wie erwartet funktionieren und das **MongoDB**-Repository verwenden.

    Glückwunsch!

  	![Web Browser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Nächste Schritte

Unter den folgenden Links finden Sie weitere Informationen zu Python-Tools für Visual Studio, Flask und MongoDB.

- [Python Tools für Visual Studio - Dokumentation][]
  - [Webprojekte][]
  - [Cloud-Dienstprojekte][]
  - [Remotedebugging auf Microsoft Azure][]
- [Flask-Dokumentation][]
- [MongoDB][]
- [PyMongo-Dokumentation][]
- [PyMongo][]


<!--Link references-->
[Python Developer Center]: /en-us/develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs/

<!--External Link references-->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools 2,1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio - Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK-Tools für Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-Tools für Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio - Dokumentation]: http://pytools.codeplex.com/documentation
[Flask-Dokumentation]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[PyMongo-Dokumentation]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Remotedebugging auf Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud-Dienstprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
