<properties
	pageTitle="Problembehandlung bei der Remotedesktopverbindung mit einem virtuellen Azure-Computer | Microsoft Azure"
	description="Problembehandlung bei Remotedesktop.Verbindungsfehlern auf einem virtuellen Windows-Computer Sie erhalten schnelle Schritte zur Lösung, Hilfe zur jeweiligen Fehlermeldung und Informationen zur ausführlichen Problembehandlung für Netzwerke."
	keywords="Remotedesktop-Fehler,Remotedesktop-Verbindungsfehler,Verbindung mit virtuellem Computer nicht möglich,Remotedesktop-Problembehandlung"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/25/2016"
	ms.author="dkshir"/>

# Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer

Die Remotedesktopverbindung (RDP) mit Ihrem Windows-basierten virtuellen Azure-Computer kann aus verschiedenen Gründen fehlschlagen. Das Problem kann mit dem Remotedesktopdienst auf dem virtuellen Computer, der Netzwerkverbindungoder dem Remotedesktopclient auf Ihrem Hostcomputer zusammenhängen. Dieser Artikel hilft Ihnen, die Ursachen zu ermitteln und zu korrigieren.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Dieser Artikel gilt für virtuelle Azure-Computer, auf denen Windows ausgeführt wird. Informationen zu virtuellen Azure-Computern mit Linux finden Sie unter [Behandeln von Problemen mit SSH-Verbindungen mit einem virtuellen Azure-Computer](virtual-machines-troubleshoot-ssh-connections.md).

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.


<a id="quickfixrdp"></a>
## Beheben allgemeiner Remotedesktop-Fehler

Dieser Abschnitt enthält Schritte für die schnelle Behebung häufig auftretender Probleme mit der Remotedesktopverbindung.

### Virtuelle Computer, die mit dem klassischen Bereitstellungsmodell erstellt wurden

Diese Schritte können dabei helfen, die meisten Remotedesktop-Verbindungsfehler bei virtuellen Azure-Computern zu beheben, die mit dem klassischen Bereitstellungsmodell erstellt wurden. Versuchen Sie nach jedem Schritt, die Verbindung mit dem virtuellen Computer erneut herzustellen.

