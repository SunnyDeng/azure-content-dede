<properties
	pageTitle="Kontobereitstellungsbenachrichtigungen | Microsoft Azure"
	description="Erfahren Sie, wie Sie durch das Aktivieren von Benachrichtigungen zur Kontobereitstellung sicherstellen können, dass Sie zu Problemen benachrichtigt werden, die sich auf die Benutzerbereitstellung beziehen und Ihr Eingreifen erfordern."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/20/2015"
	ms.author="markusvi"/>


# Kontobereitstellungsbenachrichtigungen

Mithilfe der Benutzerbereitstellung können Sie den Vorgang der Verwaltung von Benutzern in SaaS-Anwendungen von Drittanbietern automatisieren. <br> Auch wenn es sich um einen automatisierten Vorgang handelt, ist Ihre Interaktion mit diesem Vorgang von Zeit zu Zeit erforderlich. <br> Dies ist z. B. dann der Fall, wenn das Kennwort des Kontos, das Sie für den Austausch von Daten mit einer SaaS-Anwendung eines Drittanbieters konfiguriert haben, abgelaufen ist.

Indem Sie Benachrichtigungen zur Kontobereitstellung aktivieren, können Sie sicherstellen, dass Sie zu Problemen benachrichtigt werden, die sich auf die Benutzerbereitstellung beziehen und Ihr Eingreifen erfordern.

Sie aktivieren oder deaktivieren Benachrichtigungen zur Kontobereitstellung als Teil der Bereitstellungskonfiguration für eine SaaS-Anwendung eines Drittanbieters.

![Benutzerbereitstellung][1]



Wenn Sie Benachrichtigungen zur Kontobereitstellung aktivieren möchten, aktivieren Sie das entsprechende Kontrollkästchen auf der Dialogfeldseite **Bestätigung**, und geben Sie dann den E-Mail-Alias des Empfängers ein.

![Kontobereitstellungsbenachrichtigungen][2]
 


Sie können eine Verteilerliste als Empfänger eingeben. Beachten Sie jedoch unbedingt, dass die Benachrichtigungs-E-Mail Links zu Berichten enthält, die nur für die Azure AD-Administratoren zugänglich sind.

Wenn Sie Benachrichtigungen zur Kontobereitstellung aktiviert haben, erhalten Sie E-Mail-Nachrichten zu wichtigen Problemen, die sich auf die Benutzerbereitstellung beziehen. Damit eine zu große Anzahl von E-Mails vermieden wird, erhalten Sie jedoch nur eine Benachrichtigungs-E-Mail pro Tag für jede SaaS-Anwendung, für die Benachrichtigungs-E-Mails aktiviert sind.


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-account-provisioning-notifications/ic766307.png
[2]: ./media/active-directory-saas-account-provisioning-notifications/ic766308.png

<!---HONumber=Oct15_HO4-->