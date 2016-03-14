<properties
   pageTitle="Anleitung zur Konvertierung von Web- und Workerrollen in zustandslose Service Fabric-Dienste | Microsoft Azure"
   description="In dieser Anleitung werden Cloud Services-Web- und -Workerrollen mit zustandslosen Service Fabric-Diensten verglichen, um die Migration von Cloud Services zu Service Fabric zu unterstützen."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/29/2016"
   ms.author="vturecek"/>
 
# Anleitung zur Konvertierung von Web- und Workerrollen in zustandslose Service Fabric-Dienste

In diesem Artikel wird beschrieben, wie Sie Ihre Cloud Services-Web- und -Workerrollen zu zustandslosen Service Fabric-Diensten migrieren. Dies ist für Anwendungen, deren allgemeine Architektur weitgehend gleich bleibt, der einfachste Migrationspfad von Cloud Services zu Service Fabric.

## Vom Cloud Services-Projekt zum Service Fabric-Anwendungsprojekt
 
 Ein Cloud Services-Projekt und ein Service Fabric-Anwendungsprojekt haben eine ähnliche Struktur, und beide stellen die Bereitstellungseinheit für Ihre Anwendung dar. Sie dienen also jeweils zum Definieren des kompletten Pakets, das zur Ausführung Ihrer Anwendung bereitgestellt wird. Ein Cloud Services-Projekt enthält mindestens eine Web- oder Workerrolle. Analog dazu enthält ein Service Fabric-Anwendungsprojekt mindestens einen Dienst.

Der Unterschied besteht darin, dass die Anwendungsbereitstellung beim Cloud Services-Projekt mit einer VM-Bereitstellung gekoppelt ist und daher VM-Konfigurationseinstellungen enthält. Beim Service Fabric-Anwendungsprojekt wird dagegen nur eine Anwendung definiert, die für eine Gruppe vorhandener VMs in einem Service Fabric-Cluster bereitgestellt wird. Der eigentliche Service Fabric-Cluster wird nur einmal bereitgestellt, und zwar entweder über eine ARM-Vorlage oder über das Azure-Portal. Hierfür können mehrere Service Fabric-Anwendungen bereitgestellt werden.

![Vergleich von Service Fabric- und Cloud Services-Projekten][3]
 
## Von der Workerrolle zum zustandslosen Dienst 

In Bezug auf das Konzept stellt eine Workerrolle eine zustandslose Workload dar. Dies bedeutet, dass jede Instanz der Workload identisch ist und Anforderungen jederzeit an alle Instanzen geleitet werden können. Für die einzelnen Instanzen wird nicht erwartet, dass sie sich der vorherigen Anforderung jeweils bewusst sind. Der Zustand, unter dem die Workload verarbeitet wird, wird von einem externen Statusspeicher verwaltet, z. B. Azure Table Storage oder Azure Document DB. Unter Service Fabric wird diese Art von Workload durch einen zustandslosen Dienst dargestellt. Der einfachste Ansatz zum Migrieren einer Workerrolle zu Service Fabric ist das Konvertieren von Workerrollencode in einen zustandslosen Dienst.

![Von der Workerrolle zum zustandslosen Dienst][4]

## Von der Webrolle zum zustandslosen Dienst

Ähnlich wie bei der Workerrolle stellt auch eine Webrolle eine zustandslose Workload dar, sodass sie vom Konzept her auch einem zustandslosen Service Fabric-Dienst zugeordnet werden kann. Im Gegensatz zu Webrollen weist Service Fabric aber keine IIS-Unterstützung auf. Zum Migrieren einer Webanwendung aus einer Webrolle in einen zustandslosen Dienst ist es zuerst erforderlich, zu einem Web-Framework zu wechseln, das sich selbst hosten kann und nicht von IIS oder System.Web abhängig ist, z. B. ASP.NET Core 1.

****Anwendung ** | **Unterstützt** | **Migrationspfad**
--- | --- | ---
ASP.NET Web Forms | Nein | In ASP.NET Core 1 MVC konvertieren
ASP.NET MVC | Per Migration | Upgrade auf ASP.NET Core 1
ASP.NET-Web-API | Per Migration | Selbst gehosteten Server oder ASP.NET Core 1 verwenden
ASP.NET Core 1 | Ja | N/V

## Einstiegspunkt-API und Lebenszyklus

APIs von Workerrollen und Service Fabric-Diensten verfügen über ähnliche Einstiegspunkte:

**Einstiegspunkt** | **Workerrolle** | **Service Fabric-Dienst**
--- | --- | ---
Verarbeitung | `Run()` | `RunAsync()`
VM starten | `OnStart()` | N/V
VM beenden | `OnStop()` | N/V
Listener für Clientanforderungen öffnen | N/V | <ul><li> `CreateServiceInstanceListener()` für zustandslos</li><li>`CreateServiceReplicaListener()` für zustandsbehaftet</li></ul>

### Workerrolle

```C#

using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
        }

        public override bool OnStart()
        {
        }

        public override void OnStop()
        {
        }
    }
}

```

### Zustandsloser Service Fabric-Dienst

```C#

using System.Collections.Generic;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace Stateless1
{
    public class Stateless1 : StatelessService
    {
        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
        }

        protected override Task RunAsync(CancellationToken cancelServiceInstance)
        {
        }
    }
}

```

Beide verfügen über eine primäre „Run“-Außerkraftsetzung (Override) zum Starten der Verarbeitung. Bei Service Fabric-Diensten werden `Run`, `Start` und `Stop` zu einem einzelnen Einstiegspunkt kombiniert: `RunAsync`. Ihr Dienst sollte gestartet werden, wenn `RunAsync` gestartet wird, und beendet werden, wenn das Signal für das CancellationToken der `RunAsync`-Methode gegeben wird.

Es gibt einige wichtige Unterschiede zwischen dem Lebenszyklus und der Lebensdauer von Workerrollen und Service Fabric-Diensten:

 - **Lebenszyklus:** Der größte Unterschied ist, dass eine Workerrolle eine VM ist und ihr Lebenszyklus daher an die VM gebunden ist. Dies umfasst auch Ereignisse, die mit dem Starten und Beenden der VM verbunden sind. Ein Service Fabric-Dienst hat einen Lebenszyklus, der vom VM-Lebenszyklus getrennt ist. Er enthält also keine Ereignisse, die sich auf das Starten und Beenden der Host-VM oder des Computers beziehen, da diese nicht verwandt sind.

 - **Lebensdauer:** Eine Workerrolleninstanz wird wiederverwendet, wenn die `Run`-Methode vorhanden ist. Die `RunAsync`-Methode in einem Service Fabric-Dienst kann aber bis zum Schluss ausgeführt werden, und die Dienstinstanz bleibt verfügbar.

Service Fabric stellt einen optionalen Kommunikationssetup-Einstiegspunkt für Dienste bereit, die auf Clientanforderungen lauschen. Sowohl der RunAsync- als auch der Kommunikations-Einstiegspunkt sind optionale Außerkraftsetzungen (Overrides) in Service Fabric-Diensten. - Ihr Dienst lauscht unter Umständen nur auf Clientanforderungen oder führt nur eine Verarbeitungsschleife aus (oder beides). - Dies ist der Grund, warum die RunAsync-Methode den Vorgang beenden kann, ohne dass die Dienstinstanz neu gestartet wird. Ggf. lauscht sie weiter auf Clientanforderungen.

## Anwendungs-API und Umgebung

Die API für die Cloud Services-Umgebung liefert Informationen und Funktionen für die aktuelle VM-Instanz und Informationen zu anderen VM-Rolleninstanzen. Service Fabric liefert Informationen zu seiner Laufzeit und einige Informationen zu dem Knoten, auf dem ein Dienst derzeit ausgeführt wird.

**Umgebungsaufgabe** | **Cloud Services** | **Service Fabric**
--- | --- | ---
Konfigurationseinstellungen und Änderungsbenachrichtigung | `RoleEnvironment` | `CodePackageActivationContext`
Lokaler Speicher | `RoleEnvironment` | `CodePackageActivationContext`
Endpunktinformationen | `RoleInstance` <ul><li>Aktuelle Instanz: `RoleEnvironment.CurrentRoleInstance`</li><li>Andere Rollen und Instanz: `RoleEnvironment.Roles`</li> | <ul><li>`NodeContext` für die aktuelle Knotenadresse</li><li>`FabricClient` und `ServicePartitionResolver` für die Ermittlung von Dienstendpunkten</li> 
Umgebungsemulation | `RoleEnvironment.IsEmulated` | N/V
Gleichzeitiges Änderungsereignis | `RoleEnvironment` | N/V

## Konfigurationseinstellungen

