<properties
	pageTitle="Synchronisierung von Offlinedaten in Azure Mobile Apps | Microsoft Azure"
	description="Eine grundlegende Übersicht über das Feature zur Synchronisierung von Offlinedaten für Azure Mobile Apps"
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="wesmc"/>

# Synchronisierung von Offlinedaten in Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Was ist die Synchronisierung von Offlinedateng?

Die Synchronisierung von Offlinedaten ist ein Client- und SDK-Feature von Azure Mobile Apps, die Entwicklern das Erstellen von Apps erleichtert, die ohne Netzwerkverbindung funktionsfähig sind.

Wenn Ihre App im Offlinemodus ist, können Benutzer weiterhin Daten erstellen und ändern. Diese Daten werden in einem lokalen Speicher abgelegt. Wenn die App wieder online ist, können sie lokale Änderungen mit dem Azure Mobile App-Back-End synchronisieren. Das Feature unterstützt auch das Erkennen von Konflikten, wenn auf dem Client und dem Back-End der gleiche Datensatz geändert wird. Konflikte können dann entweder auf dem Server oder vom Client verarbeitet werden.

Die Offlinesynchronisierung bietet eine Reihe an Vorteilen:

* Verbesserte Reaktionsfähigkeit der App durch das lokale Caching von Serverdaten auf dem Gerät
* Erstellen stabiler Apps, die auch bei Netzwerkproblemen funktionstüchtig bleiben
* Endanwender können Daten selbst dann erstellen und ändern, wenn sie keinen Netzwerkzugriff haben, also in Szenarien mit wenig oder keiner Konnektivität
* Daten auf mehreren Geräten synchronisieren und Konflikte erkennen, wenn derselbe Datensatz von zwei Geräte aus geändert wird
* Das Beschränken der Netzwerknutzung bei Netzwerken mit hoher Latenz oder getakteten Netzwerken

Die folgenden Lernprogramme zeigen, wie Sie die Offlinesynchronisierung zu Ihren mobilen Clients mithilfe von Azure Mobile Apps hinzufügen können:

* [iOS: Offlinesynchronisierung aktivieren]			
* [Xamarin iOS: Offlinesynchronisierung aktivieren]	
* [Xamarin Android: Offlinesynchronisierung aktivieren]
* [Windows 8.1: Offlinesynchronisierung aktivieren]	

## Was ist eine Synchronisierungstabelle?

Um auf den "/tables"-Endpunkt zuzugreifen, stellen Azure Mobile-Client-SDKs Schnittstellen bereit, z. B. `IMobileServiceTable` (.NET-Client-SDK) oder `MSTable` (iOS-Client). Diese APIs stellen eine direkte Verbindung zum Azure Mobile App-Back-End her und schlagen fehl, wenn das Clientgerät keine Netzwerkverbindung hat.

Zur Unterstützung der Offlinenutzung sollte Ihre App stattdessen die *Synchronisierungstabellen*-APIs verwenden, z. B. `IMobileServiceSyncTable` (.NET-Client-SDK) oder `MSSyncTable` (iOS-Client). Die gleichen CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren, Löschen) arbeiten gegen Synchronisierungstabellen-APIs, mit dem Unterschied, dass sie jetzt einen *lokalen Speicher* lesen oder in diesen schreiben. Bevor Synchronisierungtabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden.

## Was ist ein lokaler Speicher?

Ein lokaler Speicher ist die Datenpersistenzebene auf dem Clientgerät. Die Client-SDKs von Azure Mobile Apps bieten eine Standardimplementierung für den lokalen Speicher. Unter Windows, Xamarin und Android basieren sie auf SQLite, unter iOS auf Core Data.

Um die SQLite-basierte Implementierung für Windows Phone oder Windows Store 8.1 verwenden zu können, müssen Sie eine SQLite-Erweiterung installieren. Weitere Informationen finden Sie unter [Windows 8.1: Aktivieren der Offline-Synchronisierung]. Im Lieferumfang von Android und iOS ist im Betriebssystem selbst eine Version von SQLite enthalten, weshalb es nicht notwendig ist, auf eine eigene Version von SQLite zu verweisen.

