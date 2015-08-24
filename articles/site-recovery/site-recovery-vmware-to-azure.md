<properties
	pageTitle="Einrichten von Schutz zwischen lokalen virtuellen VMware-Computern oder physischen Servern und Azure" 
	description="Beschreibt, wie Azure Site Recovery zum Koordinieren von Replikation, Failover und Wiederherstellung lokaler virtueller VMware-Computer oder physischer Windows-/Linux-Server in Azure eingerichtet wird." 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="raynew"/>

# Einrichten von Schutz zwischen lokalen virtuellen VMware-Computern oder physischen Servern und Azure

In diesem Artikel wird beschrieben, wie Site Recovery zu folgenden Zwecken bereitgestellt wird:

- **Schützen virtueller VMware-Computer** – Koordinieren von Replikation, Failover und Wiederherstellung von lokalen virtuellen VMware-Computern mit Azure
- **Schützen physischer Server** – Koordinieren von Replikation, Failover und Wiederherstellung von lokalen physischen Windows- und Linux-Servern mit Azure mithilfe des Azure Site Recovery-Diensts

Dieser Artikel enthält eine Übersicht, informiert über die Voraussetzungen für die Bereitstellung und bietet Setupanweisungen. Am Ende des Artikels wird das Replizieren der virtuellen VMware-Computer oder physischen Server in Azure beschrieben. Sollten Probleme auftreten, stellen Sie Ihre Fragen im [Azure Recovery Services-Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Was ist Azure Site Recovery?

Azure Site Recovery unterstützt Ihre Strategie für Geschäftskontinuität und Notfallwiederherstellung, indem Replikation, Failover und Wiederherstellung virtueller Computer und physischer Server aufeinander abgestimmt werden. Computer können in Azure oder in einem lokalen sekundären Rechenzentrum repliziert werden. Erfahren Sie mehr über [Azure Site Recovery](site-recovery-overview.md).

## Wie werden lokale Ressourcen geschützt?

Site Recovery trägt in einer Reihe von [Bereitstellungsszenarien](site-recovery-overview.md) zum Schutz Ihrer lokalen Ressourcen bei, indem Replikation, Failover und Failback aufeinander abgestimmt und vereinfacht werden. Wenn Sie Ihre lokalen virtuellen VMware-Computer oder physischen Windows- oder Linux-Server schützen möchten, kann Sie Site Recovery auf folgende Weise dabei unterstützen:

- Ermöglicht VMware-Benutzern das Replizieren virtueller Computer in Azure.
- Ermöglicht die Replikation von lokalen physischen Servern in Azure.
- Bietet einen zentralen Ort zum Einrichten und Verwalten von Replikation, Failover und Wiederherstellung.
- Bietet einfaches Failover von der lokalen Infrastruktur zu Azure und Failback \(Wiederherstellung\) von Azure zur lokalen Infrastruktur.
- Implementiert Wiederherstellungspläne für vereinfachtes Failover von Workloads, die über mehrere Computer mehrstufig verteilt sind.
- Bietet Konsistenz zwischen mehreren virtuellen Computern, sodass virtuelle Computer und physische Server mit bestimmten Workloads zusammen an einem konsistenten Datenpunkt wiederhergestellt werden können.
- Unterstützt Datenreplikation über das Internet, über eine Site-to-Site-VPN-Verbindung oder über Azure ExpressRoute.
- Ermöglicht die automatische Ermittlung virtueller VMware-Computer.


## Was benötige ich?

Dieses Diagramm zeigt die Bereitstellungskomponenten.

![Neuer Tresor](./media/site-recovery-vmware-to-azure/ASRVMWare_Arch.png)

Sie benötigen Folgendes:

**Komponente** | **Bereitstellung** | **Details**
--- | --- | ---
**Konfigurationsserver** | <p>Die Bereitstellung erfolgt als virtueller Standard-A3-Azure-Computer in demselben Abonnement wie Site Recovery.</p> <p>Sie führen die Einrichtung im Site Recovery-Portal durch.</p> | Dieser Server koordiniert die Kommunikation zwischen geschützten Computern, dem Prozessserver und den Masterzielservern in Azure. Bei einem Failover richtet er die Replikation ein und koordiniert die Wiederherstellung in Azure.
**Masterzielserver** | <p>Die Bereitstellung erfolgt als virtueller Azure-Computer – Entweder als Windows-Server, der auf einem Windows Server 2012 R2-Galerie-Image basiert \(zum Schutz von Windows-Computern\), oder als Linux-Server, der auf einem OpenLogic CentOS 6.6-Galerie-Image basiert \(zum Schutz von Linux-Computern\).</p> <p>Es stehen drei Größenoptionen zur Verfügung – Standard A4, Standard D14 und Standard DS4.<p><p>Der Server ist mit demselben Azure-Netzwerk verbunden wie der Konfigurationsserver.</p><p>Sie führen die Einrichtung im Site Recovery-Portal durch.</p> | <p>Er empfängt replizierte Daten von Ihren geschützten Computern und speichert sie mithilfe der angefügten virtuellen Festplatten, die im Blob-Speicher in Ihrem Azure-Speicherkonto erstellt wurden.</p> <p>Wählen Sie speziell zum Konfigurieren des Schutzes für Workloads, die konsistent hohe Leistung und niedrige Latenz erfordern, "Standard DS4" mit "Premium-Speicherkonto" aus.</p>
**Prozessserver** | <p>Die Bereitstellung erfolgt als lokaler virtueller oder physischer Server mit Windows Server 2012 R2</p> <p>Es wird empfohlen, diesen in demselben Netzwerk und LAN-Segment zu platzieren, in dem sich auch die zu schützenden Computer befinden. Er kann jedoch auch in einem anderen Netzwerk ausgeführt werden, solange die geschützten Computer über L3-Netzwerksichtbarkeit verfügen.<p>Die Einrichtung des Servers und seine Registrierung beim Konfigurationsserver führen Sie im Site Recovery-Portal durch.</p> | <p>Geschützte Computer senden Replikationsdaten an den lokalen Prozessserver. Der Server verfügt über einen datenträgerbasierten Cache zum Zwischenspeichern der empfangenen Replikationsdaten. Er führt eine Reihe von Aktionen für diese Daten aus.</p><p>Er optimiert die Daten, indem er sie vor dem Senden an den Masterzielserver zwischenspeichert, komprimiert und verschlüsselt.</p><p>Er behandelt die Pushinstallation des Mobilitätsdiensts.</p><p>Er führt die automatische Ermittlung virtueller VMware-Computer durch.</p>
**Lokale Computer** | Lokale virtuelle Computer auf einem VMware-Hypervisor oder physische Server, auf denen Windows oder Linux ausgeführt wird. | Sie richten Replikationseinstellungen für virtuelle Computer und Server ein. Failover können für einzelne Computer durchgeführt werden, häufiger erfolgen sie jedoch im Rahmen eines Wiederherstellungsplans mit gemeinsamem Failover mehrerer virtueller Computer.
**Mobilitätsdienst** | <p>Wird auf jedem virtuellen Computer oder physischen Server installiert, den Sie schützen möchten.</p><p>Kann manuell installiert oder vom Prozessserver automatisch per Pushfunktion installiert werden, wenn der Schutz für den Server aktiviert ist. | Beim Mobilitätsdienst werden Daten im Rahmen der anfänglichen Replikation \(Resync\) an den Prozessserver gesendet. Nachdem der Server einen geschützten Zustand erreicht hat \(nach Abschluss der erneuten Synchronisierung\), führt der Mobilitätsdienst eine In-Memory-Erfassung von Schreibvorgängen auf den Datenträger durch und sendet die Daten an den Prozessserver. Anwendungskonsistenz für Windows-Server wird mit dem VSS-Framework erzielt.
**Azure Site Recovery-Tresor** | Führen Sie die Einrichtung durch, nachdem Sie den Site Recovery-Dienst abonniert haben. | Sie registrieren Server in einem Site Recovery-Tresor. Der Tresor koordiniert und orchestriert Datenreplikation, Failover und Wiederherstellung zwischen dem lokalen Standort und Azure.
**Replikationsmechanismus** | <p>**Über das Internet** – Kommuniziert und repliziert Daten von lokalen geschützten Servern und Azure mithilfe eines sicheren SSL/TLS-Kommunikationskanals über eine öffentliche Internetverbindung. Dies ist die Standardoption.</p><p>**VPN/ExpressRoute** – Kommuniziert und repliziert Daten zwischen lokalen Servern und Azure über eine VPN-Verbindung. Sie müssen eine Site-to-Site-VPN-Verbindung oder eine [ExpressRoute](../expressroute-introduction.md)-Verbindung zwischen dem lokalen Standort und Ihrem Azure-Netzwerk einrichten.</p><p>Sie wählen aus, wie die Replizierung während der Site Recovery-Bereitstellung erfolgen soll. Nachdem der Mechanismus konfiguriert wurde, können Sie ihn nicht ändern, ohne den Schutz bereits geschützter Server zu beeinträchtigen.| <p>Für keine der Optionen müssen Sie eingehende Netzwerkports auf geschützten Computern öffnen. Die gesamte Netzwerkkommunikation wird vom lokalen Standort aus initiiert.</p> 

Weitere Informationen über Site Recovery-Komponenten, -Anbieter und -Agents finden Sie unter [Site Recovery-Komponenten](site-recovery-components.md).

## Kapazitätsplanung

Wichtige zu berücksichtigende Punkte:

- **Quellumgebung** – Die VMware-Infrastruktur sowie Einstellungen und Anforderungen des Quellcomputers.
- **Komponentenserver** – Der Prozessserver, Konfigurationsserver und Masterzielserver. 

### Überlegungen zur Quellumgebung

- **Maximale Datenträgergröße** – Derzeit beträgt die maximale Größe eines Datenträgers, der an einen virtuellen Computer angefügt werden kann, 1 TB. Deshalb ist die maximale Größe eines Quelldatenträgers, der repliziert werden kann, ebenfalls auf 1 TB beschränkt.
- **Maximale Größe pro Quellcomputer** – Die maximale Größe eines einzelnen Quellcomputers mit einer für den Masterzielserver bereitgestellten D14-Instanz beträgt 31 TB \(mit 31 Datenträgern\). 
- **Anzahl von Quellen pro Masterzielserver** – Mit einem einzelnen Masterzielserver können mehrere Quellcomputer geschützt werden. Ein einzelner Quellcomputer kann jedoch nicht über mehrere Masterzielserver geschützt werden, da bei der Datenträgerreplikation eine virtuelle Festplatte, die die Größe des Datenträgers widerspiegelt, in Azure-BLOB-Speicher erstellt und als Datenträger mit dem Masterzielserver verbunden wird.  
- **Maximale tägliche Änderungsrate pro Quellcomputer** – Bei der empfohlenen Änderungsrate pro Quellcomputer müssen drei Faktoren berücksichtigt werden. Im Hinblick auf den Zieldatenträger sind für jeden Vorgang auf dem Quellcomputer zwei IOPS auf dem Zieldatenträger erforderlich. Der Grund dafür ist, dass auf dem Zieldatenträger ein Lesevorgang für alte Daten und ein Schreibvorgang für neue Daten erfolgen. 
	- **Vom Prozessserver unterstützte tägliche Änderungsrate** – Ein Quellcomputer kann nicht mehrere Prozessserver umfassen. Ein einzelner Prozessserver unterstützt eine tägliche Änderungsrate von bis zu 1 TB. Daher beträgt die maximale Datenänderungsrate pro Tag, die für einen Quellcomputer unterstützt wird, 1 TB. 
	- **Maximaler vom Zieldatenträger unterstützter Durchsatz ** – Die maximale Codeänderung pro Quelldatenträger darf nicht mehr als 144 GB/Tag \(mit einer Schreibgröße von 8 K\) betragen. Informationen zum Durchsatz und den IOPS des Ziels für verschiedene Schreibgrößen finden Sie in der Tabelle im Abschnitt zum Masterzielserver. Diese Zahl muss durch zwei dividiert werden, da jede Quell-IOPS 2 IOPS auf dem Zieldatenträger generiert. Ziehen Sie beim Konfigurieren des Ziels für das Premium-Speicherkonto den Artikel [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) zurate.
	- **Maximaler vom Speicherkonto unterstützter Durchsatz** – Ein Quellcomputer kann nicht mehrere Speicherkonten umfassen. Da ein Speicherkonto bis zu 20.000 Anforderungen pro Sekunde annimmt und jede Quell-IOPS 2 IOPS auf dem Masterzielserver generiert, wird empfohlen, die Anzahl der IOPS für den Quellcomputer auf 10.000 zu beschränken. Ziehen Sie beim Konfigurieren der Quelle für das Premium-Speicherkonto den Artikel [Skalierbarkeits- und Leistungsziele bei der Verwendung des Premium-Speichers](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts) zurate.

### Überlegungen zu Komponentenservern

In Tabelle 1 sind die Größen der virtuellen Computer für den Konfigurationsserver und Masterzielserver zusammengefasst.

**Komponente** | **Bereitgestellte Azure-Instanzen** | **Kerne** | **Arbeitsspeicher** | **Max. Anzahl Datenträger** | **Datenträgergröße**
--- | --- | --- | --- | --- | ---
Konfigurationsserver | Standard A3 | 4 | 7 GB | 8 | 1023 GB
Masterzielserver | Standard A4 | 8 | 14 GB | 16 | 1023 GB
 | Standard D14 | 16 | 112 GB | 32 | 1023 GB
 | Standard DS4 | 8 | 28 GB | 16 | 1023 GB

**Tabelle 1**

#### Überlegungen zu Prozessservern

Die Größe von Prozessservern hängt im Allgemeinen von der täglichen Änderungsrate aller geschützten Workloads ab. Primäre Überlegungen:

-	Sie benötigen ausreichend Rechenleistung zum Ausführen von Aufgaben wie z. B. Inlinekomprimierung und Verschlüsselung.
-	Der Prozessserver verwendet einen datenträgerbasierten Cache. Stellen Sie sicher, dass der empfohlene Cachespeicherplatz und Datenträgerdurchsatz verfügbar sind, um bei einem Netzwerkengpass oder -ausfall das Speichern geänderter Daten zu erleichtern. 
-	Stellen Sie ausreichend Bandbreite sicher, damit der Prozessserver die Daten auf den Masterzielserver hochladen kann, um kontinuierlichen Datenschutz zu bieten . 

Tabelle 2 enthält eine Zusammenfassung der Richtlinien für den Prozessserver.

**Datenänderungsrate** | **CPU** | **Arbeitsspeicher** | **Größe des Cachedatenträgers**| **Durchsatz des Cachedatenträgers** | **Eingangs-/Ausgangsbandbreite**
--- | --- | --- | --- | --- | ---
\< 300 GB | 4 vCPUs \(2 Sockets * 2 Kerne mit 2,5 GHz\) | 4 GB | 600 GB | 7 bis 10 MB pro Sekunde | 30 Mbit/s bzw. 21 Mbit/s
300 bis 600 GB | 8 vCPUs \(2 Sockets * 4 Kerne mit 2,5 GHz\) | 6 GB | 600 GB | 11 bis 15 MB pro Sekunde | 60 Mbit/s bzw. 42 Mbit/s
600 GB bis 1 TB | 12 vCPUs \(2 Sockets * 6 Kerne mit 2,5 GHz\) | 8 GB | 600 GB | 16 bis 20 MB pro Sekunde | 100 Mbit/s bzw. 70 Mbit/s
\> 1 TB | Bereitstellen eines weiteren Prozessservers | | | | 

**Tabelle 2**

Hinweis:

- Die Eingangsbandbreite ist die Downloadbandbreite \(Intranet zwischen Quellcomputer und Prozessserver\).
- Die Ausgangsbandbreite ist die Uploadbandbreite \(Internet zwischen dem Prozessserver und dem Masterzielserver\). Bei den Werten für die Ausgangsbandbreite wird eine durchschnittliche Prozessserverkomprimierung von 30 % angenommen.
- Für alle Prozessserver wird als Cachedatenträger ein eigener Betriebssystemdatenträger von mindestens 128 GB empfohlen.
- Als Benchmark für den Durchsatz des Cachedatenträgers wurde folgender Speicher verwendet: 8 SAS-Laufwerke mit 10.000 U/min und RAID 10-Konfiguration.

#### Überlegungen zu Konfigurationsservern

Jeder Konfigurationsserver unterstützt bis zu 100 Quellcomputer mit 3-4 Volumes. Wenn diese Werte überschritten werden, wird die Bereitstellung eines weiteren Konfigurationsservers empfohlen. Die Standardeigenschaften der virtuellen Computer des Konfigurationsservers sind in Tabelle 1 angegeben.

#### Überlegungen zu Masterzielservern und Speicherkonten

Der Speicher für jeden Masterzielserver besteht aus einem Betriebssystemdatenträger, einem Aufbewahrungsvolume und Datenträgern. Das Aufbewahrungslaufwerk verwaltet das Journal der Datenträgeränderungen für die im Site Recovery-Portal definierte Aufbewahrungsdauer. Die Eigenschaften virtueller Computer des Masterzielservers sind in Tabelle 1 angegeben. In Tabelle 3 wird die Verwendung der Datenträger von A4 gezeigt.

**Instanz** | **Betriebssystemdatenträger** | **Aufbewahrung** | **Datenträger**
--- | --- | --- | ---
 | | **Aufbewahrung** | **Datenträger**
Standard A4 | 1 Datenträger \(1 * 1023 GB\) | 1 Datenträger \(1 * 1023 GB\) | 15 Datenträger \(15 * 1023 GB\)
Standard D14 | 1 Datenträger \(1 * 1023 GB\) | 1 Datenträger \(1 * 1023 GB\) | 31 Datenträger \(15 * 1023 GB\)
Standard DS4 | 1 Datenträger \(1 * 1023 GB\) | 1 Datenträger \(1 * 1023 GB\) | 15 Datenträger \(15 * 1023 GB\)

**Tabelle 3**

Die Kapazitätsplanung für den Masterzielserver hängt von folgenden Faktoren ab:

- Leistung und Einschränkungen des Azure-Speichers
	- Die maximale Anzahl der Datenträger mit hoher Auslastung für einen virtuellen Computer im Standard-Tarif liegt bei etwa 40 Datenträgern \(20.000/500 IOPS pro Datenträger\) in einem einzigen Speicherkonto. Weitere Informationen finden Sie unter [Skalierbarkeitsziele für Standardspeicherkonten](../storage/storage-scalability-targets.md#scalability-targets-for-standard-storage-accounts). Weitere Informationen zum Premium-Speicherkonto finden Sie entsprechend unter [Skalierbarkeitsziele für Premium Storage-Konten](../storage/storage-scalability-targets.md#scalability-targets-for-premium-storage-accounts).
-	Tägliche Änderungsrate 
-	Speicher des Aufbewahrungsvolumes

Beachten Sie Folgendes:

- Ein Quellcomputer kann nicht mehrere Speicherkonten umfassen. Dies gilt für die Datenträger, die für die ausgewählten Speicherkonten verwendet werden, wenn Sie den Schutz konfigurieren. Der Betriebssystem- und Aufbewahrungsdatenträger werden in der Regel automatisch für das bereitgestellte Speicherkonto verwendet.
- Das erforderliche Aufbewahrungsspeichervolume hängt von der täglichen Änderungsrate und der Anzahl der Aufbewahrungstage ab. Erforderlicher Aufbewahrungsspeicher pro Masterzielserver = gesamte Codeänderung von Quellcomputer pro Tag * Anzahl Aufbewahrungstage. 
- Jeder Masterzielserver verfügt über nur ein Aufbewahrungsvolume. Das Datenträgervolume wird von den an den Masterzielserver angefügten Datenträgern gemeinsam verwendet. Beispiel:
	- Wenn ein Quellcomputer über 5 Datenträger verfügt und jeder Datenträger auf dem Quellcomputer 120 IOPS \(mit einer Größe von 8K\) generiert, ergibt dies 240 IOPS pro Datenträger \(2 Vorgänge auf dem Zieldatenträger pro Quell-E/A\). Der Wert von 240 IOPS liegt innerhalb der Azure-Beschränkung von 500 IOPS pro Datenträger.
	- Auf dem Datenträger ergeben sich daraus 120 * 5 = 600 IOPS, und dies kann zu einem Engpass führen. In diesem Fall empfiehlt es sich, dem Aufbewahrungsvolume weitere Datenträger hinzuzufügen und dieses als RAID-Stripekonfiguration auf mehrere Laufwerke zu verteilen. Dadurch wird die Leistung verbessert, da die IOPS auf mehrere Laufwerke verteilt sind. Dem Aufbewahrungsvolume sollte die folgende Anzahl von Laufwerken hinzugefügt werden:
		- IOPS gesamt aus der Quellumgebung / 500.
		- Codeänderung gesamt pro Tag aus der Quellumgebung \(unkomprimiert\) / 287 GB. 287 GB beträgt der maximale Durchsatz, der von einem Zieldatenträger pro Tag unterstützt wird. Dieses Maß variiert basierend auf der Schreibgröße mit einem Faktor von 8K, da in diesem Fall als Schreibgröße 8K angenommen wird. Wenn z. B. die Schreibgröße 4K beträgt, ist der Durchsatz 287/2. Und wenn die Schreibgröße 16K beträgt, ist der Durchsatz 287*2.
- Erforderliche Anzahl von Speicherkonten = Quell-IOPS insgesamt /10.000.


## Vorbereitung

**Komponente** | **Anforderungen** | **Details**
--- | --- | --- 
**Azure-Konto** | Sie benötigen ein [Microsoft Azure](http://azure.microsoft.com/)-Konto. Für den Einstieg steht ein [kostenloses Testkonto](pricing/free-trial/) zur Verfügung.
**Azure-Speicher** | <p>Sie benötigen ein Azure-Speicherkonto, um replizierte Daten zu speichern.</p><p>Das Konto sollte entweder ein [georedundantes Standardspeicherkonto](../storage/storage-redundancy.md#geo-redundant-storage) oder [Premium-Speicherkonto](../storage/storage-premium-storage-preview-portal.md) sein.</p><p>Es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden und dem gleichen Abonnement zugeordnet sein.</p><p>Weitere Informationen finden Sie unter [Einführung in Microsoft Azure Storage](../storage/storage-introduction.md)</p>
**Azure Virtual Network** | Sie benötigen ein virtuelles Azure-Netzwerk, in dem der Konfigurationsserver und der Masterzielserver bereitgestellt werden. Es muss in demselben Abonnement und in derselben Region enthalten sein wie der Azure Site Recovery-Tresor. Wenn Sie Daten über eine ExpressRoute- oder VPN-Verbindung replizieren möchten, muss das virtuelle Azure-Netzwerk mit Ihrem lokalen Netzwerk per ExpressRoute-Verbindung oder Standort-zu-Standort-VPN verbunden sein.
**Azure-Ressourcen** | Stellen Sie sicher, dass Sie genügend Azure-Ressourcen für die Bereitstellung aller Komponenten besitzen. Weitere Informationen finden Sie unter [Beschränkungen des Azure-Abonnements](../azure-subscription-service-limits.md).
**Azure Virtual Machines** | <p>Virtuelle Computer, die Sie schützen möchten, sollten die [Voraussetzungen für Azure](site-recovery-best-practices.md) erfüllen.</p><p>**Anzahl der Datenträger** – Auf einem einzelnen geschützten Server werden maximal 31 Datenträger unterstützt.</p><p>**Datenträgergrößen** – Die Kapazität eines einzelnen Datenträgers sollte nicht größer als 1023 GB sein.</p><p>**Clustering** – Gruppierte Server werden nicht unterstützt.</p><p>**Starten** – Das Starten im UEFI-Modus \(Unified Extensible Firmware Interface\)/EFI-Modus \(Extensible Firmware Interface\) wird nicht unterstützt.</p><p>**Volumes** – Mit Bitlocker verschlüsselte Volumes werden nicht unterstützt.</p><p> **Servernamen** – Die Namen müssen zwischen 1 und 63 Zeichen \(Buchstaben, Zahlen und Bindestriche\) enthalten. Der Name muss mit einem Buchstaben oder einer Zahl beginnen und mit einem Buchstaben oder einer Zahl enden. Nachdem ein Computer geschützt wurde, können Sie den Azure-Namen ändern.</p>
**Konfigurationsserver** | <p>Ein virtueller Standard-A3-Computer wird basierend auf einem Azure Site Recovery Windows Server 2012 R2-Galerie-Image in Ihrem Abonnement für den Konfigurationsserver erstellt. Er wird als erste Instanz in einem neuen Clouddienst erstellt. Wenn Sie „Öffentliches Internet“ als Verbindungstyp für den Konfigurationsserver wählen, wird der Clouddienst mit einer reservierten öffentlichen IP-Adresse erstellt.</p><p>Der Installationspfad sollte nur englische Zeichen enthalten.</p>
**Masterzielserver** | <p>Virtueller Azure-Computer, Standard A4, D14 oder DS4.</p><p>Der Installationspfad darf nur englische Zeichen enthalten. Für einen Masterzielserver unter Linux sollte der Pfad z. B. **/usr/local/ASR** lauten.</p></p>
**Prozessserver** | <p>Sie können den Prozessserver auf einem physischen oder virtuellen Computer mit Windows Server 2012 R2 mit den neuesten Updates bereitstellen. Installieren Sie auf Laufwerk C:/.</p><p>Es wird empfohlen, dass Sie den Server im gleichen Netzwerk und Subnetz wie die Computer platzieren, die Sie schützen möchten.</p><p>Installieren Sie VMware vSphere CLI 5.5.0 auf dem Prozessserver. Die Komponente VMware vSphere CLI ist auf dem Prozessserver erforderlich, um virtuelle Computer, die von einem vCenter-Server verwaltet werden, oder virtuelle Computer, die auf einem ESXi-Host ausgeführt werden, zu ermitteln.</p><p>Der Installationspfad darf nur englische Zeichen enthalten.</p>
**VMware** | <p>Ein VMware vCenter-Server zur Verwaltung Ihrer VMware vSphere-Hypervisoren. Auf ihm sollte vCenter, Version 5.1 oder 5.5, mit den neuesten Updates ausgeführt werden.</p><p>Einer oder mehrere vSphere-Hypervisoren mit virtuellen VMware-Computern, die Sie schützen möchten. Auf dem Hypervisor sollte ESX/ESXi, Version 5.1 oder 5.5, mit den neuesten Updates ausgeführt werden.</p><p>Auf den virtuellen VMware-Computern müssen die VMware-Tools installiert sein und ausgeführt werden.</p>
**Windows-Computer** | <p>Für geschützte physische Server oder virtuelle VMware-Computer unter Windows gelten eine Reihe von Anforderungen.</p><p>Ein unterstütztes 64-Bit-Betriebssystem: **Windows Server 2012 R2**, **Windows Server 2012** oder **Windows Server 2008 R2 mit mindestens SP1**.</p><p>Der Hostname, die Bereitstellungspunkte, die Gerätenamen und der Windows-Systempfad \(z. B.: "C:\\Windows"\) dürfen nur englische Zeichen enthalten.</p><p>Das Betriebssystem sollte auf Laufwerk C:\\ installiert werden.</p><p>Nur Basisdatenträger werden unterstützt. Dynamische Datenträger werden nicht unterstützt.</p><p><Firewall rules on protected machines should allow them to reach the configuration and master target servers in Azure.p><p>Für die Pushinstallation des Mobilitätsdiensts auf Windows-Servern müssen Sie ein Administratorkonto angeben \(lokaler Administrator auf dem Windows-Computer\). Wenn das bereitgestellte Konto kein Domänenkonto ist, müssen Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer deaktivieren. Fügen Sie hierzu unter "HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System" den Registrierungseintrag "LocalAccountTokenFilterPolicy DWORD" mit dem Wert 1 hinzu. Um den Registrierungseintrag über eine Befehlszeilenschnittstelle hinzuzufügen, öffnen Sie eine CMD- oder PowerShell-Befehlszeilenschnittstelle, und geben Sie **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`** ein. [Weitere Informationen](https://msdn.microsoft.com/library/aa826699.aspx) über die Zugriffssteuerung.</p><p>Wenn virtuelle Windows-Computer in Azure nach einem Failover mit Remotedesktop eine Verbindung herstellen sollen, stellen Sie sicher, dass Remotedesktop für den lokalen Computer aktiviert ist. Wenn Sie die Verbindung nicht über ein VPN herstellen, sollten die Firewallregeln Remotedesktopverbindungen über das Internet ermöglichen.</p>
**Linux-Computer** | <p> Ein unterstütztes 64-Bit-Betriebssystem: **Centos 6.4, 6.5, 6.6**; **Oracle Enterprise Linux 6.4, 6.5, auf dem entweder der Red Hat-kompatible Kernel oder UEK3 \(Unbreakable Enterprise Kernel Release 3\) ausgeführt wird**, **SUSE Linux Enterprise Server 11 SP3**.</p><p>Die Firewallregeln auf geschützten Computern sollten diesen den Zugriff auf den Konfigurationsserver und Masterzielserver in Azure ermöglichen.</p><p>„/etc/hosts“-Dateien auf geschützten Computern sollten Einträge enthalten, die den Namen des lokalen Hosts IP-Adressen zuordnen, die allen NICs zugeordnet sind.</p><p>Wenn Sie nach einem Failover eine Verbindung mit einem virtuellen Azure-Computer unter Linux über einen Secure Shell-Client \(SSH\) herstellen möchten, sollten Sie sicherstellen, dass der Secure Shell-Dienst auf dem geschützten Computer beim Systemstart automatisch gestartet wird und dass die Firewallregeln eine SSH-Verbindung zulassen.</p><p>Der Hostname, Bereitstellungspunkte, Gerätenamen und Linux-Systempfade und -Dateinamen \(z. B. /etc/, /usr\) sollten nur englische Zeichen umfassen.</p><p>Der Schutz kann für lokale Computer mit dem folgenden Speicher aktiviert werden: -<br>Dateisystem: EXT3, ETX4, ReiserFS, XFS<br>Multipath-Softwaregeräte-Mapper \(multipath\)<br>Volume-Manager: LVM2<br>Physische Server mit HP CCISS-Controllerspeicher werden nicht unterstützt.</p>
**Drittanbieter** | Einige Bereitstellungskomponenten in diesem Szenario sind von Drittanbietersoftware abhängig, um ordnungsgemäß zu funktionieren. Eine vollständige Liste finden Sie unter [Hinweise und Informationen zu Drittanbietersoftware](#third-party).

## Bereitstellung

In der Grafik sind die Bereitstellungsschritte zusammengefasst.

![Bereitstellungsschritte](./media/site-recovery-vmware-to-azure/VMWare2AzureSteps.png)

## Typ der Netzwerkkonnektivität

Es gibt zwei Möglichkeiten, die Netzwerkkonnektivität zwischen Ihrem lokalen Standort und dem virtuellen Azure-Netzwerk zu konfigurieren, auf dem Ihre Infrastrukturkomponenten \(Konfigurationsserver, Masterzielserver\) bereitgestellt werden. Sie müssen entscheiden, welche Option für die Netzwerkkonnektivität Sie verwenden möchten, bevor Sie den Konfigurationsserver bereitstellen können. Dies ist eine Entscheidung zur Bereitstellungszeit, die später nicht geändert werden kann.

**Öffentliches Internet:** Die Kommunikation und Replikation von Daten zwischen den lokalen Servern \(Prozessserver, geschützte Server\) und den Komponentenservern der Azure-Infrastruktur \(Konfigurationsserver, Masterzielserver\) erfolgt über eine sichere SSL/TLS-Verbindung. Sie verläuft vom lokalen Server zu den öffentlichen Endpunkten auf dem Konfigurationsserver und dem Masterzielserver. \(Die einzige Ausnahme ist die Verbindung zwischen dem Prozessserver und dem Masterzielserver auf TCP-Port 9080, die unverschlüsselt ist. Über diese Verbindung werden nur Steuerinformationen ausgetauscht, die sich auf das Replikationsprotokoll für die Einrichtung der Replikation beziehen.\)

![Bereitstellungsdiagramm: Internet](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentInternet.png)

**VPN:** Die Kommunikation und Replikation von Daten zwischen den lokalen Servern \(Prozessserver, geschützte Server\) und den Komponentenservern der Azure-Infrastruktur \(Konfigurationsserver, Masterzielserver\) erfolgt über eine VPN-Verbindung zwischen Ihrem lokalen Netzwerk und dem virtuellen Azure-Netzwerk, in dem der Konfigurationsserver und die Masterzielserver bereitgestellt werden. Stellen Sie sicher, dass Ihr lokales Netzwerk mit dem virtuellen Azure-Netzwerk über eine ExpressRoute-Verbindung oder eine Standort-zu-Standort-VPN-Verbindung verbunden ist.

![Bereitstellungsdiagramm: VPN](./media/site-recovery-vmware-to-azure/ASRVmware_deploymentVPN.png)


## Schritt 1: Erstellen eines Tresors

1. Melden Sie sich im [Verwaltungsportal](https://portal.azure.com) an.


2. Erweitern Sie **Data Services** \> **Recovery Services**, und klicken Sie auf **Site Recovery-Tresor**.


3. Klicken Sie auf **Neu erstellen** \> **Schnellerfassung**.

4. Geben Sie unter **Name** einen Anzeigenamen ein, über den der Tresor identifiziert wird.

5. Wählen Sie unter **Region** die geografische Region für den Tresor aus. Sie finden eine Liste der unterstützten Regionen unter Geografische Verfügbarkeit auf der Seite [Azure Site Recovery – Preisdetails](pricing/details/site-recovery/)

6. Klicken Sie auf **Tresor erstellen**.

	![Neuer Tresor](./media/site-recovery-vmware-to-azure/ASRVMWare_CreateVault.png)

Überprüfen Sie auf der Statusleiste, ob der Tresor erfolgreich erstellt wurde. Der Tresor wird auf der Hauptseite von **Recovery Services** als **Aktiv** angegeben.

## Schritt 2: Bereitstellen eines Konfigurationsservers

### Konfigurieren der Servereinstellungen

1. Klicken Sie auf der Seite **Recovery Services** auf den Tresor, um die Seite „Schnellstart“ zu öffnen. Schnellstart kann auch jederzeit über das Symbol geöffnet werden.

	![Schnellstart-Symbol](./media/site-recovery-vmware-to-azure/ASRVMWare_QuickStartIcon.png)

2. Wählen Sie in der Dropdownliste den Eintrag **Zwischen einem lokalen Standort mit VMware-/physischen Servern und Azure**.
3. Klicken Sie unter **Zielressourcen \(Azure-Ressourcen\) vorbereiten** auf **Konfigurationsserver bereitstellen**.

	![Konfigurationsserver bereitstellen](./media/site-recovery-vmware-to-azure/ASRVMWare_DeployCS2.png)

4. Geben Sie unter **Details des neuen Konfigurationsservers** Folgendes an:

	- Einen Namen für den Konfigurationsserver und die Anmeldeinformationen für die Verbindung mit dem Server.
	- Wählen Sie im Dropdownmenü als Netzwerkkonnektivitätstyp die Option „Öffentliches Internet“ oder „VPN“.[AZURE.NOTE]Diese Einstellung ist eine Entscheidung, die zur Bereitstellungszeit getroffen werden muss und später nicht geändert werden kann.  
	- Wählen Sie das Azure-Netzwerk aus, in dem sich der Server befinden soll. Wenn Sie VPN als Netzwerkkonnektivitätstyp angegeben haben, sollten Sie sicherstellen, dass dieses virtuelle Azure-Netzwerk mit Ihrem lokalen Standort über eine ExpressRoute-Verbindung oder ein Standort-zu-Standort-VPN verbunden ist.
	- Geben Sie die interne IP-Adresse und das Subnetz an, die bzw. das dem Server zugewiesen werden soll. Beachten Sie, dass in jedem Subnetz die ersten vier IP-Adressen für die interne Verwendung durch Azure reserviert sind. Verwenden Sie eine beliebige andere verfügbare IP-Adresse.
	
	![Konfigurationsserver bereitstellen](./media/site-recovery-vmware-to-azure/ASRVMware_CSDetails2.png)

5. Wenn Sie auf **OK** klicken, wird ein virtueller Standard-A3-Computer basierend auf einem Azure Site Recovery Windows Server 2012 R2-Galerie-Image in Ihrem Abonnement für den Konfigurationsserver erstellt. Er wird als erste Instanz in einem neuen Clouddienst erstellt. Wenn Sie als Netzwerkkonnektivitätstyp „Öffentliches Internet“ angegeben haben, wird der Clouddienst mit einer reservierten öffentlichen IP-Adresse erstellt. Sie können den Fortschritt auf der Registerkarte **Aufträge** überwachen.

	![Fortschritt überwachen](./media/site-recovery-vmware-to-azure/ASRVMWare_MonitorConfigServer.png)

6.  **Dieser Schritt gilt nur, wenn Sie als Konnektivitätstyp „Öffentliches Internet“ gewählt haben.** Nach dem Bereitstellen des Konfigurationsservers notieren Sie die ihm zugewiesene öffentliche IP-Adresse auf der Seite **Virtuelle Computer** im Azure-Portal. Notieren Sie dann auf der Registerkarte **Endpunkte** den öffentlichen HTTPS-Port, der dem privaten Port 443 zugeordnet ist. Diese Informationen benötigen Sie später, wenn Sie den Masterzielserver und den Prozessserver beim Konfigurationsserver registrieren. Der Konfigurationsserver wird mit den folgenden Endpunkten bereitgestellt:

	- HTTPS: Der öffentliche Port wird zum Koordinieren der Kommunikation zwischen Komponentenservern und Azure über das Internet verwendet. Der private Port 443 wird zum Koordinieren der Kommunikation zwischen Komponentenservern und Azure über ein VPN verwendet.
	- Benutzerdefiniert: Der öffentliche Port wird für die Kommunikation mit dem Failbacktool über das Internet
	- verwendet. Der private Port 9443 wird für die Kommunikation mit dem Failbacktool über ein VPN verwendet.
	- PowerShell: privater Port 5986
	- Remotedesktop: privater Port 3389
	
	![VM-Endpunkte](./media/site-recovery-vmware-to-azure/ASRVMWare_VMEndpoints.png)

    >[AZURE.WARNING]Sie dürfen die öffentliche oder private Portnummer der Endpunkte, die während der Bereitstellung des Konfigurationsservers erstellt wurden, nicht ändern oder löschen.

Der Konfigurationsserver wird in einem automatisch erstellten Azure-Clouddienst mit einer reservierten IP-Adresse bereitgestellt. Die reservierte Adresse ist erforderlich, um sicherzustellen, dass die IP-Adresse des Konfigurationsserver-Clouddiensts nach Neustarts der virtuellen Computer \(einschließlich des Konfigurationsservers\) im Clouddienst gleich bleibt. Wenn der Konfigurationsserver außer Betrieb gesetzt wird und die Reservierung der öffentlichen IP-Adresse nicht manuell rückgängig gemacht wird, bleibt die öffentliche IP-Adresse reserviert. Standardmäßig sind bis zu 20 reservierte öffentliche IP-Adressen pro Abonnement zulässig. [Weitere Informationen](../virtual-network/virtual-networks-reserved-private-ip.md) über reservierte IP-Adressen.

### Registrieren des Konfigurationsservers im Tresor

1. Klicken Sie auf der Seite **Schnellstart** auf **Zielressourcen vorbereiten** \> **Registrierungsschlüssel herunterladen**. Die Schlüsseldatei wird automatisch generiert. Sie ist nach der Erstellung 5 Tage lang gültig. Kopieren Sie sie auf den Konfigurationsserver.
2. Wählen Sie unter **Virtuelle Computer** den Konfigurationsserver aus der Liste der virtuellen Computer aus. Öffnen Sie die Registerkarte **Dashboard**, und klicken Sie auf **Verbinden**. **Öffnen** Sie die heruntergeladene RDP-Datei, um sich über Remotedesktop beim Konfigurationsserver anzumelden. Wenn Ihr Konfigurationsserver in einem VPN-Netzwerk bereitgestellt wird, verwenden Sie die interne IP-Adresse \(die Sie beim Bereitstellen des Konfigurationsservers angegeben haben und die auch auf der Virtual Machines-Dashboardseite für den virtuellen Konfigurationsserver angezeigt wird\) des Konfigurationsservers, um von Ihrem lokalen Netzwerk aus per Remotedesktop eine Verbindung damit herzustellen. Wenn Sie sich zum ersten Mal anmelden, wird der Azure Site Recovery-Assistent zum Einrichten des Konfigurationsservers automatisch ausgeführt.

	![Registrierung](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationSplashscreen.png)

3. Klicken Sie unter **Installation von Drittanbietersoftware** auf **Ich stimme zu**, um MySQL herunterzuladen und zu installieren.

	![MySQL-Installation](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLEULA.png)

4. Erstellen Sie unter **MySQL-Server-Details** Anmeldeinformationen, um sich bei MySQL Server anzumelden.

	![MySQL-Anmeldeinformationen](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationMySQLPWD.png)

5. Geben Sie unter **Interneteinstellungen** an, wie der Konfigurationsserver eine Verbindung mit dem Internet herstellt. Beachten Sie Folgendes:

	- Wenn Sie einen benutzerdefinierten Proxy verwenden möchten, sollten Sie diesen vor der Installation des Anbieters einrichten.
	- Wenn Sie auf **Weiter** klicken, wird ein Test ausgeführt, um die Proxyverbindung zu überprüfen.
	- Wenn Sie einen benutzerdefinierten Proxy verwenden oder wenn der Standardproxy Authentifizierung erfordert, müssen Sie die Proxydetails eingeben, einschließlich Adresse, Port und Anmeldeinformationen.
	- Über den Proxy sollte Zugriff auf die folgenden URLs möglich sein:
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- Wenn Sie auf IP-Adressen basierende Firewallregeln verwenden, sollten Sie sicherstellen, dass die Regeln die Kommunikation zwischen dem Konfigurationsserver und den unter [IP-Bereiche des Azure-Rechenzentrums](https://msdn.microsoft.com/de-de/library/azure/dn175718.aspx) beschriebenen IP-Adressen sowie das HTTPS \(443\)-Protokoll zulassen. Fügen Sie die IP-Adressbereiche der zu verwendenden Azure-Region sowie die IP-Adressbereiche der westlichen USA einer Positivliste hinzu.

	![Proxyregistrierung](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationProxy.png)

6. Geben Sie unter **Einstellungen für die Lokalisierung von Anbieterfehlermeldungen** an, in welcher Sprache Fehlermeldungen angezeigt werden sollen.

	![Fehlermeldungsregistrierung](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationLocale.png)

7. Suchen Sie auf der Seite **Azure Site Recovery-Registrierung** die Schlüsseldatei, die Sie auf den Server kopiert haben, und wählen Sie sie aus.

	![Schlüsseldateiregistrierung](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationVault.png)

8. Wählen Sie auf der Abschlussseite des Assistenten die folgenden Optionen aus:

	- Wählen Sie **Dialogfeld für die Kontoverwaltung öffnen** aus, um anzugeben, dass nach dem Beenden des Assistenten das Dialogfeld „Konten verwalten“ geöffnet werden soll.
	- Wählen Sie **Desktopsymbol für Cspsconfigtool erstellen** aus, um auf dem Konfigurationsserver eine Desktopverknüpfung hinzuzufügen, damit Sie jederzeit das Dialogfeld **Konten verwalten** öffnen können, ohne den Assistenten erneut auszuführen.

	![Registrierung abschließen](./media/site-recovery-vmware-to-azure/ASRVMWare_RegistrationFinal.png)

9. Klicken Sie auf **Fertig stellen**, um den Assistenten abzuschließen. Eine Passphrase wird generiert. Kopieren Sie sie an einen sicheren Ort. Sie benötigen diese Informationen, um den Prozess- und den Masterzielserver beim Konfigurationsserver zu authentifizieren und zu registrieren. Außerdem dienen sie zur Sicherstellung der Kanalintegrität bei der Kommunikation mit dem Konfigurationsserver. Sie können die Passphrase neu generieren. In diesem Fall müssen Sie jedoch den Masterzielserver und den Prozessserver anhand der neuen Passphrase neu registrieren.

	![Passphrase](./media/site-recovery-vmware-to-azure/passphrase.png)

Nach der Registrierung wird der Konfigurationsserver auf der Seite **Konfigurationsserver** im Tresor aufgeführt.

### Einrichten und Verwalten von Konten

Site Recovery fordert während der Bereitstellung Anmeldeinformationen für die folgenden Aktionen an:

- Wenn Sie einen vCenter-Server für die automatisierte Ermittlung von virtuellen Computern hinzufügen, die vom vCenter-Server verwaltet werden. Ein vCenter-Konto ist für die automatisierte Ermittlung von virtuellen Computern erforderlich.
- Wenn Sie zu schützende Computer hinzufügen, damit Site Recovery den Mobilitätsdienst auf ihnen installieren kann.

Nachdem Sie den Konfigurationsserver registriert haben, können Sie das Dialogfeld **Konten verwalten** öffnen, um Konten hinzuzufügen und zu verwalten, die für diese Aktionen verwendet werden sollen. Es gibt mehrere Möglichkeiten, dies zu tun:

- Öffnen Sie die Verknüpfung, die Sie auf der letzten Seite des Setups des Konfigurationsservers für das Dialogfeld erstellt haben \(cspsconfigtool\).
- Öffnen Sie das Dialogfeld beim Beenden der Konfigurationsserversetups.

1. Klicken Sie unter **Konten verwalten** auf **Konto hinzufügen**. Sie können auch vorhandene Konten ändern und löschen.

	![Konten verwalten](./media/site-recovery-vmware-to-azure/ASRVMWare_ManageAccount.png)

2. Geben Sie unter **Kontodetails** einen in Azure zu verwendenden Kontonamen und Anmeldeinformationen \(Domäne/Benutzername\) an.

	![Konten verwalten](./media/site-recovery-vmware-to-azure/ASRVMWare_AccountDetails.png)

### Herstellen der Verbindung mit dem Konfigurationsserver 

Es gibt zwei Möglichkeiten zum Herstellen der Verbindung mit dem Konfigurationsserver:

- Über eine Site-to-Site-VPN-Verbindung oder ExpressRoute-Verbindung
- Über das Internet 

Beachten Sie Folgendes:

- Eine Internetverbindung verwendet die Endpunkte des virtuellen Computers in Verbindung mit der öffentlichen virtuellen IP-Adresse des Servers.
- Bei einer VPN-Verbindung wird die interne IP-Adresse des Servers zusammen mit den Ports des privaten Endpunkts verwendet.
- Es kann nur einmal entschieden werden, ob die Verbindung \(zum Senden von Steuerungs- und Replikationsdaten\) der lokalen Server mit den verschiedenen unter Azure ausgeführten Komponentenservern \(Konfigurationsserver, Masterzielserver\) über ein VPN oder über das Internet erfolgt. Sie können diese Einstellung später nicht ändern. Wenn Sie die Einstellung ändern, müssen Sie das Szenario erneut bereitstellen und die Computer erneut schützen.  


## Schritt 3: Bereitstellen des Masterzielservers

1. Klicken Sie unter **Zielressourcen \(Azure-Ressourcen\) vorbereiten** auf **Masterzielserver bereitstellen**.
2. Geben Sie die Details und Anmeldeinformationen für den Masterzielserver an. Der Server wird in demselben Azure-Netzwerk wie der Konfigurationsserver bereitgestellt, bei dem er registriert ist. Wenn Sie zum Abschluss klicken, wird ein virtueller Azure-Computer mit einem Windows- oder Linux-Galerie-Image erstellt.

	![Zielservereinstellungen](./media/site-recovery-vmware-to-azure/ASRVMWare_TSDetails.png)

Beachten Sie, dass in jedem Subnetz die ersten vier IP-Adressen für die interne Verwendung durch Azure reserviert sind. Geben Sie eine beliebige andere verfügbare IP-Adresse an.

>[AZURE.NOTE]Wählen Sie bei der Konfiguration des Schutzes für Workloads, die konsistent hohe E/A-Leistung und niedrige Latenz erfordern, "Standard DS4" mit [Premium-Speicherkonto](../storage/storage-premium-storage-preview-portal.md) aus, um E/A-intensive Workloads zu hosten.


3. Ein virtueller Computer des Windows-Masterzielservers mit den folgenden Endpunkten wird erstellt \(öffentliche Endpunkte werden nur erstellt, wenn der Bereitstellungstyp „Öffentliches Internet“ lautet\):

	- Benutzerdefiniert: Der öffentliche Port wird vom Prozessserver zum Senden von Replikationsdaten über das Internet verwendet. Der private Port 9443 wird vom Prozessserver zum Senden von Replikationsdaten an den Masterzielserver über ein VPN verwendet.
	- Benutzerdefiniert1: Der öffentliche Port wird vom Prozessserver zum Senden von Steuerungsmetadaten über das Internet verwendet. Der private Port 9080 wird vom Prozessserver zum Senden von Steuerungsmetadaten an den Masterzielserver über ein VPN verwendet.
	- PowerShell: privater Port 5986
	- Remotedesktop: privater Port 3389

4. Ein virtueller Computer des Linux-Masterzielservers mit den folgenden Endpunkten wird erstellt \(öffentliche Endpunkte werden nur erstellt, wenn der Bereitstellungstyp „Öffentliches Internet“ lautet\):

	- Benutzerdefiniert: Der öffentliche Port wird vom Prozessserver zum Senden von Replikationsdaten über das Internet verwendet. Der private Port 9443 wird vom Prozessserver zum Senden von Replikationsdaten an den Masterzielserver über ein VPN verwendet.
	- Benutzerdefiniert1: Der öffentliche Port wird vom Prozessserver zum Senden von Steuerungsmetadaten über das Internet verwendet. Der private Port 9080 wird vom Prozessserver zum Senden von Steuerungsdaten an den Masterzielserver über ein VPN verwendet.
	- SSH: Privater Port 22

    >[AZURE.WARNING]Sie dürfen die öffentliche oder private Portnummer der Endpunkte, die während der Bereitstellung des Masterzielservers erstellt wurden, nicht ändern oder löschen.

5. Warten Sie unter **Virtuelle Computer**, bis der virtuelle Computer gestartet wird.

	- Wenn Sie den Server mit Windows konfiguriert haben, notieren Sie die Details für Remotedesktop.
	- Wenn Sie ihn mit Linux konfiguriert haben und eine Verbindung über VPN herstellen, notieren Sie die interne IP-Adresse des virtuellen Computers. Notieren Sie die öffentliche IP-Adresse, wenn Sie eine Verbindung über das Internet herstellen.

6.  Melden Sie sich beim Server an, um die Installation abzuschließen und den Server beim Konfigurationsserver zu registrieren.
7.  Wenn Sie Windows verwenden:

	1. Initiieren Sie eine Remotedesktopverbindung mit dem virtuellen Computer. Beim ersten Anmelden wird ein Skript in einem PowerShell-Fenster ausgeführt. Schließen Sie es nicht. Bei Fertigstellung wird das Host-Agent-Konfigurationstool zum Registrieren des Servers automatisch geöffnet.
	2. Geben Sie unter **Host-Agent-Konfiguration** die interne IP-Adresse des Konfigurationsservers und Port 443 an. Sie können die interne Adresse und den privaten Port 443 auch dann verwenden, wenn Sie keine Verbindung per VPN herstellen, da der virtuelle Computer demselben Azure-Netzwerk zugeordnet ist wie der Konfigurationsserver. Lassen Sie **HTTPS verwenden** aktiviert. Geben Sie die Passphrase für den Konfigurationsserver ein, die Sie zuvor notiert haben. Klicken Sie auf **OK**, um den Server zu registrieren. Beachten Sie, dass Sie die NAT-Optionen auf der Seite ignorieren können. Sie werden nicht verwendet.
	3. Wenn das Aufbewahrungslaufwerk schätzungsweise mehr als 1 TB erfordert, können Sie das Aufbewahrungsvolume \(R:\) mit einem virtuellen Datenträger und [Speicherplätzen](http://blogs.technet.com/b/askpfeplat/archive/2013/10/21/storage-spaces-how-to-configure-storage-tiers-with-windows-server-2012-r2.aspx) konfigurieren.
	
	![Windows-Masterzielserver](./media/site-recovery-vmware-to-azure/ASRVMWare_TSRegister.png)

8. Wenn Sie Linux verwenden:
	1. Stellen Sie sicher, dass Sie die aktuellen Linux Integration Services \(LIS\) installiert haben, bevor Sie die Masterzielserver-Software installieren. Die neueste Version von LIS sowie eine Anleitung zur Installation finden Sie [hier](https://www.microsoft.com/de-de/download/details.aspx?id=46842). Starten Sie den Computer nach der LIS-Installation neu.
	2. Klicken Sie unter ** Zielressourcen \(Azure-Ressourcen\) vorbereiten** auf **Weitere Software herunterladen und installieren \(nur für Linux-Masterzielserver\)**, um das Linux-Masterzielserverpaket herunterzuladen. Kopieren Sie die heruntergeladene TAR-Datei mit einem SFTP-Client auf den virtuellen Computer. Alternativ können Sie sich bei dem bereitgestellten Linux-Masterzielserver anmelden und die Datei mithilfe von *wget http://go.microsoft.com/fwlink/?LinkID=529757&clcid=0x409* herunterladen.
2. Melden Sie sich über einen Secure Shell-Client beim Server an. Wenn Sie über VPN mit dem Azure-Netzwerk verbunden sind, verwenden Sie die interne IP-Adresse. Verwenden Sie andernfalls die externe IP-Adresse und den öffentlichen SSH-Endpunkt.
	3. Extrahieren Sie die Dateien aus dem GZIP-Installationsprogramm, indem Sie folgenden Befehl ausführen: **tar –xvzf Microsoft-ASR\_UA\_8.4.0.0\_RHEL6-64*** ![Linux-Masterzielserver](./media/site-recovery-vmware-to-azure/ASRVMWare_TSLinuxTar.png)
	4. Stellen Sie sicher, dass Sie sich in dem Verzeichnis befinden, in das Sie den Inhalt der TAR-Datei extrahiert haben.
	5. Kopieren Sie die Passphrase für den Konfigurationsserver mit dem Befehl **echo *`<passphrase>`* \>passphrase.txt** in eine lokale Datei.
	6. Führen Sie den Befehl „**sudo ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i *`<Configuration server internal IP address>`* -p 443 -s y -c https -P passphrase.txt**“ aus.

	![Zielserver registrieren](./media/site-recovery-vmware-to-azure/Linux-MT-install.png)

9. Warten Sie einige Minuten \(10 bis 15\), und stellen Sie auf der Seite **Server** \> **Konfigurationsserver** sicher, dass der Masterzielserver auf der Registerkarte **Serverdetails** als registriert aufgeführt ist. Wenn Sie Linux verwenden und der Server nicht registriert wurde, führen Sie das Hostkonfigurationstool über "/usr/local/ASR/Vx/bin/hostconfigcli" erneut aus. Sie müssen die Zugriffsberechtigungen festlegen, indem Sie chmod als root ausführen.

	![Zielserver überprüfen](./media/site-recovery-vmware-to-azure/ASRVMWare_TSList.png)

>[AZURE.NOTE]Beachten Sie, dass es bis zu 15 Minuten nach Abschluss der Registrierung dauern kann, bis der Masterzielserver unter dem Konfigurationsserver aufgeführt wird. Wenn die Ansicht sofort aktualisiert werden soll, aktualisieren Sie den Konfigurationsserver, indem Sie unten auf der Seite "Konfigurationsserver" auf die Schaltfläche "Aktualisieren" klicken.

## Schritt 4: Bereitstellen eines lokalen Prozessservers

>[AZURE.NOTE]Es wird empfohlen, eine statische IP-Adresse für den Prozessserver zu konfigurieren, um sicherzustellen, dass diese nach einem Neustart beibehalten wird.

1. Klicken Sie auf „Schnellstart“ \> **Prozessserver lokal installieren** \> **Prozessserver herunterladen und installieren**.

	![Prozessserver installieren](./media/site-recovery-vmware-to-azure/ASRVMWare_PSDeploy.png)

2.  Kopieren Sie die heruntergeladene ZIP-Datei auf den Server, auf dem Sie den Prozessserver installieren möchten. Die ZIP-Datei enthält zwei Installationsdateien:

	- Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*
	- Microsoft-ASR\_CX\_8.4.0.0\_Windows*

3. Entpacken Sie das Archiv, und kopieren Sie die Installationsdateien in einen Speicherort auf dem Server.
4. Führen Sie die Installationsdatei **Microsoft-ASR\_CX\_TP\_8.4.0.0\_Windows*** aus, und folgen Sie den Anweisungen. Dadurch werden die Drittanbieterkomponenten installiert, die für die Bereitstellung erforderlich sind.
5. Führen Sie dann **Microsoft-ASR\_CX\_8.4.0.0\_Windows*** aus.
6. Wählen Sie auf der Seite **Servermodus** die Option **Prozessserver** aus.

	![Serverauswahlmodus](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerSelection.png)

7. Gehen Sie auf der Seite **Umgebungsdetails** folgendermaßen vor:


	- Wenn Sie virtuelle VMware-Computer schützen möchten, klicken Sie auf **Ja**.
	- Wenn Sie nur physische Server schützen möchten und folglich VMware vCLI nicht auf dem Prozessserver installiert werden muss, klicken Sie auf **Nein**, und fahren Sie fort.
		
	![Konfigurationsserver registrieren](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerVirtualPhysical.png)

8. Beachten Sie beim Installieren von VMware vCLI Folgendes:

	- **Es wird nur VMware vSphere CLI 5.5.0 unterstützt**. Der Prozessserver wird mit anderen Versionen oder Updates von vSphere CLI nicht ausgeführt.
	- Laden Sie vSphere CLI 5.5.0 [hier](https://my.vmware.com/web/vmware/details?downloadGroup=VCLI550&productId=352) herunter.
	- Wenn Sie vSphere CLI direkt vor dem Installieren des Prozessservers installiert haben und vSphere CLI von Setup nicht erkannt wird, warten Sie bis zu fünf Minuten lang, bevor Sie Setup erneut starten. Dadurch wird sichergestellt, dass alle für die Ermittlung von vSphere CLI erforderlichen Umgebungsvariablen richtig initialisiert wurden.

9.	Wählen Sie unter **NIC-Auswahl für den Prozessserver** den Netzwerkadapter aus, der vom Prozessserver verwendet werden soll.

	![Adapter auswählen](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerNICSelection.png)

10.	Unter **Konfigurationsserverdetails**:

	- Wenn Sie eine Verbindung über ein VPN herstellen, geben Sie als IP-Adresse die interne IP-Adresse des Konfigurationsservers und 443 für den Port an. Geben Sie andernfalls die öffentliche virtuelle IP-Adresse und den zugeordneten öffentlichen HTTP-Endpunkt.
	- Geben Sie die Passphrase des Konfigurationsservers ein.
	- Deaktivieren Sie **Softwaresignatur des Mobilitätsdiensts** prüfen, wenn Sie zum Installieren des Diensts die automatische Pushfunktion verwenden und die Überprüfung deaktivieren möchten. Die Signaturüberprüfung benötigt eine Internetverbindung vom Prozessserver aus.
	- Klicken Sie auf **Weiter**.

	![Konfigurationsserver registrieren](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerConfigServer.png)


11. Wählen Sie unter **Installationslaufwerk auswählen** ein Cachelaufwerk aus. Der Prozessserver benötigt eine Cachelaufwerk mit mindestens 600 GB freiem Speicherplatz. Klicken Sie dann auf **Weiter**.

	![Konfigurationsserver registrieren](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerCacheConfig.png)

12. Beachten Sie, dass Sie möglicherweise den Server neu starten müssen, um die Installation abzuschließen. Überprüfen Sie unter **Konfigurationsserver** \> **Serverdetails**, ob der Prozessserver angezeigt wird und erfolgreich im Tresor registriert wurde.

>[AZURE.NOTE]Es kann bis zu 15 Minuten nach Abschluss der Registrierung dauern, bis der Prozessserver unter dem Konfigurationsserver aufgeführt wird. Wenn die Ansicht sofort aktualisiert werden soll, aktualisieren Sie den Konfigurationsserver, indem Sie unten auf der Seite "Konfigurationsserver" auf die Schaltfläche "Aktualisieren" klicken.
 
![Prozessserver überprüfen](./media/site-recovery-vmware-to-azure/ASRVMWare_ProcessServerRegister.png)

Wenn Sie beim Registrieren des Prozessservers die Signaturüberprüfung für den Mobilitätsdienst nicht deaktiviert haben, können Sie dies später nachholen:

1. Melden Sie sich beim Prozessserver als Administrator an, und öffnen Sie die Datei "C:\\pushinstallsvc\\pushinstaller.conf", um sie zu bearbeiten. Fügen Sie im Abschnitt **\[PushInstaller.transport\]** die folgende Zeile hinzu: **SignatureVerificationChecks="0"**. Speichern und schließen Sie die Datei.
2. Starten Sie den InMage PushInstall-Dienst neu.


## Schritt 5: Installieren der neuesten Updates

Bevor Sie fortfahren, stellen Sie sicher, dass die neuesten Updates installiert sind. Beachten Sie, dass die Updates in der folgenden Reihenfolge installiert werden müssen:

1. Konfigurationsserver
2. Prozessserver
3. Masterzielserver

Sie erhalten die Updates im Site Recovery-**Dashboard**. Extrahieren Sie für eine Linux-Installation die Dateien aus dem GZIP-Installationsprogramm, und führen Sie zum Installieren des Updates den Befehl „sudo ./install“ aus.

Wenn Sie virtuelle Computer oder physische Server ausführen, auf denen der Mobilitätsdienst bereits installiert ist, können Sie die Updates für den Dienst wie folgt abrufen:

- Laden Sie die Updates für den Dienst wie folgt herunter:
	- [Windows Server \(nur 64 Bit\)](http://download.microsoft.com/download/8/4/8/8487F25A-E7D9-4810-99E4-6C18DF13A6D3/Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe)
	- [CentOS 6.4, 6.5, 6.6 \(nur 64 Bit\)](http://download.microsoft.com/download/7/E/D/7ED50614-1FE1-41F8-B4D2-25D73F623E9B/Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz)
	- [Oracle Enterprise Linux 6.4, 6.5 \(nur 64 Bit\)](http://download.microsoft.com/download/5/2/6/526AFE4B-7280-4DC6-B10B-BA3FD18B8091/Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz)
	- [SUSE Linux Enterprise Server SP3 \(nur 64 Bit\)](http://download.microsoft.com/download/B/4/2/B4229162-C25C-4DB2-AD40-D0AE90F92305/Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz)
- Alternativ können Sie nach dem Aktualisieren des Prozessservers die aktualisierte Version des Mobilitätsdiensts aus dem Ordner "C:\\pushinstallsvc\\repository" auf dem Prozessserver abrufen.
- Wenn Sie einen bereits geschützten Computer mit einer älteren Version des Mobilitätsdiensts installiert haben, können Sie den Mobilitätsdienst auf den geschützten Computern auch automatisch über das Verwaltungsportal aktualisieren. Hierzu wählen Sie die Schutzgruppe aus, zu der der Computer gehört, markieren den geschützten Computer und klicken unten auf die Schaltfläche „Mobilitätsdienst aktualisieren“. Die Schaltfläche „Mobilitätsdienst aktualisieren“ wird nur aktiviert, wenn eine neuere Version des Mobilitätsdiensts verfügbar ist. Stellen Sie sicher, dass auf dem Prozessserver die neueste Version der Prozessserversoftware ausgeführt wird, bevor Sie den Mobilitätsdienst aktualisieren. Der geschützte Server muss alle [Voraussetzungen für die automatische Pushinstallation](#install-the-mobility-service-automatically) erfüllen, damit die Aktualisierung des Mobilitätsdiensts erfolgreich ist.

![vCenter-Server auswählen](./media/site-recovery-vmware-to-azure/ASRVmware_UpdateMobility1.png)

Geben Sie unter „Konten auswählen“ das Administratorkonto an, das zum Aktualisieren des Mobilitätsdiensts auf dem geschützten Server verwendet werden soll. Klicken Sie auf „OK“, und warten Sie, bis der ausgelöste Auftrag abgeschlossen ist.


## Schritt 6: Hinzufügen von vCenter-Servern oder ESXi-Hosts

1. Wählen Sie auf der Registerkarte **Server** \> **Konfigurationsserver** den Konfigurationsserver aus, und klicken Sie auf **VCENTER-SERVER HINZUFÜGEN**, um einen vCenter-Server oder ESXi-Host hinzuzufügen.

	![vCenter-Server auswählen](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter.png)

2. Geben Sie Details für den vCenter-Server oder ESXi-Host an, und wählen Sie den Prozessserver aus, der zu dessen Ermittlung verwendet wird.

	- Wenn der vCenter-Server nicht über den Standardport 443 ausgeführt wird, geben Sie die Nummer des Ports an, über den der vCenter-Server ausgeführt wird.
	- Der Prozessserver muss in demselben Netzwerk wie der vCenter-Server/ESXi-Host enthalten sein, und auf ihm muss VMware vSphere CLI 5.5.0 installiert sein.

	![Einstellungen des vCenter-Servers](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter4.png)


3. Nach Abschluss der Ermittlung wird der vCenter-Server unter den Serverkonfigurationsdetails aufgeführt.

	![Einstellungen des vCenter-Servers](./media/site-recovery-vmware-to-azure/ASRVMWare_AddVCenter2.png)

4. Wenn Sie zum Hinzufügen des vCenter-Servers oder ESXi-Hosts ein anderes Konto als ein Administratorkonto verwenden, stellen Sie sicher, dass das Konto über die folgenden Berechtigungen verfügt:

	- Für vCenter-Konten müssen die Rechte "Datencenter", "Datenspeicher", "Ordner", "Host", "Netzwerk", "Ressourcen", "Speicheransichten", "Virtuelle Maschine" und "vSphere Distributed Switch" aktiviert sein.
	- Für ESXi-Hostkonten müssen die Rechte "Datencenter", "Datenspeicher", "Ordner", "Host", "Netzwerk", "Ressourcen", "Virtuelle Maschine" und "vSphere Distributed Switch" aktiviert sein.



## Schritt 7: Erstellen einer Schutzgruppe

1. Öffnen Sie **Geschützte Elemente** \> **Schutzgruppe**, und klicken Sie, um eine Schutzgruppe hinzuzufügen.

	![Schutzgruppe erstellen](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG1.png)

2. Geben Sie auf der Seite **Schutzgruppeneinstellungen festlegen** einen Namen für die Gruppe an, und wählen Sie den Konfigurationsserver aus, auf dem Sie die Gruppe erstellen möchten.

	![Schutzgruppeneinstellungen](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG2.png)

3. Konfigurieren Sie auf der Seite **Replikationseinstellungen festlegen** die Replikationseinstellungen, die für alle Computer in der Gruppe verwendet werden.

	![Schutzgruppenreplikation](./media/site-recovery-vmware-to-azure/ASRVMWare_CreatePG3.png)

4. Einstellungen:
	- **Multi-VM-Konsistenz:** Wenn Sie diese Option aktivieren, werden freigegebene anwendungskonsistente Wiederherstellungspunkte auf den Computern in der Schutzgruppe erstellt. Diese Einstellung ist besonders wichtig, wenn auf allen Computern in der Schutzgruppe dieselbe Arbeitsauslastung ausgeführt wird. Alle Computer werden an demselben Datenpunkt wiederhergestellt. Steht nur bei Windows-Servern zur Verfügung.
	- **RPO-Schwellenwert:** Warnungen werden generiert, wenn die Replikations-RPO für den kontinuierlichen Datenschutz den konfigurierten RPO-Schwellenwert überschreitet.
	- **Aufbewahrungszeitraum des Wiederherstellungspunkts:** Gibt die Aufbewahrungsdauer an. Geschützte Computer können innerhalb dieses Zeitfensters an einem beliebigen Punkt wiederhergestellt werden.
	- **Anwendungskonsistente Momentaufnahmehäufigkeit:** Gibt an, wie oft Wiederherstellungspunkte erstellt werden, die anwendungskonsistente Momentaufnahmen enthalten.

Auf der Seite **Geschützte Elemente** können Sie die Erstellung der Schutzgruppen überwachen.

## Schritt 8: Einrichten von Computern, die Sie schützen möchten

Sie müssen den Mobilitätsdienst auf virtuellen Computern und physischen Servern installieren, die Sie schützen möchten. Hierzu stehen zwei Möglichkeiten zur Verfügung:

- Automatische Pushinstallation des Diensts auf jedem Computer über den Prozessserver
- Manuelle Installation des Diensts 

### Automatisches Installieren des Mobilitätsdiensts

Beim Hinzufügen von Computern zu einer Schutzgruppe wird der Mobilitätsdienst vom Prozessserver automatisch per Pushfunktion auf jedem Computer installiert.

**Automatische Pushinstallation des Mobilitätsdiensts auf Windows-Servern:**

1. Installieren Sie die neuesten Updates für den Prozessserver wie in [Schritt 5: Installieren der neuesten Updates](#step-5-install-latest-updates) beschrieben, und stellen Sie sicher, dass der Prozessserver verfügbar ist. 
2. Stellen Sie sicher, dass eine Netzwerkverbindung zwischen dem Quellcomputer und dem Prozessserver besteht und dass der Prozessserver Zugriff auf den Quellcomputer hat.  
3. Konfigurieren Sie die Windows-Firewall so, dass die **Datei- und Druckerfreigabe** und die **Windows-Verwaltungsinstrumentation** zulässig sind. Wählen Sie in den Einstellungen der Windows-Firewall die Option "Eine App oder ein Feature durch die Windows-Firewall zulassen" aus, und wählen Sie die in der folgenden Abbildung dargestellten Anwendungen aus. Für Computer, die einer Domäne angehören, können Sie die Firewallrichtlinie mit einem Gruppenrichtlinienobjekt konfigurieren.

	![Firewalleinstellungen](./media/site-recovery-vmware-to-azure/ASRVMWare_PushInstallFirewall.png)

4. Das zur Durchführung der Pushinstallation verwendete Konto muss in der Gruppe "Administratoren" auf dem Computer enthalten sein, den Sie schützen möchten. Diese Anmeldeinformationen werden nur für die Pushinstallation des Mobilitätsdiensts verwendet, und Sie geben sie beim Hinzufügen eines Computers zu einer Schutzgruppe an.
5. Wenn es sich bei dem bereitgestellten Konto nicht um ein Domänenkonto handelt, müssen Sie die Remote-Benutzerzugriffssteuerung auf dem lokalen Computer deaktivieren. Fügen Sie hierzu unter "HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System" den Registrierungseintrag "LocalAccountTokenFilterPolicy DWORD" mit dem Wert 1 hinzu. Um den Registrierungseintrag über eine Befehlszeilenschnittstelle hinzuzufügen, öffnen Sie eine CMD- oder PowerShell-Befehlszeilenschnittstelle, und geben Sie **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`** ein. 

**Automatische Pushinstallation des Mobilitätsdiensts auf Linux-Servern:**

1. Installieren Sie die neuesten Updates für den Prozessserver wie in [Schritt 5: Installieren der neuesten Updates](#step-5-install-latest-updates) beschrieben, und stellen Sie sicher, dass der Prozessserver verfügbar ist.
2. Stellen Sie sicher, dass eine Netzwerkverbindung zwischen dem Quellcomputer und dem Prozessserver besteht und dass der Prozessserver Zugriff auf den Quellcomputer hat.  
3. Stellen Sie sicher, dass das Konto einem Root-Benutzer auf dem Linux-Quellserver entspricht.
4. Stellen Sie sicher, dass die Datei "/etc/hosts" auf dem Linux-Quellserver Einträge enthält, die den Namen des lokalen Hosts IP-Adressen zuordnen, die allen NICs zugeordnet sind.
5. Installieren Sie die neuesten openssh-, openssh-server- und openssl-Pakete auf dem Computer, den Sie schützen möchten.
6. Stellen Sie sicher, dass SSH auf Port 22 aktiviert ist und ausgeführt wird. 
7. Aktivieren Sie das SFTP-Subsystem und die Kennwortauthentifizierung in der Datei "sshd\_config" wie folgt: 

	- a\) Melden Sie sich als root an.
	- b\) Suchen Sie in der Datei „/etc/ssh/sshd\_config“ die Zeile, die mit **PasswordAuthentication** beginnt.
	- c\) Heben Sie die Auskommentierung der Zeile auf, und ändern Sie den Wert von "no" in "yes".

		![Linux-Mobilität](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility1.png)

	- d\) Suchen Sie die Zeile, die mit "Subsystem" beginnt, und heben Sie die Auskommentierung der Zeile auf.
	
		![Linux-Pushmobilität](./media/site-recovery-vmware-to-azure/ASRVMWare_LinuxPushMobility2.png)

8. Stellen Sie sicher, dass die Linux-Variante des Quellcomputers unterstützt wird.
 
### Manuelles Installieren des Mobilitätsdiensts

Die Softwarepakete zum Installieren des Mobilitätsdiensts befinden sich auf dem Prozessserver in "C:\\pushinstallsvc\\repository". Melden Sie sich beim Prozessserver an, und kopieren Sie das entsprechende Installationspaket anhand der folgenden Tabelle auf den Quellcomputer:

| Quellbetriebssystem | Mobilitätsdienstpaket auf dem Prozessserver |
|---------------------------------------------------	|------------------------------------------------------------------------------------------------------	|
| Windows Server \(nur 64 Bit\) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_Windows_GA_28Jul2015_release.exe` |
| CentOS 6.4, 6.5, 6.6 \(nur 64 Bit\) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_RHEL6-64_GA_28Jul2015_release.tar.gz` |
| SUSE Linux Enterprise Server 11 SP3 \(nur 64 Bit\) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_SLES11-SP3-64_GA_28Jul2015_release.tar.gz`|
| Oracle Enterprise Linux 6.4, 6.5 \(nur 64 Bit\) | `C:\pushinstallsvc\repository\Microsoft-ASR_UA_8.4.0.0_OL6-64_GA_28Jul2015_release.tar.gz` |


**Um den Mobilitätsdienst manuell auf einem Windows Server zu installieren**, gehen Sie folgendermaßen vor:

1. Kopieren Sie das Paket **Microsoft-ASR\_UA\_8.4.0.0\_Windows\_GA\_28Jul2015\_release.exe** aus dem in der obigen Tabelle aufgeführten Verzeichnispfad des Prozessservers auf den Quellcomputer.
2. Installieren Sie den Mobilitätsdienst, indem Sie die ausführbare Datei auf dem Quellcomputer ausführen.
3. Folgen Sie den Anweisungen des Installationsprogramms.
4. Wählen Sie als Rolle **Mobilitätsdienst** aus, und klicken Sie auf **Weiter**.
	
	![Mobilitätsdienst installieren](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall1.png)

5. Belassen Sie das Installationsverzeichnis als Standardinstallationspfad, und klicken Sie auf **Installieren**.
6. Geben Sie unter **Host-Agent-Konfiguration** die IP-Adresse und den HTTPS-Port des Konfigurationsservers an.

	- Wenn Sie die Verbindung über das Internet herstellen, geben Sie die öffentliche virtuelle IP-Adresse und als Port den öffentlichen HTTPS-Endpunkt an.
	- Wenn Sie die Verbindung über ein VPN herstellen, geben Sie die interne IP-Adresse und den Port 443 an. Lassen Sie **HTTPS verwenden** aktiviert.

	![Mobilitätsdienst installieren](./media/site-recovery-vmware-to-azure/ASRVMWare_MobilityServiceInstall2.png)

7. Geben Sie die Passphrase für den Konfigurationsserver an, und klicken Sie auf **OK**, um den Mobilitätsdienst beim Konfigurationsserver zu registrieren.

**So führen Sie dies über die Befehlszeile aus:**

1. Kopieren Sie die Passphrase vom CX-Server in die Datei "C:\\connection.passphrase" auf dem Server, und führen Sie diesen Befehl aus. In unserem Beispiel lautet die IP-Adresse des CX-Servers 104.40.75.37, und der HTTPS-Port lautet 62519:

    `C:\Microsoft-ASR_UA_8.2.0.0_Windows_PREVIEW_20Mar2015_Release.exe" -ip 104.40.75.37 -port 62519 -mode UA /LOG="C:\stdout.txt" /DIR="C:\Program Files (x86)\Microsoft Azure Site Recovery" /VERYSILENT  /SUPPRESSMSGBOXES /norestart  -usesysvolumes  /CommunicationMode https /PassphrasePath "C:\connection.passphrase"`

**Manuelles Installieren des Mobilitätsdiensts auf einem Linux-Server:**

1. Kopieren Sie anhand der obigen Tabelle das entsprechende TAR-Archiv vom Prozessserver auf den Quellcomputer.
2. Öffnen Sie ein Shellprogramm, und extrahieren Sie das gezippte TAR-Archiv in einen lokalen Pfad, indem Sie `tar -xvzf Microsoft-ASR_UA_8.2.0.0*` ausführen.
3. Erstellen Sie in dem lokalen Verzeichnis, in das Sie den Inhalt des TAR-Archivs extrahiert haben, die Datei „passphrase.txt“, indem Sie über die Shell *`echo <passphrase> >passphrase.txt`* eingeben.
4. Installieren Sie den Mobilitätsdienst, indem Sie *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`* eingeben.
5. Geben Sie die IP-Adresse und den Port an:

	- Wenn Sie die Verbindung mit dem Konfigurationsserver über das Internet herstellen, geben Sie in `<IP address>` und `<port>` die virtuelle öffentliche IP-Adresse und den öffentlichen HTTPS-Endpunkt des Konfigurationsservers an.
	- Wenn Sie die Verbindung über ein VPN herstellen, geben Sie die interne IP-Adresse und den Port 443 an.

**So führen Sie dies über die Befehlszeile aus:**

1. Kopieren Sie die Passphrase vom CX-Server in die Datei "passphrase.txt" auf dem Server, und führen Sie diesen Befehl aus. In unserem Beispiel lautet die IP-Adresse des CX-Servers 104.40.75.37, und der HTTPS-Port lautet 62519:

So installieren Sie den Dienst auf einem Produktionsserver:

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt
 
So installieren Sie den Dienst auf dem Zielserver:


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 62519 -s y -c https -P passphrase.txt

>[AZURE.NOTE]Wenn Sie einer Schutzgruppe Computer hinzufügen, auf denen bereits eine geeignete Version des Mobilitätsdiensts ausgeführt wird, wird die Pushinstallation übersprungen.


## Schritt 9: Aktivieren des Schutzes

Zum Aktivieren des Schutzes fügen Sie einer Schutzgruppe virtuelle Computer und physische Server hinzu. Bevor Sie beginnen, beachten Sie Folgendes:

- Virtuelle Computer werden alle 15 Minuten ermittelt, und es kann bis zu 15 Minuten dauern, bis sie nach der Ermittlung in Azure Site Recovery angezeigt werden.
- Es kann auch bis zu 15 Minuten dauern, bis Umgebungsänderungen auf dem virtuellen Computer \(z. B. Installation von VMware-Tools\) in Site Recovery aktualisiert werden.
- Sie können auf der Seite **Konfigurationsserver** im Feld **LETZTER KONTAKT UM** für den vCenter-Server/ESXi-Host den Zeitpunkt der letzten Ermittlung überprüfen.
- Wenn Sie bereits eine Schutzgruppe erstellt haben und anschließend einen vCenter-Server oder ESXi-Host hinzufügen, dauert es 15 Minuten, bis das Azure Site Recovery-Portal aktualisiert wird und virtuelle Computer im Dialogfeld **Hinzufügen von Computern zu einer Schutzgruppe** angezeigt werden.
- Wenn Sie einer Schutzgruppe sofort Computer hinzufügen möchten, ohne auf die planmäßige Ermittlung zu warten, markieren Sie den Konfigurationsserver \(klicken Sie nicht darauf\), und klicken Sie auf die Schaltfläche **Aktualisieren**.
- Wenn Sie einer Schutzgruppe virtuelle oder physische Computer hinzufügen, wird der Mobilitätsdienst automatisch vom Prozessserver auf den Quellserver gepusht und auf diesem installiert, sofern der Dienst nicht bereits installiert ist.
- Damit der automatische Pushmechanismus funktioniert, stellen Sie sicher, dass Sie die geschützten Computer so eingerichtet haben, wie im vorherigen Schritt beschrieben wurde.

Fügen Sie Computer wie folgt hinzu:

1. **Geschützte Elemente** \> **Schutzgruppe** \> Registerkarte **Computer**. Klicken Sie auf **COMPUTER HINZUFÜGEN**. Als bewährte Methode wird empfohlen, dass Schutzgruppen ihre Workloads spiegeln, damit Sie Computer, auf denen eine bestimmte Anwendung ausgeführt wird, derselben Gruppe hinzufügen.
2. Wenn Sie physische Server schützen, geben Sie im Assistenten **Physische Computer hinzufügen** unter **Virtuelle Computer auswählen** die IP-Adresse und den Anzeigenamen an. Wählen Sie dann die Betriebssystemfamilie aus.

	![vCenter-Server hinzufügen](./media/site-recovery-vmware-to-azure/ASRVMWare_PhysicalProtect.png)

3. Wenn Sie virtuelle VMware-Computer schützen, wählen Sie unter **Virtuelle Computer auswählen** einen vCenter-Server aus, der die virtuellen Computer verwaltet \(oder den EXSi-Host, auf dem sie ausgeführt werden\), und wählen Sie dann die Computer aus.

	![vCenter-Server hinzufügen](./media/site-recovery-vmware-to-azure/ASRVMWare_SelectVMs.png)

4. Wählen Sie unter **Zielressourcen angeben** die Masterzielserver und den Speicher aus, die für die Replikation verwendet werden sollen, und wählen Sie aus, ob die Einstellungen für alle Workloads verwendet werden sollen. Wählen Sie bei der Konfiguration des Schutzes für Workloads, die konsistent hohe E/A-Leistung und niedrige Latenz erfordern, [Premium-Speicherkonto](../storage/storage-premium-storage-preview-portal.md) aus, um E/A-intensive Workloads zu hosten. Wenn Sie ein Premium-Speicherkonto für Ihre Workloaddatenträger verwenden möchten, müssen Sie das Masterziel der DS-Serie verwenden. Sie können keine Datenträger des Premium-Speichers mit einem Masterziel verwenden, das nicht zur DS-Serie gehört.

	![vCenter-Server](./media/site-recovery-vmware-to-azure/ASRVMWare_MachinesResources.png)

5. Wählen Sie unter **Konten angeben** das Konto aus, das Sie zum Installieren des Mobilitätsdiensts auf geschützten Computern verwenden möchten. Die Kontoanmeldeinformationen werden für die automatische Installation des Mobilitätsdiensts benötigt. Wenn Sie kein Konto auswählen können, müssen Sie ein Konto einrichten, wie in Schritt 2 beschrieben. Beachten Sie, dass Azure keinen Zugriff auf dieses Konto hat. Für Windows-Server sollte das Konto über Administratorrechte auf dem Quellserver verfügen. Für Linux muss das Konto "root" lauten.

	![Linux-Anmeldeinformationen](./media/site-recovery-vmware-to-azure/ASRVMWare_VMMobilityInstall.png)

6. Klicken Sie auf das Häkchen, um das Hinzufügen von Computern zur Schutzgruppe zu beenden und die erste Replikation für jeden Computer zu starten. Sie können den Status auf der Seite **Aufträge** überwachen.

	![vCenter-Server hinzufügen](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs2.png)

7. Darüber hinaus können Sie den Schutzstatus überwachen, indem Sie auf **Geschützte Elemente** \> Name der Schutzgruppe \> **Virtuelle Computer** klicken. Nachdem die erste Replikation abgeschlossen ist und die Computer Daten synchronisieren, wird der Status **Geschützt** angezeigt.

	![Aufträge virtueller Computer](./media/site-recovery-vmware-to-azure/ASRVMWare_PGJobs.png)


### Festlegen der Eigenschaften geschützter Computer

1. Sobald ein Computer den Status **Geschützt** aufweist, können Sie die Failovereigenschaften konfigurieren. Wählen Sie in den Schutzgruppendetails den Computer aus, und öffnen Sie die Registerkarte **Konfigurieren**.
2. Sie können den Namen, der dem Computer in Azure nach dem Failover zugewiesen wird, und die Größe des virtuellen Azure-Computers ändern. Außerdem können Sie das Azure-Netzwerk auswählen, mit dem der Computer nach einem Failover verbunden werden soll.

	![Eigenschaften für virtuelle Computer festlegen](./media/site-recovery-vmware-to-azure/ASRVMWare_VMProperties.png)

Beachten Sie Folgendes:

- Der Name des Azure-Computers muss den Azure-Anforderungen entsprechen.
- Standardmäßig werden replizierte virtuelle Computer in Azure nicht mit einem Azure-Netzwerk verbunden. Wenn repliziere virtuelle Computer kommunizieren sollen, stellen Sie sicher, dass für beide dasselbe Azure-Netzwerk festgelegt wird.
- Wenn Sie die Größe ein Volumes auf einem virtuellen VMware-Computer oder einem physischen Server ändern, wechselt der virtuelle Computer bzw. der Server in einen kritischen Zustand. Wenn Sie die Größe ändern müssen, führen Sie folgende Schritte aus:

	- a\) Ändern Sie die Größeneinstellung.
	- b\) Wählen Sie auf der Registerkarte **Virtuelle Computer** den virtuellen Computer aus, und klicken Sie auf **Entfernen**.
	- c\) Wählen Sie unter **Virtuellen Computer entfernen** die Option **Schutz deaktivieren \(Für Wiederherstellungsdrilloption und Volumegrößenänderung verwenden\)** aus. Mit dieser Option wird der Schutz deaktiviert, die Wiederherstellungspunkte in Azure bleiben jedoch erhalten.

		![Eigenschaften für virtuelle Computer festlegen](./media/site-recovery-vmware-to-azure/ASRVMWare_RemoveVM.png)

	- d\) Aktivieren Sie erneut den Schutz für den virtuellen Computer. Wenn Sie den Schutz erneut aktivieren, werden die Daten für das angepasste Volume an Azure übertragen.

	

## Schritt 10: Ausführen eines Failovers

Derzeit können nur nicht geplante Failover für geschützte virtuelle VMware-Computer und physische Server ausgeführt werden. Beachten Sie Folgendes:



- Stellen Sie vor dem Initiieren eines Failovers sicher, dass der Konfigurationsserver und Masterzielserver ausgeführt werden und fehlerfrei sind. Andernfalls schlägt das Failover fehl.
- Quellcomputer werden bei einem nicht geplanten Failover nicht heruntergefahren. Bei einem nicht geplanten Failover wird die Datenreplikation für die geschützten Server beendet. Sie müssen die Computer aus der Schutzgruppe löschen und erneut hinzufügen, um nach Abschluss des nicht geplanten Failovers die Computer erneut zu schützen.
- Wenn das Failover ohne Datenverlust erfolgen soll, stellen Sie sicher, dass die virtuellen Computer des primären Standorts deaktiviert sind, bevor Sie das Failover initiieren.

1. Fügen Sie auf der Seite **Wiederherstellungspläne** einen Wiederherstellungsplan hinzu. Geben Sie Details für den Plan an, und wählen Sie **Azure** als Ziel aus.

	![Wiederherstellungsplan konfigurieren](./media/site-recovery-vmware-to-azure/ASRVMWare_RP1.png)

2. Wählen Sie unter **Virtuellen Computer auswählen** eine Schutzgruppe aus, und wählen Sie dann Computer in der Gruppe aus, die dem Wiederherstellungsplan hinzugefügt werden sollen. Weitere Informationen zu Wiederherstellungsplänen finden Sie [hier](site-recovery-create-recovery-plans.md).

	![Virtuelle Computer hinzufügen](./media/site-recovery-vmware-to-azure/ASRVMWare_RP2.png)

3. Bei Bedarf können Sie den Plan anpassen und Gruppen erstellen sowie die Reihenfolge anpassen, in der für die Computer im Wiederherstellungsplan ein Failover durchgeführt wird. Sie können auch Aufforderungen für manuelle Aktionen und Skripts hinzufügen. Die Skripts beim Wiederherstellen in Azure können mithilfe von [Azure Automation-Runbooks](site-recovery-runbook-automation.md) hinzugefügt werden.

4. Wählen Sie den Plan auf der Seite **Wiederherstellungspläne** aus, und klicken Sie auf **Nicht geplantes Failover**.
5. Überprüfen Sie unter **Failover bestätigen** die Failoverrichtung \(auf Azure\), und wählen Sie den Wiederherstellungspunkt für das Failover aus.
6. Warten Sie, bis der Failoverauftrag abgeschlossen ist, und prüfen Sie dann, ob das Failover wie erwartet durchgeführt wurde und die replizierten virtuellen Computer in Azure erfolgreich gestartet wurden.




## Schritt 11: Ausführen eines Failbacks von Computern aus Azure

[Weitere Informationen](site-recovery-failback-azure-to-vmware.md) über das Failback von unter Azure ausgeführten Computern zur lokalen Umgebung.


## Verwalten der Prozessserver

Der Prozessserver sendet Replikationsdaten an den Masterzielserver in Azure und ermittelt neue virtuelle VMware-Computer, die einem vCenter-Server hinzugefügt wurden. In den folgenden Situationen empfiehlt es sich, den Prozessserver in der Bereitstellung zu ändern:

- Wenn der aktuelle Prozessserver ausfällt
- Wenn die RPO \(Recovery Point Objective\) einen für Ihre Organisation nicht akzeptablen Wert erreicht.

Bei Bedarf können Sie die Replikation einiger oder aller lokalen virtuellen VMware-Computer und physischen Server auf einen anderen Prozess-Server verschieben. Beispiel:

- **Fehler** – Wenn ein Prozessserver ausfällt oder nicht verfügbar ist, können Sie die Replikation geschützter Computer auf einen anderen Prozessserver verschieben. Die Metadaten des Quellcomputers und Replikatcomputers werden auf den neuen Prozessserver verschoben, und die Daten werden synchronisiert. Der neue Prozessserver stellt automatisch eine Verbindung mit dem vCenter-Server her, um die automatische Ermittlung auszuführen. Im Site Recovery-Dashboard können Sie den Zustand der Prozessserver überwachen.
- **Lastenausgleich zum Anpassen der RPO** – Zur Erzielung eines verbesserten Lastenausgleichs können Sie im Site Recovery-Portal einen anderen Prozessserver auswählen und die Replikation eines oder mehrerer Computer dorthin verschieben, um einen manuellen Lastenausgleich durchzuführen. In diesem Fall werden die Metadaten des ausgewählten Quell- und Replikatcomputers auf den neuen Prozessserver verschoben. Der ursprüngliche Prozessserver bleibt mit dem vCenter-Server verbunden. 

### Überwachen des Prozessservers

Wenn ein Prozessserver in einem kritischen Zustand ist, wird im Site Recovery-Dashboard eine Statuswarnung angezeigt. Sie können auf den Status klicken, um die Registerkarte "Ereignisse" zu öffnen, und dann auf der Registerkarte "Aufträge" Detailinformationen zu bestimmten Aufträgen anzeigen.

### Ändern des für die Replikation verwendeten Prozessservers

1. Wechseln Sie zur Seite **KONFIGURATIONSSERVER** unter **SERVER**.
2. Klicken Sie auf den Namen des Konfigurationsservers, und wechseln Sie zu **Serverdetails**.
3. Klicken Sie in der Liste **Prozessserver** neben dem Server, den Sie ändern möchten, auf **Prozessserver ändern**. ![Prozessserver ändern 1](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS1.png)
4. Wählen Sie im Dialogfeld **Prozessserver ändern** unter **Zielprozessserver** den neuen Server aus, und wählen Sie dann die virtuellen Computer aus, die Sie auf den neuen Server replizieren möchten. Klicken Sie auf das Informationssymbol neben dem Servernamen, um Informationen zum Server, z. B. freier Speicherplatz und verwendeter Arbeitsspeicher, zu erhalten. Um Sie bei Entscheidungen zur Last zu unterstützen, wird der durchschnittliche Speicherplatz angezeigt, der zum Replizieren jedes ausgewählten virtuellen Computers auf den neuen Prozessserver benötigt wird. ![Prozessserver ändern 2](./media/site-recovery-vmware-to-azure/ASRVMware_ChangePS2.png)
5. Klicken Sie auf das Häkchen, um mit dem Replizieren auf den neuen Prozessserver zu beginnen. Wenn Sie alle virtuellen Computer von einem Prozessserver entfernen, der einen kritischen Zustand aufgewiesen hat, wird im Dashboard keine Warnung mehr für einen kritischen Zustand angezeigt.


## Hinweise und Informationen zu Drittanbietersoftware

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below \(collectively, “Third Party Code”\). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

Die vollständige Datei steht im [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428) zur Verfügung. Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

<!---HONumber=August15_HO7-->