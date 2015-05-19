<properties 
	pageTitle="Bottle und Azure Table Storage in Azure mit Python Tools 2.1 für Visual Studio" 
	description="Erfahren Sie, wie Sie die Python Tools für Visual Studio verwenden, um eine Bottle-Anwendung zu erstellen, die Daten in Azure Table Storage speichert und auf einer Website bereitgestellt werden kann."
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




# Bottle und Azure Table Storage in Azure mit Python Tools 2.1 für Visual Studio 

In diesem Lernprogramm erstellen wir mit [Python Tools für Visual Studio][] eine einfache Anwendung für Umfragen mithilfe einer der PTVS-Beispielvorlagen. Dieses Lernprogramm steht auch als [Video](https://www.youtube.com/watch?v=GJXDGaEPy94) zur Verfügung.

Die Umfrage-App definiert für ihr Repository eine Abstraktion. Sie können also einfach zwischen unterschiedlichen Repositorytypen wechseln (Im Arbeitsspeicher, Azure Table Storage, MongoDB).

Wir sehen uns an, wie man ein Azure-Speicherkonto anlegt, die App für die Nutzung von Azure Table Storage konfiguriert, und sie auf einer Azure Website veröffentlicht.

Weitere Artikel finden Sie im [Python Developer Center][], wo die Entwicklung von Azure Websites mit PTVS unter Einsatz der Webframeworks Bottle, Flask und Django mithilfe der Datenbankdienste MongoDB, Azure Table Storage, MySQL und SQL behandelt wird.  Zwar dreht sich dieser Artikel um Azure-Websites, doch die Schritte sind vergleichbar mit der Entwicklung von [Azure Cloud Services][].

## Voraussetzungen

 - Visual Studio 2012 oder 2013
 - [Python Tools 2.1 für Visual Studio][]
 - [Python Tools 2.1 für Visual Studio, Beispiel-VSIX][]
 - [Azure SDK Tools für VS 2013][] oder [Azure SDK Tools für VS 2012][]
 - [Python 2.7 (32 Bit)][] oder [Python 3.4 (32 Bit)][]

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

## Erstellen des Projekts

In diesem Abschnitt erstellen wir ein Visual Studio-Projekt mithilfe einer Beispielvorlage.  Wir erstellen eine virtuelle Umgebung und installieren die erforderlichen Pakete.  Dann führen wir die App lokal aus und verwenden dabei das Standard-Repository im Arbeitsspeicher.

1.  Wählen Sie in Visual Studio **Datei**, **Neues Projekt**.

1.  Die Projektvorlagen aus dem PTVS-Beispiel-VSIX stehen unter **Python**, **Beispiele** bereit.  Wählen Sie das**Polls Bottle-Webprojekt**, und klicken Sie auf "OK", sodass das Projekt erstellt wird.

  	![Dialogfeld "Neues Projekt"](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleNewProject.png)

1.  Sie werden aufgefordert, externe Pakete zu installieren.  Wählen Sie **In einer virtuellen Umgebung installieren** aus.

  	![Dialogfeld für externe Pakete](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleExternalPackages.png)

1.  Wählen Sie**Python 2.7** oder **Python 3.4** als Basisinterpreter.

  	![Dialogfeld für das Hinzufügen der virtuellen Umgebung](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAddVirtualEnv.png)

1.  Vergewissern Sie sich, dass die Anwendung funktioniert, indem Sie <kbd>F5</kbd> drücken.  Standardmäßig verwendet die Anwendung ein Repository im Arbeitsspeicher, das nicht konfiguriert werden muss.  Wird der Server angehalten, gehen alle Daten verloren.

1.  Klicken Sie auf **Beispielumfrage erstellen**, und klicken Sie dann auf eine Umfrage und Abstimmung.

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleInMemoryBrowser.png)

## Erstellen eines Azure-Speicherkontos

Für Speicheroperationen benötigen Sie ein Azure-Speicherkonto. Mit den folgenden Schritten können Sie ein Speicherkonto erstellen.

1.  Melden Sie sich am [Azure-Verwaltungsportal][] an.

1.  Klicken Sie unten im Navigationsbereich auf **NEU**.

  	![Schaltfläche "Neu"](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzurePlusNew.png)

1.  Klicken Sie auf **DATA SERVICES**, dann auf **SPEICHER** und anschließend auf **SCHNELLERFASSUNG**.

  	![Schnellerfassung](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureStorageCreate.png)

