<properties 
	pageTitle="Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning | Azure" 
	description="Erstellen einer SQL-Tabelle und Laden von Daten in die SQL-Tabelle" 
	services="machine-learning" 
	documentationCenter="" 
	authors="fashah" 
	manager="jacob.spoelstra" 
	editor="" 
	videoId=""
	scriptId="" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/08/2016" 
	ms.author="fashah;bradsev" />

# Verschieben von Daten in eine Azure SQL-Datenbank für Azure Machine Learning

## Einführung
**In diesem Thema** beschreiben wir die Optionen zum Verschieben von Daten aus Flatfiles (CSV- oder TSV-Formate) oder von auf einem lokalen SQL Server gespeicherten Daten in eine Azure SQL-Datenbank. Diese Aufgaben zum Verschieben von Daten in die Cloud gehören zum Cortana-Analyseprozess, der von Azure bereitgestellt wird.

Ein Thema, in dem die Optionen für das Verschieben von Daten in eine lokale SQL Server-Instanz für Machine Learning beschrieben werden, finden Sie unter [Verschieben von Daten in SQL Server auf einem virtuellen Computer in Azure](machine-learning-data-science-move-sql-server-virtual-machine.md).

Das nachstehende **Menü** bietet Links zu Themen, in denen beschrieben wird, wie Daten in anderen Zielumgebungen erfasst werden, in denen die Daten während des Cortana Analytics-Prozesses (CAP) gespeichert und verarbeitet werden.

[AZURE.INCLUDE [cap-ingest-data-selector](../../includes/cap-ingest-data-selector.md)]

In der folgende Tabelle sind die Optionen zum Verschieben von Daten in eine Azure SQL-Datenbank zusammengefasst.

<b>QUELLE</b> |<b>ZIEL: Azure SQL-Datenbank</b> |
-------------- |--------------------------------|
<b>Flatfile (CSV- oder TSV-Format)</b> |<a href="#bulk-insert-sql-query">SQL-Abfrage zum Masseneinfügen |
<b>Lokale SQL Server-Instanz</b> | 1\. <a href="#export-flat-file">Exportieren in eine Flatfile<br> 2. <a href="#insert-tables-bcp">SQL-Datenbankmigrations-Assistent<br> 3. <a href="#db-migration">Datenbanksicherung und -wiederherstellung<br> 4. <a href="#adf">Azure Data Factory |


## <a name="prereqs"></a>Voraussetzungen
Für diese hier beschriebenen Verfahren benötigen Sie:

* Ein **Azure-Abonnement**. Wenn Sie nicht über ein Abonnement verfügen, können Sie sich für ein [kostenloses Testabonnement](https://azure.microsoft.com/pricing/free-trial/) registrieren.
* Ein **Azure-Speicherkonto**. Sie benötigen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](storage-create-storage-account.md#create-a-storage-account). Nachdem Sie das Speicherkonto erstellt haben, müssen Sie den Kontoschlüssel für den Zugriff auf den Speicher abrufen. Siehe [Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
* Zugriff auf eine **Azure SQL-Datenbank**. Wenn Sie eine Azure SQL-Datenbank einrichten müssen, finden Sie in [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md) Informationen dazu, wie Sie eine neue Instanz einer Azure SQL-Datenbank bereitstellen.
* Lokal installierte und konfigurierte **Azure PowerShell**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

**Daten**: Die Migrationsprozesse werden anhand des [NYC Taxi-Datasets](http://chriswhong.com/open-data/foil_nyc_taxi/) demonstriert. Das NYC Taxi-Dataset enthält Informationen zu Daten und Preisen von Fahrten und ist, wie in diesem Beitrag erwähnt, in Azure Blob Storage unter [NYC Taxi Data](http://www.andresmh.com/nyctaxitrips/) verfügbar. Ein Beispiel und eine Beschreibung dieser Dateien finden Sie unter [Beschreibung des NYC Taxi Trips-Datasets](machine-learning-data-science-process-sql-walkthrough.md#dataset).
 
Sie können die hier beschriebenen Verfahren entweder auf einen Satz Ihrer eigenen Daten anpassen oder die Schritte wie beschrieben unter Verwendung des NYC Taxi-Datasets durchführen. Um das NYC Taxi-Dataset in Ihre lokale SQL Server-Datenbank hochzuladen, befolgen Sie das unter [Massenimport von Daten in eine SQL Server-Datenbank](machine-learning-data-science-process-sql-walkthrough.md#dbload) beschriebene Verfahren. Diese Anleitungen gelten für einen SQL Server auf einem virtuellen Azure-Computer, aber das Verfahren zum Hochladen in auf den lokalen SQL Server ist identisch.

## <a name="file-to-azure-sql-database"></a> Verschieben von Daten aus einer Flatfilequelle in eine Azure SQL-Datenbank

Daten in Flatfiles (CSV- oder TSV-Format) können mithilfe einer SQL-Abfrage zum Masseneinfügen in eine Azure SQL-Datenbank verschoben werden.

### <a name="bulk-insert-sql-query"></a>SQL-Abfrage zum Masseneinfügen

Die Schritte des Verfahrens unter Verwendung der SQL-Abfrage zum Masseneinfügen sind mit denen vergleichbar, die in den Abschnitten zum Verschieben von Daten aus einer Flatfilequelle in SQL Server auf einem virtuellen Azure-Computer behandelt werden. Ausführliche Informationen finden Sie unter [SQL-Abfrage zum Masseneinfügen](machine-learning-data-science-move-sql-server-virtual-machine.md#insert-tables-bulkquery).

##<a name="sql-on-prem-to-sazure-sql-database"></a> Verschieben von Daten aus einer lokalen SQL Server-Instanz in eine Azure SQL-Datenbank

Wenn die Quelldaten auf einem lokalen SQL Server gespeichert sind, stehen verschiedene Optionen zum Verschieben der Daten in eine Azure SQL-Datenbank zur Verfügung:

1. [Exportieren in eine Flatfile](#export-flat-file) 
2. [SQL-Datenbankmigrations-Assistent](#insert-tables-bcp)
3. [Datenbanksicherung und -wiederherstellung](#db-migration)
4. [Azure Data Factory](#adf)

Die Schritte für die ersten drei Möglichkeiten sind den Abschnitten unter [Verschieben von Daten in eine SQL Server-Instanz auf einem virtuellen Computer in Azure](machine-learning-data-science-move-sql-server-virtual-machine.md) sehr ähnlich, in denen diese Verfahren behandelt werden. Links zu den entsprechenden Abschnitten in diesem Thema finden Sie weiter unten.

###<a name="export-flat-file"></a>Exportieren in eine Flatfile

Die Schritte für diesen Export in eine Flatfile ähneln den unter [Exportieren in eine Flatfile](machine-learning-data-science-move-sql-server-virtual-machine.md#export-flat-file) behandelten.

###<a name="insert-tables-bcp"></a>SQL-Datenbankmigrations-Assistent

Die Schritte für die Verwendung des SQL-Datenbankmigrations-Assistenten ähneln den unter [SQL-Datenbankmigrations-Assistent](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-migration) behandelten.

###<a name="db-migration"></a>Datenbanksicherung und -wiederherstellung

Die Schritte für die Verwendung der Datenbanksicherung und -wiederherstellung ähneln den unter [Datenbanksicherung und -wiederherstellung](machine-learning-data-science-move-sql-server-virtual-machine.md#sql-backup) behandelten.

###<a name="adf"></a>Azure Data Factory

Das Verfahren zum Verschieben von Daten in eine Azure SQL-Datenbank mit Azure Data Factory (ADF) finden Sie im Thema [Verschieben von Daten von einem lokalen SQL Server in eine Azure SQL-Datenbank mit Azure Data Factory](machine-learning-data-science-move-sql-azure-adf.md). In diesem Thema wird gezeigt, wie Sie mithilfe von ADF über einen Azure-Blobspeicher Daten aus einer lokalen SQL Server-Datenbank in eine Azure SQL-Datenbank verschieben.

Sie sollten die Verwendung von ADF in Betracht ziehen, wenn Daten in einem Hybridszenario kontinuierlich migriert werden müssen, das sowohl auf lokale als auch Cloudressourcen zugreift, und wenn die Daten Transaktionen unterworfen werden oder geändert werden müssen, oder wenn ihnen im Rahmen der Migration eine Geschäftslogik hinzugefügt wird. ADF gestatte die Planung und Überwachung von Aufträgen mithilfe einfacher JSON-Skripts, die das Verschieben von Daten in regelmäßigen Abständen verwalten. ADF verfügt außerdem über weitere Funktionen wie Unterstützung für komplexe Vorgänge.

<!---HONumber=AcomDC_0211_2016-->