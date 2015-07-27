<properties 
	pageTitle="Herstellen einer Verbindung mit einem Media Services-Konto mithilfe von .NET" 
	description="In diesem Thema wird veranschaulicht, wie Sie über .NET eine Verbindung mit Media Services herstellen." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


# Herstellen einer Verbindung mit einem Media Services-Konto über das Media Services SDK für .NET

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md) und [Media Services: Livestreaming-Workflow](media-services-live-streaming-workflow.md).

In diesem Thema wird beschrieben, wie bei der Programmierung mit dem Media Services SDK für .NET eine programmgesteuerte Verbindung mit Microsoft Azure Media Services hergestellt wird.


## Herstellen einer Verbindung mit Media Services

Zum Herstellen einer programmgesteuerten Verbindung mit Media Services muss zuvor ein Azure-Konto eingerichtet werden, und anschließend müssen Media Services für dieses Konto konfiguriert und dann ein Visual Studio-Projekt für die Entwicklung mit dem Media Services SDK für .NET eingerichtet werden. Weitere Informationen finden Sie unter „Setup für die Entwicklung mit dem Media Services SDK für .NET“.

Nach abgeschlossener Einrichtung des Media Services-Kontos haben Sie die folgenden erforderlichen Verbindungswerte abgerufen. Mithilfe dieser Werte können Sie programmgesteuerte Verbindungen mit Media Services herstellen.

- Der Name Ihres Media Services-Kontos.

- Der Schlüssel Ihres Media Services-Kontos.

Sie finden diese Werte im Azure-Verwaltungsportal: Wählen Sie Ihr Media Services-Konto aus, und klicken Sie unten im Portalfenster auf das Symbol "**SCHLÜSSEL VERWALTEN**". Klicken Sie auf die Symbole neben den Textfeldern, um den jeweiligen Wert in die Systemzwischenablage zu kopieren.


## Erstellen einer CloudMediaContext-Instanz

Wenn Sie mit der Programmierung für Media Services beginnen möchten, müssen Sie eine **CloudMediaContext**-Instanz erstellen, die den Serverkontext darstellt. **CloudMediaContext** umfasst Verweise auf wichtige Auflistungen, z. B. Aufträge, Medienobjekte, Dateien, Zugriffsrichtlinien und Locators.

>[AZURE.NOTE]Die **CloudMediaContext**-Klasse ist nicht threadsicher. Sie sollten für jeden Thread oder für jeden Satz von Vorgängen einen neuen CloudMediaContext erstellen.


CloudMediaContext verfügt über fünf Konstruktorüberladungen. Es wird empfohlen, Konstruktoren zu verwenden, die **MediaServicesCredentials** als Parameter akzeptieren. Weitere Informationen finden Sie im folgenden Thema **Wiederverwenden von Token des Zugriffssteuerungsdiensts**.

Im folgenden Beispiel wird der öffentliche CloudMediaContext-Konstruktor (mit MediaServicesCredentials als Anmeldeinformationen) verwendet:

	// _cachedCredentials and _context are class member variables. 
	_cachedCredentials = new MediaServicesCredentials(
	                _mediaServicesAccountName,
	                _mediaServicesAccountKey);
	
	_context = new CloudMediaContext(_cachedCredentials);


## Wiederverwenden von Token des Zugriffssteuerungsdiensts

Dieser Abschnitt zeigt, wie Token des Zugriffssteuerungsdiensts mithilfe von CloudMediaContext-Konstruktoren, die MediaServicesCredentials als Parameter annehmen, wiederverwendet werden.


