<properties
	pageTitle="Behandeln von Problemen mit der SSH-Verbindung mit einem virtuellen Azure-Computer | Microsoft Azure"
	description="Beheben Sie SSH-Fehler wie etwa fehlgeschlagene oder abgelehnte SSH-Verbindungen auf virtuellen Azure-Computern unter Linux."
	keywords="SSH-Verbindung abgelehnt,SSH-Fehler,Azure SSH,SSH-Verbindungsfehler"
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="dkshir"/>

# Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer

Für SSH-Fehler beim Herstellen einer Verbindung mit einem Linux-basierten virtuellen Azure-Computer kann es verschiedene Ursachen geben. Dieser Artikel hilft Ihnen, diese Ursachen zu ermitteln und zu korrigieren.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Dieser Artikel gilt nur für virtuelle Azure-Computer, auf denen Linux ausgeführt wird. Bei virtuellen Azure-Computern mit Windows lesen Sie die Informationen unter [Problembehandlung bei der Remotedesktopverbindung mit einem virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md).

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).


## Allgemeine SSH-Fehler beheben.

Dieser Abschnitt enthält Schritte für die schnelle Behebung häufig auftretender Probleme mit der SSH-Verbindung.

### Virtuelle Computer, die mit dem klassischen Bereitstellungsmodell erstellt wurden

Führen Sie die folgenden Schritte aus, um die am häufigsten auftretenden SSH-Verbindungsfehler zu beheben:

1. _Remotezugriff zurücksetzen_ über das [Azure-Portal](https://portal.azure.com).<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Linux-Computer“ > **Remotezugriff zurücksetzen...**.

2. Starten Sie den virtuellen Computer neu.<br> Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Linux-Computer“ > **Neustart**.<br> Öffnen Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) nacheinander **Virtuelle Computer** > **Instanzen** > **Neustart**.

