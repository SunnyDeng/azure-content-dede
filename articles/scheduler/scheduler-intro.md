<properties
 pageTitle="Was ist Azure Scheduler?"
 description=""
 services="scheduler"
 documentationCenter=".NET"
 authors="krisragh"
 manager="dwrede"
 editor=""/>
<tags
 ms.service="scheduler"
 ms.workload="infrastructure-services"
 ms.tgt_pltfrm="na"
 ms.devlang="dotnet"
 ms.topic="get-started-article" 
 ms.date="05/12/2015"
 ms.author="krisragh"/>

# Was ist Azure Scheduler?

Mit Azure Scheduler können Sie Aktionen, die in der Cloud ausgeführt werden sollen, deklarativ beschreiben. Anschließend werden diese Aktionen automatisch geplant und ausgeführt. Mit Azure Scheduler erfolgt dies über das [Azure-Portal](scheduler-get-started-portal.md), über Code, über die [REST-API](https://msdn.microsoft.com/library/dn528946) oder über PowerShell.

Azure Scheduler pflegt, verwaltet und plant und startet die geplante Arbeit. Azure Scheduler kann weder Workloads hosten noch Code ausführen. Der Dienst _ruft_ nur Code auf, der an anderer Stelle gehostet wird. Dieser Code kann in Azure, lokal oder bei einem anderen Anbieter gehostet und über HTTP, HTTPS oder eine Speicherwarteschlange aufgerufen werden.

Azure Scheduler plant Aufträge, protokolliert einen Verlauf der Auftragsausführungsergebnisse, die abgefragt werden können, und plant deterministisch und zuverlässig auszuführende Workloads. Geplante Skripts in Azure Mobile Services, Webaufträge in Azure Web Apps und weitere Azure-Planungsfunktionen verwenden Azure Scheduler im Hintergrund. Mit der [Scheduler-REST-API](https://msdn.microsoft.com/library/dn528946) wird die Kommunikation für diese Aktionen verwaltet. Auf diese Weise unterstützt Scheduler mühelos [komplexe Zeitpläne und Wiederholungszeitpläne](scheduler-advanced-complexity.md).

Mehrere Szenarios bieten sich für die Verwendung von Azure Scheduler an. Beispiel:

+ _Periodische Anwendungsaktionen_: Erfassen von Daten von Twitter in regelmäßigen Abständen und Zusammenfassen dieser Daten in einem Feed.
+ _Tägliche Wartung_: Tägliche Bereinigung von Protokollen, Erstellen von Sicherungskopien und andere Wartungsaufgaben. Ein Administrator kann beispielsweise festlegen, dass eine Datenbank für die nächsten 9 Monate jeden Tag um 01:00 Uhr gesichert wird.

Mit Scheduler können Sie ["Auftragssammlungen" und "Aufträge"](scheduler-concepts-terms.md) programmgesteuert, mithilfe von Skripts und im Portal erstellen, aktualisieren, löschen, anzeigen und verwalten.

## Siehe auch

 [Scheduler Concepts, Terminology, and Entity Hierarchy](scheduler-concepts-terms.md) (in englischer Sprache)

 [Get Started Using Scheduler in the Management Portal](scheduler-get-started-portal.md) (in englischer Sprache)

 [Plans and Billing in Azure Scheduler](scheduler-plans-billing.md) (in englischer Sprache)

 [How to Build Complex Schedules and Advanced Recurrence with Azure Scheduler](scheduler-advanced-complexity.md) (in englischer Sprache)

 [Zeitplanungsmodul-REST-API – Referenz](https://msdn.microsoft.com/library/dn528946)

 [Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Scheduler High-Availability and Reliability](scheduler-high-availability-reliability.md) (in englischer Sprache)

 [Scheduler Limits, Defaults, and Error Codes](scheduler-limits-defaults-errors.md) (in englischer Sprache)

 [Scheduler Outbound Authentication](scheduler-outbound-authentication.md) (in englischer Sprache)
 

<!---HONumber=July15_HO2-->