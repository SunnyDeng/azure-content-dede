<properties urlDisplayName="How to create" pageTitle="Erstellen, Verwalten oder Löschen eines Speicherkontos | Azure" metaKeywords="" description="Learn how to create, manage, or delete a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="tamram" />


# Informationen zu Azure-Speicherkonten

Ein Azure-Speicherkonto ist ein sicheres Konto, über das Sie Zugriff auf Dienste in Azure Storage erhalten. Ihr Speicherkonto stellt den eindeutigen Namespace für Ihre Daten bereit und ist standardmäßig nur für Sie, den Kontoeigentümer, verfügbar. 

Es gibt zwei Arten von Speicherkonten:

- Ein Standardspeicherkonto umfasst Blob-, Tabellen- und Warteschlangenspeicher. Dateispeicher ist auf Anforderung über die [Azure-Vorschauseite](/de-de/services/preview/)verfügbar.
- Ein Premium-Speicherkonto unterstützt derzeit nur Datenträger in virtuellen Azure-Computern. Azure Premium Storage ist auf Anforderung über die [Azure-Vorschauseite](/de-de/services/preview/)verfügbar. Unter [Premium Storage: Hochleistungsspeicher für Arbeitsauslastungen auf virtuellen Azure-Computern](http://go.microsoft.com/fwlink/?LinkId=521898) finden Sie eine ausführliche Übersicht über Premium Storage.

Die Nutzung von Azure-Speicher wird Ihnen auf der Basis Ihres Speicherkontos in Rechnung gestellt. Speicherkosten basieren auf vier Faktoren: Speicherkapazität, Replikationsschema, Speichertransaktionen und Datenausgang. 

- Speicherkapazität bezieht sich darauf, wie viel von Ihrer Speicherkontozuweisung zum Speichern von Daten verwendet wird. Die Kosten des einfachen Speicherns von Daten wird dadurch bestimmt, wie viele Daten Sie speichern und wie diese Daten repliziert werden. 
- Die Replikation bestimmt, wie viele Kopien Ihrer Daten auf einmal und an welchen Speicherorten beibehalten werden. 
- Transaktionen beziehen sich auf alle Lese- und Schreibvorgänge im Azure-Speicher. 
- Datenausgang bezieht sich auf Daten, die aus einer Azure-Region übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird und entweder ein Clouddienst oder ein anderer Anwendungstyp ist, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. (Für Azure-Dienste können Sie Maßnahmen durchführen, um Daten und Dienste in den gleichen Rechenzentren zu gruppieren und so Datenausgangsgebühren zu reduzieren oder zu eliminieren.)  

Auf der Seite mit den [Details zu Speicherpreisen](http://www.windowsazure.com/de-de/pricing/details/#storage) finden Sie ausführliche Preisinformationen für Speicherkapazität, Replikation und Transaktionen. Auf der Seite [Preisdetails zur Datenübertragung](http://www.windowsazure.com/de-de/pricing/details/data-transfers/) finden Sie ausführliche Informationen für den Datenausgang.

Ausführliche Informationen zu Speicherkontokapazitäts- und -leistungszielen stehen unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/windowsazure/dn249410.aspx)zur Verfügung.

> [WACOM.NOTE] Wenn Sie einen virtuellen Azure-Computer erstellen, wird ein Speicherkonto automatisch in der Bereitstellungsregion erstellt, falls Sie noch nicht über ein Speicherkonto in der entsprechenden Region verfügen. In diesem Fall ist es nicht nötig, die unten aufgeführten Schritte zum Erstellen eines Speicherkontos für Ihre virtuellen Computerdatenträger zu befolgen. Der Name des Speicherkontos basiert auf dem Namen des virtuellen Computers. In der [Dokumentation zu virtuellen Azure-Computern](/de-de/documentation/services/virtual-machines/) finden Sie weitere Details. <br />

## Inhaltsverzeichnis ##

In diesem Artikel wird beschrieben, wie Sie ein Standardspeicherkonto erstellen. Sie erfahren zudem mehr über einige Entscheidungen, die Sie bei der Erstellung des Kontos in Betracht ziehen müssen. Darüber hinaus wird beschrieben, wie Sie die Zugriffsschlüssel für Ihr Speicherkonto verwalten und ein Speicherkonto löschen.


- [Vorgehensweise: Erstellen eines Speicherkontos](#create)
- [Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln](#regeneratestoragekeys)
- [Vorgehensweise: Löschen eines Speicherkontos](#deletestorageaccount)
* [Nächste Schritte](#next)

## <a id="create"></a>Vorgehensweise: Erstellen eines Speicherkontos ##

1. Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com)an.

2. Klicken Sie auf **Neu erstellen**, dann auf **Speicher** und schließlich auf **Schnellerfassung**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. Geben Sie unter **URL** einen Namen für Ihr Speicherkonto ein. Unter [Speicherkontoendpunkte](#account-endpoints) weiter unten finden Sie Details dazu, wie dieser Name zum Adressieren von Objekten verwendet wird, die Sie im Azure-Speicher speichern.

4. Wählen Sie unter **Speicherort/Affinitätsgruppe** einen Speicherort für Ihr Speicherkonto aus, der in Ihrer Nähe oder der Nähe Ihrer Kunden ist. Wenn von einem anderen Azure-Dienst wie einem virtuellen Azure-Computer oder einem Cloud-Dienst, auf Daten in Ihrem Speicherkonto zugegriffen wird, sollten Sie möglicherweise eine Affinitätsgruppe aus der Liste auswählen, um Ihr Speicherkonto im selben Rechenzentrum mit anderen von Ihnen verwendeten Azure-Diensten zu gruppieren, um die Leistung zu verbessern und Kosten zu verringern. 

	> [WACOM.NOTE] Beachten Sie, dass Sie eine Affinitätsgruppe beim Erstellen des Speicherkontos auswählen müssen. Sie können kein vorhandenes Konto zu einer Affinitätsgruppe verschieben.

	Details zu Affinitätsgruppen finden Sie unter [Dienstzusammenstellung mit einer Affinitätsgruppe](#affinity-group) weiter unten.

	
5. Wenn Sie über mehrere Azure-Abonnements verfügen, wird das Feld **Abonnement** angezeigt. Geben Sie unter **Abonnement** das Azure-Abonnement ein, das Sie mit dem Speicherkonto verwenden möchten. Sie können bis zu fünf Speicherkontos für ein Abonnement erstellen.

6. Wählen Sie unter **Replikation** die gewünschte Replikationsstufe für Ihr Speicherkonto aus. Die empfohlene Replikationsoption ist die georedundante Replikation, die eine maximale Lebensdauer für Ihre Daten bereitstellt. Weitere Details zu Azure-Speicherreplikationsoptionen finden Sie unter [Replikationsoptionen für das Speicherkonto](#replication-options) weiter unten.

6. Klicken Sie auf **Speicherkonto erstellen**.

	Die Erstellung Ihres Speicherkontos kann einige Minuten in Anspruch nehmen. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen. Nachdem das Speicherkonto erstellt wurde, hat Ihr neues Speicherkonto den Status **Online** und kann verwendet werden. 

![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)


### <a id="account-endpoints"></a>Speicherkontoendpunkte 

Jedes Objekt, das Sie in Azure-Speicher speichern, verfügt über eine eindeutige URL-Adresse. Der Speicherkontoname bildet die Unterdomäne dieser Adresse. Die Unterdomäne bildet zusammen mit dem Domänennamen, der für jeden Dienst spezifisch ist, einen "Endpunkt" für Ihr Speicherkonto. 

Wenn Ihr Speicherkonto beispielsweise "mystorageaccount" heißt, sind folgende Standardendpunkte für Ihr Speicherkonto vorhanden: 

- Blob-Dienst: http://*mystorageaccount*.blob.core.windows.net

- Tabellendienst: http://*mystorageaccount*.table.core.windows.net

- Warteschlangendienst: http://*mystorageaccount*.queue.core.windows.net

- Dateidienst: http://*mystorageaccount*.file.core.windows.net

Die Endpunkte für Ihr Speicherkonto werden im Speicher-Dashboard im Azure-Verwaltungsportal angezeigt, sobald Ihr Konto erstellt wurde.

Die URL für den Zugriff auf ein Objekt in einem Speicherkonto wird durch Anhängen des Objektstandorts im Speicherkonto an den Endpunkt generiert. Eine Blob-Adresse kann beispielsweise folgendes Format haben: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Sie können auch einen benutzerdefinierten Domänennamen zur Verwendung mit Ihrem Speicherkonto konfigurieren. Einzelheiten dazu stehen unter [Konfigurieren eines benutzerdefinierten Domänennamens für Blob-Daten in einem Speicherkonto](../storage-custom-domain-name/) zur Verfügung.

### <a id="affinity-group"></a>Dienstzusammenstellung mit einer Affinitätsgruppe 

Eine "Affinitätsgruppe" ist eine geografische Gruppierung Ihrer Azure-Dienste und VMs mit Ihrem Azure-Speicherkonto. Eine Affinitätsgruppe kann die Dienstleistung verbessern, indem Computerarbeitslasten im gleichen Rechenzentrum oder in der Nähe der Zielbenutzer platziert werden. Außerdem fallen keine Gebühren für den Datenausgang an, wenn ein Dienst, der zur gleichen Affinitätsgruppe gehört, auf Daten im Speicherkonto zugreift.

> [WACOM.NOTE]  Öffnen Sie zum Erstellen einer Affinitätsgruppe den Bereich <b>Einstellungen</b> des Verwaltungsportals, klicken Sie auf <b>Affinitätsgruppe,</b>und klicken Sie dann entweder auf <b>Affinitätsgruppe hinzufügen</b> oder auf <b>Hinzufügen</b> (Schaltfläche). Sie können Affinitätsgruppen auch mithilfe der Azure Service Management-API erstellen und verwalten. Hierzu finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/ee460798.aspx">Vorgänge bei Affinitätsgruppen</a> weitere Informationen.


### <a id="replication-options"></a>Speicherkonto-Replikationsoptionen

[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]


## <a id="regeneratestoragekeys"></a>Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln

Wenn Sie ein Speicherkonto erstellen, generiert Azure zwei 512-Bit-Speicherzugriffsschlüssel, die für die Authentifizierung verwendet werden, wenn der Zugriff auf das Speicherkonto erfolgt. Durch Bereitstellen von zwei Speicherzugriffsschlüsseln ermöglicht Azure Ihnen das erneute Generieren der Schlüssel ohne Unterbrechung des Speicherdiensts oder Zugriff auf diesen Dienst.

> [WACOM.NOTE] Sie sollten das Weitergeben von Speicherkonto-Zugriffsschlüsseln an andere vermeiden. Um den Zugriff auf Speicherressourcen zu gewähren, ohne Ihre Zugriffsschlüssel weiterzugeben, verwenden Sie eine "Shared Access Signature". Eine Shared Access Signature (SAS) bietet Zugriff auf eine Ressource in Ihrem Konto für ein von Ihnen definiertes Zeitintervall und mit den von Ihnen festgelegten Berechtigungen. Im Lernprogramm [Shared Access Signature](../storage-dotnet-shared-access-signature-part-1/) finden Sie weitere Informationen.

Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com)auf die Option **Schlüssel verwalten** im Dashboard oder die Seite **Speicher**, um die Speicherzugriffsschlüssel anzuzeigen, zu kopieren und erneut zu generieren, die für den Zugriff auf Blob-, Tabellen- und Warteschlangendienste verwendet werden. 

### Kopieren eines Speicherzugriffsschlüssels ###

Über die Option **Schlüssel verwalten** können Sie einen Speicherzugriffsschlüssel für die Verwendung in einer Verbindungszeichenfolge kopieren. Die Verbindungszeichenfolge benötigt für die Authentifizierung den Namen des Speicherkontos und einen Schlüssel. Informationen zum Konfigurieren von Verbindungszeichenfolgen für den Zugriff auf Azure-Speicherdienste stehen unter [Konfigurieren von Verbindungszeichenfolgen](http://msdn.microsoft.com/de-de/library/ee758697.aspx)zur Verfügung.

1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com)auf **Speicher** und dann auf den Namen des Speicherkontos, um das Dashboard zu öffnen.

2. Klicken Sie auf **Schlüssel verwalten**.

 	**Zugriffsschlüssel verwalten** wird geöffnet.

	![Managekeys](./media/storage-create-storage-account/Storage_ManageKeys.png)

 
3. Markieren Sie den Schlüsseltext, um einen Speicherzugriffsschlüssel zu kopieren. Klicken Sie dann mit der rechten Maustaste, und klicken Sie auf **Kopieren**.

### Erneutes Generieren von Speicherzugriffsschlüsseln ###
Sie sollten regelmäßig die Zugriffsschlüssel für Ihr Speicherkonto ändern, um dafür zu sorgen, dass Ihre Speicherverbindungen möglichst sicher sind. Zwei Zugriffsschlüssel werden zugewiesen, um es Ihnen zu ermöglichen, Verbindungen zum Speicherkonto mit einem Zugriffsschlüssel aufrecht zu erhalten, während Sie den anderen Zugriffsschlüssel neu generieren. 

<div class="dev-callout"> 
    <b>Warnung</b> 
    <p>Das erneute Generieren der Zugriffsschlüssel wirkt sich auf virtuelle Computer, Mediendienste und alle Anwendungen aus, die vom Speicherkonto abhängen. Alle Clients, die den Zugriffsschlüssel verwenden, um auf das Speicherkonto zuzugreifen, müssen aktualisiert werden, um den neuen Schlüssel zu verwenden.
    </p> 
    </div>

**Virtulle Computer**: Falls Ihr Speicherkonto virtuelle Computer enthält, die ausgeführt werden, müssen Sie alle virtuellen Computer nach dem erneuten Generieren der Zugriffsschlüssel neu bereitstellen. Um die erneute Bereitstellung zu vermeiden, beenden Sie die virtuellen Computer, bevor Sie die Zugriffsschlüssel neu generieren.
 
**Mediendienste**:- Falls Sie Mediendienste haben, die auf Ihr Speicherkonto angewiesen sind, müssen Sie die Zugriffsschlüssel mit Ihrem Mediendienst nach dem erneuten Generieren der Schlüssel erneut synchronisieren.
 
**Anwendungen**: Falls Sie Webanwendungen oder Cloud-Dienste haben, die das Speicherkonto verwenden, verlieren Sie die Verbindungen beim erneuten Generieren von Schlüsseln - es sei denn, Sie führen einen Rollup für die Schlüssel aus. Der Prozess ist folgendermaßen:

1. Aktualisieren Sie die Verbindungszeichenfolgen im Anwendungscode, damit sie auf den sekundären Zugriffsschlüssel des Speicherkontos verweisen. 

2. Generieren Sie den primären Zugriffsschlüssel für das Speicherkonto neu. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com)im Dashboard oder auf der Seite **Konfigurieren** auf **Schlüssel verwalten**. Klicken Sie unter dem primären Zugriffsschlüssel auf **Neu generieren** und dann auf **Ja**, um das Generieren eines neuen Schlüssels zu bestätigen.

3. Aktualisieren Sie die Verbindungszeichenfolgen in Ihrem Code, um auf den neuen primären Zugriffsschlüssel zu verweisen.

4. Generieren Sie den sekundären Zugriffsschlüssel neu.

## <a id="deletestorageaccount"></a>Vorgehensweise: Löschen eines Speicherkontos

Zum Entfernen eines nicht mehr verwendeten Speicherkontos verwenden Sie die Option **Löschen** im Dashboard oder auf der Seite **Konfigurieren**. **Löschen** löscht das gesamte Speicherkonto, einschließlich Blobs, Tabellen und Warteschlangen im Konto. 

<div class="dev-callout">
	<b>Warnung</b>
	<p>Es gibt keine Möglichkeit zur Wiederherstellung des Inhalts aus einem gelöschten Speicherkonto. Stellen 
	Sie sicher, dass Sie alle zu speichernden Inhalte sichern, bevor Sie das Konto löschen.
	</p>
	<p>
	Wenn Ihr Speicherkonto VHD-Dateien oder Datenträger für einen virtuellen Azure-Computer 
	enthält, müssen Sie alle Images und Datenträger löschen, die diese VHD-Dateien verwenden, 
	bevor Sie das Speicherkonto löschen können. Halten Sie zunächst den virtuellen Computer an, falls er ausgeführt wird, und löschen Sie ihn dann. Um Festplatten zu löschen, navigieren Sie zur Registerkarte "Festplatten". Löschen Sie alle im Speicherkonto enthaltenen Festplatten. Um Images zu löschen, navigieren Sie zur Registerkarte "Images". Löschen Sie alle im Konto gespeicherten Images.
	</p>
</div>


1. Klicken Sie im [Verwaltungsportal](http://manage.windowsazure.com)auf **Speicher**.

2. Klicken Sie im Speicherkonto auf eine beliebige Stelle außer dem Namen, und klicken Sie dann auf **Löschen**.

	 -Oder-

	Klicken Sie auf den Namen des Speicherkontos, um das Dashboard zu öffnen, und klicken Sie dann auf **Löschen**.

3. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Speicherkonto löschen möchten.

## <a id="next"></a>Nächste Schritte

- Weitere Informationen zu Azure Storage finden Sie in der Dokumentation zu Azure Storage unter [azure.com](http://azure.microsoft.com/de-de/documentation/services/storage/) und auf [MSDN](http://msdn.microsoft.com/de-de/library/gg433040.aspx). 

- Besuchen Sie den Blog des [Azure Storage-Teams](http://blogs.msdn.com/b/windowsazurestorage/).


 
\n<!--HONumber=35.1--> 
