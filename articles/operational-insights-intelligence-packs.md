<properties 
	pageTitle="Operational Insights Intelligence Packs" 
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
	ms.date="02/20/2015" 
	ms.author="alfran"/>

# Intelligence Packs

Intelligence Packs sind eine Sammlung von **Logik**-, **Visualisierungs**- und **Datenerfassungsregeln**, die sich mit den wichtigsten Kundenherausforderungen von heute befassen. Intelligence Packs werden von Operational Insights Search zum Bereitstellen von Metriken unterstützt, die eine Pivotierung in Bezug auf einen bestimmten Problembereich bieten. Damit erhalten Sie detailliertere Informationen und können betriebliche Probleme schneller untersuchen und lösen, verschiedene Typen von Computerdaten sammeln und Zusammenhänge herstellen sowie Aktivitäten wie Kapazitätsplanung, Patchstatusberichte und Sicherheitsüberwachung proaktiv angehen. 

Dieser Leitfaden führt Sie durch die verschiedenen verfügbaren Intelligence Packs und deren Verwendungszwecke.

>[AZURE.NOTE] Weitere Informationen zum *Hinzufügen* von Intelligence Packs finden Sie unter [Hinzufügen von Intelligence Packs](operational-insights-add-intelligence-pack.md)

## SQL Assessment

Mit dem SQL Assessment Intelligence Pack bewerten Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer SQL Server-Umgebungen. Standardmäßig werden die SQL-Systeme wöchentlich gescannt, und die Informationen werden in Form eines monatlichen Rollups dargestellt. SQL Assessment bietet eine priorisierte Liste von Empfehlungen, die auf Ihre Bereitstellungen zugeschnitten sind. Diese Empfehlungen sind in sechs Schwerpunktbereiche eingeteilt, um Ihnen und Ihrem Team folgende Möglichkeiten zu bieten:

- **Schnelles** Erfassen der **Risiken und der Integrität** Ihrer Umgebungen
- Einfaches Ergreifen von **Maßnahmen** zum Verringern von Risiken und zur Verbesserung der Integrität
- Setzen von **Prioritäten** für Ihre Arbeit und Steigern der **Produktivität**

