<properties
	pageTitle="ASR-Leitfaden für Active Directory | Microsoft Azure" 
	description="In diesem Artikel wird ausführlich erläutert, wie Sie eine Lösung für die Notfallwiederherstellung für Ihr AD mithilfe von Azure Site Recovery erstellen und ein geplantes/ungeplantes oder Testfailover mithilfe eines mit einem Klick aktivierbaren Wiederherstellungsplans, unterstützter Konfigurationen und erfüllter Voraussetzungen ausführen." 
	services="site-recovery" 
	documentationCenter="" 
	authors="prateek9us" 
	manager="abhiag" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="10/12/2015" 
	ms.author="pratshar"/>

#Automatisierte Lösung für die Notfallwiederherstellung für Active Directory und DNS mithilfe von Azure Site Recovery (ASR)


Alle Unternehmensanwendungen, z. B. SharePoint, Dynamics AX und SAP, basieren auf einer AD- und DNS-Infrastruktur, um richtig funktionieren zu können. Beim Erstellen einer Lösung für die Notfallwiederherstellung für eine Anwendung dieser Art ist es wichtig, AD zu schützen und wiederherzustellen, bevor die anderen Komponenten der Anwendung bei einer Störung wieder verfügbar sind.

Azure Site Recovery ist ein Azure-Dienst, der Funktionen für die Notfallwiederherstellung bietet, indem die Replikation, das Failover und die Wiederherstellung virtueller Computer koordiniert wird. Azure Site Recovery unterstützt eine Reihe von Replikationstechnologien, um für virtuelle Computer und Anwendungen eine einheitliche Replikation, Schutz und ein reibungsloses Failover in private/öffentliche Clouds bzw. in Clouds von Hostern zu bieten.

Mit Azure Site Recovery können Sie für Ihre AD-Umgebung einen vollständig automatisierten Notfallwiederherstellungsplan erstellen. Bei einer Störung können Sie das Failover von überall aus in Sekundenschnelle einleiten und Active Directory (AD) binnen weniger Minuten wieder in Betrieb nehmen. Falls Sie an Ihrem primären Standort eine AD-Umgebung für mehrere Anwendungen verwenden, z. B. SharePoint und SAP, und Sie sich für ein Failover des gesamten Standorts entscheiden, können Sie das Failover mithilfe von ASR zuerst für AD durchführen und anschließend das Failover für die anderen Anwendungen durchführen, indem Sie spezielle Wiederherstellungspläne verwenden.

In diesem Artikel wird ausführlich erläutert, wie Sie eine Lösung für die Notfallwiederherstellung für Ihr AD erstellen und ein geplantes/ungeplantes oder Testfailover mithilfe eines mit einem Klick aktivierbaren Wiederherstellungsplans, unterstützter Konfigurationen und erfüllter Voraussetzungen ausführen. Es wird erwartet, dass die Zielgruppe mit AD und Azure Site Recovery vertraut ist.Basierend auf der Komplexität der lokalen Umgebung des Kunden gibt es zwei empfohlene Optionen für den AD-Schutz.

####Option 1

Bei einer kleinen Anzahl von Anwendungen und einem einzelnen Domänencontroller in der Umgebung sowie einem gemeinsamen Failover des gesamten Standorts empfehlen wir die ASR-Replikation zum Replizieren des Computers mit dem Domänencontroller an einen sekundären Standort (gilt für die Standort-zu-Standort- und Standort-zu-Azure-Replikation). Der gleiche replizierte virtuelle Computer kann auch für ein Testfailover verwendet werden.

####Option 2
Bei einer großen Anzahl von Anwendungen und mehreren Domänencontrollern in der Umgebung oder beim Planen des aufeinander folgenden Failovers verschiedener Anwendungen empfehlen wir zusätzlich zum Aktivieren der ASR-Replikation für den virtuellen Computer mit dem Domänencontroller (der für das Testfailover verwendet werden soll) auch die Einrichtung eines zusätzlichen Domänencontrollers am Standort für die Notfallwiederherstellung (sekundärer lokaler Standort oder Standort in Azure).

