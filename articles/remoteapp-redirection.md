<properties 
    pageTitle="Verwenden der Umleitung in Azure RemoteApp" 
    description="Erfahren Sie, wie Sie die Umleitung in RemoteApp konfigurieren und nutzen." 
    services="remoteapp" 
    solutions="" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/21/2015" 
    ms.author="elizapo" />

# Verwenden der Umleitung in Azure RemoteApp

Mithilfe der Geräteumleitung können Ihre Benutzer mit Remote-Apps auf den Geräten interagieren, die an ihren lokalen Computer, Ihr Smartphone oder Tablet angeschlossen sind. Wenn Sie z. B. Skype über RemoteApp bereitgestellt haben, benötigt der Benutzer für das Arbeiten mit Skype die auf dem PC installierte Kamera. Dies gilt auch für Drucker, Lautsprecher, Monitore und verschiedene über USB angeschlossene Peripheriegeräte.

RemoteApp nutzt das Remotedesktopprotokoll (RDP) und RemoteFX zum Bereitstellen der Umleitung.

## Welche Umleitung ist standardmäßig aktiviert?
Bei Verwendung von RemoteApp sind die folgenden Umleitungen standardmäßig aktiviert. Die Informationen in Klammern zeigen die RDP-Einstellung.

- Audio auf dem lokalen Computer wiedergeben (**Auf diesem Computer wiedergeben**). (audiomode:i:0)
- Audio vom lokalen Computer aufzeichnen und an den Remotecomputer senden (** Von diesem Computer aufzeichnen **). (audiocapturemode:i:1)
- An lokalen Drucker ausgeben (redirectprinters:i:1)
- COM-Anschlüsse (redirectcomports:i:1)
- Smartcardgerät (redirectsmartcards:i:1)
- Zwischenablage (Möglichkeit zum Kopieren und Einfügen) (redirectclipboard:i:1)
- Schriftartglättung deaktivieren (Schriftartglättung zulassen: I:1)
- Alle unterstützten Plug & Play-Geräte umleiten (devicestoredirect:s:*)

## Welche andere Umleitung ist verfügbar?
Zwei Umleitungsoptionen sind standardmäßig deaktiviert:

- Laufwerkumleitung (Laufwerkzuordnung): Die lokalen Laufwerke Ihres Computers werden zugeordnete Laufwerke in der Remotesitzung. Dies ermöglicht Ihnen das Speichern oder Öffnen von Dateien auf Ihren lokalen Laufwerken, während Sie in der Remotesitzung arbeiten. 
- USB-Umleitung: Sie können die an Ihren lokalen Computer angeschlossenen USB-Geräte in der Remotesitzung verwenden.

## Ändern der Umleitungseinstellungen in RemoteApp
Sie können die Einstellungen für die Geräteumleitung für eine Auflistung mit Microsoft Azure PowerShell und dem SDK ändern. Nach der Installation der neuen PowerShell samt SDK müssen Sie sie zunächst für die Verwaltung Ihres Abonnements konfigurieren, wie unter [Installieren und Konfigurieren von Azure PowerShell](Install-Powershell-configure.md) beschrieben.

Verwenden Sie anschließend einen Befehl ähnlich dem folgenden, um die benutzerdefinierten RDP-Eigenschaften festzulegen:

	Set-AzureRemoteAppCollection -CollectionName <Auflistungsname>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"
    
(Beachten Sie, dass "`n" als Trennzeichen zwischen den einzelnen Eigenschaften verwendet wird.)

Führen Sie das folgende Cmdlet zum Abrufen einer Liste konfigurierter benutzerdefinierter RDP-Eigenschaften aus. Beachten Sie, dass als Ausgabeergebnisse nur benutzerdefinierte Eigenschaften und keine Standardeigenschaften angezeigt werden:  

    Get-AzureRemoteAppCollection -CollectionName <Auflistungsname> 
 
Wenn Sie benutzerdefinierte Eigenschaften festlegen, müssen Sie alle benutzerdefinierten Eigenschaften jedes Mal angeben. Andernfalls wird die Einstellung auf "Deaktiviert" zurückgesetzt.   

### Typische Beispiele
Verwenden Sie das folgende Cmdlet, um die Laufwerkumleitung zu aktivieren:  

	Set-AzureRemoteAppCollection -CollectionName <Auflistungsname>  -CustomRdpProperty "drivestoredirect:s:*"

