<properties
	pageTitle="Detaillierte Problembehandlung für Remotedesktopverbindungen | Microsoft Azure"
	description="Detaillierte Schritte zur Problembehandlung für RDP-Verbindungen mit einem virtuellen Azure-Computer unter Windows."
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
	ms.date="01/08/2016"
	ms.author="dkshir"/>

# Detaillierte Problembehandlung für Remotedesktopverbindungen mit Windows-basierten virtuellen Azure-Computern

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Dieser Artikel enthält ausführliche schrittweise Anleitungen zur Diagnose und Behebung komplexer Remotedesktop-Fehler für virtuelle windowsbasierte Azure-Computer.

> [AZURE.IMPORTANT]Um allgemeine Remotedesktopfehler als Ursache auszuschließen, lesen Sie zunächst den Artikel [grundlegende Problembehandlung für Remotedesktop](virtual-machines-troubleshoot-remote-desktop-connections.md), bevor Sie mit dieser Problembehandlung fortfahren.

Wenn Sie eine Fehlermeldung erhalten, die nicht den spezifischen Fehlermeldungen ähnelt, die im Artikel [grundlegende Problembehandlung für Remotedesktop](virtual-machines-troubleshoot-remote-desktop-connections.md) beschrieben sind, können Sie diese Schritte durchführen, um herauszufinden, warum der Remotedesktop (oder [RDP](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol)) Client keine Verbindung mit dem RDP-Dienst auf der Azure-VM herstellen kann.

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).


## Komponenten einer Remotedesktopverbindung

Eine Remotedesktopverbindung enthält folgende Komponenten:

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_0.png)

Bevor Sie fortfahren, kann es hilfreich sein zu überlegen, was sich seit der letzten erfolgreichen Remotedesktopverbindung mit dem virtuellen Computer geändert hat. Beispiel:

- Wenn sich die öffentliche IP-Adresse des virtuellen Computers oder des Clouddienst mit dem virtuellen Computer (auch virtuelle IP-Adresse „[VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)“ genannt) geändert hat, tritt möglicherweise deshalb ein RDP-Fehler auf, weil im DNS-Clientcache immer noch die *alte IP-Adresse* für den DNS-Namen gespeichert ist. Löschen Sie den Cache des DNS-Clients, und versuchen Sie erneut eine Verbindung zum virtuellen Computer herzustellen. Oder versuchen Sie es direkt mit der neuen VIP.
- Wenn Sie für die Verwaltung Ihrer Remotedesktopverbindungen statt der Azure-Portale eine Anwendung eines Drittanbieters verwenden, müssen Sie sicherstellen, dass in der Anwendungskonfiguration der richtige TCP-Port für den Datenverkehr des Remotedesktops angegeben ist. Bei klassischen virtuelle Computern können Sie dies im [Azure-Portal](portal.azure.com) überprüfen, indem Sie die Einstellungen für diese VM aufrufen und auf „Endpunkt“ klicken.


## Vorbereitende Schritte

Führen Sie die folgenden Schritte aus, bevor Sie mit der detaillierten Problembehandlung fortfahren:

- Überprüfen Sie im klassischen Azure-Portal oder im Azure-Portal, ob offensichtliche Probleme mit dem virtuellen Computer bestehen.
- Führen Sie die Schritte durch, die im Artikel [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md#quickfixrdp) aufgeführt sind.


Versuchen Sie anschließend erneut eine Remotedesktopverbindung zum virtuellen Computer herzustellen.


## Detaillierte Problembehandlung

Wenn die Verbindung zwischen dem Remotedesktopclient und dem Remotedesktopdienst auf dem virtuellen Azure-Computer fehlschlägt, kann dies auf Fehler an folgenden Stellen zurückzuführen sein:

- Remotedesktop-Clientcomputer
- Edgegerät im Intranet des Unternehmens
- Clouddienst-Endpunkt und die Zugriffssteuerungsliste (ACL)
- Netzwerksicherheitsgruppen
- Windows-basierter virtueller Azure-Computer

### Quelle 1: Remotedesktop-Clientcomputer

Stellen Sie sicher, dass Ihr Computer Remotedesktopverbindungen mit einem anderen lokalen windowsbasierten Computer herstellen kann.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_1.png)

