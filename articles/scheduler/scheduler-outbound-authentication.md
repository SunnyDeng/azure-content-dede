<properties 
 pageTitle="Ausgehende Authentifizierung von Scheduler" 
 description="" 
 services="scheduler" 
 documentationCenter=".NET" 
 authors="krisragh" 
 manager="dwrede" 
 editor=""/>
<tags 
 ms.service="scheduler" 
 ms.workload="infrastructure-services" 
 ms.tgt_pltfrm="na" 
 ms.devlang="dotnet" 
 ms.topic="article" 
 ms.date="12/04/2015" 
 ms.author="krisragh"/>
 
# Ausgehende Authentifizierung von Scheduler

Scheduler-Aufträge müssen unter Umständen Dienste kontaktieren, die eine Authentifizierung erfordern. Dadurch kann ein aufgerufener Dienst ermitteln, ob dem Scheduler-Auftrag Zugriff auf die Ressourcen gewährt werden soll. Zu diesen Diensten zählen etwa andere Azure-Dienste, Salesforce.com, Facebook und sichere benutzerdefinierten Websites.

## Hinzufügen und Entfernen der Authentifizierung

Ein Scheduler-Auftrag lässt sich ganz einfach mit einer Authentifizierung versehen: Fügen Sie dem Element `request` beim Erstellen oder Aktualisieren eines Auftrags ein untergeordnetes JSON-Element vom Typ `authentication` hinzu. Geheime Informationen, die in einer PUT-, PATCH- oder POST-Anforderung (als Teil des Objekts `authentication`) an den Scheduler-Dienst übergeben werden, werden niemals in Antworten zurückgegeben. In Antworten werden geheime Informationen auf NULL festgelegt, oder sie besitzen unter Umständen ein öffentliches Token, das die authentifizierte Entität darstellt.

Wenn Sie die Authentifizierung entfernen möchten, führen Sie eine explizite PUT- oder PATCH-Anforderung für den Auftrag aus, und legen Sie dabei das Objekt `authentication` auf NULL fest. In der Antwort sind keinerlei Authentifizierungseigenschaften enthalten.

Derzeit werden als Authentifizierungstypen nur das Modell `ClientCertificate` (für die Verwendung von SSL-/TLS-Clientzertifikaten), das Modell `Basic` (für die Standardauthentifizierung) und das Modell `ActiveDirectoryOAuth` (für die Active Directory-OAuth-Authentifizierung) unterstützt.

## Anforderungstext für die ClientCertificate-Authentifizierung

Wenn Sie die Authentifizierung mithilfe des Modells `ClientCertificate` hinzufügen, müssen Sie im Anforderungstext zusätzlich folgende Elemente angeben:

|Element|Beschreibung|
|:---|:---|
|_authentication (übergeordnetes Element)_|Das Authentifizierungsobjekt für die Verwendung eines SSL-Clientzertifikats.|
|_type_|Erforderlich. Die Art der Authentifizierung. Für SSL-Clientzertifikate muss der Wert auf `ClientCertificate` festgelegt werden.|
|_pfx_|Erforderlich. Base64-codierte Inhalte der PFX-Datei.|
|_password_|Erforderlich. Kennwort für den Zugriff auf die PFX-Datei.|


## Antworttext für die ClientCertificate-Authentifizierung

Wenn eine Anforderung mit Authentifizierungsinformationen gesendet wird, enthält die Antwort die folgenden authentifizierungsbezogenen Elemente:

|Element |Beschreibung |
|:--|:--|
|_authentication (übergeordnetes Element)_ |Das Authentifizierungsobjekt für die Verwendung eines SSL-Clientzertifikats.|
|_type_ |Die Art der Authentifizierung. Für SSL-Clientzertifikate lautet der Wert `ClientCertificate`.|
|_certificateThumbprint_ |Der Fingerabdruck des Zertifikats.|
|_certificateSubjectName_ |Der Distinguished Name des Antragstellers für das Zertifikat.|
|_certificateExpiration_ |Das Ablaufdatum des Zertifikats.|

