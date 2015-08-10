<properties 
	pageTitle="Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen" 
	description="Ein Themeneinstieg, der Links und Empfehlungen für Client-Programme für das Aufbauen von Verbindungen mit Azure SQL-Datenbank mit Technologien wie ADO.NET und PHP zusammenfasst." 
	services="sql-database" 
	documentationCenter="" 
	authors="MightyPen" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/24/2015" 
	ms.author="genemi"/>


# Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen


Dieses Thema ist ein guter Einstieg in die Client-Konnektivität für Azure SQL-Datenbank. Es enthält Links zu Codebeispielen für verschiedene Technologien, die Sie zum Herstellen einer Verbindung und zum Interagieren mit SQL-Datenbank verwenden können. Zu diesen Technologien gehören Enterprise Library, JDBC, PHP und weitere. Die Empfehlungen gelten allgemein und unabhängig von der spezifischen Verbindungstechnologie oder Programmiersprache.


## Technologieunabhängige Empfehlungen


Die Informationen in diesem Abschnitt gelten unabhängig von der spezifischen Technologie, die Sie zum Herstellen der Verbindung mit der SQL-Datenbank verwenden.


- [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336282.aspx) – folgende Aspekte werden erörtert:
 - Ports
 - Firewalls
 - Verbindungszeichenfolgen
- [Ressourcenverwaltung für Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn338083.aspx) – folgende Aspekte werden erörtert:
 - Ressourcenkontrolle
 - Durchsetzung von Grenzwerten
 - Drosselung


Unabhängig davon, welche Verbindungstechnologie Sie verwenden, sind bestimmte Firewalleinstellungen für SQL-Datenbankserver und selbst einzelne Datenbanken von Bedeutung:


- [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx)


## Empfehlung: Authentifizierung


- Verwenden Sie anstatt der Windows-Authentifizierung die SQL-Datenbank-Authentifizierung.
- Geben Sie eine bestimmte Datenbank an, anstatt automatisch die *master*-Datenbank zu verwenden.
- In manchen Fällen muss der Benutzername mit dem Suffix *@ihrservername* angegeben werden, in anderen Fällen dagegen muss das Suffix weggelassen werden. Dies hängt davon ab, wie Ihr Tool oder Ihre API programmiert wurde.
 - Überprüfen Sie dazu die genauen Angaben zu den einzelnen Technologien.
