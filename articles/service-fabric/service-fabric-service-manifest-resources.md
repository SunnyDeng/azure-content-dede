<properties
   pageTitle="Ressourcen des Service Fabric-Dienstmanifests"
   description="Beschreiben von Ressourcen in einem Dienstmanifest"
   services="service-fabric"
   documentationCenter=".net"
   authors="sumukhs"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="sumukhs"/>

# Dienstmanifestressourcen

## Übersicht

Mit dem Dienstmanifest können Ressourcen vom Dienst verwendet werden, um deklariert oder geändert zu werden, ohne dass der kompilierte Code geändert wird. Service Fabric unterstützt die Konfiguration von Endpunktressourcen für den Dienst. Der Zugriff auf die im Dienstmanifest angegebenen Ressourcen kann über das SecurityGroup-Element im Anwendungsmanifest gesteuert werden. Die Deklaration von Ressourcen ermöglicht es, dass diese Ressourcen zur Bereitstellungszeit geändert werden, sodass der Dienst keinen neuen Konfigurationsmechanismus einführen muss.

## Endpunkte

Wenn eine Endpunktressource im Dienstmanifest definiert wird, weist Service Fabric Ports aus dem Bereich der reservierten Anwendungsports zu. Außerdem können Dienste auch einen bestimmten Port einer Ressource anfordern. Dienstreplikate, die auf unterschiedlichen Clusterknoten ausgeführt werden, können unterschiedlichen Portnummern zugewiesen werden, während für Replikate eines Diensts auf demselben Knoten derselbe Port verwendet wird. Diese Ports können von den Dienstreplikaten für unterschiedliche Zwecke genutzt werden, z. B. die Replikation, das Überwachen auf Clientanforderungen usw.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint" Protocol="http"/>
    <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
    <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
  </Endpoints>
</Resources>
```

Weitere Informationen zum Verweisen auf Endpunkte aus der Datei mit den Konfigurationspaketeinstellungen (settings.xml) finden Sie unter [Konfigurieren von zustandsbehafteten zuverlässigen Diensten](../Service-Fabric/service-fabric-reliable-services-configuration.md).

## Beispiel
Mit dem folgenden Dienstmanifest werden eine TCP-Endpunktressource und zwei HTTP-Endpunktressourcen im &lt;Resources&gt;-Element definiert.

HTTP-Endpunkte werden von Service Fabric automatisch mit einer Zugriffssteuerungsliste (ACL) versehen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="SP1" Version="V1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Description>Test Service</Description>
  <ServiceTypes>
    <StatefulServiceType ServiceTypeName="PersistType" HasPersistedState="true" />
  </ServiceTypes>
  <CodePackage Name="CP1" Version="V1">
    <EntryPoint>
      <ExeHost>
        <Program>CB\Code.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="CP1.Config0" Version="V1" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" Protocol="http"/>
      <Endpoint Name="ServiceInputEndpoint" Protocol="http" Port="80"/>
      <Endpoint Name="ReplicatorEndpoint" Protocol="tcp"/>
    </Endpoints>
  </Resources>
</ServiceManifest>
```
 

<!---HONumber=July15_HO4-->