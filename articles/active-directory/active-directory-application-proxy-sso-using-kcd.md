<properties
	pageTitle="Einmaliges Anmelden für lokale IWA-Apps unter Verwendung von KCD mit Anwendungsproxy"
	description="Erläutert, wie Sie den Azure AD-Anwendungsproxy betriebsbereit machen."
	services="active-directory"
	documentationCenter=""
	authors="rkarlin"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/11/2015"
	ms.author="rkarlin"/>



# Einmaliges Anmelden für lokale IWA-Apps unter Verwendung von KCD mit Anwendungsproxy


Sie können für Anwendungen, die die integrierte Windows-Authentifizierung (IWA) verwenden, einmaliges Anmelden (SSO) aktivieren, indem Sie einem Anwendungsproxy-Connector in Active Directory die Berechtigung erteilen, die Identität von Benutzern anzunehmen und Token in deren Namen zu senden und zu empfangen.

> [AZURE.IMPORTANT]Das Feature "Anwendungsproxy" ist nur verfügbar, wenn Sie Azure Active Directory auf die Premium oder Basic Edition aktualisiert haben. Weitere Informationen finden Sie unter [Azure Active Directory-Editionen](active-directory-editions.md).


## Netzwerkdiagramm

![Flussdiagramm für Microsoft AAD-Authentifizierung](./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png)

Dieses Diagramm erläutert die Vorgänge, die bei einem Zugriff eines Benutzers auf eine lokale Anwendung, die IWA verwendet, ablaufen. Im Allgemeinen geschieht Folgendes:

1. Der Benutzer gibt die URL ein, um über den Anwendungsproxy auf die lokale Anwendung zuzugreifen.
2. Der Anwendungsproxy leitet die Anforderung zur Vorauthentifizierung an Azure AD-Authentifizierungsdienste weiter. Zu diesem Zeitpunkt wendet Azure AD alle gültigen Authentifizierungs- und Autorisierungsrichtlinien an, wie z. B. mehrstufige Authentifizierung. Nachdem der Benutzer überprüft wurde, erstellt Azure AD ein Token und sendet es an den Benutzer.
3. Der Benutzer übergibt das Token an den Anwendungsproxy.
4. Der Anwendungsproxy überprüft das Token und ruft den Benutzerprinzipalnamen (UPN) daraus ab. Dann sendet er die Anforderung, den UPN und den Dienstprinzipalnamen (SPN) über einen zweifach authentifizierten, sicheren Kanal an den Connector.
5. Der Connector führt eine KCD-Aushandlung mit dem lokalen AD aus und nimmt dabei die Identität des Benutzers an, um ein Kerberos-Token für die Anwendung zu erhalten.
6. Active Directory sendet das Kerberos-Token für die Anwendung an den Connector.
7. Der Connector sendet die ursprüngliche Anforderung an den Anwendungsserver und verwendet dabei das von AD empfangene Kerberos-Token.
8. Die Anwendung sendet die Antwort an den Connector, die dann an den Anwendungsproxydienst und schließlich an den Benutzer zurückgegeben wird.

### Voraussetzungen

