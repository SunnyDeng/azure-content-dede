<properties  pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga"  solutions="mobile" writer="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Planen von periodischen Aufträgen in Mobile Services

<div class="dev-center-tutorial-subselector">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript-Backend" >JavaScript-Backend</a>
</div>

In diesem Thema erfahren Sie, wie Sie die Auftragsplanerfunktion im Verwaltungsportal verwenden, um Serverskript-Code zu definieren, der auf der Grundlage eines von Ihnen festgelegten Plans ausgeführt wird. Im vorliegenden Fall führt das Skript einen periodischen Abgleich mit einem Remote-Dienst, hier Twitter, aus und speichert die Ergebnisse in einer neuen Tabelle. Im Folgenden sind einige weitere periodische Aufgaben aufgeführt, die geplant werden können:

-   Archivieren von veralteten oder doppelten Datensätzen.
-   Anfordern und Speichern von externen Daten, wie etwa Tweets, RSS-Einträgen oder Standortinformationen.
-   Verarbeiten oder Anpassen der Größe von gespeicherten Images.

In diesem Lernprogramm werden die Schritte bezüglich der Verwendung des Auftragsplaners behandelt, um mit diesem einen geplanten Auftrag zu erstellen, welcher Tweet-Daten von Twitter anfordert und die Tweets in einer neuen Aktualisierungstabelle speichert:

-   [Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen][Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen]
-   [Herunterladen und Installieren der LINQtoTwitter-Bibliothek][Herunterladen und Installieren der LINQtoTwitter-Bibliothek]
-   [Erstellen der neuen Aktualisierungstabelle][Erstellen der neuen Aktualisierungstabelle]
-   [Erstellen eines neuen geplanten Auftrags][Erstellen eines neuen geplanten Auftrags]
-   [Lokales Testen des geplanten Auftrags][Lokales Testen des geplanten Auftrags]
-   [Veröffentlichen des Diensts und Registrieren des Auftrags][Veröffentlichen des Diensts und Registrieren des Auftrags]

> [WACOM.NOTE]In diesem Lernprogramm wird die Drittanbieter-LINQtoTwitter-Bibliothek verwendet, um den OAuth 2.0-Zugriff auf Twitter v1.1. APIs zu vereinfachen. Sie müssen das LINQtoTwitter-NuGet-Paket herunterladen und installieren, um dieses Lernprogramm durchführen zu können. Weitere Informationen finden Sie unter [LINQtoTwitter-CodePlex-Projekt][LINQtoTwitter-CodePlex-Projekt].

## <a name="get-oauth-credentials"></a>Registrieren für Zugang zu Twitter v1.1 APIs und Speichern von Anmeldeinformationen

[WACOM.INCLUDE [mobile-services-register-twitter-access][mobile-services-register-twitter-access]]

1.  Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei "web.config" für das Projekt des mobilen Diensts. Suchen Sie die App-Einstellungen **MS\_TwitterConsumerKey** und **MS\_TwitterConsumerSecret**, und ersetzen Sie die Werte dieser Schlüssel durch den Twitter Consumer Key und die Consumer Secret-Werte, die Sie im Portal gesetzt haben.

2.  Fügen Sie im selben Bereich die folgenden neuen App-Einstellungen hinzu. Ersetzen Sie die Platzhalter durch das Twitter-Zugriffstoken und durch die geheimen Werte des Zugriffstoken, die Sie als App-Einstellungen im Portal gesetzt haben:

        <add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
        <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />

    Der mobile Dienst verwendet diese gespeicherten Einstellungen, wenn er auf dem lokalen Computer ausgeführt wird. Dadurch können Sie den geplanten Auftrag testen, bevor Sie ihn veröffentlichen. Beim Ausführen in Azure verwendet der mobile Dienst stattdessen jene Werte, die im Portal gesetzt wurden, und ignoriert die Projekteinstellungen.

