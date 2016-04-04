<properties
   pageTitle="Integritätsüberwachung in Service Fabric | Microsoft Azure"
   description="Eine Einführung in das Service Fabric-Integritätsüberwachungsmodell von Azure, das die Überwachung des Clusters sowie seiner Programme und Dienste ermöglicht."
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
   ms.date="03/23/2016"
   ms.author="oanapl"/>

# Einführung in die Service Fabric-Integritätsüberwachung
Mit Azure Service Fabric wird ein Integritätsmodell eingeführt, das eine umfassende, flexible und erweiterbare Integritätsevaluierung und -berichterstellung bietet. Dies umfasst das Überwachen des Clusterzustands und der darin ausgeführten Dienste nahezu in Echtzeit. Integritätsdaten können auf einfache Weise abgerufen werden, um Maßnahmen zum Korrigieren potenzieller Probleme zu ergreifen, bevor sich diese ausbreiten und massive Ausfälle verursachen. In einem typischen Modell senden die Dienste Berichte basierend auf ihren lokalen Informationen. Anhand dieser Informationen wird ein Gesamtüberblick auf Clusterebene erstellt.

Die Service Fabric-Komponenten verwenden dieses Integritätsmodell, um ihren aktuellen Zustand zu melden. Sie können den gleichen Mechanismus verwenden, um Integritätsberichte für Anwendungen zu erstellen. Wie einfach Sie Probleme mit ausgeführten Anwendungen erkennen und beheben können, hängt von der Qualität und die Vielfalt der Integritätsberichte ab, die Sie gemäß Ihren benutzerdefinierten Bedingungen festlegen.

> [AZURE.NOTE] Das Integritätssubsystem wurde ursprünglich für das Überwachen von Upgrades eingeführt. Service Fabric bietet überwachte Upgrades, die alle Informationen enthalten, um ein Cluster oder eine Anwendung ohne Ausfallzeit, völlig ohne oder nur mit minimalem Benutzereingriff und mit vollständiger Verfügbarkeit von Cluster und Anwendung zu aktualisieren. Hierzu prüft das Upgrade die Integrität basierend auf den konfigurierten Aktualisierungsrichtlinien und erlaubt die Fortführung des Upgrades nur dann, wenn die Integrität die festgelegten Schwellenwerte einhält. Werden die Schwellenwerte nicht erfüllt, wird entweder automatisch ein Rollback des Upgrades durchgeführt oder das Upgrade angehalten, damit Administratoren das Problem beheben können. Weitere Informationen zu Anwendungsupgrades finden Sie in [diesem Artikel](service-fabric-application-upgrade.md).

## Integritätsspeicher
Der Integritätsspeicher speichert integritätsbezogene Informationen zu Entitäten im Cluster, um Informationen auf einfache Weise abrufen und evaluieren zu können. Er ist als persistent zustandsbehafteter Service Fabric-Dienst implementiert, um hohe Verfügbarkeit und Skalierbarkeit zu bieten. Der Integritätsspeicher ist Bestandteil der Anwendung **fabric:/System** und ist verfügbar, sobald der Cluster eingerichtet wurde und ausgeführt wird.

## Integritätsentitäten und Hierarchie
Die Integritätsentitäten werden in einer logischen Hierarchie organisiert, die Interaktionen und Abhängigkeiten zwischen verschiedenen Entitäten erfasst. Die Entitäten und die Hierarchie werden vom Integritätsspeicher auf Grundlage der von den Service Fabric-Komponenten gesendeten Berichte automatisch erstellt.

Die Integritätsentitäten spiegeln die Service Fabric-Entitäten wider. (Beispielsweise entspricht die **Integritätsentität einer Anwendung** einer im Cluster bereitgestellten Anwendungsinstanz, und die **Integritätsentität eines Knotens** entspricht einem Service Fabric-Clusterknoten.) Die Integritätshierarchie erfasst die Interaktionen der Systementitäten, und dies ist die Grundlage für die erweiterte Integritätsevaluierung. Informationen zu den grundlegenden Service Fabric-Konzepten finden Sie unter [Technische Übersicht über Service Fabric](service-fabric-technical-overview.md). Weitere Informationen zu Anwendungen finden Sie unter [Service Fabric-Anwendungsmodell](service-fabric-application-model.md).

Die Integritätsentitäten und die Hierarchie ermöglichen das effektive Melden, Debuggen und Überwachen des Clusters und der Anwendungen. Das Integritätsmodell ermöglicht eine exakte, *differenzierte* Darstellung der Integrität der zahlreichen beweglichen Bestandteile eines Clusters.

![Integritätsentitäten][1] In einer Hierarchie organisierte Integritätsentitäten, basierend auf Beziehungen zwischen übergeordneten und untergeordneten Elementen.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Zu den Integritätsentitäten zählen Folgende:

- **Cluster**: Stellt die Integrität eines Service Fabric-Clusters dar. Clusterintegritätsberichte beschreiben Bedingungen, die den gesamten Cluster betreffen; das Eingrenzen auf ein oder mehrere untergeordnete fehlerhafte Elemente ist nicht möglich. Beispiele hierfür sind das Split-Brain-Syndrom des Clusters aufgrund von Problemen mit der Netzwerkpartitionierung oder der Kommunikation.

- **Node**: Stellt die Integrität eines Service Fabric-Knotens dar. In Knotenintegritätsberichten werden Bedingungen beschrieben, die sich auf die Knotenfunktionen auswirken. Sie wirken sich normalerweise auf alle bereitgestellten Entitäten aus, die darauf ausgeführt werden. Beispiele hierfür sind ein Knoten ohne verbleibenden Festplattenplatz (oder eine andere computerweite Eigenschaft, z. B. Arbeitsspeicher, Verbindungen) und der Ausfall eines Knotens. Die Knotenentität wird anhand des Knotennamens (Zeichenfolge) identifiziert.

- **Application**: Stellt die Integrität einer im Cluster ausgeführten Anwendungsinstanz dar. In Berichten zur Anwendungsintegrität werden Bedingungen beschrieben, die sich auf die Gesamtintegrität der Anwendung auswirken. Sie können nicht auf einzelne untergeordnete Elemente (Dienste oder bereitgestellte Anwendungen) eingegrenzt werden. Ein Beispiel hierfür ist die End-to-End-Interaktion zwischen verschiedenen Diensten in der Anwendung. Die Anwendungsentität wird anhand des Anwendungsnamens (URI) identifiziert.

- **Service**: Stellt die Integrität eines im Cluster ausgeführten Diensts dar. Dienstintegritätsberichte beschreiben Bedingungen, die Auswirkungen auf die allgemeine Integrität des Diensts haben. Das Eingrenzen auf eine Partition oder ein Replikat ist nicht möglich. Ein Beispiel hierfür ist eine Dienstkonfiguration (z. B. ein Port oder eine externe Dateifreigabe), die in allen Partitionen Probleme verursacht. Die Dienstentität wird anhand des Dienstnamens (URI) identifiziert.

- **Partition**: Stellt die Integrität einer Dienstpartition dar. Partitionsintegritätsberichte beschreiben Bedingungen, die Auswirkungen auf die gesamte Replikatgruppe haben. Beispiele hierfür sind eine Anzahl der Replikate unterhalb der Zielanzahl und eine Partition mit Quorumverlust. Die Entität für die Partition wird anhand der Partitions-ID (GUID) identifiziert.

- **Replica**: Stellt die Integrität eines zustandsbehafteten Dienstreplikats oder einer zustandslosen Dienstinstanz dar. Dies ist die kleinste Einheit einer Anwendung, für die Watchdogs und Systemkomponenten Berichte erstellen können. Beispiele für zustandsbehaftete Dienste: Ein primäres Replikat, das meldet, wenn Vorgänge in sekundären Replikaten nicht repliziert werden können, oder eine Replikation, die nicht mit der erwarteten Geschwindigkeit durchgeführt wird. Außerdem kann für eine zustandslose Instanz gemeldet werden, wenn die Instanz nicht mehr über genügend Ressourcen verfügt oder Konnektivitätsprobleme bestehen. Die Replikatentität wird anhand der Partitions-ID (GUID) und der Replikat- bzw. Instanz-ID (lang) identifiziert.

- **DeployedApplication**: Stellt die Integrität einer *auf einem Knoten ausgeführten Anwendung* dar. Integritätsberichte zu bereitgestellten Anwendungen beschreiben Bedingungen, die sich auf die Anwendung auf dem Knoten beziehen; das Eingrenzen auf Dienstpakete, die im gleichen Knoten bereitgestellt sind, ist nicht möglich. Beispiele: Das Anwendungspaket kann nicht auf den Knoten heruntergeladen werden, oder beim Einrichten der Sicherheitsprinzipale der Anwendung auf dem Knoten tritt ein Problem auf. Die bereitgestellte Anwendung wird anhand des Anwendungsnamens (URI) und des Knotennamens (Zeichenfolge) identifiziert.

- **DeployedServicePackage**: Stellt die Integrität des Dienstpakets einer Anwendung dar, die in einem Knoten im Cluster ausgeführt wird. Es beschreibt Bedingungen, die sich auf das Dienstpaket beziehen und keine Auswirkungen auf andere Dienstpakete habe, die für die gleiche Anwendung auf demselben Knoten bereitgestellt sind. Beispiele hierfür sind ein Codepaket im Dienstpaket, das nicht gestartet werden kann, und ein Konfigurationspaket, das nicht gelesen werden kann. Das bereitgestellte Dienstpaket wird anhand des Anwendungsnamens (URI), Knotennamens (Zeichenfolge) und Dienstmanifestnamens (Zeichenfolge) identifiziert.

