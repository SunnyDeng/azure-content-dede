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
	ms.date="11/26/2014" 
	ms.author="kenazk"/>


# Geplante Wartung für virtuelle Azure-Computer

## Gründe für das Ausführen einer geplanten Wartung durch Azure
<p> Microsoft Azure führt regelmäßig weltweit Updates aus, um die Zuverlässigkeit, Leistung und Sicherheit der Hostinfrastruktur zu verbessern, die virtuellen Computern unterliegt. Viele dieser Updates werden ohne Beeinträchtigung der virtuellen Computer oder Cloud-Dienste ausgeführt. Einige Updates erfordern jedoch einen Neustart Ihres virtuellen Computers, damit die erforderlichen Updates auf die Infrastruktur angewendet werden können. Der virtuelle Computer wird heruntergefahren, während das Patch für die Infrastruktur vorgenommen wird. Danach wird der virtuelle Computer neu gestartet. Beachten Sie, dass es zwei Wartungsarten gibt, die die Verfügbarkeit Ihres virtuellen Computers beeinträchtigen können: geplante und ungeplante. Diese Seite beschreibt, wie Microsoft Azure die geplante Wartung ausführt. Weitere Informationen über ungeplante Wartung finden Sie unter [Unterschied zwischen geplanter und ungeplanter Wartung].

<!--Table of contents for topic, the words in brackets must match the heading wording exactly-->

* [Konfigurationen des virtuellen Computers]  
* [Mehrinstanzenaktualisierung]
* [Einzelinstanzenaktualisierung]
* [E-Mail-Benachrichtigung]


## Konfigurationen virtueller Computer
Es gibt zwei Arten der Konfiguration virtueller Computer: mehrere Instanzen und eine Instanz.  Virtuelle Computer mit mehreren Instanzen werden konfiguriert, indem identische virtuelle Computer in eine Verfügbarkeitsgruppe platziert werden. Die Konfiguration für mehrere Instanzen bietet Redundanz und wird zum Sicherstellen der Verfügbarkeit Ihrer Anwendung empfohlen. Alle virtuellen Computer in der Verfügbarkeitsgruppe sollten nahezu identisch sein und denselben Zweck für Ihre Anwendung erfüllen. Weitere Informationen zur Konfiguration virtueller Computer für Hochverfügbarkeit finden Sie unter "<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/">Verwalten der Verfügbarkeit virtueller Computer</a>". 

