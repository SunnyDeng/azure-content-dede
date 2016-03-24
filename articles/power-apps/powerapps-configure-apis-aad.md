<properties
	pageTitle="Konfigurieren einer API, um eine Verbindung mit einem Back-End-System in einer Azure Active Directory-Domäne in PowerApps herzustellen | Microsoft Azure"
	description="Konfigurieren einer API, um eine Verbindung mit einem AAD-geschützten Back-End-System in PowerApps herzustellen"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="MandiOhlinger"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/02/2016"
   ms.author="guayan"/>

# Konfigurieren einer API zum Herstellen von Verbindungen mit einer Back-End-Ressource in einer Azure Active Directory-Domäne
Wenn mehr Benutzer Domänen in Azure Active Directory (AAD) erstellen, werden diesen AAD-Domänen auch Back-End-Ressourcen hinzugefügt. Sie können APIs erstellen und konfigurieren, über die Verbindungen mit diesen Back-End-Ressourcen hergestellt werden.

#### Voraussetzungen für den Einstieg

- Anmelden für [PowerApps Enterprise](powerapps-get-started-azure-portal.md)
- Erstellen einer [App Service-Umgebung](powerapps-get-started-azure-portal.md)
- Installieren von [Azure PowerShell][11] 1.0 Preview oder höher
- Registrieren einer in Ihrer [App Service-Umgebung](powerapps-register-api-hosted-in-app-service.md)

## Schritt 1: Erstellen einer Active Directory-Anwendung und Erteilen von Berechtigungen an diese

Um auf das Back-End-System in einer AAD-Domäne zuzugreifen, erstellen Sie eine AAD-Anwendung, und erteilen Sie dieser die richtigen Berechtigungen für Ihr vorhandenes Back-End (das ebenfalls eine AAD-Anwendung ist). Schritte:

1. Navigieren Sie im [klassischen Azure-Portal][13] zu Ihrem Azure Active Directory, öffnen Sie Ihren Mandanten (oder Ihr Verzeichnis), und wählen Sie die Registerkarte **Anwendungen** aus: ![][14].
2. Wählen Sie die unten befindliche Schaltfläche **Hinzufügen** aus. Dann:  

	a) Wählen Sie **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen** aus. b) Geben Sie einen Namen für Ihre Anwendung ein, und wählen Sie **Webanwendung und/oder Web-API** aus. c) Geben Sie in **Anmelde-URL** und **App-ID-URI**, URLs ein, die in Ihrem AAD eindeutig und für Ihr Unternehmen sinnvoll sind. Sie können z. B. http://powerappssignon.contoso.com oder http://powerappsappid.contoso.com eingeben. Sie sollten eine URL in der AAD-Domäne Ihrer Organisation verwenden. Die URLs werden als Bezeichner verwendet, und es ist nicht erforderlich, dass sie vorhanden sind. Niemand wird zu den von Ihnen eingegebenen URLs navigieren. Sie können HTTP oder HTTPS eingeben.

3. Wechseln Sie auf der neu erstellten Seite für die AAD Anwendung zur Registerkarte **Konfigurieren**: ![][15]
4. Wählen Sie im Abschnitt **Schlüssel** über die Dropdownliste eine Dauer aus. Beachten Sie, dass der Schlüssel angezeigt wird, nachdem Sie **Speichern** ausgewählt haben: ![][16]
5. Fügen Sie in **Einmaliges Anmelden** die URL ``https://<your App Service Environment name>.azure-apim.net:456/redirect`` als eine **Antwort-URL** hinzu.
6. In **Berechtigungen für andere Anwendungen**:  

	1. Wählen Sie **Anwendung hinzufügen** aus. Wählen Sie im Popupfenster die AAD-Anwendung aus, die Ihr vorhandenes Back-End schützt: ![][17]  

	2. Verwenden Sie die Dropdownliste, um die Berechtigungen hinzuzufügen: ![][18]

7. Wählen Sie unten **Speichern** aus.
8. Kopieren Sie die **Client-ID** und den **Schlüssel**, und speichern Sie diese. Der Schlüssel wird nicht nochmals angezeigt, nachdem Sie das Azure-Portal geschlossen haben. 

Weitere Informationen zu AAD-Anwendungen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](../active-directory/active-directory-integrating-applications.md).

## Schritt 2: Konfigurieren Ihrer API mit Azure PowerShell

Momentan gibt es keine Unterstützung im Azure-Portal, um die Konfiguration zu initialisieren, die für Ihre API erforderlich ist. Um die API im Azure-Portal zu konfigurieren, verwenden Sie das folgende Azure PowerShell-Skript:

> [AZURE.TIP] Informationen dazu, wie Azure PowerShell installiert, konfiguriert und ausgeführt wird, finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][11]. Das folgende Skript funktioniert mit Azure PowerShell 1.0 Preview oder höher.

```powershell
# get the API resource
$api = Get-AzureRmResource -ResourceType Microsoft.Web/apiManagementAccounts/apis -ResourceName <App Service Environment name>/<API name> -ResourceGroupName <resource group name>

# configure the API resource for AAD authentication
$connectionParameters = @{
    token = @{
        type = "oauthSetting";
        oAuthSettings = @{
            identityProvider = "aad";
            clientId = "<your AAD app client id>";
            clientSecret = "<your AAD app key>";
            customParameters = @{
                TenantId = @{ # this property is optional
                    value = "<your AAD tenant ID>"
                };
                ResourceUri = @{ # this property is required
                    value = "<the app ID URI of the AAD app protecting your backend>"
                }
            }
        }
    }
}
Add-Member -InputObject $api.Properties -MemberType NoteProperty -Name ConnectionParameters -Value $connectionParameters -Force

# update the API resource
New-AzureRmResource -Location $api.Location -ResourceId $api.ResourceId -Properties $api.Properties
```

