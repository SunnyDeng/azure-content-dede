<properties
	pageTitle="Veröffentlichen von systemeigenen Client-Apps mit Proxyanwendungen | Microsoft Azure"
	description="Erläutert, wie Sie es systemeigenen Client-Apps ermöglichen, mit einem Azure AD-Anwendungsproxyconnector zu kommunizieren und so einen sicheren Remotezugriff auf lokale Anwendungen bereitzustellen."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="steven.powell"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/02/2015"
	ms.author="rkarlin"/>

# Aktivieren von systemeigenen Client-Apps für die Interaktion mit Proxyanwendungen
Mit Azure Active Directory-Anwendungsproxy werden häufig Browseranwendungen wie SharePoint, Outlook Web Access und benutzerdefinierte Branchenanwendungen veröffentlicht. Außerdem können damit HTTP-Back-End-Anwendungen veröffentlicht werden, die über systemeigene Clients genutzt werden. Dies erfolgt durch die Unterstützung von durch Azure AD ausgegebenen Token, die in standardmäßigen Authorize- HTTP-Headern gesendet werden.


![](./media/active-directory-application-proxy-native-client/richclientflow.png)


Zum Veröffentlichen solcher Anwendungen wird empfohlen, die Azure AD-Authentifizierungsbibliothek zu verwenden. Sie übernimmt die aufwendige Authentifizierung und unterstützt viele verschiedene Clientumgebungen. Der Anwendungsproxy gehört zum Szenario [Systemeigene Anwendung zu Web-API](https://azure.microsoft.com/documentation/articles/active-directory-authentication-scenarios/#native-application-to-web-api). Die Vorgehensweise hierfür ist wie folgt:

1. Veröffentlichen Sie Ihre Proxyanwendung wie jede andere Anwendung, weisen Sie Benutzer zu und geben Sie ihnen Premium- oder Basic-Lizenzen. Weitere Informationen finden Sie unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](active-directory-application-proxy-publish.md).
2. Konfigurieren Sie Ihre systemeigene Anwendung wie folgt:
  3. Melden Sie sich beim Azure-Verwaltungsportal an.
  4. Klicken Sie im linken Menü auf das Active Directory-Symbol, und klicken Sie dann auf das gewünschte Verzeichnis.
  5. Klicken Sie im oberen Menü auf Anwendungen. Wenn Ihrem Verzeichnis keine Apps hinzugefügt wurden, wird auf dieser Seite der Link „App hinzufügen“ angezeigt. Klicken Sie auf den Link, oder klicken Sie alternativ auf der Befehlsleiste auf die Schaltfläche „Hinzufügen“.
  4. Klicken Sie auf der Seite **Was möchten Sie tun?** auf den Link **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.
  5. Geben Sie auf der Seite **Geben Sie uns Informationen zu Ihrer Anwendung** den Namen Ihrer Anwendung an, und wählen Sie **Systemeigene Clientanwendung** aus. Diese Einstellung steht für eine Anwendung, die auf einem Gerät wie einem Smartphone oder einem Computer installiert wird. Danach klicken Sie auf das Pfeilsymbol in der unteren rechten Ecke der Seite.
  6. Geben Sie auf der Seite **App-Eigenschaften** den **Umleitungs-URI** für die systemeigene Clientanwendung an, und klicken Sie dann auf das Häkchen unten rechts auf der Seite. </br>Ihre Anwendung wurde hinzugefügt, und Sie werden auf die Seite „Schnellstart“ für Ihre Anwendung geführt. 
8. Machen Sie die systemeigene Anwendung für andere Anwendungen in Ihrem Verzeichnis verfügbar:
  9. Klicken Sie im oberen Menü auf **Anwendungen**, wählen Sie die neue systemeigene Anwendung aus, und klicken Sie auf **Konfigurieren**.
  10. Scrollen Sie nach unten zum Abschnitt **Berechtigungen für andere Anwendungen**. Klicken Sie auf die Schaltfläche **Anwendung hinzufügen**, und wählen Sie die Proxyanwendung aus, auf die die systemeigene Anwendung Zugriff erhalten soll, und klicken Sie auf das Häkchen in der unteren rechten Ecke. Wählen Sie im Dropdownmenü **Delegierte Berechtigungen** die neue Berechtigung aus. </br>

![](./media/active-directory-application-proxy-native-client/delegate_native_app.png) </br></br> 4. Bearbeiten Sie den Code für systemeigene Anwendungen im Authentifizierungskontext der Active Directory-Authentifizierungsbibliothek (ADAL, Active Directory Authentication Library), sodass er Folgendes enthält:

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >", 
                                                        "< Client Id of the Native app>", 
                                                        new Uri("< Redirect Uri of the Native App>"), 
                                                        PromptBehavior.Never);
		
		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

Die Variablen müssen wie folgt ersetzt werden:


- **TenantId** finden Sie in der URL der Seite **Konfiguration** der Anwendung in der GUID, direkt nach „/Directory/“.
- **Frontend URL** ist die Front-End-URL, die Sie in der Proxyanwendung eingegeben haben. Sie finden sie auf der Seite **Konfiguration** der Proxyanwendung.
- Die **Client Id** der systemeigenen Anwendung finden Sie auf der Seite **Konfigurieren** der systemeigenen Anwendung.
- Den **Redirect URI** der systemeigenen Anwendung finden Sie auf der Seite **Konfigurieren** der systemeigenen Anwendung.

![](./media/active-directory-application-proxy-native-client/new_native_app.png) </br> </br>Weitere Informationen über den Fluss bei systemeigenen Anwendungen finden Sie unter [Systemeigene Anwendung zu Web-API](https://azure.microsoft.com/de-DE/documentation/articles/active-directory-authentication-scenarios/#native-application-to-web-api).






## Wie geht es weiter?
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:


- [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Arbeiten mit Anwendungen, die Ansprüche unterstützen](active-directory-application-proxy-claims-aware-apps.md)
- [Aktivieren des bedingten Zugriffs](active-directory-application-proxy-conditional-access.md)


### Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen
* [Als Organisation für Azure registrieren](sign-up-organization.md)
* [Azure-Identität](fundamentals-identity.md)

<!---HONumber=Nov15_HO2-->