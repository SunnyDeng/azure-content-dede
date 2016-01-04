<properties
	pageTitle="Schützen von Active Directory und DNS mit Azure Site Recovery | Microsoft Azure" 
	description="Dieser Artikel beschreibt das Implementieren einer Notfallwiederherstellungs-Lösung für Active Directory mit Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="12/14/2015" 
	ms.author="pratshar"/>

# Schützen von Active Directory und DNS mit Azure Site Recovery

Unternehmensanwendungen, z. B. SharePoint, Dynamics AX und SAP, basieren auf einer AD- und DNS-Infrastruktur, um richtig funktionieren zu können. Wenn Sie eine Notfallwiederherstellungs-Lösung für Anwendungen erstellen, müssen Sie daran denken, dass Sie Active Directory und DNS vorrangig vor den anderen Anwendungskomponenten schützen und wiederherstellen müssen, um sicherzustellen, dass Vorgänge richtig ablaufen, wenn ein Notfall eintritt.

Site Recovery ist ein Azure-Dienst, der Notfallwiederherstellung durch Koordinierung von Replikation, Failover und Wiederherstellung virtueller Computer bietet. Site Recovery unterstützt eine Reihe von Replikationsszenarien, um für virtuelle Computer und Anwendungen konsistenten Schutz und ein reibungsloses Failover in privaten/öffentlichen Clouds bzw. in Clouds von Hostern zu bieten.

Mit Site Recovery können Sie für Active Directory einen vollständig automatisierten Notfallwiederherstellungs-Plan erstellen. Wenn eine Störung eintritt, können Sie ein Failover von überall aus in Sekundenschnelle einleiten und Active Directory binnen weniger Minuten wieder in Betrieb nehmen. Wenn Sie an Ihrem primären Standort Active Directory für mehrere Anwendungen wie SharePoint und SAP nutzen und für den gesamten Standort ein Failover wünschen, können Sie zunächst für Active Directory ein Failover mit Site Recovery und anschließend für die anderen Anwendungen ein Failover mithilfe anwendungsspezifischer Wiederherstellungspläne ausführen.

In diesem Artikel wird erläutert, wie Sie eine Lösung für die Notfallwiederherstellung für Active Directory erstellen und ein geplantes/ungeplantes oder Testfailover mithilfe eines mit einem Klick aktivierbaren Wiederherstellungsplans, unterstützter Konfigurationen und erfüllter Voraussetzungen ausführen. Sie sollten mit Active Directory und Azure Site Recovery vertraut sein, bevor Sie beginnen.

Es gibt zwei empfohlene Methoden auf Grundlage der Komplexität Ihrer Umgebung.

### Option 1:

Wenn nur eine geringe Anzahl von Anwendungen und ein einzelner Domänencontroller vorhanden sind, und Sie ein Failover für den gesamten Standort ausführen möchten, empfiehlt es sich, den Domänencontroller mit Site Recovery zum sekundären Standort zu replizieren (dabei spielt es keine Rolle, ob Sie ein Failover zu Azure oder einem sekundären Standort ausführen). Der gleiche replizierte virtuelle Computer kann auch für ein Testfailover verwendet werden.

### Option 2:

Bei einer großen Anzahl von Anwendungen und mehreren Domänencontrollern in der Umgebung, oder wenn Sie das gleichzeitige Failover verschiedener Anwendungen planen, empfehlen wir Ihnen, zusätzlich zum Replizieren des virtuellen Computers, auf dem sich der Domänencontroller befindet, mit Site Recovery auch die Einrichtung eines zusätzlichen Domänencontrollers am Zielstandort (Azure oder ein sekundäres lokales Datencenter).

