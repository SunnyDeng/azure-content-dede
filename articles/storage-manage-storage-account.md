<properties linkid="manage-services-how-to-manage-a-storage-account" urlDisplayName="How to manage" pageTitle="How to manage storage accounts | Microsoft Azure" metaKeywords="Azure manage storage accounts, storage account management portal, storage account geo-replication, Azure geo-replication, Azure access keys" description="Learn how to manage storage accounts in Azure by using the Management Portal." metaCanonical="" services="storage" documentationCenter="" title="How To Manage Storage Accounts" authors="tamram" solutions="" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="managestorageaccounts"></span></a>Verwalten von Speicherkonten

## Inhaltsverzeichnis

-   [Gewusst wie: Verwalten der Speicherkontoreplikation][Gewusst wie: Verwalten der Speicherkontoreplikation]
-   [Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln][Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln]
-   [Gewusst wie: Löschen eines Speicherkontos][Gewusst wie: Löschen eines Speicherkontos]

## <span id="georeplication"></span></a>Gewusst wie: Replizieren Ihrer Speicherkontodaten für Stabilität und hohe Verfügbarkeit

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

### So geben Sie Replikationseinstellungen für ein Speicherkonto an

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard anzuzeigen.

2.  Klicken Sie auf **Konfigurieren**.

3.  Wählen Sie im Feld **Replikation** den Typ der Replikation für Ihr Speicherkonto aus.

4.  Klicken Sie auf **Speichern** und bestätigen Sie bei Aufforderung Ihre Auswahl.

## <span id="regeneratestoragekeys"></span></a>Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln

Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

Verwenden Sie im [Verwaltungsportal][Verwaltungsportal] die Option **Schlüssel verwalten** auf dem Dashboard oder die Seite **Speicher**, um die Speicherzugriffsschlüssel, dir für den Zugriff auf Blob-, Tabellen- und Warteschlangendienste verwendet werden, anzuzeigen, zu kopieren und erneut zu generieren.

### Kopieren eines Speicherzugriffsschlüssels

Sie können **Schlüssel verwalten** verwenden, um einen Speicherzugriffsschlüssel für die Verwendung in einer Verbindungszeichenfolge zu kopieren. Die Verbindungszeichenfolge benötigt für die Authentifizierung den Namen des Speicherkontos und einen Schlüssel. Informationen zum Konfigurieren von Verbindungszeichenfolgen für den Zugriff auf Azure-Speicherdienste finden Sie unter [Konfigurieren von Verbindungszeichenfolgen][Konfigurieren von Verbindungszeichenfolgen].

1.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard zu öffnen.

2.  Klicken Sie auf **Schüssel verwalten**.

    **Zugriffsschlüssel verwalten** wird geöffnet.

    ![Managekeys][Managekeys]

3.  Markieren Sie den Schlüsseltext, um einen Speicherzugriffsschlüssel zu kopieren. Klicken Sie dann mit der rechten Maustaste und klicken Sie auf **Kopieren**.

### Erneutes Generieren von Speicherzugriffsschlüsseln

Sie sollten regelmäßig die Zugriffsschlüssel für Ihr Speicherkonto ändern, um dafür zu sorgen, dass Ihre Speicherverbindungen möglichst sicher sind. Zwei Zugriffsschlüssel werden zugewiesen, um es Ihnen zu ermöglichen, Verbindungen zum Speicherkonto mit einem Zugriffsschlüssel aufrecht zu erhalten, während Sie den anderen Zugriffsschlüssel neu generieren.


<div class="dev-callout"> 
    <b>Warnung</b> 
    <p>Das erneute Generieren der Zugriffsschl&uuml;ssel wirkt sich auf virtuelle Computer, Mediendienste und alle Anwendungen aus, die vom Speicherkonto abh&auml;ngen. Alle Clients, die den Zugriffsschl&uuml;ssel verwenden, um auf das Speicherkonto zuzugreifen, m&uuml;ssen aktualisiert werden, um den neuen Schl&uuml;ssel zu verwenden.
    </p> 
</div>


**Virtuelle Computer**: Falls Ihr Speicherkonto virtuelle Computer enthält, die ausgeführt werden, müssen Sie alle virtuellen Computer nach dem erneuten Generieren der Zugriffsschlüssel neu bereitstellen. Um die erneute Bereitstellung zu vermeiden, beenden Sie die virtuellen Computer, bevor Sie die Zugriffsschlüssel neu generieren.

