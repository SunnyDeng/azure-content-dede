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
	ms.date="08/24/2015"
	ms.author="markusvi;andkjell"/>


# Azure AD Connect-Synchronisierung: Implementieren der Kennwortsynchronisierung

Mit der Synchronisierung von Kennwörtern können Sie Benutzern ermöglichen, sich mit dem gleichen Kennwort, das sie zur Anmeldung bei Ihrem lokalen Active Directory verwenden, auch bei Azure Active Directory anzumelden.

Das Ziel dieses Themas ist, Ihnen die Informationen bereitzustellen, die Sie benötigen, um zu verstehen, wie die Synchronisierung von Kennwörtern funktioniert und wie Sie sie in Ihrer Umgebung aktivieren.

## Was ist die Kennwortsynchronisierung?

Die Kennwortsynchronisierung ist ein Feature der Azure Active Directory Connect-Synchronisierungsdienste (Azure AD Connect-Synchronisierung), das Benutzerkennwörter aus Ihrem lokalen Active Directory mit Azure Active Directory (Azure AD) synchronisiert. Dieses Feature ermöglicht Ihren Benutzern, sich bei ihren Azure Active Directory-Diensten (wie Office 365, Microsoft Intune und CRM Online) mit dem gleichen Kennwort anzumelden, mit dem sie sich bei Ihrem lokalen Netzwerk anmelden.

> [AZURE.NOTE]Weitere Informationen zu Active Directory-Domänendiensten, die für die FIPS- und Kennwortsynchronisierung konfiguriert sind, siehe "Fehler bei der Kennwortsynchronisierung in FIPS-kompatiblen Systemen".

## Verfügbarkeit der Kennwortsynchronisierung

Jeder Kunde von Azure Active Directory ist berechtigt, die Kennwortsynchronisierung durchzuführen. Weitere Informationen zur Kompatibilität der Kennwortsynchronisierung und anderer Features, z. B. der Verbundauthentifizierung, finden Sie unten.

## So funktioniert die Kennwortsynchronisierung

Die Kennwortsynchronisierung ist eine Erweiterung des durch die Azure AD Connect-Synchronisierung implementierten Verzeichnissynchronisierungsfeatures. Darum muss für dieses Feature die Verzeichnissynchronisierung zwischen Ihrem lokalen und dem Azure Active Directory konfiguriert werden.

Der Active Directory-Domänendienst speichert Kennwörter in Form einer Hashwertdarstellung des tatsächlichen Benutzerkennworts. Der Kennworthash kann nicht zur Anmeldung in Ihrem lokalen Netzwerk verwendet werden. Er ist auch so konzipiert, dass er nicht umgekehrt werden kann, um das Benutzerkennwort in Nur-Text-Form zu gewinnen. Um ein Kennwort zu synchronisieren, extrahiert die Azure AD Connect-Synchronisierung den Benutzerkennworthash aus dem lokalen Active Directory. Vor der Synchronisierung mit dem Azure Active Directory-Authentifizierungsdienst wird der Kennworthash einer zusätzlichen Sicherheitsverarbeitung unterzogen. Der tatsächliche Datenfluss des Kennwortsynchronisierungsvorgangs ähnelt der Synchronisierung von Benutzerdaten, z. B. des Anzeigenamens oder von E-Mail-Adressen.

Kennwörter werden häufiger synchronisiert als es das Standardzeitfenster für die Verzeichnissynchronisierung für andere Attribute vorsieht. Kennwörter werden pro Benutzer und üblicherweise in chronologischer Reihenfolge synchronisiert. Bei Synchronisierung des Kennworts eines Benutzers aus dem lokalen AD mit der Cloud wird das vorhandene Cloudkennwort überschrieben.

Wenn Sie zuerst das Kennwortsynchronisierungsfeature aktivieren, wird eine anfängliche Synchronisierung der Kennwörter aller Benutzer im Bereich Ihres lokalen Active Directory mit Azure Active Directory ausgeführt. Sie können nicht explizit eine Gruppe von Benutzern definieren, deren Kennwörter mit der Cloud synchronisiert werden. Wenn später ein Kennwort von einem lokalen Benutzer geändert wird, erkennt das Kennwortsynchronisierungsfeature das geänderte Kennwort und synchronisiert es, meistens innerhalb von Minuten. Das Kennwortsynchronisierungsfeature versucht automatisch, fehlerhafte Kennwortsynchronisierungen erneut auszuführen. Tritt beim Versuch, ein Kennwort zu synchronisieren, ein Fehler auf, dann wird der Fehler in der Ereignisanzeige protokolliert.

