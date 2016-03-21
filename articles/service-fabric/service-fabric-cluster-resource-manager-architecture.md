<properties
   pageTitle="Architektur des Ressourcen-Managers | Microsoft Azure"
   description="Übersicht über die Architektur des Clusterressourcen-Managers von Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Übersicht über die Architektur des Clusterressourcen-Managers
Um die Ressourcen in Ihrem Cluster zu verwalten, benötigt der Clusterressourcen-Manager verschiedene Informationen. Er muss wissen, welche Dienste derzeit vorhanden sind und wie viele Ressourcen der jeweilige Dienst aktuell (oder standardmäßig) belegt. Eine weitere Information ist die tatsächliche Kapazität der Knoten im Cluster und somit die Anzahl der verfügbaren Ressourcen, sowohl im Cluster als Ganzes und als auch auf einem bestimmten Knoten. Wir müssen uns mit der Tatsache befassen, dass sich die Ressourcennutzung eines bestimmten Diensts mit der Zeit ändern kann und dass Dienste meist mehr als nur eine Ressource benötigen. Bei vielen unterschiedlichen Diensten kann es sowohl „reale“ Ressourcen wie Arbeitsspeicher und Datenträger, die in der Regel von vielen verschiedenen Diensttypen gemeinsam genutzt werden, als auch Ressourcen geben, die nur für einen bestimmten Dienst vorgesehen sind.

Erschwerend kommt die Tatsache hinzu, dass sich die Besitzer und Betreiber des Clusters gelegentlich von den Diensterstellern unterscheiden. Möglich ist auch, dass es sich um dieselben Personen mit unterschiedlichen Rollen handelt. Wenn Sie beispielsweise Ihren Dienst entwickeln, kennen Sie sich mit den Ressourcenanforderungen und der idealen Bereitstellung von Komponenten ungefähr aus. Doch als Zuständiger für Incidents auf einer Live-Website für diesen Dienst in der Produktion haben Sie andere Aufgaben, für die Sie andere Tools brauchen. Darüber hinaus sind weder der Cluster noch die Dienste selbst statisch konfiguriert: die Anzahl der Knoten im Cluster kann zu- und abnehmen, Knoten verschiedener Größen können hinzukommen oder entfernt werden, und Dienste können eine andere Ressourcenzuteilung erhalten, erstellt und entfernt werden. Upgrades oder andere Verwaltungsvorgänge können im Cluster erfolgen, und natürlich können Komponenten jederzeit ausfallen.

Unser Ressourcen-Manager muss viele Dinge über den gesamten Cluster selbst wissen und auch die Anforderungen bestimmter Dienste kennen. Um dies zu erreichen, bietet Service Fabric sowohl Agents des Ressourcen-Managers, die auf einzelnen Knoten zum Sammeln von Informationen zur lokalen Ressourcennutzung ausgeführt werden, als auch einen zentralen, fehlertoleranten Ressourcen-Manager, der alle Informationen zu den Diensten und zum Cluster sammelt und auf Änderungen basierend auf der Konfiguration des gewünschten Zustands des Clusters und Diensts reagiert. Die Fehlertoleranz wird exakt mithilfe des Mechanismus erreicht, den wir für unsere Dienste nutzen, nämlich der Replikation des Zustands des Diensts in verschiedene Replikate (meist 7).

![Resource Balancer-Architektur][Image1]

Werfen Sie einen Blick auf dieses Diagramm (Abb. 1) als Beispiel. Zur Laufzeit können viele verschiedene Änderungen auftreten. Beispielsweise können Änderungen an der Menge der Ressourcen vorkommen, die Dienste nutzen, Dienstausfälle können passieren, einige Knoten können dem Cluster hinzugefügt oder daraus entfernt werden usw. Alle Änderungen auf einem bestimmten Knoten werden gesammelt und regelmäßig an den zentralen Ressourcen-Manager-Dienst (1,2) gesendet, wo eine Aggregation, Analyse und Speicherung erfolgt. Alle paar Sekunden untersucht der zentrale Dienst alle Änderungen und ermittelt, ob Aktionen erforderlich sind (3). Er könnte z. B. feststellen, dass Knoten dem Cluster hinzugefügt wurden und leer sind und entscheiden, einige Dienste auf diese Knoten zu verschieben. Er könnte aber auch bemerken, dass ein bestimmter Knoten überlastet ist oder dass bestimmte Dienste ausgefallen sind (oder gelöscht wurden), und Ressourcen auf anderen Knoten freigeben.

Lassen Sie uns das nächste Diagramm untersuchen (Abb. 2), um festzustellen, was hier passiert. Angenommen, der Ressourcen-Manager stellt fest, dass Änderungen erforderlich sind. Er koordiniert sich mit anderen Systemdiensten (insbesondere dem Failover-Manager), um die erforderlichen Änderungen vorzunehmen. Anschließend werden die Änderungsanforderungen an die entsprechenden Knoten gesendet (4). In diesem Fall nehmen wir an, dass der Ressourcen-Manager bemerkt hat, dass Knoten 5 überlastet war, und daher entschieden hat, Dienst B von N5 auf N4 zu verschieben. Am Ende der Neukonfiguration (5) sieht der Cluster folgendermaßen aus:

![Resource Balancer-Architektur][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Informationen zur Architektur des Clusterressourcen-Managers](service-fabric-cluster-resource-manager-architecture.md)
- [Beschreiben des Clusters](service-fabric-cluster-resource-manager-cluster-description.md)

[Image1]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]: ./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png

<!---HONumber=AcomDC_0309_2016-->