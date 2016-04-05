<properties
	pageTitle="SQL-Datenbank (PaaS) im Vergleich zu SQL Server in der Cloud auf VMs (IaaS) | Microsoft Azure"
	description="Erfahren Sie, welche SQL Server-Cloudoption sich am besten für Ihre Anwendung eignet: Azure SQL-Datenbank (PaaS) oder SQL Server auf Azure Virtual Machines."
	services="sql-database, virtual-machines"
	keywords="SQL Server-Cloud, SQL Server in der Cloud, PaaS-Datenbank, Cloud-SQL Server, DBaaS"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="cjgronlund"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/25/2016"
	ms.author="jeffreyg"/>

# Wählen Sie eine SQL Server-Cloudoption: Azure SQL-Datenbank (PaaS) oder SQL Server auf Azure-VMs (IaaS)

Azure bietet zwei Optionen zum Hosten von SQL Server-Workloads in der Cloud:

* [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/): Eine vollkommen auf die Cloud abgestimmte SQL-Datenbank, auch als PaaS-Datenbank (Platform-as-a-Service) oder DBaaS (Database-as-a-Service) bezeichnet, die für die SaaS-App-Entwicklung (Software-as-a-Service) optimiert ist. Sie ist mit den meisten Features von SQL Server kompatibel.
* [SQL Server auf Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server, installiert und in der Cloud gehostet auf in Azure ausgeführten virtuellen Windows Server-Computern (Windows Server-VMs), auch als Infrastructure-as-a-Service (IaaS) bezeichnet.

Erfahren Sie, welchen Platz diese beiden Optionen in der Microsoft-Datenplattform einnehmen und für welche geschäftlichen Anforderungen sie sich am besten eignen. Ob bei Ihnen Kosteneinsparungen oder minimaler Verwaltungsaufwand Priorität haben – dieser Artikel kann Ihnen dabei helfen, zu entscheiden, welcher Ansatz der richtige ist, um Ihre wichtigsten geschäftlichen Anforderungen zu erfüllen.


## Microsoft-Datenplattform

Eines der ersten Dinge, die in jeder Diskussion von Azure im Vergleich zu lokalen SQL Server-Datenbanken verstanden werden müssen, ist, dass Sie alles verwenden können. Die Microsoft-Datenplattform nutzt SQL Server-Technologie und stellt sie auf unterschiedlichen lokalen physischen Computern, in privaten Cloudumgebungen, in von Drittanbietern gehosteten privaten Cloudumgebungen und in öffentlichen Clouds zur Verfügung. So sind Sie in der Lage, verschiedene einzigartige Geschäftsanforderungen durch eine Kombination aus lokalen und Cloud-gehosteten Bereitstellungen zu erfüllen, können für all diese Umgebungen jedoch auf dieselben Serverprodukte, Entwicklungstools und Fachkenntnisse zurückgreifen.

   ![SQL Server-Cloudoptionen: SQL Server auf IaaS oder SQL-Datenbank (SaaS) in der Cloud.](./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Wie Sie im Diagramm sehen können, kann jedes dieser Angebote durch die Administrationsebene in Bezug auf die Infrastruktur (auf der x-Achse) und den Grad der erreichten Kosteneffizienz durch Datenbankkonsolidierung und Automatisierung (auf der y-Achse) beschrieben werden.

Beim Entwurf einer Anwendung stehen vier grundlegende Optionen für das Hosten des SQL Server-Teils der Anwendung zur Verfügung:

- SQL Server auf nicht virtualisierten physischen Computern
- SQL Server auf lokalen virtualisierten Computern (private Cloud)
- SQL Server in Azure Virtual Machine (öffentliche Cloud)
- Azure SQL-Datenbank (öffentliche Cloud)

In den folgenden Abschnitten erfahren Sie mehr über SQL Server in der öffentlichen Cloud: Azure SQL-Datenbank und SQL Server auf Azure Virtual Machines. Darüber hinaus schauen wir uns an, welche allgemeinen betriebswirtschaftlichen Faktoren es gibt, um zu bestimmen, welche Option für Ihre Anwendung am besten geeignet ist.

## Genauere Betrachtung von Azure SQL-Datenbank und SQL Server auf Azure Virtual Machines

**Azure SQL-Datenbank** ist eine relationale DBaaS-Datenbank (Database-as-a-Service), die in der Azure-Cloud gehostet wird und die in die Branchenkategorien *Software-as-a-Service (SaaS)* und *Platform-as-a-Service (PaaS)* fällt. SQL-Datenbank basiert auf standardisierter Hardware und Software, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Mit SQL-Datenbank können Sie direkt im Dienst mithilfe der integrierten Features und Funktionen entwickeln. Wenn Sie mit SQL-Datenbank arbeiten, bezahlen Sie für das, was Sie nutzen, und haben die Möglichkeit, horizontal oder zentral hochzuskalieren oder ohne Unterbrechung mehr Leistung hinzuzufügen.

**SQL Server auf Azure Virtual Machines (VMs)** fällt in die Branchenkategorie *Infrastructure-as-a-Service (IaaS)* und ermöglicht es Ihnen, SQL Server auf einem virtuellen Computer in der Cloud auszuführen. Ähnlich wie SQL-Datenbank basiert dieses Angebot auf standardisierter Hardware und Software, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Wenn Sie SQL Server auf einem virtuellen Computer verwenden, können Sie entweder Ihre eigene SQL Server-Lizenz einbringen oder eines der vorlizenzierten SQL Server-Images aus dem Azure-Portal nutzen.

Im Allgemeinen sind diese beiden SQL-Optionen für unterschiedliche Zwecke optimiert:

- **SQL-Datenbank** wurde zur Minimierung der Gesamtkosten für die Bereitstellung und Verwaltung vieler Datenbanken optimiert. Die laufenden Administrationskosten werden reduziert, weil Sie keine virtuellen Computer, Betriebssysteme oder Datenbanksoftware verwalten müssen. Auch um Upgrades, hohe Verfügbarkeit und Sicherungen müssen Sie sich nicht kümmern. Im Allgemeinen lässt sich mit Azure SQL-Datenbank sich die Anzahl der Datenbanken, die von einer einzelnen IT- oder Entwicklungsressource verwaltet werden können, drastisch steigern.
- **SQL Server auf Azure Virtual Machines** ist für die Erweiterung vorhandener lokaler SQL Server-Anwendungen in die Cloud (ein Hybridszenario) oder für die Bereitstellung einer vorhandenen Anwendung in Azure (ein Migrations- oder Entwicklungs-/Test-Szenario) optimiert. Ein Beispiel für ein Hybridszenario ist das Vorhalten von sekundären Datenbankreplikaten in Azure über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Mit SQL Server auf Azure Virtual Machines verfügen Sie über eine dedizierte Instanz von SQL Server und einen cloudbasierten virtuellen Computer (VM, Virtual Machine) mit vollständigen Administratorrechten. Dies ist die perfekte Lösung, wenn ein Unternehmen bereits IT-Ressourcen verfügbar hat, um die virtuellen Computer zu verwalten. Mit SQL Server auf virtuellen Computern können Sie ein maßgeschneidertes System aufbauen, das die spezifischen Leistungs- und Verfügbarkeitsanforderungen Ihrer Anwendung erfüllt.

In der folgenden Tabelle sind die wesentlichen Merkmale von SQL-Datenbank und SQL Server auf Azure Virtual Machines zusammengefasst:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">SQL-Datenbank</th>
   <th align="left" valign="middle">SQL Server auf Azure Virtual Machines</th>

</tr>
<tr>
   <td valign="middle"><p><b>Geeignet für</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Neue Cloud-entwickelte Anwendungen, die zeitlichen Einschränkungen bei Entwicklung und Marketing unterliegen.
          <li type=round>Anwendungen, die integrierte hohe Verfügbarkeit, Notfallwiederherstellung und Upgrademechanismen benötigen.
          <li type=round>Teams, die das zugrunde liegende Betriebssystem und Konfigurationseinstellungen nicht selbst verwalten möchten.
         <li type=round>Anwendungen mit horizontalen Skalierungsmustern.
         <li type=round>Datenbanken mit bis zu 1 TB Größe.
         <li type=round>Erstellen von SaaS-Anwendungen (Software-as-a-Service).
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Vorhandene Anwendungen, die schnelle Migration zur Cloud mit minimalen Änderungen erfordern.
      <li type=round>SQL Server-Anwendungen, die aus Azure über einen sicheren Tunnel auf lokale Ressourcen (z.&#160;B. Active Directory) zugreifen müssen.
      <li type=round>Wenn Sie eine angepasste IT-Umgebung mit vollen Administratorrechten benötigen.
      <li type=round>Schnelle Entwicklungs- und Testszenarien, wenn Sie keine lokale SQL&#160;Server-Hardware für andere Zwecke als für den Produktivbetrieb erwerben möchten.
      <li type=round>Notfallwiederherstellung für lokale SQL Server-Anwendungen mithilfe einer [Sicherung in Azure Storage](http://msdn.microsoft.com/library/jj919148.aspx) oder mithilfe von AlwaysOn-Replikaten mit virtuellen Azure-Computern.
      <li type=round>Große Datenbanken, die größer als 1 TB groß sind.
      </ul></td>
</tr>
<tr>
   <td valign="middle"><p><b>Ressourcen</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>Sie möchten keine IT-Ressourcen für Support und Wartung der zugrunde liegenden Infrastruktur einsetzen.
       <li type=round>Sie möchten sich auf die Anwendungsebene konzentrieren.
       </ul></td>
   <td valign="middle"><ul><li type=round>Sie haben IT-Ressourcen für Support und Wartung.</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Gesamtbetriebskosten</b></p></td>
   <td valign="middle"><ul><li type=round>Eliminiert Hardwarekosten. Senkt Verwaltungskosten.</ul></td>
   <td valign="middle"><ul><li type=round>Eliminiert Hardwarekosten. </ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Geschäftskontinuität</b></p></td>
   <td valign="middle"><ul><li type=round>Zusätzlich zu den integrierten Fehlertoleranz-Infrastrukturfunktionen stellt die Azure SQL-Datenbank Funktionen bereit wie z. B. die Zeitpunktwiederherstellung, geografische Wiederherstellung und geografische Replikation zur Steigerung der Geschäftskontinuität. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität in SQL-Datenbank](sql-database-business-continuity.md).</ul></td>
   <td valign="middle"><ul><li type=round>Mit SQL&#160;Server auf Azure Virtual Machines können Sie eine Hochverfügbarkeits- und Notfallwiederherstellungslösung für die spezifischen Anforderungen Ihrer Datenbank einrichten. Aus diesem Grund haben Sie ein System, das genau für Ihre Anwendung optimiert ist. Sie können bei Bedarf Failovers selbst testen und ausführen. Weitere Informationen finden Sie unter [Hohe Verfügbarkeit und Notfallwiederherstellung für SQL Server auf Azure Virtual Machines](../virtual-machines/virtual-machines-windows-classic-sql-dr.md).</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Hybrid Cloud</b></p></td>
   <td valign="middle"><ul><li type=round>Die lokale Anwendung kann auf Daten in der Azure SQL-Datenbank zugreifen.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Mit SQL Server auf Azure Virtual Machines können Sie Anwendungen betreiben, die teilweise in der Cloud und teilweise lokal ausgeführt werden. Beispielsweise können Sie Ihr lokales Netzwerk und Ihre Active Directory-Domäne mit [Azure Virtual Network](../virtual-network/virtual-networks-overview.md) in die Cloud ausdehnen. Außerdem können Sie lokale Datendateien in Azure Storage speichern, indem Sie [SQL&#160;Server-Datendateien in Azure](http://msdn.microsoft.com/library/dn385720.aspx) verwenden. Weitere Informationen finden Sie unter [Einführung in SQL Server 2014 Hybrid Cloud](http://msdn.microsoft.com/library/dn606154.aspx).
      <li type=round>Unterstützt die Notfallwiederherstellung für lokale SQL Server-Anwendungen mithilfe der [SQL Server-Sicherung und -Wiederherstellung mit Azure Blob Storage](http://msdn.microsoft.com/library/jj919148.aspx) oder mit [AlwaysOn-Replikaten in Azure Virtual Machines](../virtual-machines/virtual-machines-windows-classic-sql-dr.md).
      </ul></td>

</tr>
</table>

## Entscheidungskriterien für die Wahl zwischen Azure SQL-Datenbank und SQL Server auf Azure Virtual Machines

### Kosten

Ob Sie ein Startup-Unternehmen mit wenig liquiden Mitteln oder ein Team in einem etablierten Unternehmen sind, das unter engen Budgetbeschränkungen arbeitet: häufig sind begrenzte Mittel die primäre Motivation bei der Entscheidung, wie Sie Ihre Datenbanken hosten. In diesem Abschnitt erfahren Sie zunächst etwas über die Grundlagen der Abrechnung und Lizenzierung in Azure bei diesen beiden Optionen für die relationale Datenbank: SQL-Datenbank und SQL Server auf Azure Virtual Machines. Dann sehen wir, wie wir die Kosten für die gesamte Anwendung berechnen sollten.

#### Abrechnungs- und Lizenzierungsgrundlagen

**SQL-Datenbank** wird als Dienst an Kunden verkauft, ohne dass dies mit einer Lizenzierung verbunden ist. Für SQL Server auf Azure Virtual Machines ist hingegen eine klassische SQL Server-Lizenzierung erforderlich.

Derzeit stehen für **SQL-Datenbank** verschiedene Tarife zur Verfügung, bei denen ein fester Stundensatz je nach ausgewähltem Tarif und ausgewählter Leistungsstufe abgerechnet wird. Darüber hinaus wird der ausgehende Internetdatenverkehr in Rechnung gestellt. Die Tarife Basic, Standard und Premium liefern vorhersagbare Leistung mit mehreren Leistungsstufen entsprechend den Spitzenanforderungen der Anwendung. Sie können zwischen Tarifen und Leistungsstufen entsprechend den unterschiedlichen Durchsatzanforderungen Ihrer Anwendung wechseln. Wenn Ihre Datenbank ein hohes Transaktionsvolumen aufweist und viele gleichzeitige Benutzer unterstützen muss, empfehlen wir, den Tarif „Premium“ zu verwenden. Die neuesten Informationen zu den derzeit unterstützten Tarifen finden Sie unter [Tarife für Azure SQL-Datenbank](sql-database-service-tiers.md).

Bei **SQL-Datenbank** wird die Datenbanksoftware automatisch von Microsoft konfiguriert, gepatcht und aktualisiert. Dadurch sinken Ihre Verwaltungskosten. Darüber hinaus helfen Ihnen die [integrierten Datensicherungsfunktionen](sql-database-business-continuity.md) dabei, erhebliche Kosteneinsparungen zu erzielen, vor allem bei einer großen Anzahl von Datenbanken.

Bei **SQL Server auf Azure Virtual Machines** nutzen Sie die herkömmliche SQL Server-Lizenzierung. Sie können entweder das auf der Plattform bereitgestellte SQL Server-Image verwenden, das auch eine Lizenz umfasst, oder Ihre eigene SQL Server-Lizenz einbringen. Bei Verwendung der von Azure bereitgestellten Images hängen die Betriebskosten von der Größe des virtuellen Computers und von der auswählten SQL Server-Edition ab. Unabhängig von der Größe des virtuellen Computers und der SQL Server-Edition bezahlen Sie einen minutenbezogenen Lizenzpreis für SQL Server und Windows Server. Hinzu kommt noch der Azure Storage-Preis für die VM-Datenträger. Mit der minutenbezogenen Abrechnungsoption können Sie SQL Server solange wie nötig verwenden, ohne zusätzliche SQL Server-Lizenzen zu erwerben. Wenn Sie eine eigene SQL Server-Lizenz in Azure einbringen, werden Ihnen nur Windows Server- und Speicherkosten in Rechnung gestellt. Weitere Informationen über das Einbringen eigener Lizenzen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### Berechnen der gesamten Anwendungskosten

Wenn Sie eine Cloudplattform verwenden, umfassen die Kosten der Anwendungsausführung zu Beginn hauptsächlich die Kosten für Entwicklung und Verwaltung sowie die Servicekosten, die für die öffentliche Cloudplattform anfallen.

Hier ist eine ausführliche Kostenberechnung für die Ausführung Ihrer Anwendung in SQL-Datenbank und SQL Server auf Azure Virtual Machines:

**Bei Verwendung von Azure SQL-Datenbank:**

*Gesamtkosten der Anwendung = stark minimierte Verwaltungskosten + Softwareentwicklungskosten + Servicekosten für SQL-Datenbank*

**Bei Verwendung von SQL Server auf Azure Virtual Machines:**

*Gesamtkosten der Anwendung = minimierte Kosten für Softwareentwicklung/-änderungen + Verwaltungskosten + SQL Server- und Windows Server-Lizenzkosten + Kosten für Azure Storage*

Weitere Informationen zur Preisberechnung finden Sie in den folgenden Ressourcen:

- [SQL-Datenbank – Preisübersicht](https://azure.microsoft.com/pricing/details/sql-database/)
- [Virtual Machines – Preisübersicht](https://azure.microsoft.com/pricing/details/virtual-machines/) für [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) und für [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Azure-Preisrechner](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Es gibt eine kleine Teilmenge von Features in SQL Server, die bei SQL-Datenbank nicht nutzbar oder nicht verfügbar ist. Weitere Informationen finden Sie unter [SQL-Datenbanken – Allgemeine Einschränkungen und Leitlinien](sql-database-general-limitations.md) und [SQL-Datenbank – Transact-SQL-Informationen](sql-database-transact-sql-information.md). Wenn Sie eine vorhandene SQL Server-Lösung in die Cloud überführen, beachten Sie die Hinweise unter [Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank](sql-database-cloud-migrate.md). Falls Sie eine vorhandene lokale SQL Server-Anwendung zu SQL-Datenbank migrieren, lohnt es sich unter Umständen, die Anwendung so zu aktualisieren, dass Sie von unseren Clouddiensten profitieren können. Ziehen z. B. in Betracht, den [Azure Web-Apps-Dienst](https://azure.microsoft.com/services/app-service/web/) oder [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services/) zum Hosten Ihrer Anwendungsschicht zu nutzen, um die Kostenvorteile zu vergrößern.

### Verwaltung

Bei vielen Unternehmen wird die Entscheidung zum Wechsel auf einen Clouddienst genauso stark vom Bestreben um eine Vereinfachung der Verwaltung wie von den erhofften Kostenvorteilen getrieben. Bei **SQL-Datenbank** verwaltet Microsoft die zugrunde liegende Hardware, repliziert automatisch alle Daten zur Gewährleistung einer hohen Verfügbarkeit, konfiguriert und aktualisiert die Datenbanksoftware, verwaltet den Lastenausgleich und führt bei einem Serverausfall ein transparentes Failover durch. Sie können weiterhin Ihre Datenbank administrieren, müssen aber das Datenbankmodul, das Serverbetriebssystem und die Hardware nicht mehr verwalten. Weiterhin verwalten können Sie z. B. die Datenbanken und Anmeldungen, die Index- und Abfrageoptimierung, die Überwachung und die Sicherheit.

Auf der anderen Seite verfügen Sie möglicherweise über internes Fachwissen und möchten den Speicherort der Datenbank bis hin zum Pfad auf dem Datenträger selbst kontrollieren. Bei **SQL Server auf Azure Virtual Machines** haben Sie die vollständige Kontrolle über das Betriebssystem und die Konfiguration der SQL Server-Instanzen. Mit einem virtuellen Computer (VM, Virtual Machine) können Sie entscheiden, wann das Betriebssystem und die Datenbanksoftware aktualisiert werden und wann zusätzliche Software wie z. B. Antiviren- und Sicherungstools installiert werden sollen. Darüber hinaus können Sie die Größe des virtuellen Computers, die Anzahl der Laufwerke und deren Speicherkonfigurationen steuern. Azure ermöglicht beispielsweise die Größenänderung eines virtuellen Computers je nach Bedarf. Weitere Informationen finden Sie unter [Größen von virtuellen Computern und Clouddiensten für Azure](../virtual-machines/virtual-machines-linux-sizes.md).

### Vereinbarung zum Servicelevel (SLA)

Für viele IT-Abteilungen hat die Einhaltung vereinbarter Betriebszeiten und Servicelevels höchste Priorität. In diesem Abschnitt wird erläutert, welche Vereinbarung zum Servicelevel (SLA) für die jeweilige Datenbank-Hostingoption gilt.

Für **SQL-Datenbank** bietet Microsoft für die Tarife Basic, Standard und Premium eine Verfügbarkeits-SLA von 99,99 %. Aktuelle Informationen finden Sie unter [Vereinbarung zum Servicelevel](https://azure.microsoft.com/support/legal/sla/sql-database/). Aktuelle Informationen zu den Tarifen für SQL-Datenbank und zu den unterstützten Geschäftskontinuitätsplänen finden Sie unter [Tarife](sql-database-service-tiers.md).

Für **SQL Server auf Azure Virtual Machines** bietet Microsoft eine Verfügbarkeits-SLA von 99.95 %, die nur den virtuellen Computer abdeckt. Diese SLA deckt nicht die Prozesse ab, die auf dem virtuellen Computer ausgeführt werden (z. B. SQL Server). Außerdem erfordert sie, dass mindestens zwei VM-Instanzen in einer Verfügbarkeitsgruppe gehostet werden. Aktuelle Informationen finden Sie unter [SLA für Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Für eine besonders hohe Datenbankverfügbarkeit auf den virtuellen Computern sollten Sie eine der unterstützten Hochverfügbarkeitsoptionen in SQL Server konfigurieren, wie z. B. [AlwaysOn-Verfügbarkeitsgruppen](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

### <a name="market"></a>Zeit bis zur Marktreife

**SQL-Datenbank** ist die richtige Lösung für Anwendungen für die Cloud, wenn Entwicklerproduktivität und eine schnelle Markteinführung entscheidend sind. Durch programmgesteuerte DBA-ähnliche Funktionen ist sie perfekt für Cloud-Architekten und Entwickler, da sie den Verwaltungsaufwand für das zugrunde liegende Betriebssystem und die Datenbank verringert. Sie können z. B. die [REST-API](http://msdn.microsoft.com/library/azure/dn505719.aspx) und [PowerShell-Cmdlets](http://msdn.microsoft.com/library/azure/dn546726.aspx) verwenden, um administrative Vorgänge für Tausende von Datenbanken zu automatisieren und zu verwalten. Features wie z. B. [elastische Datenbankpools](sql-database-elastic-pool.md) ermöglichen es Ihnen, sich ganz auf die Anwendungsschicht zu konzentrieren und Ihre Lösung schneller auf den Markt zu bringen.

**SQL Server auf Azure Virtual Machines** eignet sich hervorragend, wenn die vorhandenen oder neuen Anwendungen auf alle Features einer SQL Server-Instanz zugreifen und diese auch steuern müssen. Diese Option ist auch eine gute Wahl, wenn Sie vorhandene lokale Anwendungen und Datenbanken unverändert zu Azure migrieren möchten. Da Sie keine Änderungen an der Präsentations-, Anwendungs- und Datenschicht vornehmen müssen, sparen Sie sich die Zeit und das Geld, die sonst für den Neuentwurf der vorhandenen Lösung nötig wären. Sie können in diesem Fall alle Lösungen sofort zu Azure migrieren und müssen lediglich einige Leistungsoptimierungen durchführen, die möglicherweise von der Azure-Plattform verlangt werden. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](../virtual-machines/virtual-machines-windows-classic-sql-perf.md).

## Zusammenfassung

In diesem Artikel wurden SQL-Datenbank und SQL Server auf Azure Virtual Machines vorgestellt und einige geschäftliche Kriterien diskutiert, die Ihre Entscheidung für eine der beiden Optionen beeinflussen können. Nachfolgend finden Sie eine Zusammenfassung der dieser Kriterien:

Entscheiden Sie sich für **Azure SQL-Datenbank**, wenn für Sie Folgendes zutrifft:

- Sie erstellen neue cloudbasierte Anwendungen oder möchten eine vorhandene SQL Server-Lösung migrieren, um von den Kostenvorteilen und Leistungsoptimierungen bei einem Clouddienst zu profitieren. Die Vorteile bei diesem Ansatz: Sie nutzen einen vollständig verwalteten Clouddienst, Sie können die Zeit bis zur Marktreife verkürzen und langfristig Kosten sparen.

- Sie möchten, dass Microsoft allgemeine Verwaltungsvorgänge für Ihre Datenbanken ausführt, und Sie benötigen striktere Verfügbarkeits-SLAs für Datenbanken.



Entscheiden Sie sich für **SQL Server auf Azure Virtual Machines**, wenn für Sie Folgendes zutrifft:

- Sie verfügen über lokale Anwendungen und möchten Ihre eigene Hardware nicht mehr selbst verwalten oder ziehen Hybridlösungen in Betracht. Die Vorteile bei diesem Ansatz: Sie können eher auf eine hohe Datenbankkapazität zugreifen und ermöglichen eine Verbindung mit den lokalen Anwendungen über einen sicheren Tunnel.

- Sie verfügen über vorhandene IT-Ressourcen, benötigen volle Administratorrechte für SQL Server und sind auf eine vollständige Kompatibilität mit lokalen Installationen von SQL Server angewiesen. Mit dieser Methode können Sie die Kosten für Entwicklung oder Änderungen an vorhandenen Anwendungen minimieren und behalten die Flexibilität zur Ausführung der meisten Anwendungen. Darüber hinaus bietet sie einen vollständigen Zugriff auf den virtuellen Computer, das Betriebssystem und die Datenbankkonfiguration.



> [AZURE.NOTE] Möchten Sie SQL Server 2016 CTP2 testen? Registrieren Sie sich für Microsoft Azure, und fahren Sie anschließend [hier](http://aka.ms/sql2016vm "hier") fort, um einen virtuellen Computer mit einer Installation von SQL Server 2016 CTP2 zu erstellen.

## Nächste Schritte
- Erste Schritte für die SQL-Datenbank finden Sie unter [SQL Database tutorial: Create a SQL database in minutes using the Azure portal](sql-database-get-started.md) (SQL-Datenbank-Tutorial: Erstellen einer SQL-Datenbank in wenigen Minuten mit dem Azure-Portal).
- Informationen finden Sie unter [Preise für SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/).
- Informationen zu den ersten Schritten mit SQL Server auf virtuellen Azure-Computern finden Sie unter [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md).

<!---HONumber=AcomDC_0330_2016-->