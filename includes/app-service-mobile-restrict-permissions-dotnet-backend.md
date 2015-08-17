

Standardmäßig werden die in Ihrer mobilen App definierten Endpunkte öffentlich verfügbar gemacht. Um den sicheren Zugriff auf die Ressourcen zu gewährleisten, müssen Sie den Zugriff ausschließlich auf authentifizierte Clients beschränken.

1. Öffnen Sie in Visual Studio das Projekt, in dem Ihr Mobile App-Code enthalten ist. 

2. Erweitern Sie im Projektmappen-Explorer den Ordner "Controllers", und öffnen Sie die Projektdatei "TodoItemController.cs".

	Die Klasse **TodoItemController** implementiert den Datenzugriff für die Tabelle "TodoItem".

3. Übernehmen Sie das `Authorize`-Attribut für die **TodoItemController**-Klasse:

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

	So wird sichergestellt, dass für alle Vorgänge für die Tabelle **TodoItem** eine Benutzerauthentifizierung erforderlich ist.

	>[AZURE.NOTE]Wenden Sie das Attribut "Authorize" auf einzelne Methoden an, um bestimmte Berechtigungsebenen für die Methoden festzulegen, die vom Controller eingeblendet werden.

4. Veröffentlichen Sie Ihr Mobile App-Projekt erneut.

<!---HONumber=August15_HO6-->