<properties 
	pageTitle="Erste Schritte mit den Authentifizierungs-APIs von Azure Mobile Engagement"
	description="Dieser Migrationsleitfaden unterstützt Kunden, die zuvor die Authentifizierung mit Capptain-APIs verwendet haben und jetzt die Authentifizierung über die neuen Azure Mobile Engagement-APIs verwenden müssen." 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="wesmc7777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="02/29/2016"
	ms.author="wesmc"/>

# Azure Mobile Engagement – Verwenden der APIs zur Authentifizierung

## Übersicht

In diesem Dokument wird ausführlich erläutert, wie der Authentifizierungsmechanismus für die neuen APIs eingerichtet wird.

Es wird vorausgesetzt, dass Sie über ein gültiges Azure-Abonnement verfügen und mithilfe eines unserer [Entwickler-Tutorials](mobile-engagement-windows-store-dotnet-get-started.md) eine Mobile Engagement-App erstellt haben.

## Authentifizierung

Zur Authentifizierung muss ein Microsoft Azure Active Directory-basiertes OAuth-Token verwendet werden.

Um eine API-Anforderung zu authentifizieren, muss jeder Anforderung ein Authentifizierungsheader hinzugefügt werden. Folgendes Format ist erforderlich:

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Azure Active Directory-Token sind eine Stunde lang gültig.

Es gibt mehrere Möglichkeiten, ein Token zu beziehen. Da die APIs im Allgemeinen über einen Clouddienst aufgerufen werden, sollten Sie einen API-Schlüssel verwenden. In der Azure-Terminologie werden API-Schlüssel als Dienstprinzipalkennwort bezeichnet. Folgendes Verfahren beschreibt eine Möglichkeit, das Kennwort manuell einzurichten.

> [AZURE.WARNING] Der in Azure Active Directory angezeigte Schlüssel ist NICHT der im Portal angezeigte Mobile Engagement-API-Schlüssel. Das Konzept des Mobile Engagement-API-Schlüssels ist veraltet und wurde durch die in diesem Dokument beschriebene AAD-Authentifizierung ersetzt.

#### Einmalige Einrichtung (manuell)

Beachten Sie bitte folgende Informationen, die Sie später benötigen werden:
	
1. Erstellen Sie mithilfe [dieser Anleitung](../resource-group-create-service-principal-portal.md) eine Azure Active Directory-Anwendung.

	- Auf den von Ihnen gewählten Anwendungsnamen wird in diesem Dokument durch `{AD_APP_NAME}` verwiesen.
	- Auf die im Konfigurationsmenü angezeigte Client-ID wird in diesem Dokument durch `{CLIENT_ID}` verwiesen.
	- Auf den nach dem Speichern nur einmal angezeigten Schlüssel wird in diesem Dokument durch `{CLIENT_SECRET}` verwiesen.
	- Klicken Sie in der unteren Leiste auf **ENDPUNKTE ANZEIGEN**, und kopieren Sie die **OAUTH 2.0-TOKENENDPUNKT-URL**, auf die in diesem Dokument durch `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token` verwiesen wird. <br/>                                    
2. Weisen Sie über die [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) dem Dienstprinzipal eine Rolle wie z. B. „Leser“ oder „Besitzer“ zu.

	Wenn Sie unter Windows arbeiten, schließen Sie `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin` in die Umgebungsvariable `PATH` ein, um die Azure-Befehle verwenden zu können.

	Führen Sie folgende Befehle aus, um Ihr Konto mithilfe der Azure-Befehlszeilenschnittstelle einzurichten:

		azure config mode arm 
		azure login

	Verwenden Sie dann diesen Befehl, um die Objekt-ID Ihrer Anwendung zu suchen.

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	Beachten Sie die `Object Id` in der Ausgabe.

	Weisen Sie dann mithilfe dieses Befehls dem Dienstprinzipal die Rolle `Owner` zu:
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### Einmalige Einrichtung (per Skript)

Dies ist eine alternative Möglichkeit, die oben beschriebenen Schritte mithilfe eines PowerShell-Skripts auszuführen.

1. Rufen Sie die neueste Version von Azure PowerShell ab. Downloadanweisungen finden Sie unter diesem [Link](../powershell-install-configure.md). 

