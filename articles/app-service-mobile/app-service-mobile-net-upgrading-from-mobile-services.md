<properties 
	pageTitle="Upgrade von Mobile Services auf Azure App Service" 
	description="Erfahren Sie, wie einfach Sie Ihre Mobile Services-Anwendung auf eine mobile App Service-App aktualisieren können." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# Aktualisieren des vorhandenen .NET-Azure Mobile Service auf App Service

Mobile App Service-Apps sind eine neue Methode zum Erstellen mobiler Anwendungen mit Microsoft Azure. Weitere Informationen finden Sie unter [Was sind Mobile Apps?].

In diesem Thema wird beschrieben, wie Sie eine vorhandene .NET-Back-End-Anwendung von Azure Mobile Services auf eine neue mobile App Service-App aktualisieren. Bei diesem Upgrade können Sie Ihre vorhandene Mobile Services-Anwendung weiter betreiben.

Wenn ein mobiles Back-End auf Azure App Service aktualisiert wird, besteht Zugriff auf alle App Service-Features, und die Abrechnung erfolgt gemäß [App Service-Preisen], nicht gemäß den Mobile Services-Preisen.

##Vergleich von Migration und Upgrade

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###Verbesserungen im Mobile Apps-.NET-Server-SDK

Die Aktualisierung auf die neuen [Mobile Apps-SDK-NuGet-Pakete](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) hat die folgenden Vorteile:

- Mehr Flexibilität bei den NuGet-Abhängigkeiten. Die Hostumgebung stellt keine eigenen Versionen von NuGet-Paketen mehr bereit, sodass Sie alternative kompatible Versionen verwenden können. Falls aber neue kritische Bugfixes oder Sicherheitsupdates für das Mobile Services SDK oder die Abhängigkeiten vorhanden sind, müssen Sie Ihren Dienst manuell aktualisieren.

- Unterstützung für neue App Service-Authentifizierungsfeatures, die Ihnen die Verwendung einer gemeinsamen Authentifizierungskonfiguration über Web-Apps und mobile Apps hinweg ermöglichen. Authentifizierungsfeatures sind jetzt auch in einer eigenen OWIN-Middlewarekomponente enthalten. Dies bedeutet, dass für mobile Routen und Webrouten das gleiche Authentifizierungssetup verwendet werden kann.

- Unterstützung für andere ASP.NET-Projekttypen und -Routen. Sie können jetzt MVC- und Web-API-Controller in demselben Projekt wie Ihr mobiles Back-End-Projekt hosten.

