<properties linkid="web-sites-python-ptvs-flask-table-storage" title="Flask and Azure Table Storage on Azure with Python Tools 2.1 for Visual Studio" pageTitle="Flask und Azure-Tabellenspeicher unter Azure mit Python Tools 2.1 für Visual Studio" description="Erfahren Sie, wie Sie die Python-Tools für Visual Studio verwenden, um eine Flask-Anwendung zu erstellen, die Daten im Azure-Tabellenspeicher speichert und an eine Website bereitgestellt werden kann." metaKeywords="" services="" solutions="" documentationCenter="Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="10/10/2014" ms.author="huvalo" />




# Flask und Azure-Tabellenspeicher unter Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen Sie eine einfache Umfrageanwendung mithilfe einer der PTVS-Beispielvorlagen.

Die Umfrageanwendung definiert eine Abstraktion für das Repository, damit Sie problemlos zwischen verschiedenen Typen von Repositorys (InMemory, Azure-Tabellenspeicher, MongoDB) wechseln können.

Sie erfahren, wie Sie ein Azure-Speicherkonto erstellen, die Anwendung für die Verwendung von Azure-Tabellenspeicher konfigurieren und die Anwendung auf einer Azure-Website veröffentlichen.

Im [Python Developer Center][] finden Sie weitere Artikel, in denen die Entwicklung von Azure-Websites mit PTVS mithilfe von Bottle-, Flask- und Django-Web-Frameworks, mit MongoDB, Azure-Tabellenspeicher, MySQL und SQL-Datenbankdiensten behandelt wird.  Dieser Artikel befasst sich zwar mit Azure-Websites, die Schritte ähneln jedoch der Entwicklung von [Azure-Cloud-Diensten][].