[Azure Active Directory Access Control](https://msdn.microsoft.com/library/hh147631.aspx) (auch als Access Control Service oder ACS bezeichnet) ist ein cloudbasierter Dienst, der eine einfache Möglichkeit zum Authentifizieren und Autorisieren von Benutzern für den Zugriff auf ihre Webanwendungen bietet. Microsoft Azure Media Services steuert den Zugriff auf die zugehörigen Dienste über das OAuth-Protokoll, das ein ACS-Token erfordert. Media Services empfängt die ACS-Token von einem Autorisierungsserver.

Bei der Entwicklung mit dem Media Services SDK haben Sie die Möglichkeit, die Tokenverwaltung den Code-Managern des SDKs zu überlassen. Die vollständige Verwaltung der ACS-Token durch das SDK führt jedoch zu unnötigen Tokenanforderungen. Das Anfordern von Token kostet Zeit und beansprucht Client- und Serverressourcen. Außerdem werden die Anforderungen bei einer überhohen Rate vom ACS-Server gedrosselt. Das Limit beträgt 30 Anforderungen pro Sekunde. Weitere Details finden Sie unter [ACS-Diensteinschränkungen](https://msdn.microsoft.com/library/gg185909.aspx).

Ab dem Media Services SDK, Version 3.0.0.0, können Sie die ACS-Token wiederverwenden. Die **CloudMediaContext**-Konstruktoren, die **MediaServicesCredentials** als Parameter annehmen, ermöglichen die gemeinsame Verwendung von ACS-Tokens in mehreren Kontexten. Die MediaServicesCredentials-Klasse kapselt die Media Services-Anmeldeinformationen. Wenn ein ACS-Token verfügbar und seine Ablaufzeit bekannt ist, können Sie eine neue MediaServicesCredentials-Instanz mit dem Token erstellen und an den Konstruktor von CloudMediaContext übergeben. Beachten Sie, dass Token vom Media Services SDK automatisch aktualisiert werden, wenn sie ablaufen. Es gibt zwei Möglichkeiten zur Wiederverwendung von ACS-Token, wie in den folgenden Beispielen veranschaulicht.

- Sie können das **MediaServicesCredentials**-Objekt im Arbeitsspeicher zwischenspeichern (z. B. in einer Variablen einer statischen Klasse). Anschließend übergeben Sie das zwischengespeicherte Objekt an den CloudMediaContext-Konstruktor. Das MediaServicesCredentials-Objekt enthält ein ACS-Token, das wiederverwendet werden kann, wenn es noch gültig ist. Wenn das Token ungültig ist, wird es durch das Media Services SDK unter Verwendung der an den MediaServicesCredentials-Konstruktor übergebenen Anmeldeinformationen aktualisiert.

	Beachten Sie, dass das **MediaServicesCredentials**-Objekt nach dem Aufruf von RefreshToken ein gültiges Token erhält. **CloudMediaContext** ruft die **RefreshToken**-Methode im Konstruktor auf. Wenn Sie beabsichtigen, die Tokenwerte in einem externen Speicher zu speichern, stellen Sie sicher, dass der TokenExpiration-Wert gültig ist, bevor Sie die Tokendaten speichern. Wenn er nicht gültig ist, rufen Sie RefreshToken vor dem Zwischenspeichern auf.

		// Create and cache the Media Services credentials in a static class variable.
		_cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);

		
		// Use the cached credentials to create a new CloudMediaContext object.
		if(_cachedCredentials == null)
		{
		    _cachedCredentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey);
		}
		
		CloudMediaContext context = new CloudMediaContext(_cachedCredentials);

- Sie können auch die AccessToken-Zeichenfolge und die TokenExpiration-Werte zwischenspeichern. Die Werte können zu einem späteren Zeitpunkt zum Erstellen eines neuen MediaServicesCredentials-Objekts mit den zwischengespeicherten Tokendaten verwendet werden. Dies ist besonders in Szenarien sinnvoll, in denen das Token auf sichere Weise von mehreren Prozessen oder Computern gemeinsam verwendet werden kann.

	In den folgenden Codeausschnitten werden die Methoden SaveTokenDataToExternalStorage, GetTokenDataFromExternalStorage und UpdateTokenDataInExternalStorageIfNeeded aufgerufen, die in diesem Beispiel nicht definiert sind. Sie können diese Methoden so definieren, dass sie Tokendaten in einem externen Speicher speichern bzw. aktualisieren oder daraus abrufen.

		CloudMediaContext context1 = new CloudMediaContext(_mediaServicesAccountName, _mediaServicesAccountKey);
		
		// Get token values from the context.
		var accessToken = context1.Credentials.AccessToken;
		var tokenExpiration = context1.Credentials.TokenExpiration;
		
		// Save token values for later use. 
		// The SaveTokenDataToExternalStorage method should check 
		// whether the TokenExpiration value is valid before saving the token data. 
		// If it is not valid, call MediaServicesCredentials’s RefreshToken before caching.
		SaveTokenDataToExternalStorage(accessToken, tokenExpiration);
		
	Verwenden Sie die gespeicherten Tokenwerte, um MediaServicesCredentials zu erstellen.


		var accessToken = "";
		var tokenExpiration = DateTime.UtcNow;
		
		// Retrieve saved token values.
		GetTokenDataFromExternalStorage(out accessToken, out tokenExpiration);
		
		// Create a new MediaServicesCredentials object using saved token values.
		MediaServicesCredentials credentials = new MediaServicesCredentials(_mediaServicesAccountName, _mediaServicesAccountKey)
		{
		    AccessToken = accessToken,
		    TokenExpiration = tokenExpiration
		};
		
		CloudMediaContext context2 = new CloudMediaContext(credentials);

	Aktualisieren Sie die Tokenkopie für den Fall, dass das Token vom Media Services SDK aktualisiert wurde.
	
		if(tokenExpiration != context2.Credentials.TokenExpiration)
		{
		    UpdateTokenDataInExternalStorageIfNeeded(accessToken, context2.Credentials.TokenExpiration);
		}
		

