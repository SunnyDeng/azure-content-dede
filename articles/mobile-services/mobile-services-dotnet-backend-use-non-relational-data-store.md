<properties 
	pageTitle="Erstellen eines Diensts mit einem nicht-relationalen Datenspeicher | Azure Mobile Services" 
	description="Erfahren Sie, wie einen nicht relationalen Datenspeicher wie MongoDB oder Azure-Tabellenspeicher mit Ihrem .NET-basierten mobilen Dienst verwenden." 
	services="mobile-services" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="mahender"/>

# Erstellen eines mobilen .NET-Back-End-Diensts, der MongoDB anstelle einer SQL-Datenbank als Speicher verwendet

In diesem Thema erfahren Sie, wie Sie einen nicht relationalen Datenspeicher für Ihren mobilen .NET-Back-End-Dienst verwenden. In diesem Lernprogramm bearbeiten Sie das Mobile Services-Schnellstartprojekt, sodass es MongoDB statt des standardmäßigen Azure-SQL-Datenbank-Datenspeichers verwendet.

Voraussetzung für dieses Lernprogramm ist der Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Hinzufügen von Mobile Services zu einer vorhandenen App]. Sie müssen außerdem Ihrem Abonnement den Dienst MongoLab hinzufügen.

## <a name="create-store"></a>Erstellen des nicht relationalen MongoLab-Speichers

1. Klicken Sie im [Azure-Verwaltungsportal] auf **Neu** und dann auf **Marketplace**.

2. Wählen Sie das Add-On **MongoLab** aus, und schließen Sie den Assistenten ab, um sich für ein MongoLab-Konto zu registrieren.

	Weitere Informationen zu MongoLab finden Sie auf der [MongoLab-Add-On-Seite].

2. Nachdem das Konto eingerichtet wurde, wählen Sie **Verbindungsinformationen** aus und kopieren die Verbindungszeichenfolge.

3. Klicken Sie im mobilen Dienst auf die Registerkarte **Konfigurieren**, scrollen Sie nach unten zu **Verbindungszeichenfolgen**, und geben Sie eine neue Verbindungszeichenfolge mit dem **Namen** `MongoConnectionString` und einem **Wert** ein, der die MongoDB-Verbindung darstellt. Klicken Sie anschließend auf **Speichern**.

	![Hinzufügen der MongoDB-Verbindungszeichenfolge](./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png)

	Die Verbindungszeichenfolge des Speicherkontos wird verschlüsselt in den App-Einstellungen gespeichert. Sie können zur Laufzeit in jedem Tabellen-Controller auf diese Zeichenfolge zugreifen.

8. Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei "Web.config" für das Projekt mit dem mobilen Dienst, und fügen Sie die folgende neue Verbindungszeichenfolge hinzu:

		<add name="MongoConnectionString" connectionString="<MONGODB_CONNECTION_STRING>" />

9. Ersetzen Sie den Platzhalter `<MONGODB_CONNECTION_STRING>` durch die MongoDB-Verbindungszeichenfolge.

	Der mobile Dienst verwendet diese Verbindungszeichenfolge, wenn er auf dem lokalen Computer ausgeführt wird, sodass Sie den Code testen können, bevor Sie ihn veröffentlichen. Wenn der mobile Dienst in Azure ausgeführt wird, verwendet er stattdessen den im Portal festgelegten Verbindungszeichenfolgen-Wert und ignoriert die Verbindungszeichenfolge im Projekt.

## <a name="modify-service"></a>Ändern von Datentypen und Tabellen-Controllern

1. Installieren Sie das **WindowsAzure.MobileServices.Backend.Mongo** NuGet-Paket.

2. Ändern Sie **TodoItem**, sodass es von **DocumentData** anstelle von **EntityData** abgeleitet wird.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. Ersetzen Sie in **TodoItemController** die **Initialize**-Methode durch Folgendes:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, 
				collectionName, Request, Services);
        }

