<properties 
   pageTitle="Unterstützungs- und Deaktivierungsrichtlinien für Azure-Gastbetriebssysteme | Microsoft Azure" 
   description="Bietet Informationen zu den Elementen, die Microsoft hinsichtlich des von Clouddiensten verwendeten Azure-Gastbetriebssystems unterstützt." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="Thraka" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="08/18/2015"
   ms.author="adegeo"/>

# Unterstützungs- und Deaktivierungsrichtlinie für Azure-Gastbetriebssysteme
Die Informationen auf dieser Seite beziehen sich auf das Azure-Gastbetriebssystem ([Gast-BS](https://msdn.microsoft.com/library/azure/ff729422.aspx)) für Worker- und Webrollen für Clouddienste (PaaS). Sie gelten nicht für virtuelle Computer (IaaS).

Microsoft verfügt über eine veröffentlichte [Unterstützungsrichtlinie für das Gastbetriebssystem](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). Auf der vorliegenden Seite wird beschrieben, wie diese Richtlinie implementiert wird.

Die Richtlinie lautet:

1. Microsoft unterstützt **mindestens die beiden neuesten Gastbetriebssystemfamilien**. Wenn eine Familie deaktiviert wird, haben die Kunden ab dem offiziellen Deaktivierungsdatum 12 Monate Zeit, um auf eine neuere unterstützte Gastbetriebssystemfamilie zu aktualisieren.
2. Microsoft unterstützt **mindestens die beiden neuesten Versionen der unterstützten Gastbetriebssystemfamilien**. 
3. Microsoft unterstützt **mindestens die beiden neuesten Versionen des Azure SDK**. Wenn eine SDK-Version deaktiviert wird, haben die Kunden ab dem offiziellen Deaktivierungsdatum 12 Monate Zeit, um auf eine neuere Version zu aktualisieren. 

Es werden möglicherweise mehr als zwei Familien oder Versionen gleichzeitig unterstützt. Offizielle Informationen zur Unterstützung von Gastbetriebssystemen finden Sie auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md).


## Wann wird eine Gast-BS-Familie oder ein Gast-BS-Release deaktiviert? 


Eine neue **Familie** eines Gastbetriebssystems wird einige Zeit nach Veröffentlichung der neuen offiziellen Version eines Windows Server-Betriebssystems eingeführt. Wenn eine neue Gastbetriebssystemfamilie eingeführt wird, deaktiviert Microsoft die älteste Gastbetriebssystemfamilie.

Neue **Versionen** von Gastbetriebssystemen werden etwa jeden Monat eingeführt und enthalten die neuesten MSRC-Updates. Aufgrund dieser regelmäßigen monatlichen Updates wird eine Gastbetriebssystemversion normalerweise 60 Tage nach ihrer Veröffentlichung deaktiviert. Auf diese Weise sind mindestens zwei Versionen jeder Gastbetriebssystemfamilie zur Verwendung verfügbar.

### Verfahren während der Deaktivierung einer Gastbetriebssystemfamilie 


Nachdem die Deaktivierung angekündigt wurde, steht den Kunden ein Übergangszeitraum von 12 Monaten zur Verfügung, bevor die ältere Familie offiziell aus dem Dienst entfernt wird. Diese Übergangszeit kann nach dem Ermessen von Microsoft verlängert werden. Updates werden auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md) veröffentlicht.

6 Monate nach Beginn des Übergangszeitraums beginnt ein abgestufter Deaktivierungsprozess. Während dieses Zeit geschieht Folgendes:

1. Microsoft benachrichtigt die Kunden über die Deaktivierung. 
2. Die neuere Version des Azure SDK unterstützt die deaktivierte Gastbetriebssystemfamilie nicht.
3. Neue und erneute Bereitstellungen von Clouddiensten sind in der deaktivierten Familie nicht zulässig.

Microsoft führt bis zum letzten Tag des Übergangszeitraums, dem so genannten "Ablaufdatum", weiterhin neue Gastbetriebsversionen einschließlich neuer MSRC-Updates ein. Gleichzeitig wird die Unterstützung für alle noch ausgeführten Clouddienste im Rahmen der Azure-SLA aufgehoben. Nach diesem Datum kann Microsoft nach eigenem Ermessen ein Upgrade für diese Dienste erzwingen oder diese Dienste löschen oder beenden.



### Verfahren während der Deaktivierung einer Gastbetriebssystemversion 
Wenn Kunden ihr Gastbetriebssystem so eingerichtet haben, dass es automatisch aktualisiert wird, müssen sie sich keine Gedanken um Gastbetriebssystemversionen machen. Sie werden immer die neueste Version des Gastbetriebssystems verwenden.

