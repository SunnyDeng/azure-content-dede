<properties urlDisplayName=".NET Client Library" pageTitle="Creating a Leaderboard App with Azure Mobile Services .NET Backend" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Learn how to build a Windows Store app using Azure Mobile Services with a .NET backend." documentationCenter="Mobile" title="Creating a Leaderboard App with Azure Mobile Services .NET Backend" authors="mwasson" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mwasson" />

# Erstellen einer Bestenlisten-App mit Azure Mobile Services .NET-Backend

In diesem Lernprogramm erfahren Sie, wie Sie eine Windows Store-App mithilfe von Azure Mobile Services mit einem .NET-Backend erstellen. Azure Mobile Services bietet ein skalierbares, sicheres Backend mit integrierter Authentifizierung, Überwachung, Pushbenachrichtigungen und anderen Features sowie einer plattformübergreifenden Clientbibliothek für das Erstellen von mobilen Apps. Das .NET-Backend für Mobile Services basiert auf [ASP.NET Web API][ASP.NET Web API] und bietet .NET-Entwicklern eine hervorragende Möglichkeit zum Erstellen von REST-APIs.

+ [Übersicht][Übersicht]
+ [Infos zur Beispiel-App][Infos zur Beispiel-App]
+ [Hinzufügen von Datenmodellen][Hinzufügen von Datenmodellen]
+ [Hinzufügen von Web-API-Controllern][Hinzufügen von Web-API-Controllern]
+ [Verwenden von DTOs zum Zurückgeben verwandter Entitäten][Verwenden von DTOs zum Zurückgeben verwandter Entitäten]
+ [Definieren einer benutzerdefinierten API zum Übermitteln von Bewertungen][Definieren einer benutzerdefinierten API zum Übermitteln von Bewertungen]
+ [Erstellen der Windows Store-App][Erstellen der Windows Store-App]
+ [Hinzufügen von Modellklassen][Hinzufügen von Modellklassen]
+ [Erstellen von Anzeigemodellen][Erstellen von Anzeigemodellen]
+ [Hinzufügen einer MobileServiceClient-Instanz][Hinzufügen einer MobileServiceClient-Instanz]
+ [Erstellen der Hauptseite][Erstellen der Hauptseite]
+ [Veröffentlichen des mobilen Diensts][Veröffentlichen des mobilen Diensts]
+ [Nächste Schritte][Nächste Schritte]

## Übersicht

Web-API ist ein Open-Source-Framework, das .NET-Entwicklern eine hervorragende Möglichkeit zum Erstellen von REST-APIs bietet. Sie können eine Web-API-Lösung auf Azure-Websites, unter Azure Mobile Services mithilfe des .NET-Backend oder selbstgehostet in einem benutzerdefinierten Prozess hosten. Mobile Services ist eine Hostingumgebung, die speziell für mobile Apps entwickelt wurde. Wenn Sie Ihren Web-API-Dienst unter Mobile Services hosten, haben Sie zusätzlich zur Datenspeicherung folgende Vorteile:

- Integrierte Authentifizierung mit Anbietern sozialer Medien und Azure Active Directory (AAD).
- Pushbenachrichtigungen an Apps unter Verwendung gerätespezifischer Benachrichtigungsdienste.
- Ein vollständiger Satz Clientbibliotheken, die den Zugriff auf Ihren Dienst von beliebigen Apps erleichtern.
- Integrierte Protokollierung und Diagnose.

In diesem Lernprogramm führen Sie folgende Schritte aus:

- Erstellen einer REST-API mithilfe von Azure Mobile Services
- Veröffentlichen des Diensts in Azure
- Erstellen einer Windows Store-App, die den Dienst nutzt
- Verwenden von Entity Framework (EF) zum Erstellen von Fremdschlüsselbeziehungen und Datentransferobjekten (DTOs)
- Verwenden der ASP.NET Web-API zur Definition einer benutzerdefinierten API

Für dieses Lernprogramm ist [Visual Studio 2013 Update 2][Visual Studio 2013 Update 2] erforderlich.


## Infos zur Beispiel-App

Eine *Bestenliste* zeigt eine Liste mit Spielern eines Spiels mit dem jeweiligen Ergebnis und dem Rang des Spielers an. Die Bestenliste kann Teil eines größeren Spiels sein oder als separate App vorliegen. Dies ist eine praxisnahe Anwendung, aber trotzdem einfach genug für ein Lernprogramm. Hier sehen Sie einen Screenshot der App:

![][0]

Um die App einfach zu halten, gibt es kein eigentliches Spiel. Stattdessen können Sie Spieler hinzufügen und ein Ergebnis für jeden Spieler angeben. Sobald Sie ein Ergebnis übermitteln, berechnet der mobile Dienst die neue Rangfolge. Auf dem Backend erstellt der mobile Dienst eine Datenbank mit zwei Tabellen:

- Player: Enthält die Spieler-ID und den Namen.
- PlayerRank: Enthält das Ergebnis des Spielers sowie den Rang.

PlayerRank hat einen Fremdschlüssel für Player. Jeder Spieler (Player) verfügt über 0 oder 1 Rang (PlayerRank).

In einer echten Bestenlisten-App kann PlayerRank außerdem über eine Spiel-ID verfügen, damit der Spieler seine Ergebnisse für mehr als ein Spiel übermitteln kann.

![][1]

Die Client-App kann alle Erstellungs-, Lese-, Aktualisierungs- und Löschvorgänge für Player ausführen. Sie kann vorhandene PlayerRank-Entitäten lesen oder löschen, sie jedoch nicht direkt erstellen oder aktualisieren. Der Grund dafür ist, dass die Rangfolge vom Dienst berechnet wird. Stattdessen übermittelt der Client ein Ergebnis, und der Dienst aktualisiert die Rangfolge für alle Spieler.

Laden Sie das [abgeschlossene Beispielprojekt][abgeschlossene Beispielprojekt] herunter.


## Erstellen des Projekts

Starten Sie Visual Studio, und erstellen Sie ein neues ASP.NET-Webanwendungsprojekt. Geben Sie dem Projekt den Namen „Leaderboard“ (Bestenliste).

![][2]

In Visual Studio 2013 Update 2 enthält das ASP.NET-Webanwendungsprojekt jetzt eine Vorlage für Windows Azure Mobile Service. Wählen Sie die Vorlage aus, und klicken Sie auf **OK**.

![][3]

Die Projektvorlage umfasst einen Beispielcontroller und ein Datenobjekt.

![][4]

Diese werden für das Lernprogramm nicht benötigt, daher können Sie sie aus dem Projekt entfernen. Außerdem sollten Sie die Verweise auf „TodoItem“ in WebApiConfig.cs und LeaderboardContext.cs entfernen.

## Hinzufügen von Datenmodellen

Verwenden Sie [EF Code First][EF Code First] zur Definition der Datenbanktabellen. Fügen Sie im Ordner „DataObjects“ eine Klasse namens `Player` hinzu.

	using Microsoft.WindowsAzure.Mobile.Service;
	
	namespace Leaderboard.DataObjects
	{
	    public class Player : EntityData
	    {
	        public string Name { get; set; }
	    }
	}

Fügen Sie eine weitere Klasse namens `PlayerRank` hinzu.

	using Microsoft.WindowsAzure.Mobile.Service;
	using System.ComponentModel.DataAnnotations.Schema;
	
	namespace Leaderboard.DataObjects
	{
	    public class PlayerRank : EntityData
	    {
	        public int Score { get; set; }
	        public int Rank { get; set; }
	
	        [ForeignKey("Id")]
	        public virtual Player Player { get; set; }
	    }
	}

Beide Klassen erben von der **EntityData**-Klasse. Durch das Ableiten von **EntityData** wird die Nutzung der Daten für die App erleichtert. Es wird die plattformübergreifende Clientbibliothek für Azure Mobile Services verwendet. **EntityData** macht außerdem das [Behandeln von Datenbank-Schreibkonflikten][Behandeln von Datenbank-Schreibkonflikten] leichter für die App.

Die `PlayerRank`-Klasse verfügt über eine [Navigationseigenschaft][Navigationseigenschaft], die auf die zugehörige `Player`-Entität verweist. Das Attribut **[ForeignKey]** teilt EF mit, dass die `Player`-Eigenschaft einen Fremdschlüssel darstellt.

# Hinzufügen von Web-API-Controllern

Als Nächstes fügen Sie Web-API-Controller für `Player` und `PlayerRank` hinzu. Statt einfacher Web-API-Controller verwenden Sie einen speziellen Controller, den so genannten *Tabellen-Controller*, der speziell für Azure Mobile Services konzipiert wurde.

Klicken Sie mit der rechten Maustaste auf den Controller-Ordner, erweitern Sie „Hinzufügen“, und klicken Sie dann auf „New Scaffolded Item“.

