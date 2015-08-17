<properties
   pageTitle="Einführung in die Service Fabric-Integritätsüberwachung"
   description="Dieser Artikel beschreibt das Integritätsüberwachungsmodell von Azure Service Fabric, einschließlich Integritätsentitäten, Berichterstellung und Evaluierung."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>


<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/16/2015"
   ms.author="oanapl"/>


# Einführung in die Service Fabric-Integritätsüberwachung
Service Fabric führt ein Integritätsmodell ein, das eine umfassende, flexible und erweiterbare Integritätsevaluierung und -berichterstellung bietet. Dies umfasst das Überwachen des Clusterzustands und der darin ausgeführten Dienste in nahezu Echtzeit. Integritätsdaten können auf einfache Weise abgerufen werden, um Maßnahmen zum Korrigieren potenzieller Probleme zu ergreifen, bevor sich diese ausbreiten und massive Ausfälle verursachen. In einem typischen Modell senden die Dienste Berichte basierend auf ihrer lokalen Informationen. Anhand dieser Informationen wird ein Gesamtüberblick auf Clusterebene erstellt.

Die Service Fabric-Komponenten verwenden dieses Integritätsmodell, um ihren aktuellen Zustand zu melden. Der gleiche Mechanismus wird verwendet, um Integritätsberichte für Anwendungen zu erstellen. Wie einfach Sie Probleme mit ausgeführten Anwendungen erkennen und beheben können hängt von der Qualität und die Vielfalt der Integritätsberichte ab, die Sie gemäß Ihren benutzerdefinierten Bedingungen festlegen.

> [AZURE.NOTE]Das Integritätssubsystem wurde ursprünglich für das Überwachen von Upgrades eingeführt. Service Fabric bietet überwachte Upgrades, die alle Informationen enthalten, um ein Cluster oder eine Anwendung ohne Ausfallzeit, ohne oder mit minimalem Benutzereingriff und vollständiger Verfügbarkeit von Cluster und Anwendung zu aktualisieren. Hierzu prüft das Upgrade die Integrität basierend auf den konfigurierten Aktualisierungsrichtlinien und erlaubt die Fortführung des Upgrades nur dann, wenn die Integrität die festgelegten Schwellenwerte erfüllt. Werden die Schwellenwerte nicht erfüllt, wird entweder automatisch ein Rollback des Upgrades durchgeführt oder das Upgrade angehalten, damit Administratoren das Problem beheben können. Weitere Informationen zu Anwendungsupgrades finden Sie [in diesem Artikel](service-fabric-application-upgrade.md).

## Integritätsspeicher
Der Integritätsspeicher speichert zustandsbezogene Informationen zu Entitäten im Cluster, um Informationen auf einfache Weise abrufen und evaluieren zu können. Er ist als persistent zustandsbehafteter Service Fabric-Dienst implementiert, um hohe Verfügbarkeit und Skalierbarkeit zu bieten. Der Integritätsspeicher ist Bestandteil der Anwendung fabric:/System und ist verfügbar, sobald der Cluster eingerichtet wurde und ausgeführt wird.

## Integritätsentitäten und Hierarchie
Die Integritätsentitäten werden in einer logischen Hierarchie organisiert, die Interaktionen und Abhängigkeiten zwischen verschiedenen Entitäten erfasst. Die Entitäten und die Hierarchie werden vom Integritätsspeicher auf Grundlage der von den Service Fabric-Komponenten gesendeten Berichten automatisch erstellt.

Die Integritätsentitäten spiegeln die Service Fabric-Entitäten wider (d. h. die Integritätsentität einer Anwendung entspricht einer im Cluster bereitgestellten Anwendungsinstanz und die Integritätsentität eines Knotens einem Service Fabric-Clusterknoten). Die Integritätshierarchie erfasst die Interaktionen der Systementitäten und ist die Grundlage für die erweiterte Integritätsevaluierung. Informationen zu den grundlegenden Service Fabric-Konzepten finden Sie unter [Technische Übersicht über Service Fabric](service-fabric-technical-overview.md). Weitere Informationen zu Anwendungen finden Sie unter [Service Fabric-Anwendungsmodell](service-fabric-application-model.md).

Die Integritätsentitäten und die Hierarchie ermöglichen eine effiziente Berichterstellung und effektives Debuggen und Überwachen des Clusters und der Anwendungen. Das Integritätsmodell ermöglicht eine exakte, **differenzierte** Darstellung der Integrität der zahlreichen beweglichen Bestandteile eines Clusters.

