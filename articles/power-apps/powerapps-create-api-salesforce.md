<properties
	pageTitle="Hinzufügen der Salesforce-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Salesforce-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Erstellen einer neuen Salesforce-API in PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-salesforce.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-salesforce.md)

Fügen Sie die Salesforce-API der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][14]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][15]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Navigieren zu registrierten APIs][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzufügen: ![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs**, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **Salesforce** aus: ![Auswählen der Salesforce-API][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Konfigurieren der Dropbox-API-Einstellungen][7]

9. Geben Sie den *App-Schlüssel* und den *geheimen App-Schlüssel* Ihrer Salesforce-Anwendung ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer Salesforce-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für den Schlüssel und den geheimen Schlüssel.

	> [AZURE.IMPORTANT] Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue Salesforce-API hinzugefügt.


## Optional: Registrieren einer Salesforce-App zur Verwendung mit PowerApps

Wenn Sie über keine vorhandene Salesforce-App mit den Werten für den Schlüssel und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.

1. Öffnen Sie die [Startseite für Salesforce Developers][5], und melden Sie sich mit Ihrem Salesforce-Konto an. 

2. Wählen Sie auf der Startseite Ihr Profil und dann **Setup** aus: ![Salesforce-Startseite][6]

3. Wählen Sie **Create** und dann **Apps** aus. Klicken Sie auf der Seite **Apps** unter **Connected Apps** auf **New**: ![Salesforce – App erstellen][7]

4. Auf der Seite **New Connected App**:

	1. Geben Sie den Wert für **Connected App Name** ein.  
	2. Geben Sie den Wert für **API Name** ein.  
	3. Geben Sie den Wert für **Contact Email** ein.  
	4. Wählen Sie unter _API (Enable OAuth Settings)_ die Option **Enable OAuth Settings** aus, und legen Sie**Callback URL** auf die Umleitungs-URL fest, die Sie erhalten haben, als Sie die neue Salesforce-API im Azure-Portal hinzugefügt haben (in diesem Thema).  

5. Fügen Sie unter _Selected OAuth scopes_ die folgenden Bereiche zu **Selected OAuth Scopes** hinzu:

	- Access and manage your Chatter data (chatter\_api)
	- Access and manage your data (api)
	- Allow access to your unique identifier (openid)
	- Perform requests on your behalf at any time (refresh\_token, offline\_access)

6. Speichern Sie die Änderungen (**Save**): ![Salesforce – neue App][8]

Eine neue Salesforce-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Salesforce-API im Azure-Portal verwenden.

## Informationen zu REST-APIs

[Salesforce-REST-API](../connectors/create-api-salesforce.md) – Referenz.

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die Salesforce-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-salesforce/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-salesforce/add-api.PNG
[3]: ./media/powerapps-create-api-salesforce/select-salesforce-api.PNG
[4]: ./media/powerapps-create-api-salesforce/configure-salesforce-api.PNG
[5]: https://developer.salesforce.com
[6]: ./media/powerapps-create-api-salesforce/salesforce-developer-homepage.PNG
[7]: ./media/powerapps-create-api-salesforce/salesforce-create-app.PNG
[8]: ./media/powerapps-create-api-salesforce/salesforce-new-app.PNG
[14]: ./media/powerapps-create-api-salesforce/browseall.png
[15]: ./media/powerapps-create-api-salesforce/allresources.png

<!---HONumber=AcomDC_0309_2016-->