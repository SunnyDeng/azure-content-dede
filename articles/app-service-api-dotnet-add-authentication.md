<properties 
	pageTitle="Schützen einer Azure API-App" 
	description="Erfahren Sie, wie Sie eine Azure API-App mithilfe von Visual Studio schützen." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Schützen einer API-App: Hinzufügen der Authentifizierung über Azure Active Directory oder ein soziales Netzwerk

## Übersicht

Im Lernprogramm [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) haben Sie eine API-App mit der Zugriffsebene **Für jeden verfügbar** bereitgestellt. Dieses Lernprogramm zeigt, wie Sie eine API-App schützen, sodass nur authentifizierte Benutzer darauf zugreifen können.

Sie führen die folgenden Schritte aus:

- Aufrufen der API-App, um deren Funktionalität sicherzustellen
- Anwenden von Authentifizierungsregeln auf die API-App
- Erneutes Aufrufen der API-App, um sicherzustellen, dass nicht authentifizierte Anforderungen abgelehnt werden
- Anmelden beim konfigurierten Anbieter
- Erneutes Aufrufen der API-App, um sicherzustellen, dass der authentifizierte Zugriff funktioniert

## Voraussetzungen

In diesem Lernprogramm wird die API-App verwendet, die Sie in [Erstellen einer API-App](app-service-dotnet-create-api-app.md) erstellt und in [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) bereitgestellt haben.

## Verwenden des Browsers zum Aufrufen der API-App 

Die einfachste Möglichkeit zu prüfen, ob Ihre API-App öffentlich zugänglich ist, besteht darin, sie über einen Browser aufzurufen.

1. Wechseln Sie in Ihrem Browser zum [Azure-Portal].

3. Klicken Sie auf der Startseite auf **Durchsuchen > API-Apps**, und klicken Sie dann auf den Namen der API-App, die Sie schützen möchten.

	![Browse](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Select API app](./media/app-service-api-dotnet-add-authentication/select.png)

3. Klicken Sie im Blatt **API-App** auf **URL**, um ein Browserfenster zu öffnen, in dem Ihre API-App aufgerufen wird.

	![API App blade](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. Hängen Sie in der Adressleiste des Browsers den Pfad `/api/contacts/get/` an die URL.

	Angenommen, die API-App-URL lautet folgendermaßen:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	Dann lautet die vollständige URL:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	Die verschiedenen Browser verarbeiten API-Aufrufe unterschiedlich. Nachstehend wird ein erfolgreicher Aufruf über einen Chrome-Browser gezeigt.

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. Speichern Sie die verwendete URL; Sie werden sie später in diesem Lernprogramm erneut verwenden.

## Schützen der API-App

Als Sie Ihre API-App bereitgestellt haben, ist die Bereitstellung als Bestandteil einer Ressourcengruppe erfolgt. Sie können Web-Apps und andere API-Apps zur selben Ressourcengruppe hinzufügen, und jede API-App innerhalb der Ressourcengruppe kann eine von drei Zugriffseinstellungen verwenden:
<!--todo: diagram showing different accessibility settings-->

- **Öffentlich (anonym)** - Beliebige Benutzer können die API-App von außerhalb der Ressourcengruppe und ohne Anmeldung aufrufen.
- **Öffentlich (authentifiziert)** - Nur authentifizierte Benutzer können die API-App von außerhalb der Ressourcengruppe aufrufen.
- **Intern** - Nur andere API-Apps oder Web-Apps in derselben Ressourcengruppe dürfen die API-App aufrufen.

Als Visual Studio die Ressourcengruppe für Sie erstellt hat, wurde auch ein *Gateway* erstellt.  Ein Gateway ist eine spezielle Web-App, die alle Anforderungen verarbeitet, die an API-Apps in der Ressourcengruppe gerichtet sind.

Wenn Sie im [Azure-Portal] zum Blatt für die Ressourcengruppe wechseln, können Sie Ihre API-App und das Gateway im Diagramm anzeigen.

![Resource group diagram](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

Um Ihre API-App so zu konfigurieren, dass nur authentifizierte Anforderungen akzeptiert werden, legen Sie den Zugriff **Öffentlich (authentifiziert)** fest. Anschließend konfigurieren Sie das Gateway für eine Authentifizierung über einen Anbieter wie Azure Active Directory, Google oder Facebook.

1. Wechseln Sie zurück zum Blatt **API-App** für die API-App, die Sie schützen möchten.

2. Klicken Sie im Blatt für die **API-App** auf **Einstellungen** und anschließend auf **Anwendungseinstellungen**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettings.png)

	![Click Application settings](./media/app-service-api-dotnet-add-authentication/clickbasicsettings.png)

3. Ändern Sie im Blatt **Anwendungseinstellungen** die **Zugriffsebene** in **Öffentlich (authentifiziert)**, und klicken Sie dann auf **Speichern**.

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/setpublicauth.png)

	Damit haben Sie die API-App vor einem nicht authentifizierten Zugriff geschützt. Als Nächstes müssen Sie das Gateway konfigurieren, um den Authentifizierungsanbieter anzugeben.

