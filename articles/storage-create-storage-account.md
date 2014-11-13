<properties urlDisplayName="How to create" pageTitle="Erstellen eines Speicherkontos | Azure" metaKeywords="" description="Dieses Thema beschreibt die Erstellung eines Speicherkontos im Azure-Verwaltungsportal." metaCanonical="" services="storage" documentationCenter="" title="Erstellen eines Speicherkontos" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="createstorageaccount"></span></a>Erstellen eines Speicherkontos

Um Dateien und Daten in den Blob-, Tabellen-, Warteschlangen- und Dateidiensten in Azure speichern zu können, müssen Sie ein Speicherkonto in der gewünschten geografischen Region erstellen. Dieses Thema beschreibt die Erstellung eines Speicherkontos im Azure Verwaltungsportal.

Weitere Informationen zu Kapazität und Durchsatz von Speicherkonten finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure-Speicher][Skalierbarkeits- und Leistungsziele für Azure-Speicher].

> [WACOM.NOTE] Für einen virtuellen Azure-Computer wird ein Speicherkonto automatisch in der Bereitstellungsregion erstellt, falls Sie noch kein Speicherkonto in der entsprechenden Region haben. Der Name des Speicherkontos basiert auf dem Namen des virtuellen Computers.

## Inhaltsverzeichnis

-   [Gewusst wie: Speicherkonto erstellen][Gewusst wie: Speicherkonto erstellen]
-   [Nächste Schritte][Nächste Schritte]

## <span id="create"></span></a>Gewusst wie: Speicherkonto erstellen

1.  Melden Sie sich im [Verwaltungsportal][Verwaltungsportal] an.

2.  Klicken Sie auf **Neu erstellen**, auf **Speicher** und dann auf **Schnellerfassung**.

    ![NewStorageAccount][NewStorageAccount]

3.  Geben Sie unter **URL** einen Unterdomänennamen zur Verwendung in der Speicherkonto-URL ein. Sie werden den Speicherort des Objekts an den Endpunkt anhängen, um auf ein Objekt im Speicher zuzugreifen. Die URL für den Zugriff auf einen Blob kann z. B. [http://\*meinKonto][http://\*meinKonto]*.blob.core.windows.net/*meinContainer*/*meinBlob\* lauten.

4.  Wählen Sie unter **Region/Affinitätsgruppe** eine Region oder Affinitätsgruppe für den Speicher aus. Wählen Sie eine Affinitätsgruppe statt einer Region, wenn Ihre Speicherdienste im selben Datacenter vorhanden sein sollen, wie die anderen Azure-Dienste, die Sie verwenden. Dadurch wird möglicherweise die Leistung verbessert, und es fallen keine Kosten für Ausgangsverkehr an.

    > [WACOM.NOTE] Um eine Affinitätsgruppe zu erstellen, öffnen Sie den Bereich **Einstellungen** im Verwaltungsportal, klicken Sie auf **Affinitätsgruppen** und anschließend entweder auf **Affinitätsgruppe hinzufügen** oder auf die **Hinzufügen**-Schaltfläche. Sie können Affinitätsgruppen auch mithilfe der Azure Service Management-API erstellen und verwalten. Weitere Informationen erhalten Sie unter [Operations on Affinity Groups][Operations on Affinity Groups] (Vorgänge zu Affinitätsgruppen, in englischer Sprache).

5.  Wenn Sie mehr als ein Azure-Abonnement besitzen, wird das Feld **Abonnement** angezeigt. Geben Sie unter **Abonnement** das Azure-Abonnement ein, das Sie mit dem Speicherkonto verwenden möchten. Sie können bis zu fünf Speicherkontos für ein Abonnement erstellen.

6.  Wählen Sie unter **Replikation** die gewünschte Replikationsebene für Ihr Speicherkonto aus.

    [WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

7.  Klicken Sie auf **Speicherkonto erstellen**.

    Die Erstellung Ihres Speicherkontos kann einige Minuten in Anspruch nehmen. Sie können die Benachrichtigungen unten im Portal überwachen, um den Status zu überprüfen. Nachdem das Speicherkonto erstellt wurde, hat Ihr neues Speicherkonto den Status **Online** und kann verwendet werden.

    ![StoragePage][StoragePage]

## <span id="next"></span></a>Nächste Schritte

-   Weitere Informationen über den Azure-Speicher finden Sie in der entsprechenden Dokumentation auf [azure.com][azure.com] und auf [MSDN][MSDN].

-   Besuchen Sie den [Blog des Azure-Speicherteams][Blog des Azure-Speicherteams].

  [Skalierbarkeits- und Leistungsziele für Azure-Speicher]: http://msdn.microsoft.com/de-de/library/dn249410.aspx
  [Gewusst wie: Speicherkonto erstellen]: #create
  [Nächste Schritte]: #next
  [Verwaltungsportal]: https://manage.windowsazure.com
  [NewStorageAccount]: ./media/storage-create-storage-account/storage_NewStorageAccount.png
  [http://\*meinKonto]: http://*myaccount
  [Operations on Affinity Groups]: http://msdn.microsoft.com/de-de/library/windowsazure/ee460798.aspx
  [StoragePage]: ./media/storage-create-storage-account/Storage_StoragePage.png
  [azure.com]: http://azure.microsoft.com/de-de/documentation/services/storage/
  [MSDN]: http://msdn.microsoft.com/de-de/library/gg433040.aspx
  [Blog des Azure-Speicherteams]: http://blogs.msdn.com/b/windowsazurestorage/
