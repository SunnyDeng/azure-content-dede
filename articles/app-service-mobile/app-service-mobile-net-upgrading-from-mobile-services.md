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
	ms.date="11/29/2015" 
	ms.author="mahender"/>

# Aktualisieren des vorhandenen .NET-Azure Mobile Service auf App Service

Mobile App Service-Apps sind eine neue Methode zum Erstellen mobiler Anwendungen mit Microsoft Azure. Weitere Informationen finden Sie unter [Was sind Mobile Apps?].

In diesem Thema wird beschrieben, wie Sie eine vorhandene .NET-Back-End-Anwendung von Azure Mobile Services auf eine neue mobile App Service-App aktualisieren. Während Sie dieses Upgrade ausführen, können Sie Ihre vorhandene Mobile Services-Anwendung weiter betreiben.

Wenn ein Mobil-Back-End auf Azure App Service aktualisiert wird, hat dieses Zugriff auf alle App Service-Features, und die Abrechnung erfolgt gemäß [App Service-Preisen], nicht gemäß den Mobile Services-Preisen.

##Vergleich von Migration und Upgrade

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

>[AZURE.TIP]Es wird empfohlen, vor einem Upgrade eine [Migration durchzuführen](app-service-mobile-migrating-from-mobile-services.md). Auf diese Weise können Sie beide Versionen der Anwendung in den gleichen App Service-Plan übernehmen, ohne dass zusätzliche Kosten anfallen.

###Verbesserungen im Mobile Apps-.NET-Server-SDK

Die Aktualisierung auf das neue [Mobile Apps-SDK](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) hat die folgenden Vorteile:

- Mehr Flexibilität bei den NuGet-Abhängigkeiten. Die Hostumgebung stellt keine eigenen Versionen von NuGet-Paketen mehr bereit, sodass Sie alternative kompatible Versionen verwenden können. Falls aber neue kritische Bugfixes oder Sicherheitsupdates für das Mobile Services SDK oder die Abhängigkeiten vorhanden sind, müssen Sie Ihren Dienst manuell aktualisieren.

- Mehr Flexibilität im mobilen SDK. Sie können explizit steuern, welche Features und Routen eingerichtet werden, z. B. Authentifizierung, Tabellen-APIs und der Endpunkt für die Pushregistrierung. Weitere Informationen finden Sie unter [Verwenden des .NET-Server-SDK für Azure Mobile Apps](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup).

- Unterstützung für andere ASP.NET-Projekttypen und -Routen. Sie können jetzt MVC- und Web-API-Controller in demselben Projekt wie Ihr mobiles Back-End-Projekt hosten.

- Unterstützung für neue App Service-Authentifizierungsfeatures, die Ihnen die Verwendung einer gemeinsamen Authentifizierungskonfiguration über Web-Apps und mobile Apps hinweg ermöglichen.

##<a name="overview"></a>Übersicht über grundlegende Upgrades

In vielen Fällen erfolgt das Upgrade einfach durch den Wechsel zum neuen Mobile Apps-Server-SDK und einer erneuten Veröffentlichung Ihres Codes für eine neue Mobile Apps-Instanz. Es gibt jedoch einige Szenarien, die eine zusätzliche Konfiguration erfordern, beispielsweise erweiterte Authentifizierungsszenarien und das Arbeiten mit geplanten Aufträgen. Diese Fälle werden in den nachstehenden Abschnitten beschrieben.

>[AZURE.TIP]Es wird empfohlen, die restlichen Abschnitte in diesem Thema vollständig zu lesen, bevor Sie mit einem Upgrade beginnen. Notieren Sie sich alle Features, die Sie verwenden möchten und die nachfolgend behandelt werden.

Die Mobile Services-Client-SDKs sind **nicht** mit dem neuen Mobile Apps-Server-SDK kompatibel. Um die Kontinuität von Diensten für Ihre App zu gewährleisten, sollten Sie keine Änderungen auf einer Website veröffentlichen, die derzeit veröffentlichte Clients bereitstellt. Stattdessen sollten Sie eine neue mobile App erstellen, die als Duplikat dient. Sie können diese Anwendung in den gleichen App Service-Plan aufnehmen, um zu vermeiden, dass zusätzliche Kosten anfallen.

Anschließend verfügen Sie über zwei Versionen der Anwendung: eine unveränderte Version, die veröffentlichte praktisch genutzte Apps bedient, und eine zweite Version, die Sie upgraden und als Ziel für eine neue Clientversion verwenden. Sie können den eigenen Code im eigenen Tempo verschieben und testen, aber sie sollten sicherstellen, dass alle Fehlerbehebungen, die Sie vornehmen, auf beide Versionen angewendet werden. Wenn Sie das Gefühl haben, dass die gewünschte Anzahl von Client-Apps in der Praxis auf die neueste Version aktualisiert wurden, können Sie die ursprüngliche migrierte App ggf. löschen.

Der vollständige Überblick über den Upgradevorgang sieht wie folgt aus:

1. Erstellen einer neuen mobilen App
2. Aktualisieren des Projekts zur Verwendung der neuen Server-SDKs
3. Veröffentlichen einer neuen Version Ihrer Clientanwendung
4. (Optional) Löschen der ursprünglichen migrierten Instanz

##<a name="mobile-app-version"></a>Erstellen einer zweiten Anwendungsinstanz
Der erste Schritt beim Upgrade besteht darin, die Mobile App-Ressource zum Hosten der neuen Version Ihrer Anwendung zu erstellen. Wenn Sie bereits einen vorhandenen mobilen Dienst migriert haben, sollten Sie diese Version im gleichen Hostingplan erstellen. Öffnen Sie das [Azure-Portal], und navigieren Sie zur migrierten Anwendung. Notieren Sie sich den App Service-Plan, in dem diese ausgeführt wird.

Als Nächstes erstellen Sie die zweite Anwendungsinstanz anhand der [Anleitung zum Erstellen eines .NET-Back-Ends](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app). Wenn Sie zur Auswahl Ihres App Service-Plans oder „Hostingplans“ aufgefordert werden, wählen Sie den Plan der migrierten Anwendung aus.

Sie möchten sehr wahrscheinlich dieselbe Datenbank und denselben Notification Hub wie in Mobile Services verwenden. Zum Kopieren dieser Werte öffnen Sie das [Azure-Portal], navigieren Sie zur ursprünglichen Anwendung, und klicken Sie dann auf **Einstellungen** > **Anwendungseinstellungen**. Kopieren Sie unterhalb von **Verbindungszeichenfolgen** die Werte `MS_NotificationHubConnectionString` und `MS_TableConnectionString`. Navigieren Sie zu Ihrer neuen Upgradewebsite, und fügen Sie die Werte ein, wobei alle vorhandenen Werte überschrieben werden. Wiederholen Sie diesen Vorgang für alle anderen für Ihre App erforderlichen Anwendungseinstellungen. Wenn Sie keinen migrierten Dienst verwenden, finden Sie die Verbindungszeichenfolgen und App-Einstellungen auf der Registerkarte **Konfigurieren** im Abschnitt „Mobile Services“ des [klassischen Azure-Portals] lesen.

Erstellen Sie eine Kopie des ASP.NET-Projekts für Ihre Anwendung, und veröffentlichen Sie diese auf Ihrer neuen Website. Verwenden Sie eine Kopie Ihrer mit der neuen URL aktualisierten Clientanwendung, und überprüfen Sie, ob alles wie erwartet funktioniert.

## Aktualisieren des Serverprojekts

Mobile Apps bietet ein neues [Mobile App Server SDK], das einen Großteil der Funktionalität der Mobile Services-Laufzeit bereitstellt. Entfernen Sie zunächst alle Verweise auf die Mobile Services-Pakete. Suchen Sie im NuGet-Paket-Manager nach `WindowsAzure.MobileServices.Backend`. Für die meisten Apps werden hier mehrere Pakete angezeigt, einschließlich `WindowsAzure.MobileServices.Backend.Tables` und `WindowsAzure.MobileServices.Backend.Entity`. Beginnen Sie in einem solchen Fall mit dem niedrigsten Paket in der Abhängigkeitsstruktur, z. B. `Entity`, und entfernen Sie dieses. Wenn Sie gefragt werden, ob alle abhängigen Pakete entfernt werden sollen, verneinen Sie dies. Wiederholen Sie diesen Vorgang, bis Sie `WindowsAzure.MobileServices.Backend` selbst entfernt haben.

