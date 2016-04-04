


Wenn Sie auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird, nicht zugreifen können, finden Sie in diesem Artikel einen methodischen Ansatz zum Ermitteln der Ursache des Problems und zu seiner Behebung.

> [AZURE.NOTE]  Hilfeinformationen zum Herstellen einer Verbindung mit einem virtuellen Azure-Computer finden Sie unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-windows-troubleshoot-rdp-connection.md) oder [Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](virtual-machines-linux-troubleshoot-ssh-connection.md).

Wenn Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie Ihre Frage im [MSDN Azure-Forum oder im Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) stellen, um dort Hilfe von Azure-Experten zu erhalten. Alternativ dazu haben Sie die Möglichkeit, einen Azure-Supportfall zu erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und klicken Sie auf **Support erhalten**.


Die Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird, konzentriert sich auf vier Hauptbereiche.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1.	Die Anwendung, die auf dem virtuellen Azure-Computer ausgeführt wird.
2.	Den virtuellen Azure-Computer.
3.	Azure-Endpunkte für den Clouddienst, der den virtuellen Computer enthält (für virtuelle Computer im klassischen Bereitstellungsmodell), eingehende NAT-Regeln (für virtuelle Computer im Resource Manager-Bereitstellungsmodell) und Netzwerksicherheitsgruppen
4.	Ihre Internet-Edgegerät.

Bei Clientcomputern, die über eine Site-to-Site-VPN- oder ExpressRoute-Verbindung auf die Anwendung zugreifen, sind die Hauptbereiche, die zu Problemen führen können, die Anwendung und der virtuelle Azure-Computer. Gehen Sie folgendermaßen vor, um die Quelle des Problems und dessen Korrektur zu bestimmen.

## Schritt 1: Können Sie von der virtuellen Ziel-VM aus auf die Anwendung zugreifen?

Versuchen Sie, mithilfe des geeigneten Clientprogramms von der VM, auf der es läuft, auf die Anwendung zuzugreifen. Verwenden Sie den lokalen Hostnamen, die lokale IP-Adresse oder die Loopbackadresse (127.0.0.1).

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Wenn es sich bei der Anwendung beispielsweise um einen Webserver handelt, öffnen Sie einen Browser auf der VM, und versuchen Sie, auf eine Webseite zuzugreifen, die auf der VM gehostet wird.

