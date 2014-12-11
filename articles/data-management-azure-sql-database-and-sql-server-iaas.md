<properties urlDisplayName="Learn Azure SQL Database and SQL Server in Azure VMs" pageTitle="Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure VMs" metaKeywords="SQL, Azure SQL Database, SQL Server IAAS, SQL Server in Azure VM" description="Learn Azure SQL Database and SQL Server in Azure Virtual Machines. Review common business motivators for determining which SQL technology works best for your application." metaCanonical="" services="virtual-machines" documentationCenter="" title="Understanding Azure SQL Database and SQL Server in Azure VMs" authors="selcint" solutions="data-management" manager="jeffreyg" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/26/2014" ms.author="selcint" />

# Grundlegendes zur Azure SQL-Datenbank und SQL Server in Azure VMs

Microsoft Azure bietet Ihnen zwei Möglichkeiten beim Hosten Ihrer SQL Server-basierten Daten: **Azure-SQL-Datenbank** und **SQL Server in Azure Virtual Machines**. In diesem Artikel untersuchen wir zunächst, wie jede Option in das Gesamtbild der Microsoft Datenplattform passt, und fahren mit umfassenderen Diskussionen auf Grundlage der Geschäftsanforderungen, die Ihre Auswahl motivieren, fort. Ob Sie Kosteneinsparungen oder minimalen Verwaltungsaufwand priorisieren, könnte dieser Artikel Ihnen bei der Entscheidung helfen, welcher Ansatz richtig ist, basierend auf den jeweiligen geschäftlichen Anforderungen, die Sie am meisten interessieren.

