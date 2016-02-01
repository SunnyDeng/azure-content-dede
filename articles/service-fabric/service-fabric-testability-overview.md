<properties
   pageTitle="Testability – Übersicht | Microsoft Azure"
   description="In diesem Artikel wird das Testability-Subsystem in Service Fabric zum Auslösen von Fehlern und Ausführen von Testszenarien für Ihre Dienste beschrieben."
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

Das Testability-Subsystem ist für das Testen von Diensten ausgelegt, die unter Microsoft Azure Service Fabric erstellt werden. Mit Testability können Sie aussagekräftige Fehler auslösen und Szenarien ausführen. Mit diesen Fehlern und Szenarien werden die verschiedenen Zustände und Übergänge durchgespielt und überprüft, die während der Lebensdauer eines Diensts auftreten – und zwar auf kontrollierte, sichere und einheitliche Weise.

Unter Testability werden Aktionen und Szenarien bereitgestellt, die diese Funktionen ermöglichen. Aktionen sind die individuellen Fehler, die auf einen Dienst angewendet werden, um ihn zu testen. Ein Dienstentwickler kann sie als Bausteine zum Schreiben komplizierter Szenarien verwenden. Beispiel:

  * Starten Sie einen Knoten neu, um eine beliebige Anzahl von Situationen zu simulieren, in denen ein Computer oder ein virtueller Computer neu gestartet wird.

  * Verschieben Sie ein Replikat Ihres zustandsbehafteten Diensts, um Lastenausgleich, Failover oder ein Anwendungsupgrade zu simulieren.

  * Lösen Sie einen Quorumverlust für einen zustandsbehafteten Dienst aus, um zu bewirken, dass Schreibvorgänge nicht fortgesetzt werden können, weil nicht genügend Backupreplikate oder sekundäre Replikate zum Aufnehmen neuer Daten vorhanden sind.

  * Lösen Sie einen Datenverlust für einen zustandsbehafteten Dienst aus, um eine Situation zu schaffen, in der alle In-Memory-Zustände vollständig entfernt werden.

Szenarien sind komplexe Vorgänge, die aus einem oder mehreren Vorgängen bestehen. Da es sich bei diesen Aktionen um PowerShell-Befehle und C#-API-Aufrufe handelt, können sie in sehr unterschiedlicher Form auftreten: Dienste mit langer Ausführungsdauer, PowerShell-Befehle, Befehlszeilenanwendungen usw. Testability verfügt standardmäßig über zwei Szenarien:

  * Chaosszenario
  * Failoverszenario

Unter Testability werden sowohl PowerShell als auch C#-APIs bereitgestellt. So können Dienstentwickler bei der Skripterstellung mit PowerShell flexibler vorgehen und bei Bedarf eine bessere Kontrolle mit C#-APIs ausüben.

## Bedeutung von Testability

Service Fabric vereinfacht das Schreiben und Verwalten von verteilten skalierbaren Anwendungen erheblich. Mit dem Testability-Subsystem in Service Fabric wird auch das Testen einer verteilten Anwendung ähnlich einfach gestaltet. Es gibt drei vorrangige Probleme, die beim Testen gelöst werden müssen:

1. Simulieren/Generieren von Fehlern, die in der Praxis auftreten können: Einer der wichtigsten Aspekte von Service Fabric ist, dass für verteilte Anwendungen damit die Wiederherstellung nach verschiedenen Fehlern möglich ist. Um zu testen, ob die Anwendung nach diesen Fehlern wiederhergestellt werden kann, benötigen wir ein aber Verfahren zum Simulieren bzw. Generieren dieser Fehler aus der Praxis in einer kontrollierten Testumgebung.

2. Möglichkeit zum Generieren der korrelierten Fehler: Grundlegende Fehler im System, z. B. ein Netzwerk- oder Computerausfall, können leicht individuell ausgelöst werden. Das Generieren der signifikanten Anzahl von Szenarien, die in der Praxis aufgrund von Interaktionen zwischen diesen Einzelfehlern auftreten können, ist dagegen nicht so einfach.

