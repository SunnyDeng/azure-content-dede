<properties
   pageTitle="Service Fabric – Technische Übersicht | Microsoft Azure"
   description="Technischer Überblick über Service Fabric. Es werden wichtige Begriffe und die Architektur erläutert."
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager="timlt"
   editor="chackdan;subramar"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Technischer Überblick über Service Fabric

Service Fabric ist eine Plattform für verteilte Systeme, die das Packen, Bereitstellen und Verwalten skalierbarer und zuverlässiger Microservices vereinfacht.

## Wichtige Terminologiekonzepte
In diesem Abschnitt wird die Terminologie aus Service Fabric erläutert, damit Sie die an anderer Stelle in der Dokumentation verwendeten Begriffe verstehen.

**Cluster:** Per Netzwerk verbundene virtuelle oder physische Computer, auf denen Ihre Microservices bereitgestellt und verwaltet werden. Cluster können auf Tausende von Computern skaliert werden.

**Knoten:** Ein Computer oder ein virtueller Computer, der Teil eines Clusters ist, wird als Knoten bezeichnet. Jeder Knoten erhält einen Knotennamen (Zeichenfolge). Knoten weisen Merkmale wie etwa Platzierungseigenschaften auf. Jeder Computer oder virtueller Computer verfügt über einen Windows-Dienst für den automatischen Start (`FabricHost.exe`), der beim Start ausgeführt wird. Dieser Dienst wiederum startet zwei ausführbare Dateien: `Fabric.exe` und `FabricGateway.exe`. Diese zwei ausführbaren Dateien bilden zusammen den Knoten. In Testszenarien können Sie mehrere Knoten auf einem PC oder virtuellen Computer hosten, indem Sie mehrere Instanzen von `Fabric.exe` und `FabricGateway.exe` ausführen.

**Anwendungstyp:** Name und Version, die einer Sammlung von Diensttypen zugewiesen sind. Diese Informationen sind in der Datei `ApplicationManifest.xml` definiert und in ein Anwendungspaketverzeichnis eingebettet, das dann in den Imagespeicher des Service Fabric-Clusters kopiert wird. Anschließend können Sie aus diesem Anwendungstyp im Cluster eine benannte Anwendung erstellen.

Weitere Informationen finden Sie im Artikel [Anwendungsmodell](service-fabric-application-model.md).

**Anwendungspaket:** Ein Datenträgerverzeichnis mit der Datei `ApplicationManifest.xml` des Anwendungstyps. Diese Datei verweist auf die Dienstpakete für jeden Diensttyp, der den Anwendungstyp bildet. Die Dateien im Anwendungspaketverzeichnis werden in den Imagespeicher des Service Fabric-Clusters kopiert. Ein Anwendungspaket für einen E-Mail-Anwendungstyp kann beispielsweise Verweise auf ein Warteschlangendienstpaket, ein Front-End-Dienstpaket und ein Datenbankdienstpaket enthalten.

**Benannte Anwendung:** Nach dem Kopieren eines Anwendungspakets in den Imagespeicher können Sie eine Instanz der Anwendung im Cluster erstellen, indem Sie den Anwendungstyp des Anwendungspakets (unter Verwendung seines Namens/seiner Version) angeben. Jeder Anwendungstypinstanz wird ein URI-Name zugewiesen, der wie folgt aussieht: `"fabric:/MyNamedApp"`. Innerhalb eines Clusters können Sie mehrere benannte Anwendungen aus einem einzelnen Anwendungstyp erstellen. Sie können auch benannte Applikationen aus verschiedenen Anwendungstypen erstellen. Jede benannte Anwendung wird unabhängig verwaltet und versioniert.

**Diensttyp:** Name und Version, die den Code-, Daten- und Konfigurationspaketen eines Diensts zugewiesen werden. Diese Informationen werden in der Datei `ServiceManifest.xml` definiert, die in einem Dienstpaketverzeichnis eingebettet ist. Auf das Dienstpaketverzeichnis wird dann in der Datei `ApplicationManifest.xml` eines Anwendungspakets verwiesen. Innerhalb des Clusters kann nach der Erstellung einer benannten Anwendung ein benannter Dienst aus einem der Diensttypen des Anwendungstyps erstellt werden. Die Datei `ServiceManifest.xml` des Diensttyps beschreibt den Dienst.

Weitere Informationen finden Sie im Artikel [Anwendungsmodell](service-fabric-application-model.md).

Es gibt zwei Arten von Diensten:

