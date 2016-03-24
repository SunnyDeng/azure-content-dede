<properties
	pageTitle="Entwerfen Ihrer Netzwerkinfrastruktur für die Notfallwiederherstellung | Microsoft Azure"
	description="In diesem Artikel werden die Aspekte des Netzwerkentwurfs für Azure Site Recovery beschrieben."
	services="site-recovery"
	documentationCenter=""
	authors="prateek9us"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/08/2016"
	ms.author="pratshar"/>

#  Entwerfen Ihrer Netzwerkinfrastruktur für die Notfallwiederherstellung

Dieser Artikel richtet sich an IT-Experten, die für die Erstellung der Architektur, Implementierung und Unterstützung der Infrastruktur für Geschäftskontinuität und Notfallwiederherstellung (Business Continuity and Disaster Recovery, BCDR) verantwortlich sind und Microsoft Azure Site Recovery (ASR) einsetzen möchten, um ihre BCDR-Dienste zu unterstützen und zu verbessern. In diesem Artikel werden Praxisaspekte für die System Center Virtual Machine Manager-Serverbereitstellung, die Vor- und Nachteile von gestreckten Subnetzen im Vergleich mit dem Subnetzfailover und der Aufbau einer Notfallwiederherstellung für virtuelle Websites unter Microsoft Azure beschrieben.

## Übersicht

