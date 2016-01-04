<properties 
	pageTitle="Welchen Zweck erfüllen App-Kennwörter bei Azure MFA?" 
	description="Auf dieser Seite finden Benutzer Informationen zu App-Kennwörtern und zu deren Verwendung im Zusammenhang mit Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtland"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/19/2015" 
	ms.author="billmath"/>



# Welchen Zweck erfüllen App-Kennwörter bei Azure Multi-Factor Authentication?

Bestimmte Nicht-Browser-Apps, z. B. der systemeigene E-Mail-Client von Apple, der Exchange Active Sync verwendet, unterstützen derzeit keine Multi-Factor Authentication. Die Multi-Factor Authentication wird pro Benutzer aktiviert. Wenn die Multi-Factor Authentication für einen bestimmten Benutzer aktiviert wurde, können die betreffenden Nicht-Browser-Apps von diesem Benutzer nicht verwendet werden. Um dennoch eine Authentifizierung zu ermöglichen, wird ein App-Kennwort festgelegt.

>[AZURE.NOTE]Moderne Authentifizierung für Office 2013-Clients
>
> Office 2013-Clients (einschließlich Outlook) unterstützen jetzt neue Authentifizierungsprotokolle und bieten die Möglichkeit zum Aktivieren von Multi-Factor Authentication. Damit sind nach der Aktivierung keine App-Kennwörter mehr für Office 2013-Clients erforderlich. Weitere Informationen finden Sie unter [Office 2013 modern authentication public preview announced](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/) ("Öffentliche Preview für moderne Authentifizierung in Office 2013", in englischer Sprache).
 
## Verwenden von App-Kennwörtern

Im Folgenden werden einige wichtige Punkte bei der Verwendung von App-Kennwörtern aufgeführt.

- Das eigentliche Kennwort wird automatisch erzeugt und nicht vom Benutzer festgelegt. Ein Grund hierfür ist, dass automatisch generierte Kennwörter sicherer und für einen Angreifer schwerer zu erraten sind.
- Derzeit können pro Benutzer maximal 40 Kennwörter festgelegt werden. Wenn Sie versuchen, über diese Beschränkung hinaus weitere Kennwörter zu erstellen, werden Sie aufgefordert, eines der vorhandenen App-Kennwörter zu löschen, damit das neue Kennwort erstellt werden kann.
- Es wird empfohlen, App-Kennwörter pro Gerät und nicht pro Anwendung zu erstellen. Beispielsweise können Sie ein App-Kennwort für Ihren Laptop erstellen und dieses App-Kennwort für alle Anwendungen auf dem betreffenden Laptop verwenden.
- Bei der ersten Anmeldung wird ein App-Kennwort für Sie erstellt. Wenn Sie weitere Kennwörter benötigen, können Sie diese bei Bedarf erstellen.
 
![Einrichtung](./media/multi-factor-authentication-end-user-app-passwords/app.png)

Sobald ein App-Kennwort erstellt wurde, verwenden Sie in den entsprechenden Nicht-Browser-Apps dieses anstelle Ihres ursprünglichen Kennworts. Angenommen, Sie verwenden Multi-Factor Authentication und den systemeigenen E-Mail-Client von Apple auf Ihrem Telefon. In diesem Fall können Sie die Multi-Factor Authentication mithilfe des App-Kennworts umgehen, um sich im E-Mail-Client erfolgreich zu authentifizieren.

## Erstellen und Löschen von App-Kennwörtern
Während der ersten Anmeldung wird ein App-Kennwort erstellt, das Sie direkt verwenden können. Darüber hinaus können Sie später zusätzliche App-Kennwörter erstellen und löschen. Wie Sie dabei vorgehen, hängt davon ab, wie Sie Multi-Factor Authentication verwenden. Wählen Sie die für Sie am besten geeignete Vorgehensweise.