Die Synchronisierung eines Kennworts hat keinen Einfluss auf derzeit angemeldete Benutzer. Wenn ein Benutzer, der bei einem Clouddienst angemeldet ist, auch das lokale Kennwort ändert, wird die Clouddienstsitzung ohne Unterbrechung fortgesetzt. Doch sobald der Clouddienst die erneute Authentifizierung des Benutzers anfordert, muss das neue Kennwort angegeben werden. An diesem Punkt muss der Benutzer das neue Kennwort angeben, das vor kurzem aus dem lokalen Active Directory mit der Cloud synchronisiert wurde.

## Sicherheitshinweise

Beim Synchronisieren von Kennwörtern wird die Nur-Text-Version eines Benutzerkennworts weder gegenüber dem Kennwortsynchronisierungsfeature noch gegenüber Azure AD oder einem der zugehörigen Dienste offengelegt.

Darüber hinaus besteht keine Notwendigkeit, dass das lokale Active Directory das Kennwort in einem Format mit umkehrbarer Verschlüsselung speichert. Ein Digest des Active Directory-Kennworthashs wird zur Übertragung zwischen dem lokalen Active Directory und Azure Active Directory verwendet. Der Digest des Kennworthashs kann nicht zum Zugriff auf Ressourcen in der lokalen Umgebung des Kunden verwendet werden.

## Überlegungen zur Kennwortrichtlinie

Es gibt zwei Arten von Kennwortrichtlinien, die von der Aktivierung der Kennwortsynchronisierung betroffen sind:

1. Kennwortkomplexitätsrichtlinie
2. Kennwortablaufrichtlinie

### Kennwortkomplexitätsrichtlinie

Wenn Sie die Kennwortsynchronisierung aktivieren, überschreiben die im lokalen Active Directory konfigurierten Richtlinien zur Kennwortkomplexität alle Richtlinien zur Kennwortkomplexität, die ggf. in der Cloud für synchronisierte Benutzer definiert sind. Dies bedeutet, dass jedes Kennwort, das in der lokalen Active Directory-Umgebung des Kunden gültig ist, für den Zugriff auf Azure AD-Dienste verwendet werden kann.


> [AZURE.NOTE]Kennwörter für Benutzer, die direkt in der Cloud erstellt werden, unterliegen auch weiterhin in der Cloud definierten Kennwortrichtlinien.

### Kennwortablaufrichtlinie

Wenn sich ein Benutzer im Bereich der Kennwortsynchronisierung befindet, wird das Cloudkontokennwort auf "*Läuft nie ab*" festgelegt. Dies bedeutet, dass es möglich ist, dass das Kennwort eines Benutzers in der lokalen Umgebung abläuft, aber er sich weiterhin mit diesem abgelaufenen Kennwort bei Clouddiensten anmelden kann.

Das Cloudkennwort wird aktualisiert, sobald der Benutzer das nächste Mal das Kennwort in der lokalen Umgebung ändert.


## Überschreiben synchronisierter Kennwörter

Ein Administrator kann das Kennwort eines Benutzers mit Azure Active Directory PowerShell manuell zurücksetzen.

In diesem Fall überschreibt das neue Kennwort das synchronisierte Kennwort des Benutzers, und alle in der Cloud definierten Kennwortrichtlinien gelten für das neue Kennwort.

Ändert der Benutzer das lokale Kennwort erneut, wird das neue Kennwort mit der Cloud synchronisiert und überschreibt das manuell aktualisierte Kennwort.


## Vorbereitung zur Kennwortsynchronisierung

Ihr Azure Active Directory-Mandant muss für die Verzeichnissynchronisierung aktiviert werden, bevor er für die Kennwortsynchronisierung aktiviert werden kann.


## Aktivieren der Kennwortsynchronisierung

Sie aktivieren die Kennwortsynchronisierung bei Ausführung des Azure AD Connect-Konfigurations-Assistenten.

