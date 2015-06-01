<properties 
	pageTitle="Bottle und MongoDB in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Erfahren Sie, wie Sie die Python Tools für Visual Studio verwenden, um eine Bottle-Anwendung zu erstellen, die Daten in einer MongoDB-Datenbankinstanz speichert und auf einer Website bereitgestellt werden kann." 
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
	ms.date="02/09/2015" 
	ms.author="huguesv"/>




# Bottle und MongoDB in Azure mit Python Tools 2.1 für Visual Studio

In diesem Lernprogramm erstellen wir mit [Python Tools für Visual Studio][] eine einfache Anwendung für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=8hQMyf8p_Jo) zur Verfügung.

Die Umfrage-App definiert für ihr Repository eine Abstraktion. Sie können also einfach zwischen unterschiedlichen Repositorytypen wechseln (Im Arbeitsspeicher, Azure Table Storage, MongoDB).

Wir sehen uns an, wie man einen der gehosteten MongoDB-Dienste auf Azure verwendet, die App für die Nutzung von MongoDB konfiguriert, und wie man sie auf einer Azure Website veröffentlicht.

Weitere Artikel finden Sie im [Python Developer Center][], wo die Entwicklung von Azure Websites mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure Table Storage, MySQL und SQL behandelt wird.  Zwar dreht sich dieser Artikel um Azure-Websites, doch die Schritte sind vergleichbar mit der Entwicklung von [Azure Cloud Services][].

## Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio][]
 - [Python Tools 2.1 für Visual Studio, Beispiel-VSIX][]
 - [Azure SDK Tools für VS 2013][] oder [Azure SDK Tools für VS 2012][]
 - [Python 2.7 (32 Bit)][] oder [Python 3.4 (32 Bit)][]
 - [RoboMongo][] (optional)

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage.  Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete.  Dann führen wir die App lokal aus und verwenden dabei das Standard-Repository im Arbeitsspeicher.

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt**. 

1.  Die Projektvorlagen aus dem PTVS-Beispiel-VSIX stehen unter **Python**, **Beispiele** bereit.  Wählen Sie das**Polls Bottle-Webprojekt**, und klicken Sie auf "OK", sodass das Projekt erstellt wird.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren.  Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleExternalPackages.png)

1.  Wählen Sie**Python 2.7** oder **Python 3.4** als Basisinterpreter.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAddVirtualEnv.png)

1.  Vergewissern Sie sich, dass die Anwendung funktioniert, indem Sie <kbd>F5</kbd> drücken.  Standardmäßig verwendet die Anwendung ein Repository im Arbeitsspeicher, das nicht konfiguriert werden muss.  Wird der Server angehalten, gehen alle Daten verloren.

1.  Klicken Sie auf **Beispielumfrage erstellen**, und klicken Sie dann auf eine Umfrage und Abstimmung.

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleInMemoryBrowser.png)

## Erstellen einer MongoDB-Datenbank

Für die Datenbank erstellen wird eine in MongoLab gehostete Datenbank auf Azure.

Alternativ können Sie einen eigenen virtuellen Computer erstellen, der auf Azure läuft und dort MongoDB selbst installieren und verwalten.

Mit den folgenden Schritten können Sie einen kostenlosen Test bei MongoLab erstellen.

1.  Melden Sie sich am [Azure-Verwaltungsportal][] an.

1.  Klicken Sie unten im Navigationsbereich auf **NEU**.

  	![Schaltfläche "Neu"](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonAzurePlusNew.png)

1.  Klicken Sie auf**STORE**, dann auf**MongoLab**.

  	![Dialogfeld zur Add-On-Auswahl](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon1.png)

1.  Geben Sie im Feld "Name" einen Namen für den Datenbankdienst ein.

1.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Datenbankdienst befinden soll. Wenn Sie die Datenbank aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen.

  	![Dialogfeld zur Add-On-Anpassung](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabAddon2.png)

1.  Klicken Sie auf **KAUFEN**.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Anwendung, sodass Sie die eben erstellte MongoDB-Datenbank verwendet.  Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden.  Anschließend führen wir die Anwendung lokal aus.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf **ADD-ONS** und dann auf den im Vorfeld erstellten MongoLab-Dienst.

1.  Klicken Sie auf **VERBINDUNGSINFO**.  Sie können die Schaltfläche "Kopieren" verwenden, um den Wert von **MONGOLAB_URI** in der Zwischenablage abzulegen.

  	![Dialogfeld mit Verbindungsinformationen](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonMongoLabConnectionInfo.png)

1.  Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Eigenschaften**.  Klicken Sie auf die Registerkarte **Debuggen**.

  	![Debugeinstellungen für das Projekt](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBProjectDebugSettings.png)

1.  Legen Sie die Werte der von der App benötigten Umgebungsvariablen in **Serverbefehl debuggen**, **Umgebung** fest.

        REPOSITORY_NAME=mongodb
        MONGODB_HOST=<value of MONGOLAB_URI>
        MONGODB_DATABASE=<database name>

    Damit werden die Umgebungsvariablen festgelegt, wenn Sie das **Debugging starten**.  Sollen die Variablen dann festgelegt werden, wenn Sie **Ohne Debugging starten**, legen Sie dieselben Werte auch unter **Serverbefehl ausführen** fest.

    Alternativ können Sie Umgebungsvariablen mithilfe der Windows Systemsteuerung definieren.  Das ist eine bessere Option wenn Sie das Speichern von Anmeldeinformationen im Quellcode bzw. der Projektdatei vermeiden möchten.  Beachten Sie bitte, dass Sie Visual Studio neu starten müssen, damit die Umgebungswerte in der Anwendung verfügbar sind.

