<properties 
    pageTitle="Erstellen der Kopie einer Azure SQL-Datenbank mithilfe von Transact-SQL" 
    description="Erstellen der Kopie einer Azure SQL-Datenbank mithilfe von Transact-SQL" 
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/16/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Erstellen der Kopie einer Azure SQL-Datenbank mithilfe von Transact-SQL

**Einzeldatenbank**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-copy.md)
- [PowerShell](sql-database-copy-powershell.md)
- [SQL](sql-database-copy-transact-sql.md)



Die folgenden Schritte verdeutlichen, wie Sie eine Kopie einer SQL-Datenbank mit Transact-SQL erstellen. Beim Kopiervorgang für die Datenbank wird eine SQL-Datenbank in eine neue Datenbank kopiert, indem die [CREATE DATABASE]()-Anweisung verwendet wird. Die Kopie ist eine Snapshotsicherung Ihrer Datenbank, die Sie entweder auf demselben Server oder auf einem anderen Server erstellen.


> [AZURE.NOTE]Azure SQL-Datenbank erstellt und verwaltet automatisch Sicherungen für jede Benutzerdatenbank, die Sie wiederherstellen können. Weitere Informationen finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).


Wenn der Kopiervorgang abgeschlossen ist, handelt es sich bei der neuen Datenbank um eine voll funktionsfähige Datenbank, die unabhängig von der Quelldatenbank ist. Die neue Datenbank entspricht in Bezug auf Transaktionen mit der Quelldatenbank für den Zeitpunkt überein, an dem die Erstellung der Kopie abgeschlossen ist. Die Dienstebene und die Leistungsebene (Tarif) der Datenbankkopie stimmen mit den Ebenen der Quelldatenbank überein. Nachdem der Kopiervorgang abgeschlossen ist, wird die Kopie zu einer voll funktionsfähigen, unabhängigen Datenbank. Anmeldungen, Benutzer und Berechtigungen können unabhängig verwaltet werden.


