<properties 
    pageTitle="Verwenden von Umleitungen in Azure RemoteApp" 
    description="Informieren Sie sich über die Konfiguration und Verwendung der Umleitung in RemoteApp." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/10/2015" 
    ms.author="elizapo" />

# Verwenden von Umleitungen in Azure RemoteApp

Mit der Geräteumleitung können Benutzer über Geräte, die an ihren lokalen Computer, an ihr Smartphone oder an ihr Tablet angeschlossen sind, mit Remoteanwendungen interagieren. Ein Beispiel: Wenn Sie Skype über Azure RemoteApp bereitstellen, muss die Kamera, die auf dem PC des Benutzers installiert ist, mit Skype verwendbar sein. Gleiches gilt für Drucker, Lautsprecher, Monitore und eine Reihe von USB-Peripheriegeräten.

RemoteApp verwendet für die Umleitung das Remotedesktopprotokoll (RDP) und RemoteFX.

## Welche Umleitungen sind standardmäßig aktiviert?
Bei Verwendung von RemoteApp sind die folgenden Umleitungen standardmäßig aktiviert. Bei den Angaben in Klammern handelt es sich jeweils um die RDP-Einstellung.

- Sound auf dem lokalen Computer wiedergeben (**Auf diesem Computer wiedergeben**) (audiomode:i:0)
- Audio des lokalen Computers aufzeichnen und an den Remotecomputer senden (**Von diesem Computer aufzeichnen**) (audiocapturemode:i:1)
- Auf lokalen Druckern drucken (redirectprinters:i:1)
- COM-Anschlüsse (redirectcomports:i:1)
- Smartcardgerät (redirectsmartcards:i:1)
- Zwischenablage (zum Kopieren und Einfügen) (redirectclipboard:i:1)
- Schriftartglättung (allow font smoothing:i:1)
- Alle unterstützten Plug & Play-Geräte umleiten (devicestoredirect:s:*)

## Welche anderen Umleitungen sind verfügbar?
Zwei Umleitungsoptionen sind standardmäßig deaktiviert:

- Laufwerkumleitung (Laufwerkzuordnung): Die Laufwerke Ihres lokalen Computers werden in der Remotesitzung zu zugeordneten Laufwerken. Dadurch können Sie Dateien im Rahmen der Remotesitzung auf Ihren lokalen Laufwerken speichern oder öffnen. 
- USB-Umleitung: Die an den lokalen Computer angeschlossenen USB-Geräte können im Rahmen der Remotesitzung verwendet werden.

## Ändern der Umleitungseinstellungen in RemoteApp
Die Geräteumleitungseinstellungen für eine Sammlung können mithilfe von Microsoft Azure PowerShell mit SDK geändert werden. Installieren Sie zunächst die neue Version von PowerShell und SDK, und konfigurieren Sie sie für die Verwaltung Ihres Abonnements (siehe [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md)).

Legen Sie dann mithilfe eines Befehls wie dem folgenden die benutzerdefinierten RDP-Eigenschaften fest:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"
    
