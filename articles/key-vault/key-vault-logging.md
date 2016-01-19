<properties
	pageTitle="Azure-Schlüsseltresor-Protokollierung | Microsoft Azure"
	description="Dieses Tutorial dient als Hilfe bei den ersten Schritten mit der Azure-Schlüsseltresor-Protokollierung."
	services="key-vault"
	documentationCenter=""
	authors="cabailey"
	manager="mbaldwin"
	tags="azure-resource-manager"/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/08/2016"
	ms.author="cabailey"/>

# Azure-Schlüsseltresor-Protokollierung #
Azure-Schlüsseltresor ist in den meisten Regionen verfügbar. Weitere Informationen finden Sie auf der Seite [Preisübersicht für Schlüsseltresor](../../../../pricing/details/key-vault/).

## Einführung  
Nachdem Sie einen oder mehrere Schlüsseltresore erstellt haben, möchten Sie vermutlich überwachen, wie, wann und von wem auf die Schlüsseltresore zugegriffen wird. Hierfür können Sie die Protokollierung für den Schlüsseltresor aktivieren, bei der Informationen im von Ihnen bereitgestellten Azure-Speicherkonto gespeichert werden. Ein neuer Container mit dem Namen **insights-logs-auditevent** wird für Ihr angegebenes Speicherkonto automatisch erstellt, und Sie können dieses Speicherkonto verwenden, um Protokolle für mehrere Schlüsseltresore zu sammeln.

Sie können auf Ihre Protokollinformationen spätestens zehn Minuten nach dem Schlüsseltresorvorgang zugreifen. In den meisten Fällen geht es aber schneller. Die Verwaltung der Protokolle im Speicherkonto ist Ihre Aufgabe:

- Verwenden Sie zum Schützen der Protokolle standardmäßige Azure-Zugriffssteuerungsmethoden, indem Sie einschränken, wer darauf zugreifen kann.
- Löschen Sie Protokolle, die im Speicherkonto nicht mehr aufbewahrt werden sollen. 

Nutzen Sie dieses Tutorial als Hilfe bei den ersten Schritten mit der Azure-Schlüsseltresor-Protokollierung, beim Erstellen Ihres Speicherkontos, Aktivieren der Protokollierung und Interpretieren der gesammelten Protokollierungsinformationen.


>[AZURE.NOTE]Dieses Tutorial enthält keine Anleitung zur Erstellung von Schlüsseltresoren, Schlüsseln oder geheimen Schlüsseln. Weitere Informationen hierzu finden Sie unter [Erste Schritte mit dem Azure-Schlüsseltresor](key-vault-get-started.md). Anleitungen für die plattformübergreifende Befehlszeilenschnittstelle finden Sie in [diesem entsprechenden Tutorial](key-vault-manage-with-cli.md).
>
>Derzeit können Sie den Azure-Schlüsseltresor nicht im Azure-Portal konfigurieren. Sie müssen stattdessen die Anweisungen für Azure PowerShell verwenden.

Eine Übersicht über den Azure-Schlüsseltresor finden Sie unter [Was ist der Azure-Schlüsseltresor?](key-vault-whatis.md)

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Vorhandenen Schlüsseltresor, der von Ihnen genutzt wird  
- Azure PowerShell, **mindestens Version 1.0.1** Um Azure PowerShell zu installieren und Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md). Wenn Sie Azure PowerShell bereits installiert haben und die Version nicht kennen, geben Sie über die Azure PowerShell-Konsole `(Get-Module azure -ListAvailable).Version` ein.  
- Ausreichend Speicherplatz unter Azure für Ihre Schlüsseltresor-Protokolle


## <a id="connect"></a>Verbindungsherstellung mit Ihren Abonnements ##

Starten Sie eine Azure PowerShell-Sitzung, und melden Sie sich mit dem folgenden Befehl bei Ihrem Azure-Konto an:

    Login-AzureRmAccount 

Geben Sie im Popup-Browserfenster den Benutzernamen und das Kennwort Ihres Azure-Kontos ein. Azure PowerShell ruft alle Abonnements ab, die diesem Konto zugeordnet sind, und verwendet standardmäßig das erste Abonnement.

Wenn Sie über mehrere Abonnements verfügen, müssen Sie unter Umständen ein bestimmtes Abonnement angeben, das zum Erstellen des Azure-Schlüsseltresors verwendet wurde. Geben Sie Folgendes ein, um die Abonnements für Ihr Konto anzuzeigen:

    Get-AzureRmSubscription

Geben Sie dann Folgendes ein, um das Abonnement anzugeben, das dem zu protokollierenden Schlüsseltresor zugeordnet ist:

    Set-AzureRmContext -SubscriptionId <subscription ID>

Weitere Informationen zum Konfigurieren von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).


## <a id="storage"></a>Erstellen eines neuen Speicherkontos für Ihre Protokolle ##

