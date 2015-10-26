<properties 
   pageTitle="Entwerfen von Cloud-Lösungen für die Notfallwiederherstellung mithilfe der Azure SQL-Datenbank-Georeplikation"
   description="Erfahren Sie, wie Sie durch Wahl des richtigen Failovermusters Ihre Cloud-Lösung für die Notfallwiederherstellung entwerfen können."
   services="sql-database"
   documentationCenter="" 
   authors="anosov1960" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="10/07/2015"
   ms.author="sashan"/>

# Entwerfen von Cloudanwendungen zum Sicherstellen der Geschäftskontinuität mithilfe der Georeplikation

Es gibt beim Entwerfen Ihrer Anwendung zum Sicherstellen der Geschäftskontinuität mehrere Methoden zur Nutzung der Georeplikation in Azure SQL-Datenbank. Die Faktoren, die Ihren Entwurf bestimmen, sind die Bereitstellungstopologie der Anwendung, einzuhaltende Vereinbarung zum Servicelevel (SLA), Datenverkehrslatenz und Kosten. In diesem Artikel untersuchen wir die gängigsten Anwendungsmuster und erörtern die Vor- und Nachteile der einzelnen Optionen.

## Entwurfsmuster 1: Aktiv-/Passiv-Bereitstellung für Notfallwiederherstellung mit zusammengestellter Datenbank

Diese Option eignet sich am besten für Anwendungen mit den folgenden Merkmalen:

+ Aktive Instanz in einer einzelnen Azure-Region
+ Starke Abhängigkeit vom Lese-/ Schreibzugriff auf Daten 
+ Regionsübergreifende Konnektivität zwischen Anwendungslogik und Datenbank aufgrund von Latenz und Datenverkehrskosten nicht akzeptabel    

In diesem Fall wird die Bereitstellungstopologie der Anwendung für das Bewältigen regionaler Notfälle optimiert, bei denen alle Anwendungskomponenten betroffen sind und ihr Failover als Einheit erfolgen muss. Für geografische Redundanz werden sowohl die Anwendungslogik als auch die Datenbank in eine andere Region repliziert, aber unter normalen Umständen nicht für Anwendungsworkloads verwendet. Die Anwendung in der sekundären Region muss für das Verwenden einer SQL-Verbindungszeichenfolge für die sekundäre Datenbank konfiguriert werden. Traffic Manager ist für die [Failoverroutingmethode](traffic-manager-configure-failover-load-balancing.md) eingerichtet.

> [AZURE.NOTE] [Azure traffic manager](traffic-manager-overview.md) wird in diesem Artikel ausschließlich zu Demonstrationszwecken genutzt. Sie können eine beliebige Lastenausgleichslösung einsetzen, die die Failoverroutingmethode unterstützt.
 