Konfigurationseinstellungen in Cloud Services werden für eine VM-Rolle festgelegt und gelten für alle Instanzen dieser VM-Rolle. Diese Einstellungen sind in Dateien vom Typ „ServiceConfiguration.*.cscfg“ festgelegte Schlüssel-Wert-Paare, auf die direkt über RoleEnvironment zugegriffen werden kann. In Service Fabric gelten Einstellungen individuell für jeden Dienst und jede Anwendung, anstatt für eine VM, da eine VM mehrere Dienste und Anwendungen hosten kann. Ein Dienst besteht aus drei Paketen:

 - **Code:** Enthält die ausführbaren Dienstdateien, Binärdateien, DLLs und alle anderen Dateien, die ein Dienst für die Ausführung benötigt.
 - **Config:** Alle Konfigurationsdateien und Einstellungen für einen Dienst.
 - **Data:** Statische Datendateien, die einem Dienst zugeordnet sind.

Diese Pakete können unabhängig voneinander mit einer Versionsangabe versehen und aktualisiert werden. Ähnlich wie bei Cloud Services kann auf ein Config-Paket programmgesteuert über eine API zugegriffen werden, und es sind Ereignisse verfügbar, um den Dienst über eine Änderung im Config-Paket zu benachrichtigen. Die Datei „Settings.xml“ kann für die Schlüssel-Wert-Konfiguration und den programmgesteuerten Zugriff verwendet werden. Im Gegensatz zu Cloud Services kann ein Service Fabric-Config-Paket aber Konfigurationsdateien mit einem beliebigen Format enthalten, z. B. XML, JSON, YAML oder ein benutzerdefiniertes Binärformat.


### Zugreifen auf die Konfiguration
#### Cloud Services

Sie können auf die Konfigurationseinstellungen aus „ServiceConfiguration.*.cscfg“ über `RoleEnvironment` zugreifen. Diese Einstellungen sind für alle Rolleninstanzen einer Cloud Services-Bereitstellung global verfügbar.

```C#

string value = RoleEnvironment.GetConfigurationSettingValue("Key");

```

#### Service Fabric

Jeder Dienst verfügt über sein eigenes Konfigurationspaket. Es gibt keinen integrierten Mechanismus für globale Konfigurationseinstellungen, auf die mit allen Anwendungen in einem Cluster zugegriffen werden kann. Wenn Sie die spezielle Konfigurationsdatei „Settings.xml“ von Service Fabric in einem Konfigurationspaket verwenden, können Werte in „Settings.xml“ auf Anwendungsebene überschrieben werden, um Konfigurationseinstellungen auf Anwendungsebene zu ermöglichen.

Auf Konfigurationseinstellungen wird in jeder Dienstinstanz über das `CodePackageActivationContext`-Element des Diensts zugegriffen.

```C#

ConfigurationPackage configPackage = this.ServiceInitializationParameters.CodePackageActivationContext.GetConfigurationPackageObject("Config");

// Access Settings.xml
KeyedCollection<string, ConfigurationProperty> parameters = configPackage.Settings.Sections["MyConfigSection"].Parameters;

string value = parameters["Key"]?.Value;

// Access custom configuration file:
using (StreamReader reader = new StreamReader(Path.Combine(configPackage.Path, "CustomConfig.json")))
{
    MySettings settings = JsonConvert.DeserializeObject<MySettings>(reader.ReadToEnd());
}

```

### Ereignisse für die Aktualisierung der Konfiguration
#### Cloud Services

Das `RoleEnvironment.Changed`-Ereignis wird für die Benachrichtigung aller Rolleninstanzen verwendet, wenn in der Umgebung eine Änderung vorgenommen wird, z. B. eine Konfigurationsänderung. Es wird zum Nutzen von Konfigurationsupdates eingesetzt, ohne dass Rolleninstanzen wiederverwendet werden oder ein Workerprozess neu gestartet wird.

```C#

RoleEnvironment.Changed += RoleEnvironmentChanged;

private void RoleEnvironmentChanged(object sender, RoleEnvironmentChangedEventArgs e)
{
   // Get the list of configuration changes
   var settingChanges = e.Changes.OfType<RoleEnvironmentConfigurationSettingChange>();
foreach (var settingChange in settingChanges) 
   {
      Trace.WriteLine("Setting: " + settingChange.ConfigurationSettingName, "Information");
   }
}

```

#### Service Fabric

Jeder der drei Pakettypen in einem Dienst – Code, Config und Data – verfügt über Ereignisse, mit denen eine Dienstinstanz benachrichtigt wird, wenn ein Paket aktualisiert, hinzugefügt oder entfernt wird. Ein Dienst kann mehrere Pakete der unterschiedlichen Typen enthalten. Beispielsweise kann ein Dienst mehrere Config-Pakete aufweisen, von denen jedes einzeln mit Versionsangabe versehen und aktualisiert werden kann.