Entwickler können auch ihren eigenen lokalen Speicher implementieren. Wenn Sie Daten in einem verschlüsselten Format auf dem mobilen Client speichern möchten, können Sie z. B. einen lokalen Speicher definieren, der SQLCipher für die Verschlüsselung verwendet.

## Was ist ein Synchronisierungskontext?

Ein *Synchronisierungskontext* ist einem mobilen Client-Objekt zugeordnet (z. B. `IMobileServiceClient` oder `MSClient`) und verfolgt die Änderungen, die mithilfe von Synchronisierungstabellen vorgenommen werden. Der Synchronisierungskontext verwaltet eine *Vorgangswarteschlange* mit einer geordneten Liste an CUD-Vorgängen (Erstellen, Aktualisieren und Löschen), die später an den Server gesendet wird.

Ein lokaler Speicher wird mithilfe einer Initialize-Methode wie `IMobileServicesSyncContext.InitializeAsync(localstore)` im .NET-Client-SDK dem Synchronisierungskontext zugeordnet.

<!-- TODO: link to client references -->


<!-- 
Client code will interact with the table using the `IMobileServiceSyncTable` interface to support offline buffering. This interface supports all the methods of `IMobileServiceTable` along with additional support for pulling data from a Mobile App backend table and merging it into a local store table. How the local table is synchronized with the backend database is mainly controlled by your logic in the client app.