- Setzen Sie den Remotedesktopdienst über das [Azure-Portal](https://portal.azure.com) zurück, um die Startprobleme mit dem RDP-Server zu beheben.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Windows-Computer“ > **Remotezugriff zurücksetzen...**.

- Starten Sie den virtuellen Computer neu, um andere Startprobleme zu beheben.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Windows-Computer“ > **Neu starten**.

- Ändern Sie die Größe des virtuellen Computers, um Hostprobleme zu beheben.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Windows-Computer“ > **Einstellungen** > **Größe**. Ausführliche Schritte finden Sie unter [Ändern der Größe des virtuellen Computers](https://msdn.microsoft.com/library/dn168976.aspx).

- Überprüfen Sie das Konsolenprotokoll oder den Screenshot des virtuellen Computers, um Startprobleme zu beheben.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Windows-Computer“ > **Einstellungen** > **Startdiagnose**.

- Überprüfen Sie die Ressourcenintegrität des virtuellen Computers auf etwaige Plattformprobleme.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Windows-Computer“ > **Einstellungen** > **Integrität prüfen**.

### Virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden

Diese Schritte können dabei helfen, die meisten Remotedesktop-Verbindungsfehler bei virtuellen Azure-Computern zu beheben, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden. Versuchen Sie nach jedem Schritt, die Verbindung mit dem virtuellen Computer erneut herzustellen.

- _Zurücksetzen des Remotezugriffs_ mithilfe von Powershell<br> a. [Installieren Sie Azure PowerShell, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her](../powershell-install-configure.md), indem Sie die Azure AD-Methode verwenden. Beachten Sie, dass Sie in den neueren Versionen von Azure PowerShell 1.0.x nicht in den Ressourcen-Manager-Modus wechseln müssen.

	b. Setzen Sie Ihre RDP-Verbindung mit einem der folgenden Azure PowerShell-Befehle zurück. Ersetzen Sie die `myRG`, `myVM`, `myVMAccessExtension` und den Speicherort durch für Ihr Setup relevante Werte.

	```
	Set-AzureRmVMExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccessExtension" -ExtensionType "VMAccessAgent" -Publisher "Microsoft.Compute" -typeHandlerVersion "2.0" -Location Westus
	```
	ODER<br>

  ```
  Set-AzureRmVMAccessExtension -ResourceGroupName "myRG" -VMName "myVM" -Name "myVMAccess" -Location Westus
  ```

- Starten Sie den virtuellen Computer neu, um andere Startprobleme zu beheben.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer** > „Ihr virtueller Windows-Computer“ > **Neu starten**.

- Ändern Sie die Größe des virtuellen Computers, um Hostprobleme zu beheben.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer** > „Ihr virtueller Windows-Computer“ > **Einstellungen** > **Größe**.

- Überprüfen Sie das Konsolenprotokoll oder den Screenshot des virtuellen Computers, um Startprobleme zu beheben.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer** > „Ihr virtueller Windows-Computer“ > **Einstellungen** > **Startdiagnose**.


Fahren Sie mit dem nächsten Abschnitt fort, wenn die Remotedesktop-Verbindungsfehler durch die oben genannten Schritte nicht behoben wurden.

## Beheben von spezifischen Remotedesktop-Verbindungsfehlern

Hier sind die häufigsten Fehler angegeben, die bei dem Versuch auftreten können, eine Remotedesktopverbindung mit Ihrem virtuellen Azure-Computer herzustellen:

1. [Fehler bei Remotedesktopverbindung: Die Verbindung mit der Remotesitzung wurde getrennt, da keine Lizenzserver für Remotedesktop vorhanden sind](#rdplicense).

2. [Fehler bei Remotedesktopverbindung: Der Computer „Name“ kann von Remotedesktop nicht gefunden werden](#rdpname).

3. [Fehler bei Remotedesktopverbindung: Authentifizierungsfehler. Die lokale Sicherheitsautorität (LSA) ist nicht erreichbar](#rdpauth).

4. [Fehler bei Windows-Sicherheit: Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden.](#wincred)

5. [Fehler bei Remotedesktopverbindung: Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen.](#rdpconnect)

<a id="rdplicense"></a>
### Fehler bei Remotedesktopverbindung: Die Verbindung mit der Remotesitzung wurde getrennt, da keine Lizenzserver für Remotedesktop vorhanden sind.

Ursache: Die 120-Tage-Kulanzfrist für die Rolle "Remotedesktopserver" ist abgelaufen, und Sie müssen Lizenzen installieren.

Als Problemumgehung speichern Sie eine lokale Kopie der RDP-Datei aus dem Portal, und führen Sie diesen Befehl an einer Windows PowerShell-Eingabeaufforderung aus, um eine Verbindung herzustellen. Damit wird die Lizenz nur für diese Verbindung deaktiviert.

		mstsc <File name>.RDP /admin

Wenn Sie eigentlich nicht mehr als zwei gleichzeitige Remotedesktopverbindungen mit dem virtuellen Computer benötigen, können Sie mit Server-Manager die Rolle „Remotedesktopserver“ entfernen.

Lesen Sie auch den Blogbeitrag [Azure-VM schlägt fehl mit "Keine Lizenzserver für Remotedesktop verfügbar"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx) .

<a id="rdpname"></a>
### Fehler bei Remotedesktopverbindung: Der Computer „Name“ kann von Remotedesktop nicht gefunden werden.

Ursache: Der Remotedesktopclient auf Ihrem Computer konnte den Namen des Computers, der in den Einstellungen der RDP-Datei angegeben ist, nicht auflösen.

Lösungsvorschläge:

- Wenn Sie sich im Intranet einer Organisation befinden, sollten Sie sicherstellen, dass Ihr Computer Zugriff auf den Proxyserver hat und diesem HTTPS-Datenverkehr senden kann.
- Wenn Sie eine lokal gespeicherte RDP-Datei verwenden, können Sie versuchen, die vom Portal generierte Datei zu verwenden. Dadurch wird sichergestellt, dass Sie den richtigen DNS-Namen für den virtuellen Computer oder den Clouddienst und den Endpunktport des virtuellen Computers verwenden. Dies ist eine RDP-Beispieldatei, die vom Portal generiert wurde:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

Der Adressteil in dieser RDP-Datei besteht aus dem vollqualifizierten Domänennamen des Clouddiensts, der den virtuellen Computer enthält (in diesem Beispiel tailspin-azdatatier.cloudapp.net), und dem externen TCP-Port des Endpunkts für den Remotedesktop-Datenverkehr (55919).

<a id="rdpauth"></a>
### Fehler bei Remotedesktopverbindung: Authentifizierungsfehler. Die lokale Sicherheitsautorität (LSA) ist nicht erreichbar.

Ursache: Der virtuelle Zielcomputer konnte die Sicherheitsautorität im Benutzernamenteil Ihrer Anmeldeinformationen nicht finden.

Wenn Ihr Benutzername das Format *SecurityAuthority*\*UserName* (Beispiel: „CORP\\User1“) aufweist, ist *SecurityAuthority* entweder der Computername des virtuellen Computers (für die lokale Sicherheitsautorität) oder ein Active Directory-Domänenname.

Lösungsvorschläge:

- Wenn Ihr Benutzerkonto lokal für den virtuellen Computer gilt, sollten Sie überprüfen, ob der Name richtig geschrieben ist.
- Wenn sich das Konto in einer Active Directory-Domäne befindet, sollten Sie die richtige Schreibweise des Domänennamens überprüfen.
- Falls es ein Active Directory-Domänenkonto ist und der Domänenname richtig geschrieben ist, sollten Sie sicherstellen, dass in der Domäne ein Domänencontroller verfügbar ist. Dies kann ein häufiges Problem in einem virtuellen Azure-Netzwerk sein, das Domänencontroller enthält und in dem ein Domänencontrollercomputer nicht gestartet wurde. Um dieses Problem zu umgehen, können Sie anstelle eines Domänenkontos ein lokales Administratorkonto verwenden.

<a id="wincred"></a>
### Fehler bei Windows-Sicherheit: Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden.

Ursache: Der virtuelle Zielcomputer konnte Ihren Kontonamen und das Kennwort nicht überprüfen.

Ein Windows-basierter Computer kann die Anmeldeinformationen eines lokalen Kontos oder eines Domänenkontos überprüfen.

- Verwenden Sie für lokale Konten die Syntax *Computername*\*Benutzername* (Beispiel: „SQL1\\Admin4798“).
- Verwenden Sie für Domänenkonten die Syntax *Domänenname*\*Benutzername* (Beispiel: „CONTOSO\\johndoe“).

Bei virtuellen Computern, die Sie in einer neuen Active Directory-Gesamtstruktur zu Domänencontrollern heraufgestuft haben, wird auch das lokale Administratorkonto, mit dem Sie sich angemeldet haben, in der neuen Gesamtstruktur und Domäne in ein äquivalentes Konto mit dem gleichen Kennwort konvertiert. Das lokale Konto wird gelöscht. Wenn Sie sich beispielsweise mit dem lokalen Konto „DC1\\DCAdmin“ angemeldet und den virtuellen Computer zu einem Domänencontroller in einer neuen Gesamtstruktur für die Domäne „corp.contoso.com“ heraufgestuft haben, dann wird das lokale Konto DC1\\DCAdmin gelöscht und ein neues Domänenkonto („CORP\\DCAdmin“) mit demselben Kennwort erstellt.

Stellen Sie sicher, dass der Kontoname ein Name ist, der vom virtuellen Computer als gültiges Konto bestätigt werden kann, und dass das Kennwort korrekt ist.

Wenn Sie das Kennwort für das lokale Administratorkonto ändern müssen, finden Sie hierzu weitere Informationen unter [Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Computer in Windows](virtual-machines-windows-reset-password.md).

<a id="rdpconnect"></a>
### Fehler bei Remotedesktopverbindung: Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen.

Ursache: Das Konto zum Herstellen der Verbindung verfügt nicht über Remotedesktop-Anmelderechte.

Jeder Windows-Computer hat eine lokale Gruppe von Remotedesktopbenutzern, in der die Konten und Gruppen enthalten sind, die sich per Remoteverbindung anmelden können. Auch Mitglieder der lokalen Gruppe „Administratoren“ haben Zugriff, obwohl diese Konten nicht in der lokalen Gruppe „Remotedesktopbenutzer“ aufgelistet werden. Bei Computern, die Mitglieder einer Domäne sind, enthält die lokalen Gruppe "Administratoren" auch die Domänenadministratoren für die Domäne.

Stellen Sie sicher, dass das Konto, das Sie zum Herstellen der Verbindung verwenden, über Remotedesktop-Anmelderechte verfügt. Verwenden Sie als Problemumgehung ein Domänenkonto oder lokales Administratorkonto, um per Remotedesktop eine Verbindung herzustellen. Verwenden Sie anschließend das Snap-In „Computerverwaltung“ (**Systemprogramme > Lokale Benutzer und Gruppen > Gruppen > Remotedesktopbenutzer**), um das gewünschte Konto der lokalen Gruppe mit den Remotedesktopbenutzern hinzuzufügen.

## Problembehandlung bei allgemeinen Remotedesktop-Fehlern

Wenn keiner dieser Fehler auftritt und Sie trotzdem keine Verbindung mit dem virtuellen Computer per Remotedesktop herstellen können, können Sie zusätzlich im [ausführlichen Handbuch zur Problembehandlung für Remotedesktop](virtual-machines-rdp-detailed-troubleshoot.md) nachschlagen.


## Zusätzliche Ressourcen

[Azure IaaS (Windows) und Diagnoseprogramme](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer](virtual-machines-windows-reset-password.md)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

[Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-ssh-connections.md)

[Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0128_2016-->