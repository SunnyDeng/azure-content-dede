<properties linkid="develop-mobile-tutorials-get-started-offline-data-ios" urlDisplayName="Getting Started with Offline Data" pageTitle="Get started with offline data in Mobile Services (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to cache and sync offline data in your Xamarin iOS application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with offline data in Mobile Services" authors="donnam,wesmc" editor="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam,wesmc"></tags>

# Erste Schritte mit der Synchronisierung von Offlinedaten in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-ios-get-started-offline-data" title="iOS">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/de-de/documentation/articles/mobile-services-xamarin-android-get-started-offline-data" title="Xamarin.Android">Xamarin.Android</a>
</div>

In diesem Thema erfahren Sie, wie Sie die Offlinefunktionen von Azure Mobile Services nutzen können. Diese Funktionen ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie den mobilen Dienst offline verwenden. Mit den Offlinefunktionen können Sie Ihre lokalen Änderungen mit dem mobilen Dienst synchronisieren, wenn Sie erneut online sind.

In diesem Lernprogramm aktualisieren Sie die App aus dem Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] so, dass diese die Offlinefunktionen von Azure Mobile Services unterstützt. Anschließend fügen Sie Daten in einem Offlineszenario mit getrennter Verbindung hinzu, synchronisieren diese Elemente mit der Onlinedatenbank und melden sich daraufhin beim Azure-Verwaltungsportal an, um Änderungen an den Daten anzuzeigen, die bei der Ausführung der Anwendung vorgenommen wurden.

> [WACOM.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie Azure Mobile Services verwenden können, um Daten in einer Windows Store-App zu speichern und abzurufen. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.
>
> Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Aktualisieren der App für die Unterstützung von Offlinefunktionen][Aktualisieren der App für die Unterstützung von Offlinefunktionen]
2.  [Testen der App mit Verbindung zum mobilen Dienst][Testen der App mit Verbindung zum mobilen Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   XCode 4.5 und iOS 6.0 (oder neuere Versionen)
-   Visual Studio mit der [Xamarin-Erweiterung][Xamarin-Erweiterung] **oder** [Xamarin Studio][Xamarin Studio] auf OS X
-   Abschluss des Lernprogramms [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten]
-   [Azure Mobile Services SDK Version 1.3.0-alpha3 (oder neuer)][Azure Mobile Services SDK Version 1.3.0-alpha3 (oder neuer)]
-   [Azure Mobile Services SQLite Store Version 1.0.0-alpha2 (oder neuer)][Azure Mobile Services SQLite Store Version 1.0.0-alpha2 (oder neuer)]

> [WACOM.NOTE] In der Anleitung wird davon ausgegangen, dass Sie Visual Studio 2012 oder höher mit der Xamarin-Erweiterung verwenden. Bei Verwendung von Xamarin Studio auf OS X sind die meisten Anweisungen gleich, Sie sollten jedoch zusätzlich das [NuGet-Add-In für Xamarin][NuGet-Add-In für Xamarin] installieren, damit Sie die Vorabversion der Mobile Services NuGet-Pakete zu Ihrem Projekt hinzufügen können.

## <a name="enable-offline-app"></a>Aktualisieren der App für die Unterstützung von Offlinefunktionen

Offlinefunktionen von Azure Mobile Services ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie den mobilen Dienst offline verwenden. Initialisieren Sie `MobileServiceClient.SyncContext` in einem lokalen Speicher, um diese Funktionen in der App zu verwenden. Erstellen Sie dann für die Tabelle einen Verweis über die Schnittstelle `IMobileServiceSyncTable`.

1.  Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abgeschlossen haben. Entfernen Sie im Projektmappen-Explorer den Verweis auf **Azure Mobile Services SDK** unter **Komponenten**.

2.  Installieren Sie die Vorabversion von Mobile Services SQLiteStore mithilfe des folgenden Befehls in der Paket-Manager-Konsole:

        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Damit werden auch alle erforderlichen Abhängigkeiten installiert.

3.  Entfernen Sie im Verweisknoten die Verweise auf `System.IO`, `System.Runtime` und `System.Threading.Tasks`.

### Bearbeiten der Datei QSTodoService.cs

Bearbeiten Sie die Klasse `QSTodoService`, um die Offlinefunktionen von Mobile Services mit einem lokalen SQLite-Speicher nutzen zu können.

1.  Fügen Sie die folgenden Anweisungen am Anfang der Datei ein.

        using Microsoft.WindowsAzure.MobileServices; 
        using Microsoft.WindowsAzure.MobileServices.Sync; 
        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2.  Ändern Sie den Mitgliedstyp für `todoTable` von `IMobileServiceTable` in `IMobileServicesSyncTable`.

        IMobileServiceSyncTable<ToDoItem> todoTable; 

3.  Ändern Sie im Konstruktor für `QSTodoService` den Initialisierer für `todoTable`:

        todoTable = client.GetSyncTable <ToDoItem> ();

4.  Fügen Sie im Konstruktor für `QSTodoService` einen Aufruf für `SQLitePCL.CurrentPlatform.Init()` als zweite Codezeile hinzu:

        QSTodoService ()
        {
            CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

            // Initialize the Mobile Service client with your URL and key
            client = new MobileServiceClient (applicationURL, applicationKey, this);

            // Create an MSTable instance to allow us to work with the TodoItem table
            todoTable = client.GetSyncTable <ToDoItem> ();
        }

5.  Definieren Sie in der Klasse `QSTodoService` eine neue Methode `InitializeAsync`:

        public async Task InitializeStoreAsync()
        {
            string path = "syncstore.db";
            var store = new MobileServiceSQLiteStore(path);
            store.DefineTable<ToDoItem>();
            await client.SyncContext.InitializeAsync(store);
        }

6.  Definieren Sie in der Klasse `QSTodoService` eine neue Methode `SyncAsync`:

        public async Task SyncAsync()
        {
            try
            {
                await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync();
            }
            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

### Bearbeiten von ToDoItem.cs

-   Fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.WindowsAzure.MobileServices; 

-   Fügen Sie die folgenden Mitglieder zur Klasse `ToDoItem` hinzu:

        [Version]
        public string Version { get; set; }


        public override string ToString()
        {
            return "Text: " + Text + "\nComplete: " + Complete + "\n";
        }

### Bearbeiten von QSTodoListViewController.cs

Bearbeiten Sie `QSTodoListViewController` zum Aufrufen der neuen Methode `SyncAsync`, wenn der Benutzer die Aktualisierungsbewegung ausführt.

1.  Fügen Sie einen Aufruf für `InitializeStoreAsync` in `ViewDidLoad()` nach der Initialisierung von `todoService` hinzu:

        public override async void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            todoService = QSTodoService.DefaultService;
            await todoService.InitializeStoreAsync();

            ...    // the rest of the code in the method is unchanged
        }

2.  Passen Sie die Methode `AddRefreshControl` an, um `SyncAsync` vor `RefreshAsync` aufzurufen:

        RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
            await RefreshAsync();
        }; 

