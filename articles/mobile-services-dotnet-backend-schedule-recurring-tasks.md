<properties pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Windows Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="" solutions="" writer="" manager="" editor="" />

Planen von periodischen Aufträgen in Mobile Services
====================================================

[.NET backend](/de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/ ".NET backend") | [JavaScript backend](/de-de/documentation/articles/mobile-services-schedule-recurring-tasks/ "JavaScript backend")

In diesem Thema erfahren Sie, wie Sie die Auftragsplanerfunktion im Verwaltungsportal verwenden, um Serverskript-Code zu definieren, der auf der Grundlage eines von Ihnen festgelegten Plans ausgeführt wird. Im vorliegenden Fall führt das Skript einen periodischen Abgleich mit einem Remote-Dienst, hier Twitter, aus und speichert die Ergebnisse in einer neuen Tabelle. Im Folgenden sind einige weitere periodische Aufgaben aufgeführt, die geplant werden können:

-   Archivieren von veralteten oder doppelten Datensätzen.
-   Anfordern und Speichern von externen Daten, wie etwa Tweets, RSS-Einträgen oder Standortinformationen.
-   Verarbeiten oder Anpassen der Größe von gespeicherten Images.

In diesem Lernprogramm werden die Schritte bezüglich der Verwendung des Auftragsplaners behandelt, um mit diesem einen geplanten Auftrag zu erstellen, welcher Tweet-Daten von Twitter anfordert und die Tweets in einer neuen Aktualisierungstabelle speichert:

