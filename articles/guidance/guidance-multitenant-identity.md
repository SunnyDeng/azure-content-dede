<properties
   pageTitle="Identitätsverwaltung für mehrmandantenfähige Anwendungen | Microsoft Azure"
   description="Bewährte Methoden für die Authentifizierung, Autorisierung und Identitätsverwaltung mehrmandantenfähiger Apps."
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Identitätsverwaltung für mehrmandantenfähige Anwendungen in Microsoft Azure

In dieser Reihe werden bewährte Methoden für mehrere Instanzen bei Verwendung von Azure AD für Authentifizierung und Identität beschrieben.

Beim Erstellen einer mehrmandantenfähigen Anwendung besteht eine der ersten Herausforderungen in der Verwaltung der Benutzeridentitäten, da jeder Benutzer nun zu einem Mandanten gehört. Beispiel:

- Benutzer melden sich bei der App mit den Anmeldeinformationen ihrer Organisation an.
- Benutzer sollten Zugriff auf Daten der eigenen Organisation haben, dürfen jedoch nicht auf Daten anderer Mandanten zugreifen.
- Eine Organisation kann sich für eine Anwendung anmelden und Organisationsmitgliedern dann Anwendungsrollen zuweisen.

Azure Active Directory (Azure AD) verfügt über einige hervorragende Funktionen, die alle diese Szenarios unterstützen.

Die Artikelreihe begleitet die vollständige [Ende-zu-Ende-Implementierung][tailspin] einer mehrmandantenfähigen Anwendung. Die Artikel fassen zusammen, was wir im Verlauf der Erstellung dieser Anwendung gelernt haben. Informationen zu den ersten Schritten mit der Anwendung finden Sie unter [How to run the Tailspin Surveys sample application](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md) (Ausführen der Tailspin-Anwendung Surveys).


## Inhaltsverzeichnis

- [Einführung](guidance-multitenant-identity-intro.md)
- [Informationen über die Tailspin-Anwendung Surveys](guidance-multitenant-identity-tailspin.md)
- [Authentifizierung mit Azure AD](guidance-multitenant-identity-authenticate.md)
- [Arbeiten mit anspruchsbasierten Identitäten](guidance-multitenant-identity-claims.md)
- [Anmeldung und Onboarding von Mandanten](guidance-multitenant-identity-signup.md)
- [Definieren und Verwalten von Anwendungsrollen](guidance-multitenant-identity-app-roles.md)
- [Autorisierung](guidance-multitenant-identity-authorize.md)
- [Sichern einer Back-End-Web-API](guidance-multitenant-identity-web-api.md)
- [Zwischenspeichern von OAuth2-Zugriffstoken](guidance-multitenant-identity-token-cache.md)
- [Verbund mit Active Directory-Verbunddiensten (AD FS) eines Kunden](guidance-multitenant-identity-adfs.md)
- [Abrufen von Zugriffstoken aus Azure AD mithilfe der Clientassertion](guidance-multitenant-identity-client-assertion.md)
- [Verwenden des Schlüsseltresors zum Schützen geheimer Schlüssel für Anwendungen](guidance-multitenant-identity-keyvault.md)

[tailspin]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->