<!--  DM: commenting this out because this tutorial doesn't show OC conflict handling ### Edit ToDoItem.cs   Modify the strongly-type data class to add a version field  1. In the top of the file, add the using statement:           using Microsoft.WindowsAzure.MobileServices;   2. Add the following members to the class `ToDoItem`:           [Version]         public string Version { get; set; }                  public override string ToString()         {             return "Text: " + Text + "\nComplete: " + Complete + "\n";         }  -->

## <a name="test-online-app"></a>Testen der App

In diesem Abschnitt testen Sie die `SyncAsync`-Methode zur Synchronisierung des lokalen Speichers mit der Datenbank des mobilen Diensts.

1.  Klicken Sie in Visual Studio auf **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten, was die Voreinstellung bei diesem Projekt darstellt.

2.  Beachten Sie, dass die Liste der Elemente in der App leer ist. Aufgrund der Codeänderungen im vorhergehenden Abschnitt liest die App die Elemente nicht mehr aus dem mobilen Dienst, sondern aus dem lokalen Speicher.

3.  Fügen Sie Elemente zur „Todo“-Liste hinzu.

    ![][]

4.  Melden Sie sich beim Azure-Verwaltungsportal an, und zeigen Sie die Datenbank für Ihren mobilen Dienst an. Wenn Ihr Dienst das JavaScript-Back-End für mobile Dienste verwendet, können Sie die Daten in der Registerkarte **Daten** des mobilen Dienstes durchsuchen. Wenn Sie das .NET-Back-End für Ihren mobilen Dienst verwenden, können Sie auf die Schaltfläche **Manage** für Ihre Datenbank in der SQL-Azure-Erweiterung klicken, um eine Abfrage der Tabelle auszuführen.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher nicht synchronisiert wurden.

