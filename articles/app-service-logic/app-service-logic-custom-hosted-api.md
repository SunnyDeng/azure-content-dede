<properties 
	pageTitle="Aufrufen einer benutzerdefinierten API in Logik-Apps" 
	description="Verwenden der in App Service gehosteten benutzerdefinierten API mit Logik-Apps" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="stepsic"/>
	
# Verwenden der in App Service gehosteten benutzerdefinierten API mit Logik-Apps

Zwar weisen Logik-Apps einen umfangreichen Satz von mehr als 40 Connectors für eine Vielzahl von Diensten auf. Trotzdem möchten Sie unter Umständen Ihre eigene benutzerdefinierte API aufrufen, die Ihren eigenen Code ausführen kann. Die einfachste und am besten skalierbare Methode zum Hosten Ihrer eigenen benutzerdefinierten Web-APIs ist die Verwendung von App Service. In diesem Artikel wird beschrieben, wie Sie eine Web-API aufrufen, die in einer App Service-API-App, einer Web-App oder einer mobilen App gehostet wird.

## Bereitstellen der Web-App

Zunächst müssen Sie die API als Web-App in App Service bereitstellen. Die hier bereitgestellten Anweisungen decken eine einfache Bereitstellung ab: [Erstellen einer ASP.NET-Web-App](../app-service-web/web-sites-dotnet-get-started.md). Obwohl Sie jede API aus einer Logik-App aufrufen können, sollten Sie am besten Swagger-Metadaten hinzufügen, um die Integration in Logik-Apps-Aktionen zu vereinfachen. Details finden Sie unter [Hinzufügen von Swagger](../app-service-api/app-service-api-dotnet-get-started.md/#use-swagger-metadata-and-ui).

### API-Einstellungen

Damit der Logik-Apps-Designer Ihren Swagger-Code analysieren kann, müssen Sie CORS aktivieren und die APIDefinition-Eigenschaften der Web-App festlegen. Das Festlegen ist im Azure-Portal sehr einfach. Öffnen Sie einfach das Blatt „Einstellungen“ der Web-App, und legen Sie im API-Abschnitt die „API-Definition“ auf die URL der Datei „swagger.JSON“ fest (dies ist in der Regel https://{name}.azurewebsites.net/swagger/docs/v1). Fügen Sie anschließend noch eine CORS-Richtlinie für „*“ hinzu, um Anfragen vom Logik-Apps-Designer zu ermöglichen.

## Aufrufen der API

Wenn Sie im Logik-Apps-Portal CORS und die API-Definitionseigenschaften festgelegt haben, sollten Sie auf einfache Weise Aktionen benutzerdefinierter APIs im Datenfluss hinzufügen können. Sie können im Designer Ihre Abonnementwebsites durchsuchen, um die Websites aufzulisten, für die eine Swagger-URL definiert wurde. Sie können auch die Aktion „HTTP + Swagger“ verwenden, um auf Swagger zu verweisen und die verfügbaren Aktionen und Eingaben aufzulisten. Und schließlich können Sie jederzeit eine Anforderung mit der HTTP-Aktion erstellen, um beliebige APIs aufzurufen. Dazu gehören auch solche, die nicht über ein Swagger-Dokument verfügbar oder ein solches verfügbar machen.

Wenn Sie Ihre API schützen möchten, gibt es verschiedene Möglichkeiten:

1. Keine Codeänderung erforderlich: Azure Active Directory kann verwendet werden, um ohne Änderung des Codes oder ohne eine erneute Bereitstellung Ihre API zu schützen.
1. Erzwingen Sie die Standardauthentifizierung, die AAD-Authentifizierung oder die Zertifikatauthentifizierung im Code der API.

## Sichern der Aufrufe Ihrer API ohne eine Änderung des Codes 

In diesem Abschnitt erstellen Sie zwei Azure Active Directory-Anwendungen – eine für Ihre Logik-App und eine für Ihre Web-App. Sie authentifizieren Aufrufe Ihrer Web-App mit dem Dienstprinzipal (Client-ID und geheimer Schlüssel), der der AAD-Anwendung für die Logik-App zugeordnet ist. Schließlich nehmen Sie die Anwendungs-IDs in die Definition der Logik-App auf.

### Teil 1: Einrichten einer Anwendungsidentität für Ihre Logik-App

Auf diese Weise wird die Logik-App bei Active Directory authentifiziert. Sie *müssen* dies nur einmal für Ihr Verzeichnis ausführen. Sie können z. B. die gleiche Identität für alle Logik-Apps verwenden, Sie könnten bei Bedarf aber auch pro Logik-App eindeutige Identitäten erstellen. Sie können dies in der Benutzeroberfläche oder mithilfe von PowerShell durchführen.

#### Erstellen der Anwendungsidentität mithilfe des klassischen Azure-Portals

1. Navigieren Sie [im klassischen Azure-Portal zu Active Directory](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), und wählen Sie das Verzeichnis aus, das Sie für Ihre Web-App verwenden.
2. Klicken Sie auf die Registerkarte **Anwendungen**.
3. Klicken Sie in der Befehlsleiste am unteren Rand der Seite auf **Hinzufügen**.
4. Geben Sie Ihrer Identität einen Namen, klicken Sie dann auf den Pfeil "Weiter".
5. Geben Sie eine eindeutige Zeichenfolge, die als Domäne formatiert ist, in die beiden Textfelder ein, und klicken Sie auf das Häkchen.
6. Klicken Sie auf die Registerkarte **Konfigurieren** für die Anwendung.
7. Kopieren Sie die **Client-ID**, sie wird in Ihrer Logik-App verwendet.
8. Klicken Sie im Abschnitt **Schlüssel** auf **Dauer auswählen**, und wählen Sie entweder 1 oder 2 Jahre aus.
9. Klicken Sie auf die Schaltfläche **Speichern** unten auf dem Bildschirm (möglicherweise müssen Sie einige Sekunden warten).
10. Jetzt müssen Sie den Schlüssel in das Feld kopieren. Er wird auch in Ihrer Logik-App verwendet.

#### Erstellen der Anwendungsidentität mithilfe von PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Kopieren Sie die **Mandanten-ID**, die **Anwendungs-ID** und das Kennwort, das Sie verwendet haben.

### Teil 2: Schützen der Web-App mit einer AAD-App-Identität

Wenn Ihre Web-App bereits bereitgestellt wurde, können Sie sie einfach im Portal aktivieren. Andernfalls können Sie den Abschnitt für die Autorisierung in der Bereitstellung des Azure-Ressourcen-Managers verwenden.

#### Aktivieren der Autorisierung im Azure-Portal

1. Navigieren Sie zur Web-App, und klicken Sie in der Befehlsleiste auf **Einstellungen**.
2. Klicken Sie auf **Autorisierung/Authentifizierung**. 
3. **Aktivieren** Sie sie.

An diesem Punkt wird eine Anwendung automatisch für Sie erstellt. Sie benötigen die Client-ID dieser Anwendung für Teil 3, daher müssen Sie folgende Schritte ausführen:

1. Wechseln Sie zu [Active Directory im klassischen Azure-Portal](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), und wählen Sie Ihr Verzeichnis aus. 
2. Suchen Sie mit dem Suchfeld nach der App.
3. Klicken Sie in der Liste darauf.
4. Klicken Sie auf die Registerkarte **Konfigurieren**.
5. Daraufhin sollte die **Client-ID** angezeigt werden.

#### Bereitstellen Ihrer Web-App mit einer ARM-Vorlage

Zunächst müssen Sie eine Anwendung für Ihre Web-App erstellen. Dies sollte eine andere Anwendung sein als die, die für Ihre Logik-App verwendet wird. Folgen Sie den Schritten in Teil 1 oben, verwenden Sie aber nun für **HomePage** und **IdentifierUris** den tatsächlichen Wert für https://**URL** der Web-App.

>[AZURE.NOTE]Wenn Sie die Anwendung für Ihre Web-App erstellen, müssen Sie das [klassische Azure-Portal](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) verwenden, da mit dem PowerShell-Cmdlet die erforderlichen Berechtigungen zum Anmelden von Benutzern bei einer Website nicht eingerichtet werden.

Sobald Sie über die Client-ID und die Mandanten-ID verfügen, nehmen Sie Folgendes als untergeordnete Ressource der Web-App in die Bereitstellungsvorlage auf:

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

Um eine Bereitstellung automatisch auszuführen, mit der gleichzeitig eine leere Web-App und eine Logik-App bereitgestellt werden, die AAD verwenden, klicken Sie auf die folgende Schaltfläche: [![Bereitstellen in Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Die vollständige Vorlage finden Sie unter [Logik-App ruft eine benutzerdefinierte API auf, die in App Service gehostet und von AAD geschützt wird](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### Teil 3: Ausfüllen des Abschnitts für die Autorisierung in der Logik-App

Der Abschnitt für die **Autorisierung** der **HTTP**-Aktion: `{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Element | Beschreibung |
|---------|-------------|
| type | Die Art der Authentifizierung. Für die ActiveDirectoryOAuth-Authentifizierung lautet der Wert "ActiveDirectoryOAuth". |
| tenant | Die Mandanten-ID zum Identifizieren des AD-Mandanten. |
| audience | Erforderlich. Die Ressource, mit der Sie eine Verbindung herstellen. |
| clientID | Die Client-ID für die Azure AD-Anwendung. |
| secret | Erforderlich. Der geheime Schlüssel des Clients, der das Token anfordert. | 

In der obigen Vorlage ist dies bereits eingerichtet, aber wenn Sie die Logik-App direkt erstellen, müssen Sie den gesamten Abschnitt für die Autorisierung aufnehmen.

## Sichern Ihrer API im Code

### Zertifikatauthentifizierung

Clientzertifikate können zum Überprüfen der eingehenden Anforderungen bei der Web-App verwendet werden. Informationen zum Einrichten des Codes finden Sie unter [Konfigurieren der gegenseitigen TLS-Authentifizierung für eine Web-App](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

Im Abschnitt für die *Autorisierung* sollten Sie Folgendes angeben: `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Element | Beschreibung |
|---------|-------------|
| type | Erforderlich. Die Art der Authentifizierung. Für SSL-Clientzertifikate muss der Wert auf "ClientCertificate" festgelegt werden. |
| pfx | Erforderlich. Base64-codierte Inhalte der PFX-Datei. |
| password | Erforderlich. Kennwort für den Zugriff auf die PFX-Datei. |

### Standardauthentifizierung

Mit der Standardauthentifizierung (z. B. Benutzername und Kennwort) können Sie die eingehenden Anforderungen überprüfen. Die Standardauthentifizierung ist ein gängiges Muster, und Sie können sie in jeder beliebigen Sprache verwenden, in der Sie Ihre App erstellen.

Im Abschnitt für die *Autorisierung* sollten Sie Folgendes angeben: `{"type": "basic","username": "test","password": "test"}`.

| Element | Beschreibung |
|---------|-------------|
| type | Erforderlich. Die Art der Authentifizierung. Für die Standardauthentifizierung muss der Wert "basic" lauten. |
| username | Erforderlich. Der zu authentifizierende Benutzername. |
| password | Erforderlich. Das zu authentifizierende Kennwort. |
 
### Verarbeiten der AAD-Authentifizierung im Code

Standardmäßig führt die Azure Active Directory-Authentifizierung, die Sie im Portal aktivieren, keine fein abgestufte Autorisierung durch. Die API wird z. B. nicht auf einen bestimmten Benutzer oder eine App festgelegt, sondern nur auf einen bestimmten Mandanten.

Wenn Sie die API z. B. im Code nur auf die Logik-App einschränken möchten, können Sie den Header extrahieren, der das JWT enthält, und überprüfen, wer der Aufrufer ist, und dabei die nicht übereinstimmenden Anforderungen zurückweisen.

Wenn Sie dies darüber hinaus vollständig in Ihrem eigenen Code implementieren möchten, ohne die Portal-Funktion zu nutzen, können Sie den folgenden Artikel lesen: [Verwenden von Active Directory für die Authentifizierung in Azure App Service](../app-service-web/web-sites-authentication-authorization.md).

Sie müssen dennoch die obigen Schritte ausführen, um die Anwendungsidentität für Ihre Logik-App zu erstellen und diese zum Aufrufen der API zu verwenden.

<!---HONumber=AcomDC_0224_2016-->