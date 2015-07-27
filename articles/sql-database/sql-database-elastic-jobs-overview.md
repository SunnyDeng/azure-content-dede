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
	ms.date="06/25/2015" 
	ms.author="sidneyh" />

# Übersicht über Aufträge für die elastische Datenbank

**Aufträge für die elastische Datenbank** (Vorschau) ermöglichen die Ausführung von T-SQL-Skripts (Aufträge) für alle Datenbanken in einem [ Pool für elastische Datenbanken (Vorschau)](sql-database-elastic-pool.md). Beispielsweise können Sie das Schema in jeder Datenbank problemlos so aktualisieren, dass eine neue Tabelle eingeschlossen wird. Normalerweise müssen Sie unabhängig voneinander eine Verbindung mit jeder Datenbank herstellen, um T-SQL-Anweisungen oder andere administrative Aufgaben durchzuführen. Ein **Auftrag für die elastische Datenbank** übernimmt die Anmeldung und führt Skripts zuverlässig aus, während er den Status der Ausführung für jede Datenbank protokolliert. Anweisungen zum Installieren der Vorschau finden Sie unter [Installieren der Komponente für Aufträge für die elastische Datenbank](sql-database-elastic-jobs-service-installation.md).

![Dienst für elastische Datenbankaufträge][1]

## Vorteile

* Definieren, Verwalten und Beibehalten von T-SQL-Skripts zur Ausführung in einem elastischen Datenbankpool
* Zuverlässiges Ausführen von T-SQL-Skripts mit automatischer skalierter Wiederholung
* Nachverfolgen der Ausführungsstatus des Auftrags

## Szenarios

* Aufgaben mit hohem Leistungsaufwand, wie z. B. die Bereitstellung eines neuen Schemas
* Aktualisieren von Verweisdaten, z. B. allgemeine Produktinformationen aller Datenbanken
* Erneutes Erstellen von Indizes zum Verbessern der Abfrageleistung

## Funktionsweise des Auftrags

1.	Installieren Sie die Dienste, die von elastischen Datenbankaufträgen verwendet werden. Weitere Informationen finden Sie unter [Installieren von Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-service-installation.md). Wenn die Installation fehlschlägt, lesen Sie die Informationen zum [Deinstallieren](sql-database-elastic-jobs-uninstall.md).
2.	Konfigurieren Sie den elastischen Datenbankpool für die Ausführung von Aufträgen durch [Hinzufügen eines Benutzers zu jeder Datenbank](sql-database-elastic-jobs-add-logins-to-dbs.md).
3.	Erstellen Sie ein idempotentes T-SQL-Skript, das für jede Datenbank im Pool ausgeführt werden kann.
4.	Führen Sie folgende Schritte aus, um das Skript auszuführen: [Erstellen und Verwalten von elastischen Datenbankaufträgen](sql-database-elastic-jobs-create-and-manage.md). 

## Komponenten und Preise 

Die folgenden Komponenten arbeiten zusammen, um einen Azure-Clouddienst zu erstellen, der eine Ad-hoc-Ausführung von Verwaltungsaufgaben ermöglicht. Die Komponenten werden installiert und automatisch während der Installation in Ihrem Abonnement konfiguriert. Sie können die Dienste am automatisch generierten identischen Namen erkennen. Der Name ist eindeutig und besteht aus dem Präfix "edj", gefolgt von 21 zufällig generierten Zeichen.

* **Azure Cloud Service**: Elastische Datenbankaufträge (Vorschau) werden als vom Kunden gehosteter Azure-Clouddienst bereitgestellt, um die angeforderten Aufgaben auszuführen. Der Dienst wird im Portal bereitgestellt und im Microsoft Azure-Abonnement gehostet. Der standardmäßig bereitgestellte Dienst wird mit mindestens zwei Workerrollen für hohe Verfügbarkeit ausgeführt. Die Standardgröße der einzelnen Workerrollen ("ElasticDatabaseJobWorker") wird in einer A0-Instanz ausgeführt. Die Preise finden Sie unter [Cloud Services Preise](http://azure.microsoft.com/pricing/details/cloud-services/). 
* **Azure SQL-Datenbank**: Der Dienst verwendet eine Azure SQL-Datenbank, die als **Verwaltungsdatenbank** bezeichnet wird, zum Speichern aller Auftragsmetadaten. Die Standarddienstebene ist S0. Informationen zu den Preisen finden Sie unter [SQL-Datenbank Preise](http://azure.microsoft.com/pricing/details/sql-database/).
* **Azure Service Bus**: Ein Azure Service Bus dient der Koordination der Arbeit innerhalb von Azure Cloud Service. Siehe [Service Bus Preise](http://azure.microsoft.com/pricing/details/service-bus/).
* **Azure Storage**: Ein Azure-Speicherkonto wird zum Speichern von Diagnoseausgabeprotokollen verwendet, falls ein aufgetretenes Problem weiteres Debugging erfordert (üblicherweise für [Azure-Diagnosen](../cloud-services-dotnet-diagnostics.md)). Information zu den Preisen finden Sie unter [Preise für Azure Storage](http://azure.microsoft.com/pricing/details/storage/).

## Nächste Schritte
[Installieren Sie die Komponenten](sql-database-elastic-jobs-service-installation.md), [erstellen Sie ein Protokoll zu jeder Datenbank im Pool, und fügen Sie es hinzu](sql-database-elastic-jobs-add-logins-to-dbs.md). Weitere Informationen zum Erstellen und Verwalten von Aufträgen finden Sie unter [Erstellen und Verwalten von elastischen Datenbankaufträgen](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=July15_HO2-->