>[AZURE.NOTE]Auch wenn Sie Option-2 implementieren, müssen Sie für ein Testfailover dennoch den Domänencontroller mit Site Recovery replizieren. Weitere Informationen finden Sie unter [Überlegungen zum Test-Failover](#considerations-for-test-failover).


In den folgenden Abschnitten wird erläutert, wie der Schutz für einen Domänencontroller in Site Recovery aktiviert und ein Domänencontroller in Azure eingerichtet wird.


## Voraussetzungen

- Active Directory- und DNS-Server sind lokal bereitgestellt.
- Ein Azure Site Recovery Services-Tresor im Microsoft Azure-Abonnement. 
- Falls Sie zu Azure replizieren, führen Sie das Tool „Azure Virtual Machine Readiness Assessment“ auf VMs aus, um sicherzustellen, dass diese mit Azure-VMs und Azure Site Recovery Services kompatibel sind.


## Aktivieren des Schutzes mit Site Recovery


### Schützen des virtuellen Computers

Aktivieren Sie den Schutz von Domänencontroller/DNS-VM in Site Recovery. Konfigurieren Sie Site Recovery-Einstellungen auf Basis des Typs des virtuellen Computers (Hyper-V oder VMware). Sie sollten alle 15 Minuten eine absturzkonsistente Replikation ausführen.

###Konfigurieren der Netzwerkeinstellungen virtueller Computer

Konfigurieren Sie für den virtuellen Computer mit dem Domänencontroller bzw. DNS die Netzwerkeinstellungen in Site Recovery so, dass der virtuelle Computer nach einem Failover dem richtigen Netzwerk zugeordnet wird. Wenn Sie z. B. Hyper-V-VMs zu Azure replizieren, können Sie den virtuellen Computer in der VMM-Cloud oder Schutzgruppe auswählen und die Netzwerkeinstellungen wie im Folgenden gezeigt konfigurieren.

![VM-Netzwerkeinstellungen](./media/site-recovery-active-directory/VM-Network-Settings.png)

## Schützen von Active Directory mit Active Directory-Replikation 

### Standort-zu-Standort-Schutz

Erstellen Sie einen Domänencontroller am sekundären Standort, und geben Sie dem Server beim Heraufstufen auf die Rolle eines Domänencontrollers den Namen der Domäne, die am primären Standort verwendet wird. Sie können das Snap-In **Active Directory-Standorte und Dienste** zum Konfigurieren von Einstellungen für das Standortverknüpfungsobjekt nutzen, dem die Standorte hinzugefügt werden. Durch Konfigurieren von Einstellungen für eine Standortverknüpfung können Sie steuern, wann und wie oft eine Replikation zwischen zwei oder mehr Standorten erfolgt. Unter [Planen der Replikation zwischen Standorten](https://technet.microsoft.com/library/cc731862.aspx) finden Sie weitere Details.

###Standort-zu-Azure-Schutz

Befolgen Sie zum [Erstellen eines Domänencontrollers in einem virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md) diese Anweisungen. Geben Sie beim Heraufstufen des Servers auf eine Domänencontrollerrolle den gleichen Domänennamen an, der am primären Standort verwendet wird.

Dann [konfigurieren Sie den DNS-Server für das virtuelle Netzwerk neu](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), um den DNS-Server in Azure zu verwenden.
  
![Azure-Netzwerk](./media/site-recovery-active-directory/azure-network.png)

## Überlegungen zum Test-Failover

Das Testfailover erfolgt in einem Netzwerk, das vom Produktionsnetzwerk isoliert ist, damit keine Auswirkungen auf Workloads in der Produktion auftreten.

Die meisten Anwendungen sind auch auf einen funktionierenden Domänencontroller und DNS-Server angewiesen, sodass vor dem Failover der Anwendung im isolierten Netzwerk ein Domänencontroller für das Testfailover erstellt werden muss. Die einfachste Möglichkeit hierzu ist das Aktivieren des Schutzes auf dem virtuellen Computer mit dem Domänencontroller bzw. DNS mit Site Recovery. Anschließend muss das Testfailover dieses virtuellen Computer ausgeführt werden, ehe das Testfailover des Wiederherstellungsplans der Anwendung erfolgt. Gehen Sie hierzu wie folgt vor:

1. Aktivieren Sie in Site Recovery den Schutz von Domänencontroller/DNS-VM.
2. Erstellen Sie ein isoliertes Netzwerk. Jedes in Azure erstellte virtuelle Netzwerk ist standardmäßig von anderen Netzwerken isoliert. Der IP-Adressbereich dieses Netzwerks sollte mit dem Ihres Produktionsnetzwerks identisch sein. Aktivieren Sie nicht die Standort-zu-Standort-Konnektivität in diesem Netzwerk.
3. Stellen Sie eine im Netzwerk erstellte DNS-IP-Adresse als die IP-Adresse bereit, die der virtuelle DNS-Computer abrufen soll. Wenn Sie zu Azure replizieren, geben Sie die IP-Adresse für die VM, die beim Failover verwendet wird, in den VM-Eigenschaften in der Einstellung **Ziel-IP-Adresse** an. Wenn Sie zu einem anderen lokalen Standort replizieren und DHCP verwenden, befolgen Sie die Anweisungen zum [Einrichten von DNS und DHCP für das Testfailover](site-recovery-failover.md#prepare-dhcp). 

>[AZURE.NOTE]Die IP-Adresse, die einem virtuellen Computer während eines Testfailovers zugeordnet wird, entspricht der IP-Adresse, die dieser bei einem geplanten oder ungeplanten Failover erhalten würde, sofern die IP-Adresse im Testfailover-Netzwerk verfügbar ist. Wenn dies nicht der Fall ist, empfängt der virtuelle Computer eine andere IP-Adresse, die im Testfailover-Netzwerk verfügbar ist.

4. Führen Sie auf dem virtuellen Computer mit dem Domänencontroller ein Testfailover im isolierten Netzwerk aus. 
5. Führen Sie ein Testfailover für den Anwendungswiederherstellungsplan aus.
6. Markieren Sie nach Abschluss des Tests das Testfailover des Auftrags für den virtuellen Computer mit dem Domänencontroller und den Wiederherstellungsplan im Site Recovery-Portal auf der Registerkarte **Aufträge** als „Abgeschlossen“. 

### DNS und Domänencontroller auf unterschiedlichen Computern
 
Wenn der DNS sich nicht auf dem gleichen virtuellen Computer wie der Domänencontroller befindet, müssen Sie eine DNS-VM für das Testfailover erstellen. Falls sich beide auf dem gleichen virtuellen Computer befinden, können Sie diesen Abschnitt überspringen.

Sie können einen neuen DNS-Server verwenden und alle erforderlichen Zonen erstellen. Wenn Ihre Active Directory-Domäne beispielsweise „contoso.com“ lautet, können Sie eine DNS-Zone mit dem Namen „contoso.com“ erstellen. Die zu Active Directory gehörenden Einträge müssen wie folgt in DNS aktualisiert werden:

1. Stellen Sie sicher, dass diese Einstellungen vorhanden sind, bevor ein anderer virtueller Computer im Wiederherstellungsplan online geschaltet wird:

	- Die Zone muss nach dem Stammnamen der Gesamtstruktur benannt werden.
	- Der Zone muss eine Datei zugrunde liegen.
	- Für die Zone müssen sichere und nicht sichere Updates möglich sein.
	- Die Auflösung des virtuellen Computers mit dem Domänencontroller muss auf die IP-Adresse des virtuellen Computers mit DNS zeigen.

2. Führen Sie den folgenden Befehl auf dem virtuellen Computer mit dem Domänencontroller aus:

	`nltest /dsregdns`

3. Fügen Sie dem DNS-Server eine Zone hinzu, erlauben Sie nicht sichere Updates, und fügen Sie einen Eintrag dafür dem DNS hinzu:

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


## Nächste Schritte

Lesen Sie [Welche Workloads können mit Azure Site Recovery geschützt werden?](../site-recovery/site-recovery-workload.md), um weitere Informationen über den Schutz von Unternehmensworkloads mit Azure Site Recovery zu erhalten.

<!---HONumber=AcomDC_1217_2015-->