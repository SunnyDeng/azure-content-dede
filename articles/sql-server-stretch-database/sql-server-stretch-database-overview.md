<properties
	pageTitle="Stretch-Datenbank-Übersicht | Microsoft Azure"
	description="Finden Sie heraus, wie Stretch-Datenbank Ihre historischen Daten transparent und sicher zu Microsoft Azure Cloud migriert."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglasl"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Übersicht über Stretch-Datenbank

Stretch-Datenbank migriert Ihre historischen Daten transparent und sicher zu Microsoft Azure Cloud.

## Was sind die Vorteile von Stretch-Datenbank?
Stretch-Datenbank bietet die folgenden Vorteile:

**Bietet kostengünstige Verfügbarkeit für inaktive Daten** – Verlagern Sie aktive und inaktive Transaktionsdaten dynamisch von SQL Server zu Microsoft Azure mit SQL Server-Stretch-Datenbank. Im Gegensatz zu anderen Arten der Speicherung von inaktiven Daten, sind Ihre Daten immer online und jederzeit abfragbar. Sie können eine längere Dauer der Datenaufbewahrung anbieten, ohne für große Tabellen wie eine Kundenauftragshistorie hohe Ausgaben tätigen zu müssen. Profitieren Sie vom kostengünstigen Azure statt teure lokale Speicher hochzuskalieren. Wählen Sie einen Tarif und konfigurieren Sie die Einstellungen im Azure-Portal, um die Kontrolle über die Kosten und Datenzugriffsgeschwindigkeiten zu behalten. Skalieren Sie je nach Bedarf hoch oder herunter. Weitere Details finden Sie auf der Seite [SQL-Datenbank – Preise](https://azure.microsoft.com/pricing/details/sql-database/).

**Erfordert keine Änderungen der Abfragen oder Anwendungen** – Greifen Sie unkompliziert auf Ihre SQL Server-Daten zu, unabhängig davon, ob diese lokal oder in die Cloud verlagert sind. Sie legen die Richtlinien dafür fest, wo Daten gespeichert werden, und wie SQL Server das Verschieben von Daten im Hintergrund handhabt. Die ganze Tabelle ist immer online und jederzeit abfragbar. Die Stretch-Datenbank benötigt außerdem keine Änderungen der vorhandenen Abfragen oder Anwendungen – der Speicherort der Daten ist für die Anwendung vollständig transparent.

**Verbessert die lokale Datenwartung** – Reduzieren Sie den lokalen Wartungsaufwand und Speicher für Ihre Daten. Sicherungen für den Teil Ihrer Daten, der sich in der Cloud befindet, werden automatisch ausgeführt. Die Sicherungen für Ihre lokalen Daten werden schneller durchgeführt und enden innerhalb des Wartungsfensters. Ihr Bedarf an lokalem Speicher verringert sich in hohem Umfang. Azure-Speicher kann bis zu 80 % günstiger sein als der Ausbau von lokalen SSD.

**Sichert Ihre Daten sogar während der Migration** – Verlagern Sie Ihre wichtigsten Anwendungen sicher und unbesorgt in die Cloud. Always Encrypted in SQL Server verschlüsselt Ihre sich in Bewegung befindlichen Daten. Sicherheit auf Zeilenebene (RLS) und andere erweiterte Sicherheitsfunktionen von SQL Server funktionieren auch mit Stretch-Datenbank zum Schutz Ihrer Daten.

## Wie funktioniert Stretch-Datenbank?
Nachdem Sie Stretch-Datenbank für eine SQL Server-Instanz, eine Datenbank, und mindestens eine Tabelle aktivieren, beginnt die Anwendung im Hintergrund historische Daten zu Azure zu migrieren.

-   Wenn Sie Ihre historischen Daten in einer separaten Tabelle speichern, können Sie die ganze Tabelle migrieren.

-   Wenn die Tabelle alte und aktuelle Daten enthält, können Sie ein Filterprädikat zum Auswählen der Zeilen zum Migrieren angeben.

Stretch-Datenbank stellt sicher, dass keine Daten verloren gehen, falls ein Failover während der Migration auftritt. Stretch-Datenbank enthält außerdem die Wiederholungslogik, um mit Verbindungsproblemen umzugehen, die während der Migration auftreten können. Eine dynamische Verwaltungsansicht zeigt den Status der Migration.

Sie können die Datenmigration anhalten, um Probleme auf dem lokalen Server zu behandeln, oder um die verfügbare Netzwerkbandbreite zu maximieren.

Sie müssen vorhandene Abfragen und Clientanwendungen nicht ändern. Sie haben weiterhin unkomplizierten Zugriff, sowohl auf lokale als auch auf Remotedaten, sogar während der Migration. Bei Remoteabfragen ist mit einer kurzen Latenzzeit zu rechnen, die allerdings nur bei der Abfrage von historischen Daten auftritt.

![Stretch-Datenbank-Übersicht][StretchOverviewImage1]

## Eignet sich Stretch-Datenbank für Sie?
Wenn die folgenden Aussagen auf Sie zutreffen, kann Ihnen Stretch-Datenbank helfen, Ihre Ansprüche umzusetzen und Ihre Probleme zu lösen.

|Wenn Sie ein Entscheidungsträger sind|Wenn Sie ein Datenbankadministrator sind|
|------------------------------|-------------------|
|Ich muss Transaktionsdaten lange aufbewahren.|Die Größe meiner Tabellen artet aus.|
|Manchmal muss ich die historische Daten abfragen.|Meine Benutzer sagen, dass sie Zugriff auf historische Daten möchten, aber sie nutzen diesen nur selten.|
|Ich habe Apps, inklusive älteren Apps, die ich nicht aktualisieren möchte.|Ich muss immer wieder neuen Speicherplatz kaufen und hinzufügen.|
|Ich will eine Möglichkeit finden, um beim Speichern Geld zu sparen.|Innerhalb meiner SLA kann ich solch große Tabellen weder sichern noch wiederherstellen.|

## Welche Art von Datenbanken und Tabellen eignen sich für Stretch-Datenbank?
Stretch-Datenbank ist für Transaktionsdatenbanken mit großen Mengen an historische Daten gedacht, wobei diese Datenmengen in der Regel in wenigen Tabellen gespeichert werden. Diese Tabellen können mehr als eine Milliarde Zeilen enthalten.

Falls Sie die Funktion „temporale Tabelle“ von SQL Server 2016 verwenden, verwenden Sie Stretch-Datenbank, um die ganze oder einen Teil der zugehörigen Verlaufstabelle zur kostengünstigen Speicherung in Azure zu migrieren. Weitere Informationen finden Sie unter [Manage Retention of Historical Data in System-Versioned Temporal Tables](https://msdn.microsoft.com/library/mt637341.aspx) (Verwalten der Aufbewahrung historischer Daten in temporalen Tabellen mit Systemversionsverwaltung).

Verwenden Sie den Stretch-Datenbank-Ratgeber, eine Funktion von SQL Server 2016-Upgraderatgeber, zum Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank. Weitere Informationen finden Sie unter [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank). Weitere Informationen über mögliche Blockierungsprobleme finden Sie unter [Surface area limitations and blocking issues for Stretch Database ](sql-server-stretch-database-limitations.md) (Oberflächenbegrenzungen und Blockierungsprobleme für Stretch-Datenbank).

## <a name="FAQ"></a>Häufig gestellte Fragen zu Stretch-Datenbank
**Funktioniert Stretch-Datenbank mit &lt;SQL Server-Funktionsname&gt;?**
-   Eine Liste der SQL Server-Funktionen, welche die Nutzung von Tabellen in Stretch verhindern, finden Sie unter [Surface area limitations and blocking issues for Stretch Database](sql-server-stretch-database-limitations.md) (Oberflächenbegrenzungen und Blockierungsprobleme für Stretch-Datenbank).

-   Optional können Sie den SQL Server 2016 Upgraderatgeber herunterladen, und den Stretch-Datenbank-Ratgeber ausführen, um die Eignung von Datenbanken und Tabellen für Stretch-Datenbank zu prüfen. Weitere Informationen finden Sie unter [Identify databases and tables for Stretch Database](sql-server-stretch-database-identify-databases.md) (Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank).

**Kann ich eine andere lokale SQL Server-Instanz zum Ziel der Stretch-Datenbank erklären?** Nein. Stretch-Datenbank unterstützt keine andere lokale SQL Server-Instanz als den Remoteendpunkt.

**Kann ich Stretch deaktivieren, und migrierte Daten zurück in die lokale Tabelle verschieben?** Ja. Weitere Informationen finden Sie unter [Disable Stretch Database and bring back remote data ](sql-server-stretch-database-disable.md) (Stretch-Datenbank deaktivieren und Remotedaten zurückbringen).

## Begriffe
**Lokale Datenbank** –  die lokale SQL Server-Datenbank

**Remoteendpunkt** –  der Speicherort in Microsoft Azure, der die Remotedaten der Datenbank enthält

**Lokale Daten** –  Daten in einer Datenbank (Stretch-Datenbank aktiviert), die aufgrund der Tabellenkonfigurationen von Stretch-Datenbank nicht zu Azure verschoben werden

**Geeignete Daten** –  Daten in einer Datenbank (Stretch-Datenbank aktiviert), die aufgrund der Tabellenkonfigurationen von Stretch-Datenbank zu einem späteren Zeitpunkt zu Azure verschoben werden

**Remotedaten** –  Daten in einer Datenbank (Stretch-Datenbank aktiviert), die bereits zu Azure verschoben wurden

## Architektur
Stretch-Datenbank nutzt die Ressourcen in Microsoft Azure, um die Archivdatenspeicherung und die Abfragenverarbeitung auszulagern.

Wenn Sie Stretch-Datenbank in einer Datenbank aktivieren, eine sichere verknüpfte Serverdefinition im lokalen SQL Server erstellt. Diese verlinkte Serverdefinition hat den Remoteendpunkt als Ziel. Wenn Sie Stretch-Datenbank in einer Tabelle in der Datenbank aktivieren, stellt diese Remoteressourcen bereit und beginnt damit, die geeigneten Daten zu migrieren, sofern die Migration aktiviert ist.

Abfragen in Tabellen, bei denen Stretch-Datenbank aktiviert ist, werden automatisch sowohl in den lokalen Datenbanken als auch am Remoteendpunkt ausgeführt. Stretch-Datenbank nutzt Verarbeitungsleistung in Azure, um Abfragen gegen Remotedaten auszuführen, indem die Abfrage umgeschrieben wird. Diese Umschreibung sehen Sie als Operator „Remoteabfrage“ im neuen Abfrageplan.

![Stretch-Datenbank-Architektur][StretchOverviewImage2]

## Sicherheit und Berechtigungen

### Aktivieren und Deaktivieren von Stretch-Datenbank für eine SQL Server-Instanz
Um mit der Datenbankkonfiguration für Stretch-Datenbank zu beginnen, müssen Sie zunächst unter Verwendung von „sp\_configure“ die Konfigurationsoption "remote data archive" auf Instanzebene ändern. Dieser Vorgang erfordert Sysadmin- oder Serveradmin-Berechtigungen. Wenn diese Option aktiviert ist, können Sie Datenbanken für Stretch-Datenbank konfigurieren, Daten migrieren, und Daten am Remoteendpunkt abfragen.

### Aktivieren und Deaktivieren von Stretch-Datenbank für eine Datenbank oder Tabelle
Um eine Datenbank für Stretch-Datenbank zu konfigurieren, müssen Sie über die Berechtigung „CONTROL DATABASE“ verfügen. Außerdem müssen Sie für den Remoteendpunkt Administratoranmeldeinformationen angeben.

Um eine Tabelle für Stretch-Datenbank zu konfigurieren, müssen Sie über die Berechtigung „ALTER“ für die Tabelle verfügen. Darüber hinaus muss die Datenbank bereits für Stretch-Datenbank konfiguriert sein.

### Datenzugriff
Nur Systemprozesse können auf die verknüpfte Serverdefinition hinter Stretch-Datenbank zugreifen. Die Benutzeranmeldungen können keine Abfragen durch die verknüpfte Serverdefinition an den Remoteendpunkt ausgeben.

Stretch-Datenbank ändert das Berechtigungsmodell einer vorhandenen Datenbank nicht. Die Benutzeranmeldungen können die Daten in einer Tabelle, bei der Stretch-Datenbank aktiviert ist, durch die lokale Datenbank abfragen. Die lokale Datenbank führt für alle vom Benutzer initiierten Aktionen Berechtigungsüberprüfungen durch, genauso wie für jedes andere Objekt auch. Wenn Sie berechtigt sind, auf die Tabelle zuzugreifen, bei der Stretch-Datenbank aktiviert ist, haben Sie Zugriff auf alle Inhalte der Tabelle, für die Sie über Berechtigungen verfügen, unabhängig davon, wo sich die Daten physisch befinden.

![Stretch-Datenbank-Datenzugriffsmodell][StretchOverviewImage3]

## Testen Sie Stretch-Datenbank
**Testen Sie Stretch-Datenbank mit der AdventureWorks-Beispieldatenbank.** Um die AdventureWorks-Beispieldatenbank zu erhalten, laden Sie [hier](https://www.microsoft.com/download/details.aspx?id=49502) mindestens die Datenbankdatei sowie die Beispiel- und Skriptdatei herunter. Nachdem Sie die Beispieldatenbank auf einer Instanz von SQL Server 2016 wiederhergestellt haben, entpacken Sie die Datei „Stretch DB Samples“ aus dem Ordner „Stretch DB “. Führen Sie die Skripts in dieser Datei aus, um den von Ihren Daten verwendeten Speicherplatz vor und nach der Aktivierung von Stretch-Datenbank zu überprüfen, den Fortschritt der Datenmigration zu verfolgen, und um zu bestätigen, dass Sie weiterhin vorhandene Daten abfragen und neue Daten einfügen können, sowohl während als auch nach der Datenmigration.

## Nächster Schritt
**Identifizieren Sie Datenbanken und Tabellen, die sich für Stretch-Datenbank eignen.** Laden Sie SQL Server 2016-Upgraderatgeber herunter, und führen Sie den Stretch-Datenbank-Ratgeber aus, um Datenbanken und Tabellen, die sich für Stretch-Datenbank eignen, zu identifizieren. Der Stretch-Datenbank-Ratgeber erkennt auch Blockierungsprobleme. Weitere Informationen finden Sie unter [Identify databases and tables for Stretch Database ](sql-server-stretch-database-identify-databases.md) (Identifizieren von Datenbanken und Tabellen für Stretch-Datenbank).

<!--Image references-->
[StretchOverviewImage1]: ./media/sql-server-stretch-database-overview/StretchDBOverview.png
[StretchOverviewImage2]: ./media/sql-server-stretch-database-overview/StretchDBOverview1.png
[StretchOverviewImage3]: ./media/sql-server-stretch-database-overview/StretchDBOverview2.png

<!---HONumber=AcomDC_0302_2016-->