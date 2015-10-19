<properties
	pageTitle="Behandeln von Problemen beim Zugriff auf Anwendungen auf einem virtuellen Computer | Microsoft Azure"
	description="Wenn Sie auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird, nicht zugreifen können, führen Sie diese Schritte aus, um die Ursache des Problems zu ermitteln."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/05/2015"
	ms.author="dkshir"/>

# Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]In diesem Artikel erfahren Sie, wie Sie Probleme beim Zugriff auf Anwendungen auf einem virtuellen Computer behandeln, der mit dem klassischen oder Ressourcen-Manager-Bereitstellungsmodell erstellt wurde.

Wenn Sie auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird, nicht zugreifen können, finden Sie in diesem Artikel einen methodischen Ansatz zum Ermitteln der Ursache des Problems und zu seiner Behebung.

> [AZURE.NOTE]Hilfeinformationen zum Herstellen einer Verbindung mit einem virtuellen Azure-Computer finden Sie unter [Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md) oder [Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-ssh-connections.md).

Die Problembehandlung beim Zugriff auf eine Anwendung, die auf einem virtuellen Azure-Computer ausgeführt wird, konzentriert sich auf vier Hauptbereiche.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access1.png)

1.	Die Anwendung, die auf dem virtuellen Azure-Computer ausgeführt wird.
2.	Den virtuellen Azure-Computer.
3.	Azure-Endpunkte für den Clouddienst, der den virtuellen Computer enthält (bei virtuellen Computern, die mithilfe der Dienstverwaltungs-API erstellt wurden), eingehende NAT-Regeln (bei virtuellen Computern, die im Ressourcen-Manager erstellt wurden) und Netzwerk-Sicherheitsgruppen.
4.	Ihre Internet-Edgegerät.

Bei Clientcomputern, die über eine Site-to-Site-VPN- oder ExpressRoute-Verbindung auf die Anwendung zugreifen, sind die Hauptbereiche, die zu Problemen führen können, die Anwendung und der virtuelle Azure-Computer. Gehen Sie folgendermaßen vor, um die Quelle des Problems und dessen Korrektur zu bestimmen.

## Schritt 1: Können Sie vom virtuellen Zielcomputer aus auf die Anwendung zugreifen?

Versuchen Sie, mit dem entsprechenden Clientprogramm von dem virtuellen Computer aus, auf dem die Anwendung ausgeführt wird, auf die Anwendung zuzugreifen. Verwenden Sie den Namen des lokalen Hosts, die lokale IP-Adresse oder die Loopbackadresse (127.0.0.1).

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access2.png)

Wenn es sich bei der Anwendung beispielsweise um einen Webserver handelt, führen Sie einen Browser auf dem virtuellen Computer aus, und versuchen Sie, auf eine Webseite zuzugreifen, die auf dem virtuellen Computer gehostet wird.

Wenn Sie auf die Anwendung zugreifen können, fahren Sie mit [Schritt 2](#step2) fort.

Wenn kein Zugriff auf die Anwendung möglich ist, überprüfen Sie Folgendes:

- Die Anwendung wird auf dem virtuellen Zielcomputer ausgeführt.
- Die Anwendung überwacht die erwarteten TCP- und UDP-Ports.

Verwenden Sie bei Windows- und Linux-basierten virtuellen Computern den Befehl **netstat -a**, um die aktiven Überwachungsports anzuzeigen. Überprüfen Sie die Ausgabe für die erwarteten Ports, die die Anwendung überwachen soll. Starten Sie die Anwendung neu, oder konfigurieren Sie sie so, dass Sie die erwarteten Ports nach Bedarf verwendet.

## <a id="step2"></a>Schritt 2: Können von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk aus auf die Anwendung zugreifen?

Versuchen Sie, von einem anderen virtuellen Computer im gleichen virtuellen Netzwerk aus, in dem sich auch der virtuelle Computer befindet, auf dem die Anwendung ausgeführt wird, mithilfe des Hostnamens des virtuellen Computers oder seiner von Azure zugewiesenen öffentlichen, privaten oder Anbieter-IP-Adresse auf die Anwendung zuzugreifen. Verwenden Sie für virtuelle Computer, die mithilfe der Dienstverwaltungs-API erstellt wurden, nicht die öffentliche IP-Adresse des Clouddiensts.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access3.png)

Wenn es sich bei der Anwendung beispielsweise um einen Webserver handelt, versuchen Sie, in einem Browser auf einem anderen virtuellen Computer im gleichen virtuellen Netzwerk auf eine Webseite zuzugreifen.

