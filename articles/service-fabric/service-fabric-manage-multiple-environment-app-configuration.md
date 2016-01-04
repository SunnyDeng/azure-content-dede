<properties
   pageTitle="Verwalten mehrerer Umgebungen in Service Fabric | Microsoft Azure"
   description="Service Fabric-Anwendung können auf Clustern ausgeführt werden, die eine Größe von einem Computer bis zu Tausenden von Computern aufweisen. In einigen Fällen möchten Sie die Anwendung für diese verschiedenen Umgebungen unterschiedlich konfigurieren. In diesem Artikel wird beschrieben, wie unterschiedliche Anwendungsparameter pro Umgebung definiert werden."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/25/2015"
   ms.author="seanmck"/>

# Verwalten von Anwendungsparametern für mehrere Umgebungen

Service Fabric-Cluster können aus einem Computer bestehen, aber auch aus Tausenden von Computern gebildet werden. Während Anwendungsbinärdateien ohne Änderungen in diesem breiten Spektrum von Umgebungen ausgeführt werden können, möchten Sie häufig die Anwendung abhängig von der Anzahl an Computern, auf denen Sie sie bereitstellen, anders konfigurieren.

Betrachten Sie als ein einfaches Beispiel `InstanceCount` für einen zustandslosen Dienst. Bei einer Verwendung in Azure sollten Sie in der Regel diesen Parameter auf den speziellen Wert -1 festlegen. So wird sichergestellt, dass Ihr Dienst auf jedem Knoten im Cluster ausgeführt wird. Diese Konfiguration eignet sich jedoch nicht für einen Cluster mit einem Computer, da nicht mehrere Prozesse auf einem einzigen Computer denselben Endpunkt überwachen können. In diesem Fall legen Sie in der Regel `InstanceCount` auf 1 fest.

## Angeben von umgebungsspezifischen Parametern

Die Lösung ist ein Satz von parametrisierten Standarddiensten und Anwendungsparameterdateien, die die Parameterwerte für eine bestimmte Umgebung angeben.

### Standarddienste

Service Fabric-Programme bestehen aus einer Sammlung von Dienstinstanzen. Es ist zwar möglich, eine leere Anwendung zu erstellen und dann alle Dienstinstanzen dynamisch zu erstellen, aber die meisten Anwendungen verfügen über eine Reihe von Basisdiensten, die immer erstellt werden sollten, wenn die Anwendung instanziiert wird. Diese werden als „Standarddienste“ bezeichnet und im Anwendungsmanifest angegeben. Platzhalter für die Konfiguration pro Umgebung sind dabei in zusätzlichen Klammern enthalten:

    <DefaultServices>
        <Service Name="Stateful1">
            <StatefulService
                ServiceTypeName="Stateful1Type" TargetReplicaSetSize="[Stateful1_TargetReplicaSetSize]" MinReplicaSetSize="[Stateful1_MinReplicaSetSize]">

                <UniformInt64Partition
                    PartitionCount="[Stateful1_PartitionCount]" LowKey="-9223372036854775808"
                    HighKey="9223372036854775807"
                />
        </StatefulService>
    </Service>
  </DefaultServices>

Jeder benannte Parameter muss im Parameters-Element des Anwendungsmanifests definiert werden:

    <Parameters>
        <Parameter Name="Stateful1_MinReplicaSetSize" DefaultValue="2" />
        <Parameter Name="Stateful1_PartitionCount" DefaultValue="1" />
        <Parameter Name="Stateful1_TargetReplicaSetSize" DefaultValue="3" />
    </Parameters>

Die DefaultValue-Attribute geben den zu verwendenden Wert an, wenn ein spezifischerer Parameter für eine bestimmte Umgebung fehlt.

>[AZURE.NOTE]Nicht alle Dienstinstanzparameter eignen sich für die Konfiguration pro Umgebung. Im Beispiel oben werden die Werte „LowKey“ und „HighKey“ für das Partitionierungsschema des Diensts explizit für alle Instanzen des Diensts definiert, da der Partitionsbereich eine Funktion der Datendomäne und nicht der Umgebung ist.


### Dienstkonfigurationseinstellungen pro Umgebung

