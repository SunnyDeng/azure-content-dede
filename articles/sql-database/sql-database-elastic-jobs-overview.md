<properties 
	pageTitle="Übersicht über elastische Datenbankaufträge" 
	description="Veranschaulicht den Dienst für elastische Datenbankaufträge" 
	services="sql-database" 
	documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="ddove; sidneyh" />

# Übersicht über Aufträge für die elastische Datenbank

Mithilfe des Features **Aufträge für die elastische Datenbank** \(Vorschau\) können Sie ein Transact-SQL-Skript \(T-SQL\) zuverlässig ausführen oder ein DACPAC auf eine gesamte Gruppe von Datenbanken anwenden, einschließlich einer benutzerdefinierten Sammlung von Datenbanken, aller Datenbanken in einem [elastischen Datenbankpool \(Vorschau\)](sql-database-elastic-pool.md) oder einem Shardset \(das mithilfe der [Clientbibliothek für die elastische Datenbank](sql-database-elastic-database-client-library.md) erstellt wurde\). In der Vorschau stellt **Aufträge für die elastische Datenbank** derzeit einen vom Kunden gehosteten Azure Cloud Service dar, der die Ad-Hoc-Ausführung und die geplante Ausführung von Verwaltungsaufgaben ermöglicht, die als Aufträge bezeichnet werden. Mithilfe dieses Features können Sie auf einfache und zuverlässige Weise Azure SQL-Datenbank im Maßstab einer gesamten Gruppe von Datenbanken durch Ausführen von Transact-SQL-Skripts zum Durchführen von Verwaltungsvorgängen, wie Schemaänderungen, Verwaltung von Anmeldeinformationen, Aktualisierung von Verweisdaten, Sammlung von Leistungsdaten oder Erfassung von Mandantentelemetrie \(Kundentelemetrie\) verwalten. Normalerweise müssen Sie unabhängige Verbindungen mit jeder einzelnen Datenbank herstellen, um Transact-SQL-Anweisungen oder andere Verwaltungsaufgaben auszuführen. **Aufträge für die elastische Datenbank** verarbeitet die Aufgabe der Anmeldung und der zuverlässigen Skriptausführung, wobei der Ausführungsstatus für jede Datenbank protokolliert wird. Anweisungen zum Installieren finden Sie unter [Installieren der Komponente für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md).

![Dienst für elastische Datenbankaufträge][1]

## Vorteile
* Definieren benutzerdefinierter Gruppen von Azure SQL-Datenbanken
* Definieren, Verwalten und Aufbewahren von Transact-SQL-Skripts zur Ausführung für eine Gruppe von Azure SQL-Datenbanken 
* Bereitstellen einer Anwendung auf Datenebene \(DACPAC\)
* Ausführen von Transact-SQL-Skripts oder zuverlässiges Anwenden von DACPACs mit automatischer Wiederholung und Skalierung
* Nachverfolgen der Ausführungsstatus von Aufträgen
* Definieren von Ausführungszeitplänen
* Übergreifendes Durchführen von Datensammlung in einer Sammlung von Azure SQL-Datenbanken und Speicherung der Ergebnisse in einer einzelnen Zieltabelle

> [AZURE.NOTE]Der Funktionsumfang von **Aufträge für die elastische Datenbank** im Azure-Portal ist derzeit auf eine eingeschränkte Menge von Features begrenzt, die sich außerdem ausschließlich auf elastische SQL Azure-Pools beziehen. Verwenden Sie die PowerShell-APIs, um auf den gesamten aktuell verfügbaren Funktionsumfang zuzugreifen.

## Szenarien

