<properties
   pageTitle="Azure Active Directory-Authentifizierungsbibliotheken | Microsoft Azure"
   description="Die Azure AD-Authentifizierungsbibliothek (ADAL) ermöglicht es Entwicklern von Clientanwendungen, auf einfache Weise eine Benutzerauthentifizierung mit Active Directory (Cloud oder lokal) bereitzustellen und anschließend Zugriffstoken zur Absicherung von API-Aufrufen abzurufen."
   services="active-directory"
   documentationCenter=""
   authors="msmbaldwin"
   manager="mbaldwin"
   editor="mbaldwin" />
<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/17/2015"
   ms.author="mbaldwin" />

# Azure Active Directory-Authentifizierungsbibliotheken

Die Azure AD-Authentifizierungsbibliothek (ADAL) ermöglicht es Entwicklern von Clientanwendungen, auf einfache Weise eine Benutzerauthentifizierung mit Active Directory (Cloud oder lokal) bereitzustellen und anschließend Zugriffstoken zur Absicherung von API-Aufrufen abzurufen. ADAL verfügt über zahlreiche Features zur Vereinfachung der Authentifizierung für Entwickler. Dazu gehören beispielsweise die Unterstützung von asynchronen Methoden, ein konfigurierbarer Tokencache zum Speichern von Zugriffstoken und Aktualisierungstoken, eine automatische Tokenaktualisierung bei Ablauf eines Zugriffstokens, sofern ein Aktualisierungstoken verfügbar ist, und vieles mehr. Da durch ADAL die meisten komplexen Aspekte abgedeckt werden, können sich Entwickler auf die Geschäftslogik in ihren Anwendungen konzentrieren und auf einfache Weise Ressourcen sichern, ohne Sicherheitsexperten sein zu müssen.

ADAL steht auf einer Vielzahl von Plattformen zur Verfügung.