## <a name="install-linq2twitter"></a>Herunterladen und Installieren der LINQtoTwitter-Bibliothek

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online**, suchen Sie nach `linq2twitter`, klicken Sie für das Paket **linqtotwitter** auf **Installieren**, und lesen und akzeptieren Sie die Lizenzbedingungen.

    ![][]

    Damit wird die LINQtoTwitter-Bibliothek zu Ihrem Projekt des mobilen Diensts hinzugefügt.

Als Nächstes müssen Sie eine neue Tabelle erstellen, in der Tweets gespeichert werden.

## <a name="create-table"></a>Erstellen der neuen Aktualisierungstabelle

1.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf den Ordner "DataObjects", und erweitern Sie **Hinzufügen**. Klicken Sie auf **Klasse**, geben Sie `Updates` unter **Name** ein. Klicken Sie danach auf **Hinzufügen**.

    Damit wird eine neue Projektdatei für die Updates-Klasse erstellt.

2.  Klicken Sie mit der rechten Maustaste auf **Verweise**, klicken Sie auf **Verweis hinzufügen...**, wählen Sie unter **Assemblys** die Option **Framework**, aktivieren Sie **System.ComponentModel.DataAnnotations**, und klicken Sie auf **OK**.

    ![][1]

    Dadurch wird ein neuer Assemblyverweis hinzugefügt.

3.  Fügen Sie in dieser neuen Klasse die folgenden **using**-Anweisungen hinzu:

        using Microsoft.WindowsAzure.Mobile.Service;
        using System.ComponentModel.DataAnnotations;

4.  Ersetzen Sie die **Updates**-Klassendefinition durch den folgenden Code:

        public class Updates 
        {
            [Key]
            public int UpdateId { get; set; }
            public long TweetId { get; set; }
            public string Text { get; set; }
            public string Author { get; set; }
            public DateTime Date { get; set; }
        }

5.  Erweitern Sie den Ordner "Models", öffnen Sie Datenmodellkontextdatei (mit der Bezeichnung *service\_name*Context.cs), und fügen Sie die folgende Eigenschaft hinzu, die ein typisiertes **DbSet** zurückgibt:

        public DbSet<Updates> Updates { get; set; }

    Die Aktualisierungstabelle, die in der Datenbank beim ersten Zugriff auf das DbSet erstellt wird, wird vom Dienst verwendet, um Tweet-Daten zu speichern.

    > [WACOM.NOTE] Bei Verwendung des Standard-Datenbankinitialisierers löscht Entity Framework die Datenbank und erstellt sie erneut, sobald es eine Datenmodelländerung in der Code First-Modelldefinition erkennt. Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie Code First-Migrationen verwenden. Der Standard-Initialisierer kann nicht für eine SQL-Datenbank in Azure angewendet werden. Weitere Informationen finden Sie unter [Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells][Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells].

Als Nächstes erstellen Sie den geplanten Auftrag, der auf Twitter zugreift und Tweet-Daten in der neuen Aktualisierungstabelle speichert.

## <a name="add-job"></a>Erstellen eines neuen geplanten Auftrags

1.  Erweitern Sie den Ordner "ScheduledJobs", und öffnen Sie die Projektdatei "SampleJob.cs".

    Diese Klasse, die mit **ScheduledJob** verknüpft ist, stellt einen Auftrag dar, der im Azure-Verwaltungsportal geplant werden kann, um nach einem festgelegten Zeitplan oder nach Bedarf ausgeführt zu werden.

