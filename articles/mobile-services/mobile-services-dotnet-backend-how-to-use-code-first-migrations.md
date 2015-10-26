<properties
	pageTitle="Ändern des Datenmodells eines mobilen .NET-Backend-Dienstes"
	description="Dieses Thema beschreibt Datenmodellinitialisierer und wie Sie Änderungen des Datenmodells in einem mobilen .NET Back-End-Dienst vornehmen."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	writer="glenga"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="NA"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/14/2015"
	ms.author="glenga"/>

# Ändern des Datenmodells eines mobilen .NET-Backend-Dienstes

In diesem Thema wird erläutert, wie Sie Code First-Migrationen in Entity Framework zum Ändern des Datenmodells einer vorhandenen Azure SQL-Datenbank verwenden, um den Verlust vorhandener Daten zu vermeiden. Bei diesem Verfahren wird vorausgesetzt, dass Sie Ihr .NET-Back-End-Projekt bereits in Azure veröffentlicht haben, dass ihre Datenbank Daten enthält und dass das remote und das lokale Datenmodell noch synchron sind. Außerdem werden in diesem Thema die standardmäßigen von Azure Mobile Services implementierten Code First-Initialisierer beschrieben, die während der Entwicklung verwendet werden. Mit diesen Initialisierern können Sie einfach Schemaänderungen ohne Verwendung von Code First-Migrationen ausführen, wenn keine vorhandenen Daten beibehalten werden müssen.

>[AZURE.NOTE]Der Schemaname, der als Präfix für Ihre Tabellen in der SQL-Datenbank verwendet wird, wird in der Datei "web.config" durch die Anwendungseinstellung "MS\_MobileServiceName" definiert. Wenn Sie das Startprojekt aus dem Portal herunterladen, ist dieser Wert bereits auf den Namen des mobilen Diensts festgelegt. Wenn der Schemaname mit dem des mobilen Diensts übereinstimmt, können mehrere mobile Dienste problemlos dieselbe Datenbankinstanz nutzen.

Beachten Sie, dass automatische Migrationen in einem .NET-Back-End-Projekt nicht unterstützt werden.

## Aktualisieren des Datenmodells

