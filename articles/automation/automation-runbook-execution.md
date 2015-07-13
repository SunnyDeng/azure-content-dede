<properties
   pageTitle="Ausführen von Runbooks in Azure Automation"
   description="Beschreibt ausführlich, wie ein Runbook in Azure Automation verarbeitet wird."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/28/2015"
   ms.author="bwren" />

# Ausführen von Runbooks in Azure Automation


Wenn Sie ein Runbook in Azure Automation starten, wird ein Auftrag erstellt. Ein Auftrag ist eine einzelne Ausführungsinstanz eines Runbooks. Für die Ausführung jedes Auftrags wird ein Azure Automation-Worker zugewiesen. Wenngleich Worker von mehreren Azure-Konten gemeinsam genutzt werden, sind die Aufträge von verschiedenen Automation-Konten voneinander isoliert. Sie können nicht steuern, welcher Worker die Anforderung für Ihren Auftrag verarbeitet. Für ein einzelnes Runbook können mehrere Aufträge gleichzeitig ausgeführt werden. Wenn Sie die Liste der Runbooks im Azure-Portal anzeigen, wird der Status des letzten Auftrags aufgelistet, der für jedes Runbook gestartet wurde. Sie können die Liste der Aufträge für jedes Runbook anzeigen, um den Status der einzelnen Aufträge nachzuverfolgen. Eine Beschreibung der verschiedenen Auftragsstatusangaben finden Sie unter [Auftragsstatuswerte](automation-viewing-the-status-of-a-runbook-job.md#job-statuses).

![Statuswerte für einen Auftrag](./media/automation-runbook-execution/job-statuses.png)


Ihre Aufträge können auf Ihre Azure-Ressourcen zugreifen, indem sie eine Verbindung mit Ihrem Azure-Abonnement herstellen. Sie besitzen nur Zugriff auf Ressourcen in Ihrem Datencenter, wenn auf diese Ressourcen aus der öffentlichen Cloud zugegriffen werden kann.

## Berechtigungen

Runbooks in Azure Automation erfordern in der Regel Zugriff auf Ressourcen in Ihrem Azure-Abonnement. Unter [Authentifizierung bei Azure mithilfe von Azure Active Directory](http://aka.ms/runbookauthor/authentication) wird beschrieben, wie Azure Active Directory und [Anmeldeinformationen]() für die Authentifizierung bei Azure-Ressourcen in Azure Automation konfiguriert werden. Dieses Thema enthält außerdem Informationen zur Verwendung des Cmdlets [Add-AzureAccount](http://aka.ms/runbookauthor/azurecmdlets/addazureaccount) für den Zugriff auf Azure-Ressourcen in Runbooks, die Sie erstellen. Lesen Sie die Dokumentation zu den einzelnen Runbooks, die Sie importieren, um ihre Sicherheitsanforderungen zu ermitteln.

## Gleichmäßige Verteilung

Damit Ressourcen von allen Runbooks in der Cloud verwendet werden können, entlädt Azure Automation jeden Auftrag vorübergehend, nachdem er 3 Stunden lang ausgeführt wurde, und startet ihn dann vom letzten [Prüfpunkt](http://aka.ms/runbookauthor/checkpoints) aus neu. Während dieser Zeit weist der Auftrag den Status "Wird ausgeführt, auf Ressourcen wird gewartet" auf. Wenn das Runbook keine Prüfpunkte enthält oder der Auftrag den ersten Prüfpunkt vor dem Entladen nicht erreicht hat, wird der Auftrag vom Anfang neu gestartet.

Wenn das Runbook vom gleichen Prüfpunkt aus oder vom Anfang des Runbooks drei aufeinander folgende Male neu gestartet wurde, wird es mit dem Status "Fehler, auf Ressourcen wird gewartet" beendet. Dieses Verhalten schützt davor, dass Runbooks unbegrenzt ausgeführt werden, da der nächste Prüfpunkt nicht ohne Entladen erreicht werden kann. In diesem Fall wird ein Ausnahmefehler mit der folgenden Meldung angezeigt.

Die Ausführung des Auftrags kann nicht fortgesetzt werden, da er wiederholt am selben Prüfpunkt entfernt wurde. Stellen Sie sicher, dass Ihr Runbook Vorgänge mit langer Ausführungsdauer nicht ausführt, ohne deren Zustand dauerhaft zu speichern.

Sorgen Sie beim Erstellen eines Runbooks dafür, dass die Zeit zum Ausführen von Aktivitäten zwischen zwei Prüfpunkten 3 Stunden nicht überschreitet. Sie müssen ggf. Prüfpunkte zu Ihrem Runbook hinzufügen, um sicherzustellen, dass dieser Grenzwert von 3 Stunden nicht erreicht wird. Falls erforderlich, müssen Sie Vorgänge mit langer Ausführungszeit aufteilen. Angenommen, Ihr Runbook führt eine Neuindizierung für eine große SQL-Datenbank durch. Wenn dieser einzelne Vorgang nicht innerhalb des Grenzwerts für die gleichmäßige Verteilung abgeschlossen wird, wird der Auftrag entladen und vom Anfang neu gestartet. In diesem Fall sollten Sie den Neuindizierungsvorgang in mehrere Schritte unterteilen (z. B. nur jeweils eine Tabelle gleichzeitig neu indizieren) und dann einen Prüfpunkt nach jedem Vorgang einfügen, damit der Auftrag nach dem letzten Vorgang bis zum Abschluss fortgesetzt werden kann.

## Verwandte Artikel

- [Starten eines Runbooks in Azure Automation](automation-starting-a-runbook)
- [Anzeigen des Status eines Runbookauftrags in Azure Automation](automation-viewing-the-status-of-a-runbook-job)
 

<!---HONumber=62-->