Wählen Sie auf der Dialogfeldseite **Optionale Features** "**Kennwortsynchronisierung**".

![Optionale Features][1]


> [AZURE.NOTE]Dieser Vorgang löst eine vollständige Synchronisierung aus. Vollständige Synchronisierungszyklen dauern im Allgemeinen länger als andere Synchronisierungszyklen.


## Verwalten der Kennwortsynchronisierung

Sie können den Fortschritt der Kennwortsynchronisierung im Ereignisprotokoll des Computers überwachen, auf dem Azure AD Connect ausgeführt wird.


### Ermitteln des Kennwortsynchronisierungsstatus

Sie können ermitteln, für welche Benutzer die Kennwörter erfolgreich synchronisiert wurden, indem Sie die Ereignisse überprüfen, die folgende Kriterien erfüllen:

| Quelle| Ereignis-ID |
| --- | --- |
| Verzeichnissynchronisierung| 656|
| Verzeichnissynchronisierung| 657|

Die Ereignisse mit der Ereignis-ID 656 dienen als Bericht über verarbeitete Kennwortänderungsanforderungen:

![Ereignis-ID 656][2]

Die entsprechenden Ereignisse mit der ID 657 liefern das Ergebnis zu diesen Anforderungen:

![Ereignis-ID 657][3]

In den Ereignissen werden die betroffenen Objekte durch ihre Anker und den DN-Wert identifiziert. Der Ankerwert entspricht dem **ImmutableId**-Wert, der für einen Benutzer durch das Cmdlet "Get-MsoUser" zurückgegeben wird.

Neben den Objektbezeichnern stellt **Ereignis-ID 656** das Datum bereit, an dem das Kennwort des Benutzers im lokalen Active Directory-Verzeichnis geändert wurde:

![Kennwortänderungsanforderung][4]

Ereignis-ID 657 enthält zusätzlich zu den Quellobjektbezeichnern ein Ergebnisfeld, um den Status der Synchronisierung für das Benutzerobjekt anzuzeigen.

Ein erfolgreich synchronisiertes Kennwort wird in einem Ereignis mit der Ereignis-ID 657 mit dem Wert "Success" für das Attribut "Result" gekennzeichnet. Wenn ein Kennwortsynchronisierungsversuch fehlschlägt, erhält das Attribut "Result" den Wert "Failure":

![Kennwortänderungsergebnis][5]

### Auslösen einer vollständigen Synchronisierung aller Kennwörter
Wenn Sie die Filterkonfiguration geändert haben, müssen Sie eine vollständige Synchronisierung aller Kennwörter auslösen, sodass die Kennwörter der jetzt zum Bereich gehörenden Benutzer synchronisiert werden.

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


## Deaktivieren der Kennwortsynchronisierung

Sie deaktivieren die Kennwortsynchronisierung durch erneute Ausführung des Azure AD Connect-Konfigurations-Assistenten. Deaktivieren Sie bei Aufforderung durch den Assistenten das Kontrollkästchen "Kennwortsynchronisierung".


> [AZURE.NOTE]Dieser Vorgang löst eine vollständige Synchronisierung aus. Vollständige Synchronisierungszyklen dauern im Allgemeinen länger als andere Synchronisierungszyklen.

Nach Ausführung des Konfigurations-Assistenten synchronisiert Ihr Mandant keine Kennwörter mehr. Neue Kennwortänderungen werden nicht mit der Cloud synchronisiert. Benutzer, deren Kennwörter zuvor synchronisiert wurden, können sich weiterhin mit diesen Kennwörtern anmelden, bis sie ihre Kennwörter in der Cloud manuell ändern.



## Zusätzliche Ressourcen

* [Azure AD Connect-Synchronisierung: Anpassen von Synchronisierungsoptionen](active-directory-aadconnectsync-whatis.md)
* [Integrieren Ihrer lokalen Identitäten in Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-implement-password-synchronization/IC759788.png
[2]: ./media/active-directory-aadsync-implement-password-synchronization/IC662504.png
[3]: ./media/active-directory-aadsync-implement-password-synchronization/IC662505.png
[4]: ./media/active-directory-aadsync-implement-password-synchronization/IC662506.png
[5]: ./media/active-directory-aadsync-implement-password-synchronization/IC662507.png

<!---HONumber=August15_HO9-->