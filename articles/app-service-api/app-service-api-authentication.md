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
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Authentifizierung und Autorisierung für API-Apps in Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Übersicht 

Dieser Artikel beschreibt die Optionen für die Ausführung der Authentifizierung und Autorisierung für API-Apps in Azure App Service.

Das folgende Diagramm veranschaulicht die wichtigsten Eigenschaften der App Service-Authentifizierung:

* Sie verarbeitet eingehende API-Anforderungen vor, wobei Sie mit mehreren Optionen wählen können, wie hoch der Anteil der Authentifizierung ist, den Sie mit Ihrem eigenen Code ausführen möchten. 
* Sie unterstützt fünf Authentifizierungsanbieter: Azure Active Directory, Facebook, Google, Twitter und das Microsoft-Konto.
* Sie gilt sowohl für die Endbenutzer- als auch Dienstprinzipalauthentifizierung. 
* Sie funktioniert für API-Apps, Web-Apps und mobile Apps identisch.

![](./media/app-service-api-authentication/api-apps-overview.png)

## Vorverarbeiten eingehender Anforderungen

App Service kann verhindern, dass anonyme HTTP-Anforderungen Ihre API-App erreichen, oder diejenigen, die Token haben, authentifizieren, bevor sie Ihre API-App erreichen. Für eine API-App können Sie eine von drei Optionen konfigurieren:

1. Nur zulassen, dass authentifizierte Anforderungen Ihre API-App erreichen.

	Wenn ein Browser eine anonyme Anforderung empfängt, leitet App Service sie zu einer Anmeldeseite um.

	Dies funktioniert, wenn Sie im Voraus wissen, welchen Authentifizierungsanbieter (Google, Twitter usw.) Sie verwenden möchten. Dann können Sie App Service dazu konfigurieren, den Anmeldevorgang für Sie abzuwickeln. Alternativ können Sie Ihre eigene URL angeben, zu der App Service anonyme Anforderungen umleitet. Dann können Sie Benutzern verschiedene Authentifizierungsanbieter zur Auswahl präsentieren.

	Mit dieser Option müssen Sie keinen Authentifizierungscode in Ihre App schreiben, und die Autorisierung wird vereinfacht, da die wichtigsten Ansprüche in den HTTP-Headern bereitgestellt werden.

2. Zulassen, dass alle Anforderungen Ihre API-App erreichen, aber authentifizierte Anforderungen überprüfen, und Authentifizierungsdaten an die HTTP-Header übergeben.

	Diese Option bietet Ihnen mehr Flexibilität bei der Behandlung von anonymen Anforderungen und erleichtert, Code zu schreiben, der Zugriff auf die gängigsten Ansprüche benötigt. Im Gegensatz zu Option 1 müssen Sie Code schreiben, wenn Sie verhindern möchten, dass anonyme Benutzer Ihre API verwenden.

3. Zulassen, dass alle Anforderungen Ihre API erreichen, und keine Aktionen an den Authentifizierungsdaten in den Anforderungen ausführen.

	Bei dieser Option bleiben die Aufgaben der Authentifizierung und Autorisierung völlig Ihrem Anwendungscode überlassen.

