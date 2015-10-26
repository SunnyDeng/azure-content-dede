<properties
   pageTitle="Testability – Übersicht"
   description="In diesem Artikel wird das Testability-Feature von Microsoft Azure Service Fabric beschrieben."
   services="service-fabric"
   documentationCenter=".net"
   authors="rishirsinha"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/13/2015"
   ms.author="rsinha"/>

# Testability – Übersicht

Testability ist eine Suite mit Tools. Die Tools sind speziell für das Testen von Diensten ausgelegt, die mit Microsoft Azure Service Fabric erstellt wurden. Mit den Tools können Entwickler leicht aussagekräftige Fehler auslösen und Testszenarien ausführen, um die vielen unterschiedlichen Zustände und Übergänge zu schaffen und zu überprüfen, die für einen Dienst während seiner Lebensdauer gelten können. Dabei ist jederzeit für die erforderliche Kontrolle und Sicherheit gesorgt.

Testability verfügt über Aktionen und Szenarien, mit denen die oben beschriebenen Schritte ausgeführt werden können. Aktionen sind die individuellen Fehler, die auf einen Dienst angewendet werden, um ihn zu testen. Ein Dienstentwickler kann sie als Bausteine zum Schreiben komplizierter Szenarien verwenden. Beispiel:

  + Starten Sie einen Knoten neu, um eine beliebige Anzahl von Situationen zu simulieren, in denen ein Computer oder ein virtueller Computer neu gestartet wird.
  + Verschieben Sie ein Replikat Ihres zustandsbehafteten Diensts, um Lastenausgleich, Failover oder ein Anwendungsupgrade zu simulieren.
  + Lösen Sie einen Quorumverlust für einen zustandsbehafteten Dienst aus, um zu bewirken, dass Schreibvorgänge nicht fortgesetzt werden können, weil nicht genügend Backupreplikate oder sekundäre Replikate zum Aufnehmen neuer Daten vorhanden sind.
  + Lösen Sie einen Datenverlust für einen zustandsbehafteten Dienst aus, um eine Situation zu schaffen, in der alle In-Memory-Zustände vollständig entfernt werden.

Szenarien sind vollständige Tests, die aus einer oder mehreren Aktionen bestehen. Da es sich bei den Aktionen um nichts anderes als PowerShell-Befehle und C#-Funktionen handelt, können sie die unterschiedlichsten Formen aufweisen: Dienste mit langer Ausführungsdauer, PowerShell-Befehle, Befehlszeilenanwendungen usw. Testability verfügt standardmäßig über zwei Szenarien:

  + Chaostest
  + Failovertest

Unter Testability werden sowohl PowerShell als auch C#-APIs bereitgestellt. Daher haben Dienstentwickler bei der Skripterstellung mit PowerShell mehr Flexibilität und eine bessere Kontrolle mit C#-APIs, wenn dies erforderlich ist.

## Bedeutung von Testability

Service Fabric vereinfacht das Schreiben und Verwalten von verteilten skalierbaren Anwendungen erheblich. Die Testability-Komponente in Service Fabric ist dafür gedacht, das Testen einer verteilten Anwendung ähnlich einfach zu gestalten. Es gibt drei vorrangige Probleme, die beim Testen gelöst werden müssen:

1. Simulieren/Generieren von Fehlern, die in der Praxis auftreten können: Einer der wichtigsten Aspekte von Service Fabric ist, dass für verteilte Anwendungen damit die Wiederherstellung nach verschiedenen Fehlern möglich ist. Um zu testen, ob die Anwendung nach diesen Fehlern wiederhergestellt werden kann, benötigen wir ein Verfahren zum Simulieren bzw. Generieren dieser Fehler aus der Praxis in einer kontrollierten Testumgebung.
2. Möglichkeit zum Generieren der korrelierten Fehler: Wie grundlegende Fehler im System auch (z. B. ein Netzwerkausfall), können Computerfehler einfach einzeln ausgelöst werden. Das Generieren der signifikanten Anzahl von Szenarien, die in der Praxis aufgrund von Interaktionen zwischen diesen Einzelfehlern auftreten können, ist dagegen nicht so einfach.
3. Einheitliche Umgebungen für unterschiedliche Entwicklungs- und Bereitstellungsstufen: Es gibt viele Fault Injection-Systeme, mit denen verschiedene Arten von Fehlern ausgelöst werden können. Die Umgebungen sind dabei jedoch sehr uneinheitlich, wenn zwischen One-Box-Entwicklerszenarien, dem Ausführen der gleichen Tests in großen Testumgebungen und dem Testen in der Produktion gewechselt wird.

