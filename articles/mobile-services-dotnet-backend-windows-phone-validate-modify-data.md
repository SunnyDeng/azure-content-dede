<properties linkid="develop-mobile-tutorials-dotnet-backend-validate-modify-and-augment-data-wp8" urlDisplayName="Validate and Modify Data" pageTitle="Use the .Net backend to validate and modify data (Windows Phone 8) | Mobile Dev Center" metaKeywords="" description="Learn how to validate, modify, and augment data for your Windows Phone app with .Net backend Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using the .Net backend" authors="wesmc" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Prüfen und Ändern von Daten in Mobile Services mit .Net-Backend.

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-validate-modify-data/" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-validate-modify-data/" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title="Windows Phone" class="current">Windows Phone</a>
<a href="/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-ios" title="iOS">iOS</a>
<a href="/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-android" title="Android">Android</a>
<a href="/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html" title="HTML">HTML</a><a href="/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a>
<a href="/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-validate-modify-data/" title=".NET-Back-End" class="current">.NET-Back-End</a> | 
    <a href="/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8"  title="JavaScript-Back-End">JavaScript-Back-End</a>
</div>

In diesem Thema erfahren Sie, wie Sie Code im Azure Mobile Services .NET-Back-End verwenden, um Daten zu prüfen und zu ändern. Der .Net-Backend-Dienst stellt einen HTTP-Dienst dar, der mit dem Web-API-Framework erstellt wird. Wenn Sie mit der durch das Web-API-Framework definierten `ApiController`-Klasse vertraut sind, werden Sie die von Mobile Services bereitgestellte `TableController`-Klasse intuitiv verstehen. `TableController` wird von der `ApiController`-Klasse abgeleitet und bietet zusätzliche Funktionen für eine Verknüpfung mit einer Datenbanktabelle. Sie kann verwendet werden, um Vorgänge in Bezug auf einzufügende und zu aktualisierende Daten auszuführen, einschließlich Überprüfen und Ändern von Daten, was in diesem Lernprogramm demonstriert wird.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen][Überprüfung der Zeichenfolgenlänge hinzufügen]
2.  [Client zur Unterstützung der Überprüfung aktualisieren][Client zur Unterstützung der Überprüfung aktualisieren]
3.  [Testlängenüberprüfung][Testlängenüberprüfung]
4.  [Einen Zeitstempel für CompleteDate hinzufügen][Einen Zeitstempel für CompleteDate hinzufügen]
5.  [Client zum Anzeigen von CompleteDate aktualisieren][Client zum Anzeigen von CompleteDate aktualisieren]

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte][Erste Schritte] oder [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte][Erste Schritte] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen.

## <a name="string-length-validation"></a>Überprüfung hinzufügen

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-validation](../includes/mobile-services-dotnet-backend-add-validation.md)]

## <a name="update-client-validation"></a>Den Client aktualisieren

Der mobile Dienst ist nun eingerichtet, um bei einer ungültigen Textlänge die Daten zu überprüfen und Fehlerantworten zu senden. Sie müssen nun Ihre App aktualisieren, damit diese Fehlerantworten aus der Überprüfung verarbeiten kann. Der Fehler wird als `MobileServiceInvalidOperationException` vom `IMobileServiceTable<TodoItem].InsertAsync()`-Aufruf der Client-App aufgefangen.

1.  Navigieren Sie in Visual Studio im Fenster des Projektmappen-Explorers zum Clientprojekt, und öffnen Sie die Datei "MainPage.xaml.cs". Fügen Sie der Datei die folgende using-Anweisung hinzu.

        using Newtonsoft.Json.Linq;

2.  Ersetzen Sie in der "MainPage.xaml.cs" die vorhandene Methode **InsertTodoItem** durch den folgenden Code:

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

    Diese Version der Methode umfasst eine Fehlerbehandlung für **MobileServiceInvalidOperationException**, bei der die deserialisierte Fehlermeldung aus dem Inhalt der Antwort in einem Meldungsfeld angezeigt wird.

## <a name="test-length-validation"></a>Testlängenüberprüfung

1.  Konfigurieren Sie in Visual Studio das gewünschte Windows Phone-Bereitstellungsziel. Klicken Sie danach im Fenster des Projektmappen-Explorers mit der rechten Maustaste auf das Client-App-Projekt, und klicken Sie dann auf **Debug** und **Start new instance**.

2.  Geben Sie den Text für einen neuen todo-Eintrag mit einer Länge von mehr als 10 Zeichen ein, und klicken Sie dann auf **Save**.

    ![][0]

3.  Als Antwort auf den ungültigen Text erhalten Sie einen Meldungsdialog ähnlich dem folgenden.

    ![][1]

## <a name="add-timestamp"></a>Ein Zeitstempelfeld für CompleteDate hinzufügen

[WACOM.INCLUDE [mobile-services-dotnet-backend-add-completedate](../includes/mobile-services-dotnet-backend-add-completedate.md)]

## <a name="update-client-timestamp"></a>Client zum Anzeigen von CompleteDate aktualisieren

Abschließend muss der Client aktualisiert werden, um die neuen **CompleteDate**-Daten anzuzeigen.

