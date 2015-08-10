<properties 
	pageTitle="Registrieren für die Microsoft Authentifizierung – Mobile Services" 
	description="Erfahren Sie mehr über die Registrierung für die Microsoft-Authentifizierung in Ihrer Azure Mobile Services-Anwendung." 
	authors="ggailey777" 
	services="mobile-services" 
	documentationCenter="Mobile" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="06/14/2015" 
	ms.author="glenga"/>

# Registrieren Ihrer App für die Verwendung eines Microsoft-Kontos für die Authentifizierung

[AZURE.INCLUDE [mobile-services-selector-register-identity-provider](../../includes/mobile-services-selector-register-identity-provider.md)]

## Übersicht 

In diesem Thema erfahren Sie, wie Sie Ihre mobile App registrieren, um ihr Microsoft-Konto als Identitätsanbieter mit Azure Mobile Services verwenden zu können. Die gleichen Schritte gelten für die dienstgesteuerte und die Client-gesteuerte Authentifizierung mithilfe des Live SDK.

##Registrieren der Windows Store-App im Windows-Entwicklungscenter

Windows Store-Apps müssen zunächst im Windows-Entwicklungscenter registriert werden.

>[AZURE.NOTE]Windows Phone 8, Windows Phone 8.1 Silverlight und nicht auf Windows basierte Anwendungen können diesen Abschnitt überspringen.

1. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu [Anwendungsseite senden], melden Sie sich mit Ihrem Microsoft-Konto an und klicken Sie dann auf **Anwendungsname**.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-submit-win8-app.png)

2. Wählen Sie **Eine neue App erstellen, indem Sie einen eindeutigen Namen reservieren** und klicken Sie auf **Weiter**. Geben Sie dann in **App-Name** einen Namen für Ihre App ein, klicken Sie auf **App-Name reservieren**, und klicken Sie dann auf **Speichern**.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-name.png)

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Öffnen Sie in Visual Studio das Projekt, das Sie beim Ausführen des Lernprogramms [Erste Schritte mit Mobile Services](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md) erstellt haben.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows Store-App-Projekt, klicken Sie auf **Store**, und klicken Sie dann auf **App mit Store verknüpfen**.

  	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-store-association.png)

   	Auf diese Weise wird der Assistent zum **Zuordnen Ihrer Anwendung zum Windows Store** angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**, und melden Sie sich mit Ihrem Microsoft-Konto an. Wählen Sie den App-Namen aus, den Sie in Schritt 2 reserviert haben, und klicken Sie auf **Weiter** \> **Zuordnen**.

   	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

6. \(Optional\) Für universelle Windows-Apps wiederholen Sie die Schritte 4 und 5 für das Windows Phone Store-Projekt.

6. Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Dienste**.

   	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-edit-app.png)

7. Klicken Sie auf der Seite "Dienste" auf **Live-Dienste-Website** unter **Azure Mobile Services**.

	![](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-edit2-app.png)

Die Microsoft-Kontoseite für Ihre App wird nun angezeigt.

## Konfigurieren Sie Ihre Microsoft-Kontoanmeldung und stellen Sie eine Verbindung zu Mobile Services her

Der erste Schritt in diesem Abschnitt gilt nur für Windows Phone 8, Windows Phone 8.1 Silverlight und Apps, die nicht Windows Store-basiert sind. Für diese Anwendungen können Sie auch die Paket-Sicherheits-ID \(SID\) ignorieren, die nur für Windows Store-Apps verfügbar ist.

1. Für eine App, die nicht Windows Store-basiert ist, navigieren Sie zu der Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=262039" target="_blank">Meine Anwendungen</a> im Entwicklercenter des Microsoft-Kontos, melden sich mit Ihrem Microsoft-Konto an \(falls erforderlich\), klicken auf **Anwendung erstellen**, geben dann einen **Anwendungsnamen** ein und klicken auf **Ich stimme zu**.

   	Dadurch wird ein App-Name mit dem Microsoft-Konto reserviert und die Microsoft-Kontoseite für Ihre App angezeigt.

2. Klicken Sie auf der Microsoft-Kontoseite für Ihre App auf **API-Einstellungen**, aktivieren Sie das Kontrollkästchen **Mobile oder Desktop-Client-App**, legen Sie die mobile Dienst-URL als **Zieldomäne** fest, geben Sie den Wert `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` in die **Umleitungs-URL** ein, und klicken Sie anschließend auf **Speichern**.

	 \>[AZURE.NOTE]Für einen mit Visual Studio in Azure veröffentlichten mobilen .NET-Back-End-Dienst ist die Umleitungs-URL die URL Ihres mobilen Diensts mit dem Pfadsuffix _signin-microsoft_ für Ihren mobilen Dienst als .NET-Dienst, z. B. `https://todolist.azure-mobile.net/signin-microsoft`.

    ![API-Einstellungen im Microsoft-Konto](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth-2.png)

	Die **Stammdomäne** sollte automatisch ausgefüllt werden.

4. Klicken Sie auf **App-Einstellungen** und notieren Sie sich die Werte unter **Client-ID**, **Geheimer Clientschlüssel** und **Paket-SID**.
	
   	![App-Einstellungen im Microsoft-Konto](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-win8-app-push-auth.png)
	
	
    > [AZURE.NOTE]Der geheime Clientschlüssel ist eine wichtige Anmeldeinformation. Teilen Sie den geheimen Clientschlüssel mit niemandem, und geben Sie ihn nicht über Ihre App frei. Nur für App-Registrierungen in Windows Store wird ein Paket-SID-Feld angezeigt.

4. Klicken Sie im [Azure-Verwaltungsportal] auf die Registerkarte **Identität** für den mobilen Dienst, geben Sie die Client-ID, den geheimen Clientschlüssel und die Paket-SID von Ihrem Identitätsanbieter ein, und klicken Sie auf **Speichern**.

 	![Registerkarte "Mobile Services-Identität"](./media/mobile-services-how-to-register-microsoft-authentication/mobile-services-identity-tab.png)
	
	>[AZURE.NOTE]Für Windows Phone 8, Windows Phone Store 8.1 Silverlight und nicht auf Windows basierte Anwendungen ist kein Paket-SID-Wert erforderlich.
	
Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit dem Microsoft-Konto.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->

[Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO5-->