Wenn Sie auf die Anwendung zugreifen können, fahren Sie mit [Schritt 2](#step2) fort.

Wenn kein Zugriff auf die Anwendung möglich ist, überprüfen Sie Folgendes:

- Die Anwendung wird auf dem virtuellen Zielcomputer ausgeführt.
- Die Anwendung überwacht die erwarteten TCP- und UDP-Ports.

Verwenden Sie bei Windows- und Linux-basierten virtuellen Computern den Befehl **netstat -a**, um die aktiven Überwachungsports anzuzeigen. Überprüfen Sie die Ausgabe für die erwarteten Ports, die die Anwendung überwachen soll. Starten Sie die Anwendung neu, oder konfigurieren Sie sie so, dass Sie die erwarteten Ports nach Bedarf verwendet.

## <a id="step2"></a>Schritt 2: Können von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk aus auf die Anwendung zugreifen?

Versuchen Sie, von einer anderen VM im gleichen virtuellen Netzwerk auf die Anwendung zuzugreifen. Verwenden Sie dazu den VM-Hostnamen oder die von Azure zugewiesene öffentliche, private oder Anbieter-IP-Adresse. Verwenden Sie für mithilfe des klassischen Bereitstellungsmodells erstellte virtuelle Computer nicht die öffentliche IP-Adresse des Clouddiensts.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Wenn es sich bei der Anwendung beispielsweise um einen Webserver handelt, versuchen Sie, in einem Browser auf einer anderen VM im gleichen virtuellen Netzwerk auf eine Webseite zuzugreifen.

Wenn Sie auf die Anwendung zugreifen können, fahren Sie mit [Schritt 3](#step3) fort.

Wenn kein Zugriff auf die Anwendung möglich ist, überprüfen Sie Folgendes:

- Die Hostfirewall auf der Ziel-VM lässt die eingehende Anforderung und den ausgehenden Antwortdatenverkehr zu.
- Software zur Angriffserkennung oder Netzwerküberwachung, die auf der Ziel-VM ausgeführt wird, lässt den Datenverkehr zu.
- Netzwerksicherheitsgruppen lassen den Datenverkehr zu.
- Eine separate Komponente, die auf Ihrer VM auf dem Pfad zwischen der Test-VM und Ihrer VM ausgeführt wird, z.B. ein Load Balancer oder eine Firewall, lässt den Datenverkehr zu.

Verwenden Sie auf einem Windows-basierten virtuellen Computer die Windows-Firewall mit erweiterter Sicherheit, um zu bestimmen, ob die Firewallregeln den eingehenden und ausgehenden Datenverkehr der Anwendung ausschließen.

## <a id="step3"></a>Schritt 3: Können Sie von einem Computer aus, der sich außerhalb des virtuellen Netzwerks befindet, aber nicht mit demselben Netzwerk wie Ihr Computer verbunden ist, auf die Anwendung zugreifen?

Versuchen Sie, von einem Computer, der sich nicht im gleichen Netzwerk wie die VM befindet, auf der die Anwendung ausgeführt wird, auf die Anwendung zuzugreifen. Der Computer darf sich aber ebenso wenig im Netzwerk Ihres ursprünglichen Clientcomputers befinden.

![](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Wenn es sich bei der Anwendung beispielsweise um einen Webserver handelt, versuchen Sie, in einem Browser auf eine Webseite zuzugreifen, der auf einem Computer ausgeführt wird, der sich nicht im virtuellen Netzwerk befindet.

Wenn kein Zugriff auf die Anwendung möglich ist, überprüfen Sie Folgendes:

- Bei VMs, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden, muss die Endpunktkonfiguration für die VM eingehenden Datenverkehr zulassen, insbesondere das Protokoll (TCP oder UDP) und die öffentlichen und privaten Portnummern. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-windows-classic-setup-endpoints.md).
- Bei VMs, die mithilfe des klassischen Bereitstellungsmodells erstellt wurden, dürfen Zugriffssteuerungslisten (ACLs) auf dem Endpunkt aus dem Internet eingehenden Datenverkehr nicht verhindern. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-windows-classic-setup-endpoints.md).
- Bei VMs, die mithilfe des Resource Manager-Bereitstellungsmodells erstellt wurden, muss die eingehende NAT-Regelkonfiguration für die VM den eingehenden Verkehr zulassen, insbesondere das Protokoll (TCP oder UDP) und die öffentlichen und privaten Portnummern.
- Netzwerksicherheitsgruppen lassen die eingehende Anforderung und den ausgehende Antwortdatenverkehr zu. Weitere Informationen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe (NSG)?](../virtual-network/virtual-networks-nsg.md).

Wenn der virtuelle Computer oder der Endpunkt Mitglied einer Gruppe mit Lastenausgleich ist:

- Stellen Sie sicher, dass der Prüfpunkt-Protokoll (TCP oder UDP) und die Portnummer richtig sind.
- Wenn das Prüfpunkt-Protokoll und der Port vom Protokoll und Port der Gruppe mit Lastenausgleich abweichen:
	- Stellen Sie sicher, dass die Anwendung das Testprotokoll (TCP oder UDP) und die Portnummer überwacht (Verwenden Sie **netstat –a** auf der Ziel-VM).
	- Die Hostfirewall auf der Ziel-VM lässt die eingehende Testanforderung und den ausgehenden Prüfpunkt-Antwortdatenverkehr zu.

Wenn Sie auf die Anwendung zugreifen können, stellen Sie sicher, dass Ihre Internet-Edgegeräte Folgendes zulassen:

- Den ausgehenden Anwendungsanforderungs-Datenverkehr vom Clientcomputer zum virtuellen Azure-Computer.
- Den eingehenden Anwendungs-Antwortdatenverkehr vom virtuellen Azure-Computer.

## Problembehandlung bei Endpunktverbindungen

Wenn Probleme beim Verbinden mit einem Endpunkt, z. B. Remotedesktop-Endpunkt, auftreten, können Sie die folgenden allgemeinen Schritte zur Fehlerbehebung ausprobieren:

- Neustarten des virtuellen Computers
- Neuerstellen des Endpunkts
- Herstellen einer Verbindung von einem anderen Standort
- Ändern der Größe des virtuellen Computers
- Neuerstellen des virtuellen Computers

Weitere Informationen finden Sie unter [Problembehandlung bei Endpunktverbindungen (RDP/SSH/HTTP-Fehler usw.)](https://social.msdn.microsoft.com/Forums/azure/de-DE/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows).



## Zusätzliche Ressourcen

[Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-windows-troubleshoot-rdp-connection.md)

[Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](virtual-machines-linux-troubleshoot-ssh-connection.md)

<!---HONumber=AcomDC_0323_2016-->