The sync table uses the [System Properties](https://msdn.microsoft.com/library/azure/dn518225.aspx) on the table to implement change tracking for offline synchronization. 



* The data objects on the client should have some system properties, most are not required.
	* Managed
		* Write out the attributes
	* iOS
		*table for the entity
* Note: because the iOS local store is based on Core Data, the developer must define the following tables:
	* System tables  -->


## So funktioniert die Offlinesynchronisierung

Beim Synchronisieren von Tabellen steuert der Clientcode, wann lokale Änderungen mit einem Azure Mobile App-Back-End synchronisiert werden sollen. Nichts wird an das Back-End gesendet, bis ein Aufruf für einen *Pushvorgang* für lokale Änderungen erfolgt. Auf ähnliche Weise wird der lokale Speicher nur dann mit neuen Daten aufgefüllt, wenn ein Aufruf für einen *Pullvorgang* von Daten erfolgt.

* **Pushvorgang**: Push ist ein Vorgang auf dem Synchronisierungskontext, der alle CUD-Änderungen seit dem letzten Pushvorgang sendet. Beachten Sie, dass es nicht möglich ist, nur die Änderungen einer einzelnen Tabelle zu senden, da andernfalls Vorgänge außerhalb der Reihenfolge gesendet werden könnten. Ein Pushvorgang führt eine Reihe von REST-Aufrufen an Ihr Azure Mobile App-Back-End durch, das wiederum die Serverdatenbank ändert.

* **Pullvorgänge**: Pullvorgänge erfolgen pro Tabelle und können mit einer Abfrage so angepasst werden, dass nur eine Teilmenge der Serverdaten abgerufen wird. Die Azure Mobile-Client-SDKs legen dann die resultierenden Daten im lokalen Speicher ab.

* **Implizite Pushvorgänge**: Wenn ein Pullvorgang gegen eine Tabelle mit ausstehenden lokalen Updates ausgeführt wird, führt der Pullvorgang zunächst einen Pushvorgang im Synchronisierungskontext aus. Auf diese Weise minimieren Sie Konflikte zwischen Änderungen, die sich bereits in der Warteschlange befinden, und den neuen Daten vom Server.

* **Inkrementelle Synchronisierung**: der erste Parameter für den Pullvorgang ist ein *Abfragename*, der nur auf dem Client verwendet wird. Wenn Sie eine nicht-Null-Abfrage verwenden, führt das Azure Mobile-SDK eine *inkrementelle Synchronisierung* durch. Jedes Mal, wenn ein Pullvorgang einen Satz von Ergebnissen zurückgibt, wird der neueste `__updatedAt`-Zeitstempel aus der Ergebnismenge in den lokalen SDK-Systemtabellen gespeichert. Nachfolgende Pullvorgänge rufen nur Datensätze nach dem jeweiligen Zeitstempel ab.

  Um die inkrementelle Synchronisierung verwenden zu können, muss Ihr Server sinnvolle `__updatedAt`-Werte zurückgeben und das Sortieren nach diesem Feld unterstützen. Da das SDK jedoch eine eigene Sortierung auf das Feld "UpdatedAt" hinzufügt, können Sie keine Pullabfrage nutzen, die über eine eigene `$orderBy$`-Klausel verfügt.

  Der Name der Abfrage kann eine beliebige Zeichenfolge sein, muss aber für jede logische Abfrage in Ihrer App eindeutig sein. Andernfalls könnten unterschiedliche Pullvorgänge den gleichen Zeitstempel für die inkrementelle Synchronisierung überschreiben, sodass Ihre Abfragen falsche Ergebnisse zurückgeben.

  Wenn die Abfrage einen Parameter aufweist, ist das Integrieren des Parameterwerts eine Möglichkeit zum Erstellen eindeutiger Abfragenamen. Wenn Sie beispielsweise nach der Benutzer-ID filtern, könnte Ihr eindeutiger Abfragename so aussehen:

		await todoTable.PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

  Wenn Sie die inkrementelle Synchronisierung deaktivieren möchten, übergeben Sie `null` als Abfrage-ID. In diesem Fall werden alle Datensätze bei jedem Aufruf von `PullAsync` abgerufen, was möglicherweise ineffizient ist.

 

<!--   mymobileservice-code.azurewebsites.net/tables/TodoItem?$filter=(__updatedAt ge datetimeoffset'1970-01-01T00:00:00.0000000%2B00:00')&$orderby=__updatedAt&$skip=0&$top=50&__includeDeleted=true&__systemproperties=__updatedAt%2C__deleted
 -->
* **Löschen**: Sie können den Inhalt des lokalen Speichers mit `IMobileServiceSyncTable.PurgeAsync` löschen. Dies kann erforderlich sein, wenn Sie veraltete Daten in der Clientdatenbank haben, oder wenn Sie alle ausstehende Änderungen verwerfen möchten.

  Eine Aufräumaktion löscht eine Tabelle aus dem lokalen Speicher. Wenn es Vorgänge der ausstehenden Synchronisierung mit der Datenbank gibt, löst das Löschen eine Ausnahme aus, sofern nicht der Parameter *Löschen erzwingen* festgelegt ist.

  Als Beispiel für veraltete Daten auf dem Client nehmen wir an, dass im Beispiel "todo list" Gerät 1 nur Elemente abruft, die nicht abgeschlossen sind. Das todo-Element "Milch kaufen" wird dann auf dem Server von einem anderen Gerät als abgeschlossen markiert. Allerdings verfügt Gerät 1 weiterhin über das todo-Element "Milch kaufen" im lokalen Speicher, da es nur Elemente abruft, die nicht als abgeschlossen markiert sind. Eine Aufräumaktion löscht dieses veraltete Element.
 
## Nächste Schritte

* [iOS: Offlinesynchronisierung aktivieren]			
* [Xamarin iOS: Offlinesynchronisierung aktivieren]	
* [Xamarin Android: Offlinesynchronisierung aktivieren]
* [Windows 8.1: Offlinesynchronisierung aktivieren]	

<!-- Links -->

[iOS: Offlinesynchronisierung aktivieren]: ../app-service-mobile-ios-get-started-offline-data.md
[Xamarin iOS: Offlinesynchronisierung aktivieren]: ../app-service-mobile-xamarin-ios-get-started-offline-data.md
[Xamarin Android: Offlinesynchronisierung aktivieren]: ../app-service-mobile-xamarin-ios-get-started-offline-data.md
[Windows 8.1: Aktivieren der Offline-Synchronisierung]: ../app-service-mobile-windows-store-dotnet-get-started-offline-data.md
[Windows 8.1: Offlinesynchronisierung aktivieren]: ../app-service-mobile-windows-store-dotnet-get-started-offline-data.md

<!---HONumber=Nov15_HO1-->