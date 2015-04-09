## Was ist der Warteschlangenspeicher?

Der Azure-Warteschlangenspeicher ist ein Dienst zur Speicherung großer Mengen von
Nachrichten, auf die von überall auf der Welt aus
über authentifizierte Aufrufe über HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann
bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten - bis zur
Beschränkung der Gesamtkapazität des Speicherkontos. Ein Speicherkonto kann bis zu 500 TB Blob-, Warteschlangen- und Tabellendaten enthalten. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/azure/dn249410.aspx).

Warteschlangenspeicherungen werden hauptsächlich für folgende Zwecke verwendet:

-   <span>Erstellung eines Arbeits-Backlogs zur asynchronen Verarbeitung</span>
-   Weiterleitung von Nachrichten von einer Azure-Webrolle an eine Azure-
    Workerrolle

## Konzepte des Warteschlangendiensts

Der Warteschlangendienst umfasst die folgenden Komponenten:

![Warteschlange1](./media/storage-queue-concepts-include/queue1.png)


- **URL-Format:** Warteschlangen sind über das folgende URL-Format adressierbar:
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
Mit der folgenden URL kann eine der Warteschlangen im Diagramm adressiert werden:  
	http://myaccount.queue.core.windows.net/imagesToDownload

-**Speicherkonto:** Alle Zugriffe auf den Azure-Speicher erfolgen über ein Speicherkonto. Weitere Informationen zur Kapazität der Speicherkonten finden Sie unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/library/azure/dn249410.aspx).

- **Warteschlange:** Eine Warteschlange enthält einen Satz Nachrichten. Alle Nachrichten müssen in einer Warteschlange sein.

- **Nachricht:** Eine Nachricht in einem beliebigen Format und mit einer Größe von bis zu 64 KB.



<!--HONumber=49-->