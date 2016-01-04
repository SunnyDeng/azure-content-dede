<properties
	pageTitle="Hinzufügen der SharePoint Server-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen SharePoint Server-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajram"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/29/2015"
   ms.author="litran"/>

# Erstellen einer neuen SharePoint Server-API in der App Service-Umgebung Ihrer Organisation

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie auf der Taskleiste die Option **Durchsuchen** aus: ![][14]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben: ![][15]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus: ![Navigieren zu registrierten APIs][5]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzuzufügen: ![API hinzufügen][6]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.
7. Wählen Sie in **Source** die Option **Available APIs** aus, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **SharePoint Server** aus. 
8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus.
9. Geben Sie die *Client-ID* und den *geheimen App-Schlüssel* für die Azure Active Directory-App (AAD) für SharePoint Server sowie die *SharePoint-URL* und die *Ressourcen-ID* der AAD-Proxy-App ein. Führen Sie die im folgenden Abschnitt beschriebenen Schritte aus, um die Verbindung mit Ihrem lokalen SharePoint-Server zu konfigurieren.  

	> [AZURE.IMPORTANT]Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.
	
10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue SharePoint Server-API hinzugefügt.


## Konfigurieren der Verbindung mit einem lokalen SharePoint-Server

SharePoint-Server verwenden Active Directory für die Benutzerauthentifizierung. APIs in den über Apps verwalteten Umgebungen werden mithilfe von Azure Active Directory (AAD) authentifiziert. Sie müssen das AAD-Token des Benutzers austauschen und in das AD-Token konvertieren. Dieses AD-Token kann dann zum Herstellen der Verbindung mit dem lokalen Dienst verwendet werden.

Für diese Anforderung wird [Azure AD-Anwendungsproxy (AAD-Proxy)](../active-directory-application-proxy-publish.md) verwendet. Es handelt sich um einen Azure-Dienst zur allgemeinen Verfügbarkeit, der den Remotezugriff und SSO für lokale Webanwendungen sichert. Die Schritte zum Aktivieren von AAD-Proxy sind in MSDN sehr gut dokumentiert. Allgemein umfassen diese Schritte Folgendes:

1. [Aktivieren von Anwendungsproxy-Diensten](../active-directory-application-proxy-enable.md). Dies umfasst:  

	- Aktivieren von Anwendungsproxy in Azure AD
	- Installieren und Registrieren des Azure-Anwendungsproxyconnectors

2. [Veröffentlichen von Anwendungen mit Anwendungsproxy](../active-directory-application-proxy-publish.md). Dies umfasst:

	- Veröffentlichen einer Anwendungsproxy-App mit dem Assistenten. Notieren Sie nach der Erstellung der Proxy-App die externe URL der Intranet-SharePoint-Website.
	- Zuweisen von Benutzern und Gruppen zur Anwendung.
	- Geben Sie erweiterte Konfigurationseinstellungen ein, z. B. den SPN (Dienstprinzipalname), der vom Anwendungsproxyconnector verwendet wird, um das Kerberos-Token lokal abzurufen.

Nach der Erstellung der Proxy-App müssen Sie eine andere AAD-App erstellen, die die Delegierung an die Proxy-Anwendung durchführt. Dies ist notwendig, um das Zugriffstoken abzurufen und um Token zu aktualisieren, die für den Zustimmungsfluss erforderlich sind. Eine neue AAD-Anwendung können Sie anhand [dieser Anweisungen](../active-directory-integrating-applications.md) erstellen.

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie PowerApps Enterprise die SharePoint Server-API hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)


<!--References-->
[2]: https://msdn.microsoft.com/library/azure/dn768219.aspx
[3]: https://msdn.microsoft.com/library/azure/dn768214.aspx
[4]: https://msdn.microsoft.com/library/azure/dn768220.aspx
[5]: ./media/powerapps-create-api-dropbox/browse-to-registered-apis.PNG
[6]: ./media/powerapps-create-api-dropbox/add-api.PNG
[14]: ./media/powerapps-create-api-office365-outlook/browseall.png
[15]: ./media/powerapps-create-api-office365-outlook/allresources.png

<!---HONumber=AcomDC_1203_2015-->