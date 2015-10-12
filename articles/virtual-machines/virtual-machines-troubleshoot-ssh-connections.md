<properties
	pageTitle="Keine Verbindungsherstellung mit einem virtuellen Azure-Computer über SSH | Microsoft Azure"
	description="Hier erhalten Sie Informationen zum Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer."
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
	ms.date="09/25/2015"
	ms.author="dkshir"/>

# Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In diesem Artikel erfahren Sie, wie Sie Probleme bei SSH-Verbindungen auf einem virtuellen Computer behandeln, der mit dem klassischen oder Ressourcen-Manager-Bereitstellungsmodell erstellt wurde.


Es kann verschiedene Ursachen für SSH-Fehler auf einem Linux-basierten virtuellen Azure-Computer geben. Dieser Artikel hilft Ihnen, die Ursachen zu ermitteln und die Fehler zu beheben.

> [AZURE.NOTE]Dieser Artikel gilt nur für virtuelle Azure-Computer, auf denen Linux ausgeführt wird. Informationen zur Problembehandlung von Verbindungen mit virtuellen Azure-Computern unter Windows finden Sie in [diesem Artikel](virtual-machines-troubleshoot-remote-desktop-connections.md).

## Kontaktieren des Azure-Kundensupports

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](http://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten.

Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](http://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).


## Grundlegende Schritte

Führen Sie die folgenden Schritte aus, um häufig auftretende SSH-Verbindungsfehler zu beheben:

1. Setzen Sie den Remotezugriff über das [Azure-Portal](https://portal.azure.com) zurück. Klicken Sie auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > Ihr virtueller Windows-Computer > **Remotezugriff zurücksetzen**.

	![Remotezugriff zurücksetzen](./media/virtual-machines-troubleshoot-ssh-connections/Portal-SSH-Reset-Windows.png)

2. Starten Sie den virtuellen Computer neu. Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com) auf **Alle durchsuchen** > **Virtuelle Computer (klassisch)** > Ihr virtueller Windows-Computer > **Neustart**. Öffnen Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) die Option **Virtuelle Computer** > **Instanzen**, und klicken Sie auf **Neustart**.

