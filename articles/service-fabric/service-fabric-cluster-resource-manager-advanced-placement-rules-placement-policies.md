<properties
   pageTitle="Der Clusterressourcen-Manager von Service Fabric – Platzierungsrichtlinien"
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
   ms.date="03/03/2016"
   ms.author="masnider"/>

# Platzierungsrichtlinien
Es gibt viele weitere Regeln, die ggf. beachtet werden müssen, wenn sich Ihr Service Fabric-Cluster über größere Entfernungen erstreckt, z. B. über mehrere Rechenzentren oder Azure-Regionen, oder sich Ihre Umgebung über mehrere geopolitische Kontrollbereiche erstreckt (oder ein anderer Fall, bei dem rechtliche oder strategische Grenzen beachtet werden müssen). Die meisten dieser Fälle können mithilfe von Knoteneigenschaften und Platzierungseinschränkungen (die wir zuvor bereits erwähnt haben) konfiguriert werden, aber einige sind komplizierter. Ebenso wie Platzierungseinschränkungen können Platzierungsrichtlinien dienstbezogen konfiguriert werden.

-	InvalidDomain – Ermöglicht die Angabe, dass eine bestimmte Fehlerdomäne für diesen Workload ungültig ist. Geeignet zum Sicherstellen, dass ein bestimmter Dienst nie in einer bestimmten Region ausgeführt wird, z. B. aus geopolitischen Gründen oder zur Einhaltung von Unternehmensrichtlinien.

![Ungültige Domäne – Beispiel][Image1]

Code:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DCEast/"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/dc9/”)
```

-	RequiredDomain – Erfordert, dass alle Replikate in der angegebenen Domäne vorhanden sind.

![Erforderliche Domäne – Beispiel][Image2]

Code:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

-	PreferPrimaryDomain – Dies ist ein interessantes Steuerelement, da es die Auswahl der Fehlerdomäne ermöglicht, in der das primäre Replikat nach Möglichkeit vorhanden sein sollte. Wenn alles fehlerfrei ist, befindet sich das primäre Replikat in dieser Domäne. Sollten die Domäne oder das primäre Replikat ausfallen oder aus einem anderen Grund heruntergefahren werden, wird das primäre Replikat zu einem anderen Ort migriert. Sofern möglich, wird es wieder zurück zur bevorzugten Domäne bewegt. Diese Einstellung ist nur für zustandsbehaftete Dienste sinnvoll.

![Bevorzugte primäre Domänen und Failover][Image3]

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUs/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUs")
```

-	RequireDomainDistribution – Das Anfordern der Domänenverteilung ist eine weitere Option, mit deren Hilfe bestimmte Situationen vermieden werden können. Beachten Sie, dass es beim obigen Beispiel möglich ist, dass sich mehrere Replikate (tatsächlich ein Quorum, huch) vorübergehend im selben Rechenzentrum befinden. In diesem Fall gibt es wahrscheinlich keine Probleme, da sich das primäre Replikat nicht im selben Rechenzentrum wie eine Mehrheit der Replikate befindet. Doch ich schweife ab, denn es zeigt sich, dass wir vorübergehend eine Konfiguration haben könnten, die wir vermeiden möchten. Dies hängt von anderen Bedingungen im Cluster zu diesem Zeitpunkt ab, doch Sie wollen eigentlich diesen Fall vollständig vermeiden, indem sichergestellt wird, dass sich Ihre Replikate stets in getrennten Domänen befinden. Das Flag „RequireDomainDistribution“ ermöglicht dies. Doch beachten Sie, dass dies bedeuten kann, dass wir überhaupt keine Replikate platzieren, da dies zu einer Überfüllung führen könnte. Dies bedeutet, dass Sie vorübergehend mit weniger als der Zielanzahl von Replikaten arbeiten, bis Knoten in einer geeigneten Domäne wieder online sind. Wir kennen Kunden, die jederzeit über die Zielanzahl von Replikaten (Kopien des Zustands) verfügen wollen, während andere die Verfügbarkeitsaspekte nicht riskieren möchten (wenn wir die Replikate in einer Fehlerdomäne unterbringen und diese Domäne ausfällt, gehen alle diese Replikate gleichzeitig verloren). Bei einer kleinen Anzahl von Replikaten kann dies zum Quorumverlust führen. Da die meisten Benutzer mit mehr als 3 Replikaten arbeiten, ist es Standard, keine Domänenverteilung anzufordern und den Lastenausgleich und das Failover normal arbeiten zu lassen, auch wenn dies bedeutet, dass in einer Domäne vorübergehend mehrere Replikate untergebracht sind.

Code:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 2 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Sie können mehrere Domänen des Typs „Ungültig“ oder „Erforderlich“ für einen einzelnen Dienst konfigurieren, doch die beiden anderen Typen (bevorzugte Domänen und Domänenverteilung) können nur einmal pro Dienst konfiguriert werden.

Ist es auch möglich, diese Konfigurationen für Dienste in einem Cluster zu verwenden, der sich nicht über größere Entfernungen erstreckt? Das ist möglich! Allerdings gibt es keinen guten Grund. Insbesondere die Konfigurationen „Erforderlich“, „Ungültig“ und „Bevorzugte Domäne“ sollten vermieden werden, solange Sie keinen regionsübergreifenden Cluster betreiben.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Nächste Schritte
- [Informationen zur Konfiguration von Diensten](service-fabric-cluster-resource-manager-configure-services.md)



[Image1]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]: ./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png

<!---HONumber=AcomDC_0309_2016-->