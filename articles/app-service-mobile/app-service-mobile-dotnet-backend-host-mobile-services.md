<properties
	pageTitle="Hosten eines Mobile Services-Projekts in App Service | Microsoft Azure"
	description="Erfahren Sie, wie ein Mobile Services-Projekt in App Service ausgeführt wird."
	documentationCenter=""
	authors="mattchenderson"
	manager="dwrede"
	editor="na"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="get-started-article"
	ms.date="10/08/2015"
	ms.author="mahender"/>

# Hosten eines .NET-Mobile Services-Projekts in App Service

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird veranschaulicht, wie ein .NET-Mobile Services-Projekt in Azure App Service gehostet. Apps, die auf diese Weise ausgeführt werden, erhalten Zugriff auf alle App Service-Features und werden gemäß der [App Service-Preisgebung] und nicht anhand der Mobile Services-Preisgebung in Rechnung gestellt.

Eine auf diese Weise gehostete App kann alle Tutorials zur Mobile Services-.NET-Laufzeit verwenden. Allerdings muss jede URL, die die Domäne „azure-mobile.net“ verwendet, durch die Domäne der App Service-Instanz ersetzt werden.

Ein Mobile Services-Projekt kann auch in einer [App Service-Umgebung] gehostet werden. Der gesamte Inhalt in diesem Thema gilt weiterhin, aber die Website besitzt das Format „contoso.contosoase.p.azurewebsites.net“ anstelle von „contoso.azurewebsites.net“.

## <a name="app-settings"></a>Gründe für das Hosten in App Service

App Service stellt eine Hostingumgebung mit umfangreicheren Features für Ihre Anwendung bereit. Durch das Hosten in App Service erhält Ihr Dienst Zugriff auf Stagingslots, benutzerdefinierte Domänen, Traffic Manager-Unterstützung und vieles mehr. Auch wenn die [Migration eines mobilen Diensts in eine mobile App in App Service] möglich ist, möchten einige Kunden diese Features ggf. sofort nutzen, ohne direkt das SDK zu aktualisieren.

Die wesentliche Einschränkung für das Hosten in App Service besteht darin, dass die in Mobile Services geplanten Aufträge nicht integriert werden und dass entweder der Azure Scheduler für benutzerdefinierte APIs eingerichtet werden muss oder die Webaufträgefeatures aktiviert werden müssen.

Weitere Informationen zu den Vorteilen von App Service finden Sie im Thema [Mobile Services im Vergleich zu App Service].

## <a name="app-settings"></a>Anwendungseinstellungen
Für Mobile Services müssen mehrere Anwendungseinstellungen in der Umgebung verfügbar sein. In diesem Abschnitt wird das Hinzufügen der einzelnen Einstellungen schrittweise erläutert. Um die Anwendungseinstellungen für Ihre Web-App einzurichten, melden Sie sich zunächst bei der [Vorschau des Azure-Verwaltungsportals] an. Navigieren Sie zur mobilen, Web- oder API-App, die Sie verwenden möchten, und wählen Sie „Einstellungen“ und dann „Anwendungseinstellungen“ aus. Scrollen Sie nach unten bis zum Abschnitt „App-Einstellungen“. Hier können Sie die erforderlichen Schlüssel-Wert-Paare festlegen.
 
**MS\_MobileServiceName** muss auf den Namen der App festgelegt werden. Wenn Sie beispielsweise eine App mit der URL „contoso.azurewebsites.net“ ausführen, lautet der richtige Wert „contoso“.
 
**MS\_MobileServiceDomainSuffix** muss auf den Namen der Web-App festgelegt werden. Wenn Sie beispielsweise eine App mit der URL „contoso.azurewebsites.net“ ausführen, lautet der richtige Wert „azurewebsites.net“.
 
**MS\_ApplicationKey** kann auf einen beliebigen Wert festgelegt werden. Derselbe Wert muss jedoch vom Client-SDK verwendet werden. Eine GUID wird empfohlen.
 
**MS\_MasterKey** kann auf einen beliebigen Wert festgelegt werden. Derselbe Wert muss jedoch von Admin-APIs/Clients verwendet werden. Eine GUID wird empfohlen.
 
Wenn Sie eine vorhandene Mobile Services-Anwendung verschieben, können Sie sowohl den Hauptschlüssel als auch den Anwendungsschlüssel von der Registerkarte „Konfigurieren“ im Abschnitt „Mobile Services“ des [Azure-Verwaltungsportals] abrufen. Wählen Sie im unteren Bereich die Aktion „Schlüssel verwalten“, und kopieren Sie die Schlüssel.


## <a name="client-sdk"></a>Vorgehensweise: Ändern des Client-SDK

Ändern Sie im Client-App-Projekt den Konstruktor des Mobile Services-Clientobjekts so, dass er die App-URL (z. B. contoso.azurewebsites.net) und den zuvor konfigurierten Anwendungsschlüssel akzeptiert. Die Client-SDK-Version muss eine Mobile Services-Version sein und sollte **nicht** per Upgrade aktualisiert werden. Verwenden Sie für iOS- und Android-Clients die Versionen 2.x und für Windows/Xamarin die Version 1.3.2. JavaScript-Clients sollten Version 1.2.7 verwenden.

