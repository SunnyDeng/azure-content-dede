<properties 
	pageTitle="Erste Schritte mit der clientseitigen Verschlüsselung für Microsoft Azure Storage (Vorschau) | Microsoft Azure" 
	description="Die Azure Storage-Clientvorschaubibliothek für .NET bietet Unterstützung für die clientseitige Verschlüsselung und die Integration in Azure Key Vault. Die clientseitige Verschlüsselung bietet maximale Sicherheit für Ihre Azure Storage-Anwendungen, da Ihre Zugriffsschlüssel im Dienst niemals verfügbar sind. Die clientseitige Verschlüsselung ist für Blobs, Warteschlangen und Tabellen verfügbar." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="carolz" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/18/2015" 
	ms.author="tamram"/>


# Erste Schritte mit der clientseitigen Verschlüsselung für Microsoft Azure Storage (Vorschau)

## Übersicht

Willkommen bei der [Vorschau der neuen Azure Storage-Clientbibliothek für .NET](https://www.nuget.org/packages/WindowsAzure.Storage/4.4.1-preview). Diese Vorschaubibliothek enthält neue Funktionen, mit denen Entwickler Daten in Clientanwendungen vor dem Hochladen in Azure Storage verschlüsseln und beim Herunterladen entschlüsseln können. Die Vorschaubibliothek unterstützt außerdem die Integration in Azure [Key Vault](http://azure.microsoft.com/services/key-vault/) für die Schlüsselverwaltung von Speicherkonten.

## Verschlüsselung und Entschlüsselung über das Umschlagverfahren

Die Prozesse bei der Verschlüsselung und Entschlüsselung folgen dem Umschlagverfahren.

### Verschlüsselung über das Umschlagverfahren

Die Verschlüsselung über das Umschlagverfahren funktioniert wie folgt:

1. Die Azure-Speicherclientbibliothek generiert einen Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK), bei dem es sich um einen einmalig verwendeten symmetrischen Schlüssel handelt.
2. Benutzerdaten werden mit diesem CEK verschlüsselt.
3. Der CEK wird dann mit dem Schlüsselverschlüsselungsschlüssel (Key Encryption Key, KEK) umschlossen (verschlüsselt). Der KEK wird anhand eines Schlüsselbezeichners identifiziert und kann ein asymmetrisches Schlüsselpaar oder ein symmetrischer Schlüssel sein. Er kann lokal verwaltet oder im Azure-Schlüsseltresor gespeichert werden. 
	
	Die Speicherclientbibliothek hat selbst nie Zugriff auf den KEK. Die Bibliothek ruft lediglich den Algorithmus für das Umschließen des Schlüssels aus, der vom Schlüsseltresor bereitgestellt wird. Benutzer können bei Bedarf benutzerdefinierte Anbieter für das Umschließen von Schlüsseln bzw. das Aufheben dieses Zustands verwenden.

4. Die verschlüsselten Daten werden dann in den Azure Storage-Dienst hochgeladen. Der umschlossene Schlüssel und einige zusätzliche Verschlüsselungsmetadaten werden entweder als Metadaten (in einem Blob) gespeichert oder mit den verschlüsselten Daten (Warteschlangennachrichten und Tabellenentitäten) interpoliert.

### Entschlüsselung über das Umschlagverfahren

Die Entschlüsselung über das Umschlagverfahren funktioniert wie folgt:

1. Die Clientbibliothek geht davon aus, dass der Benutzer den Schlüsselverschlüsselungschlüssel (Key Encryption Key, KEK) entweder lokal oder in Azure-Schlüsseltresoren verwaltet. Der Benutzer muss den spezifischen Schlüssel, der für die Verschlüsselung verwendet wurde, nicht kennen. Stattdessen kann ein Schlüsselresolver eingerichtet und verwendet werden, der verschiedene Schlüsselkennungen in Schlüssel auflöst.
2. Die Clientbibliothek lädt die verschlüsselten Daten zusammen mit sämtlichem Verschlüsselungsmaterial herunter, das für den Dienst gespeichert ist.
3. Der umschlossene Inhaltsverschlüsselungsschlüssel (Content Encryption Key, CEK) wird dann mithilfe des Schlüsselverschlüsselungschlüssels (Key Encryption Key, KEK) entpackt. Die Clientbibliothek hat wiederum keinen Zugriff auf den KEK. Sie ruft einfach den benutzerdefinierten Entpackungsalgorithmus oder den Entpackungsalgorithmus des Schlüsseltresoranbieters auf.
4. Der Inhaltsverschlüsselungsschlüssel (CEK) wird dann zum Entschlüsseln der verschlüsselten Benutzerdaten verwendet.

