<properties 
	pageTitle="Übersicht über Azure Site Recovery" 
	description="Stellen Sie Azure Site Recovery zum Schutz lokaler Computer an einem anderen lokalen Standort oder in Azure bereit." 
	services="site-recovery" 
	documentationCenter="" 
	authors="raynew" 
	manager="jwhit" 
	editor="tysonn"/>

<tags 
	ms.service="site-recovery" 
	ms.workload="backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="raynew"/>

# Übersicht über Azure Site Recovery

<div class="dev-callout"> 
<p>Azure Site Recovery orchestriert Replikation und Failover in verschiedenen Szenarien:</p>


<ul>
<li>**Schutz von lokalen Hyper-V-Standorten in Azure mit Hyper-V-Replikation** - Orchestrierung von Replikation, Failover und Wiederherstellung von einem lokalen Standort mit einem oder mehreren Hyper-V-Servern, aber ohne System Center VMM. Die Daten der virtuellen Computer werden von einem Quell-Hyper-V-Hostserver an Azure repliziert. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=522298">Erste Schritte mit Azure Site Recovery: Schutz zwischen einer lokaler Umgebung und Azure mithilfe der Hyper-V-Replikation</a>.</li>
<li>**Schutz von lokalen VMM-Standorten in lokalen VMM-Standorten mit Hyper-V-Replikation** - Orchestrierung von Replikation, Failover und Wiederherstellung zwischen lokalen VMM-Standorten. Die Daten der virtuellen Computer werden von einem Quell-Hyper-V-Hostserver an einen Zielhostserver repliziert. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=398765">Erste Schritte mit Azure Site Recovery: Schutz zwischen zwei lokalen VMM-Standorten mit Hyper-V-Replikation</a>.</li>

<li>**Schutz von lokalen VMM-Standorten in lokalen VMM-Standorten mit SAN-Replikation** - Orchestrierung von vollständiger Replikation, vollständigem Failover und vollständiger Wiederherstellung mithilfe der speicherarraybasierten Replikation zwischen SAN-Geräten, die Daten virtueller Computer an den lokalen Quell- und Zielstandorten hosten. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=518683">Erste Schritte mit Azure Site Recovery: ab: Schutz zwischen zwei lokalen VMM-Standorten mit SAN-Replikation</a>.</li>

<li>**Schutz von VMM-Standorten in Azure** - Orchestrierung von Replikation, Failover und Wiederherstellung zwischen einem lokalen VMM-Standort und Azure. Replizierte Daten der virtuellen Computer werden in Azure-Speicher gespeichert. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=398764">Erste Schritte mit Azure Site Recovery: Schutz zwischen einem lokalen VMM-Standort und Azure.</a></li>

<li>**Schutz von lokalen VMware-Standorten in lokalen VMware-Standorten mit InMage** - InMage Scout wurde vor kurzem von Microsoft erworben und bietet Echtzeitreplikation zwischen lokalen VMware-Standorten. InMage ist zurzeit als separates Produkt verfügbar und kann über ein Abonnement für den Azure Site Recovery-Service bezogen werden. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=518684">Erste Schritte mit Azure Site Recovery: Schutz zwischen lokalen VMware-Standorten mit InMage</a>.</li>
</ul>

<p>Die Funktionsmatrix fasst diese Szenarien zusammen.</p>

<table border="1">
<thead>
<tr>
	<th>Funktion</th><th>Von lokal an Azure</th>
	<th>Zwischen lokal und lokal (Hyper-V)</th>
	<th>Zwischen lokal und lokal (SAN)</th>
</tr>
</thead>

<tr>
<td>Daten an Azure Site Recovery</td>
<td><p>Cloudmetadaten werden an Azure Site Recovery gesendet.</p> <p>Replizierte Daten werden in Azure-Speicher gespeichert.</p></td>
<td><p>Cloudmetadaten werden an Azure Site Recovery gesendet.</p> <p>Replizierte Daten werden an dem Standort gespeichert, der durch den Hyper-V-Zielserver angegeben wird.</p></td>
<td><p>Cloudmetadaten werden an Azure Site Recovery gesendet.</p> <p>Replizierte Daten werden im Zielarrayspeicher gespeichert.</p></td>
</tr>

