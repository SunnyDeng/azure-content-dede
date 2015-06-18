<properties 
   pageTitle="Migration zu Azure SQL-Datenbank" 
   description="Microsoft Azure SQL-Datenbank, Datenbankbereitstellung, Datenbankmigration, Datenbank importieren, Datenbank exportieren, Migrations-Assistent" 
   services="sql-database" 
   documentationCenter="" 
   authors="pehteh" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/15/2015"
   ms.author="pehteh"/>

# Übersicht
Azure SQL-Datenbank V12 bringt nahezu vollständige Modulkompatibilität mit SQL Server 2014. Somit vereinfacht diese Version erheblich die Migration der meisten Datenbanken von SQL Server in Azure SQL-Datenbank. Die Migration vieler Datenbanken ist ein unkomplizierter Verschiebungsvorgang, für den höchstens wenige Änderungen am Schema und nur wenige oder keine Umstrukturierungen von Anwendungen erforderlich sind. Wenn Datenbanken geändert werden müssen, ist der Rahmen dieser Änderungen stärker eingeschränkt.

Standardmäßig werden Server-Funktionen von SQL Server in SQL-Datenbank nicht unterstützt, daher müssen Datenbanken und Anwendungen, die diese Funktionen benötigen, weiterhin umstrukturiert werden, bevor sie migriert werden können. Auch wenn SQL-Datenbank V12 die Kompatibilität mit SQL Server verbessert, muss die Migration immer noch geplant und sorgfältig durchgeführt werden, besonders bei größeren komplexeren Datenbanken.

## Auf einen Blick
Es gibt verschiedene Ansätze für die Migration einer SQL Server-Datenbank nach Azure, bei denen jeweils ein oder mehrere Tools verwendet werden. Einige Verfahren sind schnell und einfach, während andere länger vorbereitet werden müssen. Bedenken Sie dabei, dass die Migration großer, komplexer Datenbanken viele Stunden dauern kann.

### Option 1:
***Migrieren kompatibler Datenbanken mit SQL Server Management Studio (SSMS)***

Die Datenbank wird mit SSMS in Azure SQL-Datenbank bereitgestellt. Die Datenbank kann direkt bereitgestellt oder in eine BACPAC-Datei exportiert werden, die dann zum Erstellen einer neuen Azure SQL-Datenbank importiert wird. Dies kann verwendet werden, wenn die Quelldatenbank mit Azure SQL-Datenbank vollständig kompatibel ist.

### Option 2:
***Migrieren einer nahezu kompatiblen Datenbank mit dem Azure SQL-Datenbankmigrations-Assistenten (SAMW)***

Die Datenbank wird mit dem Azure SQL-Datenbankmigrations-Assistenten verarbeitet, um ein Migrationsskript mit Schema oder mit Schema und Daten im BCP-Format zu generieren. Dies kann verwendet werden, wenn das Datenbankschema aktualisiert werden muss und die Änderungen vom Assistenten verarbeitet werden können.

### Option 3:
***Aktualisieren von Datenbankschemas offline mit Visual Studio (VS) und SAMW und Bereitstellen mit SSMS***

Die Quelldatenbank wird in ein Visual Studio-Datenbankprojekt für die Offlineverarbeitung importiert. Der Azure SQL-Datenbankmigrations-Assistent wird dann über alle Skripts im Projekt ausgeführt, um eine Reihe von Transformationen und Korrekturen zu übernehmen. Das Projekt wird auf SQL-Datenbank V12 ausgerichtet und dafür erstellt, und alle übrigen Fehler werden gemeldet. Diese Fehler werden dann manuell mithilfe der SQL Server-Tools in Visual Studio aufgelöst. Nachdem das Projekt erfolgreich erstellt wurde, wird es in einer Kopie der Quelldatenbank wieder veröffentlicht. Diese aktualisierte Datenbank wird dann mithilfe von Option 1 in Azure SQL-Datenbank bereitgestellt. Wenn nur die Schema-Migration erforderlich ist, kann das Schema direkt aus Visual Studio in Azure SQL-Datenbank veröffentlicht werden. Dies kann verwendet werden, wenn für das Datenbankschema weitere Änderungen erforderlich sind, die mit SAMW alleine verarbeitet werden können.

