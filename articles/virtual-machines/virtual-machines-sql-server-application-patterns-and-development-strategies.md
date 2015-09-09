<properties 
	pageTitle="Anwendungsmuster und Entwicklungsstrategien für SQL Server in Azure Virtual Machines"
	description="Dieser Artikel behandelt eine Reihe von Anwendungsmustern im Zusammenhang mit SQL Server auf virtuellen Azure-Computern. Er bietet Lösungsarchitekten und Entwicklern eine Grundlage für gute Anwendungsarchitektur und deren Entwurf."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="jroth" />

# Anwendungsmuster und Entwicklungsstrategien für SQL Server in Azure Virtual Machines

## Zusammenfassung: 
Die Ermittlung des oder der richtigen Muster für die SQL Server-basierten Anwendungen in einer Azure-Umgebung ist eine wichtige Entwurfsentscheidung und erfordert ein solides Grundwissen über die Zusammenarbeit von SQL Server mit den einzelnen Infrastrukturkomponenten von Azure. Mit SQL Server in Azure-Infrastrukturdiensten können Sie Ihre unter Windows Server entwickelten vorhandenen SQL Server-Anwendungen problemlos auf virtuelle Computer in Azure migrieren und dort verwalten und überwachen.

Das Ziel dieses Artikels ist, Lösungsarchitekten und Entwicklern eine Grundlage für gute Anwendungsarchitektur und deren Entwurf zu bieten, die sie bei der Migration der vorhandenen Anwendungen nach Azure sowie bei der Entwicklung neuer Anwendungen in Azure verfolgen können.

Für jedes Anwendungsmuster sind lokale Szenarios, die jeweilige Cloudlösung sowie entsprechende technische Empfehlungen verfügbar. Darüber hinaus behandelt der Artikel Azure-spezifische Entwicklungsstrategien für einen ordnungsgemäßen Entwurf Ihrer Anwendungen. Aufgrund der vielen möglichen Anwendungsmuster empfiehlt sich die sorgfältige Auswahl des geeignetsten Musters für die Anwendung und die Benutzer durch die Architekten und Entwickler.

**Technische Mitarbeiter:** Luis Carlos Vargas Hering, Madhan Arumugam Ramakrishnan

**Technische Bearbeiter:** Corey Sanders, Drew McDaniel, Narayan Annamalai, Nir Mashkowski, Sanjay Mishra, Silvano Coriani, Stefan Schackow, Tim Hickey, Tim Wieman, Xin Jin

## Einführung

Sie können viele Arten von Anwendungen mit n Ebenen entwickeln, indem Sie die Komponenten der unterschiedlichen Anwendungsebenen auf verschiedenen Computern und in verschiedene Komponenten unterteilen. Beispielsweise können Sie die Clientanwendung und Geschäftsregelkomponenten auf einem Computer, die Front-End-Webebene und die Komponenten der Datenzugriffsebene auf einem anderen Computer und eine Back-End-Datenbankebene auf einem weiteren Computer speichern. Diese Variante der Strukturierung hilft, die einzelnen Ebenen voneinander zu isolieren. Wenn Sie die Datenquelle ändern, müssen Sie die Client- oder Webanwendung nicht ändern, sondern nur die Komponenten der Datenzugriffsebene.

Eine typische *n-Ebenen*-Anwendung umfasst die Präsentationsebene, die Geschäftsebene und die Datenebene:


| Preisstufe | Beschreibung |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Präsentation** | Die *Präsentationsebene* (Webebene, Front-End-Ebene) ist die Ebene, auf der Benutzer mit einer Anwendung interagieren. |
| **Geschäftlich** | Die *Geschäftsebene* (mittlere Ebene) ist die Ebene, die von der Präsentations- und der Datenebene zur Kommunikation verwendet wird. Sie enthält die Hauptfunktionalität des Systems. |
| **Daten** | Die *Datenebene* ist im Grunde der Server, auf dem Daten einer Anwendung (z. B. ein Server mit SQL Server) gespeichert werden. |

