<properties
   pageTitle="Ausführen der automatisierten Elasticsearch-Resilienztests | Microsoft Azure"
   description="Beschreibung der Art und Weise, in der Sie die Resilienztests in Ihrer eigenen Umgebung ausführen können."
   services=""
   documentationCenter="na"
   authors="mabsimms"
   manager="marksou"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/18/2016"
   ms.author="masimms"/>

# Ausführen der automatisierten Elasticsearch-Resilienztests

Dieser Artikel ist [Teil einer Serie](guidance-elasticsearch.md).

[Resilience and Recovery Testing] (Resilienz- und Wiederherstellungstests) beschreibt eine Reihe von Tests, die an einem Elasticsearch-Beispielcluster ausgeführt wurden, um zu bestimmen, wie gut das System auf einige häufige Fehlerformen reagiert, und wie gut es wiederhergestellt werden kann. Vier Szenarien wurden getestet:

- **Ausfall und Neustart eines Knotens ohne Datenverlust**. Ein Datenknoten wird beendet und nach fünf Minuten neu gestartet. Elasticsearch wurde so konfiguriert, dass fehlende Shards während dieses Zeitraums nicht neu zugeordnet wurden, damit kein zusätzlicher E/A-Aufwand für das Verschieben von Shards anfällt. Wenn der Knoten neu gestartet wird, bringt der Wiederherstellungsprozess die Shards auf diesem Knoten wieder auf den neuesten Stand.

- **Ausfall eines Knotens mit schwerwiegendem Datenverlust**. Ein Datenknoten wird beendet, und die darauf enthaltenen Daten werden gelöscht, um einen schwerwiegenden Datenträgerausfall zu simulieren. Der Knoten wird dann neu gestartet (nach fünf Minuten) und soll quasi als Ersatz für den ursprünglichen Knoten fungieren. Für den Wiederherstellungsprozess ist die Neuerstellung der fehlenden Daten für diesen Knoten erforderlich, und unter Umständen müssen Shards neu angeordnet werden, die sich auf anderen Knoten befinden.

- **Ausfall und Neustart eines Knotens ohne Datenverlust, aber mit Neuzuordnung von Shards**. Ein Datenknoten wird beendet, und die darauf enthaltenen Shards werden neu auf andere Knoten verteilt. Der Knoten wird dann neu gestartet, und es werden weitere Schritte für die Neuzuordnung ausgeführt, um für den Cluster eine gute Balance zu erzielen.

- **Parallele Updates**. Jeder Knoten im Cluster wird beendet und nach kurzer Zeit neu gestartet, um zu simulieren, wie Computer nach einem Softwareupdate neu gestartet werden. Es wird jeweils nur ein Konten beendet. Shards werden nicht neu zugeordnet, während ein Knoten heruntergefahren ist.

Diese Tests wurden in Skripts niedergeschrieben, damit sie automatisiert ausgeführt werden können. Dieses Dokument beschreibt, wie Sie die Tests in Ihrer eigenen Umgebung wiederholen können.

## Voraussetzungen

Die automatisierten Tests erfordern die folgenden Elemente:

- Einen Elasticsearch-Cluster.

- Ein JMeter-Umgebungssetup wie in [Performance Testing Guidance] (Leistungstestleitfaden) beschrieben.

- Die folgenden Ergänzungen werden nur auf der JMeter-Master-VM installiert.

    - Java Runtime 7.

    - Nodejs 4.x.x oder höher.

    - Die Git-Befehlszeilentools.

## Die Funktionsweise der Skripts

Die Testskripts sollen auf der JMeter-Master-VM ausgeführt werden. Wenn Sie die Ausführung eines Tests auswählen, führen die Skripts die folgende Sequenz von Vorgängen aus:

1.  Starten eines JMeter-Testplans, der die Parameter übergibt, die Sie angegeben haben.

2.  Kopieren eines Skripts, das die zum Test erforderlichen Vorgänge auf einem angegebenen virtuellen Computer im Cluster ausführt. Dies kann ein beliebiger virtueller Computer sein, der über eine öffentliche IP-Adresse verfügt, oder die *Jumpbox*-VM, wenn Sie den Cluster mit der [Azure Elasticsearch-Schnellstartvorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) erstellt haben.

