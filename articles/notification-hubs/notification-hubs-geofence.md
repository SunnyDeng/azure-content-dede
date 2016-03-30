<properties
	pageTitle="Geofencing-Pushbenachrichtigungen mit Azure Notification Hubs und Bing Spatial Data | Microsoft Azure"
	description="In diesem Tutorial erfahren Sie, wie Sie standortbasierte Pushbenachrichtigungen mit Azure Notification Hubs und Bing Spatial Data bereitstellen."
	services="notification-hubs"
	documentationCenter="windows"
    keywords="Pushbenachrichtigungen,Pushbenachrichtigung"
	authors="dend"
	manager="yuaxu"
	editor="dend"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/16/2016"
	ms.author="dendeli"/>
    
# Geofencing-Pushbenachrichtigungen mit Azure Notification Hubs und Bing Spatial Data
 
 > [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02).

In diesem Tutorial erfahren Sie, wie Sie standortbasierte Pushbenachrichtigungen mit Azure Notification Hubs und Bing Spatial Data aus einer Anwendung der universellen Windows-Plattform bereitstellen.

##Voraussetzungen
Zunächst müssen Sie sicherstellen, dass alle Voraussetzungen in Bezug auf Software und Dienste erfüllt sind:

* [Visual Studio 2015 Update 1](https://www.visualstudio.com/de-DE/downloads/download-visual-studio-vs.aspx) oder höher ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409) ist ebenfalls geeignet) 
* Neueste Version des [Azure SDK](https://azure.microsoft.com/downloads/) 
* [Bing Maps Dev Center-Konto](https://www.bingmapsportal.com/) (Sie können kostenlos ein Konto erstellen und Ihrem Microsoft-Konto zuordnen) 

##Erste Schritte

Erstellen Sie als Erstes das Projekt. Starten Sie in Visual Studio ein neues Projekt vom Typ **Leere App (Universelle Windows-App)**.

![](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

Nachdem die Projekterstellung abgeschlossen ist, sollten Sie über das Grundgerüst der App verfügen. Nun führen wir die Einrichtung für die Geofencing-Infrastruktur durch. Da wir hierfür Bing-Dienste verwenden, ist ein öffentlicher REST-API-Endpunkt vorhanden, mit dem wir spezielle Standortrahmen abfragen:

    http://spatial.virtualearth.net/REST/v1/data/
    
Hierfür müssen Sie die folgenden Parameter angeben:

* **Datenquellen-ID** und **Datenquellenname**: In der Bing Maps-API enthalten Datenquellen Metadaten in verschiedenen „Buckets“, z.B. Standorte und Geschäftszeiten. Weitere Informationen hierzu finden Sie hier. 
* **Entitätsname**: Gibt die Entität an, die Sie als Referenzpunkt für die Benachrichtigung verwenden möchten. 
* **Bing Maps-API-Schlüssel**: Dies ist der Schlüssel, den Sie beim Erstellen des Bing Dev Center-Kontos abgerufen haben.
 
Wir sehen uns die Einrichtung der obigen Elemente nun genauer an.

##Einrichten der Datenquelle

Hierfür können Sie das Bing Maps Dev Center verwenden. Klicken Sie in der oberen Navigationsleiste einfach auf **Datenquellen**, und wählen Sie **Datenquellen verwalten**.

![](./media/notification-hubs-geofence/bing-maps-manage-data.png)

Falls Sie noch nicht mit der Bing Maps-API gearbeitet haben, sind wahrscheinlich keine Datenquellen vorhanden. Sie können also einfach eine neue Datenquelle erstellen, indem Sie auf „Daten in eine Datenquelle hochladen“ klicken. Stellen Sie sicher, dass Sie alle erforderlichen Felder ausfüllen:

![](./media/notification-hubs-geofence/bing-maps-create-data.png)

Vielleicht stellen Sie sich folgende Frage: Wie sieht die Datendatei aus, und was muss hochgeladen werden? Für diesen Test können wir das Pipe-Beispiel verwenden, mit dem ein Bereich im Hafen von San Francisco eingerahmt wird:

    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    
Der obige Code steht für diese Entität:

![](./media/notification-hubs-geofence/bing-maps-geofence.png)

Kopieren Sie einfach die obige Zeichenfolge, und fügen Sie sie in eine neue Datei ein. Speichern Sie die Datei unter dem Namen **NotificationHubsGeofence.pipe**, und laden Sie sie in Bing Dev Center hoch.

>[AZURE.NOTE]Unter Umständen werden Sie aufgefordert, einen neuen Schlüssel als **Hauptschlüssel** anzugeben, der sich vom **Abfrageschlüssel** unterscheidet. Erstellen Sie im Dashboard einfach einen neuen Schlüssel, und aktualisieren Sie die Seite zum Hochladen der Datenquelle.

Nachdem Sie die Datendatei hochgeladen haben, müssen Sie sicherstellen, dass Sie die Datenquelle veröffentlichen.

Wechseln Sie wie weiter oben zu **Datenquellen verwalten**, suchen Sie in der Liste nach Ihrer Datenquelle, und klicken Sie in der Spalte **Aktionen** auf **Veröffentlichen**. Nach einer kurzen Wartezeit sollte Ihre Datenquelle auf der Registerkarte **Veröffentlichte Datenquellen** angezeigt werden:

![](./media/notification-hubs-geofence/bing-maps-published-data.png)

Wenn Sie auf **Bearbeiten** klicken, können Sie auf einen Blick sehen, welche Standorte enthalten sind:

![](./media/notification-hubs-geofence/bing-maps-data-details.png)

An diesem Punkt werden die Grenzen für den von uns erstellten Geofence nicht angezeigt. Wir benötigen lediglich eine Bestätigung, dass sich der angegebene Standort in der richtigen Umgebung befindet.

Sie verfügen nun über alle Anforderungen für die Datenquelle. Klicken Sie zum Abrufen der Details zur Anforderungs-URL für den API-Aufruf im Bing Maps Dev Center auf **Datenquellen**, und wählen Sie **Datenquelleninformationen**.

![](./media/notification-hubs-geofence/bing-maps-data-info.png)

Wir benötigen hier die **Abfrage-URL**. Dies ist der Endpunkt, für den wir Abfragen ausführen können, um zu überprüfen, ob sich das Gerät derzeit innerhalb der Grenzen eines Standorts befindet. Zum Durchführen dieser Überprüfung müssen wir nur einen GET-Aufruf mit den folgenden angefügten Parametern für die Abfrage-URL ausführen:

    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY

So geben Sie einen Zielpunkt an, den wir vom Gerät abrufen. Bing Maps führt dann automatisch die Berechnungen durch, um zu ermitteln, ob er sich im Geofence befindet. Nachdem Sie die Anforderung über einen Browser (oder cURL) ausgeführt haben, erhalten Sie eine standardmäßige JSON-Antwort:

![](./media/notification-hubs-geofence/bing-maps-json.png)

Diese Antwort erfolgt nur, wenn sich der Punkt tatsächlich innerhalb der festgelegten Grenzen befindet. Wenn nicht, erhalten Sie einen leeren **results**-Bucket:

![](./media/notification-hubs-geofence/bing-maps-nores.png)

##Einrichten der UWP-Anwendung

Da die Datenquelle jetzt bereitsteht, können wir mit der Arbeit an der UWP-Anwendung beginnen, für die wir zuvor das Bootstrapping durchgeführt haben.

Zuallererst müssen wir die Standortdienste für die Anwendung aktivieren. Doppelklicken Sie hierzu im **Projektmappen-Explorer** auf die Datei `Package.appxmanifest`.

![](./media/notification-hubs-geofence/vs-package-manifest.png)

Klicken Sie auf der Registerkarte mit den Paketeigenschaften, die geöffnet wird, auf **Funktionen**, und aktivieren Sie **Standort**:

![](./media/notification-hubs-geofence/vs-package-location.png)

Da die Standortfunktion nun deklariert wurde, können Sie in Ihrer Projektmappe einen neuen Ordner mit dem Namen `Core` erstellen und diesem dann eine neue Datei mit dem Namen `LocationHelper.cs` hinzufügen:

![](./media/notification-hubs-geofence/vs-location-helper.png)

Die eigentliche `LocationHelper`-Klasse ist bisher noch sehr einfach aufgebaut. Sie ermöglicht es uns nur, den Benutzerstandort über die System-API abzurufen:

    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }

Weitere Informationen zum Abrufen des Benutzerstandorts in UWP-Apps finden Sie im offiziellen [MSDN-Dokument](https://msdn.microsoft.com/library/windows/apps/mt219698.aspx).

Um zu überprüfen, ob die Standorterfassung funktioniert, öffnen Sie die Codeseite der Hauptseite (`MainPage.xaml.cs`). Erstellen Sie einen neuen Ereignishandler für das `Loaded`-Ereignis im `MainPage`-Konstruktor:

    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }

Die Implementierung des Ereignishandlers lautet wie folgt:

    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }

Beachten Sie, dass wir den Handler als „async“ deklariert haben, da `GetCurrentLocation` „awaitable“ ist und daher in einem asynchronen Kontext ausgeführt werden muss. Da es unter bestimmten Umständen auch zu einem NULL-Standort kommen kann (wenn z.B. die Standortdienste deaktiviert sind oder die Anwendung keine Berechtigungen für den Zugriff auf den Standort erhalten hat), müssen wir sicherstellen, dass dieser Fall per NULL CHECK richtig behandelt wird.

Führen Sie die Anwendung aus. Achten Sie darauf, dass Sie den Standortzugriff zulassen:

![](./media/notification-hubs-geofence/notification-hubs-location-access.png)

Nach dem Starten der Anwendung sollten die Koordinaten im Fenster **Ausgabe** angezeigt werden:

![](./media/notification-hubs-geofence/notification-hubs-location-output.png)

Jetzt wissen Sie, dass die Standorterfassung funktioniert. Sie können den Testereignishandler für „Loaded“ jetzt entfernen, da er nicht mehr benötigt wird.

Der nächste Schritt ist das Erfassen der Standortänderungen. Hierfür wechseln wir zurück zur `LocationHelper`-Klasse und fügen den Ereignishandler für `PositionChanged` hinzu:

    geolocator.PositionChanged += Geolocator_PositionChanged;

Die Implementierung zeigt die Standortkoordinaten im Fenster **Ausgabe** an:

    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }

##Einrichten des Back-Ends

Laden Sie das [.NET-Back-End-Beispiel von GitHub herunter](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers). Öffnen Sie nach Abschluss des Downloads den Ordner `NotifyUsers` und anschließend die Datei `NotifyUsers.sln`.

Geben Sie das Projekt `AppBackend` mit der Option **Als Startprojekt festlegen** als Startprojekt an, und starten Sie es.

![](./media/notification-hubs-geofence/vs-startup-project.png)

Das Projekt ist bereits zum Senden von Pushbenachrichtigungen an Zielgeräte konfiguriert, sodass wir nur zwei Dinge tun müssen: die richtige Verbindungszeichenfolge für den Notification Hub verwenden und eine Grenzidentifizierung hinzufügen, um die Benachrichtigung nur dann zu senden, wenn sich der Benutzer im Geofence befindet.

Öffnen Sie zum Konfigurieren der Verbindungszeichenfolge im Ordner `Models` die Datei `Notifications.cs`. Die Funktion `NotificationHubClient.CreateClientFromConnectionString` sollte die Informationen zu Ihrem Notification Hub enthalten, die Sie im [Azure-Portal](https://portal.azure.com) abrufen können (Blatt **Zugriffsrichtlinien** in den **Einstellungen**). Speichern Sie die aktualisierte Konfigurationsdatei.

Jetzt müssen wir ein Modell für das Bing Maps-API-Ergebnis erstellen. Die einfachste Möglichkeit ist ein Rechtsklick auf den Ordner `Models` und das Wählen der Option **Hinzufügen** > **Klasse**. Geben Sie dem Projekt den Namen `GeofenceBoundary.cs`. Kopieren Sie anschließend den JSON-Code aus der API-Antwort, die im ersten Abschnitt beschrieben wurde, und verwenden Sie in Visual Studio die Option **Bearbeiten** > **Inhalte einfügen** > **JSON als Klassen einfügen**.

Auf diese Weise wird sichergestellt, dass das Objekt genau wie gewünscht deserialisiert wird. Der sich ergebende Klassensatz sollte wie folgt aussehen:

    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }

Öffnen Sie nun `Controllers` > `NotificationsController.cs`. Wir müssen den Post-Aufruf anpassen, um den Längengrad und Breitengrad des Ziels abzudecken. Fügen Sie der Funktionssignatur hierzu einfach zwei Zeichenfolgen hinzu: `latitude` und `longitude`.

    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)