2. Öffnen Sie Windows PowerShell im Administratormodus, und stellen Sie sicher, dass die [Azure Resource Manager-Cmdlets](https://msdn.microsoft.com/library/mt125356.aspx) installiert sind.

		Install-Module AzureRM
		Install-AzureRM

3. Führen Sie den folgenden Befehl aus:

		Import-Module AzureRM.profile

4. Führen Sie den folgenden Befehl aus, um ein Anmeldedialogfeld zu starten. Nach dem Anmelden zeigt der Befehl das „aktive“ Abonnement an, also dasjenige, auf das sich alle von Ihnen ausgeführten Befehle auswirken.

		Add-AzureRmAccount

5. Führen Sie den folgenden Befehl aus, um all Ihre Abonnements aufzuführen. Kopieren Sie die GUID des Abonnements, das Sie verwenden möchten.

		Get-AzureRmSubscription

6. Führen Sie den folgenden Befehl mit der Abonnement-GUID aus, um das zu verwendende Abonnement zu konfigurieren. Dies ist besonders nützlich, wenn Sie über mehrere Abonnements verfügen.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. Kopieren Sie den Text für das Skript [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) auf Ihren lokalen Computer, und führen Sie es aus.

	>[Azure.Note] Ihre Standardsicherheitsrichtlinie verhindert möglicherweise das Ausführen von PowerShell-Skripts. Konfigurieren Sie in diesem Fall Ihre Ausführungsrichtlinie temporär so, dass die Skriptausführung zugelassen wird. Verwenden Sie dafür folgenden Befehl:

	>	Set-ExecutionPolicy RemoteSigned

	Das Skript fordert Sie auf, dem Dienstprinzipal einen „Namen“ zuzuweisen. Sie können hier jeden beliebigen Namen eingeben.

	Nach dem Ausführen zeigt das Skript vier Werte an, die Sie für die programmgesteuerte Authentifizierung mit AD benötigen: **TenantId**, **SubscriptionId**, **ApplicationId** und **Secret**.

	Kopieren Sie diese Werte zur Referenz. Um jetzt ein Zugriffstoken abzurufen, verwenden Sie „TenantId“ als `{TENANT_ID}`, „ApplicationId“ als `{CLIENT_ID}` und „Secret“ als `{CLIENT_SECRET}`.

8. Überprüfen Sie im Azure-Verwaltungsportal, ob unter **Anwendungen im Besitz meines Unternehmens anzeigen** eine neue AD-Anwendung angezeigt wird.

#### Schritte zum Abrufen eines gültigen Tokens

Um ein neues Token abzurufen, rufen Sie diese API auf:

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

Im Folgenden finden Sie eine Beispielanforderung:

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

Hier eine Beispielantwort:

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

Dieses Beispiel umfasst die URL-Codierung der POST-Parameter, der Wert `resource` lautet eigentlich `https://management.core.windows.net/`. Fügen Sie auch für `{CLIENT_SECRET}` eine URL-Codierung hinzu, da der Wert Sonderzeichen enthalten kann.

Schließen Sie in jeden API-Aufruf den Autorisierungsanforderungsheader ein:

	Authorization: Bearer {ACCESS_TOKEN}

Wenn ein 401-Statuscode zurückgegeben wird, überprüfen Sie den Antworttext, dieser könnte darauf hinweisen, dass das Token abgelaufen ist. In diesem Fall rufen Sie ein neues Token ab.

##Verwenden der APIs

Da Sie jetzt über ein gültiges Token verfügen, sind Sie bereit für die API-Aufrufe.

1. Sie müssen in jeder API-Anforderung ein gültiges, nicht abgelaufenes Token übergeben, das Sie im vorigen Abschnitt abgerufen haben.

2. Sie müssen einige Parameter in den Anforderungs-URI einfügen, der Ihre Anwendung identifiziert. Der Anwendungs-URI sieht folgendermaßen aus:

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Um die Parameter abzurufen, klicken Sie auf den Namen Ihrer Anwendung, und klicken Sie auf das Dashboard, um eine Seite wie die folgende mit allen 3 Parametern anzuzeigen.

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/>
>1. Ignorieren Sie die API-Stammadresse, da diese für die vorherigen APIs galt.<br/> 2. Sie müssen den Namen der Anwendungsressource verwenden, der sich vom Namen der Anwendung selbst unterscheidet. 

<!---HONumber=AcomDC_0302_2016-->