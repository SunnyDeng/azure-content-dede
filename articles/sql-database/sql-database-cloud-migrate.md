<properties
   pageTitle="Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank"
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
   ms.date="10/12/2015"
   ms.author="carlrab"/>

# Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank

Die Komplexität beim Verschieben einer lokalen Datenbank zu Azure SQL-Datenbank variiert je nach Datenbank- und Anwendungsentwurf und tolerierter Ausfallzeit. Für kompatible Datenbanken besteht die Migration zu Azure SQL-Datenbank aus einer einfachen Verschiebung von Schemas und Daten, bei der nur wenige oder gar keine Schemaänderungen und nur wenige oder gar keine Umstrukturierungsmaßnahmen für Anwendungen erforderlich sind. [Azure SQL-Datenbank V12](../sql-database-v12-whats-new.md) sorgt für nahezu vollständige Modulkompatibilität mit SQL Server 2014 und SQL Server 2016. Die meisten SQL Server 2016 Transact-SQL-Anweisungen werden in Microsoft Azure SQL-Datenbank vollständig unterstützt. Dies umfasst die Datentypen, Operatoren, Zeichenfolgen-, arithmetischen, logischen und Cursorfunktionen sowie die anderen Transact-SQL-Elemente von SQL Server, mit denen die meisten Programme arbeiten. Teilweise oder nicht unterstützte Funktionen steht meist in Zusammenhang mit den Unterschieden bei der Verwaltung der Datenbank durch Azure SQL-Datenbank (z. B. Datei-, Hochverfügbarkeits- und Sicherheitsfunktionen) und bei speziellen Features wie dem Service Broker. Da Azure SQL-Datenbank viele Features von der Abhängigkeit von der master-datenbank isoliert, sind viele Aktivitäten auf Serverebene ungeeignet und werden nicht unterstützt. Features, die in SQL Server veraltet sind, werden im Allgemeinen von Azure SQL-Datenbank nicht unterstützt. Auf [teilweise oder nicht unterstützten Funktionen](../sql-database-transact-sql-information.md) basierende Datenbanken und Anwendungen müssen vor der Migration etwas umstrukturiert werden.

