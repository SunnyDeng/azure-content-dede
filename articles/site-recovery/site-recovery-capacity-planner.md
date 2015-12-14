<properties
	pageTitle="Site Recovery Capacity Planner | Microsoft Azure" 
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung lokaler virtueller Computer und physischer Server zu Azure oder zu einem sekundären lokalen Standort." 
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
	ms.date="11/27/2015" 
	ms.author="pratshar"/>

# Site Recovery Capacity Planner

In diesem Dokument wird die Nutzung des Microsoft ASR Capacity Planning-Tools erläutert. Dieses Tool enthält Richtlinien betreffend die Ressourcen, die für nahtlose Site Recovery-Vorgänge bereitgestellt werden müssen. Das Capacity Planning-Tool kann verwendet werden, um die Quellumgebung (Workloads), die Bandbreitenanforderungen, die Ressourcenanforderungen (VMs, Speicher) in der Zielumgebung sowie alle weiteren Serverressourcen zu analysieren, die auf der Quellseite erforderlich sind (SC VMMs, Konfigurationsserver, Prozessserver usw.). Das [Azure Site Recovery Capacity Plannner-Tool](http://aka.ms/asr-capacity-planner-excel) herunterladen
 
Im Capacity Planner gibt es zwei Modi:
 
- **Schnelle Planung**: Abrufen von Netzwerk- und Serverhochrechnungen basierend auf der durchschnittlichen Anzahl/Größe von VMs, Datenträgern, Speicher und der Änderungsrate. 
- **Detaillierte Planung**: Details zu jedem Workload auf VM-Ebene. Sie analysieren die Kompatibilität auf VM-Ebene und erhalten zudem Hochrechnungen betreffend das Netzwerk und die Server.
     
Das Dokument setzt voraus, dass der Benutzer bereits mit Azure Site Recovery vertraut ist. Mehr hierzu finden Sie unter [Übersicht über Azure Site Recovery](site-recovery-overview.md).

## Erste Schritte
###Voraussetzungen
Abhängig vom Modus, in dem der Capacity Planner verwendet werden soll, die Details, die erforderlich sind, um Änderungen vorzunehmen. Neben Details zur Infrastruktur wie VMs, Datenträger pro VM, Speicher pro Datenträger sind noch einige wenige weitere Details erforderlich. Die wichtigste Angabe ist die tägliche Änderungsrate oder die Wechselrate. Wenn es sich bei der Quellumgebung um Hyper-V handelt, verwenden Sie das [Hyper-V Capacity Planning-Tool](https://www.microsoft.com/en-in/download/details.aspx?id=39057), um die Wechselrate abzurufen. Hier finden Sie Anweisungen zur Verwendung des [Hyper-V Capacity Planning-Tools](site-recovery-capacity-planning-for-hyper-v-replication.md). Bei VMWare verwenden Sie das [VMWare Capacity Planning Appliance-Tool](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance).

##Schnelle Planung
1.	Öffnen Sie die Datei **ASR Capacity Planner.xlsm**. Die Datei setzt voraus, dass Makros ausgeführt werden. Klicken Sie daher auf **Bearbeitung aktivieren** und **Inhalt aktivieren**, wenn Sie dazu aufgefordert werden. 
1.	Wählen Sie im Listenfeld **Quick Planner** aus. Damit wird ein Arbeitsblatt mit dem Titel **Capacity Planner** geöffnet.

	![Erste Schritte](./media/site-recovery-capacity-planner/getting-started.png)

1.	Machen Sie im Arbeitsblatt "Capacity Planner" die notwendigen Eingaben. Alle eingekreisten Felder sind Pflichtfelder.
	1.	Wählen Sie im Listenfeld "Scenario" Ihr Szenario aus. Mit diesem Listenfeld können Sie auswählen, ob die Quellumgebung zwischen "Hyper-V to Azure" und "VMWare/Physical to Azure" umgestellt werden kann.
	1. 	Die durchschnittliche tägliche Datenänderungsrate muss ermittelt werden. In Hyper-V-Umgebungen kann hierfür das Hyper-V Capacity Planner-Tool verwendet werden. Bei VMWare kann das VMWare Capacity Planner-Tool verwendet werden. Es wird in der Regel empfohlen, das Tool mindestens einmal pro Woche auszuführen, damit alle Lastspitzen erfasst und gemittelt werden können. 
	1. 	"Compression" – dies ist die von ASR im Szenario "VMWare/Physical to Azure" gebotene Komprimierung. Im Szenario "Hyper-V to Azure" kann dies mithilfe einer Drittanbieter-Appliance wie Riverbed erreicht werden. 
	1. Im Szenario "VMWare/Physical to Azure" sollte "Number of retention" (Beibehaltung) in Tagen angegeben werden. Im Hyper-V-Szenario wird dies in Stunden angegeben. 
	1. Die letzten beiden Eingaben werden verwendet, um die anfängliche Replikation (Initial Replication, IR) zu berechnen. Bei der Bereitstellung des ASR-Setups sollte das gesamte Anfangsdatenset hochgeladen werden. Wünschenswerte Eingaben sind auch die Anzahl der Stunden, in denen die anfängliche Replikation des Batches virtueller Computer abgeschlossen sein sollte, und die Anzahl der virtuellen Computer je anfänglichem Replikationsbatch. Andererseits können diese Zahlen auch optimiert werden, um die vorhandene Bandbreite anzupassen. 

	![Eingaben](./media/site-recovery-capacity-planner/inputs.png)

1. Nach der Eingabe der Details zur Quellumgebung wird die Ausgabe mit Richtlinien angezeigt, die Folgendes umfassen:
	1.	Anforderungen an die Netzwerkbandbreite
		1. Erforderliche Bandbreite für die Deltareplikation (in Megabit/s) Dieser Wert wird basierend auf der durchschnittlichen täglichen Datenänderungsrate berechnet. 
		1. Hier wird auch die für die erste Replikation erforderliche Bandbreite (in Megabit/s) angegeben. Dieser Wert wird basierend auf den angezeigten Eingaben für die erste Replikation (die letzten beiden Zeilen in den Eingaben) berechnet. 
	1.	Anforderungen für Azure
		1. 	In diesem Abschnitt werden die Azure-Anforderungen im Hinblick auf Speicher, IOPS, Speicherkonten und Datenträger näher erläutert. 
	1. 	Weitere Anforderungen an die Infrastruktur 
		1. Alle sonstigen Anforderungen im Szenario "VMware/Physical to Azure" wie Konfigurationsserver und Prozessserver. 
		1. 	Alle sonstigen Anforderung im Szenario "Hyper-V to Azure" wie zusätzlicher Speicherbedarf in der Quellumgebung.
			
	![Ausgabe](./media/site-recovery-capacity-planner/output.png)
 
##Detailed Planner

1.	Öffnen Sie die Datei **ASR Capacity Planner.xlsm**. Die Datei setzt voraus, dass Makros ausgeführt werden. Klicken Sie daher auf **Bearbeitung aktivieren** und **Inhalt aktivieren**, wenn Sie dazu aufgefordert werden. 
1.	Wählen Sie im Listenfeld **Detailed Planner** aus. Damit wird ein Arbeitsblatt mit dem Titel **Workload Qualification** geöffnet.

	![Erste Schritte](./media/site-recovery-capacity-planner/getting-started-2.png)


1.	Machen Sie im Arbeitsblatt "Workload Qualification" die erforderlichen Eingaben. Alle rot markierten Spalten sind Pflichtfelder. Alle anderen Spalten sind optional.
	1.	"Process Cores": Gibt die Gesamtanzahl der Kerne eines Quellservers an.
	1. "Memory Allocation in MB": Gibt die Größe des RAMs eines Quellservers an.
	1.	"Number of NICs": Gibt die Anzahl der Netzwerkkarten eines Quellservers an.
	1. "Total Storage (in GBs)": Gibt die Gesamtspeichergröße der VM an. Wenn der Quellserver beispielsweise über drei Datenträger mit jeweils 500 GB verfügt, beträgt die Gesamtgröße 1.500 GB.
	1. "Number of disks attached": Gibt die Gesamtanzahl der Datenträger eines Quellservers an.
	1. "Disk capacity utilization": Gibt die durchschnittliche Auslastung an. 
	1. "Daily Change rate (%)": Gibt die tägliche Änderungsrate eines Quellservers an.
	1. "Mapping Azure size": Sie können entweder die Größe der Azure-VM angeben, die zugeordnet werden soll, oder auf die Schaltfläche "Compute IaaS VMs" klicken, um die bestmögliche Übereinstimmung zu berechnen. 

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)
 

1. Wenn Sie auf die Schaltfläche **Compute IaaS VMs** klicken, werden die vorstehenden Eingaben ausgewertet, und es wird die bestmögliche Azure VM-Übereinstimmung berechnet. Wenn für einen Quellserver nicht die geeignete Größe der Azure-VM gefunden werden kann, wird für den Server eine Fehlermeldung ausgegeben. Wenn die Anzahl der an eine Quell-VM angefügten Datenträger beispielsweise 65 beträgt, wird eine Fehlermeldung ausgegeben, da bei einer Azure-VM die maximale Anzahl anfügbarer Datenträger 64 beträgt.


Mit **Compute IaaS VMs** wird auch berechnet, ob eine VM ein standardmäßiges Azure-Speicherkonto oder ein Premium-Azure-Speicherkonto benötigt. Darüber hinaus wird angegeben, wie viele standardmäßige Speicherkonten und wie viele Premium-Speicherkonten für den Workload erforderlich sind. Scrollen Sie rechts nach unten, um den Azure-Speichertyp und das Speicherkonto anzuzeigen, das für einen Quellserver verwendet werden kann.
 
**Beispiel**: Für fünf VMs mit den folgenden Werten hat das Tool die beste Übereinstimmung für die Größe der Azure-VM berechnet und zugewiesen und gibt an, ob für die VM Standardspeicher oder Premiumspeicher verwendet werden sollte.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)