Sie können zwar ein vorhandenes Speicherkonto für Ihre Protokolle verwenden, aber wir erstellen ein neues Speicherkonto, das den Schlüsseltresor-Protokollen zugeordnet wird. Wir speichern die Details in einer Variablen mit dem Namen **sa**, damit wir sie später leicht angeben können.

Um die Verwaltung noch weiter zu vereinfachen, verwenden wir auch die gleiche Ressourcengruppe wie die Gruppe, die unseren Schlüsseltresor enthält. Im [Tutorial zu den ersten Schritten](key-vault-get-started.md) hat diese Ressourcengruppe den Namen **ContosoResourceGroup**, und wir nutzen auch wieder den Standort Ostasien. Ersetzen Sie diese Werte nach Bedarf durch Ihre eigenen Werte:

	$sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name ContosoKeyVaultLogs -Type Standard_LRS -Location 'East Asia'


>[AZURE.NOTE]Wenn Sie ein vorhandenes Speicherkonto verwenden möchten, muss dafür dasselbe Abonnement wie für den Schlüsseltresor verwendet werden. Außerdem muss das Ressourcen-Manager-Bereitstellungsmodell genutzt werden, nicht das klassische Bereitstellungsmodell.

## <a id="identify"></a>Identifizieren des Schlüsseltresors für Ihre Protokolle ##

In unserem Tutorial zu den ersten Schritten lautete der Name des Schlüsseltresors **ContosoKeyVault**. Wir nutzen den Namen also weiter und speichern die Details in einer Variablen mit dem Namen **kv**:

	$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'


## <a id="enable"></a>Aktivieren der Protokollierung ##

Zum Aktivieren der Protokollierung für den Schlüsseltresor verwenden wir das Set-AzureRmDiagnosticSetting-Cmdlet zusammen mit den Variablen, die wir für unser neues Speicherkonto und unseren Schlüsseltresor erstellt haben. Außerdem legen wir das Flag **-Enabled** auf **$true** und die Kategorie auf AuditEvent (einzige Kategorie für Schlüsseltresor-Protokollierung) fest:

   
	Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent


Die Ausgabe enthält z. B. Folgendes:

**Logs**

**Enabled : True**

**Category : AuditEvent**

So wird bestätigt, dass die Protokollierung für Ihren Schlüsseltresor jetzt aktiviert ist und Informationen in Ihrem Speicherkonto gespeichert werden.

Protokollierte Daten:

- Alle authentifizierten REST-API-Anforderungen werden protokolliert, z. B. auch Anforderungen, die aufgrund von Zugriffsberechtigungen, Systemfehlern oder fehlerhaften Anforderungen nicht erfolgreich sind.
- Vorgänge im Schlüsseltresor selbst, z. B. Erstellung, Löschung und Festlegung von Schlüsseltresor-Zugriffsrichtlinien und Aktualisierung von Schlüsseltresor-Attributen, beispielsweise Tags.
- Vorgänge mit Schlüsseln und geheimen Schlüsseln im Schlüsseltresor, z. B. Erstellung, Änderung oder Löschung dieser Schlüssel bzw. geheimen Schlüssel. Vorgänge wie das Signieren, Verifizieren, Verschlüsseln, Entschlüsseln, Umschließen und Aufheben der Umschließung von Schlüsseln, Abrufen von geheimen Schlüsseln, Auflisten von Schlüsseln und geheimen Schlüsseln sowie ihren Versionen.

Nicht authentifizierte Anforderungen werden nicht protokolliert.

## <a id="access"></a>Zugreifen auf Ihre Protokolle ##

Schlüsseltresor-Protokolle werden im Container **insights-logs-auditevent** im von Ihnen angegebenen Speicherkonto gespeichert. Geben Sie Folgendes ein, um alle Blobs in diesem Container aufzulisten:

    Get-AzureStorageBlob -Container 'insights-logs-auditevent' -Context $sa.Context

Die Ausgabe sieht etwa wie folgt aus:

****Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**


**Name**

**----**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json****
 

Wie Sie in dieser Ausgabe sehen, wird für die Blobs eine Benennungskonvention genutzt: **resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

Für die Werte für Datum und Uhrzeit wird UTC verwendet.

Da dasselbe Speicherkonto zum Erfassen von Protokollen für mehrere Ressourcen verwendet werden kann, ist die vollständige Ressourcen-ID im Blobnamen sehr hilfreich, um nur auf die benötigten Blobs zuzugreifen bzw. diese herunterzuladen. Zuerst wird aber beschrieben, wie Sie alle Blobs herunterladen.

Erstellen Sie zunächst einen Ordner zum Herunterladen der Blobs. Beispiel:

	New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force

Rufen Sie anschließend eine Liste mit allen Blobs ab:

	$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context