![Integritätsentitäten][1] Integritätsentitäten organisiert in einer Hierarchie, basierend auf Beziehungen zwischen übergeordnete und untergeordneten Elementen

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Zu den Integritätsentitäten zählen Folgende:

- **Cluster**: Stellt die Integrität eines Service Fabric-Clusters dar. Clusterintegritätsberichte beschreiben Bedingungen, die den gesamten Cluster betreffen; das Eingrenzen auf ein oder mehrere untergeordnete fehlerhafte Elemente ist nicht möglich. Beispiel: Der Cluster weist ein Split Brain-Syndrom auf, das auf Netzwerkprobleme mit Partitionierung oder Kommunikation zurückzuführen ist.

- **Node**: Stellt die Integrität eines Service Fabric-Knotens dar. Knotenintegritätsberichte beschreiben die Bedingungen, welche die Knotenfunktionalität und in der Regel alle bereitgestellten Entitäten betreffen. Beispiel: Auf einem Knoten ist nicht mehr genügend Speicherplatz vorhanden (oder andere computerbezogenen Eigenschaften wie Arbeitsspeicher, Verbindungen usw. sind fehlerhaft) oder der Knoten ist ausgefallen. Die Knotenentität wird anhand des Knotennamens (Zeichenfolge) identifiziert.

- **Application**: Stellt die Integrität einer im Cluster ausgeführten Anwendungsinstanz dar. Anwendungsintegritätsberichte beschreiben Bedingungen, die Auswirkungen auf die allgemeine Integrität der Anwendung haben; das Eingrenzen auf einzelne untergeordnete Elemente (Dienste oder bereitgestellte Anwendungen) ist nicht möglich. Beispiel: Die End-to-End-Interaktion zwischen verschiedenen Diensten in der Anwendung. Anwendungsentität wird anhand des Anwendungsnamens (URI) identifiziert.

- **Service**: Stellt die Integrität eines im Cluster ausgeführten Diensts dar. Dienstintegritätsberichte beschreiben Bedingungen, die Auswirkungen auf die allgemeine Integrität des Diensts haben; das Eingrenzen auf eine Partition oder ein Replikat ist nicht möglich. Beispiel: Eine Dienstkonfiguration (z. B. ein Port oder eine externe Dateifreigabe), die in allen Partitionen Probleme verursacht. Die Dienstentität wird anhand des Dienstnamens (URI) identifiziert.

- **Partition**: Stellt die Integrität einer Dienstpartition dar. Partitionsintegritätsberichte beschreiben Bedingungen, die Auswirkungen auf die gesamte Replikatgruppe haben. Beispiel: Die Anzahl der Replikate liegt unterhalb der Zielanzahl oder die Partition weist einen Quorumverlust auf. Die Entität für die Partition wird anhand der Partitions-ID (GUID) identifiziert.

- **Replica**: Stellt die Integrität eines zustandsbehafteten Dienstreplikats oder einer zustandslosen Dienstinstanz dar. Dies ist die kleinsten Einheit einer Anwendung, für die Watchdogs und Systemkomponenten Berichte erstellen können. Beispiel: Ein primäres Replikat erstellt für zustandbehaftete Dienste einen Bericht, wenn Vorgänge in sekundären Replikaten nicht repliziert werden können oder eine Replikation nicht mit der erwarteten Geschwindigkeit durchgeführt wird. Für zustandslose Instanzen kann ein Bericht erstellt werden, wenn die Instanz nicht mehr über genügend Ressourcen verfügt oder Konnektivitätsprobleme bestehen. Die Replikatentität wird anhand der Partitions-ID (GUID) und der Replikat- bzw. Instanz-ID (lang) identifiziert.

- **DeployedApplication**: Stellt die Integrität einer *auf einem Knoten ausgeführten Anwendung* dar. Integritätsberichte zu bereitgestellten Anwendungen beschreiben Bedingungen, die sich auf die Anwendung auf dem Knoten beziehen; das Eingrenzen auf Dienstpakte, die im gleichen Knoten bereitgestellt sind, ist nicht möglich. Beispiel: Das Anwendungspaket kann nicht auf den Knoten heruntergeladen werden oder beim Einrichten der Sicherheitsprinzipale der Anwendung auf dem Knoten ist ein Problem aufgetreten. Die bereitgestellte Anwendung wird anhand des Anwendungsnamens (URI) und des Knotennamens (Zeichenfolge) identifiziert.