## <a name="data"></a>Vorgehensweise: Aktivieren von Datenfeatures

Um in Mobile Services mit Entity Framework-Standardklassen zu arbeiten, sind zwei weitere App-Einstellungen erforderlich.
 
Verbindungszeichenfolgen werden auf dem Blatt „Anwendungseinstellungen“ im Abschnitt „Verbindungszeichenfolgen“ direkt unter dem Abschnitt „App-Einstellungen“ gespeichert. Die Verbindungszeichenfolge für Ihre Datenbank muss unter dem Schlüssel **MS\_TableConnectionString** festgelegt werden. Zum Verschieben einer vorhandenen Mobile Services-Anwendung navigieren Sie im Mobile Services-Portal auf der Registerkarte „Konfigurieren“ zum Abschnitt „Verbindungszeichenfolgen“. Klicken Sie auf „Verbindungszeichenfolgen anzeigen“, und kopieren Sie den Wert.
 
Standardmäßig lautet das zu verwendende Schema **MS\_MobileServiceName**, dies kann jedoch durch die Einstellung **MS\_TableSchema** überschrieben werden. Legen Sie unter „App-Einstellungen“ **MS\_TableSchema** als Namen des zu verwendenden Schemas fest. Wenn Sie eine vorhandene Mobile Services-Anwendung verschieben, wurde mit Entity Framework bereits ein Schema erstellt – dies ist der Name der Mobile Services-Instanz, nicht der der App Service-Instanz, die den Code nun hosten wird.

## <a name="push"></a>Vorgehensweise: Aktivieren von Pushfeatures

Damit Pushfeatures funktionieren, benötigt die Web-App außerdem Informationen zum Notification Hub.
 
Legen Sie unter „Verbindungszeichenfolgen“ **MS\_NotificationHubConnectionString** mit der Verbindungszeichenfolge „DefaultFullSharedAccessSignature“ für den Notification Hub fest. Zum Verschieben einer vorhandenen Mobile Services-Anwendung navigieren Sie im Mobile Services-Portal auf der Registerkarte „Konfigurieren“ zum Abschnitt „Verbindungszeichenfolgen“. Klicken Sie auf „Verbindungszeichenfolgen anzeigen“, und kopieren Sie den Wert.

Die App-Einstellung **MS\_NotificationHubName** muss auf den Namen des Hubs festgelegt werden. Beim Verschieben einer vorhandenen Mobile Services-App erhalten Sie diesen Wert von der Registerkarte „Push“ im Mobile Services-Portal. Die anderen Felder auf dieser Registerkarte werden mit dem Hub selbst verknüpft und müssen nicht kopiert werden.
 
## <a name="auth"></a>Vorgehensweise: Aktivieren von Authentifizierungsfeatures

Auch die Identitätsfeatures bringen Anforderungen an die App-Einstellungen für die einzelnen Anbieter mit sich. Wenn Sie eine vorhandene Mobile Services-App umstellen, besitzt jedes der Felder auf der Registerkarte „Identität“ des Mobile Services-Portals eine entsprechende App-Einstellung.
 
Microsoft-Konto: * **MS\_MicrosoftClientID** * **MS\_MicrosoftClientSecret** * **MS\_MicrosoftPackageSID**
 
Facebook: * **MS\_FacebookAppID** * **MS\_FacebookAppSecret**
 
Twitter * **MS\_TwitterConsumerKey** * **MS\_TwitterConsumerSecret**
 
Google * **MS\_GoogleClientID** * **MS\_GoogleClientSecret**
 
AAD * **MS\_AadClientID** * **MS\_AadTenants** – Hinweis: **MS\_AadTenants** wird als eine durch Trennzeichen getrennte Liste der Mandantendomänen gespeichert (die Felder „Zulässige Mandanten“ im Mobile Services-Portal).

## <a name="publish"></a>Vorgehensweise: Veröffentlichen des Mobile Services-Projekts

1. Navigieren Sie im Vorschauportal zu Ihrer App, und wählen Sie in der Befehlsleiste „Veröffentlichungsprofil abrufen“. Speichern Sie das heruntergeladene Profil auf dem lokalen Computer.
2. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Mobile Services-Serverprojekt, und wählen Sie „Veröffentlichen“. Wählen Sie auf der Profilregisterkarte „Importieren“ aus, und wechseln Sie zum heruntergeladenen Profil.
3. Klicken Sie auf „Veröffentlichen“, um den Code für App Service bereitzustellen.

Protokolle werden über die Standardprotokollfeatures von App Service verarbeitet. Weitere Informationen zur Protokollierung finden Sie unter [Aktivieren der Diagnoseprotokollierung in Azure App Service].

<!-- URLs. -->

[Vorschau des Azure-Verwaltungsportals]: https://portal.azure.com/
[Azure-Verwaltungsportals]: https://manage.windowsazure.com/
[Aktivieren der Diagnoseprotokollierung in Azure App Service]: web-sites-enable-diagnostic-log.md
[App Service-Preisgebung]: https://azure.microsoft.com/de-DE/pricing/details/app-service/
[App Service-Umgebung]: app-service-app-service-environment-intro.md
[Mobile Services im Vergleich zu App Service]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[Migration eines mobilen Diensts in eine mobile App in App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO1-->