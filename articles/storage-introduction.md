<properties linkid="storage-introduction" urlDisplayName="Introduction to Azure Storage" pageTitle="Introduction to Storage | Microsoft Azure" metaKeywords="Get started  Azure storage introduction  Azure storage overview  Azure blob   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage  Azure queue   Azure asynchronous processing   Azure queue   Azure queue storage Azure table   Azure nosql   Azure large structured data store   Azure table   Azure table storage  Azure file storage  Azure file  Azure file share  Azure " description="An overview of Microsoft Azure Storage." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="Introduction to Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram"></tags>

# Einführung in Microsoft Azure Storage

Dieser Artikel enthält eine Einführung in Microsoft Azure Storage für Entwickler, IT-Fachleute und geschäftliche Entscheidungsträger. In diesem Artikel lernen Sie Folgendes:

-   Was Azure Storage ist und wie Sie in Ihren Cloud-, Mobilgerät-, Server- und Desktop-Anwendungen davon profitieren können
-   Welche Arten von Daten Sie mithilfe folgender Azure Storage-Dienste speichern können: Blob-, Tabellen-, Warteschlangen- und Dateispeicher
-   Wie der Zugriff auf Ihre Daten in Azure Storage verwaltet wird
-   Wie Ihre Azure Storage-Daten durch Redundanz und Replikation gesichert werden
-   Welche Schritte erforderlich ist, um Ihre erste Azure Storage-Anwendung zu erstellen

## Was ist Azure Storage?

Cloud Computing ermöglicht neue Szenarien für Anwendungen, die skalierbaren, haltbaren und hochverfügbaren Speicher für ihre Daten benötigen. Genau aus diesem Grund hat Microsoft den Azure-Speicher entwickelt. Mit dem Azure-Speicher können Entwickler umfangreiche Anwendungen für neue Szenarien entwickeln. Außerdem dient der Speicher als Grundlage für virtuelle Azure-Computer, ein weiteres Zeugnis für dessen Robustheit.

Azure Storage ist in hohem Maße skalierbar. Sie können also Hunderte Terabytes von Daten speichern und verarbeiten, um die "Big Data"-Szenarios zu verwirklichen, die für Anwendungen in der Wissenschaft, in der Finanzbranche und in der Medienwelt erforderlich sind. Oder Sie können geringe Datenmengen einer kleinen Geschäftswebsite speichern. Sie bezahlen in jedem Fall nur für die Daten, die Sie tatsächlich gespeichert haben. Azure Storage speichert aktuell Billionen einzigartiger Kundenobjekte und bearbeitet durchschnittlich mehrere Millionen Anfragen pro Sekunde.

Der Azure-Speicher ist elastisch. Sie können Anwendungen für ein breites globales Publikum entwickeln und anschließend nach Bedarf skalieren - sowohl im Hinblick auf die gespeicherten Daten als auch auf die gestellten Abfragen. Sie zahlen nur für das, was Sie verwenden, und nur dann, wenn Sie es verwenden.

Azure Storage verwendet ein automatisches Partitionierungssystem, das einen automatischen Lastenausgleich Ihrer Daten auf Grundlage des Datenverkehrs durchführt. Dies bedeutet, dass Azure Storage immer automatisch die nötigen Ressourcen zuordnet, um die wachsenden Anforderungen Ihrer Anwendung zu erfüllen.

Der Azure-Speicher ist weltweit und von jeder Art von Anwendung erreichbar, egal ob diese in der Cloud, auf dem Desktop, auf einem lokalen Server oder einem mobilen oder Tabletgerät läuft. Sie können Azure Storage in mobilen Szenarios verwenden, in denen eine Anwendung eine Datenteilmenge auf einem Gerät speichert und sie mit der vollständigen Datenmenge in der Cloud synchronisiert.

Azure Storage unterstützt Kunden mit verschiedenen Betriebssystemen (z. B. Windows und Linux) und einer Vielzahl von Programmiersprachen (darunter .NET, Java und C++) für eine problemlose Entwicklung. Azure Storage macht auch Datenressourcen über einfache REST-APIs verfügbar, die auf jedem Client ausgeführt werden können, der Daten über HTTP/HTTPS senden und empfangen kann.

## Anwenden der Azure Storage-Dienste

Die Azure-Speicher-Dienste sind Blob-Speicher, Tabellenspeicher, Warteschlangenspeicher und Dateispeicher:

-   Der **Blob-Speicher** speichert Dateiendaten. Ein Blob kann jede Art von Text- oder binären Daten sein, z. B. ein Dokument, eine Mediendatei oder ein Anwendungs-Installer.
-   Der **Tabellenspeicher** speichert strukturierte Datensätze. Der Tabellenspeicher ist ein NoSQL-Schlüssel-/Attribut-Datenspeicher, der eine rasche Entwicklung und schnellen Zugriff auf große Datenmengen erlaubt.
-   Der **Warteschlangenspeicher** bietet eine zuverlässige Nachrichtenfunktion zur Workflow-Verarbeitung und zur Kommunikation zwischen Komponenten von Clouddiensten.
-   Der **Dateispeicher** bietet einen gemeinsam genutzten Speicher für ältere Anwendungen und verwendet dabei das SMB 2.1-Protokoll. Virtuelle Azure-Computer und Cloud-Dienste können Dateidaten in verschiedenen Anwendungskomponenten über eingebundene Freigaben teilen, und lokale Anwendungen können über die REST-API des Dateidiensts auf freigegebene Dateien zugreifen.

Blob-, Tabellen- und Warteschlangenspeicher sind in jedem Speicherkonto enthalten, während der Dateispeicher nur auf Anfrage in der [Azure-Vorschauseite][] verfügbar ist.

Das Speicherkonto ist ein eindeutiger Namespace, mit dem Sie Zugriff auf den Azure-Speicher erhalten. Jedes Speicherkonto kann bis zu 500 TB an kombinierten Blob-, Warteschlangen-, Tabellen- und Dateidaten enthalten. In den [Skalierbarkeits- und Leistungszielen für Azure-Speicher][] finden Sie weitere Details zur Kapazität des Azure-Speichers.

Die folgende Abbildung zeigt die Beziehungen zwischen Azure-Speicherressourcen:

![Azure-Speicherressourcen][]

Um ein Speicherkonto erstellen zu können, müssen Sie über ein Azure-Abonnement verfügen. Dies ist ein Plan, der Ihnen Zugriff auf eine Vielzahl von Azure-Diensten verschafft. Sie können bis zu 20 eindeutig benannte Speicherkonten unter einem einzigen Abonnement erstellen.

Sie können die Verwendung von Azure mit einem [kostenlosen Testkonto][] starten. Wenn Sie sich entscheiden, einen Plan zu kaufen, können Sie aus einer Vielzahl von [Kaufoptionen][] auswählen. [MSDN-Abonnenten][] erhalten ein monatliches Gratis-Guthaben für Azure-Dienste, inklusive Azure-Speicher.

## Blob-Speicher

Für Benutzer mit großen Mengen unstrukturierter Daten, die in einer Cloud gespeichert werden sollen, bietet der Blob-Speicher eine kostengünstige und skalierbare Lösung. Der Blob-Speicher dient zur Speicherung der folgenden Inhalte:

-   Dokumente
-   Daten aus sozialen Netzwerken wie Fotos, Videos, Musik und Blogs
-   Sicherungskopien von Dateien, Computern, Datenbanken und Geräten
-   Bilder und Texte für Webanwendungen
-   Konfigurationsdaten für Cloudanwendungen
-   Große Datenmengen wie Protokolle und andere große Datensätze

Jeder Blob ist in einem Container organisiert. Container bieten auch eine praktische Methode, um Gruppen von Objekten Sicherheitsrichtlinien zuzuordnen. Ein Speicherkonto kann eine beliebige Anzahl von Containern enthalten, und ein Container kann eine beliebige Anzahl von Blobs enthalten. Die Speicherkapazität eines Speicherkontos beträgt 500 TB.

Der Blob-Speicher bietet zwei Arten von Blobs: Block-Blobs und Seiten-Blobs (Festplatten). Block-Blobs sind für das Streaming und die Speicherung von Cloudobjekten optimiert und stellen eine gute Wahl zur Speicherung von Dokumenten, Mediendateien, Sicherungskopien usw. dar. Ein Block-Blob kann bis zu 200 GB groß sein. Seiten-Blobs sind für die Darstellung von IaaS-Festplatten und für die Unterstützung von Schreibzugriffen optimiert und können bis zu 1 TB groß sein. Eine an ein Netzwerk angehängte IaaS-Festplatte auf einem virtuellen Azure-Computer ist eine als Seiten-Blob gespeicherte virtuelle Festplatte.