Verwendung von Multi-Factor Authentication|Beschreibung
:------------- | :------------- | 
[Ich verwende sie mit Office 365.](#creating-and-deleting-app-passwords-with-office-365)| Erstellen Sie Ihre App-Kennwörter über das Office 365-Portal.
[Nicht bekannt](#creating-and-deleting-app-passwords-with-myapps-portal)|Erstellen Sie Ihre App-Kennwörter unter [https://myapps.microsoft.com](https://myapps.microsoft.com)
[Ich verwende sie mit Microsoft Azure.](#create-app-passwords-in-the-azure-portal)| Erstellen Sie Ihre App-Kennwörter über das Azure-Portal.

## Erstellen und Löschen von App-Kennwörtern mit Office 365 

Wenn Sie Multi-Factor Authentication mit Office 365 verwenden, können Sie App-Kennwörter über das Office 365-Portal erstellen und löschen.

### Erstellen von App-Kennwörtern im Office 365-Portal
--------------------------------------------------------------------------------

1. Melden Sie sich beim [Office 365-Portal](https://login.microsoftonline.com/) an.
2. Klicken Sie in der oberen rechten Ecke auf das Widget, und wählen Sie die Office 365-Einstellungen.
3. Klicken Sie auf "Zusätzliche Sicherheitsüberprüfung".
4. Wählen Sie auf der rechten Seite **Aktualisieren meiner Telefonnummern für die Kontosicherheit** aus. ![Einrichtung](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Dadurch gelangen Sie zu der Seite, auf der Sie Ihre Einstellungen ändern können. ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Klicken Sie oben neben "Zusätzliche Sicherheitsüberprüfung" auf **App-Kennwörter.**
7. Klicken Sie auf **Erstellen**. ![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. Geben Sie einen Namen für das App-Kennwort an, und klicken Sie auf **Weiter**. ![Erstellen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu. ![Erstellen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### So löschen Sie App-Kennwörter im Office 365-Portal
--------------------------------------------------------------------------------


1. Melden Sie sich beim [Office 365-Portal](https://login.microsoftonline.com/) an.
2. Klicken Sie in der oberen rechten Ecke auf das Widget, und wählen Sie die Office 365-Einstellungen.
3. Klicken Sie auf "Zusätzliche Sicherheitsüberprüfung".
4. Wählen Sie auf der rechten Seite **Aktualisieren meiner Telefonnummern für die Kontosicherheit** aus. ![Einrichtung](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. Dadurch gelangen Sie zu der Seite, auf der Sie Ihre Einstellungen ändern können. ![Cloud](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. Klicken Sie oben neben "Zusätzliche Sicherheitsüberprüfung" auf **App-Kennwörter.**
7. Klicken Sie neben dem App-Kennwort, das Sie entfernen möchten, auf **Löschen**. ![Löschen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. Bestätigen Sie den Löschvorgang mit **Ja**. ![Löschen bestätigen](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. Sobald das App-Kennwort gelöscht wurde, klicken Sie auf **Schließen**. ![Schließen](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Erstellen und Löschen von App-Kennwörtern mit Myapps-Portal.
Wenn Sie nicht sicher sind, wie Sie die mehrstufige Authentifizierung verwenden, können Sie jederzeit ADN zum Löschen von App-Kenwörtern über das Portal "Meine Apps" erstellen.

### So erstellen Sie ein App-Kennwort mithilfe des Myapps-Portals

1. Melden Sie sich an bei [https://myapps.microsoft.com](https://myapps.microsoft.com).	
2. Wählen Sie oben das Profil aus.
3. Klicken Sie auf "Zusätzliche Sicherheitsüberprüfung". ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Dadurch gelangen Sie zu der Seite, auf der Sie Ihre Einstellungen ändern können. ![Einrichtung](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Klicken Sie oben neben "Zusätzliche Sicherheitsüberprüfung" auf **App-Kennwörter.**
6. Klicken Sie auf **Erstellen**. ![Erstellen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. Geben Sie einen Namen für das App-Kennwort an, und klicken Sie auf **Weiter**. ![Erstellen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu. ![Erstellen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### So löschen Sie ein App-Kennwort mithilfe des Myapps-Portals

1. Melden Sie sich an bei [https://myapps.microsoft.com](https://myapps.microsoft.com).	
2. Wählen Sie oben das Profil aus.
3. Klicken Sie auf "Zusätzliche Sicherheitsüberprüfung". ![Cloud](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. Dadurch gelangen Sie zu der Seite, auf der Sie Ihre Einstellungen ändern können. ![Einrichtung](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. Klicken Sie oben neben "Zusätzliche Sicherheitsüberprüfung" auf **App-Kennwörter.**
6. Klicken Sie neben dem App-Kennwort, das Sie entfernen möchten, auf **Löschen**. ![Löschen eines App-Kennworts](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. Bestätigen Sie den Löschvorgang mit **Ja**. ![Löschen bestätigen](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. Sobald das App-Kennwort gelöscht wurde, klicken Sie auf **Schließen**. ![Schließen](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Erstellen von App-Kennwörtern im Azure-Portal

Wenn Sie Multi-Factor Authentication mit Azure verwenden, können Sie App-Kennwörter über das Azure-Portal erstellen.

### Erstellen von App-Kennwörtern im Azure-Portal

1. Melden Sie sich im Azure-Verwaltungsportal an.
2. Klicken Sie oben mit der rechten Maustaste auf Ihren Benutzernamen, und wählen Sie "Zusätzliche Sicherheitsüberprüfung" aus.
3. Wählen Sie auf der Seite "Proofup" oben "App-Kennwörter".
4. Klicken Sie auf **Erstellen**.
5. Geben Sie einen Namen für das App-Kennwort an, und klicken Sie auf **Weiter**.
6. Kopieren Sie das App-Kennwort in die Zwischenablage, und fügen Sie es in Ihrer Anwendung hinzu. ![Cloud](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### So löschen Sie App-Kennwörter im Azure-Portal

1. Melden Sie sich im Azure-Verwaltungsportal an.
2. Klicken Sie oben mit der rechten Maustaste auf Ihren Benutzernamen, und wählen Sie "Zusätzliche Sicherheitsüberprüfung" aus.
3. Klicken Sie oben neben "Zusätzliche Sicherheitsüberprüfung" auf **App-Kennwörter.**
4. Klicken Sie neben dem App-Kennwort, das Sie entfernen möchten, auf **Löschen**.
5. Bestätigen Sie den Löschvorgang mit **Ja**.
6. Sobald das App-Kennwort gelöscht wurde, klicken Sie auf **Schließen**. ![Schließen](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

<!---HONumber=AcomDC_1125_2015-->