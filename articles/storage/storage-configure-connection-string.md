<properties 
	pageTitle="Konfigurieren von Azure-Verbindungszeichenfolgen" 
	description="Erfahren Sie, wie Sie Verbindungszeichenfolgen zum Speicherkonto in Azure konfigurieren." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor="cgronlun"/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Konfigurieren von Azure Storage-Verbindungszeichenfolgen

## Übersicht

Eine Verbindungszeichenfolge enthält die Informationen, die für den programmgesteuerten Zugriff auf Ihr Speicherkonto in Azure erforderlich sind. Sie können eine Verbindungszeichenfolge zum Herstellen einer Verbindung mit Azure Storage folgendermaßen konfigurieren:

- Stellen Sie eine Verbindung mit dem Azure-Speicheremulator her, während Sie Ihren Dienst oder eine Anwendung lokal testen.

- Verbinden Sie sich über die Standardendpunkte für die Speicherdienste mit einem Speicherkonto in Azure.

- Verbinden Sie sich über explizite Endpunkte für die Speicherdienste mit einem Speicherkonto in Azure.

Wenn es sich bei Ihrer Anwendung um einen in Azure ausgeführten Cloud-Dienst handelt, speichern Sie die Verbindungszeichenfolge zumeist im [Azure-Dienstkonfigurationsschema (einer CSCFG-Datei)](https://msdn.microsoft.com/library/ee758710.aspx). Wenn Ihre Anwendung in einer anderen Umgebung ausgeführt wird (z. B. auf dem Desktop), speichern Sie die Verbindungszeichenfolge in der Regel in einer APP.CONFIG-Datei oder einer anderen Konfigurationsdatei. Über die Azure-Klasse  `CloudConfigurationManager` können Sie zur Laufzeit auf die Verbindungszeichenfolge zugreifen, unabhängig davon, wo sie ausgeführt wird.

## Erstellen einer Verbindungszeichenfolge mit dem Speicheremulator

Der Speicheremulator ist ein lokales Konto mit einem bekannten Namen und Schlüssel. Da der Kontoname und der Schlüssel für alle Benutzer identisch sind, können Sie ein verkürztes Zeichenfolgenformat verwenden, um innerhalb einer Verbindungszeichenfolge auf den Speicheremulator zu verweisen. Legen Sie den Wert der Verbindungszeichenfolge auf  `UseDevelopmentStorage=true` fest.

Sie können auch einen HTTP-Proxy angeben, der beim Testen des Dienstes anhand des Speicheremulators verwendet werden soll. Dies kann zum Beobachten von HTTP-Anforderungen und -Antworten nützlich sein, während Sie Vorgänge anhand der Speicherdienste debuggen. Fügen Sie der Verbindungszeichenfolge die Option  `DevelopmentStorageProxyUri` hinzu, um einen Proxy anzugeben, und legen Sie ihren Wert auf den Proxy-URI fest. Hier sehen Sie beispielsweise eine Verbindungszeichenfolge, die auf den Speicheremulator verweist und einen HTTP-Proxy konfiguriert:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

## Erstellen einer Verbindungszeichenfolge für ein Azure-Speicherkonto

Um eine Verbindungszeichenfolge für Ihr Azure-Speicherkonto zu erstellen, verwenden Sie das nachstehende Format der Verbindungszeichenfolge. Geben Sie an, ob Sie über HTTP oder HTTPS (empfohlen) eine Verbindung mit dem Speicherkonto herstellen möchten, ersetzen Sie  `myAccountName` durch den Namen Ihres Speicherkontos, und ersetzen Sie  `myAccountKey` durch Ihren Kontozugriffsschlüssel:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Ihre Verbindungszeichenfolge sollte der folgenden Beispiel-Verbindungszeichenfolge ähneln:

```        DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg==
```

> [AZURE.NOTE] Azure Storage unterstützt sowohl HTTP als auch HTTPS. Die Verwendung von HTTPS wird jedoch ausdrücklich empfohlen.
    
## Erstellen einer Verbindungszeichenfolge für einen bestimmten Speicherendpunkt

Sie können die Dienstendpunkte in Ihrer Verbindungszeichenfolge explizit angeben, wenn Folgendes gilt:

- Sie haben einen benutzerdefinierten Domänennamen für Ihr Speicherkonto beim Blob-Dienst registriert.
- Sie verwenden eine SAS (Shared Access Signature) für den Zugriff auf Speicherressourcen.

Um eine Verbindungszeichenfolge zu erstellen, die einen expliziten Blob-Endpunkt festlegt, geben Sie für jeden Dienst den vollständigen Dienstendpunkt einschließlich Protokollspezifikation (HTTP oder HTTPS) im folgenden Format an:

``` 
BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;FileEndpoint=myFileEndpoint;[Anmeldeinformationen]
```

Sie müssen mindestens einen, jedoch nicht unbedingt alle Dienstendpunkte angeben. Wenn Sie beispielsweise eine Verbindungszeichenfolge zur Verwendung mit einem benutzerdefinierten BLOB-Endpunkt erstellen, ist die Angabe der Endpunkte für Warteschlange und Tabelle optional. Wenn Sie die Endpunkte für Warteschlange und Tabelle in der Verbindungszeichenfolge auslassen, beachten Sie, dass Sie anhand dieser Verbindungszeichenfolge nicht von Ihrem Code aus auf die Warteschlangen- und Tabellendienste zugreifen können.

Wenn Sie Dienstendpunkte explizit angeben, haben Sie zwei Möglichkeiten zum Angeben von  `credentials`.

- Sie können den Kontonamen und -schlüssel angeben: `AccountName=myAccountName;AccountKey=myAccountKey` 
- Sie können eine SAS (Shared Access Signature) angeben: `SharedAccessSignature=base64Signature`

### Angeben eines Blob-Endpunkts mit einem benutzerdefinierten Domänennamen 

Wenn Sie einen benutzerdefinierten Domänennamen für die Verwendung mit dem Blob-Dienst registriert haben, möchten Sie den Blob-Endpunkt möglicherweise explizit in der Verbindungszeichenfolge konfigurieren. Der in der Verbindungszeichenfolge aufgeführte Endpunktwert wird verwendet, um die Anforderungs-URIs für den BLOB-Dienst zu erstellen. Ferner gibt er die Form aller URIs vor, die dem Code zurückgegeben werden. 

Eine Verbindungszeichenfolge für einen Blob-Endpunkt für eine benutzerdefinierte Domäne lautet ungefähr wie folgt:

```
DefaultEndpointsProtocol=https;BlobEndpoint=www.mydomain.com;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtB7wYQw33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIAy5l/Yhg== 
```

### Angeben eines Blob-Endpunkts mit einer SAS (Shared Access Signature) 

Sie können eine Verbindungszeichenfolge mit expliziten Endpunkten für den Zugriff auf Speicherressourcen über eine SAS erstellen. In diesem Fall können Sie die SAS anstelle des Kontonamens und der Schlüsselanmeldeinformationen als Teil der Verbindungszeichenfolge angeben. Das SAS-Token enthält Informationen über die Ressource, auf die zugegriffen werden soll, den Zeitraum, für den sie verfügbar ist, sowie die erteilten Berechtigungen. Weitere Informationen zu SAS finden Sie unter [Delegieren des Zugriffs mit einer SAS (Shared Access Signature)](https://msdn.microsoft.com/library/ee395415.aspx).

Um eine Verbindungszeichenfolge zu erstellen, die eine SAS enthält, geben Sie die Zeichenfolge im folgenden Format ein:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

Der Endpunkt kann entweder dem Standarddienstendpunkt oder einem benutzerdefinierten Endpunkt entsprechen. Die `base64Signature` entspricht dem Signaturteil einer SAS. Die Signatur ist ein HMAC, der mithilfe des SHA256-Algorithmus über StringToSign-Zeichenfolge und Schlüssel erstellt und anschließend mit Base64 codiert wird.

### Erstellen einer Verbindungszeichenfolge mit einem Endpunktsuffix

Zum Erstellen einer Verbindungszeichenfolge für den Speicherdienst in Regionen oder Instanzen mit anderen Endpunktsuffixen, z. B. für Azure China oder Azure Governance, verwenden Sie das folgende Format der Verbindungszeichenfolge. Geben Sie an, ob Sie über HTTP oder HTTPS (empfohlen) eine Verbindung mit dem Speicherkonto herstellen möchten, ersetzen Sie  `myAccountName` durch den Namen Ihres Speicherkontos und  `myAccountKey` durch Ihren Kontozugriffsschlüssel und  `mySuffix` durch das URI-Suffix:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Ihre Verbindungszeichenfolge sollte der folgenden Beispiel-Verbindungszeichenfolge ähneln:

	DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==;EndpointSuffix=core.chinacloudapi.cn;



<!--HONumber=52--> 