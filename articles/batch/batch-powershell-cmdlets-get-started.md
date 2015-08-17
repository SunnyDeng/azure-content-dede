<properties
   pageTitle="Erste Schritte mit Azure PowerShell-Batch-Cmdlets"
   description="Einführung in die Azure PowerShell-Cmdlets zum Verwalten des Azure Batch-Diensts"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>


<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="07/08/2015"
   ms.author="danlep"/>


# Erste Schritte mit Azure Batch für PowerShell-Cmdlets
Dieser Artikel dient als kurze Einführung in die Azure PowerShell-Cmdlets, mit denen Sie Ihre Batch-Konten verwalten und Informationen über Ihre Batch-Aufträge und -Aufgaben sowie weitere Details abrufen können.

Die ausführliche Cmdlet-Syntax erhalten Sie durch Eingabe von `get-help <Cmdlet_name>` oder in der [Referenz zu Azure Batch-Cmdlets](https://msdn.microsoft.com/library/azure/mt125957.aspx). a

## Voraussetzungen

* **Azure PowerShell**: Anweisungen zu erforderlichen Komponenten, zum Download und zur Installation finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Batch-Cmdlets wurden ab Version 0.8.10 eingeführt.

## Verwenden der Batch-Cmdlets

Starten Sie Azure PowerShell entsprechend dem Standardverfahren, und [stellen Sie eine Verbindung mit Ihren Azure-Abonnements her](../powershell-install-configure.md#Connect). Weitere Vorgänge:

* **Auswählen eines Azure-Abonnements** – Falls Sie mehrere Abonnements besitzen, wählen Sie ein Abonnement aus:

    ```
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

* **Wechseln in den AzureResourceManage-Modus**: Die Batch-Cmdlets befinden sich im Modul Azure-Ressourcen-Manager. Ausführliche Informationen siehe [Verwenden von Windows PowerShell mit dem Ressourcen-Manager](../powershell-azure-resource-manager.md). Führen Sie zur Verwendung dieses Moduls das Cmdlet [Switch-AzureMode](https://msdn.microsoft.com/library/dn722470.aspx) aus:

    ```
    Switch-AzureMode -Name AzureResourceManager
    ```

## Verwalten von Batch-Konten und Schlüsseln

Mit Azure PowerShell-Cmdlets können Sie Batch-Konten und Schlüssel erstellen und verwalten.

### Erstellen eines Batch-Kontos

**New-AzureBatchAccount** erstellt ein neues Batch-Konto in einer angegebenen Ressourcengruppe. Wenn Sie noch keine Ressourcengruppe erstellt haben, erstellen Sie sie durch Ausführen des Cmdlets [New-AzureResourceGroup](https://msdn.microsoft.com/library/dn654594.aspx). Geben Sie dabei eine der Azure-Regionen im **Location**-Parameter an. (Verfügbare Regionen für verschiedene Azure-Ressourcen finden Sie durch Ausführen von [Get-AzureLocation](https://msdn.microsoft.com/library/dn654582.aspx).) Beispiel:

```
New-AzureResourceGroup –Name MyBatchResourceGroup –location "Central US"
```

Erstellen Sie dann ein neues Batch-Konto in der Ressourcengruppe. Geben Sie zudem einen Kontonamen für <*account\_name*> und einen Speicherort an, an dem der Batch-Dienst verfügbar ist. Die Erstellung des Kontos kann mehrere Minuten in Anspruch nehmen. Beispiel:

```
New-AzureBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup
```

> [AZURE.NOTE]Der Batch-Kontoname muss in Azure eindeutig sein, zwischen 3 und 24 Zeichen umfassen und kann nur Kleinbuchstaben und Zahlen enthalten.

### Abrufen von Kontozugriffsschlüsseln
**Get-AzureBatchAccountKeys** zeigt die Zugriffsschlüssel an, die einem Azure Batch-Konto zugeordnet sind. Führen Sie beispielsweise Folgendes aus, um den primären und sekundären Schlüssel des Kontos abzurufen, das Sie erstellt haben.

```
$Account = Get-AzureBatchAccountKeys –AccountName <accountname>
$Account.PrimaryAccountKey
$Account.SecondaryAccountKey
```

### Generieren eines neuen Zugriffsschlüssels
**New-AzureBatchAccountKey** generiert einen neuen primären oder sekundären Kontoschlüssel für ein Azure Batch-Konto. Geben Sie zum Generieren eines neuen primären Schlüssels für Ihr Batch-Konto z. B. Folgendes ein:

```
New-AzureBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [AZURE.NOTE]Um einen neuen sekundären Schlüssel zu generieren, geben Sie "Secondary" für den **KeyType**-Parameter ein. Sie müssen den primären und sekundären Schlüssel separat neu generieren.

### Löschen eines Batch-Kontos
**Remove-AzureBatchAccount** löscht ein Batch-Konto. Beispiel:

```
Remove-AzureBatchAccount -AccountName <account_name>
```

Bestätigen Sie bei der entsprechenden Aufforderung, dass Sie das Konto entfernen möchten. Das Entfernen des Kontos kann einige Zeit in Anspruch nehmen.

## Abfragen von Aufträgen, Aufgaben und weiteren Details

Verwenden Sie Cmdlets wie **Get-AzureBatchJob**, **Get-AzureBatchTask** und **Get-AzureBatchPool** zum Abfragen von Entitäten, die in einem Batch-Konto erstellt wurden.

Um diese Cmdlets verwenden zu können, müssen Sie zunächst ein AzureBatchContext-Objekt erstellen, um Ihren Kontonamen und Ihre Schlüssel zu speichern:

```
$context = Get-AzureBatchAccountKeys "<account_name>"
```

Sie übergeben diesen Kontext in Cmdlets, die über den **BatchContext**-Parameter mit dem Batch-Dienst interagieren.

> [AZURE.NOTE]Standardmäßig wird der primäre Schlüssel des Kontos für die Authentifizierung verwendet. Sie können jedoch ausdrücklich den zu verwendenden Schlüssel auswählen, indem Sie die **KeyInUse**-Eigenschaft des BatchAccountContext-Objekts ändern: `$context.KeyInUse = "Secondary"`.


### Abfragen von Daten

Verwenden Sie beispielsweise **Get-AzureBatchPools** zum Suchen Ihrer Pools. Damit werden standardmäßig alle Pools unter Ihrem Konto abgefragt, sofern Sie das BatchAccountContext-Objekt bereits in *$context* gespeichert haben:

```
Get-AzureBatchPool -BatchContext $context
```
### Verwenden eines OData-Filters

Mit dem **Filter**-Parameter können Sie einen OData-Filter angeben, um nur bestimmte gewünschte Objekte zu suchen. Sie können beispielsweise alle Pools suchen, deren Namen mit „myPool“ beginnt:

```
$filter = "startswith(name,'myPool')"
Get-AzureBatchPool -Filter $filter -BatchContext $context
```

Diese Methode ist nicht so flexibel wie die Verwendung von "Where-Object" in einer lokalen Pipeline. Die Abfrage wird jedoch direkt an den Batch-Dienst gesendet, sodass die gesamte Filterung auf dem Server erfolgt, was Internetbandbreite einspart.

### Verwenden des Name-Parameters

Eine Alternative zu einem OData-Filter ist die Verwendung des **Name**-Parameters. So führen Sie eine Abfrage für einen bestimmten Pool mit dem Namen „myPool“ aus:

```
Get-AzureBatchPool -Name "myPool" -BatchContext $context

```
Der **Name**-Parameter unterstützt nur die Suche nach dem vollständigen Namen, jedoch keine Platzhalter oder Filter im OData-Format.

### Verwenden der Pipeline

Batch-Cmdlets können die PowerShell-Pipeline zum Senden von Daten zwischen Cmdlets nutzen. Dies hat dieselbe Auswirkung wie die Angabe eines Parameters, vereinfacht jedoch das Auflisten mehrerer Entitäten. Sie können beispielsweise alle Aufgaben unter Ihrem Konto suchen:

```
Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context
```

### Verwenden des MaxCount-Parameters

Jedes Cmdlet gibt standardmäßig bis zu 1.000 Objekte zurück. Wenn dieser Grenzwert erreicht ist, können Sie entweder den Filter weiter eingrenzen, sodass weniger Objekte zurückgegeben werden, oder mit dem **MaxCount**-Parameter explizit einen maximalen Wert festlegen. Beispiel:

```
Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

```

Setzen Sie den **MaxCount**-Parameter auf 0 oder eine negative Zahl, um die Obergrenze zu entfernen.

## Verwandte Themen
* [Batch – Technische Übersicht](batch-technical-overview.md)
* [Azure PowerShell herunterladen](http://go.microsoft.com/p/?linkid=9811175)
* [Referenz zu Azure-Batch-Cmdlets](https://msdn.microsoft.com/library/azure/mt125957.aspx)
* [Effiziente Listenabfragen](batch-efficient-list-queries.md)

<!---HONumber=August15_HO6-->