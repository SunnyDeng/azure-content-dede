<properties
	pageTitle="Problembehandlung bei der Remotedesktopverbindung auf einem virtuellen Windows-Computer | Microsoft Azure"
	description="Problembehandlung bei Remotedesktopverbindungen oder RDP-Verbindungen mit einem Windows-basierten virtuellen Azure-Computer"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/16/2015"
	ms.author="dkshir"/>

# Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In diesem Artikel erfahren Sie, wie Sie Probleme bei einem virtuellen Computer behandeln, der mit dem klassischen oder Ressourcen-Manager-Bereitstellungsmodell erstellt wurde.

Es kann unterschiedliche Gründe haben, warum Remotedesktop (RDP) keine Verbindung mit Ihrem Windows-basierten virtuellen Azure-Computer herstellen kann. Dieser Artikel hilft Ihnen, die Ursachen zu ermitteln und zu korrigieren.

> [AZURE.NOTE]Dieser Artikel gilt nur für virtuelle Azure-Computer, auf denen Windows ausgeführt wird. Informationen zur Problembehandlung von Verbindungen mit virtuellen Azure-Computern mit Linux finden Sie in [diesem Artikel](virtual-machines-troubleshoot-ssh-connections.md).

## Kontaktieren des Azure-Kundensupports

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten.

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).


## Grundlegende Schritte

Diese einfachen Schritte sind hilfreich, um die meisten Remotedesktop-Verbindungsfehler zu beheben:

- Setzen Sie den Remotedesktopdienst im [Azure-Portal](https://portal.azure.com) zurück. Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > Ihr virtueller Windows-Computer > **Remotezugriff zurücksetzen**.

![Remotezugriff zurücksetzen](./media/virtual-machines-troubleshoot-remote-desktop-connections/Portal-RDP-Reset-Windows.png)

- [Starten Sie den virtuellen Computer neu.](https://msdn.microsoft.com/library/azure/dn763934.aspx)

- [Ändern Sie die Größe des virtuellen Computers.](https://msdn.microsoft.com/library/dn168976.aspx)


## Ausführen des Azure IaaS-Diagnosepakets unter Windows

Wenn Sie die Problembehandlung über einen Computer mit Windows 8, Windows 8.1, Windows Server 2012 oder Windows Server 2012 R2 durchführen, können Sie auch das [Azure IaaS (Windows)-Diagnosepaket](http://support.microsoft.com/kb/2976864) ausführen. Mit diesem Paket können viele häufig auftretende Probleme mit Remotedesktop gelöst werden.

1.	Klicken Sie auf der Seite [Unterstützung Diagnose](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) auf **Microsoft Azure IaaS (Windows)-Diagnosepaket**. Klicken Sie auf **Erstellen**, um eine neue Diagnosesitzung zu erstellen. Sie können diese Sitzung entweder für einen anderen Zielcomputer **freigeben** oder sie auf den lokalen Computer **herunterladen**.
2.	Sie können die Sitzung **ausführen**, die Microsoft-Lizenzvereinbarung **akzeptieren** und das Diagnosetool **starten**.
3.	Authentifizieren Sie Ihr Azure-Abonnement im Popupfenster, und befolgen Sie die angezeigten Aufforderungen.
4.	Wählen Sie auf der Seite **Welches der folgenden Probleme tritt mit Ihrem virtuellen Azure-Computer auf?** das Problem **RDP-Verbindung mit einem virtuellen Azure-Computer (Neustart erforderlich)**.

Falls das Azure IaaS-Diagnosepaket nicht ausgeführt werden konnte oder nicht hilfreich war, fahren Sie mit dem nächsten Abschnitt fort, um das Problem anhand des Fehlers zu lösen, den Sie vom Remotedesktopclient erhalten.


## Häufige RDP-Fehler

Hier sind die häufigsten Fehler angegeben, die bei dem Versuch auftreten können, eine Remotedesktopverbindung mit Ihrem virtuellen Azure-Computer herzustellen:

1. [Fehler bei Remotedesktopverbindung: Die Verbindung mit der Remotesitzung wurde getrennt, da keine Lizenzserver für Remotedesktop vorhanden sind.](#rdplicense)

2. [Fehler bei Remotedesktopverbindung: Der Computer "Name" kann von Remotedesktop nicht gefunden werden.](#rdpname)

3. [Fehler bei Remotedesktopverbindung: Authentifizierungsfehler. Die lokale Sicherheitsautorität (LSA) ist nicht erreichbar](#rdpauth).

4. [Fehler bei Windows-Sicherheit: Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden.](#wincred)

5. [Fehler bei Remotedesktopverbindung: Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen.](#rdpconnect)

<a id="rdplicense"></a>
### Fehler bei Remotedesktopverbindung: Die Verbindung mit der Remotesitzung wurde getrennt, da keine Lizenzserver für Remotedesktop vorhanden sind.

Ursache: Die 120-Tage-Kulanzfrist für die Rolle "Remotedesktopserver" ist abgelaufen, und Sie müssen Lizenzen installieren.

Als Problemumgehung speichern Sie eine lokale Kopie der RDP-Datei aus dem Azure-Portal, und führen Sie diesen Befehl an einer Windows PowerShell-Eingabeaufforderung aus, um eine Verbindung herzustellen.

		mstsc <File name>.RDP /admin

Damit wird die Lizenz nur für diese Verbindung deaktiviert.

Wenn Sie eigentlich nicht mehr als zwei gleichzeitige Remotedesktopverbindungen mit dem virtuellen Computer benötigen, können Sie mit Server-Manager die Rolle „Remotedesktopserver“ entfernen.

Lesen Sie auch den Blogbeitrag [Azure-VM schlägt fehl mit "Keine Lizenzserver für Remotedesktop verfügbar"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx) .

<a id="rdpname"></a>
### Fehler bei Remotedesktopverbindung: Der Computer „Name“ kann von Remotedesktop nicht gefunden werden.

Ursache: Der Remotedesktopclient auf Ihrem Computer konnte den Namen des Computers, der in den Einstellungen der RDP-Datei angegeben ist, nicht auflösen.

Lösungsvorschläge:

- Wenn Sie sich im Intranet einer Organisation befinden, sollten Sie sicherstellen, dass Ihr Computer Zugriff auf den Proxyserver hat und diesem HTTPS-Datenverkehr senden kann.
- Wenn Sie eine lokal gespeicherte RDP-Datei verwenden, können Sie versuchen, die vom Azure-Portal generierte Datei zu nutzen. Dadurch wird sichergestellt, dass Sie den richtigen DNS-Namen für den virtuellen Computer oder den Clouddienst und den Endpunktport des virtuellen Computers verwenden. Es folgt eine RDP-Beispieldatei, die vom Azure-Portal generiert wird:

		full address:s:tailspin-azdatatier.cloudapp.net:55919
		prompt for credentials:i:1

Der Adressteil in dieser RDP-Datei besteht aus dem vollqualifizierten Domänennamen des Clouddiensts, der den virtuellen Computer enthält (in diesem Beispiel tailspin-azdatatier.cloudapp.net), und dem externen TCP-Port des Endpunkts für den Remotedesktop-Datenverkehr (55919).

<a id="rdpauth"></a>
### Fehler bei Remotedesktopverbindung: Authentifizierungsfehler. Die lokale Sicherheitsautorität (LSA) ist nicht erreichbar.

Ursache: Der virtuelle Zielcomputer konnte die Sicherheitsautorität im Benutzernamenteil Ihrer Anmeldeinformationen nicht finden.

Wenn Ihr Benutzername das Format *SecurityAuthority*\*UserName* (Beispiel: "CORP\\User1") aufweist, ist *SecurityAuthority* entweder der Computername des virtuellen Computers (für die lokale Sicherheitsautorität) oder ein Active Directory-Domänenname.

Lösungsvorschläge:

- Wenn Ihr Benutzerkonto lokal für den virtuellen Computer gilt, sollten Sie überprüfen, ob der Name richtig geschrieben ist.
- Wenn sich das Konto in einer Active Directory-Domäne befindet, sollten Sie die richtige Schreibweise des Domänennamens überprüfen.
- Falls es ein Active Directory-Domänenkonto ist und der Domänenname richtig geschrieben ist, sollten Sie sicherstellen, dass in der Domäne ein Domänencontroller verfügbar ist. Dies kann ein häufiges Problem in einem virtuellen Azure-Netzwerk sein, das Domänencontroller enthält und in dem ein Domänencontrollercomputer nicht gestartet wurde. Um dieses Problem zu umgehen, können Sie anstelle eines Domänenkontos ein lokales Administratorkonto verwenden.

<a id="wincred"></a>
### Fehler bei Windows-Sicherheit: Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden.

Ursache: Der virtuelle Zielcomputer konnte Ihren Kontonamen und das Kennwort nicht überprüfen.

Ein Windows-basierter Computer kann die Anmeldeinformationen eines lokalen Kontos oder eines Domänenkontos überprüfen.

- Verwenden Sie für lokale Konten die Syntax *Computername*\*Benutzername* (Beispiel: "SQL1\\Admin4798").
- Verwenden Sie für Domänenkonten die Syntax *Domänenname*\*Benutzername* (Beispiel: "CONTOSO\\johndoe").

Wenn Sie den virtuellen Computer auf einem Domänencontroller in einer neuen Active Directory-Gesamtstruktur heraufgestuft haben, wird auch das lokale Administratorkonto, mit dem Sie sich angemeldet haben, in ein äquivalentes Konto mit dem gleichen Kennwort in der neuen Gesamtstruktur und Domäne konvertiert. Das lokale Administratorkonto wird gelöscht. Wenn Sie sich beispielsweise mit dem lokalen Administratorkonto DC1\\DCAdmin angemeldet und den virtuellen Computer als Domänencontroller in einer neuen Gesamtstruktur für die Domäne "corp.contoso.com" heraufgestuft haben, dann wird das lokale Konto DC1\\DCAdmin gelöscht und ein neues Domänenkonto (CORP\\DCAdmin) mit demselben Kennwort erstellt.

Stellen Sie sicher, dass der Kontoname ein Name ist, der vom virtuellen Computer als gültiges Konto bestätigt werden kann, und dass das Kennwort korrekt ist.

Wenn Sie das Kennwort für das lokale Administratorkonto ändern müssen, finden Sie hierzu weitere Informationen unter [Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Computer in Windows](virtual-machines-windows-reset-password.md).

<a id="rdpconnect"></a>
### Fehler bei Remotedesktopverbindung: Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen.

Ursache: Das Konto zum Herstellen der Verbindung verfügt nicht über Remotedesktop-Anmelderechte.

Jeder Windows-Computer hat eine lokale Gruppe von Remotedesktopbenutzern, in der die Konten und Gruppen enthalten sind, die sich per Remoteverbindung anmelden können. Auch Mitglieder der lokalen Gruppe „Administratoren“ haben Zugriff, obwohl diese Konten nicht in der lokalen Gruppe „Remotedesktopbenutzer“ aufgelistet werden. Bei Computern, die Mitglieder einer Domäne sind, enthält die lokalen Gruppe "Administratoren" auch die Domänenadministratoren für die Domäne.

Stellen Sie sicher, dass das Konto, das Sie zum Herstellen der Verbindung verwenden, über Remotedesktop-Anmelderechte verfügt. Verwenden Sie als Problemumgehung ein Domänenkonto oder lokales Administratorkonto, um per Remotedesktop eine Verbindung herzustellen. Verwenden Sie anschließend das Snap-In "Computerverwaltung" (**Systemprogramme > Lokale Benutzer und Gruppen > Gruppen > Remotedesktopbenutzer**), um das gewünschte Konto der lokalen Gruppe mit den Remotedesktopbenutzern hinzuzufügen.


## Detaillierte Problembehandlung

Wenn keiner dieser Fehler auftritt und Sie trotzdem keine Verbindung mit dem virtuellen Computer per Remotedesktop herstellen können, können Sie [diesen Artikel](virtual-machines-rdp-detailed-troubleshoot.md) lesen, um nach anderen Ursachen zu forschen.


## Zusätzliche Ressourcen

[Azure IaaS (Windows) und Diagnoseprogramme](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer](virtual-machines-windows-reset-password.md)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

[Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-ssh-connections.md)

[Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Sept15_HO4-->