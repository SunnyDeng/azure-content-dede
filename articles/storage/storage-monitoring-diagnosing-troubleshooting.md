<properties 
	pageTitle="Überwachung, Diagnose und Problembehandlung von Speicher | Microsoft Azure" 
	description="Verwenden Sie Funktionen wie Speicheranalyse, clientseitige Protokollierung und andere Tools von Drittanbietern, um mit Azure-Speicher verbundene Probleme zu erkennen, zu diagnostizieren und zu beheben." 
	services="storage" 
	documentationCenter="" 
	authors="dominicbetts" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/03/2015" 
	ms.author="v-dobett"/>

# Microsoft Azure-Speicher: Überwachung, Diagnose und Problembehandlung 

Diagnose und Problembehandlung können in einer verteilten Anwendung, die in einer Cloudumgebung gehostet wird, komplexer sein als in herkömmlichen Umgebungen. Anwendungen können in einer PaaS- oder IaaS-Infrastruktur bereitgestellt werden, lokal, auf einem Mobilgerät oder in einer Kombination hiervon. In der Regel kann der Netzwerkverkehr der Anwendung öffentliche und private Netzwerke durchlaufen, und Ihre Anwendung kann mehrere Speichertechnologien wie Microsoft Azure-Speichertabellen, Blobs, Warteschlangen oder Dateien neben anderen Datenspeichern wie relationalen und Dokumentendatenbanken verwenden.

Um solche Anwendungen erfolgreich zu verwalten, sollten Sie sie proaktiv überwachen und verstehen, wie die Diagnose und Problembehandlung aller Anwendungsaspekte und ihrer abhängigen Technologien durchzuführen sind. Als Nutzer des Azure-Speicherdiensts sollten Sie ständig die von Ihrer Anwendung verwendeten Speicherdienste überwachen, um unerwartete Verhaltensveränderungen zu erkennen (beispielsweise langsamere Antwortzeiten als üblich), und Protokollierung verwenden, um detailliertere Daten zu sammeln und ein Problem in der Tiefe zu analysieren. Die Diagnoseinformationen, die Sie sowohl aus der Überwachung als auch der Protokollierung erhalten, werden Ihnen helfen, die Ursache des Problems, das bei Ihrer Anwendung auftritt, zu bestimmen. Dann können Sie das Problem behandeln und die entsprechenden Schritte, die Sie für seine Beseitigung ergreifen können, festlegen. Azure-Speicher ist ein Kerndienst von Azure und ein wichtiger Bestandteil der meisten Lösungen, die Kunden auf der Azure-Infrastruktur bereitstellen. Azure-Speicher beinhaltet Funktionen zur vereinfachten Überwachung, Diagnose und Problembehandlung von Speicherproblemen in Ihren cloudbasierten Anwendungen.

Eine praktische Anleitung für die End-to-End-Problembehandlung in Azure-Speicheranwendungen finden Sie unter [End-to-End-Problembehandlung mit Azure-Speichermetriken und -Protokollierung, AzCopy und Message Analyzer](../storage-e2e-troubleshooting/).

+ [Einführung]
	+ [Wie diese Anleitung aufgebaut ist]
+ [Überwachung Ihres Speicherdiensts]
	+ [Überwachung der Dienstintegrität]
	+ [Kapazitätsüberwachung]
	+ [Verfügbarkeitsüberwachung]
	+ [Leistungsüberwachung]
+ [Diagnose von Speicherproblemen]
	+ [Probleme mit der Dienstintegrität]
	+ [Leistungsprobleme]
	+ [Fehlerdiagnose]
	+ [Probleme mit dem Speicheremulator]
	+ [Speicher-Protokollierungstools]
	+ [Verwendung von Netzwerk-Protokollierungstools]
+ [Durchgängige Verfolgung]
	+ [Korrelation von Protokollierungsdaten]
	+ [Clientanfrage-ID]
	+ [Serveranfrage-ID]
	+ [Zeitstempel]
