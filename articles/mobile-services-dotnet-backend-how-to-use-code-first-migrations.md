<properties pageTitle="How to use Code First Migrations .NET backend (Mobile Services)" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="Considerations for supporting multiple clients from a single mobile service" authors="glenga" solutions="" writer="glenga" manager="dwrede" editor="" />

Ändern des Datenmodells eines mobilen .NET-Backend-Dienstes
===========================================================

In einem .NET-Backend-Mobildienstprojekt wird der standardmäßige Datenbankinitialisierer von Entity Framework Code First von der Klasse [DropCreateDatabaseIfModelChanges] abgeleitet. Dieser Initialisierer weist das Entity Framework an, die Datenbank zu löschen und neu zu erstellen, wenn er eine von [DbContext] veranlasste Änderung des Datenmodells erkennt. Verwenden Sie diesen Initialisierer weiterhin bei der lokalen Entwicklung Ihres Mobildienstprojekts. Die .NET-Backend-Lernprogramme gehen davon aus, dass Sie mit diesem Initialisierer arbeiten. Wenn Sie allerdings das Datenmodell ändern, jedoch in der Datenbank vorhandene Daten beibehalten wollen, müssen Sie Code First Migrations verwenden. Die Verwendung von Code First Migrations empfiehlt sich ebenfalls für die Veröffentlichung von Datenmodelländerungen in Azure, da eine SQL-Datenbank nicht gelöscht werden kann.

In diesem Thema wird die Verwendung von Code First Migrations für Änderungen des Datenmodells einer vorhandenen SQL-Datenbank ohne Verlust vorhandener Daten erläutert. Bei diesem Verfahren wird vorausgesetzt, dass Sie Ihr Mobildienstprojekt bereits in Azure veröffentlicht haben, dass ihre Datenbank Daten enthält und dass das remote und das lokale Datenmodell noch synchron sind.

> [WACOM.NOTE]Sie sollten das Datenmodell so weit wie möglich auf Ihrem lokalen Computer entwickelt haben, bevor sie es in Azure veröffentlichen. Wenn Sie Ihr .NET-Backend-Mobildienstprojekt bereits in Azure veröffentlicht haben und das Tabellenschema Ihrer SQL-Datenbank nicht mit dem aktuellen Datenmodell Ihres Projekts übereinstimmt, müssen Sie die Tabellen löschen oder auf andere Weise manuell synchronisieren, bevor Sie den Versuch einer Veröffentlichung mit Code First Migrations unternehmen.

Wenn Sie ein .NET-Backend-Mobildienstprojekt auf Ihrem lokalen Computer entwickeln, sollten Sie für Datenmodelländerungen konsequent den Standardinitialisierer verwenden. Das ist die einfachste Lösung. Jedes Mal, wenn eine Datenmodelländerung erkannt wird, löscht dieser Initialisierer die Datenbank und erstellt sie neu. Das funktioniert allerdings nicht, wenn Sie Ihr Projekt erneut in Azure veröffentlichen. Der Initialisierer schlägt fehlt, weil zur Laufzeit sinnvollerweise nicht die Berechtigung zum Löschen einer SQL-Datenbank in Azure besteht.

> [WACOM.NOTE]Wenn Sie Ihr Mobildienstprojekt entwickeln und mit Live-Azure-Diensten testen, sollten Sie immer eine speziell für Testzwecke vorgesehene Mobildienstinstanz verwenden. In der Entwicklungs- und Testphase sollten Sie niemals einen mobilen Dienst verwenden, der sich aktuell im Produktionseinsatz befindet oder von Client-Anwendungen benutzt wird.

Löschen von Tabellen in der SQL-Datenbank
-----------------------------------------

Bevor Sie Migrations in Azure auf eine SQL-Datenbank anwenden, sollten Sie in dem von Ihrem mobilen Dienst verwendeten Datenbankschema eventuell vorhandene Tabellen manuell löschen. Gehen Sie wie folgt vor, um vorhandene Tabellen aus Ihrer SQL-Datenbank zu löschen. Wenn Ihr Datenbankschema bereits mit dem aktuellen Datenmodell synchron ist, können Sie diesen Schritt überspringen und mit [Migrations](#migrations) beginnen.

1.  Melden Sie sich auf dem [Azure-Verwaltungsportal] an, wählen Sie Ihren mobilen Dienst aus, klicken Sie auf die Registerkarte **Configure**, und klicken Sie dann auf den Link **SQL Database**.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png)

    Die Portalseite für die von Ihrem mobilen Dienst verwendete Datenbank wird aufgerufen.

