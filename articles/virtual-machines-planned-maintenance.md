<properties title="Planned maintenance for Azure virtual machines" pageTitle="Planned maintenance for Azure virtual machines" description="Understand what Azure planned maintenance is and how it affects your virtual machines running in Azure." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kenazk" videoId="" scriptId="" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kenazk"></tags>

# Geplante Wartung für virtuelle Azure-Computer

## Warum Azure geplante Wartung durchführt

Microsoft Azure führt regelmäßig weltweite Aktualisierungen durch, um Zuverlässigkeit, Leistung und Sicherheit der gehosteten Infrastruktur zu verbessern, auf denen die virtuellen Computer betrieben werden. Viele dieser Aktualisierungen haben keine Auswirkung auf virtuelle Computer oder Cloud-Dienste. Manche allerdings machen einen Neustart Ihres virtuellen Computers nötig, damit die erforderlichen Aktualisierungen auf die Infrastruktur angewandt werden können. Der virtuelle Computer wird ausgeschaltet ,während wir die Infrastruktur patchen und anschließend neu gestartet. Beachten Sie bitte, dass es zwei Arten von Wartung gibt, die sich auf die Verfügbarkeit Ihres virtuellen Computers auswirkt: geplant und ungeplant. Diese Seite beschreibt, wie Microsoft Azure geplante Wartungsarbeiten durchführt. Weitere Informationen über ungeplante Wartung finden Sie unter [Unterschied zwischen geplanter und ungeplanter Wartung][Unterschied zwischen geplanter und ungeplanter Wartung].

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

-   [Konfiguration virtueller Computer][Konfiguration virtueller Computer]
-   [Aktualisierung mehrerer Instanzen][Aktualisierung mehrerer Instanzen]
-   [Aktualisierung einzelner Instanzen][Aktualisierung einzelner Instanzen]
-   [E-Mail-Benachrichtigung][E-Mail-Benachrichtigung]

## Konfiguration virtueller Computer

Es existieren zwei Konfigurationstypen virtueller Computer: mehrere Instanzen oder einzelne Instanz. Virtuelle Computer mit mehreren Instanzen werden durch Platzieren identischer virtueller Computer in einen Verfügbarkeitssatz konfiguriert. Die Konfiguration mit mehreren Instanzen sorgt für Redundanz und wird empfohlen, um die Verfügbarkeit Ihrer Anwendung sicherzustellen. Alle virtuellen Computer im Verfügbarkeitssatz sollten weitgehend identisch sein und für denselben Zweck Ihrer Anwendung dienen. Weitere Informationen zur Konfiguration virtueller Computer für Hochverfügbarkeit finden Sie unter „[Verfügbarkeit virtueller Computer verwalten][Verfügbarkeit virtueller Computer verwalten]“.

Virtuelle Computer mit nur einer Instanz dagegen sind eigenständige virtuelle Computer, die sich nicht in einem Verfügbarkeitssatz befinden. Virtuelle Computer mit einer Instanz an sich fallen nicht unter die Vereinbarung zum Servicelevel (SLA), denn hier ist es erforderlich, dass zwei oder mehr virtuelle Computer im selben Verfügbarkeitssatz bereitgestellt werden. Weitere Informationen zur SLA finden Sie im Abschnitt „Cloud-Dienste, virtuelle Computer und das virtuelle Netzwerk“ von [Vereinbarungen zum Servicelevel][Vereinbarungen zum Servicelevel].

## Aktualisierung mehrerer Instanzen

