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
   ms.date="01/25/2016"
   ms.author="mcoskun"/>

# Sichern und Wiederherstellen von Reliable Services

Azure Service Fabric ist eine Plattform mit hoher Verfügbarkeit, bei der der Status über mehrere Knoten repliziert wird, um diese hohe Verfügbarkeit zu gewährleisten. Auch wenn ein Knoten im Cluster ausfällt, bleiben die Dienste somit verfügbar. Diese von der Plattform bereitgestellte integrierte Redundanz reicht in manchen Fällen aus. In bestimmten Fällen wäre es jedoch wünschenswert, dass der Dienst Daten (auf einem externen Speicher) sichert.

Ein Dienst möchte Daten beispielsweise in den folgenden Szenarien sichern:

* Bei dauerhaftem Verlust eines gesamten Service Fabric-Clusters oder aller Knoten, die eine bestimmte Partition ausführen. Dies kann beispielsweise passieren, wenn Sie keine Georeplikation für den Zustand verwenden, der gesamte Cluster sich in einem Rechenzentrum befindet und das gesamte Rechenzentrum ausfällt.

* Administrative Fehler, durch die der Zustand versehentlich gelöscht oder beschädigt wird. Das kann z. B. passieren, wenn ein Administrator mit ausreichenden Berechtigungen versehentlich den Dienst löscht.

* Fehler im Dienst, die zu einer Beschädigung von Daten führen. Dies kann beispielsweise bei einem Dienstcode-Upgrade geschehen, bei dem fehlerhafte Daten in eine Reliable Collection geschrieben werden. In diesem Fall müssen unter Umständen der Code und die Daten in einen früheren Zustand zurückversetzt werden.

* Offline-Datenverarbeitung. Es kann zweckmäßig sein, Daten für Business Intelligence separat von dem Dienst, der die Daten generiert, offline zu verarbeiten.

Das Feature für die Sicherung/Wiederherstellung ermöglicht mit Reliable Services-API entwickelten Diensten die Erstellung und Wiederherstellung von Sicherungen. Die von der Plattform bereitgestellten Sicherungs-APIs ermöglichen das Erstellen von Sicherungen eines Partitionsstatus, ohne Lese- oder Schreibvorgänge zu blockieren. Die Wiederherstellungs-APIs ermöglichen die Wiederherstellung des Status einer Partition aus einer Sicherung.


## Sicherungsvorgang

Der Dienstautor kann bestimmen, wann Sicherungen erstellt und wo sie gespeichert werden.

Der Dienst muss **IReliableStateManager.BackupAsync** aufrufen, um die Sicherung zu starten. Sicherungen können nur von primären Replikaten erstellt werden und benötigen Schreibzugriff.

Wie Sie unten sehen, hat die einfachste Überladung von **BackupAsync** in Func<< BackupInfo  bool >> **backupCallback** aufgerufen.

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** liefert Informationen über die Sicherung, einschließlich des Speicherorts des Ordners, in dem die Runtime die Sicherung **(BackupInfo.Directory)** gespeichert hat. Die Callback-Funktion möchte **BackupInfo.Directory** in einen externen Speicher oder an einen anderen Speicherort verschieben. Diese Funktion gibt auch einen booleschen Wert zurück, der angibt, ob der Sicherungsordner erfolgreich an den Zielspeicherort verschoben werden konnte.

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

Beachten Sie Folgendes:

- Pro Replikat-Inflight ist stets nur ein **BackupAsync**-Vorgang möglich. Mehrere **BackupAsync**-Aufrufe gleichzeitig lösen **FabricBackupInProgressException** aus, um nur eine Inflight-Sicherung zuzulassen.

- Wenn ein Replikat während einer Sicherung ausfällt, wird die Sicherung möglicherweise nicht abgeschlossen. Folglich muss der Dienst nach Abschluss des Failovers neu starten, indem bei Bedarf **BackupAsync** aufgerufen wird.

## Wiederherstellungsvorgang

Im Allgemeinen können die Fälle, in denen Sie möglicherweise eine Wiederherstellung durchführen müssen, einer der folgenden Kategorien zugeordnet werden:


- Die Dienstpartition hat Daten verloren. Beispielsweise wird die Festplatte für zwei von drei Replikaten einer Partition (einschließlich des primären Replikats) beschädigt oder gelöscht. Das neue primäre Replikat muss möglicherweise Daten aus einer Sicherung wiederherstellen.

- Der gesamte Dienst geht verloren. Beispielsweise entfernt ein Administrator den gesamten Dienst, sodass Dienst und Daten wiederhergestellt werden müssen.

- Der Dienst hat beschädigte Anwendungsdaten repliziert (z. B. aufgrund eines Anwendungsfehlers). In diesem Fall muss der Dienst aktualisiert oder zurückgesetzt werden, um die Ursache der Beschädigung zu entfernen, und nicht beschädigte Daten müssen wiederhergestellt werden.

Da viele Ansätze möglich sind, nennen wir einige Beispiele zum Verwenden von **RestoreAsync** zur Wiederherstellung in den obigen Szenarien.

