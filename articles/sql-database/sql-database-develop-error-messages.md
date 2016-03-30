<properties
	pageTitle="SQL-Fehlercodes – Datenbankverbindungsfehler | Microsoft Azure"
	description="Erfahren Sie mehr über SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen, beispielsweise zu häufigen Datenbankverbindungsfehlern, Datenbankkopiefehlern und allgemeinen Fehlern."
	keywords="SQL-Fehlercodes,Zugriff auf SQL,Datenbankverbindungsfehler,SQL-Fehlercodes"
	services="sql-database"
	documentationCenter=""
	authors="annemill"
	manager="jhubbard"
	editor="" />


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="annemill"/>


# SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen: Datenbankverbindungsfehler und andere Probleme


<!--
Old Title on MSDN:  Error Messages (Azure SQL Database)
ShortId on MSDN:  ff394106.aspx
Dx 4cff491e-9359-4454-bd7c-fb72c4c452ca
-->


In diesem Artikel werden SQL-Fehlercodes für SQL-Datenbank-Clientanwendungen aufgeführt, darunter Datenbankverbindungsfehler, vorübergehende Fehler, Fehler zur Ressourcenkontrolle, Datenbankkopiefehler und andere. Die meisten Kategorien gelten speziell für Azure SQL-Datenbank und nicht für Microsoft SQL Server.

In Ihrer Clientanwendung können Sie dem Benutzer für jeden Fehler eine angepasste Meldung anzeigen.

<a id="bkmk_connection_errors" name="bkmk_connection_errors">&nbsp;</a>


## Datenbankverbindungsfehler, vorübergehende Fehler und andere temporäre Fehler

Die folgende Tabelle enthält die SQL-Fehlercodes für Fehler bei Verbindungsverlust und andere vorübergehende Fehler, die auftreten können, wenn Ihre Anwendung versucht, auf SQL-Datenbank zuzugreifen.


### Häufigste Datenbankverbindungsfehler und vorübergehende Fehler


Vorübergehende Fehler machen sich in der Regel in Form einer der folgenden Fehlermeldungen von Ihren Clientprogrammen bemerkbar:

- Die Datenbank <db_name> auf dem Server <Azure_instance> ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später. Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport, und geben Sie die Ablaufverfolgungs-ID der Sitzung <session_id> an.

- Die Datenbank <db_name> auf dem Server <Azure_instance> ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später. Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport, und geben Sie die Ablaufverfolgungs-ID der Sitzung <session_id> an. (Microsoft SQL Server, Fehler: 40613)

- Eine vorhandene Verbindung wurde erzwungenermaßen vom Remotehost geschlossen.

- System.Data.Entity.Core.EntityCommandExecutionException: Fehler beim Ausführen der Befehlsdefinition. Details siehe innere Ausnahme. ---> System.Data.SqlClient.SqlException: Beim Empfangen von Ergebnissen vom Server ist ein Fehler auf Übertragungsebene aufgetreten. (Anbieter: Sitzungsanbieter, Fehler: 19 – Physische Verbindung kann nicht verwendet werden)

Vorübergehende Fehler sollten in Ihrem Clientprogramm eine Ausführung der von Ihnen entworfenen *Wiederholungslogik* auslösen, um den Vorgang erneut durchzuführen. Codebeispiele zur Wiederholungslogik finden Sie unter:


- [Codebeispiele für die Cliententwicklung und erste Schritte mit SQL-Datenbanken](sql-database-develop-quick-start-client-code-samples.md)

- [Maßnahmen zum Behandeln von Verbindungsfehlern und vorübergehenden Fehlern in SQL-Datenbank](sql-database-connectivity-issues.md)


### Fehlercodes für vorübergehende Fehler


| Fehlercode | Schweregrad | Beschreibung |
| ---: | ---: | :--- |
| 4060 | 16 | Die von der Anmeldung angeforderte „%.&#x2a;ls“-Datenbank kann nicht geöffnet werden. Fehler bei der Anmeldung. |
|40197|17|Dienstfehler beim Verarbeiten Ihrer Anforderung. Wiederholen Sie den Vorgang. Fehlercode %d.<br/><br/>Sie erhalten diesen Fehler, wenn der Dienst aufgrund von Software- oder Hardwareupgrades, Hardwarefehlern oder sonstigen Failoverproblemen ausgefallen ist. Der Fehlercode (%d), der in der Meldung zum Fehler 40197 enthalten ist, liefert weitere Informationen zur Art des aufgetretenen Fehlers oder Failovers. Beispiele für Fehlercodes, die in die Meldung zum Fehler 40197 eingebettet sind, lauten 40020, 40143, 40166 und 40540.<br/><br/>Wenn Sie erneut eine Verbindung mit Ihrem SQL-Datenbank-Server herstellen, werden Sie automatisch mit einer intakten Kopie Ihrer Datenbank verbunden. Ihre Anwendung muss den Fehler 40197 abfangen, den für die Problembehandlung in der Meldung enthaltenen Fehlercode (%d) protokollieren und versuchen, eine neue Verbindung mit SQL-Datenbank herzustellen, bis die Ressourcen verfügbar sind, damit Ihre Verbindung wiederhergestellt wird.|
|40501|20|Der Dienst ist derzeit ausgelastet. Wiederholen Sie die Anforderung in 10 Sekunden. Vorgangs-ID: %ls. Code: %d.<br/><br/>*Hinweis:* Weitere Informationen finden Sie unter: <br/>• [Ressourceneinschränkungen für Azure SQL-Datenbanken](sql-database-resource-limits.md).
|40613|17|Die „%.&#x2a;ls“-Datenbank auf Server „%.&#x2a;ls“ ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später. Falls das Problem weiterhin besteht, wenden Sie sich an den Kundensupport und geben als Ablaufverfolgungs-ID der Sitzung „%.&#x2a;ls“ an.|
|49918|16|Anforderung kann nicht verarbeitet werden. Zum Verarbeiten der Anforderung sind nicht genügend Ressourcen vorhanden.<br/><br/>Der Dienst ist derzeit ausgelastet. Versuchen Sie die Anforderung später erneut. |
|49919|16|Die Erstellung oder Aktualisierung der Anforderung kann nicht verarbeitet werden. Für das Abonnement „%ld“ werden derzeit zu viele Erstell- oder Aktualisierungsvorgänge ausgeführt.<br/><br/>Der Dienst ist mit der Verarbeitung mehrerer Erstell- oder Aktualisierungsvorgänge für Ihr Abonnement oder Ihren Server ausgelastet. Zur Ressourcenoptimierung werden Anforderungen derzeit blockiert. Fragen Sie [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) auf ausstehende Vorgänge ab. Warten Sie, bis ausstehende Erstell- oder Aktualisierungsanforderungen abgeschlossen sind, oder löschen Sie eine Ihrer ausstehenden Anforderungen, und wiederholen Sie die Anforderung später. |
|49920|16|Anforderung kann nicht verarbeitet werden. Für das Abonnement „%ld“ werden derzeit zu viele Vorgänge ausgeführt.<br/><br/>Der Dienst ist mit der Verarbeitung mehrerer Vorgänge für dieses Abonnement ausgelastet. Zur Ressourcenoptimierung werden Anforderungen derzeit blockiert. Fragen Sie [sys.dm\_operation\_status](https://msdn.microsoft.com/library/dn270022.aspx) auf den Vorgangsstatus ab. Warten Sie, bis ausstehende Anforderungen abgeschlossen sind, oder löschen Sie eine Ihrer ausstehenden Anforderungen, und wiederholen Sie die Anforderung später. |


<a id="bkmk_b_database_copy_errors" name="bkmk_b_database_copy_errors">&nbsp;</a>

## Fehler beim Kopieren von Datenbanken


Die folgende Tabelle enthält die verschiedenen Fehler, die auftreten können, wenn Sie eine Datenbank in Azure SQL-Datenbank kopieren. Weitere Informationen finden Sie unter [Kopieren einer Azure SQL-Datenbank](sql-database-copy.md).


|Fehlercode|Schweregrad|Beschreibung|
|---:|---:|:---|
|40635|16|Der Client mit der IP-Adresse „%.&#x2a;ls“ ist vorübergehend deaktiviert.|
|40637|16|Das Kopieren von Datenbanken ist derzeit deaktiviert.|
|40561|16|Fehler beim Kopieren der Datenbank. Die Quell- oder die Zieldatenbank ist nicht vorhanden.|
|40562|16|Fehler beim Kopieren der Datenbank. Die Quelldatenbank wurde gelöscht.|
|40563|16|Fehler beim Kopieren der Datenbank. Die Zieldatenbank wurde gelöscht.|
|40564|16|Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang.|
|40565|16|Fehler beim Kopieren der Datenbank. Es darf jeweils nur eine Datenbankkopie von derselben Quelle erstellt werden. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut.|
|40566|16|Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang.|
|40567|16|Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang.|
|40568|16|Fehler beim Kopieren der Datenbank. Die Quelldatenbank ist nicht mehr verfügbar. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang.|
|40569|16|Fehler beim Kopieren der Datenbank. Die Zieldatenbank ist nicht mehr verfügbar. Löschen Sie die Zieldatenbank, und wiederholen Sie den Vorgang.|
|40570|16|Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut.|
|40571|16|Interner Fehler beim Kopieren der Datenbank. Löschen Sie die Zieldatenbank, und versuchen Sie es später erneut.|


<a id="bkmk_c_resource_gov_errors" name="bkmk_c_resource_gov_errors">&nbsp;</a>

## Fehler bei der Ressourcenkontrolle


In der folgende Tabelle sind die Fehler enthalten, die beim Arbeiten mit Azure SQL-Datenbank aufgrund der übermäßigen Nutzung von Ressourcen auftreten. Beispiel:


- Unter Umständen war die Transaktion zu lange geöffnet.
- Unter Umständen enthält die Transaktion zu viele Sperren.
- Unter Umständen verbraucht Ihr Programm zu viel Arbeitsspeicher.
- Unter Umständen belegt Ihr Programm zu viel `TempDb`-Speicherplatz.


**Tipp:** Unter dem folgenden Link finden Sie weitere Informationen, die für die meisten bzw. alle Fehler in diesem Abschnitt gelten:


- [Ressourceneinschränkungen für Azure SQL-Datenbanken](sql-database-resource-limits.md)


|Fehlercode|Schweregrad|Beschreibung|
|---:|---:|:---|
|10928|20|Ressourcen-ID: %d. Das %s-Limit für die Datenbank beträgt %d und wurde erreicht. Weitere Informationen finden Sie unter [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637).<br/><br/>Mit der Ressourcen-ID wird die Ressource angegeben, für die das Limit erreicht wurde. Bei Arbeitsthreads lautet die Ressourcen-ID „1“. Bei Sitzungen lautet die Ressourcen-ID „2“.<br/><br/>*Hinweis:* Weitere Informationen zu diesem Fehler und zu seiner Behebung finden Sie unter:<br/>• [Ressourceneinschränkungen für Azure SQL-Datenbanken](sql-database-resource-limits.md). |
|10929|20|Ressourcen-ID: %d. Die %s-Mindestgarantie beträgt %d, der maximale Wert beträgt %d und die aktuelle Nutzung für die Datenbank beträgt %d. Der Server ist jedoch derzeit zu stark ausgelastet, um Anforderungen über %d für diese Datenbank zu unterstützen. Weitere Informationen finden Sie unter [http://go.microsoft.com/fwlink/?LinkId=267637](http://go.microsoft.com/fwlink/?LinkId=267637). Versuchen Sie es andernfalls später noch einmal.<br/><br/>Mit der Ressourcen-ID wird die Ressource angegeben, für die das Limit erreicht wurde. Bei Arbeitsthreads lautet die Ressourcen-ID „1“. Bei Sitzungen lautet die Ressourcen-ID „2“.<br/><br/>*Hinweis:* Weitere Informationen zu diesem Fehler und zu seiner Behebung finden Sie unter:<br/>• [Ressourceneinschränkungen für Azure SQL-Datenbanken](sql-database-resource-limits.md).|
|40544|20|Das Datenbankkontingent wurde erreicht. Partitionieren oder löschen Sie Daten, löschen Sie Indizes, oder informieren Sie sich in der Dokumentation über mögliche Lösungen.|
|40549|16|Die Sitzung wird aufgrund einer Transaktion mit langer Laufzeit beendet. Verkürzen Sie die Transaktion.|
|40550|16|Die Sitzung wurde beendet, da zu viele Sperren abgerufen wurden. Reduzieren Sie die Anzahl der in einer einzelnen Transaktion gelesenen oder geänderten Zeilen.|
|40551|16|Die Sitzung wurde aufgrund übermäßiger `TEMPDB`-Auslastung beendet. Ändern Sie die Abfrage, um die Verwendung des temporären Tabellenbereichs zu verringern.<br/><br/>*Tipp:* Wenn Sie temporäre Objekte verwenden, können Sie Speicherplatz in der `TEMPDB`-Datenbank sparen, indem Sie die temporären Objekte verwerfen, die von der Sitzung nicht mehr benötigt werden.|
|40552|16|Die Sitzung wurde aufgrund übermäßiger Verwendung des Speicherplatzes für das Transaktionsprotokoll beendet. Reduzieren Sie die Anzahl der in einer einzelnen Transaktion geänderten Zeilen.<br/><br/>*Tipp:* Versuchen Sie beim Durchführen von Masseneinfügungen mit dem Hilfsprogramm `bcp.exe` oder der `System.Data.SqlClient.SqlBulkCopy`-Klasse, die Option `-b batchsize` oder `BatchSize` zu verwenden, um die Anzahl von Zeilen zu beschränken, die bei jeder Transaktion auf den Server kopiert werden. Versuchen Sie es mit der Option `REBUILD WITH ONLINE = ON`, wenn Sie einen Index mit der `ALTER INDEX`-Anweisung neu erstellen.|
|40553|16|Die Sitzung wurde aufgrund übermäßiger Speicherauslastung beendet. Ändern Sie die Abfrage, damit weniger Zeilen verarbeitet werden.<br/><br/>*Tipp:* Wenn Sie die Anzahl von `ORDER BY`- und `GROUP BY`-Vorgängen im Transact-SQL-Code reduzieren, verringern sich auch die Arbeitsspeicheranforderungen Ihrer Abfrage.|


Weitere Informationen zur Ressourcenkontrolle und den dazugehörigen Fehlern finden Sie unter:


- [Ressourceneinschränkungen für Azure SQL-Datenbanken](sql-database-resource-limits.md)



<a id="bkmk_e_general_errors" name="bkmk_e_general_errors">&nbsp;</a>

## Allgemeine Fehler


Die folgende Tabelle enthält alle allgemeinen Fehler, die nicht in eine der vorherigen Kategorien fallen.


|Fehlercode|Schweregrad|Beschreibung|
|---:|---:|:---|
|15006|16|<AdministratorLogin> ist kein gültiger Name, da er ungültige Zeichen enthält.|
|18452|14|Anmeldefehler. Die Anmeldung stammt aus einer nicht vertrauenswürdigen Domäne und kann mit der Windows-Authentifizierung nicht verwendet werden.%.&#x2a;ls (Windows-Anmeldungen werden in dieser SQL Server-Version nicht unterstützt.)|
|18456|14|Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“.%.&#x2a;ls%.&#x2a;ls(Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“. Fehler bei der Kennwortänderung. Kennwortänderungen während der Anmeldung werden in dieser SQL Server-Version nicht unterstützt.)|
|18470|14|Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“. Ursache: Das Konto ist deaktiviert.%.&#x2a;ls|
|40014|16|Es können nicht mehrere Datenbanken in derselben Transaktion verwendet werden.|
|40054|16|Tabellen ohne gruppierten Index werden in dieser SQL Server-Version nicht unterstützt. Erstellen Sie einen gruppierten Index, und wiederholen Sie den Vorgang.|
|40133|15|Dieser Vorgang wird in dieser SQL Server-Version nicht unterstützt.|
|40506|16|Die angegebene SID ist für diese SQL Server-Version ungültig.|
|40507|16|„%.&#x2a;ls“ kann in dieser SQL Server-Version nicht mit Parametern aufgerufen werden.|
|40508|16|Die USE-Anweisung wird zum Wechseln zwischen Datenbanken nicht unterstützt. Verwenden Sie eine neue Verbindung, um eine Verbindung mit einer anderen Datenbank herzustellen.|
|40510|16|Die „%.&#x2a;ls“-Anweisung wird in dieser SQL Server-Version nicht unterstützt.|
|40511|16|Die integrierte „%.&#x2a;ls“-Funktion wird in dieser SQL Server-Version nicht unterstützt.|
|40512|16|Die als veraltet markierte „%ls“-Funktion wird in dieser SQL Server-Version nicht unterstützt.|
|40513|16|Die „%.&#x2a;ls“-Servervariable wird in dieser SQL Server-Version nicht unterstützt.|
|40514|16|„%ls“ wird in dieser SQL Server-Version nicht unterstützt.|
|40515|16|In dieser SQL Server-Version werden keine Verweise auf eine Datenbank und/oder einen Servernamen in „%.&#x2a;ls“ unterstützt.|
|40516|16|Globale temporäre Objekte werden in dieser SQL Server-Version nicht unterstützt.|
|40517|16|Die Schlüsselwort- oder Anweisungsoption „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt.|
|40518|16|Der DBCC-Befehl „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt.|
|40520|16|Die sicherungsfähige Klasse „%S\_MSG“ wird in dieser SQL Server-Version nicht unterstützt.|
|40521|16|Die sicherungsfähige Klasse „%S\_MSG“ wird in dieser SQL Server-Version im Serverbereich nicht unterstützt.|
|40522|16|Der „%.&#x2a;ls“-Datenbankprinzipaltyp wird in dieser SQL Server-Version nicht unterstützt.|
|40523|16|Das implizite Erstellen des Benutzers „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt. Erstellen Sie den Benutzer vor der Verwendung explizit.|
|40524|16|Der „%.&#x2a;ls“-Datentyp wird in dieser SQL Server-Version nicht unterstützt.|
|40525|16|WITH „%.ls“ wird in dieser SQL Server-Version nicht unterstützt.|
|40526|16|Der Rowsetanbieter „%.&#x2a;ls“ wird in dieser SQL Server-Version nicht unterstützt.|
|40527|16|Verbindungsserver werden in dieser SQL Server-Version nicht unterstützt.|
|40528|16|In dieser SQL Server-Version können Benutzer keinen Zertifikaten, asymmetrischen Schlüsseln oder Windows-Anmeldungen zugeordnet werden.|
|40529|16|Die integrierte „%.&#x2a;ls“-Funktion wird in dieser SQL Server-Version im Identitätswechselkontext nicht unterstützt.|
|40532|11|Der bei der Anmeldung angeforderte Server „%.&#x2a;ls“ kann nicht geöffnet werden. Fehler bei der Anmeldung.|
|40553|16|Die Sitzung wurde aufgrund übermäßiger Speicherauslastung beendet. Ändern Sie die Abfrage, damit weniger Zeilen verarbeitet werden.<br/><br/>*Hinweis:* Wenn Sie die Anzahl von `ORDER BY`- und `GROUP BY`-Vorgängen im Transact-SQL-Code reduzieren, verringern sich auch die Arbeitsspeicheranforderungen Ihrer Abfrage.|
|40604|16|CREATE/ALTER DATABASE war nicht möglich, da das Serverkontingent überschritten würde.|
|40606|16|Das Anfügen von Datenbanken wird in dieser SQL Server-Version nicht unterstützt.|
|40607|16|Windows-Anmeldungen werden in dieser SQL Server-Version nicht unterstützt.|
|40611|16|Für Server können maximal 128 Firewallregeln definiert werden.|
|40614|16|Die IP-Endadresse der Firewallregel darf nicht vor der IP-Startadresse liegen.|
|40615|16|Der bei der Anmeldung angeforderte Server „{0}“ kann nicht geöffnet werden. Der Client mit der IP-Adresse „{1}“ hat keine Zugriffsberechtigung für den Server. Aktivieren Sie den Zugriff über das SQL Azure-Portal, oder indem Sie „sp\_set\_firewall\_rule“ für die master-Datenbank ausführen, um eine Firewallregel für diese IP-Adresse bzw. diesen IP-Adressbereich zu erstellen. Es kann bis zu fünf Minuten dauert, bis diese Änderung wirksam wird.|
|40617|16|Der mit <rule name> beginnende Name der Firewallregel ist zu lang. Die maximale Länge beträgt 128.|
|40618|16|Der Name für die Firewallregel darf nicht leer sein.|
|40620|16|Fehler bei der Anmeldung für den Benutzer „%.&#x2a;ls“. Fehler bei der Kennwortänderung. Kennwortänderungen während der Anmeldung werden in dieser SQL Server-Version nicht unterstützt.|
|40627|20|Der Vorgang für Server „{0}“ und die „{1}“-Datenbank wird ausgeführt. Warten Sie vor dem Wiederholen des Vorgangs einige Minuten.|
|40630|16|Fehler bei der Kennwortüberprüfung. Das Kennwort ist zu kurz und erfüllt daher nicht die Richtlinienanforderungen.|
|40631|16|Das Kennwort, das Sie angegeben haben, ist zu lang. Das Kennwort sollte nicht mehr als 128 Zeichen aufweisen.|
|40632|16|Fehler bei der Kennwortüberprüfung. Das Kennwort ist nicht komplex genug und erfüllt daher nicht die Richtlinienanforderungen.|
|40636|16|Der reservierte Datenbankname „%.&#x2a;ls“ kann in diesem Vorgang nicht verwendet werden.|
|40638|16|Ungültige Abonnement-ID <subscription-id>. Das Abonnement ist nicht vorhanden.|
|40639|16|Anforderung entspricht nicht dem <schema error>-Schema.|
|40640|20|Der Server hat eine unerwartete Ausnahme erkannt.|
|40641|16|Der angegebene Pfad ist ungültig.|
|40642|17|Der Server ist derzeit überlastet. Bitte versuchen Sie es später erneut.|
|40643|16|Der angegebene Headerwert „x-ms-version“ ist ungültig.|
|40644|14|Fehler beim Autorisieren des Zugriffs auf das angegebene Abonnement.|
|40645|16|Der Servername <servername> darf nicht leer oder NULL sein. Er darf nur aus den Kleinbuchstaben „a“ - „z“, den Zahlen 0 - 9 und Bindestrichen bestehen. Der Bindestrich darf nicht am Anfang oder Ende des Namens stehen.|
|40646|16|Die Abonnement-ID darf nicht leer sein.|
|40647|16|Der Server <Servername> ist im <Abonnement-ID>-Abonnement nicht enthalten.|
|40648|17|Zu viele Anforderungen wurden durchgeführt. Versuchen Sie es später erneut.|
|40649|16|Es wurde ein ungültiger „content-type“ angegeben. Nur „application“ oder „xml“ wird unterstützt.|
|40650|16|Das <subscription-id>-Abonnement ist nicht vorhanden oder nicht betriebsbereit.|
|40651|16|Fehler beim Erstellen des Servers, weil das <subscription-id>-Abonnement deaktiviert ist.|
|40652|16|Der Server kann nicht verschoben oder erstellt werden, weil das <subscription-id>-Abonnement das Serverkontingent überschreitet.|
|40671|17|Kommunikationsfehler zwischen dem Gateway und dem Verwaltungsdienst. Versuchen Sie es später erneut.|
|40852|16|Die von der Anmeldung angeforderte Datenbank „%.*ls“ auf Server „%.*ls“ kann nicht geöffnet werden. Der Zugriff auf die Datenbank ist nur mit einer Verbindungszeichenfolge mit aktivierter Sicherheit zulässig. Ändern Sie für den Zugriff auf diese Datenbank Ihre Verbindungszeichenfolgen so, dass „secure“ im FQDN des Servers enthalten ist. „'server name'.database.windows.net“ muss demnach in „'server name'.database.`secure`.windows.net“ geändert werden.|
|45168|16|Das SQL Azure-System ist ausgelastet und richtet eine Obergrenze für gleichzeitige DB CRUD-Vorgänge für einen Server ein (z. B. CREATE DATABASE). Für den in der Fehlermeldung angegebenen Server wurde die maximale Anzahl von gleichzeitigen Verbindungen überschritten. Versuchen Sie es erneut. |
|45169|16| Das SQL Azure-System ist ausgelastet und richtet eine Obergrenze für gleichzeitige CRUD-Servervorgänge für ein Abonnement ein (z. B. CREATE SERVER). Für das in der Fehlermeldung angegebene Abonnement wurde die maximale Anzahl von gleichzeitigen Verbindungen überschritten, und die Anforderung wurde verweigert. Versuchen Sie es erneut.|


## Verwandte Links

- [Azure SQL-Datenbanken – Allgemeine Einschränkungen und Leitlinien](sql-database-general-limitations.md)
- [Ressourceneinschränkungen für Azure SQL-Datenbanken](sql-database-resource-limits.md)

<!---HONumber=AcomDC_0316_2016-->
