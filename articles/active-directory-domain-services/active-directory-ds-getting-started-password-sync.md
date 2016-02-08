<properties
	pageTitle="Vorschau der Azure Active Directory-Domänendienste: Erste Schritte | Microsoft Azure"
	description="Erste Schritte mit Azure Active Directory-Domänendiensten"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/26/2016"
	ms.author="maheshu"/>

# Azure AD-Domänendienste *(Vorschau)* – Erste Schritte

## Schritt 5: Aktivieren der Kennwortsynchronisierung
Nachdem Sie Azure Active Directory-Domänendienste für Ihren Azure AD-Mandanten aktiviert haben, besteht der nächste Schritt darin, die Synchronisierung von Kennwörtern zu ermöglichen. Dadurch können Benutzer sich mithilfe ihrer Unternehmensanmeldeinformationen bei der Domäne anmelden.

Die einzelnen Schritte unterscheiden sich je nachdem, ob es sich bei Ihrer Organisation um einen reinen Cloud-Azure AD-Mandanten handelt oder ob die Synchronisierung mit Ihrem lokalen Verzeichnis über Azure AD Connect festgelegt ist.

### Reine Cloudmandanten: Aktivieren der Hashgenerierung für NTLM- und Kerberos-Anmeldeinformationen in Azure AD
Wenn es sich bei Ihrer Organisation um einen reinen Cloud-Azure AD-Mandanten handelt, müssen Benutzer, die Azure Active Directory-Domänendienste verwenden, ihre Kennwörter ändern. Dieser Schritt führt dazu, dass die Anmeldeinformationshashes, die von den Azure AD-Domänendiensten für die Kerberos- und NTLM-Authentifizierung benötigt werden, in Azure AD generiert werden. Sie können entweder die Kennwörter für alle Benutzer im Mandanten ablaufen lassen, die Azure AD-Domänendienste verwenden müssen, oder diese Benutzer zum Ändern ihrer Kennwörter anweisen.

Hier finden Sie Anweisungen, die Sie den Benutzern zur Änderung ihrer Kennwörter bereitstellen müssen:

1. Navigieren Sie zur Seite "Azure AD-Zugriffsbereich" für Ihre Organisation. Diese steht in der Regel unter [http://myapps.microsoft.com](http://myapps.microsoft.com) zur Verfügung.
2. Wählen Sie auf dieser Seite die Registerkarte **Profil**.
3. Klicken Sie auf dieser Seite auf die Kachel **Kennwort ändern**, um eine Kennwortänderung einzuleiten.

    ![Erstellen Sie ein virtuelles Netzwerk für Azure Active Directory-Domänendienste.](./media/active-directory-domain-services-getting-started/user-change-password.png)

4. Die Seite **Kennwort ändern** wird geöffnet. Benutzer können das vorhandene (alte) Kennwort eingeben und es dann ändern.

    ![Erstellen Sie ein virtuelles Netzwerk für Azure Active Directory-Domänendienste.](./media/active-directory-domain-services-getting-started/user-change-password2.png)

Nachdem die Benutzer ihr Kennwort geändert haben, kann das neue Kennwort bald in den Azure AD-Domänendiensten verwendet werden. Nach einigen Minuten können sich Benutzer mit ihren neuen geänderten Kennwörtern bei Computern in der verwalteten Domäne anmelden.


### Synchronisierte Mandanten: Aktivieren der Synchronisierung von Hashes für NTLM- und Kerberos-Anmeldeinformationen mit Azure AD
Wenn der Azure AD-Mandant für Ihre Organisation so festgelegt ist, dass er über Azure AD Connect mit Ihrem lokalen Verzeichnis synchronisiert wird, müssen Sie Azure AD Connect so konfigurieren, dass die für die NTLM- und Kerberos-Authentifizierung erforderlichen Anmeldeinformationshashes synchronisiert werden. Diese Hashes werden standardmäßig nicht mit Azure AD synchronisiert. Anhand der folgenden Schritte können Sie die Synchronisierung der Hashes mit Ihrem Azure AD-Mandanten aktivieren.

#### Installieren oder Aktualisieren von Azure AD Connect

Sie müssen die neueste empfohlene Version von Azure AD Connect auf einem in die Domäne eingebundenen Computer installieren. Wenn Sie über eine vorhandene Instanz von Azure AD Connect-Setup verfügen, müssen Sie sie aktualisieren, damit der Azure AD Connect-GA-Build verwendet wird. Stellen Sie sicher, dass Sie die neueste Version von Azure AD Connect verwenden, um bekannte Probleme oder Fehler zu vermeiden.

**[Azure AD Connect herunterladen](http://www.microsoft.com/download/details.aspx?id=47594)**

Empfohlene Mindestversion: **1.0.9131** – veröffentlicht am 3. Dezember 2015

  >[AZURE.WARNING] Sie müssen die neueste empfohlene Version von Azure AD Connect installieren, um ältere Anmeldeinformationen zu aktivieren (erforderlich für NTLM- und Kerberos-Authentifizierung) und Ihren Azure AD-Mandanten zu synchronisieren. Diese Funktionalität ist in früheren Versionen von Azure AD Connect oder im DirSync-Legacytool nicht verfügbar.

Installationshinweise für Azure AD Connect finden Sie im folgenden Artikel: [Erste Schritte mit Azure AD Connect](../active-directory/active-directory-aadconnect.md)


#### Erzwingen der vollständigen Kennwortsynchronisierung mit Azure AD

Um eine vollständige Kennwortsynchronisierung zu erzwingen und die Synchronisierung der Kennworthashes aller lokalen Benutzer (einschließlich der Anmeldeinformationshashes, die für die NTLM-/Kerberos-Authentifizierung erforderlich sind) mit Ihrem Azure AD-Mandanten zu aktivieren, führen Sie für jede Active Directory-Gesamtstruktur das folgende PowerShell-Skript aus.

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"  
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"  
Import-Module adsync  
$c = Get-ADSyncConnector -Name $adConnector  
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1  
$c.GlobalParameters.Remove($p.Name)  
$c.GlobalParameters.Add($p)  
$c = Add-ADSyncConnector -Connector $c  
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false   
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true  
```

Abhängig von der Größe Ihres Verzeichnisses (Anzahl der Benutzer, Gruppen usw.) nimmt die Synchronisierung von Anmeldeinformationen mit Azure AD etwas Zeit in Anspruch. Die Kennwörter können kurz nach der Synchronisierung der Hashes für die Anmeldeinformationen mit Azure AD in der verwalteten Domäne der Azure AD-Domänendienste verwendet werden.

<!---HONumber=AcomDC_0128_2016-->