3.  Ausführen des Skripts auf der VM (oder Jumpbox).

Die folgende Abbildung zeigt die Struktur von Testumgebung und Elasticsearch-Cluster. Beachten Sie, dass die Testskripts SSH für die Verbindung mit jedem Knoten im Cluster verwenden, um verschiedene Elasticsearch-Vorgänge, wie z. B. Beenden oder Neustarten eines Knotens, auszuführen.

![](./media/guidance-elasticsearch/resilience-testing1.png)

## Einrichten der JMeter-Tests

Vor dem Ausführen der Resilienztests sollten Sie die JUnit-Tests kompilieren und bereitstellen, die sich im Ordner *resiliency/jmeter/tests* befinden. Auf dieses Tests verweist der JMeter-Testplan. Weitere Informationen finden Sie im Verfahren „Importing an Existing JUnit Test Project into Eclipse“ (Importieren eines vorhandenen JUnit-Testprojekts in Eclipse) unter [Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance][] (Bereitstellen eines JMeter-JUnit-Samplers zum Testen der Elasticsearch-Leistung).

Zwei Versionen der JUnit-Tests stehen in den folgenden Ordnern zur Verfügung:

- **Elasticsearch17.** Das Projekt in diesem Ordner generiert die Datei „Elasticsearch17.jar“. Verwenden Sie diese JAR-Datei zum Testen von Elasticsearch-Versionen 1.7.x.

- **Elasticsearch20**. Das Projekt in diesem Ordner generiert die Datei „Elasticsearch20.jar“. Verwenden Sie diese JAR-Datei zum Testen von Elasticsearch-Versionen 2.0.0 und höher.

Kopieren Sie die entsprechende JAR-Datei zusammen mit dem Rest der Abhängigkeiten auf Ihre JMeter-Computer. Der Vorgang wird in [Considerations for JMeter] (Überlegungen zu JMeter) in dem Verfahren „Deploying a JUnit Test to JMeter“ (Bereitstellen eines JUnit-Tests für JMeter) beschrieben.

## Konfigurieren der VM-Sicherheit für jeden Knoten

Die Testskripts setzen voraus, dass auf jedem Elasticsearch-Knoten im Cluster ein Authentifizierungszertifikat installiert wird. Dann können die Skripts ohne Eingabe von Benutzername oder Kennwort automatisch ausgeführt werden, wenn sie sich mit verschiedenen virtuellen Computern verbinden.

Starten Sie, indem Sie sich bei einem der Knoten im Elasticsearch-Cluster (oder der Jumpbox-VM) anmelden, und führen Sie dann den folgenden Befehl aus, um einen Authentifizierungsschlüssel zu generieren:

```Shell
ssh-keygen -t rsa
```

