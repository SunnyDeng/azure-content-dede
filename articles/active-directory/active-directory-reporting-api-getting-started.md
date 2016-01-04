<properties
   pageTitle="Erste Schritte mit der Azure AD Reporting-API"
   description="Vorgehensweise zum Einstieg in die Azure Active Directory Reporting-API"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="12/07/2015"
   ms.author="kenhoff"/>


# Erste Schritte mit der Azure AD Reporting-API

*Diese Dokumentation ist Teil des [Handbuchs für Azure Active Directory Reporting](active-directory-reporting-guide.md).*

Azure Active Directory bietet eine Vielzahl von Aktivitäts-, Sicherheits- und Überwachungsberichten. Diese Daten können über das Azure-Portal genutzt werden, sind aber auch in vielen anderen Anwendungen sehr nützlich, z. B. in SIEM-Systemen, Überwachungs- und Business Intelligence-Tools.

Die Azure AD Reporting-APIs ermöglichen den programmgesteuerten Zugriff auf diese Daten über verschiedene REST-basierte APIs, die in einer Vielzahl von Programmiersprachen und Tools aufgerufen werden können.

Dieser Artikel begleitet Sie durch das Aufrufen der Azure AD Reporting-APIs über PowerShell. Sie können das PowerShell-Beispielskript ändern, um Ihrem Szenario entsprechend auf Daten in einem beliebigen der verfügbaren Berichte im JSON-, XML- oder Textformat zuzugreifen.

Um dieses Beispiel verwenden zu können, benötigen Sie ein [Azure Active Directory](active-directory-whatis.md).

## Erstellen einer Azure AD-Anwendung für den Zugriff auf die API

