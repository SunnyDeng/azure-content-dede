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
   ms.date="03/14/2016"
   ms.author="carlrab"/>

# Importieren aus BACPAC zur SQL-Datenbank mithilfe von SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Azure-Portal](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

In diesem Artikel wird beschrieben, wie Sie mithilfe des Assistenten „Datenebenenanwendung exportieren“ in SQL Server Management Studio aus einer [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-Datei zur SQL-Datenbank importieren.

> [AZURE.NOTE] Bei den folgenden Schritten wird davon ausgegangen, dass Sie Ihre logische SQL Azure-Instanz bereits bereitgestellt haben und dass Ihnen die Verbindungsinformationen vorliegen.

1. Stellen Sie sicher, dass Sie über die neueste Version von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.

	 > [AZURE.IMPORTANT] Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden, damit Sie mit Updates von Microsoft Azure und SQL-Datenbank synchron sind. [Aktualisieren Sie SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

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

<!---HONumber=AcomDC_0316_2016-->