<properties 
	pageTitle="Überwachen Sie Ihre lokalen Identitätsinfrastruktur in der Cloud."
	description="Auf der Seite ";Azure AD Connect Health"; wird der Dienst beschrieben, und es werden die Gründe für seine Verwendung erörtert."
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
	ms.topic="get-started-article"
	ms.date="08/12/2015"
	ms.author="billmath"/>

# Überwachen Ihrer lokalen Identitätsinfrastruktur und Synchronisierung von Diensten in der Cloud

Azure AD Connect Health unterstützt Sie bei der Überwachung Ihrer lokalen Identitätsinfrastruktur und über Azure AD Connect verfügbarer Synchronisierungsdienste und vermittelt Ihnen wichtige Einblicke. Sie können damit Warnungen, Leistungsdaten, Verwendungsmuster und Konfigurationseinstellungen anzeigen, eine zuverlässige Verbindung mit Office 365 aufrechterhalten und viele weitere Funktionen nutzen. Dies wird mithilfe eines Agents erreicht, der auf den Zielservern installiert ist.

![Was ist Azure AD Connect Health?](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Diese Informationen sind alle im Azure AD Connect Health-Portal aufgeführt. Im Azure AD Connect Health-Portal können Sie Warnungen, Leistungsüberwachungsdaten und Nutzungsanalysen anzeigen. Diese Informationen werden alle an einer zentralen Stelle präsentiert, damit Sie keine Zeit mit der Suche nach den Informationen verschwenden, die Sie benötigen.

![Was ist Azure AD Connect Health?](./media/active-directory-aadconnect-health/usage.png)

Künftige Updates für Azure AD Connect Health werden zusätzliche Überwachungsfunktionen und Einblicke in andere Identitätskomponenten und -dienste umfassen, wie z. B. die Azure AD Connect-Synchronisierungsdienste. Entsprechend können Sie mithilfe eines zentralen Dashboards im Hinblick auf die Identität für eine stabilere, fehlerfreie und besser integrierte Umgebung sorgen, damit Ihre Benutzer ihre Aufgaben schneller erledigen können.


<center>![Was ist Azure AD Connect Health](./media/active-directory-aadconnect-health/logo1.png)</center>




## Gründe für die Verwendung von Azure AD Connect Health

Die Integration Ihrer lokalen Verzeichnisse in Azure AD steigert die Produktivität Ihrer Benutzer, da für den Zugriff auf die Cloud und lokale Ressourcen nur eine Identität benötigt wird. Allerdings erfordert diese Integration, dass diese Umgebung fehlerfrei bleibt, sodass Benutzer von jedem Gerät verlässlich auf lokale ebenso wie auf Cloudressourcen zugreifen können. Azure AD Connect Health bietet einen einfachen cloudbasierten Ansatz zum Überwachen und Gewinnen von Einblicken in Ihre lokale Identitätsverwaltungsinfrastruktur, die für den Zugriff auf Office 365 oder andere Azure AD-Anwendungen genutzt wird. Dieser Ansatz ist ähnlich unkompliziert wie das Installieren eines Agents auf Ihren lokalen Identitätsservern.

Azure AD Connect Health für AD FS unterstützt AD FS 2.0 unter Windows Server 2008/2008 R2 und AD FS unter Windows Server 2012/2012R2. Dies umfasst auch alle AD FS-Proxy- oder Webanwendungsproxy-Server, die die Authentifizierung für den Extranetzugriff unterstützen. Azure AD Connect Health für AD FS stellt die folgenden wichtigen Funktionen bereit:

- Anzeigen von Warnungen und Einleiten entsprechender Gegenmaßnahmen, um den zuverlässigen Zugriff auf AD FS-geschützte Anwendungen wie Azure AD zu ermöglichen
- E-Mail-Benachrichtigungen für kritische Warnungen
- Anzeigen von Leistungsdaten für die Kapazitätsplanung
- Detaillierte Ansichten Ihrer AD FS-Anmeldemuster, um Abweichungen zu ermitteln und Baselines für die Kapazitätsplanung festzulegen

Das folgende Video bietet einen Überblick über Azure AD Connect Health:

[AZURE.VIDEO azure-ad-connect-health--monitor-you-identity-bridge]



## Erste Schritte im Azure-Portal
Führen Sie zum Einstieg in Azure Active Directory Connect Health die folgenden Schritte aus.

1. Melden Sie sich beim [Microsoft Azure-Portal](https://portal.azure.com/) an.
2. Sie greifen auf Azure Active Directory Connect Health zu, indem Sie zum Marketplace wechseln und dort danach suchen, oder Sie wechseln zum Marketplace und wählen dort "Sicherheit + Identität" aus.
3. Klicken Sie im einführenden Blatt (ein Blatt ist ein Element der Gesamtansicht, Sie können sich ein Blatt wie ein Fenster oder einen Ausfalter vorstellen. Klicken Sie auf **Erstellen**. Es wird ein weiteres Blatt mit Informationen zu Ihrem Verzeichnis geöffnet.
4. Klicken Sie im Blatt mit den Verzeichnisinformationen auf **Erstellen**. Zur Verwendung von Azure AD Connect Health benötigen Sie eine Azure Active Directory Premium-Lizenz. Informationen zu Azure AD Premium finden Sie unter "Erste Schritte mit Azure AD Premium".

>[AZURE.NOTE]Denken Sie daran, dass Sie den Azure AD Connect Health-Agent auf Ihren Zielservern installieren müssen, um in Ihrer Azure AD Connect Health-Instanz Daten anzeigen zu können. Um den Azure AD Connect Health-Agent herunterzuladen, wählen Sie im ersten Blatt "Schnellstart und Tools herunterladen". Sie können den Agenten auch direkt über den nachstehenden [Link](#download-the-agent) herunterladen. Gehen Sie zur Verwendung von Azure Active Directory Connect Health folgendermaßen vor:



### Azure AD Connect Health-Portal und -Dienste
Im Azure AD Connect Health-Portal können Sie Warnungen, Leistungsüberwachungsdaten und Nutzungsanalysen anzeigen. Beim ersten Zugriff auf Azure AD Connect Health wird das erste Blatt angezeigt. Sie können es sich wie ein Fenster vorstellen. Das zuerst angezeigte Blatt zeigt Schnellstart, Dienste und Konfiguration. Der nachstehende Screenshot zeigt jedes dieser Elemente, die nachfolgend kurz beschrieben werden. Dieser Abschnitt zeigt die aktiven Dienste und Instanzen von Diensten, die von Azure AD Connect Health überwacht werden.

![Azure AD Connect Health-Portal](./media/active-directory-aadconnect-health/portal2.png)

- **Schnellstart** – Bei Auswahl wird das Blatt "Schnellstart" geöffnet. Hier können Sie den Azure AD Connect Health-Agent herunterladen, indem Sie auf "Tools abrufen" klicken, Sie können auf die Dokumentation zugreifen und Feedback geben.
- **Active Directory-Verbunddienste** – Repräsentiert alle AD FS-Dienste, die aktuell von Azure AD Connect Health überwacht werden. Durch Auswahl einer der Instanzen wird ein Blatt mit Informationen zu dieser Dienstinstanz geöffnet. darunter beispielsweise eine Übersicht, Eigenschaften, Warnungen, Überwachungsinformationen und eine Nutzungsanalyse. 
- "Konfigurieren" – Ermöglicht das Aktivieren oder Deaktivieren der folgenden Optionen:
<ol>
1. Automatische Aktualisierung des Azure AD Connect Health-Agents auf die aktuelle Version – Dies bedeutet, dass eine automatische Aktualisierung auf die aktuelle Version des Azure AD Connect Health-Agents durchgeführt wird, sobald diese verfügbar ist. Diese Einstellung ist standardmäßig aktiviert.
2. Erlauben Sie Microsoft, zu Zwecken der Problembehandlung den Zugriff auf die Integritätsdaten für Ihr Azure AD-Verzeichnis – Wenn Sie diese Einstellung aktivieren, kann Microsoft dieselben Daten anzeigen wie Sie. Dies kann bei der Problembehandlung und zur Unterstützung bei der Fehlerbeseitigung von Nutzen sein. Diese Einstellung ist standardmäßig deaktiviert.





## Anforderungen
Nachfolgend wird eine Liste der Voraussetzungen bereitgestellt, die vor der Verwendung von Azure AD Connect Health erfüllt werden müssen.

| Voraussetzung | Beschreibung|
| ----------- | ---------- |
|Azure AD Premium| Azure AD Connect Health ist ein Azure AD Premium-Feature und erfordert Azure AD Premium. </br></br>Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD Premium](active-directory-get-started-premium.md).</br></br>Wie Sie eine kostenlose 30-Tage-Testversion starten, erfahren Sie unter [Starten einer Testversion](https://azure.microsoft.com/trial/get-started-active-directory/).|.
|Sie müssen ein globaler Administrator Ihres Azure AD-Verzeichnisses sein.|Globale Administratoren haben standardmäßig Zugriff auf die Informationen, die von Azure AD Connect Health bereitgestellt werden. Wenn Sie kein globaler Administrator Ihres Azure AD-Verzeichnisses sind, können Sie keine Dienstinstanz von Azure AD Connect Health erstellen. Stellen Sie sicher, dass Sie als globaler Administrator konfiguriert sind. Weitere Informationen finden Sie unter [Verwalten Sie Ihr Azure AD-Verzeichnis](active-directory-administer.md).</br></br>** Wichtig: ** Das bei der Installation des Agents verwendete Konto muss ein Arbeits- oder Organisationskonto und darf kein Microsoft-Konto sein. Weitere Informationen finden Sie unter [Als Unternehmen für Azure registrieren](sign-up-organization.md).|
|Aktivieren der AD FS-Überwachung zum Verwenden der Nutzungsanalyse| Wenn Sie eine Verwendungsanalyse mit AD FS ausführen möchten, muss die AD FS-Überwachung aktiviert werden. </br></br>Siehe [Aktivieren der Überwachung für AD FS](active-directory-aadconnect-health-operations.md#enable-auditing-for-ad-fs).
|Erfüllen der Anforderungen für den Azure AD Connect Health-Agenten|Die folgende Tabelle enthält agentenspezifische Anforderungen.

Nachfolgend wird eine Liste der Voraussetzungen für den Agenten bereitgestellt, die vor der Verwendung von Azure AD Connect Health erfüllt werden müssen.

| Voraussetzung | Beschreibung|
| ----------- | ---------- |
|Der Azure AD Connect Health-Agent ist auf jedem Zielserver installiert| Azure AD Connect Health erfordert, dass ein Agent auf den Zielservern installiert wird, um die im Portal angezeigten Daten bereitzustellen. </br></br>Beispielsweise muss zum Abrufen von Daten aus Ihrer lokalen AD FS-Infrastruktur der Agent auf den AD FS-Servern installiert sein. Dies schließt die AD FS-Proxyserver und Webanwendungsproxyserver ein. </br></br>Informationen zur Installation des Agents finden Sie unter [Installation des Azure AD Connect Health-Agents.](active-directory-aadconnect-health-agent-install.md)</br></br>** Wichtig: ** Das bei der Installation des Agents verwendete Konto muss ein Arbeits- oder Organisationskonto und darf kein Microsoft-Konto sein. Weitere Informationen finden Sie unter [Als Unternehmen für Azure registrieren](sign-up-organization.md).|
|Ausgehende Verbindungen zu den Azure-Dienstendpunkten|Während der Installation und der Laufzeit erfordert der Agent Verbindungen mit den nachfolgend aufgeführten Endpunkten des Azure AD Connect Health-Diensts. Wenn Sie ausgehende Verbindungen blockieren, stellen Sie sicher, dass folgende Einträge der Zulassungsliste hinzugefügt werden: </br></br><li>&#42;.servicebus.windows.net - Port: 5671</li><li>https://&#42;.adhybridhealth.azure.com/</li><li>https://&#42;.table.core.windows.net/</li><li>https://policykeyservice.dc.ad.msft.net/</li><li>https://login.windows.net</li><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li> |
|Firewall-Ports auf dem Server, auf dem der Agent ausgeführt wird.| Für den Agenten müssen die folgenden Firewallports geöffnet sein, damit er mit den Azure AD Health-Dienstendpunkten kommunizieren kann: </br></br><li>TCP/UDP-Port 80</li><li>TCP/UDP-Port 443</li>.
|Zulassen der folgenden Websites, wenn die verstärkte IE-Sicherheit aktiviert ist|Die folgenden Websites müssen zugelassen werden, wenn die verstärkte Sicherheit für den Internet Explorer auf dem Server aktiviert ist, auf dem der Agent installiert werden soll.</br></br><li>https://login.microsoftonline.com</li><li>https://secure.aadcdn.microsoftonline-p.com</li><li>https://login.windows.net</li><li>Der Verbundserver für Ihre Organisation, dem Azure Active Directory vertraut. Beispiel: https://sts.contoso.com</li> 

## Herunterladen des Agents

Für die ersten Schritte mit Azure AD Connect Health können Sie die neuste Version des Agents hier herunterladen: [Azure AD Connect Health-Agent – Download](http://go.microsoft.com/fwlink/?LinkID=518973). Stellen Sie vor der Installation der Agents sicher, dass Sie den Dienst aus dem Marketplace hinzugefügt haben.


## Verwandte Links

* [Azure AD Connect Health Agent-Installation für AD FS](active-directory-aadconnect-health-agent-install-adfs.md)
* [Azure AD Connect Health-Vorgänge](active-directory-aadconnect-health-operations.md)
* [Verwenden von Azure AD Connect Health mit AD FS](active-directory-aadconnect-health-adfs.md)
* [Azure AD Connect Health – FAQ](active-directory-aadconnect-health-faq.md)


 

<!---HONumber=August15_HO9-->