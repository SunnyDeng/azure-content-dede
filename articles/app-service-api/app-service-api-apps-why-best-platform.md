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
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# API-Apps-Übersicht

API-Apps ist einer der vier App-Typen, die von [Azure App Service](../app-service/app-service-value-prop-what-is.md) angeboten werden.

![](./media/app-service-api-apps-why-best-platform/appservicesuite.png)

[App Service](../app-service/app-service-value-prop-what-is.md) ist eine vollständig verwaltete Plattform, die einen umfangreichen Satz von Funktionen für mobile, Web- und Integrationsszenarien bereitstellt. API-Apps in App Service bietet Funktionen, mit denen APIs in der Cloud und lokal einfacher erstellt, gehostet und genutzt werden können. Stellen Sie Ihre API als API-App in App Service bereit, und profitieren Sie von einer für Unternehmen geeigneten Sicherheit, einfacher Zugriffssteuerung, hybrider Konnektivität, automatischer SDK-Generierung und nahtloser Integration in [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md).

## Warum API-Apps?

API-Apps stellt die folgenden Funktionen bereit:

- **Einfache Nutzung** – Die integrierte Unterstützung für [Swagger-API-Metadaten](#concepts) ermöglicht es, dass Ihre APIs auf einfache Weise von zahlreichen Clients genutzt werden können. Clientcode für Ihre APIs kann in zahlreichen verschiedenen Programmiersprachen generiert werden, darunter C#, Java und Javascript. Sie können ganz einfach [CORS](#concepts) konfigurieren, ohne den Code zu ändern. Weitere Informationen finden Sie unter [Metadaten in App Service-API-Apps für die API-Ermittlung und Codegenerierung](app-service-api-metadata.md) und [Nutzen einer API-App aus JavaScript mit CORS](app-service-api-cors-consume-javascript.md). 

- **Einfache Zugriffssteuerung** – Sie können eine API-App vor nicht authentifiziertem Zugriff schützen, ohne Änderungen an Ihrem Code vornehmen zu müssen. Integrierte Authentifizierungsdienste sichern APIs für den Zugriff durch andere Dienste oder Clients, die Benutzer repräsentieren. Zu den unterstützten Identitätsanbietern zählen Azure Active Directory und Drittanbieterdienste wie Facebook und Twitter. Clients können die Active Directory Authentication Library (ADAL) oder das Mobile Apps-SDK verwenden. Weitere Informationen finden Sie unter [Expanding App Service authentication / authorization](/blog/announcing-app-service-authentication-authorization/) (in englischer Sprache) und [App Service-API-Apps – Änderungen](app-service-api-whats-changed.md).

- **Visual Studio-Integration** – Dedizierte Tools in Visual Studio optimieren das Erstellen, Bereitstellen, Nutzen, Debuggen und Verwalten von API-Apps. Weitere Informationen finden Sie unter [Announcing the Azure SDK 2.8.1 for .NET](/blog/announcing-azure-sdk-2-8-1-for-net/) (in englischer Sprache).

- **Integration in Logik-Apps** – Von Ihnen erstellte API-Apps können von [App Service-Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) genutzt werden. Wie, das erfahren Sie unter [Verwenden der in App Service gehosteten benutzerdefinierten API mit Logik-Apps](../app-service-logic/app-service-logic-custom-hosted-api.md). Informationen zu aktuellen Änderungen im Hinblick auf die Integration von API-Apps in Logik-Apps finden Sie unter [App Service-API-Apps – Änderungen](app-service-api-whats-changed.md).

- **Nutzen Sie eigene APIs in unveränderter Form** – Es ist nicht erforderlich, den Code vorhandener APIs zu ändern, um all die API-App-Features zu nutzen – stellen Sie lediglich Ihren Code in einer API-App bereit. Ihre API kann alle von App Service unterstützten Sprachen oder Frameworks verwenden, einschließlich ASP.NET und C#, Java, PHP, Node.js und Python.

Darüber hinaus kann eine API-App von Features profitieren, die von [Web-Apps](../app-service-web/app-service-web-overview.md) und [Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) bereitgestellt werden. Dies gilt auch umgekehrt: Wenn Sie eine API mithilfe einer Web-App oder einer mobilen App hosten, kann diese von API-App-Funktionen wie Swagger-Metadaten zur Generierung des Clientcodes und CORS für den domänenübergreifenden Browserzugriff profitieren. Der einzige Unterschied zwischen den drei App-Typen (API, Web, mobil) ist der Name und das Symbol im Azure-Portal. Da alle dieselben Funktionen haben, muss der Typ einer App zu keinem Zeitpunkt geändert werden, um die von Ihnen gewünschten Funktionen zu erhalten. Wenn Sie jedoch den Typ einer vorhandenen App ändern möchten, ist dies ein einfacher Vorgang. Weitere Informationen finden Sie im Abschnitt **Ändern eines App-Typs** unter [Erste Schritte mit API-Apps und ASP.NET in Azure App Service](app-service-api-dotnet-get-started.md#optional-changing-an-app-type).

## Erweitern von API-Apps mithilfe von Azure API Management 

API-Apps und [Azure API Management](../api-management/api-management-key-concepts.md) sind sich ergänzende Dienste:

* API Management dient zur Verwaltung von APIs. Sie können mit einem API Management-Front-End auf einer API unter anderem die Nutzung überwachen und drosseln, die Ein- und Ausgabe bearbeiten und mehrere APIs zusammenführen konsolidieren. Die verwalteten APIs können an einem beliebigen Ort gehostet werden.
* API-Apps wird für das Hosting von APIs verwendet. Der Dienst umfasst Funktionen zur einfachen Entwicklung und Nutzung von APIs, führt aber nicht die Art von Überwachung, Drosselung, Bearbeitung oder Konsolidierung durch, für die API Management zuständig ist. 

Mit API Management können Sie von API-Apps gehostete APIs verwalten oder API-Apps ohne API Management verwenden.

API Management und API-Apps verfügen über ein paar ähnliche Funktionen. Beispielsweise können beide die CORS-Unterstützung automatisieren. Wenn Sie die beiden Dienste zusammen verwenden, würden Sie API Management für CORS verwenden, da der Dienst als Front-End für API-Apps dient.

## <a id="concepts"></a> API-Apps – Konzepte

- **Swagger**: – Ein Framework für die Dokumentation und Erkennung einer RESTful-API, wird in API-Apps standardmäßig verwendet. Weitere Informationen finden Sie unter [http://swagger.io/](http://swagger.io/).
- **Cross Origin Resource Sharing (CORS)**: Ein Mechanismus, der die JavaScript-Ausführung in einem Browser ermöglicht, damit eine API aufgerufen werden kann, die auf einer anderen Domäne gehostet wird als die, von der die Webseite geladen wurde. Weitere Informationen finden Sie unter [Nutzen einer API-App aus JavaScript mit CORS](app-service-api-cors-consume-javascript.md). 
- **Trigger** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) aufgerufen werden kann, um einen Workflowprozess zu initiieren, wenn eine bestimmte Bedingung erfüllt ist. Beispielsweise kann eine API-App eine Methode bereitstellen, die von der Logik-App regelmäßig aufgerufen wird, um in einem Twitter-Feed nach einem bestimmten Schlagwort zu suchen. Weitere Informationen finden Sie unter [API-App-Trigger](app-service-api-dotnet-triggers.md).
- **Aktion** – Eine REST-API, die von [Logik-Apps](../app-service-logic/app-service-logic-what-are-logic-apps.md) zum Verarbeiten von Daten aufgerufen werden kann, nachdem ein Workflow durch einen Trigger gestartet wurde. Beispielsweise könnte eine API-App eine Methode bereitstellen, die die Logik-App als Reaktion auf einen Tweet aufruft, der über den Twitter-Trigger gefunden wurde. Aktionen sind API-Methoden, die durch eine Swagger-API-Definition verfügbar gemacht werden.

## Erste Schritte

Arbeiten Sie zum Einstieg in API-Apps eines der Tutorials unter [Erste Schritte mit API-Apps ](app-service-api-dotnet-get-started.md) durch.

Um Fragen über API-Apps zu stellen, starten Sie einen Thread im [API-Apps-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureAPIApps).

<!---HONumber=AcomDC_0316_2016-->