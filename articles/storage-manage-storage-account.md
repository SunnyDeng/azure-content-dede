<properties 
	pageTitle="Verwalten von Speicherkonten | Microsoft Azure" 
	description="Dieses Thema beschreibt die Verwaltung von Speicherkonten in Azure im Verwaltungsportal." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2014" 
	ms.author="tamram"/>



<h1><a id="managestorageaccounts"></a>Verwalten von Speicherkonten</h1>

##Inhaltsverzeichnis##

* [Vorgehensweise: Verwalten der Speicherkontoreplikation](#georeplication)
* [Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren eines Speicherzugriffsschlüssels](#regeneratestoragekeys)
* [Vorgehensweise: Löschen eines Speicherkontos](#deletestorageaccount)

<h2><a id="georeplication"></a>Vorgehensweise: Replizieren Ihrer Speicherkontodaten für Stabilität und hohe Verfügbarkeit</h2>

[AZURE.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### So geben Sie Replikationseinstellungen für ein Speicherkonto an ###

1. Klicken Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard anzuzeigen.

2. Klicken Sie auf **Konfigurieren**.

3. Wählen Sie im Feld **Replikation** den Typ der Replikation für Ihr Speicherkonto aus.

4. Klicken Sie auf **Speichern** und bestätigen Sie bei Aufforderung Ihre Auswahl.


<h2><a id="regeneratestoragekeys"></a>Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln</h2>
Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

Verwenden Sie im [Verwaltungsportal](http://manage.windowsazure.com) die Option **Schlüssel verwalten** auf dem Dashboard oder die Seite **Speicher**, um die Speicherzugriffsschlüssel, die für den Zugriff auf Blob-, Tabellen- und Warteschlangendienste verwendet werden, anzuzeigen, zu kopieren und erneut zu generieren. 

### Kopieren eines Speicherzugriffsschlüssels ###

Sie können **Schlüssel verwalten** verwenden, um einen Speicherzugriffsschlüssel für die Verwendung in einer Verbindungszeichenfolge zu kopieren. Die Verbindungszeichenfolge benötigt für die Authentifizierung den Namen des Speicherkontos und einen Schlüssel. Informationen zum Konfigurieren von Verbindungszeichenfolgen für den Zugriff auf Azure-Speicherdienste finden Sie unter [Konfigurieren von Verbindungszeichenfolgen](http://msdn.microsoft.com/de-de/library/ee758697.aspx).

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard zu öffnen.

2. Klicken Sie auf **Schlüssel verwalten**.

 	**Zugriffsschlüssel verwalten** wird geöffnet.

	![Managekeys](./media/storage-manage-storage-account/Storage_ManageKeys.png)

 
3. Markieren Sie den Schlüsseltext, um einen Speicherzugriffsschlüssel zu kopieren. Klicken Sie dann mit der rechten Maustaste und klicken Sie auf **Kopieren**.

### Erneutes Generieren von Speicherzugriffsschlüsseln ###
Sie sollten regelmäßig die Zugriffsschlüssel für Ihr Speicherkonto ändern, um dafür zu sorgen, dass Ihre Speicherverbindungen möglichst sicher sind. Zwei Zugriffsschlüssel werden zugewiesen, um es Ihnen zu ermöglichen, Verbindungen zum Speicherkonto mit einem Zugriffsschlüssel aufrecht zu erhalten, während Sie den anderen Zugriffsschlüssel neu generieren. 

> [AZURE.WARNING] Das erneute Generieren der Zugriffsschlüssel wirkt sich auf virtuelle Computer, Mediendienste und alle Anwendungen aus, die vom Speicherkonto abhängen. Alle Clients, die den Zugriffsschlüssel verwenden, um auf das Speicherkonto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.

**Virtuelle Computer**: Falls Ihr Speicherkonto virtuelle Computer enthält, die ausgeführt werden, müssen Sie alle virtuellen Computer nach dem erneuten Generieren der Zugriffsschlüssel neu bereitstellen. Um die erneute Bereitstellung zu vermeiden, beenden Sie die virtuellen Computer, bevor Sie die Zugriffsschlüssel neu generieren.
 
**Mediendienste**: Falls Sie Mediendienste haben, die auf Ihr Speicherkonto angewiesen sind, müssen Sie die Zugriffsschlüssel mit Ihrem Mediendienst nach dem erneuten Generieren der Schlüssel erneut synchronisieren.
 
**Anwendungen**: Falls Sie Webanwendungen oder Clouddienste haben, die das Speicherkonto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln - es sei denn, Sie führen einen Rollup für die Schlüssel aus. Der Prozess ist folgendermaßen:

1. Aktualisieren Sie die Verbindungszeichenfolgen im Anwendungscode, damit sie auf den sekundären Zugriffsschlüssel des Speicherkontos verweisen. 

2. Generieren Sie den primären Zugriffsschlüssel für das Speicherkonto neu. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) im Dashboard oder auf der Seite **Konfigurieren** auf **Schlüssel verwalten**. Klicken Sie unter dem primären Zugriffsschlüssel auf **Neu generieren** und dann auf **Ja**, um das Generieren eines neuen Schlüssels zu bestätigen.

3. Aktualisieren Sie die Verbindungszeichenfolgen in Ihrem Code, um auf den neuen primären Zugriffsschlüssel zu verweisen.

4. Generieren Sie den sekundären Zugriffsschlüssel neu.


<h2><a id="deletestorageaccount"></a>Vorgehensweise: Löschen eines Speicherkontos</h2>

Um ein nicht mehr verwendetes Speicherkonto zu entfernen, verwenden Sie **Löschen** im Dashboard oder auf der Seite **Konfigurieren**. **Löschen** bewirkt, dass das gesamte Speicherkonto gelöscht wird, einschließlich aller Blobs, Tabellen und Warteschlangen im Konto. 

> [AZURE.WARNING] Es gibt keine Möglichkeit zur Wiederherstellung des Inhalts aus einem gelöschten Speicherkonto. Stellen Sie sicher, dass Sie alle zu speichernden Inhalte sichern, bevor Sie das Konto löschen.
> 
> Falls Ihr Speicherkonto VHD-Dateien oder -Festplatten für einen virtuellen Azure-Computer enthält, müssen Sie alle Images und Festplatten, die diese VHD-Dateien verwenden, löschen, bevor Sie das Speicherkonto löschen können. Halten Sie zunächst den virtuellen Computer an, falls er ausgeführt wird, und löschen Sie ihn dann. Um Festplatten zu löschen, navigieren Sie zur Registerkarte "Festplatten". Löschen Sie alle im Speicherkonto enthaltenen Festplatten. Um Images zu löschen, navigieren Sie zur Registerkarte "Images". Löschen Sie alle im Konto gespeicherten Images.

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com) auf **Speicher**.

2. Klicken Sie im Speicherkonto auf eine beliebige Stelle (außer auf den Namen). Klicken Sie dann auf **Löschen**.

	 -Oder:-

	Klicken Sie auf den Namen des Speicherkontos, um das Dashboard zu öffnen. Klicken Sie dann auf **Löschen**.

3. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Speicherkonto löschen möchten.
<!--HONumber=42-->
