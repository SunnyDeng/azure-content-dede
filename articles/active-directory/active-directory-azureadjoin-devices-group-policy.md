<properties 
	pageTitle="Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen | Microsoft Azure" 
	description="In diesem Thema wird erklärt, wie Gruppenrichtlinien konfiguriert werden müssen, damit Geräte durch Domänenbeitritt ins Unternehmensnetzwerk eingebunden werden können." 
	services="active-directory" 
	documentationCenter="" 
	authors="femila" 
	manager="stevenpo" 
	editor=""
	tags="azure-classic-portal"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="femila"/>

# Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen

Der Domänenbeitritt ist das klassische Verfahren, mit dem Organisationen in den letzten 15 oder mehr Jahren geschäftliche genutzte Geräte in die Domäne eingebunden haben. Benutzer konnten sich mit ihren AD-Geschäftskonten (Active Directory) an den Geräten anmelden, und die IT-Mitarbeiter konnten diese Geräte vollständig verwalten. Organisationen nutzen normalerweise Verfahren für die Imageerstellung, um Geräte für Benutzer bereitzustellen, und sie verwalten die Geräte im Allgemeinen mit System Center Configuration Manager (SCCM) oder mit Gruppenrichtlinien.

Ein Domänenbeitritt unter Windows 10 bietet nach der Einbindung in Azure AD folgende Vorteile:

- Einmaliges Anmelden (SSO, Single Sign-On) bei Azure AD-Ressourcen von jedem Ort aus
- Zugriff auf den Windows Store des Unternehmens mit dem Geschäftskonto (kein Microsoft-Konto erforderlich)
- Für Unternehmen geeignetes geräteübergreifendes Roaming von Benutzereinstellungen mit dem Geschäftskonto (kein Microsoft-Konto erforderlich)
- Strenge Authentifizierung und bequeme Anmeldung beim Geschäftskonto mit Microsoft Passport und Windows Hello
- Möglichkeit zum Beschränken des Zugriffs auf Geräte, die die Unternehmensrichtlinien für Geräte erfüllen

##Voraussetzungen

Der Domänenbeitritt funktioniert weiterhin wie gehabt. Damit Sie jedoch die Vorteile von Azure AD nutzen können, z. B. für einmaliges Anmelden, Roaming von Einstellungen bei Geschäftskonten, Zugriff auf den Windows Store mit dem Geschäftskonto, benötigen Sie Folgendes:

- Azure AD-Abonnement
- Azure AD Connect, um das lokale Verzeichnis auf Azure AD zu erweitern
- Eine Richtlinie zum Herstellen einer Verbindung für in die Domäne eingebundene Geräte mit Azure AD
- Windows 10 (Build 10551 oder neuer) für die Geräte

Zum Aktivieren von Microsoft Passport for Work und Windows Hello benötigen Sie zusätzlich Folgendes:

- Eine Public Key-Infrastruktur (PKI) zum Ausstellen von Benutzerzertifikaten
- System Center Configuration Manager, Version 1509 für Technical Preview Weitere Informationen erhalten Sie unter [Microsoft System Center Configuration Manager Technical Preview](https://technet.microsoft.com/library/dn965439.aspx#BKMK_TP3Update) und [System Center Configuration Manager Team Blog](http://blogs.technet.com/b/configmgrteam/archive/2015/09/23/now-available-update-for-system-center-config-manager-tp3.aspx) (in englischer Sprache). Dies ist erforderlich, um Benutzerzertifikate auf der Basis von Microsoft Passport-Schlüsseln bereitzustellen.

Alternativ zur PKI-Bereitstellung haben Sie folgende Möglichkeit:

- Verwenden einiger Domänencontroller mit Windows Server 2016 Active Directory-Domänendiensten

Um einen bedingten Zugriff zu ermöglichen, können Sie eine Richtlinie erstellen, die den Zugriff auf in die Domäne eingebundene Geräte ohne weitere Bereitstellungen zulässt. Um die Zugriffssteuerung anhand der Gerätecompliance zu verwalten, benötigen Sie Folgendes:

- System Center Configuration Manager, Version 1509 für Technical Preview, für Passport-Szenarien

## Anweisungen zur Bereitstellung

1. Bereitstellen von Azure Active Directory Connect: Azure AD Connect ermöglicht die Bereitstellung lokaler Computer als Geräteobjekte in der Cloud. Weitere Informationen zum Bereitstellen von Azure AD Connect finden Sie unter „Aktivieren Ihres Verzeichnisses für das Hybridmanagement mit Azure AD Connect“.


Wenn Sie eine [benutzerdefinierte Installation für Azure AD Connect](https://azure.microsoft.com/de-DE/documentation/articles/active-directory-aadconnect-get-started-custom/) ausgeführt haben (nicht die Express-Installation), müssen Sie das unten beschriebene Verfahren **Erstellen eines Dienstverbindungspunkts im lokalen Active Directory** durchlaufen.

Wenn Sie vor dem Installieren von Azure AD Connect über eine Verbundkonfiguration für Azure AD verfügen (z. B. weil Sie zuvor Active Directory-Verbunddienste (AD FS) bereitgestellt haben), müssen Sie das unten beschriebene Verfahren **Konfigurieren von AD FS-Anspruchsregeln** ausführen.

### Erstellen eines Dienstverbindungspunkts im lokalen Active Directory
In die Domäne eingebundene Geräte verwenden dieses Objekt, um während der automatischen Registrierung beim Azure-Geräteregistrierungsdienst die Azure AD-Mandanteninformationen zu ermitteln. Führen Sie auf dem Azure AD Connect-Server die folgenden PowerShell-Befehle aus:

    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

>[AZURE.NOTE]Ersetzen Sie [*connector account name*] durch das Domänenkonto, das als AD-Connector-Konto verwendet wird.

>[AZURE.NOTE]Wenn das Get-Credential-Popup angezeigt wird, muss der Benutzername für die Anmeldeinformationen im Format **user@example.com* angegeben werden.

### Konfigurieren von AD FS-Anspruchsregeln
Dieser Schritt ermöglicht die sofortige Registrierung eines Computers bei Azure DRS, indem den Computern die Authentifizierung mit Kerberos/NTLM über AD FS erlaubt wird. Ohne diesen Schritt werden Computer nur mit Verzögerung in Azure AD eingebunden (abhängig von den Synchronisationszeiten von Azure AD Connect).

>[AZURE.NOTE]Wenn Sie lokal nicht mit AD FS als Verbundserver arbeiten, befolgen Sie die Anweisungen Ihres Anbieters, um die Anspruchsregeln zu erstellen.

Führen Sie auf Ihrem AD FS-Server (oder in einer mit dem AD FS-Server verbundenen Sitzung) die folgenden PowerShell-Befehle aus:

      <#----------------------------------------------------------------------
     |   Modify the Azure AD Relying Party to include the claims needed 
     |   for DomainJoin++.  The rules include:
     |   -ObjectGuid
     |   -AccountType
     |   -ObjectSid
     +---------------------------------------------------------------------#>
 
    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules
 
    $rule1 = '@RuleName = "Issue object GUID" 
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] &&
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 
          => issue(store = "Active Directory", types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), query = ";objectguid;{0}", param = c2.Value);'
 
    $rule2 = '@RuleName = "Issue account type for domain joined computers" 
          c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 
          => issue(Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", Value = "DJ");'
 
    $rule3 = '@RuleName = "Pass through primary SID" 
          c1:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Value =~ "515$", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] && 
          c2:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"] 
          => issue(claim = c2);'
 
    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3
 
    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules
 
    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

>[AZURE.NOTE]Windows 10-Computer authentifizieren sich mit der integrierten Windows-Authentifizierung bei einem aktiven von AD FS gehosteten WS-Trust-Endpunkt. Sie müssen sicherstellen, dass dieser Endpunkt aktiviert ist. Wenn Sie einen Webauthentifizierungsproxy verwenden, müssen Sie außerdem sicherstellen, dass dieser Endpunkt durch den Proxy veröffentlicht wird. Dazu prüfen Sie, ob in der AD FS-Verwaltungskonsole unter „Dienst > Endpunkte“ die Option „adfs/services/trust/13/windowstransport“ als aktiviert angezeigt wird.

