<properties
	pageTitle="Einführung in den Zugriffsbereich | Microsoft Azure"
	description="Erfahren Sie, wie Sie die verschiedenen Varianten des Zugriffsbereichs (Webbrowser, Android-App, iPhone- und iPad-App) verwenden, um auf die Ihnen zugewiesenen SaaS-Apps zuzugreifen."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="markusvi"/>


# Einführung in den Zugriffsbereich


Der Zugriffsbereich ist ein webbasiertes Portal, das einem Endbenutzer mit einem Organisationskonto in Azure Active Directory erlaubt, cloudbasierte Anwendungen anzuzeigen und zu starten, für die der Azure AD-Administrator Zugriff gewährt hat. Als Endbenutzer mit Azure Active Directory-Editionen können Sie über den Zugriffsbereich auch Self-Service-Gruppenverwaltungsfunktionen verwenden. <br> Der Zugriffsbereich ist unabhängig vom Azure-Verwaltungsportal und setzt nicht voraus, dass Benutzer über ein Azure-Abonnement verfügen.


![Anpassung des Zugriffsbereichs][1]


Der Zugriffsbereich ermöglicht Benutzern, einige ihrer Profileinstellungen zu bearbeiten. Folgende Aufgaben können ausgeführt werden:

- Ändern des Kennworts, das Ihrem Organisationskonto zugeordnet ist.

- Bearbeiten der Einstellungen zum Zurücksetzen des Kennworts.

- Bearbeiten der Kontakt- und Voreinstellungen, die sich auf die mehrstufige Authentifizierung beziehen (für Konten, für die dies vom Administrator verlangt wird).

- Anzeigen von Kontodetails wie z. B. Benutzer-ID, alternative E-Mail-Adresse, Mobil- und geschäftliche Telefonnummern.

- Anzeigen und Starten der cloudbasierten Anwendungen, für die Ihnen der Azure AD-Administrator Zugriff gewährt hat. Informationen über den Zugriffsbereich aus Sicht der Endbenutzer finden Sie unter [Verwenden des Zugriffsbereichs](https://msdn.microsoft.com/library/azure/dn756411.aspx).

- Selbstständiges Verwalten von Gruppen. Sie können Sicherheitsgruppen erstellen und verwalten und die Mitgliedschaft in Sicherheitsgruppen in Azure AD anfordern. Weitere Informationen finden Sie unter [Self-Service-Gruppenverwaltung für Benutzer in Azure AD](active-directory-accessmanagement-self-service-group-management.md) und [Verwalten Ihrer Gruppen](active-directory-manage-groups.md).




## Zugreifen auf den Zugriffsbereich


Benutzer greifen auf den Zugriffsbereich zu, indem sie in einem Webbrowser die folgende URL verwenden: <br> ****http://myapps.microsoft.com**

Wenn Sie benutzerdefiniertes Branding für Ihre Anmeldeseite konfiguriert haben, können Sie dieses Branding standardmäßig laden, indem Sie die Domäne Ihrer Organisation am Ende der URL anfügen <br> ****http://myapps.microsoft.com/contosobuild.com**

In diesem Fall kann jeder aktive oder überprüfte Domänenname verwendet werden, der auf der Registerkarte "Domänen" Ihres Verzeichnisses im Azure-Verwaltungsportal konfiguriert wurde, wie in folgendem Screenshot gezeigt.


![Wingtip Toys][2]


Diese URL muss allen Benutzern mitgeteilt werden, die sich bei in Azure AD integrierten Anwendungen anmelden.
 




## Authentifizierung

Um auf den Zugriffsbereich zugreifen zu können, muss ein Benutzer unter Verwendung eines Organisationskontos in Azure AD authentifiziert sein. <br> Ein Benutzer kann direkt bei Azure AD authentifiziert sein. <br> Wenn eine Organisation mithilfe von ADFS oder anderen Technologien einen Verbund konfiguriert hat, können Benutzer alternativ dazu auch über Windows Server Active Directory authentifiziert werden.

Wenn ein Benutzer über ein Abonnement für Azure oder Office 365 verfügt und das Azure-Verwaltungsportal oder eine Office 365-Anwendung verwendet hat, wird dem Benutzer die Liste der Anwendungen angezeigt, ohne dass eine erneute Anmeldung erforderlich ist. Benutzer, die nicht authentifiziert sind, werden aufgefordert, sich mit dem Benutzernamen und Kennwort für ihr Konto in Azure AD anzumelden. Wenn die Organisation einen Verbund konfiguriert hat, reicht die Eingabe des Benutzernamens aus.

Nach der Authentifizierung können Benutzer mit den Anwendungen interagieren, die durch den Administrator in das Verzeichnis integriert wurden. Informationen zum Integrieren von Anwendungen in Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](active-directory-appssoaccess-whatis.md).
 