>[AZURE.NOTE]Auch wenn Sie sich für Option 2 entscheiden, ist für ein Testfailover weiter die Einrichtung der ASR-Replikation für den Domänencontroller erforderlich. Weitere Details finden Sie im Abschnitt [Überlegungen zum Testfailover](#considerations-for-test-failover).


In den folgenden Abschnitten wird erläutert, wie der Schutz für den Domänencontroller in ASR aktiviert und ein Domänencontroller in Azure eingerichtet wird.


##Voraussetzungen

Für das Implementieren der Notfallwiederherstellung für Active Directory mithilfe von Azure Site Recovery müssen die folgenden Voraussetzungen erfüllt sein:

- Active Directory- und DNS-Server sind lokal bereitgestellt.
- Ein Azure Site Recovery Services-Tresor wurde im Microsoft Azure-Abonnement erstellt. 
- Falls Azure Ihr Wiederherstellungsstandort ist, führen Sie das Tool "Azure Virtual Machine Readiness Assessment" auf VMs aus, um sicherzustellen, dass diese mit Azure VMs und Azure Site Recovery Services kompatibel sind.


##Aktivieren des Schutzes für Domänencontroller und DNS mit ASR


###Schützen virtueller Computer
Aktivieren Sie den Schutz für Domänencontroller und DNS in ASR. Nehmen Sie die entsprechende Azure Site Recovery-Konfiguration basierend darauf vor, ob der virtuelle Computer in Hyper-V oder VMware bereitgestellt ist.Die empfohlene zu konfigurierende Frequenz für Ausfallsicherheit ist 15 Minuten.

###Konfigurieren von VM-Netzwerkeinstellungen
- Konfigurieren Sie für den virtuellen Computer mit dem Domänencontroller bzw. DNS die Netzwerkeinstellungen in ASR so, dass der virtuelle Computer nach einem Failover dem richtigen Netzwerk zugeordnet wird. 
- Wenn Sie z. B. Azure als Standort für die Notfallwiederherstellung nutzen, können Sie den virtuellen Computer in der "VMM-Cloud" oder "Schutzgruppe" auswählen und die Netzwerkeinstellungen wie in der folgenden Momentaufnahme gezeigt konfigurieren.
- 
![VM-Netzwerkeinstellungen](./media/site-recovery-active-directory/VM-Network-Settings.png)

##Aktivieren des Schutzes für Active Directory mithilfe der AD-Replikation
###Standort-zu-Standort-Szenario

Erstellen Sie einen Domänencontroller am sekundären Standort für die Notfallwiederherstellung. Geben Sie beim Heraufstufen des Servers auf die Rolle "Domänencontroller" den Server den Namen der Domäne, die am primären Standort verwendet wird.Sie können das Snap-In "Active Directory-Standorte und Dienste" zum Konfigurieren von Einstellungen für das Standortverknüpfungsobjekt nutzen, dem die Standorte hinzugefügt werden. Durch Konfigurieren von Einstellungen für eine Standortverknüpfung können Sie steuern, wann und wie oft eine Replikation zwischen zwei oder mehr Standorten erfolgt. Unter [Planen der Replikation zwischen Standorten](https://technet.microsoft.com/de-DE/library/cc731862.aspx) finden Sie weitere Details.

###Standort-zu-Azure-Szenario
Befolgen Sie zum Erstellen eines [Domänencontrollers in einem virtuellen Azure-Netzwerk](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md) diese Anweisungen. Geben Sie beim Heraufstufen des Servers auf die Rolle "Domänencontroller" dem Server den Namen der Domäne, die am primären Standort verwendet wird.

Anschließend müssen Sie den [DNS-Server für das virtuelle Netzwerk so neu konfigurieren](../virtual-network/virtual-networks-install-replica-active-directory-domain-controller.md#reconfigure-dns-server-for-the-virtual-network), dass der DNS-Server in Azure verwendet wird.
  
![Azure-Netzwerk](./media/site-recovery-active-directory/azure-network.png)

##Überlegungen zum Testfailover
Das Testfailover erfolgt in einem Netzwerk, das vom Produktionsnetzwerk isoliert ist, damit es keine Auswirkungen auf Workloads in der Produktion gibt. Bei den meisten Anwendungen muss außerdem ein Domänencontroller und ein DNS-Server vorhanden sein, damit das Testfailover funktioniert. Bevor ein Failover für eine Anwendung erfolgt, muss ein Domänencontroller im isolierten Netzwerk erstellt werden, der für das Testfailover verwendet werden soll. Die einfachste Möglichkeit hierzu ist zunächst das Aktivieren des Schutzes auf dem virtuellen Computer mit dem Domänencontroller bzw. DNS mithilfe von ASR. Anschließend muss das Testfailover auf dem virtuellen Computer mit dem Domänencontroller ausgelöst werden, ehe das Testfailover des Wiederherstellungsplans der Anwendung ausgelöst wird. Es folgt die schrittweise Anleitung für das Testfailover:

1. Aktivieren Sie den Schutz auf dem virtuellen Computer mit dem Domänencontroller bzw. DNS wie bei jedem anderen virtuellen Computer.
2. Erstellen Sie ein isoliertes Netzwerk. Jedes in Azure erstellte virtuelle Netzwerk ist standardmäßig von anderen Netzwerken isoliert. Es wird empfohlen, dass der IP-Adressbereich dieses Netzwerks mit dem Ihres Produktionsnetzwerk identisch ist. Aktivieren Sie nicht die Standort-zu-Standort-Konnektivität in diesem Netzwerk.
3. Geben Sie die DNS-IP-Adresse in dem im vorherigen Schritt erstellten Netzwerk als die IP-Adresse an, die die VM mit DNS erhalten soll. Bei Verwendung von Azure als Standort für die Notfallwiederherstellung können Sie die IP-Adresse der VM, die beim Failover verwendet wird, in den VM-Eigenschaften in der Einstellung "Ziel-IP-Adresse" angeben. Wenn Ihr Standort für die Notfallwiederherstellung lokal ist und Sie DHCP nutzen, befolgen Sie diese Anweisungen zum [Einrichten von DNS und DHCP für ein Testfailover](site-recovery-failover.md#prepare-dhcp). 

>[AZURE.NOTE]Die IP-Adresse, die ein virtueller Computer für ein Testfailover erhält, entspricht der IP-Adresse, die dieser bei einem geplanten oder ungeplanten Failover erhalten würde. Voraussetzung ist, dass die IP-Adresse im Testfailovernetzwerk verfügbar ist. Wenn im Testfailovernetzwerk nicht dieselbe IP-Adresse verfügbar ist, wird dem virtuellen Computer eine andere IP-Adresse zugewiesen, die im Testfailovernetzwerk verfügbar ist.

4. Wechseln Sie zum virtuellen Computer mit dem Domänencontroller, und führen Sie für diesen im isolierten Netzwerk ein Testfailover durch. 
5. Führen Sie ein Testfailover des Wiederherstellungsplans der Anwendung durch.
6. Markieren Sie nach Abschluss des Tests das Testfailover des Auftrags für den virtuellen Computer mit dem Domänencontroller und den Wiederherstellungsplan in ASR auf der Registerkarte "Aufträge" als "Abgeschlossen". 

###Wenn DNS nicht auf demselben virtuellen Computer wie der Domänencontroller ist: 
Für den Fall, dass DNS nicht auf dem gleichen virtuellen Computer wie der Domänencontroller ist, müssen Sie einen DNS-Server für das Testfailover erstellen. Falls sich beide auf demselben virtuellen Computer befinden, können Sie diesen Abschnitt überspringen. Sie können einen neuen DNS-Server verwenden und alle erforderlichen Zonen erstellen. Wenn Ihre Active Directory-Domäne beispielsweise „contoso.com“ lautet, können Sie eine Zone mit dem Namen „contoso.com“ erstellen. Die zu Active Directory gehörenden Einträge müssen in DNS aktualisiert werden. Gehen Sie hierzu wie folgt vor:

- Stellen Sie sicher, dass die folgenden Einstellungen vorhanden sind, bevor ein anderer virtueller Computer im Wiederherstellungsplan online geschaltet wird:
	- Die Zone muss nach dem Stammnamen der Gesamtstruktur benannt werden.
	- Der Zone muss eine Datei zugrunde liegen.
	- Für die Zone müssen sichere und nicht sichere Updates möglich sein.
	- Die Auflösung des virtuellen Computers mit dem Domänencontroller muss auf die IP-Adresse des virtuellen Computers mit DNS zeigen.
- Führen den folgenden Befehl auf dem virtuellen Computer mit dem Domänencontroller aus: nltest /dsregdns

- **Hinzufügen der Zone**: Verwenden Sie das folgende Beispielskript, um auf dem DNS-Server eine Zone hinzufügen, nicht sichere Updates zuzulassen und einen Eintrag für sich selbst zu DNS hinzuzufügen:

	    dnscmd /zoneadd contoso.com  /Primary 
	    dnscmd /recordadd contoso.com  contoso.com. SOA %computername%.contoso.com. hostmaster. 1 15 10 1 1 
	    dnscmd /recordadd contoso.com %computername%  A <IP_OF_DNS_VM> 
	    dnscmd /config contoso.com /allowupdate 1


##Zusammenfassung
Mit Azure Site Recovery können Sie für Ihre AD-Umgebung einen vollständig automatisierten Notfallwiederherstellungsplan erstellen. Bei einer Störung können Sie das Failover von überall aus in Sekundenschnelle einleiten und Active Directory (AD) binnen weniger Minuten wieder in Betrieb nehmen. Falls Sie an Ihrem primären Standort eine AD-Umgebung für mehrere Anwendungen verwenden, z. B. SharePoint und SAP, und Sie sich für ein Failover des gesamten Standorts entscheiden, können Sie das Failover mithilfe von ASR zuerst für AD durchführen und anschließend das Failover für die anderen Anwendungen durchführen, indem Sie spezielle Wiederherstellungspläne verwenden.


Im [Leitfaden zu Site Recovery-Workloads](../site-recovery/site-recovery-workload.md) finden Sie weitere Details zum Schützen verschiedener Workloads mit ASR.

<!---HONumber=Oct15_HO3-->