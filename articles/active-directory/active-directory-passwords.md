<properties 
	pageTitle="Azure AD-Kennwortzurücksetzung | Microsoft Azure"
	description="Beschreibung der Funktionen zur Kennwortverwaltung in Azure AD, einschließlich Zurücksetzen und Ändern des Kennworts, Berichte zur Kennwortverwaltung und Rückschreiben in Ihr lokales Active Directory." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/16/2015" 
	ms.author="asteen"/>
	

# Zurücksetzen des Azure AD-Kennworts für Benutzer und Administratoren

  >[AZURE.IMPORTANT]Sind Sie hier, weil Sie Ihr Azure- oder Office 365-Kennwort zurücksetzen möchten? Wenn das der Fall ist, [überspringen Sie diesen Abschnitt](#users-how-to-manage-your-own-password).
  
Als Maßnahme zur Reduzierung von Kosten und Arbeitsaufwand ist Self-Service seit jeher ein wichtiges Ziel für IT-Abteilungen auf der ganzen Welt. Tatsächlich ist der Markt überflutet mit Produkten, mit denen Sie Ihre lokalen Gruppen, Kennwörter oder Benutzerprofile über die Cloud oder lokal verwalten können. Azure AD unterscheidet sich von diesen Angeboten durch die Bereitstellung einiger der benutzerfreundlichsten und leistungsstärksten Self-Service-Funktionen, die gegenwärtig verfügbar sind.

**Azure AD-Kennwortverwaltung** ist ein Satz von Funktionen, mit denen Ihre Benutzer jederzeit und überall sowie unter Einhaltung der von Ihnen definierten Sicherheitsrichtlinien alle Kennwörter von einem beliebigen Gerät aus verwalten können.

##FÜR BENUTZER: Verwalten Ihres eigenen Kennworts
Wenn Sie ein Benutzer (kein Administrator) in einer Organisation sind, die Office 365 oder Microsoft-Konten zum Zugriff auf geschäftliche Ressourcen verwendet, klicken Sie auf die folgenden Links, um zu erfahren, wie Sie häufige Probleme mit Ihrem Kennwort beheben.

| Thema | |
| --------- | --------- |
| Ich möchte mich für das Zurücksetzen meines Kennworts registrieren. | [Registrieren für die Kennwortzurücksetzung](active-directory-passwords-update-your-own-password.md#how-to-register-for-password-reset) |
| Ich möchte mein Kennwort in Office 365 ändern. | [Ändern des Kennworts in Office 365](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-o365) |
| Ich möchte mein Kennwort in myapps.microsoft.com ändern. | [Ändern des Kennworts im Zugriffsbereich](active-directory-passwords-update-your-own-password.md#how-to-change-your-password-from-the-access-panel) |
| Ich habe mein Kennwort vergessen und möchte es zurücksetzen. | [Zurücksetzen des Kennworts](active-directory-passwords-update-your-own-password.md#how-to-reset-your-password) |
| Ich kann mich nicht anmelden und möchte mein Konto entsperren. | [Entsperren Ihres lokalen Kontos](active-directory-passwords-update-your-own-password.md#how-to-unlock-your-account) |
| Ich brauche Hilfe wegen eines Fehlers beim Zurücksetzen des Kennworts. | [Häufige Probleme und Lösungen](active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions) |

##FÜR ADMINISTRATOREN: Erste Schritte für das Zurücksetzen von Kennwörtern mit Azure AD
Wenn Sie Administrator sind und das Zurücksetzen von Kennwörtern in Azure AD aktivieren oder einfach nur mehr darüber erfahren möchten, klicken Sie auf die Links zu den für Sie relevanten Themen.

| Thema | |
| --------- | --------- |
| Unterstützte Szenarien | [Welche Möglichkeiten bietet die Kennwortzurücksetzung in Azure AD?](#what-is-possible-with-azure-ad-password-reset) |
| Gründe für die Verwendung | [Gründe für die Verwendung der Kennwortzurücksetzung in Azure AD](#why-use-azure-ad-password-reset) |
| Preise und Verfügbarkeit | [Preise und Verfügbarkeit](#pricing-and-availability) |
| Aktivieren der Kennwortzurücksetzung | [Aktivieren der Kennwortzurücksetzung für Ihre Benutzer](#enable-password-reset-for-your-users) |
| Anpassen der Funktionsweise | [Anpassen des Verhaltens der Kennwortzurücksetzung](#customize-password-reset-behavior) |
| Einführung für die Benutzer (Rollout) | [Konfigurieren der Benutzer zur Verwendung der Kennwortzurücksetzung](#configure-your-users-to-use-password-reset) |
| Anzeigen von Berichten | [Anzeigen der Kennwortzurücksetzungsaktivitäten mit integrierten Berichten](#view-password-reset-activity-with-integrated-reports) |
| Zurücksetzen des Kennworts für einen Benutzer | [Verwalten der Kennwörter Ihrer Benutzer](#manage-your-users-passwords) |
| Festlegen der Kennwortrichtlinien für meine Organisation | [Festlegen von Kennwortrichtlinien](#set-password-policies) |
| Beheben von Problemen | [Beheben von Problemen](#troubleshoot-a-problem) |
| Häufig gestellte Fragen | [Häufig gestellte Fragen (FAQ)](#read-a-faq) |
| Technische Details | [Erläuterungen zu den technischen Details](#understand-the-technical-details) |
| Neue Features | [Aktuelle Dienstupdates](#recent-service-updates) |
| Links zu weiteren Informationen | [Links zur Dokumentation für die Kennwortzurücksetzung](#links-to-password-reset-documentation) |

### Welche Möglichkeiten bietet die Kennwortzurücksetzung in Azure AD?
Hier sind einige Aktivitäten aufgeführt, die Sie mit den Funktionen der Azure AD-Kennwortverwaltung ausführen können.

- Durch die **Self-Service-Kennwortänderung** können Endbenutzer oder Administratoren ihre abgelaufenen oder nicht abgelaufenen Kennwörter ändern, ohne einen Administrator oder den Helpdesk um Unterstützung zu bitten.
- Mit der **Self-Service-Kennwortzurücksetzung** können Endbenutzer oder Administratoren ihre Kennwörter automatisch zurücksetzen, ohne einen Administrator oder den Helpdesk um Unterstützung zu bitten. Die Self-Service-Kennwortzurücksetzung erfordert Azure AD Premium oder Basic. Weitere Informationen finden Sie unter "Azure Active Directory-Editionen".
- Mit der vom **Administrator ausgelösten Kennwortzurücksetzung** kann ein Administrator über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) das Kennwort eines Endbenutzers oder eines anderen Administrators zurücksetzen.
- **Berichte zu Kennwortverwaltungsaktivitäten** bieten Administratoren Einblicke in die Aktivitäten, die sich in ihrer Organisation in Bezug auf die Kennwortzurücksetzung und -registrierung ereignen. 
- Das **Rückschreiben von Kennwörtern** ermöglicht die Verwaltung lokaler Kennwörter in der Cloud, sodass alle oben genannten Szenarien von oder im Auftrag von Verbund- oder kennwortsynchronisierten Benutzern durchgeführt werden können. Für das Rückschreiben von Kennwörtern ist Azure AD Premium erforderlich. Weitere Informationen finden Sie unter "Erste Schritte mit Azure AD Premium".

### Gründe für die Verwendung der Kennwortzurücksetzung in Azure AD
Hier sind einige Gründe aufgeführt, warum Sie die Funktionen der Azure AD-Kennwortverwaltung nutzen sollten.

- **Kostensenkung** – Die vom Support unterstützte Kennwortzurücksetzung macht in der Regel 20 % der IT-Ausgaben einer Organisation aus.
- **Verbesserte Benutzerfreundlichkeit** – Benutzer möchten nicht beim Helpdesk anrufen und eine Stunde am Telefon verbringen, wenn sie ihre Kennwörter vergessen haben.
- **Verringerter Helpdeskaufwand** – Die Kennwortverwaltung ist in den meisten Organisationen einer der Hauptgründe, einen Helpdesk zu betreiben.
- **Möglichkeit zur Mobilität** – Benutzer können ihre Kennwörter unabhängig von ihrem Standort zurücksetzen.

### Preise und Verfügbarkeit
Die Azure AD-Kennwortzurücksetzung steht in drei Tarifen zur Verfügung, abhängig von Ihrem Abonnement:

- **Azure AD Free**: Cloudadministratoren können ihre eigenen Kennwörter zurücksetzen.
- **Azure AD Basic oder beliebiges kostenpflichtiges Office 365-Abonnement**: Cloudbenutzer und Cloudadministratoren können ihre eigenen Kennwörter zurücksetzen.
- **Azure AD Premium**: Alle Benutzer und alle Administratoren können ihr eigenes Kennwort zurücksetzen, einschließlich Cloudbenutzern, Verbundbenutzern oder kennwortsynchronisierten Benutzern (hierfür muss das [Kennwortrückschreiben aktiviert](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) sein).

Weitere Informationen über die Preise für Azure AD Premium oder Basic finden Sie auf der Seite [Azure Active Directory Preise](https://azure.microsoft.com/pricing/details/active-directory/).

##Aktivieren der Kennwortzurücksetzung für Ihre Benutzer
| Thema | |
| --------- | --------- |
| Wie aktiviere ich die Kennwortzurücksetzung für Cloudbenutzer? | [Aktivieren von Benutzern für das Zurücksetzen ihrer Cloudkennwörter für Azure Active Directory](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) |
| Wie aktiviere ich das Zurücksetzen und Ändern von Kennwörtern für lokale Benutzer? | [Aktivieren von Benutzern für das Zurücksetzen oder Ändern ihrer lokalen Active Directory-Kennwörter](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) |
| Wie beschränke ich das Zurücksetzen von Kennwörtern auf eine bestimmte Gruppe von Benutzern? | [Beschränken der Kennwortzurücksetzung auf bestimmte Benutzer](active-directory-passwords-customize.md#restrict-access-to-password-reset) |
| Wie teste ich das Zurücksetzen von Cloudkennwörtern? | [Zurücksetzen Ihres Azure AD-Kennworts als Benutzer](active-directory-passwords-getting-started.md#step-3-reset-your-azure-ad-password-as-a-user) |
| Wie teste ich das Zurücksetzen von lokalen Kennwörtern? | [Zurücksetzen Ihres lokalen AD-Kennworts als Benutzer](active-directory-passwords-getting-started.md#step-5-reset-your-ad-password-as-a-user) |
| Wie kann ich die Kennwortzurücksetzung später deaktivieren? | [Einstellung: Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist](active-directory-passwords-customize.md#users-enabled-for-password-reset) |


##Anpassen des Verhaltens der Kennwortzurücksetzung
| Thema | |
| --------- | --------- |
| Wie ändere ich die unterstützten Authentifizierungsmethoden? | [Einstellung: Für Benutzer verfügbare Authentifizierungsmethoden](active-directory-passwords-customize.md#authentication-methods-available-to-users) |
| Wie ändere ich die Anzahl erforderlicher Authentifizierungsmethoden? | [Einstellung: Anzahl erforderlicher Authentifizierungsmethoden](active-directory-passwords-customize.md#number-of-authentication-methods-required) |
| Wie richte ich benutzerdefinierte Sicherheitsfragen ein? | [Einstellung: Benutzerdefinierte Sicherheitsfragen](active-directory-passwords-customize.md#custom-security-questions) |
| Wie richte ich vorgefertigte lokalisierte Sicherheitsfragen ein? | [Einstellung: Informationsbasierte Sicherheitsfragen](active-directory-passwords-customize.md#knowledge-based-security-questions) |
| Wie kann ich ändern, wie viele Sicherheitsfragen erforderlich sind? | [Einstellung: Anzahl der Sicherheitsfragen für die Registrierung oder das Zurücksetzen](active-directory-passwords-customize.md#number-of-questions-required-to-register) |
| Wie kann ich anpassen, wie ein Benutzer mit dem Administrator in Kontakt tritt? | [Einstellung: Link „Wenden Sie sich Ihren Administrator“ anpassen](active-directory-passwords-customize.md#customize-the-contact-your-administrator-link) |
| Wie kann ich Benutzern das Entsperren von AD-Konten ohne Zurücksetzen eines Kennworts ermöglichen? | [Einstellung: Benutzern ermöglichen, ihre AD-Konten ohne Zurücksetzen eines Kennworts zu entsperren](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password) |
| Wie kann ich Benachrichtigungen zur Kennwortzurücksetzung für Benutzer aktivieren? | [Einstellung: Benutzer benachrichtigen, wenn ihre Kennwörter zurückgesetzt wurden](active-directory-passwords-customize.md#notify-users-and-admins-when-their-own-password-has-been-reset) |
| Wie kann ich Benachrichtigungen zur Kennwortzurücksetzung für Administratoren aktivieren? | [Einstellung: Andere Administratoren benachrichtigen, wenn ein Administrator sein eigenes Kennwort zurückgesetzt hat](active-directory-passwords-customize.md#notify-admins-when-other-admins-reset-their-own-passwords) |
| Wie kann ich das Look-and-Feel für das Zurücksetzen von Kennwörtern anpassen? | [Einstellung: Firmenname, Branding und Logo](active-directory-passwords-customize.md#password-managment-look-and-feel) |


##Konfigurieren der Benutzer zur Verwendung der Kennwortzurücksetzung
| Thema | |
| --------- | --------- |
| Wie erfahre ich, ob ein Konto für die Kennwortzurücksetzung konfiguriert ist? | [Erforderliche Konfiguration für die Kontozurücksetzung](active-directory-passwords-best-practices.md#what-makes-an-account-configured) |
| Wie konfiguriere ich Benutzer für die Kennwortzurücksetzung? | [Möglichkeiten zum Auffüllen von Authentifizierungsdaten für die Kennwortzurücksetzung für Ihre Benutzer](active-directory-passwords-best-practices.md#ways-to-populate-authentication-data) |
| Wie kann ich Daten für meine Benutzer manuell hochladen? | [Selbstständiges Hochladen von Daten für die Kennwortzurücksetzung](active-directory-passwords-best-practices.md#uploading-data-yourself) |
| Wie verwende ich PowerShell zum Lesen oder Festlegen von Daten für meine Benutzer? | [Zugriff auf Daten zur Kennwortzurücksetzung für Ihre Benutzer](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) |
| Wie kann ich Daten für die Kennwortzurücksetzung vom lokalen Standort aus synchronisieren? | [Welche Daten werden bei der Kennwortzurücksetzung verwendet?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |
| Wie kann ich meine Benutzer mit einer E-Mail-Kampagne zum Registrieren für die und Verwenden der Kennwortzurücksetzung motivieren? | [E-Mail-basierte Einführung der Kennwortzurücksetzung](active-directory-passwords-best-practices.md#email-based-rollout) |
| Wie kann ich meine Benutzer bei der Anmeldung zum Registrieren zwingen? | [Erzwungene registrierungsbasierte Einführung der Kennwortzurücksetzung](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) |
| Wie kann ich veranlassen, dass meine Benutzer ihre registrierten Authentifizierungsdaten in regelmäßigen Abständen erneut bestätigen müssen? | [Einstellung: Anzahl der Tage, bis die Benutzer ihre Authentifizierungsdaten erneut bestätigen müssen](active-directory-passwords-customize.md#number-of-days-before-users-must-confirm-their-contact-data) |
| Welche Methoden zum Kommunizieren der Kennwortzurücksetzung an die Endbenutzer haben sich besonders bewährt? | [Erstellen eines eigenen Kennwortportals für Ihre Benutzer](active-directory-passwords-best-practices.md#creating-your-own-password-portal) |


##Anzeigen der Kennwortzurücksetzungsaktivitäten mit integrierten Berichten
| Thema | |
| --------- | --------- |
| Wo kann ich Berichte zur Kennwortzurücksetzung sehen? | [Übersicht über Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md#overview-of-password-management-reports) |
| Wo kann ich sehen, wie die Benutzer in meiner Organisation die Kennwortzurücksetzung nutzen? | [Anzeigen der Aktivität „Zurücksetzen des Kennworts“](active-directory-passwords-get-insights.md#view-password-reset-activity) |
| Wo kann ich sehen, wie viele Benutzer sich registrieren und wofür sie sich registrieren? | [Anzeigen der Aktivität „Registrierung für Zurücksetzen des Kennworts“](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) |
| Wie kann ich Berichte zur Kennwortzurücksetzung über eine API abrufen? | [Erstellen einer Azure AD-Anwendung für den Zugriff auf die Reporting-API](active-directory-reporting-api-getting-started.md#creating-an-azure-ad-application-to-access-the-api) |
| Welche Art von Berichtsinformationen zur Kennwortzurücksetzung ist über eine API verfügbar? | [In der Reporting-API verfügbare Kennwortzurücksetzungs- und Registrierungsereignisse](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview#SsprActivityEvent) |


##Verwalten der Kennwörter Ihrer Benutzer
| Thema | |
| --------- | --------- |
| Wie setze ich das Kennwort eines Benutzers im Office 365-Verwaltungsportal zurück? | [Zurücksetzen des Kennworts für einen Benutzer in Office 365](https://support.office.com/article/Reset-a-user-s-password-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C) |
| Wie setze ich das Kennwort eines Benutzers mit PowerShell zurück? | [Zurücksetzen des Kennworts eines Benutzers mit Set-MsolUserPassword](https://msdn.microsoft.com/library/azure/dn194140.aspx) |


##Festlegen von Kennwortrichtlinien
| Thema | |
| --------- | --------- |
| Wie lege ich eine Kennwortablaufrichtlinie für die Organisation in Office 365 fest? | [Festlegen einer Kennwortablaufrichtlinie](https://support.office.com/article/Set-a-user-s-password-expiration-policy-0f54736f-eb22-414c-8273-498a0918678f) |
| Wie lege ich mit PowerShell fest, dass das Kennwort eines bestimmten Benutzers niemals abläuft? | [Festlegen mithilfe von PowerShell, dass das Kennwort eines einzelnen Benutzers nie abläuft](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466) |
| Wie finde ich mithilfe von PowerShell heraus, ob für einen Benutzer festgelegt wurde, dass sein Kennwort nie abläuft? | [Überprüfen des Kennwortablaufstatus für einen Benutzer mit PowerShell](https://support.office.com/article/Set-an-individual-user-s-password-to-never-expire-f493e3af-e1d8-4668-9211-230c245a0466#__toc378845827) |


##Beheben von Problemen
| Thema | |
| --------- | --------- |
| Welche Informationen sollte ich dem Support geben, wenn ich Hilfe benötige? | [Erforderliche Informationen beim Anfordern von Hilfe](active-directory-passwords-troubleshoot.md#information-to-include-when-you-need-help) |
| Wie kann ich ein Problem bei der Kennwortzurücksetzung beheben? | [Problembehandlung: Kennwortzurücksetzungsportal](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-portal) |
| Wie kann ich ein Problem beim Rückschreiben von Kennwörtern beheben? | [Problembehandlung: Kennwortrückschreiben](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Wie kann ich ein Problem mit der Verbindung beim Kennwortrückschreiben beheben? | [Problembehandlung: Konnektivität beim Kennwortrückschreiben](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback-connectivity) |
| Wie kann ich ein Problem bei der Konfiguration für die Kennwortzurücksetzung beheben? | [Problembehandlung: Konfiguration der Kennwortzurücksetzung im Azure-Verwaltungsportal](active-directory-passwords-troubleshoot.md#troubleshoot-password-reset-configuration-in-the-azure-management-portal) |
| Wie kann ich ein Problem bei den Berichten zur Kennwortzurücksetzung beheben? | [Problembehandlung: Berichte zur Kennwortverwaltung im Azure-Verwaltungsportal](active-directory-passwords-troubleshoot.md#troubleshoot-password-management-reports-in-the-azure-management-portal) |
| Wie kann ich ein Problem bei der Registrierung für die Kennwortzurücksetzung beheben? | [Problembehandlung: Registrierungsportal für die Kennwortzurücksetzung](active-directory-passwords-troubleshoot.md#troubleshoot-the-password-reset-registration-portal) |
| Ereignisprotokoll-Fehlercodes für das Kennwortrückschreiben | [Ereignisprotokoll-Fehlercodes für das Kennwortrückschreiben](active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes) |


##Häufig gestellte Fragen (FAQ)
| Thema | |
| --------- | --------- |
| Ich möchte Fragen und Antworten zum Registrieren für die Kennwortzurücksetzung lesen. | [FAQ zum Registrieren für die Kennwortzurücksetzung](active-directory-passwords-faq.md#password-reset-registration) |
| Ich möchte Fragen und Antworten zum Zurücksetzen des Kennworts lesen. | [FAQ zum Zurücksetzen des Kennworts](active-directory-passwords-faq.md#password-reset) |
| Ich möchte Fragen und Antworten zu den Berichten zur Kennwortzurücksetzung lesen. | [FAQ zu den Berichten zur Kennwortverwaltung](active-directory-passwords-faq.md#password-management-reports) |
| Ich möchte Fragen und Antworten zum Rückschreiben von Kennwörtern lesen. | [FAQ zum Rückschreiben von Kennwörtern](active-directory-passwords-faq.md#password-writeback) |


##Erläuterungen zu den technischen Details

| Thema | |
| --------- | --------- |
| Ich möchte wissen, was das Rückschreiben von Kennwörtern ist. | [Übersicht über das Kennwortrückschreiben](active-directory-passwords-learn-more.md#password-writeback-overview) |
| Ich möchte wissen, wie das Rückschreiben von Kennwörtern funktioniert. | [Funktionsweise des Kennwortrückschreibens](active-directory-passwords-learn-more.md#how-password-writeback-works) |
| Ich möchte wissen, welche Szenarien von der Funktion zum Kennwortrückschreiben unterstützt werden. | [Unterstützte Szenarien für das Kennwortrückschreiben](active-directory-passwords-learn-more.md#scenarios-supported-for-password-writeback) |
| Ich möchte wissen, wie beim Kennwortrückschreiben die Sicherheit gewährleistet wird. | [Sicherheitsmodell für das Kennwortrückschreiben](active-directory-passwords-learn-more.md#password-writeback-security-model) |
| Ich möchte wissen, wie das Kennwortzurücksetzungsportal funktioniert. | [Wie funktioniert das Portal für die Kennwortzurücksetzung?](active-directory-passwords-learn-more.md#how-does-the-password-reset-portal-work) |
| Ich möchte wissen, welche Daten bei der Kennwortzurücksetzung verwendet werden. | [Welche Daten werden bei der Kennwortzurücksetzung verwendet?](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) |

## Aktuelle Dienstupdates

####Erzwingen der Registrierung für die Kennwortzurücksetzung während der Anmeldung bei Office 365-Apps – November 2015

- Nachdem nun das Feature für die [erzwungene Registrierung](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) aktiviert wurde, müssen sich Ihre Benutzer für die Kennwortzurücksetzung registrieren, sobald sie sich von einem beliebigen Ort aus mit einem Geschäfts- oder Schulkonto anmelden. Dadurch wird die Einführung der Kennwortzurücksetzung in vielen Organisationen deutlich beschleunigt. Mit diesem Feature war die Einführung bei einigen großen Organisationen innerhalb von nur zwei Wochen abgeschlossen.

####Unterstützung der Entsperrung von Active Directory-Konten ohne Zurücksetzung eines Kennworts – November 2015

- Das alleinige Entsperren (ohne Zurücksetzen) ist ein heute ein gewichtiger Faktor für Helpdesks. In vielen Organisationen werden bis zu 70 % des Budgets für Kennwortzurücksetzungen durch das Entsperren von Konten verbraucht. Um auf diese Situation zu reagieren, können Sie nun bei der Azure AD-Kennwortzurücksetzung ein Feature aktivieren, das es den Benutzern ermöglicht, AD-Konten unabhängig vom Zurücksetzen des Kennworts zu entsperren. Erfahren Sie hier, wie Sie dieses Feature aktivieren: [Einstellung: Benutzern ermöglichen, ihre AD-Konten ohne Zurücksetzen eines Kennworts zu entsperren](active-directory-passwords-customize.md#allow-users-to-unlock-accounts-without-resetting-their-password).

####Updates für die Benutzerfreundlichkeit der Registrierungsseite – Oktober 2015

- Wenn ein Benutzer Daten bereits registriert hat, kann er einfach auf „Sieht gut aus“ klicken, um die Daten zu aktualisieren, ohne die E-Mail erneut senden oder einen Anruf tätigen zu müssen.

####Verbesserung der Zuverlässigkeit des Kennwortrückschreibens – September 2015

- Ab der Septemberversion von Azure AD Connect versucht der Agent für das Kennwortrückschreiben „aggressiver“, Verbindungen erneut herzustellen, und verfügt über robustere Failoverfunktionen.

####API zum Abrufen von Berichtsdaten zur Kennwortzurücksetzung – August 2015

- Die Daten aus den Berichten zur Kennwortzurücksetzung können jetzt direkt aus der [Azure AD-API zu Berichten und Ereignissen](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) abgerufen werden.

####Unterstützung für die Azure AD-Kennwortzurücksetzung während des Clouddomänenbeitritts – August 2015

- Alle Cloudbenutzer können ihr Kennwort während des Onboarding-Vorgangs für den Clouddomänenbeitritt jetzt direkt über den Anmeldebildschirm von Windows 10 zurücksetzen. Beachten Sie, dass dies auf dem Anmeldebildschirm von Windows 10 derzeit noch nicht verfügbar gemacht wurde.

####Erzwingen der Registrierung für die Kennwortzurücksetzung bei der Anmeldung bei Azure-Apps und Partner-Apps – Juli 2015

- Zusätzlich zur Erzwingung der Registrierung während der Anmeldung an „myapps.microsoft.com“ wird jetzt das Erzwingen der Registrierung bei Anmeldungen am Azure-Verwaltungsportal und allen Partneranwendungen mit einmaligem Anmelden unterstützt.

####Unterstützung der Lokalisierung der Sicherheitsfragen – Mai 2015

- Sie können jetzt vordefinierte Sicherheitsfragen auswählen, die im vollständigen O365-Sprachsatz lokalisiert sind, wenn Sie Sicherheitsfragen für die Kennwortzurücksetzung konfigurieren.

####Unterstützung für das Entsperren des Kontos während der Kennwortzurücksetzung – Juni 2015

- Wenn Sie das Kennwortrückschreiben verwenden und Ihr Kennwort zurücksetzen, während Ihr Konto gesperrt ist, wird von uns automatisch Ihr Active Directory-Konto entsperrt!

####Organisationsspezifische SSPR-Registrierung – April 2015

- Die Registrierungsseite für die Kennwortzurücksetzung enthält jetzt Ihr Firmenlogo!

####Sicherheitsfragen – März 2015

- Sicherheitsfragen zur allgemeinen Verfügbarkeit veröffentlicht!

####Entsperren von Konten – März 2015

- Benutzer können jetzt bei der Kennwortzurücksetzung ihre Konten entsperren.

## In Kürze verfügbar

Unten sind einige interessante Funktionen aufgeführt, an denen wir gerade arbeiten!

**Unterstützung der Erinnerung von Benutzern an die Aktualisierung von registrierten Daten während der Anmeldung** – In Bearbeitung

- Erinnerungen für Benutzer an die Aktualisierung ihrer registrierten Daten beim Zugreifen auf „myapps.microsoft.com“ werden bereits unterstützt, aber wir arbeiten daran, dies für alle Anmeldungen zu ermöglichen.

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD:

* [**Zurücksetzen Ihres eigenen Kennworts**](active-directory-passwords-update-your-own-password) – Hier erhalten Sie Informationen zum Zurücksetzen oder Ändern Ihres eigenen Kennworts als Benutzer des Systems.
* [**Funktionsweise**](active-directory-passwords-how-it-works.md) – Erfahren Sie mehr über die sechs verschiedenen Komponenten des Diensts und deren Funktionen.
* [**Erste Schritte**](active-directory-passwords-getting-started.md) – Erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben.
* [**Anpassen**](active-directory-passwords-customize.md) – Erfahren Sie, wie Sie das Aussehen und Verhalten des Diensts an die Anforderungen Ihrer Organisation anpassen.
* [**Best Practices**](active-directory-passwords-best-practices.md) – Erfahren Sie, wie Sie Kennwörter in Ihrer Organisation schnell bereitstellen und effektiv verwalten.
* [**Einblicke erhalten**](active-directory-passwords-get-insights.md) – Erfahren Sie mehr über unsere integrierten Berichtsfunktionen.
* [**Häufig gestellte Fragen**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen.
* [**Problembehandlung**](active-directory-passwords-troubleshoot.md) – Erfahren Sie, wie Sie Probleme mit dem Dienst schnell beheben.
* [**Weitere Informationen**](active-directory-passwords-learn-more.md) – Erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts.

<!---HONumber=Nov15_HO4-->