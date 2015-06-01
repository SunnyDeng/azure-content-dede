<properties 
	pageTitle="Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer" 
	description="Erfahren Sie, wie Sie die RDP-Konnektivität (Remote Desktop) von virtuellen Azure-Computern mit Diagnoseprogrammen und Schritten zur Isolierung der Problemursache wiederherstellen können."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/27/2015" 
	ms.author="josephd"/>

# Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer

In diesem Thema wird ein methodischer Ansatz für die Korrektur und Bestimmung der Hauptursache von Remotedesktopverbindungen mit Windows-basierten virtuellen Azure-Computern beschrieben.

## Schritt 1: Ausführen des Azure IaaS-Diagnosepakets

Zur Behebung von Problemen, die beim Herstellen von Remotedesktopverbindungen häufig auftreten, hat Microsoft ein [Diagnosepaket für Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) erstellt.

1.	Klicken Sie auf dieser Seite auf **Diagnosepaket Microsoft Azure IaaS (Windows)**, um eine neue Diagnosesitzung zu erstellen.
2.	Wählen Sie auf der Seite **Welches der folgenden Probleme tritt mit Ihrem virtuellen Azure-Computer auf?** das Problem **RDP-Verbindung mit einem virtuellen Azure-Computer (Neustart erforderlich)**. 

