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

# Konfigurieren von Azure-Verbindungszeichenfolgen

## Übersicht
Eine Verbindungszeichenfolge enthält die Parameter, die für den Zugriff auf Ihr Speicherkonto in Azure erforderlich sind. Sie können eine Verbindungszeichenfolge folgendermaßen konfigurieren:

- Stellen Sie eine Verbindung mit dem Azure-Speicheremulator her, während Sie Ihren Dienst oder eine Anwendung lokal testen.

- Verbinden Sie sich über die Standardendpunkte für die Speicherdienste mit einem Speicherkonto in Azure.

- Verbinden Sie sich über explizite Endpunkte für die Speicherdienste mit einem Speicherkonto in Azure.

Wenn es sich bei Ihrer Anwendung um einen Cloud-Dienst in Azure handelt, bietet es sich an, die Verbindungszeichenfolge im [Azure-Dienstkonfigurationsschema (.cscfg-Datei)](https://msdn.microsoft.com/library/ee758710.aspx) abzulegen. Wenn Ihre Anwendung in einer anderen Umgebung ausgeführt wird (z. B. auf dem Desktop), sollten Sie die Verbindungszeichenfolge in einer app.config-Datei oder einer anderen Konfigurationsdatei speichern. Über die Azure-Klasse "CloudConfigurationManager" können Sie zur Laufzeit auf die Verbindungszeichenfolge zugreifen, unabhängig davon, wo sie ausgeführt wird.

##Herstellen einer Verbindung mit dem Speicheremulator

Der Speicheremulator ist ein lokales Konto mit einem bekannten Namen und Schlüssel. Da der Kontoname und der Schlüssel für alle Benutzer identisch sind, können Sie ein verkürztes Zeichenfolgenformat verwenden, um innerhalb einer Verbindungszeichenfolge auf den Speicheremulator zu verweisen. Setzen Sie den Wert der Verbindungszeichenfolge auf `UseDevelopmentStorage=true`.

Sie können auch einen HTTP-Proxy angeben, der beim Testen des Dienstes anhand des Speicheremulators verwendet werden soll. Dies kann zum Beobachten von HTTP-Anforderungen und -Antworten nützlich sein, während Sie Vorgänge anhand der Speicherdienste debuggen. Fügen Sie der Verbindungszeichenfolge die Option "DevelopmentStorageProxyUri" hinzu, um einen Proxy anzugeben, und legen Sie ihren Wert auf den Proxy-URI fest. Hier sehen Sie beispielsweise eine Verbindungszeichenfolge, die auf den Speicheremulator verweist und einen HTTP-Proxy konfiguriert:

    UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri

##Herstellen einer Verbindung mit einem Speicherkonto in Azure

Zum Definieren einer Verbindungszeichenfolge für ein Speicherkonto in Azure gibt es folgende Möglichkeiten:

- Nehmen Sie die Standardendpunkte für die Speicherdienste an. Dies ist die einfachste Möglichkeit zum Erstellen einer Verbindungszeichenfolge. Wenn Sie dieses Verbindungszeichenfolgenformat verwenden, müssen Sie Folgendes beachten: 

- Geben Sie explizite Endpunkte für die Speicherdienste an. Mit dieser Option können Sie eine komplexere Verbindungszeichenfolge erstellen. Wenn Sie dieses Zeichenfolgenformat verwenden, können Sie Speicherdienstendpunkte festlegen, die einen benutzerdefinierten Domänennamen enthalten, oder die Informationsfreigabe für eine signaturbasierte Verbindungszeichenfolge für den gemeinsamen Zugriff auf ein Mindestmaß beschränken.


> [AZURE.NOTE] Die Azure-Speicherdienste unterstützen sowohl HTTP als auch HTTPS, die Verwendung von HTTPS wird jedoch dringend empfohlen.

##Erstellen einer Verbindungszeichenfolge mit Standardendpunkten

Um eine Verbindungszeichenfolge zu erstellen, welche auf den Standardendpunkten für den Speicherdienst beruht, verwenden Sie das folgende Format für die Verbindungszeichenfolge. Geben Sie an, ob Sie über HTTP oder HTTPS eine Verbindung mit dem Speicherkonto herstellen möchten, ersetzen Sie "MyAccountName" durch den Namen Ihres Speicherkontos, und ersetzen Sie "MyAccountKey" durch Ihren Kontozugriffsschlüssel:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Ihre Verbindungszeichenfolge sollte der folgenden Beispiel-Verbindungszeichenfolge ähneln:

    DefaultEndpointsProtocol=https;AccountName=storagesample;AccountKey=KWPLd0rpW2T0U7K2pVpF8rYr1BgYtR7wYQk33AYiXeUoquiaY6o0TWqduxmPHlqeCNZ3LU0DHptbeIHy5l/Yhg==

##Erstellen einer Verbindungszeichenfolge mit expliziten Endpunkten

Möglicherweise möchten Sie die Dienstendpunkte in der Verbindungszeichenfolge aus den folgenden Gründen explizit angeben:

- Sie haben einen benutzerdefinierten Domänennamen für Ihr Speicherkonto beim BLOB-Dienst registriert.

- Sie verwenden eine SAS (Shared Access Signature) für den Zugriff auf Speicherressourcen.

###Angeben eines BLOB-Endpunkts mit einem benutzerdefinierten Domänennamen 
Wenn Sie einen benutzerdefinierten Domänennamen für die Verwendung mit dem BLOB-Dienst registriert haben, möchten Sie den BLOB-Endpunkt möglicherweise explizit in der Verbindungszeichenfolge konfigurieren. Der in der Verbindungszeichenfolge aufgeführte Endpunktwert wird verwendet, um die Anforderungs-URIs für den BLOB-Dienst zu erstellen. Ferner gibt er die Form aller URIs vor, die dem Code zurückgegeben werden. 

Um eine Verbindungszeichenfolge zu erstellen, die explizite Endpunkte festlegt, geben Sie für jeden Dienst den vollständigen Dienstendpunkt einschließlich der Protokollspezifikation (HTTP oder HTTPS) an. Verwenden Sie dabei folgendes Format:

    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;[credentials]

Wenn Sie Dienstendpunkte explizit angeben, haben Sie zwei Möglichkeiten zum Angeben von Anmeldeinformationen. Sie können, den Kontonamen und -schlüssel (`AccountName=myAccountName;AccountKey=myAccountKey`) wie im vorherigen Abschnitt gezeigt angeben, oder Sie können **eine SAS (Shared Access Signature) angeben**, wie im Abschnitt "Angeben von Endpunkten mit einer SAS (Shared Access Signature)" gezeigt wird. Wenn Sie den Kontonamen und -schlüssel angeben, lautet das vollständige Zeichenfolgenformat folgendermaßen:
    BlobEndpoint=myBlobEndpoint;QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;AccountName=myAccountName;AccountKey=myAccountKey

Sie können Endpunkte für BLOB, Tabelle und Warteschlange in einer Verbindungszeichenfolge angeben. Sie müssen mindestens einen Endpunkt, jedoch nicht unbedingt alle drei angeben. Wenn Sie beispielsweise eine Verbindungszeichenfolge zur Verwendung mit einem benutzerdefinierten BLOB-Endpunkt erstellen, ist die Angabe der Endpunkte für Warteschlange und Tabelle optional. Wenn Sie die Endpunkte für Warteschlange und Tabelle in der Verbindungszeichenfolge auslassen, beachten Sie, dass Sie anhand dieser Verbindungszeichenfolge nicht von Ihrem Code aus auf die Warteschlangen- und Tabellendienste zugreifen können.

###Angeben von Endpunkten mit einer SAS (Shared Access Signature) 
Sie können eine Verbindungszeichenfolge mit expliziten Endpunkten für den Zugriff auf Speicherressourcen über eine SAS erstellen. In diesem Fall können Sie die SAS anstelle des Kontonamens und der Schlüsselanmeldeinformationen als Teil der Verbindungszeichenfolge angeben. Das SAS-Token enthält Informationen über die Ressource, auf die zugegriffen werden soll, den Zeitraum, für den sie verfügbar ist, sowie die erteilten Berechtigungen. Weitere Informationen zu SAS finden Sie unter [Delegieren des Zugriffs mit einer SAS (Shared Access Signature)](https://msdn.microsoft.com/library/ee395415.aspx).

Um eine Verbindungszeichenfolge zu erstellen, die eine SAS enthält, geben Sie die Zeichenfolge im folgenden Format ein:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

Der Endpunkt kann entweder dem Standarddienstendpunkt oder einem benutzerdefinierten Endpunkt entsprechen. Die `base64Signature` entspricht dem Signaturteil einer SAS. Die Signatur ist ein HMAC, der mithilfe des SHA256-Algorithmus über StringToSign-Zeichenfolge und Schlüssel erstellt und anschließend mit Base64 codiert wird.

<!--HONumber=47-->
