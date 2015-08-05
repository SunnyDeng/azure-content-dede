<properties 
	pageTitle="Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer" 
	description="Wenn Sie keine Verbindung mit Ihrem Linux-basierten virtuellen Azure-Computer herstellen können, verwenden Sie diese Schritte, um die Ursache des Problems zu isolieren."
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager"/>


<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/07/2015" 
	ms.author="josephd"/>

# Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer

Wenn Sie keine Verbindung mit Linux-basierten virtuellen Azure-Computern herstellen können, finden Sie in diesem Artikel die Beschreibung eines methodischen Ansatzes zum Isolieren und Beheben des Problems.

## Schritt 1: Zurücksetzen von SSH-Konfiguration, -Schlüssel oder Kennwort

Führen Sie dazu auf dem virtuellen Computer die unter [Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) beschriebenen Anweisungen aus. Diese Anweisungen ermöglichen Folgendes:

- Zurücksetzen des Kennworts oder des SSH-Schlüssels
- Erstellen eines neuen sudo-Benutzerkontos
- Zurücksetzen der SSH-Konfiguration

Falls der SSH-Client den SSH-Dienst auf dem virtuellen Computer immer noch nicht erreichen kann, kann dies verschiedenste Ursachen haben. Nachfolgend werden die beteiligten Komponenten aufgeführt.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot1.png)

In den folgenden Abschnitten wird schrittweise erläutert, wie die verschiedenen Ursachen für dieses Problem isoliert und ermittelt werden. Zudem werden Lösungen und Problemumgehungen bereitgestellt.

## Schritt 2: Vorbereitende Schritte vor der detaillierten Problembehandlung

Überprüfen Sie zunächst im Azure-Verwaltungsportal oder im Azure-Vorschauportal den Status des virtuellen Computers.

Azure-Verwaltungsportal:

1. Klicken Sie auf **Virtuelle Computer** > *VM-Name*.
2. Klicken Sie für den virtuellen Computer auf **Dashboard**, um den Status zu überprüfen.
3. Klicken Sie auf **Überwachen**, um aktuelle Aktivitäten für Compute-, Speicher- und Netzwerkressourcen anzuzeigen. 
4. Klicken Sie auf **Endpunkte**, und vergewissern Sie sich, dass ein Endpunkt für SSH-Datenverkehr vorhanden ist.

Azure-Vorschauportal:

1. Klicken Sie auf **Durchsuchen** > **Virtuelle Computer** > *VM-Name*. Klicken Sie für einen virtuellen Computer, der im Ressourcen-Manager erstellt wurde, auf **Durchsuchen** > **Virtuelle Computer (v2)** > *VM-Name*. Im Statusbereich für den virtuellen Computer wird **Wird ausgeführt** angezeigt. Führen Sie einen Bildlauf nach unten durch, um aktuelle Aktivitäten für Compute-, Speicher- und Netzwerkressourcen anzuzeigen.
2. Klicken Sie auf **Einstellungen**, um Endpunkte, IP-Adressen und andere Einstellungen zu überprüfen.

Analysieren Sie zum Überprüfen der Netzwerkverbindung die konfigurierten Endpunkte, und testen Sie, ob der virtuelle Computer über ein anderes Protokoll (z. B. HTTP) oder über einen anderen bekannten Dienst erreichbar ist.

Führen Sie bei Bedarf einen [Neustart des virtuellen Computers](https://msdn.microsoft.com/library/azure/dn763934.aspx) durch, oder [passen Sie die Größe des virtuellen Computers an](https://msdn.microsoft.com/library/dn168976.aspx).

Versuchen Sie nach diesen Schritten erneut, die SSH-Verbindung herzustellen.

## Schritt 3: Detaillierte Problembehandlung 

Das Verbindungsproblem zwischen dem SSH-Client und dem SSH-Dienst auf dem virtuellen Azure-Computer ist u. U. auf die folgenden Problem- oder Konfigurationsfehlerquellen zurückzuführen:

- SSH-Clientcomputer
- Edgegerät der Organisation
- Cloud-Dienstendpunkt und die Zugriffssteuerungsliste (ACL)
- Netzwerksicherheitsgruppen
- Linux-basierter virtueller Azure-Computer

### Quelle 1: SSH-Clientcomputer

Überprüfen Sie, ob Ihr Computer SSH-Verbindungen mit einem anderen Linux-basierten lokalen Computer herstellen kann, um Ihren Computer als Problem- oder Konfigurationsfehlerquelle auszuschließen.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot2.png)
 
Falls dies nicht möglich ist, suchen Sie auf Ihrem Computer Folgendes:

- Eine lokale Firewall-Einstellung, die eingehenden oder ausgehenden SSH-Datenverkehr (TCP-22) blockiert
- Lokal installierte Proxy-Clientsoftware, die SSH-Verbindungen verhindert
- Lokal installierte Netzwerküberwachungssoftware, die SSH-Verbindungen verhindert
- Andere Arten von Sicherheitssoftware, die Datenverkehr überwachen oder bestimmte Arten von Datenverkehr zulassen/unterbinden und dadurch SSH-Verbindungen verhindern

In allen diesen Fällen sollten Sie zur Ermittlung der Ursache die Software vorübergehend deaktivieren und dann versuchen, eine SSH-Verbindung mit einem lokalen Computer herzustellen. Korrigieren Sie dann in Zusammenarbeit mit Ihrem Netzwerkadministrator die Einstellungen der Software, um SSH-Verbindungen zu ermöglichen.

Vergewissern Sie sich bei Verwendung der Zertifikatauthentifizierung, dass Sie für den SSH-Ordner in Ihrem Basisverzeichnis über folgende Berechtigungen verfügen:

- Chmod 700 ~/.ssh
- Chmod 644 ~/.ssh/*.pub
- Chmod 600 ~/.ssh/id_rsa (bzw. beliebige andere Dateien, in denen Ihre privaten Schlüssel gespeichert sind)
- Chmod 644 ~/.ssh/known_hosts (enthält Hosts, mit denen Sie eine SSH-Verbindung hergestellt haben)

### Quelle 2: Edgegerät der Organisation

Überprüfen Sie, ob ein direkt mit dem Internet verbundener Computer SSH-Verbindungen mit Ihrem virtuellen Azure-Computer herstellen kann, um das Edgegerät der Organisation als Problem- oder Konfigurationsfehlerquelle auszuschließen. Wenn der Zugriff auf den virtuellen Computer über ein Standort-zu-Standort-VPN oder über eine ExpressRoute-Verbindung erfolgt, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsg) fort.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot3.png)
 
Wenn Sie keinen Computer haben, der direkt mit dem Internet verbunden ist, können Sie problemlos einen neuen virtuellen Azure-Computer in einer eigenen Ressourcengruppe oder einem eigenen Clouddienst erstellen und verwenden. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Linux in Azure](virtual-machines-linux-tutorial.md). Löschen Sie die Ressourcengruppe oder den virtuellen Computer und den Clouddienst, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem direkt mit dem Internet verbundenen Computer herstellen können, überprüfen Sie das Edgegerät Ihrer Organisation auf Folgendes:

- Eine interne Firewall, die SSH-Verbindungen mit dem Internet blockiert
- Proxy-Server, der SSH-Verbindungen verhindert
- Auf dem Edgegerät ausgeführte Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen, die SSH-Verbindungen verhindert

Korrigieren Sie in Zusammenarbeit mit Ihrem Netzwerkadministrator die Einstellungen des Edgegeräts Ihrer Organisation, um SSH-Datenverkehr über das Internet zuzulassen.

### Quelle 3: Cloud-Dienstendpunkt und ACL

Um Clouddienst-Endpunkt und ACL als Problemquelle oder die Fehlkonfiguration virtueller Computer auszuschließen, die in der Dienstverwaltung erstellt wurden, überprüfen Sie, ob ein anderer virtueller Azure-Computer in demselben virtuellen Netzwerk SSH-Verbindungen mit Ihrem virtuellen Azure-Computer herstellen kann.
 
![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot4.png)

> [AZURE.NOTE]Für virtuelle Computer, die im Ressourcen-Manager erstellt wurden, fahren Sie mit [Quelle 4: Netzwerksicherheitsgruppen](#nsg) fort.

Ist kein anderer virtueller Computer im gleichen virtuellen Netzwerk vorhanden, können Sie problemlos einen neuen virtuellen Azure-Computer erstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Computers unter Linux in Azure](virtual-machines-linux-tutorial.md). Löschen Sie den zusätzlichen virtuellen Computer, wenn Sie Ihre Tests abgeschlossen haben.

Wenn Sie eine SSH-Verbindung mit einem virtuellen Computer im gleichen virtuellen Netzwerk herstellen können, überprüfen Sie Folgendes:

- Die Endpunktkonfiguration für den SSH-Datenverkehr auf dem virtuellen Zielcomputer. Der private TCP-Port des Endpunkts muss dem TCP-Port entsprechen, an dem der SSH-Dienst auf dem virtuellen Computer lauscht. In der Standardeinstellung ist dies Port 22. Überprüfen Sie bei virtuellen Computern, die im Ressourcen-Manager mithilfe von Vorlagen erstellt wurden, die SSH-TCP-Portnummer im Azure-Vorschauportal unter **Durchsuchen** > **Virtuelle Computer (v2)** > *VM-Name* > **Einstellungen** > **Endpunkte**.
- Die ACL für den SSH-Datenverkehrsendpunkt auf dem virtuellen Zielcomputer. In ACLs können Sie anhand der Quell-IP-Adresse angeben, ob eingehender Datenverkehr aus dem Internet zugelassen oder verweigert wird. Falsch konfigurierte ACLs können dazu führen, dass eingehender SSH-Datenverkehr den Endpunkt nicht erreicht. Untersuchen Sie die ACLs, um sicherzustellen, dass eingehender Verkehr von den öffentlichen IP-Adressen des Proxy- oder anderen Edgeservers zugelassen wird. Weitere Informationen finden Sie unter [Netzwerk-Zugriffssteuerungslisten](https://msdn.microsoft.com/library/azure/dn376541.aspx).

Entfernen Sie den aktuellen Endpunkt, und erstellen Sie einen neuen Endpunkt mit dem SSH-Namen und dem TCP-Port 22 als öffentliche und private Portnummer, um den Endpunkt als Problemquelle auszuschließen. Weitere Informationen finden Sie unter [Festlegen von Endpunkten auf einem virtuellen Computer in Azure](virtual-machines-set-up-endpoints.md).

### <a id="nsg"></a>Quelle 4: Netzwerksicherheitsgruppen

Netzwerk-Sicherheitsgruppen ermöglichen Ihnen eine präzisere Steuerung des zulässigen eingehenden und ausgehenden Datenverkehrs. Sie können Regeln erstellen, die mehrere Subnetze und Cloud-Dienste in einem virtuellen Azure-Netzwerk umfassen. Überprüfen Sie die Regeln der Netzwerksicherheitsgruppe, um sicherzustellen, dass sowohl eingehender als auch ausgehender SSH-Datenverkehr über das Internet zulässig ist. Weitere Informationen finden Sie unter [Informationen zu Netzwerksicherheitsgruppen](../traffic-manager/virtual-networks-nsg.md).

### Quelle 5: Linux-basierter virtueller Azure-Computer 

Die letzte mögliche Problemquelle ist der virtuelle Azure-Computer selbst.

![](./media/virtual-machines-troubleshoot-ssh-connections/ssh-tshoot5.png)
 
Führen Sie auf dem virtuellen Computer die unter [Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md) beschriebenen Anweisungen aus.

Versuchen Sie erneut, die Verbindung von Ihrem Computer aus herzustellen. Sollten weiterhin Probleme auftreten, können u. a. die folgenden Probleme der Grund dafür sein:

- Der SSH-Dienst wird auf dem virtuellen Zielcomputer nicht ausgeführt.
- Der SSH-Dienst lauscht nicht am TCP-Port 22. Installieren Sie zu Testzwecken einen Telnet-Client auf Ihrem lokalen Computer, und führen Sie „telnet *Name des Clouddiensts*.cloudapp.net 22“ aus. So können Sie ermitteln, ob der virtuelle Computer die eingehende und ausgehende Kommunikation mit dem SSH-Endpunkt zulässt.
- Für die lokale Firewall auf dem virtuellen Zielcomputer sind Regeln definiert, die eingehenden oder ausgehenden SSH-Datenverkehr verhindern.
- Auf dem virtuellen Azure-Computer wird Software zur Netzwerküberwachung oder zur Erkennung von Eindringversuchen ausgeführt, die SSH-Verbindungen verhindert.


## Schritt 4: Übermitteln des Problems an die Azure-Support-Foren

Sie können Hilfe von Azure-Experten aus aller Welt erhalten, indem Sie Ihr Problem an das MSDN Azure-Forum oder das Stack Overflow-Forum senden. Weitere Informationen finden Sie unter [Microsoft Azure-Foren](http://azure.microsoft.com/support/forums/).

## Schritt 5: Erstellen eines Azure-Supportfalls

Wenn Sie die Schritte 1 bis 4 in diesem Artikel durchgeführt und Ihr Problem an die Azure-Supportforen gesendet haben, aber immer noch keine SSH-Verbindung herstellen können, empfiehlt es sich unter Umständen, den virtuellen Computer neu zu erstellen.

Wenn Sie den virtuellen Computer nicht neu erstellen können, ist es wahrscheinlich an der Zeit, einen Azure-Supportfall zu erstellen.

Zum Übermitteln eines Vorfalls wechseln Sie zur [Azure-Support-Website](http://azure.microsoft.com/support/options/), und klicken Sie auf **Support erhalten**.

Informationen zur Nutzung von Azure-Support finden Sie unter der [Microsoft Azure-Support-FAQ](http://azure.microsoft.com/support/faq/).

## Zusätzliche Ressourcen

[Gewusst wie: Zurücksetzen eines Kennworts oder einer SSH für virtuelle Linux-Computer](virtual-machines-linux-use-vmaccess-reset-password-or-ssh.md)

[Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird](virtual-machines-troubleshoot-access-application.md)
 

<!---HONumber=July15_HO4-->