<properties 
   pageTitle="Erste Schritte mit Azure Data Lake Analytics unter Verwendung der Azure-Befehlszeilenschnittstelle | Azure" 
   description="Hier erfahren Sie, wie Sie mithilfe der Azure-Befehlszeilenschnittstelle Data Lake Analytics-Konten, -Datenquellen, -Aufträge und -Benutzer verwalten." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/11/2016"
   ms.author="jgao"/>

# Erste Schritte mit Azure Data Lake Analytics unter Verwendung der Azure-Befehlszeilenschnittstelle (CLI)

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Hier erfahren Sie, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mithilfe von Azure verwalten. Wenn Sie das Verwaltungsthema für andere Tools anzeigen möchten, klicken Sie weiter oben auf die gewünschte Registerkarte.

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Azure-Befehlszeilenschnittstelle**. Weitere Informationen finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).
	- Laden Sie für diese Demo die **Vorabversion** von [Azure Command-line Tools (CLI) for Data Lake](https://github.com/MicrosoftBigData/AzureDataLake/releases) herunter, und installieren Sie sie.
- **Authentifizierung**. Verwenden Sie den folgenden Befehl:

		azure login
	Weitere Informationen zur Authentifizierung mit einem Geschäfts- oder Schulkonto finden Sie unter [Herstellen einer Verbindung mit einem Azure-Abonnement über die Azure-Befehlszeilenschnittstelle](xplat-cli-connect.md).
- **Wechseln Sie in den Azure-Ressourcen-Manager-Modus:**

		azure config mode arm

**So zeigen Sie eine Liste mit den Befehlen für Data Lake-Speicher und Data Lake Analytics an:**

	azure datalake store
	azure datalake analytics

<!-- ################################ -->
<!-- ################################ -->
## Konten verwalten

Zur Ausführung von Data Lake Analytics-Aufträgen ist ein Data Lake Analytics-Konto erforderlich. Im Gegensatz zu Azure HDInsight entstehen durch ein Analytics-Konto keine Kosten, solange darin kein Auftrag ausgeführt wird. Sie bezahlen nur für die Zeit, während der in dem Konto ein Auftrag ausgeführt wird. Weitere Informationen finden Sie unter [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md).

###Erstellen von Konten

  	azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"


###Aktualisieren von Konten

Der folgende Befehl aktualisiert die Eigenschaften eines vorhandenen Data Lake Analytics-Kontos:
  	
	azure datalake analytics account set "<Data Lake Analytics Account Name>"


###Auflisten von Konten

Auflisten der Data Lake Analytics-Konten

	azure datalake analytics account list

Auflisten von Data Lake Analysekonten in einer bestimmten Ressourcengruppe

	azure datalake analytics account list -g "<Azure Resource Group Name>"

Abrufen von Details eines bestimmten Data Lake Analytics-Kontos

	azure datalake analytics account show -g "<Azure Resource Group Name>" -n "<Data Lake Analytics Account Name>"

###Löschen von Data Lake Analytics-Konten

  	azure datalake analytics account delete "<Data Lake Analytics Account Name>"


<!-- ################################ -->
<!-- ################################ -->
## Verwalten von Kontodatenquellen

Data Lake Analytics unterstützt derzeit die folgenden Datenquellen:

- [Azure Data Lake-Speicher](../data-lake-store/data-lake-store-overview.md)
- [Azure Storage (in englischer Sprache)](../storage/storage-introduction.md)

Beim Erstellen eines Analytics-Kontos müssen Sie ein Azure Data Lake-Speicherkonto als Standardspeicherkonto festlegen. Das ADL-Standardspeicherkonto dient zum Speichern von Auftragsmetadaten und -überwachungsprotokollen. Nachdem Sie ein Analytics-Konto erstellt haben, können Sie zusätzliche Data Lake-Speicherkonten und/oder Azure-Speicherkonten hinzufügen.

### Suchen des ADL-Standardspeicherkontos

	azure datalake analytics account show "<Data Lake Analytics Account Name>"

Der Wert wird unter „properties:datalakeStoreAccount:name“ aufgeführt.

### Hinzufügen zusätzlicher Azure Blob-Speicherkonten

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -b "<Azure Blob Storage Account Short Name>" -k "<Azure Storage Account Key>"

>[AZURE.NOTE] Nur Blob-Speicherkurznamen werden unterstützt. Verwenden Sie keine vollqualifizierten Domänennamen wie etwa „myblob.blob.core.windows.net“.

### Hinzufügen zusätzlicher Data Lake-Speicherkonten

  	azure datalake analytics account datasource add -n "<Data Lake Analytics Account Name>" -l "<Data Lake Store Account Name>" [-d]

„[-d]“ ist eine optionale Option, die angibt, ob es sich bei dem hinzugefügten Data Lake-Konto um das Data Lake-Standardkonto handelt.

### Aktualisieren einer vorhandenen Datenquelle

So konfigurieren Sie ein vorhandenes Data Lake-Speicherkonto als Standardkonto:

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -l "<Azure Data Lake Store Account Name>" -d
	  
So aktualisieren Sie den Schlüssel eines Blob-Speicherkontos:

  	azure datalake analytics account datasource set -n "<Data Lake Analytics Account Name>" -b "<Blob Storage Account Name>" -k "<New Blob Storage Account Key>"

### Auflisten von Datenquellen:

	azure datalake analytics account show "<Data Lake Analytics Account Name>"
	
![Data Lake Analytics-Datenquellenliste](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-data-source.png)

### Löschen von Datenquellen:

So löschen Sie ein Data Lake-Speicherkonto:

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Azure Data Lake Store Account Name>"

So löschen Sie ein Blob-Speicherkonto:

  	azure datalake analytics account datasource delete "<Data Lake Analytics Account Name>" "<Blob Storage Account Name>"

## Verwalten von Aufträgen

Für das Erstellen eines Auftrags ist ein Data Lake Analytics-Konto erforderlich. Weitere Informationen finden Sie unter [Verwalten von Data Lake Analytics-Konten](#manage-accounts).

### Auflisten von Aufträgen

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"

![Data Lake Analytics-Datenquellenliste](./media/data-lake-analytics-manage-use-cli/data-lake-analytics-list-jobs.png)

### Abrufen von Auftragsdetails

  	azure datalake analytics job show -n "<Data Lake Analytics Account Name>" -j "<Job ID>"
	
### Übermitteln von Aufträgen

> [AZURE.NOTE] Die Standardpriorität eines Auftrags ist 1000, und der Standardparallelitätsgrad eines Auftrag ist 1.

	azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"

### Abbrechen von Aufträgen

Suchen Sie mithilfe des Befehls „list“ nach der Auftrags-ID, und brechen Sie den Auftrag anschließend mithilfe des Befehls „cancel“ ab.

  	azure datalake analytics job list -n "<Data Lake Analytics Account Name>"
  	azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job ID>"

## Verwalten des Katalogs

Der U-SQL-Katalog wird zum Strukturieren von Daten und Code verwendet, damit diese von U-SQL-Skripts gemeinsam genutzt werden können. Der Katalog ermöglicht die höchstmögliche Leistung mit Daten in Azure Data Lake. Weitere Informationen finden Sie unter [Verwenden des U-SQL-Katalogs](data-lake-analytics-use-u-sql-catalog.md).
 
###Auflisten von Katalogelementen

	#List databases
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t database

	#List tables
	azure datalake analytics catalog list -n "<Data Lake Analytics Account Name>" -t table
	
Verfügbare Typen: „database“, „schema“, „assembly“, „externaldatasource“, „table“, „tablevaluedfunction“ und „tablestatistics“.

###Erstellen von Katalogschlüsseln

	azure datalake analytics catalog secret create -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

### Ändern von Katalogschlüsseln

  	azure datalake analytics catalog secret set -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

###Löschen von Katalogschlüsseln

	azure datalake analytics catalog secrete delete -n "<Data Lake Analytics Account Name>" <databaseName> <hostUri> <secretName>

<!-- ################################ -->
<!-- ################################ -->
## Verwenden von ARM-Gruppen

Anwendungen bestehen normalerweise aus vielen Komponenten, z. B. Web-App, Datenbank, Datenbankserver, Speicher und Drittanbieterdiensten. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-Ressourcengruppe bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md).

Ein Data Lake Analytics-Dienst kann folgende Komponenten enthalten:

- Azure Data Lake Analytics-Konto
- Erforderliches Azure Data Lake-Speicherkonto
- Zusätzliche Azure Data Lake-Speicherkonten
- Zusätzliche Azure-Speicherkonten

Alle diese Komponenten lassen sich zur einfacheren Verwaltung unter einer ARM-Gruppe erstellen.

![Azure Data Lake Analytics-Konto und -Speicher](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Ein Data Lake Analytics-Konto und die dazugehörigen Speicherkonten müssen sich im gleichen Azure-Rechenzentrum befinden. Die ARM-Gruppe kann sich jedoch in einem anderen Rechenzentrum befinden.


##Weitere Informationen 

- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
- [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-manage-use-portal.md)
- [Überwachen und Problembehandeln von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0218_2016-->