## Beispielanforderung und -antwort für die ClientCertificate-Authentifizierung

Das folgende Beispiel führt eine PUT-Anforderung aus, die die Authentifizierung vom Typ `ClientCertificate` integriert. Die Anforderung sieht wie folgt aus:


	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Content-Length: 4013
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "clientcertificate",
			"password": "test",
			"pfx": "long-pfx-key”
		  }
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Die Antwort auf diese Anforderung lautet wie folgt:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET
	 

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication": {
			"type": "ClientCertificate",
			"certificateThumbprint": "C1645E2AF6317D9FCF9C78FE23F9DE0DAFAD2AB5",
			"certificateExpiration": "2021-01-01T08:00:00Z",
			"certificateSubjectName": "CN=Scheduler Management"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}
## Anforderungstext für die Standardauthentifizierung

Wenn Sie die Authentifizierung mithilfe des Modells `Basic` hinzufügen, müssen Sie im Anforderungstext zusätzlich folgende Elemente angeben:

|Element|Beschreibung|
|:--|:--|
|_authentication (übergeordnetes Element)_ |Das Authentifizierungsobjekt für die Verwendung der Standardauthentifizierung.|
|_type_ |Erforderlich. Die Art der Authentifizierung. Für die Standardauthentifizierung muss der Wert `Basic` lauten.|
|_username_ |Erforderlich. Der zu authentifizierende Benutzername.|
|_password_ |Erforderlich. Das zu authentifizierende Kennwort.|

## Antworttext für die Standardauthentifizierung

Wenn eine Anforderung mit Authentifizierungsinformationen gesendet wird, enthält die Antwort die folgenden authentifizierungsbezogenen Elemente:

|Element|Beschreibung|
|:--|:--|
|_authentication (übergeordnetes Element)_ |Das Authentifizierungsobjekt für die Verwendung der Standardauthentifizierung.|
|_type_ |Die Art der Authentifizierung. Für die Standardauthentifizierung lautet der Wert `Basic`.|
|_username_ |Der authentifizierte Benutzername.|

## Beispielanforderung und -antwort für die Standardauthentifizierung

Das folgende Beispiel führt eine PUT-Anforderung aus, die die Authentifizierung vom Typ `Basic` integriert. Die Anforderung sieht wie folgt aus:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		"authentication":{  
		  "username":"user1",
		  "password":"password",
		  "type":"basic"
		  }           
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Die Antwort auf diese Anforderung lautet wie folgt:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET

	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"username":"user1",
			"type":"Basic"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Anforderungstext für die ActiveDirectoryOAuth-Authentifizierung

Wenn Sie die Authentifizierung mithilfe des Modells `ActiveDirectoryOAuth` hinzufügen, müssen Sie im Anforderungstext zusätzlich folgende Elemente angeben:

|Element |Beschreibung |
|:--|:--|
|_authentication (übergeordnetes Element)_ |Das Authentifizierungsobjekt für die Verwendung der ActiveDirectoryOAuth-Authentifizierung.|
|_type_ |Erforderlich. Die Art der Authentifizierung. Für die ActiveDirectoryOAuth-Authentifizierung muss der Wert `ActiveDirectoryOAuth` lauten.|
|_tenant_ |Erforderlich. Die Mandanten-ID für den Azure AD-Mandanten.|
|_audience_ |Erforderlich. Dieser Wert wird auf https://management.core.windows.net/.| festgelegt.
|_clientId_ |Erforderlich. Geben Sie die Client-ID für die Azure AD-Anwendung an.|
|_secret_ |Erforderlich. Der geheime Schlüssel des Clients, der das Token anfordert.|

### Ermitteln der Mandanten-ID

Durch Ausführen von `Get-AzureAccount` in Azure PowerShell können Sie die Mandanten-ID für den Azure AD-Mandanten ermitteln.

## Antworttext für die ActiveDirectoryOAuth-Authentifizierung