- **DeployedServicePackage**: Stellt die Integrität des Dienstpakets einer Anwendung dar, die in einem Knoten im Cluster ausgeführt wird. Es beschreibt Bedingungen, die sich auf das Dienstpaket beziehen und keine Auswirkungen auf andere Dienstpakete habe, die für die gleiche Anwendung auf demselben Knoten bereitgestellt sind. Beispiel: Ein Codepaket im Dienstpaket kann nicht gestartet oder ein Konfigurationspaket kann nicht gelesen werden. Das bereitgestellte Dienstpakte wird anhand des Anwendungsnamens (URI), Knotennamens (Zeichenfolge) und Dienstmanifestnamens (Zeichenfolge) identifiziert.

Die Granularität des Integritätsmodells erleichtert das Erkennen und Beheben von Problemen. Wenn beispielsweise ein Dienst nicht antwortet, kann der fehlerhafte Zustand der Anwendungsinstanz gemeldet werden; dies ist jedoch nicht ideal, da das Problem möglicherweise nicht alle Dienste in der Anwendung betrifft. Der Bericht sollte für den fehlerhaften Dienst oder, falls eine bestimmte untergeordnete Partition die Ursache sein könnte, für die Partition erstellt werden. Die Daten werden automatisch in der Hierarchie angezeigt: eine fehlerhafte Partition wird auf Dienst- und Anwendungsebene sichtbar gemacht. Hierdurch kann die zugrunde liegende Ursache des Problems schneller ermittelt und behoben werden.

Die Integritätshierarchie besteht aus Beziehungen zwischen übergeordneten und untergeordneten Beziehungen. Ein Cluster besteht aus Knoten und Anwendungen; Anwendungen verfügen über Dienste und bereitgestellte Anwendungen; bereitgestellte Anwendungen verfügen über bereitgestellte Dienstpakete. Dienste verfügen über Partitionen, wobei jede Partition über ein oder mehrere Replikate verfügt. Zwischen Knoten und bereitgestellten Entitäten besteht eine besondere Beziehung. Wird ein Knoten von der übergeordneten Systemkomponente (Failover-Manager-Dienst) als fehlerhaft gemeldet, hat dies Auswirkungen auf die bereitgestellten Anwendungen, Dienstpakete und Replikate, die auf dem Knoten bereitgestellt sind.

Die Integritätshierarchie stellt den aktuellen Zustand des Systems basierend auf den aktuellen Integritätsberichten dar, d. h., die Informationen sind nahezu in Echtzeit verfügbar. Interne und externe Watchdogs können Berichte über die selben Entitäten basierend auf anwendungsspezifischer Logik oder benutzerdefinierten überwachte Bedingungen erstellen. Die Benutzerberichte können zusammen mit den Systemberichten verwendet werden.

Indem Sie sich beim Entwerfen der Dienste Zeit nehmen, um zu planen, welche Integritätsberichte erstellt und wie darauf geantwortet werden soll, vereinfachen Sie das Debuggen, Überwachen und letztendlich das Ausführen umfangreicher Clouddienste.

## Integritätszustände
Service Fabric verwendet drei Zustände um die Integrität einer Entität zu beschreiben: "Ok", "Warning" und "Error". Jeder Bericht, der an den Integritätsspeicher gesendet wird, gibt einen der drei Zustände an. Das Ergebnis der Integritätsevaluierung ist einer dieser Zustände.

Mögliche Integritätszustände:

- Ok: Die Entität ist fehlerfrei. Es wurden keine bekannten Probleme für das Element oder eines seiner untergeordneten Elemente (falls zutreffend) gemeldet.

- Warning: Die Entität weist Fehler auf, ist jedoch noch nicht fehlerhaft (d. h., es liegt eine unerwartete Verzögerung vor, die keine funktionalen Probleme verursacht). In einigen Fällen wird die Warnungsbedingung von selbst wieder behoben, ohne dass ein Benutzereingriff erforderlich ist. Es empfiehlt sich jedoch, die Warnung näher zu untersuchen. In anderen Fällen kann sich die Warnungsbedingung ohne Benutzereingriff zu einem schwerwiegenden Problem entwickeln.

