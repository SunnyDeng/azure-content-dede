<properties urlDisplayName="" pageTitle="Hinzufügen von Paging zu Daten (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="mobile-services" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="donnam" />


# Verfeinern von Mobile Services-Abfragen mit Paging

[WACOM.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema wird gezeigt, wie Sie die aus Azure Mobile Services an Ihre iOS-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfrageeigenschaften **Skip** und **Take** im Client, um bestimmte "Seiten" von Daten abzufragen.

<div class="dev-callout"><b>Hinweis</b>
<p>Um einen Datenüberlauf auf mobilen Geräteclients zu verhindern, implementiert Mobile Services eine automatische Seitenbegrenzung, die standardmäßig die Antwort auf 50 Elemente begrenzt. Durch Angeben der Seitengröße können Sie explizit bis zu 1.000 Elemente in der Antwort anfordern.</p>
</div>

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten] auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten]. 

1. Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] geändert haben.

2. Klicken Sie auf die Schaltfläche **Run** um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text in das Textfeld ein, und klicken Sie auf das Plus-Symbol (**+**).

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

4. Öffnen Sie die Datei **TodoService.cs**, suchen Sie nach der **RefreshDataAsync**-Methode, und ersetzen Sie die LINQ-Abfrage in der <code>todoTable</code> durch die folgende Abfrage: 

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
        		            .Take(3)
                		    .ToListAsync();

   	Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind. 

5. Erstellen und starten Sie die App. 
   
    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

7. Aktualisieren Sie die LINQ-Abfrage in der **RefreshDataAsync**-Methode noch einmal folgendermaßen:

			Items = await todoTable
							.Where (todoItem => todoItem.Complete == false)
		                    .Skip(3)
		                    .Take(3)
		                    .ToListAsync();

   	Setzen Sie den **Skip**-Wert dieses Mal auf 3. 

Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die drei darauffolgenden zurück. Dabei handelt es sich effektiv um die zweite "Seite" von Daten, die Seitengröße beträgt dabei drei Elemente.

    <div class="dev-callout"><b>Hinweis</b>
    <p>DiesesDieses Lernprogramm verwendet zur Vereinfachung fest codierte Werte für die <strong>Skip</strong>- und <strong>Take</strong>-Eigenschaften. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die <strong>IncludeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl aller Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
    </div>

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services. Weitere Informationen finden Sie im folgenden Mobile Services-Thema:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit Windows-Konto authentifizieren.
 
<!--
* [Get started with push notifications] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.
-->

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: ./de-de/develop/mobile/tutorials/get-started-xamarin-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Verwaltungsportal]: https://manage.windowsazure.com/
