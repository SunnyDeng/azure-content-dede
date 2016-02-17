<properties
	pageTitle="Roaming von Einstellungen und Daten – Häufig gestellte Fragen | Microsoft Azure"
	description="Enthält Antworten auf einige Fragen, die für IT-Administratoren in Bezug auf die Synchronisierung von Einstellungen und App-Daten ggf. interessant sind."
	services="active-directory"
    keywords="Enterprise State Roaming-Einstellungen, Windows-Cloud, Häufig gestellte Fragen zu Enterprise State Roaming"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory"  
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="femila"/>

# Roaming von Einstellungen und Daten – Häufig gestellte Fragen

Dieses Thema enthält Antworten auf einige Fragen, die für IT-Administratoren in Bezug auf die Synchronisierung von Einstellungen und App-Daten unter Umständen interessant sind.

## Für welche Daten wird das Roaming durchgeführt?
**Windows-Einstellungen:**: Die PC-Einstellungen, die in das Windows-Betriebssystem integriert sind. Im Allgemeinen sind dies Einstellungen, mit denen der PC eines Benutzers personalisiert wird. Sie lassen sich in folgende grobe Kategorien unterteilen:

- **Design:** Desktopdesign, Taskleisteneinstellungen usw. 
- **Internet Explorer-Einstellungen:** Zuletzt geöffnete Registerkarten, Favoriten usw.
- **Einstellungen des Edge-Browsers**: Favoriten, Leseliste
- **Kennwörter:** Internet-Kennwörter, WLAN-Profile usw. 
- **Spracheinstellungen:** Tastaturlayouts, Systemsprache, Datum und Uhrzeit usw. 
- **Erleichterte Bedienung:** Design mit hohem Kontrast, Sprachausgabe, Bildschirmlupe usw. 
- **Andere Windows-Einstellungen:** Einstellungen für Eingabeaufforderung, Anwendungsliste usw. 