- **Zustandslos:** Verwenden Sie einen zustandslosen Dienst, wenn der persistente Zustand des Diensts in einem externen Speicherdienst wie Azure Storage, Azure SQL-Datenbank oder Azure DocumentDB gespeichert ist. Wenn der Dienst über keinen persistenten Speicher verfügt, sollten Sie ebenfalls einen zustandslosen Dienst verwenden. Ein Beispiel hierfür ist ein Rechnerdienst, in dem Werte an den Dienst übergeben werden und eine Berechnung unter Verwendung dieser Werte ausgeführt und ein Ergebnis zurückgegeben wird.

- **Zustandsbehaftet:** Verwenden Sie einen zustandsbehafteten Dienst, wenn Service Fabric zur Verwaltung des Dienstzustands Programmiermodelle mit Reliable Collections oder Reliable Actors verwenden soll. Bei der Erstellung eines benannten Diensts geben Sie aus Gründen der Skalierbarkeit und Zuverlässigkeit an, auf wie viele Partitionen der Zustand verteilt und wie oft der Zustand auf den einzelnen Knoten repliziert werden soll. Jeder benannte Dienst verfügt über ein einzelnes primäres Replikat und mehrere sekundäre Replikate. Sie ändern den Zustand des benannten Diensts, indem Sie ins primäre Replikat schreiben. Service Fabric repliziert diesen Zustand dann in allen sekundären Replikaten, sodass der Zustand synchron gehalten wird. Wenn ein primäres Replikat fehlschlägt, erkennt Service Fabric das automatisch, stuft ein vorhandenes sekundäres Replikat auf ein primäres Replikat hoch und erstellt dann ein neues sekundäres Replikat.

**Dienstpaket:** Ein Datenträgerverzeichnis mit der Datei `ServiceManifest.xml` des Diensttyps. Diese Datei verweist auf den Code, die statischen Daten und die Konfigurationspakete für den Diensttyp. Auf die Dateien im Dienstpaketverzeichnis wird in der Datei `ApplicationManifest.xml` des Anwendungstyps verwiesen. Ein Dienstpaket kann beispielsweise auf den Code, die statischen Daten und die Konfigurationspakete verweisen, die zusammen einen Datenbankdienst bilden.

**Benannter Dienst:** Nach der Erstellung einer benannten Anwendung können Sie eine Instanz eines Diensttyps im Cluster erstellen, indem Sie den Diensttyp (unter Verwendung seines Namens/seiner Version) angeben. Jeder Diensttypinstanz wird ein URI-Name zugewiesen, der dem URI der benannten Anwendung zugeordnet ist. Wenn Sie beispielsweise den benannten Dienst „MyDatabase“ in der benannten Anwendung „MyNamedApp“ erstellen, sieht der URI folgendermaßen aus: `"fabric:/MyNamedApp/MyDatabase"`. Innerhalb einer benannten Anwendung können Sie mehrere benannte Dienste erstellen. Jeder benannte Dienst kann ein eigenes Partitionsschema und eigene Werte für die Instanzen-/Replikatanzahl besitzen.

**Codepaket**: Ein Datenträgerverzeichnis mit den ausführbaren Dateien des Diensttyps (in der Regel EXE-/DLL-Dateien). Auf die Dateien im Codepaketverzeichnis wird in der Datei `ServiceManifest.xml` des Diensttyps verwiesen. Beim Erstellen eines benannten Diensts werden die Dateien im Codepaket auf die Knoten kopiert, die Service Fabric zum Ausführen des benannten Diensts auswählt. Anschließend wird mit der Ausführung des Codes begonnen. Es gibt zwei Arten von ausführbaren Dateien für Codepakete:

- **Ausführbare Gastanwendungsdateien:** Ausführbare Dateien, die ohne Änderungen auf dem Hostbetriebssystem (Windows oder Linux) ausgeführt werden. Das bedeutet, dass diese ausführbaren Dateien nicht mit Service Fabric-Laufzeitdateien verknüpft sind oder auf solche Dateien verweisen und daher keine Service Fabric-Programmiermodelle verwenden. Diese ausführbaren Dateien können einige Service Fabric-Funktionen wie den Naming-Dienst für die Endpunktermittlung nicht nutzen und für einzelne Dienstinstanzen spezifische Lastmetriken nicht melden.

- **Ausführbare Dateien des Diensthosts:** Hierbei handelt es sich um ausführbare Dateien, die Service Fabric-Programmiermodelle nutzen, indem eine Verknüpfung mit Service Fabric-Laufzeitdateien hergestellt wird. Dadurch werden Teile des Codes der ausführbaren Datei mit Service Fabric verknüpft und zusätzliche Funktionen aktiviert. Eine Instanz eines benannten Diensts kann beispielsweise Endpunkte im Service FabricNaming-Dienst registrieren und Lastmetriken melden.

