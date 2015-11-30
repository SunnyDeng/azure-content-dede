<properties
   pageTitle="Sichern und Wiederherstellen von Reliable Services| Microsoft Azure"
   description="Dokumentation zum Sichern und Wiederherstellen von Reliable Services mit Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2015"
   ms.author="mcoskun"/>

# Sichern und Wiederherstellen von Reliable Services

Service Fabric ist eine Plattform mit hoher Verfügbarkeit und repliziert den Status über mehrere Knoten, um diese hohe Verfügbarkeit zu gewährleisten. Auch wenn ein Knoten im Cluster ausfällt, bleiben die Dienste somit verfügbar. Diese von der Plattform bereitgestellte integrierte Redundanz reicht in manchen Fällen aus. In bestimmten Fällen wäre es jedoch wünschenswert, dass der Dienst Daten (auf einem externen Speicher) sichert.

Ein Dienst möchte Daten beispielsweise in den folgenden Szenarien sichern:

* Bei dauerhaftem Verlust eines gesamten Service Fabric-Clusters oder aller Knoten, die eine bestimmte Partition ausführen (also alle Knoten im Cluster). Dies kann beispielsweise passieren, wenn Sie keine Georeplikation verwenden, der gesamte Cluster sich in einem Rechenzentrum befindet und das gesamte Rechenzentrum ausfällt.
* Administrative Fehler, durch die der Zustand versehentlich gelöscht bzw. beschädigt wird. Das kann z. B. passieren, wenn ein Administrator mit ausreichenden Berechtigungen versehentlich den Dienst löscht.
* Fehler im Dienst, die Daten beschädigen. Dies kann beispielsweise passieren, wenn ein Dienstupgrade fehlerhafte Daten in eine Sammlung schreibt und der Dienst sowie die Daten auf einen früheren Status zurückgesetzt werden müssen.
* Offline-Datenverarbeitung. Es mag zweckmäßig sein, Daten für Business Intelligence separat von dem Dienst, der die Daten generiert, zu verarbeiten.

Das Feature für die Sicherung/Wiederherstellung ermöglicht mit Reliable Services-API entwickelten Diensten die Erstellung und Wiederherstellung von Sicherungen. Die von der Plattform bereitgestellten Sicherungs-APIs ermöglichen das Ausführen von Sicherungen eines Partitionsstatus, ohne Lese- oder Schreibvorgänge zu blockieren. Die Wiederherstellungs-APIs ermöglichen die Wiederherstellung des Status einer Partition aus einer Sicherung.



### Durchführen der Sicherung

Der Dienstautor kann bestimmen, wann Sicherungen erstellt werden und wo sie gespeichert werden.

Der Dienst muss **IReliableStateManager.BackupAsync** aufrufen, um die Sicherung zu starten. Sicherungen können nur von primären Replikaten übernommen werden und erfordern Schreibzugriff.

Wie Sie unten sehen, hat die einfachste Überladung von **BackupAsync** in Func<< BackupInfo  bool >> **backupCallback** aufgerufen.

        await this.StateManager.BackupAsync(this.BackupCallbackAsync);

**BackupInfo** gibt Informationen über die Sicherung, einschließlich des Speicherorts des Ordners, in dem die Runtime die Sicherung (BackupInfo.Directory) gespeichert hat. Die Callback-Funktion möchte BackupInfo.Directory in einen externen Speicher oder an einen anderen Speicherort verschieben. Diese Funktion gibt auch einen booleschen Wert zurück, der angibt, ob der Sicherungsordner erfolgreich an den Zielspeicherort verschoben werden konnte.

Der folgende Code zeigt, wie die Sicherung mit BackupCallback in den Azure-Speicher hochgeladen wird:

```C#
        private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
        {
            var backupId = Guid.NewGuid();

            await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

            return true;
        }
```

Im obigen Beispiel ist **ExternalBackupStore** die Beispielklasse, die als Schnittstelle zum Azure-Blobspeicher verwendet wird, und **UploadBackupFolderAsync** ist die Methode, die den Ordner komprimiert und im Azure-Blobspeicher platziert.

>[AZURE.NOTE]Pro Replikat-Inflight ist stets nur ein **BackupAsync** möglich. Mehrere **BackupAsync**-Aufrufe gleichzeitig lösen **FabricBackupInProgressException** aus, um nur eine Inflight-Sicherung zuzulassen.[AZURE.NOTE]Wenn ein Replikat während einer Sicherung ausfällt, wird die Sicherung möglicherweise nicht abgeschlossen. Folglich muss der Dienst nach Abschluss des Failovers neu starten, indem bei Bedarf **BackupAsync** aufgerufen wird.

### Wiederherstellen von Daten

Die Wiederherstellung kann in folgende Szenarien klassifiziert werden, in denen der ausgeführte Dienst Daten aus dem Sicherungsspeicher wiederherstellt:

1. Die Dienstpartition hat Daten verloren. Beispielsweise wird die Festplatte für zwei von drei Replikaten einer Partition (einschließlich des primären Replikates) beschädigt/gelöscht. Die Daten für das neue primäre Replikat müssen wiederhergestellt werden.