## Webbrowseranforderungen

Die Mindestanforderung für den Zugriffsbereich besteht in einem Browser mit aktivierter Unterstützung für JavaScript und CSS. Damit der Benutzer bei Anwendungen mithilfe von kennwortbasiertem SSO (Single Sign-On, einmaliges Anmelden) angemeldet werden kann, muss die Erweiterung für den Zugriffsbereich im Browser des Benutzers installiert sein. Diese Zugriffsbereichserweiterung wird automatisch heruntergeladen, wenn ein Benutzer eine Anwendung auswählt, die für kennwortbasiertes SSO konfiguriert ist.

Zurzeit ist die Zugriffsbereichserweiterung für die Browser Internet Explorer 8 oder höher, Chrome und Firefox verfügbar.





## Unterstützung für mobile Apps

Damit Benutzer sich bei kennwortbasierten SSO-Anwendungen auf iOS- und Android-Geräten anmelden können, sollten sie die mobile App "Meine Apps" installieren, die vom Azure Active Directory-Team veröffentlicht wird.





### "Meine Apps" für Android


"Meine Apps" für Android wird auf jedem Android-Gerät unterstützt, auf dem Android, Version 4.1 oder höher, ausgeführt wird. Die App ist zurzeit im [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps) verfügbar.


![Meine Apps][3]






### "Meine Apps" für iPhone und iPad


"Meine Apps" für iOS wird auf jedem iPhone oder iPad unterstützt, auf dem iOS, Version 7 oder höher, ausgeführt wird. Die App ist zurzeit im Apple App Store verfügbar.


![Anwendungsprofil][4]




