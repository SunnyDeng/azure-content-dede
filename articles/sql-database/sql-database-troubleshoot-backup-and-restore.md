<properties
	pageTitle="Problembehebung bei der Sicherung und Wiederherstellung mit Azure SQL-Datenbank"
	description="Erfahren Sie, wie eine Clouddatenbank bei Fehlern oder Ausfällen mithilfe von Sicherungen und Replikaten in Azure SQL-Datenbank wiederhergestellt wird."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/14/2016"
	ms.author="daleche"/>

# Wiederherstellen einer Datenbank zu einem früheren Zeitpunkt, einer gelöschten Datenbank oder eines Rechenzentrums nach einem Ausfall

Azure SQL-Datenbank erstellt Replikate Ihrer Datenbank, damit nach einem Ausfall oder einem Benutzerfehler eine Wiederherstellung möglich ist. Verfügbaren Optionen hängen vom Datenbanktarif und den gewählten Optionen ab. In der [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md) finden Sie Details und Entwurfsaspekte.

##So stellen Sie eine Datenbank zu einem früheren Zeitpunkt wieder her
1.	Klicken Sie im [Azure-Portal](https://azure.microsoft.com/) auf **SQL-Datenbanken**.
2.	Wählen Sie die Datenbank in der Liste aus, und klicken Sie dann auf **Wiederherstellen**.
3.	Geben Sie einen neuen Namen für die Datenbank ein, wählen für den Wiederherstellungszeitpunkt Datum und Uhrzeit aus, und klicken Sie dann auf **Erstellen**.
4.	Nehmen Sie an der App Anpassungen vor, damit auf die neue Datenbank verwiesen wird. Siehe [Wiederherstellen einer Datenbank nach einem Benutzerfehler](sql-database-user-error-recovery.md).

##So stellen Sie eine versehentlich gelöschte Datenbank wieder her
1.	Klicken Sie im [Azure-Portal](https://azure.microsoft.com/) auf **SQL-Server**.
2.	Wählen Sie in der Liste den Server aus, der die Datenbank gehostet hat.
3.	Führen Sie auf dem Blatt „Server“ einen Bildlauf nach unten aus, und klicken Sie auf **Gelöschte Datenbanken**.
4.	Wählen Sie die wiederherzustellende Datenbank aus, und klicken Sie auf **Erstellen**.
5.	Nehmen Sie an der App Anpassungen vor, damit auf die neue Datenbank verwiesen wird. Siehe [Wiederherstellen einer Datenbank nach einem Benutzerfehler](sql-database-user-error-recovery.md).

##So erfolgt eine Wiederherstellung nach Ausfall eines regionalen Rechenzentrums
Bei Standard- und Premium-Datenbanken können Sie nach Einrichten sekundärer Datenbanken mithilfe der Georeplikation diese sekundären Datenbanken wiederherstellen. Dadurch haben Sie bei geringerem Datenverlustpotenzial die Möglichkeit zum Wiederherstellen einer Datenbank. Einzelheiten finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank nach einem Ausfall](sql-database-disaster-recovery.md).

Azure bietet zudem Sicherungen aller Datenbanken in einer anderen Region (als sog. georedundante Sicherungen). Sie können eine neue Datenbank aus diesen Sicherungen erstellen, was als Geowiederherstellung bezeichnet wird. Jedoch ist es wahrscheinlich, dass Sie Datenverluste erleiden werden, wenn Sie nur diese Methode verwenden.

**So stellen Sie eine Datenbank mithilfe der Geowiederherstellung wieder her**

- Klicken Sie im [Azure-Portal](https://azure.microsoft.com/) auf **Neu**, dann auf **Daten und Speicher**, danach auf **SQL-Datenbank**, und wählen Sie als Datenbankquelle **Sicherung** aus. Einzelheiten finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank nach einem Ausfall](sql-database-disaster-recovery.md).

<!---HONumber=AcomDC_0218_2016-->