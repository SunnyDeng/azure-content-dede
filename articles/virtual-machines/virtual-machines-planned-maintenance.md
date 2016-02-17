<properties
	pageTitle="Geplante Wartung für virtuelle Azure-Computer | Microsoft Azure"
	description="Verstehen Sie, was eine geplante Azure-Wartung ist und wie sie sich auf Ihre virtuellen Computer auswirkt, die in Azure ausgeführt werden."
	services="virtual-machines"
	documentationCenter=""
	authors="drewm"
	manager="timlt"
	editor=""
	tags="azure-service-management,azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="drewm"/>


# Geplante Wartung für virtuelle Azure-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## Gründe für das Ausführen einer geplanten Wartung durch Azure

Microsoft Azure führt regelmäßig weltweit Updates aus, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur zu verbessern, die virtuellen Computern unterliegt. Viele dieser Updates werden ohne Beeinträchtigung der virtuellen Computer oder Clouddienste ausgeführt. Dazu zählen auch speichererhaltende Updates.

Bei bestimmten Updates ist allerdings ein Neustart der virtuellen Computer erforderlich, damit die erforderlichen Aktualisierungen auf die Infrastruktur angewendet werden können. Die virtuellen Computer werden heruntergefahren, während der Patch für die Infrastruktur vorgenommen wird. Danach werden die virtuellen Computer neu gestartet.

Beachten Sie, dass es zwei Wartungsarten gibt, die die Verfügbarkeit Ihrer virtuellen Computer beeinträchtigen können: geplante und ungeplante. Diese Seite beschreibt, wie Microsoft Azure die geplante Wartung ausführt. Weitere Informationen über ungeplante Wartung finden Sie unter [Unterschied zwischen geplanter und ungeplanter Wartung](virtual-machines-manage-availability.md).

## Speichererhaltende Updates

Bei einer Klasse von Updates in Microsoft Azure sind für die Kunden keinerlei Auswirkungen auf die ausgeführten virtuellen Computer spürbar. Viele dieser Updates betreffen Komponenten oder Dienste, die ohne Beeinträchtigung der ausgeführten Instanz aktualisiert werden können. Bei einigen dieser Updates handelt es sich um Plattforminfrastrukturaktualisierungen für das Hostbetriebssystem, die ohne vollständigen Neustart des virtuellen Computers angewendet werden können.

Diese Updates werden mit Techniken durchgeführt, die eine Live-Migration (eine "speichererhaltende" Aktualisierung) ermöglichen. Bei der Aktualisierung wird der virtuelle Computer in einen pausierten Zustand versetzt. Dadurch bleibt der Speicher im Arbeitsspeicher erhalten, während das zugrunde liegende Hostbetriebssystem die erforderlichen Updates und Patches erhält. Der virtuelle Computer wird innerhalb von 30 Sekunden Pause wieder fortgesetzt. Die Uhr des fortgesetzten virtuellen Computers wird automatisch synchronisiert.

Nicht alle Updates können mit diesem Mechanismus bereitgestellt werden. Dank der Kürze der Pausierung verringert diese Art der Updatebereitstellung jedoch deutlich die Auswirkungen auf virtuelle Computer.

Updates für mehrere Instanzen (für virtuelle Computer in einer Verfügbarkeitsgruppe) werden nach und nach auf die einzelnen Updatedomänen angewendet.

## Konfigurationen für virtuelle Computer

Es gibt zwei Arten der Konfiguration virtueller Computer: mehrere Instanzen und eine Instanz. In einer Konfiguration mit mehreren Instanzen werden ähnliche virtuelle Computer in eine Verfügbarkeitsgruppe platziert.

Die Konfiguration mit mehreren Instanzen sorgt für Redundanz über mehrere physische Computer hinweg, für Leistungskraft und für optimale Vernetzung. Sie wird empfohlen, um die Verfügbarkeit Ihrer Anwendung sicherzustellen. Alle virtuellen Computer in der Verfügbarkeitsgruppe sollten denselben Zweck für Ihre Anwendung erfüllen.

Weitere Informationen über das Konfigurieren Ihrer virtuellen Computer für hohe Verfügbarkeit finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer](virtual-machines-manage-availability.md).

Im Gegensatz dazu wird eine Einzelinstanz-Konfiguration für eigenständige virtuelle Computer verwendet, die nicht in einer Verfügbarkeitsgruppe platziert wurden. Diese virtuellen Computer fallen nicht unter die Vereinbarung zum Servicelevel (SLA), denn hier ist es erforderlich, dass mindestens zwei virtuelle Computer in derselben Verfügbarkeitsgruppe bereitgestellt werden.