4. Ersetzen Sie im Code für die oben angegebene **Initialize**-Methode `<YOUR-DATABASE-NAME>` durch den Namen, den Sie bei der Bereitstellung des MongoLab-Add-Ons verwendet haben.

Sie können jetzt die App testen.

## <a name="test-application"></a>Testen der Anwendung

1. (Optional) Veröffentlichen Sie Ihr Mobile Services-Back-End-Projekt erneut.

	Sie können auch den mobilen Dienst lokal testen, bevor Sie das .NET-Back-End-Projekt in Azure veröffentlichen. Der mobile Dienst verwendet MongoDB als Speicher, egal ob Sie ihn lokal oder in Azure testen.

4. Verwenden Sie entweder wie zuvor die Schaltfläche **Ausprobieren** auf der Startseite, oder verwenden Sie eine mit der mobilen App verbundene Client-App, um Elemente in der Datenbank abzufragen.
 
	Sie werden feststellen, dass keine Objekte mehr angezeigt werden, die zuvor in der SQL-Datenbank aus dem Schnellstart-Lernprogramm gespeichert waren.

	>[AZURE.NOTE]Bei Verwendung der Schaltfläche **Ausprobieren** zum Starten der Hilfe-API-Seiten, denken Sie daran, Ihren Anwendungsschlüssel als Kennwort (mit einem leeren Benutzernamen) anzugeben.

3. Erstellen Sie ein neues Objekt.

	Das Verhalten der App und des mobilen Diensts bleibt unverändert, mit der Ausnahme, dass jetzt die Daten im nicht relationalen Speicher statt in der SQL-Datenbank gespeichert werden.

##Nächste Schritte

Da Sie jetzt gesehen haben, wie einfach sich der Tabellenspeicher mit dem .NET Back-End verwenden lässt, sollten Sie weitere Optionen für den Back-End-Speicher untersuchen:

+ [Erstellen eines mobilen .NET-Back-End-Diensts, der den Tabellenspeicher anstelle einer SQL-Datenbank verwendet ](mobile-services-dotnet-backend-store-data-table-storage.md)</br>Wie in dem Lernprogramm, das Sie gerade abgeschlossen haben, wird in diesem Thema gezeigt, wie ein nicht relationaler Datenspeicher für einen mobilen Dienst verwendet wird. In diesem Lernprogramm bearbeiten Sie das Mobile Services-Schnellstartprojekt, sodass es den Azure-Speicher statt der SQL-Datenbank als Datenspeicher verwendet.
 
+ [Herstellen einer Verbindung mit einem lokalen SQL Server mithilfe von Hybridverbindungen](mobile-services-dotnet-backend-hybrid-connections-get-started.md)</br>Hybridverbindungen ermöglichen Ihnen das Herstellen einer sicheren Verbindung des mobilen Diensts mit den lokalen Ressourcen. Auf diese Weise können Sie Ihre lokalen Daten mithilfe von Azure für Ihre mobilen Clients zugänglich machen. Unterstützt werden unter anderem alle Ressourcen, die auf einem statischen TCP-Port laufen, inklusive Microsoft SQL Server, MySQL, HTTP Web-APIs und die meisten selbst entwickelten Webdienste.

+ [Hochladen von Bildern in den Azure-Speicher mithilfe von Mobile Services](mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage.md)</br>In diesem Thema wird gezeigt, wie Sie das TodoList-Beispielprojekt erweitern, damit Sie Bilder von Ihrer App in den Azure-BLOB-Speicher hochladen können.


<!-- Anchors. -->
[Create a non-relational store]: #create-store
[Modify data and controllers]: #modify-service
[Test the application]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Hinzufügen von Mobile Services zu einer vorhandenen App]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-data.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[What is the Table Service]: ../storage-dotnet-how-to-use-tables.md#what-is
[MongoLab-Add-On-Seite]: /gallery/store/mongolab/mongolab
 

<!---HONumber=August15_HO6-->