![][5]

Erweitern Sie im Gerüst-Dialogfeld **Add Scaffold** den Eintrag **Common** auf der linken Seite, und wählen Sie **Windows Azure Mobile Services** aus. Wählen Sie anschließend den **Windows Azure Mobile Services Table Controller** aus. Klicken Sie auf **Hinzufügen**.

Im Dialogfeld **Controller hinzufügen**:

1. Wählen Sie unter **Modellklasse** „Player“ aus.
2. Wählen Sie unter **Datenkontextklasse** „LeaderboardContext“ aus.
3. Nennen Sie den Controller „PlayerContoller“.
4. Klicken Sie auf **Hinzufügen**.

![][6]


Mit diesen Schritten wird eine Datei namens „PlayerController.cs“ zum Projekt hinzugefügt.

![][7]

Der Controller wird von **TableController<t>** abgeleitet. Diese Klasse erbt **ApiController**, ist jedoch auf Azure Mobile Services spezialisiert.

- Routing: Die Standardroute für einen **TableController** ist `/tables/{table_name}/{id}`, wobei *table\_name* dem Namen der Entität entspricht. Die Route für den Player-Controller ist also */tables/player/{id}*. Diese Routingkonvention macht **TableController** mit der Mobile Services [REST-API][REST-API] konsistent.
- Datenzugriff: Für Datenbankvorgänge verwendet die Klasse **TableController** die Schnittstelle **IDomainManager**, die eine Abstraktion für den Datenzugriff definiert. Das Gerüst verwendet **EntityDomainManager**, welches eine konkrete Implementierung von **IDomainManager** ist, die einen EF-Kontext einfasst.

Fügen Sie nun einen zweiten Controller für PlayerRank-Entitäten hinzu. Führen Sie die gleichen Schritte aus, aber wählen Sie PlayerRank als Modellklasse. Verwenden Sie dieselbe Datenkontextklasse; Sie müssen keine neue erstellen. Nennen Sie den Controller „PlayerRankController“.

## Verwenden von DTOs zum Zurückgeben verwandter Entitäten

Sie erinnern sich, dass `PlayerRank` eine zugehörige `Player`-Entität hat:

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

Die Mobile Service-Clientbibliothek unterstützt keine Navigationseigenschaften, und diese werden nicht serialisiert. Hier ist beispielsweise die reine HTTP-Antwort für GET `/tables/PlayerRank`:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 97
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 17:58:43 GMT
	
	[{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

Beachten Sie, dass `Player` nicht im Objektdiagramm enthalten ist. Um „Player“ einzuschließen, können wir das Objektdiagramm durch Definition eines *Datenübertragungsobjekts* (DTO) abflachen.

Ein DTO ist ein Objekt, das definiert, wie Daten über das Netzwerk gesendet werden. DTOs sind nützlich, wenn Sie ein anderes Übertragungsformat als im Datenbankmodell wünschen. Um ein DTO für `PlayerRank` zu erstellen, fügen Sie eine neue Klasse namens `PlayerRankDto` in den DataObjects-Ordner ein.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerRankDto
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	}

In der Klasse `PlayerRankController` verwenden wir die LINQ **Select**-Methode, um `PlayerRank`-Instanzen in `PlayerRankDto`-Instanzen zu konvertieren. Aktualisieren Sie die Controllermethoden `GetAllPlayerRank` und `GetPlayerRank` wie folgt:

	// GET tables/PlayerRank
	public IQueryable<PlayerRankDto> GetAllPlayerRank()
	{
	    return Query().Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	}
	
	// GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
	public SingleResult<PlayerRankDto> GetPlayerRank(string id)
	{
	    var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
	    {
	        Id = x.Id,
	        PlayerName = x.Player.Name,
	        Score = x.Score,
	        Rank = x.Rank
	    });
	
	    return SingleResult<PlayerRankDto>.Create(result);
	}