- Error: Die Entität ist fehlerhaft. Eine fehlerhafte Entität funktioniert nicht ordnungsgemäß. Der fehlerhafte Zustand der Entität muss durch einen Benutzereingriff behoben werden.

- Unbekannt: Die Entität ist nicht im Integritätsspeicher Integrität vorhanden. Dieses Ergebnis erhalten Sie möglicherweise bei verteilte Abfragen, wie z. B. beim Abrufen von Service Fabric-Knoten oder -Anwendungen. Diese Abfragen führen die Ergebnisse aus mehreren Systemkomponenten zusammen. Weist eine andere Systemkomponente eine Entität auf, die noch nicht an den Integritätsspeicher gesendet bzw. noch nicht aus dem Integritätsspeicher entfernt wurde, füllt die zusammengeführte Abfrage das Integritätsergebnis mit dem Integritätszustand "Unknown" auf.

## Integritätsrichtlinien
Der Integritätsspeicher wendet Integritätsrichtlinien an, um basierend auf den Berichten und untergeordneten Elementen einer Entität festzustellen, ob die Entität fehlerfrei ist.

> [AZURE.NOTE]Die Integritätsrichtlinien werden im Clustermanifest (für die Integritätsevaluierung von Cluster und Knoten) oder im Anwendungsmanifest (für die Evaluierung einer Anwendung und der untergeordneten Elemente) angegeben. Die Integritätsevaluierungsanforderungen können auch an benutzerdefinierte Richtlinien zur Integritätsevaluierung übergeben werden, die nur für diese eine Evaluierung verwendet werden.

Standardmäßig wendet Service Fabric strenge Regeln für die hierarchische Beziehung zwischen übergeordneten und untergeordneten Elementen an (alle Komponenten müssen fehlerfrei sein); solange eines der untergeordneten Elemente ein fehlerhaftes Ereignis aufweist, wird das übergeordnete Element als fehlerhaft eingestuft.

### Clusterintegritätsrichtlinie
Die Clusterintegritätsrichtlinie wird zum Auswerten des Integritätszustands des Clusters und der Knoten verwendet. Die Richtlinie wird im Clustermanifest definiert. Wenn keine Clusterintegritätsrichtlinie vorhanden ist, wird standardmäßig die Standardrichtlinie verwendet (Null-Fehler-Toleranz). Sie enthält folgende Elemente:

- **ConsiderWarningAsError**: Gibt an, ob während der Integritätsevaluierung Integritätsberichte mit dem Ergebnis "Warning" als Fehler zu behandeln sind. Standard: False.

- **MaxPercentUnhealthyApplications**: Maximal tolerierter Prozentsatz an Anwendungen, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird.

- **MaxPercentUnhealthyNodes**: Maximal tolerierter Prozentsatz an Knoten, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Bei der Konfiguration des Prozentsatzes sollte berücksichtigt werden, dass in großen Clustern immer einige Knoten ausfallen können oder für Wartungsarbeiten heruntergefahren sind.

Hier sehen Sie einen Auszug aus einem Clustermanifest:

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />

    <Parameter Name="MaxPercentUnhealthyApplications" Value="0" />

    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />

  </Section>
