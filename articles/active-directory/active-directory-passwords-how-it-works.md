<properties 
	pageTitle="Funktionsweise: Azure AD-Kennwortverwaltung | Microsoft Azure" 
	description="Lernen Sie die verschiedenen Komponenten der Azure AD-Kennwortverwaltung kennen und erfahren Sie, wo Benutzer sich registrieren, ihre Kennwörter zurücksetzen und ändern, wo Administratoren die Verwaltung lokaler Active Directory-Kennwörter konfigurieren, aktivieren und Berichte zur Kennwortverwaltung anzeigen können." 
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

# Funktionsweise der Kennwortverwaltung  

Die Kennwortverwaltung in Azure Active Directory umfasst verschiedene logische Komponenten, nachfolgend beschrieben werden. Klicken Sie auf jeden Link, um mehr über die jeweilige Komponente zu erfahren.

- [**Portal für die Konfiguration der Kennwortverwaltung**](#password-management-configuration-portal) – Administratoren können verschiedene Aspekte der Kennwortverwaltung ihrer Mandanten steuern, indem sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) zur Registerkarte "Konfigurieren" navigieren.
- [**Portal für die Benutzerregistrierung**](#user-registration-portal) – Benutzer können sich über dieses Self-Service-Webportal für die Kennwortzurücksetzung registrieren.
- [**Portal für das Zurücksetzen von Benutzerkennwörtern**](#user-password-reset-portal) – Benutzer können durch das Ausführen verschiedener Überprüfungsschritte – diese werden über die vom Administrator gesteuerte Richtlinie für die Kennwortzurücksetzung festlegt – ihre eigenen Kennwörter zurücksetzen.
- [**Portal zum Ändern von Benutzerkennwörtern**](#user-password-change-portal) – Benutzer können ihre eigenen Kennwörter jederzeit ändern, indem sie über dieses Webportal ihr altes Kennwort eingeben und dann ein neues Kennwort festlegen.
- [**Berichte zur Kennwortverwaltung**](#password-management-reports) – Administratoren können Aktivitäten zu Kennwortzurücksetzung und Registrierung in ihrem Mandanten anzeigen und analysieren, indem sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) zur Registerkarte "Berichte" für ihr Verzeichnis wechseln und den Abschnitt "Aktivitätsberichte" anzeigen.
- [**Komponente zur Kennwortrückschreibung von Azure AD Connect**](#password-writeback-component-of-azure-ad-connect) – Administratoren können beim Installieren von Azure AD Connect die Funktion zur Kennwortrückschreibung aktivieren, um die Verwaltung von Verbundkennwörtern oder synchronisierten Benutzerkennwörtern aus der Cloud zu aktivieren.

## Portal für die Konfiguration der Kennwortverwaltung
Sie können im [Azure-Verwaltungsportal](https://manage.windowsazure.com) Richtlinien für die Kennwortverwaltung konfigurieren, indem Sie auf der Registerkarte **Konfigurieren** zum Abschnitt **Richtlinie zum Zurücksetzen des Benutzerkennworts** navigieren. Auf dieser Konfigurationsseite können Sie zahlreiche Aspekte der Kennwortverwaltung für Ihre Organisation steuern, darunter:

- Aktivieren und Deaktivieren der Kennwortzurücksetzung für alle Benutzer in einem Verzeichnis
- Festlegen der Mindestanzahl von Überprüfungsschritten (einer oder zwei), die ein Benutzer zum Zurücksetzen des Kennworts durchlaufen muss
- Festlegen der Art von Überprüfungsschritten, die Sie für Benutzer in Ihrer Organisation aktivieren möchten. Es stehen folgende Optionen zur Auswahl:
 - Mobiltelefon (Prüfcode per SMS oder Sprachanruf)
 - Bürotelefon (Sprachanruf)
 - Alternative E-Mail-Adresse (Prüfcode per E-Mail)
 - Sicherheitsfragen (informationsbasierte Authentifizierung)
- Festlegen der Anzahl von Fragen, die ein Benutzer registrieren muss, damit die Authentifizierungsmethode "Sicherheitsfragen" verwenden kann (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Festlegen der Anzahl von Fragen, die ein Benutzer beim Zurücksetzen beantworten muss, um die Authentifizierungsmethode "Sicherheitsfragen" zu verwenden (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Anhand vordefinierter, lokalisierter Sicherheitsfragen, aus denen ein Benutzer beim Registrieren für das Kennwortzurücksetzen auswählen kann (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Anhand der benutzerdefinierten Sicherheitsfragen, die ein Benutzer beim Registrieren für das Kennwortzurücksetzen auswählen kann (nur sichtbar, wenn Sicherheitsfragen aktiviert sind)
- Erzwingen der Benutzerregistrierung für die Kennwortzurücksetzung, wenn Benutzer zum Zugriffsbereich der Anwendung unter [http://myapps.microsoft.com](http://myapps.microsoft.com) wechseln
- Erzwingen der erneuten Bestätigung von zuvor registrierten Daten durch die Benutzer, nachdem eine bestimmte Anzahl von Tagen vergangen ist (nur sichtbar, wenn die erzwungene Registrierung aktiviert ist)
- Bereitstellen einer benutzerdefinierten Helpdesk-E-Mail-Adresse oder -URL, die Benutzern angezeigt wird, wenn beim Zurücksetzen von Kennwörtern Probleme auftreten
- Aktivieren oder Deaktivieren der Kennwortrückschreibung (wenn die Kennwortrückschreibung mithilfe von AAD Connect bereitgestellt wurde)
- Anzeigen des Status für den Kennwortrückschreibung-Agent (wenn die Kennwortrückschreibung mithilfe von AAD Connect bereitgestellt wurde)
- Aktivieren von E-Mail-Benachrichtigungen für Benutzer, wenn ihre eigenen Kennwörter zurückgesetzt wurden (Abschnitt **Benachrichtigungen** im [Azure-Verwaltungsportal](https://manage.windowsazure.com))
- Aktivieren von E-Mail-Benachrichtigungen für Administratoren, wenn andere Administratoren ihre eigenen Kennwörter zurücksetzen (Abschnitt **Benachrichtigungen** im [Azure-Verwaltungsportal](https://manage.windowsazure.com))
- Branding des Portals für das Zurücksetzen von Benutzerkennwörtern und von E-Mails- zur Kennwortzurücksetzung mit Ihrem Unternehmenslogo und -namen über die Anpassungsfunktion für das Mandantenbranding (Abschnitt **Verzeichniseigenschaften** im [Azure-Verwaltungsportal](https://manage.windowsazure.com))

Weitere Informationen zum Konfigurieren der Kennwortverwaltung in Ihrer Organisation finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).

##Portal für die Benutzerregistrierung
Bevor Benutzer ihre Kennwörter zurücksetzen können, müssen ihre Cloudbenutzerkonten mit den richtigen Authentifizierungsdaten aktualisiert werden. Auf diese Weise wird sichergestellt, dass sie die geeignete Anzahl von Überprüfungsschritten für die Kennwortzurücksetzung durchlaufen können, die vom Administrator festgelegt wurde. Administratoren können diese Authentifizierungsinformationen unter Verwendung des Azure- oder Office-Verwaltungsportals, mit DirSync / Azure AD Connect oder Windows PowerShell auch im Namen der Benutzer definieren.

Wenn Sie möchten, dass Benutzer eigene Daten registrieren, wird auch eine Webseite bereitgestellt, auf der Benutzer diese Informationen selbst eingeben können. Auf dieser Seite können Benutzer Authentifizierungsinformationen gemäß den Richtlinien für die Kennwortzurücksetzung eingeben, die in der Organisation aktiviert wurde. Nachdem diese Daten überprüft wurden, werden sie im zugehörigen Cloudbenutzerkonto gespeichert, damit sie zu einem späteren Zeitpunkt wiederhergestellt werden können. Das Registrierungsportal sieht folgendermaßen aus:

  ![][001]

Weitere Informationen finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md) und [Best Practices: Azure AD-Kennwortverwaltung](active-directory-passwords-best-practices.md).

##Portal für das Zurücksetzen von Benutzerkennwörtern
Nachdem Sie die Self-Service-Kennwortzurücksetzung aktiviert haben, richten Sie eine Unternehmensrichtlinie für die Self-Service-Kennwortzurücksetzung ein und stellen sicher, dass Ihre Benutzer über geeignete Kontaktdaten im Verzeichnis verfügen. Benutzer innerhalb der Organisation können ihre eigenen Kennwörter automatisch über eine beliebige Webseite zurücksetzen, die ein Geschäfts- oder Schulkonto für die Anmeldung verwendet (z. B. [portal.microsoftonline.com](https://portal.microsoftonline.com)). Auf Seiten wie dieser wird den Benutzern der Link **Können Sie nicht auf Ihr Konto zugreifen?** angezeigt.

  ![][002]

Durch Klicken auf diesen Link wird das Self-Service-Portal für die Kennwortzurücksetzung gestartet.

  ![][003]

Weitere Informationen dazu, wie Benutzer ihre eigenen Kennwörter zurücksetzen können, finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).

##Portal zum Ändern von Benutzerkennwörtern
Wenn Benutzer ihre eigenen Kennwörter ändern möchten, können sie hierzu jederzeit das Portal zum Ändern von Benutzerkennwörtern verwenden. Benutzer können über die Profilseite im Zugriffsbereich auf das Portal zur Kennwortänderung zugreifen, oder indem sie auf den Link "Kennwort ändern" aus einer Office 365-Anwendung klicken. Wenn ein Kennwort abläuft, werden Benutzer bei der Anmeldung automatisch aufgefordert, ihr Kennwort zu ändern.

  ![][004]

In beiden Fällen werden die geänderten Kennwörter – sofern die Kennwortrückschreibung aktiviert ist und der Benutzer entweder ein Verbundkennwort oder ein synchronisiertes Kennwort verwendet – in das lokale Active Directory zurückgeschrieben. Das Portal zum Ändern von Benutzerkennwörtern sieht folgendermaßen aus:

  ![][005]

Weitere Informationen dazu, wie Benutzer ihre eigenen, lokalen Active Directory-Kennwörter zurücksetzen können, finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).

##Berichte zur Kennwortverwaltung
Wenn Sie zur Registerkarte **Berichte** navigieren und sich den Abschnitt **Aktivitätsprotokolle** ansehen, werden unterhalb dieses Abschnitts zwei Berichte zur Kennwortverwaltung angezeigt: **Aktivität "Zurücksetzen des Kennworts"** und **Aktivität "Registrierung für Zurücksetzen des Kennworts"**. Diese zwei Berichte liefern einen Überblick über Benutzer innerhalb Ihrer Organisation, die sich für das Zurücksetzen von Kennwörtern registrieren und die Kennwortzurücksetzung verwenden. Diese Berichte sehen im [Azure-Verwaltungsportal](https://manage.windowsazure.com) folgendermaßen aus:

  ![][006]

Weitere Informationen finden Sie unter [Operative Einblicke: Kennwortverwaltung in Azure AD](active-directory-passwords-get-insights.md).

##Komponente zur Kennwortrückschreibung von Azure AD Connect
Wenn die Kennwörter von Benutzern in Ihrer Organisation aus Ihrer lokalen Umgebung stammen (Verbund oder Kennwortsynchronisierung), können Sie die aktuelle Version von Azure AD Connect installieren, um diese Kennwörter direkt aus der Cloud zu aktualisieren. Dies bedeutet, dass Benutzer ihre AD-Kennwörter direkt über das Web zurücksetzen oder ändern können, wenn sie sie vergessen haben oder ändern möchten. Die Kennwortrückschreibung wird an dieser Stelle im Installations-Assistenten von Azure AD Connect aktiviert:

  ![][007]

Weitere Informationen zu Azure AD Connect finden Sie unter [Erste Schritte: Azure AD Connect](active-directory-aadconnect.md). Weitere Informationen zum Rückschreiben von Kennwörtern finden Sie unter [Erste Schritte: Azure AD-Kennwortverwaltung](active-directory-passwords-getting-started.md).


<br/> <br/> <br/>

## Links zur Dokumentation für die Kennwortzurücksetzung
Im Folgenden finden Sie Links zu allen Webseiten mit Informationen zur Kennwortzurücksetzung für Azure AD:

* [**Zurücksetzen Ihres eigenen Kennworts**](active-directory-passwords-update-your-own-password.md): Hier erhalten Sie Informationen zum Zurücksetzen oder Ändern Ihres eigenen Kennworts als Benutzer des Systems.
* [**Erste Schritte**](active-directory-passwords-getting-started.md) – Erfahren Sie, wie Sie Benutzern das Zurücksetzen und Ändern ihrer Cloud- oder lokalen Kennwörter erlauben.
* [**Anpassen**](active-directory-passwords-customize.md) – Erfahren Sie, wie Sie das Aussehen und Verhalten des Diensts an die Anforderungen Ihrer Organisation anpassen.
* [**Best Practices**](active-directory-passwords-best-practices.md) – Erfahren Sie, wie Sie Kennwörter in Ihrer Organisation schnell bereitstellen und effektiv verwalten.
* [**Einblicke erhalten**](active-directory-passwords-get-insights.md) – Erfahren Sie mehr über unsere integrierten Berichtsfunktionen.
* [**Häufig gestellte Fragen**](active-directory-passwords-faq.md) – Hier erhalten Sie Antworten auf häufig gestellte Fragen.
* [**Problembehandlung**](active-directory-passwords-troubleshoot.md) – Erfahren Sie, wie Sie Probleme mit dem Dienst schnell beheben.
* [**Weitere Informationen**](active-directory-passwords-learn-more.md) – Erhalten Sie tiefgehende technische Details zur Funktionsweise des Diensts.



[001]: ./media/active-directory-passwords-how-it-works/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-how-it-works/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-how-it-works/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-how-it-works/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-how-it-works/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-how-it-works/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-how-it-works/007.jpg "Image_007.jpg"

<!---HONumber=AcomDC_1125_2015-->