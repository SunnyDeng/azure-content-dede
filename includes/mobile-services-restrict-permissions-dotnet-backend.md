Standardmäßig sind sämtliche Anfragen an die Ressourcen des mobilen Diensts auf Clients beschränkt, die den Anwendungsschlüssel vorweisen. Der sichere Zugriff auf die Ressourcen ist dadurch allerdings nicht gegeben. Um den sicheren Zugriff auf die Ressourcen zu gewährleisten, müssen Sie den Zugriff ausschließlich auf authentifizierte Clients beschränken.

1.  Öffnen Sie in Visual Studio das Projekt, in dem Ihr mobiler Dienst enthalten ist.

2.  Erweitern Sie im Projektmappen-Explorer den Ordner "Controllers", und öffnen Sie die Projektdatei "TodoItemController.cs".

    Die Klasse **TodoItemController** implementiert den Datenzugriff für die Tabelle "TodoItem".

3.  Fügen Sie die folgende `using`-Anweisung am Anfang der Codepage ein:

        using Microsoft.WindowsAzure.Mobile.Service.Security;

4.  Übernehmen Sie das folgende AuthorizeLevel-Attribut für die Klasse **TodoItemController**:

        [AuthorizeLevel(AuthorizationLevel.User)] 

    So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist.

    > [WACOM.NOTE]Verwenden Sie das AuthorizeLevel-Attribut für einzelne Methoden, um bestimmte Autorisierungsstufen für die vom Controller bereitgestellten Methoden festzulegen.

5.  Wenn Sie die Authentifizierung lokal debuggen möchten, erweitern Sie den Ordner "App\_Start", öffnen Sie die Projektdatei "WebApiConfig.cs", und fügen Sie anschließend den folgenden Code der Methode **Registrieren** hinzu:

        config.SetIsHosted(true);

    Daraufhin wird das lokale mobile Dienstprojekt so ausgeführt, als ob dieses in Azure gehostet wird. Die AuthorizeLevel-Einstellungen werden dabei auch berücksichtigt. Ohne diese Einstellung sind sämtliche HTTP-Anforderungen an *localhost* ohne Authentifizierung trotz der AuthorizeLevel-Einstellung zulässig.

6.  Veröffentlichen Sie Ihr Dienstprojekt erneut.


