<properties
	pageTitle="Hinzufügen der SharePoint Online-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen SharePoint Online-API in der App Service-Umgebung Ihrer Organisation"
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

# Erstellen einer neuen SharePoint Online-API in PowerApps Enterprise

Fügen Sie die SharePoint Online-API in der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste **Durchsuchen**: ![][14]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][15]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Navigieren zu registrierten APIs][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzufügen: ![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
	
7. Wählen Sie in **Source** die Option **Available APIs** aus, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **SharePoint Online** aus: ![Auswählen der SharePoint Online-API][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus: ![Konfigurieren der Einstellungen der SharePoint Online-API][4]

9. Geben Sie die *Client-ID* und den *geheimen App-Schlüssel* Ihrer Azure Active Directory-Anwendung (AAD) für SharePoint Online ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer AAD-App zur Verwendung mit der SharePoint Online-API in PowerApps“ Informationen zum Erstellen der benötigten Werte für die ID und den geheimen Schlüssel.

	> [AZURE.IMPORTANT] Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue SharePoint Online-API hinzugefügt.


## Registrieren einer AAD-App zur Verwendung mit der SharePoint Online-API in PowerApps

1. Öffnen Sie das [Azure-Portal][5].

2. Wählen Sie **Durchsuchen** und dann **Active Directory** aus:

	> [AZURE.NOTE] Damit wird Active Directory im klassischen Azure-Portal geöffnet.

3. Wählen Sie den Mandantennamen Ihrer Organisation aus: ![Starten von Azure Active Directory][6]

4. Klicken Sie auf die Registerkarte **Anwendungen**, und wählen Sie **Hinzufügen** aus: ![AAD-Mandanten-Anwendungen][7]

5. Auf der Seite **Anwendung hinzufügen**:

	a) Geben Sie unter **Name** einen Namen für Ihre Anwendung ein. b) Übernehmen Sie unter „Typ“ die aktivierte Option **Web**. c) Wählen Sie **Weiter** aus.


	![AAD-Anwendung hinzufügen – App-Info][8]

6. Unter **App-Eigenschaften**:

	a) Geben Sie unter **URL FÜR ANMELDUNG** die Anmelde-URL Ihrer Anwendung ein. Da Sie die Authentifizierung mit AAD für PowerApps durchführen, legen Sie die Anmelde-URL auf \__https://login.windows.net_ fest. b) Geben Sie einen gültigen **APP-ID-URI** für Ihre App ein. c) Wählen Sie **OK** aus.

	![AAD-Anwendung hinzufügen – App-Eigenschaften][9]

7. Nach erfolgreichem Abschluss werden Sie zu der neuen AAD-App weitergeleitet. Wählen Sie **Konfigurieren** aus: ![Contoso-AAD-App][10]

8. Legen Sie die **Antwort-URL** im Abschnitt _OAuth 2_ auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen SharePoint Online-API im Azure-Portal erhalten haben (weiter oben in diesem Thema). Wählen Sie **Anwendung hinzufügen** aus: ![Contoso-AAD-App konfigurieren][11]

9. Wählen Sie im Fenster **Berechtigungen für andere Anwendungen** die Option **Office 365 Exchange Online** und dann **OK** aus: ![Contoso-App-Delegat][12]

10. Auf der Konfigurationsseite können Sie nun sehen, dass _Office 365 Exchange Online_ der Liste _Berechtigungen für andere Anwendungen_ hinzugefügt wurde.

11. Wählen Sie **Delegierte Berechtigungen** für _Office 365 Exchange Online_ und dann die folgenden Berechtigungen aus:

	- Read and write items in all site collections
	- Read and write items and lists in all site collections

	![Berechtigungen für Contoso-App-Delegat][13]

Eine neue Azure Active Directory-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer SharePoint Online-API im Azure-Portal verwenden.

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie die SharePoint Online-API zu PowerApps Enterprise hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)

<!--References-->
[1]: ./media/powerapps-create-api-sharepointonline/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-sharepointonline/add-api.PNG
[3]: ./media/powerapps-create-api-sharepointonline/select-sharepointonline-api.PNG
[4]: ./media/powerapps-create-api-sharepointonline/configure-sharepointonline-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-sharepointonline/launch-aad.PNG
[7]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-sharepointonline/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-configure.PNG
[12]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline.PNG
[13]: ./media/powerapps-create-api-sharepointonline/contoso-aad-app-delegate-sharepointonline-permissions.PNG
[14]: ./media/powerapps-create-api-sharepointonline/browseall.png
[15]: ./media/powerapps-create-api-sharepointonline/allresources.png

<!----HONumber=AcomDC_0309_2016-->