Anwendungsebenen beschreiben die logische Gruppierung der Funktionen und Komponenten einer Anwendung. Außerdem beschreiben Ebenen die physische Verteilung der Funktionen und Komponenten über separate physische Server, Computer, Netzwerke oder Remotestandorte. Die Ebenen einer Anwendung können sich auf demselben physischen Computer (auf derselben Ebene) oder auf separaten Computern (n-Ebene) befinden, und die Komponenten in jeder Ebene kommunizieren mit Komponenten in anderen Ebenen über klar definierte Schnittstellen. Sie können sich den Begriff Ebene als Verweis auf physische Verteilungsmuster vorstellen, wie z. B. zwei Ebenen, drei Ebenen und n Ebenen. Ein **2-Ebenen-Anwendungsmuster** enthält zwei Anwendungsebenen: Anwendungsserver und Datenbankserver. Die direkte Kommunikation erfolgt zwischen dem Anwendungsserver und dem Datenbankserver. Der Anwendungsserver enthält sowohl Webebenen- als auch Geschäftsebenenkomponenten. Beim **3-Ebenen-Anwendungsmuster** gibt es drei Anwendungsebenen: Webserver, Anwendungsserver, der die Geschäftslogikebene und/oder Geschäfts-Datenzugriffskomponenten enthält, und der Datenbankserver. Die Kommunikation zwischen Webserver und Datenbankserver erfolgt über den Anwendungsserver. Ausführliche Informationen zu Anwendungsebenen und anderen Ebenen finden Sie unter [Microsoft Application Architecture Guide](https://msdn.microsoft.com/library/ff650706.aspx) (in englischer Sprache).

Bevor Sie beginnen, diesen Artikel zu lesen, sollten Sie mit den grundlegenden Konzepten von SQL Server und Azure vertraut sein. Weitere Informationen finden Sie unter [SQL Server Books Online](https://msdn.microsoft.com/library/bb545450.aspx), [SQL Server in Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md) und auf [Azure.com](http://azure.microsoft.com).

Dieser Artikel beschreibt mehrere Anwendungsmuster, die für einfache Anwendungen ebenso wie für komplexe Unternehmensanwendungen geeignet sind. Bevor wir ausführlich auf die einzelnen Muster eingehen, wird empfohlen, dass Sie sich mit den verfügbaren Datenspeicherdiensten in Azure vertraut machen, wie z. B. [Azure Storage](../storage/storage-introduction.md), [Azure SQL-Datenbank](../sql-database/sql-database-technical-overview.md), und [SQL Server auf einem virtuellen Azure-Computer](virtual-machines-sql-server-infrastructure-services.md). Um die optimalen Entwurfsentscheidungen für Ihre Anwendung zu treffen, sollten Sie genau wissen, wann Sie welchen Speicherdienst verwenden sollten.

### Wählen Sie SQL Server in Azure Virtual Machines in folgenden Fällen:

- Sie benötigen Kontrolle über SQL Server und Windows. Dies kann z. B. die SQL Server-Version, spezielle Hotfixes, Leistungskonfiguration usw. umfassen.

- Sie benötigen vollständige lokale Kompatibilität mit SQL Server, und Sie möchten vorhandene Anwendungen direkt nach Azure migrieren.

- Sie möchten die Funktionen der Azure-Umgebung nutzen, die Azure SQL-Datenbank unterstützt jedoch nicht alle Features, die die Anwendung benötigt. Dies könnte die folgenden Bereiche umfassen:

	- **Datenbankgröße**: Zum Zeitpunkt der Aktualisierung dieses Artikels unterstützt die SQL-Datenbank eine Datenbank mit bis zu 500 GB an Daten. Wenn die Anwendung mehr als 500 GB an Daten erfordert und Sie keine benutzerdefinierten Shardinglösungen implementieren möchten, empfiehlt es sich, SQL Server auf einem virtuellen Azure-Computer zu verwenden. Die neuesten Informationen finden Sie unter [Horizontales Skalieren von Azure SQL-Datenbanken](https://msdn.microsoft.com/library/azure/dn495641.aspx) und [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn741336.aspx).
	- **HIPAA-Konformität**: Kunden und unabhängige Softwarehersteller (ISVs) können [SQL Server in Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md) anstelle von [Azure SQL-Datenbank](../sql-database/sql-database-technical-overview.md) wählen, da SQL Server auf einem virtuellen Azure-Computer vom HIPAA Business Associate Agreement (BAA) abgedeckt wird. Weitere Informationen zur Kompatibilität finden Sie unter [Microsoft Azure Trust Center: Compliance](http://azure.microsoft.com/support/trust-center/compliance/) (in englischer Sprache).
	- **Features auf Instanzebene**: Zum aktuellen Zeitpunkt unterstützt die SQL-Datenbank keine Funktionen, die sich außerhalb der Datenbank befinden (z. B. verknüpfte Server, Agent-Aufträge, FileStream, Service Broker usw.). Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](https://msdn.microsoft.com/library/azure/ff394102.aspx).

## 1 Ebene (einfach): einzelner virtueller Computer

Bei diesem Anwendungsmuster stellen Sie die SQL Server-Anwendung und -Datenbank auf einem eigenständigen virtuellen Computer in Azure bereit. Derselbe virtuelle Computer enthält Ihre Client-/Webanwendung, die Geschäftskomponenten, die Datenzugriffsebene und den Datenbankserver. Präsentations-, Geschäfts- und Datenzugriffscode sind logisch getrennt, befinden sich jedoch physisch auf einem einzigen Servercomputer. Die meisten Kunden starten mit diesem Anwendungsmuster und skalieren anschließend horizontal hoch, indem Sie ihrem System weitere Webrollen oder virtuelle Computer hinzufügen.

Dieses Anwendungsmuster eignet sich für folgende Situationen:

- Sie möchten eine einfache Migration zur Azure-Plattform ausführen, um zu prüfen, ob die Plattform die Anforderungen Ihrer Anwendung erfüllt.

- Sie möchten alle Anwendungsebenen auf demselben virtuellen Computer im gleichen Azure-Rechenzentrum behalten, um die Wartezeit zwischen den Ebenen zu reduzieren.

- Sie möchten schnell und für kurze Zeit Entwicklungs- und Testumgebungen bereitstellen.

- Sie möchten Belastungstests für unterschiedliche Workloadniveaus durchführen, gleichzeitig jedoch nicht ständig viele physische Computer warten.

Das folgende Diagramm zeigt ein einfaches lokales Szenario und die Bereitstellung einer Cloudlösung auf einem einzelnen virtuellen Computer in Azure.

![1-Ebenen-Anwendungsmuster](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728008.png)

Durch das Bereitstellen der Geschäftsebene (Geschäftslogik- und Datenzugriffskomponenten) auf derselben physischen Ebene wie die Präsentationsebene können Sie die Anwendungsleistung steigern, es sei denn, Sie müssen eine separate Ebene aus Gründen der Skalierbarkeit oder Sicherheit verwenden.

Da dies gerade für den Anfang ein häufig verwendetes Muster ist, sollten Sie möglicherweise folgenden Artikel zur Migration lesen, um Daten auf ihren virtuellen SQL Server-Computer zu verschieben: [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md).

## 3 Ebenen (einfach): mehrere virtuelle Computer

Bei diesem Anwendungsmuster stellen Sie eine 3-Ebenen-Anwendung in Azure bereit, indem Sie jede Anwendungsebene auf einem anderen virtuellen Computer platzieren. Dies bietet eine flexible Umgebung für einfache Szenarios zur zentralen und horizontalen Hochkalierung. Wenn ein virtueller Computer die Client-/Webanwendung enthält, hostet ein anderer die Geschäftskomponenten und ein weiterer hostet den Datenbankserver.

Dieses Anwendungsmuster eignet sich für folgende Situationen:

- Sie möchten eine Migration komplexer Datenbankanwendungen nach Azure Virtual Machines durchführen.

- Sie möchten verschiedene Anwendungsebenen in unterschiedlichen Regionen hosten. Möglicherweise verfügen Sie beispielsweise über freigegebene Datenbanken, die für Berichtszwecke in mehreren Regionen bereitgestellt werden.

- Sie möchten Unternehmensanwendungen von lokalen virtualisierten Plattformen nach Azure Virtual Machines verschieben. Ausführliche Informationen zu Unternehmensanwendungen finden Sie unter [What is an Enterprise Application](https://msdn.microsoft.com/library/aa267045.aspx) (in englischer Sprache).

- Sie möchten schnell und für kurze Zeit Entwicklungs- und Testumgebungen bereitstellen.

- Sie möchten Belastungstests für unterschiedliche Workloadniveaus durchführen, gleichzeitig jedoch nicht ständig viele physische Computer warten.

Das folgende Diagramm veranschaulicht, wie Sie eine einfache 3-Ebenen-Anwendung in Azure platzieren können, indem Sie jede Anwendungsebene auf einem anderen virtuellen Computer speichern.

![3-Ebenen-Anwendungsmuster](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728009.png)

In diesem Anwendungsmuster gibt es nur einen virtuellen Computer auf jeder Ebene. Wenn Sie über mehrere virtuelle Computer in Azure verfügen, empfehlen wir, dass Sie ein virtuelles Netzwerk einrichten. [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) erstellt eine vertrauenswürdige Sicherheitsbegrenzung und erlaubt virtuellen Computern die Kommunikation untereinander über die private IP-Adresse. Darüber hinaus sollten Sie immer sicherstellen, dass alle Internetverbindungen nur auf der Präsentationsebene erfolgen. Dies bedeutet, dass Sie einen öffentlichen Endpunkt in der Präsentationsebene, jedoch nicht auf den anderen Ebenen öffnen sollten. Wenn Sie dieses Anwendungsmuster verfolgen, müssen Sie auch die Netzwerk-Zugriffssteuerungsliste (ACL) für diesen öffentlichen Port einrichten, um den Zugriff auf bestimmte IP-Adressen zu ermöglichen. Weitere Informationen finden Sie unter [Verwaltung der ACL für einen Endpunkt](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint).

Im Diagramm kann es sich bei den Internetprotokollen um TCP, UDP, HTTP oder HTTPS handeln.

>[AZURE.NOTE]Das Einrichten eines virtuellen Netzwerks in Azure ist kostenlos. Allerdings wird das VPN-Gateway für die lokale Verbindung in Rechnung gestellt. Diese Kosten basieren auf dem Zeitraum, in dem die Verbindung bereitgestellt wird und verfügbar ist.

## 2 und 3 Ebenen mit horizontal hochskalierter Präsentationsebene

Bei diesem Anwendungsmuster stellen Sie eine 2- oder 3-Ebenen-Datenbankanwendung in Azure Virtual Machines bereit, indem Sie jede Anwendungsebene auf einem anderen virtuellen Computer platzieren. Darüber hinaus skalieren Sie die Präsentationsebene aufgrund erhöhter Volumen eingehender Clientanforderungen horizontal hoch.

Dieses Anwendungsmuster eignet sich für folgende Situationen:

- Sie möchten Unternehmensanwendungen von lokalen virtualisierten Plattformen nach Azure Virtual Machines verschieben.

- Sie möchten die Präsentationsebene aufgrund erhöhter Volumen eingehender Clientanforderungen horizontal hochskalieren.

- Sie möchten schnell und für kurze Zeit Entwicklungs- und Testumgebungen bereitstellen.

- Sie möchten Belastungstests für unterschiedliche Workloadniveaus durchführen, gleichzeitig jedoch nicht ständig viele physische Computer warten.

- Sie möchten eine Infrastrukturumgebung nutzen, die bei Bedarf zentral hoch- und herunterskaliert werden kann.

Das folgende Diagramm veranschaulicht, wie Sie die Anwendungsebenen auf mehreren virtuellen Computern in Azure platzieren können, indem Sie die Präsentationsebene aufgrund erhöhter Volumen eingehender Clientanforderungen horizontal hochskalieren. Wie Sie im Diagramm sehen können, ist der Azure Load Balancer für die Verteilung des Datenverkehrs auf mehrere virtuelle Computer sowie für die Bestimmung der Webserver zum Herstellen einer Verbindung verantwortlich. Durch mehrere Webserverinstanzen hinter einem Load Balancer wird die hohe Verfügbarkeit der Präsentationsebene sichergestellt.

![Anwendungsmuster – Horizontales Hochskalieren auf Präsentationsebene](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728010.png)

### Bewährte Methoden für 2-Ebenen-, 3-Ebenen- oder n-Ebenen-Muster, die über mehrere virtuelle Computer auf einer Ebene verfügen

Es wird empfohlen, dass Sie die virtuellen Computer, die der gleichen Ebene angehören, im selben Clouddienst und in der gleichen Verfügbarkeitsgruppe platzieren. Platzieren Sie z. B. eine Gruppe von Webservern in **Clouddienst1** und **Verfügbarkeitsgruppe1** sowie einen Satz von Datenbankservern in **Clouddienst2** und **Verfügbarkeitsgruppe2**. Durch eine Verfügbarkeitsgruppe in Azure können Sie Hochverfügbarkeitsknoten in separaten Fehler- und Upgradedomänen platzieren.

Um mehrere virtuelle Computerinstanzen einer Ebene zu nutzen, müssen Sie Azure Load Balancer zwischen den Anwendungsebenen konfigurieren. Um den Load Balancer für jede Ebene zu konfigurieren, erstellen Sie einen separaten Endpunkt mit Lastenausgleich auf jedem virtuellen Computer. Für eine bestimmte Ebene müssen Sie zuerst virtuelle Computer im selben Clouddienst erstellen. Dadurch wird sichergestellt, dass sie alle über die gleiche öffentliche virtuelle IP-Adresse verfügen. Als Nächstes erstellen Sie einen Endpunkt auf einem virtuellen Computer in dieser Ebene. Anschließend weisen Sie diesen Endpunkt für den Lastenausgleich den anderen virtuellen Computer in dieser Ebene zu. Durch das Erstellen einer Gruppe mit Lastenausgleich verteilen Sie den Datenverkehr auf mehrere virtuelle Computer und ermöglichen dem Load Balancer festzustellen, mit welchem Knoten eine Verbindung hergestellt werden soll, wenn ein Back-End-VM-Knoten einen Fehler verursacht. Durch mehrere Webserverinstanzen hinter einem Load Balancer wird beispielsweise die hohe Verfügbarkeit der Präsentationsebene sichergestellt.

Darüber hinaus empfiehlt es sich als bewährte Methode, immer sicherzustellen, dass alle Internetverbindungen nur auf der Präsentationsebene erfolgen. Die Präsentationsebene greift auf die Geschäftsebene und die Geschäftsebene wiederum auf die Datenebene zu. Öffnen Sie z. B. einen Endpunkt auf der Präsentationsebene. Jeder Endpunkt verfügt über einen öffentlichen und einen privaten Port. Der private Port wird vom virtuellen Computer intern verwendet, um auf den Verkehr an diesem Endpunkt zu lauschen. Der öffentliche Port ist der Einstiegspunkt für eingehende Kommunikation außerhalb von Azure und wird vom Azure Load Balancer verwendet. Es wird empfohlen, eine Netzwerk-Zugriffssteuerungsliste einzurichten, um Regeln zu definieren, mit deren Hilfe Sie eingehenden Verkehr auf jedem öffentlichen Port und jedem öffentlichen Endpunkt auf jeder Anwendungsebene isolieren und steuern können. Weitere Informationen finden Sie unter [Verwaltung der ACL für einen Endpunkt](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint).

Beachten Sie, dass der Load Balancer in Azure ähnlich wie Load Balancer in einer lokalen Umgebung funktioniert. Weitere Informationen finden Sie unter [Lastenausgleich für Azure-Infrastrukturdienste](virtual-machines-load-balance.md).

Darüber hinaus wird empfohlen, dass Sie mithilfe von Azure Virtual Network ein privates Netzwerk für die virtuellen Computer einrichten. Dies ermöglicht diesen die Kommunikation untereinander über die private IP-Adresse. Weitere Informationen finden Sie unter [Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

## 2 und 3 Ebenen mit horizontal hochskalierter Geschäftsebene

Bei diesem Anwendungsmuster stellen Sie eine 2- oder 3-Ebenen-Datenbankanwendung in Azure Virtual Machines bereit, indem Sie jede Anwendungsebene auf einem anderen virtuellen Computer platzieren. Möglicherweise möchten Sie aufgrund der Komplexität der Anwendung die Anwendungsserverkomponenten darüber hinaus auf mehrere virtuelle Computer verteilen.

Dieses Anwendungsmuster eignet sich für folgende Situationen:

- Sie möchten Unternehmensanwendungen von lokalen virtualisierten Plattformen nach Azure Virtual Machines verschieben.

- Sie möchten aufgrund der Komplexität der Anwendung die Anwendungsserverkomponenten außerdem auf mehrere virtuelle Computer verteilen.

- Sie möchten lokale LOB-Anwendungen mit hohem Unternehmenslogikaufkommen nach Azure Virtual Machines verlagern. Bei LOB-Anwendungen (auch Branchenanwendungen oder Line-of-Business-Anwendungen) handelt es sich um eine Reihe von wichtigen Computeranwendungen für ein Unternehmen, wie z. B. Anwendungen zur Buchhaltung, Personalverwaltung (HR), Lohnbuchhaltung, Lieferkettenmanagement und Ressourcenplanung.

- Sie möchten schnell und für kurze Zeit Entwicklungs- und Testumgebungen bereitstellen.

- Sie möchten Belastungstests für unterschiedliche Workloadniveaus durchführen, gleichzeitig jedoch nicht ständig viele physische Computer warten.

- Sie möchten eine Infrastrukturumgebung nutzen, die bei Bedarf zentral hoch- und herunterskaliert werden kann.

Das folgende Diagramm zeigt ein lokales Szenario und die zugehörige Cloudlösung. In diesem Szenario platzieren Sie die Anwendungsebenen auf mehreren virtuellen Computern in Azure durch horizontales Hochskalieren der Geschäftsebene, die die Geschäftslogikebene und Datenzugriffskomponenten enthält. Wie Sie im Diagramm sehen können, ist der Azure Load Balancer für die Verteilung des Datenverkehrs auf mehrere virtuelle Computer sowie für die Bestimmung der Webserver zum Herstellen einer Verbindung verantwortlich. Durch mehrere Anwendungsserverinstanzen hinter einem Load Balancer wird die hohe Verfügbarkeit der Geschäftsebene sichergestellt. Weitere Informationen finden Sie unter [Bewährte Methoden für 2-, 3- oder n-Ebenen-Anwendungsmuster mit mehreren virtuellen Computern auf einer Ebene](#best-practices-for-2-tier-3-tier-or-n-tier-patterns-that-have-multiple-vms-in-one-tier).

![Anwendungsmuster mit horizontalem Hochskalieren auf Geschäftsebene](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728011.png)

## 2 und 3 Ebenen mit horizontal hochskalierten Präsentations- und Geschäftsebenen sowie HADR

Bei diesem Anwendungsmuster stellen Sie Datenbankanwendungen mit 2 oder 3 Ebenen in Azure Virtual Machines bereit, indem Sie die Präsentationsebene (Webserver) und die Geschäftsebene (Anwendungsserver) auf mehrere virtuelle Computer verteilen. Darüber hinaus implementieren Sie Hochverfügbarkeits-und Notfallwiederherstellungslösungen für Ihre Datenbanken in Azure Virtual Machines.

Dieses Anwendungsmuster eignet sich für folgende Situationen:

- Sie möchten Unternehmensanwendungen von lokalen virtualisierten Plattformen nach Azure verschieben, indem Sie SQL Server-Funktionen für hohe Verfügbarkeit und Notfallwiederherstellung implementieren.

- Sie möchten die Präsentations- und Geschäftsebene aufgrund erhöhter Volumen eingehender Clientanforderungen und der Komplexität Ihrer Anwendung horizontal hochskalieren.

- Sie möchten schnell und für kurze Zeit Entwicklungs- und Testumgebungen bereitstellen.

- Sie möchten Belastungstests für unterschiedliche Workloadniveaus durchführen, gleichzeitig jedoch nicht ständig viele physische Computer warten.

- Sie möchten eine Infrastrukturumgebung nutzen, die bei Bedarf zentral hoch- und herunterskaliert werden kann.

Das folgende Diagramm zeigt ein lokales Szenario und die zugehörige Cloudlösung. In diesem Szenario skalieren Sie die Präsentations- und Geschäftsebenenkomponenten auf mehreren virtuellen Computern in Azure horizontal hoch. Darüber hinaus implementieren Sie Hochverfügbarkeits-und Notfallwiederherstellungstechnologien (HADR) SQL Server-Datenbanken in Azure.

Wenn Sie mehrere Kopien einer Anwendung auf unterschiedlichen virtuellen Computern ausführen, sollten Sie sicherstellen, dass Sie einen Load Balancer für deren Anforderungen untereinander implementieren. Bei mehreren virtuellen Computern müssen Sie sicherstellen, dass alle virtuellen Computer gleichzeitig ausgeführt werden und erreichbar sind. Wenn Sie einen Lastenausgleich konfigurieren, überwacht der Azure Load Balancer den Zustand der virtuellen Computer und leitet eingehende Aufrufe an die ordnungsgemäß funktionierenden virtuellen Computerknoten. Informationen zum Einrichten eines Lastenausgleichs für virtuelle Computer finden Sie unter [Lastenausgleich für Azure-Infrastrukturdienste](virtual-machines-load-balance.md). Durch mehrere Anwendungs- und Webserverinstanzen hinter einem Load Balancer wird die hohe Verfügbarkeit der Präsentations- und Geschäftsebene sichergestellt.

![Horizontale Hochskalierung und hohe Verfügbarkeit](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728012.png)

### Bewährte Methoden für Anwendungsmuster, die SQL-HADR erfordern

Beim Einrichten von SQL Server-Hochverfügbarkeits- und Notfallwiederherstellungslösungen in Azure Virtual Machines ist das Einrichten eines virtuellen Netzwerks für die virtuellen Computer mit [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) unerlässlich. Virtuelle Computer in einem virtuellen Netzwerk verfügen auch nach einem Dienstausfall über eine stabile private IP-Adresse. Sie können somit die Aktualisierungszeit für die Auflösung von DNS-Namen vermeiden. Darüber hinaus ermöglicht das virtuelle Netzwerk es Ihnen, Ihr lokales Netzwerk auf Azure zu erweitern, und es erstellt eine vertrauenswürdige Sicherheitsgrenze. Wenn beispielsweise Ihre Anwendung über Einschränkungen für die Unternehmensdomäne verfügt (wie Windows-Authentifizierung, Active Directory), müssen Sie [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) einrichten.

Die meisten Kunden, die Produktionscode auf Azure ausführen, behalten sowohl primäre als auch sekundäre Replikate in Azure.

Ausführliche Informationen und Lernprogramme zu Hochverfügbarkeits- und Notfallwiederherstellungsmethoden finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server in Azure Virtual Machines](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## 2 und 3 Ebenen mit virtuellen Azure-Computern und Cloud Services

Bei diesem Anwendungsmuster stellen Sie eine Anwendung mit 2 oder 3 Ebenen in Azure bereit, indem Sie sowohl [Azure Cloud Services](cloud-services-choose-me.md#tellmecs) (Web- und Workerrollen – Platform-as-a-Service (PaaS)) als auch [Azure Virtual Machines](virtual-machines/) (Infrastructure-as-a-Service (IaaS)) verwenden. Die Verwendung von [Azure Cloud Services](http://azure.microsoft.com/documentation/services/cloud-services/) für die Präsentations-/Geschäftsebene und SQL Server in [Azure Virtual Machines](virtual-machines-about.md) für die Datenebene bietet für die meisten in Azure ausgeführten Programme Vorteile. Der Grund dafür ist, dass eine Compute-Instanz in Cloud Services einfache Verwaltung, Bereitstellung, Überwachung und horizontale Hochskalierung bietet.

Über Cloud Services verwaltet Azure automatisch die Infrastruktur für Sie, indem es Routinewartungen durchführt, die Betriebssysteme patcht und eine Wiederherstellung nach Dienst- und Hardwarefehlern versucht. Wenn Ihre Anwendung horizontales Hochskalieren erfordert, stehen automatische und manuelle Skalierungsoptionen für das Clouddienstprojekt zur Verfügung, indem die Anzahl der Instanzen oder virtuellen Computern, die von der Anwendung verwendet werden, erhöht oder verringert wird. Darüber hinaus können Sie Visual Studio lokal zum Bereitstellen der Anwendung in einem Clouddienstprojekt in Azure verwenden.

Wenn Sie keine umfangreichen administrativen Aufgaben für die Präsentations-/Geschäftsebenen benötigen und Ihre Anwendung keine komplexen Softwarekonfigurationen oder Betriebssysteme erfordert, sollten Sie Azure Cloud Services verwenden. Wenn Azure SQL-Datenbank nicht alle benötigten Funktionen unterstützt, verwenden Sie SQL Server auf einem virtuellen Azure-Computer für die Datenebene. Die Ausführung einer Anwendung auf Azure Cloud Services und das Speichern von Daten in Azure Virtual Machines verbindet die Vorteile beider Dienste. Einen ausführlichen Vergleich finden Sie in diesem Thema im Abschnitt [Vergleichen der Entwicklungsstrategien in Azure](#comparing-development-strategies-in-azure).

Bei diesem Anwendungsmuster enthält die Präsentationsebene eine Webrolle, bei der es sich um eine Cloud Services-Komponente handelt, die in der Azure-Ausführungsumgebung ausgeführt wird und die für die Programmierung von Webanwendungen (mit IIS- und ASP.NET-Unterstützung) angepasst ist. Die Geschäfts- oder Back-End-Ebene enthält eine Workerrolle, bei der es sich um eine Cloud Services-Komponente handelt, die in der Azure-Ausführungsumgebung ausgeführt wird, der allgemeinen Entwicklung dient und die Hintergrundprozesse für eine Webrolle ausführen kann. Die Datenbankebene befindet sich auf einem virtuellen SQL Server-Computer in Azure. Die Kommunikation zwischen der Präsentations- und der Datenbankebene erfolgt direkt oder über die Workerrollenkomponenten der Geschäftsebene.

Dieses Anwendungsmuster eignet sich für folgende Situationen:

- Sie möchten Unternehmensanwendungen von lokalen virtualisierten Plattformen nach Azure verschieben, indem Sie SQL Server-Funktionen für hohe Verfügbarkeit und Notfallwiederherstellung implementieren.

- Sie möchten eine Infrastrukturumgebung nutzen, die bei Bedarf zentral hoch- und herunterskaliert werden kann.

- Azure SQL-Datenbank unterstützt nicht alle Funktionen, die Ihre Anwendung oder Datenbank benötigt.

- Sie möchten Belastungstests für unterschiedliche Workloadniveaus durchführen, gleichzeitig jedoch nicht ständig viele physische Computer warten.

Das folgende Diagramm zeigt ein lokales Szenario und die zugehörige Cloudlösung. In diesem Szenario platzieren Sie die Präsentationsebene in Webrollen und die Geschäftsebene in Workerrollen, die Datenebene wird jedoch auf virtuelle Computer in Azure verlagert. Durch das Ausführen mehrerer Kopien der Präsentationsebene in verschiedenen Webrollen wird sichergestellt, dass ein Load Balancer für Anfragen zwischen diesen implementiert wird. Wenn Sie Azure Cloud Services mit Azure Virtual Machines kombinieren, empfiehlt es sich, ebenfalls [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) einzurichten. Durch [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) verfügen Sie über stabile und permanente private IP-Adressen innerhalb desselben Clouddiensts in der Cloud. Nachdem Sie ein virtuelles Netzwerk für die virtuellen Computer und Clouddienste definiert haben, können diese mit der Kommunikation untereinander über die private IP-Adresse beginnen. Wenn Sie außerdem virtuelle Computer und Azure-Web- oder -Workerrollen im gleichen [virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) ausführen, erhalten Sie zusätzlich geringe Latenz und höhere Verbindungssicherheit. Weitere Informationen finden Sie unter [Was ist ein Clouddienst](../cloud-services/fundamentals-application-models.md).

Wie Sie im Diagramm sehen können, verteilt der Azure Load Balancer den Datenverkehr auf mehrere virtuelle Computer und legt fest, mit welchem Webserver oder Anwendungsserver eine Verbindung hergestellt wird. Durch mehrere Anwendungs- und Webserverinstanzen hinter einem Load Balancer wird die hohe Verfügbarkeit der Präsentations- und Geschäftsebene sichergestellt. Weitere Informationen finden Sie unter [Bewährte Methoden für Anwendungsmuster, die SQL-HADR erfordern](#best-practices-for-application-patterns-requiring-sql-hadr).

![Anwendungsmuster mit Cloud Services](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728013.png)

Ein anderer Ansatz zum Implementieren dieses Anwendungsmusters ist die Verwendung einer konsolidierten Webrolle mit sowohl Präsentations- als auch Geschäftsebenenkomponenten wie im folgenden Diagramm dargestellt. Dieses Anwendungsmuster eignet sich für Anwendungen, die zustandsbehaftetes Design erfordern. Da Azure zustandslose Computeknoten für Web-und Workerrollen bereitstellt, wird empfohlen, dass Sie eine Logik zum Speichern des Sitzungszustands mithilfe einer der folgenden Technologien implementieren: [Azure Caching](http://azure.microsoft.com/documentation/services/redis-cache/), [Azure-Tabellenspeicher](../storage/storage-dotnet-how-to-use-tables.md) oder [Azure SQL-Datenbank](../sql-database/sql-database-technical-overview.md).

![Anwendungsmuster mit Cloud Services](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728014.png)

## Muster mit Azure-VMs, Azure SQL-Datenbank und Azure-Web-Apps

Das primäre Ziel dieses Anwendungsmusters ist, aufzuzeigen, wie Sie Azure-IaaS-Komponenten mit Azure-PaaS-Komponenten in Ihrer Lösung kombinieren können. Dieses Muster konzentriert sich auf Azure SQL-Datenbank für relationale Datenspeicher. Es umfasst keine SQL Server auf virtuellen Azure-Computern, die Teil des Azure-IaaS-Angebots sind.

Bei diesem Anwendungsmuster stellen Sie eine Datenbankanwendung in Azure bereit, indem Sie die Präsentations- und Geschäftsebenen auf demselben virtuellen Computer platzieren und den Zugriff auf eine Datenbank auf Azure SQL-Datenbankservern ermöglichen. Sie können die Präsentationsebene mit herkömmlichen IIS-basierten Weblösungen implementieren. Oder Sie können eine kombinierte Präsentations- und Geschäftsebene mit [Azure-Web-Apps](http://azure.microsoft.com/documentation/services/app-service/web/) implementieren.

Dieses Anwendungsmuster eignet sich für folgende Situationen:

- Sie verfügen bereits über einen vorhandenen, in Azure konfigurierten SQL-Datenbankserver, und Sie möchten Ihre Anwendung schnell testen.

- Sie möchten die Funktionen der Azure-Umgebung testen.

- Sie möchten schnell und für kurze Zeit Entwicklungs- und Testumgebungen bereitstellen.

- Ihre Geschäftslogik- und Datenzugriffskomponenten können eigenständig in einer Webanwendung vorliegen.

Das folgende Diagramm zeigt ein lokales Szenario und die zugehörige Cloudlösung. In diesem Szenario platzieren Sie die Anwendungsebenen auf einem einzelnen virtuellen Computer in Azure und greifen auf Daten in einer Azure SQL-Datenbank zu.

![Gemischtes Anwendungsmuster](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728015.png)

Wenn Sie eine kombinierte Web- und Anwendungsebene mithilfe von Azure-Web-Apps implementieren, empfehlen wir, dass Sie die mittlere Ebene oder Anwendungsebene als Dynamic Link Libraries (DLLs) im Kontext einer Webanwendung beibehalten.

Lesen Sie außerdem die Empfehlungen im Abschnitt [Vergleichen von Webentwicklungsstrategien in Azure](#comparing-web-development-strategies-in-azure) am Ende dieses Artikels, um mehr über Programmiertechniken zu erfahren.

## n-Ebenen-Hybridanwendungsmuster

Beim n-Ebenen-Hybridanwendungsmuster implementieren Sie Ihre Anwendung auf mehreren Ebenen zwischen lokaler Verteilung und Azure. Aus diesem Grund erstellen Sie ein flexibles und wiederverwendbares Hybridsystem, das Sie ändern oder dem Sie bestimmte Ebenen hinzufügen können, ohne die anderen Ebenen zu ändern. Um Ihr Unternehmensnetzwerk auf die Cloud zu erweitern, verwenden Sie den [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)-Dienst.

Dieses Hybridanwendungsmuster eignet sich für folgende Situationen:

- Sie möchten Anwendungen erstellen, die teilweise in der Cloud und teilweise lokal ausgeführt werden.

- Sie möchten einige oder alle Elemente einer vorhandenen lokalen Anwendung in die Cloud migrieren.

- Sie möchten Unternehmensanwendungen von lokalen virtualisierten Plattformen nach Azure verschieben.

- Sie möchten eine Infrastrukturumgebung nutzen, die bei Bedarf zentral hoch- und herunterskaliert werden kann.

- Sie möchten schnell und für kurze Zeit Entwicklungs- und Testumgebungen bereitstellen.

- Sie suchen eine kostengünstige Möglichkeit, Sicherungen von Unternehmensdatenbank-Anwendungen zu erstellen.

Das folgende Diagramm zeigt ein n-Ebenen-Hybridanwendungsmuster, das sich lokal und auf Azure erstreckt. Wie im Diagramm dargestellt, umfasst die lokale Infrastruktur [Active Directory-Domänendienst](https://technet.microsoft.com/library/hh831484.aspx)-Domänencontroller für die Benutzerauthentifizierung und Autorisierung. Beachten Sie, dass das Diagramm ein Szenario veranschaulicht, in dem sich einige Teile der Datenebene in einem lokalen Rechenzentrum befinden, wohingegen sich andere Teile der Datenebene in Azure befinden. Je nach den Anforderungen Ihrer Anwendung können Sie mehrere Hybridszenarios implementieren. Sie können z. B. die Präsentations- und Geschäftsebene in einer lokalen Umgebung belassen, die Datenebene jedoch nach Azure verlagern.

![n-Ebenen-Anwendungsmuster](./media/virtual-machines-sql-server-application-patterns-and-development-strategies/IC728016.png)

Sie können Active Directory in Azure als eigenständiges Cloudverzeichnis für Ihr Unternehmen verwenden. Es ist auch möglich, ein vorhandenes lokales Active Directory in [Azure Active Directory](http://azure.microsoft.com/documentation/services/active-directory/) zu integrieren. Wie im Diagramm dargestellt, können die Geschäftsebenenkomponenten auf mehrere Datenquellen zugreifen, z. B. auf [SQL Server in Azure](virtual-machines-sql-server-infrastructure-services.md) über eine private interne IP-Adresse, auf einen lokalen SQL Server über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) oder auf eine [SQL-Datenbank](../sql-database/sql-database-technical-overview) über .NET Framework-Datenanbietertechnologien. In diesem Diagramm ist die Azure SQL-Datenbank ein optionaler Datenspeicherdienst.

Beim n-Ebenen-Hybridanwendungsmuster können Sie den folgenden Workflow in der angegebenen Reihenfolge implementieren:

1. Identifizieren Sie Unternehmensdatenbank-Anwendungen, die mithilfe des [Microsoft Assessment and Planning (MAP) Toolkit](http://microsoft.com/map) (in englischer Sprache) in die Cloud verschoben werden müssen. Das MAP-Toolkit erfasst Bestands-und Leistungsdaten von Computern, die Sie für die Virtualisierung in Betracht ziehen, und stellt Empfehlungen für die Kapazitäts- und Bewertungsplanung bereit.

1. Planen Sie die erforderlichen Ressourcen und die Konfiguration in der Azure-Plattform, z. B. Speicherkonten und virtuelle Computer.

1. Richten Sie die Netzwerkkonnektivität zwischen dem lokalen Unternehmensnetzwerk und [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) ein. Um die Verbindung zwischen dem lokalen Unternehmensnetzwerk und einem virtuellen Computer in Azure einzurichten, verwenden Sie eine der beiden folgenden Methoden:
									
	1. Stellen Sie eine Verbindung zwischen lokalem Netzwerk und Azure über öffentliche Endpunkte auf einem virtuellen Computer in Azure her. Diese Methode bietet eine einfache Einrichtung und ermöglicht es Ihnen, SQL Server-Authentifizierung auf dem virtuellen Computer zu verwenden. Richten Sie außerdem die Netzwerk-Zugriffssteuerungsliste (ACL) für die öffentlichen Ports ein, sodass der Zugriff über bestimmte IP-Adressen zugelassen wird. Weitere Informationen finden Sie unter [Verwaltung der ACL für einen Endpunkt](virtual-machines-set-up-endpoints.md/#manage-the-acl-on-an-endpoint).
	
	1. Stellen Sie eine Verbindung zwischen lokalem Netzwerk und Azure über Azure-VPN-Tunnel her. Diese Methode ermöglicht Ihnen die Erweiterung von Domänenrichtlinien auf einen virtuellen Computer in Azure. Darüber hinaus können Sie Firewallregeln einrichten und Windows-Authentifizierung auf Ihrem virtuellen Computer verwenden. Zurzeit unterstützt Azure sichere Standort-zu-Standort-VPN- und Punkt-zu-Standort-VPN-Verbindungen:
	
		- Mit einer sicheren Standort-zu-Standort-Verbindung können Sie die Netzwerkkonnektivität zwischen Ihrem lokalen Netzwerk und dem virtuellen Netzwerk in Azure herstellen. Dies wird für die Verbindung Ihrer lokalen Rechenzentrumsumgebung mit Azure empfohlen.
		
		- Mit einer sicheren Punkt-zu-Standort-Verbindung können Sie die Netzwerkkonnektivität zwischen Ihrem virtuellen Netzwerk in Azure und einzelnen Computern an beliebigen Standorten herstellen. Dies wird hauptsächlich für Entwicklungs- und Testzwecke empfohlen.

	Weitere Informationen zum Herstellen einer Verbindung mit SQL Server in Azure finden Sie unter [Verbinden eines virtuellen SQL Server-Computers in Azure](virtual-machines-sql-server-connectivity.md).

1. Richten Sie geplante Aufträge und Warnungen ein, die lokale Daten auf einem Datenträger eines virtuellen Computers in Azure sichern. Weitere Informationen finden Sie unter [SQL Server-Sicherung und -Wiederherstellung mit dem Windows Azure-BLOB-Speicherdienst](https://msdn.microsoft.com/library/jj919148.aspx) und [Sicherung und Wiederherstellung für SQL Server in Azure Virtual Machines](virtual-machines-sql-server-backup-and-restore.md).

1. Abhängig von den Anforderungen Ihrer Anwendung können Sie eines der folgenden drei gängigen Szenarios implementieren:

	1. Sie können Ihren Webserver, den Anwendungsserver und weniger wichtige Daten auf einem Datenbankserver in Azure und die vertraulichen Daten lokal speichern.
	
	1. Sie können den Webserver und Anwendungsserver lokal und den Datenbankserver auf einem virtuellen Computer in Azure ausführen.
	
	1. Sie können den Datenbankserver, den Webserver und den Anwendungsserver lokal ausführen und Datenbankreplikate auf virtuellen Computern in Azure speichern. Diese Einstellung ermöglicht dem lokalen Webserver oder Anwendungen zur Berichtserstellung den Zugriff auf die Datenbankreplikate in Azure. Aus diesem Grund können Sie mit einer lokalen Datenbank geringere Workloads erzielen. Es wird empfohlen, dass Sie dieses Szenario bei hohen Leseworkloads und zu Entwicklungszwecken implementieren. Informationen zum Erstellen von Datenbankreplikaten in Azure finden Sie in "AlwaysOn-Verfügbarkeitsgruppen" unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server in Azure Virtual Machines](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).

## Vergleichen von Webentwicklungsstrategien in Azure

Zum Implementieren und Bereitstellen einer SQL Server-basierten Anwendung mit mehreren Ebenen in Azure können Sie eine der zwei folgenden Programmiermethoden verwenden:

- Richten Sie einen herkömmlichen Webserver (IIS – Internet Information Services) in Azure ein, und greifen Sie auf SQL Server-Datenbanken in Azure Virtual Machines zu.

- Implementieren und Sie einen Clouddienst in Azure, und stellen Sie ihn bereit. Stellen Sie dann sicher, dass dieser Clouddienst auf Datenbanken von SQL Server in Azure Virtual Machines zugreifen kann. Ein Clouddienst kann mehrere Web- und Workerrollen enthalten.

Die folgende Tabelle enthält einen Vergleich der herkömmlichen Webentwicklung mit Azure Cloud Services und Azure-Web-Apps in Bezug auf SQL Server in Azure Virtual Machines. Die Tabelle umfasst Azure-Web-Apps, da es möglich ist, SQL Server auf einem virtuellen Azure-Computer als Datenquelle für Azure-Web-Apps über die öffentliche virtuelle IP-Adresse oder den DNS-Namen zu verwenden.

||Herkömmliche Webentwicklung in Azure Virtual Machines|Cloud Services in Azure|Webhosting mit Azure-Web-Apps|
|------|--------|-------------|-----------------------| 
|**Lokale Anwendungsmigration**|Vorhandene Anwendungen bleiben unverändert.|Anwendungen benötigen Web-und Workerrollen.|Vorhandene Anwendungen bleiben unverändert, sind aber geeignet für eigenständige Webanwendungen und Webdienste, die schnelle Skalierbarkeit benötigen.| 
|**Entwicklung und Bereitstellung**|Visual Studio, WebMatrix, Visual Web Developer, WebDeploy, FTP, TFS, IIS-Manager, PowerShell.|Visual Studio, Azure SDK, TFS, PowerShell. Jeder Clouddienst hat zwei Umgebungen, in denen Sie das Dienstpaket und die Konfiguration bereitstellen können: Staging und Produktion. Sie können einen Clouddienst für die Stagingumgebung bereitstellen, um diesen zu testen, bevor Sie ihn für die Produktion heraufstufen.|Visual Studio, WebMatrix, Visual Web Developer, FTP, GIT, BitBucket, CodePlex, DropBox, GitHub, Mercurial, TFS, Web Deploy, PowerShell.| 
|**Administration und Einrichtung**|Sie sind verantwortlich für administrative Aufgaben der Anwendung, Daten, Firewallregeln, virtuelles Netzwerk und Betriebssystem.|Sie sind verantwortlich für administrative Aufgaben der Anwendung, Daten, Firewallregeln und virtuelles Netzwerk.|Sie sind verantwortlich für administrative Aufgaben der Anwendung und ausschließlich Daten.| 
|**Hohe Verfügbarkeit und Notfallwiederherstellung (HADR)**|Es wird empfohlen, dass Sie virtuelle Computer in derselben Verfügbarkeitsgruppe und im selben Clouddienst platzieren. Durch das Speichern der virtuellen Computer in derselben Verfügbarkeitsgruppe kann Azure die Hochverfügbarkeitsknoten in separaten Fehler- und Upgradedomänen platzieren. Auf ähnliche Weise ermöglicht das Speichern der virtuellen Computer im selben Clouddienst den Lastenausgleich, und die virtuellen Computer können direkt über das lokale Netzwerk innerhalb eines Azure-Rechenzentrums miteinander kommunizieren.<br/><br/>Sie sind verantwortlich für die Implementierung einer Hochverfügbarkeits- und Notfallwiederherstellungslösung für SQL Server in Azure Virtual Machines, um Ausfallzeiten zu vermeiden. Informationen zu unterstützten HADR-Technologien finden Sie unter [Hochverfügbarkeit und Notfallwiederherstellung für SQL Server in Azure Virtual Machines](virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions.md).<br/><br/>Sie sind verantwortlich für das Sichern Ihrer eigenen Daten und Anwendungen.<br/><br/>Azure kann Ihre virtuellen Computer verschieben, wenn der Hostcomputer im Rechenzentrum aufgrund von Hardwareproblemen fehlschlägt. Darüber hinaus gibt es möglicherweise geplante Ausfallzeiten Ihres virtuellen Computers, wenn der Hostcomputer mit Sicherheits- oder Softwareupdates aktualisiert wird. Daher empfehlen wir, dass Sie mindestens zwei virtuelle Computer in jeder Anwendungsebene betreiben, um die kontinuierliche Verfügbarkeit sicherzustellen. Azure bietet keine SLA für einen einzelnen virtuellen Computer. Weitere Informationen finden Sie unter [Technische Dokumentation zur Geschäftskontinuität mit Azure](https://msdn.microsoft.com/library/azure/hh873027.aspx).|Azure verwaltet die Fehler, die durch die zugrunde liegende Hardware oder das Betriebssystem auftreten. Es wird empfohlen, dass Sie mehrere Instanzen einer Web- oder Workerrolle implementieren, um die hohe Verfügbarkeit Ihrer Anwendung sicherzustellen. Weitere Informationen finden Sie unter [Cloud Services, Virtual Machines, and Virtual Network Service Level Agreement](http://www.microsoft.com/download/details.aspx?id=38427) (in englischer Sprache) und [Notfallwiederherstellung und hohe Verfügbarkeit für Azure-Anwendungen](https://msdn.microsoft.com/library/azure/dn251004.aspx).<br/><br/>Sie sind verantwortlich für das Sichern Ihrer eigenen Daten und Anwendungen.<br/><br/>Bei Datenbanken, die sich in einer SQL Server-Datenbank auf einem virtuellen Azure-Computer befinden, sind Sie verantwortlich für die Implementierung einer Lösung für hohe Verfügbarkeit und Notfallwiederherstellung, um Ausfallzeiten zu vermeiden. Informationen zu unterstützten HDAR-Technologien finden Sie unter "Hochverfügbarkeit und Notfallwiederherstellung für SQL Server in Azure Virtual Machines".<br/><br/>**SQL Server-Datenbankspiegelung**: Verwendung mit Azure Cloud Services (Web-/Workerrollen). Virtuelle SQL Server-Computer und ein Clouddienstprojekt können sich im selben virtuellen Azure-Netzwerk befinden. Wenn sich der virtuelle SQL Server-Computer nicht im gleichen virtuellen Netzwerk befindet, müssen Sie einen SQL Server-Alias zum Weiterleiten der Kommunikation an die SQL Server-Instanz erstellen. Darüber hinaus muss der Aliasname mit dem SQL Server-Namen übereinstimmen.|Hohe Verfügbarkeit wird von Azure-Workerrollen, von Azure-Blob-Speicher und von Azure SQL-Datenbanken geerbt. Beispielsweise werden drei Replikate aller Blob-, Tabellen- und Warteschlangendaten von Azure Storage gespeichert. Zu jedem Zeitpunkt speichert Azure SQL-Datenbank drei Replikate der ausgeführten Daten – ein primäres Replikat und zwei sekundäre Replikate. Weitere Informationen finden Sie unter [Azure Storage](http://azure.microsoft.com/documentation/services/storage) und [Azure SQL-Datenbank](../sql-database/sql-database-technical-overview.md).<br/><br/>Wenn Sie SQL Server auf einem virtuellen Azure-Computer als Datenquelle für Azure-Web-Apps verwenden, sollten Sie bedenken, dass Azure-Web-Apps keine Unterstützung für Azure Virtual Network bieten. Das heißt, dass alle Verbindungen zwischen Azure-Web-Apps und virtuellen Computern mit SQL Server in Azure öffentliche Endpunkte von virtuellen Computern durchlaufen. Dadurch könnten einige Einschränkungen für hohe Verfügbarkeit und Notfallwiederherstellungsszenarios entstehen. So kann beispielsweise die Clientanwendung in Azure-Web-Apps, die eine Verbindung mit dem virtuellen Computer mit SQL Server mit Datenbankspiegelung herstellt, keine Verbindung mit dem neuen primären Server herstellen, da Datenbankspiegelung erfordert, dass Sie Azure Virtual Network zwischen virtuellen SQL Server-Hostcomputern in Azure einrichten. Daher wird die Verwendung von **SQL Server-Datenbankspiegelung** mit Azure-Web-Apps zurzeit nicht unterstützt.<br/><br/>**SQL Server-AlwaysOn-Verfügbarkeitsgruppen**: Sie können AlwaysOn-Verfügbarkeitsgruppen bei der Verwendung von Azure-Web-Apps mit virtuellen SQL Server-Computern in Azure einrichten. Sie müssen jedoch Listener für AlwaysOn-Verfügbarkeitsgruppen konfigurieren, um die Kommunikation über öffentliche Endpunkte mit Lastenausgleich an das primäre Replikat weiterzuleiten.| 
|**Standortübergreifende Konnektivität**|Sie können Azure Virtual Network für lokale Verbindungen verwenden.|Sie können Azure Virtual Network für lokale Verbindungen verwenden.|Azure Virtual Network wird nicht unterstützt. Weitere Informationen finden Sie unter [Web Apps Virtual Network Integration](http://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/) (in englischer Sprache).| 
|**Skalierbarkeit**|Zentrales Hochskalieren ist durch eine Erhöhung der Größe der virtuellen Computer oder durch Hinzufügen weiterer Datenträger verfügbar. Weitere Informationen zu Größen virtueller Computer finden Sie unter [Größen virtueller Computer und Clouddienste für Azure](virtual-machines-size-specs.md).<br/><br/>**Für Datenbankserver**: Horizontale Hochskalierung ist über Datenbankpartitionierungs-Methoden und SQL Server AlwaysOn-Verfügbarkeitsgruppen verfügbar.<br/><br/>Für hohe Leseworkloads können Sie [AlwaysOn-Verfügbarkeitsgruppen](https://msdn.microsoft.com/library/hh510230.aspx) auf mehreren sekundären Knoten und die SQL Server-Replikation verwenden.<br/><br/>Bei hohen Schreibworkloads können Sie horizontale Partitionierung von Daten auf mehreren physischen Servern implementieren, um eine horizontale Hochskalierung der Anwendung bereitzustellen.<br/><br/>Darüber hinaus können Sie die horizontale Hochskalierung über datenabhängiges Routing ([Scaling Out SQL Server with Data Dependent Routing](https://technet.microsoft.com/library/cc966448.aspx), in englischer Sprache) implementieren. Bei datenabhängigem Routing müssen Sie die Partitionierungsmethode in der Clientanwendung implementieren, i. d. R. auf Geschäftsebene, um die Datenbankanforderungen an mehrere SQL Server-Knoten weiterzuleiten. Die Geschäftsebene enthält Zuordnungen der Datenpartitionierung und der Datenknoten.<br/><br/>Sie können Anwendungen skalieren, die virtuelle Computer ausführen. Weitere Informationen finden Sie unter [Skalieren einer Anwendung](../cloud-services/cloud-services-how-to-scale.md).<br/><br/>**Wichtiger Hinweis**: Mit der **AutoScale**-Funktion in Azure können Sie die virtuellen Computer, die von der Anwendung verwendet werden, automatisch vergrößern oder verkleinern. Diese Funktion gewährleistet, dass die Endbenutzererfahrung in Spitzenzeiten nicht negativ beeinflusst wird und dass virtuelle Computer heruntergefahren werden, wenn der Bedarf gering ist. Es wird empfohlen, die AutoScale-Option nicht für den Clouddienst festzulegen, wenn dieser virtuelle Computer mit SQL Server enthält. Dies liegt darin begründet, dass Azure mit der AutoScale-Funktion einen virtuellen Computer aktivieren kann, wenn die CPU-Auslastung auf diesem virtuellen Computer über einem bestimmten Schwellenwert liegt, und einen virtuellen Computer deaktivieren kann, wenn die CPU-Auslastung unter den Schwellenwert sinkt. Die AutoScale-Funktion eignet sich für zustandslose Anwendungen, wie z. B. Webserver, bei denen jeder virtuelle Computer den Workload ohne Verweise auf vorherige Zustände verwalten kann. Die AutoScale-Funktion ist jedoch nicht sinnvoll für zustandsbehaftete Anwendungen, wie SQL Server, bei denen nur eine einzige Instanz in die Datenbank schreiben kann.|Zentrales Hochskalieren ist durch die Verwendung mehrerer Web- und Workerrollen verfügbar. Weitere Informationen zu Größen virtueller Computer für Web- und Workerrollen, finden Sie unter [Konfigurieren von Größen für Cloud Services](../cloud-services/cloud-services-sizes-specs.md).<br/><br/>Bei der Verwendung von **Cloud Services** können Sie mehrere Rollen zur Verteilung der Verarbeitung definieren und eine flexible Skalierung der Anwendung erzielen. Jeder Clouddienst umfasst eine oder mehrere Webrollen und/oder Workerrollen, die jeweils über eigene Anwendungsdateien und eine eigene Konfiguration verfügen. Sie können einen Clouddienst zentral hochskalieren, indem Sie die Anzahl der Rolleninstanzen (virtuelle Computer) erhöhen, die für eine Rolle bereitgestellt werden. Das Herunterskalieren eines Clouddiensts erzielen Sie durch die Verringerung der Anzahl der Rolleninstanzen. Ausführliche Informationen finden Sie unter [Azure-Ausführungsmodelle](fundamentals-application-models.md).<br/><br/>Horizontale Hochskalierung ist über die integrierte Unterstützung für hohe Verfügbarkeit durch unter [Cloud Services, Virtual Machines, and Virtual Network Service Level Agreement](http://www.microsoft.com/download/details.aspx?id=38427) (in englischer Sprache) beschriebenen Dienste und Load Balancer in Azure verfügbar.<br/><br/>Für eine Anwendung mit mehreren Ebenen empfehlen wir, dass Sie Web-/Workerrollenanwendungen mit virtuellen Datenbank-Servercomputern über Azure Virtual Network verbinden. Azure bietet Lastenausgleich für virtuelle Computer innerhalb desselben Clouddiensts und verteilt Benutzeranforderungen über diese Computer. Auf diese Weise verbundene virtuelle Computer können direkt über das lokale Netzwerk innerhalb eines Azure-Rechenzentrums miteinander kommunizieren.<br/><br/>Sie können **AutoScale** im Verwaltungsportal einschließlich der zugehörigen Zeitplanung einrichten. Weitere Informationen finden Sie unter [Skalieren einer Anwendung](../cloud-services/cloud-services-how-to-scale.md).|**Zentrale Hoch- und Herunterskalierung**: Sie können die Größe der Instanz (VM) erhöhen/verringern, die für Ihre Website reserviert ist.<br/><br/>Horizontale Hochskalierung: Sie können weitere reservierte Instanzen (VMs) für Ihre Website hinzufügen.<br/><br/>Sie können **AutoScale** im Verwaltungsportal einschließlich der zugehörigen Zeitplanung einrichten. Weitere Informationen finden Sie unter [Skalieren von Web-Apps](../app-service-web/web-sites-scale.md).|

Weitere Informationen zur Auswahl der richtigen Programmiermethode finden Sie unter [Azure-Web-Apps, Cloud Services und Virtual Machines im Vergleich](../app-service-web/choose-web-site-cloud-service-vm.md).

## Nächste Schritte

Ausführlichere Informationen zur Verwendung von SQL Server auf virtuellen Azure-Computern finden Sie unter [SQL Server in Azure Virtual Machines](virtual-machines-sql-server-infrastructure-services.md).

<!----HONumber=August15_HO8-->