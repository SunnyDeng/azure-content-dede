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
	ms.date="04/08/2015" 
	ms.author="kenazk"/>


# Geplante Wartung für virtuelle Azure-Computer

## Gründe für das Ausführen einer geplanten Wartung durch Azure
<p> Microsoft Azure führt regelmäßig weltweit Updates aus, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur zu verbessern, die virtuellen Computern unterliegt. Viele dieser Updates werden ohne Beeinträchtigung der virtuellen Computer oder Cloud-Dienste ausgeführt. Manche allerdings machen einen Neustart Ihres virtuellen Computers nötig, damit die erforderlichen Aktualisierungen auf die Infrastruktur angewandt werden können. Der virtuelle Computer wird heruntergefahren, während das Patch für die Infrastruktur vorgenommen wird. Danach wird der virtuelle Computer neu gestartet. Beachten Sie, dass es zwei Wartungsarten gibt, die die Verfügbarkeit Ihres virtuellen Computers beeinträchtigen können: geplante und ungeplante. Diese Seite beschreibt, wie Microsoft Azure die geplante Wartung ausführt. Weitere Informationen über ungeplante Wartung finden Sie unter [Unterschied zwischen geplanter und ungeplanter Wartung].

## Konfigurationen des virtuellen Computers
Es gibt zwei Arten der Konfiguration virtueller Computer: mehrere Instanzen und eine Instanz. Virtuelle Computer mit mehreren Instanzen werden konfiguriert, indem identische virtuelle Computer in eine Verfügbarkeitsgruppe platziert werden. Die Konfiguration mit mehreren Instanzen sorgt für Redundanz und wird empfohlen, um die Verfügbarkeit Ihrer Anwendung sicherzustellen. Alle virtuellen Computer in der Verfügbarkeitsgruppe sollten nahezu identisch sein und denselben Zweck für Ihre Anwendung erfüllen. Weitere Informationen über das Konfigurieren Ihrer virtuellen Computer für hohe Verfügbarkeit finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">Verwalten der Verfügbarkeit virtueller Computer</a>.

Im Gegensatz dazu sind virtuelle Computer mit einer einzelnen Instanz eigenständige virtuelle Computer, die nicht in einer Verfügbarkeitsgruppe platziert sind. Virtuelle Computer mit einer Instanz an sich fallen nicht unter die Vereinbarung zum Servicelevel (SLA), denn hier ist es erforderlich, dass zwei oder mehr virtuelle Computer im selben Verfügbarkeitssatz bereitgestellt werden. Weitere Informationen zur SLA finden Sie im Abschnitt „Cloud-Dienste, virtuelle Computer und das virtuelle Netzwerk“ von [Vereinbarungen zum Servicelevel](http://azure.microsoft.com/support/legal/sla/).


## Aktualisierung mehrerer Instanzen
Während der Wartung aktualisiert die Azure-Plattform zunächst den Satz an Host-Computern, der die virtuellen Computer in einer Mehrfachinstanzkonfiguration hostet, was zum Neustart dieser virtuellen Computer führt. Virtuelle Computer in einer Mehrfachinstanzkonfiguration werden so aktualisiert, dass die Verfügbarkeit während des Prozesses gegeben ist. Dabei wird davon ausgegangen, dass jeder Computer die gleiche Funktion hat wie die anderen im Satz. Jeder virtuelle Computer im Verfügbarkeitssatz wird einer Aktualisierungs- (Update Domain, UD) und Ausfalldomäne (Fault Domain, FD) der zugrunde liegenden Azure-Plattform zugewiesen. Jede UD ist eine Gruppe virtueller Computer, die im selben Zeitfenster neu gestartet wird. Jede Fehlerdomäne ist eine Gruppe virtueller Computer, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen.

Weitere Informationen über Aktualisierungs- und Fehlerdomänen finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz</a>.

Microsoft Azure garantiert, dass kein ungeplantes Wartungsereignis dazu führt, dass virtuelle Computer von zwei unterschiedlichen Aktualisierungsdomänen gleichzeitig ausfallen. Die Wartung erfolgt durch Herunterfahren jedes virtuellen Computers, Anwenden der Aktualisierung auf die Host-Computer, Neustart des virtuellen Computers und Fortschreiten mit der nächsten UD. Die geplanten Wartungsarbeiten enden, wenn alle UDs aktualisiert wurden. Die UDs werden im Rahmen der geplanten Wartung möglicherweise nicht der Reihe nach neu gestartet, sondern es wird jeweils nur eine UD neu gestartet. Aktuell gibt es keine Vorab-Benachrichtigung über geplante Wartungsarbeiten für virtuelle Computer in einer Mehrfachinstanzkonfiguration.

Im Folgenden finden Sie ein Beispiel, was Ihre Windows-Ereignisanzeige möglicherweise anzeigt, nachdem der virtuelle Computer wiederhergestellt wurde:

<!--Image reference-->
![][image2]

## Einzelinstanzenaktualisierung
Nachdem die Mehrinsanzaktualisierungen abgeschlossen sind, führt Azure die Aktualisierung an einem Satz von Host-Computern durch, die virtuelle Computer mit nur einer Instanz hosten. Diese Aktualisierung führt ebenfalls zum Neustart der virtuellen Computer, die nicht in Verfügbarkeitssätzen laufen. Beachten Sie bitte, dass die Azure-Plattform bei nur einer Instanz in einem Verfügbarkeitssatz diese doch als Mehrfachinstanz behandelt. Bei virtuellen Computern in einer Konfiguration mit nur einer Instanz werden diese ausgeschaltet, dann die Aktualisierung auf dem Host-Computer eingespielt und anschließend der virtuelle Computer neu gestartet. Diese Aktualisierungen werden auf allen virtuellen Computern in einer Region in einem einzigen Wartungsfenster ausgeführt. Der geplante Wartungsvorgang wirkt sich bei dieser Art von Konfiguration eines virtuellen Computers auf die Verfügbarkeit aus.
 
### E-Mail-Benachrichtigung
Für die als nur eine Instanz konfigurierten virtuellen Computer sendet Azure mindestens eine Woche im Voraus eine E-Mail-Benachrichtigung, sodass Sie über anstehende Wartungsarbeiten informiert sind. Diese E-Mail wird an das Haupt-E-Mail-Konto gesandt, das im Abonnement angegeben ist. Ein Beispiel dieses E-Mail-Typs finden Sie unten:

<!--Image reference-->
![][image1]

## Regionspaare
Azure organisiert eine Gruppe von Regionspaaren und gewährleistet, dass geplante Wartungsarbeiten jeweils nur an einer Region durchgeführt werden. Azure führt während einer geplanten Wartung nicht an beiden Regionen eines Paares gleichzeitig ein Update durch. Informationen zu aktuellen Regionspaaren finden Sie in der folgenden Tabelle:

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

<!---HONumber=58-->