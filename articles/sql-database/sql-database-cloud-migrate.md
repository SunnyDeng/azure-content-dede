<properties
   pageTitle="Migrieren von Datenbanken zur Azure SQL-Datenbank"
	description="Microsoft Azure SQL-Datenbank, Datenbankbereitstellung, Datenbankmigration, Datenbank importieren, Datenbank exportieren, Migrations-Assistent"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="09/02/2015"
	ms.author="carlrab"/>

# Migrieren von Datenbanken zur Azure SQL-Datenbank

Azure SQL-Datenbank V12 sorgt für nahezu vollständige Modulkompatibilität mit SQL Server 2014 und höheren Versionen. Dies vereinfacht die Migration der meisten Datenbanken von einer lokalen Instanz von SQL Server 2005 oder höher zu einer Azure SQL-Datenbank erheblich. Bei vielen Datenbanken besteht die Migration in einer einfachen Verschiebung von Schemas und Daten, bei der nur wenige oder gar keine Schemaänderungen und nur wenige oder gar keine Umstrukturierungsmaßnahmen für Anwendungen erforderlich sind. Wenn Datenbanken geändert werden müssen, ist dies in einem engeren Rahmen möglich.

Standardmäßig werden die serverbezogenen Features von SQL Server nicht von Azure SQL-Datenbank V12 unterstützt. Auf diesen Features beruhende Datenbanken und Anwendungen müssen vor der Migration etwas umstrukturiert werden. Azure SQL-Datenbank V12 verbessert zwar die Kompatibilität mit einer lokalen SQL Server-Datenbank, die Migration muss jedoch trotzdem sorgfältig geplant und durchgeführt werden. Dies gilt insbesondere bei umfangreichen, komplexen Datenbanken.

## Ermitteln der Kompatibilität
Um zu ermitteln, ob Ihre lokale SQL Server-Datenbank mit Azure SQL-Datenbank V12 kompatibel ist, haben Sie zwei Möglichkeiten: Sie können die Migration mit einer der beiden Methoden beginnen, die weiter unten in der ersten Option erläutert werden, und sich ansehen, ob die Schemaüberprüfungsroutinen eine Inkompatibilität feststellen. Oder Sie können die Kompatibilität mithilfe von SQL Server Data Tools in Visual Studio prüfen, wie in der zweiten Option beschrieben. Sollten für Ihre lokale SQL Server-Datenbank Kompatibilitätsprobleme festgestellt werden, können Sie diese mithilfe von SQL Server Data Tools in Visual Studio oder mithilfe von SQL Server Management Studio beheben.

## Migrationsmethoden
Eine kompatible lokale SQL Server-Datenbank lässt sich auf verschiedene Arten zu Azure SQL-Datenbank V12 migrieren.

