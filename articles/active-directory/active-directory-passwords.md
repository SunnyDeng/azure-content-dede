<properties 
	pageTitle="Erläuterung: Azure AD-Kennwortverwaltung | Microsoft Azure"
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
	ms.date="10/08/2015" 
	ms.author="asteen"/>

# Verwalten von Kennwörtern von einem beliebigen Ort aus

  >[AZURE.IMPORTANT]Sind Sie hier, da Sie Ihr Azure- oder O365- Kennwort zurücksetzen möchten? Wenn dies der Fall ist, [folgen Sie den Anweisungen in diesem Abschnitt](https://support.microsoft.com/kb/2606983).
  
Als Maßnahme zur Reduzierung von Kosten und Arbeitsaufwand ist Self-Service seit jeher ein wichtiges Ziel für IT-Abteilungen auf der ganzen Welt. Tatsächlich ist der Markt überflutet mit Produkten, mit denen Sie Ihre lokalen Gruppen, Kennwörter oder Benutzerprofile über die Cloud oder lokal verwalten können. Azure AD unterscheidet sich von diesen Angeboten durch die Bereitstellung einiger der benutzerfreundlichsten und leistungsstärksten Self-Service-Funktionen, die gegenwärtig verfügbar sind.

**Azure AD-Kennwortverwaltung** ist ein Satz von Funktionen, mit denen Ihre Benutzer jederzeit und überall sowie unter Einhaltung der von Ihnen definierten Sicherheitsrichtlinien alle Kennwörter von einem beliebigen Gerät aus verwalten können.

## Übersicht
In weniger als fünf Minuten können Sie erste Schritte mit einer Pilotbereitstellung der Azure AD-Kennwortverwaltung durchführen und diese in wenigen Stunden für die gesamte Organisation bereitstellen. Im Folgenden finden Sie einige nützliche Ressourcen für einen schnellen Einstieg in den Dienst:

* [**Funktionsweise**](active-directory-passwords-how-it-works.md) – Erfahren Sie mehr über die sechs verschiedenen Komponenten des Diensts und deren Funktionen.
* [**Erste Schritte**](active-directory-passwords-getting-started.md) – Erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben.
* [**Anpassen**](active-directory-passwords-customize.md) – Erfahren Sie, wie Sie das Aussehen und Verhalten des Diensts an die Anforderungen Ihrer Organisation anpassen.
* [**Best Practices**](active-directory-passwords-best-practices.md) – Erfahren Sie, wie Sie Kennwörter in Ihrer Organisation schnell bereitstellen und effektiv verwalten.
* [**Einblicke erhalten**](active-directory-passwords-get-insights.md) – Erfahren Sie mehr über unsere integrierten Berichtsfunktionen.
* [**Häufig gestellte Fragen**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen.
* [**Problembehandlung**](active-directory-passwords-troubleshoot.md) – Erfahren Sie, wie Sie Probleme mit dem Dienst schnell beheben.
* [**Weitere Informationen**](active-directory-passwords-learn-more.md) – Erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts.


## Welche Möglichkeiten bietet die Azure AD-Kennwortverwaltung?
Hier sind einige Aktivitäten aufgeführt, die Sie mit den Funktionen der Azure AD-Kennwortverwaltung ausführen können.

- Durch die **Self-Service-Kennwortänderung** können Endbenutzer oder Administratoren ihre abgelaufenen oder nicht abgelaufenen Kennwörter ändern, ohne einen Administrator oder den Helpdesk um Unterstützung zu bitten.
- Mit der **Self-Service-Kennwortzurücksetzung** können Endbenutzer oder Administratoren ihre Kennwörter automatisch zurücksetzen, ohne einen Administrator oder den Helpdesk um Unterstützung zu bitten. Die Self-Service-Kennwortzurücksetzung erfordert Azure AD Premium oder Basic. Weitere Informationen finden Sie unter "Azure Active Directory-Editionen".
- Mit der vom **Administrator ausgelösten Kennwortzurücksetzung** kann ein Administrator über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) das Kennwort eines Endbenutzers oder eines anderen Administrators zurücksetzen.
- **Berichte zu Kennwortverwaltungsaktivitäten** bieten Administratoren Einblicke in die Aktivitäten, die sich in ihrer Organisation in Bezug auf die Kennwortzurücksetzung und -registrierung ereignen. 
- Das **Rückschreiben von Kennwörtern** ermöglicht die Verwaltung lokaler Kennwörter in der Cloud, sodass alle oben genannten Szenarien von oder im Auftrag von Verbund- oder kennwortsynchronisierten Benutzern durchgeführt werden können. Für das Rückschreiben von Kennwörtern ist Azure AD Premium erforderlich. Weitere Informationen finden Sie unter "Erste Schritte mit Azure AD Premium".

## Gründe für die Verwendung der Azure AD-Kennwortverwaltung
Hier sind einige Gründe aufgeführt, warum Sie die Funktionen der Azure AD-Kennwortverwaltung nutzen sollten.

- **Kostensenkung** – Die vom Support unterstützte Kennwortzurücksetzung macht in der Regel 20 % der IT-Ausgaben einer Organisation aus.
- **Verbesserte Benutzerfreundlichkeit** – Benutzer möchten nicht beim Helpdesk anrufen und eine Stunde am Telefon verbringen, wenn sie ihre Kennwörter vergessen haben.
- **Verringerter Helpdeskaufwand** – Die Kennwortverwaltung ist in den meisten Organisationen einer der Hauptgründe, einen Helpdesk zu betreiben.
- **Möglichkeit zur Mobilität** – Benutzer können ihre Kennwörter unabhängig von ihrem Standort zurücksetzen.

## Aktuelle Dienstupdates

**Nutzbarkeitsupdates für die Registrierungsseite** – Oktober 2015

- Wenn ein Benutzer Daten bereits registriert hat, kann er einfach auf „Sieht gut aus“ klicken, um die Daten zu aktualisieren, ohne die E-Mail erneut senden oder einen Anruf tätigen zu müssen.

**Verbesserung der Zuverlässigkeit des Kennwortrückschreibens** – September 2015

- Ab der Septemberversion von Azure AD Connect versucht der Agent für das Kennwortrückschreiben „aggressiver“, Verbindungen erneut herzustellen, und verfügt über robustere Failoverfunktionen.

**API zum Abrufen von Berichtsdaten zur Kennwortzurücksetzung** – August 2015

- Die Daten aus den Berichten zur Kennwortzurücksetzung können jetzt direkt aus der [Azure AD-API zu Berichten und Ereignissen](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent) abgerufen werden.

**Unterstützung für Azure AD-Kennwortzurücksetzung während des Clouddomänenbeitritts** – August 2015

- Alle Cloudbenutzer können ihr Kennwort während des Onboarding-Vorgangs für den Clouddomänenbeitritt jetzt direkt über den Anmeldebildschirm von Windows 10 zurücksetzen. Beachten Sie, dass dies auf dem Anmeldebildschirm von Windows 10 derzeit noch nicht verfügbar gemacht wurde.

**Erzwingen der Registrierung für die Kennwortzurücksetzung bei der Anmeldung an Azure-Apps und Partner-Apps** – Juli 2015

- Zusätzlich zur Erzwingung der Registrierung während der Anmeldung an „myapps.microsoft.com“ wird jetzt das Erzwingen der Registrierung bei Anmeldungen am Azure-Verwaltungsportal und allen Partneranwendungen mit einmaligem Anmelden unterstützt.

**Unterstützung der Lokalisierung der Sicherheitsfrage** – Mai 2015

- Sie können jetzt vordefinierte Sicherheitsfragen auswählen, die im vollständigen O365-Sprachsatz lokalisiert sind, wenn Sie Sicherheitsfragen für die Kennwortzurücksetzung konfigurieren.

**Unterstützung für das Entsperren des Kontos während der Kennwortzurücksetzung** – Juni 2015

- Wenn Sie das Kennwortrückschreiben verwenden und Ihr Kennwort zurücksetzen, während Ihr Konto gesperrt ist, wird von uns automatisch Ihr Active Directory-Konto entsperrt!

**Organisationsspezifische SSPR-Registrierung** – April 2015

- Die Registrierungsseite für die Kennwortzurücksetzung enthält jetzt Ihr Firmenlogo!

**Sicherheitsfragen** – März 2015

- Sicherheitsfragen zur allgemeinen Verfügbarkeit veröffentlicht!

**Konto entsperren** – März 2015

- Benutzer können jetzt bei der Kennwortzurücksetzung ihre Konten entsperren.

## In Kürze verfügbar

Unten sind einige interessante Funktionen aufgeführt, an denen wir gerade arbeiten!

**Unterstützung der Entsperrung von Active Directory-Konten ohne Zurücksetzung eines Kennworts** – In Kürze verfügbar!

- Die Möglichkeit, AD-Konten separat von der Kennwortzurücksetzung zu entsperren, wurde von vielen Benutzern gefordert. Wir freuen uns, ankündigen zu können, dass wir dieser Funktion gerade den letzten Schliff verpassen und dass sie bald für alle Benutzer des Kennwortrückschreibens veröffentlicht wird!

**Unterstützung der Erinnerung von Benutzern an die Aktualisierung von registrierten Daten während der Anmeldung** – In Bearbeitung

- Erinnerungen für Benutzer an die Aktualisierung ihrer registrierten Daten beim Zugreifen auf „myapps.microsoft.com“ werden bereits unterstützt, aber wir arbeiten daran, dies für alle Anmeldungen zu ermöglichen.

**Erzwingen des Registrierens für die Kennwortzurücksetzung während der Anmeldung an Office 365-Apps** – In Bearbeitung

- Für diese neue und beste Azure AD-Anmeldeoberfläche aller Zeiten sind immer mehr Büro-Apps geeignet. Dann wird jeweils auch automatisch die Registrierung mit SSPR-Erzwingung unterstützt!

<br/> <br/> <br/>

**Zusätzliche Ressourcen**


* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Best Practices für die Kennwortverwaltung](active-directory-passwords-best-practices.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)
* [Kennwortverwaltung auf MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)

<!---HONumber=Nov15_HO1-->