Gastbetriebssystemversionen werden jeden Monat veröffentlicht. Aufgrund der Häufigkeit der regulären Releases hat jede Version eine feste Lebensdauer.

60 Tage nach Beginn der Lebensdauer wird eine Version "*deaktiviert*". "Deaktiviert" bedeutet, dass die Version aus dem Azure-Verwaltungsportal entfernt wird. Sie kann auch nicht mehr über die CSCFG-Konfigurationsdatei festgelegt werden. Vorhandene Bereitstellungen werden weiterhin ausgeführt, neue Bereitstellungen sowie Code- und Konfigurationsupdates für vorhandene Bereitstellungen sind jedoch nicht zulässig.

Zu einem späteren Zeitpunkt wird die Gast-BS-Version "*ablaufen*", und für alle Installationen, die noch unter dieser Version ausgeführt werden, wird ein Upgrade erzwungen. Gleichzeitig wird für diese Installationen festgelegt, dass das Gast-BS in Zukunft automatisch aktualisiert wird. Der Ablauf erfolgt in Batches, sodass die Zeitspanne zwischen Deaktivierung und tatsächlichem Ablauf variieren kann.

Diese Zeiträume können nach Ermessen von Microsoft verlängert werden, um den Kunden den Übergang zu erleichtern. Updates werden auf der Seite [Azure-Gastbetriebssystemreleases und SDK-Kompatibilitätsmatrix](cloud-services-guestos-update-matrix.md) veröffentlicht.



### Benachrichtigungen während der Deaktivierung 

* **Deaktivierung der Familie** <br>Microsoft verwendet Blogbeiträge sowie Benachrichtigungen im Verwaltungsportal, um die Kunden zu informieren. Bei Kunden, die eine deaktivierte Gastbetriebssystemfamilie weiterhin verwenden, werden zugewiesene Dienstadministratoren auf direktem Weg benachrichtigt (E-Mail, Mitteilung im Portal, Telefonanruf). Alle Änderungen werden auf dieser Seite und im oben auf dieser Seite aufgeführten RSS-Feed veröffentlicht. 


* **Deaktivierung der Version** <br>Alle Änderungen, einschließlich des Veröffentlichungs-, Deaktivierungs- und Ablaufdatums, werden auf dieser Seite und im oben auf dieser Seite aufgeführten RSS-Feed veröffentlicht. Dienstadministratoren erhalten E-Mails, wenn sie über Bereitstellungen verfügen, die unter einer deaktivierten Gastbetriebssystemversion oder -familie ausgeführt werden. Diese E-Mails können zu verschiedenen Zeitpunkten gesendet werden. Im Allgemeinen werden sie mindestens einen Monat vor der Deaktivierung gesendet, dies ist jedoch keine offizielle SLA.


## Häufig gestellte Fragen

**Wie kann ich die Auswirkungen einer Migration minimieren?**

Sie sollten die neueste Gastbetriebssystemfamilie verwenden, um Ihre Clouddienste zu konzipieren.

1. Starten Sie die Planung der Migration zu einer neueren Familie frühzeitig. 
2. Richten Sie temporäre Testbereitstellungen ein, um die Ausführung Ihres Clouddiensts unter der neuen Gastbetriebssystemfamilie zu testen. 
3. [Legen Sie die Gastbetriebssystemversion](https://msdn.microsoft.com/library/azure/gg433101.aspx) auf „Automatisch“ fest (osVersion=* in der [CSCFG](https://msdn.microsoft.com/library/azure/gg456324.aspx)-Datei), sodass die Migration zu neuen Gastbetriebssystemversionen automatisch erfolgt.

**Was geschieht, wenn meine Webanwendung eine tiefer greifende tiefere Integration in das Betriebssystem erfordert?**

Wenn die Architektur Ihrer Webanwendung eine tiefer gehende Abhängigkeit vom zugrunde liegenden Betriebssystem erfordert, verwenden Sie von der Plattform unterstützte Funktionen wie z. B. "[Startaufgaben](https://msdn.microsoft.com/library/windowsazure/gg456327.aspx)" oder andere, zukünftig verfügbare Mechanismen zur Erweiterbarkeit. Alternativ dazu können Sie auch [Azure Virtual Machines](http://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – Infrastructure-as-a-Service) verwenden, wo die Verwaltung des zugrunde liegenden Betriebssystems in Ihrem Verantwortungsbereich liegt.
 
## Nächste Schritte
Überprüfen Sie die neuesten [Gastbetriebssystemreleases](cloud-services-guestos-update-matrix.md).

<!---HONumber=Oct15_HO3-->