2. Der gesamte Dienst geht verloren. Beispielsweise entfernt ein Administrator den gesamten Dienst, sodass Dienst und Daten wiederhergestellt werden müssen.

3. Der Dienst hat Daten der beschädigten Anwendung repliziert (z. B. aufgrund eines Anwendungsfehler). In diesem Fall muss der Dienst aktualisiert/zurückgesetzt werden, um die Ursache der Beschädigung zu entfernen, und nicht beschädigte Daten müssen wiederhergestellt werden.

Viele Ansätze sind möglich, daher nennen wir einige Beispiele zum Verwenden von RestoreAsync zur Wiederherstellung in den obigen Szenarien.

#### Verlust von Partitionsdaten

In diesem Fall würde die Runtime automatisch den Datenverlust erkennen und die **OnDataLossAsync**-API aufrufen.

Der Dienstautor muss zum Wiederherstellen folgende Schritte ausführen: – **IReliableStateManager** überschreiben, um einen neuen ReliableStateManager zurückzugeben und eine Rückruffunktion für Ereignisse mit Datenverlust bereitstellen – Die neuste Sicherung im externen Speicherort mit den Sicherungen des Dienstes suchen – Die neueste Sicherung herunterladen (und im Sicherungsordner entpacken, sofern sie komprimiert war) – **IReliableStateManager.RestoreAsync** mit dem Pfad zum Sicherungsordner aufrufen – Bei erfolgreicher Wiederherstellung „true“ zurückgeben.

Nachfolgend finden Sie ein Beispiel für das Implementieren **OnDataLossAsync**-Methode und das Überschreiben von **IReliableStateManager**.

```C#
        protected override IReliableStateManager CreateReliableStateManager()
        {
            return new ReliableStateManager(new ReliableStateManagerConfiguration(
                    onDataLossEvent: this.OnDataLossAsync));
        }

        protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
        {
            var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

            await this.StateManager.RestoreAsync(backupFolder);

            return true;
        }
```

>[AZURE.NOTE]Für die RestorePolicy ist standardmäßig „sicher“ eingestellt. Dies bedeutet, dass die RestoreAsync-API mit ArgumentException fehlschlägt, wenn sie erkennt, dass der Sicherungsordner einen Status enthält, der älter ist als der Status in diesem Replikat bzw. diesem entspricht. RestorePolicy.Force kann verwendet werden, um diese Sicherheitsprüfung zu überspringen.

#### Gelöschter oder verlorener Dienst

Wenn ein Dienst entfernt wird, muss der Dienst neu erstellt werden, bevor die Daten wiederhergestellt werden können. Der Dienst muss unbedingt mit der gleichen Konfiguration erstellt werden, z. B. Partitionierungsschema, sodass die Daten problemlos wiederhergestellt werden können. Sobald der Dienst wieder läuft, muss die API zum Wiederherstellen von Daten (**OnDataLossAsync** oben) auf jeder Partition dieses Dienstes aufgerufen werden. Eine Möglichkeit, das zu erreichen, ist die Verwendung von **FabricClient.ServiceManager.InvokeDataLossAsync** auf jeder Partition. Weitere Informationen zu **InvokeDataLossAsync** finden Sie im Testability-Kapitel.

Ab diesem Zeitpunkt erfolgt die Implementierung wie im oben aufgeführten Szenario. Jede Partition muss die letzte relevante Sicherung aus dem externen Speicher wiederherstellen. Ein Nachteil ist, dass die Partitions-ID sich geändert haben kann, da die Runtime Partitions-IDs dynamisch erstellt. Daher muss der Dienst die entsprechenden Partitionsinformationen und den Dienstnamen speichern, um die aktuelle Sicherung zum Speichern für jede Partition zu finden.


#### Replizieren beschädigter Anwendungsdaten

Liegt nach der Aktualisierung ein Fehler in der Anwendung war, werden möglicherweise Daten beschädigt – z. B. beginnt ein Anwendungsupgrade, jede Telefonnummer in einem Reliable Dictionary mit einer ungültigen Vorwahl zu versehen. In diesem Fall werden die ungültigen Telefonnummern repliziert, da Service Fabric die Art der gespeicherten Daten nicht kennt.

Treten solche Fehler auf, die Daten beschädigen, frieren Sie den Dienst am Besten auf Anwendungsebene ein und upgraden, falls möglich, auf die Version des Anwendungscodes, in der dieser Fehler nicht auftritt. Auch nach dem Korrigieren des Dienstcodes sind die Daten möglicherweise immer noch beschädigt und müssen daher wiederhergestellt werden. In solchen Fällen reicht es möglicherweise nicht aus, die neueste Sicherung wiederherzustellen, da die neuesten Sicherungen möglicherweise ebenfalls beschädigt sind. Daher muss die letzte Sicherung vor Beschädigung der Daten gefunden werden.