**Beachten Sie**, dass der Verbindungsparametername **token** wichtig ist. Sie können Ihren eigenen Namen auswählen, sofern er in Camel-Case-Schreibweise vorliegt. Sie verwenden diesen Namen später in Ihrem Back-End-Code oder in Ihrer API-Richtlinie.

Wechseln Sie nun zum [Azure-Portal][19], und navigieren Sie zu den Einstellungen Ihrer API und dann zum Blatt **Allgemein**. Sie sehen die weiteren Konfigurationsoptionen: ![][21]


## Ausprobieren

Öffnen Sie eine App im PowerApps. In **Available connections** wird Ihre neue API aufgelistet. Wenn Sie **Connect** auswählen, zeigt sie ein AAD-Anmeldefenster an. Geben Sie die AAD-Kontodetails Ihrer Organisation ein. Daraufhin wird die Verbindung erstellt.

Ab jetzt empfängt Ihr Back-End, wenn die API zur Laufzeit aus Ihrer APP über diese Verbindung aufgerufen wird, das AAD-Token des Benutzers im HTTP-Header **x-ms-apim-tokens** im folgenden [Base64-Codierung][20]-Format:

```json
{
  "token": {
    "AccessToken": ""
    // ...
  }
}
```

**Beachten Sie**, dass der Eigenschaftsname **token** mit dem Verbindungsparameternamen übereinstimmt, den Sie beim Konfigurieren der Einstellung verwenden.

In Ihrem Back-End-Code kann das AAD-Token dann aus der **AccessToken**-Eigenschaft abgerufen und bei Bedarf verwendet werden. Die App Service-Umgebung aktualisiert das Token automatisch.

## Konfigurieren der API-Richtlinie

Sie können auch die API-Richtlinie verwenden, um das AAD-Token im Standard-HTTP-Header **Authorization** festzulegen. Dies ermöglicht es Ihnen, das AAD-Token, wenn es in Ihrem Back-End-Code benötigt wird, mit einer Standardmethode abzurufen statt einen benutzerdefinierten HTTP-Header auszuwerten und Base64-Decodierung auszuführen. Wenn Sie die API-Richtlinie verwenden möchten, wechseln Sie zum Azure-Portal, navigieren Sie zum **Richtlinie**-Blatt Ihrer API, und legen Sie die folgende Richtlinie fest:

```xml
<policies>
	<inbound>
		<base/>
		<choose>
			<when condition="@(context.Variables.ContainsKey(";tokens";) &amp;&amp; ((JObject)context.Variables[";tokens";])[";token";] != null &amp;&amp; !String.IsNullOrEmpty((string)((JObject)context.Variables[";tokens";])[";token";][";AccessToken";]))">
				<set-header exists-action="override" name="Authorization">
					<value>@("Bearer " + (string)((JObject)context.Variables["tokens"])[";token";]["AccessToken"])</value>
				</set-header>
			</when>
		</choose>
	</inbound>
	<backend>
		<base/>
	</backend>
	<outbound>
		<base/>
	</outbound>
</policies>
```

Wenn Sie sich diese Richtlinie ansehen, ermöglicht Sie Ihnen im Wesentlichen, auf die Werte im **x-ms-apim-tokens**-Header als decodiertes JObject mit einer **tokens**-Variablen zu verweisen. Anschließend können Sie die **set-header**-Richtlinie verwenden, um das tatsächliche AAD-Token abzurufen und es auf den **Authorization**-Header festzulegen. Dies ist die Richtlinie, die auch von [Azure API Management](https://azure.microsoft.com/services/api-management/) verwendet wird. Weitere Informationen hierzu finden Sie unter [Richtlinien in Azure API Management](../api-management/api-management-howto-policies.md).

**Beachten Sie**, dass der Eigenschaftsname **token** mit dem Verbindungsparameternamen übereinstimmt, den Sie beim Konfigurieren der Einstellung verwendet haben.

## Zusammenfassung und nächste Schritte

In diesem Thema wurde erläutert, wie eine API so konfiguriert wird, dass sie eine Verbindung (samt Authentifizierung) mit einer Back-End-Ressource in einer Azure Active Directory-Domäne herstellt. In den folgenden Themen und Ressourcen finden Sie weitere Informationen zu PowerApps.

- [Entwickeln von APIs für PowerApps](powerapps-develop-api.md)


<!--References-->
[11]: ../powershell-install-configure.md
[13]: https://manage.windowsazure.com
[14]: ./media/powerapps-configure-apis-aad/aad-applications-tab.png
[15]: ./media/powerapps-configure-apis-aad/aad-application-configure-tab.png
[16]: ./media/powerapps-configure-apis-aad/aad-application-configure-keys.png
[17]: ./media/powerapps-configure-apis-aad/aad-application-add-other-application.png
[18]: ./media/powerapps-configure-apis-aad/aad-application-add-permissions.png
[19]: https://portal.azure.com
[20]: https://tools.ietf.org/html/rfc4648
[21]: ./media/powerapps-configure-apis-aad/api-settings-aad.png

<!---HONumber=AcomDC_0309_2016-->