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
	ms.date="02/09/2016"
	ms.author="kgremban"/>



# Arbeiten mit Ansprüche unterstützenden Apps im Anwendungsproxy

> [AZURE.IMPORTANT] Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).

Ansprüche unterstützende Apps führen eine Umleitung zum Sicherheitstokendienst (STS, Security Token Service) durch, der wiederum Anmeldeinformationen vom Benutzer im Austausch gegen ein Token anfordert, bevor der Benutzer zur Anwendung umgeleitet wird. Damit der Anwendungsproxy mit diesen Umleitungen arbeiten kann, müssen die folgenden Schritte ausgeführt werden.

## Voraussetzung

Stellen Sie vor dem Ausführen dieses Verfahrens sicher, dass der STS, zu dem die Ansprüche unterstützende App umgeleitet wird, außerhalb Ihres lokalen Netzwerks zur Verfügung steht.


### Konfiguration im klassischen Azure-Portal

1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](active-directory-application-proxy-publish.md).
2. Wählen Sie in der Liste der Anwendungen die Ansprüche unterstützende App aus, und klicken Sie auf **Konfigurieren**.
3. Wenn Sie **Passthrough** als **Präauthentifizierungsmethode** gewählt haben, stellen Sie sicher, dass Sie **HTTPS** als **Externe URL**-Schema wählen.
4. Wenn Sie **Azure Active Directory** als **Präauthentifizierungsmethode** gewählt haben, wählen Sie **Keine** als **Interne Authentifizierungsmethode**.


### ADFS-Konfiguration

1. Öffnen Sie die ADFS-Verwaltung.
2. Wechseln Sie zu **Vertrauensstellungen der vertrauenden Seite**, klicken Sie mit der rechten Maustaste auf die App, die Sie mit dem Anwendungsproxy veröffentlichen, und wählen Sie **Eigenschaften**. ![Vertrauensstellungen der vertrauenden Seite: mit der rechten Maustaste auf den App-Namen klicken – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyrelyingpartytrust.png)  
3. Wählen Sie auf der Registerkarte **Endpunkte** unter **Endpunkttyp** die Option **WS-Verbund**.
4. Geben Sie unter **Vertrauenswürdige URL** die URL ein, die Sie im Anwendungsproxy unter **Externe URL** eingegeben haben, und klicken Sie auf **OK**. ![Hinzufügen eines Endpunkts – Wert "Vertrauenswürdige URL" festlegen – Screenshot](./media/active-directory-application-proxy-claims-aware-apps/appproxyendpointtrustedurl.png)  

## Weitere Informationen
Der Anwendungsproxy bietet Ihnen noch viele weitere Möglichkeiten:

- [Veröffentlichen von Anwendungen mit dem Anwendungsproxy](active-directory-application-proxy-publish.md)
- [Veröffentlichen von Anwendungen mit Ihrem eigenen Domänennamen](active-directory-application-proxy-custom-domains.md)
- [Aktivieren der einmaligen Anmeldung](active-directory-application-proxy-sso-using-kcd.md)
- [Problembehandlung von Anwendungsproxys](active-directory-application-proxy-troubleshoot.md)

## Weitere Informationen zum Anwendungsproxy
- [Onlinehilfe anzeigen](active-directory-application-proxy-enable.md)
- [Blog zum Anwendungsproxy aufrufen](http://blogs.technet.com/b/applicationproxyblog/)
- [Sehen Sie sich unsere Videos auf Channel 9 an!](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## Zusätzliche Ressourcen

- [Artikelindex für die Anwendungsverwaltung in Azure Active Directory](active-directory-apps-index.md)
- [Bereitstellen eines sicheren Remotezugriffs auf lokale Anwendungen](active-directory-application-proxy-get-started.md)
- [Aktivieren von systemeigenen Client-Apps für die Interaktion mit Proxyanwendungen](active-directory-application-proxy-native-client.md)
- [Als Organisation für Azure registrieren](sign-up-organization.md)
- [Azure-Identität](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->