<properties title="Geplante Wartung f&uuml;r virtuelle Azure-Computer" pageTitle="Geplante Wartung f&uuml;r virtuelle Azure-Computer" description="Verstehen Sie, was eine geplante Azure-Wartung ist und wie sie sich auf Ihre virtuellen Computer auswirkt, die in Azure ausgef&uuml;hrt werden." metaKeywords="" services="virtual-machines" solutions="" documentationCenter="" authors="kenazk" videoId="" scriptId="" manager="timlt" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-multiple" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="kenazk" />

# Geplante Wartung für virtuelle Azure-Computer

## Gründe für das Ausführen einer geplanten Wartung durch Azure

Microsoft Azure führt regelmäßig weltweit Updates aus, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur zu verbessern, die virtuellen Computern unterliegt. Viele dieser Updates werden ohne Beeinträchtigung der virtuellen Computer oder Cloud-Dienste ausgeführt. Einige Updates erfordern jedoch einen Neustart Ihres virtuellen Computers, damit die erforderlichen Updates auf die Infrastruktur angewendet werden können. Der virtuelle Computer wird heruntergefahren, während das Patch für die Infrastruktur vorgenommen wird. Danach wird der virtuelle Computer neu gestartet. Beachten Sie, dass es zwei Wartungsarten gibt, die die Verfügbarkeit Ihres virtuellen Computers beeinträchtigen können: die geplante und die ungeplante. Diese Seite beschreibt, wie Microsoft Azure die geplante Wartung ausführt. Weitere Informationen über die ungeplante Wartung finden Sie unter [Unterschied zwischen geplanter und ungeplanter Wartung][Unterschied zwischen geplanter und ungeplanter Wartung].

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

-   [Konfigurationen des virtuellen Computers][Konfigurationen des virtuellen Computers]
-   [Aktualisieren mehrerer Instanzen][Aktualisieren mehrerer Instanzen]
-   [Aktualisieren einer einzelnen Instanz][Aktualisieren einer einzelnen Instanz]
-   [E-Mail-Benachrichtigung][E-Mail-Benachrichtigung]

## Konfigurationen des virtuellen Computers

Es gibt zwei Konfigurationsarten für virtuellen Computer: mit mehreren Instanzen und Einzelinstanz. Virtuelle Computer mit mehreren Instanzen werden konfiguriert, indem identische virtuelle Computer in eine Verfügbarkeitsgruppe platziert werden. Die Konfiguration für mehrere Instanzen bietet Redundanz und wird zum Sicherstellen der Verfügbarkeit Ihrer Anwendung empfohlen. Alle virtuellen Computer in der Verfügbarkeitsgruppe sollten nahezu identisch sein und denselben Zweck für Ihre Anwendung erfüllen. Weitere Informationen über das Konfigurieren Ihres virtuellen Computers für die hohe Verfügbarkeit finden Sie unter [Verwalten der Verfügbarkeit virtueller Computer][Verwalten der Verfügbarkeit virtueller Computer].

Im Gegensatz dazu sind virtuelle Computer mit einer einzelnen Instanz eigenständige virtuelle Computer, die nicht in einer Verfügbarkeitsgruppe platziert sind. Allein qualifizieren sich virtuelle Computer mit einer einzelnen Instanz nicht für die Vereinbarung zum Servicelevel (Service Level Agreement, SLA), wofür mindestens zwei virtuelle Computer erforderlich sind, die unter derselben Verfügbarkeitsgruppe bereitgestellt sind. Weitere Informationen über das SLA finden Sie im Abschnitt „Cloud-Dienste, virtuelle Computer und das virtuelle Netzwerk“ der [Vereinbarungen zum Servicelevel (SLAs)][Vereinbarungen zum Servicelevel (SLAs)].

## Aktualisieren mehrerer Instanzen