[Azure Site Recovery (ASR)](https://azure.microsoft.com/services/site-recovery/) ist ein Microsoft Azure-Dienst, mit dem der Schutz und die Wiederherstellung Ihrer virtualisierten Anwendungen für die Geschäftskontinuität und Notfallwiederherstellung orchestriert werden. Mit diesem Dokument sollen Leser durch den Prozess zum Entwerfen von Netzwerken geführt werden. Der Schwerpunkt liegt hierbei auf der Einrichtung von IP-Bereichen und Subnetzen am Standort der Notfallwiederherstellung, wenn virtuelle Computer (VMs) per Site Recovery repliziert werden.

Außerdem wird in diesem Artikel veranschaulicht, wie Site Recovery das Entwerfen und Implementieren eines virtuellen Rechenzentrums mit mehreren Standorten ermöglicht, damit BCDR-Dienste für Tests oder im Notfall unterstützt werden.

In einer Welt, in der die Verbindung rund um die Uhr vorhanden sein muss, ist es wichtiger als jemals zuvor, dass Ihre Infrastruktur und Anwendungen immer betriebsbereit sind. Der Zweck der Geschäftskontinuität und Notfallwiederherstellung besteht darin, ausgefallene Komponenten wiederherzustellen, damit die Organisation schnell wieder den normalen Betrieb aufnehmen kann. Die Entwicklung von Strategien für die Notfallwiederherstellung bei wenig wahrscheinlichen, desaströsen Vorfällen ist sehr anspruchsvoll. Dies liegt an der damit verbundenen Schwierigkeit, die Zukunft vorherzusagen. Es hängt vor allem mit den unwahrscheinlichen Vorfällen und den hohen Kosten zusammen, die für die Bereitstellung geeigneter Maßnahmen zum Schutz vor weit reichenden Katastrophen anfallen.

Für die BCDR-Planung ist es von entscheidender Bedeutung, dass „Recovery Time Objective“ (RTO) und „Recovery Point Objective“ (RPO) im Rahmen eines Plans für die Notfallwiederherstellung definiert werden. Wenn das Rechenzentrum des Kunden von einem Notfall betroffen ist, können Kundenmitarbeiter mit Azure Site Recovery ihre replizierten virtuellen Computer, die sich entweder im sekundären Rechenzentrum oder unter Microsoft Azure befinden, schnell (niedriger RTO-Wert) und mit minimalem Datenverlust (niedriger RPO-Wert) online schalten.

Das Failover wird durch ASR ermöglicht. Zuerst werden die angegebenen virtuellen Computer aus dem primären Rechenzentrum in das sekundäre Rechenzentrum oder in Azure (je nach Szenario) kopiert, und anschließend werden die Replikate regelmäßig aktualisiert. Während der Infrastrukturplanung sollte der Netzwerkentwurf als potenzieller Engpass angesehen werden, der verhindern kann, dass Sie Ihre Unternehmensziele in Bezug auf die Werte für RTO und RPO erreichen.

Wenn Administratoren die Bereitstellung einer Lösung für die Notfallwiederherstellung planen, ist eine der wichtigsten Fragen, wie der virtuelle Computer nach Abschluss des Failovers erreichbar ist. ASR ermöglicht dem Administrator, das Netzwerk auszuwählen, mit dem ein virtueller Computer nach einem Failover verbunden werden soll. Wenn der primäre Standort von einem VMM-Server verwaltet wird, erfolgt dies über Netzwerkzuordnungen. Weitere Informationen finden Sie unter [Vorbereiten für die Netzwerkzuordnung](site-recovery-network-mapping.md).

Beim Entwerfen des Netzwerks für den Wiederherstellungsstandort haben Administratoren zwei Möglichkeiten:

- Verwenden eines anderen IP-Adressbereichs für das Netzwerk am Wiederherstellungsstandort. Bei diesem Szenario erhält der virtuelle Computer nach dem Failover eine neue IP-Adresse, und der Administrator muss ein DNS-Update durchführen. Wie Sie das DNS-Update durchführen, erfahren Sie [hier](site-recovery-vmm-to-vmm.md#test-your-deployment): 
- Verwenden des gleichen IP-Adressbereichs für das Netzwerk am Wiederherstellungsstandort. Bei bestimmten Szenarien ziehen es Administratoren vor, die IP-Adressen beizubehalten, die sie am primären Standort verwenden, und zwar auch nach dem Failover. Bei einem normalen Szenario muss ein Administrator die Routen aktualisieren, um den neuen Standort der IP-Adressen anzugeben. Aber bei diesem Szenario, bei dem ein gestrecktes VLAN zwischen dem primären und sekundären Standort bereitgestellt wird, ist das Beibehalten der IP-Adressen für die virtuellen Computer eine attraktive Option. Das Beibehalten der gleichen IP-Adressen vereinfacht den Wiederherstellungsprozess, da alle netzwerkbezogenen Schritte nach dem Failover entfallen.


Wenn Administratoren die Bereitstellung einer Lösung für die Notfallwiederherstellung planen, ist eine der wichtigsten Fragen, wie die Anwendungen nach Abschluss des Failovers erreichbar sind. Moderne Anwendungen sind fast immer zu einem gewissen Grad von Netzwerken abhängig, sodass mit dem physischen Verschieben eines Diensts von einem Standort zum anderen fast immer besondere Netzwerkanforderungen verbunden sind. Es gibt zwei Hauptverfahren, wie dieses Problem bei Lösungen für die Notfallwiederherstellung gelöst wird. Der erste Ansatz besteht in der Verwaltung fester IP-Adressen. Trotz der Verschiebung der Dienste und der Tatsache, dass sich die Hostserver an verschiedenen physischen Standorten befinden, nehmen die Anwendungen die IP-Adresskonfiguration mit sich an den neuen Speicherort. Beim zweiten Ansatz ist eine vollständige Änderung der IP-Adresse während des Übergangs auf den wiederhergestellten Standort erforderlich. Jeder Ansatz verfügt über mehrere Implementierungsvarianten, die unten zusammengefasst sind.

Beim Entwerfen des Netzwerks für den Wiederherstellungsstandort haben Administratoren zwei Möglichkeiten:

## Option 1: Beibehalten der IP-Adressen 

Aus Sicht des Prozesses für die Notfallwiederherstellung ist die Verwendung feststehender IP-Adressen scheinbar die am einfachsten zu implementierende Methode. Sie ist aber mit einer Reihe potenzieller Herausforderungen verbunden, sodass es in der Praxis der am wenigsten beliebte Ansatz ist. Azure Site Recovery bietet die Möglichkeit, die IP-Adressen in allen Szenarien beizubehalten. Bevor Sie sich für die Beibehaltung der IP-Adressen entscheiden, sollten Sie sich über die Einschränkungen im Klaren sein, die sich daraus für die Failoverfunktionen ergeben. Wir sehen uns nun die Faktoren an, die Ihnen bei der Entscheidungsfindung in Bezug auf die Beibehaltung von IP-Adressen behilflich sein können. Dies kann auf zwei Arten erfolgen: mit einem gestreckten Subnetz oder durch Ausführen eines vollständigen Subnetzfailovers.

### Gestrecktes Subnetz

Hier wird das Subnetz gleichzeitig sowohl am primären Standort als auch am Standort für die Notfallwiederherstellung verfügbar gemacht. Einfach ausgedrückt bedeutet dies, dass Sie einen Server und seine IP-Konfiguration (Layer 3) an den zweiten Standort verschieben können und der Datenverkehr im Netzwerk automatisch an den neuen Standort weitergeleitet wird. Dies ist sehr einfach in der Handhabung aus Sicht des Servers, es bestehen jedoch eine Reihe von Herausforderungen:

- Aus Sicht von Schicht 2 (Sicherungsschicht) sind Netzwerkgeräte erforderlich, die ein gestrecktes VLAN verwalten können. Dies stellt jedoch kein größeres Problem mehr dar, da diese Geräte jetzt allgemein verfügbar sind. Das zweite und schwierigere Problem ist, dass sich die potenzielle Fehlerdomäne durch Strecken das VLANs auf beide Standorte ausweitet und im Grunde zu einer einzigen Fehlerquelle wird. Dies ist zwar ein relativ unwahrscheinlicher Fall, aber es ist schon passiert, dass ein Broadcast-Storm gestartet wurde und nicht isoliert werden konnte. Wir haben zu diesem letzten Problem gemischte Rückmeldungen erhalten. Es gibt viele erfolgreiche Implementierungen, aber auch Aussagen der Art „Wir werden diese Technologie bei uns niemals implementieren“.
- Gestreckte Subnetze sind nicht möglich, wenn Sie Microsoft Azure als Standort für die Notfallwiederherstellung verwenden.


### Subnetzfailover

Die Implementierung des Subnetzfailovers ist möglich, um von den oben beschriebenen Vorteilen der Lösung mit gestrecktem Subnetz zu profitieren, ohne dass das Subnetz über mehrere Standorte gestreckt wird. Hierbei ist ein Subnetz an Standort 1 oder 2 vorhanden, aber nie an beiden Standorten gleichzeitig. Um den IP-Adressraum im Falle eines Failovers beizubehalten, können Sie programmgesteuert dafür sorgen, dass die Routerinfrastruktur die Subnetze von einem Standort an einen anderen verschiebt. In einem Failoverszenario werden die Subnetze mit den zugeordneten, geschützten virtuellen Computern verschoben. Der größte Nachteil dieses Ansatzes ist, dass Sie bei Auftreten eines Fehlers das gesamte Subnetz verschieben müssen. Dies ist unter Umständen kein Problem, kann sich aber auf Überlegungen im Hinblick auf die Failovergranularität auswirken.

Wir untersuchen nun, wie ein fiktives Unternehmen mit dem Namen Contoso seine virtuellen Computer während der Ausführung eines Failovers des gesamten Subnetzes an einen Wiederherstellungsstandort replizieren kann. Zuerst wird beschrieben, wie Contoso seine Subnetze während der Replikation von VMs zwischen zwei lokalen Standorten verwaltet. Anschließend wird erläutert, wie ein Subnetzfailover funktioniert, wenn [Azure als Standort für die Notfallwiederherstellung verwendet wird](#failover-to-azure).

#### Failover zu einem sekundären lokalen Standort

Wir sehen uns nun ein Szenario an, bei dem wir die IP-Adresse aller VMs beibehalten und das Failover für das gesamte Subnetz zusammen durchführen möchten. Der primäre Standort verfügt über Anwendungen, die im Subnetz 192.168.1.0/24 ausgeführt werden. Wenn das Failover erfolgt, wird für alle virtuellen Computer, die Teil dieses Subnetzes sind, der Failoverschritt zum Wiederherstellungsstandort durchgeführt, und ihre IP-Adressen werden beibehalten. Die Routen müssen entsprechend geändert werden, um die Tatsache widerzuspiegeln, dass alle virtuellen Computer, die zum Subnetz 192.168.1.0/24 gehören, jetzt an den Wiederherstellungsstandort verschoben wurden.

In der folgenden Abbildung müssen die Routen zwischen primärem Standort und Wiederherstellungsstandort, drittem Standort und primärem Standort und drittem Standort und Wiederherstellungsstandort entsprechend geändert werden.

Die folgenden Bilder zeigen die Subnetze vor dem Failover. Subnetz 192.168.0.1/24 ist am primären Standort vor dem Failover aktiv und wird am Wiederherstellungsstandort nach dem Failover aktiviert

![Vor dem Failover](./media/site-recovery-network-design/network-design2.png)

Vor dem Failover


Die nachstehende Abbildung zeigt die Netzwerke und Subnetze nach dem Failover.
	
![Nach dem Failover](./media/site-recovery-network-design/network-design3.png)

Nach dem Failover

Wenn Ihr sekundärer Standort lokal ist und Sie einen VMM-Server für die Verwaltung verwenden, weist ASR beim Aktivieren des Schutzes für einen bestimmten virtuellen Computer Netzwerkressourcen anhand des folgenden Workflows zu:

- ASR weist jeder Netzwerkschnittstelle auf dem virtuellen Computer eine IP-Adresse aus dem statischen IP-Adresspool zu, der im relevanten Netzwerk für jede System Center VMM-Instanz definiert ist.
- Wenn der Administrator für das Netzwerk am Wiederherstellungsstandort den gleichen IP-Adresspool definiert, der für das Netzwerk am primären Standort festgelegt wurde, würde ASR beim Zuweisen der IP-Adresse zum virtuellen Replikatcomputer die gleiche IP-Adresse wie für den primären virtuellen Computer zuordnen. Die IP-Adresse ist in VMM reserviert, aber nicht als Failover-IP-Adresse festgelegt. Die Failover-IP-Adresse wird erst unmittelbar vor dem Failover festgelegt.

![IP-Adresse beibehalten](./media/site-recovery-network-design/network-design4.png)
	
Abbildung 5

Abbildung 5 zeigt die TCP/IP-Failovereinstellungen für den virtuellen Replikatcomputer (auf der Hyper-V-Konsole). Diese Einstellungen werden unmittelbar vor dem Starten des virtuellen Computers nach einem Failover aufgefüllt.

Falls die gleiche IP-Adresse nicht verfügbar ist, ordnet ASR eine andere verfügbare IP-Adresse aus dem definierten IP-Adresspool zu.

Nachdem der virtuelle Computer für den Schutz aktiviert wurde, können Sie das folgende Beispielskript verwenden, um die IP-Adresse zu überprüfen, die dem virtuellen Computer zugewiesen wurde. Die gleiche IP-Adresse würde als Failover-IP-Adresse festgelegt und dem virtuellen Computer zum Zeitpunkt des Failovers zugewiesen werden:

    	$vm = Get-SCVirtualMachine -Name <VM_NAME>
		$na = $vm[0].VirtualNetworkAdapters>
		$ip = Get-SCIPAddress -GrantToObjectID $na[0].id
		$ip.address  

>[AZURE.NOTE] Bei diesem Szenario, bei dem virtuelle Computer DHCP verwenden, liegt die Verwaltung von IP-Adressen vollständig außerhalb der Kontrolle von ASR. Ein Administrator muss sicherstellen, dass der DHCP-Server, über den die IP-Adressen am Wiederherstellungsstandort bereitgestellt werden, den gleichen Bereich wie für den primären Standort nutzen kann.

#### Failover zu Azure

Mit Azure Site Recovery (ASR) kann Microsoft Azure für virtuelle Computer als Standort für die Notfallwiederherstellung genutzt werden. In diesem Fall muss eine weitere Einschränkung berücksichtigt werden.

Wir sehen uns ein Szenario an, bei dem das fiktive Unternehmen Woodgrove Bank über eine lokale Infrastruktur zum Hosten seiner Branchenanwendungen verfügt und die mobilen Anwendungen unter Azure gehostet werden. Die Verbindung zwischen den VMs der Woodgrove Bank in Azure und den lokalen Servern wird mit einem virtuellen privaten Netzwerk (Virtual Private Network, VPN) vom Typ „Standort-zu-Standort“ (S2S) sichergestellt. Dank S2S-VPN kann das virtuelle Netzwerk der Woodgrove Bank in Azure als Erweiterung des lokalen Netzwerks der Woodgrove Bank angesehen werden. Diese Kommunikation wird per S2S-VPN zwischen dem Woodgrove Bank-Edgenetzwerk und dem virtuellen Azure-Netzwerk ermöglicht. Woodgrove möchte nun ASR einsetzen, um seine Workloads, die im eigenen Rechenzentrum ausgeführt werden, zu Azure zu replizieren. Diese Option erfüllt die Anforderungen von Woodgrove, nämlich eine wirtschaftliche Option für die Notfallwiederherstellung und die Speicherung von Daten in Public Cloud-Umgebungen. Bei der Woodgrove Bank sind Anwendungen und Konfigurationen vorhanden, die von hartcodierten IP-Adressen abhängig sind. Daher besteht die Anforderung, dass IP-Adressen der Anwendungen nach einem Failover zu Azure beibehalten werden müssen.

Die Woodgrove Bank hat sich dafür entschieden, ihren in Azure ausgeführten Ressourcen IP-Adressen aus dem IP-Adressbereich (172.16.1.0/24, 172.16.2.0/24) zuzuweisen.

Damit die Woodgrove Bank ihre virtuellen Computer zu Azure replizieren kann, während die IP-Adressen beibehalten werden, muss ein Azure Virtual Network erstellt werden. Es sollte sich hierbei um eine Erweiterung des lokalen Netzwerks handeln, damit Anwendungen das Failover vom lokalen Standort zu Azure nahtlos durchführen können. Azure ermöglicht Ihnen sowohl das Hinzufügen von Standort-zu-Standort- als auch von Punkt-zu-Standort-VPN-Verbindungen mit den virtuellen Netzwerken, die in Azure erstellt werden. Beim Einrichten der Standort-zu-Standort-Verbindung können Sie in einem Azure-Netzwerk nur dann Datenverkehr an den lokalen Standort (bei Azure als „local-network“ oder „lokales Netzwerk“ bezeichnet) weiterleiten, wenn der IP-Adressbereich sich vom lokalen IP-Adressbereich unterscheidet, weil Azure das Strecken von Subnetzen nicht unterstützt. Daraus folgt, dass Sie im Azure-Netzwerk kein lokales Netzwerk 192.168.1.0/24 hinzufügen können, wenn Sie über ein lokales Subnetz 192.168.1.0/24 verfügen. Der Grund hierfür liegt darin, dass Azure nicht weiß, dass keine aktiven virtuellen Computer im Subnetz vorhanden sind und dass das Subnetz nur für die Notfallwiederherstellung erstellt wird. Um einen korrekten Netzwerkdatenverkehr aus einem Azure-Netzwerk zu gewährleisten, dürfen keine Konflikte zwischen den Subnetzen im Netzwerk und dem lokalen Netzwerk vorliegen.

![Vor dem Subnetzfailover](./media/site-recovery-network-design/network-design7.png)

Vor dem Failover

Wir müssen die folgenden Workflows implementieren, um für Woodgrove die geschäftlichen Anforderungen zu erfüllen:

- Erstellen Sie ein zusätzliches Netzwerk (mit dem Namen „Recovery Netzwerk“), in dem die virtuellen Computer nach dem Failover erstellt werden.
- Gehen Sie wie folgt vor, um sicherzustellen, dass die IP-Adresse für einen virtuellen Computer nach einem Failover beibehalten wird: Navigieren Sie in den VM-Eigenschaften zur Registerkarte „Konfigurieren“, geben Sie die gleiche IP-Adresse an, die für den virtuellen Computer lokal verwendet wird, und klicken Sie dann auf „Speichern“. Wenn das Failover für den virtuellen Computer erfolgt, weist Azure Site Recovery die bereitgestellte IP-Adresse dem virtuellen Computer zu. 

![Netzwerkeigenschaften](./media/site-recovery-network-design/network-design8.png)

Nachdem das Failover ausgelöst wurde und die virtuellen Computer im Wiederherstellungsnetzwerk mit der gewünschten IP-Adresse erstellt wurden, kann die Verbindung mit diesem Netzwerk über eine [VNET-zu-VNET-Verbindung](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) hergestellt werden. Falls erforderlich, kann diese Aktion in Form eines Skripts erstellt werden. Wie im vorherigen Abschnitt über das Subnetzfailover erläutert, müssten die Routen auch beim Failover zu Azure entsprechend geändert werden, um widerzuspiegeln, dass 192.168.1.0/24 jetzt zu Azure verschoben wurde.

![Nach dem Subnetzfailover](./media/site-recovery-network-design/network-design9.png)

Nach dem Failover

Wenn „Azure Network“ nicht wie in der Abbildung oben dargestellt wird, können Sie nach dem Failover eine Standort-zu-Standort-VPN-Verbindung zwischen Ihrem primären Standort und dem Wiederherstellungsnetzwerk herstellen.


## Option 2: Ändern der IP-Adressen

Dieser Ansatz scheint nach unserem Erkenntnisstand am weitesten verbreitet zu sein. Hierbei wird die IP-Adresse jedes virtuellen Computers geändert, der am Failover beteiligt ist. Ein Nachteil bei diesem Ansatz ist, dass das eingehende Netzwerk „lernen“ muss, dass sich die Anwendung, die sich unter IPx befunden hat, jetzt unter IPy befindet. Auch wenn IPx und IPy logische Namen sind, müssen DNS-Einträge normalerweise im gesamten Netzwerk geändert oder gelöscht werden, und zwischengespeicherte Einträge in Netzwerktabellen müssen aktualisiert oder gelöscht werden. Aus diesem Grund kann es je nach Einrichtung der DNS-Infrastruktur zu Ausfallzeiten kommen. Sie können diese Probleme abmildern, indem Sie für Intranetanwendungen niedrige TTL-Werte und für internetbasierte Anwendungen [Azure Traffic Manager mit ASR](http://azure.microsoft.com/blog/2015/03/03/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/) verwenden.

### Ändern der IP-Adressen – Veranschaulichung

Wir sehen uns nun das Szenario an, bei dem Sie die Verwendung von unterschiedlichen IP-Adressen übergreifend für primäre Standorte und Wiederherstellungsstandorte planen. Im folgenden Beispiel verwenden wir zusätzlich einen dritten Standort, von dem aus auf die Anwendungen zugegriffen werden kann, die am primären Standort oder Wiederherstellungsstandort gehostet werden.

![Andere IP-Adresse – vor dem Failover](./media/site-recovery-network-design/network-design10.png)

Abbildung 11

In Abbildung 11 werden einige Anwendungen im Subnetz 192.168.1.0/24 am primären Standort gehostet und wurden so konfiguriert, dass sie nach einem Failover am Wiederherstellungsstandort im Subnetz 172.16.1.0/24 wieder hochgefahren werden. VPN-Verbindungen/Netzwerkrouten wurden entsprechend konfiguriert, damit alle drei Standorte aufeinander zugreifen können.
 
In Abbildung 12 ist zu sehen, dass Anwendungen nach einem Failover einer oder mehrerer Anwendungen im Wiederherstellungssubnetz wiederhergestellt werden. In diesem Fall besteht nicht die Einschränkung, das Failover für das gesamte Subnetz zur gleichen Zeit durchführen zu müssen. Es sind keine Änderungen erforderlich, um VPN- oder Netzwerkrouten neu zu konfigurieren. Mit einem Failover und einigen DNS-Updates wird sichergestellt, dass Zugriff auf die Anwendungen besteht. Wenn der DNS so konfiguriert ist, dass er dynamische Updates zulässt, registrieren die virtuellen Computer sich unter Verwendung der neuen IP-Adresse selbst, sobald sie nach einem Failover gestartet werden.

![Andere IP-Adresse – nach dem Failover](./media/site-recovery-network-design/network-design11.png)

Abbildung 12

Nach dem Failover weist die Replikat-VM möglicherweise eine IP-Adresse auf, die nicht der IP-Adresse des primären virtuellen Computers entspricht. Virtuelle Computer aktualisieren den DNS-Server, den sie nach dem Start verwenden. DNS-Einträge müssen in der Regel im gesamten Netzwerk geändert oder gelöscht werden, und zwischengespeicherte Einträge in Netzwerktabellen müssen aktualisiert oder gelöscht werden, daher ist es nicht ungewöhnlich, dass es während dieser Zustandsänderungen zu Ausfallzeiten kommt. Dieses Problem kann mit folgenden Maßnahmen vermieden werden:

- Durch Verwenden niedrige TTL-Werte für Intranetanwendungen.
- Durch Verwenden von Azure Traffic Manager mit ASR für internetbasierte Anwendungen.
- Durch Verwenden des folgende Skripts in Ihrem Wiederherstellungsplan zum Aktualisieren der DNS-Server, um ein rechtzeitiges Update sicherzustellen. (Das Skript ist nicht erforderlich, wenn die dynamische DNS-Registrierung konfiguriert ist wird.)

		string]$Zone,
		[string]$name,
		[string]$IP
		)
		$Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
		$newrecord = $record.clone()
		$newrecord.RecordData[0].IPv4Address  =  $IP
		Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord


### Ändern der IP-Adressen – Notfallwiederherstellung in Azure

Im Blogbeitrag [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) (Netzwerkinfrastruktur-Einrichtung für Microsoft Azure als Standort für die Notfallwiederherstellung) wird erläutert, wie die erforderliche Azure-Netzwerkinfrastruktur konfiguriert wird, wenn das Beibehalten der IP-Adressen keine Voraussetzung ist. Zuerst wird die Anwendung beschrieben, und anschließend wird erläutert, wie Sie das Netzwerk lokal und unter Azure einrichten. Am Ende geht es um die Durchführung eines Testfailovers und eines geplanten Failovers.



## Nächste Schritte

[Erfahren Sie](site-recovery-network-mapping.md), wie Site Recovery Quell- und Zielnetzwerke zuordnet, wenn der primäre Standort mit einem VMM-Server verwaltet wird.

<!---HONumber=AcomDC_0309_2016-->