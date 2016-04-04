<properties
	pageTitle="Hochverfügbarkeit und Notfallwiederherstellung für SQL Server | Microsoft Azure"
	description="Eine Beschreibung der verschiedenen HADR-Strategien für SQL Server in Azure Virtual Machines."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/22/2016"
	ms.author="jroth" />

# Hohe Verfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern

## Übersicht

Virtuelle Microsoft Azure-Computer (VMs) mit SQL Server können die Kosten für Datenbanklösungen mit hoher Verfügbarkeit und Notfallwiederherstellung (High Availability, Disaster Recovery – HADR) senken. Die meisten SQL Server-HADR-Lösungen werden in Azure Virtual Machines sowohl als reine Azure- als auch als Hybridlösungen unterstützt. In einer reinen Azure-Lösung wird das gesamte HADR-System in Azure ausgeführt. In einer Hybridkonfiguration wird ein Teil der Lösung in Azure und der andere Teil lokal in Ihrer Organisation ausgeführt. Die Flexibilität der Azure-Umgebung können Sie teilweise oder vollständig in Azure verschieben, um Budget- und HADR-Anforderungen der SQL Server-Datenbanksysteme zu erfüllen.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


## Grundlegendes zum Bedarf an einer HADR-Lösung

Es ist Ihre Aufgabe sicherzustellen, dass Ihr Datenbanksystem über die gemäß der Vereinbarung zum Servicelevel (SLA) erforderlichen HADR-Funktionen verfügt. Die Tatsache, dass Azure Funktionen für eine hohe Verfügbarkeit bereitstellt, z. B. die Selbstreparatur für Clouddienste und die Wiederherstellungserkennung für virtuelle Computer, gewährleistet noch nicht, dass Sie die Vereinbarung zum Servicelevel erfüllen können. Diese Mechanismen stellen eine hohe Verfügbarkeit der virtuellen Computer sicher, aber nicht die hohe Verfügbarkeit der auf den virtuellen Computern ausgeführten SQL Server-Instanzen. Es ist möglich, dass eine SQL Server-Instanz ausfällt, obwohl die VM online und betriebsbereit ist. Und selbst die Funktionen für eine hohe Verfügbarkeit von Azure lassen Ausfallzeiten von virtuellen Computern zu, z. B. für die Wiederherstellung nach Software- oder Hardwareausfällen und um Betriebssystemupgrades zu ermöglichen.

