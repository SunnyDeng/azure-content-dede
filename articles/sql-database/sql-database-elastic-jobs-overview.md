<properties 
	title="Elastic database jobs overview" 
	pageTitle="Übersicht über elastische Datenbankaufträge" 
	description="Veranschaulicht den Dienst für elastische Datenbankaufträge" 
	metaKeywords="azure sql database elastic databases" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="sidneyh" />

# Übersicht über elastische Datenbankaufträge

**Elastische Datenbankaufträge** (Vorschau) ermöglichen die Ausführung von T-SQL-Skripts (Aufträge) für alle Datenbanken in einem [elastischen Datenbankpool (Vorschau)](sql-database-elastic-pool.md). Beispielsweise können Sie das Schema in jeder Datenbank problemlos so aktualisieren, dass eine neue Tabelle eingeschlossen wird. Normalerweise müssen Sie unabhängig voneinander eine Verbindung mit jeder Datenbank herstellen, um T-SQL-Anweisungen oder andere administrative Aufgaben durchzuführen. **Elastische Datenbankaufträge** übernehmen die Anmeldung und führen Skripts zuverlässig aus, während sie den Status der Ausführung für jede Datenbank protokollieren. Anweisungen zum Installieren der Vorschau finden Sie unter [Installieren der Komponente für elastische Datenbankaufträge](sql-database-elastic-jobs-service-installation.md).

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

1.	Installieren Sie die Dienste, die von elastischen Datenbankaufträgen verwendet werden. Weitere Informationen finden Sie unter [Installieren von elastischen Datenbankaufträgen](sql-database-elastic-jobs-service-installation.md). Wenn die Installation fehlschlägt, lesen Sie die Informationen zum [Deinstallieren](sql-database-elastic-jobs-uninstall.md).
2.	Konfigurieren Sie den elastischen Datenbankpool für die Ausführung von Aufträgen durch [Hinzufügen eines Benutzers zu jeder Datenbank](sql-database-elastic-jobs-add-logins-to-dbs.md).
3.	Klicken Sie in der Ansicht für elastische Datenbankpools auf **Auftrag erstellen**.
4.	Geben Sie den Benutzernamen und das Kennwort für die Auftragsverwaltungs-Datenbank (Metadatenspeicher für Aufträge) ein. (Sie erstellen den Benutzernamen und das Kennwort bei der Installation der elastischen Datenbankaufträge.)
5.	Geben Sie auf dem Blatt **Auftrag erstellen** den Namen des Auftrags, den Benutzernamen und das Kennwort für die Zieldatenbanken (mit ausreichenden Berechtigungen für die Skriptausführung) ein, und fügen oder geben Sie das T-SQL-Skript ein.
6.	Klicken Sie auf **Ausführen**. Der Auftrag führt das Skript dann für jede Datenbank aus.
7.	In der Ansicht **Aufträge verwalten** werden alle Aufträge angezeigt, die ausgeführt werden oder wurden, einschließlich des aktuellen Ausführungsstatus.
8.	Klicken Sie auf jeden Auftrag, um die Auftragsdetails für die Ausführung und den Status der Auftragsausführung für jede Datenbank anzuzeigen.
9.	Wenn ein Auftrag fehlschlägt, klicken Sie auf den Namen, um das Fehlerprotokoll anzuzeigen.

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

<!---HONumber=58_postMigration-->