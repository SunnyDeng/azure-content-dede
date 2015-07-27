<properties 
	pageTitle="Flask und MongoDB in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Erfahren Sie, wie Sie mithilfe der Python-Tools für Visual Studio eine Flask-Web-App erstellen, die Daten in einer MongoDB-Datenbankinstanz speichert, und die Web-App für Azure App Service-Web-Apps bereitstellen." 
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



# Flask und MongoDB in Azure mit Python Tools 2.1 für Visual Studio

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

In diesem Lernprogramm erstellen wir mit [Python-Tools für Visual Studio] eine einfache Web-App für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=eql-crFgrAE) zur Verfügung.

Die Umfrage-Web-App definiert für ihr Repository eine Abstraktion. Sie können also einfach zwischen unterschiedlichen Repository-Typen wechseln (In-Memory, Azure-Tabellenspeicher, MongoDB).

Wir sehen uns an, wie Sie einen der gehosteten MongoDB-Dienste in Azure verwenden, die Web-App für die Nutzung von MongoDB konfigurieren und sie in [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) veröffentlichen.

Weitere Artikel finden Sie im [Python Developer Center], wo die Entwicklung von Azure App Service-Web-Apps mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure-Tabellenspeicher, MySQL und SQL behandelt wird. Der Schwerpunkt dieses Artikels liegt zwar auf App Service, die Schritte sind jedoch vergleichbar mit der Entwicklung von [Azure Cloud Services].

## Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio]
 - [Python Tools 2.1 für Visual Studio, Beispiel-VSIX]
 - [Azure-SDK-Tools für VS 2013] oder [Azure-SDK-Tools für VS 2012]
 - [Python 2.7 32-Bit] oder [Python 3.4 32-Bit]
 - [RoboMongo](optional)

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete. Dann führen wir die App lokal aus und verwenden dabei das Standard-Repository im Arbeitsspeicher.

1.  Wählen Sie in Visual Studio **Datei** -> **Neues Projekt** aus. 

1.  Die Projektvorlagen aus der PTVS-Beispiel-VSIX-Datei stehen unter **Python**, **Beispiele** bereit. Wählen Sie **Polls Flask Web Project** aus, und klicken Sie auf "OK", um das Projekt zu erstellen.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren. Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskExternalPackages.png)

1.  Wählen Sie **Python 2.7** oder **Python 3.4** als Basisinterpreter.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAddVirtualEnv.png)

1.  Drücken Sie `F5`, um sicherzustellen, dass die Anwendung funktioniert. Standardmäßig verwendet die Anwendung ein Repository im Arbeitsspeicher, das nicht konfiguriert werden muss. Wird der Server angehalten, gehen alle Daten verloren.

1.  Klicken Sie auf **Beispielumfragen erstellen**, und klicken Sie dann auf eine Umfrage und Abstimmung.

  	![Webbrowser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskInMemoryBrowser.png)

## Erstellen einer MongoDB-Datenbank

Als Datenbank erstellen Sie eine gehostete MongoLab-Datenbank in Azure.

Alternativ können Sie einen eigenen, auf Azure ausgeführten virtuellen Computer erstellen und dann MongoDB selbst installieren und verwalten.

Mit den folgenden Schritten können Sie einen kostenlosen Test bei MongoLab erstellen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal] an.

1.  Klicken Sie unten auf der Seite auf **NEU**.

  	<!-- ![New Button](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonAzurePlusNew.png) -->

1.  Klicken Sie auf **MARKETPLACE**, auf **MongoLab** und anschließend auf **Weiter**.

  	<!-- ![Choose Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon1.png) -->

1.  Geben Sie im Feld "Name" einen Namen für den Datenbankdienst ein.

1.  Wählen Sie eine Region aus, in der sich der Datenbankdienst befinden soll. Wenn Sie die Datenbank aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen.

  	<!-- ![Personalize Add-on Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabAddon2.png) -->

1.  Klicken Sie auf **Weiter** und anschließend auf **KAUFEN**.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Anwendung, sodass Sie die eben erstellte MongoDB-Datenbank verwendet. Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden. Anschließend führen wir die Anwendung lokal aus.

1.  Klicken Sie im [Azure-Verwaltungsportal] auf **MARKETPLACE** und dann auf den im Vorfeld erstellten MongoLab-Dienst.

