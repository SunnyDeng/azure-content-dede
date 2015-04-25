<properties 
	pageTitle="Hinzufügen von Paging zu Daten (Xamarin iOS) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie mithilfe des Auslagern die Menge der von Ihrer Xamarin iOS-App von Mobile Services zurückgegebenen Daten verwalten können." 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor="" 
	documentationCenter="xamarin"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>


# Optimieren von Mobile Services-Abfragen mit Paging

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema wird gezeigt, wie Sie die aus Azure Mobile Services an Ihre iOS-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfrageeigenschaften **Skip** und **Take** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [AZURE.NOTE] Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie mindestens das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten]. 

1. Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] geändert haben.

2. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text in das Textfeld ein, und klicken Sie auf das Plus-Symbol (**+**).

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

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die drei darauffolgenden zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    > [AZURE.NOTE] Dieses Lernprogramm verwendet zur Vereinfachung hartcodierte Werte für die **Skip**- und **Take**-Eigenschaften. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **IncludeTotalCount**-Methode aufrufen, um die Gesamtzahl aller Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

## <a name="next-steps"> </a>Nächste Schritte

Hiermit ist die Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services abgeschlossen. Weitere Informationen finden Sie im folgenden Mobile Services-Thema:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit einem Windows-Konto authentifizieren.
 
<!--
* [Erste Schritte mit Pushbenachrichtigungen]
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





<!--HONumber=42-->
