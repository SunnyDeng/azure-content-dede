<properties 
	pageTitle="Welchen Zweck erfüllen App-Kennwörter bei Azure MFA?" 
	description="Auf dieser Seite finden Benutzer Informationen zu App-Kennwörtern und zu deren Verwendung im Zusammenhang mit Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
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
 
<center>![Setup](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

Sobald ein App-Kennwort erstellt wurde, verwenden Sie in den entsprechenden Nicht-Browser-Apps dieses anstelle Ihres ursprünglichen Kennworts. Angenommen, Sie verwenden Multi-Factor Authentication und den systemeigenen E-Mail-Client von Apple auf Ihrem Telefon. In diesem Fall können Sie die Multi-Factor Authentication mithilfe des App-Kennworts umgehen, um sich im E-Mail-Client erfolgreich zu authentifizieren.

### Erstellen von App-Kennwörtern
Während der ersten Anmeldung wird ein App-Kennwort erstellt, das Sie direkt verwenden können. Darüber hinaus können Sie später zusätzliche App-Kennwörter erstellen. Wie Sie dabei vorgehen, hängt davon ab, wie Sie Multi-Factor Authentication verwenden. Wählen Sie die für Sie am besten geeignete Vorgehensweise.

Verwendung von Multi-Factor Authentication|Beschreibung
:------------- | :------------- | 
[Ich verwende sie mit Office 365.](multi-factor-authentication-end-user-manage-o365.md)| Erstellen Sie Ihre App-Kennwörter über das Office 365-Portal.
[Ich verwende sie mit Microsoft Azure.](multi-factor-authentication-end-user-manage-azure.md)| Erstellen Sie Ihre App-Kennwörter über das Azure-Portal.
[Nicht bekannt](multi-factor-authentication-end-user-manage-myapps.md)|Erstellen Sie Ihre App-Kennwörter unter [https://myapps.microsoft.com](https://myapps.microsoft.com)




 

<!---HONumber=August15_HO6-->