Während die Verbindung mit dem Elasticsearch-Knoten (oder der Jumpbox) besteht, führen Sie die folgenden Befehle für jeden Knoten im Elasticsearch-Cluster aus. Ersetzen Sie `<username>` mit dem Namen eines gültigen Benutzers für jeden virtuellen Computer, und ersetzen Sie `<nodename>` mit dem DNS-Namen der IP-Adresse des virtuellen Computers, der den Elasticsearch-Knoten hostet. Beachten Sie, dass Sie beim Ausführen dieser Befehle zur Eingabe des Benutzerkennworts aufgefordert werden. Weitere Informationen finden Sie unter [SSH login without password](http://www.linuxproblem.org/art_9.html) (SSH-Anmeldung ohne Kennwort):

```Shell
ssh <username>@<nodename> mkdir -p .ssh (
cat .ssh/id\_rsa.pub | ssh <username>*@<nodename> 'cat &gt;&gt; .ssh/authorized\_keys'
```

## Herunterladen und Konfigurieren der Testskripts

Die Testskripts werden in einem Git-Repository bereitgestellt. Verwenden Sie das folgende Verfahren, um die Skripts herunterzuladen und zu konfigurieren.

Öffnen Sie auf dem JMeter-Master-Computer, auf dem Sie die Tests ausführen möchten, ein Git-Desktopfenster (Git BASH), und klonen Sie das Repository, das die Skripts enthält, wie folgt:

```Shell
git clone https://github.com/mspnp/azure-guidance.git
```

Wechseln Sie zum Ordner *resiliency-tests*, und führen Sie den folgenden Befehl aus, um die zum Ausführen der Tests notwendigen Abhängigkeiten zu installieren:

```Shell
npm install
```

Wenn der JMeter-Master unter Windows ausgeführt wird, [laden Sie PLINK herunter](http://the.earth.li/~sgtatham/putty/latest/x86/plink.exe), und kopieren Sie die Datei in den Ordner *resiliency-tests/lib*.

Wenn der JMeter-Master unter Linux ausgeführt wird, müssen Sie PLINK nicht herunterladen, aber Sie müssen die SSH-Verbindung ohne Kennwort zwischen JMeter-Master und Elasticsearch-Knoten bzw. Jumpbox konfigurieren, die Sie verwendet haben, indem Sie die Schritte des Verfahrens [Configuring VM Security for Each Node](#configuring-vm-security-for-each-node) (Konfigurieren von VM-Sicherheit für jeden Knoten) ausführen.

Bearbeiten Sie die Datei `config.js`, und legen Sie die folgenden Konfigurationsparameter so fest, dass sie Ihrer Testumgebung und dem Elasticsearch-Cluster entsprechen. Diese Parameter gelten für alle Tests:

| Name | Beschreibung | Standardwert |
| ---- | ----------- | ------------- |
| `jmeterPath` | Lokaler Pfad, in dem sich JMeter befindet. | `C:/apache-jmeter-2.13` |
| `resultsPath` | Relatives Verzeichnis, in dem das Skript das Ergebnis ablegt. | `results` |
| `verbose` | Gibt an, ob das Skript eine ausführliche Ausgabe bietet oder nicht. | `true` |
| `remote` | Gibt an, ob die JMeter-Tests lokal oder auf den Remoteservern ausgeführt werden. | `true` |
| `cluster.clusterName` | Der Name des Elasticsearch-Clusters. | `elasticsearch` |
| `cluster.jumpboxIp` | Die IP-Adresse des Jumpbox-Computers. |-| | `cluster.username` | Der Administratorbenutzer, den Sie beim Bereitstellen des Clusters erstellt haben. |-| | `cluster.password` | Das Kennwort für den Administratorbenutzer. |-| | `cluster.loadBalancer.ip` | Die IP-Adresse des Elasticsearch-Lastenausgleichs. |-| | `cluster.loadBalancer.url` | Basis-URL des Lastenausgleichs. |-|

## Ausführen der Tests

Wechseln Sie zum Ordner *resiliency-tests*, und führen Sie den folgenden Befehl aus:

```Shell
node app.js
```

Das folgende Menü sollte angezeigt werden:

![](./media/guidance-elasticsearch/resilience-testing2.png)

Geben Sie die Nummer des Szenarios ein, das ausgeführt werden soll: `11`, `12`, `13` oder `21`.

Nachdem Sie ein Szenario ausgewählt haben, wird der Test automatisch ausgeführt. Die Ergebnisse werden als Satz von CSV-Dateien in einem Ordner gespeichert, der unter dem Verzeichnis *results* erstellt wird. Jede Ausführung hat einen eigenen Ergebnisordner. Sie können diese Daten in Excel analysieren und in Diagrammen darstellen.

[Running Elasticsearch on Azure]: guidance-elasticsearch-running-on-azure.md
[Tuning Data Ingestion Performance for Elasticsearch on Azure]: guidance-elasticsearch-tuning-data-ingestion-performance.md
[Performance testing guidance]: guidance-elasticsearch-performance-testing-environment.md
[JMeter guidance]: guidance-elasticsearch-implementing-jmeter.md
[Considerations for JMeter]: guidance-elasticsearch-deploy-jmeter-junit-sampler.md
[Query aggregation and performance]: guidance-elasticsearch-query-aggregation-performance.md
[Resilience and Recovery]: guidance-elasticsearch-resilience-recovery.md
[Resilience and Recovery Testing]: guidance-elasticsearch-resilience-testing.md
[Deploying a JMeter JUnit Sampler for Testing Elasticsearch Performance]: guidance-elasticsearch-deploying-jmeter-junit-sampler.md

<!---HONumber=AcomDC_0224_2016-->