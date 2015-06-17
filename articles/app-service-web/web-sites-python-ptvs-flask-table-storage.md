<properties 
	pageTitle="Flask und Azure Table Storage in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Enthält Informationen zum Verwenden der Python-Tools für Visual Studio eine Kolben Webanwendung erstellen, die Daten in Azure-Tabellenspeicher gespeichert und in Azure App Service-Webanwendungen bereitzustellen." 
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




# Flask und Azure Table Storage in Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen wir mit [Python-Tools für Visual Studio] eine einfache Web-App für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=qUtZWtPwbTk) zur Verfügung.

Die Umfrage-Web-App definiert für ihr Repository eine Abstraktion. Sie können also einfach zwischen unterschiedlichen Repository-Typen wechseln (In-Memory, Azure-Tabellenspeicher, MongoDB).

Wir lernen Azure Storage-Konto erstellen, konfigurieren Sie die Webanwendung, um Azure-Tabellenspeicher zu verwenden und die Webanwendung zu veröffentlichen [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714).

Weitere Artikel finden Sie im [Python Developer Center], wo die Entwicklung von Azure App Service-Web-Apps mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure-Tabellenspeicher, MySQL und SQL behandelt wird. Der Schwerpunkt dieses Artikels liegt zwar auf App Service, die Schritte sind jedoch vergleichbar mit der Entwicklung von [Azure Cloud Services].

## Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio]
 - [Python Tools 2.1 für Visual Studio, Beispiel-VSIX]
 - [Azure-SDK-Tools für VS 2013] oder [Azure-SDK-Tools für VS 2012]
 - [Python 2.7 32-Bit] oder [Python 3.4 32-Bit]

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage. Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete. Dann führen wir die App lokal aus und verwenden dabei das Standard-Repository im Arbeitsspeicher.

1.  Wählen Sie in Visual Studio **Datei** -> **Neues Projekt** aus.

1.  Die Projektvorlagen aus der PTVS-Beispiel-VSIX-Datei stehen unter **Python**, **Beispiele** bereit. Wählen Sie **Umfragen Kolben Webprojekt** und klicken Sie auf OK, um das Projekt zu erstellen.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren. Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Wählen Sie **Python 2.7** oder **Python 3.4** als Basisinterpreter.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Vergewissern Sie sich, dass die Anwendung durch Drücken von `F5`. Standardmäßig verwendet die Anwendung ein Repository im Arbeitsspeicher, das nicht konfiguriert werden muss. Wird der Server angehalten, gehen alle Daten verloren.

