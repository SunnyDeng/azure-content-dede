<properties
	pageTitle="Azure AD Connect-Synchronisierung – Implementieren der Kennwortsynchronisierung | Microsoft Azure"
	description="Bietet Ihnen die Informationen, die Sie benötigen, um zu verstehen, wie die Synchronisierung von Kennwörtern funktioniert, und wie Sie sie in Ihrer Umgebung aktivieren."
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
	ms.date="11/16/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Implementieren der Kennwortsynchronisierung

Mit der Synchronisierung von Kennwörtern können Sie Benutzern ermöglichen, sich mit dem gleichen Kennwort, das sie zur Anmeldung bei Ihrem lokalen Active Directory verwenden, auch bei Azure Active Directory anzumelden.

Das Ziel dieses Themas ist, Ihnen die Informationen bereitzustellen, die Sie benötigen, um zu verstehen, wie die Synchronisierung von Kennwörtern funktioniert und wie Sie sie in Ihrer Umgebung aktivieren.

## Was ist die Kennwortsynchronisierung?

Die Kennwortsynchronisierung ist ein Feature der Azure Active Directory Connect-Synchronisierungsdienste (Azure AD Connect-Synchronisierung), das Benutzerkennwörter aus Ihrem lokalen Active Directory mit Azure Active Directory (Azure AD) synchronisiert. Dieses Feature ermöglicht Ihren Benutzern, sich bei ihren Azure Active Directory-Diensten (wie Office 365, Microsoft Intune und CRM Online) mit dem gleichen Kennwort anzumelden, mit dem sie sich bei Ihrem lokalen Netzwerk anmelden.

