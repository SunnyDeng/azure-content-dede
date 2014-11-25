<properties urlDisplayName="Using Soft Delete" pageTitle="Vorl&auml;ufiges L&ouml;schen in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Lernen Sie, wie Sie die Funktion zum vorl&auml;ufigen L&ouml;schen von Azure Mobile Services in Ihrer Anwendung nutzen" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Vorl&auml;ufiges L&ouml;schen in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Vorläufiges Löschen in Mobile Services

Für Tabellen, die mit dem JavaScript- oder .NET-Backend erstellt wurden, kann das vorläufige Löschen aktiviert sein. Bei Verwendung des vorläufigen Löschens wird eine neue Spalte \*\_\_deleted\* vom [SQL-Bit-Typ][SQL-Bit-Typ] zur Datenbank hinzugefügt. Bei aktiviertem vorläufigem Löschen werden Reihen nicht physisch aus der Datenbank gelöscht; stattdessen wird der Wert der gelöschten Spalte auf „TRUE“ gesetzt.

Bei der Abfrage von Datensätzen in einer Tabelle mit aktiviertem vorläufigem Löschen werden gelöschte Reihen standardmäßig nicht zurückgegeben. Um diese Reihen anzufordern, müssen Sie einen Abfrageparameter \*\_\_includeDeleted=true\* in den [REST-Abfragevorgang][REST-Abfragevorgang] einschließen. Im .NET-Client-SDK können Sie zudem die Hilfsmethode `IMobileServiceTable.IncludeDeleted()` nutzen.

Unterstützung für vorläufiges Löschen für das .NET-Backend wurde erstmalig mit Version 1.0.402 des .NET-Backend von Microsoft Azure Mobile Services veröffentlicht. Die neuesten NuGet-Pakete stehen unter [Microsoft Azure Mobile Services .NET Backend][Microsoft Azure Mobile Services .NET Backend] (in englischer Sprache) zur Verfügung.

Potenzielle Vorteile der Verwendung von vorläufigem Löschen:

-   Bei Verwendung der [Offlinedaten-Synchronisierung für Mobile Services][Offlinedaten-Synchronisierung für Mobile Services] fragt das Client-SDK automatisch gelöschte Datensätze ab und entfernt sie aus der lokalen Datenbank. Ist das vorläufige Löschen nicht aktiviert, müssen Sie zusätzlichen Code in das Backend schreiben, damit das Client-SDK weiß, welche Datensätze aus dem lokalen Speicher entfernt werden sollen. Andernfalls sind lokaler Client-Speicher und Backend im Hinblick auf diese gelöschten Datensätze inkonsistent und die Client-Methode `PurgeAsync()` muss aufgerufen werden, um den lokalen Speicher zu leeren.
-   Bei einigen Anwendungen werden Daten entsprechend einer Geschäftsanforderung niemals physisch gelöscht oder erst nach einem Audit gelöscht. In einem solchen Szenario kann die Funktion zum vorläufigen Löschen nützlich sein.
-   Das vorläufige Löschen kann zum Implementieren einer Funktion zum Rückgängigmachen eines Löschvorgangs verwendet werden, um unbeabsichtigt gelöschte Daten wiederherzustellen.
    Vorläufig gelöschte Datensätze belegen allerdings Speicherplatz in der Datenbank. Sie sollten daher überlegen, einen geplanten Job zum regelmäßigen, dauerhaften Löschen vorläufig gelöschter Datensätze zu erstellen. Ein Beispiel dazu finden Sie unter [Verwenden des vorläufigen Löschens mit dem .NET-Backend][Verwenden des vorläufigen Löschens mit dem .NET-Backend] und [Verwenden des vorläufigen Löschens mit dem JavaScript-Backend][Verwenden des vorläufigen Löschens mit dem JavaScript-Backend]. Ihr Client-Code sollte zudem regelmäßig `PurgeAsync()` aufrufen, damit diese dauerhaft gelöschten Datensätze nicht im lokalen Datenspeicher des Geräts verbleiben.

Überblick über dieses Thema:

1.  [Aktivieren des vorläufigen Löschens für das .NET-Backend][Aktivieren des vorläufigen Löschens für das .NET-Backend]
2.  [Aktivieren des vorläufigen Löschens für das JavaScript-Backend][Aktivieren des vorläufigen Löschens für das JavaScript-Backend]
3.  [Verwenden des vorläufigen Löschens mit dem .NET-Backend][Verwenden des vorläufigen Löschens mit dem .NET-Backend]
4.  [Verwenden des vorläufigen Löschens mit dem JavaScript-Backend][Verwenden des vorläufigen Löschens mit dem JavaScript-Backend]

## <a name="enable-for-dotnet"></a>Aktivieren des vorläufigen Löschens für das .NET-Backend

Unterstützung für vorläufiges Löschen für das .NET-Backend wurde erstmalig mit Version 1.0.402 des .NET-Backend von Microsoft Azure Mobile Services veröffentlicht. Die neuesten NuGet-Pakete stehen unter [Microsoft Azure Mobile Services .NET Backend][Microsoft Azure Mobile Services .NET Backend] (in englischer Sprache) zur Verfügung.

Die folgenden Schritte zeigen Ihnen, wie Sie das vorläufige Löschen für einen mobilen Dienst mit .NET-Backend aktivieren.

