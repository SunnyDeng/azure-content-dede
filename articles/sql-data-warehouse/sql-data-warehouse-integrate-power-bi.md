<properties
   pageTitle="Verwenden von Power BI mit SQL Data Warehouse | Microsoft Azure"
   description="Tipps zur Verwendung von Power BI in Azure SQL Data Warehouse zum Entwickeln von Lösungen."
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
   ms.date="03/03/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Verwenden von Power BI mit SQL Data Warehouse
Wie bei Azure SQL-Datenbank ermöglicht auch SQL Data Warehouse Direct Connect leistungsfähige logische Pushdowns in Verbindung mit den Analysefunktionen von Power BI. Abfragen werden mit Direct Connect in Echtzeit zurück an Ihr Azure SQL Data Warehouse gesendet, während Sie die Daten durchsuchen. Dadurch haben Benutzer in Kombination mit der Skalierung von SQL Data Warehouse die Möglichkeit, in Minuten dynamische Berichte für Terabytes an Daten zu erstellen. Darüber hinaus ermöglicht die Einführung der Schaltfläche "Open in Power BI" Benutzern, direkt eine Verbindung zwischen Power BI und SQL Data Warehouse herzustellen, ohne dass Informationen aus anderen Teilen von Azure gesammelt werden müssen.

Beachten Sie bei der Verwendung von Direct Connect Folgendes:

+ Geben Sie beim Herstellen einer Verbindung den vollqualifizierten Servernamen an (weitere Informationen im Folgenden)
+ Stellen Sie sicher, dass die Firewallregeln für die Datenbank mit "Zugriff auf Azure-Dienste erlauben" konfiguriert sind.
+ Für jede Aktion, wie z. B. das Auswählen einer Spalte oder das Hinzufügen eines Filters, wird das Data Warehouse direkt abgefragt.
+ Kacheln werden ca. alle 15 Minuten aktualisiert (die Aktualisierung muss nicht geplant werden).
+ Fragen und Antworten sind nicht für Direct Connect-DataSets verfügbar.
+ Schemaänderungen werden nicht automatisch übernommen.

Diese Einschränkungen und Hinweise können sich ändern, da wir die Umgebung weiter verbessern. Die Schritte zum Herstellen der Verbindung werden nachfolgend beschrieben.

## Mithilfe der Schaltfläche "Open in Power BI"
Die einfachste Möglichkeit zum Navigieren zwischen Ihrem SQL Data Warehouse und Power BI ist über die Schaltfläche "Open in Power BI". Mit dieser Schaltfläche können Sie nahtlos beginnen, neue Dashboards in Power BI zu erstellen.

1.	Navigieren Sie zunächst zu Ihrer SQL Data Warehouse-Instanz im klassischen Azure-Portal.
2.	Klicken Sie auf die Schaltfläche "Open in Power BI".
3.	Wenn Sie nicht direkt angemeldet werden können oder wenn Sie nicht über ein Power BI-Konto verfügen, müssen Sie sich anmelden.  
4.	Sie werden auf die SQL Data Warehouse-Verbindungsseite weitergeleitet, die bereits mit den Informationen aus dem SQL Data Warehouse ausgefüllt ist.
5.  Nachdem Sie Ihre Anmeldeinformationen eingegeben haben, ist die Verbindung mit SQL Data Warehouse vollständig hergestellt.

## Herstellen einer Verbindung über das Power BI-Portal
Zusätzlich zur Schaltfläche "Open in Power BI" können Benutzer auch über das Power BI-Portal eine Verbindung mit dem SQL Data Warehouse herstellen.

1.  Klicken Sie unten im Navigationsbereich auf „Get Data“.
2.  Wählen Sie „Databases“ aus.
3.  Wenn die Seite „Databases“ angezeigt wird, wählen Sie „Azure SQL Data Warehouse“ aus, und klicken Sie dann auf „Connect“.
4.  Geben Sie die erforderlichen Verbindungsinformationen ein. Im Abschnitt „Finding Parameters“ unten wird gezeigt, wo Sie diese Daten finden.
5.  Sie gelangen zurück zur Hauptseite von Power BI, und nachdem die Verbindung hergestellt ist, wird unter „Datasets“ ein neuer Eintrag mit dem Namen Ihrer Instanz angezeigt.  
6.	 Sie können auf das neue Dataset klicken, um alle Tabellen und Sichten in der Datenbank zu untersuchen. Durch die Auswahl einer Spalte wird eine Abfrage zurück an die Quelle gesendet und dynamisch ein visuelles Element erstellt. Diese visuellen Elemente können in einem neuen Bericht gespeichert und wieder an Ihr Dashboard angeheftet werden.

## Suchen nach Parameterwerten
Ihre vollqualifizierten Servernamen und Datenbanknamen finden Sie im klassischen Azure-Portal. Bitte beachten Sie, dass SQL Data Warehouse zu diesem Zeitpunkt nur im Azure-Portal verfügbar ist.


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop/
[SQL Data Warehouse integration overview]: ./sql-data-warehouse-overview-integration/

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0309_2016-->