<properties
	pageTitle="Installieren des AD Connect Health-Agents | Microsoft Azure"
	description="Auf dieser Seite zu Azure AD Connect Health wird die Installation des Agents für AD FS und für die Synchronisierung beschrieben."
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
	ms.date="10/15/2015"
	ms.author="billmath"/>






# Installieren des Azure AD Connect Health-Agents 

In diesem Dokument werden Sie durch die Installation und Konfiguration des Azure AD Connect Health-Agents für AD FS und für die Synchronisierung geleitet.

>[AZURE.NOTE]Denken Sie daran, dass Sie den Azure AD Connect Health-Agent auf Ihren Zielservern installieren müssen, um in Ihrer Azure AD Connect Health-Instanz AD FS-Daten anzeigen zu können. Stellen Sie sicher, dass die [hier](active-directory-aadconnect-health.md#requirements) aufgeführten Voraussetzungen erfüllt sind, bevor Sie den Agenten installieren. Sie können den Agenten [hier](http://go.microsoft.com/fwlink/?LinkID=518973) herunterladen.


## Installieren des Azure AD Connect Health-Agents für AD FS
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


### Installation des Agents auf Windows Server 2008 R2-Servern

Führen Sie für Windows Server 2008 R2-Server die folgenden Schritte aus:

1. Stellen Sie sicher, dass auf dem Server Service Pack 1 oder höher ausgeführt wird.
1. Deaktivieren Sie für die Installation des Agents die verstärkte Sicherheit für Internet Explorer:
1. Installieren Sie auf jedem Server Windows PowerShell 4.0, bevor Sie den AD Health-Agent installieren. So installieren Sie Windows PowerShell 4.0
 - Installieren Sie [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779), indem Sie den folgenden Link zum Herunterladen des Offlineinstallationsprogramms nutzen.
 - Installieren Sie PowerShell ISE (über "Windows-Features").
 - Installieren Sie [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).
 - Installieren Sie Internet Explorer 10 oder höher auf dem Server. Dies ist erforderlich, damit der Health-Dienst Sie mithilfe der Azure-Administratoranmeldeinformationen authentifizieren kann.
1. Weitere Informationen zum Installieren von Windows PowerShell 4.0 unter Windows Server 2008 R2 finden Sie in [diesem](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx) Wiki-Artikel.

### Aktivieren der Überwachung für AD FS

Um mithilfe der Nutzungsanalyse Daten zu sammeln und zu analysieren, benötigt der Azure AD Connect Health-Agent die in den AD FS-Überwachungsprotokollen enthaltenen Informationen. Diese Protokolle sind nicht standardmäßig aktiviert. Dies gilt nur für AD FS-Verbundserver. Das Aktivieren der Überwachung ist auf AD FS-Proxyservern oder Webanwendungsproxyservern nicht erforderlich. Führen Sie die nachstehenden Anweisungen aus, um die AD FS-Überwachung zu aktivieren und die AD FS-Überwachungsprotokolle zu ermitteln.

#### So aktivieren Sie die Überwachung für AD FS 2.0

1. Klicken Sie auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf **Lokale Sicherheitsrichtlinie**.
2. Navigieren Sie zum Ordner **Sicherheitseinstellungen\\Lokale Richtlinien\\User Rights Management**, und doppelklicken Sie dann auf "Generieren von Sicherheitsüberwachungen".
3. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS 2.0-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
4. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>.
5. Schließen Sie "Lokale Sicherheitsrichtlinie", und öffnen Sie dann das Verwaltungs-Snap-In. Klicken Sie zum Öffnen des Verwaltungs-Snap-Ins auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf "AD FS 2.0 Management".
6. Klicken Sie im Bereich "Aktionen" auf "Verbunddiensteigenschaften bearbeiten".
7. Klicken Sie im Dialogfeld **Federation Service Properties** auf die Registerkarte **Ereignisse**.
8. Aktivieren Sie die Kontrollkästchen **Success audits** und **Failure audits**.
9. Klicken Sie auf **OK**.

#### So aktivieren Sie die Überwachung für AD FS unter Windows Server 2012 R2

1. Öffnen Sie **Lokale Sicherheitsrichtlinie**, indem Sie den Server-Manager auf dem Startbildschirm oder über die Taskleiste auf dem Desktop öffnen, und klicken Sie dann auf **Tools/Lokale Sicherheitsrichtlinie**.
2. Navigieren Sie zum Ordner **Sicherheitseinstellungen\\Lokale Richtlinien\\Zuweisen von Benutzerrechten**, und doppelklicken Sie dann auf **Generieren von Sicherheitsüberwachungen**.
3. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
4. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>.
5. Schließen Sie **Lokale Sicherheitsrichtlinie**, und öffnen Sie dann das AD FS-Verwaltungs-Snap-In (klicken Sie hierzu im Server-Manager auf "Tools", und wählen Sie dann "AD FS Management" aus).
6. Klicken Sie im Bereich "Aktionen" auf **Verbunddiensteigenschaften bearbeiten**.
7. Klicken Sie im Dialogfeld "Verbunddiensteigenschaften" auf die Registerkarte **Ereignisse**.
8. Aktivieren Sie die Kontrollkästchen **Success audits und Failure audits**, und klicken Sie anschließend auf **OK**.






#### So ermitteln Sie die AD FS-Überwachungsprotokolle


1. Öffnen Sie die Ereignisanzeige.
2. Wechseln Sie zu "Windows-Protokolle", und wählen Sie **Sicherheit** aus.
3. Klicken Sie auf der rechten Seite auf **Aktuelle Protokolle filtern**.
4. Wählen Sie unter "Ereignisquelle" die **AD FS-Überwachung** aus.

![AD FS-Überwachungsprotokolle](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING]Wenn eine Gruppenrichtlinie implementiert ist, die die AD FS-Überwachung deaktiviert, kann der Azure AD Connect Health-Agent keine Informationen sammeln. Stellen Sie sicher, dass die Überwachung nicht durch eine Gruppenrichtlinie deaktiviert wird.

[//]: # "Start des Abschnitts für die Konfiguration des Agent-Proxys"

## Installieren des Azure AD Connect Health-Agents für die Synchronisierung
Der Azure AD Connect Health-Agent für die Synchronisierung wird im neuesten Build von Azure AD Connect automatisch installiert. Damit Sie Azure AD Connect für die Synchronisierung verwenden können, müssen Sie die neueste Version von Azure AD Connect herunterladen und installieren. Sie können die neueste Version [hier](http://www.microsoft.com/download/details.aspx?id=47594) herunterladen.

Um sicherzustellen, dass der Agent installiert wurde, öffnen Sie die Dienste und prüfen Folgendes. Nach Abschluss der Konfiguration sollten die folgenden Dienste ausgeführt werden. Die Dienste werden erst gestartet, wenn die Konfiguration abgeschlossen wurde.

- Azure AD Connect Health AadSync Insights-Dienst
- Azure AD Connect Health AadSync-Überwachungsdienst
 
![Überprüfen von Azure AD Connect Health für die Synchronisierung](./media/active-directory-aadconnect-health-sync/services.png)

>[Azure.NOTE]Denken Sie daran, dass Sie Azure AD Connect Health nur mit Azure AD Premium nutzen können. Nur wenn Sie über Azure AD Premium verfügen, können Sie die Konfiguration im Azure-Portal abschließen. Weitere Informationen finden Sie in den [hier](active-directory-aadconnect-health.md#requirements) beschriebenen Anforderungen.




## Konfigurieren des Azure AD Connect Health-Agents zur Verwendung eines HTTP-Proxys
Sie können den Azure AD Connect Health-Agent für die Arbeit mit einem HTTP-Proxy konfigurieren.

>[AZURE.NOTE]- Die Verwendung von "Netsh WinHttp set ProxyServerAddress" funktioniert nicht, da der Agent Webanfragen mithilfe von System.Net anstelle der Microsoft Windows HTTP-Dienste durchführt. - Die konfigurierte HTTP-Proxyadresse wird für das Weiterleiten von verschlüsselten HTTPS-Nachrichten verwendet. -Authentifizierte Proxys (mit HTTPBasic) werden nicht unterstützt.

### Ändern der Health Agent-Proxykonfiguration
Sie haben die folgenden Möglichkeiten zum Konfigurieren des Azure AD Connect Health-Agents für die Verwendung eines HTTP-Proxys.

>[AZURE.NOTE]Sie müssen alle Azure AD Connect Health-Agent-Dienste neu starten, damit die Proxyeinstellungen aktualisiert werden. Führen Sie den folgenden Befehl aus:<br> Restart-Service AdHealth*

#### Importieren von vorhandenen Proxyeinstellungen

##### Importieren von Internet Explorer
Sie können die HTTP-Proxyeinstellungen von Internet Explorer importieren und für Azure AD Connect Health-Agents verwenden, indem Sie den folgenden PowerShell-Befehl auf jedem Server mit dem Health-Agent ausführen.

	Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importieren von WinHTTP
Sie können Ihre WinHTTP-Proxyeinstellungen importieren, indem Sie den folgenden PowerShell-Befehl auf jedem Server mit dem Health-Agent ausführen.

	Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Manuelles Angeben von Proxyadressen
Sie können einen Proxyserver manuell angeben, indem Sie den folgenden PowerShell-Befehl auf jedem Server mit dem Health-Agent ausführen.

	Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Beispiel: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

- "address" kann ein über DNS auflösbarer Servername oder eine IPv4-Adresse sein.
- "port" kann ausgelassen werden. In diesem Fall wird der Standardport 443 ausgewählt.

#### Löschen der vorhandenen Proxykonfiguration
Sie können die vorhandene Proxykonfiguration löschen, indem Sie den folgenden Befehl ausführen.

	Set-AzureAdConnectHealthProxySettings -NoProxy


### Lesen der aktuellen Proxyeinstellungen
Mit dem folgenden Befehl können Sie die aktuell konfigurierten Proxyeinstellungen lesen.

	Get-AzureAdConnectHealthProxySettings


[//]: # "Ende des Abschnitts für die Konfiguration des Agent-Proxys"


## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)

<!---HONumber=Nov15_HO2-->