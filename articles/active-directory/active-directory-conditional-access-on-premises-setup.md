<properties
	pageTitle="Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung | Microsoft Azure"
	description="Eine Schrittanleitung, die zeigt, wie Sie mithilfe der Active Directory-Verbunddienste (AD FS) in Windows Server 2012 R2 den bedingten Zugriff auf lokale Anwendungen ermöglichen."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# Einrichten des lokalen bedingten Zugriffs mithilfe der Azure Active Directory-Geräteregistrierung

Eigene Geräte der Benutzer können als Ihrer Organisation bekannt markiert werden. Dazu müssen die Benutzer für ihre Geräte eine Arbeitsbereichverknüpfung mit dem Geräteregistrierungsdienst für Azure Active Directory durchführen. Die folgende Schrittanleitung zeigt, wie Sie mithilfe der Active Directory-Verbunddienste (AD FS) in Windows Server 2012 R2 den bedingten Zugriff auf lokale Anwendungen ermöglichen.

> [AZURE.NOTE]Wenn Geräte verwendet werden, die über den Azure Active Directory-Geräteregistrierungsdienst registriert und mit Richtlinien für den bedingten Zugriff konfiguriert sind, wird eine Office 365-Lizenz oder eine Azure AD Premium-Lizenz benötigt. Hierzu gehören auch Richtlinien, die von den Active Directory-Verbunddiensten (AD FS) für lokale Ressourcen erzwungen werden.

