<properties
	pageTitle="Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy"
	description="Erläutert, wie Sie den Azure AD-Anwendungsproxy betriebsbereit machen."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/08/2015"
	ms.author="kgremban"/>



# Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy

Ansprüche unterstützende Apps führen eine Umleitung zum Sicherheitstokendienst (STS, Security Token Service) durch, der wiederum Anmeldeinformationen vom Benutzer im Austausch gegen ein Token anfordert, bevor der Benutzer zur Anwendung umgeleitet wird. Damit der Anwendungsproxy mit diesen Umleitungen arbeiten kann, müssen die folgenden Schritte ausgeführt werden.

> [AZURE.IMPORTANT]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).


## Voraussetzung

Stellen Sie vor dem Ausführen dieses Verfahrens sicher, dass der STS, zu dem die Ansprüche unterstützende App umgeleitet wird, außerhalb Ihres lokalen Netzwerks zur Verfügung steht.


### Konfiguration im klassischen Azure-Portal

1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](active-directory-application-proxy-publish.md).
2. Wählen Sie in der Liste der Anwendungen die Ansprüche unterstützende App aus, und klicken Sie auf **Konfigurieren**.
3. Wenn Sie **Passthrough** als **Präauthentifizierungsmethode** gewählt haben, stellen Sie sicher, dass Sie **HTTPS** als **Externe URL**-Schema wählen.
4. Wenn Sie **Azure Active Directory** als **Präauthentifizierungsmethode** gewählt haben, wählen Sie **Keine** als **Interne Authentifizierungsmethode**.


### ADFS-Konfiguration

1. Öffnen Sie die ADFS-Verwaltung.
2. Wechseln Sie zu **Vertrauensstellungen der vertrauenden Seite**, klicken Sie mit der rechten Maustaste auf die App, die Sie mit dem Anwendungsproxy veröffentlichen, und wählen Sie **Eigenschaften**.

![Vertrauensstellungen der vertrauenden Seite: mit der rechten Maustaste auf den App-Namen klicken – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. Wählen Sie auf der Registerkarte **Endpunkte** unter **Endpunkttyp** die Option **WS-Verbund**.
4. Geben Sie unter **Vertrauenswürdige URL** die URL ein, die Sie unter **Externe URL** in den Anwendungsproxy eingegeben haben, und klicken Sie auf **OK**.

![Hinzufügen eines Endpunkts – Wert "Vertrauenswürdige URL" festlegen – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)

<!---HONumber=AcomDC_1210_2015-->