<properties
	pageTitle="Planen der Kapazität für den Schutz virtueller Computer und physischer Server in Azure Site Recovery | Microsoft Azure"
	description="Azure Site Recovery koordiniert Replikation, Failover und Wiederherstellung lokaler virtueller Computer und physischer Server zu Azure oder zu einem sekundären lokalen Standort." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

# Planen der Kapazität für den Schutz virtueller Computer und physischer Server in Azure Site Recovery

Mit dem Tool Azure Site Recovery Capacity Planner können Sie ermitteln, welche Kapazitätsanforderungen für den Schutz von Hyper-V-VMs, VMware-VMs und physischen Windows/Linux-Servern mit Azure Site Recovery gelten.


## Übersicht

Verwenden Sie Site Recovery Capacity Planner, um Ihre Quellumgebung und Workloads zu analysieren. Außerdem können Sie die Bandbreitenanforderungen, Serverressourcen am Quellstandort und die Ressourcen (virtuelle Computer und Speicher usw.) ermitteln, die Sie am Zielstandort benötigen.

Sie können das Tool in einem von zwei Modi ausführen:

- **Schnelle Planung**: Führen Sie das Tool in diesem Modus aus, um Netzwerk- und Serverhochrechnungen basierend auf der durchschnittlichen Anzahl/Größe von VMs, Datenträgern, Speicher und der Änderungsrate zu erhalten.
- **Detaillierte Planung**: Führen Sie das Tool in diesem Modus aus, und geben Sie die Details für jede Workload auf VM-Ebene an. Analysieren Sie die VM-Kompatibilität, und rufen Sie Netzwerk- und Serverhochrechnungen ab.

## Vorbereitung

Vor Ausführung des Tools:

1. Sammeln Sie Informationen zu Ihrer Umgebung, z. B. VMs, Festplatten pro VM, Speicher pro Datenträger.
2. Ermitteln Sie Ihre tägliche Änderungsrate für replizierte Daten. Gehen Sie dazu folgendermaßen vor:

	- Laden Sie beim Replizieren von Hyper-V-VMs das [Tool für die Hyper-V-Kapazitätsplanung](https://www.microsoft.com/download/details.aspx?id=39057) herunter, um die Änderungsrate zu erhalten. [Hier](site-recovery-capacity-planning-for-hyper-v-replication.md) finden Sie weitere Informationen zu diesem Tool. Es wird empfohlen, das Tool eine Woche lang auszuführen, um Durchschnittswerte zu erfassen.
	- Verwenden Sie beim Replizieren von virtuellen VMware-Maschinen die [Appliance für die vSphere-Kapazitätsplanung](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance), um die Änderungsrate zu ermitteln.
	- Wenn Sie physische Server replizieren, müssen Sie eine manuelle Schätzung erstellen.

## Ausführen des Quick Planner-Tools
1.	Laden Sie das Tool [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) herunter, und öffnen Sie es. Sie müssen Makros ausführen. Aktivieren Sie also die Bearbeitung und den Inhalt, wenn Sie dazu aufgefordert werden. 
2.	Wählen Sie unter **Planertyp auswählen** im Listenfeld die Option **Quick Planner** aus.

	![Erste Schritte](./media/site-recovery-capacity-planner/getting-started.png)

3.	Geben Sie auf dem Arbeitsblatt **Capacity Planner** die erforderlichen Informationen ein. Sie müssen alle Felder ausfüllen, die unten im Screenshot rot eingekreist sind.

	- Wählen Sie unter **Szenario auswählen** die Option **Hyper-V zu Azure** oder **VMware/Physisch zu Azure**.
	- Geben Sie unter **Durchschnittliche tägliche Änderungsrate (%)** die Informationen ein, die Sie mit dem [Tool für die Hyper-V-Kapazitätsplanung](site-recovery-capacity-planning-for-hyper-v-replication.md) oder der [Appliance für die vSphere-Kapazitätsplanung](https://labs.vmware.com/flings/vsphere-replication-capacity-planning-appliance) erfasst haben.  
	- **Komprimierung** gilt nur für die Komprimierung, die beim Replizieren von VMware-VMs oder physischen Servern zu Azure angeboten wird. Wir schätzen 30 % oder mehr, aber Sie können die Einstellung nach Bedarf ändern. Zum Replizieren von Hyper-V-VMs zur Azure-Komprimierung können Sie eine Drittanbieter-Appliance verwenden, z. B. Riverbed. 
	-  Geben Sie unter **Aufbewahrungseingaben** an, wie lange Replikate aufbewahrt werden sollen. Geben Sie beim Replizieren von VMware- oder physischen Servern den Wert in Tagen ein. Geben Sie die Dauer in Stunden ein, wenn Sie eine Hyper-V-Replikation durchführen.
	-  Geben Sie unter **Anzahl der Stunden für den Abschluss der anfänglichen Replikation für den Batch virtueller Maschinen** und **Anzahl virtueller Maschinen pro anfänglichem Replikationsbatch** die Einstellungen ein, die zum Berechnen der Anforderungen für die erste Replikation verwendet werden. Bei der Bereitstellung von Site Recovery sollte das gesamte Anfangsdataset hochgeladen werden. 

	![Eingaben](./media/site-recovery-capacity-planner/inputs.png)

2.	Nachdem Sie die Werte für die Quellumgebung eingegeben haben, enthält die angezeigte Ausgabe Folgendes:

	- **Erforderliche Bandbreite für die Deltareplikation** (MB/s) Die Netzwerkbandbreite für die Deltareplikation wird basierend auf der durchschnittlichen täglichen Datenänderungsrate berechnet.
	- **Erforderliche Bandbreite für die erste Replikation** (MB/s) Die Netzwerkbandbreite für die erste Replikation wird basierend auf den Werten für die erste Replikation berechnet, die Sie eingeben. 
	- **Erforderlicher Speicher (in GB)**: Gibt den insgesamt erforderlichen Azure-Speicher an.
	- **Gesamt-IOPS für Standardspeicherkonten**: Wird basierend auf der Einheitengröße 8K IOPS für alle Standardspeicherkonten berechnet. Für den Quick Planner wird die Anzahl basierend auf allen VM-Quelldatenträgern und der täglichen Änderungsrate berechnet. Für den Detailed Planner wird die Zahl basierend auf der Gesamtzahl der VMs, die standardmäßigen Azure-VMs zugeordnet sind, und der Datenänderungsrate auf diesen VMs berechnet. 
	- **Anzahl von Standard-Speicherkonten**: Gibt die Gesamtzahl von Standard-Speicherkonten an, die zum Schützen der VMs benötigt werden. Beachten Sie, dass ein Standard-Speicherkonto bis zu 20.000 IOPS über alle VMs in einem Standardspeicher hinweg mit einer maximalen Unterstützung von 500 IOPS pro Datenträger leisten kann. 
	- **Anzahl erforderlicher Blobdatenträger**: Gibt die Anzahl von Datenträgern an, die im Azure-Speicher erstellt werden.
	- **Anzahl erforderlicher Storage Premium-Konten**: Gibt die Gesamtzahl von Storage Premium-Konten an, die zum Schützen der VMs benötigt werden. Beachten Sie, dass für eine Quell-VM mit hohem IOPS-Wert (höher als 20.000) ein Premium-Speicherkonto erforderlich ist. Ein Premium-Speicherkonto kann bis zu 80.000 IOPS leisten.
	- **Gesamt-IOPS für Storage Premium**: Wird basierend auf der Einheitengröße 256K IOPS für alle Storage Premium-Konten berechnet. Für den Quick Planner wird die Anzahl basierend auf allen VM-Quelldatenträgern und der täglichen Änderungsrate berechnet. Für den Detailed Planner wird die Zahl basierend auf der Gesamtzahl der VMs, die Azure-Premium-VMs (DS- und GS-Reihe) zugeordnet sind, und der Datenänderungsrate auf diesen VMs berechnet. 
	- **Anzahl der erforderlichen Konfigurationsserver** zeigt, wie viele Konfigurationsserver für die Bereitstellung (1) erforderlich sind.
	- **Anzahl der erforderlichen zusätzlichen Prozessserver** zeigt an, ob weitere Prozessserver außer dem Prozessserver erforderlich sind, der standardmäßig auf dem Konfigurationsserver konfiguriert ist.
	- **100 % zusätzlicher Speicher für die Quelle** zeigt an, ob am Quellspeicherort zusätzlicher Speicher erforderlich ist.
			
	![Ausgabe](./media/site-recovery-capacity-planner/output.png)
 
## Ausführen des Detailed Planner-Tools


1.	Laden Sie das Tool [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) herunter, und öffnen Sie es. Sie müssen Makros ausführen. Aktivieren Sie also die Bearbeitung und den Inhalt, wenn Sie dazu aufgefordert werden. 
2.	Wählen Sie unter **Planertyp auswählen** im Listenfeld die Option **Detailed Planner** aus.

	![Erste Schritte](./media/site-recovery-capacity-planner/getting-started-2.png)

3.	Geben Sie im Arbeitsblatt **Workload-Qualifikation** die erforderlichen Informationen ein. Sie müssen alle markierten Felder ausfüllen.

	- Geben Sie unter **Prozessorkerne** die Gesamtzahl der Kerne auf einem Quellserver an.
	- Geben Sie unter **Speicherbelegung in MB** die Größe des RAM eines Quellservers an. 
	- Geben Sie unter **Anzahl der Netzwerkkarten** die Anzahl von Netzwerkadaptern auf einem Quellserver an. 
	-  Geben Sie unter **Gesamter Speicher (in GB)** die Gesamtgröße des VM-Speichers an. Wenn der Quellserver beispielsweise über drei Datenträger mit jeweils 500 GB verfügt, beträgt die Gesamtgröße 1.500 GB.
	-  Geben Sie unter **Anzahl der angefügten Datenträger** die Gesamtzahl der Datenträger eines Quellservers an.
	-  Geben Sie unter **Auslastung der Datenträgerkapazität** die durchschnittliche Auslastung an.
	-  Geben Sie unter **Tägliche Änderungsrate (%)** die tägliche Änderungsrate eines Quellservers an.
	-  Geben Sie unter **Azure-Größe für Zuordnung** jeweils die Größe der Azure-VM ein, die Sie zuordnen möchten. Wenn Sie dies nicht manuell durchführen möchten, klicken Sie auf **IaaS-VMs berechnen**. Beachten Sie: Wenn Sie eine manuelle Einstellung eingeben und dann auf „IaaS-VMs berechnen“ klicken, könnte die manuelle Einstellung überschrieben werden, da der Berechnungsprozesses automatisch die beste Übereinstimmung mit der Azure-VM-Größe erkennt.

	![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification.png)

4.	Wenn Sie auf **IaaS-VMs berechnen** klicken, wird Folgendes durchgeführt:

	- Die obligatorischen Eingaben werden überprüft.
	- Der IOPS-Wert wird berechnet, und es wird die beste Azure-VM-Größenübereinstimmung für jede VM ermittelt, die für die Replikation zu Azure geeignet ist. Falls keine passende Größe einer Azure-VM erkannt werden kann, wird ein Fehler ausgegeben. Wenn die Anzahl der angefügten Datenträger beispielsweise 65 beträgt, wird ein Fehler ausgegeben, weil der höchste zulässige Wert für die Azure-VM-Größe „64“ beträgt.
	- Es wird ein Speicherkonto vorgeschlagen, das für eine Azure-VM verwendet werden kann.
	- Es wird berechnet, wie viele Standard-Speicherkonten und wie viele Premium-Speicherkonten für die Workload insgesamt erforderlich sind. Scrollen Sie rechts nach unten, um den Azure-Speichertyp und das Speicherkonto anzuzeigen, das für einen Quellserver verwendet werden kann.
	- Der Rest der Tabelle wird basierend auf dem erforderlichen Speichertyp (Standard oder Premium), der einer VM zugewiesen ist, und der Anzahl von Datenträgern ausgefüllt und sortiert. Für alle VMs, die die Anforderungen für die Sicherung unter Azure erfüllen, wird in Spalte A (VM qualifiziert?) „Ja“ angezeigt. Wenn eine VM nicht unter Azure gesichert werden kann, wird ein Fehler angezeigt.

Die Spalten AA bis AE werden ausgegeben und enthalten Informationen für jeden virtuellen Computer.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-2.png)


### Beispiel
Beispielsweise berechnet das Tool für sechs VMs mit den in der Tabelle angezeigten Werten die beste Azure-VM-Übereinstimmung und die Azure-Speicheranforderungen und weist sie zu.

![Workload Qualification](./media/site-recovery-capacity-planner/workload-qualification-3.png)

- Beachten Sie in der Ausgabe für das Beispiel Folgendes:
	
	- Die erste Spalte enthält die Validierung für die VMS, die Datenträger und die Wechselrate.
	- Zwei Standard-Speicherkonten und ein Premium-Speicherkonto werden für fünf VMs benötigt. 
	-  VM3 erfüllt die Qualifikation für den Schutz nicht, da mindestens ein Datenträger größer als 1 TB ist.
	-  Für VM1 und VM2 kann das erste Standard-Speicherkonto verwendet werden.
	-  Für VM4 kann das zweite Standard-Speicherkonto verwendet werden.
	-  Für VM5 und VM6 ist ein Premium-Speicherkonto erforderlich, und beide können ein einzelnes Konto verwenden.

	>[AZURE.NOTE]  Der IOPS-Wert für Standardspeicher und Storage Premium wird auf VM-Ebene und nicht auf Datenträgerebene berechnet. Ein standardmäßiger virtueller Computer kann bis zu 500 IOPS pro Datenträger verarbeiten. Wenn der IOPS-Wert für einen Datenträger größer als 500 ist, benötigen Sie Storage Premium. Wenn jedoch der IOPS-Wert für einen Datenträger über 500 liegt, aber der IOPS-Wert für sämtliche VM-Datenträger innerhalb der standardmäßigen Unterstützungsgrenzen (VM-Größe, Anzahl der Datenträger, Anzahl der Adapter, CPU, Arbeitsspeicher) für Azure-VMs liegt, wählt der Planer statt der DS- oder GS-Reihe eine Standard-VM aus. Sie müssen die Zuordnung der Azure-Größenzelle manuell mit einer geeigneten VM aus der DS- oder GS-Reihe aktualisieren.

5. Klicken Sie nach dem Einrichten aller Details auf **Daten an das Planungstool senden**, um den **Capacity Planner** zu öffnen. Die Workloads sind hervorgehoben, um anzuzeigen, ob sie für den Schutz geeignet sind.


### Senden von Daten im Capacity Planner

1.	Beim Öffnen wird das Arbeitsblatt **Capacity Planner** basierend auf den Einstellungen, die Sie angegeben haben, gefüllt. In der Zelle **Infrastruktureingabenquelle** wird „Workload“ angegeben, um das Arbeitsblatt **Workload-Qualifikation** anzuzeigen. 
2.	Änderungen müssen Sie ggf. im Arbeitsblatt **Workload-Qualifikation** vornehmen und erneut auf „Daten an das Planungstool senden“ klicken.  

	![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

<!---HONumber=AcomDC_0224_2016-->