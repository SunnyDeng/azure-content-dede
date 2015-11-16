<properties 
	pageTitle="API-Apps-Übersicht" 
	description="Erfahren Sie, warum Azure App Service die beste Plattform zum Entwickeln, Veröffentlichen und Hosten von RESTful-APIs ist." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/15/2015" 
	ms.author="tdykstra"/>

# API-Apps-Übersicht

API-Apps ist einer der vier App-Typen, die von [Azure App Service](../app-service/app-service-value-prop-what-is.md) angeboten werden.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

API-Apps bieten eine leistungsfähige Plattform zum Erstellen, Hosten und Nutzen von APIs in der Cloud und in lokalen Systemen. Stellen Sie Ihre API als API-App in App Service bereit, und profitieren Sie von einer für Unternehmen geeigneten Sicherheit, einfacher Zugriffssteuerung, hybrider Konnektivität, automatischer SDK-Generierung und nahtloser Integration in [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Warum API-Apps?

API-Apps stellt die folgenden Features bereit, die heute als öffentliche Vorschau verfügbar sind:

- **Einfache Nutzung** – Die integrierte [Swagger](#concepts)-Unterstützung ermöglicht es, dass Ihre APIs auf einfache Weise von zahlreichen Clients genutzt werden können. Mühelos konfigurieren Sie [CORS](#concepts), und automatisch generieren Sie Clientcode für Ihre APIs in zahlreichen verschiedenen Sprachen, darunter C#, Java und Javascript.

- **Einfache Zugriffssteuerung** – Sie können eine API-App vor nicht authentifiziertem Zugriff schützen, ohne Änderungen an Ihrem Code vornehmen zu müssen. Integrierte Authentifizierungsdienste sichern APIs für den Zugriff durch andere Dienste oder Clients, die Benutzer repräsentieren. Zu den unterstützten Identitätsanbietern zählen Azure Active Directory und Drittanbieterdienste wie Facebook und Twitter. Clients können die Active Directory Authentication Library (ADAL) oder das Mobile Apps-SDK verwenden. Weitere Informationen finden Sie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service](../app-service/app-service-authentication-overview.md).

- **Visual Studio-Integration** – Dedizierte Tools in Visual Studio optimieren das Erstellen, Bereitstellen, Nutzen, Debuggen und Verwalten von API-Apps.

- **Integration in Logik-Apps** – Von Ihnen erstellte API-Apps können von [App Service-Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) genutzt werden.

- **Nutzen Sie eigene APIs in unveränderter Form** – Es ist nicht erforderlich, den Code vorhandener APIs zu ändern, um all die API-App-Features zu nutzen – stellen Sie lediglich Ihren Code in einer API-App bereit. Ihre API kann alle von App Service unterstützten Sprachen oder Frameworks verwenden, einschließlich ASP.NET und C#, Java, PHP, Node.js und Python.

Dies sind nur einige Highlights. Weitere Features, die API-Apps nutzen können, finden Sie unter [Web-Apps – Übersicht](../app-service-web/app-service-web-overview.md).

>[AZURE.NOTE]Mit [Azure API Management](../api-management/api-management-key-concepts.md) können Sie den Clientzugriff auf APIs steuern, die von App Service-API-Apps gehostet werden. API-Apps bietet zwar Authentifizierungsdienste, jedoch nicht andere Features zur Zugriffsverwaltung, die API Management bietet, z. B. Endpunktkonsolidierung und Drosselung.
>
>API-Apps befindet sich derzeit in der öffentlichen Vorschau. [App Service-Web-Apps](../app-service-web/app-service-web-overview.md) ist ein allgemein verfügbarer Dienst, der zum Erstellen und Hosten von sicheren geschäftskritischen Anwendungen auf globaler Ebene vorgesehen ist. Wenn Sie jetzt nach einem allgemein verfügbaren Dienst zum Erstellen Ihrer API suchen, ist Web-Apps eine hervorragende Option. Sobald API-Apps allgemein verfügbar ist, wird eine Möglichkeit bereitgestellt, vorhandene Web-Apps zu migrieren und die zusätzlichen Features von API-Apps zu nutzen.

## API-Apps – Konzepte ##

- **Swagger** – Ein Framework für die Dokumentation und Erkennung einer RESTful-API, wird in API-Apps standardmäßig verwendet. Weitere Informationen finden Sie unter [http://swagger.io/](http://swagger.io/).
- **Cross Origin Resource Sharing (CORS)** – ein Mechanismus, der die JavaScript-Ausführung in einem Browser ermöglicht, damit eine API aufgerufen werden kann, die auf einer anderen Domäne gehostet wird als die, von der die Webseite geladen wurde.
- **Trigger** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) aufgerufen werden kann, um einen Workflowprozess zu initiieren, wenn eine bestimmte Bedingung erfüllt ist. Beispielsweise kann eine API-App eine Methode bereitstellen, die von der Logik-App regelmäßig aufgerufen wird, um in einem Twitter-Feed nach einem bestimmten Schlagwort zu suchen. Weitere Informationen finden Sie unter [API-App-Trigger](app-service-api-dotnet-triggers.md).
- **Aktion** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) zum Verarbeiten von Daten aufgerufen werden kann, nachdem ein Workflow durch einen Trigger gestartet wurde. Beispielsweise könnte eine API-App eine Methode bereitstellen, die die Logik-App als Reaktion auf einen Tweet aufruft, der über den Twitter-Trigger gefunden wurde. Aktionen sind API-Methoden, die durch eine Swagger-API-Definition verfügbar gemacht werden.
- **Gateway** – Eine Web-App, die API-Verwaltungsfunktionen wie z. B. die Authentifizierung für alle API-Apps in einer Ressourcengruppe übernimmt.

## Erste Schritte

Arbeiten Sie zum Einstieg in API-Apps das Lernprogramm [Erstellen einer API-App](app-service-dotnet-create-api-app.md) durch.

Eine Liste der bekannten Probleme mit API-Apps finden Sie in dem entsprechenden [Forumsbeitrag](https://social.msdn.microsoft.com/Forums/de-DE/7f8b42f2-ac0d-48b8-a35e-3b4934e1c25e/api-app-known-issues?forum=AzureAPIApps).

Weitere Informationen zur Azure App Service-Plattform finden Sie unter [Azure App Service](../app-service/app-service-value-prop-what-is.md).

 

<!---HONumber=Nov15_HO2-->