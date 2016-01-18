<properties 
   pageTitle="Kopieren von Daten aus Azure Storage-Blobs in den Data  Lake-Speicher | Microsoft Azure"
   description="Verwenden des AdlCopy-Tools zum Kopieren von Daten aus Azure Storage-Blobs in den Data  Lake-Speicher" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/05/2016"
   ms.author="nitinme"/>

# Kopieren von Daten aus Azure Storage-Blobs in den Data  Lake-Speicher

Azure Data Lake-Speicher bietet das Befehlszeilentool [AdlCopy](http://aka.ms/downloadadlcopy), mit dem Sie Daten **aus Azure Storage-Blobs in den Data Lake-Speicher** kopieren können. Sie können AdlCopy nicht verwenden, um Daten aus dem Data Lake-Speicher in Azure Storage-Blobs zu kopieren.

Sie können das AdlCopy-Tool auf zwei Arten verwenden:

* **Eigenständig**, wobei das Tool Ressourcen des Data Lake-Speichers verwendet, um die Aufgabe auszuführen.
* **Mit einem Data Lake Analytics-Konto**, wobei die Einheiten, die Ihrem Data Lake Analytics-Konto zugewiesen sind, zum Ausführen des Kopiervorgangs verwendet werden. Möglicherweise möchten diese Option verwenden, wenn Sie die Kopie in einer vorhersagbaren Weise ausführen möchten.

##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Aktiviertes Azure-Abonnement** für die öffentliche Vorschauversion des Data Lake-Speichers. Weitere Informationen finden Sie in den [Anweisungen](data-lake-store-get-started-portal.md#signup). 
- **Azure Storage-Blobscontainer** mit einigen Daten.
- **Azure Data Lake Analytics-Konto (optional)** – Anweisungen zum Erstellen eines Data Lake-Speicherkontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics](data-lake-analytics/data-lake-analytics-get-started-portal.md) .
- **AdlCopy-Tool**. Installieren Sie das AdlCopy-Tool von [http://aka.ms/downloadadlcopy](http://aka.ms/downloadadlcopy).

## Syntax des AdlCopy-Tools

Verwenden Sie die folgende Syntax, um mit dem AdlCopy-Tool zu arbeiten.

	AdlCopy /Source <Blob source> /Dest <ADLS destination> /SourceKey <Key for Blob account> /Account <ADLA account> /Unit <Number of Analytics units>

Die Parameter in der Syntax werden nachfolgend beschrieben:

| Option | Beschreibung |
|-----------|------------|
| Quelle | Gibt den Speicherort der Quelldaten im Azure-Speicherblob an. Die Quelle kann ein Blobcontainer oder ein Blob sein. |
| Dest | Gibt das Data Lake-Speicherziel für das Kopieren an. |
| SourceKey | Gibt den Speicherzugriffsschlüssel für die Azure-Speicherblobquelle an. |
| Konto | **Optional**. Verwenden Sie diese Option, wenn Sie das Azure Data Lake Analytics-Konto verwenden möchten, um den Kopierauftrag auszuführen. Wenn Sie die Option „/Account“ in der Syntax verwenden, aber kein Data Lake Analytics-Konto angeben, verwendet „AdlCopy“ ein Standardkonto zum Ausführen des Auftrags. Wenn Sie diese Option verwenden, müssen Sie außerdem die Quelle (Azure Storage-Blob) und das Ziel (Azure Data Lake-Speicher) als Datenquellen für das Data Lake Analytics-Konto hinzufügen. |
| Units | Gibt die Anzahl der Data Lake Analytics-Einheiten an, die für den Kopierauftrag verwendet werden. Diese Option ist erforderlich, wenn Sie mithilfe der Option **/Account** das Data Lake Analytics-Konto angeben.                                                                                                                                                                                                                                                                                                                                               



## Verwenden von „AdlCopy“ als eigenständiges Programm

1. Öffnen Sie eine Eingabeaufforderung, und navigieren Sie zu dem Verzeichnis, in dem „AdlCopy“ installiert ist, in der Regel `%HOMEPATH%\Documents\adlcopy`.

2. Führen Sie den folgenden Befehl zum Kopieren eines bestimmten Blobs aus dem Quellcontainer in einen Data Lake-Speicher aus:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

	Beispiel:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== 

	Sie werden aufgefordert, die Anmeldeinformationen für das Azure-Abonnement einzugeben, dem Ihr Data Lake-Speicherkonto zugeordnet ist. Eine Ausgabe ähnlich der folgenden wird angezeigt.

		Initializing Copy.
		Copy Started.
		...............
		0.00% data copied.
		. . .
		. . .
		100% data copied.
		Finishing copy.
		....
		Copy Completed.

1. Sie können auch alle Blobs aus einem Container mit folgendem Befehl in das Data Lake-Speicherkonto kopieren:

		AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>		

	Beispiel:

		AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== 

	

## Verwenden von „AdlCopy“ mit einem Data Lake Analytics-Kontos

Sie können mit Ihrem Data Lake Analytics-Konto auch den AdlCopy-Auftrag zum Kopieren von Daten aus Azure Storage-Blobs in den Data Lake-Speicher ausführen. In der Regel verwenden Sie diese Option, wenn die zu verschiebenden Daten im GB- und TB-Bereich liegen, und Sie einen besseren und vorhersagbareren Leistungsdurchsatz wünschen.

Um das Data Lake Analytics-Konto mit „AdlCopy“ zu verwenden, müssen Sie die Quelle (Azure Storage-Blob) und das Ziel (Azure Data Lake-Speicher) als Datenquellen für das Data Lake Analytics-Konto hinzufügen. Informationen zum Hinzufügen von zusätzlichen Datenquellen zu Ihrem Data Lake Analytics-Konto finden Sie unter [Verwalten der Datenquellen des Data Lake Analytics-Kontos](data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-account-data-sources).

Führen Sie den folgenden Befehl aus:

	AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Unit <number_of_data_lake_analytics_units_to_be_used>

Beispiel:

	AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestore.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeaccount /Units 2 

## Abrechnung

* Bei Verwendung des AdlCopy-Tools als eigenständiges Programm werden Ihnen Ausgangskosten für das Verschieben von Daten berechnet, wenn das Azure Storage-Quellkonto nicht in der gleichen Region wie der Data Lake-Speicher liegt.

* Bei Verwendung des AdlCopy-Tools mit Ihrem Data Lake Analytics-Konto gelten standardmäßige [Data Lake Analytics-Abrechnungsraten](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

## Überlegungen zur Verwendung von AdlCopy

* Das Kopieren von Daten aus Quellen, die gemeinsam mehr als 1000 Dateien und Ordner enthalten, wird von AdlCopy nicht unterstützt. Alternativ können Sie die Dateien und Ordner in verschiedene Unterordner verteilen und den Pfad zu diesen Unterordnern als Quelle angeben.

## Nächste Schritte

- [Sichern von Daten in Data Lake-Speicher](data-lake-store-secure-data.md)
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_0107_2016-->