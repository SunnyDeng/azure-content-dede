

Standardmäßig sind sämtliche Anfragen an die Ressourcen des mobilen Diensts auf Clients beschränkt, die den Anwendungsschlüssel vorweisen. Der sichere Zugriff auf die Ressourcen ist dadurch allerdings nicht gegeben. Um die Ressourcen zu schützen, müssen Sie den Zugriff auf authentifizierte Clients einschränken.

1. Öffnen Sie in Visual Studio Ihr Projekt für mobile Dienste, erweitern Sie den Ordner "Controllers", und öffnen Sie **TodoItemController.cs**. Die Klasse **TodoItemController** implementiert den Datenzugriff für die Tabelle "TodoItem". Fügen Sie die folgende `using`-Anweisung hinzu:

		using Microsoft.WindowsAzure.Mobile.Service.Security;

2. Übernehmen Sie das folgende _AuthorizeLevel_-Attribut für die Klasse **TodoItemController**:

		[AuthorizeLevel(AuthorizationLevel.User)]

	So wird sichergestellt, dass für alle Vorgänge für die Tabelle _TodoItem_ eine Benutzerauthentifizierung erforderlich ist. Sie können auch das *AuthorizeLevel*-Attribut auf Methodenebene anwenden.

3. (Optional) Wenn Sie die Authentifizierung lokal debuggen möchten, erweitern Sie den Ordner `App_Start`, öffnen Sie die Projektdatei **WebApiConfig.cs**, und fügen Sie anschließend den folgenden Code der Methode **Registrieren** hinzu.

		config.SetIsHosted(true);

	Daraufhin wird das lokale mobile Dienstprojekt so ausgeführt, als ob dieses in Azure gehostet wird. Die *AuthorizeLevel*-Einstellungen werden dabei auch berücksichtigt. Ohne diese Einstellung sind sämtliche HTTP-Anforderungen an "localhost" ohne Authentifizierung trotz der *AuthorizeLevel*-Einstellung zulässig. Wenn Sie den selbstgehosteten Modus aktivieren, müssen Sie auch einen Wert für den lokalen Anwendungsschlüssel festlegen.

4. (Optional) Legen Sie in der web.config-Projektdatei einen Zeichenfolgenwert für die Anwendungseinstellung `MS_ApplicationKey` fest.

	Dies ist das Kennwort, mit dem Sie die API-Hilfeseiten testen (ohne Benutzername), wenn Sie den Dienst lokal ausführen. Dieser Zeichenfolgenwert wird nicht von der Live-Website in Azure verwendet, und Sie müssen nicht den tatsächlichen Anwendungsschlüssel verwenden; es kann eine beliebige gültige Zeichenfolge verwendet werden.
 
4. Veröffentlichen Sie das Projekt erneut.

<!---HONumber=Oct15_HO3-->