Mit neuen Funktionen fügen Sie Ihrem mobilen .NET-Back-End-Dienst auch Controller hinzu, um in Ihrer API neue Endpunkte bereitzustellen. Erstellen Sie eine neue API als einen benutzerdefinierten Controller oder einen Tabellen-Controller. Ein [TableController<TEntity>] stellt einen Datentyp bereit, der Informationen von [EntityData] übernimmt. Damit Daten in der Datenbank gespeichert werden können, muss dieser Datentyp dem Datenmodell auch als neues [DbSet<T>] im [DbContext] hinzugefügt werden. Weitere Informationen zu Code First im Entity Framework finden Sie unter [Erstellen eines Modells mit Code First](https://msdn.microsoft.com/data/ee712907#codefirst).

Visual Studio erleichtert das Erstellen eines neuen Tabellen-Controllers, um einen neuen Datentyp für Clientanwendungen bereitzustellen. Weitere Informationen finden Sie unter [Verwenden von Controllern für den Zugriff auf Daten in Mobile Services](https://msdn.microsoft.com/library/windows/apps/xaml/dn614132.aspx).

## Datenmodellinitialisierer

Mobile Services stellt in einem Projekt für mobile .NET-Back-End-Dienste zwei Basisklassen für Datenmodellinitialisierer bereit. Von beiden Initialisierern werden Tabellen in der Datenbank gelöscht und neu erstellt, sobald Entity Framework eine Datenmodelländerung im [DbContext] erkennt. Die Initialisierer funktionieren sowohl bei Ausführung des mobilen Diensts auf einem lokalen Computer als auch beim Hosting des mobilen Diensts in Azure.

>[AZURE.NOTE]Wenn Sie einen mobilen .NET Back-End-Dienst veröffentlichen, wird der Initialisierer erst ausgeführt, wenn ein Datenzugriffsvorgang erfolgt ist. Das bedeutet, dass die für einen neu veröffentlichte Dienst zum Speichern verwendeten Datentabellen erst erstellt werden, wenn ein Datenzugriffsvorgang, z. B. eine Abfrage, vom Client angefordert wird.
>
>Sie können auch einen Datenzugriffsvorgang mithilfe der integrierten API-Hilfefunktionen ausführen, auf die Sie über den Link **Ausprobieren** auf der Startseite zugreifen. Weitere Informationen zur Verwendung der API-Webseiten zum Testen Ihres mobilen Diensts finden Sie im Abschnitt "Lokales Testen des Projekt des mobilen Diensts" unter [Hinzufügen von Mobile Services zu einer vorhandenen App](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md#test-the-service-locally).

Durch beide Initialisierer werden alle Tabellen, Sichten, Funktionen und Prozeduren in dem vom mobilen Dienst verwendeten Schema aus der Datenbank gelöscht.

+ **ClearDatabaseSchemaIfModelChanges** <br/> Schemaobjekte werden nur gelöscht, wenn Code First eine Änderung im Datenmodell erkennt. Der Standardinitialisierer in einem .NET-Back-End-Projekt, das vom [Azure-Verwaltungsportal] heruntergeladen wurde, erbt von dieser Basisklasse.

+ **ClearDatabaseSchemaAlways**: <br/> Schemaobjekte werden bei jedem Zugriff auf das Datenmodell gelöscht. Verwenden Sie diese Basisklasse, um die Datenbank zurückzusetzen, ohne dass Sie eine Änderung am Datenmodell vornehmen müssen.

Sie können bei Ausführung auf einem lokalen Computer andere Code First-Datenmodellinitialisierer verwenden. Initialisierer, die versuchen, die Datenbank zu löschen, verursachen in Azure jedoch einen Fehler, weil der Benutzer keine Berechtigungen zum Löschen der Datenbank besitzt, was eine empfehlenswerte Vorkehrung ist.

Sie können Initialisierer weiterhin bei der lokalen Entwicklung des mobilen Dienst verwenden, und in den .NET-Back-End-Tutorials wird davon ausgegangen, dass Sie mit Initialisierern arbeiten. Wenn Sie allerdings das Datenmodell ändern, jedoch in der Datenbank vorhandene Daten beibehalten wollen, müssen Sie Code First Migrations verwenden.

>[AZURE.IMPORTANT]Wenn Sie Ihr Mobildienstprojekt entwickeln und mit Live-Azure-Diensten testen, sollten Sie immer eine speziell für Testzwecke vorgesehene Mobildienstinstanz verwenden. In der Entwicklungs- und Testphase sollten Sie niemals einen mobilen Dienst verwenden, der sich aktuell im Produktionseinsatz befindet oder von Client-Anwendungen benutzt wird.

Im heruntergeladenen Schnellstartprojekt ist der Code First-Initialisierer in der Datei "WebApiConfig.cs" definiert. Überschreiben Sie die **Seed**-Methode, um neuen Tabellen erste Datenzeilen hinzuzufügen. Beispiele zum Seeding von Daten finden Sie unter [Seeding von Daten bei Migrationen].

## <a name="migrations"></a>Aktivieren von Code First-Migrationen

Code First Migrations erzeugt mit einer Snapshotmethode Code, der bei Ausführung Schemaänderungen in der Datenbank durchführt. Mit Migrations können Sie inkrementelle Änderungen an Ihrem Datenmodell derart vornehmen, dass die in der Datenbank vorhandenen Daten erhalten bleiben.

>[AZURE.NOTE]Wenn Sie Ihr Projekt für mobile .NET-Back-End-Dienste bereits in Azure veröffentlicht haben und das Tabellenschema der SQL-Datenbank nicht mit dem aktuellen Datenmodell Ihres Projekts übereinstimmt, müssen Sie einen Initialisierer verwenden, die Tabellen manuell löschen oder das Schema und Datenmodell auf andere Weise synchronisieren, bevor Sie eine Veröffentlichung mit Code First-Migrationen ausführen können.

Gehen Sie folgendermaßen vor, um Migrations zu aktivieren und Änderungen des Datenmodells auf das Projekt, die lokale Datenbank und in Azure anzuwenden.

1. Klicken Sie in Visual Studio im Solution Explorer mit der rechten Maustaste auf das Mobildienstprojekt und dann auf **Als Startprojekt festlegen**.

2. Erweitern Sie aus dem Menü **Tools** heraus **NuGet-Paket-Manager**, und klicken Sie dann auf **Paket-Manager-Konsole**.

	Daraufhin wird die Paket-Manager-Konsole aufgerufen, mit der Sie die Code First Migrations verwalten werden.

3. Führen Sie in der Paket-Manager-Konsole den folgenden Befehl aus:

		PM> Enable-Migrations

	Damit aktivieren Sie Code First Migrations für Ihr Projekt.

4. Führen Sie auf der Konsole den folgenden Befehl aus:

		PM> Add-Migration Initial

	Dadurch wird eine neue Migration mit dem Namen *Initial* erstellt. Der Migration-Code wird im Migrations-Projektordner abgelegt.

5. Erweitern Sie den App\_Start-Ordner, öffnen Sie die Projektdatei WebApiConfig.cs, und fügen Sie folgende **using**-Anweisungen hinzu:

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	Im obigen Code müssen Sie die Zeichenfolge _todolistService _durch den Namespace Ihres Projekts ersetzen. Für das heruntergeladene Schnellstartprojekt lautet dieser <em>mobile&#95;service&#95;name</em>Service.

6. In derselben Codedatei müssen Sie den Aufruf des Verfahrens **Database.SetInitializer** auskommentieren und danach folgenden Code einfügen:

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	Dadurch wird der Code First-Datenbankinitialisierer, der die Datenbank löscht und wieder neu erstellt, deaktiviert und durch die explizite Anforderung, die neueste Migration anzuwenden, ersetzt. Von diesem Moment an führen alle Änderungen des Datenmodells zu einer InvalidOperationException, wenn auf die Daten zugegriffen wird, es sei denn, dafür wurde eine Migration erstellt. Von nun an muss Ihr Dienst die Migration von Änderungen des Datenmodells in der Datenbank mit Code First Migrations durchführen.

7.  Drücken Sie die Taste F5, um das Mobildienstprojekt auf dem lokalen Computer zu starten.

	Jetzt ist die Datenbank synchron mit dem Datenmodell. Wenn Sie Seeddaten bereitgestellt haben, können Sie durch Anklicken von **Try it out**, **GET tables/todoitem**, dann **Try this out** und **Send** eine Überprüfung durchführen. Weitere Informationen finden Sie unter [Seeding von Daten bei Migrationen].

8.   Ändern Sie nun Ihr Datenmodell, indem Sie beispielsweise eine neue UserId-Eigenschaft zu dem Typ TodoItem hinzufügen, das Projekt neu erstellen und dann im Paket-Manager den folgenden Befehl ausführen:

		PM> Add-Migration NewUserId

	Dadurch wird eine neue Migration mit dem Namen *NewUserId* erstellt. Eine neue Codedatei, die diese Änderung bereitstellt, wird in den Migrations-Ordner eingefügt.

9.  Drücken Sie nochmals die Taste F5, um das Mobildienstprojekt erneut auf dem lokalen Computer zu starten.

	Die Migration wird auf die Datenbank angewendet, und die Datenbank ist wieder synchron mit dem Datenmodell. Wenn Sie Seeddaten bereitgestellt haben, können Sie durch Anklicken von **Try it out**, **GET tables/todoitem**, dann **Try this out** und **Send** eine Überprüfung durchführen. Weitere Informationen finden Sie unter [Seeding von Daten bei Migrationen].

10. Veröffentlichen Sie den mobilen Dienst wieder in Azure, führen Sie dann die Client-App aus, um auf die Daten zugreifen zu können, und prüfen Sie, ob die Daten geladen werden und kein Fehler auftritt.

13. (Optional) Wählen Sie im [Azure-Verwaltungsportal] Ihren mobilen Dienst aus, und klicken Sie auf **Konfigurieren** > **SQL-Datenbank**. Die SQL-Datenbankseite für die Datenbank Ihres mobilen Dienstes wird aufgerufen.

14. (Optional) Klicken Sie auf **Manage**, melden Sie sich auf Ihrem SQL-Datenbankserver an, klicken Sie dann auf **Design**, und prüfen Sie, ob die Schemaänderungen in Azure durchgeführt wurden.


## Verwenden von Code First-Migrationen ohne Initialisierer
Bevor Sie Code First-Migrationen mit Ihrem .NET-Back-End-Projekt verwenden, sollten Sie einen Datenmodellinitialisierer ausführen. Wenn Sie KEINEN Initialisierer verwenden, können beim Anwenden von Migrationen Fehler auftreten. Wenn Sie keinen der vordefinierten Datenmodellinitialisierer verwenden, stellen Sie sicher, dass Migrationen in der Datei "Migrations\\Configuration.cs" zur Verwendung von "EntityTableSqlGenerator" als "SqlGenerator" konfiguriert sind, wie im nachstehenden Beispiel gezeigt:

    public Configuration()
    {
        AutomaticMigrationsEnabled = false;
        SetSqlGenerator("System.Data.SqlClient", new EntityTableSqlGenerator());
    }
    
##<a name="seeding"></a>Seeding von Daten bei Migrationen

Sie können veranlassen, dass Migrations bei Ausführung einer Migration Seeddaten in die Datenbank einfügt. Die **Configuration**-Klasse verfügt über eine **Seed**-Methode, die Sie überschreiben können, um Daten einzufügen oder zu aktualisieren. Die Codedatei Configuration.cs wird in den Migrations-Ordner eingefügt, wenn Migrations aktiviert wird. Die nachfolgenden Beispiele zeigen, wie die [Seed]-Methode überschrieben wird, um durch Seeding Daten in die Tabelle **TodoItems** einzufügen. Die [Seed]-Methode wird im Anschluss an die Migration zur neuesten Version aufgerufen.

###Einfügen neuer Tabellendaten durch Seeding

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

###Einfügen einer neuen Tabellenspalte durch Seeding

Der folgende Code fügt durch Seeding nur die Spalte UserId ein:

        context.TodoItems.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Dieser Code ruft die Hilfserweiterungsmethode [AddOrUpdate] auf, um Seed-Daten in die neue UserId-Spalte einzufügen. [AddOrUpdate] verhindert die Erstellung doppelter Zeilen.

<!-- Anchors -->
[Migrations]: #migrations
[Seeding von Daten bei Migrationen]: #seeding

<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/navagate-to-sql-database.png
[1]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/manage-sql-database.png
[2]: ./media/mobile-services-dotnet-backend-how-to-use-code-first-migrations/sql-database-drop-tables.png

<!-- URLs -->
[DropCreateDatabaseIfModelChanges]: http://msdn.microsoft.com/library/gg679604(v=vs.113).aspx
[Seed]: http://msdn.microsoft.com/library/hh829453(v=vs.113).aspx
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[DbContext]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx
[AddOrUpdate]: http://msdn.microsoft.com/library/system.data.entity.migrations.idbsetextensions.addorupdate(v=vs.103).aspx
[TableController<TEntity>]: https://msdn.microsoft.com/library/azure/dn643359.aspx
[EntityData]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.entitydata.aspx
[DbSet<T>]: https://msdn.microsoft.com/library/azure/gg696460.aspx

<!---HONumber=Oct15_HO3-->