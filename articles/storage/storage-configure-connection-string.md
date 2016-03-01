<properties 
	pageTitle="Konfigurieren einer Verbindungszeichenfolge für Azure Storage | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine Verbindungszeichenfolge für ein Azure-Speicherkonto erstellen. Eine Verbindungszeichenfolge enthält die erforderlichen Informationen zum Authentifizieren des programmgesteuerten Zugriffs auf Ressourcen in einem Speicherkonto. Die Verbindungszeichenfolge kann Ihre Zugriffsschlüssel für ein Konto kapseln, das Sie besitzen, oder sie kann eine SAS für den Zugriff auf Ressourcen in einem Konto ohne den Zugriffsschlüssel enthalten."
	services="storage"
	documentationCenter=""
	authors="tamram"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="tamram"/>

# Konfigurieren von Azure Storage-Verbindungszeichenfolgen

## Übersicht

Eine Verbindungszeichenfolge enthält die Informationen, die für den programmgesteuerten Zugriff auf Azure Storage-Ressourcen erforderlich sind. Die Anwendung verwendet die Verbindungszeichenfolge, um für Azure Storage die Informationen zum Authentifizieren des Zugriffs bereitzustellen.

Sie können eine Verbindungszeichenfolge konfigurieren, um Folgendes zu tun:

- Stellen Sie eine Verbindung mit dem Azure-Speicheremulator her, während Sie Ihren Dienst oder eine Anwendung lokal testen.
- Verbinden Sie sich über die Standardendpunkte für die Speicherdienste mit einem Speicherkonto in Azure oder über explizite Endpunkte, die Sie definiert haben.
- Greifen Sie über eine SAS auf Ressourcen in einem Speicherkonto zu.

## Speichern der Verbindungszeichenfolge

Die Anwendung muss die Verbindungszeichenfolge speichern, um den Zugriff auf Azure Storage während der Ausführung zu authentifizieren. Es gibt mehrere Möglichkeiten, die Verbindungszeichenfolge zu speichern:

- Für eine Anwendung, die auf dem Desktop oder auf einem Gerät ausgeführt wird, können Sie die Verbindungszeichenfolge in einer app.config-Datei oder einer anderen Konfigurationsdatei speichern. Wenn Sie eine app.config-Datei verwenden, fügen Sie die Verbindungszeichenfolge zum Abschnitt **AppSettings** hinzu.
- Wenn Ihre Anwendung in einem Clouddienst in Azure ausgeführt wird, speichern Sie die Verbindungszeichenfolge zumeist im [Azure-Dienstkonfigurationsschema (CSCFG-Datei)](https://msdn.microsoft.com/library/ee758710.aspx). Fügen Sie die Verbindungszeichenfolge zum Abschnitt **ConfigurationSettings** der Dienstkonfigurationsdatei hinzu.

Das Speichern der Verbindungszeichenfolge in der Konfigurationsdatei erleichtert die Aktualisierung der Verbindungszeichenfolge, um zwischen dem Speicheremulator und einem Azure-Speicherkonto in der Cloud zu wechseln. Sie müssen die Verbindungszeichenfolge nur so bearbeiten, dass sie auf Ihr Speicherkonto verweist.

Über die [Microsoft Azure Configuration Manager](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)-Klasse können Sie zur Laufzeit auf die Verbindungszeichenfolge zugreifen, unabhängig davon, wo Ihre Anwendung ausgeführt wird.

## Erstellen einer Verbindungszeichenfolge mit dem Speicheremulator

[AZURE.INCLUDE [storage-emulator-connection-string-include](../../includes/storage-emulator-connection-string-include.md)]

Unter [Einsatz des Azure-Speicheremulators für Entwicklung und Tests](storage-use-emulator.md) finden Sie weitere Informationen zum Speicheremulator.

## Erstellen einer Verbindungszeichenfolge für ein Azure-Speicherkonto

Um eine Verbindungszeichenfolge für Ihr Azure-Speicherkonto zu erstellen, verwenden Sie das nachstehende Format der Verbindungszeichenfolge. Geben Sie an, ob Sie über HTTP oder HTTPS (empfohlen) eine Verbindung mit dem Speicherkonto herstellen möchten, ersetzen Sie `myAccountName` durch den Namen Ihres Speicherkontos, und ersetzen Sie `myAccountKey` durch Ihren Kontozugriffsschlüssel:

    DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey

Ihre Verbindungszeichenfolge sollte der folgenden Beispiel-Verbindungszeichenfolge ähneln:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>

> [AZURE.NOTE] Azure Storage unterstützt sowohl HTTP als auch HTTPS. Die Verwendung von HTTPS wird jedoch ausdrücklich empfohlen.

## Erstellen einer Verbindungszeichenfolge für einen bestimmten Speicherendpunkt

Sie können die Dienstendpunkte in Ihrer Verbindungszeichenfolge explizit angeben, wenn Folgendes gilt:

- Sie haben einen benutzerdefinierten Domänennamen für Ihr Speicherkonto beim Blob-Dienst registriert.
- Sie verwenden eine SAS (Shared Access Signature) für den Zugriff auf Speicherressourcen.

Um eine Verbindungszeichenfolge zu erstellen, die einen expliziten Blob-Endpunkt festlegt, geben Sie für jeden Dienst den vollständigen Dienstendpunkt einschließlich Protokollspezifikation (HTTP oder HTTPS) im folgenden Format an:

	BlobEndpoint=myBlobEndpoint;
	QueueEndpoint=myQueueEndpoint;
	TableEndpoint=myTableEndpoint;
	FileEndpoint=myFileEndpoint;
	[credentials]


Sie müssen mindestens einen, jedoch nicht unbedingt alle Dienstendpunkte angeben. Wenn Sie beispielsweise eine Verbindungszeichenfolge zur Verwendung mit einem benutzerdefinierten BLOB-Endpunkt erstellen, ist die Angabe der Endpunkte für Warteschlange und Tabelle optional. Wenn Sie die Endpunkte für Warteschlange und Tabelle in der Verbindungszeichenfolge auslassen, beachten Sie, dass Sie anhand dieser Verbindungszeichenfolge nicht von Ihrem Code aus auf die Warteschlangen- und Tabellendienste zugreifen können.

Wenn Sie in der Verbindungszeichenfolge Dienstendpunkte explizit angeben, haben Sie zwei Möglichkeiten zum Angeben von `credentials`.

- Sie können den Kontonamen und -schlüssel angeben: `AccountName=myAccountName;AccountKey=myAccountKey`
- Sie können eine SAS angeben: `SharedAccessSignature=base64Signature`

### Angeben eines Blob-Endpunkts mit einem benutzerdefinierten Domänennamen

Wenn Sie einen benutzerdefinierten Domänennamen für die Verwendung mit dem Blob-Dienst registriert haben, möchten Sie den Blob-Endpunkt möglicherweise explizit in der Verbindungszeichenfolge konfigurieren. Der in der Verbindungszeichenfolge aufgeführte Endpunktwert wird verwendet, um die Anforderungs-URIs für den BLOB-Dienst zu erstellen. Ferner gibt er die Form aller URIs vor, die dem Code zurückgegeben werden.

Eine Verbindungszeichenfolge für einen Blob-Endpunkt für eine benutzerdefinierte Domäne lautet ungefähr wie folgt:

	DefaultEndpointsProtocol=https;
	BlobEndpoint=www.mydomain.com;
	AccountName=storagesample;
	AccountKey=<account-key>


### Angeben eines Blob-Endpunkts mit einer SAS (Shared Access Signature)

Sie können eine Verbindungszeichenfolge mit expliziten Endpunkten für den Zugriff auf Speicherressourcen über eine SAS erstellen. In diesem Fall können Sie die SAS anstelle des Kontonamens und der Schlüsselanmeldeinformationen als Teil der Verbindungszeichenfolge angeben. Das SAS-Token enthält Informationen über die Ressource, auf die zugegriffen werden soll, den Zeitraum, für den sie verfügbar ist, sowie die erteilten Berechtigungen. Weitere Informationen zu SAS finden Sie unter [Delegieren des Zugriffs mit einer SAS (Shared Access Signature)](https://msdn.microsoft.com/library/ee395415.aspx).

Um eine Verbindungszeichenfolge zu erstellen, die eine SAS enthält, geben Sie die Zeichenfolge im folgenden Format ein:

    BlobEndpoint=myBlobEndpoint; QueueEndpoint=myQueueEndpoint;TableEndpoint=myTableEndpoint;SharedAccessSignature=base64Signature

Der Endpunkt kann entweder dem Standarddienstendpunkt oder einem benutzerdefinierten Endpunkt entsprechen. Die `base64Signature` entspricht dem Signaturteil einer SAS. Die Signatur ist ein HMAC, der mithilfe des SHA256-Algorithmus über StringToSign-Zeichenfolge und Schlüssel erstellt und anschließend mit Base64 codiert wird.

### Erstellen einer Verbindungszeichenfolge mit einem Endpunktsuffix

Zum Erstellen einer Verbindungszeichenfolge für den Speicherdienst in Regionen oder Instanzen mit anderen Endpunktsuffixen, z. B. für Azure China oder Azure Governance, verwenden Sie das folgende Format der Verbindungszeichenfolge. Geben Sie an, ob Sie über HTTP oder HTTPS (empfohlen) eine Verbindung mit dem Speicherkonto herstellen möchten, ersetzen Sie `myAccountName` durch den Namen Ihres Speicherkontos, `myAccountKey` durch Ihren Kontozugriffsschlüssel und `mySuffix` durch das URI-Suffix:


	DefaultEndpointsProtocol=[http|https];AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=mySuffix;


Ihre Verbindungszeichenfolge sollte der folgenden Beispiel-Verbindungszeichenfolge ähneln:

	DefaultEndpointsProtocol=https;
	AccountName=storagesample;
	AccountKey=<account-key>;
	EndpointSuffix=core.chinacloudapi.cn;

<!---HONumber=AcomDC_0218_2016-->