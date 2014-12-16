<properties urlDisplayName="Validate and Modify Data" pageTitle="Verwenden des .Net-Back-End zum Prüfen und Ändern von Daten (Windows Phone 8) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Phone app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Prüfen und Ändern von Daten in Mobile Services mit .NET-Back-End

[WACOM.INCLUDE [mobile-services-selector-validate-modify-data](../includes/mobile-services-selector-validate-modify-data.md)]

In diesem Thema erfahren Sie, wie Sie Code im Azure Mobile Services .NET-Back-End verwenden, um Daten zu prüfen und zu ändern. Der .NET-Back-End-Dienst ist ein HTTP-Dienst, der mit dem Web-API-Framework erstellt wird. Wenn Sie mit der `ApiController`-Klasse vertraut sind, die mit dem Web-API-Framework definiert wird, so ist die durch Mobile Services bereitgestellte `TableController`-Klasse überwiegend selbsterklärend. `TableController` wird von der `ApiController`-Klasse abgeleitet und bietet zusätzliche Funktionen für eine Verknüpfung über Schnittstellen mit einer Datenbanktabelle. Er kann verwendet werden, um Vorgänge mit einzufügenden und zu aktualisierenden Daten auszuführen, einschließlich zum Überprüfen und Ändern von Daten, was in diesem Lernprogramm demonstriert wird. 

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Überprüfung der Zeichenfolgenlänge hinzufügen]
2. [Client zur Unterstützung der Überprüfung aktualisieren]
3. [Testlängenüberprüfung]
4. [Einen Zeitstempel für CompleteDate hinzufügen]
5. [Client zum Anzeigen von CompleteDate aktualisieren]

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen Anwendung](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/) auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst dieses Lernprogramm abschließen:  

## <a name="string-length-validation"></a>Add validation

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]


## <a name="update-client-validation"></a>Update the client

Der mobile Dienst ist nun eingerichtet, um bei einer ungültigen Textlänge die Daten zu überprüfen und Fehlerantworten zu senden. Sie müssen nun Ihre App aktualisieren, damit diese Fehlerantworten aus der Überprüfung verarbeiten kann. Der Fehler wird als `MobileServiceInvalidOperationException` vom `IMobileServiceTable<TodoItem].InsertAsync()`-Aufruf der Client-App abgefangen.

1. Navigieren Sie in Visual Studio im Fenster des Projektmappen-Explorers zum Clientprojekt, und öffnen Sie die Datei "MainPage.xaml.cs". Fügen Sie der Datei die folgende using-Anweisung hinzu.

        using Newtonsoft.Json.Linq;

2. Ersetzen Sie in der Datei "MainPage.xaml.cs" die vorhandene **InsertTodoItem**-Methode durch den folgenden Code:

        private async void InsertTodoItem(TodoItem todoItem)
        {
            // This code inserts a new TodoItem into the database. When the operation completes
            // and Mobile Services has assigned an Id, the item is added to the CollectionView
            MobileServiceInvalidOperationException invalidOpException = null;
            try
            {
                await todoTable.InsertAsync(todoItem);
                items.Add(todoItem);
            }
            catch (MobileServiceInvalidOperationException e)
            {
                invalidOpException = e;
            }
            if (invalidOpException != null)
            {
                string strJsonContent = await invalidOpException.Response.Content.ReadAsStringAsync();
                var responseContent = JObject.Parse(strJsonContent);
                MessageBox.Show(string.Format("{0} (HTTP {1})",
                    (string)responseContent["message"], (int)invalidOpException.Response.StatusCode),
                    invalidOpException.Message, MessageBoxButton.OK);
            }
        }

   	Diese Version der Methode umfasst eine Fehlerbehebung für **MobileServiceInvalidOperationException**, bei der die deserialisierte Fehlermeldung aus dem Antworteninhalt in einem Meldungsfeld angezeigt wird.

## <a name="test-length-validation"></a>Test Length Validation

1. Konfigurieren Sie in Visual Studio das gewünschte Windows Phone-Bereitstellungsziel. Klicken Sie danach im Fenster des Projektmappen-Explorers mit der rechten Maustaste auf das Client-App-Projekt, und klicken Sie dann auf **Debug**, **Start new instance**.

2. Geben Sie den Text für einen neuen todo-Eintrag mit einer Länge von mehr als 10 Zeichen ein, und klicken Sie dann auf **Speichern**.

    ![][1]

3. Als Antwort auf den ungültigen Text erhalten Sie einen Meldungsdialog ähnlich dem folgenden.

    ![][2]

## <a name="add-timestamp"></a>Add a timestamp field for CompleteDate

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]


## <a name="update-client-timestamp"></a>Client zum Anzeigen von CompleteDate aktualisieren

Abschließend muss der Client aktualisiert werden, um die neuen **CompleteDate**-Daten anzuzeigen. 


