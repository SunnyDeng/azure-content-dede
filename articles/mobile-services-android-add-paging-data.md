<properties pageTitle="Hinzufügen von Paging für Daten (Android) | Mobile Dev Center" description="Erfahren Sie, wie Sie mithilfe des Auslagern die Menge der von Ihrer Android-App von Mobile Services zurückgegebenen Daten verwalten können." services="mobile-services" documentationCenter="android" authors="RickSaling" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="09/25/2014" ms.author="ricksal"/>

# Verfeinern von Mobile Services-Abfragen mit Paging

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Android-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **top** und **skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [AZURE.NOTE] Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten]. 

1. Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] erstellt haben.

2. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten, geben Sie einen Text in das Textfeld ein, und klicken Sie auf **Hinzufügen**.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält. 

4. Ersetzen Sie in ToDoActivity.java die Methode **RefreshTodoItems** durch den folgenden Code:

		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

  	Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

5. Erstellen und starten Sie die App. 
   Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden. 

6. (Optional) Sie können die URI der Anfrage an den mobilen Dienst anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler]. 

   	Beachten Sie, dass die  `top(3)`-Methode im Abfrage-URI als "$top=3" übersetzt wurde.

7. Aktualisieren Sie die **RefreshTodoItems**-Methode noch einmal mit dem folgenden Code:        

		private void refreshItemsFromTable() {
			// Define a filtered query that returns the top 3 items.
			mToDoTable.where().field("complete").eq(false).skip(3).top(3)
					.execute(new TableQueryCallback<ToDoItem>() {
	
						public void onCompleted(List<ToDoItem> result, int count,
								Exception exception, ServiceFilterResponse response) {
							if (exception == null) {
								mAdapter.clear();
	
								for (ToDoItem item : result) {
									mAdapter.add(item);
								}
	
							} else {
								createAndShowDialog(exception, "Error");
							}
						}
					});
		}

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.
> [AZURE.NOTE] Dieses Lernprogramm übergibt zur Vereinfachung fest codierte Werte an die **top**- und **skip**-Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **includeInlineCount**-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.
8. (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an. 

   	Beachten Sie, dass die  `skip(3)`-Methode im Abfrage-URI als "$skip=3" übersetzt wurde.

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogrammreihe über die Grundlagen des Arbeitens mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit einem Windows-Konto authentifizieren.
 
* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android

[Verwaltungsportal]: https://manage.windowsazure.com/



<!--HONumber=42-->
