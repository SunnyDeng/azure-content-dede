<properties 
	pageTitle="Migrieren von Mobile Services in eine mobile App Service-App" 
	description="Erfahren Sie, wie einfach Sie Ihre Mobile Services-Anwendung in eine mobile App Service-App migrieren können." 
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
	ms.date="11/04/2015" 
	ms.author="mahender"/>

# Migrieren des vorhandenen Azure Mobile Service zu App Service 

In diesem Thema erfahren Sie, wie Sie eine vorhandene Anwendung aus Azure Mobile Services in eine neue mobile App Service-App migrieren können. Alle vorhandenen Mobile Services-Apps können auf einfache Weise in eine neue mobile App Service-App migriert werden. Während der Migration kann Ihre vorhandene Mobile Services-Anwendung weiterhin betrieben werden.

>[AZURE.NOTE]Derzeit können .NET-Back-End-Mobile Services manuell zu App Service migriert werden. Eine fertige Migrationsoberfläche für „Node.js“ und .NET wird in Kürze verfügbar sein. Wenn Sie eine manuelle Migration durchführen, können Sie Ihre vorhandene **service.azure-mobile.net**-URL aber *nicht* beibehalten.

Wenn eine App zu Azure App Service migriert wird, hat sie Zugriff auf alle App Service-Features, und die Abrechnung erfolgt gemäß [App Service-Preisen], nicht gemäß den Mobile Services-Preisen.

### <a name="why-host"></a>Gründe für das Hosten in App Service

App Service stellt eine Hostingumgebung mit umfangreicheren Features für Ihre Anwendung bereit. Durch das Hosten in App Service erhält Ihr Dienst Zugriff auf Stagingslots, benutzerdefinierte Domänen, Traffic Manager-Unterstützung und vieles mehr. Auch wenn die [Migration eines mobilen Diensts in eine mobile App in App Service] möglich ist, möchten einige Kunden diese Features ggf. sofort nutzen, ohne direkt das SDK zu aktualisieren.

Die wesentliche Einschränkung für das Hosten in App Service besteht darin, dass die in Mobile Services geplanten Aufträge nicht integriert werden und dass entweder der Azure Scheduler für benutzerdefinierte APIs eingerichtet werden muss oder die Webaufträgefeatures aktiviert werden müssen.

Weitere Informationen zu den Vorteilen von App Service finden Sie im Thema [Mobile Services im Vergleich zu App Service].

