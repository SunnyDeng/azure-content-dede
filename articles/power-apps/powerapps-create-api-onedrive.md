<properties
	pageTitle="Hinzufügen der OneDrive-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen OneDrive-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Erstellen einer neuen OneDrive-API in PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-onedrive.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-onedrive.md)

Fügen Sie die OneDrive-API in der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][14]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][15]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Zu registrierten APIs navigieren][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzufügen: ![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs** aus , um die vorgefertigten APIs auszuwählen, und wählen Sie dann **OneDrive** aus: ![OneDrive-API auswählen][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Einstellungen für die OneDrive-API konfigurieren][4]

9. Geben Sie den *App-Schlüssel* und den *geheimen App-Schlüssel* Ihrer OneDrive-Anwendung ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer OneDrive-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für den Schlüssel und den geheimen Schlüssel.

	> [AZURE.IMPORTANT] Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue OneDrive-API hinzugefügt.

## Optional: Registrieren einer OneDrive-App zur Verwendung mit PowerApps

Wenn Sie über keine vorhandene OneDrive-App mit den Werten für den Schlüssel und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.

1. Wechseln Sie zu der [App-Erstellungsseite][5] im _Developer Center des Microsoft-Kontos_ und melden Sie sich mit Ihrem _Microsoft-Konto_ an.

2. Geben Sie den **Anwendungsnamen** ein und wählen Sie **Ich stimme zu**: ![Neue OneDrive-App][6]

3. Auf der Einstellungsseite:

	1. Wählen **API-Einstellungen** aus.  
	2. Legen Sie die Umleitungs-URL auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen OneDrive-API im Azure-Portal erhalten haben (in diesem Thema).  
	3. Wählen Sie **Speichern** aus.  

	![API-Einstellungen der OneDrive-App][7]

Eine neue OneDrive-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer OneDrive-API im Azure-Portal verwenden.

## Informationen zu REST-APIs

Referenz zur [OneDrive-REST-API](../connectors/create-api-onedrive.md)

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die OneDrive-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-onedrive/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-onedrive/add-api.PNG
[3]: ./media/powerapps-create-api-onedrive/select-onedrive-api.PNG
[4]: ./media/powerapps-create-api-onedrive/configure-onedrive-api.PNG
[5]: https://account.live.com/developers/applications/create
[6]: ./media/powerapps-create-api-onedrive/onedrive-new-app.PNG
[7]: ./media/powerapps-create-api-onedrive/onedrive-app-api-settings.PNG
[14]: ./media/powerapps-create-api-onedrive/browseall.png
[15]: ./media/powerapps-create-api-onedrive/allresources.png

<!---HONumber=AcomDC_0309_2016-->