## Verlust von Partitionsdaten

In diesem Fall würde die Runtime automatisch den Datenverlust erkennen und die **OnDataLossAsync**-API aufrufen.

Der Dienstautor muss Folgendes wiederherstellen:

- Überschreiben Sie **CreateReliableStateManager**, um ein neues **ReliableStateManager**-Element zurückzugeben, und geben Sie eine Rückruffunktion an, die bei einem Datenverlustereignis aufgerufen wird.

- Suchen Sie am externen Speicherort, der die Sicherungen des Diensts enthält, nach den neuesten Sicherungen.

- Geben Sie „false“ zurück, wenn sich der Zustand der letzten Sicherung hinter dem neuen primären Replikat befindet. Dadurch wird sichergestellt, dass das neue primäre Replikat nicht mit älteren Daten überschrieben wird.

- Laden Sie die neueste Sicherung herunter (und dekomprimieren Sie die Sicherung in den Sicherungsordner, wenn sie komprimiert wurde).

- Rufen Sie **IReliableStateManager.RestoreAsync** mit dem Pfad zum Sicherungsordner auf.

- Geben Sie „true“ zurück, wenn die Wiederherstellung erfolgreich war.

Nachfolgend finden Sie ein Beispiel für das Implementieren der **OnDataLossAsync**-Methode und das Überschreiben von **IReliableStateManager**.

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

>[AZURE.NOTE] Für die RestorePolicy ist standardmäßig „sicher“ eingestellt. Dies bedeutet, dass die **RestoreAsync**-API mit ArgumentException fehlschlägt, wenn sie erkennt, dass der Sicherungsordner einen Status enthält, der älter ist als der Status in diesem Replikat bzw. diesem entspricht. **RestorePolicy.Force** kann verwendet werden, um diese Sicherheitsprüfung zu überspringen.

## Gelöschter oder verlorener Dienst

Wenn ein Dienst entfernt wird, muss der Dienst erst neu erstellt werden, bevor die Daten wiederhergestellt werden können. Der Dienst muss unbedingt mit der gleichen Konfiguration erstellt werden, z. B. dem Partitionierungsschema, damit die Daten problemlos wiederhergestellt werden können. Sobald der Dienst wieder läuft, muss die API zum Wiederherstellen von Daten (**OnDataLossAsync** oben) auf jeder Partition dieses Dienstes aufgerufen werden. Eine Möglichkeit, dies zu erreichen, ist die Verwendung von **FabricClient.ServiceManager.InvokeDataLossAsync** auf jeder Partition.

Ab diesem Zeitpunkt erfolgt die Implementierung wie im oben aufgeführten Szenario. Jede Partition muss die letzte relevante Sicherung aus dem externen Speicher wiederherstellen. Ein Nachteil ist, dass die Partitions-ID sich geändert haben kann, da die Runtime Partitions-IDs dynamisch erstellt. Daher muss der Dienst die entsprechenden Partitionsinformationen und den Dienstnamen speichern, um die aktuelle Sicherung zum Speichern für jede Partition zu finden.


## Replizieren beschädigter Anwendungsdaten

Wenn das neu bereitgestellte Anwendungsupgrade fehlerhaft ist, werden ggf. Daten beschädigt. Beispielsweise kann ein Anwendungsupgrade damit beginnen, jede Telefonnummer in einem Reliable Dictionary mit einer ungültigen Vorwahl zu versehen. In diesem Fall werden die ungültigen Telefonnummern repliziert, da Service Fabric die Art der gespeicherten Daten nicht kennt.

Treten solche Fehler auf, bei denen Daten beschädigt werden, frieren Sie den Dienst am besten auf Anwendungsebene ein und führen, falls möglich, ein Upgrade auf die Version des Anwendungscodes durch, in der dieser Fehler nicht auftritt. Auch nach dem Korrigieren des Dienstcodes sind die Daten möglicherweise immer noch beschädigt und müssen daher wiederhergestellt werden. In solchen Fällen reicht es möglicherweise nicht aus, die neueste Sicherung wiederherzustellen, da die neuesten Sicherungen möglicherweise ebenfalls beschädigt sind. Daher müssen Sie die letzte Sicherung vor Beschädigung der Daten finden.

Wenn Sie nicht sicher sind, welche Sicherungen fehlerhaft sind, können Sie einen neuen Service Fabric-Cluster bereitstellen und die Sicherungen der betroffenen Partitionen wie im oben beschriebenen Szenario „Gelöschter oder verlorener Dienst“ wiederherstellen. Beginnen Sie für jede Partition mit der Wiederherstellung der Sicherungen, beginnend mit der neuesten bis hin zur ältesten. Sobald Sie eine Sicherung ohne Beschädigung gefunden haben, verschieben/löschen Sie alle Sicherungen dieser Partition, die aktueller sind als diese Sicherung. Wiederholen Sie diesen Vorgang für jede Partition. Wird nun **OnDataLossAsync** auf der Partition im Produktionscluster aufgerufen, handelt es sich bei der letzten im externen Speicher gefundenen Sicherung um die vom oben beschriebenen Verfahren verwendete.

