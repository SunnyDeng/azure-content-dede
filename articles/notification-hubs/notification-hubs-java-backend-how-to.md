<properties 
	pageTitle="Verwenden von Notification Hubs mit Java" 
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs von einem Java-Back-End." 
	services="notification-hubs" 
	documentationCenter="" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="java" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="01/12/2015" 
	ms.author="yuaxu"/>

# Verwenden von Notification Hubs von Java aus
<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-java-backend-how-to/" title="Java" class="current">Java</a><a href="/documentation/articles/notification-hubs-php-backend-how-to/" title="PHP">PHP</a><a href="/documentation/articles/notification-hubs-python-backend-how-to/" title="Python">Python</a><a href="/documentation/articles/notification-hubs-nodejs-how-to-use-notification-hubs/" title="Node.js">Node.js</a>
</div>

In diesem Thema werden die wichtigsten Features des neuen, vollständig unterstützten, offiziellen Azure Notification Hub Java-SDKs beschrieben. 
Dies ist ein Open-Source-Projekt, und Sie können den gesamten SDK-Code unter [Java-SDK] anzeigen. 

Sie können von einem Java/PHP/Python/Ruby-Back-End aus über die REST-Schnittstelle für Notification Hubs, die im MSDN-Thema [REST-APIs für Notification Hubs](http://msdn.microsoft.com/library/dn223264.aspx) beschrieben ist, auf alle Notification Hubs-Features zugreifen. Dieses Java-SDK stellt einen dünnen Wrapper über diese REST-Schnittstellen in Java bereit. 

Das SDK unterstützt derzeit:

- CRUD auf Notification Hubs 
- CRUD für Registrierungen
- Installationsverwaltung
- Importieren/Exportieren von Registrierungen
- Reguläre Sendevorgänge
- Geplante Sendevorgänge
- Asynchrone Vorgänge über Java NIO
- Unterstützte Plattformen: APNS (iOS), GCM (Android), WNS (Windows Store-Apps), MPNS (Windows Phone), ADM (Amazon Kindle Fire), Baidu (Android ohne Google-Dienste) 

## SDK-Verwendung

### Kompilieren und Erstellen

Verwenden von [Maven]

Zum Erstellen von:

	mvn package

## Code

### Notification Hub-CRUDs

**"NamespaceManager"-Objekt erstellen:**
	
	NamespaceManager namespaceManager = new NamespaceManager("connection string")

**Notification Hub erstellen:**
	
	NotificationHubDescription hub = new NotificationHubDescription("hubname");
	hub.setWindowsCredential(new WindowsCredential("sid","key"));
	hub = namespaceManager.createNotificationHub(hub);
	
 ODER

	hub = new NotificationHub("connection string", "hubname");

**Notification Hub abrufen:**
	
	hub = namespaceManager.getNotificationHub("hubname");

**Notification Hub aktualisieren:**
	
	hub.setMpnsCredential(new MpnsCredential("mpnscert", "mpnskey"));
	hub = namespaceManager.updateNotificationHub(hub);

**Notification Hub löschen:**
	
	namespaceManager.deleteNotificationHub("hubname");

### Registrierungs-CRUDs
**Notification Hub-Client erstellen:**

	hub = new NotificationHub("connection string", "hubname");

**Windows-Registrierung erstellen:**

	WindowsRegistration reg = new WindowsRegistration(new URI(CHANNELURI));
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");    
	hub.createRegistration(reg);

**IOS-Registrierung erstellen:**

	AppleRegistration reg = new AppleRegistration(DEVICETOKEN);
	reg.getTags().add("myTag");
	reg.getTags().add("myOtherTag");
	hub.createRegistration(reg);

Auf ähnliche Weise können Sie die Registrierungen für Android (GCM), Windows Phone (MPNS) und Kindle Fire ADM) erstellen.