-   [Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen](#get-oauth-credentials)
-   [Herunterladen und Installieren der LINQtoTwitter-Bibliothek](#install-linq2twitter)
-   [Erstellen der neuen Aktualisierungstabelle](#create-table)
-   [Erstellen eines neuen geplanten Auftrags](#add-job)
-   [Lokales Testen des geplanten Auftrags](#run-job-locally)
-   [Veröffentlichen des Diensts und Registrieren des Auftrags](#register-job)

> [WACOM.NOTE]In diesem Lernprogramm wird die Drittanbieter-LINQtoTwitter-Bibliothek verwendet, um den OAuth 2.0-Zugriff auf Twitter v1.1. APIs zu vereinfachen. Sie müssen das LINQtoTwitter-NuGet-Paket herunterladen und installieren, um dieses Lernprogramm durchführen zu können. Weitere Informationen finden Sie unter [LINQtoTwitter-CodePlex-Projekt](http://linqtotwitter.codeplex.com/).

Registrieren für Zugang zu Twitter v1.1 APIs und Speichern von Anmeldeinformationen
-----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

1.  Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei "web.config" für das Projekt des mobilen Diensts. Suchen Sie die App-Einstellungen **MS\_TwitterConsumerKey** und **MS\_TwitterConsumerSecret**, und ersetzen Sie die Werte dieser Schlüssel durch den Twitter Consumer Key und die Consumer Secret-Werte, die Sie im Portal gesetzt haben.

2.  Fügen Sie im selben Bereich die folgenden neuen App-Einstellungen hinzu. Ersetzen Sie die Platzhalter durch das Twitter-Zugriffstoken und durch die geheimen Werte des Zugriffstoken, die Sie als App-Einstellungen im Portal gesetzt haben:

    ``` {}
    <add key="TWITTER_ACCESS_TOKEN" value="**your_access_token**" />
    <add key="TWITTER_ACCESS_TOKEN_SECRET" value="**your_access_token_secret**" />
    ```

    Der mobile Dienst verwendet diese gespeicherten Einstellungen, wenn er auf dem lokalen Computer ausgeführt wird. Dadurch können Sie den geplanten Auftrag testen, bevor Sie ihn veröffentlichen. Beim Ausführen in Azure verwendet der mobile Dienst stattdessen jene Werte, die im Portal gesetzt wurden, und ignoriert die Projekteinstellungen.

Herunterladen und Installieren der LINQtoTwitter-Bibliothek
-----------------------------------------------------------

1.  Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2.  Wählen Sie im linken Bereich die Kategorie **Online** aus, suchen Sie nach `linq2twitter`, und klicken Sie auf **Install** für das **linqtotwitter**-Paket. Lesen Sie anschließend die Lizenzvereinbarungen, und akzeptieren Sie diese.

	![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png) 

	Damit wird die LINQtoTwitter-Bibliothek zu Ihrem Projekt des mobilen Diensts hinzugefügt.

Als Nächstes müssen Sie eine neue Tabelle erstellen, in der Tweets gespeichert werden.

Erstellen der neuen Aktualisierungstabelle
------------------------------------------

1.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf den Ordner "DataObjects", und erweitern Sie **Add**. Klicken Sie auf **Klasse**, geben Sie `Updates` für **Name** ein. Klicken Sie danach auf **Add**.

    Damit wird eine neue Projektdatei für die Updates-Klasse erstellt.

2.  Fügen Sie in dieser neuen Klasse die folgenden **using**-Anweisungen hinzu:

         using Microsoft.WindowsAzure.Mobile.Service;
         using System.ComponentModel.DataAnnotations;

3.  Ersetzen Sie die **Updates**-Klassendefinition durch den folgenden Code:

         public class Updates 
         {
             [Key]
             public int UpdateId { get; set; }
             public long TweetId { get; set; }
             public string Text { get; set; }
             public string Author { get; set; }
             public DateTime Date { get; set; }
	    }

4.  Erweitern Sie den Ordner "Models", öffnen Sie Datenmodellkontextdatei (mit der Bezeichnung *service\_name*Context.cs), und fügen Sie die folgende Eigenschaft hinzu, die ein typisiertes **DbSet** zurückgibt:

         public DbSet<Updates> Updates { get; set; }

    Die Aktualisierungstabelle, die in der Datenbank beim ersten Zugriff auf das DbSet erstellt wird, wird vom Dienst verwendet, um Tweet-Daten zu speichern.

    > [WACOM.NOTE] Bei der Verwendung des Standarddatenbank-Initialisierers verwirft das Entity Framework die Datenbank und erstellt diese neu, wenn es eine Datenmodelländerung in der Code First-Modelldefinition feststellt. Sie müssen "Code First Migrations" verwenden, um die Datenmodelländerung vornehmen und die vorhandenen Daten in der Datenbank verwalten zu können. Der Standard-Initialisierer kann nicht gegenüber einer SQL-Datenbank in Azure verwendet werden. Weitere Informationen finden Sie unter [Verwenden von "Code First Migrations" zum Aktualisieren des Datenmodells](/de-de/documentation/articles/mobile-services-dotnet-backend-use-code-first-migrations).

Als Nächstes erstellen Sie den geplanten Auftrag, der auf Twitter zugreift und Tweet-Daten in der neuen Aktualisierungstabelle speichert.

Erstellen eines neuen geplanten Auftrags
----------------------------------------

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
             // Einfacher geplanter Auftrag, der manuell aufgerufen werden kann, indem eine HTTP
             // POST Anforderung für den Pfad "/jobs/sample" eingereicht wird.
             public class SampleJob : ScheduledJob
             {
                 private todolistContext context;
                 private string accessToken;
                 private string accessTokenSecret;
            
                 protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, CancellationToken cancellationToken)
                 {
                     base.Initialize(scheduledJobDescriptor, cancellationToken);
            
                     // Erstellen eines neuen Kontexts mit der bereitgestellten Schemabezeichnung.
                     context = new todolistContext(Services.Settings.Name);
                 }
            
                 public async override Task ExecuteAsync()
                 {            
                     // Versuch des Abrufens des gespeicherten Twitter-Zugriffstokens aus den App-Einstellungen.  
                     if (!(Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN", out accessToken) |
                     Services.Settings.TryGetValue("TWITTER_ACCESS_TOKEN_SECRET", out accessTokenSecret)))
                     {
                         Services.Log.Error("Could not retrieve Twitter access credentials.");
                     }
            
                     // Erstellen eines neuen Genehmigenden zum Zugriff auf Twitter v1.1 APIs
                     // mit OAUth 2.0 Einzelbenutzeranmeldeinformationen.
                     MvcAuthorizer auth = new MvcAuthorizer();
                     SingleUserInMemoryCredentialStore store = 
                         new SingleUserInMemoryCredentialStore()
                     {
                         ConsumerKey = Services.Settings.TwitterConsumerKey,
                         ConsumerSecret = Services.Settings.TwitterConsumerSecret,
                         OAuthToken = accessToken,
                         OAuthTokenSecret = accessTokenSecret
                     };
            
                     // Setzen der Anmeldeinformationen für den Genehmigenden.
                     auth.CredentialStore = store;
            
                     // Erstellen eines neuen LINQtoTwitter-Kontexts.
                     TwitterContext twitter = new TwitterContext(auth);
            
                     // Abrufen der ID des neuesten gespeicherten Tweets.
                     long lastTweetId = 0;
                     if (context.Updates.Count() > 0)
                     {
                         lastTweetId = (from u in context.Updates
                                        orderby u.TweetId descending
                                        select u).Take(1).SingleOrDefault()
                                                     .TweetId;
                     }
            
                     // Ausführen einer Suche, die ein gefiltertes Ergebnis zurückgibt.
                     var response = await (from s in twitter.Search
                                           where s.Type == SearchType.Search
                                           && s.Query == "%23mobileservices"
                                           && s.SinceID == Convert.ToUInt64(lastTweetId + 1)
                                           && s.ResultType == ResultType.Recent
                                           select s).SingleOrDefaultAsync();
            
                     // Entfernen der Retweets und Antworten und Protokollieren der Anzahl der Tweets.
                     var filteredTweets = response.Statuses
                         .Where(t => !t.Text.StartsWith("RT") && t.InReplyToUserID == 0);
                     Services.Log.Info("Fetched " + filteredTweets.Count()
                         + " new tweets from Twitter.");
            
                     // Speichern der neuen Tweets in der Aktualisierungstabelle.
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

Lokales Testen des geplanten Auftrags
-------------------------------------

Geplante Aufträge können lokal getestet werden, bevor Sie in Azure veröffentlicht und im Portal registriert werden.

1.  Drücken Sie in Visual Studio die Taste F5. Das Projekt des mobilen Diensts ist dabei als Startprojekt eingestellt.

    Dadurch wird das Projekt des mobilen Diensts gestartet und ein neues Browser-Fenster mit der Willkommensseite angezeigt.

2.  Kopieren Sie die URL des mobilen Diensts aus dem geöffneten Browser-Fenster. Hängen Sie den Pfad `/tables/samplejob` an die URL an, und führen Sie danach eine neue POST-Anforderung für diese URL mit einer HTTP-Utility, wie etwa Fiddler, aus.

    Die *\*ExecuteAsync*-\*Methode wird auf dem lokalen Computer gestartet.

3.  Erweitern Sie im Server-Explorer die Optionen **Data Connections**, **MSTableConnectionString** und **tables**. Klicken Sie mit der rechten Maustaste auf **Updates**, und klicken Sie dann auf **Show Table Data**.

    Die neuen Tweets werden als Zeilen in die Datentabelle eingetragen.

Veröffentlichen des Diensts und Registrieren des neuen Auftrags
---------------------------------------------------------------

Der Auftrag muss auf der Registerkarte **Scheduler** registriert werden, sodass Mobile Services ihn nach dem von Ihnen definierten Zeitplan ausführen kann.

1.  Veröffentlichen Sie das Projekt des mobilen Diensts erneut in Azure.

2.  Klicken Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) auf "Mobile Services" und anschließend auf Ihre App.

    ![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png)

3.  Klicken Sie auf der Registerkarte **Scheduler** auf **+Create**.

	![][3]

    > [WACOM.NOTE] Wenn Sie Ihren mobilen Dienst auf der Ebene *Free* ausführen, können Sie lediglich einen geplanten Auftrag zur Zeit ausführen. Bei bezahlten Ebenen können Sie bis zu zehn geplante Aufträge zur Zeit ausführen.

4.  Geben Sie im Scheduler-Dialog (Auftragsplanerdialog) den Eintrag *SampleJob* für **Auftragsname** ein. Stellen Sie die Planintervalle und -einheiten ein, und klicken Sie anschließend auf den Häkchenknopf.

	![][4]

	Dadurch wird ein neuer Auftrag namens **SampleJob** erstellt. 

5.  Klicken Sie auf den gerade erstellten neuen Auftrag und danach auf **Einmal ausführen**, um das Skript zu testen.

	![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png) 

	Dadurch wird der Auftrag ausgeführt, während er im Scheduler deaktiviert bleibt. Sie können von dieser Seite aus den Auftrag aktivieren und dessen Zeitplan jederzeit ändern.

    >[WACOM.NOTE]Eine POST-Anforderung kann weiterhin verwendet werden, um den geplanten Auftrag zu starten. Die Autorisierung ist jedoch für den Benutzer voreingestellt. Dies bedeutet, dass die Anforderung den Anwendungsschlüssel im Header enthalten muss.

1.  (Optional) Klicken Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com/) auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![](./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png)

2.  Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage ist ähnlich wie die folgende Abfrage. Allerdings wird dabei der Name Ihres mobilen Diensts als Schemabezeichnung anstelle der `todolist` verwendet.

         SELECT * FROM [todolist].[Updates]

Glückwunsch! Sie haben erfolgreich einen neuen geplanten Auftrag in Ihrem mobilen Dienst erstellt. Dieser Auftrag wird wie geplant ausgeführt, bis Sie ihn deaktivieren oder modifizieren.


<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Download and install the LINQ to Twitter library]: #install-linq2twitter
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Test the scheduled job locally]: #run-job-locally
[Publish the service and register the job]: #register-job
[Next steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/add-linq2twitter-nuget-package.png
[2]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-services-selection.png
[3]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
[4]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/create-new-job.png
[5]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/sample-job-run-once.png
[6]: ./media/mobile-services-dotnet-backend-schedule-recurring-tasks/manage-sql-azure-database.png

<!-- URLs. -->
[Azure Management Portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /de-de/documentation/articles/mobile-services-how-to-register-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/de-de/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
[LINQ to Twitter CodePlex project]: http://linqtotwitter.codeplex.com/