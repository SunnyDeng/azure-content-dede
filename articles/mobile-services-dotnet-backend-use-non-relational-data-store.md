<properties urlDisplayName="Build a Service Using a Non-Relational Data Store" pageTitle="Erstellen eines Diensts mit einem nicht-relationalen Datenspeicher - Azure Mobile Services" metaKeywords="" description="Learn how to use a non-relational data store such as MongoDB or Azure Table Storage with your .NET based mobile service" metaCanonical="" services="" documentationCenter="Mobile" title="Build a Service Using a Non-Relational Data Store" authors="yavorg, mahender" solutions="" manager="dwrede" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="yavorg, mahender" />

# Erstellen eines Diensts mit MongoDB als Datenspeicher mit .NET-Back-End

In diesem Thema erfahren Sie, wie Sie einen nicht relationalen Datenspeicher für Ihren mobilen Dienst verwenden. In diesem Lernprogramm bearbeiten Sie das Mobile Services-Schnellstartprojekt, sodass es MongoDB statt SQL als Datenspeicher verwendet.

Dieses Lernprogramm erläutert die grundlegenden Schritte zum Einrichten eines nicht relationalen Speichers:

1. [Erstellen eines nicht relationalen Speichers]
2. [Ändern von Daten und Controllern]
3. [Testen der Anwendung]

Voraussetzung für dieses Lernprogramm ist der Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten].

## <a name="create-store"></a>Erstellen eines nicht relationalen Speichers

1. Klicken Sie im [Azure-Verwaltungsportal] auf **Neu** und dann auf **Speicher**.

2. Wählen Sie das Add-On **MongoLab** aus, und navigieren Sie durch den Assistenten, um sich für ein Konto zu registrieren. Weitere Informationen zu MongoLab finden Sie auf der [MongoLab Add-On-Seite].

    ![][0]

2. Nachdem das Konto eingerichtet wurde, wählen Sie **Verbindungsinformationen** aus und kopieren die Verbindungszeichenfolge.

3. Navigieren Sie zum Abschnitt Mobile Services des Portals, und wählen Sie die Registerkarte **Konfigurieren** aus.

4. Geben Sie unter **App-Einstellungen** die Verbindungszeichenfolge mit dem Schlüssel "MongoConnectionString" ein, und klicken Sie auf **Speichern**.

    ![][1]

2. Fügen Sie folgenden Code zu `TodoItemController` hinzu:

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
    
    Dieser Code lädt die Anwendungseinstellung und teilt dem mobilen Dienst mit, ihn als Verbindung zu behandeln, der von einem `TableController` verwendet werden kann. Später rufen Sie diese Methode beim Aufruf von `TodoItemController` auf.



## <a name="modify-service"></a>Ändern von Daten und Controllern

1. Installieren Sie das **WindowsAzure.MobileServices.Backend.Mongo** NuGet-Paket.

2. Ändern Sie `TodoItem` zur Ableitung von `DocumentData` statt von `EntityData`.

        public class TodoItem : DocumentData
        {
            public string Text { get; set; }

            public bool Complete { get; set; }
        }

3. Ersetzen Sie in `TodoItemController` die `Initialize`-Methode durch Folgendes:

        protected override async void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);
            string connectionStringName = "MongoConnectionString";
            string databaseName = "<YOUR-DATABASE-NAME>";
            string collectionName = "todoItems";
            InitializeConnectionString(connectionStringName);
            DomainManager = new MongoDomainManager<TodoItem>(connectionStringName, databaseName, collectionName, Request, Services);
        }

4. Ersetzen Sie im Code für die oben angegebene `Initialize`-Methode die Angabe **YOUR-DATABASE-NAME** durch den Namen, den Sie bei der Bereitstellung des MongoLab-Add-Ons verwendet haben.


## <a name="test-application"></a>Testen der Anwendung

1. Veröffentlichen Sie Ihr Mobile Services Backend-Projekt erneut.

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
[Was ist der Tabellendienst?]: /de-de/documentation/articles/storage-dotnet-how-to-use-tables/#what-is
[MongoLab Add-On-Seite]: /de-de/gallery/store/mongolab/mongolab