## Entscheidungsoptionen
- Wenn Sie davon ausgehen, dass eine Datenbank ohne Änderungen migriert werden kann, sollten Sie Option 1 verwenden, die schnell und einfach ist. Wenn Sie unsicher sind, beginnen Sie damit, eine Nur-Schema-BACPAC-Datei aus der Datenbank zu exportieren, wie unter Option 1 beschrieben. Wenn der Export ohne Fehler durchgeführt wird, können Sie zum Migrieren der Datenbank mit den Daten Option 1 verwenden.  
- Wenn beim Export gemäß Option 1 Fehler auftreten, verarbeiten Sie die Datenbank mit dem Azure SQL-Datenbankmigrations-Assistenten (SAMW) im Nur-Schema-Modus, wie unter Option 2 beschrieben. Wenn SAMW keine Fehler meldet, kann Option 2 verwendet werden. 
- Wenn SAMW meldet, dass für das Schema weitere Bearbeitungen und nicht nur geringe Fehlerbehebungen notwendig sind, sollte am besten Option 3 verwendet werden und das Datenbankschema offline in Visual Studio mit einer Kombination aus SAMW und manuellen Schemaänderungen korrigiert werden. Eine Kopie der Quelldatenbank wird dann lokal aktualisiert und anschließend mit Option 1 nach Azure migriert.

## Migrationstools
Zu den verwendeten Tools gehören SQL Server Management Studio (SSMS), die SQL Server-Tools in Visual Studio (VS, SSDT) und der Azure SQL-Datenbankmigrations-Assistent (SAMW) sowie das Azure-Portal.

> Achten Sie darauf, dass Sie die neuesten Versionen der Clienttools installieren, da frühere Versionen nicht mit SQL-Datenbank V12 kompatibel sind.

### SQL Server Management Studio (SSMS)
Mit SSMS kann eine kompatible Datenbank direkt in Azure SQL-Datenbank bereitgestellt oder eine logische Sicherung der Datenbank als BACPAC-Datei exportiert werden, die dann, ebenfalls mit SSMS, importiert werden kann, um eine neue Azure SQL-Datenbank zu erstellen.

