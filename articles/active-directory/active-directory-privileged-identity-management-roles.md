<properties
   pageTitle="Azure Privileged Identity Management: Rollen"
   description="Erfahren Sie, welche Rollen mit der Erweiterung Azure Privileged Identity Management für privilegierte Identitäten verwendet werden."
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: Rollen

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

## Rollen aus Active Directory Azure, Office 365 und anderen Quellen

Azure PIM verwendet die folgenden Rollen als Standardadministratorrollen.

- Globaler Administrator
- Abrechnungsadministrator
- Dienstadministrator
- Benutzeradministrator
- Kennwortadministrator

Klicken Sie hier, um weitere Informationen über die Rollen aus Office 365, Exchange Online, SharePoint Online und Skype for Business zu erhalten.[Zuweisen von Administratorrollen in Office 365](https://support.office.com/de-DE/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504?ui=de-DE&rs=de-DE&ad=US)

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Benutzerrollen und Durchführen der Anmeldung
> [AZURE.NOTE]Damit sich Benutzer bei Azure PIM anmelden können, müssen sie über eine Lizenz für Azure verfügen.

## Zuweisen einer Lizenz an einen Benutzer in Azure AD

> [AZURE.NOTE]Die Lizenzoption wird nur angezeigt, wenn tatsächlich Lizenzen tatsächlich für das Abonnement vorhanden sind.

1. Melden Sie sich mit einem globalen Administratorkonto oder CO-Administratorkonto bei [http://manage.windowsazure.com](http://manage.windowsazure.com) an.
2. Klicken Sie im Hauptmenü auf **Alle Elemente**.
3. Wählen Sie das Verzeichnis aus, das Sie verwenden möchten, und dem Lizenzen zugeordnet sind.
4. Klicken Sie auf **Lizenzen**. Die Liste der verfügbaren Lizenzen wird angezeigt.
5. Klicken Sie auf den Lizenzplan, der die zu verteilenden Lizenzen enthält.
6. Klicken Sie auf **Benutzer zuweisen**.
7. Wählen Sie den Benutzer aus, dem Sie eine Lizenz zuweisen möchten.
8. Klicken Sie auf die Schaltfläche **Zuweisen**. Der Benutzer kann sich jetzt bei Azure anmelden.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Oct15_HO3-->