4. Wechseln Sie zurück zum Blatt **API-App**, und klicken Sie auf den Link zum Gateway.

	![Click gateway](./media/app-service-api-dotnet-add-authentication/gateway.png)

7. Klicken Sie im Blatt **Gateway** auf **Einstellungen** und anschließend auf **Identität**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettingsingateway.png)

	![Click Identity](./media/app-service-api-dotnet-add-authentication/clickidentity.png)

	Vom Blatt **Identität** aus können Sie zu unterschiedlichen Blättern zum Konfigurieren der Authentifizierung mithilfe von Azure Active Directory und verschiedenen anderen Anbietern navigieren.

	![Identity blade](./media/app-service-api-dotnet-add-authentication/identityblade.png)
  
3. Wählen Sie den gewünschten Identitätsanbieter aus, und führen Sie die Schritte zum Konfigurieren Ihrer API-App für diesen Anbieter aus, die im entsprechenden Artikel aufgeführt sind. Diese Artikel wurden für mobile Apps geschrieben, die Vorgehensweise für API-Apps ist jedoch identisch. Für einige der Verfahren ist das [alte Portal] erforderlich. 

 - [Microsoft-Konto](app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Facebook-Anmeldung](app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Twitter-Anmeldung](app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Google-Anmeldung](app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Die nachfolgenden Screenshots zeigen beispielhaft, welche Seiten im [alten Portal] und welche Blätter im [Azure-Portal] angezeigt werden sollten, nachdem Sie die Azure Active Directory-Authentifizierung eingerichtet haben.

Im Azure-Portal umfasst das Blatt **Azure Active Directory** eine **Client-ID** aus der Anwendung, die Sie auf der Registerkarte "Azure Active Directory" im alten Portal erstellt haben, und die Einstellung **Zulässige Mandanten** enthält Ihren Azure Active Directory-Mandanten (beispielsweise "contoso.onmicrosoft.com").

![Azure Active Directory blade](./media/app-service-api-dotnet-add-authentication/tdinaadblade.png)

Im alten Portal enthält die Registerkarte **Konfiguration** der Anwendung, die Sie auf der Registerkarte **Azure Active Directory** erstellt haben, die Einstellungen **Anmelde-URL**, **App-ID-URI** und **Antwort-URL** aus dem Blatt **Azure Active Directory** im Azure-Portal.

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal1.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal2.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal3.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal4.png)

(Die Antwort-URL im Screenshot zeigt zweimal dieselbe URL, einmal mit `http:` und einmal mit `https:`.)

## Sicherstellen, dass die Authentifizierung funktioniert 