Weitere Informationen zu den Szenarien für den lokalen bedingten Zugriff finden Sie unter [Arbeitsplatzbeitritt von einem beliebigen Gerät für SSO und die nahtlose zweistufige Authentifizierung bei allen Unternehmensanwendungen](https://technet.microsoft.com/library/dn280945.aspx). Diese Funktionen sind für Kunden verfügbar, die eine Azure Active Directory Premium-Lizenz erwerben.

Unterstützte Geräte
-------------------------------------------------------------------------
* Windows 7-Geräte, die einer Domäne angehören.
* Persönliche Windows 8.1-Geräte und Windows 8.1-Geräte, die einer Domäne angehören.
* Geräte mit iOS 6 und höher.
* Telefone mit Android 4.0 oder höher, Samsung GS3 oder höher, Tablets mit Samsung Note2 oder höher.

Voraussetzungen für die einzelnen Szenarien
------------------------------------------------------------------------
* Windows Server Active Directory (Windows Server 2008 oder höher)
* Aktualisiertes Schema in Windows Server 2012 R2
* Azure Active Directory Premium-Abonnement
* Windows Server 2012 R2-Verbunddienste, konfiguriert für SSO in Azure AD
* Windows Server 2012 R2-Webanwendungsproxy
* Tool zur Verzeichnissynchronisierung (DirSync) mit Zurückschreiben von Geräteobjekten. [Laden Sie den neuesten Build von "dirsync.exe" hier herunter.](http://go.microsoft.com/fwlink/?LinkID=278924)

Bekannte Probleme in dieser Version
-------------------------------------------------------------------------------
* Gerätebasierte bedingte Zugriffsrichtlinien erfordern das Zurückschreiben von Geräteobjekten aus Azure Active Directory nach Active Directory. Es kann bis zu drei Stunden dauern, bis Geräteobjekte nach Active Directory zurückgeschrieben werden.
* iOS7-Geräte fordern den Benutzer immer auf, während der Zertifikatauthentifizierung ein Zertifikat auszuwählen. 
* Geräte mit iOS8 Beta funktionieren zurzeit nicht.


## Bereitstellen des Azure Active Directory-Geräteregistrierungsdiensts
Verwenden Sie diese Anleitung, um den Azure Active Directory-Geräteregistrierungsdienst für Ihre Organisation bereitzustellen und zu konfigurieren.

In diesem Handbuch wird vorausgesetzt, dass Sie Windows Server Active Directory konfiguriert und Microsoft Azure Active Directory abonniert haben. Weitere Informationen zu den Voraussetzungen finden Sie oben.

Wenn Sie den Azure Active Directory-Geräteregistrierungsdienst für Ihren Azure Active Directory-Mandanten bereitstellen möchten, führen Sie die in der folgenden Prüfliste aufgeführten Aufgaben in der angegebenen Reihenfolge aus. Wenn ein Ressourcenlink auf ein grundlegendes Thema verweist, kehren Sie nach der Durchsicht des grundlegenden Themas zu dieser Prüfliste zurück, um die verbleibenden Aufgaben in dieser Prüfliste auszuführen. Einige Aufgaben erfordern einen Überprüfungsschritt für das Szenario, mit dem bestätigt werden kann, dass der Schritt erfolgreich abgeschlossen wurde.

**Prüfliste: Aktivieren der Azure Active Directory-Geräteregistrierung**

Führen Sie die Aufgaben in der Prüfliste unten aus, um den Azure Active Directory-Geräteregistrierungsdienst zu aktivieren und zu konfigurieren.

| Aufgabe | Referenz |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Aktivieren Sie Geräteregistrierung in Ihrem Azure Active Directory-Mandanten, damit Geräte dem Arbeitsplatz beitreten können. Standardmäßig ist die mehrstufige Authentifizierung nicht für den Dienst aktiviert. Es wird jedoch empfohlen, beim Registrieren eines Geräts die mehrstufige Authentifizierung zu verwenden. Stellen Sie vor dem Aktivieren der mehrstufigen Authentifizierung in ADRS sicher, dass AD FS für einen MFA-Anbieter (Multi-Factor Authentication) konfiguriert ist. | [Aktivieren der Azure Active Directory-Geräteregistrierung](active-directory-conditional-access-device-registration-overview.md) |
| Geräte ermitteln den Azure Active Directory-Geräteregistrierungsdienst, indem sie nach bekannten DNS-Einträgen suchen. Sie müssen den DNS-Eintrag für Ihr Unternehmen so konfigurieren, dass Geräte den Azure Active Directory-Geräteregistrierungsdienst ermitteln können. | [Konfigurieren der Ermittlung für die Azure Active Directory-Geräteregistrierung](active-directory-conditional-access-device-registration-overview.md) |

**Prüfliste: Bereitstellen und Konfigurieren der Windows Server 2012 R2 Active Directory-Verbunddienste und Einrichten einer Verbundbeziehung mit Azure Active Directory** Mithilfe der folgenden Aufgaben integrieren Sie die Windows Server 2012 R2-Verbunddienste in Azure Active Directory. Auf diese Weise können Sie in AD FS Geräte verwenden, die im Azure Active Directory-Geräteregistrierungsdienst für Richtlinien für den bedingten Zugriff registriert sind.


| Aufgabe | Referenz |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Bereitstellen der Active Directory-Domänendienste mit den Windows Server 2012 R2-Schemaerweiterungen. (Es ist nicht erforderlich, ein Upgrade Ihrer Domänencontroller auf Windows Server 2012 R2 auszuführen. Das Upgrade des Schemas ist die einzige Anforderung.) | Ausführen eines Upgrades des Schemas der Active Directory-Domänendienste |
| Bereitstellen der Windows Server 2012 R2-Verbunddienste zusammen mit dem Webanwendungsproxy. | Bereitstellen der Windows Server 2012 R2-Verbunddienste zusammen mit dem Webanwendungsproxy. |
| Ihre Gesamtstruktur der Active Directory-Domänendienste muss mit den Objekten und Containern konfiguriert sein, die für die Unterstützung von Geräteobjekten erforderlich sind. Aktivieren Sie außerdem die Geräteauthentifizierung in AD FS. | Vorbereiten der Active Directory-Gesamtstruktur für die Unterstützung von Geräten |
| Einrichten einer Verbundbeziehung zwischen Ihrer Organisation und Azure Active Directory. In diesem Schritt werden Sie durch die Konfiguration Ihres Azure Active Directory-Mandanten mit den Windows Server 2012 R2-Verbunddiensten geleitet. | Konfigurieren einer Verbundbeziehung mit Azure Active Directory und Konfigurieren des Verzeichnissynchronisierungstools |
| Konfigurieren der Verzeichnissynchronisierung (DirSync) mit Zurückschreiben der Geräteobjekte. In Azure Active Directory erstellte Geräte werden in Ihr Active Directory-Verzeichnis zurückgeschrieben. | Konfigurieren von DirSync mit Zurückschreiben des Geräteobjekts |
| Es wird dringend empfohlen, einen Anbieter für die mehrstufige Authentifizierung mit den Windows Server 2012 R2-Verbunddiensten zu konfigurieren. Auf diese Weise können Ihre Benutzer ihre Geräte mithilfe einer mehrstufigen Authentifizierung sicher registrieren. | Konfigurieren von Verbunddiensten für die Bereitstellung von Multi-Factor Authentication (MFA) |


### Ausführen eines Upgrades des Schemas der Active Directory-Domänendienste
> [AZURE.NOTE]Das Upgrade des Active Directory-Schemas kann nicht rückgängig gemacht werden. Es wird empfohlen, dieses Upgrade in einer Testumgebung auszuführen.

1. Melden Sie sich an Ihrem Domänencontroller mit einem Konto an, das über die Rechte "Unternehmensadministrator" und "Schemaadministrator" verfügt.
2. Kopieren Sie das Verzeichnis "[medien]\\support\\adprep" und die Unterverzeichnisse auf einen Ihrer Active Directory-Domänencontroller. 
3. Hierbei steht "[medien]" für den Pfad zum Windows Server 2012 R2-Installationsmedium.
4. Navigieren Sie an einer Eingabeaufforderung zum Verzeichnis "adprep", und führen Sie dann den folgenden Befehl aus: adprep.exe /forestprep. Folgen Sie den Anweisungen auf dem Bildschirm, um das Upgrade des Schemas abzuschließen.

### Bereitstellen der Windows Server 2012 R2-Verbunddienste zusammen mit dem Webanwendungsproxy
Folgen Sie den Anweisungen in den zwei nachstehend genannten Handbüchern, um die Windows Server 2012 R2-Verbunddienste mit dem Webanwendungsproxy bereitzustellen. Kehren Sie dann zu diesem Handbuch zurück. Folgen Sie den Anweisungen zum Bereitstellen einer Windows Server 2012 R2-Verbundserverfarm.

> [AZURE.NOTE]Wenn Sie AD FS bereitstellen, müssen Sie den optionalen Schritt "Konfigurieren eines Verbundservers mit dem Geräteregistrierungsdienst" überspringen. Dieser Schritt ist nicht erforderlich, wenn Sie die Azure Active Directory-Geräteregistrierung verwenden. Folgen Sie den Anweisungen zum Bereitstellen eines Windows Server 2012 R2-Verbundserverproxys.

Installieren Sie das neueste Update für Windows Server 2012 R2 auf allen Windows Server 2012 R2-Verbundservern und -Proxyservern. Dieser Schritt kann vor oder nach dem Konfigurieren Ihrer Verbundfarm erfolgen. Das Update finden Sie hier: Windows Server 2012 R2-Update


### Vorbereiten der Active Directory-Gesamtstruktur für die Unterstützung von Geräten

> [AZURE.NOTE]Dies ist ein einmaliger Vorgang, den Sie ausführen müssen, um Ihre Active Directory-Gesamtstruktur für die Unterstützung von Geräten vorzubereiten. Sie müssen mit der Berechtigung "Unternehmensadministrator" angemeldet sein, und Ihre Active Directory-Gesamtstruktur muss über das Windows Server 2012 R2-Schema verfügen, damit dieses Verfahren abgeschlossen werden kann.

1. Öffnen Sie auf Ihrem Verbundserver ein Windows PowerShell-Befehlsfenster, und geben Sie dann Folgendes ein: *Initialize-ADDeviceRegistration*

2. Wenn Sie aufgefordert werden, einen Wert für "ServiceAccountName" einzugeben, geben Sie den Namen des Dienstkontos ein, das Sie als Dienstkonto für AD FS ausgewählt haben. Wenn es sich um ein gMSA-Konto handelt, geben Sie das Konto im Format "Domäne\\Kontoname$" ein. Verwenden Sie das Format "Domäne\\Kontoname", um ein Domänenkonto anzugeben.


### Aktivieren der Geräteauthentifizierung in AD FS

1. Öffnen Sie auf Ihrem Verbundserver die AD FS-Verwaltungskonsole, und navigieren Sie zu **AD FS** > **Authentifizierungsrichtlinien**.
2. Wählen Sie im Bereich **Aktionen** die Option "Globale primäre Authentifizierung bearbeiten" aus.
3. Aktivieren Sie **Geräteauthentifizierung aktivieren**, und klicken Sie dann auf "OK".
4. Standardmäßig entfernt AD FS regelmäßig nicht verwendete Geräte aus Active Directory. Sie müssen diese Aufgabe deaktivieren, wenn Sie die Azure Active Directory-Geräteregistrierung verwenden, damit Geräte in Azure verwaltet werden können.


### Deaktivieren der Bereinigung nicht verwendeter Geräte
1. Öffnen Sie auf Ihrem Verbundserver ein Windows PowerShell-Befehlsfenster, und geben Sie dann Folgendes ein: `*Set-AdfsDeviceRegistration -MaximumInactiveDays 0*`


###Konfigurieren einer Verbundbeziehung mit Azure Active Directory und Konfigurieren des Verzeichnissynchronisierungstools
Der folgende Abschnitt unterstützt Sie beim Einrichten einer Verbundvertrauensstellung zwischen Azure Active Directory und AD FS. Einige Schritte führen möglicherweise zum Verlassen dieser Seite. Befolgen Sie die unten aufgeführten Anweisungen, und kehren Sie dann zu diesem Handbuch zurück.

Integrieren von Azure Active Directory in die lokale Active Directory-Umgebung und Konfigurieren der einmaligen Anmeldung (SSO)
------------------------------------------------------------------------------------------------------

1. Melden Sie sich als **Administrator** beim Azure-Portal an.
2. Wählen Sie im linken Bereich **Active Directory** aus.
3. Wählen Sie auf der Registerkarte **Verzeichnis** Ihr Verzeichnis aus.
4. Wechseln Sie zur Registerkarte **Verzeichnisintegration**.
5. Suchen Sie unterhalb des Abschnitts "Integration in das lokale Active Directory-Verzeichnis" nach der Option "Verzeichnissynchronisierung", und wählen Sie dann "Aktiviert" aus.
6. Führen Sie die Schritte 1 bis 4 aus, um Azure Active Directory in Ihr lokales Verzeichnis zu integrieren.
  1. **Hinzufügen von Domänen**
  2. **Konfigurieren der einmaligen Anmeldung (SSO) und Vorbereiten der Verzeichnissynchronisierung** Wenn Sie AD FS bereits bereitgestellt haben, können Sie diese Unterabschnitte verwenden, um eine Vertrauensstellung zwischen AD FS und Azure AD zu konfigurieren und zu überprüfen. 
      *  [Installieren von Windows PowerShell für einmaliges Anmelden mit AD FS](https://msdn.microsoft.com/library/azure/jj151814.aspx)
      *  [Einrichten einer Vertrauensstellung zwischen AD FS und Azure AD](https://msdn.microsoft.com/library/azure/jj205461.aspx)
      *  [Überprüfen und Verwalten der einmaligen Anmeldung mit AD FS](https://msdn.microsoft.com/library/azure/jj151809.aspx)
  	  *  [Zusätzliche AD FS-Referenzen](https://msdn.microsoft.com/library/azure/dn151321.aspx)

  3. Installieren Sie das Tool für die Verzeichnissynchronisierung, und führen Sie es aus. Wenn Sie DirSync bereits installiert haben, müssen Sie ein Upgrade auf die aktuelle Version durchführen. Es ist mindestens Version 6862.0000 erforderlich.
  4. Überprüfen und Verwalten der Verzeichnissynchronisierung 
  5. Konfigurieren Sie einige zusätzliche Anspruchsregeln für das Vertrauensstellungsobjekt der vertrauenden Seite für Azure Active Directory. Dieses Vertrauensstellungsobjekt der vertrauenden Seite trägt normalerweise den Namen "Microsoft Office 365 Identity Platform".

###Konfigurieren der Azure Active Directory-Vertrauensstellungs-Anspruchsregeln der vertrauenden Seite

1. Öffnen Sie die AD FS-Verwaltungskonsole, und navigieren Sie zu **AD FS > Vertrauensstellungen > Vertrauensstellungen der vertrauenden Seite**. Klicken Sie mit der rechten Maustaste auf das Vertrauensstellungsobjekt der vertrauenden Seite **Microsoft Office 365 Identity Platform**, und wählen Sie dann "Anspruchsregeln bearbeiten" aus.
2. Wählen Sie auf die Registerkarte **Ausstellungstransformationsregeln** die Option "Regel hinzufügen" aus.
3. Wählen Sie im Dropdownfeld **Anspruchsregelvorlage** die Option **Ansprüche mit benutzerdefinierter Regel senden** aus. Klicken Sie auf **Weiter**.
4. Geben Sie im Textfeld **Anspruchsregelname** den Wert "Authentifizierungsmethode Anspruchsregel" ein.
5. Geben Sie die folgende Anspruchsregel in das Textfeld "Anspruchsregel" ein:

    ` c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]` `=> issue(claim = c);`

6. Klicken Sie zwei Mal auf **OK**, um das Dialogfeld zu schließen.
7. Konfigurieren Sie einige zusätzliche Authentifizierungsklassenverweise für das Vertrauensstellungsobjekt der vertrauenden Seite für Azure Active Directory. Dieses Vertrauensstellungsobjekt der vertrauenden Seite trägt normalerweise den Namen "Microsoft Office 365 Identity Platform".


###Konfigurieren des Authentifizierungsklassenverweises für das Azure Active Directory-Vertrauensstellungsobjekt der vertrauenden Seite

Öffnen Sie auf Ihrem Verbundserver ein Windows PowerShell-Befehlsfenster, und geben Sie Folgendes ein:

*Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn*


Hierbei steht <RPObjectName> für den Objektnamen der vertrauenden Seite für Ihr Azure Active Directory-Vertrauensstellungsobjekt der vertrauenden Seite. Dieses Objekt trägt üblicherweise den Namen "Microsoft Office 365 Identity Platform".

### Konfigurieren von DirSync mit Zurückschreiben des Geräteobjekts
Sie müssen die Verzeichnissynchronisierung (DirSync) so konfigurieren, dass ein Zurückschreiben von Geräteobjekten zulässig ist. In Azure Active Directory erstellte Geräte werden in Ihr Active Directory-Verzeichnis zurückgeschrieben. Es kann bis zu drei Stunden dauern, bis in Azure Active Directory erstellte Geräteobjekte nach Active Directory zurückgeschrieben werden.
>[Azure.Note]Sie müssen mit der Berechtigung "Unternehmensadministrator" angemeldet sein, um das folgende Verfahren ausführen zu können. Laden Sie hier den neuesten Build von "dirsync.exe" herunter.

> Für das Zurückschreiben von Geräten nach Active Directory ist eine Office 365-Lizenz oder eine Azure AD Premium-Lizenz erforderlich.

> Wenn Sie den DirSync-Installations-Assistenten gerade erst ausgeführt haben, melden Sie sich ab und dann erneut an, bevor Sie den Vorgang fortsetzen. Auf diese Weise wird sichergestellt, dass Sie über ein aktualisiertes Zugriffstoken verfügen.

>Es kann bis zu drei Stunden dauern, bis Geräteobjekte in die Active Directory-Domänendienste zurückgeschrieben werden.

1. Öffnen Sie auf Ihrem Server für die Verzeichnissynchronisierung ein Windows PowerShell-Befehlsfenster, und geben Sie dann die folgenden Befehle aus:

    `Import-Module DirSync`
    
    `$AADCreds = Get-Credential`
    
    `$ADCreds = Get-Credential`

2. Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie die Anmeldeinformationen des Administratorkontos für Ihren Clouddienst und Ihre Active Directory-Administratoranmeldeinformationen ein.

*Enable-MSOnlineObjectManagement –ObjectTypes Device –TargetCredential $AADCreds -Credential $ADCreds*

### Konfigurieren von Verbunddiensten für die Bereitstellung von Multi-Factor Authentication (MFA)
Geräte, die in Ihrer Organisation registriert sind, können als eine nahtlose zweite Stufe der Authentifizierung verwendet werden. Daher empfiehlt es sich, für die Geräteregistrierung eine strenge Authentifizierung zu konfigurieren. Sie können das unten genannte Handbuch verwenden, um AD FS mit Azure Multi-Factor Authentication zu konfigurieren und dann zu diesem Handbuch zurückkehren. Folgen Sie hierzu den Anweisungen zum Bereitstellen von Microsoft Azure Multi-Factor Authentication.

## Arbeitsbereichverknüpfung für Geräte mithilfe der Azure Active Directory-Geräteregistrierung

### Verknüpfen eines iOS-Geräts mithilfe der Azure Active Directory-Geräteregistrierung

Die Azure Active Directory-Geräteregistrierung verwendet einen drahtlosen Profilregistrierungsvorgang für iOS-Geräte. Dieser Vorgang beginnt damit, dass ein Benutzer eine Verbindung mit der Profilregistrierungs-URL mithilfe des Safari-Webbrowsers herstellt. Die URL weist das folgende Format auf:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Hier steht <yourdomainname> für den Domänennamen, den Sie mit Azure Active Directory konfiguriert haben. Wenn Sie z. B. die Domäne "contoso.com" verwenden, lautet die URL folgendermaßen:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Diese URL kann auf unterschiedlichste Weise an die Benutzer kommuniziert werden. Ein empfohlenes Verfahren besteht im Veröffentlichen dieser URL in einer benutzerdefinierten Anwendungsmeldung "Zugriff verweigert" in AD FS. Dieser Vorgang wird im folgenden Abschnitt beschrieben: Erstellen einer Anwendungszugriffsrichtlinie und einer benutzerdefinierten Meldung "Zugriff verweigert".

###Verknüpfen eines Windows 8.1-Geräts mithilfe der Azure Active Directory-Geräteregistrierung

1. Navigieren Sie auf Ihrem Windows 8.1-Gerät zu **PC-Einstellungen** > **Netzwerk** > **Arbeitsplatz**.
2. Geben Sie Ihren Benutzernamen im UPN-Format ein. Beispiel: dan@contoso.com..
3. Wählen Sie **Beitreten**.
4. Melden Sie sich bei Aufforderung mit Ihren Anmeldeinformationen an. Das Gerät ist jetzt verknüpft.

### Verknüpfen eines Android-Geräts mithilfe der Azure Active Directory-Geräteregistrierung

Im Thema "Azure Authenticator für Android" finden Sie Anweisungen zum Installieren der Azure Authenticator-App auf Ihrem Android-Gerät und zum Hinzufügen eines Geschäftskontos. Nach der erfolgreichen Erstellung eines Geschäftskontos auf einem Android-Gerät wird das Gerät per Arbeitsbereich mit der Organisation verknüpft.

### Überprüfen, ob registrierte Geräte nach Active Directory zurückgeschrieben werden
Sie können mithilfe von "LDP.exe" oder ADSI Edit Geräteobjekte anzeigen und bestätigen, dass diese zurück in Ihr Active Directory-Verzeichnis geschrieben wurden. Beide Tools gehören zum Lieferumfang der Administratortools von Active Directory.

Standardmäßig werden Geräteobjekte, die aus Azure Active Directory zurückgeschrieben werden, in der gleichen Domäne platziert wie Ihre AD FS-Farm.

`CN=RegisteredDevices,defaultNamingContext`

###Erstellen einer Anwendungszugriffsrichtlinie und einer benutzerdefinierten Meldung "Zugriff verweigert"
Stellen Sie sich folgendes Szenario vor: Sie erstellen eine Anwendungsvertrauensstellung der vertrauenden Seite in AD FS und konfigurieren eine Ausstellungsautorisierungsregel , die nur registrierte Geräte zulässt. Nun dürfen ausschließlich registrierte Geräte auf die Anwendung zugreifen. Damit der Zugriff auf die Anwendung für Ihre Benutzer vereinfacht wird, konfigurieren Sie eine benutzerdefinierte Meldung "Zugriff verweigert", die Anweisungen zum Hinzufügen von Geräten enthält. Jetzt steht Ihren Benutzern ein nahtloses Verfahren zum Registrieren ihrer Geräte zur Verfügung, um Zugriff auf eine Anwendung zu erhalten.

Die folgenden Schritte zeigen, wie dieses Szenario implementiert wird.
>[AZURE.NOTE]Dieser Abschnitt setzt voraus, dass Sie bereits eine Vertrauensstellung der vertrauenden Seite für Ihre Anwendung in AD FS konfiguriert haben.

1. Öffnen Sie die AD FS-Verwaltungskonsole, und navigieren Sie zu "AD FS > Vertrauensstellungen > Vertrauensstellungen der vertrauenden Seite".
2. Suchen Sie nach der Anwendung, für die diese neue Zugriffsregel gelten soll. Klicken Sie mit der rechten Maustaste auf die Anwendung, und wählen Sie dann "Anspruchsregeln bearbeiten" aus.
3. Klicken Sie auf die Registerkarte **Ausstellungsautorisierungsregeln**, und wählen Sie dann **Regel hinzufügen** aus.
4. Wählen Sie in der Dropdownliste **Anspruchsregelvorlage** die Option **Benutzer basierend auf einem eingehenden Anspruch zulassen oder ablehnen** aus. Klicken Sie auf **Weiter**.
5. Geben Sie im Feld "Anspruchsregelname" Folgendes ein: **Zugriff von registrierten Geräten zulassen**
6. Wählen Sie in der Dropdownliste "Eingehender Anspruchstyp" den Eintrag **Ist registrierter Benutzer** aus.
7. Geben Sie im Feld "Eingehender Anspruchswert" Folgendes ein: **true**
9. Wählen Sie **Fertig stellen** aus, uns klicken Sie dann auf **Übernehmen**.
10. Entfernen Sie alle Regeln, die weniger restriktiv sind als die Regel, die Sie soeben erstellt haben. Entfernen Sie z. B. die Standardregel "Allen Benutzern den Zugriff erlauben". 
8. Aktivieren Sie das Optionsfeld **Benutzern mit diesem eingehenden Anspruch Zugriff erlauben**.

Ihre Anwendung ist nun so konfiguriert, dass der Zugriff nur erlaubt wird, wenn der Benutzer ein registriertes Gerät verwendet, das dem Arbeitsplatz hinzugefügt wurde. Erweiterte Zugriffsrichtlinien finden Sie unter "Verwalten von Risiken mit der mehrstufigen Zugriffssteuerung".

Im nächsten Schritt konfigurieren Sie eine benutzerdefinierte Fehlermeldung für Ihre Anwendung. Diese Fehlermeldung informiert den Benutzer, dass er sein Gerät dem Arbeitsplatz hinzufügen muss, bevor er auf die Anwendung zugreifen darf. Sie können eine benutzerdefinierte Anwendungsmeldung "Zugriff verweigert" mithilfe von benutzerdefiniertem HTML und Windows PowerShell erstellen.

Öffnen Sie auf Ihrem Verbundserver ein Windows PowerShell-Befehlsfenster, und geben Sie dann den folgenden Befehl ein. Sie müssen Teile des Befehls durch Elemente ersetzen, die für Ihr System spezifisch sind:

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage ` Sie müssen Ihr Gerät registrieren, bevor Sie auf diese Anwendung zugreifen können.

**Wenn Sie ein iOS-Gerät verwenden, verwenden Sie diesen Link, um Ihr Gerät zu verknüpfen**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Dieses iOS-Gerät mit Ihrem Arbeitsplatz verknüpfen


**Wenn Sie ein Windows 8.1-Gerät** verwenden, können Sie Ihr Gerät über "PC-Einstellungen" > **Netzwerk** > **Arbeitsplatz** verknüpfen.


Hierbei gibt "**relying party trust name**" den Namen des Vertrauensstellungsobjekts der vertrauenden Seite Ihrer Anwendung in AD FS an. "yourdomain.com" steht für den Domänennamen, den Sie mit Azure Active Directory konfiguriert haben. Beispiel: contoso.com. Stellen Sie sicher, dass alle Zeilenumbrüche (sofern vorhanden) aus dem HTML-Inhalt entfernt wurden, den Sie an das Cmdlet "Set-AdfsRelyingPartyWebContent" übergeben.

<!---HONumber=August15_HO9-->