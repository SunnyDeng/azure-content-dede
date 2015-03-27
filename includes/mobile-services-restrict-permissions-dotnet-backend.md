

Standardmäßig sind sämtliche Anfragen an die Ressourcen des mobilen Diensts auf Clients beschränkt, die den Anwendungsschlüssel vorweisen. Der sichere Zugriff auf die Ressourcen ist dadurch allerdings nicht gegeben. Um die Ressourcen zu schützen, müssen Sie den Zugriff auf authentifizierte Clients einschränken.

1. Öffnen Sie in Visual Studio Ihr Projekt für mobile Dienste, erweitern Sie den Ordner "Controllers", und öffnen Sie **TodoItemController.cs**. Die Klasse **TodoItemController** implementiert den Datenzugriff für die Tabelle "TodoItem". Fügen Sie die folgende `using`-Anweisung ein:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Übernehmen Sie das folgende "AuthorizeLevel"-Attribut für die Klasse **TodoItemController**: Dadurch wird sichergestellt, dass alle Vorgänge für die Tabelle "TodoItem" einen authentifizierten Benutzer erfordern.

		[AuthorizeLevel(AuthorizationLevel.User)]

	>[AZURE.NOTE]Wenden Sie das Attribut "AuthorizeLevel" für einzelne Methoden an, um bestimmte Berechtigungsebenen für die Methoden festzulegen, die vom Controller eingeblendet werden.

3. Wenn Sie die Authentifizierung lokal debuggen möchten, erweitern Sie den Ordner `App_Start`, öffnen Sie die Projektdatei **WebApiConfig.cs**, und fügen Sie anschließend den folgenden Code der Methode **Registrieren** hinzu:  

		config.SetIsHosted(true);

	Daraufhin wird das lokale mobile Dienstprojekt so ausgeführt, als ob dieses in Azure gehostet wird. Die *AuthorizeLevel*-Einstellungen werden dabei auch berücksichtigt. Ohne diese Einstellung sind sämtliche HTTP-Anforderungen an "localhost" ohne Authentifizierung trotz der *AuthorizeLevel*-Einstellung zulässig. 

4. Veröffentlichen Sie das Projekt erneut.

<!--HONumber=47-->
