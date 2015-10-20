In diesem Thema wird das Erstellen einer App sowohl mit einem mobilen Client als auch mit einem Webclient veranschaulicht. Sie erstellen eine mobile App und eine Web-App und verwenden für beide dieselbe zugrunde liegende Datenbank.

Zunächst erstellen Sie ein neues mobiles App-Back-End und eine einfache *To-do-List*-App, die App-Daten im neuen mobilen App-Back-End speichert. Das mobile App-Back-End verwendet die unterstützten .NET-Sprachen für die serverseitige Geschäftslogik. Die Client-App kann alle von der mobilen App unterstützten Clientplattformen nutzen, einschließlich iOS, Windows, Xamarin iOS und Xamarin Android.

Anschließend erstellen Sie eine Web-App, die dieselbe Datenbank wie die mobile App verwendet. Am Ende des Lernprogramms verfügen Sie über einen Webclient und einen mobilen Client, die mit denselben Daten arbeiten.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Erstellen eines neuen mobilen App-Back-Ends und -Clients

* Folgen Sie den Schritten im Lernprogramm [Erstellen einer mobilen App] zum Erstellen eines mobilen App-Back-Ends und eines Clients. Sie können alle von den mobilen Apps unterstützten Clientplattformen nutzen, einschließlich iOS, Windows, Xamarin iOS und Xamarin Android.

* Stellen Sie sicher, dass Sie Ihr mobiles App-Back-End auf Azure bereitgestellt haben und dass Ihre mobile Client-App mit dem gehosteten Back-End verbunden ist. Das Codeprojekt für die mobile App verwendet Entity Framework Code First und initialisiert die Datenbank nach der ersten REST-Anforderung von einer mobilen Client-App.

## Veröffentlichen einer TodoList-Web-API in Visual Studio

In diesem Abschnitt erstellen Sie eine neue Web-App mithilfe einer Beispiellösung für die Web-App. Sie bearbeiten das Beispiel für die Verwendung desselben Datenbankschemanamens und derselben Verbindungszeichenfolge wie die mobile App.

>[AZURE.NOTE]Bevor Sie diese Schritte abschließen, stellen Sie sicher, dass die Datenbank des mobilen App-Back-Ends initialisiert wurde, indem Sie von einem Client aus eine Verbindung herstellen. Andernfalls kann die Web-App keine Verbindung zu derselben Datenbank herstellen.

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com/) eine neue Web-App mithilfe derselben Ressourcengruppe und desselben Webhostingplans wie Ihre mobile App.

2. Laden Sie die Beispiellösung [MultiChannelToDo] herunter, und öffnen Sie sie in Visual Studio. Die Lösung enthält ein Web-API-Projekt und ein Web-App-Projekt für die Web-Client-Benutzeroberfläche.

3. Bearbeiten Sie im Web-API-Projekt die Datei "MultiChannelToDoContext.cs". Ändern Sie in `OnModelCreating` den Schemanamen in den Namen Ihrer mobilen App:

        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore

4. Als Nächstes rufen wir die Verbindungszeichenfolge der mobilen App aus dem Azure-Portal ab:

    - Wählen Sie Ihre mobile App im Portal aus, und klicken Sie auf den Abschnitt mit der Bezeichnung **Benutzercode**.

    - Wählen Sie auf dem geöffneten Blatt **Alle Einstellungen** aus und dann **Anwendungseinstellungen**.

    - Klicken Sie unter **Verbindungszeichenfolgen** auf **Verbindungszeichenfolgen anzeigen**. Kopieren Sie den Wert für die Einstellung **MS\_TableConnectionString**. Dies ist die Verbindungszeichenfolge, die von der mobilen App verwendet wird, um eine Verbindung mit der SQL-Datenbank herzustellen.

5. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Web-API-Projekt, und wählen Sie **Veröffentlichen** aus. Wählen Sie als Veröffentlichungsziel **Azure-Web-Apps** aus, und wählen Sie die Web-App aus, die Sie erstellt haben. Klicken Sie auf **Weiter** bis der Bereich **Einstellungen** im Assistenten "Web veröffentlichen" angezeigt wird.

6. Fügen Sie im Abschnitt **Datenbanken** die Verbindungszeichenfolge für die mobile App als Wert für **MultiChannelToDoContext ein**. Aktivieren Sie nur das Kontrollkästchen **Diese Verbindungszeichenfolge zur Laufzeit verwenden**.

7. Sobald Ihre Web-API erfolgreich in Azure veröffentlicht wurde, wird eine Bestätigungsseite angezeigt. Kopieren Sie die URL für den veröffentlichten Dienst.

## Veröffentlichen einer Benutzeroberfläche für einen TodoList-Webclient aus Visual Studio

In diesem Abschnitt verwenden Sie eine Beispiel-Webclient-App, die mit AngularJS implementiert wird. Anschließend veröffentlichen Sie das Projekt mit Visual Studio in einer neuen gehosteten App Service-Web-App in Azure.

1. Öffnen Sie in Visual Studio das Projekt **MultiChannelToDo.Web**. Bearbeiten Sie die Datei `js/service/ToDoService.js`, und fügen Sie die URL der gerade veröffentlichten Web-API hinzu:

        var apiPath = "https://your-web-api-site-name.azurewebsites.net";

2. Klicken Sie mit der rechten Maustaste auf das Projekt **MultiChannelToDo.Web**, und wählen Sie **Veröffentlichen** aus.

3. Wählen Sie im Assistenten **Web veröffentlichen** als Veröffentlichungsziel **Azure-Web-App** aus, und erstellen Sie eine neue Web-App ohne eine Datenbank.

4. Sobald das Projekt erfolgreich veröffentlicht wurde, wird die Webbenutzeroberfläche in Ihrem Browser angezeigt.

## Testen von mobilen und Web-Apps

1. Fügen Sie in der Webbenutzeroberfläche Aufgaben hinzu, oder bearbeiten Sie einige.

    ![Ansicht der Web-App im Browser](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)

2. Führen Sie die im Lernprogramm [Erstellen einer mobilen App] erstellte mobile App aus. Sie sehen dieselben Aufgabenelemente wie in der Web-App.

    ![Ansicht der mobilen Xamarin-App](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[Erstellen einer mobilen App]: ../article/app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

<!---HONumber=Oct15_HO3-->