Es gibt zwar viele Verfahren zum Lösen der oben beschriebenen Probleme, aber es fehlt ein System, das diese Aufgaben mit der erforderlichen Garantie ausführen kann – von der One-Box-Entwicklerumgebung bis zum Testen in Produktionsclustern. Mit der Testability-Komponente können sich Anwendungsentwickler auf das Testen ihrer Geschäftslogik konzentrieren. Das Testability-Feature enthält alle Funktionen, die zum Testen der Interaktion des Diensts mit dem zugrunde liegenden verteilten System erforderlich sind.

### Simulieren/Generieren von Fehlerszenarien aus der Praxis
Um die Robustheit eines verteilten Systems in Bezug auf Fehler zu testen, benötigen wir ein Verfahren zum Generieren von Fehlern. Das Generieren eines Fehlers wie der Ausfall eines Knotens ist theoretisch zwar einfach, aber dabei kommt es zu den gleichen Konsistenzproblemen, die von Service Fabric gelöst werden müssen. Wenn ein Knoten heruntergefahren werden soll, ist der folgende Workflow erforderlich:

1. Geben Sie auf dem Client eine Anforderung zum Herunterfahren des Knotens aus.
2. Senden Sie die Anforderung an den richtigen Knoten.
	1. Wenn der Knoten nicht gefunden wird, tritt ein Fehler auf.
	2. Wenn der Knoten gefunden wird, sollte die Rückgabe nur durchgeführt werden, sofern der Knoten heruntergefahren wurde.

Aus Testsicht muss die Information vorliegen, ob der Fehler nach der Auslösung wirklich auftritt, um ihn überprüfen zu können. Die Garantie von Windows Fabric besteht darin, dass der Knoten entweder ausfällt oder bereits ausgefallen ist, wenn der Befehl den Knoten erreicht. In beiden Fällen sollte der Test in der Lage sein, den Zustand und den Erfolg bzw. Misserfolg richtig zu bestimmen und dies bei der Überprüfung richtig wiederzugeben. Für ein System, das außerhalb von Service Fabric für die gleiche Gruppe von Fehlern implementiert wird, können die unterschiedlichsten Netzwerk-, Hardware- und Softwarefehler auftreten, sodass die oben beschriebene Garantie nicht erreichbar ist. Beim Auftreten der obigen Probleme wird der Clusterzustand von Service Fabric neu konfiguriert, um diese zu umgehen. Daher können mit Testability weiterhin die richtigen Garantien gegeben werden.

### Generieren von erforderlichen Ereignissen und Szenarien
Das konsistente Simulieren von Fehlern aus der Praxis ist schon schwierig, aber das Generieren von korrelierten Fehlern ist noch deutlich aufwendiger. Beispielsweise tritt für einen zustandsbehafteten dauerhaften Dienst ein Datenverlust auf, wenn Folgendes passiert:

1. Bei der Replikation wird nur ein Schreibquorum der Replikate berücksichtigt. Alle sekundären Replikate hinken dem primären Replikat hinterher.
2. Das Schreibquorum fällt aus, weil die Replikate ausfallen (aufgrund eines Ausfalls des Codepakets oder Knotens).
3. Das Schreibquorum kann nicht wiederhergestellt werden, weil die Daten für die Replikate verloren gegangen sind (aufgrund einer Beschädigung der Festplatte oder eines Reimagings des Computers).

