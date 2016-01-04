<properties
	pageTitle="Hinzufügen der Dropbox-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Dropbox-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="linhtranms"
	manager="dwerde"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>

# Erstellen einer neuen Dropbox-API in der App Service-Umgebung Ihrer Organisation

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][12]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][13]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Zu registrierten APIs navigieren][4]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzuzufügen: ![API hinzufügen][5]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs**, um die vorgefertigten APIs zu sehen, und wählen Sie dann **Dropbox**: ![Dropbox-API wählen][6]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Einstellungen für die Dropbox-API konfigurieren][7]

9. Geben Sie die Werte für den **App-Schlüssel** und den **geheimen App-Schlüssel** Ihrer Dropbox-Anwendung ein. Wenn Sie nicht bereits über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer Dropbox-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für den Schlüssel und den geheimen Schlüssel.

	> [AZURE.IMPORTANT]Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.


Ihrer App Service-Umgebung wird dann eine neue Dropbox-API hinzugefügt.


## Optional: Registrieren einer Dropbox-App zur Verwendung mit PowerApps

Wenn Sie über keine vorhandene Dropbox-App mit den Werten für den Schlüssel und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.

1. Wechseln Sie zu [Dropbox][1] und melden Sie sich mit Ihrem Konto an.

2. Wechseln Sie zur Dropbox-Entwicklerwebsite und wählen Sie **My apps**: ![Dropbox-Entwicklerwebsite][8]

3. Klicken Sie auf **Create app**: ![Dropbox-App erstellen][9]

4. In **Create a new app on the Dropbox Platform**:

	(a) Wählen Sie unter **Choose an API** **Dropbox API**. b) Wählen Sie unter **Choose the type of access you need** **Full Dropbox...**. c) Geben Sie einen Namen für Ihre App ein.

	![Dropbox-App erstellen Seite 1][10]

5. Auf der Seite mit den App-Einstellungen:

	a) Legen Sie die **Umleitungs-URL** im Abschnitt **OAuth 2** auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen Dropbox-API im Azure-Portal erhalten haben (weiter oben in diesem Thema). Wählen Sie **Hinzufügen**. b) Wählen Sie den Link **Anzeigen**, damit der **geheime App-Schlüssel** sichtbar wird:

	![Dropbox-App erstellen Seite 2][11]

Eine neue Dropbox-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Dropbox-API im Azure-Portal verwenden.


## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die Dropbox-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: https://www.dropbox.com/login
[2]: https://www.dropbox.com/developers/apps/create
[3]: https://www.dropbox.com/developers/apps
[4]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[5]: ./media/powerapps-create-api-dropbox/add-api.PNG
[6]: ./media/powerapps-create-api-dropbox/select-dropbox-api.PNG
[7]: ./media/powerapps-create-api-dropbox/configure-dropbox-api.PNG
[8]: ./media/powerapps-create-api-dropbox/dropbox-developer-site.PNG
[9]: ./media/powerapps-create-api-dropbox/dropbox-create-app.PNG
[10]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page1.PNG
[11]: ./media/powerapps-create-api-dropbox/dropbox-create-app-page2.PNG


[12]: ./media/powerapps-create-api-dropbox/browseall.png
[13]: ./media/powerapps-create-api-dropbox/allresources.png

<!---HONumber=AcomDC_1203_2015-->