2.  Klicken Sie auf die Schaltfläche **Manage**, und melden Sie sich auf Ihrem SQL-Datenbankserver an.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png)

3.  Klicken Sie im SQL-Datenbankmanager auf **Design**, dann auf **Tables**, wählen Sie im Schema Ihres mobilen Dienstes eine Tabelle aus, klicken Sie auf **Drop table**, und bestätigen Sie abschließend mit **OK**.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png)

4.  Wiederholen Sie den vorherigen Schritt für jede Tabelle im Schema des mobilen Dienstes.

    Sobald die vorhandenen Tabellen entfernt worden sind, kann Code First Migrations für die SQL-Datenbank initialisiert werden. Tabellen, die nicht zum Schema des mobilen Dienstes gehören, haben keinen Einfluss auf Ihren mobilen Dienst und sollten nicht entfernt werden.

Aktivieren von Code First Migrations
------------------------------------

Code First Migrations erzeugt mit einer Snapshotmethode Code, der bei Ausführung Schemaänderungen in der Datenbank durchführt. Mit Migrations können Sie inkrementelle Änderungen an Ihrem Datenmodell derart vornehmen, dass die in der Datenbank vorhandenen Daten erhalten bleiben. Gehen Sie folgendermaßen vor, um Migrations zu aktivieren und Änderungen des Datenmodells auf das Projekt, die lokale Datenbank und in Azure anzuwenden.

1.  Klicken Sie in Visual Studio im Solution Explorer mit der rechten Maustaste auf das Mobildienstprojekt und dann auf **Als Startprojekt festlegen**.

2.  Erweitern Sie aus dem Menü **Tools** heraus **NuGet-Paket-Manager**, und klicken Sie dann auf **Paket-Manager-Konsole**.

    Daraufhin wird die Paket-Manager-Konsole aufgerufen, mit der Sie die Code First Migrations verwalten werden.

3.  Führen Sie in der Paket-Manager-Konsole den folgenden Befehl aus:

         PM> Enable-Migrations

    Damit aktivieren Sie Code First Migrations für Ihr Projekt.

4.  Führen Sie auf der Konsole den folgenden Befehl aus:

         PM> Add-Migration Initial

    Dadurch wird eine neue Migration mit dem Namen *Initial* erstellt. Der Migration-Code wird im Migrations-Projektordner abgelegt.

5.  Erweitern Sie den App\_Start-Ordner, öffnen Sie die Projektdatei WebApiConfig.cs, und fügen Sie folgende **using**-Anweisungen hinzu:

         using System.Data.Entity.Migrations;
         using todolistService.Migrations;

    In dem obigen Code müssen Sie die Zeichenfolge *todolistService* durch den Namespace Ihres Projekts ersetzen. Für das heruntergeladene Schnellstartprojekt lautet dieser Name *mobile\_service\_name*Service.

6.  In derselben Codedatei müssen Sie den Aufruf des Verfahrens **Database.SetInitializer** auskommentieren und danach folgenden Code einfügen:

         var migrator = new DbMigrator(new Configuration());
         migrator.Update();

    Dadurch wird der Code First-Datenbankinitialisierer, der die Datenbank löscht und wieder neu erstellt, deaktiviert und durch die explizite Anforderung, die neueste Migration anzuwenden, ersetzt. Von diesem Moment an führen alle Änderungen des Datenmodells zu einer InvalidOperationException, wenn auf die Daten zugegriffen wird, es sei denn, dafür wurde eine Migration erstellt. Von nun an muss Ihr Dienst die Migration von Änderungen des Datenmodells in der Datenbank mit Code First Migrations durchführen.