Der Workflow für das Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank lautet:

 1. [Ermitteln der Kompatibilität der Datenbank](#determine-if-your-database-is-compatible)
 2. [Beheben von Problemen mit der Datenbankkompatibilität (falls erforderlich)](#fix-database-compatibility-issues)
 3. [Migrieren einer kompatiblen Datenbank](#options-to-migrate-a-compatible-database-to-azure-sql-database)

>[AZURE.NOTE]Informationen zum Migrieren anderer Arten von Datenbanken, z. B. Microsoft Access, Sybase, MySQL Oracle und DB2, zu Azure SQL-Datenbank finden Sie unter [SQL Server-Migrations-Assistent](http://blogs.msdn.com/b/ssma/).

## Ermitteln der Kompatibilität der Datenbank
Es gibt zwei Hauptverfahren, mit denen Sie ermitteln können, ob Ihre Quelldatenbank kompatibel ist. - Export der Dateiebenenanwendung: Bei diesem Verfahren wird ein Assistent in Management Studio verwendet, um Ihre Datenbank zu analysieren und Probleme mit der Datenbankkompatibilität, falls vorhanden, auf der Konsole anzuzeigen. - SQLPackage: Bei diesem Verfahren wird das Befehlszeilenhilfsprogramm [sqlpackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) eingesetzt, um die Datenbank zu analysieren und einen Bericht zu erstellen. SQLPackage ist im Lieferumfang von Visual Studio und SQL Server enthalten.

> [AZURE.NOTE]Es gibt noch ein drittes Verfahren, bei dem Ablaufverfolgungsdateien als zusätzliche Quellinformationen verwendet werden, um die Kompatibilität auf Anwendungsebene und Datenbankebene zu testen. Dies ist der [SQL Azure-Migrations-Assistent](http://sqlazuremw.codeplex.com/), ein kostenloses Tool von Codeplex. Mit diesem Tool werden derzeit aber unter Umständen Kompatibilitätsfehler gefunden, die für Azure SQL-Datenbank V11 ein Problem darstellten, während dies für Azure SQL-Datenbank V12 nicht mehr der Fall ist.

Wenn für die Datenbank Inkompatibilitäten erkannt werden, müssen Sie diese Inkompatibilitäten beheben, bevor Sie die Datenbank zu Azure SQL-Datenbank migrieren können. Eine Anleitung zum Beheben von Problemen mit der Datenbankkompatibilität finden Sie unter [Beheben von Problemen mit der Datenbankkompatibilität](#fix-database-compatibility-issues).

> [AZURE.IMPORTANT]Mit diesen Optionen werden nicht alle Kompatibilitätsprobleme für unterschiedliche Ebenen von SQL Server-Datenbanken erkannt (Ebenen 90, 100 und 110). Wenn Sie eine Migration von einer älteren Datenbank durchführen (Ebene 80, 90, 100 und 110), sollten Sie zuerst den Upgradeprozess durchführen (mindestens in der Entwicklungsumgebung) und unter SQL Server 2014 oder höher dann zu Azure SQL-Datenbank migrieren.

## Ermitteln der Datenbankkompatibilität mit „sqlpackage.exe“

1. Öffnen Sie ein Eingabeaufforderungsfenster, und wechseln Sie in ein Verzeichnis mit der neuesten Version von „sqlpackage.exe“. Dieses Hilfsprogramm wird mit Visual Studio und SQL Server bereitgestellt. Sie können auch die aktuelle Version der SQL Server Data Tools [herunterladen](https://msdn.microsoft.com/library/mt204009.aspx), um dieses Hilfsprogramm zu erhalten.
2. Führen Sie den folgenden sqlpackage.exe-Befehl mit den unten angegebenen Argumenten für Ihre Umgebung aus:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file > /p:TableData=< schema_name.table_name > > < output_file > 2>&1'

	| Argument | Beschreibung |
	|---|---|
	| < server_name > | Name des Quellservers |
	| < database_name > | Name der Quelldatenbank |
	| < target_file > | Dateiname und Speicherort für die BACPAC-Datei |
	| < schema_name.table_name > | Tabellen, für die Daten in die Zieldatei ausgegeben werden |
	| < output_file > | Dateiname und Speicherort für die Ausgabedatei mit Fehlern, falls vorhanden |

	Der Grund für das Argument „/p:TableName“ ist, dass wir nur die Datenbankkompatibilität in Bezug auf das Exportieren nach Azure SQL-Datenbank V12 testen möchten und nicht die Daten aus allen Tabellen exportieren möchten. Leider unterstützt das export-Argument für „sqlpackage.exe“ das Extrahieren von 0 Tabellen nicht. Sie müssen also eine kleine Tabelle angeben. < output_file > enthält den Bericht zu etwaigen Fehlern. Mit der Zeichenfolge „> 2>&1“ werden sowohl die Standardausgabe als auch der Standardfehler, die bzw. der sich aus der Befehlsausführung ergibt, an die angegebene Ausgabedatei geleitet.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Öffnen Sie die Ausgabedatei, und sehen Sie sich die Kompatibilitätsfehler an, falls vorhanden. Eine Anleitung zum Beheben von Problemen mit der Datenbankkompatibilität finden Sie unter [Beheben von Problemen mit der Datenbankkompatibilität](#fix-database-compatibility-issues).

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## Ermitteln der Kompatibilität der Datenbank per Export der Datenebenenanwendung

1. Stellen Sie sicher, dass Sie über Version 13.0.600.65 oder höher von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.

 	 >[AZURE.IMPORTANT]Laden Sie die [aktuelle](https://msdn.microsoft.com/library/mt238290.aspx) Version von SQL Server Management Studio herunter. Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden.

2. Öffnen Sie Management Studio, und stellen Sie eine Verbindung mit Ihrer Quelldatenbank im Objekt-Explorer her.
3. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf die Quelldatenbank, zeigen Sie auf **Aufgaben**, und klicken Sie auf **Datenebenenanwendung exportieren…**.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Klicken Sie im Export-Assistenten auf **Weiter**, und konfigurieren Sie dann auf der Registerkarte **Einstellungen** den Export für das Speichern der BACPAC-Datei entweder an einem lokalen Speicherort auf dem Datenträger oder in einem Azure-Blob. Eine BACPAC-Datei wird nur gespeichert, wenn keine Probleme mit der Datenbankkompatibilität vorliegen. Wenn Kompatibilitätsprobleme auftreten, werden sie auf der Konsole angezeigt.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Klicken Sie auf die Registerkarte **Erweitert**, und deaktivieren Sie das Kontrollkästchen **Alles markieren**, um das Exportieren von Daten zu überspringen. An diesem Punkt möchten wir nur die Kompatibilität testen.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Klicken Sie auf **Weiter** und anschließend auf **Fertig stellen**. Probleme mit der Datenbankkompatibilität werden, falls vorhanden, angezeigt, nachdem der Assistent das Schema überprüft hat.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Wenn keine Fehler angezeigt werden, ist Ihre Datenbank kompatibel, und Sie können die Migration durchführen. Falls Fehler auftreten, müssen Sie sie beheben. Klicken Sie zum Anzeigen der Fehler für **Schema wird verifiziert** auf **Fehler**. Informationen zum Beheben dieser Fehler finden Sie unter [Beheben von Problemen mit der Datenbankkompatibilität](#fix-database-compatibility-issues).

	![Exporteinstellungen](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.	Wenn die BACPAC-Datei erfolgreich generiert wurde, ist Ihre Datenbank mit der SQL-Datenbank kompatibel, und Sie können die Migration durchführen.

## Optionen zum Migrieren einer kompatiblen Datenbank zu Azure SQL-Datenbank

Nachdem Sie überprüft haben, dass Sie über eine kompatible Datenbank verfügen, müssen Sie die Migrationsmethode auswählen. Zunächst müssen Sie entscheiden, ob Sie es sich leisten können, die Datenbank während der Migration aus der Produktion herauszunehmen. Falls nicht, können Sie die SQL Server-Transaktionsreplikation verwenden, die unten beschrieben ist. Sehen Sie sich die folgenden drei Methoden an, wenn Sie sich Ausfallzeit leisten können oder eine Testmigration Ihrer Produktionsdatenbank durchführen, die Sie ggf. später per Transaktionsreplikation migrieren möchten.

### Migrieren einer kompatiblen Datenbank mit Ausfallzeit   
In der folgenden Liste sind die Optionen zum Migrieren einer kompatiblen Datenbank zu Azure SQL-Datenbank aufgeführt. Sie können sie verwenden, wenn Sie sich Ausfallzeit leisten können, während die Migration durchgeführt wird und bevor Sie für Ihre Benutzer und Anwendungen auf die migrierte Datenbank in Azure SQL-Datenbank verweisen. Mit diesen Methoden migrieren Sie den Stand Ihrer Datenbank zu einem bestimmten Zeitpunkt.

> [AZURE.WARNING]Vergewissern Sie sich vor dem Migrieren der Datenbank mit einer dieser Methoden, dass keine aktiven Transaktionen vorhanden sind, um die Transaktionskonsistenz während der Migration sicherzustellen. Es gibt viele Methoden zum Stilllegen einer Datenbank: vom Deaktivieren der Clientkonnektivität bis zum Erstellen einer [Datenbankmomentaufnahme](https://msdn.microsoft.com/library/ms175876.aspx).

- Für kleine und mittlere Datenbanken ist das Migrieren einer [kompatiblen](#determine-if-your-database-is-compatible) SQL Server 2005-Datenbank (oder höher) so einfach wie das Ausführen des [Assistenten zum Bereitstellen einer Datenbank unter Microsoft Azure](#use-the-deploy-database-to-microsoft-azure-database-wizard) in SQL Server Management Studio. Wenn Probleme mit der Konnektivität auftreten (keine Verbindung, geringe Bandbreite oder Zeitüberschreitungen), können Sie [ein BACPAC-Objekt verwenden, um eine SQL Server-Datenbank zu Azure SQL-Datenbank zu migrieren](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database).
- Für mittlere bis große Datenbanken oder bei Verbindungsproblemen können Sie [ein BACPAC-Objekt verwenden, um eine SQL Server-Datenbank zu Azure SQL-Datenbank zu migrieren](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database). Bei dieser Methode verwenden Sie SQL Server Management Studio zum Exportieren der Daten und des Schemas in eine [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-Datei (lokal oder in einem Azure-Blob gespeichert) und importieren die BACPAC-Datei dann in Ihre Azure SQL-Instanz. Wenn Sie die BACPAC-Datei in einem Azure-Blob speichern, können Sie sie auch über das [Azure-Portal](sql-database-import.md) oder [per PowerShell](sql-database-import-powershell.md) importieren.
- Bei größeren Datenbanken empfiehlt es sich, Schema und Daten separat zu migrieren. Mit dieser Methode erstellen Sie eine [BACPAC-Datei ohne Daten](#use-a-bacpac-to-migrate-a-database-to-azure-sql-database) und importieren diese BACPAC-Datei in Azure SQL-Datenbank. Nachdem das Schema in Azure SQL-Datenbank importiert wurde, verwenden Sie [BCP](https://msdn.microsoft.com/library/ms162802.aspx), um die Daten in Flatfiles zu extrahieren, und importieren diese Dateien dann in Azure SQL-Datenbank.

	 ![Diagramm der SSMS-Migration](./media/sql-database-cloud-migrate/01SSMSDiagram_new.png)

### Migrieren einer kompatiblen Datenbank ohne Ausfallzeit

Wenn Sie es sich nicht leisten können, Ihre SQL Server-Datenbank während der Migration aus der Produktion herauszunehmen, können Sie die SQL Server-Transaktionsreplikation als Migrationslösung verwenden. Bei der Transaktionsreplikation werden alle Änderungen an Ihren Daten oder Schemas, die zwischen dem Beginn der Migration und dem Abschluss der Migration erfolgen, in Ihrer Azure SQL-Datenbank angezeigt. Nach Abschluss die Migration müssen Sie nur die Verbindungszeichenfolge Ihrer Anwendungen ändern, um dafür auf Ihre Azure SQL-Datenbank zu verweisen, anstatt auf Ihre lokale Datenbank. Sobald mit der Transaktionsreplikation alle verbleibenden Änderungen Ihrer lokalen Datenbank abgearbeitet wurden und alle Anwendungen auf Azure DB zeigen, können Sie die Replikation sicher deinstallieren und Azure SQL-Datenbank als Produktionssystem nutzen.

 ![SeedCloudTR-Diagramm](./media/sql-database-cloud-migrate/SeedCloudTR.png)


Die Transaktionsreplikation ist eine Technologie, die seit SQL Server 6.5 in SQL Server integriert ist. Es handelt sich hierbei um eine sehr ausgereifte und bewährte Technologie, für die die meisten Datenbankadministratoren über Erfahrung verfügen. Mit der [SQL Server 2016-Vorschau](http://www.microsoft.com/server-cloud/products/sql-server-2016/) kann die Azure SQL-Datenbank als [Transaktionsreplikationsabonnent](https://msdn.microsoft.com/library/mt589530.aspx) für die lokale Veröffentlichung konfiguriert werden. Wenn Sie die Einrichtung über Management Studio durchführen, erhalten Sie genau das gleiche Ergebnis wie beim Einrichten eines Abonnenten der Transaktionsreplikation auf einem lokalen Server. Dieses Szenario wird für die folgenden SQL Server-Versionen unterstützt:

 - SQL Server 2016 CTP3 (Vorschau) und höher 
 - SQL Server 2014 SP1 CU3 und höher
 - SQL Server 2014 RTM CU10 und höher
 - SQL Server 2012 SP2 CU8 und höher
 - SQL Server 2013 SP3 (nach der Veröffentlichung)

Sie können die Transaktionsreplikation auch zum Migrieren einer Teilmenge Ihrer lokalen Datenbank verwenden. Die Veröffentlichung, die Sie zu Azure SQL-Datenbank replizieren, kann auf eine Teilmenge der Tabellen in der replizierten Datenbank beschränkt werden. Für jede replizierte Tabelle können Sie die Daten außerdem auf eine Teilmenge der Zeilen bzw. eine Teilmenge der Spalten beschränken.

## Verwenden des Assistenten zum Bereitstellen einer Datenbank unter Microsoft Azure

Mit dem Assistenten zum Bereitstellen einer Datenbank unter Microsoft Azure in SQL Server Management Studio wird eine [kompatible](#determine-if-your-database-is-compatible) SQL Server 2005-Datenbank (oder höher) direkt zu Ihrer logischen Azure SQL-Serverinstanz migriert.

> [AZURE.NOTE]Bei den folgenden Schritten wird davon ausgegangen, dass Sie Ihre logische SQL Azure-Instanz bereits [bereitgestellt](../sql-database-get-started.md) haben und dass Ihnen die Verbindungsinformationen vorliegen.

1. Stellen Sie sicher, dass Sie über Version 13.0.600.65 oder höher von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.

	 >[AZURE.IMPORTANT]Laden Sie die [aktuelle](https://msdn.microsoft.com/library/mt238290.aspx) Version von SQL Server Management Studio herunter. Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden.

2. Öffnen Sie Management Studio, und stellen Sie eine Verbindung mit Ihrer Quelldatenbank im Objekt-Explorer her.
3. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf die Quelldatenbank, zeigen Sie auf **Aufgaben**, und klicken Sie auf **Datenbank für Microsoft Azure SQL-Datenbank bereitstellen…**

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	Klicken Sie im Bereitstellungs-Assistenten auf **Weiter**, und klicken Sie dann auf **Verbinden**, um die Verbindung mit Ihrem Azure SQL-Datenbank-Server zu konfigurieren.

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Geben Sie im Dialogfeld „Verbindung mit Server herstellen“ Ihre Verbindungsinformationen ein, um die Verbindung mit Ihrem Azure SQL-Datenbank-Server herzustellen.

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	Geben Sie den **neuen Datenbanknamen** für die Datenbank unter Azure SQL-Datenbank an, und legen Sie **Edition von Microsoft Azure SQL-Datenbank** (Dienstebene), **Maximale Datenbankgröße**, **Serviceziel** (Leistungsebene) und **Temporärer Dateiname** für die BACPAC-Datei fest, die vom Assistenten während des Migrationsprozesses erstellt wird. Weitere Informationen zu Dienstebenen und Leistungsebenen finden Sie unter [Dienstebenen von Azure SQL-Datenbank](sql-database-service-tiers.md).

	![Exporteinstellungen](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Schließen Sie den Assistenten zum Migrieren der Datenbank ab. Je nach Größe und Komplexität der Datenbank kann die Bereitstellung wenige Minuten bis viele Stunden dauern.
7.	Stellen Sie mit dem Objekt-Explorer eine Verbindung mit der migrierten Datenbank auf Ihrem Azure SQL-Datenbank-Server her.
8.	Zeigen Sie über das Azure-Portal Ihre Datenbank und deren Eigenschaften an.

## Verwenden eines BACPAC-Objekts zum Migrieren einer SQL Server-Datenbank zu Azure SQL-Datenbank

Für mittlere bis große Datenbanken oder bei Verbindungsproblemen können Sie den Migrationsprozess in zwei getrennte Schritte unterteilen. Sie können das Schema und dessen Daten mit ein oder zwei Methoden in eine [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-Datei exportieren.

- [Exportieren in eine BACPAC-Datei mit SQL Server Management Studio](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio)
- [Exportieren in eine BACPAC-Datei per SqlPackage](#export-a-compatible-sql-server-database-to-a-bacpac-file-using-sqlpackage)

Sie können die BACPAC-Datei lokal oder in einem Azure-Blob speichern. Anschließend können Sie diese BACPAC-Datei mit einer von mehreren Methoden in Azure SQL-Datenbank importieren.

- [Importieren aus einer BACPAC-Datei in Azure SQL-Datenbank mit SQL Server Management Studio](#import-from-a-bacpac-file-into-azure-sql-database-using-sql-server-management-studio)
- [Importieren aus einer BACPAC-Datei in Azure SQL-Datenbank per SqlPackage](#import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage)
- [Importieren aus einer BACPAC-Datei in Azure SQL-Datenbank über das Azure-Portal](sql-database-import.md)
- [Importieren aus einer BACPAC-Datei in Azure SQL-Datenbank per PowerShell](sql-database-import-powershell.md)

## Exportieren einer kompatiblen SQL Server-Datenbank in eine BACPAC-Datei mit SQL Server Management Studio

Führen Sie die unten angegebenen Schritte aus, um Management Studio zum Exportieren einer [kompatiblen](#determine-if-your-database-is-compatible) SQL Server-Datenbank für die Migration in eine BACPAC-Datei zu verwenden.

1. Stellen Sie sicher, dass Sie über Version 13.0.600.65 oder höher von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.

	 >[AZURE.IMPORTANT]Laden Sie die [aktuelle](https://msdn.microsoft.com/library/mt238290.aspx) Version von SQL Server Management Studio herunter. Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden.

2. Öffnen Sie Management Studio, und stellen Sie eine Verbindung mit Ihrer Quelldatenbank im Objekt-Explorer her.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf die Quelldatenbank, zeigen Sie auf **Aufgaben**, und klicken Sie auf **Datenebenenanwendung exportieren…**.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. Konfigurieren Sie im Export-Assistenten den Export für das Speichern der BACPAC-Datei entweder an einem lokalen Speicherort auf dem Datenträger oder in einem Azure-Blob. Die exportierte BACPAC-Datei enthält immer das vollständige Datenbankschema und standardmäßig die Daten aus allen Tabellen. Verwenden Sie die Registerkarte "Erweitert", wenn Sie Daten aus einigen oder allen Tabellen ausschließen möchten. Sie können z. B. nur die Daten für Verweistabellen exportieren, anstatt die Daten aller Tabellen.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)

## Exportieren einer kompatiblen SQL Server-Datenbank in eine BACPAC-Datei per SqlPackage

Führen Sie die unten angegebenen Schritte aus, um das Befehlszeilen-Hilfsprogramm [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) zum Exportieren einer [kompatiblen](#determine-if-your-database-is-compatible) Datenbank für die Migration in eine BACPAC-Datei zu verwenden.

> [AZURE.NOTE]Bei den Schritten unten wird vorausgesetzt, dass Sie bereits einen Azure SQL-Datenbank-Server bereitgestellt haben, dass Ihnen die Verbindungsinformationen vorliegen und dass Sie die Kompatibilität der Quelldatenbank überprüft haben.

1. Öffnen Sie ein Eingabeaufforderungsfenster, und wechseln Sie in ein Verzeichnis mit dem Befehlszeilen-Hilfsprogramm „sqlpackage.exe“. Dieses Hilfsprogramm ist im Lieferumfang von Visual Studio und SQL Server enthalten.
2. Führen Sie den folgenden sqlpackage.exe-Befehl mit den unten angegebenen Argumenten für Ihre Umgebung aus:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Argument | Beschreibung |
	|---|---|
	| < server_name > | Name des Quellservers |
	| < database_name > | Name der Quelldatenbank |
	| < target_file > | Dateiname und Speicherort für die BACPAC-Datei |

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Importieren aus einer BACPAC-Datei in Azure SQL-Datenbank mit SQL Server Management Studio

Führen Sie die unten angegebenen Schritte aus, um den Import aus einer BACPAC-Datei in Azure SQL-Datenbank durchzuführen.

> [AZURE.NOTE]Bei den folgenden Schritten wird davon ausgegangen, dass Sie Ihre logische SQL Azure-Instanz bereits bereitgestellt haben und dass Ihnen die Verbindungsinformationen vorliegen.

1. Stellen Sie sicher, dass Sie über Version 13.0.600.65 oder höher von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.

	> [AZURE.IMPORTANT]Laden Sie die [aktuelle](https://msdn.microsoft.com/library/mt238290.aspx) Version von SQL Server Management Studio herunter. Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden.

2. Öffnen Sie Management Studio, und stellen Sie eine Verbindung mit Ihrer Quelldatenbank im Objekt-Explorer her.

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)

3. Sobald die BACPAC-Datei erstellt wurde, stellen Sie eine Verbindung mit dem Azure SQL-Datenbank-Server her. Klicken Sie mit der rechten Maustaste auf den Ordner **Datenbanken**, und klicken Sie auf **Datenebenenanwendung importieren...**.

    ![Menüelement für das Importieren von Datenebenenanwendungen](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

4.	Wählen Sie im Import-Assistenten die BACPAC-Datei aus, die Sie gerade exportiert haben, um die neue Datenbank in Azure SQL-Datenbank zu erstellen.

    ![Importieren von Einstellungen](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

5.	Geben Sie den **neuen Datenbanknamen** für die Datenbank in Azure SQL-Datenbank an, und legen Sie **Edition von Microsoft Azure SQL-Datenbank** (Dienstebene), **Maximale Datenbankgröße** und **Serviceziel** (Leistungsebene) fest.

    ![Datenbankeinstellungen](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

6.	Klicken Sie auf **Weiter** und dann auf **Fertig stellen**, um die BACPAC-Datei in eine neue Datenbank auf dem Azure SQL-Datenbank-Server zu importieren.

7. Stellen Sie mit dem Objekt-Explorer eine Verbindung mit der migrierten Datenbank auf Ihrem Azure SQL-Datenbank-Server her.

8.	Zeigen Sie über das Azure-Portal Ihre Datenbank und deren Eigenschaften an.

## Importieren aus einer BACPAC-Datei in Azure SQL-Datenbank per SqlPackage

Führen Sie die unten angegebenen Schritte aus, um das Befehlszeilen-Hilfsprogramm [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) zum Importieren einer kompatiblen SQL Server-Datenbank (oder Azure SQL-Datenbank) aus einer BACPAC-Datei zu verwenden.

> [AZURE.NOTE]Bei den folgenden Schritten wird davon ausgegangen, dass Sie bereits einen Azure SQL-Datenbank-Server bereitgestellt haben und dass Ihnen die Verbindungsinformationen vorliegen.

1. Öffnen Sie ein Eingabeaufforderungsfenster, und wechseln Sie in ein Verzeichnis mit dem Befehlszeilen-Hilfsprogramm „sqlpackage.exe“. Dieses Hilfsprogramm ist im Lieferumfang von Visual Studio und SQL Server enthalten.
2. Führen Sie den folgenden sqlpackage.exe-Befehl mit den unten angegebenen Argumenten für Ihre Umgebung aus:

	'sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >

	| Argument | Beschreibung |
	|---|---|
	| < server_name > | Zielservername |
	| < database_name > | Zieldatenbankname |
	| < user_name > | Benutzername auf dem Zielserver |
	| < password > | Kennwort des Benutzers |
	| < source_file > | Dateiname und Speicherort für die zu importierende BACPAC-Datei |

	![Exportieren von Datenebenenanwendungen im Menü "Aufgaben"](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)


## Beheben von Problemen mit der Datenbankkompatibilität

Wenn Sie ermitteln, dass Ihre SQL Server-Quelldatenbank nicht kompatibel ist, haben Sie eine Reihe von Optionen zum Beheben der Probleme mit der Datenbankkompatibilität, die Sie [bereits identifiziert](#determine-if-your-database-is-compatible) haben.

- Verwenden Sie den [SQL Azure-Migrations-Assistenten](http://sqlazuremw.codeplex.com/). Sie können dieses Codeplex-Tool zum Generieren eines T-SQL-Skripts aus einer inkompatiblen Quelldatenbank verwenden, das vom Assistenten dann transformiert wird, um es mit der SQL-Datenbank kompatibel zu machen. Anschließend wird eine Verbindung mit Azure SQL-Datenbank hergestellt, um das Skript auszuführen. Mit diesem Tool werden außerdem Ablaufverfolgungsdateien analysiert, um Kompatibilitätsprobleme zu ermitteln. Das Skript kann mit der Option "Nur Schema" generiert werden oder Daten im BCP-Format enthalten. Weitere Dokumentation, einschließlich einer Schritt-für-Schritt-Anleitung, finden Sie bei Codeplex unter [SQL Azure-Migrations-Assistent](http://sqlazuremw.codeplex.com/).  

 ![Diagramm der SAMW-Migration](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

 >[AZURE.NOTE]Beachten Sie, dass nicht jedes inkompatible Schema, das vom Assistenten erkannt werden kann, durch dessen integrierte Transformationen verarbeitet werden kann. Ein inkompatibles Skript, das nicht verarbeitet werden kann, wird als Fehler gemeldet, wobei Kommentare in das generierte Skript eingefügt werden. Falls viele Fehler erkannt werden, sollten Sie entweder Visual Studio oder SQL Server Management Studio nutzen, um die Fehler durchzugehen und zu beheben, die mit dem SQL Server-Migrations-Assistenten nicht behoben werden konnten.

- Verwenden Sie Visual Studio. Sie können Visual Studio verwenden, um das Datenbankschema zur Analyse in ein Visual Studio-Datenbankprojekt zu importieren. Für die Analyse muss SQL-Datenbank V12 als Zielplattform angegeben und das Projekt erstellt werden. Wenn der Erstellungsvorgang erfolgreich ist, ist die Datenbank kompatibel. Ist der Erstellungsvorgang nicht erfolgreich, können Sie die Fehler in SQL Server Data Tools für Visual Studio (SSDT) beheben. Wenn sich das Projekt erfolgreich erstellen lässt, können Sie es als Kopie der Quelldatenbank veröffentlichen und die Daten anschließend unter Verwendung des Datenvergleichsfeatures von SSDT aus der Quelldatenbank in die Azure SQL V12-kompatible Datenbank kopieren. Diese aktualisierte Datenbank wird dann mit den [bereits erwähnten Optionen](#options-to-migrate-a-compatible-database-to-azure-sql-database) für Azure SQL-Datenbank bereitgestellt.

 ![Diagramm der VSSSDT-Migration](./media/sql-database-cloud-migrate/03VSSSDTDiagram.png)

 >[AZURE.NOTE]Wenn nur das Schema migriert werden muss, kann das Schema direkt in Visual Studio für die Azure SQL-Datenbank veröffentlicht werden. Diese Methode kann verwendet werden, wenn das Datenbankschema mehr Änderungen erfordert, als der Migrations-Assistent allein verarbeiten kann.

- SQL Server Management Studio. Sie können die Probleme in Management Studio mithilfe von verschiedenen Transact-SQL-Befehlen beheben, z. B. **ALTER DATABASE**.

<!---HONumber=Nov15_HO2-->