**Anwendungsdaten:** Universelle Windows-Apps können Einstellungsdaten in einen Ordner „Roaming“ schreiben, und alle Daten, die in diesen Ordner geschrieben werden, werden automatisch synchronisiert. App-Entwickler können eine App entwerfen, um diese Funktion zu nutzen. Weitere Informationen zur Entwicklung einer universellen Windows-App, für die das Roaming verwendet wird, finden Sie im Artikel zur [APP-Daten-API (Speichern und Abrufen von Einstellungen und anderen App-Daten)](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) und im [Entwicklerblog zum Roaming von Windows 8-App-Daten](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## Welches Konto wird für die Einstellungssynchronisierung verwendet?
In Windows 8 und Windows 8.1 wurden für die Einstellungssynchronisierung immer Microsoft-Benutzerkonten verwendet. Unternehmensbenutzer hatten die Möglichkeit, ein Microsoft-Konto mit ihrem Active Directory-Domänenkonto zu verbinden, um Zugriff auf die Einstellungssynchronisierung zu erhalten. Unter Windows 10 wurde diese Funktion des „verbundenen Microsoft-Kontos“ durch das Modell „Framework mit primärem/sekundärem Konto“ ersetzt.
 
Das primäre Konto ist als das Konto definiert, das zum Anmelden an Windows verwendet wird. Dies kann ein Microsoft-Konto sein, ein Azure Active Directory-Konto (Azure AD), ein lokales Active Directory-Konto oder ein lokales Konto. Zusätzlich zum primären Konto können Windows 10-Benutzer ihrem Gerät ein oder mehrere sekundäre Cloudkonten hinzufügen. Bei diesen sekundären Konten handelt es sich im Allgemeinen um ein Microsoft-Konto, Azure Active Directory-Konto oder ein anderes Konto, z. B. Gmail oder Facebook. Diese sekundären Konten ermöglichen den Zugriff auf weitere Dienste, z. B. einmaliges Anmelden und den Windows Store, aber sie sind nicht für die Einstellungssynchronisierung geeignet.

Unter Windows 10 kann nur das primäre Konto für das Gerät für die Einstellungssynchronisierung verwendet werden (siehe Ausnahme unter „Gewusst wie: Aktualisieren von Microsoft-Konto-Einstellungssynchronisierung unter Windows 8 auf Azure AD-Einstellungssynchronisierung unter Windows 10“).

Daten werden in Bezug auf die verschiedenen Benutzerkonten niemals gemischt. Es gibt zwei Regeln für die Synchronisierung von Einstellungen: - Für Windows-Einstellungen wird das Roaming immer mit dem primären Konto durchgeführt. - App-Daten werden mit dem Konto verknüpft, das zum Beschaffen der App verwendet wird. Es werden nur Apps synchronisiert, die mit dem primären Konto verknüpft („getaggt“) sind. Die App-Besitzverknüpfung wird bestimmt, wenn eine App über den Windows Store installiert wird oder wenn die App per mobiler Geräteverwaltung (Mobile Device Management, MDM) quergeladen wird.

Falls der Besitzer einer App nicht identifiziert werden kann, wird das Roaming basierend auf dem primären Konto durchgeführt. Wenn ein Gerät von Windows 8 oder Windows 8.1 auf Windows 10 aktualisiert wird, werden alle Apps so gekennzeichnet, dass sie über das Microsoft-Konto beschafft wurden. Normalerweise werden die meisten Apps über den Windows Store beschafft, und vor Windows 10 gab es keine Windows Store-Unterstützung für Azure AD-Konten. Wenn eine App per Offlinelizenz installiert wird, wird die App mit dem primären Konto auf dem Gerät verknüpft.

>[AZURE.NOTE]  
Die Möglichkeit zum Verbinden eines Microsoft-Kontos mit einem Domänenkonto und Synchronisieren aller Daten des Benutzers mit dem Microsoft-Konto (also das Microsoft-Konto-Roaming mit der Funktion „Verbindung von Microsoft-Konto und Active Directory”) wird für Windows 10-Geräte entfernt, für die der Beitritt zu einer verbundenen Active Directory/Azure AD-Umgebung durchgeführt wird.
 
## Gewusst wie: Aktualisieren von Microsoft-Konto-Einstellungssynchronisierung unter Windows 8 auf Azure AD-Einstellungssynchronisierung unter Windows 10
Für einen Benutzer, der der Active Directory-Domäne beigetreten ist und Windows 8 oder Windows 8.1 mit einem verbundenen Microsoft-Konto ausführt, werden Einstellungen über das Microsoft-Konto synchronisiert. Nach der Aktualisierung auf Windows 10 werden für Benutzer, die der Domäne beigetreten sind, die Benutzereinstellungen weiterhin über das Microsoft-Konto synchronisiert, solange für die Active Directory-Domäne keine Verbindung mit Azure AD hergestellt wird. Falls für die lokale Active Directory-Domäne doch eine Verbindung mit Azure AD hergestellt wird, beginnt das Gerät des Benutzers mit dem Versuch, die Einstellungen über das verbundene Azure AD-Konto zu synchronisieren. Wenn Enterprise State Roaming vom Azure AD-Administrator nicht aktiviert wird, wird das Synchronisieren von Einstellungen für Benutzer mit einem verbundenen Azure AD-Konto beendet. Nachdem die Synchronisierung von Einstellungen über Azure AD aktiviert wurde, wird für Windows 10-Benutzer sofort mit dem Synchronisieren von Windows-Einstellungen per Azure AD begonnen, wenn sich die Benutzer mit einer Azure AD-Identität anmelden.

Benutzer, die persönliche Daten auf ihren Unternehmensgeräten gespeichert haben, sollten sich darüber im Klaren sein, dass für die Daten des Windows-Betriebssystems und der -Anwendungen die Synchronisierung mit Azure AD durchgeführt wird. Dies hat folgende Auswirkungen: - Für Benutzer mit persönlichen Microsoft-Konten „entfernen“ sich die Einstellungen nach und nach von den Einstellungen der Azure AD-Unternehmenskonten, da für die Synchronisierung der Microsoft-Konto- und Azure AD-Einstellungen jetzt separate Konten verwendet werden. - Persönliche Daten, z. B. WLAN-Kennwörter, Webanmeldeinformationen und Internet Explorer-Favoriten und -Registerkarten, die bisher über das verbundene Microsoft-Konto synchronisiert wurden, werden nun per Azure AD synchronisiert.


## Wie funktioniert die Enterprise State Roaming-Interoperabilität zwischen Microsoft- und Azure AD-Konten? 
In den 2015er Versionen von Windows 10 wird Enterprise State Roaming nur für jeweils ein Konto unterstützt. Wenn sich Benutzer an Windows mit einem Azure AD-Unternehmenskonto anmelden, werden alle Daten über Azure AD synchronisiert. Wenn sich Benutzer an Windows mit einem persönlichen Microsoft-Konto anmelden, werden alle Daten über das Microsoft-Konto synchronisiert. Für App-Daten wird das Roaming mit dem primären Anmeldekonto auf dem Gerät durchgeführt, wenn sich die Lizenz der App im Besitz des primären Kontos befindet. Apps, die sich im Besitz von sekundären Konten befinden, führen keine Synchronisierung von App-Daten unter Windows 10 durch.

## Können Einstellungen für Azure AD-Konten mehrerer Mandanten synchronisiert werden?
Wenn sich mehrere Azure AD-Konten von unterschiedlichen Azure AD-Mandanten auf demselben Gerät befinden, müssen Sie die Registrierung des Geräts aktualisieren, um mit dem Dienst „Azure Rights Management Service (RMS)“ für jeden Azure AD-Mandanten zu kommunizieren.

1. Zuerst benötigen Sie die GUID für jeden Azure AD-Mandanten. Öffnen Sie das klassische Azure-Portal, und wählen Sie einen Azure AD-Mandanten aus. Die GUID für den Mandanten ist in der URL in der Adressleiste des Browsers enthalten: `https://manage.windowsazure.com/YourAccount.onmicrosoft.com#Workspaces/ActiveDirectoryExtension/Directory/Tenant GUID/directoryQuickStart`
2. Nachdem Sie die GUID ermittelt haben, müssen Sie den folgenden Registrierungsschlüssel hinzufügen: **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\SettingSync\\WinMSIPC<Mandanten-ID-GUID>**. Erstellen Sie unter dem Schlüssel <**Mandanten-ID-GUID**> einen neuen mehrteiligen Zeichenfolgenwert (REG-MULTI-SZ) mit dem Namen **AllowedRMSServerUrls**, und geben Sie als Daten hierfür die Lizenzierungsverteilungspunkt-URLs der anderen Azure-Mandanten an, auf die das Gerät zugreift. 
3. Sie können die Lizenzierungsverteilungspunkt-URLs ermitteln, indem Sie das **Get-AadrmConfiguration**-Cmdlet ausführen. Falls sich die Werte für **LicensingIntranetDistributionPointUrl** und **LicenseingExtranetDistributionPointUrl** unterscheiden, müssen Sie beide Werte angeben. Wenn die Werte gleich sind, müssen Sie den Wert nur einmal angeben.


## Welche Optionen gibt es für das Roaming der Einstellungen für klassische Windows-Anwendungen?
Roaming funktioniert nur für universelle Windows-Apps. Es sind zwei Optionen verfügbar, um das Roaming für eine klassische Windows-Anwendung zu ermöglichen:

- Mit Project Centennial können Sie eine klassische Windows-Anwendung leicht in eine universelle Windows-App konvertieren. Ab diesem Punkt müssen App-Entwickler minimale Codeänderungen vornehmen, um das Roaming von Azure AD-App-Daten zu nutzen. Dies ist die empfohlene Vorgehensweise, um das Roaming von Win32-App-Daten zu ermöglichen. 
- Mit dem Vorlagen-Generator [User Experience Virtualization (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) (Virtualisierung der Benutzerfreundlichkeit) können Sie eine benutzerdefinierte Vorlage mit Einstellungen erstellen, die bei jedem Start auf Ihre App angewendet wird. Bei dieser Option müssen App-Entwickler den Code der App nicht ändern. In den 2015er Versionen von Windows 10 ist UE-V aber auf das lokale Active Directory-Roaming für Kunden beschränkt, die das Microsoft Desktop Optimization Package erworben haben. Es ist möglich, dass Microsoft in Zukunft nach Wegen suchen wird, wie UE-V fest in Windows integriert und erweitert werden kann, um das Roaming von Einstellungen über die Azure AD-Cloud zu ermöglichen. 

## Können synchronisierte Einstellungen und Daten lokal gespeichert werden?
Enterprise State Roaming ist dafür ausgelegt, dass Einstellungsdaten in der Azure-Cloud gespeichert werden. UE-V verfügt über eine Lösung für lokales Roaming für Windows 10.

## Von wem werden Daten verwaltet, für die das Roaming durchgeführt wird?
Der Mandantenadministrator verwaltet die Daten, und die Daten werden in einem Azure-Rechenzentrum gespeichert. Alle Benutzerdaten sind sowohl bei der Übertragung als auch im Ruhezustand in der Cloud per Azure Rights Management (Azure RMS) verschlüsselt. Dies ist eine Verbesserung im Vergleich zur Synchronisierung von Einstellungen, die auf Microsoft-Konten basieren, da dabei nur bestimmte vertrauliche Daten, z. B. Anmeldeinformationen von Benutzern, verschlüsselt werden, bevor sie das Gerät verlassen.

Microsoft liegt der Schutz von Kundendaten am Herzen. Die Einstellungsdaten eines Unternehmensbenutzers werden immer automatisch per Azure RMS verschlüsselt, wenn sie ein Windows 10-Gerät verlassen, damit andere Benutzer diese Daten nicht lesen können. Wenn Ihr Unternehmen über ein kostenpflichtiges Abonnement für Azure RMS verfügt, können Sie weitere Azure RMS-Features nutzen, z. B. Nachverfolgen und Widerrufen von Dokumenten, automatisches Schützen von E-Mails mit vertraulichen Informationen und Verwalten Ihrer eigenen Schlüssel (Lösung „Bring Your Own Key“ (BYOK)). Unter [Was ist Azure Rights Management?](https://technet.microsoft.com/jj585026.aspx) finden Sie weitere Informationen zu diesen Features und zur Funktionsweise von Azure RMS.

## Kann ich die Synchronisierung für eine bestimmte Anwendung oder Einstellung verwalten?
In Windows 10 gibt es keine MDM- oder Gruppenrichtlinien-Einstellung, mit der das Roaming für eine einzelne App deaktiviert werden kann. Mandantenadministratoren können die Synchronisierung von App-Daten für alle Apps auf einem verwalteten Gerät deaktivieren, aber es ist keine präzisere Steuerung auf App-Ebene oder in den Apps vorhanden.

## Was können einzelne Benutzer tun, um das Roaming zu aktivieren oder zu deaktivieren?
Navigieren Sie in der App „Einstellungen“ zu „Konten“ -> „Einstellungen synchronisieren“. Auf dieser Seite sehen Sie, welches Konto für das Roaming von Einstellungen verwendet wird, und Sie können einzelne Gruppen mit Einstellungen für das Roaming aktivieren oder deaktivieren.
 
##Was wird von Microsoft in Bezug auf die Aktivierung des Roamings in Windows 10 derzeit empfohlen? 
Microsoft bietet verschiedene Lösungen für das Roaming von Einstellungen an, z. B. servergespeicherte Benutzerprofile (Roaming User Profiles), UE-V und Azure AD Roaming. Wenn Ihr Unternehmen noch nicht für das Verschieben von Daten in die Cloud bereit ist oder noch Bedenken bestehen, empfiehlt Microsoft die Verwendung von UE-V als primäre Roamingtechnologie. Falls Ihr Unternehmen Roamingunterstützung für klassische Windows-Anwendungen benötigt, aber an der schnellen Umstellung auf die Cloud interessiert ist, empfiehlt Microsoft die parallele Verwendung der Synchronisierung von Unternehmenseinstellungen und von UE-V. UE-V und die Synchronisierung von Unternehmenseinstellungen sind zwar sehr ähnliche Technologien, aber sie schließen sich nicht gegenseitig aus. Heutzutage ergänzen sie einander, um sicherzustellen, dass Ihr Unternehmen die Roamingdienste bereitstellen kann, die Ihre Benutzer benötigen. Bei der parallelen Nutzung der Synchronisierung von Unternehmenseinstellungen und von UE-V gelten die folgenden Regeln:

- Enterprise State Roaming ist der primäre Roaming-Agent auf dem Gerät. UE-V wird zum Schließen der „Win32-Lücke“ verwendet. 
- Das UE-V-Roaming für Windows-Einstellungen und moderne UWP-App-Daten sollte deaktiviert werden, da diese Aufgaben bereits durch das Enterprise State Roaming abgedeckt werden. 

## Was passiert, wenn mein Unternehmen Azure RMS erwirbt, nachdem es eine Zeit lang das Roaming genutzt hat? 
Falls Ihr Unternehmen das Roaming unter Windows 10 bereits über das kostenlose Azure RMS-Abonnement nutzt, hat der Erwerb eines kostenpflichtigen Azure RMS-Abonnements keinerlei Auswirkungen auf die Funktionen des Roamingfeatures, und Ihr IT-Administrator muss keine Änderungen an der Konfiguration vornehmen.

## Bekannte Probleme

- Das Anmelden per Smartcard oder virtueller Smartcard an Windows führt dazu, dass die Synchronisierung von Einstellungen nicht mehr funktioniert. Wenn Sie versuchen, sich mit einer Smartcard oder einer virtuellen Smartcard an Ihrem Gerät anzumelden, funktioniert die Synchronisierung nicht mehr. Dieses Problem wird im Rahmen von zukünftigen Updates für Windows 10 voraussichtlich behoben.
- Es besteht ein Problem mit der Synchronisierung von **Favoriten** in Internet Explorer. Die Synchronisierung von IE-Favoriten wurde vorübergehend deaktiviert, um das Entstehen weiterer Probleme zu verhindern. Dieses Problem wird im Rahmen von zukünftigen Updates für Windows 10 behoben.


## Verwandte Themen
- [Übersicht über Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-overview.md)
- [Aktivieren von Enterprise State Roaming in Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Gruppenrichtlinien- und MDM-Einstellungen für Einstellungssynchronisierung](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
- [Windows 10-Roamingeinstellungen – Referenz](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)

<!---HONumber=AcomDC_0204_2016-->