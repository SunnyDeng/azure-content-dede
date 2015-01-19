<properties urlDisplayName="Using Offline Data" pageTitle="Verwenden von Offlinedaten in Mobile Services (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie, wie Sie mit Azure Mobile Services Offline-Daten in Ihrer Xamarin-iOS-Anwendung in den Cache verschieben und synchronisieren." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data in Mobile Services" authors="donnam" editor="wesmc" manager="dwrede"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="donnam" />

# Verwenden der Offlinedatensynchronisierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]

In diesem Thema erfahren Sie, wie Sie die Offlinefunktionen von Azure Mobile Services nutzen können. Diese Funktionen ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie den mobilen Dienst offline verwenden. Mit den Offlinefunktionen können Sie Ihre lokalen Änderungen mit dem mobilen Dienst synchronisieren, wenn Sie erneut online sind. 

In diesem Lernprogramm aktualisieren Sie die App aus dem Lernprogramm [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] so, dass diese die Offlinefunktionen von Azure Mobile Services unterstützt. Anschließend fügen Sie Daten in einem Offlineszenario mit getrennter Verbindung hinzu, synchronisieren diese Elemente mit der Onlinedatenbank und melden sich daraufhin beim Azure-Verwaltungsportal an, um Änderungen an den Daten anzuzeigen, die bei der Ausführung der Anwendung vorgenommen wurden.

>[WACOM.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie Azure Mobile Services verwenden können, um Daten in einer Windows Store-App zu speichern und abzurufen. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.
>
>Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">"Kostenlose Azure-Testversion"</a>. 

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Aktualisieren der App für die Unterstützung von Offlinefunktionen]
2. [Testen der App mit Verbindung zum mobilen Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

* XCode 4.5 und iOS 6.0 (oder neuere Versionen) 
* Visual Studio mit der [Xamarin-Erweiterung] **oder** [Xamarin Studio] unter OS X
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten]
* [Azure Mobile Services SDK Version 1.3.0 (oder höher)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store Version 1.0.0 (oder höher)][SQLite store nuget]

>[WACOM.NOTE] In der folgenden Anleitung wird davon ausgegangen, dass Sie Visual Studio 2012 oder höher mit der Xamarin-Erweiterung verwenden. Bei Verwendung von Xamarin Studio unter OS X verwenden Sie die integrierte Unterstützung für den NuGet-Paket-Manager.

## <a name="enable-offline-app"></a>Aktualisieren der App für die Unterstützung von Offlinefunktionen

Mit der Offlinesynchronisierung von Azure Mobile Services können Endbenutzer mit einer lokalen Datenbank interagieren, wenn das Netzwerk nicht verfügbar ist. Um diese Funktionen in der App zu verwenden, initialisieren Sie einen "MobileServiceClient.SyncContext" in einem lokalen Speicher. Erstellen Sie dann für die Tabelle einen Verweis über die Schnittstelle "IMobileServiceSyncTable".

Ein Projekt mit dem abgeschlossenen Zustand dieses Lernprogramms ist [hier](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS) verfügbar.

1. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten abgeschlossen] haben. Entfernen Sie im Projektmappen-Explorer den Verweis auf **Azure Mobile Services SDK** unter **Komponenten**.

2. Installieren Sie die Vorabversion von Mobile Services SQLiteStore mithilfe des folgenden Befehls in der Paket-Manager-Konsole: 
    
        install-package WindowsAzure.MobileServices.SQLiteStore -Pre

    Damit werden auch alle erforderlichen Abhängigkeiten installiert.
    
3. Entfernen Sie im Knoten "Verweise" die Verweise auf "System.IO", "System.Runtime" und "System.Threading.Tasks".

### Bearbeiten der Datei QSTodoService.cs 

Bearbeiten Sie die Klasse "QSTodoService", um die Offlinefunktionen von Mobile Services mit einem lokalen SQLite-Speicher nutzen zu können.

1. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein.

		using Microsoft.WindowsAzure.MobileServices; 
		using Microsoft.WindowsAzure.MobileServices.Sync; 
		using Microsoft.WindowsAzure.MobileServices.SQLiteStore;

2. Ändern Sie den Typ des Elements "todoTable" von "IMobileServiceTable" in "IMobileServicesSyncTable".

		IMobileServiceSyncTable<ToDoItem> todoTable; 

3. Ändern Sie im Konstruktor für "QSTodoService" den Initialisierer für "TodoTable":

        todoTable = client.GetSyncTable <ToDoItem> ();

