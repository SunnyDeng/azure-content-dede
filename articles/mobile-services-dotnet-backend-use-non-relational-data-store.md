<properties 
	pageTitle="Erstellen eines Diensts mit einem nicht-relationalen Datenspeicher - Azure Mobile Services" 
	description="Erfahren Sie, wie einen nicht relationalen Datenspeicher wie MongoDB oder Azure-Tabellenspeicher mit Ihrem .NET-basierten mobilen Dienst verwenden." 
	services="" 
	documentationCenter="windows" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="mollybos"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="mahender"/>

# Erstellen eines Diensts mit MongoDB als Datenspeicher mit .NET-Back-End

In diesem Thema erfahren Sie, wie Sie einen nicht relationalen Datenspeicher für Ihren mobilen Dienst verwenden. In diesem Lernprogramm bearbeiten Sie das Mobile Services-Schnellstartprojekt, sodass es MongoDB statt SQL als Datenspeicher verwendet.

Dieses Lernprogramm erläutert die grundlegenden Schritte zum Einrichten eines nicht relationalen Speichers:

1. [Erstellen eines nicht relationalen Speichers]
2. [Ändern von Daten und Controllern]
3. [Testen der Anwendung]

Voraussetzung für dieses Lernprogramm ist der Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten].

## <a name="create-store"></a>Erstellen eines nicht relationalen Speichers

1. Klicken Sie im [Azure-Verwaltungsportal] auf **Neu** und dann auf **Speicher**.

2. Wählen Sie das Add-On **MongoLab** aus, und navigieren Sie durch den Assistenten, um sich für ein Konto zu registrieren. Weitere Informationen zu MongoLab finden Sie auf der [MongoLab-Add-On-Seite].

    ![][0]

2. Nachdem das Konto eingerichtet wurde, wählen Sie **Verbindungsinformationen** aus und kopieren die Verbindungszeichenfolge.

3. Navigieren Sie zum Abschnitt "Mobile Services" des Portals, und wählen Sie die Registerkarte **Konfigurieren** aus.

4. Geben Sie unter **App-Einstellungen** die Verbindungszeichenfolge mit dem Schlüssel "MongoConnectionString" ein, und klicken Sie auf **Speichern**.

    ![][1]

2. Fügen Sie den folgenden Code zu  `TodoItemController` hinzu:

        static bool connectionStringInitialized = false;

        private void InitializeConnectionString(string connectionStringName)
        {
            if (!connectionStringInitialized)
            {
                connectionStringInitialized = true;
                if (!this.Services.Settings.Connections.ContainsKey(connectionStringName))
                {
                    var connectionString = this.Services.Settings[connectionStringName];
                    var connectionSetting = new ConnectionSettings(connectionStringName, connectionString);
                    this.Services.Settings.Connections.Add(connectionStringName, connectionSetting);
                }
            }
        }
    
    Dieser Code lädt die Anwendungseinstellung und teilt dem mobilen Dienst mit, ihn als Verbindung zu behandeln, der von einem  `TableController` verwendet werden kann. Später rufen Sie diese Methode beim Aufruf von  `TodoItemController` auf.



## <a name="modify-service"></a>Ändern von Daten und Controllern

1. Installieren Sie das **WindowsAzure.MobileServices.Backend.Mongo**-NuGet-Paket.

2. Ändern Sie  `TodoItem` zur Ableitung von  `DocumentData` statt von  `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. Ersetzen Sie in  `TodoItemController` die  `Initialize`-Methode durch:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. Ersetzen Sie im oben angegebenen Code für die  `Initialize`-Methode **YOUR-DATABASE-NAME** durch den bei der Bereitstellung des MongoLab-Add-Ons ausgewählten Namen.


## <a name="test-application"></a>Testen der Anwendung

1. Veröffentlichen Sie Ihr Mobile Services-Back-End-Projekt erneut.

2. Führen Sie die Clientanwendung aus. Sie werden feststellen, dass keine Objekte mehr angezeigt werden, die zuvor in der SQL-Datenbank aus dem Schnellstart-Lernprogramm gespeichert waren.

3. Erstellen Sie ein neues Objekt. Die App sollte sich wie zuvor verhalten, außer dass die Daten sich jetzt in Ihrem nicht relationalen Speicher befinden.


<!-- Anchors. -->
[Erstellen eines nicht relationalen Speichers]: #create-store
[Ändern von Daten und Controllern]: #modify-service
[Testen der Anwendung]: #test-application


<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/create-mongo-lab.png
[1]: ./media/mobile-services-dotnet-backend-use-non-relational-data-store/mongo-connection-string.png


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Was ist der Tabellenspeicherdienst?]: /de-de/documentation/articles/storage-dotnet-how-to-use-tables/#what-is
[MongoLab-Add-On-Seite]: /de-de/gallery/store/mongolab/mongolab

<!--HONumber=42-->