Für die Ausführung auf den einzelnen Agents erfordert SQL Assessment .NET 4. Es unterstützt die Standard, die Developer und die Enterprise Edition von SQL Server und damit alle derzeit unterstützten Versionen.
[Weitere Informationen finden Sie auf MSDN](https://msdn.microsoft.com/library/azure/dn873958.aspx)


## Configuration Assessment

Die Konfigurationsbewertung bietet Ihnen ausführliche Informationen über den aktuellen Zustand Ihrer Serverinfrastruktur. 

Im Gegensatz zu Intelligence Packs steht die Konfigurationsbewertung gleich bei der ersten Verwendung von Operational Insights mit System Center Operations Manager zur Verfügung. Sie ist nicht verfügbar, wenn Sie nur direkt verbundene Agents verwenden.

Konfigurationsdaten werden von überwachten Servern gesammelt und dann zur Verarbeitung an den Operational Insights-Dienst in der Cloud gesendet. Auf die empfangenen Daten wird Logik angewendet, und der Cloud-Dienst zeichnet die Daten auf. Verarbeitete Daten für die Server werden für die folgenden Bereiche angezeigt:

- **Warnungen:** Zeigt die konfigurationsbezogenen, proaktiven Warnungen an, die für Ihre überwachten Server ausgelöst wurden. Diese werden durch Regeln erstellt, die vom Microsoft-Kundendienst und -Support anhand bewährter Methoden aus der Praxis erstellt wurden.
- **Empfehlungen:** Zeigt die Artikel aus der Microsoft Knowledge Base an, die für die in Ihrer Infrastruktur vorgefundenen Arbeitsauslastungen empfohlen werden. Diese Artikel werden aufgrund Ihrer Konfiguration mithilfe von Machine Learning automatisch vorgeschlagen
- **Analysierte Server und Arbeitsauslastungen:** Zeigt die Server und die Arbeitsauslastungen an, die von Operational Insights überwacht werden
- **Aktuelle Momentaufnahme:** Zeigt die neuesten Informationen zu Servern an, die Daten an den Operational Insights-Dienst gemeldet haben.
- **Änderungsverlauf:** Zeigt eine Liste von Konfigurationsänderungen an, die an den überwachten Servern vorgenommen wurden

> [AZURE.WICHTIG] Die Konfigurationsbewertung kann nur für die von **Operations Manager** verwalteten Computer aktiviert werden.  Weitere Informationen finden Sie unter [Verbinden von Operations Manager](operational-insights-connect-scom.md)


## Malware Assessment
Wenn ein unzureichender Schutz festgestellt wird, werden Server mit aktiven Bedrohungen und Server mit unzureichendem Schutz auf der Seite **Malware** angezeigt. Anhand der Informationen auf der Seite **Malware** können Sie einen Plan zum Anwenden von Schutzfunktionen auf die betreffenden Server entwickeln.

> [AZURE.WICHTIG] Malware Assessment unterstützt derzeit nur die Echtzeitclients Windows Defender und System Center Endpoint Protection (SCEP). Wenn keiner dieser Clients gefunden wird, verwendet Malware Assessment die Daten aus dem Tool zum Entfernen bösartiger Software und markiert die Server ohne Echtzeitschutz. Weitere Informationen dazu finden Sie [hier](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519202-support-other-antivirus-products-in-malware-assess). Außerdem verfolgen wir [ein Problem mit Computern unter Windows 7 und Windows Server 2008 R2](http://feedback.azure.com/forums/267889-azure-operational-insights/suggestions/6519211-windows-server-2008-r2-sp1-servers-are-shown-as-n)





## Alert Management

Mit dem Alert Management Intelligence Pack können Sie Ihre **Operations Manager**-Warnungen für all Ihre Server anzeigen. Mit diesem Tool können Warnungen leicht selektiert und Ursachen in Ihrer Umgebung schnell und reibungslos identifiziert werden.

Sie erhalten Einblicke in wichtige Szenarien:

- Wie viele Warnungen wurden innerhalb eines angegebenen Zeitraums ausgelöst?
- Die Hauptquellen mit der höchsten Anzahl aktiver Warnungen innerhalb eines angegebenen Zeitraums.
- Die häufigsten aktiven kritischen Warnungen und Warnungen innerhalb eines angegebenen Zeitraums.
- Die Möglichkeit zum Durchsuchen aller Warnungen und zur Anzeige jeder einzelnen Warnung im Detail

> [AZURE.WICHTIG] Alert Management kann nur aktiviert werden, wenn Operational Insights in Verbindung mit **System Center Operations Manager** verwendet wird. Es werden keine Daten von den Agents gesendet. Stattdessen werden die Operations Manager-Warnungen einfach mit der Cloud synchronisiert, sodass Sie sie in Operational Insights selektieren und Search verwenden können. Weitere Informationen finden Sie unter [Verbinden von Operations Manager](operational-insights-connect-scom.md)


## Kapazitätsplanung

Mit dem Capacity Management Intelligence Pack in Microsoft Azure Operational Insights können Sie die Kapazität Ihrer Serverinfrastruktur erfassen. Das Intelligence Pack liest die Leistungsindikatoren auf dem überwachten Server und sendet Daten zur Verarbeitung an den Operational Insights-Dienst in der Cloud. Auf die Nutzungsdaten wird Logik angewendet, und der Cloud-Dienst zeichnet die Daten auf. Mit der Zeit entstehen Verwendungsmuster, und die Kapazität wird anhand des aktuellen Verbrauchs prognostiziert.

Beispielsweise könnte anhand einer Prognose ermittelt werden, wann zusätzliche Prozessorkerne oder zusätzlicher Arbeitsspeicher für einen einzelnen Server benötigt wird. In diesem Beispiel kann die Prognose darauf hinweisen, dass der Server in 30 Tagen zusätzlichen Arbeitsspeicher benötigen wird. Damit können Sie ein Speicherupgrade für das nächste Wartungsfenster des Servers planen, das zum Beispiel einmal alle zwei Wochen ansteht.

>[AZURE.WICHTIG] Capacity Management kann nur aktiviert werden, wenn Operational Insights in Verbindung mit **System Center Operations Manager** verwendet wird. Außerdem müssen Sie auch den Operations Manager-Connector mit Virtual Machine Manager (VMM) aktivieren. Weitere Informationen zum Verbinden von Systemen finden Sie unter [Verbinden von VMM mit Operations Manager](https://technet.microsoft.com/library/hh882396.aspx).

Weitere Informationen zur Verwendung des Capacity Management Intelligence Packs finden Sie unter:

- [Verwenden der Seite "Compute"](https://msdn.microsoft.com/library/azure/dn873965.aspx)
- [Verwenden der Seite "Direkt angeschlossener Speicher"](https://msdn.microsoft.com/library/azure/dn873954.aspx) 


## Change Tracking

Mit dem Intelligence Pack zur Nachverfolgung von Konfigurationsänderungen können Sie ganz einfach Änderungen an Software und Windows-Diensten identifizieren, die in Ihrer Umgebung vorgenommen werden - durch das Feststellen solcher Konfigurationsänderungen können Sie Betriebsprobleme leichter lokalisieren. Mithilfe der Informationen auf der Seite "Change Tracking" können Sie ganz leicht die Änderungen erkennen, die in Ihrer Serverinfrastruktur vorgenommen wurden. Sie können Änderungen an Ihrer Infrastruktur anzeigen und dann Einzelheiten für die folgenden Kategorien abrufen:

- Änderungen nach Konfigurationstyp für Software und Windows-Dienste
- Softwareänderungen an Anwendungen und Updates für einzelne Server
- Gesamtanzahl von Softwareänderungen für jede Anwendungen
- Änderungen am Windows-Dienst für einzelne Server


## System Update Assessment

Mithilfe des System Updates Intelligence Packs in Microsoft Azure Operational Insights können Sie fehlende Updates auf Server in Ihrer Infrastruktur anwenden. Wenn ermittelt wird, dass Updates fehlen, werden diese auf der Seite **Updates** angezeigt. Mithilfe der Seite **Updates** können Sie mit fehlenden Updates arbeiten und einen Plan für deren Anwendung auf die betreffenden Server entwickeln.

Die Seite "Updates" enthält die folgenden Kategorien:

- Server, auf denen Sicherheitsupdates fehlen
- Server, die nicht kürzlich aktualisiert wurden
- Updates, die auf bestimmte Server angewendet werden sollen
- Typ der fehlenden Updates

Klicken Sie auf eine beliebige Kachel oder ein Element, um Details dazu auf der Seite **Search** anzuzeigen und weitere Informationen zu dem fehlenden Update zu erhalten.  Die Suchergebnisse umfassen:

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







<!--HONumber=52-->