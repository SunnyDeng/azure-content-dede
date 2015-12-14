<properties
	pageTitle="Registrierungsverwaltung"
	description="In diesem Thema wird erläutert, wie Geräte bei Notification Hubs registriert werden, um Pushbenachrichtigungen zu empfangen."
	services="notification-hubs"
	documentationCenter=".net"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="wesmc"/>

# Registrierungsverwaltung

##Übersicht

In diesem Thema wird erläutert, wie Geräte bei Notification Hubs registriert werden, um Pushbenachrichtigungen zu empfangen. Nach einem allgemeinen Überblick über Registrierungen werden in diesem Thema die beiden Hauptmuster zum Registrieren von Geräten vorgestellt: die direkte Registrierung beim Notification Hub über das Gerät und die Registrierung über ein Anwendungs-Back-End.


##Was ist die Geräteregistrierung?

Die Geräteregistrierung bei einem Notification Hub erfolgt mithilfe einer **Registrierung** oder **Installation**.

#### Registrierungen
Eine Registrierung ist eine untergeordnete Entität eines Notification Hubs. Während der Registrierung wird das PNS (Platform Notification Service)-Handle für ein Gerät Tags und ggf. einer Vorlage zugeordnet. Das PNS-Handle könnte einem ChannelURI, einem Gerätetoken oder einer GCM-Registrierungs-ID entsprechen. Tags werden verwendet, um Benachrichtigungen an die richtige Gruppe von Gerätehandles weiterzuleiten. Weitere Informationen finden Sie unter [Weiterleitung und Tagausdrücke](notification-hubs-routing-tag-expressions.md). Vorlagen werden verwendet, um Transformationen pro Registrierung zu implementieren. Weitere Informationen finden Sie unter [Vorlagen](notification-hubs-templates.md).

#### Installationen
Eine Installation ist eine erweiterte Registrierung, die einen Behälter von Eigenschaften umfasst, die sich auf Pushvorgänge beziehen. Dies ist jedoch der neueste und beste Ansatz zum Registrieren Ihrer Geräte.

Im Folgenden sind die wichtigsten Vorteile bei der Verwendung von Installationen beschrieben:

* Das Erstellen oder Aktualisieren einer Installation ist vollständig idempotent. Sie können eine Installation wiederholen, ohne sich um doppelte Registrierungen sorgen zu müssen.
* Das Installationsmodell erleichtert die Ausführung individueller Pushvorgänge für bestimmte Geräte. Bei jeder installationsbasierten Registrierung wird automatisch das Systemtag **"$InstallationId:[installationId]"** hinzugefügt. So können Sie ein Sendetag für ein bestimmtes Gerät aufrufen, ohne dass zusätzlicher Code geschrieben werden muss.
* Mithilfe von Installationen können Sie zudem Registrierungsteilupdates durchführen. Das Teilupdate einer Installation wird mit einer PATCH-Methode unter Verwendung des [JSON-Patch-Standards](https://tools.ietf.org/html/rfc6902) angefordert. Dies ist besonders nützlich, wenn Sie Tags für die Registrierung aktualisieren möchten. Sie müssen nicht die gesamte Registrierung auflösen und dann alle vorherigen Tags erneut senden.

Installationen werden derzeit nur vom [Notification Hub SDK für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) unterstützt. Weitere Informationen finden Sie unter der [Installation-Klasse](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation.aspx). Um über ein Clientgerät unter Verwendung einer Installations-ID ohne Back-End eine Registrierung vorzunehmen, müssen Sie derzeit die [Notification Hubs-REST-API](https://msdn.microsoft.com/library/mt621153.aspx) verwenden.

Eine Installation kann folgende Eigenschaften enthalten. Eine vollständige Liste der Installationseigenschaften finden Sie unter [Erstellen oder Überschreiben einer Installation mit REST](https://msdn.microsoft.com/library/azure/mt621153.aspx) oder [Installationseigenschaften](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.installation_properties.aspx).

	// Example installation format to show some supported properties
	{
	    installationId: "",
	    expirationTime: "",
	    tags: [],
	    platform: "",
	    pushChannel: "",
	    ………
	    templates: {
	        "templateName1" : {
				body: "",
				tags: [] },
			"templateName2" : {
				body: "",
				// Headers are for Windows Store only
				headers: {
					"X-WNS-Type": "wns/tile" }
				tags: [] }
	    },
	    secondaryTiles: {
	        "tileId1": {
	            pushChannel: "",
	            tags: [],
	            templates: {
	                "otherTemplate": {
	                    bodyTemplate: "",
	                    headers: {
	                        ... }
	                    tags: [] }
	            }
	        }
	    }
	}

 

Sie sollten unbedingt beachten, dass Registrierungen und Installationen sowie die darin enthaltenen PNS-Handles ablaufen. Sie können die Gültigkeitsdauer im Notification Hub auf bis zu 90 Tage festlegen. Dieser Grenzwert bedeutet, dass die Objekte in regelmäßigen Abständen aktualisiert werden müssen und darüber hinaus nicht den einzigen Speicher für wichtige Informationen darstellen sollten. Dieser automatische Ablauf vereinfacht außerdem die Bereinigung, wenn die mobile Anwendung deinstalliert wird.

Registrierungen und Installationen müssen das aktuelle PNS-Handle für jedes Gerät bzw. jeden Kanal enthalten. Da PNS-Handles nur in einer Client-App auf dem Gerät abgerufen werden können, besteht ein Muster darin, sich direkt auf dem Gerät mit der Client-App zu registrieren. Andererseits können tagbezogene Sicherheitsaspekte und Geschäftslogik die Verwaltung der Geräteregistrierung im App-Back-End erforderlich machen.

#### Vorlagen

Wenn Sie [Vorlagen](notification-hubs-templates.md) verwenden möchten, sollte die Geräteinstallation auch alle Vorlagen, die dem jeweiligen Gerät zugeordnet sind, in einem JSON-Format enthalten (siehe Beispiel oben). Mithilfe der Vorlagennamen können unterschiedliche Vorlagen problemlos auf dasselbe Gerät abzielen.

Beachten Sie, dass jeder Vorlagenname einem Vorlagentext und einer optionalen Gruppe von Tags zugeordnet ist. Darüber hinaus kann jede Plattform zusätzliche Vorlageneigenschaften aufweisen. Für den Windows Store (mit WNS) und Windows Phone 8 (mit MPNS) kann die Vorlage einen zusätzlichen Satz von Headern enthalten. Bei APNs können Sie eine Ablaufeigenschaft auf eine Konstante oder auf einen Vorlagenausdruck festlegen. Eine vollständige Liste der Installationseigenschaften finden Sie im Thema [Erstellen oder Überschreiben einer Installation mit REST](https://msdn.microsoft.com/library/azure/mt621153.aspx).

#### Sekundäre Kacheln für Windows Store-Apps

Für Windows Store-Clientanwendungen ist das Senden von Benachrichtigungen an sekundäre Kacheln und an die primäre Kachel identisch. Dies wird auch in Installationen unterstützt. Beachten Sie, dass sekundäre Kacheln über einen unterschiedlichen ChannelUri verfügen, der vom SDK in der Client-App transparent behandelt wird.

Das SecondaryTiles-Wörterbuch verwendet dieselbe TileId, die zum Erstellen des SecondaryTiles-Objekts in der Windows Store-App verwendet wird. Wie beim primären ChannelUri können sich ChannelUris sekundärer Kacheln jederzeit ändern. Damit die Installationen im Notification Hub aktuell bleiben, müssen sie vom Gerät mit den aktuellen ChannelUris der sekundären Kacheln aktualisiert werden.


##Registrierungsverwaltung über das Gerät

Wenn die Geräteregistrierung über Client-Apps verwaltet wird, ist das Back-End nur für das Senden von Benachrichtigungen verantwortlich. Client-Apps sorgen dafür, dass PNS-Handles auf dem neuesten Stand bleiben, und registrieren Tags. Dieses Muster wird in der folgenden Abbildung veranschaulicht.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Zuerst ruft das Gerät das PNS-Handle aus dem PNS ab und registriert sich dann direkt beim Notification Hub. Wenn die Registrierung erfolgreich verläuft, kann das App-Back-End eine zielgerichtete Benachrichtigung an diese Registrierung senden. Weitere Informationen zum Senden von Benachrichtigungen finden Sie unter [Weiterleitung und Tagausdrücke](notification-hubs-routing-tag-expressions.md). Beachten Sie, dass Sie in diesem Fall nur Lauschrechte verwenden, um über das Gerät auf die Notification Hubs zuzugreifen. Weitere Informationen finden Sie unter [Sicherheit](notification-hubs-security.md).

Die Registrierung über das Gerät ist die einfachste Methode, birgt aber auch Nachteile. Der erste Nachteil besteht darin, dass eine Client-App ihre Tags nur aktualisieren kann, wenn die App aktiv ist. Angenommen, ein Benutzer verfügt über zwei Geräte, die Tags im Zusammenhang mit Sportmannschaften registrieren. Wenn sich das erste Gerät für ein zusätzliches Tag (z. B. Borussia Dortmund) registriert, empfängt das zweite Gerät erst Benachrichtigungen zu Borussia Dortmund, wenn die App auf dem zweiten Gerät ein zweites Mal ausgeführt wird. Allgemeiner ausgedrückt bedeutet dies, dass die Verwaltung von Tags möglichst über das Back-End erfolgen sollte, wenn Tags für mehrere Geräte gelten. Der zweite Nachteil der Registrierungsverwaltung über die Client-App besteht darin, dass die Registrierung mit besonderer Sorgfalt auf bestimmte Tags beschränkt werden muss (wie im Abschnitt "Sicherheit auf Tagebene" erläutert), da Apps gehackt werden können.



#### Beispielcode zur Registrierung bei einem Notification Hub über ein Gerät mithilfe einer Installation 

Momentan wird dieser Vorgang nur bei Verwendung der [Notification Hubs-REST-API](https://msdn.microsoft.com/library/mt621153.aspx) unterstützt.

Die Installation kann auch mit der PATCH-Methode unter Verwendung des [JSON-Patch-Standards](https://tools.ietf.org/html/rfc6902) aktualisiert werden.

	class DeviceInstallation
	{
	    public string installationId { get; set; }
	    public string platform { get; set; }
	    public string pushChannel { get; set; }
	    public string[] tags { get; set; }
	}

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
		 string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/de-DE/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
		// See, https://msdn.microsoft.com/de-DE/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored a installation id in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
						"<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }

   

#### Beispielcode zur Registrierung bei einem Notification Hub über ein Gerät mithilfe einer Registrierung


Durch diese Methoden wird eine Registrierung für das Gerät erstellt oder aktualisiert, auf dem sie aufgerufen werden. Dies bedeutet, dass zum Aktualisieren des Handles oder der Tags die gesamte Registrierung überschrieben werden muss. Wie bereits erwähnt, sind Registrierungen temporär. Daher sollten die aktuellen Tags, die ein bestimmtes Gerät benötigt, immer an einem zuverlässigen Ort gespeichert sein.


	// Initialize the Notification Hub
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

	// The Device id from the PNS
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    // If you are registering from the client itself, then store this registration id in device
	// storage. Then when the app starts, you can check if a registration id already exists or not before
	// creating.
	var settings = ApplicationData.Current.LocalSettings.Values;

	// If we have not stored a registration id in application data, store in application data.
	if (!settings.ContainsKey("__NHRegistrationId"))
	{
		// make sure there are no existing registrations for this push handle (used for iOS and Android)	
		string newRegistrationId = null;
		var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
		foreach (RegistrationDescription registration in registrations)
		{
			if (newRegistrationId == null)
			{
				newRegistrationId = registration.RegistrationId;
			}
			else
			{
				await hub.DeleteRegistrationAsync(registration);
			}
		}

		newRegistrationId = await hub.CreateRegistrationIdAsync();

        settings.Add("__NHRegistrationId", newRegistrationId);
	}
     
    string regId = (string)settings["__NHRegistrationId"];

    RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
    registration.RegistrationId = regId;
    registration.Tags = new HashSet<string>(YourTags);

	try
	{
		await hub.CreateOrUpdateRegistrationAsync(registration);
	}
	catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
	{
		// regId likely expired, delete from local storage and try again
		settings.Remove("__NHRegistrationId");
	}


## Registrierungsverwaltung über ein Back-End

Zum Verwalten von Registrierungen über das Back-End muss zusätzlicher Code geschrieben werden. Bei jedem Start muss die App auf dem Gerät das aktualisierte PNS-Handle (zusammen mit den Tags und Vorlagen) für das Back-End bereitstellen, und das Back-End muss dieses Handle im Notification Hub aktualisieren. Dieses Verfahren wird in der folgenden Abbildung veranschaulicht.

![](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

Die Vorteile der Verwaltung von Registrierungen über das Back-End liegen darin, dass Tags für Registrierungen selbst dann geändert werden können, wenn die entsprechende App auf dem Gerät inaktiv ist, und dass die Client-App authentifiziert werden kann, bevor ihrer Registrierung ein Tag hinzugefügt wird.


#### Beispielcode zur Registrierung bei einem Notification Hub über ein Back-End mithilfe einer Installation

Das Clientgerät ruft sein PNS-Handle und die relevanten Installationseigenschaften wie zuvor ab und ruft eine benutzerdefinierte API im Back-End auf, die die Registrierung ausführen und Tags autorisieren kann usw. Das Back-End kann die Vorteile des [Notification Hub SDKs für Back-End-Vorgänge](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) nutzen.

Die Installation kann auch mit der PATCH-Methode unter Verwendung des [JSON-Patch-Standards](https://tools.ietf.org/html/rfc6902) aktualisiert werden.
 

	// Initialize the Notification Hub
	NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

    // Custom API on the backend
    public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
    {

        Installation installation = new Installation();
        installation.InstallationId = deviceUpdate.InstallationId;
        installation.PushChannel = deviceUpdate.Handle;
        installation.Tags = deviceUpdate.Tags;

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                installation.Platform = NotificationPlatform.Mpns;
                break;
            case "wns":
                installation.Platform = NotificationPlatform.Wns;
                break;
            case "apns":
                installation.Platform = NotificationPlatform.Apns;
                break;
            case "gcm":
                installation.Platform = NotificationPlatform.Gcm;
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }


        // In the backend we can control if a user is allowed to add tags
        //installation.Tags = new List<string>(deviceUpdate.Tags);
        //installation.Tags.Add("username:" + username);

        await hub.CreateOrUpdateInstallationAsync(installation);

        return Request.CreateResponse(HttpStatusCode.OK);
    }


#### Beispielcode zur Registrierung bei einem Notification Hub über ein Gerät mithilfe einer Registrierungs-ID

Über das App-Back-End können Sie grundlegende CRUDS-Vorgänge für Registrierungen ausführen. Beispiel:

	var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");
            
	// create a registration description object of the correct type, e.g.
	var reg = new WindowsRegistrationDescription(channelUri, tags);

	// Create
	await hub.CreateRegistrationAsync(reg);

	// Get by id
	var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

	// update
	r.Tags.Add("myTag");

	// update on hub
	await hub.UpdateRegistrationAsync(r);

	// delete
	await hub.DeleteRegistrationAsync(r);


Die Nebenläufigkeit zwischen Registrierungsupdates muss vom Back-End behandelt werden. Service Bus unterstützt die Steuerung für optimistische Nebenläufigkeit für die Registrierungsverwaltung. Auf der HTTP-Ebene wird dies durch die Verwendung von ETag für Registrierungsverwaltungsvorgänge implementiert. Dieses Feature wird von Microsoft-SDKs, die eine Ausnahme auslösen, wenn ein Update aus Gründen der Nebenläufigkeit abgelehnt wird, transparent verwendet. Das Back-End ist dafür verantwortlich, diese Ausnahmen zu behandeln und das Update ggf. zu wiederholen.

<!---HONumber=AcomDC_1203_2015-->