* Aufgaben mit hohem Leistungsaufwand, wie z. B. die Bereitstellung eines neuen Schemas
* Aktualisieren von Verweisdaten, z. B. gemeinsamer Produktinformationen für alle Datenbanken, auch unter Verwendung von Zeitplänen, um die Aktualisierungen wochentäglich nach Büroschluss zu automatisieren.
* Neuerstellung von Indizes zum Verbessern der Abfrageleistung. Für die Neuerstellung kann die Ausführung für eine gesamte Sammlung von Datenbanken auf wiederkehrender Basis konfiguriert werden, etwa in Zeiten mit geringer Auslastung.
* Sammlung von Abfrageergebnissen aus einer Menge von Datenbanken in einer zentralen Tabelle auf fortlaufender Grundlage. Leistungsabfragen können fortlaufend ausgeführt werden und für die Auslösung der Ausführung weiterer Aufgaben konfiguriert werden.
* Ausführung von Abfragen zur Datenverarbeitung mit längerer Laufzeit für eine große Anzahl von Datenbanken, z. B. bei der Sammlung von Kundentelemetrie. Die Ergebnisse werden zur weiteren Analyse in einer einzelnen Zieltabelle gesammelt.

## Einfache End-to-End-Überprüfung von Aufträgen für die elastische Datenbank
1.	Installieren Sie die Komponenten der **Aufträge für die elastische Datenbank**. Weitere Informationen finden Sie unter [Installieren von elastischen Datenbankaufträgen](sql-database-elastic-jobs-service-installation.md). Wenn die Installation fehlschlägt, lesen Sie die Informationen zum [Deinstallieren](sql-database-elastic-jobs-uninstall.md).
2.	Verwenden Sie die PowerShell-APIs, um auf weitere Funktionen zuzugreifen, beispielsweise zum Erstellen von benutzerdefinierten Datenbanksammlungen, zum Hinzufügen von Zeitplänen und/oder dem Erfassen von Ergebnismengen. Verwenden Sie das Portal für die einfache Installation und Erstellung/Überwachung von Aufträgen, die auf die Ausführung in einem **elastischen Datenbankpool** eingeschränkt sind. 
3.	Erstellen Sie verschlüsselte Anmeldeinformationen für die Auftragsausführung und [fügen Sie den Benutzer \(oder die Rolle\) jeder Datenbank in der Gruppe hinzu](sql-database-elastic-jobs-add-logins-to-dbs.md).
4.	Erstellen Sie ein idempotentes T-SQL-Skript, das für jede Datenbank in der Gruppe ausgeführt werden kann.
5.	Führen Sie folgende Schritte aus, um das Skript auszuführen: [Erstellen und Verwalten von elastischen Datenbankaufträgen](sql-database-elastic-jobs-create-and-manage.md). 

## Komponenten und Preise 
Die folgenden Komponenten arbeiten zusammen, um einen Azure-Clouddienst zu erstellen, der eine Ad-hoc-Ausführung von Verwaltungsaufgaben ermöglicht. Die Komponenten werden installiert und automatisch während der Installation in Ihrem Abonnement konfiguriert. Sie können die Dienste am automatisch generierten identischen Namen erkennen. Der Name ist eindeutig und besteht aus dem Präfix "edj", gefolgt von 21 zufällig generierten Zeichen.

