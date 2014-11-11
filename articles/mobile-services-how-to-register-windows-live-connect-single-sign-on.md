<properties pageTitle="Register for single sign-on - Azure Mobile Services" metaKeywords="" description="Learn how to register for single sign-on authentication in your Azure Mobile Services application." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Register your Windows Store apps to use Windows Live Connect single sign-on" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrieren Ihrer Windows Store-App für die einmalige Anmeldung mit Windows Live Connect

In diesem Thema erfahren Sie, wie Sie Ihre App im Windows Store registrieren, um Live Connect als Authentifizierungsanbieter für einmalige Anmeldung in Azure Mobile Services zu verwenden. Dieser Schritt wird ebenfalls für die Verwendung von Pushbenachrichtigungen benötigt.

<div class="dev-callout"><b>Hinweis</b>
<p>Sie m&uuml;ssen Ihre App nicht im Windows Store registrieren, um Microsoft-Konten f&uuml;r die Authentifizierung zu verwenden, bevor Sie Ihre App ver&ouml;ffentlichen. Falls Ihre Windows Store-App keine einmalige Anmeldung oder Pushbenachrichtigungen ben&ouml;tigt, k&ouml;nnen Sie die App einfach mit Live Connect f&uuml;r die Verwendung von Microsoft-Konten registrieren.  Weitere Informationen finden Sie unter <a href="/de-de/develop/mobile/how-to-guides/register-for-microsoft-authentication">Registrieren Ihrer Windows Store-Apps zum Verwenden von Microsoft-Konten</a>.</p>
</div>

1.  Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Submit an app page][Submit an app page], melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App name**.

    ![][0]

2.  Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.

    ![][1]

    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3.  Öffnen Sie im Visual Studio 2012 Express für Windows 8 das Projekt, das Sie beim Abschluss des Lernprogramms [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] erstellt haben.

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Anwendung dem Store zuordnen**.

    ![][2]

    Auf diese Weise wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

5.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

6.  Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

    ![][3]

    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

7.  Navigieren Sie zur Seite [My Applications][My Applications] im Live Connect Developer Center, und klicken Sie in der Liste **My applications** auf Ihre App.

    ![][4]

8.  Klicken Sie auf **Edit Settings**, dann auf **API Settings** und notieren Sie sich den Wert für **Client secret**.

    ![][5]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Der geheime Clientschl&uuml;ssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschl&uuml;ssel mit niemandem, und geben Sie ihn nicht &uuml;ber Ihre App frei.</p>
</div>

9.  Geben Sie unter **Redirect domain** die URL Ihres mobilen Dienstes aus Schritt 8 ein, und klicken Sie auf **Save**.

Nun können Sie die Authentifizierung mit Live Connect in Ihre App integrieren. Mobile Services bietet die beiden folgenden Methoden zur Authentifizierung von Benutzern mit Live Connect an:

-   Einmaliges Anmelden für Windows Store-Apps. Mit dieser Methode müssen die Benutzer die Authentifizierung in Ihrer App per Live Connect nur einmal autorisieren. Anschließend werden die Anmeldeinformationen anhand der Benutzerpräferenzen von Windows verwaltet. Weitere Informationen finden Sie unter [Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect][Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect].

-   Einfache Authentifizierung. Diese Methode unterstützt eine Vielzahl verschiedener Authentifizierungsanbieter, allerdings müssen sich die Benutzer bei jedem Start der App erneut anmelden. Weitere Informationen finden Sie unter [Get started with authentication][Get started with authentication] (Erste Schritte zur Authentifizierung, in englischer Sprache).



  [Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-win8-app-name.png
  [Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
  [2]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-store-association.png
  [3]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-services-select-app-name.png
  [My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
  [4]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-apps-list.png
  [5]: ./media/mobile-services-how-to-register-windows-live-connect-single-sign-on/mobile-live-connect-app-api-settings.png
  [Einmalige Anmeldung für Windows Store Apps mithilfe von Live Connect]: /de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
  [Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