**Mediendienste**: Falls Sie Mediendienste haben, die auf Ihr Speicherkonto angewiesen sind, müssen Sie die Zugriffsschlüssel mit Ihrem Mediendienst nach dem erneuten Generieren der Schlüssel erneut synchronisieren.

**Anwendungen**: Falls Sie Webanwendungen oder Clouddienste haben, die das Speicherkonto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln – es sei denn, Sie führen einen Rollup für die Schlüssel aus. Der Prozess ist folgendermaßen:

1.  Aktualisieren Sie die Verbindungszeichenfolgen im Anwendungscode, damit sie auf den sekundären Zugriffsschlüssel des Speicherkontos verweisen.

2.  Generieren Sie den primären Zugriffsschlüssel für das Speicherkonto neu. Klicken Sie im [Verwaltungsportal][Verwaltungsportal] im Dashboard oder auf der Seite **Konfigurieren** auf **Schlüssel verwalten**. Klicken Sie unter dem primären Zugriffsschlüssel auf **Neu generieren** und dann auf **Ja**, um das Generieren eines neuen Schlüssels zu bestätigen.

3.  Aktualisieren Sie die Verbindungszeichenfolgen in Ihrem Code, um auf den neuen primären Zugriffsschlüssel zu verweisen.

4.  Generieren Sie den sekundären Zugriffsschlüssel neu.

## <span id="deletestorageaccount"></span></a>Gewusst wie: Löschen eines Speicherkontos

Um ein nicht mehr verwendetes Speicherkonto zu entfernen, verwenden Sie **Löschen** im Dashboard oder auf der Seite **Konfigurieren**. Durch **Löschen** wird das gesamte Speicherkonto gelöscht, einschließlich aller Blobs, Tabellen und Warteschlangen im Konto.

<div class="dev-callout">
    <b>Warnung</b>
    <p>Es gibt keine M&ouml;glichkeit zur Wiederherstellung des Inhalts aus einem gel&ouml;schten Speicherkonto. Stellen 
    Sie sicher, dass Sie alle zu speichernden Inhalte sichern, bevor Sie das Konto l&ouml;schen.
    </p>
    <p>
    Wenn Ihr Speicherkonto VHD-Dateien oder Datentr&auml;ger f&uuml;r einen virtuellen Azure-Computer 
    enth&auml;lt, m&uuml;ssen Sie alle Images und Datentr&auml;ger l&ouml;schen, die diese VHD-Dateien verwenden, 
    bevor Sie das Speicherkonto l&ouml;schen k&ouml;nnen. Halten Sie zun&auml;chst den virtuellen Computer an, falls er ausgef&uuml;hrt wird, und l&ouml;schen Sie ihn dann. Um Festplatten zu l&ouml;schen, navigieren Sie zur Registerkarte &quot;Festplatten&quot;. L&ouml;schen Sie alle im Speicherkonto enthaltenen Festplatten. Um Images zu l&ouml;schen, navigieren Sie zur Registerkarte &quot;Images&quot;. L&ouml;schen Sie alle im Konto gespeicherten Images.
    </p>
</div>

1.  Klicken Sie im [Verwaltungsportal][Verwaltungsportal] auf **Speicher**.

2.  Klicken Sie im Speicherkonto auf eine beliebige Stelle (nicht auf den Namen). Klicken Sie dann auf **Löschen**.

    -Oder-

    Klicken Sie auf den Namen des Speicherkontos, um das Dashboard zu öffnen. Klicken Sie dann auf **Löschen**.

3.  Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Speicherkonto löschen möchten.

  [Gewusst wie: Verwalten der Speicherkontoreplikation]: #georeplication
  [Gewusst wie: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln]: #regeneratestoragekeys
  [Gewusst wie: Löschen eines Speicherkontos]: #deletestorageaccount
  [storage-replication-options]: ../includes/storage-replication-options.md
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  [Verwaltungsportal]: http://manage.windowsazure.com
  [Konfigurieren von Verbindungszeichenfolgen]: http://msdn.microsoft.com/de-de/library/ee758697.aspx
  [Managekeys]: ./media/storage-manage-storage-account/Storage_ManageKeys.png