Weitere Informationen zur SLA finden Sie im Abschnitt „Clouddienste, virtuelle Computer und virtuelles Netzwerk“ unter [Vereinbarungen zum Servicelevel (SLAs)](https://azure.microsoft.com/support/legal/sla/).


## Konfigurationsupdates bei mehreren Instanzen

Während der geplanten Wartung aktualisiert die Azure-Plattform zuerst den Satz von virtuellen Computern, die in einer Konfiguration mit mehreren Instanzen gehostet werden. Dabei wird ein Neustart dieser virtuellen Computer ausgeführt, was mit einer Downtime von etwa 15 Minuten verbunden ist.

In einer Konfiguration mit mehreren Instanzen werden die virtuellen Computer so aktualisiert, dass die Verfügbarkeit während des Prozesses gewährleistet bleibt. Dabei wird davon ausgegangen, dass jeder virtuelle Computer die gleiche Funktion hat wie die anderen im Satz.

Jeder virtuelle Computer in der Verfügbarkeitsgruppe wird einer Updatedomäne (UD) und einer Fehlerdomäne (FD) der zugrunde liegenden Azure-Plattform zugewiesen. Jede Updatedomäne ist eine Gruppe virtueller Computer, die im selben Zeitfenster neu gestartet wird. Jede Fehlerdomäne ist eine Gruppe virtueller Computer, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen.

Weitere Informationen zu Updatedomänen und Fehlerdomänen finden Sie unter [Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz](virtual-machines-manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).

Um zu verhindern, dass Updatedomänen zur gleichen Zeit offline gehen, erfolgt die Wartung, indem jeder virtuelle Computer in der betreffenden Updatedomäne heruntergefahren wird, das Update auf die Hostcomputer angewendet wird, die virtuellen Computer neu gestartet werden und dann mit der nächsten Updatedomäne fortgefahren wird. Der geplante Wartungsvorgang endet, nachdem alle Updatedomänen aktualisiert wurden.

Die Reihenfolge der Updatedomänen, die neu gestartet werden, muss bei der Wartung nicht unbedingt in Folge bearbeitet werden, es befindet sich jedoch nur jeweils eine Updatedomäne im Neustartvorgang. Aktuell wird bei Azure eine Woche vorher über geplante Wartungsarbeiten für virtuelle Computer in einer Konfiguration mit mehreren Instanzen informiert.

Nach der Wiederherstellung eines virtuellen Computers ist in der Windows-Ereignisanzeige beispielsweise folgender Inhalt zu sehen:

<!--Image reference-->
![][image2]

Ermitteln Sie mithilfe der Ereignisanzeige, welche virtuellen Maschinen (über das Azure-Portal, über Azure PowerShell oder über die Azure-Befehlszeilenschnittstelle) als Teil einer Konfiguration mit mehreren Instanzen konfiguriert wurden. Beispiel: Um zu ermitteln, welche virtuellen Maschinen sich in einer Konfiguration mit mehreren Instanzen befinden, können Sie die Liste der virtuellen Maschinen durchsuchen. Fügen Sie dazu die Spalte „Verfügbarkeitsgruppe“ zum Dialogfeld „Durchsuchen“ der virtuellen Maschinen hinzu. Im folgenden Beispiel befinden sich die virtuellen Maschinen „Example-VM1“ und „Example-VM2“ in einer Konfiguration mit mehreren Instanzen:

<!--Image reference-->
![][image4]

## Konfigurationsupdates bei einzelnen Instanzen

Nachdem die Konfigurationsupdates bei mehreren Instanzen abgeschlossen sind, führt Azure die Konfigurationsupdates für einzelne Instanzen durch. Durch diese Aktualisierung wird auch für virtuelle Computer ein Neustart ausgelöst, die nicht in Verfügbarkeitsgruppen laufen.

Bitte beachten Sie, dass wenn auch nur eine Instanz in einer Verfügbarkeitsgruppe ausgeführt wird, die Azure-Plattform die Aktualisierung bereits als eine Konfigurationsaktualisierung mit mehreren Instanzen behandelt.

Bei virtuellen Computern in einer Konfiguration mit nur einer Instanz werden diese heruntergefahren, anschließend wird das Update auf dem Host-Computer eingespielt, und dann werden die virtuellen Computer neu gestartet. Dies ist mit einer Downtime von etwa 15 Minuten verbunden. Diese Aktualisierungen werden auf allen virtuellen Computern in einer Region in einem einzigen Wartungsfenster ausgeführt.

Der geplante Wartungsvorgang wirkt sich bei dieser Art von Konfiguration eines virtuellen Computers auf die Verfügbarkeit aus. Aktuell wird bei Azure eine Woche vorher über geplante Wartungsarbeiten für virtuelle Computer in einer Einzelinstanzkonfiguration informiert.

### E-Mail-Benachrichtigung

Nur bei virtuellen Computern mit Konfigurationen mit einzelnen und mehreren Instanzen informiert Azure im Voraus per E-Mail über geplante Wartungsarbeiten (eine Woche im Voraus). Diese E-Mail wird an die E-Mail-Konten des Kontoadministrators und die des Co-Administrators gesendet, die im Abonnement bereitgestellt werden. Hier ist ein Beispiel für diese Art von E-Mail:

<!--Image reference-->
![][image1]

## Regionspaare

Beim Ausführen der Wartung aktualisiert Azure nur die VM-Instanzen in einer Region des jeweiligen Regionspaars. Wenn z. B. die virtuellen Computer (Virtual Machines, VMs) in der Region „USA, Norden-Mitte“ aktualisiert werden, aktualisiert Azure nicht gleichzeitig die virtuellen Computer in der Region „USA, Süden-Mitte“. Die Aktualisierung für diese Region wird zu einem anderen Zeitpunkt geplant. Dies ermöglicht ein Failover oder einen Lastenausgleich zwischen den Regionen. Andere Regionen wie Nordeuropa können jedoch gleichzeitig mit USA (Ost) gewartet werden.

Informationen zu aktuellen Regionspaaren finden Sie in der folgenden Tabelle:

Region 1 | Region 2
:----- | ------:
USA (Mitte/Norden) | USA (Mitte/Süden)
USA (Ost) | USA (West)
USA (Ost 2) | USA (Mitte)
Nordeuropa | Westeuropa
Südostasien | Ostasien
Ostchina | Nordchina
Japan Ost | Japan (Westen)
Brasilien Süd | USA (Mitte/Süden)
Australien (Südost) | Australien (Osten)
US Government, Iowa | US Government, Virginia

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG
[image4]: ./media/virtual-machines-planned-maintenance/AvailabilitySetExample.png


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Understand planned versus unplanned maintenance]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/

<!---HONumber=AcomDC_0204_2016-->