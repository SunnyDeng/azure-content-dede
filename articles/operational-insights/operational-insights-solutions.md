<properties
	pageTitle="Operational Insights-Lösungen"
	description="Operational Insights ist ein Analyse-Dienst, der IT-Administratoren einen tiefen Einblick in lokale und Cloud-Umgebungen gewährt. Die Interaktion mit Echtzeit- und historischen Computerdaten sorgt für die schnelle Entwicklung benutzerdefinierter Einblicke; bereitgestellt werden außerdem Muster für die Datenanalyse, die von Microsoft und der Community entwickelt wurden."
	services="operational-insights"
	documentationCenter="n/a"
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="operational-insights"
	ms.tgt_pltfrm="NA"
	ms.devlang="NA"
	ms.topic="article"
	ms.date="08/05/2015"
	ms.author="alfran"/>

# Lösungen in Operational Insights

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

Lösungen sind Sammlungen von **Logik**-, **Visualisierungs**- und **Datenerfassungsregeln** für die wichtigsten Kundenherausforderungen von heute. Lösungen werden in der Protokollsuche von Operational Insights zum Bereitstellen von Metriken unterstützt, die sich auf einen bestimmten Problembereich beziehen. Damit erhalten Sie detailliertere Informationen und können betriebliche Probleme schneller untersuchen und lösen, verschiedene Typen von Computerdaten sammeln und Zusammenhänge herstellen sowie Aktivitäten wie Kapazitätsplanung, Patchstatusberichte und Sicherheitsüberwachung proaktiv angehen.

Dieser Leitfaden führt Sie durch die verschiedenen verfügbaren Lösungen und deren Verwendungszwecke.

>[AZURE.NOTE]Weitere Informationen zum *Hinzufügen* von Lösungen finden Sie unter [Hinzufügen von Lösungen](operational-insights-setup-workspace.md).

## SQL Assessment

Mit der Lösung "SQL Assessment" bewerten Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer SQL Server-Umgebungen. Standardmäßig werden die SQL-Systeme wöchentlich gescannt, und die Informationen werden in Form eines monatlichen Rollups dargestellt. SQL Assessment bietet eine priorisierte Liste von Empfehlungen, die auf Ihre Bereitstellungen zugeschnitten sind. Diese Empfehlungen sind in sechs Schwerpunktbereiche eingeteilt, um Ihnen und Ihrem Team folgende Möglichkeiten zu bieten:

- **Schnelles** Erfassen der **Risiken und der Integrität** Ihrer Umgebungen
- Einfaches Ergreifen von **Maßnahmen** zum Verringern von Risiken und zur Verbesserung der Integrität
- Festlegen von **Prioritäten** für Ihre Arbeit und Steigern der **Produktivität**

Für die Ausführung auf den einzelnen Agents erfordert SQL Assessment .NET 4. Es unterstützt die Standard, die Developer und die Enterprise Edition von SQL Server und damit alle derzeit unterstützten Versionen. Weitere Informationen finden Sie unter [Optimieren der Umgebung mit Assessment-Lösungen](operational-insights-assessment.md).


## Konfigurationsbewertung

Die Konfigurationsbewertung bietet Ihnen ausführliche Informationen über den aktuellen Zustand Ihrer Serverinfrastruktur.

Im Gegensatz zu Lösungen steht die Konfigurationsbewertung gleich bei der ersten Verwendung von Operational Insights mit System Center Operations Manager zur Verfügung. Sie ist nicht verfügbar, wenn Sie nur direkt verbundene Agents verwenden.

Konfigurationsdaten werden von überwachten Servern gesammelt und dann zur Verarbeitung an den Operational Insights-Dienst in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf. Verarbeitete Daten für die Server werden für die folgenden Bereiche angezeigt:

- **Alerts**: zeigt die konfigurationsbezogenen, proaktiven Warnungen, die für die überwachten Server ausgelöst wurden. Diese werden durch Regeln von der Microsoft-Kunden- und -Supportorganisation mithilfe von bewährten Methoden aus dem Einsatz erstellt.
- **Knowledge Recommendations**: zeigt die Artikel aus der Microsoft Knowledge Base an, die für die in Ihrer Infrastruktur vorgefundenen Workloads empfohlen werden. Diese Artikel werden aufgrund Ihrer Konfiguration mithilfe von maschinellem Lernen automatisch vorgeschlagen.
- **Servers and Workloads Analyzed**: zeigt die Server und die Arbeitsauslastungen an, die von Operational Insights überwacht werden.
- **Current Snapshot**: zeigt die neuesten Informationen zu Servern an, die Daten an den Operational Insights-Dienst gemeldet haben.
- **Change History**: zeigt eine Liste von Konfigurationsänderungen an, die an den überwachten Servern vorgenommen wurden.

> [AZURE.IMPORTANT]Die Konfigurationsbewertung kann nur für die von **Operations Manager** verwalteten Computer aktiviert werden. Weitere Informationen finden Sie unter [Verbinden von Operations Manager](operational-insights-connect-scom.md).


## Malware Assessment
Wenn ein unzureichender Schutz festgestellt wird, werden Server mit aktiven Bedrohungen und Server mit unzureichendem Schutz auf der Seite **Malware** angezeigt. Anhand der Informationen auf der Seite **Malware** können Sie einen Plan zum Anwenden von Schutzfunktionen auf die betreffenden Server entwickeln.

> [AZURE.IMPORTANT]Malware Assessment unterstützt derzeit nur die Echtzeitclients Windows Defender und System Center Endpoint Protection (SCEP). Wenn keiner dieser Clients gefunden wird, verwendet Malware Assessment die Daten aus dem Tool zum Entfernen bösartiger Software und markiert die Server ohne Echtzeitschutz. Weitere Informationen dazu finden Sie [hier](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess). Außerdem verfolgen wir [ein Problem mit Computern unter Windows 7 und Windows Server 2008 R2](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n).





