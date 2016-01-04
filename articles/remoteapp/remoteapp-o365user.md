
<properties 
    pageTitle="Verwenden von Azure RemoteApp mit Office 365-Benutzerkonten | Microsoft Azure"
	description="Informationen zum Verwenden von Azure RemoteApp mit meinen Office 365-Benutzerkonten"
	services="remoteapp"
	documentationCenter="" 
	authors="piotrci" 
	manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/16/2015" 
    ms.author="elizapo" />



# Verwenden von Azure RemoteApp mit Office 365-Benutzerkonten
Wenn Sie über ein Office 365-Abonnement verfügen, haben Sie ein Azure Active Directory, in dem Ihre Benutzernamen und Kennwörter für den Zugriff auf Office 365-Dienste gespeichert werden. Wenn Ihre Benutzer z. B. Office 365 ProPlus aktivieren, authentifizieren sie sich bei Azure AD, um das Vorhandensein von Lizenzen zu überprüfen. Die meisten Kunden möchten das gleiche Verzeichnis mit Azure RemoteApp verwenden.

Bei der Bereitstellung von Azure RemoteApp verwenden Sie wahrscheinlich ein Azure-Abonnement, das mit einem anderen Azure AD verknüpft ist. Um Ihr Office 365-Verzeichnis verwenden zu können, müssen Sie das Azure-Abonnement in das betreffende Verzeichnis verschieben.

Informationen zum Bereitstellen von Office 365-Clientanwendungen finden Sie unter [Verwenden Ihres Office 365-Abonnements mit Azure RemoteApp](remoteapp-officesubscription.md).
 
## Phase 1: Registrieren Sie Ihr kostenloses Abonnement für Office 365 Azure Active Directory.
Führen Sie [Ändern des Azure Active Directory-Mandanten in Azure RemoteApp](remoteapp-changetenant.md) aus, um administrativen Zugriff auf Ihr Azure AD über das Azure-Verwaltungsportal zu erhalten. Als Ergebnis dieses Prozesses sollten Sie sich beim Azure-Portal anmelden können und dort Ihr Verzeichnis sehen – an diesem Punkt wird nicht viel mehr angezeigt, da sich das vollständige Azure-Abonnement, das Sie mit Azure RemoteApp verwenden, in einem anderen Verzeichnis befindet.

Merken Sie sich den Namen und das Kennwort des Administratorkontos, das Sie in diesem Schritt erstellt haben – sie werden in Phase 2 benötigt.

## Phase 2: Ändern Sie das Azure AD, das mit Ihrem Azure-Abonnement verknüpft ist.
Wir werden Ihr Azure-Abonnement von ihrem aktuellen Verzeichnis in das Office 365-Verzeichnis ändern, mit dem wir in Phase 1 gearbeitet haben.

Befolgen Sie die Anweisungen in [Ändern des Azure Active Directory-Mandanten in Azure RemoteApp](remoteapp-changetenant.md). Achten Sie besonders auf die folgenden Schritte:

- Schritt 1: Wenn Sie Azure RemoteApp (ARA) in diesem Abonnement bereitgestellt haben, stellen Sie sicher, dass Sie zuerst alle Azure AD-Benutzerkonten aus allen ARA-Sammlungen entfernen, bevor Sie versuchen, etwas anderes zu tun. Alternativ können Sie erwägen, alle vorhandenen Sammlungen zu löschen.
- Schritt 2: Dies ist ein wichtiger Schritt. Sie müssen ein Microsoft-Konto (z. B. @outlook.com) als Dienstadministrator des Abonnements verwenden, weil keine Benutzerkonten aus dem vorhandenen Azure AD dem Abonnement angefügt sein dürfen – andernfalls können wir es nicht in ein anderes Azure AD verschieben.
- Schritt 4: Wenn Sie ein vorhandenes Verzeichnis hinzufügen, fordert das System Sie auf, sich mit dem Administratorkonto für dieses Verzeichnis anzumelden. Stellen Sie sicher, dass Sie das Administratorkonto aus Phase 1 verwenden.
- Schritt 5: Ändern Sie das übergeordnete Verzeichnis des Abonnements in Ihr Office 365-Verzeichnis. Das Endergebnis sollte sein, dass Ihr Abonnement unter „Einstellungen -> Abonnements“ das Office 365-Verzeichnis auflistet. ![Ändern des übergeordneten Verzeichnisses des Abonnements](./media/remoteapp-o365user/settings.png)
 

An diesem Punkt ist Ihr Azure RemoteApp-Abonnement Ihrem Office 365 Azure AD zugeordnet. Sie können die vorhandenen Office 365-Benutzerkonten mit Azure RemoteApp verwenden!

<!---HONumber=AcomDC_1217_2015-->