Falls dies nicht möglich ist, suchen Sie auf Ihrem Computer Folgendes:

- Eine lokale Firewall-Einstellung, die den Remotedesktop-Datenverkehr blockiert
- Lokal installierte Proxy-Clientsoftware, die Remotedesktopverbindungen verhindert
- Lokal installierte Netzwerküberwachungssoftware, die Remotedesktopverbindungen verhindert
- Andere Arten von Sicherheitssoftware, die Datenverkehr überwachen oder bestimmte Arten von Datenverkehr zulassen/unterbinden, die Remotedesktopverbindungen verhindern

In allen diesen Fällen deaktivieren Sie zur Ermittlung der Ursache die Software vorübergehend, und versuchen Sie dann, eine Remotedesktopverbindung mit einem lokalen Computer herzustellen. Wenn Sie damit die Ursache gefunden haben, korrigieren Sie zusammen mit Ihrem Netzwerkadministrator die Einstellungen der Software, um Remotedesktopverbindungen zu ermöglichen.

### Quelle 2: Edgegerät im Intranet des Unternehmens

Stellen Sie sicher, dass ein direkt mit dem Internet verbundener Computer Remotedesktopverbindungen mit Ihrem virtuellen Azure-Computer herstellen kann.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_2.png)

Wenn Sie keinen Computer haben, der direkt mit dem Internet verbunden ist, können Sie einen neuen virtuellen Azure-Computer in einer Ressourcengruppe oder einem Clouddienst erstellen und mit diesem testen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Windows in Azure](virtual-machines-windows-tutorial.md). Sie können den virtuellen Computer und die Ressourcengruppe bzw. den Clouddienst nach Abschluss des Tests löschen.

Wenn Sie eine Remotedesktopverbindung mit einem direkt mit dem Internet verbundenen Computer herstellen können, überprüfen Sie das Edgegerät für das Intranet Ihrer Organisation auf Folgendes:

- Eine interne Firewall, die HTTPS-Verbindungen mit dem Internet blockiert
- Ein Proxy-Server, der Remotedesktopverbindungen verhindert
- Software zur Erkennung von Eindringversuchen oder zur Netzwerküberwachung, die auf dem Edgegerät ausgeführt und die Remotedesktopverbindungen verhindert

Korrigieren Sie zusammen mit Ihrem Netzwerkadministrator die Einstellungen des Edgegeräts für das Intranet Ihrer Organisation, um HTTPS-basierte Remotedesktopverbindungen mit dem Internet zu ermöglichen.

### Quelle 3: Clouddienst-Endpunkt und ACL

Überprüfen Sie für mit dem klassischen Bereitstellungsmodell erstellte virtuelle Computer, ob ein anderer virtueller Azure-Computer in demselben Clouddienst oder virtuellen Netzwerk Remotedesktopverbindungen mit Ihrem virtuellen Azure-Computer herstellen kann.

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_3.png)

> [AZURE.NOTE]Für virtuelle Computer, die im Ressourcen-Manager erstellt wurden, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsgs) fort.

Wenn Sie nicht über einen anderen virtuellen Computer im gleichen Clouddienst oder virtuellen Netzwerk verfügen, können Sie anhand der Schritte in [Erstellen eines virtuellen Computers unter Windows in Azure](virtual-machines-windows-tutorial.md)einen neuen virtuellen Computer erstellen. Löschen Sie den zusätzlichen virtuellen Computer, nach Abschluss des Tests.

Wenn Sie mit einem virtuellen Computer in demselben Clouddienst oder virtuellen Netzwerk eine Remotedesktopverbindung herstellen können, überprüfen Sie Folgendes:

- Die Endpunktkonfiguration für den Remotedesktop-Datenverkehr auf der Ziel-VM: der private TCP-Port des Endpunkts muss mit dem TCP-Port übereinstimmen, den der Remotedesktop-Dienst der VM überwacht (standardmäßig 3389).
- Die ACL für den Endpunkt des Remotedesktop-Datenverkehrs auf der Ziel-VM: In ACLs können Sie anhand der Quell-IP-Adresse angeben, ob eingehender Datenverkehr aus dem Internet zugelassen oder verweigert wird. Falsch konfigurierte ACLs können verhindern, dass die Remotedesktop-Datenverkehr zum Endpunkt gelangt. Überprüfen Sie die ACLs, um sicherzustellen, dass eingehender Verkehr von den öffentlichen IP-Adressen des Proxy- oder eines anderen Edgeservers zugelassen wird. Weitere Informationen finden Sie unter [Was ist eine Netzwerk-Zugriffssteuerungsliste (ACL)?](../virtual-network/virtual-networks-acl.md).

Um den Endpunkt als Problemquelle auszuschließen, entfernen Sie den aktuellen Endpunkt und erstellen Sie einen neuen Endpunkt. Wählen Sie einen zufälligen Port im Bereich von 49152 bis 65535 für die externe Portnummer aus. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-set-up-endpoints.md).

### <a id="nsgs"></a>Quelle 4: Netzwerksicherheitsgruppen

Netzwerksicherheitsgruppen ermöglichen Ihnen eine präzisere Steuerung des zulässigen eingehenden und ausgehenden Datenverkehrs. Sie können Regeln erstellen, die mehrere Subnetze und Clouddienste in einem virtuellen Azure-Netzwerk umfassen. Überprüfen Sie die Regeln der Netzwerk-Sicherheitsgruppe, um sicherzustellen, dass Remotedesktop-Datenverkehr aus dem Internet zulässig ist.

Weitere Informationen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe (NSG)?](../virtual-network/virtual-networks-nsg.md).

### Quelle 5: Windows-basierter virtueller Azure-Computer

![](./media/virtual-machines-rdp-detailed-troubleshoot/tshootrdp_5.png)