## Konfigurieren der automatischen Geräteregistrierung mithilfe einer Gruppenrichtlinie in Active Directory
Sie können eine Active Directory-Gruppenrichtlinie verwenden, um Ihre in die Domäne eingebundenen Windows 10-Geräte für die automatische Registrierung bei Azure AD zu konfigurieren. Dazu führen Sie die folgenden Schritte aus:

1. 	Öffnen Sie den Server-Manager, und navigieren Sie zu **Tools** > **Gruppenrichtlinienverwaltung**.
2.	Navigieren Sie von der Gruppenrichtlinienverwaltung zu dem Domänenknoten, der der Domäne entspricht, in der Sie Azure AD Join aktivieren möchten.
3.	Klicken Sie mit der rechten Maustaste auf Ihr **Gruppenrichtlinienobjekt**, und wählen Sie dann **Neu** aus. Geben Sie Ihrem Gruppenrichtlinienobjekt einen Namen, z. B. „Automatischer Azure AD-Beitritt“. Klicken Sie auf **OK**.
4.	Klicken Sie mit der rechten Maustaste auf Ihr neues Gruppenrichtlinienobjekt, und wählen Sie dann **Bearbeiten** aus.
5.	Navigieren Sie zu **Computerkonfiguration** > **Richtlinien** > **Administrative Vorlagen** > **Windows-Komponenten** > **Arbeitsbereichverknüpfung**.
6.	Klicken Sie mit der rechten Maustaste auf **Clientcomputer automatisch in Arbeitsbereich einbinden**, und wählen Sie dann **Bearbeiten**.
7.	Aktivieren Sie das Optionsfeld **Aktiviert**, und klicken Sie dann auf **Übernehmen**. Klicken Sie auf **OK**.
8.	Sie können das Gruppenrichtlinienobjekt jetzt mit einem Speicherort Ihrer Wahl verknüpfen. Um diese Richtlinie für alle in die Domäne eingebundenen Windows 10-Geräte in Ihrer Organisation zu aktivieren, verknüpfen Sie die Gruppenrichtlinie mit der Domäne. Beispiel:
 - Eine bestimmte Organisationseinheit in AD, in der in die Domäne eingebundene Windows 10-Computer platziert werden
 - Eine bestimmte Sicherheitsgruppe mit in die Domäne eingebundenen Windows 10-Computern, die automatisch bei Azure AD registriert werden
 
>[AZURE.NOTE]Die Gruppenrichtlinienvorlage wurde in Windows 10 umbenannt. Wenn Sie das Gruppenrichtlinientool auf einem Windows 10-Computer ausführen, wird die Richtlinie angezeigt als: <br> **In die Domäne eingebundene Computer als Geräte registrieren**. Die Richtlinie wird in folgendem Verzeichnis gespeichert:<br> ***Computerkonfiguration/Richtlinien/Administrative Vorlagen/Windows-Komponenten/Geräteregistrierung***

 
## Zusätzliche Informationen
* [Windows 10 für Unternehmen: Möglichkeiten der geschäftlichen Nutzung von Geräten](active-directory-azureadjoin-windows10-devices-overview.md)
* [Erweitern von Cloudfunktionen auf Windows 10-Geräte über Azure Active Directory Join](active-directory-azureadjoin-user-upgrade.md)
* [Weitere Informationen zu Verwendungsszenarios für Azure AD Join](active-directory-azureadjoin-deployment-aadjoindirect.md)
* [Verbinden von in die Domäne eingebundenen Geräten mit Azure AD für Windows 10-Funktionen](active-directory-azureadjoin-devices-group-policy.md)
* [Einrichten von Azure AD Join](active-directory-azureadjoin-setup.md)

<!---HONumber=Nov15_HO4-->