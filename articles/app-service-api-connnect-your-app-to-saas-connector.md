<properties 
	pageTitle="Verbinden Ihrer App mit einem vorhandenen SaaS-Connector" 
	description="In diesem Artikel wird veranschaulicht, wie Sie eine Verbindung mit einem vorhandenen SaaS-Connector herstellen." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# Verbinden Ihrer App mit einem vorhandenen SaaS-Connector

## Übersicht

In diesem Lernprogramm erfahren Sie, wie Sie einen SaaS-Connector nutzen, der im Azure Marketplace verfügbar ist. 

Sie führen die folgenden Schritte aus:
- Bereitstellen des Dropbox-Connectors
- Konfigurieren der Dropbox-API-App
- Konfigurieren des Gateways
- Abrufen und Speichern des Tokens im Tokenspeicher 
- Aufrufen der Dropbox-API und Sicherstellen, dass der authentifizierte Zugriff funktioniert

## Bereitstellen des Dropbox-Connectors

1. Wechseln Sie zur Startseite des Vorschauportals, und klicken Sie auf "Marketplace".

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. Suchen Sie in der Marketplace-Galerie nach Dropbox.

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Klicken Sie auf das Dropbox-Symbol, um den Dropbox-Connector bereitzustellen. Klicken Sie auf die Schaltfläche "Erstellen", um den Dropbox-Connector bereitzustellen. Stellen Sie sicher, dass Sie den Namen und gewünschte Werte für alle Felder ausfüllen, bevor Sie auf die Schaltfläche "Erstellen" klicken. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. Zeigen Sie das Blatt für die Ressourcengruppe im [Vorschauportal] (https://portal.azure.com/) an. Sie sehen den Dropbox-Connector und das Gateway. Wenn Sie eine API-App bereitgestellt haben, erfolgt die Bereitstellung als Bestandteil einer Ressourcengruppe. Als das Portal die Ressourcengruppe für Sie erstellt hat, wurde auch ein Gateway erstellt. Ein Gateway ist eine spezielle Web-App, die alle Anforderungen verarbeitet, die an API-Apps in der Ressourcengruppe gerichtet sind.

	Sie können Web-Apps und andere API-Apps zur selben Ressourcengruppe hinzufügen, und jede API-App innerhalb der Ressourcengruppe kann eine von drei Zugriffseinstellungen verwenden:

	* Öffentlich (anonym) - Beliebige Benutzer können die API-App von außerhalb der Ressourcengruppe und ohne Anmeldung aufrufen.
	* Öffentlich (authentifiziert) - Nur authentifizierte Benutzer können die API-App von außerhalb der Ressourcengruppe aufrufen.
	* Intern - Nur andere API-Apps oder Web-Apps in derselben Ressourcengruppe dürfen die API-App aufrufen.

## Konfigurieren der Dropbox-API-App

Konfigurieren Sie zunächst Dropbox so, dass nur authentifizierte Anforderungen akzeptiert werden.  Legen Sie die Zugriffseinstellung auf **Öffentlich (authentifiziert)** fest, und konfigurieren Sie das Gateway für eine Authentifizierung über einen Anbieter wie Azure Active Directory, Google oder Facebook.

1.	Klicken Sie im [Vorschauportal](https://portal.azure.com/) von Azure auf **Durchsuchen > API-Apps**, und klicken Sie dann auf den Namen der API-App, die Sie schützen möchten.

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	Klicken Sie im Blatt für die API-App auf "Einstellungen" und anschließend auf "Anwendungseinstellungen".
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	Ändern Sie im Blatt **Anwendungseinstellungen** die **Zugriffsebene** in **Öffentlich (authentifiziert)**. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	Damit haben Sie den Dropbox-Connector vor einem nicht authentifizierten Zugriff geschützt. Jetzt müssen Sie die Dropbox-Authentifizierung einrichten. Tragen Sie die Werte für *Client ID* und "Client Secret* wie nachfolgend gezeigt ein (Sie erhalten die Werte für *Client ID* und "Client Secret* über das [Dropbox-Entwicklerkonto](https://www.dropbox.com/developers/apps)).

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

Als Nächstes müssen Sie das Gateway konfigurieren, um den Authentifizierungsanbieter anzugeben.

## Konfigurieren des Gateways

1. Wechseln Sie zurück zum Blatt für die Dropbox-API-App, und klicken Sie auf den Link zum Gateway.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. Wählen Sie den gewünschten Identitätsanbieter aus, und führen Sie die Schritte zum Konfigurieren Ihrer API-App für diesen Anbieter aus, die im entsprechenden Artikel aufgeführt sind. Diese Artikel wurden für mobile Apps geschrieben, die Vorgehensweise für API-Apps ist jedoch identisch. Für einige Verfahren benötigen Sie sowohl das [Verwaltungsportal](https://manage.windowsazure.com/) als auch das [Vorschauportal](https://portal.azure.com/).
  
	- [Microsoft-Konto](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Facebook-Anmeldung](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Twitter-Anmeldung](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Google-Anmeldung](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	Schrittanleitungen für die Konfiguration finden Sie im Artikel "Schützen einer API-App: Hinzufügen der Authentifizierung über Azure Active Directory oder ein soziales Netzwerk". 

## Rufen Sie das OAuth-Token ab, und speichern Sie es im Tokenspeicher.

1.	Wechseln Sie im Browser zu dieser Anmelde-URL: 

	Sie können die URL im Blatt für die Active Directory-Einrichtung des Gateways abrufen.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	Ihre URL weist das folgende Format auf...
	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Beispiel: Wenn Ihre Ressourcengruppe "MyResource" heißt, und Sie das Gateway für die Azure Active Directory-Authentifizierung konfiguriert haben, lautet die URL folgendermaßen:

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	Bevor Sie die URL im Browser eingeben und die EINGABETASTE drücken, müssen Sie die Entwicklertools starten. Es sollte eine Meldung angezeigt werden, dass die Anmeldung abgeschlossen wurde. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	Rufen Sie Token und Wert für die Zumo-Authentifizierung über die Registerkarte für die Netzwerkkonfiguration ab (Chrome).
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Öffnen Sie die Postman-Erweiterung in Chrome. Geben Sie Gateway-Zustimmungs-URL, Zumo-Authentifzierungstoken (Header) und -Wert ein, und führen Sie eine POST-Anforderung aus. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	Prüfen Sie, ob die von der POST-Anforderung zurückgegebene Umleitungs-URL funktioniert. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	Wenn die Umleitungs-URL funktioniert, ist das Token gültig. Alle Aufrufe an den SaaS-Connector erfolgen mit dem soeben erstellten und geprüften Token. Dieses Token muss in alle Anforderungen von externen Quellen eingeschlossen werden, die über das Gateway erfolgen. Wenn Sie über einen Browser auf eine API zugreifen, wird das Token typischerweise in einem Cookie gespeichert und zusammen mit allen nachfolgenden Aufrufen an die API gesendet.

Der gleiche Ablauf gilt für alle SaaS-Connectors wie Salesforce, Facebook usw. 

## Aufrufen der Dropbox-API und Sicherstellen, dass der authentifizierte Zugriff funktioniert

1. Wechseln Sie zurück zur API-App-Liste, und wählen Sie den Dropbox-Connector. 

1. Notieren Sie die oben angezeigte URL.

2. Klicken Sie auf den Dropbox-Connector (wie in der Abbildung gezeigt), um alle unterstützten APIs anzuzeigen.

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	Im Blatt für die API-Definition können Sie sehen, welche Vorgänge in der API-App unterstützt werden. Sie können Swagger herunterladen, um es in Visual Studio zum Generieren von stark typisierten Clients zu nutzen. Darüber hinaus können Sie Swaddle herunterladen, das in Sienna und PowerApp Studio verwendet werden kann. 

2. Geben Sie in einem Browserfenster die URL ein, die Sie aus dem Portal kopiert haben, und hängen Sie eine beliebige der unterstützten APIs an, um in Ihrem Dropbox-Konto auf die Dateien oder andere Details zuzugreifen. 

	Format: `<URL>`/Operation

	Beispiel:

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	Sofern bereits die richtige Authentifizierung eingerichtet wurde, ist der obige Aufruf erfolgreich und führt zur Anzeige der Details des Ordners "photos" im Browser. 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<Copy the image of the browser>-->

## Nächste Schritte

Sie haben erfahren, wie Sie eine API-App schützen und das Gateway so konfigurieren, dass mit dem Tokenspeicher auf den SaaS-Connector zugegriffen wird. Weitere Informationen finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md). 

<!--HONumber=49-->