Mit diesen Änderungen geben die beiden GET-Methoden `PlayerRankDto`-Objekte an den Client zurück. Die `PlayerRankDto.PlayerName`-Eigenschaft wird auf den Player-Namen eingestellt. Dies ist eine Beispielantwort, nachdem diese Änderung vorgenommen wurde:

	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 160
	Content-Type: application/json; charset=utf-8
	Expires: 0
	Server: Microsoft-IIS/8.0
	Date: Mon, 21 Apr 2014 19:57:08 GMT
	
	[{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

Beachten Sie, dass die JSON-Nutzlast nun die Player-Namen umfasst.

Alternativ zur Verwendung von LINQ Select-Anweisungen ist AutoMapper eine weitere Option. Diese Option erfordert zusätzlichen Einrichtungscode, ermöglicht jedoch die automatische Zuordnung von Domänenentitäten zu DTOs. Weitere Informationen finden Sie unter [Zuordnung zwischen Datenbanktypen und Clienttypen im .NET-Backend mithilfe von AutoMapper][Zuordnung zwischen Datenbanktypen und Clienttypen im .NET-Backend mithilfe von AutoMapper].

## Definieren einer benutzerdefinierten API zum Übermitteln von Bewertungen

Die `PlayerRank`-Entität umfasst eine Eigenschaft `Rank`. Dieser Wert wird vom Server berechnet, er soll nicht vom Client festgelegt werden. Stattdessen verwenden Clients eine benutzerdefinierte API, um das Spielergebnis zu übermitteln. Sobald der Server ein neues Ergebnis empfängt, aktualisiert er die Rangfolge der Spieler.

Fügen Sie im Ordner „DataObjects“ zuerst eine Klasse namens `PlayerScore` hinzu.

	namespace Leaderboard.DataObjects
	{
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Verschieben Sie in der Klasse `PlayerRankController` die Variable `LeaderboardContext` vom Konstruktor zu einer Klassenvariable:

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        LeaderboardContext context = new LeaderboardContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // LeaderboardContext context = new LeaderboardContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }


Löschen Sie die folgenden Methoden aus `PlayerRankController`:

- `PatchPlayerRank`
- `PostPlayerRank`
- `DeletePlayerRank`

Fügen Sie anschließend den folgenden Code zu `PlayerRankController` hinzu:

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

Die `PostPlayerScore`-Methode verwendet eine `PlayerScore`-Instanz als Eingabe. (Der Client sendet das Ergebnis als `PlayerScore` in einer HTTP POST-Anforderung.) Die Methode bewirkt Folgendes:

1. Fügt einen neuen Wert für `PlayerRank` für den Spieler hinzu, falls er nicht bereits in der Datenbank vorhanden ist.
2. Aktualisiert das Ergebnis des Spielers.
3. Führt eine SQL-Abfrage durch, welche die Rangfolge für alle Spieler in einem Batchvorgang aktualisiert.

Das Attribut **[Route]** zur Definition einer benutzerdefinierten Route für diese Methode:

	[Route("api/score")]

Sie können die Methode auch in einen separaten Controller einfügen. Es gibt keinen besonderen Vor- oder Nachteil, es hängt einfach davon ab, wie Sie Ihren Code organisieren.
Weitere Informationen zum Attribut **[Route]** finden Sie unter [Attribut-Routing in Web-API][Attribut-Routing in Web-API].

## Erstellen der Windows Store-App

In diesem Abschnitt wird die Windows Store-App beschrieben, die den mobilen Dienst nutzt. Das Thema XAML oder die UI wird hier jedoch nicht weiter vertieft. Stattdessen konzentrieren wir uns auf die Anwendungslogik. Sie können das vollständige Projekt [hier][abgeschlossene Beispielprojekt] herunterladen.

Fügen Sie ein neues Windows Store App-Projekt zur Lösung hinzu. Hier wurde die Vorlage „Blank App (Windows)“ verwendet.

![][8]

Verwenden Sie den NuGet-Paket-Manager, um die Mobile Services-Clientbibliothek hinzuzufügen. Wählen Sie in Visual Studio im Menü **Extras** die Option **NuGet-Paket-Manager** aus. Klicken Sie anschließend auf **Paket-Manager-Konsole**. Geben Sie im Fenster „Paket-Manager-Konsole“ den folgenden Befehl ein:

	Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

Der Switch „-Project“ gibt an, in welchem Projekt das Paket installiert wird.

## Hinzufügen von Modellklassen

Erstellen Sie einen Ordner namens „Models“, und fügen Sie die folgenden Klassen hinzu:

	namespace LeaderboardApp.Models
	{
	    public class Player
	    {
	        public string Id { get; set; }
	        public string Name { get; set; }
	    }
	
	    public class PlayerRank
	    {
	        public string Id { get; set; }
	        public string PlayerName { get; set; }
	        public int Score { get; set; }
	        public int Rank { get; set; }
	    }
	
	    public class PlayerScore
	    {
	        public string PlayerId { get; set; }
	        public int Score { get; set; }
	    }
	}

Diese Klassen stimmen direkt mit den Datenentitäten im mobilen Dienst überein.

## Erstellen von Anzeigemodellen

Model-View-ViewModel (MVVM) ist eine Variante von Model-View-Controller (MVC). Das MVVM-Muster hilft, die Anwendungslogik von der Darstellung zu trennen.

- Das Modell stellt die Domänendaten dar (Spieler, Rangfolge und Ergebnis).
- Das Ansichtsmodell ist eine abstrakte Darstellung der Ansicht.
- Die Ansicht zeigt das Ansichtsmodell an und sendet Benutzereingaben an das Ansichtsmodell. Für eine Windows Store-App ist die Ansicht in XAML definiert.

![][9]

Fügen Sie eine Klasse namens `LeaderboardViewModel` hinzu.

	using LeaderboardApp.Models;
	using Microsoft.WindowsAzure.MobileServices;
	using System.ComponentModel;
	using System.Net.Http;
	using System.Threading.Tasks;
	
	namespace LeaderboardApp.ViewModel
	{
	    class LeaderboardViewModel : INotifyPropertyChanged
	    {
	        MobileServiceClient _client;
	
	        public LeaderboardViewModel(MobileServiceClient client)
	        {
	            _client = client;
	        }
	    }
	}

Implementieren Sie **INotifyPropertyChanged** im Ansichtsmodell, damit das Ansichtsmodell an der Datenbindung teilnehmen kann.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }    
    }

Anschließend fügen Sie überwachbare Eigenschaften hinzu. XAML bindet Daten an diese Eigenschaften.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

Die Eigenschaft `IsPending` ist „true“, solange eine asynchrone Operation für den Dienst aussteht. Die Eigenschaft `ErrorMessage` enthält ggf. Fehlermeldungen vom Dienst.

Fügen Sie zuletzt Methoden hinzu, welche die Dienstebene aufrufen.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            }; 
            
            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }    
    }