**Vorlagen-Registrierungen erstellen:**

	WindowsTemplateRegistration reg = new WindowsTemplateRegistration(new URI(CHANNELURI), WNSBODYTEMPLATE);
	reg.getHeaders().put("X-WNS-Type", "wns/toast");
	hub.createRegistration(reg);

**Registrierungen mit "create registrationid + upsert"-Muster erstellen**

Entfernt Duplikate aufgrund verlorener Antworten beim Speichern von Registrierungs-IDs dem Gerät:

	String id = hub.createRegistrationId();
	WindowsRegistration reg = new WindowsRegistration(id, new URI(CHANNELURI));
	hub.upsertRegistration(reg);

**Aktualisieren von Registrierungen:**
	
	hub.updateRegistration(reg);

**Löschen von Registrierungen:**
	
	hub.deleteRegistration(regid);

**Abfragen von Registrierungen:**

* 	**Abrufen einer einzelnen Registrierung:**
	
		hub.getRegistration(regid);
	
* 	**Abrufen aller Registrierungen im Hub:**
	
		hub.getRegistrations();
	
* 	**Abrufen von Registrierungen mit Tags:**
	
		hub.getRegistrationsByTag("myTag");
	
* 	**Abrufen von Registrierungen nach Kanal:**
	
		hub.getRegistrationsByChannel("devicetoken");

Alle Sammlungsabfragen unterstützen $top und Fortsetzungstoken.

### Verwendung der Installations-API
Die Installations-API ist ein alternativer Mechanismus für die Registrierungsverwaltung. Anstatt mehrere Registrierungen zu verwalten, was keine leichte Aufgabe ist und möglicherweise falsch oder ineffizient durchgeführt wird, ist es möglich, ein EINZIGES Installationsobjekt zu verwenden. 
Die Installation enthält alles, was Sie benötigen: Pushkanal (Gerätetoken), Tags, Vorlagen, sekundäre Kacheln (für WNS und APNS). Sie brauchen den Dienst nicht mehr aufzurufen, um die ID zu erhalten - generieren Sie lediglich die GUID oder einen anderen Bezeichner, speichern Sie diesen auf dem Gerät, und senden Sie ihn zusammen mit dem Pushkanal (Geräte-Token) an Ihr Back-End. 
Auf dem Back-End sollten Sie nur einen einzigen Aufruf durchführen: "CreateOrUpdateInstallation". Er ist vollständig idempotent, daher können Sie ihn bei Bedarf beliebig wiederholen.

Für Amazon Kindle Fire sieht er beispielsweise folgendermaßen aus:

	Installation installation = new Installation("installation-id", NotificationPlatform.Adm, "adm-push-channel");
	hub.createOrUpdateInstallation(installation);

Wenn Sie ihn aktualisieren möchten: 

	installation.addTag("foo");
	installation.addTemplate("template1", new InstallationTemplate("{"data":{"key1":"$(value1)"}}","tag-for-template1"));
	installation.addTemplate("template2", new InstallationTemplate("{"data":{"key2":"$(value2)"}}","tag-for-template2"));
	hub.createOrUpdateInstallation(installation);

Für erweiterte Szenarien gibt es Funktionen für die teilweise Aktualisierung, die Ihnen ermöglichen, nur bestimmte Eigenschaften des Installationsobjekts zu ändern. Im Grunde umfasst die teilweise Aktualisierung eine Teilmenge der JSON Patch-Vorgänge, die Sie für ein Installationsobjekt ausführen können.

	PartialUpdateOperation addChannel = new PartialUpdateOperation(UpdateOperationType.Add, "/pushChannel", "adm-push-channel2");
	PartialUpdateOperation addTag = new PartialUpdateOperation(UpdateOperationType.Add, "/tags", "bar");
	PartialUpdateOperation replaceTemplate = new PartialUpdateOperation(UpdateOperationType.Replace, "/templates/template1", new InstallationTemplate("{"data":{"key3":"$(value3)"}}","tag-for-template1")).toJson());
	hub.patchInstallation("installation-id", addChannel, addTag, replaceTemplate);

