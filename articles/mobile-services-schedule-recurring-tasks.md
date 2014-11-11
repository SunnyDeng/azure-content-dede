<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Planen von periodischen Aufträgen in Mobile Services

<div class="dev-center-tutorial-subselector">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/" title=".NET-Backend">.NET-Backend</a> | <a href="/de-de/documentation/articles/mobile-services-schedule-recurring-tasks/"  title="JavaScript-Backend" class="current">JavaScript-Backend</a>
</div>

In diesem Thema erfahren Sie, wie Sie die Auftragsplanerfunktion im Verwaltungsportal verwenden, um Serverskript-Code zu definieren, der auf der Grundlage eines von Ihnen festgelegten Plans ausgeführt wird. Im vorliegenden Fall führt das Skript einen periodischen Abgleich mit einem Remote-Dienst, hier Twitter, aus und speichert die Ergebnisse in einer neuen Tabelle. Im Folgenden sind einige weitere periodische Aufgaben aufgeführt, die geplant werden können:

-   Archivieren von veralteten oder doppelten Datensätzen.
-   Anfordern und Speichern von externen Daten, wie etwa Tweets, RSS-Einträgen oder Standortinformationen.
-   Verarbeiten oder Anpassen der Größe von gespeicherten Images.

<!-- // Removed because this shortcode b/c it's old and doesn't use the new Twitter v1.1. APIs >[AZURE.VIDEO Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler] --> <!-- // Original video HTML code for reference. <div class="dev-onpage-video-clear clearfix"> <div class="dev-onpage-left-content"> <p> </div> <div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="label">watch the tutorial</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-with-scheduler-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Windows-Store-app-Getting-Started-with-the-Windows-Azure-Mobile-Services-Scheduler" target="_blank" class="dev-onpage-video"><span class="icon">Play Video</span></a> <span class="time">5:22</span></div> </div>-->

In diesem Lernprogramm werden die Schritte bezüglich der Verwendung des Auftragsplaners behandelt, um mit diesem einen geplanten Auftrag zu erstellen, welcher Tweet-Daten von Twitter anfordert und die Tweets in einer neuen Aktualisierungstabelle speichert:

-   [Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen][Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen]
-   [Erstellen der neuen Aktualisierungstabelle][Erstellen der neuen Aktualisierungstabelle]
-   [Erstellen eines neuen geplanten Auftrags][Erstellen eines neuen geplanten Auftrags]

## <a name="get-oauth-credentials"></a>Registrieren für Zugang zu Twitter v1.1 APIs und Speichern von Anmeldeinformationen

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

## <a name="create-table"></a>Erstellen der neuen Aktualisierungstabelle

Als Nächstes müssen Sie eine neue Tabelle erstellen, in der Tweets gespeichert werden.

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** für Ihren mobilen Dienst und anschließend auf **+Create**.

    ![][0]

    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

2.  Geben Sie unter **Tabellenname** den Namen *Updates* ein, und klicken Sie auf den Häkchenknopf.

    ![][1]

    Dadurch wird eine neue Speichertabelle **Updates** erstellt.

## <a name="add-job"></a>Erstellen eines neuen geplanten Auftrags

Sie können nun den geplanten Auftrag erstellen, der auf Twitter zugreift und Tweet-Daten in der neuen Aktualisierungstabelle speichert.

1.  Klicken Sie auf der Registerkarte **Scheduler** auf **+Create**.

    ![][2]

    > [WACOM.NOTE] Wenn Sie Ihren mobilen Dienst auf der Ebene *Free* ausführen, können Sie lediglich einen geplanten Auftrag zur Zeit ausführen. Bei bezahlten Ebenen können Sie bis zu zehn geplante Aufträge zur Zeit ausführen.

2.  Geben Sie im Scheduler-Dialog (Auftragsplanerdialog) den Eintrag *getUpdates* für **Auftragsname** ein. Stellen Sie die Planintervalle und -einheiten ein, und klicken Sie anschließend auf den Häkchenknopf.

    ![][3]

    Dadurch wird ein neuer Auftrag mit dem Namen **getUpdates** erstellt.

3.  Klicken Sie auf den gerade erstellten neuen Auftrag und danach auf die Registerkarte **Skript**.

    ![][4]

