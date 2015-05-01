<properties 
   pageTitle="Überlegungen zu Operations Manager mit Operational Insights"
   description="Wenn Sie Microsoft Azure Operational Insights mit Operations Manager verwenden, basiert Ihre Konfiguration auf einer Verteilung von Operations Manager-Agents und Verwaltungsgruppen, um Daten zu sammeln und zur Analyse an den Operational Insights-Dienst zu senden"
   services="operational-insights"
   documentationCenter=""
   authors="bandersmsft"
   manager="jwhit"
   editor="tysonn" />
<tags 
   ms.service="operational-insights"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/20/2015"
   ms.author="banders" />

# Überlegungen zu Operations Manager mit Operational Insights

Wenn Sie Microsoft Azure Operational Insights mit Operations Manager verwenden, basiert Ihre Konfiguration auf einer Verteilung von Operations Manager-Agents und Verwaltungsgruppen, um Daten zu sammeln und zur Analyse an den Operational Insights-Dienst zu senden. Wenn Sie jedoch Agents verwenden, die sich direkt mit dem Webdienst verbinden, ist Operations Manager nicht notwendig. Berücksichtigen Sie die folgenden Probleme bei Ihrer Verwendung von Operational Insights mit Operations Manager.

## Funktionen und Anforderungen der Operational Insights-Software

Operative Insights besteht aus einem Webdienst in der Cloud und entweder Agents, die direkt mit dem Webdienst verbunden sind, oder Operations Manager-Agents und Operations Manager-Verwaltungsgruppen, die Computer in Ihrer Umgebung verwalten.

Bevor Sie Operations Manager-Agents (zum Sammeln von Daten) und Verwaltungsgruppen (zum Verwalten von Agents und Senden von Daten an den Operational Insights-Dienst) auswählen, sollten Sie Folgendes wissen:

- Der Operations Manager-Agent wird auf jedem Server installiert, auf dem Sie Daten sammeln und analysieren möchten.

- Die Operations Manager-Verwaltungsgruppe überträgt Daten von Agents an den Operational Insights-Webdienst. Sie analysiert keine Daten.

- Die Operations Manager-Verwaltungsgruppe benötigt Zugriff auf das Internet, um Daten an den Webdienst hochzuladen.

- Für ein optimales Ergebnis installieren Sie den Operations Manager-Verwaltungsserver nicht auf einem Computer, der auch als Domänencontroller dient.

- Ein Operations Manager-Agent muss über eine Netzwerkverbindung mit der Operations Manager-Verwaltungsgruppe verfügen, um Daten übertragen zu können.

Operational Insights kann den Integritätsdienst von System Center zum Sammeln und Analysieren von Daten verwenden. Operations Manager ist vom Integritätsdienst von System Center abhängig. Wenn Sie die Programme anzeigen, die auf dem Server installiert sind, sehen Sie die System Center Operations Manager-Agent-Software, insbesondere unter "Software". Entfernen Sie diese nicht, da Operational Insights davon abhängig ist. Wenn Sie die Operations Manager-Agent-Software entfernen, funktioniert Operational Insights nicht mehr.

## Koexistenz mit Operations Manager

Bei Verwendung von Operations Manager wird Operational Insights nur mit dem Operations Manager-Agent in System Center Operations Manager 2012 R2 oder System Center Operations Manager 2012 SP1 unterstützt. Es wird nicht mit früheren Versionen von System Center Operations Manager unterstützt. Da der Operations Manager-Agent zum Sammeln von Daten eingesetzt wird, verwendet er bestimmte Anmeldeinformationen (Aktionskonten oder ausführende Konten) zur Unterstützung einiger der analysierten Arbeitsauslastungen, z. B. SharePoint 2012.

## Operational Insights und SQL Server 2012

Wenn Sie Operations Manager verwenden, wird der Integritätsdienst von System Center unter dem lokalen Systemkonto ausgeführt. In SQL Server-Versionen vor SQL Server 2008 R2 war das lokale Systemkonto standardmäßig aktiviert und Mitglied der Systemadministrator-Serverrolle. In SQL Server 2012 ist die lokale Systemanmeldung nicht Teil der Systemadministrator-Serverrolle. Daher kann bei Verwendung von Operational Insights die SQL Server 2012-Instanz nicht vollständig überwacht werden, und nicht alle Regeln können Warnungen generieren.

## Internet und interne Netzwerkverbindungen

Bei Verwendung von Agents, die direkt mit dem Webdienst verbunden sind, müssen die Agents auf das Internet zugreifen können, um Daten an den Webdienst zu senden und aktualisierte Konfigurationsinformationen aus dem Webdienst zu erhalten.