- Bei kleineren bis mittelgroßen Datenbanken muss zum Migrieren kompatibler Datenbanken ab SQL Server 2005 lediglich in SQL Server Management Studio der Assistent zum Bereitstellen der Datenbank für die Microsoft Azure-Datenbank ausgeführt werden – vorausgesetzt, es liegen keine Verbindungsprobleme (keine Verbindung, geringe Bandbreite oder Timeoutprobleme) vor.
- Bei mittelgroßen bis großen Datenbanken oder bei Verbindungsproblemen können Sie die Daten und das Schema in SQL Server Management Studio in eine BACPAC-Datei (lokal oder in einem Azure-Blob) exportieren und die BACPAC-Datei anschließend in Ihre Azure SQL-Instanz importieren. Wenn Sie die BACPAC-Datei in einem Azure-Blob speichern, können Sie sie auch über das Azure-Portal importieren. Weitere Informationen zu einer BACPAC-Datei finden Sie unter [Datenebenenanwendungen](https://msdn.microsoft.com/library/ee210546.aspx).
- Bei größeren Datenbanken empfiehlt es sich, Schema und Daten separat zu migrieren. Sie können das Schema mit SQL Server Management Studio oder Visual Studio in ein Datenbankprojekt extrahieren und das Schema dann bereitstellen, um die Azure SQL-Datenbank zu erstellen. Anschließend können Sie die Daten per BCP extrahieren und sie unter Verwendung paralleler Streams in die Azure SQL-Datenbank importieren. Die Migration einer umfangreichen, komplexen Datenbank dauert mehrere Stunden – unabhängig von der gewählten Methode.

### Option 1:
******Migrieren kompatibler Datenbanken mit SQL Server Management Studio***

SQL Server Management Studio bietet zwei Methoden für die Migration einer kompatiblen lokalen SQL Server-Datenbank zu einer Azure SQL-Datenbank: Sie können entweder den Assistenten zum Bereitstellen der Datenbank für die Microsoft Azure SQL-Datenbank verwenden oder die Datenbank in eine BACPAC-Datei exportieren und diese anschließend importieren, um eine neue Azure SQL-Datenbank zu erstellen. Der Assistent überprüft die Kompatibilität mit Azure SQL-Datenbank V12, extrahiert das Schema und die Daten in eine BACPAC-Datei und importiert sie anschließend in die angegebene Azure SQL-Datenbankinstanz. Informationen zum Verwenden dieser Option finden Sie unter [Verwenden von SSMS](sql-database-migrate-ssms.md).

### Option 2:
***Aktualisieren von Datenbankschemata mit Visual Studio (offline) und Bereitstellen mit SQL Server Management Studio***

Wenn Ihre lokale SQL Server-Datenbank nicht kompatibel ist oder Sie ermitteln möchten, ob sie kompatibel ist, können Sie das Datenbankschema zur Analyse in ein Visual Studio-Datenbankprojekt importieren. Für die Analyse muss SQL-Datenbank V12 als Zielplattform angegeben und das Projekt erstellt werden. Wenn der Erstellungsvorgang erfolgreich ist, ist die Datenbank kompatibel. Ist der Erstellungsvorgang nicht erfolgreich, können Sie die Fehler in SQL Server Data Tools für Visual Studio (SSDT) beheben. Wenn sich das Projekt erfolgreich erstellen lässt, können Sie es als Kopie der Quelldatenbank veröffentlichen und die Daten anschließend unter Verwendung des Datenvergleichsfeatures von SSDT aus der Quelldatenbank in die Azure SQL V12-kompatible Datenbank kopieren. Diese aktualisierte Datenbank wird dann mithilfe von Option 1 in Azure SQL-Datenbank bereitgestellt. Wenn nur das Schema migriert werden muss, kann das Schema direkt in Visual Studio für die Azure SQL-Datenbank veröffentlicht werden. Diese Methode kann verwendet werden, wenn das Datenbankschema mehr Änderungen erfordert, als der Migrations-Assistent allein verarbeiten kann. Informationen zum Verwenden dieser Option finden Sie unter [Verwenden von Visual Studio](sql-database-migrate-visualstudio-ssdt.md).

## Entscheidungsoptionen
- Wenn Sie davon ausgehen, dass eine Datenbank ohne Änderungen migriert werden kann, sollten Sie Option 1 verwenden, die schnell und einfach ist. Wenn Sie unsicher sind, beginnen Sie damit, eine Nur-Schema-BACPAC-Datei aus der Datenbank zu exportieren, wie unter Option 1 beschrieben. Wenn der Export ohne Fehler durchgeführt wird, können Sie zum Migrieren der Datenbank mit den Daten Option 1 verwenden.  
- Sollten im Rahmen der ersten Option Exportfehler auftreten, verwenden Sie die zweite Option, und reparieren Sie das Datenbankschema offline in Visual Studio mit einer Kombination aus Migrations-Assistent und manuellen Schemaänderungen. Eine Kopie der Quelldatenbank wird dann lokal aktualisiert und anschließend mit Option 1 nach Azure migriert.

## Migrationstools
Zu den verwendeten Tools gehören SQL Server Management Studio (SSMS) und die SQL Server-Tools in Visual Studio (VS, SSDT) sowie das Azure-Portal.

> Achten Sie darauf, dass Sie jeweils die neueste Version der Clienttools installieren, da ältere Versionen nicht mit Azure SQL-Datenbank V12 kompatibel sind.

### SQL Server Management Studio (SSMS)
Mit SSMS kann eine kompatible Datenbank direkt in Azure SQL-Datenbank bereitgestellt oder eine logische Sicherung der Datenbank als BACPAC-Datei exportiert werden, die dann, ebenfalls mit SSMS, importiert werden kann, um eine neue Azure SQL-Datenbank zu erstellen.

[Aktuelle Version von SSMS herunterladen](https://msdn.microsoft.com/library/mt238290.aspx)

### SQL Server-Tools in Visual Studio (VS, SSDT)
Die SQL Server-Tools in Visual Studio können zum Erstellen und Verwalten eines Datenbankprojekts verwendet werden, das eine Reihe von Transact-SQL-Dateien für jedes Objekt im Schema enthält. Das Projekt kann aus einer Datenbank oder einer Skriptdatei importiert werden. Nach dem Erstellen kann das Projekt in Azure SQL-Datenbank V12 übernommen werden. Beim Erstellen des Projekts wird dann die Schemakompatibilität überprüft. Durch Klicken auf einen Fehler wird die entsprechende Transact-SQL-Datei geöffnet, sodass diese bearbeitet und der Fehler behoben werden kann. Sobald alle Fehler behoben sind, kann das Projekt veröffentlicht werden, entweder in einer SQL-Datenbank, um eine leere Datenbank zu erstellen, oder in (einer Kopie) der ursprünglichen SQL Server-Datenbank, um das Schema zu aktualisieren. Dadurch kann die Datenbank wie oben beschrieben mithilfe von SSMS mit ihren Daten bereitgestellt werden.

Verwenden Sie die [aktuelle SSDT-Version (SQL Server Data Tools) für Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx) mit Visual Studio 2013 Update 4 oder höher.

## Vergleiche
| Option 1: | Option 2: |
| ------------ | ------------ | ------------ |
| Bereitstellen einer kompatiblen Datenbank in Azure SQL-Datenbank | Lokales Aktualisieren einer Datenbank und Bereitstellen in Azure SQL-Datenbank |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![Offlinebearbeitung](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| SSMS | VS und SSMS |
|Einfacher Prozess erfordert ein kompatibles Schema. Schema wird unverändert migriert. | Schema wird in ein Datenbankprojekt in Visual Studio importiert. Zusätzliche Updates erfolgen mithilfe von SSDT für Visual Studio und des abschließenden Schemas zum lokalen Aktualisieren der Datenbank. |
| Es wird immer die gesamte Datenbank bereitgestellt oder exportiert. | Es besteht vollständige Kontrolle über die Objekte, die in der Migration enthalten sein sollen. |
| Keine Möglichkeit zum Ändern der Ausgabe, wenn Fehler vorhanden sind; das Quellschema muss kompatibel sein. | Sämtliche Features von SSDT für Visual Studio verfügbar. Schema wird offline geändert. | Anwendungsvalidierung erfolgt in Azure. Meist minimal, da das Schema ohne Änderungen migriert wird. | Anwendungsvalidierung kann in SQL Server erfolgen, bevor die Datenbank in Azure bereitgestellt wird. |
| Einfach zu konfigurierender Prozess mit ein oder zwei Schritten. | Komplexerer Prozess mit mehreren Schritten (einfacher, wenn nur ein Schema bereitgestellt wird). |

<!---HONumber=September15_HO1-->