5.  Führen Sie in der App die Aktualisierungsbewegung aus, indem Sie die Elementliste nach unten ziehen. Dadurch ruft die App `MobileServiceClient.SyncContext.PushAsync` und `IMobileServiceSyncTable.PullAsync()` auf und dann `RefreshTodoItems`, um die App mit Elementen aus dem lokalen Speicher zu aktualisieren.

    Dieser Push-Vorgang führt dazu, dass die Datenbank des mobilen Dienstes die Daten aus dem Speicher erhält. Der Vorgang wird aus `MobileServiceClient.SyncContext` statt `IMobileServicesSyncTable` ausgeführt und überträgt per Push alle Änderungen in alle Tabellen, die mit dem Synchronisierungskontext verknüpft sind. Dies ist bei Szenarios erforderlich, in denen Beziehungen zwischen Tabellen bestehen.

    Im Gegensatz dazu ruft der Pull-Vorgang Datensätze nur aus der angegebenen Tabelle ab. Falls ausstehende Vorgänge für diese Tabelle im Synchronisierungskontext vorhanden sind, wird implizit ein `PushAsync`-Vorgang vom Mobile Services SDK aufgerufen.

    ![][1]

    ![][2]

## Zusammenfassung

Um die Offlinefunktionen mobiler Dienste zu unterstützen, haben Sie die Schnittstelle der Tabelle `IMobileServiceSyncTable` verwendet und `MobileServiceClient.SyncContext` mit einem lokalen Speicher initialisiert. In diesem Fall war der lokale Speicher eine SQLite-Datenbank.

Die normalen CRUD-Operationen für mobile Dienste funktionieren so, als ob die App immer noch verbunden wäre. Alle Operationen erfolgen jedoch nur im lokalen Speicher.

Zur Synchronisierung des lokalen Speichers mit dem Server haben Sie die Methoden `IMobileServiceSyncTable.PullAsync` und `MobileServiceClient.SyncContext.PushAsync` verwendet.

-   Anschließend haben Sie `IMobileServiceSyncContext.PushAsync()` abgerufen, um die Änderungen per Push-Vorgang auf den Server zu übertragen. Diese Methode ist ein Element von `IMobileServicesSyncContext` statt der Synchronisierungstabelle, da Änderungen per Push-Vorgang auf alle Tabellen übertragen werden:

    Nur Datensätze, die lokal geändert wurden (mit CRUD-Operationen), werden an den Server gesendet.

-   Um Daten per Pull-Vorgang von einer Tabelle auf dem Server in die App zu übertragen, haben Sie `IMobileServiceSyncTable.PullAsync` abgerufen.

    Ein Pull-Vorgang führt immer zuerst einen Push-Vorgang aus.

    Es gibt außerdem Überladungen von **PullAsync()**, mit denen eine Abfrage spezifiziert werden kann. Beachten Sie, dass in der Vorabversion von Offlineunterstützung für Mobile Services **PullAsync** alle Zeilen in der entsprechenden Tabelle (oder Abfrage) liest. Es wird nicht etwa versucht, nur die Zeilen zu lesen, die neuer als die letzte Synchronisierung sind. Wenn die Zeilen bereits in der lokalen Synchronisierungstabelle vorhanden sind, bleiben sie unverändert.

## Nächste Schritte

<!--* [Handling conflicts with offline support for Mobile Services] -->

-   [So verwenden Sie den Client der Xamarin-Komponente für Azure Mobile Services][So verwenden Sie den Client der Xamarin-Komponente für Azure Mobile Services]

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data "Windows Store C#"
  [Windows Phone]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data "Windows Phone"
  [iOS]: /de-de/documentation/articles/mobile-services-ios-get-started-offline-data "iOS"
  [Xamarin.iOS]: /de-de/documentation/articles/mobile-services-xamarin-ios-get-started-offline-data "Xamarin.iOS"
  [Xamarin.Android]: /de-de/documentation/articles/mobile-services-xamarin-android-get-started-offline-data "Xamarin.Android"
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28
  [Aktualisieren der App für die Unterstützung von Offlinefunktionen]: #enable-offline-app
  [Testen der App mit Verbindung zum mobilen Dienst]: #test-online-app
  [Xamarin-Erweiterung]: http://xamarin.com/visual-studio
  [Xamarin Studio]: http://xamarin.com/download
  [Azure Mobile Services SDK Version 1.3.0-alpha3 (oder neuer)]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-alpha3
  [Azure Mobile Services SQLite Store Version 1.0.0-alpha2 (oder neuer)]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-alpha2
  [NuGet-Add-In für Xamarin]: https://github.com/mrward/monodevelop-nuget-addin
  []: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
  [1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png
  [2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
  [So verwenden Sie den Client der Xamarin-Komponente für Azure Mobile Services]: /de-de/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/
