Mit einem Lastenausgleichsmodul erreichen Sie eine hohe Verfügbarkeit für Ihre Workloads in Azure. Beim Azure Lastenausgleichsmodul handelt sich um ein Lastenausgleichsmodul des Layer-4-Typs (TCP, UDP), das eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer verteilt, die in einem Lastenausgleichsmodul definiert wurden.
 
Ein Lastenausgleichsmodul lässt sich konfigurieren, um

- einen Lastenausgleich des auf den Virtuellen Computern (VMs) eingehenden Internetdatenverkehrs herzustellen. Wir bezeichnen ein Lastenausgleichsmodul in diesem Szenario als ein [Lastenausgleich mit Internetzugriff](load-balancer-internet-overview.md).
- Lastenausgleich für Datenverkehr zwischen VMs in einem virtuellen Netzwerk (VNet), zwischen virtuellen Computern in Clouddiensten oder zwischen lokalen Computern und VMs in einem standortübergreifenden virtuellen Netzwerk. Wir bezeichnen ein Lastenausgleichsmodul in diesem Szenario als ein [internes Lastenausgleichsmodul mit Internetzugriff (ILB)](load-balancer-internal-overview.md).
- 	Weiterleiten von externem Datenverkehr an eine bestimmte VM-Instanz.

<!---HONumber=Nov15_HO1-->