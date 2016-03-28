<properties 
	pageTitle="Authentifizieren mit Mobile Engagement-REST-APIs"
	description="Beschreibt die Authentifizierung mit Azure Mobile Engagement-REST-APIs" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="03/11/2016"
	ms.author="wesmc"/>

# Authentifizieren mit Mobile Engagement-REST-APIs

## Übersicht

In diesem Dokument wird beschrieben, wie Sie ein gültiges AAD-OAuth-Token zur Authentifizierung mit Mobile Engagement-REST-APIs erhalten.

Es wird vorausgesetzt, dass Sie über ein gültiges Azure-Abonnement verfügen und mithilfe eines unserer [Entwickler-Tutorials](mobile-engagement-windows-store-dotnet-get-started.md) eine Mobile Engagement-App erstellt haben.

## Authentifizierung

Zur Authentifizierung wird ein Microsoft Azure Active Directory-basiertes OAuth-Token verwendet.

Um eine API-Anforderung zu authentifizieren, muss jeder Anforderung ein Autorisierungsheader hinzugefügt werden. Dieser hat die folgende Form:

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Azure Active Directory-Token sind eine Stunde lang gültig.

Es gibt mehrere Möglichkeiten, ein Token zu beziehen. Da die APIs im Allgemeinen über einen Clouddienst aufgerufen werden, sollten Sie einen API-Schlüssel verwenden. In der Azure-Terminologie werden API-Schlüssel als Dienstprinzipalkennwort bezeichnet. Folgendes Verfahren beschreibt eine Möglichkeit, das Kennwort manuell einzurichten.

### Einmalige Einrichtung (per Skript)

Sie sollten die Anweisungen unten befolgen, um die Einrichtung mithilfe eines PowerShell-Skripts durchzuführen. Damit lässt sich die Einrichtung in der kürzestmöglichen Zeit durchführen. Dabei werden jedoch die lockersten Standards verwendet. Alternativ können Sie auch die Anweisungen unter [manuelle Einrichtung](mobile-engagement-api-authentication-manual.md) direkt im Azure-Portal befolgen, und eine detailliertere Konfiguration vornehmen.

