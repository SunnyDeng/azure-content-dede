<properties 
	pageTitle="Migrieren von Mobile Services in eine mobile App Service-App" 
	description="Erfahren Sie, wie einfach Sie Ihre Mobile Services-Anwendung in eine mobile App Service-App migrieren können." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/23/2015" 
	ms.author="mahender"/>

# Migrieren vorhandener Azure Mobile Services in eine mobile Azure App Service-App

In diesem Thema erfahren Sie, wie Sie eine vorhandene Anwendung aus Azure Mobile Services in eine neue mobile App Service-App migrieren können. Alle vorhandenen Mobile Services-Apps können auf einfache Weise in eine neue mobile App Service-App migriert werden. Während der Migration kann Ihre vorhandene Mobile Services-Anwendung weiterhin betrieben werden. Langfristig soll die Vorgehensweise für die Migration sogar noch weiter vereinfacht werden, aber bis dahin müssen für eine Migration die folgenden Schritte ausgeführt werden.

>[AZURE.NOTE]Eine Migration wird derzeit nur für Kunden unterstützt, die das Mobile Services .NET-Back-End verwenden. Anwendungen, die das Node.JS-Back-End verwenden, müssen vorerst in Mobile Services verbleiben.

##<a name="understand"></a>Grundlegendes zu mobilen App Service-Apps

Mobile App Service-Apps sind eine neue Methode zum Erstellen mobiler Anwendungen mithilfe von Microsoft Azure. Weitere Einzelheiten zu mobilen Apps erhalten Sie im Thema [Was sind mobile Apps?].

Bei einer Migration in mobile Apps kann die gesamte Funktionalität der App (sowie der Code) beibehalten werden. Darüber hinaus stehen neue Funktionen für die Anwendung zur Verfügung. Im Mobile Apps-Modell wird Ihr Code tatsächlich in einer Web-App ausgeführt (der neuen Version von Azure-Websites). Sie haben vollständige Kontrolle über die Web-App und deren Funktionsweise. Zusätzlich können jetzt Funktionen der Web-App verwendet werden, die bisher für Mobile Services-Kunden nicht verfügbar waren. Dazu gehören beispielsweise Traffic Manager und Entwicklungsslots.

Mit dem neuen Modell werden außerdem einige Schwierigkeiten beseitigt, die beim Arbeiten mit Mobile Services auftraten. Jetzt kann eine beliebige Version eines NuGet-Pakets bereitgestellt werden, ohne sich über Konflikte durch Abhängigkeiten Gedanken machen zu müssen. Weitere Informationen zu den Vorteilen der Migration finden Sie im Thema [Ich verwende bereits Websites und mobile Dienste – welche Vorteile bietet App Service?].

Wenn Sie eine mobile App Service-App erstellen, erhalten Sie Folgendes:

- Eine Mobile App-Ressource mit neuer Funktionalität 
- Eine Mobile App-Codesite, die Ihr Web-API-Projekt mit dem Mobile App Server SDK ausführt
- Ein App Service-Gateway, das die Anmeldung übernimmt und Sie beim Hinzufügen von App Service-API-Apps zu Ihrer Anwendung unterstützt

##<a name="overview"></a>Übersicht über die grundlegenden Schritte bei der Migration
Die einfachste Form der Migration besteht darin, eine neue Instanz einer mobilen App Service-App zu erstellen. In vielen Fällen erfolgt die Migration einfach durch den Wechsel zum neuen Server-SDK und einer erneuten Veröffentlichung Ihres Codes auf einer neuen mobilen App. Es gibt jedoch einige Szenarien, die eine zusätzliche Konfiguration erfordern, beispielsweise erweiterte Authentifizierungsszenarien und das Arbeiten mit geplanten Aufträgen. Diese Fälle werden in den nachstehenden Abschnitten beschrieben.

>[AZURE.NOTE]Es wird empfohlen, die restlichen Abschnitte in diesem Thema vollständig zu lesen, bevor Sie mit einer Migration beginnen. Notieren Sie sich alle Features, die Sie verwenden möchten und die nachfolgend behandelt werden.

Sie können Ihren Code im eigenen Tempo verschieben und testen. Wenn die mobile App bereit ist, können Sie eine neue Version Ihrer Clientanwendung veröffentlichen. Zu diesem Zeitpunkt verfügen Sie über zwei Kopien Ihrer Anwendung, die parallel ausgeführt werden. Wenn Sie eine Fehlerbehebung durchführen, muss diese auf beide Versionen angewendet werden. Wenn Sie Ihre Benutzer schließlich auf die neue Version aktualisiert haben, können Sie den ursprünglichen mobilen Dienst löschen.

Nachfolgend werden alle erforderlichen Schritte für die Migration aufgeführt:

1. Erstellen und Konfigurieren einer neuen mobilen App
2. Beseitigen aller Unsicherheiten in Bezug auf die Authentifizierung
3. Veröffentlichen einer neuen Version Ihrer Clientanwendung
4. Löschen der ursprünglichen Mobile Services-Instanz


##<a name="mobile-app-version"></a>Einrichten einer Mobile App-Version Ihrer Anwendung
Der erste Schritt bei der Migration besteht darin, den App Service zum Hosten der neuen Version Ihrer Anwendung zu erstellen. Sie können unter [Vorschau auf das Azure-Verwaltungsportal] eine neue mobile App erstellen. Weitere Informationen finden Sie im Thema [Erstellen einer mobilen App].

Sie möchten sehr wahrscheinlich dieselbe Datenbank und denselben Notification Hub wie in Mobile Services verwenden. Sie können diese Werte aus der Registerkarte **Konfigurieren** im Mobile Services-Abschnitt im [Azure-Verwaltungsportal] kopieren. Kopieren Sie unterhalb von **Verbindungszeichenfolgen** die Werte `MS_NotificationHubConnectionString` und `MS_TableConnectionString`. Navigieren Sie zur Mobile App-Codesite, wählen Sie **Einstellungen**, **Anwendungseinstellungen**, und fügen Sie diese Verbindungszeichenfolgen hinzu. Überschreiben Sie hierbei ggf. vorhandene Werte. Zusätzlich sollten Sie diese Werte auch zur Mobile App-Ressource hinzufügen. Navigieren Sie hierzu zum Blatt "Mobile App", wählen Sie **Einstellungen** und anschließend **Eigenschaften**. Klicken Sie auf den Link mit dem Namen **API-App-Host**, um den Standort anzuzeigen, der Ihre Mobile App-Ressource hostet. Wechseln Sie zu **Einstellungen**, **Anwendungseinstellungen**, und fügen Sie die Verbindungszeichenfolgen wie in der Codesite ein. Ändern Sie keinen der weiteren Werte, da dies zu Fehlern in der Mobile App-Funktionalität führen könnte. Beachten Sie, dass zu diesem Zeitpunkt das Blatt "Mobile App" selbst nach diesem Konfigurationsschritt weiterhin die vorhandenen Verbindungen anzeigt. Nach der Aktualisierung der Mobile Apps-Benutzeroberfläche sind möglicherweise weitere Schritte erforderlich.

Mobile Apps bietet ein neues [Mobile App Server SDK], das einen Großteil der Funktionalität der Mobile Services-Laufzeit bereitstellt. Sie sollten das Mobile Services-NuGet-Paket aus Ihrem vorhandenen Projekt entfernen und stattdessen das Server-SDK einfügen. Sie müssen möglicherweise einige Anweisungen ändern. Visual Studio bietet hierbei Unterstützung. Das wichtigste Element, das Sie möglicherweise im Server-SDK vermissen, ist die Klasse "PushRegistrationHandler". Registrierungen werden in Mobile Apps etwas anders gehandhabt, es sind standardmäßig Registrierungen ohne Tags aktiviert. Das Verwalten von Tags kann mithilfe von benutzerdefinierten APIs erfolgen. Weitere Informationen finden Sie im Thema [Hinzufügen von Pushbenachrichtigungen zu Ihrer mobilen App].

Geplante Aufträge sind nicht in Mobile Apps integriert, deshalb müssen vorhandene Aufträge in Ihrem .NET-Back-End manuell migriert werden. Eine Option hierbei ist das Erstellen eines geplanten [WebJob] auf der Mobile App-Codesite. Sie können auch einen Controller einrichten, der Ihren Auftragscode enthält und den [Azure Scheduler] zum Ansprechen dieses Endpunkts zum erwarteten Zeitpunkt konfigurieren.


##<a name="authentication"></a>Überlegungen zur Authentifizierung
Einer der größten Unterschiede zwischen Mobile Apps und Mobile Services besteht darin, dass die Anmeldung für Mobile Apps nicht über die Codesite, sondern über das App Service-Gateway erfolgt. Für die meisten Anwendungen erfordert dies keine zusätzlichen Schritte, aber es gibt einige erweiterte Szenarien, die beachtet werden müssen.

Für die meisten Apps ist es ausreichend, einfach eine neue Registrierung für den gewünschten Identitätsanbieter zu verwenden. Informationen zum Hinzufügen einer Identität zu einer App Service-App finden Sie im Lernprogramm [Hinzufügen der Authentifizierung zu Ihrer mobilen App].

