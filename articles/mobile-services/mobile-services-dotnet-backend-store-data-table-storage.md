<properties
	pageTitle="Erstellen eines mobilen .NET-Back-End-Diensts, der den Tabellenspeicher verwendet | Azure Mobile Services"
	description="Erfahren Sie, wie Sie den Azure-Tabellenspeicher mit dem mobilen .NET-Back-End-Dienst verwenden."
	services="mobile-services"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="glenga"/>

# Erstellen eines mobilen .NET-Back-End-Diensts, der den Tabellenspeicher verwendet

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

In diesem Thema erfahren Sie, wie Sie einen nicht relationalen Datenspeicher für Ihren mobilen .NET-Back-End-Dienst verwenden. In diesem Lernprogramm ändern Sie das Azure Mobile Services-Schnellstartprojekt, um den Azure-Tabellenspeicher statt des standardmäßigen Azure SQL-Datenbank-Datenspeichers zu verwenden.

Voraussetzung für dieses Lernprogramm ist der Abschluss des Lernprogramms [Erste Schritte mit Mobile Services]. Sie benötigen außerdem ein Azure-Speicherkonto.

##Konfigurieren des Azure-Tabellenspeichers im mobilen .NET-Back-End-Dienst

Zunächst müssen Sie den mobilen Dienst und das .NET Back-End-Codeprojekt zum Herstellen einer Verbindung mit dem Azure-Speicher konfigurieren.

1. Klicken Sie im **Projektmappen-Explorer** in Visual Studio mit der rechten Maustaste auf das .NET-Back-End-Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** aus, wählen Sie **Stabile Only** aus, suchen Sie nach **MobileServices**, klicken Sie im Paket **Microsoft Azure Mobile Services .NET Backend Azure Storage Extension** auf **Installieren**, und akzeptieren Sie dann die Lizenzbedingungen.

  	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-add-storage-nuget-package-dotnet.png)

  	Dadurch wird dem Projekt für den mobilen .NET-Back-End-Dienst Unterstützung für Azure-Speicherdienste hinzugefügt.

3. Wenn Sie das Speicherkonto noch nicht erstellt haben, lesen Sie [So erstellen Sie ein Speicherkonto](../storage/storage-create-storage-account.md).

4. Klicken Sie im [klassischen Azure-Portal] auf **Speicher**, klicken Sie auf das Speicherkonto, und klicken Sie dann auf **Schlüssel verwalten**.

5. Notieren Sie sich **Speicherkontoname** und **Zugriffsschlüssel**.

6. Klicken Sie im mobilen Dienst auf die Registerkarte **Konfigurieren**, scrollen Sie nach unten zu **Verbindungszeichenfolgen**, und geben Sie eine neue Verbindungszeichenfolge mit dem **Namen** `StorageConnectionString` und einem **Wert**, der die Verbindungszeichenfolge Ihres Speicherkontos ist, in folgendem Format ein.

		DefaultEndpointsProtocol=https;AccountName=<ACCOUNT_NAME>;AccountKey=<ACCESS_KEY>;

	![](./media/mobile-services-dotnet-backend-store-data-table-storage/mobile-blob-storage-app-settings.png)

7. Ersetzen Sie in der oben dargestellten Zeichenfolge die Werte von `<ACCOUNT_NAME>` und `<ACCESS_KEY>` durch die Werte aus dem Portal, und klicken Sie dann auf **Speichern**.

	Die Verbindungszeichenfolge des Speicherkontos wird verschlüsselt in den App-Einstellungen gespeichert. Sie können zur Laufzeit in jedem Tabellen-Controller auf diese Zeichenfolge zugreifen.

8. Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei "Web.config" für das Projekt mit dem mobilen Dienst, und fügen Sie die folgende neue Verbindungszeichenfolge hinzu:

		<add name="StorageConnectionString" connectionString="<STORAGE_CONNECTION_STRING>" />

9. Ersetzen Sie den Platzhalter `<STORAGE_CONNECTION_STRING>` durch die Verbindungszeichenfolge aus Schritt 6.

	Der mobile Dienst verwendet diese Verbindungszeichenfolge, wenn er auf dem lokalen Computer ausgeführt wird, sodass Sie den Code testen können, bevor Sie ihn veröffentlichen. Wenn der mobile Dienst in Azure ausgeführt wird, verwendet er stattdessen den im Portal festgelegten Verbindungszeichenfolgen-Wert und ignoriert die Verbindungszeichenfolge im Projekt.

## <a name="modify-service"></a>Ändern von Datentypen und Tabellencontrollern

Da das TodoList-Schnellstartprojekt für die Ausführung mit einer SQL-Datenbank unter Verwendung von Entity Framework vorgesehen ist, müssen Sie im Projekt einige Aktualisierungen vornehmen, damit es mit Tabellenspeicher verwendet werden kann.

1. Ändern Sie den **TodoItem**-Datentyp wie folgt, sodass er von **StorageData** anstelle von **EntityData** abgeleitet wird.

	    public class TodoItem : StorageData
	    {
	        public string Text { get; set; }
	        public bool Complete { get; set; }
	    }

	>[AZURE.NOTE]Der Typ **StorageData** verfügt über eine Id-Eigenschaft, die einen zusammengesetzten Schlüssel erfordert. Dieser ist eine Zeichenfolge im Format *partitionId*,*rowValue*.