Leiten Sie diese Liste an Get-AzureStorageBlobContent um, um die Blobs in Ihren Zielordner herunterzuladen:

	$blobs | Get-AzureStorageBlobContent -Destination 'C:\Users\username\ContosoKeyVaultLogs'

Beim Ausführen dieses zweiten Befehls wird mit dem Trennzeichen **/** in den Blobnamen eine vollständige Ordnerstruktur unter dem Zielordner erstellt. Diese Struktur wird zum Herunterladen und Speichern der Blobs als Dateien verwendet.

Verwenden Sie Platzhalter, um Blobs selektiv herunterzuladen. Beispiel:

- Bei Verwendung mehrerer Schlüsseltresore und einem Download von Protokollen nur für einen Schlüsseltresor mit dem Namen CONTOSOKEYVAULT3:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3

- Verwenden Sie bei mehreren Ressourcengruppen und einem Download von Protokollen nur für eine Ressourcengruppe das Element `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'

- Wenn Sie alle Protokolle für den Monat Januar 2016 herunterladen möchten, verwenden Sie `-Blob '*/year=2016/m=01/*'`:

		Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'

Sie können sich nun ansehen, was in den Protokollen enthalten ist. Bevor Sie fortfahren, ist es ratsam, sich mit zwei weiteren Parametern für Get-AzureRmDiagnosticSetting vertraut zu machen:

- Zum Abfragen des Status von Diagnoseeinstellungen für Ihre Schlüsseltresor-Ressource: `Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
 
- Zum Deaktivieren der Protokollierung für Ihre Schlüsseltresor-Ressource: `Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`


## <a id="interpret"></a>Interpretieren der Schlüsseltresor-Protokolle ##

Einzelne Blobs werden als Text und formatiert als JSON-Blob gespeichert. Dies ist ein Beispiel für einen Protokolleintrag nach der Ausführung von `Get-AzureRmKeyVault -VaultName 'contosokeyvault'`:

	{
    	"records": 
    	[
        	{
        	    "time": "2016-01-05T01:32:01.2691226Z",
        	    "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
            	"operationName": "VaultGet",
            	"operationVersion": "2015-06-01",
            	"category": "AuditEvent",
            	"resultType": "Success",
            	"resultSignature": "OK",
            	"resultDescription": "",
            	"durationMs": "78",
            	"callerIpAddress": "104.40.82.76",
            	"correlationId": "",
            	"identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
            	"properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
        	}
    	]
	}


In der folgenden Tabelle sind die Feldnamen und Beschreibungen aufgeführt.


| Feldname | Beschreibung |
| ------------- |-------------|
| time | Datum und Uhrzeit (UTC)|
| resourceId | Azure-Ressourcen-Manager-Ressourcen-ID. Für Schlüsseltresor-Protokolle ist dies immer die Schlüsseltresor-Ressourcen-ID.|
| operationName | Name des Vorgangs, wie in der folgenden Tabelle beschrieben|
| operationVersion | Vom Client angeforderte REST-API-Version|
| category | Für Schlüsseltresor-Protokolle ist AuditEvent der einzige verfügbare Wert|
| resultType | Ergebnis der REST-API-Anforderung|
| resultSignature | HTTP-Status|
| resultDescription | Zusätzliche Beschreibung zum Ergebnis, falls verfügbar|
| durationMs | Verarbeitungsdauer der REST-API-Anforderung in Millisekunden. Die Netzwerklatenz ist hierin nicht enthalten, sodass die auf der Clientseite gemessene Zeit unter Umständen nicht mit diesem Zeitraum übereinstimmt.|
| callerIpAddress | IP-Adresse des Clients, der die Anforderung gestellt hat|
| correlationId | Optionale GUID, die vom Client zum Korrelieren von clientseitigen Protokollen mit dienstseitigen Protokollen (Schlüsseltresor) übergeben werden kann|
| Identität | Identität des Tokens, das beim Erstellen der REST-API-Anforderung angegeben wurde. Dies ist normalerweise ein „Benutzer“, ein Dienstprinzipal oder die Kombination „Benutzer + appId“, wie bei einer Anforderung, die auf einem Azure PowerShell-Cmdlet basiert.|
| Eigenschaften | Dieses Feld enthält je nach Vorgang verschiedene Informationen (operationName). In den meisten Fällen enthält es Clientinformationen (vom Client übergebene Zeichenfolge „useragent“), den genauen REST-API-Anforderungs-URI und den HTTP-Statuscode. Wenn ein Objekt als Ergebnis einer Anforderung (z. B. KeyCreate oder VaultGet) zurückgegeben wird, enthält es außerdem den Schlüssel-URI (als „id“), Tresor-URI oder URI des geheimen Schlüssels.|

 


Die Feldwerte unter **operationName** liegen im ObjectVerb-Format vor. Beispiel:

- Alle Schlüsseltresor-Vorgänge verfügen über das Format „Vault`<action>`“, z. B. `VaultGet` und `VaultCreate`. 

- Alle Schlüsselvorgänge verfügen über das Format „Key`<action>`“, z. B. `KeySign` und `KeyList`.

- Alle Vorgänge mit geheimen Schlüsseln verfügen über das Format „Secret`<action>`“, z. B. `SecretGet` und `SecretListVersions`.

Die folgende Tabelle enthält das operationName-Element und den entsprechenden REST-API-Befehl.

| operationName | REST-API-Befehl |
| ------------- |-------------|
| Authentifizierung | Über Azure Active Directory-Endpunkt|
| VaultGet | [Abrufen von Informationen über einen Schlüsseltresor](https://msdn.microsoft.com/de-DE/library/azure/mt620026.aspx)|
| VaultPut | [Erstellen oder Aktualisieren eines Schlüsseltresors](https://msdn.microsoft.com/de-DE/library/azure/mt620025.aspx)|
| VaultDelete | [Löschen eines Schlüsseltresors](https://msdn.microsoft.com/de-DE/library/azure/mt620022.aspx)|
| VaultPatch | [Erstellen oder Aktualisieren eines Schlüsseltresors](https://msdn.microsoft.com/library/azure/mt620025.aspx)|
| VaultList | [Auflisten aller wichtigen Depots in einer Ressourcengruppe](https://msdn.microsoft.com/de-DE/library/azure/mt620027.aspx)|
| KeyCreate | [Erstellen eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn903634.aspx)|
| KeyGet | [Abrufen von Informationen zu einem Schlüssel](https://msdn.microsoft.com/de-DE/library/azure/dn878080.aspx)|
| KeyImport | [Importieren eines Schlüssels in einen Tresor](https://msdn.microsoft.com/de-DE/library/azure/dn903626.aspx)|
| KeyBackup | [Sichern eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn878058.aspx)|
| KeyDelete | [Löschen eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn903611.aspx)|
| KeyRestore | [Wiederherstellen eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn878106.aspx)|
| KeySign | [Melden Sie sich mit einem Schlüssel an](https://msdn.microsoft.com/de-DE/library/azure/dn878096.aspx)|
| KeyVerify | [Überprüfen mit einem Schlüssel](https://msdn.microsoft.com/de-DE/library/azure/dn878082.aspx)|
| KeyWrap | [Umschließen eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn878066.aspx)|
| KeyUnwrap | [Aufheben der Umschließung eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn878079.aspx)|
| KeyEncrypt | [Verschlüsseln mit einem Benutzerschlüssel](https://msdn.microsoft.com/de-DE/library/azure/dn878060.aspx)|
| KeyDecrypt | [Mit einem Schlüssel entschlüsseln](https://msdn.microsoft.com/de-DE/library/azure/dn878097.aspx)|
| KeyUpdate | [Aktualisieren eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn903616.aspx)|
| KeyList | [Auflisten der Schlüssel in einem Tresor](https://msdn.microsoft.com/de-DE/library/azure/dn903629.aspx)|
| KeyListVersions | [Auflisten der Versionen eines Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn986822.aspx)|
| SecretSet | [Erstellen eines geheimen Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn903618.aspx)|
| SecretGet | [Abrufen von Informationen über einen geheimen Schlüssel](https://msdn.microsoft.com/de-DE/library/azure/dn903633.aspx)|
| SecretUpdate | [Aktualisieren eines geheimen Schlüssels](https://msdn.microsoft.com/de-DE/library/azure/dn986818.aspx)|
| SecretDelete | [Löschen eines Geheimnisses](https://msdn.microsoft.com/de-DE/library/azure/dn903613.aspx)|
| SecretList | [Auflisten geheimer Schlüssel in einem Tresor](https://msdn.microsoft.com/de-DE/library/azure/dn903614.aspx)|
| SecretListVersions | [Auflisten von Versionen geheimer Schlüssel](https://msdn.microsoft.com/de-DE/library/azure/dn986824.aspx)|




## <a id="next"></a>Nächste Schritte ##

Ein Tutorial zur Verwendung des Azure-Schlüsseltresors in einer Webanwendung finden Sie unter [Verwenden des Azure-Schlüsseltresors aus einer Webanwendung](key-vault-use-from-web-application.md).

Eine Referenz zur Programmierung finden Sie im [Entwicklerhandbuch für den Azure-Schlüsseltresor](key-vault-developers-guide.md).

Eine Liste der Azure PowerShell 1.0-Cmdlets für den Azure-Schlüsseltresor finden Sie unter [Cmdlets für den Azure-Schlüsseltresor](https://msdn.microsoft.com/library/azure/dn868052.aspx).
 

<!---HONumber=AcomDC_0114_2016-->