* **Azure Cloud Service**: Elastische Datenbankaufträge \(Vorschau\) werden als vom Kunden gehosteter Azure-Clouddienst bereitgestellt, um die angeforderten Aufgaben auszuführen. Der Dienst wird im Portal bereitgestellt und im Microsoft Azure-Abonnement gehostet. Der standardmäßig bereitgestellte Dienst wird mit mindestens zwei Workerrollen für hohe Verfügbarkeit ausgeführt. Die Standardgröße der einzelnen Workerrollen \("ElasticDatabaseJobWorker"\) wird in einer A0-Instanz ausgeführt. Die Preise finden Sie unter [Cloud Services Preise](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Azure SQL-Datenbank**: Der Dienst verwendet eine Azure SQL-Datenbank, die als **Verwaltungsdatenbank** bezeichnet wird, zum Speichern aller Auftragsmetadaten. Die Standarddienstebene ist S0. Informationen zu den Preisen finden Sie unter [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/).
* **Azure Service Bus**: Ein Azure Service Bus dient der Koordination der Arbeit innerhalb von Azure Cloud Service. Siehe [Service Bus Preise](http://azure.microsoft.com/pricing/details/service-bus/).
* **Azure Storage**: Ein Azure-Speicherkonto wird zum Speichern von Diagnoseausgabeprotokollen verwendet, falls ein aufgetretenes Problem weiteres Debugging erfordert \(üblicherweise für [Azure-Diagnosen](../cloud-services-dotnet-diagnostics.md)\). Information zu den Preisen finden Sie unter [Preise für Azure Storage](http://azure.microsoft.com/pricing/details/storage/).

## Funktionsweise von Aufträgen für die elastische Datenbank
1.	Einer Azure SQL-Datenbank wird eine Steuerdatenbank zugeordnet, die alle Meta- und Statusdaten enthält.
2.	Auf die Steuerdatenbank wird durch die **Aufträge für die elastische Datenbank** sowohl zum Starten als auch zum Nachverfolgen der auszuführenden Aufträge zugegriffen.
3.	Zwei verschiedene Rollen kommunizieren mit der Steuerdatenbank: 
	* Controller: Bestimmt, welche Aufträge Aufgaben zum Durchführen des angeforderten Auftrags benötigen und führt Wiederholungsversuche bei Aufträgen mit Fehlern durch Erstellen neuer Auftragsaufgaben aus.
	* Ausführung von Auftragsaufgaben: Führt die Auftragsaufgaben aus.

### Typen von Auftragsaufgaben
Es gibt mehrere Typen von Auftragsaufgaben, die die Ausführung von Aufträgen abwickeln:

* ShardMapRefresh: Fragt die Shardzuordnung ab, um alle als Shards verwendeten Datenbanken zu bestimmen
* ScriptSplit: Teilt das Skript entlang der GO-Anweisungen in Batches auf
* ExpandJob: Erstellt untergeordnete Aufträge für jede Datenbank aus einem Auftrag, der eine Gruppe von Datenbanken zum Ziel hat
* ScriptExecution: Führt ein Skript für eine bestimmte Datenbank mithilfe definierter Anmeldeinformationen aus
* Dacpac: Wendet ein DACPAC auf eine bestimmte Datenbank mithilfe bestimmter Anmeldeinformationen an

## End-to-End-Arbeitsablauf bei der Auftragsausführung
1.	Ein Auftrag wird entweder mithilfe des Portals oder der PowerShell-API in die **Steuerdatenbank** eingefügt. Der Auftrag fordert die Ausführung eines Transact-SQL-Skripts für eine Gruppe von Datenbanken mithilfe bestimmter Anmeldeinformationen an.
2.	Der Controller identifiziert den neuen Auftrag. Auftragsaufgaben werden erstellt und ausgeführt, um das Skript aufzuteilen und die Datenbanken der Gruppe zu aktualisieren. Abschließend wird ein neuer Auftrag erstellt und ausgeführt, um den Auftrag zu erweitern und neue untergeordnete Aufträge zu erstellen, von denen für jeden die Ausführung des Transact-SQL-Skripts für eine bestimmte Datenbank der Gruppe festgelegt ist.
3.	Der Controller identifiziert die erstellten untergeordneten Aufträge. Für jeden Auftrag erstellt der Controller eine Auftragsaufgabe und löst sie aus, um das Skript für eine Datenbank auszuführen. 
4.	Nach dem Abschluss aller Auftragsaufgaben aktualisiert der Controller die Aufträge mit dem Status „abgeschlossen“. Während der Auftragsausführung kann die PowerShell-API verwendet werden, um den aktuellen Status der Auftragsausführung anzuzeigen. Alle von den PowerShell-APIs zurückgegebenen Uhrzeiten werden in UTC angegeben. Gegebenenfalls kann eine Abbruchanforderung eingeleitet werden, um einen Auftrag zu beenden. 

## Nächste Schritte
[Installieren Sie die Komponenten](sql-database-elastic-jobs-service-installation.md), [erstellen Sie ein Protokoll für jede Datenbank in der Gruppe der Datenbanken, und fügen Sie es hinzu](sql-database-elastic-jobs-add-logins-to-dbs.md). Weitere Informationen zum Erstellen und Verwalten von Aufträgen finden Sie unter [Erstellen und Verwalten von elastischen Datenbankaufträgen](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=August15_HO7-->