Bei Verwendung von Operations Manager muss der Verwaltungsserver auf das Internet zugreifen können, um Daten an den Webdienst zu senden und aktualisierte Konfigurationsinformationen aus dem Webdienst zu erhalten. Allerdings müssen die Agents nicht auf das Internet zugreifen. Wenn Sie Operations Manager-Agents auf Servern verwenden, die nicht mit dem Internet verbunden sind, können Sie den Webdienst verwenden, wenn sie mit einem Verwaltungsserver mit Internetverbindung kommunizieren können.

## Unterstützung für Clustering

Der Operations Manager-Agent wird auf Computern unter Windows Server 2012, Windows Server 2008 R2 oder Windows Server 2008 unterstützt, die als Teil eines Windows-Failoverclusters konfiguriert sind. Die Cluster können im Operational Insights-Portal angezeigt werden. Auf der Seite "Server" werden Cluster als TYPE=CLUSTER identifiziert (im Gegensatz zu TYPE=AGENT, womit physische Computer gekennzeichnet werden).

Die Ermittlungs- und Konfigurationsregeln werden auf den aktiven und passiven Knoten des Clusters ausgeführt, die auf den passiven Knoten generierten Warnungen werden jedoch ignoriert. Wenn ein Knoten von passiv in aktiv geändert wird, werden Warnungen für den Knoten automatisch angezeigt, ohne dass ein Benutzereingriff erforderlich ist.

Einige Warnungen werden möglicherweise zweimal erstellt, abhängig von der Regel, die die Warnung generiert. Eine Regel, die durch Untersuchen des Betriebssystems einen fehlerhaften Treiber erkennt, generiert z. B. Warnungen für den physischen Server und für den Cluster.

Die Konfigurationsanalyse der passiven Knoten wird nicht unterstützt.

Operational Insights unterstützt nicht die Gruppierung oder Verknüpfung von Computern unter Windows Server, die demselben Failovercluster angehören.

## Skalieren Ihrer Operational Insights-Umgebung

Beachten Sie bei der Planung Ihrer Operational Insights-Bereitstellung die Kapazität des Servers im Hinblick auf dem Speicherplatz (insbesondere dann, wenn Sie die Anzahl der Operations Manager-Agents analysieren, die zum Übertragen von Daten über eine einzelne Verwaltungsgruppe verwendet werden sollen).

Berücksichtigen Sie die folgenden Variablen:

- Anzahl der Agents pro Verwaltungsgruppe

- Die durchschnittliche Größe der Daten, die pro Tag vom Agent an die Verwaltungsgruppe übertragen werden. Standardmäßig lädt jeder Agent zweimal pro Tag CAB-Dateien in die Verwaltungsgruppe hoch. Die Größe der CAB-Dateien hängt von der Konfiguration des Servers (z. B. von der Anzahl der SQL Server-Module und der Anzahl der Datenbanken) und von der Integrität des Servers ab (z. B. von der Anzahl der generierten Warnungen). In den meisten Fällen beträgt die tägliche Uploadgröße in der Regel weniger als 100 KB.

- Der Archivierungszeitraum für die Aufbewahrung von Daten in der Verwaltungsgruppe (der Standardwert ist 5 Tage)

Wenn Sie beispielsweise von einer täglichen Uploadgröße von 100 KB pro Agent und vom Standardarchivierungszeitraum ausgehen, benötigen Sie für die Verwaltungsgruppe den folgenden Speicherplatz:

<table border="1" cellspacing="4" cellpadding="4">
    <tbody>
    <tr align="left" valign="top">
		<td><b>Anzahl der Agents</b></td>
		<td><b>Geschätzter Speicherplatzbedarf für die Verwaltungsgruppe</b></td>
    </tr>
    <tr align="left" valign="top">
		<td>5</td>
		<td>~2,5 MB (5 Agents x 100 KB Daten/Tag x 5 Tage = 2.500 KB)</td>
    </tr>
    <tr align="left" valign="top">
		<td>50</td>
		<td>~25 MB (50 Agents x 100 KB Daten/Tag x 5 Tage = 25.000 KB)</td>
    </tr>

    </tbody>
    </table>

## Geografische Standorte

Wenn Sie Daten von Servern an unterschiedlichen geografischen Standorten analysieren möchten, sollten Sie die Verwendung einer Verwaltungsgruppe pro Standort in Betracht ziehen. Dadurch können Sie die Leistung der Datenübertragung vom Agent an die Verwaltungsgruppe verbessern.



<!--HONumber=52-->