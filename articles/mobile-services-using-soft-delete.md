<properties 
	pageTitle="Vorläufiges Löschen in Mobile Services (Windows Store) | Mobile Dev Center" 
	description="Lernen Sie, wie Sie die Funktion zum vorläufigen Löschen von Azure Mobile Services in Ihrer Anwendung nutzen" 
	documentationCenter="" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="wesmc"/>

# Vorläufiges Löschen in Mobile Services

Für Tabellen, die mit dem JavaScript- oder .NET-Backend erstellt wurden, kann das vorläufige Löschen aktiviert sein. Bei Verwendung des vorläufigen Löschens wird eine neue Spalte *\__deleted* vom [SQL-Bit-Typ] zur Datenbank hinzugefügt. Bei aktiviertem vorläufigem Löschen werden Reihen nicht physisch aus der Datenbank gelöscht; stattdessen wird der Wert der gelöschten Spalte auf "TRUE" gesetzt.

Bei der Abfrage von Datensätzen in einer Tabelle mit aktiviertem vorläufigem Löschen werden gelöschte Reihen standardmäßig nicht zurückgegeben. Um diese Reihen anzufordern, müssen Sie einen Abfrageparameter *\__includeDeleted=true* n den [REST-Abfragevorgang](http://msdn.microsoft.com/library/azure/jj677199.aspx) einschließen. Im .NET-Client-SDK können Sie zudem die Hilfsmethode `IMobileServiceTable.IncludeDeleted()` verwenden.

Unterstützung für vorläufiges Löschen für das .NET-Backend wurde erstmalig mit Version 1.0.402 des .NET-Backend von Microsoft Azure Mobile Services veröffentlicht. Die neuesten NuGet-Pakete stehen unter [.NET-Back-End von Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165) zur Verfügung.


Potenzielle Vorteile der Verwendung von vorläufigem Löschen:

* Bei Verwendung der [Offlinedaten-Synchronisierung für Mobile Services] fragt das Client-SDK automatisch gelöschte Datensätze ab und entfernt sie aus der lokalen Datenbank. Ist das vorläufige Löschen nicht aktiviert, müssen Sie zusätzlichen Code in das Backend schreiben, damit das Client-SDK weiß, welche Datensätze aus dem lokalen Speicher entfernt werden sollen. Andernfalls sind lokaler Clientspeicher und Back-End im Hinblick auf diese gelöschten Datensätze inkonsistent, und die  `PurgeAsync()`-Clientmethode muss aufgerufen werden, um den lokalen Speicher zu leeren.
* Bei einigen Anwendungen werden Daten entsprechend einer Geschäftsanforderung niemals physisch gelöscht oder erst nach einem Audit gelöscht. In einem solchen Szenario kann die Funktion zum vorläufigen Löschen nützlich sein.
* Mit vorläufigem Löschen kann eine "undelete"-Funktion implementiert werden, damit versehentlich gelöschte Daten wiederhergestellt werden können.
Allerdings nehmen vorläufig gelöschte Datensätze Speicherplatz in der Datenbank in Anspruch. Daher sollten Sie ggf. einen geplanten Auftrag erstellen, mit dem vorläufig gelöschte Datensätze in regelmäßigen Abständen dauerhaft gelöscht werden. Ein Beispiel dazu finden Sie unter [Verwenden des vorläufigen Löschens mit dem .NET-Back-End] und [Verwenden des vorläufigen Löschens mit dem JavaScript-Back-End]. Ihr Clientcode sollte zudem regelmäßig `PurgeAsync()` aufrufen, damit diese dauerhaft gelöschten Datensätze nicht im lokalen Datenspeicher des Geräts verbleiben.



Überblick über dieses Thema:

1. [Aktivieren des vorläufigen Löschens für das .NET-Back-End]
2. [Aktivieren des vorläufigen Löschens für das JavaScript-Back-End]
3. [Verwenden des vorläufigen Löschens mit dem .NET-Back-End] 
4. [Verwenden des vorläufigen Löschens mit dem JavaScript-Back-End] 



## <a name="enable-for-dotnet"></a>Aktivieren des vorläufigen Löschens für das .NET-Back-End

Unterstützung für vorläufiges Löschen für das .NET-Backend wurde erstmalig mit Version 1.0.402 des .NET-Backend von Microsoft Azure Mobile Services veröffentlicht. Die neuesten NuGet-Pakete stehen unter [.NET-Back-End von Microsoft Azure Mobile Services](http://go.microsoft.com/fwlink/?LinkId=513165) zur Verfügung.

Die folgenden Schritte zeigen Ihnen, wie Sie das vorläufige Löschen für einen mobilen Dienst mit .NET-Backend aktivieren.

1. Öffnen Sie das Projekt für den mobilen Dienst mit .NET-Back-End in Visual Studio.
2. Klicken Sie mit der rechten Maustaste auf das .NET-Back-End-Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**. 
3. Klicken Sie im Paketverwaltungsdialogfeld unter den Updates auf **Nuget.org** , und installieren Sie Version 1.0.402 oder höher der [Microsoft Azure Mobile Services .NET Back-End](http://go.microsoft.com/fwlink/?LinkId=513165)-NuGet-Pakete.
3. Erweitern Sie im Projektmappen-Explorer für Visual Studio den Knoten **Controller** unter Ihrem .NET-Back-End-Projekt, und öffnen Sie die Controllerquelle. Zum Beispiel *TodoItemController.cs*.
4. Übergeben Sie in der `Initialize()`-Methode des Controllers den `enableSoftDelete: true`-Parameter an den EntityDomainManager-Konstruktor.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }


## <a name="enable-for-javascript"></a>Aktivieren des vorläufigen Löschens für das JavaScript-Back-End

Wenn Sie eine neue Tabelle für Ihren mobilen Dienst erstellen, können Sie das vorläufige Löschen auf der Seite für die Tabellenerstellung aktivieren.

![][2]

So aktivieren Sie das vorläufige Löschen für eine vorhandene Tabelle im JavaScript-Backend:

1. Klicken Sie im [Verwaltungsportal] auf Ihren mobilen Dienst. Klicken Sie dann auf die Registerkarte "Daten".
2. Klicken Sie auf der Seite "Daten", um die gewünschte Tabelle auszuwählen. Klicken Sie dann in der Befehlsleiste auf die Schaltfläche **Vorläufiges Löschen aktivieren**. Sollte das vorläufige Löschen bereits für die Tabelle aktiviert sein, wird die Schaltfläche nicht angezeigt, Sie können jedoch die Spalte *\__deleted* sehen, wenn Sie auf die Registerkarte **Durchsuchen** oder **Spalten** für die Tabelle klicken.

    ![][0]

    Um das vorläufige Löschen für die Tabelle zu deaktivieren, klicken Sie auf die Registerkarte **Spalten**, und die Schaltfläche **Löschen**.  

    ![][1]

## <a name="using-with-dotnet"></a>Verwenden des vorläufigen Löschens mit dem .NET-Back-End


Mit dem folgenden geplanten Job werden vorläufig gelöschte Datensätze, die älter als einen Monat sind, bereinigt:

    public class SampleJob : ScheduledJob
    {
        private MobileService1Context context;
     
        protected override void Initialize(ScheduledJobDescriptor scheduledJobDescriptor, 
            CancellationToken cancellationToken)
        {
            base.Initialize(scheduledJobDescriptor, cancellationToken);
            context = new MobileService1Context();
        }
     
        public override Task ExecuteAsync()
        {
            Services.Log.Info("Purging old records");
            var monthAgo = DateTimeOffset.UtcNow.AddDays(-30);
     
            var toDelete = context.TodoIte
	ms.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoIte
	ms.RemoveRange(toDelete);
            context.SaveChanges();
     
            return Task.FromResult(true);
        }
    }

Weitere Informationen zum Planen von Jobs mit Mobile Services mit .NET-Backend finden Sie unter [Planen von periodischen Aufträgen mit JavaScript-Back-End von Mobile Services](/de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/) 




## <a name="using-with-javascript"></a>Verwenden des vorläufigen Löschens mit dem JavaScript-Back-End

Sie verwenden Tabellenskripte, um der Funktion für das vorläufige Löschen mit Mobile Services mit JavaScript-Backend Logik hinzuzufügen.

Um eine Anforderung für das Rückgängigmachen eines Löschvorgangs zu ermitteln, verwenden Sie die entsprechende Eigenschaft in Ihrem Update-Tabellenskript:
    
    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }
To include deleted records in query result in a script, set the "includeDeleted" parameter to true:
    
    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Um gelöschte Datensätze über eine HTTP-Abfrage abzurufen, fügen Sie den Abfrageparameter "__includedeleted=true" hinzu:

    http://youservice.azure-mobile.net/tables/todoitem?__includedeleted=true

### Beispiel für einen geplanten Job zum Bereinigen vorläufig gelöschter Datensätze.

Dies ist ein beispielhafter geplanter Job für das Löschen von Datensätzen, die vor einem bestimmten Datum aktualisiert wurden:

    function purgedeleted() {
         mssql.query('DELETE FROM softdelete WHERE __deleted=1', {
            success: function(results) {
                console.log(results);
            },
            error: function(err) {
                console.log("error is: " + err);
        }});
    }

Weitere Informationen zu geplanten Jobs mit Mobile Services mit JavaScript-Backend finden Sie unter [Planen von periodischen Aufträgen mit JavaScript-Back-End von Mobile Services](/de-de/documentation/articles/mobile-services-schedule-recurring-tasks/).




<!-- Anchors. -->
[Aktivieren des vorläufigen Löschens für das .NET-Back-End]: #enable-for-dotnet
[Aktivieren des vorläufigen Löschens für das JavaScript-Back-End]: #enable-for-javascript
[Verwenden des vorläufigen Löschens mit dem .NET-Back-End]: #using-with-dotnet
[Verwenden des vorläufigen Löschens mit dem JavaScript-Back-End]: #using-with-javascript

<!-- Images -->
[0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
[1]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
[2]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png

<!-- URLs. -->
[SQL-Bit-Typ]: http://msdn.microsoft.com/library/ms177603.aspx
[Offlinedatensynchronisierung für Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/
[Verwaltungsportal]: https://manage.windowsazure.com/




<!--HONumber=42-->
