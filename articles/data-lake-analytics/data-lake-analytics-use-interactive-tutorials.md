<properties 
   pageTitle="Erlernen von Data Lake Analytics und U-SQL mithilfe der interaktiven Lernprogramme im Azure-Vorschauportal | Azure" 
   description="Schnellstart beim Erlernen von Data Lake Analytics und U-SQL." 
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
   ms.date="10/28/2015"
   ms.author="jgao"/>


# Verwenden interaktiver Lernprogramme zu Azure Data Lake Analytics

Das Azure-Vorschauportal bietet ein interaktives Lernprogramm für die ersten Schritte mit Data Lake Analytics. In diesem Artikel wird gezeigt, wie Sie das Lernprogramm für die Analyse von Websiteprotokollen durchlaufen.


>[AZURE.NOTE]Falls Sie dasselbe Lernprogramm für Visual Studio durchlaufen möchten, lesen Sie hierzu [Analysieren von Websiteprotokollen mit Data Lake Analytics](data-lake-analytics-analyze-weblogs.md). Weitere interaktive Lernprogramme werden dem Portal demnächst hinzugefügt.


Weitere Lernprogramme finden Sie unter:

- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
- [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des .NET SDK](data-lake-analytics-get-started-net-sdk.md)
- [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md) 

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Data Lake Analytics-Konto**. Weitere Informationen unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md).

##Erstellen eines Data Lake Analytics-Kontos 

Zum Ausführen von Aufträgen ist ein Data Lake Analytics-Konto erforderlich.

Jedes Data Lake Analytics-Konto ist von einem [Azure Data Lake-Speicherkonto](data-lake-store-overview.md) abhängig. Dieses Konto wird als Data Lake-Standardspeicherkonto bezeichnet. Sie können das Data Lake-Speicherkonto bereits im Vorfeld oder während der Erstellung Ihres Data Lake Analytics-Kontos erstellen. In diesem Lernprogramm erstellen Sie das Data Lake-Speicherkonto zusammen mit dem Analysekonto.

**So erstellen Sie ein Data Lake Analytics-Konto**

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com/signin/index/?Microsoft_Azure_Kona=true&Microsoft_Azure_DataLake=true&hubsExtension_ItemHideKey=AzureDataLake_BigStorage%2cAzureKona_BigCompute) an.
2. Klicken Sie links oben auf **Microsoft Azure**, um das Startmenü zu öffnen.
3. Klicken Sie auf die Kachel **Marketplace**.  
3. Geben Sie **Azure Data Lake Analytics** in das Suchfeld auf dem Blatt **Alles** ein, und drücken Sie die **EINGABETASTE**. **Azure Data Lake Analytics** wird in der Liste angezeigt.
4. Klicken Sie in der Liste auf **Azure Data Lake Analytics**.
5. Klicken Sie unten auf dem Blatt auf **Erstellen**.
6. Nehmen Sie folgende Eingaben bzw. Auswahlen vor:

    ![Portalblatt für Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-create-adla.png)

	- **Name**: Geben Sie dem Analysekonto einen Namen.
	- **Data Lake Store**: Jedes Data Lake Analytics-Konto verfügt über ein zugehöriges Data Lake-Speicherkonto. Das Data Lake Analytics-Konto und das zugehörige Data Lake-Speicherkonto müssen sich in demselben Azure-Rechenzentrum befinden. Führen Sie die Anweisungen zum Erstellen eines neuen Data Lake-Speicherkontos aus, oder wählen Sie ein vorhandenes Konto aus.
	- **Abonnement**: Wählen Sie das Azure-Abonnement aus, das für das Analysekonto verwendet wird.
	- **Ressourcengruppe**. Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Anwendungen bestehen normalerweise aus vielen Komponenten, z. B. Web-App, Datenbank, Datenbankserver, Speicher und Drittanbieterdiensten. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-Ressourcengruppe bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md). 
	- **Standort**: Wählen Sie ein Azure-Rechenzentrum für das Data Lake Analytics-Konto aus. 
7. Wählen Sie **An Startmenü anheften** aus. Dies ist für das Verfolgen dieses Lernprogramms erforderlich.
8. Klicken Sie auf **Erstellen**. Sie gelangen zum Startmenü des Portals. Dem Startmenü wird eine neue Kacheln mit der Bezeichnung „Deploying Azure Data Lake Analytics“ hinzugefügt. Es dauert einige Zeit, bis ein Data Lake Analytics-Konto erstellt wurde. Bei seiner Erstellung wird das Konto im Portal auf einem neuen Blatt geöffnet.

	![Portalblatt für Azure Data Lake Analytics](./media/data-lake-analytics-get-started-portal/data-lake-analytics-portal-blade.png)

##Ausführen des interaktiven Lernprogramms zum Analysieren von Websiteprotokollen

**So öffnen Sie das interaktive Lernprogramm zum Analysieren von Websiteprotokollen**

1. Klicken Sie im linken Menü des Vorschauportals auf **Microsoft Azure**, um das Startmenü zu öffnen.
2. Klicken Sie auf die Kachel, die mit Ihrem Data Lake Analytics-Konto verknüpft ist.
3. Klicken Sie auf **Explore interactive tutorials** in der Leiste **Zusammenfassung**.

	![Interaktive Lernprogramme zu Data Lake Analytics](./media/data-lake-analytics-use-interactive-tutorials/data-lake-analytics-explore-interactive-tutorials.png)

4. Falls die orangefarbene Warnung „Samples not set up, click ...“ angezeigt wird, klicken Sie auf **Copy Sample Data**, um die Beispieldaten in das Data Lake-Standardspeicherkonto zu kopieren. Das interaktive Lernprogramm benötigt diese Daten zur Ausführung.
5. Klicken Sie auf dem Blatt **Interactive Tutorials** auf **Website Log Analytics**. Im Portal wird das Lernprogramm auf einem neuen Portalblatt geöffnet.
5. Klicken Sie auf **1 Introduction**, und befolgen Sie dann die Anweisungen.

##Weitere Informationen

- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Vorschauportals](data-lake-analytics-get-started-portal.md)
- [Erste Schritte mit Data Lake Analytics mithilfe von Azure PowerShell](data-lake-analytics-get-started-powershell.md)
- [Entwickeln von U-SQL-Skripts mit Data Lake-Tools für Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
- [Analysieren von Websiteprotokollen mit Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)

<!---HONumber=Nov15_HO1-->