<properties 
	pageTitle="Einsatz des Azure-Speicheremulators für Entwicklung und Tests" 
	description="Erfahren Sie, wie Sie den Azure-Speicheremulators für Entwicklung und Tests verwenden." 
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
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Einsatz des Azure-Speicheremulators für Entwicklung und Tests

## Übersicht
Der Microsoft Azure-Speicheremulator bietet eine lokale Umgebung, die die Azure-Blob-, -Warteschlangen- und -Tabellendienste für Entwicklungszwecke emuliert. Durch Verwendung des Speicheremulators können Sie die Anwendung bezüglich der Speicherdienste lokal testen, ohne Kosten zu verursachen.

> [AZURE.NOTE] Der Speicheremulator ist als Teil des Microsoft Azure-SDK verfügbar. Sie können den Speicheremulator auch als eigenständiges Paket installieren.
Zum Konfigurieren des Speicheremulators benötigen Sie Administratorrechte auf dem Computer. 
> Beachten Sie, dass der Zugriff auf die Daten, die in einer bestimmten Version des Speicheremulators erstellt wurden, bei Verwendung einer anderen Version nicht gewährleistet ist. Wenn Sie die Daten langfristig beibehalten müssen, wird empfohlen, diese Daten in einem Azure Storage-Konto und nicht im Speicheremulator zu speichern.
 