<tr>
<td>Tresoranforderungen</td>
<td><p>Sie benötigen ein Azure-Konto, um einen Azure Site Recovery-Tresor einzurichten. Siehe <a href="http://aka.ms/try-azure">1 Monat kostenlose Testversion</a>. Preisinformationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=378268">Azure Site Recovery-Preise</a>.</p></td>
<td><p>Azure-Konto mit aktiviertem Azure Site Recovery.</p>
</td><td><p>Azure-Konto mit aktiviertem Azure Site Recovery.</p></td>
</tr>

<tr>
<td>Replikation</td>
<td>Die Daten der virtuellen Computer werden vom lokalen Hyper-V-Quellserver an Azure-Zielspeicher repliziert. Sie können die umgekehrte Replikation einrichten, um Daten zurück an den Quellstandort zu replizieren.</td>
<td>Die Daten der virtuellen Computer werden von einem lokalen Hyper-V-Quellserver an einen anderen repliziert. Sie können die umgekehrte Replikation einrichten, um Daten zurück an den Quellstandort zu replizieren.</td>
<td>Die Daten der virtuellen Computer werden von einem SAN-Quellspeichergerät an ein SAN-Zielgerät repliziert. Sie können die umgekehrte Replikation einrichten, um Daten zurück an den Quellstandort zu replizieren.</td>
</tr>

<tr>
<td>Speicher für virtuelle Computer</td>
<td>Festplatte des virtuellen Computers in Azure-Speicher gespeichert</td>
<td>Festplatte des virtuellen Computers auf Hypver-V-Host gespeichert.</td>
<td>Festplatte des virtuellen Computers in SAN-Speicherarray gespeichert.</td>
</tr>

<tr>
<td>Azure-Speicher</td>
<td><p>Azure-Speicherkonto erforderlich.</p> <p>Für das Konto muss Georeplikation aktiviert sein, es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden, und es muss dem gleichen Abonnement zugeordnet sein.</p></td>
<td>Nicht zutreffend</td>
<td>Nicht zutreffend</td>
</tr>

<tr>
<td>SAN-Speicherarray</td>
<td><p>Nicht zutreffend</p></td>
<td>Nicht zutreffend</td>
<td>Das SAN-Speicherarray muss sowohl am Quell- als auch am Zielstandort verfügbar sein und durch VMM verwaltet werden. Weitere Informationen finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=518685">Voraussetzungen für die Bereitstellung</a>. </td>
</tr>

<tr>
<td>VMM-Server</td>
<td>Erfordert nur einen VMM-Server ausschließlich am Quellstandort. Der VMM-Server muss über mindestens eine Cloud verfügen, die mindestens einen Hyper-V-Hostserver oder -Cluster enthält.</td>
<td>Erfordert VMM-Quell- und -Zielserver mit je mindestens einer Cloud bzw. einen einzelnen VMM-Server mit zwei Clouds. Clouds müssen mindestens einen Hyper-V-Hostserver oder -Cluster umfassen.</td>
<td>Erfordert VMM-Quell- und -Zielserver mit je mindestens einer Cloud. Clouds müssen mindestens einen Hyper-V-Cluster umfassen.</td>
</tr>

<tr>
<td>VMM System Center-Version</td>
<td>System Center 2012 R2</td>
<td><p>System Center 2012 mit SP1</p><p>System Center 2012 R2</p></td>
<td><p>System Center 2012 R2 mit VMM Update Rollup 5.0</p></td>
</tr>

<tr>
<td>VMM-Konfiguration</td>
<td><p>Einrichten von Clouds am Quell- und Zielstandort</p><p>Einrichten von VM-Netzwerken am Quell- und Zielstandort</p><p>Einrichten von Speicherklassifizierungen am Quell- und Zielstandort <p>Installieren des Anbieters auf den VMM-Quell- und -Zielservern</p></td>
<td><p>Einrichten von Clouds am Quellstandort</p><p>Einrichten des SAN-Speichers</p><p>Einrichten von VM-Netzwerken am Quellstandort</p><p>Installieren des Anbieters auf dem VMM-Quellserver</p><p>Aktivieren des Schutzes für virtuelle Computer</p></td>
<td><p>Einrichten von Clouds am Quell- und Zielstandort</p><p>Einrichten von VM-Netzwerken am Quell- und Zielstandort</p><p>Installieren des Anbieters auf den VMM-Quell- und -Zielservern</p><p>Aktivieren des Schutzes für virtuelle Computer</p></td>
</tr>