1. Öffnen Sie im Projektmappen-Explorer für Visual Studio im todolist-Clientprojekt die Datei "MainPage.xaml", und ersetzen Sie das Element **StackPanel** durch die unten angegebene Definition. Speichern Sie anschließend die Datei. Dadurch wird der Ereignishandler für **CheckBoxComplete** geändert, sodass das `click-Ereignis behandelt wird. Außerdem wird ein Textblock neben dem Kontrollkästchen hinzugefügt und mit dem abgeschlossenen Datumszeitstempel verknüpft.
	      
        <StackPanel Orientation="Horizontal">
          <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}"
            Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" 
            VerticalAlignment="Center"/>
          <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" 
            VerticalAlignment="Center" />
        </StackPanel>


2. Öffnen Sie im Projektmappen-Explorer für Visual Studio im todolist-Clientprojekt die Datei "MainPage.xaml.cs, ersetzen Sie den Ereignishandler `CheckBoxComplete_Checked` durch den Ereignishandler `CheckBoxComplete_Clicked`, der unten angegeben ist. Damit kann nach Abschluss des Eintrags das vollständige Datum angezeigt werden.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }


3. Ersetzen Sie in der Datei "MainPage.xaml.cs" dann die vorhandene **TodoItem**-Klasse durch die folgende Definition, die eine neue **CompleteDate**-Eigenschaft als Nullable-Typ beinhaltet.

        public class TodoItem
        {
            public string Id { get; set; }
            [JsonProperty(PropertyName = "text")]
            public string Text { get; set; }
            [JsonProperty(PropertyName = "complete")]
            public bool Complete { get; set; }        
            [JsonProperty(PropertyName = "CompleteDate")]
            public DateTime? CompleteDate { get; set; }
        }
	
    >[WACOM.NOTE] <code>DataMemberAttribute</code> weist den Client an, die neue <code>CompleteDate</code>-Eigenschaft in der App der <code>CompleteDate</code>-Spalte zuzuordnen, die in der TodoItem-Tabelle definiert ist. Durch die Verwendung dieses Attributs kann die App Eigenschaftennamen für Objekte haben, die von den Spaltennamen in der SQL-Datenbank abweichen.
    

	


4. Nehmen Sie in der Datei "MainPage.xaml.cs" die `.Where`-Klauselfunktion in der vorhandenen **RefreshTodoItems**-Methode, oder kennzeichnen Sie sie als Kommentar, sodass abgeschlossene todoitems in die Ergebnisse einbezogen werden.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();


5. Aktualisieren Sie in der Datei "MainPage.xaml.cs" die **UpdateCheckedTodoItem**-Methode wie folgt, sodass die Einträge nach einem Update aktualisiert werden und abgeschlossene Einträge nicht aus der Liste entfernt werden. Speichern Sie anschließend die Datei.	

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }


6. Klicken Sie in den Fenstern des Projektmappen-Explorers von Visual Studio mit der rechten Maustaste auf die **Projektmappe,**, und klicken Sie dann auf **Projektmappe neu erstellen**, um sowohl den Client als auch den .NET-Back-End-Dienst neu zu erstellen. Vergewissern Sie sich, dass beide Projekte ohne Fehler erstellt werden.


	
7. Drücken Sie die Taste **F5**, um die Client-App und den Dienst lokal auszuführen. Fügen Sie einige neue Einträge hinzu, und klicken Sie auf einige der Einträge, um diese als abgeschlossen zu markieren und festzustellen, ob der aktualisierte **CompleteDate**-Zeitstempel angezeigt wird.


8. Klicken Sie im Projektmappen-Explorer für Visual Studio mit der rechten Maustaste auf das todolist-Dienstprojekt, und klicken Sie auf **Veröffentlichen**. Veröffentlichen Sie den .NET-Back-End-Dienst mithilfe der Veröffentlichungseinstellungsdatei, die Sie vom Azure-Portal heruntergeladen haben, in Microsoft Azure.

9. Aktualisieren Sie die Datei "App.xaml.cs" für das Clientprojekt, indem Sie Kommentarzeichen vor der Verbindung zur Adresse des mobilen Diensts entfernen. Testen Sie die App mit dem in Ihrem Azure-Konto gehosteten .NET Back-End.


## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

* [Dienstseitige Autorisierung von Benutzern]
  <br/>Erfahren Sie, wie Daten anhand der ID eines authentifizierten Benutzers gefiltert werden können.

* [Erste Schritte mit Pushbenachrichtigungen] 
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET

<!-- Anchors. -->
[Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
[Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
[Testlängenüberprüfung]: #test-length-validation
[Einen Zeitstempel für CompleteDate hinzufügen]: #add-timestamp
[Client zum Anzeigen von CompleteDate aktualisieren]: #update-client-timestamp
[Nächste Schritte]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png



<!-- URLs. -->
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Dienstseitige Autorisierung von Benutzern]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js

[Verwaltungsportal]: https://manage.windowsazure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