Sie müssen die neueste Version von SSMS verwenden (CU6 in SQL Server 2013 und höher) oder die [neueste Version](http://msdn.microsoft.com/evalcenter/dn434042.aspx) des Tools herunterladen.

### Azure SQL-Datenbankmigrations-Assistent (SAMW)
Mit SAMW kann das Schema einer vorhandenen Datenbank auf Kompatibilität mit Azure SQL-Datenbank analysiert werden. In vielen Fällen ist es auch möglich, ein T-SQL-Skript mit Schemas und Daten zu generieren und bereitzustellen. Der Assistent meldet Fehler während der Transformation, wenn Schemainhalte erkannt werden, die nicht transformiert werden können. In diesem Fall muss das generierte Skript weiter bearbeitet werden, bevor es erfolgreich bereitgestellt werden kann. SAMW verarbeitet den Hauptteil von Funktionen oder gespeicherten Prozeduren, der normalerweise von der Validierung durch die SQL Server-Tools in Visual Studio (siehe unten) ausgenommen ist, daher werden möglicherweise Probleme erkannt, die anderenfalls bei der Überprüfung in Visual Studio möglicherweise nicht gemeldet werden. Eine Kombination von SAMW mit den SQL Server-Tools in Visual Studio kann den erforderlichen Arbeitsaufwand für die Migration von komplexen Schemas erheblich reduzieren.

Achten Sie darauf, die neueste Version des [Azure SQL-Datenbankmigrations-Assistenten](http://sqlazuremw.codeplex.com/) von CodePlex zu verwenden.

### SQL Server-Tools in Visual Studio (VS, SSDT)
Die SQL Server-Tools in Visual Studio können zum Erstellen und Verwalten eines Datenbankprojekts verwendet werden, das eine Reihe von T-SQL-Dateien für jedes Objekt im Schema enthält. Das Projekt kann aus einer Datenbank oder einer Skriptdatei importiert werden. Nach dem Erstellen kann das Projekt in Azure SQL-Datenbank V12 übernommen werden. Beim Erstellen des Projekts wird dann die Schemakompatibilität überprüft. Durch Klicken auf einen Fehler wird die entsprechende T-SQL-Datei geöffnet, sodass diese bearbeitet und der Fehler behoben werden kann. Sobald alle Fehler behoben sind, kann das Projekt veröffentlicht werden, entweder in einer SQL-Datenbank, um eine leere Datenbank zu erstellen, oder in (einer Kopie) der ursprünglichen SQL Server-Datenbank, um das Schema zu aktualisieren. Dadurch kann die Datenbank wie oben beschrieben mithilfe von SSMS mit ihren Daten bereitgestellt werden.

Sie müssen die neueste Version der Azure SQL-Datenbank-Tools für Visual Studio für das aktuelle Update V12 von SQL Server-Datenbank verwenden. Stellen Sie sicher, dass Visual Studio 2013 mit Update 4 installiert ist.

## Vergleiche
| Option 1: | Option 2: | Option 3: |
| ------------ | ------------ | ------------ |
| Bereitstellen einer kompatiblen Datenbank in Azure SQL-Datenbank | Generieren eines Migrationsskripts mit Änderungen und Ausführen in Azure SQL-Datenbank | Lokales Aktualisieren einer Datenbank und Bereitstellen in Azure SQL-Datenbank |
|![SSMS](./media/sql-database-cloud-migrate/01SSMSDiagram.png)| ![SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png) | ![Offlinebearbeitung](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png) |
| SSMS | SAMW | SAMW, VS, SSMS |
|Einfacher Prozess erfordert ein kompatibles Schema. Schema wird unverändert migriert. | T-SQL-Skript wird durch SAMW generiert und enthält Änderungen, die zum Sicherstellen der Kompatibilität erforderlich sind. Einige nicht unterstützte Funktionen werden aus dem Schema gelöscht, die meisten werden als Fehler gekennzeichnet. | Schema wird in ein Datenbankprojekt in Visual Studio importiert und (optional) mit SAMW transformiert. Zusätzliche Updates erfolgen mithilfe der SQL Server-Tools in Visual Studio und des abschließenden Schemas zum lokalen Aktualisieren der Datenbank. |
| Wenn Sie eine BACPAC-Datei exportieren, können Sie auch nur das Schema migrieren. | Der Assistent kann so konfiguriert werden, dass das Schema oder das Schema und die Daten im Skript berücksichtigt werden. | Das Schema kann nur direkt aus Visual Studio in Azure veröffentlicht werden. Die Datenbank wird mit allen erforderlichen Änderungen lokal aktualisiert, damit Schema und Daten bereitgestellt bzw. exportiert werden können. |
| Es wird immer die gesamte Datenbank bereitgestellt oder exportiert. | Bestimmte Objekte können von der Migration ausgeschlossen werden. | Es besteht vollständige Kontrolle über die Objekte, die in der Migration enthalten sein sollen. |
| Keine Möglichkeit zum Ändern der Ausgabe, wenn Fehler vorhanden sind; das Quellschema muss kompatibel sein. | Ein einzelnes, monolithisches generiertes Skript kann bei Bedarf schwierig zu bearbeiten sein. Das Skript kann in SSMS oder in Visual Studio mit den SQL Server-Datenbank-Tools geöffnet und bearbeitet werden. Alle Fehler müssen behoben werden, bevor das Skript in Azure SQL-Datenbank bereitgestellt werden kann.| Vollständige Funktionen von SQL Server-Tools in Visual Studio verfügbar. Schema wird offline geändert. |
| Anwendungsvalidierung erfolgt in Azure. Meist minimal, da das Schema ohne Änderungen migriert wird. | Anwendungsvalidierung erfolgt in Azure nach der Migration. Das generierte Skript kann auch vor Ort für eine erste Anwendungsvalidierung installiert werden. | Anwendungsvalidierung kann in SQL Server erfolgen, bevor die Datenbank in Azure bereitgestellt wird. |
| Von Microsoft unterstütztes Tool. | Von der Community unterstütztes, von CodePlex heruntergeladenes Tool. | Von Microsoft unterstützte Tools mit optionaler Verwendung eines von der Community unterstützten, von CodePlex heruntergeladenen Tools. |
| Einfach zu konfigurierender Prozess mit ein oder zwei Schritten. | Schematransformationen sowie Generierung und Bereitstellung in der Cloud werden über einen einzigen benutzerfreundlichen Assistenten koordiniert. | Komplexerer Prozess mit mehreren Schritten (einfacher, wenn nur ein Schema bereitgestellt wird). |

<!---HONumber=58--> 