Diese Ereignisse sind verfügbar, um Änderungen von Dienstpaketen zu verarbeiten, ohne die Dienstinstanz neu zu starten.
 
```C#

this.ServiceInitializationParameters.CodePackageActivationContext.ConfigurationPackageModifiedEvent +=
                    this.CodePackageActivationContext_ConfigurationPackageModifiedEvent;

private void CodePackageActivationContext_ConfigurationPackageModifiedEvent(object sender, PackageModifiedEventArgs<ConfigurationPackage> e)
{
    this.UpdateCustomConfig(e.NewPackage.Path);
    this.UpdateSettings(e.NewPackage.Settings);
}

```

## Startaufgaben

Startaufgaben sind Aktionen, die vor dem Starten einer Anwendung durchgeführt werden. Eine Startaufgabe wird normalerweise zum Ausführen von Setupskripts mit erhöhten Rechten verwendet. Startaufgaben werden sowohl für Cloud Services als auch für Service Fabric unterstützt. Der Hauptunterschied besteht darin, dass eine Startaufgabe in Cloud Services an eine VM gebunden ist, da sie Teil einer Rolleninstanz ist. In Service Fabric ist eine Startaufgabe dagegen an einen Dienst gebunden, der nicht an eine bestimmte VM gebunden ist.

 | Cloud Services | Service Fabric
--- | --- | ---
Speicherort der Konfiguration | ServiceDefinition.csdef | ServiceManifest.xml
Rechte | „Eingeschränkt“ oder „Erhöht“ | Beliebiges Benutzer- oder Computerkonto
Sequenzierung | „Einfach“, „Hintergrund“, „Vordergrund“ | Die Ausführung der Startaufgabe muss erfolgreich sein, bevor der Dienst gestartet wird.

### Cloud Services
Bei Cloud Services wird pro Rolle in „ServiceDefintion.csdef“ ein Einstiegspunkt für den Start konfiguriert.

```xml

<ServiceDefinition>
    <Startup>
        <Task commandLine="Startup.cmd" executionContext="limited" taskType="simple" >
            <Environment>
                <Variable name="MyVersionNumber" value="1.0.0.0" />
            </Environment>
        </Task>
    </Startup>
    ...
</ServiceDefinition>

```

### Service Fabric

Bei Service Fabric wird ein Einstiegspunkt für den Start pro Dienst in „ServiceManifest.xml“ konfiguriert:

```xml

<ServiceManifest>
  <CodePackage Name="Code" Version="1.0.0">
    <SetupEntryPoint>
      <ExeHost>
        <Program>Startup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    ...
</ServiceManifest>

``` 

## Hinweis zur Entwicklungsumgebung

Sowohl Cloud Services als auch Service Fabric sind in Visual Studio mit Projektvorlagen integriert und verfügen über Unterstützung für das Debuggen, Konfigurieren und Bereitstellen lokal und unter Azure. Sowohl unter Cloud Services als auch unter Service Fabric wird zudem eine lokale Laufzeitumgebung für die Entwicklung bereitgestellt. Der Unterschied besteht hierbei darin, dass von der Cloud Services-Entwicklungslaufzeit die Azure-Umgebung emuliert wird, in der sie ausgeführt wird. Service Fabric verwendet dagegen keinen Emulator, sondern die vollständige Service Fabric-Laufzeit. Die Service Fabric-Umgebung, die Sie auf Ihrem lokalen Entwicklungscomputer ausführen, ist die gleiche Umgebung, die in der Produktion ausgeführt wird.

##Nächste Schritte

Lesen Sie die weiteren Informationen zu Service Fabric Reliable Services und den grundlegenden Unterschieden zwischen Cloud Services der Service Fabric-Anwendungsarchitektur, um zu verstehen, wie Sie die Vorteile der umfassenden Service Fabric-Features nutzen können.

 - [Erste Schritte mit Service Fabric Reliable Services](./service-fabric-reliable-services-quick-start.md)

 - [Konzeptioneller Leitfaden zu den Unterschieden zwischen Cloud Services und Service Fabric](./service-fabric-cloud-services-migration-differences.md)
 
<!--Image references-->
[3]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/service-fabric-cloud-service-projects.png
[4]: ./media/service-fabric-cloud-services-migration-worker-role-stateless-service/worker-role-to-stateless-service.png

<!---HONumber=AcomDC_0302_2016-->