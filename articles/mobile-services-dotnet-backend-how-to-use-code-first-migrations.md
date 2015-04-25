<properties 
	pageTitle="Ändern des Datenmodells eines mobilen .NET-Back-End-Dienstes" 
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
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="glenga"/>

# Ändern des Datenmodells eines mobilen .NET-Back-End-Dienstes

In diesem Thema wird erläutert, wie Sie Entity Framework Code First-Migrationen zum Ändern des Datenmodells einer vorhandenen Azure SQL-Datenbank verwenden, um den Verlust vorhandener Daten zu vermeiden. Bei diesem Verfahren wird vorausgesetzt, dass Sie Ihr Mobildienstprojekt bereits in Azure veröffentlicht haben, dass ihre Datenbank Daten enthält und dass das remote und das lokale Datenmodell noch synchron sind. Außerdem werden in diesem Thema die standardmäßigen von Azure Mobile Services implementierten Code First-Initialisierer beschrieben, die während der Entwicklung verwendet werden. Mit diesen Initialisierern können Sie einfach Schemaänderungen ohne Verwendung von Code First-Migrationen ausführen, wenn keine vorhandenen Daten beibehalten werden müssen. 

## Datenmodellinitialisierer

Mobile Services unterstützen in einem Projekt für mobile .NET-Back-End-Dienste zwei Basisklassen für Datenmodellinitialisierer. Von beiden Initialisierern werden Tabellen in der Datenbank gelöscht und neu erstellt, sobald Entity Framework eine Datenmodelländerung im [DbContext] erkennt. Die Initialisierer funktionieren sowohl bei Ausführung des mobilen Diensts auf einem lokalen Computer als auch beim Hosting des mobilen Diensts in Azure. 

