<properties 
	pageTitle="Verwalten von Azure Media Services-Konten mit PowerShell" 
	description="Erfahren Sie, wie Sie Azure Media Services-Konten mit PowerShell-Cmdlets verwalten." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/16/2015" 
	ms.author="juliako"/>


#Verwalten von Azure Media Services-Konten mit PowerShell

> [AZURE.SELECTOR]
- [Portal](media-services-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [REST](https://msdn.microsoft.com/library/azure/dn167014.aspx)

##Übersicht 

Dieser Artikel veranschaulicht, wie Sie mithilfe von PowerShell-Cmdlets Azure Media Services-Konten verwalten.

>[AZURE.NOTE]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Kostenlose Azure-Testversion</a>.

##Installieren von Microsoft Azure PowerShell-Cmdlets

Informationen zum Installieren der neuesten Azure PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

##Auswählen eines Azure-Abonnements

Wenn Sie PowerShell-Cmdlets installieren und konfigurieren, sollten Sie angeben, welches Abonnement Sie verwenden möchten.

Führen Sie das folgende Cmdlet aus, um eine Liste der verfügbaren Abonnements aufzurufen:

	PS C:\> Get-AzureSubscription

Wählen Sie dann wie folgt ein Abonnement aus:

	PS C:\> Select-AzureSubscription "TestSubscription"

 
##Abrufen des Speicherkontonamens

Azure Media Services verwendet den Azure-Speicher zum Speichern von Medieninhalten. Wenn Sie ein neues Media Services-Konto erstellen, müssen Sie es einem Speicherkonto zuordnen. Das Speicherkonto muss zu dem Abonnement gehören, das Sie auch für Ihr Media Services-Konto verwenden möchten.

In diesem Beispiel wird ein vorhandenes Speicherkonto verwendet. Das Cmdlet [Get-AzureStorageAccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) ruft Speicherkonten im aktuellen Abonnement ab. Rufen Sie den Namen (StorageAccountName) des Speicherkontos ab, dem Sie Ihr Media Services-Konto zuordnen möchten.

	StorageAccountDescription : 
	AffinityGroup             :
	Location                  : East US
	GeoReplicationEnabled     : True
	GeoPrimaryLocation        : East US
	GeoSecondaryLocation      : West US
	Label                     : storagetest001
	StorageAccountStatus      : Created
	StatusOfPrimary           : Available
	StatusOfSecondary         : Available
	Endpoints                 : {https://storagetest001.blob.core.windows.net/,
	                            https://storagetest001.queue.core.windows.net/,
	                            https://storagetest001.table.core.windows.net/}
	AccountType               : Standard_GRS
	StorageAccountName        : storatetest001
	OperationDescription      : Get-AzureStorageAccount
	OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
	OperationStatus           : Succeeded

##Erstellen eines neuen Media Services-Kontos

Verwenden Sie zum Erstellen eines neuen Azure Media Services-Kontos das Cmdlet [New-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx), und geben Sie den Namen des Media Services-Kontos, den Speicherort im Rechenzentrum, in dem es erstellt wird, und den Namen des Speicherkontos an.


	PS C:\> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

##Abrufen von Media Services-Konten

Nachdem Sie Media Services-Konten erstellt haben, können Sie mithilfe von [Get-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx) Informationen abrufen.

	
	PS C:\> Get-AzureMediaServicesAccount
	
	AccountId		Name				State
	---------       ----       			 -----
	xxxxxxxxxx      amstestaccount001   Active

Durch Angabe des "Name"-Parameters erhalten Sie ausführlichere Informationen, darunter die Kontoschlüssel.

	PS C:\> Get-AzureMediaServicesAccount -Name amstestaccount001

##Neugenerieren der Media Services-Zugriffsschlüssel

Wenn Sie primäre oder sekundäre Media Services-Zugriffsschlüssel aktualisieren möchten, können Sie [New-AzureMediaServicesKey](https://msdn.microsoft.com/library/azure/dn495215.aspx) verwenden. Sie müssen den Kontonamen angeben und festlegen, welcher Schlüssel neu generiert werden soll (primärer oder sekundärer Schlüssel).

Geben Sie einen "-Force"-Schalter an, wenn in der PowerShell keine Aufforderungen zur Bestätigung angezeigt werden sollen.

	PS C:\> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

##Entfernen von Media Services-Kontos

Wenn Sie das Azure Media Services-Konto löschen möchten, verwenden Sie [Remove-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495220.aspx).

	PS C:\> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force

 

<!---HONumber=July15_HO4-->