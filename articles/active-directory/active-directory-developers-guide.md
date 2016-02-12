<properties
   pageTitle="Entwicklerhandbuch zu Azure Active Directory | Microsoft Azure"
   description="Dieser Artikel enthält eine umfassende Übersicht über die für Entwickler wichtigen Ressourcen zu Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="02/01/2016"
   ms.author="mbaldwin"/>


# Entwicklerhandbuch zu Azure Active Directory

## Übersicht
Als Plattform vom Typ "Identity Management as a Service (IDMaaS)" stellt Azure Active Directory für Entwickler eine effektive Möglichkeit zum Integrieren der Identitätsverwaltung in ihre Anwendungen dar. Die folgenden Artikel enthalten Übersichten über die Implementierung sowie wichtige Features von Azure Active Directory. Wir empfehlen Ihnen, die Artikel in der angegebenen Reihenfolge zu lesen. Wenn Sie möchten, können Sie aber auch direkt mit [Erste Schritte](#getting-started) beginnen.


1. [Vorteile der Integration in Azure Active Directory](active-directory-how-to-integrate.md): Erfahren Sie, warum die Integration in Azure Active Directory die beste Lösung für eine sichere Anmeldung und Autorisierung ist.

1. [Active Directory-Authentifizierungsszenarien](active-directory-authentication-scenarios.md): Nutzen Sie die vereinfachte Authentifizierung von Azure Active Directory, um eine Anmeldung für Ihre Anwendung bereitzustellen.

1. [Integrieren von Anwendungen in Azure Active Directory](active-directory-integrating-applications.md): Weitere Informationen, wie Sie Anwendungen in Azure Active Directory hinzufügen, aktualisieren und entfernen, sowie zu den Brandingrichtlinien für integrierte Apps.

1. [Azure Active Directory Graph-API](active-directory-graph-api.md): Verwenden Sie die Azure Active Directory Graph-API, um über REST-API-Endpunkte programmgesteuert auf Azure Active Directory zuzugreifen. Beachten Sie, dass die Azure AD Graph-API auch über [Microsoft Graph](https://graph.microsoft.io/) zugänglich ist, eine einheitliche API, die Zugriff auf mehrere Microsoft Clouddienst-APIs über einen einzelnen Endpunkt für die REST-API und mit einem einzelnen Zugriffstoken ermöglicht.

1. [Azure Active Directory-Authentifizierungsbibliotheken](active-directory-authentication-libraries.md): Authentifizieren Sie Benutzer auf einfache Weise, indem Sie Zugriffstoken über die Azure AD-Authentifizierungsbibliotheken für .NET, JavaScript, Objective-C, Android und weitere abrufen.


## Erste Schritte

Diese Tutorials sind auf verschiedene Plattformen ausgelegt und ermöglichen Ihnen einen schnellen Einstieg in die Entwicklung mit Azure Active Directory. Voraussetzung ist, dass Sie über einen [Azure Active Directory-Mandanten](active-directory-howto-tenant.md) verfügen.

### Mobile Anwendung und PC-Anwendung – Schnellstartanleitungen

|[![iOS](./media/active-directory-developers-guide/ios.png)](active-directory-devquickstarts-ios.md)|[![Android](./media/active-directory-developers-guide/android.png)](active-directory-devquickstarts-android.md)|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-dotnet.md)| [![Windows Phone](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsphone.md)|[![Windows Store](./media/active-directory-developers-guide/windows.png)](active-directory-devquickstarts-windowsstore.md)|[![Xamarin](./media/active-directory-developers-guide/xamarin.png)](active-directory-devquickstarts-xamarin.md)|[![Cordova](./media/active-directory-developers-guide/cordova.png)](active-directory-devquickstarts-cordova.md)
|:--:|:--:|:--:|:--:|:--:|:--:|:--:
|[iOS](active-directory-devquickstarts-ios.md)|[Android](active-directory-devquickstarts-android.md)|[.NET](active-directory-devquickstarts-dotnet.md)|[Windows Phone](active-directory-devquickstarts-windowsphone.md)|[Windows Store](active-directory-devquickstarts-windowsstore.md)|[Xamarin](active-directory-devquickstarts-xamarin.md)|[Cordova](active-directory-devquickstarts-cordova.md)

### Webanwendung – Schnellstartanleitungen

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapp-dotnet.md)|[![Java](./media/active-directory-developers-guide/java.png)](active-directory-devquickstarts-webapp-java.md)|[![JavaScript](./media/active-directory-developers-guide/javascript.png)](active-directory-devquickstarts-angular.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-openidconnect-nodejs.md)
|:--:|:--:|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapp-dotnet.md)|[Java](active-directory-devquickstarts-webapp-java.md)|[JavaScript](active-directory-devquickstarts-angular.md)|[Node.js](active-directory-devquickstarts-openidconnect-nodejs.md)

### Web-API – Schnellstartanleitungen

|[![.NET](./media/active-directory-developers-guide/net.png)](active-directory-devquickstarts-webapi-dotnet.md)|[![Node.js](./media/active-directory-developers-guide/nodejs.png)](active-directory-devquickstarts-webapi-nodejs.md)
|:--:|:--:|
|[.NET](active-directory-devquickstarts-webapi-dotnet.md)|[Node.js](active-directory-devquickstarts-webapi-nodejs.md)

### Abfragen des Verzeichnisses – Schnellstartanleitung

| [![.NET](./media/active-directory-developers-guide/graph.png)](active-directory-graph-api-quickstart.md)|
|:--:|
|[Graph-API](active-directory-graph-api-quickstart.md)|

## Vorgehensweisen

In diesen Artikeln wird beschrieben, wie Sie mit Azure Active Directory bestimmte Aufgaben ausführen:

- [Abrufen eines Azure Active Directory-Mandanten](active-directory-howto-tenant.md)
- [Auflisten Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](active-directory-app-gallery-listing.md)
- [Grundlegendes zum Azure Active Directory-Anwendungsmanifest](active-directory-application-manifest.md)
- [Erstellen einer App mit Office 365-APIs](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Übertragen von Web-Apps für Office 365 an das Verkäuferdashboard (in englischer Sprache)](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard)
- [Vorschau: Erstellen von Apps, bei denen sich Benutzer sowohl mit persönlichen als auch mit Arbeits- oder Schulkonten anmelden können](active-directory-appmodel-v2-overview.md)
- [Vorschau: Erstellen von Apps, die Kunden registrieren und anmelden](../active-directory-b2c/active-directory-b2c-overview.md)


## Referenz

Diese Artikel enthalten grundlegende Referenzen zu REST- und Authentifizierungsbibliothek-APIs, Protokollen, Fehlern, Codebeispielen und Endpunkten.

###  Support
- [Mit Tags markierte Fragen](http://stackoverflow.com/questions/tagged/azure-active-directory): Sie finden Azure Active Directory-Lösungen bei Stack Overflow, indem Sie nach den Tags [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) und [adal](http://stackoverflow.com/questions/tagged/adal) suchen.

### Code

- [Open-Source-Bibliotheken für Azure Active Directory](http://github.com/AzureAD): Am einfachsten finden Sie die Quelle einer Bibliothek in unserer [Bibliotheksliste](active-directory-authentication-libraries.md).

- [Azure Active Directory-Beispiele](https://github.com/azure-samples?query=active-directory): Die einfachste Möglichkeit zum Navigieren in der Liste mit den Beispielen bietet der [Index für die Codebeispiele](active-directory-code-samples.md).


### Graph-API

- [Graph-API-Referenz](https://msdn.microsoft.com/library/azure/hh974476.aspx): Hier finden Sie die REST-Referenz für die Azure Active Directory Graph-API. [Sehen Sie sich die interaktive Umgebung für die Graph-API-Referenz an](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- [Graph-API-Berechtigungsbereiche](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-permission-scopes): Hier werden die OAuth 2.0-Berechtigungsbereiche beschrieben, mit denen gesteuert wird, welchen Zugriff eine App auf die Verzeichnisdaten für einen Mandanten hat.

### Authentifizierungsbibliotheken

- [.NET](https://msdn.microsoft.com/library/azure/mt417579.aspx): Dokumentation für die .NET-Authentifizierungsbibliothek.

### Authentifizierungsprotokolle

- [SAML 2.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn195591.aspx): Mit dem SAML 2.0-Protokoll können Anwendungen für Benutzer das einmalige Anmelden bereitstellen.


- [OAuth 2.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn645545.aspx): Mit dem OAuth 2.0-Protokoll können Sie den Zugriff auf Webanwendungen und Web-APIs in Ihrem Azure Active Directory-Mandanten autorisieren.


- [OpenID Connect 1.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn645541.aspx): Mit dem OpenID Connect 1.0-Protokoll wird OAuth 2.0 um die Verwendung als Authentifizierungsprotokoll erweitert.


- [WS-Federation 1.2-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn903702.aspx): Das WS-Federation 1.2-Protokoll ist in der Web Services Federation-Spezifikation, Version 1.2, festgelegt.

- [Unterstützte Token und Anspruchstypen](active-directory-token-and-claims.md): Verwenden Sie dieses Handbuch, um die Ansprüche in SAML 2.0- und JSON-Webtoken (JWT) zu verstehen und auszuwerten.

## Videos

### Build 2015

In diesen Übersichtspräsentationen zur Entwicklung von Apps mithilfe von Azure Active Directory kommen Mitglieder des Entwicklungsteams zu Wort. In den Präsentationen werden grundlegende Themen abgedeckt, darunter IDMaaS, Authentifizierung, Identitätsverbund und einmaliges Anmelden.

- [Azure Active Directory: Identitätsverwaltung als Dienst für moderne Anwendungen](https://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications/)
- [Entwickeln von modernen Webanwendungen mit Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory/)
- [Entwickeln von modernen systemeigenen Anwendungen mit Azure Active Directory](https://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory/)

### Azure Friday
[Azure Friday](https://azure.microsoft.com/documentation/videos/azure-friday/) ist eine Videoreihe, bei der jeden Freitag neue kurze Videos veröffentlicht werden. Dabei handelt es sich um kurze Interviews (10 bis 15 Minuten) mit Experten, bei denen es um unterschiedliche Azure-Themen geht. Verwenden Sie die Dienstfilterfunktion auf der Seite, um alle Azure Active Directory-Videos anzuzeigen.

- [Azure-Identität 101](https://azure.microsoft.com/documentation/videos/azure-identity-basics/)
- [Azure-Identität 102](https://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)
- [Azure-Identität 103](https://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)

## Soziale Netzwerke

- [Active Directory-Teamblog](http://blogs.technet.com/b/ad/): Hier können Sie sich über die neuesten Entwicklungen der Azure Active Directory-Welt informieren.

- [Azure Active Directory Graph-Teamblog](http://blogs.msdn.com/b/aadgraphteam): Hier werden Azure Active Directory-Informationen speziell zur Graph-API bereitgestellt.

- [Cloudidentität](http://www.cloudidentity.net): Hier finden Sie Informationen zu IMaaS (Identity Management as a Service) von einem erfahrenen Azure Active Directory-PM.

- [Azure Active Directory auf Twitter](https://twitter.com/azuread): Azure Active Directory-Ankündigungen in höchstens 140 Zeichen.

<!---HONumber=AcomDC_0204_2016-->