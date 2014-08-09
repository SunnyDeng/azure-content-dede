<properties linkid="develop-mobile-tutorials-schedule-backend-tasks" urlDisplayName="Schedule Backend Tasks" pageTitle="Schedule Backend Tasks with Scheduler - Mobile Services" metaKeywords="" description="Use the Azure Mobile Services Scheduler to schedule jobs for your mobile app." metaCanonical="" services="" documentationCenter="Mobile" title="Schedule recurring jobs in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Planen von periodischen Aufträgen in Mobile Services
====================================================

In diesem Thema erfahren Sie, wie Sie die Auftragsplanerfunktion im Verwaltungsportal verwenden, um Serverskript-Code zu definieren, der auf der Grundlage eines von Ihnen festgelegten Plans ausgeführt wird. Im vorliegenden Fall führt das Skript einen periodischen Abgleich mit einem Remote-Dienst, hier Twitter, aus und speichert die Ergebnisse in einer neuen Tabelle. Im Folgenden sind einige weitere periodische Aufgaben aufgeführt, die geplant werden können:

-   Archivieren von veralteten oder doppelten Datensätzen.
-   Anfordern und Speichern von externen Daten, wie etwa Tweets, RSS-Einträgen oder Standortinformationen.
-   Verarbeiten oder Anpassen der Größe von gespeicherten Images.

In diesem Lernprogramm werden die Schritte bezüglich der Verwendung des Auftragsplaners behandelt, um mit diesem einen geplanten Auftrag zu erstellen, welcher Tweet-Daten von Twitter anfordert und die Tweets in einer neuen Aktualisierungstabelle speichert:

-   [Registrieren für Twitter-Zugang und Speichern der Anmeldeinformationen](#get-oauth-credentials)
-   [Erstellen der neuen Aktualisierungstabelle](#create-table)
-   [Erstellen eines neuen geplanten Auftrags](#add-job)

Registrieren für Zugang zu Twitter v1.1 APIs und Speichern von Anmeldeinformationen
-----------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-register-twitter-access](../includes/mobile-services-register-twitter-access.md)]

Erstellen der neuen Aktualisierungstabelle
------------------------------------------

Als Nächstes müssen Sie eine neue Tabelle erstellen, in der Tweets gespeichert werden.

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** für Ihren mobilen Dienst und anschließend auf **+Create**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-data-tab-empty-cli.png)

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

2.  Geben Sie unter **Tabellenname** den Namen *Updates* ein, und klicken Sie auf den Häkchenknopf.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-updates-table.png)

  	Dadurch wird eine neue Speichertabelle **Updates** erstellt.

Erstellen eines neuen geplanten Auftrags
----------------------------------------

Sie können nun den geplanten Auftrag erstellen, der auf Twitter zugreift und Tweet-Daten in der neuen Aktualisierungstabelle speichert.

1.  Klicken Sie auf der Registerkarte **Scheduler** auf **+Create**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-new-job-cli.png)

    >[WACOM.NOTE]Wenn Sie Ihren mobilen Dienst auf der Ebene *Free* ausführen, können Sie lediglich einen geplanten Auftrag zur Zeit ausführen. Bei bezahlten Ebenen können Sie bis zu zehn geplante Aufträge zur Zeit ausführen.

2.  Geben Sie im Scheduler-Dialog (Auftragsplanerdialog) den Eintrag *getUpdates* für **Auftragsname** ein. Stellen Sie die Planintervalle und -einheiten ein, und klicken Sie anschließend auf den Häkchenknopf.
   
   	![](./media/mobile-services-schedule-recurring-tasks/mobile-create-job-dialog.png)

   	Dadurch wird ein neuer Auftrag namens **getUpdates** erstellt. 

3.  Klicken Sie auf den gerade erstellten neuen Auftrag und danach auf die Registerkarte **Skript**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script-new.png) 

