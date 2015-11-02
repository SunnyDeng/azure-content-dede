<properties
	pageTitle="Sicherheitskonfiguration für die Standard- oder aktive Georeplikation"
	description="In diesem Thema werden Sicherheitsaspekte für die Verwaltung standardmäßiger oder aktiver Georeplikationsszenarien für SQL-Datenbank erläutert."
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
	ms.date="10/22/2015"
	ms.author="jroth" />

# Sicherheitskonfiguration für die Standard- oder aktive Georeplikation

## Übersicht
In diesem Thema werden die Authentifizierungsanforderungen zum Konfigurieren und Steuern der [Standard- und aktiven Georeplikation](sql-database-geo-replication-overview.md) und die erforderlichen Schritte zum Einrichten des Benutzerzugriffs auf die sekundäre Datenbank beschrieben. Weitere Informationen zur Verwendung der Georeplikation finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank nach einem Ausfall](sql-database-disaster-recovery.md).

## Verwenden eigenständiger Benutzer
Mit der [V12-Version von Azure SQL-Datenbank](sql-database-v12-whats-new.md) unterstützt SQL-Datenbank jetzt eigenständige Benutzer. Im Gegensatz zu herkömmlichen Benutzern, die Anmeldungen in der „master“-Datenbank zugeordnet werden müssen, wird ein eigenständiger Benutzer vollständig von der Datenbank selbst verwaltet. Dies hat zwei Vorteile. Beim Georeplikationsszenario können sich die Benutzer weiter ohne zusätzliche Konfiguration mit der sekundären Datenbank verbinden, da die Datenbank die Benutzer verwaltet. Es gibt bei dieser Konfiguration auch vom Standpunkt der Anmeldung potenzielle Skalierbarkeits- und Leistungsvorteile. Weitere Informationen finden Sie unter [Eigenständige Datenbankbenutzer – machen Sie Ihre Datenbank portabel](https://msdn.microsoft.com/library/ff929188.aspx).

Wenn Sie bei eigenständigen Benutzern mehrere Datenbanken haben, die dieselbe Anmeldung verwenden, müssen Sie diese Benutzer für jede Datenbank separat verwalten (z. B. für eine Kennwortänderung), anstatt die Anmeldung auf Serverebene zu verwalten.

>[AZURE.NOTE]Wenn Sie den Lesezugriff auf die primäre und sekundäre Datenbank unabhängig voneinander ändern möchten, müssen Sie herkömmliche Anmeldungen und Benutzer verwenden. Eigenständige Benutzer können in der sekundären Datenbank nicht unabhängig von der primären Datenbank verwaltet werden.

## Verwenden herkömmlicher Anmeldungen und Benutzer
Wenn Sie mit herkömmlichen Anmeldungen und Benutzern (keinen eigenständigen Benutzer) arbeiten, müssen zusätzliche Schritt zum Sicherstellen erfolgen, dass dieselben Anmeldungen auf dem sekundären Datenbankserver vorhanden sind. In den folgenden Abschnitten werden die entsprechenden Schritte und zusätzliche Aspekte behandelt.

### Einrichten des Benutzerzugriffs auf die sekundäre Onlinedatenbank
Damit die sekundäre Datenbank als schreibgeschützte Datenbank (bzw. sekundäre Onlinedatenbank) oder einsetzbare Datenbankkopie in einer Failoversituation genutzt werden kann, muss die sekundäre Datenbank über die entsprechende Sicherheitskonfiguration verfügen.

Nur der Serveradministrator kann alle weiter unten in diesem Thema beschriebenen Schritte erfolgreich ausführen. Die spezifischen Berechtigungen für die einzelnen Schritte werden weiter unten in diesem Thema beschrieben.

Die Vorbereitung des Benutzerzugriffs auf eine sekundäre Onlinedatenbank für die aktive Georeplikation kann jederzeit erfolgen. Dies umfasst die drei folgenden Schritte:

1. Bestimmen der Anmeldungen mit Zugriff auf die primäre Datenbank
2. Suchen der SID für diese Anmeldungen auf dem Quellserver
3. Generieren der Anmeldungen auf dem Zielserver mit der entsprechenden SID vom Quellserver

#### 1\. Bestimmen der Anmeldungen mit Zugriff auf die primäre Datenbank:
Der erste Schritt des Prozesses ist das Bestimmen, welche Anmeldungen auf dem Zielserver dupliziert werden müssen. Dies geschieht mit zwei SELECT-Anweisungen, die auf die logische „master“-Datenbank auf dem Quellserver und auf die primäre Datenbank selbst angewendet werden.

Nur der Serveradministrator oder ein Mitglied der Serverrolle **LoginManager** kann mit der folgenden SELECT-Anweisung die Anmeldungen auf dem Quellserver bestimmen.

	SELECT [name], [sid] 
	FROM [sys].[sql_logins] 
	WHERE [type_desc] = 'SQL_Login'

Nur ein Mitglied der Datenbankrolle „db\_owner“, der Benutzer „dbo“ oder der Serveradministrator kann alle Datenbankbenutzerprinzipale in der primären Datenbank bestimmen.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

#### 2\. Suchen der SID für die in Schritt 1 identifizierten Anmeldungen:
Durch Vergleichen der Ausgabe der Abfragen aus dem vorherigen Abschnitt und Zuordnen der SIDs können Sie die Serveranmeldung dem Datenbankbenutzer zuordnen. Anmeldungen, die über einen Datenbankbenutzer mit einer entsprechenden SID verfügen, haben als Datenbankbenutzerprinzipal Benutzerzugriff auf diese Datenbank.

Die folgende Abfrage kann verwendet werden, um alle Benutzerprinzipale mit ihre SIDs in einer Datenbank anzuzeigen. Nur ein Mitglied der Datenbankrolle „db\_owner“ oder Serveradministrator kann diese Abfrage ausführen.

	SELECT [name], [sid]
	FROM [sys].[database_principals]
	WHERE [type_desc] = 'SQL_USER'

>[AZURE.NOTE]Die Benutzer **INFORMATION\_SCHEMA** und **sys** haben die SID *NULL*, und die SID von **guest** ist **0x00**. Die SID von **dbo** kann mit *0x01060000000001648000000000048454* beginnen, wenn der Datenbankersteller der Serveradministrator und kein Mitglied von **DbManager** war.

#### 3\. Generieren der Anmeldungen auf dem Zielserver:
Der letzte Schritt besteht darin, auf dem oder den Zielservern die Anmeldungen mit den entsprechenden SIDs zu erstellen. Die grundlegende Syntax ist wie folgt.

	CREATE LOGIN [<login name>]
	WITH PASSWORD = <login password>,
	SID = <desired login SID>

>[AZURE.NOTE]Wenn Sie Benutzerzugriff auf die sekundäre Datenbank, aber nicht auf die primäre gewähren möchten, ändern Sie dazu die Benutzeranmeldung auf dem primären Server mithilfe der folgenden Syntax.
>
>ALTER LOGIN <login name> DISABLE
>
>DISABLE ändert nicht das Kennwort, sodass Sie sie bei Bedarf stets aktivieren können.

## Einrichten des Benutzerzugriff bei Beendigung einer fortlaufenden Kopierbeziehung
Bei einem Failover muss die fortlaufende Kopierbeziehung zwischen der primären und sekundären Datenbank beendet werden. Weitere Informationen zu diesem Prozess finden Sie unter [Wiederherstellen einer Azure SQL-Datenbank nach einem Ausfall](sql-database-disaster-recovery.md).

Bei der standardmäßigen Georeplikation kann der Benutzer nicht auf die sekundäre Offlinedatenbank zugreifen, weshalb die Änderungen an den Benutzerkonten nach Beendigung der fortlaufenden Kopierbeziehung erfolgen müssen.

Wenn die Anmelde-SIDs nicht auf dem Zielserver dupliziert wurden, ist der Zugriff auf die sekundäre Datenbank nach der Beendigung auf den Serveradministrator beschränkt. Wenn der Benutzer, der die Replikation einleitet, die Rolle „DbManager“ hat, hat er keinen Zugriff auf die sekundäre Datenbank, es sei denn, seine Anmelde-ID wurde vom Quellserver dupliziert. Dies gilt für die gesamte Dauer des Replikationsprozesses.

Wenn die Replikation beendet ist, wird im Rahmen des Beendigungsvorgangs der Benutzerprinzipal [dbo] entsprechend der Anmelde-SID des Benutzers geändert, der die Replikation eingeleitet hat, und für diesen Benutzer wird der Zugriff wiederhergestellt. Dies ist bei anderen Benutzern der Datenbank nicht der Fall.

Das Benutzerkonto samt dazugehöriger Anmeldung, das zum Einleiten des Beendigungsvorgangs verwendet wurde, muss auf dem Zielserver und in der Datenbank vorhanden sein. Damit wird sichergestellt, dass das Benutzerkonto auf die sekundäre Datenbank zugreifen kann, nachdem die Beendigung abgeschlossen wurde.

Weitere Informationen zu den Schritten, die nach einem Failover erforderlich sind, finden Sie unter [Abschließen der wiederhergestellten Azure SQL-Datenbank](sql-database-recovered-finalize.md).

## Nächste Schritte
Weitere Informationen zur Georeplikation und zu zusätzlichen Geschäftskontinuitätsfeatures von Azure SQL-Datenbank finden Sie unter [Geschäftskontinuität – Übersicht](sql-database-business-continuity.md).

<!---HONumber=Oct15_HO4-->