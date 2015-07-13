<properties 
	pageTitle="Geplante Wartung für virtuelle Azure-Computer" 
	description="Verstehen Sie, was eine geplante Azure-Wartung ist und wie sie sich auf Ihre virtuellen Computer auswirkt, die in Azure ausgeführt werden." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="kenazk" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-multiple" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="kenazk"/>


# Geplante Wartung für virtuelle Azure-Computer

## Gründe für das Ausführen einer geplanten Wartung durch Azure
<p> Microsoft Azure führt regelmäßig weltweit Updates aus, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur zu verbessern, die virtuellen Computern unterliegt. Viele dieser Updates werden ohne Beeinträchtigung der virtuellen Computer oder Clouddienste ausgeführt. Dazu zählen auch speichererhaltende Updates. Bei bestimmten Updates ist allerdings ein Neustart des virtuellen Computers erforderlich, damit die erforderlichen Aktualisierungen auf die Infrastruktur angewendet werden können. Der virtuelle Computer wird heruntergefahren, während das Patch für die Infrastruktur vorgenommen wird. Danach wird der virtuelle Computer neu gestartet. Beachten Sie, dass es zwei Wartungsarten gibt, die die Verfügbarkeit Ihres virtuellen Computers beeinträchtigen können: geplante und ungeplante. Diese Seite beschreibt, wie Microsoft Azure die geplante Wartung ausführt. Weitere Informationen über ungeplante Wartung finden Sie unter [Unterschied zwischen geplanter und ungeplanter Wartung].

## Speichererhaltende Updates
Bei einer Klasse von Updates in Microsoft Azure sind für die Kunden keinerlei Auswirkungen auf die ausgeführten virtuellen Computer spürbar. Viele dieser Updates betreffen Komponenten oder Dienste, die ohne Beeinträchtigung der ausgeführten Instanz aktualisiert werden können. Bei einigen dieser Updates handelt es sich um Plattforminfrastrukturaktualisierungen für das Hostbetriebssystem, die ohne vollständigen Neustart des virtuellen Computers angewendet werden können. Diese Updates werden mithilfe einer vom Azure-Team entwickelten Technologie durchgeführt, die eine Livemigration (also ein speichererhaltendes Update) ermöglicht. Bei der Aktualisierung wird der virtuelle Computer in einen angehaltenen Zustand versetzt. Dadurch bleibt der Speicher im Arbeitsspeicher erhalten, während das zugrunde liegende Hostbetriebssystem die erforderlichen Updates und Patches erhält. Nach spätestens 30 Sekunden wird der virtuelle Computer fortgesetzt. Die Uhr des fortgesetzten virtuellen Computers wird automatisch synchronisiert.

Nicht alle Updates können mit diesem Mechanismus bereitgestellt. Dank der Kürze der Anhalteperiode verringert diese Art der Updatebereitstellung jedoch deutlich die Auswirkungen auf virtuelle Computer. Bei virtuellen Computern mit mehreren Instanzen (in einer Verfügbarkeitsgruppe) werden die Updates nach und nach auf die einzelnen Updatedomänen (UDs) angewendet.

## Konfigurationen des virtuellen Computers
Es gibt zwei Arten der Konfiguration virtueller Computer: mehrere Instanzen und eine Instanz. Virtuelle Computer mit mehreren Instanzen werden konfiguriert, indem identische virtuelle Computer in eine Verfügbarkeitsgruppe platziert werden. Die Konfiguration mit mehreren Instanzen sorgt für Redundanz und wird empfohlen, um die Verfügbarkeit Ihrer Anwendung sicherzustellen. Alle virtuellen Computer in der Verfügbarkeitsgruppe sollten nahezu identisch sein und denselben Zweck für Ihre Anwendung erfüllen. Weitere Informationen über das Konfigurieren Ihrer virtuellen Computer für hohe Verfügbarkeit finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">Verwalten der Verfügbarkeit virtueller Computer</a>.

Im Gegensatz dazu sind virtuelle Computer mit einer einzelnen Instanz eigenständige virtuelle Computer, die nicht in einer Verfügbarkeitsgruppe platziert sind. Virtuelle Computer mit einer Instanz an sich fallen nicht unter die Vereinbarung zum Servicelevel (SLA), denn hier ist es erforderlich, dass zwei oder mehr virtuelle Computer im selben Verfügbarkeitssatz bereitgestellt werden. Weitere Informationen zur SLA finden Sie im Abschnitt „Cloud-Dienste, virtuelle Computer und das virtuelle Netzwerk“ von [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/).


