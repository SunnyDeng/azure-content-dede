<properties 
	pageTitle="Verwenden des Azure-Speicheremulators für Entwicklung und Tests | Microsoft Azure" 
	description="Der Azure-Speicheremulator bietet eine kostenlose lokale Entwicklungsumgebung zum Entwickeln und Testen für Azure Storage. Informationen zum Speicheremulator, einschließlich der Authentifizierung von Anforderungen, der Herstellung von Verbindungen von Anwendungen mit dem Emulator und Verwenden des Befehlszeilentools." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="tamram"/>

# Einsatz des Azure-Speicheremulators für Entwicklung und Tests

## Übersicht

Der Microsoft Azure-Speicheremulator bietet eine lokale Umgebung, die die Azure-Blob-, -Warteschlangen- und -Tabellendienste für Entwicklungszwecke emuliert. Durch Verwendung des Speicheremulators können Sie die Anwendung bezüglich der Speicherdienste lokal testen, ohne ein Azure-Abonnement zu erwerben oder sonstige Kosten zu verursachen. Wenn Sie mit der Funktion der Anwendung im Emulator zufrieden sind, können Sie zur Verwendung eines Azure-Speicherkontos in der Cloud wechseln.

> [AZURE.NOTE]Der Speicheremulator ist als Teil des [Microsoft Azure-SDK](https://azure.microsoft.com/downloads/) verfügbar. Sie können den Speicheremulator auch als eigenständiges Paket installieren. Zum Konfigurieren des Speicheremulators benötigen Sie Administratorrechte auf dem Computer.
>  
> Beachten Sie, dass der Zugriff auf die Daten, die in einer bestimmten Version des Speicheremulators erstellt wurden, bei Verwendung einer anderen Version nicht gewährleistet ist. Wenn Sie die Daten langfristig beibehalten müssen, wird empfohlen, diese Daten in einem Azure Storage-Konto und nicht im Speicheremulator zu speichern.

## Funktionsweise des Speicheremulators
 
Der Speicheremulator verwendet zum Emulieren der Azure Storage-Dienste eine lokale Microsoft SQL Server-Instanz und das lokale Dateisystem. Der Speicheremulator verwendet standardmäßig eine Datenbank in Microsoft SQL Server 2012 Express LocalDB. Sie können den Speicheremulator für den Zugriff auf eine lokale Instanz von SQL Server statt für den Zugriff auf die LocalDB-Instanz konfigurieren. Weitere Informationen finden Sie unten unter [Starten und Initialisieren des Speicheremulators](#start-and-initialize-the-storage-emulator).

Sie können für die Verwaltung der LocalDB-Installation SQL Server Management Studio Express installieren. Der Speicheremulator stellt über die Windows-Authentifizierung eine Verbindung mit SQL Server oder LocalDB her.

Es bestehen einige Unterschiede in der Funktionsweise zwischen dem Speicheremulator und den Azure Storage-Diensten. Weitere Informationen zu diesen Unterschieden finden Sie unter [Unterschiede zwischen dem Speicheremulator und Azure Storage](#differences-between-the-storage-emulator-and-azure-storage).

## Authentifizieren von Anforderungen an den Speicheremulator

Wie bei Azure Storage in der Cloud muss jede Anforderung, die Sie mit dem Speicheremulator vornehmen, authentifiziert werden, sofern es sich nicht um eine anonyme Anforderung handelt. Sie können Anforderungen an den Speicheremulator mit einem gemeinsam verwendeten Schlüssel oder mit einer Shared Access Signature (SAS) authentifizieren.

### Authentifizierung mit Benutzeranmeldeinformationen eines gemeinsam verwendeten Schlüssels

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Weitere Informationen zu Verbindungszeichenfolgen finden Sie unter [Konfigurieren von Verbindungszeichenfolgen für Azure Storage](storage-configure-connection-string.md).

### Authentifizierung mit einer SAS (Shared Access Signature) 

Einige Azure Storage-Clientbibliotheken, wie z. B. die Xamarin-Bibliothek, unterstützen nur Authentifizierung mit einem SAS (Shared Access Signature)-Token. Sie benötigen zum Erstellen dieses SAS-Tokens ein Tool oder eine Anwendung mit Unterstützung für die Authentifizierung mit einem gemeinsam verwendeten Schlüssel. Eine einfache Möglichkeit zum Generieren des SAS-Tokens bietet Azure PowerShell:

1. Installieren Sie Azure PowerShell, sofern noch nicht geschehen. Es empfiehlt sich, die neueste Version der Azure PowerShell-Cmdlets zu verwenden. Installationsanweisungen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../articles/powershell-install-configure.md#Install).

2. Öffnen Sie Azure PowerShell, und führen Sie die folgenden Befehle aus. Ersetzen Sie unbedingt *ACCOUNT_NAME* und *ACCOUNT_KEY==* durch Ihre eigenen Anmeldeinformationen. Ersetzen Sie *CONTAINER_NAME* durch einen Namen Ihrer Wahl.

		$context = New-AzureStorageContext -StorageAccountName "ACCOUNT_NAME" -StorageAccountKey "ACCOUNT_KEY=="
		
		New-AzureStorageContainer CONTAINER_NAME -Permission Off -Context $context
		
		$now = Get-Date 
		
		New-AzureStorageContainerSASToken -Name CONTAINER_NAME -Permission rwdl -ExpiryTime $now.AddDays(1.0) -Context $context -FullUri

Der resultierende SAS-URI für den neuen Container sollte dem folgenden ähneln:

	https://storageaccount.blob.core.windows.net/sascontainer?sv=2012-02-12&se=2015-07-08T00%3A12%3A08Z&sr=c&sp=wl&sig=t%2BbzU9%2B7ry4okULN9S0wst%2F8MCUhTjrHyV9rDNLSe8g%3Dsss

Die mit diesem Beispiel erstellte SAS gilt für einen Tag. Die Signatur gewährt uneingeschränkten Zugriff (z. B. Lesen, Schreiben, Löschen und Auflisten) auf die Blobs im Container.

Weitere Informationen zu SAS finden Sie unter [Shared Access Signatures: Grundlagen zum SAS-Modell](storage-dotnet-shared-access-signature-part-1.md).


## Starten und Initialisieren des Speicheremulators

Wählen Sie zum Starten des Azure-Speicheremulators die Schaltfläche "Start" aus, oder drücken Sie die Windows-Taste. Beginnen Sie mit der Eingabe von **Azure-Speicheremulator**, und wählen Sie den Emulator in der Liste der Anwendungen aus.

Wenn der Emulator ausgeführt wird, wird im Infobereich der Windows-Taskleiste ein Symbol angezeigt.

Beim Start des Speicheremulators wird ein Befehlszeilenfenster angezeigt. In diesem Befehlszeilenfenster können Sie den Speicheremulator starten und beenden, Daten löschen, den aktuellen Status abfragen und den Emulator initialisieren. Weitere Informationen finden Sie unter [Referenz zum Speicheremulator-Befehlszeilentool](#storage-emulator-command-line-tool-reference).

Wenn Sie das Befehlszeilenfenster schließen, wird der Speicheremulator weiterhin ausgeführt. Wiederholen Sie die obigen Schritte zum Start des Speicheremulators, um die Befehlszeile erneut anzuzeigen.

Wenn Sie den Speicheremulator zum ersten Mal ausführen, wird die lokale Speicherumgebung für Sie initialisiert. Durch die Initialisierung wird in LocalDB eine Datenbank erstellt, und es werden für jeden lokalen Speicherdienst HTTP-Ports reserviert.

Der Speicheremulator wird standardmäßig im Verzeichnis "C:\Programme (x86)\Microsoft SDKs\Azure\Storage Emulator" installiert.

### Initialisieren des Speicheremulators zur Verwendung einer anderen SQL-Datenbank

Sie können das Speicheremulator-Befehlszeilentool zum Initialisieren des Speicheremulators verwenden, damit dieser auf eine andere SQL-Datenbankinstanz als die Standardinstanz von LocalDB verweist. Beachten Sie, dass Sie das Befehlszeilentool mit Administratorberechtigungen ausführen müssen, um die Back-End-Datenbank für den Speicheremulator zu initialisieren:

1. Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die **Windows**-Taste. Geben Sie `Azure Storage Emulator` ein, und wählen Sie den Speicheremulator aus, wenn er angezeigt wird, um das Speicheremulator-Befehlszeilentool aufzurufen.
2. Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein, wobei `<SQLServerInstance>` der Name der SQL Server-Instanz ist. Geben Sie als SQL Server-Instanz `(localdb)\v11.0` ein, um LocalDB zu verwenden.

		AzureStorageEmulator init /sqlInstance <SQLServerInstance> 
    
	Mit dem folgenden Befehl können Sie den Emulator anweisen, die SQL Server-Standardinstanz zu verwenden:

    	AzureStorageEmulator init /server .\ 

	Sie können auch den folgenden Befehl verwenden, mit dem die Datenbank erneut als Standardinstanz von LocalDB initialisiert wird:

    	AzureStorageEmulator init /forceCreate 

Weitere Informationen zu diesen Befehlen finden Sie unter [Referenz zum Speicheremulator-Befehlszeilentool](#storage-emulator-command-line-tool-reference).

## Adressieren von Ressourcen im Speicheremulator

Die Dienstendpunkte für den Speicheremulator unterscheiden sich von denen eines Azure-Speicherkontos. Der Unterschied besteht darin, dass der lokale Computer keine Auflösung des Domänennamens durchführt. Die Endpunkte des Speicheremulators benötigen daher eine lokale Adresse anstelle eines Domänennamens.

Wenn Sie eine Ressource in einem Azure-Speicherkonto adressieren, verwenden Sie das folgende Schema, wobei der Kontoname Teil des URI-Hostnamens ist und die adressierte Ressource Teil des URI-Pfads:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>

Der folgende URI ist z. B. eine gültige Adresse für ein Blob in einem Azure-Speicherkonto:

	https://myaccount.blob.core.windows.net/mycontainer/myblob.txt

Im Speicheremulator ist der Kontoname Teil des URI-Pfads und nicht des Hostnamens, weil der lokale Computer Domänennamen nicht auflöst. Verwenden Sie das folgende Schema für eine im Speicheremulator ausgeführte Ressource:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Beispielsweise kann für den Zugriff auf ein Blob im Speicheremulator die folgende Adresse verwendet werden:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

Die Dienstendpunkte für den Speicheremulator sind:

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

### Adressieren des sekundären Kontos mit RA-GRS

Ab Version 3.1 unterstützt das Speicheremulatorkonto georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Für Speicherressourcen in der Cloud und im lokalen Emulator können Sie auf den sekundären Speicherort zugreifen, indem Sie "-secondary" an den Kontonamen anfügen. Beispielsweise kann die folgende Adresse für den Zugriff auf ein Blob mithilfe des sekundären Speicherorts mit Lesezugriff verwendet werden:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE]Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. Ausführliche Informationen finden Sie unter [Microsoft Azure-Speicherclientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

## Referenz zum Speicheremulator-Befehlszeilentool

Ab Version 3.0 wird beim Start des Speicheremulators ein Befehlszeilenfenster angezeigt. In diesem Befehlszeilenfenster können Sie den Emulator starten und beenden, den Status abfragen und weitere Operationen ausführen.

> [AZURE.NOTE]Falls Sie den Microsoft Azure-Serveremulator installiert haben, wird beim Start des Speicheremulators ein Taskleistensymbol angezeigt. Klicken Sie mit der rechten Maustaste auf das Symbol, um ein Menü zu öffnen, das eine grafische Möglichkeit bietet, den Speicheremulator zu starten und zu beenden.

### Befehlszeilensyntax

	AzureStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### Optionen

Geben Sie zum Anzeigen der Liste der Optionen an der Eingabeaufforderung `/help` ein.

| Option | Beschreibung | Befehl | Argumente |
|--------|----------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| **Starten** | Startet den Speicheremulator. | `AzureStorageEmulator start [-inprocess]` | *-inprocess*: startet den Emulator im aktuellen Prozess, anstatt einen neuen Prozess zu erstellen. |
| **Beenden** | Beendet den Speicheremulator. | `AzureStorageEmulator stop` | |
| **Status** | Zeigt den Status des Speicheremulators an. | `AzureStorageEmulator status` | |
| **Clear** | Löscht die Daten in allen Diensten, die an der Befehlszeile angegeben werden. | `AzureStorageEmulator clear [blob] [table] [queue] [all]                                                    `| *blob*: löscht Blob-Daten. <br/>*queue*: löscht Warteschlangendaten. <br/>*table*: löscht Tabellendaten. <br/>*all*: löscht sämtliche Daten in allen Diensten. |
| **Init** | Führt eine einmalige Initialisierung zur Einrichtung des Emulators durch. | `AzureStorageEmulator.exe init [-server serverName] [-sqlinstance instanceName] [-forcecreate] [-inprocess]` | *-server serverName*: gibt den Server an, auf dem die SQL-Instanz gehostet wird. <br/>*-sqlinstance instanceName*: gibt den Namen der zu verwendenden SQL-Instanz an. <br/>*-forcecreate*: erzwingt die Erstellung der SQL-Datenbank, auch wenn diese bereits vorhanden ist. <br/>*-inprocess*: führt die Initialisierung im aktuellen Prozess aus, anstatt einen neuen Prozess zu erstellen. Dazu muss der aktuelle Prozess mit erweiterten Berechtigungen gestartet worden sein. |
                                                                                                                  
## Unterschiede zwischen dem Speicheremulator und Azure Storage

Da der Speicheremulator eine emulierte Umgebung darstellt, die in einer lokalen SQL-Instanz ausgeführt wird, gibt es einige funktionelle Unterschiede zwischen dem Emulator und einem Azure-Speicherkonto in der Cloud:

- Der Speicheremulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel.

- Der Speicheremulator ist kein skalierbarer Speicherdienst, und er unterstützt keine große Anzahl von gleichzeitigen Clients.

- Wie in [Adressieren von Ressourcen im Speicheremulator](#addressing-resources-in-the-storage-emulator) beschrieben, werden Ressourcen im Speicheremulator im Vergleich zu einem Azure-Speicherkonto anders adressiert. Dieser Unterschied ist darauf zurückzuführen, dass die Domänennamensauflösung zwar in der Cloud, nicht aber auf dem lokalen Computer verfügbar ist.

- Ab Version 3.1 unterstützt das Speicheremulatorkonto georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Im Emulator ist für alle Konten RA-GRS aktiviert, und es entsteht niemals eine Verzögerung zwischen den primären und sekundären Replikaten. Die Vorgänge "Get Blob Service Stats", "Get Queue Service Stats" und "Get Table Service Stats" werden für das sekundäre Konto unterstützt. Sie geben immer den Wert des `LastSyncTime`-Antwortelements als aktuelle Uhrzeit der zugrunde liegenden SQL-Datenbank zurück.

	Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. Ausführliche Informationen finden Sie unter [Microsoft Azure-Speicherclientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

- Die Endpunkte für Dateidienst und SMB-Protokolldienst werden im Speicheremulator zurzeit nicht unterstützt.

### Unterschiede beim Blob-Speicher 

Die folgenden Unterschiede gelten für Blob-Speicher im Emulator:

- Der Speicheremulator unterstützt nur Blob-Größen bis 2 GB.

- Ein Ablegevorgang für ein Blob kann bei einem Blob, das im Speicheremulator vorhanden ist und über eine aktive Lease verfügt, erfolgreich durchgeführt werden, auch wenn die Lease-ID nicht in der Anforderung angegeben wurde.

### Unterschiede beim Tabellenspeicher 

Die folgenden Unterschiede gelten für Tabellenspeicher im Emulator:

- Datumseigenschaften im Tabellendienst im Speicheremulator unterstützen nur den von SQL Server 2005 unterstützten Bereich (*d. h.*, sie müssen nach dem 1. Januar 1753 liegen). Alle Datumsangaben vor dem 1. Januar 1753 werden in diesen Wert geändert. Die Genauigkeit der Daten ist begrenzt auf die Genauigkeit von SQL Server 2005, d. h., Datumsangaben sind auf 1/300 Sekunde genau.

- Der Speicheremulator unterstützt Eigenschaftenwerte für Partitions- und Zeilenschlüssel von jeweils weniger als 512 Byte. Darüber hinaus darf die Gesamtgröße von Kontoname, Tabellenname und Schlüsseleigenschaftennamen 900 Byte nicht überschreiten.

- Die Gesamtgröße einer Zeile in einer Tabelle im Speicheremulator ist auf weniger als 1 MB beschränkt.

- Im Speicheremulator unterstützen Eigenschaften des Datentyps `Edm.Guid` oder `Edm.Binary` in Filterzeichenfolgen für Abfragen nur die Vergleichsoperatoren `Equal (eq)` und `NotEqual (ne)`.

### Unterschiede beim Warteschlangenspeicher

Es bestehen keine Unterschiede beim Warteschlangenspeicher im Emulator.

## Speicheremulator – Versionshinweise

### Version 4.0

Die ausführbare Datei des Speicheremulators wurde in *AzureStorageEmulator.exe* umbenannt.

### Version 3.2
- Der Speicheremulator unterstützt nun Version 2014-02-14 der Speicherdienste auf Blob-, Warteschlangen- und Dienstendpunkten. Beachten Sie, dass Endpunkte für den Dateidienst im Speicheremulator zurzeit nicht unterstützt werden. Ausführliche Informationen über die Version 2014-02-14 finden Sie unter [Versionsverwaltung für den Blob-Dienst, den Warteschlangendienst und den Tabellendienst in Windows Azure](https://msdn.microsoft.com/library/azure/dd894041.aspx).

### Version 3.1
- Lesezugriff auf den geografisch redundanten Speicher (RA-GRS) wird nun im Speicheremulator unterstützt. Die API-Vorgänge "Get Blob Service Stats", "Get Queue Service Stats" und "Get Table Service Stats" werden für das sekundäre Konto unterstützt. Sie geben immer den Wert des LastSyncTime-Antwortelements als aktuelle Uhrzeit der zugrunde liegenden SQL-Datenbank zurück. Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. Ausführliche Informationen finden Sie in der Referenz zur Speicherclientbibliothek.

### Version 3.0
- Der Azure-Speicheremulator wird nicht mehr im gleichen Paket wie der Serveremulator ausgeliefert.

- Die grafische Benutzeroberfläche des Speicheremulators wird durch eine skriptfähige Befehlszeilenschnittstelle ersetzt. Ausführliche Informationen zur Befehlszeilenschnittstelle finden Sie in der Referenz zum Speicheremulator-Befehlszeilentool. Die grafische Benutzeroberfläche wird weiterhin in Version 3.0 enthalten sein, es kann aber nur sie zugegriffen werden, wenn der Serveremulator installiert ist, indem Sie mit der rechten Maustaste auf das Taskleistensymbol klicken und die Speicheremulator-Benutzeroberfläche auswählen.

- Version 2013-08-15 der Azure Storage-Dienste wird jetzt vollständig unterstützt. (Zuvor wurde diese Version nur von Version 2.2.1 Preview des Speicheremulators unterstützt.)



 

<!---HONumber=July15_HO4-->