1.  Geben Sie im Feld "URL" einen Unterdomänennamen ein, der im URI für das Speicherkonto verwendet werden soll.  Der Eintrag kann drei bis 24 Kleinbuchstaben und Zahlen enthalten. Dieser Wert wird der Hostname im URI, der zum Adressieren von Blob-, Warteschlangen- oder Tabellenspeicherressourcen für das Abonnement verwendet wird.

1.  Wählen Sie eine Region/Affinitätsgruppe, in der sich der Speicher befinden soll. Wenn Sie Speicher aus Ihrer Azure-Anwendung verwenden, wählen Sie die Region aus, in der Sie auch Ihre Anwendung bereitstellen.

1.  Optional können Sie die Georeplikation aktivieren.  Mit Georeplikation speichert Azure-Speicher Ihre Daten jetzt beständig an zwei Standorten. An beiden Orten unterhält Azure-Speicher konstant mehrere intakte Replikationen Ihrer Daten.

1.  Klicken Sie auf **SPEICHERKONTO ERSTELLEN**.

## Konfigurieren des Projekts

In diesem Abschnitt konfigurieren wir unsere Anwendung, sodass sie das eben erstellte Speicherkonto verwendet.  Sie sehen, wie Verbindungseinstellungen vom Azure-Portal abgerufen werden.  Anschließend führen wir die Anwendung lokal aus.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf das im vorherigen Abschnitt erstellte Speicherkonto.

1.  Klicken Sie auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.

  	![Dialogfeld "Zugriffsschlüssel verwalten"](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonAzureTableStorageManageKeys.png)

1.  Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Eigenschaften**.  Klicken Sie auf die Registerkarte **Debuggen**.

  	![Debugeinstellungen für das Projekt](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageProjectDebugSettings.png)

1.  Legen Sie die Werte der von der App benötigten Umgebungsvariablen in **Serverbefehl debuggen**, **Umgebung** fest.

        REPOSITORY_NAME=azuretablestorage
        STORAGE_NAME=<storage account name>
        STORAGE_KEY=<primary access key>

    Damit werden die Umgebungsvariablen festgelegt, wenn Sie das **Debugging starten**.  Sollen die Variablen dann festgelegt werden, wenn Sie **Ohne Debugging starten**, legen Sie dieselben Werte auch unter **Serverbefehl ausführen** fest.

    Alternativ können Sie Umgebungsvariablen mithilfe der Windows Systemsteuerung definieren.  Das ist eine bessere Option wenn Sie das Speichern von Anmeldeinformationen im Quellcode bzw. der Projektdatei vermeiden möchten.  Beachten Sie bitte, dass Sie Visual Studio neu starten müssen, damit die Umgebungswerte in der Anwendung verfügbar sind.

1.  Der Code, der das Azure Table Storage-Repository implementiert, befindet sich in **models/azuretablestorage.py**.  Weitere Informationen zur Nutzung von Table Service in Python finden Sie in der [Dokumentation].

1.  Führen Sie die App mit <kbd>F5</kbd> aus.  Umfragen, die mit **Beispielumfrage erstellen** erstellt wurden, und die durch die Abstimmung erfassten Daten werden in Azure Table Storage serialisiert.

1.  Navigieren Sie zur Seite **Info**, und prüfen Sie, ob die App das **Azure Table Storage**-Repository verwendet.

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureTableStorageAbout.png)

## Untersuchen von Azure Table Storage

Das Anzeigen und Bearbeiten von Speichertabellen in Visual Studio ist mit Server-Explorer ganz einfach.  In diesem Abschnitt verwenden wir Server-Explorer für die Anzeige der Inhalte aus den Tabellen der Umfrage-App.

> [AZURE.NOTE] Dafür müssen die Microsoft Azure-Tools installiert sein, die als Teil des[Azure SDK für .NET][] erhältlich sind.

1.  Öffnen Sie **Server-Explorer**.  Erweitern Sie **Azure**, **Speicher**, Ihr Speicherkonto und dann **Tabellen**.

  	![Server-Explorer](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorer.png)

1.  Doppelklicken Sie auf die Tabelle **polls** oder **choices**, sodass Sie den Inhalt der Tabelle in einem Dokumentenfenster sehen und Entitäten hinzufügen, entfernen oder bearbeiten können.

  	![Ergebnisse der Tabellenabfrage](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonServerExplorerTable.png)

## Veröffentlichen in einer Azure-Website

PTVS bietet eine einfache Möglichkeit, wie Sie Ihre Webanwendung auf einer Azure Websites bereitstellen können.

1.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektknoten, und wählen Sie **Veröffentlichen**.

  	![Dialogfeld "Web veröffentlichen"](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonPublishWebSiteDialog.png)

