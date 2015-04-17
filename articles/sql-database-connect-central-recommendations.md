<properties 
	pageTitle="Verbindungen mit Azure SQL-Datenbanken: Wichtige Empfehlungen" 
	description="Zentrales Thema mit Links zu spezifischeren Themen über verschiedene Treiber, z. B. ADO.NET und PHP, für die Verbindung mit einer Azure SQL-Datenbank." 
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
	ms.topic="article" 
	ms.date="03/19/2015" 
	ms.author="genemi"/>


#Verbindungen mit SQL-Datenbanken: Wichtige Empfehlungen


<!--
GeneMi , 2015-March-19 Thursday 15:41pm
sql-database-connect-central-recommendations.md
sql-database-connect-*.md

Re SqlException, not .HResult, rather .Number.
-->


Dieses Thema enthält Links zu Codebeispielen für verschiedene Technologien, die Sie zum Herstellen einer Verbindung und zum Interagieren mit einer Azure SQL-Datenbank verwenden können. Zu diesen Technologien gehören Enterprise Library, JDBC und PHP. Die Empfehlungen gelten allgemein und unabhängig von der spezifischen Verbindungstechnologie.


##Technologieunabhängige Empfehlungen


Die Informationen in diesem Abschnitt gelten unabhängig von der spezifischen Technologie, die Sie zum Herstellen der Verbindung mit der SQL-Datenbank verwenden.


- [Richtlinien zum programmgesteuerten Herstellen einer Verbindung mit Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/ee336282.aspx) - Folgende Aspekte werden erörtert:
 - Ports
 - Firewalls
 - Verbindungszeichenfolgen
- [Ressourcenverwaltung für Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn338083.aspx) - Folgende Aspekte werden erörtert:
 - Ressourcenkontrolle
 - Durchsetzung von Grenzwerten
 - Drosselung


Unabhängig davon, welche Verbindungstechnologie Sie verwenden, sind bestimmte Firewalleinstellungen für SQL-Datenbankserver und selbst einzelne Datenbanken von Bedeutung:


- [Firewall für die Azure SQL-Datenbank](https://msdn.microsoft.com/library/azure/ee621782.aspx)


###Kurzempfehlungen


####Verbindung


- Ändern Sie in der Clientverbindungslogik das Standardtimeout in 30 Sekunden.
 - Der Standardwert von 15 Sekunden ist zu kurz für Verbindungen, die über das Internet hergestellt werden.
- Wenn Sie einen [Verbindungspool](http://msdn.microsoft.com/library/8xx3tyca.aspx) verwenden, trennen Sie die Verbindung, wenn Ihre Anwendung sie nicht aktiv verwendet und nicht gerade auf die Wiederverwendung der Verbindung vorbereitet wird.
 - Sofern Ihre Anwendung die Verbindung nicht unmittelbar und ohne Pause für einen anderen Vorgang wiederverwendet,wird die folgende Vorgehensweise empfohlen:
<br/><br/>Stellen Sie eine Verbindung her.
<br/>Führen Sie über die Verbindung einen Vorgang aus.
<br/>Trennen Sie die Verbindung.<br/><br/>
- Verwenden Sie Wiederholungslogik mit der Verbindungslogik, jedoch nur für vorübergehende Fehler. Wenn Sie eine SQL-Datenbank verwenden, können Ihre Versuche zum Herstellen einer Verbindung oder zum Ausführen einer Abfrage aus verschiedenen Gründen fehlschlagen.
 - Ein dauerhafter Grund für den Fehler ist beispielsweise, dass die Verbindungszeichenfolge falsch formatiert ist.
 - Ein vorübergehender Grund für den Fehler ist möglicherweise darauf zurückzuführen, dass die Gesamtarbeitslast in der Azure SQL-Datenbank ausgeglichen werden muss. Der vorübergehende Grund klärt sich von allein. Das bedeutet, dass die Anwendung den Vorgang der Verbindungsherstellung wiederholen soll.
 - Trennen Sie bei der Wiederholung einer Abfrage zunächst die Verbindung, und stellen Sie dann eine andere Verbindung her.
- Stellen Sie sicher, dass die [Firewall für die Azure SQL-Datenbank](http://msdn.microsoft.com/library/ee621782.aspx) die ausgehende TCP-Kommunikation über den Port 1433 zulässt.
 - Sie können die Einstellungen für die [Firewall](http://msdn.microsoft.com/library/azure/ee621782.aspx) auf einem SQL-Datenbankserver oder für eine einzelne Datenbank konfigurieren.


####Authentifizierung


- Verwenden Sie anstatt der Windows-Authentifizierung die SQL-Datenbank-Authentifizierung.
- Geben Sie eine bestimmte Datenbank an, anstatt automatisch die *master*-Datenbank zu verwenden.
- In manchen Fällen muss der Benutzername mit dem Suffix *@IhrServername* angegeben werden, in anderen Fällen dagegen muss das Suffix weggelassen werden. Dies hängt davon ab, wie Ihr Tool oder Ihre API programmiert wurde.
 - Überprüfen Sie dazu die genauen Angaben zu den einzelnen Technologien.
- Stellen Sie eine Verbindung her, indem Sie einen Benutzer in einer [eigenständigen Datenbank](http://msdn.microsoft.com/library/ff929071.aspx) angeben.
 - Dies bietet eine optimierte Leistung und Skalierbarkeit, da dadurch die Anmeldung nicht in der Masterdatenbank erfolgen muss.
 - Sie können die **USE meinDatenbankName;**-Transact-SQL-Anweisung in SQL-Datenbank nicht verwenden.


###Vorübergehende Fehler


Einige Verbindungsfehler mit SQL-Datenbanken sind von dauerhafter Natur. Dann besteht kein Grund, die Herstellung der Verbindung unmittelbar zu wiederholen. Andere Fehler sind dagegen vorübergehend. In diesem Fall empfehlen sich einige automatische Wiederholungsversuche durch Ihre Anwendung. Beispiele:


- *Dauerhaft:* Wenn Sie den Namen Ihres SQL-Datenbankservers beim Herstellen der Verbindung falsch angeben, sind Wiederholungsversuche nicht sinnvoll.
- *Vorübergehend:* Wenn die funktionierende hergestellte Verbindung mit der SQL-Datenbank zu einem bestimmten Zeitpunkt durch das Einschränkungs- oder Lastenausgleichssystem in Azure getrennt wird, werden Wiederholungsversuche empfohlen.


Die durch den Aufruf der SQL-Datenbank ausgelöste [SqlException](https://msdn.microsoft.com/library/system.data.sqlclient.sqlexception.aspx) enthält einen numerischen Fehlercode in der **Number**-Eigenschaft. Wenn der Fehlercode als vorübergehender Fehler aufgeführt ist, sollte die Anwendung den Aufruf wiederholen.


- [Fehlermeldungen (Azure SQL-Datenbank)](http://msdn.microsoft.com/library/azure/ff394106.aspx) - Im Abschnitt **Fehler aufgrund eines Verbindungsabbruchs** sind die vorübergehenden Fehler aufgeführt, bei denen sich Wiederholungsversuche empfehlen.
 - Wiederholen Sie die Verbindungsherstellung, wenn der Fehler 40613 ausgegeben wird, der in etwa wie folgt lautet:<br/>*Die Datenbank 'mydatabase' auf dem Server 'theserver' ist zurzeit nicht verfügbar.*


Weitere Unterstützung bei dauerhaften oder vorübergehenden Verbindungsfehlern finden Sie unter:


- [Problembehandlung bei Verbindungsproblemen in Azure SQL-Datenbank](http://support.microsoft.com/de-de/kb/2980233/de-de)


##Technologien


Das folgende Thema enthält Links zu Codebeispielen für verschiedene Verbindungstechnologien:


- [Azure SQL-Datenbankentwicklung: Themen zur Vorgehensweise](http://msdn.microsoft.com/library/azure/ee621787.aspx)


Informationen zu ADO.NET mit Enterprise Library und zu den Klassen zur Behandlung vorübergehender Fehler finden Sie unter:


- [Vorgehensweise: Zuverlässiges Herstellen einer Verbindung mit einer Azure SQL-Datenbank](http://msdn.microsoft.com/library/azure/dn864744.aspx)


Informationen zu Elastic Scale finden Sie unter:


- [Erste Schritte mit der Vorschauversion von Elastic Scale für die Azure SQL-Datenbank](sql-database-elastic-scale-get-started.md)
- [Datenabhängiges Routing](sql-database-elastic-scale-data-dependent-routing.md)


##Unvollständige oder veraltete Beiträge


Die Links in diesem Abschnitt führen zu Blogbeiträgen oder ähnlichen Quellen und können daher veraltet oder unvollständig sein. Dennoch bieten sie möglicherweise nützliche Hintergrundinformationen.


- [Retry Logic for Transient Failures in Microsoft Azure SQL Database](http://social.technet.microsoft.com/wiki/contents/articles/4235.retry-logic-for-transient-failures-in-windows-azure-sql-database.aspx) (in englischer Sprache)

<!-- -->


<!--HONumber=49-->