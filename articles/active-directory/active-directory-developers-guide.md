<properties
   pageTitle="Entwicklerhandbuch zu Azure Active Directory"
   description="Enthält eine umfassende Anleitung für auf Entwickler ausgerichtete Ressourcen für Azure Active Directory."
   services="active-directory"
   documentationCenter="dev-center-name"
   authors="msmbaldwin"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/12/2015"
   ms.author="mbaldwin"/>


# Entwicklerhandbuch zu Azure Active Directory

## Übersicht
Als Plattform vom Typ „Identity Management as a Service (IDMaaS)“ stellt Azure Active Directory für Entwickler eine effektive Möglichkeit zum Integrieren der Identitätsverwaltung in ihre Anwendungen dar. Die folgenden Artikel enthalten Übersichten über die Implementierung sowie wichtige Features von Azure Active Directory. Wir empfehlen Ihnen, die Artikel in der richtigen Reihenfolge zu lesen. Wenn Sie möchten, können Sie aber auch direkt mit [Erste Schritte](#getting-started) beginnen.


1. **[Vorteile der Integration in Azure AD](active-directory-how-to-integrate.md)**: Erfahren Sie, warum die Integration in Azure Active Directory die beste Lösung für eine sichere Anmeldung und Autorisierung ist.

1. **[Verwenden von Azure AD für die Anmeldung](active-directory-authentication-scenarios.md)**: Nutzen Sie die vereinfachte Authentifizierung von Azure Active Directory, um die Anmeldung für Ihre Anwendung bereitzustellen.

1. **[Abfragen des Verzeichnisses](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: Verwenden Sie die Azure Active Directory Graph-API, um über REST-API-Endpunkte programmgesteuert auf Azure AD zuzugreifen.

1. **[Grundlagen des Anwendungsmodells](https://msdn.microsoft.com/library/azure/dn151122.aspx)**: Hier erhalten Sie Informationen zur Registrierung Ihrer Anwendung und zu den Brandingrichtlinien für mehrinstanzenfähige Anwendungen.

1. **[Bibliotheken](https://msdn.microsoft.com/library/azure/dn151135.aspx)**: Nutzen Sie die einfache Authentifizierung von Benutzern, um Zugriffstoken über die Azure-Authentifizierungsbibliotheken zu erhalten.

AAD-Übersichten von der Konferenz Build 2015 finden Sie unten im Abschnitt [Videos](#videos).


## Erste Schritte

Diese Lernprogramme sind für mehrere Plattformen ausgelegt und ermöglichen es Ihnen, schnell mit der Entwicklung mit Azure Active Directory zu beginnen. Voraussetzung ist, dass Sie über einen [Azure Active Directory-Mandanten](active-directory-howto-tenant.md) verfügen.

#### Mobile Anwendung oder PC-Anwendung – Schnellstartanleitungen

- [iOS](active-directory-devquickstarts-ios.md)
- [Android](active-directory-devquickstarts-android.md)
- [.NET](active-directory-devquickstarts-dotnet.md)
- [Windows Phone](active-directory-devquickstarts-windowsphone.md)
- [Windows Store](active-directory-devquickstarts-windowsstore.md)
- [Xamarin](active-directory-devquickstarts-xamarin.md)
- [Cordova](active-directory-devquickstarts-cordova.md)


####Webanwendung oder Web-API – Schnellstartanleitungen

- [.NET Web-App](active-directory-devquickstarts-webapp-dotnet.md)
- [.NET Web-API](active-directory-devquickstarts-webapi-dotnet.md)
- [JavaScript](active-directory-devquickstarts-angular.md)
- [Node.js](active-directory-devquickstarts-webapi-nodejs.md)


## Anleitungen zur Vorgehensweise

In diesen Artikeln wird beschrieben, wie Sie mit Azure Active Directory (AD) bestimmte Aufgaben erfüllen.

- [Erhalten eines Azure AD-Mandanten](active-directory-howto-tenant.md)
- [Auflisten Ihrer Anwendung im Azure AD-Anwendungskatalog](active-directory-app-gallery-listing.md)
- [Erste Schritte mit Office 365-APIs in Apps](https://msdn.microsoft.com/office/office365/howto/getting-started-Office-365-APIs)
- [Submit Web apps for Office 365 to the Seller Dashboard](https://msdn.microsoft.com/office/office365/howto/submit-web-apps-seller-dashboard) (Senden von Web-Apps für Office 365 mit dem Verkäuferdashboard, in englischer Sprache)
- [Vorschau: Erstellen von Apps, bei denen sich Benutzer sowohl mit persönlichen als auch mit Arbeits- oder Schulkonten anmelden können](active-directory-appmodel-v2-overview.md)


## Referenz

Diese Artikel enthalten grundlegende Referenzen zu REST- und Authentifizierungsbibliothek-APIs, Protokollen, Fehlern, Codebeispielen und Endpunkten.

###  Support
- **[Support](http://stackoverflow.com/questions/tagged/azure-active-directory)**: Sie finden Azure AD-Lösungen bei Stack Overflow, indem Sie nach den Tags [azure-active-directory](http://stackoverflow.com/questions/tagged/azure-active-directory) und [adal](http://stackoverflow.com/questions/tagged/adal) suchen.

### Code

- **[Open-Source-Bibliotheken für Azure AD](http://github.com/AzureAD)**: Am einfachsten finden Sie die Quelle einer Bibliothek in unserer [Bibliotheksliste](https://msdn.microsoft.com/library/azure/dn151135.aspx).

- **[Azure AD-Beispiele](http://github.com/AzureADSamples)**: Die einfachste Möglichkeit zum Navigieren in der Liste mit den Beispielen ist der [Index für die Codebeispiele](active-directory-code-samples.md).


### Graph-API

- **[Graph-API-Referenz](https://msdn.microsoft.com/library/azure/hh974476.aspx)**: Hier finden Sie die REST-Referenz für die Azure Active Directory Graph-API. [Sehen Sie sich die neue interaktive Umgebung für die Graph-API-Referenz an](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog).

- **[Graph-API-Berechtigungsbereiche](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/graph-api-permission-scopes)**: Hier werden die OAuth 2.0-Berechtigungsbereiche beschrieben, mit denen gesteuert wird, welchen Zugriff eine App auf die Verzeichnisdaten für einen Mandanten hat.


### Authentifizierungsprotokolle

- **[SAML 2.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn195591.aspx)**: Mit dem SAML 2.0-Protokoll können Anwendungen für Benutzer das einmalige Anmelden bereitstellen.


- **[OAuth 2.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn645545.aspx)**: Mit dem OAuth 2.0-Protokoll können Sie den Zugriff auf Webanwendungen und Web-APIs in Ihrem Azure AD-Mandanten autorisieren.


- **[OpenID Connect 1.0-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn645541.aspx)**: Mit dem OpenID Connect 1.0-Protokoll wird OAuth 2.0 um die Verwendung als Authentifizierungsprotokoll erweitert.


- **[WS-Federation 1.2-Protokoll – Referenz](https://msdn.microsoft.com/library/azure/dn903702.aspx)**: Hier ist das WS-Federation 1.2-Protokoll so beschrieben, wie es in der Spezifikation Web Services Federation Version 1.2 angegeben ist.

- **[Unterstützte Sicherheitstoken und Ansprüche](active-directory-token-and-claims.md)**: Dies ist eine Anleitung zu den Grundlagen und der Auswertung der Ansprüche in SAML 2.0- und JWT-Tokens (JSON Web Tokens).

## Videos

### Build 2015

In diesen allgemeinen Präsentationen zur Entwicklung von Apps mit Azure Active Directory sehen Sie Referenten, die direkt im Entwicklungsteam arbeiten und für Sie wichtige Themen besprechen, z. B. IDMaaS, Authentifizierung, Identitätsverbund und einmaliges Anmelden (Single Sign-On, SSO).

- **[Azure Active Directory: Identitätsverwaltung als Dienst für moderne Anwendungen](http://azure.microsoft.com/documentation/videos/build-2015-azure-active-directory-identity-management-as-a-service-for-modern-applications)**
- **[Entwickeln von modernen Webanwendungen mit Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-web-applications-with-azure-active-directory)**
- **[Entwickeln von modernen systemeigenen Anwendungen mit Azure Active Directory](http://azure.microsoft.com/documentation/videos/build-2015-develop-modern-native-applications-with-azure-active-directory)**

### Azure Friday
[Azure Friday](http://azure.microsoft.com/documentation/videos/azure-friday/) ist eine Videoreihe, bei der regelmäßig am Freitag neue kurze Videos veröffentlicht werden. Dabei handelt es sich um kurze Interviews mit Experten, die 10 bis 15 Minuten lang sind und bei denen es um unterschiedliche Themen zu Azure geht. Verwenden Sie die Dienstfilterfunktion auf der Seite, um alle Azure Active Directory-Videos anzuzeigen.

- **[Azure-Identität 101](http://azure.microsoft.com/documentation/videos/azure-identity-basics/)**
- **[Azure-Identität 102](http://azure.microsoft.com/documentation/videos/azure-identity-creating-active-directory/)**
- **[Azure-Identität 103](http://azure.microsoft.com/documentation/videos/azure-identity-application-to-authenticate/)**

## Soziale Netzwerke

- **[Active Directory-Teamblog](http://blogs.technet.com/b/ad/)**: Hier können Sie sich immer über die neuesten Entwicklungen der Azure AD-Welt informieren.

- **[Azure AD Graph-Blog](http://blogs.msdn.com/b/aadgraphteam)**: Hier erhalten Sie Azure AD-Informationen, die speziell für die Graph-API gelten.

- **[Cloud-Identität](http://www.cloudidentity.net)**: Hier finden Sie Gedanken zu „Identity Management as a Service“ von einem erfahrenen Azure Active Directory-PM.

- **[Azure AD auf Twitter](https://twitter.com/azuread)**: Azure AD-Ankündigungen mit einer Länge von maximal 140 Zeichen.

<!---HONumber=August15_HO7-->