Im Beispiel werden zwei Standardspeicherkonten und ein Premium-Speicherkonto für fünf VMs benötigt. VM1 und VM2 verwenden das erste Standardspeicherkonto, und VM3 kann das zweite Standardspeicherkonto verwenden. VM4 und VM5 benötigen je ein Premium-Speicherkonto, daher wird ein einzelnes Premium-Speicherkonto zugewiesen.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)


>[AZURE.NOTE]IOPS wird auf VM-Ebene und nicht auf Datenträgerebene berechnet. Wenn einer der Datenträger einer Quell-VM einen IOPS-Wert > 500 aufweist, die IOPS der VM insgesamt jedoch innerhalb der unterstützten Azure-Standard-VM liegt und wenn alle anderen Werte (Anzahl Datenträger, Anzahl Netzwerkkarten, Anzahl CPU-Kerne, Arbeitsspeichergröße usw.) innerhalb der Grenzwerte der Standard-VM liegen, wählt das Tool eine Standard-VM anstelle des Premiumspeichers aus. Die Benutzer müssen die Zuordnung der Azure-Größenzelle manuell mit einer geeigneten VM aus der DS- oder GS-Reihe aktualisieren.


1.	Die erste Spalte enthält die Validierung für die VMS, die Datenträger und die Wechselrate. 
1.	Nachdem alle Details angegeben wurden, klicken Sie oben auf die Schaltfläche **Submit data to the planner tool**. Damit wird das Arbeitsblatt **Capacity Planner** mit den automatisch ausgefüllten Mittelwerten wie in der nachstehenden Abbildung gezeigt geöffnet. 
1.	Mit dieser Aktion wird auch hervorgehoben, welche Workloads schutzbedürftig sind und welche nicht.


###Capacity Planner

1.	Auf dem Arbeitsblatt **Capacity Planner** wird in der ersten Zeile "Infra Inputs Source" mit Namen **Workload** vorgeschlagen, die Eingabeinformationen aus dem Arbeitsblatt **Workload Qualification** abzurufen.  
1.	Wann immer Änderungen notwendig werden, nehmen Sie diese auf dem Arbeitsblatt **Workload Qualification** vor, und klicken Sie dann auf die Schaltfläche **Submit Data To the planner tool**. 

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_1203_2015-->