|Plattform|Paketname|Client/Server|Download|Quellcode|Beispiel|
|---|---|---|---|---|---|
|.NET Client, Windows Store, Windows Phone (8.1)|Active Directory-Authentifizierungsbibliothek (ADAL) für .NET|Client|[Microsoft.IdentityModel.Clients.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory)|[ADAL für .NET (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet)||
|JavaScript|Active Directory-Authentifizierungsbibliothek (ADAL) für JavaScript|Client|[ADAL für JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[ADAL für JavaScript (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-js)|[SinglePageApp-DotNet (Github)](https://github.com/AzureADSamples/SinglePageApp-DotNet)|
|OS X, iOS|Active Directory-Authentifizierungsbibliothek (ADAL) für Objective-C|Client|[ADAL für Objective-C (CocoaPods)](https://cocoapods.org/?q=adal%20io)|[ADAL für Objective-C (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-objc)|[NativeClient-iOS (Github)](https://github.com/AzureADSamples/NativeClient-iOS)|
|Android|Active Directory-Authentifizierungsbibliothek (ADAL) für Android|Client|[ADAL für Android (The Central Repository)](http://search.maven.org/remotecontent?filepath=com/microsoft/aad/adal/)|[ADAL für Android (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-android)|[NativeClient-Android (Github)](https://github.com/AzureADSamples/NativeClient-Android)|
|Node.js|Active Directory-Authentifizierungsbibliothek (ADAL) für Node.js|Client|[ADAL für Node.js (npm)](https://www.npmjs.com/package/adal-node)|[ADAL für Node.js (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-nodejs)|[WebAPI-Nodejs (Github)](https://github.com/AzureADSamples/WebAPI-Nodejs)|
|Node.js|Windows Azure Active Directory Passport-Middleware zur Authentifizierung für Node|Client|[Azure Active Directory Passport für Node.js (npm)](https://www.npmjs.com/package/passport-azure-ad)|[Azure Active Directory für Node.js (Github)](https://github.com/AzureAD/passport-azure-ad)||
|Java|Active Directory-Authentifizierungsbibliothek (ADAL) für Java|Client|[ADAL für Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)|[ADAL für Java (Github)](https://github.com/AzureAD/azure-activedirectory-library-for-java)||
|.NET|Identitätsmodellerweiterungen für Microsoft .NET Framework 4.5|Server|[Microsoft.IdentityModel.Protocol.Extensions (NuGet)](https://www.nuget.org/packages/Microsoft.IdentityModel.Protocol.Extensions)|[Azure AD-Identitätsmodellerweiterungen für .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|JSON-Webtokenhandler für Microsoft .Net Framework 4.5|Server|[System.IdentityModel.Tokens.Jwt (NuGet)](https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt)|[Azure AD-Identitätsmodellerweiterungen für .NET (Github)](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet)||
|.NET|OWIN-Middleware, die einer Anwendung das Verwenden von Microsoft-Technologie für die Authentifizierung ermöglicht.|Server|[Microsoft.Owin.Security.ActiveDirectory (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.ActiveDirectory/)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)||
|.NET|OWIN-Middleware, die einer Anwendung das Verwenden von OpenIDConnect für die Authentifizierung ermöglicht.|Server|[Microsoft.Owin.Security.OpenIdConnect (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|[WebApp-OpenIDConnecty-DotNet (Github)](https://github.com/AzureADSamples/WebApp-OpenIDConnect-DotNet)|
|.NET|OWIN-Middleware, die einer Anwendung das Verwenden von WS-Federation für die Authentifizierung ermöglicht.|Server|[Microsoft.Owin.Security.WsFederation (NuGet)](https://www.nuget.org/packages/Microsoft.Owin.Security.WsFederation)|[OWIN (CodePlex)](http://katanaproject.codeplex.com)|[WebApp-WSFederation-DotNet (Github)](https://github.com/AzureADSamples/WebApp-WSFederation-DotNet)|

## Szenarien

Nachfolgend werden die drei gängigsten Szenaren vorgestellt, in denen ADAL für die Authentifizierung verwendet werden kann.

### Authentifizieren von Benutzern einer Clientanwendung bei einer Remoteressource

In diesem Szenario verwendet ein Entwickler einen Client (z. B. eine WPF-Anwendung), der auf eine durch Azure AD geschützte Remoteressource (z. B. eine Web-API) zugreifen muss. Er verfügt über ein Azure-Abonnement, weiß, wie die Downstream-Web-API aufgerufen wird, und kennt den Azure AD-Mandanten, den die Web-API verwendet. ADAL kann daher zur Vereinfachung der Authentifizierung bei Azure AD eingesetzt werden – durch vollständiges Delegieren der Authentifizierung an ADAL oder durch explizites Verarbeiten der Benutzeranmeldeinformationen. Dank ADAL ist es einfach, den Benutzer zu authentifizieren, ein Zugriffstoken und ein Aktualisierungstoken aus Azure AD abzurufen und das Zugriffstoken dann zu verwenden, um Anforderungen an die Web-API zu senden.

Ein Codebeispiel, in dem dieses Szenario mithilfe von Authentifizierung für Azure AD veranschaulicht wird, finden Sie unter [Systemeigene Client-WPF-Anwendung für Web-API](https://github.com/azureadsamples/nativeclient-dotnet).

### Authentifizieren einer Serveranwendung bei einer Remoteressource

In diesem Szenario verwendet ein Entwickler eine Anwendung, die auf einem Server ausgeführt wird und auf eine durch Azure AD geschützte Remoteressource (z. B. eine Web-API) zugreifen muss. Er verfügt über ein Azure-Abonnement, weiß, wie der Downstreamdienst aufgerufen wird, und kennt den Azure AD-Mandanten, den die Web-API verwendet. Er kann daher ADAL zum Ermöglichen der Authentifizierung mit Azure AD verwenden, indem die Anmeldeinformationen der Anwendung explizit verarbeitet werden. ADAL vereinfacht das Abrufen eines Tokens aus Azure AD, indem die Clientanmeldeinformationen der Anwendung verwendet werden und dann das Token verwendet wird, um Anforderungen an die Web-API zu senden. ADAL übernimmt außerdem die Verwaltung der Lebensdauer des Zugriffstokens, indem es zwischengespeichert und bei Bedarf erneuert wird. Ein Codebeispiel, das dieses Szenario veranschaulicht, finden Sie unter [Konsolenanwendung für Web-API](https://github.com/AzureADSamples/Daemon-DotNet).

### Authentifizieren einer Serveranwendung im Auftrag eines Benutzers, um auf eine Remoteressource zuzugreifen

In diesem Szenario verwendet ein Entwickler eine Anwendung, die auf einem Server ausgeführt wird und auf eine durch Azure AD geschützte Remoteressource (z. B. eine Web-API) zugreifen muss. Die Anforderung muss außerdem im Auftrag eines Benutzers in Azure AD vorgenommen werden. Der Entwickler verfügt über ein Azure-Abonnement, weiß, wie die Downstream-Web-API aufgerufen wird, und kennt den Azure AD-Mandanten, den der Dienst verwendet. Sobald der Benutzer für die Webanwendung authentifiziert ist, kann die Anwendung einen Autorisierungscode für den Benutzer aus Azure AD abrufen. Die Webanwendung kann dann ADAL verwenden, um im Auftrag eines Benutzers ein Zugriffstoken und ein Aktualisierungstoken abzurufen, wobei der Autorisierungscode und die Clientanmeldeinformationen verwendet werden, die der Anwendung aus Azure AD zugeordnet sind. Sobald die Webanwendung im Besitz des Zugriffstokens ist, kann sie bis zum Ablauf des Tokens die Web-API aufrufen. Nach Ablauf des Tokens kann die Webanwendung über ADAL mit dem zuvor empfangenen Aktualisierungstoken ein neues Zugriffstoken abrufen.


## Siehe auch

[Entwicklerhandbuch zu Azure Active Directory](active-directory-developers-guide.md)

[Authentifizierungsszenarien für Azure Active Directory](active-directory-authentication-scenarios.md)

[Azure Active Directory-Codebeispiele](active-directory-code-samples.md)

<!---HONumber=AcomDC_1125_2015-->