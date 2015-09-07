<properties 
	pageTitle="Azure AD Connect Health Agent-Installation für AD FS | Microsoft Azure"
	description="Dies ist die Azure AD Connect Health-Seite, die die Agent-Installation für Active Directory-Verbunddienste (AD FS) beschreibt."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="billmath"/>






# Azure AD Connect Health Agent-Installation für AD FS

In diesem Dokument werden Sie durch die Installation und Konfiguration des Azure AD Connect Health-Agents für AD FS auf Ihren Servern geleitet.

>[AZURE.NOTE]Denken Sie daran, dass Sie den Azure AD Connect Health-Agent auf Ihren Zielservern installieren müssen, um in Ihrer Azure AD Connect Health-Instanz Daten anzeigen zu können. Stellen Sie sicher, dass die [hier](active-directory-aadconnect-health.md#requirements) aufgeführten Voraussetzungen erfüllt sind, bevor Sie den Agenten installieren. Sie können den Agenten [hier](http://go.microsoft.com/fwlink/?LinkID=518973) herunterladen.


## Installation des Agenten
Um die Installation des Agenten zu starten, doppelklicken Sie auf die EXE-Datei, die Sie heruntergeladen haben. Klicken Sie auf dem ersten angezeigten Bildschirm auf "Installieren".

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Klicken Sie nach Abschluss der Installation auf "Jetzt konfigurieren".

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Es wird eine Eingabeaufforderung mit PowerShell-Befehlen zum Ausführen von "Register-AzureADConnectHealthADFSAgent" geöffnet. Sie werden aufgefordert, sich bei Azure anzumelden. Melden Sie sich bei Azure an.

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Nach der Anmeldung werden weitere PowerShell-Befehle ausgeführt. Nach deren Ausführung können Sie PowerShell schließen, und die Konfiguration ist abgeschlossen.

Die Dienste sollten jetzt automatisch gestartet werden, und der Agent sammelt Daten und führt eine Überwachung durch. Wenn nicht sämtliche der in den obigen Abschnitten beschriebenen Voraussetzungen erfüllt sind, werden im PowerShell-Fenster entsprechende Warnungen angezeigt. Stellen Sie sicher, dass die [hier](active-directory-aadconnect-health.md#requirements) aufgeführten Voraussetzungen erfüllt sind, bevor Sie den Agenten installieren. Der folgende Screenshot zeigt ein Beispiel dieser Fehler.

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Um sicherzustellen, dass der Agent installiert wurde, öffnen Sie die Dienste und prüfen Folgendes. Nach Abschluss der Konfiguration sollten die folgenden Dienste ausgeführt werden. Die Dienste werden erst gestartet, wenn die Konfiguration abgeschlossen wurde.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service
 
![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


## Installation des Agents auf Windows Server 2008 R2-Servern

Führen Sie für Windows Server 2008 R2-Server die folgenden Schritte aus:

1. Stellen Sie sicher, dass auf dem Server Service Pack 1 oder höher ausgeführt wird.
1. Deaktivieren Sie für die Installation des Agents die verstärkte Sicherheit für Internet Explorer:
1. Installieren Sie auf jedem Server Windows PowerShell 4.0, bevor Sie den AD Health-Agent installieren. So installieren Sie Windows PowerShell 4.0
 - Installieren Sie [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779), indem Sie den folgenden Link zum Herunterladen des Offlineinstallationsprogramms nutzen.
 - Installieren Sie PowerShell ISE (über "Windows-Features").
 - Installieren Sie [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).
 - Installieren Sie Internet Explorer 10 oder höher auf dem Server. Dies ist erforderlich, damit der Health-Dienst Sie mithilfe der Azure-Administratoranmeldeinformationen authentifizieren kann.
1. Weitere Informationen zum Installieren von Windows PowerShell 4.0 unter Windows Server 2008 R2 finden Sie in [diesem](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx) Wiki-Artikel.

## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)

<!---HONumber=August15_HO9-->