## Hinzufügen einer MobileServiceClient-Instanz

Öffnen Sie die Datei „App.xaml.cs“, und fügen Sie eine **MobileServiceClient**-Instanz zur Klasse `App` hinzu.

	// New code:
	using Microsoft.WindowsAzure.MobileServices;
	
	namespace LeaderboardApp
	{
	    sealed partial class App : Application
	    {
	        // New code.
	        // TODO: Replace 'port' with the actual port number.
	        const string serviceUrl = "http://localhost:port/";
	        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);
	
	
	        // ...
	    }
	}

Beim lokalen Debuggen wird der mobile Dienst in IIS Express ausgeführt. Visual Studio weist eine zufällige Portnummer zu, sodass die lokale URL <http://localhost>:*port* lautet, wobei *port* der Portnummer entspricht. Um die Portnummer abzurufen, starten Sie den Dienst in Visual Studio, indem Sie F5 zum Debuggen drücken. Visual Studio startet einen Browser und navigiert zur Dienst-URL. Sie finden die lokale URL außerdem in den Projekteigenschaften unter **Web**.

## Erstellen der Hauptseite

Fügen Sie auf der Hauptseite eine Instanz des Ansichtsmodells hinzu. Legen Sie dann das Ansichtsmodell als **DataContext** für die Seite fest.

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...


Wie bereits erwähnt ist dies nicht die gesamte XAML für die App. Einer der Vorteile des MVVM-Muster besteht darin, dass die Darstellung von der Anwendungslogik getrennt ist, sodass die UI ganz einfach geändert werden kann, wenn Ihnen die Beispiel-App nicht gefällt.

Die Liste der Spieler wird in einem Listenfeld (**ListBox**) angezeigt:

	<ListBox Width="200" Height="400" x:Name="PlayerListBox" 
	    ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

Die Rangfolge wird in einer Listenansicht (**ListView**) angezeigt:

	<ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
	    <!-- Header and styles not shown -->
	    <ListView.ItemTemplate>
	        <DataTemplate>
	            <Grid>
	                <Grid.ColumnDefinitions>
	                    <ColumnDefinition Width="*"/>
	                    <ColumnDefinition Width="2*"/>
	                    <ColumnDefinition Width="*"/>
	                </Grid.ColumnDefinitions>
	                <TextBlock Text="{Binding Path=Rank}"/>
	                <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
	                <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
	            </Grid>
	        </DataTemplate>
	    </ListView.ItemTemplate>
	</ListView>

Alle Datenbindungen erfolgen über das Ansichtsmodell.

