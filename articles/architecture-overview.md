<properties linkid="develop-net-architecture sublanding" urlDisplayName="" pageTitle="Architecture" metaKeywords="" description="Architecture overview that covers common design patterns" metaCanonical="" services="" documentationCenter="" videoId="" scriptId="" title="Architecture Overview" authors="robb" solutions="" manager="johndaw" editor="mattshel" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robb"></tags>

# Architektur

Erfahren Sie, wie Sie häufig verwendete Entwurfsmuster in Azure implementieren können.

### Symbolsatz für Azure

[Laden Sie den Symbolsatz für Azure herunter][Laden Sie den Symbolsatz für Azure herunter], um technisches Informationsmaterial im Stil von Azure zu erstellen, beispielsweise Architekturdiagramme, Schulungsmaterialien, Präsentationen, Datenblätter, Infografiken und Whitepaper. Sie können die Symbole in den Formaten PPT, Visio oder PNG herunterladen. Ihr Feedback ist uns sehr wichtig. Daher ist im Download eine Feedback-Anleitung enthalten.

![Symbolsatz für Azure][Symbolsatz für Azure]

## Entwurfsmuster

### [Konkurrierende Consumer][Konkurrierende Consumer]

![Konkurrierende Consumer][1]

Mehrere nebenläufige Consumer können Nachrichten verarbeiten, die über denselben Nachrichtenkanal eingegangen sind. Dieses Muster ermöglicht ein System, mit dem mehrere Nachrichten gleichzeitig verarbeitet werden können. So wird der Durchsatz optimiert, Skalierbarkeit und Verfügbarkeit werden erhöht und die Arbeitsauslastung wird ausgeglichen.

### [Zuständigkeitstrennung für Befehle und Abfragen][Zuständigkeitstrennung für Befehle und Abfragen]

![Zuständigkeitstrennung für Befehle und Abfragen][2]

Trennen Sie Vorgänge, die Daten lesen, von Vorgängen, die Daten aktualisieren, durch unterschiedliche Schnittstellen. Mit diesem Muster können Sie Leistung, Skalierbarkeit und Sicherheit optimieren. Sie erreichen dank höherer Flexibilität im Laufe der Zeit eine Weiterentwicklung des Systems. Außerdem können Sie verhindern, dass Aktualisierungsbefehle auf Domänenebene Zusammenführungskonflikte auslösen.

### [Auswahl einer übergeordneten Instanz][Auswahl einer übergeordneten Instanz]

![Auswahl einer übergeordneten Instanz][3]

Sie können die Aktionen koordinieren, die eine Sammlung zusammenarbeitender Aufgabeninstanzen in einer dezentralen Anwendung ausführt. Wählen Sie eine übergeordnete Instanz aus, die für die Verwaltung der anderen Instanzen zuständig ist. Mit diesem Muster können Sie sicherstellen, dass Aufgabeninstanzen nicht miteinander in Konflikt geraten, bei gemeinsam genutzten Ressourcen Konflikte auslösen oder unabsichtlich Vorgänge stören, die andere Aufgabeninstanzen durchführen.

### [Pipes und Filter][Pipes und Filter]

![Pipes und Filter][4]

Sie können eine Aufgabe, die komplexe Verarbeitung umfasst, in einer Reihe separater Elemente zerlegen, die erneut verwendet werden können. Mit diesem Muster können Sie Leistung, Skalierbarkeit und Wiederverwendbarkeit optimieren, indem Aufgabenelemente, die für die Verarbeitung zuständig sind, unabhängig bereitgestellt und skaliert werden.

### [Eingeschränkter Schlüssel][Eingeschränkter Schlüssel]

![Eingeschränkter Schlüssel][5]

Sie können ein Token oder einen Schlüssel verwenden, um Clients eingeschränkten direkten Zugriff auf eine bestimmte Ressource oder einen bestimmten Dienst zu gewähren. Dies dient dazu, Datenübertragungsoperationen aus dem Anwendungscode auszulagern. Dieses Muster ist besonders hilfreich bei Anwendungen, die in der Cloud gehostete Speichersysteme oder Warteschlangen verwenden. Sie können außerdem Kosten minimieren sowie Skalierbarkeit und Leistung maximieren.

### Zusätzliche Anleitungen

Informationen zu weiteren Entwicklungsmustern in Azure finden Sie unter [Cloudentwicklungsmuster][Cloudentwicklungsmuster].

  [Laden Sie den Symbolsatz für Azure herunter]: http://www.microsoft.com/de-de/download/details.aspx?id=41937
  [Symbolsatz für Azure]: ./media/architecture-overview/AzureSymbols.png
  [Konkurrierende Consumer]: http://msdn.microsoft.com/de-de/library/dn568101.aspx
  [1]: ./media/architecture-overview/CompetingConsumers.png
  [Zuständigkeitstrennung für Befehle und Abfragen]: http://msdn.microsoft.com/de-de/library/dn568103.aspx
  [2]: ./media/architecture-overview/CQRS.png
  [Auswahl einer übergeordneten Instanz]: http://msdn.microsoft.com/de-de/library/dn568104.aspx
  [3]: ./media/architecture-overview/LeaderElection.png
  [Pipes und Filter]: http://msdn.microsoft.com/de-de/library/dn568100.aspx
  [4]: ./media/architecture-overview/PipesAndFilters.png
  [Eingeschränkter Schlüssel]: http://msdn.microsoft.com/de-de/library/dn568102.aspx
  [5]: ./media/architecture-overview/ValetKey.png
  [Cloudentwicklungsmuster]: http://msdn.microsoft.com/de-de/library/dn568099.aspx