3. Einheitliche Umgebungen für unterschiedliche Entwicklungs- und Bereitstellungsstufen: Es gibt viele Fault Injection-Systeme, mit denen verschiedene Arten von Fehlern ausgelöst werden können. Dies ist aber nicht sehr benutzerfreundlich, wenn zwischen One-Box-Entwicklerszenarien, dem Ausführen der gleichen Tests in großen Testumgebungen und dem Testen in der Produktion gewechselt wird.

Es gibt zwar viele Verfahren zum Lösen dieser Probleme, aber es fehlt ein System, das diese Aufgaben mit der erforderlichen Garantie ausführen kann – von der One-Box-Entwicklerumgebung bis zum Testen in Produktionsclustern. Dank des Testability-Subsystems können sich Anwendungsentwickler mehr auf das Testen der Geschäftslogik konzentrieren. Testability enthält alle Funktionen, die zum Testen der Interaktion des Diensts mit dem zugrunde liegenden verteilten System erforderlich sind.

### Simulieren/Generieren von Fehlerszenarien aus der Praxis

Um die Robustheit eines verteilten Systems in Bezug auf Fehler zu testen, benötigen wir ein Verfahren zum Generieren von Fehlern. Das Generieren eines Fehlers, z. B. der Ausfall eines Knotens, ist theoretisch zwar einfach, aber dabei kommt es zu den gleichen Konsistenzproblemen, die von Service Fabric gelöst werden müssen. Wenn ein Knoten heruntergefahren werden soll, ist der folgende Workflow erforderlich:

1. Geben Sie auf dem Client eine Anforderung zum Herunterfahren des Knotens aus.

2. Senden Sie die Anforderung an den richtigen Knoten.

    a. Wenn der Knoten nicht gefunden wird, tritt ein Fehler auf.

    b. Wenn der Knoten gefunden wird, sollte die Rückgabe nur durchgeführt werden, sofern der Knoten heruntergefahren wurde.

Zum Überprüfen des Fehlers aus Testsicht muss die Information vorliegen, ob der Fehler nach der Auslösung wirklich auftritt. Die Garantie von Service Fabric besteht darin, dass der Knoten entweder ausfällt oder bereits ausgefallen ist, wenn der Befehl den Knoten erreicht. In beiden Fällen sollte der Test in der Lage sein, den Zustand und den Erfolg bzw. Misserfolg richtig zu bestimmen und dies bei der Überprüfung richtig wiederzugeben. Für ein System, das außerhalb von Service Fabric für die gleiche Gruppe von Fehlern implementiert wird, können viele Netzwerk-, Hardware- und Softwarefehler auftreten, sodass die oben beschriebene Garantie nicht erreichbar ist. Beim Auftreten der obigen Probleme wird der Clusterzustand von Service Fabric neu konfiguriert, um diese zu umgehen. Daher können mit dem Testability-Subsystem weiterhin die richtigen Garantien gegeben werden.

### Generieren von erforderlichen Ereignissen und Szenarien

Das konsistente Simulieren von Fehlern aus der Praxis ist schon schwierig, aber das Generieren von korrelierten Fehlern ist noch deutlich aufwendiger. Beispielsweise tritt für einen zustandsbehafteten dauerhaften Dienst ein Datenverlust auf, wenn Folgendes passiert:

1. Bei der Replikation wird nur ein Schreibquorum der Replikate berücksichtigt. Alle sekundären Replikate hinken dem primären Replikat hinterher.

2. Das Schreibquorum fällt aus, weil die Replikate ausfallen (aufgrund eines Ausfalls des Codepakets oder Knotens).

3. Das Schreibquorum kann nicht wiederhergestellt werden, weil die Daten für die Replikate verloren gegangen sind (aufgrund einer Beschädigung der Festplatte oder eines Reimagings des Computers).