1.  Öffnen Sie im Projektmappen-Explorer für Visual Studio im todolist-Clientprojekt die Datei "MainPage.xaml", und ersetzen Sie das Element **StackPanel** durch die unten angegebene Definition. Speichern Sie anschließend die Datei. Dadurch wird der Ereignishandler für **CheckBoxComplete** geändert, sodass das `click`-Ereignis behandelt wird. Außerdem wird ein Textblock neben dem Kontrollkästchen hinzugefügt und mit dem abgeschlossenen Datumszeitstempel verknüpft.

        <StackPanel Orientation="Horizontal">
          <CheckBox Name="CheckBoxComplete" IsChecked="{Binding Complete, Mode=TwoWay}"
            Click="CheckBoxComplete_Clicked" Content="{Binding Text}" Margin="10,5" 
            VerticalAlignment="Center"/>
          <TextBlock Name="textCompleteDate" Text="{Binding CompleteDate}" 
            VerticalAlignment="Center" />
        </StackPanel>

2.  Öffnen Sie im Projektmappen-Explorer für Visual Studio im todolist-Clientprojekt die Datei "MainPage.xaml.cs", und ersetzen Sie den `CheckBoxComplete_Checked`-Ereignishandler durch den nachfolgenden `CheckBoxComplete_Clicked`-Ereignishandler. Damit kann nach Abschluss des Eintrags das vollständige Datum eingesehen werden.

        private void CheckBoxComplete_Clicked(object sender, RoutedEventArgs e)
        {
            CheckBox cb = (CheckBox)sender;
            TodoItem item = cb.DataContext as TodoItem;
            UpdateCheckedTodoItem(item);
        }

3.  Ersetzen Sie dann in der Datei "MainPage.xaml.cs" die vorhandene **TodoItem**-Klasse durch die folgende Definition, welche die neue **CompleteDate**-Eigenschaft als nullbaren Typ beinhaltet.

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

    > [WACOM.NOTE] `DataMemberAttribute` weist den Client an, die neue `CompleteDate`-Eigenschaft in der App der `CompleteDate`-Spalte zuzuordnen, die in der TodoItem-Tabelle definiert ist. Durch die Verwendung dieses Attributs kann Ihre App Eigenschaftennamen zu Objekten haben, die von den Spaltennamen in der SQL-Datenbank abweichen.

4.  Nehmen Sie in der "MainPage.xaml.cs" eine Entfernung oder Auskommentierung der `.Where`-Klauselfunktion in der vorhandenen **RefreshTodoItems**-Methode vor, sodass abgeschlossene "todoitems" in die Ergebnisse einbezogen werden.

            // This query filters out completed TodoItems and 
            // items without a timestamp. 
            items = await todoTable
               //.Where(todoItem => todoItem.Complete == false)
               .ToCollectionAsync();

5.  Aktualisieren Sie in der "MainPage.xaml.cs" die **UpdateCheckedTodoItem**-Methode wie folgt, sodass die Einträge nach einem Update aktualisiert sind und abgeschlossene Einträge nicht aus der Liste entfernt werden. Speichern Sie anschließend die Datei.

        private async void UpdateCheckedTodoItem(TodoItem item)
        {
            // This code takes a freshly completed TodoItem and updates the database.
            await todoTable.UpdateAsync(item);
            //items.Remove(item);
            RefreshTodoItems();
        }

6.  Klicken Sie in den Fenstern des Projektmappen-Explorers von Visual Studio mit der rechten Maustaste auf **Solution**. Klicken Sie dann auf **Rebuild Solution**, um sowohl den Client als auch den .NET-Backend-Dienst neu zu erstellen. Stellen Sie sicher, dass beide Projekte ohne Fehler erstellt werden.

7.  Drücken Sie die Taste **F5**, um die Client-App und den Dienst lokal auszuführen. Fügen Sie einige neue Einträge hinzu, und klicken Sie auf diese, um einige der Einträge als abgeschlossen zu markieren, damit der **CompleteDate**-Zeitstempel als aktualisiert angezeigt werden kann.

8.  Klicken Sie im Projektmappen-Explorer für Visual Studio mit der rechten Maustaste auf das todolist-Dienstprojekt, und klicken Sie auf **Veröffentlichen**. Veröffentlichen Sie Ihren .NET-Backend-Dienst unter Microsoft Azure mithilfe Ihrer Veröffentlichungseinstellungsdatei, die Sie vom Azure-Portal heruntergeladen haben.

9.  Aktualisieren Sie die Datei "App.xaml.cs" für das Clientprojekt, indem Sie die Verbindung zur Adresse des mobilen Diensts auskommentieren. Testen Sie die App bezüglich des .NET-Backend, das in Ihrem Azure-Konto gehostet wird.

## <a name="next-steps"> </a>Nächste Schritte

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging][Optimieren von Abfragen mit Paging].

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Dienstweite Autorisierung von Benutzern][Dienstweite Autorisierung von Benutzern]
    Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.



  [Überprüfung der Zeichenfolgenlänge hinzufügen]: #string-length-validation
  [Client zur Unterstützung der Überprüfung aktualisieren]: #update-client-validation
  [Testlängenüberprüfung]: #test-length-validation
  [Einen Zeitstempel für CompleteDate hinzufügen]: #add-timestamp
  [Client zum Anzeigen von CompleteDate aktualisieren]: #update-client-timestamp
  [Erste Schritte]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
  [mobile-services-dotnet-backend-add-validation]: ../includes/mobile-services-dotnet-backend-add-validation.md
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length.png
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-validate-modify-data/mobile-services-invalid-text-length-exception-dialog.png
  [mobile-services-dotnet-backend-add-completedate]: ../includes/mobile-services-dotnet-backend-add-completedate.md
  [Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
  [Dienstweite Autorisierung von Benutzern]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