Erstellen Sie im Projekt eine neue Klasse mit dem Namen `ApiHelper.cs`. Wir verwenden sie zum Herstellen der Verbindung mit Bing, um Grenzschnittpunkte zu überprüfen. Implementieren Sie wie folgt die Funktion `IsPointWithinBounds`:

    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }

>[AZURE.NOTE] Stellen Sie sicher, dass Sie den API-Endpunkt durch die Abfrage-URL ersetzen, die Sie weiter oben aus Bing Dev Center abgerufen haben (dasselbe gilt für den API-Schlüssel).

Wenn die Abfrage zu einem Ergebnis führt, bedeutet dies, dass sich der angegebene Punkt innerhalb der Grenzen des Geofence befindet. Wir geben also `true` zurück. Wenn keine Ergebnisse vorliegen, teilt Bing uns mit, dass sich der Punkt außerhalb des Suchbereichs befindet. Wir geben `false` zurück.

Wechseln Sie zurück zu `NotificationsController.cs`, und erstellen Sie direkt vor der switch-Anweisung eine Prüfung:

    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }

Auf diese Weise wird die Benachrichtigung nur gesendet, wenn sich der Punkt innerhalb der Grenzen befindet.

##Testen von Pushbenachrichtigungen in der UWP-App

In der UWP-App sollte es jetzt möglich sein, Benachrichtigungen zu testen. Erstellen Sie in der `LocationHelper`-Klasse die neue Funktion `SendLocationToBackend`:

    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent(""" + message + """,
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }

>[AZURE.NOTE] Tauschen Sie die `POST_URL` durch den Standort Ihrer bereitgestellten Webanwendung aus, die wir im vorherigen Abschnitt erstellt haben. Die lokale Ausführung ist vorerst in Ordnung. Wenn Sie an der Bereitstellung einer öffentlichen Version arbeiten, müssen Sie diese aber über einen externen Anbieter hosten.

Als Nächstes stellen wir sicher, dass wir die UWP-App für Pushbenachrichtigungen registrieren. Klicken Sie in Visual Studio auf **Projekt** > **Store** > **App mit Store verknüpfen**.

![](./media/notification-hubs-geofence/vs-associate-with-store.png)

Stellen Sie nach dem Anmelden an Ihrem Entwicklerkonto sicher, dass Sie eine vorhandene App auswählen oder eine neue App erstellen und ihr das Paket zuordnen.

Navigieren Sie zum Dev Center, und öffnen Sie die gerade erstellte App. Klicken Sie auf **Dienste** > **Pushbenachrichtigungen** > **Live Services-Website**.

![](./media/notification-hubs-geofence/ms-live-services.png)

Notieren Sie sich von der Website den **geheimen Schlüssel der Anwendung** und die **Paket-SID**. Sie benötigen beide Angaben im Azure-Portal. Öffnen Sie Ihren Notification Hub, klicken Sie auf **Einstellungen** > **Notification Services** > **Windows (WNS)**, und geben Sie die Informationen in die erforderlichen Felder ein.

