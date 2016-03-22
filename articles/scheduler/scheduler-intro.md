<properties
 pageTitle="Was ist Azure Scheduler? | Microsoft Azure"
 description="Mit Azure Scheduler können Sie Aktionen, die in der Cloud ausgeführt werden sollen, deklarativ beschreiben. Anschließend werden diese Aktionen automatisch geplant und ausgeführt."
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
 ms.topic="hero-article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Was ist Azure Scheduler?

Mit Azure Scheduler können Sie Aktionen, die in der Cloud ausgeführt werden sollen, deklarativ beschreiben. Anschließend werden diese Aktionen automatisch geplant und ausgeführt. Mit Scheduler erfolgt dies über das [Azure-Portal](scheduler-get-started-portal.md), über Code, über die [REST-API](https://msdn.microsoft.com/library/dn528946) oder über Azure PowerShell.

Scheduler erstellt, pflegt und startet geplante Arbeit. Scheduler kann weder Workloads hosten noch Code ausführen. Der Dienst _ruft_ nur Code auf, der an anderer Stelle gehostet wird, z. B. in Azure, lokal oder bei einem anderen Anbieter. und über HTTP, HTTPS oder eine Speicherwarteschlange aufgerufen werden.

Scheduler plant [Aufträge](scheduler-concepts-terms.md), protokolliert einen Verlauf der Auftragsausführungsergebnisse, die überprüft werden können, und plant deterministisch und zuverlässig auszuführende Workloads. Azure WebJobs (Teil des Web-Apps-Features im Azure App Service) und andere Planungsfunktionen von Azure verwenden Scheduler im Hintergrund. Mit der [Scheduler-REST-API](https://msdn.microsoft.com/library/dn528946) wird die Kommunikation für diese Aktionen verwaltet. Auf diese Weise unterstützt Scheduler mühelos [komplexe Zeitpläne und Wiederholungszeitpläne](scheduler-advanced-complexity.md).

Mehrere Szenarios bieten sich für die Verwendung von Scheduler an. Beispiel:

+ _Periodische Anwendungsaktionen_: Erfassen von Daten von Twitter in regelmäßigen Abständen in einem Feed.
+ _Tägliche Wartung_: Tägliche Bereinigung von Protokollen, das Durchführen von Sicherungen und anderen Wartungsaufgaben. Ein Administrator möchte eine Datenbank z. B. für die nächsten neun Monate täglich um 1:00 Uhr sichern.

Mit Scheduler können Sie [Auftragssammlungen](scheduler-concepts-terms.md) programmgesteuert, mithilfe von Skripts und im Portal erstellen, aktualisieren, löschen, anzeigen und verwalten.

## Weitere Informationen

 [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)

 [Erste Schritte mit dem Scheduler im Azure-Portal](scheduler-get-started-portal.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Erstellen komplexer Zeitpläne und erweiterter Serien mit Azure Scheduler](scheduler-advanced-complexity.md)

 [Azure Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Hochverfügbarkeit und Zuverlässigkeit von Azure Scheduler](scheduler-high-availability-reliability.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Ausgehende Authentifizierung von Azure Scheduler](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0316_2016-->