Die Reporting-API verwendet [OAuth](https://msdn.microsoft.com/library/azure/dn645545.aspx) zum Autorisieren des Zugriffs auf die Web-APIs. Für den Zugriff auf Informationen in Ihrem Verzeichnis müssen Sie eine Anwendung in Ihrem Active Directory erstellen und ihr entsprechende Berechtigungen für den Zugriff auf die AAD-Daten erteilen.


### Erstellen einer Anwendung
- Navigieren Sie zum [Azure-Verwaltungsportal](https://manage.windowsazure.com/).
- Navigieren Sie zu Ihrem Verzeichnis.
- Navigieren Sie zu Anwendungen.
- Klicken Sie auf der unteren Leiste auf "Hinzufügen".
	- Klicken Sie auf "Eine von meinem Unternehmen entwickelte Anwendung hinzufügen".
	- **Name**: Hier haben Sie die freie Wahl Ein Eintrag wie "Reporting-API-Anwendung" wird empfohlen.
	- **Typ**: Wählen Sie "Webanwendung und/oder Web-API".
	- Klicken Sie auf den Pfeil, um zur nächsten Seite zu gelangen.
	- **Anmelde-URL**: ```http://localhost```.
	- **App-ID-URI**: ```http://localhost```.
	- Klicken Sie auf das Häkchen, um das Hinzufügen der Anwendung abzuschließen.

### Gewähren Ihrer Anwendung die Berechtigung zum Verwenden der API
- Navigieren Sie zur Registerkarte "Anwendungen".
- Navigieren Sie zu Ihrer neu erstellten Anwendung.
- Klicken Sie auf die Registerkarte **Konfigurieren**.
- Im Abschnitt "Berechtigungen für andere Anwendungen":
	- Wählen Sie in "Microsoft Azure Active Directory" > "Anwendungsberechtigungen" **Verzeichnisdaten lesen** aus.
- Klicken Sie auf der unteren Leiste auf **Speichern**.


### Abrufen der Verzeichnis-ID, der Client-ID und des geheimen Clientschlüssels

Die folgenden Schritte begleiten Sie durch das Abrufen der Client-ID Ihrer Anwendung und des geheimen Clientschlüssels. Sie müssen auch den Namen Ihres Mandanten kennen. Dieser kann entweder "*.onmicrosoft.com" oder ein benutzerdefinierter Domänenname sein. Kopieren Sie diesen an einen separaten Ort, da Sie ihn benötigen, um das Skript zu ändern.

#### Client-ID der Anwendung
- Navigieren Sie zur Registerkarte "Anwendungen".
- Navigieren Sie zu Ihrer neu erstellten Anwendung.
- Navigieren Sie zur Registerkarte **Konfigurieren**.
- Die Client-ID Ihrer Anwendung ist im Feld **Client-ID** aufgeführt.

#### Geheimer Clientschlüssel der Anwendung
- Navigieren Sie zur Registerkarte "Anwendungen".
- Navigieren Sie zu Ihrer neu erstellten Anwendung.
- Navigieren Sie zur Registerkarte "Konfigurieren".
- Generieren Sie einen neuen geheimen Schlüssel für die Anwendung, indem Sie im Abschnitt "Schlüssel" eine Dauer auswählen.
- Der Schlüssel wird beim Speichern angezeigt. Kopieren Sie ihn, und fügen Sie ihn unbedingt an einem sicheren Ort ein, da es später keine Möglichkeit des Abrufs gibt.


## Ändern des Skripts
Bearbeiten Sie eines des folgenden Skripts, um mit Ihrem Verzeichnis zu arbeiten, indem Sie "$ClientID", "$ClientSecret" und "$tenantdomain" durch die ordnungsgemäßen Werte aus "Delegieren des Zugriffs in Azure AD" ersetzen.

### PowerShell-Skript

    # This script will require the Web Application and permissions setup in Azure Active Directory
    $ClientID	  	= "your-application-client-id-here"				# Should be a ~35 character string insert your info here
    $ClientSecret  	= "your-application-client-secret-here"			# Should be a ~44 character string insert your info here
    $loginURL		= "https://login.windows.net"
    $tenantdomain	= "your-directory-name-here.onmicrosoft.com"			# For example, contoso.onmicrosoft.com

    # Get an Oauth 2 access token based on client id, secret and tenant domain
    $body		= @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth		= Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    $7daysago = "{0:s}" -f (get-date).AddDays(-7) + "Z"
    # or, AddMinutes(-5)

    Write-Output $7daysago

    if ($oauth.access_token -ne $null) {
    	$headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.windows.net/$tenantdomain/reports/auditEvents?api-version=beta&`$filter=eventTime gt $7daysago"

    	$myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)
    	foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
    		Write-Output ($event | ConvertTo-Json)
    	}
        $myReport.Content | Out-File -FilePath auditEvents.json -Force
    } else {
    	Write-Host "ERROR: No Access Token"
    }

### Bash-Skript

    #!/bin/bash

    # Author: Ken Hoff (kenhoff@microsoft.com)
    # Date: 2015.08.20
    # NOTE: This script requires jq (https://stedolan.github.io/jq/)

    CLIENT_ID="your-application-client-id-here"         # Should be a ~35 character string insert your info here
    CLIENT_SECRET="your-application-client-secret-here" # Should be a ~44 character string insert your info here
    LOGIN_URL="https://login.windows.net"
    TENANT_DOMAIN="your-directory-name-here.onmicrosoft.com"    # For example, contoso.onmicrosoft.com

    TOKEN_INFO=$(curl -s --data-urlencode "grant_type=client_credentials" --data-urlencode "client_id=$CLIENT_ID" --data-urlencode "client_secret=$CLIENT_SECRET" "$LOGIN_URL/$TENANT_DOMAIN/oauth2/token?api-version=1.0")

    TOKEN_TYPE=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.token_type')
    ACCESS_TOKEN=$(echo $TOKEN_INFO | ./jq-win64.exe -r '.access_token')

    # get yesterday's date

    YESTERDAY=$(date --date='1 day ago' +'%Y-%m-%d')

    URL="https://graph.windows.net/$TENANT_DOMAIN/reports/auditEvents?api-version=beta&\$filter=eventTime%20gt%20$YESTERDAY"


    REPORT=$(curl -s --header "Authorization: $TOKEN_TYPE $ACCESS_TOKEN" $URL)

    echo $REPORT | ./jq-win64.exe -r '.value' | ./jq-win64.exe -r ".[]"





## Ausführen des Skripts
Wenn Sie die Bearbeitung des Skripts abgeschlossen haben, führen Sie es aus, und prüfen Sie, ob die erwarteten Daten aus dem Bericht "AuditEvents" zurückgegeben werden.

Das Skript gibt Listen aller verfügbaren Berichte und die Ausgabe des Berichts "AccountProvisioningEvents" im PowerShell-Fenster im JSON-Format zurück. Außerdem erstellt es Dateien mit der gleichen Ausgabe im JSON-, Text- und XML-Format. Sie können mit dem Ändern des Skripts experimentieren, um Daten aus anderen Berichten zurückzugeben, und nicht benötigte Ausgabeformate auskommentieren.

## Hinweise

- Es gibt keine Beschränkung für die Anzahl der von der Azure AD Reporting-API (mithilfe von OData-Paginierung) zurückgegebenen Ereignisse.
	- Informationen zu den Beschränkungen für die Aufbewahrung von Berichtsdaten finden Sie unter [Aufbewahrungsrichtlinien für Azure Active Directory-Berichte](active-directory-reporting-retention.md).


## Nächste Schritte
- Möchten Sie wissen, welche Sicherheits-, Überwachungs- und Aktivitätsberichte zur Verfügung stehen? Lesen Sie [Sicherheits-, Überwachungs- und Aktivitätsberichte](active-directory-view-access-usage-reports.md).
- Unter [Azure AD-Überwachungsberichtsereignisse](active-directory-reporting-audit-events.md) finden Sie weitere Informationen zum Überwachungsbericht.
- Unter [Azure AD-Berichte und -Ereignisse (Vorschau)](https://msdn.microsoft.com/library/azure/mt126081.aspx) finden Sie weitere Informationen zum Graph-API-REST-Dienst.

<!---HONumber=AcomDC_1217_2015-->