1.  Klicken Sie auf **VERBINDUNGSINFORMATIONEN**. Sie können die Schaltfläche "Kopieren" verwenden, um den Wert von **MONGOLAB_URI** in der Zwischenablage abzulegen.

  	![Dialogfeld mit Verbindungsinformationen](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Eigenschaften** aus. Klicken Sie auf die Registerkarte **Debuggen**.

  	![Debugeinstellungen für das Projekt](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBProjectDebugSettings.png)

1.  Legen Sie die Werte der von der App benötigten Umgebungsvariablen in **Serverbefehl debuggen**, **Umgebung** fest.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Damit werden die Umgebungsvariablen festgelegt, wenn Sie das **Debugging starten**. Sollen die Variablen dann festgelegt werden, wenn Sie **ohne Debugging starten**, legen Sie dieselben Werte auch unter **Serverbefehl ausführen** fest.

    Alternativ können Sie Umgebungsvariablen mithilfe der Windows Systemsteuerung definieren. Das ist eine bessere Option wenn Sie das Speichern von Anmeldeinformationen im Quellcode bzw. der Projektdatei vermeiden möchten. Beachten Sie bitte, dass Sie Visual Studio neu starten müssen, damit die Umgebungswerte in der Anwendung verfügbar sind.

1.  Der Code, der das MongoDB-Repository implementiert, befindet sich in **models/mongodb.py**.

1.  Drücken Sie `F5`, um die Anwendung auszuführen. Umfragen, die mit **Beispielumfragen erstellen** erstellt wurden, und die durch die Abstimmung erfassten Daten werden in MongoDB serialisiert.

1.  Navigieren Sie zur Seite **Info**, und überprüfen Sie, ob die App das **MongoDB**-Repository verwendet.

  	![Webbrowser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskMongoDBAbout.png)

## Untersuchen der MongoDB-Datenbank

Sie können eine App wie [RoboMongo] verwenden, um Abfragen auszuführen und Änderungen an einer MongoDB-Datenbank vorzunehmen. In diesem Abschnitt verwenden wir RoboMongo für die Anzeige der Inhalte aus der Datenbank der Umfrage-App.

1.  Erstellen Sie eine neue Verbindung. Sie benötigen den **MONGOLAB_URI**, den wir im vorherigen Abschnitt abgerufen haben.

    Beachten Sie das Format des URI: `mongodb://<name>:<password>@<address>:<port>/<name>`.

    Der Name entspricht dem Namen, den Sie beim Erstellen des Diensts in Azure eingegeben haben. Er wird sowohl für den Datenbanknamen als auch den Benutzernamen verwendet.

1.  Legen Sie auf der Seite "Verbindung" für **Name** einen beliebigen Namen fest, den Sie für die Verbindung verwenden möchten. Geben Sie außerdem in den Feldern **Adresse** und **Port** die Werte von *address* und *port* aus **MONGOLAB_URI** ein.

  	![Dialogfeld mit Verbindungseinstellungen](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Geben Sie auf der Seite "Authentifizierung" die **Datenbank** und den **Benutzernamen** von *name* aus **MONGOLAB_URI** ein. Geben Sie schließlich bei **Kennwort** den Wert von *password* aus **MONGOLAB_URI** ein.

  	![Dialogfeld mit Verbindungseinstellungen](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Speichern Sie und stellen Sie die Verbindung zur Datenbank her. Sie können jetzt die Sammlung der Umfragen abfragen.

  	![Ergebnisse der RoboMongo-Abfrage](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonRobomongoQuery.png)

## Veröffentlichen der Web-App in Azure App Service

PTVS bietet eine einfache Möglichkeit zum Bereitstellen Ihrer Web-App in Azure App Service.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

1.  Klicken Sie auf **Microsoft Azure-Web-Apps**.

3. Wenn Sie sich nicht bei Azure angemeldet haben, klicken Sie auf **Anmelden**, und verwenden Sie zum Anmelden das Microsoft-Konto für Ihr Azure-Abonnement.

2.  Klicken Sie auf **Neu**, um eine neue Web-App zu erstellen.

1.  Füllen Sie die folgenden Felder aus, und klicken Sie auf **Erstellen**.
	-	**Name der Web-App**
	-	**App Service-Plan**
	-	**Ressourcengruppe**
	-	**Region**
	-	Lassen Sie für **Datenbankserver** die Option **Keine Datenbank** festlegt.

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonCreateWebSite.png) -->

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch mit der veröffentlichten Web-App. Wenn Sie zur Seite "Info" navigieren , sehen Sie, dass **In-Memory** als Repository verwendet wird und nicht **MongoDB**.

    Das liegt daran, dass die Umgebungsvariablen nicht für die Web-Apps-Instanz in Azure App Service festgelegt wurden. Daher werden die in **settings.py** festgelegten Standardwerte verwendet.

## Konfigurieren der Web-Apps-Instanz

In diesem Abschnitt konfigurieren wir die Umgebungsvariablen für die Web-Apps-Instanz.

1.  Klicken Sie im [Azure-Verwaltungsportal] auf die im vorherigen Abschnitt erstellte Web-App.

1.  Klicken Sie im Menü oben auf **KONFIGURIEREN**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteTopMenu.png) -->

1.  Navigieren Sie nach unten zum Abschnitt **App-Einstellungen**, und legen Sie die Werte für **REPOSITORY_NAME**, **MONGODB_HOST** und **MONGODB_DATABASE** wie im obigen Abschnitt beschrieben fest.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png) -->

1.  Klicken Sie im Menü unten auf **SPEICHERN**, auf **NEU STARTEN** und dann auf **DURCHSUCHEN**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-flask-mongodb/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  Jetzt sollte die App wie erwartet arbeiten und dabei das **MongoDB**-Repository verwenden.

    Glückwunsch!

  	![Webbrowser](./media/web-sites-python-ptvs-flask-mongodb/PollsFlaskAzureBrowser.png)

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Flask und MongoDB erfahren möchten.

- [Python Tools für Visual Studio – Dokumentation]
  - [Webprojekte]
  - [Cloud Service-Projekte]
  - [Remotedebugging in Microsoft Azure]
- [Dokumentation zu Flask]
- [MongoDB]
- [Dokumentation zu PyMongo]
- [PyMongo]

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md

<!--External Link references-->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python-Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure-SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure-SDK-Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio – Dokumentation]: http://pytools.codeplex.com/documentation
[Dokumentation zu Flask]: http://flask.pocoo.org/
[MongoDB]: http://www.mongodb.org/
[Dokumentation zu PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Remotedebugging in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud Service-Projekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
 

<!---HONumber=July15_HO3-->