    <properties 
	pageTitle="What is the DevTest Lab service? | Microsoft Azure"
	description="Learn how DevTest Lab can make it easy to create, manage, and monitor Azure virtual machines"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/01/2015"
	ms.author="tarcher"/>

#Was ist DevTest Lab?

Entwickler und Tester versuchen, die Verzögerungen beim Erstellen und Verwalten ihrer Umgebungen zu beseitigen, indem sie in die Cloud wechseln. Azure löst das Problem von Umgebungsverzögerungen und ermöglicht Self-Service im Rahmen einer neuen, kostengünstigen Struktur. Entwickler und Tester müssen jedoch weiterhin viel Zeit für die Konfiguration ihrer selbst betreuten Umgebungen aufbringen. Darüber hinaus sind Entscheidungsträger unsicher, wie sie die Cloud für eine maximale Kostenersparnis nutzen können, ohne zu großen Verarbeitungsaufwand zu erzeugen.

Azure DevTest Lab ist ein Dienst, der Entwicklern und Testern dabei hilft, Umgebungen in Azure schnell zu erstellen und dabei unnötigen Aufwand zu minimieren und die Kosten unter Kontrolle zu halten. Sie können die neueste Version Ihrer Anwendung testen, indem Sie schnell Windows- und Linux-Umgebungen mit wiederverwendbaren Vorlagen und Artefakten bereitstellen. Integrieren Sie Ihre Bereitstellungspipeline einfach mit DevTest Lab, um Umgebungen bei Bedarf bereitstellen zu können. Skalieren Sie Ihre Auslastungstests zentral hoch, indem Sie mehrere Test-Agents bereitstellen, und erstellen Sie vorab bereitgestellte Umgebungen für Schulungen und Vorführungen.

##Warum DevTest Lab?

DevTest Lab bietet die folgenden Vorteile beim Erstellen, Konfigurieren und Verwalten von Entwickler- und Testumgebungen in der Cloud:

###Self-Service-Bereitstellung für sorgenfreies Arbeiten

DevTest Lab erleichtert Kostensenkungen, indem Sie Richtlinien für Ihr Lab festlegen können, z. B. die Anzahl der virtuellen Computer (VM) pro Benutzer und die Anzahl der virtuellen Computer pro Lab. Mit DevTest Lab können Sie außerdem Richtlinien erstellen, um virtuelle Computer automatisch herunterzufahren und zu starten.

###Schnelles Erreichen der Testphase

DevTest Lab ermöglicht Ihnen, Umgebungen mit allen erforderlichen Elementen, die Ihr Team benötigt, um mit der Entwicklung und dem Testen von Anwendungen zu beginnen, im Voraus zu erstellen und bereitzustellen. Übernehmen Sie einfach die Umgebungen, in denen der letzte funktionsfähige Build Ihrer Anwendung installiert ist, und beginnen Sie sofort mit der Arbeit. Um die Erstellung der Umgebung zusätzlich zu beschleunigen und zu vereinfachen, können Sie alternativ auch Container verwenden.

###Einmal erstellen, überall verwenden

Indem Sie Umgebungsvorlagen und -elemente erfassen und innerhalb Ihres Teams oder Ihrer Organisation freigeben, können Sie problemlos Entwicklungs- und Testumgebungen erstellen – und das im Rahmen der Quellcodeverwaltung.

###Lässt sich in Ihre vorhandene Toolkette integrieren

Nutzen Sie unsere sofort einsatzbereiten Plug-Ins oder unsere API, um Entwicklungs-/Testumgebungen direkt über Ihr bevorzugtes CI (Continuous Integration)-Tool, Ihre bevorzugte integrierte Entwicklungsumgebung (IDE) oder Ihre bevorzugte automatisierte Veröffentlichungspipeline bereitzustellen. Darüber hinaus steht Ihnen unser umfassendes Befehlszeilentool zur Verfügung.

##DevTest Lab-Konzepte

Die folgende Liste enthält wichtige DevTest Lab-Konzepte und Definitionen:

**Artefakte** werden zum Bereitstellen und Konfigurieren Ihrer Anwendung nach der Bereitstellung eines virtuellen Computers verwendet. Bei Artefakten kann es sich um Folgendes handeln:

- Tools, die Sie auf dem virtuellen Computer installieren möchten, z. B. Agents, Fiddler oder Visual Studio.
- Aktionen, die auf dem virtuellen Computer ausgeführt werden sollen, z. B. das Klonen eines Repositorys.
- Anwendungen, die Sie testen möchten.

Artefakte sind JSON-Dateien auf Grundlage des Azure-Ressourcen-Managers (ARM), die Anweisungen zum Ausführen der Bereitstellung und Übernehmen der Konfiguration enthalten. Weitere Informationen zu ARM finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).

**Artefaktrepositorys** sind Git-Repositorys, in denen Artefakte eingecheckt werden. Die gleichen Artefaktrepositorys können mehreren Labs in Ihrer Organisation hinzugefügt werden, sodass sie wiederverwendet und geteilt werden können.

**Basis** ist ein VM-Image, in dem alle Tools und Einstellungen vorinstalliert und konfiguriert sind, sodass Sie schnell einen virtuellen Computer erstellen können. Sie können einen virtuellen Computer bereitstellen, indem Sie eine vorhandene Basis auswählen ein Artefakt hinzufügen, um Ihren Test-Agent zu installieren. Anschließend können Sie dann die bereitgestellte VM als Basis speichern, die dann verwendet werden kann, ohne den Test-Agent bei jeder Bereitstellung der VM erneut installieren zu müssen.

**Obergrenzen** sind ein Mechanismus zum Minimieren von unnötiger Ressourcennutzung im Lab. Beispielsweise können Sie eine Obergrenze für die Anzahl der virtuellen Computer festlegen, die pro Benutzer oder in einem Lab erstellt werden können.

**Richtlinien** helfen dabei, die Kosten im Lab zu kontrollieren. Beispielsweise können Sie eine Richtlinie erstellen, um virtuelle Computer automatisch anhand eines definierten Zeitplans herunterzufahren.

##Nächste Schritte

Befolgen Sie zum Einstieg in DevTest Lab zunächst das schrittweise Lernprogramm [Erstellen von Azure DevTest Labs](devtest-lab-create-lab.md).

<!---HONumber=Nov15_HO4-->