4.  Ersetzen Sie die Platzhalterfunktion **getUpdates** durch den folgenden Code:

        var updatesTable = tables.getTable('Updates');
        var request = require('request');
        var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json?q=%23mobileservices&result_type=recent";

        // Get the service configuration module.
        var config = require('mobileservice-config');

        // Get the stored Twitter consumer key and secret. 
        var consumerKey = config.twitterConsumerKey,
            consumerSecret = config.twitterConsumerSecret
        // Get the Twitter access token from app settings.    
        var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
            accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;

        function getUpdates() {   
            // Check what is the last tweet we stored when the job last ran
            // and ask Twitter to only give us more recent tweets
            appendLastTweetId(
                twitterUrl, 
                function twitterUrlReady(url){            
                    // Create a new request with OAuth credentials.
                    request.get({
                        url: url,                
                        oauth: {
                            consumer_key: consumerKey,
                            consumer_secret: consumerSecret,
                            token: accessToken,
                            token_secret: accessTokenSecret
                        }},
                        function (error, response, body) {
                        if (!error && response.statusCode == 200) {
                            var results = JSON.parse(body).statuses;
                            if(results){
                                console.log('Fetched ' + results.length + ' new results from Twitter');                       
                                results.forEach(function (tweet){
                                    if(!filterOutTweet(tweet)){
                                        var update = {
                                            twitterId: tweet.id,
                                            text: tweet.text,
                                            author: tweet.user.screen_name,
                                            date: tweet.created_at
                                        };
                                        updatesTable.insert(update);
                                    }
                                });
                            }            
                        } else { 
                            console.error('Could not contact Twitter');
                        }
                    });

                });
         }
        // Find the largest (most recent) tweet ID we have already stored
        // (if we have stored any) and ask Twitter to only return more
        // recent ones
        function appendLastTweetId(url, callback){
            updatesTable
            .orderByDescending('twitterId')
            .read({success: function readUpdates(updates){
                if(updates.length){
                    callback(url + '&since_id=' + (updates[0].twitterId + 1));           
                } else {
                    callback(url);
                }
            }});
        }

        function filterOutTweet(tweet){
            // Remove retweets and replies
            return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
        }

    Dieses Skript ruft die Twitter-Abfrage-API mit gespeicherten Anmeldeinformationen auf, um aktuelle Tweets anzufordern, die das Hashtag `#mobileservices` enthalten. Doppelte Tweets und Antworten werden aus den Ergebnissen entfernt, bevor sie in der Tabelle gespeichert werden.

    > [WACOM.NOTE] In diesem Beispiel wird davon ausgegangen, dass während eines jeden geplanten Laufs nur einige Zeilen in die Tabelle eingefügt werden. In jenen Fällen, bei denen eine Vielzahl von Zeilen in eine Schleife eingefügt werden, können Sie bei der Ausführung auf der Free-Ebene eventuell einen Mangel an Verbindungen feststellen. In diesem Fall sollten Sie Einfügungen in Stapeln vornehmen. Weitere Informationen finden Sie unter [Gewusst wie: Ausführen von Masseneinfügungen][Gewusst wie: Ausführen von Masseneinfügungen].

5.  Klicken Sie auf **Einmal ausführen**, um das Skript zu testen.

    ![][5]

    Dadurch wird der Auftrag gespeichert und ausgeführt, während er im Scheduler deaktiviert bleibt.

6.  Klicken Sie auf die Zurück-Schaltfläche und dann auf **Daten**. Klicken Sie danach auf die Tabelle **Updates** und anschließend auf **Durchsuchen**. Überprüfen Sie im Anschluss, ob Twitter-Daten in die Tabelle eingefügt wurden.

    ![][6]

7.  Klicken Sie auf die Zurück-Schaltfläche und danach auf **Scheduler**. Wählen Sie **getUpdates** aus, und klicken Sie dann auf **Enable**.

    ![][7]

    So wird der Auftrag dahingehend aktiviert, dass er nach einem vorgegebenen Zeitplan ausgeführt wird, im vorliegenden Fall jede Stunde.

Glückwunsch! Sie haben erfolgreich einen neuen geplanten Auftrag in Ihrem mobilen Dienst erstellt. Dieser Auftrag wird wie geplant ausgeführt, bis Sie ihn deaktivieren oder modifizieren.

## <a name="nextsteps"> </a>Nächste Schritte

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.



  [Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen]: #get-oauth-credentials
  [Erstellen der neuen Aktualisierungstabelle]: #create-table
  [Erstellen eines neuen geplanten Auftrags]: #add-job
  [mobile-services-register-twitter-access]: ../includes/mobile-services-register-twitter-access.md
  [0]: ./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png
  [1]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png
  [2]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png
  [3]: ./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png
  [4]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png
  [Gewusst wie: Ausführen von Masseneinfügungen]: /de-de/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts
  [5]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png
  [6]: ./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png
  [7]: ./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
