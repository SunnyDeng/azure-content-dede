## <a name="what-is"> </a>Was ist Warteschlangenspeicherung?

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Ein Speicherkonto kann bis zu 200 TB Blob-, Warteschlangen- und Tabellendaten enthalten. Unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/de-de/library/dn249410.aspx) finden Sie Details zur Speicherkontokapazität.

Warteschlangenspeicherungen werden hauptsächlich für folgende Zwecke verwendet:

-   <span>Erstellung eines Arbeits-Backlogs zur asynchronen Verarbeitung</span>
-   Weiterleitung von Nachrichten von einer Azure-Webrolle an eine Azure-
    Workerrolle

## <a name="concepts"></a>Konzepte

Der Warteschlangendienst umfasst die folgenden Komponenten:

![Queue1](./media/howto-queue-storage/queue1.png)


- **URL-Format:** Warteschlangen sind über das folgende URL-Format adressierbar:
	http://`<storage account>`.queue.core.windows.net/`<queue>` 
      
Mit der folgenden URL kann eine der Warteschlangen im Diagramm adressiert werden:  
	http://myaccount.queue.core.windows.net/imagesToDownload 

-**Speicherkonto:** Alle Zugriffe auf Azure Storage erfolgen über ein Speicherkonto. Unter [Ziele für Skalierbarkeit und Leistung des Azure-Speichers](http://msdn.microsoft.com/de-de/library/dn249410.aspx)finden Sie Details zur Speicherkontokapazität.

- **Warteschlange:** Eine Warteschlange enthält einen Satz Nachrichten. Alle Nachrichten müssen in einer Warteschlange sein.

- **Nachricht:** Eine Nachricht in einem beliebigen Format und mit einer Größe von bis zu 64 KB.


<!--HONumber=42-->