![](./media/notification-hubs-geofence/notification-hubs-wns.png)

Klicken Sie auf **Speichern**.

Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise**, und wählen Sie **NuGet-Pakete verwalten**. Wir müssen einen Verweis auf die **verwaltete Microsoft Azure Service Bus-Bibliothek** hinzufügen. Suchen Sie einfach nach `WindowsAzure.Messaging.Managed`, und fügen Sie das Element dem Projekt hinzu.

![](./media/notification-hubs-geofence/vs-nuget.png)

Zu Testzwecken können wir den Ereignishandler `MainPage_Loaded` noch einmal erstellen und ihm diesen Codeausschnitt hinzufügen:

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }

Mit dem obigen Code wird die App beim Notification Hub registriert. Sie sind fertig!

Im `LocationHelper`-Element im Handler `Geolocator_PositionChanged` können Sie Testcode hinzufügen, mit dem für den Standort erzwungen wird, dass er sich im Geofence befindet:

    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");

Da wir nicht die echten Koordinaten übergeben (die sich derzeit ggf. nicht innerhalb der Grenzen befinden) und vordefinierte Testwerte verwenden, wird beim Aktualisieren eine Benachrichtigung angezeigt:

![](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

##Nächste Schritte

Es gibt einige Schritte, deren Ausführung zusätzlich zu den obigen Schritten unter Umständen erforderlich ist, um sicherzustellen, dass die Lösung bereit für die Produktion ist.

Zunächst sollten Sie ggf. sicherstellen, dass die Geofences dynamisch sind. Hierfür ist etwas zusätzliche Arbeit mit der Bing-API erforderlich, um neue Grenzen innerhalb der vorhandenen Datenquelle hochzuladen. Weitere Informationen zu diesem Thema finden Sie in der API-Dokumentation zu [Bing Spatial Data Services](https://msdn.microsoft.com/library/ff701734.aspx).

Außerdem kann es bei der Sicherstellung, dass die Bereitstellung für die richtigen Teilnehmer erfolgt, ratsam sein, das [Tagging](notification-hubs-routing-tag-expressions.md) zu verwenden.

In der oben gezeigten Projektmappe wird ein Szenario beschrieben, bei dem Sie viele verschiedene Plattformen nutzen können. Daher haben wir das Geofencing nicht auf systemspezifische Funktionen beschränkt. Die universelle Windows-Plattform verfügt aber standardmäßig über Funktionen zum [Erkennen von Geofences](msdn.microsoft.com/de-DE/windows/uwp/maps-and-location/set-up-a-geofence).

Weitere Informationen zu Notification Hubs-Funktionen finden Sie in unserem [Dokumentationsportal](https://azure.microsoft.com/documentation/services/notification-hubs/).

<!---HONumber=AcomDC_0323_2016-->