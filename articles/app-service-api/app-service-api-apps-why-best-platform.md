<properties 
	pageTitle="API-Apps-Übersicht" 
	description="Erfahren Sie, warum Azure App Service die beste Plattform zum Entwickeln, Veröffentlichen und Hosten von RESTful-APIs ist." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/30/2015" 
	ms.author="tdykstra"/>

# API-Apps-Übersicht

API-Apps ist einer der vier App-Typen, die von [Azure App Service](../app-service/app-service-value-prop-what-is.md) angeboten werden.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

[App Service](../app-service/app-service-value-prop-what-is.md) ist eine vollständig verwaltete Plattform, die einen umfangreichen Satz von Funktionen für Web-, mobile und Integrationsszenarios bereitstellt. API-Apps in App Service bietet Funktionen, mit denen APIs in der Cloud und lokal einfacher erstellt, gehostet und genutzt werden können. Stellen Sie Ihre API als API-App in App Service bereit, und profitieren Sie von einer für Unternehmen geeigneten Sicherheit, einfacher Zugriffssteuerung, hybrider Konnektivität, automatischer SDK-Generierung und nahtloser Integration in [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Warum API-Apps?

API-Apps stellt die folgenden Funktionen bereit:

- **Einfache Nutzung** – Die integrierte Unterstützung für [Swagger-API-Metadaten](#concepts) ermöglicht es, dass Ihre APIs auf einfache Weise von zahlreichen Clients genutzt werden können. Clientcode für Ihre APIs kann in zahlreichen verschiedenen Programmiersprachen generiert werden, darunter C#, Java und Javascript. Sie können ganz einfach [CORS](#concepts) konfigurieren, ohne den Code zu ändern. Weitere Informationen finden Sie unter [Metadaten in App Service-API-Apps für die API-Ermittlung und Codegenerierung](app-service-api-metadata.md) und [Nutzen einer API-App aus JavaScript mit CORS](app-service-api-cors-consume-javascript.md). 

- **Einfache Zugriffssteuerung** – Sie können eine API-App vor nicht authentifiziertem Zugriff schützen, ohne Änderungen an Ihrem Code vornehmen zu müssen. Integrierte Authentifizierungsdienste sichern APIs für den Zugriff durch andere Dienste oder Clients, die Benutzer repräsentieren. Zu den unterstützten Identitätsanbietern zählen Azure Active Directory und Drittanbieterdienste wie Facebook und Twitter. Clients können die Active Directory Authentication Library (ADAL) oder das Mobile Apps-SDK verwenden. Weitere Informationen finden Sie unter [Expanding App Service authentication / authorization](/blog/announcing-app-service-authentication-authorization/) (in englischer Sprache) und [App Service-API-Apps – Änderungen](app-service-api-whats-changed.md).

- **Visual Studio-Integration** – Dedizierte Tools in Visual Studio optimieren das Erstellen, Bereitstellen, Nutzen, Debuggen und Verwalten von API-Apps. Weitere Informationen finden Sie unter [Announcing the Azure SDK 2.8.1 for .NET](/blog/announcing-azure-sdk-2-8-1-for-net/) (in englischer Sprache).

- **Integration in Logik-Apps** – Von Ihnen erstellte API-Apps können von [App Service-Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) genutzt werden. Wie, das erfahren Sie unter [Verwenden der in App Service gehosteten benutzerdefinierten API mit Logik-Apps](../app-service-logic/app-service-logic-custom-hosted-api.md). Informationen zu aktuellen Änderungen im Hinblick auf die Integration von API-Apps in Logik-Apps finden Sie unter [App Service-API-Apps – Änderungen](app-service-api-whats-changed.md).

- **Nutzen Sie eigene APIs in unveränderter Form** – Es ist nicht erforderlich, den Code vorhandener APIs zu ändern, um all die API-App-Features zu nutzen – stellen Sie lediglich Ihren Code in einer API-App bereit. Ihre API kann alle von App Service unterstützten Sprachen oder Frameworks verwenden, einschließlich ASP.NET und C#, Java, PHP, Node.js und Python.

Darüber hinaus sind die mit API-Apps, Web-Apps und Mobile Apps angebotenen Funktionen austauschbar. Dies bedeutet, dass in einer Instanz von API-Apps Funktionen zum Entwickeln und Hosten von Web- und mobilen Apps genutzt werden können, die mit Web-Apps und Mobile Apps bereitgestellt werden. Dies gilt auch umgekehrt: Sie können beispielsweise eine API mithilfe einer Web-App hosten und dennoch die Swagger-Metadaten zur Generierung des Clientcodes und CORS für den domänenübergreifenden Browserzugriff verwenden. Weitere Informationen finden Sie unter [Web-Apps – Übersicht](../app-service-web/app-service-web-overview.md) und [Mobile Apps – Übersicht](../app-service-mobile/app-service-mobile-value-prop.md).

>[AZURE.NOTE]Mit [Azure API Management](../api-management/api-management-key-concepts.md) können Sie den Clientzugriff auf APIs steuern, die von App Service-API-Apps gehostet werden. API-Apps bietet zwar Authentifizierungsdienste, jedoch nicht andere Features zur Zugriffsverwaltung, die API Management bietet, z. B. Endpunktkonsolidierung und Drosselung.

## API-Apps – Konzepte ##

- **Swagger**: – Ein Framework für die Dokumentation und Erkennung einer RESTful-API, wird in API-Apps standardmäßig verwendet. Weitere Informationen finden Sie unter [http://swagger.io/](http://swagger.io/).
- **Cross Origin Resource Sharing (CORS)**: Ein Mechanismus, der die JavaScript-Ausführung in einem Browser ermöglicht, damit eine API aufgerufen werden kann, die auf einer anderen Domäne gehostet wird als die, von der die Webseite geladen wurde. Weitere Informationen finden Sie unter [Nutzen einer API-App aus JavaScript mit CORS](app-service-api-cors-consume-javascript.md). 
- **Trigger** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) aufgerufen werden kann, um einen Workflowprozess zu initiieren, wenn eine bestimmte Bedingung erfüllt ist. Beispielsweise kann eine API-App eine Methode bereitstellen, die von der Logik-App regelmäßig aufgerufen wird, um in einem Twitter-Feed nach einem bestimmten Schlagwort zu suchen. Weitere Informationen finden Sie unter [API-App-Trigger](app-service-api-dotnet-triggers.md).
- **Aktion** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) zum Verarbeiten von Daten aufgerufen werden kann, nachdem ein Workflow durch einen Trigger gestartet wurde. Beispielsweise könnte eine API-App eine Methode bereitstellen, die die Logik-App als Reaktion auf einen Tweet aufruft, der über den Twitter-Trigger gefunden wurde. Aktionen sind API-Methoden, die durch eine Swagger-API-Definition verfügbar gemacht werden.

## Erste Schritte

Arbeiten Sie zum Einstieg in API-Apps das Tutorial [Erste Schritte mit API-Apps ](app-service-api-dotnet-get-started.md) durch.

Eine Liste der bekannten Probleme mit API-Apps finden Sie in dem entsprechenden [Forumsbeitrag](https://social.msdn.microsoft.com/Forums/de-DE/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](../app-service/app-service-value-prop-what-is.md).

<!---HONumber=AcomDC_1210_2015-->