Weitere Informationen finden Sie in der Knowledge Base im Artikel [Microsoft Azure IaaS (Windows) und Diagnoseprogramme](http://support.microsoft.com/kb/2976864).

Wenn sich Problem nicht durch Ausführen des Azure IaaS-Diagnosepakets beheben ließ oder wenn Sie das Diagnosepaket nicht ausführen konnten, kann eine ausführlichere Problembehandlung erforderlich sein, die in den folgenden Schritten beschrieben wird.

> [AZURE.NOTE] Azure IaaS (Windows)-Diagnosepaket muss auf einem Computer mit Windows 8, Windows 8.1, Windows Server 2012 oder Windows Server 2012 R2 ausgeführt werden.

## Schritt 2: Ermitteln der Fehlermeldung des Remotedesktopclients

Orientieren Sie sich bei der Verwendung dieser Abschnitte anhand der Fehlermeldung, die Sie erhalten.

### Meldungsfenster der Remotedesktopverbindung: Die Verbindung mit der Remotesitzung wurde getrennt, da keine Lizenzserver für Remotedesktop vorhanden sind.

Ursache: Die 120-Tage-Kulanzfrist für die Rolle "Remotedesktopserver" ist abgelaufen, und Sie müssen Lizenzen installieren.
 
Als vorübergehende Problemumgehung speichern Sie eine lokale Kopie der RDP-Datei aus dem Azure-Verwaltungsportal, und führen Sie diesen Befehl an einer Windows PowerShell-Eingabeaufforderung aus, um eine Verbindung herzustellen.

	mstsc <File name>.RDP /admin

Damit wird die Lizenz nur für diese Verbindung deaktiviert.

Wenn Sie eigentlich nicht mehr als zwei gleichzeitige Remotedesktopverbindungen mit dem virtuellen Computer benötigen, können Sie mit Server-Manager die Rolle „Remotedesktopserver“ entfernen.

Lesen Sie auch den Blogbeitrag [Azure-VM schlägt fehl mit "Keine Lizenzserver für Remotedesktop verfügbar"](http://blogs.msdn.com/b/wats/archive/2014/01/21/rdp-to-azure-vm-fails-with-quot-no-remote-desktop-license-servers-available-quot.aspx) .

### Meldungsfenster der Remotedesktopverbindung: Der Computer "Name" kann von Remote Desktop nicht gefunden werden.

Ursache: Der Remotedesktopclient auf Ihrem Computer kann den Namen des Computers, der in den Einstellungen der RDP-Datei angegeben ist, nicht auflösen.

Es folgt eine RDP-Beispieldatei, die vom Azure-Verwaltungsportal generiert wird:

	full address:s:tailspin-azdatatier.cloudapp.net:55919
	prompt for credentials:i:1

Der Adressteil besteht aus dem vollqualifizierten Domänennamen des Cloud-Diensts, der den virtuellen Computer enthält, (in diesem Beispiel tailspin-azdatatier.cloudapp.net) und dem externen TCP-Port des Endpunkts für den Remotedesktop-Datenverkehr (55919).

Mögliche Lösungen für dieses Problem:

- Wenn Sie im Intranet einer Organisation sind, stellen Sie sicher, dass Ihr Computer Zugriff auf den Proxyserver hat und ihm HTTPS-Datenverkehr zusenden kann.
- Wenn Sie eine lokal gespeicherte RDP-Datei verwenden, versuchen Sie, die vom Azure-Verwaltungsportal generierte Datei zu verwenden, um sicherzustellen, dass der richtige Name für den Cloud-Dienst und der richtige Endpunktport des virtuellen Computers angegeben werden.

### Meldungsfenster von Windows-Sicherheit: Mit den Anmeldeinformationen konnte keine Verbindung hergestellt werden

Ursache: Der Kontoname und das Kennwort, die Sie übermittelt haben, können von dem virtuellen Computer, mit dem Sie eine Verbindung herstellen, nicht überprüft werden.

Ein Windows-basierter Computer kann die Anmeldeinformationen eines lokalen Kontos oder eines domänenbasierten Kontos überprüfen.

- Verwenden Sie für lokale Konten die Syntax <computer name><Kontoname> (Beispiel: SQL1\\Admin4798). 
- Verwenden Sie für Domänenkonten die Syntax <domain name><Kontoname> (Beispiel: CONTOSO\\maxmuster).

Bei Computern, die Sie in einer neuen Active Directory-Gesamtstruktur zu Domänencontrollern heraufstufen, wird das lokale Administratorkonto, mit dem Sie beim Heraufstufen angemeldet sind, in der neuen Gesamtstruktur und Domäne in ein entsprechendes Konto mit demselben Kennwort konvertiert. Das vorherige lokale Administratorkonto wird gelöscht. Wenn Sie sich beispielsweise mit dem lokalen Administratorkonto DC1\\DCAdmin angemeldet und den virtuellen Computer als Domänencontroller in einer neuen Gesamtstruktur für die Domäne "corp.contoso.com" heraufgestuft haben, dann wird das lokale Konto DC1\\DCAdmin gelöscht und ein neues Domänenkonto CORP\\DCAdmin mit demselben Kennwort erstellt.

Überprüfen Sie den Kontonamen sorgfältig, um sicherzustellen, dass es sich um einen Namen handelt, den der virtuelle Computer als gültiges Konto überprüfen kann. Überprüfen Sie das Kennwort erneut, um sicherzustellen, dass es das richtige Kennwort ist.

Wenn Sie das Kennwort für das lokale Administratorkonto ändern müssen, finden Sie hierzu weitere Informationen unter [Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Computer in Windows](virtual-machines-windows-reset-password.md).

### Meldungsfenster der Remotedesktopverbindung: Dieser Computer kann keine Verbindung mit dem Remotecomputer herstellen.

Ursache: Das Konto, mit dem Sie eine Verbindung herstellen, verfügt über keine Remotedesktop-Anmelderechte.

Jeder Windows-Computer verfügt über die lokale Gruppe "Remotedesktopbenutzer", die Konten und Gruppen enthält, die dazu berechtigt sind, sich über eine Remotedesktopverbindung anzumelden. Auch Mitglieder der lokalen Gruppe Administratoren haben Zugriff, obwohl diese Konten nicht als Mitglieder der lokalen Gruppe "Remotedesktopbenutzer" aufgelistet werden. Bei Computern, die Mitglieder einer Domäne sind, enthält die lokalen Gruppe "Administratoren" auch die Domänenadministratoren für die Domäne.

Stellen Sie sicher, dass das Konto, das Sie zum Herstellen der Verbindung verwenden, über Remotedesktop-Anmelderechte verfügt. Verwenden Sie ein Domänenadministrator- oder ein lokales Administratorkonto als vorübergehende Problemumgehung, um eine Remotedesktopverbindung herzustellen, und fügen Sie das gewünschte Konto mit dem Snap-In "Computerverwaltung" der lokalen Gruppe "Remotedesktopbenutzer" hinzu (**Systemprogramme > Lokale Benutzer und Gruppen > Gruppen > Remotedesktopbenutzer**).

### Meldungsfenster der Remotedesktopverbindung: Remotedesktop kann aus einem der folgenden Gründe keine Verbindung mit dem Remotecomputer herstellen...

Ursache: Der Remotedesktopclient kann den Dienst der Remotedesktopdienste auf dem virtuellen Computer nicht erreichen. Dieses Problem kann viele Ursachen haben.

Nachfolgend werden die beteiligten Komponenten aufgeführt.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_0.png)
 
Bevor Sie mit einer detaillierte Problembehandlung beginnen, empfiehlt es sich, sich zu überlegen, was sich geändert hat, seit Sie zuletzt erfolgreich Remotedesktopverbindungen herstellen konnten, und diese Änderung als Ausgangspunkt für die Problembehebung zu verwenden. Zum Beispiel:

- Wenn Sie Remotedesktopverbindungen herstellen konnten und die öffentliche IP-Adresse des Cloud-Diensts mit dem virtuellen Computer (auch virtuelle IP-Adresse [VIP] genannt) geändert haben, enthält der DNS-Clientcache möglicherweise einen Eintrag für den DNS-Namen des Cloud-Diensts und die *alte IP-Adresse*. Löschen Sie den Cache des DNS-Clients, und versuchen Sie es erneut. Versuchen Sie stattdessen, die Verbindung mit der neuen VIP herzustellen.
- Wenn Sie statt des Azure-Verwaltungsportals oder des Azure-Vorschauportals eine Anwendung zum Verwalten von Remotedesktopverbindungen verwenden, stellen Sie sicher, dass die Anwendungskonfiguration den nach dem Zufallsprinzip bestimmten TCP-Port für den Remotedesktop-Datenverkehr enthält. 

In den folgenden Abschnitten wird schrittweise erläutert, wie die verschiedenen Ursachen für dieses Problem isoliert und ermittelt werden. Zudem werden Lösungen und Problemumgehungen bereitgestellt.

## Schritt 3: Vorbereitende Schritte vor der detaillierten Problembehandlung

Gehen Sie wie folgt vor:

- Überprüfen Sie den Status des virtuellen Computers im Azure-Verwaltungsportal oder dem Azure-Vorschauportal.
- [Starten Sie den virtuellen Computer neu.](https://msdn.microsoft.com/library/azure/dn763934.aspx)
- [Ändern Sie die Größe des virtuellen Computers.](https://msdn.microsoft.com/library/dn168976.aspx)

Versuchen Sie nach diesen Schritten erneut, die Remotedesktopverbindung herzustellen.

## Schritt 4: Detaillierte Problembehandlung 

Dass der Remotedesktopclient den Dienst der Remotedesktopdienste auf dem virtuellen Azure-Computer nicht erreichen kann, ist u. U. auf die folgenden Problem- oder Konfigurationsfehlerquellen zurückzuführen:

- Remotedesktop-Clientcomputer
- Edgegerät im Intranet des Unternehmens
- Cloud-Dienstendpunkt und die Zugriffssteuerungsliste (ACL)
- Netzwerksicherheitsgruppen
- Windows-basierter virtueller Azure-Computer

### Quelle 1: Remotedesktop-Clientcomputer

Um Ihren Computer als Problemquelle oder dessen Fehlkonfiguration auszuschließen, überprüfen Sie, ob Ihr Computer Remotedesktopverbindungen mit einem anderen lokalen Windows-basierten Computer herstellen kann.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_1.png)
 
Falls dies nicht möglich ist, suchen Sie auf Ihrem Computer Folgendes:

- Eine lokale Firewall-Einstellung, die den Remotedesktop-Datenverkehr blockiert
- Lokal installierte Proxy-Clientsoftware, die Remotedesktopverbindungen verhindert
- Lokal installierte Netzwerküberwachungssoftware, die Remotedesktopverbindungen verhindert
- Andere Arten von Sicherheitssoftware, die Datenverkehr überwachen oder bestimmte Arten von Datenverkehr zulassen/unterbinden, die Remotedesktopverbindungen verhindern

Versuchen Sie in allen diesen Fällen, die Software vorübergehend zu deaktivieren, und versuchen Sie, eine Remotedesktopverbindung mit einem lokalen Computer herzustellen, um die Hauptursache zu ermitteln. Anschließend korrigieren Sie zusammen mit Ihren Netzwerkadministrator die Einstellungen der Software, um Remotedesktopverbindungen zu ermöglichen.

### Quelle 2: Edgegerät im Intranet des Unternehmens

Um das Edgegerät im Intranet des Unternehmens als Problemquelle oder dessen Fehlkonfiguration auszuschließen, überprüfen Sie, ob ein direkt mit dem Internet verbundener Computer Remotedesktopverbindungen mit Ihrem virtuellen Azure-Computer herstellen kann.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_2.png)
 
Wenn Sie keinen Computer haben, der direkt mit dem Internet verbunden ist, können Sie problemlos einen neuen virtuellen Azure-Computer in einem eigenen Cloud-Dienst erstellen und verwenden. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Windows in Azure](virtual-machines-windows-tutorial.md). Löschen Sie den virtuellen Computer und den Cloud-Dienst, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine Remotedesktopverbindung mit einem direkt mit dem Internet verbundenen Computer herstellen können, überprüfen Sie das Edgegerät für das Intranet Ihrer Organisation auf Folgendes:

- Eine interne Firewall, die HTTPS-Verbindungen mit dem Internet blockiert
- Der Proxy-Server, der Remotedesktopverbindungen verhindert
- Software zur Erkennung von Eindringversuchen oder zur Netzwerküberwachung, die auf dem Edgegerät ausgeführt und die Remotedesktopverbindungen verhindert

Korrigieren Sie zusammen mit Ihrem Netzwerkadministrator die Einstellungen des Edgegeräts für das Intranet Ihrer Organisation, um HTTPS-basierte Remotedesktopverbindungen mit dem Internet zu ermöglichen.

### Quelle 3: Cloud-Dienstendpunkt und ACL

Um Cloud-Dienstendpunkt und ACL als Problemquelle oder deren Fehlkonfiguration auszuschließen, überprüfen Sie, ob ein anderer virtueller Azure-Computer im selben Cloud-Dienst Remotedesktopverbindungen mit Ihrem virtuellen Azure-Computer iherstellen kann.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_3.png)
 
Wenn im gleichen Cloud-Dienst kein anderer virtueller Computer vorhanden ist, können Sie problemlos einen neuen virtuellen Azure-Computer erstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Windows in Azure](virtual-machines-windows-tutorial.md). Löschen Sie den zusätzlichen virtuellen Computer, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie mit einem virtuellen Computer im gleichen Cloud-Dienst eine Remotedesktopverbindung herstellen können, überprüfen Sie Folgendes:

- Die Endpunktkonfiguration für den Remotedesktop-Datenverkehr auf dem virtuellen Zielcomputer. Der private TCP-Port des Endpunkts muss mit dem TCP-Port übereinstimmen, der vom Dienst der Remotedesktopdienste auf dem virtuellen Computer überwacht wird. In der Standardeinstellung ist dies Port 3389. 
- Die ACL für den Endpunkt des Remotedesktop-Datenverkehr auf dem virtuellen Zielcomputer. In ACLs können Sie anhand der Quell-IP-Adresse angeben, ob eingehender Datenverkehr aus dem Internet zugelassen oder verweigert wird. Falsch konfigurierte ACSs können verhindern, dass die Remotedesktop-Datenverkehr zum Endpunkt gelangt. Untersuchen Sie die ACLs, um sicherzustellen, dass eingehender Verkehr von den öffentlichen IP-Adressen des Proxy- oder anderen Edge-Servers zugelassen wird. Weitere Informationen finden Sie unter [Netzwerk-Zugriffssteuerungslisten](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Um den Endpunkt als Problemquelle auszuschließen, entfernen Sie den aktuellen Endpunkt, und erstellen Sie einen neuen Endpunkt. Wählen Sie einen zufälligen Port im Bereich von 49152 bis 65535 für die externe Portnummer aus. Weitere Informationen finden Sie unter [Festlegen von Endpunkten auf einem virtuellen Computer in Azure](virtual-machines-set-up-endpoints.md).

### Quelle 4: Netzwerksicherheitsgruppen

Netzwerk-Sicherheitsgruppen ermöglichen Ihnen eine präzisere Steuerung des zulässigen eingehenden und ausgehenden Datenverkehrs. Sie können Regeln erstellen, die mehrere Subnetze und Cloud-Dienste in einem virtuellen Azure-Netzwerk umfassen. Überprüfen Sie die Regeln der Netzwerk-Sicherheitsgruppe, um sicherzustellen, dass Remotedesktop-Datenverkehr aus dem Internet zulässig ist.

Weitere Informationen finden Sie unter zu [Netzwerk-Sicherheitsgruppen](https://msdn.microsoft.com/library/azure/dn848316.aspx).

### Quelle 5: Windows-basierter virtueller Azure-Computer

Der letzte Satz möglicher Probleme hat mit dem virtuellen Azure-Computer selbst zu tun.

![](./media/virtual-machines-troubleshoot-remote-desktop-connections/tshootrdp_5.png)
 
Wenn Sie das [Diagnosepaket Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) für das Problem **RDP-Verbindung mit einer Azure-VM (Neustart erforderlich)** ausführen konnten, folgen Sie den Anweisungen unter [Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer](virtual-machines-windows-reset-password.md), um den Dienst der Remotedesktopdienste auf dem virtuellen Computer zurückzusetzen. Dies bewirkt Folgendes:

- Die Windows-Firewall-Standardregel "Remotedesktop" (TCP-Port 3389) wird aktiviert.
- Remotedesktopverbindungen werden aktiviert, indem der HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections-Registrierungswert auf 0 festgelegt wird.

Versuchen Sie erneut, die Verbindung von Ihrem Computer aus herzustellen. Wenn Sie nicht erfolgreich sind, können u. a. die folgenden Probleme der Grund dafür sein:

- Der Dienst der Remotedesktopdienste wird auf dem virtuellen Zielcomputer nicht ausgeführt.
- Der Dienst der Remotedesktopdienste überwacht nicht den TCP-Port 3389.
- Die Windows-Firewall oder eine andere lokale Firewall enthält eine Regel für ausgehenden Datenverkehr, durch die Remotedesktop-Datenverkehr verhindert wird.
- Software zur Erkennung von Eindringversuchen oder zur Netzwerküberwachung, die auf dem virtuellen Azure-Computer ausgeführt wird, verhindert Remotedesktopverbindungen.

Um mögliche Probleme zu beheben, können Sie eine PowerShell-Remotesitzung mit dem virtuellen Azure-Computer starten. Zunächst müssen Sie ein Zertifikat für den Cloud-Dienst installieren, der als Host des virtuellen Computers fungiert. Wechseln Sie zu [Konfigurieren eines sicheren PowerShell-Remotezugriffs auf virtuellen Azure-Computern](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe), und laden Sie die Skriptdatei **InstallWinRMCertAzureVM.ps1** in einen Ordner auf Ihrem lokalen Computer herunter.

Installieren Sie dann Azure PowerShell, sofern noch nicht geschehen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md).

Als Nächstes öffnen Sie eine Azure-PowerShell-Eingabeaufforderung, und ändern Sie dann den aktuellen Ordner in den Speicherort der Skriptdatei **InstallWinRMCertAzureVM.ps1**. Um ein PowerShell-Skript auszuführen, müssen Sie die richtige Ausführungsrichtlinie festlegen. Führen Sie den Befehl **Get-ExecutionPolicy** aus, um die aktuelle Richtlinienebene zu ermitteln. Weitere Informationen zum Festlegen der geeigneten Ebene finden Sie unter [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Als Nächstes tragen Sie Ihren Azure-Abonnement-Namen, den Namen des Cloud-Diensts und den Name des virtuellen Computers ein (entfernen Sie die < and >-Zeichen), führen Sie dann diese Befehle aus.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Sie können den richtigen Abonnementnamen der SubscriptionName-Eigenschaft in der Ausgabe des Befehls **Get-AzureSubscription** entnehmen. Der Name des virtuellen Computers und der Name des Cloud-Diensts wird in der ServiceName-Spalte in der Ausgabe des Befehls **Get-AzureVM** angezeigt.

Um sich davon zu überzeugen, dass Sie das neue Zertifikat besitzen, öffnen Sie ein Zertifikate-Snap-In für den aktuellen Benutzer, und zeigen Sie den Ordner **Trusted Root Certification Authorities\\Certificates** an. Es sollte ein Zertifikat mit dem DNS-Namen des Cloud-Diensts in der Spalte "Ausgestellt für" angezeigt werden (Beispiel: cloudservice4testing.cloudapp.net).

Starten Sie als Nächstes mit diesen Befehlen eine Powershell-Remotesitzung.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Nachdem Sie gültige Administratoranmeldeinformationen eingegeben haben, sollte Ihre Azure-PowerShell-Eingabeaufforderung etwa wie folgt angezeigt werden:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

Der erste Teil der Eingabeaufforderung zeigt an, dass Sie jetzt PowerShell-Befehle für den Cloud-Dienst eingeben, der den virtuellen Zielcomputer enthält. Ihr Cloud-Dienstname wird etwas anders lauten als "cloudservice4testing.cloudapp.net".

Sie können jetzt PowerShell-Befehle eingeben, um die oben genannten weitere Probleme zu untersuchen und Korrekturen an der Konfiguration vorzunehmen.

### So korrigieren Sie manuell den TCP-Port, der vom Dienst der Remotedesktopdienste überwacht wird

Wenn Sie [Azure IaaS (Windows) und Diagnoseprogramme](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) für das Problem **RDP-Verbindung mit einer Azure-VM (Neustart erforderlich)** nicht ausführen konnten, führen Sie diesen Befehl an der Eingabeaufforderung der PowerShell-Sitzung aus.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Die PortNumber-Eigenschaft enthält die aktuelle Portnummer. Ändern Sie bei Bedarf die Remotedesktop-Portnummer mit dem folgenden Befehl wieder in den Standardwert (3389).

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Stellen Sie sicher, dass die Portnummer mit diesem Befehl in 3389 geändert wurde.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Beenden Sie die PowerShell-Remotesitzung mit dem folgenden Befehl.

	Exit-PSSession

Überprüfen Sie, ob der Remotedesktop-Endpunkt für den virtuellen Azure-Computer auch den TCP-Port 3398 als internen Port verwendet. Starten Sie den virtuellen Azure-Computer dann neu, und versuchen Sie erneut, die Remotedesktopverbindung herzustellen.

## Schritt 5: Übermitteln des Problems an die Azure-Support-Foren

Um Hilfe von Azure-Experten aus aller Welt zu erhalten, können Sie Ihr Problem an das MSDN Azure-Forum oder das Stack Overflow-Forum senden. Weitere Informationen finden Sie unter [Microsoft Azure-Foren](http://azure.microsoft.com/support/forums/).

## Schritt 6: Erstellen eines Azure-Supportfalls

Wenn Sie [Azure IaaS (Windows) und Diagnoseprogramme](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) für das Problem **RDP-Verbindung mit einer Azure-VM (Neustart erforderlich)** oder die Schritte 2 bis 5 in diesem Artikel ausgeführt und Ihr Problem an die Azure-Supportforen gesendet haben, aber weiterhin keine Remotedesktopverbindung  herstellen können, besteht eine zu berücksichtigende Alternative darin, ob Sie den virtuellen Computer neu erstellen können.

Wenn Sie den virtuellen Computer nicht neu erstellen können, ist es wahrscheinlich an der Zeit, einen Azure-Supportfall zu erstellen.

Zum Übermitteln eines Vorfalls wechseln Sie zur [Azure-Support-Website](http://azure.microsoft.com/support/options/), und klicken Sie auf **Support erhalten**.

Informationen zur Nutzung von Azure-Support finden Sie unter der [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).

## Zusätzliche Ressourcen

[Azure IaaS (Windows) und Diagnoseprogramme](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer](virtual-machines-windows-reset-password.md)

[Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Virtuelle Computer in Azure – häufig gestellte Fragen](http://msdn.microsoft.com/library/azure/dn683781.aspx)


<!--HONumber=54-->