7.  Drücken Sie die Taste F5, um das Mobildienstprojekt auf dem lokalen Computer zu starten.

    Jetzt ist die Datenbank synchron mit dem Datenmodell. Wenn Sie Seeddaten bereitgestellt haben, können Sie durch Anklicken von **Try it out**, **GET tables/todoitem**, dann **Try this out** und **Send** eine Überprüfung durchführen. Weitere Informationen finden Sie unter [Seeding von Daten bei Migrationen](#seeding).

8.  Ändern Sie nun Ihr Datenmodell, indem Sie beispielsweise eine neue UserId-Eigenschaft zu dem Typ TodoItem hinzufügen, das Projekt neu erstellen und dann im Paket-Manager den folgenden Befehl ausführen:

    	PM\> Add-Migration NewUserId



    Dadurch wird eine neue Migration mit dem Namen *NewUserId* erstellt. Eine neue Codedatei, die diese Änderung bereitstellt, wird in den Migrations-Ordner eingefügt.  

1.  Drücken Sie nochmals die Taste F5, um das Mobildienstprojekt erneut auf dem lokalen Computer zu starten.

    Die Migration wird auf die Datenbank angewendet, und die Datenbank ist wieder synchron mit dem Datenmodell. Wenn Sie Seeddaten bereitgestellt haben, können Sie durch Anklicken von **Try it out**, **GET tables/todoitem**, dann **Try this out** und **Send** eine Überprüfung durchführen. Weitere Informationen finden Sie unter [Seeding von Daten bei Migrationen](#seeding).

2.  Veröffentlichen Sie den mobilen Dienst wieder in Azure, führen Sie dann die Client-App aus, um auf die Daten zugreifen zu können, und prüfen Sie, ob die Daten geladen werden und kein Fehler auftritt.

3.  (Optional) Wählen Sie im [Azure-Verwaltungsportal] Ihren mobilen Dienst aus, klicken Sie auf die Registerkarte **Configure**, und klicken Sie dann auf den Link **SQL Database**.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png)

    Die SQL-Datenbankseite für die Datenbank Ihres mobilen Dienstes wird aufgerufen.

4.  (Optional) Klicken Sie auf **Manage**, melden Sie sich auf Ihrem SQL-Datenbankserver an, klicken Sie dann auf **Design**, und prüfen Sie, ob die Schemaänderungen in Azure durchgeführt wurden.

    ![](./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png)

Seeding von Daten bei Migrationen
---------------------------------

Sie können veranlassen, dass Migrations bei Ausführung einer Migration Seeddaten in die Datenbank einfügt. Die Configuration-Klasse verfügt über eine Seed-Methode, die Sie überschreiben und dann Daten einfügen oder aktualisieren können. Die Codedatei Configuration.cs wird in den Migrations-Ordner eingefügt, wenn Migrations aktiviert wird. Die nachfolgenden Beispiele zeigen, wie die [Seed]-Methode überschrieben werden kann, um durch Seeding Daten in die Tabelle **TodoItems** einzufügen. Die [Seed]-Methode wird im Anschluss an die Migration auf die neueste Version aufgerufen.

### Einfügen neuer Tabellendaten durch Seeding

Der folgende Code fügt durch Seeding neue Datenzeilen in die Tabelle **TodoItems** ein:

        List<TodoItem> todoItems = new List<TodoItem>
        {
            new TodoItem { Id = "1", Text = "First item", Complete = false },
            new TodoItem { Id = "2", Text = "Second item", Complete = false },
        };

        foreach (TodoItem todoItem in todoItems)
        {
            context.Set<TodoItem>().Add(todoItem);
        }
        base.Seed(context);

### Einfügen einer neuen Tabellenspalte durch Seeding

Der folgende Code fügt durch Seeding nur die Spalte UserId ein:

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Dieser Code ruft die Helper-Erweiterungsmethode [AddOrUpdate] auf, um Seed-Daten in die neue Spalte UserId einzufügen. [AddOrUpdate] verhindert die Erstellung doppelter Zeilen.

[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/query/dev12.query?appId=Dev12IDEF1&l=EN-US&k=k("System.Data.Entity.DropCreateDatabaseIfModelChanges\`1");k(TargetFrameworkMoniker-.NETFramework,Version%3Dv4.5);k(DevLang-csharp)&rd=true [Seed]: http://msdn.microsoft.com/de-de/library/hh829453(v=vs.113).aspx [Azure-Verwaltungsportal]: https://manage.windowsazure.com/ [DbContext]: http://msdn.microsoft.com/de-de/library/system.data.entity.dbcontext(v=vs.113).aspx [AddOrUpdate]: http://msdn.microsoft.com/de-de/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx

