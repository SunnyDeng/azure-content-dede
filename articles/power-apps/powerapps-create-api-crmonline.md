<properties
	pageTitle="Hinzufügen der Dynamics CRM Online-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Dynamics CRM Online-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter=""
	authors="schabungbam"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/25/2015"
   ms.author="sameerch"/>

# Erstellen einer neuen Dynamics CRM Online-API in der App Service-Umgebung Ihrer Organisation

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.

2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][1]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][2]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Zu registrierten APIs navigieren][3]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzuzufügen: ![API hinzufügen][4]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.

7. Wählen Sie in **Source** die Option **Available APIs** aus, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **Dynamics CRM Online** aus: ![Dynamics CRM Online-API wählen][5]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Einstellungen der Dynamics CRM Online-API konfigurieren][6]

9. Geben Sie die **Client-ID** und den **geheimen App-Schlüssel ** Ihrer Azure Active Directory (AAD)-Anwendung für Dynamics CRM Online ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer AAD-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für die ID und den geheimen Schlüssel.

	> [AZURE.IMPORTANT]Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue Dynamics CRM Online-API hinzugefügt.

## Registrieren einer AAD-App zur Verwendung mit der Dynamics CRM Online-API in PowerApps

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com).

2. Wählen Sie **Durchsuchen** und dann **Active Directory** aus:

	> [AZURE.NOTE]Damit wird Active Directory im klassischen Azure-Portal geöffnet.

3. Wählen Sie den Mandantennamen Ihrer Organisation aus: ![Azure Active Directory starten][7]

4. Klicken Sie auf die Registerkarte **Anwendungen**, und wählen Sie **Hinzufügen** aus: ![AAD-Mandanten-Anwendungen][8]

5. Auf der Seite **Anwendung hinzufügen**:

	a) Geben Sie unter **Name** einen Namen für Ihre Anwendung ein. b) Übernehmen Sie unter „Typ“ die aktivierte Option **Web**. c) Wählen Sie **Weiter** aus.

	![AAD-Anwendung hinzufügen – App-Info][9]

6. Unter **App-Eigenschaften**:

	a) Geben Sie unter **URL FÜR ANMELDUNG** die Anmelde-URL Ihrer Anwendung ein. Da Sie die Authentifizierung mit AAD für PowerApps durchführen, legen Sie die Anmelde-URL auf \__https://login.windows.net_ fest. b) Geben Sie einen gültigen **APP-ID-URI** für Ihre App ein. c) Wählen Sie **OK** aus.

	![AAD-Anwendung hinzufügen – App-Eigenschaften][10]

7. Nach erfolgreichem Abschluss werden Sie zu der neuen AAD-App weitergeleitet. Wählen Sie **Konfigurieren** aus: ![Contoso-AAD-App][11]

8. Legen Sie die **Antwort-URL** im Abschnitt _OAuth 2_ auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen Dynamics CRM Online-API im Azure-Portal erhalten haben (weiter oben in diesem Thema): ![Contoso-AAD-App konfigurieren][12]

9. Wählen Sie **Speichern** aus.

Eine neue Azure Active Directory-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Dynamics CRM Online-API im Azure-Portal verwenden.

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die Dynamics CRM Online-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)

<!-- References -->

[1]: ./media/powerapps-create-api-crmonline/browseall.png
[2]: ./media/powerapps-create-api-crmonline/allresources.png
[3]: ./media/powerapps-create-api-crmonline/browse-to-registered-apis.PNG
[4]: ./media/powerapps-create-api-crmonline/add-api.PNG
[5]: ./media/powerapps-create-api-crmonline/select-crmonline-api.PNG
[6]: ./media/powerapps-create-api-crmonline/configure-crmonline-settings.PNG
[7]: ./media/powerapps-create-api-crmonline/launch-aad.PNG
[8]: ./media/powerapps-create-api-crmonline/aad-tenant-applications.PNG
[9]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-appinfo.PNG
[10]: ./media/powerapps-create-api-crmonline/aad-tenant-applications-add-app-properties.PNG
[11]: ./media/powerapps-create-api-crmonline/contoso-aad-app.PNG
[12]: ./media/powerapps-create-api-crmonline/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_1203_2015-->