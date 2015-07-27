<properties
   pageTitle="Integration von Cloud Cruiser und Microsoft Azure-Abrechnungs-API"
   description="Enthält eine einzigartige Sicht auf den Microsoft Azure-Abrechnungspartner Cloud Cruiser und dessen Umgebungen mit Integration der Azure-Abrechnungs-APIs in das Produkt. Dies ist besonders hilfreich für Azure- und Cloud Cruiser-Kunden, die an der Verwendung bzw. am Testen von Cloud Cruiser für Microsoft Azure Pack interessiert sind."
   services="billing"
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="06/17/2015"
   ms.author="mobandyo;sirishap;bryanla"/>

# Integration von Cloud Cruiser und Microsoft Azure-Abrechnungs-API 

In diesem Artikel wird beschrieben, wie die mit den neuen Microsoft Azure-Abrechnungs-APIs gesammelten Informationen in Cloud Cruiser für die Simulation und Analyse von Workflowkosten verwendet werden können.

## Azure-RateCard-API
Mit der RateCard-API werden Gebühren- und Preisinformationen aus Azure bereitgestellt. Nachdem Sie sich mit den richtigen Anmeldeinformationen authentifiziert haben, können Sie die API abfragen, um Metadaten zu den unter Azure verfügbaren Diensten sowie die Gebühren zu erfassen, die Ihrer Angebots-ID zugeordnet sind.

Unten ist ein Beispiel für eine Antwort der API mit den Preisen für die Instanz „A0 (Windows)“ angegeben:

    {       
		"MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",       
		"MeterName": "Compute Hours",       
		"MeterCategory": "Virtual Machines",       
		"MeterSubCategory": "A0 VM (Windows)",       
		"Unit": "Hours",       
		"MeterRates": 
		{         
			"0": 0.029       
		},       
		"EffectiveDate": "2014-08-01T00:00:00Z",       
		"IncludedQuantity": 0.0     
	}, 

## Schnittstelle von Cloud Cruiser für die Azure-RateCard-API
Cloud Cruiser kann die Informationen der RateCard-API auf unterschiedliche Weise nutzen. In diesem Artikel wird gezeigt, wie damit IaaS-Workload-Kostensimulationen und -Analysen durchgeführt werden können.

Um diesen Anwendungsfall zu demonstrieren, stellen wir uns eine Workload vor, bei der mehrere Instanzen unter Microsoft Azure Pack (WAP) ausgeführt werden. Das Ziel besteht darin, diese Workload unter Azure zu simulieren und die Kosten für eine Migration dieser Art zu schätzen. Zum Erstellen dieser Simulation müssen zwei Hauptaufgaben durchgeführt werden:

1. **Importieren und Verarbeiten der Dienstinformationen, die mit der RateCard-API erfasst werden** – Diese Aufgabe wird auch in den Arbeitsmappen durchgeführt, in denen die per RateCard-API extrahierten Daten transformiert und in einer neuen Gebührenübersicht veröffentlicht werden. Diese neue Gebührenübersicht wird in den Simulationen zum Schätzen der Azure-Preise verwendet.

2. **Normalisieren der WAP-Dienste und Azure-Dienste für IaaS** – Standardmäßig basieren WAP-Dienste auf einzelnen Ressourcen (CPU, Arbeitsspeichergröße, Datenträgergröße usw.), während Azure-Dienste auf der Instanzgröße basieren (A0, A1, A2 usw.). Diese erste Aufgabe kann vom ETL-Modul (als Arbeitsmappen bezeichnet) von Cloud Cruiser durchgeführt werden, wobei diese Ressourcen analog zu Azure-Instanzdiensten für Instanzgrößen gebündelt werden können.

## Importieren von Daten aus der RateCard-API

Cloud Cruiser-Arbeitsmappen sind eine Möglichkeit, Informationen aus der RateCard-API auf automatisierte Weise zu erfassen und zu verarbeiten. Mit ETL-Arbeitsmappen (Extrahieren-Transformieren-Laden) können Sie die Sammlung, Transformation und Veröffentlichung von Daten in der Cloud Cruiser-Datenbank konfigurieren.