**Datenpaket:** Ein Datenträgerverzeichnis mit den statischen, schreibgeschützten Datendateien eines Diensttyps (in der Regel Foto-, Audio- und Videodateien). Auf die Dateien im Datenpaketverzeichnis wird in der Datei `ServiceManifest.xml` des Diensttyps verwiesen. Bei der Erstellung eines benannten Diensts werden die Dateien im Datenpaket auf die Knoten kopiert, die Service Fabric zum Ausführen des benannten Diensts auswählt. Anschließend wird mit der Ausführung des Codes begonnen, und der Code kann nun auf die Datendateien zugreifen.

**Konfigurationspaket:** Ein Datenträgerverzeichnis mit den statischen, schreibgeschützten Konfigurationsdateien eines Diensttyps (in der Regel Textdateien). Auf die Dateien im Konfigurationspaketverzeichnis wird in der Datei `ServiceManifest.xml` des Diensttyps verwiesen. Bei der Erstellung eines benannten Diensts werden die Dateien im Konfigurationspaket auf die Knoten kopiert, die Service Fabric zum Ausführen des benannten Diensts auswählt. Anschließend wird mit der Ausführung des Codes begonnen, und der Code kann nun auf die Konfigurationsdateien zugreifen.

**Imagespeicher**: Jeder Service Fabric-Cluster verwaltet einen Imagespeicher. Sie müssen den Inhalt eines Anwendungspakets in den Imagespeicher kopieren und anschließend den Anwendungstyp in diesem Anwendungspaket registrieren. Nach der Bereitstellung des Anwendungstyps können Sie benannte Anwendungen daraus erstellen. Sie können die Registrierung eines Anwendungstyps im Imagespeicher erst aufheben, wenn alle benannten Anwendungen gelöscht wurden.

Weitere Informationen zum Bereitstellen des Imagespeichers finden Sie unter [Bereitstellen von Anwendungen](service-fabric-deploy-remove-applications.md).

**Partitionsschema:** Bei der Erstellung eines benannten Diensts geben Sie ein Partitionsschema an. Dienste mit großen Zustandsdatenmengen teilen die Daten auf verschiedene Partitionen auf, wodurch sie auf die Knoten des Clusters verteilt werden. Dadurch wird die Skalierung des Zustands des benannten Diensts ermöglicht. Innerhalb einer Partition besitzen zustandslose benannte Dienste Instanzen, wohingegen zustandsbehaftete benannte Dienste Replikate besitzen. In der Regel verfügen zustandslose benannte Dienste immer nur über eine Partition, da sie keinen internen Zustand aufweisen. Die Partitionsinstanzen sorgen für Verfügbarkeit. Wenn eine Instanz ausfällt, werden andere Instanzen weiterhin normal ausgeführt, und Service Fabric erstellt eine neue Instanz. Zustandsbehaftete benannte Dienste behalten ihren Zustand in Replikaten bei, und jede Partition verfügt über eine eigene Replikatgruppe, in der alle Zustände synchronisiert werden. Fällt ein Replikat aus, erstellt Service Fabric aus den vorhandenen Replikaten ein neues Replikat.

Weitere Informationen finden Sie unter [Partitionieren von Service Fabric Reliable Services](service-fabric-concepts-partitioning.md).

**Programmiermodelle:** Zum Erstellen von Service Fabric-Diensten stehen zwei .NET Framework-Programmiermodelle zur Verfügung:

- Reliable Services: Eine API zum Erstellen zustandsloser und zustandsbehafteter Dienste. Zustandsbehaftete Dienste speichern ihren Zustand in Reliable Collections (z. b. in einem Wörter oder einer Warteschlange). Sie können auch verschiedene Kommunikationsstapel verknüpfen, z. B. Web-API und Windows Communication Foundation (WCF).

- Reliable Actors: Eine API zum Erstellen zustandsloser und zustandsbehafteter Objekte über das Programmiermodell mit virtuellen Actors. Dieses Modell kann bei zahlreichen unabhängigen Berechnungs-/Zustandseinheiten nützlich sein. Da dieses Modell ein rundenbasiertes Threadingmodell verwendet, sollten Sie Code vermeiden, der andere Actors oder Dienste aufruft, weil ein einzelner Actor andere eingehende Anforderungen erst verarbeiten kann, wenn alle ausgehenden Anforderungen abgeschlossen sind.

Weitere Informationen finden Sie im Artikel [Auswählen eines Programmiermodells für den Dienst](service-fabric-choose-framework.md).

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
Weitere Informationen zu Service Fabric

- [Übersicht über Service Fabric](service-fabric-overview.md)
- [Gründe für einen Microservice-Ansatz zum Erstellen von Anwendungen](service-fabric-overview-microservices.md)
- [Anwendungsszenarien](service-fabric-application-scenarios.md)

<!---HONumber=AcomDC_0224_2016-->