Es bestehen einige Unterschiede zwischen dem Speicheremulator und den Azure Storage-Diensten. Weitere Informationen zu diesen Unterschieden finden Sie unter [Unterschiede zwischen dem Speicheremulator und den Azure Storage-Diensten](https://msdn.microsoft.com/library/azure/gg433135.aspx).

Der Speicheremulator verwendet zum Emulieren der Azure Storage-Dienste eine Microsoft(r) SQL Server(tm)-Instanz und das lokale Dateisystem. Standardmäßig ist der Speicheremulator für eine Datenbank in Microsoft(r) SQL Server(tm) 2012 Express LocalDB konfiguriert. Sie können für die Verwaltung der LocalDB-Installation SQL Server Management Studio Express installieren. Der Speicheremulator stellt über die Windows-Authentifizierung eine Verbindung mit SQL Server oder LocalDB her. Sie können den Speicheremulator mithilfe der Referenz zum Speicheremulator-Befehlszeilentool für den Zugriff auf eine lokale Instanz von SQL Server statt für den Zugriff auf LocalDB konfigurieren.

## Authentifizieren von Anforderungen

Der Speicheremulator unterstützt nur ein einziges festgelegtes Konto und einen bekannten Authentifizierungsschlüssel. Dieses Konto und dieser Schlüssel sind die einzigen Anmeldeinformationen, die für den Speicheremulator verwendet werden dürfen. Sie lauten wie folgt:

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
    
> [AZURE.NOTE] Der vom Speicheremulator unterstützte Authentifizierungsschlüssel ist lediglich für das Testen der Funktionalität des Clientauthentifizierungscodes vorgesehen. Er erfüllt keinerlei Sicherheitszwecke. Sie können das in der Produktion verwendete Speicherkonto und den zugehörigen Schlüssel nicht mit dem Speicheremulator verwenden. Beachten Sie außerdem, dass Sie das Entwicklungskonto nicht mit Produktionsdaten verwenden sollten.
 

## Starten und Initialisieren des Speicheremulators
Wählen Sie zum Starten des Azure-Speicheremulators die Schaltfläche "Start" aus, oder drücken Sie die Windows-Taste. Beginnen Sie mit der Eingabe von "Azure-Speicheremulator", und wählen Sie "Azure-Speicheremulator" in der Liste der Anwendungen aus. 

Falls der Azure-Serveremulator bereits ausgeführt wird, können Sie den Speicheremulator auch starten, indem Sie mit der rechten Maustaste auf das Taskleistensymbol klicken und "Speicheremulator starten" auswählen. Weitere Informationen zur Ausführung des Serveremulators finden Sie unter [Ausführen einer Azure-Anwendung im Serveremulator](https://msdn.microsoft.com/library/azure/hh403990.aspx).

Beim Start des Speicheremulators wird eine Befehlszeile angezeigt. Mit dieser Befehlszeile können Sie den Speicheremulator starten und beenden, Daten löschen, den aktuellen Status abfragen und den Emulator initialisieren. Weitere Informationen finden Sie unter [Referenz zum Speicheremulator-Befehlszeilentool](https://msdn.microsoft.com/library/azure/gg433005.aspx).

Wenn Sie das Befehlszeilenfenster schließen, wird der Speicheremulator weiterhin ausgeführt. Wiederholen Sie die obigen Schritte zum Start des Speicheremulators, um die Befehlszeile erneut anzuzeigen.


Wenn Sie den Speicheremulator zum ersten Mal ausführen, wird die lokale Speicherumgebung für Sie initialisiert. Sie können mit dem Speicheremulator-Befehlszeilentool auf eine andere Datenbankinstanz verweisen oder die vorhandene Datenbank erneut initialisieren. Durch die Initialisierung wird in LocalDB eine Datenbank erstellt, und es werden für jeden lokalen Speicherdienst HTTP-Ports reserviert. Für diesen Schritt sind Administratorrechte erforderlich. Ausführliche Informationen finden Sie unter [Referenz zum Speicheremulator-Befehlszeilentool](https://msdn.microsoft.com/library/azure/gg433005.aspx).

## Speicherdienst-URIs

Die Adressierung einer Ressource in den Azure Storage-Diensten richtet sich danach, ob sich die Ressource in Azure oder in den Speicheremulatordiensten befindet. Zum Adressieren einer Speicherressource in Azure und zum Adressieren einer Speicherressource im Speicheremulator wird jeweils ein anderes URI-Schema verwendet. Der Grund für die unterschiedlichen Schemas ist, dass der lokale Computer keine Domänennamen auflöst. Beide URI-Schemas enthalten immer den Kontonamen und die Adresse der angeforderten Ressource.

## Adressieren von Azure Storage-Ressourcen in der Cloud

Im URI-Schema für die Adressierung von Speicherressourcen in Azure ist der Kontoname Teil des URI-Hostnamens, und die adressierte Ressource ist Teil des URI-Pfads. Für den Zugriff auf Speicherressourcen wird das folgende einfache Adressierungsschema verwendet:

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


`<account-name>` ist der Name des Speicherkontos. `<service-name>` ist der Name des Diensts, auf den der Zugriff erfolgt, und `<resource-path>` ist der Pfad zu der angeforderten Ressource. folgende Liste enthält das URI-Schema für jeden der Speicherdienste:

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

Beispielsweise kann für den Zugriff auf ein Blob in der Cloud die folgende Adresse verwendet werden:
    
    http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] Sie können auch einem Blob-Speicherendpunkt in der Cloud einen benutzerdefinierten Domänennamen zuordnen und diesen benutzerdefinierten Domänennamen zum Adressieren von Blobs und Containern verwenden. Siehe 
 
## Adressieren von lokalen Azure Storage-Ressourcen im Speicheremulator

Im Speicheremulator ist der Kontoname Teil des URI-Pfads, da der lokale Computer Domänennamen nicht auflöst. Das URI-Schema für eine im Speicheremulator ausgeführte Ressource weist das folgende Format auf:

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Das folgende Format wird zum Adressieren von Ressourcen verwendet, die im Speicheremulator ausgeführt werden:

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

Beispielsweise kann für den Zugriff auf ein Blob im Speicheremulator die folgende Adresse verwendet werden:

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] HTTPS ist kein zulässiges Protokoll für die Adressierung von lokalen Speicherressourcen.
 
## Adressieren des sekundären Kontos mit RA-GRS
Ab Version 3.1 unterstützt das Speicheremulatorkonto georedundante Replikation mit Lesezugriff (Read-Access Geo-Redundant Replication, RA-GRS). Für Speicherressourcen in der Cloud und im lokalen Emulator können Sie auf den sekundären Speicherort zugreifen, indem Sie "-secondary" an den Kontonamen anfügen. Beispielsweise kann die folgende Adresse für den Zugriff auf ein Blob mithilfe des sekundären Speicherorts mit Lesezugriff verwendet werden:

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Für programmgesteuerten Zugriff auf den sekundären Speicherort mit dem Speicheremulator verwenden Sie die Speicherclientbibliothek für .NET, Version 3.2 oder höher. Ausführliche Informationen finden Sie in der Referenz zur Speicherclientbibliothek.
 
## Initialisieren des Speicheremulators mithilfe des Befehlszeilentools
Sie können das Speicheremulator-Befehlszeilentool zum Initialisieren des Speicheremulators verwenden, damit dieser auf eine andere Datenbankinstanz als die Standardinstanz verweist. Wenn Sie die LocalDB-Standardinstanz verwenden möchten, müssen Sie keine separate Initialisierung ausführen.

Dieses Tool wird standardmäßig im Verzeichnis C:\Programme (x86)\Microsoft SDKs\Azure\Storage Emulator\ installiert. Die Initialisierung wird beim ersten Start des Emulators automatisch ausgeführt.

> [AZURE.NOTE] Zum Ausführen des unten beschriebenen "init"-Befehls benötigen Sie Administratorberechtigungen.

1. Klicken Sie auf die Schaltfläche **Start**, oder drücken Sie die **Windows**-Taste. Beginnen Sie mit der Eingabe von `Azure-Speicheremulator`, und wählen Sie den Eintrag in der Liste der Anwendungen aus. Eine Eingabeaufforderung wird geöffnet.


2. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein, wobei `<SQLServerInstance> der Name der SQL Server-Instanz ist. Geben Sie `(localdb)\v11.0` als SQL Server-Instanz ein, um LocalDB zu verwenden.

    WAStorageEmulator init /sqlInstance <SQLServerInstance> 
    
Mit dem folgenden Befehl können Sie den Emulator anweisen, die SQL Server-Standardinstanz zu verwenden:

    WAStorageEmulator init /server .\\ 

Oder Sie können den folgenden Befehl verwenden, mit dem die Datenbank erneut initialisiert wird:

    WAStorageEmulator init /forceCreate 

## Referenz zum Speicheremulator-Befehlszeilentool

Ab Version 3.0 wird beim Start des Speicheremulators eine Eingabeaufforderung angezeigt. Mit dieser Eingabeaufforderung können Sie den Emulator starten und beenden, den Status abfragen und weitere Operationen ausführen.

> [AZURE.NOTE] Falls Sie den Serveremulator installiert haben, wird beim Start des Speicheremulators ein Taskleistensymbol angezeigt. Klicken Sie mit der rechten Maustaste auf das Symbol, um ein Menü zu öffnen, das eine grafische Möglichkeit bietet, den Speicheremulator zu starten und zu beenden.

### Befehlszeilensyntax

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### Optionen
Geben Sie zum Anzeigen der Liste der Optionen "/help" an der Eingabeaufforderung ein.

## Nächste Schritte
- [Referenz zum Speicheremulator-Befehlszeilentool](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- [Unterschiede zwischen dem Speicheremulator und den Azure Storage-Diensten](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [Speicheremulator - Versionshinweise](https://msdn.microsoft.com/library/azure/dn683879.aspx) 

<!--HONumber=47-->