1.  Klicken Sie auf **Microsoft Azure-Websites**.

1.  Klicken Sie auf **Neu**, sodass eine neue Website erstellt wird.

1.  Legen Sie einen **Websitenamen** sowie eine **Region** fest, und klicken Sie auf **Erstellen**.

  	![Dialogfeld zum Erstellen einer Website auf Microsoft Azure](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonCreateWebSite.png)

1.  Übernehmen Sie alle anderen Standardwerte, und klicken Sie auf **Veröffentlichen**.

1.  Ihr Webbrowser öffnet automatisch die veröffentlichte Website.  Wenn Sie zur Seite "Info" navigieren , sehen Sie, dass **In-Memory** als Repository verwendet wird und nicht **Azure Table Storage**.

    Das liegt daran, dass die Umgebungsvariablen nicht für die Azure-Website festgelegt wurden. Sie verwendet daher die in **settings.py** festgelegten Standardwerte.

## Konfigurieren der Azure Website

In diesem Abschnitt konfigurieren wir die Umgebungsvariablen für die Website.

1.  Klicken Sie im [Azure-Verwaltungsportal][] auf die im vorherigen Abschnitt erstellte Website.

1.  Klicken Sie im Menü oben auf **KONFIGURIEREN**.

  	![Hauptmenü](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteTopMenu.png)

1.  Navigieren Sie nach unten zum Abschnitt **App-Einstellungen**, und legen Sie die Werte für **REPOSITORY\_NAME**, **STORAGE\_NAME** und **MSTORAGE\_KEY** wie im obigen Abschnitt beschrieben fest.

  	![App-Einstellungen](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureSettingsTableStorage.png)

1. Klicken Sie im Menü unten auf **SPEICHERN**, dann auf **NEU STARTEN** und schließlich auf **DURCHSUCHEN**.

  	![Menü am unteren Rand](./media/web-sites-python-ptvs-bottle-table-storage/PollsCommonWebSiteConfigureBottomMenu.png)

1.  Jetzt sollte die App wie erwartet arbeiten und dabei das **Azure Table Storage**-Repository verwenden.

    Glückwunsch!

  	![Webbrowser](./media/web-sites-python-ptvs-bottle-table-storage/PollsBottleAzureBrowser.png)

## Nächste Schritte

Folgen Sie diesen Links, wenn Sie mehr über Python Tools für Visual Studio, Bottle und Azure Table Storage erfahren möchten.

- [Python Tools für Visual Studio - Dokumentation][]
  - [Webprojekte][]
  - [Cloud-Dienstprojekte][]
  - [Remotedebugging in Microsoft Azure][]
- [Dokumentation zu Bottle][]
- [Azure-Speicher][]
- [Azure SDK für Python][]
- [Verwenden des Tabellenspeicherdiensts in Python][]


<!--Link references-->
[Python Developer Center]: /develop/python/
[Azure Cloud Services]: cloud-services-python-ptvs.md
[Dokumentation]: storage-python-how-to-use-table-storage.md
[Verwenden des Tabellenspeicherdiensts in Python]: storage-python-how-to-use-table-storage.md

<!--External Link references-->
[Azure-Verwaltungsportal]: https://manage.windowsazure.com
[Azure SDK für .NET]: http://azure.microsoft.com/downloads/
[Python Tools für Visual Studio]: http://aka.ms/ptvs
[Python Tools 2.1 für Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=517189
[Python Tools 2.1 für Visual Studio, Beispiel-VSIX]: http://go.microsoft.com/fwlink/?LinkId=517189
[Azure SDK Tools für VS 2013]: http://go.microsoft.com/fwlink/?LinkId=323510
[Azure SDK Tools für VS 2012]: http://go.microsoft.com/fwlink/?LinkId=323511
[Python 2.7 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517190 
[Python 3.4 (32 Bit)]: http://go.microsoft.com/fwlink/?LinkId=517191
[Python Tools für Visual Studio - Dokumentation]: http://pytools.codeplex.com/documentation
[Dokumentation zu Bottle]: http://bottlepy.org/docs/dev/index.html
[Remotedebugging in Microsoft Azure]: http://pytools.codeplex.com/wikipage?title=Features%20Azure%20Remote%20Debugging
[Webprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Web%20Project
[Cloud-Dienstprojekte]: http://pytools.codeplex.com/wikipage?title=Features%20Cloud%20Project
[Azure-Speicher]: http://azure.microsoft.com/documentation/services/storage/
[Azure SDK für Python]: https://github.com/Azure/azure-sdk-for-python

<!--HONumber=52-->