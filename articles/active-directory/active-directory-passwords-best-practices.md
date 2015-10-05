<properties
	pageTitle="Best Practices: Azure AD-Kennwortverwaltung | Microsoft Azure"
	description="Best Practices für die Bereitstellung und Nutzung, Beispieldokumentation für Endbenutzer und Schulungshandbücher für die Kennwortverwaltung in Azure Active Directory."
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
	ms.date="09/18/2015" 
	ms.author="asteen"/>

# Bereitstellen der Kennwortverwaltung und Schulen der Benutzer zu deren Verwendung
Nachdem Sie die Kennwortzurücksetzung aktiviert haben, müssen Sie als Nächstes die Benutzer dazu anhalten, diesen Dienst in Ihrer Organisation zu verwenden. Zu diesem Zweck müssen Sie sicherstellen, dass Ihre Benutzer so konfiguriert sind, dass sie den Dienst ordnungsgemäß verwenden können. Außerdem müssen die Benutzer für die Verwaltung ihrer eigenen Kennwörter geschult werden. In diesem Artikel werden die folgenden Konzepte erläutert:

* [**Konfigurieren Ihrer Benutzer für die Kennwortverwaltung**](#how-to-get-users-configured-for-password-reset)
  * [Erforderliche Konfiguration für die Kontozurücksetzung](#what-makes-an-account-configured)
  * [Methoden zum selbstständigen Auffüllen der Authentifizierungsdaten](#ways-to-populate-authentication-data)
* [**Die besten Methoden zum Einführen der Kennwortzurücksetzung in Ihrer Organisation**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [E-Mail-basierte Einführung und Beispiele für die E-Mail-Kommunikation](#email-based-rollout)
  * [Verwenden der erzwungenen Registrierung zum Durchsetzen der Benutzerregistrierung bei der Anmeldung](#using-enforced-registration)
  * [Hochladen von Authentifizierungsdaten für Benutzerkonten](#uploading-data-yourself)
* [**Beispielschulungsunterlagen für Benutzer und Support (demnächst verfügbar!)**](#sample-training-materials)

## Konfigurieren von Benutzern für die Kennwortzurücksetzung
Dieser Abschnitt beschreibt verschiedene Methoden, mit denen Sie sicherstellen können, dass alle Benutzer in Ihrer Organisation ihre Kennwörter selbstständig und effektiv zurücksetzen können, falls sie ihre Kennwörter vergessen haben.

### Erforderliche Kontokonfiguration
Bevor ein Benutzer die Kennwortzurücksetzung verwenden kann, müssen **alle** folgenden Bedingungen erfüllt sein:

1.	Die Kennwortzurücksetzung muss im Verzeichnis aktiviert sein. Informationen zum Aktivieren der Kennwortzurücksetzung finden Sie unter [Aktivieren von Benutzern für das Zurücksetzen ihrer Azure AD-Kennwörter](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) oder unter [Aktivieren von Benutzern für das Zurücksetzen ihrer AD-Kennwörter](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords).
2.	Der Benutzer muss lizenziert sein.
 - Für Cloudbenutzer muss dem Benutzer **eine beliebige kostenpflichtige Office 365-Lizenz** oder eine **AAD Basic**- oder eine **AAD Premium-Lizenz** zugewiesen sein.
 - Für lokale Benutzer (Verbund oder hashsynchronisiert) muss dem Benutzer **eine AAD Premium-Lizenz zugewiesen sein**.
3.	Für den Benutzer muss der **Mindestsatz an Authentifizierungsdaten definiert** sein in Übereinstimmung mit der aktuellen Kontozurücksetzungsrichtlinie.
 - Authentifizierungsdaten gelten als definiert, wenn das entsprechende Feld im Verzeichnis wohlgeformte Daten enthält.
 - Ein Mindestsatz von Authentifizierungsdaten ist definiert als **mindestens eine** der aktivierten Authentifizierungsoptionen, wenn eine Richtlinie für die einstufige Überprüfung konfiguriert ist, bzw. **mindestens zwei** der aktivierten Authentifizierungsoptionen, wenn eine Richtlinie für die zweistufige Überprüfung konfiguriert ist.
4.	Wenn der Benutzer ein lokales Konto verwendet, muss die [Kennwortrückschreibung](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) aktiviert sein.

### Möglichkeiten zum Auffüllen von Authentifizierungsdaten
Sie haben mehrere Möglichkeiten, für Organisationsbenutzer Daten einzugeben, die für das Zurücksetzen von Kennwörtern verwendet werden.

- Bearbeiten Sie Benutzer im [Azure-Verwaltungsportal](https://manage.windowsazure.com) oder im [Office 365-Verwaltungsportal](https://portal.microsoftonline.com).
- Verwenden Sie AADSync, um Benutzereigenschaften aus einer lokalen Active Directory-Domäne mit Azure AD zu synchronisieren.
- Verwenden Sie Windows PowerShell zum Bearbeiten der Benutzereigenschaften.
- Ermöglichen Sie es Benutzern, ihre eigenen Daten zu registrieren, indem Sie sie auf das Registrierungsportal unter [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) weiterleiten.
- Legen Sie die Benutzerregistrierung für die Kennwortzurücksetzung bei Anmeldung beim Zugriffsbereich unter [http://myapps.microsoft.com](http://myapps.microsoft.com) als erforderlich fest, indem Sie die Konfigurationsoption **Sollen sich Benutzer für die Self-Service-Kennwortzurücksetzung registrieren müssen?** auf **Ja** festlegen.

Benutzer müssen sich für die Kennwortzurücksetzung nicht registrieren, damit das System funktioniert. Wenn Sie beispielsweise über vorhandene Rufnummern in Ihrem lokalen Verzeichnis verfügen, können Sie diese in Azure AD synchronisieren, und wir verwenden sie automatisch für die Kennwortzurücksetzung.

## Welche Möglichkeit ist am besten zum Einführen der Kennwortzurücksetzung für Benutzer geeignet?
Im Folgenden finden Sie allgemeine Schritte zur Einführung der Kennwortrücksetzung:

1.	Aktivieren Sie die Kennwortzurücksetzung in Ihrem Verzeichnis, indem Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) auf der Registerkarte **Konfigurieren** die Option **Benutzer, für die das Zurücksetzen des Kennworts aktiviert ist** auf **Ja** setzen.
2.	Weisen Sie jedem Benutzer, dem Sie die Kennwortzurücksetzung bieten möchten, auf der Registerkarte **Lizenzen** im [Azure-Verwaltungsportal](https://manage.windowsazure.com) die entsprechenden Lizenzen zu.
3.	Beschränken Sie die Kennwortzurücksetzung optional auf eine Gruppe von Benutzern, um das Feature langsam über einen gewissen Zeitraum einzuführen, indem Sie die Option **Zugriff auf die Kennwortrücksetzung beschränken** auf **Ja** festlegen und eine Sicherheitsgruppe für das Aktivieren der Kennwortzurücksetzung auswählen. (Beachten Sie, dass all diesen Benutzern Lizenzen zugewiesen sein müssen.)
4.	Weisen Sie die Benutzer zum Verwenden der Kennwortzurücksetzung an, indem Sie ihnen entweder eine E-Mail mit Anweisungen zur Registrierung senden und die erzwungene Registrierung im Zugriffsbereich aktivieren, oder indem Sie die entsprechenden Authentifizierungsdaten für diese Benutzer selbst über DirSync, PowerShell oder das [Azure-Verwaltungsportal](https://manage.windowsazure.com) hochladen. Weitere Informationen hierzu finden Sie im Folgenden.
5.	Überprüfen Sie die sich im Laufe der Zeit registrierenden Benutzer, indem Sie auf der Registerkarte "Berichte" den Bericht [**Aktivität "Registrierung für Zurücksetzen des Kennworts"**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity) anzeigen.
6.	Sobald sich zahlreiche Benutzer registriert haben, beobachten Sie sie bei der Verwendung der Kennwortzurücksetzung, indem Sie auf der Registerkarte "Berichte" den Bericht [**Aktivität "Zurücksetzen des Kennworts"**](active-directory-passwords-get-insights.md#view-password-reset-activity) anzeigen.

Es gibt mehrere Möglichkeiten, die Benutzer darüber informieren, dass sie sich in Ihrer Organisation für die Kennwortzurücksetzung registrieren und diese verwenden können. Sie werden nachfolgend beschrieben.

### E-Mail-basierte Einführung
Der einfachste Ansatz zum Informieren Ihrer Benutzer über die Registrierung für die Kennwortzurücksetzung oder über deren Verwendung besteht wahrscheinlich darin, ihnen eine E-Mail mit entsprechenden Anweisungen zu senden. Unten sehen Sie eine Vorlage, die Sie zu diesem Zweck verwenden können. Gerne können Sie die Farben / Logos durch eigene ersetzen und die Vorlage nach Ihren spezifischen Anforderungen anpassen.

  ![][001]

Sie können die E-Mail-Vorlage [hier](http://1drv.ms/1xWFtQM) herunterladen.

### Verwenden der erzwungenen Registrierung
Wenn Ihre Benutzer sich selbst für die Kennwortzurücksetzung registrieren sollen, können Sie sie auch zur Registrierung zwingen, sobald sie sich im Zugriffsbereich unter [http://myapps.microsoft.com](http://myapps.microsoft.com) anmelden. Aktivieren Sie diese Option auf der Registerkarte **Konfigurieren** des Verzeichnisses, indem Sie die Option **Sollen sich Benutzer bei der Anmeldung im Zugriffsbereich registrieren müssen?** aktivieren.

Sie können optional auch definieren, ob sie nach einem konfigurierbaren Zeitraum zur Neuregistrierung aufgefordert werden, indem Sie die Option **Anzahl der Tage, bis Benutzer ihre Kontaktdaten bestätigen müssen** auf einen Wert ungleich null festlegen. Weitere Informationen finden Sie unter [Anpassen des Verhaltens der Benutzerkennwortverwaltung](active-directory-passwords-customize.md#password-management-behavior)

  ![][002]

Nachdem Sie diese Option aktiviert haben, wird den Benutzern bei der Anmeldung im Zugriffsbereich ein Popupfenster angezeigt, das sie über die vom Administrator vorgegebene Notwendigkeit zur Prüfung ihrer Kontaktinformationen informiert. Sie können darüber ihr Kennwort zurücksetzen, falls sie jemals den Zugriff auf ihr Konto verlieren.

  ![][003]

Durch Klicken auf **Jetzt überprüfen** gelangen sie zum **Registrierungsportal für die Kennwortzurücksetzung** unter [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup). Dort müssen sie sich registrieren. Die Registrierung über diese Methode kann durch Klicken auf die Schaltfläche **Abbrechen** oder durch Schließen des Fensters verworfen werden. Benutzer werden jedoch bei jeder Anmeldung daran erinnert, wenn sie sich nicht registrieren.

  ![][004]

### Selbstständiges Hochladen von Daten
Wenn Sie Authentifizierungsdaten selbst hochladen möchten, müssen Benutzer sich nicht für die Kennwortzurücksetzung registrieren, damit sie ihre Kennwörter zurücksetzen können. Solange in den Konten der Benutzer Authentifizierungsdaten definiert sind, die der von Ihnen definierten Richtlinie für die Kontozurücksetzung entsprechen, können diese Benutzer ihre Kennwörter zurücksetzen.

Informationen zu den Eigenschaften, die Sie über AAD Connect oder Windows PowerShell festlegen können, finden Sie unter [Von der Kennwortzurücksetzung verwendete Daten](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Sie können die Authentifizierungsdaten über das [Azure-Verwaltungsportal](https://manage.windowsazure.com) hochladen, indem Sie die folgenden Schritte durchführen:

1.	Navigieren Sie im **Azure-Verwaltungsportal** in der [Active Directory-Erweiterung](https://manage.windowsazure.com) zu Ihrem Verzeichnis.
2.	Klicken Sie auf die Registerkarte **Benutzer**.
3.	Wählen Sie den für Sie relevanten Benutzer aus der Liste aus.
4.	Auf der ersten Registerkarte finden Sie **Alternative E-Mail-Adresse**, die als Eigenschaft zum Aktivieren der Kennwortzurücksetzung verwendet werden kann.

    ![][005]

5.	Klicken Sie auf die Registerkarte **Arbeitsinformationen**.
6.	Auf dieser Seite finden Sie **Bürotelefon**, **Mobiltelefon**, **Telefon für Authentifizierung** und **Authentifizierungs-E-Mail**. Diese Eigenschaften können auch so festgelegt werden, dass sie einem Benutzer das Zurücksetzen des Kennworts ermöglichen.

    ![][006]

Unter [Von der Kennwortzurücksetzung verwendete Daten](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) erfahren Sie, wie die einzelnen Eigenschaften verwendet werden können.

## Beispielschulungsmaterial
Wir arbeiten an Beispielschulungsmaterial, mit dem Sie Ihre IT-Organisation und Ihre Benutzer im Hinblick auf die Bereitstellung und Verwendung der Kennwortzurücksetzung schnell auf den neuesten Stand bringen können. Halten Sie sich auf dem Laufenden.


<br/> <br/> <br/>

**Zusätzliche Ressourcen**


* [Was ist die Kennwortverwaltung?](active-directory-passwords.md)
* [Funktionsweise der Kennwortverwaltung](active-directory-passwords-how-it-works.md)
* [Erste Schritte mit der Kennwortverwaltung](active-directory-passwords-getting-started.md)
* [Anpassen der Kennwortverwaltung](active-directory-passwords-customize.md)
* [Operative Einblicke durch Berichte zur Kennwortverwaltung](active-directory-passwords-get-insights.md)
* [Häufig gestellte Fragen zur Kennwortverwaltung](active-directory-passwords-faq.md)
* [Problembehandlung für die Kennwortverwaltung](active-directory-passwords-troubleshoot.md)
* [Weitere Informationen](active-directory-passwords-learn-more.md)
* [Kennwortverwaltung auf MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"

<!---HONumber=Sept15_HO4-->