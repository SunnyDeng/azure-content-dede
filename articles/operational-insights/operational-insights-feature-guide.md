<properties
	pageTitle="Operational Insights-Funktionsleitfaden"
	description="Operational Insights ist ein Analyse-Dienst, der IT-Administratoren einen tiefen Einblick in lokale und Cloud-Umgebungen gewährt. Die Interaktion mit Echtzeit- und historischen Computerdaten sorgt für die schnelle Entwicklung benutzerdefinierter Einblicke; bereitgestellt werden außerdem Muster für die Datenanalyse, die von Microsoft und der Community entwickelt wurden."
	services="operational-insights"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="operational-insights"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/06/2015"
	ms.author="banders"/>

# Operational Insights-Funktionsleitfaden

[AZURE.INCLUDE [operational-insights-note-moms](../../includes/operational-insights-note-moms.md)]

In diesem Artikel wird erläutert, welche Probleme Sie mithilfe von Operational Insights lösen können, aus welchen Elementen die Operational Insights-Umgebung besteht und wie Sie ein Operational Insights-Konto erstellen und sich beim Dienst anmelden.

## Transformieren von Computerdaten

Operational Insights ist ein Analyse-Dienst, der IT-Administratoren einen tiefen Einblick in lokale und Cloud-Umgebungen gewährt. Die Interaktion mit Echtzeit- und historischen Computerdaten sorgt für die schnelle Entwicklung benutzerdefinierter Einblicke; bereitgestellt werden außerdem Muster für die Datenanalyse, die von Microsoft und der Community entwickelt wurden.

Mit Operational Insights können Sie mithilfe der folgenden Funktionen Computerdaten in operative Informationen umwandeln.


|**Symbol** | **Funktion** | **Funktionsbeschreibung**|
|---|---|---|
|![](./media/operational-insights-feature-guide/cap-plan.png) | [Capacity Planning](operational-insights-capacity.md) | Mit der Lösung "Capacity Planning" in Microsoft Azure Operational Insights können Sie die Kapazität Ihrer Serverinfrastruktur analysieren. |
| ![](./media/operational-insights-feature-guide/update.png) | [System Update Assessment](operational-insights-updates.md) | Mithilfe der Lösung "System Updates" in Microsoft Azure Operational Insights können Sie fehlende Updates auf Server in Ihrer Infrastruktur anwenden. |
| ![](./media/operational-insights-feature-guide/log-search.png) | [Protokollsuche](operational-insights-search.md) | Sie können auch die Protokollsuche verwenden, um für Ihre Ergebnisse Abfragen zum Transformieren, Filtern und Erstellen von Berichten zu erstellen. Die Suche verwendet Ereignisdaten und IIS-Protokolle für Protokollsuchvorgänge in Operational Insights. |
| ![](./media/operational-insights-feature-guide/malware.png) | [Malware Assessment](operational-insights-antimalware.md) | Mit der Antischadsoftware-Lösung in Microsoft Azure Operational Insights können Sie die Server in Ihrer Infrastruktur vor Schadsoftware schützen. |
| ![](./media/operational-insights-feature-guide/sec-audit.png) | [Security and Audit](operational-insights-security-audit.md) | Mit der Lösung "Security and Audit" erhalten Sie mit integrierten Suchabfragen für relevante Probleme, die Ihre Aufmerksamkeit erfordern, einen umfassenden Einblick in die Lage der IT-Sicherheit Ihres Unternehmens. |
| ![](./media/operational-insights-feature-guide/assessment.png) | [Active Directory and SQL Assessment](operational-insights-assessment.md) | Mit den Assessment-Lösungen bewerten Sie in regelmäßigen Abständen die Risiken und die Integrität Ihrer Serverumgebungen. |
| ![](./media/operational-insights-feature-guide/alert.png) | [Alert Management](operational-insights-alerts.md) | Die Lösung "Alert Management" können Sie zum Verwalten von Warnungen von Servern verwenden, die mit Operations Manager überwacht werden. |


Weitere Funktionen:

- **Senden von Computerdaten an das System mit oder ohne Verwendung eines Agents oder in Verbindung mit System Center Operations Manager**. Weitere Informationen finden Sie unter:
	- [Verbinden mit Operational Insights von System Center Operations Manager aus](operational-insights-connect-scom.md)
	- [Direktes Verbinden von Computern mit Operational Insights](operational-insights-direct-agent.md)
	- [Analysieren der Daten von Servern in Microsoft Azure](operational-insights-analyze-data-azure.md)
- **Ausführen aller oben genannten Funktionen mit der mobilen Anwendung von unterwegs**
	- Weitere Informationen über die Windows Phone-Anwendung finden Sie unter [Operational Insights Mobile App](http://www.windowsphone.com/de-de/store/app/operational-insights/4823b935-83ce-466c-82bb-bd0a3f58d865) (in englischer Sprache).

## Operational Insights-Umgebung

Die Operational Insights-Umgebung besteht aus folgenden Komponenten:

- Arbeitsbereiche, die von Microsoft Azure gehostet werden und Container für Azure-Konten sind
- Webdienst von Operational Insights, der in der Cloud gehostet wird
- Separate Agents, die eine direkte Verbindung zum Webdienst herstellen,
- oder ein Dienst, der an System Center Operations Manager angefügt ist (nicht unbedingt erforderlich)


Falls Sie die vorherige Version von Operationel Insights, genannt System Center Advisor, verwenden, ist möglicherweise Advisor-Software in Ihrer lokalen Umgebung installiert. Advisor-Software wird jedoch mit Operational Insights nicht unterstützt.

Wenn Sie Operational Insights-Software als Operations Manager-Dienst verwenden, enthält die Umgebung mindestens eine Verwaltungsgruppe und mindestens einen Agent pro Verwaltungsgruppe. Die Operations Manager-Agents sammeln Daten von Ihren Servern und analysieren diese mithilfe von Lösungen (vergleichbar den Management Packs in System Center Operations Manager). Die Daten werden regelmäßig von Operations Manager an den Operational Insights-Webdienst gesendet (ggf. über einen Proxyserver), ohne dass sie in der Operations Manager-Datenbanken gespeichert werden. Die Datenbanken müssen also keine zusätzlichen Lasten aufnehmen.

Auf ähnliche Weise können die auf den einzelnen Computern installierten Agents eine direkte Verbindung zum Webdienst herstellen, um die gesammelten Daten zur Verarbeitung zu senden.

Die Daten in den einzelnen Lösungen werden analysiert, indiziert und im Operational Insights-Portal dargestellt. Sie können alle Warnungen und die entsprechenden Abhilfemaßnahmen, Konfigurationsbewertungen, Kapazitätsprobleme der Infrastruktur, Status der Systemupdates, Antischadsoftware-Warnungen und Protokolldaten anzeigen. Sie können auch detaillierte Ad-hoc-Protokollsuchvorgänge und Untersuchungen ausführen.

![Bild des Operational Insights-Übersichtsdiagramms](./media/operational-insights-feature-guide/environment.png)

### Wo ist Operational Insights verfügbar?
Microsoft Azure Operational Insights wird in den USA gehostet. Obwohl die Sprache von Operational Insights Englisch ist, ist der Dienst auf einer Reihe weiterer Märkte verfügbar. Weitere Informationen finden Sie unter [Internationale Verfügbarkeit](http://go.microsoft.com/fwlink/?LinkId=229842).

<!---HONumber=August15_HO7-->