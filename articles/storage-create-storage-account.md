<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" authors="tysonn" solutions="" manager="paulettm" editor="cgronlun" />

Erstellen eines Speicherkontos
==============================

Sie müssen ein Speicherkonto in der geografischen Region erstellen, in der Sie die Daten speichern möchten, um Dateien und Daten in den Blob-Diensten, Warteschlangendiensten und Tabellenspeicherdiensten zu speichern. Ein Speicherkonto kann bis zu 200 TB Daten umfassen, und Sie können bis zu zwanzig Speicherkontos für jedes Azure-Abonnement erstellen. Weitere Informationen finden Sie unter [Windows Azure Storage Scalability and Performance Targets](http://msdn.microsoft.com/en-us/library/dn249410.aspx) (Speicherskalierbarkeit und Leistungsziele von Azure, in englischer Sprache).

Dieses Thema beschreibt die Erstellung eines Speicherkontos im Azure Verwaltungsportal.

**Hinweis**

Für einen virtuellen Azure-Computer wird automatisch ein Speicherkonto am Speicherort der Bereitstellung erstellt, sofern Sie dort nicht bereits über ein Speicherkonto verfügen. Der Name des Speicherkontos basiert auf dem Namen des virtuellen Computers.

Inhaltsverzeichnis
------------------

-   [Vorgehensweise: Speicherkonto erstellen](#create)
-   [Nächste Schritte](#next)

Vorgehensweise: Speicherkonto erstellen
---------------------------------------

1.  Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com) an.

2.  Klicken Sie auf **Neu erstellen**, auf **Speicher** und dann auf **Schnellerfassung**.

    ![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3.  Geben Sie unter **URL** einen Unterdomänennamen zur Verwendung in der Speicherkonto-URL ein. Sie werden den Speicherort des Objekts an den Endpunkt anhängen, um auf ein Objekt im Speicher zuzugreifen. Eine URL zum Zugreifen auf ein Blob wäre beispielsweise http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

4.  Wählen Sie unter **Region/Affinitätsgruppe** eine Region oder Affinitätsgruppe für den Speicher aus. Wählen Sie eine Affinitätsgruppe statt einer Region, wenn Ihre Speicherdienste im selben Datacenter vorhanden sein sollen, wie die anderen Azure-Dienste, die Sie verwenden. Dadurch wird möglicherweise die Leistung verbessert, und es fallen keine Kosten für Ausgangsverkehr an.

    > [WACOM.NOTE] \> Öffnen Sie den Bereich **Netzwerke** des Verwaltungsportals, klicken Sie auf **Affinitätsgruppen** und dann entweder auf **Neue Affinitätsgruppe erstellen** oder auf **Erstellen**, um eine Affinitätsgruppe zu erstellen. Sie können Affinitätsgruppen verwenden, die Sie im vorherigen Verwaltungsportal erstellt haben. Klicken Sie in der Titelleiste auf **Vorschau** und dann auf **Take me to the previous portal**, um das andere Portal zu öffnen. (Klicken Sie auf **View the Preview Portal** unten im Portal, um zu diesem Portal zurückzukehren.) Sie können Affinitätsgruppen auch mithilfe der Azure Service Management-API erstellen und verwalten. Weitere Informationen erhalten Sie unter [Operations on Affinity Groups](http://msdn.microsoft.com/en-us/library/windowsazure/ee460798.aspx) (Vorgänge zu Affinitätsgruppen, in englischer Sprache).

5.  Wenn Sie mehr als ein Azure-Abonnement besitzen, wird das Feld **Abonnement** angezeigt. Geben Sie unter **Abonnement** das Azure-Abonnement ein, das Sie mit dem Speicherkonto verwenden möchten. Sie können bis zu fünf Speicherkontos für ein Abonnement erstellen.

6.  Wählen Sie unter **Replikation** die gewünschte Replikationsebene für Ihr Speicherkonto aus.

    Standardmäßig ist **Geo-Redundant** für die Replikation festgelegt. Bei georedundanter Replikation werden Ihr Speicherkonto und alle darin enthaltenen Daten im Falle eines schwerwiegenden Ausfalls des primären Speicherorts an einen sekundären Speicherort übertragen. Azure weist einen sekundären Speicherort in der selben Region zu, der nicht geändert werden kann. Nach einem Failover wird der sekundäre Speicherort zum primären Speicherort des Speicherkontos, und Ihre Daten werden an einen neuen sekundären Speicherort repliziert.

    Wenn Sie auf Daten des sekundären Speicherorts zugreifen möchten, können Sie die Replikation **Read-Access Geo-Redundant** auswählen. Diese Option bietet georedundante Replikation und ermöglicht schreibgeschützten Zugriff auf replizierte Daten am sekundären Speicherort. Mithilfe der schreibgeschützten georedundanten Replikation können Sie entweder vom primären oder vom sekundären Speicherort aus auf Ihre Daten zugreifen, falls einer der Speicherorte nicht verfügbar ist.

    Eine dritte Replikationsoption, **Read Access Geo-Redundant**, befindet sich derzeit im Vorschaustadium. Diese Option ermöglicht schreibgeschützten Zugriff auf replizierte Daten am sekundären Speicherort. Mithilfe der schreibgeschützten georedundanten Replikation können Sie entweder vom primären oder vom sekundären Speicherort aus auf Ihre Daten zugreifen, falls einer der Speicherorte nicht verfügbar ist.

    > [WACOM.NOTE] \> Wenn Sie die Testversion der schreibgeschützten georedundanten Replikation nutzen möchten, müssen Sie eine manuelle Anfrage stellen, damit die Funktion für Ihr Abonnement aktiviert wird. Auf der Seite mit [Azure-Vorschaufunktionen](https://account.windowsazure.com/PreviewFeatures) können Sie Zugriff auf die schreibgeschützte georedundante Replikation für Ihr Abonnement anfordern. Weitere Informationen zur schreibgeschützten georedundanten Replikation erhalten Sie unter [Azure Storage Team Blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx) (in englischer Sprache). Wenn die schreibgeschützte georedundante Replikation nicht als Vorschaufunktion für Ihr Abonnement aktiviert ist, dann ist die Option zur Auswahl für Ihr Speicherkonto deaktiviert.

    Preisinformationen für die Speicherkontoreplikation finden Sie unter [Speicher – Preisdetails](http://www.windowsazure.com/en-us/pricing/details/storage/).

7.  Klicken Sie auf **Speicherkonto erstellen**.

    Die Erstellung Ihres Speicherkontos kann einige Minuten in Anspruch nehmen. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen. Nachdem das Speicherkonto erstellt wurde, hat Ihr neues Speicherkonto den Status **Online** und kann verwendet werden.

    ![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

Nächste Schritte
----------------

-   Weitere Informationen zu Azure-Speicherdiensten erhalten Sie unter [Übersicht über Cloudspeicher](http://www.windowsazure.com/en-us/develop/net/fundamentals/cloud-storage/) (in englischer Sprache) und [Blob-Dienste, Warteschlangendienste und Tabellenspeicherdienste](http://msdn.microsoft.com/en-us/library/gg433040.aspx) (in englischer Sprache).

-   Besuchen Sie den [Azure Storage Team Blog](http://blogs.msdn.com/b/windowsazurestorage/).

-   Konfigurieren Sie Ihre Apps zur Verwendung mit Azure Blob-Diensten, Tabellenspeicherdiensten und Warteschlangendiensten. Das [Azure Developer Center](http://www.windowsazure.com/en-us/develop/overview/) bietet Anleitungen für die Verwendung von Blob-Diensten, Tabellenspeicherdiensten und Warteschlangendiensten mit Ihren .NET-, Node.js-, Java- und PHP-Anwendungen. Anleitungen speziell für eine Programmiersprache finden Sie in der Anleitung für die jeweilige Sprache.