Eine Möglichkeit hierzu ist, einen neuen Service Fabric-Cluster bereitzustellen und die Sicherungen der betroffenen Partitionen wie im oben aufgeführten Szenario zum gelöschten Dienst wiederherzustellen. Wenn die letzte Sicherung vor Beschädigung der Daten gefunden wurde, können alle Sicherungen dieser Partition nach dieser Sicherung verschoben/gelöscht werden. Wird nun **OnDataLossAsync** auf der Partition im Produktionscluster aufgerufen, wird die letzte im externen Speicher gefundene Sicherung vom oben beschriebenen Verfahren verwendet.

Mit den Schritten aus dem Szenario zum gelöschten Dienst kann der Status der Dienstsicherung wieder auf den Status vor Beschädigung der Datei wiederhergestellt werden.


>[AZURE.NOTE]Bei jeder Wiederherstellung besteht die Möglichkeit, dass die wiederherzustellende Sicherung älter als der Status der Partition vor Verlust der Daten ist. Deshalb sollte die Wiederherstellung nur als letzter Ausweg verwendet werden, um so viele Daten wie möglich wiederherzustellen.

>[AZURE.NOTE]Die Zeichenfolge, die den Sicherungsorderpfad sowie die Dateipfade im Sicherungsordner repräsentiert, kann je nach dem FabricDataRoot-Pfad und der Namenslänge des Anwendungstyps länger als 255 Zeichen sein. Einige .Net-Methoden wie **Directory.Move** werfen daraufhin möglicherweise die **PathTooLongException** aus. Um das zu umgehen, können kernel32-APIs wie **CopyFile** direkt aufgerufen werden.


## Weitere Informationen zum Sichern und Wiederherstellen

### Sicherung

Mit dem Reliable State Manager können konsistente Sicherungen erstellen werden, ohne vom Dienst benötigte Lese- und Schreibvorgänge zu blockieren. Zu diesem Zweck verwendet er einen Mechanismus für Prüfpunkt- und Protokollbeständigkeit. Der Reliable State Manager entlastet durch Prüfpunkte an bestimmten Punkten das Transaktionsprotokoll und verbessert Wiederherstellungszeiten. Wenn IReliableStateManager.**BackupAsync** aufgerufen wird, weist der Reliable State Manager alle zuverlässigen Objekte an, ihre neuesten Prüfpunktdateien in einen lokalen Sicherungsordner zu kopieren. Der Reliable State Manager kopiert dann alle Protokolleinträge ab dem „Start“-Zeiger auf den aktuellen Datensatz in den Sicherungsordner. Da alle Protokolldatensätze bis zum aktuellen Protokolldatensatz in der Sicherung enthalten sind und der Reliable State Manager „Write Ahead Logging“ beibehält, garantiert der Reliable State Manager, dass alle bestätigten (CommitAsync wurde erfolgreich wiedergegeben) Transaktionen in der Sicherung enthalten sind.

Sämtliche nach dem Aufrufen von **BackupAsync** bestätigten Transaktionen sind möglicherweise nicht in der Sicherung enthalten. Sobald der lokale Sicherungsordner der Plattform von der Plattform ausgefüllt wurde (z  B. lokale Sicherung von der Runtime abgeschlossen) ausgefüllt wurde, wird der Rückruf der Dienstsicherung aufgerufen. Durch diesen Rückruf wird der Sicherungsordner in einen externen Speicherort wie den Azure-Speicher verschoben.

### Wiederherstellen

Reliable State Manager ermöglicht das Wiederherstellen aus einer Sicherung mithilfe der IReliableStateManager.RestoreAsync-API. Die RestoreAsync-Methode kann nur innerhalb der OnDataLossAsync-Methode aufgerufen werden. Die von OnDataLossAsync zurückgegebene Bool gibt an, ob der Dienst seinen Status aus einer externen Quelle wiederhergestellt hat. Wenn die OnDataLossAsync „true“ zurückgibt, erstellt Service Fabric alle anderen Replikate aus diesem primären neu. Replikate, die OnDataLossAsync empfangen, gehen zuerst zur primären Rolle über, erhalten jedoch keinen Lese- oder Schreibstatus. Dies bedeutet, dass RunAsync bei StatefulService-Implementierern erst aufgerufen wird, wenn OnDataLossAsync erfolgreich abgeschlossen wurde. Bis ein Dienst diese API erfolgreich (durch Rückgabe von true oder false) abschließt und die relevanten Neukonfigurationen fertigstellt, wird weiterhin die API aufgerufen.

RestoreAsync löscht zuerst jeden bestehenden Zustand in dem primären Replikat, von dem es aufgerufen wurde. Danach erstellt der Reliable State Manager die Reliable Objects, die im Sicherungsordner vorhanden sind. Als Nächstes werden die Reliable Objects angewiesen, ihre Prüfpunkte im Sicherungsordner wiederherzustellen. Schließlich stellt Reliable State Manager seinen eigenen Status aus den Protokolldatensätzen im Sicherungsordner wieder her und führt die Wiederherstellung aus. Im Rahmen des Wiederherstellungsprozesses werden ab dem „Startpunkt“ beginnende Vorgänge, die Protokolldatensätze in den Sicherungsordner ausgeführt haben, in den Reliable Objects wiederholt. Dadurch wird sichergestellt, dass der wiederhergestellte Status konsistent ist.

<!---HONumber=Nov15_HO4-->