<tr>
<td>Azure Site Recovery-Anbieter</td>
<td>Auf VMM-Quellserver installiert</td>
<td>Auf VMM-Quell- und Zielservern installiert</td>
<td>Auf VMM-Quell- und Zielservern installiert</td>
</tr>

<tr>
<td>Azure Recovery Services-Agent</td>
<td>Auf Hyper-V-Hostservern in VMM-Clouds installiert</td>
<td>Nicht erforderlich</td>
<td>Nicht erforderlich</td>
</tr>

<tr>
<td>Wiederherstellungspunkte für virtuellen Computer</td>
<td><p>Legen Sie Wiederherstellungspunkte nach Zeit fest.</p> <p>Gibt an, wie lang ein Wiederherstellungspunkt aufbewahrt werden soll (0-24 Stunden). Ein Wiederherstellungspunkt wird mit der Formel "90/copy_frequency during the first hour,and then once an hour" erstellt.</p></td>
<td><p>Legen Sie Wiederherstellungspunkte nach Menge fest.</p> <p>Gibt an, wie viele zusätzliche Wiederherstellungspunkte aufbewahrt werden sollen (0-15). Standardmäßig wird ein Wiederherstellungspunkt pro Stunde erstellt. Wenn Null festgelegt ist, wird nur der letzte Wiederherstellungspunkt auf dem Replikat-Hyper-V-Hostserver gespeichert.</p></td>
<td>Konfiguriert in Arrayspeichereinstellungen.</td>
</tr>

<tr>
<td>Netzwerkzuordnung</td>
<td><p>Ordnen Sie VM-Netzwerke Azure-Netzwerken zu.</p> <p>Durch die Netzwerkzuordnung wird sichergestellt, dass alle virtuellen Computer im gleichen VM-Quellnetzwerk, für die ein Failover erfolgt, nach dem Failover eine Verbindung herstellen können. Wenn ein Netzwerk-Gateway im Azure-Zielnetzwerk vorhanden ist, können die virtuellen Computer zudem eine Verbindung zu den lokalen virtuellen Computern herstellen. </p><p>Wenn die Zuordnung nicht aktiviert ist, können nur virtuelle Computer im gleichen Wiederherstellungsplan, für die ein Failover erfolgt, nach dem Failover an Azure eine Verbindung miteinander herstellen.</p></td>
<td><p>Ordnen Sie VM-Quellnetzwerke zu VM-Zielnetzwerken zu.</p> <p>Die Netzwerkzuordnung wird verwendet, um replizierte virtuelle Computer auf optimalen Hyper-V-Hostservern abzulegen. Sie stellt sicher, dass die virtuellen Computer, die dem VM-Quellnetzwerk zugeordnet sind, nach dem Failover dem zugeordneten Zielnetzwerk zugeordnet sind. </p><p>Wenn die Zuordnung nicht aktiviert ist, werden die replizierten virtuellen Computer nicht mit einem Netzwerk verbunden.</p></td>
<td><p>Ordnen Sie VM-Quellnetzwerke zu VM-Zielnetzwerken zu.</p> <p>Die Netzwerkzuordnung stellt sicher, dass mit dem VM-Quellnetzwerk verknüpfte virtuelle Computer nach dem Failover mit dem zugeordneten Zielnetzwerk verknüpft werden. </p><p>Wenn die Zuordnung nicht aktiviert ist, werden die replizierten virtuellen Computer nicht mit einem Netzwerk verbunden.</p></td>
</tr>

<tr>
<td>Speicherzuordnung</td>
<td>Nicht zutreffend</td>
<td><p>Ordnet Speicherklassifizierungen auf VMM-Quellservern den Speicherklassifizierungen auf VMM-Zielservern zu.</p> <p>Wenn die Zuordnung aktiviert ist, befinden sich die Festplatten virtueller Computer aus der Quellspeicherklassifizierung nach den Failover in der Zielspeicherklassifizierung.</p><p>Wenn die Speicherzuordnung nicht aktiviert ist, werden die replizierten virtuellen Festplatten am Standardspeicherort auf dem Ziel-Hyper-V-Hostserver gespeichert.</p></td>
<td><p>Zuordnungen zwischen Speicherarrays und -pools am primären und sekundären Standort.</p></td>
</tr>

</table>

<p>Wenn Sie Fragen haben, besuchen Sie das <a href="http://go.microsoft.com/fwlink/?LinkId=313628">Azure Recovery Services-Forum</a>.</p> 

<!--HONumber=49-->