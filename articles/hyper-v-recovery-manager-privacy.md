<properties 
	pageTitle="Datenschutzinformationen für Azure Site Recovery" 
	description="Beschreibt zusätzliche Datenschutzinformationen für Azure Site Recovery" 
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
	ms.date="03/19/2015" 
	ms.author="raynew"/>

# Datenschutzinformationen für Azure Site Recovery

Diese Tabelle enthält zusätzliche Datenschutzinformationen für den Microsoft Azure Site Recovery-Service ("Service"). Die Datenschutzbestimmungen für Microsoft Azure-Dienste finden Sie in den
[Onlinedatenschutzbestimmungen für Online Dienste](http://go.microsoft.com/fwlink/?LinkId=324899)

<table>
<thead>
<tr>
	<th>Funktion</th>
	<th>Funktionsbeschreibung</th>
	<th>Erfasste Informationen</th>
	<th>Verwenden Sie</th>
	<th>Auswahl</th>
</tr>
</thead>
<tbody>
<tr>
	<td><p><b>Registrierung</b></p></td>
	<td><p>Registriert Server beim Service, sodass virtuelle Computer geschützt werden können.</p></td>
	<td><p>Nach der Registrierung eines Servers erfasst und überträgt der Service folgende Informationen:</p>
		<ul>
			<li>Informationen zum Verwaltungszertifikat von dem VMM-Server, auf dem die Notfallwiederherstellung unter Verwendung des Servicenamens des VMM-Servers erfolgen soll.</li>
			<li>Der Name der VM-Clouds auf dem VMM-Server.</li>
		</ul>
</td>
	<td><p>Der Service verwendet die oben stehenden Informationen wie folgt:</p>
		<ul>
			<li>Verwaltungszertifikat - wird verwendet, um den registrierten VMM-Server für den Zugriff auf den Service zu identifizieren und zu authentifizieren. Der Service verwendet den öffentlichen Schlüsselteil des Zertifikats zum Sichern eines Tokens, auf das nur der registrierte VMM-Server zugreifen kann. Der Server muss dieses Token für den Zugriff auf die Servicefunktionen zu verwenden.</li>
			<li>Name des VMM-Servers - der VMM-Servername ist erforderlich, um den entsprechenden VMM-Server, auf dem sich die Clouds befinden, zu identifizieren und mit diesem zu kommunizieren. 
			</li>
			<li>Namen der Clouds auf dem VMM-Server - der Cloudname wird benötigt, wenn die unten beschriebene Servicefunktion zum Koppeln/Entkoppeln von Clouds verwendet wird. Wenn Sie eine Cloud in einem primären Datencenter mit einer anderen Cloud im Wiederherstellungs-Datencenter koppeln möchten, werden die Namen aller Clouds im Wiederherstellungs-Datencenter angezeigt.</li>
		</ul>
</td>
	<td><p>Diese Informationen sind ein wesentlicher Bestandteil des Registrierungsprozesses des Service, da sie Ihnen und dem Service dabei helfen, den VMM-Server zu identifizieren, für den Sie Azure Site Recovery-Schutz bereitstellen möchten. Die Informationen helfen auch dabei, den richtigen registrierten VMM-Server zu identifizieren. Wenn Sie diese Informationen nicht an den Service senden möchten, verwenden Sie diesen Service nicht. Wenn Sie den Server registrieren und die Registrierung später aufheben möchten, können Sie die VMM-Serverinformationen vom Serviceportal (dem Azure-Portal) löschen.</p></td>
</tr>

<tr>
	<td><p><b>Aktivieren des Azure Site Recovery-Schutzes</b></p></td>
	<td><p>Der auf dem VMM-Server installierte Azure Site Recovery-Anbieter ist zuständig für die Kommunikation mit dem Service. Der Anbieter ist eine Dynamic Link Library (DLL), die im VMM-Prozess gehostet wird. Nachdem der Anbieter installiert wurde, wird die Funktion zur Datencenter-Wiederherstellung in der VMM-Administratorkonsole aktiviert. Alle neuen oder vorhandenen virtuellen Computer in einer Cloud können eine Eigenschaft zur Datencenter-Wiederherstellung aktivieren, um den virtuellen Computer zu schützen. Sobald diese Eigenschaft festgelegt ist, sendet der Anbieter den Namen und die ID des virtuellen Computers an den Service. Der virtuelle Schutz wird durch die Hyper-V-Replikationstechnologie von Windows Server 2012 oder Windows Server 2012 R2 aktiviert. Die Daten des virtuellen Computers werden von einem Hyper-V-Host an einen anderen repliziert (der sich in der Regel in einem anderen Wiederherstellungs-Datencenter befindet).</p></td>
	<td><p>Der Service erfasst, verarbeitet und überträgt Metadaten für den virtuellen Computer, einschließlich des Namens, der ID, des virtuellen Netzwerks und des Namens der Cloud, zu der er gehört.</p>
	</td>
	<td><p>Der Service verwendet oben stehende Informationen zum Auffüllen des Serviceportals mit den Informationen zum virtuellen Computer.</p>
	</td>
	<td><p>Dies ist ein wesentlicher Bestandteil des Service und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Service gesendet werden, aktivieren Sie den Azure Site Recovery-Schutz für keinen virtuellen Computer. Beachten Sie, dass alle Daten vom Anbieter über HTTPS an den Service gesendet werden.</p></td>
</tr>
<tr>
	<td><p><b>Wiederherstellungsplan</b></p></td>
	<td><p>Diese Funktion unterstützt Sie beim Erstellen eines Orchestrierungsplans für das Wiederherstellungs-Datencenter. Sie können die Reihenfolge definieren, in der die virtuellen Computer oder eine Gruppe von virtuellen Computern am Wiederherstellungsstandort gestartet werden sollen. Sie können auch automatisierte Skripts oder manuelle Aktionen festlegen, die zum Zeitpunkt der Wiederherstellung für jeden virtuellen Computer ausgeführt werden sollen. Failover (wird im nächsten Abschnitt behandelt) wird in der Regel auf der Ebene des Wiederherstellungsplans ausgelöst, um für eine koordinierte Wiederherstellung zu sorgen.</p></td>
	<td><p>Der Service erfasst, verarbeitet und überträgt diese Informationen als Teil der Wiederherstellungsplanfunktion:</p>
		<ul>
			<li>Wiederherstellungsplan, einschließlich Metadaten zu virtuellen Computern.</li>
			<li>Metadaten des Automatisierungsskripts oder der Hinweis zu manuellen Aktionen.</li>
		</ul>
	</td>
	<td><p>Die oben beschriebenen Metadaten werden zum Erstellen des Wiederherstellungsplans im Serviceportal verwendet.</p>
	</td>
	<td><p>Dies ist ein wesentlicher Bestandteil des Service und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Service gesendet werden, erstellen Sie in diesem Service keine Wiederherstellungspläne.</p></td>
</tr>
<tr>
	<td><p><b>Netzwerkzuordnung</b></p></td>
	<td><p>Mit dieser Funktion können Sie dem Wiederherstellungs-Datencenter Netzwerkinformationen des primären Datencenters zuordnen. Wenn die virtuellen Computer am Wiederherstellungsstandort wiederhergestellt werden, hilft diese Zuordnung beim Herstellen der Netzwerkkonnektivität für sie.</p></td>
	<td><p>Im Rahmen der Netzwerkzuordnungsfunktion erfasst, verarbeitet und überträgt der Service die Metadaten der logischen Netzwerke für jeden Standort (primär und Datencenter).</p>
	</td>
	<td><p>Der Service verwendet die Metadaten zum Auffüllen des Serviceportals, in dem Sie die Netzwerkinformationen zuordnen können.</p>
	</td>
	<td><p>Dies ist ein wesentlicher Bestandteil des Service und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Service gesendet werden, verwenden Sie die Funktion zur Netzwerkzuordnung nicht.</p></td>
</tr>
<tr>
	<td><p><b>Fehler - geplant, ungeplant, Text</b></p></td>
	<td><p>Mit dieser Funktion kann ein Failover eines virtuellen Computers in einem über VMM verwalteten Datencenter zu einem anderen über VMM verwalteten Datencenter ausgeführt werden. Die Failoveraktion wird durch die Benutzer in ihrem Serviceportal ausgelöst. Mögliche Gründe für ein Failover: ungeplante Ereignisse (z. B. eine Naturkatastrophe), geplante Ereignisse (z. B. Lastenausgleich im Datencenter), Testfailover (z. B. ein Probelauf für einen Wiederherstellungsplan).</p>
	<p>Der Anbieter auf dem VMM-Server erhält eine Benachrichtigung des Service zum Ereignis und führt eine Failoveraktion über VMM-Schnittstellen auf dem Hyper-V-Host aus. Das eigentlich Failover des virtuellen Computers von einem Hyper-V-Host zu einem anderen (der in der Regel in einem anderen Wiederherstellungsrechenzentrum ausgeführt wird) wird durch die Hyper-V-Replikationstechnologie von Windows Server 2012 oder Windows Server 2012 R2 verarbeitet. Nachdem das Failover abgeschlossen ist, sendet der auf dem VMM-Server des Wiederherstellungsrechenzentrum installierte Anbieter die Informationen zur erfolgreichen Ausführung an den Service.</p>
	</td>
	<td><p>Der Service verwendet oben stehende Informationen zum Auffüllen des Serviceportals mit den Statusinformationen zur Failoveraktion.</p>
	</td>
	<td><p>Der Service verwendet die oben stehenden Informationen wie folgt:</p>
		<ul>
			<li>Verwaltungszertifikat - wird verwendet, um den registrierten VMM-Server für den Zugriff auf den Service zu identifizieren und zu authentifizieren. Der Service verwendet den öffentlichen Schlüsselteil des Zertifikats zum Sichern eines Tokens, auf das nur der registrierte VMM-Server zugreifen kann. Der Server muss dieses Token für den Zugriff auf die Servicefunktionen zu verwenden.</li>
			<li>Name des VMM-Servers - der VMM-Servername ist erforderlich, um den entsprechenden VMM-Server, auf dem sich die Clouds befinden, zu identifizieren und mit diesem zu kommunizieren. 
			</li>
			<li>Namen der Clouds auf dem VMM-Server - der Cloudname wird benötigt, wenn die unten beschriebene Servicefunktion zum Koppeln/Entkoppeln von Clouds verwendet wird. Wenn Sie eine Cloud in einem primären Datencenter mit einer anderen Cloud im Wiederherstellungs-Datencenter koppeln möchten, werden die Namen aller Clouds im Wiederherstellungs-Datencenter angezeigt.</li>
		</ul>
	</td>
	<td><p>Dies ist ein wesentlicher Bestandteil des Service und kann nicht deaktiviert werden. Wenn Sie nicht möchten, dass diese Informationen an den Service gesendet werden, verwenden Sie diesen Service nicht.</p></td>
</tr>
</table>



<!--HONumber=49-->