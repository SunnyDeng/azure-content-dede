<properties
	pageTitle="Maßnahmen zum Behandeln von vorübergehenden Verbindungsfehlern | Microsoft Azure"
	description="Maßnahmen zum Verhindern, Diagnostizieren und Behandeln von Verbindungsausfällen und anderen vorübergehenden Fehlern bei der Interaktion mit Azure SQL-Datenbank."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/30/2015"
	ms.author="genemi"/>


# Behandeln von vorübergehenden Verbindungsausfällen und -fehlern mit SQL-Datenbank


In diesem Thema wird beschrieben, wie Sie vorübergehende Verbindungsausfällen und -fehler verhindern, diagnostizieren und behandeln, die bei Ihrem Clientprogramm während der Interaktion mit Azure SQL-Datenbank auftreten.


<a id="i-transient-faults" name="i-transient-faults"></a>

## Vorübergehende Fehler


Ein vorübergehender Fehler ist ein Fehler, dessen Ursache von selbst behoben wird. Wenn das Azure-System Hardwareressourcen für einen besseren Lastenausgleich bei verschiedenen Workloads rasch verschiebt, treten gelegentlich vorübergehende Fehler auf. Während dieser Neukonfiguration werden Verbindungen mit Azure SQL-Datenbank möglicherweise unterbrochen.


Wenn Ihr Clientprogramm ADO.NET verwendet, wird eine **SqlException**-Ausnahme aufgelöst, um das Programm über den vorübergehenden Fehler zu informieren. Die **Number**-Eigenschaft kann mit der Liste der vorübergehenden Fehler im oberen Bereich des Themas verglichen werden: [Fehlermeldungen für Clientprogramme mit SQL-Datenbank](sql-database-develop-error-messages.md).


### Vorübergehende Fehler bei Verbindungsherstellung und Befehlen


Wenn beim Versuch, eine Verbindung herzustellen, ein vorübergehender Fehler auftritt, sollte nach einigen Sekunden erneut versucht werden, die Verbindung herzustellen.


Wenn während eines SQL-Abfragebefehls ein vorübergehender Fehler auftritt, sollte nicht umgehend versucht werden, den Befehl erneut auszuführen. Stattdessen sollte die Verbindung nach einer Verzögerungszeit neu hergestellt werden. Anschließend kann der Befehl erneut ausgeführt werden.


<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## Wiederholungslogik für vorübergehende Fehler


Clientprogramme, bei denen gelegentlich ein vorübergehender Fehler auftritt, sind stabiler, wenn sie Wiederholungslogik enthalten.


Wenn Ihr Programm über Middleware eines Drittanbieters mit Azure SQL-Datenbank kommuniziert, wenden Sie sich an diesen Anbieter, um Informationen dazu zu erhalten, ob die Middleware Wiederholungslogik umfasst.


### Prinzipien für Wiederholungsversuche


- Wenn beim Versuch, eine Verbindung herzustellen, ein vorübergehender Fehler auftritt, sollte der Versuch wiederholt werden.


- Wenn bei einer SQL-SELECT-Anweisung ein vorübergehender Fehler auftritt, sollte nicht umgehend erneut versucht werden, die Anweisung auszuführen.
 - Stellen Sie stattdessen eine neue Verbindung her, und führen Sie dann die SELECT-Anweisung erneut aus.


- Wenn bei einer SQL-UPDATE-Anweisung ein vorübergehender Fehler auftritt, sollte zunächst eine neue Verbindung hergestellt werden, bevor die UPDATE-Anweisung erneut ausgeführt wird.
 - Mithilfe der Wiederholungslogik muss sichergestellt werden, dass die Datenbanktransaktion entweder vollständig ausgeführt wurde oder die gesamte Transaktion zurückgesetzt wird.


#### Weitere Überlegungen für Wiederholungsversuche


- Bei einem Batchprogramm, das nach Ende der Geschäftszeiten automatisch gestartet und vor Beginn des nächsten Tags beendet wird, sind lange Intervalle zwischen den Wiederholungsversuchen unproblematisch.


- Bei Benutzeroberflächenprogrammen sollte jedoch berücksichtigt werden, dass die Benutzer bei zu langen Wartezeiten dazu tendieren, das Programm zu beenden.
 - Dennoch sollten Wiederholungen nach nur wenigen Sekunden vermieden werden, um eine Überlastung des Systems durch eine große Anzahl von Anforderungen zu verhindern.