##Vergleich von Migration und Upgrade

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

  - Für Node.js-basierte Serverprojekte verfügt das neue [Mobile Apps-Node.js-SDK](https://github.com/Azure/azure-mobile-apps-node) über eine Reihe neuer Features. Beispielsweise können Sie jetzt lokal entwickeln und debuggen, eine beliebige Node.js-Version nach 0.10 verwenden und eine Anpassung mit beliebiger Express.js-Middleware durchführen.

  - Für .NET-basierte Serverprojekte verfügen die neuen [Mobile Apps-SDK-NuGet-Pakete](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) über mehr Flexibilität für NuGet-Abhängigkeiten, die neuen App Service-Authentifizierungsfeatures werden unterstützt, und die Zusammenstellung mit ASP.NET-Projekten ist möglich, einschließlich MVC. Weitere Informationen zu Upgrades finden Sie unter [Aktualisieren des vorhandenen .NET Mobile Service auf App Service](app-service-mobile-net-upgrading-from-mobile-services.md).


##<a name="understand"></a>Grundlegendes zu mobilen App Service-Apps 

Mobile App Service-Apps sind eine neue Methode zum Erstellen mobiler Anwendungen mit Microsoft Azure. Weitere Einzelheiten zu mobilen Apps erhalten Sie im Thema [Was sind mobile Apps?].

Bei einer Migration in mobile Apps kann die gesamte Funktionalität der App (sowie der Code) beibehalten werden. Darüber hinaus stehen neue Funktionen für die Anwendung zur Verfügung. Im Mobile Apps-Modell wird Ihr Code tatsächlich in einer Web-App ausgeführt (der neuen Version von Azure-Websites). Sie haben vollständige Kontrolle über die Web-App und deren Funktionsweise. Zusätzlich können jetzt Funktionen der Web-App verwendet werden, die bisher für Mobile Services-Kunden nicht verfügbar waren. Dazu gehören beispielsweise Traffic Manager und Entwicklungsslots.


##Manuelle Migration für Mobile Services-.NET-Back-End

In diesem Abschnitt wird veranschaulicht, wie ein .NET-Mobile Services-Projekt in Azure App Service gehostet wird. Eine auf diese Weise gehostete App kann alle Tutorials zur *Mobile Services*-.NET-Laufzeit verwenden. Allerdings muss jede URL, die die Domäne „azure-mobile.net“ verwendet, durch die Domäne der App Service-Instanz ersetzt werden.

Ein Mobile Services-Projekt kann auch in einer [App Service-Umgebung] gehostet werden. Der gesamte Inhalt in diesem Thema gilt weiterhin, aber die Website besitzt das Format „contoso.contosoase.p.azurewebsites.net“ anstelle von „contoso.azurewebsites.net“.

>[AZURE.NOTE]Wenn Sie eine manuelle Migration durchführen, können Sie Ihre vorhandene **service.azure-mobile.net**-URL *nicht* beibehalten. Wenn Sie über mobile Clients verfügen, die eine Verbindung mit dieser URL herstellen, sollten Sie entweder die automatische Migrationsoption verwenden oder die Ausführung Ihres mobilen Diensts beibehalten, bis alle mobilen Clients das Upgrade auf die neue URL durchgeführt haben.


### <a name="app-settings"></a>Anwendungseinstellungen
Für Mobile Services müssen mehrere Anwendungseinstellungen in der Umgebung verfügbar sein, die in diesem Abschnitt beschrieben werden.

Um die Anwendungseinstellungen für Ihre Web-App einzurichten, melden Sie sich zunächst bei der [Vorschau des Azure-Verwaltungsportals] an. Navigieren Sie zur mobilen, Web- oder API-App, die Sie verwenden möchten, und wählen Sie „Einstellungen“ und dann „Anwendungseinstellungen“ aus. Scrollen Sie nach unten bis zum Abschnitt „App-Einstellungen“. Hier können Sie die erforderlichen Schlüssel-Wert-Paare festlegen.
 
**MS\_MobileServiceName** muss auf den Namen der App festgelegt werden. Wenn Sie beispielsweise eine App mit der URL „contoso.azurewebsites.net“ ausführen, lautet der richtige Wert „contoso“.
 
**MS\_MobileServiceDomainSuffix** muss auf den Namen der Web-App festgelegt werden. Wenn Sie beispielsweise eine App mit der URL „contoso.azurewebsites.net“ ausführen, lautet der richtige Wert „azurewebsites.net“.
 
**MS\_ApplicationKey** kann auf einen beliebigen Wert festgelegt werden. Derselbe Wert muss jedoch vom Client-SDK verwendet werden. Eine GUID wird empfohlen.
 
**MS\_MasterKey** kann auf einen beliebigen Wert festgelegt werden. Derselbe Wert muss aber von Admin-APIs/Clients verwendet werden. Eine GUID wird empfohlen.
 
Wenn Sie eine vorhandene Mobile Services-Anwendung verschieben, können Sie sowohl den Hauptschlüssel als auch den Anwendungsschlüssel von der Registerkarte „Konfigurieren“ im Abschnitt „Mobile Services“ des [Azure-Verwaltungsportals] abrufen. Wählen Sie im unteren Bereich die Aktion „Schlüssel verwalten“, und kopieren Sie die Schlüssel.


### <a name="client-sdk"></a>Vorgehensweise: Ändern des Client-SDK

Ändern Sie im Client-App-Projekt den Konstruktor des Mobile Services-Clientobjekts so, dass er die neue App-URL (z. B. `https://contoso.azurewebsites.net`) und den zuvor konfigurierten Anwendungsschlüssel akzeptiert. Die Client-SDK-Version muss eine **Mobile Services**-Version sein und sollte **nicht** per Upgrade aktualisiert werden. Verwenden Sie für iOS- und Android-Clients die Versionen 2.x und für Windows/Xamarin die Version 1.3.2. JavaScript-Clients sollten Version 1.2.7 verwenden.

### <a name="data"></a>Vorgehensweise: Aktivieren von Datenfeatures

Um in Mobile Services mit Entity Framework-Standardklassen zu arbeiten, sind zwei weitere App-Einstellungen erforderlich.
 
Verbindungszeichenfolgen werden auf dem Blatt „Anwendungseinstellungen“ im Abschnitt „Verbindungszeichenfolgen“ direkt unter dem Abschnitt **App-Einstellungen** gespeichert. Die Verbindungszeichenfolge für Ihre Datenbank muss unter dem Schlüssel **MS\_TableConnectionString** festgelegt werden. Zum Verschieben einer vorhandenen Mobile Services-Anwendung navigieren Sie im Mobile Services-Portal auf der Registerkarte „Konfigurieren“ zum Abschnitt „Verbindungszeichenfolgen“. Klicken Sie auf „Verbindungszeichenfolgen anzeigen“, und kopieren Sie den Wert.
 
Standardmäßig lautet das zu verwendende Schema **MS\_MobileServiceName**. Dies kann aber durch die Einstellung **MS\_TableSchema** überschrieben werden. Legen Sie unter **App-Einstellungen** **MS\_TableSchema** als Namen des zu verwendenden Schemas fest. Wenn Sie eine vorhandene Mobile Services-Anwendung verschieben, wurde mit Entity Framework bereits ein Schema erstellt – dies ist der Name der Mobile Services-Instanz, nicht der der App Service-Instanz, die den Code nun hosten wird.

### <a name="push"></a>Vorgehensweise: Aktivieren von Pushfeatures

Damit Pushfeatures funktionieren, benötigt die Web-App außerdem Informationen zum Notification Hub.
 
Legen Sie unter „Verbindungszeichenfolgen“ **MS\_NotificationHubConnectionString** mit der Verbindungszeichenfolge „DefaultFullSharedAccessSignature“ für den Notification Hub fest. Zum Verschieben einer vorhandenen Mobile Services-Anwendung navigieren Sie im Mobile Services-Portal auf der Registerkarte „Konfigurieren“ zum Abschnitt „Verbindungszeichenfolgen“. Klicken Sie auf „Verbindungszeichenfolgen anzeigen“, und kopieren Sie den Wert.

Die App-Einstellung **MS\_NotificationHubName** muss auf den Namen des Hubs festgelegt werden. Beim Verschieben einer vorhandenen Mobile Services-App erhalten Sie diesen Wert von der Registerkarte „Push“ im Mobile Services-Portal. Die anderen Felder auf dieser Registerkarte werden mit dem Hub selbst verknüpft und müssen nicht kopiert werden.
 
### <a name="auth"></a>Vorgehensweise: Aktivieren von Authentifizierungsfeatures

Auch die Identitätsfeatures bringen Anforderungen an die App-Einstellungen für die einzelnen Anbieter mit sich. Wenn Sie eine vorhandene Mobile Services-App umstellen, besitzt jedes der Felder auf der Registerkarte „Identität“ des Mobile Services-Portals eine entsprechende App-Einstellung.
 
Microsoft Account

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**
 
Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**
 
Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**
 
Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**
 
AAD

* **MS\_AadClientID**

* **MS\_AadTenants** – Hinweis: **MS\_AadTenants** wird als eine durch Trennzeichen getrennte Liste der Mandantendomänen gespeichert (die Felder „Zulässige Mandanten“ im Mobile Services-Portal).

### <a name="publish"></a>Vorgehensweise: Veröffentlichen des Mobile Services-Projekts

1. Navigieren Sie im Vorschauportal zu Ihrer App, und wählen Sie in der Befehlsleiste „Veröffentlichungsprofil abrufen“. Speichern Sie das heruntergeladene Profil auf dem lokalen Computer.
2. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Mobile Services-Serverprojekt, und wählen Sie „Veröffentlichen“. Wählen Sie auf der Profilregisterkarte „Importieren“ aus, und wechseln Sie zum heruntergeladenen Profil.
3. Klicken Sie auf „Veröffentlichen“, um den Code für App Service bereitzustellen.

Protokolle werden über die Standardprotokollfeatures von App Service verarbeitet. Weitere Informationen zur Protokollierung finden Sie unter [Aktivieren der Diagnoseprotokollierung in Azure App Service].



<!-- URLs. -->

[Vorschau des Azure-Verwaltungsportals]: https://portal.azure.com/
[Azure-Verwaltungsportals]: https://manage.windowsazure.com/
[Was sind mobile Apps?]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /de-DE/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /de-DE/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Aktivieren der Diagnoseprotokollierung in Azure App Service]: web-sites-enable-diagnostic-log.md
[App Service-Preisen]: https://azure.microsoft.com/de-DE/pricing/details/app-service/
[App Service-Umgebung]: app-service-app-service-environment-intro.md
[Mobile Services im Vergleich zu App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[Migration eines mobilen Diensts in eine mobile App in App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO3-->