## Veröffentlichen des mobilen Diensts

In diesem Schritt veröffentlichen Sie Ihren mobilen Dienst in Microsoft Azure und ändern die App, um den Live-Dienst zu verwenden.

Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Leaderboard-Projekt, und wählen Sie **Veröffentlichen** aus.

![][10]

Klicken Sie im Dialogfeld **Veröffentlichen** auf **Windows Azure Mobile Services**.

![][11]

Falls Sie nicht bereits bei Ihrem Azure-Konto angemeldet sind, klicken Sie auf **Anmelden**.

![][12]


Wählen Sie einen vorhandenen mobilen Dienst aus, oder klicken Sie auf **Neu**, um einen neuen zu erstellen. Klicken Sie anschließend auf **OK**, um ihn zu veröffentlichen.

![][13]

Der Veröffentlichungsprozess erstellt die Datenbank automatisch. Sie müssen keine Verbindungszeichenfolge konfigurieren.

Nun können Sie eine Verbindung zwischen der Bestenlisten-App und dem Live-Dienst herstellen. Sie benötigen zwei Dinge:

- Die Dienst-URL
- Den Anwendungsschlüssel

Beides erhalten Sie im Azure-Verwaltungsportal. Klicken Sie im Verwaltungsportal auf **Mobile Dienste** und dann auf Ihren mobilen Dienst. Die Dienst-URL ist im Dashboard aufgelistet. Um den Anwendungsschlüssel abzurufen, klicken Sie auf **Schlüssel verwalten**.

![][14]

Kopieren Sie im Dialogfeld **Zugriffsschlüssel verwalten** den Wert für den Anwendungsschlüssel.

![][15]

Übergeben Sie die Dienst-URL und den Anwendungsschlüssel an den **MobileServiceClient**-Konstruktor.

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...


Wenn Sie nun die App ausführen, kommuniziert diese mit dem echten Dienst.

## Nächste Schritte

* [Informationen zu Azure Mobile Services][Informationen zu Azure Mobile Services]
* [Informationen zur Web-API][ASP.NET Web API]
* [Behandeln von Schreibkonflikten in Datenbanken][Behandeln von Schreibkonflikten in Datenbanken]
* [Hinzufügen von Pushbenachrichtigungen][Hinzufügen von Pushbenachrichtigungen]; wenn jemand beispielsweise einen neuen Spieler hinzufügt oder sein Ergebnis aktualisiert
* [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]

 
 


[ASP.NET Web API]: http://asp.net/web-api
[Übersicht]: #overview
[Infos zur Beispiel-App]: #about-the-sample-app
[Hinzufügen von Datenmodellen]: #add-data-models
[Hinzufügen von Web-API-Controllern]: #add-web-api-controllers
[Verwenden von DTOs zum Zurückgeben verwandter Entitäten]: #use-a-dto-to-return-related-entities
[Definieren einer benutzerdefinierten API zum Übermitteln von Bewertungen]: #define-a-custom-api-to-submit-scores
[Erstellen der Windows Store-App]: #create-the-windows-store-app
[Hinzufügen von Modellklassen]: #add-model-classes
[Erstellen von Anzeigemodellen]: #create-a-view-model
[Hinzufügen einer MobileServiceClient-Instanz]: #add-a-mobileserviceclient-instance
[Erstellen der Hauptseite]: #create-the-main-page
[Veröffentlichen des mobilen Diensts]: #publish-your-mobile-service
[Nächste Schritte]: #next-steps
[Visual Studio 2013 Update 2]: http://go.microsoft.com/fwlink/p/?LinkID=390465
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
[abgeschlossene Beispielprojekt]: http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
[EF Code First]: http://msdn.microsoft.com/de-de/data/ee712907#codefirst
[Behandeln von Datenbank-Schreibkonflikten]: http://azure.microsoft.com/de-de/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/
[Navigationseigenschaft]: http://msdn.microsoft.com/de-de/data/jj713564.aspx
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
[REST-API]: http://msdn.microsoft.com/de-de/library/azure/jj710104.aspx
[Zuordnung zwischen Datenbanktypen und Clienttypen im .NET-Backend mithilfe von AutoMapper]: http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx
[Attribut-Routing in Web-API]: http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
[13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
[14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
[15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png
[Informationen zu Azure Mobile Services]: /de-de/develop/mobile/resources/
[Behandeln von Schreibkonflikten in Datenbanken]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/
[Hinzufügen von Pushbenachrichtigungen]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
