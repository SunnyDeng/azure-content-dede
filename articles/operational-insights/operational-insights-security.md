<properties 
	pageTitle="Sicherheit von Operational Insights" 
	description="Operational Insights ist ein Analyse-Dienst, der IT-Administratoren einen tiefen Einblick in lokale und Cloud-Umgebungen gewährt. Die Interaktion mit Echtzeit- und historischen Computerdaten sorgt für die schnelle Entwicklung benutzerdefinierter Einblicke; bereitgestellt werden außerdem Muster für die Datenanalyse, die von Microsoft und der Community entwickelt wurden." 
	services="operational-insights" 
	documentationCenter="" 
	authors="bandersmsft" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="operational-insights" 
	ms.workload="appservices" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="banders"/>





<h1 id="">Sicherheit von Operational Insights</h1>


Microsoft ist bestrebt, bei der Bereitstellung von Software und Diensten, mit denen Sie die IT-Infrastruktur Ihres Unternehmens verwalten, Ihre Privatsphäre und Ihre Daten zu schützen. Wenn Sie Ihre Daten anderen anvertrauen, setzt dieses Vertrauen strikte Sicherheit voraus. Das wissen wir. Microsoft hält strenge Compliance- und Sicherheitsrichtlinien ein - angefangen bei der Codierung bis hin zum Betreiben von Diensten.

Das Sichern und Schützen von Daten hat bei Microsoft oberste Priorität. Wenden Sie sich an uns, wenn Sie Fragen, Vorschläge oder Probleme bezüglich der folgenden Informationen (einschließlich unserer Sicherheitsrichtlinien) haben: <a href="mailto:scdata@microsoft.com" target="_blank">scdata@microsoft.com</a>.

In diesem Artikel wird erläutert, wie Daten in Microsoft Azure Operational Insights gesammelt, verarbeitet und geschützt werden. Sie können entweder Agents mit direkter Verbindung zum Webdienst oder System Center Operations Manager verwenden, um betriebliche Daten für den Operational Insights-Dienst zu sammeln. Die gesammelten Daten werden über das Internet zum Operational Insights-Dienst gesendet, der in Microsoft Azure gehostet wird.

Der Operational Insights-Dienst verwaltet Ihre Daten sicher mithilfe der folgenden Methoden:

**Trennung von Daten:** Die Kundendaten werden für jede Komponente im gesamten Operational Insights-Dienst logisch getrennt gehalten. Alle Daten werden nach Unternehmen markiert. Diese Markierung bleibt während des gesamten Lebenszyklus der Daten bestehen und wird auf jeder Ebene des Dienstes erzwungen. 

Jeder Kunde verfügt über einen dedizierten Azure-Blob (Speicher), der die langfristigen Daten enthält. Dieser Blob wird verschlüsselt. Dazu werden kundenbezogene, eindeutige Schlüssel verwendet, die alle 90 Tage geändert werden.

**Datenaufbewahrung:** Die aggregierten Messwerte werden für jeden Intelligence Pack in einer SQL-Datenbank gespeichert, die von Microsoft Azure gehostet wird. Diese Daten werden 390 Tage lang gespeichert. Indizierte Suchdaten werden durchschnittlich 10 Tage lang gespeichert, bevor sie aktualisiert werden. Wenn der obere Grenzwert von 20 Millionen Datensätzen für den einzelnen Datentyp früher erreicht wird, aktualisiert Operational Insights die Daten vor dem Ablauf von 10 Tagen. Wird der Grenzwert innerhalb von 10 Tagen nicht erreicht, wartet Operational Insights mit der Aktualisierung, bis der Grenzwert erreicht ist.

**Physische Sicherheit:** Der Operational Insights-Dienst ist mit Microsoft-Mitarbeitern besetzt. Alle Aktivitäten werden protokolliert und können überwacht werden. Der Operational Insights-Dienst wird vollständig in Azure ausgeführt und entspricht dem Standard "Windows Common Engineering Criteria". Details zur physischen Sicherheit der Azure-Elemente finden Sie auf Seite 18 der Abhandlung  <a href="http://download.microsoft.com/download/6/0/2/6028B1AE-4AEE-46CE-9187-641DA97FC1EE/Windows%20Azure%20Security%20Overview%20v1.01.pdf" target="_blank">"Microsoft Azure Security Overview"</a>.

**Compliance und Zertifizierungen:** Das Softwareentwicklungs- und Serviceteam für Operational Insights arbeitet aktiv mit den Microsoft Legal- und Compliance-Teams und anderen Partnern der Branche zusammen, um eine Vielzahl von Zertifizierungen (einschließlich ISO) zu erhalten, bevor der Operational Insights-Dienst allgemein verfügbar ist.

Derzeit erfüllen wir die folgenden Sicherheitsstandards:

- Windows Common Engineering Criteria
- Microsoft Trustworthy Computing Certification



<!--HONumber=45--> 
 