Während der geplanten Wartung aktualisiert die Azure-Plattform zunächst die Gruppe an Hostcomputern, die die Gruppe von virtuellen Computern in einer Konfiguration für mehrere Instanzen hosten, wodurch diese virtuellen Computer neu gestartet werden. Bei virtuellen Computern in einer Konfiguration für mehrere Instanzen werden virtuelle Computer in bestimmter Weise aktualisiert, wodurch die Verfügbarkeit während des Vorgangs bestehen bleibt. Dabei wird davon ausgegangen, dass jeder Computer eine ähnliche Funktion wie die anderen in der Gruppe innehat. Jedem virtuellen Computer in der Verfügbarkeitsgruppe wird von der zugrunde liegenden Azure-Plattform eine Aktualisierungsdomäne und eine Fehlerdomäne zugeordnet. Jede Aktualisierungsdomäne ist eine Gruppe virtueller Computer, die im selben Zeitfenster neu gestartet werden. Jede Fehlerdomäne ist eine Gruppe virtueller Computer, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen.

Weitere Informationen über Aktualisierungs- und Fehlerdomänen finden Sie unter [Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz][Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz].

Microsoft Azure garantiert, dass kein ungeplantes Wartungsereignis dazu führt, dass virtuelle Computer von zwei unterschiedlichen Aktualisierungsdomänen gleichzeitig ausfallen. Die Wartung wird ausgeführt, indem jeder virtuelle Computer heruntergefahren, das Update auf die Hostcomputer angewendet, der virtuelle Computer neu gestartet und zur nächsten Aktualisierungsdomäne gewechselt wird. Das geplante Wartungsereignis endet, nachdem alle Aktualisierungsdomänen aktualisiert wurden. Während einer geplanten Wartung werden die Aktualisierungsdomänen möglicherweise nicht sequenziell neu gestartet. Es wird aber jeweils nur eine Aktualisierungsdomäne neu gestartet. Zurzeit steht keine erweiterte Benachrichtigung der geplanten Wartung für virtuelle Computer in der Konfiguration für mehrere Instanzen bereit.

Im Folgenden finden Sie ein Beispiel, was Ihre Windows-Ereignisanzeige möglicherweise anzeigt, nachdem der virtuelle Computer wiederhergestellt wurde:

<!--Image reference-->

![][0]

## Aktualisieren einer einzelnen Instanz

Nach der Fertigstellung der Updates für mehrere Instanzen wendet Azure die Aktualisierung auf die Hostcomputergruppe an, die die virtuellen Computer mit einer einzelnen Instanz hostet. Dieses Update führt zu einem Neustart Ihrer virtuellen Computer, die nicht in Verfügbarkeitsgruppen ausgeführt werden. Beachten Sie, auch wenn Sie nur über eine Instanz verfügen, die in einer Verfügbarkeitsgruppe ausgeführt wird, behandelt sie die Azure-Plattform so, als würde es sich um mehrere Instanzen handeln. Bei virtuellen Computern in einer Einzelinstanzkonfiguration werden virtuelle Computer aktualisiert, indem die virtuellen Computer heruntergefahren werden, die Aktualisierung auf den Hostcomputer angewendet wird und der virtuelle Computer neu gestartet wird. Diese Updates werden über alle virtuellen Computer in einer Region in einem einzelnen Wartungsfenster ausgeführt. Dieses geplante Wartungsereignis beeinträchtigt die Verfügbarkeit Ihrer Anwendung für diesen Typ der Konfiguration des virtuellen Computers.

### E-Mail-Benachrichtigung

Ausschließlich für die Einzelinstanzkonfiguration für virtuelle Computer sendet Azure mindestens eine Woche im Voraus eine E-Mail, um über die bevorstehende geplante Wartung zu informieren. Diese E-Mail wird an Ihr primäres E-Mail-Konto gesendet, das in Ihrem Abonnement angegeben ist. Ein Beispiel dieses E-Mail-Typs finden Sie unten:

<!--Image reference-->

![][1]

<!--Anchors--> <!--Link references-->

  [Unterschied zwischen geplanter und ungeplanter Wartung]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/
  [Konfigurationen des virtuellen Computers]: #virtual-machine-configurations
  [Aktualisieren mehrerer Instanzen]: #multi-instance-update
  [Aktualisieren einer einzelnen Instanz]: #single-instance-update
  [E-Mail-Benachrichtigung]: #email-notification
  [Verwalten der Verfügbarkeit virtueller Computer]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-manage-availability/
  [Vereinbarungen zum Servicelevel (SLAs)]: ../../support/legal/sla/
  [Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz]: http://azure.microsoft.com/de-de/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy
  [0]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png
  [1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