2. Fügen Sie in **TodoItemController** die folgende using-Anweisung hinzu.

		using System.Web.Http.OData.Query;
		using System.Collections.Generic;

3. Ersetzen Sie die **Initialize**-Methode von **TodoItemController** durch folgenden Code.

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Create a new Azure Storage domain manager using the stored
            // connection string and the name of the table exposed by the controller.
            string connectionStringName = "StorageConnectionString";
            var tableName = controllerContext.ControllerDescriptor.ControllerName.ToLowerInvariant();
            DomainManager = new StorageDomainManager<TodoItem>(connectionStringName,
                tableName, Request, Services);
        }

	Hierdurch wird mit der Verbindungszeichenfolge für das Speicherkonto ein neuer Speicherdomänen-Manager für den angeforderten Controller erstellt.

3. Ersetzen Sie die vorhandene **GetAllTodoItems**-Methode durch folgenden Code.

		public Task<IEnumerable<TodoItem>> GetAllTodoItems(ODataQueryOptions options)
        {
            // Call QueryAsync, passing the supplied query options.
            return DomainManager.QueryAsync(options);
        }

	Im Gegensatz zu einer SQL-Datenbank gibt diese Version nicht IQueryable<TEntity> zurück. Daher kann das Ergebnis an eine Abfrage gebunden, jedoch nicht in eine weitere Abfrage eingefügt werden.

## Aktualisieren der Client-App

Sie müssen auf Clientseite eine Änderung vornehmen, damit die Schnellstart-App mit dem .NET-Back-End, das den Tabellenspeicher verwendet, genutzt werden kann. Der Grund hierfür ist der Verbundschlüssel, der vom Tabellenspeicheranbieter erwartet wird.

1. Öffnen Sie die Clientcodedatei, die den Datenzugriffscode enthält, und suchen Sie die Methode, in der der Einfügevorgang ausgeführt wird.

2. Aktualisieren Sie die TodoItem-Instanz, die hinzugefügt wird, um das Id-Feld explizit im Zeichenfolgenformat `<partitionID>,<rowValue>` festzulegen.

	Dieses Beispiel zeigt, wie die ID in einer C#-App festgelegt werden kann, wo der partition-Teil konstant ist und der row-Teil auf einer GUID basiert.

		 todoItem.Id = string.Format("partition,{0}", Guid.NewGuid());

Sie können jetzt die App testen.

## <a name="test-application"></a>Testen der Anwendung

1. (Optional) Veröffentlichen Sie Ihr Mobile Services-Back-End-Projekt erneut.

	Sie können auch den mobilen Dienst lokal testen, bevor Sie das .NET-Back-End-Projekt in Azure veröffentlichen. Der mobile Dienst verwendet den Azure-Tabellenspeicher, egal ob Sie ihn lokal oder in Azure testen.

4. Führen Sie die Schnellstart-Client-App aus, die mit dem mobilen Dienst verbunden ist.

	Beachten Sie, dass Elemente, die Sie zuvor mit dem Schnellstart-Lernprogramm hinzugefügt haben, nicht angezeigt werden. Der Grund dafür ist, dass der Tabellenspeicher derzeit leer ist.

5. Fügen Sie neue Elemente hinzu, um Datenbankänderungen zu generieren.

	Das Verhalten der App und des mobilen Diensts bleibt unverändert, mit der Ausnahme, dass jetzt die Daten im nicht relationalen Speicher statt in der SQL-Datenbank gespeichert werden.

##Nächste Schritte

Da Sie jetzt gesehen haben, wie einfach sich der Tabellenspeicher mit dem .NET Back-End verwenden lässt, sollten Sie weitere Optionen für den Back-End-Speicher untersuchen:

+ [Herstellen einer Verbindung mit einem lokalen SQL Server mithilfe von Hybridverbindungen](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Hybridverbindungen ermöglichen Ihnen das Herstellen einer sicheren Verbindung des mobilen Diensts mit den lokalen Ressourcen. Auf diese Weise können Sie Ihre lokalen Daten mithilfe von Azure für Ihre mobilen Clients zugänglich machen. Unterstützt werden unter anderem alle Ressourcen, die auf einem statischen TCP-Port laufen, inklusive Microsoft SQL Server, MySQL, HTTP Web-APIs und die meisten selbst entwickelten Webdienste.

+ [Hochladen von Bildern in den Azure-Speicher mithilfe von Mobile Services](mobile-services-dotnet-backend-windows-universal-dotnet-upload-data-blob-storage.md)</br>In diesem Thema wird gezeigt, wie Sie das TodoList-Beispielprojekt erweitern, damit Sie Bilder von Ihrer App in den Azure-BLOB-Speicher hochladen können.

<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[klassischen Azure-Portal]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab Add-on Page]: /gallery/store/mongolab/mongolab

<!---HONumber=AcomDC_0218_2016-->