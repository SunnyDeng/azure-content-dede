<properties 
	pageTitle="Authentifizieren bei Mobile Engagement-REST-APIs – manuelles Setup"
	description="Beschreibt, wie Sie die Authentifizierung für Mobile Engagement-REST-APIs manuell einrichten" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="03/11/2016"
	ms.author="piyushjo"/>

# Authentifizieren bei Mobile Engagement-REST-APIs – manuelles Setup

Dies ist eine Zusatzdokumentation zu [Authentifizieren bei Mobile Engagement-REST-APIs](mobile-engagement-api-authentication.md). Lesen Sie den Artikel unbedingt zuerst, um den Kontext zu kennen. Hier wird eine alternative Möglichkeit zum einmaligen Einrichten Ihrer Authentifizierung für die Mobile Engagement-REST-APIs über das Azure-Portal beschrieben.

>[AZURE.NOTE] Die folgenden Anweisungen basieren auf diesem [Active Directory-Handbuch](../resource-group-create-service-principal-portal/) und wurden an die Anforderungen der Authentifizierung für Mobile Engagement-APIs angepasst. In diesem Handbuch können Sie nachschlagen, wenn Sie die folgenden Schritte im Detail verstehen möchten.

1. Melden Sie sich über das [klassische Portal](https://manage.windowsazure.com/) bei Ihrem Azure-Konto an.

2. Wählen Sie im linken Bereich **Active Directory** aus.

     ![Active Directory auswählen][1]

3. Wählen Sie in Ihrem Azure-Portal das **Standard-Active Directory** aus.

     ![Verzeichnis wählen][2]

	>[AZURE.IMPORTANT] Dieser Ansatz funktioniert nur, wenn Sie im Standard-Active Directory Ihres Kontos arbeiten. Er funktioniert nicht, wenn Sie diese Schritte in einem Active Directory durchführen, das Sie in Ihrem Konto erstellt haben.

4. Klicken Sie auf **Anwendungen**, um die Anwendungen in Ihrem Verzeichnis anzuzeigen.

     ![Anwendungen anzeigen][3]

5. Klicken Sie auf **HINZUFÜGEN**.

     ![Anwendung hinzufügen][4]

6. Klicken Sie auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.

     ![neue Anwendung][5]

6. Geben Sie den Namen der Anwendung ein, und wählen Sie den Typ der Anwendung als **WEBANWENDUNG UND/ODER WEB-API** aus. Klicken Sie dann auf die Schaltfläche „Weiter“.

     ![Anwendung benennen][6]

7. Sie können beliebige Dummy-URLs für **ANMELDE-URL** und **APP-ID-URI** angeben. Sie werden in diesem Szenario nicht verwendet, und die URLs selbst werden nicht überprüft.

     ![Anwendungseigenschaften][7]

8. Am Ende dieses Vorgangs besitzen Sie eine AAD-App wie die folgende, mit dem Namen, den Sie zuvor angegeben haben. Dies ist der **AD\_APP\_NAME**, den Sie notieren sollten.

     ![App-Name][8]

9. Klicken Sie auf den App-Namen, und klicken Sie auf **Konfigurieren**.

     ![App konfigurieren][9]

10. Notieren Sie sich die CLIENT-ID, die für Ihre API-Aufrufe als **CLIENT\_ID** verwendet wird.

     ![App konfigurieren][10]

11. Scrollen Sie nach unten zum Abschnitt **Schlüssel**, und fügen Sie einen Schlüssel mit einer Dauer von vorzugsweise 2 Jahren (Ablauf) hinzu, und klicken Sie auf **Speichern**.

     ![App konfigurieren][11]


12. Kopieren Sie sofort den Wert, der für den Schlüssel angezeigt wird. Er wird nur jetzt angezeigt, und er wird nicht gespeichert, daher ist er später nicht mehr abrufbar. Wenn er verloren geht, müssen Sie einen neuen Schlüssel generieren. Dies ist das **CLIENT\_SECRET** für Ihre API-Aufrufe.

     ![App konfigurieren][12]

	>[AZURE.IMPORTANT] Dieser Schlüssel läuft am Ende der angegebenen Dauer ab. Stellen Sie daher sicher, dass sie ihn zu gegebener Zeit erneuern, da Ihre API-Authentifizierung andernfalls nicht mehr funktioniert. Sie können diesen Schlüssel auch löschen und neu erstellen, wenn Sie vermuten, dass er offengelegt wurde.
 
13. Klicken Sie jetzt auf die Schaltfläche **ENDPUNKTE ANZEIGEN**, um das Dialogfeld **Anwendungsendpunkte** zu öffnen.

	![][13]

14. Kopieren Sie im Dialogfeld „Anwendungsendpunkte“ den **OAUTH 2.0-TOKEN-ENDPUNKT**.

	![][14]

15. Dieser Endpunkt liegt im folgenden Format vor, wobei die GUID in der URL Ihre **TENANT\_ID** darstellt, die Sie ebenfalls notieren sollten:

		https://login.microsoftonline.com/<GUID>/oauth2/token

16. Jetzt werden wir die Berechtigungen für diese App konfigurieren. Dazu müssen Sie das [Azure-Portal](https://portal.azure.com) öffnen.

17. Klicken Sie auf **Ressourcengruppen**, und suchen Sie die Ressourcengruppe **Mobile Engagement**.

	![][15]

18. Klicken Sie auf die Ressourcengruppe **Mobile Engagement**, und navigieren Sie zum Blatt **Einstellungen**.

	![][16]

19. Klicken Sie im Blatt „Einstellungen“ auf **Benutzer**, und klicken Sie dann auf **Hinzufügen**, um einen Benutzer hinzuzufügen.

	![][17]

20. Klicken Sie auf **Rolle auswählen**.

	![][18]

21. Klicken Sie auf **Besitzer**.

	![][19]

22. Suchen Sie im Suchfeld nach dem Namen der Anwendung **AD\_APP\_NAME**. Dieser wird nicht standardmäßig hier angezeigt. Sobald Sie ihn gefunden haben, wählen Sie ihn aus, und klicken Sie am unteren Rand des Blatts auf **Auswählen**.

	![][20]

23. Auf dem Blatt **Zugriff hinzufügen** wird die Anwendung mit **1 Benutzer, 0 Gruppen** angezeigt. Klicken Sie auf diesem Blatt auf **OK**, um die Änderung zu bestätigen.

	![][21]

Sie haben nun die erforderliche AAD-Konfiguration abgeschlossen und sind jetzt zum Aufrufen der APIs bereit.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png

<!---HONumber=AcomDC_0316_2016-->