<properties
	pageTitle="Bewährte Methoden für die Site Recovery-Bereitstellung"
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung virtueller Computer auf lokalen Servern zu Azure oder einem sekundären Rechenzentrum."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="05/08/2015"
	ms.author="raynew"/>

# Bewährte Methoden für die Site Recovery-Bereitstellung


<a id="overview" name="overview" href="#overview"></a>

## Informationen zum Artikel

Der Artikel enthält bewährte Methoden, die Sie vor dem Bereitstellen von Azure Site Recovery lesen und implementieren sollten. Eine Einführung in Site Recovery und dazugehörige Bereitstellungsszenarien finden Sie unter [Übersicht über Site Recovery](site-recovery-overview.md).

Sollten Sie nach der Lektüre dieses Artikels noch Fragen haben, stellen Sie diese bitte im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Vorbereiten von Azure

- **Azure-Konto**: Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Beginnen Sie mit einer [kostenlosen Testversion](pricing/free-trial/), falls Sie kein Konto haben.
- Erfahren Sie mehr über die [Preise](pricing/details/site-recovery/) für Site Recovery. 
- **Azure-Speicher**: Wenn Sie Site Recovery mit Replikation zu Azure bereitstellen, benötigen Sie ein Azure-Speicherkonto. Sie können ein Konto bei der Bereitstellung einrichten oder für das Vorhandensein sorgen, bevor Sie beginnen. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Tresor befinden und dem gleichen Abonnement zugeordnet sein. Lesen Sie die Informationen unter [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md). 

## Virtuelle Computer

Beachten Sie Folgendes, wenn Sie die Replikation in den Azure-Speicher durchführen möchten:

- **Unterstütztes Betriebssystem**: Sie können Site Recovery bereitstellen, um den Schutz von virtuellen Computern mit jedem Betriebssystem einzurichten, das von Azure unterstützt wird. Dies gilt für die meisten Versionen von Windows und Linux.
- **VHDX-Unterstützung**: Obwohl VHDX in Azure derzeit nicht unterstützt wird, führt Site Recovery automatisch die Konvertierung von VHDX in VHD durch, wenn Sie das Failover zu Azure anstoßen. Wenn Sie das Failover in den lokalen Speicher durchführen, wird für die virtuellen Computer weiterhin das VHDX-Format verwendet.
- **Azure-Anforderungen**: Stellen Sie sicher, dass alle zu schützenden virtuellen Computer die Azure-Anforderungen erfüllen.

**Feature für virtuelle Computer** | **Unterstützung** | **Details**
---|---|---
Hostbetriebssystem | Windows Server 2012 R2 | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Gastbetriebssystem | <p>Windows Server 2008 R2 oder höher</p><p>Linux: Centos, openSUSE, SUSE, Ubuntu</p> | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist. Aktualisieren Sie den Wert in der VMM-Konsole.
Architektur des Gastbetriebssystems | 64 Bit | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des Betriebssystem-Datenträgers | Maximal 1.023 GB | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Anzahl von Betriebssystem-Datenträgern | 1 | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist. Aktualisieren Sie den Wert in der VMM-Konsole.
Anzahl von Datenträgern für Daten | Maximal 16 (der Höchstwert ist eine Funktion der Größe des erstellten virtuellen Computers: 16 = XL) | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Größe des VHD-Datenträgers | Maximal 1.023 GB | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Netzwerkadapter | Es werden mehrere Adapter unterstützt. |
Statische IP-Adresse | Unterstützt | Wenn für den primären virtuellen Computer eine statische IP-Adresse verwendet wird, können Sie die statische IP-Adresse für den virtuellen Computer angeben, der unter Azure erstellt wird.
iSCSI-Datenträger | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Freigegebene VHD | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Fiber-Channel-Datenträger | Nicht unterstützt | Für die Überprüfung der Voraussetzungen tritt ein Fehler auf, wenn keine Unterstützung vorhanden ist.
Festplattenformat| <p>VHD</p><p>VHDX</p> |
Name des virtuellen Computers| Zwischen 1 und 63 Zeichen. Ist auf Buchstaben, Zahlen und Bindestriche beschränkt. Muss mit einem Buchstaben oder einer Zahl beginnen und enden. | Aktualisieren Sie den Wert in den Eigenschaften für virtuelle Computer in Site Recovery.
Typ des virtuellen Computers | <p>Generation 1</p> <p>Generation 2 – Windows</p> | Virtuelle Maschine der 2. Generation mit Betriebssystem-Datenträgertyp Basisfestplatte enthält 1 oder 2 Datenvolumes mit dem Datenträgerformat VHDX, das maximal 300 GB unterstützt. Virtuelle Computer der 2. Generation mit Linux werden nicht unterstützt. [Weitere Informationen](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) 


