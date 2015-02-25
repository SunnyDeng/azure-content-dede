<properties 
	pageTitle="Verwenden von Serverskripts zum Prüfen und Ändern von Daten (Xamarin Android) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Daten überprüfen und ändern, die mithilfe von Serverskripts von Ihrer Xamarin.Android-App gesendet wurden." 
	documentationCenter="xamarin" 
	services="mobile-services" 
	authors="lindydonna" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/26/2014" 
	ms.author="donnam"/>

# Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts

[AZURE.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Android-App aktualisieren, um von diesen neuen Verhalten zu profitieren. Der fertige Code steht in der [ValidateModifyData-Beispiel-App][GitHub] zur Verfügung.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Hinzufügen einer Zeichenfolgenüberprüfung]
2. [Aktualisieren des Clients zur Unterstützung der Überprüfung]
3. [Hinzufügen eines Zeitstempels beim Einfügen]
4. [Aktualisieren des Clients zur Anzeige des Zeitstempels]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst das Lernprogramm [Erste Schritte mit Daten] abschließen.  

## <a name="string-length-validation"></a>Hinzufügen einer Überprüfung

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1. Melden Sie sich am [Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App. 

	![][0]

2. Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

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

    Dieses Skript überprüft die Länge der Eigenschaft **text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Methode **execute** aufgerufen, um das Einfügen abzuschließen.

    > [AZURE.TIP] Sie können ein registriertes Skript auf der Registerkarte **Skript** entfernen, indem Sie auf **Löschen** und anschließend auf **Speichern** klicken.

## <a name="update-client-validation"></a>Aktualisieren des Clients

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Sie müssen nun sicherstellen, dass Ihre App die Fehlerantworten aus der Validierung korrekt behandelt.

1. Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] erstellt haben.

2. Suchen Sie in der Datei "ToDoActivity.cs" die Methode **AddItem**, und ersetzen Sie den Aufruf der CreateAndShowDialog-Methode durch den folgenden Code:

    	var exDetail = ex.InnerException.InnerException as 	
			MobileServiceInvalidOperationException;
    	CreateAndShowDialog(exDetail.Message, "Error");

	Dieser Code zeigt die vom mobilen Dienst zurückgegebene Fehlermeldung an. 

3. Klicken Sie auf **Ausführen**, um die App zu starten, geben Sie einen Text mit mehr als 10 Zeichen in das Textfeld ein, und klicken Sie auf **Hinzufügen**.

  	Beachten Sie, dass der Fehler behandelt und die Fehlermeldung für den Benutzer angezeigt wird.

## <a name="add-timestamp"></a>Hinzufügen eines Zeitstempels

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

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

    > [AZURE.IMPORTANT] Bei der ersten Ausführung dieses Skripts muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services bei der ersten Ausführung automatisch die Spalte **createdAt** in die Tabelle **TodoItem** ein. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App veröffentlicht wird.

2. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten, geben Sie einen Text mit weniger als 10 Zeichen in das Textfeld ein, und klicken Sie auf **Hinzufügen**.

   	Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3. Klicken Sie im Verwaltungsportal in der Tabelle **todoitem** auf die Registerkarte **Durchsuchen**.
   
   	Beachten Sie, dass es hier nun ein Spalte **createdAt** gibt und dass das neu eingefügte Element über einen Zeitstempelwert verfügt.
  
Nun müssen Sie die Android-App aktualisieren, um diese neue Spalte anzuzeigen.

## <a name="update-client-timestamp"></a>Erneutes Aktualisieren des Clients

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1. Fügen Sie den Definitionen der privaten Felder in der Klasse **TodoItem** den folgenden Code hinzu:

        [DataMember(Name = "createdAt")]
        public DateTime? CreatedAt { get; set; }
  
    > [AZURE.NOTE] Die Notierung `DataMember's Name` weist den Client an, die neue Eigenschaft `CreatedAt` in der App der Spalte `createdAt` in der TodoItem-Tabelle zuzuordnen, die einen anderen Namen aufweist. Durch die Verwendung dieser Annotation kann Ihre App Eigenschaftennamen zu Objekten haben, die von den Spaltennamen in der SQL-Datenbank abweichen. Ohne diese Annotation tritt aufgrund der unterschiedlichen Schreibweisen ein Fehler auf.

2. Fügen Sie in der GetView-Methode den folgenden Code im Anschluss an den Code hinzu, der <code>checkBox.Text</code> auf <code>currentItem.Text</code> setzt:

       	string displayDate = "missing";
       	if (currentItem.CreatedAt.HasValue)
       		displayDate = currentItem.CreatedAt.Value.ToShortTimeString();

   	Vorausgesetzt, dass ein Zeitstempelwert vorhanden ist, wird dadurch eine formatierte Datumszeichenfolge erstellt. 

3. Suchen Sie den Code  `checkBox.Text = currentItem.Text` erneut, und ersetzen Sie diese Codezeile durch Folgendes:

		checkBox.Text = string.Format("{0} - {1}", currentItem.Text, displayDate);

	Dieser Code hängt den Zeitstempelwert zur Anzeige an das Element an.
	
4. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. 

	Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

5. Ersetzen Sie in **TodoActivity.cs** die vorhandene Abfrage in **RefreshItemsFromTableAsync** durch die folgende Abfrage:

		var list = await todoTable.Where(item => item.Complete == false && 
										 item.CreatedAt != null)
								  .ToListAsync();

	Diese Methode aktualisiert die Abfrage, damit auch Elemente herausgefiltert werden, die nicht über einen Zeitstempelwert verfügen.
	
6. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

  	Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mittels Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

* [Autorisieren von Benutzern mit Skripts]
  <br/>Erfahren Sie, wie Daten anhand der ID eines authentifizierten Benutzers gefiltert werden können.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

<!-- Anchors. -->
[Hinzufügen einer Zeichenfolgenüberprüfung]: #string-length-validation
[Aktualisieren des Clients zur Unterstützung der Überprüfung]: #update-client-validation
[Hinzufügen eines Zeitstempels beim Einfügen]: #add-timestamp
[Aktualisieren des Clients zur Anzeige des Zeitstempels]: #update-client-timestamp
[Nächste Schritte]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/partner-xamarin-mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png



<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-android
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android

[Verwaltungsportal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331330




<!--HONumber=42-->