Mit den Schritten im Abschnitt „Gelöschter oder verlorener Dienst“ kann der Status der Dienstsicherung wieder auf den Status vor der Beschädigung der Datei gesetzt werden.

Beachten Sie Folgendes:

- Bei einer Wiederherstellung besteht die Möglichkeit, dass die wiederherzustellende Sicherung älter als der Status der Partition vor dem Verlust der Daten ist. Deshalb sollte die Wiederherstellung nur als letzter Ausweg verwendet werden, um so viele Daten wie möglich wiederherzustellen.

- Die Zeichenfolge, die den Sicherungsorderpfad und die Dateipfade im Sicherungsordner repräsentiert, kann je nach FabricDataRoot-Pfad und Namenslänge des Anwendungstyps länger als 255 Zeichen sein. Einige .NET-Methoden, z. B. **Directory.Move**, lösen daraufhin unter Umständen die Ausnahme **PathTooLongException** aus. Eine Problemumgehung besteht darin, kernel32-APIs wie **CopyFile** direkt aufzurufen.


## Weitere Informationen zum Sichern und Wiederherstellen

### Sicherung
Mit dem Reliable State Manager können konsistente Sicherungen erstellt werden, ohne Lese- oder Schreibvorgänge zu blockieren. Zu diesem Zweck verwendet er einen Mechanismus für Prüfpunkt- und Protokollbeständigkeit. Der Reliable State Manager entlastet durch Prüfpunkte an bestimmten Punkten das Transaktionsprotokoll und verbessert Wiederherstellungszeiten. Wenn **IReliableStateManager.BackupAsync** aufgerufen wird, weist der Reliable State Manager alle Reliable Objects an, ihre neuesten Prüfpunktdateien in einen lokalen Sicherungsordner zu kopieren. Der Reliable State Manager kopiert dann alle Protokolleinträge ab dem „Start“-Zeiger bis zum aktuellen Protokolleintrag in den Sicherungsordner. Da alle Protokolleinträge bis zum aktuellen Protokolleintrag in der Sicherung enthalten sind und der Reliable State Manager die Write-Ahead-Protokollierung beibehält, garantiert der Reliable State Manager, dass alle bestätigten Transaktionen (**CommitAsync** wurde erfolgreich wiedergegeben) in der Sicherung enthalten sind.

Sämtliche nach dem Aufrufen von **BackupAsync** bestätigten Transaktionen können in der Sicherung enthalten sein oder auch nicht. Sobald der lokale Sicherungsordner der Plattform von der Plattform aufgefüllt wurde (z. B. Abschluss der lokalen Sicherung durch die Runtime), wird der Rückruf der Dienstsicherung aufgerufen. Durch diesen Rückruf wird der Sicherungsordner in einen externen Speicherort wie den Azure-Speicher verschoben.

### Wiederherstellen

Der Reliable State Manager ermöglicht das Wiederherstellen aus einer Sicherung mit der **IReliableStateManager.RestoreAsync**-API. Die **RestoreAsync**-Methode kann nur innerhalb der **OnDataLossAsync**-Methode aufgerufen werden. Die von **OnDataLossAsync** zurückgegebene Bool gibt an, ob der Dienst seinen Status aus einer externen Quelle wiederhergestellt hat. Wenn für **OnDataLossAsync** „true“ zurückgegeben wird, erstellt Service Fabric alle anderen Replikate aus diesem primären Replikat neu. Service Fabric stellt sicher, dass Replikate, die **OnDataLossAsync** empfangen, zunächst die primäre Rolle annehmen, aber nicht den Lese- oder Schreibstatus erhalten. Dies bedeutet, dass **RunAsync** bei StatefulService-Implementierern erst aufgerufen wird, wenn **OnDataLossAsync** erfolgreich abgeschlossen wurde. **OnDataLossAsync** wird dann für das neue primäre Replikat aufgerufen. Bis ein Dienst diese API erfolgreich (durch Rückgabe von true oder false) abschließt und die relevanten Neukonfigurationen fertigstellt, wird weiterhin die API nacheinander aufgerufen.


**RestoreAsync** löscht zuerst jeden bestehenden Zustand in dem primären Replikat, von dem es aufgerufen wurde. Danach erstellt der Reliable State Manager alle Reliable Objects, die im Sicherungsordner vorhanden sind. Als Nächstes werden die Reliable Objects angewiesen, ihre Prüfpunkte im Sicherungsordner wiederherzustellen. Schließlich stellt der Reliable State Manager seinen eigenen Status aus den Protokolldatensätzen im Sicherungsordner wieder her und führt die Wiederherstellung aus. Im Rahmen des Wiederherstellungsprozesses werden ab dem „Startpunkt“ beginnende Vorgänge, die Protokolldatensätze in den Sicherungsordner ausgeführt haben, in den Reliable Objects wiederholt. Dadurch wird sichergestellt, dass der wiederhergestellte Status konsistent ist.

<!---HONumber=AcomDC_0128_2016-->