## Aktualisierung mehrerer Instanzen
Während der Wartung aktualisiert die Azure-Plattform zunächst den Satz an Host-Computern, der die virtuellen Computer in einer Mehrfachinstanzkonfiguration hostet, was zum Neustart dieser virtuellen Computer führt. Virtuelle Computer in einer Mehrfachinstanzkonfiguration werden so aktualisiert, dass die Verfügbarkeit während des Prozesses gegeben ist. Dabei wird davon ausgegangen, dass jeder Computer die gleiche Funktion hat wie die anderen im Satz. Jeder virtuelle Computer im Verfügbarkeitssatz wird einer Aktualisierungs- (Update Domain, UD) und Ausfalldomäne (Fault Domain, FD) der zugrunde liegenden Azure-Plattform zugewiesen. Jede UD ist eine Gruppe virtueller Computer, die im selben Zeitfenster neu gestartet wird. Jede Fehlerdomäne ist eine Gruppe virtueller Computer, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen.

Weitere Informationen über Aktualisierungs- und Fehlerdomänen finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz</a>.

Microsoft Azure garantiert, dass kein ungeplantes Wartungsereignis dazu führt, dass virtuelle Computer von zwei unterschiedlichen Aktualisierungsdomänen gleichzeitig ausfallen. Die Wartung erfolgt durch Herunterfahren jedes virtuellen Computers, Anwenden der Aktualisierung auf die Host-Computer, Neustart des virtuellen Computers und Fortschreiten mit der nächsten UD. Die geplanten Wartungsarbeiten enden, wenn alle UDs aktualisiert wurden. Die UDs werden im Rahmen der geplanten Wartung möglicherweise nicht der Reihe nach neu gestartet, sondern es wird jeweils nur eine UD neu gestartet. Aktuell wird bei Azure 48 Stunden vorher über geplante Wartungsarbeiten für virtuelle Computer in einer Mehrfachinstanzkonfiguration informiert.

Im Folgenden finden Sie ein Beispiel, was Ihre Windows-Ereignisanzeige möglicherweise anzeigt, nachdem der virtuelle Computer wiederhergestellt wurde:

<!--Image reference-->
![][image2]

## Einzelinstanzenaktualisierung
Nachdem die Mehrinstanzaktualisierungen abgeschlossen sind, führt Azure die Aktualisierung an einem Satz von Host-Computern durch, die virtuelle Computer mit nur einer Instanz hosten. Diese Aktualisierung führt ebenfalls zum Neustart der virtuellen Computer, die nicht in Verfügbarkeitssätzen laufen. Beachten Sie bitte, dass die Azure-Plattform bei nur einer Instanz in einem Verfügbarkeitssatz diese doch als Mehrfachinstanz behandelt. Bei virtuellen Computern in einer Konfiguration mit nur einer Instanz werden diese ausgeschaltet, dann die Aktualisierung auf dem Host-Computer eingespielt und anschließend der virtuelle Computer neu gestartet. Diese Aktualisierungen werden auf allen virtuellen Computern in einer Region in einem einzigen Wartungsfenster ausgeführt. Der geplante Wartungsvorgang wirkt sich bei dieser Art von Konfiguration eines virtuellen Computers auf die Verfügbarkeit aus. Aktuell wird bei Azure eine Woche vorher über geplante Wartungsarbeiten für virtuelle Computer in einer Einzelinstanzkonfiguration informiert.
 
### E-Mail-Benachrichtigung
Bei virtuellen Computern mit Einzel- und Mehrfachinstanzen informiert Azure im Voraus über geplante Wartungsarbeiten. Bei Einzelinstanzen erfolgt die Benachrichtigung eine Woche im Voraus, bei Mehrfachinstanzen 48 Stunden im Voraus. Diese E-Mail wird an das Haupt-E-Mail-Konto gesandt, das im Abonnement angegeben ist. Ein Beispiel dieses E-Mail-Typs finden Sie unten:

<!--Image reference-->
![][image1]

## Regionspaare
Azure organisiert eine Gruppe von Regionspaaren und gewährleistet, dass geplante Wartungsarbeiten für Einzelinstanz-Updates jeweils nur in einer Region durchgeführt werden. Azure führt während einer geplanten Wartung für virtuelle Computer mit einer Einzelinstanz nicht gleichzeitig ein Update in beiden Regionen eines Paares durch. Informationen zu aktuellen Regionspaaren finden Sie in der folgenden Tabelle:

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
Australien (Südost) | Australien (Ost)

Beispiel: Während einer Bereitstellung im Rahmen einer geplanten Wartung stellt Azure "USA (West)" kein Update bereit, während "USA (Ost)" gewartet wird. Andere Regionen wie Nordeuropa können jedoch gleichzeitig mit USA (Ost) gewartet werden.

<!--Anchors-->
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
[image3]: ./media/virtual-machines-planned-maintenance/RegionPairs.PNG


<!--Link references-->
[Virtual Machines Manage Availability]: virtual-machines-windows-tutorial.md
[Unterschied zwischen geplanter und ungeplanter Wartung]: virtual-machines-manage-availability.md#Understand-planned-versus-unplanned-maintenance/
 

<!---HONumber=July15_HO1-->