Wenn eine Anforderung mit Authentifizierungsinformationen gesendet wird, enthält die Antwort die folgenden authentifizierungsbezogenen Elemente:

|Element |Beschreibung |
|:--|:--|
|_authentication (übergeordnetes Element)_ |Das Authentifizierungsobjekt für die Verwendung der ActiveDirectoryOAuth-Authentifizierung.|
|_type_ |Die Art der Authentifizierung. Für die ActiveDirectoryOAuth-Authentifizierung lautet der Wert `ActiveDirectoryOAuth`.|
|_tenant_ |Die Mandanten-ID für den Azure AD-Mandanten. |
|_audience_ |Dieser Wert wird auf https://management.core.windows.net/.| festgelegt.
|_clientId_ |Die Client-ID für die Azure AD-Anwendung.|

## Beispielanforderung und -antwort für die ActiveDirectoryOAuth-Authentifizierung

Das folgende Beispiel führt eine PUT-Anforderung aus, die die Authentifizierung vom Typ `ActiveDirectoryOAuth` integriert. Die Anforderung sieht wie folgt aus:

	PUT https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/cs-brazilsouth-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/testScheduler 
	x-ms-version: 2013-03-01
	User-Agent: Microsoft.WindowsAzure.Scheduler.SchedulerClient/3.0.0.0 AzurePowershell/v0.8.10
	Content-Type: application/json; charset=utf-8
	Host: management.core.windows.net
	Expect: 100-continue

	{
	  "action": {
		"type": "http",
		"request": {
		  "uri": "https://management.core.windows.net/7e2dffb5-45b5-475a-91be-d3d9973c82d5/cloudservices/CS-NorthCentralUS-scheduler/resources/scheduler/~/JobCollections/testScheduler/jobs/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"01234567-89ab-cdef-0123-456789abcdef",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"secret": "&lt;secret-key&gt;",
			"type":"ActiveDirectoryOAuth"
		  }                      
		}
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  }
	}

Die Antwort auf diese Anforderung lautet wie folgt:

	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Pragma: no-cache
	Content-Length: 721
	Content-Type: application/json; charset=utf-8
	Expires: -1
	Server: 1.0.6198.153 (rd_rdfe_stable.141027-2149) Microsoft-HTTPAPI/2.0
	x-ms-servedbyregion: ussouth2
	X-AspNet-Version: 4.0.30319
	X-Powered-By: ASP.NET


	{
	  "id": "testScheduler",
	  "action": {
		"request": {
		  "uri": "https:\/\/management.core.windows.net\/7e2dffb5-45b5-475a-91be-d3d9973c82d5\/cloudservices\/CS-NorthCentralUS-scheduler\/resources\/scheduler\/~\/JobCollections\/testScheduler\/jobs\/test",
		  "method": "GET",
		  "headers": {
			"x-ms-version": "2013-03-01"
		  },
		  "authentication":{  
			"tenant":"01234567-89ab-cdef-0123-456789abcdef",
			"audience":"https://management.core.windows.net/",
			"clientId":"8a14db88-4d1a-46c7-8429-20323727dfab",
			"type":"ActiveDirectoryOAuth"
		  }
		},
		"type": "http"
	  },
	  "recurrence": {
		"frequency": "minute",
		"interval": 1
	  },
	  "state": "enabled",
	  "status": {
		"nextExecutionTime": "2014-10-29T21:52:35.2108904Z",
		"executionCount": 0,
		"failureCount": 0,
		"faultedCount": 0
	  }
	}

## Siehe auch
 

 [Was ist Azure Scheduler?](scheduler-intro.md)
 
 [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)

 [Erste Schritte mit dem Scheduler im Azure-Portal](scheduler-get-started-portal.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Azure Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/dn528946)

 [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Hochverfügbarkeit und Zuverlässigkeit von Azure Scheduler](scheduler-high-availability-reliability.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)


  

 
  

<!---HONumber=AcomDC_1217_2015-->