Löschen der Installation:

	hub.deleteInstallation(installation.getInstallationId());

"CreateOrUpdate", "Patch" und "Delete" sind letztendlich konsistent mit "Get". Der angeforderte Vorgang wird während des Aufrufs zunächst in die Systemwarteschlange gesetzt und später im Hintergrund ausgeführt. Beachten Sie, dass "Get" nicht für das Hauptlaufzeitszenario ausgelegt, sondern nur für Debug- und Problembehandlungszwecke bestimmt ist. Es wird durch den Dienst stark eingeschränkt.

Der Sendefluss für Installationen entspricht dem für Registrierungen. Wir haben gerade eine Option eingeführt, um Benachrichtigungen an eine bestimmte Installation zu adressieren - verwenden Sie einfach das Tag "InstallationId:{desired-id}". Für den oben genannten Fall würde es folgendermaßen aussehen:

	Notification n = Notification.createWindowsNotification("WNS body");
	hub.sendNotification(n, "InstallationId:{installation-id}");

Für eine von mehreren Vorlagen:

	Map<String, String> prop =  new HashMap<String, String>();
	prop.put("value3", "some value");
	Notification n = Notification.createTemplateNotification(prop);
	hub.sendNotification(n, "InstallationId:{installation-id} && tag-for-template1");

### Planen von Benachrichtigungen (für STANDARD-Ebene verfügbar)

Dasselbe wie ein regulärer Sendevorgang, aber mit einem zusätzlichen Parameter ("scheduledTime") der angibt, wann die Benachrichtigung gesendet werden soll. Der Dienst akzeptiert jeden beliebigen Zeitpunkt zwischen "jetzt + 5 Minuten" und "jetzt + 7 Tage".

**Planen einer systemeigenen Windows-Benachrichtigung:**

	Calendar c = Calendar.getInstance();
	c.add(Calendar.DATE, 1);    
	Notification n = Notification.createWindowsNotification("WNS body");
	hub.scheduleNotification(n, c.getTime());

### Import/Export (für STANDARD-Ebene verfügbar)
Manchmal ist es erforderlich, Registrierungen als Massenvorgang auszuführen. In der Regel ist dies zur Integration in ein anderes System oder lediglich als umfangreiche Korrektur, z. B. die Aktualisierung der Tags, erforderlich. Es empfiehlt sich dringend, nicht den "Get/Update"-Fluss zu verwenden, wenn es um Tausende von Registrierungen geht. Für dieses Szenario wurde die Import/Export-Funktion entwickelt. Im Grunde gewähren Sie Zugriff auf einen bestimmten BLOB-Container unter Ihrem Speicherkonto als Quelle für eingehende Daten und Speicherort für die Ausgabe.

**Exportauftrag übermitteln:**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ExportRegistrations);
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);


**Importauftrag übermitteln:**

	NotificationHubJob job = new NotificationHubJob();
	job.setJobType(NotificationHubJobType.ImportCreateRegistrations);
	job.setImportFileUri("input file uri with SAS signature");
	job.setOutputContainerUri("container uri with SAS signature");
	job = hub.submitNotificationHubJob(job);

**Warten, bis der Auftrag abgeschlossen ist:**

	while(true){
	    Thread.sleep(1000);
	    job = hub.getNotificationHubJob(job.getJobId());
	    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
	        break;
	}       

**Alle Aufträge abrufen:**

	List<NotificationHubJob> jobs = hub.getAllNotificationHubJobs();

**URI mit SAS-Signatur:**
Dies ist die URL einer bestimmten BLOB-Datei oder eines bestimmten BLOB-Containers, einschließlich bestimmter Parameter, wie Berechtigungen und Ablaufzeitpunkt, sowie Signatur dieser Objekte unter Verwendung des SAS-Schlüssels des Kontos. Das Java-SDK für Azure-Speicher verfügt über umfangreiche Funktionen, beispielsweise zum Erstellen dieser Art von URIs. Als einfache Alternative können Sie sich die Testklasse "ImportExportE2E" (unter Github) anschauen, die über eine sehr einfache und kompakte Implementierung des Signaturalgorithmus verfügt.

