<properties 
	pageTitle="Azure AD Connect Health – Voraussetzungen" 
	description="Auf dieser Seite zu Azure AD Connect Health werden die geltenden Anforderungen sowie die Installation des Agents beschrieben." 
	services="active-directory" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/12/2015" 
	ms.author="billmath"/>

# Azure Active Directory Connect Health – Voraussetzungen
Nachfolgend wird eine Liste der Voraussetzungen bereitgestellt, die vor der Verwendung von Azure AD Connect Health erfüllt werden müssen.



## Eine Azure AD Premium-Lizenz

Azure AD Connect Health ist ein Azure AD Premium-Feature und erfordert eine Azure AD Premium-Lizenz. Informationen zum Erwerb einer Lizenz finden Sie unter "Erste Schritte mit Azure AD Premium".
 

## Sie müssen ein globaler Administrator Ihres Azure AD-Mandanten sein

Globale Administratoren haben standardmäßig Zugriff auf die Informationen, die von Azure AD Connect Health bereitgestellt werden. Wenn Sie kein globaler Administrator Ihres Azure AD-Mandanten sind, der im Verbund mit Ihrem lokalen Active Directory konfiguriert ist, können Sie keine Dienstinstanz von Azure AD Connect Health erstellen. Stellen Sie sicher, dass Sie als globaler Administrator konfiguriert sind. Zusätzliche Informationen finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](https://msdn.microsoft.com/library/azure/hh967611.aspx).
 

## Der Azure AD Connect Health-Agent ist auf jedem Zielserver installiert

Azure AD Connect Health erfordert, dass ein Agent auf den Zielservern installiert wird, um die im Azure AD Connect Health-Portal angezeigten Daten bereitzustellen. Dies bedeutet, dass zum Abrufen von Daten aus Ihrer lokalen AD FS-Infrastruktur der Agent auf den AD FS-Servern installiert sein muss. Dies schließt die AD FS-Proxyserver und Webanwendungsproxyserver ein. Informationen zum Installieren des Azure AD Connect Health-Agents finden Sie unter "Azure AD Connect Health-Agent – Installationsschritte".


## Anforderungen für den Azure AD Connect Health-Agent

In den folgenden Abschnitten werden die spezifischen Anforderungen für den Azure AD Connect Health-Agent beschrieben.
 

### Herunterladen des Azure AD Connect Health-Agents

Für die ersten Schritte mit Azure AD Connect Health können Sie die neuste Version des Agents hier herunterladen: [Azure AD Connect Health-Agent – Download](http://go.microsoft.com/fwlink/?LinkID=518973). Stellen Sie vor der Installation der Agents sicher, dass Sie den Dienst vom Marketplace hinzugefügt haben.

 
### Ausgehende Verbindungen zu den Azure-Dienstendpunkten
Während der Installation und der Laufzeit erfordert der Agent Verbindungen mit den nachfolgend aufgeführten Endpunkten des Azure AD Connect Health-Diensts. Wenn Sie ausgehende Verbindungen blockieren, stellen Sie sicher, dass folgende Einträge zur Zulassungsliste hinzugefügt werden:

- *.servicebus.windows.net – Port: 5671 - https://*.adhybridhealth.azure.com/
- https://*.table.core.windows.net/
- https://policykeyservice.dc.ad.msft.net/
- https://login.windows.net
- https://login.microsoftonline.com
- https://secure.aadcdn.microsoftonline-p.com 

## Zulassen der folgenden Websites, wenn die verstärkte IE-Sicherheit aktiviert ist
Die folgenden Websites müssen zugelassen werden, wenn die verstärkte Sicherheitskonfiguration für Internet Explorer auf dem Server aktiviert ist, auf dem der Agent installiert wird.

- https://login.microsoftonline.com 
- https://secure.aadcdn.microsoftonline-p.com
- https://login.windows.net
- Der Verbundserver für Ihre Organisation, dem Azure Active Directory vertraut. Beispiel: https://sts.contoso.com 


### Aktivieren der AD FS-Überwachung zum Verwenden der Nutzungsanalyse

Um mithilfe der Nutzungsanalyse Daten zu sammeln und zu analysieren, benötigt der Azure AD Connect Health-Agent die in den AD FS-Überwachungsprotokollen enthaltenen Informationen. Diese Protokolle sind nicht standardmäßig aktiviert. Dies gilt nur für AD FS-Verbundserver. Das Aktivieren der Überwachung ist auf AD FS-Proxyservern oder Webanwendungsproxyservern nicht erforderlich. Führen Sie die nachstehenden Anweisungen aus, um die AD FS-Überwachung zu aktivieren und die AD FS-Überwachungsprotokolle zu ermitteln.

#### So aktivieren Sie die Überwachung für AD FS 2.0

1. Klicken Sie auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf **Lokale Sicherheitsrichtlinie**.
1. Navigieren Sie zum Ordner **Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten**, und doppelklicken Sie dann auf "Generieren von Sicherheitsüberwachungen".
1. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS 2.0-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus. `auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable`
1. Schließen Sie "Lokale Sicherheitsrichtlinie", und öffnen Sie dann das Verwaltungs-Snap-In. Klicken Sie zum Öffnen des Verwaltungs-Snap-Ins auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf "AD FS 2.0-Verwaltung".
1. Klicken Sie im Bereich "Aktionen" auf "Verbunddiensteigenschaften bearbeiten".
1. Klicken Sie im Dialogfeld **Verbunddiensteigenschaften** auf die Registerkarte **Ereignisse**.
1. Aktivieren Sie die Kontrollkästchen **Erfolgsüberwachungen** und **Fehlerüberwachungen**.
1. Klicken Sie auf **OK**.

#### So aktivieren Sie die Überwachung für AD FS unter Windows Server 2012 R2

1. Öffnen Sie **Lokale Sicherheitsrichtlinie,** indem Sie den **Server-Manager** auf dem Startbildschirm oder über die Taskleiste auf dem Desktop öffnen, und klicken Sie dann auf **Tools/Lokale Sicherheitsrichtlinie**.
1. Navigieren Sie zum Ordner **Sicherheitseinstellungen\Lokale Richtlinien\Zuweisen von Benutzerrechten**, und doppelklicken Sie dann auf **Generieren von Sicherheitsüberwachungen**.
1. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
1. Öffnen Sie eine Eingabeaufforderung mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus: `auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable.`
1. Schließen Sie **Lokale Sicherheitsrichtlinie**, und öffnen Sie dann das AD FS-Verwaltungs-Snap-In (klicken Sie hierzu im Server-Manager auf "Tools", und wählen Sie dann "AD FS-Verwaltung" aus).
1. Klicken Sie im Bereich "Aktionen" auf **Verbunddiensteigenschaften bearbeiten**.
1. Klicken Sie im Dialogfeld "Verbunddiensteigenschaften" auf die Registerkarte **Ereignisse**.
1. Aktivieren Sie die Kontrollkästchen **Erfolgsüberwachungen und Fehlerüberwachungen**. Klicken Sie anschließend auf **OK**.






#### So ermitteln Sie die AD FS-Überwachungsprotokolle


1. Öffnen Sie die **Ereignisanzeige**.</li>
1. Wechseln Sie zu "Windows-Protokolle", und wählen Sie **Sicherheit** aus.
1. Klicken Sie auf der rechten Seite auf **Aktuelle Protokolle filtern**.
1. Wählen Sie unter "Ereignisquelle" die **AD FS-Überwachung** aus.

![AD FS-Überwachungsprotokolle](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Wenn eine Gruppenrichtlinie implementiert ist, die die AD FS-Überwachung deaktiviert, kann der Azure AD Connect Health-Agent keine Informationen sammeln. Stellen Sie sicher, dass die Überwachung nicht durch eine Gruppenrichtlinie deaktiviert wird.


### Installation des Agents auf Windows Server 2008 R2-Servern

Führen Sie für Windows Server 2008 R2-Server die folgenden Schritte aus:

1. Stellen Sie sicher, dass auf dem Server Service Pack 1 oder höher ausgeführt wird.
1. Deaktivieren Sie für die Installation des Agents die verstärkte Sicherheit für Internet Explorer:
1. Installieren Sie auf jedem Server Windows PowerShell 4.0, bevor Sie den AD Health-Agent installieren. So installieren Sie Windows PowerShell 4.0
 - Installieren Sie [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779), indem Sie den folgenden Link zum Download des Offlineinstallers.
 - Installieren Sie PowerShell ISE (über "Windows-Features").
 - Installieren Sie [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).
 - Installieren Sie Internet Explorer 10 oder höher auf dem Server. Dies ist erforderlich, damit der Health-Dienst Sie mithilfe der Azure-Administratoranmeldeinformationen authentifizieren kann.
1. Weitere Informationen zum Installieren von Windows PowerShell 4.0 unter Windows Server 2008 R2 finden Sie in [diesem](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx) Wiki-Artikel.

## Bereitstellen des Azure AD Connect Health-Agents
In diesem Abschnitt werden Sie durch die Installation und Konfiguration des Azure AD Connect Health-Agents auf Ihren Servern geleitet. Denken Sie daran, dass Sie den Azure AD Connect Health-Agent auf Ihren Zielservern installieren müssen, um in Ihrer Azure AD Connect Health-Instanz Daten anzeigen zu können. Stellen Sie sicher, dass die oben aufgeführten Voraussetzungen erfüllt sind, bevor Sie den Agent installieren. Sie können den Agent mithilfe des oben genannten Links herunterladen und anschließend die nachstehenden Schritte ausführen.


Doppelklicken Sie auf die EXE-Datei, die Sie heruntergeladen haben. Klicken Sie auf dem ersten angezeigten Bildschirm auf "Installieren".

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Klicken Sie nach Abschluss der Installation auf "Jetzt konfigurieren".

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Es wird eine Eingabeaufforderung mit PowerShell-Befehlen zum Ausführen von "Register-AzureADConnectHealthADFSAgent" geöffnet. Sie werden aufgefordert, sich bei Azure anzumelden. Melden Sie sich bei Azure an.

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Nach der Anmeldung werden weitere PowerShell-Befehle ausgeführt. Nach deren Ausführung können Sie PowerShell schließen, und die Konfiguration ist abgeschlossen.

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)

Die Dienste sollten jetzt automatisch gestartet werden, und der Agent sammelt Daten und führt eine Überwachung durch. Wenn nicht sämtliche der in den obigen Abschnitten beschriebenen Voraussetzungen erfüllt sind, werden im PowerShell-Fenster entsprechende Warnungen angezeigt. Der folgende Screenshot zeigt ein Beispiel.

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Um sicherzustellen, dass der Agent installiert wurde, öffnen Sie die Dienste und prüfen Folgendes. Nach Abschluss der Konfiguration sollten die folgenden Dienste ausgeführt werden. Die Dienste werden erst gestartet, wenn die Konfiguration abgeschlossen wurde.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service
 
![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)

<!---HONumber=July15_HO4-->