Wenn Sie auf die Anwendung zugreifen können, fahren Sie mit [Schritt 3](#step3) fort.

Wenn kein Zugriff auf die Anwendung möglich ist, überprüfen Sie Folgendes:

- In der Hostfirewall auf dem virtuellen Zielcomputer werden die eingehende Anforderung und der ausgehende Antwortdatenverkehr zugelassen.
- Software zur Netzwerküberwachung oder zur Angriffserkennung, die auf dem virtuellen Zielcomputer ausgeführt wird, lässt den Datenverkehr zu.
- Netzwerksicherheitsgruppen lassen den Datenverkehr zu.
- Eine separate Komponente, die im virtuellen Netzwerk auf dem Pfad zwischen dem virtuellen Testcomputer und dem virtuellen Computer ausgeführt wird, z. B. ein Load Balancer oder eine Firewall, lässt den Datenverkehr zu.

Verwenden Sie auf einem Windows-basierten virtuellen Computer die Windows-Firewall mit erweiterter Sicherheit, um zu bestimmen, ob die Firewallregeln den eingehenden und ausgehenden Datenverkehr der Anwendung ausschließen.

## <a id="step3"></a>Schritt 3: Können Sie von einem Computer aus, der sich außerhalb des virtuellen Netzwerks befindet, aber nicht mit demselben Netzwerk wie Ihr Computer verbunden ist, auf die Anwendung zugreifen?

Versuchen Sie, von einem Computer aus, der sich nicht im gleichen Netzwerk wie der ursprüngliche Clientcomputer und außerhalb des virtuellen Netzwerks mit dem virtuellen Computer befindet, auf dem die Anwendung ausgeführt wird, auf die Anwendung zuzugreifen.

![](./media/virtual-machines-troubleshoot-access-application/tshoot_app_access4.png)

Wenn es sich bei der Anwendung beispielsweise um einen Webserver handelt, versuchen Sie, in einem Browser auf eine Webseite zuzugreifen, der auf einem Computer ausgeführt wird, der sich nicht im virtuellen Netzwerk befindet.

Wenn kein Zugriff auf die Anwendung möglich ist, überprüfen Sie Folgendes:

- Bei virtuellen Computern, die mithilfe der Dienstverwaltungs-API erstellt wurden, muss von der Endpunktkonfiguration für den virtuellen Computer eingehender Datenverkehr, insbesondere das Protokoll (TCP oder UDP) und die öffentlichen und privaten Portnummern, zugelassen werden. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-set-up-endpoints.md).
- Bei virtuellen Computern, die mithilfe der Dienstverwaltungs-API erstellt wurden, darf eingehender Datenverkehr aus dem Internet nicht durch Zugriffssteuerungslisten (ACLs) auf dem Endpunkt verhindert werden. Weitere Informationen finden Sie unter [Einrichten von Endpunkten für einen virtuellen Computer](virtual-machines-set-up-endpoints.md).
- Bei virtuellen Computern, die im Ressourcen-Manager erstellt wurden, muss die Konfiguration der eingehenden NAT-Regel eingehenden Datenverkehr zulassen, insbesondere das Protokoll (TCP oder UDP) und die öffentlichen und privaten Portnummern.
- Netzwerksicherheitsgruppen lassen die eingehende Anforderung und den ausgehende Antwortdatenverkehr zu. Weitere Informationen finden Sie unter [Was ist eine Netzwerksicherheitsgruppe (NSG)?](virtual-networks-nsg.md).

Wenn der virtuelle Computer oder der Endpunkt Mitglied einer Gruppe mit Lastenausgleich ist:

- Stellen Sie sicher, dass der Prüfpunkt-Protokoll (TCP oder UDP) und die Portnummer richtig sind.
- Wenn das Prüfpunkt-Protokoll und der Port vom Protokoll und Port der Gruppe mit Lastenausgleich abweichen:
	- Stellen Sie sicher, dass die Anwendung das Prüfpunkt-Protokoll (TCP oder UDP) und die Portnummer überwacht. (Verwenden Sie **netstat –a** auf dem virtuellen Zielcomputer.)
	- In der Hostfirewall auf dem virtuellen Zielcomputer werden die eingehende Prüfpunkt-Anforderung und der ausgehende Prüfpunkt-Antwortdatenverkehr zugelassen.

Wenn Sie auf die Anwendung zugreifen können, stellen Sie sicher, dass Ihre Internet-Edgegeräte Folgendes zulassen:

- Den ausgehenden Anwendungsanforderungs-Datenverkehr vom Clientcomputer zum virtuellen Azure-Computer.
- Den eingehenden Anwendungs-Antwortdatenverkehr vom virtuellen Azure-Computer.

## Nächste Schritte

Wenn Sie die Schritte 1 bis 3 in diesem Artikel ausgeführt haben und zur Behebung des Problems weitere Hilfe benötigen, haben Sie folgende Möglichkeiten:

- Lassen Sie sich von Azure-Experten auf der ganzen Welt weiterhelfen. Posten Sie Ihr Problem in den MSDN Azure- oder Stack Overflow-Foren. Weitere Informationen finden Sie unter [Microsoft Azure-Foren](http://azure.microsoft.com/support/forums/).
- Erstellen Sie einen Azure-Supportfall. Klicken Sie auf der [Azure-Support-Website](http://azure.microsoft.com/support/options/) unter **Technischer und Abrechnungssupport** auf **Support erhalten**.

## Zusätzliche Ressourcen

[Problembehandlung bei Remotedesktopverbindungen mit einem Windows-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-remote-desktop-connections.md)

[Behandeln von Problemen mit Secure Shell (SSH)-Verbindungen mit einem Linux-basierten virtuellen Azure-Computer](virtual-machines-troubleshoot-ssh-connections.md)

<!---HONumber=Oct15_HO2-->