Für sehr große Datensätze, bei denen Netzwerkbeschränkungen das Hochladen oder Herunterladen von Daten über eine Kabelverbindung erschweren, können Sie eine Festplatte an Microsoft schicken, um Ihre Daten mithilfe des [Azure-Import-/Export-Diensts][] direkt aus dem oder in das Rechenzentrum importieren bzw. exportieren lassen zu können. Sie können auch Blob-Daten innerhalb Ihres Speicherkontos oder zwischen Speicherkonten kopieren.

## Table Storage

Moderne Anwendungen erfordern oft Datenspeicher mit höherer Skalierbarkeit und Flexibilität als in früheren Software-Generationen. Der Tabellenspeicher bietet eine hoch verfügbare, in hohem Maße skalierbare Speicherung, so dass Ihre Anwendung automatisch an die Bedürfnisse der Benutzer angepasst werden kann. Der Tabellenspeicher ist ein NoSQL-Speicher im Schlüssel-/Attributformat von Microsoft und unterscheidet sich durch das schemalose Design von herkömmlichen relationalen Datenbanken. Mit einer schemalosen Datenspeicherung ist es einfach, Ihre Daten an die Entwicklung Ihrer Anwendungen anzupassen. Der Tabellenspeicher ist so benutzerfreundlich, dass Entwickler problemlos Anwendungen erstellen können. Der Datenzugriff ist für alle Arten von Anwendungen schnell und kostengünstig. Der Tabellenspeicher ist deutlich kostengünstiger als herkömmliches SQL für ähnliche Datenmengen.

Der Tabellenspeicher verwendet ein Schlüssel-/Attributformat, d. h. jeder Wert in einer Tabelle wird mit einem typisierten Eigenschaftennamen gespeichert. Der Eigenschaftenname kann für Filter und Auswahlkriterien verwendet werden. Eine Auflistung von Eigenschaften und deren Werten bilden eine Entität. Da der Tabellenspeicher schemalos ist, können zwei Entitäten in derselben Tabelle unterschiedliche Sammlungen von Eigenschaften enthalten, und diese Eigenschaften können unterschiedliche Typen haben.

Sie können den Tabellenspeicher zur Speicherung von flexiblen Datensätzen wie Benutzerdaten für Webanwendungen, Adressbüchern, Geräteinformationen und jeder Art von Metadaten verwenden, die Ihr Dienst erfordert. Sie können eine beliebige Anzahl von Entitäten in einer Tabelle speichern, und ein Speicherkonto kann eine beliebige Anzahl von Tabellen enthalten. Die Speicherkapazität eines Speicherkontos beträgt 200 TB.

Wie bei Blobs und Warteschlangen können Entwickler den Tabellenspeicher mit Standard-REST-Protokollen aufrufen und verwalten. Der Tabellenspeicher unterstützt allerdings auch eine Untergruppe des OData-Protokolls. Dies vereinfacht erweiterte Abfragefunktionen und ermöglicht die Arbeit mit den Formaten JSON und AtomPub (XML-basiert).

NoSQL-Datenbanken wie der Tabellenspeicher bieten für heutige internetbasierte Anwendungen eine beliebte Alternative zu herkömmlichen relationalen Datenbanken.

## Warteschlangenspeicher

Bei der Entwicklung von Anwendungen für Skalierungszwecke werden einzelne Anwendungskomponenten oft entkoppelt, damit sie unabhängig skaliert werden können. Der Warteschlangenspeicher bietet eine zuverlässige Messaginglösung für die asynchrone Kommunikation zwischen Anwendungskomponenten, egal ob diese in der Cloud, auf dem Desktop, auf einem lokalen Server oder einem mobilen Gerät ausgeführt werden. Der Warteschlangenspeicher unterstützt auch die Verwaltung asynchroner Aufgaben und den Aufbau von Prozessworkflows.

Ein Speicherkonto kann eine beliebige Anzahl von Warteschlangen enthalten. Eine Warteschlange kann eine beliebige Anzahl von Nachrichten enthalten, und das entsprechende Speicherkonto hat eine Kapazität von 200 TB. Einzelne Nachrichten können bis zu 64 KB groß sein.

## Dateispeicher

Viele ältere Anwendungen verwenden Dateifreigaben. Diese Abhängigkeit erschwert den Umzug dieser Anwendungen in die Cloud. Der Dateispeicher bietet cloudbasierte Dateifreigaben, mit denen Sie ältere Anwendungen schnell und ohne kostspielige Neuentwicklungen nach Azure umziehen können.