Die Granularität des Integritätsmodells erleichtert das Erkennen und Beheben von Problemen. Wenn ein Dienst beispielsweise nicht reagiert, kann gemeldet werden, dass die Anwendungsinstanz nicht „integer“ (also fehlerhaft) ist. Dies ist aber nicht ideal, da sich das Problem unter Umständen nicht auf alle Dienste in dieser Anwendung bezieht. Der Bericht sollte auf den fehlerhaften Dienst oder eine bestimmte untergeordnete Partition angewendet werden, falls weitere Informationen auf diese Partition hindeuten. Die Daten werden automatisch in der Hierarchie angezeigt, und eine fehlerhafte Partition wird auf Dienst- und Anwendungsebene sichtbar gemacht. Hierdurch kann die zugrunde liegende Ursache des Problems schneller ermittelt und behoben werden.

Die Integritätshierarchie besteht aus Beziehungen zwischen übergeordneten und untergeordneten Elementen. Ein Cluster besteht aus Knoten und Anwendungen. Anwendungen verfügen über Dienste und bereitgestellte Anwendungen. Bereitgestellte Anwendungen verfügen über bereitgestellte Pakete. Dienste verfügen über Partitionen, wobei jede Partition über ein oder mehrere Replikate verfügt. Zwischen Knoten und bereitgestellten Entitäten besteht eine besondere Beziehung. Wird ein Knoten von der übergeordneten Systemkomponente (Failover-Manager-Dienst) als fehlerhaft gemeldet, hat dies Auswirkungen auf die darauf bereitgestellten Anwendungen, Dienstpakete und Replikate.

Die Integritätshierarchie stellt den aktuellen Zustand des Systems basierend auf den aktuellen Integritätsberichten dar, d. h., die Informationen sind nahezu in Echtzeit verfügbar. Interne und externe Watchdogs können Berichte über dieselben Entitäten basierend auf anwendungsspezifischer Logik oder benutzerdefinierten überwachten Bedingungen erstellen. Benutzerberichte können zusammen mit den Systemberichten verwendet werden.

Beim Entwerfen eines großen Clouddiensts kann die Zeit, die Sie für die Planung des Meldens und Reagierens auf Integritätsprobleme aufwenden, das Debugging, die Überwachung und den nachfolgenden Betrieb des Diensts erleichtern.

## Integritätszustände
Service Fabric verwendet drei Zustände, um die Integrität einer Entität zu beschreiben: „OK“, „Warning“ und „Error“. In jedem Bericht, der an den Integritätsspeicher gesendet wird, ist einer der Zustände angegeben. Das Ergebnis der Integritätsevaluierung ist einer dieser Zustände.

Mögliche [Integritätszustände](https://msdn.microsoft.com/library/azure/system.fabric.health.healthstate):

- **OK**. Die Entität ist fehlerfrei. Es wurden keine bekannten Probleme für das Element oder eines seiner untergeordneten Elemente (falls zutreffend) gemeldet.

- **Warning**. Die Entität weist Fehler auf, ist jedoch noch nicht „fehlerhaft“ (keine unerwartete Verzögerung, die funktionale Probleme verursacht). In einigen Fällen wird die Warnungsbedingung von selbst wieder behoben, ohne dass ein Benutzereingriff erforderlich ist. Es empfiehlt sich jedoch, die Warnung näher zu untersuchen. In anderen Fällen kann sich die Warnungsbedingung ohne Benutzereingriff zu einem schwerwiegenden Problem entwickeln.

- **Error**. Die Entität ist fehlerhaft. Eine fehlerhafte Entität funktioniert nicht richtig, und der fehlerhafte Zustand der Entität muss durch einen Benutzereingriff behoben werden.

- **Unknown**. Die Entität ist im Integritätsspeicher nicht vorhanden. Dieses Ergebnis kann über die verteilten Abfragen abgerufen werden, mit denen Ergebnisse mehrerer Komponenten zusammengeführt werden. Diese können auch die Abfrage zum Abrufen der Liste mit den Service Fabric-Knoten (über **FailoverManager** und **HealthManager**) oder die Abfrage zum Abrufen der Liste mit den Anwendungen (über **ClusterManager** und **HealthManager**) enthalten. Diese Abfragen führen die Ergebnisse aus mehreren Systemkomponenten zusammen. Wenn eine andere Systemkomponente eine Entität aufweist, die noch nicht an den Integritätsspeicher gesendet oder aus dem Integritätsspeicher entfernt wurde, gibt die zusammengeführte Abfrage das Integritätsergebnis als Integritätszustand „Unknown“ an.

## Integritätsrichtlinien
Der Integritätsspeicher wendet Integritätsrichtlinien an, um basierend auf den Berichten und untergeordneten Elementen einer Entität festzustellen, ob die Entität fehlerfrei ist.

> [AZURE.NOTE] Integritätsrichtlinien werden im Clustermanifest (für die Integritätsevaluierung von Cluster und Knoten) oder im Anwendungsmanifest (für die Evaluierung einer Anwendung und der untergeordneten Elemente) angegeben. Integritätsevaluierungsanforderungen können auch an benutzerdefinierte Richtlinien zur Integritätsevaluierung übergeben werden, die nur für diese eine Evaluierung verwendet werden.

Standardmäßig wendet Service Fabric strenge Regeln (alles muss fehlerfrei sein) auf hierarchische Beziehungen von über- und untergeordneten Elementen an. Wenn auch nur eines der untergeordneten Elemente ein fehlerhaftes Ereignis aufweist, wird das übergeordnete Element als fehlerhaft angesehen.

### Clusterintegritätsrichtlinie
Die [Clusterintegritätsrichtlinie](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.aspx) wird zum Auswerten des Integritätszustands des Clusters und der Knoten verwendet. Die Richtlinie kann im Clustermanifest definiert werden. Falls sie nicht vorhanden ist, wird die Standardrichtlinie (keine Fehler zulässig) verwendet. Die Clusterintegritätsrichtlinie enthält Folgendes:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.considerwarningaserror.aspx): Gibt an, ob während der Integritätsevaluierung Integritätsberichte mit dem Ergebnis „Warning“ als Fehler zu behandeln sind. Standardwert: false.

- [MaxPercentUnhealthyApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthyapplications.aspx): Gibt den maximal tolerierten Prozentsatz an Anwendungen an, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird.

- [MaxPercentUnhealthyNodes](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.maxpercentunhealthynodes.aspx): Gibt den maximal tolerierten Prozentsatz an Knoten an, die fehlerhaft sein können, bevor der Cluster als fehlerhaft behandelt wird. Bei der Konfiguration des Prozentsatzes sollte berücksichtigt werden, dass in großen Clustern immer einige Knoten ausfallen können oder für Wartungsarbeiten heruntergefahren sein können.

- [ApplicationTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.clusterhealthpolicy.applicationtypehealthpolicymap.aspx). Die Zuordnung der Anwendungstyp-Integritätsrichtlinie kann während der Clusterintegritätsevaluierung verwendet werden, um spezielle Anwendungstypen zu beschreiben. Standardmäßig werden alle Anwendungen in einen Pool eingefügt und anhand von MaxPercentUnhealthyApplications bewertet. Wenn spezielle Anwendungstypen vorliegen, die auf andere Weise behandelt werden sollen, können diese aus dem globalen Pool entfernt und anhand von Prozentangaben ausgewertet werden, die in der Zuordnung ihrem Anwendungstypnamen zugeordnet sind. Beispielsweise enthält ein Cluster Tausende von Anwendungen mit unterschiedlichen Typen und wenige Steueranwendungsinstanzen eines besonderen Anwendungstyps. Die Steueranwendungen dürfen niemals einen Fehlerstatus aufweisen. Benutzer können daher den globalen MaxPercentUnhealthyApplications-Wert auf 20 % setzen, um einige Fehler zu tolerieren, aber für den Anwendungstyp „ControlApplicationType“ wird der MaxPercentUnhealthyApplications-Wert auf 0 festgelegt. Wenn einige der zahlreichen Anwendungen fehlerhaft sind, aber unter dem globalen Prozentsatz für fehlerhafte Anwendungen liegen, wird der Cluster mit einer Warnung ausgewertet. Der Integritätszustand „Warnung“ wirkt sich nicht auf die ein Clusterupgrade oder auf andere Überwachungen aus, die durch den Integritätszustand „Fehler“ ausgelöst werden. Dahingegen führt eine einzige fehlerhafte Steueranwendung zu einem Clusterintegritätsfehler, durch den ein Clusterupgrade verhindert bzw. ein Rollback durchgeführt wird. Für die in der Zuordnung definierten Anwendungstypen werden alle Anwendungsinstanzen aus dem globalen Anwendungspool entfernt. Sie werden anhand des speziellen MaxPercentUnhealthyApplications-Werts aus der Zuordnung basierend auf der Gesamtanzahl von Anwendungen des Anwendungstyps ausgewertet. Die restlichen Anwendungen verbleiben im globalen Pool und werden mit MaxPercentUnhealthyApplications ausgewertet.

Hier sehen Sie einen Auszug aus einem Clustermanifest: Um Einträge in der Anwendungstypzuordnung zu definieren, stellen Sie dem Parameternamen das Präfix „ApplicationTypeMaxPercentUnhealthyApplications-“, gefolgt vom Namen des Anwendungstyps, voran.

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="20" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
    <Parameter Name="ApplicationTypeMaxPercentUnhealthyApplications-ControlApplicationType" Value="0" />
  </Section>
