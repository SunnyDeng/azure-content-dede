<properties
   pageTitle="Informationen zur Tailspin-Anwendung „Surveys“ | Microsoft Azure"
   description="Übersicht über die Tailspin-Anwendung „Surveys“"
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

# Informationen zur Tailspin-Anwendung „Surveys“

Dieser Artikel ist [Teil einer Reihe]. Es gibt auch eine vollständige [Beispielanwendung], die diese Reihe begleitet.

Tailspin ist ein fiktives Unternehmen, das eine SaaS-Anwendung mit dem Namen „Surveys“ entwickelt. Diese Anwendung ermöglicht Organisationen das Erstellen und Veröffentlichen von Online-Umfragen.

- Eine Organisation kann sich für die Anwendung registrieren.
- Nach der Registrierung der Organisation können sich Benutzer bei der Anwendung mit ihren Organisationsanmeldeinformationen anmelden.
- Benutzer können Umfragen erstellen, bearbeiten und veröffentlichen.

> [AZURE.NOTE] Informationen zu den ersten Schritten mit der Anwendung finden Sie unter [Ausführen der Anwendung „Surveys“].

Dieser Screenshot zeigt die Seite „Edit Survey“:

![Umfrage bearbeiten](media/guidance-multitenant-identity/edit-survey.png)

Beachten Sie, dass der Benutzer mit seiner Organisationsidentität angemeldet ist: `bob@contoso.com`.

Benutzer können innerhalb desselben Mandanten von anderen Benutzern erstellte Umfragen anzeigen.

![Mandantenumfragen](media/guidance-multitenant-identity/tenant-surveys.png)

Wenn ein Benutzer eine Umfrage erstellt, kann er andere Personen als Teilnehmer zur Umfrage einladen. Teilnehmer können die Umfrage bearbeiten, sie aber nicht löschen oder bereitstellen.

![Teilnehmer hinzufügen](media/guidance-multitenant-identity/add-contributor.png)

Ein Benutzer kann Teilnehmer aus anderen Mandanten hinzufügen, was die mandantenübergreifende Freigabe von Ressourcen ermöglicht. In diesem Screenshot fügt Bob (`bob@contoso.com`) Alice (`alice@fabrikam.com`) als Teilnehmerin zu einer Umfrage hinzu, die Bob erstellt hat.

Wenn sich Alice anmeldet, sieht sie die Umfrage unter „Surveys I can contribute to“.

![Teilnehmer an Umfrage](media/guidance-multitenant-identity/contributor.png)

Beachten Sie, dass sich Alice bei ihrem eigenen Mandanten und nicht als Gast des Mandanten „Contoso“ anmeldet. Alice verfügt ausschließlich für diese Umfrage über Teilnehmerberechtigungen. Sie kann keine anderen Umfragen im Mandanten „Contoso“ anzeigen.

## Architektur

Die Anwendung „Surveys“ besteht aus einem Web-Front-End und einem Web-API-Back-End. Beide werden mithilfe von [ASP.NET Core 1.0] implementiert.

Die Webanwendung verwendet Azure Active Directory (Azure AD) zur Authentifizierung von Benutzern. Die Webanwendung ruft auch Azure AD auf, um OAuth 2-Zugriffstoken für die Web-API zu erhalten. Zugriffstoken werden in Azure Redis Cache zwischengespeichert. Der Cache ermöglicht, dass sich mehrere Instanzen (z. B. in einer Serverfarm) denselben Tokencache teilen.

![Architektur](media/guidance-multitenant-identity/architecture.png)

<!-- Links -->
[Teil einer Reihe]: guidance-multitenant-identity.md
[Ausführen der Anwendung „Surveys“]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md
[ASP.NET Core 1.0]: https://docs.asp.net/en/latest/
[Beispielanwendung]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->