## VMM-Server

Mit Site Recovery kann die Replikation für virtuelle Computer, die sich auf Hyper-V-Hostservern in System Center Virtual Machine Manager (VMM)-Clouds befinden, wie folgt gesteuert werden: - Replizieren von einem lokalen VMM-Server zu Azure (per Hyper-V-Replikat) - Replizieren zu einem sekundären lokalen Standort (per Hyper-V-Replikat). Wir empfehlen Ihnen, die Bereitstellung mit einem VMM-Server am primären Standort und einem VMM-Server am sekundären Standort durchzuführen. Falls erforderlich, können Sie aber auch für beide Standorte [nur einen VMM-Server bereitstellen](site-recovery-single-vmm.md). - Replizieren zu einem sekundären lokalen Standort (per SAN). Sie benötigen an jedem Standort ein primäres und sekundäres Rechenzentrum mit einem VMM-Server. Wenn Sie VMM mit Site Recovery bereitstellen möchten, müssen Sie Ihre VMM-Infrastruktur einrichten. Lesen Sie [diese Informationen ](site-recovery-hyper-v-site-to-azure.md), wenn Sie keinen VMM-Server haben.


### Überprüfen der VMM-Version

**Unterstützte VMM-Version** | **Unterstütztes Site Recovery-Szenario** | **Unterstützter Hyper-V-Host (Quelle/Ziel)** | **Unterstützter Anbieter/Agent**
---|---|---|---
VMM unter System Center 2012 R2 und mindestens Update Rollup 5 (nur virtuell im Cluster) | Schutz lokal zu lokal mit SAN-Replikation | Mindestens Windows Server 2012 mit aktuellen Updates | Neueste Version von Azure Site Recovery-Anbieter (Installation auf VMM-Servern) |
VMM unter System Center 2012 R2 (empfohlen) (Cluster oder eigenständig) | <p>Schutz von lokal zu lokal mit Hyper-V-Replikation</p> <p>Schutz von lokal zu Azure mit Hyper-V-Replikation</p> | <p>Mindestens Windows Server 2012 mit aktuellen Updates</p><p>Windows Server 2012 R2 (Quelle, für Ziel nicht zutreffend)</p> | <p>Aktuelle Version des Azure Site Recovery-Anbieters (Installation auf VMM-Servern)</p> <p>Aktuelle Version des Azure Recovery-Dienst-Agents (Installation auf Hyper-V-Hostservern für die ausschließliche Replikation zu Azure)</p>
VMM unter System Center 2012 SP1 mit dem aktuellen kumulativen Update (Cluster oder eigenständig) | Schutz von lokal zu lokal | Windows Server 2012 mit aktuellen Updates | Neueste Version von Azure Site Recovery-Anbieter (Installation auf VMM-Servern) |

### Einrichten der VMM-Cloudinfrastruktur

Es spielt keine Rolle, welches VMM-Szenario Sie mit Site Recovery bereitstellen möchten: Sie müssen mindestens zwei Private Clouds (eine auf dem VMM-Ausgangsserver und eine auf dem Zielserver) in VMM erstellen, um die Vorteile des Private Cloud-Modells nutzen zu können. Sie verwenden die VMM-Konsole, um die Cloud und die zugrunde liegenden Ressourcen zu definieren, zu verwalten und darauf zuzugreifen. In einer VMM-Cloud wird eine Zusammenfassung der Ressourcen erstellt und präsentiert. Die Nutzung unterliegt Beschränkungen in Bezug auf die Einstellungen der Cloudkapazität und die Benutzerrollenkontingente. Self-Service-Benutzer können die Ressourcen der Cloud verwalten und verwenden, ohne eingehend mit der zugrunde liegenden Infrastruktur vertraut zu sein. Ressourcen können einfach hinzugefügt werden, um die Cloud flexibel anzupassen. Verwenden Sie die folgenden Ressourcen, wenn Sie Clouds einrichten müssen:


- [Private Cloud-Neuerungen mit System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)
- [VMM 2012 und die Clouds](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html)
- [Konfigurieren der VMM-Cloud-Fabric](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
- [Erstellen einer Private Cloud in VMM](https://technet.microsoft.com/de-de/library/jj860425.aspx)
- [Exemplarische Vorgehensweise: Erstellen von privaten Clouds mit System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx)




## Anbieter und Agents

Anbieter und Agents werden auf lokalen Servern installiert, damit darüber Verbindungen mit Site Recovery in Azure hergestellt werden können. Die Verbindungen werden über das Internet als verschlüsselte HTTPS-Verbindung hergestellt.

- **Anbieteranforderungen**: Während der Bereitstellung installieren Sie einige Komponenten auf Ihren lokalen Servern:
	- **Auf VMM-Servern**: Sie installieren den Azure Site Recovery-Anbieter auf den VMM-Servern, die Sie im Tresor registrieren möchten.
	- **Auf Hyper-V-Hostservern in einer VMM-Cloud**: Sie installieren den Azure Recovery Services-Agent.
	- **Auf Hyper-V-Hostservern ohne VMM-Server**: Sie installieren den Azure Site Recovery-Anbieter und den Azure Recovery Services-Agent auf jedem Hyper-V-Hostserver oder Clusterknoten.
-  **Anbieterinstallation**: Beachten Sie beim Installieren von Anbietern und Agents Folgendes:
	-  Sie sollten auf allen Servern eines Site Recovery-Tresors die gleiche Version des Anbieters installieren. Die Verwendung von unterschiedlichen Versionen in einem Tresor wird nicht unterstützt.
	-  Wenn Sie von Azure zu Hyper-V-Servern replizieren möchten, die sich in einer VMM-Cloud befinden, sollte auf dem VMM-Server System Center 2012 R2 ausgeführt werden. Wenn Sie die Replikation zu einem sekundären Rechenzentrum durchführen, sollte unter VMM System Center 2012 mit SP1 oder R2 ausgeführt werden.
- **Proxy**: Sie müssen keine Firewallausnahmen hinzufügen und keinen speziellen Proxy erstellen. Wenn Sie einen benutzerdefinierten Proxy für die Anbieterverbindung verwenden möchten, gehen Sie vor Beginn der Bereitstellung wie folgt vor:

	- Richten Sie den benutzerdefinierten Proxyserver ein, bevor Sie den Anbieter installieren.
	- Lassen Sie die folgenden URLs für die Firewall zu:
		- *.hypervrecoverymanager.windowsazure.com - *.accesscontrol.windows.net - *.backup.windowsazure.com - *.blob.core.windows.net - *.store.core.windows.net 
	- Wenn Sie Site Recovery mit VMM bereitstellen und einen benutzerdefinierten Proxy verwenden, wird automatisch ein VMM-RunAs-Konto (DRAProxyAccount) erstellt. Hierfür werden die Proxyanmeldeinformationen genutzt, die Sie im Site Recovery-Portal in den Einstellungen für den benutzerdefinierten Proxy angegeben haben. Sie müssen den Proxyserver so konfigurieren, dass für dieses Konto eine erfolgreiche Authentifizierung möglich ist.



## Lokale Konnektivität

- **Anbieterkonnektivität**: Anbieter und Agents werden auf lokalen Servern installiert, damit darüber Verbindungen mit Site Recovery hergestellt werden können. Die Verbindungen mit Site Recovery werden über das Internet als verschlüsselte HTTPS-Verbindung hergestellt. Sie müssen keine Firewallausnahmen hinzufügen und keinen speziellen Proxy erstellen.
- **Internetverbindung**: Sie benötigen eine Serverinternetverbindung, für die Folgendes erfüllt ist:
	- Wenn Sie virtuelle Computer auf Hyper-V-Hostservern in einer VMM-Cloud schützen, benötigt nur der VMM-Server eine Internetverbindung.
	- Wenn Sie virtuelle Computer auf Hyper-V-Hostservern ohne VMM-Server schützen, benötigen nur die Hyper-V-Hostserver eine Internetverbindung.
	- Von den virtuellen Computern, die Sie schützen möchten, ist keine Internetkonnektivität erforderlich.
- **Von VPN-Standort zu VPN-Standort**: Für die Verbindungsherstellung mit Site Recovery benötigen Sie keine Verbindung von VPN-Standort zu VPN-Standort. Wenn Sie über eine Verbindung von Standort zu Standort verfügen, können Sie auf virtuelle Computer, für die ein Failover durchgeführt wurde, genauso wie vor dem Failover zugreifen. Beachten Sie, dass Sie beim Replizieren zu Azure ein VPN-Netzwerk mit Verbindung von Standort zu Standort zwischen Ihrem lokalen Standort und einem speziellen Azure-Netzwerk einrichten. Es wird nicht für verschlüsselten Replikationsdatenverkehr genutzt. Die Verbindung erfolgt über das Internet zum Azure-Speicherkonto in Ihrem Abonnement.
- **Konnektivität nach einem Failover**: Gehen Sie wie folgt vor, um sicherzustellen, dass Benutzer nach einem Failover zu Azure eine Verbindung mit den virtuellen Computern herstellen können:
	- Aktivieren Sie vor dem Failover das Remotedesktopprotokoll (RDP) für die virtuellen Computer.
	- Verwenden Sie nach dem Failover eine Verbindung von Standort zu Standort, damit Sie wie vorher eine Verbindung damit herstellen können, oder aktivieren Sie den RDP-Endpunkt für den Computer.

## Storage

- **Azure-Speicherkonto**: Wenn Sie die Replikation zu Azure durchführen, benötigen Sie ein Azure-Speicherkonto. Für das Konto muss Georeplikation aktiviert sein. Es muss sich in der gleichen Region wie der Azure Site Recovery-Tresor befinden und dem gleichen Abonnement zugeordnet sein. Weitere Informationen finden Sie unter [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md).
- **Speicherzuordnung**: Wenn Sie virtuelle Computer auf lokalen VMM-Servern replizieren, können Sie mit der Speicherzuordnung sicherstellen, dass virtuelle Computer nach dem Failover optimal mit dem Speicher verbunden sind. Wenn Sie zwischen zwei lokalen VMM-Standorten replizieren, wird der virtuelle Replikatcomputer standardmäßig an dem Ort gespeichert, der auf dem Hyper-V-Hostzielserver angegeben ist. Sie können die Zuordnung zwischen VMM-Speicherklassifizierungen auf VMM-Ausgangs- und -Zielservern konfigurieren. Stellen Sie bei Verwendung dieses Features sicher, dass Sie die Speicherklassifizierungen einrichten, bevor Sie mit der Bereitstellung beginnen. [Erfahren Sie mehr](site-recovery-storage-mapping.md) zur Speicherzuordnung.
- **SAN**: Beachten Sie Folgendes, wenn Sie eine Replikation zwischen zwei lokalen Standorten per SAN-Replikation durchführen möchten:
	- Sie können virtuelle Hyper-V-Computer nur per SAN-Replikation zu einem sekundären Rechenzentrum replizieren. Sie können nicht zu Azure replizieren.
	- Hierfür können Sie Ihre vorhandene SAN-Umgebung verwenden. Auf SAN-Arrays müssen keine Änderungen vorgenommen werden.
	- Sie müssen überprüfen, ob Ihr SAN-Array [unterstützt](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) wird.
	- Sie benötigen für die SAN-Bereitstellung zwei VMM-Server (auf dem Quell- und Zielstandort).
	- Achten Sie darauf, dass auf den Hyper-V-Hostclustern Windows Server 2012 oder 2012 R2 ausgeführt wird, wenn Sie die SAN-Replikation bereitstellen möchten. [Erfahren Sie mehr](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) über die Gastbetriebssysteme, die von den unterschiedlichen Versionen von Hyper-V unterstützt werden.
	- Sie müssen die SAN-Speicherung in VMM ermitteln und klassifizieren.
	- Falls Sie die Replikation noch nicht durchführen, müssen Sie nach der Ermittlung LUNs erstellen und in der VMM-Konsole Speicher zuordnen. Falls Sie bereits replizieren, können Sie diesen Schritt überspringen.
	- Dieser [Artikel](site-recovery-vmm-san.md) enthält alle Anweisungen.


## Netzwerke

- **Informationen zu Netzwerkaspekten**: Hier erhalten Sie [weitere Informationen](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) zu Netzwerkaspekten und den bewährten Methoden.

- **Einrichten der Netzwerkzuordnung**: Die Netzwerkzuordnung ist ein wichtiges Element bei der Bereitstellung von VMM und Site Recovery. Hierbei werden replizierte virtuelle Computer optimal auf Hyper-V-Zielhostservern platziert. Außerdem wird sichergestellt, dass Ihre replizierten virtuellen Computer nach dem Failover mit den richtigen Netzwerken verbunden sind. Sie können die Netzwerkzuordnung konfigurieren, wenn Sie zu Azure oder zu einem sekundären Rechenzentrum replizieren:
	- **Netzwerkzuordnung zu Azure**: Beim Replizieren zu Azure wird mit der Netzwerkzuordnung dafür gesorgt, dass alle Computer, für die in einem Netzwerk ein Failover durchgeführt wird, miteinander eine Verbindung herstellen können. Dabei spielt es keine Rolle, welcher Wiederherstellungsplan für die Computer jeweils gilt. Wenn im Azure-Zielnetzwerk ein Netzwerkgateway konfiguriert wurde, können die virtuellen Computer auch eine Verbindung mit anderen lokalen virtuellen Computern herstellen. Wenn Sie keine Netzwerkzuordnung einrichten, können nur Computer Verbindungen miteinander herstellen, für die das Failover in demselben Wiederherstellungsplan festgelegt ist.
	- **Netzwerkzuordnung zum sekundären Standort**: Stellen Sie beim Replizieren zur Netzwerkzuordnung eines sekundären VMM-Standorts sicher, dass virtuelle Computer nach dem Failover mit den richtigen Netzwerken verbunden sind und dass virtuelle Replikatcomputer optimal auf Hyper-V-Hostservern platziert sind. Wenn Sie die Netzwerkzuordnung nicht konfigurieren, werden für replizierte Computer keine Verbindungen mit einem VM-Netzwerk hergestellt.
	- [Erfahren Sie mehr](site-recovery-network-mapping) zur Netzwerkzuordnung.
- **Einrichten von VMM-Netzwerken**:
	- Konfigurieren Sie logische Netzwerke und VM-Netzwerke richtig in VMM. Erfahren Sie mehr über [logische Netzwerke](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) und [VM-Netzwerke](https://technet.microsoft.com/library/jj721575.aspx).
	- Stellen Sie sicher, dass alle virtuellen Computer auf dem VMM-Quellserver mit einem VM-Netzwerk verbunden sind.
	- Überprüfen Sie, ob die VM-Netzwerke mit einem logischen Netzwerk verknüpft sind, die der Cloud zugeordnet sind.
	- Erstellen Sie virtuelle Netzwerke in Azure, wenn Sie die Replikation zu Azure durchführen. Beachten Sie, dass einem einzelnen Azure-Netzwerk mehrere VM-Netzwerke zugeordnet werden können. Erfahren Sie mehr über [Konfigurationsaufgaben für virtuelle Netzwerke](https://msdn.microsoft.com/library/azure/dn133795.aspx).

## Optimieren der Leistung

- **Volumegröße des Betriebssystems**: Wenn Sie einen virtuellen Computer zu Azure replizieren, muss das Volume des Betriebssystems kleiner als 127 GB sein. Falls Sie mehr als ein Volume nutzen, können Sie diese manuell auf einen anderen Datenträger verschieben, bevor Sie mit der Bereitstellung beginnen.
- **Größe des Datenträgers für Daten**: Wenn Sie zu Azure replizieren, können Sie auf einem virtuellen Computer bis zu 32 Datenträger für Daten nutzen, die jeweils maximal 1 TB groß sind. Für einen virtuellen Computer mit ~32 TB können Sie die Replikation und das Failover effektiv durchführen.
- **Beschränkungen des Wiederherstellungsplans**: Für Site Recovery ist eine Skalierung auf Tausende von virtuellen Computern möglich. Wiederherstellungspläne wurden als Modell für Anwendungen entwickelt, für die das Failover zusammen erfolgen soll. Die Anzahl von Computern in einem Wiederherstellungsplan wird daher auf 50 beschränkt.
- **Beschränkungen für Azure-Dienste**: Jedes Azure-Abonnement verfügt über eine Reihe von standardmäßigen Beschränkungen für Kerne, Clouddienste usw. Wir empfehlen Ihnen, ein Testfailover auszuführen, um die Verfügbarkeit von Ressourcen in Ihrem Abonnement zu überprüfen. Sie können diese Beschränkungen über den Azure-Support ändern.
- **Kapazitätsplanung**: Verwenden Sie als Anleitung den [Kapazitätsplaner für Hyper-V-Replikate](http://www.microsoft.com/de-de/download/details.aspx?id=39057).
- **Replikationsbandbreite**: Beachten Sie Folgendes, falls die Replikationsbandbreite knapp ist:
	- **ExpressRoute**: Für Site Recovery können Azure ExpressRoute und WAN-Optimierer wie Riverbed verwendet werden. [Erfahren Sie mehr](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) zu ExpressRoute.
	- **Replikationsdatenverkehr**: Site Recovery führt eine intelligente anfängliche Replikation durch, bei der nur Datenblöcke verwendet werden, und nicht die gesamte VHD. Während der laufenden Replikation werden nur Änderungen repliziert.
	- **Netzwerkdatenverkehr**: Sie können Netzwerkdatenverkehr steuern, der für die Replikation verwendet wird, indem Sie [Windows-Dienstqualität](https://technet.microsoft.com/library/hh967468.aspx) mit einer Richtlinie nutzen, die auf der IP-Zieladresse und dem Port basiert. Falls Sie die Replikation zu Azure Site Recovery mit dem Azure Backup-Agent durchführen, können Sie die Drosselung für den Agent konfigurieren. [Weitere Informationen](https://support.microsoft.com/kb/3056159)
- **RTO**: Wenn Sie die Recovery Time Objective (RTO) messen möchten, die mit Site Recovery zu erwarten ist, sollten Sie ein Testfailover ausführen und die Site Recovery-Aufträge anzeigen, um zu analysieren, wie lange die Durchführung der Vorgänge dauert. Falls Sie das Failover zu Azure nutzen, erzielen Sie den besten RTO-Wert, wenn Sie alle manuellen Aktionen automatisieren. Integrieren Sie hierfür die Azure-Automatisierung und Wiederherstellungspläne.
- **RPO**: Site Recovery unterstützt beim Replizieren zu Azure eine nahezu synchrone Recovery Point Objective (RPO). So wird für eine ausreichende Bandbreite zwischen Ihrem Rechenzentrum und Azure gesorgt.

## Failover
- **Ausfall auf primärem Element**: Wenn Sie eine Replikation von einem lokalen Rechenzentrum zu einem anderen lokalen Rechenzentrum durchführen und es für beide Rechenzentren an Ihrem primären Standort zu einem Ausfall kommt, sollten Sie ein ungeplantes Failover über das Site Recovery-Portal ausführen. Zum Ausführen des Failovers ist keine Konnektivität vom primären Rechenzentrum erforderlich.
- **Beibehalten der IP-Adresse nach dem Failover auf den sekundären Standort**: Führen Sie [diese Schritte](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx) aus, wenn Sie die IP-Adresse eines virtuellen Quellcomputers beibehalten möchten, nachdem dafür das Failover zu einem sekundären Rechenzentrum erfolgt ist.
- **Beibehalten der IP-Adresse nach einem Failover zu Azure**: Sie können die IP, die für virtuelle Computer mit Failover vergeben werden soll, auf der Registerkarte „Konfigurieren“ des virtuellen Computers angeben. Weitere Informationen finden Sie unter [Konfigurieren von Netzwerkeigenschaften des virtuellen Computers](site-recovery-vmm-to-azure.md#step-8-enable-protection-for-virtual-machines).
- **Beibehalten der öffentlichen IP-Adresse**: Wenn Sie eine öffentliche IP-Adresse nach dem Failover zu einem sekundären Standort beibehalten möchten, werden Sie von Site Recovery nicht daran gehindert, sofern Ihr Internetdienstanbieter dies unterstützt. Sie können eine öffentliche IP-Adresse nach dem Failover zu Azure nicht beibehalten.
- **Beibehalten von anderen internen Adressen als RFC-Adressen in Azure**: Sie können nach dem Failover zu Azure andere Adressen als RFC 1918-Adressräume beibehalten.
- **Teilfailover zu einem sekundären Rechenzentrum**: Wenn Sie für einen Teil eines Standorts ein Failover zu Ihrem sekundären Rechenzentrum durchführen und eine Verbindung mit dem primären Standort herstellen möchten, können Sie eine VPN-Verbindung von Standort zu Standort verwenden. Hiermit können Sie eine Anwendung am sekundären Standort, für die ein Failover erfolgt ist, mit Infrastrukturkomponenten verbinden, die am primären Standort ausgeführt werden. Beachten Sie Folgendes: Wenn für das gesamte Subnetz ein Failover auftritt, können Sie die IP-Adresse des virtuellen Computers beibehalten. Wenn Sie ein Failover für einen Teil eines Subnetzes durchführen, können Sie die IP-Adresse des virtuellen Computers nicht beibehalten, da Subnetze nicht auf mehrere Standorte aufgeteilt werden können.
- **Teilfailover zu Azure**: Wenn Sie für einen Teil eines Standorts ein Failover zu Azure durchführen und wieder eine Verbindung mit dem primären Standort herstellen möchten, können Sie eine VPN-Verbindung zwischen den Standorten verwenden, um eine Verbindung zwischen einer Failoveranwendung in Azure und Infrastrukturkomponenten am primären Standort herzustellen. Beachten Sie Folgendes: Wenn für das gesamte Subnetz ein Failover auftritt, können Sie die IP-Adresse des virtuellen Computers beibehalten. Wenn Sie ein Failover für einen Teil eines Subnetzes durchführen, können Sie die IP-Adresse des virtuellen Computers nicht beibehalten, da Subnetze nicht auf mehrere Standorte aufgeteilt werden können.
- **Beibehalten des Laufwerkbuchstaben**: Wenn Sie nach dem Failover den Laufwerkbuchstaben des virtuellen Computers beibehalten möchten, können Sie die SAN-Richtlinie für den virtuellen Computer aktivieren. [Weitere Informationen](https://support.microsoft.com/kb/3031135)
- **Weiterleiten von Clientanforderungen nach dem Failover zu Azure**: Site Recovery verwendet Azure Traffic Manager, um Clientanforderungen nach dem Failover an Ihre Anwendung weiterzuleiten. Sie können Skripts in Wiederherstellungsplänen verwenden (mit Azure Automation), um DNS-Updates durchzuführen.

## Integration

- **Integration in andere BCDR-Technologie**: Site Recovery kann in andere BCDR-Technologie (Business Continuity and Disaster Recovery) integriert werden. Für die anwendungsbasierte Replikation empfehlen wir Ihnen die Verwendung von SQL Server AlwaysOn, um virtuelle Computer zu replizieren, auf denen die Datenbank ausgeführt wird, sowie eines Hyper-V-Replikats für virtuelle Front-End-Computer. AlwaysOn erfordert SQL Server Enterprise-Lizenzen an Ihrem primären und sekundären Standort sowie einen aktiven virtuellen Azure-Computer in Ihrem Abonnement. Die Verwendung der integrierten Replikation von Site Recovery ist nützlich, wenn Anwendungen Ausfallzeiten überstehen können. In diesem Fall empfehlen wir Ihnen, Site Recovery zur Verwaltung der Replikation von virtuellen Computern für die Bereiche Datenbank, Anwendung und Front-End einzusetzen. Mit diesem Ansatz sparen Sie Kosten bei der erforderlichen SQL Server-Version, der Anzahl von Lizenzen und den Kosten für die Sicherstellung der ununterbrochenen Ausführung von virtuellen Computern in Azure.

## Nächste Schritte

Nachdem Sie diese bewährten Methoden überprüft haben, können Sie mit der Bereitstellung von Site Recovery beginnen:

- [Einrichten von Schutz zwischen einem lokalen VMM-Standort und Azure](site-recovery-vmm-to-azure.md)
- [Einrichten von Schutz zwischen einem lokalen Hyper-V-Standort und Azure](site-recovery-hyper-v-site-to-azure.md)
- [Einrichten von Schutz zwischen zwei lokalen VMM-Standorten](site-recovery-vmm-to-vmm.md)
- [Einrichten von Schutz zwischen lokalen VMM-Standorten mit einem SAN](site-recovery-vmm-san.md)
- [Einrichten von Schutz mit einem einzelnen VMM-Server](site-recovery-single-vmm.md)
 

<!---HONumber=58_postMigration-->