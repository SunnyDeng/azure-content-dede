<properties
	pageTitle="Hinzufügen der Office 365-Outlook-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Office 365-Outlook-API in der App Service-Umgebung Ihrer Organisation"
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
   ms.date="11/25/2015"
   ms.author="litran"/>

# Erstellen einer neuen Office 365-Outlook-API in der App Service-Umgebung Ihrer Organisation

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie auf der Taskleiste die Option **Durchsuchen** aus: ![][14]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][15]

4. Wählen Sie in **PowerApps Services** die Option **Manage APIs** aus: ![Zu registrierten APIs navigieren][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzuzufügen: ![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs**, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **Office 365-Outlook** aus: ![Office 365-Outlook-API auswählen][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Einstellungen für die Office 365-Outlook-API konfigurieren][4]

9. Geben Sie den *App-Schlüssel* und den *geheimen App-Schlüssel * Ihrer Azure Active Directory-Anwendung (AAD) für Office 365 ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer AAD-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für den Schlüssel und den geheimen Schlüssel.
 
	> [AZURE.IMPORTANT]Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue Office 365-Outlook-API hinzugefügt.


## Optional: Registrieren einer AAD-App zur Verwendung mit der Office 365-API in PowerApps

Wenn Sie über keine vorhandene AAD-App mit den Werten für den Schlüssel und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.

1. Öffnen Sie das [Azure-Portal][5].

2. Wählen Sie **Durchsuchen** und dann **Active Directory** aus.

	>[AZURE.NOTE]Damit wird Active Directory im klassischen Azure-Portal geöffnet.

3. Wählen Sie den Mandantennamen Ihrer Organisation aus: ![Azure Active Directory starten][6]

4. Klicken Sie auf die Registerkarte **Anwendungen**, und wählen Sie **Hinzufügen** aus: ![AAD-Mandanten-Anwendungen][7]

5. Auf der Seite **Anwendung hinzufügen**:

	a) Geben Sie unter **Name** einen Namen für Ihre Anwendung ein. b) Übernehmen Sie unter „Typ“ die aktivierte Option **Web**. c) Wählen Sie **Weiter** aus.

	![AAD-Anwendung hinzufügen – App-Info][8]

6. Unter **App-Eigenschaften**:

	a) Geben Sie unter **URL FÜR ANMELDUNG** die Anmelde-URL Ihrer Anwendung ein. Da Sie die Authentifizierung mit AAD für PowerApps durchführen, legen Sie die Anmelde-URL auf \__https://login.windows.net_ fest. b) Geben Sie einen gültigen **APP-ID-URI** für Ihre App ein. c) Wählen Sie **OK** aus.

	![AAD-Anwendung hinzufügen – App-Eigenschaften][9]

7. Nach erfolgreichem Abschluss werden Sie zu der neuen AAD-App weitergeleitet. Wählen Sie **Konfigurieren** aus: ![Contoso-AAD-App][10]

8. Legen Sie die **Antwort-URL** im Abschnitt _OAuth 2_ auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen Office 365-Outlook-API im Azure-Portal erhalten haben (weiter oben in diesem Thema). Wählen Sie dann **Anwendung hinzufügen** aus: ![Contoso-AAD-App konfigurieren][11]

9. Wählen Sie im Fenster **Berechtigungen für andere Anwendungen** die Option **Office 365 Exchange Online** und dann **OK** aus: ![Contoso-App-Delegat][12]

10. Auf der Konfigurationsseite können Sie nun sehen, dass _Office 365 Exchange Online_ der Liste _Berechtigungen für andere Anwendungen_ hinzugefügt wurde.

11. Wählen Sie **Delegierte Berechtigungen** für _Office 365 Exchange Online_ und dann die folgenden Berechtigungen aus:

	- Lesen und Schreiben von Benutzerkontakten
	- Lesen von Benutzerkontakten
	- Lesen und Schreiben von Benutzerkalendern
	- Lesen von Benutzerkalendern
	- Senden von E-Mails als Benutzer
	- Lesen und Schreiben von Benutzer-E-Mails
	- Lesen von Benutzer-E-Mails

	![Berechtigungen für Contoso-App-Delegat][13]

Eine neue Azure Active Directory-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Office 365-Outlook-API im Azure-Portal verwenden.

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie PowerApps Enterprise die Office 365-Outlook-API hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-office365-outlook/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-outlook/add-api.PNG
[3]: ./media/powerapps-create-api-office365-outlook/select-office365-outlook-api.PNG
[4]: ./media/powerapps-create-api-office365-outlook/configure-office365-outlook-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-outlook/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-outlook/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.PNG
[13]: ./media/powerapps-create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_1203_2015-->