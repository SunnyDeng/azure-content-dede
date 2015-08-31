
Standardmäßig können APIs auf mobilen App-Back-Ends anonym aufgerufen werden. Als Nächstes müssen Sie den Zugriff auf authentifizierte Clients beschränken.

1. Öffnen Sie auf Ihrem PC das Serverprojekt in Visual Studio, und navigieren Sie zu **Controller** > **TodoItemController.cs**.

2. Fügen Sie das `[Authorize]`-Attribut der **TodoItemController**-Klasse wie folgt hinzu: Dadurch erfordern alle Vorgänge für die Tabelle "TodoItem" einen authentifizierten Benutzer. Um den Zugriff auf bestimmte Methoden zu beschränken, können Sie dieses Attribut auch nur auf diese Methoden anstelle der Klasse anwenden.


        [Authorize]
        public class TodoItemController : TableController<TodoItem>
   
    Dadurch erfordern alle Vorgänge für die Tabelle "TodoItem" einen authentifizierten Benutzer. Um den Zugriff auf bestimmte Methoden zu beschränken, können Sie dieses Attribut auch nur auf diese Methoden anstelle der Klasse anwenden.
   
3. Veröffentlichen Sie Ihr Serverprojekt erneut.

<!---HONumber=August15_HO8-->