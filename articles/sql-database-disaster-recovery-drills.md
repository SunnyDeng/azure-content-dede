<properties 
   pageTitle="Notfallwiederherstellungsverfahren für SQL-Datenbank" 
   description="Hier erhalten Sie Anleitungen und bewährte Methoden für die Verwendung von Azure SQL-Datenbank zum Ausführen von Notfallwiederherstellungsverfahren, mit denen Sie wichtige Geschäftsanwendungen vor Fehlern und Ausfällen schützen." 
   services="sql-database" 
   documentationCenter="" 
   authors="mihaelablendea" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/13/2015"
   ms.author="mihaelab"/>

#Ausführen von Notfallwiederherstellungsverfahren

Es wird empfohlen, in regelmäßigen Abständen Überprüfungen der Anwendungsbereitschaft für den Wiederherstellungsworkflow durchzuführen. Überprüfungen des Anwendungsverhaltens und der Auswirkungen von Datenverlusten und/oder Unterbrechungen durch Failover gehören zu einem angemessenen Entwicklungsverfahren. Solche Überprüfungen werden auch für die meisten Branchenstandards im Zuge von Zertifizierungen zur Geschäftskontinuität vorausgesetzt.

Notfallwiederherstellungsverfahren umfassen Folgendes:

- Simulieren von Ausfällen auf Datenebene
- Wiederherstellen 
- Überprüfen der Anwendungsintegrität nach der Wiederherstellung

Je nachdem, welchem [Entwurf für Geschäftskontinuität](sql-database-business-continuity.md) Sie folgen, kann der verbundene Workflow variieren. Im Folgenden beschreiben wir bewährte Methoden zur Ausführung von Notfallwiederherstellungen im Kontext von Azure SQL-Datenbank.

##Geografische Wiederherstellung

Um Datenverluste beim Durchführen von Notfallwiederherstellungsverfahren zu verhindern, empfehlen wir die Verwendung einer Testumgebung, indem Sie eine Kopie der Produktionsumgebung erstellen und diese zum Überprüfen des Failoverworkflows der Anwendung verwenden.
 
####Simulieren von Ausfällen

- Simulieren Sie den Ausfall, indem Sie die Quelldatenbank entweder löschen oder umbenennen und dadurch einen Verbindungsfehler für die Anwendung verursachen. Auf diese Weise können Sie die Erkennung von und Warnungen zu Ausfällen überprüfen und die RTO für die Wiederherstellungsdauer messen.

####Wiederherstellen

- Führen Sie die geografische Wiederherstellung der Datenbank auf einem anderen Server aus, wie [hier](sql-database-disaster-recovery.md) beschrieben. 
- Ändern Sie die Anwendungskonfiguration zum Herstellen einer Verbindung mit den wiederhergestellten Datenbanken, und folgen Sie der Anleitung unter [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md) zum Abschließen der Wiederherstellung.

####Überprüfen

- Führen Sie das Verfahren aus, indem Sie die Anwendungsintegrität nach der Wiederherstellung sicherstellen (Verbindungszeichenfolgen, Anmeldungen, grundlegende Funktionstests oder andere Überprüfungen, die Teil der Standardabmeldeverfahren von Anwendungen sind).

##Standardmäßige Georeplikation

Eine Datenbank, die durch die standardmäßige Georeplikation geschützt ist, kann nur eine nicht lesbare sekundäre Datenbank haben. Das Verfahren beinhaltet erzwungenes Beenden der Verbindung, wodurch der Schutz der Datenbank aufgehoben wird. Darüber hinaus besteht die Möglichkeit von Datenverlusten. Daher raten wir Kunden davon ab, diese Tests auf Produktionsdatenbanken auszuführen. Wie empfehlen stattdessen, eine Kopie der Produktionsumgebung zu erstellen und diese zum Überprüfen des Failoverworkflows der Anwendung zu verwenden.

####Simulieren von Ausfällen

- Simulieren Sie die Workloads auf der primären Datenbank. Wenn die primäre Datenbank zum Zeitpunkt der Beendigung aktiv ist, können Datenverluste auftreten, die das Verfahren realistischer machen.
- Löschen Sie die primäre Datenbank, oder [erzwingen Sie das Beenden](sql-database-disaster-recovery.md) der Verbindung ausgehend von der sekundären Datenbank.

####Wiederherstellen

- Ändern Sie die Anwendungskonfiguration so, dass eine Verbindung mit der alten schreibgeschützten, sekundären Datenbank hergestellt wird. Auf diese Datenbank kann daraufhin vollständig zugegriffen werden und die Anwendung kann sie als neue primäre Datenbank verwenden. 
- Folgen Sie den Anleitungen unter [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md) zum Abschließen der Wiederherstellung.

####Überprüfen

- Führen Sie das Verfahren aus, indem Sie die Anwendungsintegrität nach der Wiederherstellung sicherstellen (Verbindungszeichenfolgen, Anmeldungen, grundlegende Funktionstests oder andere Überprüfungen, die Teil der Standardabmeldeverfahren von Anwendungen sind).

##Aktive Georeplikation

Die Notfallwiederherstellungsverfahren werden unter Verwendung eines parallelen Zielservers und eines weiteren Satzes von schreibgeschützten sekundären Datenbanken durchgeführt. Eine Testversion der Anwendungsebene sollte dazu verwendet werden, die Vorgangs- und Datenintegrität zu überprüfen, indem Sie nach dem erzwungenen Beenden Tests auf diesem Server ausführen.

####Simulieren von Ausfällen

- [Erstellen Sie eine neue aktive Georeplikationsverbindung](sql-database-business-continuity-design.md) aus der primären Datenbank auf einem sekundären Testserver. Wenn die primäre Datenbank zum Zeitpunkt der Beendigung aktiv ist, können Datenverluste auftreten, die das Verfahren realistischer machen.
- [Erzwingen Sie das Beenden](sql-database-disaster-recovery.md) der Verbindung ausgehend von der sekundären Datenbank, die sich auf dem Testserver befindet.

####Wiederherstellen

- Ändern Sie die Anwendungskonfiguration so, dass eine Verbindung mit der alten schreibgeschützten sekundären Datenbank hergestellt wird, die nach dem Beenden für Schreibvorgänge verfügbar ist.
- Folgen Sie den Anleitungen unter [Abschließen einer wiederhergestellten Datenbank](sql-database-recovered-finalize.md) zum Abschließen der Wiederherstellung.

####Überprüfen

- Führen Sie das Verfahren aus, indem Sie die Anwendungsintegrität nach der Wiederherstellung sicherstellen (Verbindungszeichenfolgen, Anmeldungen, grundlegende Funktionstests oder andere Überprüfungen, die Teil der Standardabmeldeverfahren von Anwendungen sind).

<!---HONumber=58-->