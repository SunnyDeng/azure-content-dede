<properties
   pageTitle="Identitätsverwaltung für mehrmandantenfähige Anwendungen | Microsoft Azure"
   description="Einführung in die Identitätsverwaltung für mehrmandantenfähige Anwendungen"
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

# Identitätsverwaltung für mehrmandantenfähige Anwendungen: Einführung

Dieser Artikel ist [Teil einer Reihe]. Es gibt auch eine vollständige [Beispielanwendung], die diese Reihe begleitet.

Angenommen, Sie schreiben eine SaaS-Unternehmensanwendung, die in der Cloud gehostet werden soll. Selbstredend hat die Anwendung Benutzer:

![Benutzer](media/guidance-multitenant-identity/users.png)

Doch diese Benutzer gehören zu Organisationen:

![Organisationsbenutzer](media/guidance-multitenant-identity/org-users.png)

Beispiel: Tailspin verkauft Abonnements für seine SaaS-Anwendung. Contoso und Fabrikam registrieren Sie sich für die App. Wenn Alice (`alice@contoso`) sich anmeldet, sollte die Anwendung wissen, dass Alice zu Contoso gehört.

- Alice _muss_ Zugriff auf Contoso-Daten haben.
- Alice _darf keinen_ Zugriff auf Fabrikam-Daten haben.

In dieser Anleitung wird gezeigt, wie Benutzeridentitäten in einer mehrmandantenfähigen Anwendung verwaltet werden, wobei [ Azure Active Directory][AzureAD] für die Anmeldung und Authentifizierung zuständig ist.

## Was bedeutet Mehrmandantenfähigkeit?

Ein _Mandant_ ist eine Gruppe von Benutzern. Bei einer SaaS-Anwendung ist der Mandant ein Abonnent oder Kunde der Anwendung. _Mehrmandantenfähigkeit_ ist eine Architektur, in der sich mehrere Mandanten die gleiche physische Instanz der App teilen. Obwohl Mandanten physische Ressourcen (z. B. virtuelle Computer oder Speicher) gemeinsam nutzen, ruft jeder Mandant eine eigene logische Instanz der App auf.

In der Regel werde Anwendungsdaten von den Benutzern innerhalb eines Mandanten, jedoch nicht mit anderen Mandanten gemeinsam genutzt.

![Mehrere Mandanten](media/guidance-multitenant-identity/multitenant.png)

Vergleichen Sie diese Architektur mit einer Architektur mit nur einem Mandanten, bei der jeder Mandant über eine dedizierte physische Instanz verfügt. Bei einer Architektur mit nur einem Mandanten fügen Sie Mandanten hinzu, indem Sie neue Instanzen der App in Betrieb nehmen.

![Einzelner Mandant](media/guidance-multitenant-identity/single-tenant.png)

### Mehrmandantenfähigkeit und horizontale Skalierung

Um eine Skalierung in der Cloud zu erreichen, ist es üblich, mehrere physische Instanzen hinzuzufügen. Dies wird als _horizontale Skalierung_ bzw. _horizontales Hochskalieren_ bezeichnet. Nehmen wir als Beispiel eine Web-App. Um mehr Datenverkehr zu bewältigen, können Sie weitere Server-VMs hinzufügen und sie hinter einem Load Balancer platzieren. Auf jeder VM wird eine separate physische Instanz der Web-App ausgeführt.

![Lastenausgleich für eine Website](media/guidance-multitenant-identity/load-balancing.png)

Jede Anforderung kann an eine beliebige Instanz weitergeleitet werden. Zusammen fungiert das System als einzelne logische Instanz. Sie können, ohne die Benutzer zu beeinträchtigen, eine VM in oder außer Betrieb nehmen. In dieser Architektur ist jede physische Instanz mehrmandantenfähig. Die Skalierung erfolgt durch Hinzufügen weiterer Instanzen. Wenn eine Instanz ausfällt, dürfte keiner der Mandanten beeinträchtigt werden.

## Identität in einer mehrmandantenfähigen App

In einer mehrmandantenfähigen App müssen Sie Benutzer im Kontext von Mandanten berücksichtigen.

**Authentifizierung**

- Benutzer melden Sie sich bei der App mit den Anmeldeinformationen für Ihre Organisation an. Sie müssen keine neuen Benutzerprofile für die App erstellen.
- Benutzer innerhalb derselben Organisation gehören zum selben Mandanten.
- Wenn sich ein Benutzer anmeldet, weiß die Anwendung, zu welchem Mandanten der Benutzer gehört.

**Autorisierung**

- Wenn ein Benutzer Aktionen (z. B. das Anzeigen einer Ressource) autorisiert, muss die App den Mandanten des Benutzers berücksichtigen.
- Benutzer können innerhalb der Anwendung Rollen wie „Admin“ oder „Standardbenutzer“ zugewiesen werden. Rollenzuweisungen müssen vom Kunden und nicht vom SaaS-Anbieter verwaltet werden.

**Beispiel:** Alice, eine Mitarbeiterin von Contoso, navigiert in ihrem Browser zur Anwendung und klickt auf die Schaltfläche „Anmelden“. Sie wird zu einer Anmeldeseite umgeleitet, auf der sie ihre Unternehmensanmeldeinformationen (Benutzername und Kennwort) eingibt. An dieser Stelle wird sie in der Anwendung als `alice@contoso.com` angemeldet. Die Anwendung erkennt auch, dass Alice eine Administratorbenutzerin dieser Anwendung ist. Da sie eine Administratorin ist, wird ihr eine Liste aller Ressourcen gezeigt, die zu Contoso gehören. Sie kann jedoch nicht Ressourcen von Fabrikam anzeigen, da sie nur in ihrem Mandanten Administratorin ist.

In dieser Anleitung untersuchen wir insbesondere den Einsatz von Azure AD für die Identitätsverwaltung.

- Wir setzen voraus, dass der Kunde Benutzerprofile (einschließlich Office 365- und Dynamics CRM-Mandanten) in Azure AD speichert.
- Kunden mit lokalem Active Directory (AD) können [Azure AD Connect][ADConnect] verwenden, um ihr lokales AD mit Azure AD zu synchronisieren.

Wenn AD Connect von einem Kunden mit lokalem AD Azure (aufgrund von IT-Unternehmensrichtlinien oder aus anderen Gründen) nicht verwendet werden darf, kann der SaaS-Anbieter über Active Directory-Verbunddienste (AD FS) einen Verbund mit dem AD des Kunden einrichten. Diese Option wird unter [Federating with a customer's AD FS] (Herstellen eines Verbunds mit den AD FS eines Kunden) beschrieben.

In dieser Anleitung werden andere Aspekte der Mehrmandantenfähigkeit wie Datenpartitionierung, mandantenbezogene Konfiguration usw. nicht berücksichtigt.

<!-- Links -->
[ADConnect]: ../active-directory/active-directory-aadconnect.md
[AzureAD]: https://azure.microsoft.com/documentation/services/active-directory/
[Teil einer Reihe]: guidance-multitenant-identity.md
[Federating with a customer's AD FS]: guidance-multitenant-identity-adfs.md
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->