<properties
	pageTitle="Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy"
	description="Erläutert, wie Sie den Azure AD-Anwendungsproxy betriebsbereit machen."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="msStevenPo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/09/2015"
	ms.author="rkarlin"/>



# Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy


Ansprüche unterstützende Apps führen eine Umleitung zu dem STS durch, der wiederum Anmeldeinformationen vom Benutzer im Austausch gegen ein Token anfordert, bevor der Benutzer zur Anwendung umgeleitet wird. Um dem Anwendungsproxy die Arbeit mit diesen Umleitungen zu ermöglichen, müssen die folgenden Schritte zur Aktivierung des Anwendungsproxys für die Arbeit mit Ansprüche unterstützenden Apps ausgeführt werden.

> [AZURE.IMPORTANT]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).


## Voraussetzung

Stellen Sie vor dem Ausführen dieses Verfahrens sicher, dass der STS, zu dem die Ansprüche unterstützende App umgeleitet wird, außerhalb Ihres lokalen Netzwerks zur Verfügung steht.

### Konfiguration des Azure-Portals

1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](active-directory-application-proxy-publish.md).
2. Wählen Sie in der Liste der Anwendungen die Ansprüche unterstützende App aus, und klicken Sie auf **Konfigurieren**.
3. Wenn Sie **Passthrough** als **Präauthentifizierungsmethode** gewählt haben, stellen Sie sicher, dass Sie **HTTPS** als **Externe URL**-Schema wählen.
4. Stellen Sie bei Verwendung von Azure Active Directory als **Präauthentifizierungsmethode** sicher, dass Sie **Keine** als **Interne Authentifizierungsmethode** wählen.

### ADFS-Konfiguration

1. Öffnen Sie die **ADFS-Verwaltung**.
2. Wechseln Sie zu **Vertrauensstellungen der vertrauenden Seite**, und klicken Sie mit der rechten Maustaste auf die App, die Sie mit dem Anwendungsproxy veröffentlichen, und wählen Sie **Eigenschaften**.

![Vertrauensstellungen der vertrauenden Seite: mit der rechten Maustaste auf den App-Namen klicken – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)

3. Wählen Sie auf der Registerkarte **Endpunkte** unter **Endpunkttyp** die Option **WS-Verbund**.
4. Geben Sie unter **Vertrauenswürdige URL** die URL ein, die Sie unter **Externe URL** in den Anwendungsproxy eingegeben haben, und klicken Sie auf **OK**.

![Hinzufügen eines Endpunkts – Wert "Vertrauenswürdige URL" festlegen – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)

<!---HONumber=Sept15_HO3-->