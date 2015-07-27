<properties 
	pageTitle="Planen von Back-End-Aufgaben mit der Scheduler - Mobile Services" 
	description="Verwenden Sie den Azure Mobile Services-Planer zum Planen von Aufträgen für Ihre mobile Anwendung." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Planen von periodischen Aufträgen in Mobile Services 

> [AZURE.SELECTOR-LIST (Platform | Backend)]
- [(Any | .NET)](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
- [(Any | Javascript)](mobile-services-schedule-recurring-tasks.md)
 
In diesem Thema erfahren Sie, wie Sie die Auftragsplanerfunktion im Verwaltungsportal verwenden, um Serverskript-Code zu definieren, der auf der Grundlage eines von Ihnen festgelegten Plans ausgeführt wird. Im vorliegenden Fall führt das Skript einen periodischen Abgleich mit einem Remote-Dienst, hier Twitter, aus und speichert die Ergebnisse in einer neuen Tabelle. Im Folgenden sind einige weitere periodische Aufgaben aufgeführt, die geplant werden können:

+ Archivieren von veralteten oder doppelten Datensätzen.
+ Anfordern und Speichern von externen Daten, wie etwa Tweets, RSS-Einträgen oder Standortinformationen.
+ Verarbeiten oder Anpassen der Größe von gespeicherten Images.

In diesem Lernprogramm wird die Verwendung des Auftragsplaners erläutert, um mit diesem einen geplanten Auftrag zu erstellen, welcher Tweet-Daten von Twitter anfordert und die Tweets in einer neuen Aktualisierungstabelle speichert.

##<a name="get-oauth-credentials"></a>Registrieren für Zugriff auf Twitter v1.1-APIs und Speichern von Anmeldeinformationen

[AZURE.INCLUDE [mobile-services-register-twitter-access](../../includes/mobile-services-register-twitter-access.md)]

##<a name="create-table"></a>Erstellen der neuen Updates-Tabelle

Als Nächstes müssen Sie eine neue Tabelle erstellen, in der Tweets gespeichert werden.

2. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** für Ihren mobilen Dienst und anschließend auf **+Create**.

3. Geben Sie unter **Tabellenname** den Namen _Updates_ ein, und klicken Sie auf den Häkchenknopf.

##<a name="add-job"></a>Erstellen eines neuen geplanten Auftrags  

Sie können nun den geplanten Auftrag erstellen, der auf Twitter zugreift und Tweet-Daten in der neuen Aktualisierungstabelle speichert.

2. Klicken Sie auf der Registerkarte **Scheduler** auf **+Create**. 

    >[AZURE.NOTE]Wenn Sie Ihren mobilen Dienst auf der Ebene <em>Free</em> ausführen, können Sie zu einem gegebenen Zeitpunkt jeweils nur einen geplanten Auftrag ausführen. Bei bezahlten Ebenen können Sie bis zu zehn geplante Aufträge zur Zeit ausführen.

3. Geben Sie im Scheduler-Dialog (Auftragsplanerdialog) den Eintrag _getUpdates_ für **Auftragsname** ein. Stellen Sie die Planintervalle und -einheiten ein, und klicken Sie anschließend auf den Häkchenknopf.

   	Dadurch wird ein neuer Auftrag mit dem Namen **getUpdates** erstellt.

4. Klicken Sie auf den gerade erstellten neuen Auftrag und danach auf die Registerkarte **Skript**, und ersetzen Sie die Platzhalterfunktion **getUpdates** durch den folgenden Code:

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

    >[AZURE.NOTE]In diesem Beispiel wird davon ausgegangen, dass während eines jeden geplanten Laufs nur einige Zeilen in die Tabelle eingefügt werden. In jenen Fällen, bei denen eine Vielzahl von Zeilen in eine Schleife eingefügt werden, können Sie bei der Ausführung auf der Free-Ebene eventuell einen Mangel an Verbindungen feststellen. In diesem Fall sollten Sie Einfügungen in Stapeln vornehmen. Weitere Informationen finden Sie unter [Gewusst wie: Ausführen von Masseneinfügungen](mobile-services-how-to-use-server-scripts.md#bulk-inserts).

6. Klicken Sie auf **Einmal ausführen**, um das Skript zu testen.

   	Dadurch wird der Auftrag gespeichert und ausgeführt, während er im Scheduler deaktiviert bleibt.

7. Klicken Sie auf die Zurück-Schaltfläche und dann auf **Daten**. Klicken Sie danach auf die Tabelle **Updates** und anschließend auf **Durchsuchen**. Überprüfen Sie im Anschluss, ob Twitter-Daten in die Tabelle eingefügt wurden.

8. Klicken Sie auf die Zurück-Schaltfläche und danach auf **Scheduler**. Wählen Sie **getUpdates** aus, und klicken Sie dann auf **Enable**.

   	So wird der Auftrag dahingehend aktiviert, dass er nach einem vorgegebenen Zeitplan ausgeführt wird, im vorliegenden Fall jede Stunde.

Glückwunsch! Sie haben erfolgreich einen neuen geplanten Auftrag in Ihrem mobilen Dienst erstellt. Dieser Auftrag wird wie geplant ausgeführt, bis Sie ihn deaktivieren oder modifizieren.

## <a name="nextsteps"> </a>Weitere Informationen

* [Mobile Services: Serverskriptreferenz] <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. -->
[Register for Twitter access and store credentials]: #get-oauth-credentials
[Create the new Updates table]: #create-table
[Create a new scheduled job]: #add-job
[Next steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[WindowsAzure.com]: http://www.windowsazure.com/
[Azure Management Portal]: https://manage.windowsazure.com/
[Register your apps for Twitter login with Mobile Services]: /develop/mobile/how-to-guides/register-for-twitter-authentication
[Twitter Developers]: http://go.microsoft.com/fwlink/p/?LinkId=268300
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=July15_HO3-->