Diese korrelierten Fehler kommen auch in der Praxis vor (wenn auch nicht so häufig wie Einzelfehler). Es ist wichtig, dass diese Szenarien getestet werden können, bevor sie in der Produktion auftreten. Es ist natürlich auch vorzuziehen, diese Fehler für Produktionsworkloads in kontrollierten Umgebungen zu simulieren (am Tag, wenn alle Engineers da sind), als wenn sie zum ersten Mal um zwei Uhr morgens in der Produktion auftreten.

### Einheitliche Benutzeroberfläche in unterschiedlichen Umgebungen
Bisher wurde so vorgegangen, dass drei unterschiedliche Benutzeroberflächen erstellt wurden: eine für die Entwicklungsumgebung, eine für Tests und eine für die Produktion. Das Modell lautete wie folgt:

1. In der Entwicklungsumgebung werden Statusübergänge produziert, mit denen Einheitentests einzelner Methoden durchgeführt werden können.
2. In der Testumgebung werden Fehler produziert, um umfassende Tests mit unterschiedlichen Fehlerszenarien zu ermöglichen.
3. Die Produktionsumgebung wird nicht beeinträchtigt, alle nicht natürlichen Fehler werden verhindert, und es wird sichergestellt, dass eine extrem schnelle menschliche Reaktion auf Fehler erfolgt.

In Service Fabric mit dem Testability-Modul und Diensten haben wir einen neuen Ansatz gewählt und nutzen von der Entwicklerumgebung bis zur Produktion dieselbe Methodik. Es gibt zwei Möglichkeiten, dies zu erreichen: 1. Um kontrollierte Fehler auszulösen, werden die Testability-APIs von einer One-Box-Umgebung bis zu den Produktionsclustern durchgängig verwendet. 2. Der Testability-Dienst wird verwendet, um automatische Fehler zu generieren und so für den Cluster das automatische Auslösen von Fehlern zu bewirken. Das Steuern der Fehlerrate per Konfiguration ermöglicht es, dass derselbe Dienst in verschiedenen Umgebungen auf unterschiedliche Weise getestet wird.

Bei Service Fabric ist das eigentliche Verfahren identisch, auch wenn das Ausmaß der Fehler in den unterschiedlichen Umgebungen variieren würde. Dies ermöglicht eine deutlich schnellere Pipeline vom Code bis zur Bereitstellung und das Testen von Diensten, während weiterhin die Auslastung aus der Praxis herrscht.

## Verwenden von Testability
### Verwenden von Testability in C# 
Die verschiedenen Testability-Features sind in der Datei „System.Fabric.Testability.dll“ enthalten. Sie finden diese DLL im NuGet-Paket „Microsoft.ServiceFabric.Testability.nupack“. Fügen Sie das NuGet-Paket als Referenz in Ihr Projekt ein, um die Testability-Features zu nutzen.

## Verwenden von Testability in PowerShell
Sie müssen den Laufzeit-MSI installieren, um Testability in PowerShell zu verwenden. Nach der Installation des MSI wird das ServiceFabricTestability-PowerShell-Modul für Entwickler automatisch geladen.

## Zusammenfassung
Beim Erstellen von robusten Diensten für die Cloud ist es wichtig sicherzustellen, dass Dienste in der Praxis auftretende Fehler überstehen können, bevor sie bereitgestellt werden (gilt auch für die Bereitstellung in der Produktion). Außerdem ist es für Dienste heutzutage wichtig, dass schnelle Innovationen möglich sind und Code schnell in die Produktion verlagert werden kann. Mit dem Testability-Feature von Service Fabric können Dienstentwickler genau dies erreichen.

## Nächste Schritte

- [Testability-Aktionen](service-fabric-testability-actions.md)
- [Testability-Szenarien](service-fabric-testability-actions.md)
- Gewusst wie: Testen Ihres Diensts
	- [Simulieren von Ausfällen während der Bearbeitung von Dienstworkloads](service-fabric-testability-workload-tests.md)
   - [Ausfälle bei der Kommunikation von Dienst zu Dienst](service-fabric-testability-scenarios-service-communication.md)

 

<!---HONumber=Oct15_HO3-->