> [AZURE.NOTE]Bei Anwendungen, die den Verbund mit Azure AD unterstützen (einschließlich Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 und mehr als 70 weiteren), kann die Anmeldung mit beinahe jedem Webbrowser auf beliebigen Geräten erfolgen, ohne dass ein Plug-In oder eine mobile App erforderlich ist. Für den restlichen Zugriffsbereich unter [https://myapps.microsoft.com](https://myapps.microsoft.com/) ist es ebenfalls nicht erforderlich, die mobile App "Meine Apps" auf einem mobilen Gerät zu verwenden.
 


 

## Tipps zum Testen der Benutzerfunktionen

Wenn Sie Azure-Administrator und mit einem Konto im Verzeichnis am Azure-Verwaltungsportal angemeldet sind, werden Sie automatisch mit dem aktuellen Administratorkonto beim Zugriffsbereich angemeldet. In diesem Fall werden Ihnen alle Anwendungen angezeigt, die diesem Konto zugewiesen wurden.

**So testen Sie ein anderes Benutzerkonto:**

1. Klicken Sie in der oberen rechten Ecke des Azure-Portals oder des Zugriffsbereichs auf das Benutzermenü, und wählen Sie **Abmelden** aus. Sie werden von Azure AD abgemeldet.

2. Wechseln Sie zum Zugriffsbereich: ****http://myapps.microsoft.com**.

3. Geben Sie auf der Anmeldeseite den Benutzernamen und das Kennwort für das Konto in Ihrem Verzeichnis ein, das Sie testen möchten.
 
## Starten von Anwendungen

Es gibt mehrere Arten von Anwendungen, die im Zugriffsbereich angezeigt werden können.
 
### Office 365-Anwendungen

Wenn eine Organisation Office 365-Anwendungen verwendet und der Benutzer für diese lizenziert ist, werden die Office 365-Anwendungen im Zugriffsbereich des Benutzers angezeigt.

Wenn ein Benutzer auf eine Anwendungskachel für eine Office 365-Anwendung klickt, wird er an diese Anwendung weitergeleitet und automatisch angemeldet.

### Anwendungen von Microsoft und Drittanbietern, die mit verbundbasiertem SSO konfiguriert sind

Dabei handelt es sich um Anwendungen, die der Administrator im Abschnitt "Active Directory" des Azure-Verwaltungsportals hinzugefügt und für die er den SSO-Modus auf *Azure AD – einmaliges Anmelden* festgelegt hat. Ein Benutzer sieht diese Anwendungen nur, wenn ihm durch den Administrator ausdrücklich Zugriff auf die Anwendung erteilt wurde.

Wenn ein Benutzer auf eine Anwendungskachel für eine dieser Anwendungen klickt, wird er an die betreffende Anwendung weitergeleitet und automatisch angemeldet.

### Kennwortbasiertes SSO ohne Identitätsbereitstellung

Dabei handelt es sich um Anwendungen, die der Administrator im Abschnitt "Active Directory" des Azure-Verwaltungsportals hinzugefügt und für die er den SSO-Modus auf *Kennwortbasiertes einmaliges Anmelden* festgelegt hat. <br> Alle Benutzer im Verzeichnis sehen alle Anwendungen, die in diesem Modus konfiguriert wurden.

Wenn ein Benutzer erstmals auf eine Anwendungskachel für eine dieser Anwendungen klickt, wird er aufgefordert, das Plug-In für kennwortbasiertes SSO für Internet Explorer oder Chrome zu installieren. Dies erfordert ggf. einen Neustart des Webbrowsers. Wenn der Benutzer in den Zugriffsbereich zurückkehrt und nochmals auf die Anwendungskachel klickt, wird er zur Eingabe eines Benutzernamens und Kennworts für die Anwendung aufgefordert. Nachdem der Benutzername und das Kennwort eingegeben wurden, werden diese Anmeldeinformationen sicher in Azure AD gespeichert und mit dem entsprechenden Konto in Azure AD verknüpft. Der Zugriffsbereich automatisiert die Anmeldung des Benutzers bei der Anwendung mit diesen Anmeldeinformationen.

Wenn der Benutzer das nächste Mal auf die Anwendungskachel klickt, wird er automatisch bei der Anwendung angemeldet. Er muss nicht nochmals Anmeldeinformationen eingeben oder das Plug-In für kennwortbasiertes SSO erneut installieren.

Wenn sich die Anmeldeinformationen in der Drittanbieteranwendung geändert haben, muss der Benutzer seine in Azure AD gespeicherten Anmeldeinformationen aktualisieren. Zu diesem Zweck wählt der Benutzer das Symbol in der unteren rechten Ecke der Anwendungskachel und dann "Anmeldeinformationen aktualisieren" aus, um den Benutzernamen und das Kennwort für die betreffende Anwendung erneut einzugeben.

### Kennwortbasiertes SSO mit Identitätsbereitstellung

Dabei handelt es sich um Anwendungen, die der Administrator im Abschnitt "Active Directory" des Azure-Verwaltungsportals hinzugefügt und für die er den SSO-Modus auf *Kennwortbasiertes einmaliges Anmelden* festgelegt sowie die Identitätsbereitstellung eingerichtet hat.

Wenn ein Benutzer erstmals auf eine Anwendungskachel für eine dieser Anwendungen klickt, wird er aufgefordert, das Plug-In für kennwortbasiertes SSO für Internet Explorer oder Chrome zu installieren. Dies erfordert ggf. einen Neustart des Webbrowsers. Wenn der Benutzer in den Zugriffsbereich zurückkehrt und nochmals auf die Anwendungskachel klickt, wird automatisch bei der Anwendung angemeldet.

Bei einigen Anwendungen kann es erforderlich sein, dass ein Benutzer sein Kennwort bei der ersten Anmeldung ändert. Wenn sich die Anmeldeinformationen in der Drittanbieteranwendung geändert haben, muss der Benutzer seine in Azure AD gespeicherten Anmeldeinformationen aktualisieren. Zu diesem Zweck wählt der Benutzer das Symbol in der unteren rechten Ecke der Anwendungskachel und dann "Anmeldeinformationen aktualisieren" aus, um den Benutzernamen und das Kennwort für die betreffende Anwendung erneut einzugeben.

### Anwendung mit vorhandenen SSO-Lösungen

Beim Konfigurieren der einmaligen Anmeldung für eine Anwendung bietet das Azure-Verwaltungsportal als dritte Möglichkeit die Option "Vorhandenes einmaliges Anmelden". Diese Option ermöglicht es dem Administrator, einfach eine Verknüpfung zu einer Anwendung zu erstellen, und sie für ausgewählte Benutzer im Zugriffsbereich zu platzieren. Ist eine Anwendung beispielsweise zum Authentifizieren von Benutzern mit Active Directory Federation Services 2.0 konfiguriert, kann ein Administrator mit der Option "Vorhandenes einmaliges Anmelden" einen entsprechenden Link im Zugriffsbereich erstellen. Wenn Benutzer auf den Link zugreifen, werden sie mit Active Directory Federation Services 2.0 oder einer beliebigen anderen Lösung für einmaliges Anmelden authentifiziert, die von der Anwendung bereitgestellt wird.


[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

<!---HONumber=AcomDC_0107_2016-->