4. Fügen Sie im Konstruktor für "QSTodoService" einen Aufruf für "SQLitePCL.CurrentPlatform.Init()" als zweite Codezeile hinzu:

		QSTodoService ()
		{
			CurrentPlatform.Init ();
            SQLitePCL.CurrentPlatform.Init(); // add this line

			// Initialize the Mobile Service client with your URL and key
			client = new MobileServiceClient (applicationURL, applicationKey, this);

			// Create an MSTable instance to allow us to work with the TodoItem table
			todoTable = client.GetSyncTable <ToDoItem> ();
		}
 
5. In the class `QSTodoService`, define a new method `InitializeAsync`:
 
		public async Task InitializeStoreAsync()
		{
		    string path = "syncstore.db";
		    var store = new MobileServiceSQLiteStore(path);
		    store.DefineTable<ToDoItem>();
		    await client.SyncContext.InitializeAsync(store);
		}

6. Definieren Sie in der Klasse "QSTodoService" eine neue Methode "SyncAsync":
 
		public async Task SyncAsync()
		{
		    try
		    {
		        await this.client.SyncContext.PushAsync();
                await this.todoTable.PullAsync("todoItems", todoTable.CreateQuery());
		    }
		    catch (MobileServiceInvalidOperationException e)
		    {
		        Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
		    }
		}

### Bearbeiten von ToDoItem.cs 

1. Fügen Sie die folgende using-Anweisung hinzu: 

        using Microsoft.WindowsAzure.MobileServices; 


2. Fügen Sie die folgenden Elemente zur Klasse "ToDoItem" hinzu:
 
		[Version]
		public string Version { get; set; }
		
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

### Bearbeiten von QSTodoListViewController.cs 

Ändern Sie "QSTodoListViewController" so, dass die neue Methode "SyncAsync" aufgerufen wird, wenn der Benutzer die Aktualisierungsbewegung ausführt.
 
1. Fügen Sie in "ViewDidLoad()" nach der Initialisierung von "todoService" einen Aufruf von "InitializeStoreAsync" hinzu:

		public override async void ViewDidLoad ()
		{
		    base.ViewDidLoad ();
		
		    todoService = QSTodoService.DefaultService;
			await todoService.InitializeStoreAsync();
			
			...    // the rest of the code in the method is unchanged
		}

2. Ändern Sie die Methode "AddRefreshControl" so, dass "SyncAsync" vor dem Aufruf von "RefreshAsync" aufgerufen wird:


		RefreshControl.ValueChanged += async (sender, e) => {
            await todoService.SyncAsync();
			await RefreshAsync();
		}; 

<!-- 
DM: commenting this out because this tutorial doesn't show OC conflict handling
### Edit ToDoItem.cs 

Modify the strongly-type data class to add a version field

1. In the top of the file, add the using statement: 

        using Microsoft.WindowsAzure.MobileServices; 

2. Add the following members to the class `ToDoItem`:
 
		[Version]
		public string Version { get; set; }
		
		public override string ToString()
		{
		    return "Text: " + Text + "\nComplete: " + Complete + "\n";
		}

-->

## <a name="test-online-app"></a>Testen der App 

In diesem Abschnitt testen Sie die "SyncAsync"-Methode, die den lokalen Speicher mit der Datenbank des mobilen Diensts synchronisiert.

1. Klicken Sie in Visual Studio auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten, was die Standardeinstellung bei diesem Projekt darstellt.

2. Beachten Sie, dass die Liste der Elemente in der App leer ist. Aufgrund der Codeänderungen im vorhergehenden Abschnitt liest die App die Elemente nicht mehr aus dem mobilen Dienst, sondern aus dem lokalen Speicher. 

3. Fügen Sie Elemente zur "Todo"-Liste hinzu.

    ![][1]


4. Melden Sie sich beim Azure-Verwaltungsportal an, und zeigen Sie die Datenbank für Ihren mobilen Dienst an. Wenn Ihr Dienst das JavaScript-Back-End für Mobile Services verwendet, können Sie die Daten auf der Registerkarte **Daten** mobilen Diensts durchsuchen. Wenn Sie das .NET-Back-End für Ihren mobilen Dienst verwenden, können Sie auf die Schaltfläche **Verwalten** für Ihre Datenbank in der SQL Azure-Erweiterung klicken, um eine Abfrage an die Tabelle auszuführen.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher nicht synchronisiert wurden.

