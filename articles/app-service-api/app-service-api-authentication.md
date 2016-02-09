<properties
	pageTitle="Authentifizierung und Autorisierung für API-Apps in Azure App Service | Microsoft Azure"
	description="Informationen über die Authentifizierungs- und Autorisierungsdienste von Azure App Service für API-Apps."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/21/2016"
	ms.author="tdykstra"/>

# Authentifizierung und Autorisierung für API-Apps in Azure App Service

[AZURE.INCLUDE [Auswahl](../../includes/app-service-api-auth-selector.md)]

## Übersicht 

Azure App Service bietet integrierte Authentifizierungs- und Autorisierungsdienste zum Implementieren von [OAuth 2.0](#oauth) und [OpenID Connect](#oauth). Dieser Artikel beschreibt die Dienste und Optionen, die für die API-Apps in Azure App Service verfügbar sind.

Das folgende Diagramm veranschaulicht die wichtigsten Eigenschaften der App Service-Authentifizierung:

* Sie verarbeitet eingehende API-Anfragen vor. Das bedeutet, dass es mit allen von App Service unterstützten Sprachen und Frameworks funktioniert.
* Sie können mit mehreren Optionen wählen, wie hoch der Anteil der Authentifizierung ist, den Sie mit Ihrem eigenen Code ausführen möchten.
* Sie eignet sich sowohl für die Endbenutzer- als auch Dienstkontoauthentifizierung. 
* Sie unterstützt fünf Identitätsanbieter: Azure Active Directory, Facebook, Google, Twitter und das Microsoft-Konto.
* Sie funktioniert für API-Apps, Web-Apps und mobile Apps identisch.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Sprachunabhängig

Die App Service-Authentifizierungsverarbeitung erfolgt, bevor Anforderungen Ihre API-App erreichen, d. h. die Authentifizierungsfunktionen sind mit API-Apps einsetzbar, die in beliebiger Sprache bzw. beliebigem Framework geschrieben sind. Ihre API kann auf ASP.NET, Java, Node.js oder einem beliebigen Framework basieren, dass App Service unterstützt.

App Service übergibt das JSOn-Webtoken (JWT) im Autorisierungsheader einer HTTP-Anforderung, und in jeder Sprache und jedem Framework geschriebener Code kann die erforderlichen Informationen aus dem Token abrufen. Darüber hinaus bietet App Service durch Einstellung einiger spezieller Header leichteren Zugriff auf die am häufigsten verwendeten Ansprüche, z. B. folgende:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
In einer .NET-API können Sie das Attribut `Authorize` verwenden, und für die fein abgestufte Autorisierung können Sie leicht Code schreiben, der auf Ansprüchen basiert, weil Ansprücheinformationen für Sie in .NET-Klassen eingetragen werden.

## Mehrere Datenschutzoptionen

App Service kann verhindern, dass anonyme HTTP-Anfragen Ihre API-App erreichen. In diesem Fall werden alle Anforderungen übergeben und alle Token für Anforderungen, die sie enthalten, überprüft bzw alle Anfragen ohne Aktion weitergeleitet:

1. Nur zulassen, dass authentifizierte Anforderungen Ihre API-App erreichen.

	Wenn von einem Browser eine anonyme Anforderung empfangen wird, leitet App Service sie zu einer Anmeldeseite für den von Ihnen gewählten Authentifizierungsanbieter (Azure AD, Google, Twitter usw.) um.

	Mit dieser Option müssen Sie keinen Authentifizierungscode in Ihre App schreiben, und der Autorisierungscode wird vereinfacht, da die wichtigsten Ansprüche in den HTTP-Headern bereitgestellt werden.

2. Zulassen, dass alle Anforderungen Ihre API-App erreichen, aber authentifizierte Anforderungen überprüfen, und Authentifizierungsdaten an die HTTP-Header übergeben.

	Diese Option bietet Ihnen mehr Flexibilität bei der Behandlung anonymer Anforderungen. Sie müssen jedoch Code schreiben, wenn Sie verhindern möchten, dass anonyme Benutzer Ihre API verwenden. Da die am häufigsten verwendeten Ansprüche in den Headern von HTTP-Anforderungen übergeben werden, ist der Autorisierungscode relativ einfach.
	
3. Zulassen, dass alle Anforderungen Ihre API erreichen, und keine Aktionen an den Authentifizierungsdaten in den Anforderungen ausführen.

	Bei dieser Option bleiben die Aufgaben der Authentifizierung und Autorisierung völlig Ihrem Anwendungscode überlassen.

Im [Azure-Portal](https://portal.azure.com/) wählen Sie die gewünschte Option auf dem Blatt **Authentifizierung/Autorisierung**.

![](./media/app-service-api-authentication/authblade.png)

Aktivieren Sie für Option 1 und 2 **App Service-Authentifizierung**, und wählen Sie in der Dropdownliste **Auszuführende Aktion, wenn Anforderung nicht authentifiziert wird** die Option **Anmelden** oder **Anforderung zulassen (keine Aktion)**. Wenn Sie **Anmelden** wählen, müssen Sie einen Authentifizierungsanbieter auswählen und konfigurieren.

![](./media/app-service-api-authentication/actiontotake.png)

Ausführliche Informationen zum Konfigurieren der Authentifizierung finden Sie unter [So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). Der Artikel gilt für API-Apps sowie für mobile Apps und enthält Links zu weiteren Artikeln für andere Authentifizierungsanbieter.
 
## <a id="internal"></a> Dienstkontoauthentifizierung

Die App Service-Authentifizierung eignet sich für interne Szenarien, z. B. für den Aufruf aus einer API-App, der sich an eine andere API-App richtet. In diesem Szenario können Sie ein Token abrufen, indem Sie Anmeldeinformationen für ein Dienstkonto anstelle der Endbenutzer-Anmeldeinformationen verwenden. Ein Dienstkonto wird in Azure Active Directory auch als *Dienstprinzipal* bezeichnet. Die Authentifizierung mithilfe eines solchen Kontos ist auch als Dienst-zu-Dienst-Szenario bekannt.

Schützen Sie in Dienst-zu-Dienst-Szenarien die aufgerufene API-App mithilfe von Azure Active Directory, und stellen Sie ein AAD-Dienstprinzipal-Autorisierungstoken bereit, wenn Sie die API-App aufrufen. Sie können ein Token anfordern, indem Sie die Client-ID und den geheimen Clientschlüssel aus der AAD-Anwendung bereitstellen. Es ist kein spezieller Azure-Code erforderlich, wie er beispielsweise für das Behandeln des Mobile Services-Zumo-Tokens benötigt wurde. Ein Beispiel für dieses Szenario mithilfe von ASP.NET-API-Apps finden Sie in dem Tutorial [Dienstprinzipalauthentifizierung für API-Apps in Azure App Service](app-service-api-dotnet-service-principal-auth.md).

Wenn Sie ein Dienst-zu-Dienst-Szenario ohne App Service-Authentifizierung behandeln möchten, können Sie Clientzertifikate oder Standardauthentifizierung verwenden. Informationen zu Clientzertifikaten in Azure finden Sie unter [Konfigurieren von gegenseitiger TLS-Authentifizierung für Web-Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Informationen zur grundlegenden Authentifizierung in ASP.NET finden Sie unter [Authentication Filters in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters) (Authentifizierungsfilter in ASP.NET-Web-API 2).

Dienstkontoauthentifizierung von einer App Service-Logik-App zu einer API-App ist ein Sonderfall, der in [Verwenden der in App Service gehosteten benutzerdefinierten API mit Logik-Apps](../app-service-logic/app-service-logic-custom-hosted-api.md) erklärt wird.

## Clientauthentifizierung

Informationen zur Authentifizierung von mobilen Clients finden Sie in der [Dokumentation zur Authentifizierung für mobile Apps](../app-service-mobile/app-service-mobile-ios-get-started-users.md). Die App Service-Authentifizierung für mobile Apps und API-Apps ist identisch.
  
## Weitere Informationen

Weitere Informationen zur Authentifizierung und Autorisierung in Azure App Service finden Sie in den folgenden Ressourcen:

* [Expanding App Service authentication / authorization (Erweitern der App Service-Authentifizierung/-Autorisierung)](/blog/announcing-app-service-authentication-authorization/)
* [So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md) (Enthält oben auf der Seite Links für andere Authentifizierungsanbieter.) 

Weitere Informationen zu OAuth 2.0, OpenID Connect und JSON-Webtoken (JWT) finden Sie in den folgenden Ressourcen.

* [Getting started with OAuth 2.0 (in englischer Sprache)](http://shop.oreilly.com/product/0636920021810.do "Getting started with OAuth 2.0 (in englischer Sprache)") 
* [Introduction to OAuth2, OpenID Connect and JSON Web Tokens (JWT) - PluralSight Course (in englischer Sprache)](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Building and Securing a RESTful API for Multiple Clients in ASP.NET - PluralSight course (in englischer Sprache)](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

Weitere Informationen zur Authentifizierung mit Azure Active Directory finden Sie in den folgenden Ressourcen.

* [Azure AD-Szenarien](http://aka.ms/aadscenarios)
* [Azure AD Entwicklerhandbuch](http://aka.ms/aaddev)
* [Azure AD-Beispiele](http://aka.ms/aadsamples)

## Nächste Schritte

In diesem Artikel wurden Authentifizierungs- und Autorisierungsfeatures von App Service für API-Apps erläutert.

Wenn Sie der Erste-Schritte-Sequenz der Tutorials für ASP.NET- und API-Apps folgen, probieren Sie diese Features im nächsten Tutorial aus, [Benutzerauthentifizierung für API-Apps in Azure App Service](app-service-api-dotnet-user-principal-auth.md).

Weitere Informationen zur Verwendung von Knoten und Java in Azure App Service finden Sie unter [Node.js Developer Center](/develop/nodejs/) und [Java Developer Center](/develop/java/).

<!---HONumber=AcomDC_0204_2016-->