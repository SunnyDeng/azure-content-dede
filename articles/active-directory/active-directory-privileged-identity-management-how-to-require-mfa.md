<properties
   pageTitle="Azure Privileged Identity Management: Erfordern von MFA"
   description="Erfahren Sie, wie Sie mit der Erweiterung Azure Privileged Identity Management MFA (Multi-Factor Authentication) als erforderlich festlegen."
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

# Azure Privileged Identity Management: Erfordern von MFA

Es wird dringend empfohlen, von allen Administratoren Multi-Factor Authentication zu fordern.

##Erfordern von MFA in Azure Privileged Identity Management
Wenn Sie sich als PIM-Administrator anmelden, erhalten Sie Warnungen mit der Empfehlung, für die privilegierten Konten Multi-Factor Authentication (MFA) als erforderlich festzulegen. Klicken Sie im PIM-Dashboard auf die Sicherheitswarnung. Anschließend wird ein neues Blatt mit einer Liste der Administratorkonten geöffnet, für die MFA erforderlich sein sollte. Sie können MFA als erforderlich festlegen, indem Sie mehrere Rollen auswählen und dann auf die Schaltfläche **Korrigieren** klicken. Alternativ können Sie auf die Auslassungspunkte neben einzelnen Rollen und dann auf die Schaltfläche **Korrigieren** klicken.

Darüber hinaus können Sie die MFA-Anforderung für eine spezifische Rolle ändern. Klicken Sie hierzu im Abschnitt „Rollen“ des Dashboards auf die entsprechende Rolle, und aktivieren Sie dann MFA für diese Rolle, indem Sie im Blatt „Rolle“ auf **Einstellungen** klicken und dann unter „Multi-Factor Authentication“ die Option **Aktivieren** auswählen.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->