Verwenden Sie dieses Cmdlet. um die USB- und Laufwerkumleitung zu aktivieren: 

	Set-AzureRemoteAppCollection -CollectionName <Auflistungsname>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Verwenden Sie dieses Cmdlet, um die gemeinsame Nutzung der Zwischenablage zu deaktivieren:  

	Set-AzureRemoteAppCollection -CollectionName <Auflistungsname>  -CustomRdpProperty "redirectclipboard:i:0"

Achten Sie darauf, dass sich alle Benutzer in der Auflistung vollständig abmelden (und nicht nur die Verbindung trennen), bevor Sie die Änderung testen. Um sicherzustellen, dass Benutzer vollständig abgemeldet sind, wechseln Sie im Azure-Portal zur Registerkarte **Sitzungen** der Auflistung, und melden alle Benutzer ab, die getrennt oder abgemeldet sind. Mitunter kann es mehrere Sekunden dauern, bis die lokalen Laufwerke in der Sitzung in Explorer angezeigt werden.

## Ändern der USB-Umleitungseinstellungen für Ihren Windows-Client

Wenn Sie die USB-Umleitung auf einem Computer verwenden möchten, der sich mit RemoteApp verbindet, müssen zwei Aktionen erfolgen. 1. Der Systemadministrator muss die USB-Umleitung mithilfe von Azure PowerShell auf Auflistungsebene aktivieren. 2. Auf jedem Gerät, auf dem Sie die USB-Umleitung verwenden möchten, müssen Sie eine Gruppenrichtlinie aktivieren, die dies zulässt. Dieser Schritt muss für jeden Benutzer durchgeführt werden, der die USB-Umleitung wünscht.
   
> [AZURE.NOTE] Die USB-Umleitung mit Azure RemoteApp wird nur für Windows-Computer unterstützt.

### Aktivieren der USB-Umleitung für die RemoteApp-Auflistung
Verwenden Sie das folgende Cmdlet, um die USB-Umleitung auf Auflistungsebene zu aktivieren:
    Set-AzureRemoteAppCollection -CollectionName <Auflistungsname> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### Aktivieren der USB-Umleitung für den Clientcomputer

So konfigurieren Sie die Einstellungen für die USB-Umleitung auf Ihrem Computer

1. Öffnen Sie den Editor für lokale Gruppenrichtlinien (GPEDIT. MSC). (Führen Sie "gpedit.msc" an einer Eingabeaufforderung ausführen.)
2. Öffnen Sie **Computerkonfiguration\Richtlinien\Administrative Vorlagen\Windows-Komponenten\Remotedesktopdienste\Remotedesktopverbindungs-Client\RemoteFX-USB-Geräteumleitung**.
3. Doppelklicken Sie auf **RDP-Umleitung von anderen unterstützen RemoteFX-USB-Geräten auf diesem Computer zulassen**.
4. Wählen Sie **Aktiviert** und dann in "Zugriffsrechte für RemoteFX USB-Umleitung" **Administratoren und Benutzer** aus.
5. Öffnen Sie eine Eingabeaufforderung mit Administratorrechten, und führen Sie den folgenden Befehl aus: 

    gpupdate /force
6. Starten Sie den Computer neu.

Sie können das Tool für die Gruppenrichtlinienverwaltung auch zum Erstellen und Anwenden der USB-Umleitungsrichtlinie für alle Computer in Ihrer Domäne verwenden:

1. Melden Sie sich am Domänencontroller als Domänenadministrator an.
2. Öffnen Sie die Gruppenrichtlinien-Verwaltungskonsole. (Klicken Sie auf **Start > Verwaltung > Gruppenrichtlinienverwaltung**.)
3. Navigieren Sie zur Domäne oder Organisationseinheit, für die Sie die Richtlinie erstellen möchten.
4. Klicken Sie mit der rechten Maustaste auf **Standarddomänenrichtlinie**, und klicken Sie dann auf **Bearbeiten**.
5. Öffnen Sie **Computerkonfiguration\Richtlinien\Administrative Vorlagen\Windows-Komponenten\Remotedesktopdienste\Remotedesktopverbindungs-Client\RemoteFX-USB-Geräteumleitung**.
6. Doppelklicken Sie auf **RDP-Umleitung von anderen unterstützen RemoteFX-USB-Geräten auf diesem Computer zulassen**.
7. Wählen Sie **Aktiviert** und dann in "Zugriffsrechte für RemoteFX USB-Umleitung" **Administratoren und Benutzer** aus.
8. Klicken Sie auf **OK**. 

<!--HONumber=54-->