Anwendungen in virtuellen Azure-Computern oder Cloud-Diensten können eine Dateispeicher-Freigabe einbinden, um auf Dateidaten zuzugreifen, ebenso wie eine Desktopanwendung eine typische SMB-Freigabe einbinden würde. Die Dateispeicher-Freigaben können von beliebig vielen Anwendungskomponenten gleichzeitig eingebunden und abgerufen werden.

Da es sich bei Dateispeicher-Freigaben um gewöhnliche SMB 2.1-Freigaben handelt, können Anwendungen in Azure über die E/A-System-APIs auf die Freigaben zugreifen. Entwickler können daher ihren vorhandenen Code und bereits erlernte Fertigkeiten für die Migration vorhandener Anwendungen verwenden. IT-Fachkräfte können PowerShell-Cmdlets verwenden, um Dateispeicher-Freigaben im Rahmen der Administration von Azure-Anwendungen zu erstellen, einzubinden und zu verwalten.

Wie auch die anderen Azure-Speicherdienste stellt der Dateispeicher eine REST-API für den Zugriff auf Daten in Freigaben bereit. Lokale Anwendungen können die REST-API des Dateispeichers aufrufen, um auf Daten in Dateifreigaben zuzugreifen. Auf diese Weise können Unternehmen einen Teil der älteren Anwendungen nach Azure migrieren und andere Anwendungen weiterhin intern ausführen. Die Einbindung von Dateifreigaben ist nur für Anwendungen möglich, die in Azure ausgeführt werden. Lokale Anwendungen müssen über die REST-API auf die Dateifreigaben zugreifen.

Verteilte Anwendungen können den Dateispeicher für die Speicherung und Weitergabe von Anwendungsdaten sowie für Entwicklungs- und Testtools verwenden. Eine Anwendung kann z. B. Konfigurationsdateien und Diagnosedaten wie Protokolle, Metriken und Absturzabbilder in einer Dateispeicher-Freigabe ablegen, um diese Daten anderen virtuellen Computern oder Rollen zur Verfügung zu stellen. Entwickler und Administratoren können Hilfsprogramme für die Erstellung oder Verwaltung einer Anwendung in einer Dateispeicher-Freigabe ablegen, die für alle Komponenten verfügbar ist, anstatt diese Hilfsprogramme auf allen virtuellen Computern oder Rolleninstanzen einzeln zu installieren.

## Zugriff auf Blob-, Tabellen-, Warteschlangen- und Dateispeicherressourcen

Standardmäßig kann nur der Besitzer eines Speicherkontos auf Ressourcen in diesem Konto zugreifen. Für die Sicherheit Ihrer Daten muss jede Abfrage über Ressourcen in Ihrem Konto authentifiziert werden. Die Authentifizierung beruht auf einem Modell mit gemeinsam verwendeten Schlüsseln. Blobs können auch für die Unterstützung anonymer Authentifizierungen konfiguriert werden.

Ihr Speicherkonto wird bei der Erstellung zwei privaten Zugangsschlüsseln zugeordnet, die zur Authentifizierung verwendet werden. Die Verwendung von zwei Schlüsseln stellt sicher, dass Ihre Anwendung auch dann verfügbar bleibt, wenn Sie die Schlüssel im Rahmen der regelmäßigen Sicherheitsverwaltung regelmäßig erneuern.

Wenn Sie anderen Benutzern kontrollierten Zugriff zu Ihren Speicherressourcen gewähren müssen, können Sie eine [Shared Access Signature][] erstellen. Eine Shared Access Signature ist ein Token, das an eine URL angehängt werden kann und delegierten Zugriff auf einen Container, einen Blob, eine Tabelle oder eine Warteschlange erlaubt. Jeder Benutzer, der dieses Token besitzt, kann mit festgelegten Berechtigungen und während der Gültigkeitsdauer auf die Ressource zugreifen, auf die das Token verweist. Beachten Sie, dass der Azure-Dateispeicher momentan keine Shared Access Signatures unterstützt.

