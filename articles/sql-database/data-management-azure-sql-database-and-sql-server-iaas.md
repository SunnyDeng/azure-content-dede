<properties
	pageTitle="SQL-Datenbank (SaaS) im Vergleich zu SQL Server in der Cloud | Microsoft Azure"
	description="Erfahren Sie, welche SQL Server-Cloudoption sich am besten für Ihre Anwendung eignet: Azure SQL-Datenbank (SaaS) oder SQL Server in der Cloud in Azure Virtual Machines."
	services="sql-database, virtual-machines"
	keywords="SQL Server cloud, SQL Server in the cloud, SaaS database, cloud SQL Server"
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
	ms.date="11/16/2015"
	ms.author="jeffreyg"/>

# Wählen Sie eine SQL Server-Cloudoption: Azure SQL-Datenbank (SaaS) oder Azure SQL Server in der Cloud auf VMs

Microsoft Azure bietet zwei Optionen für SQL Server in der Cloud:

* [Azure SQL-Datenbank](https://azure.microsoft.com/services/sql-database/): eine native SQL-Datenbankinstanz in der Cloud oder eine SaaS-Datenbank (Software-as-a-Service).
* [SQL Server auf Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/): eine in der Cloud installierte und gehostete SQL Server-Instanz (Infrastructure-as-a-Service, IaaS).

Erfahren Sie, wie diese beiden Optionen auf der Microsoft-Datenplattform bereitgestellt werden. Anschließend werden Sie dabei unterstützt, die richtige SQL Server-Cloudoption für Ihre Geschäftsanforderungen zu wählen. Ob bei Ihnen Kosteneinsparungen oder minimaler Verwaltungsaufwand Priorität haben – dieser Artikel kann Ihnen dabei helfen, zu entscheiden, welcher Ansatz der richtige ist, um Ihre wichtigsten geschäftlichen Anforderungen zu erfüllen.

- [Microsoft-Datenplattform](#platform)
- [Genauere Betrachtung von Azure SQL-Datenbank und SQL Server in Azure VM](#close)
- [Entscheidungsfaktoren bei der Wahl zwischen Azure SQL-Datenbank und SQL Server in Azure VM](#business)
	- [Kosten](#cost)
		- [Abrechnung und Lizenzierung](#billing)
		- [Berechnung der Gesamtkosten für die Anwendung](#appcost)
	- [Verwaltung](#admin)
	- [Vereinbarung zum Servicelevel (SLA)](#sla)
	- [Zeit bis zur Marktreife](#market)
- [Zusammenfassung](#summary)
- [Bestätigungen](#ack)
- [Zusätzliche Ressourcen](#resources)


##<a name="platform"></a>Microsoft-Datenplattform

Eines der ersten Dinge, die in jeder Diskussion von Azure im Vergleich zu lokalen SQL Server-Datenbanken verstanden werden müssen, ist, dass Sie alles verwenden können. Die Microsoft-Datenplattform nutzt SQL Server-Technologie und stellt sie auf unterschiedlichen lokalen physischen Computern, in privaten Cloudumgebungen, in von Drittanbietern gehosteten privaten Cloudumgebungen und in öffentlichen Clouds zur Verfügung. So sind Sie in der Lage, verschiedene einzigartige Geschäftsanforderungen durch eine Kombination aus lokalen und Cloud-gehosteten Bereitstellungen zu erfüllen, können für all diese Umgebungen jedoch auf dieselben Serverprodukte, Entwicklungstools und Fachkenntnisse zurückgreifen.

   ![SQL Server-Cloudoptionen: SQL Server auf IaaS oder SQL-Datenbank (SaaS) in der Cloud.][1]

Wie Sie im Diagramm sehen können, kann jedes dieser Angebote durch die Administrationsebene in Bezug auf die Infrastruktur (auf der x-Achse) und den Grad der erreichten Kosteneffizienz durch Datenbankkonsolidierung und Automatisierung (auf der y-Achse) beschrieben werden.

Beim Entwurf einer Anwendung stehen vier grundlegende Optionen für das Hosten des SQL Server-Teils der Anwendung zur Verfügung:

- SQL Server auf nicht virtualisierten physischen Computern
- SQL Server auf lokalen virtualisierten Computern (private Cloud)
- SQL Server in Azure Virtual Machine (öffentliche Cloud)
- Azure SQL-Datenbank (öffentliche Cloud)

In den folgenden Abschnitten erfahren Sie mehr über SQL Server in der öffentlichen Cloud: Azure SQL-Datenbank und SQL Server in Azure VMs. Darüber hinaus schauen wir uns an, welche allgemeinen betriebswirtschaftlichen Faktoren es gibt, um zu bestimmen, welche Option für Ihre Anwendung am besten geeignet ist.

##<a name="close"></a>Genauere Betrachtung von Azure SQL-Datenbank und SQL Server in Azure VM

**Microsoft Azure SQL-Datenbank (Azure SQL-Datenbank)** ist eine relationale Datenbank (Database-as-a-Service), die in der Cloud gehostet wird und in die Branchenkategorien *Software-as-a-Service (SaaS)* und *Platform-as-a-Service (PaaS)* fällt. Azure SQL-Datenbank basiert auf standardisierter Hardware und Software, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Mit SQL-Datenbank können Sie direkt im Dienst mithilfe der integrierten Features und Funktionen entwickeln. Wenn Sie mit der SQL-Datenbank arbeiten, bezahlen Sie für das, was Sie nutzen, und haben die Möglichkeit, horizontal zu skalieren oder mehr Leistung hinzuzufügen.

**SQL Server in Azure Virtual Machines (VM)** fällt in die Branchenkategorie *Infrastructure-as-a-Service (IaaS)* und ermöglicht es Ihnen, SQL Server auf einem virtuellen Computer in der Cloud auszuführen. Ähnlich wie Azure SQL-Datenbank basiert es auf standardisierter Hardware und Software, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Wenn Sie SQL Server auf einem virtuellen Computer verwenden, können Sie entweder Ihre eigene SQL Server-Lizenz unter Azure verwenden oder eines der vorkonfigurierten SQL Server-Abbilder im Azure-Portal nutzen.

Im Allgemeinen sind diese beiden SQL-Optionen für unterschiedliche Zwecke optimiert:

- **Azure SQL-Datenbank** wurde zur Senkung der Gesamtkosten für die Bereitstellung und Verwaltung vieler Datenbanken auf ein absolutes Mindestmaß optimiert. Die laufenden Administrationskosten werden minimiert, da keine virtuellen Computer, Betriebssysteme oder Datenbanksoftware verwaltet werden muss, einschließlich Upgrades, hohe Verfügbarkeit und Sicherungen. Mit der SQL-Datenbank lässt sich die Anzahl der Datenbanken, die von einer einzelnen IT- oder Entwicklungsressource verwaltet werden müssen, im Allgemeinen drastisch reduzieren.
- **SQL Server in Azure VM** ist für die Erweiterung vorhandener lokaler SQL Server-Anwendungen in die Azure-Cloud in einem Hybridszenario oder für die Bereitstellung einer vorhandenen Anwendung in Azure in einem Migrations- oder Dev/Test-Szenario optimiert. Ein Beispiel für ein Hybridszenario ist das Vorhalten von sekundären Datenbankreplikaten in Azure über [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Mit SQL Server auf Azure VMs verfügen Sie über eine dedizierte Instanz von SQL Server und eine Cloud-basierte VM mit vollständigen Administratorrechten. Dies ist die perfekte Lösung, wenn ein Unternehmen bereits IT-Ressourcen verfügbar hat, um die virtuellen Computer zu verwalten. Mit SQL Server auf virtuellen Computern können Sie ein maßgeschneidertes System zur Einhaltung anwendungsspezifischer Performance- und Verfügbarkeitsanforderungen erstellen.

In der folgende Tabelle sind die wesentlichen Merkmale der Azure SQL-Datenbank und von SQL Server in Azure VM zusammengefasst:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Azure SQL-Datenbank</th>
   <th align="left" valign="middle">SQL Server auf Azure VM</th>

</tr>
<tr>
   <td valign="middle"><p><b>Geeignet für</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Neue Cloud-entwickelte Anwendungen, die zeitlichen Einschränkungen bei Entwicklung und Marketing unterliegen.
          <li type=round>Anwendungen, die integrierte, automatische hohe Verfügbarkeit, Notfallwiederherstellungslösungen und Upgrade-Mechanismen benötigen.
          <li type=round>Wenn Sie Hunderte oder Tausende von Datenbanken haben, aber nicht das zugrunde liegende Betriebssystem, die Hardware und Konfigurationseinstellungen verwalten möchten.
         <li type=round>Anwendungen mit horizontalen Skalierungsmustern.
         <li type=round>Datenbanken mit bis zu 1 TB Größe.
         <li type=round>Erstellen von Software-as-a-Service-Anwendungen.

  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Vorhandene Anwendungen, die schnelle Migration zur Cloud mit minimalen Änderungen erfordern.
      <li type=round>SQL Server-Anwendungen, die Zugriff auf lokale Ressourcen (z. B. Active Directory) von Azure über einen sicheren Tunnel erfordern.
      <li type=round>Wenn Sie eine angepasste IT-Umgebung mit vollen Administratorrechten benötigen.
      <li type=round>Schnelle Entwicklung- und Testszenarios, wenn Sie nicht lokale SQL Server-Hardware außerhalb der Produktion kaufen möchten.
      <li type=round>Notfallwiederherstellung für lokale SQL&#160;Server-Anwendungen mithilfe der <a href="http://msdn.microsoft.com/library/jj919148.aspx">Sicherung in Azure Storage</a> oder <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">AlwaysOn-Replikate auf virtuellen Azure-Computern</a>.
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
   <td valign="middle"><ul><li type=round>Zusätzlich zu den integrierten Fehlertoleranz-Infrastrukturfunktionen stellt die Azure SQL-Datenbank Funktionen bereit wie z. B. die Zeitpunktwiederherstellung, geografische Wiederherstellung und geografische Replikation zur Steigerung der Geschäftskontinuität. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Azure SQL-Datenbank-Geschäftskontinuität</a>.</ul></td>
   <td valign="middle"><ul><li type=round>Mit SQL Server in Azure VMs können Sie eine Hochverfügbarkeits- und Notfallwiederherstellungslösung für die spezifischen Anforderungen Ihrer Datenbank einrichten. Aus diesem Grund haben Sie ein System, das genau für Ihre Anwendung optimiert ist. Sie können bei Bedarf Failovers selbst testen und ausführen. Weitere Informationen finden Sie unter <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern</a>.</ul></td>

</tr>
<tr>
   <td valign="middle"><p><b>Hybridcloud</b></p></td>
   <td valign="middle"><ul><li type=round>Die lokale Anwendung kann auf Daten in der Azure SQL-Datenbank zugreifen.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Mit SQL Server in Azure VMs können Sie Anwendungen betreiben, die teilweise in der Cloud und teilweise lokal ausgeführt werden. Beispielsweise können Sie das lokale Netzwerk und die Active Directory-Domäne über <a href="https://azure.microsoft.com/documentation/articles/virtual-networks-overview/">Azure Network Services</a> auf die Cloud erweitern. Darüber hinaus können Sie lokale Datendateien in Azure Storage mithilfe des Features <a href="http://msdn.microsoft.com/library/dn385720.aspx">SQL&#160;Server-Datendateien in Azure</a> speichern. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/dn606154.aspx">Einführung in SQL Server 2014 Hybrid Cloud</a>.
      <li type=round>Unterstützt die Notfallwiederherstellung für lokale SQL&#160;Server-Anwendungen mithilfe der <a href="http://msdn.microsoft.com/library/jj919148.aspx">Sicherung in Azure Storage</a> oder <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">AlwaysOn-Replikate auf virtuellen Azure-Computern</a>.
      </ul></td>

</tr>
</table>

##<a name="business"></a>Entscheidungsfaktoren bei der Wahl zwischen Azure SQL-Datenbank und SQL Server in Azure VM

###<a name="cost"></a>Kosten

Ob Sie ein Startup-Unternehmen mit wenig Bargeld oder ein Team in einem etablierten Unternehmen sind, das unter engen Budgetbeschränkungen ausgeführt wird, sind begrenzte Mittel häufig die primäre Motivation bei der Entscheidung, wie Sie Ihre Datenbanken hosten. In diesem Abschnitt erfahren Sie zunächst etwas über die Grundlagen zur Abrechnung und Lizenzierung in Azure im Hinblick auf diese beiden Optionen für die relationale Datenbank: Azure SQL-Datenbank und SQL Server in Azure VM. Dann sehen wir, wie wir die Kosten für die gesamte Anwendung berechnen sollten.

####<a name="billing"></a>Abrechnung und Lizenzierung

**Azure SQL-Datenbank** wird nicht als Dienst mit einer Lizenz an Kunden verkauft, während SQL Server auf Azure VM die herkömmliche SQL Server-Lizenzierung erfordert.

Derzeit ist **Azure SQL-Datenbank** auf verschiedenen Dienstebenen verfügbar. Auf den Dienstebenen Basic, Standard und Premium wird stündlich mit einer festen Rate basierend auf Dienstebene und ausgewählter Leistungsstufe abgerechnet. Die Dienstebenen Basic, Standard und Premium liefern vorhersagbare Leistung mit mehreren Leistungsstufen entsprechend den Spitzenanforderungen der Anwendung. Sie können zwischen Dienstebenen und Leistungsstufen entsprechend den unterschiedlichen Durchsatzanforderungen Ihrer Anwendung wechseln. Die neuesten Informationen zu den derzeit unterstützten Dienstebenen finden Sie unter [Dienstebenen der Azure SQL-Datenbank (Editionen)](sql-database-service-tiers.md).

Mit **Azure SQL-Datenbank** wird die Datenbanksoftware automatisch von Microsoft Azure in Rechenzentren auf der ganzen Welt konfiguriert, gepatcht und aktualisiert. Aus diesem Grund haben Sie geringere Verwaltungskosten. Darüber hinaus helfen Ihnen die [integrierten Datensicherungsfunktionen](http://msdn.microsoft.com/library/azure/jj650016.aspx) dabei, erhebliche Kosteneinsparungen zu erzielen, vor allem bei einer großen Anzahl von Datenbanken. Bei Verwendung der Azure SQL-Datenbank werden einzelne Abfragen für die Azure SQL-Datenbank oder eingehender Internetverkehr nicht berechnet. Nur der [ausgehende Internetverkehr](http://azure.microsoft.com/pricing/details/data-transfers/) wird berechnet. Wenn Ihre Datenbank ein hohes Transaktionsvolumen aufweist und viele gleichzeitige Benutzer unterstützen muss, empfehlen wir, die Dienstebene "Premium" anstatt "Basic" oder "Standard" zu verwenden.

Bei **SQL Server in Azure VM** können Sie die herkömmliche SQL Server-Lizenzierung nutzen. Sie können entweder das auf der SQL Server-Plattform bereitgestellte Image verwenden oder Ihre eigene SQL Server-Lizenz in Azure einbringen. Bei der Verwendung der von der SQL Server-Plattform bereitgestellten Images hängen die Kosten von der VM-Größe und der von Ihnen auswählten Version von SQL Server ab. Im Grunde zahlen Sie Lizenzierungskosten pro Minute für SQL Server, die Pro-Minuten-Lizenzierung für Windows Server und die Kosten des Azure-Speichers. Mit der Abrechnungsoption pro Minute können Sie SQL Server verwenden, solange Sie ihn benötigen, ohne die vollständige SQL Server-Lizenz zu erwerben. Wenn Sie eine eigene SQL Server-Lizenz in Azure einbringen, werden Ihnen nur Azure Rechen- und Speicherkosten in Rechnung gestellt. Weitere Informationen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](http://azure.microsoft.com/pricing/license-mobility/).

####<a name="appcost"></a>Berechnen der Gesamtkosten für die Anwendung

Wenn Sie erstmals eine Cloud-Plattform verwenden, enthalten die Kosten der Anwendungsausführung in erster Linie die Kosten für Entwicklung und Verwaltung sowie die Service-Kosten, die die öffentliche Cloud-Plattform erfordert.

Dies ist die ausführliche Kostenberechnung für die Ausführung Ihrer Anwendung in der Azure SQL-Datenbank und SQL Server auf Azure VM:

**Bei Verwendung der Azure SQL-Datenbank:**

*Gesamtkosten der Anwendung = stark minimierte Verwaltungskosten + Softwareentwicklungskosten + Servicekosten für Azure SQL-Datenbank*

**Bei Verwendung von SQL Server in Azure VMs:**

*Gesamtkosten der Anwendung = minimierte Kosten für Softwareentwicklung/-änderungen + Verwaltungskosten + SQL Server- und Windows Server-Lizenzkosten + Kosten für Azure-Speicher*

> [AZURE.IMPORTANT]Azure SQL-Datenbank unterstützt zurzeit nicht alle Funktionen von SQL Server. Ausführliche Vergleichsinformationen finden Sie unter [Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](http://msdn.microsoft.com/library/azure/ff394102.aspx). Berücksichtigen Sie dies, wenn Sie eine vorhandene Datenbank in ein Azure SQL-Datenbank verschieben möchten, da Sie möglicherweise ein zusätzliches Budget zur Umgestaltung der Datenbank benötigen. Azure SQL-Datenbank ist ein Plattform-as-a-Service-Angebot von Microsoft. Bei der Migration einer vorhandenen lokalen SQL Server-Anwendung in die Azure SQL-Datenbank wird empfohlen, dass Sie die Anwendung aktualisieren, um alle Vorteile des Platform-as-a-Service-Angebot nutzen zu können. Verwenden Sie beispielsweise [Azure Websites](http://azure.microsoft.com/documentation/services/websites/) oder [Azure Cloud Services](http://azure.microsoft.com/services/cloud-services/) auf der Anwendungsebene, um die Kostenvorteile zu vergrößern. Darüber hinaus sollten Sie Ihre Anwendung mit verschiedenen Dienstebenen der Azure SQL-Datenbank überprüfen und testen, welche für die Anforderungen Ihrer Anwendung am besten geeignet ist. Dieser Prozess hilft Ihnen, bessere Leistungsergebnisse und minimierte Kosten zu erreichen. Weitere Informationen finden Sie unter [Dienst- und Leistungsebenen für Azure SQL-Datenbanken](sql-database-service-tiers.md).

Verwenden Sie für eine ausführliche Kostenschätzung den [Azure-Preisrechner](http://azure.microsoft.com/pricing/calculator/).

Weitere Informationen zur Preisberechnung finden Sie in den folgenden Ressourcen:

- [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/)
- [Preisinformationen zu Virtual Machines](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [Preisinformationen zu SQL Server in Azure VMs](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Windows Server auf Azure VMs - Preisdetails](http://azure.microsoft.com/pricing/details/virtual-machines/#windows)

###<a name="admin"></a>Verwaltung

Wenn Sie bereits viele Aufgaben zu erledigen haben, möchten Sie sich wahrscheinlich nicht auch noch um die Server- und Datenbankverwaltung kümmern müssen. Für viele Unternehmen hängt die Entscheidung für einen Clouddienst mit der Möglichkeit zusammen, die Komplexität der Verwaltung zu verlagern. Mit **Azure SQL-Datenbank** verwaltet Microsoft die physische Hardware wie Festplatten, Server und Speicher, repliziert automatisch alle Daten zur Gewährleistung hoher Verfügbarkeit, konfiguriert und aktualisiert die Datenbanksoftware, verwaltet den Lastenausgleich und führt bei einem Serverausfall ein transparentes Failover durch. Sie können Ihre Azure SQL-Datenbankinstanzen weiterhin verwalten, jedoch ohne die physischen Ressourcen der zugrunde liegenden SQL Server-Instanz und der Azure-Plattform zu steuern. Sie können beispielsweise Datenbanken und Anmeldungen verwalten, den Index und Abfragen optimieren, aber das Systemtabellen- und Dateigruppen-Management liegt nicht in Ihrer Hand. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](http://msdn.microsoft.com/library/ff394102.aspx).

Auf der anderen Seite benötigen Sie möglicherweise Fachwissen vor Ort und möchten den Speicherort der Datenbank bis hin zum Computer selbst kontrollieren. Mit **SQL Server in Azure VM** haben Sie vollständige Kontrolle über das Betriebssystem und die Konfiguration der SQL Server-Instanz. Mit einem virtuellen Computer können Sie entscheiden, wann das Betriebssystem und die Datenbanksoftware aktualisiert werden und wann zusätzliche Software wie z. B. Anti-Virus und Sicherungstools installiert werden sollen. Darüber hinaus können Sie die Größe des virtuellen Computers, die Anzahl der Laufwerke und deren Speicherkonfigurationen steuern. Azure ermöglicht beispielsweise die Größenänderung eines ausgeführten virtuellen Computers nach Bedarf. Weitere Informationen finden Sie unter [Größen von virtuellen Computern und Clouddiensten für Azure](../virtual-machines/virtual-machines-size-specs.md).

###<a name="sla"></a>Vereinbarung zum Servicelevel (SLA)

Für einige von uns hat die Einhaltung der Betriebszeitverpflichtungen einer Vereinbarung zum Servicelevel (SLA) die höchste Priorität. In diesem Abschnitt schauen wir uns an, was SLA für die einzelnen Datenbankhostingoptionen bedeutet.

Für **Azure SQL-Datenbank** bietet Microsoft für die Dienstebenen Basic, Standard und Premium eine Verfügbarkeits-SLA von 99,99 %. Beachten Sie, dass es bei der Verfügbarkeits-SLA darum geht, eine Verbindung mit der Datenbank herstellen zu können. Mit anderen Worten, dies ist eine SLA auf Datenbankebene. Aktuelle Informationen zu SLAs finden Sie unter [Vereinbarung zum Servicelevel](http://azure.microsoft.com/support/legal/sla/). Aktuelle Informationen zu den Dienstebenen (Editionen) der Azure SQL-Datenbank und zu den unterstützten Geschäftskontinuitätsplänen finden Sie unter [Dienstebenen für Azure SQL-Datenbanken](sql-database-service-tiers.md).

Für **in Azure gehostete virtuelle Computer** bietet Microsoft eine Verfügbarkeits-SLA von 99,95 %. Diese Verfügbarkeit gilt für den virtuellen Computer, nicht für die Prozesse, die innerhalb des virtuellen Computers ausgeführt werden (z. B. SQL Server). Die [VM-SLA](http://www.microsoft.com/download/details.aspx?id=38427) erfordert, dass Sie mindestens zwei virtuelle Computer in einer Verfügbarkeitsgruppe hosten. Mit dieser Konfiguration garantiert Azure, dass mindestens eine der VMs 99,95 % der Zeit zur Verfügung steht. Für hohe Datenbankverfügbarkeit (HA) in VMs sollten Sie eine der unterstützten Hochverfügbarkeitsoptionen in SQL Server konfigurieren, wie z. B. AlwaysOn-Verfügbarkeitsgruppen. Beachten Sie, dass das Einrichten von AlwaysOn in Azure einige manuelle Konfiguration und Verwaltung erfordert und Sie für jede sekundäre Datenbank, die Sie betreiben, einen Aufpreis bezahlen müssen.


###<a name="market"></a>Zeit bis zur Marktreife

**Azure SQL-Datenbank** ist die richtige Lösung für Anwendungen für die Cloud, wenn Entwicklerproduktivität und eine schnelle Markteinführung wichtige Kriterien sind. Durch programmgesteuerte DBA-ähnliche Funktionen ist sie perfekt für Cloud-Architekten und Entwickler, da sie den Verwaltungsaufwand für das zugrunde liegende Betriebssystem und die Datenbank verringert. Sie hilft Entwicklern, Aufgaben im Zusammenhang mit der Datenbank zu verstehen und zu konfigurieren. Sie können z. B. die [REST-API](http://msdn.microsoft.com/library/azure/dn505719.aspx) und [PowerShell-Cmdlets](http://msdn.microsoft.com/library/azure/dn546726.aspx) verwenden, um administrative Vorgänge für Tausende von Datenbanken zu automatisieren und zu verwalten. Bei der [elastischen Skalierung](sql-database-elastic-pool.md) in der Cloud können Sie sich einfach auf die Anwendungsschicht konzentrieren und Ihre Anwendung schneller auf den Markt bringen.

**SQL Server in Azure VM** ist die ideale Lösung, wenn Ihre vorhandenen und neuen Anwendungen Zugriff auf und Kontrolle über alle Features einer SQL Server-Instanz erfordern und wenn Sie vorhandene lokale Anwendungen und Datenbanken im Istzustand in die Cloud migrieren möchten. Da Sie nicht die Präsentation, Anwendung und Datenebenen ändern müssen, sparen Sie Zeit und Geld für den Neuentwurf der vorhandenen Lösung. In diesem Fall können Sie alle Lösungspakete auf die virtuellen Computer migrieren und einige Leistungsoptimierungen durchführen, die von der Azure-Plattform benötigt werden. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md).

##<a name="summary"></a>Zusammenfassung

In diesem Artikel wurden Azure SQL-Datenbank und SQL Server in Azure VM erläutert. Darüber hinaus werden allgemeine geschäftliche Gründe erörtert, die die Entscheidungsfindung bei der Auswahl beeinflussen können.

Nachfolgend finden eine Zusammenfassung der Vorschläge, wann Sie welche Version verwenden:

Wählen Sie **Azure SQL-Datenbank**, wenn:

- Sie neue Cloud-basierte Anwendungen erstellen oder Ihre vorhandene SQL Server-Datenbank zu Azure migrieren möchten und Ihre Datenbank keine der nicht unterstützten Funktionen der Azure SQL-Datenbank verwendet. Weitere Informationen finden Sie unter [Transact-SQL-Referenz für die Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336281.aspx). Dieser Ansatz bietet die Vorteile eines vollständig verwalteten Clouddiensts und sorgt für eine schnelle Markteinführung.

- Sie möchten, dass Microsoft allgemeine Verwaltungsvorgänge für Ihre Datenbanken ausführt, und Sie benötigen striktere Verfügbarkeits-SLAs für Datenbanken. Mit diesem Ansatz lassen sich die Verwaltungskosten minimieren, und zugleich bietet er eine garantierte Verfügbarkeit für die Datenbank.

    [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md)


Wählen Sie **SQL Server in Azure VM**, wenn:

- Sie über vorhandene lokale Anwendungen verfügen und Ihre eigene Hardware nicht mehr selbst verwalten möchten oder Hybridlösungen erwägen. Mit diesem Ansatz können Sie schneller auf eine hohe Datenbankkapazität zugreifen und Ihre lokalen Anwendungen über einen sicheren Tunnel mit der Cloud verbinden.

- Sie über vorhandene IT-Ressourcen verfügen, volle Administratorrechte für SQL Server brauchen und vollständige Kompatibilität mit dem lokalen SQL Server benötigen (z. B. gibt es einige Features nicht in der Azure SQL-Datenbank). Mit dieser Methode können Sie die Kosten für Entwicklung oder Änderungen an vorhandenen Anwendungen minimieren und behalten die Flexibilität zur Ausführung der meisten Anwendungen. Darüber hinaus bietet sie Vollzugriff auf den virtuellen Computer, das Betriebssystem und die Datenbankkonfiguration.

    [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md)

> [AZURE.NOTE]Möchten Sie SQL Server 2016 CTP2 testen? Registrieren Sie sich für Microsoft Azure, und fahren Sie anschließend [hier](http://aka.ms/sql2016vm "hier") fort, um einen virtuellen Computer mit einer Installation von SQL Server 2016 CTP2 zu erstellen.


##<a name="ack"></a>Danksagungen

Dieser Artikel aus der Microsoft Cloud- und Enterprise Content Services-Gruppe wurde mithilfe von vielen Personen innerhalb der Microsoft-Community erstellt.

**Autor**: Selcin Turkarslan

**Technische Mitwirkende**: Conor Cunningham

**Technische Bearbeitung**: Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**Redaktionelle Bearbeitung**: Heidi Steen, Maggie Sparkman.

Vielen Dank für die Umsetzung dieses Artikels!

##<a name="resources"></a>Zusätzliche Ressourcen

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Beschreibung</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN: Azure SQL-Datenbank</a></p>
<p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/">Übersicht über SQL&#160;Server auf virtuellen Azure-Computern</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com: SQL-Datenbank</a></p></td>
   <td valign="middle">Links zur Bibliotheksdokumentation.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-application-patterns-and-development-strategies/">Anwendungsmuster und Entwicklungsstrategien für SQL Server auf virtuellen Azure-Computern</p></td>
   <td valign="middle">Dieser Artikel beschreibt die häufigsten Anwendungsmuster, die für SQL Server auf Azure VMs und Hybridszenarien einschließlich Azure SQL-Datenbank gelten. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Microsoft Enterprise Library: The Transient Fault Handling Application Block (Handhabung vorübergehender Fehler – Anwendungsblock, in englischer Sprache)</p></td>
   <td valign="middle">Mit dieser Bibliothek können Entwickler ihre Anwendungen, die auf der Azure SQL-Datenbank ausgeführt werden, mit einer höheren Ausfallsicherheit ausstatten, indem sie eine robuste Logik für die Handhabung vorübergehender Fehler hinzufügen. Vorübergehende Fehler sind Fehler, die aufgrund einer temporären Bedingung wie z. B. Probleme mit der Netzwerkkonnektivität oder einem nicht verfügbaren Dienst auftreten. Da es sich bei der Azure SQL-Datenbank um einen mehrinstanzenfähigen Dienst handelt, ist es wichtig, solche Fehler zu beheben, um Anwendungsausfallzeiten zu minimieren. </td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png

<!---HONumber=Nov15_HO4-->