> [AZURE.NOTE]Weitere Informationen zu Active Directory-Domänendiensten, die für FIPS und Kennwortsynchronisierung konfiguriert sind, finden Sie unter [Kennwortsynchronisierung und FIPS](#password-synchronization-and-fips).

### Verfügbarkeit der Kennwortsynchronisierung

Jeder Kunde von Azure Active Directory ist berechtigt, die Kennwortsynchronisierung durchzuführen. Weitere Informationen zur Kompatibilität der Kennwortsynchronisierung und anderer Features, z. B. der Verbundauthentifizierung, finden Sie unten.

### So funktioniert die Kennwortsynchronisierung

Die Kennwortsynchronisierung ist eine Erweiterung des durch die Azure AD Connect-Synchronisierung implementierten Verzeichnissynchronisierungsfeatures. Darum muss für dieses Feature die Verzeichnissynchronisierung zwischen Ihrem lokalen und dem Azure Active Directory konfiguriert werden.

Der Active Directory-Domänendienst speichert Kennwörter in Form einer Hashwertdarstellung des tatsächlichen Benutzerkennworts. Der Kennworthash kann nicht zur Anmeldung in Ihrem lokalen Netzwerk verwendet werden. Er ist auch so konzipiert, dass er nicht umgekehrt werden kann, um das Benutzerkennwort in Nur-Text-Form zu gewinnen. Um ein Kennwort zu synchronisieren, extrahiert die Azure AD Connect-Synchronisierung den Benutzerkennworthash aus dem lokalen Active Directory. Vor der Synchronisierung mit dem Azure Active Directory-Authentifizierungsdienst wird der Kennworthash einer zusätzlichen Sicherheitsverarbeitung unterzogen. Der tatsächliche Datenfluss des Kennwortsynchronisierungsvorgangs ähnelt der Synchronisierung von Benutzerdaten, z. B. des Anzeigenamens oder von E-Mail-Adressen.

Kennwörter werden häufiger synchronisiert als es das Standardzeitfenster für die Verzeichnissynchronisierung für andere Attribute vorsieht. Kennwörter werden pro Benutzer und üblicherweise in chronologischer Reihenfolge synchronisiert. Bei Synchronisierung des Kennworts eines Benutzers aus dem lokalen AD mit der Cloud wird das vorhandene Cloudkennwort überschrieben.

Wenn Sie zuerst das Kennwortsynchronisierungsfeature aktivieren, wird eine anfängliche Synchronisierung der Kennwörter aller Benutzer im Bereich Ihres lokalen Active Directory mit Azure Active Directory ausgeführt. Sie können nicht explizit eine Gruppe von Benutzern definieren, deren Kennwörter mit der Cloud synchronisiert werden. Wenn später ein Kennwort von einem lokalen Benutzer geändert wird, erkennt das Kennwortsynchronisierungsfeature das geänderte Kennwort und synchronisiert es, meistens innerhalb von Minuten. Das Kennwortsynchronisierungsfeature versucht automatisch, fehlerhafte Kennwortsynchronisierungen erneut auszuführen. Tritt beim Versuch, ein Kennwort zu synchronisieren, ein Fehler auf, dann wird der Fehler in der Ereignisanzeige protokolliert.

Die Synchronisierung eines Kennworts hat keinen Einfluss auf derzeit angemeldete Benutzer. Wenn ein Benutzer, der bei einem Clouddienst angemeldet ist, auch das lokale Kennwort ändert, wird die Clouddienstsitzung ohne Unterbrechung fortgesetzt. Doch sobald der Clouddienst die erneute Authentifizierung des Benutzers anfordert, muss das neue Kennwort angegeben werden. An diesem Punkt muss der Benutzer das neue Kennwort angeben, das vor kurzem aus dem lokalen Active Directory mit der Cloud synchronisiert wurde.

> [AZURE.NOTE]Die Kennwortsynchronisierung wird nur für Objekttyp-Benutzer in Active Directory unterstützt. Sie wird vom iNetOrgPerson-Objtktyp nicht unterstützt.

### So funktioniert die Kennwortsynchronisierung mit Azure AD-Domänendiensten

Wenn Sie diesen Dienst in Azure AD aktivieren, müssen Sie die Option für die Kennwortsynchronisierung festlegen, damit ein einmaliges Anmelden für die Benutzer möglich wird. Beim Aktivieren des Diensts wird das Verhalten für die Kennwortsynchronisierung geändert, und die Kennworthashes werden unverändert aus Ihrem lokalen Active Directory per Synchronisierung in die Azure AD-Domänendienste übernommen. Die Funktion ähnelt ADMT (Active Directory Migration Tool) und ermöglicht es den Azure AD-Domänendiensten, Benutzer mit allen Methoden zu authentifizieren, die auch im lokalen Active Directory zur Verfügung stehen.

### Sicherheitshinweise

Beim Synchronisieren von Kennwörtern wird die Nur-Text-Version eines Benutzerkennworts weder gegenüber dem Kennwortsynchronisierungsfeature noch gegenüber Azure AD oder einem der zugehörigen Dienste offengelegt.

Darüber hinaus besteht keine Notwendigkeit, dass das lokale Active Directory das Kennwort in einem Format mit umkehrbarer Verschlüsselung speichert. Ein Digest des Active Directory-Kennworthashs wird zur Übertragung zwischen dem lokalen Active Directory und Azure Active Directory verwendet. Der Digest des Kennworthashs kann nicht zum Zugriff auf Ressourcen in der lokalen Umgebung des Kunden verwendet werden.

### Überlegungen zur Kennwortrichtlinie

Es gibt zwei Arten von Kennwortrichtlinien, die von der Aktivierung der Kennwortsynchronisierung betroffen sind:

1. Kennwortkomplexitätsrichtlinie
2. Kennwortablaufrichtlinie

**Kennwortkomplexitätsrichtlinie**

Wenn Sie die Kennwortsynchronisierung aktivieren, überschreiben die im lokalen Active Directory konfigurierten Richtlinien zur Kennwortkomplexität alle Richtlinien zur Kennwortkomplexität, die ggf. in der Cloud für synchronisierte Benutzer definiert sind. Dies bedeutet, dass jedes Kennwort, das in der lokalen Active Directory-Umgebung des Kunden gültig ist, für den Zugriff auf Azure AD-Dienste verwendet werden kann.

> [AZURE.NOTE]Kennwörter für Benutzer, die direkt in der Cloud erstellt werden, unterliegen auch weiterhin in der Cloud definierten Kennwortrichtlinien.

**Kennwortablaufrichtlinie**

Wenn sich ein Benutzer im Bereich der Kennwortsynchronisierung befindet, wird das Cloudkontokennwort auf "*Läuft nie ab*" festgelegt. Dies bedeutet, dass es möglich ist, dass das Kennwort eines Benutzers in der lokalen Umgebung abläuft, aber er sich weiterhin mit diesem abgelaufenen Kennwort bei Clouddiensten anmelden kann.

Das Cloudkennwort wird aktualisiert, sobald der Benutzer das nächste Mal das Kennwort in der lokalen Umgebung ändert.

### Überschreiben synchronisierter Kennwörter

Ein Administrator kann das Kennwort eines Benutzers mit Azure Active Directory PowerShell manuell zurücksetzen.

In diesem Fall überschreibt das neue Kennwort das synchronisierte Kennwort des Benutzers, und alle in der Cloud definierten Kennwortrichtlinien gelten für das neue Kennwort.

Ändert der Benutzer das lokale Kennwort erneut, wird das neue Kennwort mit der Cloud synchronisiert und überschreibt das manuell aktualisierte Kennwort.

## Vorbereiten der Kennwortsynchronisierung


### Aktivieren der Kennwortsynchronisierung

Wenn Sie beim Installieren von Azure AD Connect die Express-Einstellungen verwenden, wird die Kennwortsynchronisierung standardmäßig aktiviert.

Wenn Sie beim Installieren von Azure AD Connect die benutzerdefinierten Einstellungen verwenden, aktivieren Sie die Kennwortsynchronisierung auf der Seite „Benutzeranmeldung“. ![Benutzeranmeldung](./media/active-directory-aadsync-implement-password-synchronization/usersignin.png)

Wenn Sie die Option **Verbund mit AD FS** auswählen, können Sie die Kennwortsynchronisierung optional als zusätzliche Sicherheit für den Fall aktivieren, dass Ihre AD FS-Infrastruktur ausfällt. Sie können sie auch aktivieren, wenn Sie vorhaben, die Azure AD-Domänendienste zu verwenden.

### Kennwortsynchronisierung und FIPS

Wenn Ihr Server wegen FIPS (Federal Information Processing Standard) gesperrt ist, wurde MD5 deaktiviert. Um dieses für die Kennwortsynchronisierung zu aktivieren, fügen Sie im Verzeichnis „C:\\Programme\\Azure AD Sync\\Bin“ in der Datei „miiserver.exe.config“ den Schlüssel „enforceFIPSPolicy“ ein.

```
<configuration>
    <runtime>
        <enforceFIPSPolicy enabled="false"/>
    </runtime>
</configuration>
```

Den Knoten „configuration/runtime“ finden Sie am Ende der config-Datei.

Informationen über Sicherheitsfragen und FIPS finden Sie im Blogbeitrag [AAD Password Sync, Encryption and FIPS compliance](http://blogs.technet.com/b/ad/archive/2014/06/28/aad-password-sync-encryption-and-and-fips-compliance.aspx) (in englischer Sprache).

## Verwalten der Kennwortsynchronisierung

### Problembehandlung bei der Kennwortsynchronisierung

Starten Sie den **Synchroisierungsdienst-Manager**, öffnen Sie **Connectors**, wählen Sie den Active Directory Connector aus, in dem sich der Benutzer befindet, wählen Sie **Connectorbereich durchsuchen** aus, und suchen Sie den gewünschten Benutzer.

![Benutzer im Connectorbereich](./media/active-directory-aadsync-implement-password-synchronization/cspasswordsync.png)

Wählen Sie für den Benutzer die Registerkarte **Herkunft** aus und stellen Sie sicher, dass für mindestens eine Synchronisierungsregel die **Kennwortsynchronisierung** als **Wahr** angezeigt wird. In der Standardsynchronisierung ist dies die Synchronisierungsregel **Eingehend von AD – Benutzerkonto aktiviert**.

Um die Details zur Kennwortsynchronisierung für das Objekt anzuzeigen, klicken Sie unten auf der Seite auf die Schaltfläche **Protokoll...**. Daraufhin wird eine Seite mit Verlaufsdaten zum Kennwortsynchronisierungsstatus des Benutzers für die vergangene Woche angezeigt.

![Protokoll für Objekt](./media/active-directory-aadsync-implement-password-synchronization/csobjectlog.png)

Die Statusspalte kann die nachfolgend aufgeführten Werte enthalten, die auch auf das Problem hinweisen. Außerdem wird kurz beschrieben, warum das Kennwort nicht synchronisiert wurde.

| Status | Beschreibung |
| ---- | ----- |
| Erfolgreich | Das Kennwort wurde erfolgreich synchronisiert. |
| SourceConnectorNotPresent | Im lokalen Active Directory Connector-Bereich wurde kein Objekt gefunden. |
| NoTargetConnection | Im Metaverse oder im Azure AD-Connectorbereich befindet sich kein Objekt. |
| TargetNotExportedToDirectory | Das Objekt im Azure AD-Connectorbereich wurde noch nicht exportiert. |


### Auslösen einer vollständigen Synchronisierung aller Kennwörter
Das Erzwingen einer vollständigen Synchronisierung aller Kennwörter sollte in der Regel nicht erforderlich sein. Falls dies aber doch einmal nötig sein sollte, finden Sie hier das PowerShell-Skript dafür.

    $adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
    $aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
    Import-Module adsync
    $c = Get-ADSyncConnector -Name $adConnector
    $p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter “Microsoft.Synchronize.ForceFullPasswordSync”, String, ConnectorGlobal, $null, $null, $null
    $p.Value = 1
    $c.GlobalParameters.Remove($p.Name)
    $c.GlobalParameters.Add($p)
    $c = Add-ADSyncConnector -Connector $c
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
    Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true




## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!---HONumber=Nov15_HO4-->