3. [Ändern Sie die Größe des virtuellen Computers.](https://msdn.microsoft.com/library/dn168976.aspx)

4. Führen Sie dazu auf dem virtuellen Computer die unter [Zurücksetzen eines Kennworts oder einer SSH für Linux-basierte virtuelle Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) beschriebenen Anweisungen für Folgende Schritte aus:

	- Zurücksetzen des Kennworts oder des SSH-Schlüssels
	- Erstellen eines neuen sudo-Benutzerkontos
	- Zurücksetzen der SSH-Konfiguration

## Detaillierte Problembehandlung

Falls der SSH-Client den SSH-Dienst auf dem virtuellen Computer immer noch nicht erreichen kann, kann dies verschiedenste Ursachen haben. Nachfolgend werden die beteiligten Komponenten aufgeführt.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

Mit den Informationen in den folgenden Abschnitten können Sie die Fehlerquelle isolieren und Lösungen oder Problemumgehungen ermitteln.

### Schritte vor der Problembehandlung

Überprüfen Sie zunächst den Status des virtuellen Computers im Azure-Portal.

Im [Azure-Verwaltungsportal](https://manage.windowsazure.com):

1. Klicken Sie auf **Virtuelle Computer** > *VM-Name*.
2. Klicken Sie auf das **Dashboard** des virtuellen Computers, um den Status zu überprüfen.
3. Klicken Sie auf **Überwachen**, um aktuelle Aktivitäten für Compute-, Speicher- und Netzwerkressourcen anzuzeigen.
4. Klicken Sie auf **Endpunkte**, und vergewissern Sie sich, dass ein Endpunkt für SSH-Datenverkehr vorhanden ist.

Im [Azure-Vorschauportal](https://portal.azure.com):

1. Klicken Sie auf **Durchsuchen** > **Virtuelle Computer** > *VM-Name*. Klicken Sie für einen virtuellen Computer, der im Azure-Ressourcen-Manager erstellt wurde, auf **Durchsuchen** > **Virtuelle Computer (v2)** > *VM-Name*. Im Statusbereich für den virtuellen Computer wird **Wird ausgeführt** angezeigt. Führen Sie einen Bildlauf nach unten durch, um aktuelle Aktivitäten für Compute-, Speicher- und Netzwerkressourcen anzuzeigen.
2. Klicken Sie auf **Einstellungen**, um Endpunkte, IP-Adressen und andere Einstellungen zu überprüfen.

Analysieren Sie zum Überprüfen der Netzwerkverbindung die konfigurierten Endpunkte, und testen Sie, ob der virtuelle Computer über ein anderes Protokoll (z. B. HTTP oder über einen anderen Dienst) erreichbar ist.

Versuchen Sie nach diesen Schritten erneut, die SSH-Verbindung herzustellen.


### Schritte zur Problembehandlung

Das Verbindungsproblem zwischen dem SSH-Client auf Ihrem Computer und dem SSH-Dienst auf dem virtuellen Azure-Computer ist u. U. auf die folgenden Problem- oder Konfigurationsfehlerquellen zurückzuführen:

- SSH-Clientcomputer
- Edgegerät der Organisation
- Clouddienst-Endpunkt und die Zugriffssteuerungsliste (ACL)
- Netzwerksicherheitsgruppen
- Linux-basierter virtueller Azure-Computer

#### Quelle 1: SSH-Clientcomputer

Überprüfen Sie, ob Ihr Computer SSH-Verbindungen mit einem anderen Linux-basierten lokalen Computer herstellen kann, um Ihren Computer als Ursache des Fehlers auszuschließen.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)

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

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)

Wenn Sie keinen Computer haben, der direkt mit dem Internet verbunden ist, können Sie problemlos einen neuen virtuellen Azure-Computer in einer eigenen Ressourcengruppe oder einem eigenen Clouddienst erstellen und verwenden. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Linux in Azure](virtual-machines-linux-tutorial.md). Löschen Sie die Ressourcengruppe oder den virtuellen Computer und den Clouddienst, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem direkt mit dem Internet verbundenen Computer herstellen können, überprüfen Sie das Edgegerät Ihrer Organisation auf Folgendes:

- Eine interne Firewall, die SSH-Verbindungen mit dem Internet blockiert
- Proxy-Server, der SSH-Verbindungen verhindert
- Auf dem Edgegerät ausgeführte Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen, die SSH-Verbindungen verhindert

Korrigieren Sie in Zusammenarbeit mit Ihrem Netzwerkadministrator die Einstellungen des Edgegeräts Ihrer Organisation, um SSH-Datenverkehr über das Internet zuzulassen.

#### Quelle 3: Clouddienst-Endpunkt und ACL

Überprüfen Sie bei virtuellen Computern, die mit dem [klassischen Bereitstellungsmodell](../resource-manager-deployment-model.md) erstellt wurden, ob ein anderer virtueller Azure-Computer im gleichen virtuellen Netzwerk eine SSH-Verbindung mit Ihrem virtuellen Computer herstellen kann, um den Clouddienst-Endpunkt und die ACL als Fehlerquelle auszuschließen.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Für virtuelle Computer, die mit dem Ressourcen-Manager erstellt wurden, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsg) fort.

Ist kein anderer virtueller Computer im gleichen virtuellen Netzwerk vorhanden, können Sie problemlos einen neuen virtuellen Azure-Computer erstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Linux in Azure](virtual-machines-linux-tutorial.md). Löschen Sie den zusätzlichen virtuellen Computer, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem virtuellen Computer im gleichen virtuellen Netzwerk herstellen können, überprüfen Sie Folgendes:

