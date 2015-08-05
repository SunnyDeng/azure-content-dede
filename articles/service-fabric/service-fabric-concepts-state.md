<properties
   pageTitle="Definieren und Verwalten von Zuständen"
   description="Definieren und Verwalten des Dienststatus in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="aprameyr"/>

# Dienstzustand
Der **Dienstzustand** entspricht den Daten, die der Dienst für seine Ausführung benötigt. Die Ausführung des Diensts besteht im Lesen und Schreiben der Datenstrukturen und Variablen.

Beispiel: Stellen Sie sich einen einfachen Rechnerdienst vor. Der Dienst berechnet die Summe von zwei Zahlen und gibt die Summe zurück. Es handelt sich hierbei eindeutig um einen zustandslosen Dienst, da mit dem Dienst keinerlei Daten assoziiert sind.

Angenommen, der gleiche Rechner verfügt zusätzlich zur Methode zum Berechnen der Summe über eine Methode zum Zurückgeben der zuletzt berechneten Summe. Dieser Dienst ist nun zustandsbehaftet, da er über einen Zustand verfügt, den er schreibt (beim Berechnen einer neuen Summe) und aus dem er liest (beim Zurückgeben der zuletzt berechneten Summe).

In Service Fabric wird der erste Dienst als zustandsloser Dienst bezeichnet. Der zweite Dienst wird zustandsbehafteter Dienst genannt.

## Speichern des Dienstzustands
Der Zustand kann ausgelagert oder mit dem Code, der den Zustand bearbeitet, gespeichert werden. Die Auslagerung des Status erfolgt in der Regel mit einer externen Datenbank oder einem externen Speicher. In unserem Rechnerbeispiel kann beispielsweise eine SQL-Datenbank verwendet werden, in der das aktuelle Ergebnis in einer Zeile gespeichert ist. Bei jeder Anforderung zum Berechnen der Summe wird diese Zeile aktualisiert.

Der Status kann auch gemeinsam mit dem Code gespeichert werden, die diesen Status bearbeitet. Auf diesem Modell basieren die statusbehafteten Dienste in Service Fabric. Eine entsprechende Infrastruktur in Service Fabric sorgt dafür, dass der Status bei einem Ausfall hochverfügbar und fehlertolerant ist.

## Nächste Schritte

Informationen zu den Service Fabric-Konzepten finden Sie hier:

- [Verfügbarkeit der Service Fabric-Dienste](service-fabric-availability-services.md)

- [Skalierbarkeit der Service Fabric-Dienste](service-fabric-concepts-scalability.md)

- [Partitionieren von Service Fabric-Diensten](service-fabric-concepts-partitioning.md)
 

<!---HONumber=July15_HO4-->