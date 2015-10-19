<properties
   pageTitle="Migration: Data Warehouse-Dienstprogramm zur Migration | Microsoft Azure"
   description="Migrieren nach SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="lodipalm"/>


#Data Warehouse-Dienstprogramm für die Migration (Vorschau)
Das Data Warehouse-Dienstprogramm für die Migration ist ein Tool zum Migrieren von Schemas und Daten aus SQL Server und der Azure SQL-Datenbank nach Azure SQL Data Warehouse. Während der Migration des Schemas ordnet das Tool automatisch das entsprechende Schema aus der Quelle zum Ziel zu. Nachdem das Schema migriert wurde, haben Benutzer außerdem die Möglichkeit, Daten über automatisch erstellte Skripts zu verschieben.

Zusätzlich zur Schema- und Datenmigration bietet dieses Tool Benutzern die Option, Kompatibilitätsberichte zu generieren, die Inkompatibilitäten zwischen den Ziel- und Quellinstanzen zusammenzufassen, die eine optimierte Migration verhindern würden.

##Erste Schritte
Das Data Warehouse-Migrationsdienstprogramm kann [hier][] heruntergeladen werden. Als Voraussetzung für die Installation benötigen Sie das BCP-Befehlszeilenhilfsprogramm zum Ausführen von Migrationsskripts und Office, um den Kompatibilitätsbericht anzuzeigen. Nach dem Starten der ausführbaren Datei, die heruntergeladen wird, werden Sie aufgefordert, eine standardmäßigen Endbenutzerlizenzvertrag zu akzeptieren, bevor das Tool installiert wird.

###Starten des Tools und Herstellen einer Verbindung
Das Tool kann problemlos durch Klicken auf das Desktopsymbol gestartet werden, das nach der Installation angezeigt wird. Beim Öffnen des Tools werden Sie auf eine Seite der Erstverbindung weitergeleitet, wo Sie die Quelle und das Ziel des Migrationstools auswählen können. Zu diesem Zeitpunkt werden SQL Server und Azure SQL-Datenbank als Quellen und SQL Data Warehouse als Ziel unterstützt. Nach dieser Auswahl werden Sie dazu aufgefordert, eine Verbindung mit dem Quellserver herzustellen, indem der Servername ausgefüllt und authentifiziert wird. Klicken Sie dann auf "Verbinden".
 
Nach der Authentifizierung zeigt das Tool eine Liste der Datenbanken an, die auf dem verbundenen Server vorhanden sind. Sie können mit der Migration durch Auswählen einer Datenbank beginnen, die Sie migrieren möchten. Klicken Sie anschließend auf "Migrieren ausgewählt".
 
##Migrationsbericht
Durch das Auswählen von "Überprüfen der Datenbank-Kompatibilität" im Tool wird ein Bericht generiert, der alle Inkompatibilitäten in den Objekten der Datenbank zusammenfasst, die Sie migrieren möchten. Eine umfassendere Liste der SQL Server-Funktionen, die nicht in SQL Data Warehouse vorhanden sind, finden Sie der [Migrationsdokumentation][]. Nachdem der Bericht generiert ist, können Sie ihn in Excel speichern und öffnen.

Beachten Sie, dass beim Generieren des Migrationsschemas die meisten als "Object" erkannte Probleme angepasst werden, um die sofortige Migration von Daten zu ermöglichen. Informieren Sie sich über die Änderungen, um sicherzustellen, dass keine zusätzliche Anpassungen vorgenommen werden sollen, bevor das Schema angewendet wird.

##Migrieren des Schemas

Nach dem Herstellen einer Verbindung generiert "Schema migrieren" ein Skript zur Schemamigration für die ausgewählten Tabellen. Dieses Skript portiert die Struktur der Tabelle, weist nicht-kompatible Datentypen kompatibleren Formen zu, und erstellt Sicherheitsanmeldeinformationen und ein Schema, wenn dies vom Benutzer in den Migrationseinstellungen angegeben wurde. Dieser Code kann gegen die SQL Data Warehouse-Zielinstanz ausgeführt, in einer Datei gespeichert, in die Zwischenablage kopiert oder sogar inline bearbeitet werden, bevor weitere Aktionen erfolgen.
 
Wie oben erwähnt sollten Sie beim Migrieren des Schemas die Migrationsänderungen überprüfen, die durch das Tool vorgenommen wurden, um sicherzustellen, dass Sie sie vollständig verstehen.

##Migrieren von Daten

Durch Klicken auf die Option „Migrieren von Daten“ können Sie BCP-Skripts erstellen, die Ihre Daten zuerst in Flatfiles auf den Server verschieben und dann direkt in das SQL Data Warehouse. Wir empfehlen diesen Prozess für das Verschieben von kleinen Datenmengen. Da wir keine integrierten Wiederholungen haben, sollten Sie sich bewusst sein, dass bei Netzwerkausfällen Fehler auftreten können. Für die Ausführung muss das BCP-Befehlszeilen-Dienstprogramm installiert sein und das Schema für die Daten bereits erstellt sein.
 
Nachdem Sie die oben aufgeführten Parameter ausgefüllt haben, müssen Sie lediglich auf „Migration ausführen“ klicken, sodass ein Satz von zwei Paketen am angegebenen Speicherort generiert wird. Führen Sie die Exportdatei zum Exportieren von Daten aus Ihrer Migrationsquelle in Flatfiles aus, und führen Sie die Importdatei zum Importieren Ihrer Daten SQL Data Warehouse aus.

## Nächste Schritte
Nachdem Sie nun einige Daten migriert haben, schauen Sie sich an, die das [Entwickeln][] funktioniert.

<!--Image references-->

<!--Article references-->
[Migrationsdokumentation]: https://azure.microsoft.com/de-DE/documentation/articles/sql-data-warehouse-overview-migrate/
[Entwickeln]: https://azure.microsoft.com/de-DE/documentation/articles/sql-data-warehouse-overview-develop/
[hier]: https://migrhoststorage.blob.core.windows.net/sqldwsample/DataWarehouseMigrationUtility.zip

<!---HONumber=Oct15_HO2-->