- Die Endpunktkonfiguration für den SSH-Datenverkehr auf dem virtuellen Zielcomputer. Der private TCP-Port des Endpunkts sollte dem TCP-Port entsprechen, an dem der SSH-Dienst auf dem virtuellen Computer lauscht (Standardeinstellung ist Port 22). Überprüfen Sie bei virtuellen Computern, die im Ressourcen-Manager-Bereitstellungsmodell mithilfe von Vorlagen erstellt wurden, die SSH-TCP-Portnummer im Azure-Vorschauportal unter **Durchsuchen** > **Virtuelle Computer (v2)** > *VM-Name* > **Einstellungen** > **Endpunkte**.
- Die ACL für den SSH-Datenverkehrsendpunkt auf dem virtuellen Zielcomputer. In ACLs können Sie anhand der Quell-IP-Adresse angeben, ob eingehender Datenverkehr aus dem Internet zugelassen oder verweigert wird. Falsch konfigurierte ACLs können dazu führen, dass eingehender SSH-Datenverkehr den Endpunkt nicht erreicht. Untersuchen Sie die ACLs, um sicherzustellen, dass eingehender Verkehr von den öffentlichen IP-Adressen des Proxy- oder anderen Edgeservers zugelassen wird. Weitere Informationen finden Sie unter [Was ist eine Netzwerk-Zugriffssteuerungsliste (Access Control List, ACL)?](../virtual-network/virtual-networks-acl.md).

Entfernen Sie den aktuellen Endpunkt, und erstellen Sie einen neuen Endpunkt mit dem SSH-Namen und dem TCP-Port 22 als öffentliche und private Portnummer, um den Endpunkt als Problemquelle auszuschließen. Weitere Informationen finden Sie unter [Festlegen von Endpunkten auf einem virtuellen Computer in Azure](virtual-machines-set-up-endpoints.md).

<a id="nsg"></a>
#### Quelle 4: Netzwerksicherheitsgruppen

Netzwerk-Sicherheitsgruppen ermöglichen Ihnen eine präzisere Steuerung des zulässigen eingehenden und ausgehenden Datenverkehrs. Sie können Regeln erstellen, die mehrere Subnetze und Clouddienste in einem virtuellen Azure-Netzwerk umfassen. Überprüfen Sie die Regeln der Netzwerksicherheitsgruppe, um sicherzustellen, dass sowohl eingehender als auch ausgehender SSH-Datenverkehr über das Internet zulässig ist. Weitere Informationen finden Sie unter [Informationen zu Netzwerksicherheitsgruppen](../traffic-manager/virtual-networks-nsg.md).

#### Quelle 5: Linux-basierter virtueller Azure-Computer

Die letzte mögliche Problemquelle ist der virtuelle Azure-Computer selbst.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)

Führen Sie auf dem virtuellen Computer die unter [Zurücksetzen eines Kennworts oder einer SSH für Linux-basierte virtuelle Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) beschriebenen Anweisungen aus.

Versuchen Sie erneut, die Verbindung von Ihrem Computer aus herzustellen. Wenn dies weiterhin nicht möglich ist, können u. a. die folgenden Probleme der Grund dafür sein:

- Der SSH-Dienst wird auf dem virtuellen Zielcomputer nicht ausgeführt.
- Der SSH-Dienst lauscht nicht am TCP-Port 22. Installieren Sie zu Testzwecken einen Telnet-Client auf Ihrem lokalen Computer, und führen Sie „telnet *Name des Clouddiensts*.cloudapp.net 22“ aus. So können Sie ermitteln, ob der virtuelle Computer die eingehende und ausgehende Kommunikation mit dem SSH-Endpunkt zulässt.
- Für die lokale Firewall auf dem virtuellen Zielcomputer sind Regeln definiert, die eingehenden oder ausgehenden SSH-Datenverkehr verhindern.
- Auf dem virtuellen Azure-Computer wird Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen ausgeführt, die SSH-Verbindungen verhindert.


## Zusätzliche Ressourcen

[Zurücksetzen eines Kennworts oder einer SSH für Linux-basierte virtuelle Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Problembehandlung bei Windows-Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](virtual-machines-troubleshoot-access-application.md)

<!---HONumber=Oct15_HO1-->