4.  Ersetzen Sie die Platzhalterfunktion **getUpdates** durch den folgenden Code:

         var updatesTable = tables.getTable('Updates');
         var request = require('request');
         var twitterUrl = "https://api.twitter.com/1.1/search/tweets.json
         q=%23mobileservices&result_type=recent";

         // Abrufen des Dienstkonfigurationsmoduls.
         var config = require('mobileservice-config');
            
         // Abrufen des gespeicherten Twitter Consumer Keys und des geheimen Schlüssels. 
         var consumerKey = config.twitterConsumerKey,
             consumerSecret = config.twitterConsumerSecret
         // Abrufen des Twitter-Zugriffstokens aus den App-Einstellungen.    
         var accessToken= config.appSettings.TWITTER_ACCESS_TOKEN,
             accessTokenSecret = config.appSettings.TWITTER_ACCESS_TOKEN_SECRET;
            
         function getUpdates() {   
             // Überprüfen, welches der letzte Tweet war, der beim letzten Ausführen des Auftrags gespeichert wurde
             // und Twitter auffordern, ausschließlich neuere Tweets zu übermitteln
             appendLastTweetId(
                 twitterUrl, 
                 function twitterUrlReady(url){            
                     // Erstellen einer neuen Anforderung mit OAuth-Anmeldeinformationen.
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
         // Ermitteln der größten (neuesten) Tweet-ID, die bereits gespeichert wurde
         // (falls Speicherungen vorliegen) außerdem Twitter auffordern, ausschließlich weitere zurückzugeben,
         // d. h. aktuelle
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
             // Entfernen von Retweets und Antworten
             return (tweet.text.indexOf('RT') === 0 || tweet.to_user_id);
         }


   	Dieses Skript ruft die Twitter-Abfrage-API mit den gespeicherten Anmeldeinformationen auf, um aktuelle Tweets anzufordern, die das Hashtag `#mobileservices` enthalten. Doppelte Tweets und Antworten werden aus den Ergebnissen entfernt, bevor sie in der Tabelle gespeichert werden.

    > [WACOM.NOTE] In diesem Beispiel wird davon ausgegangen, dass während eines jeden geplanten Laufs nur einige Zeilen in die Tabelle eingefügt werden. In jenen Fällen, bei denen eine Vielzahl von Zeilen in eine Schleife eingefügt werden, können Sie bei der Ausführung auf der Free-Ebene eventuell einen Mangel an Verbindungen feststellen. In diesem Fall sollten Sie Einfügungen in Stapeln vornehmen. Weitere Informationen finden Sie unter [Gewusst wie: Ausführen von Masseneinfügungen](/de-de/develop/mobile/how-to-guides/work-with-server-scripts/#bulk-inserts).

5.  Klicken Sie auf **Einmal ausführen**, um das Skript zu testen.

  	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-script.png)

   	Dadurch wird der Auftrag gespeichert und ausgeführt, während er im Scheduler deaktiviert bleibt.

1.  Klicken Sie auf die Zurück-Schaltfläche und dann auf **Daten**. Klicken Sie danach auf die Tabelle **Updates** und anschließend auf **Durchsuchen**. Überprüfen Sie im Anschluss, ob Twitter-Daten in die Tabelle eingefügt wurden.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-browse-updates-table.png)

2.  Klicken Sie auf die Zurück-Schaltfläche und danach auf **Scheduler**. Wählen Sie **getUpdates** aus, und klicken Sie dann auf **Enable**.

   	![](./media/mobile-services-schedule-recurring-tasks/mobile-schedule-job-enabled.png)

   	So wird der Auftrag dahingehend aktiviert, dass er nach einem vorgegebenen Zeitplan ausgeführt wird, im vorliegenden Fall jede Stunde.

Glückwunsch! Sie haben erfolgreich einen neuen geplanten Auftrag in Ihrem mobilen Dienst erstellt. Dieser Auftrag wird wie geplant ausgeführt, bis Sie ihn deaktivieren oder modifizieren.

Nächste Schritte
----------------

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