- Stellen Sie eine Verbindung her, indem Sie einen Benutzer in einer [eigenständigen Datenbank](http://msdn.microsoft.com/library/ff929071.aspx) angeben.
 - Dies bietet eine optimierte Leistung und Skalierbarkeit, da dadurch die Anmeldung nicht in der Masterdatenbank erfolgen muss.
 - Sie können die **USE myDatabaseName;**-Transact-SQL-Anweisung in SQL-Datenbank nicht verwenden.


## Empfehlungen: Verbindung


- Ändern Sie in der Clientverbindungslogik das Standardtimeout in 30 Sekunden.
 - Der Standardwert von 15 Sekunden ist zu kurz für Verbindungen, die über das Internet hergestellt werden.
- Stellen Sie sicher, dass die [Firewall für Azure SQL-Datenbank](http://msdn.microsoft.com/library/ee621782.aspx) die ausgehende TCP-Kommunikation über den Port 1433 zulässt.
 - Sie können die Einstellungen für die [Firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) auf einem SQL-Datenbankserver oder für eine einzelne Datenbank konfigurieren.
- Wenn Sie einen [Verbindungspool](http://msdn.microsoft.com/library/8xx3tyca.aspx) verwenden, trennen Sie die Verbindung, sobald Ihre Anwendung sie nicht aktiv verwendet und nicht gerade auf die erneute Verwendung der Verbindung vorbereitet wird.
 - Sofern Ihre Anwendung die Verbindung nicht unmittelbar und ohne Pause für einen anderen Vorgang wiederverwendet, wird die folgende Vorgehensweise empfohlen: <br/><br/>Stellen Sie eine Verbindung her. <br/>Führen Sie über die Verbindung einen Vorgang aus. <br/>Trennen Sie die Verbindung.<br/><br/>
- Verwenden Sie Wiederholungslogik mit der Verbindungslogik, jedoch nur für vorübergehende Fehler. Wenn Sie eine SQL-Datenbank verwenden, können Ihre Versuche zum Herstellen einer Verbindung oder zum Ausführen einer Abfrage aus verschiedenen Gründen fehlschlagen.
 - Ein dauerhafter Grund für den Fehler ist beispielsweise, dass die Verbindungszeichenfolge falsch formatiert ist.
 - Ein vorübergehender Grund für den Fehler ist möglicherweise darauf zurückzuführen, dass die Gesamtarbeitslast in der Azure SQL-Datenbank ausgeglichen werden muss. Der vorübergehende Grund klärt sich von allein. Das bedeutet, dass die Anwendung den Vorgang der Verbindungsherstellung wiederholen soll.
 - Trennen Sie bei der Wiederholung einer Abfrage zunächst die Verbindung, und stellen Sie dann eine andere Verbindung her.


Im nächsten Abschnitt erfahren Sie mehr über die Wiederholungslogik und die Behandlung vorübergehender Fehler.


## Vorübergehende Fehler und Wiederholungslogik


Clouddienste wie Azure und der zugehörige SQL-Datenbankdienst sind stets mit Lastenausgleich und dem Verwalten von Ressourcen beschäftigt. Wenn zwei Datenbanken, die vom selben Computer Daten erhalten, zeitgleich außergewöhnlich hohe Verarbeitungslasten übernehmen, kann das Verwaltungssystem die Notwendigkeit erkennen, die Workload einer der Datenbanken einer anderen Ressource mit Kapazitätsreserven zu übertragen.


Während dieses Vorgangs ist die Datenbank möglicherweise vorübergehend nicht verfügbar. Dadurch werden möglicherweise neue Verbindungen blockiert, oder Ihr Clientprogramm verliert die Verbindung. Die Ressourcenverschiebung ist jedoch vorübergehend, und das Symptom verschwindet möglicherweise nach einigen Minuten oder Sekunden von selbst. Nach Abschluss der Verschiebung kann das Clientprogramm die Verbindung erneut herstellen und seine Arbeit fortsetzen. Die Verarbeitungspause ist besser als ein vermeidbarer Fehlerzustand in Ihrem Client-Programm.


Wenn in SQL-Datenbank ein Fehler auftritt, wird eine [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) ausgelöst. Die `SqlException` enthält einen numerischen Fehlercode in der **Number**-Eigenschaft. Wenn der Fehlercode einen vorübergehenden Fehler bezeichnet, sollte die Anwendung den Aufruf wiederholen.


- [Fehlermeldungen für Clientprogramme der SQL-Datenbank](sql-database-develop-error-messages.md)
 - Der Abschnitt **Vorübergehende Fehler, Fehler bei Verbindungsabbruch** enthält eine Liste der vorübergehenden Fehler, bei denen sich automatische Wiederholungsversuche empfehlen.
 - Führen Sie beispielsweise eine Wiederholung durch, wenn Fehler Nummer 40613 auftritt, der in etwa besagt: <br/>\* Datenbank "mydatabase" auf Server "Server" ist derzeit nicht verfügbar.\*


*Vorübergehende Fehler* werden mitunter auch englisch als *Transient Faults* bezeichnet. In diesem Thema werden die beiden Begriffe als Synonyme angesehen.


Weitere Unterstützung bei vorübergehenden oder anderweitigen Verbindungsfehlern finden Sie unter:


- [Problembehandlung bei Verbindungsproblemen in Azure SQL-Datenbank](http://support.microsoft.com/kb/2980233/)


Links zu Themen mit Codebeispielen, welche die Wiederholungslogik veranschaulichen, finden Sie unter:


- [Clientcodebeispiele für die ersten Schritte mit SQL-Datenbank](sql-database-develop-quick-start-client-code-samples.md)


<a id="gatewaynoretry" name="gatewaynoretry">&nbsp;</a>


## In V12 stellt der Gateway keine Wiederholungslogik mehr bereit.


Vor Version V12 verfügte Azure SQL-Datenbank über einen Gateway, der als Proxy und Puffer für alle Interaktionen zwischen der Datenbank und Ihrem Clientprogramm fungierte. Der Gateway stellte einen zusätzlichen Netzwerkhop dar, der manchmal die Latenz des Datenbankzugriffs erhöhte.


In V12 ist der Gateway entfallen. Jetzt gilt Folgendes:


- Das Clientprogramm interagiert *direkt* mit der Datenbank. Das ist wesentlich effizienter.
- Die geringfügigen Verzerrungen durch den Gateway bei Fehlermeldungen und der übrigen Kommunikation mit dem Programm werden eliminiert.
 - SQL-Datenbank und SQL Server haben eine einheitlichere Sicht auf Ihr Programm.


#### Keine Wiederholungslogik mehr


Die Wiederholungslogik des Gateways hat einige vorübergehende Fehler behandelt. Jetzt muss das Programm in größerem Umfang vorübergehende Fehler behandeln. Codebeispiele zur Wiederholungslogik finden Sie unter:


- [Codebeispiele für die Cliententwicklung und erste Schritte mit SQL-Datenbanken](sql-database-develop-quick-start-client-code-samples.md)
 - Enthält Links zu Codebeispielen mit Wiederholungslogik und zu einfacheren Beispiele, die das Herstellen von Verbindungen und die Abfrage verdeutlichen.
- [Vorgehensweise: Zuverlässiges Herstellen einer Verbindung mit Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/dn864744.aspx)
- [Vorgehensweise: Herstellen einer Verbindung mit Azure SQL-Datenbank mithilfe von ADO.NET mit Enterprise Library](http://msdn.microsoft.com/library/azure/dn961167.aspx)
- [Codebeispiel: Wiederholungslogik in C\# für das Herstellen einer Verbindung mit einer SQL-Datenbank](sql-database-develop-csharp-retry-windows.md)


## Technologien


Die folgenden Themen enthalten Links zu Codebeispielen für mehrere Sprachen und Treiber-Technologien, die Sie zum Herstellen einer Verbindung mit Azure SQL-Datenbank aus dem Clientprogramm verwenden können.


Für Clients, die unter Windows, Linux und Mac OS X ausgeführt werden, sind unterschiedliche Codebeispiele angegeben.


**Allgemeine Beispiele:** Es gibt Codebeispiele für eine Vielzahl von Programmiersprachen, einschließlich PHP, Python, Node.js und .NET C\#. Darüber hinaus gibt es Beispiele für Clients, die unter Windows, Linux und Mac OS X ausgeführt werden.


- [Codebeispiele für die Cliententwicklung und erste Schritte mit SQL-Datenbanken](sql-database-develop-quick-start-client-code-samples.md)
- [Azure SQL-Datenbankentwicklung: Themen zur Vorgehensweise](http://msdn.microsoft.com/library/azure/ee621787.aspx)


**Elastische Skalierung:** Informationen zur Konnektivität für Datenbanken mit elastischer Skalierung finden Sie unter:


- [Erste Schritte mit der Vorschauversion von Elastic Scale für Azure SQL-Datenbank](sql-database-elastic-scale-get-started.md)
- [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md)


**Treiberbibliotheken:** Informationen zu Verbindungstreiberbibliotheken, einschließlich empfohlener Versionen, finden Sie unter:


- [Connection Libraries for SQL Database and SQL Server](sql-database-libraries.md) \(Verbindungsbibliotheken für SQL-Datenbanken und SQL Server, in englischer Sprache\)


## Siehe auch


- [Create your first Azure SQL Database](sql-database-get-started.md) \(Erstellen einer ersten Azure SQL-Datenbank, in englischer Sprache\)

 

<!---HONumber=July15_HO5-->