3. [Ändern Sie die Größe des virtuellen Computers.](https://msdn.microsoft.com/library/dn168976.aspx)

4. Führen Sie dazu auf dem virtuellen Computer die unter [Zurücksetzen eines Kennworts oder einer SSH für Linux-basierte virtuelle Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) beschriebenen Anweisungen für folgende Schritte aus:

	- Zurücksetzen des Kennworts oder des SSH-Schlüssels
	- Erstellen eines neuen _sudo_-Benutzerkontos.
	- Zurücksetzen der SSH-Konfiguration

5. Überprüfen Sie die Ressourcenintegrität des virtuellen Computers auf etwaige Plattformprobleme.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > „Ihr virtueller Linux-Computer“ > **Einstellungen** > **Integrität prüfen**.


### Virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden

Führen Sie die folgenden Schritte aus, um häufige SSH-Probleme für virtuelle Computer zu beheben, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt werden.

1. _Setzen Sie die SSH-Verbindung_ mit Ihrem virtuellen Linux-Computer in der Befehlszeile zurück, indem Sie entweder die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwenden. Stellen Sie sicher, dass [Microsoft Azure Linux Agent](virtual-machines-linux-agent-user-guide.md) Version 2.0.5 oder höher installiert ist.

**Verwenden der Azure-Befehlszeilenschnittstelle**:

a. [Installieren Sie die Azure-Befehlszeilenschnittstelle, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her](../xplat-cli-install.md), indem Sie den Befehl `azure login` verwenden (falls noch nicht geschehen).

b. Stellen Sie sicher, dass Sie sich im Ressourcen-Manager-Modus befinden. Bei den aktuellsten Versionen der Azure-Befehlszeilenschnittstelle gelangen Sie automatisch in den Ressourcen-Manager-Modus.

	```
	azure config mode arm
	```

c. Setzen Sie die SSH-Verbindung mit einer der folgenden Methoden zurück.

* Verwenden Sie den Befehl `vm reset-access` wie im folgenden Beispiel.

	```
	azure vm reset-access -g YourResourceGroupName -n YourVirtualMachineName -r
	```

Die `VMAccessForLinux`-Erweiterung wird auf Ihrem virtuellen Computer installiert.

* Alternativ dazu können Sie eine Datei mit dem Namen „PrivateConf.json“ und dem folgenden Inhalt erstellen:

	```
	{  
		"reset_ssh":"True"
	}
	```

Führen Sie anschließend manuell die `VMAccessForLinux`-Erweiterung aus, um die SSH-Verbindung zurückzusetzen.

	```
	azure vm extension set "YourResourceGroupName" "YourVirtualMachineName" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

**Verwenden von Azure PowerShell**:

a. [Installieren Sie Azure PowerShell, und stellen Sie eine Verbindung mit Ihrem Azure-Abonnement her](../powershell-install-configure.md), indem Sie die Azure AD-Methode verwenden. Bei Azure PowerShell-Versionen vor 1.0.x, müssen Sie über _Switch-AzureMode_ explizit in den Ressourcen-Manager-Modus wechseln.

b. Führen Sie die `VMAccessForLinux`-Erweiterung aus, um die SSH-Verbindung wie im folgenden Beispiel gezeigt zurückzusetzen. In früheren Versionen wäre es der Befehl _Set-AzureVMExtension_.

	```
	Set-AzureRmVMExtension -ResourceGroupName "yourRG" -VMName "yourVM" -Location "West US" -Name "VMAccessForLinux" -Publisher "Microsoft.OSTCExtensions" -ExtensionType "VMAccessForLinux" -TypeHandlerVersion "1.2" -SettingString "{}" -ProtectedSettingString '{"reset_ssh":true}'
	```

2. Starten Sie den virtuellen Linux-Computer über das [Azure-Portal](https://portal.azure.com) neu.<br> Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer** > „Ihr virtueller Linux-Computer“ > **Neu starten**.

3. _Setzen Sie Ihr Kennwort oder den SSH-Schlüssel_ mit Ihrem virtuellen Linux-Computer in der Befehlszeile zurück, indem Sie entweder die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwenden. Sie können auch wie im folgenden Beispiel gezeigt einen neuen Benutzernamen und ein neues Kennwort mit _sudo_-Berechtigung erstellen.

**Verwenden der Azure-Befehlszeilenschnittstelle**:

Installieren und konfigurieren Sie die Azure-Befehlszeilenschnittstelle wie oben erwähnt. Falls erforderlich, wechseln Sie in den Ressourcen-Manager-Modus und führen Sie die Erweiterung mit einem der folgenden Verfahren aus.

* Führen Sie den Befehl `vm reset-access` aus, um die SSH-Anmeldeinformationen festzulegen.

	```
	azure vm reset-access TestRgV2 TestVmV2 -u NewUser -p NewPassword
	```

Weitere Informationen hierzu erhalten Sie, indem Sie in der Befehlszeile `azure vm reset-access -h` eingeben.

* Alternativ dazu können Sie eine Datei mit dem Namen „PrivateConf.json“ und dem folgenden Inhalt erstellen.

	```
	{
		"username":"NewUsername", "password":"NewPassword", "expiration":"2016-01-01", "ssh_key":"", "reset_ssh":false, "remove_user":""
	}
	```

Führen Sie anschließend die Linux-Erweiterung mithilfe der oben angegebenen Datei aus.

	```
	$azure vm extension set "testRG" "testVM" VMAccessForLinux Microsoft.OSTCExtensions "1.2" --private-config-path PrivateConf.json
	```

Beachten Sie, dass Sie Schritte wie unter [Zurücksetzen eines Kennworts oder einer SSH für Linux-basierte virtuelle Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) ausführen können, um andere Varianten auszuprobieren. Denken Sie hierbei daran, die Anweisungen der Azure-Befehlszeilenschnittstelle für den Ressourcen-Manager-Modus zu ändern.


**Verwenden von Azure PowerShell**:

Installieren und konfigurieren Sie Azure PowerShell wie oben erwähnt. Wechseln Sie in den Ressourcen-Manager-Modus, und führen Sie die Erweiterung dann wie folgt aus.

	```
	$RGName = 'testRG'
	$VmName = 'testVM'
	$Location = 'West US'

	$ExtensionName = 'VMAccessForLinux'
	$Publisher = 'Microsoft.OSTCExtensions'
	$Version = '1.2'

	$PublicConf = '{}'
	$PrivateConf = '{"username":"NewUsername", "password":"NewPassword", "ssh_key":"", "reset_ssh":false, "remove_user":""}'

	Set-AzureRmVMExtension -ResourceGroupName $RGName -VMName $VmName -Location $Location -Name $ExtensionName -Publisher $Publisher -ExtensionType $ExtensionName -TypeHandlerVersion $Version -SettingString $PublicConf -ProtectedSettingString $PrivateConf
	```

Achten Sie darauf, die Werte von $RGName, $VmName, $Location und die SSH-Anmeldeinformationen durch die jeweiligen Werte für Ihre Installation zu ersetzen.



## Detaillierte Problembehandlung für SSH-Fehler

Falls der SSH-Client den SSH-Dienst auf dem virtuellen Computer immer noch nicht erreichen kann, kann dies verschiedenste Ursachen haben. Nachfolgend werden die beteiligten Komponenten aufgeführt.

![Diagramm mit den Komponenten des SSH-Diensts](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Mit den Informationen in den folgenden Abschnitten können Sie die Fehlerquelle isolieren und Lösungen oder Problemumgehungen ermitteln.

### Vorbereitende Schritte

Überprüfen Sie zunächst den Status des virtuellen Computers im Portal.

Im [klassischen Azure-Portal](https://manage.windowsazure.com) für virtuelle Computer im klassischen Bereitstellungsmodell:

1. Klicken Sie auf **Virtuelle Computer** > *VM-Name*.
2. Klicken Sie auf das **Dashboard** des virtuellen Computers, um den Status zu überprüfen.
3. Klicken Sie auf **Überwachen**, um aktuelle Aktivitäten für Compute-, Speicher- und Netzwerkressourcen anzuzeigen.
4. Klicken Sie auf **Endpunkte**, und vergewissern Sie sich, dass ein Endpunkt für SSH-Datenverkehr vorhanden ist.

Im [Azure-Portal](https://portal.azure.com):

1. Klicken Sie für einen virtuellen Computer, der mit dem klassischen Bereitstellungsmodell erstellt wurde, auf **Durchsuchen** > **Virtuelle Computer (klassisch)** > *VM-Name*. Klicken Sie für einen virtuellen Computer, der mit dem Ressourcen-Manager erstellt wurde, auf **Durchsuchen** > **Virtuelle Computer (v2)** > *VM-Name*. Im Statusbereich für den virtuellen Computer wird **Wird ausgeführt** angezeigt. Führen Sie einen Bildlauf nach unten durch, um aktuelle Aktivitäten für Compute-, Speicher- und Netzwerkressourcen anzuzeigen.
2. Klicken Sie auf **Einstellungen**, um Endpunkte, IP-Adressen und andere Einstellungen zu überprüfen. Um Endpunkte in virtuellen Computern zu identifizieren, die mit dem Ressourcen-Manager erstellt wurden, überprüfen Sie Folgendes: ob eine [Netzwerksicherheitsgruppe](../virtual-network/virtual-networks-nsg.md) definiert ist, die darauf angewendeten Regeln und das Vorhandensein eines Verweises darauf im Subnetz.

Analysieren Sie zum Überprüfen der Netzwerkverbindung die konfigurierten Endpunkte, und testen Sie, ob der virtuelle Computer über ein anderes Protokoll (z. B. HTTP oder über einen anderen Dienst) erreichbar ist.

Versuchen Sie nach diesen Schritten erneut, die SSH-Verbindung herzustellen.


### Ermitteln der Ursache des Problems

Das Verbindungsproblem zwischen dem SSH-Client auf Ihrem Computer und dem SSH-Dienst auf dem virtuellen Azure-Computer ist u. U. auf die folgenden Problem- oder Konfigurationsfehlerquellen zurückzuführen:

- SSH-Clientcomputer
- Edgegerät der Organisation
- Clouddienst-Endpunkt und die Zugriffssteuerungsliste (ACL)
- Netzwerksicherheitsgruppen
- Linux-basierter virtueller Azure-Computer

#### Quelle 1: SSH-Clientcomputer

Überprüfen Sie, ob Ihr Computer SSH-Verbindungen mit einem anderen Linux-basierten lokalen Computer herstellen kann, um Ihren Computer als Ursache des Fehlers auszuschließen.

![Diagramm, in dem die SSH-Clientcomputerkomponente hervorgehoben ist](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

Falls dies nicht möglich ist, suchen Sie auf Ihrem Computer Folgendes:

- Eine lokale Firewall-Einstellung, die eingehenden oder ausgehenden SSH-Datenverkehr (TCP-22) blockiert
- Lokal installierte Proxy-Clientsoftware, die SSH-Verbindungen verhindert
- Lokal installierte Netzwerküberwachungssoftware, die SSH-Verbindungen verhindert
- Andere Arten von Sicherheitssoftware, die Datenverkehr überwachen oder bestimmte Arten von Datenverkehr zulassen/unterbinden

In allen diesen Fällen deaktivieren Sie zur Ermittlung der Ursache die Software vorübergehend, und versuchen Sie dann, eine SSH-Verbindung mit einem lokalen Computer herzustellen. Korrigieren Sie dann in Zusammenarbeit mit Ihrem Netzwerkadministrator die Einstellungen der Software, um SSH-Verbindungen zu ermöglichen.

Vergewissern Sie sich bei Verwendung der Zertifikatauthentifizierung, dass Sie für den SSH-Ordner in Ihrem Basisverzeichnis über folgende Berechtigungen verfügen:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id\_rsa (bzw. beliebige andere Dateien, in denen Ihre privaten Schlüssel gespeichert sind)
- Chmod 644 ~/.ssh/known\_hosts (enthält Hosts, mit denen Sie eine SSH-Verbindung hergestellt haben)

#### Quelle 2: Edgegerät der Organisation

Überprüfen Sie, ob ein direkt mit dem Internet verbundener Computer SSH-Verbindungen mit Ihrem virtuellen Azure-Computer herstellen kann, um das Edgegerät der Organisation als Fehlerquelle auszuschließen. Wenn der Zugriff auf den virtuellen Computer über ein Standort-zu-Standort-VPN oder über eine ExpressRoute-Verbindung erfolgt, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsg) fort.

![Diagramm, in dem das Edgegerät der Organisation hervorgehoben ist](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Wenn Sie keinen Computer haben, der direkt mit dem Internet verbunden ist, können Sie problemlos einen neuen virtuellen Azure-Computer in einer eigenen Ressourcengruppe oder einem eigenen Clouddienst erstellen und verwenden. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Linux in Azure](virtual-machines-linux-tutorial.md). Löschen Sie die Ressourcengruppe oder den virtuellen Computer und den Clouddienst, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem direkt mit dem Internet verbundenen Computer herstellen können, überprüfen Sie das Edgegerät Ihrer Organisation auf Folgendes:

- Eine interne Firewall, die SSH-Verbindungen mit dem Internet blockiert
- Proxy-Server, der SSH-Verbindungen verhindert
- Auf dem Edgegerät ausgeführte Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen, die SSH-Verbindungen verhindert

Korrigieren Sie in Zusammenarbeit mit Ihrem Netzwerkadministrator die Einstellungen des Edgegeräts Ihrer Organisation, um SSH-Datenverkehr über das Internet zuzulassen.

#### Quelle 3: Clouddienst-Endpunkt und ACL

> [AZURE.NOTE] Diese Quelle gilt nur für virtuelle Computer, die mit dem klassischen Bereitstellungsmodell erstellt werden. Für virtuelle Computer, die mit dem Ressourcen-Manager erstellt wurden, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsg) fort.

Überprüfen Sie bei virtuellen Computern, die mit dem [klassischen Bereitstellungsmodell](../resource-manager-deployment-model.md) erstellt wurden, ob ein anderer virtueller Azure-Computer im gleichen virtuellen Netzwerk eine SSH-Verbindung mit Ihrem virtuellen Computer herstellen kann, um den Clouddienst-Endpunkt und die ACL als Fehlerquelle auszuschließen.

![Diagramm, in dem der Clouddienstendpunkt und ACL hervorgehoben sind](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

Ist kein anderer virtueller Computer im gleichen virtuellen Netzwerk vorhanden, können Sie problemlos einen neuen virtuellen Azure-Computer erstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Linux in Azure](virtual-machines-linux-tutorial.md). Löschen Sie den zusätzlichen virtuellen Computer, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem virtuellen Computer im gleichen virtuellen Netzwerk herstellen können, überprüfen Sie Folgendes:

- Die Endpunktkonfiguration für den SSH-Datenverkehr auf dem virtuellen Zielcomputer. Der private TCP-Port des Endpunkts sollte dem TCP-Port entsprechen, an dem der SSH-Dienst auf dem virtuellen Computer lauscht (Standardeinstellung ist Port 22). Überprüfen Sie bei virtuellen Computern, die im Ressourcen-Manager-Bereitstellungsmodell mithilfe von Vorlagen erstellt wurden, die SSH-TCP-Portnummer im Azure-Portal unter **Durchsuchen** > **Virtuelle Computer (v2)** > *VM-Name* > **Einstellungen** > **Endpunkte**.
- Die ACL für den SSH-Datenverkehrsendpunkt auf dem virtuellen Zielcomputer. In ACLs können Sie anhand der Quell-IP-Adresse angeben, ob eingehender Datenverkehr aus dem Internet zugelassen oder verweigert wird. Falsch konfigurierte ACLs können dazu führen, dass eingehender SSH-Datenverkehr den Endpunkt nicht erreicht. Untersuchen Sie die ACLs, um sicherzustellen, dass eingehender Verkehr von den öffentlichen IP-Adressen des Proxy- oder anderen Edgeservers zugelassen wird. Weitere Informationen finden Sie unter [Was ist eine Netzwerk-Zugriffssteuerungsliste (Access Control List, ACL)?](../virtual-network/virtual-networks-acl.md)

Entfernen Sie den aktuellen Endpunkt, und erstellen Sie einen neuen Endpunkt mit dem **SSH**-Namen und dem TCP-Port 22 als öffentliche und private Portnummer, um den Endpunkt als Problemquelle auszuschließen. Weitere Informationen finden Sie unter [Festlegen von Endpunkten auf einem virtuellen Computer in Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Quelle 4: Netzwerksicherheitsgruppen

Netzwerk-Sicherheitsgruppen ermöglichen Ihnen eine präzisere Steuerung des zulässigen eingehenden und ausgehenden Datenverkehrs. Sie können Regeln erstellen, die mehrere Subnetze und Clouddienste in einem virtuellen Azure-Netzwerk umfassen. Überprüfen Sie die Regeln der Netzwerksicherheitsgruppe, um sicherzustellen, dass sowohl eingehender als auch ausgehender SSH-Datenverkehr über das Internet zulässig ist. Weitere Informationen finden Sie unter [Informationen zu Netzwerksicherheitsgruppen](../virtual-network/virtual-networks-nsg.md).

#### Quelle 5: Linux-basierter virtueller Azure-Computer

Die letzte mögliche Problemquelle ist der virtuelle Azure-Computer selbst.

![Diagramm, in dem der Linux-basierte virtuelle Azure-Computer hervorgehoben ist](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Führen Sie auf dem virtuellen Computer die unter [Zurücksetzen eines Kennworts oder einer SSH für Linux-basierte virtuelle Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) beschriebenen Anweisungen aus.

Versuchen Sie erneut, die Verbindung von Ihrem Computer aus herzustellen. Wenn dies weiterhin nicht möglich ist, können u. a. die folgenden Probleme der Grund dafür sein:

- Der SSH-Dienst wird auf dem virtuellen Zielcomputer nicht ausgeführt.
- Der SSH-Dienst lauscht nicht am TCP-Port 22. Installieren Sie zu Testzwecken einen Telnet-Client auf Ihrem lokalen Computer, und führen Sie „telnet *Name des Clouddiensts*.cloudapp.net 22“ aus. So können Sie ermitteln, ob der virtuelle Computer die eingehende und ausgehende Kommunikation mit dem SSH-Endpunkt zulässt.
- Für die lokale Firewall auf dem virtuellen Zielcomputer sind Regeln definiert, die eingehenden oder ausgehenden SSH-Datenverkehr verhindern.
- Auf dem virtuellen Azure-Computer wird Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen ausgeführt, die SSH-Verbindungen verhindert.


## Zusätzliche Ressourcen

Für virtuelle Computer im klassischen Bereitstellungsmodell: [Zurücksetzen eines Kennworts oder einer SSH für Linux-basierte virtuelle Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Problembehandlung bei Windows-Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=AcomDC_0128_2016-->