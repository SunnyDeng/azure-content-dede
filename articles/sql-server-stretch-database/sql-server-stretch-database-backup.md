<properties
	pageTitle="Sichern und Wiederherstellen von Stretch-fähigen Datenbanken | Microsoft Azure"
	description="Erfahren Sie mehr über das Sichern und Wiederherstellen von Stretch-fähigen Datenbanken."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>


# Sichern und Wiederherstellen von Stretch-fähigen Datenbanken 

Zum Sichern und Wiederherstellen von Stretch-fähigen Datenbanken können Sie weiterhin die bereits verwendeten Methoden nutzen. Weitere Informationen zum SQL Server-Backup und zur Wiederherstellung finden Sie unter [Sichern und Wiederherstellen von SQL Server-Datenbanken](https://msdn.microsoft.com/library/ms187048.aspx).

Das Backup einer Stretch-fähigen Datenbank ist ein flaches Backup, das die an den Remoteserver migrierten Daten nicht einschließt.

Stretch-Datenbank bietet vollständige Unterstützung für die Point-in-Time-Wiederherstellung. Nachdem Sie Ihre SQL Server-Datenbank zu einem bestimmten Zeitpunkt wiederhergestellt und die Verbindung mit Azure erneut autorisiert haben, stimmt Stretch-Datenbank die Remotedaten mit demselben Zeitpunkt ab. Weitere Informationen zur Point-in-Time-Wiederherstellung in SQL Server finden Sie unter [Wiederherstellen einer SQL Server-Datenbank zu einem Zeitpunkt (vollständiges Wiederherstellungsmodell)](https://msdn.microsoft.com/library/ms179451.aspx). Informationen über die gespeicherte Prozedur, die Sie zum erneuten Autorisieren der Verbindung mit Azure nach einer Wiederherstellung ausführen müssen, finden Sie unter [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx).

## <a name="Reconnect"></a>Wiederherstellen einer Stretch-fähigen Datenbank aus einem Backup

1.  Stellen Sie die Datenbank aus einem Backup wieder her.

2.  Erstellen Sie einen Datenbankhauptschlüssel für die Stretch-fähige Datenbank. Weitere Informationen finden Sie unter [CREATE MASTER KEY (Transact-SQL)](https://msdn.microsoft.com/library/ms174382.aspx).

3.  Erstellen Sie datenbankbezogene Anmeldeinformationen für die Stretch-fähige Datenbank. Weitere Informationen finden Sie unter [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/mt270260.aspx).

4.  Führen Sie die gespeicherte Prozedur [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) aus, um die Verbindung der lokalen Stretch-fähigen Datenbank mit Azure erneut herzustellen. Geben Sie die datenbankbezogenen Anmeldeinformationen, die Sie in einem vorherigen Schritt erstellt haben, als einen Wert vom Datentyp „sysname“ oder „varchar(128)“ ein. (Verwenden Sie nicht „varchar(max)“.)

    ```tsql
    Declare @credentialName nvarchar(128);
    SET @credentialName = N’<database_scoped_credential_name_created_previously>’;
    EXEC sp_rda_reauthorize_db @credentialName;
    ```

## <a name="MoreInfo"></a>Weitere Informationen zum Backup und zur Wiederherstellung
Backups für eine Datenbank mit aktiviertem Stretch-Datenbank enthalten nur die lokalen Daten und die berechtigen Daten zu dem Zeitpunkt, an dem das Backup ausgeführt wird. Diese Backups enthalten auch Informationen über den Remoteendpunkt, an dem sich die Remotedaten der Datenbank befinden. Dies wird als „flaches Backup“ bezeichnet. Tiefe Backups, die alle Daten (lokal und remote) der Datenbank enthalten, werden nicht unterstützt.

![Stretch-Datenbank-Backupdiagramm][StretchBackupImage1]

Beim Wiederherstellen des Backups einer Datenbank mit aktiviertem Stretch-Datenbank werden die lokalen Daten und berechtigten Daten wie erwartet in der Datenbank wiederhergestellt. (Bei berechtigten Daten handelt es sich um Daten, die noch nicht verschoben wurden, für die jedoch eine Verschiebung nach Azure basierend auf der Stretch-Datenbankkonfiguration in den Tabellen geplant ist.) Nachdem der Wiederherstellungsvorgang ausgeführt wurde, enthält die Datenbank lokale und berechtigte Daten von dem Zeitpunkt, an dem das Backup ausgeführt wurde, sie enthält jedoch nicht die erforderlichen Anmeldeinformationen und Artefakte für die Verbindung mit dem Remoteendpunkt.

![Stretch-Datenbank nach dem Backup][StretchBackupImage2]

Sie müssen die gespeicherte Prozedur **sys.sp\_rda\_reauthorize\_db** ausführen, um die Verbindung zwischen der lokalen Datenbank und dem Remoteendpunkt erneut herzustellen. Nur ein db\_owner kann diesen Vorgang ausführen. Für diese gespeicherte Prozedur ist auch der Administratorbenutzernamen und das Kennwort des Remoteendpunkts erforderlich. Dies bedeutet, dass Sie den Administratorbenutzernamen und das Kennwort für den Azure-Zielserver bereitstellen müssen.

![Stretch-Datenbank nach dem Backup][StretchBackupImage3]

Nachdem Sie die Verbindung erneut hergestellt haben, versucht Stretch-Datenbank, berechtigte Daten in der lokalen Datenbank mit Remotedaten abzustimmen, indem eine Kopie der Remotedaten auf dem Remoteendpunkt erstellt und mit der lokalen Datenbank verknüpft wird. Dieser Prozess erfolgt automatisch und erfordert kein Eingreifen des Benutzers. Nachdem diese Abstimmung ausgeführt wurde, haben die lokale Datenbank und der Remoteendpunkt einen konsistenten Zustand.

![Stretch-Datenbank nach dem Backup][StretchBackupImage4]

## Weitere Informationen
[Verwalten von Stretch-Datenbank und Behandeln von Problemen ](sql-server-stretch-database-manage.md) [sys.sp\_rda\_reauthorize\_db (Transact-SQL)](https://msdn.microsoft.com/library/mt131016.aspx) [Sichern und Wiederherstellen von SQL Server-Datenbanken](https://msdn.microsoft.com/library/ms187048.aspx)

<!--Image references-->
[StretchBackupImage1]: ./media/sql-server-stretch-database-backup/StretchDBBackup1.png
[StretchBackupImage2]: ./media/sql-server-stretch-database-backup/StretchDBBackup2.png
[StretchBackupImage3]: ./media/sql-server-stretch-database-backup/StretchDBBackup3.png
[StretchBackupImage4]: ./media/sql-server-stretch-database-backup/StretchDBBackup4.png

<!---HONumber=AcomDC_0309_2016-->