An diesem Punkt verfügen Sie über ein Projekt ohne Verweise auf Mobile Services-SDKs.

Als Nächstes fügen Sie Verweise auf das Mobile Apps-SDK hinzu. Die meisten Entwickler sollten für dieses Upgrade das `Micrsoft.Azure.Mobile.Server.Quickstart`-Paket herunterladen und installieren, da hierbei der komplette erforderliche Satz per Pullvorgang bereitgestellt wird.

Aufgrund der Unterschiede zwischen den SDKs werden einige Compilerfehler auftreten, aber diese sind leicht zu beheben und werden im Rest dieses Abschnitts behandelt.

### Basiskonfiguration

Anschließend ersetzen Sie in der Datei „WebApiConfig.cs“ Folgendes:

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

Wenn Ihre App die Authentifizierungsfeatures verwendet, müssen Sie auch eine OWIN-Middleware registrieren. In diesem Fall sollten Sie den oben stehenden Konfigurationscode in eine neue OWIN-Startklasse verschieben.
 
1. Fügen Sie das NuGet-Paket `Microsoft.Owin.Host.SystemWeb` hinzu, wenn es nicht bereits im Projekt enthalten ist.
2. Klicken Sie in Visual Studio mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Hinzufügen** -> **Neues Element** aus. Wählen Sie **Web** -> **Allgemein** -> **OWIN-Startklasse** aus. 
3. Verschieben Sie den oben stehenden Code für „MobileAppConfiguration“ aus `WebApiConfig.Register()` in die `Configuration()`-Methode Ihrer neuen Startklasse.

Stellen Sie sicher, dass die `Configuration()`-Methode folgendermaßen endet:

        app.UseWebApi(config)
        app.UseAppServiceAuthentication(config);

Es gibt weitere Änderungen im Zusammenhang mit der Authentifizierung, die weiter unten im Abschnitt über die vollständige Authentifizierung behandelt werden.

### Arbeiten mit Daten

In Mobile Services diente der Name der mobilen App als Standardname für das Schema im Entity Framework-Setup.

Um sicherzustellen, dass das gleiche Schema referenziert wird wie zuvor, legen Sie das Schema in „DbContext“ für Ihre Anwendung folgendermaßen fest:

        string schema = System.Configuration.ConfigurationManager.AppSettings.Get("MS_MobileServiceName");
        
Wenn Sie den oben genannten Schritt ausführen, stellen Sie sicher, dass „MS\_MobileServiceName“ festgelegt ist. Sie können einen anderen Schemanamen angeben, falls dieser in der Anwendung zuvor angepasst wurde.

### Systemeigenschaften

#### Benennung 

Im Azure Mobile Services-Server-SDK enthalten Systemeigenschaften immer einen doppelten Unterstrich (`__`) als Präfix für die Eigenschaften:

- __\_\_createdAt
- __\_\_updatedAt
- __\_\_deleted
- __\_\_version

Die Mobile Services-Client-SDKs verwenden eine spezielle Logik für die Analyse von Systemeigenschaften im diesem Format.

In Azure Mobile Apps weisen die Systemeigenschaften kein spezielles Format mehr auf und haben die folgenden Namen:

- createdAt
- updatedAt
- deleted
- Version

Die Mobile Apps-Client-SDKs verwenden die neuen Systemeigenschaftsnamen, daher sind keine Änderungen am Clientcode erforderlich. Wenn Sie jedoch direkte REST-Aufrufe an den Dienst senden, sollten Sie die Abfragen entsprechend ändern.

#### Lokaler Speicher 