- [Microsoft Datenplattform](#platform)
- [Genauere Betrachtung der Azure SQL-Datenbank und SQL Server in Azure VM](#close)	
- [Geschäftliche Motivationen bei der Auswahl der Azure SQL-Datenbank oder von SQL Server in Azure VM](#business)	
	- [Kosten](#cost)
		- [Abrechnungs- und Lizenzierungsgrundlagen](#billing)	
		- [Berechnen der gesamten Anwendungskosten] (Appcost)	
	- [Verwaltung](#admin)	
	- [Vereinbarung zum Servicelevel (SLA)](#sla)	
	- [Zeit bis zur Marktreife](#market)	
- [Zusammenfassung](#summary)	
- [Bestätigungen](#ack)	
- [Zusätzliche Ressourcen](#resources)	


##<a name="platform"></a>Microsoft Datenplattform

Eines der ersten Dinge, die in jeder Diskussion von Azure im Vergleich zu lokalen SQL Server-Datenbanken verstanden werden müssen, ist, dass Sie alles verwenden können. Die Microsoft Datenplattform nutzt SQL Server-Technologie und stellt sie auf unterschiedlichen lokalen physischen Computern, in privaten Cloud-Umgebungen, von Drittanbietern gehosteten privaten Cloud-Umgebungen und öffentlichen Clouds zur Verfügung. Dadurch können Sie über eine Kombination aus lokalen und Cloud-gehosteten Bereitstellungen dieselben Serverprodukte, Entwicklungstools und Fachkenntnisse in diesen Umgebungen mit einzigartigen und unterschiedlichen Geschäftsanforderungen erfüllen.

   ![][1]

Wie Sie im Diagramm sehen können, kann jedes dieser Angebote durch die Administrationsebene in Bezug auf die Infrastruktur (auf der x-Achse) und den Grad der erreichten Kosteneffizienz durch Datenbankkonsolidierung und Automatisierung (auf der y-Achse) beschrieben werden.

Beim Entwurf einer Anwendung stehen vier grundlegende Optionen für das Hosten des SQL Server-Teils der Anwendung zur Verfügung: 

- SQL Server auf nicht virtualisierten physischen Computern 
- SQL Server auf lokalen virtualisierten Computern (private Cloud)
- SQL Server in Azure Virtual Machine (öffentliche Cloud)
- Azure SQL-Datenbank (öffentliche Cloud)

In den folgenden Abschnitten erfahren wir mehr über die beiden Letzteren: Azure SQL-Datenbank und SQL Server in Azure VMs. Darüber hinaus werden allgemeine geschäftliche Gründe behandelt, um zu bestimmen, welche Option am besten für Ihre Anwendung geeignet ist.

##<a name="close"></a>Genauere Betrachtung der Azure SQL-Datenbank und SQL Server in Azure VM

**Microsoft Azure SQL-Datenbank (Azure SQL-Datenbank)** ist eine relationale Datenbank-as-a-Service, die in die Branchenkategorie *Plattform-as-a-Service (PaaS)* fällt. Azure SQL-Datenbank basiert auf standardisierter Hardware und Software, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Mit SQL-Datenbank können Sie direkt im Dienst mithilfe der integrierten Features und Funktionen entwickeln. Mit der SQL-Datenbank können Sie die Optionen für vertikale oder horizontale Skalierung nutzungsbasiert für höhere Leistung verwenden.

**SQL Server in Azure Virtual Machine (VM)** fällt in die Branchenkategorie *Infrastructure-as-a-Service (IaaS)* und ermöglicht es Ihnen, SQL Server auf einem virtuellen Computer in der Cloud auszuführen. Ähnlich wie Azure SQL-Datenbank basiert es auf standardisierter Hardware und Software, die Microsoft gehört und von Microsoft gehostet und verwaltet wird. Wenn Sie SQL Server auf einem virtuellen Computer verwenden, können Sie entweder Ihre eigene SQL Server-Lizenz in Azure einbringen oder Sie verwenden eine der vorkonfigurierten SQL Server-Images im Azure-Portal.

Im Allgemeinen sind diese beiden SQL-Optionen für unterschiedliche Zwecke optimiert:

- **Azure SQL-Datenbank** ist optimiert, um die Gesamtbetriebskosten für die Bereitstellung und Verwaltung von vielen Datenbanken auf ein Minimum zu reduzieren. Die laufenden Administrationskosten werden minimiert, da keine virtuellen Computer, Betriebssysteme oder Datenbanksoftware verwaltet werden muss, einschließlich Upgrades, hohe Verfügbarkeit und Sicherungen. Die SQL-Datenbank kann im Allgemeinen die Anzahl der Datenbanken deutlich erhöhen, die von einer einzelnen IT- oder Entwicklungsressource verwaltet wird.
- **SQL Server auf Azure VM** ist zum Erweitern der vorhandener lokalen SQL Server-Anwendungen in Azure in einem Hybridszenario oder zum Bereitstellen einer vorhandenen Anwendung in Azure in einem Migrationsszenario oder Test-/Entwicklungsszenario optimiert. Ein Beispiel für ein Hybridszenario ist das Aufbewahren von sekundären Datenbankreplikaten in Azure über [Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx). Mit SQL Server auf Azure VMs verfügen Sie über eine dedizierte Instanz von SQL Server und eine Cloud-basierte VM mit vollständigen Administratorrechten. Dies ist die perfekte Lösung, wenn ein Unternehmen bereits IT-Ressourcen verfügbar hat, um die virtuellen Computer zu verwalten. Mit SQL Server auf virtuellen Computern können Sie ein maßgeschneidertes System zur Einhaltung anwendungsspezifischer Performance- und Verfügbarkeitsanforderungen erstellen.

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
         <li type=round>Datenbanken mit bis zu 500 GB Größe.
         <li type=round>Erstellen von Software-as-a-Service-Anwendungen.
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Vorhandene Anwendungen, die schnelle Migration zur Cloud mit minimalen Änderungen erfordern.
      <li type=round>SQL Server-Anwendungen, die Zugriff auf lokale Ressourcen (z. B. Active Directory) von Azure über einen sicheren Tunnel erfordern. 
      <li type=round>Wenn Sie eine angepasste IT-Umgebung mit vollen Administratorrechten benötigen.
      <li type=round>Schnelle Entwicklung- und Testszenarios, wenn Sie nicht lokale SQL Server-Hardware außerhalb der Produktion kaufen möchten.
      <li type=round>Notfallwiederherstellung für lokale SQL Server-Anwendungen mithilfe der <a href="http://msdn.microsoft.com/library/jj919148.aspx">Sicherung im Azure-Speicher</a> oder <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">AlwaysOn-Replikate auf Azure VMs</a>.
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
   <td valign="middle"><p><b>Gesamtkosten</b></p></td>
   <td valign="middle"><ul><li type=round>Eliminiert Hardwarekosten. Verringert Verwaltungskosten.</ul></td>
   <td valign="middle"><ul><li type=round>Eliminiert Hardwarekosten. </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Geschäftskontinuität</b></p></td>
   <td valign="middle"><ul><li type=round> Zusätzlich zu den integrierten Infrastruktur-Fehlertoleranzfunktionen stellt Azure SQL-Datenbank Funktionen bereit, wie z. B. Zeitpunktwiederherstellung, Geowiederherstellung und geografische Replikation, um die Geschäftskontinuität zu erhöhen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Azure SQL-Datenbank - Geschäftskontinuität</a>.</ul></td>
   <td valign="middle"><ul><li type=round>Mit SQL Server auf Azure VM können Sie eine hohe Verfügbarkeit und Notfallwiederherstellungslösung für Ihre datenbankspezifischen Anforderungen einrichten. Aus diesem Grund haben Sie ein System, das genau für Ihre Anwendung optimiert ist. Sie können bei Bedarf Failovers selbst testen und ausführen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">Hochverfügbarkeit und Notfallwiederherstellung für SQL Server auf virtuellen Azure-Computern</a>.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Hybride Cloud</b></p></td>
   <td valign="middle"><ul><li type=round>Ihre lokale Anwendung kann auf Daten in der Azure SQL-Datenbank zugreifen.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Mit SQL Server auf Azure VMs können Sie Anwendungen haben, die teilweise in der Cloud und teilweise lokal ausgeführt werden. Beispielsweise können Sie das lokale Netzwerk und die Active Directory-Domäne über <a href="http://msdn.microsoft.com/library/azure/gg433091.aspx">Azure-Netzwerkdienste</a> auf die Cloud erweitern. Darüber hinaus können Sie lokale Datendateien im Azure-Speicher mithilfe der <a href="http://msdn.microsoft.com/library/dn385720.aspx">SQL Server-Datendateien in Azure-Funktion</a> speichern. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/dn606154.aspx">Einführung in SQL Server 2014 Hybrid Cloud</a>.
      <li type=round>Unterstützt Notfallwiederherstellung für lokale SQL Server-Anwendungen   mithilfe der   <a href="http://msdn.microsoft.com/library/jj919148.aspx">Sicherung im Azure-Speicher</a> oder <a href="http://msdn.microsoft.com/library/azure/jj870962.aspx">AlwaysOn-Replikate auf Azure VMs</a>.
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>Geschäftliche Motivationen bei der Auswahl der Azure SQL-Datenbank oder von SQL Server in Azure VM

###<a name="cost"></a>Kosten

Ob Sie ein Startup-Unternehmen mit wenig Bargeld oder ein Team in einem etablierten Unternehmen sind, das unter engen Budgetbeschränkungen ausgeführt wird, sind begrenzte Mittel häufig die primäre Motivation bei der Entscheidung, wie Sie Ihre Datenbanken hosten. In diesem Abschnitt erfahren wir zuerst etwas über die Grundlagen zur Abrechnung und Lizenzierung in Azure im Hinblick auf diese beiden Optionen für die relationale Datenbank: Azure SQL-Datenbank und SQL Server auf Azure VM. Dann sehen wir, wie wir die Kosten für die gesamte Anwendung berechnen.

####<a name="billing"></a>Abrechnungs- und Lizenzierungsgrundlagen

**Azure SQL-Datenbank** wird nicht als Dienst mit einer Lizenz an Kunden verkauft, während SQL Server auf Azure VM herkömmliche SQL Server-Lizenzierung erfordert. 

Derzeit ist die **Azure SQL-Datenbank** auf verschiedenen Dienstebenen verfügbar. Auf den Dienstebenen Basic, Standard und Premium wird stündlich mit einer festen Rate basierend auf Dienstebene und ausgewählter Leistungsstufe abgerechnet. Die Dienstebenen Basic, Standard und Premium liefern vorhersagbare Leistung mit mehreren Leistungsstufen entsprechend den Spitzenanforderungen der Anwendung. Sie können zwischen Dienstebenen und Leistungsstufen entsprechend den unterschiedlichen Durchsatzanforderungen Ihrer Anwendung wechseln. Aktuelle Informationen zu den aktuell unterstützten Dienstebenen finden Sie unter [Dienstebenen der Azure SQL-Datenbank (Editionen)](http://msdn.microsoft.com/library/azure/dn741340.aspx).

Mit **Azure SQL-Datenbank** wird die Datenbanksoftware automatisch konfiguriert, gepatcht und von Microsoft Azure in Rechenzentren auf der ganzen Welt aktualisiert. Aus diesem Grund haben Sie geringere Verwaltungskosten. Darüber hinaus können die [integrierten Funktionen für die Sicherungen](http://msdn.microsoft.com/library/azure/jj650016.aspx) erhebliche Kosteneinsparungen erzielen, vor allem bei einer großen Anzahl von Datenbanken. Bei Verwendung der Azure SQL-Datenbank werden nicht einzelne Abfragen für die Azure SQL-Datenbank oder eingehender/ausgehender Internetverkehr berechnet. Wenn die Datenbank ein hohes Transaktionsvolumen hat und viele Benutzer gleichzeitig unterstützt werden müssen, empfehlen wir, die Premium- statt Basic- oder Standard-Dienstebenen zu verwenden. 

Mit **SQL Server auf Azure VM** nutzen Sie die herkömmliche SQL Server-Lizenzierung. Sie können entweder das auf der SQL Server-Plattform bereitgestellte Image verwenden oder Ihre eigene SQL Server-Lizenz in Azure einbringen. Bei der Verwendung der von der SQL Server-Plattform bereitgestellten Images hängen die Kosten von der VM-Größe und der von Ihnen auswählten Version von SQL Server ab. Im Grunde zahlen Sie Lizenzierungskosten pro Minute für SQL Server, die Pro-Minuten-Lizenzierung für Windows Server und die Kosten des Azure-Speichers. Mit der Abrechnungsoption pro Minute können Sie SQL Server verwenden, solange Sie ihn benötigen, ohne die vollständige SQL Server-Lizenz zu erwerben. Wenn Sie eine eigene SQL Server-Lizenz in Azure einbringen, werden Ihnen nur Azure Rechen- und Speicherkosten in Rechnung gestellt. Weitere Informationen finden Sie unter [Lizenzmobilität durch Software Assurance für Azure](http://azure.microsoft.com/pricing/license-mobility/).

####<a name="cost"></a>Berechnen der gesamten Anwendungskosten

Wenn Sie erstmals eine Cloud-Plattform verwenden, enthalten die Kosten der Anwendungsausführung in erster Linie die Kosten für Entwicklung und Verwaltung sowie die Service-Kosten, die die öffentliche Cloud-Plattform erfordert.

Dies ist die ausführliche Kostenberechnung für die Ausführung Ihrer Anwendung in der Azure SQL-Datenbank und SQL Server auf Azure VM:

**Wenn Sie die Azure SQL-Datenbank verwenden:**

*Gesamtkosten der Anwendung = Stark minimierte Verwaltungskosten + die Kosten für die Entwicklung von Software + Kosten für Azure SQL-Datenbankdienst*

**Bei Verwendung von SQL Server auf Azure VM:**

*Gesamtkosten der Anwendung = Minimierte Kosten für Software-Entwicklung/Änderungen + Verwaltungskosten +  SQL Server- und Windows Server-Lizenzierungskosten + Kosten des Azure-Speichers* 

**Wichtiger Hinweis:** Azure SQL-Datenbank unterstützt zurzeit nicht alle Funktionen von SQL Server. Ausführliche Vergleichsinformationen finden Sie unter [Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](http://msdn.microsoft.com/library/azure/ff394102.aspx). Berücksichtigen Sie dies, wenn Sie eine vorhandene Datenbank in ein Azure SQL-Datenbank verschieben möchten, da Sie möglicherweise ein zusätzliches Budget zur Umgestaltung der Datenbank benötigen. Azure SQL-Datenbank ist ein Plattform-as-a-Service-Angebot von Microsoft. Bei der Migration einer vorhandenen lokalen SQL Server-Anwendung in die Azure SQL-Datenbank wird empfohlen, dass Sie die Anwendung aktualisieren, um alle Vorteile des Platform-as-a-Service-Angebot nutzen zu können. Verwenden Sie beispielsweise [Azure-Websites](http://azure.microsoft.com/documentation/services/websites/) oder [Azure Cloud Services](http://azure.microsoft.com/services/cloud-services/) auf der Anwendungsebene, um die Kostenvorteile zu erhöhen. Darüber hinaus sollten Sie Ihre Anwendung mit verschiedenen Dienstebenen der Azure SQL-Datenbank überprüfen und testen, welche für die Anforderungen Ihrer Anwendung am besten geeignet ist. Dieser Prozess hilft Ihnen, bessere Leistungsergebnisse und minimierte Kosten zu erreichen. Weitere Informationen finden Sie unter [Basic-, Standard- und Premium-Vorschau für die Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/dn369873.aspx).

Verwenden Sie den [Azure-Preisrechner](http://azure.microsoft.com/pricing/calculator/) für eine ausführliche Kostenschätzung. 

Weitere Informationen zur Preisberechnung finden Sie in den folgenden Ressourcen:

- [Preisdetails für Azure SQL-Datenbanken](http://azure.microsoft.com/pricing/details/sql-database/) 
- [Preisdetails für virtuelle Computer](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [SQL Server auf Azure VMs - Preisdetails](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Windows Server auf Azure VMs - Preisdetails](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>Verwaltung

Wenn Sie bereits viele Aufgaben zu erledigen haben, ist die Server- und Datenbankverwaltung möglicherweise nichts, worauf Sie sich freuen. Für viele Unternehmen hängt die Entscheidung für einen Cloud-Dienst mit der Möglichkeit zusammen, die Komplexität der Verwaltung zu verlagern. Mit **Azure SQL-Datenbank** verwaltet Microsoft die physische Hardware wie Festplatten, Server und Speicher; repliziert alle Daten zur Gewährleistung hoher Verfügbarkeit automatisch; konfiguriert und aktualisiert die Datenbanksoftware; verwaltet den Lastenausgleich und bietet transparentes Failover, wenn ein Server ausfällt. Sie können weiterhin Ihre Azure SQL-Datenbankinstanzen verwalten, aber ohne die physischen Ressourcen der zugrunde liegenden SQL Server-Instanz und der Azure-Plattform kontrollieren zu müssen. Sie können beispielsweise Datenbanken und Anmeldungen verwalten, den Index und Abfragen optimieren, aber das Systemtabellen- und Dateigruppen-Management liegt nicht in Ihrer Hand. Weitere Informationen finden Sie unter [Richtlinien für und Einschränkungen von Azure SQL-Datenbanken](http://msdn.microsoft.com/library/ff394102.aspx). 

Auf der anderen Seite benötigen Sie möglicherweise Fachwissen vor Ort und möchten den Speicherort der Datenbank bis hin zum Computer selbst kontrollieren. Mit **SQL Server auf Azure VM** haben Sie die vollständige Kontrolle über das Betriebssystem und die Konfiguration der SQL Server-Instanz. Mit einem virtuellen Computer können Sie entscheiden, wann das Betriebssystem und die Datenbanksoftware aktualisiert werden und wann zusätzliche Software wie z. B. Anti-Virus und Sicherungstools installiert werden sollen. Darüber hinaus können Sie die VM-Größe, die Anzahl der Laufwerke und ihre Speicherkonfigurationen steuern. Azure ermöglicht beispielsweise die Größenänderung eines ausgeführten virtuellen Computers nach Bedarf. Weitere Informationen finden Sie unter [Größen virtueller Computer und Cloud-Dienste für Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx).

###<a name="sla"></a>Vereinbarung zum Servicelevel (SLA)

Für einige von uns hat die Einhaltung Betriebszeitverpflichtungen einer Vereinbarung zum Servicelevel (SLA) die höchste Priorität. In diesem Abschnitt untersuchen wir, was SLA für jede Datenbank-Hostingoption bedeutet.

Für **Azure SQL-Datenbank** auf den Dienstebenen Basic, Standard und Premium bietet Microsoft eine Verfügbarkeits-SLA von 99,99 %. Beachten Sie, dass die Verfügbarkeits-SLA die Möglichkeit betrifft, eine Verbindung mit der Datenbank herzustellen. Mit anderen Worten, dies ist eine SLA auf Datenbankebene. Aktuelle Informationen zu SLAs finden Sie in der [Vereinbarung zum Servicelevel](http://azure.microsoft.com/support/legal/sla/). Aktuelle Informationen zu Azure SQL-Datenbank-Dienstebenen (Editionen) und den unterstützten Business Continuity-Plänen finden Sie unter [Dienstebenen der Azure SQL-Datenbank](http://msdn.microsoft.com/library/dn741340.aspx).

Für **in Azure gehostete virtuelle Computer** bietet Microsoft eine Verfügbarkeits-SLA von 99,95 %, und diese Verfügbarkeit gilt für den virtuellen Computer, nicht für Prozesse, die auf dem virtuellen Computer ausgeführt werden (z. B. SQL Server). Die [VM-SLA](http://www.microsoft.com/download/details.aspx?id=38427) erfordert, dass Sie mindestens zwei virtuelle Computer in einer Verfügbarkeitsgruppe hosten. Mit dieser Konfiguration garantiert Azure, dass mindestens eine der VMs 99,95 % der Zeit zur Verfügung steht. Für hohe Datenbankverfügbarkeit (HA) in VMs sollten Sie eine der unterstützten Hochverfügbarkeitsoptionen in SQL Server konfigurieren, wie z. B. AlwaysOn-Verfügbarkeitsgruppen. Beachten Sie, dass das Einrichten von AlwaysOn in Azure einige manuelle Konfiguration und Verwaltung erfordert, und Sie zahlen extra für jede sekundäre Instanz, die Sie betreiben.


###<a name="market"></a>Zeit bis zur Marktreife

**Azure SQL-Datenbank** ist die richtige Lösung für cloudbasierte Anwendungen, bei denen Entwicklerproduktivität und schnelle Markteinführung wichtig sind. Durch programmgesteuerte DBA-ähnliche Funktionen ist sie perfekt für Cloud-Architekten und Entwickler, da sie den Verwaltungsaufwand für das zugrunde liegende Betriebssystem und die Datenbank verringert. Sie hilft Entwicklern, Aufgaben im Zusammenhang mit der Datenbank zu verstehen und zu konfigurieren. Beispielsweise können Sie die [REST-API](http://msdn.microsoft.com/library/gg715283.aspx) und [PowerShell-Cmdlets](http://msdn.microsoft.com/library/azure/dn546726.aspx) zum Automatisieren und Verwalten von Verwaltungsvorgängen für Tausende von Datenbanken nutzen. Bei der elastischen Skalierung in der Cloud können Sie sich problemlos auf die Anwendungsschicht konzentrieren und Ihre Anwendung schneller auf den Markt bringen. 

**SQL Server auf Azure VM** ist perfekt, wenn Ihre vorhandenen und neuen Anwendungen Zugriff und Kontrolle für alle Features einer SQL Server-Instanz erfordern, und wenn Sie vorhandene lokale Anwendungen und Datenbanken unverändert in die Cloud migrieren möchten. Da Sie nicht die Präsentation, Anwendung und Datenebenen ändern müssen, sparen Sie Zeit und Geld für den Neuentwurf der vorhandenen Lösung. In diesem Fall können Sie alle Lösungspakete auf die virtuellen Computer migrieren und einige Leistungsoptimierungen durchführen, die von der Azure-Plattform benötigt werden. Weitere Informationen finden Sie unter [Optimale Verfahren für die Leistung für SQL Server auf virtuellen Computern in Azure](http://msdn.microsoft.com/library/azure/dn133149.aspx).

##<a name="summary"></a>Zusammenfassung

In diesem Artikel wurde erläutert: Azure SQL-Datenbank und SQL Server auf Azure VM. Darüber hinaus haben wir allgemeine geschäftliche Gründe diskutiert, welche die Entscheidungsfindung bei der Auswahl beeinflussen können.  

Nachfolgend finden eine Zusammenfassung der Vorschläge, wann Sie welche Version verwenden:

Wählen Sie die **Azure SQL-Datenbank** in folgenden Fällen:

- Sie erstellen neue, cloudbasierte Anwendungen; oder Sie möchten eine vorhandene SQL Server-Datenbank in Azure migrieren, und die Datenbank verwendet keine der nicht unterstützten Funktionen in der Azure SQL-Datenbank. Weitere Informationen finden Sie unter [Nicht unterstützte Transact-SQL-Anweisungen](http://msdn.microsoft.com/library/azure/ee336253.aspx). Dieser Ansatz bietet die Vorteile einer vollständig verwalteten Cloud-Diensts und gewährleistet die schnelle Markteinführung.

- Sie möchten, dass Microsoft allgemeine Verwaltungsvorgänge für Ihre Datenbanken erledigt, und Sie benötigen striktere Verfügbarkeits-SLAs für Datenbanken. Dieser Ansatz kann die Verwaltungskosten minimieren und bietet gleichzeitig eine garantierte Verfügbarkeit für die Datenbank. 

Wählen Sie **SQL Server auf Azure VM** in folgenden Fällen:

- Sie haben vorhandene lokale Anwendungen und möchten nicht mehr Ihre eigene Hardware verwalten, oder Sie erwägen Hybridlösungen. Dadurch erhalten Sie schnelleren Zugriff auf hohe Datenbankkapazitäten und verbinden Ihre lokale Anwendungen über einen sicheren Tunnel mit der Cloud.

- Sie verfügen über vorhandene IT-Ressourcen, benötigen vollständige Administratorrechte über SQL Server und vollständige Kompatibilität mit dem lokalen SQL Server (z. B. einige Funktionen, die nicht in der Azure SQL-Datenbank existieren). Mit dieser Methode können Sie die Kosten für Entwicklung oder Änderungen an vorhandenen Anwendungen minimieren und behalten die Flexibilität zur Ausführung der meisten Anwendungen. Darüber hinaus bietet sie vollständige Kontrolle über den virtuellen Computer, das Betriebssystem und die Datenbankkonfiguration.

##<a name="ack"></a>Bestätigungen

Dieser Artikel aus der Microsoft Cloud- und Enterprise Content Services-Gruppe wurde mithilfe von vielen Personen innerhalb der Microsoft-Community erstellt.

**Autor:** Selcin Turkarslan

**Technische Mitarbeiter:** Conor Cunningham

**Technische Bearbeiter:** Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**Redaktionelle Bearbeitung:** Heidi Steen, Maggie Sparkman.

Vielen Dank für die Umsetzung dieses Artikels!

##<a name="resources"></a>Zusätzliche Ressourcen 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Ressource</th>
   <th align="left" valign="middle">Beschreibung</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN: Azure SQL-Datenbank</a></p>
<p><a href="http://msdn.microsoft.com/library/azure/jj823132.aspx">MSDN: SQL Server auf Azure Virtual Machines</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com: Azure SQL-Datenbank</a></p></td>
   <td valign="middle">Links zur Bibliotheksdokumentation.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/de-de/library/azure/jj879332.aspx">Azure SQL-Datenbank und SQL Server - Vergleich und Gegenüberstellung der Leistung und Skalierbarkeit</p></td>
   <td valign="middle">Dieser Artikel beschreibt die Leistungsunterschiede und Problembehandlung bei Verwendung von Azure SQL-Datenbank und SQL Server lokal oder auf einem virtuellen Computer. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/de-de/library/dn574746.aspx">Anwendungsmuster und Entwicklungsstrategien für SQL Server auf Azure Virtual Machines</p></td>
   <td valign="middle">Dieser Artikel beschreibt die häufigsten Anwendungsmuster, die für SQL Server auf Azure VMs und Hybridszenarien einschließlich Azure SQL-Datenbank gelten. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/de-de/library/hh680934(v=PandP.50).aspx">Microsoft Enterprise Library - Handhabung vorübergehender Fehler - Anwendungsblock</p></td>
   <td valign="middle">Mit dieser Bibliothek können Entwickler ihre Anwendungen in der Azure SQL-Datenbank durch Hinzufügen einer robusten Logik zur Handhabung vorübergehender Fehler stabiler ausführen. Vorübergehende Fehler sind Fehler, die aufgrund einer temporären Bedingung wie z. B. Probleme mit der Netzwerkkonnektivität oder einem nicht verfügbaren Dienst auftreten. Da Azure SQL-Datenbank ein mehrinstanzenfähiger Dienst ist, ist es wichtig, solche Fehler zur Minimierung der Anwendungsausfallzeit zu behandeln. </td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png
