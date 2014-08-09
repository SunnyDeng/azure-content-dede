<properties linkid="develop-mobile-how-to-guides-register-for-microsoft-waad-authentication" urlDisplayName="Register for Azure Active Directory Authentication" pageTitle="Register for Azure Active Directory authentication - Mobile Services" metaKeywords="Azure registering application, Azure Active Directory authentication, application authenticate, authenticate mobile services" description="Learn how to register for Azure Active Directory authentication in your Azure Mobile Services application." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Register your apps to use an Azure Active Directory Account login" authors="" />

Registrieren Ihrer Apps für die Verwendung einer Azure Active Directory-Anmeldung
=================================================================================

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Azure Active Directory als Authentifizierungsanbieter für Azure Mobile Services zu verwenden.

**Hinweis**

Wenn Sie auch eine clientgesteuerte Authentifizierung für einmaliges Anmelden (SSO, Single Sign-On) oder Pushbenachrichtigungen aus einer Windows Store-App bereitstellen möchten, sollten Sie die Registrierung Ihrer App beim Windows Store in Betracht ziehen. Weitere Informationen finden Sie unter [Registrieren Ihrer Windows Store-Apps für die Windows Live Connect-Authentifizierung](/de-de/develop/mobile/how-to-guides/register-for-single-sign-on).

1.  Melden Sie sich auf dem [Windows Azure-Verwaltungsportal](https://manage.windowsazure.com/) an.

2.  Navigieren Sie zu **Active Directory** im Verwaltungsportal und klicken Sie auf Ihr Directory.

    ![](./media/mobile-services-app-active-directory-login/mobile-services-live-connect-add-app.png)

3.  Klicken Sie auf die Registerkarte **Applications** und anschließend auf **Add an App**.

    ![](./media/mobile-services-app-active-directory-login/mobile-live-connect-app-api-settings.png)

4.  Folgen Sie den Anweisungen im Assistenten für die neue Anwendung, und wählen Sie **Web Application And/Or Web API** für XXX aus. Aktivieren Sie die einmalige Anmeldung. Wenn Sie zur Eingabe der **App URL** aufgefordert werden, fügen Sie die Anwendungs-URL des mobilen Diensts ein.

5.  *** WEITERES FOLGT ***

Sie können nun eine Azure Active Directory-Anmeldung für die Authentifizierung in Ihrer App verwenden, indem Sie die Client-ID und den geheimen Clientschlüssel für Mobile Services bereitstellen.