Durch das [Service Fabric-Anwendungsmodell](service-fabric-application-model.md) können Dienste Konfigurationspakete aufweisen, die benutzerdefinierte Schlüssel-Wert-Paare enthalten, die zur Laufzeit gelesen werden können. Die Werte dieser Einstellungen können auch nach Umgebung unterschieden werden, indem `ConfigOverride` im Anwendungsmanifest angegeben wird.

Angenommen, es gibt die folgende Einstellung in der Datei „Config\\Settings.xml“ für den `Stateful1`-Dienst:


    <Section Name="MyConfigSection">
      <Parameter Name="MaxQueueSize" Value="25" />
    </Section>

Um diesen Wert für ein bestimmtes Paar aus Anwendung und Umgebung zu überschreiben, erstellen Sie `ConfigOverride` beim Importieren des Dienstmanifests in das Anwendungsmanifest.

    <ConfigOverrides>
     <ConfigOverride Name="Config">
        <Settings>
           <Section Name="MyConfigSection">
              <Parameter Name="MaxQueueSize" Value="[Stateful1_MaxQueueSize]" />
           </Section>
        </Settings>
     </ConfigOverride>
  </ConfigOverrides>

Dieser Parameter kann dann für eine Umgebung konfiguriert werden, wie oben gezeigt, indem Sie sie im Parameters-Abschnitt des Anwendungsmanifests deklarieren und umgebungsspezifische Werte in den Anwendungsparameterdateien angeben.

>[AZURE.NOTE]Im Fall von Dienstkonfigurationseinstellungen gibt es drei Stellen, um den Wert eines Schlüssels festzulegen: das Dienstkonfigurationspaket, das Anwendungsmanifest und die Anwendungsparameterdatei. Service Fabric wählt immer zuerst die Anwendungsparameterdatei aus (falls angegeben), dann das Anwendungsmanifest und schließlich das Konfigurationspaket.


### Anwendungsparameterdateien

Das Service Fabric-Anwendungsprojekt kann eine oder mehrere Anwendungsparameterdateien enthalten, die jeweils die spezifischen Werte für die im Anwendungsmanifest definierten Parameter definieren:

    <!-- ApplicationParameters\Local.xml -->

    <Application Name="fabric:/Application1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <Parameters>
            <Parameter Name ="Stateful1_MinReplicaSetSize" Value="2" />
            <Parameter Name="Stateful1_PartitionCount" Value="1" />
            <Parameter Name="Stateful1_TargetReplicaSetSize" Value="3" />
        </Parameters>
    </Application>

Standardmäßig enthält eine neue Anwendung zwei Parameterdateien mit den Namen „Local.xml“ und „Cloud.xml“:

![Anwendungsparameterdateien im Projektmappen-Explorer][app-parameters-solution-explorer]

Um eine neue Parameterdatei zu erstellen, kopieren Sie einfach eine vorhandene Datei, und geben Sie ihr einen neuen Namen.

## Identifizieren umgebungsspezifischer Parameter während der Bereitstellung

Während der Bereitstellung müssen Sie die geeignete Anwendungsparameterdatei für Ihre Anwendung auswählen. Sie können dazu das Dialogfeld „Veröffentlichen“ in Visual Studio oder in PowerShell verwenden.

### Bereitstellen über Visual Studio

Sie können aus der Liste der verfügbaren Parameterdateien auswählen, wenn Sie die Anwendung in Visual Studio veröffentlichen.

![Auswählen einer Parameterdatei im Dialogfeld „Veröffentlichen“][publishdialog]

### Bereitstellen über PowerShell

Das PowerShell-Skript `DeployCreate-FabricApplication.ps1` akzeptiert eine Parameterdatei als Parameter.

    ./DeployCreate-FabricApplication -ApplicationPackagePath <app_package_path> -ApplicationDefinitionFilePath <app_instance_definition_path>

## Nächste Schritte

Weitere Informationen zu einigen der grundlegenden Konzepte, die in diesem Thema behandelt werden, finden Sie unter [Technischer Überblick über Service Fabric](service-fabric-technical-overview.md). Informationen zu anderen App-Verwaltungsfunktionen in Visual Studio finden Sie unter [Verwalten von Service Fabric-Anwendungen in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]: ./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png

<!---HONumber=AcomDC_1203_2015-->