1. Öffnen Sie ein Browserfenster, und geben Sie in der Adressleiste die URL ein, mit der (wie zuvor) die Get-Methode Ihrer API-App aufgerufen wird.

	Dieses Mal führt der Zugriffsversuch auf die API-App zu einer Fehlermeldung.

	![Chrome Get response fail](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. Wechseln Sie im Browser zur Anmelde-URL: 

    	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Beispiel: Wenn Ihre Ressourcengruppe "myfirstrg" heißt, und Sie das Gateway für die Azure Active Directory-Authentifizierung konfiguriert haben, lautet die URL folgendermaßen:

    	http://myfirstrggateway.azurewebsites.net/login/aad

	Beachten Sie, dass im Gegensatz zur vorherigen URL der Name der API-App nicht enthalten ist:  Das Gateway authentifiziert Sie, nicht die API-App.  Das Gateway übernimmt die Authentifizierung für alle API-Apps in der Ressourcengruppe.

	(Wenn Sie Probleme bei der Anmeldung haben, öffnen Sie ein Fenster im privaten oder im Inkognito-Modus.)

3. Geben Sie Ihre Anmeldeinformationen ein, wenn der Browser eine Anmeldeseite anzeigt. 
 
	Wenn Sie die Azure Active Directory-Anmeldung konfiguriert haben, verwenden Sie einen der auf der Registerkarte **Benutzer** aufgeführten Benutzer für die Anwendung, die Sie auf der Registerkarte "Azure Active Directory" des [alten Portals] erstellt haben, z. B. "admin@contoso.onmicrosoft.com".

	![AAD users](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Login page](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. Wenn die Meldung "Anmeldung abgeschlossen" angezeigt wird, geben Sie erneut die URL zur Get-Methode Ihrer API-App ein.

	Nun, da Sie authentifiziert wurden, ist der Aufruf erfolgreich. Das Gateway erkennt, dass Sie ein authentifizierter Benutzer sind, und übergibt Ihre Anforderung an die API-App.

	![Login completed](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo:replace with image showing fictional names-->

## Verwenden von Postman zum Senden einer Post-Anforderung

Wenn Sie sich am Gateway anmelden, gibt das Gateway ein Authentifizierungstoken zurück.  Dieses Token muss in alle Anforderungen von externen Quellen eingeschlossen werden, die über das Gateway erfolgen. Wenn Sie über einen Browser auf eine API zugreifen, wird das Token typischerweise in einem Cookie gespeichert und zusammen mit allen nachfolgenden Aufrufen an die API gesendet.

Damit Sie sehen, was im Hintergrund geschieht, verwenden Sie in diesem Abschnitt ein Browsertool zum Erstellen und Senden einer Post-Anforderung. Anschließend rufen Sie das Autorisierungstoken aus dem Cookie ab und schließen es in einen HTTP-Header ein.

Diese Anweisungen zeigen, wie Sie das Postman-Tool im Chrome-Browser verwenden, aber Sie können dieselbe Aufgabe ebenso mit einem beliebigen REST-Clienttool und den Entwicklertools für den Browser durchführen.

1. Durchlaufen Sie in einem Chrome-Browserfenster die im vorherigen Abschnitt gezeigten Schritte zur Authentifizierung, und öffnen Sie dann die Entwicklertools (F12).

	![Go to Resources tab](./media/app-service-api-dotnet-add-authentication/resources.png)

3. Suchen Sie auf der Registerkarte **Resources** der Chrome-Entwicklertools nach dem Cookie für Ihr Gateway, und klicken Sie drei Mal auf den Wert für das **x-zumo-auth**-Cookie, um alle Informationen auszuwählen.

	**Hinweis:** Stellen Sie sicher, alle Informationen zum Cookiewert abzurufen. Wenn Sie einen Doppelklick ausführen, wird nur der erste Teil abgerufen.

5. Klicken Sie mit der rechten Maustaste auf das Feld **Value** für das **x-zumo-auth**-Cookie, und klicken Sie dann auf **Copy**.

	![Copy auth token](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Sofern noch nicht geschehen, installieren Sie die Postman-Erweiterung in Ihrem Chrome-Browser.

6. Öffnen Sie die Postman-Erweiterung.

7. Geben Sie im Feld für die Anforderungs-URL die URL zur Get-Methode Ihrer API-App ein, die Sie bereits zuvor verwendet haben. Lassen Sie jedoch den Wert "get/" am Ende weg.
 
		http://[apiappurl]/api/contacts
    
8. Klicken Sie auf **Headers**, und fügen Sie einen *x-zumo-auth*-Header hinzu. Fügen Sie den Tokenwert aus der Zwischenablage in das Feld **Value** ein.

9. Fügen Sie einen *Content-Type*-Header mit dem Wert *application/json* hinzu.

10. Klicken Sie auf **form-data**, und fügen Sie einen *contact*-Schlüssel mit dem folgenden Wert hinzu:

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Klicken Sie auf "Send".

	Die API-App gibt eine *201 Created*-Antwort zurück.

	![Add headers and body](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. Um sicherzustellen, dass diese Anforderung ohne das Authentifizierungstoken nicht funktionieren würde, löschen Sie den Authentifizierungsheader, und klicken Sie erneut auf "Send".

	Sie erhalten eine *403 Forbidden*-Antwort.

	![403 Forbidden response](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## Nächste Schritte

Sie haben erfahren, wie Sie eine Azure API-App schützen, indem Sie sie für eine Authentifizierung über Azure Active Directory oder ein soziales Netzwerk konfigurieren. Weitere Informationen finden Sie unter [Was sind API-Apps?](app-service-api-apps-why-best-platform.md). 

[Altes Portal]: https://manage.windowsazure.com/
[Azure-Portal]: https://portal.azure.com/


<!--HONumber=49-->