- Mehr Flexibilität im mobilen SDK. Sie können explizit steuern, welche Features und Routen eingerichtet werden, z. B. Authentifizierung, Tabellen-APIs und der Endpunkt für die Pushregistrierung. Weitere Informationen finden Sie unter [Verwenden des .NET-Server-SDK für Azure Mobile Apps](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Weitere Optionen für DI-Frameworks. Für das Mobile Services-.NET-Server-SDK galt eine Abhängigkeit von autofac, aber mit dem Mobile Apps-Server-SDK können Sie andere Frameworks verwenden, z. B. [Unity](https://unity.codeplex.com/).

>[AZURE.NOTE]Die Mobile Services-Client-SDKs sind **nicht** mit dem neuen Mobile Apps-Server-SDK kompatibel. Wenn Sie über mobile Client-Apps verfügen, die eine Verbindung mit Ihrem vorhandenen mobilen Dienst herstellen, sollten Sie den Dienst laufen lassen, bis für alle mobilen Clients das Upgrade auf das Mobile Apps-**Client-SDK** durchgeführt wurde.
> 
> Der vorhandene mobile Dienst kann automatisch zu App Service *migriert* werden, ohne dass Änderungen an den vorhandenen Client-Apps durchgeführt werden müssen. Weitere Informationen zur Migration finden Sie unter [Migrieren des vorhandenen Mobile Service zu App Service].


##<a name="overview"></a>Übersicht über grundlegende Upgrades
Die einfachste Form des Upgrades besteht darin, eine neue Instanz einer mobilen App Service-App zu erstellen. In vielen Fällen erfolgt das Upgrade einfach durch den Wechsel zum neuen Mobile Apps-Server-SDK und einer erneuten Veröffentlichung Ihres Codes für eine neue mobile App. Es gibt jedoch einige Szenarien, die eine zusätzliche Konfiguration erfordern, beispielsweise erweiterte Authentifizierungsszenarien und das Arbeiten mit geplanten Aufträgen. Diese Fälle werden in den nachstehenden Abschnitten beschrieben.

>[AZURE.NOTE]Es wird empfohlen, die restlichen Abschnitte in diesem Thema vollständig zu lesen, bevor Sie mit einem Upgrade beginnen. Notieren Sie sich alle Features, die Sie verwenden möchten und die nachfolgend behandelt werden.

Sie können Ihren Code im eigenen Tempo verschieben und testen. Wenn die mobile App bereit ist, können Sie eine neue Version Ihrer Clientanwendung veröffentlichen. Zu diesem Zeitpunkt verfügen Sie über zwei Kopien Ihres Anwendungs-Back-Ends, die parallel ausgeführt werden. Wenn Sie eine Fehlerbehebung durchführen, muss diese auf beide Versionen angewendet werden. Wenn Sie Ihre Benutzer schließlich auf die neue Version aktualisiert haben, können Sie den ursprünglichen mobilen Dienst löschen.

Nachfolgend sind alle erforderlichen Schritte für das Upgrade aufgeführt:

1. Erstellen und Konfigurieren einer neuen mobilen App
2. Beseitigen aller Unsicherheiten in Bezug auf die Authentifizierung
3. Veröffentlichen einer neuen Version Ihrer Clientanwendung
4. Löschen der ursprünglichen Mobile Services-Instanz


##<a name="mobile-app-version"></a>Einrichten einer Mobile App-Version Ihrer Anwendung
Der erste Schritt beim Upgrade besteht darin, die mobile App-Ressource zum Hosten der neuen Version Ihrer Anwendung zu erstellen. Sie können unter [Vorschau auf das Azure-Verwaltungsportal] eine neue mobile App erstellen. Weitere Informationen finden Sie im Thema [Erstellen einer mobilen App].

Sie möchten sehr wahrscheinlich dieselbe Datenbank und denselben Notification Hub wie in Mobile Services verwenden. Sie können diese Werte aus der Registerkarte **Konfigurieren** im Mobile Services-Abschnitt im [Azure-Verwaltungsportal] kopieren. Kopieren Sie unterhalb von **Verbindungszeichenfolgen** die Werte `MS_NotificationHubConnectionString` und `MS_TableConnectionString`. Navigieren Sie zur Mobile App-Website, wählen Sie **Einstellungen**, **Anwendungseinstellungen**, und fügen Sie diese zum Abschnitt **Verbindungszeichenfolgen** hinzu. Überschreiben Sie hierbei ggf. vorhandene Werte.

Mobile Apps bietet ein neues [Mobile App Server SDK], das einen Großteil der Funktionalität der Mobile Services-Laufzeit bereitstellt. Sie sollten zunächst das Mobile Services-NuGet-Paket aus Ihrem vorhandenen Projekt entfernen und stattdessen das Server-SDK einfügen. Die meisten Entwickler sollten für dieses Upgrade das `Microsoft.Azure.Mobile.Server.Quickstart`-Paket herunterladen und installieren, da hierbei der komplette erforderliche Satz per Pull-Vorgang bereitgestellt wird. Anschließend ersetzen Sie in der Datei „WebApiConfig.cs“

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

durch

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]Wenn Sie mehr über das neue .NET-Server-SDK und die Features zum Hinzufügen oder Entfernen erfahren möchten, lesen Sie das Thema [Verwenden des .NET-Server-SDK].

Es gibt einige andere Unterschiede zwischen den SDKs von Mobile Services und denen von Mobile Apps, die aber leicht zu verständlich sind. Im Verlauf des Projekts müssen Sie möglicherweise einige Anweisungen ändern. Visual Studio bietet hierbei Unterstützung.

Sie müssen das `[MobileAppController]`-Attribut für alle API-Controller hinzufügen, indem Sie einfach den Decorator direkt vor der Klassendefinition platzieren.

Es gibt kein `[AuthorizeLevel]`-Attribut mehr, und Sie sollten stattdessen Ihre Controller und Methoden mit dem standardmäßigen ASP.NET-`[Authorize]`-Attribut ergänzen. Beachten Sie auch, dass alle Domänencontroller ohne `[AuthorizeLevel]` nicht mehr durch einen Anwendungsschlüssel geschützt sind. Sie werden als öffentlich behandelt. Das neue SDK verwendet keine Anwendungs- und Masterschlüssel mehr.

Das wichtigste Element für Pushvorgänge, das Sie möglicherweise im Server-SDK vermissen, ist die Klasse "PushRegistrationHandler". Registrierungen werden in Mobile Apps etwas anders gehandhabt, es sind standardmäßig Registrierungen ohne Tags aktiviert. Das Verwalten von Tags kann mithilfe von benutzerdefinierten APIs erfolgen. Weitere Informationen finden Sie im Thema [Hinzufügen von Pushbenachrichtigungen zu Ihrer mobilen App].

Geplante Aufträge sind nicht in Mobile Apps integriert. Deshalb müssen vorhandene Aufträge in Ihrem .NET-Back-End manuell aktualisiert werden. Eine Option hierbei ist das Erstellen eines geplanten [WebJob] auf der Mobile App-Codesite. Sie können auch einen Controller einrichten, der Ihren Auftragscode enthält und den [Azure Scheduler] zum Ansprechen dieses Endpunkts zum erwarteten Zeitpunkt konfigurieren.

Das `ApiServices`-Objekt ist nicht mehr länger Teil des SDKs. Um auf Einstellungen für die mobile Anwendung zuzugreifen, verwenden Sie Folgendes:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

Auf ähnliche Weise erfolgt die Protokollierung jetzt über das standardmäßige Schreiben von ASP.NET-Ablaufverfolgungen:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Überlegungen zur Authentifizierung
Einer der größten Unterschiede zwischen Mobile Apps und Mobile Services besteht darin, dass die Anmeldung für Mobile Apps nun nicht über die Codesite, sondern über das App Service-Gateway erfolgt. Wenn die Ressourcengruppe noch keines besitzt, können Sie ein Gateway bereitstellen, indem Sie zu Ihrer mobilen Azure-App im Verwaltungsportal navigieren. Wählen Sie dann **Einstellungen**, und klicken Sie dann in der Kategorie **Mobile** auf **Benutzerauthentifizierung**. Klicken Sie auf **Erstellen**, um Ihrer mobilen App ein Gateway zuzuordnen.

Für die meisten Anwendungen sind darüber hinaus keine zusätzlichen Schritte erforderlich, aber es gibt einige erweiterte Szenarien, die beachtet werden müssen.

Für die meisten Apps ist es ausreichend, einfach eine neue Registrierung für den gewünschten Identitätsanbieter zu verwenden. Informationen zum Hinzufügen einer Identität zu einer App Service-App finden Sie im Lernprogramm [Hinzufügen der Authentifizierung zu Ihrer mobilen App].

Wenn Ihre Anwendung Benutzer-ID-Abhängigkeiten nutzt und IDs beispielsweise in einer Datenbank speichert, muss beachtet werden, dass die Benutzer-IDs zwischen Mobile Services und mobilen App Service-Apps unterschiedlich sind. Es ist jedoch möglich, die Mobile Services-Benutzer-ID in Ihrer mobilen App Service-App über den folgenden Code abzurufen (hier wird als Beispiel Facebook verwendet):

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Wenn Sie Abhängigkeiten von Benutzer-IDs nutzen, ist es außerdem wichtig, nach Möglichkeit dieselbe Registrierung für einen Identitätsanbieter zu verwenden. Benutzer-IDs sind typischerweise auf die verwendete Anwendungsregistrierung ausgelegt, deshalb könnte das Einführen einer neuen Registrierung Probleme beim Abgleich von Benutzern mit den zugehörigen Daten verursachen. Falls aus bestimmten Gründen für Ihre Anwendung dieselbe Systematisierungsbestrebungen benötigt wird, können Sie die folgenden Schritte ausführen:

1. Kopieren Sie Client-ID und die geheimen Clientverbindungsinformationen für jeden Anbieter, der von Ihrer Anwendung genutzt wird.
2. Fügen Sie die /signin-*-Endpunkte des Gateways als zusätzlichen Umleitungs-URI für jeden Anbieter hinzu. 

>[AZURE.NOTE]Einige Anbieter, beispielsweise Twitter und Microsoft-Konto, lassen die Festlegung mehrerer Umleitungs-URIs in verschiedenen Domänen nicht zu. Wenn Ihre App einen dieser Anbieter verwendet und Abhängigkeiten von Benutzer-IDs verwendet, wird empfohlen, zu diesem Zeitpunkt kein Upgrade durchzuführen.

##<a name="updating-clients"></a>Aktualisieren von Clients
Sobald Sie über ein betriebsbereites Mobile App-Back-End verfügen, können Sie Ihre Clientanwendung für die Nutzung der neuen mobilen App aktualisieren. Mobile Apps umfasst außerdem eine neue Version der Mobile Services-Client-SDKs, die es Entwicklern ermöglichen, die Vorteile der neuen App Service-Features zu nutzen. Sobald Sie über eine Mobile App-Version Ihres Back-Ends verfügen, können Sie eine neue Version Ihrer Clientanwendung veröffentlichen, die die neue SDK-Version nutzt.

Die wichtigste Änderung, die im Clientcode erforderlich ist, betrifft den Konstruktor. Zusätzlich zur URL der Mobile App-Website müssen Sie die URL des App Service-Gateways bereitstellen, das Ihre Authentifizierungseinstellungen verwaltet:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

Auf diese Weise kann der Client Anforderungen an die Komponenten Ihrer mobilen App routen. Nähere Einzelheiten zu Ihrer Zielplattform finden Sie im entsprechenden Thema [Erstellen einer mobilen App].

Im selben Update müssen Sie Registrierungsaufrufe von Pushbenachrichtigungen anpassen, die Sie durchführen. Es gibt neue APIs, die Verbesserungen am Registrierungsprozess vornehmen (mit Verwendung von Windows als Beispiel):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Nähere Einzelheiten zu Ihrer Zielplattform finden Sie in den Themen [Hinzufügen von Pushbenachrichtigungen zu Ihrer mobilen App] und [Senden von plattformübergreifenden Pushbenachrichtigungen].

Sobald Ihre Kunden diese Updates erhalten haben, können Sie die Mobile Services-Version Ihrer App löschen. An diesem Punkt haben Sie die vollständige Aktualisierung auf eine mobile App Service-App durchgeführt und dafür das aktuelle Mobile Apps-Server-SDK verwendet.

<!-- URLs. -->

[Vorschau auf das Azure-Verwaltungsportal]: https://portal.azure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Was sind Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /de-DE/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Erstellen einer mobilen App]: app-service-mobile-xamarin-ios-get-started.md
[Hinzufügen von Pushbenachrichtigungen zu Ihrer mobilen App]: app-service-mobile-xamarin-ios-get-started-push.md
[Hinzufügen der Authentifizierung zu Ihrer mobilen App]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /de-DE/documentation/services/scheduler/
[WebJob]: ../app-service-web/websites-webjobs-resources.md
[Senden von plattformübergreifenden Pushbenachrichtigungen]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[Verwenden des .NET-Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[Migrieren des vorhandenen Mobile Service zu App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[App Service-Preisen]: https://azure.microsoft.com/de-DE/pricing/details/app-service/

<!---HONumber=Nov15_HO3-->