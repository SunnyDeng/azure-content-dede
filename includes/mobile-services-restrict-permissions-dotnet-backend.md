

Standardmäßig sind sämtliche Anfragen an die Ressourcen des mobilen Diensts auf Clients beschränkt, die den Anwendungsschlüssel vorweisen. Der sichere Zugriff auf die Ressourcen ist dadurch allerdings nicht gegeben. Um den sicheren Zugriff auf die Ressourcen zu gewährleisten, müssen Sie den Zugriff ausschließlich auf authentifizierte Clients beschränken.

1. Öffnen Sie in Visual Studio das Projekt, in dem Ihr mobiler Dienst enthalten ist. 

2. Erweitern Sie im Projektmappen-Explorer den Ordner "Controllers", und öffnen Sie die Projektdatei "TodoItemController.cs".

	Die Klasse **TodoItemController** implementiert den Datenzugriff auf die Tabelle "TodoItem". 

3. Fügen Sie die folgende Anweisung `using` oben in die Codeseite ein:

		Verwenden von Microsoft.WindowsAzure.Mobile.Service.Security;

4. Wenden Sie das folgende Attribut "AuthorizeLevel" für die Klasse **TodoItemController** an:

		[AuthorizeLevel(AuthorizationLevel.User)]

	Dadurch wird sichergestellt, dass alle Vorgänge für die Tabelle **TodoItem** einen authentifizierten Benutzer erfordern. 

	>[AZURE.NOTE]Wenden Sie das Attribut "AuthorizeLevel" für einzelne Methoden an, um bestimmte Berechtigungsebenen für die Methoden festzulegen, die vom Controller eingeblendet werden.

5. Wenn Sie die Authentifizierung lokal debuggen möchten, erweitern Sie den Ordner "App_Start", öffnen Sie die Projektdatei "WebApiConfig.cs", und fügen Sie dann den folgenden Code der **Register**-Methode hinzu:

		config.SetIsHosted(true);
	
	Daraufhin wird das lokale mobile Dienstprojekt so ausgeführt, als ob dieses in Azure gehostet wird. Die AuthorizeLevel-Einstellungen werden dabei auch berücksichtigt. Ohne diese Einstellung sind alle HTTP-Anforderungen an *localhost* ohne Authentifizierung trotz der AuthorizeLevel-Einstellung zulässig.  

6. Veröffentlichen Sie Ihr Dienstprojekt erneut.


<!--HONumber=42-->