Wenn Ihre Anwendung Benutzer-ID-Abhängigkeiten nutzt und IDs beispielsweise in einer Datenbank speichert, muss beachtet werden, dass die Benutzer-IDs zwischen Mobile Services und mobilen App Service-Apps unterschiedlich sind. Es ist jedoch möglich, die Mobile Services-Benutzer-ID in Ihrer mobilen App Service-App über den folgenden Code abzurufen (hier wird als Beispiel Facebook verwendet):

    ServiceUser user = (ServiceUser) this.User;
    FacebookCredentials creds = (await user.GetIdentitiesAsync()).OfType< FacebookCredentials >().FirstOrDefault();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

Wenn Sie Abhängigkeiten von Benutzer-IDs nutzen, ist es außerdem wichtig, nach Möglichkeit dieselbe Registrierung für einen Identitätsanbieter zu verwenden. Benutzer-IDs sind typischerweise auf die verwendete Anwendungsregistrierung ausgelegt, deshalb könnte das Einführen einer neuen Registrierung Probleme beim Abgleich von Benutzern mit den zugehörigen Daten verursachen. Falls aus bestimmten Gründen für Ihre Anwendung dieselbe Systematisierungsbestrebungen benötigt wird, können Sie die folgenden Schritte ausführen:

1. Kopieren Sie Client-ID und die geheimen Clientverbindungsinformationen für jeden Anbieter, der von Ihrer Anwendung genutzt wird.
2. Fügen Sie die /signin-*-Endpunkte des Gateways als zusätzlichen Umleitungs-URI für jeden Anbieter hinzu. 

>[AZURE.NOTE]Einige Anbieter, beispielsweise Twitter und Microsoft-Konto, lassen die Festlegung mehrerer Umleitungs-URIs in verschiedenen Domänen nicht zu. Wenn Ihre App einen dieser Anbieter verwendet und Abhängigkeiten von Benutzer-IDs verwendet, wird empfohlen, zu diesem Zeitpunkt keine Migration durchzuführen.

##<a name="updating clients"></a>Aktualisieren von Clients
Sobald Sie über ein betriebsbereites Mobile App-Back-End verfügen, können Sie Ihre Clientanwendung für die Nutzung der neuen mobilen App aktualisieren. Mobile Apps umfasst außerdem eine neue Version der Mobile Services-Client-SDKs, die es Entwicklern ermöglichen, die Vorteile der neuen App Service-Features zu nutzen. Sobald Sie über eine Mobile App-Version Ihres Back-Ends verfügen, können Sie eine neue Version Ihrer Clientanwendung veröffentlichen, die die neue SDK-Version nutzt.

Die wichtigste Änderung, die im Clientcode erforderlich ist, betrifft den Konstruktor. Zusätzlich zur URL der Mobile App-Codesite und dem Anwendungsschlüssel müssen Sie die URL des App Service-Gateways bereitstellen, das Ihre Authentifizierungseinstellungen verwaltet:

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso-code.azurewebsites.net", //URL of the Mobile App Code
        "https://contosogateway.azurewebsites.net", //URL of the App Service Gateway
        "983682c4-f043-483e-a75b-8a8545fc1846"
    );

Auf diese Weise kann der Client Anforderungen an die Komponenten Ihrer mobilen App routen. Nähere Einzelheiten zu Ihrer Zielplattform finden Sie im entsprechenden Thema [Erstellen einer mobilen App].

Im selben Update müssen Sie Registrierungsaufrufe von Pushbenachrichtigungen anpassen, die Sie durchführen. Es gibt neue APIs, die Verbesserungen am Registrierungsprozess vornehmen (mit Verwendung von Windows als Beispiel):

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

Nähere Einzelheiten zu Ihrer Zielplattform finden Sie in den Themen [Hinzufügen von Pushbenachrichtigungen zu Ihrer mobilen App] und [Senden von plattformübergreifenden Pushbenachrichtigungen].

Sobald Ihre Kunden diese Updates erhalten haben, können Sie die Mobile Services-Version Ihrer App löschen. Damit ist die Migration in eine mobile App Service-App vollständig abgeschlossen.

<!-- URLs. -->

[Vorschau auf das Azure-Verwaltungsportal]: https://portal.azure.com/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Was sind mobile Apps?]: app-service-mobile-value-prop-preview.md
[Ich verwende bereits Websites und mobile Dienste – welche Vorteile bietet App Service?]: /de-de/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Erstellen einer mobilen App]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[Hinzufügen von Pushbenachrichtigungen zu Ihrer mobilen App]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[Hinzufügen der Authentifizierung zu Ihrer mobilen App]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Azure Scheduler]: /de-de/documentation/services/scheduler/
[WebJob]: ../app-service-web/websites-webjobs-resources.md
[Senden von plattformübergreifenden Pushbenachrichtigungen]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md

<!---HONumber=August15_HO6-->