Darüber hinaus ist möglicherweise der georedundante Speicher (GRS) in Azure, der mit dem Feature Georeplikation implementiert wird, keine geeignete Notfallwiederherstellungslösung für Ihre Datenbanken. Da bei der Georeplikation Daten asynchron gesendet werden, können die neuesten Updates bei einem Ausfall verloren gehen. Weitere Informationen zu den Einschränkungen der Georeplikation finden Sie im Abschnitt [Unterstützung der Georeplikation](#geo-replication-support).

## HADR-Bereitstellungsarchitekturen

Zu den in Azure unterstützten SQL Server-HADR-Technologien gehören:

- [AlwaysOn-Verfügbarkeitsgruppen](https://technet.microsoft.com/library/hh510230.aspx)
- [Datenbankspiegelung](https://technet.microsoft.com/library/ms189852.aspx)
- [Protokollversand](https://technet.microsoft.com/library/ms187103.aspx)
- [Sicherung und Wiederherstellung mit dem Azure-Blob-Speicherdienst](https://msdn.microsoft.com/library/jj919148.aspx)
- [AlwaysOn-Failoverclusterinstanzen](https://technet.microsoft.com/library/ms189134.aspx)

Es ist möglich, mehrere Technologie zu kombinieren, um eine SQL Server-Lösung zu implementieren, die sowohl Funktionen zur hohen Verfügbarkeit als auch zur Notfallwiederherstellung verfügt. Abhängig von der verwendeten Technologie erfordert eine Hybridbereitstellung einen VPN-Tunnel mit dem virtuellen Azure-Netzwerk. In den folgenden Abschnitte werden einige Beispiele für Bereitstellungsarchitekturen gezeigt.

## Nur in Azure: Lösungen mit hoher Verfügbarkeit

Sie können eine Lösung mit hoher Verfügbarkeit für SQL Server-Datenbanken in Azure mit AlwaysOn-Verfügbarkeitsgruppen oder per Datenbankspiegelung umsetzen.

|Technologie|Beispielarchitekturen|
|---|---|
|**AlwaysOn-Verfügbarkeitsgruppen**|Alle verfügbaren Replikate werden auf den virtuellen Azure-Computern für eine hohe Verfügbarkeit innerhalb derselben Region ausgeführt. Sie müssen eine Domänencontroller-VM konfigurieren, da für das Windows Server Failover Clustering (WSFC) eine Active Directory-Domäne erforderlich ist.<br/> ![AlwaysOn-Verfügbarkeitsgruppen](./media/virtual-machines-windows-classic-sql-dr/azure_only_ha_always_on.gif)<br/>Weitere Informationen finden Sie unter [Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in Azure (GUI)](virtual-machines-windows-classic-portal-sql-availability.md).|**Spiegeln von Datenbanken**|Prinzipal-, Spiegel- und Zeugenserver werden im gleichen Azure-Rechenzentrum ausgeführt, um eine hohe Verfügbarkeit zu gewährleisten. Sie können die Bereitstellung mit einem Domänencontroller durchführen.<br/>![Datenbankspiegelung](./media/virtual-machines-windows-classic-sql-dr/azure_only_ha_dbmirroring1.gif)<br/>Mithilfe von Serverzertifikaten können Sie dieselbe Konfiguration zur Datenbankspiegelung auch ohne einen Domänencontroller bereitstellen.<br/>![Datenbankspiegelung](./media/virtual-machines-windows-classic-sql-dr/azure_only_ha_dbmirroring2.gif)|
|**AlwaysOn-Failoverclusterinstanzen**|Zum Erstellen von Failoverclusterinstanzen (FCI), die gemeinsam genutzten Speicher erfordern, gibt es zwei verschiedene Möglichkeiten:<br/><br/>1. Eine FCI auf einem WSFC mit zwei Knoten, die auf Azure-VMs mit Speicher ausgeführt wird, der von einer Drittanbieter-Clusterlösung bereitgestellt wird. Ein spezifisches Beispiel mit SIOS DataKeeper finden Sie unter [Hohe Verfügbarkeit für eine Dateifreigabe mit WSFC und der Drittanbietersoftware SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. Eine FCI auf einem WSFC mit zwei Knoten, die auf Azure-VMs mit gemeinsam genutztem Remote-iSCSI-Zielblockspeicher über ExpressRoute ausgeführt wird. Beispielsweise macht NetApp Private Storage (NPS) ein iSCSI-Ziel über ExpressRoute mit Equinix für virtuelle Azure-Computer verfügbar.<br/><br/>Für Drittanbieterlösungen für gemeinsame Speichernutzung und Datenreplikation sollten Sie sich an den Hersteller wenden, falls beim Failover Probleme in Bezug auf den Datenzugriff auftreten.<br/><br/>Beachten Sie, dass die Verwendung von FCI zusätzlich zu [Azure File Storage](https://azure.microsoft.com/services/storage/files/) noch nicht unterstützt wird, da diese Lösung nicht Storage Premium verwendet. Wir arbeiten daran, dass diese Konfiguration so bald wie möglich unterstützt wird.|

## Nur in Azure: Notfallwiederherstellungslösungen

Sie können eine Notfallwiederherstellungslösung für Ihre SQL Server-Datenbanken in Azure mit AlwaysOn-Verfügbarkeitsgruppen, Datenbankspiegelung oder Sicherung und Wiederherstellung mit Speicher-Blobs umsetzen.

|Technologie|Beispielarchitekturen|
|---|---|
|**AlwaysOn-Verfügbarkeitsgruppen**|Die Replikate für die Verfügbarkeit werden in mehreren Rechenzentren auf virtuellen Azure-Computern für die Notfallwiederherstellung ausgeführt. Diese regionsübergreifende Lösung bietet einen Schutz vor dem Ausfall einzelner Standorte. <br/> ![AlwaysOn-Verfügbarkeitsgruppen](./media/virtual-machines-windows-classic-sql-dr/azure_only_dr_alwayson.png)<br/>Alle Replikate innerhalb einer Region sollten sich in demselben Clouddienst und im gleichen VNet befinden. Da jede Region ein gesondertes VNet besitzt, benötigen diese Lösungen VNet-zu-VNet-Verbindungen. Weitere Informationen finden Sie unter [Konfigurieren eines Standort-zu-Standort-VPN im klassischen Azure-Portal](../vpn-gateway/vpn-gateway-site-to-site-create.md).|
|**Spiegeln von Datenbanken**|Prinzipale, Spiegelungen und Server werden in verschiedenen Rechenzentren für die Notfallwiederherstellung ausgeführt. Da sich eine Active Directory-Domäne über mehrere Rechenzentren erstrecken kann, müssen Sie die Bereitstellung mithilfe von Serverzertifikaten durchführen.<br/>![Datenbankspiegelung](./media/virtual-machines-windows-classic-sql-dr/azure_only_dr_dbmirroring.gif)|
|**Sicherung und Wiederherstellung mit dem Azure-Blob-Speicherdienst**|Die Produktionsdatenbanken werden direkt im Blob-Speicher in einem anderen Rechenzentrum für die Notfallwiederherstellung gesichert.<br/>![Sichern und Wiederherstellen](./media/virtual-machines-windows-classic-sql-dr/azure_only_dr_backup_restore.gif)<br/>Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-classic-sql-br.md).|

## Hybride IT: Notfallwiederherstellungslösungen

Sie können eine Notfallwiederherstellungslösung für Ihre SQL Server-Datenbanken in Hybridlösungen mit AlwaysOn-Verfügbarkeitsgruppen, Datenbankspiegelung, Protokollversand und Sicherung und Wiederherstellung mit Azure-Blob-Speicher umsetzen.

|Technologie|Beispielarchitekturen|
|---|---|
|**AlwaysOn-Verfügbarkeitsgruppen**|Einige Replikate für die Verfügbarkeit werden auf virtuellen Azure-Computern ausgeführt und andere lokal, um eine standortübergreifende Notfallwiederherstellung sicherzustellen. Der Produktionsstandort kann sich entweder vor Ort oder in einem Azure-Rechenzentrum befinden.<br/>![AlwaysOn-Verfügbarkeitsgruppen](./media/virtual-machines-windows-classic-sql-dr/hybrid_dr_alwayson.gif)<br/>Da sich alle Verfügbarkeitsreplikate in demselben WSFC-Cluster befinden müssen, muss der WSFC-Cluster beide Netzwerke (mit WSFC-Clustern in mehreren Subnetzen) einschließen. Diese Konfiguration erfordert eine VPN-Verbindung zwischen Azure und dem lokalen Netzwerk.<br/><br/>Für die erfolgreiche Notfallwiederherstellung der Datenbanken sollten Sie auch einen Replikatdomänencontroller am Standort für die Notfallwiederherstellung installieren.<br/><br/>Es ist möglich, den Assistenten zum Hinzufügen von Replikaten in SSMS zu verwenden, um ein Azure-Replikat einer vorhandenen AlwaysOn-Verfügbarkeitsgruppe hinzufügen. Weitere Informationen finden Sie im Lernprogramm zum Erweitern der AlwaysOn-Verfügbarkeitsgruppe auf Azure.|
|**Spiegeln von Datenbanken**|Ein Partner wird auf einem virtuellen Azure-Computer und der andere lokal ausgeführt, um eine standortübergreifende Notfallwiederherstellung unter Verwendung von Serverzertifikaten zu gewährleisten. Die Partner müssen sich nicht in derselben Active Directory-Domäne befinden, und es ist keine VPN-Verbindung erforderlich.<br/>![Datenbankspiegelung](./media/virtual-machines-windows-classic-sql-dr/hybrid_dr_dbmirroring.gif)<br/>Ein anderes Szenario zur Datenbankspiegelung sieht einen Partner vor, der auf einem virtuellen Azure-Computer ausgeführt wird, während der andere lokal in derselben Active Directory-Domäne für die standortübergreifende Notfallwiederherstellung ausgeführt wird. Es ist eine [VPN-Verbindung zwischen dem virtuellen Azure-Netzwerk und dem lokalen Netzwerk](../vpn-gateway/vpn-gateway-site-to-site-create.md) erforderlich.<br/><br/>Für die erfolgreiche Notfallwiederherstellung der Datenbanken sollten Sie auch einen Replikatdomänencontroller am Standort für die Notfallwiederherstellung installieren.|
|**Versenden von Protokollen**|Ein Server wird auf einem virtuellen Azure-Computer und der andere lokal für die standortübergreifende Notfallwiederherstellung ausgeführt. Für das Versenden von Protokollen ist der Windows-Dateiaustausch erforderlich, deshalb muss eine VPN-Verbindung zwischen dem virtuellen Azure-Netzwerk und dem lokalen Netzwerk bestehen.<br/>![Protokollversand](./media/virtual-machines-windows-classic-sql-dr/hybrid_dr_log_shipping.gif)<br/>Für die erfolgreiche Notfallwiederherstellung der Datenbanken sollten Sie auch einen Replikatdomänencontroller am Standort für die Notfallwiederherstellung installieren.|
|**Sicherung und Wiederherstellung mit dem Azure-Blob-Speicherdienst**|Die lokalen Produktionsdatenbanken werden direkt im Azure-Blob-Speicher für die Notfallwiederherstellung gesichert.<br/>![Sichern und Wiederherstellen](./media/virtual-machines-windows-classic-sql-dr/hybrid_dr_backup_restore.gif)<br/>Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung für SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-classic-sql-br.md).|

## Wichtige Überlegungen zu SQL Server-HADR in Azure

Für die virtuellen Azure-Computer, den Speicher und die Netzwerkressourcen gelten andere Betriebsbedingungen als für eine lokale, nicht virtualisierte IT-Infrastruktur. Für eine erfolgreiche Implementierung einer SQL Server-HADR-Lösung in Azure sollten Sie diese Unterschiede kennen und Ihre Lösung daran anpassen.

### Hochverfügbarkeitsknoten in einer Verfügbarkeitsgruppe

Durch Verfügbarkeitsgruppen in Azure können Sie Hochverfügbarkeitsknoten in separaten Fehler- und Updatedomänen (FD bzw. UD) platzieren. Damit virtuelle Azure-Computer derselben Verfügbarkeitsgruppe hinzugefügt werden, müssen Sie sie im gleichen Clouddienst bereitstellen. Nur Knoten in demselben Clouddienst können Mitglieder derselben Verfügbarkeitsgruppe sein. Weitere Informationen finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-windows-manage-availability.md).

### WSFC-Clusterverhalten in Azure-Netzwerken

Der nicht RFC-kompatible DHCP-Dienst in Azure kann dazu führen, dass die Erstellung bestimmter WSFC-Clusterkonfigurationen fehlschlägt, da dem Clusternetzwerknamen eine doppelte IP-Adresse zugewiesen wird, z. B. die IP-Adresse eines der Clusterknoten. Dies stellt beim Implementieren von AlwaysOn-Verfügbarkeitsgruppen ein Problem dar, da diese von der WSFC-Funktion abhängen.

Beachten Sie folgendes Szenario, wenn ein Cluster mit zwei Knoten erstellt und online geschaltet wird:

1. Der Cluster wird online geschaltet, und KNOTEN1 fordert eine dynamisch zugewiesene IP-Adresse für den Netzwerknamen des Clusters an.

1. Der DHCP-Dienst weist die IP-Adresse von KNOTEN1 zu, da der DHCP-Dienst erkennt, dass die Anforderung von KNOTEN1 selbst.

1. Windows erkennt, dass KNOTEN1 und dem Clusternetzwerknamen dieselbe IP-Adresse zugewiesen wurde, und die Standardclustergruppe kann nicht online geschaltet werden.

1. Die Standardclustergruppe wechselt zu KNOTEN2, der die IP-Adresse von KNOTEN1 als Cluster-IP-Adresse behandelt und die Standardclustergruppe online schaltet.

1. Wenn KNOTEN2 versucht, eine Verbindung mit KNOTEN1 herzustellen, verlassen für KNOTEN1 bestimmten Pakete niemals KNOTEN2, da die IP-Adresse von KNOTEN1 zu sich selbst aufgelöst wird. KNOTEN2 kann keine Verbindung mit KNOTEN1 herstellen, verliert daraufhin das Quorum und fährt den Cluster herunter.

1. In der Zwischenzeit kann KNOTEN1 Pakete an KNOTEN2 senden, aber KNOTEN2 kann nicht antworten. KNOTEN1 verliert das Quorum und fährt den Cluster herunter.

Dieses Szenario kann vermieden werden, indem Sie dem Netzwerknamen des Clusters eine nicht verwendete statische IP-Adresse zuweisen, z. B. eine verbindungslokale IP-Adresse wie 169.254.1.1, um diesen online zu schalten. Informationen zur Vereinfachung dieses Vorgangs finden Sie unter [Konfigurieren eines Windows-Failoverclusters in Azure für AlwaysOn-Verfügbarkeitsgruppen](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx) (in englischer Sprache).

Weitere Informationen finden Sie unter [Konfigurieren von AlwaysOn-Verfügbarkeitsgruppen in Azure (GUI)](virtual-machines-windows-classic-portal-sql-availability.md).

### Unterstützung für Verfügbarkeitsgruppenlistener

Verfügbarkeitsgruppenlistener werden auf virtuellen Azure-Computern unter Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2 unterstützt. Möglich wird diese Unterstützung durch die Verwendung von Endpunkten mit Lastenausgleich auf den virtuellen Azure-Computern, die Verfügbarkeitsgruppenknoten sind. Sie müssen bestimmte Konfigurationsschritte für die Listener einhalten, damit sie sowohl für in Azure als auch lokal ausgeführte Clientanwendungen funktionieren.

Es gibt zwei Hauptoptionen für das Einrichten des Listeners: extern (öffentlich) oder intern. Der externe (öffentliche) Listener ist mit einer öffentlichen VIP (Virtual IP) verknüpft, die über das Internet zugänglich ist. Bei einem externen Listener müssen Sie Direct Server Return (DSR) aktivieren, d. h. dass Sie von einem Computer, der sich nicht im selben Clouddienst wie die Knoten der AlwaysOn-Verfügbarkeitsgruppe befindet, eine Verbindung mit dem Listener herstellen müssen. Stattdessen können Sie auch einen internen Listener verwenden, der den internen Load Balancer (ILB) verwendet. Ein interner Listener unterstützt nur Clients im gleichen virtuellen Netzwerk.

Wenn die Verfügbarkeitsgruppe mehrere Azure-Subnetze umfasst (z. B. bei einer Bereitstellung über mehrere Azure-Regionen hinweg), muss die Client-Verbindungszeichenfolge "**MultisubnetFailover = True**" enthalten. Dies führt zu parallelen Verbindungsversuchen mit den Replikaten in unterschiedlichen Subnetzen. Eine Anleitung zum Einrichten eines Listeners finden Sie unter

- [Konfigurieren eines ILB-Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-windows-classic-ps-sql-int-listener.md).
- [Konfigurieren eines externen Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

Sie können weiterhin separate Verbindungen mit den einzelnen Verfügbarkeitsreplikaten herstellen, indem Sie eine direkte Verbindung mit der Serverinstanz herstellen. Da AlwaysOn-Verfügbarkeitsgruppen abwärtskompatibel mit Clients für die Datenbankspiegelung sind, können Sie außerdem Verbindungen mit Verfügbarkeitsreplikaten wie Datenbankspiegelungspartnern herstellen, sofern die Replikate ähnlich wie die Datenbankspiegelung konfiguriert sind:

- Ein primäres Replikat und ein sekundäres Replikat

- Das sekundäre Replikat ist als nicht lesbar konfiguriert (Option **Lesbare sekundäre Rolle** ist auf **Nein** festgelegt).

Beispiel für eine Client-Verbindungszeichenfolge für eine einer Datenbankspiegelung ähnlichen Konfiguration mithilfe von ADO.NET oder SQL Server Native Client:

	Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Weitere Informationen zur Clientkonnektivität finden Sie unter:

- [Verwenden von Schlüsselwörtern für Verbindungszeichenfolgen mit SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Verbinden von Clients mit einer Datenbank-Spiegelungssitzung (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Connecting to Availability Group Listener in Hybrid IT (in englischer Sprache)](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Verfügbarkeitsgruppenlistener, Clientkonnektivität und Anwendungsfailover (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Verwenden von Verbindungszeichenfolgen für die Datenbankspiegelung mit Verfügbarkeitsgruppen](https://technet.microsoft.com/library/hh213417.aspx)

### Netzwerklatenz bei Hybridlösungen

Sie sollten die HADR-Lösung mit der Vorgabe bereitstellen, dass möglicherweise Zeiträume mit hoher Netzwerklatenz zwischen dem lokalen Netzwerk und Azure auftreten. Wenn Sie Replikate in Azure bereitstellen, sollten Sie als Synchronisierungsmodus asynchrone Commits anstelle von synchronen Commits verwenden. Beim Bereitstellen von Datenbankspiegelungsservern sollten Sie sowohl lokal als auch in Azure den Modus für hohe Leistung anstelle des Modus für hohe Sicherheit verwenden.

### Georeplikationssupport

Die Georeplikation von Azure-Datenträgern unterstützt nicht das Speichern von Datendatei und Protokolldatei derselben Datenbank auf unterschiedlichen Datenträgern. Der GRS repliziert Änderungen auf jedem Datenträger unabhängig und asynchron. Durch dieses Vorgehen wird die Schreibreihenfolge auf einem einzelnen Datenträgers in der georeplizierten Kopie sichergestellt, aber nicht die von georeplizierten Kopien mehrerer Datenträger. Wenn Sie eine Datenbank zum Speichern von Datendatei und Protokolldatei auf separaten Datenträgern konfigurieren, enthalten die nach einem Notfall wiederhergestellten Datenträger möglicherweise eine aktuellere Kopie der Datendatei als die Protokolldatei. Dies kann das Write-Ahead-Protokoll in SQL Server und die ACID-Eigenschaften von Transaktionen beschädigen. Wenn Sie die Georeplikation für das Speicherkonto nicht deaktivieren können, sollten Sie alle Daten- und Protokolldateien für eine Datenbank auf dem gleichen Datenträger speichern. Wenn Sie aufgrund der Datenbankgröße mehrere Datenträger verwenden müssen, sollten Sie eine der oben aufgeführten Notfallwiederherstellungslösungen bereitstellen, um die Datenredundanz zu gewährleisten.

## Nächste Schritte

Wenn Sie einen virtuellen Azure-Computer mit SQL Server erstellen möchten, lesen Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-windows-classic-portal-sql.md) nach.

Anleitungen für das Sicherstellen einer optimalen Leistung von SQL Server auf einem virtuellen Azure-Computer finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](virtual-machines-windows-classic-sql-perf.md).

Weitere Informationen zum Ausführen von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server auf virtuellen Azure-Computern](virtual-machines-windows-classic-sql-overview.md).

### Weitere Ressourcen

- [Installieren einer neuen Active Directory-Gesamtstruktur in Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Create WSFC Cluster for AlwaysOn Availability Groups in Microsoft Azure VM (in englischer Sprache)](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

<!---HONumber=AcomDC_0323_2016-->