## Alert Management

Mit der Lösung "Alert Management" können Sie Ihre **Operations Manager**-Warnungen für sämtliche Server anzeigen. Mit diesem Tool können Warnungen leicht selektiert und Ursachen in Ihrer Umgebung schnell und reibungslos identifiziert werden.

Sie erhalten Einblicke in wichtige Szenarien:

- Wie viele Warnungen wurden innerhalb eines angegebenen Zeitraums ausgelöst?
- Die Hauptquellen mit der höchsten Anzahl aktiver Warnungen innerhalb eines angegebenen Zeitraums.
- Die häufigsten aktiven kritischen Warnungen und Warnungen innerhalb eines angegebenen Zeitraums.
- Die Möglichkeit zum Durchsuchen aller Warnungen und zur Anzeige jeder einzelnen Warnung im Detail

> [AZURE.IMPORTANT]Alert Management kann nur aktiviert werden, wenn Operational Insights in Verbindung mit **System Center Operations Manager** verwendet wird. Es werden keine Daten von den Agents gesendet. Stattdessen werden die Operations Manager-Warnungen einfach mit der Cloud synchronisiert, sodass Sie sie in Operational Insights auswählen und die Protokollsuche verwenden können. Weitere Informationen finden Sie unter [Verbinden von Operations Manager](operational-insights-connect-scom.md).


## Capacity Planning

Mit der Lösung "Capacity Management" in Microsoft Azure Operational Insights können Sie die Kapazität Ihrer Serverinfrastruktur erfassen. Die Lösung liest die Leistungsindikatoren auf dem überwachten Server und sendet Daten zur Verarbeitung an den Operational Insights-Dienst in der Cloud. Auf die Nutzungsdaten wird Logik angewendet, und der Clouddienst zeichnet die Daten auf. Mit der Zeit entstehen Verwendungsmuster, und die Kapazität wird anhand des aktuellen Verbrauchs prognostiziert.

Beispielsweise kann anhand einer Prognose ermittelt werden, wann zusätzliche Prozessorkerne oder zusätzlicher Arbeitsspeicher für einen einzelnen Server benötigt wird. In diesem Beispiel kann die Prognose darauf hinweisen, dass der Server in 30 Tagen zusätzlichen Arbeitsspeicher benötigen wird. Damit können Sie ein Speicherupgrade für das nächste Wartungsfenster des Servers planen, das zum Beispiel einmal alle zwei Wochen ansteht.

>[AZURE.IMPORTANT]Capacity Management kann nur aktiviert werden, wenn Operational Insights in Verbindung mit **System Center Operations Manager** verwendet wird. Außerdem müssen Sie auch den Operations Manager-Connector mit Virtual Machine Manager (VMM) aktivieren. Zusätzliche Informationen zum Verbinden der Systeme finden Sie unter [Verbinden von VMM mit Operations Manager](https://technet.microsoft.com/library/hh882396.aspx).

Weitere Informationen zur Verwendung der Lösung "Capacity Management" finden Sie unter:

- [Verwenden der Seite "Compute"](operational-insights-capacity/#compute-page)
- [Verwenden der Seite "Direkt angeschlossener Speicher"](operational-insights-capacity/#direct-attached-storage-page)


## Change Tracking

Mit der Lösung "Configuration Change Tracking" können Sie ganz einfach Änderungen an Software und Windows-Diensten identifizieren, die in Ihrer Umgebung vorgenommen werden. Durch das Feststellen solcher Konfigurationsänderungen können Sie Betriebsprobleme leichter lokalisieren. Mithilfe der Informationen auf der Seite "Change Tracking" können Sie ganz leicht die Änderungen erkennen, die in Ihrer Serverinfrastruktur vorgenommen wurden. Sie können Änderungen an Ihrer Infrastruktur anzeigen und dann Einzelheiten für die folgenden Kategorien abrufen:

- Änderungen nach Konfigurationstyp für Software und Windows-Dienste
- Softwareänderungen an Anwendungen und Updates für einzelne Server
- Gesamtanzahl von Softwareänderungen für jede Anwendungen
- Änderungen am Windows-Dienst für einzelne Server


## System Update Assessment

Mithilfe der Lösung "System Updates" in Microsoft Azure Operational Insights können Sie fehlende Updates auf Server in Ihrer Infrastruktur anwenden. Wenn ermittelt wird, dass Updates fehlen, werden diese auf der Seite **Updates** angezeigt. Mithilfe der Seite **Updates** können Sie Strategie für fehlende Updates erarbeiten und einen Plan für deren Anwendung auf die betreffenden Server entwickeln.

Die Seite "Updates" enthält die folgenden Kategorien:

- Server, auf denen Sicherheitsupdates fehlen
- Server, die nicht kürzlich aktualisiert wurden
- Updates, die auf bestimmte Server angewendet werden sollen
- Typ der fehlenden Updates

Klicken Sie auf eine beliebige Kachel oder ein Element, um Details dazu auf der Seite **Suchen** anzuzeigen und weitere Informationen zu dem fehlenden Update zu erhalten. Die Suchergebnisse umfassen:

- Server
- Updatetitel
- Knowledge Base-ID
- Das Produkt, für welches das Update gilt
- Updateschweregrad
- Veröffentlichungsdatum

Zu den Serversuchergebnissen gehören:

- Servername
- Name der Betriebssystemversion
- Methode zur automatischen Updateaktivierung
- Tage seit dem letzten Update
- Version des Windows Update-Agents

<!---HONumber=August15_HO6-->