1.  Der Code, der das MongoDB-Repository implementiert, befindet sich in **models/mongodb.py**.

1.  Führen Sie die App mit <kbd>F5</kbd> aus.  Umfragen, die mit **Beispielumfrage erstellen** erstellt wurden, und die durch die Abstimmung erfassten Daten werden in MongoDB serialisiert.

1.  Navigieren Sie zur Seite **Info**, und prüfen Sie, ob die App das **MongoDB**-Repository verwendet.

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleMongoDBAbout.png)

## Untersuchen der MongoDB-Datenbank

Sie können eine Anwendung wie [RoboMongo][] verwenden, um Abfragen und Änderungen an einer MongoDB vorzunehmen.  In diesem Abschnitt verwenden wir RoboMongo für die Anzeige der Inhalte aus der Datenbank der Umfrage-App.

1.  Erstellen Sie eine neue Verbindung.  Sie benötigen den **MONGOLAB_URI**, den wir im vorherigen Abschnitt abgerufen haben.

    Beachten Sie das Format des URI: `mongodb://<name>:<password>@<address>:<port>/<name>`

    Der Name entspricht dem Namen, den Sie beim Erstellen des Diensts in Azure eingegeben haben.  Er wird sowohl für den Datenbanknamen als auch den Benutzernamen verwendet.

1.  Legen Sie auf der Seite "Verbindung" **Name** auf einen beliebigen Namen fest, den Sie für die Verbindung verwenden möchten.  Legen Sie auch die Felder **Adresse** und **Port** auf  *address* und  *port* von **MONGOLAB_URI** fest.

  	![Dialogfeld mit Verbindungseinstellungen](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection1.png)

1.  Legen Sie in der Seite "Authentifizierung" die Felder **Datenbank** und **Benutzername** auf  *name* von **MONGOLAB_URI** fest.  Legen Sie auch das **Kennwort** auf  *password* von **MONGOLAB_URI** fest.

  	![Dialogfeld mit Verbindungseinstellungen](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoCreateConnection2.png)

1.  Speichern Sie und stellen Sie die Verbindung zur Datenbank her.  Sie können jetzt die Sammlung der Umfragen abfragen.

  	![Ergebnisse der RoboMongo-Abfrage](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonRobomongoQuery.png)

## Veröffentlichen in einer Azure-Website

PTVS bietet eine einfache Möglichkeit, wie Sie Ihre Webanwendung auf einer Azure Websites bereitstellen können.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen**.

  	![Dialogfeld "Web veröffentlichen"](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Websites**.

1.  Klicken Sie auf **Neu**, sodass eine neue Website erstellt wird.

1.  Legen Sie einen **Websitenamen** sowie eine **Region** fest, und klicken Sie auf **Erstellen**.

  	![Dialogfeld zum Erstellen einer Website auf Microsoft Azure](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonCreateWebSite.png)

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch die veröffentlichte Website.  Wenn Sie zur Seite "Info" navigieren , sehen Sie, dass **In-Memory** als Repository verwendet wird und nicht **MongoDB**.

    Das liegt daran, dass die Umgebungsvariablen nicht für die Azure-Website festgelegt wurden. Sie verwendet daher die in **settings.py** festgelegten Standardwerte.

## Konfigurieren der Azure Website

In diesem Abschnitt konfigurieren wir die Umgebungsvariablen für die Website.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf die im vorherigen Abschnitt erstellte Website.

1.  Klicken Sie im Menü oben auf **KONFIGURIEREN**.

  	![Hauptmenü](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteTopMenu.png)

1.  Navigieren Sie nach unten zum Abschnitt **App-Einstellungen**, und legen Sie die Werte für **REPOSITORY_NAME**, **MONGODB_HOST** und **MONGODB_DATABASE** wie im obigen Abschnitt beschrieben fest.

  	![App-Einstellungen](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureSettingsMongoDB.png)

1.  Klicken Sie im Menü unten auf **SPEICHERN**, dann auf **NEU STARTEN** und schließlich auf **DURCHSUCHEN**.

  	![Menü am unteren Rand](./media/web-sites-python-ptvs-bottle-mongodb/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Jetzt sollte die App wie erwartet arbeiten und dabei das **MongoDB**-Repository verwenden.

    Glückwunsch!

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-mongodb/PollsBottleAzureBrowser.png)

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Bottle und MongoDB erfahren möchten.

- [Python Tools für Visual Studio - Dokumentation][]
  - [Webprojekte][]
  - [Cloud-Dienstprojekte][]
  - [Remotedebugging in Microsoft Azure][]
- [Dokumentation zu Bottle][]
- [MongoDB][]
- [Dokumentation zu PyMongo][]
- [PyMongo][]


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: cloud-services-python-ptvs.md

<!--External Link references-->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[RoboMongo]: http://robomongo.org/
[Python Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio - Dokumentation]: http://pytools.codeplex.com/documentation
[Dokumentation zu Bottle]: http://bottlepy.org/docs/dev/index.html
[MongoDB]: http://www.mongodb.org/
[Dokumentation zu PyMongo]: http://api.mongodb.org/python/current/
[PyMongo]: https://github.com/mongodb/mongo-python-driver
[Remotedebugging in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud-Dienstprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project


<!--HONumber=52-->