Durch die Änderungen an den Namen der Systemeigenschaften ist eine lokale Datenbank für die Offlinesynchronisierung für Mobile Services nicht mit Mobile Apps kompatibel. Wenn möglich, sollten Sie Client-Apps erst dann von Mobile Services auf Mobile Apps aktualisieren, wenn die ausstehenden Änderungen an den Server gesendet wurden. Anschließend sollte die aktualisierte App einen neuen Dateinamen für die Datenbank verwenden.

Wenn eine Client-App von Mobile Services auf Mobile Apps aktualisiert wird, während die Vorgangswarteschlange ausstehende Offlineänderungen enthält, muss die Systemdatenbank aktualisiert werden, damit die neuen Spaltennamen verwendet werden. In iOS kann dies mit einfachen Migrationen zum Ändern der Spaltennamen erreicht werden. In Android und dem .NET-verwalteten Client sollten Sie benutzerdefinierten SQL-Code schreiben, um die Spalten Ihrer Datenobjekttabellen umzubenennen.

In iOS sollten Sie das Core-Datenschema für Ihre Datenentitäten folgendermaßen ändern. Beachten Sie, dass die Eigenschaften `createdAt`, `updatedAt` und `version` keinen `ms_`-Präfix mehr aufweisen:

| Attribut | Typ | Hinweis |
|---------- |  ------ | -----------------------------------------------------|
| id | Zeichenfolge, als erforderlich gekennzeichnet | Primärschlüssel im Remotespeicher |
| createdAt | Date | (optional) Zuordnung zur createdAt-Systemeigenschaft |
| updatedAt | Date | (optional) Zuordnung zur updatedAt-Systemeigenschaft |
| Version | String | (optional) Zum Erkennen von Konflikten, Zuordnung zu „version“ |

#### Abfragen von Systemeigenschaften

In Azure Mobile Services werden Systemeigenschaften nicht standardmäßig gesendet, sondern nur auf Anforderung unter Verwendung der Abfragezeichenfolge `__systemProperties`. Im Gegensatz dazu werden Systemeigenschaften in Azure Mobile Apps **immer ausgewählt**, da sie Teil des Objektmodells des Server-SDK sind.

Diese Änderung wirkt sich hauptsächlich auf benutzerdefinierte Implementierungen von Domänen-Managern aus, z. B. Erweiterungen von `MappedEntityDomainManager`. Wenn Systemeigenschaften in Mobile Services nie von einem Client angefordert werden, ist es möglich, einen `MappedEntityDomainManager` zu verwenden, der nicht alle Eigenschaften zuordnet. In Azure Mobile Apps verursachen solche nicht zugeordneten Eigenschaften allerdings einen Fehler in GET-Abfragen.

Am einfachsten können Sie dieses Problem beheben, indem Sie die DTOs so ändern, dass sie nicht von `EntityData`, sondern von `ITableData` erben. Fügen Sie dann das `[NotMapped]`-Attribut zu den Feldern hinzu, die ausgelassen werden sollen.

Mit dem folgenden Code wird z. B. `TodoItem` ohne Systemeigenschaften definiert:

    using System.ComponentModel.DataAnnotations.Schema;

    public class TodoItem : ITableData
    {
        public string Text { get; set; }

        public bool Complete { get; set; }

        public string Id { get; set; }

        [NotMapped]
        public DateTimeOffset? CreatedAt { get; set; }

        [NotMapped]
        public DateTimeOffset? UpdatedAt { get; set; }

        [NotMapped]
        public bool Deleted { get; set; }

        [NotMapped]
        public byte[] Version { get; set; }
    }

Hinweis: Wenn Sie Fehlermeldungen für `NotMapped` erhalten, fügen Sie einen Verweis auf die Assembly `System.ComponentModel.DataAnnotations` hinzu.

### CORS

Mobile Services bot einen gewissen Grad an Unterstützung für CORS durch Umschließen (Wrapping) der ASP.NET CORS-Lösung. Diese Wrappingebene wurde entfernt, um dem Entwickler mehr Kontrolle zu geben, sodass [ASP.NET CORS-Unterstützung](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) jetzt direkt genutzt werden kann.

