<properties
	pageTitle="OAUTH-Sicherheitseinstellungen in SaaS-Connectors und API-Apps | Azure"
	description="Informationen zu OAUTH-Sicherheitseinstellungen in den Connectors und API-Apps in Azure App Service; Microservices-Architektur; SaaS"
	services="app-service\logic"
	documentationCenter=""
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="mandia"/>


# Wissenswertes zu OAUTH-Sicherheitseinstellungen in SaaS-Connectors
Viele der SaaS-Connectors (Software as a Service) wie Facebook, Twitter, DropBox usw. erfordern von den Benutzern eine Authentifizierung über das OAUTH-Protokoll. Wenn Sie diese SaaS-Connectors in Logik-Apps verwenden, bieten wir eine vereinfachte Benutzerfunktionalität, bei der Sie im Logik-Apps-Designer auf "Autorisieren" klicken. Beim **Autorisieren** werden Sie aufgefordert, sich anzumelden (sofern nicht bereits erfolgt) und Ihre Zustimmung zum Herstellen einer Verbindung mit dem SaaS-Dienst in Ihrem Namen zu geben. Nachdem Sie die Zustimmung und Autorisierung erteilen, können Ihre Logik-Apps anschließend auf diese SaaS-Dienste zugreifen.

## Erstellen Ihrer eigenen SaaS-App
Diese vereinfachte Bedienung ist möglich, da wir unsere Anwendung zuvor in diesen Saas-Diensten erstellt und registriert haben. In bestimmten Fällen empfiehlt es sich, Ihre eigene Anwendung zu registrieren und zu verwenden. Dies ist z. B. erforderlich, wenn Sie diese SaaS-Connectors in Ihrer benutzerdefinierten Anwendung verwenden möchten, wie beispielsweise unter [Bereitstellen einer SaaS-Connector-API-App](app-service-api-connnect-your-app-to-saas-connector.md). Bei diesem Beispiel wird der DropBox-Connector verwendet, doch der Prozess ist für alle Connectors identisch, die OAUTH nutzen.

Auch im Kontext von Logik-Apps können Sie Ihre eigene Anwendung anstelle der Standardanwendung einsetzen, die wir bereitstellen. Wenn über die Schaltfläche "Autorisieren" keine Verbindung hergestellt werden kann, können Sie versuchen, eine eigene App zu erstellen. Im folgenden sind diese Schritte für den Twitter-Connector aufgeführt:

1. Öffnen Sie Ihren Twitter-Connector im Azure-Vorschauportal. Wechseln Sie zu **Durchsuchen** > **API-Apps**. Wählen Sie Ihren Twitter-Connector aus: ![][1]

2. Wählen Sie **Einstellungen** > **Authentifizierung**: ![][2]

3. Kopieren Sie den Wert von **Umleitungs-URI**: ![][3]

4. Wechseln Sie zu [Twitter](http://apps.twitter.com) und **Neue App erstellen**. Fügen Sie in die Eigenschaft **Rückruf-URL** den Wert von **Umleitungs-URI** ein, den Sie aus dem Twitter-Connector kopiert haben: ![][4]
5. Wählen Sie nach Erstellen der Twitter-App **Schlüssel und Zugriffstoken** aus. Kopieren Sie diese Werte.
6. Fügen Sie in diese Werte in den Authentifizierungseinstellungen Ihres Twitter-Connectors in die Eigenschaften **Client-ID** und **Geheimer Clientschlüssel** ein: ![][5]  
7. Speichern Sie Ihre Connectoreinstellungen.  

Jetzt sollten Sie den Connector in Logik-Apps verwenden können. Wenn Sie diesen Connector in Logik-Apps verwenden, wird Ihre Anwendung anstelle der Standardanwendung verwendet.

> [AZURE.NOTE]Wenn Sie zuvor eine App autorisiert haben, müssen Sie die App möglicherweise erneut autorisieren.


<!--Image references-->
[1]: ./media/app-service-logic-oauth-security/TwitterConnector.png
[2]: ./media/app-service-logic-oauth-security/Authentication.png
[3]: ./media/app-service-logic-oauth-security/RedirectURI.png
[4]: ./media/app-service-logic-oauth-security/TwitterApp.png
[5]: ./media/app-service-logic-oauth-security/TwitterKeys.png

<!---HONumber=Oct15_HO4-->