1. Stellen Sie sicher, dass Ihre Apps, wie z. B. Ihre SharePoint-Web-Apps, zur Verwendung der integrierten Windows-Authentifizierung konfiguriert sind. Weitere Informationen finden Sie unter [Aktivieren der Unterstützung für die Kerberos-Authentifizierung](https://technet.microsoft.com/library/dd759186.aspx). Weitere Informationen zu SharePoint finden Sie unter [Planen der Kerberos-Authentifizierung in SharePoint 2013](https://technet.microsoft.com/library/ee806870.aspx).
2. Erstellen Sie Dienstprinzipalnamen für Ihre Anwendungen.
3. Stellen Sie sicher, dass der Server, auf dem der Connector ausgeführt wird, und der Server, auf dem die App ausgeführt wird, die Sie veröffentlichen, in die Domäne eingebunden und Teil der gleichen Domäne sind. Weitere Informationen zum Domänenbeitritt finden Sie unter [Hinzufügen eines Computers zu einer Domäne](https://technet.microsoft.com/library/dd807102.aspx).


## Active Directory-Konfiguration

Die Active Directory-Konfiguration variiert in Abhängigkeit davon, ob Ihr Anwendungsproxy-Connector und der veröffentlichte Server sich in derselben Domäne befinden oder nicht.

### Connector und veröffentlichter Server in der gleichen Domäne

Wechseln Sie in Active Directory zu **Extras** > **Benutzer und Computer**. Wählen Sie den Server aus, der den Connector ausführt. Klicken Sie mit der rechten Maustaste, und wählen Sie **Eigenschaften** > **Delegierung** aus. Wählen Sie **Computer bei Delegierungen angegebener Dienste vertrauen** aus, und fügen Sie unter **Dienste, für die dieses Konto delegierte Anmeldeinformationen verwenden kann** den Wert für die Dienstprinzipalnamen-Identität (SPN) des Anwendungsservers hinzu. Auf diese Weise kann der Anwendungsproxy-Connector die Identität von Benutzern in AD für die Anwendungen annehmen, die in der Liste definiert sind.

![Screenshot des Connector-SVR-Eigenschaftenfensters](./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg)

### Connector und veröffentlichter Server in unterschiedlichen Domänen

1. Eine Liste der Voraussetzungen für das domänenübergreifende Arbeiten mit KCD finden Sie unter [Eingeschränkte Kerberos-Delegierung über Domänengrenzen hinweg](https://technet.microsoft.com/library/hh831477.aspx).
2. Verwenden Sie unter Windows 2012 R2 die Eigenschaft `principalsallowedtodelegateto` auf dem Connector-Server, um für den Anwendungsproxy das Delegieren für den Connector-Server zu aktivieren, wobei `sharepointserviceaccount` der veröffentlichte Server und `connectormachineaccount` der delegierende Server ist.

		$connector= Get-ADComputer -Identity connectormachineaccount -server dc.connectordomain.com

		Set-ADComputer -Identity sharepointserviceaccount -PrincipalsAllowedToDelegateToAccount $connector

		Get-ADComputer sharepointserviceaccount -Properties PrincipalsAllowedToDelegateToAccount


>[AZURE.NOTE]`sharepointserviceaccount` kann das SPS-Computerkonto oder ein Dienstkonto sein, unter dem der SPS-App-Pool ausgeführt wird.


## Konfiguration des Azure-Portals

1. Veröffentlichen Sie Ihre Anwendung entsprechend den Anweisungen unter [Veröffentlichen von Anwendungen mit einem Anwendungsproxy](active-directory-application-proxy-publish.md). Stellen Sie sicher, dass **Azure Active Directory** als **Vorauthentifizierungsmethode** ausgewählt ist.
2. Wenn Ihre Anwendung in der Liste der Anwendungen angezeigt wird, wählen Sie sie aus und klicken auf **Konfigurieren**.
3. Legen Sie unter **Eigenschaften** die Option **Interne Authentifizierungsmethode** auf **Integrierte Windows-Authentifizierung** fest.

![Erweiterte Anwendungskonfiguration](./media/active-directory-application-proxy-sso-using-kcd/cwap_auth2.png)

4. Geben Sie den **SPN der internen Anwendung** des Anwendungsservers ein. In diesem Beispiel ist der SPN für die veröffentlichte Anwendung http/lob.contoso.com.

>[AZURE.IMPORTANT]Die UPNs in Azure Active Directory müssen mit den UPNs in Ihrem lokalen Active Directory identisch sein, damit die Vorauthentifizierung funktioniert. Stellen Sie sicher, dass Ihr Azure Active Directory mit Ihrem lokalen Active Directory synchronisiert ist.

| | |
| --- | --- |
| Interne Authentifizierungsmethode | Bei Verwendung von Azure AD für die Vorauthentifizierung können Sie eine interne Authentifizierungsmethode festlegen, die Ihren Benutzern ermöglicht, vom einmaligen Anmelden (SSO) bei dieser Anwendung zu profitieren. <br><br> Wählen Sie **Integrierte Windows-Authentifizierung** (IWA) aus, wenn Ihre Anwendung IWA verwendet, und Sie die eingeschränkte Kerberos-Delegierung (KCD) konfigurieren können, um die einmalige Anmeldung für diese Anwendung zu aktivieren. Anwendungen, die IWA verwenden, müssen mithilfe von KCD konfiguriert werden. Ansonsten kann der Anwendungsproxy diese Anwendungen nicht veröffentlichen. <br><br> Wählen Sie **Keine** aus, wenn Ihre Anwendung IWA nicht verwendet. |
| Interner Anwendungs-SPN | Dies ist der Dienstprinzipalname (SPN) der internen Anwendung wie im lokalen Azure AD konfiguriert. Der SPN wird vom Anwendungsproxy-Connector verwendet, um Kerberos-Token für die Anwendung mithilfe der eingeschränkten Kerberos-Delegierung (KCD) abzurufen. |

<!--Image references-->
[1]: ./media/active-directory-application-proxy-sso-using-kcd/AuthDiagram.png
[2]: ./media/active-directory-application-proxy-sso-using-kcd/Properties.jpg

<!---HONumber=August15_HO9-->