Im [Azure-Portal](https://portal.azure.com/) wählen Sie die gewünschte Option auf dem Blatt **Authentifizierung/Autorisierung**.

![](./media/app-service-api-authentication/authblade.png)

Aktivieren Sie für Option 1 und 2 **App Service-Authentifizierung**, und wählen Sie in der Dropdownliste **Auszuführende Aktion, wenn Anforderung nicht authentifiziert wird** die Option **Anmelden** oder **Anforderung zulassen (keine Aktion)**. Wenn Sie **Anmelden** wählen, müssen Sie einen Authentifizierungsanbieter auswählen und konfigurieren.

![](./media/app-service-api-authentication/actiontotake.png)
 
## Sprachunabhängig

Die App Service-Authentifizierungsverarbeitung erfolgt, bevor Anforderungen Ihre API-App erreichen, d. h. die Authentifizierungsfunktionen sind mit API-Apps einsetzbar, die in beliebiger Sprache bzw. beliebigem Framework geschrieben sind. Ihre API kann auf ASP.NET, Java, Node.js oder einem beliebigen Framework basieren, dass App Service unterstützt.

App Service übergibt das JWT-Token im Autorisierungsheader einer HTTP-Anforderung, und in jeder Sprache und jedem Framework geschriebener Code kann die erforderlichen Informationen aus dem Token abrufen. Darüber hinaus bietet App Service durch Einstellung einiger spezieller Header leichteren Zugriff auf die am häufigsten verwendeten Ansprüche, z. B. folgende:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID
* X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN
* X-MS-TOKEN-FACEBOOK-EXPIRES-ON
 
In einer .NET-API können Sie das Attribut `Authorize` verwenden, und für die fein abgestufte Autorisierung können Sie leicht Code schreiben, der auf Ansprüchen basiert, weil Ansprücheinformationen für Sie in .NET-Klassen eingetragen werden.

## <a id="internal"></a> Dienstkontoauthentifizierung

Sie können die App Service-Authentifizierung auch für interne Szenarien verwenden, z. B. für den Aufruf aus einer API-App, der sich an eine andere API-App richtet. In diesem Szenario können Sie Anmeldeinformationen für ein Dienstkonto statt Endbenutzer-Anmeldeinformationen für die Authentifizierung verwenden. Ein Dienstkonto wird in Azure Active Directory auch als *Dienstprinzipal* bezeichnet. Die Authentifizierung mithilfe eines solchen Kontos ist auch als Dienst-zu-Dienst-Szenario bekannt.

Für Dienst-zu-Dienst-Szenarien können Sie die aufgerufene API-App mithilfe von Azure Active Directory schützen und ein AAD-Dienstprinzipal-Autorisierungstoken bereitstellen, wenn Sie die API-App aufrufen. Sie können das Token anfordern, indem Sie die Client-ID und den geheimen Clientschlüssel aus der AAD-Anwendung bereitstellen. Es ist kein spezieller Azure-Code erforderlich, wie er beispielsweise für das Behandeln des Mobile Services-Zumo-Tokens benötigt wurde. Ein Beispiel für dieses Szenario mithilfe von ASP.NET-API-Apps finden Sie in dem Tutorial [Dienstprinzipalauthentifizierung für API-Apps in Azure App Service](app-service-api-dotnet-service-principal-auth.md).

Wenn Sie ein Dienst-zu-Dienst-Szenario ohne App Service-Authentifizierung behandeln möchten, können Sie Clientzertifikate oder Standardauthentifizierung verwenden. Informationen zu Clientzertifikaten in Azure finden Sie unter [Konfigurieren von gegenseitiger TLS-Authentifizierung für Web-Apps](../app-service-web/app-service-web-configure-tls-mutual-auth.md). Informationen zum Konfigurieren einer Standardauthentifizierung in ASP.NET finden Sie unter [Authentication Filters in ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/authentication-filters).

Dienstkontoauthentifizierung von einer App Service-Logik-App zu einer API-App ist ein Sonderfall, der in [Verwenden der in App Service gehosteten benutzerdefinierten API mit Logik-Apps](../app-service-logic/app-service-logic-custom-hosted-api.md) erklärt wird.

## Weitere Informationen

Weitere Informationen zu Authentifizierungs- und Autorisierungsdiensten in Azure App Service finden Sie unter [Expanding App Service Authentication/Authorization](/blog/announcing-app-service-authentication-authorization/).

## Nächste Schritte

In diesem Artikel wurden Authentifizierungs- und Autorisierungsfeatures von App Service-API-Apps erläutert.

Wenn Sie der Erste-Schritte-Sequenz der Tutorials für ASP.NET- und API-Apps folgen, probieren Sie diese Features im nächsten Tutorial aus, [Benutzerauthentifizierung für API-Apps in Azure App Service](app-service-api-dotnet-user-principal-auth.md).

Weitere Informationen zur Verwendung von Knoten und Java in Azure App Service finden Sie unter [Node.js Developer Center](/develop/nodejs/) und [Java Developer Center](/develop/java/).

<!---HONumber=AcomDC_1217_2015-->