Verwenden Sie das [Azure IaaS-Diagnosepaket (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) um zu überprüfen, ob der virtuelle Azure-Computer selbst die Fehlerquelle ist. Wenn das Problem **RDP-Verbindung mit einem virtuellen Azure-Computer (Neustart erforderlich)** mit diesem Diagnosepaket nicht behoben werden kann, führen Sie die Schritte in [diesem Artikel](virtual-machines-windows-reset-password.md) aus, um den Remotedesktopdienst auf dem virtuellen Computer zurückzusetzen. Dies bewirkt Folgendes:

- Die Windows-Firewall-Standardregel "Remotedesktop" (TCP-Port 3389) wird aktiviert.
- Remotedesktopverbindungen werden aktiviert, indem der HKLM\\System\\CurrentControlSet\\Control\\Terminal Server\\fDenyTSConnections-Registrierungswert auf 0 festgelegt wird.

Versuchen Sie erneut, die Verbindung von Ihrem Computer aus herzustellen. Wenn Sie noch immer keine Remotedesktopverbindung herstellen können, überprüfen Sie folgende Fehlerquellen:

- Der Remotedesktopdienst wird auf dem virtuellen Zielcomputer nicht ausgeführt.
- Der Remotedesktopdienst überwacht nicht den TCP-Port 3389.
- Windows-Firewall oder eine andere lokale Firewall enthält eine Regel für ausgehenden Datenverkehr, durch die Remotedesktop-Datenverkehr verhindert wird.
- Software zur Erkennung von Eindringversuchen oder zur Netzwerküberwachung, die auf dem virtuellen Azure-Computer ausgeführt wird, verhindert Remotedesktopverbindungen.

Bei virtuellen Computern, die mit dem klassischen Bereitstellungsmodell erstellt wurden, können Sie eine Azure PowerShell-Remotesitzung auf dem virtuellen Azure-Computer nutzen. Zunächst müssen Sie ein Zertifikat für den Clouddienst installieren, der als Host des virtuellen Computers fungiert. Wechseln Sie zu [Konfigurieren eines sicheren PowerShell-Remotezugriffs auf virtuellen Azure-Computern](http://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe), und laden Sie die Skriptdatei „**InstallWinRMCertAzureVM.ps1**“ auf Ihren lokalen Computer herunter.

Installieren Sie dann Azure PowerShell, sofern noch nicht geschehen. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).

Als Nächstes öffnen Sie eine Azure PowerShell-Eingabeaufforderung. Ändern Sie dann den aktuellen Ordner in den Speicherort der Skriptdatei **InstallWinRMCertAzureVM.ps1**. Um ein Azure PowerShell-Skript auszuführen, müssen Sie die richtige Ausführungsrichtlinie festlegen. Führen Sie den Befehl **Get-ExecutionPolicy** aus, um die aktuelle Richtlinienebene zu ermitteln. Weitere Informationen zum Festlegen der geeigneten Ebene finden Sie unter [Set-ExecutionPolicy](https://technet.microsoft.com/library/hh849812.aspx).

Als Nächstes tragen Sie den Namen Ihres Azure-Abonnements, den Namen des Clouddiensts und den Namen des virtuellen Computers ein (entfernen Sie die < and >-Zeichen). Führen Sie dann diese Befehle aus.

	$subscr="<Name of your Azure subscription>"
	$serviceName="<Name of the cloud service that contains the target virtual machine>"
	$vmName="<Name of the target virtual machine>"
	.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName

Den richtigen Abonnementnamen können Sie der Eigenschaft _SubscriptionName_ in der Ausgabe des Befehls **Get-AzureSubscription** entnehmen. Der Name des virtuellen Computers und der Name des Clouddiensts werden in der Spalte _ServiceName_ in der Ausgabe des Befehls **Get-AzureVM** angezeigt.

Um sich davon zu überzeugen, dass Sie das neue Zertifikat besitzen, öffnen Sie ein Zertifikate-Snap-In für den aktuellen Benutzer, und zeigen Sie den Ordner „**Vertrauenswürdige Stammzertifizierungsstellen\\Zertifikate**“ an. Es sollte ein Zertifikat mit dem DNS-Namen des Clouddiensts in der Spalte "Ausgestellt für" angezeigt werden (Beispiel: "cloudservice4testing.cloudapp.net").

Starten Sie als Nächstes mit diesen Befehlen eine Azure PowerShell-Remotesitzung.

	$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
	$creds = Get-Credential
	Enter-PSSession -ConnectionUri $uri -Credential $creds

Nachdem Sie gültige Administratoranmeldeinformationen eingegeben haben, sollte Ihre Azure PowerShell-Eingabeaufforderung etwa wie folgt angezeigt werden:

	[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>

Den ersten Teil dieser Eingabeaufforderung stellt der Name Ihres Clouddiensts dar, der die Ziel-VM enthält, z. B. „cloudservice4testing.cloudapp.net“. Sie können jetzt Azure PowerShell-Befehle für diesen Clouddienst eingeben, um die oben genannten weiteren Probleme zu untersuchen und Korrekturen an der Konfiguration vorzunehmen.

### So korrigieren Sie manuell den TCP-Port, der von den Remotedesktopdiensten überwacht wird

Wenn Sie [Azure IaaS (Windows) und Diagnoseprogramme](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864) für das Problem **RDP-Verbindung mit einer Azure-VM (Neustart erforderlich)** nicht ausführen konnten, führen Sie diesen Befehl an der Eingabeaufforderung der Azure PowerShell-Sitzung aus.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Die PortNumber-Eigenschaft enthält die aktuelle Portnummer. Ändern Sie bei Bedarf die Remotedesktop-Portnummer mit dem folgenden Befehl wieder in den Standardwert (3389).

	Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389

Stellen Sie sicher, dass die Portnummer mit diesem Befehl in 3389 geändert wurde.

	Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"

Beenden Sie die Azure PowerShell-Remotesitzung mit dem folgenden Befehl.

	Exit-PSSession

Überprüfen Sie, ob der Remotedesktop-Endpunkt für den virtuellen Azure-Computer auch den TCP-Port 3398 als internen Port verwendet. Starten Sie den virtuellen Azure-Computer neu und versuchen Sie erneut, die Remotedesktopverbindung herzustellen.


## Zusätzliche Ressourcen

[Azure IaaS (Windows) und Diagnoseprogramme](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)

[Zurücksetzen eines Kennworts oder des Remotedesktopdiensts für virtuelle Windows-Computer](virtual-machines-windows-reset-password.md)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

[Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-ssh-connections.md)

[Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0114_2016-->