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
 ms.date="03/09/2016"
 ms.author="krisragh"/>

# Ausgehende Authentifizierung von Scheduler

Scheduler-Aufträge müssen unter Umständen Dienste kontaktieren, die eine Authentifizierung erfordern. Dadurch kann ein aufgerufener Dienst ermitteln, ob dem Scheduler-Auftrag Zugriff auf die Ressourcen gewährt werden soll. Zu diesen Diensten zählen etwa andere Azure-Dienste, Salesforce.com, Facebook und sichere benutzerdefinierten Websites.

## Hinzufügen und Entfernen der Authentifizierung

Ein Scheduler-Auftrag lässt sich ganz einfach mit einer Authentifizierung versehen: Fügen Sie dem Element `request` beim Erstellen oder Aktualisieren eines Auftrags ein untergeordnetes JSON-Element vom Typ `authentication` hinzu. Geheime Informationen, die in einer PUT-, PATCH- oder POST-Anforderung (als Teil des Objekts `authentication`) an den Scheduler-Dienst übergeben werden, werden niemals in Antworten zurückgegeben. In Antworten werden geheime Informationen auf NULL festgelegt, oder sie besitzen unter Umständen ein öffentliches Token, das die authentifizierte Entität darstellt.

Wenn Sie die Authentifizierung entfernen möchten, führen Sie eine explizite PUT- oder PATCH-Anforderung für den Auftrag aus, und legen Sie dabei das Objekt `authentication` auf NULL fest. In der Antwort sind keinerlei Authentifizierungseigenschaften enthalten.

Derzeit werden als Authentifizierungstypen nur das Modell `ClientCertificate` (für die Verwendung von SSL-/TLS-Clientzertifikaten), das Modell `Basic` (für die Standardauthentifizierung) und das Modell `ActiveDirectoryOAuth` (für die Active Directory-OAuth-Authentifizierung) unterstützt.

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

## Anforderungstext für die ActiveDirectoryOAuth-Authentifizierung

Wenn Sie die Authentifizierung mithilfe des Modells `ActiveDirectoryOAuth` hinzufügen, müssen Sie im Anforderungstext zusätzlich folgende Elemente angeben:

|Element |Beschreibung |
|:--|:--|
|_authentication (übergeordnetes Element)_ |Das Authentifizierungsobjekt für die Verwendung der ActiveDirectoryOAuth-Authentifizierung.|
|_type_ |Erforderlich. Die Art der Authentifizierung. Für die ActiveDirectoryOAuth-Authentifizierung muss der Wert `ActiveDirectoryOAuth` lauten.|
|_tenant_ |Erforderlich. Die Mandanten-ID für den Azure AD-Mandanten.|
|_audience_ |Erforderlich. Dieser Wert wird auf https://management.core.windows.net/.| festgelegt.
|_clientId_ |Erforderlich. Geben Sie die Client-ID für die Azure AD-Anwendung an.|
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
|_clientId_ |Die Client-ID für die Azure AD-Anwendung.|

## Siehe auch


 [Was ist Azure Scheduler?](scheduler-intro.md)

 [Konzepte, Terminologie und Entitätshierarchie für Azure Scheduler](scheduler-concepts-terms.md)

 [Erste Schritte mit dem Scheduler im Azure-Portal](scheduler-get-started-portal.md)

 [Pläne und Abrechnung in Azure Scheduler](scheduler-plans-billing.md)

 [Azure Scheduler-REST-API – Referenz](https://msdn.microsoft.com/library/mt629143)

 [Azure Scheduler – PowerShell-Cmdlets-Referenz](scheduler-powershell-reference.md)

 [Hochverfügbarkeit und Zuverlässigkeit von Azure Scheduler](scheduler-high-availability-reliability.md)

 [Einschränkungen, Standardwerte und Fehlercodes für Azure Scheduler](scheduler-limits-defaults-errors.md)

<!---HONumber=AcomDC_0323_2016-->