</FabricSettings>
```

### Anwendungsintegritätsrichtlinie
Die [Anwendungsintegritätsrichtlinie](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.aspx) beschreibt, wie die Auswertung von Ereignissen und Aggregationen der Zustände von untergeordneten Elementen für eine Anwendung und ihre untergeordneten Elemente erfolgen soll. Diese Richtlinie kann im Anwendungsmanifest (**ApplicationManifest.xml**) im Anwendungspaket definiert werden. Wenn keine Richtlinien angegeben sind, geht Service Fabric davon aus, dass die Entität fehlerhaft ist, sofern sie über einen Integritätsbericht oder ein untergeordnetes Element mit dem Integritätsstatus „Warning“ oder „Error“ verfügt. Die folgenden Richtlinien sind konfigurierbar:

- [ConsiderWarningAsError](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.considerwarningaserror.aspx): Gibt an, ob während der Integritätsevaluierung Integritätsberichte mit dem Ergebnis „Warning“ als Fehler zu behandeln sind. Standardwert: false.

- [MaxPercentUnhealthyDeployedApplications](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.maxpercentunhealthydeployedapplications.aspx): Gibt den maximal tolerierten Prozentsatz an bereitgestellten Anwendungen an, die fehlerhaft sein können, bevor eine Anwendung als fehlerhaft behandelt wird. Der Prozentsatz wird berechnet, indem die Anzahl der fehlerhaften bereitgestellten Anwendungen durch die Anzahl der Knoten geteilt wird, auf denen die Anwendungen im Cluster aktuell bereitgestellt sind. Die Berechnung wird aufgerundet, um einen Fehler auf einer kleinen Anzahl von Knoten zu tolerieren. Standardprozentsatz : null.

- [DefaultServiceTypeHealthPolicy](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.defaultservicetypehealthpolicy.aspx): Gibt die Standardintegritätsrichtlinie für Diensttypen an, welche die Standardintegritätsrichtlinie für alle Diensttypen in der Anwendung ersetzt.

- [ServiceTypeHealthPolicyMap](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthpolicy.servicetypehealthpolicymap.aspx): Bietet eine Übersicht über Dienstintegritätsrichtlinien pro Diensttyp. Diese ersetzen die standardmäßigen Diensttyp-Integritätsrichtlinien für jeden angegebenen Diensttyp. Beispiel: In einer Anwendung, die sowohl einen zustandslosen Gateway-Diensttyp als auch einen zustandsbehafteten Modul-Diensttyp enthält, können die Integritätsrichtlinien für die zustandsbehafteten und zustandslosen Dienste unterschiedlich konfiguriert werden. Wenn Sie die Richtlinie pro Diensttyp angeben, können Sie die Integrität des Diensts genauer steuern.

### Diensttyp-Integritätsrichtlinie
Die [Diensttyp-Integritätsrichtlinie](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.aspx) gibt an, wie die Dienste und die untergeordneten Elemente von Diensten ausgewertet und aggregiert werden sollen. Die Richtlinie enthält Folgendes:

- [MaxPercentUnhealthyPartitionsPerService](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthypartitionsperservice.aspx): Gibt den maximal tolerierten Prozentsatz an fehlerhaften Partitionen an, ab dem ein Dienst als fehlerhaft angesehen wird. Standardprozentsatz : null.

- [MaxPercentUnhealthyReplicasPerPartition](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyreplicasperpartition.aspx): Gibt den maximal tolerierten Prozentsatz an fehlerhaften Replikaten an, ab dem eine Partition als fehlerhaft angesehen wird. Standardprozentsatz : null.

- [MaxPercentUnhealthyServices](https://msdn.microsoft.com/library/azure/system.fabric.health.servicetypehealthpolicy.maxpercentunhealthyservices.aspx): Gibt den maximal tolerierten Prozentsatz an fehlerhaften Diensten an, ab dem eine Anwendung als fehlerhaft angesehen wird. Standardprozentsatz : null.

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
Benutzer und automatisierte Dienste können die Integrität einer beliebigen Entität jederzeit evaluieren. Um die Integrität einer Entität zu evaluieren, aggregiert der Integritätsspeicher alle Integritätsberichte für die Entität und wertet alle untergeordneten Elemente (falls zutreffend) aus. Der Algorithmus für die Integritätsaggregation verwendet Integritätsrichtlinien, die angeben, wie Integritätsberichte ausgewertet und die Zustände untergeordneter Elemente aggregiert werden sollen (falls zutreffend).

### Aggregation von Integritätsberichten
Eine Entität kann mehrere Integritätsberichte aufweisen, die von unterschiedlichen Berichterstattern (Systemkomponenten oder Watchdogs) für verschiedene Eigenschaften gesendet wurden. Die Aggregation verwendet die zugehörigen Integritätsrichtlinien, insbesondere das ConsiderWarningAsError-Element einer Anwendungs- oder Clusterintegritätsrichtlinie. Darin wird angegeben ist, wie Warnungen ausgewertet werden sollen.

Der aggregierte Integritätszustand wird durch die *schlechtesten* Integritätsberichte für die Entität ausgelöst. Wenn mindestens ein Integritätsbericht mit dem Ergebnis „Error“ vorliegt, lautet der aggregierte Integritätszustand „Error“.

![Aggregation der Integritätsberichte mit Berichtergebnis „Error“][2]

Ein Integritätsbericht mit Fehler oder ein abgelaufener Integritätsbericht (unabhängig von seinem Integritätszustand) löst den Fehlerzustand der Integritätsentität aus.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

Wenn keine Fehler berichtet werden und eine oder mehrere Warnungen vorliegen, lautet der aggregierte Integritätszustand je nach Flag der ConsiderWarningsAsError-Richtlinie entweder „Warning“ oder „Error“.

![Aggregation der Integritätsberichte mit Berichtergebnis „Warning“ und der Einstellung „false“ für ConsiderWarningAsError][3]

Aggregation der Integritätsberichte mit dem Ergebnis „Warning“ und der Einstellung „false“ (Standard) für ConsiderWarningAsError

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Aggregation der untergeordneten Integrität
Der aggregierte Integritätszustand einer Entität spiegelt die Zustände der untergeordneten Elemente wider (falls zutreffend). Der Algorithmus zum Aggregieren von Integritätszuständen untergeordneter Elemente verwendet die Integritätsrichtlinien basierend auf dem Entitätstyp.

![Aggregation der Integrität untergeordneter Entitäten][4]

Aggregation untergeordneter Elemente basierend auf Integritätsrichtlinien

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Nachdem der Integritätsspeicher alle untergeordneten Elemente ausgewertet hat, werden deren Integritätszustände basierend auf dem konfigurierten maximalen Prozentsatz für fehlerhafte untergeordnete Elemente aggregiert. Dies wird über die Richtlinie basierend auf der Entität und dem Typ des untergeordneten Elements abgeleitet.

- Wenn alle untergeordneten Elemente den Zustand „OK“ aufweisen, lautet der aggregierte Integritätszustand „OK“.

- Wenn sowohl untergeordnete Elemente mit dem Status „OK“ als auch mit dem Status „Warning“ vorliegen, lautet der aggregierte Integritätszustand „Warning“.

- Wenn untergeordnete Elemente mit dem Status „Error“ vorliegen und der maximal zulässige Prozentsatz für fehlerhafte untergeordnete Elemente überschritten wird, lautet der aggregierte Integritätszustand „Error“.

- Wenn untergeordnete Elemente mit dem Zustand „Error“ vorliegen und der maximal zulässige Prozentsatz für fehlerhafte untergeordnete Elemente nicht überschritten wird, lautet der aggregierte Integritätszustand „Warning“.

## Integritätsberichterstellung
Berichte für Service Fabric-Entitäten können von Systemkomponenten, Systemfabricanwendungen und internen/externen Watchdogs erstellt werden. Die Berichterstatter führen eine *lokale* Ermittlung der Integrität der überwachten Entitäten basierend auf den Bedingungen durch, die sie überwachen. Globale Zustände oder aggregierte Daten werden von den Berichterstattern nicht berücksichtigt. Dies ist gewollt, da die Berichterstatter andernfalls komplexe Vorgänge durchführen müssten, um die zahlreichen Elemente zu durchsuchen und abzuleiten, welche Informationen gesendet werden sollen.

Um die Integritätsdaten an den Integritätsspeicher zu senden, muss ein Berichterstatter die betroffene Entität identifizieren und einen Integritätsbericht erstellen. Der Bericht kann dann über die API mit [FabricClient.HealthClient.ReportHealth](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient_members.aspx), über PowerShell oder über REST gesendet werden.

### Integritätsberichte
Die [Integritätsberichte](https://msdn.microsoft.com/library/azure/system.fabric.health.healthreport.aspx) für die einzelnen Entitäten im Cluster enthalten die folgenden Informationen:

- **SourceId**: Eine Zeichenfolge, die den Berichterstatter des Integritätsereignisses eindeutig identifiziert.

- **Entitätsbezeichner**: Identifiziert die Entität, für die der Bericht erstellt wird. Er unterscheidet sich abhängig vom [Entitätstyp](service-fabric-health-introduction.md#health-entities-and-hierarchy):

  - Cluster: Keine

  - Knoten: Knotenname (Zeichenfolge)

  - Anwendung: Anwendungsname (URI). Stellt den Namen der im Cluster bereitgestellten Anwendungsinstanz dar.

  - Dienst: Dienstname (URI). Stellt den Namen der im Cluster bereitgestellten Dienstinstanz dar.

  - Partition: Partitions-ID (GUID). Stellt den eindeutigen Bezeichner der Partition dar.

  - Replikat: Die ID des zustandsbehafteten Dienstreplikats oder die ID der zustandslosen Dienstinstanz (INT64).

  - DeployedApplication: Anwendungsname (URI) und Knotenname (Zeichenfolge).

  - DeployedServicePackage: Anwendungsname (URI), Knotenname (Zeichenfolge) und Dienstmanifestname (Zeichenfolge).

- **Property**: Ein *Zeichenfolge* (keine feste Enumeration), mit der Berichtersteller das Integritätsereignis für eine bestimmte Eigenschaft der Entität kategorisieren. Beispiel: Berichterstatter A kann einen Integritätsbericht für die Eigenschaft „storage“ auf Node01 erstellen, und Berichterstatter B kann einen Integritätsbericht für die Eigenschaft „connectivity“ auf Node01 erstellen. Im Integritätsspeicher werden diese Berichte als separate Integritätsereignisse für die Entität „Node01“ behandelt.

- **Beschreibung**: Eine Zeichenfolge, die es einem Berichterstatter ermöglicht, detaillierte Informationen zum Integritätsereignis bereitzustellen. Die Verwendung von **SourceId**, **Property** und **HealthState** reicht in der Regel aus, um den Bericht vollständig zu beschreiben. Mit der Beschreibung werden dem Bericht für Menschen lesbare Informationen hinzugefügt. Dies erleichtert Administratoren und Benutzern das Verständnis.

- **HealthState**: Eine [Enumeration](service-fabric-health-introduction.md#health-states), die den Integritätszustand des Berichts beschreibt. Zulässige Werte sind "Ok", "Warning" und "Error".

- **TimeToLive**: Ein Zeitraum, der angibt, wie lange der Integritätsbericht gültig ist. In Kombination mit **RemoveWhenExpired** weist dieser Wert den Integritätsspeicher an, wie abgelaufene Ereignisse ausgewertet werden sollen. Der Zeitraum ist standardmäßig unendlich, und der Bericht ist immer gültig.

- **RemoveWhenExpired**: Ein boolescher Wert. Wenn hierfür „true“ festgelegt ist, wird der abgelaufene Integritätsbericht automatisch aus dem Integritätsspeicher entfernt, und der Bericht wirkt sich nicht auf die Integritätsevaluierung der Entität aus. Dies wird verwendet, wenn der Bericht nur für einen angegebenen Zeitraum gilt und der Berichterstatter den Bericht nicht explizit bereinigen muss. Das Verfahren wird auch zum Löschen von Berichten aus dem Integritätsspeicher verwendet (z. B. wird ein Watchdog geändert und beendet das Senden von Berichten für die vorherige Quelle und Eigenschaft). Hierbei kann ein Bericht mit einer kurzen TimeToLive zusammen mit RemoveWhenExpired gesendet werden, um alle vorherigen Zustände aus dem Integritätsspeicher zu löschen. Wenn der Wert auf „false“ festgelegt ist, wird der abgelaufene Bericht bei der Integritätsevaluierung als Fehler („Error“) behandelt. Mit dem Wert „false“ wird für den Integritätsspeicher angegeben, dass die Quelle regelmäßig Berichte zu dieser Eigenschaft senden soll. Wenn dies nicht der Fall ist, muss ein Fehler für den Watchdog vorliegen. Die Integrität des Watchdogs wird berücksichtigt, indem das Ereignis als Fehler behandelt wird.

- **SequenceNumber**: Eine positive ganze Zahl, mit der die Reihenfolge der Berichte dargestellt wird und die daher stetig erhöht wird. Der Wert wird vom Integritätsspeicher verwendet, um überfällige Berichte zu ermitteln, die wegen Netzwerkverzögerungen oder anderen Problemen verspätet empfangen werden. Ein Bericht wird abgelehnt, wenn die Sequenznummer kleiner oder gleich der zuletzt angewendeten Nummer für dieselbe Entität, Quelle und Eigenschaft ist. Wenn sie nicht angegeben wird, wird die Sequenznummer automatisch generiert. Die Sequenznummer muss nur angegeben werden, wenn Berichte zu Statusübergängen erstellt werden. In diesem Fall muss für die Quelle gespeichert werden, welche Berichte gesendet werden, und die Informationen müssen für die Wiederherstellung bei einem Failover vorgehalten werden.

Die vier Informationselemente – SourceId, Entitätsbezeichner, Property und HealthState – sind für jeden Integritätsbericht erforderlich. Die SourceId-Zeichenfolge darf nicht mit dem Präfix **System.** beginnen, da dieses Präfix Systemberichten vorbehalten ist. Für eine Entität wird für dieselbe Quelle und Eigenschaft nur ein Bericht verwendet. Wenn für dieselbe Quelle und Eigenschaft mehrere Berichte generiert werden, überschreiben sie sich gegenseitig – entweder auf der Integritätsclientseite (bei einem Batch) oder auf der Seite des Integritätsspeichers. Das Überschreiben erfolgt anhand von Sequenznummern. Neuere Berichte (mit höheren Sequenznummern) überschreiben ältere Berichte.

### Integritätsereignisse
Intern hält der Integritätsspeicher [Integritätsereignisse](https://msdn.microsoft.com/library/azure/system.fabric.health.healthevent.aspx) vor, die alle Informationen aus den Berichten und zusätzliche Metadaten enthalten. Darin ist auch der Zeitpunkt enthalten, zu dem der Bericht an den Integritätsclient übergeben wurde, sowie der Zeitpunkt der Änderung auf der Serverseite. Die Integritätsereignisse werden von [Integritätsabfragen](service-fabric-view-entities-aggregated-health.md#health-queries) zurückgegeben.

Die hinzugefügte Metadaten enthalten Folgendes:

- **SourceUtcTimestamp**: Gibt den Zeitpunkt an, an dem der Bericht an den Integritätsclient übergeben wurde (Coordinated Universal Time).

- **LastModifiedUtcTimestamp**: Gibt die Uhrzeit der letzten Änderung des Berichts auf der Serverseite (Coordinated Universal Time) an.

- **IsExpired**: Ein Flag, mit dem angegeben wird, ob der Bericht zum Zeitpunkt der Ausführung der Abfrage durch den Integritätsspeicher bereits abgelaufen war. Ein Ereignis kann nur ablaufen, wenn RemoveWhenExpired auf „false“ festgelegt ist. Andernfalls wird das Ereignis von der Abfrage nicht zurückgegeben und aus dem Speicher entfernt.

- **LastOkTransitionAt**, **LastWarningTransitionAt**, **LastErrorTransitionAt**: Zeitpunkt des letzten Übergangs des Zustands „OK“/„Warning“/„Error“. Diese Felder geben den Verlauf der Integritätszustandsübergänge für das Ereignis an.

Die Felder für die Zustandsübergänge können für erweiterte Warnungen verwendet werden und geben Verlaufsinformationen für das Integritätsereignis an. Sie ermöglichen beispielsweise folgende Szenarien:

- Warnung ausgeben, wenn der Zustand einer Eigenschaft länger als X Minuten den Zustand „Warning/Error“ aufweist: Dadurch werden Warnungen zu vorübergehenden Bedingungen vermieden. Eine Warnung, die ausgegeben wird, wenn der Integritätsstatus „Warning“ länger als 5 Minuten besteht, wird beispielsweise wie folgt angegeben: (HealthState == Warning and Now – LastWarningTransitionTime > 5 minutes).

- Warnung nur zu Bedingungen ausgeben, die sich in den letzten X Minuten geändert haben: Wenn ein Bericht schon vor dem angegebenen Zeitpunkt den Zustand „Error“ erreicht hat, kann er ignoriert werden, da dies bereits signalisiert wurde.

- Beim Umschalten des Zustands einer Eigenschaft zwischen „Warning“ und „Error" bestimmen, wie lange die Eigenschaft fehlerhaft (d. h. nicht „OK“) war: Eine Warnung, die ausgegeben wird, wenn die Eigenschaft länger als fünf Minuten fehlerhaft war, wird beispielsweise wie folgt angegeben: (HealthState != Ok and Now - LastOkTransitionTime > 5 minutes).

## Beispiel: Melden und Evaluieren der Anwendungsintegrität
Im folgenden Beispiel wird über PowerShell ein Integritätsbericht zur Anwendung **fabric:/WordCount** aus der Quelle **MyWatchdog** gesendet. Der Integritätsbericht enthält Informationen zur Integritätseigenschaft „Availability“, die den Integritätszustand „Error“ und den TimeToLive-Wert „Infinite“ aufweist. Anschließend wird die Anwendungsintegrität abgefragt. Die Abfrage gibt den aggregierten Integritätszustand „Error“ und die gemeldeten Integritätsereignisse in der Liste mit den Integritätsereignissen zurück.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            : 
                                  Error event: SourceId='MyWatchdog', Property='Availability'.
                                  
ServiceHealthStates             : 
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error
                                  
                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok
                                  
DeployedApplicationHealthStates : 
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok
                                  
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok
                                  
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM
                                  
                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 131032204762818013
                                  SentAt                : 3/23/2016 3:27:56 PM
                                  ReceivedAt            : 3/23/2016 3:27:56 PM
                                  TTL                   : Infinite
                                  Description           : 
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Ok->Error = 3/23/2016 3:27:56 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## Verwendung des Integritätsmodells
Das Integritätsmodell ermöglicht die Skalierung der Clouddienste und der zugrundeliegenden Service Fabric-Plattform, da die Überwachung und Ermittlung der Integrität auf verschiedene Überwachungselemente im Cluster verteilt werden. Andere Systeme verfügen über einen einzelnen zentralen Dienst auf Clusterebene, der alle *potenziell* nützlichen Informationen analysiert, die von den Diensten ausgegeben werden. Dieser Ansatz beeinträchtigt die Skalierbarkeit. Außerdem können keine spezifischen Informationen gesammelt werden, um die Hauptursache von Problemen oder potenziellen Problemen so weit wie möglich einzugrenzen.

Das Integritätsmodell wird hauptsächlich für die Überwachung und Diagnose, Evaluierung der Cluster- und Anwendungsintegrität und Überwachung von Upgrades verwendet. Andere Dienste nutzen die Integritätsdaten, um automatische Reparaturen durchzuführen, Verläufe der Clusterintegrität zu erstellen und Warnungen bei bestimmten Bedingungen auszugeben.

## Nächste Schritte
[Anzeigen von Service Fabric-Integritätsberichten](service-fabric-view-entities-aggregated-health.md)

[Verwenden von Systemintegritätsberichten für die Problembehandlung](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Hinzufügen von benutzerdefinierten Service Fabric-Integritätsberichten](service-fabric-report-health.md)

[Lokales Überwachen und Diagnostizieren von Diensten](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-Anwendungsupgrade](service-fabric-application-upgrade.md)

<!---HONumber=AcomDC_0323_2016-->