Bei der Verwendung von CORS ist ein wichtiger Aspekt zu beachten: Die Header `eTag` und `Location` müssen zulässig sein, damit das Client-SDKs ordnungsgemäß funktioniert.

### Pushbenachrichtigungen
Das wichtigste Element für Pushvorgänge, das Sie möglicherweise im Server-SDK vermissen, ist die Klasse "PushRegistrationHandler". Registrierungen werden in Mobile Apps etwas anders gehandhabt, es sind standardmäßig Registrierungen ohne Tags aktiviert. Das Verwalten von Tags kann mithilfe von benutzerdefinierten APIs erfolgen. Weitere Informationen finden Sie in den Anweisungen zum [Registrieren von Tags](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#tags).

### Geplante Aufträge
Geplante Aufträge sind nicht in Mobile Apps integriert. Deshalb müssen vorhandene Aufträge in Ihrem .NET-Back-End manuell aktualisiert werden. Eine Option hierbei ist das Erstellen eines geplanten [WebJob] auf der Mobile App-Codesite. Sie können auch einen Controller einrichten, der Ihren Auftragscode enthält und den [Azure Scheduler] zum Ansprechen dieses Endpunkts zum erwarteten Zeitpunkt konfigurieren.

### Verschiedene Änderungen
Alle ApiControllers, die von einem mobilen Client genutzt werden, müssen jetzt über das `[MobileAppController]`-Attribut verfügen. Es wird nicht mehr standardmäßig angegeben, damit andere ApiControllers von mobilen Formatierungsprogrammen nicht beeinträchtigt werden.

Das `ApiServices`-Objekt ist nicht mehr länger Teil des SDKs. Um auf Einstellungen für die mobile Anwendung zuzugreifen, verwenden Sie Folgendes:

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

Auf ähnliche Weise erfolgt die Protokollierung jetzt über das standardmäßige Schreiben von ASP.NET-Ablaufverfolgungen:

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>Überlegungen zur Authentifizierung

Die Authentifizierungskomponenten von Mobile Services wurden jetzt in das App Service-Feature „Authentifizierung/Autorisierung“ verschoben. Weitere Informationen zum Aktivieren dieses Features für Ihre Website finden Sie im Thema [Hinzufügen von Authentifizierung zu Ihrer mobilen App](app-service-mobile-ios-get-started-users.md).

Für einige Anbieter, z. B. AAD, Facebook und Google, sollten Sie die vorhandene Registrierung aus der kopierten Anwendung nutzen können. Sie müssen lediglich zum Portal des Identitätsanbieters navigieren und eine neue Umleitungs-URL zur Registrierung hinzufügen. Anschließend konfigurieren Sie „App Service-Authentifizierung/Autorisierung“ mit der Client-ID und dem geheimen Schlüssel.

### Controlleraktionsautorisierung
Alle Instanzen des `[AuthorizeLevel(AuthorizationLevel.User)]`-Attributs müssen jetzt so geändert werden, dass das ASP.NET-Standardattribut `[Authorize]` verwendet wird. Darüber hinaus sind Controller jetzt, wie in anderen ASP.NET-Anwendungen, standardmäßig anonym. Wenn Sie bisher eine der anderen AuthorizeLevel-Optionen, wie z. B. „Admin“ oder „Application“ verwendet haben, beachten Sie, dass diese nicht mehr vorhanden sind. Stattdessen können Sie AuthorizationFilters für gemeinsam genutzte geheime Schlüssel einrichten oder einen AAD-Dienstprinzipal konfigurieren, um sichere Dienst-zu-Dienst-Aufrufe zu ermöglichen.

### Abrufen weiterer Benutzerinformationen

Weitere Benutzerinformationen, z. B. Zugriffstoken, können Sie über die `GetAppServiceIdentityAsync()`-Methode abrufen:

        FacebookCredentials creds = await this.User.GetAppServiceIdentityAsync<FacebookCredentials>();
        
Wenn Ihre Anwendung Benutzer-ID-Abhängigkeiten nutzt und IDs beispielsweise in einer Datenbank speichert, ist außerdem zu beachten, dass die Benutzer-IDs von Mobile Services und mobilen App Service-Apps verschieden sind. Sie können die Mobile Services-Benutzer-ID aber dennoch abrufen. Alle ProviderCredentials-Unterklassen verfügen über eine UserId-Eigenschaft. Ausgehend von dem oben stehenden Beispiel:

        string mobileServicesUserId = creds.Provider + ":" + creds.UserId;
        
Wenn Ihre App Abhängigkeiten von Benutzer-IDs nutzt, ist es wichtig, nach Möglichkeit dieselbe Registrierung bei einem Identitätsanbieter zu verwenden. Benutzer-IDs sind typischerweise auf die verwendete Anwendungsregistrierung ausgelegt, deshalb könnte das Einführen einer neuen Registrierung Probleme beim Abgleich von Benutzern mit den zugehörigen Daten verursachen.

### Benutzerdefinierte Authentifizierung

Wenn Ihre Anwendung eine benutzerdefinierte Lösung für die Authentifizierung verwendet, sollten Sie sicherstellen, dass die aktualisierte Website Zugriff auf das System hat. Befolgen Sie die neuen Anweisungen für die benutzerdefinierte Authentifizierung in der [Übersicht über das .NET Server-SDK], um Ihre Lösung zu integrieren. Beachten Sie, dass die benutzerdefinierten Authentifizierungskomponenten noch in der Vorschauversion vorliegen.

##<a name="updating-clients"></a>Aktualisieren von Clients
Sobald Sie über ein betriebsbereites Mobile App-Back-End verfügen, können Sie an einer neuen Version Ihrer Clientanwendung arbeiten, die dieses nutzt. Mobile Apps enthält außerdem eine neue Version der Client-SDKs. Wie bei dem oben beschriebenen Serverupgrade müssen Sie vor der Installation der Mobile Apps-Versionen alle Verweise auf die Mobile Services-SDKs entfernen.

Einer der wichtigsten Unterschiede zwischen den Versionen ist, dass die Konstruktoren keinen Anwendungsschlüssel mehr benötigen. Sie brauchen jetzt nur noch die URL der mobilen App zu übergeben. Auf den .NET-Clients lautet der `MobileServiceClient`-Konstruktor nun:

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://contoso.azurewebsites.net", // URL of the Mobile App
        );