1.  Klicken Sie auf **Beispielumfragen erstellen**, und klicken Sie dann auf eine Umfrage und Abstimmung.

  	![Webbrowser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

## Erstellen eines Azure-Speicherkontos

Für Speichervorgänge benötigen Sie ein Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen.

1.  Melden Sie sich beim [Azure-Portal] an.

2. Klicken Sie unten links im Portal auf das Symbol **Neu** und dann auf **Daten + Speicher** > **Speicher**. Geben Sie dem Speicherkonto einen eindeutigen Namen, und erstellen Sie eine neue [Ressourcengruppe](../resource-group-overview.md) dafür.

  	<!-- ![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png) -->Wenn das Speicherkonto erstellt wurde, leuchtet auf der Schaltfläche **Benachrichtigungen** grün **ERFOLG**. Das Blatt des Speicherkontos wird geöffnet, und darauf wird angezeigt, dass es zu der neu erstellten Ressourcengruppe gehört.

5. Klicken Sie auf die **Einstellungen** teilweise in das Speicherkonto Blade. Notieren Sie sich den Kontonamen und den Primärschlüssel.

	Wir benötigen diese Informationen zum Konfigurieren des Projekts im nächsten Abschnitt.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Anwendung, sodass sie das eben erstellte Speicherkonto verwendet. Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden. Anschließend führen wir die Anwendung lokal aus.

1.  Klicken Sie in Visual Studio mit der rechten Maustaste auf den Projektknoten im Projektmappen-Explorer, und wählen Sie **Eigenschaften**. Klicken Sie auf die Registerkarte **Debuggen**.

  	![Debugeinstellungen für das Projekt](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Legen Sie die Werte der von der App benötigten Umgebungsvariablen in **Serverbefehl debuggen**, **Umgebung** fest.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Damit werden die Umgebungsvariablen festgelegt, wenn Sie das **Debugging starten**. Sollen die Variablen dann festgelegt werden, wenn Sie **ohne Debugging starten**, legen Sie dieselben Werte auch unter **Serverbefehl ausführen** fest.

    Alternativ können Sie Umgebungsvariablen mithilfe der Windows Systemsteuerung definieren. Das ist eine bessere Option wenn Sie das Speichern von Anmeldeinformationen im Quellcode bzw. der Projektdatei vermeiden möchten. Beachten Sie bitte, dass Sie Visual Studio neu starten müssen, damit die Umgebungswerte in der Anwendung verfügbar sind.

1.  Der Code zum Implementieren des Azure-Tabellenspeicher Repositorys befindet sich in **models/azuretablestorage.py**. Siehe die [Dokumentation] Weitere Informationen zur Verwendung von Python-Diensts.

1.  Führen Sie die Anwendung mit `F5`. Umfragen, die mit erstellt werden **Beispiel-Umfragen erstellen** und stimmen übermittelten Daten werden in Azure-Tabellenspeicher.

1.  Navigieren Sie zu den **zu** und vergewissern Sie sich, dass die Anwendung die **Azure Table Storage** Repository.

  	![Webbrowser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

## Untersuchen von Azure Table Storage

Das Anzeigen und Bearbeiten von Speichertabellen in Visual Studio ist mit Server-Explorer ganz einfach. In diesem Abschnitt verwenden wir Server-Explorer für die Anzeige der Inhalte aus den Tabellen der Umfrage-App.

> [AZURE.NOTE]Dies erfordert Microsoft Azure-Tools installiert werden, die als werden Teil der [Azure SDK für .NET].

1.  Open **Server-Explorer**. Erweitern Sie **Azure**, **Speicher**, Ihr Speicherkonto **Tabellen**.

  	<!-- ![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png) -->

1.  Doppelklicken Sie auf die **Umfragen** oder **Optionen** Tabelle, um den Inhalt der Tabelle in einem Dokumentfenster sowie hinzufügen/entfernen/Bearbeiten von Entitäten anzuzeigen.

  	<!-- ![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png) -->

## Veröffentlichen Sie die Webanwendung in Azure-App-Dienst

Azure .NET SDK bietet eine einfache Möglichkeit, Ihre Webanwendung auf Azure-App-Dienst bereitstellen.

1.  Klicken Sie auf **Microsoft Azure-Web-Apps**.

1.  Klicken Sie auf **Neu**, um eine neue Web-App zu erstellen.

1.  Füllen Sie die folgenden Felder aus, und klicken Sie auf **Erstellen**.
	-	**Name der Web-App**
	-	**App Service-Plan**
	-	**Ressourcengruppe**
	-	**Region**
	-	Lassen Sie für **Datenbankserver** die Option **Keine Datenbank** festlegt.

  	<!-- ![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png) -->

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch mit der veröffentlichten Web-App. Wenn Sie durchsuchen die Infoseite, sehen Sie, dass es verwendet die **im Arbeitsspeicher** Repository, nicht die **Azure Table Storage** Repository.

    Das liegt daran, dass die Umgebungsvariablen nicht für die Web-Apps-Instanz in Azure App Service festgelegt wurden. Daher werden die in **settings.py** festgelegten Standardwerte verwendet.

## Konfigurieren der Web-Apps-Instanz

In diesem Abschnitt konfigurieren wir die Umgebungsvariablen für die Web-Apps-Instanz.

1.  In [Azure Portal], die Web-app-Fenster öffnen, indem Sie auf **Durchsuchen** > **Webanwendungen** > den Namen der Web-app.

1.  Klicken Sie in Ihrer Web-app-Blade auf **alle Einstellungen**, klicken Sie dann auf **Anwendungseinstellungen**.

  	<!-- ![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png) -->

1.  Führen Sie einen Bildlauf nach unten, um die **app-Einstellungen** aus, und legen Sie die Werte für **Repositor**, **STORAGE_NAME** und **STORAGE_KEY** wie im obigen Abschnitt beschrieben.

  	<!-- ![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png) -->

1. Klicken Sie auf **Speichern**, dann **Neustart** und schließlich **Durchsuchen**.

  	<!-- ![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png) -->

1.  Die Webanwendung ordnungsgemäß funktioniert, verwenden, sollte der **Azure Table Storage** Repository.

    Glückwunsch!

  	![Webbrowser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

## Nächste Schritte

Unter den folgenden Links finden Sie weitere Informationen zu Python Tools für Visual Studio, Flask und Azure-Tabellenspeicher.

- [Python Tools für Visual Studio – Dokumentation]
  - [Webprojekte]
  - [Cloud Service-Projekte]
  - [Remotedebugging in Microsoft Azure]
- [Kolben-Dokumentation]
- [Azure Storage] (in englischer Sprache)
- [Azure SDK für Python]
- [Gewusst wie: Verwenden des Tabellenspeicherdiensts aus Python]

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Eine Anleitung für die Änderung des alten Portal für das neue Portal finden Sie unter: [Referenz für das Portal navigieren](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs.md
[Dokumentation]: ../storage-python-how-to-use-table-storage.md
[Gewusst wie: Verwenden des Tabellenspeicherdiensts aus Python]: ../storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure Management Portal]: https://portal.azure.com
[Azure SDK für .NET]: http://azure.microsoft.com/downloads/
[Python-Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure-SDK-Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure-SDK-Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517190
[Python 3.4 32-bit]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio – Dokumentation]: http://pytools.codeplex.com/documentation
[Kolben-Dokumentation]: http://flask.pocoo.org/
[Remotedebugging in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud Service-Projekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure Storage]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK für Python]: https://github.com/Azure/azure-sdk-for-python
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->