## Verschlüsselungsmechanismus

Die Speicherclientbibliothek verwendet [AES](http://en.wikipedia.org/wiki/Advanced_Encryption_Standard), um Benutzerdaten zu verschlüsseln. Der [CBC-Modus (Blockchiffreverkettung, Cipher Block Chaining)](http://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) mit AES wird verwendet. Da jeder Dienst eine andere Funktionsweise aufweist, werden die Dienste hier erörtert.

### Blobs (in englischer Sprache)

In der Vorschauversion unterstützt die Clientbibliothek nur die Verschlüsselung vollständiger Blobs. Die Verschlüsselung wird unterstützt, wenn die Benutzer **UploadFrom***-Methoden oder **BlobWriteStream** verwenden. Es werden sowohl vollständige Downloads als auch Downloads von Bereichen unterstützt.

Bei der Verschlüsselung generiert die Clientbibliothek einen zufälligen Initialisierungsvektor (IV) mit einer Größe von 16 Byte zusammen mit einem zufälligen Inhaltsverschlüsselungsschlüssel (CEK) mit einer Größe von 32 Byte. Mithilfe dieser Informationen wird die Umschlagverschlüsselung der Blobdaten ausgeführt. Der umschlossene CEK und einige zusätzliche Verschlüsselungsmetadaten werden dann als Blobmetadaten zusammen mit dem verschlüsselten Blob für den Dienst gespeichert.

> [AZURE.WARNING]Wenn Sie eigene Metadaten für den Blob bearbeiten oder hochladen, müssen Sie sicherstellen, dass diese Metadaten beibehalten werden. Wenn Sie neue Metadaten ohne diese Metadaten hochladen, gehen der umschlossene CEK, der IV und andere Metadaten verloren und der Blobinhalt wird nie wieder abrufbar sein.

Beim Herunterladen eines verschlüsselten Blobs wird der Inhalt des gesamten Blobs mit den **DownloadTo***/**BlobReadStream**-Hilfsmethoden abgerufen. Der umschlossene CEK wird entpackt und zusammen mit dem IV (in diesem Fall als Blobmetadaten gespeichert) verwendet, um die entschlüsselten Daten an die Benutzer zurückzugeben.

Beim Herunterladen eines beliebigen Bereichs (**DownloadRange***-Methoden) im verschlüsselten Blob wird der von den Benutzern angegebene Bereich angepasst, um eine kleine Menge zusätzlicher Daten abzurufen, die verwendet werden können, um den angeforderten Bereich erfolgreich zu entschlüsseln.

Alle Blobtypen (Blockblobs und Seitenblobs) können mit diesem Schema verschlüsselt/entschlüsselt werden.

### Warteschlangen

Da Warteschlangenmeldungen ein beliebiges Format aufweisen können, definiert die Clientbibliothek ein benutzerdefiniertes Format, das den Initialisierungsvektor (IV) und den verschlüsselten Inhaltsverschlüsselungsschlüssel (CEK) im Meldungstext enthält.

Bei der Verschlüsselung generiert die Clientbibliothek einen zufälligen IV mit einer Größe von 16 Byte zusammen mit einem zufälligen CEK mit einer Größe von 32 Byte. Mithilfe dieser Informationen wird die Umschlagverschlüsselung des Texts der Warteschlangenmeldung durchgeführt. Der umschlossene CEK und einige zusätzliche Verschlüsselungsmetadaten werden der verschlüsselten Warteschlangenmeldung dann hinzugefügt. Diese geänderte Meldung (siehe unten) wird für den Dienst gespeichert.

	<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Bei der Entschlüsselung wird der umschlossene Schlüssel aus der Warteschlangenmeldung extrahiert und entpackt. Der Initialisierungsvektor wird ebenfalls aus der Warteschlangenmeldung extrahiert und zusammen mit dem entpackten Schlüssel verwendet, um die Daten der Warteschlangenmeldung zu entschlüsseln. Beachten Sie, dass die Verschlüsselungsmetadaten eine geringe Größe aufweisen (weniger als 500 Byte). Dies wird zwar für die 64-KB-Begrenzung für eine Warteschlangenmeldung angerechnet, die Auswirkungen sollten vertretbar sein.

### Tabellen

In der Vorschauversion unterstützt die Clientbibliothek die Verschlüsselung von Entitätseigenschaften für Insert- und Replace-Vorgänge.

>[AZURE.NOTE]Das Zusammenführen wird derzeit nicht unterstützt. Da eine Teilmenge der Eigenschaften möglicherweise bereits mit einem anderen Schlüssel verschlüsselt wurde, führen das einfache Zusammenführen der neuen Eigenschaften und das Aktualisieren der Metadaten zu Datenverlusten. Das Zusammenführen erfordert entweder zusätzliche Dienstaufrufe, um die bereits vorhandene Entität aus dem Dienst zu lesen, oder die Verwendung eines neuen Schlüssels pro Eigenschaft. Beide Verfahren sind aus Leistungsgründen nicht geeignet.

Die Verschlüsselung von Tabellendaten funktioniert wie folgt:

1. Die Benutzer geben die Eigenschaften an, die verschlüsselt werden sollen.
2. Die Clientbibliothek generiert einen zufälligen Initialisierungsvektor (IV) mit einer Größe von 16 Byte zusammen mit einem zufälligen Inhaltsverschlüsselungsschlüssel (CEK) mit einer Größe von 32 Byte für jede Entität und führt die Umschlagverschlüsselung für die einzelnen Eigenschaften durch, indem ein neuer IV pro Eigenschaft abgeleitet wird.
3. Der umschlossene CEK und einige zusätzliche Verschlüsselungsmetadaten werden dann als zwei zusätzliche reservierte Eigenschaften gespeichert. Die erste reservierte Eigenschaft ("_ClientEncryptionMetadata1") ist eine Zeichenfolgeneigenschaft, die die Informationen über den IV, die Version und den umschlossenen Schlüssel enthält. Die andere reservierte Eigenschaft ("_ClientEncryptionMetadata2") ist eine binäre Eigenschaft, die die Informationen zu den Eigenschaften enthält, die verschlüsselt werden.
4. Aufgrund dieser zusätzlichen reservierten Eigenschaften, die für die Verschlüsselung erforderlich sind, verfügen die Benutzer jetzt möglicherweise nur über 250 benutzerdefinierte Eigenschaften anstelle von 252. Die Gesamtgröße der Entität muss weniger als 1 MB betragen.

Beachten Sie, dass nur Zeichenfolgeneigenschaften verschlüsselt werden können. Wenn andere Typen von Eigenschaften verschlüsselt werden sollen, müssen sie in Zeichenfolgen konvertiert werden.

Für Tabellen müssen die Benutzer zusätzlich zur Verschlüsselungsrichtlinie die Eigenschaften angeben, die verschlüsselt werden sollen. Dies kann erfolgen, indem ein [EncryptProperty\]-Attribut (für POCO-Entitäten, die von "TableEntity" abgeleitet werden) oder ein Verschlüsselungsresolver in den Anforderungsoptionen angegeben werden. Ein Verschlüsselungsresolver ist ein Delegat, der einen Partitionsschlüssel, einen Zeilenschlüssel und einen Eigenschaftennamen annimmt und einen booleschen Wert zurückgibt, der angibt, ob die Eigenschaft verschlüsselt werden soll. Bei der Verschlüsselung verwendet die Clientbibliothek diese Informationen, um zu entscheiden, ob eine Eigenschaft beim Schreiben in das Netzwerk verschlüsselt werden soll. Der Delegat bietet zudem die Möglichkeit einer Logik bezüglich der Verschlüsselung der Eigenschaften. (Beispiel: Wenn X, dann wird Eigenschaft A verschlüsselt, andernfalls werden die Eigenschaften A und B verschlüsselt.) Beachten Sie, dass es nicht notwendig ist, diese Informationen beim Lesen oder Abfragen von Entitäten bereitzustellen.

### Batchvorgänge

Bei Batchvorgängen wird derselbe KEK für alle Zeilen in einem Batchvorgang verwendet, da die Clientbibliothek nur ein Optionenobjekt (und somit eine Richtlinie/einen KEK) pro Batchvorgang zulässt. Allerdings generiert die Clientbibliothek intern einen neuen zufälligen IV und einen zufälligen CEK pro Zeile im Batch . Die Benutzer können auch verschiedene Eigenschaften für jeden Vorgang im Batch verschlüsseln, indem dieses Verhalten im Verschlüsselungsresolver definiert wird.

### Abfragen

Zum Ausführen von Abfragevorgängen müssen Sie einen Schlüsselresolver angeben, der alle Schlüssel im Resultset auflösen kann. Wenn eine im Abfrageergebnis enthaltene Entität nicht in einen Anbieter aufgelöst werden kann, löst die Clientbibliothek einen Fehler aus. Für jede Abfrage, die serverseitige Projektionen ausführt, fügt die Clientbibliothek den ausgewählten Spalten standardmäßig die spezifischen Verschlüsselungsmetadateneigenschaften ("_ClientEncryptionMetadata1" und_ClientEncryptionMetadata2) hinzu.

## Azure-Schlüsseltresor

Der Azure-Schlüsseltresor (Vorschau) unterstützt Sie dabei, kryptografische und geheime Schlüssel zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Durch Verwenden des Azure-Schlüsseltresors können Benutzer Schlüssel und geheime Schlüssel (beispielsweise Authentifizierungsschlüssel, Schlüssel für Speicherkonten, Datenverschlüsselungsschlüssel, PFX-Dateien und Kennwörter) verschlüsseln, indem sie durch Hardwaresicherheitsmodule (HSMs) geschützte Schlüssel verwenden. Weitere Informationen finden Sie unter [Was ist der Azure-Schlüsseltresor?](../articles/key-vault-whatis.md).

Die Speicherclientbibliothek verwendet die Schlüsseltresor-Kernbibliothek, um ein gemeinsames Framework zum Verwalten von Schlüsseln innerhalb von Azure bereitzustellen. Ein zusätzlicher Vorteil für die Benutzer besteht in der Verwendung der Schlüsseltresor-Erweiterungsbibliothek. Die Erweiterungsbibliothek bietet nützliche Funktionen für die einfache und nahtlose Nutzung von lokalen und Cloudschlüsselanbietern von symmetrischen/RSA-Schlüsseln sowie für Aggregation und Zwischenspeicherung.

### Schnittstelle und Abhängigkeiten

Es gibt drei Schlüsseltresorpakete:

- "Microsoft.Azure.KeyVault.Core" enthält "IKey" und "IKeyResolver". Dies ist ein kleines Paket ohne Abhängigkeiten. Die Speicherclientbibliotheken für .NET und Windows Phone definieren dies als Abhängigkeit.
- "Microsoft.Azure.KeyVault" enthält den REST-Client des Schlüsseltresors.
- "Microsoft.Azure.KeyVault.Extensions" enthält Erweiterungscode, der Implementierungen von kryptografischen Algorithmen sowie einen RSA-Schlüssel und einen symmetrischen Schlüssel beinhaltet. Dieser hängt von den Namespaces "Core" und "KeyVault" ab und bietet Funktionen zum Definieren eines Aggregatresolvers (wenn die Benutzer mehrere Schlüsselanbieter verwenden möchten) und eines Cacheschlüsselresolvers. Obwohl die Speicherclientbibliothek nicht direkt von diesem Paket abhängig ist, benötigen die Benutzer dieses Paket, wenn sie den Azure-Schlüsseltresor zum Speichern ihrer Schlüssel verwenden möchten oder wenn sie die Schlüsseltresorerweiterungen verwenden möchten, um die lokalen und Cloudkryptografieanbieter zu nutzen.

Der Schlüsseltresor ist für hochwertige Hauptschlüssel ausgelegt und der Begrenzungsdrosselung über den Schlüsseltresor liegt dieses Konzept zugrunde. Bei der Durchführung von clientseitiger Verschlüsselung mit dem Schlüsseltresor besteht die bevorzugte Methode darin, symmetrische Hauptschlüssel zu verwenden, die als geheime Schlüssel im Schlüsseltresor gespeichert sind und lokal zwischengespeichert werden. Die Benutzer müssen wie folgt vorgehen:

1. Erstellen Sie offline einen geheimen Schlüssel und laden Sie ihn in den Schlüsseltresor hoch.
2. Verwenden Sie den Basisbezeichner des geheimen Schlüssels als Parameter, um die aktuelle Version des geheimen Schlüssels für die Verschlüsselung aufzulösen und diese Informationen lokal zwischenzuspeichern. Verwenden Sie "CachingKeyResolver" für die Zwischenspeicherung. Es wird nicht erwartet, dass die Benutzer eigene Logik für das Zwischenspeichern implementieren.
3. Verwenden Sie beim Erstellen der Verschlüsselungsrichtlinie den Zwischenspeicherungsresolver als Eingabe.

Weitere Informationen zur Verwendung des Schlüsseltresors finden Sie in den [Beispielen für Verschlüsselungscode](https://github.com/Azure/azure-storage-net/tree/preview/Samples/GettingStarted/EncryptionSamples).

### Bewährte Methoden

Die Verschlüsselungsunterstützung steht nur in den Speicherclientbibliotheken für .NET und Windows Phone zur Verfügung. Die Windows-Runtime unterstützt derzeit keine Verschlüsselung. Darüber hinaus werden für Windows Phone zurzeit keine Schlüsseltresorerweiterungen unterstützt. Wenn Sie die Speicherclientverschlüsselung auf dem Telefon verwenden möchten, müssen Sie eigene Schlüsselanbieter implementieren. Darüber hinaus wird aufgrund einer Einschränkung auf der .NET-Plattform für Windows Phone die Seiten-BLOB-Verschlüsselung derzeit nicht auf Windows Phone unterstützt.

>[AZURE.IMPORTANT]Beachten Sie bei Verwendung der Vorschaubibliothek die folgenden wichtigen Punkte:
>
>- Verwenden Sie die Vorschaubibliothek nicht für Produktionsdaten. In Zukunft wirken sich Änderungen an der Bibliothek auf die verwendeten Schemas aus. Die Entschlüsselung von Daten, die mit der Vorschaubibliothek verschlüsselt wurden, wird in zukünftigen Versionen nicht gewährleistet.  
>- Verwenden Sie beim Lesen aus einem verschlüsselten Blob oder beim Schreiben in diesen Befehle zum Hochladen des vollständigen Blobs und zum Herunterladen des bereichsbasierten oder vollständigen Blobs. Vermeiden Sie beim Schreiben in einen verschlüsselten Blob Protokollvorgänge wie z. B. "Put Block", "Put Block List", "Write Pages" oder "Clear Pages". Andernfalls wird der verschlüsselte Blob möglicherweise beschädigt und kann nicht mehr gelesen werden.
>- Für Tabellen gilt eine ähnliche Einschränkung. Achten Sie darauf, dass Sie beim Aktualisieren verschlüsselter Eigenschaften auch die Verschlüsselungsmetadaten aktualisieren.
>- Wenn Sie Metadaten für den verschlüsselten Blob festlegen, werden die für die Entschlüsselung erforderlichen verschlüsselungsbezogenen Metadaten möglicherweise überschrieben, da das Festlegen von Metadaten kein additiver Vorgang ist. Dies gilt auch für Momentaufnahmen: Geben Sie während der Erstellung einer Momentaufnahme eines verschlüsselten Blobs keine Metadaten an.


## Client-API/Schnittstelle

Beim Erstellen eines EncryptionPolicy-Objekts können die Benutzer nur einen Schlüssel ("IKey"-Implementierung), nur einen Resolver (IKeyResolver-Implementierung) oder beides bereitstellen. "IKey" ist der grundlegende Schlüsseltyp, der mit einer Schlüsselkennung gekennzeichnet wird und der die Logik für das Einschließen/Entpacken bereitstellt. "IKeyResolver" wird verwendet, um einen Schlüssel während des Entschlüsselungsvorgangs aufzulösen. Er definiert eine ResolveKey-Methode, die bei einer angegebenen Schlüsselkennung einen "IKey" zurückgibt. Dies ermöglicht den Benutzern die Auswahl zwischen mehreren Schlüsseln, die an mehreren Speicherorten verwaltet werden.

- Für die Verschlüsselung wird immer der Schlüssel verwendet. Ein fehlender Schlüssel führt zu einem Fehler.
- Für die Entschlüsselung gilt:
	- Der Schlüsselresolver wird aufgerufen, wenn er angegeben wurde, um den Schlüssel abzurufen. Wenn der Resolver angegeben wird, dieser aber nicht über eine Zuordnung für die Schlüsselkennung verfügt, wird ein Fehler ausgelöst.
	- Wenn kein Resolver, aber ein Schlüssel angegeben wird, wird die Schlüsselkennung für den Schlüssel mit den Angaben abgeglichen, die für den Dienst gespeichert sind.

Die [Verschlüsselungsbeispiele](https://github.com/Azure/azure-storage-net/tree/preview/Samples/GettingStarted/EncryptionSamples) veranschaulichen ein ausführlicheres End-to-End-Szenario für Blobs, Warteschlangen und Tabellen und für die Schlüsseltresorintegration.

### Blobs (in englischer Sprache)

Die Benutzer erstellen ein **BlobEncryptionPolicy**-Objekt und legen es in den Anforderungsoptionen fest (über die API oder auf einer Clientebene unter Verwendung von **DefaultRequestOptions**). Alles Weitere wird intern von der Clientbibliothek behandelt.

	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);
  
 	// Set the encryption policy on the request options.
 	BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };
  
 	// Upload the encrypted contents to the blob.
 	blob.UploadFromStream(stream, size, null, options, null);
  
 	// Download and decrypt the encrypted contents from the blob.
 	MemoryStream outputStream = new MemoryStream();
 	blob.DownloadToStream(outputStream, null, options, null);

### Warteschlangen

Die Benutzer erstellen ein **QueueEncryptionPolicy**-Objekt und legen es in den Anforderungsoptionen fest (über die API oder auf einer Clientebene unter Verwendung von **DefaultRequestOptions**). Alles Weitere wird intern von der Clientbibliothek behandelt.


	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);
  
 	// Add message
 	QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 	queue.AddMessage(message, null, null, options, null);
  
 	// Retrieve message
 	CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);

### Tabellen

Zusätzlich zum Erstellen einer Verschlüsselungsrichtlinie und zum Festlegen für die Anforderungsoptionen müssen die Benutzer einen **EncryptionResolver** in **TableRequestOptions** angeben oder Attribute für die Entität festlegen.

#### Verwenden des Resolvers


	// Create the IKey used for encryption.
 	RsaKey key = new RsaKey("private:key1" /* key identifier */);
  
 	// Create the encryption policy to be used for upload and download.
 	TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);
  
 	TableRequestOptions options = new TableRequestOptions() 
 	{ 
    	EncryptionResolver = (pk, rk, propName) =>
     	{
        	if (propName == "foo")
         	{
            	return true;
         	}
         	return false;
     	},
     	EncryptionPolicy = policy
 	};
  
 	// Insert Entity
 	currentTable.Execute(TableOperation.Insert(ent), options, null);
  
 	// Retrieve Entity
 	// No need to specify an encryption resolver for retrieve
 	TableRequestOptions retrieveOptions = new TableRequestOptions() 
 	{
    	EncryptionPolicy = policy
 	};
  
 	TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 	TableResult result = currentTable.Execute(operation, retrieveOptions, null);

#### Verwenden von Attributen

Wenn die Entität "TableEntity" implementiert, können die Eigenschaften, wie bereits erwähnt, mit dem [EncryptProperty\]-Attribut ergänzt werden, statt den "EncryptionResolver" anzugeben.

	[EncryptProperty]
 	public string EncryptedProperty1 { get; set; }

## Nächste Schritte

[Clientseitige Verschlüsselung für Microsoft Azure Storage – Vorschau](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview.aspx) [Azure-Speicherclientbibliothek für .NET – NuGet-Paket](http://www.nuget.org/packages/WindowsAzure.Storage/4.4.0-preview) herunterladen [Azure-Speicherclientbibliothek für .NET-Quellcode](https://github.com/Azure/azure-storage-net/tree/preview) von GitHub herunterladen NuGet-Pakete [Core](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](http://www.nuget.org/packages/Microsoft.Azure.KeyVault/) und [Extensions](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/) für den Azure-Schlüsseltresor herunterladen Lesen Sie die [Dokumentation zum Azure-Schlüsseltresor](../articles/key-vault-whatis.md)

<!---HONumber=July15_HO2-->