<properties
   pageTitle="Azure Privileged Identity Management: Ändern oder Anzeigen der Standardeinstellungen für eine Rolle"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Privileged Identity Management die Standardeinstellungen für privilegierte Identitäten ändern."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: Ändern oder Anzeigen der Standardaktivierungseinstellungen für eine Rolle

## Ändern und Anzeigen der Standardrollenaktivierung
1. Klicken Sie im Dashboard in der Rollentabelle auf die Rolle, die konfiguriert werden soll.
2. Klicken Sie auf **Einstellungen**.
3. Legen Sie die Standardaktivierungsdauer in Stunden fest. Verwenden Sie hierzu den Schieberegler, oder geben Sie im Textfeld die Anzahl der Stunden ein.
4. Klicken Sie auf **Aktivieren** oder **Deaktivieren**, um festzulegen, ob Aktivierungsbenachrichtigungen an Administratoren gesendet werden sollen.
5. Klicken Sie auf **Aktivieren** oder **Deaktivieren**, um festzulegen, ob Administratoren in der Aktivierungsanforderung Informationen über das Ausstellen von Tickets eingeben dürfen.
6. Klicken Sie auf **Aktivieren** oder **Deaktivieren**, um festzulegen, ob eine Aktivierungsanforderung Multi-Factor Authentification erfordert. Weitere Informationen zum Verwenden von MFA mit PIM finden Sie unter [Erfordern von MFA](active-directory-privileged-identity-management-how-to-require-mfa.md).
7. Klicken Sie auf **Aktivieren** oder **Deaktivieren**, um festzulegen, ob globale Administratoren als temporäre Rolle zulässig sind.
8. Klicken Sie auf **Speichern**.

<!--PLACEHOLDER: Need an explanation of what the temporary Global Administrator setting is for.-->

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->