Jede Arbeitsmappe kann über eine oder mehrere Sammlungen verfügen. So können Sie Informationen aus unterschiedlichen Quellen korrelieren, um die Nutzungsdaten zu erweitern oder zu verbessern. In den beiden folgenden Screenshots sind die Erstellung einer neuen *Sammlung* in einer vorhandenen Arbeitsmappe und das Importieren in die *Sammlung* aus der RateCard-API dargestellt:

![Abbildung 1: Erstellen einer neuen Sammlung][1]

![Abbildung 2: Importieren von Daten aus der neuen Sammlung][2]

Nach dem Importieren der Daten in die Arbeitsmappe können Sie mehrere Schritte und Transformationsprozesse erstellen, um die Daten zu ändern und zu modellieren. Da wir nur an Infrastructure-as-a-Service (IaaS) interessiert sind, können wir in diesem Beispiel die Transformationsschritte zum Entfernen von nicht benötigten Zeilen oder Datensätzen verwenden, die sich auf andere Dienste als IaaS beziehen.

Im folgenden Screenshot sind die Transformationsschritte zu sehen, die zum Verarbeiten von mit der RateCard-API erfassten Daten verwendet werden:

![Abbildung 3: Transformationsschritte zum Verarbeiten von gesammelten Daten aus der RateCard-API][3]

## Definieren neuer Dienste und Gebührenübersichten

Es gibt verschiedene Möglichkeiten, in Cloud Cruiser Dienste zu definieren. Eine der Optionen ist das Importieren der Dienste aus den Nutzungsdaten. Diese Methode wird häufig verwendet, wenn mit Public Clouds gearbeitet wird, bei denen die Dienste bereits vom Anbieter definiert wurden.

Eine Gebührenübersicht umfasst eine Reihe von Gebühren oder Preisen, die z. B. auf unterschiedliche Dienste (basierend auf Gültigkeitsdaten) oder Kundengruppen angewendet werden können. Außerdem können Gebührenübersichten in Cloud Cruiser zum Erstellen von Simulationen oder Was-wäre-wenn-Szenarien verwendet werden, um zu ermitteln, wie sich Änderungen von Diensten auf die Gesamtkosten einer Workload auswirken können.

In diesem Beispiel verwenden wir die Dienstinformationen der RateCard-API, um in Cloud Cruiser neue Dienste zu definieren. Ebenso können wir die Gebühren, die den Diensten zugeordnet sind, zum Erstellen einer neuen Gebührenübersicht in Cloud Cruiser verwenden.

Am Ende des Transformationsvorgangs ist es möglich, einen neuen Schritt zu erstellen und die Daten der RateCard-API als neue Dienste und Gebühren zu veröffentlichen.

![Abbildung 4: Veröffentlichen der Daten aus der RateCard-API als neue Dienste und Gebühren][4]

## Überprüfen von Azure-Diensten und -Gebühren

Nachdem Sie die Dienste und Gebühren veröffentlicht haben, können Sie die Liste mit den importierten Diensten in Cloud Cruiser auf der Registerkarte *Dienste* überprüfen:

![Abbildung 5: Überprüfen der neuen Dienste][5]

Auf der Registerkarte *Gebührenübersichten* können Sie die neue Gebührenübersicht mit dem Namen „AzureSimulation“ mit den aus der RateCard-API importierten Gebühren überprüfen.

![Abbildung 6: Überprüfen der neuen Gebührenübersicht und der zugeordneten Gebühren][6]

## Normalisieren von WAP- und Azure-Diensten

Standardmäßig werden von WAP Nutzungsinformationen basierend auf den Server-, Arbeitsspeicher- und Netzwerkressourcen bereitgestellt. In Cloud Cruiser können Sie Ihre Dienste direkt basierend auf der Zuweisung oder der gemessenen Nutzung dieser Ressourcen definieren. Beispielsweise können Sie eine Grundgebühr für jede Stunde der CPU-Nutzung festlegen oder die GB-Menge des Arbeitsspeichers berechnen, der einer Instanz zugeordnet ist.