Abschließend können Sie noch einen Container und seine Blobs – oder einen speziellem Blob – öffentlich verfügbar machen. Wenn Sie einen Container oder einen Blob zu einer öffentlichen Ressource erklären, kann jeder Benutzer anonym drauf zugreifen, ohne eine Authentifizierung zu benötigen. Öffentliche Container und Blobs sind hilfreich zur Verfügbarmachung von Ressourcen wie Medien und Dokumenten, die auf Websites gehostet werden. Um die Netzwerklatenz für ein globales Publikum zu reduzieren, können Sie Blob-Daten, die von Websites verwendet werden, in einem Cache zwischenspeichern.

## Replikation für Dauerhaftigkeit und hohe Verfügbarkeit

[WACOM.INCLUDE [storage-replication-options][]]

## Preise

Die Verwendung von Azure Storage wird dem Kunden auf Grundlage von vier Faktoren berechnet: der verwendeten Speicherkapazität, der ausgewählten Replikationsoption, der Anzahl der Abfragen an den Dienst und den Datenausgang.

Speicherkapazität bezieht sich darauf, wie viel von Ihrer Speicherkontozuweisung zum Speichern von Daten verwendet wird. Die Kosten des einfachen Speicherns von Daten wird dadurch bestimmt, wie viele Daten Sie speichern und wie diese Daten repliziert werden. Für jede Lese- und Schreiboperation auf dem Azure-Speicher wird außerdem eine Anfrage an den Dienst ausgeführt. Datenausgang bezieht sich auf die Daten, die aus einer Windows Azure-Region heraus übertragen werden. Wenn eine Anwendung, die nicht in der gleichen Region ausgeführt wird und entweder ein Clouddienst oder ein anderer Anwendungstyp ist, auf die Daten in Ihrem Speicherkonto zugreift, fallen Gebühren für den Datenausgang an. (Für Azure-Dienste können Sie Maßnahmen ergreifen, um Ihre Daten und Dienste in den gleichen Rechenzentren zu gruppieren und auf diese Weise Prozess- und Datenausgangsgebühren zu senken oder komplett einzusparen.)

Die Seite mit den [Speicherpreisdetails][] bietet detaillierte Preisinformationen für Speicherkapazität, Replikation und Transaktionen. In den [Datenübertragungs-Preisdetails][] finden Sie detaillierte Preisinformationen für den Datenausgang. Sie können den [Azure Storage-Preisrechner][] verwenden, um Ihre Kosten zu bestimmen.

## Entwicklung bei der Speicherung

Azure Storage stellt Speicherressourcen über eine [REST-API][] zur Verfügung, die in jeder Sprache aufgerufen werden kann, mit der sich HTTP/HTTPS-Anfragen durchführen lassen. Zusätzlich bietet Azure Storage Programmierbibliotheken für einige beliebte Sprachen. Diese Bibliotheken vereinfachen viele Aspekte der Arbeit mit Azure Storage durch Verarbeitung von Details wie synchronen und asynchronen Aufrufen, der Zusammenfassung von Vorgängen, Ausnahmeverwaltung, automatischen Neuversuchen, Betriebsverhalten usw. Bibliotheken sind aktuell für die folgenden Sprachen und Plattformen erhältlich. Weitere sind in Planung.

-   [.NET][]
-   [Nativer Code][]
-   [Java/Android][]
-   [Node.js][]
-   [PHP][]
-   [Ruby][]
-   [Python][]
-   [PowerShell][]

## Nächste Schritte

Informationen zu den ersten Schritten mit Azure Storage finden Sie in folgenden Ressourcen:

-   [Azure Storage-Dokumentation][]
-   [Skalierbarkeits- und Leistungsziele für Azure Storage][Skalierbarkeits- und Leistungszielen für Azure-Speicher]

### Für .NET-Entwickler

-   [Verwenden des Blob-Speichers mit .NET][]
-   [Verwenden des Tabellenspeichers mit .NET][]
-   [Verwenden des Warteschlangenspeichers mit .NET][]

### Für Java-/Android-Entwickler

-   [Verwenden des Blob-Speichers in Java/Android][]
-   [Verwenden des Tabellenspeichers in Java/Android][]
-   [Verwenden des Warteschlangenspeichers in Java/Android][]

### Für Node.js-Entwickler

-   [Verwenden des Blob-Dienstes aus Node.js][]
-   [Verwenden des Tabellenspeicherdiensts aus Node.js][]
-   [Verwenden des Warteschlangendiensts aus Node.js][]

### Für PHP-Entwickler

-   [Verwenden des Blob-Diensts aus PHP][]
-   [Verwenden des Tabellendiensts von PHP][]
-   [Verwenden des Warteschlangendienstes aus PHP][]