+ [Voraussetzungen](#prerequisites)
+ [Erstellen des Projekts](#create-the-project)
+ [Erstellen eines Azure-Speicherkontos](#create-an-azure-storage-account)
+ [Konfigurieren des Projekts](#configure-the-project)
+ [Untersuchen des Azure-Tabellenspeichers](#explore-the-azure-table-storage)
+ [Veröffentlichen in einer Azure-Website](#publish-to-an-azure-website)
+ [Konfigurieren der Azure-Website](#configure-the-azure-website)
+ [Nächste Schritte](#next-steps)

##<a name="prerequisites"></a>Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2,1 für Visual Studio][]
 - [Python Tools 2.1 für Visual Studio - Beispiel-VSIX][]
 - [Azure SDK-Tools für VS 2013][] oder [Azure SDK-Tools für VS 2012][]
 - [Python 2.7 32-Bit][] oder [Python 3.4 32-Bit][]

[WACOM.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

##<a name="create-the-project"></a>Erstellen des Projekts

In diesem Abschnitt erstellen Sie ein Visual Studio-Projekt anhand einer Beispielvorlage.  Sie erstellen eine virtuelle Umgebung und installieren erforderliche Pakete.  Dann führen Sie die Anwendung lokal mithilfe des standardmäßigen InMemory-Repository aus.

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt** aus.

1.  Die Projektvorlagen aus dem PTVS-Beispiel-VSIX sind unter **Python**, **Beispiele** verfügbar.  Wählen Sie **Umfragen-Flask-Webprojekt** aus, und klicken Sie auf "OK", um das Projekt zu erstellen.

  	![New Project Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren.  Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![External Packages Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskExternalPackages.png)

1.  Wählen Sie **Python 2.7** oder **Python 3.4** als Basisinterpreten aus.

  	![Add Virtual Environment Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAddVirtualEnv.png)

1.  Vergewissern Sie sich, dass die Anwendung funktioniert, indem Sie <kbd>F5</kbd> drücken.  Standardmäßig verwendet die Anwendung ein InMemory-Repository, das keine Konfiguration erfordert.  Alle Daten gehen verloren, wenn der Webserver beendet wird.

1.  Klicken Sie auf **Beispielumfragen erstellen**, und klicken Sie dann auf eine Umfrage, und stimmen Sie ab.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskInMemoryBrowser.png)

##<a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos

Für Speicheroperationen benötigen Sie ein Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.

1.  Klicken Sie unten im Navigationsbereich auf**NEU**.

  	![New Button](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzurePlusNew.png)

1.  Klicken Sie auf **DATA SERVICES**, dann auf **SPEICHER** und anschließend auf **SCHNELLERFASSUNG**.

  	![Quick Create](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureStorageCreate.png)

1.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für das Speicherkonto verwendet werden soll.  Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Wert wird zum Hostnamen im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

1.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Speicher befinden soll. Wenn Sie Speicher aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung bereitstellen.

1.  Optional können Sie die Georeplikation aktivieren.  Bei der Georeplikation werden Ihre Daten im Azure-Speicher dauerhaft an zwei Orten gespeichert. An beiden Standorten unterhält Azure-Speicher ständig mehrere fehlerfreie Replikate Ihrer Daten.

1.  Klicken Sie auf **SPEICHERKONTO ERSTELLEN**.

##<a name="configure-the-project"></a>Konfigurieren des Projekts

In diesem Abschnitt konfigurieren Sie die Anwendung für die Verwendung des soeben erstellten Speicherkontos.  Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden.  Dann führen Sie die Anwendung lokal aus.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf das Speicherkonto, das im vorherigen Abschnitt erstellt wurde.

1.  Klicken Sie auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.

  	![Manage Access Keys Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  Klicken Sie in Visual Studio mit der rechten Maustaste im Projektmappen-Explorer auf Ihren Projektknoten, und wählen Sie **Eigenschaften** aus.  Klicken Sie auf die Registerkarte **Debuggen**.

  	![Project Debug Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageProjectDebugSettings.png)

1.  Legen Sie die Werte der für die Anwendung erforderlichen Umgebungsvariablen in **Serverbefehl debuggen**, **Umgebung** fest.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<Name des Speicherkontos>
        STORAGE_KEY = <primärer Zugriffsschlüssel>

Dadurch werden die Umgebungsvariablen beim **Starten des Debuggens** festgelegt.  Wenn die Variablen beim **Starten ohne Debuggen** festgelegt werden sollen, legen Sie die gleichen Werte auch unter **Serverbefehl ausführen** fest.

Alternativ können Sie Umgebungsvariablen mithilfe der Windows-Systemsteuerung definieren.  Dies ist eine bessere Option, wenn Sie keine Anmeldeinformationen im Quellcode/in der Projektdatei speichern möchten.  Beachten Sie, dass Sie Visual Studio neu starten müssen, damit die neuen Umgebungswerte für die Anwendung verfügbar sind.

1.  Der Code zum Implementieren des Azure-Tabellenspeicher-Repositorys befindet sich in **models/azuretablestorage.py**.  Weitere Informationen zur Verwendung des Tabellendiensts aus Python finden Sie in der [Dokumentation].

1.  Führen Sie die Anwendung mit <kbd>F5</kbd> aus.  Umfragen, die über **Beispielumfragen erstellen** erstellt werden und die durch Abstimmung übermittelten Daten werden in Azure-Tabellenspeicher serialisiert.

1.  Navigieren Sie zur Seite **Info**, um zu überprüfen, ob die Anwendung das Repository des **Azure-Tabellenspeichers** verwendet.

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureTableStorageAbout.png)

##<a name="explore-the-azure-table-storage"></a>Untersuchen des Azure-Tabellenspeichers

Mit Server-Explorer in Visual Studio lassen sich Speichertabellen leicht anzeigen und bearbeiten.  In diesem Abschnitt verwenden Sie Server-Explorer zum Anzeigen des Inhalts der Umfragen-Anwendungsdatenbank.

> [WACOM.NOTE]Hierfür müssen Microsoft Azure Tools installiert werden, die als Teil des [Azure SDKs für .NET][] verfügbar sind.

1.  Öffnen Sie **Server-Explorer**.  Erweitern Sie **Azure**, **Speicher**, Ihr Speicherkonto und dann **Tabellen**.

  	![Server Explorer](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorer.png)

1.  Doppelklicken Sie auf die Tabelle**Umfragen** **Auswahl**, um den Inhalt der Tabelle in einem Dokumentfenster sowie die Entitäten für Hinzufügen/Entfernen/Bearbeiten anzuzeigen.

  	![Table Query Results](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonServerExplorerTable.png)

##<a name="publish-to-an-azure-website"></a>Veröffentlichen in einer Azure-Website

PTVS bietet eine einfache Möglichkeit, Ihre Webanwendung auf einer Azure-Website bereitzustellen.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen** aus.

  	![Publish Web Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Websites**.

1.  Klicken Sie auf **Neu**, um eine neue Website zu erstellen.

1.  Wählen Sie einen **Websitenamen** und eine **Region** aus, und klicken Sie auf **Erstellen**.

  	![Create Site on Microsoft Azure Dialog](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonCreateWebSite.png)

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Die veröffentlichte Website wird automatisch im Webbrowser geöffnet.  Wenn Sie die Info-Seite öffnen, sehen Sie, dass es sich um ein **InMemory**-Repository handelt, nicht um das **Azure-Tabellenspeicher**-Repository.

Das liegt daran, dass die Umgebungsvariablen nicht auf der Azure-Website festgelegt wurden, daher werden die in **settings.py** angegebenen Standardwerte verwendet.

##<a name="configure-the-azure-website"></a>Konfigurieren der Azure-Website

In diesem Abschnitt werden wir die Umgebungsvariablen für die Site konfigurieren.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf die Site, die im vorherigen Abschnitt erstellt wurde.

1.  Klicken Sie im oberen Menü auf **KONFIGURIEREN**.

  	![Top Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Scrollen Sie nach unten zum Abschnitt **App-Einstellungen**, und legen Sie die Werte für **REPOSITORY\_NAME**, **STORAGE\_NAME** und **STORAGE\_KEY** fest, wie im Abschnitt oben beschrieben.

  	![App Settings](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. Klicken Sie im unteren Menü auf **SPEICHERN**, auf **NEUSTART** und zuletzt auf **DURCHSUCHEN**.

  	![Bottom Menu](./media/web-sites-python-ptvs-flask-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Die Anwendung sollte wie erwartet funktionieren und das **Azure-Tabellenspeicher**-Repository verwenden.

    Glückwunsch!

  	![Web Browser](./media/web-sites-python-ptvs-flask-table-storage/PollsFlaskAzureBrowser.png)

##<a name="next-steps"></a>Nächste Schritte

Unter den folgenden Links finden Sie weitere Informationen zu Python-Tools für Visual Studio, Flask und Azure-Tabellenspeicher.

- [Python Tools für Visual Studio - Dokumentation][]
  - [Webprojekte][]
  - [Cloud-Dienstprojekte][]
  - [Remotedebugging auf Microsoft Azure][]
- [Flask-Dokumentation][]
- [Azure Storage][]
- [Azure SDK für Python][]
- [Verwenden des Tabellenspeicherdiensts aus Python][]


<!--Link references-->
[Python Developer Center]: /en-us/develop/python/
[Azure Cloud Services]: ../cloud-services-python-ptvs/
[Dokumentation]: ../storage-python-how-to-use-table-storage/
[Verwenden des Tabellenspeicherdiensts aus Python]: ../storage-python-how-to-use-table-storage/

<!--External Link references-->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[Azure SDK für .NET]: http://azure.microsoft.com/en-us/downloads/
[Python Tools 2,1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio - Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK-Tools für Visual Studio 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK-Tools für Visual Studio 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio - Dokumentation]: http://pytools.codeplex.com/documentation
[Flask-Dokumentation]: http://flask.pocoo.org/
[Remotedebugging auf Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud-Dienstprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure Storage]: http://azure.microsoft.com/en-us/documentation/services/storage/
[Azure SDK für Python]: https://github.com/Azure/azure-sdk-for-python