Wenn Sie eine Datenbank auf denselben logischen Server kopieren, können für beide Datenbanken die gleichen Anmeldedaten verwendet werden. Das Sicherheitsprinzipal, das Sie zum Kopieren der Datenbank verwenden, wird zum Datenbankbesitzer (DBO) der neuen Datenbank. Alle Datenbankbenutzer, ihre Berechtigungen und ihre Sicherheits-IDs (SIDs) werden in die Kopie der Datenbank kopiert.


Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Wenn Sie ein Azure-Abonnement benötigen, müssen Sie lediglich oben auf dieser Seite auf den Link **Kostenlose Testversion** klicken. Lesen Sie anschließend den Artikel weiter.
- Azure SQL-Datenbank. Wenn Sie nicht über eine SQL-Datenbank verfügen, können Sie die Erstellung anhand der Schritte im folgenden Artikel durchführen: [Erstellen der ersten Azure SQL-Datenbank](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Wenn Sie nicht über SSMS verfügen oder in diesem Artikel beschriebene Funktionen nicht verfügbar sind, können Sie die [aktuelle Version herunterladen](https://msdn.microsoft.com/library/mt238290.aspx).




## Kopieren der SQL-Datenbank

Melden Sie sich an der Datenbank „master“ an, indem Sie die Prinzipalanmeldung auf Serverebene oder die Anmeldung verwenden, mit der die zu kopierende Datenbank erstellt wurde. Anmeldungen, bei denen das Prinzipal auf Serverebene nicht verwendet wird, müssen Mitglieder der Rolle „dbmanager“ sein, um Datenbanken zu kopieren. Weitere Informationen zu Anmeldungen und zur Verbindungsherstellung mit dem Server finden Sie unter „Verwalten von Datenbanken, Anmeldungen und Benutzern in Azure SQL-Datenbank“ bzw. „Azure SQL-Datenbankentwicklung: Gewusst-wie-Themen“.

Starten Sie das Kopieren der Quelldatenbank mit der CREATE DATABASE-Anweisung. Durch das Ausführen dieser Anweisung wird der Kopiervorgang für die Datenbank initiiert. Da das Kopieren einer Datenbank ein asynchroner Prozess ist, erfolgt die Rückgabe der CREATE DATABASE-Anweisung, bevor die Datenbank den Kopiervorgang abgeschlossen hat.


### Kopieren einer SQL-Datenbank auf denselben Server

Melden Sie sich an der Datenbank „master“ an, indem Sie die Prinzipalanmeldung auf Serverebene oder die Anmeldung verwenden, mit der die zu kopierende Datenbank erstellt wurde. Anmeldungen, bei denen das Prinzipal auf Serverebene nicht verwendet wird, müssen Mitglieder der Rolle „dbmanager“ sein, um Datenbanken zu kopieren.

Mit diesem Befehl wird „Database1“ in eine neue Datenbank mit dem Namen „Database2“ auf demselben Server kopiert. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Kopiervorgang abgeschlossen ist.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### Kopieren einer SQL-Datenbank auf einen anderen Server

Melden Sie sich an der Datenbank „master“ des Zielservers an. Dies ist der Azure SQL-Datenbank-Server, auf dem die neue Datenbank erstellt werden soll. Verwenden Sie eine Anmeldung, die den gleichen Namen und das gleiche Kennwort wie der Datenbankbesitzer (DBO) der Quelldatenbank auf dem Azure SQL-Datenbank-Quellserver hat. Die Anmeldung auf dem Zielserver muss auch ein Mitglied der Rolle „dbmanager“ sein, oder es muss sich um die Prinzipalanmeldung auf Serverebene handeln.

Mit diesem Befehl wird „Database1“ auf „Server1“ in eine neue Datenbank mit dem Namen „Database2“ auf „Server2“ kopiert. Je nach Größe Ihrer Datenbank kann es einige Zeit dauern, bis der Kopiervorgang abgeschlossen ist.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## Überwachen des Fortschritts des Kopiervorgangs

Überwachen Sie den Kopiervorgang, indem Sie die Ansichten „sys.databases“ und „sys.dm\_database\_copies“ abfragen. Während des Kopiervorgangs wird die Spalte „state\_desc“ der Ansicht „sys.databases“ für die neue Datenbank auf COPYING gesetzt.


- Wenn der Kopiervorgang nicht erfolgreich ist, wird die Spalte „state\_desc“ der Ansicht „sys.databases“ für die neue Datenbank auf SUSPECT gesetzt. Führen Sie in diesem Fall die DROP-Anweisung in der neuen Datenbank aus, und wiederholen Sie den Vorgang später noch einmal.
- Wenn der Kopiervorgang erfolgreich ist, wird die Spalte „state\_desc“ der Ansicht „sys.databases“ für die neue Datenbank auf ONLINE gesetzt. In diesem Fall ist der Kopiervorgang abgeschlossen, und die neue Datenbank ist eine normale Datenbank, die unabhängig von der Quelldatenbank geändert werden kann.



## Nächste Schritte


- Wenn Sie den Kopiervorgang während der Ausführung abbrechen möchten, können Sie die [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) -Anweisung für die neue Datenbank ausführen. Alternativ dazu kann der Kopiervorgang auch abgebrochen werden, indem die DROP DATABASE-Anweisung in der Quelldatenbank ausgeführt wird.
- Nachdem die neue Datenbank auf dem Zielserver online ist, können Sie die [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx)-Anweisung verwenden, um die Benutzer aus der neuen Datenbank den Anmeldungen auf dem Zielserver zuzuordnen. Für alle Benutzer werden in der neuen Datenbank die Berechtigungen beibehalten, über die sie auch in der Quelldatenbank verfügt haben. Der Benutzer, der das Kopieren der Datenbank initiiert hat, wird zum Datenbankbesitzer der neuen Datenbank und erhält eine neue Sicherheits-ID (SID). Nachdem der Kopiervorgang erfolgreich abgeschlossen wurde und bevor andere Benutzer neu zugeordnet werden, kann nur die Anmeldung, über die der Kopiervorgang initiiert wurde (also der Datenbankbesitzer (DBO)), zum Anmelden an der neuen Datenbank verwendet werden.




## Zusätzliche Ressourcen

- [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md)
- [Warnungen zur Notfallwiederherstellung](sql-database-disaster-recovery-drills.md)
- [SQL-Datenbankdokumentation](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=Oct15_HO4-->