>[AZURE.NOTE]Wenn Sie einen mobilen .NET Back-End-Dienst veröffentlichen, wird der Initialisierer erst ausgeführt, wenn ein Datenzugriffsvorgang erfolgt ist. Das bedeutet, dass die für einen neu veröffentlichte Dienst zum Speichern verwendeten Datentabellen erst erstellt werden, wenn ein Datenzugriffsvorgang, z. B. eine Abfrage, vom Client angefordert wird. 
>
>Sie können einen Datenzugriffsvorgang auch mithilfe der integrierten API-Hilfefunktionen ausführen, die Sie über den Link **Ausprobieren** auf der Startseite aufrufen. Weitere Informationen zur Verwendung der API-Seiten zum Testen eines mobilen Diensts finden Sie im Abschnitt "Lokales Testen des mobilen Dienstprojekts" in [Hinzufügen von Mobile Services zu einer vorhandenen App](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data/#test-the-service-locally).  .

Durch beide Initialisiererbasisklassen werden alle Tabellen, Sichten, Funktionen und Prozeduren in dem vom mobilen Dienst verwendeten Schema aus der Datenbank gelöscht. 

+ **ClearDatabaseSchemaIfModelChanges** <br/> Schemaobjekte werden nur gelöscht, wenn durch Code First eine Änderung am Datenmodell erkannt wird. Der Standardinitialisierer in einem .NET-Back-End-Projekt, das vom [Azure-Verwaltungsportal] heruntergeladen wurde, erbt von dieser Basisklasse.
 
+ **ClearDatabaseSchemaAlways**: <br/> Schemaobjekte werden bei jedem Zugriff auf das Datenmodell gelöscht. Verwenden Sie diese Basisklasse, um die Datenbank zurückzusetzen, ohne dass Sie eine Änderung am Datenmodell vornehmen müssen.   	 	

Im heruntergeladenen Schnellstartprojekt ist der Code First-Initialisierer in der Datei "WebApiConfig.cs" definiert. Überschreiben Sie die **Seed**-Methode, um neuen Tabellen erste Datenzeilen hinzuzufügen. Beispiele zum Datenseeding finden Sie unter [Seeding von Daten bei Migrationen]. Bei der Ausführung auf einem lokalen Computer können Sie andere Code First-Datenmodellinitialisierer verwenden. Initialisierer, die versuchen, die Datenbank zu löschen, verursachen in Azure jedoch einen Fehler, weil der Benutzer keine Berechtigungen zum Löschen der Datenbank besitzt, was eine empfehlenswerte Vorkehrung ist. 

Sie können Initialisierer weiterhin bei der lokalen Entwicklung des mobilen Dienst verwenden, und in den .NET-Back-End-Lernprogrammen wird davon ausgegangen, dass Sie mit Initialisierern arbeiten. Wenn Sie allerdings das Datenmodell ändern, jedoch in der Datenbank vorhandene Daten beibehalten wollen, müssen Sie Code First Migrations verwenden. 

>[AZURE.IMPORTANT]Wenn Sie Ihr Mobildienstprojekt entwickeln und mit Live-Azure-Diensten testen, sollten Sie immer eine speziell für Testzwecke vorgesehene Mobildienstinstanz verwenden. In der Entwicklungs- und Testphase sollten Sie niemals einen mobilen Dienst verwenden, der sich aktuell im Produktionseinsatz befindet oder von Client-Anwendungen benutzt wird. 

## <a name="migrations"></a>Aktivieren von Code First-Migrationen

Code First-Migrationen erzeugen mit einer Snapshotmethode Code, der bei Ausführung Schemaänderungen in der Datenbank durchführt. Mit Migrations können Sie inkrementelle Änderungen an Ihrem Datenmodell derart vornehmen, dass die in der Datenbank vorhandenen Daten erhalten bleiben. 

>[AZURE.NOTE]Wenn Sie Ihr Projekt für mobile .NET-Back-End-Dienste bereits in Azure veröffentlicht haben und das Tabellenschema der SQL-Datenbank nicht mit dem aktuellen Datenmodell Ihres Projekts übereinstimmt, müssen Sie einen Initialisierer verwenden, die Tabellen manuell löschen oder das Schema und Datenmodell auf andere Weise synchronisieren, bevor Sie eine Veröffentlichung mit Code First-Migrationen ausführen können.

Gehen Sie folgendermaßen vor, um Migrations zu aktivieren und Änderungen des Datenmodells auf das Projekt, die lokale Datenbank und in Azure anzuwenden. 

1. Klicken Sie in Visual Studio im Solution Explorer mit der rechten Maustaste auf das Mobildienstprojekt und dann auf **Als Startprojekt festlegen**.
 
2. Erweitern Sie aus dem Menü **Extras** heraus **NuGet-Paket-Manager**, und klicken Sie dann auf **Paket-Manager-Konsole**.

	Daraufhin wird die Paket-Manager-Konsole aufgerufen, mit der Sie die Code First Migrations verwalten werden.

3. Führen Sie in der Paket-Manager-Konsole den folgenden Befehl aus:

		PM> Enable-Migrations

	Damit aktivieren Sie Code First Migrations für Ihr Projekt.

4. Führen Sie auf der Konsole den folgenden Befehl aus:

		PM> Add-Migration Initial

	Dadurch wird eine neue Migration mit dem Namen  *Initial* erstellt. Der Migration-Code wird im Migrations-Projektordner abgelegt.

5. Erweitern Sie den App_Start-Ordner, öffnen Sie die Projektdatei WebApiConfig.cs, und fügen Sie folgende **using**-Anweisungen hinzu:

		using System.Data.Entity.Migrations;
		using todolistService.Migrations;

	Ersetzen Sie im obigen Code, die Zeichenfolge _todolistService_ mit dem Namespace des Projekts, der für das heruntergeladene Schnellstartprojekt <em>mobile&#95;service&#95;name</em>Service lautet.  
 
6. In derselben Codedatei müssen Sie den Aufruf des Verfahrens **Database.SetInitializer** auskommentieren und danach folgenden Code einfügen:

        var migrator = new DbMigrator(new Configuration());
        migrator.Update();

	Dadurch wird der Code First-Datenbankinitialisierer, der die Datenbank löscht und wieder neu erstellt, deaktiviert und durch die explizite Anforderung, die neueste Migration anzuwenden, ersetzt. Von diesem Moment an führen alle Änderungen des Datenmodells zu einer InvalidOperationException, wenn auf die Daten zugegriffen wird, es sei denn, dafür wurde eine Migration erstellt. Von nun an muss Ihr Dienst die Migration von Änderungen des Datenmodells in der Datenbank mit Code First Migrations durchführen.

7.  Drücken Sie die Taste F5, um das Mobildienstprojekt auf dem lokalen Computer zu starten.
 
	Jetzt ist die Datenbank synchron mit dem Datenmodell. Wenn Sie Seeddaten bereitgestellt haben, können Sie durch Anklicken von **Testen Sie es**, **GET tables/todoitem**, dann **Ausprobieren** und **Senden** eine Überprüfung durchführen. Weitere Informationen finden Sie unter [Seeding von Daten bei Migrationen].

8.   Ändern Sie nun Ihr Datenmodell, indem Sie beispielsweise eine neue UserId-Eigenschaft zu dem Typ TodoItem hinzufügen, das Projekt neu erstellen und dann im Paket-Manager den folgenden Befehl ausführen:

		PM> Add-Migration NewUserId
                                                               
	Dadurch wird eine neue Migration mit dem Namen  *NewUserId* erstellt. Eine neue Codedatei, die diese Änderung bereitstellt, wird in den Migrations-Ordner eingefügt.  

9.  Drücken Sie nochmals die Taste F5, um das Mobildienstprojekt erneut auf dem lokalen Computer zu starten.

	Die Migration wird auf die Datenbank angewendet, und die Datenbank ist wieder synchron mit dem Datenmodell. Wenn Sie Seeddaten bereitgestellt haben, können Sie durch Anklicken von **Testen Sie es**, **GET tables/todoitem**, dann **Ausprobieren** und **Senden** eine Überprüfung durchführen. Weitere Informationen finden Sie unter [Seeding von Daten bei Migrationen].

10. Veröffentlichen Sie den mobilen Dienst wieder in Azure, führen Sie dann die Client-App aus, um auf die Daten zugreifen zu können, und prüfen Sie, ob die Daten geladen werden und kein Fehler auftritt. 

13. (Optional) Wählen Sie im [Azure-Verwaltungsportal] Ihren mobilen Dienst aus, klicken Sie auf die Registerkarte **Konfigurieren**, und klicken Sie dann auf den Link **SQL-Datenbank**. 

	![][0]

	Die SQL-Datenbankseite für die Datenbank Ihres mobilen Dienstes wird aufgerufen.

14. (Optional) Klicken Sie auf **Verwalten**, melden Sie sich auf Ihrem SQL-Datenbankserver an, klicken Sie dann auf **Design**, und prüfen Sie, ob die Schemaänderungen in Azure durchgeführt wurden. 

    ![][1] 


##<a name="seeding"></a>Seeding von Daten bei Migrationen

Sie können veranlassen, dass Migrations bei Ausführung einer Migration Seeddaten in die Datenbank einfügt. Die **Configuration**-Klasse verfügt über eine **Seed**-Methode, die Sie überschreiben und dann Daten einfügen oder aktualisieren können. Die Codedatei Configuration.cs wird in den Migrations-Ordner eingefügt, wenn Migrations aktiviert wird. Die nachfolgenden Beispiele zeigen, wie die [Seed]-Methode überschrieben wird, um durch Seeding Daten in die Tabelle **TodoItems** einzufügen. Die [Seed]-Methode wird im Anschluss an die Migration zur neuesten Version aufgerufen. 

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
 		    
        context.TodoIte
	ms.AddOrUpdate(
            t => t.UserId,
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 1 },
                new TodoItem { UserId = 2 }
            );
        base.Seed(context);

Dieser Code ruft die Helper-Erweiterungsmethode [AddOrUpdate] auf, um Seed-Daten in die neue Spalte UserId einzufügen. [AddOrUpdate] verhindert die Erstellung doppelter Zeilen.

<!-- Anchors -->
[Migrationen]: #migrations
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


<!--HONumber=42-->
