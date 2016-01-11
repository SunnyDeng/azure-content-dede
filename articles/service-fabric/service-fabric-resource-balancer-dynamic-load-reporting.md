<properties
   pageTitle="Dynamisches Melden der Auslastung | Microsoft Azure"
   description="Sie erhalten einen Überblick über das dynamische Melden der Auslastung an Resource Balancer."
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

# Übersicht über das dynamische Melden der Auslastung

Zur Laufzeit können zustandsbehaftete und zustandslose Dienstobjekte die Auslastung über die ReportLoad-Methode melden (Member der Schnittstellen IStatefulServicePartition und IStatelessServicePartition). Das Melden von Werten der Auslastung zur Laufzeit ist wichtig. Damit wird das genaue Packen von Diensten in die Knoten ermöglicht. Außerdem wird sichergestellt, dass die Ressourcennutzung von der zentralen Resource Balancer-Instanz in Azure Service Fabric so präzise nachverfolgt wird, wie sie für die Dienste auf den Knoten abläuft.

Beachten Sie Folgendes: Wenn ein Replikat seine Auslastung meldet, wird diese Meldung zuerst mit anderen Auslastungsberichten lokal zusammengefasst, und anschließend wird ein gemeinsamer Bericht an Resource Balancer gesendet. Dieser Prozess bedeutet, dass beim wiederholten und sehr schnellen Melden der Auslastung durch einen Dienst nur der letzte Bericht tatsächlich an Resource Balancer gesendet wird.

Wenn Service Fabric Resource Balancer ausgeführt wird, werden damit alle Auslastungsinformationen untersucht, die von allen Knoten aggregiert werden, und es werden einige Überprüfungen durchgeführt. Zu diesen Überprüfungen gehören der Ausgleichsschwellenwert und Aktivitätsschwellenwerte für die Metriken gemäß der Definition im Clustermanifest. Damit wird bestimmt, ob die Unausgeglichenheit des Clusters ausreicht, um Resource Balancer auszuführen. Außerdem wird ermittelt, ob für einen bestimmten Knoten die Kapazität für eine der Metriken überschritten wurde, für die eine Kapazität festgelegt wurde. Bei einer Kapazitätsüberschreitung werden von Resource Balancer zuerst nur die Dienste auf dem Knoten bzw. den Knoten mit Kapazitätsüberschreitung berücksichtigt, die diese Metrik aufweisen. Bei einer Unausgeglichenheit des Clusters berücksichtigt Resource Balancer alle Dienste, die über Metriken mit Diensten verknüpft sind, von denen die unausgeglichene Metrik gemeldet wird. Wenn Resource Balancer ermittelt, dass eine dieser Situationen eingetreten ist, wird eine Simulation ausgeführt. Damit wird versucht, eine bessere Anordnung der Dienste zu erreichen.

Dienste sollen die Auslastung immer melden, wenn sie sich ändert. Sie sollen selbst keine Aggregierung oder Glättung von Auslastungsberichten ausführen.

Beachten Sie Folgendes: Wenn ein Dienst die Auslastung meldet, ersetzen diese Auslastungsberichte die standardmäßigen primären und sekundären Auslastungswerte, die bei der Erstellung des Diensts definiert wurden. Sie werden zu den neuen Auslastungswerten, die verwendet werden, wenn Service Fabric Resource Balancer ab diesem Zeitpunkt Lastenausgleichs- oder Platzierungsentscheidungen treffen muss.



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte

Weitere Informationen: [Resource Balancer-Architektur](service-fabric-resource-balancer-architecture.md)

<!---HONumber=AcomDC_1223_2015-->