Während der Wartung aktualisiert die Azure-Plattform zunächst den Satz an Host-Computern, der die virtuellen Computer in einer Mehrfachinstanzkonfiguration hostet, was zum Neustart dieser virtuellen Computer führt. Virtuelle Computer in einer Mehrfachinstanzkonfiguration werden so aktualisiert, dass die Verfügbarkeit während des Prozess gegeben ist. Dabei wird davon ausgegangen, dass jeder Computer die gleiche Funktion hat wie die anderen im Satz. Jeder virtuelle Computer im Verfügbarkeitssatz wird einer Aktualisierungs- (Update Domain, UD) und Ausfalldomäne (Fault Domain, FD) der zugrunde liegenden Azure-Plattform zugewiesen. Jede UD ist eine Gruppe virtueller Computer, die im selben Zeitfenster neu gestartet wird. Jede FD ist eine Gruppe virtueller Computer, die dieselbe Energiequelle und den Netzwerk-Switch teilen.

Weitere Informationen zu UDs und FDs finden Sie unter „[Redundanz durch Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe][Redundanz durch Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe]“.

Microsoft Azure garantiert, dass geplante Wartungsarbeiten nicht dazu führen, dass virtuelle Computer aus zwei unterschiedlichen UDs gleichzeitig offline gehen. Die Wartung erfolgt durch Herunterfahren jedes virtuellen Computers, Anwenden der Aktualisierung auf die Host-Computer, Neustart des virtuellen Computers und Fortschreiten mit der nächsten UD. Die geplanten Wartungsarbeiten enden, wenn alle UDs aktualisiert wurden. Die UDs werden im Rahmen der geplanten Wartung möglicherweise nicht der Reihe nach neu gestartet, sondern es wird jeweils nur eine UD neu gestartet. Aktuell gibt es keine Vorab-Benachrichtigung über geplante Wartungsarbeiten für virtuelle Computer in einer Mehrfachinstanzkonfiguration.

Nachdem ein virtueller Computer wiederhergestellt wurde, kann ein Windows Event Viewer angezeigt werden, der ungefähr so aussieht:

<!--Image reference-->

![][]

## Aktualisierung einzelner Instanzen

Nachdem die Mehrinsanzaktualisierungen abgeschlossen sind, führt Azure die Aktualisierung an einem Satz von Host-Computern durch, die virtuelle Computer mit nur einer Instanz hosten. Diese Aktualisierung führt ebenfalls zum Neustart der virtuellen Computer, die nicht in Verfügbarkeitssätzen laufen. Beachten Sie bitte, dass die Azure-Plattform bei nur einer Instanz in einem Verfügbarkeitssatz diese doch als Mehrfachinstanz behandelt. Bei virtuellen Computern in einer Konfiguration mit nur einer Instanz werden diese ausgeschaltet, dann die Aktualisierung auf dem Host-Computer eingespielt und anschließend der virtuelle Computer neu gestartet. Diese Aktualisierungen werden auf allen virtuellen Computern in einer Region in einem einzigen Wartungsfenster ausgeführt. Der geplante Wartungsvorgang wirkt sich bei dieser Art von Konfiguration eines virtuellen Computers auf die Verfügbarkeit aus.

### E-Mail-Benachrichtigung

Für die als nur eine Instanz konfigurierten virtuellen Computer sendet Azure mindestens eine Woche im Voraus eine E-Mail-Benachrichtigung, sodass Sie über anstehende Wartungsarbeiten informiert sind. Diese E-Mail wird an das Hautp-E-Mail-Konto gesandt, das im Abonnement angegeben ist. Ein Beispiel für diese Art von E-Mail sehen Sie unten:

<!--Image reference-->

![][1]

<!--Anchors--> <!--Link references-->

  [Unterschied zwischen geplanter und ungeplanter Wartung]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/
  [Konfiguration virtueller Computer]: #virtual-machine-configurations
  [Aktualisierung mehrerer Instanzen]: #multi-instance-update
  [Aktualisierung einzelner Instanzen]: #single-instance-update
  [E-Mail-Benachrichtigung]: #email-notification
  [Verfügbarkeit virtueller Computer verwalten]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-manage-availability/
  [Vereinbarungen zum Servicelevel]: ../../support/legal/sla/
  [Redundanz durch Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
  []: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
  [1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