Informationen zum Installieren der neuen SDKs und zum Verwenden der neuen Struktur finden Sie unter den folgenden Links:

- [iOS 3.0.0 oder höher](app-service-mobile-ios-how-to-use-client-library.md)
- [.NET (Windows/Xamarin) 2.0.0 oder höher](app-service-mobile-dotnet-how-to-use-client-library.md) 

Wenn Ihre Anwendung Pushbenachrichtigungen verwendet, notieren Sie sich die speziellen Registrierungsanweisungen für jede Plattform, weil auch dort einige Änderungen vorgenommen wurden.

Wenn die neue Clientversion bereit ist, testen Sie sie mit dem aktualisierten Serverprojekt. Nachdem Sie sich vergewissert haben, dass sie funktioniert, können Sie eine neue Version der Anwendung für die Kunden freigeben. Nachdem Ihre Kunden diese Updates erhalten haben, können Sie die Mobile Services-Version Ihrer App schließlich löschen. An diesem Punkt haben Sie die vollständige Aktualisierung auf eine mobile App Service-App durchgeführt und dafür das aktuelle Mobile Apps-Server-SDK verwendet.

<!-- URLs. -->

[Azure-Portal]: https://portal.azure.com/
[klassischen Azure-Portals]: https://manage.windowsazure.com/
[Was sind Mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /de-DE/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /de-DE/documentation/services/scheduler/
[WebJob]: ../app-service-web/websites-webjobs-resources.md
[Verwenden des .NET-Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-migrating-from-mobile-services.md
[Migrate your existing Mobile Service to App Service]: app-service-mobile-migrating-from-mobile-services.md
[App Service-Preisen]: https://azure.microsoft.com/de-DE/pricing/details/app-service/
[Übersicht über das .NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_1210_2015-->