5. Führen Sie in der App die Aktualisierungsbewegung aus, indem Sie die Elementliste nach unten ziehen. Dadurch ruft die App "MobileServiceClient.SyncContext.PushAsync" und "IMobileServiceSyncTable.PullAsync()" auf und anschließend "RefreshTodoItems", um die App mit den Elementen aus dem lokalen Speicher zu aktualisieren. 

    Dieser Push-Vorgang führt dazu, dass die Datenbank des mobilen Dienstes die Daten aus dem Speicher erhält. Dieser Vorgang wird aus "MobileServiceClient.SyncContext" statt aus "IMobileServicesSyncTable" ausgeführt und überträgt per Push die Änderungen in alle Tabellen, die mit diesem Synchronisierungskontext verknüpft sind. Dies ist bei Szenarios erforderlich, in denen Beziehungen zwischen Tabellen bestehen.
    
    Im Gegensatz dazu ruft der Pull-Vorgang Datensätze nur aus der angegebenen Tabelle ab. Falls ausstehende Vorgänge für diese Tabelle im Synchronisierungskontext vorhanden sind, wird implizit ein "PushAsync"-Vorgang vom Mobile Services SDK aufgerufen.
        
    ![][3] 



    ![][2]


  

##Zusammenfassung

Um die Offlinefunktionen von Mobile Services zu unterstützen, haben wir die Schnittstelle der Tabelle "IMobileServiceSyncTable" verwendet und "MobileServiceClient.SyncContext" mit einem lokalen Speicher initialisiert. In diesem Fall war der lokale Speicher eine SQLite-Datenbank.

Die normalen CRUD-Operationen für mobile Dienste funktionieren so, als ob die App immer noch verbunden wäre. Alle Operationen erfolgen jedoch nur im lokalen Speicher.

Zur Synchronisierung des lokalen Speichers mit dem Server haben wir die Methoden "IMobileServiceSyncTable.PullAsync" und "MobileServiceClient.SyncContext.PushAsync" verwendet.

*  Um Änderungen per Push-Vorgang auf den Server zu übertragen, haben wir "IMobileServiceSyncContext.PushAsync()" aufgerufen. Diese Methode ist ein Element von "IMobileServicesSyncContext" statt der Synchronisierungstabelle, da Änderungen per Push-Vorgang in alle Tabellen übertragen werden:

    Nur Datensätze, die lokal geändert wurden (mit CRUD-Operationen), werden an den Server gesendet.
   
* Um Daten per Pull-Vorgang von einer Tabelle auf dem Server in die App zu übertragen, haben wir "IMobileServiceSyncTable.PullAsync" aufgerufen.

    Ein Pull-Vorgang führt immer zuerst einen Push-Vorgang aus.  

    Die Methode **PullAsync()** erfordert eine Abfrage-ID und eine Abfrage. Die Abfrage-ID wird für die inkrementelle Synchronisierung verwendet, und Sie sollten eine andere Abfrage-ID für jede eindeutige Abfrage in Ihrer App verwenden. Das Mobile Services SDK verfolgt den Zeitstempel der letzten Aktualisierung nach jedem erfolgreichen Pull-Vorgang. Beim nächsten Pull-Vorgang werden nur neuere Datensätze abgerufen. Wenn Null als Abfrage-ID angegeben wird, wird für die Synchronisierungstabelle eine vollständige Synchronisierung ausgeführt.

## Nächste Schritte

Sie können die abgeschlossene Version dieses Lernprogramms in unserem [Repository mit GitHub-Beispielen](https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/Xamarin.iOS) herunterladen.


<!--* [Handling conflicts with offline support for Mobile Services]
-->
* [Verwenden des Clients der Xamarin-Komponente für Azure Mobile Services]

<!-- Anchors. -->
[Aktualisieren der App für die Unterstützung von Offlinefunktionen]: #enable-offline-app
[Testen der App mit Verbindung zum mobilen Dienst]: #test-online-app
[Nächste Schritte]:#next-steps

<!-- Images -->
[1]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-startup-ios.png
[2]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-data-browse.png
[3]: ./media/mobile-services-xamarin-ios-get-started-offline-data/mobile-quickstart-completed-ios.png



<!-- URLs. -->
[Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]: /de-de/documentation/articles/mobile-services-xamarin-ios-handling-conflicts-offline-data/ 
[Erste Schritte mit Daten]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started/
[Verwenden des Clients der Xamarin-Komponente für Azure Mobile Services]: /de-de/documentation/articles/partner-xamarin-mobile-services-how-to-use-client-library/

[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite Store Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0
[Xamarin Studio]: http://xamarin.com/download
[Xamarin-Erweiterung]: http://xamarin.com/visual-studio
[NuGet-Add-In für Xamarin]: https://github.com/mrward/monodevelop-nuget-addin

<!--HONumber=35.2-->
