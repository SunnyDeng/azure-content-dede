<properties 
   pageTitle="Verwalten von Azure Data Lake Analytics mithilfe des Azure-Vorschauportals | Azure" 
   description="Erfahren Sie, wie Sie Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge verwalten." 
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
   ms.date="10/27/2015"
   ms.author="jgao"/>

# Verwalten von Azure Data Lake Analytics mithilfe des Azure-Vorschauportals

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Erfahren Sie, wie Sie Azure Data Lake Analytics-Konten, -Datenquellen, -Benutzer und -Aufträge mithilfe des Azure-Vorschauportals verwalten. Klicken Sie oben auf die Registerkartenauswahl, um Themen anzuzeigen, in denen die Verwaltung mit anderen Tools durchgeführt wird.

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/de-DE/pricing/free-trial/).




<!-- ################################ -->
<!-- ################################ -->
## Konten verwalten

Zur Ausführung von Data Lake Analytics-Aufträgen ist ein Data Lake Analytics-Konto erforderlich. Im Gegensatz zu Azure HDInsight entstehen durch ein Analytics-Konto keine Kosten, solange darin kein Auftrag ausgeführt wird. Sie bezahlen nur für die Zeit, während der im Konto ein Auftrag ausgeführt wird. Weitere Informationen finden Sie unter [Azure Data Lake Analytics – Übersicht](data-lake-analytics-overview.md).

**So erstellen Sie Data Lake Analytics-Konten**

1. Melden Sie sich am neuen [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Neu**, auf **Daten + Analysen** und anschließend auf **Data Lake Analytics**.
6. Geben Sie Folgendes ein bzw. wählen Sie es aus:

	![Azure Data Lake Analytics-Portalblatt](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Name**: Geben Sie dem Analytics-Konto einen Namen.
	- **Data Lake-Speicher**: Jedes Data Lake Analytics-Konto verfügt über ein abhängiges Azure Data Lake-Speicherkonto. Das Data Lake Analytics-Konto und das abhängige Data Lake-Speicherkonto müssen sich in demselben Azure-Rechenzentrum befinden. Führen Sie die Anweisungen zum Erstellen eines neuen Data Lake-Speicherkontos aus, oder wählen Sie ein vorhandenes Konto aus.
	- **Abonnement**: Wählen Sie das Azure-Abonnement aus, das für das Analytics-Konto verwendet wird.
	- **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md). 
	- **Standort**: Wählen Sie ein Azure-Rechenzentrum für das Data Lake Analytics-Konto aus. 

8. Klicken Sie auf **Erstellen**. Sie gelangen auf die Startseite des Portals. Dem Startmenü wird eine neue Kachel mit der Bezeichnung „Azure Data Lake Analytics wird bereitgestellt“ hinzugefügt. Es dauert einige Zeit, bis ein Data Lake Analytics-Konto erstellt wurde. Bei seiner Erstellung wird das Konto im Portal auf einem neuen Blatt geöffnet.

	![Azure Data Lake Analytics-Portalblatt](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

<a name="access-adla-account"></a> **So greifen Sie auf ein Data Lake Analytics-Konto zu bzw. öffnen es**

1. Melden Sie sich am neuen [Azure-Portal](https://portal.azure.com/) an.
2. Klicken Sie im linken Menü auf **Durchsuchen** und dann auf **Data Lake Analytics**.
3. Klicken Sie auf das Data Lake Analytics-Konto, auf das Sie zugreifen möchten. Das Konto wird auf einem neuen Blatt geöffnet.

**So löschen Sie ein Data Lake Analytics-Konto**

1. Öffnen Sie das Data Lake Analytics-Konto, das Sie löschen möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account).
2. Klicken Sie im Menü oben auf dem Blatt auf **Löschen**.
3. Geben Sie den Kontonamen ein, und klicken Sie dann auf **Löschen**.

Durch das Löschen eines Analytics-Kontos wird das zugehörige Data Lake-Speicherkonto nicht gelöscht. Eine Anleitung zum Löschen von Data Lake-Speicherkonten finden Sie unter [Löschen von Data Lake-Speicherkonten](data-lake-store-get-started-portal.md#delete-azure-data-lake-store-account).




























<!-- ################################ -->
<!-- ################################ -->
## Verwalten von Kontodatenquellen

Data Lake Analytics unterstützt derzeit die folgenden Datenquellen:

- [Azure Data Lake-Speicher](data-lake-store-overview.md)
- [Azure-BLOB-Speicher](storage-introduction.md)

Beim Erstellen eines Data Lake Analytics-Kontos müssen Sie ein Azure Data Lake-Speicherkonto als Standardspeicherkonto festlegen. Das Data Lake-Standardspeicherkonto dient zum Speichern von Auftragsmetadaten und -überwachungsprotokollen. Nachdem Sie ein Data Lake Analytics-Konto erstellt haben, können Sie zusätzliche Data Lake-Speicherkonten bzw. Azure-Speicherkonten hinzufügen.

<a name="default-adl-account"></a>**So suchen Sie nach dem Data Lake-Standardspeicherkonto**

- Öffnen Sie das Data Lake Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account). Der Data Lake-Standardspeicher wird unter **Zusammenfassung** angezeigt:

	![Azure Data Lake Analytics, Datenquelle hinzufügen](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-default-adl-storage-account.png)

**So fügen Sie zusätzliche Datenquellen hinzu**

1. Öffnen Sie das Data Lake Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account).
2. Klicken Sie auf **Einstellungen** und dann auf **Datenquellen**. Hier wird das Data Lake-Standardspeicherkonto aufgeführt. 
3. Klicken Sie auf **Datenquelle hinzufügen**.

	![Azure Data Lake Analytics, Datenquelle hinzufügen](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-add-data-source.png)

	Zum Hinzufügen eines Azure Data Lake-Speicherkontos benötigen Sie den Kontonamen und Zugriff auf das Konto, um eine Abfrage durchzuführen. Zum Hinzufügen eines Azure-BLOB-Speichers benötigen Sie das Speicherkonto und den Kontoschlüssel. Sie finden den Schlüssel, indem Sie im Portal zum Speicherkonto navigieren.

<a name="explore-data-sources"></a>**So erkunden Sie die Datenquellen**

1. Öffnen Sie das Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account).
2. Klicken Sie auf **Einstellungen** und dann auf **Daten-Explorer**. 
 
	![Azure Data Lake Analytics, Daten-Explorer](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-data-explorer.png)
	
3. Klicken Sie auf ein Data Lake-Speicherkonto, um es zu öffnen.

	![Azure Data Lake Analytics, Daten-Explorer-Speicherkonto](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-adls.png)
	
	Sie können für jedes Data Lake-Speicherkonto Folgendes durchführen:
	
	- **Neuer Ordner**: Dient zum Hinzufügen eines neuen Ordners.
	- **Hochladen**: Dient zum Hochladen von Dateien in das Speicherkonto von Ihrer Arbeitsstation.
	- **Zugriff**: Dient zum Konfigurieren von Zugriffsberechtigungen.
	- **Ordner umbenennen**: Dient zum Umbenennen eines Ordners.
	- **Ordnereigenschaften**: Dient zum Anzeigen von Datei- oder Ordnereigenschaften, z. B. WASB-Pfad, WEBHDFS-Pfad, Zeitpunkt der letzten Änderung usw.
	- **Ordner löschen**: Dient zum Löschen eines Ordners.

<a name="upload-data-to-adls"></a> **So laden Sie Dateien in das Data Lake-Speicherkonto hoch**

1. Klicken Sie im Vorschauportal im Menü auf der linken Seite auf **Durchsuchen** und dann auf **Data Lake-Speicher**.
2. Klicken Sie auf das Data Lake-Speicherkonto, in das Sie Daten hochladen möchten. Informationen zum Suchen nach dem Data Lake-Standardspeicherkonto finden Sie [hier](#default-adl-account).
3. Klicken Sie im Menü oben auf **Daten-Explorer**.
4. Klicken Sie auf **Neues Verzeichnis**, um einen neuen Ordner zu erstellen, oder klicken Sie auf einen Ordnernamen, um den Ordner zu ändern.
6. Klicken Sie im Menü oben auf **Hochladen**, um die Datei hochzuladen.


<a name="upload-data-to-wasb"></a> **So laden Sie die Dateien in das Azure-BLOB-Speicherkonto hoch**

Weitere Informationen finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md). Die Informationen gelten für Data Lake Analytics.


## Verwalten von Benutzern

Für Data Lake Analytics wird die rollenbasierte Zugriffssteuerung mit Azure Active Directory verwendet. Wenn Sie ein Data Lake Analytics-Konto erstellen, wird dem Konto die Rolle „Abonnement-Administratoren“ hinzugefügt. Sie können weitere Benutzer und Sicherheitsgruppen mit den folgenden Rollen hinzufügen:

|Rolle|Beschreibung|
|----|-----------|
|Besitzer|Sie können alles verwalten, einschließlich des Zugriffs auf Ressourcen.|
|Mitwirkender|Sie können auf das Portal zugreifen und Aufträge senden und überwachen. Zum Senden von Aufträgen benötigt ein Mitwirkender auch die Lese- oder Schreibberechtigung für die Data Lake-Speicherkonten.|
|DataLakeAnalyticsDeveloper | Benutzer können Aufträge senden und alle Aufträge überwachen, aber sie können nur eigene Aufträge abbrechen. Sie können beispielsweise nicht ihr eigenes Konto verwalten, keine Benutzer hinzufügen, keine Berechtigungen ändern und das Konto nicht löschen. Um Aufträge ausführen zu können, benötigen sie Lese- oder Schreibzugriff auf die Data Lake-Speicherkonten. | 
|Leser|Sie können alles anzeigen, aber keine Änderungen vornehmen.|  
|DevTest Lab-Benutzer|Sie können alles anzeigen und virtuelle Computer verbinden, starten, neu starten und herunterfahren.|  
|Benutzerzugriffsadministrator|Ermöglicht Ihnen die Verwaltung von Benutzerzugriffen auf Azure-Ressourcen.|  

Informationen zum Erstellen von Azure Active Directory-Benutzern und -Sicherheitsgruppen finden Sie unter [Was ist Azure Active Directory?](active-directory-whatis.md)

**So fügen Sie einem Analytics-Konto Benutzer oder Sicherheitsgruppen hinzu**

1. Öffnen Sie das Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account).
2. Klicken Sie auf **Einstellungen** und dann auf **Benutzer**. Sie können auch auf der Titelleiste **Zusammenfassung** auf **Zugriff** klicken. Dies ist im folgenden Screenshot dargestellt:

	![Azure Data Lake Analytics-Konto, Benutzer hinzufügen](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-access-button.png)
3. Klicken Sie auf dem Blatt **Benutzer** auf **Hinzufügen**.
4. Wählen Sie eine Rolle aus, und fügen Sie einen Benutzer hinzu. Klicken Sie anschließend auf **OK**.

**Hinweis: Wenn der Benutzer oder die Sicherheitsgruppe Aufträge senden muss, müssen Sie auch die Berechtigung für den Data Lake-Speicher erteilen. Weitere Informationen finden Sie unter [Schützen von Daten im Data Lake-Speicher](data-lake-store-secure-data.md).**





<!-- ################################ -->
<!-- ################################ -->
## Verwalten von Aufträgen

Zum Ausführen von Aufträgen ist ein Data Lake Analytics-Konto erforderlich. Weitere Informationen finden Sie unter [Verwalten von Data Lake Analytics-Konten](#manage-data-lake-analytics-accounts).

<a name="create-job"></a>**So erstellen Sie einen Auftrag**

1. Öffnen Sie das Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account).
2. Klicken Sie auf **Neuer Auftrag**.

	![Azure Data Lake Analytics-U-SQL-Aufträge erstellen](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-create-job-button.png)

	Sie sehen ein neues Blatt, das etwa wie folgt aussieht:

	![Azure Data Lake Analytics-U-SQL-Aufträge erstellen](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-new-job.png)

	Sie können für jeden Auftrag Folgendes konfigurieren:


	|Name|Beschreibung|
	|----|-----------|
	|Auftragsname|Geben Sie den Namen des Auftrags ein.|
	|Priorität|Je niedriger die Zahl ist, desto höher ist die Priorität. Wenn sich zwei Aufträge in der Warteschlange befinden, wird zuerst der Auftrag mit dem niedrigeren Prioritätswert ausgeführt.|
	|Parallelität |Gibt die maximale Anzahl von Compute-Prozessen an, die gleichzeitig möglich sind. Wenn Sie diesen Wert erhöhen, können Sie die Leistung steigern, aber auch die Kosten können sich erhöhen.|
	|Skript|Geben Sie das U-SQL-Skript für den Auftrag ein.|

	Über dieselbe Oberfläche können Sie auch die verknüpften Datenquellen erkunden und diesen weitere Dateien hinzufügen. 
3. Klicken Sie auf **Auftrag senden**, wenn Sie den Auftrag übermitteln möchten.

**So senden Sie einen Auftrag**

Weitere Informationen finden Sie unter [Erstellen von Data Lake Analytics-Aufträgen](#create-job).

<a name="monitor-jobs"></a>**So überwachen Sie Aufträge**

1. Öffnen Sie das Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account). Im Bereich für die Auftragsverwaltung werden die grundlegenden Auftragsinformationen angezeigt:

	![Azure Data Lake Analytics-U-SQL-Aufträge verwalten](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs.png)

3. Klicken Sie auf **Auftragsverwaltung** (wie im vorherigen Screenshot gezeigt).

	![Azure Data Lake Analytics-U-SQL-Aufträge verwalten](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-manage-jobs-details.png)

4. Klicken Sie auf einen Auftrag aus den Listen. Oder klicken Sie auf **Filter**, um die Suche nach den Aufträgen zu vereinfachen:

	![Azure Data Lake Analytics-U-SQL-Aufträge filtern](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-filter-jobs.png)

	Sie können die Aufträge nach **Zeitbereich**, **Auftragsname** und **Autor** filtern.
5. Klicken Sie auf **Erneut übermitteln**, wenn Sie den Auftrag erneut übermitteln möchten.

**So senden Sie einen Auftrag erneut**

Weitere Informationen finden Sie unter [Überwachen von Data Lake Analytics-Aufträgen](#monitor-jobs).

##Überwachen der Kontoverwendung

[Einführung: Wir müssen die Begriffe erklären und die einzelnen Teile verbinden.]

**So überwachen Sie die Kontoverwendung**

1. Öffnen Sie das Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account). Der Bereich „Nutzung“ enthält Informationen zur Nutzung:

	![Azure Data Lake Analytics-Nutzung überwachen](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-monitor-usage.png)

2. Doppelklicken Sie auf den Bereich, um weitere Details anzuzeigen.

##Anzeigen des U-SQL-Katalogs

Der [U-SQL-Katalog](data-lake-analytics-use-u-sql-catalog.md) wird zum Strukturieren von Daten und Code verwendet, damit diese von U-SQL-Skripts gemeinsam genutzt werden können. Der Katalog ermöglicht die höchstmögliche Leistung mit Daten in Azure Data Lake. Im Azure-Vorschauportal können Sie den U-SQL-Katalog anzeigen.

**So durchsuchen Sie den U-SQL-Katalog**

1. Öffnen Sie das Analytics-Konto, das Sie verwalten möchten. Weitere Informationen finden Sie unter [Zugreifen auf Data Lake Analytics-Konten](#access-adla-account).
2. Klicken Sie im Menü oben auf **Daten-Explorer**.
3. Erweitern Sie **Katalog**, **master** und dann **Tabellen** oder **Tabellenwertfunktionen** bzw. **Assemblys**. Im folgenden Screenshot ist eine Tabellenwertfunktion dargestellt.

	![Azure Data Lake Analytics, Daten-Explorer-Speicherkonto](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-explore-catalog.png)


<!-- ################################ -->
<!-- ################################ -->
## Verwenden von Azure-Ressourcen-Manager-Gruppen

Anwendungen bestehen normalerweise aus vielen Komponenten, z. B. Web-App, Datenbank, Datenbankserver, Speicher und Drittanbieterdiensten. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-Ressourcengruppe bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).

Ein Data Lake Analytics-Dienst kann folgende Komponenten enthalten:

- Azure Data Lake Analytics-Konto
- Erforderliches Azure Data Lake-Standardspeicherkonto
- Zusätzliche Azure Data Lake-Speicherkonten
- Zusätzliche Azure-Speicherkonten

Alle diese Komponenten lassen sich zur leichteren Verwaltung unter einer ARM-Gruppe erstellen.

![Azure Data Lake Analytics-Konto und -Speicher](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Ein Data Lake Analytics-Konto und das zugehörige Speicherkonto müssen sich in demselben Azure-Rechenzentrum befinden. Die ARM-Gruppe kann sich jedoch in einem anderen Rechenzentrum befinden.



##Weitere Informationen 

- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
- [Verwalten von Azure Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-use-powershell.md)
- [Überwachung und Problembehandlung von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Vorschauportals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=Nov15_HO1-->