1.  Öffnen Sie das Projekt für den mobilen Dienst mit .NET-Backend in Visual Studio.
2.  Klicken Sie mit der rechten Maustaste auf das .NET-Backend-Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.
3.  Klicken Sie im Paketverwaltungsdialogfeld unter den Updates auf **Nuget.org**, und installieren Sie Version 1.0.402 oder höher der [Microsoft Azure Mobile Services .NET Backend][Microsoft Azure Mobile Services .NET Backend]-NuGet-Pakete.
4.  Erweitern Sie im Projektmappen-Explorer für Visual Studio den Knoten **Controller** unter Ihrem .NET-Backend-Projekt, und öffnen Sie die Controllerquelle. Zum Beispiel *TodoItemController.cs*.
5.  Übergeben Sie in der `Initialize()`-Methode des Controllers den Parameter `enableSoftDelete: true` an den EntityDomainManager-Konstruktor.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            MobileService1Context context = new MobileService1Context();
            DomainManager = new EntityDomainManager<TodoItem>(context, Request, Services, enableSoftDelete: true);
        }

## <a name="enable-for-javascript"></a>Aktivieren des vorläufigen Löschens für das JavaScript-Backend

Wenn Sie eine neue Tabelle für Ihren mobilen Dienst erstellen, können Sie das vorläufige Löschen auf der Seite für die Tabellenerstellung aktivieren.

![][0]

So aktivieren Sie das vorläufige Löschen für eine vorhandene Tabelle im JavaScript-Backend:

1.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf Ihren mobilen Dienst. Klicken Sie dann auf die Registerkarte **Daten**.
2.  Klicken Sie auf der Seite **Daten**, um die gewünschte Tabelle auszuwählen. Klicken Sie dann in der Befehlsleiste auf die Schaltfläche **Vorläufiges Löschen aktivieren**. Sollte das vorläufige Löschen bereits für die Tabelle aktiviert sein, wird die Schaltfläche nicht angezeigt, Sie können jedoch die Spalte \*\_\_deleted\* sehen, wenn Sie auf die Registerkarte **Durchsuchen** oder **Spalten** für die Tabelle klicken.

    ![][1]

    Um das vorläufige Löschen für die Tabelle zu deaktivieren, klicken Sie auf die Registerkarte **Spalten** und dann auf die Spalte \*\_\_deleted\* und die Schaltfläche **Löschen**.

    ![][2]

## <a name="using-with-dotnet"></a>Verwenden des vorläufigen Löschens mit dem .NET-Backend

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
     
            var toDelete = context.TodoItems.Where(x => x.Deleted == true && x.UpdatedAt <= monthAgo).ToArray();
            context.TodoItems.RemoveRange(toDelete);
            context.SaveChanges();
     
            return Task.FromResult(true);
        }
    }

Weitere Informationen zum Planen von Jobs mit Mobile Services mit .NET-Backend finden Sie unter [Planen von periodischen Aufträgen in Mobile Services.][Planen von periodischen Aufträgen in Mobile Services.]

## <a name="using-with-javascript"></a>Verwenden des vorläufigen Löschens mit dem JavaScript-Backend

Sie verwenden Tabellenskripte, um der Funktion für das vorläufige Löschen mit Mobile Services mit JavaScript-Backend Logik hinzuzufügen.

Um eine Anforderung für das Rückgängigmachen eines Löschvorgangs zu ermitteln, verwenden Sie die entsprechende Eigenschaft in Ihrem Update-Tabellenskript:

    function update(item, user, request) {
        if (request.undelete) { /* any undelete specific code */; }
    }

Um gelöschte Datensätze in das Abfrageergebnis in einem Skript einzuschließen, setzen Sie den Parameter „includeDeleted“ auf „true“:

    tables.getTable('softdelete_scenarios').read({
        includeDeleted: true,
        success: function (results) {
            request.respond(200, results)
        }
    });

Um gelöschte Datensätze über eine HTTP-Abfrage abzurufen, fügen Sie den Abfrageparameter „\_\_includedeleted=true“ hinzu:

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

Weitere Informationen zu geplanten Jobs mit Mobile Services mit JavaScript-Backend finden Sie unter [Planen von periodischen Aufträgen in Mobile Services][Planen von periodischen Aufträgen in Mobile Services].

 <!-- Images --> 

  [SQL-Bit-Typ]: http://msdn.microsoft.com/de-de/library/ms177603.aspx
  [REST-Abfragevorgang]: http://msdn.microsoft.com/de-de/library/azure/jj677199.aspx
  [Microsoft Azure Mobile Services .NET Backend]: http://go.microsoft.com/fwlink/?LinkId=513165
  [Offlinedaten-Synchronisierung für Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data/
  [Verwenden des vorläufigen Löschens mit dem .NET-Backend]: #using-with-dotnet
  [Verwenden des vorläufigen Löschens mit dem JavaScript-Backend]: #using-with-javascript
  [Aktivieren des vorläufigen Löschens für das .NET-Backend]: #enable-for-dotnet
  [Aktivieren des vorläufigen Löschens für das JavaScript-Backend]: #enable-for-javascript
  [0]: ./media/mobile-services-using-soft-delete/enable-soft-delete-new-table.png
  [Verwaltungsportal]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-using-soft-delete/enable-soft-delete-button.png
  [2]: ./media/mobile-services-using-soft-delete/disable-soft-delete.png
  [Planen von periodischen Aufträgen in Mobile Services.]: /de-de/documentation/articles/mobile-services-dotnet-backend-schedule-recurring-tasks/
  [Planen von periodischen Aufträgen in Mobile Services]: /de-de/documentation/articles/mobile-services-schedule-recurring-tasks/
