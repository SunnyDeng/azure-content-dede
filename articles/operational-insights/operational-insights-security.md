<properties
	pageTitle="Sicherheit von Operational Insights"
	description="Erfahren Sie, wie Operational Insights Ihre Privatsphäre und Ihre Daten schützt."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="dev-center-name"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/07/2015"
	ms.author="banders"/>

# Sicherheit von Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Microsoft ist bestrebt, bei der Bereitstellung von Software und Diensten, mit denen Sie die IT-Infrastruktur Ihres Unternehmens verwalten, Ihre Privatsphäre und Ihre Daten zu schützen. Wenn Sie Ihre Daten anderen anvertrauen, setzt dieses Vertrauen strikte Sicherheit voraus. Das wissen wir. Microsoft hält strenge Compliance- und Sicherheitsrichtlinien ein – angefangen bei der Codierung bis hin zum Betreiben von Diensten.

Das Sichern und Schützen von Daten hat bei Microsoft oberste Priorität. Wenden Sie sich unter den [Azure-Supportoptionen](http://azure.microsoft.com/support/options/) an uns, wenn Sie Fragen, Vorschläge oder Probleme bezüglich der folgenden Informationen (einschließlich unserer Sicherheitsrichtlinien) haben.

In diesem Artikel wird erläutert, wie Daten in Microsoft Azure Operational Insights gesammelt, verarbeitet und geschützt werden. Sie können entweder Agents mit direkter Verbindung zum Webdienst oder System Center Operations Manager verwenden, um betriebliche Daten für den Operational Insights-Dienst zu sammeln. Die gesammelten Daten werden über das Internet zum Operational Insights-Dienst gesendet, der in Microsoft Azure gehostet wird.

Der Operational Insights-Dienst verwaltet Ihre Daten sicher mithilfe der folgenden Methoden:

**Trennung von Daten**: Kundendaten werden für jede Komponente des Operational Insights-Diensts logisch getrennt verwaltet. Sämtliche Daten werden nach Organisation gekennzeichnet. Dieser Kennzeichnung wird während des gesamten Datenlebenszyklus beibehalten und auf jeder Ebene des Diensts erzwungen.

Jeder Kunde verfügt über einen dedizierten Azure-Blob (Speicher), der die langfristigen Daten enthält. Dieser Blob wird verschlüsselt. Dazu werden kundenbezogene, eindeutige Schlüssel verwendet, die alle 90 Tage geändert werden.

**Datenaufbewahrung**: Die gesammelten Metriken für jede Lösung (früher als "Intelligence Pack" bezeichnet) werden in einer von Microsoft Azure gehosteten SQL-Datenbank gespeichert. Diese Daten werden für 390 Tage gespeichert. Indizierte Protokollsuchdaten werden im Durchschnitt für 10 Tage gespeichert, bevor die Daten bereinigt werden. Wenn das obere Limit von 20 Millionen Datensätzen für jeden Datentyp bereits früher erreicht wird, werden die Daten in Operational Insights nach weniger als 10 Tagen bereinigt. Wenn das Datenlimit nach 10 Tagen noch nicht erreicht ist, wartet Operational Insights vor der Bereinigung, bis das Limit erreicht ist.

**Physische Sicherheit**: Der Operational Insights-Dienst ist mit Microsoft-Mitarbeitern besetzt. Alle Aktivitäten werden protokolliert und können überwacht werden. Der Operational Insights-Dienst wird vollständig in Azure ausgeführt und erfüllt die allgemeinen technischen Kriterien für Azure. Ausführliche Informationen über die physische Sicherheit der Azure-Ressourcen finden Sie auf Seite 18 des Dokuments [Windows Azure Security Overview](http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf) (in englischer Sprache).

**Compliance und Zertifizierungen**: Das Softwareentwicklungs- und Serviceteam für Operational Insights arbeitet aktiv mit den Microsoft Legal- und Compliance-Teams und anderen Partnern der Branche zusammen, um eine Vielzahl von Zertifizierungen (einschließlich ISO) zu erhalten, bevor der Operational Insights-Dienst allgemein verfügbar ist.

Derzeit erfüllen wir die folgenden Sicherheitsstandards:

- Windows Common Engineering Criteria
- Microsoft Trustworthy Computing Certification


## Datensicherheitsfluss
Das folgende Diagramm zeigt den Informationsfluss von Ihrem Unternehmen und dessen Schutz auf dem Weg zum Operational Insights-Dienst, wo Sie sie letztlich in Operational Insights anzeigen können. Weitere Informationen zu den einzelnen Schritten finden Sie nach dem Diagramm.

![Bild der Datenerfassung und der Sicherheit von Operational Insights](./media/operational-insights-security/security.png)

### 1. Registrieren für Operational Insights und Sammeln von Daten

Damit Ihre Organisation Daten an den Operational Insights-Dienst senden kann, müssen Sie entweder Microsoft Monitoring Agents konfigurieren, wenn Sie eine direkte Verbindung mit dem Webdienst vornehmen, oder einen Konfigurations-Assistenten in der Betriebskonsole in Operations Manager verwenden. Benutzer (Sie, andere Einzelbenutzer oder eine Gruppe von Personen) müssen Operational Insights-Konten erstellen und jeden direkt verbundenen Agent bzw. ihre Operations Manager-Umgebung mithilfe eines der folgenden Konten registrieren:


- [Organisations-ID](../sign-up-organization.md)

- [Microsoft-Konto – Outlook, Office Live, MSN](../sign-up-organization.md)

In einem Operational Insights-Konto werden Daten gesammelt, aggregiert, analysiert und präsentiert. Operational Insights-Konten werden hauptsächlich zum Partitionieren von Daten verwendet, und jedes Operational Insights-Konto ist eindeutig. Beispielsweise empfiehlt es sich, Produktionsdaten mit einem Operational Insights-Konto zu verwalten und Testdaten mit einem anderen Konto. Konten helfen Administratoren außerdem dabei, den Benutzerzugriff auf Daten zu steuern. Jedem Operational Insights-Konto können mehrere Benutzerkonten zugeordnet werden, und jedes Benutzerkonto kann über mehrere Operational Insights-Konten verfügen.

Wenn der Konfigurations-Assistent abgeschlossen ist, stellt jede Operations Manager-Verwaltungsgruppe eine Verbindung mit dem Operational Insights-Dienst her. Sie wählen dann mit dem Assistenten zum Hinzufügen von Computern aus, welche Computer in der Verwaltungsgruppe Daten an den Dienst senden können.

Beide Typen von Agents sammeln Daten für Operational Insights. Der Typ der gesammelten Daten ist abhängig von den Typen der verwendeten Lösungen. Eine Lösung ist ein Bündel von vordefinierten Ansichten, Protokollsuchabfragen, Datensammlungsregeln und Verarbeitungslogik. Nur Operational Insights-Administratoren können mit Operational Insights Lösungen importieren. Nach dem Importieren der Lösung wird diese auf die Operations Manager-Verwaltungsserver verschoben (sofern verwendet) und dann auf die ausgewählten Operations Manager Agents. Anschließend sammeln die Agents die Daten.

Die folgende Tabelle enthält die verfügbaren Lösungen in Operational Insights und die Typen der von diesen gesammelten Daten.

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Lösung</b></td>
		<td><b>Datentypen</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Konfigurationsbewertung</td>
		<td>Konfigurationsdaten, Metadaten und Statusdaten</td>
    </tr>
    <tr align="left" valign="top">
		<td>Capacity Planning</td>
		<td>Leistungsdaten, Metadaten und Statusdaten</td>
    </tr>
	<tr align="left" valign="top">
		<td>Antimalware</td>
		<td>Konfigurationsdaten, Metadaten und Statusdaten</td>
    </tr>
	    <tr align="left" valign="top">
		<td>System Update Assessment</td>
		<td>Metadaten und Statusdaten</td>
    </tr>
    <tr align="left" valign="top">
		<td>Log Management</td>
		<td>Benutzerdefinierte Ereignisprotokolle</td>
    </tr>
    <tr align="left" valign="top">
		<td>Change Tracking</td>
		<td>Softwarebestand und Windows-Dienstmetadaten</td>
    </tr>
    <tr align="left" valign="top">
		<td>SQL und Active Directory Assessment</td>
		<td>WMI-Daten, Registrierungsdaten, Leistungsdaten und Ergebnisse der dynamischen SQL Server-Verwaltungssichten</td>
    </tr>
    </tbody>
    </table>


Die folgende Tabelle zeigt Beispiele für Datentypen:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Datentyp</b></td>
		<td><b>Felder</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>Warnung</td>
		<td>AlertName, AlertDescription, BaseManagedEntityId, ProblemId, IsMonitorAlert, RuleId, ResolutionState, Priority, Severity, Category, Owner, ResolvedBy, TimeRaised, TimeAdded, LastModified, LastModifiedBy, LastModifiedExceptRepeatCount, TimeResolved, TimeResolutionStateLastModified, TimeResolutionStateLastModifiedInDB, RepeatCount</td>
    </tr>
    <tr align="left" valign="top">
		<td>Konfiguration</td>
		<td>CustomerID, AgentID, EntityID, ManagedTypeID, ManagedTypePropertyID, CurrentValue, ChangeDate</td>
    </tr>
	<tr align="left" valign="top">
		<td>Ereignis</td>
		<td>EventId, EventOriginalID, BaseManagedEntityInternalId, RuleId, PublisherId, PublisherName, FullNumber, Number, Category, ChannelLevel, LoggingComputer, EventData, EventParameters, TimeGenerated, TimeAdded<p><b>Hinweis</b>: Wenn Sie Ereignisse mit benutzerdefinierten Feldern im Windows-Ereignisprotokoll protokollieren, werden diese von Operational Insights gesammelt. </td>
    </tr>
	    <tr align="left" valign="top">
		<td>Metadata</td>
		<td>BaseManagedEntityId, ObjectStatus, OrganizationalUnit, ActiveDirectoryObjectSid, PhysicalProcessors, NetworkName, IPAddress, ForestDNSName, NetbiosComputerName, VirtualMachineName, LastInventoryDate, HostServerNameIsVirtualMachine, NetbiosDomainName, LogicalProcessors, DNSName, DisplayName, DomainDnsName, ActiveDirectorySite, PrincipalName, OffsetInMinuteFromGreenwichTime</td>
    </tr>
    <tr align="left" valign="top">
		<td>Leistung</td>
		<td>ObjectName, CounterName, PerfmonInstanceName, PerformanceDataId, PerformanceSourceInternalID, SampleValue, TimeSampled, TimeAdded</td>
    </tr>
    <tr align="left" valign="top">
		<td>Zustand</td>
		<td>StateChangeEventId, StateId, NewHealthState, OldHealthState, Context, TimeGenerated, TimeAdded, StateId2, BaseManagedEntityId, MonitorId, HealthState, LastModified, LastGreenAlertGenerated, DatabaseTimeModified</td>
    </tr>
    </tbody>
    </table>


### 2. Senden von Daten von Agents

Agents, die direkte Verbindungen mit dem Webdienst herstellen, werden mit einem Schlüssel registriert. Zwischen dem Agent und dem Operational Insights-Dienst wird eine sichere Verbindung über Port 443 hergestellt.

Mit Operations Manager registrieren Sie ein Konto für den Operational Insights-Dienst. Zwischen dem Operations Manager-Verwaltungsserver und dem Operational Insights-Dienst wird eine sichere HTTPS-Verbindung über Port 443 hergestellt. Wenn Operations Manager aus irgendeinem Grund nicht mit dem Dienst kommunizieren kann, werden die gesammelten Daten in einem temporären Cache gespeichert, und der Verwaltungsserver versucht zwei Stunden lang alle acht Minuten, die Daten erneut zu senden. Gesammelte Daten werden komprimiert und unter Umgehung von lokalen Datenbanken an den Operational Insights-Dienst gesendet, sodass keine weitere Last hinzugefügt wird. Nach dem Senden werden die gesammelten Daten aus dem Cache entfernt.

### 3. Empfangen und Verarbeiten von Daten durch den Operational Insights-Dienst

Der Operational Insights-Dienst stellt sicher, dass eingehende Daten aus einer vertrauenswürdigen Quelle stammen, indem Zertifikate und die Integrität der Daten überprüft werden. Die unverarbeiteten Rohdaten werden dann als Blob in [Microsoft Azure Storage](http://azure.microsoft.com/documentation/services/storage/) gespeichert. Jeder Operational Insights-Benutzer verfügt über ein dediziertes Azure-Blob, auf das nur er zugreifen kann. Der Typ der gespeicherten Daten ist abhängig von den Typen der importierten und zum Sammeln von Daten verwendeten Lösungen.

Der Operational Insights-Dienst verarbeitet die Rohdaten, und die aggregierten verarbeiteten Daten werden in einer SQL-Datenbank gespeichert. Die Kommunikation zwischen dem Operational Insights-Dienst und der SQL-Datenbank basiert auf SQL-Datenbank-Authentifizierung.

### 4. Verwenden von Operational Insights für den Datenzugriff

Sie können sich über das Konto, das Sie bereits eingerichtet haben, bei Operational Insights anmelden. Sämtlicher Datenverkehr zwischen Operational Insights und dem Operational Insights-Dienst wird über einen sicheren HTTPS-Kanal gesendet.
 

<!---HONumber=July15_HO1-->