### Für Ruby-Entwickler

-   [Verwenden des Blob-Diensts von Ruby][]
-   [Verwenden des Tabellenspeicherdiensts von Ruby][]
-   [Verwenden des Warteschlangenspeicherdiensts aus Ruby][]

### Für Python-Entwickler

-   [Verwenden des Blob-Speicherdiensts aus Python][]
-   [Verwenden des Tabellenspeicherdiensts aus Python][]
-   [Verwenden des Warteschlangenspeicherdiensts aus Python][]

  [Azure-Vorschauseite]: /en-us/services/preview/
  [Skalierbarkeits- und Leistungszielen für Azure-Speicher]: http://msdn.microsoft.com/library/windowsazure/dn249410.aspx
  [Azure-Speicherressourcen]: ./media/storage-introduction/storage-concepts.png
  [kostenlosen Testkonto]: /en-us/pricing/free-trial/
  [Kaufoptionen]: /en-us/pricing/purchase-options/
  [MSDN-Abonnenten]: /en-us/pricing/member-offers/msdn-benefits-details/
  [Azure-Import-/Export-Diensts]: http://azure.microsoft.com/en-us/documentation/articles/storage-import-export-service/
  [Shared Access Signature]: ../storage-dotnet-shared-access-signature-part-1/
  [storage-replication-options]: ../includes/storage-replication-options.md
  [Speicherpreisdetails]: /en-us/pricing/details/storage/
  [Datenübertragungs-Preisdetails]: /en-us/pricing/details/data-transfers/
  [Azure Storage-Preisrechner]: /en-us/pricing/calculator/?scenario=data-management
  [REST-API]: http://msdn.microsoft.com/library/windowsazure/dd179355.aspx
  [.NET]: http://msdn.microsoft.com/library/dn495001.aspx
  [Nativer Code]: http://msdn.microsoft.com/library/dn495438.aspx
  [Java/Android]: /en-us/develop/java/
  [Node.js]: /en-us/develop/nodejs/
  [PHP]: /en-us/develop/php/
  [Ruby]: /en-us/develop/ruby/
  [Python]: /en-us/develop/python/
  [PowerShell]: http://msdn.microsoft.com/library/dn495240.aspx
  [Azure Storage-Dokumentation]: /en-us/documentation/services/storage/
  [Verwenden des Blob-Speichers mit .NET]: ../storage-dotnet-how-to-use-blobs/
  [Verwenden des Tabellenspeichers mit .NET]: ../storage-dotnet-how-to-use-tables/
  [Verwenden des Warteschlangenspeichers mit .NET]: ../storage-dotnet-how-to-use-queues/
  [Verwenden des Blob-Speichers in Java/Android]: ../storage-java-how-to-use-blob-storage/
  [Verwenden des Tabellenspeichers in Java/Android]: ../storage-java-how-to-use-table-storage/
  [Verwenden des Warteschlangenspeichers in Java/Android]: ../storage-java-how-to-use-queue-storage/
  [Verwenden des Blob-Dienstes aus Node.js]: ../storage-nodejs-how-to-use-blob-storage/
  [Verwenden des Tabellenspeicherdiensts aus Node.js]: ../storage-nodejs-how-to-use-table-storage/
  [Verwenden des Warteschlangendiensts aus Node.js]: ../storage-nodejs-how-to-use-queues/
  [Verwenden des Blob-Diensts aus PHP]: ../storage-php-how-to-use-blobs/
  [Verwenden des Tabellendiensts von PHP]: ../storage-php-how-to-use-table-storage/
  [Verwenden des Warteschlangendienstes aus PHP]: ../storage-php-how-to-use-queues/
  [Verwenden des Blob-Diensts von Ruby]: ../storage-ruby-how-to-use-blob-storage/
  [Verwenden des Tabellenspeicherdiensts von Ruby]: ../storage-ruby-how-to-use-table-storage/
  [Verwenden des Warteschlangenspeicherdiensts aus Ruby]: ../storage-ruby-how-to-use-queue-storage/
  [Verwenden des Blob-Speicherdiensts aus Python]: ../storage-python-how-to-use-blob-storage/
  [Verwenden des Tabellenspeicherdiensts aus Python]: ../storage-python-how-to-use-table-storage/
  [Verwenden des Warteschlangenspeicherdiensts aus Python]: ../storage-python-how-to-use-queue-storage/