2.  Ersetzen Sie den Inhalt der "SampleJob.cs" durch den folgenden Code:

        using System;
        using System.Linq;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Web.Http;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.WindowsAzure.Mobile.Service.ScheduledJobs;
        using LinqToTwitter;
        using todolistService.Models;
        using todolistService.DataObjects;

        namespace todolistService
        {
            // A simple scheduled job which can be invoked manually by submitting an HTTP
            // POST request to the path "/jobs/sample".
            public class SampleJob : ScheduledJob
            {
                private todolistContext context;
                private string accessToken;
                private string accessTokenSecret;

                protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
                {
                    base.Initialize(scheduledJobDescriptor, cancellationToken);

                    // Create a new context with the supplied schema name.
                    context = new todolistContext(Services.Settings.Name);
                }

                public async override Task ExecuteAsync()
                {            
                    // Try to get the stored Twitter access token from app settings.  
                    if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
                    Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
                    {
                        Services.Log.Error("Could not retrieve Twitter access credentials.");
                    }

                    // Create a new authorizer to access Twitter v1.1 APIs
                    // using single-user OAUth 2.0 credentials.
                    MvcAuthorizer auth = new MvcAuthorizer();
                    SingleUserInMemoryCredentialStore store = 
                        new SingleUserInMemoryCredentialStore()
                    {
                        ConsumerKey = Services.Settings.TwitterConsumerKey,
                        ConsumerSecret = Services.Settings.TwitterConsumerSecret,
                        OAuthToken = accessToken,
                        OAuthTokenSecret = accessTokenSecret
                    };

                    // Set the credentials for the authorizer.
                    auth.CredentialStore = store;

                    // Create a new LINQ to Twitter context.
                    TwitterContext twitter = new TwitterContext(auth);

                    // Get the ID of the most recent stored tweet.
                    long lastTweetId = 0;
                    if (context.Updates.Count() > 0)
                    {
                        lastTweetId = (from u in context.Updates
                                       orderby u.TweetId descending
                                       select u).Take(1).SingleOrDefault()
                                                    .TweetId;
                    }

                    // Execute a search that returns a filtered result.
                    var response = await (from s in twitter.Search
                                          where s.Type == SearchType.Search
                                          && s.Query == "%23mobileservices"
                                          && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
                                          && s.ResultType == ResultType.Recent
                                          select s).SingleOrDefaultAsync();

                    // Remove retweets and replies and log the number of tweets.
                    var filteredTweets = response.Statuses
                        .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
                    Services.Log.Info("Fetched " + filteredTweets.Count()
                        + " new tweets from Twitter.");

                    // Store new tweets in the Updates table.
                    foreach (Status tweet in filteredTweets)
                    {
                        Updates newTweet =
                            new Updates
                            {
                                TweetId = Convert.ToInt64(tweet.StatusID),
                                Text = tweet.Text,
                                Author = tweet.User.Name,
                                Date = tweet.CreatedAt
                            };

                        context.Updates.Add(newTweet);
                    }

                    await context.SaveChangesAsync();
                }
                protected override void Dispose(bool disposing)
                {
                    base.Dispose(disposing);
                    if (disposing)
                    {
                        context.Dispose();
                    }
                }
            }
        }

    Im oben angegebenen Code müssen Sie die Zeichenfolgen *todolistService* und *todolistContext* durch den Namespace und den DbContext Ihres heruntergeladenen Projekts ersetzen. Dabei handelt es sich in diesem Fall um den Dienst *mobile\_service\_name* beziehungsweise um den Kontext *mobile\_service\_name*.

    Im oben angegebenen Code ruft die Überschreibungsmethode **ExecuteAsync** die Twitter-Abfrage-API mithilfe der gespeicherten Anmeldeinformationen auf, um aktuelle Tweets anzufordern, die das Hashtag `#mobileservices` enthalten. Doppelte Tweets und Antworten werden aus den Ergebnissen entfernt, bevor sie in der Tabelle gespeichert werden.

## <a name="run-job-locally"></a>Lokales Testen des geplanten Auftrags

Geplante Aufträge können lokal getestet werden, bevor Sie in Azure veröffentlicht und im Portal registriert werden.

1.  Drücken Sie in Visual Studio die Taste F5. Das Projekt des mobilen Diensts ist dabei als Startprojekt eingestellt.

    Dadurch wird das Projekt des mobilen Diensts gestartet und ein neues Browser-Fenster mit der Willkommensseite angezeigt.

2.  Klicken Sie auf **Testen Sie es**, und klicken Sie dann auf **POST jobs/{jobName}**.

    ![][2]