###Senden von Benachrichtigungen
Das Benachrichtigungsobjekt ist lediglich ein Text mit Headern. Einige Dienstprogrammmethoden helfen beim Erstellen von system- oder vorlagenbasierten Benachrichtigungsobjekten.

* **Windows Store und Windows Phone 8.1 (nicht-Silverlight)**

		String toast = "<toast><visual><binding template="ToastText01"><text id="1">Hello from Java!</text></binding></visual></toast>";
		Notification n = Notification.createWindowsNotification(toast);
		hub.sendNotification(n);

* **iOS**

		String alert = "{"aps":{"alert":"Hello from Java!"}}";
		Notification n = Notification.createAppleNotification(alert);
		hub.sendNotification(n);

* **Android**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createGcmNotification(message);
		hub.sendNotification(n);

* **Windows Phone 8.0 und 8.1 Silverlight**

		String toast = "<?xml version="1.0" encoding="utf-8"?>" +
			        "<wp:Notification xmlns:wp="WPNotification">" +
			           "<wp:Toast>" +
			                "<wp:Text1>Hello from Java!</wp:Text1>" +
			           "</wp:Toast> " +
			        "</wp:Notification>";
		Notification n = Notification.createMpnsNotification(toast);
		hub.sendNotification(n);

* **Kindle Fire**

		String message = "{"data":{"msg":"Hello from Java!"}}";
		Notification n = Notification.createAdmNotification(message);
		hub.sendNotification(n);

* **Senden an Tags**

		Set<String> tags = new HashSet<String>();
		tags.add("boo");
		tags.add("foo");
		hub.sendNotification(n, tags);

* **Senden an Tagausdruck**       

		hub.sendNotification(n, "foo && ! bar");

* **Senden einer Vorlagenbenachrichtigung**

		Map<String, String> prop =  new HashMap<String, String>();
		prop.put("prop1", "v1");
		prop.put("prop2", "v2");
		Notification n = Notification.createTemplateNotification(prop);
		hub.sendNotification(n);

Beim Ausführen des Java-Codes sollte jetzt eine Benachrichtigung erstellt werden, die auf dem Zielgerät angezeigt wird.

##<a name="next-steps"></a>Nächste Schritte
In diesem Thema haben wir gezeigt, wie Sie einen einfachen Java REST-Client für Notification Hubs erstellen. Mögliche nächste Schritte:

* Laden Sie das vollständige [Java-SDK] herunter, das den gesamten SDK-Code enthält. 
* Arbeiten Sie die Beispiele durch:
	- [Erste Schritte mit Notification Hubs]
	- [Senden aktueller Nachrichten]
	- [Senden lokalisierter aktueller Nachrichten]
	- [Senden von Benachrichtigungen an authentifizierte Benutzer]
	- [Senden plattformübergreifender Benachrichtigungen an authentifizierte Benutzer]

[Java-SDK]: https://github.com/Azure/azure-notificationhubs-java-backend
[Erste Schritte mit Notification Hubs]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Erste Schritte mit Notification Hubs]: http://www.windowsazure.com/manage/services/notification-hubs/getting-started-windows-dotnet/
[Senden aktueller Nachrichten]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-dotnet/
[Senden lokalisierter aktueller Nachrichten]: http://www.windowsazure.com/manage/services/notification-hubs/breaking-news-localized-dotnet/
[Senden von Benachrichtigungen an authentifizierte Benutzer]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users/
[Senden plattformübergreifender Benachrichtigungen an authentifizierte Benutzer]: http://www.windowsazure.com/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Maven]: http://maven.apache.org/

<!--HONumber=49--> 