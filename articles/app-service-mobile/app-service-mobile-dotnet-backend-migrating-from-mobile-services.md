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
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# Migrieren des vorhandenen Azure Mobile Service zu App Service

In diesem Thema erfahren Sie, wie Sie eine vorhandene Anwendung aus Azure Mobile Services in eine neue mobile App Service-App migrieren können. Alle vorhandenen mobilen Dienste können auf einfache Weise in ein neues Mobile App-Back-End migriert werden. Während der Migration kann der vorhandene mobile Dienst weiterhin verwendet werden.

Wenn ein mobiler Dienst zu App Service migriert wird, hat er Zugriff auf alle App Service-Features, und die Abrechnung erfolgt gemäß [App Service-Preisen], nicht gemäß den Mobile Services-Preisen.

Außerdem ist zu beachten, dass alle geplanten Aufträge aus einem von Microsoft verwalteten Azure Scheduler-Plan in einen Azure Scheduler-Plan unter Ihrem eigenen Abonnement verschoben werden, den Sie selbst verwalten können. Ähnlich wie bei den Vorteilen der Migration zu App Service profitieren Sie auch hier von der vollen Leistung von Azure Scheduler.

### <a name="why-host"></a>Gründe für das Hosten in App Service

App Service stellt eine Hostingumgebung mit umfangreicheren Features für Ihre Anwendung bereit. Durch das Hosten in App Service erhält Ihr Dienst Zugriff auf Stagingslots, benutzerdefinierte Domänen, Traffic Manager-Unterstützung und vieles mehr. Sie können einen mobilen Dienst zwar auf ein Mobile App-Back-End aktualisieren, aber nachdem Sie zu App Service migriert sind, möchten einige Kunden diese Features möglicherweise sofort nutzen, ohne direkt das SDK zu aktualisieren.

Weitere Informationen zu den Vorteilen von App Service finden Sie unter [Mobile Services im Vergleich zu App Service].

