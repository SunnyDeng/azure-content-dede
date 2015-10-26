<properties
   pageTitle="Melden der dynamischen Auslastung"
   description="Sie erhalten einen Überblick über das Melden der dynamischen Auslastung an den Resource Balancer."
   services="service-fabric"
   documentationCenter=".net"
   authors="GaugeField"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="masnider"/>

# Melden der dynamischen Auslastung – Übersicht

Zur Laufzeit können zustandsbehaftete und zustandslose Dienstobjekte die Auslastung über die ReportLoad-Methode melden (Member der Schnittstellen IStatefulServicePartition und IStatelessServicePartition). Das Melden von Auslastungswerten zur Laufzeit ist wichtig, weil damit das genaue Packen von Diensten in die Knoten ermöglicht wird. Außerdem wird sichergestellt, dass die Ressourcennutzung vom zentralen Service Fabric Resource Balancer so präzise nachverfolgt wird, wie sie für die Dienste auf den Knoten abläuft.

Beachten Sie Folgendes: Wenn ein Replikat seine Auslastung meldet, wird diese Meldung zuerst mit anderen Auslastungsberichten lokal zusammengefasst, und anschließend wird ein gemeinsamer Bericht an den Resource Balancer gesendet. Dieser Prozess bedeutet, dass beim wiederholten und sehr schnellen Melden der Auslastung durch einen Dienst nur der letzte Bericht tatsächlich an den Resource Balancer gesendet wird.

Wenn der Service Fabric Resource Balancer ausgeführt wird, untersucht er die gesamten Auslastungsinformationen, die von allen Knoten zusammengefasst werden, und führt einige Überprüfungen durch. Zu diesen Überprüfungen gehören der Ausgleichsschwellenwert und der Aktivitätsschwellenwert für die Metriken gemäß der Definition im Clustermanifest. Damit wird bestimmt, ob die Unausgeglichenheit des Clusters ausreicht, um den Resource Balancer auszuführen. Außerdem wird ermittelt, ob für einen bestimmten Knoten die Kapazität für eine der Metriken überschritten wurde, für die eine Kapazität festgelegt wurde. Bei einer Kapazitätsüberschreitung werden vom Resource Balancer zuerst nur die Dienste auf dem Knoten bzw. den Knoten mit Kapazitätsüberschreitung berücksichtigt, von denen die überschreitende Metrik gemeinsam genutzt wird. Bei einer Unausgeglichenheit des Clusters berücksichtigt der Resource Balancer alle Dienste, die über Metriken mit Diensten verknüpft sind, von denen die unausgeglichene Metrik gemeldet wird. Wenn der Service Fabric Resource Balancer ermittelt, dass eine dieser Situationen eingetreten ist, führt er eine Simulation aus. Damit wird versucht, eine bessere Anordnung der Dienste zu erzielen.

Die Dienste sollten die Auslastung immer melden, wenn sich diese ändert, und selbst keine Aggregierung oder Glättung von Auslastungsberichten durchführen.

Beachten Sie Folgendes: Wenn ein Dienst die Auslastung meldet, ersetzen diese Auslastungsberichte die standardmäßigen primären und sekundären Auslastungswerte, die bei der Erstellung des Diensts definiert wurden. Sie werden zu den neuen Auslastungswerten, die verwendet werden, wenn der Service Fabric Resource Balancer ab diesem Zeitpunkt Lastenausgleichs- oder Platzierungsentscheidungen treffen muss.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md)
 

<!---HONumber=Oct15_HO3-->