### Steigerung der Intervalle zwischen Wiederholungsversuchen



Es wird empfohlen, dass vor dem ersten Wiederholungsversuch eine Verzögerungszeit von fünf Sekunden verwendet wird. Wiederholungsversuche nach weniger als fünf Sekunden können den Clouddienst überfordern. Für jeden nachfolgenden Wiederholungsversuch sollte die Verzögerung exponentiell steigen, bis zu einem Maximum von 60 Sekunden.

Eine Erörterung der *Sperrfrist* für Clients, die ADO.NET verwenden, finden Sie unter [SQL Server-Verbindungspooling (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx).

Darüber hinaus kann es sinnvoll sein, eine maximale Anzahl von Wiederholungsversuchen festzulegen, bevor das Programm beendet wird.


### Codebeispiele mit Wiederholungslogik


Auf der folgenden Seite finden Sie Codebeispiele mit Wiederholungslogik in diversen Programmiersprachen:

- [Schnellstart-Codebeispiele](sql-database-develop-quick-start-client-code-samples.md)


<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

## Testen der Wiederholungslogik


Um Ihre Wiederholungslogik zu testen, müssen Sie einen Fehler simulieren oder verursachen, der behandelt werden kann, während Ihr Programm weiterhin ausgeführt wird.


### Testen der Logik, indem der Computer vom Netzwerk getrennt wird


Eine Möglichkeit, um Ihre Wiederholungslogik zu testen, besteht darin, Ihren Clientcomputer vom Netzwerk zu trennen, während das Programm ausgeführt wird. Bei diesem Problem wird der folgende Fehler ausgegeben: – **SqlException.Number** = 11001 – Fehlermeldung: „Der Host ist nicht bekannt“


Beim ersten Wiederholungsversuch kann Ihr Programm den Namen korrigieren und erneut versuchen, eine Verbindung herzustellen.


Um diesen Test in der Praxis umzusetzen, trennen Sie Ihren Computer vom Netzwerk, bevor Sie das Programm starten. Als Folge ermittelt Ihr Programm einen Laufzeitparameter, der folgende Auswirkungen hat: 1. 11001 wird zeitweilig zur Liste der Fehler hinzugefügt, die als vorübergehend betrachtet werden. 2. Das Programm führt den ersten Verbindungsversuch durch. 3. Nachdem der Fehler ermittelt wurde, wird 11001 aus der Liste entfernt. 4. Der Benutzer wird in einer Meldung aufgefordert, den Computer mit dem Netzwerk zu verbinden. – Die weitere Ausführung wird angehalten (über die Methode **Console.ReadLine** oder über ein Dialogfeld mit einer Schaltfläche „OK“). Nachdem der Computer mit dem Netzwerk verbunden wurde, drückt der Benutzer die EINGABETASTE. 5. Es wird erneut versucht, eine Verbindung herzustellen (dieser Versuch sollte erfolgreich sein).


### Testen der Logik, indem beim Herstellen der Verbindung ein falscher Datenbankname eingegeben wird


Ihr Programm kann vor dem ersten Verbindungsversuch mit Absicht einen falschen Benutzernamen verwenden. Bei diesem Problem wird der folgende Fehler ausgegeben: – **SqlException.Number** = 18456 – Fehlermeldung: „Fehler bei der Anmeldung für den Benutzer 'WRONG\_MyUserName'.“


Beim ersten Wiederholungsversuch kann Ihr Programm den Namen korrigieren und erneut versuchen, eine Verbindung herzustellen.


In der Praxis könnte Ihr Programm einen Laufzeitparameter ermitteln, der folgende Auswirkungen hat: 1. 18456 wird zeitweilig zur Liste der Fehler hinzugefügt, die als vorübergehend betrachtet werden. 2. Der Benutzername wird mit Absicht um „WRONG\_“ ergänzt. 3. Nachdem der Fehler ermittelt wurde, wird 18456 aus der Liste entfernt. 4. „WRONG\_“ wird vom Benutzernamen entfernt. 5. Es wird erneut versucht, eine Verbindung herzustellen (dieser Versuch sollte erfolgreich sein).


<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## Verbindung: Verbindungszeichenfolge


Die für die Verbindung mit Azure SQL-Datenbank erforderliche Verbindungszeichenfolge unterscheidet sich geringfügig von der Zeichenfolge für die Verbindung mit Microsoft SQL Server. Sie können die Verbindungszeichenfolge für Ihre Datenbank aus dem [Azure-Portal](http://portal.azure.com/) kopieren.


[AZURE.INCLUDE [sql-database-include-connection-string-20-portalshots](../../includes/sql-database-include-connection-string-20-portalshots.md)]



### SqlConnection-Parameter von .NET für wiederholte Verbindungsversuche


Wenn Ihr Clientprogramm mithilfe der .NET Framework-Klasse **System.Data.SqlClient.SqlConnection** eine Verbindung mit der Azure SQL-Datenbank herstellt, verwenden Sie .NET 4.5.1 oder höher, sodass Sie die Funktion für wiederholte Verbindungsversuche nutzen können. Details der Funktion finden Sie [hier](http://go.microsoft.com/fwlink/?linkid=393996).


<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->


Beim Erstellen der [Verbindungszeichenfolge](http://msdn.microsoft.com/library/System.Data.SqlClient.SqlConnection.connectionstring.aspx) für Ihr **SqlConnection**-Objekt sollten Sie die Werte der folgenden Parameter abstimmen:

- ConnectRetryCount &nbsp;&nbsp;*(Der Standardwert ist 0. Der Bereich reicht von 0 bis 255.)*
- ConnectRetryInterval &nbsp;&nbsp;*(Der Standardwert ist 1 Sekunde. Der Bereich reicht von 1 bis 60.)*
- Verbindungstimeout &nbsp;&nbsp;*(Der Standardwert ist 15 Sekunden. Der Bereich reicht von 0 bis 2147483647.)*


Insbesondere sollte für Ihre ausgewählten Werte die folgende Gleichung gelten:

- Verbindungstimeout = ConnectRetryCount * ConnectionRetryInterval

Beispiel: Wenn die Anzahl 3 ist und das Intervall 10 Sekunden beträgt, wäre ein Timeout von nur 29 Sekunden für das System nicht ganz ausreichend für den 3. und letzten Verbindungsversuch: 29 < 3 * 10.


#### Vorübergehende Fehler bei Verbindungsherstellung und Befehlen


Mit den Parametern **ConnectRetryCount** und **ConnectRetryInterval** kann Ihr **SqlConnection**-Objekt den Verbindungsversuch wiederholen, ohne Ihr Programm zu unterbrechen, sodass das Programm die Steuerung behält. Die Wiederholungen können in folgenden Situationen auftreten:

- mySqlConnection.Open-Methodenaufruf
- mySqlConnection.Execute-Methodenaufruf

Es gibt eine Besonderheit. Wenn ein vorübergehender Fehler auftritt, während Ihre *Abfrage* ausgeführt wird, wiederholt das **SqlConnection**-Objekt den Verbindungsversuch nicht, und es versucht auch nicht, die Abfrage erneut auszuführen. Allerdings überprüft **SqlConnection** sehr schnell die Verbindung, bevor die Abfrage für die Ausführung gesendet wird. Wenn bei der schnellen Überprüfung ein Verbindungsproblem festgestellt wird, wiederholt **SqlConnection** den Verbindungsvorgang. Ist die Wiederholung erfolgreich, wird die Abfrage für die Ausführung gesendet.


#### Sollte „ConnectRetryCount“ mit der Wiederholungslogik der Anwendung kombiniert werden?

Angenommen, Ihre Anwendung verfügt über eine zuverlässige benutzerdefinierte Wiederholungslogik. Sie könnte den Verbindungsvorgang 4 Mal wiederholen. Wenn Sie **ConnectRetryInterval** und **ConnectRetryCount** = 3 zur Verbindungszeichenfolge hinzufügen, erhöhen Sie die Anzahl der Wiederholungsversuche auf 4 * 3 = 12 Wiederholungen. Möglicherweise ist eine so hohe Anzahl von Wiederholungsversuchen nicht erwünscht.



<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

## Verbindung: IP-Adresse


Der SQL-Datenbankserver muss so konfiguriert werden, dass er Verbindungen von der IP-Adresse des Computers akzeptiert, auf dem Ihr Clientprogramm gehostet wird. Bearbeiten Sie dazu die Firewalleinstellungen über das [Azure-Portal](http://portal.azure.com/).


Wenn Sie die IP-Adresse nicht konfigurieren, tritt bei Ihrem Programm ein Fehler auf, und in einer Fehlermeldung wird die erforderliche IP-Adresse angezeigt.


[AZURE.INCLUDE [sql-database-include-ip-address-22-v12portal](../../includes/sql-database-include-ip-address-22-v12portal.md)]


Weitere Informationen finden Sie unter [Vorgehensweise: Konfigurieren von Firewalleinstellungen für SQL-Datenbank](sql-database-configure-firewall-settings.md)


<a id="c-connection-ports" name="c-connection-ports"></a>

## Verbindung: Ports


Üblicherweise muss lediglich auf dem Computer, auf dem Ihr Clientprogramm gehostet wird, sichergestellt werden, dass Port 1433 für die ausgehende Kommunikation geöffnet ist.


Wenn Ihr Clientprogramm z. B. auf einem Windows-Computer gehostet wird, kann Port 1433 über die Windows-Firewall auf dem Host geöffnet werden:


1. Öffnen Sie die Systemsteuerung.
2. &gt; Alle Systemsteuerungselemente
3. &gt; Windows-Firewall
4. &gt; Erweiterte Einstellungen
5. &gt; Ausgehende Regeln
6. &gt; Aktionen
7. &gt; Neue Regel


Wenn Ihr Clientprogramm auf einem virtuellen Azure-Computer gehostet wird, sollten Sie den folgenden Artikel lesen:<br/>[Andere Ports als 1433 für ADO.NET 4.5 und SQL-Datenbank V12](sql-database-develop-direct-route-ports-adonet-v12.md).


Hintergrundinformationen zur Konfiguration von Ports und IP-Adressen finden Sie hier: [Firewall für Azure SQL-Datenbank](sql-database-firewall-configure.md)


<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

## Verbindung: ADO.NET 4.5


Wenn Ihr Programm ADO.NET-Klassen wie **System.Data.SqlClient.SqlConnection** für die Verbindung mit Azure SQL-Datenbank verwendet, sollten Sie .NET Framework 4.5 oder höher verwenden.


ADO.NET 4.5: für Unterstützung des TDS 7.4-Protokolls. Dies umfasst Verbindungserweiterungen, die über die Erweiterungen in ADO.NET 4.0 hinausgehen. Bietet Unterstützung für Verbindungspooling. Dies umfasst eine effiziente Überprüfung, ob das für Ihr Programm zur Verfügung gestellte Verbindungsobjekt funktioniert.


Bei Verwendung eines Verbindungsobjekts aus einem Verbindungspool sollte Ihr Programm die Verbindung vorübergehend schließen, wenn diese nicht umgehend verwendet wird. Das erneute Öffnen einer Verbindung ist weniger kostenintensiv als das Erstellen einer neuen Verbindung.


Wenn Sie ADO.NET 4.0 oder früher verwenden, sollten Sie ein Upgrade auf die aktuelle ADO.NET-Version durchführen. Seit Juli 2015 können Sie [ADO.NET 4.6 herunterladen](http://blogs.msdn.com/b/dotnet/archive/2015/07/20/announcing-net-framework-4-6.aspx).


<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## Diagnose: Testen, ob Hilfsprogramme eine Verbindung herstellen können


Wenn Ihr Programm keine Verbindung mit Azure SQL-Datenbank herstellen kann, können Sie zu Diagnosezwecken versuchen, eine Verbindung mit einem Hilfsprogramm herzustellen. Idealerweise verwendet das Hilfsprogramm für die Verbindung dieselbe Bibliothek wie Ihr Programm.


Auf einem Windows-Computer können Sie die folgenden Hilfsprogramme nutzen: SQL Server Management Studio (ssms.exe), das ADO.NET für die Verbindung nutzt, oder sqlcmd.exe, das [ODBC](http://msdn.microsoft.com/library/jj730308.aspx) für die Verbindung nutzt.


Sobald die Verbindung hergestellt wurde, testen Sie, ob eine kurze SQL SELECT-Abfrage erfolgreich ausgeführt wird.


<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

## Diagnose: Überprüfen der offenen Ports


Angenommen, Sie vermuten, dass aufgrund von Portproblemen keine Verbindung hergestellt werden kann. Sie können auf Ihrem Computer ein Hilfsprogramm ausführen, das Informationen zu den Portkonfigurationen zurückgibt.


Unter Linux sind die folgenden Hilfsprogramme nützlich: `netstat -nap` und `nmap -sS -O 127.0.0.1` (ersetzen Sie den Beispielwert durch Ihre IP-Adresse.)


Unter Windows kann das Hilfsprogramm [PortQry.exe](http://www.microsoft.com/download/details.aspx?id=17148) nützliche Informationen liefern. Nachfolgend finden Sie eine Beispielabfrage der Portinformationen für einen Azure SQL-Datenbankserver, die auf einem Laptop ausgeführt wurde.


```
[C:\Users\johndoe]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called:
 johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe]
>>
```


<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

## Diagnose: Protokollieren Ihrer Fehler


Periodisch auftretende Probleme lassen sich mitunter am besten diagnostizieren, indem Sie über mehrere Tage oder Wochen hinweg ein allgemeines Muster ermitteln.


Dabei kann es hilfreich sein, sämtliche Fehler auf dem Client zu protokollieren. Möglicherweise lassen sich die Protokolleinträge mit den Fehlerdaten in Zusammenhang bringen, die Azure SQL-Datenbank intern protokolliert.


Zur Unterstützung bei der Protokollierung bietet Enterprise Library 6 (EntLib60) von .NET verwaltete Klassen: – [5 – Protokollierung leicht gemacht: mit dem Protokollierungsanwendungsblock](http://msdn.microsoft.com/library/dn440731.aspx)


<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

## Diagnose: Überprüfen der Systemprotokolle auf Fehler


Nachfolgend finden Sie einige Transact-SQL-SELECT-Anweisungen, mit denen Fehler- und andere Informationen aus Protokollen abgefragt werden.


| Protokollabfrage | Beschreibung |
| :-- | :-- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` | [sys.event\_log](http://msdn.microsoft.com/library/dn270018.aspx) zeigt Informationen zu einzelnen Ereignissen an, darunter einige, die zu vorübergehenden Fehlern oder Verbindungsfehlern führen können.<br/><br/>Idealerweise können Sie die Werte **start\_time** oder **end\_time** mit Informationen zum Zeitpunkt der Probleme bei Ihren Clientprogrammen in Beziehung bringen.<br/><br/>**TIPP:** Zur Ausführung dieser Abfrage müssen Sie eine Verbindung mit der **Masterdatenbank** herstellen. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` | Für eine weitere Diagnose zeigt [sys.database\_connection\_stats](http://msdn.microsoft.com/library/dn269986.aspx) die Gesamtzahl verschiedener Ereignistypen an.<br/><br/>**TIPP:** Zur Ausführung dieser Abfrage müssen Sie eine Verbindung mit der **Masterdatenbank** herstellen. |


### Diagnose: Suche nach Problemereignissen im Protokoll von SQL-Datenbank


Sie können im Protokoll von Azure SQL-Datenbank nach Einträgen zu Problemereignissen suchen. Führen Sie die folgende Transact-SQL-SELECT-Anweisung in der **Masterdatenbank** aus:


```
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```


#### Zurückgegebene Zeilen aus „sys.fn\_xe\_telemetry\_blob\_target\_read\_file“


Nachfolgend wird ein Beispiel einer zurückgegebenen Zeile gezeigt. Die gezeigten Nullwerte sind in anderen Zeilen häufig keine Nullwerte.


```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```


<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## Enterprise Library 6


Bei Enterprise Library 6 (EntLib60) handelt es sich um ein Framework aus .NET-Klassen, mit denen Sie stabile Clouddienstclients implementieren können (u. a. den Azure SQL-Datenbankdienst). Auf der folgenden Seite finden Sie Themen zu den verschiedenen Bereichen, in denen EntLib60 nützlich ist: – [Enterprise Library 6 – April 2013](http://msdn.microsoft.com/library/dn169621%28v=pandp.60%29.aspx)


Einer dieser Bereiche, in denen EntLib60 nützlich ist, ist die Wiederholungslogik für die Behandlung von vorübergehenden Fehlern: – [4 – Durchhaltevermögen, der Schlüssel zum Erfolg: Verwenden des Anwendungsblocks für die Behandlung von vorübergehenden Fehlern](http://msdn.microsoft.com/library/dn440719%28v=pandp.60%29.aspx)


Ein kurzes C#-Codebeispiel unter Verwendung von EntLib60 in der Wiederholungslogik finden Sie hier: – [Codebeispiel: Wiederholungslogik von Enterprise Library 6 in C# für das Herstellen einer Verbindung mit SQL-Datenbank](sql-database-develop-entlib-csharp-retry-windows.md)


> [AZURE.NOTE]Der Quellcode für EntLib60 steht zum öffentlichen [Download](http://go.microsoft.com/fwlink/p/?LinkID=290898) bereit. Microsoft plant keine weiteren Funktions- oder Wartungsupdates für EntLib.


### EntLib60-Klassen für vorübergehende Fehler und Wiederholungsversuche


Die folgenden EntLib60-Klassen sind besonders nützlich für Wiederholungslogik. All diese Klassen befinden sich im Namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling** oder einem untergeordneten Namespace:

*Im Namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling**:*

- Die Klasse **RetryPolicy**
 - Die Methode **ExecuteAction**


- Die Klasse **ExponentialBackoff**


- Die Klasse **SqlDatabaseTransientErrorDetectionStrategy**


- Die Klasse **ReliableSqlConnection**
 - Die Methode **ExecuteCommand**


Im Namespace **Microsoft.Practices.EnterpriseLibrary.TransientFaultHandling.TestSupport**:

- Die Klasse **AlwaysTransientErrorDetectionStrategy**

- Die Klasse **NeverTransientErrorDetectionStrategy**


Unter folgenden Links finden Sie weitere Informationen zu EntLib60:

- Kostenloses E-Book: [Developer's Guide to Microsoft Enterprise Library, 2nd Edition](http://www.microsoft.com/download/details.aspx?id=41145).

- Bewährte Methoden: [Allgemeiner Leitfaden zum Wiederholen von Vorgängen](best-practices-retry-general.md) bietet eine detaillierte Erläuterung wichtiger Aspekte im Zusammenhang mit Wiederholungslogik.

- NuGet-Download: [Enterprise Library – Transient Fault Handling Application Block 6.0](http://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/)


### EntLib60: der Protokollierungsblock


- Der Protokollierungsblock ist eine äußerst flexible und umfassend konfigurierbare Lösung, die Folgendes ermöglicht:
 - Erstellen und Speichern von Protokollmeldungen an diversen Speicherorten.
 - Kategorisieren und Filtern von Meldungen.
 - Erfassen von Kontextinformationen für Debugging und Ablaufverfolgung sowie für Überwachung und allgemeine Protokollierungsanforderungen.


- Der Protokollierungsblock abstrahiert die Protokollierungsfunktionalität vom Protokollziel, sodass der Anwendungscode unabhängig von Speicherort und Typ des Zielprotokollspeichers einheitlich ist.


Einzelheiten finden Sie hier: [5 – Protokollierung leicht gemacht: mit dem Protokollierungsanwendungsblock](https://msdn.microsoft.com/library/dn440731%28v=pandp.60%29.aspx)


### Quellcode der IsTransient-Methode von EntLib60


Nachfolgend wird der C#-Quellcode für die Methode **IsTransient** der Klasse **SqlDatabaseTransientErrorDetectionStrategy** gezeigt. Anhand dieses Quellcodes wird ermittelt, welche Fehler als vorübergehend eingestuft werden und einen Wiederholungsversuch rechtfertigen (April 2013).

Zur Verbesserung der Lesbarkeit wurden eine Reihe von **//comment**-Zeilen aus diesem Code entfernt.


```
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```


## Weitere Informationen


- [SQL Server-Verbindungspooling (ADO.NET)](http://msdn.microsoft.com/library/8xx3tyca.aspx)


- [*Retrying* ist eine Apache 2.0-lizenzierte Allzweckwiederholungsbibliothek, die in **Python** geschrieben wurde und das Hinzufügen von Wiederholungsverhalten zu praktisch jeglichen Elementen vereinfacht.](https://pypi.python.org/pypi/retrying)

<!---HONumber=AcomDC_1203_2015-->