- Wenn Sie mehrere Media Services-Konten besitzen (z. B. für die Verteilung der Nutzlast oder geografische Verteilung), können Sie MediaServicesCredentials-Objekte mithilfe der System.Collections.Concurrent.ConcurrentDictionary-Auflistung zwischenspeichern (die ConcurrentDictionary-Auflistung stellt eine threadsichere Auflistung von Schlüssel/Wert-Paaren dar, auf die von mehreren Threads gleichzeitig zugegriffen werden kann). Sie können dann die GetOrAdd-Methode verwenden, um die zwischengespeicherten Anmeldeinformationen abzurufen.

		// Declare a static class variable of the ConcurrentDictionary type in which the Media Services credentials will be cached.  
		private static readonly ConcurrentDictionary<string, MediaServicesCredentials> mediaServicesCredentialsCache = 
		    new ConcurrentDictionary<string, MediaServicesCredentials>();
		

		// Cache (or get already cached) Media Services credentials. Use these credentials to create a new CloudMediaContext object.
		static public CloudMediaContext CreateMediaServicesContext(string accountName, string accountKey)
		{
		    CloudMediaContext cloudMediaContext;
		    MediaServicesCredentials mediaServicesCredentials;
		
		    mediaServicesCredentials = mediaServicesCredentialsCache.GetOrAdd(
		        accountName,
		        valueFactory => new MediaServicesCredentials(accountName, accountKey));
		
		    cloudMediaContext = new CloudMediaContext(mediaServicesCredentials);
		
		    return cloudMediaContext;
		}
		
## Herstellen einer Verbindung mit einem Media Services-Konto, das sich in der Region Nordchina befindet

Wenn sich Ihr Konto in der Region Nordchina befindet, verwenden Sie den folgenden Konstruktor:

	public CloudMediaContext(Uri apiServer, string accountName, string accountKey, string scope, string acsBaseAddress)

Beispiel:


	_context = new CloudMediaContext(
	    new Uri("https://wamsbjbclus001rest-hs.chinacloudapp.cn/API/"),
	    _mediaServicesAccountName,
	    _mediaServicesAccountKey,
	    "urn:WindowsAzureMediaServices",
	    "https://wamsprodglobal001acs.accesscontrol.chinacloudapi.cn");


## Speichern von Verbindungswerten in der Konfiguration

Es wird dringend empfohlen, Verbindungswerte in der Konfiguration zu speichern. Dies gilt insbesondere für sensible Werte wie Ihren Kontonamen und das Kennwort. Außerdem wird empfohlen, sensible Konfigurationsdaten zu verschlüsseln. Sie können die gesamte Konfigurationsdatei mithilfe des verschlüsselnden Dateisystems von Windows (Encrypting File System, EFS) verschlüsseln. Klicken Sie zum Aktivieren von EFS für eine Datei mit der rechten Maustaste auf die Datei, wählen Sie **Eigenschaften** aus, und aktivieren Sie dann in den Einstellungen auf der Registerkarte **Erweitert** die Verschlüsselung. Sie können auch eine benutzerdefinierte Lösung für das Verschlüsseln ausgewählter Teile einer Konfigurationsdatei erstellen, indem Sie eine geschützte Konfiguration verwenden. Siehe [Verschlüsseln von Konfigurationsinformationen mithilfe der geschützten Konfiguration](https://msdn.microsoft.com/library/53tyfkaw.aspx).

Die folgende Datei „App.config“ enthält die erforderlichen Verbindungswerte. Die Werte im <appSettings>-Element sind die erforderlichen Werte, die Sie bei der Einrichtung des Media Services-Kontos erhalten haben.


<pre><code>
&lt;configuration>
    &lt;appSettings>
	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
    	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
    &lt;/appSettings>
&lt;/configuration>
</code></pre>

Zum Abrufen von Verbindungswerten aus der Konfiguration können Sie die **ConfigurationManager**-Klasse verwenden und die Werte dann Feldern in Ihrem Code zuweisen:
	
	private static readonly string _accountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	private static readonly string _accountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];


<!-- Anchors. -->


<!-- URLs. -->

<!---HONumber=July15_HO3-->