In diesem Beispiel müssen wir zum Vergleichen der Kosten für WAP und Azure die Ressourcennutzung unter WAP in Bündeln zusammenfassen, die dann Azure-Diensten zugeordnet werden können. Die Implementierung dieser Transformation in die Arbeitsmappen ist einfach:

![Abbildung 7: Transformieren von WAP-Daten zum Normalisieren von Diensten][7]

Der letzte Schritt für die Arbeitsmappe ist die Veröffentlichung der Daten in der Cloud Cruiser-Datenbank. Während dieses Schritts werden die Nutzungsdaten jetzt zu Diensten zusammengefasst (mit Zuordnung zu den Azure-Diensten) und mit Standardgebühren verknüpft, um die Gebühren zu erstellen.

Nach Fertigstellung der Arbeitsmappe können Sie die Verarbeitung der Daten automatisieren, indem Sie im Scheduler eine neue Aufgabe hinzufügen und die Häufigkeit und Uhrzeit für die Ausführung der Arbeitsmappe angeben.

![Abbildung 8: Planen von Arbeitsmappen][8]

## Erstellen von Berichten für die Workload-Kostensimulationsanalyse

Da die Nutzung erfasst und die Gebühren in die Cloud Cruiser-Datenbank geladen werden, können wir das Cloud Cruiser Insights-Modul nutzen – ein erweitertes Berichterstellungstool für Analysen –, um die gewünschte Workload-Kostensimulation zu erstellen.

Zum Demonstrieren dieses Szenarios haben wir den folgenden Bericht erstellt:

![Kostenvergleich][9]

Das obere Diagramm zeigt einen Kostenvergleich unterteilt nach Diensten. Darin wird der Preis für die Ausführung der Workload für jeden speziellen Dienst bezogen auf WAP (dunkelblau) und Azure (hellblau) verglichen.

Das untere Diagramm zeigt dieselben Daten, die jetzt jedoch nach Abteilung unterteilt sind. Es werden die Kosten dargestellt, die für jede Abteilung beim Ausführen der Workload unter WAP und Azure anfallen, sowie die jeweilige Differenz (grüner Balken zu Einsparungen).

## Nächste Schritte

+ Eine ausführliche Anleitung zur Erstellung von Cloud Cruiser-Arbeitsmappen und -Berichten finden Sie in der Cloud Cruiser-[Onlinedokumentation](http://docs.cloudcruiser.com/) (gültige Anmeldung erforderlich). Weitere Informationen zu Cloud Cruiser erhalten Sie, indem Sie sich an [info@cloudcruiser.com](mailto:info@cloudcruiser.com) wenden.
+ Eine Übersicht über die Azure-APIs zur Ressourcennutzung und Gebührenkarte (Resource Usage und RateCard) finden Sie unter [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](billing-usage-rate-card-overview.md). 
+ Ausführlichere Informationen zu beiden APIs finden Sie unter [Azure-Abrechnungs-REST-API – Referenz](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c). Sie sind Teil der APIs, die vom Azure-Ressourcen-Manager bereitgestellt werden.
+ Wenn Sie direkt in den Beispielcode eintauchen möchten, können Sie dies unter [Codebeispiele für Microsoft Azure-Abrechnungs-API bei GitHub](https://github.com/Azure/BillingCodeSamples) tun.

## Weitere Informationen
+ Weitere Informationen zum Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).

<!--Image references-->
[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Abbildung 1: Erstellen einer neuen Sammlung"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Abbildung 2: Importieren von Daten aus der neuen Sammlung"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Abbildung 3: Transformationsschritte zum Verarbeiten von gesammelten Daten aus der RateCard-API"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Abbildung 4: Veröffentlichen der Daten aus der RateCard-API als neue Dienste und Gebühren"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Abbildung 5: Überprüfen der neuen Dienste"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Abbildung 6: Überprüfen der neuen Gebührenübersicht und der zugeordneten Gebühren"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Abbildung 7: Transformieren von WAP-Daten zum Normalisieren von Diensten"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Abbildung 8: Planen von Arbeitsmappen"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Abbildung 9: Beispielbericht für das Workload-Kostenvergleichsszenario"

<!---HONumber=July15_HO3-->