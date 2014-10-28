<properties linkid="develop-mobile-how-to-guides-register-for-google-authentication" urlDisplayName="Register for Google Authentication" pageTitle="Register for Google authentication - Mobile Services" metaKeywords="Azure registering application, Azure authentication, Google application authenticate, authenticate mobile services" description="Learn how to register your apps to use Google to authenticate with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your apps for Google login with Mobile Services" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registrieren Ihrer App für die Google-Anmeldung mit Mobile Services

In diesem Thema erfahren Sie, wie Sie Ihre Apps registrieren, um Google zur Authentifizierung mit Azure Mobile Services zu verwenden.

<div class="dev-callout"><b>Hinweis</b>
<p>Sie ben&ouml;tigen ein Google-Konto mit verifizierter E-Mail-Adresse, um den in diesem Thema beschriebenen Vorgang abzuschlie&szlig;en. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.</p>
</div>

1.  Navigieren Sie zur [Google APIs][Google APIs]-Website, melden Sie sich mit den Anmeldeinformationen für Ihr Google-Konto an, klicken Sie auf **Create project**, geben Sie einen **Project name** an, und klicken Sie auf **Create**.

    ![][]

2.  Klicken Sie auf **API & Auth**, auf **Credentials** und dann auf **Create new Client ID**.

    ![][1]

3.  Wählen Sie **Web application**, geben Sie in **Authorized JavaScript Origins** die URL Ihres mobilen Diensts ein, ersetzen Sie die generierte URL in **Authorized Redirect URI** durch die URL des mobilen Diensts, fügen Sie den Pfad */login/google* an die URL an, und klicken Sie dann auf **Create client ID**.

    > [WACOM.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix *signin-google* für Ihren mobilen Dienst als .NET-Dienst, z. B. `https://todolist.azure-mobile.net/signin-google`.

    ![][2]

4.  Notieren Sie unter **Client-ID für Webanwendungen** die Werte für **Client-ID** und **Geheimer Clientschlüssel**.

    ![][3]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Der geheime Clientschl&uuml;ssel ist eine wichtige Anmeldeinformation. Teilen Sie diesen Schl&uuml;ssel mit niemandem, und geben Sie ihn nicht &uuml;ber Ihre App frei.</p>
</div>

Sie können nun ein Google-Login für die Authentifizierung in Ihrer App verwenden, indem Sie die Werte für Client-ID und geheimen Clientschlüssel für Mobile Services bereitstellen.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Google APIs]: http://go.microsoft.com/fwlink/p/?LinkId=268303
  []: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-new-project.png
  [1]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client.png
  [2]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client2.png
  [3]: ./media/mobile-services-how-to-register-google-authentication/mobile-services-google-create-client3.png
