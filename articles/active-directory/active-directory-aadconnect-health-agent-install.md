<properties
	pageTitle="Installieren des Azure AD Connect Health-Agents | Microsoft Azure"
	description="Auf dieser Seite zu Azure AD Connect Health wird die Installation des Agents für AD FS und für die Synchronisierung beschrieben."
	services="active-directory"
	documentationCenter=""
	authors="karavar"
	manager="stevenpo"
	editor="karavar"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="03/21/2016"
	ms.author="vakarand"/>


# Installieren des Azure AD Connect Health-Agents

In diesem Dokument werden Sie durch die Installation und Konfiguration der Azure AD Connect Health-Agents geleitet. Sie können die Agents [hier](active-directory-aadconnect-health.md#download-and-install-azure-ad-connect-health-agent) herunterladen.

## 	Anforderungen
Die folgende Tabelle enthält eine Liste mit Anforderungen für die Verwendung von Azure AD Connect Health.

| Voraussetzung | Beschreibung|
| ----------- | ---------- |
|Azure AD Premium| Azure AD Connect Health ist ein Azure AD Premium-Feature und erfordert Azure AD Premium. </br></br>Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD Premium](active-directory-get-started-premium.md). </br>Wie Sie eine kostenlose 30-Tage-Testversion starten, erfahren Sie unter [Starten einer Testversion](https://azure.microsoft.com/trial/get-started-active-directory/).|
|Sie müssen ein globaler Administrator Ihrer Azure AD-Instanz sein, um die ersten Schritte für Azure AD Connect Health durchzuführen|Standardmäßig können nur globale Administratoren die Health-Agents installieren und konfigurieren, um die ersten Schritte auszuführen, auf das Portal zuzugreifen und Vorgänge in Azure AD Connect Health durchzuführen. Zusätzliche Informationen finden Sie unter [Verwalten Ihres Azure AD-Verzeichnisses](active-directory-administer.md). <br><br> Mit der rollenbasierten Zugriffssteuerung können Sie anderen Benutzern in Ihrer Organisation den Zugriff auf Azure AD Connect Health gewähren. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung für Azure AD Connect Health](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control). </br></br>**Wichtig:** Das bei der Installation des Agents verwendete Konto muss ein Geschäfts- oder Schulkonto und darf kein Microsoft-Konto sein. Weitere Informationen finden Sie unter [Als Unternehmen für Azure registrieren](sign-up-organization.md).
|Der Azure AD Connect Health-Agent ist auf jedem Zielserver installiert| Azure AD Connect Health erfordert, dass ein Agent auf den Zielservern installiert wird, um die im Portal angezeigten Daten bereitzustellen. </br></br>Um Daten für Ihre lokale AD FS-Infrastruktur zu erhalten, muss der Agent auf den AD FS-Servern, AD FS-Proxyservern und Webanwendungsproxy-Servern installiert sein. </br></br>**Wichtig:** Das bei der Installation des Agents verwendete Konto muss ein Geschäfts- oder Schulkonto und darf kein Microsoft-Konto sein. Weitere Informationen finden Sie unter [Als Unternehmen für Azure registrieren](sign-up-organization.md).|
|Ausgehende Verbindungen zu den Azure-Dienstendpunkten|Während der Installation und der Laufzeit erfordert der Agent Verbindungen mit den nachfolgend aufgeführten Endpunkten des Azure AD Connect Health-Diensts. Wenn Sie ausgehende Verbindungen blockieren, stellen Sie sicher, dass folgende Einträge der Zulassungsliste hinzugefügt werden: </br></br><li>&#42;.blob.core.windows.net </li><li>&#42;.queue.core.windows.net</li><li>adhsprodwus.servicebus.windows.net - Port: 5671 </li><li>https://management.azure.com </li><li>https://s1.adhybridhealth.azure.com/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|Firewall-Ports auf dem Server, auf dem der Agent ausgeführt wird.| Für den Agent müssen die folgenden Firewallports geöffnet sein, damit er mit den Azure AD Health-Dienstendpunkten kommunizieren kann:</br></br><li>TCP/UDP-Port 443</li><li>TCP/UDP-Port 5671</li>
|Zulassen der folgenden Websites, wenn die verstärkte IE-Sicherheit aktiviert ist|Die folgenden Websites müssen zugelassen werden, wenn die verstärkte Sicherheit für den Internet Explorer auf dem Server aktiviert ist, auf dem der Agent installiert werden soll.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Der Verbundserver für Ihre Organisation, dem Azure Active Directory vertraut. Beispiel: https://sts.contoso.com</li>