1. Rufen Sie [hier](http://aka.ms/webpi-azps) die neueste Version von Azure PowerShell ab. Weitere Informationen zu den Anweisungen zum Herunterladen, finden Sie unter diesem [Link](../powershell-install-configure.md).  

2. Sobald Azure PowerShell installiert ist, verwenden Sie die folgenden Befehle, um sicherzustellen, dass das **Azure-Modul** installiert ist:

    a. Stellen Sie sicher, dass das Azure PowerShell-Modul in der Liste der verfügbaren Module enthalten ist.
    
		Get-Module –ListAvailable 

	![Verfügbare Azure-Module][1]
    	
    b. Falls Sie das Azure PowerShell-Modul nicht in der obigen Liste finden, müssen Sie wie folgt vorgehen:
    	
		Import-Module Azure 
    	
3. Melden Sie sich von PowerShell aus im Azure Resource Manager an, indem Sie den folgenden Befehl ausführen, und Ihren Benutzernamen und das Kennwort für Ihr Azure-Konto bereitstellen:
    	
		Login-AzureRmAccount

4. Falls Sie mehrere Abonnements haben, sollten Sie wie folgt vorgehen:

	a. Rufen Sie eine Liste all Ihrer Abonnements ab, und kopieren Sie die SubscriptionIds des Abonnements, das Sie verwenden möchten. Stellen Sie sicher, dass es sich um das gleiche Abonnement handelt, das die Mobile Engagement-App hat, mit der Sie unter Verwendung der APIs interagieren werden.

		Get-AzureRmSubscription

	b. Führen Sie den folgenden Befehl mit der SubscriptionId aus, um das zu verwendende Abonnement zu konfigurieren.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

5. Kopieren Sie den Text des Skripts [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) auf Ihren lokalen Computer, speichern Sie ihn als ein PowerShell-Cmdlet (z. B. `APIAuth.ps1`), und führen Sie dieses aus `.\APIAuth.ps1`.
	
6. Das Skript fordert Sie auf, eine Eingabe für **principalName** vorzunehmen. Geben Sie hier einen geeigneten Namen ein, den Sie für die Erstellung Ihrer Active Directory-Anwendung verwenden wollen (z. B. APIAuth).

7. Nach dem Ausführen zeigt das Skript die folgenden vier Werte an, die Sie für die programmgesteuerte Authentifizierung mit AD benötigen:
		
	**TenantId**, **SubscriptionId**, **ApplicationId**, und **Secret**.

	Sie werden TenantId als `{TENANT_ID}`, ApplicationId als `{CLIENT_ID}` und Secret als `{CLIENT_SECRET}` verwenden.

	> [AZURE.NOTE] Ihre Standardsicherheitsrichtlinie verhindert möglicherweise das Ausführen von PowerShell-Skripts. Konfigurieren Sie in diesem Fall Ihre Ausführungsrichtlinie temporär so, dass die Skriptausführung zugelassen wird. Verwenden Sie dafür folgenden Befehl:

    	> Set-ExecutionPolicy RemoteSigned

8. Hier sehen Sie, wie der Satz PS-Cmdlets aussehen würde.

	![][3]

9. Überprüfen Sie im Azure-Verwaltungsportal, ob eine neue AD-Anwendung mit dem Namen, den Sie zum Skript **principalName** bereitgestellt haben, erstellt wurde. Sie sehen dies unter **Anwendungen im Besitz meines Unternehmens anzeigen**.

	![][4]

#### Schritte zum Abrufen eines gültigen Tokens

1. Rufen Sie die API mit den folgenden Parametern auf, und stellen Sie sicher, dass Sie TENANT\_ID, CLIENT\_ID und CLIENT\_SECRET ersetzen:

	- **Abfrage-URL** als **https://login.microsoftonline.com/{TENANT\_ID}/oauth2/token*
- **HTTP Content-Type-Header** als *application/x-www-form-urlencoded*
	- **HTTP Request Body** als *grant\_type=client\_credentials&client\_id={CLIENT\_ID}&client\_secret={CLIENT\_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F*

	Im Folgenden finden Sie eine Beispielanforderung:

		POST /{TENANT_ID}/oauth2/token HTTP/1.1
		Host: login.microsoftonline.com
		Content-Type: application/x-www-form-urlencoded
		grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
		urce=https%3A%2F%2Fmanagement.core.windows.net%2F

	Hier eine Beispielantwort:

		HTTP/1.1 200 OK
		Content-Type: application/json; charset=utf-8
		Content-Length: 1234
	
		{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
		5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}

	Dieses Beispiel umfasst die URL-Codierung der POST-Parameter; Der Wert `resource` lautet eigentlich `https://management.core.windows.net/`. Fügen Sie auch für `{CLIENT_SECRET}` eine URL-Codierung hinzu, da der Wert Sonderzeichen enthalten kann.

	> [AZURE.NOTE] Zu Testzwecken können Sie ein HTTP-Client-Tool wie [Fiddler](http://www.telerik.com/fiddler) oder [die Postman-Erweiterung für Chrome](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) verwenden.

2. Schließen Sie in jeden API-Aufruf den Autorisierungsanforderungsheader ein:

		Authorization: Bearer {ACCESS_TOKEN}

	Wenn ein 401-Statuscode zurückgegeben wird, überprüfen Sie den Antworttext, dieser könnte darauf hinweisen, dass das Token abgelaufen ist. In diesem Fall rufen Sie ein neues Token ab.

##Verwenden der APIs

Da Sie jetzt über ein gültiges Token verfügen, sind Sie bereit für die API-Aufrufe.

1. Sie müssen in jeder API-Anforderung ein gültiges, nicht abgelaufenes Token übergeben, das Sie im vorigen Abschnitt abgerufen haben.

2. Sie müssen einige Parameter in den Anforderungs-URI einfügen, der Ihre Anwendung identifiziert. Der Anwendungs-URI sieht folgendermaßen aus:

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Um die Parameter abzurufen, klicken Sie auf den Namen Ihrer Anwendung, und klicken Sie auf das Dashboard, um eine Seite wie die folgende mit allen 3 Parametern anzuzeigen.

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`
	- **4** Ihr Ressourcengruppenname ist **MobileEngagement**, sofern Sie keinen neuen erstellt haben. 

	![Mobile Engagement-API-URI-Parameter][2]

>[AZURE.NOTE] <br/>
>1. Ignorieren Sie die API-Stammadresse, da diese für die vorherigen APIs galt.<br/> 2. Sie müssen den Namen der Anwendungsressource verwenden, der sich vom Namen der Anwendung selbst unterscheidet. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png

<!---HONumber=AcomDC_0316_2016-->