</FabricSettings>
```

### Anwendungsintegritätsrichtlinie
Die Anwendungsintegritätsrichtlinie beschreibt, wie die Auswertung von Ereignissen und Aggregationen der Zustände von untergeordneten Elementen für eine Anwendung und ihre untergeordneten Elemente erfolgen soll. Diese Richtlinie wird im Anwendungsmanifest in der Datei "ApplicationManifest.xml" im Anwendungspaket definiert werden. Wird keine Anwendungsintegritätsrichtlinie angegeben, nimmt Service Fabric an, dass die Entität fehlerhaft ist, wenn einer der Integritätsberichte oder ein untergeordnetes Element den Integritätszustand "Warning" oder "Error" aufweist. Die folgenden Richtlinien sind konfigurierbar:

- **ConsiderWarningAsError**: Gibt an, ob während der Integritätsevaluierung Integritätsberichte mit dem Ergebnis "Warning" als Fehler zu behandeln sind. Standard: False.

- **MaxPercentUnhealthyDeployedApplications**: Maximal tolerierter Prozentsatz an bereitgestellten Anwendungen, die fehlerhaft sein können, bevor eine Anwendung als fehlerhaft behandelt wird. Der Prozentsatz wird berechnet, indem die Anzahl der fehlerhaften bereitgestellten Anwendungen durch die Anzahl der Knoten geteilt wird, auf denen die Anwendungen im Cluster aktuell bereitgestellt sind. Die Berechnung wird aufgerundet, um einen Fehler auf einer kleinen Anzahl von Knoten zu tolerieren. Standard: 0 %.

- **DefaultServiceTypeHealthPolicy**: Gibt die Standardintegritätsrichtlinie für Diensttypen an, welche die Standardintegritätsrichtlinie für alle Diensttypen in der Anwendung ersetzt.

- **ServiceTypeHealthPolicyMap**: Zuordnung der Dienstintegritätsrichtlinien je Diensttyp, welche die Standardintegritätsrichtlinie für die angegebenen Diensttypen ersetzt. Beispiel: In einer Anwendung, die einen zustandslosen Gateway-Diensttyp und einen zustandsbehafteten Engine-Diensttyp enthält, kann die Integritätsrichtlinie für den zustandsbehafteten und zustandslosen Dienst unterschiedlich konfiguriert werden. Das Festlegen von Richtlinien je Diensttyp ermöglicht eine präzisere Kontrolle über die Dienstintegrität.

### Diensttypintegritätsrichtlinie
Die Diensttypintegritätsrichtlinie gibt an, wie die untergeordneten Elemente des Diensts ausgewertet und aggregiert werden sollen. Sie enthält folgende Elemente:

- **MaxPercentUnhealthyPartitionsPerService**: Maximal tolerierter Prozentsatz an fehlerhafter ab dem ein Dienst als fehlerhaft angesehen wird. Standard: 0 %.

- **MaxPercentUnhealthyReplicasPerPartition**: Maximale toleriert Prozentsatz an fehlerhaften Replikaten, ab dem eine Partition als fehlerhaft angesehen wird. Standard: 0 %.

- **MaxPercentUnhealthyServices**: Maximal tolerierter Prozentsatz an fehlerhaften Diensten, ab dem eine Anwendung als fehlerhaft angesehen wird. Standard: 0 %.

Hier sehen Sie einen Auszug aus einem Anwendungsmanifest:

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>

            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>

            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## Integritätsevaluierung
Benutzer oder automatisierter Dienste können die Integrität einer beliebigen Entität zu einem beliebigen Zeitpunkt auswerten. Um die Integrität einer Entität zu evaluieren, aggregiert der Integritätsspeicher alle Integritätsberichte für die Entität und wertet alle untergeordneten Elemente (falls zutreffend) aus. Die Integritätsaggregationsalgorithmus verwendet Integritätsrichtlinien, die angeben, wie Integritätsberichte ausgewertet und die Zustände untergeordneter Elemente aggregiert werden sollen (falls zutreffend).

### Aggregation von Integritätszuständen
Eine Entität kann mehrere Integritätsberichte aufweisen, die von unterschiedlichen Berichterstattern (Systemkomponenten oder Watchdogs) für verschiedene Eigenschaften gesendet wurden. Die Aggregation verwendet die zugehörigen Integritätsrichtlinien, insbesondere das ConsiderWarningAsError-Mitglied einer Anwendungs- oder Clusterintegritätsrichtlinie, in denen angegeben ist, wie Warnungen ausgewertet werden sollen.

Der aggregierte Integritätszustand wird durch die **schlechtesten** Integritätsberichte für die Entität ausgelöst. Wenn mindestens ein Integritätsbericht mit dem Ergebnis "Error" vorliegt, lautet der aggregierte Integritätszustand "Error".

![Aggregation der Integritätsberichte mit Berichtergebnis "Error"][2]

Der Integritätsbericht mit dem Ergebnis "Error" löst den Integritätszustand "Error" für die Entität aus

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Wenn keine Fehler berichtet werde und eine oder mehre Warnungen vorliegen, lautet der aggregierte Integritätszustand je nach Flag der ConsiderWarningsAsError-Richtlinie entweder "Warning" oder "Error".

![Aggregation der Integritätsberichte mit Berichtergebnis "Warning" und der Einstellung "false" für ConsiderWarningAsError][3]

Aggregation der Integritätsberichte mit dem Ergebnis "Warning" und der Einstellung "false" (Standard) für ConsiderWarningAsError

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Aggregation der Integrität untergeordneter Elemente
Der aggregierten Integritätszustand einer Entität spiegelt die Zustände der untergeordneten Elemente (falls zutreffend) wider. Der Algorithmus zum Aggregieren von Integritätszuständen untergeordneter Elemente verwendet die Integritätsrichtlinien basierend auf dem Entitätstyp.

![Aggregation der Integrität untergeordneter Entitäten][4]

Aggregation untergeordneter Elemente basierend auf Integritätsrichtlinien

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Nach der Auswertung aller untergeordneten Elemente aggregiert der Integritätsspeicher die Integritätszustände basierend auf dem konfigurierten maximalen Prozentsatz fehlerhafter Elemente aus der Richtlinie basierend auf der Entität oder dem untergeordneten Elementtyp.

- Wenn alle untergeordneten Elemente den Zustand "Ok" aufweisen, lautet der aggregierte Integritätszustand "Ok".

- Wenn untergeordnete Elemente mit dem Status "Ok" und "Warning" vorliegen, lautet der aggregierte Integritätszustand "Warning".

- Wenn untergeordnete Elemente mit dem Status "Error" vorliegen und der maximal zulässige Prozentsatz für fehlerhafte untergeordnete Elemente überschritten wird, lautet der aggregierte Integritätszustand "Error".

- Wenn untergeordnete Elemente mit dem Zustand "Error" vorliegen und der maximal zulässige Prozentsatz für fehlerhafte untergeordnete Elemente nicht überschritten wird, lautet der aggregierte Integritätszustand "Warning".

## Integritätsberichterstellung
Berichte für Service Fabric-Entitäten können von Systemkomponenten und internen/externen Watchdogs erstellt werden. Die *Berichterstatter* führen eine **lokale** Ermittlung der Integrität der überwachten Entität basierend auf Bedingungen durch, die sie überwachen. Globale Zustände und aggregierte Daten werden von den Berichterstattern nicht berücksichtigt. Das ist gewollt, da die Berichterstatter andernfalls komplexe Vorgänge durchführen müssten, um die zahlreichen Elemente zu durchsuchen und abzuleiten, welche Informationen gesendet werden sollen.

Um die Integritätsdaten an den Integritätsspeicher zu senden, müssen die Berichterstatter die betroffene Entität identifizieren und einen Integritätsbericht erstellen. Der Bericht kann mithilfe einer API und FabricClient.HealthManager.ReportHealth, über Powershell oder über REST gesendet werden.

### Integritätsberichte
Die Integritätsberichte für die einzelnen Entitäten im Cluster enthalten die folgende Informationen:

- SourceId: Eine Zeichenfolge, die den Berichterstatter des Integritätsereignisses eindeutig identifiziert.

- Entity identifier: Identifiziert die Entität, für die der Bericht erstellt wird. Er unterscheidet sich abhängig vom [Entitätstyp](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster: Keine

  - Node: Knotenname (Zeichenfolge)

  - Application: Anwendungsname (URI); stellt den Namen der im Cluster bereitgestellten Anwendungsinstanz dar.

  - Service: Dienstname (URI); stellt den Namen der im Cluster bereitgestellten Dienstinstanz dar.

  - Partition: Partitions-ID (GUID); stellt den eindeutigen Bezeichner der Partition dar.

  - Replica: Die ID des zustandsbehafteten Dienstreplikats oder die ID der zustandslosen Dienstinstanz (Int64)

  - DeployedApplication: Anwendungsname (URI) und Knotenname (Zeichenfolge)

  - DeployedServicePackage: Anwendungsnamen (URI), Knotennamen (Zeichenfolge) und Dienstmanifestnamen (Zeichenfolge)

- Property: Ein *Zeichenfolge* (keine feste Enumeration), mit der Berichtersteller das Integritätsereignis für eine bestimmte Eigenschaft der Entität kategorisieren. Beispiel: Berichterstatter A kann einen Integritätsbericht für die Eigenschaft "storage" auf Node01 erstellen und Berichterstatter B einen Integritätsbericht für die Eigenschaft "connectivity" auf Node01. Beide Berichte werden im Integritätsspeicher als separate Integritätsereignisse für die Entität "Node01" behandelt.

- Description: Eine Zeichenfolge, die es Berichterstattern ermöglicht, detaillierte Informationen zum Integritätsereignis bereitzustellen. Die Verwendung von "SourceID", "Property" und "HealthState" reicht in der Regel aus, um einen den Bericht vollständig zu beschreiben. Mit "Description" können benutzerfreundliche Informationen hinzugefügt werden, um den Bericht für Administratoren und Benutzern verständlich zu machen.

- HealthState: Eine [Enumeration](service-fabric-health-introduction.md#health-states), die den Integritätszustand des Berichts beschreibt. Zulässige Werte sind "Ok", "Warning" und "Error".

- TimeToLive: Ein Zeitraum, der angibt, wie lange der Integritätsbericht gültig ist. In Kombination mit "RemoveWhenExpired" weist dieser Wert den Integritätsspeicher an, wie abgelaufene Ereignisse ausgewertet werden sollen. Der Zeitraum ist standardmäßig unendlich, und der Bericht ist immer gültig.

- RemoveWhenExpired: Ein boolescher Wert. Wenn "true" festgelegt ist, wird der abgelaufene Integritätsbericht automatisch aus dem Integritätsspeicher entfernt und bei der Integritätsevaluierung der Entität nicht berücksichtigt. Wird verwendet, wenn der Bericht nur für einen Zeitraum gilt, und der Berichterstatter den Bericht nicht explizit bereinigen muss. Wird auch verwendet, um Berichte aus dem Integritätsspeicher zu löschen. Beispiel: Ein Watchdog wird so geändert, dass das Senden von Berichten mit identischer vorheriger Quelle und Eigenschaft gestoppt wird. So kann mit einem geringen Wert für die Lebensdauer (Time-to-Live, TTL) und "RemoveWhenExpired" ein Bericht gesendet werden, um alle vorherigen Zustände aus dem Integritätsspeicher zu löschen. Wenn "false" festgelegt ist, wird der abgelaufene Bericht als Fehler bei der Integritätsevaluierung behandelt. Hierdurch wird dem Integritätsspeicher signalisiert, dass die Quelle in regelmäßigen Abständen eine Bericht für diese Eigenschaft erstellen soll; werden die Berichte nicht erstellt, ist der Watchdog fehlerhaft. Die Integrität des Watchdogs wird berücksichtigt, indem das Ereignis als Fehler behandelt wird.

- SequenceNumber: Eine positive ganze Zahl, welche die Reihenfolge der Berichte darstellt und daher stetig erhöht wird. Der Wert wird vom Integritätsspeicher verwendet, um überfällige Berichte zu ermitteln, die wegen Netzwerkverzögerugen oder anderen Problemen verspätet empfangen werden. Ein Bericht werden abgelehnt, wenn die Sequenznummer kleiner als oder gleich der letzten angewendeten Nummer für die gleiche Entität, Quelle und Eigenschaft ist. Die Sequenznummer wird automatisch erstellt, wenn nicht anders angegeben. Zum Erstellen von Berichten zu Zustandsübergängen muss lediglich die Sequenznummer eingegeben werden: Die Quelle muss wissen, welche Berichte von ihr gesendet wurden, und die Informationen beibehalten, um Informationen für die Wiederherstellung nach einem Ausfall bereitstellen zu können.

"SourceID", "entity identifier", "Property" und "HealthState" sind für jeden Integritätsbericht erforderlich. Die SourceId-Zeichenfolge darf nicht mit dem Präfix "System." beginnen, da dieses Präfix Systemberichten vorbehalten ist. Für eine Entität kann jeweils nur ein Bericht für die gleiche Quelle und Eigenschaft vorhanden sein; werden mehrere Berichte für die gleichen Quelle und Eigenschaft generiert, überschreiben sich die Berichte gegenseitig entweder auf dem Integritätsclient (wenn sie als Stapel verarbeitet werden) oder im Integritätsspeicher. Das Überschreiben erfolgt anhand der Sequenznummer: neuere Berichte (mit höherer Sequenznummer) überschreiben ältere Berichte.

### Integritätsereignisse
Der Integritätsspeicher speichert intern Integritätsereignisse, die alle Informationen aus den Berichten sowie zusätzliche Metadaten enthalten, wie den Zeitpunkt der Übergabe an den Integritätsclient und den Zeitpunkt der Änderung auf Serverseite. Die Integritätsereignisse werden von den [Integritätsabfragen](service-fabric-view-entities-aggregated-health.md#health-queries) zurückgegeben.

Die hinzugefügte Metadaten enthalten Folgendes:

- SourceUtcTimestamp: Der Zeitpunkt, zu dem der Bericht an den Integritätsclient übergeben wurde (UTC).

- LastModifiedUtcTimestamp: Der Zeitpunkt der letzten Änderungen des Berichts auf Serverseite (UTC).

- IsExpired: Flag, das angibt, ob der Bericht zum Zeitpunkt der Ausführung der Abfrage durch den Integritätsspeicher bereits abgelaufen war. Ein Ereignis kann nur dann abgelaufen sein, wenn RemoveWhenExpired "false" ist; andernfalls wird das Ereignis nicht von der Abfrage zurückgegeben, sondern aus dem Speicher entfernt.

- LastOkTransitionAt, LastWarningTransitionAt, LastErrorTransitionAt: Zeitpunkt des letzten Übergangs zu "Ok/Warning/Error". Diese Felder geben den Verlauf der Übergänge der Integritätszustände für das Ereignis an.

Die Felder für die Zustandsübergänge können für erweiterte Warnungen verwendet werden und geben Verlaufsinformationen für das Integritätsereignis an. Sie ermöglichen beispielsweise folgende Szenarien:

- Warnung ausgeben, wenn der Zustand einer Eigenschaft länger als X Minuten den Zustand "Warning/Error/" aufweist. Dadurch werden Warnungen zu vorübergehenden Bedingungen vermieden. Beispiel: Eine Warnung, die ausgegeben wird, wenn der Integritätsstatus "Warning" länger als 5 Minuten besteht, wird folgendermaßen angegeben: (HealthState == Warning und Now - LastWarningTransitionTime > 5 minutes).
> 5 Minuten).

- Warnung nur zu Bedingungen ausgeben, die in den letzten X Minuten geändert wurden. Bericht, die vor diesem Zeitpunkt den Status "Error" haben, werden ignoriert (da dieser bereits zuvor signalisiert wurde).

- Beim Umschalten des Zustands einer Eigenschaft zwischen "Warning" und "Error" bestimmen, wie lange die Eigenschaft fehlerhaft (d. h. nicht "Ok") war. Beispiel: Eine Warnung, die ausgegeben wird, wenn die Eigenschaft länger als 5 Minuten fehlerhaft war, wird folgendermaßen angegeben: (HealthState != Ok und Now - LastOkTransitionTime > 5 minutes).

## Beispiel: Erstellen und Evaluieren von Berichten zur Anwendungsintegrität
Im folgenden Beispiel wird über Powershell ein Integritätsbericht zur Anwendung "fabric:/WordCound" aus der Quelle "MyWatchdog" gesendet. Der Integritätsbericht enthält Informationen zur Integritätseigenschaft "Availability", die den Integritätszustand "Error" und den TTL-Wert "Infinite" aufweist. Anschließend wird die Anwendungsintegrität abgefragt. Die Abfrage gibt den aggregierten Integritätszustand "Error" und das gemeldete Integritätsereignis in der Liste der Integritätsereignisse zurück.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 5102
                                  SentAt                : 4/15/2015 5:29:15 PM
                                  ReceivedAt            : 4/15/2015 5:29:15 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/15/2015 5:29:15 PM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 130736794527105907
                                  SentAt                : 4/16/2015 5:37:32 PM
                                  ReceivedAt            : 4/16/2015 5:37:32 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Error = 4/16/2015 5:37:32 PM

```

## Verwendung des Integritätsmodells
Das Integritätsmodell ermöglicht die Skalierung der Clouddienste und der zugrundeliegenden Service Fabric-Plattform, da die Überwachung und Ermittlung der Integrität auf verschiedene Überwachungselemente im Cluster verteilt wird. Andere Systeme verfügen über einen einzelnen zentralen Dienst auf Clusterebene, der alle *potenziell* nützlichen Informationen analysiert, die von den Diensten ausgegeben werden. Dies behindert die Skalierbarkeit, und es können keine spezifischen Informationen gesammelt werden, um die Hauptursache von Problemen oder potenziellen Problemen soweit wie möglich einzugrenzen.

Das Integritätsmodell wird hauptsächlich für Überwachung und Diagnose, Evaluierung der Cluster- und Anwendungsintegrität und Überwachung von Upgrades verwendet. Andere Dienste nutzen die Integritätsdaten, um automatische Reparaturen durchzuführen, Verläufe der Clusterintegrität zu erstellen und Warnungen bei bestimmten Bedingungen auszugeben.

## Nächste Schritte
[Gewusst wie: Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)

[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten](service-fabric-report-health.md)

[Gewusst wie: Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)
 

<!---HONumber=August15_HO6-->