## Vergleich von Migration und Upgrade

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- Für Node.js-basierte Serverprojekte verfügt das neue [Mobile Apps-Node.js-SDK](https://github.com/Azure/azure-mobile-apps-node) über eine Reihe neuer Features. Beispielsweise können Sie jetzt lokal entwickeln und debuggen, eine beliebige Node.js-Version nach 0.10 verwenden und eine Anpassung mit beliebiger Express.js-Middleware durchführen.

- Für .NET-basierte Serverprojekte verfügen die neuen [Mobile Apps-SDK-NuGet-Pakete](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) über mehr Flexibilität für NuGet-Abhängigkeiten, die neuen App Service-Authentifizierungsfeatures werden unterstützt, und die Zusammenstellung mit ASP.NET-Projekten ist möglich, einschließlich MVC. Weitere Informationen zu Upgrades finden Sie unter [Aktualisieren des vorhandenen .NET Mobile Service auf App Service](app-service-mobile-net-upgrading-from-mobile-services.md).


## <a name="understand"></a>Grundlegendes zu mobilen App Service-Apps

Mobile Apps in App Service ist eine neue Methode zum Erstellen mobiler Anwendungen mithilfe von Azure. Weitere Informationen zu Mobile Apps finden Sie unter [Was sind mobile Apps?].

Bei der Migration zu Mobile Apps können alle vorhandenen App-Funktionen, einschließlich der Geschäftslogik, beibehalten werden. Darüber hinaus stehen neue Funktionen für die Anwendung zur Verfügung. Im Mobile Apps-Modell wird Ihr Code tatsächlich in einer Web-App in App Service (der neuen Version von Azure-Websites) ausgeführt. Sie haben vollständige Kontrolle über die Web-App und deren Funktionsweise. Zusätzlich können jetzt Funktionen der Web-App verwendet werden, die bisher für Mobile Services-Kunden nicht verfügbar waren. Dazu gehören beispielsweise Traffic Manager und Entwicklungsslots.

Die wesentliche Einschränkung für das Upgraden besteht darin, dass die in Mobile Services geplanten Aufträge nicht integriert werden und dass entweder der Azure Scheduler zum Aufrufen benutzerdefinierter APIs eingerichtet werden muss oder die Webaufträgefeatures aktiviert werden müssen.

## Migrieren des mobilen Diensts mit dem Assistenten für die Migration zu App Service

Die einfachste und empfohlene Methode der Migration Ihres mobilen Diensts zu App Service ist die Verwendung des Assistenten für die Migration zu App Service, der im klassischen Azure-Portal verfügbar ist. Navigieren Sie zum [klassischen Azure-Portal], durchsuchen Sie Ihre mobilen Dienste, und wählen Sie einen mobilen Dienst aus, den Sie migrieren möchten. Am unteren Rand des Bildschirms wird die Schaltfläche **Zu App Service migrieren** angezeigt, die Sie durch den Prozess der Migration Ihres mobilen Diensts führt.

### <a name="what-gets-migrated"></a>Was wird migriert?

Der Migrations-Assistent wechselt für die Serverfarmen, die Ihren mobilen Dienst hosten, die Verwaltung von Mobile Services zu App Service. Er verschiebt außerdem alle von Mobile Services verwalteten Scheduler-Pläne von einem durch Microsoft verwalteten Abonnement in Ihr eigenes Abonnement. Bei der Übertragung der Serverfarmen in die Kontrolle der App Service-Verwaltung werden alle mobilen Dienste, die dieser Serverfarm zugeordnet waren, gleichzeitig verschoben.

Mobile Services organisiert Ihre mobilen Dienste in Serverfarmen basierend auf Region und SKU (Free, Basic, Standard). Alle Ihre kostenlosen Dienste (Free) in einer Region befinden sich in der gleichen Serverfarm und werden zusammen migriert. Eine gewisse Anzahl von Basisdiensten (Basic) in einer Region werden zusammen auf der gleichen Farm gehostet, aber wenn Sie über eine große Anzahl von Basisdiensten verfügen, werden diese in separaten Plänen verwaltet. Jeder Standarddienst (Standard) befindet sich in einer eigenen Serverfarm. Wenn Sie jemals einen einzelnen Dienst migrieren möchten, oder wenn Sie vermeiden möchten, dass ein bestimmter Dienst mit Ihren anderen Websites verschoben wird, können Sie ihn auf „Basic“ aktualisieren, um ihn in einem eigenen unabhängigen Plan zu verwalten.

Außerdem werden Azure Scheduler-Pläne zu Ihrem Abonnement hinzugefügt, wenn Sie geplante Aufträge verwenden. Pro Abonnement ist eine begrenzte Anzahl von kostenlosen geplanten Aufträgen zulässig, daher können möglicherweise Kosten anfallen, die Sie nach dem Abonnement überprüfen sollten.

###  Verwenden des Migrations-Assistenten

1. Navigieren Sie zum [klassischen Azure-Portal], und klicken Sie auf „Mobile Services“. 

2. Wählen Sie den mobilen Dienst aus, den Sie migrieren möchten, und klicken Sie unten in der Menüleiste auf „Zu App Service migrieren“. Dadurch wird ein Popup mit der Liste der mobilen Dienste angezeigt, die migriert werden. Details dazu, welche Dienste angezeigt werden und warum sie angezeigt werden, finden Sie im [vorherigen Abschnitt](#what-gets-migrated).

3. Geben Sie den Namen des mobilen Diensts ein, um die Migration zu bestätigen, und klicken Sie auf das Häkchen, um den Vorgang fortzusetzen. Wenn die Migration mehrere mobile Dienste umfasst, müssen Sie dennoch den Namen des ursprünglich ausgewählten Diensts eingeben.

4. Nach dem Start der Migration können Sie ihren aktuellen Status in der Liste der mobilen Dienste im [klassischen Azure-Portal] anzeigen. Für alle Dienste, die aktuell migriert werden, wird „Migration wird ausgeführt“ angezeigt. Nachdem alle Migrationen abgeschlossen sind, können Sie sie im [Azure-Portal] unter Mobile Apps anzeigen. Während und nach der Migration können Ihre mobilen Dienste weiterhin verwendet werden, und die URL wird nicht geändert.

## Manuelle Migration für Mobile Services-.NET-Back-End

>[AZURE.NOTE]Beachten Sie, dass die empfohlene Methode zum Migrieren eines mobilen Diensts der im vorherigen Abschnitt beschriebene Assistenten für die Migration zu App Service ist. Die manuelle Migration sollte nur in Situationen verwendet werden, in denen der Assistent nicht verwendet werden kann.

In diesem Abschnitt wird veranschaulicht, wie ein .NET-Mobile Services-Projekt in Azure App Service gehostet wird. Eine auf diese Weise gehostete App kann alle Tutorials zur *Mobile Services*-.NET-Laufzeit verwenden. Allerdings muss jede URL, die die Domäne „azure-mobile.net“ verwendet, durch die Domäne der App Service-Instanz ersetzt werden.

Ein Mobile Services-Projekt kann auch in einer [App Service-Umgebung] gehostet werden. Der gesamte Inhalt in diesem Thema gilt weiterhin, aber Sie Website weist das Format `contoso.contosoase.p.azurewebsites.net` anstelle von `contoso.azurewebsites.net` auf.

>[AZURE.NOTE]Wenn Sie eine manuelle Migration durchführen, können Sie Ihre vorhandene **service.azure-mobile.net**-URL *nicht* beibehalten. Wenn Sie über mobile Clients verfügen, die eine Verbindung mit dieser URL herstellen, sollten Sie entweder die automatische Migrationsoption verwenden oder die Ausführung Ihres mobilen Diensts beibehalten, bis alle mobilen Clients das Upgrade auf die neue URL durchgeführt haben.


### <a name="app-settings"></a>Anwendungseinstellungen
Für Mobile Services müssen mehrere Anwendungseinstellungen in der Umgebung verfügbar sein, die in diesem Abschnitt beschrieben werden.

Um die Anwendungseinstellungen für Ihr Mobile App-Back-End einzurichten, melden Sie sich zunächst beim [Azure-Portal] an. Navigieren Sie zur App Service-App, die Sie als Mobile App-Back-End verwenden möchten, klicken Sie auf **Einstellungen** > **Anwendungseinstellungen**, und führen Sie einen Bildlauf nach unten zu **App-Einstellungen** aus. Hier können Sie die folgenden erforderlichen Schlüssel-Wert-Paare festlegen:

+ **MS\_MobileServiceName** muss auf den Namen der App festgelegt werden. Wenn die URL des Back-Ends z. B. `contoso.azurewebsites.net` lautet, dann ist *contoso* der richtige Wert.

+ **MS\_MobileServiceDomainSuffix** muss auf den Namen der Web-App festgelegt werden. Wenn die URL des Back-Ends z. B. `contoso.azurewebsites.net` lautet, dann ist *azurewebsites.net* der richtige Wert.

+ **MS\_ApplicationKey** kann auf einen beliebigen Wert festgelegt werden. Derselbe Wert muss jedoch vom Client-SDK verwendet werden. Eine GUID wird empfohlen.

+ **MS\_MasterKey** kann auf einen beliebigen Wert festgelegt werden. Derselbe Wert muss aber von Admin-APIs/Clients verwendet werden. Eine GUID wird empfohlen.

Wenn Sie einen mobilen Dienst migrieren, können Sie sowohl den Hauptschlüssel als auch den Anwendungsschlüssel von der Registerkarte **Konfigurieren** im Abschnitt „Mobile Services“ des [klassischen Azure-Portals] abrufen. Klicken Sie unten auf **Schlüssel verwalten**, und kopieren Sie die Schlüssel.


### <a name="client-sdk"></a>Vorgehensweise: Ändern des Client-SDK

Ändern Sie im Client-App-Projekt den Konstruktor des Mobile Services-Clientobjekts so, dass er die neue App-URL (z. B. `https://contoso.azurewebsites.net`) und den zuvor konfigurierten Anwendungsschlüssel akzeptiert. Die Client-SDK-Version muss eine **Mobile Services**-Version sein und sollte **nicht** per Upgrade aktualisiert werden. Verwenden Sie für iOS- und Android-Clients die Versionen 2.x und für Windows/Xamarin die Version 1.3.2. JavaScript-Clients sollten Version 1.2.7 verwenden.

### <a name="data"></a>Vorgehensweise: Aktivieren von Datenfeatures

Um in Mobile Services mit Entity Framework-Standardklassen zu arbeiten, sind zwei weitere Einstellungen erforderlich.

Verbindungszeichenfolgen werden auf dem Blatt „Anwendungseinstellungen“ im Abschnitt **Verbindungszeichenfolgen** direkt unter dem Abschnitt **App-Einstellungen** gespeichert. Die Verbindungszeichenfolge für Ihre Datenbank muss unter dem Schlüssel **MS\_TableConnectionString** festgelegt werden. Wenn Sie einen vorhandenen mobilen Dienst zu App Service migrieren, navigieren Sie zum Abschnitt **Verbindungszeichenfolgen** der Registerkarte **Konfigurieren** des mobilen Diensts im [klassischen Azure-Portal](https://manage.windowsazure.com/). Klicken Sie auf **Verbindungszeichenfolgen anzeigen**, und kopieren Sie den Wert.

Standardmäßig lautet das zu verwendende Schema **MS\_MobileServiceName**, dies kann jedoch durch die Einstellung **MS\_TableSchema** überschrieben werden. Legen Sie unter **App-Einstellungen** **MS\_TableSchema** als Namen des zu verwendenden Schemas fest. Wenn Sie eine vorhandene Mobile Services-Anwendung verschieben, wurde mit Entity Framework bereits ein Schema erstellt – dies ist der Name des mobilen Diensts, nicht der der App Service-Instanz, die den Code nun hosten wird.

### <a name="push"></a>Vorgehensweise: Aktivieren von Pushfeatures

Damit Pushfeatures funktionieren, benötigt die Web-App außerdem Informationen zum Notification Hub.

Legen Sie unter **Verbindungszeichenfolgen** **MS\_NotificationHubConnectionString** mit der Verbindungszeichenfolge „DefaultFullSharedAccessSignature“ des Notification Hubs fest. Um einen vorhandenen mobilen Dienst zu migrieren, navigieren Sie zum Abschnitt **Verbindungszeichenfolgen** der Registerkarte **Konfigurieren** des mobilen Diensts im [klassischen Azure-Portal](https://manage.windowsazure.com/). Klicken Sie auf **Verbindungszeichenfolgen anzeigen**, und kopieren Sie den Wert.

Die App-Einstellung **MS\_NotificationHubName** muss auf den Namen des Hubs festgelegt werden. Beim Verschieben eines vorhandenen mobilen Diensts erhalten Sie diesen Wert von der Registerkarte **Push** des mobilen Diensts im [klassischen Azure-Portal](https://manage.windowsazure.com/). Die anderen Felder auf dieser Registerkarte werden mit dem Hub selbst verknüpft und müssen nicht kopiert werden.

### <a name="auth"></a>Vorgehensweise: Aktivieren von Authentifizierungsfeatures

Auch die Identitätsfeatures bringen Anforderungen an die App-Einstellungen für die einzelnen Anbieter mit sich. Wenn Sie einen vorhandenen mobilen Dienst umstellen, besitzt jedes der Felder auf der Registerkarte **Identität** im klassischen Azure-Portal eine entsprechende App-Einstellung.

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

* **MS\_AadTenants** – Hinweis: **MS\_AadTenants** wird als eine durch Trennzeichen getrennte Liste der Mandantendomänen gespeichert (die Felder **Zulässige Mandanten** im klassischen Azure-Portal).

### <a name="publish"></a>Vorgehensweise: Veröffentlichen des Mobile Services-Projekts

1. Navigieren Sie im [Azure-Portal] zu Ihrer App, klicken Sie in der Befehlsleiste auf **Veröffentlichungsprofil abrufen**, und speichern Sie das heruntergeladene Profil auf dem lokalen Computer.
2. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Mobile Services-Serverprojekt und dann auf **Veröffentlichen**. 
3. Wählen Sie auf der Profilregisterkarte **Importieren** aus, und wechseln Sie zum heruntergeladenen Profil.
3. Klicken Sie auf **Veröffentlichen**, um den Code für App Service bereitzustellen.

Protokolle werden über die Standardprotokollfeatures von App Service verarbeitet. Weitere Informationen zur Protokollierung finden Sie unter [Aktivieren der Diagnoseprotokollierung in Azure App Service].



<!-- URLs. -->

[Azure-Portal]: https://portal.azure.com/
[klassischen Azure-Portal]: https://manage.windowsazure.com/
[klassischen Azure-Portals]: https://manage.windowsazure.com/
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
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1203_2015-->