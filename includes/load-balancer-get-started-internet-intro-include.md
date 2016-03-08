Mit einem Lastenausgleichsmodul erreichen Sie eine hohe Verfügbarkeit für Ihre Workloads in Azure. Beim Azure-Load Balancer handelt sich um einen Load Balancer der Schicht 4 (TCP, UDP), das eingehenden Datenverkehr zwischen funktionierenden Dienstinstanzen in Clouddiensten oder auf virtuelle Computer verteilt, die in einer Load Balancer-Gruppe definiert wurden.
 
Ein Lastenausgleichsmodul lässt sich konfigurieren, um

- einen Lastenausgleich des auf den virtuellen Computern (VMs) eingehenden Internetdatenverkehrs herzustellen. Wir bezeichnen ein Lastenausgleichsmodul in diesem Szenario als ein [Lastenausgleich mit Internetzugriff](../articles/load-balancer/load-balancer-internet-overview.md).
- Lastenausgleich für Datenverkehr zwischen VMs in einem virtuellen Netzwerk (VNet), zwischen virtuellen Computern in Clouddiensten oder zwischen lokalen Computern und VMs in einem standortübergreifenden virtuellen Netzwerk. Wir bezeichnen einwn Load Balancer in diesem Szenario als einen [internen Load Balancer (ILB)](../articles/load-balancer/load-balancer-internal-overview.md).
- 	Weiterleiten von externem Datenverkehr an eine bestimmte VM-Instanz.

<!---HONumber=AcomDC_0224_2016-->