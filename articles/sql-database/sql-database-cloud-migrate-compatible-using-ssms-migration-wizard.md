<properties
   pageTitle="Migrieren von SQL Server-Datenbank auf SQL-Datenbank mit dem Assistenten zum Bereitstellen einer Datenbank unter Microsoft Azure-Datenbank"
   description="Microsoft Azure SQL-Datenbank, Datenbankmigration, Assistent für Microsoft Azure-Datenbank"
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

# Migrieren von SQL Server-Datenbank auf SQL-Datenbank mit dem Assistenten zum Bereitstellen einer Datenbank unter Microsoft Azure-Datenbank

Mit dem Assistenten zum Bereitstellen einer Datenbank unter Microsoft Azure in SQL Server Management Studio wird eine [kompatible SQL Server-Datenbank](sql-database-cloud-migrate.md) direkt in Ihren Azure SQL-Datenbankserver migriert.

## Verwenden des Assistenten zum Bereitstellen einer Datenbank unter Microsoft Azure-Datenbank

> [AZURE.NOTE] Die folgenden Schritte setzen voraus, dass Sie über einen [bereitgestellten SQL-Datenbankserver](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/) verfügen.

1. Stellen Sie sicher, dass Sie über die neueste Version von SQL Server Management Studio verfügen. Neue Versionen von Management Studio werden monatlich aktualisiert, damit sie mit Updates des Azure-Portals synchron sind.

    > [AZURE.IMPORTANT] Es wird empfohlen, immer die neueste Version von Management Studio zu verwenden, damit Sie mit Updates von Microsoft Azure und SQL-Datenbank synchron sind. [Aktualisieren Sie SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Öffnen Sie Management Studio und stellen Sie eine Verbindung mit Ihrer SQL Server-Datenbank her, die in den Objekt-Explorer migriert werden soll.
3. Klicken Sie im Objekt-Explorer mit der rechten Maustaste auf die Datenbank, zeigen Sie auf **Aufgaben**, und klicken Sie auf **Datenbank für Microsoft Azure SQL-Datenbank bereitstellen…**

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.	Klicken Sie im Bereitstellungs-Assistenten auf **Weiter**, und klicken Sie dann auf **Verbinden**, um die Verbindung mit Ihrem SQL-Datenbankserver zu konfigurieren.

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Geben Sie im Dialogfeld „Verbindung mit Server herstellen“ Ihre Verbindungsinformationen ein, um die Verbindung mit Ihrem SQL-Datenbankserver herzustellen.

	!["Bereitstellen in Azure" aus dem Menü "Aufgaben"](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.	Geben Sie den **neuen Datenbanknamen** für denselben an, legen Sie **Edition von Microsoft Azure SQL-Datenbank** ([Dienstebene](sql-database-service-tiers.md)), **Maximale Datenbankgröße**, **Serviceziel** (Leistungsebene) und **Temporärer Dateiname** für die [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4)-Datei fest, die vom Assistenten während des Migrationsprozesses erstellt wird.

	![Exporteinstellungen](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.	Schließen Sie den Assistenten zum Migrieren der Datenbank ab. Je nach Größe und Komplexität der Datenbank kann die Bereitstellung wenige Minuten bis viele Stunden dauern. Wenn dieser Assistent Kompatibilitätsprobleme erkennt, werden auf dem Bildschirm Fehler angezeigt und die Migration wird nicht fortgesetzt. Eine Anleitung zum Beheben von Problemen mit der Datenbankkompatibilität finden Sie unter [Beheben von Problemen mit der Datenbankkompatibilität](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.	Stellen Sie mit dem Objekt-Explorer eine Verbindung mit der migrierten Datenbank auf Ihrem Azure SQL-Datenbankserver her.
8.	Zeigen Sie über das Azure-Portal Ihre Datenbank und deren Eigenschaften an.

## Nächster Schritt: Beheben von Kompatibilitätsproblemen, falls vorhanden

[Beheben von Problemen mit der Datenbankkompatibilität](sql-database-cloud-migrate-fix-compatibility-issues.md), falls vorhanden.

<!---HONumber=AcomDC_0316_2016-->