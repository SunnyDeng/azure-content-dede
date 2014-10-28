<properties linkid="Azure Site Recovery Overview" urlDisplayName="Azure Site Recovery Overview" pageTitle="Azure Site Recovery Overview" metaKeywords="Azure Site Recovery, on-premises, clouds, Azure, VMM, Hyper-V" description="Deploy Azure Site Recovery to protect virtual machines on Hyper-V host servers that are located in VMM clouds. You can deploy from one on-premises site to another, or from an on-premises site to Azure." metaCanonical="" umbracoNaviHide="0" disqusComments="1" title="Azure Site Recovery Overview" editor="jimbe" manager="cfreeman" authors="raynew" />

<tags ms.service="site-recovery" ms.workload="backup-recovery" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="raynew"></tags>

# Übersicht über Azure Site Recovery

<div class="dev-callout">

Verwenden Sie Azure Site Recovery zum Schutz virtueller Computer, die auf Hyper-V-Hostservern in Clouds mit System Center Virtual Machine Manager (VMM) ausgeführt werden. Sie können Azure Site Recovery auf zwei Arten bereitstellen:

-   **Schutz „von lokal an lokal“** – Virtuelle Computer werden von einer lokalen Site an eine andere repliziert. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Die Daten der virtuellen Computer werden von einem Quell-Hyper-V-Hostserver an einen Zielhostserver repliziert. Der Prozess wird von Azure Site Recovery orchestriert. Ein Lernprogramm für dieses Szenario finden Sie unter [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“][Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“].
-   **Schutz „von lokal an Azure“** – Virtuelle Computer werden von einer lokalen Site an Microsoft Azure repliziert. Sie konfigurieren und aktivieren Schutzeinstellungen in Azure Site Recovery-Tresoren. Der Prozess wird von Azure Site Recovery orchestriert, und die replizierten Daten der virtuellen Computer werden in Azure-Speicher gespeichert. Ein Lernprogramm für dieses Szenario finden Sie unter [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an Azure“][Erste Schritte mit Azure Site Recovery: Schutz „von lokal an Azure“].

