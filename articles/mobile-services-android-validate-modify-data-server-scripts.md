<properties 
	pageTitle="Verwenden von Serverskripts zum Überprüfen und Ändern von Daten (Android) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Daten überprüfen und ändern, die mithilfe von Serverskripts von Ihrer Android-App gesendet wurden." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="ricksal"/>

# Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Android-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Überprüfung der Zeichenfolgenlänge hinzufügen]
2. [Client zur Unterstützung der Überprüfung aktualisieren]
3. [Zeitstempel beim Einfügen hinzufügen]
4. [Client zum Anzeigen des Zeitstempels aktualisieren]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten] abschließen.  

## <a name="string-length-validation"></a>Hinzufügen der Überprüfung

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1. Melden Sie sich beim [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App. 

   	![][0]

2. Klicken Sie auf die Registerkarte **Daten**, dann auf die Tabelle **TodoItem**.

   	![][1]

3. Klicken Sie auf **Skript**, und wählen Sie dann den Vorgang **Einfügen** aus.

   	![][2]

4. Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
            } else {
                request.execute();
            }
        }

    Das Skript überprüft die Länge der Eigenschaft **text** und sendet eine Fehlerantwort, wenn die Länge 10 Zeichen überschreitet. Andernfalls wird die Methode **execute** aufgerufen, um das Einfügen abzuschließen.

   > [AZURE.TIP] Sie können ein registriertes Skript auf der Registerkarte **Skript** entfernen, indem Sie auf **Löschen** und dann auf **Speichern** klicken.

## <a name="update-client-validation"></a>Aktualisieren des Clients

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Sie müssen nun sicherstellen, dass Ihre App die Fehlerantworten aus der Validierung korrekt behandelt.

1. Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] erstellt haben.

2. Suchen Sie in der Datei "ToDoActivity.java" die Methode **addItem**, und ersetzen Sie den Aufruf der createAndShowDialog-Methode durch den folgenden Code:

		createAndShowDialog(exception.getCause().getMessage(), "Error");

	Dieser Code zeigt die vom mobilen Dienst zurückgegebene Fehlermeldung an. 

3. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten, geben Sie einen Text mit mehr als 10 Zeichen in das Textfeld ein, und klicken Sie auf **Hinzufügen**.

  Beachten Sie, dass der Fehler behandelt und die Fehlermeldung für den Benutzer angezeigt wird.

## <a name="add-timestamp"></a>Hinzufügen eines Zeitstempels

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

> [AZURE.NOTE] Die hier demonstrierte Zeitstempeleigenschaft **createdAt** ist nun redundant. Mobile Services erstellt automatisch eine Systemeigenschaft **__createdAt** für jede Tabelle.

1. Ersetzen Sie im [Verwaltungsportal] auf der Registerkarte **Skripts** das aktuelle Skript **Insert** durch die folgende Funktion, und klicken Sie dann auf **Speichern**.

        function insert(item, user, request) {
            if (item.text.length > 10) {
                request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
            } else {
                item.createdAt = new Date();
                request.execute();
            }
        }

    Diese Funktion erweitert das vorherige insert-Skript, indem es eine neue Zeitstempeleigenschaft **createdAt** zum Objekt hinzufügt, bevor es durch den **request**.**execute**-Aufruf eingefügt wird. 

    > [AZURE.IMPORTANT] Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **createdAt** in die Tabelle **TodoItem** ein. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App veröffentlicht wird.

2. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten, geben Sie einen Text mit weniger als 10 Zeichen in das Textfeld ein, und klicken Sie auf **Hinzufügen**.

   	Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3. Klicken Sie im Verwaltungsportal auf die Registerkarte **Durchsuchen** in der Tabelle **todoitem**.
   
   	Beachten Sie, dass es nun ein Spalte **createdAt** gibt und dass das neu eingefügte Element über einen Zeitstempelwert verfügt.
  
Nun müssen Sie die Android-App aktualisieren, um diese neue Spalte anzuzeigen.

## <a name="update-client-timestamp"></a>Erneutes Aktualisieren des Clients

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1. Öffnen Sie die Datei "ToDoItem.java" im Paket-Explorer, und fügen Sie die folgende **import**-Anweisung hinzu:

		import java.util.Date;

2. Fügen Sie den folgenden Code zu den Definitionen der privaten Felder in der Klasse **TodoItem** hinzu:

		/**
		 * Timestamp of the item inserted by the service.
		 */
		@com.google.gson.annotations.SerializedName("createdAt")
		private Date mCreatedAt;
  
    > [AZURE.NOTE] Die `SerializedName`-Annotation weist den Client an, die neue `mCreatedAt`-Eigenschaft in der App der in der TodoItem-Tabelle definierten Spalte `createdAt` zuzuordnen, die einen anderen Namen hat. Durch die Verwendung dieser Annotation kann Ihre App Eigenschaftennamen zu Objekten haben, die von den Spaltennamen in der SQL-Datenbank abweichen. Ohne diese Annotation tritt aufgrund der unterschiedlichen Schreibweisen ein Fehler auf.

2. Fügen Sie die folgenden Methoden zur ToDoItem-Klasse hinzu, um die neue mCreatedAt-Eigenschaft abzurufen und zu ändern:

		/**
		 * Sets the timestamp.
		 * 
		 * @param date
		 *            timestamp to set
		 */
		public final void setCreatedAt(Date date) {
			mCreatedAt = date;
		}
		
		/**
		 * Returns the timestamp.
		 */
		public Date getCreatedAt() {
			return mCreatedAt;
		}

5. Öffnen Sie die Datei "ToDoItemAdapter.java"im Paket-Explorer,und fügen Sie die folgende **import**-Anweisung hinzu:

		import java.text.DateFormat;

6. Fügen Sie in der GetView-Methode den folgenden Code hinzu:

		String createdAtText = "";
		if (currentItem.getCreatedAt() != null){
			DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
			createdAtText = formatter.format(currentItem.getCreatedAt());
		}

   	Vorausgesetzt, dass ein Zeitstempelwert vorhanden ist, wird dadurch eine formatierte Datumszeichenfolge generiert. 

7. Suchen Sie den Code `checkBox.setText(currentItem.getText());`, und ersetzen Sie die Codezeile durch folgenden Code:

		checkBox.setText(currentItem.getText() + " " + createdAtText);

	Dieser Code hängt den Zeitstempelwert zur Anzeige an das Element an.
	
6. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. 

   	Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

7. Ersetzen Sie die vorhandene Methode **RefreshItemsFromTable** durch den folgenden Code:

		private void refreshItemsFromTable() {
			
			mToDoTable.where().field("complete").eq(false).and().field("createdAt").ne((String)null)
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

   	Diese Methode aktualisiert die Abfrage, damit auch Elemente herausgefiltert werden, die nicht über einen Zeitstempelwert verfügen.
	
8. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

   	Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

* [Autorisieren von Benutzern mit Skripts]
  <br/>Erfahren Sie, wie Daten anhand der ID eines authentifizierten Benutzers gefiltert werden können.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. -->
[Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
[Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
[Zeitstempel beim Einfügen hinzufügen]: #add-timestamp
[Client zum Anzeigen des Zeitstempels aktualisieren]: #update-client-timestamp
[Nächste Schritte]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-android
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-android
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android

[Verwaltungsportal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/



<!--HONumber=42-->
