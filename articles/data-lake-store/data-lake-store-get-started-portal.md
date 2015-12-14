<properties 
   pageTitle="Erste Schritte mit Data Lake-Speicher | Azure" 
   description="Verwenden des Portals zum Erstellen eines Data Lake-Speicherkontos und Ausführen grundlegender Vorgänge im Data Lake-Speicher" 
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
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# Erste Schritte mit Azure Data Lake-Speicher mithilfe des Azure-Portals

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)

Hier erfahren Sie, wie Sie im Azure-Portal ein Azure Data Lake-Speicherkonto erstellen und grundlegende Vorgänge ausführen, z. B. Ordner erstellen, Datendateien hoch- und herunterladen, Ihr Konto löschen usw. Weitere Informationen zu Data Lake-Speicher finden Sie unter [Übersicht über Azure Data Lake-Speicher](data-lake-store-overview.md).

## Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).

## <a name="signup"></a>Aktivieren Ihres Azure-Abonnements für die öffentliche Vorschau von Data Lake-Speicher

Zunächst müssen Sie die Aktivierung Ihres Azure-Abonnements für die öffentliche Vorschau von Data Lake-Speicher anfordern. Führen Sie die folgenden Schritte aus.

1. Melden Sie sich beim neuen [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **NEU**, auf **Daten und Speicher** und anschließend auf **Azure Data Lake-Speicher**.
3. Klicken Sie auf dem Blatt **Neuer Data Lake-Speicher** auf **Für Vorschau anmelden**. Prüfen Sie die Informationen, und klicken Sie auf **OK**. Sie erhalten eine E-Mail, sobald Ihr Abonnement für die öffentliche Vorschau aktiviert wurde.

	![Anmeldung für die öffentliche Vorschau](./media/data-lake-store-get-started-portal/preview-signup.png "Erstellen eines neuen Azure Data Lake-Kontos")

## Erstellen eines Azure Data Lake-Speicherkontos

1. Melden Sie sich beim neuen [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **NEU**, auf **Daten und Speicher** und anschließend auf **Azure Data Lake-Speicher**. Prüfen Sie die Informationen auf dem Blatt **Azure Data Lake-Speicher**, und klicken Sie dann in der unteren linken Ecke des Blatts auf **Erstellen**.

3. Geben Sie auf dem Blatt **Neuer Data Lake-Speicher** die Werte wie im folgenden Screenshot gezeigt an:

	![Erstellen eines neuen Azure Data Lake-Speicherkontos](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Erstellen eines neuen Azure Data Lake-Kontos")

	- **Abonnement**. Wählen Sie das Abonnement aus, unter dem Sie ein neues Data Lake-Speicherkonto erstellen möchten.
	- **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder klicken Sie auf **Neue Ressourcengruppe erstellen**, um eine neue Ressourcengruppe zu erstellen. Eine Ressourcengruppe ist ein Container, der verwandte Ressourcen für eine Anwendung enthält. Weitere Informationen finden Sie unter [Ressourcengruppen in Azure](resource-group-overview.md#resource-groups).
	- **Ort**: Wählen Sie den Ort aus, an dem Sie das Data Lake-Speicherkonto erstellen möchten.

4. Wählen Sie **An Startmenü anheften** aus, wenn das Data Lake-Speicherkonto über das Startmenü zugänglich sein soll.

5. Klicken Sie auf **Erstellen**. Wenn Sie die Option zum Anheften des Kontos an das Startmenü ausgewählt haben, wird wieder das Startmenü angezeigt, und Sie können den Fortschritt der Bereitstellung Ihres Data Lake-Speicherkontos überprüfen. Nachdem das Data Lake-Speicherkonto bereitgestellt wurde, wird das Kontoblatt angezeigt.

6. Erweitern Sie die Dropdownliste **Zusammenfassung**, um die Informationen zu Ihrem Data Lake-Speicherkonto anzuzeigen, z. B. die zugehörige Ressourcengruppe, den Ort usw. Klicken Sie auf das Symbol **Schnellstart**, um Links zu anderen Ressourcen im Zusammenhang mit Data Lake-Speicher anzuzeigen.

	![Ihr Azure Data Lake-Speicherkonto](./media/data-lake-store-get-started-portal/ADL.Account.QuickStart.png "Ihr Azure Data Lake-Konto")

## <a name="createfolder"></a>Erstellen von Ordnern im Azure Data Lake-Speicherkonto

Sie können in Ihrem Data Lake-Speicherkonto Ordner zum Verwalten und Speichern von Daten erstellen.

1. Öffnen Sie das eben erstellte Data Lake-Speicherkonto. Klicken Sie im linken Bereich auf **Durchsuchen** und dann auf **Data Lake-Speicher**. Klicken Sie anschließend auf dem Blatt „Data Lake-Speicher“ auf den Namen des Kontos, in dem Sie Ordner erstellen möchten. Wenn Sie das Konto an das Startmenü angeheftet haben, klicken Sie auf die Kontokachel.

2. Klicken Sie auf dem Blatt Ihres Data Lake-Speicherkontos auf **Daten-Explorer**.

	![Erstellen von Ordnern im Data Lake-Speicherkonto](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Erstellen von Ordnern im Data Lake-Speicherkonto")

3. Klicken Sie auf dem Blatt Ihres Data Lake-Speicherkontos auf **Neuer Ordner**, geben Sie einen Namen für den neuen Ordner ein, und klicken Sie dann auf **OK**.
	
	![Erstellen von Ordnern im Data Lake-Speicherkonto](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Erstellen von Ordnern im Data Lake-Speicherkonto")
	
	Der neu erstellte Ordner wird auf dem Blatt **Daten-Explorer** aufgelistet. Sie können geschachtelte Ordner mit beliebig vielen Schachtelungsebenen erstellen.

	![Erstellen von Ordnern im Data Lake-Konto](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Erstellen von Ordnern im Data Lake-Konto")


## <a name="uploaddata"></a>Hochladen von Daten in das Azure Data Lake-Speicherkonto

Sie können Ihre Daten direkt auf die Stammebene eines Azure Data Lake-Speicherkontos oder in einen im Konto erstellten Ordner hochladen. Gehen Sie wie im folgenden Screenshot gezeigt vor, um auf dem Blatt **Daten-Explorer** eine Datei in einen Unterordner hochzuladen. In diesem Screenshot wird die Datei in den auf der Breadcrumb-Leiste (rot markiert) angezeigten Unterordner hochgeladen.

Wenn Sie Beispieldaten zum Hochladen verwenden möchten, können Sie den Ordner **Ambulance Data** aus dem [Azure Data Lake-Git-Repository](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData) herunterladen.

![Hochladen von Daten](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Hochladen von Daten")


## <a name="properties"></a>Verfügbare Eigenschaften und Aktionen für die gespeicherten Daten

Klicken Sie auf die neu hinzugefügte Datei, um das Blatt **Eigenschaften** zu öffnen. Auf diesem Blatt werden die Eigenschaften der Datei und die verfügbaren Aktionen für die Datei angezeigt. Sie können auch den vollständigen Pfad der Datei in Ihrem Azure Data Lake-Speicherkonto kopieren (rot markiert im folgenden Screenshot).

![Eigenschaften der Daten](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Eigenschaften der Daten")

* Klicken Sie auf **Vorschau**, um direkt im Browser eine Vorschau der Datei anzuzeigen. Sie können auch das Format der Vorschau angeben. Klicken Sie auf **Vorschau**, klicken Sie auf dem Blatt **Dateivorschau** auf **Format**, und geben Sie dann auf dem Blatt **Format der Dateivorschau** Optionen wie die Anzahl anzuzeigender Zeilen, die zu verwendende Codierung, das Trennzeichen usw. an.

  ![Format der Dateivorschau](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Format der Dateivorschau")

* Klicken Sie auf **Herunterladen**, um die Datei auf Ihren Computer herunterzuladen.

* Klicken Sie auf **Datei umbenennen**, um die Datei umzubenennen.

* Klicken Sie auf **Datei löschen**, um die Datei zu löschen.


## Sichern der Daten

Sie können die in Ihrem Azure Data Lake-Speicherkonto gespeicherten Daten mithilfe von Azure Active Directory und Access Control (Zugriffssteuerungslisten, ACLs) sichern. Anweisungen dazu finden Sie unter [Sichern von Daten in Azure Data Lake-Speicher](data-lake-store-secure-data.md).


## Löschen des Azure Data Lake-Speicherkontos

Um ein Azure Data Lake-Speicherkonto zu löschen, klicken Sie auf dem Blatt „Data Lake-Speicher“ auf **Löschen**. Sie werden aufgefordert, den Namen des zu löschenden Kontos einzugeben, um die Aktion zu bestätigen. Geben Sie den Namen des Kontos ein, und klicken Sie dann auf **Löschen**.

![Löschen eines Data Lake-Kontos](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Löschen eines Data Lake-Kontos")

## Weitere Methoden zum Erstellen eines Data Lake-Speicherkontos

- [Erste Schritte mit Data Lake-Speicher mithilfe von PowerShell](data-lake-store-get-started-powershell.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe des .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Erste Schritte mit Data Lake-Speicher mithilfe von Azure CLI](data-lake-store-get-started-cli.md)


## Nächste Schritte

- [Sichern von Daten in Data Lake-Speicher](data-lake-store-secure-data.md)
- [Verwenden von Azure Data Lake Analytics mit Data Lake-Speicher](data-lake-analytics-get-started-portal.md)
- [Verwenden von Azure HDInsight mit Data Lake-Speicher](data-lake-store-hdinsight-hadoop-use-portal.md)

<!---HONumber=AcomDC_1203_2015-->