Im Gegensatz dazu sind virtuelle Computer mit einer einzelnen Instanz eigenständige virtuelle Computer, die nicht in einer Verfügbarkeitsgruppe platziert sind. Allein qualifizieren sich virtuelle Computer mit einer einzelnen Instanz nicht für die Vereinbarung zum Servicelevel (Service Level Agreement, SLA), wofür mindestens zwei virtuelle Computer erforderlich sind, die unter derselben Verfügbarkeitsgruppe bereitgestellt sind. Weitere Informationen über das SLA finden Sie im Abschnitt "Cloud-Dienste, virtuelle Computer und virtuelles Netzwerk" der [Vereinbarungen zum Servicelevel (SLAs)](http://azure.microsoft.com/support/legal/sla/).


## Update mehrerer Instanzen
Während der geplanten Wartung aktualisiert die Azure-Plattform zunächst die Gruppe an Hostcomputern, die die Gruppe von virtuellen Computern in einer Konfiguration für mehrere Instanzen hosten, wodurch diese virtuellen Computer neu gestartet werden. Bei virtuellen Computern in einer Konfiguration für mehrere Instanzen werden virtuelle Computer in bestimmter Weise aktualisiert, wodurch die Verfügbarkeit während des Vorgangs bestehen bleibt. Dabei wird davon ausgegangen, dass jeder Computer eine ähnliche Funktion wie die anderen in der Gruppe innehat. Jedem virtuellen Computer in der Verfügbarkeitsgruppe wird von der zugrunde liegenden Azure-Plattform eine Aktualisierungsdomäne und eine Fehlerdomäne zugeordnet. Jede Aktualisierungsdomäne ist eine Gruppe virtueller Computer, die im selben Zeitfenster neu gestartet werden. Jede Fehlerdomäne ist eine Gruppe virtueller Computer, die eine Stromquelle und einen Netzwerkswitch gemeinsam nutzen. 

Weitere Informationen über Aktualisierungs- und Fehlerdomänen erhalten Sie unter "<a href="http://azure.microsoft.com/documentation/articles/virtual-machines-manage-availability/#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy">Konfigurieren mehrerer virtueller Computer in einer Verfügbarkeitsgruppe für höhere Redundanz</a>".

Microsoft Azure garantiert, dass kein ungeplantes Wartungsereignis dazu führt, dass virtuelle Computer von zwei unterschiedlichen Aktualisierungsdomänen gleichzeitig ausfallen. Die Wartung wird ausgeführt, indem jeder virtuelle Computer heruntergefahren, das Update auf die Hostcomputer angewendet, der virtuelle Computer neu gestartet und zur nächsten Aktualisierungsdomäne gewechselt wird. Das geplante Wartungsereignis endet, nachdem alle Updatedomänen aktualisiert wurden. Während einer geplanten Wartung werden die Updatedomänen möglicherweise nicht sequenziell neu gestartet. Es wird aber jeweils nur eine Updatedomäne neu gestartet. Zurzeit steht keine erweiterte Benachrichtigung der geplanten Wartung für virtuelle Computer in der Konfiguration für mehrere Instanzen bereit.

Im Folgenden finden Sie ein Beispiel, was Ihre Windows-Ereignisanzeige möglicherweise anzeigt, nachdem der virtuelle Computer wiederhergestellt wurde:

<!--Image reference-->
![][image2]

## Einzelinstanzenaktualisierung
Nach der Fertigstellung der Updates für mehrere Instanzen wendet Azure das Update auf die Hostcomputergruppe an, die die virtuellen Computer mit einer einzelnen Instanz hostet. Dieses Update führt zu einem Neustart Ihrer virtuellen Computer, die nicht in Verfügbarkeitsgruppen ausgeführt werden. Beachten Sie, auch wenn Sie nur über eine Instanz verfügen, die in einer Verfügbarkeitsgruppe ausgeführt wird, behandelt sie die Azure-Plattform so, als würde es sich um mehrere Instanzen handeln. Bei virtuellen Computern in einer Einzelinstanzkonfiguration werden virtuelle Computer aktualisiert, indem die virtuellen Computer heruntergefahren werden, die Aktualisierung auf den Hostcomputer angewendet wird und der virtuelle Computer neu gestartet wird. Diese Updates werden über alle virtuellen Computer in einer Region in einem einzelnen Wartungsfenster ausgeführt. Dieses geplante Wartungsereignis beeinträchtigt die Verfügbarkeit Ihrer Anwendung für diesen Typ der Konfiguration des virtuellen Computers. 
 
### E-Mail-Benachrichtigung
Ausschließlich für die Einzelinstanzkonfiguration für virtuelle Computer sendet Azure mindestens eine Woche im Voraus eine E-Mail, um über die bevorstehende geplante Wartung zu informieren. Diese E-Mail wird an Ihr primäres E-Mail-Konto gesendet, das in Ihrem Abonnement angegeben ist. Ein Beispiel dieses E-Mail-Typs finden Sie unten:

<!--Image reference-->
![][image1]

<!--Anchors-->
[Gründe für das Ausführen einer geplanten Wartung durch Azure]: #why-azure-performs-planned-maintenance
[Konfigurationen des virtuellen Computers]: #virtual-machine-configurations
[Mehrinstanzenaktualisierung]: #multi-instance-update
[Einzelinstanzenaktualisierung]: #single-instance-update
[E-Mail-Benachrichtigung]: #email-notification
[image1]: ./media/virtual-machines-planned-maintenance/vmplanned1.png
[image2]: ./media/virtual-machines-planned-maintenance/EventViewerPostReboot.png


<!--Link references-->
[Verwaltungsverfügbarkeit virtueller Computer]: ../virtual-machines-windows-tutorial/
[Unterschied zwischen geplanter und ungeplanter Wartung]: ../virtual-machines-manage-availability/#Understand-planned-versus-unplanned-maintenance/ 

<!--HONumber=47-->
