
Standardmäßig können APIs auf mobilen App-Back-Ends anonym aufgerufen werden. Als Nächstes müssen Sie den Zugriff auf authentifizierte Clients beschränken.

+ **Node.js-Back-End (über das Portal)**:  
	
	Klicken Sie in den **Einstellungen** der mobilen App auf **Easy Tables**, und wählen Sie Ihre Tabelle aus. Klicken Sie auf **Berechtigungen ändern**, wählen Sie für alle Berechtigungen **Authenticated access only** aus, und klicken Sie auf **Speichern**.

+ **.NET-Back-End (C#)**:

	Navigieren Sie im Serverprojekt zu **Controller** > **TodoItemController.cs**. Fügen Sie das `[Authorize]`-Attribut der **TodoItemController**-Klasse wie folgt hinzu: Um den Zugriff auf bestimmte Methoden zu beschränken, können Sie dieses Attribut auch nur auf diese Methoden anstelle der Klasse anwenden. Veröffentlichen Sie das Serverprojekt erneut.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node.js-Back-End (über Node.js-Code)**:
	
	Um für den Zugriff auf Tabellen eine Authentifizierung anzufordern, fügen Sie die folgende Zeile in das Node.js-Serverskript ein:


        table.access = 'authenticated';

	Weitere Informationen finden Sie unter [Erzwingen der Authentifizierung für den Zugriff auf Tabellen](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth) in [Verwenden des Azure Mobile Apps-SDK für Node.js](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md).

<!---HONumber=AcomDC_1203_2015-->