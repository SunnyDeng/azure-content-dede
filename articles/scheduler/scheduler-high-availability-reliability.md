<properties
 pageTitle="Hochverfügbarkeit und Zuverlässigkeit von Scheduler"
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
 ms.topic="article"
 ms.date="03/09/2016"
 ms.author="krisragh"/>


# Hochverfügbarkeit und Zuverlässigkeit von Scheduler

## Hochverfügbarkeit von Azure Scheduler

Als Kerndienst der Azure-Plattform ist Azure Scheduler hochverfügbar und bietet sowohl eine georedundante Dienstbereitstellung als auch eine georegionale Auftragsreplikation.

### Georedundante Dienstbereitstellung

Azure Scheduler steht über die Benutzeroberfläche in nahezu allen geografischen Regionen zur Verfügung, die aktuell in Azure verfügbar sind. Die Regionen, in denen Azure Scheduler verfügbar ist, finden Sie [hier](https://azure.microsoft.com/regions/#services). Beim Ausfall eines Rechenzentrums in einer gehosteten Region wird der Azure Scheduler-Dienst dank Failoverfunktionen über ein anderes Rechenzentrum bereitgestellt.

### Georegionale Auftragsreplikation

Das Front-End von Azure Scheduler ist nicht nur für Verwaltungsanforderungen verfügbar, Ihr eigener Auftrag wird auch geografisch repliziert. Bei einem Ausfall in einer Region wird mittels Failover für Azure Scheduler sichergestellt, dass der Auftrag in einem anderen Rechenzentrum in der gekoppelten geografischen Region ausgeführt wird.

Wenn Sie also beispielsweise einen Auftrag in der Region „USA (Mitte/Süden)“ erstellt haben, repliziert Azure Scheduler diesen Auftrag automatisch in die Region „USA (Mitte/Norden)“. Bei einem Ausfall in der Region „USA (Mitte/Süden)“ sorgt Azure Scheduler dafür, dass der Auftrag in der Region „USA (Mitte/Norden)“ ausgeführt wird. [Eine ausführliche Erläuterung der Georeplikationsfunktionen von Azure finden Sie in diesem Thema.](../sql-database/sql-database-business-continuity-design.md)

![][1]

Dadurch gewährleistet Azure Scheduler, dass Ihre Daten bei einem Ausfall von Azure grob innerhalb der gleichen geografischen Region verbleiben. Sie müssen Ihren Auftrag also nicht duplizieren, um eine hohe Verfügbarkeit zu erreichen, da Azure Scheduler bereits automatisch eine hohe Verfügbarkeit für Ihre Aufträge bietet.

## Zuverlässigkeit von Azure Scheduler

Azure Scheduler bietet eine garantierte hohe Verfügbarkeit sowie einen anderen Ansatz für Aufträge, die von Benutzern erstellt werden. Es kann beispielsweise vorkommen, dass Ihr Auftrag einen HTTP-Endpunkt aufruft, der nicht verfügbar ist. Azure Scheduler versucht trotzdem, den Auftrag erfolgreich auszuführen, und stellt dafür alternative Optionen für die Fehlerbehandlung bereit. Dies wird auf zwei Arten erreicht:

### Konfigurierbare Wiederholungsrichtlinie („retryPolicy“)

In Azure Scheduler können Sie eine Wiederholungsrichtlinie konfigurieren. Nach einer nicht erfolgreichen Auftragsausführung wird die Ausführung standardmäßig viermal im Abstand von jeweils 30 Sekunden wiederholt. Diese Wiederholungsrichtlinie kann aggressiver (beispielsweise zehnmal im 30-Sekunden-Takt) oder großzügiger (beispielsweise zweimal in Tagesintervallen) konfiguriert werden.

Dies kann zum Beispiel hilfreich sein, wenn Sie einen Auftrag erstellen, der einmal pro Woche ausgeführt wird und einen HTTP-Endpunkt aufruft. Fällt dieser HTTP-Endpunkt für einige Stunden aus, empfiehlt es sich wahrscheinlich nicht, eine ganze Woche zu warten, bis der Auftrag erneut ausgeführt wird, da hier sogar die standardmäßige Wiederholungsrichtlinie nicht greift. In einem solchen Fall können Sie die standardmäßige Wiederholungsrichtlinie beispielsweise mit einer Wiederholung im Drei-Stunden-Takt konfigurieren.

Informationen zum Konfigurieren einer Wiederholungsrichtlinie finden Sie unter [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### Konfigurierbarer alternativer Endpunkt („errorAction“)

Ist der Zielendpunkt für Ihren Azure Scheduler-Auftrag weiterhin nicht erreichbar, greift Azure Scheduler nach Anwendung der Wiederholungsrichtlinie auf den alternativen Fehlerbehandlungsendpunkt zurück. Wenn ein alternativer Fehlerbehandlungsendpunkt konfiguriert ist, wird er von Azure Scheduler aufgerufen. Mit einem alternativen Endpunkt sind Ihre Aufträge bei einem Ausfall hochverfügbar.

Ein Beispiel sehen Sie im folgenden Diagramm: Azure Scheduler wendet die Wiederholungsrichtlinie an und versucht, einen Webdienst in New York zu erreichen. Da die Wiederholungen nicht erfolgreich sind, überprüft der Dienst, ob eine Alternative vorhanden ist. Daraufhin werden Anforderungen unter Verwendung der gleichen Wiederholungsrichtlinie an den alternativen Endpunkt gerichtet.

![][2]

Beachten Sie, dass die gleiche Wiederholungsrichtlinie sowohl für die ursprüngliche Aktion als auch für die alternative Fehleraktion gilt. Der Aktionstyp der alternativen Fehleraktion kann sich auch vom Aktionstyp der Hauptaktion unterscheiden. So kann die Fehleraktion beispielsweise eine Speicherwarteschlangen-, Service Bus-Warteschlangen- oder Service Bus-Topic-Aktion mit Fehlerprotokollierung sein, obwohl die Hauptaktion einen HTTP-Endpunkt aufruft.

Weitere Informationen zum Konfigurieren eines alternativen Endpunkts finden Sie unter [errorAction](scheduler-concepts-terms.md#action-and-erroraction).

## Siehe auch

 [Was ist Azure Scheduler?](scheduler-intro.md)

 [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)

 [Erste Schritte mit dem Scheduler im Azure-Portal](scheduler-get-started-portal.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Erstellen komplexer Zeitpläne und erweiterter Serien mit Azure Scheduler](scheduler-advanced-complexity.md)

 [Azure Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Ausgehende Authentifizierung von Azure Scheduler](scheduler-outbound-authentication.md)


[1]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png

[2]: ./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png

<!---HONumber=AcomDC_0323_2016-->