In der folgenden Tabelle werden die beiden Bereitstellungsoptionen zusammengefasst und verglichen.

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Funktion</th>
<th align="left">Von lokal an Azure</th>
<th align="left">Von lokal an lokal</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Daten an Azure Site Recovery</td>
<td align="left">Cloudmetadaten werden an Azure Site Recovery gesendet. Replizierte Daten werden in Azure-Speicher gespeichert.</td>
<td align="left">Cloudmetadaten werden an Azure Site Recovery gesendet. Replizierte Daten werden auf dem Ziel-Hyper-V-Hostserver gespeichert.</td>
</tr>
<tr class="even">
<td align="left">Tresoranforderungen</td>
<td align="left"><p>Für den Tresor ist ein Zertifikat erforderlich, das die <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">Tresoranforderungen</a> erfüllt.</p>
<p>Es wird automatisch ein Tresorschlüssel erstellt. Der Schlüssel stellt die Integrität des Datenverkehrs zwischen dem Azure Site Recovery-Anbieter auf dem VMM-Server und Azure Site Recovery sicher.</p></td>
<td align="left"><p>Für den Tresor ist ein Zertifikat erforderlich, das die <a href="%20http://go.microsoft.com/fwlink/?LinkId=386485">Tresoranforderungen</a> erfüllt.</p>
<p>Es wird automatisch ein Tresorschlüssel erstellt. Der Schlüssel stellt die Integrität des Datenverkehrs zwischen dem Azure Site Recovery-Anbieter auf dem VMM-Server und Azure Site Recovery sicher.</p></td>
</tr>
<tr class="odd">
<td align="left">Replikation</td>
<td align="left">Die Daten der virtuellen Computer werden vom lokalen Hyper-V-Server an Azure-Speicher repliziert.</td>
<td align="left">Die Daten der virtuellen Computer werden von einem lokalen Hyper-V-Server an einen anderen repliziert.</td>
</tr>
<tr class="even">
<td align="left">Speicher für virtuelle Computer</td>
<td align="left">Festplatte des virtuellen Computers in Azure-Speicher gespeichert</td>
<td align="left">Festplatte des virtuellen Computers auf Hypver-V-Host gespeichert.</td>
</tr>
<tr class="odd">
<td align="left">Azure-Speicher</td>
<td align="left"><p>Azure-Speicherkonto erforderlich.</p>
<p>Für das Konto muss Georeplikation aktiviert sein, es muss sich in der gleichen Region wie der Azure Site Recovery-Dienst befinden, und es muss dem gleichen Abonnement zugeordnet sein.</p></td>
<td align="left">Nicht zutreffend</td>
</tr>
<tr class="even">
<td align="left">VMM-Server</td>
<td align="left">Erfordert nur einen VMM-Quellserver</td>
<td align="left">Erfordert VMM-Quell- und -Zielserver mit je mindestens einer Cloud, bzw. einen einzelnen VMM-Server mit zwei Clouds</td>
</tr>
<tr class="odd">
<td align="left">System Center-Version des VMM-Servers</td>
<td align="left">System Center 2012 R2</td>
<td align="left"><p>System Center 2012 mit SP1</p>
<p>System Center 2012 R2</p></td>
</tr>
<tr class="even">
<td align="left">Azure Site Recovery-Anbieter</td>
<td align="left">Auf VMM-Quellserver installiert</td>
<td align="left">Auf VMM-Quell- und Zielservern installiert</td>
</tr>
<tr class="odd">
<td align="left">Azure Recovery Services-Agent</td>
<td align="left">Auf Hyper-V-Hostservern in VMM-Clouds installiert</td>
<td align="left">Nicht erforderlich</td>
</tr>
<tr class="even">
<td align="left">Wiederherstellungspunkte für virtuellen Computer</td>
<td align="left"><p>Legen Sie Wiederherstellungspunkte nach Zeit fest.</p>
<p>Gibt an, wie lang ein Wiederherstellungspunkt aufbewahrt werden soll (0-24 Stunden). Ein Wiederherstellungspunkt wird mit der Formel „90/copy_frequency during the first hour,and then once an hour“ erstellt.</p></td>
<td align="left"><p>Legen Sie Wiederherstellungspunkte nach Menge fest.</p>
<p>Gibt an, wie viele zusätzliche Wiederherstellungspunkte aufbewahrt werden sollen (0-15). Standardmäßig wird ein Wiederherstellungspunkt pro Stunde erstellt. Wenn Null festgelegt ist, wird nur der letzte Wiederherstellungspunkt auf dem Replikat-Hyper-V-Hostserver gespeichert.</p></td>
</tr>
<tr class="odd">
<td align="left">Netzwerkzuordnung</td>
<td align="left"><p>Ordnen Sie VM-Netzwerke Azure-Netzwerken zu.</p>
<p>Durch die Netzwerkzuordnung wird sichergestellt, dass alle virtuellen Computer im gleichen VM-Quellnetzwerk, für die ein Failover erfolgt, nach dem Failover eine Verbindung herstellen können. Wenn ein Netzwerk-Gateway im Azure-Zielnetzwerk vorhanden ist, können die virtuellen Computer zudem eine Verbindung zu den lokalen virtuellen Computern herstellen.</p>
<p>Wenn die Zuordnung nicht aktiviert ist, können nur virtuelle Computer im gleichen Wiederherstellungsplan, für die ein Failover erfolgt, nach dem Failover an Azure eine Verbindung miteinander herstellen.</p></td>
<td align="left"><p>Ordnen Sie VM-Quellnetzwerke zu VM-Zielnetzwerken zu.</p>
<p>Die Netzwerkzuordnung wird verwendet, um replizierte virtuelle Computer auf optimalen Hyper-V-Hostservern abzulegen. Sie stellt sicher, dass die virtuellen Computer, die dem VM-Quellnetzwerk zugeordnet sind, nach dem Failover dem zugeordneten Zielnetzwerk zugeordnet sind.</p>
<p>Wenn die Zuordnung nicht aktiviert ist, werden die replizierten virtuellen Computer nicht mit einem Netzwerk verbunden.</p></td>
</tr>
<tr class="even">
<td align="left">Speicherzuordnung</td>
<td align="left">Nicht zutreffend</td>
<td align="left"><p>Ordnet Speicherklassifizierungen auf VMM-Quellservern den Speicherklassifizierungen auf VMM-Zielservern zu.</p>
<p>Wenn die Zuordnung aktiviert ist, befinden sich die Festplatten virtueller Computer aus der Quellspeicherklassifizierung nach den Failover in der Zielspeicherklassifizierung.</p>
<p>Wenn die Speicherzuordnung nicht aktiviert ist, werden die replizierten virtuellen Festplatten am Standardspeicherort auf dem Ziel-Hyper-V-Hostserver gespeichert.</p></td>
</tr>
</tbody>
</table>

Wenn Sie Fragen haben, besuchen Sie das [Azure Recovery Services-Forum][Azure Recovery Services-Forum]

</div>

  [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an lokal“]: http://go.microsoft.com/fwlink/?LinkId=398765
  [Erste Schritte mit Azure Site Recovery: Schutz „von lokal an Azure“]: http://go.microsoft.com/fwlink/?LinkId=398764
  [Tresoranforderungen]: %20http://go.microsoft.com/fwlink/?LinkId=386485
  [Azure Recovery Services-Forum]: http://go.microsoft.com/fwlink/?LinkId=313628
