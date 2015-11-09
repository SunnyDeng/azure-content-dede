<properties 
	pageTitle="Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe des Azure-Speicher-Explorers | Microsoft Azure" 
	description="Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe des Azure-Speicher-Explorers" 
	services="machine-learning,storage" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/23/2015" 
	ms.author="bradsev" />

# Verschieben von Daten in den und aus dem Azure-Blobspeicher mithilfe des Azure-Speicher-Explorers

Nachstehend finden Sie Links zu Anleitungen für Technologien zum Verschieben von Daten in den und aus dem Azure-BLOB-Speicher:

[AZURE.INCLUDE [blob-storage-tool-selector](../../includes/machine-learning-blob-storage-tool-selector.md)]

## Einführung

Azure Storage Explorer ist ein kostenloses Windows-Tool zum Prüfen und Ändern der Daten in einem Azure-Speicherkonto. Es kann von [Azure Storage-Explorer](http://azurestorageexplorer.codeplex.com/) heruntergeladen werden.

> [AZURE.NOTE]Wenn Sie einen virtuellen Computer verwenden, der mit den von den [virtuellen Data Science-Computern in Azure](machine-learning-data-science-virtual-machines.md) bereitgestellten Skripts eingerichtet wurde, ist der Azure-Speicher-Explorer bereits auf dem virtuellen Computer installiert.

> [AZURE.NOTE]Eine umfassende Einführung in Azure-Blobspeicher finden Sie unter [Grundlagen zu Azure-Blobspeicher](../storage-dotnet-how-to-use-blobs.md) und [Azure-Blobdienst](https://msdn.microsoft.com/library/azure/dd179376.aspx).

## Voraussetzungen

In diesem Dokument wird davon ausgegangen, dass Sie über ein Azure-Abonnement, ein Speicherkonto und den zugehörigen Speicherschlüssel für dieses Konto verfügen. Bevor Sie Daten hoch- und herunterladen können, müssen Sie den Namen Ihres Azure-Speicherkontos und den Kontoschlüssel kennen.

- Informationen zum Einrichten eines Azure-Abonnements finden Sie unter [Kostenlose 1-Monat-Testversion](https://azure.microsoft.com/de-DE/pricing/free-trial/).
- Anleitungen zum Erstellen eines Speicherkontos und zum Abrufen von Konto- und Schlüsselinformationen finden Sie unter [Informationen zu Azure-Speicherkonten](../storage-create-storage-account.md).


<a id="explorer"></a>
## Verwenden von Azure Storage Explorer 

Die folgenden Schritte beschreiben das Hoch- und Herunterladen von Daten mithilfe von Azure Storage Explorer.

1.  Starten von Azure Storage Explorer 
2.  Wenn das Speicherkonto, auf das Sie zugreifen möchten, noch nicht in Azure Storage Explorer hinzugefügt wurde, klicken Sie auf die Schaltfläche "Add Account", um das Konto hinzuzufügen. Wenn es bereits hinzugefügt wurde, wählen Sie das Konto aus der Dropdownliste "--Wählen Sie ein Speicherkonto aus--" aus. ![Arbeitsbereich erstellen][1] <br>
3. Geben Sie den Speicherkontonamen und den Speicherkontoschlüssel ein, und klicken Sie dann auf "Add Storage Account". Sie können mehrere Speicherkonten hinzufügen. Jedes Konto wird auf einer Registerkarte angezeigt. Die Container unter diesem Speicherkonto werden im linken Bereich angezeigt. Wählen Sie einen Container aus, um die Blobs im Container im rechten Bereich anzuzeigen. ![Arbeitsbereich erstellen][2] <br> ![Arbeitsbereich erstellen][3] <br>
4. Sie laden Daten hoch, indem Sie auf die Schaltfläche "Upload" klicken. Wählen Sie eine oder mehrere Dateien aus dem Dateisystem zum Hochladen aus, und klicken Sie auf "Öffnen", um mit dem Hochladen der Dateien zu beginnen.
5. Sie laden Daten herunter, indem Sie das Blob im entsprechenden Container auswählen und dann auf die Schaltfläche "Download" klicken.

<!-- Images -->

[1]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img1.png
[2]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img2.png
[3]: ./media/machine-learning-data-science-move-azure-blob/data-science-process-uploading-data-to-blob-storage-img3.png

<!---HONumber=Nov15_HO1-->