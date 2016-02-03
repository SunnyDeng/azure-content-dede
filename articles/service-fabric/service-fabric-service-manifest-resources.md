<properties
   pageTitle="Angeben von Service Fabric-Dienstendpunkten | Microsoft Azure"
   description="Gewusst wie: Beschreiben von Endpunktressourcen in einem Dienstmanifest, einschließlich der Einrichtung von HTTPS-Endpunkten"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/26/2015"
   ms.author="sumukhs"/>

# Angeben von Ressourcen in einem Dienstmanifest

## Übersicht

Mit dem Dienstmanifest können vom Dienst verwendete Ressourcen deklariert oder geändert werden, ohne dass der kompilierte Code geändert werden muss. Azure Service Fabric unterstützt die Konfiguration von Endpunktressourcen für den Dienst. Der Zugriff auf die im Dienstmanifest angegebenen Ressourcen kann über das SecurityGroup-Element im Anwendungsmanifest gesteuert werden. Die Deklaration von Ressourcen ermöglicht es, dass diese Ressourcen zur Bereitstellungszeit geändert werden, sodass der Dienst keinen neuen Konfigurationsmechanismus einführen muss.

## Endpunkte

Wenn eine Endpunktressource im Dienstmanifest definiert wird, weist Service Fabric Ports aus dem Bereich der reservierten Anwendungsports zu, sofern kein expliziter Port angegeben ist (sehen Sie sich beispielsweise den Endpunkt *ServiceEndpoint1* weiter unten an). Außerdem können Dienste auch einen bestimmten Port einer Ressource anfordern. Dienstreplikate, die auf unterschiedlichen Clusterknoten ausgeführt werden, können unterschiedlichen Portnummern zugewiesen werden, während für Replikate eines Diensts auf demselben Knoten derselbe Port verwendet wird. Diese Ports können von den Dienstreplikaten für unterschiedliche Zwecke genutzt werden, z. B. die Replikation, das Lauschen auf Clientanforderungen usw.

```xml
<Resources>
  <Endpoints>
    <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
    <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
    <Endpoint Name="ServiceEndpoint3" Protocol="https"/>
  </Endpoints>
</Resources>
```

Weitere Informationen zum Verweisen auf Endpunkte aus der Datei mit den Konfigurationspaketeinstellungen (settings.xml) finden Sie unter [Konfigurieren zustandsbehafteter Reliable Services](../Service-Fabric/service-fabric-reliable-services-configuration.md).

## Beispiel: Angeben eines HTTP-Endpunkts für Ihren Dienst

Mit dem folgenden Dienstmanifest werden eine TCP-Endpunktressource und zwei HTTP-Endpunktressourcen im &lt;Resources&gt;-Element definiert.

HTTP-Endpunkte werden von Service Fabric automatisch mit einer Zugriffssteuerungsliste (ACL) versehen.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest Name="Stateful1Pkg"
                 Version="1.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <ServiceTypes>
    <!-- This is the name of your ServiceType.
         This name must match the string used in the RegisterServiceType call in Program.cs. -->
    <StatefulServiceType ServiceTypeName="Stateful1Type" HasPersistedState="true" />
  </ServiceTypes>

  <!-- Code package is your service executable. -->
  <CodePackage Name="Code" Version="1.0.0">
    <EntryPoint>
      <ExeHost>
        <Program>Stateful1.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>

  <!-- Config package is the contents of the Config directoy under PackageRoot that contains an
       independently updateable and versioned set of custom configuration settings for your service. -->
  <ConfigPackage Name="Config" Version="1.0.0" />

  <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port number on which to
           listen. Note that if your service is partitioned, this port is shared with
           replicas of different partitions that are placed in your code. -->
      <Endpoint Name="ServiceEndpoint1" Protocol="http"/>
      <Endpoint Name="ServiceEndpoint2" Protocol="http" Port="80"/>
      <Endpoint Name="ServiceEndpoint3" Protocol="https"/>

      <!-- This endpoint is used by the replicator for replicating the state of your service.
           This endpoint is configured through the ReplicatorSettings config section in the Settings.xml
           file under the ConfigPackage. -->
      <Endpoint Name="ReplicatorEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>
```

## Beispiel: Angeben eines HTTPS-Endpunkts für Ihren Dienst

Das HTTPS-Protokoll ermöglicht die Serverauthentifizierung und wird auch zum Verschlüsseln der Kommunikation zwischen Client und Server verwendet. Um dieses Protokoll für Ihren Service Fabric-Dienst zu aktivieren, geben Sie es beim Festlegen des Diensts im Abschnitt *Ressourcen -> Endpunkte -> Endpunkt* des Dienstmanifests an (wie weiter oben für den Endpunkt *ServiceEndpoint3* gezeigt).

>[AZURE.NOTE]Das Protokoll eines Diensts kann nicht während der Anwendungsaktualisierung geändert werden, da diese Änderung eine Unterbrechung verursacht.


Hier sehen Sie ein Beispiel für ein Anwendungsmanifest, das Sie für HTTPS festlegen müssen. (Sie müssen den Fingerabdruck für Ihr Zertifikat bereitstellen.) Bei EndpointRef handelt es sich um einen Verweis auf die Endpunktressource im Dienstmanifest, für das Sie das HTTPS-Protokoll festgelegt haben. Sie können mehrere Endpunktzertifikate hinzufügen.

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest ApplicationTypeName="Application1Type"
                     ApplicationTypeVersion="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric"
                     xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Parameters>
    <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
    <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
    <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
  </Parameters>
  <!-- Import the ServiceManifest from the ServicePackage. The ServiceManifestName and ServiceManifestVersion
       should match the Name and Version attributes of the ServiceManifest element defined in the
       ServiceManifest.xml file. -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides />
    <Policies>
      <EndpointBindingPolicy CertificateRef="TestCert1" EndpointRef="ServiceEndpoint3"/>
    </Policies>
  </ServiceManifestImport>
  <DefaultServices>
    <!-- The section below creates instances of service types when an instance of this
         application type is created. You can also create one or more instances of service type by using the
         Service Fabric PowerShell module.

         The attribute ServiceTypeName below must match the name defined in the imported ServiceManifest.xml file. -->
    <Service Name="Stateful1">
      <StatefulService ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
      </StatefulService>
    </Service>
  </DefaultServices>
  <Certificates>
    <EndpointCertificate Name="TestCert1" X509FindValue="FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF FF F0" X509StoreName="MY" />  
  </Certificates>
</ApplicationManifest>
```

<!---HONumber=AcomDC_0121_2016-->