+ [Anleitungen zur Problembehandlung]
	+ [Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an]
	+ [Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz]
	+ [Metriken zeigen hohe AverageServerLatency an]
	+ [Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange]
	+ [Metriken zeigen Anstieg bei PercentThrottlingError an]
	+ [Metriken zeigen Anstieg bei PercentTimeoutError an]
	+ [Metriken zeigen Anstieg bei PercentNetworkError an]
	+ [Der Client empfängt HTTP 403 (Verboten)-Meldungen]
	+ [Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen]
	+ [Der Client empfängt HTTP 409 (Konflikt)-Meldungen]
	+ [Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"]
	+ [Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an]
	+ [Sie stoßen auf unerwartete Neustarts von virtuellen Computern, die über eine große Anzahl angeschlossener virtueller VHDs verfügen]
	+ [Ihre Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test]
	+ [Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme]
	+ [Sie haben ein anderes Problem mit einem Speicherdienst]
+ [Anhänge]
	+ [Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr]
	+ [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr]
	+ [Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr]
	+ [Anhang 4: Verwendung von Excel zur Anzeige von Metrik- und Protokollierungsdaten]
	+ [Anhang 5: Überwachung mit Application Insights für Visual Studio Online]

## <a name="introduction"></a>Einführung

Diese Anleitung zeigt Ihnen, wie Sie Funktionen wie Azure Storage Analytics, clientseitige Protokollierung in der Azure-Speicherclient-Bibliothek und andere Tools von Drittanbietern verwenden, um mit Azure-Speicher verbundene Probleme zu erkennen, zu diagnostizieren und zu beheben.

![][1]

*Abbildung 1: Überwachung, Diagnose und Problembehandlung*

Dieser Leitfaden richtet sich in erster Linie an Entwickler von Online-Diensten, die Azure-Speicherdienste verwenden, sowie an IT-Experten, die für die Verwaltung solcher Online-Dienste verantwortlich sind. Mit dieser Anleitung möchten wir:

- Ihnen helfen, die Integrität und Leistungsfähigkeit Ihres Azure-Speicherkontos aufrechtzuerhalten
- Ihnen die notwendigen Prozesse und Tools für die Diagnose an die Hand geben, ob ein Problem in einer Anwendung mit Azure-Speicher zusammenhängt
- Ihnen umsetzbare Anleitungen für die Lösung von Problemen in Verbindung mit Azure-Speicher bereitstellen

### <a name="how-this-guide-is-organized"></a>Wie diese Anleitung aufgebaut ist

Der Abschnitt "[Überwachung Ihres Speicherdiensts]" beschreibt, wie Sie die Integrität und Leistungsfähigkeit Ihres Azure-Speicherdiensts mit Azure-Speicher-Analytikmetriken (Speichermetriken) überwachen können.

Der Abschnitt "[Diagnose von Speicherfehlern]" beschreibt, wie Probleme unter Verwendung von Azure-Speicheranalytik-Protokollierung (Speicherprotokollierung) diagnostiziert werden. Er beschreibt auch, wie Sie die clientseitige Protokollierung unter Verwendung einer der Clientbibliotheken wie der Speicher-Clientbibliothek für .NET oder des Azure SDK für Java aktivieren.

Der Abschnitt "[Durchgehende Verfolgung]" beschreibt, wie Sie die in Protokollierungsdateien und Metrikdaten enthaltenen Informationen zuordnen.

Der Abschnitt "[Anleitungen zur Problembehandlung]" stellt Anleitungen für die Behandlung einiger der allgemeinen speicherbezogenen Probleme bereit, auf die Sie stoßen könnten.

Die "[Anhänge]" enthalten Informationen zur Verwendung anderer Tools wie Wireshark und Netmon für die Analyse von Netzwerkpaketdaten, Fiddler für die Analyse von HTTP-/HTTPS-Nachrichten und Microsoft Message Analyzer für die Korrelation von Protokollierungsdaten.


## <a name="monitoring-your-storage-service"></a>Überwachung Ihres Speicherdiensts

Wenn Sie mit der Windows-Leistungsüberwachung vertraut sind, können Sie von Speichermetriken als einem Azure-Speicher-Pendant zu Windows-Leistungsüberwachungsindikatoren ausgehen. In Speichermetriken finden Sie einen umfassenden Metriksatz (Indikatoren in der Windows Performance Monitor-Terminologie) wie Dienstverfügbarkeit, Gesamtzahl der Dienstanfragen, oder Prozentsatz der erfolgreichen Dienstanfragen (für eine vollständige Liste der verfügbaren Metriken siehe <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Tabellenschema der Speicher-Analytikmetriken</a> in MSDN). Sie können spezifizieren, ob der Speicherdienst die Metriken jede Stunde oder jede Minute sammeln und aggregieren soll. Weitere Informationen zur Metrik-Aktivierung und Überwachung Ihrer Speicherkonten finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=510865" target="_blank">Aktivierung von Speichermetriken</a> in MSDN.

Sie können wählen, welche Stundenmetriken Sie im Azure-Portal anzeigen möchten, und Regeln konfigurieren, die den Administrator per E-Mail benachrichtigen, wenn eine Stundenmetrik einen bestimmten Schwellenwert überschreitet (weitere Informationen finden Sie auf der Seite <a href="http://msdn.microsoft.com/library/azure/dn306638.aspx" target="_blank">Gewusst wie: Empfangen von Warnbenachrichtigungen und Verwalten von Warnungsregeln in Azure</a>). Der Speicherdienst sammelt Metriken nach dem Best-Effort-Prinzip, kann aber nicht jeden Speichervorgang aufzeichnen.

Abbildung 2 unten zeigt die Überwachungsseite im Portal, auf der Sie Metriken wie Verfügbarkeit, Gesamtanfragen und durchschnittliche Latenzzahlen für ein Speicherkonto anzeigen können. Zudem wurde eine Benachrichtigungsregel eingerichtet, um einen Administrator zu benachrichtigen, wenn die Verfügbarkeit unter ein bestimmtes Niveau sinkt. Aus der Anzeige dieser Daten ergibt sich als möglicher Untersuchungsbereich der unter 100 % liegende Erfolgsprozentsatz des Tabellendiensts. (Weitere Informationen finden Sie im Abschnitt "[Metriken zeigen niedrigen PercentSuccess an, oder Vorgänge in Analyse-Protokolleinträgen haben den Transaktionsstatus 'ClientOtherErrors']".)

![][2]

*Abbildung 2: Anzeige von Speichermetriken im Portal*

Sie sollten Ihre Azure-Anwendungen kontinuierlich überwachen, um sicherzustellen, dass sie wie erwartet integer und leistungsfähig sind, durch:

- Festlegung einiger Baseline-Metriken für die Anwendung, mit denen Sie die aktuellen Daten vergleichen und alle wesentlichen Änderungen im Verhalten des Azure-Speichers und Ihrer Anwendung identifizieren können. Die Werte Ihrer Baseline-Metriken werden in vielen Fällen anwendungsspezifisch sein und sollten von Ihnen bei der Durchführung der Leistungstest für Ihre Anwendung festgelegt werden.
- Aufzeichnung von Minutenmetriken und ihre Verwendung zur aktiven Überwachung unerwarteter Fehler und Anomalien wie Spitzen in Fehlerzahlen oder Anfrageraten. 
- Aufzeichnung von Stundenmetriken und ihre Verwendung zur Überwachung von Durchschnittswerten wie durchschnittliche Fehlerzahlen und Anfrageraten. 
- Untersuchung potentieller Probleme unter Verwendung von Diagnosetools, entsprechend der Darstellung weiter unten im Abschnitt "[Diagnose von Speicherproblemen]."

Die Diagramme in Abbildung 3 unten illustrieren, wie die Durchschnittsberechnung für die Stundenmetrik Aktivitätsspitzen verstecken kann. Die Stundenmetriken scheinen eine konstante Anfragerate anzuzeigen, während die Minutenmetriken die wirklichen Schwankungen offenbaren.

![][3]

Im verbliebenen Teil dieses Abschnitts wird beschrieben, welche Metriken Sie überwachen sollten und warum.

### <a name="monitoring-service-health"></a>Überwachung der Dienstintegrität

Sie können das Microsoft Azure-Portal auf <a href="https://portal.azure.com" target="_blank">https://portal.azure.com</a> verwenden, um die Integrität des Speicherdiensts (und anderer Azure-Dienste) in allen Azure-Regionen der Welt anzuzeigen. So können Sie sofort sehen, ob ein Problem außerhalb Ihrer Kontrolle den Speicherdienst in der Region, in der Sie Ihre Anwendung verwenden, beeinträchtigt.

Das Azure-Portal kann auch Benachrichtigungen zu Vorfällen bereitstellen, die die verschiedenen Azure-Dienste beeinträchtigen. Hinweis: Diese Informationen waren bisher, zusammen mit Verlaufsdaten, auf dem Azure-Dienstdashboard unter <a href="http://status.azure.com" target="_blank">http://status.azure.com</a> verfügbar.

Während das Portal Zustandsinformationen innerhalb der Azure-Rechenzentren (Inside-out-Überwachung) sammelt, könnten Sie auch einen Outside-in-Ansatz in Erwägung ziehen, um synthetische Transaktionen zu generieren, die in regelmäßigen Abständen von mehreren Standorten aus auf Ihre Azure-gehostete Webanwendung zugreifen. Die von <a href="http://www.keynote.com/solutions/monitoring/web-monitoring" target="_blank">Keynote</a>, <a href="https://www.gomeznetworks.com/?g=1" target="_blank">Gomez</a> und Application Insights für Visual Studio Online angebotenen Dienste sind Beispiele für diesen Outside-in-Ansatz. Weitere Informationen über Application Insights für Visual Studio Online finden Sie in "[Anhang 5: Überwachung mit Application Insights für Visual Studio Online].“

### <a name="monitoring-capacity"></a>Kapazitätsüberwachung

Speichermetriken speichern nur Kapazitätsmetriken für den Blob-Dienst, weil Blobs in der Regel den größten Teil der gespeicherten Daten ausmachen. (Es ist zum Redaktionszeitpunkt nicht möglich, Speichermetriken zu verwenden, um die Kapazität Ihrer Tabellen und Warteschlangen zu überwachen.) Sie finden diese Daten in der Tabelle **$MetricsCapacityBlob**, wenn Sie die Überwachung für den Blob-Dienst aktiviert haben. Speichermetriken zeichnen diese Daten einmal täglich auf, und Sie können den Wert des **RowKey** verwenden, um zu bestimmen, ob die Zeile eine Entität enthält, die sich auf Benutzerdaten (Wert **data**) oder Analysedaten (Wert **analytics**) bezieht. Jede gespeicherte Entität enthält Informationen zum verwendeten Speicheranteil (**Capacity**, gemessen in Byte) und die im Speicherkonto verwendete aktuelle Containerzahl (**ContainerCount**) sowie Blobs (**ObjectCount**). Weitere Informationen über die in der Tabelle **$MetricsCapacityBlob** gespeicherten Kapazitätsmetriken finden Sie unter <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Tabellenschema der Speicher-Analytikmetriken</a> in MSDN.

> [AZURE.NOTE]Sie sollten diese Werte überwachen, um früh zu erkennen, dass Sie sich den Kapazitätsgrenzen Ihres Speicherkontos nähern. Auf der Seite **Überwachung** für Ihr Speicherkonto im Azure-Portal können Sie Warnregeln hinzufügen, um benachrichtigt zu werden, wenn die Gesamtspeichernutzung die von Ihnen festgelegten Grenzwerte über- oder unterschreitet.

Hilfe für die Schätzung der Größe der verschiedenen Speicherobjekte wie Blobs finden Sie im Blogbeitrag <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx" target="_blank">Windows Azure-Speicherabrechnung verstehen – Bandbreite, Transaktionen und Kapazität</a>.

### <a name="monitoring-availability"></a>Verfügbarkeitsüberwachung

Sie sollten die Verfügbarkeit des Speicherdiensts in Ihrem Speicherkonto überwachen, indem Sie den Wert in der Spalte **Availability** in den Stunden- oder Minutenmetriken-Tabellen überwachen – **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Die Spalte **Availability** enthält einen Prozentwert, der die Verfügbarkeit des Diensts oder der API-Operation in der jeweiligen Zeile darstellt. (Der **RowKey** zeigt an, ob die Zeile Metriken für den Dienst insgesamt oder für eine bestimmte API-Operation enthält.)

Jeder unter 100 % liegende Wert zeigt an, dass einige Speicheranfragen fehlschlagen. Sie können sehen, warum sie fehlschlagen, indem Sie die anderen Spalten in den Metrikdaten untersuchen, in denen die Anzahl der Anfragen nach verschiedenen Fehlerarten angezeigt wird, wie **ServerTimeoutError**. Sie müssen mit dem vorübergehenden Absinken der **Verfügbarkeit** unter 100 % aufgrund vorübergehender Server-Timeouts rechnen, wenn der Server Partitionen bewegt, um eine bessere Lastverteilung der Anfragen durchzuführen. Die Wiederholungslogik in Ihrer Clientanwendung sollte solche intermittierenden Bedingungen handhaben. Die Seite <a href="http://msdn.microsoft.com/library/azure/hh343260.aspx" target="_blank"></a> führt die Transaktionsarten auf, welche die Speichermetriken in ihrer Berechnung der **Verfügbarkeit** beinhalten.

Auf der Seite **Überwachung** für Ihr Speicherkonto im Azure-Portal können Sie Warnregeln hinzufügen, um benachrichtigt zu werden, wenn die **Verfügbarkeit** den von Ihnen festgelegten Grenzwert unterschreitet.

Der Abschnitt "[Anleitungen zur Problembehandlung]" in dieser Anleitung beschreibt einige bekannte Speicherdienstprobleme in Verbindung mit der Verfügbarkeit.

### <a name="monitoring-performance"></a>Leistungsüberwachung

Um die Leistung des Speicherdiensts zu überwachen, können Sie die folgenden Metriken aus den Stunden- und Minuten-Metriktabellen verwenden.

- Die Werte in **AverageE2ELatency** und **AverageServerLatency** zeigen die vom Speicherdienst oder der API-Operationsart benötigte Durchschnittszeit für die Anfragebearbeitung an. **AverageE2ELatency** ist eine Maßzahl für die durchgehende Latenz, die die benötigte Zeit für das Lesen der Anfrage und die Antwortversendung zusätzlich zu der für die Anfragebearbeitung benötigten Zeit beinhaltet (beinhaltet daher Netzwerklatenz, sobald die Anfrage den Speicherdienst erreicht). **AverageServerLatency** ist eine Maßzahl allein für die Bearbeitungszeit und schließt daher jegliche mit der Kommunikation mit dem Client verbundene Netzwerklatenz aus. Siehe den Abschnitt "[Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an]" weiter unten in dieser Anleitung, wo die Unterschiede zwischen diesen beiden Werten erörtert werden.
- Die Werte in den Spalten **TotalIngress** und **TotalEgress** zeigen die Gesamtzahl der Daten in Byte an, die bei Ihrem Speicherdienst eingehen oder von ihm ausgehen bzw. über eine bestimmte API-Operationsart eingehen.
- Die Werte in der Spalte **TotalRequests** zeigen die Gesamtzahl der Anfragen an, die beim Speicherdienst der API-Operation eingehen. **TotalRequests** ist die Gesamtzahl der Anfragen, die beim Speicherdienst eingehen. 

In der Regel werden Sie unerwartete Veränderungen bei einem dieser Werte überwachen. Sie sind der Indikator für ein Problem, das untersucht werden muss.

Auf der Seite **Überwachung** für Ihr Speicherkonto im Azure-Portal können Sie Warnregeln hinzufügen, um benachrichtigt zu werden, wenn die Leistungsmetriken für diesen Dienst einen von Ihnen festgelegten Grenzwert über- oder unterschreiten.

Der Abschnitt "[Anleitungen zur Problembehandlung]" in dieser Anleitung beschreibt einige bekannte Speicherdienstprobleme in Verbindung mit der Verfügbarkeit.


## <a name="diagnosing-storage-issues"></a>Diagnose von Speicherproblemen

Es gibt verschiedene Möglichkeiten, um Probleme in Ihrer Anwendung zu erkennen. Dazu gehören:

- Ein Hauptfehler, der zum Absturz oder Ausfall der Anwendung führt
- Wesentliche Änderungen gegenüber den Ausgangswerten in den von Ihnen überwachten Metriken, entsprechend der Beschreibung im vorhergegangenen Abschnitt "[Überwachung Ihres Speicherdiensts]" 
- Berichte von Anwendungsbenutzern, dass bestimmte Vorgänge nicht wie erwartet abgeschlossen wurden oder dass einige Funktionen nicht funktionieren
- In Ihrer Anwendung erzeugte Fehler, die in Protokollierungsdateien oder durch eine andere Benachrichtigungsmethode angezeigt werden

In der Regel fallen Probleme zu Azure-Speicherdiensten unter eine von vier weit gefassten Kategorien:

- Ihre Anwendung hat ein Leistungsproblem gemeldet, das entweder von Ihren Benutzern gemeldet oder durch Änderungen in den Leistungskennzahlen angezeigt wurde.
- Es gibt ein Problem mit der Azure-Speicher-Infrastruktur in einer oder mehrerer Regionen. 
- In Ihrer Anwendung ist ein Fehler aufgetreten, der entweder von Ihren Benutzern gemeldet oder durch einen Anstieg in einer der von Ihnen überwachten Fehlerzahl-Metriken aufgezeigt wurde.
- Während der Entwicklung und Tests können Sie den lokalen Speicheremulator verwenden; es können einige speziell mit der Speicheremulator-Nutzung verbundene Probleme auftreten.

Die folgenden Abschnitte beschreiben die Schritte, die Sie befolgen sollten, um Probleme in jeder dieser vier Kategorien zu diagnostizieren und zu beheben. Der unten in dieser Anleitung folgende Abschnitt "[Anleitungen zur Problembehandlung]" liefert weitere Informationen zu einigen bekannten Problemen, die auftreten können.

### <a name="service-health-issues"></a>Probleme mit der Dienstintegrität

Probleme mit der Dienstintegrität liegen in der Regel außerhalb Ihrer Kontrolle. Das Azure-Portal bietet Informationen zu allen auftretenden Problemen mit Azure-Diensten, inklusive Speicherdiensten. Falls Sie sich für georedundante Speicher mit Lesezugriff entschieden haben, als Sie Ihr Speicherkonto erstellt haben, dann sollte Ihre Anwendung im Falle der Nichtverfügbarkeit Ihrer Daten am Primärort vorübergehend auf die schreibgeschützte Kopie im Sekundärort umschalten. Hierzu muss die Anwendung in der Lage sein, zwischen der Verwendung der primären und sekundären Speicherorte zu wechseln und in einem Modus mit eingeschränkter Funktionalität mit schreibgeschützten Daten zu arbeiten. Mit den Azure Speicher-Clientbibliotheken können Sie eine Wiederholungsrichtlinie für das Lesen aus dem Sekundärspeicher definieren, falls der Primärort ausfällt. Ihre Anwendung muss auch erkennen können, ob die Daten am Sekundärort konsistent sind. Weitere Informationen finden Sie im Blogbeitrag <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx" target="_blank">Windows Azure-Speicher: Redundanzoptionen und Lesezugriff georedundanter Speicher</a>.

### <a name="performance-issues"></a>Leistungsprobleme

Die Leistungsfähigkeit einer Anwendung kann subjektiv sein, vor allem aus der Benutzerperspektive. Daher ist es wichtig, Baseline-Metriken zur Verfügung zu haben, um Leistungsprobleme identifizieren zu können. Viele Faktoren können die Leistung eines Azure-Speicherdiensts aus der Client-Anwendungs-Perspektive beeinflussen. Die Ursachen für Leistungsprobleme können im Speicherdienst, im Client oder in der Netzwerkinfrastruktur liegen, daher ist es wichtig, über eine Strategie für ihre Identifizierung zu verfügen.

Nachdem Sie anhand der Messdaten ermittelt haben, wo die mögliche Ursache des Leistungsproblems liegt, können Sie anschließend die Protokollierungsdateien verwenden, um detaillierte Informationen zur Diagnose und weiteren Problembehandlung zu erhalten.

Die "[Anleitungen zur Problembehandlung]" weiter unten liefern weitere Informationen zu einigen bekannten leistungsbezogenen Problemen, die auftreten können.

### <a name="diagnosing-errors"></a>Fehlerdiagnose

Benutzer Ihrer Anwendung können Sie über Fehler informieren, die von der Clientanwendung gemeldet werden. Speichermetriken verzeichnen auch die Anzahl der verschiedenen Fehlerarten in Ihren Speicherdiensten, wie **NetworkError**, **ClientTimeoutError** oder **AuthorizationError**. Während Speichermetriken nur die Anzahl der verschiedenen Fehlerarten verzeichnen, können Sie durch die Untersuchung von Serverseite, Clientseite und Netzwerkprotokollen weitere Informationen zu Einzelanfragen erhalten. In der Regel liefert der vom Speicherdienst zurückgegebene HTTP-Statuscode einen Hinweis auf den Grund für das Scheitern der Anfrage.

> [AZURE.NOTE]Denken Sie daran, dass Sie mit einigen intermittierenden Fehlern rechnen müssen, beispielsweise Fehler aufgrund von vorübergehenden Netzwerkbedingungen oder Anwendungsfehler.

Die folgenden Ressourcen in MSDN sind nützlich für das Verständnis von speicherbezogenem Status und Fehlercodes:

- <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Allgemeine REST API-Fehlercodes</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179439.aspx" target="_blank">Blob-Dienst-Fehlercodes</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179446.aspx" target="_blank">Warteschlangendienst-Fehlercodes</a>
- <a href="http://msdn.microsoft.com/library/azure/dd179438.aspx" target="_blank">Tabellendienst-Fehlercodes</a>

### <a name="storage-emulator-issues"></a>Probleme mit dem Speicheremulator

Azure SDK enthält einen Speicheremulator, den Sie auf einer Entwicklungsworkstation ausführen können. Dieser Emulator simuliert die meisten Verhaltensweisen des Azure-Speicherdiensts und ist nützlich bei Entwicklung und Test. Mit ihm können Sie Anwendungen, die Azure-Speicherdienste verwenden, ohne Azure-Abonnement und ohne Azure-Speicherkonto ausführen.

Der Abschnitt "[Anleitungen zur Problembehandlung]" in dieser Anleitung beschreibt einige bekannte Speicherdienstprobleme, die bei der Verwendung des Speicheremulators aufgetreten sind.

### <a name="storage-logging-tools"></a>Speicher-Protokollierungstools

Speicherprotokollierung stellt serverseitige Protokollierung von Speicheranfragen in Ihrem Azure-Speicherkonto bereit. Weitere Informationen zu Aktivierung serverseitiger Protokollierung und Zugriff auf die Protokollierungsdaten finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=510867" target="_blank">Verwenden von serverseitiger Protokollierungsaktivierung</a> in MSDN.

Mit der Speicher-Clientbibliothek für .NET können Sie clientseitige Protokollierungsdaten sammeln, die sich auf Speicheroperationen in Ihrer Anwendung beziehen. Weitere Informationen zu Aktivierung clientseitiger Protokollierung und Zugriff auf die Protokollierungsdaten finden Sie unter <a href="http://go.microsoft.com/fwlink/?LinkId=510868" target="_blank">Clientseitige Protokollierung mit der .NET-Speicherclientbibliothek</a> in MSDN.

> [AZURE.NOTE]In einigen Fällen (wie SAS-Authentifizierungsfehler) kann ein Benutzer einen Fehler melden, für den Sie keine Anfragedaten in den serverseitigen Speicherprotokollen finden. Sie können die Protokollierungsfunktionen der Speicher-Clientbibliothek verwenden, um zu untersuchen, ob die Ursache des Problems im Client liegt, oder Überwachungstools verwenden, um das Netzwerk zu untersuchen.

### <a name="using-network-logging-tools"></a>Verwenden von Netzwerk-Protokollierungstools

Sie können den Verkehr zwischen Client und Server erfassen, um detaillierte Informationen über die Daten, die Client und Server austauschen, und die zugrunde liegenden Netzwerkbedingungen bereitzustellen. Nützliche Netzwerkprotokollierungstools sind:

- Fiddler (<a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a>) ist ein kostenloser Web Debugging Proxy, mit dem Sie die Header und Nutzlastdaten von HTTP- und HTTPS-Anfrage- und Antwortnachrichten untersuchen können. Weitere Informationen finden Sie unter [Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr].
- Microsoft Network Monitor (Netmon) (<a href="http://www.microsoft.com/download/details.aspx?id=4865" target="_blank">http://www.microsoft.com/de-DE/download/details.aspx?id=4865</a>) und Wireshark (<a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a>) sind kostenlose Netzwerkprotokollanalysatoren, mit denen Sie detaillierte Paketinformationen für eine Vielzahl von Netzwerkprotokollen anzeigen können. Weitere Informationen zu Wireshark finden Sie unter [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr].
- Microsoft Message Analyzer ist ein Tool von Microsoft, das Netmon ersetzt und Sie zusätzlich zur Erfassung von Netzwerkpaketdaten bei der Anzeige und Analyse von Protokollierungsdaten unterstützt, die von anderen Tools erfasst werden. Weitere Informationen finden Sie unter [Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr].
- Wenn Sie einen Basis-Konnektivitätstest durchführen möchten, um zu überprüfen, ob sich der Client-Computer über das Netzwerk mit dem Azure-Speicherdienst verbinden kann, können Sie dazu nicht das standardmäßige **Ping**-Tool auf dem Client verwenden. Dennoch können Sie das Tool **TCPing** zur Überprüfung der Konnektivität verwenden. **TCPing** steht zum Download unter <a href="http://www.elifulkerson.com/projects/tcping.php" target="_blank">http://www.elifulkerson.com/projects/tcping.php</a> zur Verfügung.

In vielen Fällen reichen die Protokollierungsdaten aus der Speicherprotokollierung und der Speicher-Clientbibliothek aus, um ein Problem zu diagnostizieren, aber in einigen Szenarien können detailliertere Informationen benötigt werden, als diese Netzwerkprotokollierungstools bereitstellen können. Beispielsweise können Sie Fiddler zur Anzeige von HTTP- und HTTPS-Nachrichten verwenden. Anhand der Header- und Nutzlastdaten, die an die und von den Speicherdiensten gesendet werden, können Sie untersuchen, wie eine Clientanwendung Speicheroperationen wiederholt. Protokollierungsanalysatoren wie Wireshark operieren auf Paketebene und erlauben Ihnen dadurch die Anzeige von TCP-Daten, mit denen Sie Probleme bezüglich verlorener Pakete und Konnektivität beheben können. Message Analyzer kann sowohl auf HTTP- und TCP-Schichten ausgeführt werden.

## <a name="end-to-end-tracing"></a>End-to-End-Ablaufverfolgung

Durchgängige Verfolgung unter Verwendung einer Vielzahl von Protokollierungsdateien ist eine nützliche Technik für die Untersuchung möglicher Probleme. Sie können die Datums-/Zeitinformationen aus Ihren Metrikdaten als Hinweis darauf verwenden, wo Sie in den Protokollierungsdateien nach den detaillierteren Informationen suchen müssen, die Ihnen bei der Fehlerbehebung helfen.
	
### <a name="correlating-log-data"></a>Korrelation von Protokollierungsdaten

Bei der Anzeige von Protokollen aus Clientanwendungen, Netzwerkverfolgungen und serverseitiger Speicherprotokollierung ist es entscheidend, Anfragen über die verschiedenen Protokollierungsdateien korrelieren zu können. Die Protokollierungsdateien enthalten eine Reihe von unterschiedlichen Feldern, die als Korrelationskennungen von Bedeutung sind. Die Clientanfrage-ID ist das nützlichste Feld, das für die Korrelation von Einträgen in den verschiedenen Protokollierungen zu verwenden ist. Dennoch kann es manchmal nützlich sein, entweder die Serveranfrage-ID oder Zeitstempel zu verwenden. Die folgenden Abschnitte stellen weitere Informationen zu diesen Optionen bereit.

### <a name="client-request-id"></a>Clientanfrage-ID

Die Speicher-Clientbibliothek erzeugt automatisch eine eindeutige Clientanfrage-ID für jede Anfrage.

- Bei der clientseitigen Protokollierung, die die Speicher-Clientbibliothek erstellt, wird die Clientanfrage-ID im Feld **Client Request ID** in jedem auf die Anfrage bezogenen Protokolleintrag angezeigt.
- In einer Netzwerkverfolgung wie mit Fiddler wird die Clientanfrage-ID in Anforderungsnachrichten wie dem HTTP-Headerwert **x-ms-client-request-id** angezeigt.
- Bei der serverseitigen Speicherprotokollierung wird die Clientanfrage-ID in der Spalte "Client request ID“ angezeigt.

> [AZURE.NOTE]Für mehrere Anfragen ist es möglich, die gleiche Clientanfrage-ID zu teilen, weil der Client diesen Wert zuteilen kann (obwohl die Speicher-Clientbibliothek automatisch einenneuen Wert zuteilt). Bei Wiederholungsversuchen seitens des Client teilen alle Versuche die gleiche Clientanfrage-ID. Bei einem vom Client gesendeten Batch verfügt der Batch über eine eigene Clientanfrage-ID.


### <a name="server-request-id"></a>Serveranfrage-ID

Der Speicherdienst generiert automatisch Serveranfrage-IDs.

- Bei der serverseitigen Speicherprotokollierung wird die Serveranfrage-ID in der Spalte **Request ID header** angezeigt.
- In einer Netzwerkverfolgung wie mit Fiddler wird die Serveranfrage-ID in Antwortnachrichten wie dem HTTP-Headerwert **x-ms-request-id** angezeigt.
- Bei der clientseitigen Protokollierung, die die Speicher-Clientbibliothek erstellt, wird die Clientanfrage-ID in der Spalte **Operation Text** für die Serverantwort angezeigt.

> [AZURE.NOTE]Der Speicherdienst teilt jeder empfangenen Anfrage immer eine eindeutige Serveranfrage-ID zu, sodass jeder Wiederholungsversuch vom Client und jeder in einem Batch enthaltene Vorgang eine eindeutige Serveranfrage-ID hat.

Wenn die Speicher-Clientbibliothek eine **StorageException** (Speicherausnahme) an den Client ausgibt, enthält die Eigenschaft **RequestInformation** (Anfrageinformation) das Objekt **RequestResult** (Anfrageergebnis), das die Eigenschaft **ServiceRequestID** (Dienstanfrage-ID) enthält. Sie können auf ein **RequestResult**-Objekt auch von einer **OperationContext**-Instanz aus zugreifen.

Das Codebeispiel unten zeigt, wie ein benutzerdefinierter Wert **ClientRequestId** eingestellt wird, indem ein **OperationContext**-Objekt mit dem Speicherdienst verknüpft wird. Es zeigt auch, wie der Wert **ServerRequestId** aus der Antwortnachricht abgerufen werden kann.

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(CloudConfigurationManager.GetSetting("StorageConnectionString"));
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid. 
    OperationContext oc = new OperationContext();
    oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

    try
    {
        CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
        ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
        var downloadToPath = string.Format("./{0}", blob.Name);
        using (var fs = File.OpenWrite(downloadToPath))
        {
            blob.DownloadToStream(fs, null, null, oc);
            Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
        }
    }
    catch (StorageException storageException)
    {
        Console.WriteLine("Storage exception {0} occurred", storageException.Message); 
        // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
        foreach (var result in oc.RequestResults)
        {
                Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID); 
        }
    }


### <a name="timestamps"></a>Zeitstempel

Sie können auch Zeitstempel verwenden, um verbundene Protokolleinträge zu finden, aber achten Sie auf eventuellen Zeitversatz zwischen Client und Server. Sie sollten etwa 15 Minuten nach passenden serverseitigen Einträgen suchen, die auf dem Zeitstempel auf dem Client basieren. Denken Sie daran, dass die Blob-Metadaten für die Metriken enthaltenden Blobs den Zeitraum für die im Blob gespeicherten Metriken anzeigen. Dies ist nützlich, wenn Sie viele Metrik-Blobs für die gleiche Minute oder Stunde haben.

## <a name="troubleshooting-guidance"></a>Anleitungen zur Problembehandlung

Dieser Abschnitt wird Sie bei der Diagnose und Behandlung einiger bekannter Probleme unterstützen, die auftreten können, wenn Ihre Anwendung Azure-Speicherdienste verwendet. In der Liste unten finden Sie die für Ihre spezifische Fragestellung relevanten Informationen.

**Entscheidungsbaum für die Problembehandlung**

----------

Bezieht sich Ihr Problem auf die Leistungsfähigkeit eines Speicherdiensts?

- [Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an]
- [Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz]
- [Metriken zeigen hohe AverageServerLatency an]
- [Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange]

----------

Bezieht sich Ihr Problem auf die Verfügbarkeit eines Speicherdiensts?

- [Metriken zeigen Anstieg bei PercentThrottlingError an]
- [Metriken zeigen Anstieg bei PercentTimeoutError an]
- [Metriken zeigen Anstieg bei PercentNetworkError an]

----------

Empfängt Ihre Clientanwendung eine HTTP 4XX-Antwort (beispielsweise 404) von einem Speicherdienst?

- [Der Client empfängt HTTP 403 (Verboten)-Meldungen]
- [Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen]
- [Der Client empfängt HTTP 409 (Konflikt)-Meldungen]

----------

[Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"]

----------

[Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an]

----------

[Sie stoßen auf unerwartete Neustarts von virtuellen Computern, die über eine große Anzahl angeschlossener virtueller VHDs verfügen]

----------

[Ihre Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test]

----------

[Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme]

----------

[Sie haben ein anderes Problem mit einem Speicherdienst]

----------

### <a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an

Die Abbildung unten aus dem Überwachungstool zeigt ein Beispiel, in dem die **AverageE2ELatency** wesentlich höher als die **AverageServerLatency** ist.

![][4]

Beachten Sie, dass der Speicherdienst nur die Metrik **AverageE2ELatency** für erfolgreiche Anfragen berechnet und, im Gegensatz zur **AverageServerLatency**, die vom Client benötigte Zeit für die Datenversendung und die Empfangsbestätigung vom Speicherdienst enthält. Daher könnte ein Unterschied zwischen **AverageE2ELatency** und **AverageServerLatency** entweder durch eine langsame Antwort der Clientanwendung oder durch das Netzwerk bedingt sein.

> [AZURE.NOTE]Sie können auch **E2ELatency** und **ServerLatency** für einzelne Speichervorgänge in den Speicherprotokollierungsdaten anzeigen.

#### Untersuchung von Clientleistungsproblemen

Möglicher Grund für eine langsame Clientreaktion ist z. B. eine begrenzte Anzahl verfügbarer Verbindungen oder Threads. Möglicherweise können Sie das Problem lösen, indem Sie den Clientcode zugunsten einer höheren Effizienz ändern (z. B. durch Verwendung von asynchronen Aufrufen an den Speicherdienst) oder indem Sie einen größeren virtuellen Computer (mit mehr Kernen und mehr Arbeitsspeicher) verwenden.

Für den Tabellen- und Warteschlangendienst kann der Nagle-Algorithmus auch höhere **AverageE2ELatency** verglichen mit **AverageServerLatency** verursachen: Weitere Informationen finden Sie im Eintrag <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx" target="_blank">Nagle-Algorithmus geht nicht freundlich mit kleinen Anfragen um</a> im Blog des Microsoft Azure-Speicherteams. Sie können den Nagle-Algorithmus im Code deaktivieren, indem Sie die Klasse **ServicePointManager** im **System.Net**-Namespace verwenden. Sie sollten dies tun, bevor Sie Aufrufe an die Tabellen- oder Warteschlangendienste in Ihrer Anwendung senden, da dies keinen Einfluss auf die bereits offenen Verbindungen hat. Das folgende Beispiel stammt aus der Methode **Application\_Start** in einer Workerrolle.

    var storageAccount = CloudStorageAccount.Parse(connStr);
    ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
    tableServicePoint.UseNagleAlgorithm = false;
    ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
    queueServicePoint.UseNagleAlgorithm = false;

Sie sollten die clientseitigen Protokollierungen überprüfen, um zu sehen, wie viele Anfragen Ihre Clientanwendung übermittelt, und allgemeine .NET- bezogene Leistungsengpässe in Ihrem Client wie CPU, .NET Garbage Collection, Netzwerkauslastung oder Speicher überprüfen (als Ausgangspunkt für die Fehlerbehandlung bei .NET-Clientanwendungen siehe <a href="http://msdn.microsoft.com/library/7fe0dd2y(v=vs.110).aspx" target="_blank">Debuggen, Ablaufverfolgung und Profilerstellung</a> in MSDN).

#### Untersuchung von Netzwerklatenzproblemen

In der Regel wird eine hohe durchgängige Latenz durch die Übermittlungsbedingungen im Netzwerk verursacht. Sie können sowohl vorübergehende als auch permanente Netzwerkprobleme wie gedroppte Pakete mithilfe von Tools wie Wireshark oder Microsoft Message Analyzer untersuchen.

Weitere Informationen zu Wireshark zur Behandlung von Netzwerkproblemen finden Sie unter [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr].

Weitere Informationen zur Verwendung von Microsoft Message Analyzer zur Behandlung von Netzwerkproblemen finden Sie unter [Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr].

### <a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz

In diesem Szenario ist ein verzögertes Erreichen des Speicherdiensts durch die Speicheranfragen die wahrscheinlichste Ursache. Sie sollten untersuchen, warum Clientanfragen den Blob-Dienst nicht erreichen.

Möglicher Grund für das verzögerte Senden von Anfragen durch den Client ist z. B. eine begrenzte Anzahl verfügbarer Verbindungen oder Threads. Sie sollten auch prüfen, ob der Client mehrere Wiederholungen ausführt, und den Grund untersuchen, falls dies der Fall ist. Sie können dies systematisch tun, indem Sie das mit der Anfrage verbundene Objekt **OperationContext** anzeigen und den Wert **ServerRequestId** abrufen. Weitere Information finden Sie im Codebeispiel im Abschnitt "[Serveranfrage-ID]".

Wenn das Problem nicht beim Client liegt, sollten Sie potenzielle Netzwerkprobleme wie Paketverlust untersuchen. Sie können Tools wie Wireshark oder Microsoft Message Analyzer verwenden, um Netzwerkprobleme zu untersuchen.

Weitere Informationen zu Wireshark zur Behandlung von Netzwerkproblemen finden Sie unter [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr].

Weitere Informationen zur Verwendung von Microsoft Message Analyzer zur Behandlung von Netzwerkproblemen finden Sie unter [Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr].

### <a name="metrics-show-high-AverageServerLatency"></a>Metriken zeigen hohe AverageServerLatency an

Im Fall von hoher **AverageServerLatency** bei Blob-Download-Anfragen sollten Sie die Speicherprotokollierungen verwenden, um herauszufinden, ob wiederholte Anfragen für den gleichen Blob (oder die gleiche Blob-Gruppe) vorliegen. In Bezug auf Blob-Upload-Anfragen sollten Sie untersuchen, welche Blockgröße der Client verwendet (zum Beispiel können Blöcke mit weniger als 64 K zu Overheads führen, wenn Lesevorgänge auch in Blöcken von weniger als 64 K erfolgen) und ob mehrere Clients parallel Blöcke auf den gleichen Blob hochladen. Sie sollten auch die Pro-Minute-Metriken für Anfrageanzahlspitzen überprüfen, die zu einer Überschreitung der Pro-Sekunde-Skalierbarkeitsziele führen "[Metriken zeigen Anstieg bei PercentTimeoutError an]".

Im Falle von hoher **AverageServerLatency** bei wiederholten Blob-Download-Anfragen für den gleichen Blob oder die gleiche Blob-Gruppe sollten Sie ein Cashen dieser Blobs in Betracht ziehen. Dazu können Sie Azure Cache oder dem Azure Content Delivery Network (CDN) verwenden. In Bezug auf Upload-Anfragen können Sie den Durchlauf durch Verwendung einer größeren Blockgröße verbessern. Für Abfragen in Tabellen kann auch clientseitiges Caching auf Clients implementiert werden, die die gleichen Abfrageoperationen durchführen und deren Daten sich nicht häufig ändern.

Hohe Werte bei der **AverageServerLatency** können auch ein Zeichen für schlecht entworfene Tabellen oder Abfragen sein, die zu Scanoperationen führen oder die dem angehängten/vorangestellten Gegenmuster folgen. Weitere Informationen finden Sie unter "[Metriken zeigen Anstieg bei PercentThrottlingError an]".

> [AZURE.NOTE]Hier finden Sie eine umfassende Checkliste mit weiteren Problemstellungen, die Sie kennen sollten: ”Entwerfen einer skalierbaren und leistungsfähigen speicherbasierten Anwendungscheckliste.”

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange

Wenn Sie eine Verszögerung zwischen dem Zeitpunkt feststellen, in dem eine Anwendung eine Nachricht in eine Warteschlange einfügt, und dem Zeitpunkt, an dem sie zur Verfügung steht, um aus der Warteschlage gelesen zu werden, sollten Sie die folgenden Schritte ausführen, um das Problem zu diagnostizieren:

- Stellen Sie sicher, dass die Anwendung die Nachrichten erfolgreich in die Warteschlange einfügt. Überprüfen Sie, ob die Anwendung die Methode **AddMessage** nicht mehrmals wiederholt, bevor sie zum Erfolg führt. Die Speicher-Clientbibliothek zeigt alle wiederholten Versuche der Speicheroperationen an.
- Stellen Sie sicher, dass kein Zeitunterschied zwischen der Workerrolle, die die Nachricht in die Warteschlange einfügt, und der Workerrolle, die die Nachricht aus der Warteschlange liest, besteht, der zum Eindruck einer Bearbeitungsverzögerung führt.
- Prüfen Sie, ob der Fehler bei der Workerrolle liegt, die die Nachrichten aus der Warteschlange liest. Wenn ein Warteschlangenclient die Methode **GetMessage** aufruft, aber nicht mit einer Bestätigung antwortet, bleibt die Nachricht in der Warteschlange unsichtbar, bis der Zeitraum **invisibilityTimeout** abläuft. An diesem Punkt steht die Nachricht wieder zur Verarbeitung zur Verfügung.
- Prüfen Sie, ob die Länge der Warteschlange im Laufe der Zeit wächst. Dies kann auftreten, wenn Sie nicht über genügend Worker verfügen, um alle Nachrichten zu verarbeiten, die andere Worker in der Warteschlange platzieren. Sie sollten auch die Metriken überprüfen, um festzustellen, ob Löschanfragen scheitern, sowie die Anzahl der Nachrichten-Freigabeanforderungen, was wiederholt gescheiterte Nachrichtenlöschversuche bedeuten könnte.
- Untersuchen Sie die Speicher-Protokollierungen auf alle Warteschlangenoperationen, die über höhere Werte für **E2ELatency** und **ServerLatency** als erwartet über einen längeren Zeitraum als üblich verfügen.


### <a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metriken zeigen Anstieg bei PercentThrottlingError an

Drosselungsfehler treten auf, wenn Sie die Skalierbarkeitsziele eines Speicherdiensts überschreiten. Der Speicherdienst tut dies, um sicherzustellen, dass kein Einzelclient oder Mandant diesen Dienst auf Kosten anderer verwenden kann. Weitere Informationen zu Skalierbarkeitszielen für Speicherkonten und Leistungszielen für Partitionen in Speicherkonten finden Sie unter <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Ziele für Skalierbarkeit und Leistung des Azure-Speichers</a>.

Wenn die Metrik **PercentThrottlingError** einen Anstieg des Prozentsatzes der Anfragen anzeigt, die mit einem Drosselungsfehler scheitern, müssen Sie eines der folgenden beiden Szenarien untersuchen:

- [Vorübergehender Anstieg bei PercentThrottlingError]
- [Permanenter Anstieg bei PercentThrottlingError]

Ein Anstieg bei **PercentThrottlingError** erfolgt oft gleichzeitig mit einem Anstieg der Anzahl an Speicheranfragen oder tritt auf, wenn Sie Ihre Anwendung zu Beginn Lasttests unterziehen. Dies kann sich auch im Client als HTTP-Status-Meldungen "503 Server Busy" oder "500 Operation Timeout" bei Speicheroperationen äußern.

#### <a name="transient-increase-in-PercentThrottlingError"></a>Vorübergehender Anstieg bei PercentThrottlingError

Im Fall der Übereinstimmung der Wertespitzen von **PercentThrottlingError** mit Zeiträumen hoher Aktivität für die Anwendung sollten Sie eine exponentielle (nicht lineare) Backoff-Strategie für Wiederholungen in Ihrem Client implementieren: Dies wird die sofortige Auslastung auf der Partition reduzieren und Ihre Anwendung beim Ausgleich von Verkehrsspitzen unterstützen. Weitere Informationen zur Implementierung von Wiederholungsrichtlinien unter Verwendung der Speicher-Clientbibliothek finden Sie unter <a href="http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.retrypolicies.aspx" target="_blank">Microsoft.WindowsAzure.Storage.RetryPolicies-Namespace</a> in MSDN.

> [AZURE.NOTE]Es können auch Spitzen in den Werten von **PercentThrottlingError** auftreten, die nicht mit Zeiträumen hoher Aktivität für die Anwendung übereinstimmen. Die wahrscheinlichste Ursache ist hier die Verschiebung von Partitionen durch den Speicherdienst zur Verbesserung des Lastausgleichs.

#### <a name="permanent-increase-in-PercentThrottlingError"></a>Permanenter Anstieg bei PercentThrottlingError

Wenn Sie einen gleichbleibend hohen Wert für **PercentThrottlingError** nach einer dauerhaften Steigerung Ihres Transaktionsvolumens feststellen oder wenn Sie Ihre Anwendung zu Beginn Lasttests unterziehen, müssen Sie bewerten, wie Ihre Anwendung Speicherpartitionen verwendet und ob sie sich an die Skalierbarkeitsziele für ein Speicherkonto annähert. Wenn Sie beispielsweise Drosselungsfehler in einer (als Einzelpartition zählende) Warteschlange sehen, sollten Sie die Verwendung zusätzlicher Warteschlangen zur Transaktionsverteilung über mehrere Partitionen in Erwägung ziehen. Wenn Sie Drosselungsfehler in einer Tabelle feststellen, müssen Sie die Verwendung eines anderen Partitionsschemas für die Verteilung Ihrer Transaktionen über mehrere Partitionen mithilfe einer breiteren Auswahl von Partitionsschlüsseln in Erwägung ziehen. Eine häufige Ursache für dieses Problem sind die vorangestellten/angehängten Gegenmuster, in denen Sie das Datum als Partitionsschlüssel auswählen und anschließend alle Daten an einem bestimmten Tag auf eine Partition geschrieben werden: Unter Last kann dies zu einem Schreibengpass führen. Sie sollten entweder ein anderes Partitionierungsmuster verwenden oder prüfen, ob Blob-Speicher die bessere Lösung wäre. Sie sollten auch prüfen, ob die Drosselung als Folge der Spitzen in Ihrem Verkehr auftreten und Möglichkeiten des Ausgleich Ihrer Anfragemuster untersuchen.

Wenn Sie Ihre Transaktionen über mehrere Partitionen verteilen, müssen Sie die für das Speicherkonto eingestellten Skalierbarkeitsgrenzen kennen. Wenn Sie beispielsweise zehn Warteschlangen verwenden, die jeweils maximal 2.000 1KB-Nachrichten pro Sekunde verarbeiten, erreichen Sie eine Gesamtgrenze von 20.000 Nachrichten pro Sekunde für das Speicherkonto. Wenn Sie mehr als 20.000 Einheiten pro Sekunde verarbeiten müssen, sollten Sie die Verwendung mehrerer Speicherkonten in Betracht ziehen. Sie sollten auch bedenken, dass die Größe Ihrer Anfragen und Einheiten einen Einfluss darauf hat, wann der Speicherdienst Ihre Clients drosselt: Größere Anfragen und Einheiten könnten früher gedrosselt werden.

Ineffiziente Abfragemuster können ebenfalls dazu führen, dass Sie an die Skalierbarkeitsgrenzen für Tabellenpartitionen stoßen. Zum Beispiel benötigt eine Abfrage mit einem Filter, der nur ein Prozent der Einheiten in einer Partition auswählt, aber alle Objekte in einer Partition scannt, Zugriff auf jede Entität. Jede Entitätsanzeige wird gegen die Gesamtzahl der Transaktionen in dieser Partition gezählt; daher können Sie die Skalierbarkeitsziele einfach erreichen.

> [AZURE.NOTE]Ihr Leistungstest sollte ineffiziente Abfragemuster in Ihrer Anwendung aufdecken.

### <a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metriken zeigen Anstieg bei PercentTimeoutError an

Ihre Metriken zeigen einen Anstieg bei **PercentTimeoutError** für einen Ihrer Speicherdienste an. Zugleich erhält der Client ein hohes Volumen an HTTP-Status-Meldungen "500 Operation Timeout" von Speicheroperationen.

> [AZURE.NOTE]Timeout-Fehler werden vorübergehend angezeigt, während der Speicherdienst Lasten durch Verschieben einer Partition zu einem neuen Server ausgleicht.
 
Die **ClientTimeoutError**-Metrik ist eine Aggregation der folgenden Metriken: **ClientTimeoutError**. **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** und **SASServerTimeoutError**.

Die Server-Timeouts werden durch einen Fehler auf dem Server verursacht. Es kommt zu Client-Timeouts, weil ein Vorgang auf dem Server das vom Client spezifizierte Timeout überschritten hat. Ein Client, der die Speicher-Clientbibliothek verwendet, kann mithilfe der Eigenschaft **ServerTimeout** der Klasse **QueueRequestOptions** ein Timeout für einen Vorgang einrichten.

Server-Timeouts zeigen ein Problem mit dem Speicherdienst an, das weitere Untersuchung erfordert. Sie können Metriken verwenden, um zu überprüfen, ob Sie die Skalierbarkeitsgrenzen für den Dienst erreichen, und alle Verkehrsspitzen zu identifizieren, die dieses Problem verursachen könnten. Wenn das Problem periodisch auftritt, kann es durch Lastenausgleich im Dienst verursacht sein. Wenn das Problem anhält und nicht dadurch verursacht wird, dass Ihre Anwendung die Dienst-Skalierbarkeitsgrenzen erreicht, sollten Sie eine Support-Problemstellung eröffnen. In Bezug auf Client-Timeouts müssen Sie entscheiden, ob das Zeitlimit auf einen angemessenen Wert im Client festgelegt ist und entweder den im Client eingestellten Timeout-Wert ändern oder untersuchen, wie Sie die Leistung des Speicherdiensts verbessern können – beispielsweise durch die Optimierung Ihrer Tabellenabfragen oder die Reduzierung der Größe Ihrer Nachrichten.

### <a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metriken zeigen Anstieg bei PercentNetworkError an

Ihre Metriken zeigen einen Anstieg bei **PercentNetworkError** für einen Ihrer Speicherdienste an. Die **PercentNetworkError**-Metrikist eine Aggregation der folgenden Metriken: **NetworkError**, **AnonymousNetworkError**, und **SASNetworkError**. Diese treten auf, wenn der Speicherdienst bei einer Speicheranfrage des Client einen Netzwerkfehler entdeckt.

Die häufigste Ursache für diesen Fehler ist eine Trennung der Client-Verbindung vor Ablauf eines Timeouts im Speicherdienst. Sie sollten den Code in Ihrem Client untersuchen, um herauszufinden, warum und wann der Client die Verbindung zum Speicherdienst abbricht. Sie können auch Wireshark, Microsoft Message Analyzer oder TCPing verwenden, um Netzwerkverbindungsprobleme des Client zu untersuchen. Die Beschreibung dieser Tools finden Sie in den [Anhängen].

### <a name="the-client-is-receiving-403-messages"></a>Der Client empfängt HTTP 403 (Verboten)-Meldungen

Wenn Ihre Clientanwendung einen HTTP 403 (Verboten)-Fehler ausgibt, ist eine wahrscheinliche Ursache, dass der Client eine abgelaufene Shared Access Signature (SAS) verwendet, wenn er eine Speicheranfrage versendet. (Weitere mögliche Ursachen sind Zeitverzögerung, ungültige Schlüssel und leere Header). Wenn ein abgelaufener Schlüssel die Ursache ist, werden Sie keine Einträge in den serverseitigen Speicherprotokollierungsdaten finden. Die folgende Tabelle zeigt ein Beispiel für eine von der Speicher-Clientbibliothek generierte Clientprotokollierung, die folgende Problemstellung veranschaulicht:

Quelle|Ausführlichkeit|Ausführlichkeit|Clientanfrage-ID|Vorgangstext
---|---|---|---|---
Microsoft.WindowsAzure.Storage|Information|3|85d077ab-…|Vorgang mit Primärspeicherort pro Speicherortmodus PrimaryOnly starten.
Microsoft.WindowsAzure.Storage|Information|3|85d077ab-…|Starten einer synchronen Anforderung an https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;sr=c&amp;si=mypolicy&amp;sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&amp;api-version=2014-02-14.
Microsoft.WindowsAzure.Storage|Information|3|85d077ab-…|Warten auf Antwort.
Microsoft.WindowsAzure.Storage|Warnung|2|85d077ab-…|Ausnahme beim Warten auf Antwort: Der Remoteserver hat einen Fehler zurückgegeben: (403) Verboten.
Microsoft.WindowsAzure.Storage|Information|3|85d077ab-…|Antwort erhalten. Statuscode = 403, Anfrage-ID = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = .
Microsoft.WindowsAzure.Storage|Warnung|2|85d077ab-…|Während des Vorgangs ausgelöste Ausnahme: Der Remoteserver hat einen Fehler zurückgegeben: (403) Verboten.
Microsoft.WindowsAzure.Storage|Information|3 |85d077ab-…|Prüfung, ob Vorgang wiederholt werden sollte. Wiederholungsanzahl = 0, HTTP-Statuscode = 403, Ausnahme = Der Remoteserver hat einen Fehler zurückgegeben: (403) Verboten. 
Microsoft.WindowsAzure.Storage|Information|3|85d077ab-…|Der nächste Speicherort wurde auf Primär gesetzt, basierend auf dem Speicherortmodus.
Microsoft.WindowsAzure.Storage|Fehler|1|85d077ab-…|Wiederholungsrichtlinie hat keinen erneuten Versuch erlaubt. Scheitern mit: Der Remoteserver hat einen Fehler zurückgegeben: (403) Verboten.

In diesem Szenario sollten Sie untersuchen, warum der SAS-Token abläuft, bevor der Client den Token an den Server sendet:

- In der Regel sollten Sie keine Startzeit festlegen, wenn Sie eine SAS für einen Client zur sofortigen Verwendung erstellen. Wenn es kleine Zeitunterschiede zwischen dem die SAS generierenden Host und dem Speicherdienst gibt, kann der Speicherdienst eine noch nicht gültige SAS empfangen.
- Sie sollten keine sehr kurze Ablaufzeit für eine SAS einstellen. Auch hier können kleine Zeitunterschiede zwischen dem die SAS generierenden Host und dem Speicherdienst dazu führen, dass eine SAS scheinbar früher als erwartet abläuft.
- Stimmt der Versionsparameter im SAS-Schlüssel (zum Beispiel **sv=2012-02-12**) mit der von Ihnen verwendeten Version der Speicher-Clientbibliothek überein? Sie sollten immer die neueste Version der Speicher-Clientbibliothek verwenden. Weitere Informationen zu SAS-Token-Versionierung und Abhängigkeiten von der Clientbibliotheksversion finden Sie unter <a href="http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/14/what-s-new-for-microsoft-azure-storage-at-teched-2014.aspx" target="_blank"></a>. 
- Wenn Sie Ihren Speicherzugriffsschlüssel neu erstellen (klicken Sie auf einer beliebigen Seite in Ihrem Speicherkonto im Azure-Portal auf **Zugriffsschlüssel verwalten**), kann dies jedes vorhandene SAS-Token unwirksam machen. Dies kann problematisch sein, wenn Sie SAS-Token mit einer langen Ablaufzeit zum Cachen von Clientanwendungen generieren.

Wenn Sie die Speicher-Clientbibliothek verwenden, um SAS-Token zu erstellen, ist es einfach, einen gültigen Token anzulegen. Wenn Sie allerdings die Speicher-REST-API verwenden und den SAS-Token manuell anlegen, sollten Sie sorgfältig das Thema <a href="http://msdn.microsoft.com/library/azure/ee395415.aspx" target="_blank">Zugriffsdelegierung mit einer Shared Access Signature</a> in MSDN lesen.

### <a name="the-client-is-receiving-404-messages"></a>Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen
Wenn die Clientanwendung eine HTTP 404 (Nicht gefunden)-Meldung vom Server empfängt, bedeutet dies, dass das Objekt, das der Client verwenden will (z. B. eine Entität, Tabelle, Blob, Container oder Warteschlange) nicht im Speicherdienst vorhanden ist. Hierfür gibt es eine Reihe möglicher Gründe, beispielsweise:

- [Der Client oder ein anderer Prozess haben das Objekt vorher gelöscht]
- [Ein Problem mit der Shared Access Signature (SAS)-Authentifizierung]
- [Clientseitiger JavaScript-Code verfügt nicht über die Zugriffsberechtigung für das Objekt]
- [Netzwerkausfall]

#### <a name="client-previously-deleted-the-object"></a>Der Client oder ein anderer Prozess hat das Objekt vorher gelöscht
In Szenarien, in denen der Client versucht, Daten in einem Speicherdienst zu lesen, zu aktualisieren oder zu löschen, kann eine vorherige Operation, die das fragliche Objekt aus dem Storage-Service gelöscht hat, normalerweise einfach anhand der Serverprotokollierung identifiziert werden. Sehr oft ergeben die Protokollierungsdaten, dass ein anderer Benutzer oder Prozess das Objekt gelöscht hat. In der serverseitigen Speicherprotokollierung zeigen die Spalten mit der Vorgangsart und den angeforderten Objektschlüsseln, wann ein Client ein Objekt gelöscht hat.

Im Szenario, in dem ein Client versucht, ein Objekt einzufügen, ist es möglicherweise nicht sofort offensichtlich, warum dies zu einer HTTP 404 (Nicht gefunden)-Antwort führt, da der Client ein neues Objekt erstellt. Wenn der Client allerdings ein Blob anlegt, muss er den Blob-Container finden können, wenn der Client eine Nachricht anlegt, muss er eine Warteschlange finden können, und wenn der Client eine Zeile hinzufügt, muss er die Tabelle finden können.

Sie können die clientseitige Protokollierung aus der Speicher-Clientbibliothek verwenden, um genauer zu erkennen, wann der Client spezifische Anfragen an den Speicherdienst sendet.

Die folgende von der Speicher-Clientbibliothek erzeugte Clientprotokollierung verdeutlicht das Problem, wenn der Client den Container für den Blob, den er erstellt, nicht findet. Die Protokollierung enthält Details zu den folgenden Speicheroperationen:

Anfrage-ID|Vorgang
---|---
07b26a5d-...|**DeleteIfExists**: Methode, um den Blob-Container zu löschen. Beachten Sie, dass dieser Vorgang eine **HEAD**-Anforderung zur Prüfung der Existenz des Containers beinhaltet.
e2d06d78…|**CreateIfNotExists**: Methode zur Erstellung des Blob-Containers. Beachten Sie, dass dieser Vorgang eine **HEAD**-Anforderung beinhaltet, welche die Existenz des Containers prüft. Die **HEAD**-Anforderung gibt eine 404-Meldung zurück, fährt aber fort.
de8b1c3c-...|**UploadFromStream**: Methode, um das Blob zu erstellen. Die **PUT**-Anforderung schlägt mit einer 404-Meldung fehl

Protokollierungseinträge:

Anfrage-ID | Vorgangstext
---|---
07b26a5d-...|Starten einer synchronen Anforderung an https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-...|StringToSign = HEAD...x-ms-client-request-id:07b26a5d-...x-ms-date:Tue, 03 Jun 2014 10:33:11 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-...|Warten auf Antwort.
07b26a5d-... | Antwort erhalten. Status code = 200, Request ID = eeead849-...Content-MD5 = , ETag = ";0x8D14D2DC63D059B";.
07b26a5d-... | Antwortheader wurden erfolgreich verarbeitet, fortfahren mit dem Rest der Operation.
07b26a5d-... | Antworttext downloaden.
07b26a5d-... | Operation erfolgreich abgeschlossen.
07b26a5d-... | Starten einer synchronen Anforderung an https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
07b26a5d-... | StringToSign = DELETE............x-ms-client-request-id:07b26a5d-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
07b26a5d-... | Warten auf Antwort.
07b26a5d-... | Antwort erhalten. Statuscode = 202, Anfrage-ID = 6ab2a4cf-..., Content-MD5 = , ETag = .
07b26a5d-... | Antwortheader wurden erfolgreich verarbeitet, fortfahren mit dem Rest der Operation.
07b26a5d-... | Antworttext downloaden.
07b26a5d-... | Operation erfolgreich abgeschlossen.
e2d06d78-... | Starten einer asynchronen Anforderung an https://domemaildist.blob.core.windows.net/azuremmblobcontainer.</td>.
e2d06d78-... | StringToSign = HEAD............x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-...| Warten auf Antwort.
de8b1c3c-... | Starten einer synchronen Anforderung an https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | StringToSign = PUT...64.qCmF+TQLPhq/YYK50mP9ZQ==........x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt.
de8b1c3c-... | Vorbereitung, um Anfragedaten zu schreiben.
e2d06d78-... | Ausnahme beim Warten auf Antwort: Der Remoteserver hat einen Fehler zurückgegeben: (404) Verboten.
e2d06d78-... | Antwort erhalten. Statuscode = 404, Anfrage-ID = 353ae3bc-..., Content-MD5 = , ETag = .
e2d06d78-... | Antwortheader wurden erfolgreich verarbeitet, fortfahren mit dem Rest der Operation.
e2d06d78-... | Antworttext downloaden.
e2d06d78-... | Operation erfolgreich abgeschlossen.
e2d06d78-... | Starten einer asynchronen Anforderung an https://domemaildist.blob.core.windows.net/azuremmblobcontainer.
e2d06d78-...|StringToSign = PUT...0.........x-ms-client-request-id:e2d06d78-....x-ms-date:Tue, 03 Jun 2014 10:33:12 GMT.x-ms-version:2014-02-14./domemaildist/azuremmblobcontainer.restype:container.
e2d06d78-... | Warten auf Antwort.
de8b1c3c-... | Anfragedaten schreiben.
de8b1c3c-... | Warten auf Antwort. 
e2d06d78-... | Ausnahme beim Warten auf Antwort: Der Remoteserver hat einen Fehler zurückgegeben: (409) Konflikt.
e2d06d78-... | Antwort erhalten. Statuscode = 409, Anfrage-ID = c27da20e-..., Content-MD5 = , ETag = .
e2d06d78-... | Fehlerantworttext downloaden.
de8b1c3c-... | Ausnahme beim Warten auf Antwort: Der Remoteserver hat einen Fehler zurückgegeben: (404) Nicht gefunden..
de8b1c3c-... | Antwort erhalten. Statuscode = 404, Anfrage-ID = 0eaeab3e-..., Content-MD5 = , ETag = .
de8b1c3c-...| Ausnahme beim Warten auf Antwort: Der Remoteserver hat einen Fehler zurückgegeben: (404) Nicht gefunden.
de8b1c3c-... | Wiederholungsrichtlinie hat keinen erneuten Versuch erlaubt. Scheitern mit: Der Remoteserver hat einen Fehler zurückgegeben: (404) Nicht gefunden.
e2d06d78-... | Wiederholungsrichtlinie hat keinen erneuten Versuch erlaubt. Scheitern mit: Der Remoteserver hat einen Fehler zurückgegeben: (409) Konflikt.

In diesem Beispiel zeigt die Protokollierung, dass der Client Anfragen der Methode **CreateIfNotExists** (Anfrage-ID e2d06d78…) mit den Anfragen aus der Methode **UploadFromStream** (de8b1c3c-...) überlappt: Dies ist möglich, weil die Clientanwendung diese Methoden asynchron aktiviert. Sie sollten den asynchronen Code im Client ändern, um sicherzustellen, dass er die Container erstellt, bevor er versucht, Daten an ein Blob in diesem Container hochzuladen. Idealerweise sollten Sie alle Ihre Container im Voraus erstellen.

#### <a name="SAS-authorization-issue"></a>Ein Problem mit der Shared Access Signature (SAS)-Authentifizierung

Wenn die Clientanwendung versucht, einen SAS-Schlüssel ohne die notwendigen Genehmigungen für den Vorgang zu verwenden, liefert der Speicherdienst eine HTTP 404 (Nicht gefunden)-Meldung an den Client zurück. Zugleich wird auch ein Wert für **SASAuthorizationError** in den Metriken angezeigt, der nicht null ist.

Die folgende Tabelle zeigt eine Muster-Serverprotokollierungsnachricht aus der Speicherprotokollierungsdatei:

<table>
  <tr>
    <td>Anfragestartzeit</td>
    <td>2014-05-30T06:17:48.4473697Z</td>
  </tr>
  <tr>
    <td>Vorgangsart</td>
    <td>GetBlobProperties</td>
  </tr>
  <tr>
    <td>Anfragestatus</td>
    <td>SASAuthorizationError</td>
  </tr>
  <tr>
    <td>HTTP-Statuscode</td>
    <td>404</td>
  </tr>
  <tr>
    <td>Authentifizierungsart</td>
    <td>SAS</td>
  </tr>
  <tr>
    <td>Dienstart</td>
    <td>Blob</td>
  </tr>
  <tr>
    <td>Anfrage-URL</td>
    <td>
    https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&amp;amp;sr=c&amp;amp;si=mypolicy&amp;amp;sig=XXXXX&amp;amp;api-version=2014-02-14&amp;amp;</td>
  </tr>
  <tr>
    <td>Anfrage-ID-Header</td>
    <td>a1f348d5-8032-4912-93ef-b393e5252a3b</td>
  </tr>
  <tr>
    <td>Clientanfrage-ID</td>
    <td>2d064953-8436-4ee0-aa0c-65cb874f7929</td>
  </tr>
</table>

Sie sollten untersuchen, warum Ihre Clientanwendung versucht, einen Vorgang auszuführen, für die ihr keine Berechtigungen zugeteilt wurden.

#### <a name="JavaScript-code-does-not-have-permission"></a>Clientseitiger JavaScript-Code verfügt nicht über die Zugriffsberechtigung für das Objekt

Wenn Sie einen JavaScript-Client verwenden und der Speicherdienst HTTP 404-Meldungen zurückliefert, überprüfen Sie die folgenden JavaScript-Fehler im Browser:

    SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
    SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.

> [AZURE.NOTE]Sie können die F12-Entwicklertools im Internet Explorer verwenden, um die zwischen Browser und Speicherdienst ausgetauschten Meldungen zu verfolgen, wenn Sie clientseitige JavaScript-Probleme behandeln.

Diese Fehler treten auf, weil im Webbrowser die <a href="http://www.w3.org/Security/wiki/Same_Origin_Policy" target="_blank">Same Origin Policy</a> als Sicherheitsbeschränkung implementiert ist, welche die Webseite daran hindert, eine API aus einer anderen Domäne aufzurufen.

Um eine Übergangslösung für das JavaScript-Problem zu finden, können Sie Cross-Origin Resource Sharing (CORS) für den Speicherdienst konfigurieren, auf den der Client zugreift. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/azure/dn535601.aspx" target="_blank">Cross-Origin Resource Sharing (CORS)-Support für die Azure-Speicherdienste</a> in MSDN.

Das folgende Codebeispiel zeigt, wie Sie Ihren Blob-Dienst so konfigurieren, dass JavaScript in der Contoso-Domäne ausgeführt wird, um auf ein Blob in Ihrem Blob-Speicherdienst zuzugreifen:

    CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
    // Set the service properties.
    ServiceProperties sp = client.GetServiceProperties();
    sp.DefaultServiceVersion = "2013-08-15";
    CorsRule cr = new CorsRule();
    cr.AllowedHeaders.Add("*");
    cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
    cr.AllowedOrigins.Add("http://www.contoso.com");
    cr.ExposedHeaders.Add("x-ms-*");
    cr.MaxAgeInSeconds = 5;
    sp.Cors.CorsRules.Clear();
    sp.Cors.CorsRules.Add(cr);
    client.SetServiceProperties(sp);

#### <a name="network-failure"></a>Netzwerkfehler 

In einigen Fällen können verlorene Netzwerkpakete dazu führen, dass der Speicherdienst HTTP 404-Nachrichten an den Client zurückliefert. Wenn Ihre Clientanwendung beispielsweise eine Entität aus dem Tabellendienst löscht, liefert der Client eine Speicherausnahme mit einer "HTTP 404 (Nicht gefunden)"-Statusmeldung vom Tabellendienst zurück. Wenn Sie die Tabelle im Tabellenspeicherdienst untersuchen, sehen Sie, dass der Dienst die Entität wie gefordert gelöscht hat.

Zu den Ausnahmedetails im Client gehört die vom Tabellendienst für die Anfrage zugeteilte Anfrage-ID (7e84f12d...). Sie können diese Information verwenden, um die Anfragedetails in der serverseitigen Speicherprotokollierung ausfindig zu machen, indem Sie nach der Spalte **request-id-header** in der Protokollierungsdatei suchen. Sie könnten die Metriken auch verwenden, um zu identifizieren, wann solche Fehler auftreten, und anschließend die Protokollierungsdateien durchsuchen, ausgehend vom Zeitpunkt, an dem die Metriken diesen Fehler aufgezeichnet haben. Dieser Protokolleintrag zeigt, dass das Löschen mit einer HTTP 404-Status-Meldung "Client Other Error" fehlgeschlagen ist. Derselbe Protokolleintrag enthält in der Spalte **client-request-id** auch die vom Client generierte Anfrage-ID (813ea74f…).

Die serverseitige Protokollierung umfasst noch einen weiteren Eintrag mit dem gleichen Wert für die **Clientanfrage-ID** (813ea74f…) für einen erfolgreichen Löschvorgang vom selben Client für dieselbe Entität. Dieser erfolgreiche Löschvorgang erfolgte unmittelbar vor der fehlgeschlagenen Löschanfrage.

Die wahrscheinlichste Ursache dieses Szenarios ist, dass der Client eine Löschanforderung für die Entität an den Tabellendienst gesendet hat, der erfolgreich war, für die er jedoch keine Bestätigung vom Server empfangen hat (vielleicht aufgrund eines temporären Netzwerkproblems). Der Client hat den Vorgang dann automatisch wiederholt (unter Verwendung derselben **Clientanfrage-ID**), und diese Wiederholung ist fehlgeschlagen, weil die Entität bereits gelöscht wurde.

Wenn dieses Problem häufig auftritt, sollten Sie untersuchen, warum der Client keine Bestätigungen aus dem Tabellendienst erhält. Wenn das Problem intermittierend ist, sollten Sie den Fehler "HTTP (404) Nicht gefunden" eingrenzen und im Client protokollieren, aber dem Client die Fortführung erlauben.

### <a name="the-client-is-receiving-409-messages"></a>Der Client empfängt HTTP 409 (Konflikt)-Meldungen

Die folgende Tabelle zeigt einen Auszug aus der serverseitigen Protokollierung für zwei Clientvorgänge: **DeleteIfExists**, unmittelbar gefolgt von **CreateIfNotExists**, unter Verwendung des gleichen Blob-Containernamens. Beachten Sie, dass jeder Clientvorgang zu zwei an den Server gesendeten Anfragen führt, zuerst **GetContainerProperties**, um zu prüfen, ob der Container existiert, gefolgt von **DeleteContainer** bzw. **CreateContainer**.

Timestamp|Vorgang|Ergebnis|Containername|Clientanfrage-ID
---|---|---|---|---
05:10:13.7167225|GetContainerProperties|200|mmcont|c9f52c89-…
05:10:13.8167325|DeleteContainer|202|mmcont|c9f52c89-…
05:10:13.8987407|GetContainerProperties|404|mmcont|bc881924-…
05:10:14.2147723|CreateContainer|409|mmcont|bc881924-…

Der Code in der Clientanwendung löscht und erstellt dann umgehend einen Blob-Container unter Verwendung des gleichen Namens neu: Die Methode **CreateIfNotExists** (Clientanfrage-ID bc881924-…) scheitert schließlich mit dem HTTP 409 (Konflikt)-Fehler. Wenn ein Client Blob-Container, -Tabellen oder -Warteschlangen löscht, gibt es eine kurze Verzögerung, bevor der Name wieder verfügbar ist.

Beim Erstellen neuer Container sollte die Clientanwendung eindeutige Containernamen verwenden, wenn das Lösch-/Wiedererstellungsmuster übereinstimmt.

### <a name="metrics-show-low-percent-success"></a>Metriken zeigen niedrigen PercentSuccess an, oder Vorgänge in Analyse-Protokolleinträgen haben den Transaktionsstatus ClientOtherErrors

Die **PercentSuccess**-Metrik erfasst den Prozentsatz der erfolgreichen Vorgänge, basierend auf ihrem HTTP-Statuscode. Vorgänge mit Statuscodes im Bereich 2XX gelten als erfolgreich, während Vorgänge mit Statuscodes in den Bereichen 3XX, 4XX und 5XX als nicht erfolgreich gelten und den Metrikwert **PercentSucess** senken. In den serverseitigen Speicher-Protokollierungsdateien sind diese Vorgänge mit dem Transaktionsstatus **ClientOtherErrors** erfasst.

Es ist wichtig zu beachten, dass diese Vorgänge erfolgreich abgeschlossen wurden und somit keinen Einfluss auf andere Metriken wie die Verfügbarkeit haben. Hier sind einige Beispiele für Vorgänge, die erfolgreich ausgeführt werden, aber zu erfolglosen HTTP-Statuscodes führen: - **ResourceNotFound** (Nicht Gefunden, 404), z. B. von einer GET-Anforderung für ein Blob, das nicht vorhanden ist. - **ResouceAlreadyExists** (Konflikt, 409), z. B. von einem**CreateIfNotExist**-Vorgang, in dem die Ressource bereits vorhanden ist. - **ConditionNotMet** (Nicht Geändert, 304), z. B. aus einer bedingten Operation, wenn ein Client beispielsweise einen **ETag**-Wert und einen HTTP-**If-None-Match**-Header sendet, um ein Image nur dann anzufordern, wenn es seit dem letzten Vorgang aktualisiert wurde.

Eine Liste bekannter REST API-Fehlercodes, die von den Speicherdiensten zurückgegeben werden, finden Sie auf der Seite <a href="http://msdn.microsoft.com/library/azure/dd179357.aspx" target="_blank">Allgemeine REST API-Fehlercodes</a>.

### <a name="capacity-metrics-show-an-unexpected-increase"></a>Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an


Wenn Sie plötzliche, unerwartete Änderungen in der Kapazitätsauslastung Ihres Speicherkontos feststellen, können Sie die Gründe untersuchen, indem Sie zuerst einen Blick auf Ihre Verfügbarkeitsmetriken werfen. Zum Beispiel könnte eine Erhöhung der Anzahl fehlgeschlagener Löschanfragen, die Sie als anwendungsspezifische Bereinigungsvorgänge verwenden, nicht wie erwartet Platz freimachen (zum Beispiel, weil die für die Bereinigung verwendeten SAS-Token abgelaufen sind) und zu einem Anstieg der Blob-Speichergröße führen.

### <a name="you-are-experiencing-unexpected-reboots"></a>Sie stoßen auf unerwartete Neustarts von virtuellen Computern, die über eine große Anzahl angeschlossener virtueller VHDs verfügen

Wenn eine virtuelle Maschine (VM) über eine große Anzahl von angeschlossenen VHDs verfügt, die sich im gleichen Speicherkonto befinden, sollten Sie die Skalierbarkeitsziele für ein das Scheitern der VM verursachendes Einzelspeicherkonto überschreiten. Sie sollten die Minutenmetriken für das Speicherkonto (**TotalRequests**/**TotalIngress**/**TotalEgress**) für Spitzen überprüfen, welche die Skalierbarkeitsziele für ein Speicherkonto überschreiten. Siehe "[Metriken zeigen Anstieg bei PercentThrottlingError an]" für Unterstützung bei der Feststellung, ob Drosselung in Ihrem Speicherkonto stattgefunden hat.

In der Regel wird jede einzelne Input- oder Output-Operation auf einer VHD von einem virtuellen Computer in **Get Page**- oder **Put Page**-Operationen des zugrunde liegenden Seiten-Blobs übersetzt. Daher können Sie die geschätzten IOPS für Ihre Umgebung verwenden, um zu bestimmen, über wie viele virtuelle Festplatten Sie in einem einzigen Speicherkonto auf Grundlage des spezifischen Anwendungsverhaltens verfügen können. Wir empfehlen, nicht über mehr als 40 Festplatten in einem einzigen Speicherkonto zu verwenden. Unter <a href="http://msdn.microsoft.com/library/azure/dn249410.aspx" target="_blank">Ziele für Skalierbarkeit und Leistung des Azure-Speichers</a> finden Sie nähere Informationen über die aktuellen Skalierbarkeitsziele für Speicherkonten, insbesondere die Gesamtanforderungsrate und die Gesamtbandbreite für den Speicherkontotyp, den Sie verwenden. Wenn Sie die Skalierbarkeitsziele für Ihr Speicherkonto überschreiten, sollten Sie die virtuellen Festplatten in mehreren verschiedenen Speicherkonten platzieren, um die Aktivität in den einzelnen Konten zu reduzieren.

### <a name="your-issue-arises-from-using-the-storage-emulator"></a>Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test

Sie verwenden den Speicheremulator in der Regel während der Entwicklungs- und Testphase, um kein Azure-Speicherkonto einrichten zu müssen. Gängige Probleme, die bei der Verwendung des Speicheremulators auftreten können, sind:

- [Funktion "X" funktioniert im Speicheremulator nicht]
- [Fehler „Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format" bei der Verwendung des Speicheremulators]
- [Das Ausführen des Speicheremulators erfordert Administratorrechte]

#### <a name="feature-X-is-not-working"></a>Funktion "X" funktioniert im Speicheremulator nicht

Der Speicheremulator unterstützt nicht alle Funktionen des Azure-Speicherdiensts wie beispielsweise den Dateidienst. Weitere Informationen dazu finden Sie unter <a href="http://msdn.microsoft.com/library/azure/gg433135.aspx" target="_blank">Unterschiede zwischen dem Speicheremulator und den Azure-Speicherdiensten</a> in MSDN.

Für die nicht vom Speicheremulator unterstützten Funktionen verwenden Sie den Azure-Speicherdienst in der Cloud.

#### <a name="error-HTTP-header-not-correct-format"></a>Fehler "Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format" bei der Verwendung des Speicheremulators

Sie testen Ihre die Speicher-Clientbibliothek verwendende Anwendung mit dem lokalen Speicheremulator, und Methodenaufrufe wie **CreateIfNotExists** scheitern mit der Fehlernachricht "Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format". Dies weist darauf hin, dass die von Ihnen verwendete Version des Speicheremulators die Version Ihrer Speicher-Clientbibliothek nicht unterstützt. Die Speicher-Clientbibliothek fügt den Header **x-ms-version** zu allen getätigten Anfragen hinzu. Wenn der Speicheremulator den Wert im **x-ms-version**-Header nicht erkennt, lehnt er die Anfrage ab.

Sie können die Speicher-Clientbibliothek-Protokollierung verwenden, um den Wert des gesendeten **x-ms-version-Headers** anzuzeigen. Sie können den Wert des **x-ms-version-Headers** auch anzeigen, wenn Sie Fiddler verwenden, um die Anfragen Ihrer Clientanwendung zu verfolgen.

Dieses Szenario tritt in der Regel ein, wenn Sie die neueste Version der Speicher-Clientbibliothek installieren und verwenden, ohne den Speicheremulator zu aktualisieren. Sie sollten entweder die neueste Version des Speicheremulators installieren oder für Entwicklung und Test Cloudspeicher anstelle des Emulators verwenden.

#### <a name="storage-emulator-requires-administrative-privileges"></a>Zum Ausführen des Speicheremulators sind Administratorrechte erforderlich

Sie benötigen Administratorrechte, wenn Sie den Speicheremulator ausführen. Dies ist nur erforderlich, wenn Sie den Speicheremulator zum ersten Mal initialisieren. Nachdem Sie den Speicheremulator initialisiert haben, benötigen Sie keine Administratorrechte, um ihn wieder auszuführen.

Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/azure/gg433132.aspx" target="_blank">Initialisierung des Speicheremulators mithilfe des Befehlszeilen-Tools</a> in MSDN. (Sie können den Speicheremulator auch in Visual Studio initialisieren, wofür ebenfalls Administratorrechte erforderlich sind.)

### <a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme

Wenn Sie versuchen, SDK zu installieren, scheitert dies beim Versuch, den Speicheremulator auf Ihrem lokalen Computer zu installieren. Die Installationsprotokollierung beinhaltet eine der folgenden Nachrichten:

- CAQuietExec: Fehler: Nicht in der Lage, auf SQL-Instanz zuzugreifen
- CAQuietExec: Fehler: Nicht in der Lage, Datenbank anzulegen

Die Ursache ist ein Problem mit der vorhandenen LocalDB- Installationen. Standardmäßig verwendet der Speicheremulator LocalDB zur Datenaufbewahrung, wenn er die Azure-Speicherdienste simuliert. Sie können Ihre LocalDB-Instanz zurücksetzen, indem Sie die folgenden Befehle in einem Eingabeaufforderungs-Fenster ausführen, bevor Sie versuchen, SDK zu installieren.

    sqllocaldb stop v11.0
    sqllocaldb delete v11.0
    delete %USERPROFILE%\WAStorageEmulatorDb3*.*
    sqllocaldb create v11.0

Der **Lösch**-Befehl entfernt alle alten Datenbankdateien von vorhergehenden Installationen aus dem Speicheremulator.

### <a name="you-have-a-different-issue-with-a-storage-service"></a>Sie haben ein anderes Problem mit einem Speicherdienst

Wenn die vorherigen Abschnitte nicht Ihr Problem mit dem Speicherdienst enthalten, sollten Sie den folgenden Ansatz zur Diagnose und Fehlerbehebung Ihres Problems verfolgen.

- Überprüfen Sie Ihre Metriken, um zu sehen, ob es eine Abweichung von erwarteten Baseline-Verhalten gibt. Anhand der Metriken können Sie feststellen, ob das Problem vorübergehend oder dauerhaft ist und welche Speicheroperationen das Problem beeinträchtigt.
- Die Metrikinformationen unterstützen Sie bei der Suche nach serverseitigen Protokollierungsdaten, um weitere detaillierte Informationen über alle auftretenden Fehler zu erhalten. Diese Informationen können Ihnen dabei helfen, das Problem zu finden und zu beheben.
- Wenn die Informationen in den serverseitigen Protokollen nicht ausreichen, um das Problem erfolgreich zu beheben, können Sie die Clientprotokolle der Speicher-Clientbibliothek zur Untersuchung des Verhaltens Ihrer Clientanwendung und Tools wie Fiddler, Wireshark und Microsoft Message Analyzer zur Untersuchung Ihres Netzwerks verwenden.

Weitere Informationen finden Sie unter [Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr].

Weitere Informationen zu Wireshark finden Sie unter [Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr].

Weitere Informationen zur Verwendung von Microsoft Message Analyzer finden Sie unter [Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr].

## <a name="appendices"></a>Anhänge

Die Anhänge beschreiben verschiedene Tools, die bei der Diagnose und Fehlerbehandlung von Problemen mit Azure-Speicher (und anderen Diensten) nützlich sein könnten. Diese Werkzeuge sind nicht Teil von Azure-Speicher, und einige sind Produkte von Drittanbietern. Als solche sind die in diesen Anhängen diskutierten Tools nicht durch Support-Vereinbarungen abgedeckt, die Sie ggf. mit Microsoft Azure oder Azure-Speicher getroffenen haben. Als Bestandteil Ihrer Evaluierung sollten Sie daher die Lizenz- und Supportoptionen prüfen, die die Anbieter dieser Tools zur Verfügung stellen.

### <a name="appendix-1"></a>Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr

Fiddler ist ein nützliches Werkzeug für die Analyse des HTTP- und HTTPS-Datenverkehrs zwischen Ihrer Clientanwendung und dem von Ihnen verwendeten Azure-Speicherdienst. Sie können Fiddler von <a href="http://www.telerik.com/fiddler" target="_blank">http://www.telerik.com/fiddler</a> herunterladen.

> [AZURE.NOTE]Fiddler kann HTTPS-Datenverkehr entschlüsseln. Sie sollten die Fiddler-Dokumentation sorgfältig lesen, um zu verstehen, wie dies erfolgt und welche Auswirkungen es auf die Sicherheit hat.

Dieser Anhang enthält eine kurze Anleitung, wie Sie Fiddler konfigurieren, damit der Datenverkehr zwischen dem lokalen Rechner, auf dem Sie Fiddler installiert haben, und den Azure-Speicherdiensten erfasst wird.

Nachdem Sie Fiddler gestartet haben, beginnt die Erfassung von HTTP- und HTTPS-Datenverkehr auf dem lokalen Rechner. Hier folgen einige nützliche Befehle zur Steuerung von Fiddler:

- Erfassung des Datenverkehrs stoppen und starten. Gehen Sie im Hauptmenü auf **File**, und klicken Sie auf **Capture Traffic**, um die Erfassung ein- und auszuschalten.
- Erfasste Verkehrsdaten speichern. Gehen Sie im Hauptmenü auf **File**, klicken Sie auf **Save** und anschließend auf **All Sessions**: Damit speichern Sie die erfassten Verkehrsdaten in einer Session-Archivdatei. Sie können ein Session-Archiv später für die Analyse neu laden oder senden, falls der Microsoft-Support dies benötigt.

Um die Menge des von Fiddler erfassten Verkehrs einzuschränken, können Sie Filter verwenden, die Sie auf der Registerkarte **Filter** konfigurieren. Der folgende Screenshot zeigt einen Filter, der nur an den Speicherendpunkt **contosoemaildist.table.core.windows.net** gesendeten Verkehr erfasst:

![][5]

### <a name="appendix-2"></a>Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr

Wireshark ist ein Netzwerkprotokoll-Analysator, mit dem Sie detaillierte Paketinformationen für einen breiten Netzwerkprotokollbereich anzeigen können. Sie können Wireshark von <a href="http://www.wireshark.org/" target="_blank">http://www.wireshark.org/</a> herunterladen.

Das folgende Verfahren zeigt, wie Sie detaillierte Paketinformationen für Datenverkehr aus dem lokalen Computer erfassen, auf dem Sie Wireshark für den Tabellendienst Ihres Azure-Speicherkontos installiert haben.

1.	Starten Sie Wireshark auf Ihrem lokalen Computer.
2.	Wählen Sie im Abschnitt **Start** die lokale Netzwerkschnittstelle oder mit dem Internet verbundene Schnittstellen aus.
3.	Klicken Sie auf **Capture Options**.
4.	Fügen Sie einen Filter zum Textfeld **Capture Filter** hinzu. Beispielsweise wird **host contosoemaildist.table.core.windows.net** Wireshark so konfigurieren, dass nur an den oder vom Tabellendienstendpunkt im **contosoemaildist**-Speicherkonto gesendete Pakete erfasst werden. Eine vollständige Liste der Erfassungsfilter finden Sie unter <a href="http://wiki.wireshark.org/CaptureFilters" target="_blank">http://wiki.wireshark.org/CaptureFilters</a>. 

    ![][6]

5.	Klicken Sie auf **Start**. Wireshark wird nun alle an oder aus dem Tabellendienstendpunkt gesendeten Pakete erfassen, wenn Sie Ihre Clientanwendung auf dem lokalen Computer verwenden.
6.	Wenn Sie fertig sind, klicken Sie im Hauptmenü auf **Capture** und anschließend auf **Stop**.
7.	Um die erfassten Daten in einer Wireshark-Erfassungsdatei zu speichern, klicken Sie im Hauptmenü auf **File** und anschließend auf **Save**.

WireShark wird alle aufgetretenen Fehler im Fenster **Packetlist** markieren. Sie können auch eine Zusammenfassung der Fehler und Warnungen im Fenster **Expert Info** anzeigen (klicken Sie auf **Analyze** und dann auf **Expert Info**).

![][7]

Sie können die TCP-Daten auch so anzeigen, wie sie in der Anwendungsschicht vorliegen, indem Sie mit der rechten Maustaste auf die TCP-Daten klicken und **Follow TCP Stream** auswählen. Dies ist besonders nützlich, wenn Sie Ihren Dump ohne Erfassungsfilter erfassen. Weitere Informationen finden Sie <a href="http://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html" target="_blank">hier</a>.

![][8]

> [AZURE.NOTE]Weitere Informationen zu Wireshark finden Sie im <a href="http://www.wireshark.org/docs/wsug_html_chunked/" target="_blank">Wireshark-Benutzerhandbuch</a>.

### <a name="appendix-3"></a>Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr

Sie können Microsoft Message Analyzer verwenden, um HTTP- und HTTPS-Datenverkehr ähnlich wie Fiddler und Netzwerkverkehr ähnlich wie Wireshark zu erfassen.

#### Konfigurieren einer Web-Rückverfolgungssession mit Microsoft Message Analyzer

Um eine Rückverfolgungssession für HTTP- und HTTPS-Verkehr mit Microsoft Message Analyzer zu konfigurieren, führen Sie Microsoft Message Analyze aus, und klicken Sie anschließend im Menü **Datei** auf **Rückverfolgung**. Wählen Sie in der Liste der verfügbaren Rückverfolgungsszenarien **Web-Proxy** aus. Geben Sie anschließend im Bereich **Konfiguration des Rückverfolgungsszenarios** im Textfeld **HostnameFilter** die Namen Ihrer Speicherendpunkte ein (Sie können diese Namen im Azure-Portal nachschlagen). Wenn beispielsweise der Name Ihres Azure-Speicherkontos **contosodata** lautet, sollten Sie im Textfeld **HostnameFilter** Folgendes hinzufügen:

    contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
    
> [AZURE.NOTE]Die Hostnamen werden durch ein Leerzeichen getrennt.

Wenn Sie bereit sind, mit dem Sammeln von Rückverfolgungsdaten zu beginnen, klicken Sie auf die Schaltfläche **Starten mit**.

Weitere Informationen zur **Web-Proxy**-Rückverfolgung mit Microsoft Message Analyzer finden Sie unter <a href="http://technet.microsoft.com/library/jj674814.aspx" target="_blank">PEF-WebProxy Provider</a> im TechNet.

Die integrierte **Web-Proxy**-Rückverfolgung in Microsoft Message Analyzer basiert auf Fiddler; sie kann clientseitigen HTTPS-Verkehr erfassen und unverschlüsselte HTTPS-Nachrichten anzeigen. Die **Web-Proxy**-Rückverfolgung funktioniert durch Konfiguration eines lokalen Proxy für den gesamten HTTP- und HTTPS-Verkehr, der Zugriff auf unverschlüsselte Nachrichten verschafft.

#### Diagnose von Netzwerkproblemen mithilfe von Microsoft Message Analyzer

Neben der Erfassung des HTTP-/HTTPS-Verkehrs zwischen der Anwendung und dem Speicherdienst mithilfe der **Web-Proxy**-Rückverfolgung können Sie mit dem Microsoft Message Analyzer auch Netzwerkpaketinformationen über die integrierte **Local-Link-Layer**-Rückverfolgung erfassen. Dadurch können Sie wie mit Wireshark Daten erfassen und Netzwerkprobleme wie verworfene Pakete diagnostizieren.

Der folgende Screenshot zeigt ein Beispiel von **Local-Link-Layer**-Rückverfolgung mit einigen **informativen** Meldungen in der Spalte **DiagnosisTypes**. Mit einem Klick auf die Spalte **DiagnosisTypes** werden die Nachrichtendetails angezeigt. In diesem Beispiel hat der Server Nachricht "#305" neu übertragen, weil sie keine Bestätigung vom Client erhalten hat:

![][9]

Wenn Sie die Rückverfolgungssession in Microsoft Message Analyzer erstellen, können Sie Filter festlegen, um die Störungsmenge bei der Rückverfolgung zu reduzieren. Klicken Sie auf der Seite **Erfassung/Rückverfolgung,** auf der Sie die Rückverfolgung definieren, auf den Link **Konfigurieren** neben **Microsoft-Windows-NDIS-PacketCapture**. Der folgende Screenshot zeigt eine Konfiguration, die TCP-Datenverkehr für die IP-Adressen der drei Speicherdienste filtert:

![][10]

Weitere Informationen zur Local-Link-Layer-Rückverfolgung von Microsoft Message Analyzer finden Sie unter <a href="http://technet.microsoft.com/library/jj659264.aspx" target="_blank">PEF-NDIS-PacketCapture Provider</a> im TechNet.

### <a name="appendix-4"></a>Anhang 4: Verwendung von Excel zur Anzeige von Metrik- und Protokollierungsdaten

Viele Tools ermöglichen das Herunterladen von Speichermetrikdaten aus dem Azure-Tabellenspeicher in einem abgegrenzten Format, wodurch die Daten einfach in Excel für die Anzeige und Analyse geladen werden können. Speicherprotokollierungsdaten vom Azure-Blob-Speicher verfügen bereits über eine abgetrenntes Format, das Sie in Excel laden können. Allerdings müssen Sie basierend auf den Informationen in <a href="http://msdn.microsoft.com/library/azure/hh343259.aspx" target="_blank">Protokollformat der Speicheranalyse</a> und <a href="http://msdn.microsoft.com/library/azure/hh343264.aspx" target="_blank">Schema der Tabellen für Speicheranalysemetriken</a> entsprechende Spaltenüberschriften hinzufügen.

Um Speicherprotokollierungsdaten in Excel zu importieren, nachdem Sie diese aus dem BLOB-Speicher heruntergeladen haben, gehen Sie folgendermaßen vor:

- Klicken Sie im Menü **Daten** auf **Von Text**.
- Navigieren Sie zur Protokollierungsdatei, die Sie anzeigen möchten, und klicken Sie auf **Importieren**.
- In Schritt 1 des **Textimport-Assistenten**, wählen Sie **Getrennt** aus.

In Schritt 1 des **Textimport-Assistenten**, wählen Sie **Semikolon** als einziges Trennungszeichen und Anführungszeichen als **Textbegrenzungszeichen** aus. Klicken Sie anschließend auf **Fertigstellen** und wählen Sie aus, wo Sie die Datei in Ihrer Arbeitsmappe ablegen möchten.

### <a name="appendix-5"></a>Anhang 5: Überwachung mit Application Insights für Visual Studio Online

Sie können die Application Insights-Funktion für Visual Studio Online als Bestandteil Ihrer Leistungs- und Verfügbarkeitsüberwachung verwenden. Dieses Tool kann:

- Sicherstellen, dass Ihr Webdienst verfügbar und reaktionsschnell ist. Ob Ihre Anwendung eine Website ist, die einen Webdienst verwendet, oder eine Geräteanwendung: Sie kann Ihre URL alle paar Minuten von Standorten auf der ganzen Welt testen, und Ihnen mitteilen, ob es ein Problem gibt. 
- Leistungsprobleme oder Ausnahmen in Ihrem Webdienst schnell diagnostizieren. Finden Sie heraus, ob CPU oder andere Ressourcen gestretched werden, gewinnen Sie Stack-Traces aus Ausnahmen, und durchsuchen Sie Protokolltraces leicht. Wenn die Leistung der Anwendung unter akzeptable Grenzen fällt, können wir Ihnen eine E-Mail senden. Sie können sowohl .NET- als auch Java-Webdienste überwachen.

Zum Redaktionszeitpunkt befindet sich Application Insights in der Vorschau. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/azure/dn481095.aspx" target="_blank">Application Insights für Visual Studio Online in MSDN</a>.


<!--Anchors-->
[Einführung]: #introduction
[Wie diese Anleitung aufgebaut ist]: #how-this-guide-is-organized

[Überwachung Ihres Speicherdiensts]: #monitoring-your-storage-service
[Überwachung der Dienstintegrität]: #monitoring-service-health
[Kapazitätsüberwachung]: #monitoring-capacity
[Verfügbarkeitsüberwachung]: #monitoring-availability
[Leistungsüberwachung]: #monitoring-performance

[Diagnose von Speicherfehlern]: #diagnosing-storage-issues
[Diagnose von Speicherproblemen]: #diagnosing-storage-issues
[Probleme mit der Dienstintegrität]: #service-health-issues
[Leistungsprobleme]: #performance-issues
[Fehlerdiagnose]: #diagnosing-errors
[Probleme mit dem Speicheremulator]: #storage-emulator-issues
[Speicher-Protokollierungstools]: #storage-logging-tools
[Verwendung von Netzwerk-Protokollierungstools]: #using-network-logging-tools

[Durchgehende Verfolgung]: #end-to-end-tracing
[Durchgängige Verfolgung]: #end-to-end-tracing
[Korrelation von Protokollierungsdaten]: #correlating-log-data
[Clientanfrage-ID]: #client-request-id
[Serveranfrage-ID]: #server-request-id
[Zeitstempel]: #timestamps

[Anleitungen zur Problembehandlung]: #troubleshooting-guidance
[Metriken zeigen hohe AverageE2ELatency und niedrige AverageServerLatency an]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metriken zeigen niedrige AverageE2ELatency und niedrige AverageServerLatency an, aber der Client erfährt hohe Latenz]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metriken zeigen hohe AverageServerLatency an]: #metrics-show-high-AverageServerLatency
[Sie stoßen auf unerwartete Verzögerungen bei der Nachrichtenübermittlung in einer Warteschlange]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metriken zeigen Anstieg bei PercentThrottlingError an]: #metrics-show-an-increase-in-PercentThrottlingError
[Vorübergehender Anstieg bei PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Permanenter Anstieg bei PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Metriken zeigen Anstieg bei PercentTimeoutError an]: #metrics-show-an-increase-in-PercentTimeoutError
[Metriken zeigen Anstieg bei PercentNetworkError an]: #metrics-show-an-increase-in-PercentNetworkError