## Installieren des Azure AD Connect Health-Agents für AD FS
Um die Installation des Agenten zu starten, doppelklicken Sie auf die EXE-Datei, die Sie heruntergeladen haben. Klicken Sie auf dem ersten angezeigten Bildschirm auf "Installieren".

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install1.png)

Klicken Sie nach Abschluss der Installation auf "Jetzt konfigurieren".

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install2.png)

Es wird eine Eingabeaufforderung mit PowerShell-Befehlen zum Ausführen von "Register-AzureADConnectHealthADFSAgent" geöffnet. Sie werden aufgefordert, sich bei Azure anzumelden. Melden Sie sich bei Azure an.

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install3.png)


Nach der Anmeldung werden weitere PowerShell-Befehle ausgeführt. Nach deren Ausführung können Sie PowerShell schließen, und die Konfiguration ist abgeschlossen.

Die Dienste sollten jetzt automatisch gestartet werden, und der Agent sammelt Daten und führt eine Überwachung durch. Wenn nicht sämtliche der in den obigen Abschnitten beschriebenen Voraussetzungen erfüllt sind, werden im PowerShell-Fenster entsprechende Warnungen angezeigt. Stellen Sie sicher, dass die [hier](active-directory-aadconnect-health-agent-install.md#requirements) aufgeführten Voraussetzungen erfüllt sind, bevor Sie den Agenten installieren. Der Screenshot unten enthält ein Beispiel für diese Fehler.

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install4.png)

Um sicherzustellen, dass der Agent installiert wurde, öffnen Sie die Dienste und prüfen Folgendes. Nach Abschluss der Konfiguration sollten die folgenden Dienste ausgeführt werden. Die Dienste werden erst gestartet, wenn die Konfiguration abgeschlossen wurde.

- Azure AD Connect Health AD FS Diagnostics Service
- Azure AD Connect Health AD FS Insights Service
- Azure AD Connect Health AD FS Monitoring Service

![Überprüfen von Azure AD Connect Health](./media/active-directory-aadconnect-health-requirements/install5.png)


### Installation des Agents auf Windows Server 2008 R2-Servern

Führen Sie für Windows Server 2008 R2-Server die folgenden Schritte aus:

1. Stellen Sie sicher, dass auf dem Server Service Pack 1 oder höher ausgeführt wird.
1. Deaktivieren Sie für die Installation des Agents die verstärkte Sicherheit für Internet Explorer:
1. Installieren Sie auf jedem Server Windows PowerShell 4.0, bevor Sie den AD Health-Agent installieren. So installieren Sie Windows PowerShell 4.0
 - Installieren Sie [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=40779), indem Sie den folgenden Link zum Herunterladen des Offlineinstallationsprogramms nutzen.
 - Installieren Sie PowerShell ISE (über "Windows-Features").
 - Installieren Sie [Windows Management Framework 4.0](https://www.microsoft.com/download/details.aspx?id=40855).
 - Installieren Sie Internet Explorer 10 oder höher auf dem Server. Dies ist erforderlich, damit der Health-Dienst Sie mithilfe der Azure-Administratoranmeldeinformationen authentifizieren kann.
1. Weitere Informationen zum Installieren von Windows PowerShell 4.0 unter Windows Server 2008 R2 finden Sie in [diesem](http://social.technet.microsoft.com/wiki/contents/articles/20623.step-by-step-upgrading-the-powershell-version-4-on-2008-r2.aspx) Wiki-Artikel.

### Aktivieren der Überwachung für AD FS

Um mithilfe der Nutzungsanalyse Daten zu sammeln und zu analysieren, benötigt der Azure AD Connect Health-Agent die in den AD FS-Überwachungsprotokollen enthaltenen Informationen. Diese Protokolle sind nicht standardmäßig aktiviert. Dies gilt nur für AD FS-Verbundserver. Das Aktivieren der Überwachung ist auf AD FS-Proxyservern oder Webanwendungsproxyservern nicht erforderlich. Führen Sie die nachstehenden Anweisungen aus, um die AD FS-Überwachung zu aktivieren und die AD FS-Überwachungsprotokolle zu ermitteln.

#### So aktivieren Sie die Überwachung für AD FS 2.0

1. Klicken Sie auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf **Lokale Sicherheitsrichtlinie**.
2. Navigieren Sie zum Ordner **Sicherheitseinstellungen\\Lokale Richtlinien\\User Rights Management**, und doppelklicken Sie dann auf "Generieren von Sicherheitsüberwachungen".
3. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS 2.0-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
4. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>.
5. Schließen Sie "Lokale Sicherheitsrichtlinie", und öffnen Sie dann das Verwaltungs-Snap-In. Klicken Sie zum Öffnen des Verwaltungs-Snap-Ins auf **Start**, zeigen Sie auf **Programme** und dann auf **Verwaltung**, und klicken Sie anschließend auf "AD FS 2.0 Management".
6. Klicken Sie im Bereich "Aktionen" auf "Verbunddiensteigenschaften bearbeiten".
7. Klicken Sie im Dialogfeld **Federation Service Properties** auf die Registerkarte **Ereignisse**.
8. Aktivieren Sie die Kontrollkästchen **Success audits** und **Failure audits**.
9. Klicken Sie auf **OK**.

#### So aktivieren Sie die Überwachung für AD FS unter Windows Server 2012 R2

1. Öffnen Sie **Lokale Sicherheitsrichtlinie**, indem Sie den Server-Manager auf dem Startbildschirm oder über die Taskleiste auf dem Desktop öffnen, und klicken Sie dann auf **Tools/Lokale Sicherheitsrichtlinie**.
2. Navigieren Sie zum Ordner **Sicherheitseinstellungen\\Lokale Richtlinien\\Zuweisen von Benutzerrechten**, und doppelklicken Sie dann auf **Generieren von Sicherheitsüberwachungen**.
3. Stellen Sie auf der Registerkarte **Lokale Sicherheitseinstellung** sicher, dass das AD FS-Dienstkonto aufgeführt wird. Wenn es nicht vorhanden ist, klicken Sie auf **Benutzer oder Gruppe hinzufügen** und fügen es der Liste hinzu. Klicken Sie dann auf **OK**.
4. Öffnen Sie ein Eingabeaufforderungsfenster mit erhöhten Rechten, und führen Sie den folgenden Befehl zum Aktivieren der Überwachung aus: <code>auditpol.exe /set /subcategory:"Application Generated" /failure:enable /success:enable</code>.
5. Schließen Sie **Lokale Sicherheitsrichtlinie**, und öffnen Sie dann das AD FS-Verwaltungs-Snap-In (klicken Sie hierzu im Server-Manager auf "Tools", und wählen Sie dann "AD FS Management" aus).
6. Klicken Sie im Bereich "Aktionen" auf **Verbunddiensteigenschaften bearbeiten**.
7. Klicken Sie im Dialogfeld "Verbunddiensteigenschaften" auf die Registerkarte **Ereignisse**.
8. Aktivieren Sie die Kontrollkästchen **Success audits und Failure audits**, und klicken Sie anschließend auf **OK**.






#### So ermitteln Sie die AD FS-Überwachungsprotokolle


1. Öffnen Sie die Ereignisanzeige.
2. Wechseln Sie zu "Windows-Protokolle", und wählen Sie **Sicherheit** aus.
3. Klicken Sie auf der rechten Seite auf **Aktuelle Protokolle filtern**.
4. Wählen Sie unter "Ereignisquelle" die **AD FS-Überwachung** aus.

![AD FS-Überwachungsprotokolle](./media/active-directory-aadconnect-health-requirements/adfsaudit.png)

> [AZURE.WARNING] Wenn eine Gruppenrichtlinie implementiert ist, die die AD FS-Überwachung deaktiviert, kann der Azure AD Connect Health-Agent keine Informationen sammeln. Stellen Sie sicher, dass die Überwachung nicht durch eine Gruppenrichtlinie deaktiviert wird.

[//]: # "Start des Abschnitts für die Konfiguration des Agent-Proxys"

## Installieren des Azure AD Connect Health-Agents für die Synchronisierung
Der Azure AD Connect Health-Agent für die Synchronisierung wird im neuesten Build von Azure AD Connect automatisch installiert. Damit Sie Azure AD Connect für die Synchronisierung verwenden können, müssen Sie die neueste Version von Azure AD Connect herunterladen und installieren. Sie können die neueste Version [hier](http://www.microsoft.com/download/details.aspx?id=47594) herunterladen.

Um sicherzustellen, dass der Agent installiert wurde, öffnen Sie die Dienste und prüfen Folgendes. Nach Abschluss der Konfiguration sollten die folgenden Dienste ausgeführt werden. Die Dienste werden erst gestartet, wenn die Konfiguration abgeschlossen wurde.

- Azure AD Connect Health Sync Insights-Dienst
- Azure AD Connect Health Sync-Überwachungsdienst

![Überprüfen von Azure AD Connect Health für die Synchronisierung](./media/active-directory-aadconnect-health-sync/services.png)

>[Azure.NOTE] Denken Sie daran, dass Sie Azure AD Connect Health nur mit Azure AD Premium nutzen können. Nur wenn Sie über Azure AD Premium verfügen, können Sie die Konfiguration im Azure-Portal abschließen. Weitere Informationen finden Sie in den [hier](active-directory-aadconnect-health-agent-install.md#requirements) beschriebenen Anforderungen.


## Manuelle Registrierung für Azure AD Connect Health für die Synchronisierung
Wenn die Agent-Registrierung von Azure AD Connect Health für die Synchronisierung fehlschlägt, nachdem Sie Azure AD Connect erfolgreich installiert haben, können Sie den Agent mit dem folgenden PowerShell-Befehl manuell registrieren.

>[AZURE.IMPORTANT] Die Verwendung dieses PowerShell-Befehls ist nur erforderlich, wenn die Agent-Registrierung nach der Installation von Azure AD Connect fehlschlägt.

Der folgende PowerShell-Befehl ist NUR erforderlich, wenn die Health-Agent-Registrierung auch nach der erfolgreichen Installation und Konfiguration von Azure AD Connect fehlschlägt. In solchen Fällen können Azure AD Connect Health-Dienste NICHT gestartet werden, solange der Agent nicht erfolgreich registriert wurde.

Sie können den Azure AD Connect Health-Agent für die Synchronisierung mit dem folgenden PowerShell-Befehl manuell registrieren:

`Register-AzureADConnectHealthSyncAgent -AttributeFiltering $false -StagingMode $false`

Der Befehl hat folgende Parameter:

- AttributeFiltering : $true (default) – wenn Azure AD Connect den Standardattributsatz nicht synchronisiert und für die Verwendung eines gefilterten Attributsatzes angepasst wurde. Andernfalls $false.
- StagingMode: $false (Standard) – wenn der Azure AD Connect-Server sich NICHT im Stagingmodus befindet; $true, wenn der Server werden für den Stagingmodus konfiguriert ist.

Wenn Sie zur Authentifizierung aufgefordert werden, geben Sie das globale Administratorkonto ein (z.B. admin@domain.onmicrosoft.com), das zum Konfigurieren von Azure AD Connect verwendet wurde.



## Konfigurieren des Azure AD Connect Health-Agents zur Verwendung eines HTTP-Proxys
Sie können den Azure AD Connect Health-Agent für die Arbeit mit einem HTTP-Proxy konfigurieren.

>[AZURE.NOTE]
- Die Verwendung von „Netsh WinHttp set ProxyServerAddress“ funktioniert in diesem Fall nicht, da der Agent System.Net anstelle der Microsoft Windows HTTP-Dienste verwendet, um Webanforderungen zu senden.
- Die konfigurierte HTTP-Proxyadresse wird für das durchlaufen verschlüsselter HTTPS-Nachrichten verwendet.
- Authentifizierte Proxys (mit HTTPBasic) werden nicht unterstützt.

### Ändern der Health Agent-Proxykonfiguration
Sie haben die folgenden Möglichkeiten zum Konfigurieren des Azure AD Connect Health-Agents für die Verwendung eines HTTP-Proxys.

>[AZURE.NOTE] Sie müssen alle Azure AD Connect Health-Agent-Dienste neu starten, damit die Proxyeinstellungen aktualisiert werden. Führen Sie den folgenden Befehl aus:<br> Restart-Service AdHealth*

#### Importieren von vorhandenen Proxyeinstellungen

##### Importieren von Internet Explorer
Sie können die HTTP-Proxyeinstellungen von Internet Explorer importieren und für Azure AD Connect Health-Agents verwenden, indem Sie den folgenden PowerShell-Befehl auf jedem Server mit dem Health-Agent ausführen.

	Set-AzureAdConnectHealthProxySettings -ImportFromInternetSettings

##### Importieren von WinHTTP
Sie können Ihre WinHTTP-Proxyeinstellungen importieren, indem Sie den folgenden PowerShell-Befehl auf jedem Server mit dem Health-Agent ausführen.

	Set-AzureAdConnectHealthProxySettings -ImportFromWinHttp

#### Manuelles Angeben von Proxyadressen
Sie können einen Proxyserver manuell angeben, indem Sie den folgenden PowerShell-Befehl auf jedem Server mit dem Health-Agent ausführen.

	Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress address:port

Beispiel: *Set-AzureAdConnectHealthProxySettings -HttpsProxyAddress myproxyserver:443*

- "address" kann ein über DNS auflösbarer Servername oder eine IPv4-Adresse sein.
- "port" kann ausgelassen werden. In diesem Fall wird der Standardport 443 ausgewählt.

#### Löschen der vorhandenen Proxykonfiguration
Sie können die vorhandene Proxykonfiguration löschen, indem Sie den folgenden Befehl ausführen.

	Set-AzureAdConnectHealthProxySettings -NoProxy


### Lesen der aktuellen Proxyeinstellungen
Mit dem folgenden Befehl können Sie die aktuell konfigurierten Proxyeinstellungen lesen.

	Get-AzureAdConnectHealthProxySettings


## Testen der Verbindung mit dem Azure AD Connect Health-Dienst
Es können Probleme auftreten, die dazu führen, dass die Verbindung zwischen dem Azure AD Connect Health-Agent und dem Azure AD Connect Health-Dienst unterbrochen wird. Dazu gehören Netzwerkprobleme, Berechtigungsprobleme oder verschiedene andere Ursachen.

Wenn der Agent für mehr als zwei Stunden keine Daten an den Azure AD Connect Health-Dienst senden kann, wird Ihnen eine Warnung mit dem Hinweis angezeigt, dass die Daten des Health-Diensts nicht auf dem aktuellen Stand sind. In diesem Fall können Sie jetzt testen, ob die Azure AD Connect Health-Agents Daten an den Azure AD Connect Health-Dienst hochladen können, indem Sie den folgenden PowerShell-Befehl auf dem Computer ausführen, auf dem das Problem mit dem Agent vorliegt.

    Test-AzureADConnectHealthConnectivity -Role Adfs

Der Rollenparameter akzeptiert derzeit die folgenden Werte:

- Adfs
- Synchronisierung

Sie können das Flag -ShowResults im Befehl verwenden, um detaillierte Protokolle anzuzeigen. Nehmen Sie das folgende Beispiel:

    Test-AzureADConnectHealthConnectivity -Role Sync -ShowResult

>[AZURE.NOTE]Um das Konnektivitätstool zu verwenden, müssen Sie zunächst die Agent-Registrierung abschließen. Wenn Sie die Registrierung des Agents nicht abschließen können, stellen Sie sicher, dass alle [Anforderungen](active-directory-aadconnect-health-agent-install.md#requirements) für Azure AD Connect Health erfüllt sind. Dieser Verbindungstest wird standardmäßig bei der Agent-Registrierung durchgeführt.



## Verwandte Links

* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Verwenden von Azure AD Connect Health für die Synchronisierung](active-directory-aadconnect-health-sync.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)
* [Azure AD Connect Health: Versionsverlauf](active-directory-aadconnect-health-version-history.md)

<!---HONumber=AcomDC_0323_2016-->