<properties
	pageTitle="Ressourceneinschränkungen für Azure SQL-Datenbanken"
	description="Diese Seite beschreibt einige allgemeine Ressourceneinschränkungen für Azure SQL-Datenbanken."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="08/10/2015"
	ms.author="jroth" />


# Ressourceneinschränkungen für Azure SQL-Datenbanken


Azure SQL-Datenbanken überwachen die Verwendung von freigegebenen Ressourcen, wie z. B. das Transaktionsprotokoll, E/A und viele andere Ressourcen. So kann eine Azure SQL-Datenbank Datenbanken innerhalb der festgelegten Ressourcengrenzen halten. Diese Ressourcegrenze bzw. der Schwellenwert wird als Ressourceneinschränkung bezeichnet. Wenn die Ressourcennutzung durch die Clients diese Grenzen überschreitet, entweder auf Ebene eines Mandanten oder auf der physischen Knotenebene, dann reagiert die Azure SQL-Datenbank durch Verwalten der Ressourcennutzung, wodurch es zu Verbindungsabbrüchen oder Anforderungsablehnungen kommt.

> [AZURE.NOTE]Wenn Ressourceneinschränkungen Abfragen davon abhalten, Leistungsprobleme in einer Datenbank zu analysieren, müssen Sie möglicherweise die dedizierte Administratorverbindung \(DAC\) verwenden, die ab Azure SQL-Datenbank V12 verfügbar sein wird. Weitere Informationen zur Verwendung der DAC finden Sie unter [Diagnoseverbindungen für Datenbankadministratoren](https://msdn.microsoft.com/library/ms189595.aspx).

## Zusammenfassungstabelle zu Ressourcenlimits

Die folgende Tabelle enthält eine Zusammenfassung der Grenzwerte für die einzelnen Ressourcen, bei deren Überschreitung Azure SQL-Datenbank Anforderungen verweigert oder Verbindungen mit der betreffenden Ressource beendet und einen Fehlercode zurückgibt. In einigen Fällen bestimmen die [Dienstebene \(Basic, Standard, Premium\)](sql-database-service-tiers.md) und die Leistungsebene die genaue Anzahl. In solchen Fällen informieren Sie sich unter [Dienstebenen und Leistungsstufen der Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn741336.aspx).

[AZURE.INCLUDE [azure-sql-database-limits](../../includes/azure-sql-database-limits.md)]

## Grundlegendes zu Fehlercodes

Manchmal wird derselbe Fehlercode für mehrere Einschränkungsbedingungen einer Ressource zurückgegeben. Diese Bedingungen werden als Zustand in der Fehlermeldung angegeben. Beispielsweise werden die folgenden beiden Fehlermeldungen für die Ressource „Transaktionsprotokolllänge“ angezeigt. Obwohl der Text der Nachricht identisch ist, haben sie basierend auf verschiedenen Einschränkungsbedingungen verschiedene Zustandswerte \(1 oder 2\).

Fehlermeldung 1:

	Msg 40552, Level 17, State 1, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

Fehlermeldung 2:

	Msg 40552, Level 17, State 2, Line 1
	The session has been terminated because of excessive transaction log space usage.
	Try modifying fewer rows in a single transaction.

Dasselbe gilt für die Ressourcen-ID, die in einigen Meldungen angezeigt wird. Die Ressourcen-ID ergibt eine Systemressource, bei der die Beschränkung auftritt.

Im weiteren Verlauf dieses Themas werden die möglichen Fehlercodes im Detail erläutert, einschließlich der Instanzen, in denen der Statuswert und die Ressourcen-ID zusätzliche Informationen zum Fehler enthalten.

## Datenbankgröße

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Wenn der einem Benutzer zugeteilte Speicherplatz in einer Datenbank voll ist, erhält der Benutzer die Fehlermeldung, dass die Datenbank voll ist. |
| **Fehlercode** | **40544**: Das maximale Datenbankkontingent wurde erreicht. Partitionieren oder löschen Sie Daten, Löschen Sie Indizes, oder suchen Sie in der Dokumentation nach möglichen Lösungen. |
| **Begrenzung** | Hängt von der [Dienstebene und Leistungsstufe](https://msdn.microsoft.com/library/azure/dn741336.aspx) ab. |
| **Typ der abgelehnten Anforderungen** | Nicht-Select-DML \(einfügen, aktualisieren, zusammenführen zum Einfügen oder Aktualisieren\). |
| **Empfehlungen** | Verwenden Sie DELETE/DROP-Anweisungen, um Daten aus der Datenbank zu entfernen, bis die Größe der Datenbank unterhalb der Begrenzung liegt. |

## Anmeldungen

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Die SQL-Datenbank steuert die Begrenzung auf die Anzahl gleichzeitiger Anmeldungen, die mit einer Datenbank hergestellt werden können. Wenn die Begrenzung für gleichzeitige Anmeldungen für eine Datenbank erreicht ist, werden neue Anmeldungsanforderungen an die Datenbank verweigert und der Fehlercode 10928 zurückgegeben. |
| **Fehlercode** | **10928**: Ressourcen-ID: 3. Das %s-Limit für die Datenbank beträgt %d und wurde erreicht. Hilfe finden Sie in http://go.microsoft.com/fwlink/?LinkId=267637. |
| **Begrenzung** | Hängt von der [Dienstebene und Leistungsstufe](https://msdn.microsoft.com/library/azure/dn741336.aspx) ab. |
| **Empfehlungen** | Überprüfen Sie "dm\_exec\_connections", um zu sehen, welche Benutzerverbindungen momentan aktiv sind.<br><br>Der Backoff und das Wiederholen der Anmeldung erfolgt nach 10 Sekunden. |

> [AZURE.NOTE]Der Ressourcen-ID-Wert in der Fehlermeldung gibt die Ressource an, für die die Begrenzung erreicht wurde. Für Anmeldungen, die Ressourcen-ID = 3.

## Speicherauslastung

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Wenn es Sitzungen gibt, die über 20 Sekunden auf Arbeitsspeicherzuweisungen warten, werden Sitzungen, die mehr als 16 MB Arbeitsspeicherzuweisung für über 20 Sekunden benötigen, in absteigender Reihenfolge gemäß der Zeit beendet, die die Ressource beansprucht wurde, sodass die älteste Sitzung zuerst beendet wird. Die Beendung von Sitzungen hört auf, sobald der erforderliche Arbeitsspeicher verfügbar ist. |
| **Fehlercode** | **40553**: Die Sitzung wurde aufgrund übermäßiger Speicherauslastung beendet. Versuchen Sie, die Abfrage so zu ändern, dass weniger Zeilen verarbeitet werden. |
| **Begrenzung** | Mehr als 16 MB Arbeitsspeicherzuweisung für über 20 Sekunden. |
| **Typ der abgelehnten Anforderungen** | Abfragen, die Arbeitsspeicherzuweisungen belegen, einschließlich solcher, die Sortierungen und Hashjoins verwenden. |
| **Empfehlungen** | Optimieren Sie Abfragen, die Sortierungen und/oder Hashjoins verwenden. |

## Sitzungen

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Die SQL-Datenbank steuert die Begrenzung auf die Anzahl gleichzeitiger Sitzungen, die mit einer Datenbank hergestellt werden können. Wenn die maximale Anzahl gleichzeitiger Sitzungen für eine Datenbank erreicht ist, werden neue Verbindungen zur Datenbank verweigert, und Benutzer erhalten den Fehlercode „10928“. In der Datenbank werden vorhandenen Sitzungen jedoch nicht beendet. |
| **Fehlercode** | **10928**: Ressourcen-ID: 2. Das %s-Limit für die Datenbank beträgt %d und wurde erreicht. Hilfe finden Sie in http://go.microsoft.com/fwlink/?LinkId=267637. |
| **Begrenzung** | Hängt von der [Dienstebene und Leistungsstufe](https://msdn.microsoft.com/library/azure/dn741336.aspx) ab. |
| **Empfehlungen** | Überprüfen Sie „dm\_exec\_requests“, um anzuzeigen, welche Benutzeranforderungen derzeit ausgeführt werden. |

> [AZURE.NOTE]Der Ressourcen-ID-Wert in der Fehlermeldung gibt die Ressource an, für die die Begrenzung erreicht wurde. Für Sitzungen, die Ressourcen-ID = 2.

## Tempdb

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Ihre Anforderungen für Tempdb können aufgrund der folgenden drei Bedingungen abgelehnt werden:<br><br>**Status 1:** Wenn eine Sitzung mehr als 5 GB Speicherplatz in Tempdb verbraucht, wird die Sitzung beendet.<br><br>**Status 2:** Transaktionen in Tempdb mit über 2 GB großen Protokollen werden abgeschnitten. Beispielvorgänge, die Protokollspeicherplatz in Tempdb belegen: einfügen, aktualisieren, löschen, zusammenführen, Index erstellen.<br><br>**Status 3:** Transaktionen in Tempdb ohne Commit können das Abschneiden von Protokolldateien blockieren. Um dies zu verhindern, darf der Abstand zwischen der ältesten aktiven Protokollsequenznummer für die Transaktion und dem Protokollfragment \(aktuelle Protokollsequenznummer\) in Tempdb 20 % der Größe der Protokolldatei nicht überschreiten. Bei einem Verstoß wird die problematische Transaktion in Tempdb beendet und ein Rollback ausgeführt, damit das Protokoll abgeschnitten werden kann. |
| **Fehlercode** | **40551**: Die Sitzung wurde aufgrund übermäßiger Tempdb-Auslastung beendet. Versuchen Sie, Ihre Abfrage zu ändern, um die temporäre Nutzung des Tabellenspeicherplatzes zu reduzieren. |
| **Begrenzung** | **Status 1:** 5 GB Speicherplatz in Tempdb<br><br>**Status 2:** 2 GB pro Transaktion in Tempdb <br><br>**Status 3:** 20 % des gesamten Protokollspeicherplatzes in Tempdb |
| **Typ der abgelehnten Anforderungen** | Beliebige DDL- oder DML-Anweisungen in Tempdb. |
| **Empfehlungen** | Ändern Sie Abfragen, um die temporäre Nutzung des Tabellenspeicherplatzes zu reduzieren, temporäre Objekte zu löschen, wenn sie nicht mehr benötigt werden, Tabellen abzuschneiden, oder nicht mehr verwendete Tabellen zu entfernen. Reduzieren Sie die Datengröße der Transaktion in Tempdb, indem Sie die Anzahl der Zeilen reduzieren oder den Vorgang in mehrere Transaktionen aufteilen. |

## Transaktionsdauer

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Transaktionen fordern Sperren für Ressourcen wie Zeilen, Seiten oder Tabellen, von denen die Transaktion abhängig ist, und geben die Sperren frei, wenn sie die Abhängigkeit auf den gesperrten Ressourcen nicht mehr vorhanden ist. Ihre Anforderungen können aufgrund der folgenden zwei Bedingungen abgelehnt werden: Status 1: Wenn eine Transaktion länger als 24 Stunden ausgeführt wurde, wird Sie beendet. Status 2: Wenn eine Transaktion länger als 20 Sekunden eine Ressource sperrt, die für eine zugrunde liegende Systemaufgabe erforderlich ist, wird sie beendet. |
| **Fehlercode** | **40549**: Die Sitzung wird beendet, weil es eine lang andauernde Transaktion gibt. Verkürzen Sie die Transaktion. |
| **Begrenzung** | **Status 1:** 24 Stunden<br><br>**Status 2:** 20 Sekunden, wenn eine Transaktion länger als 20 Sekunden eine Ressource sperrt, die für eine zugrunde liegende Systemaufgabe erforderlich ist |
| **Typ der abgelehnten Anforderungen** | Jede Transaktion, die länger als 24 Stunden ausgeführt wurde oder beliebige DDL- oder DML-Anweisungen, die eine Sperre annehmen, was zu einem Blockieren einer Systemaufgabe führt. |
| **Empfehlungen** | Vorgänge für die SQL-Datenbank sollte Benutzereingaben nicht blockieren oder andere Abhängigkeiten haben, die zu lang andauernden Transaktionen führen. |

## Anzahl der Transaktionssperren

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Sitzungen, die über eine Million Sperren beanspruchen, werden beendet. |
| **Fehlercode** | **40550**: Die Sitzung wurde beendet, da zu viele Sperren abgerufen wurden. Versuchen Sie, weniger Zeilen in einer einzigen Transaktion zu lesen oder abzuändern. |
| **Begrenzung** | 1 Million Sperren pro Transaktion |
| **Typ der abgelehnten Anforderungen** | Beliebige DDL- oder DML-Anweisungen. |
| **Empfehlungen** | Die folgenden DMVs können zur Überwachung von Transaktionen verwendet werden: **sys.dm\_tran\_active\_transactions**, **sys.dm\_tran\_database\_transactions**, **sys.dm\_tran\_locks** und **sys.dm\_tran\_session\_transactions**. Je nach Art der Anwendung kann es möglich sein, gröbere Sperrhinweise zu verwenden, wie z. B. **PAGLOCK** oder **TABLOCK**, um die Anzahl der Sperren in einer bestimmten Anweisung/Transaktion zu begrenzen. Beachten Sie, dass sich dies negativ auf die Anwendungsparallelität auswirken kann. |


## Transaktionsprotokolllänge


Für Version V12 von Azure SQL-Datenbank ist die maximale Länge des Transaktionsprotokolls in den Tarifen "Standard" und "Premium" nicht mehr auf 2 GB begrenzt.

In der folgenden Tabelle wird die Transaktionsprotokollgrenze für Version V11 beschrieben.


| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Ihre Anforderungen könnte aufgrund der folgenden beiden Bedingungen abgelehnt werden:<br><br>**Status 1:** Die SQL-Datenbank unterstützt Transaktionen, die Protokolle von bis zu 2 GB Größe generieren. Transaktionen mit Protokollen über diesen Grenzwert hinaus werden abgeschnitten. Beispielvorgänge, die Protokollspeicherplatz in diesem Volume belegen: Einfügen, Aktualisieren, Löschen, Zusammenführen, Index erstellen.<br><br>**Status 2:** Transaktionen ohne Commit können das Abschneiden von Protokolldateien blockieren. Um dies zu verhindern, darf der Abstand zwischen der ältesten aktiven Protokollsequenznummer für die Transaktion und dem Protokollfragment \(aktuelle Protokollsequenznummer\) 20 % der Größe der Protokolldatei nicht überschreiten. Bei einem Verstoß wird die problematische Transaktion beendet und ein Rollback ausgeführt, damit das Protokoll abgeschnitten werden kann. |
| **Fehlercode** | **40552**: Die Sitzung wurde aufgrund übermäßiger Nutzung des Speicherplatzes im Transaktionsprotokoll beendet. Versuchen Sie, weniger Zeilen in einer einzigen Transaktion abzuändern. |
| **Begrenzung** | **Status 1:** 2 GB pro Transaktion<br><br>** Status 2:** 20 % des gesamten Protokollspeicherplatzes |
| **Typ der abgelehnten Anforderungen** | Beliebige DDL- oder DML-Anweisungen. |
| **Empfehlungen** | Bei Zeilenvorgängen verringern Sie die Größe der Daten in der Transaktion, beispielsweise durch Reduzieren der Anzahl der Zeilen oder Aufteilen des Vorgangs in mehrere Transaktionen. Bei Tabellen- bzw. Index-Vorgängen, die eine einzelne Transaktion erfordern, stellen Sie sicher, dass die folgende Formel eingehalten wird: Anzahl der betroffenen Zeilen in Tabelle * \(durchschnittliche Größe des aktualisierten Felds in Bytes + 80\) \< 2 GB \(bei einer Indexneuerstellung sollte die durchschnittliche Größe des aktualisierten Felds durch die durchschnittliche Indexgröße ersetzt werden\). |

## Worker-Threads \(max. Anzahl gleichzeitiger Anforderungen\)

| &nbsp; | Weitere Informationen |
| :--- | :--- |
| **Bedingung** | Die SQL-Datenbank steuert die maximale Anzahl von Worker-Threads \(gleichzeitigen Anforderungen\) an eine Datenbank. Jede Datenbank mit mehr als der zulässigen Anzahl gleichzeitiger Anforderungen erhält den Fehler „10928“, und weitere Anforderungen an diese Datenbank können verweigert werden. |
| **Fehlercodes** | **10928**: Ressourcen-ID: 1. Das %s-Limit für die Datenbank beträgt %d und wurde erreicht. Hilfe finden Sie in http://go.microsoft.com/fwlink/?LinkId=267637.<br><br>** 10929 **: Ressourcen-ID: 1. Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Hilfe finden Sie in http://go.microsoft.com/fwlink/?LinkId=267637. Bitte versuchen Sie es andernfalls später noch einmal. |
| **Begrenzung** | Bei den Tarifen Basic, Standard und Premium hängt dies von der [Leistungsstufe](https://msdn.microsoft.com/library/azure/dn741336.aspx) ab. Für ältere Web/Business Edition-Datenbanken ist die maximale Anzahl gleichzeitiger Anforderungen 180 \(abhängig von der Systemaktivität möglicherweise weniger\). |
| **Empfehlungen** | Überprüfen Sie „dm\_exec\_requests“, um anzuzeigen, welche Benutzeranforderungen derzeit ausgeführt werden.<br><br>Backoff und Wiederholen der Anforderung nach 10 Sekunden. |

> [AZURE.NOTE]Der Ressourcen-ID-Wert in der Fehlermeldung gibt die Ressource an, für die die Begrenzung erreicht wurde. Bei Arbeitsthreads ist die Ressourcen-ID = 1.

Fehler aufgrund von Kontrollen in Arbeitsthreads \(10928/10929\) ersetzen den ursprünglichen Moduldrosselungsfehler \(40501\) für Arbeitsthreads. Unter normalen Bedingungen sollten Benutzer keine Moduldrosselungsfehler für Arbeitsthreads mehr erhalten.

In bestimmten Szenarien wie bei der Verwendung der Funktion für Verbunddatenbanken ist es möglich, den Fehler zur Obergrenze für Arbeitsthreads \(10928\) zu erreichen, und zwar zum Zeitpunkt der Anmeldung bei einer Datenbank, da dieser Vorgang einen Arbeitsthread unter dem Connection.Open-Aufruf nutzt. Dadurch kann die Anwendung über die Obergrenze für Arbeitsthreads versetzt werden. Anwendungen sollten über eine integrierte Logik verfügen, um diese Fehler in solchen Fällen entsprechend zu behandeln.

## Siehe auch

[Ressourcenmanagement für Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn338083.aspx)

[Fehlermeldungen für Clientprogramme der SQL-Datenbank](sql-database-develop-error-messages.md)

[Bewährte Methoden für Azure SQL-Datenbanken zum Verhindern von abgelehnten Anforderungen oder abgebrochenen Verbindungen](https://msdn.microsoft.com/library/azure/dn338082.aspx)

<!---HONumber=August15_HO7-->