[Der Client empfängt HTTP 403 (Verboten)-Meldungen]: #the-client-is-receiving-403-messages
[Der Client empfängt HTTP 404 (Nicht gefunden)-Meldungen]: #the-client-is-receiving-404-messages
[Der Client oder ein anderer Prozess haben das Objekt vorher gelöscht]: #client-previously-deleted-the-object
[Ein Problem mit der Shared Access Signature (SAS)-Authentifizierung]: #SAS-authorization-issue
[Clientseitiger JavaScript-Code verfügt nicht über die Zugriffsberechtigung für das Objekt]: #JavaScript-code-does-not-have-permission
[Netzwerkausfall]: #network-failure
[Der Client empfängt HTTP 409 (Konflikt)-Meldungen]: #the-client-is-receiving-409-messages

[Metriken zeigen niedrigen PercentSuccess an, oder Analyse-Protokollierungseinträge enthalten Operationen mit Transaktionsstatus "ClientOtherErrors"]: #metrics-show-low-percent-success
[Metriken zeigen niedrigen PercentSuccess an, oder Vorgänge in Analyse-Protokolleinträgen haben den Transaktionsstatus 'ClientOtherErrors']: #metrics-show-low-percent-success
[Kapazitätsmetriken zeigen einen unerwarteten Anstieg der Speicherkapazitätsauslastung an]: #capacity-metrics-show-an-unexpected-increase
[Sie stoßen auf unerwartete Neustarts von virtuellen Computern, die über eine große Anzahl angeschlossener virtueller VHDs verfügen]: #you-are-experiencing-unexpected-reboots
[Ihre Probleme entstehen aus der Verwendung des Speicheremulators für Entwicklung oder Test]: #your-issue-arises-from-using-the-storage-emulator
[Funktion "X" funktioniert im Speicheremulator nicht]: #feature-X-is-not-working
[Fehler „Der Wert einer der HTTP-Header verfügt nicht über das korrekte Format" bei der Verwendung des Speicheremulators]: #error-HTTP-header-not-correct-format
[Das Ausführen des Speicheremulators erfordert Administratorrechte]: #storage-emulator-requires-administrative-privileges
[Sie stoßen bei der Installation von Azure SDK für .NET auf Probleme]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Sie haben ein anderes Problem mit einem Speicherdienst]: #you-have-a-different-issue-with-a-storage-service

[Anhänge]: #appendices
[Anhängen]: #appendices
[Anhang 1: Verwendung von Fiddler zur Erfassung von HTTP- und HTTPS-Verkehr]: #appendix-1
[Anhang 2: Verwendung von Wireshark zur Erfassung von Netzwerkverkehr]: #appendix-2
[Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr]: #appendix-3
[Anhang 3: Verwendung von Microsoft Message Analyzer zur Erfassung von Netzwerkverkehr]: #appendix-3
[Anhang 4: Verwendung von Excel zur Anzeige von Metrik- und Protokollierungsdaten]: #appendix-4
[Anhang 5: Überwachung mit Application Insights für Visual Studio Online]: #appendix-5
[Anhang 5: Überwachung mit Application Insights für Visual Studio Online]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[2]: ./media/storage-monitoring-diagnosing-troubleshooting/portal-screenshot.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png
 

<!---HONumber=Sept15_HO2-->