(Hinweis. *`n* fungiert als Trennzeichen zwischen den einzelnen Eigenschaften.)

Führen Sie das folgende Cmdlet aus, um eine Liste mit den konfigurierten benutzerdefinierten RDP-Eigenschaften abzurufen. Beachten Sie, dass die Ausgabe keine Standardeigenschaften, sondern nur benutzerdefinierte Eigenschaften enthält:

    Get-AzureRemoteAppCollection -CollectionName <collection name> 
 
Wenn Sie benutzerdefinierte Eigenschaften festlegen, müssen Sie jedes Mal alle benutzerdefinierten Eigenschaften angeben. Andernfalls wird die Einstellung wieder deaktiviert.

### Typische Beispiele
Verwenden Sie das folgende Cmdlet, um die Laufwerkumleitung zu aktivieren:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Verwenden Sie das folgende Cmdlet, um sowohl die USB- als auch die Laufwerkumleitung zu aktivieren:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Verwenden Sie das folgende Cmdlet, um die gemeinsame Verwendung der Zwischenablage zu deaktivieren:

	Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT]Stellen Sie vor dem Testen der Änderung sicher, dass alle Benutzer in der Sammlung nicht nur getrennt, sondern vollständig abgemeldet sind. Navigieren Sie hierzu in der Sammlung im Azure-Portal zur Registerkarte **Sitzungen**, und melden Sie alle getrennten oder angemeldeten Benutzer ab. Manchmal dauert es etwas, bis die lokalen Laufwerke im Rahmen der Sitzung im Explorer angezeigt werden.

## Ändern der USB-Umleitungseinstellungen in Ihrem Windows-Client

Wenn Sie die USB-Umleitung auf einem mit RemoteApp verbundenen Computer verwenden möchten, müssen zwei Voraussetzungen erfüllt werden: 1. Der Administrator muss mithilfe von Azure PowerShell die USB-Umleitung auf Sammlungsebene aktivieren. 2. Sie müssen auf jedem Gerät, auf dem Sie die USB-Umleitung verwenden möchten, eine Gruppenrichtlinie aktivieren, die die Umleitung zulässt. Dieser Schritt muss für jeden Benutzer ausgeführt werden, der die USB-Umleitung verwenden möchte.
   
> [AZURE.NOTE]Die USB-Umleitung mit Azure RemoteApp wird nur für Windows-Computer unterstützt.

### Aktivieren der USB-Umleitung für die RemoteApp-Sammlung
Verwenden Sie das folgende Cmdlet, um die USB-Umleitung auf Sammlungsebene zu aktivieren:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### Aktivieren der USB-Umleitung für den Clientcomputer

Gehen Sie zum Konfigurieren der USB-Umleitungseinstellungen auf Ihrem Computer wie folgt vor:

1. Öffnen Sie den Editor für lokale Gruppenrichtlinien (GPEDIT. MSC). (Führen Sie „gpedit.msc“ an einer Eingabeaufforderung aus.)
2. Öffnen Sie **Computerkonfiguration\\Richtlinien\\Administrative Vorlagen\\Windows-Komponenten\\Remotedesktopdienste\\Remotedesktopverbindungs-Client\\RemoteFX USB-Geräteumleitung**.
3. Doppelklicken Sie auf **RDP-Umleitung für andere unterstützte RemoteFX USB-Geräte auf diesem Computer zulassen**.
4. Wählen Sie **Aktiviert** und anschließend unter **Zugriffsrechte für RemoteFX USB-Umleitung** die Option für Administratoren und Benutzer aus.
5. Öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie den folgenden Befehl ein: 

		gpupdate /force
6. Starten Sie den Computer neu.

Sie können auch das Gruppenrichtlinienverwaltungstool verwenden, um die USB-Umleitungsrichtlinie zu erstellen und auf alle Computer in Ihrer Domäne anzuwenden:

1. Melden Sie sich als Domänenadministrator beim Domänencontroller an.
2. Öffnen Sie die Gruppenrichtlinien-Verwaltungskonsole. (Klicken Sie auf **Start > Verwaltung > Gruppenrichtlinienverwaltung**.)
3. Navigieren Sie zu der Domäne oder Organisationseinheit, für die Sie die Richtlinie erstellen möchten.
4. Klicken Sie mit der rechten Maustaste auf **Standarddomänenrichtlinie**, und klicken Sie anschließend auf **Bearbeiten**.
5. Öffnen Sie **Computerkonfiguration\\Richtlinien\\Administrative Vorlagen\\Windows-Komponenten\\Remotedesktopdienste\\Remotedesktopverbindungs-Client\\RemoteFX USB-Geräteumleitung**.
6. Doppelklicken Sie auf **RDP-Umleitung für andere unterstützte RemoteFX USB-Geräte auf diesem Computer zulassen**.
7. Wählen Sie **Aktiviert** und anschließend unter **Zugriffsrechte für RemoteFX USB-Umleitung** die Option für Administratoren und Benutzer aus.
8. Klicken Sie auf **OK**.  

<!---HONumber=August15_HO7-->