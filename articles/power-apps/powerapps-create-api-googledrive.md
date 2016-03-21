<properties
	pageTitle="Hinzufügen der Google Drive-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Google Drive-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Erstellen einer neuen Google Drive-API in PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-googledrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-googledrive.md)

Fügen Sie die Google Drive-API der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][15]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][16]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Navigieren zu registrierten APIs][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzufügen: ![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs** aus, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **Google Drive** aus: ![Auswählen der Google Drive-API][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Konfigurieren der Einstellungen für die Google Drive-API][4]

9. Geben Sie den *App-Schlüssel* und den *geheimen App-Schlüssel* Ihrer Google Drive-Anwendung ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer Google Drive-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für den Schlüssel und den geheimen Schlüssel.

	> [AZURE.IMPORTANT] Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue Google Drive-API hinzugefügt.


## Optional: Registrieren einer Google Drive-App zur Verwendung mit PowerApps

Wenn Sie über keine vorhandene Google Drive-App mit den Werten für den Schlüssel und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.

1. Melden Sie sich an der [Google Developers Console][5] an: ![Google Developers Console][6]

2. Wählen Sie **Create an empty project** aus.

3. Geben Sie einen Namen für Ihre Anwendung ein, akzeptieren Sie die Nutzungsbedingungen, und wählen Sie **Create** aus: ![Erstellen eines neuen Google Drive-Projekts][7]

4. Wählen Sie nach der erfolgreichen Erstellung des neuen Projekts die Option **Use Google APIs** aus: ![Verwenden von Google-APIs][8]

5. Wählen Sie auf der Seite „Overview“ die Option **Drive API** aus: ![Übersicht über Google Drive-API][9]

6. Wählen Sie **Enable API** aus: ![Aktivieren der Google Drive-API][10]

7. Wählen Sie nach dem Aktivieren der Drive API die Registerkarte **Credentials** und dann **OAuth 2.0 Client ID** aus: ![Hinzufügen von Anmeldeinformationen][12]

8. Wählen Sie **Configure consent screen** aus.

9. Geben Sie auf der Registerkarte **OAuth consent screen** unter **Product Name** einen Produktnamen ein, und wählen Sie **Save** aus: ![Konfigurieren des Zustimmungsbildschirms][13]

10. Auf der Seite „Create client ID“:

	1. Wählen Sie unter **Anwendungstyp** die Option **Webanwendung** aus.  
	2.  Geben Sie einen Namen für den Client ein.  
	3. Legen Sie die Umleitungs-URL auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen Google Drive-API im Azure-Portal erhalten haben (in diesem Thema).  
	4. Klicken Sie auf **Erstellen**.  

	![Erstellen der Client-ID][14]

11. Die Client-ID und der geheime Client-Schlüssel der registrierten Anwendung werden angezeigt.

Eine neue Google Drive-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Google Drive-API im Azure-Portal verwenden.

## Informationen zu REST-APIs

[Google Drive-REST-API](../connectors/create-api-googledrive.md) – Referenz

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die Google Drive-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-googledrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-googledrive/add-api.PNG
[3]: ./media/powerapps-create-api-googledrive/select-googledrive-api.PNG
[4]: ./media/powerapps-create-api-googledrive/configure-googledrive-api.PNG
[5]: https://console.developers.google.com/
[6]: ./media/powerapps-create-api-googledrive/google-developers-console.PNG
[7]: ./media/powerapps-create-api-googledrive/googledrive-create-project.PNG
[8]: ./media/powerapps-create-api-googledrive/use-google-apis.PNG
[9]: ./media/powerapps-create-api-googledrive/googledrive-api-overview.PNG
[10]: ./media/powerapps-create-api-googledrive/enable-googledrive-api.PNG
[11]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials.PNG
[12]: ./media/powerapps-create-api-googledrive/googledrive-api-credentials-add.PNG
[13]: ./media/powerapps-create-api-googledrive/configure-consent-screen.PNG
[14]: ./media/powerapps-create-api-googledrive/create-client-id.PNG
[15]: ./media/powerapps-create-api-googledrive/browseall.png
[16]: ./media/powerapps-create-api-googledrive/allresources.png

<!---HONumber=AcomDC_0309_2016-->