3.  Klicken Sie auf **Testen Sie es**, geben Sie `Sample` als Parameterwert für **{jobName}** ein, und klicken Sie auf **Senden**.

    ![][3]

    Daraufhin wird eine neue POST-Anforderung an den Endpunkt des Beispielauftrags gesendet. Die **ExecuteAsync**-Methode wird im lokalen Dienst gestartet. Sie können einen Breakpoint in dieser Methode setzen, um den Code zu debuggen.

4.  Erweitern Sie im Server-Explorer die Optionen **Data Connections**, **MSTableConnectionString** und **tables**. Klicken Sie mit der rechten Maustaste auf **Updates**, und klicken Sie dann auf **Show Table Data**.

    Die neuen Tweets werden als Zeilen in die Datentabelle eingetragen.

## <a name="register-job"></a>Veröffentlichen des Diensts und Registrieren des neuen Auftrags

Der Auftrag muss auf der Registerkarte **Scheduler** registriert werden, sodass Mobile Services ihn nach dem von Ihnen definierten Zeitplan ausführen kann.

1.  das Projekt für den mobilen Service erneut auf Azure veröffentlichen.

2.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf "Mobile Services" und anschließend auf Ihre App.

    ![][4]

3.  Klicken Sie auf der Registerkarte **Scheduler** auf **+Create**.

    ![][5]

    > [WACOM.NOTE] Wenn Sie Ihren mobilen Dienst auf der Ebene *Free* ausführen, können Sie lediglich einen geplanten Auftrag zur Zeit ausführen. Bei bezahlten Ebenen können Sie bis zu zehn geplante Aufträge zur Zeit ausführen.

4.  Geben Sie im Scheduler-Dialog (Auftragsplanerdialog) den Eintrag *SampleJob* für **Auftragsname** ein. Stellen Sie die Planintervalle und -einheiten ein, und klicken Sie anschließend auf den Häkchenknopf.

    ![][6]

    Dadurch wird ein neuer Auftrag mit dem Namen **SampleJob** erstellt.

5.  Klicken Sie auf den gerade erstellten neuen Auftrag und danach auf **Einmal ausführen**, um das Skript zu testen.

    ![][7]

    Dadurch wird der Auftrag ausgeführt, während er im Scheduler deaktiviert bleibt. Sie können von dieser Seite aus den Auftrag aktivieren und dessen Zeitplan jederzeit ändern.

    > [WACOM.NOTE]Eine POST-Anforderung kann weiterhin verwendet werden, um den geplanten Auftrag zu starten. Die Autorisierung ist jedoch für den Benutzer voreingestellt. Dies bedeutet, dass die Anforderung den Anwendungsschlüssel im Header enthalten muss.

6.  (Optional) Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![][8]

7.  Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage ist mit der folgenden Abfrage vergleichbar, allerdings wird anstelle von `todolist` der Name Ihres mobilen Diensts als Schemaname verwendet.

        SELECT * FROM [todolist].[Updates]

Glückwunsch! Sie haben erfolgreich einen neuen geplanten Auftrag in Ihrem mobilen Dienst erstellt. Dieser Auftrag wird wie geplant ausgeführt, bis Sie ihn deaktivieren oder modifizieren.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ ".NET-Backend"
  [JavaScript-Backend]: /de-de/documentation/articles/mobile-services-schedule-recurring-tasks/ "JavaScript-Backend"
  [Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen]: #get-oauth-credentials
  [Herunterladen und Installieren der LINQtoTwitter-Bibliothek]: #install-linq2twitter
  [Erstellen der neuen Aktualisierungstabelle]: #create-table
  [Erstellen eines neuen geplanten Auftrags]: #add-job
  [Lokales Testen des geplanten Auftrags]: #run-job-locally
  [Veröffentlichen des Diensts und Registrieren des Auftrags]: #register-job
  [LINQtoTwitter-CodePlex-Projekt]: http://linqtotwitter.codeplex.com/
  [mobile-services-register-twitter-access]: ../includes/mobile-services-register-twitter-access.md
  []: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
  [1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-component-model-reference.png
  [Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells]: /de-de/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations
  [2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-start-page.png
  [3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-service-try-this-out.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
  [5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
  [7]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
  [8]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png
