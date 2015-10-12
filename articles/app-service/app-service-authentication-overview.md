<properties 
	pageTitle="Authentifizierung in API-Apps und mobilen Apps in Azure App Service" 
	description="Erfahren Sie, wie die Authentifizierung für API-Apps und mobile Apps in Azure App Service konfiguriert und verwendet wird." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Authentifizierung für API-Apps und mobile Apps in Azure App Service

## Übersicht

In diesem Artikel werden die integrierten Authentifizierungsfeatures für [API-Apps](../app-service-api/app-service-api-apps-why-best-platform.md) und [mobile Apps](../app-service-mobile/app-service-mobile-value-prop-preview.md) beschrieben.

Der Abschnitt [Nächste Schritte](#next-steps) am Ende des Artikels enthält Links zu relevanten Dokumentationen zu Vorgehensweisen.

## Azure App Service-Gateway

Azure App Service bietet integrierte Authentifizierungsdienste, die [OAuth 2.0](#oauth) und [OpenID Connect](#oauth) implementieren und mit mehreren *Identitätsanbietern* funktionieren. Ein Identitätsanbieter ist ein externer Dienst mit Azure App Service-Vertrauensstellung, der die App-Benutzer authentifiziert. App Service unterstützt die am häufigsten verwendeten Identitätsanbieter:

* Azure Active Directory
* Microsoft-Konto
* Google
* Twitter
* Facebook

### Gatewayarchitektur

Alle Azure-Ressourcengruppen, die API-Apps oder mobile Apps enthalten, umfassen ein *Gateway*. Das Gateway ist eine Azure-Ressource, die in einer Web-App ausgeführt wird und Verwaltungsaufgaben verarbeitet, einschließlich der Authentifizierung für API-Apps und mobile Apps in der Ressourcengruppe.

Das Gateway verarbeitet Anmeldeverfahren, verwaltet Token und verhindert, dass nicht authentifizierte Aufrufe API-Apps erreichen, die [so konfiguriert sind, dass sie einen authentifizierten Zugriff erfordern](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app). Das Gateway kann den Zugriff auf API-Apps steuern, da alle eingehenden HTTP-Anforderungen, die für API-Apps in der Ressourcengruppe bestimmt sind, über das Gateway weitergeleitet werden.

Das folgende Diagramm veranschaulicht diese Gatewayfunktionen.

![](./media/app-service-authentication-overview/gateway.png)

### Features von Gateways

Die Gatewayauthentifizierungsdienste bieten mehrere Vorteile gegenüber einer eigenen Implementierung von OAuth 2.0:

* Microsoft bietet SDKs, die die Ausführung von Authentifizierungs- und Autorisierungsaufgaben über eine vereinfachte Syntax ermöglichen.

* Da App Service mehr Authentifizierungsaufgaben verarbeitet, wird Ihre Entwicklungs- und Testzeit minimiert, und Sie vermeiden die meisten oder alle Auswirkungen von Änderungen in Anbieterimplementierungen von OAuth.

* Wenn Sie mehrere Anwendungen schützen und in eine Ressourcengruppe aufnehmen müssen, benötigen Sie nur eine Client-ID und einen geheimen Clientschlüssel für jeden Authentifizierungsanbieter, da nur eine Umleitungs-URL für das Gateway vorhanden ist.

* Überwachung und Problembehandlung sind einfacher, da Sie Datenverkehr im Zusammenhang mit der Authentifizierung für eine vollständige Ressourcengruppe durch die Überwachung des Gateways überwachen können.

* Debuggen ist einfacher, da Sie ein Programm für die Verwendung des Gateways konfigurieren können, während Sie lokal den Debugmodus verwenden, und Sie müssen Umleitungs-URLs in Ihrem Identitätsanbieterkonto nicht ändern.

## Serverfluss oder Clientfluss

Das App Service-Gateway bietet zwei Möglichkeiten zum Authentifizieren von Clients: *Clientfluss* und *Serverfluss*. In beiden Fällen sendet die Clientanwendung die Anmeldeinformationen des Benutzers (normalerweise Benutzername und Kennwort) direkt an den Identitätsanbieter. In beiden Fällen empfängt weder das Gateway noch die Anwendung Benutzeranmeldeinformationen.

### Clientfluss

Clientfluss bedeutet, dass die Clientanwendung direkt mit dem Identitätsanbieter kommuniziert, um das Zugriffstoken des Anbieters zu erhalten. Die Clientanwendung sendet das Zugriffstoken des Anbieters zum Gateway. Das Gateway erstellt ein Benutzerkontexttoken und sendet es an den Client. Dieses Benutzerkontexttoken wird auch als Zumo-Token bezeichnet, abgeleitet vom ursprünglichen Codenamen für [Azure Mobile Services](/documentation/services/mobile-services/). (Authentifizierungsdienste für API-Apps und mobile Apps basieren auf der gleichen Architektur, die ursprünglich für Mobile Services entwickelt wurde.)

Das folgende Diagramm veranschaulicht diesen Fluss.

![](./media/app-service-authentication-overview/clientflow.png)

Der Client gibt dann das Zumo-Token in HTTP-Anforderungen an, wenn er geschützte API-Apps oder mobile Apps aufruft. Das Gateway speichert die Anbietertoken und verfolgt, welche welchen Zumo-Token zugeordnet sind.

### Serverfluss

Serverfluss bedeutet, dass die Clientanwendung auf das Gateway angewiesen ist, um mit dem Identitätsanbieter zu kommunizieren und die Anmeldung zu initiieren. Der Clientbrowser ruft eine Gateway-URL auf, und das Gateway leitet die Anforderung an die Anmeldeseite des Identitätsanbieters weiter. Nachdem sich der Benutzer angemeldet hat, erhält das Gateway das Token des Identitätsanbieters, erstellt das Zumo-Token und sendet das Zumo-Token an den Client.

![](./media/app-service-authentication-overview/serverflow.png)

Nachfolgende Interaktionen zwischen der Clientanwendung und geschützten API-Apps oder mobilen Apps werden wie beim Clientfluss verarbeitet: Der Client gibt das Zumo-Token in HTTP-Anforderungen an.

### Auswählen zwischen den Clientfluss und Serverfluss

Der Clientfluss ist in der Regel die beste Wahl, wenn der Identitätsanbieter, den Sie verwenden möchten, über ein SDK für die Clientplattform verfügt, die Sie unterstützen möchten. Der Clientfluss bietet die größte Benutzerfreundlichkeit, da der Benutzer weniger häufig Anmeldeinformationen eingeben muss. Beispiel: Besitzt der Benutzer ein Android-Gerät, ist dem Gerät wahrscheinlich ein Google-Konto zugeordnet. Wenn er dieses Konto also verwenden könnte, ohne den Benutzernamen und das Kennwort erneut eingeben zu müssen, wäre dadurch die Benutzerfreundlichkeit besser. Das Gleiche gilt für ein Facebook-Konto auf einem Android-, iOS- oder Windows Phone-Gerät bzw. für ein Microsoft-Konto auf einem Windows-Desktop oder Windows Phone.

In anderen Szenarien kann der Serverfluss eine bessere Wahl sein:

- Es gibt kein systemeigenes Client-SDK für den Identitätsanbieter und die Clientplattform, die Sie unterstützen möchten.

- Sie möchten schnell etwas in die Produktion einbinden und die Benutzerfreundlichkeit später verbessern, wenn es zeitlich besser passt. Beim Serverfluss übernimmt Azure App Service mehr von den Authentifizierungsaufgaben. Dadurch verringert sich für Sie der Aufwand von Entwicklung und Tests.

## Im Auftrag ausgeführte ausgehende Aufrufe von SaaS-Plattformen

Sie können Code schreiben, um ausgehende Aufrufe von SaaS-Plattformen (Software-as-a-Service) im Auftrag eines angemeldeten Benutzers vorzunehmen, oder Sie können eine [Connector-API-App](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md) verwenden. Beispiel: Um einen Tweet vom Twitter-Konto des Benutzers zu senden, können Sie [ein Twitter-SDK](https://dev.twitter.com/overview/api/twitter-libraries) verwenden oder einen [Twitter-Connector](../app-service-mobile/app-service-logic-connector-twitter.md) im Azure-Abonnement bereitstellen und aufrufen. In diesem Abschnitt wird der Zugriff auf eine SaaS-Plattform über Code behandelt, der in einer API-App oder einer mobilen App ausgeführt wird.

### <a id="obotoidprovider"></a> Verwenden des Identitätsanbietertokens 

Das Gateway verwaltet einen *Tokenspeicher*, in dem es ein Zumo-Token einem oder mehreren Identitätsanbieter-Zugriffstoken und -Aktualisierungstoken zuordnet. Wenn eine HTTP-Anforderung mit einem gültigen Zumo-Token empfangen wird, weiß das Gateway, welche Identitätsanbietertoken zu dem Benutzer gehören.
  
Wenn der in der API-App oder mobilen App ausgeführte Code eine geschützte Ressource für den angemeldeten Benutzer aufrufen muss, kann er das Token des Identitätsanbieters aus dem Tokenspeicher des Gateways abrufen und verwenden, wie in der folgenden Abbildung dargestellt. Im Diagramm wird davon ausgegangen, dass sich der Client bereits beim Gateway authentifiziert hat und über das Zumo-Token verfügt.

![](./media/app-service-authentication-overview/idprovidertoken.png)

Nehmen wir beispielsweise an, dass der Identitätsanbieter Azure Active Directory (AAD) ist und dass Ihre API-App das AAD-Zugriffstoken verwenden möchte, um die Graph-API von AAD aufzurufen oder Zugriff auf eine SharePoint-Website anzufordern, für die der Benutzer Berechtigungen hat. Sie können eine Anforderung an das Gateway senden, um das AAD-Tokens abzurufen, und dann das AAD-Token verwenden, um die Graph-API aufzurufen oder ein Zugriffstoken für die SharePoint-Website zu erhalten.

### <a id="obotosaas"></a>Erhalten der Zustimmung des Benutzers für den Zugriff auf andere Ressourcen

Das Gateway verfügt auch über integrierte Features, um die Zustimmung des Benutzers zu erhalten, wenn Sie auf Ressourcen zugreifen möchten, die von einem anderen Anbieter als dem ursprünglichen Identitätsanbieter gesichert werden. Beispiel: Sie möchten für einen Benutzer, der sich mit Azure Active Directory anmeldet, auf Dateien im Dropbox-Konto des Benutzers zugreifen.

Das App Service-Gateway bietet integrierte Unterstützung, um die Zustimmung des Benutzers für einen solchen Zugriff bei den folgenden Anbietern zu erhalten:

* Box
* DropBox
* Facebook
* Google
* Office 365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Microsoft Account

Für diese Anbieter verwaltet das Gateway Zugriffstoken und ordnet sie dem Zumo-Token zu, wie für das Zugriffstoken des Identitätsanbieters. Der Prozess zum Erhalten der Zustimmung des Benutzers und zum Aufrufen einer SaaS-Plattform wird im folgenden Diagramm veranschaulicht. Im Diagramm wird davon ausgegangen, dass sich der Client bereits beim Gateway authentifiziert hat und über das Zumo-Token verfügt.

![](./media/app-service-authentication-overview/saastoken.png)

Durch die App Service-Laufzeitunterstützung und -SDKs ist es relativ einfach, Code zu schreiben, der auf Ressourcen zugreift, die durch einen dieser Anbieter gesichert sind. Es gibt einen vorbereitenden Schritt, der nicht im Diagramm dargestellt ist: Sie müssen ein Konto bei dem Anbieter einrichten und die Einstellungen für die Client-ID und den geheimen Clientschlüssel des Anbieters in Azure App Service konfigurieren.

Bei diesen und vielen anderen Anbietern können Sie auch mithilfe einer vorgefertigten [Connector-API-App](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md) auf gesicherte Ressourcen zugreifen.

## SDK-Verfügbarkeit

Für API-Apps bietet das SDK für .NET Authentifizierungsfunktionen:

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService) – für die Verwendung in einem API-App-Client.  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) – für die Verwendung in einem Web-API-Projekt, das in einer API-App ausgeführt wird.
 
Darüber hinaus kann Visual Studio automatisch Code generieren, der mit dem SDK für .NET verwendet werden kann, um den Code weiter zu vereinfachen, den Sie zum Aufrufen der API-App schreiben. Weitere Informationen finden Sie unter [Nutzen einer API-App in Azure App Service aus einem .NET-Client](../app-service-api/app-service-api-dotnet-consume.md).

Für mobile Apps stehen SDKs für die folgenden Plattformen zu Verfügung:

- [.NET-Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (Lernprogramm in der Entwicklung)

## Alternative Authentifizierungsmethoden

Wenn die Gatewayauthentifizierungsdienste den Anforderungen der App nicht entsprechen, können Sie die Authentifizierung selbst durchführen oder den Azure API Management-Dienst dazu verwenden.

### <a id="doityourself"></a>Selbst durchgeführte Authentifizierung

Sie können z. B. ein Authentifizierungsframework wie [ASP.NET Identity](http://www.asp.net/identity) oder [Thinktecture](http://www.thinktecture.com/identityAndAccessControl) in Azure ausführen. Dadurch können Sie steuern, wie alles funktioniert, Sie müssen aber auch mehr Zeit investieren, um die Authentifizierungsfunktionalität zu entwickeln und zu testen. Wenn Sie darüber hinaus mehrere Apps mit mehreren Umleitungs-URLs schützen müssen, müssen Sie mehrere Client-IDs und geheime Clientschlüssel bei Authentifizierungsdrittanbietern wie Facebook, Google und Twitter konfigurieren.

Zurzeit unterstützt App Service keine selbst durchgeführte Lösung zusammen mit der Gatewayauthentifizierung, so wie es in [Mobile Services](mobile-services-dotnet-backend-get-started-custom-authentication.md) möglich ist.

### <a id="apim"></a>Azure API Management

Wenn APIs vorhanden sind, die Sie mit der Authentifizierung schützen möchten, können Sie dazu den Azure API Management-Dienst verwenden. Informationen zum Verwenden von API Management mit API-Apps finden Sie im Blogbeitrag von Panos Kefalidis: [Taking advantage of API Management for API Apps](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/) (in englischer Sprache).

## Nächste Schritte

In diesem Artikel wurden die Authentifizierungsdienste von Azure App Service für API-Apps und mobile Apps erläutert. Im Folgenden finden Sie einige Links zu Ressourcen mit Informationen über die zugrunde liegenden Authentifizierungsprotokolle sowie die Dokumentationen zur Verwendung der App Service-Authentifizierungsfeatures.

* [OAuth 2.0, OpenID Connect und JSON Web Tokens (JWT)](#oauth)
* Ressourcen für API-Apps
	* [API-Apps – Clientfluss](#apiaclient)
	* [API-Apps – Serverfluss](#apiaserver)
	* [API-Apps – Aufrufe im Auftrag](#apiaobo)
* Ressourcen für mobile Apps
	* [Mobile Apps – Clientfluss](#maclient)
	* [Mobile Apps – Serverfluss](#maserver)
	* [Mobile Apps – Aufrufe im Auftrag](#maobo)

### <a id="oauth"></a>OAuth 2.0, OpenID Connect und JSON Web Tokens (JWT)

* [Getting started with OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Getting started with OAuth 2.0 (in englischer Sprache)") (in englischer Sprache) 
* [Introduction to OAuth2, OpenID Connect and JSON Web Tokens (JWT) - PluralSight Course](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) (in englischer Sprache) 
* [Building and Securing a RESTful API for Multiple Clients in ASP.NET - PluralSight course](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet) (in englischer Sprache)

### <a id="apiaclient"></a>API-Apps – Clientfluss

* [Schützen einer API-App](../app-service-api/app-service-api-dotnet-add-authentication.md) – Der Teil zur API-App-Konfiguration betrifft sowohl den Client- als auch den Serverfluss, aber der Teil mit dem Test im Browser veranschaulicht den Serverfluss.
* [Nutzen einer API-App in Azure App Service über einen .NET-Client](../app-service-api/app-service-api-dotnet-consume.md) – Die Beispiel-App für einen authentifizierten Aufruf veranschaulicht den Serverfluss. Der folgende Abschnitt [Clientfluss](../app-service-api/app-service-api-dotnet-consume.md#client-flow) enthält den Beispielcode.

### <a id="apiaserver"></a>API-Apps – Serverfluss

* [Schützen einer API-App](../app-service-api/app-service-api-dotnet-add-authentication.md) – Der Teil der API-App-Konfiguration betrifft sowohl den Client- als auch den Serverfluss. Der Teil mit dem Test im Browser veranschaulicht den Serverfluss.
* [Nutzen einer API-App in Azure App Service über einen .NET-Client](../app-service-api/app-service-api-dotnet-consume.md) – Der Beispielcode für einen authentifizierten Aufruf veranschaulicht den Serverfluss. 

### <a id="apiaobo"></a>API-Apps – Aufrufe im Auftrag

* [Bereitstellen und Konfigurieren einer SaaS-Connector-API-App in Azure App Service](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md) – Veranschaulicht, wie eine vorgefertigte Connector-API-App bereitgestellt, konfiguriert und mit Browsertools aufgerufen wird.
* [Verbinden mit einer SaaS-Plattform aus einer ASP.NET-API-App in Azure App Service](../app-service-api/app-service-api-dotnet-connect-to-saas.md) – Veranschaulicht, wie Sie einen eigenen Connector schreiben, d. h. Code für eine benutzerdefinierte API-App bereitstellen, konfigurieren und schreiben, die Aufrufe im Auftrag an einen SaaS-Anbieter richtet.

### <a id="maclient"></a>Mobile Apps – Clientfluss

* [Hinzufügen der einmaligen Azure Active Directory-Anmeldung zu Ihrer iOS-App](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>Mobile Apps – Serverfluss

* [Hinzufügen der Authentifizierung zu Ihrer iOS-App](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Hinzufügen der Authentifizierung zu Ihrer Xamarin.iOS-App](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Hinzufügen der Authentifizierung zu Ihrer Windows-App](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-users-preview.md)

### <a id="maobo"></a>Mobile Apps – Aufrufe gesicherter Ressourcen im Auftrag

* [Abrufen eines Zugriffstokens und Aufrufen der SharePoint-API in einer mobilen App](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=Oct15_HO1-->