Zusätzlich zu den Hauptanwendungsinstanzen sollten Sie die Bereitstellung einer kleinen [Workerrollenanwendung](cloud-services-choose-me.md#tellmecs) erwägen, die die primäre Datenbank durch regelmäßiges Aufrufen schreibgeschützter T-SQL-Befehle überwacht. Sie können sie verwenden, um automatisch ein Failover auszulösen, eine Warnung in der Verwaltungskonsole der Anwendung auszulösen oder für beides. Um sicherzustellen, dass die Überwachung nicht von Ausfällen in der gesamten Region betroffen ist, müssen Sie zunächst die Instanzen der Überwachungsanwendung in den einzelnen Regionen bereitstellen. Anschließend müssen Sie sie mit der Datenbank in der anderen Region verbinden, wobei jedoch nur die Instanz in der sekundären Region aktiv sein muss.

> [AZURE.NOTE]Bei Verwendung der [aktiven Georeplikation](https://msdn.microsoft.com/library/azure/dn741339.aspx) können beide Überwachungsanwendungen aktiv sein und sowohl die primären als auch die sekundären Datenbanken testen. Letztere kann verwendet werden, um einen Ausfall in der sekundären Region zu erkennen und zu warnen, wenn die Anwendung nicht geschützt ist.

Das folgende Diagramm zeigt diese Konfiguration vor einem Ausfall.

![Abbildung 1](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-1.png)

Nach einem Ausfall in der primären Region erkennt die Überwachungsanwendung, dass kein Zugriff auf die primäre Datenbank möglich ist, und löst eine Warnung aus. Je nach der für Ihre Anwendung geltenden SLA können Sie bestimmen, wie viele nachfolgende Überwachungstests fehlerhaft sein dürfen, ehe Sie einen Datenbankausfall bekanntgeben. Um ein koordiniertes Failover des Anwendungsendpunkts und der Datenbank zu ermöglichen, muss die Überwachungsanwendung die folgenden Schritte ausführen:

1. [Aktualisieren des Status des primären Endpunkts](https://msdn.microsoft.com/library/hh758250.aspx) zum Auslösen des Endpunktfailovers
2. Aufrufen der sekundären Datenbank zum [Auslösen des Datenbankfailovers](https://msdn.microsoft.com/library/azure/dn509573.aspx) 

Nach einem Failover verarbeitet die Anwendung die Benutzeranforderungen in der sekundären Region, bleibt jedoch mit der Datenbank zusammengestellt, da die primäre Datenbank sich nun in der sekundären Region befindet. Dies wird im folgenden Diagramm veranschaulicht. In allen Diagrammen stehen durchgezogene Linien für aktive Verbindungen, gepunktete Linien für unterbrochene Verbindungen und Stoppschilder für Aktionstrigger.


![Abbildung 2](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-2.png)

Bei einem Ausfall in der sekundären Region wird der Replikationslink zwischen der primären und sekundären Datenbank unterbrochen, und die Überwachungsanwendung löst die Warnung aus, dass die primäre Datenbank ungeschützt ist. Dies hat keine Auswirkung auf die Leistung der Anwendung, die jedoch ungeschützt und mit dem erhöhten Risiko betrieben wird, dass beide Regionen nacheinander ausfallen.

> [AZURE.NOTE]Wir empfehlen ausschließlich Bereitstellungskonfigurationen mit einer einzelnen Notfallwiederherstellungsregion. Der Grund ist, dass die meisten Azure-Gebiete zwei Regionen aufweisen. Diese Konfigurationen bieten Ihrer Anwendung keinen Schutz vor einem katastrophenbedingten Ausfall beider Regionen. Im unwahrscheinlichen Fall eines solchen Ausfalls können Ihre Datenbanken mithilfe eines [Geowiederherstellungsvorgangs](sql-database-disaster-recovery.md#recovery-using-geo-restore) wiederhergestellt werden.
 
Nach Behebung der Ausfallursache wird die sekundäre Datenbank automatisch mit der primären synchronisiert. Während der Synchronisierung kann die Leistung der primären Datenbank geringfügig beeinträchtigt werden, was von der Menge der Daten abhängt, die synchronisiert werden muss. Das folgende Diagramm zeigt einen Ausfall in der sekundären Region.

![Abbildung 3](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern1-3.png)


Die **Hauptvorteile** dieses Entwurfsmusters sind:

+ Die SQL-Verbindungszeichenfolge wird während der Anwendungsbereitstellung in den einzelnen Regionen festgelegt und ändert sich nach dem Failover nicht.
+ Die Leistung der Anwendung nicht durch das Failover beeinträchtigt, da Anwendung und Datenbank stets zusammengestellt sind. 

Der **Hauptnachteil** besteht darin, dass die redundante Anwendungsinstanz in der sekundären Region nur für die Notfallwiederherstellung verwendet wird.

## Entwurfsmuster 2: Aktiv-/Aktiv-Bereitstellung für Anwendungslastenausgleich
Diese Option eignet sich am besten für Anwendungen mit den folgenden Merkmalen:

+ Hohes Verhältnis von Datenbanklesevorgängen zu -schreibvorgängen
+ Latenz von Datenbankschreibvorgängen ohne Auswirkung auf Endbenutzer  
+ Schreibgeschützte Logik kann von Lese-/Schreiblogik mittels einer anderen Verbindungszeichenfolge getrennt werden 
+ Schreibgeschützte Logik nicht davon abhängig, dass Daten vollständig mit den neuesten Aktualisierungen synchronisiert sind  

Wenn Ihre Anwendung diese Merkmale aufweist, kann ein Lastenausgleich der Endbenutzerverbindungen über mehrere Anwendungsinstanzen in verschiedenen Regionen die Leistung und Endbenutzererfahrung verbessern. Um dies zu erreichen, muss jede Region über eine aktive Instanz der Anwendung verfügen, wobei die Lese-/Schreiblogik mit der primären Datenbank in der primären Region verbunden ist. Die schreibgeschützte Logik muss mit einer sekundären Datenbank in derselben Region wie die Anwendungsinstanz verbunden werden. Traffic Manager muss so eingerichtet werden, dass für jede Anwendungsinstanz das [Roundrobin-Routing](traffic-manager-configure-round-robin-load-balancing.md) oder das [Leistungsrouting](traffic-manager-configure-performance-load-balancing.md) mit [Endpunktüberwachung](traffic-manager-monitoring.md) aktiviert wird.

Wie bei Muster 1 sollten Sie das Bereitstellen einer ähnlichen Überwachungsanwendung erwägen. Doch im Gegensatz zu Muster 1 ist diese nicht für das Auslösen des Endpunktfailovers zuständig.

> [AZURE.NOTE]Wenngleich bei diesem Muster mehr als eine sekundäre Datenbank zum Einsatz kommt, wird nur eine dieser sekundären Datenbanken aus den zuvor genannten Gründen für das Failover genutzt. Da dieses Muster einen schreibgeschützten Zugriff auf die sekundäre Datenbank verlangt, ist eine [aktive Georeplikation](https://msdn.microsoft.com/library/azure/dn741339.aspx) erforderlich.

Traffic Manager muss für das Leistungsrouting so konfiguriert werden, dass die Benutzerverbindungen zur Anwendungsinstanz geleitet werden, die dem geografischen Standort des Benutzers am nächsten ist. Das folgende Diagramm veranschaulicht diese Konfiguration vor einem Ausfall. ![Abbildung 4](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-1.png)

Wenn in der primären Region ein Datenbankausfall erkannt wird, lösen Sie das Failover der primären Datenbanken in eine der sekundären Regionen aus, wodurch sich der Standort der primären Datenbank ändert. Traffic Manager schließt automatisch den Endpunkt im Offlinestatus aus der Routingtabelle aus, setzt aber das Routing des Endbenutzerdatenverkehrs an die verbleibenden Online-Instanzen fort. Da sich die primäre Datenbank nun in einer anderen Region befindet, muss bei allen Online-Instanzen die SQL-Verbindungszeichenfolge für den Lese-/Schreibzugriff so geändert werden, dass eine Verbindung mit der neuen primären Datenbank hergestellt wird. Wichtig ist, dass Sie diese Änderung vor dem Auslösen des Datenbankfailovers vornehmen. Schreibgeschützte SQL-Verbindungszeichenfolgen müssen unverändert bleiben, da sie stets auf die Datenbank in derselben Region zeigen. Die Failoverschritte sind wie folgt:

1. Ändern der SQL-Verbindungszeichenfolgen für den Lese-/ Schreibzugriff dergestalt, dass sie auf die neue primäre Datenbank zeigen
2. Aufrufen der vorgesehenen sekundären Datenbank zum [Auslösen des Datenbankfailovers](https://msdn.microsoft.com/library/azure/dn509573.aspx) 

Das folgende Diagramm veranschaulicht die neue Konfiguration nach dem Failover. ![Abbildung 5](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-2.png)

Bei einem Ausfall einer der sekundären Regionen entfernt Traffic Manager automatisch den Endpunkt im Offlinestatus in dieser Region aus der Routingtabelle. Der Replikationskanal zur sekundären Datenbank in dieser Region wird unterbrochen. Da die übrigen Regionen zusätzlichen Benutzerdatenverkehr erhalten, kann die Leistung der Anwendung während des Ausfalls beeinträchtigt werden. Nach Behebung der Ausfallursache wird die sekundäre Datenbank in der betroffenen Region sofort mit der primären Datenbank synchronisiert. Während der Synchronisierung kann die Leistung der primären Datenbank geringfügig beeinträchtigt werden, was von der Menge der Daten abhängt, die synchronisiert werden muss. Das folgende Diagramm zeigt einen Ausfall in einer der sekundären Regionen.

![Abbildung 6](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern2-3.png)

Der **Hauptvorteil** dieses Entwurfsmusters ist, dass Sie den Anwendungsworkload auf mehrere sekundäre Datenbanken verteilen können, um eine optimale Leistung für die Endbenutzer zu erzielen. Diese Option hat folgende **Nachteile**:

+ Lese-/Schreibzugriffsverbindungen zwischen Anwendungsinstanzen und Datenbank haben unterschiedliche Wartezeiten und Kosten.
+ Die Anwendungsleistung ist während des Ausfalls beeinträchtigt.
+ Anwendungsinstanzen müssen die SQL-Verbindungszeichenfolge nach einem Datenbankfailover dynamisch ändern.  

> [AZURE.NOTE]Ein ähnlicher Ansatz kann verwendet werden, um spezielle Workloads wie Berichtsaufträge, Business Intelligence-Tools oder Sicherungen auszulagern. Diese Workloads nutzen in der Regel umfangreiche Datenbankressourcen. Deshalb wird empfohlen, dass Sie ihnen eine der sekundären Datenbanken mit der Leistungsstufe zuweisen, die dem erwarteten Workload entspricht.

## Entwurfsmuster 3: Aktiv-/Passiv-Bereitstellung für die Beibehaltung von Daten  
Diese Option eignet sich am besten für Anwendungen mit den folgenden Merkmalen:

+ Jeder Datenverlust stellt ein hohes Geschäftsrisiko dar, und das Datenbankfailover darf nur als letzter Ausweg in Frage kommen, sollte der Ausfall dauerhaft sein.
+ Die Anwendung kann für bestimmte Zeit im "schreibgeschützten Modus" betrieben werden. 

Bei diesem Muster schaltet die Anwendung in den schreibgeschützten Modus um, sobald die Verbindung mit der sekundären Datenbank hergestellt wurde. Die Anwendungslogik in der primären Region ist mit der primären Datenbank zusammengestellt und wird im Lese-/Schreibmodus betrieben. Die Anwendungslogik in der sekundären Region ist mit der sekundären Datenbank zusammengestellt und wird im schreibgeschützten Modus betrieben. Traffic Manager muss so eingerichtet werden, dass für beide Anwendungsinstanzen das [Failoverrouting](traffic-manager-configure-failover-load-balancing.md) mit [Endpunktüberwachung](traffic-manager-monitoring.md) aktiviert wird.

Das folgende Diagramm veranschaulicht diese Konfiguration vor einem Ausfall. ![Abbildung 7](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-1.png)

Wenn Traffic Manager einen Ausfall der Verbindung mit der primären Region erkennt, wird der Datenverkehr der Benutzer automatisch zur Anwendungsinstanz in der sekundären Region umgeleitet. Bei diesem Muster dürfen Sie **kein** Datenbankfailover einleiten, nachdem der Ausfall erkannt wurde. Die Anwendung in der sekundären Region wird aktiviert und im schreibgeschützten Modus unter Verwendung der sekundären Datenbank betrieben. Dies wird im folgenden Diagramm veranschaulicht.

![Abbildung 8](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-2.png)

Nachdem die Ausfallursache in der primären Region beseitigt wurde, erkennt Traffic Manager die Wiederherstellung der Verbindung mit der primären Region und leitet den Datenverkehr der Benutzer wieder zur Anwendungsinstanz in der primären Region. Diese Anwendungsinstanz nimmt wieder den Betrieb im Lese-/Schreibmodus unter Verwendung der primären Datenbank auf.

> [AZURE.NOTE]Da dieses Muster einen schreibgeschützten Zugriff auf die sekundäre Datenbank verlangt, ist eine [aktive Georeplikation](https://msdn.microsoft.com/library/azure/dn741339.aspx) erforderlich.

Bei einem Ausfall in der sekundären Region markiert Traffic Manager den Anwendungsendpunkt in der primären Region als nicht einsatzbereit, und der Replikationskanal wird unterbrochen. Die Leistung der Anwendung wird jedoch während des Ausfalls nicht beeinträchtigt. Nach Behebung der Ausfallursache wird die sekundäre Datenbank sofort mit der primären synchronisiert. Während der Synchronisierung kann die Leistung der primären Datenbank geringfügig beeinträchtigt werden, was von der Menge der Daten abhängt, die synchronisiert werden muss.

![Abbildung 8](./media/sql-database-designing-cloud-solutions-for-disaster-recovery/pattern3-3.png)

Dieses Entwurfsmuster bietet mehrere **Vorteile**:

+ Datenverluste während temporärer Ausfälle werden verhindert.
+ Es muss keine Überwachungsanwendung bereitgestellt werden, da die Wiederherstellung von Traffic Manager ausgelöst wird. 
+ Ausfallzeiten hängen nur davon ab, wie schnell Traffic Manager den Verbindungsausfall erkennt, was konfigurierbar ist.

Die **Nachteile** sind:

+ Die Anwendung muss im schreibgeschützten Modus betrieben werden können.
+ Ein dynamisches Umschalten zu ihr ist erforderlich, wenn sie mit einer schreibgeschützten Datenbank verbunden ist.
+ Die Fortsetzung von Lese-/Schreibvorgängen erfordert die Wiederherstellung der primären Region, was bedeuten kann, dass der vollständige Datenzugriff stunden- oder sogar tagelang deaktiviert ist. 

> [AZURE.NOTE]Bei einem dauerhaften Dienstausfall in der Region müssen Sie das Datenbankfailover manuell aktivieren und den Datenverlust akzeptieren. Die Anwendung ist in der sekundären Region mit Lese-/ Schreibzugriff auf die Datenbank betriebsbereit.

## Zusammenfassung

Für Ihre spezifische Strategie für die Notfallwiederherstellung können diese Muster kombiniert oder erweitert werden, um die Anforderungen Ihrer Anwendung bestmöglich zu erfüllen. Wie bereits erwähnt, basiert die von Ihnen gewählte Strategie auf der SLA, die Sie Ihren Kunden anbieten möchten, und auf der Bereitstellungstopologie der Anwendung. Zum Erleichtern Ihrer Entscheidung werden in der folgenden Tabelle die Optionen basierend auf dem geschätzten Datenverlust bzw. auf dem RPO (Recovery Point Objective, Wiederherstellungspunkt-Ziel) und der geschätzten Wiederherstellungszeit verglichen.

| Muster | RPO | Geschätzte Wiederherstellungszeit 
| :--- |:--- | :--- 
| Aktiv-/Passiv-Bereitstellung für Notfallwiederherstellung mit zusammengestelltem Datenbankzugriff | Lese-/Schreibzugriff < 5 Sek. | Ausfallerkennungszeit + Aufruf der Failover-API + Anwendungsüberprüfungstest 
| Aktiv-/Aktiv-Bereitstellung für den Anwendungslastenausgleich | Lese-/Schreibzugriff < 5 Sek. | Ausfallerkennungszeit + Aufruf der Failover-API + Änderung der SQL-Verbindungszeichenfolge + Anwendungsüberprüfungstest
| Aktiv-/Passiv-Bereitstellung für die Beibehaltung von Daten | Schreibgeschützter Zugriff < 5 Sek. Lese-/ Schreibzugriff = 0 (null) | Schreibgeschützter Zugriff = Erkennungszeit für Verbindungsausfall + Anwendungsüberprüfungstest <br>Lese-/Schreibzugriff = Zeit zum Beseitigen der Ausfallursache 

<!---HONumber=Oct15_HO3-->