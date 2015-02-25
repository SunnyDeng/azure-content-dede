<properties 
	pageTitle="Hinzufügen von Paging für Daten (Xamarin Android) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die Nutzung des Paging zur Steuerung der an Ihre Xamarin Android-App zurückgegebenen Datenmenge aus Mobile Services." 
	services="mobile-services" 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="donnam"/>

# Optimieren von Mobile Services-Abfragen mit Paging

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]


In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Xamarin.Android-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [AZURE.NOTE] Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie mindestens das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten]. 

1. Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] erstellt haben.

2. Klicken Sie auf **Run**, um die App zu starten, geben Sie einen Text in das Textfeld ein, und klicken Sie auf die Schaltfläche **Add**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

4. Ersetzen Sie in der Datei **TodoActivity.cs** die LINQ-Abfrage in der **RefreshItemsFromTableAsync**-Methode durch die folgende Abfrage:

		var list = await todoTable.Where(item => item.Complete == false)
						          .Take(3)
			                      .ToListAsync();

	  Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

5. Erstellen und starten Sie die App. 
   
    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

6. (Optional) Sie können den an den mobilen Dienst gesendeten Anforderungs-URI anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler]. 

   	Beachten Sie, dass die `Take(3)`-Methode im Abfrage-URI in die Abfrageoption `$top=3` übersetzt wurde.

7. Aktualisieren Sie die LINQ-Abfrage in der **RefreshItemsFromTableAsync**-Methode mit der folgenden Abfrage:
            
			var list = await todoTable.Where(item => item.Complete == false)
							          .Skip(3)
				                      .Take(3)
                 				      .ToListAsync();

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die drei darauffolgenden zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    > [AZURE.NOTE] Dieses Lernprogramm übergibt zur Vereinfachung hartcodierte Werte an die **Take**- und **Skip**-Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **includeTotalCount**-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

8. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

   	Beachten Sie, dass die `Skip(3)`-Methode im Abfrage-URI als "$skip=3" übersetzt wurde.

## <a name="next-steps"> </a>Nächste Schritte

Hiermit ist die Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services abgeschlossen. Weitere Informationen zu Mobile Services:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit einem Windows-Konto authentifizieren.
 
* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Verwaltungsportal]: https://manage.windowsazure.com/





<!--HONumber=42-->