Diese korrelierten Fehler kommen auch in der Praxis vor, aber nicht so häufig wie Einzelfehler. Es ist wichtig, dass diese Szenarien getestet werden können, bevor sie in der Produktion auftreten. Noch wichtiger ist die Möglichkeit, diese Szenarien mit Produktionsworkloads in kontrolliertem Rahmen zu simulieren (am Tag, wenn alle Techniker anwesend sind). Dies ist viel besser, als wenn der Fehler zum ersten Mal um 2 Uhr morgens in der Produktion auftritt.

### Einheitliche Benutzeroberfläche in unterschiedlichen Umgebungen

Bisher wurde so vorgegangen, dass drei unterschiedliche Benutzeroberflächen erstellt wurden: eine für die Entwicklungsumgebung, eine für Tests und eine für die Produktion. Das Modell lautete wie folgt:

1. In der Entwicklungsumgebung werden Statusübergänge produziert, mit denen Komponententests einzelner Methoden durchgeführt werden können.

2. In der Testumgebung werden Fehler produziert, um umfassende Tests mit unterschiedlichen Fehlerszenarien zu ermöglichen.

3. Die Produktionsumgebung wird nicht beeinträchtigt, alle nicht natürlichen Fehler werden verhindert, und es wird sichergestellt, dass eine extrem schnelle menschliche Reaktion auf Fehler erfolgt.

In Service Fabric haben wir mit dem Testability-Subsystem einen neuen Ansatz gewählt und nutzen von der Entwicklerumgebung bis zur Produktion dieselbe Methodik. Es gibt zwei Möglichkeiten, dies zu erreichen:

1. Um kontrollierte Fehler auszulösen, werden die Testability-APIs von einer One-Box-Umgebung bis zu den Produktionsclustern durchgängig verwendet.

2. Das Testability-Subsystem wird verwendet, um automatische Fehler zu generieren und so für den Cluster das automatische Auslösen von Fehlern zu bewirken. Das Steuern der Fehlerrate per Konfiguration ermöglicht es, dass derselbe Dienst in verschiedenen Umgebungen auf unterschiedliche Weise getestet wird.

Bei Service Fabric ist das eigentliche Verfahren identisch, auch wenn das Ausmaß der Fehler in den unterschiedlichen Umgebungen variieren würde. Dies ermöglicht eine deutlich schnellere Pipeline vom Code bis zur Bereitstellung und das Testen von Diensten mit praxisnahen Auslastungen.

## Verwenden von Testability

### Verwenden von Testability in C#

Die Testability-Features sind in der Datei „System.Fabric.Testability.dll“ enthalten. Diese DLL befindet sich im NuGet-Paket „Microsoft.ServiceFabric.Testability.nupack“. Fügen Sie das NuGet-Paket als Referenz in Ihr Projekt ein, um die Testability-Features zu nutzen.

### Verwenden von Testability in PowerShell

Zum Verwenden von Testability in PowerShell müssen Sie den Laufzeit-MSI installieren. Nach der Installation des MSI wird das ServiceFabricTestability-PowerShell-Modul für Entwickler automatisch geladen.

## Zusammenfassung

Zum Erstellen echter Dienste für die Cloud müssen Sie vor und nach der Bereitstellung unbedingt sicherstellen, dass die Dienste für die in der Praxis auftretenden Fehler gerüstet sind. Für Dienste ist es heutzutage wichtig, dass schnelle Innovationen möglich sind und Code schnell in die Produktion verlagert werden kann. Genau bei dieser Aufgabe werden Dienstentwickler von Service Fabric Testability unterstützt.

## Nächste Schritte

- [Testability-Aktionen](service-fabric-testability-actions.md)
- [Testability-Szenarien](service-fabric-testability-actions.md)
- Gewusst wie: Testen Ihres Diensts
  - [Simulieren von Ausfällen während der Bearbeitung von Dienstworkloads](service-fabric-testability-workload-tests.md)
  - [Ausfälle bei der Kommunikation von Dienst zu Dienst](service-fabric-testability-scenarios-service-communication.md)

<!---HONumber=AcomDC_0121_2016-->