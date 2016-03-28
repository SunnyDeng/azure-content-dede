<properties
   pageTitle="Der Clusterressourcen-Manager von Service Fabric – Platzierungsrichtlinien | Microsoft Azure"
   description="Übersicht über zusätzlichen Platzierungsrichtlinien und -regeln für Service Fabric-Dienste"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Platzierungsrichtlinien für Service Fabric-Dienste
Es gibt viele weitere Regeln, die ggf. beachtet werden müssen, wenn sich Ihr Service Fabric-Cluster über größere Entfernungen erstreckt, z. B. über mehrere Rechenzentren oder Azure-Regionen, oder sich Ihre Umgebung über mehrere geopolitische Kontrollbereiche erstreckt (oder ein anderer Fall, bei dem rechtliche oder strategische Grenzen beachtet werden müssen). Die meisten dieser Fälle können mithilfe von Knoteneigenschaften und Platzierungseinschränkungen (die wir zuvor bereits erwähnt haben) konfiguriert werden, aber einige sind komplizierter. In jedem Fall stellen wir diese Verfahren bereit. Ebenso wie Platzierungseinschränkungen können Platzierungsrichtlinien dienstbezogen konfiguriert werden.

## Angeben ungültiger Domänen
Die Platzierungsrichtlinie „InvalidDomain“ ermöglicht die Angabe, dass eine bestimmte Fehlerdomäne für diese Workload ungültig ist. Diese Richtlinie stellt sicher, dass ein bestimmter Dienst nie in einer bestimmten Region ausgeführt wird, z. B. aus geopolitischen Gründen oder zur Einhaltung von Unternehmensrichtlinien. Es können mehrere ungültige Domänen angegeben werden.

![Ungültige Domäne – Beispiel][Image1]

Code:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```
## Angeben erforderlicher Domänen
Die Platzierungsrichtlinie „RequiredDomain“ erfordert, dass alle Replikate in der angegebenen Domäne vorhanden sind. Es können mehrere erforderliche Domänen angegeben werden.

![Erforderliche Domäne – Beispiel][Image2]

Code:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## Angeben einer bevorzugten Domäne für die primären Replikate
Die bevorzugte Domäne für primäre Replikate ist ein interessantes Steuerelement, da es die Auswahl der Fehlerdomäne ermöglicht, in die das primäre Replikat nach Möglichkeit platziert werden soll. Wenn alles fehlerfrei ist, befindet sich das primäre Replikat in dieser Domäne. Sollten die Domäne oder das primäre Replikat ausfallen oder aus einem anderen Grund heruntergefahren werden, wird das primäre Replikat zu einem anderen Ort migriert. Sofern möglich, wird es wieder zurück zur bevorzugten Domäne bewegt. Diese Einstellung ist nur für zustandsbehaftete Dienste sinnvoll. Diese Richtlinie ist besonders nützlich bei geografisch übergreifenden Clustern, in denen Sie zu Redundanzzwecken andere Speicherorte verwenden, aber die primären Replikate vorzugsweise an einem bestimmten Standort platzieren möchten, um bei Vorgängen an primäre Replikate niedrigere Latenzzeiten bereitzustellen. (Schreibvorgänge und standardmäßig auch alle Lesevorgänge werden vom primären Replikat verarbeitet.)

![Bevorzugte primäre Domänen und Failover][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

## Anfordern einer Verteilung von Replikaten auf alle Domänen und Unterbinden einer Überlastung
Eine andere Richtlinie, die Sie angeben können, erfordert das grundsätzliche Verteilen von Replikaten auf die verfügbaren Domänen. Beachten Sie, dass es beim obigen Beispiel möglich ist, dass sich mehrere Replikate (tatsächlich ein Quorum, huch) vorübergehend im selben Rechenzentrum befinden. In diesem Fall gibt es wahrscheinlich keine Probleme, da sich das primäre Replikat nicht im selben Rechenzentrum wie eine Mehrheit der Replikate befindet. Doch es zeigt sich, dass wir vorübergehend eine Konfiguration haben könnten, die wir vermeiden möchten. Dies hängt von anderen Bedingungen im Cluster zu diesem Zeitpunkt ab, doch Sie wollen eigentlich diesen Fall vollständig vermeiden, indem sichergestellt wird, dass sich Ihre Replikate stets in getrennten Domänen befinden. Beachten Sie, dass Sie bei Auswahl dieser Option eher Ausfallzeiten herbeiführen, statt eine kontinuierliche Ausführung des Diensts auf den verbleibenden Knoten zu gestatten. Mit der Richtlinie „RequireDomainDistribution“ wird dieses Verhalten konfiguriert. Doch beachten Sie, dass wir dadurch möglicherweise überhaupt keine Replikate platzieren, da dies zu einer Überlastung führen könnte. Dies bedeutet, dass Sie vorübergehend mit weniger als der Zielanzahl von Replikaten arbeiten, bis Knoten in einer geeigneten Domäne wieder online sind. Wir kennen Kunden, die jederzeit über die Zielanzahl von Replikaten (Kopien des Zustands) verfügen wollen, während andere die Verfügbarkeitsaspekte nicht riskieren möchten (wenn wir die Replikate in einer Fehlerdomäne unterbringen und diese Domäne ausfällt, gehen alle diese Replikate gleichzeitig verloren). Bei einer kleinen Anzahl von Replikaten kann dies zum Quorumverlust führen. In dem unwahrscheinlichen Fall, dass die Domäne nie wieder verfügbar würde, könnte dies auch zu Datenverlusten führen. Da die meisten Benutzer mit mehr als 3 Replikaten arbeiten, ist es Standard, keine Domänenverteilung anzufordern und den Lastenausgleich und das Failover normal arbeiten zu lassen, auch wenn dies bedeutet, dass in einer Domäne vorübergehend mehrere Replikate untergebracht sind.

Code:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Ist es auch möglich, diese Konfigurationen für Dienste in einem Cluster zu verwenden, der sich nicht über größere Entfernungen erstreckt? Das ist möglich! Allerdings gibt es keinen guten Grund. Insbesondere die Konfigurationen „Erforderlich“, „Ungültig“ und „Bevorzugte Domäne“ sollten vermieden werden, solange Sie keinen regionsübergreifenden Cluster betreiben.

## Nächste Schritte
- Weitere Informationen zu den anderen Optionen, die für die Konfiguration von Diensten zur Verfügung stehen, finden Sie im Thema zu den anderen verfügbaren Clusterressourcen-Manager-Konfigurationen, [Konfigurieren von Diensten](service-fabric-cluster-resource-manager-configure-services.md).

[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0316_2016-->